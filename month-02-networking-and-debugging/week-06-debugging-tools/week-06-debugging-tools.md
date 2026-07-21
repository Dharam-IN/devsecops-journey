# tcpdump

## What it is
- Command-line packet capture tool. Captures packets flowing through a network interface and prints them as text.
- Uses **libpcap** (the capture library) and **BPF** (Berkeley Packet Filter) for filtering — the *same* engine Wireshark uses.
- Needs **root** (`sudo`) because it reads raw network traffic.

## Essential flags
| Flag | Meaning |
|---|---|
| `-i <iface>` | Which interface to capture on (`eth0`, `wlo1`, `any`) |
| `-D` | List available interfaces |
| `-w file.pcap` | Write raw packets to a file (for Wireshark later) |
| `-r file.pcap` | Read packets back from a file |
| `-n` | Don't resolve IPs to hostnames (faster, cleaner) |
| `-nn` | Don't resolve IPs *or* port names |
| `-c <N>` | Stop after N packets |
| `-v` / `-vv` / `-vvv` | More verbose detail |
| `-A` | Show packet payload as ASCII (readable text) |
| `-X` | Show payload as hex + ASCII |
| `-e` | Show Ethernet (link-layer) header too |
| `-tttt` | Human-readable timestamps |

## BPF filter syntax (capture filters)
Built from three building blocks you combine:
- **Type:** `host`, `net`, `port`, `portrange`
- **Direction:** `src`, `dst` (omit = both)
- **Protocol:** `tcp`, `udp`, `icmp`, `ip`, `ip6`, `arp`
- **Combine with:** `and` / `or` / `not`

Examples:
```bash
tcpdump -i eth0 host 192.168.1.1          # to/from this IP
tcpdump -i eth0 dst port 80               # only traffic going to port 80
tcpdump -i eth0 tcp port 443              # HTTPS
tcpdump -i eth0 net 192.168.1.0/24        # a whole subnet
tcpdump -i eth0 'port 80 or port 443'     # web traffic
tcpdump -i eth0 'not port 22'             # exclude SSH (see note)
```
- ⚠️ **Always exclude your SSH session** when capturing on a remote server (`not port 22`), or you create a feedback loop — your capture generates traffic that you then capture.
- Quote the filter (`'...'`) when it contains spaces or special characters.

## Reading the output
A typical TCP line:
```
12:34:56.789 IP 192.168.1.10.50432 > 93.184.216.34.80: Flags [S], seq 12345, win 64240, length 0
```
Read it as: `time  src_ip.src_port > dst_ip.dst_port: flags, ...`

**TCP flag codes** (the important part):
| Code | Flag | Meaning |
|---|---|---|
| `[S]` | SYN | Start a connection |
| `[S.]` | SYN-ACK | Server agrees |
| `[.]` | ACK | Acknowledgement (the `.` = ACK bit) |
| `[P.]` | PSH-ACK | Carries actual data |
| `[F.]` | FIN | Closing connection |
| `[R]` | RST | Connection reset (rejected/killed) |

- **TCP 3-way handshake** = `[S]` → `[S.]` → `[.]`. You'll see this at the start of every TCP connection. Memorise it.

## HTTP capture
```bash
tcpdump -i eth0 -A -s0 'tcp port 80'
```
- `-A` shows the text, so you can read `GET / HTTP/1.1`, `Host:`, headers, etc.
- Only works for plain HTTP (port 80). HTTPS is encrypted — you'd see nothing readable.

## DNS capture
```bash
tcpdump -i eth0 -n port 53
```
- DNS runs on **UDP port 53**.
- You'll see queries (`A? google.com`) and responses (the IPs returned).
- `-n` is important here or the output gets cluttered with reverse lookups.

---

# Wireshark

## What it is (and how it relates to yesterday)
- **GUI version of tcpdump, with a protocol decoder.** Same libpcap capture engine, same BPF capture filters.
- The difference: instead of raw text, it **reassembles** packets and lets you click through each one layer by layer, and decodes 3000+ protocols.
- Mental model: **tcpdump captures, Wireshark analyses.** They're a team — capture on a server with tcpdump, open the file in Wireshark.

