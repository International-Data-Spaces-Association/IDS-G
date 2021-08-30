# Interface Requirements
| | |
| --- | --- |
| B 058 | An IDS Meta Data Broker must have at least one of the following:<p> - an HTML-based UI at `https://<authority>[:port][/path/to/broker]/browse` <p> - an HTTPS endpoint for IDS Multipart messages at `https://<authority>[:port][/path/to/broker]/infrastructure`. In this case a Broker must support the IDS messages as specified in Table Functions.<p> - An IDS-CP socket at `idscp://<authority>[:port]/` responding to Broker-related messages. |
| B 059 | An IDS Meta Data Broker must be able to enforce the security of data transmission using state of the art communication technologies. See also B40++. |
  
