# BQ76952 Multi-Channel LED Monitoring PCB

## How to Use This Repo
1. Clone or download the project and open `BQ76952_uPi_Hat.kicad_pro` with KiCad 8.x or newer.
2. Use the schematic (`.kicad_sch`) to inspect signal naming, channel scaling, and LM317 regulator settings; propagate changes into the PCB (`.kicad_pcb`) via KiCad's **Update PCB from Schematic** tool.
3. Generate fabrication outputs (Gerbers, drills, pick-and-place, and `BQ76952_uPi_Hat.csv`) when you are ready to order boards or run simulations for your experiments.
4. Document measurement runs and firmware configurations in your own branch to keep bachelor thesis logs aligned with the exact hardware revision.
<img width="2338" height="1353" alt="BQ76952_uPi_Hat_Filled_Top" src="https://github.com/user-attachments/assets/720f18d9-de41-4366-95a7-dce52ca37a84" />

## Purpose & Scope
This board is the hardware platform for a bachelor thesis focused on predicting high-power LED lifetime and scheduling preventative maintenance. It leverages Texas Instruments' `BQ76952` BMS front-end to acquire per-LED telemetry with up to 32-bit resolution, enabling precise degradation tracking for as many as 16 LEDs (down to 5 by shorting unused inputs). The design is intentionally scalable: multiple boards can share a single Raspberry Pi Zero host via SPI chip-select fan-out or operate independently over I2C.

## Hardware Highlights
- `BQ76952` monitors every LED string, providing ADC accuracy, protection, and internal diagnostics normally reserved for battery stacks.
- External LM317-based LDO supplies the Raspberry Pi Zero directly from the board; an additional comparator supervises the LDO output so a 5 V status indicator confirms the Pi rail is within tolerance.
- Status LEDs expose board health at a glance: chip power-on via the internal LDO, network OK, I2C OK, streaming OK, error flags, and 5 V OK from the comparator stage.
- 2.54 mm pitch Würth 20-pin connector handles the LED array interface with ample current capability and clear pin-1 orientation for field swaps.
- Mini DIP switch enables or disables immediate cloud streaming so field techs can start data uploads without reflashing firmware.
- Debug header breaks out key BQ76952 and Pi Zero signals to simplify firmware bring-up and probing.

## File Structure (Essential Only)
- `BQ76952_uPi_Hat.kicad_pro` – master KiCad project tying schematic, PCB, and fabrication settings together.
- `BQ76952_uPi_Hat.kicad_sch` – complete schematic including LED channels, LM317 LDO, comparator, DIP switch, and interface headers.
- `BQ76952_uPi_Hat.kicad_pcb` – board layout optimized for short sense paths and high-current connector routing.
- `BQ76952_uPi_Hat.csv` – bill of materials exported for sourcing and documentation.
- `BQ76952_uPi_Hat-backups/` – automatically generated KiCad backup snapshots for traceability between thesis milestones.

## Operating Modes & Scalability
- **LED Capacity**: Populate between 5 and 16 channels; unused inputs can be shorted to maintain ADC scaling without redesigning the board.
- **Host Interfaces**: Default communication relies on I2C, but SPI pads and separate slave-select lines allow one microprocessor to orchestrate two boards simultaneously.
- **Streaming Control**: Flip the dedicated DIP switch to trigger the firmware's cloud-streaming routine, useful when demonstrating predictive maintenance dashboards.
- **Resolution**: Each LED measurement path maintains up to 32 bits of resolution through firmware-side accumulation, helping extrapolate lumen depreciation trends.

## Bring-Up & Test Flow
1. Power the board from a current-limited supply, verify the BQ76952 internal LDO brings up the power-on indicator, and ensure the comparator asserts 5 V OK once the LM317 output settles.
2. Attach a Raspberry Pi Zero through the onboard connector, confirm I2C communication, then enable SPI if you plan to chain boards via different slave-select pins.
3. Toggle the DIP switch to start cloud streaming and observe the network and streaming indicators before connecting real LED loads.
4. Connect LED strings incrementally, watching each status LED and reading diagnostic registers to confirm per-channel integrity.
5. Use the debug header to probe GPIOs or update firmware while recording data for the thesis lifetime-prediction models.

## Thesis Context
The design forms the experimental backbone for a bachelor thesis investigating predictive maintenance of high-power LED arrays. It demonstrates how a BMS-grade analog front-end, auxiliary regulation, and human-friendly status cues can be repurposed into a scalable monitoring platform suitable for lab studies and eventual field deployments.
