# LDO Performance & Benchmarks

## 1. Overall LDO performance
* [cite_start]**Supply voltage:** 1.2V [cite: 3]
* [cite_start]**Output voltage:** 1V [cite: 4]

## 2. Total DC quiescent current consumption

### a. Full load (load current = 38mA)
* **i. [cite_start]Ideal 100p cap:** 38.5mA [cite: 7]
* **ii. [cite_start]Active cap:** 38.55mA [cite: 8]

### b. No load
* **i. [cite_start]Ideal 100p cap:** 500.2uA [cite: 10]
* **ii. [cite_start]Active cap:** 549.8uA [cite: 11]

## 3. Stability analysis with probe

### a. Test schematic
[cite_start]![Test Schematic for Stability Analysis](path/to/schematic_image_1.png) [cite: 13]

### b. Results
[cite_start]The top section is ideal 100pF cap, bottom section is active cap, ref to 100u iload, the active cap has some Resr effect to cancel the pole. [cite: 14]

**Stability Summary - circuit "test_LDO_stb_currentmode_fullrange_52dB" with loop probe "IPRB2"**

| iload | PM(deg) | @Freq(Hz) | GM(dB) | @Freq(Hz) |
| :--- | :--- | :--- | :--- | :--- |
| 0 | 58.632 | 1.6551M | 24.104 | 10.418M |
| 9.5m | 63.902 | 1.6503M | 40.301 | 32.001M |
| 19m | 64.861 | 1.5814M | 42.851 | 36.368M |
| 28.5m | 65.956 | 1.502M | 44.503 | 38.87M |
| 38m | 67.377 | 1.415M | 45.942 | 40.82M |

**Stability Summary - circuit "test_LDO_stb_currentmode_fullrange_52dB" with loop probe "IPRB0"**

| iload | PM(deg) | @Freq(Hz) | GM(dB) | @Freq(Hz) |
| :--- | :--- | :--- | :--- | :--- |
| 0 | 63.642 | 1.6612M | 38.312 | 28.927M |
| 9.5m | 64.337 | 1.6504M | 47.284 | 48.828M |
| 19m | 65.131 | 1.5814M | 48.198 | 50.113M |
| 28.5m | 66.157 | 1.5021M | 49.126 | 51.223M |
| 38m | 67.538 | 1.4151M | 50.156 | 52.477M |

### c. Bode Plot
[cite_start]![Stability Response Bode Plot](path/to/bode_plot_image.png) [cite: 15]

## 4. PSR test

### a. Test schematic
[cite_start]![PSR Test Schematic](path/to/psr_schematic.png) [cite: 17]

### b. Results
[cite_start]Red/orange is ideal, and blue/green is the one with active cap. [cite: 18]

[cite_start]![AC Response Graph](path/to/ac_response_graph.png) [cite: 18]

### c. Ideal cap PSR stats

**i. Iload = 38mA**
* [cite_start]**Low freq:** -51.53dB until 1kHz [cite: 21]
* [cite_start]**100kHz:** -43.46dB [cite: 22]
* [cite_start]**1Mhz:** -23.74dB [cite: 23]
* [cite_start]**Peak:** -18.46dB at 2.52MHz [cite: 24]

**ii. Iload = 0A**
* [cite_start]**Low freq:** -68.32dB until 500Hz [cite: 26]
* [cite_start]**100kHz:** -46.22dB [cite: 27]
* [cite_start]**1Mhz:** -25.46dB [cite: 28]
* [cite_start]**Peak:** -18.71dB at 2.52Mhz [cite: 29]

## 5. Transient analysis

### a. Test schematic
[cite_start]![Transient Analysis Test Schematic](path/to/transient_schematic.png) [cite: 31]

### b. Results
[cite_start]Red: ideal 100p cap; green: active cap, the waveforms coincide with one another similarly. [cite: 32]

[cite_start]![Transient Response Graph](path/to/transient_response_graph.png) [cite: 32]

### c. The performance of active cap

**i. Undershoot situation:**
* [cite_start]**Undershoot voltage:** 735.544mV [cite: 35]
* [cite_start]**Settling time:** 508.989ns - 200ns = [cite: 36]

**ii. Overshoot situation:**
* [cite_start]**Overshoot voltage:** 1.193V [cite: 38]
* [cite_start]**Settling time:** 1.298us - 1.005us = [cite: 39]

## 6. Line transient simulation
[cite_start]The supply voltage will be applied as a pulse with certain range, for example +/- 10%. [cite: 40]

### a. Line Transient Pulse settings
[cite_start]![Pulse Settings](path/to/pulse_settings_image.png) [cite: 41]

### b. Test schematic
[cite_start]![Line Transient Test Schematic](path/to/line_transient_schematic.png) [cite: 42]

### c. Results
[cite_start]The waveforms looks very similar when being put together, for both the ideal 100pF cap and the active cap. [cite: 43]

[cite_start]![Line Transient Response Combined](path/to/combined_transient_response.png) [cite: 43]

### d. Ideal 100p cap waveform
[cite_start]![Ideal 100p Cap Waveform](path/to/ideal_cap_waveform.png) [cite: 44]

### e. Active cap waveform
[cite_start]![Active Cap Waveform](path/to/active_cap_waveform.png) [cite: 45]

### f. Measurement relevant to the waveform of the LDO with the active cap setup

**Full load:**
* **Overshoot situation:**
    * [cite_start]**Overshoot voltage:** 985.242mV jump to 1.2271V [cite: 49]
    * [cite_start]**Settling time:** 618.356ns - 300ns = 318.356ns [cite: 50]
* **Undershoot situation:**
    * [cite_start]**Undershoot voltage:** 1V drop to 759.675mV [cite: 52]
    * [cite_start]**Settling time:** 1.627us - 1.11us = 0.517us [cite: 53]

**No load:**
* **Overshoot situation:**
    * [cite_start]**Overshoot voltage:** 999.891mV jump to 1.231V [cite: 56]
    * [cite_start]**Settling time:** 550.1313ns - 300ns = 250.1313ns [cite: 57]
* **Undershoot situation:**
    * [cite_start]**Undershoot voltage:** 1V drop to 772.22mV [cite: 59]
    * [cite_start]**Settling time:** 1.372us - 1.11us = 0.262us [cite: 60]
