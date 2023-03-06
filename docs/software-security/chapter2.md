---
layout: default
title: Chapter 2
description: Chapter 2 notes
has_toc: false
nav_order: 2
parent: Software Security
permalink: /software-security/Chapter2
---
# Chapter 2
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

# Threat Modeling/Risk Analysis
A __threat model__ is essentially a structured representation of all the information that affects the security of an application. Can be thought of as viewing an application and its environment through security glasses. __Threat modeling__ is a process for capturing, organizing, and analyzing all of this information. Its purpose is to identify objectives and vulnerabilities, and then define countermeasures to prevent or mitigate the effects of the threats. Benefits include...
- Enables informed decision-making about application security risk
- Produces a prioritized list of security improvements to the concept, requirements, design, or implementation

Threat modeling is best applied __continuously__ throughout a software development project. Ideally a high-level threat model should be defined in the concept or planning phase and then refined as you move along in the development.

## Threat-driven Design
Different threat models will elicit different responses. __Snooping attackers__ means message traffic is visible, to fix use encrypted wifi (link layer), and encrypted network layer (IPSec), or encrypted application layer (SSL)

Any assumptions that are made in a threat model are potential __holes that the adversary can exploit__. Some bad assumptions include...
- Encrypted traffic carries no information
  - Not true, by analyzing the size and distribution of messages you can infer application state
- Timing channels carry little information
  - Timing measurements of previous RSA implementations could be used eventually to reveal a remote SSL secret key

## Finding a Good Model
- Compare against similar systems
  - What attacks does their design contend with?
- Understand past attacks and attack patterns
- Challenge assumptions in your design

__Four key questions you can ask yourself...__
1. What are you building?
   1. Diagrams are a good way to illustrate what you are building
2. What can go wrong?
3. What should you do about those things that can go wrong?
4. Did you do a decent job of analysis?

---

# Threat Modeling Methodology
Generally it is important to focus on finding high likelihood and high impact threats instead of attempting to evaluate all the possible attacks. The threat modeling process naturally produces an assurance argument that can be used to explain and defend the security of your application

**Threat Search Space**
The process of exploring the search space is iterative and constantly refined based on what you have done so far. The four broad steps include...
- Decompose the application or infrastructure
- Determine the threats
- Determine the countermeasures and mitigation's
- Rank the threats

**Basic Steps**
The basic threat modeling process consists of the following steps
- _Assessment Scope_
  - Understand what is on the line
  - Identify tangible assets
  - Understand the capabilities of the system
  - Less concrete things, such as reputation and goodwill are the most difficult to measure, but are often most critical
- _Identify threat agents and possible attacks_
  - Key component is the characterization of the different groups of people who might be able to attack your application

## Threat Modeling Process
1. Understand existing countermeasures
2. Identify exploitable vulnerabilities
   1. Once you have an understanding of the security of your application, you can then analyze for new vulnerabilities
3. Prioritize identified risks
   1. Prioritization is everything in threat modeling
4. Identify counter measures to reduce threat

---

# Structured approach to Threat Modeling
1. Focusing on Assets
2. Focusing on Attackers
3. Focusing on Software

## Focusing on Assets
1. Things attackers want
   1. Usually are assets that attackers want and are relatively tangible. Passwords, SSN, credit card numbers, etc.
2. Things you want to protect
   1. Many are intangible like the company's reputation or goodwill. Protect the things that matter to your customers and you will protect your image
3. Stepping stones to either of those
   1. Anything that can be used along the way to get to what attackers actually want

### Implementing Asset-centric modeling
Make a list of your assets and then consider how an attacker could threaten each, after you identify these trace them back to a computer system of sets of systems. Draw these systems in order to show the assets and other components that interconnect each.

## Focusing on Attackers
Getting into the mind of an attacker and testing a system is often more fun and productive
### Focusing on Software
Often the best structured threat modeling approach, they are models that focus on the software being built. Some projects have UML diagrams built or API documentation. Having teams draw on a whiteboard is often very productive.

