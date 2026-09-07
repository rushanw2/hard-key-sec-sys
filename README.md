# Hack-Resistant Key-Resistor Motor Starter
This repository contains the hardware design and documentation for a hack-resistant electronic key verification system. The circuit activates a motor within 3 seconds when a specific "key" resistor is inserted, while actively preventing brute-force discovery via potentiometer sweeping.

## Features
- Precise Key Detection: Uses a Wheatstone bridge to create a tight window of accepted resistance values around the target key ($1\text{ k}\Omega$).
- Anti-Tuning / Hack Resistance:
  - A narrow resistance acceptance window ($\sim 38\ \Omega$) representing less than $0.04\%$ of a standard $100\text{ k}\Omega$ potentiometer's mechanical range.
  - An RC delay circuit enforces a 3-second dwell time before activation, preventing immediate feedback during brute-force sweeping.
- Discrete Logic Verification: Custom dual-nMOS series AND gate ensures both upper and lower voltage threshold conditions are strictly met.
- Inductive Load Protection: Dedicated BJT/MOSFET motor driver stage with a flyback diode to prevent inductive voltage spikes.

## System Architecture Breakdown
### Wheatstone Bridge
<img width="417" height="279" alt="wheatstone" src="https://github.com/user-attachments/assets/0beafc0b-3ade-4c79-a310-d31f1972d7bb" />

The key resistor ($R_{key}$) forms one leg of a Wheatstone bridge alongside a fixed resistor ($R = 10\text{ k}\Omega$) and two tuning potentiometers used to establish reference voltages $V_b$ and $V_c$.

$$R_{key} = \frac{V_a \cdot R}{V_{bat} - V_a}$$

$$R_{key(min)} = \frac{V_c \cdot R}{V_{bat} - V_c} = \frac{0.796 \cdot 10000}{9 - 0.796} \approx 970.2\ \Omega$$

$$R_{key(max)} = \frac{V_b \cdot R}{V_{bat} - V_b} = \frac{0.824 \cdot 10000}{9 - 0.824} \approx 1008.0\ \Omega$$

(Acceptance Window: $\approx 37.8\ \Omega$)

### Comparators
A LM358 comparator convert the analog output of the bridge ($V_a$) into digital logic levels:
- Lower Bound: Checks if $V_a > V_c$.
- Upper Bound: Checks if $V_a < V_b$.

<img width="587" height="257" alt="basic-key-rest-circ" src="https://github.com/user-attachments/assets/fe4b31c5-c780-4d31-8918-6f727f84e79c" />

This circuit is a basic resistor key circuit, only turning on the LED if a resistor is in the correct range, as calculated above.

### Discrete nMOS AND Gate
Logical condition combining is handled using two nMOS transistors configured in series:
- The output passes high ($V_{out} = \text{HIGH}$) only when both gate inputs ($V_{spd}$ and $V_{bal}$) exceed the threshold voltage ($V_{th}$) simultaneously.

This can be seen on the right side of the hand drawn schematic shown previously.

### Times Reaction & Output Driver
To eliminate real-time feedback during trial-and-error sweeping, the output of the AND gate feeds an RC timing circuit:
- Charging Dwell: The capacitor must charge to the switching threshold of an LM311 comparator to produce a sharp high-to-low/low-to-high transition.
- Driver Protection: The LM311 output drives a power transistor that switches the motor. A flyback diode across the motor terminals clamps back-EMF voltage transients during turn-off.

<img width="476" height="385" alt="full-circ" src="https://github.com/user-attachments/assets/13884735-8d45-4b45-a279-e6fa756050ac" />

Here the final schematic can be seen, implementing all these techniques.

## Technical Specifications
Operating Voltage ($V_{bat}$): $9\text{ V}$

Target Key Resistor: $1\text{ k}\Omega$ ($5\%$ nominal tolerance)

Accepted Resistance Range: $970.2\ \Omega - 1008.0\ \Omega$

Detection Dwell Delay: $\approx 3.0\text{ seconds}$

Active Components: LM358, LM311, N-Channel MOSFETs, Power BJT, Flyback Diode



https://github.com/user-attachments/assets/0aee8e35-d0e2-48c3-b369-693c6a6c8b15



