# Wireshark IP Analysis

## Overview

This project documents a Wireshark analysis of IPv4 and IPv6 traffic using packet capture files provided as part of the Net-Centric Computing coursework at Florida International University.

The analysis focuses on examining IP header fields, traceroute behavior, IPv4 fragmentation, and IPv6 addressing. It also includes the inspection of DNS AAAA requests and responses to identify IPv6 addresses returned for a domain.

## Objectives

- Identify IPv4 source and destination addresses.
- Analyze the Time-to-Live (TTL) field and its behavior during traceroute.
- Examine IPv4 header length, total length, payload size, and upper-layer protocol fields.
- Observe patterns in the IPv4 Identification and Header Checksum fields.
- Analyze IPv4 fragmentation using the More Fragments flag and Fragment Offset.
- Identify first, intermediate, and final fragments of a fragmented IPv4 datagram.
- Examine IPv6 source and destination addresses.
- Analyze the IPv6 Flow Label, Payload Length, and Next Header fields.
- Inspect DNS AAAA requests and responses for IPv6 address resolution.

## Tools and Trace Files

- **Wireshark** — used to inspect and analyze captured network packets.
- **`ip-wireshark-trace1-1.pcapng`** — used for the IPv4, traceroute, TTL, header-field, and fragmentation analysis.
- **`ip-wireshark-trace2-1.pcapng`** — used for the IPv6 and DNS AAAA analysis.

The packet captures were analyzed using Wireshark display filters and packet-detail inspection to isolate relevant traffic and examine individual IP header fields.

## IPv4 Traceroute Analysis

The first trace was used to examine IPv4 datagrams generated during a traceroute to `gaia.cs.umass.edu`.

Key observations included:

- **Source IPv4 address:** `192.168.86.61`
- **Destination IPv4 address:** `128.119.245.12`
- **Upper-layer protocol:** UDP (`17`)
- **IPv4 header length:** `20 bytes`
- **Total IP datagram length:** `56 bytes`
- **Payload size:** `36 bytes`

The first traceroute probe used a **TTL value of 1**. Traceroute then sent multiple probes for each hop before incrementing the TTL, producing a pattern such as:

`1, 1, 1 → 2, 2, 2 → 3, 3, 3 → 4, 4, 4`

This behavior allows traceroute to collect multiple round-trip measurements for each hop and account for packet loss or response variation.

Across the traceroute sequence, the **Identification**, **Time to Live**, and **Header Checksum** fields changed, while fields such as the IPv4 version, header length, source address, destination address, Differentiated Services field, and upper-layer protocol remained constant.

The IPv4 Identification field also showed a sequential pattern, increasing by one between successive datagrams.

## IPv4 Fragmentation Analysis

The trace was also used to examine how IPv4 handles datagrams that are divided into multiple fragments.

Packet `179` was identified as the **first fragment** of a fragmented IPv4 datagram. Its header showed:

- **Total Length:** `1500 bytes`
- **Fragment Offset:** `0`
- **More Fragments (MF):** Set
- **Upper-layer protocol:** UDP (`17`)

A Fragment Offset of `0` indicates that packet 179 is the first fragment, while the **More Fragments flag being set** indicates that additional fragments follow.

The subsequent fragments retained the same **Identification** value so they could be associated with the same original IP datagram. Between fragments, the **Fragment Offset** changed to indicate where each fragment belonged in the original datagram, and the **Header Checksum** changed as the IP header changed.

Wireshark indicated that the fragmented datagram was **reassembled in packet 181**. The final fragment was identified by a non-zero Fragment Offset together with the **More Fragments flag not being set**, indicating that no additional fragments followed.

## IPv6 Header Analysis

The second trace was used to examine an IPv6 datagram carrying a DNS AAAA request for `youtube.com`.

For packet `20`, the IPv6 header contained:

- **Source IPv6 address:** `2601:193:8302:4620:215c:f5ae:8b40:a27a`
- **Destination IPv6 address:** `2001:558:feed::1`
- **Flow Label:** `0x63ed0`
- **Payload Length:** `37 bytes`
- **Next Header:** UDP (`17`)

The IPv6 source and destination addresses were displayed using standard compressed notation, where consecutive zero-valued groups may be represented using `::`.

The **Flow Label** identifies packets that may belong to the same traffic flow, while the **Next Header** field specifies the protocol carried in the IPv6 payload. In this datagram, the payload was delivered to UDP.

## DNS AAAA Response Analysis

The IPv6 trace was also used to inspect DNS resolution for `youtube.com` using an AAAA query.

Packet `20` contained the DNS AAAA request with transaction ID `0x920d`. The matching response was found in packet `27`, which used the same transaction ID.

The DNS response contained:

- **Answer RRs:** `1`
- **Returned IPv6 address:** `2607:f8b0:4006:815::200e`

Matching the transaction ID between the request and response confirmed that packet 27 was the correct DNS response associated with the AAAA query.

This analysis demonstrated how Wireshark can be used to trace an IPv6 DNS request and identify the IPv6 address returned by the DNS server.

## Key Findings

- The traceroute traffic originated from `192.168.86.61` and targeted `128.119.245.12`.
- IPv4 traceroute probes used UDP (`17`) and increased the TTL value by hop, with three probes sent for each TTL level.
- The IPv4 Identification field increased sequentially, while the Header Checksum changed as header values changed.
- IPv4 fragmentation was identified using the **More Fragments flag** and **Fragment Offset**.
- The first fragment used a Fragment Offset of `0`, while later fragments used non-zero offsets.
- The final fragment was identified when the **More Fragments flag was not set**.
- Wireshark reassembled the fragmented IPv4 datagram in packet `181`.
- The IPv6 trace showed a DNS AAAA request using UDP (`17`) with a Flow Label of `0x63ed0`.
- The DNS AAAA response for `youtube.com` returned the IPv6 address `2607:f8b0:4006:815::200e`.

## Conclusion

This lab provided hands-on experience analyzing both IPv4 and IPv6 traffic in Wireshark. The packet traces were used to examine how traceroute relies on TTL values to discover network hops, how IPv4 header fields behave across related datagrams, and how fragmentation is represented and reassembled.

The analysis also demonstrated how IPv6 headers differ from IPv4 and how DNS AAAA requests and responses can be inspected to identify IPv6 addressing information.

Overall, the lab strengthened practical packet-analysis skills by connecting IP header fields and protocol behavior with real captured network traffic.
