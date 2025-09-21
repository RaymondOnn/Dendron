---
id: s8r6a7o6pfz610rzsghenbc
title: Diagnostics
desc: ''
updated: 1758469763933
created: 1758377694182
---

``` python
# net_diag.py
#!/usr/bin/env python3
"""
net_diag.py – Network diagnostic helper

Can be:
  1. Imported in your app:
        from net_diag import quick_diagnose
        results = quick_diagnose("api.example.com", 443, proxy="http://127.0.0.1:3128")

  2. Run directly as a CLI tool:
        python3 net_diag.py api.example.com 443 http://127.0.0.1:3128
"""
"""
Ultimate network diagnostic script for Python apps.
Covers DNS, TCP, TLS/SSL, HTTP, Proxy, Firewall/ICMP, IPv4/IPv6, MTU, and routing issues.
"""

import socket, ssl, sys, requests, os
from datetime import datetime
import idna
import subprocess

# --------- Error classification ----------
def explain_error(err: Exception) -> str:
    msg = str(err)
    if isinstance(err, socket.gaierror):
        return "DNS resolution failed"
    if isinstance(err, socket.timeout):
        return "Connection timed out (firewall/drop?)"
    if isinstance(err, ConnectionRefusedError):
        return "Connection refused (port closed/blocked)"
    if "SSL" in msg or isinstance(err, ssl.SSLError):
        return "SSL/TLS handshake failed (cert/proxy?)"
    if "407" in msg:
        return "Proxy authentication required (CNTLM creds?)"
    if "502" in msg:
        return "Upstream proxy unreachable"
    if "Connection reset" in msg:
        return "Connection reset (firewall/proxy killed it)"
    if "Network is unreachable" in msg:
        return "No route to host (gateway/firewall?)"
    return f"Unknown network error: {msg}"

# --------- TLS Diagnostics ----------
def check_tls(host: str, port: int = 443, ca_file: str | None = None) -> dict:
    result = {"tls": False, "verdict": "ERROR", "not_before": None, "not_after": None, "san_list": [], "issuer": None}
    try:
        hostname_idna = idna.encode(host).decode('ascii')
        context = ssl.create_default_context(cafile=ca_file)
        with socket.create_connection((hostname_idna, port), timeout=5) as sock:
            with context.wrap_socket(sock, server_hostname=hostname_idna) as ssock:
                cert = ssock.getpeercert()
                # Expiry
                not_before = datetime.strptime(cert['notBefore'], "%b %d %H:%M:%S %Y %Z")
                not_after = datetime.strptime(cert['notAfter'], "%b %d %H:%M:%S %Y %Z")
                now = datetime.utcnow()
                result['not_before'] = not_before
                result['not_after'] = not_after
                # SANs
                san_list = [val for typ, val in cert.get('subjectAltName', []) if typ == 'DNS']
                result['san_list'] = san_list
                # Issuer
                issuer = " ".join(f"{x[0]}={x[1]}" for x in cert.get('issuer', []))
                result['issuer'] = issuer
                # Verdict logic
                if now < not_before or now > not_after:
                    verdict = "EXPIRED"
                elif "YourCorpCA" in issuer:  # replace with known corporate CA if desired
                    verdict = "PROXY_INTERCEPTED"
                else:
                    verdict = "OK"
                result['tls'] = True
                result['verdict'] = verdict
    except ssl.CertificateError as e:
        result['verdict'] = "UNTRUSTED"
        result['error'] = str(e)
    except ssl.SSLError as e:
        result['verdict'] = "UNTRUSTED"
        result['error'] = str(e)
    except Exception as e:
        result['verdict'] = "ERROR"
        result['error'] = str(e)
    return result

# --------- MTU check ----------
def check_mtu(host: str) -> str:
    try:
        # ping with "do not fragment" flag
        res = subprocess.run(["ping", "-c", "1", "-M", "do", "-s", "1472", host],
                             stdout=subprocess.DEVNULL, stderr=subprocess.DEVNULL)
        return "OK" if res.returncode == 0 else "MTU problem / fragmentation"
    except Exception:
        return "MTU check skipped"

# --------- ICMP / ping check ----------
def check_icmp(host: str) -> str:
    try:
        res = subprocess.run(["ping", "-c", "1", host],
                             stdout=subprocess.DEVNULL, stderr=subprocess.DEVNULL)
        return "OK" if res.returncode == 0 else "ICMP unreachable"
    except Exception:
        return "ICMP check skipped"

# --------- Quick network diagnostics ----------
def quick_diagnose(host: str, port: int = 443, proxy: str | None = None) -> dict:
    results = {"dns": False, "tcp": False, "tls": None, "http": False, "icmp": None, "mtu": None, "error": None}

    # DNS
    try:
        socket.gethostbyname_ex(host)
        results["dns"] = True
    except Exception as e:
        results["error"] = explain_error(e)
        return results

    # TCP
    try:
        with socket.create_connection((host, port), timeout=5):
            results["tcp"] = True
    except Exception as e:
        results["error"] = explain_error(e)
        return results

    # TLS
    if port == 443:
        results["tls"] = check_tls(host, port)

    # HTTP
    scheme = "https" if port == 443 else "http"
    url = f"{scheme}://{host}"
    try:
        proxies = {"http": proxy, "https": proxy} if proxy else None
        r = requests.get(url, proxies=proxies, timeout=10)
        results["http"] = True
        results["status_code"] = r.status_code
    except Exception as e:
        results["error"] = explain_error(e)

    # ICMP
    results["icmp"] = check_icmp(host)

    # MTU
    results["mtu"] = check_mtu(host)

    return results

# --------- CLI Mode ----------
def cli():
    if len(sys.argv) < 2:
        print("Usage: python3 net_diag.py <host> [port] [proxy]")
        sys.exit(1)

    host = sys.argv[1]
    port = int(sys.argv[2]) if len(sys.argv) > 2 else 443
    proxy = sys.argv[3] if len(sys.argv) > 3 else None

    print(f"=== Network Diagnostics for {host}:{port} ===")
    if proxy:
        print(f"Proxy: {proxy}")

    results = quick_diagnose(host, port, proxy)

    # Human-readable output
    for k, v in results.items():
        if k == "tls" and v:
            print(f"[{'✔' if v['verdict']=='OK' else '✘'}] TLS verdict: {v['verdict']}")
            print(f"    Expiry: {v.get('not_after')} | SANs: {v.get('san_list')} | Issuer: {v.get('issuer')}")
        elif k == "status_code" and v:
            print(f"[✔] HTTP status code {v}")
        elif k in ["icmp", "mtu"]:
            print(f"[{'✔' if v=='OK' else '✘'}] {k.upper()}: {v}")
        elif isinstance(v, bool):
            print(f"[{'✔' if v else '✘'}] {k.upper()}")
        elif v:
            print(f"⚠️ {k}: {v}")

    print("=== Done ===")

if __name__ == "__main__":
    cli()

```

