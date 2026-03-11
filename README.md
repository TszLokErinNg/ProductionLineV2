---

This project is a month-long PLC automation project where I designed and programmed a working production line using Ladder Logic (LAD) on a Siemens PLC in TIA Portal. The system coordinates multiple parts of the line, including supply and storage sections, pick-and-place mechanisms for different box sizes, and various sensors, to move and process boxes automatically on the conveyor. With almost 30-40 hours of dedication per week, this is by far one of the biggest projects I have done in awhile, so I decided to share some of the most important takeaways and a deep dive of my project.

## Production line components

The production line consists of five stations: Control panel, incoming supply storage, component assembly, pallet and loading stations. During the transfer process it involves machining, pick and place mechanism and sensor controls. Below I will be sharing my experiences and mistakes I have made so that you don't have the make the same mistake again.

### Applications used:

1.  Siemens TIA portal v20 — Backend PLC programming tool application
2. PLCSIM — Simulating a Siemens S7-1200 PLC controller 
3. Factory IO — Frontend simulation

## Project motivation

This project is mainly to gain better insights of how an automated production line works in the industry, as well as understanding the common challenges that PLC programmers faced it involes controlling different industrial machines programmed and how they incorporated together. To line up all the dots and getting to utilize all the machines provided by FACTORY I/O. I have created a process flowchart of my production line.

## Project Gap

Even though we get to see how industrial machines work with PLC program through a simulator, the results in simulation are still superficial and not fully comprehensive, FACTORY I/O cannot 100% simulate the real impacts of machine faults ran though the program, therefore the learning outcome of the simulation could help programmers to develop PLC programming practices that fulfills the industrial standards and learning how to troubleshoot programming(backend) errors.

## Backend PLC programming

Here I will be sharing my general practices that I have learnt in school and how different that I have applied here to make a efficient, scalable and reliable system.

### 1. Categorize your IO tags

Categorizing IO tags is very essential and beneficial since programming every station would require at least 10 IOs, therefore organizing IOs with tag folders with the appropriate comments would help during the long run from the programming and troubleshooting perspective. 

### 2. Modular & Structured Programming

Breaking logic into focused Function Blocks (FBs) is essential for maintainability, it makes the program simpler to interpret and easier for maintenance, it can also be called while running just like functions in other software programming languages. I programmed each FB to handle a single task, for example, the Palletizer function only controls the palletizer machine, all relevant LAD will be written in the FB and later called from the Main block. 

### 3. Timers & Counters Techniques

The following are the optimal use cases for timers and counters:

| Technique | Use Case |
| --- | --- |
| **TON** (On-delay timer) | Debounce sensors, delay step transitions |
| **TOF** (Off-delay timer) | Keep outputs active briefly after condition clears |
| **CTU/CTD** (Up/Down counter) | Track pallet counts, cycle counts |
| **TONR** (Retentive timer) | Accumulate runtime hours for maintenance tracking |

### 4. Fault & Safety Techniques

Fault and safety techniques in PLC programming revolve around layered protection: 

1. **Watchdog timers** — set a max time per step; if exceeded, trigger a fault alarm — critical for your robotic arm misplacement issues
2. **Interlock logic** — prevent simultaneous conflicting outputs (e.g., arm moving while gripper is mid-action)
3. **Manual / Auto / Maintenance modes** — always structure your program with mode selection logic so technicians can override safely
4. **Limit OTL/OTU latches** — minimize latched outputs; prefer SR/RS flip-flop blocks for clarity

The overall priority list(level 1 as lowest, level 5 as highest):

- Level 5: E-Stop → Cut all outputs, halt everything
- Level 4: Safety Interlock violation → Halt sequence, set fault
- Level 3: Watchdog timeout → Halt sequence, set step fault
- Level 2: Sensor/feedback fault → Set specific fault bit
- Level 1: Warning → Log event, continue operation

### 5. Sequential Programming

Sequential programming in a PLC is used whenever a machine needs to follow a specific, repeatable **order of operations**. Instead of every part of the program fighting for control at once, the code is structured so that "Step 2" cannot happen until "Step 1" is successfully completed. This is the most common "State Machine" logic, where the PLC tracks exactly which phase of a process the machine is currently in. 

### 6. Analog Signal Handling

### 7. Naming & Documentation Standards

Always use **symbolic names** like `#SP_X` or `#Step` instead of raw addresses like `%QD48`, since symbolic names make your logic readable to anyone reviewing or maintaining the code without needing to cross-reference an address map. Every network should have a descriptive comment — following the same pattern as your Network 5 "Move arm to item position" — so that the intent of each rung is immediately clear during troubleshooting. Finally, adopt consistent tag prefixes across your entire project: `i_` for inputs (e.g., `i_Gripper_Feedback`), `o_` for outputs (e.g.,`o_Gripper_Enable`), `s_` for setpoints (e.g., `s_SP_X`), and `f_` for faults (e.g.,`f_Step1_Timeout`), this convention makes it instantly obvious what a tag does just by reading its name, which is especially valuable when navigating large projects like your ProductionV2 with multiple FBs and DBs.

## Frontend simulation

Here I will be sharing the general components I have used in the FACTORY I/O simulation software, why would I choose it, how I incorporate them together and most importantly the challenges and how I overcome it.

### General components used

### Station 1: Control panel

### Station 2: Incoming supply storage

### Station 3: Component assembly

### Station 4: Pallet

### Station 5: Loading station(autonomous forklifts)

## Traces and analysis

### 1. Sensor Hiccups

### 2. Analog noises

## Final thoughts

### Future research