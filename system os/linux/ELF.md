#linux #elf #binary #low-level 
Les fichiers binaires compilés pour [[Linux]]
Magic number : `7f 45 4c 46`
## Structure
![](https://upload.wikimedia.org/wikipedia/commons/thumb/7/77/Elf-layout--en.svg/260px-Elf-layout--en.svg.png)
![](https://upload.wikimedia.org/wikipedia/commons/e/e4/ELF_Executable_and_Linkable_Format_diagram_by_Ange_Albertini.png)

## Headers
[See Wikipedia](https://en.wikipedia.org/wiki/Executable_and_Linkable_Format#File_header)

On a la table des headers (qui suit généralement le header de l'ELF -- donc un offset de `0x34` pour 32bits et `0x40` pour 64)
Le consensus veut que la table des headers soit à la suite directement du header ELF, et que la table des sections soit à la fin du binaire.

`objdump -x test.o` : avoir les sections et les informations associées

Note : ne pas oublier de mettre les permissions aux segments car bah... sinon le code s'exécute pas et on a droit à un joli SEGFAULT.

[Flags des segments](https://docs.oracle.com/cd/E19683-01/816-1386/6m7qcoblk/index.html#chapter6-tbl-39)

## Sections
> Les sections sont là pour mettre de l'ordre et des conventions, mais en vrai on s'en fout un peu (c'est comme `<header>`, `<footer>`, etc en HTML 🤫)
### `.text`
Le code, les routines, tout ce qu'on veut.
### `.rodata`
De la donnée readonly, genre des constantes globales...
### `.bss`
Les variables, allocations dynamiques...
### `.dynamic`
C'est là que le linker (`ld`, `rtld`...) va cherche sa configuration.
Table des symboles

## ELF golfing
c'est recommandé de plutôt faire ses binaires en [[Assembly (language)]] car on peut mieux gérer les headers et le contenu général et en 32bits ou moins (car sinon ça prend trop de place)
https://discord.com/channels/782652844577652796/817144996728930304/949770913245691924 ([[tmp.out]] discord)
on peut faire a la main des binaires (pour optimiser la taille) mais c'est assez complexe car on doit bidouiller les entetes des fichiers (ELF headers) pour qu'ils se chevauchent (on minimise le nombre d'octests inutiles)
en fait ce qu'il ce passe c'est que il faut d'abord compiler le binaire ou l'assembleur et ensuite utiliser un outil linker (`ld.bfd`) poour terminer l'optimisation de la taille (en gros de ce que je comprend c'est un outil qui va constriore le binaire  dynamiquement via le `.o`  et appliquer un scrupt.)

> In brief, the section header table is for use by the compiler and linker, while the program header table is for use by the program loader.

The program header table is optionnal and never present in practice, the section header table is also optional but always present.

### Compilation
**Classque :**
`nasm -f elf32 file.s`
`ld -m elf_i386 -nmagic file.o -o bin`

**Mieux :**
Un truc qui marche vachement bien pour build un binaire direct c'est `nasm -f bin file.s`, en effet ça va juste pousser le binaire dans un fichier.

### Header
Le plus petit header pour l'instant je vois que c'est genre
* x86
* 32bits
* little endian
* entry = `0xdeadbeef`
* header table = `0xcafebabe`
* section header = `0xdefaced0`
* `???? ????` = depend de l'archi (ici x86)
* header size = `0x34`
* program header size = `0x0045`
* `0x0004` entries in program header

```hex
7f45 4c46 0101 0300 0000 0000 0000 0000
0300 0000 0100 dead beef cafe babe defa
ced0 ???? ???? 0034 4500 0400
```

Hop un ptit binaire tout petit et des bons headers
```asm
  BITS 32
  
                org     0x08048000
  
  ehdr:                                                 ; Elf32_Ehdr
                db      0x7F, "ELF", 1, 1, 1, 0         ;   e_ident
        times 8 db      0
                dw      2                               ;   e_type
                dw      3                               ;   e_machine
                dd      1                               ;   e_version
                dd      _start                          ;   e_entry
                dd      phdr - $$                       ;   e_phoff
                dd      0                               ;   e_shoff
                dd      0                               ;   e_flags
                dw      ehdrsize                        ;   e_ehsize
                dw      phdrsize                        ;   e_phentsize
                dw      1                               ;   e_phnum
                dw      0                               ;   e_shentsize
                dw      0                               ;   e_shnum
                dw      0                               ;   e_shstrndx
  
  ehdrsize      equ     $ - ehdr
  
  phdr:                                                 ; Elf32_Phdr
                dd      1                               ;   p_type
                dd      0                               ;   p_offset
                dd      $$                              ;   p_vaddr
                dd      $$                              ;   p_paddr
                dd      filesize                        ;   p_filesz
                dd      filesize                        ;   p_memsz
                dd      5                               ;   p_flags
                dd      0x1000                          ;   p_align
  
  phdrsize      equ     $ - phdr
  
  _start:
  
  ; your program here
  
  filesize      equ     $ - $$
```

### Unethical stuff
#### Déclarer des variables n'importe où
🙈 rien n'interdit de mettre les variables dans la section `.text`, ou même n'importe où finalement
```asm
section .text
	var: db "salut", 0xa
```

#### Mettre du code dans le header
**🧠** big brain move here: on met le code dans le header
```asm
ehdr:
	db 0x7f, "ELF"
	db 1, 1, 1, 0, 0
_start:
	mov bl, 42
	xor eax, eax
	inc eax
	int 0x80
	;; continue le header
	dw 2
	dw 3
	dw 1
	;; ...
```


### Golfing resources
https://codegolf.stackexchange.com/questions/5696/shortest-elf-for-hello-world-n
[Create tiny ELF for Linux](https://www.muppetlabs.com/~breadbox/software/tiny/teensy.html)
https://www.muppetlabs.com/~breadbox/software/tiny/
[Analyzing ELF with malformed headers](https://binaryresearch.github.io/2019/09/17/Analyzing-ELF-Binaries-with-Malformed-Headers-Part-1-Emulating-Tiny-Programs.html)
