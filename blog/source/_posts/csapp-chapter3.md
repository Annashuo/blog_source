---
title: csapp-chapter3
date: 2017-03-06 11:25:23
tags: csapp
categories: STUDY
---

Chapter 3 REVIEW

<!--more-->
### data formats
 | | size
:----:|:----:|:----:
byte | b | 1 byte
word | w | 2 bytes
double word | l |4 bytes
quad word | q | 8 bytes
### integer registers
%rax | %eax | %ax | %al |return value
:----:|:----:|:----:|:----:|:----:
%rbx | %ebx | %bx | %bl | callee saved 
%rcx | %ecx | %cx | %cl | 
%rdx | %edx | %dx | %dl | 
%rsi | %esi | %si | %sil | source
%rdi | %edi | %di | %dil | destination
%rbp | %ebp | %bp | %bpl | callee saved
%rsp | %esp | %sp | %spl | stack pointer
%r8 | %r8d | %r8w | %r8b | 
%r9 | %r9d | %r9w | %r9b |
%r10 | %r10d | %r10w | %r10b | caller saved
%r11 | %r11d | %r11w | %r11b | caller saved 
%r12 | %r12d | %r12w | %r12b | callee saved
%r13 | %r13d | %r13w | %r13b | callee saved
%r14 | %r14d | %r14w | %r14b | callee saved
%r15 | %r15d | %r15w | %r15b | callee saved
### operand specifiers
 instructions | effect | description
:----:|:----:|:----:
mov s,d | d<-s | move
movz s,r | r<-zeroextend(s) | move with zero extension
movs s,r | r<-signextend(s) | move with sign extension
pushq s | R[%rsp]<-R[%rsp]-8; M[R[%rsp]]<-s | push quad word
popq d | d<-M[R[%rsp]]; R[%rsp]<-R[%rsp]+8 | pop quad word
leaq s,d | d<-&s | load effective address
inc d | d<-d+1 | increment
dec d | d<-d-1 | decrement
neg d | d<- -d | negate
not d | d<-~d | complement
add s,d | d<-d+s | add
sub s,d | d-<d-s | subtract
imul s,d | d<-mul(d,s) | miltiply
xor s,d | d<-d^s | exclusive-or
or s,d | d<-d|s | or
and s,d | d<-d&s | and
sal k,d | d<d<<k |left shift
sar k,d | d<-d>>(a)k | arithmetic right shift
shr k,d | d<-d>>(l)k | logical right shift
cmp s1,s2 | s2-s1 | compare
test s1,s2 | s1&s2 | test


instructions | instructions | instructions | description
:----:|:----:|:----:|:----:
sete d = setz d | je Label | cmove s,r | zero
setne d = setnz d | jne Label | cmovne s,r | not zero
sets d | js Label | cmovs s,r | negative
setns d | jns Label | cmovns s,r| nonnegative
setg d | jg Label | cmovg s,r | signed >
setge d | jge Label | cmovge s,r | signed >=
setl d | jl Label | cmovl s,r | signed <
setle d | jle Label | cmovle s,r | signed <=
seta d | ja Label | cmova s,r | unsigned >
setae d | jae Label | cmovae s,r | unsigned >=
setb d | jb Label | cmovb s,r | unsigned <
setbe d | jbe Label | cmovbe s,r | unsigned <=
jmp Label |  |  | direct jump

### data alignment
k bytes 的数据类型的地址必须为k的倍数
### buffer overflow
- gets(),strcpy(),strcat(),sprintf()均有可能导致buffer overflow
- thwarting buffer overflow attacks
	- stack randomization
	- stack corruption detection(canary)
	- limiting executable code regions
 