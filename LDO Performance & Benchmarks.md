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
<img width="940" height="793" alt="image" src="https://github.com/user-attachments/assets/a8edd1d9-e66f-4bc1-a0c0-23726406316a" />


### b. Results
The top section is ideal 100pF cap, bottom section is active cap, ref to 100u iload, the active cap has some Resr effect to cancel the pole. 

<img width="788" height="349" alt="image" src="https://github.com/user-attachments/assets/5dc6d835-7343-4e7e-85c6-a75f941a5eee" />


### c. Bode Plot
<img width="940" height="445" alt="image" src="https://github.com/user-attachments/assets/4b44edc9-bc76-4b1a-a192-241c2c28a6b7" />


## 4. PSR test

### a. Test schematic
<img width="940" height="837" alt="image" src="https://github.com/user-attachments/assets/5d6f36c4-99e3-49fe-87f2-30ba7daec8a2" />



### b. Results
Red/orange is ideal, and blue/green is the one with active cap. 

<img width="940" height="446" alt="image" src="https://github.com/user-attachments/assets/d6bf195e-8cac-454a-b06f-70b8f492684e" />


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
<img width="940" height="796" alt="image" src="https://github.com/user-attachments/assets/837dae29-564a-4a23-8ac7-3d2e8c62db42" />


### b. Results
Red: ideal 100p cap; green: active cap, the waveforms coincide with one another similarly. 

<img width="940" height="447" alt="image" src="https://github.com/user-attachments/assets/1ba7c694-b45f-4808-bc1d-81cc6df4e053" />


### c. The performance of active cap

**i. Undershoot situation:**
* **Undershoot voltage:** 735.544mV 
* **Settling time:** 508.989ns - 200ns = 308.989ns

**ii. Overshoot situation:**
* **Overshoot voltage:** 1.193V 
* **Settling time:** 1.298us - 1.005us = 0.293us

## 6. Line transient simulation
The supply voltage will be applied as a pulse with certain range, for example +/- 10%. 

### a. Line Transient Pulse settings
<img width="451" height="476" alt="image" src="https://github.com/user-attachments/assets/df6e62aa-4ac0-445f-b5ec-e686c345f5db" />


### b. Test schematic
<img width="940" height="819" alt="image" src="https://github.com/user-attachments/assets/0d08dd2c-a686-4650-893b-061680120ecb" />


### c. Results
The waveforms looks very similar when being put together, for both the ideal 100pF cap and the active cap. 

<img width="940" height="443" alt="image" src="https://github.com/user-attachments/assets/0c3a1bd1-242d-4d23-98f6-fecc0cb45170" />

### d. Ideal 100p cap waveform
<img width="940" height="446" alt="image" src="https://github.com/user-attachments/assets/f0a80541-42d8-4e8a-8ca4-67b859765759" />


### e. Active cap waveform
<img width="940" height="446" alt="image" src="https://github.com/user-attachments/assets/1afaee24-4c65-411a-9256-e7f4d62a090e" />


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
