# sparc

;;- Machine description for SPARC chip for GNU C compiler\
;; Copyright (C) 1988, 1989 Free Software Foundation, Inc.\
;; Contributed by Michael Tiemann (tiemann@mcc.com)

;; This file is part of GNU CC.

;; GNU CC is free software; you can redistribute it and/or modify\
;; it under the terms of the GNU General Public License as published by\
;; the Free Software Foundation; either version 1, or (at your option)\
;; any later version.

;; GNU CC is distributed in the hope that it will be useful,\
;; but WITHOUT ANY WARRANTY; without even the implied warranty of\
;; MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE. See the\
;; GNU General Public License for more details.

;; You should have received a copy of the GNU General Public License\
;; along with GNU CC; see the file COPYING. If not, write to\
;; the Free Software Foundation, 675 Mass Ave, Cambridge, MA 02139, USA.

;;- See file "rtl.def" for documentation on define\_insn, match\_\*, et. al.

;;- cpp macro #define NOTICE\_UPDATE\_CC in file tm.h handles condition code\
;;- updates for most instructions.

;;- Operand classes for the register allocator:\
;; Compare instructions.\
;; This controls RTL generation and register allocation.

;; Put cmpsi first among compare insns so it matches two CONST\_INT operands.

(define\_insn "cmpsi"\
\[(set (cc0)\
(compare (match\_operand:SI 0 "arith\_operand" "r,rI")\
(match\_operand:SI 1 "arith\_operand" "I,r")))]\
""\
"\*\
{\
if (! REG\_P (operands\[0]))\
{\
cc\_status.flags |= CC\_REVERSED;\
return "cmp %1,%0";\
}\
return "cmp %0,%1";\
}")

(define\_expand "cmpdf"\
\[(set (cc0)\
(compare (match\_operand:DF 0 "nonmemory\_operand" "f,fG")\
(match\_operand:DF 1 "nonmemory\_operand" "G,f")))]\
""\
"emit\_insn (gen\_rtx (USE, VOIDmode, gen\_rtx (REG, DFmode, 32)));")

(define\_insn ""\
\[(set (cc0)\
(compare (match\_operand:DF 0 "nonmemory\_operand" "f,fG")\
(match\_operand:DF 1 "nonmemory\_operand" "G,f")))]\
""\
"\*\
{\
if (GET\_CODE (operands\[0]) == CONST\_DOUBLE\
|| GET\_CODE (operands\[1]) == CONST\_DOUBLE)\
make\_f0\_contain\_0 (2);

cc\_status.flags |= CC\_IN\_FCCR;\
if (GET\_CODE (operands\[0]) == CONST\_DOUBLE)\
return "fcmped %%f0,%1;nop";\
if (GET\_CODE (operands\[1]) == CONST\_DOUBLE)\
return "fcmped %0,%%f0;nop";\
return "fcmped %0,%1;nop";\
}")

(define\_expand "cmpsf"\
\[(set (cc0)\
(compare (match\_operand:SF 0 "nonmemory\_operand" "f,fG")\
(match\_operand:SF 1 "nonmemory\_operand" "G,f")))]\
""\
"emit\_insn (gen\_rtx (USE, VOIDmode, gen\_rtx (REG, SFmode, 32)));")

(define\_insn ""\
\[(set (cc0)\
(compare (match\_operand:SF 0 "nonmemory\_operand" "f,fG")\
(match\_operand:SF 1 "nonmemory\_operand" "G,f")))]\
""\
"\*\
{\
if (GET\_CODE (operands\[0]) == CONST\_DOUBLE\
|| GET\_CODE (operands\[1]) == CONST\_DOUBLE)\
make\_f0\_contain\_0 (1);

cc\_status.flags |= CC\_IN\_FCCR;\
if (GET\_CODE (operands\[0]) == CONST\_DOUBLE)\
return "fcmpes %%f0,%1;nop";\
if (GET\_CODE (operands\[1]) == CONST\_DOUBLE)\
return "fcmpes %0,%%f0;nop";\
return "fcmpes %0,%1;nop";\
}")

;; Put tstsi first among test insns so it matches a CONST\_INT operand.

(define\_insn "tstsi"\
\[(set (cc0)\
(match\_operand:SI 0 "register\_operand" "r"))]\
""\
"tst %0")

;; Need this to take a general operand because cse can make\
;; a CONST which won't be in a register.\
(define\_insn ""\
\[(set (cc0)\
(match\_operand:SI 0 "immediate\_operand" "i"))]\
""\
"set %0,%%g1;tst %%g1")

;; Optimize the case of following a reg-reg move with a test\
;; of reg just moved.

(define\_peephole\
\[(set (match\_operand:SI 0 "register\_operand" "=r")\
(match\_operand:SI 1 "register\_operand" "r"))\
(set (cc0) (match\_operand:SI 2 "register\_operand" "r"))]\
"operands\[2] == operands\[0]\
|| operands\[2] == operands\[1]"\
"orcc %1,%%g0,%0 ! 2-insn combine")

;; Optimize 5(6) insn sequence to 3(4) insns.\
;; These patterns could also optimize more complicated sets\
;; before conditional branches.

;; Turned off because (1) this case is rarely encounted\
;; (2) to be correct, more conditions must be checked\
;; (3) the conditions must be checked with rtx\_equal\_p, not ==\
;; (4) when branch scheduling is added to the compiler,\
;; this optimization will be performed by the branch scheduler\
;; Bottom line: it is not worth the trouble of fixing or\
;; maintaining it.

;(define\_peephole\
; \[(set (match\_operand:SI 0 "register\_operand" "=r")\
; (match\_operand:SI 1 "general\_operand" "g"))\
; (set (match\_operand:SI 2 "register\_operand" "=r")\
; (match\_operand:SI 3 "reg\_or\_0\_operand" "rJ"))\
; (set (cc0) (match\_operand:SI 4 "register\_operand" "r"))\
; (set (pc) (match\_operand 5 "" ""))]\
; "GET\_CODE (operands\[5]) == IF\_THEN\_ELSE\
; && operands\[0] != operands\[3]\
; && ! reg\_mentioned\_p (operands\[2], operands\[1])\
; && (operands\[4] == operands\[0]\
; || operands\[4] == operands\[2]\
; || operands\[4] == operands\[3])"\
; "\*\
;{\
; rtx xoperands\[2];\
; int parity;\
; xoperands\[0] = XEXP (operands\[5], 0);\
; if (GET\_CODE (XEXP (operands\[5], 1)) == PC)\
; {\
; parity = 1;\
; xoperands\[1] = XEXP (XEXP (operands\[5], 2), 0);\
; }\
; else\
; {\
; parity = 0;\
; xoperands\[1] = XEXP (XEXP (operands\[5], 1), 0);\
; }\
;\
; if (operands\[4] == operands\[0])\
; {\
; /\* Although the constraints for operands\[1] permit a general\
; operand (and hence possibly a const\_int), we know that\
; in this branch it cannot be a CONST\_INT, since that would give\
; us a fixed condition, and those should have been optimized away. \*/\
; if (REG\_P (operands\[1]))\
; output\_asm\_insn ("orcc %1,%%g0,%0 ! 3-insn reorder", operands);\
; else if (GET\_CODE (operands\[1]) != MEM)\
; abort ();\
; else\
; {\
; if (CONSTANT\_ADDRESS\_P (XEXP (operands\[1], 0)))\
; output\_asm\_insn ("sethi %%hi(%m1),%%g1;ld \[%%g1+%%lo(%m1)],%0;tst %0 ! 4-insn reorder", operands);\
; else\
; output\_asm\_insn ("ld %1,%0;tst %0 ! 3.5-insn reorder", operands);\
; }\
; XVECEXP (PATTERN (insn), 0, 0) = XVECEXP (PATTERN (insn), 0, 2);\
; XVECEXP (PATTERN (insn), 0, 1) = XVECEXP (PATTERN (insn), 0, 3);\
; }\
; else\
; {\
; output\_asm\_insn ("orcc %3,%%g0,%2 ! 3-insn reorder", operands);\
; }\
; if (parity)\
; return output\_delayed\_branch ("b%N0 %l1", xoperands, insn);\
; else\
; return output\_delayed\_branch ("b%C0 %l1", xoperands, insn);\
;}")

;; By default, operations don't set the condition codes.\
;; These patterns allow cc's to be set, while doing some work

(define\_insn ""\
\[(set (cc0)\
(zero\_extend:SI (subreg:QI (match\_operand:SI 0 "register\_operand" "r") 0)))]\
""\
"andcc %0,0xff,%%g0")

(define\_insn ""\
\[(set (cc0)\
(plus:SI (match\_operand:SI 0 "register\_operand" "r%")\
(match\_operand:SI 1 "arith\_operand" "rI")))]\
""\
"addcc %0,%1,%%g0")

(define\_insn ""\
\[(set (cc0)\
(plus:SI (match\_operand:SI 0 "register\_operand" "r%")\
(match\_operand:SI 1 "arith\_operand" "rI")))\
(set (match\_operand:SI 2 "register\_operand" "=r")\
(plus:SI (match\_dup 0) (match\_dup 1)))]\
""\
"addcc %0,%1,%2")

(define\_insn ""\
\[(set (cc0)\
(minus:SI (match\_operand:SI 0 "register\_operand" "r")\
(match\_operand:SI 1 "arith\_operand" "rI")))\
(set (match\_operand:SI 2 "register\_operand" "=r")\
(minus:SI (match\_dup 0) (match\_dup 1)))]\
""\
"subcc %0,%1,%2")

(define\_insn ""\
\[(set (cc0)\
(and:SI (match\_operand:SI 0 "register\_operand" "r%")\
(match\_operand:SI 1 "arith\_operand" "rI")))]\
""\
"andcc %0,%1,%%g0")

(define\_insn ""\
\[(set (cc0)\
(and:SI (match\_operand:SI 0 "register\_operand" "r%")\
(match\_operand:SI 1 "arith\_operand" "rI")))\
(set (match\_operand:SI 2 "register\_operand" "=r")\
(and:SI (match\_dup 0) (match\_dup 1)))]\
""\
"andcc %0,%1,%2")

(define\_insn ""\
\[(set (cc0)\
(and:SI (match\_operand:SI 0 "register\_operand" "r%")\
(not:SI (match\_operand:SI 1 "arith\_operand" "rI"))))]\
""\
"andncc %0,%1,%%g0")

(define\_insn ""\
\[(set (cc0)\
(and:SI (match\_operand:SI 0 "register\_operand" "r%")\
(not:SI (match\_operand:SI 1 "arith\_operand" "rI"))))\
(set (match\_operand:SI 2 "register\_operand" "=r")\
(and:SI (match\_dup 0) (not:SI (match\_dup 1))))]\
""\
"andncc %0,%1,%2")

(define\_insn ""\
\[(set (cc0)\
(ior:SI (match\_operand:SI 0 "register\_operand" "r%")\
(match\_operand:SI 1 "arith\_operand" "rI")))]\
""\
"orcc %0,%1,%%g0")

(define\_insn ""\
\[(set (cc0)\
(ior:SI (match\_operand:SI 0 "register\_operand" "r%")\
(match\_operand:SI 1 "arith\_operand" "rI")))\
(set (match\_operand:SI 2 "register\_operand" "=r")\
(ior:SI (match\_dup 0) (match\_dup 1)))]\
""\
"orcc %0,%1,%2")

(define\_insn ""\
\[(set (cc0)\
(ior:SI (match\_operand:SI 0 "register\_operand" "r%")\
(not:SI (match\_operand:SI 1 "arith\_operand" "rI"))))]\
""\
"orncc %0,%1,%%g0")

(define\_insn ""\
\[(set (cc0)\
(ior:SI (match\_operand:SI 0 "register\_operand" "r%")\
(not:SI (match\_operand:SI 1 "arith\_operand" "rI"))))\
(set (match\_operand:SI 2 "register\_operand" "=r")\
(ior:SI (match\_dup 0) (not:SI (match\_dup 1))))]\
""\
"orncc %0,%1,%2")

