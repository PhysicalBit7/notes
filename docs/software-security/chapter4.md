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

---

Other properties that don't necessarily make software secure but characterize how secure it is are
1. **Dependability**
  - property of software that ensures that the software always operates as intended
1. **Correctness**
  - correctness of software should be consistently demonstrated under all anticipated operating conditions, correctness should be maintained under unanticipated conditions as well
1. **Predictability**
  - the software's functionality, properties, and behaviors will always be what are expected, as long as the conditions under which the software operates are also predictable
  - the best way to ensure that predictability is met is to minimize the presence of vulnerabilities and other weaknesses, prevent the insertion of malicious logic, and isolate the software to the greatest extent possible from unanticipated environmental conditions
1. **Reliability**
  - preserving predictability despite the presence of unintentional defects and other weaknesses and unpredictable environment state changes
  - fault tolerance
1. **Safety**
  - depends on reliability and typically has very real and significant implications if the property is not met

**Software's Behavior**: testing needs to include observing the software's behavior under the following circumstances
1. Attacks are launched against the software itself
2. The software's inputs or outputs are compromised
3. The software's interfaces to other entities are compromised
4. The software's execution environment is attacked

Software security extends the requirements of reliability and safety to the need to preserve predictable, correct execution even in the face of _malicious_ attacks on defects or weaknesses and environmental state changes

# Influence the Security Properties of Software
Balance between engaging in defensive action and thinking like an attacker

**Defender Side:** build into the software appropriate security features and characteristics to make the software more resistant to attack and to minimize weaknesses that make it more vulnerable
**Attacker Side:** understand the exact nature of the threat that the software is likely to face so as to focus defensive efforts on areas of highest risk

## The Defensive Perspective
Analyze the software for vulnerabilities and opportunities for the security of the software to be compromised through malicious attack

- Address expected issues through the application of appropriate security architectures and features
- Address unexpected issues through the avoidance, removal, and mitigation of weaknesses
- Continually strive to improve and strengthen the attack  resistance, tolerance, and resilience of the software in everything they do

### Addressing the Expected: Security Architecture and Features
Security features and functionality alone are insufficient to ensure software security. __Security architecture__ is the overall framework that holds these security functionalities together, the functionality and architecture of the software have to be checked for vulnerabilities

The activities and practices to reduce and mitigate weaknesses present in software can typically be categorized into two approaches:
- **Application defense**
  - Employing practices focused at detecting and mitigating weaknesses in software systems after they are deployed
  - Constraining the extent and impact of damage that might result from the exploit of a vulnerability in the application
  - Often the same as the techniques and tools used for securing networks, operating systems, and middleware services like intrusion detection tools and firewalls
- **Software security**
  - Not only incorporates protective, post-implementation techniques, but also addresses the need to specify, design, and implement an application with a minimal attack surface
  - Preventing weaknesses from entering the software in the first place or at least removing them as early in the life cycle as possible and before the software is deployed

### Cyberinfrastructure Security
Research environments that support advanced data acquisition, data storage, and other computing and information processing services distributed over the Internet. In scientific usage, it is a technological and sociological solution to the problem of efficiently connecting laboratories and computers for novel scientific knowledge

**Security** is the degree of resistance to, or protection from harm

---

### Attack Resistance, Attack Tolerance, and Attack Resilience
1. **Attack Resilience** - the ability of the software to prevent the capability of an attacker to execute an attack against it
   1. Often the most difficult to achieve, as it involves minimizing all weaknesses from every level, sometimes impossible to achieve
2. **Attack Tolerance** - the ability of the software to "tolerate" the errors and failure that result from successful attacks and, in effect, to continue to operate as if it had not occurred
3. **Attack Resilience** - the ability of the software to isolate, contain, and limit the damage resulting from any failures caused by attack-triggered faults that the software was unable to resist or tolerate and to recover as quickly as possible

