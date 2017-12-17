# M2GL025-Creative-Board-Notes

Some notes from tinkering with the Microsemi M2GL025 creative board.

[![Blue Board](/images/Microsemi_Board_IMG_9611_tn.jpg)](/images/Microsemi_Board_IMG_9611.jpg)
[![Yellow Board](/images/RISC-V_yellow_board_tn.png)](/images/RISC-V_yellow_board.png)

(Looks like it was originally done up in blue and got a new silkscreen job in 2017.  I don't see any changes to PCB routing or parts placement between the two).

## About the board

It's a USD$99 evaluation board from Microsemi that implements a RISC-V CPU system in a 25kLE IGLOO2 FPGA.

 * IGLOO2 M2GL025-VF256 flash-based FPGA
 * 64MB DDR2 SDRAM (Alliance AS4C32M16D2A-25BCN)
 * 8MB SPI flash (Microchip 26F064B)
 * Integrated FT4232-based FlashPro5 USB JTAG adapter
 * Pin headers compatible with various popular hobbyist boards:
   * Arduino
   * mikroBUS (MikroElektronika)
   * PMod (digilent)

The board comes pre-flashed with a minimal RISC-V system and firmware.

[Link to Buy](http://www.futureelectronics.com/en/Technologies/Product.aspx?ProductID=FUTUREM2GLEVBFUTUREELECTRONICSDEVTOOLS7091559&IM=0)
(I have no affiliation with any of these companies)

## What tools do I need in order to program the board?

It depends what you mean by "program".  You can either program the FPGA or the CPU (assuming your FPGA design includes a soft CPU such as the RISC-V).

### FPGA Tools

In order to program the FPGA, you need to use Microsemi's Libero tool to synthesize a design and generate the FPGA programming files.  Once you've generated the programming files in Libero you use the FlashPro application to flash them into the target board's FPGA.  The FlashPro application comes with Libero.

### RISC-V CPU Tools

In order to program the CPU you need a compiler/linker/assembler toolchain.  For RISC-V that toolchain is [GCC](https://github.com/riscv/riscv-gcc) (though folks are working on [LLVM](https://github.com/riscv/riscv-llvm)).

 * [RISC-V GCC](https://github.com/riscv/riscv-gcc) is the primary compiler/linker/assembler/debugger toolchain.
 * [RISC-V OpenOCD](https://github.com/riscv/riscv-openocd) is an OpenOCD fork that supports RISC-V JTAG debug interface.
 * [GNU MCU Eclipse](https://gnu-mcu-eclipse.github.io/) is a family of Eclipse extensions for ARM and RISC-V development

You can either use the above tools directly or use a vendor-branded Eclipse-based IDE, such as:

 * [Microsemi SoftConsole](https://www.microsemi.com/products/fpga-soc/design-resources/design-software/softconsole#overview) Bundles Eclipse + GNU MCU extensions (OpenOCD and GCC) + some Microsemi-related examples.
 * [SiFive Freedom Fries Studio](https://www.sifive.com/products/tools/) is like SoftConsole but geared towards SiFive's product line.  Maybe you could get it to work with the SoftConsole projects mentioned on this page -- I haven't tried.

 If you're just getting started with the M2GL025 creative board then you probably want SoftConsole.

## What's the licensing deal?

 * **RISC-V core**: Freely available.  Note that there appear to be two RISC-V cores in the Microsemi DirectCore catalog (CoreRISCV_AXI4 and MiV_RV32) -- more on that below.  The license verbiage for the MiV_RV32 core sounds more permissive (["This core is being released under a modified Apache 2.0 license and is freely available through Libero"](http://www.actel.com/ipdocs/MiV_RV32IMA_L1_AHB_HB.pdf)).
 * **Libero**: The [Silver/Free license level](https://www.microsemi.com/products/fpga-soc/design-resources/licensing) can program devices up to 25k LEs, which matches the capacity of the M2GL025 IGLOO2 part.
 * **SoftConsole**: Free.  It's just Eclipse + MCU Eclipse extensions + GCC + OpenOCD in a bundle with some Microsemi-related examples.

However, IANAL and I'm not sure about the specifics of licensing for commercial applications.

## What's the difference between CoreRISCV_AXI4 and MiV_RV32 cores?

There are three RISC-V cores in Microsemi catalog:
 * [MiV_RV32IMA_L1_AHB](http://soc.microsemi.com/products/ip/search/detail.aspx?id=903)
 * [MiV_RV32IMAF_L1_AHB](http://soc.microsemi.com/products/ip/search/detail.aspx?id=904) "F is for float"?
 * [RISC-V_AXI4](http://soc.microsemi.com/products/ip/search/detail.aspx?id=896)

I'm not sure how MiV and AXI4 differ.  It looks like MiV is the most recent version (does that mean CoreRISCV_AXI4 is deprecated?) and comes in two flavors (with and without floating point).  If you have more information please let me know.

One difference is in the HAL files produced by the Firmware Catalog.  The RISC-V HAL v2.0.104 is to be used with the RISC-V_AXI4 core, while version >= 2.1.101 work with the MiV-RV32 cores, according to the HAL User Guide pdfs.

## Do I need to purchase a separate USB FlashPro programmer to use with the creative board?

No.  The board has an integrated FT4232-based USB FlashPro5 that works with the FlashPro application.  Signal-wise the integrated FlashPro connects to the IGLOO2 via JTAG, so there is nothing non-standard going on, it's just included on the board.  There is no JTAG header on the creative board.

If you use the creative board design as a basis for your own design, you could leave off the FT4232 parts and just bring out the normal JTAG signals on a header for use with a [regular FlashPro4/5](https://www.microsemi.com/products/fpga-soc/design-resources/programming/flashpro#ordering).

## Where can I get board schematics?

 * [Future Electronics](http://www.futureelectronics.com/en/campaign/microsemi/Pages/CreativeDevelopmentBoard.aspx).  Fill out the "Request Demo Projects" link, submit your information, and you'll get an email (<1min for me) with links to the board schematics and BOM in pdf format.

## What example projects are available as a starting point?

The very best way to get an example project that builds in SoftConsole 5.2 and runs on the YellowBoard is to use Firmware Catalog to generate it.
  * Search for 'riscv' in Firmware Catalog
  * Right click the latest RISC-V HAL version
  * Select "Generate Sample Project": ![GenSampleProject](/images/firm_cat_systick.png)
  * Open SoftConsole and choose File -> Import, Existing Projects into Workspace, and enter the directory of the files you generated above.

As for other examples, Future and Microsemi have conveniently duplicated content across a variety of websites.  Here's a summary.  Note that most of these examples are for SoftConsole 5.1 and won't build under SoftConsole 5.2 unless you [migrate them](https://github.com/RISCV-on-Microsemi-FPGA/SoftConsole/#migrating-from-softconsole-v51-to-v52).

* At [this Future page](http://www.futureelectronics.com/en/campaign/microsemi/Pages/CreativeDevelopmentBoard.aspx) there are four (ok, three) projects available for download as .zip archives.
  * [Blinky LED](http://www.futureelectronics.com/en/manufacturers/microsemi/Documents/BlinkyIGLOO2.zip)
    * HDL-only Libero project.  No RISC-V core.  Includes pre-built programming files (.stp and .pro) so you can flash the FPGA with only [FlashPro Express](https://www.microsemi.com/products/fpga-soc/design-resources/programming/flashpro#software) if you don't want to rebuild everything in Libero.
  * [FLiR Thermal Imaging](http://www.futureelectronics.com/en/campaign/microsemi/PublishingImages/TVBLockDiagram.jpg)
    * Wait, it's just a link to a block diagram?!?
    * Here's [more information that still isn't a .zip file](http://ftm.futureelectronics.com/2017/03/future-electronics-flir-lepton-lwir-thermal-imaging-camera-demo-on-microsemi-igloo2-creative-board/).
  * [Hello World](http://www1.futureelectronics.com/mailing/Microsemi/IGL2_RISC-V_Hello_World.zip)
    * Includes one HDL project: IGL2_RISCV_Systick_Blinky and pre-built .stp programming file
    * Includes one SoftConsole project: IGL2_RISCV_Systick_Blinky (with "Alternate application file" main.c)
      * This project does not build when I import it into SoftConsole 5.2: ![HelloWorldBuild](/images/hello_world_build_fail.png)
  * [RISC-V Tic-tac-toe](http://www.futureelectronics.com/en/manufacturers/microsemi/Documents/TicTacToe%20Demo.zip)
    * Includes two Libero HDL projects (and pre-built programming files): CoreRISCV_AXI4_BaseDesign and IGL2_CoreRISCV_AXI4_TickTacToe
    * Includes two SoftConsole projects: riscv-systick-blinky and TickTackToe
      * Has same issue as Hello World above: projects are for SoftConsole 5.1 and need to be migrated to 5.2.

* https://github.com/RISCV-on-Microsemi-FPGA/M2GL025-Creative-Board
  * Includes two Libero HDL projects (and pre-built programming files): CoreRISCV_AXI4_BaseDesign and IGL2_CoreRISCV_AXI4_TickTacToe
  * Includes two SoftConsole 5.1 projects: riscv-systick-blinky and TickTackToe
  * YellowBoard directory contains the factory-programmed HDL and firmware (Systick_Blinky) and uses the CoreRISCV_AXI4 core.  Programming files are also included.
  * None of the SoftConsole projects will build in 5.2 without [migrating them](https://github.com/RISCV-on-Microsemi-FPGA/SoftConsole/#migrating-from-softconsole-v51-to-v52).

* https://github.com/RISCV-on-Microsemi-FPGA/uCOS
  * Includes one SoftConsole 5.1 project: blinky (needs migration to 5.2 in order to build)
  * Includes riscv-uCOS-sample: This is a port of Micrium's uC/OS-II RTOS to RISC-V.
    * DOES build under SoftConsole 5.2 - but only the Debug target.  So it's possible to load into RAM and run with OpenOCD.  The Release target isn't properly configured.

* https://github.com/RISCV-on-Microsemi-FPGA/SoftConsole/
  * Very useful bits of information about generating SoftConsole project templates from Firmware Catalog and migrating from SoftConsole 5.1 to 5.2.

* https://github.com/RISCV-on-Microsemi-FPGA/Documentation
  * Not a project, but a small assortment of somewhat useful pdfs related to the projects.

## What are the HAL files and where do they come from?

Microsemi provides the low-level hardware abstraction libraries (normally called a BSP - board support package) that expose a C API for of any IP cores instantiated in your design.  You can generate these files from the [Firmware Catalog](https://www.microsemi.com/products/fpga-soc/design-resources/design-software/firmware-catalog) application, which is installed by default with Libero.

In Firmware Catalog, for a design that uses the Microsemi RISC-V core, you'd search for 'risc' and select "Generate...".  This will make the tool spit out the C/asm files that you can include in your SoftConsole/gcc project.  Repeat as needed for any additional cores (e.g., SPI).

Additionally, Firmware Catalog can be used to generate RISC-V SoftConsole project templates (see above section).

**NOTE**: The RISC-V HAL v2.0.104 is to be used with the RISC-V_AXI4 core, while version >= 2.1.101 work with the MiV-RV32 cores, according to the HAL User Guide pdfs.

**NOTE2**: The RISC-V HAL files are duplicated at https://github.com/RISCV-on-Microsemi-FPGA/riscv-hal.  This is the same riscv-HAL you would normally generate using the Firmware Catalog application and it's probably best to get them from the catalog instead.

![riscvHAL](/images/firm_cat_riscv.png)

## How do I restore the original as-shipped FPGA bitfile onto the board?

HDL sources and pre-built FPGA programming files corresponding to the as-shipped design (Systick_Blinky) can be found in the [YellowBoard folder of the M2GL025-Creative-Board repo](https://github.com/RISCV-on-Microsemi-FPGA/M2GL025-Creative-Board/tree/master/YellowBoard/).  Use the FlashPro application to program the [.stp file](https://github.com/RISCV-on-Microsemi-FPGA/M2GL025-Creative-Board/blob/master/YellowBoard/Programming%20the%20Target%20Device/PROC_SUBSYSTEM.stp).

## How do I restore the original as-shipped RISC-V CPU firmware onto the board?

The original firmware is in the [IGL2_RISCV_Systick_Blinky](https://github.com/RISCV-on-Microsemi-FPGA/M2GL025-Creative-Board/tree/master/YellowBoard/Software%20project/IGL2_RISCV_Systick_Blinky) SoftConsole project.  How do you get it on the board?

**Short answer**: The CPU firmware gets programmed along with the FPGA fabric when you use FlashPro to program the YellowBoard [.stp file](https://github.com/RISCV-on-Microsemi-FPGA/M2GL025-Creative-Board/blob/master/YellowBoard/Programming%20the%20Target%20Device/PROC_SUBSYSTEM.stp) mentioned above.  So you don't actually have to do anything to reload this software other than re-flash the FPGA.

**Longer answer**: You might be tempted to think that the software lives in the 26F064B serial flash part, but it doesn't!  It's stored in the eNVM (embedded non-volatile memeory) of the IGLOO2.  The M2GL025 part has 256kB of eNVM at base address is fixed at 0x6000_0000.  The eNVM gets programmed along with the FPGA fabric when you use FlashPro to program the target device.

## How do I load new CPU firmware so that it runs at power up?

1. Setup a SoftConsole 5.2 project with your software.
2. Build the Release target.  This will use a linker command file that links the code at 0x6000_0000 (eNVM base) as opposed to RAM base 0x8000_0000.  If you use Firmware Catalog to generate your SoftConsole project template, the Release target uses the microsemi-riscv-igloo2.ld linker file.  The build result is a .hex file.
3. In Libero, double-click "Update eNVM Memory Content" in the Design Flow tab: ![eNVM0](/images/envm_update_00.png)
  * You'll be presented with this dialog: ![eNVM1](/images/envm_update_01.png)
  * Select the data storage region and click Edit to choose the .hex file you just built in SoftConsole: ![eNVM1](/images/envm_update_02.png)
3. Generate the programming files in Libero and program the target with FlashPro as usual.

## What is the initial program counter / reset vector for the RISC-V CPU?

The [privileged ISA](blob:https://riscv.org/ab5ac531-378c-4f43-ba5c-1baf025519a6) (section 3.3) says reset vector is implementation specific.

Looking at our specific implementation then, the [CoreRISCV_AXI4 core user manual](http://www.actel.com/ipdocs/CoreRISCV_AXI4_HB.pdf) (section 5.1.1) says default `RESET_VECTOR_ADDR` is `0x6000_0000`, and the example projects [show it configured that way](https://github.com/RISCV-on-Microsemi-FPGA/M2GL025-Creative-Board/blob/master/Modify_The_FPGA_Design/CoreRISCV_AXI4_BaseDesign/component/work/PROC_SUBSYSTEM/CORERISCV_AXI4_0/rtl/vlog/core/coreriscv_axi4.v#L153):

```verilog
parameter RESET_VECTOR_ADDR = 60000000;
```

As described in the firmware programming section above, `0x6000_0000` is the base address of the eNVM memory in the IGLOO2 and is the location of \_start/.text when using the [Linker_Run_from_NVM.lds](https://github.com/RISCV-on-Microsemi-FPGA/M2GL025-Creative-Board/blob/master/YellowBoard/Software%20project/IGL2_RISCV_Systick_Blinky/Linker_Run_from_NVM.lds) linker command file.  So it makes a sense as the reset vector!

## Is the IGLOO2 eNVM memory base address configurable?
According to [UG0448](https://www.microsemi.com/document-portal/doc_download/132009-ug0448-igloo2-fpga-high-performance-memory-subsystem-user-guide) (sections 2.2, 4.1.4)... I'm not sure.  Maybe you can configure the AHB bus matrix.  I didn't go that deep.

There's some related information about doing an initial copy from eNVM to SRAM in [AC421](https://www.microsemi.com/document-portal/doc_view/133704-ac421-igloo2-fpga-sram-initialization-from-envm-libero-soc-v11-7-application-note).  It doesn't apply to the creative board though.

## What is the memory map of the Creative Board?
It depends on the specific HDL implementation, but for the factory default FPGA load (Systick_Blinky), it's given here:

* [README.txt](https://github.com/RISCV-on-Microsemi-FPGA/M2GL025-Creative-Board/blob/master/YellowBoard/FPGA%20Design/IGL2_RISCV_Systick_Blinky/README.txt)

I think that comes from an output of Libero... maybe [subsystem.bfm](https://github.com/RISCV-on-Microsemi-FPGA/M2GL025-Creative-Board/blob/master/YellowBoard/FPGA%20Design/IGL2_RISCV_Systick_Blinky/component/work/PROC_SUBSYSTEM/subsystem.bfm)?

That's the hardware memory map.  If you're just talking about the linker sections you can look at:
* [Linker command file](https://github.com/RISCV-on-Microsemi-FPGA/M2GL025-Creative-Board/blob/master/YellowBoard/Software%20project/IGL2_RISCV_Systick_Blinky/Linker_Run_from_NVM.lds)
* [.lst file](https://github.com/RISCV-on-Microsemi-FPGA/M2GL025-Creative-Board/blob/master/YellowBoard/Software%20project/IGL2_RISCV_Systick_Blinky/Release/IGL2_RISCV_Systick_Blinky.lst)
* [.map file](https://github.com/RISCV-on-Microsemi-FPGA/M2GL025-Creative-Board/blob/master/YellowBoard/Software%20project/IGL2_RISCV_Systick_Blinky/Release/IGL2_RISCV_Systick_Blinky.map)

## How does the external serial flash / SPI memory get loaded into RAM?
It doesn't, at least not in the default design.  An SPI core is only instantiated in some of the example designs (e.g., [TicTacToe](https://github.com/RISCV-on-Microsemi-FPGA/M2GL025-Creative-Board/tree/master/Modify_The_FPGA_Design/IGL2_CoreRISCV_AXI4_TickTacToe)).  The default program is stored in the eNVM region of the IGLOO2, not the serial flash.  See eNVM discussion above.

If you want to read/write the serial flash part:
1. Make sure CORESPI is appropriately instantiated in your Libero project
2. Generate and include the SPI HAL files in your SoftConsole project
3. Write application code that uses the SPI HAL API.  A starting place might be the [baremetal bootloader](https://github.com/RISCV-on-Microsemi-FPGA/SoftConsole/tree/master/riscv-simple-baremetal-bootloader). (Note that this particular project doesn't run on the YellowBoard with the factory FPGA.)

## Simulation

There are a few simulators to choose from.  Here are two.  Some others are listed [here](https://riscv.org/software-status/#simulators).
1. [Spike](https://github.com/riscv/riscv-isa-sim) This is the RISC-V reference simulator, if there's such a thing.
2. [Qemu](https://github.com/riscv/riscv-qemu/issues) A Qemu fork for RISC-V.

### What is pk?

https://github.com/riscv/riscv-pk

Per https://riscv.org/software-tools/ (and [this SO post](https://stackoverflow.com/a/28842732)), RISC-V PK is a proxy kernel that services system calls generated by code built and linked against the RISC-V Newlib port (this does not apply to Linux, as it handles the system calls).  pk should only be used when you want to simulate an application that uses such system calls.

### What is the reset vector for qemu and spike simulators?
It's the same for both, as described here: https://github.com/slavaim/riscv-notes/blob/master/bbl/boot.md
* reset vector: 0x1000
* memory base: 0x80000000

There are two instructions at 0x1000, hard-coded into qemu, that do a jump to 0x80000000.  So the effective initial PC (and the location your code should be linked to for simulation) is 0x80000000.

### Can I change qemu's RISC-V reset vector?
No, it's hard-coded.  See above.

### How do I set the program counter manually (in gdb)?
`(gdb) set $pc = 0x80000000`

### How do I simulate the M2GL025 Creative Board examples?
TBD.


## Additional Resources
### Vendor information
* [Microsemi](https://www.microsemi.com/products/fpga-soc/mi-v-ecosystem#overview): RISC-V documentation root

* **Future Electronics**:
  * [Static archive of HDL + software projects for the M2GL025 Creative Board](http://www.futureelectronics.com/en/campaign/microsemi/Pages/CreativeDevelopmentBoard.aspx).  If you follow the "Request Demo Projects" link and submit the information, you'll get an email pointing to the board schematics and BOM in pdf format.
  * [M2GL025 Creative Board Purchase](http://www.futureelectronics.com/en/Technologies/Product.aspx?ProductID=FUTUREM2GLEVBFUTUREELECTRONICSDEVTOOLS7091559&IM=0)

* **SiFive**: [GCC toolchain and SiFive's IDE](https://www.sifive.com/products/tools/)
