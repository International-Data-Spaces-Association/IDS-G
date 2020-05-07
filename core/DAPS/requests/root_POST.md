# DAPS DAT request (root POST)

This request is a POST-request against the root of given
 DAPS-instance `https://daps.example.com`. The requester,
 an IDS Connector, will hand in its `client_assertion`
 and recieves its very own
 [Dynamic Attribute Token (DAT)](../README.md#dynamic-attribute-token-dat).


## Summary

Request Dynamic Attribute Token.


## Description

A client that requests a [DAT](../README.md#dynamic-attribute-token-dat)
 needs to create JWT as a request token. The JWT contains
 a header, payload and signature. The information content of
 the JWT is modeled as an instance of the `ids:DatPayload`
 class, with the content attributes as specified in the
 table below.


## Examples

### 1 request

```http request
POST /
Host: https://daps.example.com
Content-Type: application/x-www-form-urlencoded

"grant_type=client_credentials
 &client_assertion_type=urn:ietf:params:oauth:client-assertion-type:jwt-bearer
 &client_assertion=eyJhbGciOiJSUzI1NiJ9.eyJpc3MiOiJkZW1vY29ubmVjdG9yMSIsInN1YiI6ImRlbW9jb25uZWN0b3IxIiwiZXhwIjoxNTQ4Nzg1Mzg2LCJuYmYiOjE1NDg3ODE3ODYsImlhdCI6MTU0ODc4MTc4NiwiYXVkIjoiaHR0cHM6Ly9hcGkubG9jYWxob3N0In0.JSQuMf-9Fd7DNna_-s-sR7eXgcSYNCau5WgurrGJTuCSLKqhZe3odXfunN2vRFgUhU21ADFlEq96mlbQDueBlMtaXrcHFPSpIUtvuIMIVqQcGYkDdSJr_VmDuAykCYpyTCkLa7a8DTV-N3sECp-AxUgmEzYIfh8jW0WS6ehgUzrnpH6t_h_GWXKkNSAg3ERakDc4NY02pBGmiN7bmtLZNt5b4LWALiiFiduC7JbIpx4awOU6skMApmzgLnZmmTG20JlJRg6hAqyHEz5Cd4rUgrt0twmpC0Us_CG23KdUF5fWI55dcO2qAVvhNQXpqz7IiPcF7-jgkrx4oukYNY6eHA
 &scope=https://w3id.org/idsa/core/Connector"

```


### 1 response

The response to given request is a
 [Dynamic Attribute Token (DAT)](../README.md#dynamic-attribute-token-dat), here shown decoded:

```
{
    "typ": "JWT",
    "alg": "HS256"
}
.
{
    "@context": "https://w3id.org/idsa/contexts/context.jsonld",
    "@type": "ids:DatPayload",  
    "iss": "AE:2F:31:46:93:48:33:10:11:BE:D5:49:49:88:EB:87:D7:75:CF:A4:keyid:CB:8C:C7:B6:85:79:A8:23:A6:CB:15:AB:17:50:2F:E6:65:43:5D:E8",
    "sub": "DD:CB:FD:0B:93:84:33:01:11:EB:5D:94:94:88:BE:78:7D:57:FC:4A:keyid:CB:8C:C7:B6:85:79:A8:23:A6:CB:15:AB:17:50:2F:E6:65:43:5D:E8",
    "referringConnector": "http://some-connector-uri.com",
    "securityProfile": "BASE_SECURITY_PROFILE",
    "extendedGuarantee": "USAGE_CONTROL_POLICY_ENFORCEMENT",
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

---