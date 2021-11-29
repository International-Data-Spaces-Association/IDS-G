# Connector Identifiers (Connector IDs)

Each connector in the IDS needs a valid, outlasting and unique identifier,
never be re-used for any other resource inside the IDS ecosystem.

The architecture aims to be open to multiple [Certificate Authorities](README.md) (CAs) issuing certificates.
This means, a truly unique identifier needs to consist of the issuer of the certificate and the subject identifier.
For an easy machine readable identifier, two ´X.509v3´ extensions will be used:

* Subject Key Identifier (SKI)
* Authority Key Identifier (AKI)

The concatenation of ´SKI´ and ´AKI´ provides a unique identifier valid - even if multiple CAs are able to issue valid certificates.

### Example
The following is a snippet from a X.509 certificate in text format:

```
...
X509v3 extensions:
    X509v3 Subject Key Identifier:
        DD:CB:FD:0B:93:84:33:01:11:EB:5D:94:94:88:BE:78:7D:57:FC:4A
    X509v3 Authority Key Identifier:
        keyid:CB:8C:C7:B6:85:79:A8:23:A6:CB:15:AB:17:50:2F:E6:65:43:5D:E8
...
```

This leads to the unique [connector](../../Connector/README.md) identifier:

```
DD:CB:FD:0B:93:84:33:01:11:EB:5D:94:94:88:BE:78:7D:57:FC:4A:keyid:CB:8C:C7:B6:85:79:A8:23:A6:CB:15:AB:17:50:2F:E6:65:43:5D:E8
```

### Notes
In examples and for reasons of readability editors might use

```
SKI:AKI
```

See also:
- [X.509 certificates](https://en.wikipedia.org/wiki/X.509)

