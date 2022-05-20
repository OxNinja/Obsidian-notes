#reverse #low-level 

A valid NES header
```asm
4e 45 53 1a ; magic number
; PRG
; CHR
XY ; Mapper 0-15 - X: mapper number, Y: 
; Mapper 16-?
00 00 00 00 00 00 00 00 ; pad

```
