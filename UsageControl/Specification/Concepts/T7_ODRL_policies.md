## Extension of Policy Classes

In this section, we present the extension of the Policy Classes specified in ODRL. The domain-specific elements are referenced by adding the URI of their profiles in the *@Context* part of the policy. Therefore, in the sample policies below, there is a reference to the Information Model of IDS to cover the IDS specific terminology.

**22. Restrict the data usage to the location of the participant**

The location of Participants shall be properly defined.

```json
{
   "@type": "vcard:Organization",
   "@id": "http://example.com/organization/org1",
   "vcard:fn": "org 1",
   "vcard:hasEmail": "org1@example.com",
   "odrl:partOf": "http://example.com/ids/party/456456",
   "vcard:hasCountryName": "Germany",
   "vcard:hasGeo": "http://ontologi.es/place/DE"
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
    "@type": "Agreement",
    "uid": "http://example.com/policy/restrict-participants-by-location/12",
    "profile": "http://www.w3.org/ns/odrl/2/core",
    "dc:creator": "Data Provider 123",
    "dc:description": "This policy restricts the usage of the data to the location of the Data Consumer party.",
    "dc:issued": "2022-05-19T12:00",
    "permission": [{
        "target": "http://example.com/ids/data/789",
        "assigner": {
           "@type": [ "PartyCollection", "vcard:Organization" ],
           "uid": "http://example.com/ids/party/123123",
        },
        "assignee": {
           "@type": [ "PartyCollection", "vcard:Group" ],
           "source": "http://example.com/ids/party/456456",
           "refinement": [{
              "leftOperand": "vcard:hasGeo",
              "operator": "eq",
              "rightOperand": [{ "@value": "http://ontologi.es/place/DE", "@type": "xsd:anyURI" }]
           }]
        },
        "action": "use"
    }]
}
```

**23. Restrict the data usage to the user consent**

The Participants and users shall be propertly defined.

```json
{
   "@type": "vcard:Individual",
   "@id": "http://example.com/person/sara",
   "vcard:fn": "Sara",
   "vcard:hasEmail": "sara@example.com"
}

{
   "@type": "dc:Dataset",
   "@id": "http://example.com/asset:sara-data.json",
   "dc:title": "Sara's health data",
   "dc:fileFormat": "application/json",
   "odrl:partOf": "http://example.com/ids/data/health-data-collection1"
}

{
   "@type": "vcard:Individual",
   "@id": "http://example.com/ids/party/john",
   "vcard:fn": "John",
   "vcard:hasEmail": "john@example.com"
}

{
   "@type": "dc:Dataset",
   "@id": "http://example.com/asset:john-data.json",
   "dc:title": "John's health data",
   "dc:fileFormat": "application/json",
   "odrl:partOf": "http://example.com/ids/data/health-data-collection1"
}

{
   "@type": "vcard:Organization",
   "@id": "http://example.com/organization/org1",
   "vcard:fn": "org 1",
   "vcard:hasEmail": "org1@example.com",
   "odrl:partOf": "http://example.com/ids/party/dataprovider1",
   "vcard:hasCountryName": "Germany",
   "vcard:hasGeo": "http://ontologi.es/place/DE"
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
    "@type": "Agreement",
    "uid": "http://example.com/policy/obtain-consent/12",
    "profile": "http://www.w3.org/ns/odrl/2/core",
    "dc:creator": "Data Provider 123",
    "dc:description": "This policy demands to obtain consent from the Data Owner before using the data.",
    "dc:issued": "2022-05-25T12:00",
    "permission": [{
        "target": "http://example.com/asset:sara-data.json",
        "assigner": "http://example.com/ids/party/dataprovider1",
        "assignee": "http://example.com/ids/party/456",
        "action": "use",
        "duty": [{
           "action": [{
              "rdf:value": { "@id": "odrl:obtainConsent" }
            }],
            "consentingParty": ["http://example.com/person/sara"]
            "constraint": [{
                "leftOperand": "event",
                "operator": "lt",
                "rightOperand": { "@id": "odrl:policyUsage" }
            }]
        }]
    }]
}
```

We may need consents from more than one Participant or user.

