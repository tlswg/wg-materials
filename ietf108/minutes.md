# TLS@IETF108

## Logistics

### Time

Tuesday 2020-07-28 14:10 UTC

### Meeting Information

Meetecho (a/v and chat):
https://gce.conf.meetecho.com/conference/?group=tls&short=&item=1

Audio (only):
http://mp3.conf.meetecho.com/ietf/ietf1086.m3u

Jabber (chat):
tls@jabber.ietf.org

## Agenda

Agenda bash:
Benajmin Kaduk: time for oldversions-deprecate?
    Response: Yes
Draft status
DTLS is next on slate
in queue: ticket requests

Sean: People are anxious to get DTLS out the door, but ok with order

### Administrivia

- Virtual Meeting Tips
- Note Well
- Virtual Bluesheet (automatic)
- Note Taker
- Jabber Scribe

### Drafts

#### TLS Subcerts

Draft: https://tools.ietf.org/html/draft-ietf-tls-subcerts-09

Slides: [Delegated Credentials](https://www.ietf.org/proceedings/108/slides/slides-108-tls-delegated-credentials-00)

Presenter: Nick Sullivan

Discussion:

- 2 last calls

- Several updates to 07

- Created an 09

- No wire changes since 06

- Major comment: test vectors

- Running code: if upload to cloudflare, they'll automatically be used

- Landed in Firefox 78, can enable in settings

- Formal analysis: Hoyland has posted results on the list (slightly stronger model than default)

- Will be more concise write-up

- We're done with this; 10 will address final comments

#### TLS Flags

Draft: https://tools.ietf.org/html/draft-ietf-tls-tlsflags-03

Slides:[TLS Flags](https://www.ietf.org/proceedings/108/slides/slides-108-tls-tls-flags-00)

Presenter: Yoav Nir

Discussion:

- Bitstring of flags, flags indicate whether feature is enabled

- Most common ones will have lower numbers

- Flag specifies whether it goes in CH or EncryptedExtensions

- If client doesn't present flag x, server doesn't respond to that flag

- Don't need >2000 flags

- Post-handshake client auth: already in TLS 1.3, hasn't been added

**Martin Thompson**: concrete use in SH? Wouldn't want feature negotiation in clear
    Simpler if only in EncryptedExtensions

**Eric Rescorla**: NST has immediate use-case: cross-name SNI. Wouldn't need an empty registry

**Benjamin Kaduk**: If don't say you can put it in NST, locking out forever. Would need new flags.
    Hard to implement for SH, consider define to be in SH, but will need good excuse

**Nick Harper**: Are we locked out of putting it in SH? Specify that flag can be in SH

**Ben Schwartz**: not all flag bits are equally good. (Lower number higher value) Better to have different extensions--one for EncryptedExtension, another for other flags

**Joseph Saloway**: resolve on the list about where this can appear

#### TLS 1.3 Extended Key Schedule

Draft: https://tools.ietf.org/html/draft-jhoyla-tls-extended-key-schedule-01

Slides: [TLS Extended Key Schedule](https://www.ietf.org/proceedings/108/slides/slides-108-tls-tls-extended-key-schedule-00)

Presenter: Jonathan Hoyland

Discussion:

- Similar to exporter keys, can use to prove to other protocols that you're a participant

- Use cases: IoT device that doesn't have access to roots, QR code proves ownership of device, this is injected into the handshake

- Authentication properties, PQ ciphersuites

- Why? Generic interface only needs one security analysis

- Would allow us to experiment with PQ

- Multiple injections in single handshake

- Arbitrary limit of # of injections

- Two injection sites: before handshake secret, before master secret

- Most flexible way of doing things

- Every user given a type, strict way of putting in input 

- Could use NKDF

- Interest in making this a working group item?

**Eric Rescorla**: sad about particular way of injecting. Important not to avoid ambiguity, but two Derive-Secrets is goofy

**Jonathan**: if draft-stebila is better, sure

**Eric Rescorla**: How modify key schedule, how glue inputs together. XOR thing seems ok but hard to reason about

**Jonathan**: XOR thing comes with a 40-page proof, but not intuitive

**Eric**: seems like an argument against it

**Richard Barnes**: chaining with derive-secret: why not just HKDF?

**Jonathan**: Proof for HKDFs relies on expand-extract pattern. If other proofs for extract-extract, whatever is secure

**Richard Barnes**: this seems like a conservative option

**Chris Wood**: In context of MLS, use concat and extract rather than XOR (lines up with Douglas)

**Martin**: want to understand the different between this and stebila. Reasonable support for this. Unspecified rumblings about what's in that draft. Is this supplementary?

**Jonathan**: I took one of the designs from that that I liked and made it generic. Supposed to supercede Stebila.

Hums: 

* Result of hum for adoption: forte
* Result of hum against: piano

No one stated why they were against adoption

##### Cross-SNI Resumption

Draft: https://tools.ietf.org/html/draft-vvv-tls-cross-sni-resumption-00

Slides: [Cross-SNI Resumption](https://www.ietf.org/proceedings/108/slides/slides-108-tls-cross-sni-resumption-00)

Presenter: Victor Vasiliev

Discussion:

- Normally, only resume is SNI is the same as original connection

- If you choose to resume, you lose the ticket

- RFC states that you can indicate that resuming with a different SNI is ok

- Currently in empty extension

- Example: connection to a.example.com, cert is for \*.example.com, might try b.example.com

- Not helpful for a lot of setups; already solved for polling

- One deployment: YouTube CDN: if you don't do cross-SNI resumption, you'll never get there.

- Originally this was a PR to TLS 1.3, but decided to postpone this question

- We've discussed this, research showed that this would be helpful for performance

- Interest in adoption?

**Ben Schwartz**: I support this; thought about case where one server is capable of responding for the whole cert, but other servers aren't? Question about whether it's safe for a server to do this. Needs to know about all servers in the world that can respond to any name in cert.

**Victor**: If you have the cert, you have the authority to speak for all servers that have it. Connection attempt would result in failure

**Ian Swett**: support this. Wouldn't anticipate that the handshake will fail.

**Victor**: No, it wouldn't fail

**Daniel Gillmor**: DNS lookups?

**Victor**: This doesn't bypass DNS checks; you're establishing a new connection.

Hums:

* Result of hum in support: forte
* Result of hum against: pianissimo

##### TLS Application Protocol Settings (ALPS)

Draft: https://tools.ietf.org/html/draft-vvv-tls-alps-00

Slides: [ALPS](https://www.ietf.org/proceedings/108/slides/slides-108-tls-alps-00)

Presenter: Victor Vasiliev

Discussion:

- In theory HTTP2, server speaks first.

- In TLS 1.3, can send settings immediately after SH, EncryptedExtensions, Finished

- But in practice this doesn't happen.

- If we do this, clients won't tolerate it.

- Can't send request before you know the settings.

- ALPS solves this problem by moving SETTINGS from application layer to TLS handshake. Don't have to design protocol assuming the settings will be acceptable

- Interaction between TLS and application protocols. If doing 0-RTT, need to have some way of knowing settings of server, have to include in ticket. Standardized way of doing this.

- ALPS copies model of HTTP settings frame; opaque blob

- Client settings are independent from server settings. Can move from second flight to first flight with ECH. In current draft, everything encrypted

- QUIC: client settings weren't encrypted

- 0-RTT: store settings in ticket, reuse if they match. If client decides to use something else, discard ticket

- Problem with h3 design: HTTP interaction messy 

- Settings in QUIC: might not be available when you send session ticket

- Weird synchronization between HTTP and QUIC; this solves that problem

- Just get 0-RTT to work in all situations? We made sure it works in QUIC; make it work in TCP

- Questions about if it'll actually work

- Would need explicit signal in handshake

- May as well go for ALPS rather than fixing 0-RTT

- Easy to define, simple API

- Can't copy QUIC because QUIC designed explicitly around paradigm where you have to work with default settings

**Martin Thompson**: Don't find that arguments in favor of new adventure are justified here
- Just making half-RTT work is superior. Don't want to put this in TLS 1.2. Wait for 0.5 RTT settings, it's do-able.

**Eric Rescorla**: When adding new message to third leg of handshake, looked into this and no one had use for it. Add generic solution rather than go through it twice.

**David Ben**: halfRTT data, connection properties are stable. For first connection, want to be able to send settings early. That's more complicated. Server has requested client certs, need to let them write. Weird deadlocks can happen. Server thinks data is part of handshake but client doesn't. Intentionally punted this.

**Victor**: NSS doesn't implement this for client certs

**Martin**: I have not, intentionally.

**Ben Schwartz**: QUIC wouldn't use this? Thought this was for QUIC

**Martin**: QUIC the transport protocol wouldn't use this.

**Ben Schwartz**: arguments for half-RTT are persuasive enough if this can be done without new handshake alert

**Martin**: most compelling argument is QUIC 

**Daniel Gillmor**: I'm torn because these sound like two different things. How TLS is going to be used by implementers (security dust). Just for H2 settings? Doesn't matter for client certs?

**Martin**: Reasoning about what's safe to send in 0-RTT is hard

**Chris Wood**: Proposal (KEMTLS) for removing ability to do half-RTT data. This would be useful in that context.

**Joseph Salowey**: Sounds like there should be more discussion before we decide on adoption


#### SNIP

Draft: https://tools.ietf.org/html/draft-thomson-tls-snip-00

Slides: [SNIP](https://www.ietf.org/proceedings/108/slides/slides-108-tls-snip-00)

Presenter: Martin Thomson

Discussion:

- Came out of discussion about DNS records.

- ALPN allows compatible protocols. DTLS/TLS not compatible. QUIC incompatible with both; has versions that are incompatible.

- Clients responsible for what they will try. Can't negotiate, but can authenticate outcome. Provide client with all available options.

- 1st bad idea: DNSSEC authenticates what you advertise. Problems: uses different authorities, clients don't get all DNS records at a time. That's a critical feature we can't take away. Might be upgrading a deployment, might be served by multiple CDNs, support different protocols.

- Hard part of draft: divide overall deployment into smaller compartments, e.g. single domain name. If you have one compartment with one protocol and another compartment with another, an attacker can route you towards the less preferred one.

- Truly uniform deployment: full downgrade protection

- Multiple ways to get to a server: A record (how HTTPS today); only identifies a single endpoint. Can't say you also support QUIC. Service B: anything within certain scope share the same configuration.

- Can look in DNS, discovery process will show which services are available. What you get after handshake will give definitive information.

- Draft: TLS extension that lists other protocols that are available.

- Debate about scope of advertisements. Want input from people about how difficult it can be

- Using ALPN identifiers is convenient; limited to one protocol at a time.

**Ben Schwartz**: a problem worth solving. Doesn't apply today, but problem we can care about.

**Martin**: biggest question: mechanism that enables client to fail? Or try to write out a mechanism that enables an upgrade? Assertion that this isn't needed: if you got a QUIC connection to a number of sites, but make a different decision about using it.

**Ben Schwartz**: SVCB draft hazy on this point. Encourage clients to fall back within list of endpoints made available. Client could demand availability.

**Martin**: DNS provider can always deny access to the service

**Eric Rescorla**: More bake time before considering

**Martin**: Need more clear-cut example? E.g. IMAP2 to 3 is more motivating. Rat hole about different name style for things? Just go all in on SVCB? TLS gives a minumum strength on security.

**David Schinazi**: supportive of this; yes to 4 first questions. Don't need right away because fallback considered ok. But this is a simple way to solve this problem

**Carrick Bartle**: What's the security risk from downgrading?

**Martin**: Just don't want attacker to be able to downgrade your service

**Carrick Bartle**: What should the client do if it discovers that it's been downgraded? Should it just try again with the protocol it wanted?

**Martin**: Shut down the connection, don't talk to that server.

**Daniel Gillmor**: One security risk: less protection from metadata in TLS than QUIC
    what should the user do with this info?
    network operator might be interfering with getting the strongest security rating
    QUIC: designed so that if it doesn't work, we'll fall back to TLS
    HSTS: model for ridiculous process; worked across time.
    Used in network diagnostic reports? How does this fix the problem? What problem is this fixing?

**Martin**: Concrete example: specific servers that have QUIC blocked. Can make a decision, make this as strong as we can make it

**Joseph Salowey**: Needs more discussion


#### Benjamin Kaduk

DES: Ciphers removed from 1.2 (essentially don't exist anymore)

RFC 7507 - downgrade attack doesn't make sense if 1.0/1.1 aren't used anymore

Same deprecation document?

**Sean**: The old ciphers are dead as doornails

"You MUST implement TLS 1.0/1.1"; suggested another sentence saying don't do that; additional details about the updates

**Sean Turner**: seems reasonable

Don't have to go into detail; overall summary

OAuth docs: yes, TLS 1.2 is the current version, but 1.0 is more widespread.

**Sean Turner**: They just said that to get through the process

**Ben**: Suggested some language.

10 or so docs: mandatory to implement cipher. Not saying anything about ciphers in this doc; seems correct. If people aren't picking bad MTI ciphers, don't do anything.

**Sean Turner**: if changing base protocol with MTI, natural progression, don't have to specify cipher

**Ben**: Some of them specify MTI cipher in TLS 1.0; not super great writing.

**Joseph Salowey**: could be errata in other specs

Filed errata about FTPS spec, which requires TLS 1.0 even though 1.1 exists


