Understanding is essential of your software, a good understanding offers insight into what can be attacked. Projects accumulate complexity often making security harder. Software centric threat modeling can help expose the threats within the complexity. Again creating a model helps to expose vulnerabilities. 

&nbsp;

### Models of Software
1. Data Flow Diagrams
2. UML
3. Swim Lane Diagrams
4. State Diagrams

---

- DFD

<img src="../assets/software-security/dfd.png"  width="70%" height="30%">
<img src="../assets/software-security/dfd2.png"  width="60%" height="20%">

---

- UML
Unified Modeling Language, adapted for use in threat modeling with the addition of trust boundaries. Complex but often beneficial as nuances are brought in.

---

- Swim Lane Diagrams
A common way to represent flows between various participants

<img src="../assets/software-security/sld.png"  width="30%" height="5%">

---

- State Diagrams
Represent the various states that a system can be in, and the transitions between those states.

<img src="../assets/software-security/state.png"  width="50%" height="10%">

---

### Trust Boundaries
Adding boundaries to show who controls what is a simple way to improve the diagram. Makes it simple in seeing the threats that would cross those boundaries. Examples include...
1. Accounts
2. Network Interfaces
3. Different physical computers 
4. Virtual machines
5. Organizational boundaries
6. Almost anywhere you can argue for different privileges

### Attack Trees
One of the most widely used and oldest applied techniques on cyber-only systems, cyber-physical systems, and purely-physical systems. Attack trees have since been combined with other methods. They are diagrams that show attacks on a system in a tree form. The root is the goal and the leaves are ways to get to the root. Attack trees can be built for whole systems or broken down into trees for each component. As mentioned, attack trees have been combine with other frameworks such as STRIDE, CVSS, and PASTA.

Once you've modeled your system, with say a DFD, you use an attack tree to analyze it.

### Creating an Attack Tree
1. Create a root node, this will be some goal of an attacker. Can also be a problematic state that the system can be in.
2. Create leaves. These are methods that when used help to get you to the root node
   1. First level leaves example...
      1. Attack a system:
         1. Physical access
         2. Subvert a software
         3. Subvert a person
   2. Remember that leaves can be an AND/OR state

---


# Stride
A mnemonic that stands for things that can go wrong in security. Stands for
1. Spoofing - pretending to be someone you are not
2. Tampering - modifying something that you are not supposed to modify
3. Repudiation - claiming you didn't do something (regardless of if you did it or not)
4. Information Disclosure - exposing information to people who are not authorized to see it
5. Denial of Service - attacks designed to prevent a system from providing service, crashing it, making it slow, or filling all its storage.
6. Elevation of privilege - when a program or user is technically able to do something that they are not authorized to

## Identifying Threats
Start with external entities. STRIDE is a tool to identify threats, not to categorize, don't ignore a threat because its not what your looking for right now, write them down. Focus on feasible threats.

With a decent sized list of threats
1. Mitigate them to make it harder to take advantage of
2. Eliminating threats is almost always achieved by eliminating features
3. You can transfer threats to be handled elsewhere, by OS or user

## Addressing Each Threat
Accepting the risk is the final approach to addressing threats. Accepting a risk means that you stop worrying about it

---

## Spoofing Threats
Examples...
- Spoofing a file
  - Creating a file in the local directory
  - Creating many files in a directory
  - Leverage the OS
- Spoofing a person
  - Setting email display name
  - Takes over real account
  - Addressed by usernames, biometrics, tokens
- Spoofing a role
  - Declares themselves to be that person

## Tampering Threats
Examples...
- Tampering with a file 
  - Modifying a file you own
  - Modifying files on a server
  - Addressed using OS ACLs
- Tampering with memory
  - Worry when a process with less privileges than you can alter memory
  - Modifies your code
  - Modifies data they supplied to your API
