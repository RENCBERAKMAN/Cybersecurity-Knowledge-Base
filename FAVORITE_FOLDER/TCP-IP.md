## TCP/IP Stack & Packet Anatomy: The Foundation Every Security Professional Must Master

---

There is a question that separates people who work in cybersecurity from people who truly understand cybersecurity: do you know what actually happens between the moment you press Enter and the moment a web server responds? Not the textbook answer. Not the diagram with seven colored boxes. The real answer — at the byte level, at the wire level, at the level where attackers operate.

If you cannot answer that question with precision, every tool you use is a black box. You can run Nmap, you can launch Metasploit, you can analyze traffic in Wireshark — but you are doing it blind. You are pushing buttons and hoping the output makes sense. Real security work, whether offensive or defensive, starts with understanding exactly how data moves across a network, because every attack, every defense, and every detection technique is built directly on top of this foundation.

This article will not give you a surface-level overview. We are going to go deep — deep enough that by the end, the way you look at network traffic will be permanently changed.

## Why the OSI Model Is Taught Wrong (And What You Actually Need to Know)

Every networking course starts with the OSI model. Seven layers, nice acronyms, maybe a mnemonic. And then the teacher moves on, and most students never really connect the model to what is actually happening on the wire.

The OSI model is a conceptual framework created in the late 1970s by the International Organization for Standardization. It was designed to allow different vendors to build interoperable networking equipment by agreeing on a common abstraction. It has seven layers: Physical, Data Link, Network, Transport, Session, Presentation, and Application.

The TCP/IP model is what the internet actually runs on. It collapses the OSI model into four layers: Network Access (combining Physical and Data Link), Internet (corresponding to Network), Transport, and Application (combining Session, Presentation, and Application).

Here is why this matters for security: attacks do not happen in a single layer. They happen across layers, exploiting the boundaries between them. A SYN flood attack targets the Transport layer but exhausts resources at the Application layer. DNS cache poisoning targets the Application layer but exploits assumptions made at the Network layer. An ARP spoofing attack targets the Data Link layer but enables man-in-the-middle attacks that intercept Application layer data. If you think in silos — “this is a Layer 4 attack” — you will miss the full picture.

The key insight is this: each layer adds its own header to the data as it goes down the stack (encapsulation), and strips that header as it comes up the stack (decapsulation). Every header contains fields that can be inspected, manipulated, forged, or exploited. Understanding every field in every header is not academic — it is operational.

## The Physical and Data Link Layers: Where Bits Become Frames

Before a single packet is transmitted, the physical medium must carry the signal. Ethernet uses differential signaling over twisted pair cables. Wi-Fi encodes data in radio waves. Fiber uses light pulses. The specific encoding mechanism matters less for most security work than what happens at the Data Link layer, which is where Ethernet frames live.

An Ethernet frame has the following structure. It begins with a preamble — seven bytes of alternating 1s and 0s — which allows the receiving network card to synchronize its clock with the incoming signal. This is followed by one byte called the Start Frame Delimiter, which is always 0b10101011, signaling that the actual frame is about to begin.

Then comes the destination MAC address: six bytes. Then the source MAC address: six bytes. Then a two-byte EtherType field, which tells the receiving device what protocol is encapsulated inside — 0x0800 for IPv4, 0x0806 for ARP, 0x86DD for IPv6, 0x8100 for VLAN-tagged frames (802.1Q). Then the payload, between 46 and 1500 bytes. Then a four-byte Frame Check Sequence, which is a CRC-32 checksum used to detect transmission errors.

MAC addresses are 48-bit identifiers burned into network interface cards by manufacturers. The first three bytes are the Organizationally Unique Identifier (OUI), assigned to the manufacturer. The last three bytes are the device-specific portion. MAC addresses are supposed to be globally unique, but they can be trivially changed in software — a technique called MAC spoofing, which is used to bypass MAC-based access controls and to evade detection after a network intrusion.

The Address Resolution Protocol (ARP) operates at this layer and is responsible for mapping IP addresses to MAC addresses. When a device wants to send traffic to an IP address on the local network, it sends an ARP request: a broadcast frame saying “who has IP address X, tell IP address Y.” The device with that IP responds with an ARP reply: “IP address X is at MAC address Z.”

