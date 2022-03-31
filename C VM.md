#emulation #c 
My [[Virtual Machine]] in C

``` 
      if isReg2: regId2
      else: value left-padded with 0
      |
    if isReg: regId
    | |
  OPcode
  | | |
0x1234567
   | |
   isReg?
     |
     if isReg: isReg2?
```

Depending on the OPcode, there will be a different parsing (like `call` is different than `mov` or `inc`)

First making an [[Assembler]] to process the thing and then making the [[Disassembler]] to execute the VM.
## Assembler
