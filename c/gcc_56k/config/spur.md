# spur

;;- Machine description for SPUR chip for GNU C compiler\
;; Copyright (C) 1988 Free Software Foundation, Inc.

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
;; This pattern is used for generating an "insn"\
;; which does just a compare and sets a (fictitious) condition code.

;; The actual SPUR insns are compare-and-conditional-jump.\
;; The define\_peephole's below recognize the combinations of\
;; compares and jumps, and output each pair as a single assembler insn.

;; Put cmpsi first among compare insns so it matches two CONST\_INT operands.

;; This controls RTL generation and register allocation.\
(define\_insn "cmpsi"\
\[(set (cc0)\
(compare (match\_operand:SI 0 "nonmemory\_operand" "rK")\
(match\_operand:SI 1 "nonmemory\_operand" "rK")))]\
""\
"\*\
{\
cc\_status.value1 = operands\[0], cc\_status.value2 = operands\[1];\
return "";\
}")

;; Put tstsi first among test insns so it matches a CONST\_INT operand.

;; We have to have this because cse can optimize the previous pattern\
;; into this one.

(define\_insn "tstsi"\
\[(set (cc0)\
(match\_operand:SI 0 "register\_operand" "r"))]\
""\
"\*\
{\
cc\_status.value1 = operands\[0], cc\_status.value2 = const0\_rtx;\
return "";\
}")

;; These control RTL generation for conditional jump insns\
;; and match them for register allocation.

(define\_insn "beq"\
\[(set (pc)\
(if\_then\_else (eq (cc0)\
(const\_int 0))\
(label\_ref (match\_operand 0 "" ""))\
(pc)))]\
""\
"\* return output\_compare (operands, "eq", "eq", "ne", "ne"); ")

(define\_insn "bne"\
\[(set (pc)\
(if\_then\_else (ne (cc0)\
(const\_int 0))\
(label\_ref (match\_operand 0 "" ""))\
(pc)))]\
""\
"\* return output\_compare (operands, "ne", "ne", "eq", "eq"); ")

(define\_insn "bgt"\
\[(set (pc)\
(if\_then\_else (gt (cc0)\
(const\_int 0))\
(label\_ref (match\_operand 0 "" ""))\
(pc)))]\
""\
"\* return output\_compare (operands, "gt", "lt", "le", "ge"); ")

(define\_insn "bgtu"\
\[(set (pc)\
(if\_then\_else (gtu (cc0)\
(const\_int 0))\
(label\_ref (match\_operand 0 "" ""))\
(pc)))]\
""\
"\* return output\_compare (operands, "ugt", "ult", "ule", "uge"); ")

(define\_insn "blt"\
\[(set (pc)\
(if\_then\_else (lt (cc0)\
(const\_int 0))\
(label\_ref (match\_operand 0 "" ""))\
(pc)))]\
""\
"\* return output\_compare (operands, "lt", "gt", "ge", "le"); ")

(define\_insn "bltu"\
\[(set (pc)\
(if\_then\_else (ltu (cc0)\
(const\_int 0))\
(label\_ref (match\_operand 0 "" ""))\
(pc)))]\
""\
"\* return output\_compare (operands, "ult", "ugt", "uge", "ule"); ")

(define\_insn "bge"\
\[(set (pc)\
(if\_then\_else (ge (cc0)\
(const\_int 0))\
(label\_ref (match\_operand 0 "" ""))\
(pc)))]\
""\
"\* return output\_compare (operands, "ge", "le", "lt", "gt"); ")

(define\_insn "bgeu"\
\[(set (pc)\
(if\_then\_else (geu (cc0)\
(const\_int 0))\
(label\_ref (match\_operand 0 "" ""))\
(pc)))]\
""\
"\* return output\_compare (operands, "uge", "ule", "ult", "ugt"); ")

(define\_insn "ble"\
\[(set (pc)\
(if\_then\_else (le (cc0)\
(const\_int 0))\
(label\_ref (match\_operand 0 "" ""))\
(pc)))]\
""\
"\* return output\_compare (operands, "le", "ge", "gt", "lt"); ")

(define\_insn "bleu"\
\[(set (pc)\
(if\_then\_else (leu (cc0)\
(const\_int 0))\
(label\_ref (match\_operand 0 "" ""))\
(pc)))]\
""\
"\* return output\_compare (operands, "ule", "uge", "ugt", "ult"); ")\
;; These match inverted jump insns for register allocation.

(define\_insn ""\
\[(set (pc)\
(if\_then\_else (eq (cc0)\
(const\_int 0))\
(pc)\
(label\_ref (match\_operand 0 "" ""))))]\
""\
"\* return output\_compare (operands, "ne", "ne", "eq", "eq"); ")

(define\_insn ""\
\[(set (pc)\
(if\_then\_else (ne (cc0)\
(const\_int 0))\
(pc)\
(label\_ref (match\_operand 0 "" ""))))]\
""\
"\* return output\_compare (operands, "eq", "eq", "ne", "ne"); ")

(define\_insn ""\
\[(set (pc)\
(if\_then\_else (gt (cc0)\
(const\_int 0))\
(pc)\
(label\_ref (match\_operand 0 "" ""))))]\
""\
"\* return output\_compare (operands, "le", "ge", "gt", "lt"); ")

(define\_insn ""\
\[(set (pc)\
(if\_then\_else (gtu (cc0)\
(const\_int 0))\
(pc)\
(label\_ref (match\_operand 0 "" ""))))]\
""\
"\* return output\_compare (operands, "ule", "uge", "ugt", "ult"); ")

