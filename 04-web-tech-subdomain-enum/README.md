# 04 — Web Tech Fingerprinting + Subdomain Enumeration

point of fingerprinting: find EXACT version of whatever's running, then check exploit-db / nvd / github advisories for that exact version. old software = known CVEs = often public PoC exploit code already written for you.

- **wappalyzer** — firefox/chrome extension, auto-detects stack on any page visit. fastest first look.
- **builtwith.com** — same but web-based, shows historical tech too (what they used to run before migrating — sometimes old subdomains still run the old stack)
- **whatweb** cli: `whatweb target.com -v` — verbose mode actually shows version numbers, not just "wordpress detected"
- **httpx** (projectdiscovery): `httpx -l subdomains.txt -title -tech-detect -status-code` — run tech detection across hundreds of subdomains at once instead of clicking through each

once you have the exact tech + version:
```
searchsploit "wordpress 6.2"          # searches exploit-db locally, kali has it preinstalled
```
if there's a match, you likely have a working exploit path without writing anything yourself.

## subdomain enumeration — full workflow, not just tool names

goal isn't "run one tool", it's chaining passive + active sources then filtering to what's alive:

```bash
# passive - pulls from public sources, zero interaction with target
subfinder -d target.com -o subs1.txt
amass enum -passive -d target.com -o subs2.txt

# certificate transparency - explained in recon notes, do this too
curl -s "https://crt.sh/?q=%.target.com&output=json" | jq -r '.[].name_value' | sort -u >> subs3.txt

# merge + dedupe
cat subs1.txt subs2.txt subs3.txt | sort -u > all_subs.txt

# check which ones actually respond
httpx -l all_subs.txt -sc -title -o alive.txt
```

why this matters for finding critical stuff: dev/staging/test/uat subdomains are found this way ALL THE TIME and are almost always less secured than the main app — default creds, debug mode on, no WAF, old vulnerable versions still deployed there because "it's not production." this is one of the highest-hit-rate techniques in real bug bounty.

also always check every subdomain's CNAME for takeover potential (see recon notes) — this alone is a common source of critical severity findings on bounty platforms because it lets you serve arbitrary content on the company's actual domain.