## Installation (Ubuntu/Debian)
```bash
sudo apt install wireshark
```
- Installer asks *"Should non-superusers be able to capture packets?"* → **Yes**
- Add yourself to the capture group so you don't need root every time:
```bash
sudo usermod -aG wireshark $USER
```
- **Log out and back in** (group changes don't apply until you do). Verify with `groups`.

## The GUI — 3 panes
1. **Packet List** (top) — one row per packet: No., Time, Source, Destination, Protocol, Length, Info. Color-coded by protocol.
2. **Packet Details** (middle) — the protocol tree for the selected packet, layer by layer:
   `Frame → Ethernet → IP → TCP → HTTP/TLS`
   Click the arrows to expand each layer. *This is what tcpdump couldn't show nicely.*
3. **Packet Bytes** (bottom) — the raw hex + ASCII.

**Menus worth knowing:**
- **Capture** → pick interface, start/stop
- **Statistics** → analysis tools (see below)
- **Analyze** → Follow streams, filter expressions

## Capture vs Display filters — THE key concept
This trips up every beginner. They do different jobs **and use different syntax.**

| | Capture filter | Display filter |
|---|---|---|
| **When** | *Before* capturing | *After* — filters the view |
| **Syntax** | **BPF** (tcpdump syntax) | **Wireshark's own syntax** |
| **Effect** | Discards non-matching **permanently** | Only **hides** — data still there, reversible |
| **Where** | Green bar on welcome screen | Main filter bar (top) |
| **Example** | `tcp port 443` | `tcp.port == 443` |

- **Rule of thumb:** capture everything, filter the display. Only use capture filters when traffic volume is huge (you don't want a 10 GB file). You can never recover packets you chose not to capture.
- Note the syntax difference: capture filter uses `tcp port 443` (spaces), display filter uses `tcp.port == 443` (dots and `==`).

## Display filter syntax
- **Operators:** `==` `!=` `>` `<` `>=` `<=`
- **Logic:** `and` / `or` / `not` (or `&&` `||` `!`)
- **Just type a protocol name** to see only that: `http`, `dns`, `tls`, `tcp`, `icmp`, `arp`, `dhcp`
- **Special operators:** `contains` (substring), `matches` (regex)

Common field filters:
```
ip.addr == 192.168.1.1                      # any packet to/from this IP
ip.src == 10.0.0.5                          # from this IP only
tcp.port == 80                              # this port
http.request.method == "GET"                # only GET requests
http.response.code == 404                   # only 404 errors
dns.qry.name contains "google"              # DNS lookups for google
tcp.flags.syn == 1 and tcp.flags.ack == 0   # connection starts (SYN only)
tcp.analysis.retransmission                 # find retransmitted packets
frame.len > 1000                            # large packets
!(arp or dns)                               # hide ARP/DNS noise
```

## Follow streams
- Right-click any packet → **Follow → TCP Stream** (or HTTP / TLS / UDP)
- Reassembles the **entire conversation** into one readable view:
  - Client → server data in one color
  - Server → client data in another
- Auto-applies a filter like `tcp.stream eq 0` so you're now seeing only that one conversation.
- **To get back to everything:** clear the filter bar.
- This is how you read a full HTTP request + response instead of clicking packet-by-packet.

## Analyzing a .pcap file
The real workflow that connects to yesterday:
```bash
# On a headless server (no GUI):
sudo tcpdump -i eth0 -w capture.pcap
# Ctrl+C to stop, copy file to your machine, then:
```
In Wireshark: **File → Open → capture.pcap**
- Practice files: search "**Wireshark sample captures**" — the official wiki has real ones (DNS, HTTP, malware traffic, etc.).

## Statistics menu (where the real analysis happens)
Don't just scroll packets — use these:
- **Protocol Hierarchy** — what protocols are in the capture and their % breakdown
- **Conversations** — who talked to whom, how many bytes/packets
- **Endpoints** — every host in the capture
- **Expert Information** — Wireshark auto-flags retransmissions, resets, errors, warnings for you
- **I/O Graph** — traffic volume over time

## Inspecting HTTPS
- **HTTPS = HTTP inside TLS = encrypted.** Filter with `tls`.
- **What you CAN see (unencrypted):** the **TLS handshake** —
  - **Client Hello** — includes **SNI** (the domain you're visiting), supported cipher suites
  - **Server Hello** — chosen cipher, TLS version
  - **Certificate** — the server's cert
  - Filter: `tls.handshake` or `tls.handshake.type == 1` (Client Hello only)
- **What you CAN'T see:** the actual content — it's `Application Data` = encrypted gibberish. **This is correct, not broken.**
- **The lesson = the contrast:** your neverssl.com stream was fully readable (HTTP). An HTTPS stream is encrypted. You can see *who* you talked to and *that* it happened — never *what* was said.

**Decrypting your own HTTPS (optional, advanced):**
```bash
export SSLKEYLOGFILE=~/sslkeys.log
# launch Chrome/Firefox from THIS terminal
```
Then Wireshark → **Edit → Preferences → Protocols → TLS → "(Pre)-Master-Secret log filename"** → point to that file. Now your own HTTPS decrypts.
- ⚠️ Only works for **your own** browser traffic (you're the client, you have the keys). You **cannot** passively decrypt someone else's HTTPS off the network — that's the entire point of TLS.

---

## 🔑 Quick reference: tcpdump vs Wireshark
| | tcpdump | Wireshark |
|---|---|---|
| Interface | Terminal | GUI |
| Best for | Quick capture, servers, no GUI | Deep analysis, reading streams |
| Filter syntax | BPF | BPF (capture) + own syntax (display) |
| Save format | `.pcap` | opens the same `.pcap` |
| Typical use | Capture on remote box | Analyse the captured file |

**Bottom line:** capture with tcpdump on servers → analyse in Wireshark. Same packets, two tools.