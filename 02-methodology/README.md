# 02 — Ethical Hacking Methodology + Cyber Kill Chain

## methodology (5 phases)

1. recon/footprinting — active or passive. chor dekh raha hai ghar mein kaha se andar ghus sakta hai.
2. scanning — find actual vulns on what recon turned up
3. gaining access — exploit it, usually a payload
4. maintaining access — persistent connection, survives reboot/disconnect
5. clearing tracks — wipe logs, delete evidence

## cyber kill chain (7 stages, Lockheed Martin, military-inspired)

1. recon — id vuln + gather info
2. weaponization — build payload matched to that vuln
3. delivery — get payload to target (phishing, malicious download, etc)
4. exploitation — payload fires and executes
5. installation — persistence (backdoor, rootkit)
6. command & control (C2) — remote channel to control the compromised box
7. actions on objectives — do whatever the fuck you actually came for

why it matters defensively: break ANY one link, whole attack dies. this is the entire logic behind defense-in-depth — you don't need to stop everything, you need to stop one stage.

## MITRE ATT&CK — this basically replaced the kill chain in real practice

kill chain is 7 linear stages. real attacks don't go 1→7 cleanly, they loop, skip, repeat stages. ATT&CK is a MATRIX instead of a line — tactics (initial access, execution, persistence, privesc, defense evasion, credential access, lateral movement, exfil, impact) each with dozens of specific documented techniques (with IDs like T1566 for phishing) pulled from real observed attacks, not theory. when you read an incident report or a threat intel writeup today, it's talking in ATT&CK technique IDs, not kill-chain-stage-numbers. learn this framework, not just the kill chain — it's what's actually used.