Properties contributing to the attacking perspective

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
1. **Unit Testing** - generate proper tests for each component ensuring that each component at least resists weaknesses
2. **Integration Testing** - whether the individual components make differing assumptions that affect security, such that the integrated whole may contain conflicts or ambiguities
3. **System Testing** - the entire system is exercised and probed to ensure that it meets all of its functional and non functional requirements

**Important to perform security testing of the software in its actual operational environment**          
Vulnerabilities can often be masked by firewalls or application firewalls that aren't present in the applications actual environment
- **White-box analysis** - performing testing on a system you know
  - Good for finding unexpected architecture and design issues
- **Black-box analysis** - performing testing on a system you don't know
  - Good for finding the specific implementation issues you know to look for
- **Gray-box analysis** - a mix of white-box analysis and black-box analysis

__White-box testing__ is typically more thorough than black-box testing, it involves extensive testing by experts who have full access to the system, also can uncover obscure implementation bugs

Attack patterns can be used to determine areas that pose a risk, then white-box testing can scrutinize those areas

# Secure Software Development Life-Cycle
In the traditional SDLC, security is usually an afterthought, security is usually done after the software has been developed. However, vulnerabilities emerge anywhere making a before, during, and after approach to software development required

## Benefits to SSDL
Allows the security team to provide a quality review of the security requirements specification, attack use cases, and software design. Team will also understand the expectations and the risks associated with them, developing the most appropriate system environment

**Functional specifications** need to be evaluated at a minimum, those include
- *Regulations* - do security requirements meet the regulatory security requirements the project must adhere to?
- *Completeness* - evaluate the extent to which the security requirement is thoroughly defined and verify that it meets the regulatory requirements or security policies
- *Consistency* - ensure that each requirement does not contradict other requirements
- *Feasibility* - evaluate the extent to which a requirement can actually be implemented using the available technology
- *Testability* - evaluate the extent to which a test method can prove that a secure requirement has been successfully implemented
- *Priority* - help everyone understand the requirements relative value to stakeholders

## Components of SSDL
SSDL is geared toward ensuring successful implementation of secure software, it has six primary components...
1. Security guidelines, rules, and regulations
2. Security requirements: attack use cases
3. Architectural and design reviews/threat modeling
4. Secure coding guidelines
5. Black/gray/white box testing
6. Determining exploitability

### Phase 1: Security Guidelines, Rules, and Regulations
These three must be considered during the projects inception phase. A system wide specification is created that defines the security requirements that apply to the system, it can be based on specific government regulations that serve as a baseline for a company wide security policy. Important to constantly monitor the policy to continue enforcing it

### Phase 2: Security Requirements: Attack Use Cases
This second phase aids in the software design, implementation, and test case development. Determines technology choices and areas of risk. Security requirements need to be documented

__Security Defect Prevention__ - the use of techniques and processes that can help detect and avoid security errors before they propagate to later development phases

__Requirements Traceability__ - every security requirement is identified in such a way that it can be associated with all parts of the system where it is used. Helps identify what areas have been affected when a requirement changes

Sample security requirements could be checking that buffers do not overflow if the software is written in C or C++ or the application takes user input and uses SQL, so SQL injection mitigation's are a requirement. Also a good one is that a process uses multiple threads or processes and needs to protect itself from race conditions

### Phase 3: Architectural and Design Reviews/Threat Modeling
Security teams need a solid understanding of the product's architecture and design so that they can devise better and more complete security strategies. Early security involvement helps to eliminate confusion about the application's behavior later in the project lifecycle. Helps security to learn which aspects of the application are the most critical

The benefits of threat modeling are that it find different issues than code reviews and testing, and it can find higher-level design issues versus implementation bugs. Finding security problems early is a big plus

### Phase 4: Secure Coding Guidelines
A design vulnerability is a flaw in the design that precludes the program from operating securely no matter how perfectly it is implemented. Implementation vulnerabilities are caused by security bugs in the actual coding of the software

_Static analysis_ tools can detect many implementation errors by scanning the source code or the binary executable, there are also services that you can send your code to in order to analyze it for defects 

