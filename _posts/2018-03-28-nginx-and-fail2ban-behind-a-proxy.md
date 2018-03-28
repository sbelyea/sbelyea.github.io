---
layout: post
title: "Nginx and Fail2ban Behind a Proxy (HAProxy)"
category: articles
---

Using Fail2ban behind a proxy requires additional configuration to block the IP address of offenders.  Depending on how proxy is configured, Internet traffic may appear to the web server as originating from the proxy's IP address, instead of the visitor's IP address.  This results in Fail2ban blocking traffic from the proxy IP address, preventing visitors from accessing the site.

To properly block offenders, configure the proxy and Nginx to pass and receive the visitor's IP address.  Then configure Fail2ban to add (and remove) the offending IP addresses to a deny-list which is read by Nginx.


## Architecture
Proxy: HAProxy 1.6.3
Web Server: Nginx (Fail2ban)

Requests coming from the Internet will hit the proxy server (HAProxy), which analyzes the request and forwards it on to the appropriate server (Nginx). _Errata: both systems are running Ubuntu Server 16.04.  HAProxy is performing TLS termination and then communicating with the web server with HTTP._


## Configuring the Proxy (HAProxy)
By default, HAProxy receives connections from visitors to a frontend and then redirects traffic to the appropriate backend.  Connections to the frontend show the visitor's IP address, while connections made by HAProxy to the backends use HAProxy's IP address.  On the web server, all connections made to it from the proxy will appear to come from the proxy's IP address.

To change this behavior, use the `option forwardfor` [directive](https://cbonte.github.io/haproxy-dconv/1.6/configuration.html#4.2-option%20forwardfor "HAProxy 1.6 Documentation").  You can add this to the `defaults`, `frontend`, `listen` and `backend` sections of the HAProxy config.  If you wish to apply this to all sections, add it to your `default` code block.

```
defaults
	log	global
	mode	http
	...
	option	forwardfor
```

Once this option is set, HAProxy will take the visitor's IP address and add it as a HTTP header to the request it makes to the backend.  The header name is set to `X-Forwarded-For` by default, but you can set custom values as required.  The value of the header will be set to the visitor's IP address.  _(Note: if you change this header name value, you'll want to make sure that you're properly capturing it within Nginx to grab the visitor's IP address)._


## Configuring Nginx (Part 1)
Requests from HAProxy to the web server will contain a HTTP header named `X-Forwarded-For` that contains the visitor's IP address.  To make this information appear in the logs of Nginx, modify `nginx.conf` to include the following directives in your `http` block.

```
http {	
	...
	set_real_ip_from	<proxy ip>; # enter your proxy's IP here.
	real_ip_header		X-Forwarded-For;	
	...
}
```

This tells Nginx to grab the IP address from the `X-Forwarded-For` header when it comes from the IP address specified in the `set_real_ip_from` value.  This change will make the visitor's IP address appear in the `access` and `error` logs.


## Configuring Fail2ban
With the visitor IP addresses now being logged in Nginx's `access` and `error` logs, Fail2ban can be configured.  First, create a new jail:

```
[nginx-proxy]
enabled  = true
port     = http
logpath	 = %(nginx_error_log)s #path to Nginx's error log
ignoreip = <IPs excluded from jail scope>
action   = nginx-proxy #name of the custom action that will be created
```

This jail will monitor Nginx's `error` log and perform the actions defined below:

```
actionstart = /etc/init.d/nginx reload
actionstop = /etc/init.d/nginx reload
actionban = echo "deny <ip>;" > /etc/nginx/deny/deny.conf && /etc/init.d/nginx reload
actionunban = grep -i "deny <ip>;" /etc/nginx/deny/deny.conf | sed -i '1d' /etc/nginx/deny/deny.conf  && /etc/init.d/nginx reload
```

The ban action will take the IP address that matches the jail rules (based on max retry and findtime), prefix it with `deny`, and add it to the `deny.conf` file.  Finally, it will force a reload of the Nginx configuration.  The unban action greps the deny.conf file for the IP address and removes it from the file. _Note: there's probably a more elegant way to accomplish this._

All of the actions force a hot-reload of the Nginx configuration.  This is important - reloading ensures that changes made to the deny.conf file are recognized.


## Configuring Nginx (Part 2)
Finally, configure the `sites-enabled` file with a location block that includes the `deny.conf` file Fail2ban is writing to.  This will allow Nginx to block IPs that Fail2ban identifies from the Nginx `error` log file.

```
location / {		
		include /etc/nginx/deny/deny.conf;
	}
```