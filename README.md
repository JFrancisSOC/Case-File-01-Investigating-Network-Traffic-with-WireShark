# Case File 01: Investigating Network Traffic

## Case Overview

| Case Detail | Information |
|---|---|
| Case ID | CF-01 |
| Investigation Type | Network Traffic Review |
| Analyst | Jamaal Francis |
| Alert Source | No alert — home-lab investigation |
| Severity | Informational |
| Status | Closed |
| Operating System | Windows 11 |
| Analysis Tool | Wireshark 4.6.7 |
| Capture Interface | Wi-Fi |
| Local IP Address | `192.168.1.155` |
| Router/DNS Server | `192.168.1.1` |
| Final Disposition | Normal network activity |

## Case Summary

I completed a live network traffic investigation using Wireshark. This case was not started by a security alert. I performed the investigation to practice how a SOC analyst captures traffic, reviews packets, filters protocols, and documents findings.

I captured traffic from my Wi-Fi connection and reviewed the IP addresses, domains, ports, protocols, TCP flags, and packet details. I also followed a TCP stream to examine the communication between my computer and an external server.

## Investigation Objective

My objective was to:

- Identify the active network interface.
- Capture live network traffic.
- Identify my computer’s local IP address.
- Review source and destination IP addresses.
- Identify the protocols being used.
- Filter DNS and TCP traffic.
- Examine DNS requests.
- Review TCP ports, flags, and header information.
- Follow one TCP conversation.
- Determine whether the reviewed traffic appeared normal or suspicious.

## Evidence Collected

The following evidence was captured during the investigation:

1. Wireshark network interfaces
2. Live packet capture
3. Captured traffic and identified protocols
4. DNS-filtered traffic
5. TCP packet details
6. TCP header details
7. Follow TCP Stream conversation

## Network Indicators

### Internal Network Information

| Indicator | Value | Purpose |
|---|---|---|
| Local IP address | `192.168.1.155` | Computer being investigated |
| Router/DNS server | `192.168.1.1` | Local router used to process DNS requests |
| DNS port | `53` | Domain name requests |
| HTTP port | `80` | Unencrypted web communication |
| HTTPS port | `443` | Encrypted web communication |

### External IP Addresses Observed

- `20.59.128.9`
- `72.154.7.114`
- `172.183.7.192`
- `20.184.175.19`
- `20.184.175.23`
- `13.89.179.15`

### Domains and Hostnames Observed

- `history.google.com`
- `settings-win.data.microsoft.com`
- `v10.events.data.microsoft.com`
- `img-s-msn-com.akamaized.net`

### Protocols Observed

- DNS
- UDP
- TCP
- HTTP
- TLS 1.2
- QUIC
- IPv4
- IPv6

# Investigation Timeline

## Step 1 — Identified the Active Network Interface

I opened Wireshark and reviewed the available network interfaces. The list included Wi-Fi, Ethernet, loopback traffic, Bluetooth, and several local network connections.

I selected the Wi-Fi interface because the activity graph showed that it was actively sending and receiving network traffic.

### Evidence 01 — Wireshark Interfaces

![01 Wireshark Interfaces](01%20WireShark%20Interfaces.png)

## Step 2 — Started the Live Packet Capture

I started a live capture on the Wi-Fi interface. Wireshark began displaying the network traffic going to and from my computer.

The packet list showed:

- Packet numbers
- Timestamps
- Source addresses
- Destination addresses
- Protocols
- Packet lengths
- Packet information

One selected packet showed TCP traffic traveling from external IP address `20.59.128.9` to my computer at `192.168.1.155`.

| Packet Detail | Observed Value |
|---|---|
| Source IP | `20.59.128.9` |
| Destination IP | `192.168.1.155` |
| Protocol | TCP |
| Source port | `80` |
| Destination port | `62946` |
| TCP flags | PSH, ACK |
| Frame size | 2,778 bytes |
| TCP payload | 2,712 bytes |

The source port was `80`, which is normally used for HTTP traffic. The PSH and ACK flags showed that data was being sent through an established connection and previously received data was being acknowledged.

### Evidence 02 — Live Packet Capture

![02 Live Packet Capture](02%20Live%20Packet%20Capture%20.png)

## Step 3 — Identified the Network Protocols

I reviewed the Protocol column and identified several different types of network traffic.

The protocols included:

- DNS
- TCP
- HTTP
- QUIC
- UDP
- IPv4
- IPv6

The packet capture showed a DNS request involving `history.google.com`. I also observed HTTP communication between my computer at `192.168.1.155` and external IP address `20.59.128.9`.

A selected DNS packet showed my computer sending a request to my local router.

| Packet Detail | Observed Value |
|---|---|
| Source IP | `192.168.1.155` |
| Destination IP | `192.168.1.1` |
| Transport protocol | UDP |
| Destination port | `53` |
| Application protocol | DNS |

### Evidence 03 — Captured Network Traffic and Identified Protocols

![03 Captured Network Traffic and ID Protocols](03%20Captured%20Network%20Traffic%20and%20Id%20Protocols.png)

## Step 4 — Filtered the DNS Traffic

I entered the following Wireshark display filter:

`dns`

This filter removed unrelated packets and displayed only DNS traffic.

I observed my computer at `192.168.1.155` sending DNS requests to the router and DNS server at `192.168.1.1`.

One of the selected requests was for `settings-win.data.microsoft.com`.

| DNS Detail | Observed Value |
|---|---|
| Source IP | `192.168.1.155` |
| Destination IP | `192.168.1.1` |
| Source port | `49876` |
| Destination port | `53` |
| Requested domain | `settings-win.data.microsoft.com` |
| DNS record types | A and AAAA |

