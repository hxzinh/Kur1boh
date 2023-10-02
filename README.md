# Write-up for [Assembly Crash Courses](https://pwn.college/cse466-f2022/assembly-crash-course)

## Challenge_1: Set a register
Ở bài đầu tiền chúng ta sẽ tập làm quen với thanh ghi. Đề bài yêu cầu:
```
In this level you will work with registers_use! Please set the following:
* rdi = 0x1337
```
Chúng ta đơn giản chỉ cần gán ```rdi = 0x1337```:  
```asm
mov rdi, 0x1337
```
## Challenge_2: Addition
Ở challange 2 này chúng ta sẽ làm việc với phép cộng. Đề bài như sau:
```
Do the following:
* add 0x331337 to rdi

We will now set the following in preparation for your code:
rdi = 0xc99
```
Bài đã set sẵn cho ta thanh ghi ```rdi = 0x99```. Ta chỉ cần cộng 0x331337 vào thanh ghi rdi.
```asm
add rdi, 0x331337
```
## Challenge_3: Multiplication
Đề bài như sau:
```
Using your new knowledge, please compute the following:
f(x) = mx + b, where:
m = rdi
x = rsi
b = rdx
Place the value into rax given the above.
We will now set the following in preparation for your code:
rdi = 0x8ce
rsi = 0x1bd1
rdx = 0x1feb
```
Ta phải tính phương trình f(x) đề cho và lưu vào rax. Ta chỉ cần gán rdi vào rax và thực hiện các phép toán từ trái sang phải.
```asm
mov rax, rdi
imul rax, rsi
add rax, rdx
```
## Challenge_4: Division
Đề bài như sau:
```
Please compute the following:
speed = distance / time, where:
distance = rdi
time = rsi
Place the value of speed into rax given the above.
We will now set the following in preparation for your code:
rdi = 0x64f
rsi = 0x5f
```
Khi thực hiện phép chia div regX sẽ mặc định là ```rax / regX``` và kết quả được lưu vào rax (phần dư lưu trong rdx) nên ta cần gán ```rax = rdi``` và thực hiện chia cho ```rsi```
```asm
mov rax, rdi
div rsi
```

## Challenge_5: Modulus
Đề bài như sau:
```
Please compute the following:
rdi % rsi
Place the value in rax.

We will now set the following in preparation for your code:
rdi = 0x39a66f69
rsi = 0x3f
```
Như đã nói ở challenge trước khi thực hiện ```reg1 / reg2``` thì ans được lưu trong rax và phần dư lưu trong rdx. Challenge này ta chỉ cần thực hiện chia như challenge4 và gán ```rax = rdx``` để lấy phần dư.
```asm
mov rax, rdi
div rsi
mov rax, rdx
```
## Challenge_6: Register sizes
Đề bài như sau:
```
Using only the following instruction(s)
mov
Please compute the following:
rax = rdi modulo 256
rbx = rsi modulo 65536

We will now set the following in preparation for your code:
rdi = 0x2177
rsi = 0xdf5418a7
```
Đề yêu cầu tính ```rdi % 2^8``` và ```rsi % 2^16```.
Một thanh ghi quản lí tổng cộng 64 bits và chúng ta có thể truy cập và những bits thấp hơn của thanh ghi bằng cách gọi những tên khác của thanh ghi như:
```
+----------------------------------------+
|               rax (64 bits)            |
+--------------------+-------------------+
                     |    eax (32 bits)  |
                     +---------+---------+
                               |   ax    |
                               +----+----+
                               | ah | al |
                               +----+----+
```
Vậy nên ta chỉ cần lấy phần ```al``` ở ```rax``` và ```si``` ở ```rsi``` là được.
```asm
mov al, dil
mov bx, si
```
## Challenge_7: Bitwise shift
Đề bài như sau:
```
Using only the following instructions:
mov, shr, shl
Please perform the following:
Set rax to the 5th least significant byte of rdi
i.e.
rdi = | B7 | B6 | B5 | B4 | B3 | B2 | B1 | B0 |
Set rax to the value of B4

We will now set the following in preparation for your code:
rdi = 0x91affdcbc43713f0
```
Đề yêu cầu ta gán rax = byte thứ 5 của rdi. Vì không thể trực tiếp lấy byte thứ 5 nên ta sẽ dịch byte thứ 5 lên đầu và gán qua rax như ở challenge 6.
```asm
shr rdi, 32
mov al, dil
```
## Challenge_8: Bitwise and
Đề như sau:
```
Without using the following instructions:
mov, xchg
Please perform the following:
rax = rdi AND rsi
i.e. Set rax to the value of (rdi AND rsi)

We will now set the following in preparation for your code:
rdi = 0x72a2e58ce98b4813
rsi = 0x67bba0a6868b145c
```
Một số vị dụ về toán tử logic:
```
    AND          OR           XOR
 A | B | X    A | B | X    A | B | X
---+---+---  ---+---+---  ---+---+---
 0 | 0 | 0    0 | 0 | 0    0 | 0 | 0
 0 | 1 | 0    0 | 1 | 1    0 | 1 | 1
 1 | 0 | 0    1 | 0 | 1    1 | 0 | 1
 1 | 1 | 1    1 | 1 | 1    1 | 1 | 0
```

