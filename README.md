# elf-structure

**main.cpp:**
```shell
int add(int a, int b);

int main() {
    int x = add(10, 20);
    return add(x, 20);
}
```
**libmath.cpp:**
```shell
int add(int a, int b) {
    return a+b;
}
```

**Compilation:**
```shell
g++ --shared -o libmath.so -fPIC libmath.cpp
g++ main.cpp -Wl,-rpath,. -L. -lmath -o main
```

```shell
readelf --all -W --demangle main
```

```shell
ELF Header:
  Magic:   7f 45 4c 46 02 01 01 00 00 00 00 00 00 00 00 00 
  Class:                             ELF64
  Data:                              2's complement, little endian
  Version:                           1 (current)
  OS/ABI:                            UNIX - System V
  ABI Version:                       0
  Type:                              DYN (Position-Independent Executable file)
  Machine:                           Advanced Micro Devices X86-64
  Version:                           0x1
  Entry point address:               0x1060
  Start of program headers:          64 (bytes into file)
  Start of section headers:          13968 (bytes into file)
  Flags:                             0x0
  Size of this header:               64 (bytes)
  Size of program headers:           56 (bytes)
  Number of program headers:         13
  Size of section headers:           64 (bytes)
  Number of section headers:         31
  Section header string table index: 30

Section Headers:
  [Nr] Name              Type            Address          Off    Size   ES Flg Lk Inf Al
  [ 0]                   NULL            0000000000000000 000000 000000 00      0   0  0
  [ 1] .interp           PROGBITS        0000000000000318 000318 00001c 00   A  0   0  1
    Contains the string /lib64/ld-linux-x86-64.so.2  - try readelf -x .interp main-lazy
  [ 2] .note.gnu.property NOTE            0000000000000338 000338 000030 00   A  0   0  8
    Don't know what this is yet
  [ 3] .note.gnu.build-id NOTE            0000000000000368 000368 000024 00   A  0   0  4
    Don't know what this is yet
  [ 4] .note.ABI-tag     NOTE            000000000000038c 00038c 000020 00   A  0   0  4
    Don't know what this is yet
  [ 5] .gnu.hash         GNU_HASH        00000000000003b0 0003b0 000024 00   A  6   0  8
    Don't know what this is yet
  [ 6] .dynsym           DYNSYM          00000000000003d8 0003d8 0000a8 18   A  7   1  8
    This is a symbol table which contains all the symbols in the program that are undefined. This is a subset of .symtab
  [ 7] .dynstr           STRTAB          0000000000000480 000480 00009e 00   A  0   0  1
    This is a string table which contains list of string referred to by .dynsym and .dynamic sections. This contains entry repeated from .strtab. i.e. same name are also present in .strtab
  [ 8] .gnu.version      VERSYM          000000000000051e 00051e 00000e 02   A  6   0  2
    Don't know what this is yet
  [ 9] .gnu.version_r    VERNEED         0000000000000530 000530 000030 00   A  7   1  8
    Don't know what this is yet
  [10] .rela.dyn         RELA            0000000000000560 000560 0000c0 18   A  6   0  8
    This is a relocation table which is a subset of .dynsym. It contains records that are dynamically resolved by runtime linker using a mechanism I don't know yet. Addresses for symbols in this table don't seem to be loading lazily, rather they are getting loaded at load time itself. Even if you specity -Wl,-z,lazy. One of the symbols in this section __cxa_finalize appears in .plt.got section of code. Its instructions are very similar to .plt.sec section code which is actually used for lazy loading. But code in .plt.got does not do lazy loading.
  [11] .rela.plt         RELA            0000000000000620 000620 000018 18  AI  6  24  8
    This is a relocation table which is a subset of .dynsym. It contains record that can be lazily loaded or loaded at start depending on flag. This i understand. It loads corresponding entry from GOT and jumps to that instuction. If the symbol is loaded, it goes to corresponding instruction of the loaded function, if not it jumps to corresponding entry in .plt section. -Wl,-z,lazy affects this.
  [12] .init             PROGBITS        0000000000001000 001000 00001b 00  AX  0   0  4
    Don't know what this is yet
  [13] .plt              PROGBITS        0000000000001020 001020 000020 10  AX  0   0 16
    It contains code for jumpling to dynamic linker. It has similar instructions for each symbol in .rela.plt, that ultimately jumps to dynamic linker
  [14] .plt.got          PROGBITS        0000000000001040 001040 000010 10  AX  0   0 16
    This section contains code for .rela.dyn symbols. Refer its description
  [15] .plt.sec          PROGBITS        0000000000001050 001050 000010 10  AX  0   0 16
    This section contains code for .rela.plt symbols. Refer its description
  [16] .text             PROGBITS        0000000000001060 001060 000119 00  AX  0   0 16
    Acutal code for program
  [17] .fini             PROGBITS        000000000000117c 00117c 00000d 00  AX  0   0  4
    Don't know what this is yet
  [18] .rodata           PROGBITS        0000000000002000 002000 000004 04  AM  0   0  4
    Read only data in the program. Refer .data section description
  [19] .eh_frame_hdr     PROGBITS        0000000000002004 002004 000034 00   A  0   0  4
    Don't know what this is yet
  [20] .eh_frame         PROGBITS        0000000000002038 002038 0000ac 00   A  0   0  8
    Don't know what this is yet
  [21] .init_array       INIT_ARRAY      0000000000003d98 002d98 000008 08  WA  0   0  8
    Contains list of addresses of functions that needs to be called to initialize global variables before the control is handled to main. One for each translation unit statically linked.
  [22] .fini_array       FINI_ARRAY      0000000000003da0 002da0 000008 08  WA  0   0  8
    Contains list of addresses of functions that needs to be called before program exits. One for each translation unit statically linked.
  [23] .dynamic          DYNAMIC         0000000000003da8 002da8 000210 10  WA  7   0  8
    Table of structures that guide the dynamic properties of this program. For example: rpath, list of dynamically linked libraries, bind now vs bind lazily. These 2 i understand rest I don't
  [24] .got              PROGBITS        0000000000003fb8 002fb8 000048 08  WA  0   0  8
    Global offset table that contains the mapping of all the undefined and dynamically linked symbols: variables and functions. All variables are bound at load time. Functions we can choose to bind lazily or at load time using flag: -Wl,-z,now or -Wl,-z,lazy. Instant binding by default if you don't specify anything. man page of ld says otherwise
  [25] .data             PROGBITS        0000000000004000 003000 000010 00  WA  0   0  8
    Contains the Initialized global variables. For example int a = 10 in globa scope, Here 10 will go in .data. But int a = 0, it will go in .bss. What goes where amonst .bss and .data and .rodata is to the complier for great extent.
  [26] .bss              NOBITS          0000000000004010 003010 000008 00  WA  0   0  1
    Contains uninitialized global primitive vairiables and zero initialized global primitive variables and gloablly initilized object variables. .bss is guaranteed to be initilized to zero by loader. For initialized global objects: objects are constructed even before main is caled. see description of .init_array. My hunch is Local staic variables should also be stored this way. But I am not sure. .bss section does not utilize any space on the executable file and is only initialized to zero by loader. An optimization to save disk space. 
  [27] .comment          PROGBITS        0000000000000000 003010 00002b 01  MS  0   0  1
    Don't know what this is yet
  [28] .symtab           SYMTAB          0000000000000000 003040 000360 18     29  18  8
    This is the table which contains all the defined and undefined symbols in the program and infromation about them.
  [29] .strtab           STRTAB          0000000000000000 0033a0 0001d4 00      0   0  1
    This table contains the names of all the symbols contained in the program. Referred by .symtab
  [30] .shstrtab         STRTAB          0000000000000000 003574 00011a 00      0   0  1
    Section Header string table. This table contains names of all the sections that are in the program. 
Key to Flags:
  W (write), A (alloc), X (execute), M (merge), S (strings), I (info),
  L (link order), O (extra OS processing required), G (group), T (TLS),
  C (compressed), x (unknown), o (OS specific), E (exclude),
  D (mbind), l (large), p (processor specific)

There are no section groups in this file.

Program Headers:
  Type           Offset   VirtAddr           PhysAddr           FileSiz  MemSiz   Flg Align
  PHDR           0x000040 0x0000000000000040 0x0000000000000040 0x0002d8 0x0002d8 R   0x8
    Don't know what this is yet
  INTERP         0x000318 0x0000000000000318 0x0000000000000318 0x00001c 0x00001c R   0x1
      [Requesting program interpreter: /lib64/ld-linux-x86-64.so.2]
  LOAD           0x000000 0x0000000000000000 0x0000000000000000 0x000638 0x000638 R   0x1000
    It says to load x638 bytes from file offset 0x0 at virtaual memory 0x0
  LOAD           0x001000 0x0000000000001000 0x0000000000001000 0x000189 0x000189 R E 0x1000
    It says to load x189 bytes from file offset 0x1000 at virtaual memory 0x1000
  LOAD           0x002000 0x0000000000002000 0x0000000000002000 0x0000e4 0x0000e4 R   0x1000
    It says to load x4e bytes from file offset 0x2000 at virtaual memory 0x2000
  LOAD           0x002d98 0x0000000000003d98 0x0000000000003d98 0x000278 0x000280 RW  0x1000
    It says to load x278 bytes from file offset 0x29d8 at virtaual memory 0x39d8. Not that it says to allot 0x280 bytes insted to 0x278. Its because this program header maps .bss as well. .bss does not use space in file but allocates space in program runtime memory.
  DYNAMIC        0x002da8 0x0000000000003da8 0x0000000000003da8 0x000210 0x000210 RW  0x8
    Don't know what this is yet
  NOTE           0x000338 0x0000000000000338 0x0000000000000338 0x000030 0x000030 R   0x8
  NOTE           0x000368 0x0000000000000368 0x0000000000000368 0x000044 0x000044 R   0x4
    Don't know what this is yet
  GNU_PROPERTY   0x000338 0x0000000000000338 0x0000000000000338 0x000030 0x000030 R   0x8
    Don't know what this is yet
  GNU_EH_FRAME   0x002004 0x0000000000002004 0x0000000000002004 0x000034 0x000034 R   0x4
    Don't know what this is yet
  GNU_STACK      0x000000 0x0000000000000000 0x0000000000000000 0x000000 0x000000 RW  0x10
    Don't know what this is yet
  GNU_RELRO      0x002d98 0x0000000000003d98 0x0000000000003d98 0x000268 0x000268 R   0x1
    Sets a flag that 0x268 bytes from 0x0000000000003d98 should be made readonly after the loading is done. Security mechanism to make the .init_array, .fini_array .dynamic and .got read only after program is loaded. can be disabled by -Wl,-z,norelro. See the section to segment mapping index 05

 Section to Segment mapping:
  Segment Sections...
   00     
   01     .interp 
   02     .interp .note.gnu.property .note.gnu.build-id .note.ABI-tag .gnu.hash .dynsym .dynstr .gnu.version .gnu.version_r .rela.dyn .rela.plt 
   03     .init .plt .plt.got .plt.sec .text .fini 
   04     .rodata .eh_frame_hdr .eh_frame 
   05     .init_array .fini_array .dynamic .got .data .bss 
   06     .dynamic 
   07     .note.gnu.property 
   08     .note.gnu.build-id .note.ABI-tag 
   09     .note.gnu.property 
   10     .eh_frame_hdr 
   11     
   12     .init_array .fini_array .dynamic .got 

Dynamic section at offset 0x2da8 contains 29 entries:
  Tag        Type                         Name/Value
 0x0000000000000001 (NEEDED)             Shared library: [libmath.so]
 0x0000000000000001 (NEEDED)             Shared library: [libc.so.6]
 0x000000000000001d (RUNPATH)            Library runpath: [.]
 0x000000000000000c (INIT)               0x1000
 0x000000000000000d (FINI)               0x117c
 0x0000000000000019 (INIT_ARRAY)         0x3d98
 0x000000000000001b (INIT_ARRAYSZ)       8 (bytes)
 0x000000000000001a (FINI_ARRAY)         0x3da0
 0x000000000000001c (FINI_ARRAYSZ)       8 (bytes)
 0x000000006ffffef5 (GNU_HASH)           0x3b0
 0x0000000000000005 (STRTAB)             0x480
 0x0000000000000006 (SYMTAB)             0x3d8
 0x000000000000000a (STRSZ)              158 (bytes)
 0x000000000000000b (SYMENT)             24 (bytes)
 0x0000000000000015 (DEBUG)              0x0
 0x0000000000000003 (PLTGOT)             0x3fb8
 0x0000000000000002 (PLTRELSZ)           24 (bytes)
 0x0000000000000014 (PLTREL)             RELA
 0x0000000000000017 (JMPREL)             0x620
 0x0000000000000007 (RELA)               0x560
 0x0000000000000008 (RELASZ)             192 (bytes)
 0x0000000000000009 (RELAENT)            24 (bytes)
 0x000000000000001e (FLAGS)              BIND_NOW
 0x000000006ffffffb (FLAGS_1)            Flags: NOW PIE
 0x000000006ffffffe (VERNEED)            0x530
 0x000000006fffffff (VERNEEDNUM)         1
 0x000000006ffffff0 (VERSYM)             0x51e
 0x000000006ffffff9 (RELACOUNT)          3
 0x0000000000000000 (NULL)               0x0

Relocation section '.rela.dyn' at offset 0x560 contains 8 entries:
    Offset             Info             Type               Symbol's Value  Symbol's Name + Addend
0000000000003d98  0000000000000008 R_X86_64_RELATIVE                         1140
0000000000003da0  0000000000000008 R_X86_64_RELATIVE                         1100
0000000000004008  0000000000000008 R_X86_64_RELATIVE                         4008
0000000000003fd8  0000000100000006 R_X86_64_GLOB_DAT      0000000000000000 __libc_start_main@GLIBC_2.34 + 0
0000000000003fe0  0000000200000006 R_X86_64_GLOB_DAT      0000000000000000 _ITM_deregisterTMCloneTable + 0
0000000000003fe8  0000000400000006 R_X86_64_GLOB_DAT      0000000000000000 __gmon_start__ + 0
0000000000003ff0  0000000500000006 R_X86_64_GLOB_DAT      0000000000000000 _ITM_registerTMCloneTable + 0
0000000000003ff8  0000000600000006 R_X86_64_GLOB_DAT      0000000000000000 __cxa_finalize@GLIBC_2.2.5 + 0

Relocation section '.rela.plt' at offset 0x620 contains 1 entry:
    Offset             Info             Type               Symbol's Value  Symbol's Name + Addend
0000000000003fd0  0000000300000007 R_X86_64_JUMP_SLOT     0000000000000000 add(int, int) + 0
No processor specific unwind information to decode

Symbol table '.dynsym' contains 7 entries:
   Num:    Value          Size Type    Bind   Vis      Ndx Name
     0: 0000000000000000     0 NOTYPE  LOCAL  DEFAULT  UND 
     1: 0000000000000000     0 FUNC    GLOBAL DEFAULT  UND __libc_start_main@GLIBC_2.34 (2)
     2: 0000000000000000     0 NOTYPE  WEAK   DEFAULT  UND _ITM_deregisterTMCloneTable
     3: 0000000000000000     0 FUNC    GLOBAL DEFAULT  UND add(int, int)
     4: 0000000000000000     0 NOTYPE  WEAK   DEFAULT  UND __gmon_start__
     5: 0000000000000000     0 NOTYPE  WEAK   DEFAULT  UND _ITM_registerTMCloneTable
     6: 0000000000000000     0 FUNC    WEAK   DEFAULT  UND __cxa_finalize@GLIBC_2.2.5 (3)

Symbol table '.symtab' contains 36 entries:
   Num:    Value          Size Type    Bind   Vis      Ndx Name
     0: 0000000000000000     0 NOTYPE  LOCAL  DEFAULT  UND 
     1: 0000000000000000     0 FILE    LOCAL  DEFAULT  ABS Scrt1.o
     2: 000000000000038c    32 OBJECT  LOCAL  DEFAULT    4 __abi_tag
     3: 0000000000000000     0 FILE    LOCAL  DEFAULT  ABS crtstuff.c
     4: 0000000000001090     0 FUNC    LOCAL  DEFAULT   16 deregister_tm_clones
     5: 00000000000010c0     0 FUNC    LOCAL  DEFAULT   16 register_tm_clones
     6: 0000000000001100     0 FUNC    LOCAL  DEFAULT   16 __do_global_dtors_aux
     7: 0000000000004010     1 OBJECT  LOCAL  DEFAULT   26 completed.0
     8: 0000000000003da0     0 OBJECT  LOCAL  DEFAULT   22 __do_global_dtors_aux_fini_array_entry
     9: 0000000000001140     0 FUNC    LOCAL  DEFAULT   16 frame_dummy
    10: 0000000000003d98     0 OBJECT  LOCAL  DEFAULT   21 __frame_dummy_init_array_entry
    11: 0000000000000000     0 FILE    LOCAL  DEFAULT  ABS main.cpp
    12: 0000000000000000     0 FILE    LOCAL  DEFAULT  ABS crtstuff.c
    13: 00000000000020e0     0 OBJECT  LOCAL  DEFAULT   20 __FRAME_END__
    14: 0000000000000000     0 FILE    LOCAL  DEFAULT  ABS 
    15: 0000000000003da8     0 OBJECT  LOCAL  DEFAULT   23 _DYNAMIC
    16: 0000000000002004     0 NOTYPE  LOCAL  DEFAULT   19 __GNU_EH_FRAME_HDR
    17: 0000000000003fb8     0 OBJECT  LOCAL  DEFAULT   24 _GLOBAL_OFFSET_TABLE_
    18: 0000000000000000     0 FUNC    GLOBAL DEFAULT  UND __libc_start_main@GLIBC_2.34
    19: 0000000000000000     0 NOTYPE  WEAK   DEFAULT  UND _ITM_deregisterTMCloneTable
    20: 0000000000004000     0 NOTYPE  WEAK   DEFAULT   25 data_start
    21: 0000000000000000     0 FUNC    GLOBAL DEFAULT  UND add(int, int)
    22: 0000000000004010     0 NOTYPE  GLOBAL DEFAULT   25 _edata
    23: 000000000000117c     0 FUNC    GLOBAL HIDDEN    17 _fini
    24: 0000000000004000     0 NOTYPE  GLOBAL DEFAULT   25 __data_start
    25: 0000000000000000     0 NOTYPE  WEAK   DEFAULT  UND __gmon_start__
    26: 0000000000004008     0 OBJECT  GLOBAL HIDDEN    25 __dso_handle
    27: 0000000000002000     4 OBJECT  GLOBAL DEFAULT   18 _IO_stdin_used
    28: 0000000000004018     0 NOTYPE  GLOBAL DEFAULT   26 _end
    29: 0000000000001060    38 FUNC    GLOBAL DEFAULT   16 _start
    30: 0000000000004010     0 NOTYPE  GLOBAL DEFAULT   26 __bss_start
    31: 0000000000001149    48 FUNC    GLOBAL DEFAULT   16 main
    32: 0000000000004010     0 OBJECT  GLOBAL HIDDEN    25 __TMC_END__
    33: 0000000000000000     0 NOTYPE  WEAK   DEFAULT  UND _ITM_registerTMCloneTable
    34: 0000000000000000     0 FUNC    WEAK   DEFAULT  UND __cxa_finalize@GLIBC_2.2.5
    35: 0000000000001000     0 FUNC    GLOBAL HIDDEN    12 _init

Histogram for `.gnu.hash' bucket list length (total of 2 buckets):
 Length  Number     % of total  Coverage
      0  1          ( 50.0%)
      1  1          ( 50.0%)    100.0%

