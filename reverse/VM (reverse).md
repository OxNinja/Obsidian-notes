#reverse #assembly #low-level 
A VM is to emulate custom instructions in order to emulate some architectures or break reversers' brains

[[Disassembler]] evasion technique for [[Reverse]]

* custom syscalls
* custom instructions
* just compare `cmp rax, $val` and jump where to go `je _my_custom_mov_rbx_r8`

https://github.com/OxNinja/nasm_/blob/main/vm/vm.asm

From [[tmp.out]]
https://tmpout.sh/2/7.html
`%define MOV_Rx_Ry(x,y) db 0x04,x,y`
```asm
; now, the spider:
spider:
  mov rbx, qword [rdi+rax] ; rbx contains the current virtual opcode

  cmp bl, 0x1 ; NOP1
  je handlers_table.NOP
  cmp bl, 0x2 ; PUSHR
  je handlers_table.PUSH_Reg
  cmp bl, 0x3 ; POP_R
  je handlers_table.POP_Reg
  cmp bl, 0x4 ; MOV_Reg_to_Reg
  je handlers_table.MOV_Reg_to_Reg
  ...
  cmp bl, 0x20
  je handlers_table.JMPNEG

  .cmp_end:
    cmp rax, virus_end-code-5
    jl spider
```

```asm
.MOV_Reg_to_Reg:
  ; first we clean the registers
  xor rcx, rcx
  xor rdx, rdx

  mov cl, byte [rdi+rax+1] ; rcx = Rx
  mov dl, byte [rdi+rax+2] ; rdx = Ry

  push rbx
  mov rbx, qword [rsi+rdx*8] ; move to into rbx, the value stored in Rx
  mov qword [rsi+rcx*8], rbx ; move rbx into Ry
  pop rbx

  add rax, 0x8 ; pc += 8 (easy because each instruction is made of 8 bytes)
  jmp spider.cmp_end ; return to spider
  ```
