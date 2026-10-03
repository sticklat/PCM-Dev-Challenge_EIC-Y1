# PCM Transmission Control Challenge — Team Contributions

Our team of three second-year students developed and tested transmission-control logic in **MATLAB and Simulink** for the McMaster EcoCAR Propulsion, Controls and Modelling (PCM) development challenge. The project used a supplied gasoline-engine longitudinal vehicle model with a **10-speed automatic transmission**.

Our work covered range selection and park engagement, gear and engine safety, and demand-sensitive gear shifting. Each member took responsibility for implementing control logic and developing and executing tests for their assigned requirements. We worked together on requirements development, controller integration, debugging, and presentation preparation.

## Project context

The controller uses driver requests and vehicle feedback to determine the accepted operating range, gear command, and parking-actuator command. Its design must account for vehicle speed, engine RPM, current gear, and driver throttle while coordinating safety restrictions with ordinary shift decisions.

We extended the supplied controller through defined requirements and simulation tests. The challenge also provided a **0–60 mph target of less than 12 seconds**, giving our shift-calibration work a concrete performance objective.

## Team members

| Team member | Year | Primary responsibility |
| --- | --- | --- |
| Ved Lakkad | 2 | Range selection and park engagement |
| Tejas Chopra | 2 | Gear and engine safety |
| Manil Ferr | 2 | Gear-shift optimization |

## Individual contributions

### Ved Lakkad — Range selection and park engagement

- Developed the range-selection and parking logic for **RP-01, RP-02, RP-03, RP-04, and Park 2.1**.
- Implemented speed-dependent admission checks for Drive and Reverse: Drive requires signed vehicle speed above **−0.5 m/s**, while Reverse requires speed below **0.5 m/s**.
- Developed Neutral handling as an available operating range at any vehicle speed and as the fallback for rejected range requests.
- Developed Park admission logic around the stationary-vehicle condition, **absolute speed below 0.1 m/s**, and parking-actuator behavior with **100 ms engagement and release requirements**.
- Created test cases and ran simulations to evaluate the range and parking logic against the assigned requirements.

### Tejas Chopra — Gear and engine safety

- Developed the gear-safety logic for **GS-01, GS-03, and GS-04**.
- Implemented range-dependent gear-command handling: forward gears **1–10** in Drive, gear **0** in Neutral or Park, and gear **−1** in Reverse.
- Developed engine-overspeed protection logic that requests an upshift when engine speed exceeds **7250 RPM**.
- Developed a stateful **one-second shift timer** for controlling when subsequent gear commands can be accepted.
- Created test cases and ran simulations to evaluate gear-command handling, engine-speed protection, and shift timing against the assigned requirements.

### Manil Ferr — Gear-shift optimization

- Developed the shift-planning logic for **PE-01 and PE-02** using Simulink blocks.
- Implemented **demand-sensitive upshift thresholds** based on current gear, engine RPM, and driver throttle. At the same gear and RPM, lower demand can request an earlier upshift, while higher demand retains the current gear longer.
- Used per-gear low- and high-demand RPM calibration tables with throttle interpolation, with ordinary upshift requests restricted to accepted Drive and gears **1–9**.
- Developed a **shift-reversal hold** that uses confirmed commanded-gear changes to block opposite-direction proposals for **two controller periods**. The logic includes cancellation on leaving Drive and prevents repeated proposals from restarting the hold.
- Created component test harnesses, scheduled input datasets, and logical and temporal assessments for PE-01 and PE-02, and ran simulations to evaluate the shift-planning behavior.
- Worked on interpreting vehicle-speed, engine-RPM, gear-command, and throttle traces to assess shift behavior and guide calibration toward the acceleration objective.

## Shared engineering work

- Translated the challenge specification into controller requirements, including explicit speed thresholds, gear constraints, and timing criteria.
- Integrated the range/park, gear-safety, and shift-planning logic into the controller and reviewed how decisions passed between them.
- Used **Simulink Test** simulation cases and logical and temporal assessments to compare controller outputs with expected behavior.
- Debugged control logic and test setup together, including signal connections, feedback delays, timer behavior, input mappings, and assessment timing.
- Created the team presentation to explain the requirements, controller design, testing approach, and individual contributions.
- Met **at least four times per week**, both in person and online, to coordinate development, review progress, and resolve issues.
