Day Log: August 29, 2026

Topic: Wireshark & Network Traffic Monitoring

What I Learned

Today's focus was Wireshark, getting hands-on with capturing and analyzing live network traffic to actually understand what's moving across a network instead of just reading about it.

1. What Wireshark Actually Does

Wireshark is a packet analyzer. It captures raw network traffic passing through a network interface and breaks each packet down layer by layer (Ethernet, IP, TCP/UDP, and the application layer on top). Instead of seeing "a website loaded," you see every individual packet that made it happen.

2. Interface and Capture Basics

Learned how to pick the correct network interface to capture on (Wi-Fi vs Ethernet vs loopback), start/stop a capture, and read the three main panes: the packet list (one row per packet), packet details (protocol breakdown), and the hex/ASCII byte view at the bottom.

3. The OSI Model in Practice

Seeing real packets made the OSI/TCP-IP layers click in a way theory never did. Each packet shows Frame, Ethernet II, IP, TCP/UDP, and the application protocol stacked on top of each other. Watching a single HTTP request unfold layer by layer made the abstract model concrete.

4. Filtering Traffic

Spent significant time on display filters, which is where Wireshark becomes usable instead of overwhelming:
- ip.addr == x.x.x.x: traffic to/from a specific host
- tcp.port == 80 or http: isolate web traffic
- dns: isolate DNS queries and responses
- tcp.flags.syn == 1 and tcp.flags.ack == 0: spot the start of a TCP handshake (useful for scan detection)
- Combining filters with and / or to narrow down noisy captures

5. The TCP Three-Way Handshake

Watched a live handshake in Wireshark: SYN, SYN-ACK, ACK. Being able to see this directly, instead of just reading it in a textbook, made concepts like connection state, sequence numbers, and retransmissions much clearer.

6. Following a Stream

Used "Follow > TCP Stream" to reconstruct an entire conversation between client and server as readable text. This is how plaintext protocols like HTTP or FTP can leak credentials, and exactly why encryption (HTTPS/TLS) matters. Seeing unencrypted data in the clear was the most eye-opening part of the day.

7. Why This Matters for Security Work

- Traffic monitoring is a core blue-team skill. Spotting unusual patterns (port scans, beaconing, unexpected external connections) starts with knowing what "normal" traffic looks like.
- Wireshark is often the tool used to validate what a SIEM or IDS alert is actually seeing at the packet level.
- Understanding protocols at the packet level makes it much easier to later understand exploitation and detection both. You can't defend what you don't understand at the wire level.

Resources Used
- Wireshark (hands-on packet capture and analysis)
- Local network traffic for practice captures

Key Takeaways
- Filters are what make Wireshark usable. Capturing everything without filtering is just noise.
- Seeing a TCP handshake and a plaintext HTTP stream in real time made networking theory click far more than reading about it.
- Traffic analysis is a foundational skill for both offensive (recon) and defensive (monitoring) security work.

Problems Faced
- Capture output was overwhelming at first until I learned to apply filters immediately rather than trying to scroll through raw traffic.

Tomorrow's Plan
- Practice writing more advanced filters and try capturing traffic during a simple simulated attack (e.g. an Nmap scan) to see what it looks like from the defender's side in Wireshark.