### Phase 5: Black/Grey/White Box Testing
The test environment is part of the security test planning, the test team needs to
- Schedule and track environment setup activities
- Install test environment hardware, software, and network resources
- Integrate and install test environment resources
- Obtain/refine test databases
- Develop environment setup scripts and test bed scripts

### Phase 6: Determining Exploitablity 
Ideally, every vulnerability discovered in the testing phase of the SSDL could be easily fixed. A vulnerability's exploitability is an important factor in gauging the risk it presents, this involves weighing five factors
1. The access or positioning required by the attacker to attempt exploitation
2. The level of access or privilege yielded by successful exploitation
3. The time or work factor required to exploit the vulnerability
4. The exploit's potential reliability
5. The repeatability of exploit attempts

This is where prioritizing the risks among each other comes in handy, the higher priority can be addressed first

### Summary
After this the application is now ready to be deployed, it is imperative that the secure defaults are set and understood and that testers verify the settings

## Deploying Applications Securely
Secure deployment should occur at the end of the lifecycle. Secure deployment has to be monitored constantly, and vulnerabilities have to be managed. An all-encompassing software patch management process needs to be in place

Infrastructure security, like a firewall, are assumed to be in place. Backup/recoverability and availability plans are in place. The focus of the SSDL is to address secure software development processes

### Patch Management
After software is deployed it is important to have a patch management process in place to manage vulnerabilities.

__Roles and Responsibilities__
1. Program or product manager should write the security policies
2. Architects and developers are responsible for providing design and implementation details
3. QA/testers drive critical analyses of the system, take part in threat-modeling efforts, determine and investigate threats, and build white bow and black bow tests
4. Security process managers can oversee threat modeling, security assessments, and secure coding training

### System Development lifecycle/ SSDL relationship
Again, deploying the application securely should be done at the end of the lifecycle. Determining exploitability, vulnerability, and patch management is best done during the maintenance phase, fixes become very expensive later on

## Summary of SSDL
1. Define security/software development roles and responsibilities
2. Understand the security regulations your system has to abide by, as applicable
3. Request a security policy if none exists
4. Request documented security requirements and/or attack use cases
5. Develop and execute test cases for adherence to umbrella security regulations, if applicable
6. Request secure coding guidelines, and train software developers and testers on them
7. Test for adherence to secure coding practices
8. Participate in threat-modeling walkthroughs, and prioritize security tests
9. Understand and practice secure deployment practices
10. Maintain a secure system by having a patch management process in place, including evaluating exploitability 

# Security Principles and Policies
Security is different from other IT services, the best security professionals combine there knowledge of computers and networks with general theories about security, technology, and human nature

## Security Principles
1. **Principle 1**: There is no such thing as absolute security
   1. Given enough time, tools, skills, and inclination, a malicious person can break through any security measure. Confidentiality is sometimes referred to as the principle of least privileges
2. **Principle 2**: The three security goals
   1. Confidentiality, Integrity, and Availability
3. **Principle 3**: Defense in depth as strategy
   1. Layered security is known as defense in depth, defense implemented as layers providing three elements: prevention, detection, and response
   2. Also seeks to offset the weaknesses of one security layer by the strengths of two or more layers
4. **Principle 4**: When left on their own, people tend to make the worst security decisions
   1. People are easily duped into giving up the secrets technologies use to secure systems
5. **Principle 5**: Computer security depends on two types of requirements: functional and assurance
   1. __Functional__ requirements - describe what a system should do
   2. __Assurance__ requirements - describe how functional requirements should be implemented and tested
   3. Both answer questions
      1. Does the system do the right thing, __Verification__. Ex. testing a seatbelt for functions such as stress tests on fabric, testing the locking mechanism, etc.
      2. Does the system do the right things in the right way, __Validation__. Ex. crashing the car with crash-test dummies inside to "prove" that the seat belt is indeed safe when used under normal circumstances
