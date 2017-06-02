# Basics
| Term | Description |
|------|-------------|
| Connection | Is a transport providing suitable type of service, connection is transient, associated with **one** session |
| Session | Association between client and server. Created by the handshake protocol. Contains security parameters that are shared between **multiple** connections. They are used to avoid **expensive** negotiation of new security parameters for each connection |
# TLS
Protocol directly _above_ layer 4 ISO/OSI. Uses reliable transport **only** (TCP in general).  
Main goal of TLS is to provide secure connection between parties.

Properties:  
* Identity exchange via use of public keys (certificates). They’re used to verify counterparts during initialization of communication. Then the session key (symmetric key) is agreed
* Privacy: data is encrypted via **symmetric** cryptography (key is negotiated during [TLS Handshake Protocol](https://github.com/kiemlicz/util/wiki/SSL#handshake-protocol))
* Reliability, uses integrity checks via secure hash functions

**All** TLS messages are encrypted, even handshake (but with _NULL_ protocol - so they are plain text).
## Description
TLS is composed of two sub-protocols (layers), identified by _Content Type_ field.
 1. TLS Record Protocol (encapsulates handshake protocol)  
 2. TLS Handshake Protocol

Privacy and reliability is ensured by lower layer - _TLS Record Protocol_  
Authentication and encryption algorithm negotiation is ensured by upper layer - _TLS Handshake Protocol_

### Handshake protocol
Designed to authenticate peers with each other using asymmetric cryptography (one way authentication is required, mutual is optional)  
Shared secret negotiation (for latter symmetric cryptography) - in general: session negotiation  

Handshake protocol consists of three sub-protocols:

 1. Handshake Protocol  
  Creates the session (context of whole communication)
  ```
  Client sends ClientHello
  ----------------------->
  
  Server responds with ServerHello
  <-----------------------
  ```
  At this point both sides have established following parameters:
    - ProtocolVersion
    - SessionID
    - CipherSuite 
    - CompressionMethod
  ```
  Server sends its Certificate and ServerKeyExchange
  <-----------------------
  ```
  Optional
  ```
  Server may request client’s certificate (CertificateRequest)
  <-----------------------
  ```
  Optional
  ```
  Server sends ServerHelloDone
  <-----------------------
  ```
  ```
  Client sends its Certificate and ClientKeyExchange 
  ----------------------->
  ```
  ```
  Client sends CertificateVerify 
  ----------------------->
  ```
  Optional, send if the certificate that client has sent “had the signing ability” (all certificates besides ones containing fixed DH parameters). _CertificateVerify_ message contains signature of all sent/received handshake messages so far by the client. Hash and signature used in computation must be the one of those present in supported_signature_algorithms (from _CertificateRequest_). Client creates signature using its private key, server verifies it using client’s public key.
  ```
  Client sends ChangeCipherSpec 
  ----------------------->
  ```
  With cipher it had set as pending. This cipher becomes current (explained in Change Cipher sub-section)
  ```
  Client sends Finished
  ----------------------->
  ```
  Uses new ciphers to send _Finished_
  ```
  Server sends its own ChangeCipherSpec
  <-----------------------
  Server sends Finished message (using new ciphers)
  <-----------------------
  ```

 2. Alert Protocol  
  Indicates failures, associated session identifier must be invalidated. May be used to indicate connection end (via _Alert(close_notify)_).

 3. Change Cipher  
  Receiver of this message must instruct the _Record Layer_ (_Record Protocol_) to immediately copy the read pending state into current state. Sender of this message must immediately instruct the record layer to copy pending write state to current write state.

### Record Protocol
Compression/decompression, division into blocks, reassembly.  
Used by Handshake Protocol.  
Maintains connection state - encryption algorithm, compression algorithm and MAC algorithm.  
Receiving unexpected record type results in _Alert(UnexpectedMessage)_.  
Contains information about compression, MAC and encryption for: 
 - current read/write states 
 - pending read/write states

_Current_ are used for record processing.  
To become current:
 1. the pending is first agreed upon in Handshake Protocol
 2. the change cipher spec message makes it current

# References
 1. [RFC handshake flow](https://tools.ietf.org/html/rfc5246#section-7.3)
 2. [RFC CertificateVerify details](https://tools.ietf.org/html/rfc4492#section-5.8)
 3. [DTLS RFC](https://tools.ietf.org/html/rfc6347)