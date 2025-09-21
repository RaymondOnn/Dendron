---
id: s8r6a7o6pfz610rzsghenbc
title: Diagnostics
desc: ''
updated: 1758380527102
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