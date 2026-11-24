# Hi, I'm Zach

**Cybersecurity Student · Offensive Security · Linux Hardening**

I build and harden Linux systems, and I write tools that expose what the operating system is actually doing. My work centers on offensive security, host-level detection, and operational security, with the goal of reducing attack surface without sacrificing usability. Off the clock, I'm building a pixel-art game entirely in code.

[TryHackMe (Top 1%)](https://tryhackme.com/p/stlwtrs) · [LinkedIn](https://www.linkedin.com/in/zachary-white-11a859421/) · [Repositories](https://github.com/Stlwtrs?tab=repositories) · [Sponsor my work](https://github.com/sponsors/Stlwtrs)

---

## Flagship Project: [AuricOS](https://github.com/Stlwtrs/AuricOS)

A security-hardened Arch Linux distribution built from scratch with archiso. AuricOS ships as a bootable live ISO with a custom Calamares installer, and every system it installs is hardened by default. Each hardening change is measured with Lynis on fresh installs and documented, including the findings deliberately accepted as trade-offs.

**Status:** v1.0 beta, in active development · **License:** GPL-3.0

### System Composition

| Component | Implementation |
| --- | --- |
| Base | Arch Linux (core, extra) |
| Kernel | `linux-cachyos-bore` (CachyOS repo, kernel only) |
| Boot / Init | GRUB (BIOS and UEFI), systemd |
| Desktop | XFCE, LightDM GTK greeter, custom navy-and-gold branding |
| Installer | Calamares 3.4.2 with custom theme, slideshow, and password policy |
| Bundled Tooling | Metasploit, Lynis, arch-audit, AIDE, auditd, AppArmor, ufw |
| Browsers | Mullvad Browser, Tor Browser, LibreWolf |

### Measured Hardening (Lynis 3.1.7, fresh QEMU/KVM installs)

| Round | Hardening Index | Changes |
| --- | --- | --- |
| Baseline | 64 | Stock build, 30 suggestions |
| Round 1 | 72 | Firewall, AppArmor, auditd, sysctl hardening, core dumps disabled |
| Round 2 | 75 | Login banner, umask 027, password hashing and quality, protocol blacklist, compiler restriction |
| Round 3 | 78 | 16-rule auditd ruleset, AIDE baseline, ufw sysctl fix, SSH config permissions |
| Round 4 | 78 | Copy Fail mitigation (`algif_aead` blocked), CopyFix, USBSnitch and hextcp bundled, AIDE database exclusion |

### Key Controls

- **Kernel:** AppArmor via `lsm=` (160+ profiles); `kptr_restrict=2`, `dmesg_restrict=1`, Yama ptrace scope, unprivileged BPF disabled, BPF JIT hardening, SysRq disabled
- **Network:** ufw default-deny inbound; reverse-path filtering, SYN cookies, ICMP redirect and source-route rejection; dccp, sctp, rds and tipc blocked; sshd disabled
- **Authentication:** Root locked on every install; `pam_pwquality` enforcement; yescrypt cost factor raised; authorized-use banner
- **Integrity and Auditing:** auditd rules covering identity files, sudoers, PAM, SSH, kernel modules, and firewall changes; AIDE baseline of \~400,000 files on a systemd timer
- **Toolchain:** Compilers restricted to `root:wheel`, enforced by a pacman hook after upgrades
- **Supply Chain:** AUR packages built in clean chroots, PGP-verified, reviewed before build, and served from a local repo removed from installed systems

### Installer Engineering

Three custom Calamares shellprocess stages handle work the stock installer cannot: copying the kernel from outside the squashfs, removing live-only components and applying all hardening inside the target, and locking root after user creation.

### Roadmap

Per-service systemd sandboxing · UEFI and Secure Boot validation · physical hardware testing · optional LUKS full-disk encryption · custom AppArmor profiles · CI-built, signed ISO releases · additional desktop environments

> Developed with AI assistance (Claude) for configuration drafting. Design decisions, builds, testing, and verification are my own. Full disclosure in the repository.

---

## Security Tooling

All three ship in AuricOS as of hardening round 4.

| Project | Description | Stack |
| --- | --- | --- |
| [**CopyFix**](https://github.com/Stlwtrs/CopyFix) | Detects page-cache tampering of setuid/setgid binaries, the footprint of the Copy Fail kernel vulnerability (CVE-2026-31431). Compares cached reads against `O_DIRECT` disk reads byte-for-byte and reports offsets and SHA-256 hashes of both versions. JSON output, defined exit codes, sandboxed systemd timer. | Python (stdlib only) |
| [**hextcp**](https://github.com/Stlwtrs/hextcp) | Decodes `/proc/net` socket tables where `netstat`, `ss`, and `lsof` are unavailable, such as restricted shells during privilege escalation. Supports TCP/UDP over IPv4 and IPv6, Unix sockets, PID mapping, suspicious-path flagging, IP watchlists, and JSON Lines output. Core decoding verified to run with an empty `PATH`. | Pure Bash |
| [**USBSnitch**](https://github.com/Stlwtrs/USBSnitch) | Real-time USB connection alerts alongside USBGuard. Listens for kernel hotplug events over netlink, reads raw descriptors, rates BadUSB risk (e.g. storage devices also presenting as keyboards), and reports USBGuard's allow/block decision. Runs as a sandboxed systemd service. | Python (stdlib only) |

---

## Documentation & Research

**[Personal Operational Security Stack](https://github.com/Stlwtrs/My-Personal-Operational-Security-Stack)** A six-layer security architecture for a Linux daily driver, built around a defined threat model: physical security (Dasharo firmware, Intel ME mitigation, LUKS2, USBGuard), operating system (`linux-hardened`, AppArmor, Btrfs snapshots), network privacy, identity and credentials (self-hosted Vaultwarden), financial privacy, and mobile security (planned). Includes a read-only audit script that verifies the deployed controls.

**TryHackMe Write-Ups** Walkthroughs that document the reasoning behind each step, not only the commands.

- [Intermediate Nmap](https://github.com/Stlwtrs/THM-Write-Up-Intermediate-Nmap)
- [Net Sec Challenge](https://github.com/Stlwtrs/THM-Write-Up-NetSec-Challenge)
- [Red Stone One Carat](https://github.com/Stlwtrs/THM-Write-Up-Red-Stone-One-Carat)

---

## Game Development

[![The Thousand Year Crown](https://raw.githubusercontent.com/Stlwtrs/TTYC/main/docs/banner.png)](https://stlwtrs.github.io/TTYC/)

**[The Thousand Year Crown](https://github.com/Stlwtrs/TTYC)** A free pixel-art action platformer that runs in the browser. You rise from the ash of a burned city and climb a ruined kingdom toward a crown that has passed from tyrant to tyrant for a thousand years. No engine, no libraries, and no image or audio files: the art is drawn in code on a low-resolution canvas, and the soundtrack is synthesized live with the Web Audio API.

- **Play it:** [stlwtrs.github.io/TTYC](https://stlwtrs.github.io/TTYC/)
- **Status:** milestone 5 of 9: five regions, six bosses and four of the six hidden Voices are playable, with three endings planned
- **Engineering:** rooms as plain-text tile maps, a generator-based cutscene runner, a boss whose second phase lands every attack on the music's beat, and imported save files validated field by field
- **License:** free to play, all rights reserved so it stays in one official place

**[Aurelith](https://github.com/Stlwtrs/Aurelith)** The five-phase boss fight that started it: a bullet-hell duel with a live-synthesized metal soundtrack, in a single HTML file. It becomes the game's final boss.

> Built with AI assistance (Claude), which drafted most of the code, art, music and dialogue. The direction, design decisions, testing and balance calls are my own.

---

## Technical Skills

| Area | Tools and Technologies |
| --- | --- |
| Offensive & Enumeration | Metasploit, Burp Suite, Nmap, Gobuster, Hydra, Wireshark |
| Defensive & Hardening | AppArmor, auditd, AIDE, Lynis, ufw, USBGuard, LUKS2, sysctl |
| Linux Engineering | Arch Linux, archiso, Calamares, systemd, PAM, pacman/AUR packaging |
| Privacy & OPSEC | Mullvad VPN, Tor, Tails, Vaultwarden |
| Languages | Bash, Python, Ruby, PowerShell |

## Current Focus

- Active Directory exploitation, host evasion, and network pivoting
- CompTIA Security+ certification
- AuricOS: systemd service sandboxing and UEFI validation
- The Thousand Year Crown: the Glass Choir, the Ascent, and the finale
- CLI automation for enumeration and OSINT

## Approach

Root-cause analysis over running scripts blindly. Every tool and engagement is documented, every security claim is measured, and every limitation is stated.

## Support

Everything here is free. If my work has been useful to you, or you enjoyed the game, you can [sponsor me on GitHub](https://github.com/sponsors/Stlwtrs). Monero and Bitcoin addresses are in the [AuricOS README](https://github.com/Stlwtrs/AuricOS#supporting-auricos), PGP-signed in [`DONATE.asc`](https://github.com/Stlwtrs/AuricOS/blob/main/DONATE.asc).
