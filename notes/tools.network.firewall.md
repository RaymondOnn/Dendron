---
id: jxlzab0szv5pdpsjac1le0b
title: firewall
desc: ''
updated: 1758377658002
created: 1758361707560
---


## Firewall fundamentals (what you must understand)

### What is a Firewall?

- Inspects network packets/flows and enforces rules: allow, block, NAT, log.
- Operates at several layers: IP, transport, application.

#### Types of Firewalls

- **Host-based:** `iptables`, `nftables`, `ufw`, `firewalld` (Linux), Windows Defender Firewall.
- **Network/perimeter:** Hardware/virtual appliances (Cisco, Palo Alto, FortiGate).
- **Cloud:** AWS Security Groups & NACLs, Azure NSGs, GCP Firewall rules.
- **Application-layer/WAF:** ModSecurity, AWS WAF.
- **Next-Gen Firewall (NGFW):** DPI, IDS/IPS, URL filtering, application awareness.

#### Core Concepts

- **Stateful vs stateless:** Stateful tracks connection state, stateless inspects packets individually.
- **Chains/tables:** iptables (filter, nat, mangle, raw), nftables (families/tables/chains).
- **Default policy:** DROP vs ACCEPT.
- **Zones:** Trusted vs public.
- **NAT:** SNAT/MASQUERADE (outgoing), DNAT/port-forward (incoming).
- **Conntrack:** Kernel connection tracking table.
- **Rules ordering:** First match wins.

#### Important Primitives

- Ports/protocols (TCP/UDP/ICMP)
- IPs/CIDR
- Interface/zone binding
- Time-based/rate-limited rules
- Logging (iptables LOG/syslog)

### Where firewalls can block traffic (the path)

When an app runs on a server, traffic may be blocked at multiple points in the path:

- **Local app misconfiguration** (not a firewall, but check first)
- **Host firewall (INPUT/OUTPUT):** local iptables/nft rules
- **Kernel-level filters:** `rp_filter`, netfilter settings
- **Bridge / virtual interface / Docker host rules:** FORWARD chain
- **Network firewall in the same LAN / corporate edge**
- **Perimeter router / ISP:** rate limits, blocked ports
- **Cloud security groups / virtual firewall:** AWS SG, Azure NSG
- **Load balancer / reverse proxy / WAF:** can block based on IP, headers, or content
- **Destination host’s firewall:** if connecting to a remote service
- **Return path asymmetry:** packets egress a different path that drops replies

### How Firewalls Affect Apps

Most Python apps use libraries like `requests`, `urllib`, `http.client`, or DB drivers.

**What a firewall can block:**

- **Outbound connections:** e.g., `requests.get("https://api.example.com")`
- **Inbound connections:** e.g., Flask app not reachable
- **Ports/protocols:** 80/443 usually allowed; 8080, 3306, 5432 often blocked
- **Destination IP ranges:** Some orgs only allow whitelisted domains
- **Deep Packet Inspection:** MITM SSL/TLS, break HTTPS without CA cert

### Identifying Firewall-Related Errors

Python apps rarely say “firewall blocked this.” Instead, you get network errors:

- **Connection timed out:** firewall silently drops packets
- **Connection refused:** host reachable, port closed (firewall or service not listening)
- **Network is unreachable:** routing/firewall dropping packets
- **SSL certificate verify failed:** corporate firewall intercepting HTTPS with unknown CA
- **Works locally but not on server:** strong indicator of firewall restrictions
- **Works with proxy but not directly:** outbound firewall requires proxy

### Dependencies that must work for network requests to pass through a firewall

For your app to succeed, all must work:

- **Application layer:** correct host/port, SSL CA certs if HTTPS
- **DNS resolution:** server can resolve domain names; firewalls may block DNS (UDP 53)
- **Routing:** default gateway to reach external networks
- **Outbound firewall rules:** host/network firewall allows outgoing traffic on required ports
- **Return path:** firewall must allow reply traffic back in (stateful inspection)
- **SSL/TLS interception:** corporate CA cert installed if firewall MITMs HTTPS

### How to interpret common error symptoms (quick mapping)

