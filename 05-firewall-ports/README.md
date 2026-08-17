# 05 — Firewalls, Ports & Protocols

## firewall

acts like a security guard — controls what traffic is allowed in/out of a network. detecting one before scanning matters because it changes what results you can trust (a "closed" port might actually be filtered by a firewall, not actually closed).

- **wafw00f** — tries to detect if a site is behind a WAF (Web Application Firewall) and identify which one. works sometimes, not 100% reliable since WAFs actively try to hide.

## ports

ports = doorways for data on a device. each port is tied to a specific service so traffic goes to the right program.

## protocols

rules for how data moves between devices, regardless of manufacturer.

- **TCP** — reliable, connection-based ("long term" handshake, guarantees delivery)
- **UDP** — fast, connectionless ("short term", no delivery guarantee — used where speed > reliability)

## common ports

| Port | Protocol | Service | Notes |
|---|---|---|---|
| 20 | TCP | FTP (data) | actual file transfer |
| 21 | TCP | FTP (control) | connect here first, then data flows through 20 |
| 22 | TCP | SSH | encrypted remote login |
| 23 | TCP | Telnet | remote login, **unencrypted** — basically deprecated, huge red flag if open |
| 25 | TCP | SMTP | sending mail between servers |
| 53 | UDP | DNS | domain → IP resolution |
| 80 | TCP | HTTP | unencrypted web traffic |
| 110 | TCP | POP3 | retrieving email |
| 143 | TCP | IMAP | retrieving email (syncs across devices, unlike POP3) |
| 443 | TCP | HTTPS | encrypted web traffic |
| 445 | TCP | SMB | Windows file sharing — classic target (EternalBlue etc.) |
| 3306 | TCP | MySQL | database, should basically never be exposed to the internet |
| 3389 | TCP | RDP | Windows remote desktop — huge ransomware entry point when exposed |
| 8080 | TCP | HTTP-alt | common alt web port, dev servers, proxies |
