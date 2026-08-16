# SSH Hardening Portfolio

## Q1

Ed25519 is considered the best option for SSL key pairs due to its superior levels of security, as well as its smaller key size as compared to the RSA keys leading to efficient work. Ed25519 is usually quicker to generate and use than the RSA keys, while providing strong public authentication services as well. Even though RSA keys are also secure with suitable configuration, Ed25519 is the latest option for SSH implementation.

## Q2

Fail2ban safeguards SSH servers by tracking unsuccessful logins and banning IPs for a period of time. Fail2ban was set up in this case such that `maxretry = 3`, `findtime = 600` and `bantime = 600`. In other words, an IP will be banned after three failed logins within ten minutes for ten minutes.

There is one drawback in fail2ban. It does not substitute for good authentication. As it merely reacts to repeated failed attempts, it is important to ensure that one has secure key-based authentication.

## Q3

Key-only authentication provides a different type of SSH protection than fail2ban. First of all, key-only authentication enables you to eliminate logging in with a password altogether, whereas fail2ban will keep track of repeated failed authentication attempts to ban the IP address. This means that key authentication limits the chances of being attacked by methods that use passwords, while fail2ban takes care of repeated attempts to connect.

## Q4

By using Local port forwarding with the `-L` option, one can make a port on the SSH client available locally as well as route the traffic through the SSH server to another destination. In this case, command:

```
ssh -f -N -L 9090:10.10.1.40:8080 root@10.10.1.20
```

means that localhost:9090 on Admin is forwarded through Bastion to the Internal web server found at 10.10.1.40:8080.

Furthermore, Remote port forwarding with `-R` routes the data in the reverse direction. Remote port forwarding establishes a listening port on the remote SSH machine, which forwards data back to the client side of the SSH connection. This technique allows the remote machine to access services connected to the client.

## Q5

In the Admin-switch capture, it can be observed that Admin uses SSH to communicate with Bastion. Therefore, the web request is safe inside the SSH tunnel, and there is no possibility of reading it. Admin does not have any direct connection with the Internal web server.

The Internal-switch capture illustrates that the HTTP request reaches the Internal server through Bastion. Therefore, on this end, the HTTP request and response can be viewed in plaintext. The Internal server sees that the connection comes from Bastion's address 10.10.1.20. This indicates that there is encryption between Admin and Bastion, and the transmission from Bastion to Internal is done through plain HTTP.
