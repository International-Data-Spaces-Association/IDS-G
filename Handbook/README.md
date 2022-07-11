# Handbook to IDS-G
Version 0.1

---

## Overview
The [International Data Spaces Association IDSA](https://internationaldataspaces.org/) provides standards to establish data exchange and data sharing while maintaing data sovereignty for the provider of the data. Therefore IDSA provides a Reference Architecture Model IDS-RAM including additional specifications and schemes. All work of IDSA is based on the consensus of the IDSA members and shall be available to the public for use. This handbook provides guidance on the structure of IDS github repositories on the bodies for decision making and the processes to collaborate and publish IDSA specifications.  

## IDS-G Repositories overview 

### IDS-G
The IDS-G is the point of truth for specifications of the IDS and its components. It is public for everyone and contains the approved specifications that were confirmed by the IDS Technical Steering Committee (IDS-TSC) and the IDSA Working Groups. IDS-G publishes quarterly releases with new approvals by the Working Groups and the TSC.
You are invited to [create issues on IDS-G](https://github.com/International-Data-Spaces-Association/IDS-G/issues). The issues are tracked, approved and worked on in IDS-G-pre.

### IDS-G-pre
As an internal place for preparing specifications or changes thereof within the community, we utilize the IDS-G-Pre. Every IDSA member can get access to this private repository and is invited to collaborate to prepare (changes to) the technical specifications of the IDS and its components. The prepared documents then need to be approved by the Technical Steering Committee and the IDSA Working Groups before putting them to the IDS-G.
You are invited to create issues and to contribute to the issues. Create a branch and a pull request if you can contribute to the issues raised. The [IDS-G roadmap](https://github.com/International-Data-Spaces-Association/IDS-G-pre/projects/1) provides and overview on the current status of the work. Pull requestes that are for review will be discussed in the Working Groups meetings based on the [Milestones](https://github.com/International-Data-Spaces-Association/IDS-G-pre/milestones) they relate to. Pull requests will be merged by the IDSA TSC after approval by Working Groups and TSC.

### IDS-TT
The IDS-ThinkTank is meant as a place for all community members to discuss new ideas and concepts. It is a private repository, but access will be provided to all IDSA members. Please use issues and pull requests, to provide transparency and an overview on current work entries.

## Bodies and roles

Bodies of the IDSA recommendation and specification development process
•	Working Group: An IDSA Working Group  
•	Sub Working Group: a named sub Working Group of an IDSA Working Group. The Working Group or the Steering Committee MAY create a Sub Working Group. The creation of a Sub Working Group MUST be documented in the Meeting Minutes.
•	Originator: A person from an IDSA member company, an IDSA member company or a group of IDSA members that are willing to work on a specified content or topic.
•	Technical Steering Committee: The IDSA Technical Steering Committee
•	Editor: A person named by the Working Group, that coordinates the development of documents. If no editor is named by the group, the chair of the Working group is the editor. 

## Definitions

The IDSA Recommendation and Specification Development Process is based on the W3C technical report development process and is designed to:
•	maximize consensus about the content of stable technical reports
•	ensure high technical and editorial quality
•	promote consistency among specifications
•	earn endorsement by IDSA members and the broader community.
 
### General Requirements
Every document published as part of the technical report development process must clearly indicate its maturity level, and must include information about the status of the document. This status information must be unique each time a specification is published,
must state which Working Group developed the specification, must state how to send comments or file bugs, and where these are recorded,
must include expectations about next steps, should explain how the technology relates to existing international standards and related work inside or outside W3C, and should explain or link to an explanation of significant changes from the previous version.
 
### Transition Requests
For all Transition Requests, to advance a specification to a new maturity level other than Note, the Working Group:
•	must record the group's decision to request advancement.
•	must obtain Editors approval.
•	must provide public documentation of all substantive changes to the technical re-port since the previous publication.
•	must formally address all issues raised about the document since the previous maturity level.
•	must provide public documentation of any formal objections.
•	should provide public documentation of changes that are not substantive.
•	should report which, if any, of the Working Group's requirements for this document have changed since the previous step.
•	should report any changes in dependencies with other groups.
•	should provide information about implementations known to the Working Group.


### Classes of Changes
This document distinguishes the following 4 classes of changes to a  document. The first two classes of change are considered editorial  changes, the latter two substantive chang-es.
 
1.	No changes to text content These changes include fixing  broken links, style sheets or invalid markup.
2.	Corrections that do not affect conformance Changes that reasonable implementers would not interpret as changing architectural or interoperability requirements or their implementation. Changes which resolve ambiguities in the specification are considered to change (by clarification) the implementation requirements and do not fall into this class. Examples of changes in this class include correcting non-normative code examples where the code clearly conflicts with normative require-ments, clarifying informative use cases or other non-normative text, fixing typos or grammatical errors where the change does not change implementation require-ments. If there is any doubt or dissent as to whether requirements are changed, such changes do not fall into this class.
3.	Corrections that do not add new features These changes may affect conformance to the specification. A change that affects conformance is one that:
makes conforming data, processors, or other conforming agents become non-conforming according to the new version, or makes non-conforming data, proces-sors, or other agents become conforming, or clears up an ambiguity or under-specified part of the specification in such a way that data, 
a processor, or an agent whose conformance was once unclear becomes clearly either conforming or non-conforming.
4.	New features Changes that add a new functionality, element, etc.

 
### Maturity Levels of the Recommendations and Specifications
IDSA differs 4 maturity levels of documents:
1.	Working Draft: Proposal of a Working Groups Subgroup to specify new features for existing recommendations and specifications or the creation of a new recommendation or specification, approved by the editor. The originator or subgroup should edit the document on IDS-G-pre, IDS-TT or IDSA Jive.
2.	Candidate Recommendation: Proposal approved by the editor for discussion in the Working Group. The Working Group discusses the document and may decide to return the document to the originator for further review. A change request to an existing recommendation or specification, that means a substantive change to the document, but does not implement new features, will be given to the Working Group as Candidate Recommendation. Candidate Recommendations should be provided as Pull requests on IDS-G-pre.
3.	Proposed Recommendation: Proposal accepted and approved by the Working group and the editor, to be approved by the Teechnical Steering Committee. The editor or the Technical Steering Committee may decide to return the document to the working group for further review. The Pull request stays unaccepted.
4.	Release: Current state of work, that is accepted and approved by the Working Group, the editor and the Technical Steering Committee. The Pull Request is merged to the main branch in IDS-G-pre. 


## Processes and workflows
### General Workflow

A template for a new Specification on IDS-G can be found [here](template_for_IDS-G_specifications.md).

### IDS-G
New content on IDS-G will be released on a quarterly basis with approved contnent by the IDSA-TSC. Please refer to the IDS-G-pre [Milestones]().

### IDS-G-pre
 
#### Tasks for the development
 
**Creation of a working Draft**
The Working Group decides with the approval of the editor to create a working draft on a specified content. The originators should create the Working Draft in Markdown in IFD-G-pre and collaborate on the content creation in issues, pull requests and drafts.
 
**Creation of a Candidate Recommendation**
Preparation:  The originators decides to raise the maturity of a document to a Candidate Recommendation by creating a Pull Request to the main branch in IDS-G-pre. 
 
The Working Group discusses and extends the Working Draft if necessary. The originators or the Working Group MAY create a public position paper with a notice of the maturity status Working Draft.
 
Decision: When the Working Group and the editor approve the Candidate Recommendation the originators have to stand up for the Candidate Recommendation. The Working Group MUST decide if the maturity is raised to Proposed Recommendation, the Proposed Recommendation is 
refused or the Proposed Recommendation must be adapted. The Pull Request will be provided to the IDSA-TSC for approval. 

The originators or the Working Group MAY create a public White Paper with a notice of the maturity status Candidate Recommendation.
 
**Creation of a Proposed Recommendation**
A Proposed Recommendation is checked by the editor for consistency with the current Recommendations and Specifications, with Candidate recommendations and Working Drafts. The Editor MUST notify the Technical Steering Committee of the new Proposed Recommendation. The Technical Steering Committee MAY refuse the change of the Maturity Level with tangible Change Requests. The TSC approves the document and merges it to main branch of IDS-G-pre.
 
The document category MUST be Proposed Recommendation. The originators or the Working Group MAY create a white paper with a notice of the maturity status Proposed Recommendation.
 
**Creation of a Recommendation / Specification**
Recommendations and Specifications are provided by the IDSA-TSC by merging a Pull Request to the man branch of IDS-G-pre. The originators or the Working Group MAY create a white paper with a notice of the maturity status Recommendation / Specification.
 
 
Changes to a recommendation /specification
A change request can be issued by the members of the Working Group or issued by a third party. The Editor MUST inform the Working Group on the Change Request. If the request is not a substantive change to the recommendation and specification, the Editor can change the current state of work. If the Change Request asks for a new feature in a specification or recommendation, a new Working Draft will be issued and approved by the editor. If the Change Request is a substantive change but no new feature will be introduced the corresponding Proposed Recommendation is issued as a Candidate Recommendation. This refers to the Classes of Changes.
 
#### Approval Stages
This document differs 3 decision types working group decisions / approval, Editors approval, Steering Committee approval.

•	Working Group Decisions and approval: The Working Group decides and approves in a meeting or online in the IDSA Jive collaboration platform in a ballot or in IDS-G-pre. A simple majority is required to decide or approve on topic. The decision MUST be documented in the meeting minutes. To speed up the process a monthly online session is set up for this development process. The originators have to present their results in order to get a decision or an approval from the Working Group.
•	Editors approval: The editor approves on o topic and documents this in the IDSA Jive platform. The Editor MAY request a ballot in the working group for the approval.
•	Techncial Steering Committee approval:  The Technical Steering Committee decides and approves in a meeting or online in the IDSA Jive collaboration platform in a ballot or in IDS-G-pre. A simple ma-jority is required to decide or approve on topic. The decision MUST be documented in the meeting minutes. The originators or the working group have to present their work in the Steering Committee meeting. The Steering Committee MAY speed up the approval by convoking an online meeting for this topic.

### IDS-TT
The IDS-ThinkTank is meant as a place for all community members to discuss new ideas and concepts. Access will be provided for employees of the IDSA members.

## Guidelines for working on documents

### Use relative path 
Links should always be relative, do not absolut paths

| Path |	Description |
|---|---|
| \[picture\]\(file.md\)	| The "file.md" file is located in the same folder as the current file |
| \[picture\]\(folder/file.md\) |	The "file.md" file is located in the folder folder in the current folder |
| \[picture\]\(../file.md\) |	The "file.md" file is located in the folder one level up from the current folder |


### Case sensitive

Github is case sensitive with regards to links, so please check your use of capital and small initial letters 

=======

