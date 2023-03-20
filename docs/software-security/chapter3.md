---
layout: default
title: Trust in Security
description: Chapter 3 notes
has_toc: false
nav_order: 3
parent: Software Security
permalink: /software-security/Chapter3
---
# Chapter 3
{: .no_toc }

<details closed markdown="block">
  <summary>
    Table of contents
  </summary>
  {: .text-delta }
1. TOC
{:toc}
</details>

---

# Trust Modeling


## Trust in General
Many different disciplines have different definitions of trust. *Philosophy* - Risky action deriving from personal, moral relationships between two entities. *Economics* - Expectations upon a risky action under uncertainty and ignorance  based on the calculated incentives for the action

__Composite Trust__ - multilayered trust dimensions
1. __Communication trust__ from communication networks such as quality of service (service response time, packet drop rates)
2. __Information trust__ from information networks (information credibility, integrity)
3. __Social trust__ from interactions/networks (sources reliability)
4. __Cognitive trust__ from cognitive process (cognitive information processing capability)

_Dictionary definition_: trust is assured reliance on the character, ability, strength, or truth of someone or something

---

### Measurement of Trust
**Trust** is a belief that does not necessarily require observed behavior in the past, that is distinct from trustworthiness, which is a verified objective of trust through observed evidence

**Distrust**       
Is not simply the complement of trust

**Untrust**      
Is defined as the state in which a trustee cannot reach the cooperation threshold in terms of its capability

**Undistrust**       
Means the lack of trust indicating when a trustor cannot sufficiently make a decision for whether he or she distrusts a trustee

**Mistrust**       
Derives from misinformation, often called misplaced trust, where a trustee does not have the intention to betray a trustor

**Misdistrust**      
Is defines as distrusting or ignoring an entity that is trustworthy

## Trust in Security
Systems must be built that match with the societal expectations of trust. Security must be built into every layer of a solution, it should'nt be an afterthought.

### Principles of Trust
Trust is 
1. a characteristic and quality of a security architecture. An equilibrium of trust must be made in a system
2. the enabling of confidence that something will or will not occur in a predictable or promised manner
3. binding of unique attributes to a unique identity, for example, accountability, basically, a trust relationship is established when a satisfactory level of confidence in the attributes provided by an entity is achieved
4. defined as a binary relationship, based on individual identity or unique characteristic validation
   1. the establishment of a trust relationship through a validation process and the subsequent use of that relationship in some transactional context

### Establishing Trust
To establish trust or confidence, there must be a binding of unique attributes to a unique identity, and the binding must be able to be tested satisfactorily by a relying entity. When you achieve a satisfactory level of confidence in the attributes provided by an entity, you establish a trust relationship

A trust model is the combination of security mechanisms in conjunction with the security policy when the address all business, technical legal, regulatory, or fiduciary requirements to the satisfaction of a relying entity

### Defining Trust Modeling
A security architecture, based on an acceptable trust model, provides a framework for delivering security mechanisms. Threat modeling identifies the specific mechanisms that are necessary to respond to a specific threat profile.

The purpose is to respond to a specific threat profile, a threat profile is the set of threats and vulnerabilities identified through a use-case-driven data flow analysis that is particular to an organization. __It identifies likely attackers and what they want__

Trust exists on a gradient

__Three Essentials__
1. Trust requirements must be matched to the specific kinds of threats or vulnerabilities facing an organization and to the degree of risk that the threats will occur
2. There must be a starting point in establishing credentials for identity
3. Trust does not happen spontaneously

__Threat Profile and Risk Analysis__      
Threat profiles and risk analyses are intrinsically related. Threat profiles identify the specific threats that are most likely to put your environment at risk

The most common types of threats fall into categories such as:
- Actual or attempted unauthorized probing of any system or data
- Actual or attempted unauthorized access
- Introduction of viruses or malicious code
- Unauthorized modification, deletion, or disclosure of data
- Denial of Service attacks

## Original Entity Authentication   
Permeates all trust models. Before trust can be made you have to have assurance that who you are speaking to is who they say they are. Original entity authentication occurs only once, at the beginning of a trust relationship. The more rigorous the original entity authentication process, the greater the degree of trust

For a library an ID is enough of an original entity authentication. For a financial institution they may need to use some sort of cryptographic key to verify the entity. To ensure a reliable validation or authentication process, tokens or credentials must be unique and bound to a particular entity

Process could be layed out like...
1. Entity A requests a trust relationship with Entity B
2. Entity B, in accordance with its stated security policy, requires Entity A to provide proof of identity
3. Entity B validates these proofs of identity
4. Entity B returns to Entity A some unique identity credential that Entity B can test to validate Entity A in future interactions

