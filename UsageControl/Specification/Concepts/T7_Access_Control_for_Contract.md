## Access Control for the Contract Metadata

Policies represent the rules about how to access or use a piece of data and shall be stored securely. The data may be in different formats and sizes and may have different contents. An (Offer) Contract which includes the Usage Control policies and rules can be considered as a piece of data itself. Therefore, we can introduce an Access Control policy for it. This Access Control policy includes a reference to the Contract and must be stored separately and securely. This means that the information about who can access a Contract or not must not be stored inside the Contract. Though, it can be securely attached to it. These attached policies are also known as *Sticky policies*.

The following policy is a sample Offer Contract with the uid *".../restrict-to-time-and-purpose/987"*:

```json
{
    "@context": [
        "http://www.w3.org/ns/odrl.jsonld",
        { "dc": "http://purl.org/dc/terms/",
        "ids":"https://w3id.org/idsa/core/",
        "idsc" : "https://w3id.org/idsa/code/" }
    ],
    "@type": "Offer",
    "uid": "http://example.com/policy/offer/restrict-to-time-and-purpose/987",
    "profile": "http://www.w3.org/ns/odrl/2/core",
    "dc:creator": "Data Provider 123",
    "dc:description": "This Offer policy restricts the usage of the data to a specific purpose and a specific time interval.",
    "dc:issued": "2022-05-19T12:00",
    "ids:provider": "http://example.com/ids/party/123",
    "permission": [{
        "target": "http://example.com/ids/data/789",
        "assigner": "http://example.com/ids/party/123",
        "action": "display",
        "constraint": [{
           "leftOperand": "purpose",
           "operator": "eq",
           "rightOperand":  { "@value": "Educational Use", "@type": "xsd:string" },
           "ids:pipEndpoint": [{
              "@type":"ids:PIP",
              "ids:interfaceDescription":{ "@value":"?interfaceURI", "@type":"xsd:anyURI" }, 
              "ids:endpointURI":{ "@value":"?endpointURI", "@type":"xsd:anyURI" } 
           }]
       },{
           "leftOperand": "dateTime",
           "operator": "lteq",
           "rightOperand":  { "@value": "2022-06-01T08:00Z", "@type": "xsd:dateTimeStamp" },
           "ids:pipEndpoint": [{
              "@type":"ids:PIP",
              "ids:interfaceDescription":{ "@value":"?interfaceURI", "@type":"xsd:anyURI" }, 
              "ids:endpointURI":{ "@value":"?endpointURI", "@type":"xsd:anyURI" } 
           }]
       }]
    }]
}
```

And we may have two parties in our dataspace introduced as follows:

```json
{
   "@type": "vcard:Organization",
   "@id": "http://example.com/organization/org1",
   "vcard:fn": "org 1",
   "vcard:hasEmail": "org1@example.com",
   "odrl:partOf": "http://example.com/ids/partycollection/456456",
   "vcard:hasCountryName": "Germany",
   "vcard:hasGeo": "http://ontologi.es/place/DE"
}

{
   "@type": "vcard:Organization",
   "@id": "http://example.com/organization/org2",
   "vcard:fn": "org 2",
   "vcard:hasEmail": "org2@example.com",
   "odrl:partOf": "http://example.com/ids/partycollection/456456",
   "vcard:hasCountryName": "Germany",
   "vcard:hasGeo": "http://ontologi.es/place/DE"
}
```

An Access Control policy for the Contract *".../restrict-to-time-and-purpose/987"* could be like any of the following policies:

```json
{
    "@context": [
        "http://www.w3.org/ns/odrl.jsonld",
        { "dc": "http://purl.org/dc/terms/",
        "ids":"https://w3id.org/idsa/core/",
        "idsc" : "https://w3id.org/idsa/code/" }
    ],
    "@type": "Policy",
    "uid": "http://example.com/metadatapolicy/456781",
    "profile": "http://www.w3.org/ns/odrl/2/core",
    "dc:creator": "Data Provider 123",
    "dc:description": "This policy restricts the access to a specific contract.",
    "dc:issued": "2022-05-19T12:00",
    "ids:provider": "http://example.com/ids/party/123",
    "permission": [{
        "target": "http://example.com/policy/offer/restrict-to-time-and-purpose/987",
        "assigner": "http://example.com/ids/party/123",
        "assignee": [{
           "@type": [ "Party", "vcard:Organization" ],
           "source": "http://example.com/organization/org1"
        },{
           "@type": [ "Party", "vcard:Organization" ],
           "source": "http://example.com/organization/org2"
        }]
        "action": "read"
    }]
}
```

```json
{
    "@context": [
        "http://www.w3.org/ns/odrl.jsonld",
        { "dc": "http://purl.org/dc/terms/",
        "ids":"https://w3id.org/idsa/core/",
        "idsc" : "https://w3id.org/idsa/code/" }
    ],
    "@type": "Policy",
    "uid": "http://example.com/metadatapolicy/456782",
    "profile": "http://www.w3.org/ns/odrl/2/core",
    "dc:creator": "Data Provider 123",
    "dc:description": "This policy restricts the access to a specific contract.",
    "dc:issued": "2022-05-19T12:00",
    "ids:provider": "http://example.com/ids/party/123",
    "permission": [{
        "target": "http://example.com/policy/offer/restrict-to-time-and-purpose/987",
        "assigner": "http://example.com/ids/party/123",
        "assignee": ["http://example.com/organization/org1", "http://example.com/organization/org2"]
        "action": "read"
    }]
}
```

```json
{
    "@context": [
        "http://www.w3.org/ns/odrl.jsonld",
        { "dc": "http://purl.org/dc/terms/",
        "ids":"https://w3id.org/idsa/core/",
        "idsc" : "https://w3id.org/idsa/code/" }
    ],
    "@type": "Policy",
    "uid": "http://example.com/metadatapolicy/456783",
    "profile": "http://www.w3.org/ns/odrl/2/core",
    "dc:creator": "Data Provider 123",
    "dc:description": "This policy restricts the access to a specific contract.",
    "dc:issued": "2022-05-19T12:00",
    "ids:provider": "http://example.com/ids/party/123",
    "permission": [{
        "target": "http://example.com/policy/offer/restrict-to-time-and-purpose/987",
        "assigner": "http://example.com/ids/party/123",
        "assignee": {
           "@type": [ "PartyCollection", "vcard:group" ],
           "source": "http://example.com/ids/partycollection/456456"
        }
        "action": "read"
    }]
}
```

It is important to notice that the targets in the above mentioned Access policies refer to the uid of the Offer Contract.
