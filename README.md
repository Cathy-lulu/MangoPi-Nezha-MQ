# MangoPi-Nezha-MQ


There's a growing demand for [embedded operating systems](https://opensource.com/article/20/6/open-source-rtos), and it's best when the one you build upon is open source. The [RT-Thread](https://github.com/RT-Thread/rt-thread) project's R&D team has spent three years of research and intensive development to arrive at the project's latest offering: RT-Thread Smart. It is a microkernel operating system aimed primarily at midrange to high-end processors such as RISC-V or Arm Cortex-A that with a memory management unit (MMU) and provides a competitive and POSIX-based software platform for all industries in the embedded space.

RT-Thread Smart is a professional, high-performance, microkernel operating system for real-time applications. It offers an open source foundation for embedded devices in any market, including security (e.g., internet protocol cameras), industrial control, onboard devices, consumer electronics, and anything else using embedded technology (which is increasingly coming to mean "everything"). It's significant because, unlike traditional IoT operating systems, a microkernel operating system can fill the gap between a traditional real-time operating system (RTOS) and a comparatively large operating system like Linux to achieve the best balance between real-time performance, cost, security, startup speed, and more.


Get Started Tutorial: https://medium.com/@rt-thread/get-started-with-rt-thread-smart-opensource-microkernel-operating-system-37e30c9c4ac3


RT-Thread Smart splits a system into kernel space and user space by taking advantage of the MMU and system call methods. It then divides the address space for each mode

![No photo description available.](https://scontent-sin6-3.xx.fbcdn.net/v/t1.6435-9/126206632_209074050607296_3083640399039373909_n.png?_nc_cat=106&ccb=1-7&_nc_sid=825194&_nc_ohc=PCef46rx-Q8AX8Zptfy&tn=ktcpvKAYWqe1aHgy&_nc_ht=scontent-sin6-3.xx&oh=00_AT9EL2upulCEat7CT30yypJwvU_SNABf_Ulq8bcioPI7BA&oe=63286F6A)



The process management module rtthread-smart / kernel / components / lwp, which includes the following parts:

- system call for user space (lwp_syscall.c/h);
- User space process management (lwp_pid.c/h, lwp.c/h);
- elf executable application loader;
- MMU-based virtual memory management, address space management;
- Inter-process channel communication mechanism and shared memory mechanism;

In the overall operating system, the kernel also includes:

- File system Interface (DFS) - Virtual File System Interface;
- BSD socket interface (SAL/socket) - abstract socket;
- Device driver frame interface;
- Optional device drivers (e.g. UART, GPIO, IIC, etc.);



# MangoPi-Nezha MQ

**MangoPi-Nezha MQ** is an open-source RISC-V development board that integrates Wi-Fi connectivity and dual USB Type-C ports into one tiny (4x4 cm) package. Powered by the Allwinner D1s chip, it provides support for rich display interfaces like RGB, DSI, and LVDS. It also includes onboard silicon microphones and class-D amplifiers for audio output.

## Features & Specifications

- Powered by a D1s (a 1 GHz D1 chip with a C906 core, a RISC-V architecture, and 64 MB of storage)
- USB Type-C support for USB-OTG & USB-HOST
- 2 x 22 pin-expansion headers
- Solder points for NAND/NOR flash as storage or system ROM
- MicroSD card slot
- On-board RTL8189-based Wi-Fi
- 15-pin universal Raspberry Pi DSI FPC
- 40-pin universal RGB FPC (4-Wire resistive-touch interface included)
- 6-pin universal capacitive touch FPC
- On-board mic
- Audio output
- 24-pin DVP interface (usable as RMII)
- BOOT & Reset buttons
- 4 fixed assembly feet
- 4x4 cm

![img](https://www.crowdsupply.com/img/9fd5/mangopi-pair-both-sides-3-cs_jpg_md-xl.jpg)

# Get RT-Smart Run on MangoPi-Nezha MQ

**Preparation**

- Kernel: rt_smart_kernel.bin
- Hardware: MangoPi-Nezha MQ
- RT-Smart Pack: [mango_rtsmart_app.zip](https://drive.google.com/file/d/1HhyHWwQrh4y7pjzXgEDCsOVHbUF_WE4p/view?usp=sharing)

**Partition**

Use fdisk for partitioning.

After mounting the USB disk to the Ubuntu system, check out the device path /dev/sdb. Using a 32GB TF card, the sector size is 512 bytes, according to the instructions we should reserve 8M idle, then we will get to know that the partition sector starts at: 16384.

Command: sudo fdisk /dev/sdb 
select: o, n, p, 1, 16384, enter

```Command
Created a new DOS disklabel with disk identifier 0x3668b987.

Command (m for help): n
Partition type
   p   primary (0 primary, 0 extended, 4 free)
   e   extended (container for logical partitions)
Select (default p): p
Partition number (1-4, default 1): 1
First sector (2048-62410751, default 2048): 16384
Last sector, +sectors or +size{K,M,G,T,P} (16384-62410751, default 62410751): 

Created a new partition 1 of type 'Linux' and of size 29.8 GiB.

Command (m for help): w
The partition table has been altered.
```

The correct partitioning result is as follows:

```
mango@virtual:/dev$ sudo fdisk -l /dev/sdb
Disk /dev/sdb: 29.8 GiB, 31954305024 bytes, 62410752 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: dos
Disk identifier: 0x3668b987

Device     Boot Start      End  Sectors  Size Id Type
/dev/sdb1       16384 62410751 62394368 29.8G 83 Linux
mango@virtual:/dev$
```

Format sdb1: 

```
$ sudo mkfs -t fat /dev/sdb1
```

**Flashed to RT-Smart Kernel**

```
$ sudo dd if=rt_smart_kernel.bin of=/dev/sdb bs=1024
2888+1 records in
2888+1 records out
2957313 bytes (3.0 MB, 2.8 MiB) copied, 8.32574 s, 355 kB/s
$ sync
```

**Enable RT-Thread**

Put the TF card into the MangoPi-Nezha MQ, the serial port 0 (bottom row needle 7, 8 pin ), the serial port baud rate is 500000, and then power on:

```
[26]HELLO! BOOT0 is starting!
[27]BOOT0 commit : fb73c68
[28]set pll start
[28]periph0 has been enabled
[29]set pll end
[30]board init ok
[30]ZQ value = 0x2f***********
[31]get_pmu_exist() = -1
[32]ddr_efuse_type: 0xa
[33][AUTO DEBUG] single rank and full DQ!
[34]ddr_efuse_type: 0xa
[35][AUTO DEBUG] rank 0 row = 13
[36][AUTO DEBUG] rank 0 bank = 4
[37][AUTO DEBUG] rank 0 page size = 2 KB
[37]DRAM BOOT DRIVE INFO: V0.24
[38]DRAM CLK = 528 MHz
[39]DRAM Type = 2 (2:DDR2,3:DDR3)
[40]DRAMC read ODT  off.
[40]DRAM ODT off.
[41]ddr_efuse_type: 0xa
[42]DRAM SIZE =64 M
[44]DRAM simple test OK.
[45]dram size =64
[45]card no is 0
[45]sdcard 0 line count 4
[46][mmc]: mmc driver ver 2021-04-2 16:45
[52][mmc]: Wrong media type 0x0
[53][mmc]: ***Try SD card 0***
[60][mmc]: HSSDR52/SDR25 4 bit
[61][mmc]: 50000000 Hz
[61][mmc]: 30474 MB
[62][mmc]: ***SD/MMC 0 init OK!!!***
[98]Loading boot-pkg Succeed(index=0).
[99]*******************TOC1 Item Message*************************
[100]Entry_name        = opensbi
[101]Entry_data_offset = 0x600
[102]Entry_data_len    = 0x188e0
[103]encrypt           = 0x0
[103]Entry_type        = 0x0
[104]run_addr          = 0x40000000
[105]index             = 0x0
[105]Entry_end         = 0x3b454949
[106]*************************************************************
[108]*******************TOC1 Item Message*************************
[109]Entry_name        = dtb
[110]Entry_data_offset = 0x19000
[111]Entry_data_len    = 0x7475
[111]encrypt           = 0x0
[112]Entry_type        = 0x0
[113]run_addr          = 0x40200000
[114]index             = 0x0
[114]Entry_end         = 0x3b454949
[115]*************************************************************
[117]*******************TOC1 Item Message*************************
[118]Entry_name        = kernel
[119]Entry_data_offset = 0x20600
[120]Entry_data_len    = 0xa6cf0
[120]encrypt           = 0x0
[121]Entry_type        = 0x0
[122]run_addr          = 0x40400000
[123]index             = 0x0
[123]Entry_end         = 0x3b454949
[124]*************************************************************
[126]Jump to second Boot.

OpenSBI v0.9-165-gefbc2b8
   ____                    _____ ____ _____
  / __ \                  / ____|  _ \_   _|
 | |  | |_ __   ___ _ __ | (___ | |_) || |
 | |  | | '_ \ / _ \ '_ \ \___ \|  _ < | |
 | |__| | |_) |  __/ | | |____) | |_) || |_
  \____/| .__/ \___|_| |_|_____/|____/_____|
        | |
        |_|

Platform Name             : Allwinner D1 NeZha
Platform Features         : medeleg
Platform HART Count       : 1
Platform IPI Device       : aclint-mswi
Platform Timer Device     : aclint-mtimer @ 24000000Hz
Platform Console Device   : uart8250
Platform HSM Device       : ---
Platform Reboot Device    : sunxi-wdt-reset
Platform Shutdown Device  : ---
Firmware Base             : 0x40000000
Firmware Size             : 248 KB
Runtime SBI Version       : 0.3

Domain0 Name              : root
Domain0 Boot HART         : 0
Domain0 HARTs             : 0*
Domain0 Region00          : 0x0000000014008000-0x000000001400bfff (I)
Domain0 Region01          : 0x0000000014000000-0x0000000014007fff (I)
Domain0 Region02          : 0x0000000040000000-0x000000004003ffff ()
Domain0 Region03          : 0x0000000000000000-0xffffffffffffffff (R,W,X)
Domain0 Next Address      : 0x0000000040400000
Domain0 Next Arg1         : 0x0000000040200000
Domain0 Next Mode         : S-mode
Domain0 SysReset          : yes

Boot HART ID              : 0
Boot HART Domain          : root
Boot HART ISA             : rv64imafdcvsux
Boot HART Features        : scounteren,mcounteren,mcountinhibit,time
Boot HART PMP Count       : 16
Boot HART PMP Granularity : 2048
Boot HART PMP Address Bits: 38
Boot HART MHPM Count      : 0
Boot HART MIDELEG         : 0x0000000000000222
Boot HART MEDELEG         : 0x000000000000b1ff
heap: [0x404fbdf4 - 0x41cfbdf4]
[I/I2C] I2C bus [i2c2] registered

 \ | /
- RT -     Thread Smart Operating System
 / | \     5.0.0 build Dec 31 2021
 2006 - 2020 Copyright by rt-thread team
lwIP-2.1.2 initialized!
SDC:hal_sdc_create host:000000004051bdf8 id:0
SDC:Not implement __mci_restore_io,798
SDC:Not imp hal_sdc_init,2218
SDC:hal_sdc_init,2251 no imp
SDC:hal_sdc_init,2259 no imp
SDC:SDC Host Capability:0x3820f Ocr avail:0x3f0000
SDC:SDC cd_mode:2 present_val:0
SDC:SDC id:0 dma_use:1 present:1
 driver version SD/MMC/SDIO Host Controller Driver(v0.27 2021-04-30 16:50) init ok.
card_detect insert
SD:mmc_card_create card:000000004052e1a8 id:0
SDC:Not implement __mci_restore_io,798
SDC:SDC clock=400000 kHz,src:0, n:1, m:14
SDC:SDC clock=400000 kHz,src:0, n:1, m:14
SD:***** Try sd *****
SD:card ocr: ffffffffc0ff8000
SD:Card CID number:55
SD:card raw cid:
SD:card raw csd:
SD:card raw scr:
SD:card raw SD status:
SDC:SDC clock=50000000 kHz,src:1, n:0, m:5
SD:card is switched to high speed mode, clk:50000 KHz
SD:Set bus width type: 2
SD:
============= card information ==============
SD:Card Type     : SDHC
SD:Card Spec Ver : 5.0
SD:Card RCA      : 0x0001
SD:Card OCR      : 0x40ff8000
SD:    vol_window  : 0x00ff8000
SD:    to_1v8_acpt : 0
SD:    high_capac  : 0
SD:Card CSD      :
SD:    speed       : 50000 KHz
SD:    cmd class   : 0x5b5
SD:    capacity    : 30474MB
SD:Card CUR_STA  :
SD:    speed_mode  : HS: 50 MHz
SD:    bus_width   : 2
SD:    speed_class : 10
SD:=============================================
SD:***** sd init ok *****
Initial card success
[I/sal.skt] Socket Abstraction Layer initialize success.
file system initialization done!
gt9xx_probe
Hello RISC-V
msh />[D/TOUCH.gt9xx] 39 31 31 0 0
[I/TOUCH.gt9xx] Found chip gt911
[I/TOUCH.gt9xx] GT9xx Config version: 0x00
[I/TOUCH.gt9xx] GT9xx Sensor id: 0xFF

msh />
```

**Run the Program**

Compile it and get the output: hello.elf file, directly copy it to the file system of the TF card.

![img](https://www.aw-ol.com/storage/portal/qq_20220104132056_3733b0e011.png)

Then put the TF card back into the MangoPi-Nezha MQ and boot it up:

```
msh />ls
Directory /:
System Volume Information<DIR>
etc                 <DIR>
hello.elf           237512
vi.elf              398544
msh />
msh />./hello.elf
msh />hello world!
```

Hello Program is working now!

**Notes**

We'd recommend you to use this pack under the Linux environment(64 bits), if you're considering Windows, please configure and install it under the tool directory, and then configure the environment variables concerning the script in the smart-env. sh.

Steps to use under Linux:

- First, execute source smart-env. sh riscv64, feel free to modify it based on your development environment.
- Enter the user app directory for application compilation, for example, to compile the hello application, you only need to execute the scons --app=hello.
- The compiled program will be generated in the root/bin directory, copy it to the SD card (note that it is the starting partition of the 8M offset), and then enter the rt-smart system, when the mount is successfully enabled it will start running.
