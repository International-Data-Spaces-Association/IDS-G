# Catalog Protocol

## 1 Introduction: Terms

This document outlines the catalog protocol. The following terms are used:

- A _**message type**_ defines the structure of a _message_.
- A _**message**_  is an instantiation of a _message type_.
- A _**catalog**_ is a [DCAT catalog](https://www.w3.org/TR/vocab-dcat-3/) offered by a _provider_
- a _**catalog service**_ is a provider participant agent that advertises offered assets
- A _**consumer**_ is a participant agent that requests access to an offered asset.

The catalog protocol defines a how a `Catalog` is requested from a catalog service by a consumer using an abstract message exchange format. The concrete message exchange wire
format is defined in binding specifications.

## 2 Message Types

All messages must be serialized in JSON-LD compact form as specified in the [JSON-LD 1.1 Processing Algorithms and API](https://www.w3.org/TR/json-ld11-api/#compaction-algorithms).
Future IDS specifications may define additional optional serialization formats.

### 2.1 CatalogRequestMessage

**Sent by**: Consumer

**Example**:

```
{
  "@context: {
     "ids": "https://idsa.org/"
   },
  "@type": "ids:CatalogRequest"
  "ids:filter": {}
}
```

**Response**: [CatalogMessage](./message/catalog.message.json) containing the [DCAT catalog](https://www.w3.org/TR/vocab-dcat-3/#Class:Catalog).

**Schema**: (xx)[]

#### Description

The `CatalogRequestMessage` is message sent by a consumer to a catalog service. The catalog service must respond with a `CatalogMessage,` which is a
valid [DCAT Catalog](https://www.w3.org/TR/vocab-dcat-3/#Class:Catalog).

The `CatalogRequestMessage` may have a `filter` property which contains an implementation-specific query or filter expression type supported by the catalog service.

The catalog service may require an authorization token. Details for including that token can be found in the relevant catalog binding specification. Similarly, pagination may
be defined in the relevant catalog binding specification.

## 3 Technical Considerations

### 3.1 Queries and Filter Expressions

A _**catalog service**_ may support catalog queries or filter expressions as an implementation-specific feature. However, it is expected that query capabilities will be implemented
by the consumer against the results of a `CatalogRequestMessage,` as the latter is an RDF vocabulary. Client-side querying can be scaled by periodically crawling provider catalog
services, caching the results, and executing queries against the locally-stored catalogs.

### 3.2 Replication Protocol

The catalog protocol is designed to be used by federated services without the need for a replication protocol. Each consumer is responsible for issuing requests
to 1..N catalog services, and managing the results. It follows that a specific replication protocol is not needed, or more precisely, each consumer replicates data from catalog
services by issuing `CatalogRequestMessages`.

The discovery protocol adopted by a particular dataspace defines how a consumer discovers catalog services.

### 3.3 Security

It is expected (although not required) that catalog services implement access control. A catalog as well as individual catalog _datasets_ may be restricted to trusted parties.
The catalog service may require consumers to include a security token along with a `CatalogRequestMessage.` The specifics of how this is done can be found in the relevant
catalog binding specification. In addition, this specification does not define the contents of the security token. It is expected different security mechanisms may be used such
as [OAuth2 2.1](https://datatracker.ietf.org/doc/html/draft-ietf-oauth-v2-1-06) or [did:web](https://w3c-ccg.github.io/did-method-web/).

### 3.4 Catalog Brokers

A dataspace may include _**catalog brokers**_. A catalog broker is a consumer that has trusted access to 1..N upstream catalog services and advertises their respective catalogs as
a single catalog service. The broker is expected to honor upstream access control requirements.

## 4 DCAT and ODRL Profiles

The catalog is a DCAT catalog with the following restrictions:

1. Each ODRL `Offer` must be unique to a dataset since the target of the offer is derived from its enclosing context.
2. Each ODRL `Offer` must NOT include an explicit `target` attribute. 
