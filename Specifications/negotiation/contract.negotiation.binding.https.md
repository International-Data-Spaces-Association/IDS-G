# Contract Negotiation HTTPS Binding

## 1 Introduction

This specification defines a RESTful API over HTTPS for the [Contract Negotiation Protocol](./contract.negotiation.protocol.md).

The OpenAPI definitions for this specification can be accessed [here](TBD).

## 2 Provider Path Bindings

### 2.1 Prerequisites

1. The `<base>` notation indicates the base URL for a connector endpoint. For example, if the base connector URL is `connector.example.com`, the
   URL `https://<base>/negotiation/request` will map to `https//connector.example.com/negotiation/request`.

2. All request and response messages must use the `application/json` media type.

### 2.2 NegotiationErrorMessage

In the event of a client request error, the connector must return an appropriate HTTP 4xxx client error code. If an error body is returned it must be
a [ContractNegotiationErrorMessage](./message/contract.negotiation.error.message.json) with the following properties:

| Field         | Type          | Description                                                 |
|---------------|---------------|-------------------------------------------------------------|
| negotiationId | UUID          | The contract negotiation unique id.                         |
| code          | string        | An optional implementation-specific error code.             |
| reasons       | Array[object] | An optional array of implementation-specific error objects. |

### 2.2.1 State transition errors

If a client or provider connector makes a request that results in an invalid contract negotiation state transition as defined by the Contract Negotiation Protocol, it must return
an HTTP code 400 (Bad Request) with an NegotiationErrorMessage in the response body.

### 2.4 The provider `negotiations` resource

#### 2.4.1 GET

```
GET https://connector.provider.com/negotiations/:id

Authorization: ...

```

If the negotiation is found and the client is authorized, the provider connector must return an HTTP 200 (OK) response and a body containing
the [ContractNegotiation](./message/contract.negotiation.json):

```
{
  "@context": {
    "ids": "https://idsa.org/"
  },
  "@type": "ids:ContractNegotiation"
  "@id": "urn:uuid:dcbf434c-eacf-4582-9a02-f8dd50120fd3",
  "ids:state" :"consumer_requested"
}  
```

If the negotiation does not exist or the client is not authorized, the provider connector must return an HTTP 404 (Not Found) response.

### 2.5 The provider `negotiations/request` resource

#### 2.5.1 POST

