# IDS-ParIS GET root request

## Summary

[ParIS'](../README.md) Self-Description.


## Description

Request the [ParIS'](../README.md) Self-Description in JSON-LD.


## Examples

### 1 request

Remark: `ids-securityToken`is mandantory.

```http request
GET /
host: https://www.example.com/ParIS-example
accept: application/json
ids-modelVersion: 4.1.0
ids-issued: 2021-07-21T17:32:28Z
ids-correlationMessage: http://example.org/some-related-message
ids-issuerConnector: http://example.org/some-sending-connector/
ids-recipientConnector: http://example.org/some-target-connector/
ids-senderAgent: http://example.org/some-participant/
ids-recipientAgent: http://example.org/some-participant/
ids-securityToken: eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCIsImtpZCI6ImRlZmF1bHQifQ.eyJzZWN1cml0eV9sZXZlbCI6Nywic2NvcGVzIjpbImlkc19jb25uZWN0b3IiLCJzZWN1cml0eV9sZXZlbCJdLCJhdWQiOiJJRFNfQ29ubmVjdG9yIiwiaXNzIjoiaHR0cHM6Ly9kYXBzLmFpc2VjLmZyYXVuaG9mZXIuZGUiLCJzdWIiOiI3MTgxMGZmZi00MTM5LTQ5ZDAtYjBhNC03ZDAyYjliNTRkMTUiLCJuYmYiOjE1NTk3MzAyNTAsImV4cCI6MTU1OTczMzg1MH0.oS5kZtFhqn1jUSBtmCKSU0MzcVAxhfW7BCa4oIo_aQi6Qcc-FN-bBhmx1fXgD57HqXaV7r0kxJrDsoDGo4AE1qG384HhSzFJOqA6Nws7n0OorkjsOIFMM4ulPYNKy2srUAb9EeDaAWb6uGA5bbk5E1yKM73ub_U8OF1W4OmgUCOFizxOPY8HEnklsk6eiqrAFFCebp-BtuaVp1p7U9SotpcFxoq235OwRWz2aFiAr9v53VLJzlBYPGsuqdBZ1lpOUQXCMbDfqWXfyNSa0uDPeHKP_Xj1tUug0g-WXjGsZIMzXHPvAM-syw1SweYq_c4ulpZyABSYPSwEfufdtaO_Iw
ids-transferContract: http://example.org/some-suitable-contract/
ids-contentVersion: 1.0.5
```


### 1 response

Header of response is dropped - the self-description of this [ParIS](../README.md) in JSON-LD:

```json
{
  "@context": "http://w3id.org/idsa/contexts/context.json",
  "@id": "https://www.example.com/ParIS-example",
  "ids:description": {
    "@value": "This is a description of a ParIS in a commonly known language",
    "@language": "en"
  },
  "ids:curator": "https://www.example.com/Alice",
  "ids:maintainer": "https://www.example.com/Bob",
  "ids:inboundModelVersion": [
    "4.0.0",
    "4.1.0"
  ],
  "ids:outboundModelVersion": "3.0.1",
  "ids:title": {
    "@value": "A text in a commonly known language",
    "@language": "en"
  },
  "ids:catalog": {
    "@id": "http://some-uri-unique-to-the-entity-described-by-this-JSON-LD",
    "@type": [
      "ids:Catalog",
      "ldp:BasicContainer"
    ],
  },
  "ldp:contains": {
    "@id": "Catalog"
  },
  "@type": "ids:ParIS"
}
```

---
