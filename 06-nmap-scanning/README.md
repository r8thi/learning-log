# 06 — Scanning with Nmap

nmap = network mapper. once a target exists (from recon), this is how you find out what's actually running on it.

## scan types

| Scan | Flag | What it actually does |
|---|---|---|
| TCP SYN | `-sS` | sends SYN, doesn't finish handshake — fast, stealthy, "half-open" |
| TCP Connect | `-sT` | full handshake — noisier, but works without root |
| UDP | `-sU` | checks UDP ports (dns, snmp etc) — slow af, don't run it on everything blindly |
| FIN | `-sF` | lone FIN packet, sneaks past dumb stateless firewalls |
| NULL | `-sN` | zero flags set, same evasion idea |
| Xmas | `-sX` | FIN+PSH+URG together ("lit up like a christmas tree"), evasion |
| ACK | `-sA` | doesn't tell open/closed, tells you if firewall is filtering |
| Host Discovery | `-sn` | which hosts are alive, no port scan |
| Version Detect | `-sV` | service + version on open ports — THIS is what you feed into searchsploit/nuclei |
| OS Detect | `-O` | fingerprints target OS |
| Aggressive | `-A` | OS + version + NSE scripts + traceroute, all in one |
| NSE | `--script` | runs nmap's scripting engine |

## commands you'll actually run

```bash
nmap -sS -sV -O target.com                  # standard first pass
nmap -p- target.com                         # ALL 65535 ports, not just top 1000 — people hide services on weird ports specifically hoping you don't check
nmap -sC -sV target.com                     # default safe scripts + version, common combo
nmap --script vuln target.com               # NSE vuln-detection category, flags known CVEs directly
nmap --script safe target.com               # when you don't want to risk crashing a fragile service
```

## when nmap alone is too slow

**masscan** — scans entire IPv4 ranges in minutes, insanely fast, but weak on service/version detail:
```bash
masscan -p1-65535 target.com --rate 1000
```

**rustscan** — scans all ports fast then auto-feeds results straight into nmap for the detailed `-A`/`-sV` pass. basically masscan's speed + nmap's depth, one command:
```bash
rustscan -a target.com -- -A
```

practical flow: rustscan/masscan first to find EVERY open port fast across the full range → nmap `-sV -sC` on just those specific ports for detail → feed the service+version info into searchsploit / nuclei to check for known exploits (see folder 07).