ARP has no authentication. There is no mechanism in the protocol to verify that the device claiming to own an IP address actually does. This is the root cause of ARP spoofing, also called ARP poisoning. An attacker sends gratuitous ARP replies — unsolicited replies that update the ARP tables of other devices on the network — falsely claiming that a target IP address maps to the attacker’s MAC address. The result is that traffic intended for the target gets sent to the attacker instead. This is one of the oldest and most effective man-in-the-middle techniques on local networks, and it still works today because ARP was never designed with security in mind.

The ARP table (sometimes called the ARP cache) on any machine can be viewed with the arp -a command on Windows or Linux. In a penetration test against a local network, checking and manipulating the ARP table is often one of the first steps. Tools like arpspoof and Ettercap automate this process, but understanding what they are doing — sending crafted ARP reply frames — is essential for both executing and detecting these attacks.

VLAN tagging, defined in IEEE 802.1Q, inserts a four-byte tag into the Ethernet frame between the source MAC address and the EtherType field. The tag contains a 12-bit VLAN identifier (allowing up to 4094 VLANs), a 3-bit priority field, and a Drop Eligible Indicator bit. VLANs are used to logically separate network segments on shared physical infrastructure. The security assumption is that devices on different VLANs cannot communicate directly. VLAN hopping attacks exploit misconfigurations in trunk ports and native VLANs to bypass this segmentation, which is a common finding in enterprise network penetration tests.

## The IP Layer: Routing, Addressing, and the Fields That Matter

The Internet Protocol is the glue that holds the internet together. It provides two fundamental services: addressing and routing. Every device on the internet has an IP address, and IP is responsible for getting packets from source to destination, potentially across many intermediate routers.

An IPv4 header is a minimum of 20 bytes. Every field in it has security implications.

The first field is the Version, which occupies the top four bits. For IPv4 it is always 4. For IPv6 it is 6. Simple, but occasionally relevant when dealing with protocol confusion attacks.

The Internet Header Length (IHL) field occupies the next four bits and specifies the length of the IP header in 32-bit words. The minimum value is 5 (meaning 20 bytes), and the maximum is 15 (meaning 60 bytes). If IHL is greater than 5, the header contains options. IP options are a source of significant complexity and have been used in various attacks over the years, including the infamous IP record route option which can be used for network reconnaissance.

The Differentiated Services Code Point (DSCP) field is six bits and is used for Quality of Service markings. From a security perspective, an unexpected DSCP value can sometimes indicate traffic that is being specially prioritized, which may be relevant in traffic analysis.

The Total Length field is two bytes and indicates the total size of the IP datagram — header plus payload — in bytes. The maximum value is 65535 bytes. This field has been involved in several historical vulnerabilities. The Ping of Death attack, for example, sent ICMP packets with a total size exceeding 65535 bytes when reassembled from fragments, causing buffer overflows in vulnerable operating systems. The field is also relevant in fragmentation attacks.

The Identification field is two bytes and is used to group IP fragments together. When a large IP datagram must be fragmented to traverse a link with a small Maximum Transmission Unit (MTU), each fragment gets the same Identification value so the receiving host knows which fragments belong together. This field has been used in OS fingerprinting because different operating systems use different strategies for generating Identification values. Some use sequential counters, some use random values, and some use patterns that are specific to particular implementations. By analyzing the Identification field in responses, it is sometimes possible to determine the operating system without even looking at other headers.

The Flags field is three bits. The first bit is reserved and must be zero. The second bit is the Don’t Fragment (DF) bit. When set, it tells routers not to fragment the packet. If the packet is too large for a link and the DF bit is set, the router drops the packet and sends an ICMP “Fragmentation Needed” message back to the source. This is the mechanism behind Path MTU Discovery. Attackers sometimes manipulate the DF bit to interfere with MTU discovery or to cause deliberate packet drops. The third bit is the More Fragments (MF) bit, which is set on all fragments except the last one.

The Fragment Offset field is 13 bits and specifies where in the original datagram this fragment belongs, measured in units of eight bytes. IP fragmentation and reassembly has a long history of security vulnerabilities. The Teardrop attack sent overlapping fragments with invalid offsets, causing vulnerable systems to crash when trying to reassemble them. Fragment overlap attacks can also be used to evade intrusion detection systems: the IDS sees harmless fragments, but the target host reassembles them into an attack payload because it resolves the overlap differently than the IDS does.

