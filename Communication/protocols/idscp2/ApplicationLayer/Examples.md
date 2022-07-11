# Examples for IDSCP2 Messages

## ContractRequestMessage
The serialization of the header of the message might look like this:
````json
{
  "@context" : {
    "ids" : "https://w3id.org/idsa/core/",
    "idsc" : "https://w3id.org/idsa/code/"
  },
  "@type" : "ids:ContractRequestMessage",
  "@id" : "https://w3id.org/idsa/autogen/contractRequestMessage/321b5923-78d9-4d49-9b6e-b9b860a60ee6",
  "ids:modelVersion" : "4.1.0",
  "ids:issuerConnector" : {
    "@id" : "https://connector.aisec.fraunhofer.de"
  },
  "ids:issued" : {
    "@value" : "2020-11-17T10:42:28.701+01:00",
    "@type" : "http://www.w3.org/2001/XMLSchema#dateTimeStamp"
  },
  "ids:securityToken" : {
    "@type" : "ids:DynamicAttributeToken",
    "@id" : "https://w3id.org/idsa/autogen/dynamicAttributeToken/287a9524-2c67-41dd-b55f-08e95ad6188f",
    "ids:tokenValue" : "eyJ0eXAiOiJKV1QiLCJraWQiOiJkZWZhdWx0Iiwi...",
    "ids:tokenFormat" : {
      "@id" : "idsc:JWT"
    }
  },
  "ids:senderAgent" : {
    "@id" : "https://aisec.fraunhofer.de"
  }
}
````

The serialization of the payload of the message might look like this:
````json
{
  "@context" : {
    "ids" : "https://w3id.org/idsa/core/"
  },
  "@type" : "ids:ContractRequest",
  "@id" : "https://w3id.org/idsa/autogen/contractRequest/b3b711e4-a5ba-4076-9e44-144c50bfe68a",
  "ids:contractDate" : {
    "@value" : "2021-11-17T10:42:29.243+01:00",
    "@type" : "http://www.w3.org/2001/XMLSchema#dateTimeStamp"
  },
  "ids:contractEnd" : {
    "@value" : "2021-11-17T10:42:29.243+01:00",
    "@type" : "http://www.w3.org/2001/XMLSchema#dateTimeStamp"
  },
  "ids:contractStart" : {
    "@value" : "2021-11-17T10:42:29.243+01:00",
    "@type" : "http://www.w3.org/2001/XMLSchema#dateTimeStamp"
  },
  "ids:permission" : [ {
    "@type" : "ids:Permission",
    "@id" : "https://w3id.org/idsa/autogen/permission/471b980c-c7f5-4a1e-af39-e5993d7057ee",
    "ids:target" : {
      "@id" : "https://example.com/some_artifact"
    }
  } ]
}
````

## ContractAgreementMessage
The following serialised Header might be used to respont to a ContractRequest-Message:
````json
{
  "@context" : {
    "ids" : "https://w3id.org/idsa/core/",
    "idsc" : "https://w3id.org/idsa/code/"
  },
  "@type" : "ids:ContractAgreementMessage",
  "@id" : "https://w3id.org/idsa/autogen/contractAgreementMessage/bbb217e8-d946-45dc-891b-1e71b8071c60",
  "ids:modelVersion" : "4.0.0",
  "ids:correlationMessage" : {
    "@id" : "https://w3id.org/idsa/autogen/contractResponseMessage/ac7940f9-4707-44f1-853a-d2839cded1f1"
  },
  "ids:issuerConnector" : {
    "@id" : "https://connector.aisec.fraunhofer.de"
  },
  "ids:issued" : {
    "@value" : "2020-11-17T10:42:36.328+01:00",
    "@type" : "http://www.w3.org/2001/XMLSchema#dateTimeStamp"
  },
  "ids:securityToken" : {
    "@type" : "ids:DynamicAttributeToken",
    "@id" : "https://w3id.org/idsa/autogen/dynamicAttributeToken/9ccd5552-c556-49a3-9ff4-1fc89c8268dd",
    "ids:tokenValue" : "eyJ0eXAiOiJKV1QiLCJraWQiOiJkZWZhdWx0Iiwi...",
    "ids:tokenFormat" : {
      "@id" : "idsc:JWT"
    }
  },
  "ids:senderAgent" : {
    "@id" : "https://aisec.fraunhofer.de"
  }
}
````

