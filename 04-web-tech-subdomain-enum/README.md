# 04 — Identifying Web Tech + Subdomain Enumeration

## identifying web technology

point: figure out exactly what a site is built with (framework, CMS, server, JS libraries) — then check if that specific version has a known exploit. this is where recon turns into an actual attack path.

- **Wappalyzer** — browser extension (Firefox/Chrome), auto-detects tech stack visiting any site. fastest way to fingerprint.
- **builtwith.com** — same idea, web-based, sometimes has more historical data (what tech a site used to run).
- **whatweb** — terminal tool, preinstalled on Kali. good for scripting/automation since it's CLI.
- worth adding: **httpx** (from ProjectDiscovery) — fast, scriptable, great for fingerprinting a huge list of hosts at once instead of one at a time.

## subdomain enumeration

subdomains (`pay.example.com`, `admin.example.com`, `staging.example.com`) matter because dev/staging/admin subdomains are often way less secured than the main site — classic place to find low-hanging vulnerabilities.

- older tools mentioned in lecture: **Sublist3r**, various subdomain-finder websites
- current go-to stack (faster + more thorough, actively maintained):
  - **subfinder** (ProjectDiscovery) — passive subdomain discovery, pulls from many sources at once
  - **amass** (OWASP) — deeper, combines passive + active enumeration, also does network mapping
  - **assetfinder** — quick and simple, good for a first pass
  - **crt.sh** (certificate transparency) — covered in recon notes, but genuinely one of the best subdomain sources since it's just public cert logs, no guessing involved
- pipe subdomain results into **httpx** to check which ones are actually alive before wasting time scanning dead subdomains