Version symbols section '.gnu.version' contains 7 entries:
 Addr: 0x000000000000051e  Offset: 0x00051e  Link: 6 (.dynsym)
  000:   0 (*local*)       2 (GLIBC_2.34)    1 (*global*)      1 (*global*)   
  004:   1 (*global*)      1 (*global*)      3 (GLIBC_2.2.5)

Version needs section '.gnu.version_r' contains 1 entry:
 Addr: 0x0000000000000530  Offset: 0x000530  Link: 7 (.dynstr)
  000000: Version: 1  File: libc.so.6  Cnt: 2
  0x0010:   Name: GLIBC_2.2.5  Flags: none  Version: 3
  0x0020:   Name: GLIBC_2.34  Flags: none  Version: 2

Displaying notes found in: .note.gnu.property
  Owner                Data size 	Description
  GNU                  0x00000020	NT_GNU_PROPERTY_TYPE_0	      Properties: x86 feature: IBT, SHSTK, x86 ISA needed: x86-64-baseline

Displaying notes found in: .note.gnu.build-id
  Owner                Data size 	Description
  GNU                  0x00000014	NT_GNU_BUILD_ID (unique build ID bitstring)	    Build ID: 338d16cbdc8b971fe7694c0f446de996d922d824

Displaying notes found in: .note.ABI-tag
  Owner                Data size 	Description
  GNU                  0x00000010	NT_GNU_ABI_TAG (ABI version tag)	    OS: Linux, ABI: 3.2.0
```


