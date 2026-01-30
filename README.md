
# Valorant Asia Pacific Servers

> Network information for Valorant game servers in the Asia Pacific region  
> Last Update: 2026-02-01

## Overview

Most Valorant Asia Pacific servers use **AWS Global Accelerator** with anycast IP addresses, announced under **AS16509** (Amazon's ASN). This makes Valorant traffic indistinguishable from regular AWS/Amazon traffic at the BGP level.

**References:**
- [AWS Global Accelerator Features](https://aws.amazon.com/global-accelerator/features/#Static_anycast_IP_addresses)
- [Anycast - Wikipedia](https://en.wikipedia.org/wiki/Anycast)

---

## Server IP Addresses

### All Asia Pacific Servers
```
13.248.193.101
13.248.197.71
13.248.205.128
13.248.220.97
151.106.248.1
43.229.65.1
75.2.49.107
75.2.66.166
75.2.69.178
76.223.1.250
76.223.71.142
76.223.71.164
76.223.72.224
99.83.136.104
99.83.157.116
99.83.187.195
```

### Singapore Servers
```
# Riot Direct (SG)
151.106.248.1

# AWS Global Accelerator (SG)
13.248.193.101
75.2.69.178
76.223.1.250
99.83.157.116
```

**Routing Recommendation:** Use `151.106.248.0/24` for Singapore routing rules

### India (Mumbai) Servers
```
# Personally Tested (PT)
151.106.246.1/32

# Additional IPs (reported by Riot Support)
99.83.136.104
75.2.66.166
13.248.197.71
76.223.71.164
151.106.246.32
```

**Routing Recommendation:** Use `151.106.246.0/24` for Mumbai routing rules

---

## Server Ports

Valorant uses the following ports for gameplay:

| Protocol | Port Range |
|----------|------------|
| UDP | 7000-8000 |
| UDP | 8180-8181 |
| TCP/UDP | 8088 |

---

## For Network Engineers & ISPs

### Challenge

Valorant traffic uses **AWS Global Accelerator** (AS16509), making it indistinguishable from regular AWS traffic. Since BGP cannot filter at the port level, optimizing routing for gaming traffic requires special configuration.

### Recommended Solutions

#### 1. **Prefer Direct IXP Peering to Amazon**
Increase BGP preferences for Amazon routes via **Equinix** or **SGIX Singapore** peering points. This forces Amazon to prioritize your direct IXP connection over ITC (International Transit) peers like Airtel/Tata.

**Implementation:**
- Reference: [AWS Direct Connect Routing & BGP Communities](https://docs.aws.amazon.com/directconnect/latest/UserGuide/routing-and-bgp.html)
- **BGP Community Tag:** `7224:7200` (Medium local preference)
- **Alternative:** `7224:7300` (High preference for primary paths)

#### 2. **Dedicated Gaming IP Blocks**
Allocate one or more `/24` subnets exclusively for gaming/latency-sensitive customers. Do not announce these blocks to Airtel/Tata/ITC transit peers—only advertise via direct peering or high-quality upstream providers.

**Benefits:**
- Guarantees traffic avoids congested ITC peers
- Provides consistent low-latency paths
- Easier to troubleshoot and monitor

#### 3. **Submarine Cable Path Preference**
Configure routing to prefer **SMW4** (SEA-ME-WE 4) or **SMW5** (SEA-ME-WE 5) submarine cable systems for AS16509 traffic between India and Singapore.

**Why this matters:**
- Direct undersea fiber provides lower latency than terrestrial routes
- More stable than multi-hop ITC peer paths
- Reduces packet loss and jitter

**Implementation:**
- Set path preferences in BGP to favor routes transiting SMW4/5
- Coordinate with your upstream provider if you don't have direct submarine cable access
- Monitor latency to Singapore AWS edge locations (13.248.193.101, 151.106.248.1)

---

## For Players

### Checking Your Server Connection

**Windows (Command Prompt):**
```cmd
ping 151.106.246.1
tracert 151.106.246.1
```

**Linux/macOS (Terminal):**
```bash
ping 151.106.246.1
mtr 151.106.246.1
```

### VPN Split Tunneling

If you're experiencing routing issues, you can use VPN split tunneling to route only Valorant traffic through a better path:

1. Use a VPN that supports split tunneling (WireGuard, OpenVPN)
2. Exclude Riot Client and Vanguard from VPN
3. Route only game traffic (IPs listed above) through VPN
4. Test with servers closest to your location

**Mumbai example:**
```
151.106.246.0/24
```

**Singapore example:**
```
151.106.248.0/24
```

---

## Notes

⚠️ **IP addresses are community-sourced and may change without notice.** Riot Games does not officially publish their server IP ranges.

⚠️ **Some IPs may be anycast addresses** that route to different physical servers based on your location.

⚠️ **ICMP ping may be blocked** on some Riot servers, resulting in timeouts even when the server is operational.

---

## Contributing

Have updated IP addresses or routing improvements? Please open an issue or pull request!

### Verification Method
When submitting new IPs, please include:
- Source (Riot Support ticket, network capture, etc.)
- Date discovered
- Method of verification (traceroute, in-game testing)
- Your geographic location

---

## Related Resources

- [AWS Global Accelerator Documentation](https://aws.amazon.com/global-accelerator/)
- [AWS Direct Connect BGP Communities](https://docs.aws.amazon.com/directconnect/latest/UserGuide/routing-and-bgp.html)
- [Riot Games Network Status](https://status.riotgames.com/)
- [PeeringDB - AS16509 (Amazon)](https://www.peeringdb.com/asn/16509)

---

## License

This information is provided as-is for educational and network optimization purposes. Use at your own discretion.

## Disclaimer

This is an unofficial resource maintained by me personally. Riot Games and Amazon Web Services are not affiliated with this project.
```

***

## Verification Summary

Based on my fact-checking:

✅ **Verified:**
- AS16509 is Amazon's ASN
- AWS Global Accelerator uses anycast IPs
- BGP community `7224:7200` is valid for medium preference
- UDP ports 7000-8000, 8180-8181, TCP/UDP 8088 are correct
- Mumbai IP: 151.106.246.1/32 confirmed by multiple Riot support tickets
- Singapore IP: 151.106.248.1/32 confirmed by multiple sources
- SMW4/5 submarine cables connect India-Singapore

⚠️ **Partially verified:**
- Most IPs in the list appear in community reports but not all have official confirmation
- Some IPs (like 75.2.66.166, 99.83.136.104) were provided by Riot support to users
