# Security Context

With physical mail, different approaches offer different guarantees. For
 example, if my letter is sealed with red wax and a stamp, I may be able
 to trust that it hasn't been tampered with since the wax hardened. Understanding
 such guarantees can have very high stakes, as with this envelope that
 was censored many years ago:

![By Russian post (Self-scanned) [Public domain], via Wikimedia Commons.
Russia 1914-10-02 reverse of censored cover registered R-532, sent from
St. Petersburg to Västerås Sweden. Opened by censor and sealed with large
red circular wax seal at reverse and large purple censor marking.
Stockholm 21.10.14 transit postmark and Vasteras receiver same day.
Censor initials in blue crayon manuscript '3 HR'. Sent by ACEA or ASEA,
Allmänna Svenska Elektriska Aktiebolaget.](sealed-envelope.jpg)

Digital communication has similar variety in its guarantees, and a similar
 need to understand what can be trusted. Many exploits owe their existence
 to treating this need for clarity with casual disregard.

Janus introduces a mechanism to clarify what can be trusted about a given
 communication -- the <font color="blue">security context</font>.

## What a security context can tell you

All of the following questions can be answered by inspecting the security
context associated with a message:

* <b>Confidentiality</b> (_Could the message have been observed by another party_?)
* <b>Integrity</b> (_Could the message have been tampered with since it was sent_?)
* <b>Origin</b> (_Is the sender of the message known?_)
* <b>Non-repudiation</b> (_Can the receiver prove the identity of
  the sender to a third party?_ See [this discussion about semantics of
  repudiation](repudiation.md).)
* <font color="gray"><b>Traceability</b> (_Could a third party have
  observed the transmission of this message from sender to receiver?_)</font>
* <font color="gray"><b>Replayability</b> (_Could this message have been
  sent before, and copied to send again?_)</font>
* <font color="gray"><b>Sender Attributes</b> (_Are certain attributes of
  the sender cryptographically verifiable?_ (See the [themis subprotocol](
  ../themis/README.md).)</font>

## How to use a security context

When a party receives a Janus message, it also gains access to the
 message's security context. By inspecting the security context, it is
 possible to make informed decisions about how much trust should be
 imputed to the communication. This is a far safer model that traditional
 authentication, which simply asks for a party's identity, but has little
 to say about the way that identity is established and how it interacts
 with the communcations channel.

Here is some pseudocode for a web service request handler that might
 take advantage of a Janus security context:

```
void handle_request_for_secret_meeting(HttpRequest locked, SecurityContext ctx) {
    boolean unlock = false;
    if (DANGEROUS_MODE) {
        ctx.ignore(CONFIDENTIALITY | INTEGRITY | ORIGIN | NONREPUDIATION);
        unlock = true;
    } else if (!ctx.has_confidentiality()) {
        ignore();
    } else if (!ctx.has_integrity()) {
        ask_to_resend("Please prove your request for a secret meeting hasn't been tampered with.");
    } else if (!ctx.has_origin()) {
        int n = System.time().hourOfDay();
        if (n < 6 or n > 20) {
            deny("I don't accept secret rendezvous requests from anonymous senders at night.");
        } else {
            unlock = true;
        }
    } else if (!ctx.has_nonrepudiation()) {
        deny("I don't accept secret rendezvous requests unless you give me evidence that I could later use to prove you asked for the appointment.");
    } else {
        unlock = true;
    }
    if (unlock) {
        HttpRequest req = ctx.unlock_if_safe(locked, CONFIDENTIALITY | INTEGRITY | ORIGIN | NONREPUDIATION);
        // process request
        ...
    }
}
```


