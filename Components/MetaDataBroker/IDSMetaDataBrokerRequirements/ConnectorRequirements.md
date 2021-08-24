# Connector Requirements
The requirements listed here are required for the certification as a connector, like specified by Certification Criteria - Components v2.1.0. Criteria 
that are already covered by stronger requirements for the broker are not listed.
|  |  |
| --- | --- |
| B 016 (B12) | The communication between a Connector and an IDS Meta Data Broker relies on the general IDS communication between two Connectors as specified by The IDS Communication Guide and The IDS Handshake. |
| B 017 | An IDS Meta Data Broker must provide a Self-Description document at `<scheme>://<authority>[:port][/path/to/broker]/`according to the IDS Information Model. |
| B 018 (B27) | An IDS Meta Data Broker has to protocol all executed actions or queries. This log is used for error and problem report and is not public. Log request  must be addressed to the admin and management team of the broker. Retention time of the log is specified by the International Data Spaces. |
| B 019 (COM 03) | Encryption and integrity protection is facilitated by means of mechanisms considered state of the art by BSI TR 02102-1, NIST SP 800-175b, or an equivalent crypto catalogue. |
| B 020 | An IDS Meta Data Broker has a valid IDS Identity, an according X509 identity certificate and provides a valid DAT with each interaction. |
| B 021 (OS 01) | An IDS Meta Data Broker supports installation and execution of containers. |
| B 022 (OS 06) | System data backups as well as backups of data transferred are always encrypted before being stored outside system. |
| B 023 (APS 01) | A Broker supports only apps possessing a valid signature. This signature is the signed check sum of the soſtware artefact, which was created by means of a private key of the app publisher. |
| B 024 (APS 02) | A Broker verifies signature aſter app was downloaded (from IDS-AppStore or other source) and before it is installed, and before every execution of app. Public key of app publisher is contained in an X.509v3 certificate signed by a Certification Authority accepted by data provider and data consumer. |
| B 025 (APS 05) | A Broker supports apps delivered and installed as independent soſtware containers (i.e. apps bring along possible dependencies of e.g. soſtware modules themselves and can be used irrespective of Connector’s configuration). |
| B 026 (APS 06) | A Broker receives apps from a central app store. |
| B 027 (AUD 01) | Connector logs each access control decision in the form of an integrity protected log entry in its domain. |
| B 028 (AUD 03) | Connector logs any changes made to its configuration in the form of integrity protected entries in its domain. |
