# 目的
## 知识点
### 计算机原理
    - CPU的编址与寻址: 基于分段机制的内存管理
    - CPU的中断机制
    - 外设：串口/并口/CGA，时钟，硬盘
    - Bootloader软件
### 编译运行bootloader的过程
    - 调试bootloader的方法
    - PC启动bootloader的过程
    - ELF执行文件的格式和加载
    - 外设访问：读硬盘，在CGA上显示字符串
    - ucore OS软件
### 编译运行ucore OS的过程
    - ucore OS的启动过程
    - 调试ucore OS的方法
    - 函数调用关系：在汇编级了解函数调用栈的结构和处理过程
    - 中断管理：与软件相关的中断处理
    - 外设管理：时钟

## 练习
### exec1:理解通过make生成执行文件的过程。
1.   操作系统镜像文件ucore.img是如何一步一步生成的？(需要比较详细地解释Makefile中每一条相关命令和命令参数的含义，以及说明命令导致的结果)
> gcc命令选项说明
>> 表：GCC常用的编译选项
>> - -c	编译、汇编指定的源文件，但是不进行链接
>> - -S	编译指定的源文件，但是不进行汇编
>> - -E	预处理指定的源文件，不进行编译
>> - -o [file1] [file2]	将文件 file2 编译成可执行文件 file1
>> - -I directory	指定 include 包含文件的搜索目录
>> - -g	生成调试信息，该程序可以被调试器调试
>> - -O 优化选项
>> - -fno-builtin:-fno-builtin-function:不使用C语言的内建函，使用得函数名可以与系统函数重名
>> - -Wall:-意思是编译后显示所有警告
>> - -w:不显示任何警告
>> - -W:显示编译器认为会出现错误的警告
>> - -ggdb:使用程序可以使用gdb调试 gdb2 gdb3
>> - -m32:生成32位机器的汇编代码
>> - -gstabs:生成本地OSstabs调试信息
>> - -nostdinc:不要在标准系统目录中寻找头文件.只搜索`-I'选项指定的目录(以及当前目录,如果合适)
>> - -fno-stack-protector:关闭代码编译时堆栈检查


1.1 ***编译、汇编所有的源代码为目标文件，为接下来的连接做准备***
```code 
        gcc -Ikern/init/ -fno-builtin -Wall -ggdb -m32 -gstabs -nostdinc  -fno-stack-protector -Ilibs/ -Ikern/debug/ -Ikern/driver/ -Ikern/trap/ -Ikern/mm/ -c kern/init/init.c -o obj/kern/init/init.o
       
        gcc -Ikern/libs/ -fno-builtin -Wall -ggdb -m32 -gstabs -nostdinc  -fno-stack-protector -Ilibs/ -Ikern/debug/ -Ikern/driver/ -Ikern/trap/ -Ikern/mm/ -c kern/libs/stdio.c -o obj/kern/libs/stdio.o
       
        gcc -Ikern/libs/ -fno-builtin -Wall -ggdb -m32 -gstabs -nostdinc  -fno-stack-protector -Ilibs/ -Ikern/debug/ -Ikern/driver/ -Ikern/trap/ -Ikern/mm/ -c kern/libs/readline.c -o obj/kern/libs/readline.o
       
        gcc -Ikern/debug/ -fno-builtin -Wall -ggdb -m32 -gstabs -nostdinc  -fno-stack-protector -Ilibs/ -Ikern/debug/ -Ikern/driver/ -Ikern/trap/ -Ikern/mm/ -c kern/debug/panic.c -o obj/kern/debug/panic.o

        gcc -Ikern/debug/ -fno-builtin -Wall -ggdb -m32 -gstabs -nostdinc  -fno-stack-protector -Ilibs/ -Ikern/debug/ -Ikern/driver/ -Ikern/trap/ -Ikern/mm/ -c kern/debug/kdebug.c -o obj/kern/debug/kdebug.o

        gcc -Ikern/debug/ -fno-builtin -Wall -ggdb -m32 -gstabs -nostdinc  -fno-stack-protector -Ilibs/ -Ikern/debug/ -Ikern/driver/ -Ikern/trap/ -Ikern/mm/ -c kern/debug/kmonitor.c -o obj/kern/debug/kmonitor.o

        gcc -Ikern/driver/ -fno-builtin -Wall -ggdb -m32 -gstabs -nostdinc  -fno-stack-protector -Ilibs/ -Ikern/debug/ -Ikern/driver/ -Ikern/trap/ -Ikern/mm/ -c kern/driver/clock.c -o obj/kern/driver/clock.o

        gcc -Ikern/driver/ -fno-builtin -Wall -ggdb -m32 -gstabs -nostdinc  -fno-stack-protector -Ilibs/ -Ikern/debug/ -Ikern/driver/ -Ikern/trap/ -Ikern/mm/ -c kern/driver/console.c -o obj/kern/driver/console.o

        gcc -Ikern/driver/ -fno-builtin -Wall -ggdb -m32 -gstabs -nostdinc  -fno-stack-protector -Ilibs/ -Ikern/debug/ -Ikern/driver/ -Ikern/trap/ -Ikern/mm/ -c kern/driver/picirq.c -o obj/kern/driver/picirq.o

        gcc -Ikern/driver/ -fno-builtin -Wall -ggdb -m32 -gstabs -nostdinc  -fno-stack-protector -Ilibs/ -Ikern/debug/ -Ikern/driver/ -Ikern/trap/ -Ikern/mm/ -c kern/driver/intr.c -o obj/kern/driver/intr.o

        gcc -Ikern/trap/ -fno-builtin -Wall -ggdb -m32 -gstabs -nostdinc  -fno-stack-protector -Ilibs/ -Ikern/debug/ -Ikern/driver/ -Ikern/trap/ -Ikern/mm/ -c kern/trap/trap.c -o obj/kern/trap/trap.o

        gcc -Ikern/trap/ -fno-builtin -Wall -ggdb -m32 -gstabs -nostdinc  -fno-stack-protector -Ilibs/ -Ikern/debug/ -Ikern/driver/ -Ikern/trap/ -Ikern/mm/ -c kern/trap/vectors.S -o obj/kern/trap/vectors.o

        gcc -Ikern/trap/ -fno-builtin -Wall -ggdb -m32 -gstabs -nostdinc  -fno-stack-protector -Ilibs/ -Ikern/debug/ -Ikern/driver/ -Ikern/trap/ -Ikern/mm/ -c kern/trap/trapentry.S -o obj/kern/trap/trapentry.o

        gcc -Ikern/mm/ -fno-builtin -Wall -ggdb -m32 -gstabs -nostdinc  -fno-stack-protector -Ilibs/ -Ikern/debug/ -Ikern/driver/ -Ikern/trap/ -Ikern/mm/ -c kern/mm/pmm.c -o obj/kern/mm/pmm.o

        gcc -Ilibs/ -fno-builtin -Wall -ggdb -m32 -gstabs -nostdinc  -fno-stack-protector -Ilibs/  -c libs/string.c -o obj/libs/string.o

        gcc -Ilibs/ -fno-builtin -Wall -ggdb -m32 -gstabs -nostdinc  -fno-stack-protector -Ilibs/  -c libs/printfmt.c -o obj/libs/printfmt.o