(define\_insn ""\
\[(set (cc0)\
(xor:SI (match\_operand:SI 0 "register\_operand" "r%")\
(match\_operand:SI 1 "arith\_operand" "rI")))]\
""\
"xorcc %0,%1,%%g0")

(define\_insn ""\
\[(set (cc0)\
(xor:SI (match\_operand:SI 0 "register\_operand" "r%")\
(match\_operand:SI 1 "arith\_operand" "rI")))\
(set (match\_operand:SI 2 "register\_operand" "=r")\
(xor:SI (match\_dup 0) (match\_dup 1)))]\
""\
"xorcc %0,%1,%2")

(define\_insn ""\
\[(set (cc0)\
(xor:SI (match\_operand:SI 0 "register\_operand" "r%")\
(not:SI (match\_operand:SI 1 "arith\_operand" "rI"))))]\
""\
"xnorcc %0,%1,%%g0")

(define\_insn ""\
\[(set (cc0)\
(xor:SI (match\_operand:SI 0 "register\_operand" "r%")\
(not:SI (match\_operand:SI 1 "arith\_operand" "rI"))))\
(set (match\_operand:SI 2 "register\_operand" "=r")\
(xor:SI (match\_dup 0) (not:SI (match\_dup 1))))]\
""\
"xnorcc %0,%1,%2")

(define\_expand "tstdf"\
\[(set (cc0)\
(match\_operand:DF 0 "register\_operand" "f"))]\
""\
"emit\_insn (gen\_rtx (USE, VOIDmode, gen\_rtx (REG, DFmode, 32)));")

(define\_insn ""\
\[(set (cc0)\
(match\_operand:DF 0 "register\_operand" "f"))]\
""\
"\*\
{\
make\_f0\_contain\_0 (2);\
cc\_status.flags |= CC\_IN\_FCCR;\
return "fcmped %0,%%f0;nop";\
}")

(define\_expand "tstsf"\
\[(set (cc0)\
(match\_operand:SF 0 "register\_operand" "f"))]\
""\
"emit\_insn (gen\_rtx (USE, VOIDmode, gen\_rtx (REG, SFmode, 32)));")

(define\_insn ""\
\[(set (cc0)\
(match\_operand:SF 0 "register\_operand" "f"))]\
""\
"\*\
{\
make\_f0\_contain\_0 (1);\
cc\_status.flags |= CC\_IN\_FCCR;\
return "fcmpes %0,%%f0;nop";\
}")\
;; There are no logical links for the condition codes. This\
;; would not normally be a problem, but on the SPARC (and possibly\
;; other RISC machines), when argument passing, the insn which sets\
;; the condition code and the insn which uses the set condition code\
;; may not be performed adjacently (due to optimizations performed\
;; in combine.c). To make up for this, we emit insn patterns which\
;; cannot possibly be rearranged on us.\
(define\_expand "seq"\
\[(set (match\_operand:SI 0 "general\_operand" "=r")\
(eq (cc0) (const\_int 0)))]\
""\
"gen\_scc\_insn (EQ, VOIDmode, operands); DONE;")

(define\_expand "sne"\
\[(set (match\_operand:SI 0 "general\_operand" "=r")\
(ne (cc0) (const\_int 0)))]\
""\
"gen\_scc\_insn (NE, VOIDmode, operands); DONE;")

(define\_insn ""\
\[(set (match\_operand:SI 0 "general\_operand" "=r,r")\
(match\_operator 1 "eq\_or\_neq"\
\[(compare (match\_operand:SI 2 "general\_operand" "r,rI")\
(match\_operand:SI 3 "general\_operand" "I,r"))\
(const\_int 0)]))]\
""\
"\*\
{\
CC\_STATUS\_INIT;\
cc\_status.value1 = operands\[0];\
if (! REG\_P (operands\[2]))\
{\
output\_asm\_insn ("cmp %3,%2", operands);\
cc\_status.flags |= CC\_REVERSED;\
}\
else\
output\_asm\_insn ("cmp %2,%3", operands);\
return output\_scc\_insn (GET\_CODE (operands\[1]), operands\[0]);\
}")

(define\_insn ""\
\[(set (match\_operand:SI 0 "general\_operand" "=r")\
(match\_operator 1 "eq\_or\_neq"\
\[(match\_operand:SI 2 "general\_operand" "r")\
(const\_int 0)]))]\
""\
"\*\
{\
CC\_STATUS\_INIT;\
cc\_status.value1 = operands\[0];\
output\_asm\_insn ("tst %2", operands);\
return output\_scc\_insn (GET\_CODE (operands\[1]), operands\[0]);\
}")

(define\_insn ""\
\[(set (match\_operand:SI 0 "general\_operand" "=r,r")\
(match\_operator 1 "eq\_or\_neq"\
\[(compare (match\_operand:DF 2 "general\_operand" "f,fG")\
(match\_operand:DF 3 "general\_operand" "G,f"))\
(const\_int 0)]))]\
""\
"\*\
{\
CC\_STATUS\_INIT;\
cc\_status.value1 = operands\[0];\
cc\_status.flags |= CC\_IN\_FCCR;

if (GET\_CODE (operands\[2]) == CONST\_DOUBLE\
|| GET\_CODE (operands\[3]) == CONST\_DOUBLE)\
make\_f0\_contain\_0 (2);

if (GET\_CODE (operands\[2]) == CONST\_DOUBLE)\
output\_asm\_insn ("fcmped %%f0,%3;nop", operands);\
else if (GET\_CODE (operands\[3]) == CONST\_DOUBLE)\
output\_asm\_insn ("fcmped %2,%%f0;nop", operands);\
else output\_asm\_insn ("fcmped %2,%3;nop", operands);\
return output\_scc\_insn (GET\_CODE (operands\[1]), operands\[0]);\
}")

(define\_insn ""\
\[(set (match\_operand:SI 0 "general\_operand" "=r")\
(match\_operator 1 "eq\_or\_neq"\
\[(match\_operand:DF 2 "general\_operand" "f")\
(const\_int 0)]))]\
""\
"\*\
{\
CC\_STATUS\_INIT;\
cc\_status.value1 = operands\[0];\
cc\_status.flags |= CC\_IN\_FCCR;

make\_f0\_contain\_0 (2);\
output\_asm\_insn ("fcmped %2,%%f0;nop", operands);\
return output\_scc\_insn (GET\_CODE (operands\[1]), operands\[0]);\
}")

(define\_insn ""\
\[(set (match\_operand:SI 0 "general\_operand" "=r,r")\
(match\_operator 1 "eq\_or\_neq"\
\[(compare (match\_operand:SF 2 "general\_operand" "f,fG")\
(match\_operand:SF 3 "general\_operand" "G,f"))\
(const\_int 0)]))]\
""\
"\*\
{\
CC\_STATUS\_INIT;\
cc\_status.value1 = operands\[0];\
cc\_status.flags |= CC\_IN\_FCCR;

if (GET\_CODE (operands\[2]) == CONST\_DOUBLE\
|| GET\_CODE (operands\[3]) == CONST\_DOUBLE)\
make\_f0\_contain\_0 (1);

if (GET\_CODE (operands\[2]) == CONST\_DOUBLE)\
output\_asm\_insn ("fcmpes %%f0,%3;nop", operands);\
else if (GET\_CODE (operands\[3]) == CONST\_DOUBLE)\
output\_asm\_insn ("fcmpes %2,%%f0;nop", operands);\
else output\_asm\_insn ("fcmpes %2,%3;nop", operands);\
return output\_scc\_insn (GET\_CODE (operands\[1]), operands\[0]);\
}")

(define\_insn ""\
\[(set (match\_operand:SI 0 "general\_operand" "=r")\
(match\_operator 1 "eq\_or\_neq"\
\[(match\_operand:SF 2 "general\_operand" "f")\
(const\_int 0)]))]\
""\
"\*\
{\
CC\_STATUS\_INIT;\
cc\_status.value1 = operands\[0];\
cc\_status.flags |= CC\_IN\_FCCR;

make\_f0\_contain\_0 (1);\
output\_asm\_insn ("fcmpes %2,%%f0;nop", operands);\
return output\_scc\_insn (GET\_CODE (operands\[1]), operands\[0]);\
}")\
;; These control RTL generation for conditional jump insns\
;; and match them for register allocation.

(define\_insn "beq"\
\[(set (pc)\
(if\_then\_else (eq (cc0)\
(const\_int 0))\
(label\_ref (match\_operand 0 "" ""))\
(pc)))]\
""\
"\*\
{\
if (cc\_prev\_status.flags & CC\_IN\_FCCR)\
return "fbe %l0;nop";\
return "be %l0;nop";\
}")

(define\_insn "bne"\
\[(set (pc)\
(if\_then\_else (ne (cc0)\
(const\_int 0))\
(label\_ref (match\_operand 0 "" ""))\
(pc)))]\
""\
"\*\
{\
if (cc\_prev\_status.flags & CC\_IN\_FCCR)\
return "fbne %l0;nop";\
return "bne %l0;nop";\
}")

(define\_insn "bgt"\
\[(set (pc)\
(if\_then\_else (gt (cc0)\
(const\_int 0))\
(label\_ref (match\_operand 0 "" ""))\
(pc)))]\
""\
"\*\
{\
if (cc\_prev\_status.flags & CC\_IN\_FCCR)\
return "fbg %l0;nop";\
return "bg %l0;nop";\
}")

(define\_insn "bgtu"\
\[(set (pc)\
(if\_then\_else (gtu (cc0)\
(const\_int 0))\
(label\_ref (match\_operand 0 "" ""))\
(pc)))]\
""\
"\*\
{\
if (cc\_prev\_status.flags & CC\_IN\_FCCR)\
abort ();\
return "bgu %l0;nop";\
}")

(define\_insn "blt"\
\[(set (pc)\
(if\_then\_else (lt (cc0)\
(const\_int 0))\
(label\_ref (match\_operand 0 "" ""))\
(pc)))]\
""\
"\*\
{\
if (cc\_prev\_status.flags & CC\_IN\_FCCR)\
return "fbl %l0;nop";\
return "bl %l0;nop";\
}")

(define\_insn "bltu"\
\[(set (pc)\
(if\_then\_else (ltu (cc0)\
(const\_int 0))\
(label\_ref (match\_operand 0 "" ""))\
(pc)))]\
""\
"\*\
{\
if (cc\_prev\_status.flags & CC\_IN\_FCCR)\
abort ();\
return "blu %l0;nop";\
}")

(define\_insn "bge"\
\[(set (pc)\
(if\_then\_else (ge (cc0)\
(const\_int 0))\
(label\_ref (match\_operand 0 "" ""))\
(pc)))]\
""\
"\*\
{\
if (cc\_prev\_status.flags & CC\_IN\_FCCR)\
return "fbge %l0;nop";\
return "bge %l0;nop";\
}")

(define\_insn "bgeu"\
\[(set (pc)\
(if\_then\_else (geu (cc0)\
(const\_int 0))\
(label\_ref (match\_operand 0 "" ""))\
(pc)))]\
""\
"\*\
{\
if (cc\_prev\_status.flags & CC\_IN\_FCCR)\
abort ();\
return "bgeu %l0;nop";\
}")

(define\_insn "ble"\
\[(set (pc)\
(if\_then\_else (le (cc0)\
(const\_int 0))\
(label\_ref (match\_operand 0 "" ""))\
(pc)))]\
""\
"\*\
{\
if (cc\_prev\_status.flags & CC\_IN\_FCCR)\
return "fble %l0;nop";\
return "ble %l0;nop";\
}")

