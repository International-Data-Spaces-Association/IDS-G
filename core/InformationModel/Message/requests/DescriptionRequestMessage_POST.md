# DescriptionRequestMessage POST


## Summary

## Description


## Examples

### 1 request

```http request
POST /infrastructure
Host: https://conn1.example.com

----------------------------949567736778671771657427
Content-Disposition: form-data; name="header"

{
  "@context": "https://w3id.org/idsa/contexts/context.jsonld",
  "@id": "https://w3id.org/idsa/autogen/selfDescriptionRequest/b0731661-7df1-43e5-bb75-50f0709f31c9"
  "@type": "ids:DescriptionRequestMessage",
  "modelVersion": "3.1.0",
  "issued": "2020-05-21T13:32:33.073+02:00",
  "issuerConnector": "http://example.org#connector",
  "securityToken": {
    "@type": "ids:DynamicAttributeToken",
    "tokenFormat": "https://w3id.org/idsa/code/tokenformat/JWT",
    "tokenValue": "eyJhbGciOiJSUzI1NiIsInR5cCI..."
  }  
}
----------------------------949567736778671771657427--

```

### 1 response



```http request
--mPLw1UTMYjqqYqh1Bb_ttWBKcdSPfB9FBgz3
Content-Disposition: form-data; name="header"
Content-Type: application/json
Content-Length: 409

{
  "@context": "https://w3id.org/idsa/contexts/context.jsonld",
  "@id": "https://w3id.org/idsa/autogen/selfDescriptionResponse/851e3218-2bb7-45f9-8795-7f99c1f19680",
  "@type": "ids:DescriptionResponseMessage",
  "issued": "2020-05-21T13:11:14.596Z",
  "issuerConnector": "https://broker.ids.isst.fraunhofer.de/",
  "correlationMessage": "https://w3id.org/idsa/autogen/selfDescriptionRequest/b0731661-7df1-43e5-bb75-50f0709f31c9",
  "modelVersion": "3.1.0",
  "securityToken": {
    "@type": "ids:DynamicAttributeToken",
    "tokenFormat": "https://w3id.org/idsa/code/tokenformat/JWT",
    "tokenValue": "ex42..."
  }
}
--mPLw1UTMYjqqYqh1Bb_ttWBKcdSPfB9FBgz3
Content-Disposition: form-data; name="payload"
Content-Type: application/ld+json
Content-Length: 965

{
  "@context" : "https://w3id.org/idsa/contexts/context.jsonld",
  "@type" : "ids:Broker",
  "outboundModelVersion" : "3.1.0",
  "description" : [ {
    "@value" : "A Broker with a graph persistence layer",
    "@language" : "en"
  } ],
  "inboundModelVersion" : [ "3.1.0" ],
  "title" : [ {
    "@value" : "EIS Broker",
    "@language" : "en"
  } ],
  "maintainer" : "https://www.iais.fraunhofer.de",
  "curator" : "https://www.iais.fraunhofer.de",
  "catalog" : {
    "@type" : "ids:Catalog",
    "@id" : "https://w3id.org/idsa/autogen/catalog/a50c93a3-388b-4bbd-959e-a446b7ec2946"
  },
  "securityProfile" : {
    "@type" : "ids:SecurityProfile",
    "basedOn" : {
      "@type" : "ids:SecurityProfile",
      "@id" : "https://w3id.org/idsa/core/Level0SecurityProfile"
    },
    "@id" : "https://w3id.org/idsa/autogen/securityProfile/cca6c0e6-ad34-4d66-8137-ab94e3fad424"
  },
  "@id" : "https://broker.ids.isst.fraunhofer.de/"
}
--mPLw1UTMYjqqYqh1Bb_ttWBKcdSPfB9FBgz3--

```

## REST API description

REST API description can be found here.

---
