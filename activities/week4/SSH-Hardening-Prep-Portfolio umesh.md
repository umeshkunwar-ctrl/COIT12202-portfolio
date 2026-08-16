# SSH Hardening — Activity Preparation Portfolio

**Aim (from the SSH Hardening lab instructions):** Harden an SSH server with Ed25519 key-based authentication, protect it from brute-force login attempts with fail2ban, and use SSH tunnelling to reach an internal service.

---

## Interactive Activity: SSH Fundamentals

An interactive quiz on SSH fundamentals was completed as preparation before starting the GNS3 lab.

![SSH Fundamentals quiz — 7/8](ssh2-images/Screenshot_2026-08-16_115223.png)

**Score: 7/8.** Feedback confirmed a solid grasp of SSH fundamentals, and readiness to move on to the next topic.

---

## SSH Hardening Lab — Task Overview

The quiz above covers the SSH fundamentals underpinning the lab, which is structured as follows per the instructions:

1. Build a four-host topology (`Admin`, `Bastion`, `Server`, `Internal`) on a shared switch, addressed on `10.10.1.0/24`.
2. On `Admin`, generate an Ed25519 key pair and enable key-based login to `Server`.
3. Set up key-based login for the pre-installed `student` account on `Server`.
4. Harden `Server`'s `sshd_config`: disable root login and password authentication, restrict to `student`, and limit authentication attempts, then verify each restriction.
5. Configure fail2ban on `Server` to ban an address after repeated failed login attempts, and trigger a ban from `Bastion`.
6. Enable TCP forwarding on `Bastion`, run a web service on `Internal`, and open a local port forward from `Admin` through `Bastion` to reach it — verified with packet captures on the `Admin`–switch and `Internal`–switch links.

This lab has not yet been run — no terminal evidence (network screenshot, hardened `sshd_config`, fail2ban ban, or the two packet captures) has been produced yet. Once completed, that evidence should be added alongside this activity as the main body of the portfolio.

---

## Reflection

Scoring 7/8 on the SSH Fundamentals quiz suggests the core concepts — key-based versus password authentication, what a bastion/jump host does, and the general shape of an SSH session — are already reasonably solid going into the lab. Missing one question is worth following up on before relying on that concept in the hardening or tunnelling tasks, since a small gap in fundamentals tends to show up later as a confusing error message rather than as an obvious knowledge gap at the time. Having that base in place should make the more fiddly parts of the lab — getting the `sshd_config` directives exactly right, and correctly reasoning about the `-L`/`-R` forwarding direction — easier to reason about rather than just pattern-matching the commands.

## Conclusion

This portfolio currently documents the preparatory SSH Fundamentals activity for the SSH Hardening lab: a strong 7/8 result, with one question to revisit before moving on. The activity reinforced the authentication and connection concepts that the upcoming lab puts into practice through Ed25519 key generation, `sshd_config` hardening, fail2ban, and SSH tunnelling. The lab itself — building the four-host topology, hardening the server, setting up the fail2ban jail, and tunnelling to the internal service — is outlined above from the instructions but has not yet been carried out; the next step is to complete that lab and add its evidence (the network diagram, hardened `sshd_config`, fail2ban ban, and the two packet captures) to this portfolio.
