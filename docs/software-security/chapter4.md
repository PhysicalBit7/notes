---
layout: default
title: Chapter 4
description: Chapter 4 notes
has_toc: false
nav_order: 4
parent: Software Security
permalink: /software-security/Chapter4
---
# Chapter 4
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

# Secure Software
Core properties of secure software include 
- Confidentiality
- Integrity
- Availability
- Accountability
- Non-repudiation

Other properties that don't necessarily make software secure but characterize how secure it is are
- Dependability
  - property of software that ensures that the software always operates as intended
- Correctness
  - correctness of software should be consistently demonstrated under all anticipated operating conditions, correctness should be maintained under unanticipated conditions as well
- Predictability
  - the software's functionality, properties, and behaviors will always be what are expected, as long as the conditions under which the software operates are also predictable
  - the best way to ensure that predictability is met is to minimize the presence of vulnerabilities and other weaknesses, prevent the insertion of malicious logic, and isolate the software to the greatest extent possible from unanticipated environmental conditions
- Reliability
  - preserving predictability despite the presence of unintentional defects and other weaknesses and unpredictable environment state changes
  - fault tolerance
- Safety
  - depends on reliability and typically has very real and significant implications if the property is not met

**Software's Behavior**: testing needs to include observing the software's behavior under the following circumstances
1. Attacks are launched against the software itself
2. The software's inputs or outputs are compromised
3. The software's interfaces to other entities are compromised
4. The software's execution environment is attacked

Software security extends the requirements of reliability and safety to the need to preserve predictable, correct execution even in the face of _malicious_ attacks on defects or weaknesses and environmental state changes

## Influence the Security Properties of Software
Balance between engaging in defensive action and thinking like an attacker

**Defender Side:** build into the software appropriate security features and characteristics to make the software more resistant to attack and to minimize weaknesses that make it more vulnerable
**Attacker Side:** understand the exact nature of the threat that the software is likely to face so as to focus defensive efforts on areas of highest risk

### The Defensive Perspective
Analyze the software for vulnerabilities and opportunities for the security of the software to be compromised through malicious attack

- Address expected issues through the application of appropriate security architectures and features
- Address unexpected issues through the avoidance, removal, and mitigation of weaknesses
- Continually strive to improve and strengthen the attack  resistance, tolerance, and resilience of the software in everything they do

#### Addressing the Expected: Security Architecture and Features
Security features and functionality alone are insufficient to ensure software security. __Security architecture__ is the overall framework that holds these security functionalities together, the functionality and architecture of the software have to be checked for vulnerabilities

The activities and practices to reduce and mitigate weaknesses present in software can typically be categorized into two approaches:
- Application defense
  - Employing practices focused at detecting and mitigating weaknesses in software systems after they are deployed
  - Constraining the extent and impact of damage that might result from the exploit of a vulnerability in the application
  - Often the same as the techniques and tools used for securing networks, operating systems, and middleware services like intrusion detection tools and firewalls
- Software security
  - Not only incorporates protective, post-implementation techniques, but also addresses the need to specify, design, and implement an application with a minimal attack surface
  - Preventing weaknesses from entering the software in the first place or at least removing them as early in the life cycle as possible and before the software is deployed

#### Cyberinfrastructure Security
Research environments that support advanced data acquisition, data storage, and other computing and information processing services distributed over the Internet. In scientific usage, it is a technological and sociological solution to the problem of efficiently connecting laboratories and computers for novel scientific knowledge

**Security** is the degree of resistance to, or protection from harm

#### Attack Resistance, Attack Tolerance, and Attack Resilience
1. Attack Resilience - the ability of the software to prevent the capability of an attacker to execute an attack against it
   1. Often the most difficult to achieve, as it involves minimizing all weaknesses from every level, sometimes impossible to achieve
2. Attack Tolerance - the ability of the software to "tolerate" the errors and failure that result from successful attacks and, in effect, to continue to operate as if it had not occurred
3. Attack Resilience - the ability of the software to isolate, contain, and limit the damage resulting from any failures caused by attack-triggered faults that the software was unable to resist or tolerate and to recover as quickly as possible

## The Attackers Perspective
Much easier to find vulnerabilities in software than it is to make software secure, an attacker may have a physical copy of the software, or attack it over the Internet