Trong bài này ta không được dùng lệnh ```mov``` nên ta có thể thay thế bằng lệnh ```xor```, với ```rax = 0``` thì ```rax xor rdi = rdi```.
```asm
xor rax, rax     ;make sure that rax = 0
xor rax, rdi
and rax, rsi
```

## Challenge_9: Bitwise logic
Đề như sau:
```
Using only the following instructions:
and, or, xor
Implement the following logic:

if x is even then
  y = 1
else
  y = 0
where:
x = rdi
y = rax

We will now set the following in preparation for your code:
rdi = 0x3292dda5
```
Chúng ta kiểm tra tính chẵn lẻ của rdi bằng cách:
* Gán ```rax = rdi```.
* Chỉnh bit đầu tiên của ```rdi``` luôn bằng 1.
* Xor ```rax``` và ```rdi``` để lấy kết quả.
```asm
xor rax, rax    ;make sure that rax = 0
xor rax, rdi
or rdi, 1
xor rax, rdi
```
## Challenge_10: Memory reads and writes
Đề bài như sau:
```
Please perform the following:
1. Place the value stored at 0x404000 into rax
2. Increment the value stored at the address 0x404000 by 0x1337
Make sure the value in rax is the original value stored at 0x404000 and make sure
that [0x404000] now has the incremented value.

We will now set the following in preparation for your code:
[0x404000] = 0x19be30
```

```asm
mov rax, [0x404000]
mov rbx, 0x1337
add [0x404000], rbx
```
## Challenge_11: Data sizes
Đề bài như sau:
```
Please perform the following:
1) Set rax to the byte at 0x404000
2) Set rbx to the word at 0x404000
3) Set rcx to the double word at 0x404000
4) Set rdx to the quad word at 0x404000

We will now set the following in preparation for your code:
[0x404000] = 0x1aea24
```
Hint:
```
Recall that registers in x86_64 are 64 bits wide, meaning they can store 64 bits in them.
Similarly, each memory location is 64 bits wide. We refer to something that is 64 bits
(8 bytes) as a quad word. Here is the breakdown of the names of memory sizes:
* Quad Word = 8 Bytes = 64 bits
* Double Word = 4 bytes = 32 bits
* Word = 2 bytes = 16 bits
* Byte = 1 byte = 8 bits
In x86_64, you can access each of these sizes when dereferencing an address, just like using
bigger or smaller register accesses:
mov al, [address]        <=>         moves the least significant byte from address to rax
mov ax, [address]        <=>         moves the least significant word from address to rax
mov eax, [address]        <=>         moves the least significant double word from address to rax
mov rax, [address]        <=>         moves the full quad word from address to rax
Remember that moving only into al for instance does not fully clear the upper bytes.
```
Ta chỉ cần làm theo đề dự vào hint trong bài
```asm
mov al, [0x404000]
mov bx, [0x404000]
mov ecx, [0x404000]
mov rdx, [0x404000]
```
## Challenge_12: Dynamic address memory writes
Đề bài như sau:
```
For this challenge we will give you two addresses created dynamically each run. The first address
will be placed in rdi. The second will be placed in rsi.
Using the earlier mentioned info, perform the following:
1. set [rdi] = 0xdeadbeef00001337
2. set [rsi] = 0xc0ffee0000
Hint: it may require some tricks to assign a big constant to a dereferenced register. Try setting
a register to the constant then assigning that register to the derefed register.

We will now set the following in preparation for your code:
[0x404260] = 0xffffffffffffffff
[0x4049f8] = 0xffffffffffffffff
rdi = 0x404260
rsi = 0x4049f8
```

