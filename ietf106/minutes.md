Agenda IETF 106TLS Working Group Agenda
Saturday and Sunday, 16/17 November, 2019
8:30 - 16:00, Moor/Morrison

Notetaker: Tommy Pauly

Request to move semi-static after extended key schedule.

Working Group Drafts

Importing External PSKs for TLS
===============================

Chris Wood: Changes from -00: Changed to be more specific about the protocol that is used for imported identities. Added opaque field to defend against Selfie attack. Started work on formal analysis.

PR open to describe Selfie attack

Mohit: Some people thought this was a misconfiguration, but I provided another example to describe the problem. If people want to have secret communication, and they exchange a secret, it's vulnerable to selfie attack without the attacker knowing the PSK. I disagree with Martin Thomson that this attack requires some additional signalling.

Martin Thomson: I forget what the signalling comment was specifically. To address the core question, I think the comment is on the text more than the design. We need to understand what's required to do this. I think the proposed change can cause a regression in privacy.

Mohit: Even the previous text mentioned identities such as MAC address. If it's only between two parties, you just need to make sure the address isn't shared. Would like the WG to make a decision on this.

Chris: I mainly want to ensure we don't regress anything.

Jonathan: We need to make sure that people don't assume that role and identity are in some way tied. People suggest putting "client" and "server" instead of name, which doesn't fix anything. Make sure that things are always different, not necessarily always unique.

ekr: The idea here is to start with one PSK and move to N^2 PSKs. In some unspecified way, figure out what key to use. The current text punts on the problem to the application. The proposed text is somewhat confusing and worse. How are the identifiers used?

Chris: Yes, the document does try to punt on this question

Ben: We were thinking a lot about resumption PSKs and not external PSKs before. My suggestion is to have a standalone document to describe considerations for how to USE the PSKs to avoid various attacks.

Chris: I want to keep a reference to the selfie attack. I don't think we need an errata on the existing RFC.

Martin: I was going to say the same thing as Ben--have a separate document. We're not ready for that in this document. This is not for an appendix.

Sean: Are people interested in joining a design team for this on a new document?
(Several hands go up)

ekr: There are two modes of issue: being confused between client and server (solvable problem by separating what keys you use as each role); and the other problem of having multiple other parties (N-party), in which case you use more identifiers.
I'd suggest that we remove all of this material. I think we should reject the PR and maybe strip down the existing text.

Sean: We will reject the PR, and form a new team to look at this.

Mohit: I want to sleep on rejecting the PR. Fine if there is consensus. My quibble is that if we are going to add identifiers to this, we should have some text to describe what to do about the Selfie attack.

Jonathan: It boils down to: do you trust everyone who has your key.

(Debate cut off)

Chris: Other open PR is to change key binder computation. Not sure if it's needed.

Jonathan: I think it's needed as a general thing, not sure it needs to happen here. May fit better in the other document than here. We should use binder labels but define them in the key schedule document.

ekr: I've concluded that we do have to do this. We've had too many complicated attacks on this kind of thing.

Hannes: I am uncomfortable about making changes like this based on gut feeling, that adds complexity.

Chris: There is a case in which this is potentially a problem, so I think it is concrete enough.

Martin: It's not very clear what the structure of the imported identifier is. The intent is to import it in the structure mentioned?

Chris: Yes, please file an issue.


Exported Authenticators
=======================

Nick Sullivan: This document has gone through last call reviews. One last point raised by SECDIR.

Client-initiated server auth for an extra certificate is possible using an authenticator request, mirroring the certificate request, and has an exported authenticator. The set of extensions currently allowed in this uses the server list from IANA. So this isn't allowed for clients. The suggestion was to have the document update RFC 8446; or else spin out a new extension point for SNI that's not in the client hello; or treat the cert-request-like structure like a client hello, and use client-hello extensions.

So we have three proposals.

Martin: I think this is the right approach. We started pulling about extensions and their roles, and there are multiple types of extensions in the client hello. They have different purposes. The third way is the simplest.

Kyle: Would it make sense to treat this as a different message?

Nick: If so, I don't think we need a new column in IANA.

Ben: I like the third option, but if you put yourself in the shoes of a bad server, what could happen if they just process whatever client hello extensions.

Nick: Yeah, may make sense to have a whitelist.

Jonathan: Would we have to ignore mandatory extensions?

Nick: We wouldn't have any such mandatory requirements, it's not about expecting a full list.

Jonathan: Yes, do a whitelist.

ekr: I don't like the third option, to the point that Ben made. We can discuss how to express this. I am OK to update 8446.

Nick: What about just whitelisting SNI? That's the only one we need.