(define\_insn "bleu"\
\[(set (pc)\
(if\_then\_else (leu (cc0)\
(const\_int 0))\
(label\_ref (match\_operand 0 "" ""))\
(pc)))]\
""\
"\*\
{\
if (cc\_prev\_status.flags & CC\_IN\_FCCR)\
abort ();\
return "bleu %l0;nop";\
}")\
;; This matches inverted jump insns for register allocation.

(define\_insn ""\
\[(set (pc)\
(if\_then\_else (match\_operator 0 "relop" \[(cc0) (const\_int 0)])\
(pc)\
(label\_ref (match\_operand 1 "" ""))))]\
""\
"\*\
{\
if (cc\_prev\_status.flags & CC\_IN\_FCCR)\
return "fb%F0 %l1;nop";\
return "b%N0 %l1;nop";\
}")\
;; Move instructions

(define\_insn "swapsi"\
\[(set (match\_operand:SI 0 "general\_operand" "r,rm")\
(match\_operand:SI 1 "general\_operand" "m,r"))\
(set (match\_dup 1) (match\_dup 0))]\
""\
"\*\
{\
if (GET\_CODE (operands\[1]) == MEM)\
{\
if (CONSTANT\_ADDRESS\_P (XEXP (operands\[1], 0)))\
output\_asm\_insn ("set %a1,%%g1", operands),\
operands\[1] = gen\_rtx (MEM, SImode, gen\_rtx (REG, SImode, 1)),\
cc\_status.flags &= \~CC\_KNOW\_HI\_G1;\
output\_asm\_insn ("swap %1,%0", operands);\
}\
if (REG\_P (operands\[0]))\
{\
if (REGNO (operands\[0]) == REGNO (operands\[1]))\
return "";\
return "xor %0,%1,%0;xor %1,%0,%1;xor %0,%1,%0";\
}\
if (CONSTANT\_ADDRESS\_P (XEXP (operands\[0], 0)))\
{\
output\_asm\_insn ("set %a0,%%g1", operands);\
operands\[0] = gen\_rtx (MEM, SImode, gen\_rtx (REG, SImode, 1));\
cc\_status.flags &= \~CC\_KNOW\_HI\_G1;\
}\
return "swap %0,%1";\
}")

(define\_insn "movsi"\
\[(set (match\_operand:SI 0 "general\_operand" "=r,m")\
(match\_operand:SI 1 "general\_operand" "rmif,rJ"))]\
""\
"\*\
{\
if (GET\_CODE (operands\[0]) == MEM)\
{\
if (CONSTANT\_ADDRESS\_P (XEXP (operands\[0], 0)))\
return output\_store (operands);\
return "st %r1,%0";\
}\
if (GET\_CODE (operands\[1]) == MEM)\
{\
if (CONSTANT\_ADDRESS\_P (XEXP (operands\[1], 0)))\
return output\_load\_fixed (operands);\
return "ld %1,%0";\
}\
if (FP\_REG\_P (operands\[1]))\
return "st %r1,\[%%fp-4];ld \[%%fp-4],%0";\
if (REG\_P (operands\[1])\
|| (GET\_CODE (operands\[1]) == CONST\_INT\
&& SMALL\_INT (operands\[1])))\
return "mov %1,%0";\
if (GET\_CODE (operands\[1]) == CONST\_INT\
&& (INTVAL (operands\[1]) & 0x3ff) == 0)\
return "sethi %%hi(%1),%0";\
return "sethi %%hi(%1),%0;or %%lo(%1),%0,%0";\
}")

(define\_insn "movhi"\
\[(set (match\_operand:HI 0 "general\_operand" "=r,m")\
(match\_operand:HI 1 "general\_operand" "rmi,rJ"))]\
""\
"\*\
{\
if (GET\_CODE (operands\[0]) == MEM)\
{\
if (CONSTANT\_ADDRESS\_P (XEXP (operands\[0], 0)))\
return output\_store (operands);\
return "sth %r1,%0";\
}\
if (GET\_CODE (operands\[1]) == MEM)\
{\
if (CONSTANT\_ADDRESS\_P (XEXP (operands\[1], 0)))\
return output\_load\_fixed (operands);\
return "ldsh %1,%0";\
}\
if (REG\_P (operands\[1])\
|| (GET\_CODE (operands\[1]) == CONST\_INT\
&& SMALL\_INT (operands\[1])))\
return "mov %1,%0";\
return "sethi %%hi(%1),%0;or %%lo(%1),%0,%0";\
}")

(define\_insn "movqi"\
\[(set (match\_operand:QI 0 "general\_operand" "=r,m")\
(match\_operand:QI 1 "general\_operand" "rmi,rJ"))]\
""\
"\*\
{\
if (GET\_CODE (operands\[0]) == MEM)\
{\
if (CONSTANT\_ADDRESS\_P (XEXP (operands\[0], 0)))\
return output\_store (operands);\
return "stb %r1,%0";\
}\
if (GET\_CODE (operands\[1]) == MEM)\
{\
if (CONSTANT\_ADDRESS\_P (XEXP (operands\[1], 0)))\
return output\_load\_fixed (operands);\
return "ldsb %1,%0";\
}\
if (REG\_P (operands\[1])\
|| (GET\_CODE (operands\[1]) == CONST\_INT\
&& SMALL\_INT (operands\[1])))\
return "mov %1,%0";\
return "sethi %%hi(%1),%0;or %%lo(%1),%0,%0";\
}")

;; The definition of this insn does not really explain what it does,\
;; but it should suffice\
;; that anything generated as this insn will be recognized as one\
;; and that it won't successfully combine with anything.\
(define\_expand "movstrsi"\
\[(parallel \[(set (mem:BLK (match\_operand:BLK 0 "general\_operand" ""))\
(mem:BLK (match\_operand:BLK 1 "general\_operand" "")))\
(use (match\_operand:SI 2 "arith32\_operand" ""))\
(use (match\_operand:SI 3 "immediate\_operand" ""))\
(clobber (match\_dup 4))\
(clobber (match\_dup 0))\
(clobber (match\_dup 1))])]\
""\
"\
{\
operands\[0] = copy\_to\_mode\_reg (SImode, XEXP (operands\[0], 0));\
operands\[1] = copy\_to\_mode\_reg (SImode, XEXP (operands\[1], 0));\
operands\[4] = gen\_reg\_rtx (SImode);\
}")

(define\_insn ""\
\[(set (mem:BLK (match\_operand:SI 0 "register\_operand" "r"))\
(mem:BLK (match\_operand:SI 1 "register\_operand" "r")))\
(use (match\_operand:SI 2 "arith32\_operand" "rn"))\
(use (match\_operand:SI 3 "immediate\_operand" "i"))\
(clobber (match\_operand:SI 4 "register\_operand" "=r"))\
(clobber (match\_operand:SI 5 "register\_operand" "=0"))\
(clobber (match\_operand:SI 6 "register\_operand" "=1"))]\
""\
"\* return output\_block\_move (operands);")\
;; Floating point move insns

;; This pattern forces (set (reg:DF ...) (const\_double ...))\
;; to be reloaded by putting the constant into memory.\
;; It must come before the more general movdf pattern.\
(define\_insn ""\
\[(set (match\_operand:DF 0 "general\_operand" "=r,f,o")\
(match\_operand:DF 1 "" "mG,m,G"))]\
"GET\_CODE (operands\[1]) == CONST\_DOUBLE"\
"\*\
{\
if (FP\_REG\_P (operands\[0]))\
return output\_fp\_move\_double (operands);\
if (operands\[1] == dconst0\_rtx && GET\_CODE (operands\[0]) == REG)\
{\
operands\[1] = gen\_rtx (REG, SImode, REGNO (operands\[0]) + 1);\
return "mov %%g0,%0;mov %%g0,%1";\
}\
if (operands\[1] == dconst0\_rtx && GET\_CODE (operands\[0]) == MEM)\
{\
if (CONSTANT\_ADDRESS\_P (XEXP (operands\[0], 0)))\
{\
if (! ((cc\_prev\_status.flags & CC\_KNOW\_HI\_G1)\
&& XEXP (operands\[0], 0) == cc\_prev\_status.mdep))\
{\
cc\_status.flags |= CC\_KNOW\_HI\_G1;\
cc\_status.mdep = XEXP (operands\[0], 0);\
output\_asm\_insn ("sethi %%hi(%m0),%%g1", operands);\
}\
return "st %%g0,\[%%g1+%%lo(%%m0)];st %%g0,\[%%g1+%%lo(%%m0)+4]";\
}\
operands\[1] = adj\_offsettable\_operand (operands\[0], 4);\
return "st %%g0,%0;st %%g0,%1";\
}\
return output\_move\_double (operands);\
}")

(define\_insn "movdf"\
\[(set (match\_operand:DF 0 "general\_operand" "=rm,\&r,?f,?rm")\
(match\_operand:DF 1 "general\_operand" "r,m,rfm,f"))]\
""\
"\*\
{\
if (GET\_CODE (operands\[0]) == MEM\
&& CONSTANT\_ADDRESS\_P (XEXP (operands\[0], 0)))\
return output\_store (operands);\
if (GET\_CODE (operands\[1]) == MEM\
&& CONSTANT\_ADDRESS\_P (XEXP (operands\[1], 0)))\
return output\_load\_floating (operands);

if (FP\_REG\_P (operands\[0]) || FP\_REG\_P (operands\[1]))\
return output\_fp\_move\_double (operands);\
return output\_move\_double (operands);\
}")

(define\_insn "movdi"\
\[(set (match\_operand:DI 0 "general\_operand" "=rm,\&r,?f,?rm")\
(match\_operand:DI 1 "general\_operand" "r,mi,rfm,f"))]\
""\
"\*\
{\
if (GET\_CODE (operands\[0]) == MEM\
&& CONSTANT\_ADDRESS\_P (XEXP (operands\[0], 0)))\
return output\_store (operands);\
if (GET\_CODE (operands\[1]) == MEM\
&& CONSTANT\_ADDRESS\_P (XEXP (operands\[1], 0)))\
return output\_load\_fixed (operands);

if (FP\_REG\_P (operands\[0]) || FP\_REG\_P (operands\[1]))\
return output\_fp\_move\_double (operands);\
return output\_move\_double (operands);\
}")

(define\_insn "movsf"\
\[(set (match\_operand:SF 0 "general\_operand" "=rf,m")\
(match\_operand:SF 1 "general\_operand" "rfm,rf"))]\
""\
"\*\
{\
if (GET\_CODE (operands\[0]) == MEM\
&& CONSTANT\_ADDRESS\_P (XEXP (operands\[0], 0)))\
return output\_store (operands);\
if (GET\_CODE (operands\[1]) == MEM\
&& CONSTANT\_ADDRESS\_P (XEXP (operands\[1], 0)))\
return output\_load\_floating (operands);\
if (FP\_REG\_P (operands\[0]))\
{\
if (FP\_REG\_P (operands\[1]))\
return "fmovs %1,%0";\
if (GET\_CODE (operands\[1]) == REG)\
return "st %r1,\[%%fp-4];ld \[%%fp-4],%0";\
if (CONSTANT\_ADDRESS\_P (XEXP (operands\[1], 0)))\
{\
cc\_status.flags |= CC\_KNOW\_HI\_G1;\
cc\_status.mdep = XEXP (operands\[1], 0);\
return "sethi %%hi(%m1),%%g1;ld \[%%g1+%%lo(%m1)],%0";\
}\
return "ld %1,%0";\
}\
if (FP\_REG\_P (operands\[1]))\
{\
if (GET\_CODE (operands\[0]) == REG)\
return "st %r1,\[%%fp-4];ld \[%%fp-4],%0";\
if (CONSTANT\_ADDRESS\_P (XEXP (operands\[0], 0)))\
{\
if (! ((cc\_prev\_status.flags & CC\_KNOW\_HI\_G1)\
&& XEXP (operands\[0], 0) == cc\_prev\_status.mdep))\
{\
cc\_status.flags |= CC\_KNOW\_HI\_G1;\
cc\_status.mdep = XEXP (operands\[0], 0);\
output\_asm\_insn ("sethi %%hi(%m0),%%g1", operands);\
}\
return "st %r1,\[%%g1+%%lo(%m0)]";\
}\
return "st %r1,%0";\
}\
if (GET\_CODE (operands\[0]) == MEM)\
return "st %r1,%0";\
if (GET\_CODE (operands\[1]) == MEM)\
return "ld %1,%0";\
return "mov %1,%0";\
}")\
;;- truncation instructions\
(define\_insn "truncsiqi2"\
\[(set (match\_operand:QI 0 "general\_operand" "=g")\
(truncate:QI\
(match\_operand:SI 1 "register\_operand" "r")))]\
""\
"\*\
{\
if (GET\_CODE (operands\[0]) == MEM)\
if (CONSTANT\_ADDRESS\_P (XEXP (operands\[0], 0)))\
{\
if (! ((cc\_prev\_status.flags & CC\_KNOW\_HI\_G1)\
&& XEXP (operands\[0], 0) == cc\_prev\_status.mdep))\
{\
cc\_status.flags |= CC\_KNOW\_HI\_G1;\
cc\_status.mdep = XEXP (operands\[0], 0);\
output\_asm\_insn ("sethi %%hi(%m0),%%g1", operands);\
}\
return "stb %1,\[%%g1+%%lo(%m0)]";\
}\
else\
return "stb %1,%0";\
return "mov %1,%0";\
}")

