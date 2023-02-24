---
layout: default
title: Chapter 1
description: Chapter 1 notes
has_toc: false
nav_order: 1
parent: Software Security
permalink: /software-security/Chapter1
---
# Chapter 1
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

## What is Computer Security?
Computer security is the protection of the items you value, called the _assets_ of a computer or computer system. There are many types of assets, involving hardware, software, data, people, processes, or combinations of these.
- Computer objects of value
  - Hardware, software, and data - these represent intellectual endeavor or property
  - Computer systems have value and deserve security protection
  
Computer assets of different levels of value. Some hardware/software is easily replaceable; disk drives, networking gear, operating system, etc. Data such as documents, photos, and individual applications are unique and irreplaceable.

### Threat and Vulnerability
- A __vulnerability__ is a weakness in the system, for example, in procedures, design, or implementation, that might be exploited to cause loss or harm
  - A system may be vulnerable to unauthorized data manipulation because the system does not verify a user's identity before allowing data access
  - Summarized, a vulnerability is a weakness that could be exploited to cause harm
- A __threat__ to a computing system is a set of circumstances that has the potential to cause loss or harm
  - There are many threats to a computer system, including human-initiated and computer-initiated ones
  - _Threats usually exploit vulnerabilities_

---
#### Summarized
A vulnerability is a weakness in a system, process, or asset that could potentially be exploited by a threat actor (such as a hacker) to cause harm or damage. A vulnerability could be a flaw in software code, a misconfiguration in a network, or a weakness in physical security controls, among others.

On the other hand, a threat is a potential danger or harm that could be posed by an adversary, such as a hacker or a malicious insider. Threats can exploit vulnerabilities to carry out attacks or cause damage. Examples of threats include cyber-criminals, hackers, insiders, and natural disasters.

In summary, a vulnerability is a weakness in a system or asset that could be exploited by a threat, while a threat is a potential danger or harm posed by an adversary that could exploit a vulnerability. It's important to identify and address vulnerabilities in order to reduce the likelihood and impact of threats.

---

## Control
A human who exploits a vulnerability perpetrates an attack on the system, we use a __control__ or __countermeasure__ as protection.
- A control is an action, device, procedure, or technique that removes or reduces a vulnerability
- __Controls__ prevent threats from exercising vulnerabilities
  - A _threat_ is blocked by _control_ of a _vulnerability_


## CIA
Properties make your computer valuable to you. 
- Computer properties (CIA)
  - __Confidentiality__: the ability of a system to ensure that an asset is viewed only by authorized parties
  - __Integrity__: the ability of a system to ensure that an asset is modified only by authorized parties
  - __Availability__: the ability of a system to ensure that an asset can be used by any authorized parties
- Network Properties
  - __Authentication__: the ability of a system to confirm the identity of a sender
  - __Nonrepudiation or accountability__: the ability of a system to confirm that a sender cannot convincingly deny having sent something

![](../assets/software-security/cia.png)

### Confidentiality
Only authorized people or systems can access protected data. The properties of data confidentiality include...
- An unauthorized person accesses a data item
- An unauthorized process or program accesses a data item
- A person authorized to access certain data accesses other data not authorized (which is a specialized version of “an unauthorized person accesses a data item”)
- An unauthorized person accesses an approximate data value (for example, not knowing someone’s exact salary but knowing that the salary falls in a particular range or exceeds a particular amount)
- An unauthorized person learns the existence of a piece of data (for example, knowing that a company is developing a certain new product or that talks are underway about the merger of

Confidentiality can not only be thought of as protecting sensitive data, but also can be in relation to keeping hardware(a new invention) or a person(the whereabouts of a wanted criminal) confidential

#### Access control
A person, process, or program is(or is not) authorized to access a data item in a particular way
- We call the __person, process, or program a subject__, the data item is an __object__, the kind of access(read, write, execute) an __access mode__, and the authorization a __policy__

### Integrity
Can mean several things, properties include
- Precise
- Accurate
- Unmodified
- Modified only in acceptable ways
- Modified only by authorized people
- Modified only by authorized processes
- Consistent
- Internally consistent
- Meaningful and usable

![](../assets/software-security/acc-prec.png)

Accuracy refers to the closeness of a measured value to a standard, precision refers to the closeness of two or more measurements to each other

### Availability
Availability applies to both the data and to services(information and information processing). An object is thought to be available if the following are true...
- It is present in a usable form
- It has enough capacity to meet the service's needs
- It is making clear progress, and, if in wait mode, it has bounded waiting time
- The service is completed in an acceptable period of time

![](../assets/software-security/availability.png)


### Computer Security
Computer security seeks to prevent unauthorized viewing(confidentiality) or modification(integrity) of data while preserving access(availability).

A paradigm to computer security is access control
- A small, centralized control of access is fundamental to preserving confidentiality and integrity, but it is not clear that a single access control point can enforce availability
  - Some believe this creates a single point of failure

## Types of Threats
Threats can be caused both by human and other sources, they can be malicious or not, and can be targeted or random
- Advanced persistent threat attacks come from organized, well financed, patient assailants

![](../assets/software-security/threats.png)

## Types of Attackers
Can be individuals, organized: worldwide groups, organized: crime, or terrorists

Anti-Users: Motivation
- Curiosity
- Personal fame
- Personal gain
- National interests

Anti-Users: Skill
- Script Kiddie
- The undergraduate
- The expert

## Harms
Four acts can be done to cause harm to a system
1. Interception
2. Interruption
3. Modification
4. Fabrication

The negative consequence of an actualized threat is harm, we protect ourselves against threats in order to reduce or eliminate harm. The value of harm can change over time as asset value increases. Because resources are limited, we must choose what we want to protect. __Risk management__ involves choosing which threats to control and what resources to devote to protection

### Method-Opportunity-Motive
1. Method - the skill and knowledge to perform a successful attack
2. Opportunity - time and access by which to attack
3. Motive - a reason to want to attack

These are all necessary for an attack to succeed, deny any of these and the attack will fail

### Vulnerabilities
Vulnerabilities are weaknesses that can allow harm to occur. Harm occurs when a threat is realized against vulnerability. The possibility for harm to occur is called risk

### Controls
A __control__ or __countermeasure__ is a means to counter threats. To protect against harm, then, we can neutralize the threat, close the vulnerability, or both. Harm can be dealt with in several ways...
- Prevent it, by blocking the attack or closing the vulnerability
- Deter it, by making the attack harder but not impossible
- Deflect it, by making another target more attractive (or this one less so)
- Mitigate it, by making its impact less severe
- Detect it, either as it happens or some time after the fact
- Recover from its effects

***Types of controls include...***
1. __Physical controls__ stop or block an attack by using something tangible too, such as walls and fences
   1. Locks
   2. Human Guards
   3. Sprinklers and other fire extinguisher
2. __Procedural__ or __administrative__ controls use a command or agreement
   1. Requires or advises people how to act
      1. Laws or regulations
      2. Policies, procedures, guidelines
      3. Copyrights
      4. Contracts or agreements
3. __Technical controls__ counter threats with technology
   1. Passwords
   2. Program or OS system access controls
   3. Network protocols
   4. Encryption
   5. Network traffic flow regulators