- Tampering with a network
  - Redirects the flow of data to their machine
  - Modifies data flowing over the network
  - Most common solutions are SSL and IPsec protocols

## Repudiation Threats
Repudiating an action
- Repudiating an action
  - Claims to not have clicked or received
- Attacking logs
  - Notices you have no logs
  - Puts attacks in the logs to confuse reader

Addressing usually means having tamper-resistant logging mechanisms

## Information Disclosure Threats
- Information disclosure against a process
  - Extracts secrets from error messages
  - Extract machine secrets from error messages
- Information disclosure against data stores
  - Takes advantage of inappropriate or missing ACLs
  - Finds files protected by obscurity
- Information disclosure against a data flow
  - Reads data on the network
  - Redirects traffic to enable reading on the network
  - Learns who's talking to whom by analyzing DNS information

Network monitoring takes advantage of the architecture of most networks to monitor traffic. Mitigating network information disclosure threats requires handling both spoofing and tampering threats

Names reveal information, create a parent directory with an innocuous name and use ACLs or permissions from OS. When designing an API select carefully what information you disclose

## Denial of Service Threats
- Denial of service against a process 
  - Absorbs memory 
  - Absorbs CPU
- Denial of service against a data store
  - Fills data store up
  - Makes enough requests to slow down a system
- Denial of service against a data flow
  - Consumes network resources

Mainly mitigated by limiting the resources an entity can acquire, also as far as network DoS, don't allow connections from the same IP over and over. Also elastic resource management is a good practice

## Elevation of Privilege Threats
- Elevation of privilege against a process by corrupting the process
  - Done by sending inputs that the code doesn't handle properly
- Gaining access to read or write memory inappropriately

Keep data and code separate. `.html` files can be tricky because they often contain code such as in the Javascript elements, and also data that is displayed. _Control flow memory corruption attacks_ generally takes advantage of weak typing and static structures in C-like languages to enable an attacker to provide code and then jump to that code. Using Java or C# is better as they are type safe languages.

Command injection attacks, an attacker supplies a control character, followed by commands. When dealing with Unix shell scripts, the shell can interpret a semicolon as the end of input, taking anything after that as a command

---

# CVSS
The Common Vulnerability Scoring System. CVSS captures the principal characteristics of a vulnerability and produces a numerical severity score. A system of metrics is used in order to give a vulnerability a score based on characteristics 

## Base Metric Group
Most fundamental qualities of a vulnerability, represents a general vulnerability severity. Consists of two subsets of three metrics each:
- Exploitability - access vector, access complexity, authentication
- Impact - confidentiality, integrity, availability

### Access Vector (AV)
Measures how remote an attacker can be to exploit a vulnerability
- __L__: the vulnerability is only exploitable locally(physical access or local account)
- __Adjacent Network__: the attacker must have access to either the broadcast network or collision domain of the vulnerable software
- __Network__: the vulnerable software is bound to the network stack and the attacker does not need local or adjacent network access to exploit it

### Access Complexity (AC)
Measure the complexity of attack required to exploit the vulnerability once an attacker has access to the target system
- __High (H)__: specialized access conditions exist, such as the attacker already having elevated privileges, spoofing additional systems, or relying on obvious and convoluted social engineering methods.
- __Medium (M)__: the access conditions are somewhat specialized, such as only certain systems or users being able to perform attacks, the affected configuration being uncommon, or some information gathering being required
- __Low (L)__: the access conditions are not specialized or extenuating circumstances, the attack requires little skill or information gathering

### Authentication (Au)
Measures the amount of times an attacker must be authenticated to a target system _once the system has been accessed_ in order to exploit a vulnerability
- __Multiple (M)__: exploiting the vulnerability requires the attacker to be authenticated two or more times, even if the same credentials are used each time
- __Single (S)__: one instance of authentication is required
- __None (N)__: the attack requires no authentication