| Error Message                        | Interpretation                                                                                  |
|-------------------------------------- |----------------------------------------------------------------------------------------------- |
| **Connection refused (TCP RST)**      | Remote host reachable but port closed OR firewall actively sending TCP RST. Usually means no process listening or firewall responds with RST. |
| **Connection timed out (no response)**| Packets dropped silently (firewall drop or routing issue). Likely a firewall silently dropping or network blackhole. |
| **Network is unreachable**            | Local routing/gateway missing — not a firewall drop.                                            |
| **Destination unreachable (ICMP)**    | Intermediate router responded (ICMP) indicating reachability issue (e.g., admin prohibited, host unreachable). |
| **No route to host**                  | Local OS says it can’t send — routing table or gateway issue.                                   |
| **Intermittent failures / high latency / resets** | Rate limiting, connection tracking exhaustion, or IDS/IPS blocking (e.g., suspicious patterns). |
| **Only some ports fail (e.g., 25 blocked, 443 OK)** | Firewall policy blocking specific services (common: SMTP outbound blocked to prevent spam).     |

### Diagnostic workflow — Bash Commands to Diagnose (No Root Needed)

#### Step A. Check Connectivity (Basic)

```bash
ping -c 4 api.example.com
```

If ping fails but service is HTTP, firewall may block ICMP (not always fatal).

#### Step B. Check DNS

```bash
dig api.example.com +short
nslookup api.example.com
```

If no result → DNS blocked or misconfigured.

#### Step C. Test TCP Connectivity

```bash
nc -vz api.example.com 443
telnet api.example.com 443
```

- Succeeded → firewall allows outbound TCP 443
- Connection refused → host reachable, port blocked or closed
- Hangs/times out → firewall silently dropping

#### Step D. Test with curl

```bash
curl -v https://api.example.com
```

- Timeout/refused → blocked
- SSL cert problem → intercepted SSL

#### Step E. Compare Direct vs Proxy

```bash
curl -v --proxy http://127.0.0.1:3128 https://api.example.com
```

Works with proxy but not without → outbound firewall requires proxy.

#### Step F. Trace Route

```bash
traceroute api.example.com
```

Stops inside local network → firewall/router dropping.

#### Step G. Python Test Snippet

```python
import requests
try:
    r = requests.get("https://api.example.com", timeout=10)
    print("Status:", r.status_code)
except Exception as e:
    print("Error:", e)
```

- Timeout → firewall drop
- SSLError → interception
- Works with proxy settings → outbound firewall present

### How to Tell Firewall vs Proxy vs App Error (Short Checklist)

- **Exact error text**
  - `407` → proxy auth.
  - `certificate verify failed` → TLS / MITM / CA.
  - `connection refused` → port closed / RST.
  - `connection timed out` → likely blocked/dropped (firewall) or unreachable.
- **Does `nc -vz host port` hang?**
  - If yes → drop/firewall somewhere.
  - If it responds RST → port closed.
- **Do you see outbound SYN in tcpdump?**
  - No → local app/config issue.
  - Yes but no SYN-ACK → firewall or remote drop.
- **Does `curl --proxy` via a known working proxy succeed?**
  - If yes and direct fails → likely firewall blocks direct path (or proxy allowed by policy).
- **Test from another machine on same network**
  - If other machine works → issue on host firewall or host routing.
- **Check cloud SGs / NACLs**
  - If cloud rules block, remote hosts can’t be reached even if local firewall is open.

### Common Firewall Causes and Fixes (Practical)

1. **Outbound blocked by host firewall**
   - Symptom: `curl` times out, `nc` hangs, `tcpdump` shows SYN leaving but no response.
   - Fix: add rule to allow outbound port (or change default policy):

     ```bash
     sudo iptables -I OUTPUT 1 -p tcp --dport 443 -j ACCEPT
     ```

     Persist using distro's firewall management (`ufw`/`firewalld` or `iptables-save`).

2. **Inbound port closed (server won’t accept connections)**
   - Symptom: Client sees connection refused or RST. `ss -ltn` shows no listener.
   - Fix: ensure app is listening, then open INPUT port:

     ```bash
     sudo iptables -I INPUT -p tcp --dport 8080 -m conntrack --ctstate NEW -j ACCEPT
     sudo nft add rule inet myfilter INPUT tcp dport 8080 ct state new accept
     ```

3. **DNAT / port-forward broken on gateway**
   - Symptom: `traceroute` shows gateway but no access to internal host from outside. `tcpdump` on gateway sees incoming SYN but not forwarded.
   - Fix: enable NAT rules and forwarding, check `sysctl net.ipv4.ip_forward=1`, create DNAT rule.