6. **Principle 6**: Security through obscurity is not an answer
   1. Means that hiding the details of the security mechanisms is not sufficient to secure the system alone, leads to a false sense of security
   2. The entire system collapses the second someone discovers the underlying security mechanisms
   3. Open source - anyone can gain access to program code, analyze it for security problems, and then share with the community
7. **Principle 7**: Security = Risk Management
   1. Spending more on security than the value of the asset is a waste of resources, security is not concerned with eliminating all threats but eliminating known threats and minimizing losses if exploited
   2. Risk analysis and risk management are central themes to securing computer systems
   3. When risks are well understood
      1. The risks are mitigated
      2. Insurance is acquired against the losses that would occur if a system were compromised
      3. The risks are accepted and the consequences are managed
   4. Risk assessment and risk analysis are concerned with placing economic value on assets to best determine appropriate countermeasures that protect them from losses
      1. What is a consequence of a loss?
      2. What is the likelihood that this loss will occur?
8. **Principles 8**: The three types of security controls are preventative, detective, and responsive
   1. Controls (documented processes) and countermeasures (such as firewalls) must be implemented as one or more of these: preventing a compromise, detecting that a compromise is underway, and responding to a compromise while it's happening or after it has been discovered
9. **Principle 9**: Complexity is the enemy of security
   1.  With too many moving parts in a system security plummets
10. **Principle 10**: Fear, uncertainty, and doubt do not work in selling security
    1.  IT has to justify investments in security, when justified they are rarely denied
11. **Principle 11**: People, policies, and technology are all needed to adequately secure a system or facility
    1.  Security practitioner needs a series of countermeasures and controls to implement an effective security system
    2.  People must agree to close down a security activity, polices are documented procedures done every time the same way, technology needs people
12. **Principle 12**: Open disclosure of vulnerabilities is good for security
    1.  Users have a right to know about security vulnerabilities within their system, just like automobile recalls

## Security Policies
A security policy is a document that defines hwo an organization will deal with some aspect of security, can also be created to deal with regulatory requirements, policies can also be advisory

Types of policies include...
1. **User policies**
   1. Passwords - never written down or shared
   2. Internet Use - some web activities are not appropriate for a company's network
   3. Email Usage - do not open emails that include executables, from an unknown email address, is an animation/movie
   4. Installing/Uninstalling software - end users should not be allowed to install software
   5. Instant Messaging - can pose a risk, threat from a purely informational security perspective
   6. Desktop Configuration - background images can have malicious content, also you have to give a user rights to change the background
2. **System administration policies** - must be a procedure for adding users, removing users, dealing with security issues, changing any system, etc.
   1. When new employees are hired they must be given access to resources, this must be documented and the user must be given security policy details
   2. Departing employees logins must be terminated along with all access (VPN, keys, accounts, etc)
   3. Change requests must follow steps
      1. Manager must sign approval of the request
      2. Departments verify those changes can be made
      3. Security verifies the change doesn't raise security concerns
      4. Department plans for the change and can roll back if needed
      5. The date and time for the change is scheduled, and all parties notified
   4. **Security Breaches**
      1. Virus Infection - immediately quarantine and shut off all access to internet. After you can scan and clean, log, and notify the appropriate people to talk about what happened and how to prevent it from happening again
      2. Denial of Service Attacks - Routers and Firewalls must be properly configured to prevent these attacks, find the origin IP and contact that organization, log all details, have a meeting to discuss what can be learned
      3. Intrusion by a hacker - copy logs of all affected systems for evidence, scan all systems for changes, trojan horses, port filtering, new services running, changes to firewall setting, document everything, change all passwords, repair damage, and inform appropriate people whilst having a meeting
3. **Access control policies**
   1. Fine balance between giving access and denying, best to practice least privileges concept and implicit deny(all users being immediately denied access until someone explicitly grants them access)
   2. Proper separation of duties, job rotation, and mandatory vacations are all important. Mix up who sees what to make it more difficult to exploit something