### Confidentiality Impact (C)
Measures the impact on confidentiality of a successfully exploited vulnerability
- __None (N)__: no impact on confidentiality
- __Partial (P)__: considerable informational disclosure, such as access to some files or certain database tables
- __Complete (C)__: total information disclosure; the attacker can read all of the system's data (including files and memory)

### Integrity Impact (I)
Measures the impact to integrity of a successfully exploited vulnerability
- __None (N)__: no impact on integrity
- __Partial (P)__: modification of some system files or information
- __Complete (C)__: total compromise of system integrity; the attacker can modify all of the system's data (including files and memory)

### Availability Impact (A)
Measures the impact to availability of a successfully exploited vulnerability
- __None (N)__: no impact on availability
- __Partial (P)__: reduced performance or interruptions of resource availability
- __Complete (C)__: total shutdown of the affected resource

## Base Scoring
Computed by vendors and coordinators, each metric has a number assigned to each possible value, the final base score is between 0.0 and 10.0

__Base Vector__- a vector is a representation of the values assigned to the CVSS metrics. Sample vector: (AV:N/AC:L/Au:N/C:P/I:P/A:P)

---

__Temporal Metric Group__- time-dependent qualities of a vulnerability, representing urgency at a specific point in time. Three temporal metrics:
1. __Exploitability__
   1. Measures the current state of exploit techniques or code availability
      1. Unproven (U): no exploit code is available
      2. Proof-of-Concept (POC): POC exploit code is available
      3. Functional (F): functional explicit code is available
      4. High (H): either there is functional mobile autonomous code of no exploit is required and details are widely available
      5. Not Defined (ND): no value assigned, skip this metric in calculating the score
2. __Remediation Level__
   1. Measures the level of available remediation solutions
      1. Official Fix (OF): complete vendor solution available, patch or upgrade
      2. Temporary Fix (TF): official temporary fix available
      3. Workaround (W): unofficial non-vendor fix available
      4. Unavailable (U): either no solution is available or it is impossible to apply
      5. Not Defined (ND): no value assigned, skip this metric in calculating
3. __Report Confidence__
   1. Measures the degree of confidence in the existence of the vulnerability and the credibility of reports
      1. Unconfirmed (UC): a single unconfirmed source or possibly multiple conflicting reports: little confidence
      2. Uncorroborated (UC): multiple non-official sources, possibly including independent security companies
      3. Confirmed (C): vendor has reported/confirmed a problem with its own product, or functional exploit code is available
      4. Not Defined (ND): no value assigned, skip this metric in calculating the score

__Temporal Scoring__ - computed by vendors and coordinators, designed to be re-evaluated at specific times, has the following form (E:[U,POC,F,H,ND]/RL:[OF,TF,W,U,ND]/ RC:[UC,UR,C,ND])

---

__Environmental Metric Group__ - qualities of a vulnerability specific to a particular IT environment, five environmental metrics
1. __Collateral Damage Potential__
   1. Measures the potential for loss of life or physical assets through damage or theft of property or equipment, and economic loss of productivity or revenue
      1. None (N): no potential for physical assets, productivity or revenue damage
      2. Low (L): slight damage or loss of revenue or productivity
      3. Low-Medium (LM): moderate damage or loss of revenue or productivity
      4. Medium-High (MH): significant damage or loss of revenue or productivity
      5. High (H): catastrophic damage or loss of revenue or productivity
      6. Not Defined (ND): no value assigned, skip this metric
2. __Target Distribution__
   1. Measures the proportion of vulnerable systems in an environment
      1. None (N): no target systems exist, or targets are highly specialized and exit only in a laboratory environment
      2. Low (L): targets exist on a small scale
      3. Medium (M): targets exist on a medium scale
      4. High (H): targets exist on a considerable scale
      5. Not Defined (ND): no value assigned, skip this metric
