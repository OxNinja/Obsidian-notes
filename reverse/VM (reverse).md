#reverse #assembly 
A VM is to emulate custom instructions in order to emulate some architectures or break reversers' brains

[[Disassembler]] evasion technique for [[Reverse]]

* custom syscalls
* custom instructions
* just compare `cmp rax, $val` and jump where to go `je _my_custom_mov_rbx_r8`

https://github.com/OxNinja/nasm_/blob/main/vm/vm.asm
