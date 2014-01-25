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

### Footnotes

[0]:https://crypto.stanford.edu/forcehttps/forcehttps.pdf
[1]:https://crypto.stanford.edu/forcehttps/forcehttps.pdf