```json
{
    "@context": [
        "http://www.w3.org/ns/odrl.jsonld",
        { "dc": "http://purl.org/dc/terms/",
        "ids":"https://w3id.org/idsa/core/",
        "idsc" : "https://w3id.org/idsa/code/" }
    ],
    "@type": "Agreement",
    "uid": "http://example.com/policy/obtain-consent/12",
    "profile": "http://www.w3.org/ns/odrl/2/core",
    "dc:creator": "Data Provider 123",
    "dc:description": "this policy restricts the usage of the data to obtaining consent from the Data Provider/Owner.",
    "dc:issued": "2022-05-25T12:00",
    "permission": [{
        "target": "http://example.com/ids/data/health-data-collection1",
        "assigner": "http://example.com/ids/party/dataprovider1",
        "assignee": "http://example.com/ids/party/456",
        "action": "use",
        "duty": [{
           "action": [{
              "rdf:value": { "@id": "odrl:obtainConsent" }
            }],
            "consentingParty": ["http://example.com/person/sara","http://example.com/ids/party/john"]
            "constraint": [{
                "leftOperand": "event",
                "operator": "lt",
                "rightOperand": { "@id": "odrl:policyUsage" }
            }]
        }]
    }]
}
```

## Usage Control Policy

One can instantiate and combine a set of the above mentioned Policy Classes and build a Usage Control policy. In order to specify a policy in ODRL language, the following template can be used.

**ODRL Policy Template without IDS Information Model referenced in the *@context*:**

```json
{
    "@context": [
        "http://www.w3.org/ns/odrl.jsonld",
        { "dc": "http://purl.org/dc/terms/",
        "ids":"https://w3id.org/idsa/core/",
        "idsc" : "https://w3id.org/idsa/code/"}
    ],
    "@type": "Set",
    "uid": " ",
    "profile": "http://www.w3.org/ns/odrl/2/core",
    "dc:creator": " ",
    "dc:description": " ",
    "dc:issued": " ",
    "permission": [{
        "target": " ",
        "assigner": " ",
        "assignee": " ",
        "action": [" ", " "],
        "constraint": [{
           "leftOperand": " ",
           "operator": " ",
           "rightOperand":  [{ }]
       },{
           "leftOperand": " ",
           "operator": " ",
           "rightOperand":  [{ }]
       }],
         "duty": [{
           "action": [" "],
           "constraint": [{
               "leftOperand": "event",
               "operator": " ",
               "rightOperand": { "@id": "odrl:policyUsage" }
           }]
        }]
    }]
}
```

**ODRL Policy Template with IDS Information Model referenced in the *@context*:**

```json
{
    "@context": [
        "http://www.w3.org/ns/odrl.jsonld",
        { "dc": "http://purl.org/dc/terms/",
        "ids":"https://w3id.org/idsa/core/",
        "idsc" : "https://w3id.org/idsa/code/" }
    ],
    "@type": "Set",
    "uid": " ",
    "profile": "http://www.w3.org/ns/odrl/2/core",
    "dc:creator": " ",
    "dc:description": " ",
    "dc:issued": " ",
    "ids:provider": " ",
    "ids:consumer": " ",
    "permission": [{
        "target": " ",
        "assigner": " ",
        "assignee": " ",
        "action": [" ", " "],
        "constraint": [{
           "leftOperand": " ",
           "operator": " ",
           "rightOperand":  [{ }],
           "ids:pipEndpoint": [{
              "@type":"ids:PIP",
              "ids:interfaceDescription":{ "@value":" ", "@type":"xsd:anyURI" }, 
              "ids:endpointURI":{ "@value":" ", "@type":"xsd:anyURI" } 
           }]
       },{
           "leftOperand": " ",
           "operator": " ",
           "rightOperand":  [{ }],
           "ids:pipEndpoint": [{
              "@type":"ids:PIP",
              "ids:interfaceDescription":{ "@value":" ", "@type":"xsd:anyURI" }, 
              "ids:endpointURI":{ "@value":" ", "@type":"xsd:anyURI" } 
           }]
       }],
         "duty": [{
           "action": [" "],
           "constraint": [{
               "leftOperand": "event",
               "operator": " ",
               "rightOperand": { "@id": "odrl:policyUsage" }
           }],
           "ids:pxpEndpoint": [{
              "@type":"ids:PXP",
              "ids:interfaceDescription":{ "@value":" ", "@type":"xsd:anyURI" }, 
              "ids:endpointURI":{ "@value":" ", "@type":"xsd:anyURI" } 
           }]
        }]
    }]
}

```

