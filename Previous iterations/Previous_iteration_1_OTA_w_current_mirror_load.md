# Previous Iteration 1: Capacitor Connected to an Operational Transconductance Amplifier with Current Mirror Load

## Overview
This iteration was based on the concepts highlighted in the paper ‘Design and Implementation of Active Decoupling Capacitor Circuits for Power Supply Regulation in Digital ICs’ by Jie Gu, Student Member, IEEE, Ramesh Harjani, Fellow, IEEE, and Chris H. Kim, Member, IEEE.

## Motivation: The Miller Effect
The motivation to adopt this topology is to take advantage of the Miller effect. Shown in the diagram from the research paper below, the effective capacitance looking from the supply rails (input of the op-amp in this case) will be boosted by a factor of (1+A(ωj)) when a passive capacitor is connected between the output of the op-amp and an input of the op-amp. 

<img width="808" height="299" alt="image" src="https://github.com/user-attachments/assets/e50e6a2d-8525-4be8-9439-e053fbecf780" />


## Circuit Schematics

### Proposed Active Capacitor Circuit
The following shows the schematic of the active capacitor circuit shown in the paper.

<img width="829" height="356" alt="image" src="https://github.com/user-attachments/assets/57f93680-f019-4754-87bf-60dcc6df622f" />


As seen from the above the two-stage op-amp contains the following blocks providing different functionalities which I briefly explain here: 
1.  **Differential pair:** Senses the differential signal and provides the signal gain.
2.  **Source follower:** Drives the capacitive load.
3.  **0.8pF input coupling capacitors:** Transfers signal variations to differential pairs while maintaining different levels of DC biasing.
4.  **Transmission gates:** Always on to provide DC bias for the differential pair.

### Implemented Circuit (Cadence Virtuoso)
The picture below shows my implementation of the proposed circuit in Cadence Virtuoso.

<img width="940" height="507" alt="image" src="https://github.com/user-attachments/assets/0c970f0d-9b0a-43b0-ac50-84cfb69ffbca" />


### Simulation Setup
* **Iprobe:** Behaves as DC connected and AC disconnected in a closed loop during stability simulation.
* It inserts an AC signal to the loop at one end and measures the AC signal received at the other end.

### Differences Between Implementation and Reference Paper
The difference between my implementation and the circuit from the papers are in the following:

1.  **Biasing:**
    * *Paper:* Direct connection of the gate voltages of the two current source transistors in the op-amp to the positive supply rail to avoid extra routing effort.
    * *Implementation:* The gate voltage is biased by a regular current mirror which is biased by an ideal current source.
2.  **Multiplexers:**
    * *Paper:* Used to switch off the bias voltages of the current source transistors, cutting off static current during idle mode.
    * *Implementation:* Not included, as the focus is ensuring capacitance multiplication works.

## Performance Results
The following is the performance of the implemented op-amp circuit under a stability analysis with `iprobe` connected between the output and inverting input:

* **Loop gain:** 21.32 dB 
* **Bandwidth (-3dB):** 75 MHz 

From the results, the circuit shows its potential of serving as a capacitance multiplier, given a 10.66 times gain is achievable within the desired frequency range.

## Challenges and Failure Analysis
Nevertheless, the circuit did not make it to the capacitance multiplication test stage due to the following reasons:

1.  **Transmission Gate Issues:**
In my implementation, the transmission gates act like a short circuit once the iprobe is removed, which formed a a closed loop consisting of the output node and inverting input, limiting the gain to unity. In short, the self-biasing property of the differential input gate is achieved at the cost of achieving the high gain that the circuit is expected to provide with the PMOS current mirror load, as the current mirror shall provide a high output resistance seen from the drain of the differential input pairs. 

2.  **Biasing Difficulties:**
The most straightforward solution was to unplug the transmission gates and explore other biasing methods, however, this will lead to other issues:

i. Without proper biasing, the DC operating voltage of the differential input gate and source follower input node will be undefined.

ii. Thus, to define the DC operating voltage of the source follower input node, one solution is to replace the current mirror NMOS into a resistor or a diode-connected NMOS in the source follower branch. This will help in defining the DC operating voltage of the source follower input node but will cause other issues related to source degeneration resistance, which shall be investigated further.

iii. Therefore, another solution is to replace the current mirror load for a pair of diode-connected PMOS in the differential pair, which leads to the second iteration of the voltage mode active capacitor multiplier circuit. This approach works as VDS = VGS will be defined by the drain current passing through the diode-connected PMOS, and VGS can be further tuned by adjusting the tail current and the W/L of the diode-connected PMOS.

iv. Further work must be done to bias the differential input pair gate DC operating voltage.

## Conclusion and Lessons Learned
This is where the URECA project started. Although this concept did not work and must be abandoned, the following are the lessons learned:

### 1. Iterative Design Process
The design started out with a few specs, for example VDD = 1.2V and current consumption should be less than 100uA, and was refined iteratively to bias the transistors into the desired operating region and achieve the desired gain and bandwidth performance. In the future, a more rigorous approach can be employed, which involves NMOS and PMOS characterization and hand calculations before the completion of any schematic or simulation. 