(define\_insn "trunchiqi2"\
\[(set (match\_operand:QI 0 "general\_operand" "=g")\
(truncate:QI\
(match\_operand:HI 1 "register\_operand" "r")))]\
""\
"\*\
{\
if (GET\_CODE (operands\[0]) == MEM)\
if (CONSTANT\_ADDRESS\_P (XEXP (operands\[0], 0)))\
{\
if (! ((cc\_prev\_status.flags & CC\_KNOW\_HI\_G1)\
&& XEXP (operands\[0], 0) == cc\_prev\_status.mdep))\
{\
cc\_status.flags |= CC\_KNOW\_HI\_G1;\
cc\_status.mdep = XEXP (operands\[0], 0);\
output\_asm\_insn ("sethi %%hi(%m0),%%g1", operands);\
}\
return "stb %1,\[%%g1+%%lo(%m0)]";\
}\
else\
return "stb %1,%0";\
return "mov %1,%0";\
}")

(define\_insn "truncsihi2"\
\[(set (match\_operand:HI 0 "general\_operand" "=g")\
(truncate:HI\
(match\_operand:SI 1 "register\_operand" "r")))]\
""\
"\*\
{\
if (GET\_CODE (operands\[0]) == MEM)\
if (CONSTANT\_ADDRESS\_P (XEXP (operands\[0], 0)))\
{\
if (! ((cc\_prev\_status.flags & CC\_KNOW\_HI\_G1)\
&& XEXP (operands\[0], 0) == cc\_prev\_status.mdep))\
{\
cc\_status.flags |= CC\_KNOW\_HI\_G1;\
cc\_status.mdep = XEXP (operands\[0], 0);\
output\_asm\_insn ("sethi %%hi(%m0),%%g1", operands);\
}\
return "sth %1,\[%%g1+%%lo(%m0)]";\
}\
else\
return "sth %1,%0";\
return "mov %1,%0";\
}")\
;;- zero extension instructions

;; Note that the one starting from HImode comes before those for QImode\
;; so that a constant operand will match HImode, not QImode.

(define\_insn "zero\_extendhisi2"\
\[(set (match\_operand:SI 0 "register\_operand" "=r")\
(zero\_extend:SI\
(match\_operand:HI 1 "general\_operand" "g")))]\
""\
"\*\
{\
if (REG\_P (operands\[1]))\
return "sll %1,0x10,%0;srl %0,0x10,%0";\
if (GET\_CODE (operands\[1]) == CONST\_INT)\
{\
operands\[1] = gen\_rtx (CONST\_INT, VOIDmode,\
INTVAL (operands\[1]) & 0xffff);\
output\_asm\_insn ("set %1,%0", operands);\
return "";\
}\
if (CONSTANT\_ADDRESS\_P (XEXP (operands\[1], 0)))\
{\
cc\_status.flags |= CC\_KNOW\_HI\_G1;\
cc\_status.mdep = XEXP (operands\[1], 0);\
return "sethi %%hi(%m1),%%g1;lduh \[%%g1+%%lo(%m1)],%0";\
}\
else\
return "lduh %1,%0";\
}")

(define\_insn "zero\_extendqihi2"\
\[(set (match\_operand:HI 0 "register\_operand" "=r")\
(zero\_extend:HI\
(match\_operand:QI 1 "general\_operand" "g")))]\
""\
"\*\
{\
if (REG\_P (operands\[1]))\
return "and %1,0xff,%0";\
if (GET\_CODE (operands\[1]) == CONST\_INT)\
{\
operands\[1] = gen\_rtx (CONST\_INT, VOIDmode,\
INTVAL (operands\[1]) & 0xff);\
output\_asm\_insn ("set %1,%0", operands);\
return "";\
}\
if (CONSTANT\_ADDRESS\_P (XEXP (operands\[1], 0)))\
{\
cc\_status.flags |= CC\_KNOW\_HI\_G1;\
cc\_status.mdep = XEXP (operands\[1], 0);\
return "sethi %%hi(%m1),%%g1;ldub \[%%g1+%%lo(%m1)],%0";\
}\
else\
return "ldub %1,%0";\
}")

(define\_insn "zero\_extendqisi2"\
\[(set (match\_operand:SI 0 "register\_operand" "=r")\
(zero\_extend:SI\
(match\_operand:QI 1 "general\_operand" "g")))]\
""\
"\*\
{\
if (REG\_P (operands\[1]))\
return "and %1,0xff,%0";\
if (GET\_CODE (operands\[1]) == CONST\_INT)\
{\
operands\[1] = gen\_rtx (CONST\_INT, VOIDmode,\
INTVAL (operands\[1]) & 0xff);\
output\_asm\_insn ("set %1,%0", operands);\
return "";\
}\
if (CONSTANT\_ADDRESS\_P (XEXP (operands\[1], 0)))\
{\
cc\_status.flags |= CC\_KNOW\_HI\_G1;\
cc\_status.mdep = XEXP (operands\[1], 0);\
return "sethi %%hi(%m1),%%g1;ldub \[%%g1+%%lo(%m1)],%0";\
}\
else\
return "ldub %1,%0";\
}")\
;;- sign extension instructions\
;; Note that the one starting from HImode comes before those for QImode\
;; so that a constant operand will match HImode, not QImode.

(define\_insn "extendhisi2"\
\[(set (match\_operand:SI 0 "register\_operand" "=r")\
(sign\_extend:SI\
(match\_operand:HI 1 "general\_operand" "g")))]\
""\
"\*\
{\
if (REG\_P (operands\[1]))\
return "sll %1,0x10,%0;sra %0,0x10,%0";\
if (GET\_CODE (operands\[1]) == CONST\_INT)\
{\
int i = (short)INTVAL (operands\[1]);\
operands\[1] = gen\_rtx (CONST\_INT, VOIDmode, i);\
output\_asm\_insn ("set %1,%0", operands);\
return "";\
}\
if (CONSTANT\_ADDRESS\_P (XEXP (operands\[1], 0)))\
{\
cc\_status.flags |= CC\_KNOW\_HI\_G1;\
cc\_status.mdep = XEXP (operands\[1], 0);\
return "sethi %%hi(%m1),%%g1;ldsh \[%%g1+%%lo(%m1)],%0";\
}\
else\
return "ldsh %1,%0";\
}")

(define\_insn "extendqihi2"\
\[(set (match\_operand:HI 0 "register\_operand" "=r")\
(sign\_extend:HI\
(match\_operand:QI 1 "general\_operand" "g")))]\
""\
"\*\
{\
if (REG\_P (operands\[1]))\
return "sll %1,0x18,%0;sra %0,0x18,%0";\
if (GET\_CODE (operands\[1]) == CONST\_INT)\
{\
int i = (char)INTVAL (operands\[1]);\
operands\[1] = gen\_rtx (CONST\_INT, VOIDmode, i);\
output\_asm\_insn ("set %1,%0", operands);\
return "";\
}\
if (CONSTANT\_ADDRESS\_P (XEXP (operands\[1], 0)))\
{\
cc\_status.flags |= CC\_KNOW\_HI\_G1;\
cc\_status.mdep = XEXP (operands\[1], 0);\
return "sethi %%hi(%m1),%%g1;ldsb \[%%g1+%%lo(%m1)],%0";\
}\
else\
return "ldsb %1,%0";\
}")

(define\_insn "extendqisi2"\
\[(set (match\_operand:SI 0 "register\_operand" "=r")\
(sign\_extend:SI\
(match\_operand:QI 1 "general\_operand" "g")))]\
""\
"\*\
{\
if (REG\_P (operands\[1]))\
return "sll %1,0x18,%0;sra %0,0x18,%0";\
if (GET\_CODE (operands\[1]) == CONST\_INT)\
{\
int i = (char)INTVAL (operands\[1]);\
operands\[1] = gen\_rtx (CONST\_INT, VOIDmode, i);\
output\_asm\_insn ("set %1,%0", operands);\
return "";\
}\
if (CONSTANT\_ADDRESS\_P (XEXP (operands\[1], 0)))\
{\
cc\_status.flags |= CC\_KNOW\_HI\_G1;\
cc\_status.mdep = XEXP (operands\[1], 0);\
return "sethi %%hi(%m1),%%g1;ldsb \[%%g1+%%lo(%m1)],%0";\
}\
else\
return "ldsb %1,%0";\
}")

;; Signed bitfield extractions come out looking like\
;; (shiftrt (shift (sign\_extend ) ) )\
;; which we expand poorly as four shift insns.\
;; These patters yeild two shifts:\
;; (shiftrt (shift ) )\
(define\_insn ""\
\[(set (match\_operand:SI 0 "register\_operand" "=r")\
(ashiftrt:SI\
(sign\_extend:SI\
(match\_operand:QI 1 "register\_operand" "r"))\
(match\_operand:SI 2 "small\_int" "n")))]\
""\
"sll %1,0x18,%0;sra %0,0x18+%2,%0")

(define\_insn ""\
\[(set (match\_operand:SI 0 "register\_operand" "=r")\
(ashiftrt:SI\
(sign\_extend:SI\
(subreg:QI (ashift:SI (match\_operand:SI 1 "register\_operand" "r")\
(match\_operand:SI 2 "small\_int" "n")) 0))\
(match\_operand:SI 3 "small\_int" "n")))]\
""\
"sll %1,0x18+%2,%0;sra %0,0x18+%3,%0")\
;; Special patterns for optimizing bit-field instructions.

;; First two patterns are for bitfields that came from memory\
;; testing only the high bit. They work with old combiner.\
;; @@ Actually, the second pattern does not work if we\
;; @@ need to set the N bit.\
(define\_insn ""\
\[(set (cc0)\
(zero\_extend:SI (subreg:QI (lshiftrt:SI (match\_operand:SI 0 "register\_operand" "r")\
(const\_int 7)) 0)))]\
"0"\
"andcc %0,128,%%g0")

(define\_insn ""\
\[(set (cc0)\
(sign\_extend:SI (subreg:QI (ashiftrt:SI (match\_operand:SI 0 "register\_operand" "r")\
(const\_int 7)) 0)))]\
"0"\
"andcc %0,128,%%g0")