ekr: I'm not sure.

Chairs: More discussion required. Please chime in on the list!

Delegated Credentials
=====================

Nick Sullivan: Also a proposal to add algorithm flexibility to advertise a set of supported algorithms to be used for delegated credentials. There's a dual use of the algorithms.

Martin: We struggled with this in RSA PSS. We have multiple pieces of stack and policy that need to support this thing before we turn it on. At the point we turn on PSS generally, we can turn it on here. I'm OK with this proposal. Having a small set of schemes for delegated credentials is a nice picture.

ekr: I can live with things are they are, or the change. It's a beauty contest. Does anyone feel strongly on one side or the other?

Kyle: I realize there's a use case, but will anyone use this? We wouldn't. No problem if someone does...

Martin: Let's not add something that we don't have a use case for, but there seems to be a good one here. It's nice to be able to experiment with some of these algorithms.

Sean: Seeming like we should accept this proposal.

Hannes: On a separate issue, the max lifetime of a delegated credential (7 days) seems to be random and a little short. 

Nick: It's a max based on typical deployment of OCSP.

Martin: This is coming out of the web deployment. Maybe say "absent other information, should be 7 days" but let it change for other deployment models.

Hannes: IoT devices can benefit from longer period here.

Ben: I'm not sure there's a web context that's identifiable to the server operator; they don't know what kind of client they have. You could signal a max in the client hello to indicate this.

Martin: This gets into a vague area. Not sure if we're making this worse.

Kyle: If we don't have a MUST in here, what do we do if the lifetime is beyond what the client will accept.

ekr: I would suggest that when we understand the other uses, we add some signalling mechanism then.

Hannes: Specifically, in the TLS spec, we explain that things are mandatory to implement, absent another configuration. Specify 7 days UNLESS some other need.

Subodh: Can we get an early allocation?

ekr: It's already in FireFox

Sean: Yes, we need an early allocation then.

ekr: We need something like QUIC has for provisional allocation space. I can work on that.


TLS 1.3 Extended Key Schedule
=============================
Jonathan: TLS has nice properties, but may we want some new properties, like privacy properties for clienthello, or hybrid quantum keys.
Can we add these without breaking things?
We know that TLS has properties based on analysis. Modifying the key schedule may mean we need to reanalyze everything.
How do we extend key schedule without breaking things? There are many drafts that want to change the schedule, such as:
    hybrid key exchange
    ESNI
    External PSK
    Semi-static
All of these modify the key schedule in different ways, and don't have analysis of how they combine. Many dragons lurk here!
This draft attempts to catch these changes and define a framework for making this composable.
Defines a few points to inject into the schedule that are safe. One for PSK, one for extra secret before D-H, one for master secret extension.
To clarify, the labels in these additional injected values are important (based on discussion).

Have an IANA registry for the fixed order of how to add new secrets.
There is an alternate proposal to use the fact that HKDF can take two inputs, but makes the key schedule non-linear.

Martin: You could also just concatenate things. We have in most implementations the ability to switch out the input to HKDF-extract, rather than inventing new joints in the protocols. The concern that this is trying to solve is valid.

ekr: The current schedule has one secret insertion points. Trying to have more inputs shows the original problem. I also like the concatenate option. Let's let the crypto people suggest what to do.

Russ: Does this affect early data or not?

ekr: For any case where we have an input into the schedule, we could change it from 1 to N.

Hannes: The schedule became more complicated into 1.3; and we are making it incrementally more complicated. Can we work on the proof to allow the implementation to be simpler? It's a lot more code, which adds risk.

Russ: This is very interesting but also complex. The key schedule is the core of 1.3. This looks like 1.4.

Jonathan: Should we not do any of the extensions like ESNI?

Sean: Let's take this to the list...

Daniel Migault: This is a major change. Let's be careful.

Compact TLS 1.3
===============
THESE SLIDES ARE APPALLINGLY GREEN
ekr: What are we trying to solve? There's still cruft in the TLS handshake due to legacy that can't change. Why does QUIC have to use legacy version numbers though?
From the beginning of 1.3, there was concern about the feature set being too rich. What's the minimal feature set that easier to reason about?
Also helps make the implementation smaller if we can simplify.
Various use cases: QUIC, ATLS, LAKE, EAP...
- Clean up the handshake messages (use varints, remove unnecessary fields, remove redundant message lengths)
- Specialization Mechanism (not everyone wants all extensions, create more slimmed profiles). Remove unneeded extensions, but allow extensions.
Signal a profile that you want, with variation.
- Predefined extensions.
Initial numbers show a good win of bytes on the wire. Goes from 1K bytes to 300 bytes for ECDHE.
QUIC had separated handshake and records; but this wasn't formal in TLS yet. This change makes that formal, and defines the interface strongly.