### 2. CMOS Process Knowledge
A native NMOS device is used as the source follower, which requires relatively low Vth to be turned on. This enables a wide range of output voltage swing, which prevents output signal degradation after transmitting through the source follower. Other low voltage devices are used in the second iteration of the voltage mode active capacitor multiplier circuit as well, where the diode-connected PMOS differential pair load has to be implemented in pch_lvt to make way for a sufficient headroom for output voltage swing.

<img width="508" height="242" alt="image" src="https://github.com/user-attachments/assets/0091d9b0-f5be-4b1e-b743-d6cff1487f8e" />

*Cross-sectional view of the native NMOS.*

### 3. Source Follower Design
Enough DC quiescent current quota has to be allocated to the source follower, to increase gm. A big gm value ensures unity gain, and reduces output resistance, as by low-pass filter model equation shown below:

<img width="283" height="91" alt="image" src="https://github.com/user-attachments/assets/50676872-172d-4e9f-b6e1-04e78b1cca47" />

This moves the pole created by the source follower beyond the unity-gain point, improving the phase margin.

The table of equations below (taken from ‘MICROELECTRONIC CIRCUIT DESIGN’ by Richard C. Jaeger and Travis N. Blalock) shows the equations which have been utilized to tune the differential input pairs and the source follower:

<img width="940" height="357" alt="image" src="https://github.com/user-attachments/assets/3e3a98fc-74b1-48b9-92f6-67663441d851" />

<img width="940" height="338" alt="image" src="https://github.com/user-attachments/assets/007efb31-21cc-4c29-8fea-e74667753709" />


### 4. Weak Inversion / Sub-threshold Operation
The differential input pairs of the op-amp in this project are operated in the sub-threshold region to achieve a high $g_m$ with very low biasing current.

**Drain current in Saturation Region:**

<img width="332" height="88" alt="image" src="https://github.com/user-attachments/assets/14b4f893-5f1b-43d2-9685-04f5d6c0deb3" />

Where the square root relation between transconductance and drain current is shown below:

<img width="472" height="241" alt="image" src="https://github.com/user-attachments/assets/9ffb2732-4326-44ef-b5c9-5320b82f53b3" />


**Drain Current in Sub-threshold Region:**

<img width="606" height="108" alt="image" src="https://github.com/user-attachments/assets/3226c03b-6f0f-49b5-a35e-ca1373d55d30" />


Where:
* $n$: Subthreshold slope factor 
* $I_{DO}$: Characteristic current 
* All voltages are referenced to the body/bulk voltage 

n can be found by the following formula:

<img width="409" height="114" alt="image" src="https://github.com/user-attachments/assets/0049c488-3cad-4133-8609-e74f583a835c" />

where CBC is the bulk junction capacitance per unit area, Cox is the gate oxide capacitance per unit area, q is the electron charqe and NFS is the fast surface-states 

**Condition for Weak Inversion:**

<img width="678" height="399" alt="image" src="https://github.com/user-attachments/assets/175efc61-f27f-4569-8c8d-0813bee7e428" />



**And finally, the linear relationship between transconductance and the DC drain current is shown below:**

<img width="662" height="155" alt="image" src="https://github.com/user-attachments/assets/2a139f03-7531-4753-af7e-36b665b42d4c" />



**Pros and Cons of Sub-threshold Operation:**
* **Pros:** very low current/power required to achieve a higher gm than a MOSFET in strong inversion, with gm comparable to that of a BJT due to the linear relationship between transconductance and DC biasing drain current.
* **Cons:**
i. poor high frequency performance 
ii. large transistor dimension, high consumption of silicon area 
iii. leakage current can be of the same order as bias current at high temperature 
iv. poor choice for low noise application 
v. poor control over IDO 

Nevertheless, the differential input pairs of the op-amp in this project are operated in sub-threshold region to achieve a high gm with very low biasing current. 

<img width="702" height="359" alt="image" src="https://github.com/user-attachments/assets/8f76622d-3272-4517-b021-3d9fb9e2e934" />


### 5. Transistor Length Tuning
The minimum transistor length of the PMOS current mirror load and the NMOS differential input pair must be set as 100nm instead of the minimum length allowed by TSMC 40n PDK, as an extremely short transistor channel length will lead to severe channel length modulation issues, as resistance is proportional to length, shortening the channel due to increasing drain source voltage decreases the output resistance thus reducing the gain of the amplifier. The effect is more pronounced in short channel transistors. However, the increment in transistor length shall be handled in moderation as the bandwidth will shrink significantly if the output resistance increases by too much.

### 6. Amplifier Characteristics
* **Gain:** Limited as there is only one gain stage.
* **Bandwidth:** Potentially wide if the supply current is large enough, since there is no multiple stages and the resistance and capacitance seen from the output of the gain stage are limited.

Below shows the upper cutoff frequency (dominant pole) of different configurations of a single transistor amplifier:
<img width="892" height="715" alt="image" src="https://github.com/user-attachments/assets/bb07feac-886e-47bc-bb51-c707b9ae9fa6" />

