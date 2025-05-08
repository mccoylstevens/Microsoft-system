# ns32k

;; BUGS:\
;; Insert no-op between an insn with memory read-write operands\
;; following by a scale-indexing operation.\
;; The Sequent assembler does not allow addresses to be used\
;; except in insns which explicitly compute an effective address.\
;; I.e., one cannot say "cmpd \_p,@\_x"\
;; Implement unsigned multiplication??

;;- Machine descrption for GNU compiler\
;;- ns32000 Version\
;; Copyright (C) 1988 Free Software Foundation, Inc.\
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

;;- Instruction patterns. When multiple patterns apply,\
;;- the first one in the file is chosen.\
;;-\
;;- See file "rtl.def" for documentation on define\_insn, match\_\*, et. al.\
;;-\
;;- cpp macro #define NOTICE\_UPDATE\_CC in file tm.h handles condition code\
;;- updates for most instructions.

; tstsi is first test insn so that it is the one to match\
; a constant argument.

(define\_insn "tstsi"\
\[(set (cc0)\
(match\_operand:SI 0 "general\_operand" "rmn"))]\
""\
"\*\
{ cc\_status.flags |= CC\_REVERSED;\
operands\[1] = const0\_rtx;\
return "cmpqd %1,%0"; }")

(define\_insn "tsthi"\
\[(set (cc0)\
(match\_operand:HI 0 "general\_operand" "g"))]\
""\
"\*\
{ cc\_status.flags |= CC\_REVERSED;\
operands\[1] = const0\_rtx;\
return "cmpqw %1,%0"; }")

(define\_insn "tstqi"\
\[(set (cc0)\
(match\_operand:QI 0 "general\_operand" "g"))]\
""\
"\*\
{ cc\_status.flags |= CC\_REVERSED;\
operands\[1] = const0\_rtx;\
return "cmpqb %1,%0"; }")

(define\_insn "tstdf"\
\[(set (cc0)\
(match\_operand:DF 0 "general\_operand" "fmF"))]\
"TARGET\_32081"\
"\*\
{ cc\_status.flags |= CC\_REVERSED;\
operands\[1] = dconst0\_rtx;\
return "cmpl %1,%0"; }")

(define\_insn "tstsf"\
\[(set (cc0)\
(match\_operand:SF 0 "general\_operand" "fmF"))]\
"TARGET\_32081"\
"\*\
{ cc\_status.flags |= CC\_REVERSED;\
operands\[1] = fconst0\_rtx;\
return "cmpf %1,%0"; }")

;; Put cmpsi first among compare insns so it matches two CONST\_INT operands.

(define\_insn "cmpsi"\
\[(set (cc0)\
(compare (match\_operand:SI 0 "general\_operand" "rmn")\
(match\_operand:SI 1 "general\_operand" "rmn")))]\
""\
"\*\
{\
if (GET\_CODE (operands\[1]) == CONST\_INT)\
{\
int i = INTVAL (operands\[1]);\
if (i <= 7 && i >= -8)\
{\
cc\_status.flags |= CC\_REVERSED;\
return "cmpqd %1,%0";\
}\
}\
cc\_status.flags &= \~CC\_REVERSED;\
if (GET\_CODE (operands\[0]) == CONST\_INT)\
{\
int i = INTVAL (operands\[0]);\
if (i <= 7 && i >= -8)\
return "cmpqd %0,%1";\
}\
return "cmpd %0,%1";\
}")

(define\_insn "cmphi"\
\[(set (cc0)\
(compare (match\_operand:HI 0 "general\_operand" "g")\
(match\_operand:HI 1 "general\_operand" "g")))]\
""\
"\*\
{\
if (GET\_CODE (operands\[1]) == CONST\_INT)\
{\
short i = INTVAL (operands\[1]);\
if (i <= 7 && i >= -8)\
{\
cc\_status.flags |= CC\_REVERSED;\
if (INTVAL (operands\[1]) > 7)\
operands\[1] = gen\_rtx(CONST\_INT, VOIDmode, i);\
return "cmpqw %1,%0";\
}\
}\
cc\_status.flags &= \~CC\_REVERSED;\
if (GET\_CODE (operands\[0]) == CONST\_INT)\
{\
short i = INTVAL (operands\[0]);\
if (i <= 7 && i >= -8)\
{\
if (INTVAL (operands\[0]) > 7)\
operands\[0] = gen\_rtx(CONST\_INT, VOIDmode, i);\
return "cmpqw %0,%1";\
}\
}\
return "cmpw %0,%1";\
}")

(define\_insn "cmpqi"\
\[(set (cc0)\
(compare (match\_operand:QI 0 "general\_operand" "g")\
(match\_operand:QI 1 "general\_operand" "g")))]\
""\
"\*\
{\
if (GET\_CODE (operands\[1]) == CONST\_INT)\
{\
char i = INTVAL (operands\[1]);\
if (i <= 7 && i >= -8)\
{\
cc\_status.flags |= CC\_REVERSED;\
if (INTVAL (operands\[1]) > 7)\
operands\[1] = gen\_rtx(CONST\_INT, VOIDmode, i);\
return "cmpqb %1,%0";\
}\
}\
cc\_status.flags &= \~CC\_REVERSED;\
if (GET\_CODE (operands\[0]) == CONST\_INT)\
{\
char i = INTVAL (operands\[0]);\
if (i <= 7 && i >= -8)\
{\
if (INTVAL (operands\[0]) > 7)\
operands\[0] = gen\_rtx(CONST\_INT, VOIDmode, i);\
return "cmpqb %0,%1";\
}\
}\
return "cmpb %0,%1";\
}")

(define\_insn "cmpdf"\
\[(set (cc0)\
(compare (match\_operand:DF 0 "general\_operand" "fmF")\
(match\_operand:DF 1 "general\_operand" "fmF")))]\
"TARGET\_32081"\
"cmpl %0,%1")

(define\_insn "cmpsf"\
\[(set (cc0)\
(compare (match\_operand:SF 0 "general\_operand" "fmF")\
(match\_operand:SF 1 "general\_operand" "fmF")))]\
"TARGET\_32081"\
"cmpf %0,%1")\
(define\_insn "movdf"\
\[(set (match\_operand:DF 0 "general\_operand" "=\&fg<")\
(match\_operand:DF 1 "general\_operand" "fFg"))]\
""\
"\*\
{\
if (FP\_REG\_P (operands\[0]))\
{\
if (FP\_REG\_P (operands\[1]) || GET\_CODE (operands\[1]) == CONST\_DOUBLE)\
return "movl %1,%0";\
if (REG\_P (operands\[1]))\
{\
rtx xoperands\[2];\
xoperands\[1] = gen\_rtx (REG, SImode, REGNO (operands\[1]) + 1);\
output\_asm\_insn ("movd %1,tos", xoperands);\
output\_asm\_insn ("movd %1,tos", operands);\
return "movl tos,%0";\
}\
return "movl %1,%0";\
}\
else if (FP\_REG\_P (operands\[1]))\
{\
if (REG\_P (operands\[0]))\
{\
output\_asm\_insn ("movl %1,tos;movd tos,%0", operands);\
operands\[0] = gen\_rtx (REG, SImode, REGNO (operands\[0]) + 1);\
return "movd tos,%0";\
}\
else\
return "movl %1,%0";\
}\
return output\_move\_double (operands);\
}")

(define\_insn "movsf"\
\[(set (match\_operand:SF 0 "general\_operand" "=fg<")\
(match\_operand:SF 1 "general\_operand" "fFg"))]\
""\
"\*\
{\
if (FP\_REG\_P (operands\[0]))\
{\
if (GET\_CODE (operands\[1]) == REG && REGNO (operands\[1]) < 8)\
return "movd %1,tos;movf tos,%0";\
else\
return "movf %1,%0";\
}\
else if (FP\_REG\_P (operands\[1]))\
{\
if (REG\_P (operands\[0]))\
return "movf %1,tos;movd tos,%0";\
return "movf %1,%0";\
}\
else if (GET\_CODE (operands\[1]) == CONST\_DOUBLE)\
{\
union {int i\[2]; float f; double d;} convrt;\
convrt.i\[0] = CONST\_DOUBLE\_LOW (operands\[1]);\
convrt.i\[1] = CONST\_DOUBLE\_HIGH (operands\[1]);\
convrt.f = convrt.d;

```
  /* Is there a better machine-independent way to to this?  */
  operands[1] = gen_rtx (CONST_INT, VOIDmode, convrt.i[0]);
  return \"movd %1,%0\";
}
```

else return "movd %1,%0";\
}")

(define\_insn ""\
\[(set (match\_operand:TI 0 "memory\_operand" "=m")\
(match\_operand:TI 1 "memory\_operand" "m"))]\
""\
"movmd %1,%0,4")

(define\_insn "movdi"\
\[(set (match\_operand:DI 0 "general\_operand" "=\&g<")\
(match\_operand:DI 1 "general\_operand" "gF"))]\
""\
"\* return output\_move\_double (operands); ")

;; This special case must precede movsi.\
(define\_insn ""\
\[(set (reg:SI 17)\
(match\_operand:SI 0 "general\_operand" "rmn"))]\
""\
"lprd sp,%0")

(define\_insn "movsi"\
\[(set (match\_operand:SI 0 "general\_operand" "=g<")\
(match\_operand:SI 1 "general\_operand" "gx"))]\
""\
"\*\
{ if (GET\_CODE (operands\[1]) == CONST\_INT)\
{\
int i = INTVAL (operands\[1]);\
if (i <= 7 && i >= -8)\
return "movqd %1,%0";\
if (i < 0x4000 && i >= -0x4000)\
\#ifdef GNX\_V3\
return "addr %c1,%0";\
\#else\
return "addr @%c1,%0";\
\#endif\
return "movd %1,%0";\
}\
else if (GET\_CODE (operands\[1]) == REG)\
{\
if (REGNO (operands\[1]) < 16)\
return "movd %1,%0";\
else if (REGNO (operands\[1]) == FRAME\_POINTER\_REGNUM)\
{\
if (GET\_CODE(operands\[0]) == REG)\
return "sprd fp,%0";\
else\
return "addr 0(fp),%0" ;\
}\
else if (REGNO (operands\[1]) == STACK\_POINTER\_REGNUM)\
{\
if (GET\_CODE(operands\[0]) == REG)\
return "sprd sp,%0";\
else\
return "addr 0(sp),%0" ;\
}\
else abort (0);\
}\
else if (GET\_CODE (operands\[1]) == MEM)\
return "movd %1,%0";\
/\* Check if this effective address can be\
calculated faster by pulling it apart. \*/\
if (REG\_P (operands\[0])\
&& GET\_CODE (operands\[1]) == MULT\
&& GET\_CODE (XEXP (operands\[1], 1)) == CONST\_INT\
&& (INTVAL (XEXP (operands\[1], 1)) == 2\
|| INTVAL (XEXP (operands\[1], 1)) == 4))\
{\
rtx xoperands\[3];\
xoperands\[0] = operands\[0];\
xoperands\[1] = XEXP (operands\[1], 0);\
xoperands\[2] = gen\_rtx (CONST\_INT, VOIDmode, INTVAL (XEXP (operands\[1], 1)) >> 1);\
return output\_shift\_insn (xoperands);\
}\
return "addr %a1,%0";\
}")

(define\_insn "movhi"\
\[(set (match\_operand:HI 0 "general\_operand" "=g<")\
(match\_operand:HI 1 "general\_operand" "g"))]\
""\
"\*\
{\
if (GET\_CODE (operands\[1]) == CONST\_INT)\
{\
short i = INTVAL (operands\[1]);\
if (i <= 7 && i >= -8)\
{\
if (INTVAL (operands\[1]) > 7)\
operands\[1] =\
gen\_rtx (CONST\_INT, VOIDmode, i);\
return "movqw %1,%0";\
}\
}\
return "movw %1,%0";\
}")

