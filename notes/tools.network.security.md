---
id: 13paj5aj0fh6cxh7wcn8cc2
title: Security
desc: ''
updated: 1758379506215
created: 1758378751853
---


## SSL/TLS Deep Dive for Python Apps on Servers

This guide covers SSL/TLS in detail, its purpose, version checks, dependencies, how to identify SSL/TLS-related errors in Python apps, and diagnostic commands usable without root access.

### Purpose of SSL/TLS

SSL (Secure Sockets Layer) and TLS (Transport Layer Security) are cryptographic protocols that:

- **Encrypt traffic** between client and server.
- **Authenticate the server** to the client (and optionally the client to the server using mTLS).
- **Ensure data integrity** so transmitted data is not tampered with.
- **Enable secure HTTP (HTTPS)** for web services, APIs, and Python apps.

### TLS Versions

| Version         | Status                |
|-----------------|----------------------|
| SSL 2.0 / 3.0   | Deprecated / insecure|
| TLS 1.0         | Deprecated           |
| TLS 1.1         | Deprecated           |
| TLS 1.2         | Secure, widely supported |
| TLS 1.3         | Secure, modern, faster handshake |

**Check TLS version supported by server:**

```bash
openssl s_client -connect api.example.com:443 -tls1_3 -servername api.example.com
openssl s_client -connect api.example.com:443 -tls1_2 -servername api.example.com
```

**Check TLS version used by Python client:**

```python
import ssl
print("Python TLS support:", ssl.OPENSSL_VERSION)
```

### Dependencies for TLS Connectivity

To successfully make TLS requests from a server:

- **DNS Resolution:** Hostname must resolve.
- **TCP Connectivity:** Client must reach server:port (443 for HTTPS).
- **SNI Support:** Server Name Indication required by most modern servers.
- **OpenSSL Library:** Python relies on OpenSSL for TLS.
- **Trusted CA Certificates:** Required to verify server certificate.
- **System Time Accuracy:** Certificate validity depends on correct clock.
- **Optional Client Certificates (mTLS):** If server requires client authentication.
- **Proxy Configuration:** Ensure HTTPS traffic is allowed if a proxy exists.
- **OCSP/CRL Checks:** Some servers require online certificate validation.

---

### Common SSL/TLS Errors and Their Causes

| Symptom / Exception                       | Likely Cause                                               |
|-------------------------------------------|------------------------------------------------------------|
| ssl.SSLError: CERTIFICATE_VERIFY_FAILED   | Server certificate untrusted, missing intermediate, self-signed, or expired. |
| ssl.SSLError: WRONG_VERSION_NUMBER        | Connected to non-TLS service or proxy returned plain HTTP. |
| ssl.SSLError: TLSV1_ALERT_PROTOCOL_VERSION| Client TLS version too old.                                |
| certificate has expired / not yet valid   | System clock skew or invalid cert dates.                   |
| hostname mismatch                        | Certificate SAN/CN does not match requested hostname.      |
| requests.exceptions.SSLError              | Python requests propagated an OpenSSL error.               |
| 407 Proxy Authentication Required         | TLS blocked by proxy requiring authentication.             |

---

### Diagnostic Commands (No Root Required)

#### Check Python/OpenSSL versions

```bash
python3 -c "import ssl; print('Python', __import__('sys').version_info[:3]); print('OpenSSL:', ssl.OPENSSL_VERSION)"
```

#### Inspect server certificate

```bash
openssl s_client -connect api.example.com:443 -servername api.example.com -showcerts < /dev/null
```

#### View certificate details and expiry

```bash
openssl s_client -connect api.example.com:443 -servername api.example.com < /dev/null | openssl x509 -noout -text
openssl s_client -connect api.example.com:443 -servername api.example.com < /dev/null | openssl x509 -noout -dates
```

#### Verify certificate using CA bundle

```bash
openssl s_client -connect api.example.com:443 -servername api.example.com -CAfile /etc/ssl/certs/ca-certificates.crt < /dev/null
openssl s_client -connect api.example.com:443 -servername api.example.com -CAfile ~/my_bundle.pem < /dev/null
```

#### Test TLS versions

```bash
openssl s_client -connect api.example.com:443 -tls1_3 -servername api.example.com
openssl s_client -connect api.example.com:443 -tls1_2 -servername api.example.com
```

#### Test specific cipher

```bash
openssl s_client -connect api.example.com:443 -cipher 'ECDHE-RSA-AES128-GCM-SHA256' -servername api.example.com
```

#### Test with curl

```bash
curl -vI https://api.example.com/
curl -vI https://api.example.com/ --cacert ~/my_bundle.pem
curl -vI https://api.example.com/ --insecure  # debug only
```

