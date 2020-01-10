# Proposed Charter Text

The TLS (Transport Layer Security) working group was established in 1996 to standardize a 'transport layer' security protocol. The basis for the work was SSL (Secure Socket Layer) v3.0 [RFC6101]. The TLS working group has completed a series of specifications that describe the TLS protocol v1.0 [RFC2246], v1.1 [RFC4346], v1.2 [RFC5346], and v1.3 [RFC8446], and DTLS (Datagram TLS) v1.0 [RFC4347], v1.2 [RFC6347], and v1.3 [draft-ietf-tls-dtls13], as well as extensions to the protocols and ciphersuites.

The working group aims to achieve three goals. First, improve the applicability and suitability of the TLS family of protocols for use in emerging protocols and use cases. This includes extensions or changes that help protocols better use TLS as an authenticated key exchange protocol, or extensions that help protocols better leverage TLS security properties, such as Exported Authenticators. Extensions that focus specifically on protocol extensibility are also in scope. This goal also includes protocol changes that reduce the size of TLS without affecting security. Extensions that help reduce TLS handshake size meet this criteria. 

The second working group goal is to improve security, privacy, and deployability. This includes, for example, Delegated Credentials, Encrypted SNI, and GREASE. Security and privacy goals will place emphasis on the following:

- Encrypt the ClientHello SNI (Server Name Indication) and other application-sensitive extensions, such as ALPN (Application-Layer Protocol Negotiation).
- Identify and mitigate other (long-term) user tracking or fingerprinting vectors enabled by TLS deployments and implementations.

The third goal is to maintain current and previous version of the (D)TLS protocol as well as to specify general best practices for use of (D)TLS, extensions to (D)TLS, and cipher suites. This includes recommendations as to when a particular version should be deprecated. Changes or additions to older versions of (D)TLS whether via extensions or ciphersuites are discouraged and require significant justification to be taken on as work items.

With these goals in mind, the working group will also place a priority in minimizing gratuitous changes to (D)TLS.