;; next two patterns are good for bitfields coming from memory\
;; (via pseudo-register) or from a register, though this optimization\
;; is only good for values contained wholly within the bottom 13 bits\
(define\_insn ""\
\[(set (cc0)\
(and:SI (lshiftrt:SI (match\_operand:SI 0 "register\_operand" "r")\
(match\_operand:SI 1 "small\_int" "n"))\
(match\_operand:SI 2 "small\_int" "n")))]\
"(unsigned)((INTVAL (operands\[2]) << INTVAL (operands\[1])) + 0x1000) < 0x2000"\
"andcc %0,%2<<%1,%%g0")

(define\_insn ""\
\[(set (cc0)\
(and:SI (ashiftrt:SI (match\_operand:SI 0 "register\_operand" "r")\
(match\_operand:SI 1 "small\_int" "n"))\
(match\_operand:SI 2 "small\_int" "n")))]\
"(unsigned)((INTVAL (operands\[2]) << INTVAL (operands\[1])) + 0x1000) < 0x2000"\
"andcc %0,%2<<%1,%%g0")\
;; Conversions between float and double.

(define\_insn "extendsfdf2"\
\[(set (match\_operand:DF 0 "register\_operand" "=f")\
(float\_extend:DF\
(match\_operand:SF 1 "register\_operand" "f")))]\
""\
"fstod %1,%0")

(define\_insn "truncdfsf2"\
\[(set (match\_operand:SF 0 "register\_operand" "=f")\
(float\_truncate:SF\
(match\_operand:DF 1 "register\_operand" "f")))]\
""\
"fdtos %1,%0")\
;; Conversion between fixed point and floating point.\
;; Note that among the fix-to-float insns\
;; the ones that start with SImode come first.\
;; That is so that an operand that is a CONST\_INT\
;; (and therefore lacks a specific machine mode).\
;; will be recognized as SImode (which is always valid)\
;; rather than as QImode or HImode.

;; This pattern forces (set (reg:SF ...) (float:SF (const\_int ...)))\
;; to be reloaded by putting the constant into memory.\
;; It must come before the more general floatsisf2 pattern.\
(define\_insn ""\
\[(set (match\_operand:SF 0 "general\_operand" "=f")\
(float:SF (match\_operand 1 "" "m")))]\
"GET\_CODE (operands\[1]) == CONST\_INT"\
"\*\
{\
if (CONSTANT\_ADDRESS\_P (XEXP (operands\[1], 0)))\
{\
cc\_status.flags |= CC\_KNOW\_HI\_G1;\
cc\_status.mdep = XEXP (operands\[1], 0);\
return "sethi %%hi(%m1),%%g1;ld \[%%g1+%%lo(%m1)],%0;fitos %0,%0";\
}\
return "ld %1,%0;fitos %0,%0";\
}")

(define\_insn "floatsisf2"\
\[(set (match\_operand:SF 0 "general\_operand" "=f")\
(float:SF (match\_operand:SI 1 "general\_operand" "rfm")))]\
""\
"\*\
{\
if (GET\_CODE (operands\[1]) == MEM)\
if (CONSTANT\_ADDRESS\_P (XEXP (operands\[1], 0)))\
{\
cc\_status.flags |= CC\_KNOW\_HI\_G1;\
cc\_status.mdep = XEXP (operands\[1], 0);\
return "sethi %%hi(%m1),%%g1;ld \[%%g1+%%lo(%m1)],%0;fitos %0,%0";\
}\
else\
return "ld %1,%0;fitos %0,%0";\
else if (FP\_REG\_P (operands\[1]))\
return "fitos %1,%0";\
return "st %r1,\[%%fp-4];ld \[%%fp-4],%0;fitos %0,%0";\
}")

;; This pattern forces (set (reg:DF ...) (float:DF (const\_int ...)))\
;; to be reloaded by putting the constant into memory.\
;; It must come before the more general floatsidf2 pattern.\
(define\_insn ""\
\[(set (match\_operand:DF 0 "general\_operand" "=f")\
(float:DF (match\_operand 1 "" "m")))]\
"GET\_CODE (operands\[1]) == CONST\_INT"\
"\*\
{\
if (CONSTANT\_ADDRESS\_P (XEXP (operands\[1], 0)))\
{\
cc\_status.flags |= CC\_KNOW\_HI\_G1;\
cc\_status.mdep = XEXP (operands\[1], 0);\
return "sethi %%hi(%m1),%%g1;ld \[%%g1+%%lo(%m1)],%0;fitod %0,%0";\
}\
return "ld %1,%0;fitod %0,%0";\
}")

(define\_insn "floatsidf2"\
\[(set (match\_operand:DF 0 "general\_operand" "=f")\
(float:DF (match\_operand:SI 1 "general\_operand" "rfm")))]\
""\
"\*\
{\
if (GET\_CODE (operands\[1]) == MEM)\
if (CONSTANT\_ADDRESS\_P (XEXP (operands\[1], 0)))\
{\
cc\_status.flags |= CC\_KNOW\_HI\_G1;\
cc\_status.mdep = XEXP (operands\[1], 0);\
return "sethi %%hi(%m1),%%g1;ld \[%%g1+%%lo(%m1)],%0;fitod %0,%0";\
}\
else\
return "ld %1,%0;fitod %0,%0";\
else if (FP\_REG\_P (operands\[1]))\
return "fitod %1,%0";\
else\
return "st %r1,\[%%fp-4];ld \[%%fp-4],%0;fitod %0,%0";\
}")

;; Convert a float to an actual integer.\
;; Truncation is performed as part of the conversion.\
(define\_insn "fix\_truncsfsi2"\
\[(set (match\_operand:SI 0 "general\_operand" "=rm")\
(fix:SI (fix:SF (match\_operand:SF 1 "general\_operand" "fm"))))]\
""\
"\*\
{\
cc\_status.flags &= \~(CC\_F1\_IS\_0);\
if (FP\_REG\_P (operands\[1]))\
output\_asm\_insn ("fstoi %1,%%f1", operands);\
else if (CONSTANT\_ADDRESS\_P (XEXP (operands\[1], 0)))\
{\
cc\_status.flags |= CC\_KNOW\_HI\_G1;\
cc\_status.mdep = XEXP (operands\[1], 0);\
output\_asm\_insn ("sethi %%hi(%m1),%%g1;ld \[%%g1+%%lo(%m1)],%%f1;fstoi %%f1,%%f1", operands);\
}\
else\
output\_asm\_insn ("ld %1,%%f1;fstoi %%f1,%%f1", operands);\
if (GET\_CODE (operands\[0]) == MEM)\
if (CONSTANT\_ADDRESS\_P (XEXP (operands\[0], 0)))\
{\
if (! ((cc\_prev\_status.flags & CC\_KNOW\_HI\_G1)\
&& XEXP (operands\[0], 0) == cc\_prev\_status.mdep))\
{\
cc\_status.flags |= CC\_KNOW\_HI\_G1;\
cc\_status.mdep = XEXP (operands\[0], 0);\
output\_asm\_insn ("sethi %%hi(%m0),%%g1", operands);\
}\
return "st %%f1,\[%%g1+%%lo(%m0)]";\
}\
else\
return "st %%f1,%0";\
else\
return "st %%f1,\[%%fp-4];ld \[%%fp-4],%0";\
}")

(define\_insn "fix\_truncdfsi2"\
\[(set (match\_operand:SI 0 "general\_operand" "=rm")\
(fix:SI (fix:DF (match\_operand:DF 1 "general\_operand" "fm"))))]\
""\
"\*\
{\
cc\_status.flags &= \~CC\_F0\_IS\_0;\
if (FP\_REG\_P (operands\[1]))\
output\_asm\_insn ("fdtoi %1,%%f0", operands);\
else\
{\
rtx xoperands\[2];\
xoperands\[0] = gen\_rtx (REG, DFmode, 32);\
xoperands\[1] = operands\[1];\
output\_asm\_insn (output\_fp\_move\_double (xoperands), xoperands);\
output\_asm\_insn ("fdtoi %%f0,%%f0", 0);\
}\
if (GET\_CODE (operands\[0]) == MEM)\
if (CONSTANT\_ADDRESS\_P (XEXP (operands\[0], 0)))\
{\
if (! ((cc\_prev\_status.flags & CC\_KNOW\_HI\_G1)\
&& XEXP (operands\[0], 0) == cc\_prev\_status.mdep))\
{\
cc\_status.flags |= CC\_KNOW\_HI\_G1;\
cc\_status.mdep = XEXP (operands\[0], 0);\
output\_asm\_insn ("sethi %%hi(%m0),%%g1", operands);\
}\
return "st %%f0,\[%%g1+%%lo(%m0)]";\
}\
else\
return "st %%f0,%0";\
else\
return "st %%f0,\[%%fp-4];ld \[%%fp-4],%0";\
}")\
;;- arithmetic instructions

(define\_insn "addsi3"\
\[(set (match\_operand:SI 0 "register\_operand" "=r")\
(plus:SI (match\_operand:SI 1 "arith32\_operand" "%r")\
(match\_operand:SI 2 "arith32\_operand" "rn")))]\
""\
"\*\
{\
if (REG\_P (operands\[2]))\
return "add %1,%2,%0";\
if (SMALL\_INT (operands\[2]))\
return "add %1,%2,%0";\
cc\_status.flags &= \~CC\_KNOW\_HI\_G1;\
return "sethi %%hi(%2),%%g1;or %%lo(%2),%%g1,%%g1;add %1,%%g1,%0";\
}")

(define\_insn "subsi3"\
\[(set (match\_operand:SI 0 "register\_operand" "=r")\
(minus:SI (match\_operand:SI 1 "register\_operand" "r")\
(match\_operand:SI 2 "arith32\_operand" "rn")))]\
""\
"\*\
{\
if (REG\_P (operands\[2]))\
return "sub %1,%2,%0";\
if (SMALL\_INT (operands\[2]))\
return "sub %1,%2,%0";\
cc\_status.flags &= \~CC\_KNOW\_HI\_G1;\
return "sethi %%hi(%2),%%g1;or %%lo(%2),%%g1,%%g1;sub %1,%%g1,%0";\
}")

(define\_expand "mulsi3"\
\[(set (match\_operand:SI 0 "register\_operand" "r")\
(mult:SI (match\_operand:SI 1 "general\_operand" "")\
(match\_operand:SI 2 "general\_operand" "")))]\
""\
"\
{\
rtx src;

if (GET\_CODE (operands\[1]) == CONST\_INT)\
if (GET\_CODE (operands\[2]) == CONST\_INT)\
{\
emit\_move\_insn (operands\[0],\
gen\_rtx (CONST\_INT, VOIDmode,\
INTVAL (operands\[1]) \* INTVAL (operands\[2])));\
DONE;\
}\
else\
src = gen\_rtx (MULT, SImode,\
copy\_to\_mode\_reg (SImode, operands\[2]),\
operands\[1]);\
else if (GET\_CODE (operands\[2]) == CONST\_INT)\
src = gen\_rtx (MULT, SImode,\
copy\_to\_mode\_reg (SImode, operands\[1]),\
operands\[2]);\
else src = 0;

if (src)\
emit\_insn (gen\_rtx (SET, VOIDmode, operands\[0], src));\
else\
emit\_insn (gen\_rtx (PARALLEL, VOIDmode, gen\_rtvec (5,\
gen\_rtx (SET, VOIDmode, operands\[0],\
gen\_rtx (MULT, SImode, operands\[1], operands\[2])),\
gen\_rtx (CLOBBER, VOIDmode, gen\_rtx (REG, SImode, 8)),\
gen\_rtx (CLOBBER, VOIDmode, gen\_rtx (REG, SImode, 9)),\
gen\_rtx (CLOBBER, VOIDmode, gen\_rtx (REG, SImode, 12)),\
gen\_rtx (CLOBBER, VOIDmode, gen\_rtx (REG, SImode, 13)))));\
DONE;\
}")

