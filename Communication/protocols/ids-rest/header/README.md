IDS-REST# IDS-REST HTTP-Header

---

## HTTP Headers of IDS-REST in alphabetical order

### ids-authorizationToken

Authorization token. Technology and issuer can be selected by the server, is not specified further by the IDS.

- used by: `request` (`optional`, depending on the receiving IDS Connector)

> Type: `xsd:string`
```http request
GET /
Host: https://www.example.com
ids-authorizationToken: "eyJhbGci..."
```
---

### ids-contentVersion

The version of the content in the payload.

- used by: `request` (`optional`)

> Type: `xsd:string`
```http request
POST /
Host: https://www.example.com
ids-contentVersion: "1.4"
```
---

### ids-correlationMessage

Correlated message. Usually needed, if a messages responds to a previous message.
 A Connector may, e.g., send a MessageProcessedNotification as a response to an incoming message and therefore needs this property to refer to the incoming message.

- not applicable for RESTful interactions as the server is not enforced to remember previous interactions (state-less nature).
---

### ids-issued

DateTimeStamp of issuing the message. Default issuing time of a message is time when the HTTP message is sent. Can be used to indicate that the original source of the message - maybe a backend system proxied by the sending IDS Connector - has created the message at a different time.  

- used by: `request`  (`optional`)
- used by: `response` (`optional`)

Type: `xsd:dateTimestamp`
```http request
GET /
Host: https://www.example.com
ids-issued: "2019-12-02T08:25:08.245Z"
```
---

### ids-issuerConnector

Original Connector of the message.

- used by: `request`  (`optional`, default issuer Connector is the sender of the request)
- used by: `response` (`optional`, default issuer Connector is the sender of the response)

> Type: `xsd:anyURI`
```http request
GET /
Host: https://www.example.com
ids-issuerConnector: "https://www.example.com/Connector1/"
```
---

### ids-modelVersion

Information Model version, against which the Message should be interpreted. Should follow the pattern "[0-9]\.[0-9]\.[0-9]".

- used by: `request` (`mandatory`, if the body contains an IDS Entity)
- used by: `response` (`mandatory`, if the body contains an IDS Entity)

Type: `xsd:string`
```http request
PUT /
Host: https://www.example.com
ids-modelVersion: "4.0.0"
```
---

### ids-recipientAgent

Agent, for which the message is intended.

> - used by: `request` (`optional`,  implicitly defined by the target resource)
> - used by: `response` (`optional`, implicitly defined by the target resource)

> Type: `xsd:anyURI`
```http request
GET /
Host: https://www.example.com
ids-recipientAgent: "https://www.example.com/Agent1"
```
---

### ids-recipientConnector

Target Connector. Can be used if the target IDS Connector proxies other IDS Connectors.

- used by: `request` (`optional`, implicitly defined by the connector hosting the target resource)

> - used by: `response` (`optional`, implicitly defined by the connector hosting the target resource)

> Type: `xsd:anyURI`
```http request
GET /
Host: https://www.example.com
ids-recipientConnector: "https://www.example.com/Connector1"
```
---

### ids-securityToken

Dynamic Attribute Token (`DAT`) representing the security-related claims,
 for instance that the sender supports a certain security profile.

- used by: `request` (`mandatory`)
- used by: `response` (`mandatory`)

> Type: `xsd:string`
```http request
GET /
Host: https://www.example.com
ids-securityToken: "eyJhbGci..."
```
---

### ids-senderAgent

Agent who initiated the response. Can be used if the sending IDS Connector acts on behalf of (see user agents in REST) more than one IDS Participant.

> - used by: `request`  (`optional`)
> - used by: `response` (`optional`)

> Type: `xsd:anyURI`
```http request
GET /
Host: https://www.example.com
ids-senderAgent: "https://www.example.com/Agent1"
```
---

### ids-transferContract

The contract which is (or will be) the legal basis of the data transfer.

- used by: `request` (`optional`)
- used by: `response` (`optional`)

> Type: `xsd:anyURI`
```http request
GET /
Host: https://www.example.com
ids-transferContract: "https://www.example.com/ContractAgreement1"
```
---

## Http headers of LDP in alphabetical order

### Accept