```asm
mov rax, qword 0xdeadbeef00001337
mov rbx, qword 0xc0ffee0000
mov [rdi], rax
mov [rsi], rbx
```
## Challenge_13: Consecutive memory reads
Đề bài như sau:
```
Perform the following:
1. load two consecutive quad words from the address stored in rdi
2. calculate the sum of the previous steps quad words.
3. store the sum at the address in rsi

We will now set the following in preparation for your code:
[0x4041e8] = 0x9bd96
[0x4041f0] = 0x20417
rdi = 0x4041e8
rsi = 0x404670
```
Challenge yêu cầu ta tính tổng của 2 giá trị ở 2 địa chỉ cho trước. Một ở ```rdi``` và một ở ```rdi + 8```.
```asm
mov rax, [rdi]
add rax, [rdi + 8]
mov [rsi], rax
```
## Challenge_14: The stack
Đề bài như sau:
```
Replace the top value of the stack with (top value of the stack - rdi).
We will now set the following in preparation for your code:
rdi = 0x1765
(stack) [0x7fffff1ffff8] = 0x3259ce5
```
Ở bài này ta sẽ làm quen với stack. Stack là một cấu trúc dữ liệu quen thuộc với kiểu lưu trữ LIFO (Last in First out). Hiểu đơn giản là thằng nào vào sau thì ra trước như xếp chén vậy thằng nào xếp sau ở trên sẽ được lấy ra trước rồi mới tới những thằng ở dưới.

