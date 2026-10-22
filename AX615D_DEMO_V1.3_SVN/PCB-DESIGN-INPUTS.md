# AX615 Stereo PCB Design Inputs

Updated: 2026-10-23
Target: AX615DV200, two bare SC438HAI sensors, KiCad design on the home Windows machine.

This is a design-input checklist, not an electrical approval or a released schematic. The Linux host has no KiCad and did not edit or validate the product board.

## Frozen Decisions

- SoC: AX615DV200, integrated 128 MiB DDR3L, DDR rail 1.35 V.
- Sensors: two SC438HAI-CSMNN00 bare CSP devices; optical baseline target 60 mm.
- Sensor sync: same-edge PWM triggering has already been verified in the AX615 stereo topic. Windows rev0.5 connects CAM0 U1.41/PWM9 and CAM1 U1.39/PWM8 to FSYNC, with PWM7 reserved for VDDCORE DVS. G5 must be 1.8 V; pins 39/41 cannot also drive Ethernet LEDs. Product firmware and waveform validation remain pending. The earlier requested no-runtime-adjustment policy remains unresolved against the hardware guide/checklist requirements; see the power-tree gate below.
- Storage: W25N02KVZEIR, 3.3 V, 2 Gbit. The confirmed build command is `make p=AX615_nand all install axp`; the resulting build dependencies support this NAND.
- External interfaces: one USB-C USB 2.0 Device/UFP at 5 V / 1.5 A, UART, and 10/100 Ethernet.
- Product power design target supplied by the user: dual sensors plus NPU within 5 W. This is a design target, not a measured full-board result.

## Already Packaged

- AX615D DEMO V1.3 OrCAD schematic (`.DSN` and PDF), Allegro board (`.BRD`), and BOM.
- AX615D V1.2 hardware guide, DEMO guide, hardware checklist, AX615DV200 specification, and AX615D pinout.
- AX615D VI input scenario matrix V0.6, covering DPHY lane and BT/DVP reuse cases; use it with the pinout and reference schematic when deciding signal muxing.
- AX615 AVL, ROM NAND image README, six-page SC438HAI reference schematic and two-page assembly placement drawing. `SENSOR_SC438HAI_V1_0.pdf` is the schematic, not the datasheet.
- Added the actual 39-page `SC438HAI_数据手册_V1.0.pdf` from Wiki and SVN `动态调压注意事项.pdf`; exact sources and revisions are in `README.md`. RAG indexes SC438HAI V1.3, but that original PDF is still unverified.
- These are reference inputs. Do not treat the DEMO schematic/board as the new product design or the sensor assembly PDF as an approved PCB land-pattern.

## Required Before Schematic Freeze

1. **SC438HAI circuit and exact package**: the reference circuit is already present (schematic pages 3–4), and the actual datasheet pages 8–9 supply power-up/down timing. Apply these to the product rail/reset/clock design. The V0.5 revision history (page 38) explicitly changes E1/F1 to NC, supporting the six-NC pin table despite the stale `41(4NC)` package summary. Obtain and review the indexed V1.3 original or supplier-current revision. Manufacturer-approved CSMNN00 PCB land-pattern and stencil/paste guidance remain missing and block footprint/layout approval.
2. **SoC and sensor signal map**: Windows rev0.5 records the dual CSI/I2C, reset/PWDNB and PWM9/PWM8-to-FSYNC wiring as connected and checked against its native netlist. Complete the firmware/bus mapping and board-level review using `AX615D_PIN_OUT.xlsx`, `AX615D_VI输入接口场景_V0.6.xlsx`, and the reference schematic. Both sensors keep SID grounded at `0x30` on separate buses; reference page 3 also documents SID high as `0x32`. EFSYNC and G7 pins 92/93 are NC in rev0.5. Reserve PWM7 for DVS and resolve the policy conflict below before schematic freeze.
3. **NAND boot wiring**: match BOND/boot strap, SFC voltage domain, and partition/image configuration to the confirmed `AX615_nand` build. The exact NAND model/build path is confirmed; final product schematic still needs its own strap/netlist review.
4. **Power tree**: select exact regulator and protection part numbers; document each AX615D and sensor rail, sequencing, current/thermal margin, and input transient. V1.2 guide section 1.2.2 requires DVS support; V1.1 CheckList explicitly requires PWM adjustment and the reference feedback/filter values. The requested no-runtime-adjustment policy needs an applicable hardware/SDK exception before it can be frozen; do not infer that fixed 0.9 V or omitted DVS is approved. Verify the 5 V / 1.5 A USB-C budget against the 5 W system target and conversion losses. Define CC current detection/source requirements, ESD, over-current/over-voltage protection, and connector ratings; a UFP pull-down alone does not establish available 1.5 A current.
5. **External interfaces**: freeze exact USB-C, UART, Ethernet connector and magnetics/PHY implementation, logic levels, ESD parts, and cable/connector pinouts. The DEMO reference is not the product interface specification.

## Required Before PCB Layout Freeze

1. **Optics and mechanics**: exact lens and M12 holder part numbers/drawings; holder height, hole pitch, back-focus, IR-cut arrangement, sensor optical-center offset, enclosure clearances, final board outline, mounting holes, and connector placement. Keep the 60 mm target defined between optical centers, not package centers.
2. **Fabrication stack-up**: selected board house stack-up, finished thickness, copper weights, impedance table/tolerances, minimum line/space, via/drill rules, solder mask, and fabrication/assembly capability for the CSP. Recalculate MIPI and Ethernet routing constraints for that stack-up; do not copy DEMO widths blindly.
3. **Validation rules**: approve symbol-to-footprint pin mapping, net classes, keepouts, return paths, differential-pair constraints, length matching, test points, assembly orientation, and DFM constraints. Classify imported Allegro DRC reports before using them as constraints.

## Open Evidence Gates

- Verify the complete Linux + dual VIN/IVPS buffers + model/AXEngine working memory + application peak fits V200's 128 MiB. The existing model-only CMM result does not validate the full system; public specs cannot replace this budget or target configuration measurement.
- The newer Windows design record reports rev0.5 with six sheets, 90 components, and 86 candidate footprints; sensor decoupling and sync wiring have been added. Power/interface implementation, regulator stability with effective MLCC capacitance, mechanics, sensor footprints, and routing remain unfinished. These are recorded Windows results, not a new Linux/KiCad validation.
- The reference DEMO is AX615D hardware, but it is not the new AX615DV200 stereo product; validate the chosen V200 pinout and power tree against the exact package and target assembly.
- AX615Q DEMO `10.126.16.141` was confirmed to contain W25N02KVZEIR; its MTD capacity is 256 MiB. This validates that local board's component identity only, not the new board's soldering or cold-boot test.
- No product schematic/PCB, full-board ERC/DRC pass, optical calibration, or fabrication approval is included in this reference packet.

## Source Package

See `README.md` for SVN paths, revision notes, and the packaged file inventory. The packet is tracked in the private `liyue75/pcb` repository.
