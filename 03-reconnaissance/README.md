# 03 — Reconnaissance

recon = gathering info, either by interacting with the target or just from what's public.

- **active recon** — you interact directly with the target (e.g. sending a phishing email, pinging their server). riskier, they can see you.
- **passive recon** — pulling info that's already publicly available (WHOIS, social media, search engines). no direct contact, no risk of detection.

## google dorking (google hacking)

using Google's search operators to find hidden pages, sensitive files, exposed creds, misconfigured stuff that shouldn't be indexed.

| Operator | Example | What it does |
|---|---|---|
| `site:` | `site:amazon.in` | only results from that exact domain |
| `filetype:` | `filetype:pdf` | only that file type |
| `intitle:` | `intitle:templerun` | word must be in the page's `<title>` tag |
| `intext:` | `intext:pokemon` | word must appear in the page body |
| `inurl:` | `site:cosmomentor.com inurl:admin` | word must appear in the URL path |

worth adding to the toolkit: **Google Hacking Database (GHDB)** on exploit-db has pre-built dork queries for finding exposed login panels, config files, cameras, etc. also `-site:` to exclude a domain, and combining operators (`filetype:env intext:DB_PASSWORD`) is where dorking actually gets dangerous/useful.

## WHOIS + DNS recon

**WHOIS** — public record of who registered a domain (owner, registration date, contact info, domain status). open protocol, anyone can query it.

- via browser: who.is
- via terminal: `whois microsoft.com`

**DNS (Domain Name System)** — the internet actually runs on IP addresses (like `142.250.win.win`), DNS just gives them human-readable names so we don't have to memorize numbers. when you type a domain, DNS resolves it to the corresponding IP.

### DNS record types

| Record | Purpose |
|---|---|
| `A` | maps domain → IPv4 address |
| `AAAA` | maps domain → IPv6 address |
| `CNAME` | aliases one domain to another (e.g. `www.google.com` → `google.com`) |
| `NS` | name server — which servers are authoritative for this domain |
| `SOA` | start of authority — admin info, refresh/retry timing for the zone |
| `TXT` | arbitrary text — often used for domain verification, SPF/DKIM (email security) |
| `MX` | mail exchange — where email for this domain should be delivered |

### tools

```bash
host microsoft.com          # gives IPv4/IPv6 (note: CDNs like Cloudflare can show multiple/rotating IPs to obscure origin)
host -t ns microsoft.com
host -t mx microsoft.com

nslookup
> set type=ns
> microsoft.com

dig microsoft.com -t SOA
dig microsoft.com -t NS
dig microsoft.com -t MX
```

`host`, `dig`, and `nslookup` all do roughly the same job, `dig` just gives more detail by default. worth also knowing **`dnsdumpster.com`** and **`crt.sh`** (certificate transparency logs) — crt.sh in particular is gold for finding subdomains since every SSL cert issued for a domain gets logged publicly, including subdomains you'd never find by guessing.

## social media recon

find what software a company/target uses, find employees to target (for social engineering), find leaked info.

- **Sherlock** — searches a username across a huge list of platforms/sites at once. great first step in OSINT recon.
- worth adding: **theHarvester** (pulls emails, subdomains, employee names from search engines + public sources), **Maltego** (visual link-analysis tool for mapping relationships between people/domains/infra — industry standard for OSINT investigations).