```
1.2 ***将1.1生成的相关目标文件使用链接器在bin目录下生成kernel核心***
> LD命令参数说明
>> - -m:使用的模拟器，可以使用ld -m -v查看列表
>> - -nostdlib:不连接系统标准启动文件和标准库文件，只把指定的文件传递给连接器。这个选项常用于编译内核、bootloader等程序，它们不需要启动文件、标准库文件
>> - -T:ld使用的脚本文件
>> - -o:产生的输出文件
>> - -e:设置启动点(入口)
>> - -N:
```
    ld -m  elf_i386 -nostdlib -T tools/kernel.ld -o bin/kernel  obj/kern/init/init.o obj/kern/libs/stdio.o obj/kern/libs/readline.o obj/kern/debug/panic.o obj/kern/debug/kdebug.o obj/kern/debug/kmonitor.o obj/kern/driver/clock.o obj/kern/driver/console.o obj/kern/driver/picirq.o obj/kern/driver/intr.o obj/kern/trap/trap.o obj/kern/trap/vectors.o obj/kern/trap/trapentry.o obj/kern/mm/pmm.o  obj/libs/string.o obj/libs/printfmt.o
```

1.3 ***编译汇编代码及boot代码生成目标文件***

```gcc -Iboot/ -fno-builtin -Wall -ggdb -m32 -gstabs -nostdinc  -fno-stack-protector -Ilibs/ -Os -nostdinc -c boot/bootasm.S -o obj/boot/bootasm.o

