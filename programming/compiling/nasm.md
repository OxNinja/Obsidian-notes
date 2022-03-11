#assembly #low-level 
[[Assembly (language)]] for [[Linux]] x86 64bits
https://nasm.us/doc/nasmdoc3.html

```asm
global _start

section .rodata
txt: db "Hello world", 0xa
size: equ $-txt

section .text
_start:
	mov rax, 1
	mov rsi, 1
	mov rdi, txt
	mov rdx, size
	syscall
```
## Registers
| Register | Purpose       |
| -------- | ------------- |
| rax      | Result        |
| rbx      | Scratch       |
| rcx      | Argument 4    |
| rdx      | Argument 3    |
| rsi      | Argument 2    |
| rdi      | Argument 1    |
| rbp      | Base Pointer  |
| rsp      | Stack Pointer |
| r8       | Argument 5    |
| r9       | Argument 6    |
| r10      | Scratch       |
| r11      | Scratch       |
| r12      | Scratch       |
| r13      | Scratch       |
| r14      | Scratch       |
| r15      | Scratch       |

## Declaring
`db`: declare byte (1 byte)
`dw`: declare word (2 bytes)
`dd`: declare dword (4 bytes)
`times n`: declare n times the var (`times 3 db 0x01` == `db 0x01 0x01 0x01`)
`$$`: beginning of current section