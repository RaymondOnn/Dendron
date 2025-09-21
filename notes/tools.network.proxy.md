---
id: c7klaa72fqozyplvctd1xbs
title: proxy
desc: ''
updated: 1758361186554
created: 1758344619332
---


## Proxies

### What a proxy is (short)

- a proxy is an intermediary that accepts client requests and forwards them to the destination server — often used by organizations for security, access control, caching, logging, and to require authentication before allowing traffic out.

#### Common proxy types

- Forward proxy (client-side) — what your client uses to reach the internet (e.g., corporate HTTP proxy).
- Typical setup:

    ``` rust
    App -> Forward proxy -> Internet
    ```

- Reverse proxy (server-side) — sits in front of backend servers (e.g., Nginx, AWS ALB).
- Transparent proxy — intercepts traffic at network level (clients don’t configure it).
- SOCKS proxy — generic TCP proxy (socks5 supports UDP, auth, etc.).
- HTTP/HTTPS proxy — HTTP proxies understand HTTP; HTTPS commonly uses the CONNECT method (tunnel).

#### Authentication types you’ll encounter

- None (open proxy for certain clients)
- Basic (username:password base64 — insecure if not TLS)
- Digest
- NTLM (Microsoft challenge/response multi-step; common in Windows AD/IE corporate environments)
- Kerberos / SPNEGO (Negotiate) — Integrated Windows auth (GSSAPI/Kerberos)
- Token / OAuth — for modern cloud proxies/APIs

#### How HTTPS works through an HTTP proxy

- Client issues CONNECT host:443 HTTP/1.1 to the proxy.
- If proxy allows, it opens a TCP tunnel to host:443 and the TLS handshake happens directly inside that tunnel.
- In many corporate environments an intercepting proxy performs a TLS MITM: proxy terminates TLS, re-encrypts with its own cert — this requires clients to trust the corporate CA.

#### Headers & identity

- Proxies insert headers like Via, Forwarded, or X-Forwarded-For. Reverse proxies commonly use X-Forwarded-For.

### CNTLM —

- TLDR: CNTLM is a local proxy that performs NTLM authentication to a corporate (parent) proxy on behalf of clients that don’t natively support NTLM. It’s a “translator/bridge”.

#### When to use CNTLM

- Your corporate proxy requires NTLM (challenge/response) and your app/library does not support NTLM.
- You can run a small local daemon (CNTLM) on the host and point the app at it (no NTLM logic required in the app).
- Diagram:

    ``` rust
    App -> http(s) proxy=127.0.0.1:3128 (CNTLM)
    CNTLM -> NTLM-authenticated corporate proxy -> Internet
    ```

- What CNTLM does not do:
  - CNTLM does not implement Kerberos/SPNEGO. If your proxy uses Kerberos-only integrated auth, you need a different solution (e.g., configure apps to use GSSAPI, use a Kerberos-capable client, or configure a proxy that supports “negotiate”).
  - CNTLM doesn’t magically fix network/firewall or DNS issues; it only handles NTLM auth.

#### Exact CNTLM settings you must know (and a sample config)

- Default config file (Linux): /etc/cntlm.conf
- Key settings you’ll see and what they mean:

    ``` bash
    # Authentication & identity
    Username    raymond          # AD username (without domain\)
    Domain      CORP             # NetBIOS domain (or FQDN) - required for NTLM
    #Password   secret           # Plain text password (not recommended)
    PassLM      <LM-hash>        # LM hash (optional)
    PassNT      <NT-hash>        # NT hash  (optional)
    PassNTLMv2  <NTLMv2-hash>    # NTLMv2 hash (preferred to store instead of Password)

    # Parent corporate proxy (can specify multiple)
    Proxy       proxy.corp.example.com:8080
    Proxy       proxy2.corp.example.com:8080  # fallback if first fails

    # Where CNTLM listens for clients
    Listen      127.0.0.1:3128  # recommended: bind to loopback only

    # Hosts/domains to bypass the proxy
    NoProxy     localhost,127.0.0.1,::1,.internal.corp.example

    # Debug/logging (optional)
    Debug       1      # or increase verbosity (check man page)
    #PidFile   /var/run/cntlm.pid   # usually set by package/systemd
    ```

    > Important: do not store plaintext passwords.

    Generate hashes locally:

    ``` bash
    cntlm -H -d YOURDOMAIN -u yourusername
    # Enter password when prompted
    # Output will include PassLM, PassNT and PassNTLMv2 lines
    ```

    Copy only the PassNTLMv2 (and/or PassNT) lines into /etc/cntlm.conf and comment out Password.