gcc -Iboot/ -fno-builtin -Wall -ggdb -m32 -gstabs -nostdinc  -fno-stack-protector -Ilibs/ -Os -nostdinc -c boot/bootmain.c -o obj/boot/bootmain.o

gcc -Itools/ -g -Wall -O2 -c tools/sign.c -o obj/sign/tools/sign.o

gcc -g -Wall -O2 obj/sign/tools/sign.o -o bin/sign
```
1.4 ***链接生成bootloader***

```
ld -m  elf_i386 -nostdlib -N -e start -Ttext 0x7C00 obj/boot/bootasm.o obj/boot/bootmain.o -o obj/bootblock.o
```
>'obj/bootblock.out' size: 488 bytes
build 512 bytes boot sector: 'bin/bootblock' success!

1.5 ***制作ucore.img镜像启动文件***

> dd命令选项
>> - if:
>> - of:
>> - seek:
>> - conv:

```
dd if=/dev/zero of=bin/ucore.img count=10000
创建一个ucore.img文件，有10000个块
dd if=bin/bootblock of=bin/ucore.img conv=notrunc
顺序将bootblock写入到文件中，从0块开始
dd if=bin/kernel of=bin/ucore.img seek=1 conv=notrunc
顺序写入kernel，从1块开始(seek=1就跳过了第0块)
```

2.   一个被系统认为是符合规范的硬盘主引导扇区的特征是什么？
一个引导扇区的最后两个字节是0XAA55


### 练习2：使用qemu执行并调试lab1中的软件。（要求在报告中简要写出练习过程）

为了熟悉使用qemu和gdb进行的调试工作，我们进行如下的小练习：

    1.   从CPU加电后执行的第一条指令开始，单步跟踪BIOS的执行。

    2.   在初始化位置0x7c00设置实地址断点,测试断点正常。

    3.   从0x7c00开始跟踪代码运行,将单步跟踪反汇编得到的代码与bootasm.S和bootblock.asm进行比较。

    4.   自己找一个bootloader或内核中的代码位置，设置断点并进行测试。

### 练习3：分析bootloader进入保护模式的过程。（要求在报告中写出分析）

BIOS将通过读取硬盘主引导扇区到内存，并转跳到对应内存中的位置执行bootloader。请分析bootloader是如何完成从实模式进入保护模式的。

> 提示：需要阅读小节“保护模式和分段机制”和lab1/boot/bootasm.S源码，了解如何从实模式切换到保护模式，需要了解：
> - 为何开启A20，以及如何开启A20
>> A20地址线并不是打开保护模式的关键，只是在保护模式下，不打开A20地址线，你将无法访问到所有的内存（具体参考下面的第5点）
    
    1.用于80286与8086兼容
    2.用于80286处于实模式下时，防止用户程序访问到100000h~10FFEFh之间的内存（高端内存）
    3.8086模式，A20关闭的情况下，访问超过1MB内存时，会自动回卷
    4.8086模式下，A20打开的情况下，访问超过1MB内存，就真实的访问
    5.保护模式下，A20关闭（始终为0），则用户的地址只能是：0 - (1MB-1), 2 - (3MB-1), 4 - (5MB-1)，我们可以这样设想，A20为个位数（以1MB为单位），如果它始终为0，你永远不可能让这个数变成奇数。
    6.保护模式下，A20开启，则可以访问全地址，没有奇偶MB的问题。
    7.调用BIOS中断就可以实现A20 Gate的控制功能。这个BIOS中断为 INT 15h,AX=2401h。被称为Fast A20。
    很多稀奇古怪的东西都是由于系统升级时，为了保持向下兼容而产生的，A20Gate就是其中之一。
    在8086/8088中，只有20根地址总线，所以可以访问的地址是2^20=1M，但由于8086/8088是16位地址模式，能够表示的地址范围是0-64K，所以为了在8086/8088下能够访问1M内存，Intel采取了分段的模式：16位段基地址:16位偏移。其绝对地址计算方法为：16位基地址左移4位+16位偏移=20位地址。
    但这种方式引起了新的问题，通过上述分段模式，能够表示的最大内存为：FFFFh:FFFFh=FFFF0h+FFFFh=10FFEFh=1M+64K-16Bytes（1M多余出来的部分被称做高端内存区HMA）。但8086/8088只有20位地址线，如果访问100000h~10FFEFh之间的内存，则必须有第21根地址线。所以当程序员给出超过1M（100000H-10FFEFH）的地址时，系统并不认为其访问越界而产生异常，而是自动从重新0开始计算，也就是说系统计算实际地址的时候是按照对1M求模的方式进行的，这种技术被称为wrap-around。
    到了80286，系统的地址总线发展为24根，这样能够访问的内存可以达到2^24=16M。Intel在设计80286时提出的目标是，在实模式下，系统所表现的行为应该和8086/8088所表现的完全一样，也就是说，在实模式下，80286以及后续系列，应该和8086/8088完全兼容。但最终，80286芯片却存在一个BUG：如果程序员访问100000H-10FFEFH之间的内存，系统将实际访问这块内存，而不是象过去一样重新从0开始。
    为了解决上述问题，IBM使用键盘控制器上剩余的一些输出线来管理第21根地址线（从0开始数是第20根），被称为A20Gate：如果A20 Gate被打开，则当程序员给出100000H-10FFEFH之间的地址的时候，系统将真正访问这块内存区域；如果A20Gate被禁止，则当程序员给出100000H-10FFEFH之间的地址的时候，系统仍然使用8086/8088的方式。绝大多数IBM PC兼容机默认的A20Gate是被禁止的。由于在当时没有更好的方法来解决这个问题，所以IBM使用了键盘控制器来操作A20 Gate，但这只是一种黑客行为，毕竟A20Gate和键盘操作没有任何关系。在许多新型PC上存在着一种通过芯片来直接控制A20 Gate的BIOS功能。从性能上，这种方法比通过键盘控制器来控制A20Gate要稍微高一点。
    上面所述的内存访问模式都是实模式，在80286以及更高系列的PC中，即使A20Gate被打开，在实模式下所能够访问的内存最大也只能为10FFEFH，尽管它们的地址总线所能够访问的能力都大大超过这个限制。为了能够访问10FFEFH以上的内存，则必须进入保护模式。（其实所谓的实模式，就是8086/8088的模式，这种模式存在的唯一理由就是为了让旧的程序能够继续正常的运行在新的PC体系上）
    1. A20 Gate inProtected Mode
    从80286开始，系统出现了一种新的机制，被称为保护模式。到了80386，保护模式得到了进一步的完善和发展，并且对于80386以后的芯片，保护模式的变化就非常小了。
    我们在上一节已经谈到，如果要访问更多的内存，则必须进入保护模式，那么，在保护模式下，A20Gate对于内存访问有什么影响呢？
    为了搞清楚这一点，我们先来看一看A20的工作原理。A20，从它的名字就可以看出来，其实它就是对于20-bit（从0开始数）的特殊处理(也就是对第21根地址线的处理)。如果A20Gate被禁止，对于80286来说，其地址为24bit，其地址表示为EFFFFF；对于80386极其随后的32-bit芯片来说，其地址表示为FFEFFFFF。这种表示的意思是如果A20Gate被禁止，则其第20-bit在CPU做地址访问的时候是无效的，永远只能被作为0；如果A20 Gate被打开，则其第20-bit是有效的，其值既可以是0，又可以是1。
    所以，在保护模式下，如果A20Gate被禁止，则可以访问的内存只能是奇数1M段，即1M,3M,5M…，也就是00000-FFFFF,200000-2FFFFF,300000-3FFFFF…。如果A20 Gate被打开，则可以访问的内存则是连续的。
    2. How to Enable A20Gate
    多数PC都使用键盘控制器（8042芯片）来处理A20Gate。
    从理论上讲，打开A20Gate的方法是通过设置8042芯片输出端口（64h）的2nd-bit，但事实上，当你向8042芯片输出端口进行写操作的时候，在键盘缓冲区中，或许还有别的数据尚未处理，因此你必须首先处理这些数据。


> - 如何初始化GDT表
    
    GDT意思[https://wiki.osdev.org/LGDT]

> - 如何使能和进入保护模式

``` code 
    #include <asm.h>

