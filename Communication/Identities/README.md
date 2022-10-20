# Identities

Connector identities according to this specification are typically represented by some sort of token.
This token contains statements about properties of the connector ("claims"), like its membership in one or more data spaces.
In order to establish trust via these tokens, two requirements have to be met:

- Security-relevant claims within the token, or the entire token as a whole, MUST be signed by one or more trusted entities.
- There MUST exist a sound mechanism that enables a connector to proof ownership over the tokens or claims issued for this particular connector.
Specifically, any connector implementing a corresponding validation mechanism MUST be capable of identifying and rejecting tokens that have not been issued for the respective peer connector(s).

## X.509/OAuth2-Token-based Identities
Identities of this kind are established via X.509 certificates and corresponding private keys.
The tokens used to provide identity information to other peers are regular OAuth2 JSON Web Tokens (JWTs).
In the IDS context, the OAuth2 server issuing such tokens is called a "Dynamic Attribute Provisioning Server", or DAPS for short.

### Format and Signature
The format of Dynamic Attribute Tokens (DATs) and the interaction between DAPS and connector are described in the [DAPS Specification](../../Components/IdentityProvider/DAPS/README.md).
As DATs are JWTs with a (RSA) signature, the issuing DAPS acts as a central, trusted entity, which also manages the contained claims about the connector.

### Token Ownership Verification
The ownership over DAPS tokens is proven to peer connectors via ownership of a private key used for transport encryption, typically an RSA key belonging to a X.509 certificate.

For typical TLS encryption scenarios, the respective approach via the `transportCertsSha256` claim is documented in the [DAPS Token Response Specification](../../Components/IdentityProvider/DAPS/README.md#token-response).
This claim is specified to contain an array of SHA256 fingerprints as hex-encoded strings, or a single such hash as a string.
As per that specification, DAPS implementations SHOULD allow the `transportCertsSha256` claim to be overridden with a dynamic value when requesting the DAT.
For non-TLS encrypted communication channels, hashes of corresponding public keys or their very values (e.g. for short representations of EC keys) MAY be used instead.

For verification, a connector MUST check that the fingerprint of its peer's certificate is contained in - or equal to - the value of the `transportCertsSha256` claim.
