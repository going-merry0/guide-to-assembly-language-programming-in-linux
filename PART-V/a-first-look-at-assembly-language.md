- [简介 (Introduction)](#%E7%AE%80%E4%BB%8B-introduction)
- [数据分配 (Data Allocation)](#%E6%95%B0%E6%8D%AE%E5%88%86%E9%85%8D-data-allocation)
  - [2的补数 (2's complement)](#2%E7%9A%84%E8%A1%A5%E6%95%B0-2s-complement)
  - [未初始化的数据 (Uninitialized Data)](#%E6%9C%AA%E5%88%9D%E5%A7%8B%E5%8C%96%E7%9A%84%E6%95%B0%E6%8D%AE-uninitialized-data)
  - [Multiple Definitions](#multiple-definitions)
  - [Multiple Initializations](#multiple-initializations)
  - [Symbol Table](#symbol-table)
- [操作数在哪里 (Where Are the Operands)](#%E6%93%8D%E4%BD%9C%E6%95%B0%E5%9C%A8%E5%93%AA%E9%87%8C-where-are-the-operands)
  - [寄存器寻址模式 (Register Addressing Mode)](#%E5%AF%84%E5%AD%98%E5%99%A8%E5%AF%BB%E5%9D%80%E6%A8%A1%E5%BC%8F-register-addressing-mode)
  - [立即寻址模式 (Immediate Addressing Mode)](#%E7%AB%8B%E5%8D%B3%E5%AF%BB%E5%9D%80%E6%A8%A1%E5%BC%8F-immediate-addressing-mode)
  - [直接内存寻址模式 (Direct Addressing Mode)](#%E7%9B%B4%E6%8E%A5%E5%86%85%E5%AD%98%E5%AF%BB%E5%9D%80%E6%A8%A1%E5%BC%8F-direct-addressing-mode)
  - [间接内存寻址模式 (Indirect Addressing Mode)](#%E9%97%B4%E6%8E%A5%E5%86%85%E5%AD%98%E5%AF%BB%E5%9D%80%E6%A8%A1%E5%BC%8F-indirect-addressing-mode)
- [汇编语言指令概览 (Overview of Assembly Language Instructions)](#%E6%B1%87%E7%BC%96%E8%AF%AD%E8%A8%80%E6%8C%87%E4%BB%A4%E6%A6%82%E8%A7%88-overview-of-assembly-language-instructions)
  - [MOV 指令](#mov-%E6%8C%87%E4%BB%A4)
  - [有歧义的移动 (Ambiguous Moves)](#%E6%9C%89%E6%AD%A7%E4%B9%89%E7%9A%84%E7%A7%BB%E5%8A%A8-ambiguous-moves)
  - [简单的算术指令 (Simple Arithmetic Instructions)](#%E7%AE%80%E5%8D%95%E7%9A%84%E7%AE%97%E6%9C%AF%E6%8C%87%E4%BB%A4-simple-arithmetic-instructions)
    - [INC 和 DEC 指令](#inc-%E5%92%8C-dec-%E6%8C%87%E4%BB%A4)
    - [ADD 指令](#add-%E6%8C%87%E4%BB%A4)
    - [SUB 和 CMP 指令](#sub-%E5%92%8C-cmp-%E6%8C%87%E4%BB%A4)
  - [条件执行 (Conditional Execution)](#%E6%9D%A1%E4%BB%B6%E6%89%A7%E8%A1%8C-conditional-execution)
  - [无条件跳转 (Unconditional Jump)](#%E6%97%A0%E6%9D%A1%E4%BB%B6%E8%B7%B3%E8%BD%AC-unconditional-jump)
  - [条件跳转 (Conditional Jump)](#%E6%9D%A1%E4%BB%B6%E8%B7%B3%E8%BD%AC-conditional-jump)
  - [迭代指令 (Iteration Instruction)](#%E8%BF%AD%E4%BB%A3%E6%8C%87%E4%BB%A4-iteration-instruction)
  - [逻辑指令 (Logic Instructions)](#%E9%80%BB%E8%BE%91%E6%8C%87%E4%BB%A4-logic-instructions)

这一节的目的就是简单的介绍一下汇编语言的基础。汇编语言的语句可以是处理器指令、命令处理器执行一个任务，也可以是在汇编过程中控制汇编器的指令。第三种指令的形式就是宏指令(macros)，将在下一讲中讨论。

汇编器提供了很多 指令(directives) 来为变量预留存储空间。将会详细讨论这些指令。处理器的一些 指令(instructions) 中包含了执行所需的操作类型，以及操作数的规格。将在 [Chapter 13](./addressing-modes.md) 中讨论。

## 简介 (Introduction)

汇编语言具有 3种 不同的语句类型。第一种类型就是操作处理器的。这种类型的语句称为 可执行指令(executable instructions) 或者简称 指令(instruction)。每个可执行指令都包含一个 操作码(operation code) 简称 opcode。可执行指令将会促使汇编器生成对应的机器指令。

第二种语句用于在汇编过程中为汇编器提供不同的信息。这些指令称为 汇编器指令(assembler directives) 或者 伪操作码(pseudo-ops)。汇编器指令不是可执行指令，因此不会生成任何机器码。

最后一种类型称为 宏(macros)，提供一个成熟的文本替换机制。下一讲中将会讨论它。

汇编语言的每一条语句都占源码中的一行。所有这3中类型的语句，都具有下面一样相同的格式:

```asm
[label]  mnemonic  [operands] [;comment]
```

方括号括起来的字段表示它在语句中是可选的。通常为了可读性，都会将字段进行对齐，但是汇编器并不在意字段之间的空格。

看一个具体的例子:

```asm
repeat:  inc  result  ;increment result by 1
```

标注(label) repeat 可以用来引用该语句。助记词 inc 用来表示该语句的操作希望将制定内存 result 中的数据加 1。某些保留字对于汇编器而言含有特殊的意义，因此不能作为标注的名称，比如这里的助记词 inc。

语句中的字段必须至少被一个制表符或者空格分隔，多于的空白由编程人员使用，汇编器并不在意它们。

最佳编程实践是使用一个空行和空白来提高程序的可读性，比如:

```asm
repeat:
    inc  result  ;increment result by 1
```

## 数据分配 (Data Allocation)

在高级语言中，变量的存储分配只需要直接指定变量的类型即可。比如 C 语言中:

```c
char response;    /* allocates 1 byte */
int value;        /* allocates 4 bytes */
float total;      /* allocates 4 bytes */
double temp;      /* allocates 8 bytes */
```

这个变量的声明，不光指定了需要多少的存储空间，还表示了存储后的 bit 位该如何被解析。比如下面的 C 语言例子:

```c
unsigned value_1;
int      value_2;
```

两个变量都使用 4字节 的存储空间。不过存储于它们之上的 bit 位具有不同的意义。比如 (0x8FF08DB9):

```
1000 1111 1111 0000 1000 1101 1011 1001
```

如果保存在 value_1 上的话，表示 `+2.4149 x 10^9`，同样的内容保存在 value_2 上的话，表示 `-1.88006 x 10^9`。

在汇编语言中，存储的分配是通过 汇编器指令(assembler directives) 完成的。define 指令可以用于预留和初始化一个或者多个字节。不过没有类似 C 语言变量声明的解析语义附加到其中。对于存储的数据如何解析完全取决于程序。

一般的用于初始化数据的存储分配语句如下:

```asm
[variable-name]  define-directive  initial-value  [,initial-value], ...
```

方括号括起来的部分表示该字段是可选的。variable-name 用于标识所分配的存储空间。汇编为定义在数据段的每一个变量名关联了一个偏移量。注意变量名之后没有冒号(:)。

define directives 具有下面五种形式:

```
DB  Define byte          ;allocates 1 byte
DW  Define Word          ;allocates 2 bytes      
DD  Define Doubleword    ;allocates 4 bytes
DQ  Define Quadword      ;allocates 8 bytes
DT  Define Ten Bytes     ;allocates 10 bytes
```

看下例子:

```asm
sorted  DB  'y'
```

这个语句分配了一个字节的存储，并将该存储空间的内容初始化为字符 y。我们的汇编语言程序可以通过变量名 sorted 来引用到该字节。我们也可以使用数字来进行初始化:

```asm
sorted  DB  79H
```

```asm
sorted  DB  1111001B
```

上面这三条语句的含义相同。

下面的语句分配和初始化了两个字节：

```asm
value  DW  25159
```

十进制 25159 将自动被转换为等价的 16进制数 (6247H)。因为处理器使用的是小端序，所以 16位的数在内存中的形式为:

```
 address:  x   x+1
contents:  47  62
```

也可以使用负数值:

```asm
balance  DW  -29255
```

因为在计算机中，负数是以其2的补数的形式存在的，-29255 被转换为 8DB8H：

```
 address:  x   x+1
contents:  B9  8D
```

短浮点和长浮点数分别表示为 32位 和 64位:

```asm
float1  DD  1.234
real2  DQ  123.456
```

### 2的补数 (2's complement)
简单回顾下 2的补数 的概念。

首先补数的概念存在于有限的计数系统，因为 CPU 中的电路设计和原件所限，它只能计一个有限范围的数。相同场景的应用比如时钟系统。所谓一个数的补数，就是该数和其补数的值相加，可以使得计数系统发生溢出，从而归 0。比如时钟系统中，3 的补数就是 9。如果当前的时针在 1 点，那么 顺时针拨动3个时针单位 和 逆时针拨动9个时针单位 的结果是一样的，都是到 4 点。这就是因为时针系统的计数范围就是 0~11 点。

计算机中之所以使用负数的 2的补数 形式，是因为可以使用同一个加法电路完成加法和减法的操作。回到时钟系统中，3 的补数 9 计算方式是 `9 = 11 - 3 + 1` 加 1 就是为了溢出。

所以 -29255 的 2的补数 的计算方式就是，符号位不变，其余各位按位取反，最后的值加 1。

```
1111001001000111

符号位不变，其余各位按位取反

1000110110111000
+              1
-----------------
1000110110111001

等于 16进制 8db9
```

### 未初始化的数据 (Uninitialized Data)

为了给未初始化的数据预留存储空间，我们可以使用 RESB, RESW 等指令。每个预留指令接收一个参数来指定需要预留的单位空间数。

```
RESB  Reserve a Byte
RESW  Reserve a Word
RESD  Reserve a Doubleword
RESQ  Reserve a Quadword
REST  Reserve Ten Bytes
```

这是例子:

```asm
response  RESB  1
buffer    RESW  100
total     RESD  1
```

第一个语句预留了一个字节的空间，第二个语句预留了一个 100字 的数组，第三个语句预留了一个双字的空间。

### Multiple Definitions

汇编语言程序通常含有多个数据定义语句。比如下面的汇编程序片段:

```asm
sort  DB  'y'       ; ASCII of y = 79H
value DW  25159     ; 25169D = 6247H
total DD  542803535 ; 542803535 = 205A864FH
```

当像上面一个使用多个数据定义语句时，汇编器将分配连续的内存地址，内存结构如下:

```
 address:  x   x+1   x+2   x+3   x+4   x+5   x+6
contents:  79  47    62    4F    86    5A    20
           -   --------    ---------------------
          sort   value            total
```

多个数据定义语句可以被简写，比如下面的指令:

```asm
message  DB 'W 
         DB 'E' 
         DB 'L' 
         DB 'C 
         DB '0' 
         DB 'M' 
         DB 'E'
         DB '!'
```

可以简写成:

```asm
message  DB  'W,'E','L','C,'O','M','E','!'
```

或更加紧凑地写成:

```asm
message  DB  'WELCOME!'
```

下面这两个语句意义相同:

```asm
max_marks  DW  7*25
max_marks  DW  175
```

需要注意的是，汇编器在汇编阶段会计算常量表达式的值，所以上面两个语句的含义相同。

### Multiple Initializations

```asm
marks  TIMES  8  DW  0
```

TIMES 指令在定义数组和表的时候使用。

### Symbol Table

当我们使用数据定义指令分配存储空间的时候，我们常常使用一个象征性的名字来关联到分配的存储空间。汇编器在汇编阶段，会位每个名称赋值，该值为偏移量。比如下面的例子:

```asm
.DATA
value   DW  0
sum     DD  0
marks   TIMES   10  DW  0
message DB  'The grade is:', 0
char1   DB  '?'
```

前面已经提到了，汇编器会为这些变量分配连续的存储空间。汇编器还会使用和源码中变量出现的相同顺序来分配空间。因此找到一个变量的偏移量变得很简单，即找到在它之前分配的字节数即可。比如 marks 的偏移量是 6，这是因为 value 和 sum 分别被分配了 2个 和 4个 字节。上面的变量的符号表如下:

Name | Offset
-----|------
value | 0
sum | 2
marks | 6
message | 26
char1 | 40

## 操作数在哪里 (Where Are the Operands)

大部分的汇编语言指令都需要操作数。有很多的方式去指定操作数的位置。这些方式被称为寻址模式(addressing modes)。这一节简述几个基础汇编所需的寻址模式，跟多的细节将在 [Chapter 13]() 中讨论。

指令(instruction) 所需的操作数可以存在于以下几个位置:

* 处理器内部的寄存器
* 指令本身
* 主内存，通常在数据段
* I/O port([chapter 20]() 中讨论)

将操作数放在寄存器中的模式称为 register addressing mode，指令本身包含操作数的模式称为 immediate addressing mode。提供这些寻址模式的目的就是为了支持高级语言的特性 [Chapter 13]()。

### 寄存器寻址模式 (Register Addressing Mode)

在这个模式下，处理器内部的寄存器包含了指令所需要处理的数据，比如:

```asm
mov  EAX,EBX
```

需要两个操作数都在处理器的寄存器中。mov 指令的语法如下:

```
mov  destination, source
```

mov 指令将 source 的内容拷贝到 destination 中。source 的内容不会被销毁。因此上面的例子中，将寄存器 EBX 的内容拷贝到寄存器 EAX 中。

寄存器寻址模式是性能最高的，因为操作数已经位于处理器内部，不需要进行内存访问。

### 立即寻址模式 (Immediate Addressing Mode)

在这个寻址模式中，数据作为指令的一部分。所以即使数据也在内存中，但是它存在于代码段而不是数据段。这个寻址模式通常使用在那种至少需要两个操作数的指令中。

例子:

```asm
mov  AL,75
```

source 操作数 75 使用了立即寻址模式，destination 操作数使用了寄存器寻址模式，这样的指令称为混合寻址模式 (mixed-mode addressing。

接下来我们将讨论数据存在于数据段的寻址模式。这种模式被称为 memory addressing modes。我们将讨论两种内存寻址模式: direct 和 indirect。其余的内存寻址模式将会在 [Chapter 13]() 中讨论。

### 直接内存寻址模式 (Direct Addressing Mode)

操作数存在于内存中、通常在数据段时，就需要访问内存，因此执行速度会慢于前面两种模式。

回顾将数据放在数据段的内容，我们需要两个部件: 段起始地址，和段内的偏移量。段起始地址通常在 DS 寄存器中。因此，不同的内存寻址模式的区别在于偏移量的指定方式。偏移量也称为 有效地址(effective address)。

在直接寻址模式中，偏移量直接作为指令的一部分。在汇编语言中，这个值通过数据元素的变量名在指定。汇编器在汇编过程中将变量名转换成与其对应的偏移量。为了方便转换，汇编器维护了一个符号表(symbol table)。前面已经讨论过了，符号表中保存了程序中的所有变量的偏移量。

这个寻址模式是所有内存寻址模式中最简单的。这个模式的一个限制就是，只能指定一个操作数。下面是几个例子:

```asm
response  DB 'y'              ;allocates a byte, initializes to y
table1    TIMES  20  DW  0    ;allocates 40 bytes, initializes to 0
name1     DB  'Jim Ray'       ;allocates 7 bytes, initializes to Jim Ray
```

下面是一些关于 mov 指令的例子：

```asm
mov   AL,[response]    ;copies y into AL register
mov   [response], 'N'  ;N is written into response
mov   [name1], 'K'     ;write K as the first character of name1
move  [table1], 56     ;56 is written in the first element
```

最后一个语句等同于 C语言 中的 `table1[0] = 56`。

### 间接内存寻址模式 (Indirect Addressing Mode)

直接寻址模式比较直观，但是它只能处理一些简单的情况。比如当需要访问 table1 中的第二个元素时，它将无能为力:

```c
table1[1] = 99
```

间接寻址模式弥补了这一缺陷。在这个模式中，偏移量或者说有效地址存在于一个通用寄存器中。因此，这个模式也被称为寄存器间接寻址模式(register indirect addressing mode。

间接寻址模式不限定变量只能是一个简单值。对于像 table1 一样含有多个元素的变量，可以将它的起始地址载入 EBX寄存区，然后 EBX寄存器 就好比指向 table1 的指针。通过改变 EBX寄存器 的值，我们可以访问 table1 中的不同元素。

下面的例子中，将 100 赋值给第一个元素，将 99 赋值第二个元素。注意 EBX 增加了 2，因为 table1 中的每个元素是 2个字节。

```asm
mov  EBX,table1    ;copy address of table1 to EBX
mov  [EBX],100     ;table1[0] = 100
add  EBX,2         ;EBX = EBX + 2
mov  [EBX],99      ;table[1] = 99
```

[Chapter 13]() 中将讨论如何更有效的完成这个任务。

有效地址(effective address) 可以通过 lea指令(load effective address) 载入寄存器。语法如下:

```
lea   register,source
```

因此,

```asm
lea   EBX,[table1]
```

可以用来替换 

```asm
mov   EBX,table1
```

不同点在于，lea指令将在运行时计算偏移量，而 mov 指令在汇编阶段计算偏移量。基于这点，优先使用后一种方式。不过，lea 为 source 操作数提供了更灵活的方式，比如:

```asm
lea   EBX,[array+ESI]
```

这个语句中加载了 array 中的元素，该元素的索引存在于寄存器 ESI 中。这种情况下，就无法使用 mov 指令，因为 ESI 寄存器的内容在汇编阶段无法获得。


## 汇编语言指令概览 (Overview of Assembly Language Instructions)

这一些简单介绍一些基本的汇编语言指令，目的就是可以利用这一些指令写一些可以运行的程序，达到先睹为快的效果。

### MOV 指令

我们已经介绍过了 mov 指令。它需要两个操作数，语法如下:

```
mov   destination,source
```

数据从 source 拷贝到 destination，source 的数据不会被破坏。两个操作数的字节大小必须相同。mov 指令可以有下面几种形式:

```
mov    register,register
mov    register,immediate
mov    memory,immediate
mov    register,memory
mov    memory,register
```

没有直接从 memory 到 memory 的 mov 指令。不过在 [Chapter 17]() 中将会介绍，可以通过字符串指令来完成内存到内存的数据传输。

下面是 mov 指令的一些列子:

```asm
mov    AL,[response]
mov    DX,[table1]
mov    [response],'N'
mov    [name1+4],'K'
```

### 有歧义的移动 (Ambiguous Moves)

当将立即数移动到内存中时，有时会因为类型问题造成歧义，比如：

```asm
mov    EBX,[table1]
mov    ESI,[name1]
mov    [EBX],100
mov    [ESI],100
```

对于 100 而言，是使用字(2 bytes)还是字节来写入内存。我们可以通过指定类型来消除歧义:

```asm
mov    WORD  [EBX],100
mov    BYTE  [EBX],100
```

我们还是将上述的语句写成:

```asm
mov    [EBX], WORD 100
mov    [EBX], BYTE 100
```

下面是一些类型标识符:

Type specifier | Bytes addressed
---------------|----------------
BYTE | 1
WORD | 2
DWORD | 4
QWORD | 8
TBYTE | 10

### 简单的算术指令 (Simple Arithmetic Instructions)

介绍一些简单的算术指令，更加详细的见 [Chapter 14](./addressing-modes.md)

#### INC 和 DEC 指令
分别用于将操作数 加1 和 减1。格式如下:

```
inc    destination
dec    destination
```

destination 可以是 8、16、32位的操作数。

```asm
inc    EBX    ;increment 32-bit register
dec    DL     ;decrement 8-bit register
```

下面的例子:

```asm
.DATA
count    DW    0
value    DB    25

.CODE
    inc    [count]    ;unambiguous
    dec    [value]    ;unambiguous
    mov    EBX,count
    inc    [EBX]      ;ambiguous
    mov    ESI,value 
    dec    [ESI]      ;ambiguous
```

前两个语句没有歧义，是因为汇编器通过操作数的类型，可以知道需要操作的字节数。
后两个语句含有歧义，是因为汇编器还不够智能，无法通过上下文来判断需要操作的字节数，所以为了消除歧义，如果指定类型：

```asm
inc    WORD  [EBX]
dec    BYTE  [ESI]
```

#### ADD 指令
用于两个 8、16、32位的操作数相加。语法是：

```
add    destination,source
```

语义类似:

```
destination = destination + source
```

一些例子比如:

```asm

inc    EAX
;等于
add    EAX,1
```

inc 相对更易阅读，当时两者的执行速度相同。

#### SUB 和 CMP 指令
sub(SUBtract)指令 用于两个 8、16、32位的操作数的减法。语法是:

```
sub    destination,source
```

语义类似：

```
destination = destination - source
```

cmp(COMpare) 指令用于比较两个操作数(相等、不等，等等)。cmp 指令执行了和 sub 指令相同的操作，只不过并没有保存减法结果。因此 cmp 指令不会修改 source 和 destination 的内容。cmp 指令通常和 条件跳转指令(conditional jump instruction) 一起使用，后面会介绍到。

### 条件执行 (Conditional Execution)

指令集中有一些分支和循环的指令，为了满足程序的构造。接下来将讨论其中一部分，剩余的部分将在 [Chapter 15]() 中讨论。

### 无条件跳转 (Unconditional Jump)

无条件跳转指令 jmp，顾名思义，就是告诉处理器下一条需要执行的指令在标注的地方，标注作为指令的一部分被提供。形式如下:

```asm
jmp    label
```

label 表示了下一条指令的位置。例子:

```asm
    mov     EAX,1
inc_again:
    inc     EAX
    jmp     inc_again
    mov     EBX,EAX
```

上面的例子将会执行一个无线循环，最后一条语句永远不会被执行。

从例子上开看，跳转指令好像一无是处。随后将会提供一些例子展示其功能。

### 条件跳转 (Conditional Jump)

在条件跳转指令中，只有在特定的条件满足的情况下，程序才会转移执行权。形式如下:

```
j<cond>    label
```

`<cond>` 表示的就是需要执行 label 执行所要满足的条件。例子:

```asm
read_char:
    mov     DL,0
    ;code for reading a character into AL
    cmp     AL,ODH          ;compare the character to CR
    je      CR_received     ;if equal, jump to CR_received
    jnc     CL              ;otherwise, increment CL and
    jmp     read_char       ;character from keyboard
CR_received:
    mov     DL,AL
```

从键盘读取字符，知道按下了回车键(CR)。字符数是由 CL寄存器 维护的。下面两条语句执行了所需的条件判断:

```asm
cmp     AL,ODH
je      CR_received
```

当执行 je 指令时，处理器是如何记录上一步中 cmp 指令的执行结果的？标志寄存器(flags register) 的其中一个目的就是提供指令间的结果暂存。

上文提到 cmp 指令会从 AL寄存器中减去 ODH，但是并不会保存减法结果，两个操作数相等，那么设置 zero flag (ZF=1)，否则 ZF=0。zero flag 将一直保存这个结果，直到下一个影响到该标志位的指令修改其值。

因此当 je 指令执行的时候，处理器会检查 ZF标志位，只有当 ZF = 1 时，处理器才会将下一条需要执行的指令载入到 EIP寄存器 中。回顾 EIP寄存器，它总是指向下一条需要执行的指令。因此，当输入了回车后，原本要执行的:

```asm
inc     CL
```

变为了取得指令(fetch)

```asm
mov     DL,AL
```

跳转指令可以有下面几种跳转条件：

```
je          jump if equal
jg          jump if greater
jl          jump if less
jge         jump if greater or equal
jle         jump if greater or equal
jne         jump if not equal
```

条件跳转页可以直接验证标志位，比如:

```asm
jz          jump if zero(ie. if ZF = 1)
jnz         jump if not zero(ie. if ZF = 0)
jc          jump if carry(ie. if CF = 1)
jnc         jump if not carry(ie. if CF = 0)
```

### 迭代指令 (Iteration Instruction)

迭代可以通过上面的跳转指令来完成。不过指令集中仍然提供了一组 loop指令来支持迭代。语法如下：

```
loop       target
```

target 表示和上述 jump 指令中类似的需要跳转到的目标标注。

这个指令假定 ECX 寄存器包含了循环次数。作为循环指令的一部分，它会消去 ECX 寄存器的值然后跳转到指定的标注，直到 ECX == 0。

### 逻辑指令 (Logic Instructions)

```
and         destination,source
or          destination,source
xor         destination,source
not         destination,source
```

逻辑指令也会操作上述的标志位:

```asm
    and     AL,O1H
    je      bit_is_zero
bit_is_zero:
    ;...
```

假设 AL寄存器 的内容为 10101110B。此时执行 and指令 后，结果为 0，由于逻辑指令也会操作标志位，因此 ZF = 1，所以 je 指令的条件满足，处理器将会将 bit_is_zero 处的指令位置载入到 EIP 中。

这里的 je 也可以使用 jz 替换。实际上 je 就是 jz 的别名。