# Start the CPU: switch to 32-bit protected mode, jump into C.
# The BIOS loads this code from the first sector of the hard disk into
# memory at physical address 0x7c00 and starts executing in real mode
# with %cs=0 %ip=7c00.
# 代码段，数据段及CRO开启标志位
.set PROT_MODE_CSEG,        0x8                     # kernel code segment selector
.set PROT_MODE_DSEG,        0x10                    # kernel data segment selector
.set CR0_PE_ON,             0x1                     # protected mode enable flag

# start address should be 0:7c00, in real mode, the beginning address of the running bootloader
.globl start
start:
.code16                                             # Assemble for 16-bit mode
    cli                                             # Disable interrupts
    cld                                             # String operations increment

    # Set up the important data segment registers (DS, ES, SS).初始化段寄存器
    xorw %ax, %ax                                   # Segment number zero
    movw %ax, %ds                                   # -> Data Segment
    movw %ax, %es                                   # -> Extra Segment
    movw %ax, %ss                                   # -> Stack Segment

    # Enable A20:启动A20，具体参数前面A20 Gate的历史及原因
    #  For backwards compatibility with the earliest PCs, physical
    #  address line 20 is tied low, so that addresses higher than
    #  1MB wrap around to zero by default. This code undoes this.
seta20.1:
    inb $0x64, %al                                  # Wait for not busy(8042 input buffer empty).
    testb $0x2, %al
    jnz seta20.1

    movb $0xd1, %al                                 # 0xd1 -> port 0x64
    outb %al, $0x64                                 # 0xd1 means: write data to 8042's P2 port

