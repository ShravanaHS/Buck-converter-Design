# Buck-converter-Design
A compact LM2596-based DC-DC buck converter with a 2-layer PCB. Delivers an adjustable 1.23V-9.43V output at 1A from a 7-35V input. Features robust input protection. This repository contains the complete design files, including schematics, PCB layout, Gerbers, and a Bill of Materials (BOM).

# Regulated DC-DC Buck Converter using LM2596

![3D model of the Buck Converter PCB](https://i.imgur.com/your-image-url.png) ## 1. Introduction

This repository contains the complete design files for a compact and reliable DC-DC step-down (buck) converter based on the **LM2596** switching regulator. The project is designed to be a versatile power supply solution, capable of converting a high DC input voltage into a lower, stable, and precisely adjustable DC output. It is an ideal project for demonstrating skills in power electronics, schematic capture, and PCB layout.

All design files, including schematics, PCB layout (KiCad), Gerber files for manufacturing, and a Bill of Materials (BOM), are included in this repository.

---

## 2. Problem Statement

Many electronic projects and devices require specific DC voltages (e.g., 3.3V, 5V) that are lower than the voltage provided by standard power sources like batteries or wall adapters (e.g., 12V, 24V). A linear regulator could be used, but it would be highly inefficient, dissipating the excess voltage as heat.

The objective of this project is to design and build an efficient, regulated power supply that can:
1.  Accept a wide range of DC input voltages.
2.  Provide a stable, adjustable DC output capable of delivering a 1A load.
3.  Incorporate protection features to ensure reliability and safety.
4.  Be compact and built with readily available components.

---

## 3. Specifications Based Block Diagram (White-Box Analysis)

This diagram breaks down the circuit into its core functional blocks, showing the flow of power and control with key design specifications.

```mermaid
graph TD
    subgraph Input Stage
        A[Input Connector <br><b>7V-35V DC</b>] --> B{Fuse <br><b>1.5A Slow-Blow</b>};
        B --> C{Reverse Polarity Diode <br><b>1N4004</b>};
        C --> D[Input Capacitor <br><b>100µF, 50V</b>];
    end

    subgraph Converter Core
        E[Regulator IC <br><b>LM2596S-ADJ</b>];
        F[Catch Diode <br><b>1N5822 Schottky</b>];
        G[Power Inductor <br><b>100µH, >2A Sat</b>];
    end

    subgraph Feedback Network
        H["Potentiometer (R2)<br>10kΩ"]
        I[Fixed Resistor (R1) <br><b>1.5kΩ</b>];
    end

    subgraph Output Stage
        J[Output Capacitor <br><b>220µF, 25V</b>];
        K[HF Filter Cap <br><b>0.1µF Ceramic</b>];
        L[Indicator LED];
        M[Output Connector <br><b>1.23V-9.43V, 1A</b>];
    end

    D --> E;
    E -- Pin 2 --> G;
    E -- Pin 3 --> GND;
    E -- Pin 5 --> GND;
    G -- V_out Node --> J;
    J --> K;
    K --> L;
    K --> M;
    K -- Samples V_out --> H;
    H --> I;
    H -- Junction --> E_FB((Pin 4 FB));
    I --> GND;
    F -- Cathode --> GND;
    F -- Anode --> G;
```

---

## 4. Component Selection and Calculations

Component selection was driven by the project specifications to ensure reliable and efficient performance.

### **Regulator IC (LM2596S-ADJ)**
* **Reasoning:** Chosen for its robustness, high current capability (3A), integrated protection features, and wide input voltage range (up to 40V). The adjustable version provides design flexibility.

### **Power Inductor (L1)**
* **Calculation:** The inductance value is chosen to limit the inductor's ripple current ($\Delta I_L$) to ~30% of the maximum load current (1A).
    * $\Delta I_L = 1A \times 30\% = 0.3A$
    * $L = \frac{V_{out} \times (V_{in(max)} - V_{out})}{V_{in(max)} \times \Delta I_L \times f_{sw}} = \frac{5V \times (35V - 5V)}{35V \times 0.3A \times 150000Hz} \approx 95 \mu H$
* **Selection:** A standard **100 µH** shielded power inductor was chosen. The saturation current rating (**>2A**) is critical and provides a safe margin over the peak current of 1.15A.

### **Feedback Network (R1, R2)**
* **Calculation:** The output voltage is set by the voltage divider R1/R2 and the IC's internal reference voltage ($V_{ref} = 1.23V$). The formula is $V_{out} = V_{ref} \times (1 + R2/R1)$.
    * **R1 Selection:** A fixed resistor of **1.5 kΩ** was chosen.
    * **R2 Selection:** A **10 kΩ** multiturn potentiometer was chosen for R2 to provide an adjustable output.
    * **Min Voltage** ($R2=0\Omega$): $V_{out} = 1.23V \times (1 + 0/1.5k) = \textbf{1.23V}$
    * **Max Voltage** ($R2=10k\Omega$): $V_{out} = 1.23V \times (1 + 10k/1.5k) \approx \textbf{9.43V}$

---

## 5. Block Diagram with Actual Components (Black-Box Analysis)

This diagram shows the physical implementation of the block diagram with images of the selected components.



---

## 6. Components Used List (BOM)

| Designator | Quantity | Component                       | Description                                                     |
| :--------- | :------- | :------------------------------ | :-------------------------------------------------------------- |
| **U1** | 1        | LM2596S-ADJ                     | 3A Step-Down Voltage Regulator, TO-263 Package                  |
| **D1** | 1        | 1N5822                          | 3A, 40V Schottky Diode (Catch Diode)                            |
| **D2** | 1        | 1N4004                          | 1A Rectifier Diode (Reverse Polarity Protection)                |
| **L1** | 1        | Bourns SRP1245A-101M            | 100 µH Power Inductor, Saturation Current > 2A                  |
| **C1** | 1        | 100 µF, 50V Electrolytic Cap    | Input Filter Capacitor                                          |
| **C2** | 1        | 220 µF, 25V Electrolytic Cap    | Output Filter Capacitor (Low ESR recommended)                   |
| **C3** | 1        | 0.1 µF (100 nF), 50V Ceramic Cap| Output High-Frequency Filter Capacitor                       |
| **R1** | 1        | 1.5 kΩ Resistor (0805)          | Feedback Network Resistor (1/4W, 5% tolerance)                  |
| **R2** | 1        | Bourns 3296W-1-103RLF           | 10 kΩ Trimmer Potentiometer                                     |
| **R3** | 1        | 330 Ω Resistor (0805)           | LED Current Limiting Resistor (1/4W)                            |
| **LED1** | 1        | Green LED (0805)                | Power Indicator LED                                             |
| **J1, J2** | 2        | 2-Pin Screw Terminal            | Input and Output Connectors                                     |
| **F1** | 1        | 1.5A Slow-Blow Fuse (1206)      | Input Overcurrent Protection                                    |

---

## 7. Schematic

Below is the complete circuit diagram for the buck converter.

![Schematic of the LM2596 Buck Converter](https://i.imgur.com/your-schematic-image.png) ---

## 8. PCB Layout

The circuit is implemented on a compact two-layer PCB. Power traces are widened to handle the current, and a ground plane is used on the bottom layer for thermal management and signal integrity.

### Top Layer
![PCB Top Layer](https://i.imgur.com/your-pcb-top-image.png) ### Bottom Layer
![PCB Bottom Layer](https://i.imgur.com/your-pcb-bottom-image.png) ---

## 9. 3D Diagram

A 3D rendering of the final assembled PCB.

![3D model of the Buck Converter PCB](https://i.imgur.com/your-3d-model-image.png) ---

## 10. Schematic Capture

The schematic was captured using KiCad, with logical separation of the functional blocks for clarity and ease of debugging.

![Screenshot of Schematic Capture in KiCad](https://i.imgur.com/your-kicad-screenshot.png) ````
