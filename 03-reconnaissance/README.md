# 03 — Reconnaissance

recon = info gathering, active (touching the target, they can see you) or passive (public info only, zero risk).

## google dorking — actual usage, not just the operators

operators are useless without knowing WHAT to search for. this is the actual list of dorks that find real stuff:

```
filetype:env "DB_PASSWORD"                    -> exposed .env files with db creds
filetype:log "password"                        -> log files leaking creds
intitle:"index of" "backup"                     -> open directory listings with backups
inurl:wp-content/uploads filetype:sql           -> exposed wordpress db dumps
site:pastebin.com "api_key" target_company      -> leaked keys on pastebin
intext:"sql syntax near" OR intext:"syntax error has occurred"   -> pages leaking sql errors (sqli indicator)
filetype:xls inurl:"email.xls"                  -> leaked email lists
inurl:"/actuator/env"                           -> exposed spring boot actuator (leaks full config incl. secrets)
inurl:swagger-ui.html OR inurl:api-docs          -> exposed api docs, sometimes with no auth
```

**Google Hacking Database (GHDB)** on exploit-db.com — thousands of pre-built dorks sorted by category (footholds, files containing passwords, vulnerable servers etc). don't reinvent dorks, browse GHDB for the target's tech stack first.

combine operators for real hits:
```
site:target.com filetype:pdf intext:"confidential"
site:target.com -site:www.target.com   -> forces subdomains to show up instead of the main site
```

## WHOIS + DNS recon

```bash
whois target.com
host target.com
host -t ns target.com
host -t mx target.com
dig target.com -t SOA
dig target.com ANY          # some resolvers still support this, dumps everything at once
```

### DNS record types (what each one actually tells you as an attacker)

| Record | Attacker use |
|---|---|
| A / AAAA | maps domain to IP — tells you the actual server to attack, or if it's behind a CDN/WAF |
| CNAME | if it points to an unclaimed service (github pages, s3 bucket, herokuapp etc) = **subdomain takeover** target |
| NS | tells you who hosts DNS — sometimes leaks internal infra provider |
| MX | tells you the mail provider — google workspace vs self-hosted changes your phishing/spoofing approach |
| TXT | SPF/DKIM records here — misconfigured SPF (`~all` instead of `-all` or missing entirely) = email spoofing is possible |

**subdomain takeover — actually understand this one, it's a real critical finding:**
company sets up `blog.target.com` pointing (via CNAME) to a github pages / heroku / s3 / azure app, then deletes that app but forgets to remove the DNS record. the CNAME still points to `something.github.io` but nobody owns it anymore. you claim that same service name on github pages, and now `blog.target.com` serves YOUR content. tool for finding this: **`nuclei -t subdomain-takeover`** or **`subjack`**. always check CNAMEs on every subdomain you find.

crt.sh is the best subdomain source there is — it's public certificate transparency logs, every SSL cert issued gets logged whether the company wants it public or not:
```
https://crt.sh/?q=%.target.com&output=json
```
pull that, extract unique subdomains, feed into httpx to check which are alive.

## social recon

- **sherlock** — username across platforms, first move for OSINT on a person
- **theHarvester** — pulls emails, subdomains, employee names straight from search engines: `theHarvester -d target.com -b all`
- leaked employee emails from breaches (haveibeenpwned, dehashed) matter because reused passwords across services is still one of the most common initial access vectors in real breaches — not a sophisticated exploit, just credential reuse.
