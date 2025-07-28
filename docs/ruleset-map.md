# 🧭 RuleSet Threat Mapping - auditd (arch-secure-admin)

This document provides a strategic overview of the syscall-based auditd rules in the `arch-secure-admin` project. It maps each rule to its corresponding threat model, syscall or file target, and provides operational context for manual log review, incident detection, and system forensics.

---

## 📊 Rule Mapping Table

| Threat Description            | Monitored Action / Target                          | Source / Risk Origin       | RuleSet File                             | Tag (`-k`)                    |
| ----------------------------- | -------------------------------------------------- | -------------------------- | ---------------------------------------- | ----------------------------- |
| Privilege escalation attempt  | `setuid`, `setgid`, `setreuid`, ... syscalls       | Malicious binary or script | `10-core.rules`                          | `priv_esc`                    |
| Unauthorized binary execution | `execve` syscall from UID 0 or UID ≥ 1000          | Root user or local user    | `10-core.rules`                          | `exec_root`, `exec_user`      |
| Stealth backdoor persistence  | Write to `.bashrc`, `.profile`, `.bash_logout`     | Local user or malware      | `20-shell-profile.rules`                 | `shell_rc`                    |
| Malware runtime in /tmp       | `execve` syscall in `/tmp`, `/dev/shm`, `/var/tmp` | Fileless malware, droppers | `20-shell-profile.rules`                 | `tmp_exec`                    |
| Hostname/domainname spoofing  | `sethostname`, `setdomainname` syscalls            | Root user, rogue script    | `30-network.rules`                       | `netcfg_change`               |
| DNS resolver tampering        | Write to `/etc/resolv.conf`                        | Manual or automated tamper | `30-network.rules`, `32-dns-leaks.rules` | `netcfg_change`, `dns_tamper` |
| Network tool misuse           | Execution of `ip`, `ifconfig`, `iptables`          | User with elevated access  | `30-network.rules`                       | `netcfg_tool`                 |
| VPN deactivation / bypass     | Execution of `wg`, `wg-quick`, `nmcli` commands    | User/script disabling VPN  | `31-wireguard.rules`                     | `wireguard`                   |
| WireGuard config modification | Write to `/etc/wireguard/wg0.conf`                 | Root / malicious change    | `31-wireguard.rules`                     | `wireguard_config`            |
| Routing manipulation          | Syscalls: `clone`, `unshare`, `setrlimit`          | Advanced evasion script    | `31-wireguard.rules`                     | `wireguard_route`             |
| DNS-over-HTTPS override (DoH) | Edit Firefox/Chrome DoH configuration files        | User extension or malware  | `32-dns-leaks.rules`                     | `doh_attempt`                 |
| DNS tool abuse (leak testing) | Exec of `dig`, `nslookup`, `curl`, `wget`, etc.    | User/network exfiltration  | `32-dns-leaks.rules`                     | `dns_tools`                   |

---

## 🧠 Usage

* Each tag (`-k`) can be used with `ausearch -k <tag>` to filter relevant events
* This mapping helps prioritize alerts, identify misuse patterns, and correlate low-level actions with higher-level risks
* Especially useful in air-gapped or VPN-only environments where trust boundaries are enforced strictly

> "You can't stop what you can't see — auditd sees everything."