The Time to Live (TTL) field is one byte and is decremented by each router the packet passes through. When TTL reaches zero, the packet is dropped and an ICMP Time Exceeded message is sent back to the source. This is the mechanism behind the traceroute tool. The initial TTL value also varies by operating system — Windows typically starts at 128, Linux at 64, and older Cisco devices at 255. Observing the TTL of incoming packets can sometimes reveal information about the operating system and the number of hops between you and the sender, though VPNs and proxies complicate this analysis.

The Protocol field is one byte and identifies the transport layer protocol carried in the IP payload: 6 for TCP, 17 for UDP, 1 for ICMP, 50 for ESP (IPsec), 51 for AH (IPsec), 89 for OSPF, and so on. The complete list is maintained by IANA. This field matters in firewall rules and in protocol tunneling. ICMP tunneling, for example, hides data inside ICMP packets by sending it in the payload field of Echo Request or Echo Reply messages, allowing exfiltration through firewalls that block other protocols but allow ICMP. Tools like iodine and ptunnel implement various forms of protocol tunneling.

The Header Checksum is two bytes and covers only the IP header. It is recalculated at every router hop because the TTL changes. This checksum does not protect the payload — that is the responsibility of TCP, UDP, or the application. IP spoofing is possible precisely because IP itself provides no authentication — any device can craft a packet with any source IP address it chooses.

The source and destination IP addresses are four bytes each. IPv4 uses 32-bit addresses, providing approximately 4.3 billion unique addresses. This space is divided into public addresses, routable across the internet, and private address ranges defined in RFC 1918: 10.0.0.0/8, 172.16.0.0/12, and 192.168.0.0/16. There are also several other special ranges: 127.0.0.0/8 is the loopback range, 169.254.0.0/16 is the link-local range used when DHCP is unavailable, 224.0.0.0/4 is multicast, and 0.0.0.0/8 represents “this network.”

IP address spoofing — sending packets with a forged source address — is trivially easy to implement but has limited utility for attacks that require a response, because the response goes to the forged address, not the attacker. It is, however, extremely useful for reflection and amplification attacks. In these attacks, the attacker sends requests to legitimate servers (DNS resolvers, NTP servers, memcached instances) with the source IP set to the victim’s IP address. The servers send their responses to the victim, and if the responses are much larger than the requests (amplification), the victim receives a flood of traffic without the attacker ever sending that volume directly. DNS amplification attacks can achieve amplification factors of 50x or more, meaning the attacker sends 1 GB of traffic and the victim receives 50 GB.

CIDR notation (Classless Inter-Domain Routing) is the standard way to express IP address ranges. An address like 192.168.1.0/24 means the first 24 bits are the network portion and the remaining 8 bits are the host portion, giving 256 addresses (254 usable, since the network address and broadcast address are not usable). Understanding CIDR is essential for reading firewall rules, subnetting, and understanding the scope of network scans.

## ICMP: More Than Just Ping

The Internet Control Message Protocol lives at the same layer as IP and is used for diagnostic and error-reporting functions. Most people know it as the protocol behind ping and traceroute, but ICMP has a large number of message types and codes that are relevant to security.

An ICMP message has a one-byte Type field, a one-byte Code field, a two-byte checksum, and a variable-length data field. The Type and Code together specify the exact meaning of the message.

Type 0, Code 0 is Echo Reply — the ping response. Type 8, Code 0 is Echo Request — the ping itself. The data field of a ping can carry arbitrary data. This is the basis of ICMP tunneling: by putting a command or payload in the data field of an Echo Request and reading the response from the data field of the Echo Reply, two parties can establish a covert channel through ICMP.

Type 3 is Destination Unreachable. The Code value specifies why the destination was unreachable. Code 0 means the network is unreachable (routing failure). Code 1 means the host is unreachable. Code 3 means the port is unreachable — this is the response UDP sends when a packet arrives at a closed port. Code 4 means fragmentation is needed but the DF bit is set (relevant for Path MTU Discovery). Code 13 means communication is administratively prohibited — this is what a firewall sends when it drops a packet and is configured to respond. The presence or absence of these responses, and which codes are returned, reveals information about the network topology and firewall configuration.

Type 5 is Redirect. ICMP Redirect messages tell a host to update its routing table to use a different gateway for a particular destination. These messages are legitimate in properly configured networks, but they can also be sent by attackers to redirect traffic through a machine they control. This is another vector for man-in-the-middle attacks. Modern operating systems can be configured to ignore ICMP Redirect messages.

