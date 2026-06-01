# Home Security Lab
 
A personal lab built to study enterprise network architecture, firewall management, and network segmentation.
 
---
 
## Objectives
 
- Implement enterprise-grade network segmentation using VLANs
- Configure and manage a stateful firewall with strict access control policies
- Deploy VPN tunnelling with kill switch and policy-based routing
- Practice blue team concepts: isolation, monitoring, and lateral movement prevention
---
 
## Architecture
 
All traffic passes through a central firewall before reaching any segment or the internet. No direct communication between segments is possible without an explicit firewall rule.
 
```
[ISP]
  |
[Firewall / Router]
  |
[Managed Switch]
  |
  +-- Segment A 
  +-- Segment B 
  +-- Segment C
  +-- Segment D 
  +-- Segment E 
  +-- Segment F 
  +-- Segment G
```
 
---
 
## Network Segmentation
 
| Purpose                            | Internet      | Inter-Segment |
|------------------------------------|---------------|---------------|
| Internal services and compute      | Yes           | Segment B only |
| Daily-use machines                 | Yes           | Segment A only |
| Visitor access                     | Yes           | None |
| Smart home devices                 | Via VPN only  | None |
| Streaming, IPTV                    | Via VPN only  | None |
| Security testing, CTF              | Yes           | None |
| Infrastructure control plane       | Yes           | Full Access - Physically only |
 
---
 
## Security Policies
 
**Default Deny**
All inter-segment traffic is blocked unless an explicit rule permits it.
 
**RFC1918 Blocking**
Each segment has a firewall rule blocking all private address space. Internet-bound traffic passes. Internal traffic does not, unless explicitly allowed above the block rule.
 
**Management Isolation**
The management segment is accessible via physical cable only. All infrastructure interfaces (firewall GUI, hypervisor, switch management) listen exclusively on this segment.
 
**VPN Kill Switch**
The two VLAN's with VPN segment routes all traffic through a WireGuard tunnel to an external exit node. If the tunnel drops, traffic is blocked entirely — no fallback to the ISP connection.
 
**Global Firewall Rule**
A floating rule blocks access to management ports (SSH, HTTPS, hypervisor GUI) from all segments except management. Applies automatically to any new segment added.
 
---
 
## Stack
 
Proxmox / OPNsense / WireGuard / 802.1Q VLANs / Dnsmasq / Linux
 
