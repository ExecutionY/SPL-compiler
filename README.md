# SPL-compiler

Course project of Compile Principle in CS, ZJU.

Reference from CS375: UT

Materials: ftp://ftp.cs.utexas.edu/pub/novak/cs375/

### Requires

SPL is a Simple Pascal Language. The target is to compile SPL programs into x86 assemble codes.

### Attention

- I'm not good at Pascal & X86 asm, so the result might be wrong.
- At least there's no running bug within test files.

## Part 1

Lexical Analyzer - lexer

#### New Files

- lexan.l
- mainlexer.c
- Makefile
- lexan.h *
- token.h *


- printtoken.c	*

### Instruction

```
$ make clean
$ make lexer
$ ./lexer < [SPL-file]
```

### output

```
yylex() =  304   tokenType:  2  which:   17      program
yylex() =  258   tokenType:  3  value:            graph1
yylex() =  270   tokenType:  1  which:    8            ;
yylex() =  313   tokenType:  2  which:   26          var
yylex() =  258   tokenType:  3  value:                 i
yylex() =  268   tokenType:  1  which:    6            ,
yylex() =  258   tokenType:  3  value:               lim
yylex() =  269   tokenType:  1  which:    7            :
yylex() =  319   tokenType:  2  which:   32     SYS_TYPE
yylex() =  270   tokenType:  1  which:    8            ;
yylex() =  289   tokenType:  2  which:    2        begin
yylex() =  258   tokenType:  3  value:               lim
yylex() =  282   tokenType:  0  which:   12           :=
yylex() =  260   tokenType:  5  type:     0            7
yylex() =  270   tokenType:  1  which:    8            ;
yylex() =  296   tokenType:  2  which:    9          for
yylex() =  258   tokenType:  3  value:                 i
yylex() =  282   tokenType:  0  which:   12           :=
yylex() =  260   tokenType:  5  type:     0            0
yylex() =  310   tokenType:  2  which:   23           to
yylex() =  258   tokenType:  3  value:               lim
yylex() =  292   tokenType:  2  which:    5           do
yylex() =  318   tokenType:  2  which:   31     SYS_PROC
yylex() =  263   tokenType:  1  which:    1            (
yylex() =  262   tokenType:  6  value:                 *
yylex() =  264   tokenType:  1  which:    2            )
yylex() =  270   tokenType:  1  which:    8            ;
yylex() =  295   tokenType:  2  which:    8          end
yylex() =  271   tokenType:  0  which:    1            .
```

## Part 2

Syntax Analysis - parser

### Files

Modified/new files

- parse.y
- parse.h
- parsefun.c
- mainparser.c
- pprint.c *
- pprint.h
- symtab.c *
- symtab.h *
- symtab.txt *

### Instruction

```
$ make clean
$ make parser
$ ./parser < [SPL-file]
```

### Output

```
yyparse result =        0
 token 140443849342752  OP       program  dtype  0  link 0  operands 140443849339712
(program graph1 (progn (:= lim 7)
                       (progn (:= i 0)
                              (label 0)
                              (if (<= i lim)
                                  (progn (funcall write
                                                  '*')
                                         (:= i (+ i 1))
                                         (goto 0))))))
```

## Part 3

Code Generator - compiler

### Files

- codegen.c
- codegen.h
- maincompiler.c
- genasm.c *
- genasm.h *

### Instruction

```
$ make clean
$ make compiler
$ ./compiler < [SPL-file]
```

### Output

```
# ---------------------- Beginning of Generated Code --------------------
        .file   "foo"
        .text
.globl graph1
        .type   graph1, @function
graph1:
.LFB0:
	.cfi_startproc
	pushq	%rbp              # save base pointer on stack
	.cfi_def_cfa_offset 16
	movq	%rsp, %rbp        # move stack pointer to base pointer
	.cfi_offset 6, -16
	.cfi_def_cfa_register 6
        subq	$32, %rsp 	  # make space for this stack frame
	movq	%rbx, %r9        # save %rbx (callee-saved) in %r9
# ------------------------- begin Your code -----------------------------
	jmp	.L0 			#  jump 
.L0:
	movl	$7,%eax         	#  7 -> %eax
	movl	%eax,-28(%rbp)     	#  %eax -> lim
	movl	$0,%eax         	#  0 -> %eax
	movl	%eax,-32(%rbp)     	#  %eax -> i
.L1:
	movl	-32(%rbp),%eax     	#  i -> %eax
	movl	-28(%rbp),%ecx     	#  lim -> %ecx
	cmpl	%ecx,%eax           	#  compare %eax - %ecx
	jle	.L2 			#  jump if     <=
	jmp	.L3 			#  jump 
.L2:
	movl	$.LC4,%edi       	#  addr of literal .LC4
	call	write              	#  write()
	movl	-32(%rbp),%eax     	#  i -> %eax
	movl	$1,%ecx         	#  1 -> %ecx
	addl	%ecx,%eax         	#  %eax + %ecx -> %eax
	movl	%eax,-32(%rbp)     	#  %eax -> i
	jmp	.L1 			#  jump 
.L3:
# ----------------------- begin Epilogue code ---------------------------
	movq	%r9, %rbx        # restore %rbx (callee-saved) from %r9
        leave
        ret
        .cfi_endproc
.LFE0:
        .size   graph1, .-graph1
# ----------------- end Epilogue; Literal data follows ------------------
        .section        .rodata
	.align  4
.LC4:
	.string	"*"

        .ident  "Compiler Principle - Spring 2017"
```
## Part4

run 80386 asm file

### Attention

- It's fine to run Part1-3 on Mac OS. However Part4 must run on Linux, because our assembly grammar is for Linux.
- There are some bugs, if you use SPL-files other than test1.spl/test2.spl/test6.spl, you might get errors.
- The program name must be "test", which you should find in driver.c

### File

- driver.c

### Instruction

```
$ ./compiler < test2.spl > test2.s
$ cc driver.c test2.s -lm
$ ./a.out
```

### Output

test2.spl compute fibonacci(10).

```
calling test
55
exit from test
```