Type 11 is Time Exceeded. Code 0 means TTL exceeded in transit — this is what routers send when they drop a packet with TTL=0. This is how traceroute works: it sends packets with increasing TTL values, starting at 1, and each router along the path drops the packet and sends back a Time Exceeded message, revealing its own IP address. This allows the path to the destination to be mapped. Code 1 is Fragment Reassembly Timeout, sent when a host does not receive all the fragments of a datagram within a time limit.

ICMP is frequently blocked at perimeter firewalls, but this is often done imprecisely. Blocking all ICMP breaks important functionality like Path MTU Discovery and can cause subtle connectivity problems. The recommended approach is to block certain ICMP types while allowing others.

## TCP: The Protocol Where Most Attacks Live

The Transmission Control Protocol provides reliable, ordered, and error-checked delivery of a stream of bytes between applications. It is defined in RFC 793, which dates to 1981 and is remarkable in how much of the internet still depends on it essentially unchanged.

A TCP header is a minimum of 20 bytes. Understanding every field is not optional for security work.

The source port is two bytes. The destination port is two bytes. Ports range from 0 to 65535. Ports 0 through 1023 are “well-known” ports, assigned by IANA to specific services: 22 for SSH, 25 for SMTP, 53 for DNS (over both TCP and UDP), 80 for HTTP, 443 for HTTPS, 445 for SMB. Ports 1024 through 49151 are “registered” ports. Ports 49152 through 65535 are “ephemeral” ports, used as source ports for outgoing connections. The specific ephemeral port range varies by operating system and can itself be used for OS fingerprinting.

The Sequence Number is four bytes. TCP is a stream-oriented protocol, and the sequence number identifies where in the byte stream this segment belongs. The sequence number space wraps around — it is a 32-bit counter, so it goes from 0 to 4,294,967,295 and then back to 0.

The initial sequence number (ISN) chosen at the start of a TCP connection is critically important from a security perspective. In early TCP implementations, the ISN was predictable — it was often incremented by a fixed amount for each new connection. This predictability enabled TCP session hijacking attacks, where an attacker who could observe or guess the sequence numbers could inject data into an established TCP connection or terminate it. Modern operating systems use cryptographically random ISNs to prevent this.

The Acknowledgment Number is four bytes and is only meaningful when the ACK flag is set. It contains the next sequence number the sender of this segment expects to receive — in other words, it acknowledges receipt of all bytes up to but not including this number.

The Data Offset field (also called the Header Length) is four bits and specifies the size of the TCP header in 32-bit words, allowing for TCP options. The Flags field is nine bits. In practice, six flags matter most for security work.

The SYN flag (Synchronize) is used to initiate a connection. The ACK flag (Acknowledge) indicates that the Acknowledgment Number field is valid. The FIN flag (Finish) requests a graceful connection termination. The RST flag (Reset) abruptly terminates the connection. The PSH flag (Push) tells the receiving TCP implementation to deliver data to the application immediately rather than buffering it. The URG flag (Urgent) indicates that the Urgent Pointer field is valid, but this flag is rarely used in practice and its use is often associated with certain types of probing.

The combination of flags in a packet carries a lot of information. During normal operation, you see SYN, SYN-ACK, ACK during the handshake, and then ACK packets carrying data, and eventually FIN-ACK exchanges for connection teardown. In network scanning and attack tools, you see unusual flag combinations.

An Xmas tree scan (so called because all the flags are “lit up”) sends packets with FIN, PSH, and URG all set. A Null scan sends packets with no flags set. A FIN scan sends packets with only the FIN flag set. According to the TCP standard, a closed port should respond to these unusual packets with a RST, while an open port should silently drop them. This allows port states to be inferred without completing a full TCP handshake, which some intrusion detection systems might not log as aggressively as a full SYN scan. However, this technique only works on certain operating systems — Windows, for example, sends RST for all these packet types regardless of whether the port is open, making these scans unreliable against Windows targets.

The Window Size is two bytes and tells the other side how much buffer space is available — how many bytes can be sent before waiting for an acknowledgment. The window size has historically been used in OS fingerprinting because different operating systems advertise different default window sizes. The TCP window size is also at the center of TCP flow control: if an application cannot consume incoming data fast enough, the window size shrinks, eventually reaching zero, which tells the sender to stop transmitting until further notice. This mechanism can be exploited in TCP window manipulation attacks to slow down connections deliberately.