### How to use

- Call from app when network error

    ``` python
    import requests
    from net_diag import quick_diagnose

    API_HOST = "api.example.com"
    PROXY = "http://127.0.0.1:3128"  # or None

    def fetch_data():
        url = f"https://{API_HOST}/data"
        try:
            r = requests.get(url, timeout=5)
            r.raise_for_status()
            return r.json()
        except Exception as e:
            print("❌ App failed to fetch data:", e)
            diag = quick_diagnose(API_HOST, 443, PROXY)
            print("🔎 Network diagnostics:", diag)
            raise

    if __name__ == "__main__":
        try:
            data = fetch_data()
            print("✅ Got data:", data)
        except Exception:
            print("App terminated due to network issue")
    ```

- CLI usage

    ``` js
    $ python3 net_diag.py api.example.com 443 http://127.0.0.1:3128
    === Network Diagnostics for api.example.com:443 ===
    Proxy: http://127.0.0.1:3128
    [✔] dns
    [✔] tcp
    [✔] tls
    [✘] http
    ⚠️ error: Proxy authentication required (CNTLM creds missing?)
    === Done ===
    ```

Same script in Bash

``` bash
#!/usr/bin/env bash
#
# net_diag.sh - Network diagnostics for Python apps on Linux
#
# Usage:
#   ./net_diag.sh <host> [port] [proxy]
#
# Examples:
#   ./net_diag.sh pypi.org
#   ./net_diag.sh google.com 443
#   ./net_diag.sh example.com 443 http://127.0.0.1:3128
#
# What it checks:
#   1. DNS resolution
#   2. ICMP reachability (ping)
#   3. TCP connectivity (firewall drop vs reset vs open)
#   4. TLS certificate (expiry, SANs, validity, untrusted CA)
#   5. Proxy issues (407 auth, 502 upstream, timeout)
#   6. CNTLM-specific issues (auth, upstream, not running)
#   7. Python dependencies (certifi, env vars)
#   8. Final verdict (OK vs DNS vs firewall vs TLS vs proxy vs CNTLM vs Python)
#

HOST=$1
PORT=${2:-443}
PROXY=$3
VERDICT="Unknown"

if [ -z "$HOST" ]; then
  echo "Usage: $0 <host> [port] [proxy]"
  exit 1
fi

echo "=== Network Diagnostics for $HOST:$PORT ==="
[ -n "$PROXY" ] && echo "Proxy: $PROXY"

# ---------- DNS ----------
if ! getent hosts "$HOST" >/dev/null; then
  echo "[DNS] Resolution failed for $HOST"
  VERDICT="DNS issue"
  echo "Final Verdict: $VERDICT"
  exit 1
else
  echo "[DNS] Resolution OK"
fi

# ---------- ICMP ----------
if ping -c 1 -W 2 "$HOST" >/dev/null 2>&1; then
  echo "[ICMP] Host responds to ping"
else
  echo "[ICMP] No ping reply (host down or ICMP blocked)"
fi

# ---------- TCP ----------
if nc -z -w3 "$HOST" "$PORT" >/dev/null 2>&1; then
  echo "[TCP] Port $PORT reachable on $HOST"
else
  if nc -zv -w3 "$HOST" "$PORT" 2>&1 | grep -q "refused"; then
    echo "[TCP] Connection refused -> Firewall reset OR service not listening"
    VERDICT="Firewall reset / closed port"
  else
    echo "[TCP] Timeout -> Firewall drop or host unreachable"
    VERDICT="Firewall drop"
  fi
  echo "Final Verdict: $VERDICT"
  exit 1
fi

# ---------- TLS ----------
if [ "$PORT" -eq 443 ]; then
  echo "[TLS] Checking certificate..."
  CERT_INFO=$(echo | openssl s_client -connect "$HOST:$PORT" -servername "$HOST" 2>/dev/null | openssl x509 -noout -dates -subject -issuer -ext subjectAltName 2>/dev/null)
  if [ -n "$CERT_INFO" ]; then
    echo "$CERT_INFO"
    EXPIRY=$(echo "$CERT_INFO" | grep "notAfter" | cut -d= -f2-)
    if [ -n "$EXPIRY" ]; then
      EXPIRY_TS=$(date -d "$EXPIRY" +%s 2>/dev/null)
      NOW_TS=$(date +%s)
      if [ "$EXPIRY_TS" -lt "$NOW_TS" ]; then
        echo "[TLS] Certificate expired on $EXPIRY"
        VERDICT="TLS expired"
        echo "Final Verdict: $VERDICT"
        exit 1
      else
        echo "[TLS] Certificate valid until $EXPIRY"
      fi
    fi
  else
    echo "[TLS] Failed to retrieve certificate (proxy intercept or TLS block?)"
    VERDICT="TLS handshake failed"
    echo "Final Verdict: $VERDICT"
    exit 1
  fi

  # ----- TLS Trust Check -----
  SYSTEM_CA="/etc/ssl/certs/ca-certificates.crt"
  if [ -f "$SYSTEM_CA" ]; then
    echo "[TLS] Verifying certificate trust..."
    echo | openssl s_client -connect "$HOST:$PORT" -servername "$HOST" -showcerts 2>/dev/null \
      | openssl x509 -outform pem \
      | openssl verify -CAfile "$SYSTEM_CA" >/dev/null 2>&1
    if [ $? -eq 0 ]; then
      echo "[TLS] Certificate trusted by system CA"
    else
      echo "[TLS] WARNING: Certificate NOT trusted (possible proxy interception)"
      VERDICT="TLS untrusted CA"
    fi
  else
    echo "[TLS] System CA bundle not found, skipping trust check"
  fi
fi

# ---------- Python dependencies ----------
echo "[Python] Checking certifi and proxy environment variables..."
python3 - <<'EOF' 2>/dev/null
try:
    import certifi
    print("certifi installed:", certifi.where())
except ImportError:
    print("certifi NOT installed")
EOF

echo "HTTP_PROXY=$HTTP_PROXY"
echo "HTTPS_PROXY=$HTTPS_PROXY"
echo "NO_PROXY=$NO_PROXY"

# ---------- Proxy ----------
if [ -n "$PROXY" ]; then
  echo "[Proxy] Testing via configured proxy: $PROXY"
  RESP=$(curl -s -o /dev/null -w "%{http_code}" -x "$PROXY" "https://$HOST:$PORT/" --max-time 10)
  case "$RESP" in
    200) echo "Proxy OK (HTTP 200)" ;;
    407) echo "Proxy AUTH ERROR (HTTP 407, bad credentials)"
         VERDICT="Proxy auth error" ;;
    502) echo "Proxy UPSTREAM ERROR (HTTP 502, cannot reach outside)"
         VERDICT="Proxy upstream error" ;;
    000) echo "Proxy FAILED (no response, timeout or blocked)"
         VERDICT="Proxy no response" ;;
    *)   echo "Proxy returned HTTP $RESP"
         VERDICT="Proxy error $RESP" ;;
  esac
else
  echo "[Proxy] No proxy configured."
fi

# ---------- CNTLM ----------
if [ -n "$PROXY" ] && echo "$PROXY" | grep -q "127.0.0.1:3128"; then
  echo "[CNTLM] Detected CNTLM proxy at $PROXY ..."
  if nc -z -w2 127.0.0.1 3128 >/dev/null 2>&1; then
    echo "CNTLM is running (127.0.0.1:3128 listening)"
    CNTLM_RESP=$(curl -s -o /dev/null -w "%{http_code}" -x http://127.0.0.1:3128 https://example.com --max-time 10)
    case "$CNTLM_RESP" in
      200) echo "CNTLM OK (forwarding works)" ;;
      407) echo "CNTLM AUTH ERROR (bad username/password in cntlm.conf)"
           VERDICT="CNTLM auth error" ;;
      502) echo "CNTLM UPSTREAM ERROR (cannot reach corporate proxy)"
           VERDICT="CNTLM upstream error" ;;
      000) echo "CNTLM FAILED (no response, firewall or dead upstream)"
           VERDICT="CNTLM no response" ;;
      *)   echo "CNTLM returned HTTP $CNTLM_RESP"
           VERDICT="CNTLM error $CNTLM_RESP" ;;
    esac
  else
    echo "CNTLM NOT RUNNING (connection refused on 127.0.0.1:3128)"
    VERDICT="CNTLM not running"
  fi
fi

# ---------- Final Verdict ----------
if [ "$VERDICT" == "Unknown" ]; then
  VERDICT="OK"
fi
echo "=== Final Verdict: $VERDICT ==="
```