**Sample ODRL Usage Control policy:**

```json
{
    "@context": [
        "http://www.w3.org/ns/odrl.jsonld",
        { "dc": "http://purl.org/dc/terms/",
        "ids":"https://w3id.org/idsa/core/",
        "idsc" : "https://w3id.org/idsa/code/"}
    ],
    "@type": "Agreement",
    "uid": "http://example.com/policy/restrict-security-level/12",
    "profile": "http://www.w3.org/ns/odrl/2/core",
    "dc:creator": "Data Provider 123",
    "dc:description": "This policy restrict the usage of the data (perform derive and display actions) to the Security Levels of the Consumer Connectors, purposes and a time interval. In addition, it demands to log the usage information and delete the data after a delay period of one month.",
    "dc:issued": "2022-05-19T12:00",
    "permission": [{
        "target": "http://example.com/ids/data/789",
        "assigner": "http://example.com/ids/party/123",
        "assignee": "http://example.com/ids/party/456",
        "action": [ "derive", "display" ],
        "constraint": [{
           "leftOperand": "idsc:SECURITY_LEVEL",
           "operator": "isPartOf",
           "rightOperand":  [
              { "@value": "idsc:TRUST_PLUS_SECURITY_PROFILE", "@type": "xsd:string" },
              { "@value": "idsc:TRUST_SECURITY_PROFILE", "@type": "xsd:string" }
           ]
       },{
           "leftOperand": "dateTime",
           "operator": "lteq",
           "rightOperand": { "@value": "2022-10-01T08:00Z", "@type": "xsd:dateTimeStamp" }
       },{
           "leftOperand": "purpose",
           "operator": "isPartOf",
           "rightOperand": [{ "@value": "Educational Use", "@type": "xsd:string" },
                            { "@value": "Risk Management", "@type": "xsd:string" },
                            { "@value": "Defect Analysis", "@type": "xsd:string" }]
       }],
         "duty": [{
           "action": [{
              "rdf:value": { "@id": "idsc:log" },
              "includedIn": "inform",
              "refinement": [{
                 "leftOperand": "systemDevice",
                 "operator": "eq",
                 "rightOperand": { "@value": "?loggingServer", "@type": "xsd:anyURI" }
              },{
                 "leftOperand": "idsc:LOG_LEVEL",
                 "operator": "eq",
                 "rightOperand": { "@value": "idsc:ON_ALLOW", "@type": "xsd:string" }
              }]
           }],
           "constraint": [{
               "leftOperand": "event",
               "operator": "gt",
               "rightOperand": { "@id": "odrl:policyUsage" }
           }]
        },{
           "action": [{
              "rdf:value": { "@id": "delete" },
              "refinement": [{
                 "leftOperand": "delayPeriod",
                 "operator": "eq",
                 "rightOperand": { "@value": "P1M", "@type": "xsd:duration" }
              }]
           }],
           "informedParty": "http://example.com/ids/party/123",
           "constraint": [{
               "leftOperand": "event",
               "operator": "gt",
               "rightOperand": { "@id": "odrl:policyUsage" }
           }]
        }]
    }]
}
```

**Sample ODRL Usage Control policy with IDS Information Model referenced in the *@context*:**

Including the IDS Information Model in the Context, we can additionally represent the PIP and PXP interfaces in the policy.

