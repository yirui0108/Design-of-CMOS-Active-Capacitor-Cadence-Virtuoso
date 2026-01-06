# Previous Iteration 1: Capacitor Connected to an Operational Transconductance Amplifier with Current Mirror Load

## Overview
This iteration was based on the concepts highlighted in the paper ‘Design and Implementation of Active Decoupling Capacitor Circuits for Power Supply Regulation in Digital ICs’ by Jie Gu, Student Member, IEEE, Ramesh Harjani, Fellow, IEEE, and Chris H. Kim, Member, IEEE.

## Motivation: The Miller Effect
The motivation to adopt this topology is to take advantage of the Miller effect.

As shown in the diagram from the research paper below, the effective capacitance looking from the supply rails (input of the op-amp in this case) will be boosted by a factor of $(1+A(\omega j))$ when a passive capacitor is connected between the output of the op-amp and an input of the op-amp.

![Fig 2. Principle of decap boosting based on Miller effect](path/to/Fig_2_Miller_effect.png)
*Fig. 2. Principle of decap boosting based on Miller effect. The decap value seen from the power supply has been boosted by a factor of $(1+A(\omega j))$.*

## Circuit Schematics

### Proposed Active Capacitor Circuit
The following shows the schematic of the active capacitor circuit shown in the paper.

![Fig 4. Schematic of the proposed active decap circuit](path/to/Fig_4_Schematic_proposed.png)
*Fig. 4. Schematic of the proposed active decap circuit.*

As seen from the above, the two-stage op-amp contains the following blocks providing different functionalities:
1.  **Differential pair:** Senses the differential signal and provides the signal gain.
2.  **Source follower:** Drives the capacitive load.
3.  **0.8pF input coupling capacitors:** Transfers signal variations to differential pairs while maintaining different levels of DC biasing.
4.  **Transmission gates:** Always on to provide DC bias for the differential pair.

### Implemented Circuit (Cadence Virtuoso)
The picture below shows my implementation of the proposed circuit in Cadence Virtuoso.

![Cadence Virtuoso Implementation Schematic](path/to/Cadence_Schematic.png)

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

1.  **Transmission Gate Issues:** In my implementation, the transmission gates act like a short circuit once the `iprobe` is removed, which formed a closed loop consisting of the output node and inverting input, limiting the gain to unity.
    * The self-biasing property of the differential input gate is achieved at the cost of the high gain expected from the PMOS current mirror load (which should provide high output resistance).
2.  **Biasing Difficulties:** The most straightforward solution was to unplug the transmission gates and explore other biasing methods, but this leads to other issues:
    * Without proper biasing, the DC operating voltage of the differential input gate and source follower input node will be undefined.
    * To define the DC operating voltage of the source follower input node, one solution is to replace the current mirror NMOS with a resistor or diode-connected NMOS in the source follower branch. This creates issues related to source degeneration resistance.

### Pivot to Second Iteration
Therefore, another solution is to replace the current mirror load with a pair of diode-connected NMOS in the differential pair, leading to the **second iteration of the voltage mode active capacitor multiplier circuit**.
* This approach works as $V_{DS} = V_{GS}$ will be defined by the drain current passing through the diode-connected PMOS.
* $V_{GS}$ can be tuned by adjusting the tail current and the W/L of the diode-connected PMOS.
* Further work must be done to bias the differential input pair gate DC operating voltage.

## Conclusion and Lessons Learned
This is where the URECA project started. Although this concept did not work and must be abandoned, the following are the lessons learned:

### 1. Iterative Design Process
The design started with a few specs (e.g., $V_{DD} = 1.2V$, current consumption < 100uA) and was refined iteratively to bias transistors into the desired operating region. In the future, a more rigorous approach involving NMOS/PMOS characterization and hand calculations should be employed before schematic simulation.

### 2. CMOS Process Knowledge
* **Native NMOS:** Used as the source follower because it requires relatively low $V_{th}$ to be turned on. This enables a wide output voltage swing, preventing signal degradation.
* **Low Voltage Devices:** Used in the second iteration where the diode-connected PMOS differential pair load has to be implemented in `pch_lvt` to allow sufficient headroom for output voltage swing.

![Cross-sectional view of Native NMOS](path/to/Native_NMOS_cross_section.png)
*Cross-sectional view of the native NMOS.*

### 3. Source Follower Design
Enough DC quiescent current quota must be allocated to the source follower to increase $g_m$. A large $g_m$ ensures unity gain and reduces output resistance, as shown by the low-pass filter model equation below:

$$f_p \approx \frac{1}{2\pi R_{out}C_{load}}$$

This moves the pole created by the source follower beyond the unity-gain point, improving the phase margin.

*(Refer to Table 14.11 and 14.12 from ‘MICROELECTRONIC CIRCUIT DESIGN’ by Richard C. Jaeger and Travis N. Blalock for single-transistor FET amplifier equations used for tuning).*

### 4. Weak Inversion / Sub-threshold Operation
The differential input pairs of the op-amp in this project are operated in the sub-threshold region to achieve a high $g_m$ with very low biasing current.

**Drain current in Saturation Region:**
$$i_D = \frac{K'_n}{2}\frac{W}{L}(v_{GS} - V_{TN})^2(1+\lambda v_{DS})$$


**Transconductance ($g_m$) in Saturation:**
$$g_m = \frac{2I_D}{V_{OV}}$$

**Drain Current in Sub-threshold Region:**
$$I_D = I_{DO}\frac{W}{L}e^{\frac{|V_G|}{nV_T}} \left[e^{-\frac{|V_S|}{V_T}} - e^{-\frac{|V_D|}{V_T}} \right]$$


Where:
* $n$: Subthreshold slope factor 
* $I_{DO}$: Characteristic current 
* All voltages are referenced to the body/bulk voltage 

**Slope Factor ($n$):**
$$n = 1 + \frac{C_{BC}}{C_{ox}} + \frac{qNFS}{C_{ox}}$$


**Condition for Weak Inversion:**
$$I_D \le \frac{W}{L}\mu C_{ox} \frac{(n-1)}{e^2} \left[ \frac{kT}{q} \right]^2 \text{ with } V_D - V_S > \frac{3kT}{q}$$


**Transconductance in Sub-threshold:**
$$g_m \approx \frac{I_D}{nV_T}$$


**Pros and Cons of Sub-threshold Operation:**
* **Pros:** Very low current/power required to achieve higher $g_m$ (comparable to BJT due to linear relationship).
* **Cons:** Poor high-frequency performance, large silicon area (dimensions), high leakage current at high temps, poor noise performance, and poor control over $I_{DO}$ .

![Graph of Subthreshold slope](path/to/Subthreshold_graph.png)

### 5. Transistor Length Tuning
The minimum transistor length of the PMOS current mirror load and NMOS differential input pair must be set to **100nm** (instead of the minimum 40nm).
* Extremely short channel lengths lead to severe channel length modulation.
* Resistance is proportional to length; shortening the channel decreases output resistance and reduces amplifier gain.
* Increment in length must be moderate to avoid shrinking bandwidth significantly.

### 6. Amplifier Characteristics
* **Gain:** Limited as there is only one gain stage[cite: 69].
* **Bandwidth:** Potentially wide if supply current is large enough, due to the lack of multiple stages and limited resistance/capacitance at the output[cite: 70].