(define\_expand "umulsi3"\
\[(set (match\_operand:SI 0 "register\_operand" "r")\
(umult:SI (match\_operand:SI 1 "general\_operand" "")\
(match\_operand:SI 2 "general\_operand" "")))]\
""\
"\
{\
rtx src;

if (GET\_CODE (operands\[1]) == CONST\_INT)\
if (GET\_CODE (operands\[2]) == CONST\_INT)\
{\
emit\_move\_insn (operands\[0],\
gen\_rtx (CONST\_INT, VOIDmode,\
(unsigned)INTVAL (operands\[1]) \* (unsigned)INTVAL (operands\[2])));\
DONE;\
}\
else\
src = gen\_rtx (UMULT, SImode,\
copy\_to\_mode\_reg (SImode, operands\[2]),\
operands\[1]);\
else if (GET\_CODE (operands\[2]) == CONST\_INT)\
src = gen\_rtx (UMULT, SImode,\
copy\_to\_mode\_reg (SImode, operands\[1]),\
operands\[2]);\
else src = 0;

if (src)\
emit\_insn (gen\_rtx (SET, VOIDmode, operands\[0], src));\
else\
emit\_insn (gen\_rtx (PARALLEL, VOIDmode, gen\_rtvec (5,\
gen\_rtx (SET, VOIDmode, operands\[0],\
gen\_rtx (UMULT, SImode, operands\[1], operands\[2])),\
gen\_rtx (CLOBBER, VOIDmode, gen\_rtx (REG, SImode, 8)),\
gen\_rtx (CLOBBER, VOIDmode, gen\_rtx (REG, SImode, 9)),\
gen\_rtx (CLOBBER, VOIDmode, gen\_rtx (REG, SImode, 12)),\
gen\_rtx (CLOBBER, VOIDmode, gen\_rtx (REG, SImode, 13)))));\
DONE;\
}")

(define\_insn ""\
\[(set (match\_operand:SI 0 "register\_operand" "=r")\
(mult:SI (match\_operand:SI 1 "register\_operand" "r")\
(match\_operand:SI 2 "immediate\_operand" "n")))]\
""\
"\* return output\_mul\_by\_constant (insn, operands, 0);")

(define\_insn ""\
\[(set (match\_operand:SI 0 "register\_operand" "=r")\
(umult:SI (match\_operand:SI 1 "register\_operand" "r")\
(match\_operand:SI 2 "immediate\_operand" "n")))]\
""\
"\* return output\_mul\_by\_constant (insn, operands, 1);")

(define\_insn ""\
\[(set (match\_operand:SI 0 "register\_operand" "=r")\
(mult:SI (match\_operand:SI 1 "general\_operand" "%r")\
(match\_operand:SI 2 "general\_operand" "r")))\
(clobber (reg:SI 8))\
(clobber (reg:SI 9))\
(clobber (reg:SI 12))\
(clobber (reg:SI 13))]\
""\
"\* return output\_mul\_insn (operands, 0);")

(define\_insn ""\
\[(set (match\_operand:SI 0 "register\_operand" "=r")\
(umult:SI (match\_operand:SI 1 "general\_operand" "%r")\
(match\_operand:SI 2 "general\_operand" "r")))\
(clobber (reg:SI 8))\
(clobber (reg:SI 9))\
(clobber (reg:SI 12))\
(clobber (reg:SI 13))]\
""\
"\* return output\_mul\_insn (operands, 1);")

;; this pattern is needed because cse may eliminate the multiplication,\
;; but leave the clobbers behind.

(define\_insn ""\
\[(set (match\_operand:SI 0 "register\_operand" "=r")\
(match\_operand:SI 1 "general\_operand" "g"))\
(clobber (reg:SI 8))\
(clobber (reg:SI 9))\
(clobber (reg:SI 12))\
(clobber (reg:SI 13))]\
""\
"\*\
{\
if (GET\_CODE (operands\[1]) == CONST\_INT)\
{\
if (SMALL\_INT (operands\[1]))\
return "mov %1,%0";\
return "sethi %%hi(%1),%0;or %%lo(%1),%0,%0";\
}\
if (GET\_CODE (operands\[1]) == MEM)\
return "ld %1,%0";\
return "mov %1,%0";\
}")

;; In case constant factor turns out to be -1.\
(define\_insn ""\
\[(set (match\_operand:SI 0 "register\_operand" "=r")\
(neg:SI (match\_operand:SI 1 "general\_operand" "rI")))\
(clobber (reg:SI 8))\
(clobber (reg:SI 9))\
(clobber (reg:SI 12))\
(clobber (reg:SI 13))]\
""\
"sub %%g0,%1,%0")

;;- and instructions (with compliment also)\
(define\_insn "andsi3"\
\[(set (match\_operand:SI 0 "register\_operand" "=r")\
(and:SI (match\_operand:SI 1 "arith32\_operand" "%r")\
(match\_operand:SI 2 "arith32\_operand" "rn")))]\
""\
"\*\
{\
if (REG\_P (operands\[2]) || SMALL\_INT (operands\[2]))\
return "and %1,%2,%0";\
cc\_status.flags &= \~CC\_KNOW\_HI\_G1;\
return "sethi %%hi(%2),%%g1;or %%lo(%2),%%g1,%%g1;and %1,%%g1,%0";\
}")

(define\_insn "andcbsi3"\
\[(set (match\_operand:SI 0 "register\_operand" "=r")\
(and:SI (match\_operand:SI 1 "register\_operand" "r")\
(not:SI (match\_operand:SI 2 "register\_operand" "r"))))]\
""\
"andn %1,%2,%0")

(define\_insn "iorsi3"\
\[(set (match\_operand:SI 0 "register\_operand" "=r")\
(ior:SI (match\_operand:SI 1 "arith32\_operand" "%r")\
(match\_operand:SI 2 "arith32\_operand" "rn")))]\
""\
"\*\
{\
if (REG\_P (operands\[2]) || SMALL\_INT (operands\[2]))\
return "or %1,%2,%0";\
cc\_status.flags &= \~CC\_KNOW\_HI\_G1;\
return "sethi %%hi(%2),%%g1;or %%lo(%2),%%g1,%%g1;or %1,%%g1,%0";\
}")

(define\_insn "iorcbsi3"\
\[(set (match\_operand:SI 0 "register\_operand" "=r")\
(ior:SI (match\_operand:SI 1 "register\_operand" "r")\
(not:SI (match\_operand:SI 2 "register\_operand" "r"))))]\
""\
"orn %1,%2,%0")

(define\_insn "xorsi3"\
\[(set (match\_operand:SI 0 "register\_operand" "=r")\
(xor:SI (match\_operand:SI 1 "arith32\_operand" "%r")\
(match\_operand:SI 2 "arith32\_operand" "rn")))]\
""\
"\*\
{\
if (REG\_P (operands\[2]) || SMALL\_INT (operands\[2]))\
return "xor %1,%2,%0";\
cc\_status.flags &= \~CC\_KNOW\_HI\_G1;\
return "sethi %%hi(%2),%%g1;or %%lo(%2),%%g1,%%g1;xor %1,%%g1,%0";\
}")

(define\_insn "xorcbsi3"\
\[(set (match\_operand:SI 0 "register\_operand" "=r")\
(xor:SI (match\_operand:SI 1 "register\_operand" "r")\
(not:SI (match\_operand:SI 2 "register\_operand" "r"))))]\
""\
"xnor %1,%2,%0")

;; We cannot use the "neg" pseudo insn because the Sun assembler\
;; does not know how to make it work for constants.\
(define\_insn "negsi2"\
\[(set (match\_operand:SI 0 "general\_operand" "=r")\
(neg:SI (match\_operand:SI 1 "arith\_operand" "rI")))]\
""\
"sub %%g0,%1,%0")

;; We cannot use the "not" pseudo insn because the Sun assembler\
;; does not know how to make it work for constants.\
(define\_insn "one\_cmplsi2"\
\[(set (match\_operand:SI 0 "general\_operand" "=r")\
(not:SI (match\_operand:SI 1 "arith\_operand" "rI")))]\
""\
"xnor %%g0,%1,%0")\
;; Floating point arithmetic instructions.

(define\_insn "adddf3"\
\[(set (match\_operand:DF 0 "register\_operand" "=f")\
(plus:DF (match\_operand:DF 1 "register\_operand" "f")\
(match\_operand:DF 2 "register\_operand" "f")))]\
""\
"faddd %1,%2,%0")

(define\_insn "addsf3"\
\[(set (match\_operand:SF 0 "register\_operand" "=f")\
(plus:SF (match\_operand:SF 1 "register\_operand" "f")\
(match\_operand:SF 2 "register\_operand" "f")))]\
""\
"fadds %1,%2,%0")

(define\_insn "subdf3"\
\[(set (match\_operand:DF 0 "register\_operand" "=f")\
(minus:DF (match\_operand:DF 1 "register\_operand" "f")\
(match\_operand:DF 2 "register\_operand" "f")))]\
""\
"fsubd %1,%2,%0")

(define\_insn "subsf3"\
\[(set (match\_operand:SF 0 "register\_operand" "=f")\
(minus:SF (match\_operand:SF 1 "register\_operand" "f")\
(match\_operand:SF 2 "register\_operand" "f")))]\
""\
"fsubs %1,%2,%0")

(define\_insn "muldf3"\
\[(set (match\_operand:DF 0 "register\_operand" "=f")\
(mult:DF (match\_operand:DF 1 "register\_operand" "f")\
(match\_operand:DF 2 "register\_operand" "f")))]\
""\
"fmuld %1,%2,%0")

(define\_insn "mulsf3"\
\[(set (match\_operand:SF 0 "register\_operand" "=f")\
(mult:SF (match\_operand:SF 1 "register\_operand" "f")\
(match\_operand:SF 2 "register\_operand" "f")))]\
""\
"fmuls %1,%2,%0")

(define\_insn "divdf3"\
\[(set (match\_operand:DF 0 "register\_operand" "=f")\
(div:DF (match\_operand:DF 1 "register\_operand" "f")\
(match\_operand:DF 2 "register\_operand" "f")))]\
""\
"fdivd %1,%2,%0")

(define\_insn "divsf3"\
\[(set (match\_operand:SF 0 "register\_operand" "=f")\
(div:SF (match\_operand:SF 1 "register\_operand" "f")\
(match\_operand:SF 2 "register\_operand" "f")))]\
""\
"fdivs %1,%2,%0")

(define\_insn "negdf2"\
\[(set (match\_operand:DF 0 "register\_operand" "=f")\
(neg:DF (match\_operand:DF 1 "register\_operand" "f")))]\
""\
"\*\
{\
output\_asm\_insn ("fnegs %1,%0", operands);\
if (REGNO (operands\[0]) != REGNO (operands\[1]))\
{\
operands\[0] = gen\_rtx (REG, VOIDmode, REGNO (operands\[0]) + 1);\
operands\[1] = gen\_rtx (REG, VOIDmode, REGNO (operands\[1]) + 1);\
output\_asm\_insn ("fmovs %1,%0", operands);\
}\
return "";\
}")

(define\_insn "negsf2"\
\[(set (match\_operand:SF 0 "register\_operand" "=f")\
(neg:SF (match\_operand:SF 1 "register\_operand" "f")))]\
""\
"fnegs %1,%0")

(define\_insn "absdf2"\
\[(set (match\_operand:DF 0 "register\_operand" "=f")\
(abs:DF (match\_operand:DF 1 "register\_operand" "f")))]\
""\
"\*\
{\
output\_asm\_insn ("fabss %1,%0", operands);\
if (REGNO (operands\[0]) != REGNO (operands\[1]))\
{\
operands\[0] = gen\_rtx (REG, VOIDmode, REGNO (operands\[0]) + 1);\
operands\[1] = gen\_rtx (REG, VOIDmode, REGNO (operands\[1]) + 1);\
output\_asm\_insn ("fmovs %1,%0", operands);\
}\
return "";\
}")

(define\_insn "abssf2"\
\[(set (match\_operand:SF 0 "register\_operand" "=f")\
(abs:SF (match\_operand:SF 1 "register\_operand" "f")))]\
""\
"fabss %1,%0")\
;; Shift instructions

