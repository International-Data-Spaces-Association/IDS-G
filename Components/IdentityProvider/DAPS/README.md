# Dynamic Attribute Provisioning Service (DAPS)

The infrastructure component "Dynamic Attribute Provisioning Service" (DAPS)
in a given IDS ecosystem enables enrichment of identities of organisations
and connectors with additional attributes.

DAPS can be understood (like the [Certificate Authority](../CA/README.md), too) as building block of the construct of an
[IDS Identity Provider](../../glossary/README.md#identity-provider).

These attributes are embedded by the DAPS into a [Dynamic Attribute Token (DAT)]().
The advantages of this technique of *dynamic attribute provisioning* instead
of embedding them into [X.509 certificates](https://en.wikipedia.org/wiki/X.509) are:

- Attribute revocation does not enforce certificate revocation and re-issuance.
 This is also true if new attributes are added.
- By defining scopes or attribute sets, only the needed attributes can be
 included in the DAT. This limits information leakage, so only needed
 attributes are communicated.
- Baseline certificates can be issued to enable connector deployment
 in uncomplicated manner.
- More complex scenarios can be created as soon as attributes are
 assigned later on.

---

## Token Request

To request a Dynamic Attribute Token,
a connector must send an [OAuth2 Access Token Request with a Client Credentials Grant Type](https://datatracker.ietf.org/doc/html/rfc6749#section-4.4)
to the corresponding DAPS Token Endpoint.
As authentication mechanism, a [JWT-Bearer-Token type](https://datatracker.ietf.org/doc/html/rfc7523#section-2.2) [Client Assertion](https://datatracker.ietf.org/doc/html/rfc7521#section-4.2) is used.

Additionally, the following restrictions must be met within the request parameters:

|**FormBody Attribute**|**content**
|:---|:---|
| **scope** | Must include `idsc:IDS_CONNECTOR_ATTRIBUTES_ALL` |
| **client_id** | Must be the connector's ID |
| **client_assertion** | Must be Base64-encoded, signed using the key pre-registered at the DAPS, and contain the claims described below |

The JWT-Bearer itself adheres to [RFC 7519](https://www.rfc-editor.org/rfc/rfc7519) and [RFC 7515](https://datatracker.ietf.org/doc/html/rfc7515) in particular.
It contains [JSON-LD](https://www.w3.org/TR/json-ld11/) data, and must additionally contain all of the following claims:

|**Field name**|**additional restrictions**
|:---|:---|:---|:---|
|**@context**  | Must be `https://w3id.org/idsa/contexts/context.jsonld` |
|**@type**     | Must be `ids:DatRequestToken` |
|**sub**       | Must be the connector's ID |
|**iss**       | Must be the connector's ID |
|**aud**       | Must be `idsc:IDS_CONNECTORS_ALL` |
|**iat**       | Must be present |
|**nbf**       | Must equal **iat** |
|**exp**       | Should be limited to a short time period (Recommendation: 1 Minute) |

Future revisions of this document may allow for differing audiences and additional parameters.

### Example

The following is a non-normative and abbreviated example of a DAT request.
Line breaks are purely for readability purposes.

```http request
POST /token
Host: https://daps.example.com
Content-Type: application/x-www-form-urlencoded

grant_type=client_credentials
&client_assertion_type=urn:ietf:params:oauth:client-assertion-type:jwt-bearer
&client_assertion=eyJhbGciOiJSUzI1NiJ9.eyJ[...]N0In0.JSQu[...]gkrx4oukYNY6eHA
&scope=ids_connector_attributes
```

---

## Token Response

The DAPS issues the requested DAT, or an error response, as per [RFC 6749](https://datatracker.ietf.org/doc/html/rfc6749#section-5).
The Access Token ("the DAT") itself is a JWT adhering to [RFC 9068](https://datatracker.ietf.org/doc/html/rfc9068),
which in turn contains [JSON-LD](https://www.w3.org/TR/json-ld11/) encoded data, subject to the following additional constraints:

|**Field name**|**additional constraints**
|:---|:---|:---|:---|
|**@context**             | Must be `https://w3id.org/idsa/contexts/context.jsonld` |
|**@type**                | Must be `ids:DatPayload` |
|**iss**                  | Must be the DAPS URI |
|**sub**                  | Must be the requesting connector's ID |
|**exp**                  | Should be limited to a short time period (Recommendation: 1 hour) |
|**nbf**                  | Must equal `iat` |
|**aud**                  | Should include `idsc:IDS_CONNECTORS_ALL`. Alternatively, individual connector IDs may be specified. |
|**scope**                | Must include `idsc:IDS_CONNECTOR_ATTRIBUTES_ALL` |
|**securityProfile**      | Must be an instance of the [`ids:SecurityProfile` class](https://w3id.org/idsa/core/SecurityProfile) |

Future revisions of this document may allow for mechanisms to specify connectors to be listed in the `aud` claim such as through [RFC 8707](https://datatracker.ietf.org/doc/html/rfc8707).

Additional claims may optionally be present. This specification defines the following:

* **referringConnector**
An optional URI of the subject.
Is used to connect identifier of the connector with the self-description identifier as defined by the IDS Information Model.
A receiving connector can use this information to request more information at a Broker or directly by dereferencing this URI.

* **transportCertsSha256**
Contains the public keys of the used transport certificates, hashed using SHA256.
The identifying X509 certificate should not be used for the communication encryption.
Therefore, the receiving party needs to connect the identity of a connector by relating its hostname (from the communication encryption layer) and the used private/public key pair,
with its IDS identity claim of the DAT.
The public transportation key must be one of the `transportCertsSha256` values.
Otherwise, the receiving connector must expect that the requesting connector is using a false identity claim.
Multiple values can be specified within a single string, separated by a single space character.

* **extendedGuarantee**
In case a connector fulfills a certain security profile but deviates for a subset of attributes,
it can inform the receiving connector about its actual security features.
This can only happen if a connector reaches a higher level for a certain security attribute than the actual reached certification asks for.
A deviation to lower levels is not possible, as this would directly invalidate the complete certification level.
Multiple values can be specified within a single string, separated by a single space character.


## Example

The following is an example of a sucessful response:

```http response
200 This is fine
Content-Type: application/json

{
    "access_token": "skdj54dkGjnb[...]lsl8723ijsdfuzticby_ch",
    "scope": "idsc:IDS_CONNECTOR_ATTRIBUTES_ALL",
    "token_type": "bearer",
    "expires_in": "3600"
}
```

The decoded DAT, including header and payload is shown below:

```json
{
    "typ": "jwt+at",
    "kid": "somekid",
    "alg": "HS256"
}
.
{
    "@context": "https://w3id.org/idsa/contexts/context.jsonld",
    "@type": "ids:DatPayload",  
    "iss": "https://daps.aisec.fraunhofer.de",
    "sub": "DD:CB:FD:0B:93:84:33:01:11:EB:5D:94:94:88:BE:78:7D:57:FC:4A:keyid:CB:8C:C7:B6:85:79:A8:23:A6:CB:15:AB:17:50:2F:E6:65:43:5D:E8",
    "nbf": 1516239022,
    "iat": 1516239022,
    "exp": 1516239032,
    "aud": ["idsc:IDS_CONNECTORS_ALL"],
    "scope": "idsc:IDS_CONNECTOR_ATTRIBUTES_ALL"
    "referringConnector": "http://some-connector-uri.com",
    "securityProfile": "idsc:BASE_SECURITY_PROFILE",
    "extendedGuarantee": "idsc:USAGE_CONTROL_POLICY_ENFORCEMENT",
    "transportCertsSha256": "bacb879575730bb083f283fd5b67a8cb..."
}
.
somesignature
```

See also:
- [Glossary "Dynamic Attribute Provisioning Service"](../../../Glossary/README.md#dynamic-attribute-provisioning-service)
- [Glossary "Dynamic Attribute Token"](../../../Glossary/README.md#dynamic-attribute-token)
- [Info Model : idsc:USAGE_CONTROL_POLICY_ENFORCEMENT](https://github.com/International-Data-Spaces-Association/InformationModel/blob/develop/codes/SecurityGuarantee.ttl)
- [JSON-LD](https://en.wikipedia.org/wiki/JSON-LD)
- [URI](https://en.wikipedia.org/wiki/Uniform_Resource_Identifier)
- Shortcuts: `DAT`, `DAPS`