4. **conntrack table exhausted**
   - Symptom: new connections fail randomly; `dmesg` may show conntrack overflow messages.
   - Fix: increase `nf_conntrack_max` or fix leaking connections:

     ```bash
     cat /proc/sys/net/netfilter/nf_conntrack_max
     cat /proc/sys/net/netfilter/nf_conntrack_count
     sudo sysctl -w net.netfilter.nf_conntrack_max=262144
     ```

5. **rp_filter dropping replies in asymmetric routing**
   - Symptom: only one direction works; unexpected drops in `tcpdump`.
   - Fix: set `rp_filter` to 0 or 2 depending on security posture:

     ```bash
     sudo sysctl -w net.ipv4.conf.all.rp_filter=0
     sudo sysctl -w net.ipv4.conf.default.rp_filter=0
     ```

6. **ICMP blocked causing PMTU issues**
   - Symptom: downloads hang when large packets, small requests OK.
   - Fix: allow ICMP types for “fragmentation needed” (type 3/code 4) or permit ICMP generally.

7. **Cloud SG / NACL misconfiguration**
   - Symptom: host-level debugging looks fine; other instances or internet cannot reach instance.
   - Fix: update SG to allow port/protocol from required source; NACL requires both directions.

### Advanced Topics — Things That Bite You in Production

- **Asymmetric routing:** packet leaves via path A and reply returns via path B. Strict `rp_filter = 1` can drop that. Use policy routing or relax `rp_filter`.
- **Stateful firewalls and inbound-only rules:** cloud SG is stateful — opening outbound often permits return — but NACLs are stateless — you must author both inbound & outbound rules in NACL.
- **SYN flood / SYN cookies:** kernel can enable SYN cookies to protect against SYN floods. If SYN backlog fills, legitimate connections may be dropped.
- **Rate limiting / connection limits:** firewalls/IPS can rate-limit or block after thresholds.
- **Logging noise:** adding LOG rules without rate limits can flood disk.
- **Inspecting encrypted traffic:** WAF/NGFW may block based on SSL fingerprinting; TLS interception requires CA to be trusted by clients.
- **Firewall rule ordering:** misordered allow/deny rules can produce surprising behavior (first-match wins).
- **Kernel bypass / DPDK:** high-performance firewalls may bypass standard kernel networking.

### Practical Bash Health-Check Script for Firewall Issues

Save as `firewall-health-check.sh`, `chmod +x`, run as root for full tests (tcpdump and conntrack require root). It’s conservative (non-destructive by default) and prints hints.

