# OSCP-Journey-con
OSCP Journey – Chapter 3: Analyzing Captured Traffic with Wireshark

Author: Luis (aka Lucien Vale)
Environment: Kali Linux (VirtualBox), pfSense, Metasploitable2, iPhone (real device)
Network Mode: Host-Only Adapter
Tools Used: Wireshark, arpspoof, nmap, urlsnarf

⸻

Overview

In this lab, I completed the exercises from Chapter 3 of Ethical Hacking: A Hands-On Introduction to Breaking In by Daniel G. Graham. The objective was to analyze real network traffic using Wireshark after successfully executing an ARP spoofing attack against both a vulnerable virtual machine (Metasploitable2) and a secure real-world mobile device (iPhone). This allowed me to see the full contrast between unencrypted (HTTP) and encrypted (HTTPS) traffic in a live environment. Additionally, this chapter reinforced my understanding of the five-layer Internet protocol stack and how each layer performs its role independently.

⸻

Lab Setup & Execution

Step 1: Network Discovery
	•	Used netdiscover and nmap to identify the IPs and OS fingerprints of both Metasploitable2 and iPhone.
	•	Verified connectivity and reachable IPs:
	•	Metasploitable2: 192.168.1.101
	•	iPhone: 192.168.1.37
	•	Gateway (pfSense): 192.168.1.1

Step 2: Enabled IP Forwarding
echo 1 > /proc/sys/net/ipv4/ip_forward

Required to allow Kali to forward packets after successfully spoofing both victim and router.

Step 3: Launched ARP Spoofing Attack
arpspoof -i eth0 -t <victim_ip> <router_ip>
arpspoof -i eth0 -t <router_ip> <victim_ip>

Executed this on both Metasploitable2 and iPhone individually.

Step 4: Captured Packets in Wireshark
	•	Interface: eth0
	•	Filters used:
	•	ip.addr == <target_ip>
	•	dns
	•	tls
	•	http

Step 5: Observed Both Sides of the Web: Unencrypted vs. Encrypted
	•	Metasploitable2:
	•	Simulated browsing to vulnerable HTTP services.
	•	Captured full plaintext requests and responses in Wireshark and urlsnarf.
	•	Observed sensitive data (e.g., session tokens, login credentials) in clear text.
	•	iPhone:
	•	Captured real TLS-encrypted traffic from apps and Safari.
	•	Observed TLSv1.2 application data blocks, DNS queries, and TCP streams.
	•	Could not view content due to full HTTPS encryption (as expected in modern devices).

⸻

Key Observations
	•	Seeing both HTTP and HTTPS in action side-by-side was pivotal.
	•	Wireshark is powerful for dissecting both encrypted and unencrypted layers.
	•	DNS queries revealed domain names, even when HTTPS encrypted the payload.
	•	iPhone traffic showed what a secure ecosystem looks like—everything encrypted by default.
	•	Metasploitable2 was vulnerable by design, showing the complete anatomy of an HTTP-based attack path.

⸻

Protocol Stack Insight

Learned how the five-layer Internet protocol stack operates independently and sequentially:
	1.	Physical Layer – Physical signals via Ethernet or Wi-Fi
	2.	Data Link Layer – MAC addressing, ARP spoofing in action
	3.	Network Layer – IP-based routing of packets (observed with ip.addr)
	4.	Transport Layer – TCP/UDP stream behavior, retransmissions, ports
	5.	Application Layer – DNS, HTTP, TLS visible depending on encryption

Each layer does its job without interfering with others, allowing precise attack and defense targeting.

⸻

What I Learned
	•	How to execute and sustain a full ARP spoofing attack against both VM and real device
	•	How to intercept, analyze, and contrast plaintext and encrypted traffic
	•	How to properly filter and interpret data using Wireshark
	•	Gained real-world perspective on how encryption protects users and how outdated systems are exposed
	•	Solidified my understanding of each network layer and how traffic flows through them in real time

⸻

Troubleshooting
	•	iPhone’s Safari refused to load under attack until IP forwarding was enabled
	•	HTTPS traffic encrypted payloads beyond readable analysis
	•	Default iOS protections limited some visibility—Metasploitable2 offered full HTTP view
	•	Required filter adjustments and domain resolution via packet analysis

⸻

Next Steps
	•	Complete pfSense exercise to observe router-side traffic monitoring
	•	Begin Chapter 4: Crafting TCP Shells and Botnets
	•	Watch additional Wireshark tutorials to deepen filtering and traffic analysis skills
	•	Experiment with SSL stripping tools or proxy setups to view encrypted sessions in test environments
