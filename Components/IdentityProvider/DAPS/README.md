# Dynamic Attribute Provisioning Service (DAPS)

The infrastructure component "Dynamic Attribute Provisioning Service" (DAPS)
 in a given IDS ecosystem enables enrichment of identities of organisations
 and connectors with additional attributes.

DAPS can be understood (like the [Certificate Authority](../CA/README.md), too) as building block of the construct of an
 [IDS Identity Provider](../../glossary/README.md#identity-provider).



These attributes are embedded by the DAPS into a [Dynamic Attribute Token (DAT)]().
 The advantages of this technique of *dynamic attribute provisioning* instead
 of embedding them into [X.509 certificates](https://en.wikipedia.org/wiki/X.509)
 are:

- Attribute revocation does not enforce certificate revocation and re-issuance.
 This is also true if new attributes are added.
- By defining scopes or attribute sets, only the needed attributes can be
 included in the DAT. This limits information leakage, so only needed
 attributes are communicated.
- Baseline certificates can be issued to enable connector deployment
 in uncomplicated manner.
- More complex scenarios can be created as soon as attributes are
 assigned later on.

See also:
- [Glossary "Dynamic Attribute Provisioning Service"](../../../Glossary/README.md#dynamic-attribute-provisioning-service)
- Shortcut: `DAPS`

---

## Unique Identifier

Each connector in the IDS needs a valid, outlasting and unique
 identifier, never be re-used for any other resource inside the IDS ecosystem.

The architecture aims to be open to multiple [Certificate Authorities](../CA/README.md)
 (CAs) issuing certificates. This means, a truly unique identifier needs to consist of
  the issuer of the certificate and the subject identifier. For an easy machine readable
  identifier, two ´X.509v3´ extensions will be used:

### Subject Key Identifier (SKI)
### Authority Key Identifier (AKI)

The concatenation of ´SKI´ and ´AKI´ provides a unique identifier valid - even if multiple
 CAs are able to issue valid certificates.

EXAMPLE (snippet from a X.509 certificate):

```
...
X509v3 extensions:
    X509v3 Subject Key Identifier:
        DD:CB:FD:0B:93:84:33:01:11:EB:5D:94:94:88:BE:78:7D:57:FC:4A
    X509v3 Authority Key Identifier:
        keyid:CB:8C:C7:B6:85:79:A8:23:A6:CB:15:AB:17:50:2F:E6:65:43:5D:E8
...
```

... leads to a [connectors](../../Connector/README.md) `unique identifier`:

```
DD:CB:FD:0B:93:84:33:01:11:EB:5D:94:94:88:BE:78:7D:57:FC:4A:keyid:CB:8C:C7:B6:85:79:A8:23:A6:CB:15:AB:17:50:2F:E6:65:43:5D:E8
```

In examples and for reasons of readability editors might use

```
SKI:AKI
```

See also:
- [Glossary "Dynamic Attribute Token"](../../../Glossary/README.md#dynamic-attribute-token)
- Shortcut: `DAT`
- [X.509 certificates](https://en.wikipedia.org/wiki/X.509)

---


## Request token that is handed in at DAPS side

|**Field name**|**mandantory**|**cardinality**|**content**
|:---|:---|:---|:---|
|**`@context`**  | yes   | 1 | The JSON-LD context containing the IDS classes, properties and instances. Must be "https://w3id.org/idsa/contexts/context.jsonld". |
|**`@type`**     | yes   | 1 | In the context of the IDS, the request payload is an RDF instance and therefore must state that it has "@type" : "ids:DatRequestToken". |
|**`sub`**       | yes   | 1 | Subject the requesting connector the token is created for. This is the connector requesting the DAT. The "sub" value must be the  combined entry of the SKI and AKI of the IDS X509 as presented in Sec. 4.2.1.  In this context, this is identical to "iss". |
|**`exp`**       | yes   | 1 | Expiration date of the token. Can be chosen freely but should be limited to a short period of time (e.g., one minute). |
|**`iat`**       | yes   | 1 | Timestamp the token has been issued. |
|**`nbf`**       | yes   | 1 | "Valid not before": For practical reasons this should be identical to iat. If the system time is not in synch with the DAPS, the request token will be rejected (e.g., nbf is in the future). |
|**`aud`**       | yes   | 1 | The audience of the token. This can limit the validity for certain connectors. |
|**`iss`**       | yes   | 1 | According to RFC 7519 Sec. 4.1.1, the issuer is the component which created and signed the JWT. In the context of the IDS, this must be a valid connector. The "iss" value must be the combined entry of the SKI and AKI of the Connectors X509 certificate as presented in Sec. 4.2.1. |



## Request call to get a token

`client_assertion` is the base64 encoded request token, shown under
 ["Request token that is handed in at DAPS side"](#request-token-that-is-handed-in-at-daps-side).


|**FormBody Attribute**|**content**
|:---|:---|
|**`grant_type`**  | OAuth based grant type. Always uses client_credentials grant. |
|**`client_assertion_type`**  | See [JSON Web Token (JWT) Profile for OAuth 2.0 Client Authentication and Authorization Grants](https://tools.ietf.org/html/rfc7523). |
|**`client_assertion`**  | The signed and base64 encoded request token. Paste the example to jwt.io to see the decoded JWT. The token is signed with the connectors private key belonging to the public key contained in the X.509 certificate. |


### Example of a "request call to get a token"

```http request
POST /
Host: https://daps.example.com
Content-Type: application/x-www-form-urlencoded
"grant_type=client_credentials
 &client_assertion_type=urn:ietf:params:oauth:client-assertion-type:jwt-bearer
 &client_assertion=eyJhbGciOiJSUzI1NiJ9.eyJpc3MiOiJkZW1vY29ubmVjdG9yMSIsInN1YiI6ImRlbW9jb25uZWN0b3IxIiwiZXhwIjoxNTQ4Nzg1Mzg2LCJuYmYiOjE1NDg3ODE3ODYsImlhdCI6MTU0ODc4MTc4NiwiYXVkIjoiaHR0cHM6Ly9hcGkubG9jYWxob3N0In0.JSQuMf-9Fd7DNna_-s-sR7eXgcSYNCau5WgurrGJTuCSLKqhZe3odXfunN2vRFgUhU21ADFlEq96mlbQDueBlMtaXrcHFPSpIUtvuIMIVqQcGYkDdSJr_VmDuAykCYpyTCkLa7a8DTV-N3sECp-AxUgmEzYIfh8jW0WS6ehgUzrnpH6t_h_GWXKkNSAg3ERakDc4NY02pBGmiN7bmtLZNt5b4LWALiiFiduC7JbIpx4awOU6skMApmzgLnZmmTG20JlJRg6hAqyHEz5Cd4rUgrt0twmpC0Us_CG23KdUF5fWI55dcO2qAVvhNQXpqz7IiPcF7-jgkrx4oukYNY6eHA"
 &scope=ids_connector_attributes"

```

> **!!!** REMARK: NO line breaks in front of '&', done for better readability only **!!!**

See also:
- [JSON-LD](https://en.wikipedia.org/wiki/JSON-LD)
- [URI](https://en.wikipedia.org/wiki/Uniform_Resource_Identifier)

---


## Dynamic Attribute Token Content

The DAPS issues the requested DAT, if authentication succeeds, embedded in a JSON Object under the key _access_token_, e.g.

```
{
    "access_token": "<DAT>",
    "scope": "ids_connector_attributes",
    "token_type": "bearer",
    "expires_in": "3600"
}
```

For details about this format, please refer to [RFC 6749, Section 5.1](https://datatracker.ietf.org/doc/html/rfc6749#section-5.1)

The DAT itself is represented as an encoded JSON Web Token.
Decoded it has these header fields:

|**Field name**|**mandantory**|**cardinality**|**content**
|:---|:---|:---|:---|
|**`typ`**             | yes    | 1    | The token type. Must be "JWT". |
|**`kid`**             | yes    | 1    | Key id used to sign that token. Must match the jwks.json entry found at daps-url/.well-known/jwks.json |
|**`alg`**             | yes    | 1    | Algorithm used to sign the token.   |

The DAT has these payload fields:

|**Field name**|**mandantory**|**cardinality**|**content**
|:---|:---|:---|:---|
|**`@context`**             | yes    | 1    | The JSON-LD context containing the IDS classes, properties and instances. Must be "https://w3id.org/idsa/contexts/context.jsonld". |
|**`@type`**                | yes    | 1    | In the context of the IDS, the request payload is an RDF instance and therefore must state that it has "@type" : "ids:DatRequestToken".
|**`iss`**                  | yes    | 1    | According to RFC 7519 Sec. 4.1.1, the issuer is the component which created and signed the JWT. In the context of the IDS, this must be a valid DAPS. The "iss" value must be a valid URL for the DAPS such as "https://daps.aisec.fraunhofer.de".  |
|**`sub`**                  | yes    |      | Subject the requesting connector the token is created for. This is the connector requesting the [DAT](#dynamic-attribute-token-dat). The `sub` value must be the  combined entry of the `SKI` and `AKI` of the IDS X509 as presented in Sec. 4.2.1.  In this context, this is identical to `iss`. |
|**`exp`**                  | yes    | 1    | Expiration date of the token. Can be chosen freely but should be limited to a short period of time (e.g., one minute).  |
|**`iat`**                  | yes    | 1    | Timestamp the token has been issued.  |
|**`nbf`**                  | yes    | 1    | "Valid not before" (`nbf`): for practical reasons this should be identical to `iat`. If systems time is not aynchronized with the DAPS, the request token will be rejected (so, `nbf` is in the future).  |
|**`aud`**                  | yes    | 1    | The audience of the token. This can limit the validity for certain connectors. |
|**`scope`**                | yes    | 1..* | List of scopes. Currently, the scope is limited to "ids_connector_attributes" but can be used for scoping purposes in the future. |
|**`securityProfile`**      | yes    | 1    | States that the requesting connector conforms to a certain security profile and has been certified to do so. The value must be an URI, in particular an instance of the ids:SecurityProfile class. |
|**`referringConnector`**   | `opt`  | 0..1 | The URI of the subject, the connector represented by the DAT. Is used to connect identifier of the connector with the self-description identifier as defined by the IDS Information Model. A receiving connector can use this information to request more information at a Broker or directly by dereferencing this URI. |
|**`transportCertsSha256`** | `opt`  | 0..* | Contains the public keys of the used transport certificates. The identifying X509 certificate should not be used for the communication encryption. Therefore, the receiving party needs to connect the identity of a connector by relating its hostname (from the communication encryption layer) and the used private/public key pair, with its IDS identity claim of the DAT. The public transportation key must be one of the "transportCertsSha256" values. Otherwise, the receiving connector must expect that the requesting connector is using a false identity claim. |
|**`extendedGuarantee`**    | `opt`  | 0..* | In case a connector fulfills a certain security profile but deviates for a subset of attributes, it can inform the receiving connector about its actual security features. This can only happen if a connector reaches a higher level for a certain security attribute than the actual reached certification asks for. A deviation to lower levels is not possible, as this would directly invalidate the complete certification level. |


## Dynamic Attribute Token (DAT)

An example of a complete decoded DAT, including header and payload is shown below:

```
{
    "typ": "JWT",
    "kid": "default",
    "alg": "HS256"
}
.
{
    "@context": "https://w3id.org/idsa/contexts/context.jsonld",
    "@type": "ids:DatPayload",  
    "iss": "https://daps.aisec.fraunhofer.de",
    "sub": "DD:CB:FD:0B:93:84:33:01:11:EB:5D:94:94:88:BE:78:7D:57:FC:4A:keyid:CB:8C:C7:B6:85:79:A8:23:A6:CB:15:AB:17:50:2F:E6:65:43:5D:E8",
    "referringConnector": "http://some-connector-uri.com",
    "securityProfile": "idsc:BASE_SECURITY_PROFILE",
    "extendedGuarantee": "idsc:USAGE_CONTROL_POLICY_ENFORCEMENT",
    "transportCertsSha256": ["bacb879575730bb083f283fd5b67a8cb..." ],
    "iat": 1516239022,
    "exp": 1516239032,
    "aud": "https://w3id.org/idsa/code/IDS_CONNECTORS_ALL",
    "nbf": 1567703561,
    "scope": "ids_connector_attributes"
}
.
<signature>
```

See also:
- [Info Model : idsc:USAGE_CONTROL_POLICY_ENFORCEMENT](https://github.com/International-Data-Spaces-Association/InformationModel/blob/develop/codes/SecurityGuarantee.ttl)
- [Glossary "Dynamic Attribute Token"](../../../Glossary/README.md#dynamic-attribute-token)
- Shortcut: `DAT`

---

## Requests

Collection of valid request templates can be found [here](./requests/README.md).

## REST API description

REST API description can be found
 [here](https://app.swaggerhub.com/apis/IDS_Association/DynamicAttributeProvisioningService).

---
