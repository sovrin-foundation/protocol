![superseded](../superseded.png)
# Repudiation

If Carol receives a message that purports to come from Alice, she may naturally ask:

    Do I know that this really came from Alice?

This is a fair question, and an important one. There are two ways to
answer it:

* Alice can send in a way that's <font color="blue">repudiable</font>
* Alice can send in a way that's <font color="blue">non-repudiable</font>

BOTH of these approaches can answer the question at hand, but they differ
  in _who can trust the answer_. If Carol knows Alice is the sender,
 but can't prove it to anybody else, then the message is publicly
 <font color="blue">repudiable</font>; if Carol can prove the origin
 to others, then the  message is <font color="blue">non-repudiable</font>.

# How It Works

Repudiable sending may sound mysterious, but it's actually quite simple.
 Alice and Carol can negotiate a shared secret and trust one another not to
 leak it. Thereafter, if Alice sends Carol a message that uses the shared
 secret (e.g., it's encrypted by a negotiated symmetric
 encryption key), then Carol knows the sender must be Alice. However,
 she can't prove it to anyone, because Alice's immediate counter-response
 could be, "Carol could have encrypted this herself. She knows the key, too."
 Notice that this only works in a pairwise channel.

Non-repudiable messages are typically accomplished with digital
 signatures. Everyone can examine a signature to verify its provenance.

Fancy signature schemes such as ring signatures may represent intermediate
 positions, where the fact that a signature was provided by a member of
 a group is known--but not which specific member did the signing.

# When To Use Which Strategy

A common mistake of newcomers to cryptographically sophisticated
communications is to assume that digital signatures should be used
everywhere because they give the most guarantees. This is a misunderstanding
of who needs which guarantees under which conditions.

If Alice tells a secret to Carol, who should control whether the secret
is reshared--Alice, or Carol?

![photo by Wassim Loumi, CC SA 2.0, Flickr: https://www.flickr.com/photos/sophotow/16559284088](whisper-secret.jpg)

If Alice sends a repudiable message, _she_ gets a guarantee that Carol
can't reshare it in a way that damages Alice. On the other hand, if
she sends a message that's digitally signed, she has no control over
where Carol shares the secret and proves its provenance.

There are certainly cases where non-repudiation is appropriate. If
 Alice is entering into a borrower~lender relationship with Carol,
 Carol needs to prove to third parties that Alice, and only Alice,
 incurred the legal obligation.

Janus supports both modes of communication. However, properly modeled
 interactions tend to favor repudiable messages; non-repudiation must be
 a deliberate choice. For this reason, Janus assumes repudiable until
 an explicit signature is required (in which case the `sign()` crypto
 primitive is invoked. This matches the physical world, where most
 communication is casual and does not carry the weight of legal
 accountability--and should not.
