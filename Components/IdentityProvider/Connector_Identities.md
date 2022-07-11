# Connector Identifiers (Connector IDs)

Each connector in the IDS needs a valid, outlasting and unique identifier,
never be re-used for any other resource inside the IDS ecosystem.

The architecture aims to be open to multiple [Certificate Authorities](README.md) (CAs) issuing certificates.
This means, a truly unique identifier needs to consist of the issuer of the certificate and the subject identifier.
For an easy machine readable identifier, two ´X.509v3´ extensions will be used:

* Subject Key Identifier (SKI)
* Authority Key Identifier (AKI)

The AKI MUST include a key identifier according to [RFC 5280](https://datatracker.ietf.org/doc/html/rfc5280).
Key identifiers MUST be obtained as the 160-bit SHA-1 hash of the value of the BIT STRING subjectPuclicKey (excluding the tag, length, and number of unused bits) for the SKI and as a similar hash of the issuing public key for the AKI.
The concatenation of ´SKI´ and ´AKI´ according to the encoding below provides a unique identifier - even if multiple CAs are able to issue valid certificates.
We note that the construction presented below corresponds to the textual representation for certificates used by OpenSSL.

## Construction

Let ski be the binary representation of the "raw" SKI (i.e. the 160 Hash bits),
aki the binary representation of the "raw" AKI,
'|' denote string concatenation,
a[n] denote n-th byte (zero-indexed) of a in Network Byte Order,
and Hex(x) the hexadecimal representation of the byte x using capital letters.

The connector ID is

connector_id := skiHex | ':keyid:' | akiHex

where

skiHex := Hex(ski[0]) | ':' | Hex(ski[1]) | ':' | [omitted for brievity] | ':' | Hex(ski[19])

akiHex := Hex(aki[0]) | ':' | Hex(aki[1]) | ':' | [omitted for brievity] | ':' | Hex(aki[19])

## Examples

### Raw construction

Let the Subject Key Identifier (in Hex notation) be

```
DDCBFD0B9384330111EB5D949488BE787D57FC4A
```

and the Key Identifier of the Authority Key Identifier be

```
CB8CC7B68579A823A6CB15AB17502FE665435DE8
```

Then

```
skiHex = DD:CB:FD:0B:93:84:33:01:11:EB:5D:94:94:88:BE:78:7D:57:FC:4A
akiHex = CB:8C:C7:B6:85:79:A8:23:A6:CB:15:AB:17:50:2F:E6:65:43:5D:E8
connector_id = DD:CB:FD:0B:93:84:33:01:11:EB:5D:94:94:88:BE:78:7D:57:FC:4A:keyid:CB:8C:C7:B6:85:79:A8:23:A6:CB:15:AB:17:50:2F:E6:65:43:5D:E8
```

### Simplified Construction using the OpenSSL CLI

The following is a snippet from a X.509 certificate in text format obtained via `openssl x509 -in your_certificate -noout -text`:

```
...
X509v3 extensions:
    X509v3 Subject Key Identifier:
        DD:CB:FD:0B:93:84:33:01:11:EB:5D:94:94:88:BE:78:7D:57:FC:4A
    X509v3 Authority Key Identifier:
        keyid:CB:8C:C7:B6:85:79:A8:23:A6:CB:15:AB:17:50:2F:E6:65:43:5D:E8
...
```

Concatenating the values with ':' leads to the unique [connector](../../Connector/README.md) identifier:

```
DD:CB:FD:0B:93:84:33:01:11:EB:5D:94:94:88:BE:78:7D:57:FC:4A:keyid:CB:8C:C7:B6:85:79:A8:23:A6:CB:15:AB:17:50:2F:E6:65:43:5D:E8
```

## Notes

In examples and for reasons of readability editors might use

```
SKI:AKI
```

See also:
- [X.509 certificates](https://en.wikipedia.org/wiki/X.509)

