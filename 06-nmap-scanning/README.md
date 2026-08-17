# 06 — Scanning with Nmap (active recon)

nmap = network mapper. once you know a target exists, this is how you find out what's actually running on it.

## scan types

| Scan | Flag | What it does |
|---|---|---|
| TCP SYN Scan | `-sS` | sends a SYN packet, doesn't complete the handshake — fast + stealthy ("half-open" scan) |
| TCP Connect Scan | `-sT` | fully completes the TCP handshake — noisier, but works without root privileges |
| UDP Scan | `-sU` | checks UDP ports (DNS, SNMP, etc.) — slower than TCP scans |
| FIN Scan | `-sF` | sends a lone FIN packet to infer open/closed state, can sneak past basic firewalls |
| NULL Scan | `-sN` | TCP packet with zero flags set — same idea, evasion-focused |
| Xmas Scan | `-sX` | FIN + PSH + URG flags together ("lit up like a christmas tree") — evasion technique |
| ACK Scan | `-sA` | doesn't tell you open/closed, tells you if a firewall is filtering traffic |
| Host Discovery | `-sn` | finds which hosts are alive on a network without port-scanning them |
| Version Detection | `-sV` | identifies the service + version running on an open port — critical for finding known exploits |
| OS Detection | `-O` | fingerprints the target's OS |
| Aggressive Scan | `-A` | combines OS detection + version detection + NSE scripts + traceroute in one go |
| NSE Script Scan | `--script` | runs Nmap's scripting engine — vuln detection, brute force, enumeration, etc. |

## example commands

```bash
nmap -sS -sV -O target.com                 # SYN scan + version + OS detection
nmap -p- target.com                        # scan all 65535 ports, not just top 1000
nmap --script vuln target.com               # run NSE vulnerability-detection scripts
nmap -sC -sV target.com                     # default scripts + version detection (common combo)
```

## worth knowing (speed + modern alternatives)

- **masscan** — insanely fast port scanner, can scan the entire IPv4 range in minutes. use it to find *which* ports are open across a huge range, then hand off to nmap `-sV`/`-A` for actual service detail — masscan alone doesn't do deep service/version detection well.
- **rustscan** — modern wrapper that scans all ports fast, then automatically feeds the results into nmap for the detailed scan. basically "masscan speed, nmap depth" in one tool.
- **NSE script categories** worth knowing exist: `auth`, `vuln`, `exploit`, `brute`, `discovery`, `safe` vs `intrusive` — `--script safe` when you don't want to risk crashing something.
