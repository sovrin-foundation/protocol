![superseded](../superseded.png)
# Message Transport
<span style="color:yellow">We need more details but here is some preliminary ideas.</span>

Messaging is transport agnostic. Messaging does not rely on transport for guarantees for delivery, security or routing. The same message package must be semantically equivalent regardless of the transport protocol used to move the bytes from one agent to another.

Despite the agnostic nature of the messaging protocol, the use of a transport protocol requires agreement on conventions for the use of the said protocol. These conventions will be protocol specific and their scope is only decisions needed to transport the message on the said protocol. Anything related to the meaning, security or routing of the message is expressly out of scope.

# HTTP/HTTPS
## URL
The endpoint URL for messages should be a simple endpoint. It should not have route info (ex. DIDs) and should be consistent for all users of the endpoint service.

Example:

https://agency.example.com/msg/
## Headers
<span style="color:yellow">TODO: Any thoughts on http headers</span>
## Response
### Codes
#### OK
How the message is processed and what response is given is the prerogative of the receiver of the message. There is two conventions for non-error response.

**202 Accepted** -- The message is processed asynchronously. No content is return.

**200 OK** -- If the message is processed synchronously and the return is the corresponding message.

#### Error
Standard 400s codes for client errors and standard 500s codes for server errors.