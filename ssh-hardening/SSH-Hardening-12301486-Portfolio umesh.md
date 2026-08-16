# SSH Hardening Portfolio — Student ID 12301486

**Aim:** Harden an SSH server with Ed25519 key-based authentication, protect it from brute-force login attempts with fail2ban, and use SSH tunnelling to reach an internal service.

---

## Task 1: Build and address the topology

Four Linux hosts — `Admin` (10.10.1.10/24), `Bastion` (10.10.1.20/24), `Server` (10.10.1.30/24) and `Internal` (10.10.1.40/24) — were connected to a single Ethernet switch on the `10.10.1.0/24` subnet, and each host was addressed with `ip addr add` / `ip link set eth0 up`.

![Network topology](ssh-images/SSH-Hardening-12301486-network.png)

Connectivity was confirmed from `Admin`: `ping 10.10.1.30` and `ping 10.10.1.40` both succeeded once all four hosts were addressed, before SSH was configured on `Server`.

![Admin addressing and initial connectivity test](ssh-images/SSH-Hardening-12301486-network_png_1_.png)

---

## Task 2: Generate a key pair and enable key-based login

An Ed25519 key pair was generated on `Admin` with `ssh-keygen -t ed25519`, and `sshd` was started on `Server` with `/bin/start-ssh.sh`. The public key was then copied to `Server` with `ssh-copy-id`, confirmed by the `Number of key(s) added: 1` message, and a key-based login as `root` succeeded with no password prompt.

---

## Task 3: Set up key login for the pre-installed `student` user

The pre-installed `student` account's `.ssh` directory was inspected on `Server`, then the `Admin` public key was added to it with `ssh-copy-id -i ~/.ssh/id_ed25519.pub student@10.10.1.30`. Logging in as `student@10.10.1.30` from `Admin` succeeded with no password prompt.

![ssh-copy-id and student key login succeeding](ssh-images/SSH-Hardening-12301486-ssh-success.png)

---

## Task 4: Harden the SSH server configuration

The four hardening directives were set in `/etc/ssh/sshd_config` on `Server`:

```
PermitRootLogin no
PasswordAuthentication no
AllowUsers student
MaxAuthTries 3
```

`sshd` was reloaded with `kill -HUP $(pgrep sshd)`, and the running configuration was confirmed with `sshd -T | grep -E 'permitrootlogin|passwordauthentication'`.

![Ed25519 key generation and the hardened sshd_config directives](ssh-images/SSH-Hardening-12301486-sshd.png)

While editing `sshd_config`, an invalid directive (`PPermitRootLogin`) caused `sshd -t` to fail with a configuration error; this was fixed with `sed`, and a follow-up `Missing privilege separation directory` error was resolved by creating `/run/sshd`, after which `sshd -t` passed cleanly.

![Troubleshooting an sshd_config syntax error and privilege separation directory](ssh-images/SSH-Hardening-12301486-config-test.png)

All three restrictions were then verified from `Admin`: key login as `student` still worked, a forced password attempt (`-o PubkeyAuthentication=no`) was refused, and root login was refused.

![Verifying key-only login and root login refusal](ssh-images/SSH-Hardening-12301486-root-disabled.png)

---

## Task 5: Block brute-force attempts with fail2ban

`syslogd` was started on `Server` so that failed logins are recorded, and a `jail.local` override was created with `maxretry = 3`, `findtime = 600`, `bantime = 600`. After `fail2ban-client start`, repeated failed logins against `Server` (as non-existent users) were made from `Bastion`, and the jail status confirmed `Bastion`'s address, `10.10.1.20`, listed under `Banned IP list`.

![fail2ban status showing 10.10.1.20 banned](ssh-images/SSH-Hardening-12301486-fail2ban.png)

---

## Task 6: Tunnel to the internal web service through the bastion

`AllowTcpForwarding yes` was set in `sshd_config` on `Bastion` and reloaded, and the `Admin` public key was installed on `Bastion` with `ssh-copy-id`. On `Internal`, a page was served on port 8080 with `python3 -m http.server 8080 -d /var/www`; the access log shows the `GET / HTTP/1.1 200` requests arriving from `10.10.1.20` (the bastion), not from `Admin` directly.

![Internal host serving the web page on port 8080](ssh-images/SSH-Hardening-12301486-internal_pcap.png)

From `Bastion`, connectivity and routing to `Internal` were confirmed, and a direct `curl http://10.10.1.40:8080/` returned the `Internal Server` page.

![Bastion routing and direct curl to Internal](ssh-images/Screenshot_2026-08-15_202532.png)

On `Admin`, a local port forward was opened through the bastion:

```
ssh -f -N -L 9090:10.10.1.40:8080 root@10.10.1.20
```

`curl http://localhost:9090/` then returned the `Internal Server` page — proving the forwarded local port, not a direct connection, was carrying the traffic.

