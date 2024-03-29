# ELF简介
    
    ELF（Executableand Linking Format 可执行和链接格式），ELF文件格式是一个开放标准,各种UNIX系统的可执行文件都采用ELF格式,它有三种不同的类型:

- 可重定位的目标文件(Relocatable)也就是通常称的目标文件，后缀为.o
- 可执行文件(Executable)
- 共享库(Shared Object)共享文件：也就是通常称的库文件，后缀为.so
> 注1: Linux中的readelf命令可以查看ELF文件的详细信息
> 注2: ELF文件只能在操作系统环境下运行,裸机环境运行的是BIN文件;编译器默认输出的文件格式是ELF格式,可以使用objcopy命令转化为BIN文件:

 /* 将name.elf转化为name.bin文件 */
 
 arm-linux-objcopy -O binary name.elf name.bin

# ELF组成

 Object文件参与程序链接(编译程序)和程序执行(运行程序)。为了方便和效率,Object文件为链接程序和执行程序分别提供了不同的视角。如下图：
 
 ![ELF格式](./elf.png)

    ELF header保存在文件最顶端它记录了整个文件的基本信息；
    Sections包含了链接视角中每个节里的信息：指令、数据、符号表、重定位信息等等；
    section header table包含描述文件节的信息，每个节对应表中的一项，包含节名、节的大小信息等等；
    segment包含了执行视角中的每个段（就是程序数据和指令）文本段、数据段等等；
    program header table描述了一个段的信息或者系统准备程序运行环境时所需要的其他信息；
