TLS IETF 113

# cTLS presented by ekr:

Close to done

Ben Schwartz: this is optional because there are use cases where you know some messages are always too small to fit ?

Ekr: No, (different use case I missed)

Ben: this option would be to save 3 bytes?

Ekr: yes

Ben: no objection to this

Martin Thomson: when you said full, it has a length, .... do we need all

Ekr: we have 3, we could have diff semantics to have two.

Richard Barnes: This is fine to do later (in response to slide 6)

Hannes: proposals are good optimalizations. delaying these proposals could be tricky. Some issues could be there for embedded. Might need analyses.

Ekr: we should develop this together.(?)

Richard: dont have lot of stake in this fight. Would be good to have validation of this.

# RFC 8446bis presented by ekr:

Martin: very happy to say 1 error code for PSK

Sean Turner: on next version next month, we will issues WGLC
    
# SNIP Status presented by Martin Thomson

Martin: Looking for implementations, especially server side

Sean: thanks for work.
    
# Hybrid Key Exchange presented by (no one yet, douglas not in the room):

ekr: can we rename it, now we have Hybrid Public Key Exchange - names are confusing.

Martin: you mean Hybrid Public Key Encryption?

ekr: totally fair.
    
# ECH update by Vincent Cheval

Ekr: amazing work. can you clarify (some options disabled)?

Vincent: for the proof we "assume" some options without proof.

Arnaud Taddei: you tested a number of scenarios. Could you extend model in some way?

Vincent: not sure understand question, but the model is extensible so things can be added.

Hannes: thanks for the work -- did you publish papers/code?

Vincent: it is available in github, ongoing work to publication to CCS(?).

Stphen Farrell: there is assumption same ECH is in use for ECH1(?) and ECH2(?)? These are read from DNS and could differ due to DNS cache? Might want to write down assumptions here so people know what not to do with DNS.
    
# Hybrid Key Exchange in TLS 1.3 by Douglas Stebila

Martin: "we are not making changes to the document"?

Douglas: I meant "no technical changes".

Stephen: on question on WGLC - lot of this work is coming to soon. Seems weird to WGLC a doc where algorithms are unknown. Maybe we should park them.

Douglas: I take your point. wanted mechanism to be ready when algos are known.

Joe: we could have WGLC but then not move document further.

Sean: we could say "a WGLC", not "the WGLC" because people start looking at docs only at WGLC.

Sean: yeah we dont finish this before NIST decides algorithms, just trying to get doc in the right place.

Ekr: WGLC seems appropriate. Draft is algorythmic agnostic.

Ekr: I think solution is fine, but... let's say we make TLS 1.4 with some ext. (....) could there be an attack here

Douglas: this version with fixed length and a version with variable length secret with some protection mechanism

Ekr: good question, I was thinking more of a stupid relaxing of fixed length. 

Douglas: so if both are secure, in a variable length setting, and fixed length, my intuition tells me it is safe. (...) I believe a downgrade could happen (paraphrased by minute taker).

Ekr: this is great, let's go to WGLC.

Nimrod Aviram: .... can we proof security of this... my understanding of key compound is very hard to prove the security of the entire protocol. I would not worry about this kind of attack. Consider how to work this if ever on something not TLS 1.3 (paraphrased by minute taker).

Nicholas Gajcowski: It is not very specific on re-use of ephemeral key which is an issue for latice based protocols. It is a little loose on the guidance because we dont have specification written. NIST ...... the details are not yet set. It is a little early .... on how to reuse keys .....

Douglas: doc does not prohibit re-using ephemeral key reuse because TLS 1.3 does not forbid it. doc does state ..... re-use is forbidden.
        
# Deprecating Obsolete Key Exchange Methods in TLS presented by Nimrod Aviram

Yoav Nir: conversation in jabber room, looks like update to RFC .... This is for TLS 1.2 and lower since 1.3 already deprecated these. UTA has already done an RFC for this (on how to use TLS 1.2 when needed).

David Benjamin: we have deprecrated things in this WG. It could be done here. This draft seems reasonable for TLS.

Yaron Sheffer: RFC 7525, yes we had duplication of work between TLS and UTA. In some cases it was up to TLS to deprecate earlier versions of TLS but for anything other it would have been better if we had done the work once. The right venue for this is UTA, with 7525bis.
    
# draft-kampanakis-tls-scas-latest presented by Panos:

David Benjamin: won't work for current webpki - assumes list is pushed and always up to date.

# draft-AuthKEM by Thom Wiggers

(no questions)
