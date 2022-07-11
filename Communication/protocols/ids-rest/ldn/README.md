# IDS Linked Data Notification (IDS-LDN)

LDP and REST focus on the communication between a User-Agent and an origin server.
 A message represents a state check or a state transformation, triggered by the
 client and executed by the server. However, in many use cases the client is the
 source of an information, for instance an observed event, and wants to notify
 the server. The IDS defines NotificationMessages for this purpose.

 A REST or LDP binding does not allow such interactions without creating or
  overwriting a specific resource. In the case of an event message, the client,
  however, does not want to create a new resource each and every time it
  forwards a notification to the server. For instance, the submission of a
  huge number of POST/PUT requests in a real-time scenario would lead to
  an arbitrarily huge number of resources at the server, as each notification
  must be treated as a new creation request. This is neither efficient nor desired.

[Linked Data Notifications](https://www.w3.org/TR/ldn/) provides a solution
 to this issue. A Receiver can host a special LDP Container called 'LDN Inbox'.
 This inbox is the target for notification messages. A notification itself is a HTTPS
 POST request with the content contained in the request body. The payload must be a serialized
 RDF syntax, with JSON-LD as the recommended format. In any way, the "Content-Type"
 header must outline the used MIME type. In addition, all other header requirements
 apply as listed in the [Table 2](#table-2-request-headers).


**The notifications are optional for the IDS Connector. An IDS Connector
 can advertise itself as "IDS-REST compliant " even if it does not
 support notifications.**

However, in case an IDS Connector supports also notifications, it must use the Link
 header with the value

**Example** "request and response"
 ```http request
HEAD /that HTTP/1.1
Host: https://connector.example.org
Accept: application/ld+json

HTTP/1.1 200 OK
Link: <https://connector.example.com/inbox>; rel="http://www.w3.org/ns/ldp#inbox"
```


The Connector furthermore must add the following triple to its Self-Description:

```turtle
@prefix ldp: <http://www.w3.org/ns/ldp#> .
<https://connector.example.org>
    ldp:inbox <https://connector.example.com/inbox>
.
```

This allows the discovery of the inbox through the clients. The recommended
 location of the inbox is at https://<connector-authority>/inbox/.

The server receiving notifications may, as in any other IDS communication,
 block the sender if it assumes abuse, must verify the sender's identity,
 and insist on TLS encrypted communication. Clients violating these
 principles or senders of incorrect syntax may or may not receive an
 explaining error response. In any way, a server must not respond with a
 2xx status code if it did not accept the notification. If the server
 accepts the notification request, it must return a 200 OK response as
 for any other accepted POST request.

| Interaction Pattern  | IDS-REST Request Binding |  IDS-REST Response Binding | Consequence |
| :--- | --- | --- | --- |
| Notification to an IDS Connector	|	POST at the inbox location. The content of the notification is in the body of the request, encoded in a common RDF serialization.	|	200: OK	|	_unsafe: sending a notification might change the world_	|
|		|		|	400: Bad Request	|	idempotent: sending the same notification several times must not make any difference.	|
|		|		|	406: Not Allowed	|		|
| Notification about an IDS Connector	|	POST at the inbox location of the receiving connector. The representation of the changed IDS Connector is represented by an instance of the ids:Connector class in the request body.	|	200: OK	|	unsafe	|
|		|		|	400: Bad Request	|	idempotent	|
|		|		|	406: Not Allowed	|		|
| Notification about an IDS Resource	|	POST at the inbox location. The representation of the changed IDS Resource is represented by an instance of the ids:Resource class in the request body.	|	200: OK	|	unsafe	|
|		|		|	400: Bad Request	|	idempotent	|
|		|		|	406: Not Allowed	|		|
| Notification to many IDS Connectors	|	undefined.	|		|		|
|		|	Recommendation: Send one POST to every notification receiver.	|		|		|
| Notification about an IDS Participant	|	POST at the inbox location. The representation of the changed IDS Participant is represented by an instance of the ids:Participant class in the request body.	|	200: OK	|		|
|		|		|	400: Bad Request	|		|
|		|		|	406: Not Allowed	|		|
##### *Table 6: Notifications and their mapping in the IDS-REST binding.*

Note that the notifications about a Connector, Resource, or Participant
 state are duplicating the PUT operations on such resources. For
 instance, the information about a changed attribute in a Connector
 Self-Description could either be transported via PUT at the
 IDS Metadata Broker path representing this Connector
 (https://broker.com/catalog/connectorA/), or by sending a notification
 to the IDS Metadata Broker's inbox (https://broker.com/inbox/).
 Nevertheless, in case this type of notification is intended for a Connector
 that does not provide such a resource path, notifications are the only feasible way.

**see also**

[Linked Data Notifications, W3C Recommendation 2 May 2017](https://www.w3.org/TR/ldn/)

---

## Requests

Collection of valid request templates can be found [here](./requests/README.md).

## REST API description

REST API description can be found
 [_maybe ...here_](https://app.swaggerhub.com/apis/IDS_Association/).

## Authors
| | | |  
| :--- | :--- | :--- |
| Sebastian Bader  | GitHub  | [sebbader](https://github.com/sebbader)  |
| Jörg Langkau     | Mail    | jlangkau@nicos-rd.com  |
|                  | GitHub  | [jlangkau](https://github.com/jlangkau)  |
|                  | twitter | [@LostInDataSpace](https://twitter.com/LostInDataSpace)  |

## Contributers


---