#### Notes & best practices

- Use `Listen 127.0.0.1:3128` so only local apps can reach CNTLM.
- You can put multiple `Proxy` lines — cntlm will try them in order.
- Use `NoProxy` to exclude internal hosts (DNS or internal IP ranges).
- Check logs with `journalctl -u cntlm -f` (systemd) or `/var/log/syslog` depending on distro.

### Configure common apps to use CNTLM / proxies

- Environment variables (works for many CLIs & libraries):

    ``` bash
    export HTTP_PROXY="http://127.0.0.1:3128"
    export HTTPS_PROXY="http://127.0.0.1:3128"
    export NO_PROXY="localhost,127.0.0.1,.internal.corp.example,10.0.0.0/8"
    # set both upper- and lower-case to be safe
    export http_proxy=$HTTP_PROXY
    export https_proxy=$HTTPS_PROXY
    export no_proxy=$NO_PROXY
    ```

- Tool-specific examples

  - `curl`:
    - Inline: `curl --proxy http://127.0.0.1:3128 -v https://example.com`
    - Use env vars (preferred).

  - `wget`: supports `http_proxy` env or `/etc/wgetrc`
  - `git`:
    - `git config --global http.proxy http://127.0.0.1:3128`
    - or use env vars
  - `apt` (Debian/Ubuntu):
    - create `/etc/apt/apt.conf.d/95proxies`:

        ``` cpp
        Acquire::http::Proxy "http://127.0.0.1:3128";
        Acquire::https::Proxy "http://127.0.0.1:3128";
        ```

  - `yum`/`dnf`:
  - in `/etc/yum.conf: proxy=http://127.0.0.1:3128`
  - `pip`:
  - `pip --proxy http://127.0.0.1:3128 install <pkg>`
  - or set in pip config
  - `npm`:
    - `npm config set proxy http://127.0.0.1:3128`
    - `npm config set https-proxy http://127.0.0.1:3128`

  - Java apps (JVM args):
    - `-Dhttp.proxyHost=127.0.0.1 -Dhttp.proxyPort=3128 -Dhttps.proxyHost=127.0.0.1 -Dhttps.proxyPort=3128`
  - Docker daemon / containers:

    - daemon: create `/etc/systemd/system/docker.service.d/http-proxy.conf` with:

        ``` ini
        [Service]
        Environment="HTTP_PROXY=http://127.0.0.1:3128" "HTTPS_PROXY=http://127.0.0.1:3128"
        ```

        then `systemctl daemon-reload && systemctl restart docker`.

    - containers: `docker run -e HTTP_PROXY=... -e HTTPS_PROXY=... ...`

  - Systemd services: create override at `/etc/systemd/system/<service>.service.d/proxy.conf`:

    ``` ini
    [Service]
    Environment="HTTP_PROXY=<http://127.0.0.1:3128>" "HTTPS_PROXY=<http://127.0.0.1:3128>"
    ```

    `systemctl daemon-reload` then `systemctl restart <service>`.`

- Note: Some tools (e.g., some language libraries) may ignore env vars or require explicit settings. Check tool docs.

#### How to identify if an error is due to a proxy — mapping errors to causes + commands to run

- Think of the chain:

    ``` rust
    App -> (local proxy/CNTLM) -> Corporate proxy -> Internet (DNS + destination)
    ```

You should test each hop. Below are commands to run and what their outputs typically mean.

##### A — Is CNTLM running & listening?

``` bash
# Process
ps aux | grep -i cntlm

# Listening socket (preferred)
ss -lntp | grep 3128      # or: netstat -tulnp | grep 3128

