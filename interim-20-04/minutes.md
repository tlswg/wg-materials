# Minutes

Notetaker: Rich and Peter

## ECHO 

Slides: https://datatracker.ietf.org/meeting/interim-2020-tls-01/materials/slides-interim-2020-tls-01-sessa-echo-slides
Issues: https://github.com/tlswg/draft-ietf-tls-esni/issues

Chris Wood presenting encrypted-hello (formerly encrypted sni)

Biggest change is now an entire ClientHello is encrypted, not just SNI.

See slide p3 for description of how outer and inner hello messages are cryptographically bound together.

ProVerif formal model being developed, no red flags yet.  (Simplified by being TLS 1.3 only; complexified by adding HRR.)

Seven open isues, resolving this main obstacle to review and WGLC

### 209 Padding.  

Discussion of padding length recommendations and max-hosted-namelength vs a "quantizer" (unit of padding length).

Suggestion to pad the name length to a multiple of the name length as opposed to failing the client connection when the name is longer than the max-hosted-namelength.

Jonathan: suggests exponential dropoff as option for the length.

Way forward: tweak PR to address overflow case, merge this, and then look at non-deterministic padding schemes

### 219 ECHOConfig vs. HTTPSSVC. 

Suggestion is ECHO has one public key. DavidBen, BenSchwartz, ekr point out they are different layers

Chris Wood notes that passing an opaque blob between DNS and TLS makes thing much simpler for Apple's stack

Allison asks if its painful to change data if ONE part changes only? (DNSOP thinks atomic updates are better). BenSchwartz points out draft handles old/new ECHOConfig at the same time. Stephen says not as simple as changing encryption curve, but willing to move on and get *a* decision.  Decision to just close 219.
 
### 216 Should servers put CH in HTTPSvc or ECHOconfig?

DNSOP says HTTPsvc relates to inner CH, ECHOconfig is about out CH. Stephen suggests clarifying in the text. Ben agrees, will work with Chris to make PR's against this and HTTPSvc draft.
 
### 218 dependency on HPKE code points.

Stephen: Change TLS to use HPKE codepoints, or the other way round? Fewer IANA registries seem simpler.

RichBarnes: suggests using TLS codepoints in HPKE since MLS for example also use these.

Chris: suggests using TLS codepoints, but point to HPKE.

Stephen: Why was a TLS ciphersuite chosen instead of an AEAD?

Conclusion: use HPKE codepoints everywhere.

### 217 EchoConfigContents.extensions.

Stephen: Thinks the extensions are not a good idea, thinks DNSOP will push back on this. We don't have a known use, just delete them.

Ben thinks it's not going to be a problem.

EKR says it's like httpssvc extensions, having a placeholder is right.

Martin Thomson thinks this extension might be needed for changing padding behavior.

Sean: thinks it is better to close this with no action.  And that's the decision, assuming DNSOP doesn't push back.

### 177 GREASE indistinguishability. My short summary: client needs to grease inner ECHO message, lest things ossify.

David: there is a corner case where DNS/53 is ossified, but the client does support ECHO.

EKR assumes clients will do DoH.  (True for FFox and Chrome)

EKR: threat model 1) people trying to supress ECHO, 2) things breaking due to ossification.

EKR and David agree on the need to flesh out the threat model before moving on this issue. 

Chris says plan is to leave current text re Greasing, and not protect servers against active attacks.  Will revisit after threat discussion review.
 
### 214 Tunneling TLS 1.2 and below. Benefit is 1.2 gets full handshake encryption.

Nick: what is the complexity for the server, it seems similar between 1.2/1.3? Discussion of server-side implementation. Assumes there is a client and fronting server. Once ECHO is removed, the fronting server is essentially a proxy. Use case: fronting server does not know whether TLS 1.2 or 1.3 is supported by the origin server.

Kazuho: [was wondering about middle/origin encryption.  Chris said no, he misled everyone by his framing of the issue.]

Ben Schwarz: what about the ECHO nonce? Chris presumes it is not sent for TLS 1.2.

Does limiting to 1.3 reduce the MTU/packet pressure? (concern for QUIC). Martin answer: it will work, but (multiple packets carrying hello message) probably not well

Jonathan Hoyland: server could send generic cert and then do authenticated exporter. Would client accept that? Trying to prove it is secure with tunneling would be horrible.

Nick: dropping TLS 1.2 means HTTPSVC could specify minimum version.

Chris to create a new issue to capture the issue more accurately and close this.

### Bonus issue: 223 variance in inner vs outer client hello.

Stephen: for simpler interop purposes, require key share, etc. to remain the same?

Goal is to avoid client trial decryption.

Ekr and DavidBen: server should only need to look at the appropriate hello for its purposes. David: clients we don't know enough yet.

Stephen: okay to just close the issue if I'm in the rough.

Chris: let's close, and then revisit as we get more implementation experience.

### Discussion of HPKE dependency.  Discussion of HTTPSSVC dependency (dnsop wants to WGLC that before 108)

Ben: can we get eary code-point allocation? we'll ask the dnsop chairs for info.

Sean: don't remove it, we'll handle the dependencies. 

Chris: will create new PR's, update and do another round of interop by 108.
 
## Guidance for External PSK Usage in TLS

Slides: https://datatracker.ietf.org/meeting/interim-2020-tls-01/materials/slides-interim-2020-tls-01-sessa-external-psk-design-team

Chris Wood, reporting for the design team

(See the slides for background)

Sean: Anyone opposed to taking advice of this group?

Stephen: I don't know; the "use PAKE for low entropy" seems a bit bogus.

To be taken to the list for adopton discussion.

Big design team (12 members); under 10 calls.