Become a Medium member
The Checksum is two bytes and covers the TCP header, the payload, and a “pseudo-header” that includes the source and destination IP addresses, the protocol number (6 for TCP), and the total TCP length. The inclusion of IP address information means that the TCP checksum provides some protection against IP spoofing — a spoofed packet with an incorrect source IP will typically have an invalid checksum. However, calculating a correct checksum for a spoofed packet is trivial, so this is not a meaningful security control.

TCP options are variable-length fields that follow the fixed 20-byte header. The most important options for security work are Maximum Segment Size (MSS), which negotiates the largest segment either side will accept; SACK (Selective Acknowledgment), which allows the receiver to acknowledge non-contiguous blocks of data, improving performance in lossy networks; Window Scale, which allows the window size to exceed 65535 bytes by specifying a shift factor; and Timestamps, which allow round-trip time measurement and provide protection against wrapped sequence numbers. The set of options and their values are one of the most reliable signals used in passive OS fingerprinting — tools like p0f can identify operating systems from a single SYN packet with very high accuracy.

## The Three-Way Handshake: Mechanics, Security, and Attacks

Every TCP connection begins with a three-way handshake. Understanding this process in detail is essential because it is the basis of several important attack categories.

In the first step, the client sends a SYN segment. The SYN flag is set. The sequence number is the client’s initial sequence number — call it C_ISN. No data is carried in a SYN segment. TCP options like MSS, SACK, Window Scale, and Timestamps are negotiated here.

In the second step, the server sends a SYN-ACK segment. Both the SYN and ACK flags are set. The server chooses its own initial sequence number — call it S_ISN — and places it in the Sequence Number field. The Acknowledgment Number is C_ISN + 1 (acknowledging the client’s SYN, which consumed one sequence number). The server’s TCP options are also included here.

In the third step, the client sends an ACK segment. The ACK flag is set. The Sequence Number is C_ISN + 1. The Acknowledgment Number is S_ISN + 1. The connection is now established.

The SYN flood attack exploits this handshake. When a server receives a SYN, it must allocate resources to track the half-open connection and then wait for the final ACK. These half-open connections are stored in a structure called the SYN backlog queue or the incomplete connection queue. If an attacker sends a large number of SYN segments with spoofed source addresses, the server allocates resources for each one and sends SYN-ACK responses to the (non-existent) addresses. The final ACKs never arrive. The SYN backlog queue fills up. When it is full, the server cannot accept new legitimate connections. This is a classic denial-of-service attack.

SYN cookies are the primary defense against SYN floods. With SYN cookies enabled, the server does not allocate any resources when it receives a SYN. Instead, it encodes the connection state into the initial sequence number it sends back in the SYN-ACK. Specifically, the server generates a cryptographic hash of the source IP, source port, destination IP, destination port, and a time-based component, and uses this as the SYN-ACK’s sequence number. If the client sends a valid ACK — meaning the Acknowledgment Number equals that sequence number plus one — the server can reconstruct the connection state from the ACK and proceed normally. Spoofed SYN packets never result in a valid ACK (because the response goes to the spoofed address), so no resources are allocated until a valid three-way handshake completes.

The connection teardown process uses FIN segments. To close a connection, one side sends a FIN, the other acknowledges it with an ACK, then the other side sends its own FIN, and the first side acknowledges it. This four-step process allows both sides to independently close their half of the connection. After sending the final ACK, the active closer enters a TIME_WAIT state, lasting twice the Maximum Segment Lifetime (typically 60 to 120 seconds). The purpose of TIME_WAIT is to handle the case where the final ACK is lost — if it was lost, the other side will retransmit its FIN, and the TIME_WAIT socket can respond with another ACK. TIME_WAIT also prevents delayed duplicate segments from a previous connection from being mistakenly accepted by a new connection using the same port pair. On servers handling many short-lived connections, large numbers of TIME_WAIT sockets can be a scalability issue.

A RST segment immediately terminates a TCP connection without the graceful FIN exchange. The receiving side must discard all buffered data. RST segments are used by firewalls and intrusion prevention systems to terminate connections — a technique called TCP RST injection. Tools like Snort, when configured in inline mode, can inject RST segments to both sides of a detected attack connection, terminating it before the malicious payload reaches its destination. The Great Firewall of China famously uses TCP RST injection to terminate connections to censored resources.