;; Optimized special case of shifting.\
;; Must precede the general case.

(define\_insn ""\
\[(set (match\_operand:SI 0 "register\_operand" "=r")\
(ashiftrt:SI (match\_operand:SI 1 "memory\_operand" "m")\
(const\_int 24)))]\
""\
"\*\
{\
if (CONSTANT\_ADDRESS\_P (XEXP (operands\[1], 0)))\
{\
cc\_status.flags |= CC\_KNOW\_HI\_G1;\
cc\_status.mdep = XEXP (operands\[1], 0);\
return "sethi %%hi(%m1),%%g1;ldsb \[%%g1+%%lo(%m1)],%0";\
}\
return "ldsb %1,%0";\
}")

(define\_insn ""\
\[(set (match\_operand:SI 0 "register\_operand" "=r")\
(lshiftrt:SI (match\_operand:SI 1 "memory\_operand" "m")\
(const\_int 24)))]\
""\
"\*\
{\
if (CONSTANT\_ADDRESS\_P (XEXP (operands\[1], 0)))\
{\
cc\_status.flags |= CC\_KNOW\_HI\_G1;\
cc\_status.mdep = XEXP (operands\[1], 0);\
return "sethi %%hi(%m1),%%g1;ldub \[%%g1+%%lo(%m1)],%0";\
}\
return "ldub %1,%0";\
}")\
;;- arithmetic shift instructions\
(define\_insn "ashlsi3"\
\[(set (match\_operand:SI 0 "register\_operand" "=r")\
(ashift:SI (match\_operand:SI 1 "register\_operand" "r")\
(match\_operand:SI 2 "arith32\_operand" "rn")))]\
""\
"\*\
{\
if (GET\_CODE (operands\[2]) == CONST\_INT\
&& INTVAL (operands\[2]) >= 32)\
operands\[2] = gen\_rtx (CONST\_INT, VOIDmode, INTVAL (operands\[2]) & 31);\
return "sll %1,%2,%0";\
}")

(define\_insn "ashrsi3"\
\[(set (match\_operand:SI 0 "register\_operand" "=r")\
(ashiftrt:SI (match\_operand:SI 1 "register\_operand" "r")\
(match\_operand:SI 2 "arith32\_operand" "rn")))]\
""\
"\*\
{\
if (GET\_CODE (operands\[2]) == CONST\_INT\
&& INTVAL (operands\[2]) >= 32)\
operands\[2] = gen\_rtx (CONST\_INT, VOIDmode, INTVAL (operands\[2]) & 31);\
return "sra %1,%2,%0";\
}")

(define\_insn "lshrsi3"\
\[(set (match\_operand:SI 0 "register\_operand" "=r")\
(lshiftrt:SI (match\_operand:SI 1 "register\_operand" "r")\
(match\_operand:SI 2 "arith32\_operand" "rn")))]\
""\
"\*\
{\
if (GET\_CODE (operands\[2]) == CONST\_INT\
&& INTVAL (operands\[2]) >= 32)\
operands\[2] = gen\_rtx (CONST\_INT, VOIDmode, INTVAL (operands\[2]) & 31);\
return "srl %1,%2,%0";\
}")\
;; Unconditional and other jump instructions\
;; Note that for the Sparc, by setting the annul bit on an unconditional\
;; branch, the following insn is never executed. This saves us a nop,\
;; but requires a debugger which can handle annuled branches.\
(define\_insn "jump"\
\[(set (pc) (label\_ref (match\_operand 0 "" "")))]\
""\
"\*\
{\
extern int optimize;\
extern int flag\_no\_peephole;

if (optimize && !flag\_no\_peephole)\
return "b,a %l0";\
return "b %l0;nop";\
}")

;; Peephole optimizers recognize a few simple cases when delay insns are safe.\
;; Complex ones are up front. Simple ones after.

;; This pattern is just like the following one, but matches when there\
;; is a jump insn after the "delay" insn. Without this pattern, we\
;; de-optimize that case.

(define\_peephole\
\[(set (pc) (match\_operand 0 "" ""))\
(set (match\_operand:SI 1 "" "")\
(match\_operand:SI 2 "" ""))\
(set (pc) (label\_ref (match\_operand 3 "" "")))]\
"TARGET\_EAGER && operands\_satisfy\_eager\_branch\_peephole (operands, 2)"\
"\*\
{\
rtx xoperands\[2];\
rtx pat = gen\_rtx (SET, VOIDmode, operands\[1], operands\[2]);\
rtx delay\_insn = gen\_rtx (INSN, VOIDmode, 0, 0, 0, pat, -1, 0, 0);\
rtx label, head;\
int parity;

if (GET\_CODE (XEXP (operands\[0], 1)) == PC)\
{\
parity = 1;\
label = XEXP (XEXP (operands\[0], 2), 0);\
}\
else\
{\
parity = 0;\
label = XEXP (XEXP (operands\[0], 1), 0);\
}\
xoperands\[0] = XEXP (operands\[0], 0);\
xoperands\[1] = label;

head = next\_real\_insn\_no\_labels (label);

/\* If at the target of this label we set the condition codes,\
and the condition codes are already set for that value,\
advance, if we can, to the following insn. _/_\
_if (GET\_CODE (PATTERN (head)) == SET_\
_&& GET\_CODE (SET\_DEST (PATTERN (head))) == CC0_\
_&& cc\_status.value2 == SET\_SRC (PATTERN (head)))_\
_{_\
_rtx nhead = next\_real\_insn\_no\_labels (head);_\
_if (nhead_\
_&& GET\_CODE (nhead) == INSN_\
_&& GET\_CODE (PATTERN (nhead)) == SET_\
_&& strict\_single\_insn\_op\_p (SET\_SRC (PATTERN (nhead)),_\
_GET\_MODE (SET\_DEST (PATTERN (nhead))))_\
_&& strict\_single\_insn\_op\_p (SET\_DEST (PATTERN (nhead)), VOIDmode)_\
_/_ Moves between FP regs and CPU regs are two insns. \*/\
&& !(GET\_CODE (SET\_SRC (PATTERN (nhead))) == REG\
&& GET\_CODE (SET\_DEST (PATTERN (nhead))) == REG\
&& (FP\_REG\_P (SET\_SRC (PATTERN (nhead)))\
!= FP\_REG\_P (SET\_DEST (PATTERN (nhead))))))\
{\
head = nhead;\
}\
}

/\* Output the branch instruction first. \*/\
if (cc\_prev\_status.flags & CC\_IN\_FCCR)\
{\
if (parity)\
output\_asm\_insn ("fb%F0,a %l1 ! eager", xoperands);\
else\
output\_asm\_insn ("fb%C0,a %l1 ! eager", xoperands);\
}\
else\
{\
if (parity)\
output\_asm\_insn ("b%N0,a %l1 ! eager", xoperands);\
else\
output\_asm\_insn ("b%C0,a %l1 ! eager", xoperands);\
}

/\* Now steal the first insn of the target. \*/\
output\_eager\_then\_insn (head, operands);

XVECEXP (PATTERN (insn), 0, 0) = XVECEXP (PATTERN (insn), 0, 1);\
XVECEXP (PATTERN (insn), 0, 1) = XVECEXP (PATTERN (insn), 0, 2);

return output\_delayed\_branch ("b %l3 ! eager2", operands, insn);\
}")

;; Here is a peephole which recognizes where delay insns can be made safe:\
;; (1) following a conditional branch, if the target of the conditional branch\
;; has only one user (this insn), move the first insn into our delay slot\
;; and emit an annulled branch.\
;; (2) following a conditional branch, if we can execute the fall-through\
;; insn without risking any evil effects, then do that instead of a nop.

