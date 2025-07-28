## 🛡️ arch-secure-admin: Hardened Linux Environment for Security Professionals

**`arch-secure-admin`** is a modular and high-security hardening framework for Arch Linux, designed for system administrators, security researchers, and power users who demand full control, auditability, and stealth.  
This project provides enterprise-grade protections using native Linux tools — without third-party bloat or data leakage.

---

### 🎯 Purpose

The goal of `arch-secure-admin` is to create a professionally hardened Linux operating environment that:
- Routes all traffic through WireGuard (`wg0`) with DNS leak protection
- Monitors system behavior using `auditd` with 64-bit syscall rules only
- Enforces strict firewall policies using `firewalld` with `iptables` backend
- Enables application sandboxing with `firejail`
- Integrates `SELinux` for mandatory access control (MAC)
- Detects and logs network-based threats using `Suricata IDS`
- Protects physical access with `USBGuard`
- Provides root-only log review — no desktop notifications or telemetry

---

### 🔐 Key Features

- **Auditd syscall-only rules** for privileged commands, file changes, and suspicious behavior
- **WireGuard VPN only**, using interface `wg0` (killswitch enforced)
- **Suricata IDS** with custom rule sets for DNS, HTTP, TLS, SSH, anomaly, and flow detection
- **Firewalld with iptables backend**, interface-zoned, DROP policy by default
- **Application sandboxing** using `firejail` with fine-grained app profiles
- **Mandatory Access Control** via `SELinux` in targeted or strict mode
- **USB device lockdown** using `USBGuard` policy enforcement
- **Stealth operation**, no GUI alerts or excessive logging — manual root-level inspection only

---

### 📁 Project Structure

```bash
arch-secure-admin/
├── auditd/           # syscall-only audit rules, modularized by behavior
├── firewalld/        # firewall zones, interface binding, iptables policies
├── suricata/         # IDS configuration, custom rule files, and tuning
├── selinux/          # policy modules and boolean tuning
├── firejail/         # isolated app profiles and runtime restrictions
├── scripts/          # watchdogs, manual log summarizers, integrity checks
├── docs/             # markdown-based security documentation and usage
└── LICENSE
