Assorted notes for web security

## Secure Cookies
- There's an RFC for this (6265) which may be interesting to read.  These cookies may only be sent over a secure transport layer (ex: SSL, TLS).

- See this informative article from [Coding Horror][1] for details on the spec/implementation

- From section 2.3.1 of RFC6797, _not_ having secure cookies seems like a pretty big security hole.

- You can check to see if cookies are _not_m HttpOnly by running document.cookie in the console.  If your cookies are all HttpOnly, you shouldn't be able to see any output, even if cookies do exist for the site.

## HSTS
- Threat models can be found in the referenced [ForceHTTPS paper][0] from stanford.

- Why should you use HTTPS?
	- Because people are creepy
	- Unencrypted web traffic can be monitored very easily (firesheep, aircrack)


- Levels of security
	- HTTP
	- HTTPS
	- HTTPS + Secure Cookie requirement


- What does HSTS provide you?
	- Enforcement at the User Agent (browser) to require a secure (HTTPS) connection to the server.
		- This is actually _uber awesome_.  The user no longer has to get redirected because they bookmarked the HTTP version of the site; the browser should respect the max-age value set with the Strict-Transport-Security directive and automagically make the URI requests initiative a HTTPS connection.
		- Where it gets really cool: you could (potentially) pre-seed browsers with HSTS information for domains so that the first connection ever made to the site is guaranteed to be via HTTPS.  That's awesome.
	- Noisy termination of the connection upon error.  Once this is set and enforced, you _aren't allowed to make regular HTTP connections_.  This could cause some breakage, but is a great default from a security perspective.

- To see Chrome's HSTS store, go to [chrome://net-internals/#hsts][8].

- There's an [IIS plug-in][4] to implement HSTS in a specification compliant way (only transmitting HSTS headers over a secure connection).

- Obligatory [Wikipedia page][5].

- Chrome and Firefox share the same [HSTS preload list][6] [(source)][7].  We can ask to be added to it once we start sending the HSTS header.

### Sidenotes
- We should be sending a [301 Moved Permanently][3] to users who are visiting http://www.insightsc3m.com, instead of a 302.

- Visits to http://insightsc3m.com provide a 301, but to the http://www.insightsc3m.com URL.  Wonder if CSC DNS can change the redirect URL to https?  Should test on QA1 first...

- There's theoretical SEO benefits from using a 301 instead of a 302, but the proper _semantic_ response is a 301 for our purposes.





### Footnotes

[0]:https://crypto.stanford.edu/forcehttps/forcehttps.pdf
[1]:http://www.codinghorror.com/blog/2008/08/protecting-your-cookies-httponly.html
[2]:http://tools.ietf.org/html/rfc6797#section-5.3
[3]:http://www.html5rocks.com/en/tutorials/security/transport-layer-security/
[4]:https://hstsiis.codeplex.com/
[5]:https://en.wikipedia.org/wiki/HTTP_Strict_Transport_Security
[6]:http://www.chromium.org/sts
[7]:https://src.chromium.org/viewvc/chrome/trunk/src/net/http/transport_security_state_static.json
[8]:chrome://net-internals/#hsts