(define\_insn ""\
\[(set (pc)\
(if\_then\_else (lt (cc0)\
(const\_int 0))\
(pc)\
(label\_ref (match\_operand 0 "" ""))))]\
""\
"\* return output\_compare (operands, "ge", "le", "lt", "gt"); ")

(define\_insn ""\
\[(set (pc)\
(if\_then\_else (ltu (cc0)\
(const\_int 0))\
(pc)\
(label\_ref (match\_operand 0 "" ""))))]\
""\
"\* return output\_compare (operands, "uge", "ule", "ult", "ugt"); ")

(define\_insn ""\
\[(set (pc)\
(if\_then\_else (ge (cc0)\
(const\_int 0))\
(pc)\
(label\_ref (match\_operand 0 "" ""))))]\
""\
"\* return output\_compare (operands, "lt", "gt", "ge", "le"); ")

(define\_insn ""\
\[(set (pc)\
(if\_then\_else (geu (cc0)\
(const\_int 0))\
(pc)\
(label\_ref (match\_operand 0 "" ""))))]\
""\
"\* return output\_compare (operands, "ult", "ugt", "uge", "ule"); ")

(define\_insn ""\
\[(set (pc)\
(if\_then\_else (le (cc0)\
(const\_int 0))\
(pc)\
(label\_ref (match\_operand 0 "" ""))))]\
""\
"\* return output\_compare (operands, "gt", "lt", "le", "ge"); ")

(define\_insn ""\
\[(set (pc)\
(if\_then\_else (leu (cc0)\
(const\_int 0))\
(pc)\
(label\_ref (match\_operand 0 "" ""))))]\
""\
"\* return output\_compare (operands, "ugt", "ult", "ule", "uge"); ")\
;; Move instructions

(define\_insn "movsi"\
\[(set (match\_operand:SI 0 "general\_operand" "=r,m")\
(match\_operand:SI 1 "general\_operand" "rmi,rJ"))]\
""\
"\*\
{\
if (GET\_CODE (operands\[0]) == MEM)\
return "st\_32 %r1,%0";\
if (GET\_CODE (operands\[1]) == MEM)\
return "ld\_32 %0,%1;nop";\
if (GET\_CODE (operands\[1]) == REG)\
return "add\_nt %0,%1,$0";\
if (GET\_CODE (operands\[1]) == SYMBOL\_REF && operands\[1]->unchanging)\
return "add\_nt %0,r24,$(%1-0b)";\
return "add\_nt %0,r0,%1";\
}")

(define\_insn ""\
\[(set (match\_operand:SI 0 "register\_operand" "=r")\
(mem:SI (plus:SI (match\_operand:SI 1 "register\_operand" "r")\
(match\_operand:SI 2 "register\_operand" "r"))))]\
""\
"ld\_32 %0,%1,%2;nop")\
;; Generate insns for moving single bytes.

