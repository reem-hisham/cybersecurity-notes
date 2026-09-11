## Network Tools — Quick Note

| Tool | Use it for |
|---|---|
| `ss` | See **my machine's** ports & connections |
| `netstat` | Same idea as `ss`, but **older** |
| `nc` | **Create/test** TCP or UDP connections |
| `nmap` | **Scan another host** for open ports/services |
| `Wireshark` | See/analyze the **actual packets** |

### Remember

```text
nmap → DISCOVER
nc → CONNECT
ss → OBSERVE
netstat → OBSERVE (old)
Wireshark → ANALYZE PACKETS
````

### Useful commands

```bash
ss -tuln              # listening TCP/UDP ports
ss -tn                # TCP connections

nc -lvnp 8080         # TCP server
nc 127.0.0.1 8080     # TCP client

nmap 192.168.1.20     # scan host
nmap -sV 192.168.1.20 # detect services
```


# IP & Routing Commands

| Command | What it tells me | Why I care |
|---|---|---|
| `ip addr` | My **IP addresses + interfaces** | Know where my machine is on the network |
| `ip route` | My **routing table** | Know where packets will go |
| `ip neigh` | Nearby devices + their **MAC ↔ IP** mappings | See devices my machine has discovered |
| `traceroute` | The **routers/hops** packets pass through | Find where traffic goes or where it stops |

### Quick Mental Model

```text
ip addr      → WHERE AM I?
ip route     → WHERE WILL I SEND IT?
ip neigh     → WHO IS NEAR ME?
traceroute   → WHAT PATH DOES IT TAKE?
````

### Useful examples

```bash
ip addr
ip route
ip neigh
traceroute 8.8.8.8
```

### Don't forget

```text
Private IP → identifies me inside my local network
Gateway    → router I send traffic to
Route      → tells the OS where to send packets
NAT        → router translates private ↔ public IP
```

# Subnetting

## Core Idea

CIDR tells me how large the subnet is:

```text
/24 → 256 addresses → 254 usable hosts
/26 → 64 addresses  → 62 usable hosts
/20 → 4096 addresses → 4094 usable hosts
````

### What I need to find

```text
Network Address
Broadcast Address
Usable IP Range
Number of Usable Hosts
```

### Examples

```text
192.168.10.0/24

Network:    192.168.10.0
Broadcast:  192.168.10.255
Usable:     .1 → .254
Hosts:      254
```

```text
192.168.10.0/26

Network:    192.168.10.0
Broadcast:  192.168.10.63
Usable:     .1 → .62
Hosts:      62
```

```text
10.10.0.0/20

Network:    10.10.0.0
Broadcast:  10.10.15.255
Usable:     10.10.0.1 → 10.10.15.254
Hosts:      4094
```

## Formulas

```text
Addresses = 2^(32 - prefix)

Usable Hosts = Addresses - 2
```

## Mental Model

```text
CIDR
  ↓
Subnet size
  ↓
Network Address
  ↓
Broadcast Address
  ↓
Usable Range
```

#  DNS

## Core Idea

DNS translates a domain name into information such as an IP address.

```text
Domain
  ↓
DNS Resolver
  ↓
DNS Servers
  ↓
IP / DNS Records
````

### Useful Commands

```bash
dig example.com
dig example.com A
dig example.com MX
dig example.com NS
```

## DNS Record Types

| Record  | Purpose                                                    |
| ------- | ---------------------------------------------------------- |
| `A`     | Domain → **IPv4**                                          |
| `AAAA`  | Domain → **IPv6**                                          |
| `CNAME` | Alias → another domain                                     |
| `MX`    | **Mail servers**                                           |
| `NS`    | **Authoritative DNS servers**                              |
| `TXT`   | Arbitrary text; often used for verification/email policies |

### Example

```text
example.com
    ↓
A record
    ↓
93.184.216.34
```

## Mental Model

```text
A     → IPv4
AAAA  → IPv6
CNAME → Alias
MX    → Mail
NS    → DNS servers
TXT   → Text / verification
```

### Goal

When you see a DNS record, immediately know:

**"What does this record tell me?"**

# HTTP

## Core Idea

HTTP is the language between the client and the server.

```text
Client
  ↓
HTTP Request
  ↓
Server
  ↓
HTTP Response
````

## Request Anatomy

```http
POST /login HTTP/1.1
Host: target.local
Content-Type: application/x-www-form-urlencoded
Cookie: session=abc123

username=admin&password=test
```

```text
POST        → Method
/login      → Path
HTTP/1.1    → Version
Host        → Target host
Content-Type → How to parse the body
Cookie      → Session/state
Body        → Data sent to server
```

## Burp Repeater

Take a request → modify ONE thing → predict → Send → analyze.

Try changing:

```text
Method
Cookie
Parameter
Content-Type
Body
```

## Pentesting Mindset

```text
Input
  ↓
Application
  ↓
What happens?
  ↓
Response
```

Always ask:

> "What happens if I change this?"

## Goal

When you see an HTTP request, understand:

* What is being requested?
* Who is making the request?
* What data is being sent?
* What identifies the session?
* How will the server interpret the data?
* What do I expect if I modify it?

# HTTPS

## Core Idea

HTTPS = HTTP secured with TLS.

```text
HTTP
 ↓
TLS
 ↓
Encrypted HTTP Traffic
````

## Certificate

The server sends a certificate containing information such as:

```text
Hostname
Public Key
Issuer
Validity
```

It helps the client verify the server's identity.

## CA

CA = Certificate Authority

```text
Server Certificate
        ↓
    Signed by CA
        ↓
Browser trusts CA
        ↓
Certificate trusted
```

## Hostname Verification

The certificate must be valid for the hostname I'm visiting.

```text
example.com
     ↓
Certificate hostname
     ↓
Match?
```

Mismatch → browser warning/error.

## Encryption

HTTPS encrypts the HTTP traffic, protecting things like:

```text
Cookies
Passwords
Headers
Request/Response Body
```

from passive network observers.

## Why Burp Can See HTTPS

Burp acts as a proxy:

```text
Browser
   ↓ TLS
  Burp
   ↓ TLS
 Server
```

Burp creates two separate TLS connections.

The Burp CA is trusted by the browser, so the browser accepts Burp's generated certificate.

```text
Browser → Burp → Server
          ↓
       decrypt
          ↓
      inspect/modify
          ↓
        encrypt
```

## Mental Model

```text
Certificate → Who are you?
CA          → Who says you're trusted?
Hostname    → Is this certificate for this site?
TLS         → Encrypt the communication
Burp        → Trusted proxy that can inspect the traffic
```

## Goal

Understand:

"HTTPS protects the HTTP traffic, but a trusted TLS proxy like Burp can inspect it because the client is intentionally configured to trust Burp's CA."
