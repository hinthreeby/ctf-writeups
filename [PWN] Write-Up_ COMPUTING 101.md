---
title: '[PWN] Write-Up: COMPUTING 101'

---

[toc]

# Your First Program

## Your First Register
**Summary:**
- In x86's modern incarnation, programs have access to **16** general purpose registers. 
- We will learn about our first one: `rax`. 
- If you wanted to store the value `1337` into `rax`, the x86 Assembly would look like:
```assembly
    mov rax, 1337
```
- The destination `rax` is specified before the source (the value `1337`).
- The operands are separated by a comma.
![image](https://hackmd.io/_uploads/rJ-REWUtJg.png)

## Your First Syscall
**Summary:**
- Your programs interact with the operating system (via the `CPU`, of course) using the **syscall**, or System Call instruction.Anything your program does that doesn't involve performing computation on data is done with a system call.
-  Each system call is indicated by **a syscall number**, counting upwards from `0`, and your program invokes a specific syscall by moving its syscall number into the **rax register** and invoking the syscall instruction. 
-  For example, if we wanted to invoke **syscall 42**, we would write two instructions:
```assembly=
    mov rax, 42
    syscall
```
- Contents of assembly file:
![image](https://hackmd.io/_uploads/ryTRvbUtyg.png)
- The result:
![image](https://hackmd.io/_uploads/S1Z-OZ8F1g.png)

## Exit codes
**Summary:**
- In the x86-64 architecture, to make a system call (syscall), we follow these steps:
     - The **syscall number** is placed in the `rax` register.
     - The **syscall parameters** are placed in other registers (`rdi, rsi, rdx`, etc.).
- For the `exit` syscall, there is **only one parameter**, which is the **exit code**, and it is placed in `rdi`.
- Contents of assembly file:
![image](https://hackmd.io/_uploads/HkmujQUFJx.png)
- The result:
![image](https://hackmd.io/_uploads/HyvsiXLKke.png)
## Building Executable
**Summary:**
- Write Assembly code (asm.s)
- Assemble into an object file (asm.o) using **`as -o asm.o asm.s`**
- Link into an executable (exe) using **`ld -o exe asm.o`**
- Run the program with ./exe
- Check the exit code using **`echo $?`**
```assembly=
    .intel_syntax noprefix
    mov rdi, 42
    mov rax, 60
    syscall

```
- Contents of assembly file:
![image](https://hackmd.io/_uploads/B1Gik4IKye.png)
- The result:
![image](https://hackmd.io/_uploads/BJyh1EUtJg.png)
## Moving between registers
**Summary:**
- `rsi` is another register that can store data, like `rdi`.
- You can move data into `rsi`:
```assembly
    mov rsi, 42
```
- You can transfer data between registers:
```assembly
    mov rsi, 42
    mov rdi, rsi
```
- This sets `rdi` to the value stored in `rsi`.
- Contents of assemble file:
![image](https://hackmd.io/_uploads/SymmQE8Y1x.png)
![image](https://hackmd.io/_uploads/HJhnQELtJg.png)
- The result:
![image](https://hackmd.io/_uploads/rJT6XE8YJx.png)
# Software Introspection
## Tracing Syscalls
**Summary:**
- `strace` is a tool that traces **system calls** made by a program.
- It shows which **system calls were invoked**, what **parameters** were passed, and their **results**.
- Example output of strace on a program: 
```assembly=
hacker@dojo:~$ strace /tmp/your-program
execve("/tmp/your-program", ["/tmp/your-program"], 0x7ffd48ae28b0 /* 53 vars */) = 0
exit(42)                                 = ?
+++ exited with 42 +++
```
- The first is an `execve` system call, the second is the **exit system call** with the **parameter** 42.
- The syntax used here for output is `system_call(parameter, parameter, parameter, ...)`.
![image](https://hackmd.io/_uploads/SyfALUUK1e.png)
## Starting GDB
**Summary:**
- GDB (GNU Debugger) is a tool used for debugging and analyzing programs.
- It allows monitoring and introspection of a running process.
- To launch GDB for a program:
`gdb /path/to/binary/file`
![image](https://hackmd.io/_uploads/rJVFt8UKyg.png)
## Starting Programs in GDB
**Summary:**
- Debuggers like GDB observe a program while it runs to reveal its runtime behavior.
- To start the program: `(gdb) starti`
![image](https://hackmd.io/_uploads/SJh598IKJe.png)

# Computer Memory
## Loading From Memory
**Summary:**
- Computer memory is a vast space where data is stored sequentially, similar to houses on a street with numeric addresses.
- Modern programs do not use all addresses **continuously, leaving gaps** in memory.
-  The point is, computers store data, mostly sequentially, in memory.
- Accessing memory using registers:
- **Direct assignment**: Stores the value `31337` in `rdi`.
`mov rdi, 31337 `
- **Memory access**: Loads the **value** at **memory address** `31337` into `rdi`.
`mov rdi, [31337] `
![image](https://hackmd.io/_uploads/HkFN5vIYJe.png)

## Dereferencing Pointers
**Summary:**
- **Direct And Indirect Memory Access**:
    - Instead of manually using memory addresses like `133700`, we can **store addresses in registers** and access memory indirectly.
    - Typically, memory addresses are stored in registers, and we use the values in the registers to point to data in memory!

```
                        Address │ Contents
                      +────────────────────+
                    ┌▸│ 133700  │ 42       │
                    │ +────────────────────+
                    │
                    └────────────────────────┐
                                             │
                       Register │ Contents   │
                      +────────────────────+ │
                      │ rax     │ 133700   │─┘
                      +────────────────────+

```
```assembly=
mov rax, 133700   
mov rdi, [rax]    
```
- Pointers and Dereferencing:
    - `rax` acts as a **pointer** (holding an address).
    - `mov rdi, [rax]` dereferences `rax`, retrieving the **value** stored at `133700`.
- The CPU doesn't care if a register holds an address, syscall number, or other data—it depends on how you use it.
![image](https://hackmd.io/_uploads/SkzOC7wFkl.png)

## Dereferencing Yourself
**Summary:**
- **Registers Can Hold Pointers**:
    - We used `rax` as a pointer, but any register can be used as a pointer, including `rdi`.
```assemble=
    mov [133700], 42   ; Store 42 at memory address 133700
    mov rax, 133700    ; rax holds the memory address
    mov rax, [rax]     ; rax now holds the value at 133700 (42)
```
- **Overwriting a Register with Its Own Dereferenced Value**:
    - Instead of using a different register, we can dereference a register to overwrite itself.
    - If `rdi` already holds an address, we can dereference it to get the actual value stored at that address.
![image](https://hackmd.io/_uploads/rkpsxNwtyx.png)

## Dereferencing with Offsets
**Summary:**
- **Pointers Don't Always Point Directly to Desired Data**:
    - A pointer **references a collection of data** (e.g., a list, array, or book).
    - To access specific data within that collection, you must use **offsets**.
```
                    Address │ Contents
                      +────────────────────+
                    ┌▸│ 31337   │ 0        │
                    │ │ 31337+1 │ 0        │
                    │ │ 31337+2 │ 0        │
                    │ │ 31337+3 │ 0        │
                    │ │ 31337+4 │ 0        │
                    │ │ 31337+5 │ 0        │
                    │ │ 31337+6 │ 0        │
                    │ │ 31337+7 │ 0        │
                    │ │ 31337+8 │ ???      │
                    │ +────────────────────+
                    │
                    └────────────────────────┐
                                             │
                       Register │ Contents   │
                      +────────────────────+ │
                      │ rdi     │ 31337    │─┘
                      +────────────────────+
```
- **Accessing Data with an Offset**:
    - If rdi holds the address of a sequence of numbers. To access the second value (42), use: `mov rax, [rdi+1]`

**Note**:
- Byte: A unit of memory storage.
- Offset: The difference in memory addresses (e.g., +1 means moving 1 byte forward).

![image](https://hackmd.io/_uploads/BkjYGNDY1g.png)

## Stored Addresses
**Summary:**
- **Pointers Can Store Other Pointers**:
    - Instead of holding actual data, a memory address can store another memory address. 
    - This is similar to writing down a friend's address and storing it in your house.
```
        Address   │ Contents
        +────────────────────+
        │ 123400   │ 133700  │  ; Address 123400 stores 133700
        │ 133700   │ 42      │  ; Address 133700 stores 42
        +────────────────────+
```
- The following code retrieves `42`:
```assemble=
mov rdi, 123400    ; rdi = 123400
mov rdi, [rdi]     ; rdi = value at 123400 (133700)
mov rax, [rdi]     ; rax = value at 133700 (42)
```
- If addresses and data get **mixed up**, security issues can arise—this is a key part of binary exploitation.
![image](https://hackmd.io/_uploads/BJdGTNDtkl.png)

## Double Dereferencing
![image](https://hackmd.io/_uploads/SkoLyBvKyl.png)

## Triple Dereferencing
![image](https://hackmd.io/_uploads/r1dx-SvYJg.png)

# Hello Hackers
## Writing Ouputs
**Summary:**
- System Calls:
    - The write system call (**syscall number 1**) is used to output text.
- Every process starts with **three** File Descriptors (FDs):
    - **FD 0 (Standard Input)** → Used for input (e.g., shell commands).
    - **FD 1 (Standard Output)** → Used for normal output (e.g., printing text).
    - **FD 2 (Standard Error)** → Used for error messages.
- To write output, you set `rdi = 1` (standard output) or `rdi = 2` (standard error).
- **How write Works:**
    - Writing large amounts of text requires multiple system calls, which are slow because of CPU context switching.
    - To optimize, write allows multiple characters to be written at once.
- **Parameters for write:**
    - `write(file_descriptor, memory_address, number_of_characters)`
    - **Example**: `write(1, 1337000, 10);` → Writes 10 characters from memory address 1337000 to standard output.
- **Register Assignments**
    - `rdi` → File descriptor (1 for standard output).
    - `rsi` → Memory address of the data.
    - `rdx` → Number of characters to write.
    - `rax` → Syscall number (1 for write).

![image](https://hackmd.io/_uploads/rkxlprvYyx.png)
## Chaining Syscall
![image](https://hackmd.io/_uploads/Syhek8wt1e.png)

## Writing Strings
![image](https://hackmd.io/_uploads/BkKtJUwYke.png)
## Reading Data
- The `read` system call (**syscall number 0**)
- `read(file_descriptor, memory_address, number_of_characters)`
- **Register Assignments**
    - `rdi` → File descriptor (0 for standard output).
    - `rsi` → Memory address of the data.
    - `rdx` → Number of characters to write.
    - `rax` → Syscall number (0 for `write`).
![image](https://hackmd.io/_uploads/S1d6vxFY1x.png)
![image](https://hackmd.io/_uploads/HJuRDltFJx.png)

# Assembly Crash Course
## set-registers
![image](https://hackmd.io/_uploads/SJIS5DwKke.png)
## set-multiples-registers
![image](https://hackmd.io/_uploads/rJwAnDPtye.png)

## add-to-registers
**Summary:**
- Many instructions exist in x86 that allow you to perform all the normal math operations on registers and memory.
- For shorthand, when we say `A += B`, it really means `A = A + B`.
- Here are some useful instructions:
    - **`add reg1, reg2`**: reg1 += reg2
    - **`sub reg1, reg2`**: reg1 -= reg2
    - **`imul reg1, reg2`**: reg1 *= reg2

**Note**: all `regX` can be replaced by a constant or memory location.

## linear-euqation-registers
- There is an important difference between `mul` (**unsigned** multiply) and `imul` (**signed** multiply) in terms of which registers are used. 

![image](https://hackmd.io/_uploads/S1mH2dvtke.png)
![image](https://hackmd.io/_uploads/S1gb3uPYJl.png)
![image](https://hackmd.io/_uploads/BJYP2_PFke.png)

## integer-division
- Lệnh `div` thực hiện phép chia một số bị chia **128-bit** cho một số chia **64-bit** và lưu kết quả vào:
    - Thương số (quotient) trong `rax`.
    - Phần dư (remainder) trong `rdx`.

```assemble=
mov rax, reg1   
div reg2       
```
- Cách hoạt động: Khi thực hiện div reg2, x86 thực hiện phép chia như sau:
$$
\text{rax} = \frac{\text{rdx:rax}}{\text{reg2}}
$$
$$
\text{rdx} = \text{remainder}
$$
- `rdx:rax` là số bị chia 128-bit (gồm `rdx` là 64-bit cao, `rax` là 64-bit thấp).
- `reg2` là số chia 64-bit.
- `rax` sẽ chứa kết quả thương.
- `rdx` sẽ chứa phần dư sau phép chia (Cũng như là kết quả của phép modulo).

**Lưu ý**: Trước khi gọi div, cần đảm bảo rdx không chứa giá trị rác, vì rdx tham gia vào số bị chia!
![image](https://hackmd.io/_uploads/S1yMWYDKke.png)

## modulo-operations
```assembly=
.intel_syntax noprefix
mov rdx, 0
mov rax, rdi
div rsi
mov rax, rdx
```
## set-upper-bytes
- Another cool concept in x86 is the ability to independently **access the lower register bytes**.
- Each register in x86_64 is 64 bits in size, and in the previous levels, we have accessed the full register using `rax`, `rdi`, or `rsi`.
- We can also access the lower bytes of each register using different register names.
- For example, the lower **32 bits** of rax can be accessed using `eax`, the lower **16 bits** using `ax`, and the lower 8 bits using `al`.

```
            MSB                                    LSB
            +----------------------------------------+
            |                   rax                  |
            +--------------------+-------------------+
                                 |        eax        |
                                 +---------+---------+
                                           |   ax    |
                                           +----+----+
                                           | ah | al |
                                           +----+----+
                               
```
- Lower register bytes access is applicable to almost all registers.

## efficient-modulo
- It turns out that using the `div` operator to **compute the modulo operation** is **slow**!
- We can use a math trick to optimize the modulo operator (%). Compilers use this trick a lot.
- If we have `x % y`, and `y` is **a power of** `2`, such as `2^n`, the result will be the **lower n bits of x**.
![image](https://hackmd.io/_uploads/ryG5jX_Y1g.png)
## bytes-extraction
```
shl reg1, reg2    
shr reg1, reg2  
```
- Dịch trái/phải `reg1` với số bit được xác định trong `reg2`.
- `reg2` có thể là một hằng số hoặc một giá trị trong bộ nhớ.


| Toán tử | Trên bit| Trên số |
| -------- | -------- | -------- |
| `shl` (Shift Left)   | Đẩy bit sang trái, thêm `0` bên phải     | Nhân với 2 |
| `shr` (Shift Right)   | Đẩy bit sang phải, thêm `0` bên trái     | Chia với 2     |
- Ví dụ: 
`0001 0101`  (Giá trị thập phân: `21`)
Dịch trái:
`0010 1010`  (Giá trị thập phân: `42`)

![image](https://hackmd.io/_uploads/ByDKXEOKyx.png)
## bitwise-and
```assemble=
xor reg1, reg2
and reg1, reg2
or reg1, reg2
```
- Thực hiện (xor/and/or) `reg1` với `reg2` và lưu kết quả vào `reg1`.
![image](https://hackmd.io/_uploads/S1MM_VdF1g.png)
## check-even
- Một số chẵn nếu bit cuối cùng là `0`, lẻ nếu bit cuối là `1`.
![image](https://hackmd.io/_uploads/r19rREdYyl.png)
## memory-read
- The address is special because it is unique. But that also does not mean other addresses can't point to the same thing (as someone can have multiple houses). Memory is exactly the same!
- For instance, the address in memory where your code is stored (when we take it from you) is `0x400000`.
- In x86, we can access the thing at a memory location, called dereferencing, like so:
- `mov rax, [some_address]`: Moves the thing at 'some_address' into rax
This also works with things in registers:
- `mov rax, [rdi]`: Moves the thing stored at the address of what rdi holds to rax
This works the same for writing to memory:
- `mov [rax], rdi`: Moves rdi to the address of what rax holds.
So if rax was `0xdeadbeef`, then `rdi` would get stored at the address `0xdeadbeef`: `[0xdeadbeef] = rdi`

**Note**: Memory is linear, and in x86_64, it goes from 0 to `0xffffffffffffffff` (yes, huge).
![image](https://hackmd.io/_uploads/r1oFNBdFkg.png)

## memory-write
![image](https://hackmd.io/_uploads/SkfcLruKJx.png)
## memory-increment
```assembly=
.intel_syntax noprefix
mov rax, [0x404000]
add [0x404000], 0x1337
```
- We have a bug:
```
pwn14.s: Assembler messages:
pwn14.s:3: Error: ambiguous operand size for add
```
- The error "**ambiguous operand size for add**" occurs because the assembler doesn't know whether `[0x404000]` is a **byte (8-bit), word (16-bit), doubleword (32-bit), or quadword (64-bit)** memory location.
- **Solution**: You need to explicitly specify the operand size when modifying a memory location. Since rax is a 64-bit register, we assume `0x404000]` is also `64-bit`.
- `add qword ptr [0x404000], 0x1337` → Adds `0x1337` to the **64-bit value** stored at `0x404000`.

![image](https://hackmd.io/_uploads/HkmRPSuFJl.png)
## byte-access
- Here is the breakdown of the names of memory sizes:
    - Quad Word = 8 Bytes = 64 bits
    - Double Word = 4 bytes = 32 bits
    - Word = 2 bytes = 16 bits
    - Byte = 1 byte = 8 bits
- In x86_64, you can access each of these sizes when dereferencing an address, just like using bigger or smaller register accesses:
    - `mov al, [address]`: Moves the least significant byte from address to rax
    - `mov ax, [address]`: Moves the least significant word from address to rax
    - `mov eax, [address]`: Moves the least significant double word from address to rax
    - `mov rax, [address]`: Moves the full quad word from address to rax
- Remember that moving into al does not fully clear the upper bytes.

![image](https://hackmd.io/_uploads/HyElqB_tJe.png)
## memory-size-access
![image](https://hackmd.io/_uploads/Bkd7oBOY1x.png)
## little-endian-write
**Little Endian:**
- Little Endian giúp CPU dễ dàng xử lý các phép toán số học vì byte quan trọng nhất được đặt ở địa chỉ cao hơn.
- Giả sử ta có một giá trị 64-bit (8 byte) được lưu tại địa chỉ `0x1330` như sau: `[0x1330] = 0x00000000DEADC0DE`
Dữ liệu sẽ được lưu ngược lại trong bộ nhớ, theo từng byte:
```assembly
[0x1330] = 0xde
[0x1331] = 0xc0
[0x1332] = 0xad
[0x1333] = 0xde
[0x1334] = 0x00
[0x1335] = 0x00
[0x1336] = 0x00
[0x1337] = 0x00
```
- Trong Assembly, **không thể gán trực tiếp một hằng số lớn vào một ô nhớ** bằng cách sử dụng `[rdi]`. Thay vào đó, chúng ta cần sử dụng một thanh ghi trung gian (ở đây là `rax`).
- Lệnh `mov` không cho phép gán trực tiếp một **giá trị 64-bit** vào một địa chỉ bộ nhớ. 
- Lý do: Trong Assembly x86-64, toán hạng nguồn (src) của `mov [dest]`, **src không thể là một số nguyên lớn.**
- Các toán hạng hợp lệ có thể là:
    - Một thanh ghi (ví dụ: `mov [rdi], rax`)
    - Một giá trị nhỏ (**dưới 32-bit**) (ví dụ: `mov dword ptr [rdi], 0x1337`) 

**Lưu giá trị vào `rsi`:**
- Chúng ta cần lưu `0xC0FFEE0000` vào `[rsi]`.
- Số này chỉ có **5 byte (40-bit)**, nên có thể lưu bằng **edi (32-bit)** và thêm **1 byte bổ sung**.

![image](https://hackmd.io/_uploads/HkjhRruY1x.png)
## memmory-sum
![image](https://hackmd.io/_uploads/r11_P__Y1e.png)

## stack-subtraction
- The stack is a last in, first out (LIFO) memory structure.
- `pop reg1`: Lấy giá trị từ stack và gán cho `reg1`.
- `push reg1`: Thêm vào stack giá trị `reg1`.
-  The stack pointer register (`rsp`).

![image](https://hackmd.io/_uploads/B10fnuuFyx.png)
## swap-stack-values
![image](https://hackmd.io/_uploads/HkfPTudFkx.png)
## average-stack-value
![image](https://hackmd.io/_uploads/BJ6TzKuFJe.png)
## absolute-jump
- Hai cách chính để điều khiển luồng chương trình:
    1. Thông qua một lệnh nhảy (jump)
    2. Thông qua một lệnh gọi (call)
- Hai loại lệnh nhảy:
        - Unconditional jump: Nhảy đến target_address mà không cần kiểm tra điều kiện nào cả. `jmp target_address`.
    - Conditional jump: Chỉ xảy ra khi một điều kiện cụ thể được thỏa mãn.
```assembly=
cmp eax, ebx        ; So sánh eax với ebx
je equal_label      ; Nếu bằng nhau thì nhảy đến equal_label
```
**3 loại lệnh nhảy trong x86:**
- Nhảy tương đối (Relative jump): Nhảy đến một vị trí tương đối với lệnh kế tiếp. `jmp +5`
- Nhảy tuyệt đối (Absolute jump): Nhảy đến một địa chỉ cố định trong bộ nhớ.
- Trong x86, điều này thường được thực hiện bằng cách:
    1. Đặt địa chỉ đích vào một thanh ghi.
    2. Thực hiện jmp đến giá trị trong thanh ghi.
```assembly=
mov rax, 0x400080 ; Đặt địa chỉ 0x400080 vào thanh ghi rax
jmp rax           ; Nhảy đến địa chỉ chứa trong rax
```
- Nhảy gián tiếp (Indirect jump): Nhảy đến địa chỉ được lưu trong một thanh ghi hoặc bộ nhớ.
```assembly=
mov rbx, [some_memory] 
jmp rbx                ; Nhảy đến địa chỉ được lưu trong rbx
```
![image](https://hackmd.io/_uploads/HyBEecutkl.png)

## relative-jump
- T**hanh ghi rip trong CPU x86-64**: Thanh ghi rip (Instruction Pointer Register) là một thanh ghi đặc biệt trong kiến trúc x86-64, giữ địa chỉ của lệnh tiếp theo mà CPU sẽ thực thi.
**Làm thế nào để thực hiện nhảy tương đối?**
- Nhảy tương đối yêu cầu có **khoảng trống trong code** để nhảy đến đúng vị trí mong muốn.
- Để chèn khoảng trống, bạn có thể sử dụng lệnh `nop` (No Operation).
- Lệnh `nop` có kích thước `1 byte`, không làm gì nhưng giúp căn chỉnh mã lệnh.
- GNU Assembler hỗ trợ directive `.rept` để lặp lại một lệnh `n` lần mà không cần viết thủ công.
- `jmp (reg1 | addr | offset)`: Lệnh nhảy có thể sử dụng thanh ghi, địa chỉ, hoặc độ lệch.

**Lable trong Assembly:**
- Nhãn (Label) trong Assembly là một tên định danh dùng để **đánh dấu một địa chỉ** trong bộ nhớ.
- Khi chương trình chạy, CPU có thể **nhảy (jump), gọi (call), hoặc tham chiếu (reference)** đến nhãn thay vì dùng địa chỉ cụ thể.
- Cú pháp của Nhãn:
```assembly=
label_name:
    instruction
```
![image](https://hackmd.io/_uploads/S1WOSputke.png)
![image](https://hackmd.io/_uploads/HyuUr6uKkx.png)

## jump-trampoline

![image](https://hackmd.io/_uploads/rkM_31KKye.png)
![image](https://hackmd.io/_uploads/SkMvnkKtke.png)
## conditional-jump
**Đoạn code 1**:
```assembly=
.intel_syntax noprefix

mov eax, [rdi]          
cmp eax, 0x7f454c46  
je is_0x7f454c46       

cmp eax, 0x00005A4D   
je is_0x00005A4D      

jmp default_case       

is_0x7f454c46:
    mov eax, [rdi + 4]  
    add eax, [rdi + 8] 
    add eax, [rdi + 12] 
    jmp done          

is_0x00005A4D:
    mov eax, [rdi + 4] 
    sub eax, [rdi + 8]  
    sub eax, [rdi + 12] 
    jmp done            

default_case:
    mov eax, [rdi + 4]  
    imul eax, [rdi + 8] 
    imul eax, [rdi + 12]

done:
```
**Đoạn code 2:**
```assembly=
.intel_syntax noprefix

mov rax, [rdi + 4]  ; rax = [x + 4]

mov rdx, 0x7f454c46
cmp [rdi], rdx
je is_0x7f454c46

mov rdx, 0x00005A4D
cmp [rdi], rdx
je is_0x00005A4D

jmp default_case    

is_0x7f454c46:
    add rax, [rdi + 8]
    add rax, [rdi + 12]
    jmp done         

is_0x00005A4D:
    sub rax, [rdi + 8]
    sub rax, [rdi + 12]
    jmp done         

default_case:
    imul rax, [rdi + 8]
    imul rax, [rdi + 12]

done:
```
- Đoạn code 1 cho kết quả đúng, còn đoạn code 2 thì sai. Vấn đề là việc **sử dụng kích thước thanh ghi**.

**Sự khác biệt về thanh ghi (rax vs eax)**
- Đoạn code 1 sử dụng **eax** (thanh ghi 32-bit)
- Đoạn code 2 sử dụng **rax** (thanh ghi 64-bit)

**Lưu ý quan trọng trong x86-64:**
- Khi bạn truy xuất **một giá trị 32-bit** từ bộ nhớ và lưu vào **eax**, các **bit cao hơn (32-bit trên của rax) sẽ tự động bị xóa thành 0.**
- Nhưng nếu bạn truy xuất **giá trị 32-bit** và lưu vào **rax**, các bit cao hơn có thể **chứa rác từ giá trị trước đó.**

**Hệ quả:**
Khi `mov rax, [rdi + 4]`, có khả năng **rax** sẽ chứa **một số rác ở 32-bit cao**. Điều này làm sai lệch phép toán cộng, trừ hoặc nhân.

**Sự khác biệt về cách so sánh giá trị tại [x]:**
**Đoạn code 1:** Đúng logic
```assembly=
mov eax, [rdi]     
cmp eax, 0x7f454c46   
```
**Đoạn code 2:**
```assembly=
mov rdx, 0x7f454c46
cmp [rdi], rdx     
```
- Lỗi: `cmp [rdi], rdx `có thể so sánh **64-bit của rdx với 32-bit trong bộ nhớ**, gây ra lỗi logic.

**Cách sửa đoạn code 2 để đúng:**
- Thay rdx bằng edx để so sánh 32-bit với 32-bit.
- Dùng eax thay vì rax khi lấy giá trị từ bộ nhớ để tránh lỗi số rác.

**Bài học rút ra:** Luôn đảm bảo rằng bạn sử dụng đúng thanh ghi có cùng kích thước với dữ liệu mà bạn thao tác trong bộ nhớ để tránh lỗi logic.

## indirect-jump
```assembly=
.intel_syntax noprefix

cmp rdi, 3
jg default_case

jmp [rsi + rdi*8]

default_case:
jmp [rsi + 32]
```

![image](https://hackmd.io/_uploads/SJJasOYKJx.png)
## average-loop
```assembly=
.intel_syntax noprefix
mov rbx, rsi
mov rax, 0
mov rdx, 0

loop:
cmp rbx, 0
je done
add rax, [rdi]
add rdi, 8
sub rbx, 1
jmp loop

done:
div rsi
```

![image](https://hackmd.io/_uploads/SyLgZKtFyg.png)

## count-non-zero
```assembly=
.intel_syntax noprefix
mov rax, 0
cmp rdi, 0
je done            
loop:
    cmp qword ptr [rdi], 0   
    je done               
    add rax, 1         
    add rdi, 1            
    jmp loop         
done:
```
![image](https://hackmd.io/_uploads/HJ1KPKYFJx.png)

**Lưu ý:**
- Nếu không có dòng lệnh:
```assembly=
cmp rdi, 0
je done            
```
- Thì kết quả sẽ lỗi:
![image](https://hackmd.io/_uploads/Hk8zOtFtyg.png)
- Kiểm tra nếu **rdi** là `NULL` (tức là địa chỉ trống).
- Nếu `rdi == 0`, nhảy đến `done`, kết thúc chương trình.
## string-lower
**1. Tổng quan về hàm trong Assembly**
- Một hàm là một đoạn mã có thể được gọi (**call**) từ bất kỳ đâu và khi kết thúc nó trả quyền điều khiển lại cho nơi gọi nó.
- Hàm trong Assembly hoạt động giống như trong ngôn ngữ lập trình cấp cao, nhưng thay vì sử dụng return, nó dùng lệnh **ret**.
- Hai lệnh quan trọng để gọi hàm là:
    - `call` – Gọi hàm
    - `ret` – Trả về từ hàm
    
**2. Cách hoạt động của lệnh call**
- Lệnh `call` có hai chức năng chính:
    - Lưu địa chỉ của lệnh tiếp theo vào stack.
    - Nhảy đến địa chỉ của hàm được gọi.
- Ví dụ minh họa lệnh `call`:
```assembly=
0x1021  mov rax, 0x400000   ; Lưu địa chỉ 0x400000 vào thanh ghi RAX
0x1028  call rax            ; Gọi hàm tại địa chỉ 0x400000
0x102a  mov [rsi], rax      ; Lưu giá trị trong RAX vào địa chỉ RSI
```
- **Giải thích:**
    - `mov rax, 0x400000`: Đưa địa chỉ `0x400000` vào thanh ghi `rax`. Đây là địa chỉ của hàm cần gọi.
    - `call rax`: Đẩy (`push`) địa chỉ của lệnh tiếp theo (`0x102a`) vào stack.
    - Nhảy đến địa chỉ `0x400000` (thực thi mã ở đó).
    - Khi hàm kết thúc và gặp `ret`, nó sẽ quay lại `0x102a`
    - Bởi vì địa chỉ `0x102a` đã được đẩy vào stack, `ret` sẽ lấy địa chỉ này ra để tiếp tục chương trình.
    
**3. Cách hoạt động của lệnh ret**
- Lệnh `ret` là ngược lại với `call`:
    - Lấy địa chỉ từ stack ra (`pop`).
    - Nhảy đến địa chỉ đó để tiếp tục thực thi.
- Ví dụ minh họa lệnh `ret`:
```assembly=
0x103f  mov rax, rdx  ; Gán giá trị từ rdx vào rax
0x1042  ret           ; Quay lại địa chỉ đã lưu trên stack
```
- Giải thích:
    - `mov rax, rdx` – Gán giá trị từ `rdx` vào `rax`.
    - `ret` – Lấy địa chỉ từ stack (`0x102a`) và nhảy về đó.
    - Chương trình tiếp tục chạy từ `0x102a`.

**Quy ước gọi hàm System V AMD64 ABI:**
- `rdi`: Chứa tham số thứ nhất (ở đây là con trỏ đến chuỗi cần xử lý).
- `rsi`: Chứa tham số thứ hai (nếu có).
- `rdx, rcx, r8, r9`: Chứa các tham số tiếp theo.
- `rax`: Được dùng để chứa giá trị trả về.
```assembly=
.intel_syntax noprefix
mov rbx, 0x403000  ; Đặt địa chỉ của hàm foo vào RBX
xor rcx, rcx        ; Đặt bộ đếm số ký tự đã thay đổi (i = 0)

cmp rdi, 0          ; Kiểm tra nếu src_addr == 0
je done             ; Nếu src_addr == 0, thoát

loop:
mov al, byte ptr [rdi]  ; Lấy giá trị byte tại địa chỉ rdi (AL = [src_addr])

cmp al, 0               ; Kiểm tra nếu ký tự hiện tại là null ('\0')
je done                 ; Nếu đúng, kết thúc vòng lặp

cmp al, 0x5A            ; Kiểm tra nếu AL > 'Z' (0x5A)
jg skip                 ; Nếu AL > 'Z', bỏ qua chuyển đổi

mov rsi, rdi            ; Lưu giá trị của rdi vào rsi để bảo toàn giá trị
mov dil, al             ; Đặt tham số đầu vào cho foo (dil = al)
call rbx                ; Gọi hàm foo(al), kết quả trả về nằm trong al
mov rdi, rsi            ; Phục hồi giá trị của rdi
mov byte ptr [rdi], al  ; Lưu lại giá trị mới vào vị trí cũ
add rcx, 1              ; Tăng bộ đếm số ký tự đã thay đổi (i += 1)

skip:
add rdi, 1              ; Dịch con trỏ đến ký tự tiếp theo (src_addr += 1)
jmp loop                ; Lặp lại quy trình

done:
mov rax, rcx            ; Đưa số ký tự đã thay đổi vào RAX (giá trị trả về)
ret                     ; Kết thúc hàm
```
![image](https://hackmd.io/_uploads/r1nVRsttyl.png)
## most-common-bytes
```assembly=
.intel_syntax noprefix

push rbp
mov rbp, rsp
sub rsp, 256

xor rcx, rcx
init_list_zero:
mov byte ptr [rbp + rcx - 256], 0
add rcx, 1
cmp rcx, 256
jl init_list_zero

xor rcx, rcx
count:
movzx eax, byte ptr [rdi + rcx]
inc byte ptr [rbp + rax - 256]
inc rcx
cmp rcx, rsi
jl count

xor rcx, rcx
xor rdx, rdx
xor rbx, rbx
find_most_common:
movzx eax, byte ptr [rbp + rcx - 256]
cmp al, dl
jle next

update:
mov dl, al
mov bl, cl

next:
inc rcx
cmp rcx, 256
jl find_most_common

return:
mov al, bl

restore:
mov rsp, rbp
pop rbp
ret
```
- **Cấu trúc Stack Frame:**
    - **push rbp**: Lưu giá trị hiện tại của **rbp** lên stack để bảo toàn nó
    - **mov rbp, rsp**: **rbp = rsp**, thiết lập base pointer để bắt đầu stack frame
    - **sub rsp, 256**: **Cấp phát 256 byte** trên stack (dành cho bảng đếm tần suất).
    - **Mục đích**: Thiết lập stack frame và cấp phát 256 byte để lưu tần suất xuất hiện của các ký tự (vì có tổng cộng 256 giá trị ASCII có thể có).
- **Khởi tạo bảng đếm với giá trị 0**:
    - **xor rcx, rcx**: Đặt **rcx = 0** (dùng làm biến lặp)
    - **mov byte ptr [rbp + rcx - 256], 0**: Đặt giá trị 0 vào từng phần tử trong bảng đếm
    - **add rcx, 1**: rcx++
    - **cmp rcx, 256**: Kiểm tra nếu rcx < 256 thì tiếp tục lặp
    - **jl init_list_zero**: Nhảy về init_list_zero nếu điều kiện đúng
-  **Đếm tần suất xuất hiện của các ký tự trong chuỗi:**
    -  **xor rcx, rcx**: rcx = 0 (đặt lại bộ đếm)
    -  **movzx eax, byte ptr [rdi + rcx]**: Lấy giá trị byte tại địa chỉ rdi + rcx
    -  **inc byte ptr [rbp + rax - 256]**: Tăng giá trị đếm tại vị trí tương ứng trong bảng đếm
    -  **inc rcx**: rcx++
    -  **cmp rcx, rsi**: Kiểm tra nếu **rcx < rsi** (độ dài chuỗi) thì tiếp tục.
    -  **jl count**: Nhảy về count nếu điều kiện đúng.
- **Tìm ký tự xuất hiện nhiều nhất:**
    - **xor rcx, rcx**: rcx = 0 (duyệt qua bảng đếm).
    - **xor rdx, rdx**: rdx = 0 (lưu tần suất lớn nhất).
    - **xor rbx, rbx**: rbx = 0 (lưu ký tự có tần suất lớn nhất).
    **find_most_common:**
    - **movzx eax, byte ptr [rbp + rcx - 256]**: Lấy số lần xuất hiện của ký tự rcx.
    - **cmp al, dl**: So sánh với tần suất lớn nhất hiện tại.
    - **jle next**: Nếu al <= dl, bỏ qua cập nhật.
    **update:**
    - **mov dl, al**: Cập nhật tần suất lớn nhất.
    - **mov bl, cl**: Cập nhật ký tự có tần suất lớn nhất.
    **next:**
    - **inc rcx**: rcx++
    - **cmp rcx, 256**: Kiểm tra nếu rcx < 256 thì tiếp tục.
    - **jl find_most_common**: Nhảy về find_most_common nếu điều kiện đúng.
- **Trả về ký tự xuất hiện nhiều nhất:**
    - **return:**
    - **mov al, bl**: Đưa ký tự xuất hiện nhiều nhất vào al (giá trị trả về)
    **restore:**
    - **mov rsp, rbp**: Phục hồi con trỏ stack
    - **pop rbp**: Khôi phục **rbp** gốc.
    - **ret**: Trả về.
    
![image](https://hackmd.io/_uploads/rkIb53YYye.png)

# Debugging Refresher
## level1
![image](https://hackmd.io/_uploads/HyfjUx5tJe.png)
## level2
- You can see the values for all your registers with `info registers`. 
- Print a particular register's value with the `print` command, or `p` for short. For example, `p $rdi` will print the value of $rdi **in decimal**. 
- You can also print it's **value in hex** with `p/x $rdi`.
![image](https://hackmd.io/_uploads/rkZDPgcKyl.png)

## level3
**Cú pháp lệnh x trong GDB:**
- Lệnh kiểm tra bộ nhớ trong GDB có cú pháp như sau:
`x/<n><u><f> <địa_chỉ>`
- Trong đó:
    - `<n>`: Số lượng phần tử cần hiển thị. (Ví dụ: 8, 16, v.v.)
    - `<u>`: Kích thước của mỗi phần tử (đơn vị). Các đơn vị hợp lệ:
        - b (byte) → 1 byte
        - h (halfword) → 2 byte
        - w (word) → 4 byte
        - g (giant word) → 8 byte
    - `<f>`: Định dạng hiển thị của giá trị:
        - d → số thập phân (decimal)
        - x → số thập lục phân (hexadecimal)
        - s → chuỗi ký tự (string)
        - i → mã lệnh Assembly (instruction)
    - `<địa_chỉ>`: Vị trí bộ nhớ cần kiểm tra. Có thể là:
        - Một thanh ghi (ví dụ: $rip, $rsp, $rbp, v.v.)
        - Một tên biến hoặc hàm (ví dụ: main)
        - Một địa chỉ tuyệt đối (ví dụ: 0x601048)
        - Một biểu thức toán học (ví dụ: $rbp - 0x32)
- Hiển thị đúng cú pháp Assembly (Intel)
Mặc định, GDB có thể hiển thị mã Assembly theo cú pháp AT&T.
- Để chuyển sang cú pháp Intel, chạy lệnh sau trong GDB: `set disassembly-flavor intel`
- Để chuyển sang Intel mặc định thì ta có thể làm như sau: `nano ~./gdbinit`
```assembly=
set disassemble-flavor intel
source /opt/gef/gef.py
```
![image](https://hackmd.io/_uploads/rk14JZctJl.png)
![image](https://hackmd.io/_uploads/rkirkb9Yyx.png)
## level4
- Có nhiều cách để move forward trong quá trình thực thi của chương trình:
1. **Lệnh stepi <n> hoặc si <n>**
    - Dùng để bước tới từng lệnh một trong chương trình.
    - Nếu không có <n>, chương trình sẽ chỉ tiến 1 lệnh.
    - Nếu có <n>, chương trình sẽ tiến n lệnh liên tiếp.
2. **Lệnh nexti <n> hoặc ni <n>**
    - Giống stepi, nhưng bỏ qua (step over) các lời gọi hàm (function calls).
    - Nếu một lệnh là lời gọi hàm, stepi sẽ nhảy vào (step into) hàm đó, nhưng nexti thì không.
3. **Lệnh finish**
    - Hoàn tất (finish) hàm hiện tại và tiếp tục thực thi từ dòng lệnh kế tiếp.
4. **Lệnh break <địa_chỉ>**
    - Đặt breakpoint tại địa chỉ cụ thể.
    - Khi chương trình chạy đến địa chỉ này, nó sẽ tạm dừng để bạn có thể kiểm tra trạng thái.
5. **Lệnh continue**
    - Tiếp tục thực thi cho đến khi gặp một breakpoint khác.
6. **Quan sát giá trị quan trọng khi debug**
    - Lệnh `display/<n><u><f> <đối_tượng>`
    - Tương tự như lệnh `x/<n><u><f>` để kiểm tra bộ nhớ.
    - Ví dụ: display/8i $rip → Hiển thị 8 lệnh kế tiếp trong chương trình (dựa vào RIP).
7. **Lệnh layout regs**
    - Đưa GDB vào chế độ TUI mode, hiển thị nội dung tất cả các thanh ghi (registers) cùng với lệnh gần đó.

![image](https://hackmd.io/_uploads/Hk6vsXqtke.png)
![image](https://hackmd.io/_uploads/BJN5sQcYJg.png)
![image](https://hackmd.io/_uploads/SJiiim5Kye.png)
![image](https://hackmd.io/_uploads/BJ32jQ9Kkg.png)
![image](https://hackmd.io/_uploads/H1taom5KJl.png)
## level5
- Cách chạy script GDB: Viết các lệnh GDB vào một file, ví dụ: x.gdb. Sau đó chạy: `gdb -x x.gdb`
    → GDB sẽ thực thi toàn bộ các lệnh trong file này ngay sau khi khởi động.
- Chạy lệnh GDB riêng lẻ bằng -ex
    - Ví dụ, chạy một lệnh khi mở GDB: `gdb -ex 'break main' -ex 'run'`
    - Có thể truyền nhiều lệnh bằng nhiều -ex.
    - Thiết lập lệnh GDB chạy mặc định trong mọi phiên
- Viết các lệnh vào file ~/.gdbinit.
    - Ví dụ: `set disassembly-flavor intel`
    - Mỗi lần mở GDB, các lệnh trong file này sẽ được thực thi.
- Lệnh Breakpoint trong GDB Script
    - Một tính năng cực kỳ mạnh mẽ trong GDB scripting là Breakpoint Commands. Điều này cho phép bạn định nghĩa tập lệnh tự động chạy mỗi khi GDB dừng tại một breakpoint.
    - Ví dụ 1: In giá trị của biến cục bộ khi đến một breakpoint
```assembly=
break *main+42
commands
  x/gx $rbp-0x32
  continue
end
continue
```
- Giải thích:
    - `start` → Khởi động chương trình.
    - `break *main+42` → Đặt breakpoint tại địa chỉ main+42.
    - `commands ... end` → Định nghĩa tập lệnh thực thi khi breakpoint bị dừng.
    - `x/gx $rbp-0x32` → In giá trị của một biến cục bộ trên stack.
    - `continue` → Tiếp tục thực thi chương trình sau khi in xong.
- Ví dụ 2: GDB Script nâng cao với biến tạm thời
```assembly=
start
break *main+42
commands
  silent
  set $local_variable = *(unsigned long long*)($rbp-0x32)
  printf "Current value: %llx\n", $local_variable
  continue
end
continue
```
- Giải thích:
    - `silent` → Không hiển thị thông báo khi chạm breakpoint (giúp output gọn gàng).
    - `set $local_variable = *(unsigned long long*)($rbp-0x32)`: Định nghĩa một biến $local_variable.
    - Lấy giá trị từ địa chỉ `$rbp-0x32` và gán vào biến này.
    - `printf "Current value: %llx\n", $local_variable` → In giá trị biến ra màn hình.
    - `continue` → Tiếp tục thực thi chương trình sau khi in xong.
- Làm tương tự level trước hoặc có thể viết script.

| Cú pháp | Chức năng | 
| -------- | -------- |
| `break <địa chỉ>`     | Đặt breakpoint     | 
| `commands ... end`    | Lệnh tự động khi gặp breakpoint   | 
| `silent`   | Ẩn thông báo khi dừng tại breakpoint     | 
| `set <var> = <giá trị>`    | Gán giá trị cho biến     | 
| `while <điều kiện> ... end`     | Lặp trong GDB    | 
| `if <điều kiện> ... else ... end`   | Điều kiện     | 
| `call <hàm>`     | Gọi hàm trong chương trình     | 
| `x/<n><format> <địa chỉ>`    | Xem bộ nhớ     | 


![image](https://hackmd.io/_uploads/ryGcG49YJg.png)
## level6
![image](https://hackmd.io/_uploads/HJHA8N5Fkl.png)
## level7
![image](https://hackmd.io/_uploads/BkpWLNqYyg.png)
## level8
![image](https://hackmd.io/_uploads/Bk1GFVqt1l.png)

# Building A Web Server
## level1
```
In this series of challenges, you will be writing assembly to interact with your environment, and ultimately build a web server
In this challenge you will exit a program.

Usage: `/challenge/run <path_to_web_server>`

$ cat server.s
.intel_syntax noprefix
.globl _start

.section .text

_start:
    mov rdi, 0
    mov rax, 60     # SYS_exit
    syscall

.section .data

$ as -o server.o server.s && ld -o server server.o

$ strace ./server
execve("./server", ["./server"], 0x7ffccb8c6480 /* 17 vars */) = 0
exit(0)                                 = ?
+++ exited with 0 +++

$ /challenge/run ./server
```
![image](https://hackmd.io/_uploads/SJbcSK3Kyl.png)
![image](https://hackmd.io/_uploads/S1iiHFhF1x.png)
![image](https://hackmd.io/_uploads/H1AhBYnFkg.png)

**To be continue...**