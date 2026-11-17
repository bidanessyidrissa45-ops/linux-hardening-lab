# Linux Hardening Lab

Personal cybersecurity lab: a small virtual network (VirtualBox, isolated network) used to practise **server hardening** and **network analysis**.

| Machine | Role |
|---|---|
| Ubuntu Server | Target to harden |
| Kali Linux | Attacker / analysis workstation |

> Educational lab on my own virtual machines only.

## 1. SSH hardening

Key-based authentication with **Ed25519**, root login disabled, non-default port.

```bash
ssh-keygen -t ed25519 -C "lab"
ssh-copy-id -i ~/.ssh/id_ed25519.pub user@SERVER
```

See [`configs/sshd_config.d-hardening.conf`](configs/sshd_config.d-hardening.conf). Test the config before reloading: `sudo sshd -t && sudo systemctl reload ssh`.
Keep a second session open so you cannot lock yourself out.

## 2. Firewall (ufw), default deny

```bash
sudo ufw default deny incoming
sudo ufw default allow outgoing
sudo ufw allow 2222/tcp comment 'SSH'
sudo ufw enable
```

## 3. Brute-force protection (fail2ban)

See [`configs/jail.local`](configs/jail.local): bans an IP after 3 failed SSH logins.
Check: `sudo fail2ban-client status sshd`.

## 4. Logging

- Authentication logs: `/var/log/auth.log` (or `journalctl -u ssh`)
- Quick review: `sudo grep "Failed password" /var/log/auth.log | tail`

## 5. Network analysis (Wireshark / nmap)

- `nmap -sS -p- SERVER` from Kali before and after hardening: compare open ports
- Wireshark captures to understand **DNS**, **TLS handshakes** and **port scans**

## Method (GRC view)

Each step follows the same loop: **baseline, risk, control, verification, evidence**. This is the logic of a security control review (ISO 27001 Annex A: access control, logging, network security).

## To add

- [ ] Screenshots before / after hardening (nmap results, `ufw status`, `fail2ban-client status`)
- [ ] Wireshark capture excerpts