seta20.2:
    inb $0x64, %al                                  # Wait for not busy(8042 input buffer empty).
    testb $0x2, %al
    jnz seta20.2

    movb $0xdf, %al                                 # 0xdf -> port 0x60
    outb %al, $0x60                                 # 0xdf = 11011111, means set P2's A20 bit(the 1 bit) to 1

    # Switch from real to protected mode, using a bootstrap GDT
    # and segment translation that makes virtual addresses
    # identical to physical addresses, so that the
    # effective memory map does not change during the switch.
    # 设置全局段描述表
    lgdt gdtdesc
    #设置保护模式标志位CR0为1
    movl %cr0, %eax
    orl $CR0_PE_ON, %eax
    movl %eax, %cr0

    # Jump to next instruction, but in 32-bit code segment.
    # Switches processor into 32-bit mode.
    # 切换到32位保护模式
    ljmp $PROT_MODE_CSEG, $protcseg
#由于上面的代码已经打开了保护模式了，所以这里要使用逻辑地址，而不是之前实模式的地址了。
这里用到了PROT_MODE_CSEG, 他的值是0x8。根据段选择子的格式定义，0x8就翻译成：
　　　　　　　　INDEX　　　　　　　　 TI     CPL
　　　　    0000 0000 0000 1      0      00
INDEX代表GDT中的索引，TI代表使用GDTR中的GDT， CPL代表处于特权级。
PROT_MODE_CSEG选择子选择了GDT中的第1个段描述符。这里使用的gdt就是变量gdt，下面可以看到gdt的第1个段描述符的基地址是0x0000,所以经过映射后和转换前的内存映射的物理地址一样。

.code32                                             # Assemble for 32-bit mode
protcseg:
    # Set up the protected-mode data segment registers 设置保护模式下的段表都是共用同一个段
    movw $PROT_MODE_DSEG, %ax                       # Our data segment selector
    movw %ax, %ds                                   # -> DS: Data Segment
    movw %ax, %es                                   # -> ES: Extra Segment
    movw %ax, %fs                                   # -> FS
    movw %ax, %gs                                   # -> GS
    movw %ax, %ss                                   # -> SS: Stack Segment

    # Set up the stack pointer and call into C. The stack region is from 0--start(0x7c00)
    movl $0x0, %ebp
    movl $start, %esp
    call bootmain

    # If bootmain returns (it shouldn't), loop.