4. **Developmental policies**
   1. You cannot protect bad code, follow guidelines to lower the odds of a security breach

### Secure Programming
All code, especially that done by third parties, must be checked for back doors, all buffers must have error handling, all communication (like TCP sockets) must adhere to communication guidelines.

Any code that opens a port must be documented, all input is filtered for items that might facilitate an attack such as a SQL injection attack, and all vendors should supply you with a signed document verifying that there are no security flaws in the code

Many people are responsible for security such as developers, users, CISO, operators, and information resource managers

# Security Requirements
Security requirements are specific to a system and provide protection of essential services and assets. A systematic approach to security requirements engineering will help avoid the problem of generic lists of features and take into account the attackers perspective


## Requirement Issues
Today's software development takes place in a changing environment resulting in requirements that constantly change. Many requirements are also ill described, they are specified without any analysis or modeling

Typically haphazard, with specified requirements being ambiguous, incomplete, or inconsistent

## Security Requirements Engineering
When security requirements are considered, they are often developed independently of other requirements engineering activities, little attention is devoted to what the system should not do

An attacker is typically looking for defects, not features. Think about the attackers perspective and not just the functionality of the system

### Techniques for Security Requirements
1. CLASP (comprehensive, lightweight application security process approach) - a life cycle process that suggests a number of activities to improve security
2. SQUARE (security quality requirements engineering) - aimed at security requirements engineering
3. Core security requirements artifacts - takes an artifact view and starts with the artifacts that are needed to achieve better security requirements, provides a framework 

Security is an emergent property of a system, not a feature. It cannot be bolted on afterwards, it must be built into the system from the beginning and continue until the end through all changes

## The SQUARE Process
Elicit, categorize, and prioritize security requirements. Builds security concepts into the early stages of the SDLC

1. Agree on definitions - must agree upon the definitions to use as many people have different backgrounds
2. Identify security goals - needed to support software development in the project at hand, provides a consistency check with the organizations policies and operational security environment
   1. A stakeholder in finance will hav different goals then a stakeholder in HR
3. Develop artifacts - a documented concept of operations for a project, necessary to support all subsequent security requirements engineering activities
4. Perform risk assessment - a recommended method based on the unique needs of the organization, artifacts provide the input to the risk assessment process, outcomes identify high-priority security exposures
5. Select elicitation technique - elicitation could simply consist of sitting down with a primary stakeholder and trying to understand that stakeholders security requirements needs
6. Elicit security requirements - actual elicitation process using the selected technique, most elicitation techniques provide detailed guidance on how to perform elicitation. This effort builds on artifacts developed in earlier steps such as; misuse and abuse cases, attack trees, threats, and scenarios
7. Categorize requirements - allows an engineer to distinguish between essential requirements
8. Prioritize requirements - depends on the step above, may also involve a cost-benefit analysis to determine which security requirements have a high payoff relative to their cost. May also depend on other consequences like loss of life, loss of reputation, etc.
9. Requirements inspection - can be done at varying level, it should leave an understanding of which areas are incomplete and must be revisited at a later time

---

__Use/Misuse Cases__ - a security misuse case is a variation on a use case. It is used to describe a scenario from the point of view of the attacker, or an abuse case

Use Cases have proven effective in documenting normal use scenarios while misuse cases are effective in documenting intruder usage scenarios and ultimately in identifying security requirements

---

# Security Architecture and Design
Security architecture is the process of selecting design elements and principles to match a defined security need. Serves as a framework for secure design, which embodies the four classic stages of information security: protect, deter, detect, and react

Architecture is different from policy, a policy is formulated for your enterprise, and then used as a guideline for your application architecture

## Architectural Document

**Architecture should discuss or influence**
- Program organization
- Change strategy
- Buy versus build decisions
- Major data structures
- Key algorithms
- Major objects
- Generic functionality
- Error processing(corrective or detective)
- Active or passive robustness
- Fault tolerance

