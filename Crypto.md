# Certificates
Asymmetric, public key cryptography using trusted institution certifying ownership of public key.

Setting key-pair (with self-signed CA) for server consists of following steps:

1. create private key for CA
2. create self-signed CA cert
3. create private key for server
4. create csr (certificate signing request) for server
5. sign csr using CA

Multiple parameters asked during certificate creation can be specified using config files:
https://www.openssl.org/docs/manmaster/apps/config.html
