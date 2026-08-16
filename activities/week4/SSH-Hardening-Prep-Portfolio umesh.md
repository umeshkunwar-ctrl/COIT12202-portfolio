# SSH Hardening  Activity Preparation Portfolio

**Aim (from the SSH Hardening lab instructions):** SSH server hardening using Ed25519 keybased authentication, fail2ban and SSH tunnelling to access an internal service.

---

## Interactive Activity: SSH Fundamentals

As a preliminary step before creating the GNS3 lab, an interactive quiz was taken with the subjects of SSH was taken.

![SSH Fundamentals quiz — 7/8](ssh2-images/Screenshot_2026-08-16_115223.png)

**Score: 7/8.** Feedback indicated good understanding of basics of SSH and willingness to proceed to next topic.

---

## SSH Hardening Lab  Task Overview

The quiz above is designed to cover the basics of SSH that are used throughout this lab, and is organized as follows in accordance with the instructions:

Create a 4-host topology (Admin, Bastion, Server, Internal) on a single switch (10.10.1.0/24).
On `Admin`, generate an Ed25519 key pair, and enable key-based login to `Server`.
3. Create a key based login for the pre-installed user called student on Server.
4. Set up the `Server`'s `sshd_config` file to not accept root logins, not accept password logins, only allow login for user `student`, and restrict the number of logins, then test each restriction.
5. Set up fail2ban on Server to block an address after multiple failed logins and enable a fail2ban ban to be triggered by Bastion.
6. Enable TCP forwarding on Bastion, launch a Web service on Internal and open up a local port forwarding on Admin to reach it (confirmed via packet captures on Admin–switch and Internal–switch links).

This lab is not yet run, no terminal evidence is yet available (network screenshot, hardened `sshd_config`, fail2ban ban, or the two packet captures). When this activity is finished, that evidence should be inserted in the body of the portfolio.

---


## Reflection

A score of 7/8 in the SSH Fundamentals quiz means that the concepts of key-based versus password authentication, bastion/jump host and the general form of an SSH session should be fairly solid before beginning the lab activities. Note that it is worth following up on the one question that is not answered even though it is not a mandatory question, as a lack of understanding in the basic steps will be evident later as an error message that is not obvious, and usually has to be traced further to the actual steps of the hardening or tunnelling task. Once you have that base you can think about the more "tricky" parts of the lab, such as getting the `sshd_config` directives to be just right, and figuring out the direction of the `sshd` forwarding, `-L`/`-R`, which is easier to reason about than just pattern matching the commands.

## Conclusion

The following is a portfolio of the preparatory activity of SSH Fundamentals that was used in the SSH Hardening lab: good (7/8) result, but one question to be revisited before proceeding. The activity was meant to refresh the concept of authentication and connection as they will be applied throughout the next lab using `sshd_config` hardening, fail2ban and SSH tunnelling. Building the four-host topology, hardening the server, setting up the fail2ban jail and tunneling to the internal service is described above in the instructions, but has not yet been done; this lab will be completed and all evidence (network diagram, hardened `sshd_config`, fail2ban ban and the two packet captures) supplied and added to this portfolio.