The respective payload could look like this:
````json
Serialization body: {
  "@context" : {
    "ids" : "https://w3id.org/idsa/core/"
  },
  "@type" : "ids:ContractAgreement",
  "@id" : "https://w3id.org/idsa/autogen/contractAgreement/147884ed-70d6-4ec0-9739-15d7d376850f",
  "ids:permission" : [ {
    "@type" : "ids:Permission",
    "@id" : "https://w3id.org/idsa/autogen/permission/15c7c882-0f78-4ffc-a23b-94c05459a7f2",
    "ids:constraint" : [ {
      "@type" : "ids:Constraint",
      "@id" : "https://w3id.org/idsa/autogen/constraint/ce66908c-c05f-4559-b3f5-14b506175e07",
      "ids:leftOperand" : {
        "@id" : "idsc:SYSTEM"
      },
      "ids:operator" : {
        "@id" : "idsc:SAME_AS"
      },
      "ids:rightOperandReference" : {
        "@id" : "https://hub.docker.com/layers/fraunhoferaisec/trusted-connector-core/develop/images/sha256-aa9fe6d854fd01d0a098802bf76477fc66147646f11303e5c0c19a80add6406a#port=80"
      }
    } ],
    "ids:target" : {
      "@id" : "https://example.com/sample_artifact"
    }
  } ]
}
````

# ArtifactRequestMessage
A client might send the following serialized header for an ArtifactRequestMessage:
````json
{
  "@context" : {
    "ids" : "https://w3id.org/idsa/core/",
    "idsc" : "https://w3id.org/idsa/code/"
  },
  "@type" : "ids:ArtifactRequestMessage",
  "@id" : "https://w3id.org/idsa/autogen/artifactRequestMessage/206e16cc-856b-48dd-bcc4-c6752e6ea3ce",
  "ids:modelVersion" : "4.1.0",
  "ids:requestedArtifact" : {
    "@id" : "https://example.com/some_artifact"
  },
  "ids:issuerConnector" : {
    "@id" : "https://connector.aisec.fraunhofer.de"
  },
  "ids:issued" : {
    "@value" : "2020-11-17T10:42:41.267+01:00",
    "@type" : "http://www.w3.org/2001/XMLSchema#dateTimeStamp"
  },
  "ids:securityToken" : {
    "@type" : "ids:DynamicAttributeToken",
    "@id" : "https://w3id.org/idsa/autogen/dynamicAttributeToken/4c1c1335-f98c-4d1e-bcd7-bfd143c656c1",
    "ids:tokenValue" : "eyJ0eXAiOiJKV1QiLCJraWQiOiJkZWZhdWx0Iiwi...",
    "ids:tokenFormat" : {
      "@id" : "idsc:JWT"
    }
  },
  "ids:senderAgent" : {
    "@id" : "https://aisec.fraunhofer.de"
  }
}
````

# ArtifactResponseMessage

A server might respond with the following serialized header for an ArtifactResponseMessage:
````json
{
  "@context" : {
    "ids" : "https://w3id.org/idsa/core/",
    "idsc" : "https://w3id.org/idsa/code/"
  },
  "@type" : "ids:ArtifactResponseMessage",
  "@id" : "https://w3id.org/idsa/autogen/artifactResponseMessage/6717d2ab-377e-4510-878a-cb4899a57b3a",
  "ids:modelVersion" : "4.1.0",
  "ids:issuerConnector" : {
    "@id" : "https://connector.aisec.fraunhofer.de"
  },
  "ids:issued" : {
    "@value" : "2020-11-17T10:42:41.325+01:00",
    "@type" : "http://www.w3.org/2001/XMLSchema#dateTimeStamp"
  },
  "ids:securityToken" : {
    "@type" : "ids:DynamicAttributeToken",
    "@id" : "https://w3id.org/idsa/autogen/dynamicAttributeToken/ae6e335a-0351-49f7-a438-f0e0e8a23d3b",
    "ids:tokenValue" : "eyJ0eXAiOiJKV1QiLCJraWQiOiJkZWZhdWx0Iiwi...",
    "ids:tokenFormat" : {
      "@id" : "idsc:JWT"
    }
  },
  "ids:senderAgent" : {
    "@id" : "https://aisec.fraunhofer.de"
  }
}
````
with the following serialised payload:
````json
{
  "@context" : {
    "ids" : "https://w3id.org/idsa/core/"
  },
  "@type" : "ids:Artifact",
  "@id" : "https://w3id.org/idsa/autogen/artifact/6f58407f-de3d-48a8-908e-2985977e3f51",
  "ids:fileName" : "testArtifactFilename.dat",
  "ids:byteSize" : 50000,
  "ids:creationDate" : {
    "@value" : "2020-11-17T10:42:41.341+01:00",
    "@type" : "http://www.w3.org/2001/XMLSchema#dateTimeStamp"
  },
  "ids:checkSum" : "ABCDEFG-CHECKSUM",
  "ids:duration" : 5000
}
````