(define\_insn "movstricthi"\
\[(set (strict\_low\_part (match\_operand:HI 0 "general\_operand" "+r"))\
(match\_operand:HI 1 "general\_operand" "g"))]\
""\
"\*\
{\
if (GET\_CODE (operands\[1]) == CONST\_INT\
&& INTVAL(operands\[1]) <= 7 && INTVAL(operands\[1]) >= -8)\
return "movqw %1,%0";\
return "movw %1,%0";\
}")

(define\_insn "movqi"\
\[(set (match\_operand:QI 0 "general\_operand" "=g<")\
(match\_operand:QI 1 "general\_operand" "g"))]\
""\
"\*\
{ if (GET\_CODE (operands\[1]) == CONST\_INT)\
{\
char char\_val = (char)INTVAL (operands\[1]);\
if (char\_val <= 7 && char\_val >= -8)\
{\
if (INTVAL (operands\[1]) > 7)\
operands\[1] =\
gen\_rtx (CONST\_INT, VOIDmode, char\_val);\
return "movqb %1,%0";\
}\
}\
return "movb %1,%0";\
}")

(define\_insn "movstrictqi"\
\[(set (strict\_low\_part (match\_operand:QI 0 "general\_operand" "+r"))\
(match\_operand:QI 1 "general\_operand" "g"))]\
""\
"\*\
{\
if (GET\_CODE (operands\[1]) == CONST\_INT\
&& INTVAL(operands\[1]) < 8 && INTVAL(operands\[1]) > -9)\
return "movqb %1,%0";\
return "movb %1,%0";\
}")\
;; The definition of this insn does not really explain what it does,\
;; but it should suffice\
;; that anything generated as this insn will be recognized as one\
;; and that it won't successfully combine with anything.\
(define\_insn "movstrsi"\
\[(set (match\_operand:BLK 0 "general\_operand" "=g")\
(match\_operand:BLK 1 "general\_operand" "g"))\
(use (match\_operand:SI 2 "general\_operand" "rmn"))\
(clobber (reg:SI 0))\
(clobber (reg:SI 1))\
(clobber (reg:SI 2))]\
""\
"\*\
{\
if (GET\_CODE (operands\[0]) != MEM || GET\_CODE (operands\[1]) != MEM)\
abort ();\
operands\[0] = XEXP (operands\[0], 0);\
operands\[1] = XEXP (operands\[1], 0);\
if (GET\_CODE (operands\[0]) == MEM)\
if (GET\_CODE (operands\[1]) == MEM)\
output\_asm\_insn ("movd %0,r2;movd %1,r1", operands);\
else\
output\_asm\_insn ("movd %0,r2;addr %a1,r1", operands);\
else if (GET\_CODE (operands\[1]) == MEM)\
output\_asm\_insn ("addr %a0,r2;movd %1,r1", operands);\
else\
output\_asm\_insn ("addr %a0,r2;addr %a1,r1", operands);

if (GET\_CODE (operands\[2]) == CONST\_INT && (INTVAL (operands\[2]) & 0x3) == 0)\
{\
operands\[2] = gen\_rtx (CONST\_INT, VOIDmode, INTVAL (operands\[2]) >> 2);\
if ((unsigned) INTVAL (operands\[2]) <= 7)\
return "movqd %2,r0;movsd";\
else\
return "movd %2,r0;movsd";\
}\
else\
{\
return "movd %2,r0;movsb";\
}\
}")\
;; Extension and truncation insns.\
;; Those for integer source operand\
;; are ordered widest source type first.

(define\_insn "truncsiqi2"\
\[(set (match\_operand:QI 0 "general\_operand" "=g<")\
(truncate:QI (match\_operand:SI 1 "general\_operand" "rmn")))]\
""\
"movb %1,%0")

(define\_insn "truncsihi2"\
\[(set (match\_operand:HI 0 "general\_operand" "=g<")\
(truncate:HI (match\_operand:SI 1 "general\_operand" "rmn")))]\
""\
"movw %1,%0")

(define\_insn "trunchiqi2"\
\[(set (match\_operand:QI 0 "general\_operand" "=g<")\
(truncate:QI (match\_operand:HI 1 "general\_operand" "g")))]\
""\
"movb %1,%0")

(define\_insn "extendhisi2"\
\[(set (match\_operand:SI 0 "general\_operand" "=g<")\
(sign\_extend:SI (match\_operand:HI 1 "general\_operand" "g")))]\
""\
"movxwd %1,%0")

(define\_insn "extendqihi2"\
\[(set (match\_operand:HI 0 "general\_operand" "=g<")\
(sign\_extend:HI (match\_operand:QI 1 "general\_operand" "g")))]\
""\
"movxbw %1,%0")

(define\_insn "extendqisi2"\
\[(set (match\_operand:SI 0 "general\_operand" "=g<")\
(sign\_extend:SI (match\_operand:QI 1 "general\_operand" "g")))]\
""\
"movxbd %1,%0")

(define\_insn "extendsfdf2"\
\[(set (match\_operand:DF 0 "general\_operand" "=fm<")\
(float\_extend:DF (match\_operand:SF 1 "general\_operand" "fmF")))]\
"TARGET\_32081"\
"movfl %1,%0")

(define\_insn "truncdfsf2"\
\[(set (match\_operand:SF 0 "general\_operand" "=fm<")\
(float\_truncate:SF (match\_operand:DF 1 "general\_operand" "fmF")))]\
"TARGET\_32081"\
"movlf %1,%0")

(define\_insn "zero\_extendhisi2"\
\[(set (match\_operand:SI 0 "general\_operand" "=g<")\
(zero\_extend:SI (match\_operand:HI 1 "general\_operand" "g")))]\
""\
"movzwd %1,%0")

(define\_insn "zero\_extendqihi2"\
\[(set (match\_operand:HI 0 "general\_operand" "=g<")\
(zero\_extend:HI (match\_operand:QI 1 "general\_operand" "g")))]\
""\
"movzbw %1,%0")

(define\_insn "zero\_extendqisi2"\
\[(set (match\_operand:SI 0 "general\_operand" "=g<")\
(zero\_extend:SI (match\_operand:QI 1 "general\_operand" "g")))]\
""\
"movzbd %1,%0")\
;; Fix-to-float conversion insns.\
;; Note that the ones that start with SImode come first.\
;; That is so that an operand that is a CONST\_INT\
;; (and therefore lacks a specific machine mode).\
;; will be recognized as SImode (which is always valid)\
;; rather than as QImode or HImode.

;; Rumor has it that the National part does not correctly convert\
;; constant ints to floats. This conversion is therefore disabled.\
;; A register must be used to perform the conversion.

(define\_insn "floatsisf2"\
\[(set (match\_operand:SF 0 "general\_operand" "=fm<")\
(float:SF (match\_operand:SI 1 "general\_operand" "rm")))]\
"TARGET\_32081"\
"movdf %1,%0")

(define\_insn "floatsidf2"\
\[(set (match\_operand:DF 0 "general\_operand" "=fm<")\
(float:DF (match\_operand:SI 1 "general\_operand" "rm")))]\
"TARGET\_32081"\
"movdl %1,%0")

(define\_insn "floathisf2"\
\[(set (match\_operand:SF 0 "general\_operand" "=fm<")\
(float:SF (match\_operand:HI 1 "general\_operand" "rm")))]\
"TARGET\_32081"\
"movwf %1,%0")

(define\_insn "floathidf2"\
\[(set (match\_operand:DF 0 "general\_operand" "=fm<")\
(float:DF (match\_operand:HI 1 "general\_operand" "rm")))]\
"TARGET\_32081"\
"movwl %1,%0")

(define\_insn "floatqisf2"\
\[(set (match\_operand:SF 0 "general\_operand" "=fm<")\
(float:SF (match\_operand:QI 1 "general\_operand" "rm")))]\
"TARGET\_32081"\
"movbf %1,%0")

; Some assemblers warn that this insn doesn't work.\
; Maybe they know something we don't.\
;(define\_insn "floatqidf2"\
; \[(set (match\_operand:DF 0 "general\_operand" "=fm<")\
; (float:DF (match\_operand:QI 1 "general\_operand" "rm")))]\
; "TARGET\_32081"\
; "movbl %1,%0")\
;; Float-to-fix conversion insns.\
;; The sequent compiler always generates "trunc" insns.

(define\_insn "fixsfqi2"\
\[(set (match\_operand:QI 0 "general\_operand" "=g<")\
(fix:QI (fix:SF (match\_operand:SF 1 "general\_operand" "fm"))))]\
"TARGET\_32081"\
"truncfb %1,%0")

(define\_insn "fixsfhi2"\
\[(set (match\_operand:HI 0 "general\_operand" "=g<")\
(fix:HI (fix:SF (match\_operand:SF 1 "general\_operand" "fm"))))]\
"TARGET\_32081"\
"truncfw %1,%0")

(define\_insn "fixsfsi2"\
\[(set (match\_operand:SI 0 "general\_operand" "=g<")\
(fix:SI (fix:SF (match\_operand:SF 1 "general\_operand" "fm"))))]\
"TARGET\_32081"\
"truncfd %1,%0")

(define\_insn "fixdfqi2"\
\[(set (match\_operand:QI 0 "general\_operand" "=g<")\
(fix:QI (fix:DF (match\_operand:DF 1 "general\_operand" "fm"))))]\
"TARGET\_32081"\
"trunclb %1,%0")

(define\_insn "fixdfhi2"\
\[(set (match\_operand:HI 0 "general\_operand" "=g<")\
(fix:HI (fix:DF (match\_operand:DF 1 "general\_operand" "fm"))))]\
"TARGET\_32081"\
"trunclw %1,%0")

(define\_insn "fixdfsi2"\
\[(set (match\_operand:SI 0 "general\_operand" "=g<")\
(fix:SI (fix:DF (match\_operand:DF 1 "general\_operand" "fm"))))]\
"TARGET\_32081"\
"truncld %1,%0")

;; Unsigned

(define\_insn "fixunssfqi2"\
\[(set (match\_operand:QI 0 "general\_operand" "=g<")\
(unsigned\_fix:QI (fix:SF (match\_operand:SF 1 "general\_operand" "fm"))))]\
"TARGET\_32081"\
"truncfb %1,%0")

(define\_insn "fixunssfhi2"\
\[(set (match\_operand:HI 0 "general\_operand" "=g<")\
(unsigned\_fix:HI (fix:SF (match\_operand:SF 1 "general\_operand" "fm"))))]\
"TARGET\_32081"\
"truncfw %1,%0")

(define\_insn "fixunssfsi2"\
\[(set (match\_operand:SI 0 "general\_operand" "=g<")\
(unsigned\_fix:SI (fix:SF (match\_operand:SF 1 "general\_operand" "fm"))))]\
"TARGET\_32081"\
"truncfd %1,%0")

(define\_insn "fixunsdfqi2"\
\[(set (match\_operand:QI 0 "general\_operand" "=g<")\
(unsigned\_fix:QI (fix:DF (match\_operand:DF 1 "general\_operand" "fm"))))]\
"TARGET\_32081"\
"trunclb %1,%0")

(define\_insn "fixunsdfhi2"\
\[(set (match\_operand:HI 0 "general\_operand" "=g<")\
(unsigned\_fix:HI (fix:DF (match\_operand:DF 1 "general\_operand" "fm"))))]\
"TARGET\_32081"\
"trunclw %1,%0")

