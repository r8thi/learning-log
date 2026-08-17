# 08 — Operating Systems Fundamentals

OS = the software layer between you/apps and the actual hardware. without it every app would need to fight for direct control of CPU/RAM/disk itself — chaos. OS is the single coordinator that stops that.

stack: **user -> applications -> operating system -> hardware**

## privilege separation — kernel space vs user space

- **kernel space** — the locked-down core. kernel runs here, has raw unrestricted access to CPU, memory, storage, all hardware.
- **user space** — where normal apps run. apps CANNOT touch hardware directly. if an app wants to open a file, play sound, hit the network — it makes a **system call** and asks the kernel to do it on its behalf.

why this separation matters (this is actually a security concept, not just architecture): if a normal app crashes, it only kills itself, not the whole system, because it never had raw hardware access to begin with. this is also exactly why **privilege escalation** is a whole category of attack — the entire goal of a privesc exploit is breaking OUT of user space and getting kernel-level (or root/SYSTEM) access. a kernel exploit = full box compromise because kernel space has zero restrictions.

## core OS duties

| Duty | What it does | Why it matters offensively/defensively |
|---|---|---|
| Process management | creates/schedules/prioritizes/kills running programs, decides CPU time per process | process injection, killing security tools (EDR/AV) via process manipulation |
| Memory management | allocates RAM, isolates each process's memory, reclaims on close, virtual memory when RAM low | buffer overflows, memory corruption exploits, ASLR/DEP exist specifically to make this harder to abuse |
| File system management | directories, naming, paths, permissions, metadata | permission misconfigs = privesc, path traversal attacks abuse how paths are resolved |
| User management | multiple accounts, authentication, permissions on who can access what | credential attacks, privilege boundaries between users is the whole game in privesc |
| Device management | drivers + hardware abstraction layer so apps just say "print this" without knowing hardware specifics | vulnerable/unsigned drivers are a known privesc + persistence vector (BYOVD attacks) |

## OS-level security basics (before any AV/firewall even gets involved)

- **authentication** — passwords, biometrics, verifying identity
- **permissions** — exactly what a user/app can read/write/execute
- **isolation** — kernel/user space split, keeps one process from wrecking another
- **system protection** — guards critical system files/settings from unauthorized changes

## GUI vs CLI

- **GUI** — click-based, visual (folders, windows, menus). easier, less precise.
- **CLI** — text commands, direct and exact, faster once you know the syntax. this is the actual interface for real pentesting/sysadmin work — GUI doesn't scale for automation, scripting, remote work over SSH, or working on headless servers (which almost all real servers are — no GUI at all).

## OS types (know which category a target falls into — changes your whole approach)

| Type | Use case | Key trait |
|---|---|---|
| Desktop | personal computers | rich GUI, multitasking, user-focused |
| Server | web hosting, db, cloud backend | headless (no GUI), max uptime, multi-user, remote access — this is what you're attacking 90% of the time in real engagements |
| Mobile | phones/tablets | touch UI, power efficient, app sandboxing |
| Embedded | IoT, routers, smart TVs, cars | tiny footprint, limited hardware, often runs ancient unpatched firmware = common attack surface |
| Virtual/Cloud | lab machines, containers, cloud instances | lightweight, scalable, spun up/down fast |

### real families you'll actually run into

- **Desktop:** Windows 10/11, macOS (Sonoma/Sequoia/Tahoe), Linux (Ubuntu/Debian/Fedora)
- **Server:** Windows Server 2016/19/22/25, Linux (Ubuntu Server/Debian/CentOS/Red Hat), Unix (AIX/Solaris — legacy but still in finance/telecom/gov, worth knowing if you ever hit an old enterprise env)
- **Mobile:** Android 14-16, iOS 17/18/26
- **Embedded:** OpenWrt, Ubuntu Core, Yocto — real-time OS like FreeRTOS/VxWorks/QNX for stuff needing guaranteed response time (aircraft, industrial control)
- **Cloud:** Ubuntu LTS, Amazon Linux, Rocky Linux for VMs; Alpine/Bottlerocket/Flatcar for containers (Alpine specifically shows up EVERYWHERE in docker images because it's tiny — worth getting comfortable with its minimal toolset, `apk` not `apt`)

## gathering system info — actually do this on any box you touch

on Linux:
```bash
uname -a              # kernel version — check against known kernel exploits
cat /etc/os-release    # exact distro + version
whoami; id             # current user + privilege level
hostname
lscpu; free -h; df -h  # cpu, ram, disk
```

on Windows (CLI, not GUI clicking):
```powershell
systeminfo             # full OS build info, patch level
whoami /priv            # current privileges — tells you what's escalatable
hostname
```

this isn't just "know your own machine" — the FIRST thing you do on ANY newly compromised/accessed box in a real engagement is exactly this: figure out exact OS + kernel version, then check if that specific version has a known local privesc exploit. same searchsploit logic from folder 04, just aimed at the OS itself instead of an app.

---
*source: TryHackMe — Operating Systems Introduction room*