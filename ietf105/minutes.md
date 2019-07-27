# TLS at IETF 105, Tuesday

# Status update (drafts, code points, etc) -- see the slides

CFRG working on PAKE selection;  integration with TLS is obviously important, come to CFRG meeting.

## Delegated credentials

Server side patch in BoringSSL; NSS client side soon to be in FF nightly; FB work in progress.

Plan to drop LURK mention, remove PKCS#1 v1.5 (RSA PSS only). 

Martin Thomson: this needs more text for clarity.

Plan was to not go forward without proof that this doesn't weaken PKI security; a by-hand one is in progress

Refine "Delegated credentials" term to "Delegated authentication keys"

Plan is to start WGLC, but make sure it isn't finished until the analysis is done and reviewed by the WG

## Deprecate MD5 and SHA1 in TLS 1.2

Make signature_algorithms mandatory in handshake; forbig MD5 and SHA1 algs in that extension

Andrei says Microsoft can't enforce now but willing to do so in the future

Consensus in room is to adopt as a WG item; to be confirmed on the list

## TLS Flags Extension

TLS 1.2 has 46 extensions; TLS 1.3 has 28; more coming

Many extensions have no data, just 1 bit of data (their presence) -- call them "flag extensions"

Various methods (fixed-size bitmask, varying-size bitmask, array of bytes, etc)

Can't re-do existing extensions (at least in clientHello), but server response and other messages could do so

Consensus in room is to adopt as a WG item; to be confirmed on the list

## Suppress Intermediates

A new flag in clientHello says "don't send intermediates"

Not clear what to do if intermediate ends up not being available; options are then ugly

Server would ignore extension if it "knows" its chain is "unusual" ("weird" etc)

There is interest, but not ready to ask for adoption yet

## TLS 1.3 Impact on Network Based Security Solutions

Network solutions sometimes insert a middlebox proxy between the client and the server, observers TLS metadata to do policy and access control. TLS 1.3 handshake changes affect these solutions.

Incorporated feedback, has been stable since IETF 104. New commentary started today.

Original plan was to ask for publication as informational even though it's not in charter.

More people read this draft than any other draft; interesting and surprising factoid.

Adjourn without action.

# TLS at IETF 105, Thursday

## DTLS 1.3

Should there be key separation between TLS 1.3 and DTLS 1.3?

Conservative thing is to check for space and make change to do separation if room - plan of record.

## ESNI

Summary of changes since last version and open issues (see materials)

(Discussion of probing of distinct servers.. suggestion is to make all servers in same anonymity set respond same way to hello)

Ekr - why is 'wrong one' is responding at all? 

Martin Thomson - might be a fallback.

Martin Thomson - says right forumulation is that SH must be similar not identical, especially after crypto is configured or installed.

Rich Salz - Martin is right and we need to document that.

Martin Thomson - Will open issue and text

(Discussion of on path hrr attack (See Materials))

Martin Thomson - proposal with key schedule makes it impossible to do the wrong thing. As opposed to relying on quality of implementation

Ekr - may want to consider a more general way to annotate key schedule as a strategy.

Ekr - is a binding across the CH also necessary? Formal Analysis time.

Jonathan Hoyland is working on a draft on how to inject into key schedule.

Christian Huitema - if all CH extensions bound in the ESNI and sorting who is on top.. process not healthy. suggest a single secret for a binding

Chris Wood - concerned that keyshare and cipher suites are not enough.

(Question 1 from slides probably yes (according to author))

(Question 2 and 3 smaller team needed (according to author))

Nick Sullivan - not in favor of requirement because different domains will have different suites and forcing them to normalize will hurt size of anonymity set

Chris Woo this is mostly a text issue

Subodh Iyengar - hrr generator should be able to be different than entity that creates server hello

Ekr - possible to not require, but it definitely leaks information

Ben Kaduk - HR vs SH generation was a design decision. ESNI may constrain you.

Erik Nygren - more documentation, less enforcement on q1

Kyle Nekritz - possibility to use a fate sharing secret with client.

(HPKE vs ESNI Encryption (See Materials))

MT - size of packet is already starting to get pretty big

Chris Wood - esni keyshare is already separate from ch keyshare

David Benjamin - lets revisit after sorting the binding questions. e.g. does HPKE allow same additions to Key Schedule

Ekr - lets sort out what we need and talk with CFRG if HPKE is close but not quite right

(Split mode - continue to include? (See Materials))

Ekr - documents take a soft shoe approach to this by describing it rather than adding mechanism.

Chris Wood - would you be OK with referencing Ben's draft

Ekr - with respect to ridiculous stuff at bottom, yes

Daniel Kahn Gillmor - This is an important use case. Please keep it.

Stephen Farrell - Might be more than 1 way to do backend. Keep requirement but maybe not mechanism as only mechanism.

## Connection ID

MT - beyond presentation the other thing to consider..