(define\_insn "fixunsdfsi2"\
\[(set (match\_operand:SI 0 "general\_operand" "=g<")\
(unsigned\_fix:SI (fix:DF (match\_operand:DF 1 "general\_operand" "fm"))))]\
"TARGET\_32081"\
"truncld %1,%0")

;;; These are not yet used by GCC\
(define\_insn "fix\_truncsfqi2"\
\[(set (match\_operand:QI 0 "general\_operand" "=g<")\
(fix:QI (match\_operand:SF 1 "general\_operand" "fm")))]\
"TARGET\_32081"\
"truncfb %1,%0")

(define\_insn "fix\_truncsfhi2"\
\[(set (match\_operand:HI 0 "general\_operand" "=g<")\
(fix:HI (match\_operand:SF 1 "general\_operand" "fm")))]\
"TARGET\_32081"\
"truncfw %1,%0")

(define\_insn "fix\_truncsfsi2"\
\[(set (match\_operand:SI 0 "general\_operand" "=g<")\
(fix:SI (match\_operand:SF 1 "general\_operand" "fm")))]\
"TARGET\_32081"\
"truncfd %1,%0")

(define\_insn "fix\_truncdfqi2"\
\[(set (match\_operand:QI 0 "general\_operand" "=g<")\
(fix:QI (match\_operand:DF 1 "general\_operand" "fm")))]\
"TARGET\_32081"\
"trunclb %1,%0")

(define\_insn "fix\_truncdfhi2"\
\[(set (match\_operand:HI 0 "general\_operand" "=g<")\
(fix:HI (match\_operand:DF 1 "general\_operand" "fm")))]\
"TARGET\_32081"\
"trunclw %1,%0")

(define\_insn "fix\_truncdfsi2"\
\[(set (match\_operand:SI 0 "general\_operand" "=g<")\
(fix:SI (match\_operand:DF 1 "general\_operand" "fm")))]\
"TARGET\_32081"\
"truncld %1,%0")\
;;- All kinds of add instructions.

(define\_insn "adddf3"\
\[(set (match\_operand:DF 0 "general\_operand" "=fm")\
(plus:DF (match\_operand:DF 1 "general\_operand" "%0")\
(match\_operand:DF 2 "general\_operand" "fmF")))]\
"TARGET\_32081"\
"addl %2,%0")

(define\_insn "addsf3"\
\[(set (match\_operand:SF 0 "general\_operand" "=fm")\
(plus:SF (match\_operand:SF 1 "general\_operand" "%0")\
(match\_operand:SF 2 "general\_operand" "fmF")))]\
"TARGET\_32081"\
"addf %2,%0")

(define\_insn ""\
\[(set (reg:SI 17)\
(plus:SI (reg:SI 17)\
(match\_operand:SI 0 "immediate\_operand" "i")))]\
"GET\_CODE (operands\[0]) == CONST\_INT"\
"\*\
{\
if (INTVAL (operands\[0]) == 8)\
return "cmpd tos,tos # adjsp -8";\
if (INTVAL (operands\[0]) == 4)\
return "cmpqd %$0,tos # adjsp -4";\
if (INTVAL (operands\[0]) < 64 && INTVAL (operands\[0]) > -64)\
return "adjspb %$%n0";\
else if (INTVAL (operands\[0]) < 8192 && INTVAL (operands\[0]) >= -8192)\
return "adjspw %$%n0";\
return "adjspd %$%n0";\
}")

(define\_insn ""\
\[(set (match\_operand:SI 0 "general\_operand" "=g<")\
(plus:SI (reg:SI 16)\
(match\_operand:SI 1 "immediate\_operand" "i")))]\
"GET\_CODE (operands\[1]) == CONST\_INT"\
"addr %c1(fp),%0")

(define\_insn ""\
\[(set (match\_operand:SI 0 "general\_operand" "=g<")\
(plus:SI (reg:SI 17)\
(match\_operand:SI 1 "immediate\_operand" "i")))]\
"GET\_CODE (operands\[1]) == CONST\_INT"\
"addr %c1(sp),%0")

(define\_insn "addsi3"\
\[(set (match\_operand:SI 0 "general\_operand" "=g,=g<")\
(plus:SI (match\_operand:SI 1 "general\_operand" "%0,%r")\
(match\_operand:SI 2 "general\_operand" "rmn,n")))]\
""\
"\*\
{\
if (which\_alternative == 1)\
return "addr %c2(%1),%0";\
if (GET\_CODE (operands\[2]) == CONST\_INT)\
{\
int i = INTVAL (operands\[2]);

```
  if (i <= 7 && i >= -8)
return \"addqd %2,%0\";
  else if (GET_CODE (operands[0]) == REG
       && i < 0x4000 && i >= -0x4000)
return \"addr %c2(%0),%0\";
}
```

return "addd %2,%0";\
}")

(define\_insn "addhi3"\
\[(set (match\_operand:HI 0 "general\_operand" "=g")\
(plus:HI (match\_operand:HI 1 "general\_operand" "%0")\
(match\_operand:HI 2 "general\_operand" "g")))]\
""\
"\*\
{ if (GET\_CODE (operands\[2]) == CONST\_INT)\
{\
int i = INTVAL (operands\[2]);\
if (i <= 7 && i >= -8)\
return "addqw %2,%0";\
}\
return "addw %2,%0";\
}")

(define\_insn ""\
\[(set (strict\_low\_part (match\_operand:HI 0 "general\_operand" "=r"))\
(plus:HI (match\_operand:HI 1 "general\_operand" "0")\
(match\_operand:HI 2 "general\_operand" "g")))]\
""\
"\*\
{\
if (GET\_CODE (operands\[1]) == CONST\_INT\
&& INTVAL (operands\[1]) >-9 && INTVAL(operands\[1]) < 8)\
return "addqw %1,%0";\
return "addw %1,%0";\
}")

(define\_insn "addqi3"\
\[(set (match\_operand:QI 0 "general\_operand" "=g")\
(plus:QI (match\_operand:QI 1 "general\_operand" "%0")\
(match\_operand:QI 2 "general\_operand" "g")))]\
""\
"\*\
{ if (GET\_CODE (operands\[2]) == CONST\_INT)\
{\
int i = INTVAL (operands\[2]);\
if (i <= 7 && i >= -8)\
return "addqb %2,%0";\
}\
return "addb %2,%0";\
}")

(define\_insn ""\
\[(set (strict\_low\_part (match\_operand:QI 0 "general\_operand" "=r"))\
(plus:QI (match\_operand:QI 1 "general\_operand" "0")\
(match\_operand:QI 2 "general\_operand" "g")))]\
""\
"\*\
{\
if (GET\_CODE (operands\[1]) == CONST\_INT\
&& INTVAL (operands\[1]) >-9 && INTVAL(operands\[1]) < 8)\
return "addqb %1,%0";\
return "addb %1,%0";\
}")\
;;- All kinds of subtract instructions.

(define\_insn "subdf3"\
\[(set (match\_operand:DF 0 "general\_operand" "=fm")\
(minus:DF (match\_operand:DF 1 "general\_operand" "0")\
(match\_operand:DF 2 "general\_operand" "fmF")))]\
"TARGET\_32081"\
"subl %2,%0")

(define\_insn "subsf3"\
\[(set (match\_operand:SF 0 "general\_operand" "=fm")\
(minus:SF (match\_operand:SF 1 "general\_operand" "0")\
(match\_operand:SF 2 "general\_operand" "fmF")))]\
"TARGET\_32081"\
"subf %2,%0")

(define\_insn ""\
\[(set (reg:SI 17)\
(minus:SI (reg:SI 17)\
(match\_operand:SI 0 "immediate\_operand" "i")))]\
"GET\_CODE (operands\[0]) == CONST\_INT"\
"\*\
{\
if (GET\_CODE(operands\[0]) == CONST\_INT && INTVAL(operands\[0]) < 64\
&& INTVAL(operands\[0]) > -64)\
return "adjspb %0";\
return "adjspd %0";\
}")

(define\_insn "subsi3"\
\[(set (match\_operand:SI 0 "general\_operand" "=g")\
(minus:SI (match\_operand:SI 1 "general\_operand" "0")\
(match\_operand:SI 2 "general\_operand" "rmn")))]\
""\
"\*\
{ if (GET\_CODE (operands\[2]) == CONST\_INT)\
{\
int i = INTVAL (operands\[2]);

```
  if (i <= 8 && i >= -7)
    return \"addqd %$%n2,%0\";
}
```

return "subd %2,%0";\
}")

(define\_insn "subhi3"\
\[(set (match\_operand:HI 0 "general\_operand" "=g")\
(minus:HI (match\_operand:HI 1 "general\_operand" "0")\
(match\_operand:HI 2 "general\_operand" "g")))]\
""\
"\*\
{ if (GET\_CODE (operands\[2]) == CONST\_INT)\
{\
int i = INTVAL (operands\[2]);

```
  if (i <= 8 && i >= -7)
    return \"addqw %$%n2,%0\";
}
```

return "subw %2,%0";\
}")

(define\_insn ""\
\[(set (strict\_low\_part (match\_operand:HI 0 "general\_operand" "=r"))\
(minus:HI (match\_operand:HI 1 "general\_operand" "0")\
(match\_operand:HI 2 "general\_operand" "g")))]\
""\
"\*\
{\
if (GET\_CODE (operands\[1]) == CONST\_INT\
&& INTVAL (operands\[1]) >-8 && INTVAL(operands\[1]) < 9)\
return "addqw %$%n1,%0";\
return "subw %1,%0";\
}")

(define\_insn "subqi3"\
\[(set (match\_operand:QI 0 "general\_operand" "=g")\
(minus:QI (match\_operand:QI 1 "general\_operand" "0")\
(match\_operand:QI 2 "general\_operand" "g")))]\
""\
"\*\
{ if (GET\_CODE (operands\[2]) == CONST\_INT)\
{\
int i = INTVAL (operands\[2]);

```
  if (i <= 8 && i >= -7)
return \"addqb %$%n2,%0\";
}
```

return "subb %2,%0";\
}")

(define\_insn ""\
\[(set (strict\_low\_part (match\_operand:QI 0 "general\_operand" "=r"))\
(minus:QI (match\_operand:QI 1 "general\_operand" "0")\
(match\_operand:QI 2 "general\_operand" "g")))]\
""\
"\*\
{\
if (GET\_CODE (operands\[1]) == CONST\_INT\
&& INTVAL (operands\[1]) >-8 && INTVAL(operands\[1]) < 9)\
return "addqb %$%n1,%0";\
return "subb %1,%0";\
}")\
;;- Multiply instructions.

(define\_insn "muldf3"\
\[(set (match\_operand:DF 0 "general\_operand" "=fm")\
(mult:DF (match\_operand:DF 1 "general\_operand" "%0")\
(match\_operand:DF 2 "general\_operand" "fmF")))]\
"TARGET\_32081"\
"mull %2,%0")

(define\_insn "mulsf3"\
\[(set (match\_operand:SF 0 "general\_operand" "=fm")\
(mult:SF (match\_operand:SF 1 "general\_operand" "%0")\
(match\_operand:SF 2 "general\_operand" "fmF")))]\
"TARGET\_32081"\
"mulf %2,%0")

(define\_insn "mulsi3"\
\[(set (match\_operand:SI 0 "general\_operand" "=g")\
(mult:SI (match\_operand:SI 1 "general\_operand" "%0")\
(match\_operand:SI 2 "general\_operand" "rmn")))]\
""\
"muld %2,%0")

(define\_insn "mulhi3"\
\[(set (match\_operand:HI 0 "general\_operand" "=g")\
(mult:HI (match\_operand:HI 1 "general\_operand" "%0")\
(match\_operand:HI 2 "general\_operand" "g")))]\
""\
"mulw %2,%0")

