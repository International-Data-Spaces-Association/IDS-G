# Dynamic Attribute Provisioning Service (DAPS)

The infrastructure component "Dynamic Attribute Provisioning Service" (DAPS)
in a given IDS ecosystem enables enrichment of identities of organisations
and connectors with additional attributes.

DAPS can be understood (like the [Certificate Authority](../CA/README.md), too) as building block of the construct of an
[IDS Identity Provider](../../../Glossary/README.md#identity-provider).

Since the DAPS trust building service, its X.509 certificate (used for TLS connections) must be signed by the same CA that issues connector identities (e.g., a central IDS CA).

These attributes are embedded by the DAPS into a [Dynamic Attribute Token (DAT)](../../../Glossary/README.md#dynamic-attribute-token).
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

## Server Metadata

A compliant DAPS MUST offer Server Metadata according to [RFC 8414](https://datatracker.ietf.org/doc/rfc8414/).

Connectors SHOULD use these values to determine the relevant OAuth configuration for the server from its issuer identifier.

In particular, the following values are relevant to a connector:

* `issuer` MUST match the `iss` claim in any DAT.
* `token_endpoint` is the endpoint to request a DAT.
* `jwks_uri` is the endpoint to acquire the validation keys for a DAT.

### Example

Assuming a dataspace has a DAPS with an issuer identifier of `https://daps.example.org/some/path`,
then the metadata would be located at `https://daps.example.org/.well-known/oauth-authorization-server/some/path`.

Aquiring this document via an HTTP GET request results in a JSON formatted document like this:

```json
{
    "issuer": "https://daps.example.org/some/path",
    "token_endpoint": "https://daps.example.org/get/me/a/token",
    "jwks_uri": "https://keys.example.org/jwks",
    ...
}
```

Note that the object may have several other members.
Their meaning is defined by the corresponding OAuth specifications.
It shall be noted here that unrecognized values MUST be ignored.

## Token Request

To request a Dynamic Attribute Token,
a connector must send an [OAuth2 Access Token Request with a Client Credentials Grant Type](https://datatracker.ietf.org/doc/html/rfc6749#section-4.4)
to the corresponding DAPS Token Endpoint.
As authentication mechanism, a [JWT-Bearer-Token type](https://datatracker.ietf.org/doc/html/rfc7523#section-2.2) [Client Assertion](https://datatracker.ietf.org/doc/html/rfc7521#section-4.2) is used.
The OAuth client ID is the connector's ID.

The scope determines the set of attributes to be requested from the DAPS.
This version of IDS-G defines the scope `idsc:IDS_CONNECTOR_ATTRIBUTES_ALL`,
which includes all IDS Attributes available to the DAPS.

Future revisions of this document may define additional request parameters.

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
&scope=idsc:IDS_CONNECTOR_ATTRIBUTES_ALL
```

---

## Token Response

The DAPS issues the requested DAT, or an error response, as per [RFC 6749](https://datatracker.ietf.org/doc/html/rfc6749#section-5).
The Access Token ("the DAT") itself is a JWS adhering to [RFC 9068](https://datatracker.ietf.org/doc/html/rfc9068),
which in turn contains [JSON-LD](https://www.w3.org/TR/json-ld11/) encoded data in addition to the standard claims, subject to the following additional constraints:

|**Field name**|**additional constraints** |
|:---|:---|
|**@context**             | Must be `https://w3id.org/idsa/contexts/context.jsonld` |
|**@type**                | Must be `ids:DatPayload` |
|**securityProfile**      | Must be an instance of the [`ids:SecurityProfile` class](https://w3id.org/idsa/core/SecurityProfile) |

The DAT MUST be signed using a digital signature scheme.
It SHOULD be limited to a short time period (Recommendation: 1 hour).
The default resource indicator to be used in the DAT includes `idsc:IDS_CONNECTORS_ALL`, which SHOULD be accepted by all connectors.
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
In general, this claim holds an Array of Strings, but it may optionally hold a single String instead if the Array would have exactly one element.

* **extendedGuarantee**
In case a connector fulfills a certain security profile but deviates for a subset of attributes,
it can inform the receiving connector about its actual security features.
This can only happen if a connector reaches a higher level for a certain security attribute than the actual reached certification asks for.
A deviation to lower levels is not possible, as this would directly invalidate the complete certification level.
In general, this claim holds an Array of Strings, but it may optionally hold a single String instead if the Array would have exactly one element.


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
    "alg": "RS256"
}
.
{
    "iss": "https://daps.aisec.fraunhofer.de/v3",
    "sub": "DD:CB:FD:0B:93:84:33:01:11:EB:5D:94:94:88:BE:78:7D:57:FC:4A:keyid:CB:8C:C7:B6:85:79:A8:23:A6:CB:15:AB:17:50:2F:E6:65:43:5D:E8",
    "nbf": 1516239022,
    "iat": 1516239022,
    "exp": 1516239032,
    "aud": ["idsc:IDS_CONNECTORS_ALL"],
    "scope": "idsc:IDS_CONNECTOR_ATTRIBUTES_ALL",
    "@context": "https://w3id.org/idsa/contexts/context.jsonld",
    "@type": "ids:DatPayload",  
    "referringConnector": "http://some-connector-uri.com",
    "securityProfile": "idsc:BASE_SECURITY_PROFILE",
    "extendedGuarantee": "idsc:USAGE_CONTROL_POLICY_ENFORCEMENT",
    "transportCertsSha256": "bacb879575730bb083f283fd5b67a8cb..."
}
.
somesignature
```

---

## Dynamic Claims Request

A connector MAY request certain DAT claims to be issued with specified values
according to [draft-spencer-oauth-claims-01](https://www.ietf.org/archive/id/draft-spencer-oauth-claims-01.txt).
The resource sink for a DAT is `access_token`.
It is at the sole discretion of the DAPS to decide whether or not to grant the request,
and a DAT may be successfully issued even if the request is ignored.

DAPS Implementations MUST take great care not to grant requests that would grant a connector additional rights or allow it to masquerade as another connector.
In particular, all claims defined by [RFC 9068](https://datatracker.ietf.org/doc/html/rfc9068)
and all claims that are JSON-LD specific MUST NOT be chosen by client requests.
DAPS implementations are advised to explicitly white-list any requestable claims
and to check requested values as appropriate.

To allow clients to specify their own transport certificates,
DAPS implementations should support specifying values for the **transportCertsSha256** claim.

### Example

A typical request might look like this:

```http request
POST /token
Host: https://daps.example.com
Content-Type: application/x-www-form-urlencoded

grant_type=client_credentials
&client_assertion_type=urn:ietf:params:oauth:client-assertion-type:jwt-bearer
&client_assertion=eyJhbGciOiJSUzI1NiJ9.eyJ[...]N0In0.JSQu[...]gkrx4oukYNY6eHA
&scope=idsc:IDS_CONNECTOR_ATTRIBUTES_ALL
&claims=<Claims Request>
```

where the Claims Request is an encoded JSON object like the following:

```json
{
    "access_token": {
        "transportCertsSha256": {
            "value": ["ksjdhvs87h3w4fjhsf87hkjvs", "qz47djs87h3w4fjhsf87hg57d"]
        }
    }
}
```

See also:
- [Glossary "Dynamic Attribute Provisioning Service"](../../../Glossary/README.md#dynamic-attribute-provisioning-service)
- [Glossary "Dynamic Attribute Token"](../../../Glossary/README.md#dynamic-attribute-token)
- [Info Model : idsc:USAGE_CONTROL_POLICY_ENFORCEMENT](https://github.com/International-Data-Spaces-Association/InformationModel/blob/develop/codes/SecurityGuarantee.ttl)
- [JSON-LD](https://en.wikipedia.org/wiki/JSON-LD)
- [URI](https://en.wikipedia.org/wiki/Uniform_Resource_Identifier)
- Shortcuts: `DAT`, `DAPS`

