# IDS Participant


|**5.1**	|  							| IM 					| Type 		 | Example | `X.509` | `DAPS` | `DAT` | `self d.` |
|:---|:---|:---|:---|:---|:---:|:---:|:---:|:---:|
|**2 a 1**	| Applicant 				| `applicant` 			| `xsd:string` | 		   | x | | | x |
|**2 a 2**	| ...short name 			| `applicantShort` 		| `xsd:string` | | |
|**2 b 1**	| Zertifizierungsgegenstand | `subjectMatter` 		| | | |
|**2 b 2**	| ...short name 			| `subjectMatterShort` 	| | | |
|**2 c**	| Zertifizierungsstelle 	| `?` 					| | | |
|**3 h**	| Test result 				| `testResult` 			| `xsd:string` | | x | | | |
|**6**		| certificate number 		| `certNo` 				| `xsd:string` | "IDSCert-IDSZ-0001" | |
|****		| | | | | |
|**ids:Participant** |
|****		| | `ids:primarySite`				| `ids:Site`  						| | |
|****		| | `ids:corporateEmailAddress` 	| `xsd:string`						|
|****		| | `ids:corporateHomepage` 		| `xsd:anyURI`						|
|****		| | `ids:memberParticipant` 		| `ids:Participant`					|
|****		| | `ids:industrialClassification`	| `ids:IndustrialClassification`	|
|****		| | `ids:participantCertification`	| `ids:ParticipantCertification`	|
|**ids:Agent**		|
| |**ids:Described** 
| |						| `ids:title`			|
| |						| `ids:description`		|
| |**odrl:Party**		|
| |**foaf:Agent**		|
| |						| `foaf:account` 			| `foaf:OnlineAccount`	|
| |						| `foaf:weblog` 			| `foaf:Document`		|
|**ids:ManagedEntity**	|
| |						| `ids:lifecycleActivity`	| `ids:Activity`	|
| |						| `ids:version`				| `xsd:string`		|
|**org:Organization**	|
| | 					| `org:subOrganizationOf` 			|
| |						| `org:transitiveSubOrganizationOf`	|
| |						| `org:hasSite`						|



|||||||



See also:

- [IDS Information Model](https://github.com/International-Data-Spaces-Association/InformationModel/blob/develop/model/participant/Participant.ttl)
- [Glossary "Participant"](../../glossary/README.md#participant)

---