(define\_insn "mulqi3"\
\[(set (match\_operand:QI 0 "general\_operand" "=g")\
(mult:QI (match\_operand:QI 1 "general\_operand" "%0")\
(match\_operand:QI 2 "general\_operand" "g")))]\
""\
"mulb %2,%0")

(define\_insn "umulsi3"\
\[(set (match\_operand:SI 0 "general\_operand" "=g")\
(umult:SI (match\_operand:SI 1 "general\_operand" "%0")\
(match\_operand:SI 2 "general\_operand" "rmn")))]\
""\
"muld %2,%0")

(define\_insn "umulhi3"\
\[(set (match\_operand:HI 0 "general\_operand" "=g")\
(umult:HI (match\_operand:HI 1 "general\_operand" "%0")\
(match\_operand:HI 2 "general\_operand" "g")))]\
""\
"mulw %2,%0")

(define\_insn "umulqi3"\
\[(set (match\_operand:QI 0 "general\_operand" "=g")\
(umult:QI (match\_operand:QI 1 "general\_operand" "%0")\
(match\_operand:QI 2 "general\_operand" "g")))]\
""\
"mulb %2,%0")

(define\_insn "umulsidi3"\
\[(set (match\_operand:DI 0 "general\_operand" "=g")\
(umult:DI (match\_operand:SI 1 "general\_operand" "0")\
(match\_operand:SI 2 "general\_operand" "rmn")))]\
""\
"meid %2,%0")\
;;- Divide instructions.

(define\_insn "divdf3"\
\[(set (match\_operand:DF 0 "general\_operand" "=fm")\
(div:DF (match\_operand:DF 1 "general\_operand" "0")\
(match\_operand:DF 2 "general\_operand" "fmF")))]\
"TARGET\_32081"\
"divl %2,%0")

(define\_insn "divsf3"\
\[(set (match\_operand:SF 0 "general\_operand" "=fm")\
(div:SF (match\_operand:SF 1 "general\_operand" "0")\
(match\_operand:SF 2 "general\_operand" "fmF")))]\
"TARGET\_32081"\
"divf %2,%0")

(define\_insn "divsi3"\
\[(set (match\_operand:SI 0 "general\_operand" "=g")\
(div:SI (match\_operand:SI 1 "general\_operand" "0")\
(match\_operand:SI 2 "general\_operand" "rmn")))]\
""\
"quod %2,%0")

(define\_insn "divhi3"\
\[(set (match\_operand:HI 0 "general\_operand" "=g")\
(div:HI (match\_operand:HI 1 "general\_operand" "0")\
(match\_operand:HI 2 "general\_operand" "g")))]\
""\
"quow %2,%0")

(define\_insn "divqi3"\
\[(set (match\_operand:QI 0 "general\_operand" "=g")\
(div:QI (match\_operand:QI 1 "general\_operand" "0")\
(match\_operand:QI 2 "general\_operand" "g")))]\
""\
"quob %2,%0")

(define\_insn "udivsi3"\
\[(set (match\_operand:SI 0 "register\_operand" "=r")\
(udiv:SI (subreg:SI (match\_operand:DI 1 "reg\_or\_mem\_operand" "0") 0)\
(match\_operand:SI 2 "general\_operand" "rmn")))]\
""\
"\*\
{\
operands\[1] = gen\_rtx (REG, SImode, REGNO (operands\[0]) + 1);\
return "deid %2,%0;movd %1,%0";\
}")

(define\_insn "udivhi3"\
\[(set (match\_operand:HI 0 "register\_operand" "=r")\
(udiv:HI (subreg:HI (match\_operand:DI 1 "reg\_or\_mem\_operand" "0") 0)\
(match\_operand:HI 2 "general\_operand" "g")))]\
""\
"\*\
{\
operands\[1] = gen\_rtx (REG, HImode, REGNO (operands\[0]) + 1);\
return "deiw %2,%0;movw %1,%0";\
}")

(define\_insn "udivqi3"\
\[(set (match\_operand:QI 0 "register\_operand" "=r")\
(udiv:QI (subreg:QI (match\_operand:DI 1 "reg\_or\_mem\_operand" "0") 0)\
(match\_operand:QI 2 "general\_operand" "g")))]\
""\
"\*\
{\
operands\[1] = gen\_rtx (REG, QImode, REGNO (operands\[0]) + 1);\
return "deib %2,%0;movb %1,%0";\
}")

;; Remainder instructions.

(define\_insn "modsi3"\
\[(set (match\_operand:SI 0 "general\_operand" "=g")\
(mod:SI (match\_operand:SI 1 "general\_operand" "0")\
(match\_operand:SI 2 "general\_operand" "rmn")))]\
""\
"remd %2,%0")

(define\_insn "modhi3"\
\[(set (match\_operand:HI 0 "general\_operand" "=g")\
(mod:HI (match\_operand:HI 1 "general\_operand" "0")\
(match\_operand:HI 2 "general\_operand" "g")))]\
""\
"remw %2,%0")

(define\_insn "modqi3"\
\[(set (match\_operand:QI 0 "general\_operand" "=g")\
(mod:QI (match\_operand:QI 1 "general\_operand" "0")\
(match\_operand:QI 2 "general\_operand" "g")))]\
""\
"remb %2,%0")

(define\_insn "umodsi3"\
\[(set (match\_operand:SI 0 "register\_operand" "=r")\
(umod:SI (subreg:SI (match\_operand:DI 1 "reg\_or\_mem\_operand" "0") 0)\
(match\_operand:SI 2 "general\_operand" "rmn")))]\
""\
"deid %2,%0")

(define\_insn "umodhi3"\
\[(set (match\_operand:HI 0 "register\_operand" "=r")\
(umod:HI (subreg:HI (match\_operand:DI 1 "reg\_or\_mem\_operand" "0") 0)\
(match\_operand:HI 2 "general\_operand" "g")))]\
""\
"deiw %2,%0")

(define\_insn "umodqi3"\
\[(set (match\_operand:QI 0 "register\_operand" "=r")\
(umod:QI (subreg:QI (match\_operand:DI 1 "reg\_or\_mem\_operand" "0") 0)\
(match\_operand:QI 2 "general\_operand" "g")))]\
""\
"deib %2,%0")

; This isn't be usable in its current form.\
;(define\_insn "udivmoddisi4"\
; \[(set (subreg:SI (match\_operand:DI 0 "general\_operand" "=r") 1)\
; (udiv:SI (match\_operand:DI 1 "general\_operand" "0")\
; (match\_operand:SI 2 "general\_operand" "rmn")))\
; (set (subreg:SI (match\_dup 0) 0)\
; (umod:SI (match\_dup 1) (match\_dup 2)))]\
; ""\
; "deid %2,%0")\
;;- Logical Instructions: AND

(define\_insn "andsi3"\
\[(set (match\_operand:SI 0 "general\_operand" "=g")\
(and:SI (match\_operand:SI 1 "general\_operand" "%0")\
(match\_operand:SI 2 "general\_operand" "rmn")))]\
""\
"\*\
{\
if (GET\_CODE (operands\[2]) == CONST\_INT)\
{\
if ((INTVAL (operands\[2]) | 0xff) == 0xffffffff)\
{\
if (INTVAL (operands\[2]) == 0xffffff00)\
return "movqb %$0,%0";\
else\
{\
operands\[2] = gen\_rtx (CONST\_INT, VOIDmode,\
INTVAL (operands\[2]) & 0xff);\
return "andb %2,%0";\
}\
}\
if ((INTVAL (operands\[2]) | 0xffff) == 0xffffffff)\
{\
if (INTVAL (operands\[2]) == 0xffff0000)\
return "movqw %$0,%0";\
else\
{\
operands\[2] = gen\_rtx (CONST\_INT, VOIDmode,\
INTVAL (operands\[2]) & 0xffff);\
return "andw %2,%0";\
}\
}\
}\
return "andd %2,%0";\
}")

(define\_insn "andhi3"\
\[(set (match\_operand:HI 0 "general\_operand" "=g")\
(and:HI (match\_operand:HI 1 "general\_operand" "%0")\
(match\_operand:HI 2 "general\_operand" "g")))]\
""\
"\*\
{\
if (GET\_CODE (operands\[2]) == CONST\_INT\
&& (INTVAL (operands\[2]) | 0xff) == 0xffffffff)\
{\
if (INTVAL (operands\[2]) == 0xffffff00)\
return "movqb %$0,%0";\
else\
{\
operands\[2] = gen\_rtx (CONST\_INT, VOIDmode,\
INTVAL (operands\[2]) & 0xff);\
return "andb %2,%0";\
}\
}\
return "andw %2,%0";\
}")

(define\_insn "andqi3"\
\[(set (match\_operand:QI 0 "general\_operand" "=g")\
(and:QI (match\_operand:QI 1 "general\_operand" "%0")\
(match\_operand:QI 2 "general\_operand" "g")))]\
""\
"andb %2,%0")

(define\_insn "andcbsi3"\
\[(set (match\_operand:SI 0 "general\_operand" "=g")\
(and:SI (match\_operand:SI 1 "general\_operand" "0")\
(not:SI (match\_operand:SI 2 "general\_operand" "rmn"))))]\
""\
"\*\
{\
if (GET\_CODE (operands\[2]) == CONST\_INT)\
{\
if ((INTVAL (operands\[2]) & 0xffffff00) == 0)\
return "bicb %2,%0";\
if ((INTVAL (operands\[2]) & 0xffff0000) == 0)\
return "bicw %2,%0";\
}\
return "bicd %2,%0";\
}")

(define\_insn ""\
\[(set (match\_operand:SI 0 "general\_operand" "=g")\
(and:SI (not:SI (match\_operand:SI 1 "general\_operand" "rmn"))\
(match\_operand:SI 2 "general\_operand" "0")))]\
""\
"\*\
{\
if (GET\_CODE (operands\[1]) == CONST\_INT)\
{\
if ((INTVAL (operands\[1]) & 0xffffff00) == 0)\
return "bicb %1,%0";\
if ((INTVAL (operands\[1]) & 0xffff0000) == 0)\
return "bicw %1,%0";\
}\
return "bicd %1,%0";\
}")

(define\_insn "andcbhi3"\
\[(set (match\_operand:HI 0 "general\_operand" "=g")\
(and:HI (match\_operand:HI 1 "general\_operand" "%0")\
(not:HI (match\_operand:HI 2 "general\_operand" "g"))))]\
""\
"\*\
{\
if (GET\_CODE (operands\[2]) == CONST\_INT\
&& (INTVAL (operands\[2]) & 0xffffff00) == 0)\
return "bicb %2,%0";\
return "bicw %2,%0";\
}")

(define\_insn ""\
\[(set (match\_operand:HI 0 "general\_operand" "=g")\
(and:HI (not:HI (match\_operand:HI 1 "general\_operand" "g"))\
(match\_operand:HI 2 "general\_operand" "0")))]\
""\
"\*\
{\
if (GET\_CODE (operands\[1]) == CONST\_INT\
&& (INTVAL (operands\[1]) & 0xffffff00) == 0)\
return "bicb %1,%0";\
return "bicw %1,%0";\
}")

(define\_insn "andcbqi3"\
\[(set (match\_operand:QI 0 "general\_operand" "=g")\
(and:QI (match\_operand:QI 1 "general\_operand" "%0")\
(not:QI (match\_operand:QI 2 "general\_operand" "g"))))]\
""\
"bicb %2,%0")

(define\_insn ""\
\[(set (match\_operand:QI 0 "general\_operand" "=g")\
(and:QI (not:QI (match\_operand:QI 1 "general\_operand" "g"))\
(match\_operand:QI 2 "general\_operand" "0")))]\
""\
"bicb %1,%0")\
;;- Bit set instructions.

