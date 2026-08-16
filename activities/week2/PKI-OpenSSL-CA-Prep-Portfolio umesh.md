# PKI / OpenSSL CA  Activity Preparation Portfolio

**Aim (from the OpenSSL CA lab instructions):
**OpenSSL CA lab instructions: Create a two-tiered Certificate Authority hierarchy, sign a web server certificate and verify the entire trust chain.
---

## Interactive Activity: Identify PKI Components

The "Identify PKI Components" interactive activity was performed prior to the GNS3 lab, and included 8 rounds of certificate fields, key use, chain of trust and true/fals questions.

**Attempt 1 — 6/8 (75%):** Attempt 1 — 6/8 (75%) – the first attempt was flagged for a review, and the message said to review any incorrect answers before progressing to Activity 2.

![Activity 1: Identify PKI Components — first attempt, 6/8](pki-images/Week2_Activity1_PKI_6of8_png.png)

**Attempt 2 — 8/8 (100%):** The second attempt, after re-study of the missed questions, was correct (100%) with feedback confirming good understanding of various aspects of PKI scenarios, certificate errors, and revocation.

![Activity 1: Identify PKI Components — repeat attempt, 8/8](pki-images/PKI_Activity1_8of8_png.png)

---

## OpenSSL CA Lab  Task Overview

The interactive activity above contains the concepts that underlie the lab of the OpenSSL CA on GNS3, which is organized as follows:

1. Build a three-host topology (`CA`, `Server`, `Client`) on a shared switch, addressed on `10.10.1.0/24`.
On CA generate a root CA private key and root self-signed certificate:
3. Make and sign an interim CA certificate with the root CA key on CA.
On `Server`, create a server private key and CSR for `www.<studentid>.lab`, and submit that to the `CA` to be signed using the intermediate CA key.
6. Create a CA chain file (intermediate+root) and put it in /var/lib/ssl/certs folder and configure Nginx on `Server` to use the server certificate, key, and CA chain for HTTPS.
On 'Client', accept the root CA certificate and check 'Client' certificates with ‘openssl verify' and establish a functioning HTTPS connection using ‘curl --cacert'.
Use openssl s_client to check the TLS handshake, and record the HTTPS message on the switch–Server link.
This lab has yet to be run: No terminal evidence (network screenshot, output of openssl verify, output of curl or packet capture) has been produced yet. After this activity is completed that evidence should form the bulk of the portfolio.
---

## Reflection

I found it helpful to do the "Identify PKI Components" activity prior to the lab attempt, as it is designed to be — the first time I did it I got a couple of things mixed up (which fields are part of a certificate vs a CSR, and not knowing about the chain-of-trust scenario), but the second time I did it, I got an 8/8. I think it would be easier to follow this lab than if the user was to jump into the OpenSSL commands without that background first, as it becomes much easier to follow the steps of generating keys, signing CSRs, assembling the chain file when they have that understanding.

## Conclusion

This portfolio reflects the preparatory PKI activity for the OpenSSL CA lab: First attempt 6/8, then corrected reattempt 8/8, after looking at the missed questions. The activity helped students to remember the concepts of certificate-field, key-usage, chain-of-trust, and revocation that they will be practicing in the upcoming lab. The instructions above outline the labs which include building intermediate and root CA, signing a server certificate, setting up Nginx to accept HTTPS, and confirming the CA chain from the client, but these labs have not yet been completed and should be finished and added to this portfolio.