#### Python requests debug

```python
import logging, requests
logging.basicConfig()
logging.getLogger().setLevel(logging.DEBUG)
logging.getLogger("urllib3").setLevel(logging.DEBUG)

try:
    r = requests.get("https://api.example.com", timeout=5)
    print(r.status_code)
except Exception as e:
    import traceback
    traceback.print_exc()
```

#### Inspect SNI and OCSP

```bash
openssl s_client -connect api.example.com:443 -servername api.example.com -status < /dev/null
openssl s_client -connect api.example.com:443 < /dev/null   # test without SNI
```

#### Client certificate (mTLS) testing

```bash
openssl s_client -connect api.example.com:443 -cert client.crt -key client.key -CAfile ca.pem -servername api.example.com
curl -v --cert client.pem --key client.key --cacert ca.pem https://api.example.com/
```

Python:

```python
requests.get("https://api.example.com", cert=('/home/you/client.pem','/home/you/client.key'), verify='/home/you/ca.pem')
```

### Fixing SSL/TLS Issues Without Root

**Untrusted corporate CA or self-signed cert**

```bash
cp $(python3 -c "import certifi; print(certifi.where())") ~/my_bundle.pem
cat ~/corp_proxy_ca.crt >> ~/my_bundle.pem
export REQUESTS_CA_BUNDLE=~/my_bundle.pem
export SSL_CERT_FILE=~/my_bundle.pem
```

**TLS version/cipher mismatch**

- Upgrade Python/OpenSSL (use virtualenv or container if root unavailable).

**SNI mismatch**

- Ensure server_hostname is set in SSLContext (requests does automatically).

**Clock skew**

```bash
date  # verify system time
```

**Proxy requiring authentication**

```bash
export HTTP_PROXY=http://user:pass@proxy:port
export HTTPS_PROXY=http://user:pass@proxy:port
```

## Troubleshooting Recipes

### Certificate verification failure

```bash
openssl s_client -connect api.example.com:443 -servername api.example.com -showcerts < /dev/null
openssl s_client -connect api.example.com:443 -servername api.example.com -CAfile ~/my_bundle.pem < /dev/null
```

### Wrong version number

```bash
openssl s_client -connect api.example.com:443 -servername api.example.com < /dev/null
curl -v http://api.example.com:443  # Check for plaintext response
```

### Missing intermediate certificate

- Check Verify return code in `openssl s_client -showcerts`. Include missing intermediate in your bundle if needed.

### SNI mismatch

```bash
openssl s_client -connect api.example.com:443 -showcerts < /dev/null
openssl s_client -connect api.example.com:443 -servername api.example.com -showcerts < /dev/null
```


### Python-Specific Tips

- Enable urllib3 debug logs for TLS handshake details.
- Use `verify=path/to/bundle` to specify CA trust.
- Avoid `verify=False` in production; only for debugging.
- Check `ssl.OPENSSL_VERSION` for client-side TLS support.
- Environment variables `REQUESTS_CA_BUNDLE` and `SSL_CERT_FILE` override trust bundle.


### Summary Checklist

- Confirm DNS resolution and TCP connectivity.
- Inspect server certificate chain (leaf + intermediate + root).
- Verify certificate trust with system CA or custom bundle.
- Check TLS version and supported cipher suites.
- Ensure hostname matches SAN/CN.
- Test mTLS if required.
- Enable Python requests debug logging.
- Handle proxy and MITM certificates.
- Verify system time.
- Use environment variables to override CA bundle without root.

---

### Recommended Commands

```bash
# Python/OpenSSL version
python3 -c "import ssl; print(ssl.OPENSSL_VERSION)"

# Inspect server certificate
openssl s_client -connect api.example.com:443 -servername api.example.com -showcerts < /dev/null

# Certificate expiry
openssl s_client -connect api.example.com:443 -servername api.example.com < /dev/null | openssl x509 -noout -dates

# Test certificate trust
openssl s_client -connect api.example.com:443 -servername api.example.com -CAfile ~/my_bundle.pem < /dev/null

# Curl test
curl -vI https://api.example.com/
curl -vI https://api.example.com/ --cacert ~/my_bundle.pem

# Python requests debug
python3 - <<'PY'
import logging, requests
logging.basicConfig()
logging.getLogger().setLevel(logging.DEBUG)
logging.getLogger("urllib3").setLevel(logging.DEBUG)
try:
    r = requests.get("https://api.example.com", timeout=5)
    print(r.status_code)
except Exception as e:
    import traceback
    traceback.print_exc()
PY
```
