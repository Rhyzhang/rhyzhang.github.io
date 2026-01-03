---
layout: page
title: Vending Machine
description: A 12-bit Finite State Machine built from scratch in Logisim
img: assets/img/vending_machine_main.png
importance: 2
category: work
related_publications: false
---


## Project Overview

I completed this project for my high school Computational Thinking class to conclude our unit on Digital Systems Architecture. It serves as the culmination of everything we built from scratch, ranging from basic XOR gates and flip-flops to more complex components like adders, ripple counters, synchronous counters, and 4-bit multipliers.

The system simulates the complete user experience from accepting multi-denomination currency, maintaining a credit balance, decoding user product selection, dispensing products, and calculating/dispensing precise change. The core challenge was orchestrating asynchronous user inputs into a synchronous, clock-driven system while managing a custom 12-bit datapath for financial calculations.

## High-Level Architecture

The system is designed around a modular architecture. Instead of a monolithic circuit, the machine is broken down into distinct functional blocks (Input, Processing, Output, and Display) connected by a central 12-bit data bus.


{% include figure.liquid loading="eager" path="assets/img/vending_machine_main.png" class="img-fluid rounded z-depth-1" %}

**System Specifications:**
* **Bus Width:** 12-bit (supporting values up to 4096, sufficient for currency operations in cents).
* **Clock:** Global system clock for register synchronization.
* **Display:** Multiplexed 7-segment displays driven by a custom Binary-to-BCD converter.
* **Products:** 3 selectable flavors (Milk Tea, Brown Sugar, Mocha) with distinct pricing.


## Subsystem Deep-Dive

### 1. Money Processing (Accumulator & Arithmetic)
The heart of the machine is the money handling logic. I implemented a cumulative adder that takes single pulse inputs from coin slots (1¢ to $20) and adds them to a running 12-bit register.

{% include figure.liquid loading="eager" path="assets/img/vending_machine_money_input.png" class="img-fluid rounded z-depth-1" %}

This module acts as a specialized CPU datapath:
* **Input Stage:** AND-gate arrays validate the specific coin inputs.
* **Accumulation:** A Super Adder creates a feedback loop with a register to hold the current balance.
* **Reset Logic:** A synchronous clear signal resets the balance to zero once a transaction is finalized.

### 2. Signal Decoding & State Management
The machine relies on a rigid Finite State Machine to handle user inputs. The "Flavor Input" module uses a decoder to translate a 3-bit binary selection code into specific "enable" lines for the product registers.

{% include figure.liquid loading="eager" path="assets/img/vending_machine_flavor_input_and_state.png" class="img-fluid rounded z-depth-1" %}

This ensures the system cannot attempt to dispense two products simultaneously.

### 3. Human-Readable Output (12-bit Binary to BCD)
One of the most complex combinatorial challenges was converting the 12-bit binary internal balance into a human-readable decimal format for the digital displays.

Since a standard hex display would be user-unfriendly, I engineered a 12-bit to Binary Coded Decimal (BCD) converter. This module uses a cascaded series of Add-3 (Double Dabble) algorithms to shift binary values into Ones, Tens, Hundreds, and Thousands columns.


{% include figure.liquid loading="eager" path="assets/img/12_bit_BCD_Converter.png" class="img-fluid rounded z-depth-1" %}

### 4. Transaction Finalization & Dispensing
When the "Buy" signal is asserted, the system performs a parallel check:
1.  **Comparison:** Is Balance >= Price?
2.  **Arithmetic:** Calculate `Balance - Price` to determine change.
3.  **Actuation:** If valid, trigger the product motor simulation and the change dispenser.


{% include figure.liquid loading="eager" path="assets/img/vending_machine_coin_dispense.png" class="img-fluid rounded z-depth-1" %}

{% include figure.liquid loading="eager" path="assets/img/vending_machine_product_dispense.png" class="img-fluid rounded z-depth-1" %}

The `vending_machine_product_dispense` module utilizes shift registers to simulate the time-delay of a motor turning, ensuring the "Product Dropped" signal is only sent after a specific number of clock cycles.

## Technical Challenges & Takeaways

* **Synchronous vs. Asynchronous domains:** The user pushes buttons asynchronously (randomly), but the internal logic is synchronous. I had to implement registers to "latch" these inputs to ensure the adder didn't miss coins or double-count due to switch bounce.
* **Datapath Width Constraints:** Managing a 12-bit bus required careful routing. Every logic gate interacting with the balance (Comparators, Adders, MUXs) had to be bit-width compatible.


This project demonstrated that complex systems like vending machines are just is layers upon layers of composition of basic logic gates. Computers are so cool!