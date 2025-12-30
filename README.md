# Design of CMOS Active Capacitor

**Implemented and verified the circuit using TSMC 40nm Process Design Kit in Cadence Virtuoso.**

**Implemented the topology based on “High Accuracy CMOS cap multiplier” by Salvatore Pennisi.**

***Disclaimer***: This repository documents an exploration and implementation based on existing research papers. All underlying theory and ideas remain the intellectual property of the original authors; **I am responsible only for the implementation and simulation results presented here**.

## Project Overview:

This project is to explore the active capacitor design and demonstrate its performance while serving as the output compensation element for a Low-Dropout Regulator (LDO).

## Project Motivation:
* Analog integrated systems frequently require high-value capacitors for filtering and compensation.
* However, capacitors occupy large areas in silicon implementation and hence they suffer from the integration problem.
* Therefore, active capacitor, which makes use of circuit technique, provides a solution to enhance the capacitor value from a small capacitor, with the objective of saving silicon area.

This implementation solves these issues by effectively multiplying a small physical capacitor by a current gain factor $k$, creating a virtual input impedance of:

$$Z_{in} = \frac{1}{s \cdot k \cdot C}$$

which results in an equivalent capacitance:

$$C_{eq} = k \cdot C$$

<img width="682" height="408" alt="image" src="https://github.com/user-attachments/assets/682447e9-e485-4669-9264-3c602911163a" />

*Fig 1: Current-mode capacitance multiplier principle for a grounded capacitor C.*

## Theory:
*(refer to the annotated research paper for further explanation)*

1. Voltage fluctuation in the LDO output node is transmitted to the capacitor using a voltage follower transistor (common drain).
2. The capacitor generates capacitive current according to the transmitted voltage.
3. The capacitive current is sensed by the current mirror transistor, mirrored in the ratio 1:1 and later on 1:5, and fed back to the input node as the output current.
4. M3 is tied up to the $V_{in}$ also instead of a constant voltage, so that, when $V_{DS,M2}$ moves up and down due to $V_{GS1}$ increasing and decreasing, the same happens across M4 also, which improves matching.
5. Impedance Matching Technique: The gate of the mirroring transistor (M3) is connected directly to the input terminal $V_{in}$ rather than a constant DC bias voltage. Thus, transistors M2 and M4 operate under identical electrical and topological conditions.

<img width="549" height="531" alt="image" src="https://github.com/user-attachments/assets/0a723f2f-b7dc-4276-91e8-74e6fc56e8bd" />


#### Related Paper: A Novel High Speed Current Mirror Compensation Technique and Application
*By Thartfah Voo and Chris Toumazou*

* **a.** This method involves inserting a compensation resistor between the gates of the primary mirror transistors to introduce a zero that cancels the dominant pole.
* **b.** While theoretical models suggest this can extend bandwidth near the transistor's cut-off frequency, its application within the specific context of this LDO yielded only insignificant improvements in the overall phase margin.
* **c.** Consequently, while the technique remains a valid high-speed design principle, it did not serve as a primary contributor to the final stability metrics observed in this project.

<img width="483" height="238" alt="image" src="https://github.com/user-attachments/assets/db431a01-5f3a-40b0-8cbe-3ad76556a375" />

## Topology Analysis:

#### Benefits of the topology:
* **Simplicity**
* **Linearity and high bandwidth gain** as current mode is employed (as opposed to the voltage mode designs).
* **High bandwidth gain reason:** The R-C portion appears in the source follower-capacitor part, where the resistance looking into the source of the source follower is small.

#### Disadvantages of the topology:
* **High power dissipation:** Quiescent current increases with increasing multiplication factor.

#### Total DC Quiescent Current Consumption:

| Condition | Ideal 100pF Cap | Active Cap |
| :--- | :--- | :--- |
| **Full load (Load Current = 38mA)** | 38.5mA | 38.55mA |
| **No load** | 500.2uA | 549.8uA |

### Implementation in Cadence Virtuoso
The diagram below shows my implementation of the circuit in cadence virtuoso.

<img width="940" height="393" alt="image" src="https://github.com/user-attachments/assets/384b432e-3fb3-4b4c-a8d3-84804cf3cbca" />

---

## Simulation Results: Comparison with the Ideal Capacitor

The primary objective is to demonstrate that the proposed active capacitor circuit can effectively replace bulky physical capacitors in area-constrained integrated LDO designs.

The verification process involves a direct comparative analysis between:
1.  **A standard passive implementation** (LDO + ideal 100 pF physical output capacitor)
2.  **The proposed active solution** (LDO + 20pF capacitor connected to the active capacitor circuit with $k = 5$ multiplication factor)

### Control Variables:

#### Overall LDO Performance
* **Supply voltage:** 1.2V
* **Output voltage:** 1V

#### LDO Core Op-Amp (Cascode Symmetrical CMOS OTA) Performance:
* **Loop gain:** 52.023dB
* **Bandwidth:** 12.03kHz
* **Phase Margin:** 52.091deg
* **UGBW:** 3.21Mhz

**Schematic of the Cascode Symmetrical CMOS OTA:**
<img width="940" height="447" alt="image" src="https://github.com/user-attachments/assets/508c1b9c-668b-4b62-92d6-f3e89cc7f2d8" />

---

## Benchmarks
*The brief benchmark is shown in the below, please refer to the dedicated test and benchmark markdown file for further LDO simulations.*

### 1. Stability Analysis
Loop stability is critical in LDO design, where the output capacitor contributes a dominant pole and potentially a zero (via ESR) to the frequency response.