# systemd status
sudo systemctl status cntlm

# logs
sudo journalctl -u cntlm -n 200
```

- Interpreting
  - No process / not listening → CNTLM not running → `Connection refused` from apps that hit 127.0.0.1:3128.
  - CNTLM running & listening → good, move to next test.

##### B — Can CNTLM reach the corporate proxy?

- First, identify the corporate proxy host: check /etc/cntlm.conf (Proxy line) or ask infra.
- Then:

    ``` bash
    # check connectivity to parent proxy

    nc -vz proxy.corp.example.com 8080    # or `telnet proxy.corp.example.com 8080`

    # or

    curl -v --proxy <http://127.0.0.1:3128> <http://example.com>
    ```

- Interpreting
  - `nc` says “succeeded” → network path exists.
  - `curl` returns `200` → CNTLM authenticated and forwarded successfully.

  - `curl` returns `407 Proxy Authentication Required` → CNTLM’s credentials are wrong, or parent proxy expects a different auth method.

  - `curl` times out / connection timed out → network/firewall to parent proxy blocked; wrong proxy host:port.

##### C — Does the app actually use the proxy you expect?

- Check environment:

    ``` bash
    env | grep -i proxy

    # or in a running service
    sudo systemctl show -p Environment <service-name>

    # For a process PID
    xargs -0 -L1 -a /proc/<PID>/environ | grep -i proxy || true
    ```

- Interpreting
  - If app has no proxy env vars and no app config for proxy, it may attempt direct connect (which will fail in corporate networks that block direct outbound).
  - If systemd service or container overrides env, check those locations.

##### D — Is DNS resolving?

``` bash
dig +short example.com
nslookup example.com
```

- Interpreting
  - DNS fails → Could not resolve host errors. Fix resolvers or ensure DNS works via corporate network
  - In some designs, DNS is only accessible via proxy; if direct DNS fails but curl via proxy succeeds, that’s expected.

##### E — SSL/TLS problems (MITM / corporate CA)

- Run:

    ``` bash
    curl -v --proxy <http://127.0.0.1:3128> <https://www.google.com> 2>&1 | sed -n '1,200p'
    ```

- Look for:
  - SSL certificate problem: unable to get local issuer certificate or certificate verify failed → corporate proxy is intercepting TLS using a custom CA that is not trusted on the host. You must install the corporate root CA into:
    - system trust store (/usr/local/share/ca-certificates/ + sudo update-ca-certificates on Debian/Ubuntu)
    - Java keystore (if a Java app)
    - other app-specific stores (e.g., curl/libssl may use system store).

##### F — Real-time traffic inspection (if needed)

``` bash
# capture traffic to the parent proxy IP and port
sudo tcpdump -n -i any host proxy.corp.example.com and port 8080 -w /tmp/proxy.pcap