## ELF header
### ELF header的数据结构：
```
typedef struct elf32_hdr{
    unsigned char e_ident[EI_NIDENT];     /* 魔数和相关信息 */
    Elf32_Half    e_type;                 /* 目标文件类型 */
    Elf32_Half    e_machine;              /* 硬件体系 */
    Elf32_Word    e_version;              /* 目标文件版本 */
    Elf32_Addr    e_entry;                /* 程序进入点 */
    Elf32_Off     e_phoff;                /* 程序头部偏移量 */
    Elf32_Off     e_shoff;                /* 节头部偏移量 */
    Elf32_Word    e_flags;                /* 处理器特定标志 */
    Elf32_Half    e_ehsize;               /* ELF头部长度 */
    Elf32_Half    e_phentsize;            /* 程序头部中一个条目的长度 */
    Elf32_Half    e_phnum;                /* 程序头部条目个数  */
    Elf32_Half    e_shentsize;            /* 节头部中一个条目的长度 */
    Elf32_Half    e_shnum;                /* 节头部条目个数 */
    Elf32_Half    e_shstrndx;             /* 节头部字符表索引 */
} Elf32_Ehdr;
```
```text
    e_ident[0~3]包含了ELF文件的魔数，依次是0x7f、’E’、’L’、’F’
    e_ident[4]表示硬件系统的位数，1代表32位，2代表64位
    e_ident[5]表示数据编码方式，1代表小端格式，2代表大端格式
    e_ident[6]指定ELF头部的版本，当前必须为1
    e_ident[7～15]是填充符，通常是0
    e_ident[16]e_ident[]数组的大小
```

    一个实际程序的ELF Header信息：
    ELF Header:
    Magic:   7f 45 4c 46 01 01 01 00 00 00 00 00 00 00 00 00 
    Class:                             ELF32
    Data:                              2's complement, little endian
    Version:                           1 (current)
    OS/ABI:                            UNIX - System V
    ABI Version:                       0
    Type:                              EXEC (Executable file)
    Machine:                           ARM
    Version:                           0x1
    Entry point address:               0x858c
    Start of program headers:          52 (bytes into file)
    Start of section headers:          4500 (bytes into file)
    Flags:                             0x5000002, has entry point, Version5 EABI
    Size of this header:               52 (bytes)
    Size of program headers:           32 (bytes)
    Number of program headers:         10
    Size of section headers:           40 (bytes)
    Number of section headers:         29
    Section header string table index: 26

    section header table
    section header的数据结构：

    typedef struct elf32_shdr {
        Elf32_Wordsh_name;   
        Elf32_Wordsh_type;
        Elf32_Wordsh_flags;
        Elf32_Addrsh_addr;
        Elf32_Off sh_offset;
        Elf32_Wordsh_size;
        Elf32_Wordsh_link;
        Elf32_Wordsh_info;
        Elf32_Wordsh_addralign;
        Elf32_Wordsh_entsize;
    } Elf32_Shdr;
    一个实际程序的section header table信息：

    Section Headers:
    [Nr] Name              Type            Addr     Off    Size   ES Flg Lk Inf Al
    [ 0]                   NULL            00000000 000000 000000 00      0   0  0
    [ 1] .interp           PROGBITS        00008174 000174 000013 00   A  0   0  1
    [ 2] .note.ABI-tag     NOTE            00008188 000188 000020 00   A  0   0  4
    [ 3] .hash             HASH            000081a8 0001a8 000054 04   A  4   0  4
    [ 4] .dynsym           DYNSYM          000081fc 0001fc 000100 10   A  5   1  4
    [ 5] .dynstr           STRTAB          000082fc 0002fc 0000ee 00   A  0   0  1
    [ 6] .gnu.version      VERSYM          000083ea 0003ea 000020 02   A  4   0  2
    [ 7] .gnu.version_r    VERNEED         0000840c 00040c 000040 00   A  5   2  4
    [ 8] .rel.dyn          REL             0000844c 00044c 000008 08   A  4   0  4
    [ 9] .rel.plt          REL             00008454 000454 000070 08   A  4  11  4
    [10] .init             PROGBITS        000084c4 0004c4 00000c 00  AX  0   0  4
    [11] .plt              PROGBITS        000084d0 0004d0 0000bc 04  AX  0   0  4
    [12] .text             PROGBITS        0000858c 00058c 0002d4 00  AX  0   0  4
    [13] .fini             PROGBITS        00008860 000860 000008 00  AX  0   0  4
    [14] .rodata           PROGBITS        00008868 000868 00001c 00   A  0   0  4
    [15] .ARM.exidx        ARM_EXIDX       00008884 000884 000008 00  AL 12   0  4
    [16] .eh_frame         PROGBITS        0000888c 00088c 000004 00   A  0   0  4
    [17] .init_array       INIT_ARRAY      00010f04 000f04 000004 00  WA  0   0  4
    [18] .fini_array       FINI_ARRAY      00010f08 000f08 000004 00  WA  0   0  4
    [19] .jcr              PROGBITS        00010f0c 000f0c 000004 00  WA  0   0  4
    [20] .dynamic          DYNAMIC         00010f10 000f10 0000f0 08  WA  5   0  4
    [21] .got              PROGBITS        00011000 001000 000048 04  WA  0   0  4
    [22] .data             PROGBITS        00011048 001048 000008 00  WA  0   0  4
    [23] .bss              NOBITS          00011050 001050 000008 00  WA  0   0  4
    [24] .ARM.attributes   ARM_ATTRIBUTES  00000000 001050 000036 00      0   0  1
    [25] .comment          PROGBITS        00000000 001086 00001b 01  MS  0   0  1
    [26] .shstrtab         STRTAB          00000000 0010a1 0000f3 00      0   0  1
    [27] .symtab           SYMTAB          00000000 00161c 000770 10     28  82  4
    [28] .strtab           STRTAB          00000000 001d8c 000399 00      0   0  1
    Key to Flags:
    W (write), A (alloc), X (execute), M (merge), S (strings)
    I (info), L (link order), G (group), T (TLS), E (exclude), x (unknown)
    O (extra OS processing required) o (OS specific), p (processor specific)
    所有section类型的说明：

    .bss该节保存着未初始化的数据，这些数据存在于程序内存映象中。通过定义，当程序开始运行，系统初始化那些数据为0。该节不占文件空间，正如它的节类型SHT_NOBITS指示的一样。

    .comment该节保存着版本控制信息。

    .data和.data1这些节保存着初始化了的数据，那些数据存在于程序内存映象中。

    .debug该节保存着为符号调试的信息。这些内容不明确。

    .dynamic该节保存着动态连接的信息。SHF_ALLOC位指定该节的属性。设置SHF_WRITE位时表示是处理器特定的。

    .dynstr该节保存着动态连接时需要的字符串，一般情况下表示和一个符号表项相对应的名字

    .dynsym该节保存着动态链接符号表，如Symbol Table的描述。

    .fini该节保存着可执行指令，它包含了进程的终止代码。当一个程序正常退出时，系统安排执行这个节的中的代码。

    .got该节保存着全局的偏移量表。

    .hash该节保存着符号哈希表。

    .init该节保存着可执行指令，它构成了进程的初始化代码。当一个程序开始运行时，在main函数被调用之前(c语言中称为main)，系统安排执行这个节的中的代码。

    .interp该节保存了程序解释器(program interpreter)的路径。假如在这个节中有一个可装载的节，那么该节属性的SHF_ALLOC位将被设置；否则，该位不会被设置。

    .line该节包含源文件中的行数信息用于符号调试,它描述源程序与机器代码之间的对应关系。该节内容不明确的。

    .note该节保存Part 2讨论的Note Section节的格式信息。

    .plt该节保存过程链接表（Procedure Linkage Table）。

    .rel<name>和.rela<name>这些节保存着重定位的信息。如果文件包含了一个可装载的节需要重定位，那么该节属性中的SHF_ALLOC位会被设置；否则该位被关闭。通常，由需要重定的那个节来提供。比如一个需要重定位的节`.text`，那么该节名字为`.rel.text`或者`.rela.text`。

    .rodata和.rodata1这些节保存着只读数据，放在进程映象中的只读节。

    .shstrtab该节保存着节名称。

    .strtab该节保存着字符串，一般表示和一个符号表项相对应的名字。假如文件有一个可装载的节，并且该节包括了符号字符表，那么该节属性的SHF_ALLOC位将被设置；否则不设置。

    .symtab该节保存着一个符号表。假如文件有一个可装载的节，并且该节包含了符号表，那么该节属性的SHF_ALLOC位将被设置；否则不设置。

    .text该节保存着程序的text或者说是程序的可执行指令。

    Symbol Table
    符号表保存着程序定义和引用的符号（全局变量和函数）信息。一个符号表的索引是数组的下标。第0项既指定这个表的起始项也作为未定义符号的索引。

    Symbol Table的数据结构：

    typedef struct elf32_sym{
        Elf32_Wordst_name;
        Elf32_Addrst_value;
        Elf32_Wordst_size;
        unsigned char st_info;
        unsigned char st_other;
        Elf32_Halfst_shndx;
    } Elf32_Sym;
    一个实际程序的Symbol Table信息：

    Symbol table '.dynsym' contains 16 entries:
    Num:    Value  Size Type    Bind   Vis      Ndx Name
        0: 00000000     0 NOTYPE  LOCAL  DEFAULT  UND 
        1: 000084e4     0 FUNC    GLOBAL DEFAULT  UND abort@GLIBC_2.4 (2)
        2: 000084f0     0 FUNC    GLOBAL DEFAULT  UND pthread_exit@GLIBC_2.4 (3)
        3: 000084fc     0 FUNC    GLOBAL DEFAULT  UND __libc_start_main@GLIBC_2.4 (2)
        4: 00000000     0 NOTYPE  WEAK   DEFAULT  UND __gmon_start__
        5: 00000000     0 NOTYPE  WEAK   DEFAULT  UND _Jv_RegisterClasses
        6: 00008514     0 FUNC    GLOBAL DEFAULT  UND fclose@GLIBC_2.4 (2)
        7: 00008520     0 FUNC    GLOBAL DEFAULT  UND fopen@GLIBC_2.4 (2)
        8: 0000852c     0 FUNC    GLOBAL DEFAULT  UND pthread_getspecific@GLIBC_2.4 (3)
        9: 00008538     0 FUNC    GLOBAL DEFAULT  UND pthread_create@GLIBC_2.4 (3)
        10: 00008544     0 FUNC    GLOBAL DEFAULT  UND pthread_setspecific@GLIBC_2.4 (3)
        11: 00008550     0 FUNC    GLOBAL DEFAULT  UND fprintf@GLIBC_2.4 (2)
        12: 0000855c     0 FUNC    GLOBAL DEFAULT  UND pthread_self@GLIBC_2.4 (3)
        13: 00008568     0 FUNC    GLOBAL DEFAULT  UND sprintf@GLIBC_2.4 (2)
        14: 00008574     0 FUNC    GLOBAL DEFAULT  UND pthread_join@GLIBC_2.4 (3)
        15: 00008580     0 FUNC    GLOBAL DEFAULT  UND pthread_key_create@GLIBC_2.4 (3)
    Program Header table
    program header的数据结构：

    typedef struct elf32_phdr{
    Elf32_Word  p_type;        /* 段类型 */
    Elf32_Off   p_offset;      /* 段位置相对于文件开始处的偏移量 */
    Elf32_Addr  p_vaddr;       /* 段在内存中的地址 */
    Elf32_Addr  p_paddr;       /* 段的物理地址 */
    Elf32_Word  p_filesz;      /* 段在文件中的长度 */
    Elf32_Word  p_memsz;       /* 段在内存中的长度 */
    Elf32_Word  p_flags;       /* 段的标记 */
    Elf32_Word  p_align;       /* 段在内存中对齐标记 */
    } Elf32_Phdr;
    一个实际程序的program header table信息：

    Program Headers:
    Type           Offset   VirtAddr   PhysAddr   FileSiz MemSiz  Flg Align
    EXIDX          0x000884 0x00008884 0x00008884 0x00008 0x00008 R   0x4
    PHDR           0x000034 0x00008034 0x00008034 0x00140 0x00140 R E 0x4
    INTERP         0x000174 0x00008174 0x00008174 0x00013 0x00013 R   0x1
        [Requesting program interpreter: /lib/ld-linux.so.3]
    LOAD           0x000000 0x00008000 0x00008000 0x00890 0x00890 R E 0x8000
    LOAD           0x000f04 0x00010f04 0x00010f04 0x0014c 0x00154 RW  0x8000
    DYNAMIC        0x000f10 0x00010f10 0x00010f10 0x000f0 0x000f0 RW  0x4
    NOTE           0x000188 0x00008188 0x00008188 0x00020 0x00020 R   0x4
    GNU_STACK      0x000000 0x00000000 0x00000000 0x00000 0x00000 RWE 0x4
    GNU_RELRO      0x000f04 0x00010f04 0x00010f04 0x000fc 0x000fc R   0x1
    LOOS+5041580   0x000000 0x00000000 0x00000000 0x00000 0x00000     0x4