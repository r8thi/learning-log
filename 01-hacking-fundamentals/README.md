# 01 — Hacking Fundamentals

hacking = outsmarting a system, that's it. everything else is just labels on top of that.

## types of hackers

- **white hat (ethical)** — permission-based, gets paid to break things legally
- **black hat** — for profit / malicious, no permission
- **grey hat** — does it for fun, no real harm intended, but still no permission (legally still risky)
- **hacktivist** — hacking for a political/social cause, not money
- (worth adding: **script kiddie** — uses others' tools without understanding them, and **state-sponsored** — nation-backed, APT-level)

## 3 approaches to testing an application

| Type | Knowledge of code | Notes |
|---|---|---|
| **White box** | Full internal knowledge (source code, architecture) | closest to how a dev would audit their own app |
| **Black box** | Zero knowledge, only external interaction | closest to how a real attacker sees it |
| **Grey box** | Partial knowledge | most common in real engagements — client usually gives *some* context |

## red team / blue team / purple team

1. **red team** — pentesters, offensive side, simulates the attacker
2. **blue team** — defense, incident response, monitors + reacts
3. **purple team** — red + blue collaborating, feeding findings back and forth to actually fix vulnerabilities (not just find them)

worth knowing: purple teaming today often uses tools like **Atomic Red Team** or **Caldera** to simulate real attacker TTPs (tactics/techniques/procedures) in a controlled way, mapped straight to MITRE ATT&CK — so red and blue are testing against the same playbook instead of red just "winning."