``` bash
# !/usr/bin/env bash
# firewall-health-check.sh - passive checks to help find firewall problems
# Usage: sudo ./firewall-health-check.sh [DEST_HOST] [DEST_PORT]

DEST_HOST="${1:-example.com}"
DEST_PORT="${2:-443}"
IFACE="${3:-$(ip route get 8.8.8.8 2>/dev/null | awk '{print $5; exit}')}"

echo "Firewall health-check: $(date)"
echo "Target: $DEST_HOST:$DEST_PORT  Interface: ${IFACE:-unknown}"

# 1) Basic info
echo
echo "=== Local listeners ==="
ss -tulnp | sed -n '1,120p'

echo
echo "=== Routes ==="
ip route show

echo
echo "=== Default gateway check ==="
ip route get 1.1.1.1 2>/dev/null || true

# 2) Local firewall status
echo
echo "=== iptables filter (brief) ==="
if command -v iptables >/dev/null 2>&1; then
  sudo iptables -L -n -v --line-numbers | sed -n '1,200p'
else
  echo "iptables not present"
fi

echo
echo "=== nftables ruleset (if any) ==="
if command -v nft >/dev/null 2>&1; then
  sudo nft list ruleset | sed -n '1,200p'
else
  echo "nft not present"
fi

echo
echo "=== ufw status ==="
if command -v ufw >/dev/null 2>&1; then
  sudo ufw status verbose || true
fi

echo
echo "=== firewalld zones (if present) ==="
if command -v firewall-cmd >/dev/null 2>&1; then
  sudo firewall-cmd --list-all-zones || true
fi

# 3) Basic connectivity tests
echo
echo "=== DNS ==="
if command -v dig >/dev/null 2>&1; then
  dig +short $DEST_HOST | sed -n '1,10p'
else
  host $DEST_HOST || true
fi

echo
echo "=== Ping (4 tries) ==="
ping -c 4 -W 2 $DEST_HOST || true

echo
echo "=== TCP connect test with nc ==="
if command -v nc >/dev/null 2>&1; then
  nc -vz -w 5 $DEST_HOST $DEST_PORT || true
else
  echo "netcat (nc) not installed"
fi

echo
echo "=== curl (HTTP/HTTPS) ==="
if command -v curl >/dev/null 2>&1; then
  curl -I --max-time 15 https://$DEST_HOST 2>&1 | sed -n '1,8p' || true
fi

# 4) tcpdump short capture to see if SYN/SYN-ACK/RESET observed
echo
echo "=== tcpdump (5 sec) capturing traffic to $DEST_HOST:$DEST_PORT on $IFACE ==="
if command -v tcpdump >/dev/null 2>&1 && [ -n "$IFACE" ]; then
  DEST_IP=$(getent ahosts $DEST_HOST | awk '{print $1; exit}')
  if [ -n "$DEST_IP" ]; then
    sudo timeout 6 tcpdump -n -i "$IFACE" "host $DEST_IP and port $DEST_PORT" -c 50 -vv 2>/dev/null || true
  else
    echo "Could not resolve DEST_HOST for tcpdump capture"
  fi
else
  echo "tcpdump not installed or interface unknown"
fi

# 5) conntrack overview
echo
echo "=== conntrack counts ==="
if [ -f /proc/sys/net/netfilter/nf_conntrack_count ]; then
  echo "current: $(cat /proc/sys/net/netfilter/nf_conntrack_count) / max: $(cat /proc/sys/net/netfilter/nf_conntrack_max)"
fi

# 6) rp_filter, ip_forward
echo
echo "=== rp_filter settings ==="
for f in /proc/sys/net/ipv4/conf/*/rp_filter; do
  echo "$f: $(cat $f)"
done

echo
echo "=== ip_forward ==="
sysctl net.ipv4.ip_forward

# 7) quick hint output
echo
echo "=== Quick hints ==="
echo " - If tcpdump shows SYN leaving but no SYN-ACK: likely firewall or network drop."
echo " - If nc shows 'Connection refused' and tcpdump shows RST: remote closed or RST from firewall."
echo " - If conntrack count is near max: consider increasing nf_conntrack_max or investigating leaks."
echo
echo "Done."
```

## Quick Rules-of-Thumb for Interpretation

- SYN leaves, no reply → firewall drop somewhere (host/edge/ISP). Use traceroute/tcpdump to locate.
- No SYN leaves → app or local OS issue (proxy misconfig, DNS failure, or app not starting).
- RST returns → remote intentionally refuses (port closed) or middlebox doing RST injection.
- ICMP Unreachable → an intermediate router responded — check that router/firewall.
- Intermittent failures → consider conntrack exhaustion, rate limit, IDS.

### How to safely open a port for debugging and then revert

**iptables temporary open (be careful in prod):**

```bash
# allow SSH from your IP (replace x.x.x.x)
sudo iptables -I INPUT 1 -p tcp -s x.x.x.x --dport 22 -m conntrack --ctstate NEW,ESTABLISHED -j ACCEPT
# remove it when done (line number 1 assumed)
sudo iptables -D INPUT 1
```

**firewalld example**

```bash
sudo firewall-cmd --add-port=8080/tcp --permanent
sudo firewall-cmd --reload
# remove
sudo firewall-cmd --remove-port=8080/tcp --permanent
sudo firewall-cmd --reload
```

**UFW example**

```bash
sudo ufw allow from x.x.x.x to any port 8080 proto tcp
# To delete:
sudo ufw delete allow from x.x.x.x to any port 8080 proto tcp
```

Always document and make config persistent through your distro’s recommended mechanism.

### Checklist to find firewall-caused errors quickly

1. Reproduce problem and capture exact error message and timestamp.
2. On server: `ss -tulnp` → is the service listening?
3. On server: `ip route get <dest>` → route exists?
4. Test DNS: `dig` / `host`.
5. Test TCP connect: `nc -vz dest port` or `curl -v`.
6. Capture packets: `sudo tcpdump -n -i <iface> host <dest>` while reproducing the failure.
7. Check local firewall: `iptables -L -n -v` / `nft list ruleset` / `ufw status` / `firewall-cmd --list-all`.
8. Check logs: `journalctl`, /var/log/syslog`,`dmesg`.
9. Check conntrack: `cat /proc/sys/net/netfilter/nf_conntrack_count`.
10. If on cloud: check Security Groups / NACLs and route tables.
11. If problem persists, test from another host in the same network — isolates host vs network firewall.