(define\_insn "iorsi3"\
\[(set (match\_operand:SI 0 "general\_operand" "=g")\
(ior:SI (match\_operand:SI 1 "general\_operand" "%0")\
(match\_operand:SI 2 "general\_operand" "rmn")))]\
""\
"\*\
{\
if (GET\_CODE (operands\[2]) == CONST\_INT) {\
if ((INTVAL (operands\[2]) & 0xffffff00) == 0)\
return "orb %2,%0";\
if ((INTVAL (operands\[2]) & 0xffff0000) == 0)\
return "orw %2,%0";\
}\
return "ord %2,%0";\
}")

(define\_insn "iorhi3"\
\[(set (match\_operand:HI 0 "general\_operand" "=g")\
(ior:HI (match\_operand:HI 1 "general\_operand" "%0")\
(match\_operand:HI 2 "general\_operand" "g")))]\
""\
"\*\
{\
if (GET\_CODE(operands\[2]) == CONST\_INT &&\
(INTVAL(operands\[2]) & 0xffffff00) == 0)\
return "orb %2,%0";\
return "orw %2,%0";\
}")

(define\_insn "iorqi3"\
\[(set (match\_operand:QI 0 "general\_operand" "=g")\
(ior:QI (match\_operand:QI 1 "general\_operand" "%0")\
(match\_operand:QI 2 "general\_operand" "g")))]\
""\
"orb %2,%0")

;;- xor instructions.

(define\_insn "xorsi3"\
\[(set (match\_operand:SI 0 "general\_operand" "=g")\
(xor:SI (match\_operand:SI 1 "general\_operand" "%0")\
(match\_operand:SI 2 "general\_operand" "rmn")))]\
""\
"\*\
{\
if (GET\_CODE (operands\[2]) == CONST\_INT) {\
if ((INTVAL (operands\[2]) & 0xffffff00) == 0)\
return "xorb %2,%0";\
if ((INTVAL (operands\[2]) & 0xffff0000) == 0)\
return "xorw %2,%0";\
}\
return "xord %2,%0";\
}")

(define\_insn "xorhi3"\
\[(set (match\_operand:HI 0 "general\_operand" "=g")\
(xor:HI (match\_operand:HI 1 "general\_operand" "%0")\
(match\_operand:HI 2 "general\_operand" "g")))]\
""\
"\*\
{\
if (GET\_CODE(operands\[2]) == CONST\_INT &&\
(INTVAL(operands\[2]) & 0xffffff00) == 0)\
return "xorb %2,%0";\
return "xorw %2,%0";\
}")

(define\_insn "xorqi3"\
\[(set (match\_operand:QI 0 "general\_operand" "=g")\
(xor:QI (match\_operand:QI 1 "general\_operand" "%0")\
(match\_operand:QI 2 "general\_operand" "g")))]\
""\
"xorb %2,%0")\
(define\_insn "negdf2"\
\[(set (match\_operand:DF 0 "general\_operand" "=fm<")\
(neg:DF (match\_operand:DF 1 "general\_operand" "fmF")))]\
"TARGET\_32081"\
"negl %1,%0")

(define\_insn "negsf2"\
\[(set (match\_operand:SF 0 "general\_operand" "=fm<")\
(neg:SF (match\_operand:SF 1 "general\_operand" "fmF")))]\
"TARGET\_32081"\
"negf %1,%0")

(define\_insn "negsi2"\
\[(set (match\_operand:SI 0 "general\_operand" "=g<")\
(neg:SI (match\_operand:SI 1 "general\_operand" "rmn")))]\
""\
"negd %1,%0")

(define\_insn "neghi2"\
\[(set (match\_operand:HI 0 "general\_operand" "=g<")\
(neg:HI (match\_operand:HI 1 "general\_operand" "g")))]\
""\
"negw %1,%0")

(define\_insn "negqi2"\
\[(set (match\_operand:QI 0 "general\_operand" "=g<")\
(neg:QI (match\_operand:QI 1 "general\_operand" "g")))]\
""\
"negb %1,%0")\
(define\_insn "one\_cmplsi2"\
\[(set (match\_operand:SI 0 "general\_operand" "=g<")\
(not:SI (match\_operand:SI 1 "general\_operand" "rmn")))]\
""\
"comd %1,%0")

(define\_insn "one\_cmplhi2"\
\[(set (match\_operand:HI 0 "general\_operand" "=g<")\
(not:HI (match\_operand:HI 1 "general\_operand" "g")))]\
""\
"comw %1,%0")

(define\_insn "one\_cmplqi2"\
\[(set (match\_operand:QI 0 "general\_operand" "=g<")\
(not:QI (match\_operand:QI 1 "general\_operand" "g")))]\
""\
"comb %1,%0")\
;; arithmetic left and right shift operations

(define\_insn "ashlsi3"\
\[(set (match\_operand:SI 0 "general\_operand" "=g,g")\
(ashift:SI (match\_operand:SI 1 "general\_operand" "r,0")\
(match\_operand:SI 2 "general\_operand" "I,rmn")))]\
""\
"\* output\_shift\_insn (operands);")

(define\_insn "ashlhi3"\
\[(set (match\_operand:HI 0 "general\_operand" "=g")\
(ashift:HI (match\_operand:HI 1 "general\_operand" "0")\
(match\_operand:SI 2 "general\_operand" "rmn")))]\
""\
"\*\
{ if (GET\_CODE (operands\[2]) == CONST\_INT)\
if (INTVAL (operands\[2]) == 1)\
return "addw %1,%0";\
else if (INTVAL (operands\[2]) == 2)\
return "addw %1,%0;addw %0,%0";\
return "ashw %2,%0";\
}")

(define\_insn "ashlqi3"\
\[(set (match\_operand:QI 0 "general\_operand" "=g")\
(ashift:QI (match\_operand:QI 1 "general\_operand" "0")\
(match\_operand:SI 2 "general\_operand" "rmn")))]\
""\
"\*\
{ if (GET\_CODE (operands\[2]) == CONST\_INT)\
if (INTVAL (operands\[2]) == 1)\
return "addb %1,%0";\
else if (INTVAL (operands\[2]) == 2)\
return "addb %1,%0;addb %0,%0";\
return "ashb %2,%0";\
}")

;; Arithmetic right shift on the 32k works by negating the shift count.\
(define\_expand "ashrsi3"\
\[(set (match\_operand:SI 0 "general\_operand" "=g")\
(ashift:SI (match\_operand:SI 1 "general\_operand" "g")\
(match\_operand:SI 2 "general\_operand" "g")))]\
""\
"\
{\
operands\[2] = negate\_rtx (SImode, operands\[2]);\
}")

(define\_expand "ashrhi3"\
\[(set (match\_operand:HI 0 "general\_operand" "=g")\
(ashift:HI (match\_operand:HI 1 "general\_operand" "g")\
(match\_operand:SI 2 "general\_operand" "g")))]\
""\
"\
{\
operands\[2] = negate\_rtx (SImode, operands\[2]);\
}")

(define\_expand "ashrqi3"\
\[(set (match\_operand:QI 0 "general\_operand" "=g")\
(ashift:QI (match\_operand:QI 1 "general\_operand" "g")\
(match\_operand:SI 2 "general\_operand" "g")))]\
""\
"\
{\
operands\[2] = negate\_rtx (SImode, operands\[2]);\
}")

;; logical shift instructions

(define\_insn "lshlsi3"\
\[(set (match\_operand:SI 0 "general\_operand" "=g")\
(lshift:SI (match\_operand:SI 1 "general\_operand" "0")\
(match\_operand:SI 2 "general\_operand" "rmn")))]\
""\
"lshd %2,%0")

(define\_insn "lshlhi3"\
\[(set (match\_operand:HI 0 "general\_operand" "=g")\
(lshift:HI (match\_operand:HI 1 "general\_operand" "0")\
(match\_operand:SI 2 "general\_operand" "rmn")))]\
""\
"lshw %2,%0")

(define\_insn "lshlqi3"\
\[(set (match\_operand:QI 0 "general\_operand" "=g")\
(lshift:QI (match\_operand:QI 1 "general\_operand" "0")\
(match\_operand:SI 2 "general\_operand" "rmn")))]\
""\
"lshb %2,%0")

;; Logical right shift on the 32k works by negating the shift count.\
(define\_expand "lshrsi3"\
\[(set (match\_operand:SI 0 "general\_operand" "=g")\
(lshift:SI (match\_operand:SI 1 "general\_operand" "g")\
(match\_operand:SI 2 "general\_operand" "g")))]\
""\
"\
{\
operands\[2] = negate\_rtx (SImode, operands\[2]);\
}")

(define\_expand "lshrhi3"\
\[(set (match\_operand:HI 0 "general\_operand" "=g")\
(lshift:HI (match\_operand:HI 1 "general\_operand" "g")\
(match\_operand:SI 2 "general\_operand" "g")))]\
""\
"\
{\
operands\[2] = negate\_rtx (SImode, operands\[2]);\
}")

(define\_expand "lshrqi3"\
\[(set (match\_operand:QI 0 "general\_operand" "=g")\
(lshift:QI (match\_operand:QI 1 "general\_operand" "g")\
(match\_operand:SI 2 "general\_operand" "g")))]\
""\
"\
{\
operands\[2] = negate\_rtx (SImode, operands\[2]);\
}")

;; Rotate instructions

(define\_insn "rotlsi3"\
\[(set (match\_operand:SI 0 "general\_operand" "=g")\
(rotate:SI (match\_operand:SI 1 "general\_operand" "0")\
(match\_operand:SI 2 "general\_operand" "rmn")))]\
""\
"rotd %2,%0")

(define\_insn "rotlhi3"\
\[(set (match\_operand:HI 0 "general\_operand" "=g")\
(rotate:HI (match\_operand:HI 1 "general\_operand" "0")\
(match\_operand:SI 2 "general\_operand" "rmn")))]\
""\
"rotw %2,%0")

(define\_insn "rotlqi3"\
\[(set (match\_operand:QI 0 "general\_operand" "=g")\
(rotate:QI (match\_operand:QI 1 "general\_operand" "0")\
(match\_operand:SI 2 "general\_operand" "rmn")))]\
""\
"rotb %2,%0")

;; Right rotate on the 32k works by negating the shift count.\
(define\_expand "rotrsi3"\
\[(set (match\_operand:SI 0 "general\_operand" "=g")\
(rotate:SI (match\_operand:SI 1 "general\_operand" "g")\
(match\_operand:SI 2 "general\_operand" "g")))]\
""\
"\
{\
operands\[2] = negate\_rtx (SImode, operands\[2]);\
}")

(define\_expand "rotrhi3"\
\[(set (match\_operand:HI 0 "general\_operand" "=g")\
(rotate:HI (match\_operand:HI 1 "general\_operand" "g")\
(match\_operand:SI 2 "general\_operand" "g")))]\
""\
"\
{\
operands\[2] = negate\_rtx (SImode, operands\[2]);\
}")

(define\_expand "rotrqi3"\
\[(set (match\_operand:QI 0 "general\_operand" "=g")\
(rotate:QI (match\_operand:QI 1 "general\_operand" "g")\
(match\_operand:SI 2 "general\_operand" "g")))]\
""\
"\
{\
operands\[2] = negate\_rtx (SImode, operands\[2]);\
}")\
;;- load or push effective address\
;; These come after the move, add, and multiply patterns\
;; because we don't want pushl $1 turned into pushad 1.

