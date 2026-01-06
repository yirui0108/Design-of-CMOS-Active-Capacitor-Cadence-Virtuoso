# LDO Performance & Benchmarks

## 1. Overall LDO performance
* **Supply voltage:** 1.2V 
* **Output voltage:** 1V 

## 2. Total DC quiescent current consumption

### a. Full load (load current = 38mA)
* **i. Ideal 100p cap:** 38.5mA 
* **ii. Active cap:** 38.55mA 

### b. No load
* **i. Ideal 100p cap:** 500.2uA 
* **ii. Active cap:** 549.8uA 

## 3. Stability analysis with probe

### a. Test schematic
![Test Schematic for Stability Analysis](path/to/schematic_image_1.png) 

### b. Results
The top section is ideal 100pF cap, bottom section is active cap, ref to 100u iload, the active cap has some Resr effect to cancel the pole. 

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
![Stability Response Bode Plot](path/to/bode_plot_image.png) 

## 4. PSR test

### a. Test schematic
![PSR Test Schematic](path/to/psr_schematic.png) 

### b. Results
Red/orange is ideal, and blue/green is the one with active cap. 

![AC Response Graph](path/to/ac_response_graph.png)

### c. Ideal cap PSR stats

**i. Iload = 38mA**
* **Low freq:** -51.53dB until 1kHz 
* **100kHz:** -43.46dB 
* **1Mhz:** -23.74dB 
* **Peak:** -18.46dB at 2.52MHz 

**ii. Iload = 0A**
* **Low freq:** -68.32dB until 500Hz 
* **100kHz:** -46.22dB 
* **1Mhz:** -25.46dB 
* **Peak:** -18.71dB at 2.52Mhz 

## 5. Transient analysis

### a. Test schematic
![Transient Analysis Test Schematic](path/to/transient_schematic.png) 

### b. Results
Red: ideal 100p cap; green: active cap, the waveforms coincide with one another similarly. 

![Transient Response Graph](path/to/transient_response_graph.png) 

### c. The performance of active cap

**i. Undershoot situation:**
* **Undershoot voltage:** 735.544mV 
* **Settling time:** 508.989ns - 200ns 

**ii. Overshoot situation:**
* **Overshoot voltage:** 1.193V 
* **Settling time:** 1.298us - 1.005us 

## 6. Line transient simulation
The supply voltage will be applied as a pulse with certain range, for example +/- 10%. 

### a. Line Transient Pulse settings
![Pulse Settings](path/to/pulse_settings_image.png) 

### b. Test schematic
![Line Transient Test Schematic](path/to/line_transient_schematic.png) 

### c. Results
The waveforms looks very similar when being put together, for both the ideal 100pF cap and the active cap. 

![Line Transient Response Combined](path/to/combined_transient_response.png) 

### d. Ideal 100p cap waveform
![Ideal 100p Cap Waveform](path/to/ideal_cap_waveform.png) 

### e. Active cap waveform
![Active Cap Waveform](path/to/active_cap_waveform.png) 

### f. Measurement relevant to the waveform of the LDO with the active cap setup

**Full load:**
* **Overshoot situation:**
    * **Overshoot voltage:** 985.242mV jump to 1.2271V 
    * **Settling time:** 618.356ns - 300ns = 318.356ns 
* **Undershoot situation:**
    * **Undershoot voltage:** 1V drop to 759.675mV 
    * **Settling time:** 1.627us - 1.11us = 0.517us 

**No load:**
* **Overshoot situation:**
    * **Overshoot voltage:** 999.891mV jump to 1.231V 
    * **Settling time:** 550.1313ns - 300ns = 250.1313ns 
* **Undershoot situation:**
    * **Undershoot voltage:** 1V drop to 772.22mV 
    * **Settling time:** 1.372us - 1.11us = 0.262us 
