# TLS at IETF 105, Tuesday

# Status update (drafts, code points, etc) -- see the slides

CFRG working on PAKE selection;  integration with TLS is obviously important, come to CFRG meeting.

## Delegated credentials

Server side patch in boringSSL; NSS client side soon to be in FF nightly; FB work in progress
Plan to drop LURK mention, remove PKCS#1 v1.5 (RSA PSS only) [Martin says needs more text for clarity]
Plan was to not go forward without proof that this doesn't weaken PKI security; a by-hand one is in progress
Refine "Delegated credentials" term to "Delegated authentication keys"
Plan is to start WGLC, but make sure it isn't finished until the analysis is done and reviewed by the WG

## Deprecate MD5 and SHA1 in TLS 1.2

Make signature_algorithms mandatory in handshake; forbig MD5 and SHA1 algs in that extension
Andrei says MSFT can't enforce now but willing to do so in the future
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

should there be key separation between TLS 1.3 and DTLS 1.3?
conservative thing is to check for space and make change to do separation if room - plan of record

## ESNI

Summary of changes since last version and open issues (see materials)
discussion of probing of distinct servers.. suggestion is to make all servers in same anonymity set respond same way to hello
ekr asks why the 'wrong one' is responding at all. mt suggests its a fallback
mt says right forumulation is that SH must be similar not identical.. especially after crypto is installed
rsalz says mt is right and we need to document that.
mt to open issue and text
discussion of on path hrr attack (See Materials)
mt proposal with key schedule makes it impossible to do the wrong thing. As opposed to relying on quality of implementation
ekr may want to consider a more general way to annotate key schedule as a strategy.
ekr is a binding across the CH also necessary? Formal Analysis time.
Jonathan ??? is working on a draft on how to inject into key schedule.
C Huitema: if all CH extensions bound in the ESNI and sorting who is on top.. process not healthy. suggest a single secret for a binding
cwood concerned that keyshare and cipher suites are not enough
Question 1 from slides determined yes (according to author)
Question 2 and 3 smaller team needed (according to author)
Nick Sullivan - not in favor of requirement because different domains will have different suites and forcing them to normalize will hurt size of anonymity set
cwood - this is mostly a text issue
subodh iyengar - hrr generator should be able to be different than entity that creates server hello
ekr - possible to not require, but it definitely leaks information
kaduk - hrr vs sh generation was a design decision. esni may constrain you
erik nygren - more documentation, less enforcement on q1
kyle - possibility to use a fate sharing secret with client
HPKE vs ESNI Encryption (See Materials)
mt - size of packet is already starting to get pretty big
cwood - esni keyshare is already separate from ch keyshare
david benjamin - lets revisit after sorting the binding questions. e.g. does HPKE allow same additions to Key Schedule
ekr - lets sort out what we need and talk with CFRG if HPKE is close but not quite right
Split mode - continue to include? (See Materials)
ekr - documents take a soft shoe approach to this.. by describing it rather than adding mechanism. Impt use case
cwood - would you be ok with referencing Ben's draft
ekr - wrt ridiculous stuff at bottom, yes
dkg - important use case. please keep
stephen farrell - might be more than 1 way to do backend.. keep requirement but maybe not mechanism as only mechanism

## Connection ID

mt - beyond presentation the other thing to consider..
mt - the quic work has looked at this. the return check does not cover all these cases adequately (see eric kinnear's quic work)
hannes - that's part of the work we have a seaprate doc for return routability check so they can proceed independently
ekr - propose we say in CID drafts what the machinery is and DTLS should not automatically respond to this
it is the responsibility of a future specification to tell you how to deal with it
chair - does that mean pr 70 goes away
ekr - no, needs some refinement. take the 1.3 text and harmonize the pr 70 text. need discussion about general conditions where it would
be safe, and a prohibition on the DTLS stack itself taking action
chair - hannes and ekr need to talk it out
eric kinnear - duplicate packets is an important vector
mt - move all migration pieces into other draft
chair - we have our marching orders

## cTLS (See meeting materials)

??? - recreating 1.3 keeps proofs in place
adam langley - could we both transcript and compress? (yes). benefits are underwhelming
ekr - primarily iot use cases. more like 150 -> ~55, not 10 byte saving
schwartz - benchmark against plain compression please
subodh - same thing for places other than CH (yes)
roberto peon - is the intention to show isomorphism? (yes)
ben kaduk - assumes in order reliable transport? (yes)
ben kaduk - if this is tls 1.4 takes advantage of non backwards compat
??? - is this completely new or can it be done compatibly?
there are some angles (unexplored) to work it into existing frameworks
adam - implementing this as untrusted shims helps convince you this is secure
gary ??? qualcomm - what is a server expected to do if a client presents a ciphersuite it has never heard of
it would fail

## Hybrid Key Exchange in TLS 1.3

jonathan - ahdoc changes to key schedule harm formal analysis. standarize how to do that first
adam langley - perfers option 2 from next steps slide
ben kaduk - in terms of trade off if you think everyone is doing similar combinations its different than a lot of variety
watson ladd - we aren't going to have a large number of algorithms that are going to be combined. generally 2
martin thomson - candidate 2 and option 2 please. also concatenation is not quite the right term
mark ?? cse ?? - option 1 is useful. also keep designs as general as possible including more than 2 algorithms and classical ones too
???? - each algorithm has a parameter set which will increase the combinations
adam - +1 to watson
mt: CFRG should look into reducing parameter set issues as per use cases

## TLS MetaData for Load Balancers (see meeting materials)

stephen farrell - is full split mode something new. never considered partial split mode an esni use case
ekr - also thinks esni deals with full split mode as ben describes it
ben kaduk - it sounds like everyone wants full mode - so lets move on
jonathan - wrapping TLS does not give you what you think - need to bind them somehow
christian huitema - what about the QUIC LB work?
schwartz - largely they are different issues
roberto peon - look at intermediary assisted packet loss
schwartz - we can do better than proxy protocol so this work should be done in IETF
martin thomson - where does work like this happen? QUIC WG straddles the question. SecDispatch or Dispatch perhaps
ekr - generally enthusiastic about this. how much of this is a security protocol? May be a sec protocol based on Jonathan's comments, or perhaps those questions are isolated
kaduk - ?
jonathan - there are several adopted drafts that bind stuff into TLS. as a WG we should look at importer uses not just exporter
stephen - yes, should standardize.
erik nygren - this was a http workshop topic too, lack of standards around proxy protocol but the lack of security makes it inappropriate. efficiency matters
chairs - talk to ADs

## HTTPSSVC DNS RR (See Meeting Materials)

allesandro ghedini - does this require 2 serial resolutions
nygren: if things go right that's not true. open question does it make sense to inline a/aaaa as esni is doing?
performance and complexity are tradeoffs
allesandro - the double resoultion thing is serious
nygren: much of this can be done in parallel
ekr: more than happy to have DNS work done in DNS groups if it meets requirements
nick sullivan: what is specific to https here?
nygren: makes sense to generalize this.. specifics are alt-svc formatting and underscore handling for wildcard cases.. and hsts style handling
adam langley: why is the redirect name mandatory
nygren: need to get addresses to bind the esnikeys (or names)
adam: may not apply to me without multi cdn,
nygren: its already optional via a .
tommy pauly: this should be generic. really consider what needs to be included by ref vs what needs to be inlined
lorenzo: more generic means not httpbis. +1 to addresses being ok
mike bishop: outgrowth of altsvc dns rr proposal. altsvc could be applicable to more than https
dan york: cname at apex is important. are you talking with clients
nygren: more browsers than generic clients, but yes.
dan: beware scope creep