(define\_insn ""\
\[(set (match\_operand:SI 0 "general\_operand" "=g<")\
(match\_operand:QI 1 "address\_operand" "p"))]\
""\
"\*\
{\
if (REG\_P (operands\[0])\
&& GET\_CODE (operands\[1]) == MULT\
&& GET\_CODE (XEXP (operands\[1], 1)) == CONST\_INT\
&& (INTVAL (XEXP (operands\[1], 1)) == 2\
|| INTVAL (XEXP (operands\[1], 1)) == 4))\
{\
rtx xoperands\[3];\
xoperands\[0] = operands\[0];\
xoperands\[1] = XEXP (operands\[1], 0);\
xoperands\[2] = gen\_rtx (CONST\_INT, VOIDmode, INTVAL (XEXP (operands\[1], 1)) >> 1);\
return output\_shift\_insn (xoperands);\
}\
return "addr %a1,%0";\
}")\
;;; Index insns. These are about the same speed as multiply-add counterparts.\
;;; but slower then using power-of-2 shifts if we can use them\
;\
;(define\_insn ""\
; \[(set (match\_operand:SI 0 "register\_operand" "=r")\
; (plus:SI (match\_operand:SI 1 "general\_operand" "rmn")\
; (mult:SI (match\_operand:SI 2 "register\_operand" "0")\
; (plus:SI (match\_operand:SI 3 "general\_operand" "rmn") (const\_int 1)))))]\
; "GET\_CODE (operands\[3]) != CONST\_INT || INTVAL (operands\[3]) > 8"\
; "indexd %0,%3,%1")\
;\
;(define\_insn ""\
; \[(set (match\_operand:SI 0 "register\_operand" "=r")\
; (plus:SI (mult:SI (match\_operand:SI 1 "register\_operand" "0")\
; (plus:SI (match\_operand:SI 2 "general\_operand" "rmn") (const\_int 1)))\
; (match\_operand:SI 3 "general\_operand" "rmn")))]\
; "GET\_CODE (operands\[2]) != CONST\_INT || INTVAL (operands\[2]) > 8"\
; "indexd %0,%2,%3")\
;; Set, Clear, and Invert bit

(define\_insn ""\
\[(set (match\_operand:SI 0 "general\_operand" "=g")\
(ior:SI\
(ashift:SI (const\_int 1)\
(match\_operand:SI 1 "general\_operand" "rmn"))\
(match\_dup 0)))]\
""\
"sbitd %1,%0")

(define\_insn ""\
\[(set (match\_operand:SI 0 "general\_operand" "=g")\
(ior:SI\
(match\_dup 0)\
(ashift:SI (const\_int 1)\
(match\_operand:SI 1 "general\_operand" "rmn"))))]\
""\
"sbitd %1,%0")

(define\_insn ""\
\[(set (match\_operand:QI 0 "general\_operand" "=g")\
(ior:QI\
(subreg:QI\
(ashift:SI (const\_int 1)\
(match\_operand:QI 1 "general\_operand" "rmn")) 0)\
(match\_dup 0)))]\
""\
"sbitb %1,%0")

(define\_insn ""\
\[(set (match\_operand:QI 0 "general\_operand" "=g")\
(ior:QI\
(match\_dup 0)\
(subreg:QI\
(ashift:SI (const\_int 1)\
(match\_operand:QI 1 "general\_operand" "rmn")) 0)))]\
""\
"sbitb %1,%0")

(define\_insn ""\
\[(set (match\_operand:SI 0 "general\_operand" "=g")\
(and:SI\
(not:SI\
(ashift:SI (const\_int 1)\
(match\_operand:SI 1 "general\_operand" "rmn")))\
(match\_dup 0)))]\
""\
"cbitd %1,%0")

(define\_insn ""\
\[(set (match\_operand:SI 0 "general\_operand" "=g")\
(and:SI\
(match\_dup 0)\
(not:SI\
(ashift:SI (const\_int 1)\
(match\_operand:SI 1 "general\_operand" "rmn")))))]\
""\
"cbitd %1,%0")

(define\_insn ""\
\[(set (match\_operand:QI 0 "general\_operand" "=g")\
(and:QI\
(subreg:QI\
(not:SI\
(ashift:SI (const\_int 1)\
(match\_operand:QI 1 "general\_operand" "rmn"))) 0)\
(match\_dup 0)))]\
""\
"cbitb %1,%0")

(define\_insn ""\
\[(set (match\_operand:QI 0 "general\_operand" "=g")\
(and:QI\
(match\_dup 0)\
(subreg:QI\
(not:SI\
(ashift:SI (const\_int 1)\
(match\_operand:QI 1 "general\_operand" "rmn"))) 0)))]\
""\
"cbitb %1,%0")

(define\_insn ""\
\[(set (match\_operand:SI 0 "general\_operand" "=g")\
(xor:SI\
(ashift:SI (const\_int 1)\
(match\_operand:SI 1 "general\_operand" "rmn"))\
(match\_dup 0)))]\
""\
"ibitd %1,%0")

(define\_insn ""\
\[(set (match\_operand:SI 0 "general\_operand" "=g")\
(xor:SI\
(match\_dup 0)\
(ashift:SI (const\_int 1)\
(match\_operand:SI 1 "general\_operand" "rmn"))))]\
""\
"ibitd %1,%0")

(define\_insn ""\
\[(set (match\_operand:QI 0 "general\_operand" "=g")\
(xor:QI\
(subreg:QI\
(ashift:SI (const\_int 1)\
(match\_operand:QI 1 "general\_operand" "rmn")) 0)\
(match\_dup 0)))]\
""\
"ibitb %1,%0")

(define\_insn ""\
\[(set (match\_operand:QI 0 "general\_operand" "=g")\
(xor:QI\
(match\_dup 0)\
(subreg:QI\
(ashift:SI (const\_int 1)\
(match\_operand:QI 1 "general\_operand" "rmn")) 0)))]\
""\
"ibitb %1,%0")

;; Recognize jbs and jbc instructions.

(define\_insn ""\
\[(set (cc0)\
(zero\_extract (match\_operand:SI 0 "general\_operand" "rm")\
(const\_int 1)\
(match\_operand:SI 1 "general\_operand" "rmn")))]\
""\
"\*\
{ cc\_status.flags = CC\_Z\_IN\_F;\
return "tbitd %1,%0";\
}")

(define\_insn ""\
\[(set (cc0)\
(compare (zero\_extract (match\_operand:SI 0 "general\_operand" "rm")\
(const\_int 1)\
(match\_operand:SI 1 "general\_operand" "rmn"))\
(const\_int 1)))]\
""\
"\*\
{ cc\_status.flags = CC\_Z\_IN\_NOT\_F;\
return "tbitd %1,%0";\
}")

(define\_insn ""\
\[(set (cc0)\
(zero\_extract (match\_operand:HI 0 "general\_operand" "g")\
(const\_int 1)\
(match\_operand:HI 1 "general\_operand" "g")))]\
""\
"\*\
{ cc\_status.flags = CC\_Z\_IN\_F;\
return "tbitw %1,%0";\
}")

(define\_insn ""\
\[(set (cc0)\
(compare (zero\_extract (match\_operand:HI 0 "general\_operand" "g")\
(const\_int 1)\
(match\_operand:HI 1 "general\_operand" "rmn"))\
(const\_int 1)))]\
""\
"\*\
{ cc\_status.flags = CC\_Z\_IN\_NOT\_F;\
return "tbitw %1,%0";\
}")

(define\_insn ""\
\[(set (cc0)\
(zero\_extract (match\_operand:QI 0 "general\_operand" "g")\
(const\_int 1)\
(match\_operand:QI 1 "general\_operand" "g")))]\
""\
"\*\
{ cc\_status.flags = CC\_Z\_IN\_F;\
return "tbitb %1,%0";\
}")

(define\_insn ""\
\[(set (cc0)\
(compare (zero\_extract:SI (match\_operand:QI 0 "general\_operand" "g")\
(const\_int 1)\
(match\_operand:QI 1 "general\_operand" "rmn"))\
(const\_int 1)))]\
""\
"\*\
{ cc\_status.flags = CC\_Z\_IN\_NOT\_F;\
return "tbitb %1,%0";\
}")

(define\_insn ""\
\[(set (cc0)\
(and:SI (match\_operand:SI 0 "general\_operand" "rm")\
(match\_operand:SI 1 "immediate\_operand" "i")))]\
"GET\_CODE (operands\[1]) == CONST\_INT\
&& exact\_log2 (INTVAL (operands\[1])) >= 0"\
"\*\
{\
operands\[1]\
\= gen\_rtx (CONST\_INT, VOIDmode, exact\_log2 (INTVAL (operands\[1])));\
cc\_status.flags = CC\_Z\_IN\_F;\
return "tbitd %1,%0";\
}")

;; extract(base, width, offset)\
;; Signed bitfield extraction is not supported in hardware on the\
;; NS 32032. It is therefore better to let GCC figure out a\
;; good strategy for generating the proper instruction sequence\
;; and represent it as rtl.

;; Optimize the case of extracting a byte or word from a register.\
;; Otherwise we must load a register with the offset of the\
;; chunk we want, and perform an extract insn (each of which\
;; is very expensive). Since we use the stack to do our bit-twiddling\
;; we cannot use it for a destination. Perhaps things are fast\
;; enough on the 32532 that such hacks are not needed.

(define\_insn ""\
\[(set (match\_operand:SI 0 "general\_operand" "=ro")\
(zero\_extract:SI (match\_operand:SI 1 "register\_operand" "r")\
(match\_operand:SI 2 "const\_int" "i")\
(match\_operand:SI 3 "const\_int" "i")))]\
"(INTVAL (operands\[2]) == 8 || INTVAL (operands\[2]) == 16)\
&& (INTVAL (operands\[3]) == 8 || INTVAL (operands\[3]) == 16 || INTVAL (operands\[3]) == 24)"\
"\*\
{\
output\_asm\_insn ("movd %1,tos", operands);\
if (INTVAL (operands\[2]) == 16)\
{\
if (INTVAL (operands\[3]) == 8)\
output\_asm\_insn ("movzwd 1(sp),%0", operands);\
else\
output\_asm\_insn ("movzwd 2(sp),%0", operands);\
}\
else\
{\
if (INTVAL (operands\[3]) == 8)\
output\_asm\_insn ("movzbd 1(sp),%0", operands);\
else if (INTVAL (operands\[3]) == 16)\
output\_asm\_insn ("movzbd 2(sp),%0", operands);\
else\
output\_asm\_insn ("movzbd 3(sp),%0", operands);\
}\
return "cmpqd %$0,tos # adjsp -4";\
}")

(define\_insn ""\
\[(set (match\_operand:SI 0 "general\_operand" "=ro")\
(zero\_extract:SI (match\_operand:HI 1 "register\_operand" "r")\
(match\_operand:SI 2 "const\_int" "i")\
(match\_operand:SI 3 "const\_int" "i")))]\
"INTVAL (operands\[2]) == 8 && INTVAL (operands\[3]) == 8"\
"movw %1,tos;movzbd 1(sp),%0;adjspb %$-2")

(define\_insn "extzv"\
\[(set (match\_operand:SI 0 "general\_operand" "=g<,g<")\
(zero\_extract:SI (match\_operand:SI 1 "general\_operand" "rm,o")\
(match\_operand:SI 2 "const\_int" "i,i")\
(match\_operand:SI 3 "general\_operand" "rK,n")))]\
""\
"\*\
{ if (GET\_CODE (operands\[3]) == CONST\_INT)\
{\
if (INTVAL (operands\[3]) >= 8)\
operands\[1] = adj\_offsettable\_operand (operands\[1],\
INTVAL (operands\[3]) >> 3);\
return "extsd %1,%0,%3,%2";\
}\
else return "extd %3,%1,%0,%2";\
}")