(define\_expand "movqi"\
\[(set (match\_operand:QI 0 "general\_operand" "")\
(match\_operand:QI 1 "general\_operand" ""))]\
""\
"\
{\
if (GET\_CODE (operands\[0]) == MEM && GET\_CODE (operands\[1]) == MEM)\
operands\[1] = copy\_to\_reg (operands\[1]);

if (GET\_CODE (operands\[1]) == MEM)\
{\
rtx tem = gen\_reg\_rtx (SImode);\
rtx addr = force\_reg (SImode, XEXP (operands\[1], 0));\
rtx subreg;

```
  emit_move_insn (tem, gen_rtx (MEM, SImode, addr));
  if (GET_CODE (operands[0]) == SUBREG)
subreg = gen_rtx (SUBREG, SImode, SUBREG_REG (operands[0]),
		  SUBREG_WORD (operands[0]));
  else
subreg = gen_rtx (SUBREG, SImode, operands[0], 0);

  emit_insn (gen_rtx (SET, VOIDmode, subreg,
		  gen_rtx (ZERO_EXTRACT, SImode, tem,
			   gen_rtx (CONST_INT, VOIDmode, 8),
			   addr)));
}
```

else if (GET\_CODE (operands\[0]) == MEM)\
{\
rtx tem = gen\_reg\_rtx (SImode);\
rtx addr = force\_reg (SImode, XEXP (operands\[0], 0));\
rtx subreg;

```
  emit_move_insn (tem, gen_rtx (MEM, SImode, addr));
  if (! CONSTANT_ADDRESS_P (operands[1]))
{
  if (GET_CODE (operands[1]) == SUBREG)
    subreg = gen_rtx (SUBREG, SImode, SUBREG_REG (operands[1]),
		      SUBREG_WORD (operands[1]));
  else
    subreg = gen_rtx (SUBREG, SImode, operands[1], 0);
}

  emit_insn (gen_rtx (SET, VOIDmode,
		  gen_rtx (ZERO_EXTRACT, SImode, tem,
			   gen_rtx (CONST_INT, VOIDmode, 8),
			   addr),
		  subreg));
  emit_move_insn (gen_rtx (MEM, SImode, addr), tem);
}
```

else\
{\
emit\_insn (gen\_rtx (SET, VOIDmode, operands\[0], operands\[1]));\
}\
DONE;\
}")\
;; Recognize insns generated for moving single bytes.

(define\_insn ""\
\[(set (match\_operand:QI 0 "general\_operand" "=r,m")\
(match\_operand:QI 1 "general\_operand" "rmi,r"))]\
""\
"\*\
{\
if (GET\_CODE (operands\[0]) == MEM)\
return "st\_32 %1,%0";\
if (GET\_CODE (operands\[1]) == MEM)\
return "ld\_32 %0,%1;nop";\
if (GET\_CODE (operands\[1]) == REG)\
return "add\_nt %0,%1,$0";\
return "add\_nt %0,r0,%1";\
}")

(define\_insn ""\
\[(set (match\_operand:SI 0 "register\_operand" "=r")\
(zero\_extract:SI (match\_operand:SI 1 "register\_operand" "r")\
(const\_int 8)\
(match\_operand:SI 2 "nonmemory\_operand" "rI")))]\
""\
"extract %0,%1,%2")

(define\_insn ""\
\[(set (zero\_extract:SI (match\_operand:SI 0 "register\_operand" "+r")\
(const\_int 8)\
(match\_operand:SI 1 "nonmemory\_operand" "rI"))\
(match\_operand:SI 2 "nonmemory\_operand" "ri"))]\
""\
"wr\_insert %1;insert %0,%0,%2")

;; Constant propagation can optimize the previous pattern into this pattern.\
;\[Not any more. It could when the position-operand contains a MULT.]

;(define\_insn ""\
; \[(set (zero\_extract:QI (match\_operand:SI 0 "register\_operand" "+r")\
; (const\_int 8)\
; (match\_operand:SI 1 "immediate\_operand" "I"))\
; (match\_operand:QI 2 "register\_operand" "r"))]\
; "GET\_CODE (operands\[1]) == CONST\_INT\
; && INTVAL (operands\[1]) % 8 == 0\
; && (unsigned) INTVAL (operands\[1]) < 32"\
; "\*\
;{\
; operands\[1] = gen\_rtx (CONST\_INT, VOIDmode, INTVAL (operands\[1]) / 8);\
; return "wr\_insert 0,0,%1;insert %0,%0,%2";\
;}")\
;; The three define\_expand patterns on this page\
;; serve as subroutines of "movhi".

;; Generate code to fetch an aligned halfword from memory.\
;; Operand 0 is the destination register (HImode).\
;; Operand 1 is the memory address (SImode).\
;; Operand 2 is a temporary (SImode).\
;; Operand 3 is a temporary (SImode).\
;; Operand 4 is a temporary (QImode).

;; Operand 5 is an internal temporary (HImode).

(define\_expand "loadhi"\
\[(set (match\_operand:SI 2 "register\_operand" "")\
(mem:SI (match\_operand:SI 1 "register\_operand" "")))\
;; Extract the low byte.\
(set (subreg:SI (match\_dup 5) 0)\
(zero\_extract:SI (match\_dup 2) (const\_int 8) (match\_dup 1)))\
;; Form address of high byte.\
(set (match\_operand:SI 3 "register\_operand" "")\
(plus:SI (match\_dup 1) (const\_int 1)))\
;; Extract the high byte.\
(set (subreg:SI (match\_operand:QI 4 "register\_operand" "") 0)\
(zero\_extract:SI (match\_dup 2) (const\_int 8) (match\_dup 3)))\
;; Put the high byte in with the low one.\
(set (zero\_extract:SI (match\_dup 5) (const\_int 8) (const\_int 1))\
(subreg:SI (match\_dup 4) 0))\
(set (match\_operand:HI 0 "register\_operand" "") (match\_dup 5))]\
""\
"operands\[5] = gen\_reg\_rtx (HImode);")

;; Generate code to store an aligned halfword into memory.\
;; Operand 0 is the destination address (SImode).\
;; Operand 1 is the source register (HImode, not constant).\
;; Operand 2 is a temporary (SImode).\
;; Operand 3 is a temporary (SImode).\
;; Operand 4 is a temporary (QImode).

;; Operand 5 is an internal variable made from operand 1.

(define\_expand "storehi"\
\[(set (match\_operand:SI 2 "register\_operand" "")\
(mem:SI (match\_operand:SI 0 "register\_operand" "")))\
;; Insert the low byte.\
(set (zero\_extract:SI (match\_dup 2) (const\_int 8) (match\_dup 0))\
(match\_dup 5))\
;; Form address of high byte.\
(set (match\_operand:SI 3 "register\_operand" "")\
(plus:SI (match\_dup 0) (const\_int 1)))\
;; Extract the high byte from the source.\
(set (subreg:SI (match\_operand:QI 4 "register\_operand" "") 0)\
(zero\_extract:SI (match\_operand:HI 1 "register\_operand" "")\
(const\_int 8) (const\_int 1)))\
;; Store high byte into the memory word\
(set (zero\_extract:SI (match\_dup 2) (const\_int 8) (match\_dup 3))\
(subreg:SI (match\_dup 4) 0))\
;; Put memory word back into memory.\
(set (mem:SI (match\_dup 0))\
(match\_dup 2))]\
""\
"\
{\
if (GET\_CODE (operands\[1]) == SUBREG)\
operands\[5] = gen\_rtx (SUBREG, SImode, SUBREG\_REG (operands\[1]),\
SUBREG\_WORD (operands\[1]));\
else\
operands\[5] = gen\_rtx (SUBREG, SImode, operands\[1], 0);\
}")

;; Like storehi but operands\[1] is a CONST\_INT.

(define\_expand "storeinthi"\
\[(set (match\_operand:SI 2 "register\_operand" "")\
(mem:SI (match\_operand:SI 0 "register\_operand" "")))\
;; Insert the low byte.\
(set (zero\_extract:SI (match\_dup 2) (const\_int 8) (match\_dup 0))\
(match\_dup 5))\
;; Form address of high byte.\
(set (match\_operand:SI 3 "register\_operand" "")\
(plus:SI (match\_dup 0) (const\_int 1)))\
;; Store high byte into the memory word\
(set (zero\_extract:SI (match\_dup 2) (const\_int 8) (match\_dup 3))\
(match\_dup 6))\
;; Put memory word back into memory.\
(set (mem:SI (match\_dup 0))\
(match\_dup 2))]\
""\
" operands\[5] = gen\_rtx (CONST\_INT, VOIDmode, INTVAL (operands\[1]) & 255);\
operands\[6] = gen\_rtx (CONST\_INT, VOIDmode,\
(INTVAL (operands\[1]) >> 8) & 255);\
")\
;; Main entry for generating insns to move halfwords.

(define\_expand "movhi"\
\[(set (match\_operand:HI 0 "general\_operand" "")\
(match\_operand:HI 1 "general\_operand" ""))]\
""\
"\
{\
if (GET\_CODE (operands\[0]) == MEM && GET\_CODE (operands\[1]) == MEM)\
operands\[1] = copy\_to\_reg (operands\[1]);

if (GET\_CODE (operands\[1]) == MEM)\
{\
rtx insn =\
emit\_insn (gen\_loadhi (operands\[0],\
force\_reg (SImode, XEXP (operands\[1], 0)),\
gen\_reg\_rtx (SImode), gen\_reg\_rtx (SImode),\
gen\_reg\_rtx (QImode)));\
/\* Tell cse what value the loadhi produces, so it detect duplicates. \*/\
REG\_NOTES (insn) = gen\_rtx (EXPR\_LIST, REG\_EQUAL, operands\[1],\
REG\_NOTES (insn));\
}\
else if (GET\_CODE (operands\[0]) == MEM)\
{\
if (GET\_CODE (operands\[1]) == CONST\_INT)\
emit\_insn (gen\_storeinthi (force\_reg (SImode, XEXP (operands\[0], 0)),\
operands\[1],\
gen\_reg\_rtx (SImode), gen\_reg\_rtx (SImode),\
gen\_reg\_rtx (QImode)));\
else\
{\
if (CONSTANT\_P (operands\[1]))\
operands\[1] = force\_reg (HImode, operands\[1]);\
emit\_insn (gen\_storehi (force\_reg (SImode, XEXP (operands\[0], 0)),\
operands\[1],\
gen\_reg\_rtx (SImode), gen\_reg\_rtx (SImode),\
gen\_reg\_rtx (QImode)));\
}\
}\
else\
emit\_insn (gen\_rtx (SET, VOIDmode, operands\[0], operands\[1]));\
DONE;\
}")\
;; Recognize insns generated for moving halfwords.\
;; (Note that the extract and insert patterns for single-byte moves\
;; are also involved in recognizing some of the insns used for this purpose.)

(define\_insn ""\
\[(set (match\_operand:HI 0 "general\_operand" "=r,m")\
(match\_operand:HI 1 "general\_operand" "rmi,r"))]\
""\
"\*\
{\
if (GET\_CODE (operands\[0]) == MEM)\
return "st\_32 %1,%0";\
if (GET\_CODE (operands\[1]) == MEM)\
return "ld\_32 %0,%1;nop";\
if (GET\_CODE (operands\[1]) == REG)\
return "add\_nt %0,%1,$0";\
return "add\_nt %0,r0,%1";\
}")

(define\_insn ""\
\[(set (match\_operand:SI 0 "register\_operand" "=r")\
(zero\_extract:SI (match\_operand:HI 1 "register\_operand" "r")\
(const\_int 8)\
(match\_operand:SI 2 "nonmemory\_operand" "rI")))]\
""\
"extract %0,%1,%2")

(define\_insn ""\
\[(set (zero\_extract:SI (match\_operand:HI 0 "register\_operand" "+r")\
(const\_int 8)\
(match\_operand:SI 1 "nonmemory\_operand" "rI"))\
(match\_operand:SI 2 "nonmemory\_operand" "ri"))]\
""\
"wr\_insert %1;insert %0,%0,%2")

;; Constant propagation can optimize the previous pattern into this pattern.

;(define\_insn ""\
; \[(set (zero\_extract:QI (match\_operand:HI 0 "register\_operand" "+r")\
; (const\_int 8)\
; (match\_operand:SI 1 "immediate\_operand" "I"))\
; (match\_operand:QI 2 "register\_operand" "r"))]\
; "GET\_CODE (operands\[1]) == CONST\_INT\
; && INTVAL (operands\[1]) % 8 == 0\
; && (unsigned) INTVAL (operands\[1]) < 32"\
; "\*\
;{\
; operands\[1] = gen\_rtx (CONST\_INT, VOIDmode, INTVAL (operands\[1]) / 8);\
; return "wr\_insert 0,0,%1;insert %0,%0,%2";\
;}")\
;; This pattern forces (set (reg:DF ...) (const\_double ...))\
;; to be reloaded by putting the constant into memory.\
;; It must come before the more general movdf pattern.\
(define\_insn ""\
\[(set (match\_operand:DF 0 "general\_operand" "=\&r,f,\&o")\
(match\_operand:DF 1 "" "mG,m,G"))]\
"GET\_CODE (operands\[1]) == CONST\_DOUBLE"\
"\*\
{\
if (FP\_REG\_P (operands\[0]))\
return output\_fp\_move\_double (operands);\
if (operands\[1] == dconst0\_rtx && GET\_CODE (operands\[0]) == REG)\
{\
operands\[1] = gen\_rtx (REG, SImode, REGNO (operands\[0]) + 1);\
return "add\_nt %0,r0,$0;add\_nt %1,r0,$0";\
}\
if (operands\[1] == dconst0\_rtx && GET\_CODE (operands\[0]) == MEM)\
{\
operands\[1] = adj\_offsettable\_operand (operands\[0], 4);\
return "st\_32 r0,%0;st\_32 r0,%1";\
}\
return output\_move\_double (operands);\
}\
")

(define\_insn "movdf"\
\[(set (match\_operand:DF 0 "general\_operand" "=r,\&r,m,?f,?rm")\
(match\_operand:DF 1 "general\_operand" "r,m,r,rfm,f"))]\
""\
"\*\
{\
if (FP\_REG\_P (operands\[0]) || FP\_REG\_P (operands\[1]))\
return output\_fp\_move\_double (operands);\
return output\_move\_double (operands);\
}\
")

(define\_insn "movdi"\
\[(set (match\_operand:DI 0 "general\_operand" "=r,\&r,m,?f,?rm")\
(match\_operand:DI 1 "general\_operand" "r,m,r,rfm,f"))]\
""\
"\*\
{\
if (FP\_REG\_P (operands\[0]) || FP\_REG\_P (operands\[1]))\
return output\_fp\_move\_double (operands);\
return output\_move\_double (operands);\
}\
")

(define\_insn "movsf"\
\[(set (match\_operand:SF 0 "general\_operand" "=rf,m")\
(match\_operand:SF 1 "general\_operand" "rfm,rf"))]\
""\
"\*\
{\
if (FP\_REG\_P (operands\[0]))\
{\
if (FP\_REG\_P (operands\[1]))\
return "fmov %0,%1";\
if (GET\_CODE (operands\[1]) == REG)\
{\
rtx xoperands\[2];\
int offset = - get\_frame\_size () - 8;\
xoperands\[1] = operands\[1];\
xoperands\[0] = gen\_rtx (CONST\_INT, VOIDmode, offset);\
output\_asm\_insn ("st\_32 %1,r25,%0", xoperands);\
xoperands\[1] = operands\[0];\
output\_asm\_insn ("ld\_sgl %1,r25,%0;nop", xoperands);\
return "";\
}\
return "ld\_sgl %0,%1;nop";\
}\
if (FP\_REG\_P (operands\[1]))\
{\
if (GET\_CODE (operands\[0]) == REG)\
{\
rtx xoperands\[2];\
int offset = - get\_frame\_size () - 8;\
xoperands\[0] = gen\_rtx (CONST\_INT, VOIDmode, offset);\
xoperands\[1] = operands\[1];\
output\_asm\_insn ("st\_sgl %1,r25,%0", xoperands);\
xoperands\[1] = operands\[0];\
output\_asm\_insn ("ld\_32 %1,r25,%0;nop", xoperands);\
return "";\
}\
return "st\_sgl %1,%0";\
}\
if (GET\_CODE (operands\[0]) == MEM)\
return "st\_32 %r1,%0";\
if (GET\_CODE (operands\[1]) == MEM)\
return "ld\_32 %0,%1;nop";\
if (GET\_CODE (operands\[1]) == REG)\
return "add\_nt %0,%1,$0";\
return "add\_nt %0,r0,%1";\
}")\
;;- truncation instructions\
(define\_insn "truncsiqi2"\
\[(set (match\_operand:QI 0 "register\_operand" "=r")\
(truncate:QI\
(match\_operand:SI 1 "register\_operand" "r")))]\
""\
"add\_nt %0,%1,$0")

(define\_insn "trunchiqi2"\
\[(set (match\_operand:QI 0 "register\_operand" "=r")\
(truncate:QI\
(match\_operand:HI 1 "register\_operand" "r")))]\
""\
"add\_nt %0,%1,$0")

(define\_insn "truncsihi2"\
\[(set (match\_operand:HI 0 "register\_operand" "=r")\
(truncate:HI\
(match\_operand:SI 1 "register\_operand" "r")))]\
""\
"add\_nt %0,%1,$0")\
;;- zero extension instructions

;; Note that the one starting from HImode comes before those for QImode\
;; so that a constant operand will match HImode, not QImode.\
(define\_expand "zero\_extendhisi2"\
\[(set (match\_operand:SI 0 "register\_operand" "")\
(and:SI (match\_operand:HI 1 "register\_operand" "") ;Changed to SI below\
;; This constant is invalid, but reloading will handle it.\
;; It's useless to generate here the insns to construct it\
;; because constant propagation would simplify them anyway.\
(match\_dup 2)))]\
""\
"\
{\
if (GET\_CODE (operands\[1]) == SUBREG)\
operands\[1] = gen\_rtx (SUBREG, SImode, SUBREG\_REG (operands\[1]),\
SUBREG\_WORD (operands\[1]));\
else\
operands\[1] = gen\_rtx (SUBREG, SImode, operands\[1], 0);

operands\[2] = force\_reg (SImode, gen\_rtx (CONST\_INT, VOIDmode, 65535));\
}")

(define\_insn "zero\_extendqihi2"\
\[(set (match\_operand:HI 0 "register\_operand" "=r")\
(zero\_extend:HI\
(match\_operand:QI 1 "register\_operand" "r")))]\
""\
"extract %0,%1,$0")

(define\_insn "zero\_extendqisi2"\
\[(set (match\_operand:SI 0 "register\_operand" "=r")\
(zero\_extend:SI\
(match\_operand:QI 1 "register\_operand" "r")))]\
""\
"extract %0,%1,$0")\
;;- sign extension instructions\
;; Note that the one starting from HImode comes before those for QImode\
;; so that a constant operand will match HImode, not QImode.

(define\_expand "extendhisi2"\
\[(set (match\_dup 2)\
(and:SI (match\_operand:HI 1 "register\_operand" "") ;Changed to SI below\
(match\_dup 4)))\
(set (match\_dup 3) (plus:SI (match\_dup 2) (match\_dup 5)))\
(set (match\_operand:SI 0 "register\_operand" "")\
(xor:SI (match\_dup 3) (match\_dup 5)))]\
""\
"\
{\
if (GET\_CODE (operands\[1]) == SUBREG)\
operands\[1] = gen\_rtx (SUBREG, SImode, SUBREG\_REG (operands\[1]),\
SUBREG\_WORD (operands\[1]));\
else\
operands\[1] = gen\_rtx (SUBREG, SImode, operands\[1], 0);

operands\[2] = gen\_reg\_rtx (SImode);\
operands\[3] = gen\_reg\_rtx (SImode);\
operands\[4] = force\_reg (SImode, gen\_rtx (CONST\_INT, VOIDmode, 65535));\
operands\[5] = force\_reg (SImode, gen\_rtx (CONST\_INT, VOIDmode, -32768));\
}")

(define\_expand "extendqihi2"\
\[(set (match\_dup 2)\
(and:HI (match\_operand:QI 1 "register\_operand" "") ;Changed to SI below\
(const\_int 255)))\
(set (match\_dup 3)\
(plus:SI (match\_dup 2) (const\_int -128)))\
(set (match\_operand:HI 0 "register\_operand" "")\
(xor:SI (match\_dup 3) (const\_int -128)))]\
""\
"\
{\
if (GET\_CODE (operands\[1]) == SUBREG)\
operands\[1] = gen\_rtx (SUBREG, HImode, SUBREG\_REG (operands\[1]),\
SUBREG\_WORD (operands\[1]));\
else\
operands\[1] = gen\_rtx (SUBREG, HImode, operands\[1], 0);

operands\[2] = gen\_reg\_rtx (HImode);\
operands\[3] = gen\_reg\_rtx (HImode);\
}")

(define\_expand "extendqisi2"\
\[(set (match\_dup 2)\
(and:SI (match\_operand:QI 1 "register\_operand" "") ;Changed to SI below\
(const\_int 255)))\
(set (match\_dup 3) (plus:SI (match\_dup 2) (const\_int -128)))\
(set (match\_operand:SI 0 "register\_operand" "")\
(xor:SI (match\_dup 3) (const\_int -128)))]\
""\
"\
{\
if (GET\_CODE (operands\[1]) == SUBREG)\
operands\[1] = gen\_rtx (SUBREG, SImode, SUBREG\_REG (operands\[1]),\
SUBREG\_WORD (operands\[1]));\
else\
operands\[1] = gen\_rtx (SUBREG, SImode, operands\[1], 0);

operands\[2] = gen\_reg\_rtx (SImode);\
operands\[3] = gen\_reg\_rtx (SImode);\
}")\
;;- arithmetic instructions

(define\_insn "addsi3"\
\[(set (match\_operand:SI 0 "register\_operand" "=r")\
(plus:SI (match\_operand:SI 1 "nonmemory\_operand" "%r")\
(match\_operand:SI 2 "nonmemory\_operand" "rI")))]\
""\
"add %0,%1,%2")

(define\_insn ""\
\[(set (match\_operand:SI 0 "register\_operand" "=r")\
(plus:SI (match\_operand:SI 1 "nonmemory\_operand" "%r")\
(match\_operand:SI 2 "big\_immediate\_operand" "g")))]\
"GET\_CODE (operands\[2]) == CONST\_INT\
&& (unsigned) (INTVAL (operands\[2]) + 0x8000000) < 0x10000000"\
"\*\
{\
return\
output\_add\_large\_offset (operands\[0], operands\[1], INTVAL (operands\[2]));\
}")

(define\_insn "subsi3"\
\[(set (match\_operand:SI 0 "register\_operand" "=r")\
(minus:SI (match\_operand:SI 1 "register\_operand" "r")\
(match\_operand:SI 2 "nonmemory\_operand" "rI")))]\
""\
"sub %0,%1,%2")

(define\_insn "andsi3"\
\[(set (match\_operand:SI 0 "register\_operand" "=r")\
(and:SI (match\_operand:SI 1 "nonmemory\_operand" "%r")\
(match\_operand:SI 2 "nonmemory\_operand" "rI")))]\
""\
"and %0,%1,%2")

(define\_insn "iorsi3"\
\[(set (match\_operand:SI 0 "register\_operand" "=r")\
(ior:SI (match\_operand:SI 1 "nonmemory\_operand" "%r")\
(match\_operand:SI 2 "nonmemory\_operand" "rI")))]\
""\
"or %0,%1,%2")

(define\_insn "xorsi3"\
\[(set (match\_operand:SI 0 "register\_operand" "=r")\
(xor:SI (match\_operand:SI 1 "nonmemory\_operand" "%r")\
(match\_operand:SI 2 "nonmemory\_operand" "rI")))]\
""\
"xor %0,%1,%2")

(define\_insn "negsi2"\
\[(set (match\_operand:SI 0 "register\_operand" "=r")\
(neg:SI (match\_operand:SI 1 "nonmemory\_operand" "rI")))]\
""\
"sub %0,r0,%1")

(define\_insn "one\_cmplsi2"\
\[(set (match\_operand:SI 0 "register\_operand" "=r")\
(not:SI (match\_operand:SI 1 "register\_operand" "r")))]\
""\
"xor %0,%1,$-1")\
;; Floating point arithmetic instructions.

(define\_insn "adddf3"\
\[(set (match\_operand:DF 0 "register\_operand" "=f")\
(plus:DF (match\_operand:DF 1 "register\_operand" "f")\
(match\_operand:DF 2 "register\_operand" "f")))]\
"TARGET\_FPU"\
"fadd %0,%1,%2")

(define\_insn "addsf3"\
\[(set (match\_operand:SF 0 "register\_operand" "=f")\
(plus:SF (match\_operand:SF 1 "register\_operand" "f")\
(match\_operand:SF 2 "register\_operand" "f")))]\
"TARGET\_FPU"\
"fadd %0,%1,%2")

(define\_insn "subdf3"\
\[(set (match\_operand:DF 0 "register\_operand" "=f")\
(minus:DF (match\_operand:DF 1 "register\_operand" "f")\
(match\_operand:DF 2 "register\_operand" "f")))]\
"TARGET\_FPU"\
"fsub %0,%1,%2")

(define\_insn "subsf3"\
\[(set (match\_operand:SF 0 "register\_operand" "=f")\
(minus:SF (match\_operand:SF 1 "register\_operand" "f")\
(match\_operand:SF 2 "register\_operand" "f")))]\
"TARGET\_FPU"\
"fsub %0,%1,%2")

(define\_insn "muldf3"\
\[(set (match\_operand:DF 0 "register\_operand" "=f")\
(mult:DF (match\_operand:DF 1 "register\_operand" "f")\
(match\_operand:DF 2 "register\_operand" "f")))]\
"TARGET\_FPU"\
"fmul %0,%1,%2")

(define\_insn "mulsf3"\
\[(set (match\_operand:SF 0 "register\_operand" "=f")\
(mult:SF (match\_operand:SF 1 "register\_operand" "f")\
(match\_operand:SF 2 "register\_operand" "f")))]\
"TARGET\_FPU"\
"fmul %0,%1,%2")

(define\_insn "divdf3"\
\[(set (match\_operand:DF 0 "register\_operand" "=f")\
(div:DF (match\_operand:DF 1 "register\_operand" "f")\
(match\_operand:DF 2 "register\_operand" "f")))]\
"TARGET\_FPU"\
"fdiv %0,%1,%2")

(define\_insn "divsf3"\
\[(set (match\_operand:SF 0 "register\_operand" "=f")\
(div:SF (match\_operand:SF 1 "register\_operand" "f")\
(match\_operand:SF 2 "register\_operand" "f")))]\
"TARGET\_FPU"\
"fdiv %0,%1,%2")

(define\_insn "negdf2"\
\[(set (match\_operand:DF 0 "register\_operand" "=f")\
(neg:DF (match\_operand:DF 1 "nonmemory\_operand" "f")))]\
"TARGET\_FPU"\
"fneg %0,%1")

(define\_insn "negsf2"\
\[(set (match\_operand:SF 0 "register\_operand" "=f")\
(neg:SF (match\_operand:SF 1 "nonmemory\_operand" "f")))]\
"TARGET\_FPU"\
"fneg %0,%1")

(define\_insn "absdf2"\
\[(set (match\_operand:DF 0 "register\_operand" "=f")\
(abs:DF (match\_operand:DF 1 "nonmemory\_operand" "f")))]\
"TARGET\_FPU"\
"fabs %0,%1")

(define\_insn "abssf2"\
\[(set (match\_operand:SF 0 "register\_operand" "=f")\
(abs:SF (match\_operand:SF 1 "nonmemory\_operand" "f")))]\
"TARGET\_FPU"\
"fabs %0,%1")\
;; Shift instructions

(define\_insn ""\
\[(set (match\_operand:SI 0 "register\_operand" "=r")\
(ashift:SI (match\_operand:SI 1 "register\_operand" "r")\
(match\_operand:SI 2 "immediate\_operand" "I")))]\
"GET\_CODE (operands\[2]) == CONST\_INT"\
"\*\
{\
unsigned int amount = INTVAL (operands\[2]);

switch (amount)\
{\
case 0:\
return "add\_nt %0,%1,$0";\
case 1:\
return "sll %0,%1,$1";\
case 2:\
return "sll %0,%1,$2";\
default:\
output\_asm\_insn ("sll %0,%1,$3", operands);

```
  for (amount -= 3; amount >= 3; amount -= 3)
output_asm_insn (\"sll %0,%0,$3\", operands);

  if (amount > 0)
output_asm_insn (amount == 1 ? \"sll %0,%0,$1\" : \"sll %0,%0,$2\",
		 operands);
  return \"\";
}
```

}")

(define\_insn ""\
\[(set (match\_operand:SI 0 "register\_operand" "=r")\
(ashiftrt:SI (match\_operand:SI 1 "register\_operand" "r")\
(match\_operand:SI 2 "immediate\_operand" "I")))]\
"GET\_CODE (operands\[2]) == CONST\_INT"\
"\*\
{\
unsigned int amount = INTVAL (operands\[2]);

if (amount == 0)\
return "add\_nt %0,%1,$0";\
else\
output\_asm\_insn ("sra %0,%1,$1", operands);

for (amount -= 1; amount > 0; amount -= 1)\
output\_asm\_insn ("sra %0,%0,$1", operands);

return "";\
}")

(define\_insn ""\
\[(set (match\_operand:SI 0 "register\_operand" "=r")\
(lshiftrt:SI (match\_operand:SI 1 "register\_operand" "r")\
(match\_operand:SI 2 "immediate\_operand" "I")))]\
"GET\_CODE (operands\[2]) == CONST\_INT"\
"\*\
{\
unsigned int amount = INTVAL (operands\[2]);

if (amount == 0)\
return "add\_nt %0,%1,$0";\
else\
output\_asm\_insn ("srl %0,%1,$1", operands);

for (amount -= 1; amount > 0; amount -= 1)\
output\_asm\_insn ("srl %0,%0,$1", operands);

return "";\
}")

(define\_expand "ashlsi3"\
\[(set (match\_operand:SI 0 "register\_operand" "")\
(ashift:SI (match\_operand:SI 1 "register\_operand" "")\
(match\_operand:SI 2 "nonmemory\_operand" "")))]\
""\
"\
{\
if (GET\_CODE (operands\[2]) != CONST\_INT\
|| (! TARGET\_EXPAND\_SHIFTS && (unsigned) INTVAL (operands\[2]) > 3))\
FAIL;\
}")

(define\_expand "lshlsi3"\
\[(set (match\_operand:SI 0 "register\_operand" "")\
(ashift:SI (match\_operand:SI 1 "register\_operand" "")\
(match\_operand:SI 2 "nonmemory\_operand" "")))]\
""\
"\
{\
if (GET\_CODE (operands\[2]) != CONST\_INT\
|| (! TARGET\_EXPAND\_SHIFTS && (unsigned) INTVAL (operands\[2]) > 3))\
FAIL;\
}")

(define\_expand "ashrsi3"\
\[(set (match\_operand:SI 0 "register\_operand" "")\
(ashiftrt:SI (match\_operand:SI 1 "register\_operand" "")\
(match\_operand:SI 2 "nonmemory\_operand" "")))]\
""\
"\
{\
if (GET\_CODE (operands\[2]) != CONST\_INT\
|| (! TARGET\_EXPAND\_SHIFTS && (unsigned) INTVAL (operands\[2]) > 1))\
FAIL;\
}")

(define\_expand "lshrsi3"\
\[(set (match\_operand:SI 0 "register\_operand" "")\
(lshiftrt:SI (match\_operand:SI 1 "register\_operand" "")\
(match\_operand:SI 2 "nonmemory\_operand" "")))]\
""\
"\
{\
if (GET\_CODE (operands\[2]) != CONST\_INT\
|| (! TARGET\_EXPAND\_SHIFTS && (unsigned) INTVAL (operands\[2]) > 1))\
FAIL;\
}")\
;; Unconditional and other jump instructions\
(define\_insn "jump"\
\[(set (pc)\
(label\_ref (match\_operand 0 "" "")))]\
""\
"jump %l0;nop")

(define\_insn "tablejump"\
\[(set (pc) (match\_operand:SI 0 "register\_operand" "r"))\
(use (label\_ref (match\_operand 1 "" "")))]\
""\
"jump\_reg r0,%0;nop")

;;- jump to subroutine\
(define\_insn "call"\
\[(call (match\_operand:SI 0 "memory\_operand" "m")\
(match\_operand:SI 1 "general\_operand" "g"))]\
;;- Don't use operand 1 for most machines.\
""\
"add\_nt r2,%0;call .+8;jump\_reg r0,r2;nop")

(define\_insn "call\_value"\
\[(set (match\_operand 0 "" "=g")\
(call (match\_operand:SI 1 "memory\_operand" "m")\
(match\_operand:SI 2 "general\_operand" "g")))]\
;;- Don't use operand 1 for most machines.\
""\
"add\_nt r2,%1;call .+8;jump\_reg r0,r2;nop")

;; A memory ref with constant address is not normally valid.\
;; But it is valid in a call insns. This pattern allows the\
;; loading of the address to combine with the call.\
(define\_insn ""\
\[(call (mem:SI (match\_operand:SI 0 "" "i"))\
(match\_operand:SI 1 "general\_operand" "g"))]\
;;- Don't use operand 1 for most machines.\
"GET\_CODE (operands\[0]) == SYMBOL\_REF"\
"call %0;nop")

(define\_insn ""\
\[(set (match\_operand 0 "" "=g")\
(call (mem:SI (match\_operand:SI 1 "" "i"))\
(match\_operand:SI 2 "general\_operand" "g")))]\
;;- Don't use operand 1 for most machines.\
"GET\_CODE (operands\[1]) == SYMBOL\_REF"\
"call %1;nop")

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