__Principle 1: Start by asking questions...__  
1. About our worries...
   1. What can go wrong?
   2. What are we trying to protect?
   3. What is the weakest point in our defense?
2. About our resources...
   1. Do we have a security architecture? Is it really being used?
   2. Do we have access to a reusable software library or repository?
3. About the software itself...
   1. Where does this piece of software stand in the chain of trust? Are there downstream critical applications that will rely on us for authentication? Are there upstream libraries or utilities that may or may not feed us reliable input?
   2. Who are the legitimate users of this piece of software?
4. About our goals...
   1. What impact would a security compromise of this software have? How much money would we lost, directly and indirectly? What would be the impact on corporate operations, reputation, and morale?
   2. Who are we willing to irritate or slow down with security measures, and to what degree?

__Principle 2: Select a destination before stepping on the gas__     
Understand fully what needs to be done before you start making decisions

__Principle 3: Decide how much security is "Just Enough"__    
How secure does your application need to be? Identify tradeoffs and costs explicitly. If you have to compromise security for usability be explicit about the issues and make an appropriate decision

__Principle 4: Employ standard engineering techniques__     
Good security requires good design and good design techniques, many attacks are bred from lack of design

__Principle 5: Identify your assumptions__    
One key part to security engineering analysis is to decide on assumptions, many applications accept input emitted from other software agents upstream

__Principle 6: Engineer security in from day one__    
Many cases of software compromises result from the failure of last-minute security fix-ups, security features must be integrated from an early stage

__Principle 7: Design with the enemy in mind__    
Anticipate attacks from intelligent adversaries, they can come from outside or inside your network. The more you think about who might attack your software or your enterprise, the better equipped you will be to design securely

__Principle 8: Reluctance to trust__    
Assume the environment your system resides in is insecure, trust should be closely held and never loosely given. You must anticipate malformed input from unknown users. Minimizing the trust in other systems can increase the security of your application

__Principle 9: Least privilege__    
Only the minimum necessary rights should be assigned to a subject. Careful delegation of access rights can limit attackers ability to damage a system

__Principle 10: Complete mediation__    
A system that requires access checks to an object each time a subject requests access decreases the chances that the system will mistakenly give elevated permissions to that subject. Caching permissions can increase the performance of your application at the cost of security

__Principle 11: Build in appropriate levels of fault tolerance__  
Identify mission-critical functions then use the three R's
- Resistance
- Recognition
- Recovery

__Principle 12: Degrade gracefully__    
When a program runs into trouble, it should operate in a restricted or degraded way. It shouldn't just stop and panic

__Principle 13: Fail securely__     
When a system fails, it should do so securely. On failure, undo changes and restore the system to a secure state. The confidentiality of a system should remain un-breached even though availability has been lost, attackers must not be permitted to gain access rights to privileged objects 

__Principle 14: Economy of mechanism__   
One factor in evaluating a systems security is its complexity, simplifying is not always easy but you should strive for implementing simpler systems when possible

__Principle 15: Secure the weakest link__   
Attackers are more likely to attack a weak spot in a software system than to penetrate a heavily fortified component. Knowing weak spots have been fortified can indicate to a software vendor whether the application is secure enough to be released

__Principle 16: Separation of privilege__   
Multiple conditions must be met before the system grants permissions to an object, checking access on only one condition may not be adequate for enforcing string security. Compartmentalizing can help deter and prevent attacks

__Principle 17: Least common mechanism__      
Avoid having multiple subjects share those mechanisms that grant access to a resource. A different mechanism for each subject can provide flexibility of access control among various users and prevent potential security violations that would otherwise occur if only one mechanism were implemented

__Principle 18: Never assuming that your secrets are safe__   
Relying on an obscure design or implementation does not guarantee that a system is secure, you should always assume that an attacker can obtain enough info about your system to launch an attack. Using real protection mechanisms to secure sensitive information should be the ultimate means of protecting your secrets

__Principle 19: Psychological acceptability__     
Accessability should not be inhibited by security mechanisms, security mechanisms should not obstruct a user or they will turn it off. These mechanisms should be user friendly