(define\_insn ""\
\[(set (match\_operand:SI 0 "general\_operand" "=g<,g<")\
(zero\_extract:SI (match\_operand:HI 1 "general\_operand" "rm,o")\
(match\_operand:SI 2 "const\_int" "i,i")\
(match\_operand:SI 3 "general\_operand" "rK,n")))]\
""\
"\*\
{ if (GET\_CODE (operands\[3]) == CONST\_INT)\
{\
if (INTVAL (operands\[3]) >= 8)\
operands\[1] = adj\_offsettable\_operand (operands\[1],\
INTVAL (operands\[3]) >> 3);\
return "extsd %1,%0,%3,%2";\
}\
else return "extd %3,%1,%0,%2";\
}")

(define\_insn ""\
\[(set (match\_operand:SI 0 "general\_operand" "=g<")\
(zero\_extract:SI (match\_operand:QI 1 "general\_operand" "g")\
(match\_operand:SI 2 "const\_int" "i")\
(match\_operand:SI 3 "general\_operand" "rn")))]\
""\
"\*\
{ if (GET\_CODE (operands\[3]) == CONST\_INT)\
return "extsd %1,%0,%3,%2";\
else return "extd %3,%1,%0,%2";\
}")

(define\_insn "insv"\
\[(set (zero\_extract:SI (match\_operand:SI 0 "general\_operand" "+g,o")\
(match\_operand:SI 1 "const\_int" "i,i")\
(match\_operand:SI 2 "general\_operand" "rK,n"))\
(match\_operand:SI 3 "general\_operand" "rm,rm"))]\
""\
"\*\
{ if (GET\_CODE (operands\[2]) == CONST\_INT)\
{\
if (GET\_CODE (operands\[0]) == MEM && INTVAL (operands\[2]) >= 8)\
{\
operands\[0] = adj\_offsettable\_operand (operands\[0],\
INTVAL (operands\[2]) / 8);\
operands\[2] = gen\_rtx (CONST\_INT, VOIDmode, INTVAL (operands\[2]) % 8);\
}\
if (INTVAL (operands\[1]) <= 8)\
return "inssb %3,%0,%2,%1";\
else if (INTVAL (operands\[1]) <= 16)\
return "inssw %3,%0,%2,%1";\
else\
return "inssd %3,%0,%2,%1";\
}\
return "insd %2,%3,%0,%1";\
}")

(define\_insn ""\
\[(set (zero\_extract:SI (match\_operand:HI 0 "general\_operand" "+g,o")\
(match\_operand:SI 1 "const\_int" "i,i")\
(match\_operand:SI 2 "general\_operand" "rK,n"))\
(match\_operand:SI 3 "general\_operand" "rm,rm"))]\
""\
"\*\
{ if (GET\_CODE (operands\[2]) == CONST\_INT)\
{\
if (GET\_CODE (operands\[0]) == MEM && INTVAL (operands\[2]) >= 8)\
{\
operands\[0] = adj\_offsettable\_operand (operands\[0],\
INTVAL (operands\[2]) / 8);\
operands\[2] = gen\_rtx (CONST\_INT, VOIDmode, INTVAL (operands\[2]) % 8);\
}\
if (INTVAL (operands\[1]) <= 8)\
return "inssb %3,%0,%2,%1";\
else if (INTVAL (operands\[1]) <= 16)\
return "inssw %3,%0,%2,%1";\
else\
return "inssd %3,%0,%2,%1";\
}\
return "insd %2,%3,%0,%1";\
}")

(define\_insn ""\
\[(set (zero\_extract:SI (match\_operand:QI 0 "general\_operand" "=g")\
(match\_operand:SI 1 "const\_int" "i")\
(match\_operand:SI 2 "general\_operand" "rn"))\
(match\_operand:SI 3 "general\_operand" "rm"))]\
""\
"\*\
{ if (GET\_CODE (operands\[2]) == CONST\_INT)\
if (INTVAL (operands\[1]) <= 8)\
return "inssb %3,%0,%2,%1";\
else if (INTVAL (operands\[1]) <= 16)\
return "inssw %3,%0,%2,%1";\
else\
return "inssd %3,%0,%2,%1";\
return "insd %2,%3,%0,%1";\
}")

\
(define\_insn "jump"\
\[(set (pc)\
(label\_ref (match\_operand 0 "" "")))]\
""\
"br %l0")

(define\_insn "beq"\
\[(set (pc)\
(if\_then\_else (eq (cc0)\
(const\_int 0))\
(label\_ref (match\_operand 0 "" ""))\
(pc)))]\
""\
"\*\
{ if (cc\_prev\_status.flags & CC\_Z\_IN\_F)\
return "bfc %l0";\
else if (cc\_prev\_status.flags & CC\_Z\_IN\_NOT\_F)\
return "bfs %l0";\
else return "beq %l0";\
}")

(define\_insn "bne"\
\[(set (pc)\
(if\_then\_else (ne (cc0)\
(const\_int 0))\
(label\_ref (match\_operand 0 "" ""))\
(pc)))]\
""\
"\*\
{ if (cc\_prev\_status.flags & CC\_Z\_IN\_F)\
return "bfs %l0";\
else if (cc\_prev\_status.flags & CC\_Z\_IN\_NOT\_F)\
return "bfc %l0";\
else return "bne %l0";\
}")

(define\_insn "bgt"\
\[(set (pc)\
(if\_then\_else (gt (cc0)\
(const\_int 0))\
(label\_ref (match\_operand 0 "" ""))\
(pc)))]\
""\
"bgt %l0")

(define\_insn "bgtu"\
\[(set (pc)\
(if\_then\_else (gtu (cc0)\
(const\_int 0))\
(label\_ref (match\_operand 0 "" ""))\
(pc)))]\
""\
"bhi %l0")

(define\_insn "blt"\
\[(set (pc)\
(if\_then\_else (lt (cc0)\
(const\_int 0))\
(label\_ref (match\_operand 0 "" ""))\
(pc)))]\
""\
"blt %l0")

(define\_insn "bltu"\
\[(set (pc)\
(if\_then\_else (ltu (cc0)\
(const\_int 0))\
(label\_ref (match\_operand 0 "" ""))\
(pc)))]\
""\
"blo %l0")

(define\_insn "bge"\
\[(set (pc)\
(if\_then\_else (ge (cc0)\
(const\_int 0))\
(label\_ref (match\_operand 0 "" ""))\
(pc)))]\
""\
"bge %l0")

(define\_insn "bgeu"\
\[(set (pc)\
(if\_then\_else (geu (cc0)\
(const\_int 0))\
(label\_ref (match\_operand 0 "" ""))\
(pc)))]\
""\
"bhs %l0")

(define\_insn "ble"\
\[(set (pc)\
(if\_then\_else (le (cc0)\
(const\_int 0))\
(label\_ref (match\_operand 0 "" ""))\
(pc)))]\
""\
"ble %l0")

(define\_insn "bleu"\
\[(set (pc)\
(if\_then\_else (leu (cc0)\
(const\_int 0))\
(label\_ref (match\_operand 0 "" ""))\
(pc)))]\
""\
"bls %l0")\
(define\_insn ""\
\[(set (pc)\
(if\_then\_else (eq (cc0)\
(const\_int 0))\
(pc)\
(label\_ref (match\_operand 0 "" ""))))]\
""\
"\*\
{ if (cc\_prev\_status.flags & CC\_Z\_IN\_F)\
return "bfs %l0";\
else if (cc\_prev\_status.flags & CC\_Z\_IN\_NOT\_F)\
return "bfc %l0";\
else return "bne %l0";\
}")

(define\_insn ""\
\[(set (pc)\
(if\_then\_else (ne (cc0)\
(const\_int 0))\
(pc)\
(label\_ref (match\_operand 0 "" ""))))]\
""\
"\*\
{ if (cc\_prev\_status.flags & CC\_Z\_IN\_F)\
return "bfc %l0";\
else if (cc\_prev\_status.flags & CC\_Z\_IN\_NOT\_F)\
return "bfs %l0";\
else return "beq %l0";\
}")

(define\_insn ""\
\[(set (pc)\
(if\_then\_else (gt (cc0)\
(const\_int 0))\
(pc)\
(label\_ref (match\_operand 0 "" ""))))]\
""\
"ble %l0")

(define\_insn ""\
\[(set (pc)\
(if\_then\_else (gtu (cc0)\
(const\_int 0))\
(pc)\
(label\_ref (match\_operand 0 "" ""))))]\
""\
"bls %l0")

(define\_insn ""\
\[(set (pc)\
(if\_then\_else (lt (cc0)\
(const\_int 0))\
(pc)\
(label\_ref (match\_operand 0 "" ""))))]\
""\
"bge %l0")

(define\_insn ""\
\[(set (pc)\
(if\_then\_else (ltu (cc0)\
(const\_int 0))\
(pc)\
(label\_ref (match\_operand 0 "" ""))))]\
""\
"bhs %l0")

(define\_insn ""\
\[(set (pc)\
(if\_then\_else (ge (cc0)\
(const\_int 0))\
(pc)\
(label\_ref (match\_operand 0 "" ""))))]\
""\
"blt %l0")

(define\_insn ""\
\[(set (pc)\
(if\_then\_else (geu (cc0)\
(const\_int 0))\
(pc)\
(label\_ref (match\_operand 0 "" ""))))]\
""\
"blo %l0")

(define\_insn ""\
\[(set (pc)\
(if\_then\_else (le (cc0)\
(const\_int 0))\
(pc)\
(label\_ref (match\_operand 0 "" ""))))]\
""\
"bgt %l0")

(define\_insn ""\
\[(set (pc)\
(if\_then\_else (leu (cc0)\
(const\_int 0))\
(pc)\
(label\_ref (match\_operand 0 "" ""))))]\
""\
"bhi %l0")\
;; Subtract-and-jump and Add-and-jump insns.\
;; These can actually be used for adding numbers in the range -8 to 7

(define\_insn ""\
\[(set (pc)\
(if\_then\_else\
(ne (minus:SI (match\_operand:SI 0 "general\_operand" "+g")\
(match\_operand:SI 1 "general\_operand" "i"))\
(const\_int 0))\
(label\_ref (match\_operand 2 "" ""))\
(pc)))\
(set (match\_dup 0)\
(minus:SI (match\_dup 0)\
(match\_dup 1)))]\
"GET\_CODE (operands\[1]) == CONST\_INT\
&& INTVAL (operands\[1]) > -8 && INTVAL (operands\[1]) <= 8"\
"acbd %$%n1,%0,%l2")

(define\_insn ""\
\[(set (pc)\
(if\_then\_else\
(ne (plus:SI (match\_operand:SI 0 "general\_operand" "+g")\
(match\_operand:SI 1 "general\_operand" "i"))\
(const\_int 0))\
(label\_ref (match\_operand 2 "" ""))\
(pc)))\
(set (match\_dup 0)\
(plus:SI (match\_dup 0)\
(match\_dup 1)))]\
"GET\_CODE (operands\[1]) == CONST\_INT\
&& INTVAL (operands\[1]) >= -8 && INTVAL (operands\[1]) < 8"\
"acbd %1,%0,%l2")

;; Reversed

(define\_insn ""\
\[(set (pc)\
(if\_then\_else\
(eq (minus:SI (match\_operand:SI 0 "general\_operand" "+g")\
(match\_operand:SI 1 "general\_operand" "i"))\
(const\_int 0))\
(pc)\
(label\_ref (match\_operand 2 "" ""))))\
(set (match\_dup 0)\
(minus:SI (match\_dup 0)\
(match\_dup 1)))]\
"GET\_CODE (operands\[1]) == CONST\_INT\
&& INTVAL (operands\[1]) > -8 && INTVAL (operands\[1]) <= 8"\
"acbd %$%n1,%0,%l2")

