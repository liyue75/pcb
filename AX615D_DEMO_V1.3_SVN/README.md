# AX615D / SC438HAI PCB reference packet

Collected on 2026-10-22 for the AX615 stereo PCB work. The SVN documents below are reference designs, not an approved production design or a KiCad-ready project.

## Source

SVN root: `https://svn-ext.aixin-chip.com/svn/aixin_ext_svn`

AX615D DEMO V1.3 source directory: `MC20L/AX615_Release_doc/HW/board/AX615D/AX615D_DEMO_V1.3/`
The schematic PDF was checked at repository revision 43259. Other exported files came from the same AX615D release tree during this collection.

## Files

- `AX615D_DEMO_V1_3.pdf`, `AX615D_DEMO_V1_3.DSN`: AX615D DEMO V1.3 Capture schematic and PDF.
- `AX615D_DEMO_V1_3.BRD`: AX615D DEMO V1.3 Allegro reference board.
- `AX615D_DEMO_V1_3_BOM.xlsx`: V1.3 bill of materials.
- `AX615D_硬件设计用户指南_V1.2.docx`: AX615D hardware design guide.
- `AX615D_DEMO使用指南_V1.0.docx`: DEMO usage guide.
- `AX615D_硬件设计CheckList_V1.1.docx`: hardware checklist.
- `AX615D_Device_Specification_V0.9.docx`, `AX615D_PIN_OUT.xlsx`: AX615D device specification and pinout.
- `AX615_AVL_V1.1.xlsx`: AVL, including W25N02KVZEIR.
- `romcode-rtl0.8-readme.txt`: ROM NAND image variant notes; ROM binaries were not copied.
- `SENSOR_SC438HAI_V1_0.pdf`, `SENSOR_SC438HAI_V1_0_Assembly.pdf`: existing local SC438HAI reference files, copied into this packet without modification.

## Findings and boundaries

- The AX615D V1.2 guide associates PWM7 with VDDCORE DVS (240 kHz). The board design direction supplied for this stereo project is sensor1 on PWM8 and no runtime dynamic-voltage adjustment. If the DVS circuit itself is omitted, reconcile that choice with the guide's statement that VDDCORE must support DVS.
- The SVN AVL lists W25N02KVZEIR as WINBOND, 3.3 V, 2 Gbit. This row is a component listing and does not state ROM/SPL cold-boot PASS.
- ROM `rtl0.8/readme.txt` lists NAND image variants by 2K, 2KMP, 4K, and 4KMP geometry/mode; it does not identify W25N02KVZEIR by part number.
- The board at `10.126.16.141` is confirmed as an AX615Q DEMO and its NAND is confirmed as `W25N02KVZEIR`. The read-only MTD check reports `0x10000000` (256 MiB) and erase size `0x20000` (128 KiB); the running kernel exposes only the generic `spi-nand` identity.
- The confirmed AX615 NAND build command is `make p=AX615_nand all install axp`. Its build dependencies support booting the confirmed `W25N02KVZEIR` NAND. This is the confirmed SDK build path; no flash write or reboot was performed during this collection.
- Local U-Boot source also has a `W25N02KV` entry. The BL1 continuous-read compile option remains disabled by default in the checked header; this was not changed as part of the confirmed boot build flow.
- Same-edge PWM triggering for the two SC438HAI sensors was already verified in the `ax615-双目深度` topic and is not an open issue here.
- These OrCAD/Allegro files are reference inputs only. They were not converted, edited, or validated as a new product design. The Linux server has no KiCad; board editing remains for the Windows/KiCad environment.

## Git status at collection

`/home/liyue/pcb` was not a Git worktree when checked. No repository was initialized, and no commit or push was made. Add this packet only to the intended private repository after checking that repository's destination and status.
