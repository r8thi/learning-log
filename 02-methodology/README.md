# 02 — Ethical Hacking Methodology + Cyber Kill Chain

## ethical hacking methodology (5 phases)

1. **recon / footprinting** — active or passive. basically: chor dekh raha hai ghar mein kaha se andar ghus sakta hai (the thief is scoping out which way in)
2. **scanning** — identify actual vulnerabilities on what you found in recon
3. **gaining access** — exploit it, usually via a payload
4. **maintaining access** — keep a persistent connection (backdoor, reverse shell that survives reboot)
5. **clearing tracks** — clean logs, delete evidence of the intrusion

## cyber kill chain (Lockheed Martin, military-inspired)

7-stage model for how an actual attack unfolds end to end:

1. **recon** — identify vulnerability + gather info on target
2. **weaponization** — build a payload matched to that vulnerability
3. **delivery** — get the payload to the target (phishing, malicious download, etc.)
4. **exploitation** — payload triggers and executes
5. **installation** — establish persistence (backdoors, rootkits)
6. **command & control (C2)** — set up a channel to remotely control the compromised system
7. **actions on objectives** — the actual goal (exfil data, ransomware, pivot deeper, whatever the attacker came for)

**why this matters defensively:** each stage is a chance to break the chain. blue teams don't need to stop everything — stopping *any one* link kills the whole attack. this is the whole logic behind layered defense (defense in depth).

### worth knowing (newer than the kill chain)

- **MITRE ATT&CK** — has mostly replaced/complemented the kill chain in modern practice. instead of 7 linear stages, it's a matrix of real-world tactics + techniques (initial access, execution, persistence, privilege escalation, defense evasion, credential access, lateral movement, exfiltration, impact) mapped from actual observed attacks. more granular, less linear — real attacks don't always go 1→7 in order.
- **Unified Kill Chain** — a newer model that merges the Lockheed Martin kill chain with ATT&CK-style tactics to fix the "too linear" problem.
