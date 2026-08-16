![](media/image1.png){width="6.268055555555556in"
height="5.384722222222222in"}

![](media/image2.png){width="6.268055555555556in"
height="6.711111111111111in"}

![](media/image3.png){width="6.268055555555556in"
height="5.418055555555555in"}

![](media/image4.png){width="6.268055555555556in"
height="5.111805555555556in"}

**Q1. An intermediate CA serves as a different certificate authority
amid the root CA and server certificate. The root CA provides a
signature to the intermediate CA certificate, after which the latter can
sign server certificates. This is considered to be a better practice as
the private key of a root CA can be kept safe from prying eyes. If the
intermediate CA is breached, it can be revoked without putting the root
CA private key at risk.**

**Q2. The command openssl verify is used to verify if the server
certificate is trustworthy via the chain of certificates leading to the
trusted root certificate authority. The algorithm verifies the
signatures and relationships in the certificate chain. Without the
intermediate certificate, typically, it would not be possible for the
client to create a full trust chain from the server\'s certificate up to
the root certificate authority, making the verification of the
certificates fail.**

Q3. Once a secured connection is formed, HTTP application data becomes
safe and protected through TLS encryption. The packet capture displays
only handshake information, disclosing details of the connection itself
as well as the certificates. However, when it comes to the actual
request and response of HTTP being captured in packets, these pieces of
information are encrypted.

Q4.A self-signed certificate is authenticated by the entity that created
the certificate instead of a recognized certificate authority. Such
certificates can be helpful while testing or developing and in private
cases where they are trusted manually.

A CA-signed certificate is issued by a trusted certificate authority and
is part of the certificate chain. Such certificates are better suited
for use at production sites and services since they can be verified by
clients through trusted certificate authorities.
