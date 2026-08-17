# 01 — Hacking Fundamentals

hacking = outsmarting a system. that's it, everything else is labels.

## types of hackers

- white hat (ethical) — permission-based, gets paid to break things legally
- black hat — for profit / malicious, no permission
- grey hat — fun, no real harm intended, still no permission (legally still risky af)
- hacktivist — hacking for a cause not money
- script kiddie — runs other people's tools/exploits without understanding them
- APT / state-sponsored — nation-backed, long-term, patient, resourced completely differently from everyone above

## 3 approaches to testing an app

1. white box — full internal knowledge, source code + architecture. closest to a dev auditing their own app.
2. black box — zero knowledge, purely external. closest to what a real attacker sees on day 1.
3. grey box — partial knowledge. most real client engagements are this — they give you SOME context (creds, api docs) to save time.

## red / blue / purple team

- red team — offensive, simulates the attacker
- blue team — defense, incident response, watches and reacts
- purple team — red + blue actually talking to each other, feeding findings back so stuff gets FIXED not just found

purple teaming in practice today runs on **Atomic Red Team** or **Caldera** — automated attack simulation mapped directly to MITRE ATT&CK technique IDs, so red team isn't just "winning" against blue, both sides are testing against the exact same documented technique instead of red doing whatever and blue guessing what happened.