__Principle 20: Promote privacy__     
Information leaks can cause customers to lose value in your product

__Principle 21: Self-limit program consumption of resources__     
A very common method of attack is exhausting system resources, use whatever the operating system makes available to limit the program's consumption of those resources. Resource consumption limitations must be combined with meaningful error recovery to be most effective

__Principle 22: Reuse code known to be secure__     
Open source code is often seen by far more eyes, vulnerabilities are more likely to be found and fixed sooner than in a proprietary case

__Principle 23: Don't rely on off-the-shelf software for security__     
Be very careful relying on such software for critical operations, sometimes an outsourced solution is the secure choice, but be aware of more risks

__Principle 24: Remember to ask, "What did I forget?"__   
Important habit

### The critical role of architecture and design
- Software architecture and design is where ambiguities and ideas are translated and transformed into reality
- From a functional perspective this transition from desire to actual form is second only to the requirements phase in contributing to the overall quality and success of the eventual software deliverable
- From a security perspective architecture and design is considered by many asa the single most critical phase of the SDLC

## Architecture Risk Analysis
Architectural risk analysis provides assurance that architecture and design-level security concerns are identified and addressed as early as possible, yielding improved levels of attack resistance, tolerance, and resilience

Consists of six activities...
1. **Software characterization**       
Full understanding of what the software is and how it works, requires minimal description using high-level diagramming techniques. Illustrates the true nature of the software
   1. Useful artifacts include: software business case, use case documents, quality assurance plan, etc.
2. **Threat analysis**        
Agents that violate the protection of information assets and site security policy. Identifies relevant threats for a specific architecture, functionality, and configuration
3. **Architectural vulnerability assessment**         
Examines the preconditions that must be present for vulnerabilities to be exploited and assesses the states that the software might enter upon exploit
   1. __Attack resistance analysis__ - examining software architecture and design for common weaknesses that may lead to vulnerabilities and increase the system's susceptibility to common attack patters. Relevant attack patterns should be mapped against the architecture
   2. **CAPEC** (common attack pattern enumeration and classification) describes the following classes of attacks
      1. Abuse of functionality
      2. Spoofing
      3. Probabilistic techniques
      4. Exploitation of privilege or trust
      5. Injection
      6. Resource manipulation
      7. Time and state attacks
   3. An architectural risk assessment must include an analysis of the vulnerabilities associated with the software's execution environment. The goal is to develop a list of software or system vulnerabilities that could be accidentally triggered or intentionally exploited, resulting in a security breach or a violation of the system's security policy
1. **Risk likelihood determination**         
Can be useful when prioritizing risks and evaluating the effectiveness of potential mitigation's
1. **Risk impact determination**       
   1. Three aspects of risk impact determination
      1. Identifying the threatened assets
      2. Identifying business impact
      3. Determining impact locality
2. **Risk mitigation planning**        
   1. Often require changes to multiple modules with it being architectural flaws
   2. Many mitigation's can be described as either detection strategies or correction strategies
   3. Mitigation mechanisms themselves can introduce threats and vulnerabilities to the software

# Control Flow Technology

## Behavior-Based Detection
Idea: Observe the program's behavior, is it doing what we expect it to? If not, it might be compromised. Can be challenging to define "expected behavior", detect deviations from expectation efficiently, and avoid compromise of the detector

## Control-flow Integrity (CFI)
Answers the above complications
1. Define "expected behavior"? __Control flow graph (CFG)__
2. Detect deviations from expectation efficiently? __In-line reference monitor (IRM)__
3. Avoid compromise of the detector? __Sufficient randomness, immutability__

CFI is quite efficient. Classic EFI (2005) imposes 16% overhead on average, 45% in the worst case. Modular CFI (2014) imposes 5% overhead on average, 12% in the worst case

Modular CFI can eliminate 95.75% of ROP gadgets on x86-64 versions of SPEC2006 benchmark suite