spin:
    jmp spin

# Bootstrap GDT
.p2align 2                                          # force 4 byte alignment
gdt:
    SEG_NULLASM                                     # null seg
    SEG_ASM(STA_X|STA_R, 0x0, 0xffffffff)           # code seg for bootloader and kernel
    SEG_ASM(STA_W, 0x0, 0xffffffff)                 # data seg for bootloader and kernel

gdtdesc:
    .word 0x17                                      # sizeof(gdt) - 1
    .long gdt                                       # address gdt

```


### 练习4：分析bootloader加载ELF格式的OS的过程。（要求在报告中写出分析）

通过阅读bootmain.c，了解bootloader如何加载ELF文件。通过分析源代码和通过qemu运
行并调试bootloader&OS，

    1. bootloader如何读取硬盘扇区的？
    
     

    2. bootloader是如何加载ELF格式的OS？

> 提示：可阅读“硬盘访问概述”，“ELF执行文件格式概述”这两小节。

### 练习5：实现函数调用堆栈跟踪函数 （需要编程）

我们需要在lab1中完成kdebug.c中函数print_stackframe的实现，可以通过函数
print_stackframe来跟踪函数调用堆栈中记录的返回地址。在如果能够正确实现此函数，可在lab1中执行 “make qemu”后，在qemu模拟器中得到类似如下的输出：
请完成实验，看看输出是否与上述显示大致一致，并解释最后一行各个数值的含义。

>提示：可阅读小节“函数堆栈”，了解编译器如何建立函数调用关系的。在完成lab1编译后，查看lab1/obj/bootblock.asm，了解bootloader源码与机器码的语句和地址等的对应关系；查看lab1/obj/kernel.asm，了解 ucore OS源码与机器码的语句和地址等的对应关系。要求完成函数kern/debug/kdebug.c::print_stackframe的实现，提交改进后源代码包（可以编译执行），并在实验报告中简要说明实现过程，并写出对上述问题的回答。

> 补充材料：
>> 由于显示完整的栈结构需要解析内核文件中的调试符号，较为复杂和繁琐。代码中有一些辅
助函数可以使用。例如可以通过调用print_debuginfo函数完成查找对应函数名并打印至屏幕的功能。具体可以参见kdebug.c代码中的注释。

### 练习6：完善中断初始化和处理 （需要编程）
请完成编码工作和回答如下问题：
1.   中断描述符表（也可简称为保护模式下的中断向量表）中一个表项占多少字节？其中哪几位代表中断处理代码的入口？

2.   请编程完善kern/trap/trap.c中对中断向量表进行初始化的函数idt_init。在idt_init函数中，依次对所有中断入口进行初始化。使用mmu.h中的SETGATE宏，填充idt数组内容。每个中断的入口由tools/vectors.c生成，使用trap.c中声明的vectors数组即可。

3.   请编程完善trap.c中的中断处理函数trap，在对时钟中断进行处理的部分填写trap函数中处理时钟中断的部分，使操作系统每遇到100次时钟中断后，调用print_ticks子程序，向屏幕上打印一行文字”100 ticks”。




## 其它说明
### 计算机启动及BIOS功能
    1.计算机开机加电，CPU初始化及寄存器初始化，此时CPU工作在实模式下，设置CS(段寄)为0XF000,设计IP(指令指针寄存器)0XFFF0,此时PC（指令计数器）为CS*16+IP，为20位地址，因为在实模式下实际可操作址址为1MB；同时加载BIOS固件程序到此地址;
    2.开始执行BIOS,BIOS完成后从启动盘读取512字节的引导程序(Bootloader)到0x7c00(CS=0X0000,IP=0X7c00)，同时执行跳转指令到0x7c00;
    3.Bootloader从指定位置加载操作系统内核到1MB内存空间，并将控制权转移到内核。
    4.操作系统内核接管计算机，继续执行。

    
> BIOS功能
>> 1.基本的输入输出 2.中断功能 3.只能在实模式工作
 1. BIOS-MBR
 2. BIOS-GPT
 3. PXE(网络启动)

 > UEFI
    