Have people read this? (couple dozen hands)
We'll need to change the charter for this

Daniel Migault: This is not the first compression. We should work with LPWAN.

David Schinazi: As a question, you mentioned QUIC as a use case. This is for a future QUIC version, right?

ekr: Yes, and. The separation supports what we did in QUIC. If we change the format, that's for QUICv2. Change the charter.

Valery: Important work. We should work on it. Profiles seem to be out of band, and need to agree on profiles. What do we do when a profile needs to be changed?

ekr: I agree this should be solved. Still working on ideas.

Tommy: Strongly support adoption. Not just trying to make the protocol smaller on the wire, but make the protocol more attractive to many new use cases that don't need previous overhead.

Chairs: Two hums.

Do you want to adopt?
(Strong hum)

Do you not want to adopt?
(Weak hum)

Ted Hardie: I think we should decompose this work. The retconning of the layer separation is much easier. You can get parts done faster!

ekr: Happy to defer to the group for splitting.

Semi-Static Diffie-Hellman Key Establishment for TLS 1.3
========================================================
Bringing this document back after London, due to compact TLS interest.
The semi-static case uses a static key share, mixed into the key schedule, and replaces the cert verify with a MAC.
Key schedule change refers back to Jonathan's presentation earlier.

Martin: I am worried about this changing more in the protocol than what it intends, since implementations may not be prepared to add items it at this point in the schedule.

ekr: If we don't care about the benefit of mixing server key into the master secret, we'd be okay. To support client auth, we don't mix them. That's a little goofy. Karthik wanted to keep the structure, I think that's good to do.

Jonathan: Is it obvious that this gives you the same guarantees as TLS 1.3?

ekr: I believe it does, but we need to confirm. It is intended to.

Hum for pursuing this topic:
    (strong)

Hum against
(some, but quieter)

About 10 willing to review

5 willing to write text


Batch Signing for TLS
=====================
David Benjamin

ekr: This is cool, like the merkle tree. Can you contrast this design with just using a DC for a very fast interface?

David: You'd have to deploy new DCs frequently. It's true that that would get you some of the benefits.

ekr: I am supportive of this, just wanted to check if it's still useful with DCs

Martin: I think you can avoid the full hash for the blinding. And, did you consider minting another kind of certificate verifying?

DKG: You mentioned it may leak info about load. I think that can be avoided. Thanks for including the blinding.

Ben: This seems good for slow post-quantum signing.

Christian: Very cool work, but makes me sad. Pushes towards more concentration on big servers.

Kazuho: I think this is a good idea. Changes cost from O(N) to O(1).

Hums:
Are you interested in working on this?
Good hum

Will you contribute?
6 hands

Do you want to adopt this?
Strong hum

Do you not want to adopt?
No hums

=================================
=================================
Thursday, 21 November, 2019
17:40-18:40, Thursday Afternoon session III, Canning

Note taker: Joe Hall

Well-known URIs for ESNI
=======================

slides: https://datatracker.ietf.org/meeting/106/materials/slides-106-tls-tls-farrell-106
draft: https://datatracker.ietf.org/doc/draft-farrell-tls-wkesni/

Ben S: likes it, imagine you have to change something in the Master to do this

A: cron job that does this hourly.

Ben: do you need the .well-known or can you tell the master "here's the URI"?

dkg: I like this. your DNS auth server will check, should the client check too? Also, this should specify some sort of MIME type and register it?

A: yes

Tommy P: interesting, should explore if we really need .well-known for this when asking a server for the keys for a given name?

A: in sftcd's setup, he just knows.

David Schinazi: interesting, this is a server-wide policy

EKR: what are the semantics of this in multi-CDN use cases. 

A: Not clear. That should be resolved. I assume CDNs would need to do something to resolve this.

PHB: Think that this might be problematic. At the level of ESNI auth, Talking about keys that are with the HOST connection not the service, may be a conflict

DKG: This draft should specifically state if this is an exclusive list or additive list.

Sean T: Please do a rev and we'll take it to the list and resolve before next IETF.

Deprecating MD5+SHA1
=======================

slides: https://datatracker.ietf.org/meeting/106/materials/slides-106-tls-tls-md5-sha1-deprecate
draft: https://datatracker.ietf.org/doc/draft-ietf-tls-md5-sha1-deprecate/

Only one change: MUST abort handshake

Allesandro: even though some can't actually disable MD5+SHA1, we should move ahead.

Sean T: Would like to proceed with WGLC.

TLS Ticket Requests
=======================