# or inspect connections quickly
ss -tupan | grep proxy.corp.example.com
```

- If you see your host connecting to the proxy IP/port when the app runs, the app is configured correctly.
- If no traffic is seen, the app isn’t using the proxy.

##### G — Application-level debugging

- `curl`: `curl -v` and `curl --trace-ascii debug.txt`
- `git`: `GIT_CURL_VERBOSE=1 GIT_TRACE=1 git clone ...`
- `Java`: `-Djavax.net.debug=all` for TLS, or add `-Dhttp.proxyHost=... -Dhttp.proxyPort=...` to ensure JVM uses proxy.
- `Python`: inspect requests proxies or `print os.environ.get('HTTP_PROXY')`. For NTLM directly in Python you can use requests-ntlm, but if not using that, point to CNTLM.

### Common errors, what they mean, and remediation (quick table)

- `407 Proxy Authentication Required`
  - Cause: Parent corporate proxy rejected authentication.
  - Check: CNTLM credentials (PassNTLMv2, Domain, Username), CNTLM logs (journalctl -u cntlm), time sync (Kerberos-like issues).
  - Fix: regenerate PassNTLMv2 with `cntlm -H -u user -d DOMAIN`, update `/etc/cntlm.conf`, restart CNTLM.

- Connection refused to `127.0.0.1:3128`
  - Cause: CNTLM not running or bound to different IP/port.
  - Check: `ss -lntp | grep 3128`, `systemctl status cntlm`
  - Fix: start CNTLM, correct Listen setting.

- `Connection timed out`
  - Cause: No network path to proxy or destination (firewall).
  - Check: `nc -vz parent.proxy 8080`, `traceroute parent.proxy`
  - Fix: open ports, fix routing, contact network team.

- `Could not resolve host`
  - Cause: DNS failure.
  - Check: `dig host`
  - Fix: configure resolvers, check `/etc/resolv.conf`, or use proxy to resolve.

- `certificate verify failed`
  - Cause: TLS interception by corporate proxy — your client doesn’t trust the corporate CA.
  - Fix: install corporate CA into system trust store and app-specific stores (Java, Node, etc.).

- Errors only on server, not on developer laptop
  - Cause: environment variables, system-wide proxy settings, service-level proxy not set for systemd, firewall rules differ, CNTLM not installed.
  - Check: environment & systemd overrides, container environment, `iptables -L -n -v`.

### Dependencies that must be in place for requests to pass through a proxy

For App -> CNTLM -> Corporate Proxy -> Internet to work, the following must be correct:

1. App configuration:
   - App must be told to use `http(s)_proxy` or app-specific proxy settings.
2. Local CNTLM:
   - Installed, configured (Username/Domain/PassNTLMv2), running, listening on the declared Listen address and port.
3. Host networking
   - Host can reach the corporate proxy (route, firewall rules).
4. Corporate proxy
   - Accepts credentials used by CNTLM; permits desired destinations.
5. DNS
   - Host can resolve domain names (either locally or via proxy as designed).
6. Trusted CA chain for TLS
   - If proxy performs TLS MITM, the host must trust proxy’s CA (system / Java / app-level).
7. System/service-level environment
   - systemd, docker, cron jobs, other services must have proxy env or configuration.
8. Time sync
   - If using Kerberos or time-sensitive auth, clocks must be in sync (NTP).
9. Logging & monitoring
   - CNTLM logs and corporate proxy logs (if accessible) to verify or audit.

### Useful commands & examples (paste-and-run style) — with interpretations

(Use `sudo` if required)

- Check CNTLM process & socket

    ``` bash
    ps aux | grep -i cntlm
    ss -lntp | grep -E '(:3128|cntlm)'
    sudo systemctl status cntlm
    sudo journalctl -u cntlm -n 200
    ```

- Test simple HTTP through CNTLM

    ``` bash
    curl -v --proxy <http://127.0.0.1:3128> <http://example.com>
    # Look for "HTTP/1.1 200 OK" or similar
    ```

- Test HTTPS through CNTLM (shows TLS behavior)

    ``` bash
    curl -v --proxy <http://127.0.0.1:3128> <https://www.google.com>
    # Look for TLS handshake and certificate verification messages
    ```

- If you suspect CNTLM auth issue

    ``` bash
    # Re-generate hashes
    cntlm -H -u youruser -d YOURDOMAIN

    # Edit /etc/cntlm.conf -> put PassNTLMv2 from output
    sudo systemctl restart cntlm
    sudo journalctl -u cntlm -f
    ```

- Check reachability to parent proxy

    ``` bash
    nc -vz proxy.corp.example.com 8080
    # or
    telnet proxy.corp.example.com 8080
    ```

- Inspect TLS cert seen by curl (through proxy)

    ``` bash
    curl -v --proxy <http://127.0.0.1:3128> <https://example.com> 2>&1 | sed -n '1,200p'
    # Look for "subject" and issuer lines to see if a corporate CA is being used
    ```

- Check DNS from server

    ``` bash
    dig +short example.com
    nslookup example.com
    ```

- Capture network to parent proxy for a short time

    ``` bash
    sudo tcpdump -n -i any host proxy.corp.example.com and port 8080 -c 200
    # Open the pcap in Wireshark to check headers (look for Proxy-Authenticate, NTLM tokens)
    ```

- Find which environment/proxy a running process is using

    ``` bash
    # list environment for a PID (some OSes allow)
    sudo xargs -0 -L1 -a /proc/<PID>/environ | grep -i proxy || true
    ```

- Debug a Java TLS problem

    ``` bash
    # Add these for more TLS logging (java app)
    java -Djavax.net.debug=ssl,handshake -jar yourapp.jar
    ```

- See if your host has the corporate CA
  
    ``` bash
    # Debian/Ubuntu example
    sudo update-ca-certificates --fresh

    # Check /etc/ssl/certs/ for your corp CA
    ls -l /etc/ssl/certs | grep -i corp
    ```

### Troubleshooting workflow (step-by-step — do this order)

Confirm symptom & error text (logs, stack trace). Copy exact messages.

Check app config — does app have HTTP_PROXY/HTTPS_PROXY set or app-level proxy set?

Check CNTLM is running (ss -lntp | grep 3128, systemctl status cntlm). If not, start it.

Try curl via CNTLM (curl --proxy <http://127.0.0.1:3128> -v <http://example.com>). If this fails, fix CNTLM first.

If curl via CNTLM works but app fails, check app-specific config (systemd env, container env, language-specific proxy config).

If CNTLM cannot talk to parent proxy, nc -vz parent.proxy port. Check firewall/routing.

If TLS issues appear, check for corporate CA and add it to system/Java stores.

If DNS fails, run dig and fix /etc/resolv.conf or use proxy for name resolution if required.

If all else fails, capture traffic with tcpdump / read CNTLM and parent proxy logs.

10) Ready-to-run Bash health-check script

- Save this as `proxy-health-check.sh`, make executable (chmod +x proxy-health-check.sh) and run it.
- It’s conservative (won’t change system state), prints results and hints.

``` bash
# !/usr/bin/env bash