(define\_insn ""\
\[(set (pc)\
(if\_then\_else\
(eq (plus:SI (match\_operand:SI 0 "general\_operand" "+g")\
(match\_operand:SI 1 "general\_operand" "i"))\
(const\_int 0))\
(pc)\
(label\_ref (match\_operand 2 "" ""))))\
(set (match\_dup 0)\
(plus:SI (match\_dup 0)\
(match\_dup 1)))]\
"GET\_CODE (operands\[1]) == CONST\_INT\
&& INTVAL (operands\[1]) >= -8 && INTVAL (operands\[1]) < 8"\
"acbd %1,%0,%l2")\
(define\_insn "call"\
\[(call (match\_operand:QI 0 "general\_operand" "g")\
(match\_operand:QI 1 "general\_operand" "g"))]\
""\
"\*\
{\
if (GET\_CODE (operands\[0]) == MEM)\
{\
if (CONSTANT\_ADDRESS\_P (XEXP (operands\[0], 0)))\
\#ifdef GNX\_V3\
return "bsr %0";\
\#else\
return "bsr %?%a0";\
\#endif\
if (GET\_CODE (XEXP (operands\[0], 0)) == REG)\
\#ifdef GNX\_V3\
return "jsr %0";\
\#else\
return "jsr %a0";\
\#endif\
}\
return "jsr %0";\
}")

(define\_insn "call\_value"\
\[(set (match\_operand 0 "" "=fg")\
(call (match\_operand:QI 1 "general\_operand" "g")\
(match\_operand:QI 2 "general\_operand" "g")))]\
""\
"\*\
{\
if (GET\_CODE (operands\[1]) == MEM)\
{\
if (CONSTANT\_ADDRESS\_P (XEXP (operands\[1], 0)))\
\#ifdef GNX\_V3\
return "bsr %1";\
\#else\
return "bsr %?%a1";\
\#endif\
if (GET\_CODE (XEXP (operands\[1], 0)) == REG)\
\#ifdef GNX\_V3\
return "jsr %1";\
\#else\
return "jsr %a1";\
\#endif\
}\
return "jsr %1";\
}")

(define\_insn "return"\
\[(return)]\
"0"\
"ret 0")

(define\_insn "abssf2"\
\[(set (match\_operand:SF 0 "general\_operand" "=fm<")\
(abs:SF (match\_operand:SF 1 "general\_operand" "fmF")))]\
"TARGET\_32081"\
"absf %1,%0")

(define\_insn "absdf2"\
\[(set (match\_operand:DF 0 "general\_operand" "=fm<")\
(abs:DF (match\_operand:DF 1 "general\_operand" "fmF")))]\
"TARGET\_32081"\
"absl %1,%0")

(define\_insn "abssi2"\
\[(set (match\_operand:SI 0 "general\_operand" "=g<")\
(abs:SI (match\_operand:SI 1 "general\_operand" "rmn")))]\
""\
"absd %1,%0")

(define\_insn "abshi2"\
\[(set (match\_operand:HI 0 "general\_operand" "=g<")\
(abs:HI (match\_operand:HI 1 "general\_operand" "g")))]\
""\
"absw %1,%0")

(define\_insn "absqi2"\
\[(set (match\_operand:QI 0 "general\_operand" "=g<")\
(abs:QI (match\_operand:QI 1 "general\_operand" "g")))]\
""\
"absb %1,%0")

(define\_insn "nop"\
\[(const\_int 0)]\
""\
"nop")

;;(define\_insn "tablejump"\
;; \[(set (pc)\
;; (plus:SI (match\_operand:SI 0 "general\_operand" "g")\
;; (pc)))]\
;; ""\
;; "cased %0")

(define\_insn "tablejump"\
\[(set (pc)\
(plus:SI (pc) (match\_operand:HI 0 "general\_operand" "g")))\
(use (label\_ref (match\_operand 1 "" "")))]\
""\
"\*\
{\
ASM\_OUTPUT\_INTERNAL\_LABEL (asm\_out\_file, "LI",\
CODE\_LABEL\_NUMBER (operands\[1]));\
return "casew %0";\
}")

;;(define\_insn ""\
;; \[(set (pc)\
;; (plus:SI (match\_operand:QI 0 "general\_operand" "g")\
;; (pc)))]\
;; ""\
;; "caseb %0")\
;; Scondi instructions\
(define\_insn "seq"\
\[(set (match\_operand:SI 0 "general\_operand" "=g<")\
(eq (cc0) (const\_int 0)))]\
""\
"\*\
{ if (cc\_prev\_status.flags & CC\_Z\_IN\_F)\
return "sfcd %0";\
else if (cc\_prev\_status.flags & CC\_Z\_IN\_NOT\_F)\
return "sfsd %0";\
else return "seqd %0";\
}")

(define\_insn ""\
\[(set (match\_operand:HI 0 "general\_operand" "=g<")\
(eq (cc0) (const\_int 0)))]\
""\
"\*\
{ if (cc\_prev\_status.flags & CC\_Z\_IN\_F)\
return "sfcw %0";\
else if (cc\_prev\_status.flags & CC\_Z\_IN\_NOT\_F)\
return "sfsw %0";\
else return "seqw %0";\
}")

(define\_insn ""\
\[(set (match\_operand:QI 0 "general\_operand" "=g<")\
(eq (cc0) (const\_int 0)))]\
""\
"\*\
{ if (cc\_prev\_status.flags & CC\_Z\_IN\_F)\
return "sfcb %0";\
else if (cc\_prev\_status.flags & CC\_Z\_IN\_NOT\_F)\
return "sfsb %0";\
else return "seqb %0";\
}")

(define\_insn "sne"\
\[(set (match\_operand:SI 0 "general\_operand" "=g<")\
(ne (cc0) (const\_int 0)))]\
""\
"\*\
{ if (cc\_prev\_status.flags & CC\_Z\_IN\_F)\
return "sfsd %0";\
else if (cc\_prev\_status.flags & CC\_Z\_IN\_NOT\_F)\
return "sfcd %0";\
else return "sned %0";\
}")

(define\_insn ""\
\[(set (match\_operand:HI 0 "general\_operand" "=g<")\
(ne (cc0) (const\_int 0)))]\
""\
"\*\
{ if (cc\_prev\_status.flags & CC\_Z\_IN\_F)\
return "sfsw %0";\
else if (cc\_prev\_status.flags & CC\_Z\_IN\_NOT\_F)\
return "sfcw %0";\
else return "snew %0";\
}")

(define\_insn ""\
\[(set (match\_operand:QI 0 "general\_operand" "=g<")\
(ne (cc0) (const\_int 0)))]\
""\
"\*\
{ if (cc\_prev\_status.flags & CC\_Z\_IN\_F)\
return "sfsb %0";\
else if (cc\_prev\_status.flags & CC\_Z\_IN\_NOT\_F)\
return "sfcb %0";\
else return "sneb %0";\
}")

(define\_insn "sgt"\
\[(set (match\_operand:SI 0 "general\_operand" "=g<")\
(gt (cc0) (const\_int 0)))]\
""\
"sgtd %0")

(define\_insn ""\
\[(set (match\_operand:HI 0 "general\_operand" "=g<")\
(gt (cc0) (const\_int 0)))]\
""\
"sgtw %0")

(define\_insn ""\
\[(set (match\_operand:QI 0 "general\_operand" "=g<")\
(gt (cc0) (const\_int 0)))]\
""\
"sgtb %0")

(define\_insn "sgtu"\
\[(set (match\_operand:SI 0 "general\_operand" "=g<")\
(gtu (cc0) (const\_int 0)))]\
""\
"shid %0")

(define\_insn ""\
\[(set (match\_operand:HI 0 "general\_operand" "=g<")\
(gtu (cc0) (const\_int 0)))]\
""\
"shiw %0")

(define\_insn ""\
\[(set (match\_operand:QI 0 "general\_operand" "=g<")\
(gtu (cc0) (const\_int 0)))]\
""\
"shib %0")

(define\_insn "slt"\
\[(set (match\_operand:SI 0 "general\_operand" "=g<")\
(lt (cc0) (const\_int 0)))]\
""\
"sltd %0")

(define\_insn ""\
\[(set (match\_operand:HI 0 "general\_operand" "=g<")\
(lt (cc0) (const\_int 0)))]\
""\
"sltw %0")

(define\_insn ""\
\[(set (match\_operand:QI 0 "general\_operand" "=g<")\
(lt (cc0) (const\_int 0)))]\
""\
"sltb %0")

(define\_insn "sltu"\
\[(set (match\_operand:SI 0 "general\_operand" "=g<")\
(ltu (cc0) (const\_int 0)))]\
""\
"slod %0")

(define\_insn ""\
\[(set (match\_operand:HI 0 "general\_operand" "=g<")\
(ltu (cc0) (const\_int 0)))]\
""\
"slow %0")

(define\_insn ""\
\[(set (match\_operand:QI 0 "general\_operand" "=g<")\
(ltu (cc0) (const\_int 0)))]\
""\
"slob %0")

(define\_insn "sge"\
\[(set (match\_operand:SI 0 "general\_operand" "=g<")\
(ge (cc0) (const\_int 0)))]\
""\
"sged %0")

(define\_insn ""\
\[(set (match\_operand:HI 0 "general\_operand" "=g<")\
(ge (cc0) (const\_int 0)))]\
""\
"sgew %0")

(define\_insn ""\
\[(set (match\_operand:QI 0 "general\_operand" "=g<")\
(ge (cc0) (const\_int 0)))]\
""\
"sgeb %0")

(define\_insn "sgeu"\
\[(set (match\_operand:SI 0 "general\_operand" "=g<")\
(geu (cc0) (const\_int 0)))]\
""\
"shsd %0")

(define\_insn ""\
\[(set (match\_operand:HI 0 "general\_operand" "=g<")\
(geu (cc0) (const\_int 0)))]\
""\
"shsw %0")

(define\_insn ""\
\[(set (match\_operand:QI 0 "general\_operand" "=g<")\
(geu (cc0) (const\_int 0)))]\
""\
"shsb %0")

(define\_insn "sle"\
\[(set (match\_operand:SI 0 "general\_operand" "=g<")\
(le (cc0) (const\_int 0)))]\
""\
"sled %0")

(define\_insn ""\
\[(set (match\_operand:HI 0 "general\_operand" "=g<")\
(le (cc0) (const\_int 0)))]\
""\
"slew %0")

(define\_insn ""\
\[(set (match\_operand:QI 0 "general\_operand" "=g<")\
(le (cc0) (const\_int 0)))]\
""\
"sleb %0")

(define\_insn "sleu"\
\[(set (match\_operand:SI 0 "general\_operand" "=g<")\
(leu (cc0) (const\_int 0)))]\
""\
"slsd %0")

(define\_insn ""\
\[(set (match\_operand:HI 0 "general\_operand" "=g<")\
(leu (cc0) (const\_int 0)))]\
""\
"slsw %0")

(define\_insn ""\
\[(set (match\_operand:QI 0 "general\_operand" "=g<")\
(leu (cc0) (const\_int 0)))]\
""\
"slsb %0")

;;- Local variables:\
;;- mode:emacs-lisp\
;;- comment-start: ";;- "\
;;- eval: (set-syntax-table (copy-sequence (syntax-table)))\
;;- eval: (modify-syntax-entry ?\[ "(]")\
;;- eval: (modify-syntax-entry ?] ")\[")\
;;- eval: (modify-syntax-entry ?{ "(}")\
;;- eval: (modify-syntax-entry ?} "){")\
;;- End:
