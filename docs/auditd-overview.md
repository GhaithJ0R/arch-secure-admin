# 📘 auditd-overview\.md

## Overview: The Role of auditd in arch-secure-admin

`auditd` is the central auditing system for Linux that records low-level system activity using kernel-level syscall tracing. Within the `arch-secure-admin` project, it plays a foundational role in detecting unauthorized activity, misconfigurations, or potential security breaches — **without actively blocking any behavior**.

This document explains **why we define syscall-based audit rules**, how they complement tools like `iptables`, `firewalld`, `WireGuard`, and `Suricata`, and the rationale for structuring our rule files into logical modules.

---

## 🎯 Why Use auditd?

While firewall rules or VPN policies can block traffic, they don’t track **who** made changes to the system or **what commands** were executed. `auditd` fills this gap by:

* Logging exactly **which syscall** was executed (e.g. `execve`, `open`, `sethostname`, etc.)
* Recording the **UID**, **timestamp**, **command path**, and **result** of the action
* Allowing post-event analysis of suspicious behavior

This is crucial in both personal and enterprise environments:

* If someone modifies DNS settings → auditd shows when and how
* If WireGuard is disabled manually → auditd records the `wg-quick down` command
* If someone changes hostname, routing, or firewall rules → auditd catches that action

---

## 🧱 Layered Security: How auditd Fits Into the Stack

| Layer                    | Role                                                                                     |
| ------------------------ | ---------------------------------------------------------------------------------------- |
| `iptables` / `firewalld` | Prevents network traffic leaks and blocks unauthorized ports                             |
| `auditd`                 | Records **what actually happened** at syscall level for forensic and compliance purposes |
| `Suricata IDS`           | Inspects packet contents and flags malicious behavior in traffic (e.g. DNS tunnels, C2)  |
| `firejail`               | Restricts application behavior via sandboxing                                            |
| `SELinux`                | Enforces mandatory access control (MAC) on processes and files                           |
| `watchdog script`        | Manually analyzes and summarizes logs from auditd and Suricata                           |

---

## 📁 File Structure & Naming Convention

Audit rules are organized in modular files by function and numbered by order of execution:

| File                     | Purpose                                                               |
| ------------------------ | --------------------------------------------------------------------- |
| `10-core.rules`          | Core syscall monitoring: execve, privilege escalation, module loading |
| `20-shell-profile.rules` | Tracks `.bashrc`, `.profile` changes and /tmp execution               |
| `30-network.rules`       | Monitors hostname/domain changes and IP tools usage                   |
| `31-wireguard.rules`     | (Planned) Watch for wg interface toggling and VPN-related changes     |
| `32-dns-leaks.rules`     | (Planned) Detect tampering in DNS files and resolver configurations   |

Each file focuses on **64-bit architecture only** (`arch=b64`) for performance and clarity.

---

## ✅ Summary

* `auditd` does **not block** anything — it watches, records, and reports
* It’s essential for detecting silent or insider misuse of system tools
* It complements, not replaces, firewalls, VPN rules, and IDS
* Rules are split by purpose to improve maintainability and readability
* Logs collected by `auditd` are parsed manually or via scripts in this project

> By tracing the system's behavior instead of just filtering traffic, `auditd` provides the forensic visibility necessary for true system security.