set -u

# proxy-health-check.sh - lightweight checks for CNTLM + proxy reachability

CNTLM_CONF="/etc/cntlm.conf"
LOCAL_CNTLM="127.0.0.1:3128"
TEST_URL="<http://example.com>"
TEST_URL_HTTPS="<https://www.google.com>"

echo
echo "=== Proxy health-check $(date) ==="
echo

# Helper

cmd_exists(){ command -v "$1" >/dev/null 2>&1; }

# 1) Check CNTLM process & listening

echo "# Checking cntlm process and socket..."
if pgrep -x cntlm >/dev/null 2>&1; then
echo "OK: cntlm process found (pid: $(pgrep -x cntlm | head -n1))"
else
echo "WARN: no cntlm process found"
fi

if ss -lntp 2>/dev/null | grep -q "127.0.0.1:3128"; then
echo "OK: cntlm listening on 127.0.0.1:3128"
else
echo "WARN: no listener on 127.0.0.1:3128 (check Listen in /etc/cntlm.conf)"
fi

# 2) Show cntlm config Proxy lines (if present)

if [ -f "$CNTLM_CONF" ]; then
echo
echo "# /etc/cntlm.conf (Proxy lines):"
grep -i '^Proxy' "$CNTLM_CONF" || echo "(no Proxy lines found in $CNTLM_CONF)"
else
echo
echo "INFO: $CNTLM_CONF not found"
fi

# 3) curl test via local CNTLM

echo
echo "# Testing HTTP via CNTLM ($LOCAL_CNTLM) -> $TEST_URL"
if cmd_exists curl; then
CURL_OUTPUT=$(curl -sS --max-time 15 --proxy http://$LOCAL_CNTLM -o /dev/null -w "%{http_code}" $TEST_URL 2>&1) || CURL_OUTPUT="$?"
echo "curl result: $CURL_OUTPUT"
if [ "$CURL_OUTPUT" = "200" ]; then
    echo "OK: HTTP via CNTLM succeeded (200)"
else
    echo "WARN: HTTP via CNTLM returned: $CURL_OUTPUT"
    echo "  Re-run: curl -v --proxy http://$LOCAL_CNTLM $TEST_URL"
fi
else
echo "SKIP: curl not installed"
fi

# 4) curl HTTPS via CNTLM (check certs)

echo
echo "# Testing HTTPS via CNTLM -> $TEST_URL_HTTPS"
if cmd_exists curl; then
curl -I --proxy http://$LOCAL_CNTLM --max-time 20 $TEST_URL_HTTPS 2>&1 | sed -n '1,8p'
echo "(See above — if you see certificate verify failed, add corporate CA to trust store)"
fi

# 5) Parse /etc/cntlm.conf to find parent proxy and test conn to it

if [ -f "$CNTLM_CONF" ]; then
PPROXY=$(grep -i '^Proxy' $CNTLM_CONF | head -n1 | awk '{print $2}')
if [ -n "$PPROXY" ]; then
    echo
    echo "# Parent proxy from $CNTLM_CONF -> $PPROXY (testing TCP)"
    PROXY_HOST=$(echo "$PPROXY" | cut -d: -f1)
    PROXY_PORT=$(echo "$PPROXY" | cut -d: -f2)
    if cmd_exists nc; then
    nc -vz -w 5 "$PROXY_HOST" "$PROXY_PORT" || echo "WARN: cannot connect to parent proxy $PROXY_HOST:$PROXY_PORT"
    else
    echo "INFO: nc not installed; try: nc -vz $PROXY_HOST $PROXY_PORT"
    fi
else
    echo "INFO: no Proxy entry parsed from $CNTLM_CONF"
fi
fi

# 6) DNS check

echo
echo "# DNS: resolving example.com"
if cmd_exists dig; then
dig +short example.com | sed -n '1,5p' || echo "WARN: dig couldn't resolve example.com"
else
if cmd_exists nslookup; then
    nslookup example.com | sed -n '1,8p'
else
    echo "SKIP: dig/nslookup not available"
fi
fi

# 7) Check environment variables commonly used

echo
echo "# Proxy environment variables (in current shell):"
env | grep -i proxy || echo "(none set in current shell)"

# 8) Show recent cntlm logs (if systemd)

if cmd_exists journalctl; then
echo
echo "# Last 30 cntlm journal lines:"
sudo journalctl -u cntlm -n 30 --no-pager || echo "(no journal lines or cntlm unit)"
fi

echo
echo "=== Done ==="
echo "Interpretation tips:"
echo "- 200 from curl via CNTLM -> local CNTLM+parent proxy functional for HTTP."
echo "- 407 from curl -> auth problem (CNTLM credentials / domain / PassNTLMv2)."
echo "- connection refused to 127.0.0.1:3128 -> cntlm not running or bound to different address."
echo "- timeouts to parent proxy -> network/firewall issue to corporate proxy."
echo
```

### Extra tips, gotchas and best practices

- Never store plaintext passwords in `/etc/cntlm.conf` — always use `cntlm -H` and store `PassNTLMv2`.
- Install corporate CA into system trust store and into Java keystore for Java apps (or configure app to trust it).
- Set both uppercase & lowercase proxy env vars (`HTTP_PROXY` and `http_proxy`) — some tools check only one.
- NoProxy patterns vary by tool — some support domain suffix (e.g., `.corp`), others need explicit hostnames/IP ranges. Test with `curl --noproxy`.
- Systemd services don’t inherit your shell environment, so add proxy env to systemd unit overrides.
- Time sync matters for Kerberos-type auth; ensure `ntp/chrony` is running.
- If parent proxy uses Kerberos/SPNEGO, CNTLM won’t work — use a Kerberos-capable client or other proxy tooling.

### Final checklist (short)

- CNTLM installed? (`systemctl status cntlm`)
- CNTLM listening on expected Listen address? (`ss -lntp`)
- `PassNTLMv2` set (not plaintext password)? (`/etc/cntlm.conf`)
- Parent proxy reachable (`nc -vz proxy host port`)
- App configured to use `http(s)_proxy` or app-specific proxy
- Corporate CA in trust stores if TLS is intercepted
- systemd/docker services have proxy env if they run the app
- Logs examined: `journalctl -u cntlm` and app logs for `407/timeouts`
