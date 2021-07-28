TLS @ IETF 111

## WG draft updates

- [cTLS](https://datatracker.ietf.org/doc/draft-ietf-tls-ctls/)
- [Hybrid Key Exchange](https://datatracker.ietf.org/doc/draft-ietf-tls-hybrid-design/)
- [Encrypted Client Hello](https://datatracker.ietf.org/doc/draft-ietf-tls-esni/)

## New Work / Presentations

- [Deprecating Obsolete Key Exchange Mechanisms in TLS](https://datatracker.ietf.org/doc/draft-aviram-tls-deprecate-obsolete-kex/)
- [Auth-KEM](https://datatracker.ietf.org/doc/draft-celi-wiggers-tls-authkem/)
- [Bootstrapped TLS Authentication](https://datatracker.ietf.org/doc/html/draft-friel-tls-eap-dpp-03)
- [Secure Negotiation of Incompatible Protocols in TLS](https://datatracker.ietf.org/doc/draft-thomson-tls-snip/)

---
## Minutes

Notes: Jonathan Hoyland

Jabber: Rich Salz

### DTLS 1.3

Ekr: John Mattson said that the epoch number is too short. 

Soltn is either extend the fixed part of the AEAD or truncate it.

### [cTLS](https://datatracker.ietf.org/doc/draft-ietf-tls-ctls/)

Chris Patton: Do people plan to put this in the same stack as their standard TLS stack?

Ekr: Consider it a compression layer below TLS, so in that case part of the same stack. 

Ekr: Will remove the varint encoding.

MT: Is the proposal (semi-static slide #4) to make a cTLS extension?

Ekr: No, new cipher suite.

DavidBen: New NamedGroup code points?

Ekr: Yes.

JGH: Don't we require the record layer security for Post Handshake Auth

Ekr: Don't believe so, and this removes the Finished, not the AEAD. We shouldn't be hasty. We should check.

Ben Schwartz: Could this reasonably be done as a follow-up.

Ekr: Maybe / probably?

CP: I support this.


### [Hybrid Key Exchange](https://datatracker.ietf.org/doc/draft-ietf-tls-hybrid-design/)


Ekr: We should open last call now. Call on the list, put in what gathers interest. We have lots of code points to play with.

Jonathan Lennox: Does this only work for the pairing of classical and post-quantum algorithms, or could it also work for something like some government requiring their national algorithms?

Doug Stebilla: Can be any two algorithms.

SPT: We need to be clear that if we pick anything, we are not annointing a winner.

Sftcd: It might be worth thinking about having some GREASE-like code points that we can be sure will never be part of the final answer.

Nick Sullivan: +1 SPT, use existing code points i.e. combine existing definitions for testing.

Ekr: Just pick some popular ones and put them in as "Recommended: N"

Ludovic Perret: What is the strategy between this and KEMTLS

DS: This gives confidentiality / PQ PFS. AuthKEM and KEMTLS provide PQ Auth.

Watson Ladd: We should issue experimental code points.

Sftcd: +1

HKE should go to last call

### [Encrypted Client Hello](https://datatracker.ietf.org/doc/draft-ietf-tls-esni/)

SPT: How long do you want to let it bake for?

CAW: Unclear, let's revisit at the next IETF?

Martin Thomson: No, Yes

Ekr: Don't care, let's just get on with it. No, No / whatever the draft is closest to now.

Carrick: Don't care. Do we think middleboxes are going to freak out if the SH has this extension, or freak out more if HRR does? We just don't have enough info. It's the middleboxes problem if they can't handle it. 

DavidBen: Just do the minimal change. ATM Client is No, Yes, Server is No, No. We should pick one, or not.

CAW: I vote for minimum changes.

Ekr: No one seems to care, let's empower the editors to pick one. We guaranteed problems

CP: Middlebox problems or inter-op problems.

Ekr: Inter-op, then middlebox.

Ekr: Punt this, discuss later.

DavidBen: Definitely agreed that the interop/experiment target need not (and probably should not) include this stuff.

Carrick: I'm ok with punting it, but if we're going to have to do eventually why not do it now?

Ekr: The reason to punt is because we're not sure of the best way to do it.

ChrisP: Both the current proposals either use an extension or define a new handshake message. Both make sense in a self-contained document that ECH will rely on, so we should wait for that.

CAW: This will give us time to figure out what the write way to do this.

SPT: Let's go with the proposal and move on. 

CAW: Maybe after we've cut the inter-op version we could spin up a design team for people who care.

Ekr: Not likely to languish, it's in the issue tracker.

Carrick: If we don't do it now, might it be harder to do later?

CAW: Unclear.

Sftcd: I think the extensions are useless.

Sftcd: Are we going to WGLC on -13 as well as -13 inter-op target?

Ekr: What would the last call decide about all the issues we just punted on?

SPT: We can do a WGLC with carve-outs for the issues we just punted on (so not the final WGLC?). Will share it with those involved to date, prior to sending to WG, to avoid any unnecessary flare ups.

### [Deprecating Obsolete Key Exchange Mechanisms in TLS](https://datatracker.ietf.org/doc/draft-aviram-tls-deprecate-obsolete-kex/)

Viktor Dukhovni: I run PostFix, has a compiled in fixed group. It'll take 5 years to get significant adoption. If clients stop understandeing FFDHE there'll be interop issues. Customers generate their own groups.

NA: Can we collect metrics? 

VD: I don't have the resources to do that, perhaps gmail could collate and release that data?

CB: Why 5 years, rather than 3 or 10?

VD: People update SMPT infrastructure less frequently than browsers.

DB: In TLS 1.2 you can only specify DHE or not, but not require only well-known groups.

NA: Will this cause a lot of interop problems.

DB: Chrome just droppped DHE altogether because we couldn't fix this. 

Scott Fluhrer: Could accept server proposed groups, but check on the client if they're safe primes.

Rich Salz: We should do the right thing, and just leave those niche communities who can't upgrade to their own devices. No one is required to be RFC compliant. 

Ben Kaduk: +1 Rich Salz

Chris Patton: +1 Rich Salz

Joe Salowey: Let's do a call for adoption after the meeting?

### [Auth-KEM](https://datatracker.ietf.org/doc/draft-celi-wiggers-tls-authkem/)

Viktor: SMTP the server speaks first, but isn't latency sensitive, so that's fine.

Ekr: We're 5 years (at least) from PQ sigs being useful in a significant way. This is a big change, for a modest improvement. We need a PQ PKI first. This is interesting, but isn't worth doing, yet?

ChrisP: +1 the idea. It's the dream of OPTLS, but they didn't use the right abstraction. 

BenSchwartz: The standard handshake gives forward secrecy, what is guarantee here? Someone who has the server's LTK the server can decrypt the handshake. 

Thom Wiggers: The attacker would need both the LTK and the ephemeral secret to compute the key. 

DavidBen: I'm concerned about authenticating the server's request to the client. Client certificate decisions can result in interesting side effects, like unlocking smartcards or prompting the user. Having something so visible not be authenticated is pretty scary.

Jabber suggests leaving this to TLS 1.4 (or 1.5, 1.6, 9, 11, and so on...)


### [Bootstrapped TLS Authentication](https://datatracker.ietf.org/doc/html/draft-friel-tls-eap-dpp-03)

Ben Kaduk: Why do you need the extended PSK?

OF: The extended PSK allows you to add a type to the PSK, and given that we're deriving the PSK from the BSK we thought we should make that explicit.

Ekr: This seems more along the lines of what I was thinking. Could you speak to what you expect the outcome here to be?

OF: We got so much feedback last time we wanted to bring it back. Will advance here in EMU.

JS: Will be discussed in EMU tomorrow.

DavidBen: Is there a reason you need to authenticate the server on the basis of secret public key?

OF: This is the way the WiFi Alliancae DPP protocol works.


### [Secure Negotiation of Incompatible Protocols in TLS](https://datatracker.ietf.org/doc/draft-thomson-tls-snip/)

Ekr: In favour, clarifying question, do all anycast need to have the same config.

MT: If you're doing anycast then all anycast has to have the same configuration.

BS: Propose adoption.

RS: Can you give an example?

MT: HTTP/2 and HTTP/3. You could end up being forced to downgrade to H/3.

RS: Support adoption.

##### Poll 

Do not adopt:
2 
15 

Adopt:
19 
0