Bài này yêu cầu chỉnh giá trị ở đầu stack về ```top value of the stack - rdi``` nên ta chỉ cần pop giá trị ra trừ đi ```rdi``` và push vào lại là được.
```asm
pop rax
sub rax, rdi
push rax
```
## Challenge_15: Swap register values with the stack
Đề bài như sau:
```
Using only following instructions:
push, pop
Swap values in rdi and rsi.
i.e.
If to start rdi = 2 and rsi = 5
Then to end rdi = 5 and rsi = 2

We will now set the following in preparation for your code:
rdi = 0x3ab9d1ed
rsi = 0xad836d7
```
Ở bài này ta sẽ sử dụng cơ chế LIFO của stack để swap hai thanh ghi ```rdi``` và ```rsi```. 
Ta push hai thanh ghi vào stack theo thứ tự rồi pop ra với thứ tự tương tự, ```push rdi```, ```rsi``` thì ```pop rdi``` rồi đến ```rsi```
```asm
push rdi
push rsi
pop rdi
pop rsi
```
## Challenge_16: Memory reads and writes with the stack
Đề bài như sau:
```
Without using pop please calculate the average of 4 consecutive quad words stored on the stack.
Push the average on the stack. Hint:
RSP+0x?? Quad Word A
RSP+0x?? Quad Word B
RSP+0x?? Quad Word C
RSP      Quad Word D

We will now set the following in preparation for your code:
(stack) [0x7fffff200000:0x7fffff1fffe0]
= ['0x1216cdb5', '0x5e5b770', '0x29e46e6b', '0x1ce25588'] (list of things)
```
Challenge này yêu cầu tính trung bình cộng của 4 quad words liên tiếp trên stack. Ta chỉ cần để ý 1 quad word = 8 bytes và các giá trị được lưu liền kề nhau nên giá trị đầu sẽ là ```[rsp]```, giá trị thứ hai sẽ là ```[rsp + 8]```, ...etc
```asm
mov rax, [rsp]
add rax, [rsp + 8]
add rax, [rsp + 16]
add rax, [rsp + 24]
mov rbx, 4
div rbx
push rax
```
## Challenge_17: Control flow
Đề bài như sau:
```
Useful instructions for this level is:
jmp (reg1 | addr | offset) ; nop
Hint: for the relative jump, lookup how to use `labels` in x86.

Using the above knowledge, perform the following:
Create a two jump trampoline:
1. Make the first instruction in your code a jmp
2. Make that jmp a relative jump to 0x51 bytes from its current position
3. At 0x51 write the following code:
4. Place the top value on the stack into register rdi
5. jmp to the absolute address 0x403000

We will now set the following in preparation for your code:
- Loading your given code at: 0x4000ce
- (stack) [0x7fffff1ffff8] = 0xeb
```
```asm
jmp here
times 0x51 nop
here:
pop rdi
mov rax, 0x403000
jmp rax
```
## Challenge_18: Conditional branches
Đề bài như sau:
```
Using the above knowledge, implement the following:
if [x] is 0x7f454c46:
   y = [x+4] + [x+8] + [x+12]
else if [x] is 0x00005A4D:
   y = [x+4] - [x+8] - [x+12]
else:
   y = [x+4] * [x+8] * [x+12]
where:
x = rdi, y = rax. Assume each dereferenced value is a signed dword. This means the values can start as
a negative value at each memory position.
A valid solution will use the following at least once:
jmp (any variant), cmp

We will now run multiple tests on your code, here is an example run:
- (data) [0x404000] = {4 random dwords]}
- rdi = 0x404000
```
```asm
section.text
    global _start

_start: 
    mov eax, [rdi]

    cmp eax, 0x7f454c46
    je this_one
    cmp eax, 0x00005A4D
    je this_two

    mov eax, dword [rdi + 4]
    imul eax, dword [rdi + 8]
    imul eax, dword [rdi + 12]
    jmp end

this_one:
    mov eax, dword [rdi + 4]
    add eax, dword [rdi + 8]
    add eax, dword [rdi + 12]
    jmp end

this_two:
    mov eax, dword [rdi + 4]
    sub eax, dword [rdi + 8]
    sub eax, dword [rdi + 12]
    jmp end

end:


```
## Challenge_19: Jump tables
Đề như sau:
```
Using the above knowledge, implement the following logic:
if rdi is 0:
    jmp 0x403031
else if rdi is 1:
    jmp 0x4030d2
else if rdi is 2:
    jmp 0x40319c
else if rdi is 3:
    jmp 0x40329f
else:
    jmp 0x40334c
Please do the above with the following constraints:
- assume rdi will NOT be negative
- use no more than 1 cmp instruction
- use no more than 3 jumps (of any variant)
- we will provide you with the number to 'switch' on in rdi.
- we will provide you with a jump table base address in rsi.

Here is an example table:
    [0x404344] = 0x403031 (addrs will change)
    [0x40434c] = 0x4030d2
    [0x404354] = 0x40319c
    [0x40435c] = 0x40329f
    [0x404364] = 0x40334c
```
```asm
section.text
    global _start

_start: 
    cmp rdi, 3
    jle here
    jmp [rsi + 32]

here:   
    jmp [rsi + rdi * 8]
```
## Challenge_20: Computing averages
Đề bài như sau:
```
Please compute the average of n consecutive quad words, where:
rdi = memory address of the 1st quad word
rsi = n (amount to loop for)
rax = average computed

We will now set the following in preparation for your code:
- [0x404118:0x404350] = {n qwords]}
- rdi = 0x404118
- rsi = 71
```
```asm
section.text
    global _start

_start: 
    mov rax, 0
    mov rbx, rsi
    call get_sum

get_sum:
    add rax, [rdi]
    add rdi, 8
    dec rsi
    cmp rsi, 0
    jne get_sum
    jmp end

end:
    mov rdx, 0
    div rbx
```
## Challenge_21: Implementing strlen
Đề như sau:
```
Using the above knowledge, please perform the following:
Count the consecutive non-zero bytes in a contiguous region of memory, where:
rdi = memory address of the 1st byte
rax = number of consecutive non-zero bytes
Additionally, if rdi = 0, then set rax = 0 (we will check)!
An example test-case, let:
rdi = 0x1000
[0x1000] = 0x41
[0x1001] = 0x42
[0x1002] = 0x43
[0x1003] = 0x00
then: rax = 3 should be set

We will now run multiple tests on your code, here is an example run:
- (data) [0x404000] = {10 random bytes},
- rdi = 0x404000
```
```asm
section.text
    global _start

_start:
    mov rax, 0
    cmp rdi, 0
    jne loop
    jmp end

loop:
    mov bl, [rdi]
    cmp bl, 0
    je end

    inc rax
    inc rdi

    jmp loop

end:
```
## Challenge_22: Using library functions
Đề bài như sau:
```
Please implement the following logic:
str_lower(src_addr):
    i = 0
    if src_addr != 0:
        while [src_addr] != 0x00:
            if [src_addr] <= 0x5a:
                [src_addr] = foo([src_addr])
                i += 1
            src_addr += 1
    return i

foo is provided at 0x403000. foo takes a single argument as a value and returns a value.

All functions (foo and str_lower) must follow the Linux amd64 calling convention (also known as System V AMD64 ABI):
https://en.wikipedia.org/wiki/X86_calling_conventions#System_V_AMD64_ABI

Therefore, your function str_lower should look for src_addr in rdi and place the function return in rax.

An important note is that src_addr is an address in memory (where the string is located) and [src_addr] refers to the byte that exists at src_addr.

Therefore, the function foo accepts a byte as its first argument and returns a byte.

We will now run multiple tests on your code, here is an example run:
- (data) [0x404000] = {10 random bytes},
- rdi = 0x404000
```
```asm
section.text
    global _start

_start: 
    mov rax, 0
    mov rbx, 0
    cmp rdi, 0
    je end
    jmp str_lower

str_lower:
    mov rbx, 0
    mov bl, byte [rdi]

    cmp rbx, 0
    je end

    cmp rbx, 0x5a
    jg skip

    push rdi
    push rax
    mov rdi, qword 0
    mov dil, bl

    mov rcx, qword 0x403000
    call rcx
    mov bl, al 
    pop rax
    pop rdi
    mov [rdi], bl

    inc rax
    jmp skip

skip:
    inc rdi
    jmp str_lower

end:
    ret
```
## Challenge_23: Compute the most common byte
```
Notice how rbp is always used to restore the stack to where it originally was. If we don't restore
the stack after use, we will eventually run out TM. In addition, notice how we subtracted from rsp
since the stack grows down. To make it have more space, we subtract the space we need. The ret
and call still works the same. It is assumed that you will never pass a stack address across functions,
since, as you can see from the above use, the stack can be overwritten by anyone at any time.
Once, again, please make function(s) that implements the following:
most_common_byte(src_addr, size):
    i = 0
    for i <= size-1:
        curr_byte = [src_addr + i]
        [stack_base - curr_byte] += 1

    b = 0
    max_freq = 0
    max_freq_byte = 0
    for b <= 0xff:
        if [stack_base - b] > max_freq:
            max_freq = [stack_base - b]
            max_freq_byte = b
        b += 1

    return max_freq_byte

Assumptions:
- There will never be more than 0xffff of any byte
- The size will never be longer than 0xffff
- The list will have at least one element
Constraints:
- You must put the "counting list" on the stack
- You must restore the stack like in a normal function
- You cannot modify the data at src_addr
```
```asm
section.text
    global _start

_start:
    push rbp
    mov rbp, rsp
    sub rsp, 0xff
    
    mov rax, 0

init: 
    mov rdx, rbp
    sub rdx, rbx
    mov [rdx], rax
    inc rbx
    cmp rbx, 0xfe
    jne init

    mov rax, 1

count_byte:
    dec rsi
    mov bl, byte [rdi + rsi]
    mov rcx, rbp
    sub rcx, rbx
    add [rcx], rax

    cmp rsi, 0
    jne count_byte

    mov rax, 0
    mov rbx, 0
    mov rcx, 0
    
get_ans:
    mov rdx, 0
    mov rdx, rbp
    sub rdx, rbx
    cmp byte [rdx], cl
    jle skip
    mov rcx, [rdx]
    mov al, bl

skip:
    inc rbx
    cmp rbx, 0xfe
    jle get_ans
    jmp end

end:
    mov rsp, rbp
    pop rbp 
    ret
```