## UDP: Speed Over Reliability, and Why That Matters

The User Datagram Protocol provides a minimal transport service. There is no connection establishment, no sequencing, no acknowledgment, no flow control, and no congestion control. A UDP datagram is just delivered — or not.

The UDP header is eight bytes: two bytes for source port, two bytes for destination port, two bytes for the total length of the datagram (header plus payload), and two bytes for checksum. That is it.

UDP is used by services where low latency is more important than reliability (DNS, streaming media, online gaming, VoIP) and by services that implement their own reliability mechanisms at the application layer (QUIC, DTLS). DNS primarily uses UDP for queries, switching to TCP only for responses that exceed 512 bytes (though the EDNS extension raises this limit).

From a security perspective, UDP’s connectionless nature means that source IP spoofing is straightforward — there is no handshake to complete. This is why UDP is the preferred protocol for amplification attacks. The attacker sends a small UDP request with the victim’s IP as the source. A legitimate server sends a large response to the victim. No three-way handshake is needed, so the spoofing is not detected until the response goes out.

UDP port scanning is more complex than TCP port scanning because there is no standard “open” response. When a UDP packet is sent to an open port, the application may respond, or it may not. When sent to a closed port, the kernel typically sends back an ICMP Port Unreachable message. But firewalls often block these ICMP responses. So UDP scanning is slow, unreliable, and often generates false positives. Nmap’s UDP scan (-sU) works by sending UDP packets to each port and waiting for either an application response (indicating open) or an ICMP port unreachable (indicating closed). No response means "open or filtered."

## What Wireshark Actually Shows You (And How to Read It)

Wireshark is the de facto standard tool for packet capture and protocol analysis. Understanding how to read its output at a deep level is a fundamental skill.

When you capture traffic with Wireshark, each row in the packet list corresponds to one packet. The columns show the time (relative to the first packet by default), source IP, destination IP, protocol, length, and a brief info string. The Info column is generated by Wireshark’s protocol dissectors and summarizes the key information in the packet.

When you click on a packet, the packet details pane shows the full dissection of every protocol layer. You can expand each layer to see individual fields. And the packet bytes pane at the bottom shows the raw hex and ASCII representation of the entire packet. Selecting a field in the details pane highlights the corresponding bytes in the hex dump. This correspondence between fields and bytes is how you build the connection between abstract protocol knowledge and concrete wire-level data.

Learning to read Wireshark effectively requires practice with filters. Wireshark’s display filters are powerful and specific. tcp.flags.syn == 1 && tcp.flags.ack == 0 filters for SYN packets only — the opening of TCP connections. ip.src == 192.168.1.100 filters for packets from a specific source. tcp.stream eq 5 shows all packets belonging to the fifth TCP stream. http.request.method == "POST" shows only HTTP POST requests. !(arp or icmp or dns) hides common background noise. The ability to write precise display filters is what separates someone who can open Wireshark from someone who can actually investigate an incident with it.

Capture filters (BPF syntax, set before capture begins) and display filters (Wireshark syntax, set after capture) are different languages with similar but not identical functionality. Capture filters use BPF syntax: tcp port 80, host 192.168.1.1, not arp. Display filters are more expressive: tcp.port == 80, ip.addr == 192.168.1.1, not arp.

For practical exercises, start by capturing your own traffic as you browse the web. Find the TCP handshake for an HTTPS connection. Observe the TLS handshake that follows. Notice that the application data is encrypted but you can still see the IP addresses, ports, packet sizes, and timing. This metadata — even without the payload — reveals a great deal about the communication patterns, which is why traffic analysis remains effective even against encrypted traffic.

Then deliberately generate different types of traffic. Run an Nmap SYN scan against a local VM and capture it. You will see the SYN packets going out, the SYN-ACK responses from open ports, and the RST responses (or no response) from closed or filtered ports. Notice that Nmap immediately sends a RST after receiving the SYN-ACK — it is completing the scan without establishing a full connection. This is the defining characteristic of a SYN scan (stealth scan): it does not appear in the server’s connection logs because the connection was never fully established (though modern systems often log partial connections).

## IP Fragmentation: A Deeper Look at an Underappreciated Attack Surface

