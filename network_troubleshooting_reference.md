# Network Connectivity Troubleshooting Reference

A structured, layer-by-layer approach to diagnosing "no internet" issues, built around the same logic used in real help desk and IT support workflows.

## Overview

Most "the internet is down" tickets are actually a failure in one specific layer of the connection, not the whole thing. This reference walks through a diagnostic chain that tests each layer in order — DHCP, local network, internet reachability, and DNS — so the actual point of failure can be isolated quickly instead of guessed at.

**Core principle:** each step is only meaningful once the previous step has passed. Skipping ahead wastes time; testing in order narrows the problem systematically.

## Scope-First Triage

Before running any diagnostic commands, determine the scope of the problem:

**Is it one device, or multiple devices/everyone affected?**

- **One device affected** → likely a software/configuration issue on that machine. This is the diagnostic chain below.
- **Multiple devices / everyone affected** → likely hardware or infrastructure (router, modem, ISP outage). Outside a help desk technician's typical access — escalate to network engineering, or for home/ISP connections, the standard first step is a hardware power cycle (unplug modem/router, wait ~2 minutes, plug back in).

## The Diagnostic Chain

| Step | Tests | Command (Windows CMD) | Command (PowerShell) | Command (macOS) | Command (Linux) |
|---|---|---|---|---|---|
| 1. DHCP | Did the machine get a valid IP address? | `ipconfig /all` | `Get-NetIPConfiguration -Detailed` | `ifconfig` | `ip a` |
| 2. Local network | Can the machine reach its own router/gateway? | `ping [gateway]` | `Test-NetConnection [gateway]` | `ping [gateway]` | `ping [gateway]` |
| 3. Internet | Can the router reach the wider internet? | `ping 8.8.8.8` | `Test-NetConnection 8.8.8.8` | `ping 8.8.8.8` | `ping 8.8.8.8` |
| 4. DNS | Do domain names resolve to IP addresses? | `ping google.com` | `Resolve-DnsName google.com` | `ping google.com` | `ping google.com` |

**Reading the results:**
- Step 1 fails (address shows `169.254.x.x`) → DHCP failure, the OS self-assigned a fallback address because it couldn't reach a DHCP server (Windows calls this APIPA)
- Step 2 fails → local network problem: Wi-Fi adapter, cable, wrong network selected, local firewall, or the router itself
- Step 2 passes but step 3 fails → problem is beyond the local network: ISP outage, modem lost its upstream connection, or router WAN misconfiguration
- Step 3 passes but step 4 fails → DNS-specific issue: raw IP addresses work, but names don't resolve. This is the classic case where a user reports "the internet is down" but the actual connection is completely healthy — only name resolution is broken.

## Failure Modes and Fixes

### DHCP Failure

**Symptom:** IP address shows as `169.254.x.x` instead of a normal address (e.g. `192.168.1.x`).

**Fix:**
```
ipconfig /release
ipconfig /renew
```
Releasing first matters — without it, the OS will often just re-request the same address it already had, which does nothing if that configuration was the actual problem.

**Common causes, most to least likely:**
1. Router/DHCP server down or overloaded
2. Wi-Fi/cable connection dropped (never reached the router at all)
3. IP address pool exhausted (rare at home, more common on busy networks)
4. VPN or virtual adapter conflict
5. Leftover static IP misconfiguration

**Escalation order if release/renew doesn't resolve it:**
1. Check the physical connection (cable seated, correct Wi-Fi network selected)
2. Restart the router, if within scope of access
3. Escalate to network team if the router isn't user/technician-controlled

### DNS Failure

**Symptom:** `ping 8.8.8.8` succeeds, but `ping google.com` fails with a "could not find host" error.

**Fix 1 — clear the local DNS cache:**
```
ipconfig /flushdns          (Windows)
sudo dscacheutil -flushcache        (macOS)
sudo systemd-resolve --flush-caches (Linux)
```

**Fix 2 — switch to a known-reliable public DNS server** (e.g. 8.8.8.8 or 1.1.1.1) in network adapter settings. If DNS starts resolving correctly after the switch, it confirms the original DNS server was the actual point of failure.

**Common causes, most to least likely:**
1. DNS server itself down or unreachable
2. Bad/stale DNS cache on the local machine
3. Misconfigured DNS settings (wrong server manually set)
4. ISP's DNS servers experiencing an outage
5. Firewall or security software blocking DNS traffic

### Firewall-Related Issues

Firewalls filter traffic by port, protocol (TCP/UDP), direction (inbound/outbound), and sometimes by specific application. A common pattern: one specific service fails (e.g. Remote Desktop) while general web browsing works fine — this points at a single blocked port rather than a broken connection overall.

**Check firewall status:**
```
netsh advfirewall show allprofiles       (CMD)
Get-NetFirewallProfile                    (PowerShell)
```

**Diagnostic isolation test:** temporarily disable the firewall, retest the failing service, then re-enable immediately. This is a diagnostic step only — never a permanent fix.

**Test whether a specific port is reachable:**
```
Test-NetConnection -ComputerName [target] -Port [port number]
```
`TcpTestSucceeded: True` means the port is open — the issue is elsewhere. `False` means that specific port is blocked, narrowing the fix to a firewall rule.

**Common ports:** 80 (HTTP), 443 (HTTPS), 53 (DNS), 3389 (RDP), 22 (SSH)

## Efficient Remote Troubleshooting

When walking a user through diagnostics verbally (e.g. when the connection issue prevents remote screen-sharing tools from working), reading a full `ipconfig /all` output aloud is slow and error-prone. Filtering the output to only the relevant line reduces both time and room for miscommunication:

```
ipconfig | findstr /i "IPv4"
ipconfig | findstr /i "IPv4 Gateway"
```

PowerShell equivalent, returning only the raw value:
```
(Get-NetIPConfiguration).IPv4Address.IPAddress
```

## Key Terms

- **ICMP** — the protocol `ping` uses. Unlike TCP, there is no connection handshake; it's a single request/reply exchange.
- **TTL (Time To Live)** — a hop counter that decreases by 1 at every router a packet passes through, preventing packets from looping forever. TTL values aren't directly comparable across different destinations (different systems use different starting values), but a sudden TTL change for the *same* destination over time can indicate the network route changed.
- **Packet loss** — should be 0% on a healthy connection; any loss indicates an unstable link worth investigating further.

## Example Application: Walkthrough of a Live Call

The following is a worked example applying this diagnostic chain during a simulated support call for a "no internet" ticket on a single Windows device:

1. Confirmed scope first — asked whether other devices in the home were affected. Only one laptop was impacted, narrowing the issue to that device rather than shared infrastructure.
2. Confirmed OS (Windows 11) before selecting commands.
3. Walked the caller through opening PowerShell and running `ipconfig /all`.
4. Identified a `169.254.x.x` address, diagnosed it as a DHCP failure, and explained the cause in plain language ("your computer doesn't have a proper internet address, similar to a mailing address").
5. Ran `ipconfig /release` followed by `ipconfig /renew`.
6. Confirmed the new address was valid (`192.168.1.x` range) with a working default gateway.
7. Verified the fix by having the caller test internet access directly before closing the ticket.

This mirrors the sequence a real ticket of this type would follow: scope check, OS confirmation, diagnosis, fix, verification.
