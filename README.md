NERC'26 Autonomous 4WD Line-Following Robot

This repository contains the firmware, control algorithms, and hardware architecture for a 4-Wheel-Drive (4WD) autonomous line-following robot. Designed for the 21st National Engineering Robotics Contest (NERC) 2026, this project tackles the high-speed inertia and noise-rejection challenges of navigating a complex, 24-node competition track.

🚀 System Overview

Developed for the NERC indigenous category by students at the Ghulam Ishaq Khan Institute of Engineering Sciences and Technology (GIKI), this system is powered by a PIC18F47K42 microcontroller running at 64MHz. By combining a highly-tuned, non-linear Proportional-Derivative (PD) control loop with multi-stage software confidence filters, the robot successfully ignores false sensor triggers (track reflections/bounce) while executing dynamic maneuvers like active braking, color-reactive sweeping, and "reverse slingshot" alignments.

⚙️ Core Architecture

The system logic is divided into three primary functional sub-systems to maintain stability at high speeds:

Non-Linear PD Control (Anti-Windup): The robot tracks the line using a weighted error scale ranging from -10 to +10. The control loop utilizes optimized Proportional ($Kp = 30.0$) and Derivative ($Kd = 200.0$) terms. The Integral term ($Ki$) is intentionally zeroed out to prevent "integral windup," ensuring the heavy 4WD chassis does not violently over-correct after extended stops or sharp turns.

3-Stage Confidence Filtering (Debouncing): To mitigate false positives from the chassis bouncing over uneven tile grout or track glare, the software implements a rigid debouncing filter. If the IR array loses the line, the system waits 1ms and re-polls the array three consecutive times. A pivot recovery maneuver is only triggered if the line loss is confirmed across all readings, filtering out transient physical bounces. Similar filters are applied to the ultrasonic sensor to ignore corrupt acoustic echoes.

24-Node Junction Gatekeeper: Track navigation is managed by a strict latch mechanism that tracks the robot's progression across 24 distinct track nodes. The gatekeeper overrides the PD loop to execute hardcoded maneuvers, including perfect 180-degree U-turns, inertia-catch boosts, a custom "Reverse Slingshot" alignment at Node 20, and an automated parking sequence at Node 24.

🧰 Hardware & Component Stack

The hardware architecture focuses on robust power delivery and instant logic-level routing:

Microcontroller: PIC18F47K42 (64MHz Internal Oscillator)

Power Management: 7.4V 3000mAh battery regulated by an LM2596 buck converter to isolate the logic sensors from motor stall voltage spikes.

Motor Control: MX1508 dual DC motor driver actuating a heavy 1.5kg 4WD skid-steer chassis.

Sensors: 5-Point IR Array (Line Detection), HC-SR04 Ultrasonic (Obstacle Detection), and GY-31 TCS3200 (Color Recognition).

⏱️ Performance Dynamics: Dynamic PPS Routing & Color Actuation

To achieve instant directional shifts without blocking the CPU or relying on standard digital pins, the firmware utilizes the microcontroller's Peripheral Pin Select (PPS) module. Hardware PWM signals (PWM6 and PWM8) are dynamically unlocked, re-routed to specific PORTD H-bridge pins, and locked on the fly, allowing seamless motor reversing with embedded, un-interrupted speed control.

Additionally, the robot features interactive environmental navigation via the GY-31 color sensor. Operating at a 20% frequency scaling, the software isolates RGB intensity and applies a strict ambient noise threshold (900). Upon detecting a valid red or blue marker, the robot actuates an onboard servo motor to execute specific track maneuvers, returning to a neutral 90-degree position when ambient noise is detected.

📁 Repository Structure

src/ - Contains the primary C firmware and header files for the PIC18F47K42.

docs/ - Project reports, circuit block diagrams, and flowcharts.