IP fragmentation occurs when a datagram is too large to traverse a link in one piece. The Maximum Transmission Unit (MTU) defines the maximum size of a frame on a given link — 1500 bytes for standard Ethernet, 1492 for PPPoE, 9000 for jumbo frames. When a router needs to forward a packet larger than the MTU of the outgoing link (and the DF bit is not set), it fragments the packet into pieces that fit.

Each fragment carries the original IP Identification value, the Fragment Offset indicating where in the original datagram it belongs, and the More Fragments flag (set on all fragments except the last). The receiving host must reassemble all fragments before passing the datagram to the transport layer.

Fragmentation has been the source of numerous security vulnerabilities, and understanding why requires understanding the reassembly process. The receiving host maintains a reassembly buffer for each partially received datagram, identified by the source IP, destination IP, Protocol, and Identification tuple. When a fragment arrives, it is placed in the buffer according to its offset. When the last fragment (MF=0) arrives and all offsets are filled in, the datagram is reassembled and passed up the stack.

The security problems arise from several sources. First, the reassembly process is complex, and complex code has bugs. Historical vulnerabilities like Teardrop exploited integer overflows in the reassembly code by sending fragments with overlapping offsets that caused the kernel to calculate a negative or very large copy length, resulting in buffer overflows.

Second, fragmentation complicates inspection by security devices. A packet filter or intrusion detection system operating at the IP layer sees individual fragments, not complete datagrams. The transport layer header — containing source and destination ports, TCP flags, and the beginning of the application payload — is only in the first fragment. Subsequent fragments contain only IP headers and payload data. A firewall that makes filtering decisions based on ports or TCP flags cannot apply those decisions to subsequent fragments, because those fragments do not contain the port information.

Attackers have exploited this by placing attack payloads in later fragments. The attack signature only appears when the fragments are reassembled, but the security device may not perform reassembly. Fragment overlap attacks take this further: send one set of fragments that looks benign to the IDS, then send overlapping fragments with an actual attack payload. The IDS sees the benign content and allows everything through. The target host has a different policy for resolving overlapping fragments (using the later data, for example, while the IDS uses the earlier data) and ends up with the attack payload after reassembly.

Modern IPSes and next-generation firewalls typically perform fragment reassembly before inspection to address this. But fragment reassembly requires memory and CPU resources proportional to the number of concurrent incomplete datagrams, which creates a resource exhaustion vulnerability: an attacker can send many first fragments and never send the remaining fragments, causing the reassembly buffers to fill up. Systems mitigate this with timers that expire incomplete reassembly attempts.

## TCP State Tracking and Stateful Firewalls

Early packet filters (and still many simple ACLs on routers) make filtering decisions on each packet independently, based solely on the headers of that packet. They do not track the state of TCP connections. This means that to allow TCP traffic in both directions, you must write rules permitting traffic from both sides. To allow outbound web browsing (TCP to port 80), you need a rule allowing traffic from internal hosts to external port 80, and a rule allowing traffic from external port 80 to internal hosts (the return traffic). If an attacker crafts a packet from an external host with a source port of 80 and appropriate flags, it might be allowed through the stateless filter even though it is not part of any legitimate connection.

Stateful firewalls maintain a connection tracking table that records the state of every TCP connection passing through. When a SYN packet is allowed through, an entry is created in the connection table. When the SYN-ACK and ACK follow, the entry is updated. When data flows, the sequence numbers are tracked. When the FIN and final ACK are exchanged, the entry is removed. Subsequent packets are only allowed if they match an existing entry in the connection table. This means that crafted packets from external attackers cannot be sent to internal hosts unless they are part of an established connection.

Connection tracking tables are stored in memory and have finite capacity. This is why SYN flood attacks are effective even against stateful firewalls: each SYN packet causes an entry to be created in the firewall’s connection table, and if the table fills up, no new connections can be established.

Linux’s netfilter (the kernel’s packet filtering framework) maintains a connection tracking table that can be viewed with conntrack -L. The table shows each tracked connection with its state: SYN_SENT, SYN_RECV, ESTABLISHED, FIN_WAIT, CLOSE_WAIT, TIME_WAIT. Understanding this table is valuable for debugging firewall issues and for understanding what connections are currently active on a system.

## Practical Skills: Building This Knowledge

Reading about protocol fields is necessary but not sufficient. You need to actually work with packets. Here is a structured set of practical exercises that will cement this knowledge.

