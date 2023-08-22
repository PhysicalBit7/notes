---
layout: default
title: ASIC-FPGA
description: Chapter 7 notes
has_toc: false
nav_order: 7
parent: Heterogeneous Computing
permalink: /heterogenous-computing/Chapter7
---
# ASIC
{: .no_toc }

<details closed markdown="block">
  <summary>
    Table of contents
  </summary>
  {: .text-delta }
1. TOC
{:toc}
</details>

# Semiconductor Chips
Within semiconductor chips electrons can pass through the circuit and sometimes they cant. There are three regions to the semiconductor; the microprocessor, ASIC's(application specific integrated circuit), and the FPGA/CPLD

UV light is used to create the buses within a piece of silicon in order to create circuits

<p align="center">
  <img src="{{site.baseurl}}/assets/hetero-computing/photolith.png"  width="60%" height="30%">
</p>

# Application Specific Integrated Circuit (ASICs)
ASICs are integrated circuits that are specially designed in order to perform a specific function for a specific application as opposed to general, standard purpose microprocessors

ASIC designs are usually written in a hardware description language such as Verilog and VHDL

ASICs are needed as alternatives to combat Moores Law in order to make a computer more capable. The competition to achieve high speed and high capacities for specific applications to achieve high end application specific products. Apple phones and laptops are so fast and fluid because of ASICs. The hardware is very specially designed to achieve a specific purpose

Design for test(DFT) is inserted into the ASIC. It is special case logic designed for debugging the hardware

ASICs can be very expensive to design and also take a long time to complete. You also have to constantly update them in order to stay at the bleeding edge

## ASIC classification

<p align="center">
  <img src="{{site.baseurl}}/assets/hetero-computing/asics.png"  width="60%" height="30%">
</p>

### Full Custom ASICs
All of the mask layers(circuits) are fully custom. This fully custom design allows for the highest performance and the lowest part cost(smallest die size) for a given design

Some examples of technology that needs fully custom ASICs are high voltage(automobiles), analog/digital(communications), and memory(DRAM)

The benefits again are that the smallest die size can be used, enhanced performance of the IC, and the ability to integrate with analog components. Disadvantages include that the design process can take a long time, there is much more complexity, and the requirement for highly specialized equipment and personnel 

### Semi-Custom ASIC
In semi-custom ASICs all of the logic gates cells are pre-designed and only some or all of the mask layers are custom. There are two types of sub-designs; standard cell based ASICs and gate array based ASICs. Cell libraries are designed to design the chip and its functionality, the cell library is similar to a library of functions that can be called by a user

The __standard cell__ library is a library of all basic elements that make up the integrated circuit such as logic AND, OR, and XOR along with physical parameters such as delay time, inductance, and capacitance

<p align="center">
  <img src="{{site.baseurl}}/assets/hetero-computing/standardCell.png"  width="60%" height="30%">
</p>

The region below the standard cell are fixed areas that cannot change

__Gate array based ASICs__ are designed differently. The "gate" is used as a unit to measure the ability of semi-conductor chips to hold logic elements. Gate array design consists of two to nine metal semi-conductor layers where logical elements are uniformly distributed on

In a gate array based ASIC, the transistors are pre-defined on the silicon wafer. The pre-defined pattern of transistors is called the _base array_. The smallest element that is replicated to make the base array is called the _base_ or _primitive cell_. The design is performed by connecting pre-designed and characterized logic cells from a library(macros)

{: .note}
A gate could also go by the name cell, macro, or base

The top level interconnect between the transistors is defined by the designer in custom masks called __Masked Gate Arrays(MGA)__. Types of MGAs include:
1. Channeled Gate Array
2. Channel-less Gate Array
3. Structured Gate Array

The advantages to the gate array based design are the low cost of production, FPGAs also allow designers to reprogram without using any specialized tools. The disadvantages are that more sophisticated design software is needed

__Structured/Platform Design__ is a variation of gate array based ASIC design. Consists of the structured element and the array of structured elements. The main difference between gate array and structured ASIC design is that structured ASIC uses pre-defined structures

## FPGAs
Field programmable gate array's are rapidly growing due to the high benefits. The core is a regular array of programmable based logic, the core can also employ sequential and combinational logic. The architecture contains configurable I/O blocks, logic blocks, and interconnects

<p align="center">
  <img src="{{site.baseurl}}/assets/hetero-computing/asicCompare.png"  width="60%" height="30%">
</p>

# Design of ASIC
The ASIC cell library consists of a physical layout, behavioral model, and verilog/VHDL model. An ASIC (Application-Specific Integrated Circuit) cell library, also known as a standard cell library or simply cell library, is a collection of pre-designed and pre-verified semiconductor components (cells) that can be used as building blocks in the design of custom digital integrated circuits. These libraries are created and provided by semiconductor foundries or vendors to help chip designers in creating complex integrated circuits efficiently

ASIC cell libraries are an essential part of the ASIC design flow. Instead of designing every logic element from scratch, designers can leverage the pre-designed and optimized cells from the library. This greatly simplifies the design process, reduces design time, and ensures consistency and correctness of the circuit.

Here's a general overview of how an ASIC design flow might use an ASIC cell library:

1. __Design Entry__: The designer creates the logic design at a higher abstraction level using hardware description languages (HDLs) like Verilog or VHDL.
2. __Synthesis__: The high-level design is then synthesized into a gate-level netlist. During this step, the synthesis tool maps the logical description into the cells available in the ASIC cell library.
3. __Place and Route__: The gate-level netlist is placed and routed onto the physical layout of the chip. During this stage, the cells from the ASIC cell library are physically placed on the chip and connected with wires to create the final layout.
4. __Verification__: The design goes through various verification steps, such as functional verification, timing analysis, and power analysis, to ensure it meets the desired specifications.
5. __Tapeout__: Once the design is verified and meets all the requirements, it is sent for manufacturing (tapeout) to the semiconductor foundry, where the ASIC is fabricated.

# Programmable Logic Device(PLD)
PLDs are an integrated circuit that can be programmed/reprogrammed with a digital logic of a certain level. The integrated circuit contains a large number of gates, flip-flops, and registers that are interconnected on the chip. They can be configured by the user to perform a specific function. Many of the connections are fusable links that can be broken

A PLD consists of an array of AND gates and an array of OR gates. Each input feeds both a non-inverting buffer and an inverting buffer to produce the true and inverted forms of each variable. The AND outputs are called the _product lines_. Each product line is connected to one of the inputs of each OR gate

# FPGAs
Field programmable arrays are a dominant digital design implementation. FPGA gives us the ability to re-configure any digital logic function. Partial re-configuration allows a portion of the FPGA to be continuously running while another portion is being re-configured

Field programmable gat arrays consist of 
1. Logic Blocks: to implement combinational and sequential logic
2. Interconnect: wires to connect inputs and outputs to logic blocks
3. I/O blocks: special logic blocks at periphery of device for external connections

All of these parts are configurable. Users write configuration memory which defines the function of the system

## Configurable Logic Blocks(CLBs) Architecture
CLBs consist of lookup tables, carry and control logic, and memory elements. Lookup tables implement the entries of a logic functions truth table, some FPGAs can use LUTs to implement small RAM. Carry and control logic implement fast arithmetic operations. Memory elements are configurable flip flops/latches