A contract negotiation is started and placed in the `CONSUMER_REQUESTED` state when a consumer POSTs
a [ContractRequestMessage](./contract.negotiation.protocol.md#1-contractrequestmessage)to `negotiations/request`:

```
POST https://connector.provider.com/negotiations/request

Authorization: ...

{
  "@context": {
    "ids": "https://idsa.org/"
  },
  "@type": "ids:ContractRequest"
  "@id": "urn:uuid:dcbf434c-eacf-4582-9a02-f8dd50120fd3",
  "dataSet": "urn:uuid:3dd1add8-4d2d-569e-d634-8394a8836a88",
  "offer": "urn:uuid:2828282:3dd1add8-4d2d-569e-d634-8394a8836a88",
  "callbackAddress": "https://......"
}
```

The `Authorization` header is optional if the contract negotiation service does not require authorization. If present, the contents of the `Authorization` header are detailed in
the [Authorization section](#authorization).

The `callbackAddress` property specifies the base endpoint `URL` where the client receives messages associated with the contract negotiation. Support for the `HTTPS` scheme is
required. Implementations may optionally support other URL schemes.

Callback messages will be sent to paths under the base URL as described by this specification. Note that provider connectors should properly handle the cases where a trailing `/`
is included with or absent from the `callbackAddress` when resolving full URL.

The @id is the correlation id that will be used for callback messages.

The provider connector must return an HTTP 201 (Created) response with the location header set to the location of the contract negotiation and a body containing
the [ContractNegotiation](./message/contract.negotiation.json) message:

```
Location: /negotiations/urn:uuid:dcbf434c-eacf-4582-9a02-f8dd50120fd3

{
  "@context": {
    "ids": "https://idsa.org/"
  },
  "@type": "ids:ContractNegotiation"
  "@id": "urn:uuid:dcbf434c-eacf-4582-9a02-f8dd50120fd3",
  "ids:state" :"consumer_requested"
}
```

Note that if the location header is an absolute URL, it must resolve to an address that is relative to the base address of the request.

### 2.6 The provider `negotiations/:id/offers` resource

#### 2.6.1 POST

A consumer may make an offer by POSTing a [ContractOfferMessage](./message/contract.offer.message.json) to `negotiations/:id/offers`:

```
POST https://connector.provider.com/negotiations/urn:uuid:dcbf434c-eacf-4582-9a02-f8dd50120fd3/offers

Authorization: ...

{
  "@context": {
    "ids": "https://idsa.org/",
    "odrl": "https://www.w3.org/TR/odrl-model"
  },
  "@type": "ids:ContractOfferMessage",
  "ids:negotiationId": "urn:uuid:dcbf434c-eacf-4582-9a02-f8dd50120fd3",
  "offer": {
    "@type": "odrl:Offer",
    "uid": "...",
    "target": "urn:uuid:3dd1add8-4d2d-569e-d634-8394a8836a88"
  },
  "checksum": "..."
}
```

If the message is successfully processed, the provider connector must return and HTTP 200 (OK) response. The response body is not specified and clients are not required to process
it.

### 2.7 The provider `negotiations/:id/offers/events` resource

NOTE: We could use `negotiations/:id/offers/current/events` as an alternative

#### 2.7.1 POST

A consumer connector can POST a [ContractOfferEventMessage](./message/contract.offer.event.message.json) to `negotiations/:id/offers/events` to accept or decline the current
provider contract offer. If the negotiation state is successfully transitioned, the provider must return HTTP code 200 (OK). The response body is not specified and clients are not
required to process it.

If the current contract offer was created by the consumer, the provider must return HTTP code 400 (Bad Request) with an NegotiationErrorMessage in the response body.

### 2.8 The provider `negotiations/:id/agreement/verification` resource

#### 2.8.1 POST

The consumer connector can POST a [ContractAgreementVerificationMessage](./message/contract.agreement.verification.message.json) to verify an agreement. If the negotiation state is
successfully transitioned, the provider must return HTTP code 200 (OK). The response body is not specified and clients are not required to process it.

```
POST https://connector.provider.com/negotiations/urn:uuid:a343fcbf-99fc-4ce8-8e9b-148c97605aab/agreement/verification

Authorization: ...

{
  "@context": {
    "ids": "https://idsa.org/",
    "odrl": "https://www.w3.org/TR/odrl-model"
  },
  "@type": "ids:ContractAgreementVerificationMessage",
  "negotiationId": "urn:uuid:a343fcbf-99fc-4ce8-8e9b-148c97605aab",
  "ids:consumerSignature": {
    "timestamp": 121212,
    "hash": "....",
    "signature": ""
  }
}

```

### 2.9 The provider `negotiations/:id/cancellation` resource

#### 2.9.1 POST

The consumer connector can POST a [ContractNegotiationCancellationMessage](./message/contract.negotiation.cancellation.message.json) to verify an agreement. If the negotiation
state is successfully transitioned, the provider must return HTTP code 200 (OK). The response body is not specified and clients are not required to process it.

## 3 Consumer Callback Path Bindings

### 3.1 Prerequisites

All callback paths are relative to the `callbackAddress` base URL specified in the `ContractRequestMessage` that initiated a contract negotiation. For example, if
the `callbackAddress` is specified as `https://connector.consumer/callback` and a callback path binding is `negotiations/:id/offers`, the resolved URL will
be `https://connector.consumer.com/callback/negotiations/:id/offers`.

### 3.2 The consumer `negotiations/:id/offers` resource

#### 3.2.1 POST

A provider may make an offer by POSTing a [ContractOfferMessage](./message/contract.offer.message.json) to the `negotiations/:id/offers` callback:

```
POST https://connector.consumer.com/callback/negotiations/urn:uuid:dcbf434c-eacf-4582-9a02-f8dd50120fd3/offers

Authorization: ...

{
  "@context": {
    "ids": "https://idsa.org/",
    "odrl": "https://www.w3.org/TR/odrl-model"
  },
  "@type": "ids:ContractOfferMessage",
  "ids:negotiationId": "urn:uuid:dcbf434c-eacf-4582-9a02-f8dd50120fd3",
  "offer": {
    "@type": "odrl:Offer",
    "uid": "...",
    "target": "urn:uuid:3dd1add8-4d2d-569e-d634-8394a8836a88"
  },
  "checksum": "..."
}
```

If the message is successfully processed, consumer provider connector must return and HTTP 200 (OK) response. The response body is not specified and clients are not required to
process it.

### 3.3 The consumer `negotiations/:id/agreement` resource

#### 3.3.1 POST

The provider connector can POST a [ContractAgreementMessage](./message/contract.agreement.message.json) to the `negotiations/:id/agreement` callback to create an agreement. If the
negotiation state is successfully transitioned, the consumer must return HTTP code 200 (OK). The response body is not specified and clients are not required to process it.

```
POST https://connector.consumer.com/negotiations/urn:uuid:a343fcbf-99fc-4ce8-8e9b-148c97605aab/agreement

Authorization: ...

{
  "@context": {
    "ids": "https://idsa.org/",
    "odrl": "https://www.w3.org/TR/odrl-model"
  },
  "@type": "ids:ContractAgreementMessage",
  "negotiationId": "urn:uuid:a343fcbf-99fc-4ce8-8e9b-148c97605aab",
  "odrl:agreement": {
    "@type": "odrl:Agreement",
    "uid": "e8dc8655-44c2-46ef-b701-4cffdc2faa44",
    "ids:providerSignature": {
      "timestamp": 121212,
      "hash": "...",
      "signature": "..."
    }
  },
  "checksum": "..."
}
```

### 3.4 The consumer `negotiations/:id/offers/events` resource

NOTE: We could use `negotiations/:id/offers/current/events` as an alternative

#### 3.4.1 POST

A provider connector can POST a [ContractOfferEventMessage](./message/contract.offer.event.message.json) to the `negotiations/:id/offers/events` callback to decline the current
provider contract offer. If the negotiation state is successfully transitioned, the consumer must return HTTP code 200 (OK). The response body is not specified and clients are not
required to process it.

If the provider connector sends a `ContractOfferEventMessage` with an `eventType` other than `decline`, the consumer must return HTTP code 400 (Bad Request) with an
NegotiationErrorMessage in the response body

If the current contract offer was created by the provider, the consumer must return HTTP code 400 (Bad Request) with an NegotiationErrorMessage in the response body.

### 3.4 The consumer `negotiations/:id/agreement/events` resource

#### 3.4.1 POST

A provider can POST a [ContractAgreementEventMessage](./message/contract.agreement.event.message.json) to the `negotiations/:id/agreement/events` callback with an `eventType`
of `finanlize` to finalize a contract agreement. If the negotiation state is successfully transitioned, the consumer must return HTTP code 200 (OK). The response body is not
specified and clients are not required to process it. 