The last step outlines **bootstrapping**, meaning the association of a unique entity with a unique credential

Trust depends on the ability to bind unique attributes or credentials to a unique entity or user. There must be an agreed upon and standardized format for credentials, as well as for the protocols that are used to test those credentials marking off...
- **Authentication** - verification of identity
- **Authorization** - granting of specific privileges
- **Confidentiality** - information will not be accessed by unauthorized parties
- **Integrity** - data will not be modified by unauthorized parties
- **Non-repudiation** - legitimate transactions cannot later be denied by either the client or the server

_No spontaneous trust_, without reliance on mapping to previously established credentials, there cannot be any trust in an entity's identity. The server side implementation of SSL shows spontaneous trust as there is nothing that checks the browser's identity

## Qualities of Trust Relationships
__Portability__ and __Interoperability__ are similar but have subtle differences, portability depends on standardized credential types and formats to be used anywhere, and at any time. Interoperability depends on the standardization of protocols for testing credentials, it relies on applications and systems to implement standardized protocols for credential testing

_Portability_ refers to the ability of software or hardware to work on different platforms or environments without modification

*Interoperability*, on the other hand, refers to the ability of different systems or components to communicate and work together effectively

*Reliability* embraces a closely related aspect of credentials and their evaluation. Credentials must perform consistently over time

*Assurance* is a critical quality of any trust relationship, it is concerned with the preservation of the binding between a unique entity and its credentials, a credential continues to be accurately bound with the correct entity to which it is associated

## Major Trust Models

### Direct Trust
Exists when you perform the validation of an entity's credentials without reliance on any other entity. There is no delegation of trust, all relying parties are subordinate constituents of the trust hierarchy and all entities gain trust by their association with a common entity responsible for the original entity authentication of each relying entity

A direct trust model is found in some architectures using a **PKI**, a root certificate authority initiates all trust relationships.

#### Advantages of Direct Trust
The validation of credentials is performed by one's self with no delegation, thus ensuring a high level of confidence in every entity associated with the trust implementation, used often to reduce liability. Financial transactions, e-commerce, insurance, or health care should consider a direct trust model

#### Disadvantages of Direct Trust
Direct trust model is that it may be more labor intensive and more expensive than other trust models

### Transitive Trust
Trust transmitted through another party such as...
1. Entity A validates and trusts Entity B
2. Entity B validates and trusts Entity C
3. Entity A trusts but does not need to validate Entity C
   1. Original entity authentication does not need to be done from A to C

Such a trust model is used in peer-to-peer systems or distributive systems, for A to trust C, A needs confidence that B has validated C by the same standards A held to B

#### Advantages of Transitive Trust
The advantages of a transitive trust model is that it enables the linkage of different entities that share similar security policies while reducing the credential validation effort

### Assumptive Trust
A formal name for a model that was earlier described as spontaneous trust, you either take it or leave the trust. It is essentially one personally vouching for another. Examples include **PGP** or *pretty good privacy*.

A transitive trust model requires a validation process while an assumptive does not

## Developing Your Own Trust Model

**Security Requirements** -
at the very foundation of trust model development, there should be a basic principle governing acceptable use of both data and processing resources. Common security principle is that data must be accessible only on a need-to-know basis, and processing resources must be accessible only to those explicitly approved to use them

**Business Requirements** -
business requirements must be specific to the organization, although they may well be similar to those of other comparable organizations, if there is a requirement for a certain level of performance, it must be identified or found unacceptable

**Threat Profiles** -
built based on a data flow analysis, you should be cataloguing threats and vulnerabilities

**Risk Assessment** -
focus solely on technical issues, it cannot be stated enough that good security requires a combination of people, process, and technology. Look for places where human error or poorly designed processes introduce risk

**Security Mechanisms** - 
identify security mechanisms to respond to those risks that must be addressed

### Data Flow Analysis
A complete analysis requires examination of the type of data processed at each trust point, identification of the types of threats affecting each trust point, and specification of the appropriate countermeasures

## Summary
- Trust modeling is not an abstract intellectual exercise
- Is an essential step in designing
- Must match specific business requirements
- Must assign necessary resources to develop a model based on a threat profile and risk analysis and to identify appropriate response mechanisms
  - Establishment of trust does not happen spontaneously or without effort
- Do not focus solely on technical issues
- Failure to understand what trust model is effective can have devastating results
- Adversaries exploit weak trust models


