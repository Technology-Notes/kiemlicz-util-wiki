#Basics
| Term | Description |
|------|-------------|
| Connection | Is a transport providing suitable type of service, connection is transient, associated with **one** session |
| Session | Association between client and server. Created by the handshake protocol. Contains security parameters that are shared between **multiple** connections. They are used to avoid **expensive** negotiation of new security parameters for each connection |
#TLS
##Properties
* Identity exchange via use of public keys (certificates). They’re used to verify counterparts during initialization of communication. Then the session key (symmetric key) is agreed.
* Privacy, data is encrypted via **symmetric** cryptography (key is negotiated)
* Reliability, uses integrity checks via secure hash functions

**All** TLS messages are encrypted, even handshake (but with _NULL_ protocol - so they are plain text).
##Description
TLS is composed of two sub-protocols (layers), identified by _Content Type_ field.
 1. TLS Record Protocol
 2. TLS Handshake Protocol

Privacy and reliability is ensured by lower layer - _TLS Record Protocol_  
Authentication and encryption algorithm negotiation is ensured by upper layer - _TLS Handshake Protocol_
###Handshake protocol
Designed to authenticate peers with each other using asymmetric cryptography (one way authentication is required, mutual is optional)  
Shared secret negotiation (for latter symmetric cryptography)  

Handshake protocol consists of three sub-protocols:

 1. SSL handshake
  ```
  Client sends ClientHello
  Server responds with ServerHello
  Server sends its Certificate and ServerKeyExchange
  Server may request client’s certificate (CertificateRequest)
  Server sends ServerHelloDone
  Client sends its Certificate and ClientKeyExchange 
  Client may send CertificateVerify if the certificate it has sent “had the signing ability” (all certificates            besides ones containing fixed DH parameters). CertificateVerify message contains signature of all sent/received   handshake messages so far by the client. Hash and signature used in computation must be the one of those present   in supported_signature_algorithms (from CertificateRequest). Client creates signature using its private key,   server verifies it using client’s public key.
  Client sends ChangeCipherSpec with cipher it had set as pending. This cipher becomes current.
  Client uses new ciphers to send Finished message
  Server sends its own ChangeCipherSpec
  Server sends Finished message (using new ciphers)
  ```

 2. SSL alert protocol - indicates failure, associated session identifier must be invalidated. May be used to indicate connection end (via Alert(close_notify)).

 3. SSL change cipher - receiver of this message must instruct the record layer to immediately copy the read pending state into current state. Sender of this message must immediately instruct the record layer to copy pending write state to current write state.

###SSL record protocol
