# OpenSSL CA Portfolio — Student ID 12301486

## Evidence

### Network topology

Three hosts are running on Linux (`CA`, `Server`, `Client`) on the same subnet `10.10.1.0/24` and connected by the use of a switch.

![Network topology](images/network-topology.png)

### Certificate verification and HTTPS request

The chain is confirmed as being OK with openssl verify, and the curl HTTPS request to the server also succeeds with the --cacert option.

![openssl verify and curl output](images/verify-and-curl.png)

### TLS handshake inspection (`openssl s_client`)

The output from openssl s_client examining the certificate chain and session information given during TLS handshake.

![openssl s_client TLS handshake output](images/tls-handshake-sclient.png)

### Packet capture point
Capture point on the link between the switch and the `Server` host, used to capture the TLS handshake and HTTPS traffic.

![Packet capture point on the switch-server link](images/packet-capture-point.png)

## Questions

### Q1. Purpose of an intermediate CA

In the middle of the root CA and the server certificate you'll find an intermediate CA. The CA certificate signed by the root CA can be used by the intermediate CA to sign certificates for the server. This is deemed to be a better practice because the private key of the root CA can be safeguarded against prying pairs. The intermediate CA can be revoked without risk to the private key of the root CA if the intermediate CA is breached.


### Q2. What `openssl verify` checks

To check if the server certificate is trustworthy through the chain of certificates to the trusted root certificate authority, we will use the command 'openssl verify'. The algorithm validates the certificates in the certificate chain and the relationships between certificates. If there is no intermediate certificate, then the client would not be able to verify the trust chain from the server's certificate to the root certificate authority, and verification would fail.

### Q3. In some cases application data is not shown in the capture.Sometimes application data is not displayed in the capture.

After its connection is secured, HTTP application data is safe and protected via TLS encryption. Only handshake information is shown in the packet capture, which means that you will not be able to see information about the connection itself or the certificates. But the actual HTTP body of the request and response is encrypted in the packets.



### Q4. Self-signed vs. CA-signed certificates

The entity that issues a certificate signs the certificate itself, as opposed to a certificate authority. These certificates are beneficial for testing or development and in private cases where they can be manually trusted.

A certificate signed by a trusted Certificate Authority (CA) is a part of a certificate chain. These certificates are more suitable for production site and services, where client can validate these certificates using trusted certificate authorities.

## Reflection

This activity was very helpful to me to develop a better understanding of the certificate chain of trust, not just in theory, but in practice. Once the root CA has been set up, and the intermediate CA has been set up next, it was obvious that the private key of the root CA must be touched only once, to sign the intermediate certificate, and can then be stored offline, whereas the intermediate CA does the day-to-day signing of server certificates. The concept of a "trust chain" became very real as the server CSR was generated, signed with the intermediate key and assembled into the chain file in proper leaf-to-root order was made simple and easy to understand — one could see that a chain file with a misplaced certificate or wrong order would cause verification to fail on the client.


Nginx configuration to serve the full chain and then checking from the client with openssl verify, openssl s_client and openssl curl --cacert were used to tie everything together. Capturing traffic on the switch-server link was another reason for TLS to be important: The handshake and certificate exchange was captured and visible, but the actual content of the HTTP request and response was not, as this is the whole point of the encryption.

## Conclusion

This exercise involved the creation of both the root and intermediate keys, server certificate issuance and deployment, as well as checking the resulting trust chain end-to-end for a full two-tier CA hierarchy. The intermediate CA certificate was in the client's trust store and the client successfully trusted the server's certificate, as verified by the openssl verify and curl results, and the packet capture showed that the TLS handshake was in clear text, but the HTTP content was still encrypted. Overall, this exercise was successful in establishing and testing a functioning CA hierarchy structure, and demonstrated how common it is for intermediate CAs to be used in a real PKI deployment, a correct ordered certificate chain, and SANs.