A comma-separated list of MIME types, which the client wants to accept. Default is '*/*'.

- used by: `request` (`optional`)

> Type: mimetypes or mimetype patterns (e.g. <mimetype>/* or */*)
```http request
GET /
Host: https://www.example.com
Accept: "text/turtle, application/ld+json"
```
**see also**
- [https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Accept](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Accept)
---

### Accept-Post

A comma-separated list of allowed media types, for instance
 `text/turtle`, `application/ld+json` (for descriptions conforming
  to the IDS Information Model), or `application/octet-stream`
  (for binary data or IDS Artifacts).

- used by: `response` (`optional`)

> - Type: comma-separated list of mimetypes
```http reponse
/
Host: https://www.example.com
Accept-Post: "text/turtle, application/ld+json"
```

**see also**
- [https://www.w3.org/TR/ldp/#header-accept-post](https://www.w3.org/TR/ldp/#header-accept-post)
---

### Allow

The enabled HTTP methods, selection of GET, OPTIONS, HEAD, PUT, PATCH, POST, DELETE, CONNECT.

- used by: `response` (`mandatory`)

> - Type: comma-separated list of HTTP methods

Example:
```http response
Allow: "GET, OPTIONS, HEAD"
```

**see also**
- [https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Allow](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Allow)
---

### Content-Type

The MIME type of the request body.

- used by: `request` (`mandatory`, if a body is present)
- used by: `response` (`mandatory`, if a body is present)

> Type: `mimetype`
```http request
GET /
Host: https://www.example.com
Content-Type: "application/ld+json"
```

**see also**
- [https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Type](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Type)
---

### ETag

A unique version identifier representing the current state of
 the requested resource.

- used by: `response` (`mandatory`)

> Type: `xsd:string`
```http request
ETag: "42424242424242"
```

**see also**
- [https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/ETag](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/ETag)
---

### If-Match

The version of the resource the client wants to change.
 Is used to detect collisions. The client is informed by
 the server of correct versions through the `ETag` response
 header.

- used by: `request` (`mandatory`, if the client wants to overwrite a remote resource)

> - used by: `request` (`mandatory` for PUT and DELETE, `optional` else)

> Type: `xsd:string`
```http request
PUT /
Host: https://www.example.com
If-Match: "42"
```

**see also**
- [https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/If-Match](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/If-Match)
---

### Link

```
<IDS class URI>; rel="type"
```
and
```
<http://www.w3.org/ns/ldp#Resource>; rel="type"
```
and/or
```
<http://www.w3.org/ns/ldp#BasicContainer>; rel="type"
```


- used by: `response` (`mandatory`, if the body contains a LDP or IDS resource)

> Type: comma-separated list of relation statements
```http reponse
GET /
Host: https://www.example.com
Link: <https://w3id.org/idsa/core/DataResource>; rel="type", <http://www.w3.org/ns/ldp#Resource>; rel="type", <http://www.w3.org/ns/ldp#BasicContainer>; rel="type"
```

**see also**
- [https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Link](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Link)
---

### Location

The location of a newly created resource.

- used by: `response` (`mandatory` if a response to a POST that created a new accessible resource, else `optional`)

> Type: `xsd:anyURI`
```http response
POST /
Host: https://www.example.com
Location: "./spider-pig"
```


### Slug

The desired new name of a resource, which the client wants to create.

- used by: `request` (`optional`)

> Type: `xsd:string`
```http request
POST /
Host: https://www.example.com
Slug: "spider-pig"
```

**see also**
- [https://tools.ietf.org/html/rfc5023](https://tools.ietf.org/html/rfc5023)
- [https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html)
- [https://en.wikipedia.org/wiki/List_of_HTTP_header_fields](https://en.wikipedia.org/wiki/List_of_HTTP_header_fields)
- [https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers)



## Authors

| | | |  
| :--- | :--- | :--- |
| Sebastian Bader  | GitHub  | [sebbader](https://github.com/sebbader)  |
| Jörg Langkau     | Mail    | jlangkau@nicos-rd.com  |
|                  | GitHub  | [jlangkau](https://github.com/jlangkau)  |
|                  | twitter | [@LostInDataSpace](https://twitter.com/LostInDataSpace)  |

---
