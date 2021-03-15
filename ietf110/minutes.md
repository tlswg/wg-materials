# TLS Working Group Agenda - IETF 110 Online

* [Jabber Chat](xmpp:tls@jabber.ietf.org?join)
* [Meetecho](https://meetings.conf.meetecho.com/ietf110/?group=tls&short=&item=1)
* [Minutes](https://codimd.ietf.org/notes-ietf-110-tls)

## Monday, March 8, 2021 (UTC)
**17:00-19:00 (UTC), Monday Session III**

[See this result in your local timezone](https://www.timeanddate.com/worldclock/fixedtime.html?msg=TLS+-+IETF+110&iso=20210308T17&p1=1440&ah=2)

### Housekeeping

* Scribe Selection / [Note Well](https://www.ietf.org/about/note-well.html)
* Agenda Bashing
* [Interop Runner](https://datatracker.ietf.org/meeting/110/materials/slides-110-tls-tls-interop-runner-00)

### Presentations

#### Interop Runner - Goutam Tamvada (GT)

Watson Ladd (WL): Please add mbed support.

Hannes Tsochofenig (HT): Are the supported tests listed?

GT: There's a wiki

DKG: What's the process for adding an implementation to this?

GT: Add a docker file to the repo.

Stephen Farrell (SF): How do you interact with DNS?

GT: Hard-coded into the endpoints, taken care of by the Interop runner. 

Chris Wood (CAW): ECH keys are plumbed in directly. 

#### ECH - Chris Wood (CAW)

##### Version-10 

To be put on the list today (2021/03/08)

SF: I have issues that are not on the GitHub (GH) list.

Some issues are too complex for GH.

CAW: We'll try to not make things more complex than they need to be. 

##### Issue 384 - PSK usage sticks out:
* Recommend client sends dummy outer PSK if sending inner PSK.

##### Issue 379 - Consider a shorter config ID length

Ekr: I made both changes. I think 1-byte is too short, we'll regret making it one byte later if we need it.

CAW: Happy to compromise on 4-bytes

Ben Schwartz (BS): Variable length concerns me because 
    * the variable length itself is a form of leakage, 
    * invites arbitrary information injection,
    * and adds more plaintext on the wire.
Is trial decryption _required_, or are we trying to say trial decryption is _not_ required. 

CAW: If we go for 1-byte or 4-bytes server has to support trial decryption nonetheless. 

BS: 4-byte trial decryption is no longer required, server operator can expect to assign every key in fleet a unique key. 1-byte is too short to just ignore it.

BS: W.r.t. GREASE with 1-byte is hard to distinguish, but with 4-bytes it's trivial.

CAW: Is that a design constraint we are trying to operate under?
Possible to identify anyway. If a client connects to the same server twice it would (usually) reuse the same config id, but that's not true of GREASE.

BS: 4-byte MUST generate randomly would be ok.

David Benjamin (DB): Regarding trial decryption, the number of keys you need around is roughly ceil(DNS TTL / rotation time). I think even one byte is enough to avoid collisions.

Carrick Bartle: Jumping to 4 bytes is way too much. 

CAW: The tracking argument is not great, because there are other attacks. We assume as part of the threaot model the attacker controls DNS

Ekr: I think Ben's point is somewhat persuasive, and I'm seeing that others mostly think one byte is better, so while I think we'll regret it later, I can live with it

Erik Nygren (EN): Recommend 1 byte?

DB: Gives the DNS a pretty big space to inject stuff into the client plaintext, and we've had issues with that in the past.

Show-of-hands:
Are in favour of 1 byte - 21 raised hands, 16 did not. 111 participants
Are not in favour of 1 byte - 10 raised hands, 27 did not. 113 participants.

##### Issue 386: Fixed-length values should probably be fixed-length

DB: If you can't parse it you can just drop it on the floor.

WL: You don't have a length or a struct if all fields are fixed. So perhaps this might be a problem when you look inside?

Ekr: The ECH config has exactly one HPKE config, it's harder to parse it this way. Have to make general purpose parsers dependend on the KEM context, so against.

WL: +1 Ekr

CAW: Close and move on.

##### Issue 378: Naïve outer_extensions decoding is a DoS risk

DB: We reopened because there was a second DOS vector. If we can come up with a simpler thing that meets our goal we should do that, otherwise we should document them.

Ekr: +1 DB

SF: This creates a testing burden, and I don't see the burden. Drop entirely if we don't find a way to simplify

CB: We could throw out Outer Extensions completely

CAW: Take it to the list.

CAW: Have a biweekly interim meeting between now and IETF 111 to resolve issues.

Sean Turner: I think it's the only way we'll get this done.

Ekr +1

SF +1

###### SF Issues: 

BS: There to support fallback mechanism, without the name in SNI the fallback doesn't work. In special cases where it's not needed you can just not send it.

DB: Client requirement to send a name or server req. to _check_ that name.

SF: I think the restriction isn't needed on client or server.

Ekr: MUST is there because we need it for interop. If SF can submit a PR that covers cases where it's not needed.

CAW: Doesn't appear to be server side MUST.

SF: We should have an explicit focus on making things simpler.

CAW: The design goal is to not make it more complex than it needs to be.

Ekr: I want to make it simpler, and would welcome proposals to make it simpler, but the highest priority is to get it to work. 

SF: Will bring up at interms.

Send out doodle for interop meeting.

#### RFC 8446-bis - Eric Rescorla (Ekr)

##### Issue 1214: Recommended vs. Not Recommended

SF: Other WGs / registries have followed the TLS 1.3 approach. If we move the goalposts they may be unhappy / want to know.

DKG: I don't like "no opinion", lots of people have lots of opinions and some times things stall because we can't reach concensus, "not endorsed" perhaps?

Ekr: That's ok.

BK: IANA experts have to be involved in any codepoint allocation. We should right something that IANA experts can just do there thing and not put the working group on the critical path. 

Rich Salz (RS): The above the line / below the line w.r.t. the WG makes sense. Would the next version of TLS move around a whole bunch of ciphers?

Ekr: Hopefully not, but for example PQ will have to. 

Robin Wilton: Aren't we just reinventing RFC 2119?

Ekr: This is somewhat different

SPT: This doesn't actually affect 8446, it's actually defined in 8447, but it is actually a problem. The WG is accused of being uninviting, we do need a way to make this more approachable.

Ekr: Let's drop this for 8447-bis

##### Issue 1212: General alert

BK: (no hats) something to allowing implementations to be as black box as they can. 

CB: Encouraging as much specifity as possible is good, and allowing non-specificity is _not_ good.

##### Issue 1208: user_cancelled

DB: Don't send anything between `user_cancelled` and `close_notify`, but I think we should just ignore this. 

DB: Users sending this are totally breaking the spec, and are abusing this to signal "please use the old behaviour".

##### Issue 1221

PR submitted

Ekr: Can Chairs do a call for any remaining issues?

DB: If you don't send an extension in HRR client is required to send it as is. General consensus at last meeting was text was a mistake.

DB to file an issue

DM: Should errata be included in this?

Ekr: Filled issues against all errata. 

BK: Most errata are still in the state "reported", so when the document gets sent to me a list of errata and their state would let me process them.
What track is this document on? Proposed Standard or Internet Standard?

Ekr: Don't care

SPT: We can point to the interop matrix.

#### Deprecating FFDH - Carrick Bartle

Rene Struik: Caused because of the hash function being of fixed size, not because of FFDH per se. Important sometimes to use DH, so we should be less broad.

BK: Are you asking about the TLS key exchange in the handshake, should we just prohibit just that flawed usage of DH or should we prohibit this in all places you might use DH in the TLS handshake.

DB: I think we can simultaneously believe that fixed public keys are okay in some scenarios, and that they're not okay for the ephemeral TLS key exchange because that's meant to be ephemeral and we can do better.

DKG: +1 DB

Ekr: Semi-static is basically OPTLS, so it still has an ephemeral. 

Ekr: Some care has to be taken when using long-term DH keys, and we're not getting rid of long-term because it's needed in too many places, e.g. semi-static and HPKE.

Ekr +1 for FFDH and on reusing GGDHE and ECDHE keys, but not on ECDH. 

WL: All the draft does is prohibit certain types of keys, to allow them we would need to introduce new numbers. No reason to use FFDH, we should just ban it entirely. 

Discussion to continue on list.

#### TLS-POK - Owen Friel

WL: I don't think this draft uses the security properties of TLS. TLS assumes the public key is public. Would be better to define a new handshake that provides asymmetric handshake keys.

OF: I'll need to think about that. We're just trying to mimic DPP, which is the network proves to the device that it knows the public key. 

Ekr: I'm not persuaded this has the same properties as TLS, because of the PK secrecy.

OF: We're trying to use the DPP key pair. 

Ekr: Do you care who is the TLS client and who is the TLS server?

OF: That seems a bit weird, but potentially?

JGH: The underlying draft, draft-jhoyla, should reduce the need for analysis, because it should be impossible to weaken the handshake using it.

Scott Fluhrer: This needs CFRG analysis. 

HT: This is useful work and should be done in the TLS WG. The DPP replicate a lot of TLS-like things that are not really necessary, if they have to support TLS too. 

#### OPAQUE in TLS 1.3

BK: Is the server able to provide a certificate?

SC: In OPAQUE-Sign, the server or client cannot use certificates. If needed, you can use OPAQUE-Sign as part of a post-handshake mechanism, where the "main" TLS handshake has already used certificates.

Show-of-hands: How many people have read this draft? 7 raised their hand,  23 did not raise their hand, 94 participants. 