Chairs: Sense we got was that people want this to be published with a SHOULD and we're ok with that

Sean T: There was one issue in this draft, will do a 2nd WGLC on just this one issue.

Tommy: Pointing this out and the confusion it generated was good to see and resolve.

Sean T: Will take it to the list and point to the PR that does this and ask for objections.

Encrypted SNI
=======================

slides: https://datatracker.ietf.org/meeting/106/materials/slides-106-tls-ietf106-tls-esni
draft: https://datatracker.ietf.org/doc/draft-ietf-tls-esni/

(PR200)

sftcd: you can still have multiple RRs right? 

A: sure.

(PR 201)

Alessandro: this is how we imp'd ESNI. Wouldn't change anything for us... wouldn't simplify.

sftcd: I'd be for flattening, get rid of the complexity

Kaduk: depends on how many keyshares you have

Tommy p: does anything stop you from doing one keyshare per ESNI? 

A: No. The point is the client imp negotiation has to iterate over keyshares.

David B: Don't care much about this one. motivation was looking at sftcd's draft... the representation of the server was a double list structure. It got annoying. If you flatten it, it is a list of public things and private things.

A: we deserialize the structure and negotiate things in sequence. Store some stuff and then come back.

Ben S: the only signifincat thing that we're talking about duplicateing is the fallback thing. The ESNI config bundle is still only scoped to a single IP pool, so if you are in a multi-CDN case you'll need multiple RRs. It's a little silly to replicate the fallback name, this seems weird

Sean T: we need more discussion on the list about this. 

Sean T. On WGLC, need to get eyes on 201 so we can come to conclusion sooner.

EKR: We can't go to WGLC without substantial analysis

sftcd: What's the question marke on HPKE?

A: We're using some weird stuff that is psuedo-HPKE.

sftcd: Would support the HPKE change here.

Richard Barnes: HPKE is about what you need and should be ready in time.

EKR: If we don option 2 we'll need to an exporter from HPKE

Barnes: Should be straightforward.

Tommy: As a bikeshed, when we talk about encrypted ClientHello, do these options require a full CH?

Ekr: good question... this is a matter of intution... mine is to get it all in there as when we've done less we've been sad. That is, generate completely fresh CH. Concern that we may be duplicating things. You only use one or the other of the innards.

Tommy: e.g., in IKE there is a lot of precedent for encrypted stuff

Nick Sullivan: early data is not in the CH

Jonathan Hoyland: (something about decompression?) If you have no interior CH, and just have key that is used to encrypt the extensions... easier to reason about who has that key

Wes H.: Encrypting turtles all the way down. Protocols have a horrible time with complexity when imps get it wrong. I would want to imp both and see what got me more code reuse, etc.

Richard Barnes: seems like if we're enc CH, we can enc the Server Hello... then it's encrypted Hellos all around.

Ekr: has the benefit that you (? don't have to tunnel encryptoin)

Deb Cooley: CH inner and outer. Have the inner which we had originally, encrypting the inner. Will you force validation between the two?

Ekr: random values will be completely different... if you don't do the compression trick,

Kaduk: imagine a world where the outer CH is fixed except for the encrypted bit. Outside is just in case you have to fallback.

Deb: reminds me of SP3 where we did encapsulate... had to be careful of how you did it and had things in both places. If you have values in both places you have to make sure values in both are the same.

Ekr: here we just ignore one or the other... in trivial case KeyShare only in each.

Deb: writing a check, you put the numbers of the value of the check, and then write out the words of the value... those should match and if you don't problems.

Ekr: here, we don't care if they're the same.. and one depends on the other so they have to be different

Deb: is there going to be a way to not do this?

Ekr: many people will not do it.

Ben S: like cTLS, could treat inner and outer as reconstructed... would put them into your transcript

Ekr: that's what's supposed to happen.

Ben: your outer ESNI value could x x fingerprinting resistance

DKG: Thought on Barne's suggestion to encrypt Server Hello... not sure we'd want the ESNI acceptance signal to be observable.

Ekr: the only real reason is you can probe accepted vs. rejected.

Dkg: the work that is going on in LAMPS about protected email headers smells like this.

Tommy: Concerned that if we say we're going to ignore them but they'll probably be the same, that's not going to work. We need to think about the consequences of this.

PHB: Had a solution built on IP-based cert but realized those don't exist.

Nick S: (IP certs do exist.) The two options are not necessarily different... 2 can complement 1. Let's go forward with 1... option 2 can be implemented as a separately.

Jonathan H: with the injected key you know that the backend server knows the injected key... the reasoning is simpler because you know the other side knows the key.

Sean T: We'll take the Charter discussion to the list.
