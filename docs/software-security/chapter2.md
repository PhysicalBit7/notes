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

## Threat Modeling/Risk Analysis
A __threat model__ is essentially a structured representation of all the information that affects the security of an application. Can be thought of as viewing an application and its environment through security glasses. __Threat modeling__ is a process for capturing, organizing, and analyzing all of this information. Its purpose is to identify objectives and vulnerabilities, and then define countermeasures to prevent or mitigate the effects of the threats. Benefits include...
- Enables informed decision-making about application security risk
- Produces a prioritized list of security improvements to the concept, requirements, design, or implementation

Threat modeling is best applied __continuously__ throughout a software development project. Ideally a high-level threat model should be defined in the concept or planning phase and then refined as you move along in the development.

### Threat-driven Design
Different threat models will elicit different responses. __Snooping attackers__ means message traffic is visible, to fix use encrypted wifi (link layer), and encrypted network layer (IPSec), or encrypted application layer (SSL)

Any assumptions that are made in a threat model are potential __holes that the adversary can exploit__. Some bad assumptions include...
- Encrypted traffic carries no information
  - Not true, by analyzing the size and distribution of messages you can infer application state
- Timing channels carry little information
  - Timing measurements of previous RSA implementations could be used eventually to reveal a remote SSL secret key

### Finding a Good Model
- Compare against similar systems
  - What attacks does their design contend with?
- Understand past attacks and attack patterns

