# Proposed Charter Text

The TLS (Transport Layer Security) working group was established in 1996 to standardize a 'transport layer' security protocol. The basis for the work was SSL (Secure Socket Layer) v3.0 [RFC6101]. The TLS working group has completed a series of specifications that describe the TLS protocol v1.0 [RFC2246], v1.1 [RFC4346], v1.2 [RFC5346], and v1.3 [RFC8446], and DTLS (Datagram TLS) v1.0 [RFC4347] and v1.2 [RFC6347], as well as extensions to the protocols and ciphersuites.

The working group aims to achieve three goals. First, to develop DTLS 1.3, in a way that draws upon the design, analysis, and engineering effort put into TLS 1.3. Specifically, the protocol should exhibit the following features, in no particular order: 

- Encrypt as much of the handshake and datagram packets as possible to reduce the amount of observable data to both passive and active attackers.
- Reduce handshake latency and aim for one roundtrip for a full handshake and one or zero roundtrip for repeated handshakes without compromising current security features.
- Use cryptographic algorithms equivalent to those used in TLS 1.3.

The second working group goal is to improve protocol extensibility, usability, and deployability, e.g., GREASE, Delegated Credentials, Certificate Compression, and Exported Authenticators. These working group items will include a focus on privacy properties of (D)TLS, with a particular emphasis on the following:

- Encrypt the ClientHello SNI (Server Name Indication) and other application-sensitive extensions, such as ALPN (Applications-Layer Protocol Negotiation).
- Identify and mitigate other (long-term) user tracking or fingerprinting vectors enabled by TLS deployments and implementations.
- Consider additional privacy-preserving mechanisms, e.g., consistent application traffic padding.
- Develop privacy-friendly profiles describing recommended usage of (D)TLS for generic use. Protocol-specific profiles are out of scope. 

The third goal is to maintain current and previous version of the (D)TLS protocols as well as to specify general best practices for use of (D)TLS, extensions to (D)TLS, and cipher suites. This includes recommendations as to when a particular version should be deprecated. Changes or additions to older versions of (D)TLS whether via extensions or ciphersuites are discouraged and require significant justification to be taken on as work items. 

With these objectives in mind, the TLS WG will also place a priority in minimizing gratuitous changes to (D)TLS.