The A request was looking for an IPv4 address. The AAAA request was looking for an IPv6 address.

The responses also displayed CNAME information connected to Microsoft’s traffic-management service.

### Evidence 04 — DNS Traffic Filter

![04 DNS Traffic Filter](04%20%20DNS%20Traffic%20Filter.png)

## Step 5 — Analyzed a TCP Connection

I entered the following display filter:

`tcp`

This filter allowed me to focus on TCP traffic.

I selected packet `1203`, which showed my computer attempting to start an HTTPS connection with an external server.

| TCP Detail | Observed Value |
|---|---|
| Packet number | `1203` |
| Source IP | `192.168.1.155` |
| Destination IP | `20.184.175.19` |
| Source port | `61126` |
| Destination port | `443` |
| TCP flag | SYN |
| Frame size | 66 bytes |

The SYN flag showed that my computer was requesting the start of a TCP connection.

The packets that followed included SYN-ACK and ACK packets. This showed the TCP three-way handshake used to establish the connection.

The traffic then showed a TLS 1.2 Client Hello for `v10.events.data.microsoft.com`. The destination port was `443`, which indicated encrypted HTTPS communication.

### Evidence 05 — TCP Packet Analysis

![05 TCP Packet Analysis](05%20TCP%20Packet%20Analysis.png)

## Step 6 — Examined the TCP Header

I selected packet `358` and expanded the Transmission Control Protocol section.

This allowed me to review the information stored inside the TCP header.

| TCP Header Detail | Observed Value |
|---|---|
| Packet number | `358` |
| Source IP | `192.168.1.155` |
| Destination IP | `13.89.179.15` |
| Source port | `60896` |
| Destination port | `443` |
| Sequence number | `10544` |
| Acknowledgment number | `4679` |
| TCP flag | ACK |
| TCP header length | 20 bytes |
| TCP segment length | 0 bytes |
| Window value | `254` |
| Calculated window size | `65024` |
| Window scaling factor | `256` |
| Conversation status | Complete, with data |

The ACK flag showed that my computer was acknowledging data received during an established TCP connection.

The TCP segment length was zero, meaning the selected packet was acknowledging traffic without carrying additional application data.

The destination port was `443`, which showed that the connection was using HTTPS. The surrounding packets also showed TLS 1.2 communication involving `v10.events.data.microsoft.com`.

### Evidence 06 — TCP Header Analysis

![06 TCP Header Analysis](06%20TCP%20Header%20Analysis.png)

## Step 7 — Followed the TCP Stream

I used Wireshark’s Follow TCP Stream feature to separate one TCP conversation from the rest of the capture.

| TCP Stream Detail | Observed Value |
|---|---|
| Stream number | `5` |
| Client packets | `3` |
| Server packets | `6` |
| Communication turns | `3` |
| Total conversation size | 5,717 bytes |
| Display format | ASCII |
| Hostname visible | `img-s-msn-com.akamaized.net` |

Most of the information appeared unreadable because the application data was encrypted.

The different colors represented traffic traveling in opposite directions between the client and server. Following the stream helped me focus on one conversation without unrelated packets appearing in the results.

### Evidence 07 — Follow TCP Stream Conversation

![07 Follow TCP Stream Conversation](07%20Follow%20TCP%20Stream%20Conversation.png)

# Investigation Findings

During the investigation, I confirmed the following:

1. My computer used local IPv4 address `192.168.1.155`.
2. My router and DNS server used IP address `192.168.1.1`.
3. DNS requests were sent through UDP port `53`.
4. HTTP traffic was observed on TCP port `80`.
5. HTTPS and TLS traffic were observed on TCP port `443`.
6. DNS requests involving Google and Microsoft services were present.
7. The TCP traffic included SYN, SYN-ACK, ACK, and PSH-ACK packets.
8. The SYN, SYN-ACK, and ACK packets showed the TCP three-way handshake.
9. The packet capture included DNS, UDP, TCP, HTTP, TLS 1.2, QUIC, IPv4, and IPv6 traffic.
10. The TCP stream contained encrypted application data.
11. The hostname `img-s-msn-com.akamaized.net` was visible inside the TCP stream.
12. I did not identify clear signs of malicious network activity in the packets I reviewed.

# Analyst Assessment

The traffic I reviewed appeared consistent with normal web browsing, Windows services, Microsoft background communication, and other regular network activity.

The external IP addresses communicated through common web ports such as `80` and `443`. The DNS requests were connected to recognizable Google, Microsoft, and MSN-related services.

The encrypted TCP stream was expected because HTTPS traffic protects the application data from being read as plain text.

I did not find enough evidence to classify the traffic as malicious.

# Actions Taken

- Captured live traffic from the Wi-Fi interface.
- Preserved screenshots of the investigation.
- Identified the local IP address and DNS server.
- Documented external IP addresses and domains.
- Filtered DNS traffic using `dns`.
- Filtered TCP traffic using `tcp`.
- Reviewed DNS requests and responses.
- Examined TCP flags and header values.
- Identified a TCP three-way handshake.
- Followed and reviewed a TCP stream.
- Recorded the findings and final disposition.

No containment or remediation action was required because no malicious activity was identified.

# Final Disposition

| Closure Detail | Result |
|---|---|
| Case status | Closed |
| Severity | Informational |
| Activity classification | Normal network activity |
| Malicious activity identified | No |
| Containment required | No |
| Evidence reviewed | Seven screenshots |
| Analyst | Jamaal Francis |

I completed the investigation after reviewing the packet capture, documenting the network indicators, analyzing the DNS and TCP traffic, and following a TCP stream.

The evidence showed normal network communication, and no clear malicious activity was found. Case File 01 was closed as informational.
