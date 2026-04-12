# Hardware Implementation: Analog Current Source

To interface with the vestibular nerve safely and effectively, we cannot use standard PWM (Pulse Width Modulation) as the frequency is too sharp for biological systems. We require a true, proportional Analog Voltage.

## Component Stack
1. **Microcontroller:** ESP32 (Utilizing the internal 8-bit DAC on Pin 25).
2. **Amplifier:** LM358 Non-Inverting Operational Amplifier.
3. **Safety/Feedback:** 500Ω Resistor Chain.

## Wiring & Logic
The circuit is designed around a **1.65V Virtual Ground**. In a standard circuit, current flows from positive to ground. By biasing our neutral state to 1.65V (exactly half of our 3.3V logic level), we create a "push-pull" dynamic on a single wire.

* **ESP32 Pin 25 (DAC) -> Op-Amp Non-Inverting Input (+)**
* **Op-Amp Output -> 500Ω Resistor -> Electrode A**
* **Electrode B -> 1.65V Virtual Ground**

## The "Seesaw" Effect (Modulating Firing Rates)
When the user's joystick is centered, the DAC outputs a value of 128 (~1.65V). The net current across the electrodes is 0mA.
* **Steering Left (DAC < 128):** Voltage drops below 1.65V. Current flows in reverse (Cathodal stimulation), depolarizing the nerve and *increasing* the tonic firing rate.
* **Steering Right (DAC > 128):** Voltage rises above 1.65V. Current flows forward (Anodal stimulation), hyperpolarizing the nerve and *decreasing* the firing rate.

Current flow ($I$) is continuously monitored and calculated via: `I = (V_out - 1.65) / 500`
