# AX615 Stereo PCB Design Inputs

Updated: 2026-10-22
Target: AX615DV200, two bare SC438HAI sensors, KiCad design on the home Windows machine.

This is a design-input checklist, not an electrical approval or a released schematic. The Linux host has no KiCad and did not edit or validate the product board.

## Frozen Decisions

- SoC: AX615DV200, integrated 128 MiB DDR3L, DDR rail 1.35 V.
- Sensors: two SC438HAI-CSMNN00 bare CSP devices; optical baseline target 60 mm.
- Sensor sync: same-edge PWM triggering has already been verified in the AX615 stereo topic. On AX615D, PWM7 is for VDDCORE DVS and sensor1 is assigned PWM8. Dynamic voltage adjustment is not enabled at runtime.
- Storage: W25N02KVZEIR, 3.3 V, 2 Gbit. The confirmed build command is `make p=AX615_nand all install axp`; the resulting build dependencies support this NAND.
- External interfaces: one USB-C USB 2.0 Device/UFP at 5 V / 1.5 A, UART, and 10/100 Ethernet.
- Product power design target supplied by the user: dual sensors plus NPU within 5 W. This is a design target, not a measured full-board result.

## Already Packaged

- AX615D DEMO V1.3 OrCAD schematic (`.DSN` and PDF), Allegro board (`.BRD`), and BOM.
- AX615D V1.2 hardware guide, DEMO guide, hardware checklist, AX615DV200 specification, and AX615D pinout.
- AX615D VI input scenario matrix V0.6, covering DPHY lane and BT/DVP reuse cases; use it with the pinout and reference schematic when deciding signal muxing.
- AX615 AVL, ROM NAND image README, SC438HAI V1.0 datasheet and assembly PDF.
- These are reference inputs. Do not treat the DEMO schematic/board as the new product design or the sensor assembly PDF as an approved PCB land-pattern.

## Required Before Schematic Freeze

1. **SC438HAI exact package and assembly data**: obtain manufacturer-approved PCB land-pattern and stencil/paste guidance for CSMNN00, plus the recommended external circuit, power-up/power-down timing, decoupling, and MIPI/clock requirements. Resolve the datasheet discrepancy between six NC entries in the pin table and the package summary's four NC count. Confirm whether V1.0 is current or obtain the current revision.
2. **SoC and sensor signal map**: freeze the exact AX615DV200 package/pin mapping, RX0/RX1 and two 2-lane assignments, two I2C buses, MCLK, reset/PWDNB, and PWM8 sensor1 trigger. Cross-check `AX615D_PIN_OUT.xlsx`, `AX615D_VI输入接口场景_V0.6.xlsx`, and the V1.3 reference schematic. Keep the PWM7 DVS requirement separate from the choice not to enable dynamic adjustment at runtime; decide whether its analog circuit remains populated.
3. **NAND boot wiring**: match BOND/boot strap, SFC voltage domain, and partition/image configuration to the confirmed `AX615_nand` build. The exact NAND model/build path is confirmed; final product schematic still needs its own strap/netlist review.
4. **Power tree**: select exact regulator and protection part numbers; document each AX615D and sensor rail, sequencing, current/thermal margin, and input transient. Verify the 5 V / 1.5 A USB-C budget against the 5 W system target and conversion losses. Define CC configuration, ESD, over-current/over-voltage protection, and connector ratings.
5. **External interfaces**: freeze exact USB-C, UART, Ethernet connector and magnetics/PHY implementation, logic levels, ESD parts, and cable/connector pinouts. The DEMO reference is not the product interface specification.

## Required Before PCB Layout Freeze

1. **Optics and mechanics**: exact lens and M12 holder part numbers/drawings; holder height, hole pitch, back-focus, IR-cut arrangement, sensor optical-center offset, enclosure clearances, final board outline, mounting holes, and connector placement. Keep the 60 mm target defined between optical centers, not package centers.
2. **Fabrication stack-up**: selected board house stack-up, finished thickness, copper weights, impedance table/tolerances, minimum line/space, via/drill rules, solder mask, and fabrication/assembly capability for the CSP. Recalculate MIPI and Ethernet routing constraints for that stack-up; do not copy DEMO widths blindly.
3. **Validation rules**: approve symbol-to-footprint pin mapping, net classes, keepouts, return paths, differential-pair constraints, length matching, test points, assembly orientation, and DFM constraints. Classify imported Allegro DRC reports before using them as constraints.

## Open Evidence Gates

- The reference DEMO is AX615D hardware, but it is not the new AX615DV200 stereo product; validate the chosen V200 pinout and power tree against the exact package and target assembly.
- AX615Q DEMO `10.126.16.141` was confirmed to contain W25N02KVZEIR; its MTD capacity is 256 MiB. This validates that local board's component identity only, not the new board's soldering or cold-boot test.
- No product schematic/PCB, full-board ERC/DRC pass, optical calibration, or fabrication approval is included in this reference packet.

## Source Package

See `README.md` for SVN paths, revision notes, and the packaged file inventory. The packet is tracked in the private `liyue75/pcb` repository.