**Test Schematic:**
<img width="940" height="792" alt="image" src="https://github.com/user-attachments/assets/e884196c-8dba-43e0-bb21-a8a4df943129" />


**Results:**
The top section highlights the performance of the LDO with the 100pF cap, while the bottom section shows the performance of the LDO with the active cap setup.

<img width="783" height="347" alt="image" src="https://github.com/user-attachments/assets/3c47c4fa-13f6-4e38-b5db-d76faeeb24f1" />

* The similarity of measured figures in both cases suggests that the active capacitor circuit is effective in multiplying the capacitance of the 20pF capacitor by 5 times to 100pF.
* **Note:** There is ESR resistance associated with the active capacitor circuit that creates a zero which increases the phase margin of the LDO especially in low load current situations.


**Bode Plot:**
Below shows the bode plot of the LDO with the active capacitor connected, loaded with different load currents.
<img width="940" height="444" alt="image" src="https://github.com/user-attachments/assets/7f918878-c8c6-461c-9880-377af7c10788" />


#### Measurements (LDO with Active Cap Setup):

**1. Full load (iload = 38mA):**
* DC loop gain: 41.18dB
* Phase margin: 67.538deg
* Gain margin: 50.156dB
* Unity gain bandwidth: 1.42Mhz

**2. No load (iload = 0A):**
* DC loop gain: 62.58dB
* Phase margin: 63.642deg
* Gain margin: 38.312dB
* Unity gain bandwidth: 1.66Mhz



### 2. Line Transient Response
This test evaluates the regulator's ability to maintain a steady output voltage despite sudden variations in the supply voltage.

**Procedure:** A step function is applied to the LDO input voltage VDD creating a voltage deviation of +/- 10% with respect to the normal voltage level 1.2V, and the output voltage deviation is monitored.

<img width="429" height="453" alt="image" src="https://github.com/user-attachments/assets/97d2991f-6493-4fd2-a58e-46bc2c39d506" />


**Test Schematic:**
<img width="940" height="819" alt="image" src="https://github.com/user-attachments/assets/7311470d-ea31-424d-9ab3-c8e3cf874457" />


**Test Results:**
The output waveform of both the LDO with the 100pF cap and the LDO with the active cap setup closely resembles each other, which also suggests that the active capacitor circuit is effective in multiplying the capacitance of the 20pF capacitor by 5 times to 100pF.

<img width="940" height="443" alt="image" src="https://github.com/user-attachments/assets/a29ed358-80ac-460d-b09d-4bd6fdbf2d37" />


**When viewed separately:**

*Waveform of the LDO with the 100pF cap*
<img width="940" height="446" alt="image" src="https://github.com/user-attachments/assets/b061e6b5-4fc5-4734-9ca2-7a6736b9e938" />

*Waveform of the LDO with the active cap setup*
<img width="940" height="447" alt="image" src="https://github.com/user-attachments/assets/6c386348-7cda-41ea-a01b-74a89a4d81fb" />


#### Measurements (LDO with Active Cap Setup):

**1. Full load (iload = 38mA):**
* **Overshoot situation:**
    * Overshoot voltage: 985.242mV jump to 1.2271V
    * Settling time: 618.356ns - 300ns = **318.356ns**
* **Undershoot situation:**
    * Undershoot voltage: 1V drop to 759.675mV
    * Settling time: 1.627us - 1.11us = **0.517us**

**2. No load (iload = 0A):**
* **Overshoot situation:**
    * Overshoot voltage: 999.891mV jump to 1.231V
    * Settling time: 550.1313ns - 300ns = **250.1313ns**
* **Undershoot situation:**
    * Undershoot voltage: 1V drop to 772.22mV
    * Settling time: 1.372us - 1.11us = **0.262us**

---

## Future Work
*(Implementing other techniques proposed by Pennisi)*

### 1. High-Gain Cascaded Topology
* **Current Limitation:** In the standard single-stage mirror, the power dissipation increases linearly with the multiplication factor $k$.
* **Proposed Solution:** By cascading odd numbers of current mirror structures, the total gain becomes the product of individual stages $k_1 \cdot k_2 \cdot k_3$, while the quiescent current remains merely additive and thus increasing power efficiency.

$$I_{Q,TOT} = (4 + k_1 + k_2 + k_3)I_B$$
instead of
$$(2 + k_1 k_2 k_3)I_B$$

### 2. Electronic Tuning Mechanism
* **Problem:** CMOS fabrication processes often suffer from parameter tolerances, which can affect the accuracy of the synthesized capacitance.
* **Proposed Implementation:** Implementing triode-biased transistors as source degeneration resistors on the output current mirror.
* **Expected Outcome:** This modification will allow the multiplication factor to be fine-tuned via an external voltage $V_{TUNE}$.
* Based on the reference paper, this technique can provide a tuning range of approximately **+/-70%**, allowing for post-fabrication calibration.

---

## Documentation Navigation
This README provides a high-level overview of the active capacitor implementation. For detailed performance metrics, brief circuit explanations, and design evolution, please refer to the dedicated documentation files shown below.

### Detailed Analysis
1.  LDO Performance & Benchmarks 
2.  Core Op-Amp Design (Cascode Symmetrical CMOS OTA) performance and brief explanation (W.I.P)
3.  High-Speed Current Mirror Compensation brief explanation (W.I.P)

### Design History & References
4.  Previous Iterations: Two voltage mode op amp based active capacitor circuits 
5.  Reference Papers PDF files