```json
{
    "@context": [
        "http://www.w3.org/ns/odrl.jsonld",
        { "dc": "http://purl.org/dc/terms/",
        "ids":"https://w3id.org/idsa/core/",
        "idsc" : "https://w3id.org/idsa/code/" }
    ],
    "@type": "Agreement",
    "uid": "http://example.com/policy/restrict-security-level/12",
    "profile": "http://www.w3.org/ns/odrl/2/core",
    "dc:creator": "Data Provider 123",
    "dc:description": "This policy restrict the usage of the data (perform derive and display actions) to the Security Levels of the Consumer Connectors, purposes and a time interval. In addition, it demands to log the usage information and delete the data after a delay period of one month.",
    "dc:issued": "2022-05-19T12:00",
    "ids:provider": "http://example.com/ids/party/123",
    "ids:consumer": "http://example.com/ids/party/456",
    "permission": [{
        "target": "http://example.com/ids/data/789",
        "assigner": "http://example.com/ids/party/123",
        "assignee": "http://example.com/ids/party/456",
        "action": [ "derive", "display" ],
        "constraint": [{
           "leftOperand": "idsc:SECURITY_LEVEL",
           "operator": "isPartOf",
           "rightOperand":  [
              { "@value": "idsc:TRUST_PLUS_SECURITY_PROFILE", "@type": "xsd:string" },
              { "@value": "idsc:TRUST_SECURITY_PROFILE", "@type": "xsd:string" }
           ],
           "ids:pipEndpoint": [{
              "@type":"ids:PIP",
              "ids:interfaceDescription":{ "@value":"?interfaceURI", "@type":"xsd:anyURI" }, 
              "ids:endpointURI":{ "@value":"?endPointURI", "@type":"xsd:anyURI" } 
           }]
       },{
           "leftOperand": "dateTime",
           "operator": "lteq",
           "rightOperand": { "@value": "2022-10-01T08:00Z", "@type": "xsd:dateTimeStamp" },
           "ids:pipEndpoint": [{
              "@type":"ids:PIP",
              "ids:interfaceDescription":{ "@value":"?interfaceURI", "@type":"xsd:anyURI" }, 
              "ids:endpointURI":{ "@value":"?endPointURI", "@type":"xsd:anyURI" } 
           }]
       },{
           "leftOperand": "purpose",
           "operator": "isPartOf",
           "rightOperand": [{ "@value": "Educational Use", "@type": "xsd:string" },
                            { "@value": "Risk Management", "@type": "xsd:string" },
                            { "@value": "Defect Analysis", "@type": "xsd:string" }],
           "ids:pipEndpoint": [{
              "@type":"ids:PIP",
              "ids:interfaceDescription":{ "@value":"?interfaceURI", "@type":"xsd:anyURI" }, 
              "ids:endpointURI":{ "@value":"?endPointURI", "@type":"xsd:anyURI" } 
           }]
       }],
         "duty": [{
           "action": [{
              "rdf:value": { "@id": "idsc:log" },
              "includedIn": "inform",
              "refinement": [{
                 "leftOperand": "systemDevice",
                 "operator": "eq",
                 "rightOperand": { "@value": "?loggingServer", "@type": "xsd:anyURI" }
              },{
                 "leftOperand": "idsc:LOG_LEVEL",
                 "operator": "eq",
                 "rightOperand": { "@value": "idsc:ON_ALLOW", "@type": "xsd:string" }
              }]
           }],
           "constraint": [{
               "leftOperand": "event",
               "operator": "gt",
               "rightOperand": { "@id": "odrl:policyUsage" }
           }],
           "ids:pxpEndpoint": [{
              "@type":"ids:PXP",
              "ids:interfaceDescription":{ "@value":"?interfaceURI", "@type":"xsd:anyURI" }, 
              "ids:endpointURI":{ "@value":"?endPointURI", "@type":"xsd:anyURI" } 
           }]
        },{
           "action": [{
              "rdf:value": { "@id": "delete" },
              "refinement": [{
                 "leftOperand": "delayPeriod",
                 "operator": "eq",
                 "rightOperand": { "@value": "P1M", "@type": "xsd:duration" }
              }]
           }],
           "informedParty": "http://example.com/ids/party/123",
           "constraint": [{
               "leftOperand": "event",
               "operator": "gt",
               "rightOperand": { "@id": "odrl:policyUsage" }
           }],
           "ids:pxpEndpoint": [{
              "@type":"ids:PXP",
              "ids:interfaceDescription":{ "@value":"?interfaceURI", "@type":"xsd:anyURI" }, 
              "ids:endpointURI":{ "@value":"?endPointURI", "@type":"xsd:anyURI" } 
           }]
        }]
    }]
}
```