3. __Security Requirements__
Customize score based on the importance of the targets to the organization in terms of below, each affects the weight of the corresponding base metric
   1. Confidentiality requirement
   2. Integrity requirement
   3. Availability requirement
      1. Low (L): likely to have only a limited adverse effect
      2. Medium (M): likely to have a serious averse effect
      3. High (H): likely to have a catastrophic adverse effect
      4. Not Defined (ND): no value assigned, skip this metric

__Environmental Scoring__ - computed by end users, each metric has a number assigned to each possible value, example form: (CDP:[N,L,LM,MH,H,ND]/TD:[N,L,M,H,ND]/CR:[L,M,H,ND]/IR)

## Example
CVE-2003-0062: Buffer Overflow in NOD32 Antivirus. Discovered in Linux and Unix versions prior to 1.013 that could allow local users to execute arbitrary code with the privileges of the user executing NOD32. To trigger the buffer overflow, the attacker must wait for another user to scan a directory path of excessive length

- Since the vulnerability is exploitable only to a user locally logged into the system, the **Access Vector** is `Local`
- The __Access Complexity__ is `High` because this vulnerability is not executed at the attackers whim
- **Authentication** is set to `None` because the attacker does not have to authenticate
- Each of the three __Impact__ metrics is set to `Complete ` because upon the buffer overflow a complete system compromise is possible
- 6.2 is the score given
- The base vector is AV:L/AC:H/Au:N/C:C/I:C/A:C
- Partial exploit code has been released so the __Exploitability__ metric is set to `Proof-of-Concept`
- The vendor has released updated software so the **Remediation Level** is given `Official-Fix`
- __Report Confidence__ is set to `Confirmed` 

### Score Usage
Intended as a generalization, primarily for comparing the relative severity of different vulnerabilities, does not reflect the likelihood of the attack, also does not take into account whether deployed security controls may prevent exploits

# PASTA
Process for Attack Simulation and Threat Analysis (PASTA) aims to bring business objectives and technical requirements together. This method elevates the threat-modeling process to a strategic level by involving key decision makers and requiring security input from operations, governance, architecture, and development.

Widely regarded as a risk-centric framework, PASTA uses an attacker centric perspective to produce an asset-centric output in the form of threat enumeration and scoring

## PASTA steps
The seven steps are as follows
1. Define business objectives
   1. Focus on what is important to your business, understand your application or product
      1. They could be driven by outside sources or internal, company could be focused on a resilient product, protecting assets and customers, or avoiding reputation risks
2. Define the technical scope of assets and components
   1. Understand the attack surface, and create a picture of what it is that you are protecting
      1. For each business component identify how they are configured, what dependencies they have on other internal applications, or where third party applications are used
3. Application decomposition and identify application controls
   1. Map the relationships between components
      1. Identify users and their roles and permissions, assets, data, services, hardware, and software
4. Threat analysis based on threat intelligence
   1. Research and find the credible threats that affect your industry and products, and build a threat library
      1. Utilize intelligence to understand the latest threats affecting your industry or products, and analyze application logs to understand the behaviors the system is recording, including attacks that existing protections have mitigated
5. Vulnerability detection
   1. Attacking stage
      1. Aim is to emulate the attacks that could exploit any identified weaknesses or vulnerabilities, and prove that the suspected risks to applications actually are risks, Attack Trees are recommended
6. Risk analysis and development of countermeasures
   1. Uses the answers from earlier stages
      1. What does my threat intelligence tell me about our risks in order to create countermeasures that are truly relevant to your business, product, and the actual threats you face

### The Benefits of PASTA Threat Modeling
Put security at the center of the entire business, stakeholders can understand goals and the cybersecurity threats, therefore influencing decisions. You can get a full picture of the threats an organization may face. A security team can prioritize threats to mitigate

Also get an understanding of the evolving threat landscape, PASTA works well with the constantly evolving security realm. Informed decision making about new product security against existing security protections