Start by installing Wireshark and setting up a small lab — even two virtual machines on your own computer are sufficient. Capture traffic between them as you do various things: browse a website, make an SSH connection, run an Nmap scan. Look at every packet. Expand every header. Find the fields we discussed and confirm that they contain what you expect.

Practice writing Wireshark display filters until they feel natural. Given a capture file with thousands of packets, you should be able to immediately write a filter that shows only the traffic you care about.

Learn to use tcpdump, which is the command-line equivalent of Wireshark. In real incident response scenarios, you often cannot run a GUI application on a server — you need tcpdump. The basic syntax is tcpdump -i eth0 -w capture.pcap to capture all traffic on interface eth0 and write it to a file, and tcpdump -r capture.pcap to read a previously captured file. Combine with BPF filters: tcpdump -i eth0 'tcp port 80 and host 192.168.1.100'.

Practice with Scapy, a Python library that allows you to craft, send, receive, and analyze packets at an extremely low level. With Scapy, you can construct packets field by field, send them, and inspect the responses. This is invaluable for understanding protocol behavior and for testing.

A basic Scapy example that sends a SYN and captures the response looks like this: you create an IP layer with the destination address, a TCP layer with the target port and the SYN flag set, stack them together, and send them with the sr1 function (send and receive one response). The response tells you whether the port is open (SYN-ACK) or closed (RST) or filtered (no response). This is mechanically what Nmap’s SYN scan does, but by doing it yourself in Scapy you understand exactly what is happening.

Practice with hping3, a command-line tool for crafting TCP/IP packets. hping3 -S -p 80 target sends SYN packets to port 80. hping3 --flood -S -p 80 target sends SYN packets as fast as possible, simulating a SYN flood. hping3 -a spoofed_ip -S -p 80 target sends SYN packets with a spoofed source address. These operations, performed in a controlled lab environment, teach you what various attacks look like at the packet level.

Read RFC 793 (TCP) and RFC 791 (IP). They are long and technical, but they are the authoritative source. When you encounter behavior that surprises you, the RFC is where you find the answer.

## From Packets to Security Operations

Everything we have covered directly translates to security operations. When a SOC analyst looks at an alert, they are looking at metadata derived from network packets. When a penetration tester runs a port scan, they are sending crafted packets and interpreting responses. When a malware analyst examines network traffic from a sandbox, they are reading packet captures. When an incident responder tries to understand what an attacker did on a network, they are correlating flow records and packet captures.

A network intrusion detection system like Snort or Suricata applies rules against packet streams. Understanding how those rules work — matching on specific bytes at specific offsets in specific protocol headers — requires exactly the knowledge in this article. A rule that matches TCP traffic with the SYN flag set to a specific destination port is expressing a condition on the flags field of the TCP header and the destination port field, which you now know exactly where to find.

A web application firewall that inspects HTTP traffic is operating at the application layer but still relies on the lower layers to deliver the traffic to it. Understanding how traffic arrives — through TCP streams assembled from IP packets, which were carried in Ethernet frames — helps you understand the attack surface of the WAF itself.

Encrypted traffic analysis (ETA) is a growing field that extracts security-relevant information from TLS-encrypted traffic without decrypting it. The techniques rely on metadata that is visible even in encrypted sessions: the sizes of packets, their timing, the sequence of packet sizes and directions (the traffic flow’s “fingerprint”), the TLS handshake parameters (cipher suites, certificate information, client hello characteristics). All of this metadata is in the lower layers — the IP and TCP headers — which are not encrypted. This is why TLS does not make traffic fully opaque to a sophisticated observer.

## What Comes Next

This article has given you the packet-level foundation that everything else in network security is built on. You now understand how bits become frames, how frames become packets, how packets become segments, and how every field in every header can be inspected, manipulated, or exploited.

The next article in this series goes one layer up: DNS. The Domain Name System is the phone book of the internet, and it is also one of the most abused protocols in both offense and defense. DNS-based attacks — cache poisoning, DNS hijacking, DNS tunneling, domain generation algorithms, fast flux — are pervasive in modern malware and advanced persistent threat operations. And DNS-based defenses — DNS filtering, RPZ (Response Policy Zones), passive DNS, DNS anomaly detection — are some of the most cost-effective security controls available. Understanding DNS at a deep level requires exactly the foundation you have now: knowing how a DNS query travels from your machine to a resolver, how the response comes back, and what can go wrong (or be made to go wrong) at every step.

The packet is where security begins. Everything else is built on top of it.