(define\_peephole\
\[(set (pc) (match\_operand 0 "" ""))\
(set (match\_operand:SI 1 "" "")\
(match\_operand:SI 2 "" ""))]\
"TARGET\_EAGER && operands\_satisfy\_eager\_branch\_peephole (operands, 1)"\
"\*\
{\
rtx xoperands\[2];\
rtx pat = gen\_rtx (SET, VOIDmode, operands\[1], operands\[2]);\
rtx delay\_insn = gen\_rtx (INSN, VOIDmode, 0, 0, 0, pat, -1, 0, 0);\
rtx label, head, prev = (rtx)1;\
int parity;

if (GET\_CODE (XEXP (operands\[0], 1)) == PC)\
{\
parity = 1;\
label = XEXP (XEXP (operands\[0], 2), 0);\
}\
else\
{\
parity = 0;\
label = XEXP (XEXP (operands\[0], 1), 0);\
}\
xoperands\[0] = XEXP (operands\[0], 0);\
xoperands\[1] = label;

if (LABEL\_NUSES (label) == 1)\
{\
prev = PREV\_INSN (label);\
while (prev\
&& (GET\_CODE (prev) == NOTE\
|| (GET\_CODE (prev) == INSN\
&& (GET\_CODE (PATTERN (prev)) == CLOBBER\
|| GET\_CODE (PATTERN (prev)) == USE))))\
prev = PREV\_INSN (prev);\
if (prev == 0\
|| GET\_CODE (prev) == BARRIER)\
{\
prev = 0;\
head = next\_real\_insn\_no\_labels (label);\
}\
}\
if (prev == 0\
&& head != 0\
&& ! INSN\_DELETED\_P (head)\
&& GET\_CODE (head) == INSN\
&& GET\_CODE (PATTERN (head)) == SET\
&& strict\_single\_insn\_op\_p (SET\_SRC (PATTERN (head)),\
GET\_MODE (SET\_DEST (PATTERN (head))))\
&& strict\_single\_insn\_op\_p (SET\_DEST (PATTERN (head)), VOIDmode)\
/\* Moves between FP regs and CPU regs are two insns. _/_\
_&& !(GET\_CODE (SET\_SRC (PATTERN (head))) == REG_\
_&& GET\_CODE (SET\_DEST (PATTERN (head))) == REG_\
_&& (FP\_REG\_P (SET\_SRC (PATTERN (head)))_\
_!= FP\_REG\_P (SET\_DEST (PATTERN (head))))))_\
_{_\
_/_ If at the target of this label we set the condition codes,\
and the condition codes are already set for that value,\
advance, if we can, to the following insn. _/_\
_if (GET\_CODE (PATTERN (head)) == SET_\
_&& GET\_CODE (SET\_DEST (PATTERN (head))) == CC0_\
_&& cc\_status.value2 == SET\_SRC (PATTERN (head)))_\
_{_\
_rtx nhead = next\_real\_insn\_no\_labels (head);_\
_if (nhead_\
_&& GET\_CODE (nhead) == INSN_\
_&& GET\_CODE (PATTERN (nhead)) == SET_\
_&& strict\_single\_insn\_op\_p (SET\_SRC (PATTERN (nhead)),_\
_GET\_MODE (SET\_DEST (nhead)))_\
_&& strict\_single\_insn\_op\_p (SET\_DEST (PATTERN (nhead)), VOIDmode)_\
_/_ Moves between FP regs and CPU regs are two insns. \*/\
&& !(GET\_CODE (SET\_SRC (PATTERN (nhead))) == REG\
&& GET\_CODE (SET\_DEST (PATTERN (nhead))) == REG\
&& (FP\_REG\_P (SET\_SRC (PATTERN (nhead)))\
!= FP\_REG\_P (SET\_DEST (PATTERN (nhead))))))\
head = nhead;\
}

```
  /* Output the branch instruction first.  */
  if (cc_prev_status.flags & CC_IN_FCCR)
{
  if (parity)
    output_asm_insn (\"fb%F0,a %l1 ! eager\", xoperands);
  else
    output_asm_insn (\"fb%C0,a %l1 ! eager\", xoperands);
}
  else
{
  if (parity)
    output_asm_insn (\"b%N0,a %l1 ! eager\", xoperands);
  else
    output_asm_insn (\"b%C0,a %l1 ! eager\", xoperands);
}

  /* Now steal the first insn of the target.  */
  output_eager_then_insn (head, operands);
}
```

else\
{\
/\* Output the branch instruction first. \*/\
if (cc\_prev\_status.flags & CC\_IN\_FCCR)\
{\
if (parity)\
output\_asm\_insn ("fb%F0 %l1 ! eager", xoperands);\
else\
output\_asm\_insn ("fb%C0 %l1 ! eager", xoperands);\
}\
else\
{\
if (parity)\
output\_asm\_insn ("b%N0 %l1 ! eager", xoperands);\
else\
output\_asm\_insn ("b%C0 %l1 ! eager", xoperands);\
}\
}\
return output\_delay\_insn (delay\_insn);\
}")

;; Here are two simple peepholes which fill the delay slot of\
;; an unconditional branch.

(define\_peephole\
\[(set (match\_operand:SI 0 "register\_operand" "=r")\
(match\_operand:SI 1 "single\_insn\_src\_p" "p"))\
(set (pc) (label\_ref (match\_operand 2 "" "")))]\
"single\_insn\_extra\_test (operands\[0], operands\[1])"\
"\* return output\_delayed\_branch ("b %l2", operands, insn);")

(define\_peephole\
\[(set (match\_operand:SI 0 "memory\_operand" "=m")\
(match\_operand:SI 1 "reg\_or\_0\_operand" "rJ"))\
(set (pc) (label\_ref (match\_operand 2 "" "")))]\
""\
"\* return output\_delayed\_branch ("b %l2", operands, insn);")

(define\_insn "tablejump"\
\[(set (pc) (match\_operand:SI 0 "register\_operand" "r"))\
(use (label\_ref (match\_operand 1 "" "")))]\
""\
"jmp %0;nop")

(define\_peephole\
\[(set (match\_operand:SI 0 "register\_operand" "=r")\
(match\_operand:SI 1 "single\_insn\_src\_p" "p"))\
(parallel \[(set (pc) (match\_operand:SI 2 "register\_operand" "r"))\
(use (label\_ref (match\_operand 3 "" "")))])]\
"REGNO (operands\[0]) != REGNO (operands\[2])\
&& single\_insn\_extra\_test (operands\[0], operands\[1])"\
"\* return output\_delayed\_branch ("jmp %2", operands, insn);")

(define\_peephole\
\[(set (match\_operand:SI 0 "memory\_operand" "=m")\
(match\_operand:SI 1 "reg\_or\_0\_operand" "rJ"))\
(parallel \[(set (pc) (match\_operand:SI 2 "register\_operand" "r"))\
(use (label\_ref (match\_operand 3 "" "")))])]\
""\
"\* return output\_delayed\_branch ("jmp %2", operands, insn);")

;;- jump to subroutine\
(define\_expand "call"\
\[(call (match\_operand:SI 0 "memory\_operand" "m")\
(match\_operand 1 "" "i"))]\
;; operand\[2] is next\_arg\_register\
""\
"\
{\
rtx fn\_rtx, nregs\_rtx;

if (TARGET\_SUN\_ASM && GET\_CODE (XEXP (operands\[0], 0)) == REG)\
{\
rtx g1\_rtx = gen\_rtx (REG, SImode, 1);\
emit\_move\_insn (g1\_rtx, XEXP (operands\[0], 0));\
fn\_rtx = gen\_rtx (MEM, SImode, g1\_rtx);\
}\
else\
fn\_rtx = operands\[0];

/\* Count the number of parameter registers being used by this call.\
if that argument is NULL, it means we are using them all, which\
means 6 on the sparc. \*/\
\#if 0\
if (operands\[2])\
nregs\_rtx = gen\_rtx (CONST\_INT, VOIDmode, REGNO (operands\[2]) - 8);\
else\
nregs\_rtx = gen\_rtx (CONST\_INT, VOIDmode, 6);\
\#else\
nregs\_rtx = const0\_rtx;\
\#endif

emit\_call\_insn (gen\_rtx (PARALLEL, VOIDmode, gen\_rtvec (2,\
gen\_rtx (CALL, VOIDmode, fn\_rtx, nregs\_rtx),\
gen\_rtx (USE, VOIDmode, gen\_rtx (REG, SImode, 31)))));\
DONE;\
}")

(define\_insn ""\
\[(call (match\_operand:SI 0 "memory\_operand" "m")\
(match\_operand 1 "" "i"))\
(use (reg:SI 31))]\
;;- Don't use operand 1 for most machines.\
""\
"\*\
{\
/\* strip the MEM. \*/\
operands\[0] = XEXP (operands\[0], 0);\
CC\_STATUS\_INIT;\
if (TARGET\_SUN\_ASM && GET\_CODE (operands\[0]) == REG)\
return "jmpl %a0,%%o7;nop";\
return "call %a0,%1;nop";\
}")

(define\_peephole\
\[(set (match\_operand:SI 0 "register\_operand" "=r")\
(match\_operand:SI 1 "single\_insn\_src\_p" "p"))\
(parallel \[(call (match\_operand:SI 2 "memory\_operand" "m")\
(match\_operand 3 "" "i"))\
(use (reg:SI 31))])]\
;;- Don't use operand 1 for most machines.\
"! reg\_mentioned\_p (operands\[0], operands\[2])\
&& single\_insn\_extra\_test (operands\[0], operands\[1])"\
"\*\
{\
/\* strip the MEM. \*/\
operands\[2] = XEXP (operands\[2], 0);\
if (TARGET\_SUN\_ASM && GET\_CODE (operands\[2]) == REG)\
return output\_delayed\_branch ("jmpl %a2,%%o7", operands, insn);\
return output\_delayed\_branch ("call %a2,%3", operands, insn);\
}")

(define\_peephole\
\[(set (match\_operand:SI 0 "memory\_operand" "=m")\
(match\_operand:SI 1 "reg\_or\_0\_operand" "rJ"))\
(parallel \[(call (match\_operand:SI 2 "memory\_operand" "m")\
(match\_operand 3 "" "i"))\
(use (reg:SI 31))])]\
;;- Don't use operand 1 for most machines.\
""\
"\*\
{\
/\* strip the MEM. \*/\
operands\[2] = XEXP (operands\[2], 0);\
if (TARGET\_SUN\_ASM && GET\_CODE (operands\[2]) == REG)\
return output\_delayed\_branch ("jmpl %a2,%%o7", operands, insn);\
return output\_delayed\_branch ("call %a2,%3", operands, insn);\
}")

(define\_expand "call\_value"\
\[(set (match\_operand 0 "register\_operand" "=rf")\
(call (match\_operand:SI 1 "memory\_operand" "m")\
(match\_operand 2 "" "i")))]\
;; operand 3 is next\_arg\_register\
""\
"\
{\
rtx fn\_rtx, nregs\_rtx;\
rtvec vec;

if (TARGET\_SUN\_ASM && GET\_CODE (XEXP (operands\[1], 0)) == REG)\
{\
rtx g1\_rtx = gen\_rtx (REG, SImode, 1);\
emit\_move\_insn (g1\_rtx, XEXP (operands\[1], 0));\
fn\_rtx = gen\_rtx (MEM, SImode, g1\_rtx);\
}\
else\
fn\_rtx = operands\[1];

\#if 0\
if (operands\[3])\
nregs\_rtx = gen\_rtx (CONST\_INT, VOIDmode, REGNO (operands\[3]) - 8);\
else\
nregs\_rtx = gen\_rtx (CONST\_INT, VOIDmode, 6);\
\#else\
nregs\_rtx = const0\_rtx;\
\#endif

vec = gen\_rtvec (2,\
gen\_rtx (SET, VOIDmode, operands\[0],\
gen\_rtx (CALL, VOIDmode, fn\_rtx, nregs\_rtx)),\
gen\_rtx (USE, VOIDmode, gen\_rtx (REG, SImode, 31)));

emit\_call\_insn (gen\_rtx (PARALLEL, VOIDmode, vec));\
DONE;\
}")

(define\_insn ""\
\[(set (match\_operand 0 "" "=rf")\
(call (match\_operand:SI 1 "memory\_operand" "m")\
(match\_operand 2 "" "i")))\
(use (reg:SI 31))]\
;;- Don't use operand 2 for most machines.\
""\
"\*\
{\
/\* strip the MEM. \*/\
operands\[1] = XEXP (operands\[1], 0);\
CC\_STATUS\_INIT;\
if (TARGET\_SUN\_ASM && GET\_CODE (operands\[1]) == REG)\
return "jmpl %a1,%%o7;nop";\
return "call %a1,%2;nop";\
}")

(define\_peephole\
\[(set (match\_operand:SI 0 "register\_operand" "=r")\
(match\_operand:SI 1 "single\_insn\_src\_p" "p"))\
(parallel \[(set (match\_operand 2 "" "=rf")\
(call (match\_operand:SI 3 "memory\_operand" "m")\
(match\_operand 4 "" "i")))\
(use (reg:SI 31))])]\
;;- Don't use operand 4 for most machines.\
"! reg\_mentioned\_p (operands\[0], operands\[3])\
&& single\_insn\_extra\_test (operands\[0], operands\[1])"\
"\*\
{\
/\* strip the MEM. \*/\
operands\[3] = XEXP (operands\[3], 0);\
if (TARGET\_SUN\_ASM && GET\_CODE (operands\[3]) == REG)\
return output\_delayed\_branch ("jmpl %a3,%%o7", operands, insn);\
return output\_delayed\_branch ("call %a3,%4", operands, insn);\
}")

(define\_peephole\
\[(set (match\_operand:SI 0 "memory\_operand" "=m")\
(match\_operand:SI 1 "reg\_or\_0\_operand" "rJ"))\
(parallel \[(set (match\_operand 2 "" "=rf")\
(call (match\_operand:SI 3 "memory\_operand" "m")\
(match\_operand 4 "" "i")))\
(use (reg:SI 31))])]\
;;- Don't use operand 4 for most machines.\
""\
"\*\
{\
/\* strip the MEM. \*/\
operands\[3] = XEXP (operands\[3], 0);\
if (TARGET\_SUN\_ASM && GET\_CODE (operands\[3]) == REG)\
return output\_delayed\_branch ("jmpl %a3,%%o7", operands, insn);\
return output\_delayed\_branch ("call %a3,%4", operands, insn);\
}")\
(define\_insn "return"\
\[(return)]\
"! TARGET\_EPILOGUE"\
"ret;restore")

(define\_peephole\
\[(set (reg:SI 24)\
(match\_operand:SI 0 "reg\_or\_0\_operand" "rJ"))\
(return)]\
"! TARGET\_EPILOGUE"\
"ret;restore %r0,0x0,%%o0")

(define\_peephole\
\[(set (reg:SI 24)\
(plus:SI (match\_operand:SI 0 "register\_operand" "r%")\
(match\_operand:SI 1 "arith\_operand" "rI")))\
(return)]\
"! TARGET\_EPILOGUE"\
"ret;restore %r0,%1,%%o0")

(define\_peephole\
\[(set (reg:SI 24)\
(minus:SI (match\_operand:SI 0 "register\_operand" "r")\
(match\_operand:SI 1 "small\_int" "I")))\
(return)]\
"! TARGET\_EPILOGUE"\
"ret;restore %0,-(%1),%%o0")

(define\_insn "nop"\
\[(const\_int 0)]\
""\
"nop")\
;;- Local variables:\
;;- mode:emacs-lisp\
;;- comment-start: ";;- "\
;;- eval: (set-syntax-table (copy-sequence (syntax-table)))\
;;- eval: (modify-syntax-entry ?\[ "(]")\
;;- eval: (modify-syntax-entry ?] ")\[")\
;;- eval: (modify-syntax-entry ?{ "(}")\
;;- eval: (modify-syntax-entry ?} "){")\
;;- End:
