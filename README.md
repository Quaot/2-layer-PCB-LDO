# LM1117 5 V to 3.3 V LDO
Date: 2026-09-20
Revision: v0.1

# Regulator choice

The regulator is the LM1117MP-ADJ/NOPB (Texas Instruments, SOT-223). This is the exact part specified in the WATonomous onboarding document, so it was chosen directly. It is the adjustable (ADJ) version, so the output voltage is set by the external R1/R2 divider.

# LM1117 Absolute Maximum input voltage

Absolute Maximum input voltage = 20 V
20 V - 5 V = 15 V. (difference between absolute max and the operating voltage)

# LM1117 Dropout voltage at 500 mA

Currently: 5.0 V - 3.3 V = 1.7 V (difference between input and output voltage)
Dropout voltage at 500 mA = 1.25 V
1.7 V - 1.25 V = 0.45 V.

3.3 V + 1.25 V = 4.55 V
Thus, if input falls below 4.55 V, then it will stop regulating.


# Decision for Resistor 1 to set the output voltage of regulator

The minimum current flowing out of Vout for it to continue regulating is 5 mA, according to pg. 6 of datasheet. 5 mA was used as it is the minimum current that must flow out of Vout in order for it to continue regulating at a range of temp 0–125 °C.

The other value given for minimum current was 1.7 mA at a range of 25 °C (typical temperature)
Naturally, a regulator will create heat, so a minimum current designed around room temp will not suffice. Also 1.7 mA is a TYP value, where 5 mA is the max and is guaranteed for every chip.

Thus, from V = IR, (where V is the V-REF) across R1, the maximum resistance of R1 = 250 ohms.
Also data sheet suggests a range of 100 - 200 ohms for resistor 1 ("the value is normally between 100 and 200 ohm" [pg 15]).

**See LM1117 Divider Calculator.xlsx** for more details as to why specific values for R1 and R2 were chosen. (Specifically 162 ohm & 267 ohm for R1 and R2 respectively).

# Decision for the resistor divider passives

2 resistors, (162 and 267 ohm respectively) were chosen.
The RC0805FR-07162RL and RC0805FR-07267RL were chosen simply for the resistor value and tolerance (1%).

# Accounting for tolerances of the regulator + resistors.

According to the datasheet (pg. 5), V-REF can be between 1.225 & 1.27 V, additionally E96 series resistors have a 1% tolerance. Using the formula Vout = 1.27 x (1 + (267x1.01)/(162x0.99)) = 3.41 V for the high case and Vout = 1.225 x (1 + (267x0.99)/(162x1.01)) = 3.20 V for the low case, this yields a range of 3.2 V to 3.41 V. 

For the 'high' end case, the error term of the current leakage from ADJ pin adds 3.41 V + 120 x 10^-6 A x 267 x 1.01 ohms then gives 3.44 V as the highest value for voltage.

According to JEDEC interface standard for Nominal 3 V/3.3 V Supply Digital Integrated Circuits the allowed 'narrow range' is 3.15-3.45 for a 3.3 V output, meaning the range of 3.2 V to 3.44 V fits in and is within the JEDEC narrow range. Thus the tolerances of the regulator + resistors should not impact the normal functioning of this device

JEDEC JESD8C (June 2006), Table 1, p. 2: 3.3 V supply, normal range 3.0–3.6 V, narrow range 3.15–3.45 V. Absolute max V_DD 4.6 V (Section 2.1, p. 1).

*Note to self: make sure ADJ pin does not float. If it does, Vout rises to about 3.9 V, above the 3.6 V JEDEC operating limit, so correct operation is not guaranteed.

# Decision for Tantalum Capacitor TACR156K010XTA (Output Capacitor)

~ minimum capacitance requirement

Since a minimum capacitance of 10 uF is required for the LM1117 regulator (according to LM1117 datasheet), and there is a typical tolerance of 10-20% for 10 uF tantalum capacitor, a capacitor with a capacitance of 15 uF would meet that requirement at all times regardless of tolerances.

The TACR156K010XTA has a tolerance of 10%. With 15 uF, the minimum capacitance would be 13.5 uF.

~ ESR (equivalent series resistance)

Also, to prevent oscillation ESR must be between 0.3 - 22 ohm. Typical ceramic capacitors often have an ESR much lower than 0.3 ohm, thus a tantalum capacitor should be used.

TACR156K010XTA (Chosen tantalum capacitor) has 5 Ω max at 100 kHz , which falls between 0.3 - 22 ohm. Though this is a MAXIMUM VALUE, parts of this size typically stay well above 0.3 ohm, in practice. The 0.3 ohm minimum should not be a problem in this case, but it is worth noting for the future. 

~ Voltage rating
A 10 V rating was chosen due to the AVX guide which recommends "6.3 V tantalum for a 3.3 V rail". 10 V is conservative based upon that value. Information was from the pdf named "AVX - Tantalum Technical Summary and Application Guidelines.pdf" submitted with this deliverable.

!!Caveat: the pricing of this specific tantalum capacitor is quite expensive due to the 0805 size constraint outlined in the Watonomous design doc. If a larger size, ie 1206 was used, (TAJA156M010RNJ) the price of this component would be about 10x cheaper. 

# Decision to Include C-ADJ or not. (C meaning Capacitor)

With C-ADJ fitted data sheet requires output capacitor to go from 10 uF to 22 uF tantalum for stability.
 -(to prevent oscillation)

Addition of C-ADJ improves ripple rejection
*Ripple Rejection: AC noise kept from reaching the output.*
This would require the addition of one capacitor and replacing the smaller capacitor with a larger one [22 uF (replacing 10 uF) and C-ADJ]. This may lead to space concerns on the 28 x 17 mm board.

ASSUMING the 5 V rail is already regulated, ripple could be low.
HOWEVER, this is an assumption that the supply that the board connects to is also clean as well. If these ideal conditions do not hold, then it would be better to have the C-ADJ capacitor.

For now, the decision is to not use the C-ADJ capacitor.

[9.2.2.1.2, p. 14; 9.2.2.1.3, p. 15] (LM1117 800-mA, Low-Dropout Linear Regulator Data Sheet)

# Decision for Input Capacitor

As stated in the LM1117 datasheet: (9.2.2.1.1, p. 14)
"An input capacitor is recommended. A 10-µF tantalum on the input is a suitable input capacitor for almost all applications."

The data sheet recommends a input capacitor. It also sets no ESR minimum/limit. Thus a ceramic capacitor is usable since it is:
- cheaper
- non-polarized
- longer lifetime

~ Accounting for C-DC Bias, picking an effective derating factor
According to Murata, (the supplier for the chosen ceramic input capacitor) https://pim.murata.com/en-us/pim/details/?productCategoryId=ceramicCapacitorSMD&partNum=GRM21BR61E226ME44%23

The C-DC bias shows a 53.6% capacitance change rate at 5.0 V, thus the capacitor would deliver ~10.2 uF instead of 22 uF. As stated in # Decision for Input Capacitor, a 10 uF capacitor is suitable for almost all applications

In this case, even with the derating factor due to C-DC bias the minimum capacitance is always >=10 uF. Thus, a 22 uF ceramic capacitor is chosen.

# Decision for Resistor 3 + red LED

Since the red LED is simply an indicator, a lower current (dimmer + less power wasted) is suitable. Typically, the industry standard for indicators is about 1 - 5 mA. Hence, a current of 3 mA was chosen arbitrarily.

Given that the LTST-C170KRKT red LED has a V_F of 2.0 V (according to the **Lite-On LTST-C170KRKT datasheet**), and a current of 3 mA, then, the resistor in series with the LED must be 433.33 ohms. 

Since this value for R3 (the resistor in series with the red LED) is not a standard E96 value, a value of 432 ohms was chosen. This may slightly increase current from 3 mA, though the increase is negligible. 

Again, like R1 and R2, once the resistance value has been calculated, the passives for R3 (RC0805FR-07432RL) were chosen (1% tolerance, 1/8 W max power rating)

~ Calculating Power dissipated in R3

To make sure the power dissipated in R3 is below the resistor power rating of 0.125 W:
since Power is P = I^2 x R, P = 0.003^2 x 432 = 3.9 mW or 0.0039 W. This value is far below the maximum power rating of R3. 

# Decision for Connectors (J1 and J2)

Through hole was used instead of SMD (Surface Mount Device) since connectors are tugged when wires plug in and plug out. Typically through holes handle that stress much better than surface-mount pads

Earlier the Sullins PBC02SAAN was selected, but it had no CAD model available in Altium, so a equivalent Würth 61300211121 was used in its place.

!! The Würth 61300211121 is a bare pin header connector, meaning the input on an actual built board could technically be plugged in backwards. For this project that is 'design only' I don't believe it to be an issue, but this is something I'd probably change for production.

# Thermal Calculation - Amount of Copper Poured

Efficiency = Vout/Vin = (3.3)/(5.0)
= 66%
P = I(Vin-Vout) = (0.5)(1.7) = 0.85 W
Board size = 476 mm^2

TJ = TA + (P x ThetaJA)
According to the LM1117 Datasheet table 9-2 p.18, using 194 mm^2 of top layered copper gives a ThetaJA of 84 °C/W, resulting in a temperature at 116 C (temp is 96 at 25 C ambient temperature). This leaves a 9 C margin between T_J and the recommended maximum given in Section 7.3 p.4 if ambient temp reaches 45 degrees. This leaves at least 59% of the 476 mm^2 board free to place components. More copper can always be poured afterwards if the layout allows, but this is the absolute minimum required for now. In the final layout the poured 3V3 copper exceeds this 194 mm^2 minimum, so the actual ThetaJA is lower and the junction runs cooler than the figures above.

# Decision for SOT-223 footprint

SOT-223 DCY0004A_M (where M means IPC 'Most' Density) since the end product will be hand soldered.

# Task 7.4 Trace widths

From IPC-2221
0.25mm - 0.9A
Power Width = 0.5mm - 1.5A

Since the board current is 0.5A, a power width of 0.5mm allows for 3x capacity of the current. This was deliberately chosen since:
1. a wider trace loses less voltage 
2. a wider trace runs cooler
3. there is more than enough space on the board to accommodate thicker traces

Since the LED carries 3mA of current minimum, the trace width of 0.25mm was chosen arbitrarily. It could be much thinner, but there is more than enough space on the board.

~ return path
GND carries 0.5A. This is dealt with by using a bottom layer ground plane.

# Design Rule Deviations

No design rules were deviated from or loosened. The default rules from Section 7.3 were used throughout, and the DRC passes with zero violations.

# Open Questions & Deviations

Open questions (things I am unsure about):
- Output cap ESR. the datasheet guarantees a max ESR (5 Ω). however online I read that parts of this size stay well above 0.3 Ω in practice. I'm not sure where to actually find the minimum ESR for this specific capacitor. See "Decision for Tantalum Capacitor".

Deviations / trade-offs:
- the output cap C2 (TACR156K010XTA) is expensive because of the 0805 size constraint. a 1206 part (TAJA156M010RNJ) would be ~10x cheaper if the size (0805) rule were different.