![Opening the tunnel from Admin and testing curl localhost:9090](ssh-images/Screenshot_2026-08-15_202729.png)

![Tunnel re-established and re-tested with curl localhost:9090](ssh-images/SSH-Hardening-12301486-admin_pcap.png)

Packet captures were taken on both the `Admin`–switch and `Internal`–switch links while the forward was tested, matching the analysis in Q5 below.

---

## Questions

### Q1: Why is Ed25519 recommended over RSA for new SSH key pairs?

Ed25519 is considered the best option for SSL key pairs due to its superior levels of security, as well as its smaller key size as compared to the RSA keys leading to efficient work. Ed25519 is usually quicker to generate and use than the RSA keys, while providing strong public authentication services as well. Even though RSA keys are also secure with suitable configuration, Ed25519 is the latest option for SSH implementation.

### Q2: How does fail2ban defend an SSH server, and what is one limitation of relying on it alone?

Fail2ban safeguards SSH servers by tracking unsuccessful logins and banning IPs for a period of time. Fail2ban was set up in this case such that `maxretry = 3`, `findtime = 600` and `bantime = 600`. In other words, an IP will be banned after three failed logins within ten minutes for ten minutes.

There is one drawback in fail2ban. It does not substitute for good authentication. As it merely reacts to repeated failed attempts, it is important to ensure that one has secure key-based authentication.

### Q3: How do key-only authentication and fail2ban complement each other?

Key-only authentication provides a different type of SSH protection than fail2ban. First of all, key-only authentication enables you to eliminate logging in with a password altogether, whereas fail2ban will keep track of repeated failed authentication attempts to ban the IP address. This means that key authentication limits the chances of being attacked by methods that use passwords, while fail2ban takes care of repeated attempts to connect.

### Q4: What is the difference between local (`-L`) and remote (`-R`) port forwarding?

By using local port forwarding with the `-L` option, one can make a port on the SSH client available locally as well as route the traffic through the SSH server to another destination. In this case, command:

```
ssh -f -N -L 9090:10.10.1.40:8080 root@10.10.1.20
```

means that localhost:9090 on Admin is forwarded through Bastion to the Internal web server found at 10.10.1.40:8080.

Furthermore, remote port forwarding with `-R` routes the data in the reverse direction. Remote port forwarding establishes a listening port on the remote SSH machine, which forwards data back to the client side of the SSH connection. This technique allows the remote machine to access services connected to the client.

### Q5: Compare the two `.pcap` files — what is readable on each link, and why?

In the Admin-switch capture, it can be observed that Admin uses SSH to communicate with Bastion. Therefore, the web request is safe inside the SSH tunnel, and there is no possibility of reading it. Admin does not have any direct connection with the Internal web server.

The Internal-switch capture illustrates that the HTTP request reaches the Internal server through Bastion. Therefore, on this end, the HTTP request and response can be viewed in plaintext. The Internal server sees that the connection comes from Bastion's address 10.10.1.20. This indicates that there is encryption between Admin and Bastion, and the transmission from Bastion to Internal is done through plain HTTP.

---

## Reflection

This activity gave me a practical, hands-on understanding of SSH hardening rather than just a theoretical one. Generating the Ed25519 key pair and getting ordinary key login working first, before restricting anything, made it much easier to tell later on whether a failure was caused by the hardening itself or by a broken key — the troubleshooting around the `PPermitRootLogin` typo and the missing `/run/sshd` privilege separation directory showed how easily a single configuration error can stop `sshd` from starting at all, and how useful `sshd -t` is for catching that before reloading the service.

Setting up fail2ban made the difference between authentication and rate-limiting clearer to me: key-only login stops password-guessing outright, while fail2ban only reacts after repeated failures, which is a meaningfully weaker guarantee on its own. Seeing the banned IP appear in `fail2ban-client status sshd` after the scripted failed logins from Bastion made that reactive behaviour concrete rather than abstract.

The tunnelling task tied the whole activity together. Watching the same web request look completely different on the two packet captures — encrypted SSH on the Admin–switch link, and plaintext HTTP on the Internal–switch link — was the clearest demonstration in the whole exercise of exactly where the SSH tunnel's protection starts and stops: it secures the path to the bastion, not the bastion's own onward connection.

## Conclusion

This activity successfully hardened an SSH server using Ed25519 key-based authentication, restricted to a single account with root login and password authentication disabled, and backed by fail2ban to automatically ban addresses making repeated failed login attempts. The SSH tunnel task then demonstrated how a bastion host can expose an internal-only service without that service ever being reachable directly, and the packet captures confirmed that the protection SSH provides is limited to the encrypted leg of the connection — traffic beyond the bastion remains only as secure as the protocol carrying it, which in this case was plain HTTP. Together, the six tasks met the aim of the activity: a hardened SSH server, automated brute-force protection, and a working, verified tunnel to an internal resource.
