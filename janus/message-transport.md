# Message Transport
<span style="color:yellow">We need more details but here is some preliminary ideas.</span>

Messaging is transport agnostic. Messaging does not rely on transport for guarantees for delivery, security or routing. The same message package must be semantically equivalent regardless of the transport protocol used to move the bytes from one agent to another.

Despite the agnostic nature of the messaging protocol, the use of a transport protocol requires agreement on conventions for the use of the said protocol. These conventions will be protocol specific and their scope is only decisions needed to transport the message on the said protocol. Anything related to the meaning, security or routing of the message is expressly out of scope.

# HTTP/HTTPS
## Request
### URL
Endpoint URLs for Janus messages should be simple. The URL should not expose any extra information about the message (e.g., the type of message being sent). It should have no route info (e.g., DIDs) and should be consistent for all users of the endpoint service. Message types and destination DIDs are encapsulated in the encrypted messages themselves, so as not to reveal information to proxy agents and routers along the path to the ultimate destination.

Example: https://agency.example.com/msg/

### Method
The method used for Janus messages should be `POST`, and agents with HTTP interfaces that receive a different message are encouraged to return a 405 Method Not Allowed.

Janus does not depend on the transport protocol for confidential communications, therefore an HTTP method can leak details about the message being sent. HTTP is resource-oriented, and Janus is message-oriented. The intent of a Janus message is embedded in the message type.

### Headers
<!--
DF: I don't like vnd! vnd suggest that it is vender specific. like vnd.ms-excel from Microsoft. I think we should choose one       even if it's not part of the mime-type spec. We can use it and I think we can get it into the spec fairly easily once we       have adoption.
-->
Accept: message/vnd.janus
Content-Type: message/vnd.janus

## Response

### Headers
If a response contains a message then include a Content-Type header.

Content-Type: message/vnd.janus

### Status Codes
How the message is processed and what response is given is the prerogative of the receiver of the message. There are two conventions for non-error response.

#### Success
**200 OK** -- The message was processed successfully, and the body contains the response message.

**202 Accepted** -- The message will be processed asynchronously. No response message.

**204 No Content** -- The message was processed succesfully with no response message.

#### Errors

Standard 4xx codes for client errors and 5xx codes for server errors.
