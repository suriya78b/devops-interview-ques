Most Devops engineers hear the word “reverse proxy” but most don’t understand what it actually means.

I was in a technical interview once where someone confidently said “Nginx is a reverse proxy” but couldn’t explain what that meant or why it mattered.

>>>Let me break this down.<<<

>>A forward proxy sits between you and the internet. You want to visit a website. 

- Your request goes through the proxy first. 
- The proxy makes the request on your behalf. 
- The website sees the proxy’s IP, not yours.
- This is what VPNs do. 
- This is what corporate networks use to control what employees can access. 
- The client is hidden. 
- The server doesn’t know who the real requester is.

>> A reverse proxy flips this completely.

- You’re trying to access a website. You think you’re connecting to the actual server. 
- But you’re hitting a proxy that sits in front of the real servers. 
- The proxy receives your request, decides which backend server should handle it, forwards it there, gets the response, and sends it back to you.
- You have no idea how many servers are behind that proxy. The servers are hidden.
- This is what Nginx does. This is what Load balancer do

>>>Use cases of reverse and forward proxy<<<

>>Suppose your government banned your favorite sites and you still want to access them. 
- Thats when you use a Forward proxy? i.e. a VPN. 
- It will route your traffic through another country, and suddenly you’re browsing those sites inside locked doors
. 

>>Now suppose your website is popular, and you keep getting DDoS attacks by hackers, and your servers are melting. 
- This is where you use a Reverse proxy. - Hide your servers behind Cloudflare or AWS WAF. 
- Attackers hit the proxy, not your infrastructure. 
- Add firewall rules and rate limiting at the proxy level. 
- Bad traffic never reaches your servers.

>>>To conclude this<<<
A forward proxy hides you from the internet. A reverse proxy hides the internet from you.