MT - the quic work has looked at this. the return check does not cover all these cases adequately (see eric kinnear's quic work)

Hannes - that's part of the work we have a seaprate doc for return routability check so they can proceed independently

Ekr - propose we say in CID drafts what the machinery is and DTLS should not automatically respond to this. It is the responsibility of a future specification to tell you how to deal with it.

Sean - does that mean pr 70 goes away

Ekr - no, needs some refinement. take the 1.3 text and harmonize the pr 70 text. need discussion about general conditions where it would be safe, and a prohibition on the DTLS stack itself taking action.

Sean - hannes and ekr need to talk it out

Eric Kinnear - duplicate packets is an important vector

MT - move all migration pieces into other draft

Sean - we have our marching orders

## cTLS (See meeting materials)

Christian Huitema - removing legacy session ID affects the transcript.

Ekr - yes, this does not for legacy mode.

Scott Fluhrer - recreating 1.3 keeps proofs in place

Adam Langley - could we both transcript and compress? (yes). The benefits seem underwhelming.

Ekr - primarily IOT use cases. More like 150 -> ~55, not a 10 byte saving.

Ben Schwartz - benchmark against plain compression, possibly with a tuned dictionary.

Subodh - Does compression apply to messages beyond ClientHello? (yes)

Roberto Peon - Is the intention to show isomorphism? (yes)

Ben Kaduk - Assumes in order reliable transport?

Ekr - Yes, though compact DTLS is possible too.

Ben Kaduk - If this is TLS 1.4 takes advantage of non backwards compatibility.

(Unknown) - Is this completely new or can it be done compatibly? there are some angles (unexplored) to work it into existing frameworks.

Adam Langley - Implementing this as untrusted shims helps convince you this is transport irrelevant and does not affect security.

Richard Barnes - that's how it was implemented in mint.

Gary (Qualcomm) - what is a server expected to do if a client presents a ciphersuite it has never heard of it would fail.

Richard Barnes - this experiment used as much compression as possible, though less (negotiable) options are possible.

## Hybrid Key Exchange in TLS 1.3

Jonathan Hoyland - Opposed to key schedule changes. Adhoc changes to key schedule harm formal analysis. Let's standarize how to do that first.

Adam Langley - Perfers option 2 from next steps slide.

Ben Kaduk - In terms of trade off if you think everyone is doing similar combinations its different than a lot of variety.

Watson Ladd - We aren't going to have a large number of algorithms that are going to be combined. Generally 2.

Martin Thomson - Candidate 2 and option 2 please. Also concatenation is not quite the right term.

Mark Owen - Option 1 is useful. Also keep designs as general as possible including more than 2 algorithms and classical ones too.

Scott Fluhrer - Each algorithm has a parameter set which will increase the combinations.

Adam Langley - +1 to Watson.

Martin Thomson - CFRG should look into reducing parameter set issues as per use cases.

## TLS MetaData for Load Balancers (see meeting materials)

Stephen Farrell - is full split mode something new. never considered partial split mode an esni use case

Ekr - also thinks esni deals with full split mode as ben describes it

Ben Baduk - it sounds like everyone wants full mode - so lets move on

Jonathan Hoyland - Wrapping TLS does not give clear cryptograrphic properties. Need to bind them, possibly with exporter keys, to get desired properties.

Christian Huitema - What about the QUIC LB work?

Ben Schwartz - Largely they are different issues.

Roberto Peon - Look at intermediary assisted packet loss.

Ben Schwartz - We can do better than proxy protocol so this work should be done in IETF.

Martin Thomson - Where does work like this happen? QUIC WG straddles the question. SecDispatch or Dispatch perhaps.

Ekr - Generally enthusiastic about this. How much of this is a security protocol? May be a security protocol based on Jonathan's comments, or perhaps those questions are isolated.

Ben Kaduk - IAB and IESG should determine where this work should happen.

Jonathan Hoyland - There are several adopted drafts that bind stuff into TLS. As a WG we should look at importer uses not just exporter.

Stephen Farrell - Yes, should standardize.

Erik Nygren - This was a past HTTP workshop topic too. Lack of standards around proxy protocol but the lack of security makes it inappropriate. Efficiency matters.

Chairs - We will talk to ADs to determine next steps.

## HTTPSSVC DNS RR (See Meeting Materials)

Allesandro Ghedini - Does this require 2 serial resolutions?

Erik Nygren - If things go right that's not true. open question does it make sense to inline a/aaaa as esni is doing? performance and complexity are tradeoffs.

Allesandro Ghedini - The double resoultion thing is serious

Erik Nygren - Much of this can be done in parallel

Ekr - More than happy to have DNS work done in DNS groups if it meets requirements

Nick Sullivan - What is specific to https here?

Erik Nygren - Makes sense to generalize this.. specifics are alt-svc formatting and underscore handling for wildcard cases.. and hsts style handling

Adam Langley - Why is the redirect name mandatory

Erik Nygren - Need to get addresses to bind the esnikeys (or names)

Adam Langley - May not apply to me without multi cdn,

Erik Nygren - It's already optional via a .

Tommy Pauly - This should be generic. really consider what needs to be included by ref vs what needs to be inlined

Lorenzo Colitti - More generic means not httpbis. +1 to addresses being ok

Mike Bishop - Outgrowth of altsvc dns rr proposal. altsvc could be applicable to more than https

Dan York - CNAME at apex is important. are you talking with clients

Erik Nygren - More browsers than generic clients, but yes.

Dan York - Beware of scope creep.
