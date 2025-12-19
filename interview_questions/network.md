# Networking
## 1. A microservice frequently experiences packet drops. How do you debug using tcpdump and traceroute?
Debugging packet drops in a microservice using tcpdump and traceroute:
tcpdump: Capture packets on the host or inside the pod to identify drops.
On the host: sudo tcpdump -i any -n -vv host <service-ip> and port <service-port>
Inside the pod (if tcpdump is installed or use a debug container): kubectl exec -it <pod> -- tcpdump -i eth0 -n port <port>
Look for:
RST packets (connection reset)
ICMP "port unreachable" or "host unreachable"
No SYN-ACK in response to SYN (indicating drops)
Retransmissions (dup ack, retransmit)

Capture with filters: tcpdump -i any -w capture.pcap host <ip> and port <port> then analyze with Wireshark.

traceroute (or tracert on Windows):
Run traceroute <destination-ip> or traceroute -T -p <port> <destination-ip> (TCP traceroute to target port).
Look for:
Asterisks (***) indicating packet loss at a specific hop
Sudden latency spikes before the drop
If drops happen at a specific hop, it could be network device (load balancer, firewall, NAT gateway) dropping packets.


Combined approach: First use traceroute to identify the problematic hop, then run tcpdump on nodes around that path (e.g., source node, destination node, or intermediate router if accessible).

## 2. DNS resolution inside Kubernetes takes 2–3 seconds. How do you troubleshoot slow DNS?
Troubleshooting slow DNS resolution (2–3 seconds) in Kubernetes:
Check CoreDNS (or kube-dns) logs: kubectl logs -n kube-system -l k8s-app=kube-dns
Look for upstream timeouts, high query latency, or cache misses.

Common causes and checks:
ndots:5 in /etc/resolv.conf inside pods → causes unnecessary searches. Fix by setting options ndots:2 in CoreDNS ConfigMap or using dnsPolicy: None with custom DNS.
CoreDNS overloaded: Check CPU/memory usage of CoreDNS pods. Scale with kubectl scale deployment coredns -n kube-system --replicas=4.
Upstream DNS slowness: Test from inside pod dig @<cluster-ip-of-coredns> google.com. Then test upstream (e.g., 8.8.8.8).
Large search list: Check search lines in pod’s /etc/resolv.conf (cat /etc/resolv.conf inside pod).
Cache disabled/miss: Enable cache plugin in CoreDNS ConfigMap (cache 30).
Network policies blocking traffic to CoreDNS port 53.

Quick test: kubectl exec -it <pod> -- time nslookup kubernetes.default

## 3. What is SMTP, its uses, and its types?
SMTP (Simple Mail Transfer Protocol):
Definition: Standard protocol for sending emails across IP networks (port 25, 587 for submission, 465 for SMTPS).
Uses:
Sending emails from applications/servers (transactional emails, alerts)
Mail transfer between mail servers (MTA to MTA)
Client submission (MUA to MSA)

Types/Variants:
SMTP: Plain text, port 25 or 587 (with STARTTLS)
SMTPS: SMTP over implicit TLS (deprecated, port 465)
ESMTP: Extended SMTP (supports authentication, 8BITMIME, etc.)

## 4. DNS uses which protocols — TCP, UDP, or HTTPS?
DNS protocols:
UDP: Used for most standard DNS queries (port 53) — fast, connectionless, 512-byte limit.
TCP: Used when response exceeds 512 bytes (truncation), zone transfers (AXFR/IXFR), or DNSSEC validation (port 53).
HTTPS (DoH): DNS over HTTPS (RFC 8484) — encrypted, runs over port 443, used by browsers and clients for privacy.
TLS (DoT): DNS over TLS (port 853) — also encrypted.
Modern clients increasingly use DoH/DoT to prevent eavesdropping and manipulation.

## 5. What are common HTTPS API methods?
Common HTTPS API methods (HTTP methods used over HTTPS):
GET: Retrieve data (idempotent, safe)
POST: Create new resource or submit data
PUT: Update/replace entire resource (idempotent)
PATCH: Partial update of resource
DELETE: Remove resource (idempotent)
HEAD: Like GET but only headers
OPTIONS: Discover allowed methods
Less common: TRACE, CONNECT

## 6. Explain firewall concepts, inbound vs outbound rules, and ports.
Firewall concepts, inbound vs outbound rules, and ports:
Firewall: Security system that monitors and controls network traffic based on rules. Can be host-based (iptables, ufw, Windows Firewall) or network-based (security groups, firewalls appliances).
Inbound rules (ingress): Control traffic coming INTO the host/interface from external sources.
Example: Allow port 80/tcp from anywhere for web server.

Outbound rules (egress): Control traffic going OUT from the host to external destinations.
Example: Allow port 53/udp to 8.8.8.8 for DNS resolution.
Many default to "allow all outbound" but production often restricts.

Ports: Identify services/processes.
Well-known: 0–1023 (e.g., 80 HTTP, 443 HTTPS)
Registered: 1024–49151
Dynamic: 49152–65535
Rules specify protocol (TCP/UDP) + port + source/destination.

Stateful vs Stateless: Stateful tracks connection state (e.g., allow return traffic automatically). Stateless requires explicit rules for both directions.

## 7. SSL handshake, three way handshake
SSL/TLS Handshake vs TCP Three-Way Handshake:
TCP Three-Way Handshake (connection establishment):
Client → Server: SYN
Server → Client: SYN-ACK
Client → Server: ACK
→ Connection established, data can flow.

SSL/TLS Handshake (secure session on top of TCP):
Occurs after TCP connection is established.
Steps (simplified TLS 1.2/1.3):
ClientHello: Client sends supported ciphers, TLS version, random data, extensions.
ServerHello: Server picks cipher, version, sends certificate, random data.
Server sends ServerKeyExchange (if needed), CertificateRequest (mutual auth), ServerHelloDone.
Client verifies cert, sends ClientKeyExchange (premaster secret encrypted), ChangeCipherSpec, Finished.
Server responds with ChangeCipherSpec, Finished.
→ Symmetric session keys derived, encrypted communication begins.
TLS 1.3 simplifies: Fewer round trips, encrypts more early.
Key difference: TCP is for reliable connection; TLS is for encryption/authentication on top of it.