Design patterns attempt to tackle head-on the problems of secure, stable, and effective software architecture and design. Attack patterns apply the problem-solution paradigm of design patterns in a destructive context. Gives software developers a representation of how attackers think, which enables them to anticipate attacks and take more effective steps to mitigate the impact

An attack pattern at a minimum should fully describe:
- What the attack looks like
- What sort of skill or resources are required to successfully execute it
- In which contexts it is applicable
- Should provide enough information to enable defenders to effectively prevent or mitigate it

### Attack Pattern Components
1. Pattern name and classification
2. Attack prerequisites - which conditions must exit
3. Description
4. Related vulnerabilities or weaknesses - Which vulnerabilities does this attack leverage?
5. Method of Attack 
6. Attack Motivation/Consequences - What is the attacker trying to achieve by using this attack?
7. Attacker skill or knowledge required
8. Resources required
9. Solutions and mitigation's
10. Context description - Which OS or language
11. References 

Attack patterns provide useful information to developers at all phases of development

### Positive and Negative Requirements
Security-focused requirements are typically split between
1. Positive requirements - functional behaviors the software must exhibit, often security features
2. Negative requirements - behaviors that the software must not exhibit if it is to operate securely 

Attack patterns can help identify requirements, many vulnerabilities result from vague specifications and requirements. Once requirements have been defined, all software must go through some level of architecture and design

### Leveraging Attack Patterns in Architecture Design
Some attack patterns describe attacks that directly exploit architecture and design flaws in software. Attack patterns at all levels can provide a useful context for the threats that the software is likely to face and thereby avoid those architecture or design features

It is essential to document any attack patterns used in the architecture and design phase so that the application can be tested using those attack patterns

### Leveraging Attack Patterns in Implementation and Coding
During this phase attack patterns enumerate the specific weaknesses targeted by relevant attacks and allow developers to ensure that they do not occur in their code

These weaknesses could take the form of implementation bugs or simply valid coding constructs that can have security implications if used improperly

### Leveraging Attack Patterns in Software Security Testing
Goal is to break things so that they can be fixed before an attacker can find them, can be done various ways
1. Unit Testing - generate proper tests for each component ensuring that each component at least resists weaknesses
2. Integration Testing - whether the individual components make differing assumptions that affect security, such that the integrated whole may contain conflicts or ambiguities
3. System Testing - the entire system is exercised and probed to ensure that it meets all of its functional and non functional requirements

**Important to perform security testing of the software in its actual operational environment**, vulnerabilities can often be masked by firewalls or application firewalls that aren't present in the applications actual environment
- White-box analysis - performing testing on a system you know
  - Good for finding unexpected architecture and design issues
- Black-box analysis - performing testing on a system you don't know
  - Good for finding the specific implementation issues you know to look for
- Gray-box analysis - a mix of white-box analysis and black-box analysis

__White-box testing__ is typically more thorough than black-box testing, it involves extensive testing by experts who have full access to the system, also can uncover obscure implementation bugs

Attack patterns can be used to determine areas that pose a risk, then white-box testing can scrutinize those areas

## Secure Software Development Life-Cycle
In the traditional SDLC, security is usually an afterthought, security is usually done after the software has been developed. However, vulnerabilities emerge anywhere making a before, during, and after approach to software development required

### Benefits to SSDL
Allows the security team to provide a quality review of the security requirements specification, attack use cases, and software design. Team will also understand the expectations and the risks associated with them, developing the most appropriate system environment

**Functional specifications** need to be evaluated at a minimum, those include
- *Regulations* - do security requirements meet the regulatory security requirements the project must adhere to?
- *Completeness* - evaluate the extent to which the security requirement is thoroughly defined and verify that it meets the regulatory requirements or security policies
- *Consistency* - ensure that each requirement does not contradict other requirements
- *Feasibility* - evaluate the extent to which a requirement can actually be implemented using the available technology
- *Testability* - evaluate the extent to which a test method can prove that a secure requirement has been successfully implemented
- *Priority* - help everyone understand the requirements relative value to stakeholders

### Components of SSDL
SSDL is geared toward ensuring successful implementation of secure software, it has six primary components...
1. Security guidelines, rules, and regulations
2. Security requirements: attack use cases
3. Architectural and design reviews/threat modeling
4. Secure coding guidelines
5. Black/gray/white box testing
6. Determining exploitability