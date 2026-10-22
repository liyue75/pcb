# AX615D / SC438HAI PCB reference packet

Collected on 2026-10-22 for the AX615 stereo PCB work. The SVN documents below are reference designs, not an approved production design or a KiCad-ready project.

## Source

SVN root: `https://svn-ext.aixin-chip.com/svn/aixin_ext_svn`

AX615D DEMO V1.3 source directory: `MC20L/AX615_Release_doc/HW/board/AX615D/AX615D_DEMO_V1.3/`
The schematic PDF was checked at repository revision 43259. Other exported files came from the same AX615D release tree during this collection.

## Files

- `PCB-DESIGN-INPUTS.md`: home KiCad design-input checklist, separating confirmed decisions from missing supplier/mechanical/fab inputs.
- `AX615D_DEMO_V1_3.pdf`, `AX615D_DEMO_V1_3.DSN`: AX615D DEMO V1.3 Capture schematic and PDF.
- `AX615D_DEMO_V1_3.BRD`: AX615D DEMO V1.3 Allegro reference board.
- `AX615D_DEMO_V1_3_BOM.xlsx`: V1.3 bill of materials.
- `AX615D_硬件设计用户指南_V1.2.docx`: AX615D hardware design guide.
- `AX615D_DEMO使用指南_V1.0.docx`: DEMO usage guide.
- `AX615D_硬件设计CheckList_V1.1.docx`: hardware checklist.
- `AX615D_Device_Specification_V0.9.docx`, `AX615D_PIN_OUT.xlsx`: AX615D device specification and pinout.
- `AX615D_VI输入接口场景_V0.6.xlsx`: AX615D VI pinmux/input combinations, including DPHY 2/4-lane, RX and BT/DVP reuse cases.
- `AX615_AVL_V1.1.xlsx`: AVL, including W25N02KVZEIR.
- `romcode-rtl0.8-readme.txt`: ROM NAND image variant notes; ROM binaries were not copied.
- `SENSOR_SC438HAI_V1_0.pdf`: six-page AXERA sensor reference schematic (Sensor / Power / Connector / IR-CUT), not a datasheet. `SENSOR_SC438HAI_V1_0_Assembly.pdf` is its two-page placement drawing, not a land-pattern or stencil specification.
- `SC438HAI_数据手册_V1.0.pdf`: 39-page SmartSens datasheet downloaded through Xiaozhi from [AX620E SC438HAI](https://wiki.aixin-chip.com/pages/viewpage.action?pageId=203647265), attachment `203647344`. SHA-256: `c50d811f0eaae5ceab9b2081ce0e2059e0f09766f869c7ae286af05678858595`. Pages 8–9 contain power sequencing; page 38 records E1/F1 changing to NC in V0.5. Xiaozhi RAG also indexes V1.3, but its original PDF has not been obtained or verified.
- `动态调压注意事项.pdf`: SVN `MC20L/AX615_Release_doc/HW/chip/动态调压注意事项.pdf`, exported at repository revision `43263`, last changed `42317`; document R13, 2026-09-08. Covers PWM and VDDCORE checks across SPL / U-Boot / Kernel.

## Findings and boundaries

- The AX615D V1.2 guide associates PWM7 with VDDCORE DVS (240 kHz) and requires DVS support; the V1.1 hardware checklist explicitly requires PWM adjustment. Sensor1 remains assigned PWM8. The earlier requested no-runtime-adjustment policy conflicts with these requirements unless an applicable exception is confirmed; it is not approval to omit the DVS circuit or disable its software control.
- The sensor reference schematic, page 3, supplies VREF/rail capacitors and records SID low/high as 7-bit addresses `0x30`/`0x32`; page 4 supplies a regulator reference. These are document evidence, not target-board measurements or approval to change the current two-bus topology. Datasheet V1.0's six-NC pin table is supported by its V0.5 revision history; the package summary still says `41(4NC)`.
- The SVN AVL lists W25N02KVZEIR as WINBOND, 3.3 V, 2 Gbit. This row is a component listing and does not state ROM/SPL cold-boot PASS.
- ROM `rtl0.8/readme.txt` lists NAND image variants by 2K, 2KMP, 4K, and 4KMP geometry/mode; it does not identify W25N02KVZEIR by part number.
- The board at `10.126.16.141` is confirmed as an AX615Q DEMO and its NAND is confirmed as `W25N02KVZEIR`. The read-only MTD check reports `0x10000000` (256 MiB) and erase size `0x20000` (128 KiB); the running kernel exposes only the generic `spi-nand` identity.
- The confirmed AX615 NAND build command is `make p=AX615_nand all install axp`. Its build dependencies support booting the confirmed `W25N02KVZEIR` NAND. This is the confirmed SDK build path; no flash write or reboot was performed during this collection.
- Local U-Boot source also has a `W25N02KV` entry. The BL1 continuous-read compile option remains disabled by default in the checked header; this was not changed as part of the confirmed boot build flow.
- Same-edge PWM triggering for the two SC438HAI sensors was already verified in the `ax615-双目深度` topic and is not an open issue here.
- These OrCAD/Allegro files are reference inputs only. They were not converted, edited, or validated as a new product design. The Linux server has no KiCad; board editing remains for the Windows/KiCad environment.

## Git status

Repository: `git@github.com:liyue75/hw_chip.git` (renamed from `liyue75/pcb`), branch `main`; the Linux checkout remains `/home/liyue/pcb`. The packet and original SC438HAI PDFs were committed and pushed in `9d20ea2` and `3741ee9`; later additions are tracked in subsequent commits. The 2026-10-23 update adds the actual sensor datasheet and DVS notes and corrects their classification. The Windows rev0.5 product design is a separate working project, not a file included in this reference packet.
