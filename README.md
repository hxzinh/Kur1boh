# Write-up for [Assembly Crash Courses](https://pwn.college/cse466-f2022/assembly-crash-course)

## Challenge_1: Set a register
Ở bài đầu tiền chúng ta sẽ tập làm quen với thanh ghi. Đề bài yêu cầu:
```
In this level you will work with registers_use! Please set the following:
* rdi = 0x1337
```
Chúng ta đơn giản chỉ cần gán rdi = 0x1337:  
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
Bài đã set sẵn cho ta thanh ghi rdi = 0x99. Ta chỉ cần cộng 0x331337 vào thanh ghi rdi.
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

## Challenge_5
## Challenge_6
## Challenge_7
## Challenge_8
## Challenge_9
## Challenge_10
## Challenge_11
## Challenge_12
## Challenge_13
## Challenge_14
## Challenge_15
## Challenge_16
## Challenge_17
## Challenge_18
## Challenge_19
## Challenge_20
## Challenge_21
## Challenge_22
## Challenge_23
