# PKI / OpenSSL CA — Activity Preparation Portfolio

**Aim (from the OpenSSL CA lab instructions):** Build a two-tier Certificate Authority hierarchy using OpenSSL, sign a web server certificate, and verify the full trust chain.

---

## Interactive Activity: Identify PKI Components

Before starting the GNS3 lab, the "Identify PKI Components" interactive activity was completed as preparation — 8 rounds covering certificate fields, key usage, chain of trust, and true/false questions.

**Attempt 1 — 6/8 (75%):** the first attempt was flagged for review, with the message to check incorrect answers before moving on to Activity 2.

![Activity 1: Identify PKI Components — first attempt, 6/8](pki-images/Week2_Activity1_PKI_6of8_png.png)

**Attempt 2 — 8/8 (100%):** after reviewing the missed questions, a repeat attempt scored full marks, with feedback confirming a solid understanding of PKI scenarios, certificate errors, and revocation mechanisms.

![Activity 1: Identify PKI Components — repeat attempt, 8/8](pki-images/PKI_Activity1_8of8_png.png)

---

## OpenSSL CA Lab — Task Overview

The interactive activity above covers the PKI concepts underpinning the OpenSSL CA GNS3 lab, which is structured as follows per the instructions:

1. Build a three-host topology (`CA`, `Server`, `Client`) on a shared switch, addressed on `10.10.1.0/24`.
2. On `CA`, generate a root CA private key and self-signed root certificate.
3. On `CA`, generate and sign an intermediate CA certificate using the root CA key.
4. On `Server`, generate a server private key and CSR for `www.<studentid>.lab`, and have the `CA` sign it with the intermediate CA key.
5. Assemble a CA chain file (intermediate + root) and configure Nginx on `Server` to serve HTTPS with the server certificate, key, and chain.
6. On `Client`, trust the root CA certificate, then verify the chain with `openssl verify` and confirm a working HTTPS connection with `curl --cacert`.
7. Inspect the live TLS handshake with `openssl s_client`, and capture the HTTPS request on the switch–`Server` link.

This lab has not yet been run — no terminal evidence (network screenshot, `openssl verify` output, `curl` output, or packet capture) has been produced yet. Once completed, that evidence should be added alongside this activity as the main body of the portfolio.

---

## Reflection

Doing the "Identify PKI Components" activity before starting the lab was useful in exactly the way it's meant to be — the first attempt at 6/8 surfaced a couple of gaps (mixing up which fields belong to a certificate versus a CSR, and being unsure about a chain-of-trust scenario), and going back over those before the repeat attempt turned that into a clean 8/8. Getting that conceptual grounding sorted first — what a root CA, an intermediate CA, and a leaf certificate actually are, and how a chain of trust is verified — should make the practical OpenSSL steps in the lab (generating keys, signing CSRs, assembling the chain file) much easier to follow than jumping straight into the commands without that context.

## Conclusion

This portfolio currently documents the preparatory PKI activity for the OpenSSL CA lab: an initial attempt at 6/8, followed by a corrected repeat attempt at 8/8 after reviewing the missed questions. The activity reinforced the certificate-field, key-usage, chain-of-trust, and revocation concepts that the upcoming lab puts into practice. The lab itself — building the root and intermediate CA, signing a server certificate, configuring Nginx for HTTPS, and verifying the chain from the client — is outlined above from the instructions but has not yet been carried out; the next step is to complete that lab and add its evidence (network diagram, `openssl verify` output, `curl` output, and the packet capture) to this portfolio.
