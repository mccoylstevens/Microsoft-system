# alliant

;;- Machine description for GNU C compiler for Alliant FX systems\
;; Copyright (C) 1989 Free Software Foundation, Inc.\
;; Adapted from m68k.md by Paul Petersen (petersen@uicsrd.csrd.uiuc.edu)\
;; and Joe Weening (weening@gang-of-four.stanford.edu).

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

;;- instruction definitions

;;- @@The original PO technology requires these to be ordered by speed,\
;;- @@ so that assigner will pick the fastest.

;;- See file "rtl.def" for documentation on define\_insn, match\_\*, et. al.

;;- When naming insn's (operand 0 of define\_insn) be careful about using\
;;- names from other targets machine descriptions.

;;- cpp macro #define NOTICE\_UPDATE\_CC in file tm.h handles condition code\
;;- updates for most instructions.

;;- Operand classes for the register allocator:\
;;- 'a' one of the address registers can be used.\
;;- 'd' one of the data registers can be used.\
;;- 'f' one of the CE floating point registers can be used\
;;- 'r' either a data or an address register can be used.

;;- Immediate integer operand constraints:\
;;- 'I' 1 .. 8\
;;- 'J' -32768 .. 32767\
;;- 'K' -128 .. 127\
;;- 'L' -8 .. -1

;;- Some remnants of constraint codes for the m68k ('x','y','G','H')\
;;- may remain in the insn definitions.

;;- Some of these insn's are composites of several Alliant op codes.\
;;- The assembler (or final @@??) insures that the appropriate one is\
;;- selected.\
;; Put tstsi first among test insns so it matches a CONST\_INT operand.

(define\_insn "tstsi"\
\[(set (cc0)\
(match\_operand:SI 0 "general\_operand" "rm"))]\
""\
"\*\
{\
if (TARGET\_68020 || ! ADDRESS\_REG\_P (operands\[0]))\
return "tst%.l %0";\
/\* If you think that the 68020 does not support tstl a0,\
reread page B-167 of the 68020 manual more carefully. _/_\
_/_ On an address reg, cmpw may replace cmpl. \*/\
return "cmp%.w %#0,%0";\
}")

(define\_insn "tsthi"\
\[(set (cc0)\
(match\_operand:HI 0 "general\_operand" "rm"))]\
""\
"\*\
{\
if (TARGET\_68020 || ! ADDRESS\_REG\_P (operands\[0]))\
return "tst%.w %0";\
return "cmp%.w %#0,%0";\
}")

(define\_insn "tstqi"\
\[(set (cc0)\
(match\_operand:QI 0 "general\_operand" "dm"))]\
""\
"tst%.b %0")

(define\_insn "tstsf"\
\[(set (cc0)\
(match\_operand:SF 0 "nonimmediate\_operand" "fm"))]\
"TARGET\_CE"\
"\*\
{\
cc\_status.flags = CC\_IN\_FP;\
return "ftest%.s %0";\
}")

(define\_insn "tstdf"\
\[(set (cc0)\
(match\_operand:DF 0 "nonimmediate\_operand" "fm"))]\
"TARGET\_CE"\
"\*\
{\
cc\_status.flags = CC\_IN\_FP;\
return "ftest%.d %0";\
}")\
;; compare instructions.

;; Put cmpsi first among compare insns so it matches two CONST\_INT operands.

;; A composite of the cmp, cmpa, & cmpi m68000 op codes.\
(define\_insn "cmpsi"\
\[(set (cc0)\
(compare (match\_operand:SI 0 "general\_operand" "rKs,mr,>")\
(match\_operand:SI 1 "general\_operand" "mr,Ksr,>")))]\
""\
"\*\
{\
if (GET\_CODE (operands\[0]) == MEM && GET\_CODE (operands\[1]) == MEM)\
return "cmpm%.l %1,%0";\
if (REG\_P (operands\[1])\
|| (!REG\_P (operands\[0]) && GET\_CODE (operands\[0]) != MEM))\
{\
cc\_status.flags |= CC\_REVERSED;\
return "cmp%.l %d0,%d1";\
}\
return "cmp%.l %d1,%d0";\
}")

(define\_insn "cmphi"\
\[(set (cc0)\
(compare (match\_operand:HI 0 "general\_operand" "rnm,d,n,m")\
(match\_operand:HI 1 "general\_operand" "d,rnm,m,n")))]\
""\
"\*\
{\
if (GET\_CODE (operands\[0]) == MEM && GET\_CODE (operands\[1]) == MEM)\
return "cmpm%.w %1,%0";\
if ((REG\_P (operands\[1]) && !ADDRESS\_REG\_P (operands\[1]))\
|| (!REG\_P (operands\[0]) && GET\_CODE (operands\[0]) != MEM))\
{ cc\_status.flags |= CC\_REVERSED;\
return "cmp%.w %d0,%d1";\
}\
return "cmp%.w %d1,%d0";\
}")

(define\_insn "cmpqi"\
\[(set (cc0)\
(compare (match\_operand:QI 0 "general\_operand" "dn,md,>")\
(match\_operand:QI 1 "general\_operand" "dm,nd,>")))]\
""\
"\*\
{\
if (GET\_CODE (operands\[0]) == MEM && GET\_CODE (operands\[1]) == MEM)\
return "cmpm%.b %1,%0";\
if (REG\_P (operands\[1])\
|| (!REG\_P (operands\[0]) && GET\_CODE (operands\[0]) != MEM))\
{\
cc\_status.flags |= CC\_REVERSED;\
return "cmp%.b %d0,%d1";\
}\
return "cmp%.b %d1,%d0";\
}")

(define\_insn "cmpdf"\
\[(set (cc0)\
(compare (match\_operand:DF 0 "nonimmediate\_operand" "f,m")\
(match\_operand:DF 1 "nonimmediate\_operand" "fm,f")))]\
"TARGET\_CE"\
"\*\
{\
cc\_status.flags = CC\_IN\_FP;\
if (FP\_REG\_P (operands\[0]))\
return "fcmp%.d %1,%0";\
cc\_status.flags |= CC\_REVERSED;\
return "fcmp%.d %0,%1";\
}")

(define\_insn "cmpsf"\
\[(set (cc0)\
(compare (match\_operand:SF 0 "nonimmediate\_operand" "f,m")\
(match\_operand:SF 1 "nonimmediate\_operand" "fm,f")))]\
"TARGET\_CE"\
"\*\
{\
cc\_status.flags = CC\_IN\_FP;\
if (FP\_REG\_P (operands\[0]))\
return "fcmp%.s %1,%0";\
cc\_status.flags |= CC\_REVERSED;\
return "fcmp%.s %0,%1";\
}")\
;; Recognizers for btst instructions.

(define\_insn ""\
\[(set (cc0) (zero\_extract (match\_operand:QI 0 "nonimmediate\_operand" "do")\
(const\_int 1)\
(minus:SI (const\_int 7)\
(match\_operand:SI 1 "general\_operand" "di"))))]\
""\
"\* { return output\_btst (operands, operands\[1], operands\[0], insn, 7); }")

(define\_insn ""\
\[(set (cc0) (zero\_extract (match\_operand:SI 0 "nonimmediate\_operand" "d")\
(const\_int 1)\
(minus:SI (const\_int 31)\
(match\_operand:SI 1 "general\_operand" "di"))))]\
""\
"\* { return output\_btst (operands, operands\[1], operands\[0], insn, 31); }")

;; The following two patterns are like the previous two\
;; except that they use the fact that bit-number operands\
;; are automatically masked to 3 or 5 bits.

(define\_insn ""\
\[(set (cc0) (zero\_extract (match\_operand:QI 0 "nonimmediate\_operand" "do")\
(const\_int 1)\
(minus:SI (const\_int 7)\
(and:SI\
(match\_operand:SI 1 "general\_operand" "d")\
(const\_int 7)))))]\
""\
"\* { return output\_btst (operands, operands\[1], operands\[0], insn, 7); }")

(define\_insn ""\
\[(set (cc0) (zero\_extract (match\_operand:SI 0 "nonimmediate\_operand" "d")\
(const\_int 1)\
(minus:SI (const\_int 31)\
(and:SI\
(match\_operand:SI 1 "general\_operand" "d")\
(const\_int 31)))))]\
""\
"\* { return output\_btst (operands, operands\[1], operands\[0], insn, 31); }")

;; Nonoffsettable mem refs are ok in this one pattern\
;; since we don't try to adjust them.\
(define\_insn ""\
\[(set (cc0) (zero\_extract (match\_operand:QI 0 "nonimmediate\_operand" "md")\
(const\_int 1)\
(match\_operand:SI 1 "general\_operand" "i")))]\
"GET\_CODE (operands\[1]) == CONST\_INT\
&& (unsigned) INTVAL (operands\[1]) < 8"\
"\*\
{\
operands\[1] = gen\_rtx (CONST\_INT, VOIDmode, 7 - INTVAL (operands\[1]));\
return output\_btst (operands, operands\[1], operands\[0], insn, 7);\
}")

(define\_insn ""\
;; The constraint "o,d" here means that a nonoffsettable memref\
;; will match the first alternative, and its address will be reloaded.\
;; Copying the memory contents into a reg would be incorrect if the\
;; bit position is over 7.\
\[(set (cc0) (zero\_extract (match\_operand:HI 0 "nonimmediate\_operand" "o,d")\
(const\_int 1)\
(match\_operand:SI 1 "general\_operand" "i,i")))]\
"GET\_CODE (operands\[1]) == CONST\_INT"\
"\*\
{\
if (GET\_CODE (operands\[0]) == MEM)\
{\
operands\[0] = adj\_offsettable\_operand (operands\[0],\
INTVAL (operands\[1]) / 8);\
operands\[1] = gen\_rtx (CONST\_INT, VOIDmode,\
7 - INTVAL (operands\[1]) % 8);\
return output\_btst (operands, operands\[1], operands\[0], insn, 7);\
}\
operands\[1] = gen\_rtx (CONST\_INT, VOIDmode,\
15 - INTVAL (operands\[1]));\
return output\_btst (operands, operands\[1], operands\[0], insn, 15);\
}")

(define\_insn ""\
\[(set (cc0) (zero\_extract (match\_operand:SI 0 "nonimmediate\_operand" "do")\
(const\_int 1)\
(match\_operand:SI 1 "general\_operand" "i")))]\
"GET\_CODE (operands\[1]) == CONST\_INT"\
"\*\
{\
if (GET\_CODE (operands\[0]) == MEM)\
{\
operands\[0] = adj\_offsettable\_operand (operands\[0],\
INTVAL (operands\[1]) / 8);\
operands\[1] = gen\_rtx (CONST\_INT, VOIDmode,\
7 - INTVAL (operands\[1]) % 8);\
return output\_btst (operands, operands\[1], operands\[0], insn, 7);\
}\
operands\[1] = gen\_rtx (CONST\_INT, VOIDmode,\
31 - INTVAL (operands\[1]));\
return output\_btst (operands, operands\[1], operands\[0], insn, 31);\
}")

(define\_insn ""\
\[(set (cc0) (subreg:SI (lshiftrt:QI (match\_operand:QI 0 "nonimmediate\_operand" "dm")\
(const\_int 7))\
0\))]\
""\
"\*\
{\
cc\_status.flags = CC\_Z\_IN\_NOT\_N | CC\_NOT\_NEGATIVE;\
return "tst%.b %0";\
}")

(define\_insn ""\
\[(set (cc0) (and:SI (sign\_extend:SI (sign\_extend:HI (match\_operand:QI 0 "nonimmediate\_operand" "dm")))\
(match\_operand:SI 1 "general\_operand" "i")))]\
"(GET\_CODE (operands\[1]) == CONST\_INT\
&& (unsigned) INTVAL (operands\[1]) < 0x100\
&& exact\_log2 (INTVAL (operands\[1])) >= 0)"\
"\*\
{ register int log = exact\_log2 (INTVAL (operands\[1]));\
operands\[1] = gen\_rtx (CONST\_INT, VOIDmode, log);\
return output\_btst (operands, operands\[1], operands\[0], insn, 7);\
}")\
;; move instructions

;; A special case in which it is not desirable\
;; to reload the constant into a data register.\
(define\_insn ""\
\[(set (match\_operand:SI 0 "push\_operand" "=m")\
(match\_operand:SI 1 "general\_operand" "J"))]\
"GET\_CODE (operands\[1]) == CONST\_INT\
&& INTVAL (operands\[1]) >= -0x8000\
&& INTVAL (operands\[1]) < 0x8000"\
"\*\
{\
if (operands\[1] == const0\_rtx)\
return "clr%.l %0";\
return "pea %a1";\
}")

;This is never used.\
;(define\_insn "swapsi"\
; \[(set (match\_operand:SI 0 "general\_operand" "r")\
; (match\_operand:SI 1 "general\_operand" "r"))\
; (set (match\_dup 1) (match\_dup 0))]\
; ""\
; "exg %1,%0")

;; Special case of fullword move when source is zero.\
;; The reason this is special is to avoid loading a zero\
;; into a data reg with moveq in order to store it elsewhere.

(define\_insn ""\
\[(set (match\_operand:SI 0 "general\_operand" "=g")\
(const\_int 0))]\
""\
"\*\
{\
if (ADDRESS\_REG\_P (operands\[0]))\
return "sub%.l %0,%0";\
return "clr%.l %0";\
}")

;; General case of fullword move. The register constraints\
;; force integer constants in range for a moveq to be reloaded\
;; if they are headed for memory.\
(define\_insn "movsi"\
;; Notes: make sure no alternative allows g vs g.\
;; We don't allow f-regs since fixed point cannot go in them.\
;; We do allow y and x regs since fixed point is allowed in them.\
\[(set (match\_operand:SI 0 "general\_operand" "=g,da,y,!_&#x78;_&#x72;_m")_\
_(match\_operand:SI 1 "general\_operand" "daymKs,i,g,x&#x72;_&#x6D;"))]\
""\
"\*\
{\
if (GET\_CODE (operands\[1]) == CONST\_INT)\
{\
if (operands\[1] == const0\_rtx\
&& (DATA\_REG\_P (operands\[0])\
|| GET\_CODE (operands\[0]) == MEM))\
return "clr%.l %0";\
else if (DATA\_REG\_P (operands\[0])\
&& INTVAL (operands\[1]) < 128\
&& INTVAL (operands\[1]) >= -128)\
return "moveq %1,%0";\
else if (ADDRESS\_REG\_P (operands\[0])\
&& INTVAL (operands\[1]) < 0x8000\
&& INTVAL (operands\[1]) >= -0x8000)\
return "mov%.w %1,%0";\
else if (push\_operand (operands\[0], SImode)\
&& INTVAL (operands\[1]) < 0x8000\
&& INTVAL (operands\[1]) >= -0x8000)\
return "pea %a1";\
}\
else if ((GET\_CODE (operands\[1]) == SYMBOL\_REF\
|| GET\_CODE (operands\[1]) == CONST)\
&& push\_operand (operands\[0], SImode))\
return "pea %a1";\
else if ((GET\_CODE (operands\[1]) == SYMBOL\_REF\
|| GET\_CODE (operands\[1]) == CONST)\
&& ADDRESS\_REG\_P (operands\[0]))\
return "lea %a1,%0";\
return "mov%.l %1,%0";\
}")

(define\_insn "movhi"\
\[(set (match\_operand:HI 0 "general\_operand" "=g")\
(match\_operand:HI 1 "general\_operand" "g"))]\
""\
"\*\
{\
if (GET\_CODE (operands\[1]) == CONST\_INT)\
{\
if (operands\[1] == const0\_rtx\
&& (DATA\_REG\_P (operands\[0])\
|| GET\_CODE (operands\[0]) == MEM))\
return "clr%.w %0";\
else if (DATA\_REG\_P (operands\[0])\
&& INTVAL (operands\[1]) < 128\
&& INTVAL (operands\[1]) >= -128)\
{\
return "moveq %1,%0";\
}\
else if (INTVAL (operands\[1]) < 0x8000\
&& INTVAL (operands\[1]) >= -0x8000)\
return "mov%.w %1,%0";\
}\
else if (CONSTANT\_P (operands\[1]))\
return "mov%.l %1,%0";\
/\* Recognize the insn before a tablejump, one that refers\
to a table of offsets. Such an insn will need to refer\
to a label on the insn. So output one. Use the label-number\
of the table of offsets to generate this label. \*/\
if (GET\_CODE (operands\[1]) == MEM\
&& GET\_CODE (XEXP (operands\[1], 0)) == PLUS\
&& (GET\_CODE (XEXP (XEXP (operands\[1], 0), 0)) == LABEL\_REF\
|| GET\_CODE (XEXP (XEXP (operands\[1], 0), 1)) == LABEL\_REF)\
&& GET\_CODE (XEXP (XEXP (operands\[1], 0), 0)) != PLUS\
&& GET\_CODE (XEXP (XEXP (operands\[1], 0), 1)) != PLUS)\
{\
rtx labelref;\
if (GET\_CODE (XEXP (XEXP (operands\[1], 0), 0)) == LABEL\_REF)\
labelref = XEXP (XEXP (operands\[1], 0), 0);\
else\
labelref = XEXP (XEXP (operands\[1], 0), 1);\
ASM\_OUTPUT\_INTERNAL\_LABEL (asm\_out\_file, "LI",\
CODE\_LABEL\_NUMBER (XEXP (labelref, 0)));\
}\
return "mov%.w %1,%0";\
}")

(define\_insn "movstricthi"\
\[(set (strict\_low\_part (match\_operand:HI 0 "general\_operand" "+dm"))\
(match\_operand:HI 1 "general\_operand" "rmn"))]\
""\
"\*\
{\
if (GET\_CODE (operands\[1]) == CONST\_INT)\
{\
if (operands\[1] == const0\_rtx\
&& (DATA\_REG\_P (operands\[0])\
|| GET\_CODE (operands\[0]) == MEM))\
return "clr%.w %0";\
}\
return "mov%.w %1,%0";\
}")

(define\_insn "movqi"\
\[(set (match\_operand:QI 0 "general\_operand" "=d,_a,m,m,?a")_\
_(match\_operand:QI 1 "general\_operand" "dmia,&#x64;_&#x61;,dmi,?_a,m"))]_\
_""_\
_"_\
{\
rtx xoperands\[4];\
if (ADDRESS\_REG\_P (operands\[0]) && GET\_CODE (operands\[1]) == MEM)\
{\
xoperands\[1] = operands\[1];\
xoperands\[2]\
\= gen\_rtx (MEM, QImode,\
gen\_rtx (PLUS, VOIDmode, stack\_pointer\_rtx, const1\_rtx));\
xoperands\[3] = stack\_pointer\_rtx;\
/\* Just pushing a byte puts it in the high byte of the halfword. _/_\
_/_ We must put it in the low half, the second byte. \*/\
output\_asm\_insn ("subq%.w %#2,%3;mov%.b %1,%2", xoperands);\
return "mov%.w %+,%0";\
}\
if (ADDRESS\_REG\_P (operands\[1]) && GET\_CODE (operands\[0]) == MEM)\
{\
xoperands\[0] = operands\[0];\
xoperands\[1] = operands\[1];\
xoperands\[2]\
\= gen\_rtx (MEM, QImode,\
gen\_rtx (PLUS, VOIDmode, stack\_pointer\_rtx, const1\_rtx));\
xoperands\[3] = stack\_pointer\_rtx;\
output\_asm\_insn ("mov%.w %1,%-;mov%.b %2,%0;addq%.w %#2,%3", xoperands);\
return "";\
}\
if (operands\[1] == const0\_rtx)\
return "clr%.b %0";\
if (GET\_CODE (operands\[1]) == CONST\_INT\
&& INTVAL (operands\[1]) == -1)\
return "st %0";\
if (GET\_CODE (operands\[1]) != CONST\_INT && CONSTANT\_P (operands\[1]))\
return "mov%.l %1,%0";\
if (ADDRESS\_REG\_P (operands\[0]) || ADDRESS\_REG\_P (operands\[1]))\
return "mov%.w %1,%0";\
return "mov%.b %1,%0";\
}")

(define\_insn "movstrictqi"\
\[(set (strict\_low\_part (match\_operand:QI 0 "general\_operand" "+dm"))\
(match\_operand:QI 1 "general\_operand" "dmn"))]\
""\
"\*\
{\
if (operands\[1] == const0\_rtx)\
return "clr%.b %0";\
return "mov%.b %1,%0";\
}")

;; Floating-point moves on a CE are faster using an FP register than\
;; with movl instructions. (Especially for double floats, but also\
;; for single floats, even though it takes an extra instruction.) But\
;; on an IP, the FP registers are simulated and so should be avoided.\
;; We do this by using define\_expand for movsf and movdf, and using\
;; different constraints for each target type. The constraints for\
;; TARGET\_CE allow general registers because they sometimes need to\
;; hold floats, but they are not preferable.

(define\_expand "movsf"\
\[(set (match\_operand:SF 0 "general\_operand" "")\
(match\_operand:SF 1 "nonimmediate\_operand" ""))]\
""\
"")

(define\_insn ""\
\[(set (match\_operand:SF 0 "general\_operand" "=f,m,!_r,!&#x66;_&#x6D;")\
(match\_operand:SF 1 "nonimmediate\_operand" "fm,f,&#x66;_&#x72;_&#x6D;,_r"))]_\
_"TARGET\_CE"_\
_"_\
{\
if (FP\_REG\_P (operands\[0]))\
{\
if (FP\_REG\_P (operands\[1]))\
return "fmove%.s %1,%0";\
if (REG\_P (operands\[1]))\
return "mov%.l %1,%-;fmove%.s %+,%0";\
return "fmove%.s %1,%0";\
}\
if (FP\_REG\_P (operands\[1]))\
{\
if (REG\_P (operands\[0]))\
return "fmove%.s %1,%-;mov%.l %+,%0";\
return "fmove%.s %1,%0";\
}\
return "mov%.l %1,%0";\
}")

(define\_insn ""\
\[(set (match\_operand:SF 0 "general\_operand" "=frm")\
(match\_operand:SF 1 "nonimmediate\_operand" "frm"))]\
"!TARGET\_CE"\
"\*\
{\
if (FP\_REG\_P (operands\[0]))\
{\
if (FP\_REG\_P (operands\[1]))\
return "fmove%.s %1,%0";\
if (REG\_P (operands\[1]))\
return "mov%.l %1,%-;fmove%.s %+,%0";\
return "fmove%.s %1,%0";\
}\
if (FP\_REG\_P (operands\[1]))\
{\
if (REG\_P (operands\[0]))\
return "fmove%.s %1,%-;mov%.l %+,%0";\
return "fmove%.s %1,%0";\
}\
return "mov%.l %1,%0";\
}")

(define\_expand "movdf"\
\[(set (match\_operand:DF 0 "general\_operand" "")\
(match\_operand:DF 1 "nonimmediate\_operand" ""))]\
""\
"")

(define\_insn ""\
\[(set (match\_operand:DF 0 "general\_operand" "=f,m,!_r,!&#x66;_&#x6D;")\
(match\_operand:DF 1 "nonimmediate\_operand" "fm,f,&#x66;_&#x72;_&#x6D;,_r"))]_\
_"TARGET\_CE"_\
_"_\
{\
if (FP\_REG\_P (operands\[0]))\
{\
if (FP\_REG\_P (operands\[1]))\
return "fmove%.d %1,%0";\
if (REG\_P (operands\[1]))\
{\
rtx xoperands\[2];\
xoperands\[1] = gen\_rtx (REG, SImode, REGNO (operands\[1]) + 1);\
output\_asm\_insn ("mov%.l %1,%-", xoperands);\
output\_asm\_insn ("mov%.l %1,%-", operands);\
return "fmove%.d %+,%0";\
}\
return "fmove%.d %1,%0";\
}\
else if (FP\_REG\_P (operands\[1]))\
{\
if (REG\_P (operands\[0]))\
{\
output\_asm\_insn ("fmove%.d %1,%-;mov%.l %+,%0", operands);\
operands\[0] = gen\_rtx (REG, SImode, REGNO (operands\[0]) + 1);\
return "mov%.l %+,%0";\
}\
return "fmove%.d %1,%0";\
}\
return output\_move\_double (operands);\
}")

(define\_insn ""\
\[(set (match\_operand:DF 0 "general\_operand" "=frm")\
(match\_operand:DF 1 "nonimmediate\_operand" "frm"))]\
"!TARGET\_CE"\
"\*\
{\
if (FP\_REG\_P (operands\[0]))\
{\
if (FP\_REG\_P (operands\[1]))\
return "fmove%.d %1,%0";\
if (REG\_P (operands\[1]))\
{\
rtx xoperands\[2];\
xoperands\[1] = gen\_rtx (REG, SImode, REGNO (operands\[1]) + 1);\
output\_asm\_insn ("mov%.l %1,%-", xoperands);\
output\_asm\_insn ("mov%.l %1,%-", operands);\
return "fmove%.d %+,%0";\
}\
return "fmove%.d %1,%0";\
}\
else if (FP\_REG\_P (operands\[1]))\
{\
if (REG\_P (operands\[0]))\
{\
output\_asm\_insn ("fmove%.d %1,%-;mov%.l %+,%0", operands);\
operands\[0] = gen\_rtx (REG, SImode, REGNO (operands\[0]) + 1);\
return "mov%.l %+,%0";\
}\
return "fmove%.d %1,%0";\
}\
return output\_move\_double (operands);\
}")

(define\_insn "movdi"\
\[(set (match\_operand:DI 0 "general\_operand" "=rm,\&r,\&ro<>")\
(match\_operand:DI 1 "general\_operand" "r,m,roi<>"))]\
""\
"\*\
{\
return output\_move\_double (operands);\
}\
")

;; This goes after the move instructions\
;; because the move instructions are better (require no spilling)\
;; when they can apply. It goes before the add/sub insns\
;; so we will prefer it to them.

(define\_insn "pushasi"\
\[(set (match\_operand:SI 0 "push\_operand" "=m")\
(match\_operand:SI 1 "address\_operand" "p"))]\
""\
"pea %a1")\
;; truncation instructions\
(define\_insn "truncsiqi2"\
\[(set (match\_operand:QI 0 "general\_operand" "=dm,d")\
(truncate:QI\
(match\_operand:SI 1 "general\_operand" "doJ,i")))]\
""\
"\*\
{\
if (GET\_CODE (operands\[0]) == REG)\
return "mov%.l %1,%0";\
if (GET\_CODE (operands\[1]) == MEM)\
operands\[1] = adj\_offsettable\_operand (operands\[1], 3);\
return "mov%.b %1,%0";\
}")

(define\_insn "trunchiqi2"\
\[(set (match\_operand:QI 0 "general\_operand" "=dm,d")\
(truncate:QI\
(match\_operand:HI 1 "general\_operand" "doJ,i")))]\
""\
"\*\
{\
if (GET\_CODE (operands\[0]) == REG\
&& (GET\_CODE (operands\[1]) == MEM\
|| GET\_CODE (operands\[1]) == CONST\_INT))\
return "mov%.w %1,%0";\
if (GET\_CODE (operands\[0]) == REG)\
return "mov%.l %1,%0";\
if (GET\_CODE (operands\[1]) == MEM)\
operands\[1] = adj\_offsettable\_operand (operands\[1], 1);\
return "mov%.b %1,%0";\
}")

(define\_insn "truncsihi2"\
\[(set (match\_operand:HI 0 "general\_operand" "=dm,d")\
(truncate:HI\
(match\_operand:SI 1 "general\_operand" "roJ,i")))]\
""\
"\*\
{\
if (GET\_CODE (operands\[0]) == REG)\
return "mov%.l %1,%0";\
if (GET\_CODE (operands\[1]) == MEM)\
operands\[1] = adj\_offsettable\_operand (operands\[1], 2);\
return "mov%.w %1,%0";\
}")\
;; zero extension instructions

(define\_expand "zero\_extendhisi2"\
\[(set (match\_operand:SI 0 "register\_operand" "")\
(const\_int 0))\
(set (strict\_low\_part (subreg:HI (match\_dup 0) 0))\
(match\_operand:HI 1 "general\_operand" ""))]\
""\
"operands\[1] = make\_safe\_from (operands\[1], operands\[0]);")

(define\_expand "zero\_extendqihi2"\
\[(set (match\_operand:HI 0 "register\_operand" "")\
(const\_int 0))\
(set (strict\_low\_part (subreg:QI (match\_dup 0) 0))\
(match\_operand:QI 1 "general\_operand" ""))]\
""\
"operands\[1] = make\_safe\_from (operands\[1], operands\[0]);")

(define\_expand "zero\_extendqisi2"\
\[(set (match\_operand:SI 0 "register\_operand" "")\
(const\_int 0))\
(set (strict\_low\_part (subreg:QI (match\_dup 0) 0))\
(match\_operand:QI 1 "general\_operand" ""))]\
""\
" operands\[1] = make\_safe\_from (operands\[1], operands\[0]); ")\
;; Patterns to recognize zero-extend insns produced by the combiner.

;; Note that the one starting from HImode comes before those for QImode\
;; so that a constant operand will match HImode, not QImode.\
(define\_insn ""\
\[(set (match\_operand:SI 0 "general\_operand" "=do<>")\
(zero\_extend:SI\
(match\_operand:HI 1 "general\_operand" "rmn")))]\
""\
"\*\
{\
if (DATA\_REG\_P (operands\[0]))\
{\
if (GET\_CODE (operands\[1]) == REG\
&& REGNO (operands\[0]) == REGNO (operands\[1]))\
return "and%.l %#0xFFFF,%0";\
if (reg\_mentioned\_p (operands\[0], operands\[1]))\
return "mov%.w %1,%0;and%.l %#0xFFFF,%0";\
return "clr%.l %0;mov%.w %1,%0";\
}\
else if (GET\_CODE (operands\[0]) == MEM\
&& GET\_CODE (XEXP (operands\[0], 0)) == PRE\_DEC)\
return "mov%.w %1,%0;clr%.w %0";\
else if (GET\_CODE (operands\[0]) == MEM\
&& GET\_CODE (XEXP (operands\[0], 0)) == POST\_INC)\
return "clr%.w %0;mov%.w %1,%0";\
else\
{\
output\_asm\_insn ("clr%.w %0", operands);\
operands\[0] = adj\_offsettable\_operand (operands\[0], 2);\
return "mov%.w %1,%0";\
}\
}")

(define\_insn ""\
\[(set (match\_operand:HI 0 "general\_operand" "=do<>")\
(zero\_extend:HI\
(match\_operand:QI 1 "general\_operand" "dmn")))]\
""\
"\*\
{\
if (DATA\_REG\_P (operands\[0]))\
{\
if (GET\_CODE (operands\[1]) == REG\
&& REGNO (operands\[0]) == REGNO (operands\[1]))\
return "and%.w %#0xFF,%0";\
if (reg\_mentioned\_p (operands\[0], operands\[1]))\
return "mov%.b %1,%0;and%.w %#0xFF,%0";\
return "clr%.w %0;mov%.b %1,%0";\
}\
else if (GET\_CODE (operands\[0]) == MEM\
&& GET\_CODE (XEXP (operands\[0], 0)) == PRE\_DEC)\
{\
if (REGNO (XEXP (XEXP (operands\[0], 0), 0))\
\== STACK\_POINTER\_REGNUM)\
return "clr%.w %-;mov%.b %1,%0";\
else\
return "mov%.b %1,%0;clr%.b %0";\
}\
else if (GET\_CODE (operands\[0]) == MEM\
&& GET\_CODE (XEXP (operands\[0], 0)) == POST\_INC)\
return "clr%.b %0;mov%.b %1,%0";\
else\
{\
output\_asm\_insn ("clr%.b %0", operands);\
operands\[0] = adj\_offsettable\_operand (operands\[0], 1);\
return "mov%.b %1,%0";\
}\
}")

(define\_insn ""\
\[(set (match\_operand:SI 0 "general\_operand" "=do<>")\
(zero\_extend:SI\
(match\_operand:QI 1 "general\_operand" "dmn")))]\
""\
"\*\
{\
if (DATA\_REG\_P (operands\[0]))\
{\
if (GET\_CODE (operands\[1]) == REG\
&& REGNO (operands\[0]) == REGNO (operands\[1]))\
return "and%.l %#0xFF,%0";\
if (reg\_mentioned\_p (operands\[0], operands\[1]))\
return "mov%.b %1,%0;and%.l %#0xFF,%0";\
return "clr%.l %0;mov%.b %1,%0";\
}\
else if (GET\_CODE (operands\[0]) == MEM\
&& GET\_CODE (XEXP (operands\[0], 0)) == PRE\_DEC)\
{\
operands\[0] = XEXP (XEXP (operands\[0], 0), 0);\
return "clr%.l %0@-;mov%.b %1,%0@(3)";\
}\
else if (GET\_CODE (operands\[0]) == MEM\
&& GET\_CODE (XEXP (operands\[0], 0)) == POST\_INC)\
{\
operands\[0] = XEXP (XEXP (operands\[0], 0), 0);\
return "clr%.l %0@+;mov%.b %1,%0@(-1)";\
}\
else\
{\
output\_asm\_insn ("clr%.l %0", operands);\
operands\[0] = adj\_offsettable\_operand (operands\[0], 3);\
return "mov%.b %1,%0";\
}\
}")\
;; sign extension instructions\
;; Note that the one starting from HImode comes before those for QImode\
;; so that a constant operand will match HImode, not QImode.

(define\_insn "extendhisi2"\
\[(set (match\_operand:SI 0 "general\_operand" "=_d,a")_\
_(sign\_extend:SI_\
_(match\_operand:HI 1 "general\_operand" "0,rmn")))]_\
_""_\
_"_\
{\
if (ADDRESS\_REG\_P (operands\[0]))\
return "mov%.w %1,%0";\
return "ext%.l %0";\
}")

(define\_insn "extendqihi2"\
\[(set (match\_operand:HI 0 "general\_operand" "=d")\
(sign\_extend:HI\
(match\_operand:QI 1 "general\_operand" "0")))]\
""\
"ext%.w %0")

(define\_insn "extendqisi2"\
\[(set (match\_operand:SI 0 "general\_operand" "=d")\
(sign\_extend:SI\
(match\_operand:QI 1 "general\_operand" "0")))]\
"TARGET\_68020"\
"extb%.l %0")\
;; Conversions between float and double.

(define\_insn "extendsfdf2"\
\[(set (match\_operand:DF 0 "general\_operand" "=f,m")\
(float\_extend:DF\
(match\_operand:SF 1 "nonimmediate\_operand" "fm,f")))]\
"TARGET\_CE"\
"fmovesd %1,%0")

(define\_insn "truncdfsf2"\
\[(set (match\_operand:SF 0 "general\_operand" "=f,m")\
(float\_truncate:SF\
(match\_operand:DF 1 "nonimmediate\_operand" "fm,f")))]\
"TARGET\_CE"\
"fmoveds %1,%0")\
;; Conversion between fixed point and floating point.\
;; Note that among the fix-to-float insns\
;; the ones that start with SImode come first.\
;; That is so that an operand that is a CONST\_INT\
;; (and therefore lacks a specific machine mode).\
;; will be recognized as SImode (which is always valid)\
;; rather than as QImode or HImode.

(define\_insn "floatsisf2"\
\[(set (match\_operand:SF 0 "register\_operand" "=f")\
(float:SF (match\_operand:SI 1 "nonimmediate\_operand" "dm")))]\
"TARGET\_CE"\
"fmovels %1,%0")

(define\_insn "floatsidf2"\
\[(set (match\_operand:DF 0 "register\_operand" "=f")\
(float:DF (match\_operand:SI 1 "nonimmediate\_operand" "dm")))]\
"TARGET\_CE"\
"fmoveld %1,%0")

(define\_insn "floathisf2"\
\[(set (match\_operand:SF 0 "register\_operand" "=f")\
(float:SF (match\_operand:HI 1 "nonimmediate\_operand" "dm")))]\
"TARGET\_CE"\
"fmovews %1,%0")

(define\_insn "floathidf2"\
\[(set (match\_operand:DF 0 "register\_operand" "=f")\
(float:DF (match\_operand:HI 1 "nonimmediate\_operand" "dm")))]\
"TARGET\_CE"\
"fmovewd %1,%0")

(define\_insn "floatqisf2"\
\[(set (match\_operand:SF 0 "register\_operand" "=f")\
(float:SF (match\_operand:QI 1 "nonimmediate\_operand" "dm")))]\
"TARGET\_CE"\
"fmovebs %1,%0")

(define\_insn "floatqidf2"\
\[(set (match\_operand:DF 0 "register\_operand" "=f")\
(float:DF (match\_operand:QI 1 "nonimmediate\_operand" "dm")))]\
"TARGET\_CE"\
"fmovebd %1,%0")\
;; Float-to-fix conversion insns.

(define\_insn "fix\_truncsfqi2"\
\[(set (match\_operand:QI 0 "general\_operand" "=dm")\
(fix:QI (fix:SF (match\_operand:SF 1 "register\_operand" "f"))))]\
"TARGET\_CE"\
"fmovesb %1,%0")

(define\_insn "fix\_truncsfhi2"\
\[(set (match\_operand:HI 0 "general\_operand" "=dm")\
(fix:HI (fix:SF (match\_operand:SF 1 "register\_operand" "f"))))]\
"TARGET\_CE"\
"fmovesw %1,%0")

(define\_insn "fix\_truncsfsi2"\
\[(set (match\_operand:SI 0 "general\_operand" "=dm")\
(fix:SI (fix:SF (match\_operand:SF 1 "register\_operand" "f"))))]\
"TARGET\_CE"\
"fmovesl %1,%0")

(define\_insn "fix\_truncdfqi2"\
\[(set (match\_operand:QI 0 "general\_operand" "=dm")\
(fix:QI (fix:DF (match\_operand:DF 1 "register\_operand" "f"))))]\
"TARGET\_CE"\
"fmovedb %1,%0")

(define\_insn "fix\_truncdfhi2"\
\[(set (match\_operand:HI 0 "general\_operand" "=dm")\
(fix:HI (fix:DF (match\_operand:DF 1 "register\_operand" "f"))))]\
"TARGET\_CE"\
"fmovedw %1,%0")

(define\_insn "fix\_truncdfsi2"\
\[(set (match\_operand:SI 0 "general\_operand" "=dm")\
(fix:SI (fix:DF (match\_operand:DF 1 "register\_operand" "f"))))]\
"TARGET\_CE"\
"fmovedl %1,%0")\
;; add instructions

(define\_insn "addsi3"\
\[(set (match\_operand:SI 0 "general\_operand" "=m,r,!a,!a")\
(plus:SI (match\_operand:SI 1 "general\_operand" "%0,0,a,rJK")\
(match\_operand:SI 2 "general\_operand" "dIKLs,mrIKLs,rJK,a")))]\
""\
"\*\
{\
if (! operands\_match\_p (operands\[0], operands\[1]))\
{\
if (!ADDRESS\_REG\_P (operands\[1]))\
{\
rtx tmp = operands\[1];

```
  operands[1] = operands[2];
  operands[2] = tmp;
}

  /* These insns can result from reloads to access
 stack slots over 64k from the frame pointer.  */
  if (GET_CODE (operands[2]) == CONST_INT
  && INTVAL (operands[2]) + 0x8000 >= (unsigned) 0x10000)
    return \"mov%.l %2,%0\;add%.l %1,%0\";
  if (GET_CODE (operands[2]) == REG)
return \"lea %1@[%2:L:B],%0\";
  else
return \"lea %1@(%c2),%0\";
}
```

if (GET\_CODE (operands\[2]) == CONST\_INT)\
{\
if (INTVAL (operands\[2]) > 0\
&& INTVAL (operands\[2]) <= 8)\
return (ADDRESS\_REG\_P (operands\[0])\
? "addq%.w %2,%0"\
: "addq%.l %2,%0");\
if (INTVAL (operands\[2]) < 0\
&& INTVAL (operands\[2]) >= -8)\
{\
operands\[2] = gen\_rtx (CONST\_INT, VOIDmode,\
\- INTVAL (operands\[2]));\
return (ADDRESS\_REG\_P (operands\[0])\
? "subq%.w %2,%0"\
: "subq%.l %2,%0");\
}\
if (ADDRESS\_REG\_P (operands\[0])\
&& INTVAL (operands\[2]) >= -0x8000\
&& INTVAL (operands\[2]) < 0x8000)\
return "add%.w %2,%0";\
}\
return "add%.l %2,%0";\
}")

(define\_insn ""\
\[(set (match\_operand:SI 0 "general\_operand" "=a")\
(plus:SI (match\_operand:SI 1 "general\_operand" "0")\
(sign\_extend:SI (match\_operand:HI 2 "general\_operand" "rmn"))))]\
""\
"add%.w %2,%0")

(define\_insn "addhi3"\
\[(set (match\_operand:HI 0 "general\_operand" "=m,r")\
(plus:HI (match\_operand:HI 1 "general\_operand" "%0,0")\
(match\_operand:HI 2 "general\_operand" "dn,rmn")))]\
""\
"\*\
{\
if (GET\_CODE (operands\[2]) == CONST\_INT)\
{\
if (INTVAL (operands\[2]) > 0\
&& INTVAL (operands\[2]) <= 8)\
return "addq%.w %2,%0";\
}\
if (GET\_CODE (operands\[2]) == CONST\_INT)\
{\
if (INTVAL (operands\[2]) < 0\
&& INTVAL (operands\[2]) >= -8)\
{\
operands\[2] = gen\_rtx (CONST\_INT, VOIDmode,\
\- INTVAL (operands\[2]));\
return "subq%.w %2,%0";\
}\
}\
return "add%.w %2,%0";\
}")

(define\_insn ""\
\[(set (strict\_low\_part (match\_operand:HI 0 "general\_operand" "+m,d"))\
(plus:HI (match\_dup 0)\
(match\_operand:HI 1 "general\_operand" "dn,rmn")))]\
""\
"add%.w %1,%0")

(define\_insn "addqi3"\
\[(set (match\_operand:QI 0 "general\_operand" "=m,d")\
(plus:QI (match\_operand:QI 1 "general\_operand" "%0,0")\
(match\_operand:QI 2 "general\_operand" "dn,dmn")))]\
""\
"\*\
{\
if (GET\_CODE (operands\[2]) == CONST\_INT)\
{\
if (INTVAL (operands\[2]) > 0\
&& INTVAL (operands\[2]) <= 8)\
return "addq%.b %2,%0";\
}\
if (GET\_CODE (operands\[2]) == CONST\_INT)\
{\
if (INTVAL (operands\[2]) < 0 && INTVAL (operands\[2]) >= -8)\
{\
operands\[2] = gen\_rtx (CONST\_INT, VOIDmode, - INTVAL (operands\[2]));\
return "subq%.b %2,%0";\
}\
}\
return "add%.b %2,%0";\
}")

(define\_insn ""\
\[(set (strict\_low\_part (match\_operand:QI 0 "general\_operand" "+m,d"))\
(plus:QI (match\_dup 0)\
(match\_operand:QI 1 "general\_operand" "dn,dmn")))]\
""\
"add%.b %1,%0")

(define\_insn "adddf3"\
\[(set (match\_operand:DF 0 "register\_operand" "=f")\
(plus:DF (match\_operand:DF 1 "nonimmediate\_operand" "%f")\
(match\_operand:DF 2 "nonimmediate\_operand" "fm")))]\
"TARGET\_CE"\
"fadd%.d %2,%1,%0")

(define\_insn "addsf3"\
\[(set (match\_operand:SF 0 "register\_operand" "=f")\
(plus:SF (match\_operand:SF 1 "nonimmediate\_operand" "%f")\
(match\_operand:SF 2 "nonimmediate\_operand" "fm")))]\
"TARGET\_CE"\
"fadd%.s %2,%1,%0")\
;; subtract instructions

(define\_insn "subsi3"\
\[(set (match\_operand:SI 0 "general\_operand" "=m,r,!a,?d")\
(minus:SI (match\_operand:SI 1 "general\_operand" "0,0,a,mrIKs")\
(match\_operand:SI 2 "general\_operand" "dIKs,mrIKs,J,0")))]\
""\
"\*\
{\
if (! operands\_match\_p (operands\[0], operands\[1]))\
{\
if (operands\_match\_p (operands\[0], operands\[2]))\
{\
if (GET\_CODE (operands\[1]) == CONST\_INT)\
{\
if (INTVAL (operands\[1]) > 0\
&& INTVAL (operands\[1]) <= 8)\
return "subq%.l %1,%0;neg%.l %0";\
}\
return "sub%.l %1,%0;neg%.l %0";\
}\
/\* This case is matched by J, but negating -0x8000\
in an lea would give an invalid displacement.\
So do this specially. \*/\
if (INTVAL (operands\[2]) == -0x8000)\
return "mov%.l %1,%0;sub%.l %2,%0";\
return "lea %1@(%n2),%0";\
}\
if (GET\_CODE (operands\[2]) == CONST\_INT)\
{\
if (INTVAL (operands\[2]) > 0\
&& INTVAL (operands\[2]) <= 8)\
return "subq%.l %2,%0";\
if (ADDRESS\_REG\_P (operands\[0])\
&& INTVAL (operands\[2]) >= -0x8000\
&& INTVAL (operands\[2]) < 0x8000)\
return "sub%.w %2,%0";\
}\
return "sub%.l %2,%0";\
}")

(define\_insn ""\
\[(set (match\_operand:SI 0 "general\_operand" "=a")\
(minus:SI (match\_operand:SI 1 "general\_operand" "0")\
(sign\_extend:SI (match\_operand:HI 2 "general\_operand" "rmn"))))]\
""\
"sub%.w %2,%0")

(define\_insn "subhi3"\
\[(set (match\_operand:HI 0 "general\_operand" "=m,r")\
(minus:HI (match\_operand:HI 1 "general\_operand" "0,0")\
(match\_operand:HI 2 "general\_operand" "dn,rmn")))]\
""\
"sub%.w %2,%0")

(define\_insn ""\
\[(set (strict\_low\_part (match\_operand:HI 0 "general\_operand" "+m,d"))\
(minus:HI (match\_dup 0)\
(match\_operand:HI 1 "general\_operand" "dn,rmn")))]\
""\
"sub%.w %1,%0")

(define\_insn "subqi3"\
\[(set (match\_operand:QI 0 "general\_operand" "=m,d")\
(minus:QI (match\_operand:QI 1 "general\_operand" "0,0")\
(match\_operand:QI 2 "general\_operand" "dn,dmn")))]\
""\
"sub%.b %2,%0")

(define\_insn ""\
\[(set (strict\_low\_part (match\_operand:QI 0 "general\_operand" "+m,d"))\
(minus:QI (match\_dup 0)\
(match\_operand:QI 1 "general\_operand" "dn,dmn")))]\
""\
"sub%.b %1,%0")

(define\_insn "subdf3"\
\[(set (match\_operand:DF 0 "register\_operand" "=f,f")\
(minus:DF (match\_operand:DF 1 "nonimmediate\_operand" "f,fm")\
(match\_operand:DF 2 "nonimmediate\_operand" "fm,f")))]\
"TARGET\_CE"\
"\*\
{\
if (FP\_REG\_P (operands\[1]))\
return "fsub%.d %2,%1,%0";\
return "frsub%.d %1,%2,%0";\
}")

(define\_insn "subsf3"\
\[(set (match\_operand:SF 0 "register\_operand" "=f,f")\
(minus:SF (match\_operand:SF 1 "nonimmediate\_operand" "f,fm")\
(match\_operand:SF 2 "nonimmediate\_operand" "fm,f")))]\
"TARGET\_CE"\
"\*\
{\
if (FP\_REG\_P (operands\[1]))\
return "fsub%.s %2,%1,%0";\
return "frsub%.s %1,%2,%0";\
}")\
;; multiply instructions

(define\_insn "mulhi3"\
\[(set (match\_operand:HI 0 "general\_operand" "=d")\
(mult:HI (match\_operand:HI 1 "general\_operand" "%0")\
(match\_operand:HI 2 "general\_operand" "dmn")))]\
""\
"muls %2,%0")

(define\_insn "mulhisi3"\
\[(set (match\_operand:SI 0 "general\_operand" "=d")\
(mult:SI (match\_operand:HI 1 "general\_operand" "%0")\
(match\_operand:HI 2 "general\_operand" "dmn")))]\
""\
"muls %2,%0")

(define\_insn "mulsi3"\
\[(set (match\_operand:SI 0 "general\_operand" "=d")\
(mult:SI (match\_operand:SI 1 "general\_operand" "%0")\
(match\_operand:SI 2 "general\_operand" "dmsK")))]\
"TARGET\_68020"\
"muls%.l %2,%0")

(define\_insn "umulhi3"\
\[(set (match\_operand:HI 0 "general\_operand" "=d")\
(umult:HI (match\_operand:HI 1 "general\_operand" "%0")\
(match\_operand:HI 2 "general\_operand" "dmn")))]\
""\
"mulu %2,%0")

(define\_insn "umulhisi3"\
\[(set (match\_operand:SI 0 "general\_operand" "=d")\
(umult:SI (match\_operand:HI 1 "general\_operand" "%0")\
(match\_operand:HI 2 "general\_operand" "dmn")))]\
""\
"mulu %2,%0")

(define\_insn "umulsi3"\
\[(set (match\_operand:SI 0 "general\_operand" "=d")\
(umult:SI (match\_operand:SI 1 "general\_operand" "%0")\
(match\_operand:SI 2 "general\_operand" "dmsK")))]\
"TARGET\_68020"\
"mulu%.l %2,%0")

(define\_insn "muldf3"\
\[(set (match\_operand:DF 0 "register\_operand" "=f")\
(mult:DF (match\_operand:DF 1 "nonimmediate\_operand" "%f")\
(match\_operand:DF 2 "nonimmediate\_operand" "fm")))]\
"TARGET\_CE"\
"fmul%.d %2,%1,%0")

(define\_insn "mulsf3"\
\[(set (match\_operand:SF 0 "register\_operand" "=f")\
(mult:SF (match\_operand:SF 1 "nonimmediate\_operand" "%f")\
(match\_operand:SF 2 "nonimmediate\_operand" "fm")))]\
"TARGET\_CE"\
"fmul%.s %2,%1,%0")\
;; divide instructions

(define\_insn "divhi3"\
\[(set (match\_operand:HI 0 "general\_operand" "=d")\
(div:HI (match\_operand:HI 1 "general\_operand" "0")\
(match\_operand:HI 2 "general\_operand" "dmn")))]\
""\
"extl %0;divs %2,%0")

(define\_insn "divhisi3"\
\[(set (match\_operand:HI 0 "general\_operand" "=d")\
(div:HI (match\_operand:SI 1 "general\_operand" "0")\
(match\_operand:HI 2 "general\_operand" "dmn")))]\
""\
"divs %2,%0")

(define\_insn "divsi3"\
\[(set (match\_operand:SI 0 "general\_operand" "=d")\
(div:SI (match\_operand:SI 1 "general\_operand" "0")\
(match\_operand:SI 2 "general\_operand" "dmsK")))]\
"TARGET\_68020"\
"divs%.l %2,%0,%0")

(define\_insn "udivhi3"\
\[(set (match\_operand:HI 0 "general\_operand" "=d")\
(udiv:HI (match\_operand:HI 1 "general\_operand" "0")\
(match\_operand:HI 2 "general\_operand" "dmn")))]\
""\
"and%.l %#0xFFFF,%0;divu %2,%0")

(define\_insn "udivhisi3"\
\[(set (match\_operand:HI 0 "general\_operand" "=d")\
(udiv:HI (match\_operand:SI 1 "general\_operand" "0")\
(match\_operand:HI 2 "general\_operand" "dmn")))]\
""\
"divu %2,%0")

(define\_insn "udivsi3"\
\[(set (match\_operand:SI 0 "general\_operand" "=d")\
(udiv:SI (match\_operand:SI 1 "general\_operand" "0")\
(match\_operand:SI 2 "general\_operand" "dmsK")))]\
"TARGET\_68020"\
"divu%.l %2,%0,%0")

(define\_insn "divdf3"\
\[(set (match\_operand:DF 0 "register\_operand" "=f,f")\
(div:DF (match\_operand:DF 1 "nonimmediate\_operand" "f,fm")\
(match\_operand:DF 2 "nonimmediate\_operand" "fm,f")))]\
"TARGET\_CE"\
"\*\
{\
if (FP\_REG\_P (operands\[1]))\
return "fdiv%.d %2,%1,%0";\
return "frdiv%.d %1,%2,%0";\
}")

(define\_insn "divsf3"\
\[(set (match\_operand:SF 0 "register\_operand" "=f,f")\
(div:SF (match\_operand:SF 1 "nonimmediate\_operand" "f,fm")\
(match\_operand:SF 2 "nonimmediate\_operand" "fm,f")))]\
"TARGET\_CE"\
"\*\
{\
if (FP\_REG\_P (operands\[1]))\
return "fdiv%.s %2,%1,%0";\
return "frdiv%.s %1,%2,%0";\
}")\
;; Remainder instructions.

(define\_insn "modhi3"\
\[(set (match\_operand:HI 0 "general\_operand" "=d")\
(mod:HI (match\_operand:HI 1 "general\_operand" "0")\
(match\_operand:HI 2 "general\_operand" "dmn")))]\
""\
"\*\
{\
/\* The swap insn produces cc's that don't correspond to the result. \*/\
CC\_STATUS\_INIT;\
return "extl %0;divs %2,%0;swap %0";\
}")

(define\_insn "modhisi3"\
\[(set (match\_operand:HI 0 "general\_operand" "=d")\
(mod:HI (match\_operand:SI 1 "general\_operand" "0")\
(match\_operand:HI 2 "general\_operand" "dmn")))]\
""\
"\*\
{\
/\* The swap insn produces cc's that don't correspond to the result. \*/\
CC\_STATUS\_INIT;\
return "divs %2,%0;swap %0";\
}")

(define\_insn "umodhi3"\
\[(set (match\_operand:HI 0 "general\_operand" "=d")\
(umod:HI (match\_operand:HI 1 "general\_operand" "0")\
(match\_operand:HI 2 "general\_operand" "dmn")))]\
""\
"\*\
{\
/\* The swap insn produces cc's that don't correspond to the result. \*/\
CC\_STATUS\_INIT;\
return "and%.l %#0xFFFF,%0;divu %2,%0;swap %0";\
}")

(define\_insn "umodhisi3"\
\[(set (match\_operand:HI 0 "general\_operand" "=d")\
(umod:HI (match\_operand:SI 1 "general\_operand" "0")\
(match\_operand:HI 2 "general\_operand" "dmn")))]\
""\
"\*\
{\
/\* The swap insn produces cc's that don't correspond to the result. \*/\
CC\_STATUS\_INIT;\
return "divu %2,%0;swap %0";\
}")

(define\_insn "divmodsi4"\
\[(set (match\_operand:SI 0 "general\_operand" "=d")\
(div:SI (match\_operand:SI 1 "general\_operand" "0")\
(match\_operand:SI 2 "general\_operand" "dmsK")))\
(set (match\_operand:SI 3 "general\_operand" "=d")\
(mod:SI (match\_dup 1) (match\_dup 2)))]\
"TARGET\_68020"\
"divs%.l %2,%0,%3")

(define\_insn "udivmodsi4"\
\[(set (match\_operand:SI 0 "general\_operand" "=d")\
(udiv:SI (match\_operand:SI 1 "general\_operand" "0")\
(match\_operand:SI 2 "general\_operand" "dmsK")))\
(set (match\_operand:SI 3 "general\_operand" "=d")\
(umod:SI (match\_dup 1) (match\_dup 2)))]\
"TARGET\_68020"\
"divu%.l %2,%0,%3")\
;; logical-and instructions

(define\_insn "andsi3"\
\[(set (match\_operand:SI 0 "general\_operand" "=m,d")\
(and:SI (match\_operand:SI 1 "general\_operand" "%0,0")\
(match\_operand:SI 2 "general\_operand" "dKs,dmKs")))]\
""\
"\*\
{\
if (GET\_CODE (operands\[2]) == CONST\_INT\
&& (INTVAL (operands\[2]) | 0xffff) == 0xffffffff\
&& (DATA\_REG\_P (operands\[0])\
|| offsettable\_memref\_p (operands\[0])))\
{\
if (GET\_CODE (operands\[0]) != REG)\
operands\[0] = adj\_offsettable\_operand (operands\[0], 2);\
operands\[2] = gen\_rtx (CONST\_INT, VOIDmode,\
INTVAL (operands\[2]) & 0xffff);\
/\* Do not delete a following tstl %0 insn; that would be incorrect. \*/\
CC\_STATUS\_INIT;\
if (operands\[2] == const0\_rtx)\
return "clr%.w %0";\
return "and%.w %2,%0";\
}\
return "and%.l %2,%0";\
}")

(define\_insn "andhi3"\
\[(set (match\_operand:HI 0 "general\_operand" "=m,d")\
(and:HI (match\_operand:HI 1 "general\_operand" "%0,0")\
(match\_operand:HI 2 "general\_operand" "dn,dmn")))]\
""\
"and%.w %2,%0")

(define\_insn "andqi3"\
\[(set (match\_operand:QI 0 "general\_operand" "=m,d")\
(and:QI (match\_operand:QI 1 "general\_operand" "%0,0")\
(match\_operand:QI 2 "general\_operand" "dn,dmn")))]\
""\
"and%.b %2,%0")

(define\_insn ""\
\[(set (match\_operand:SI 0 "general\_operand" "=d")\
(and:SI (zero\_extend:SI (match\_operand:HI 1 "general\_operand" "dm"))\
(match\_operand:SI 2 "general\_operand" "0")))]\
"GET\_CODE (operands\[2]) == CONST\_INT\
&& (unsigned int) INTVAL (operands\[2]) < (1 << GET\_MODE\_BITSIZE (HImode))"\
"and%.w %1,%0")

(define\_insn ""\
\[(set (match\_operand:SI 0 "general\_operand" "=d")\
(and:SI (zero\_extend:SI (match\_operand:QI 1 "general\_operand" "dm"))\
(match\_operand:SI 2 "general\_operand" "0")))]\
"GET\_CODE (operands\[2]) == CONST\_INT\
&& (unsigned int) INTVAL (operands\[2]) < (1 << GET\_MODE\_BITSIZE (QImode))"\
"and%.b %1,%0")\
;; inclusive-or instructions

(define\_insn "iorsi3"\
\[(set (match\_operand:SI 0 "general\_operand" "=m,d")\
(ior:SI (match\_operand:SI 1 "general\_operand" "%0,0")\
(match\_operand:SI 2 "general\_operand" "dKs,dmKs")))]\
""\
"\*\
{\
register int logval;\
if (GET\_CODE (operands\[2]) == CONST\_INT\
&& INTVAL (operands\[2]) >> 16 == 0\
&& (DATA\_REG\_P (operands\[0])\
|| offsettable\_memref\_p (operands\[0])))\
{\
if (GET\_CODE (operands\[0]) != REG)\
operands\[0] = adj\_offsettable\_operand (operands\[0], 2);\
/\* Do not delete a following tstl %0 insn; that would be incorrect. \*/\
CC\_STATUS\_INIT;\
return "or%.w %2,%0";\
}\
if (GET\_CODE (operands\[2]) == CONST\_INT\
&& (logval = exact\_log2 (INTVAL (operands\[2]))) >= 0\
&& (DATA\_REG\_P (operands\[0])\
|| offsettable\_memref\_p (operands\[0])))\
{\
if (DATA\_REG\_P (operands\[0]))\
operands\[1] = gen\_rtx (CONST\_INT, VOIDmode, logval);\
else\
{\
operands\[0] = adj\_offsettable\_operand (operands\[0], 3 - (logval / 8));\
operands\[1] = gen\_rtx (CONST\_INT, VOIDmode, logval % 8);\
}\
return "bset %1,%0";\
}\
return "or%.l %2,%0";\
}")

(define\_insn "iorhi3"\
\[(set (match\_operand:HI 0 "general\_operand" "=m,d")\
(ior:HI (match\_operand:HI 1 "general\_operand" "%0,0")\
(match\_operand:HI 2 "general\_operand" "dn,dmn")))]\
""\
"or%.w %2,%0")

(define\_insn "iorqi3"\
\[(set (match\_operand:QI 0 "general\_operand" "=m,d")\
(ior:QI (match\_operand:QI 1 "general\_operand" "%0,0")\
(match\_operand:QI 2 "general\_operand" "dn,dmn")))]\
""\
"or%.b %2,%0")\
;; xor instructions

(define\_insn "xorsi3"\
\[(set (match\_operand:SI 0 "general\_operand" "=do,m")\
(xor:SI (match\_operand:SI 1 "general\_operand" "%0,0")\
(match\_operand:SI 2 "general\_operand" "di,dKs")))]\
""\
"\*\
{\
if (GET\_CODE (operands\[2]) == CONST\_INT\
&& INTVAL (operands\[2]) >> 16 == 0\
&& (offsettable\_memref\_p (operands\[0]) || DATA\_REG\_P (operands\[0])))\
{\
if (! DATA\_REG\_P (operands\[0]))\
operands\[0] = adj\_offsettable\_operand (operands\[0], 2);\
/\* Do not delete a following tstl %0 insn; that would be incorrect. \*/\
CC\_STATUS\_INIT;\
return "eor%.w %2,%0";\
}\
return "eor%.l %2,%0";\
}")

(define\_insn "xorhi3"\
\[(set (match\_operand:HI 0 "general\_operand" "=dm")\
(xor:HI (match\_operand:HI 1 "general\_operand" "%0")\
(match\_operand:HI 2 "general\_operand" "dn")))]\
""\
"eor%.w %2,%0")

(define\_insn "xorqi3"\
\[(set (match\_operand:QI 0 "general\_operand" "=dm")\
(xor:QI (match\_operand:QI 1 "general\_operand" "%0")\
(match\_operand:QI 2 "general\_operand" "dn")))]\
""\
"eor%.b %2,%0")\
;; negation instructions

(define\_insn "negsi2"\
\[(set (match\_operand:SI 0 "general\_operand" "=dm")\
(neg:SI (match\_operand:SI 1 "general\_operand" "0")))]\
""\
"neg%.l %0")

(define\_insn "neghi2"\
\[(set (match\_operand:HI 0 "general\_operand" "=dm")\
(neg:HI (match\_operand:HI 1 "general\_operand" "0")))]\
""\
"neg%.w %0")

(define\_insn "negqi2"\
\[(set (match\_operand:QI 0 "general\_operand" "=dm")\
(neg:QI (match\_operand:QI 1 "general\_operand" "0")))]\
""\
"neg%.b %0")

(define\_insn "negsf2"\
\[(set (match\_operand:SF 0 "register\_operand" "=f")\
(neg:SF (match\_operand:SF 1 "nonimmediate\_operand" "fm")))]\
"TARGET\_CE"\
"fneg%.s %1,%0")

(define\_insn "negdf2"\
\[(set (match\_operand:DF 0 "register\_operand" "=f")\
(neg:DF (match\_operand:DF 1 "nonimmediate\_operand" "fm")))]\
"TARGET\_CE"\
"fneg%.d %1,%0")\
;; Absolute value instructions

(define\_insn "abssf2"\
\[(set (match\_operand:SF 0 "register\_operand" "=f")\
(abs:SF (match\_operand:SF 1 "nonimmediate\_operand" "fm")))]\
"TARGET\_CE"\
"fabs%.s %1,%0")

(define\_insn "absdf2"\
\[(set (match\_operand:DF 0 "register\_operand" "=f")\
(abs:DF (match\_operand:DF 1 "nonimmediate\_operand" "fm")))]\
"TARGET\_CE"\
"fabs%.d %1,%0")\
;; Square root instructions

(define\_insn "sqrtsf2"\
\[(set (match\_operand:SF 0 "register\_operand" "=f")\
(sqrt:SF (match\_operand:SF 1 "nonimmediate\_operand" "fm")))]\
"TARGET\_CE"\
"fsqrt%.s %1,%0")

(define\_insn "sqrtdf2"\
\[(set (match\_operand:DF 0 "register\_operand" "=f")\
(sqrt:DF (match\_operand:DF 1 "nonimmediate\_operand" "fm")))]\
"TARGET\_CE"\
"fsqrt%.d %1,%0")\
;; one complement instructions

(define\_insn "one\_cmplsi2"\
\[(set (match\_operand:SI 0 "general\_operand" "=dm")\
(not:SI (match\_operand:SI 1 "general\_operand" "0")))]\
""\
"not%.l %0")

(define\_insn "one\_cmplhi2"\
\[(set (match\_operand:HI 0 "general\_operand" "=dm")\
(not:HI (match\_operand:HI 1 "general\_operand" "0")))]\
""\
"not%.w %0")

(define\_insn "one\_cmplqi2"\
\[(set (match\_operand:QI 0 "general\_operand" "=dm")\
(not:QI (match\_operand:QI 1 "general\_operand" "0")))]\
""\
"not%.b %0")\
;; Optimized special case of shifting.\
;; Must precede the general case.

(define\_insn ""\
\[(set (match\_operand:SI 0 "general\_operand" "=d")\
(ashiftrt:SI (match\_operand:SI 1 "memory\_operand" "m")\
(const\_int 24)))]\
"GET\_CODE (XEXP (operands\[1], 0)) != POST\_INC\
&& GET\_CODE (XEXP (operands\[1], 0)) != PRE\_DEC"\
"\*\
{\
if (TARGET\_68020)\
return "mov%.b %1,%0;extb%.l %0";\
return "mov%.b %1,%0;ext%.w %0;ext%.l %0";\
}")

(define\_insn ""\
\[(set (match\_operand:SI 0 "general\_operand" "=d")\
(lshiftrt:SI (match\_operand:SI 1 "memory\_operand" "m")\
(const\_int 24)))]\
"GET\_CODE (XEXP (operands\[1], 0)) != POST\_INC\
&& GET\_CODE (XEXP (operands\[1], 0)) != PRE\_DEC"\
"\*\
{\
if (reg\_mentioned\_p (operands\[0], operands\[1]))\
return "mov%.b %1,%0;and%.l %#0xFF,%0";\
return "clr%.l %0;mov%.b %1,%0";\
}")

(define\_insn ""\
\[(set (cc0) (compare (match\_operand:QI 0 "general\_operand" "i")\
(lshiftrt:SI (match\_operand:SI 1 "memory\_operand" "m")\
(const\_int 24))))]\
"(GET\_CODE (operands\[0]) == CONST\_INT\
&& (INTVAL (operands\[0]) & \~0xff) == 0)"\
"\* cc\_status.flags |= CC\_REVERSED;\
return "cmp%.b %0,%1";\
")

(define\_insn ""\
\[(set (cc0) (compare (lshiftrt:SI (match\_operand:SI 0 "memory\_operand" "m")\
(const\_int 24))\
(match\_operand:QI 1 "general\_operand" "i")))]\
"(GET\_CODE (operands\[1]) == CONST\_INT\
&& (INTVAL (operands\[1]) & \~0xff) == 0)"\
"\*\
return "cmp%.b %1,%0";\
")

(define\_insn ""\
\[(set (cc0) (compare (match\_operand:QI 0 "general\_operand" "i")\
(ashiftrt:SI (match\_operand:SI 1 "memory\_operand" "m")\
(const\_int 24))))]\
"(GET\_CODE (operands\[0]) == CONST\_INT\
&& ((INTVAL (operands\[0]) + 0x80) & \~0xff) == 0)"\
"\* cc\_status.flags |= CC\_REVERSED;\
return "cmp%.b %0,%1";\
")

(define\_insn ""\
\[(set (cc0) (compare (ashiftrt:SI (match\_operand:SI 0 "memory\_operand" "m")\
(const\_int 24))\
(match\_operand:QI 1 "general\_operand" "i")))]\
"(GET\_CODE (operands\[1]) == CONST\_INT\
&& ((INTVAL (operands\[1]) + 0x80) & \~0xff) == 0)"\
"\*\
return "cmp%.b %1,%0";\
")\
;; arithmetic shift instructions\
;; We don't need the shift memory by 1 bit instruction

(define\_insn "ashlsi3"\
\[(set (match\_operand:SI 0 "general\_operand" "=d")\
(ashift:SI (match\_operand:SI 1 "general\_operand" "0")\
(match\_operand:SI 2 "general\_operand" "dI")))]\
""\
"asl%.l %2,%0")

(define\_insn "ashlhi3"\
\[(set (match\_operand:HI 0 "general\_operand" "=d")\
(ashift:HI (match\_operand:HI 1 "general\_operand" "0")\
(match\_operand:HI 2 "general\_operand" "dI")))]\
""\
"asl%.w %2,%0")

(define\_insn "ashlqi3"\
\[(set (match\_operand:QI 0 "general\_operand" "=d")\
(ashift:QI (match\_operand:QI 1 "general\_operand" "0")\
(match\_operand:QI 2 "general\_operand" "dI")))]\
""\
"asl%.b %2,%0")

(define\_insn "ashrsi3"\
\[(set (match\_operand:SI 0 "general\_operand" "=d")\
(ashiftrt:SI (match\_operand:SI 1 "general\_operand" "0")\
(match\_operand:SI 2 "general\_operand" "dI")))]\
""\
"asr%.l %2,%0")

(define\_insn "ashrhi3"\
\[(set (match\_operand:HI 0 "general\_operand" "=d")\
(ashiftrt:HI (match\_operand:HI 1 "general\_operand" "0")\
(match\_operand:HI 2 "general\_operand" "dI")))]\
""\
"asr%.w %2,%0")

(define\_insn "ashrqi3"\
\[(set (match\_operand:QI 0 "general\_operand" "=d")\
(ashiftrt:QI (match\_operand:QI 1 "general\_operand" "0")\
(match\_operand:QI 2 "general\_operand" "dI")))]\
""\
"asr%.b %2,%0")\
;; logical shift instructions

(define\_insn "lshlsi3"\
\[(set (match\_operand:SI 0 "general\_operand" "=d")\
(lshift:SI (match\_operand:SI 1 "general\_operand" "0")\
(match\_operand:SI 2 "general\_operand" "dI")))]\
""\
"lsl%.l %2,%0")

(define\_insn "lshlhi3"\
\[(set (match\_operand:HI 0 "general\_operand" "=d")\
(lshift:HI (match\_operand:HI 1 "general\_operand" "0")\
(match\_operand:HI 2 "general\_operand" "dI")))]\
""\
"lsl%.w %2,%0")

(define\_insn "lshlqi3"\
\[(set (match\_operand:QI 0 "general\_operand" "=d")\
(lshift:QI (match\_operand:QI 1 "general\_operand" "0")\
(match\_operand:QI 2 "general\_operand" "dI")))]\
""\
"lsl%.b %2,%0")

(define\_insn "lshrsi3"\
\[(set (match\_operand:SI 0 "general\_operand" "=d")\
(lshiftrt:SI (match\_operand:SI 1 "general\_operand" "0")\
(match\_operand:SI 2 "general\_operand" "dI")))]\
""\
"lsr%.l %2,%0")

(define\_insn "lshrhi3"\
\[(set (match\_operand:HI 0 "general\_operand" "=d")\
(lshiftrt:HI (match\_operand:HI 1 "general\_operand" "0")\
(match\_operand:HI 2 "general\_operand" "dI")))]\
""\
"lsr%.w %2,%0")

(define\_insn "lshrqi3"\
\[(set (match\_operand:QI 0 "general\_operand" "=d")\
(lshiftrt:QI (match\_operand:QI 1 "general\_operand" "0")\
(match\_operand:QI 2 "general\_operand" "dI")))]\
""\
"lsr%.b %2,%0")\
;; rotate instructions

(define\_insn "rotlsi3"\
\[(set (match\_operand:SI 0 "general\_operand" "=d")\
(rotate:SI (match\_operand:SI 1 "general\_operand" "0")\
(match\_operand:SI 2 "general\_operand" "dI")))]\
""\
"rol%.l %2,%0")

(define\_insn "rotlhi3"\
\[(set (match\_operand:HI 0 "general\_operand" "=d")\
(rotate:HI (match\_operand:HI 1 "general\_operand" "0")\
(match\_operand:HI 2 "general\_operand" "dI")))]\
""\
"rol%.w %2,%0")

(define\_insn "rotlqi3"\
\[(set (match\_operand:QI 0 "general\_operand" "=d")\
(rotate:QI (match\_operand:QI 1 "general\_operand" "0")\
(match\_operand:QI 2 "general\_operand" "dI")))]\
""\
"rol%.b %2,%0")

(define\_insn "rotrsi3"\
\[(set (match\_operand:SI 0 "general\_operand" "=d")\
(rotatert:SI (match\_operand:SI 1 "general\_operand" "0")\
(match\_operand:SI 2 "general\_operand" "dI")))]\
""\
"ror%.l %2,%0")

(define\_insn "rotrhi3"\
\[(set (match\_operand:HI 0 "general\_operand" "=d")\
(rotatert:HI (match\_operand:HI 1 "general\_operand" "0")\
(match\_operand:HI 2 "general\_operand" "dI")))]\
""\
"ror%.w %2,%0")

(define\_insn "rotrqi3"\
\[(set (match\_operand:QI 0 "general\_operand" "=d")\
(rotatert:QI (match\_operand:QI 1 "general\_operand" "0")\
(match\_operand:QI 2 "general\_operand" "dI")))]\
""\
"ror%.b %2,%0")\
;; Special cases of bit-field insns which we should\
;; recognize in preference to the general case.\
;; These handle aligned 8-bit and 16-bit fields,\
;; which can usually be done with move instructions.

(define\_insn ""\
\[(set (zero\_extract:SI (match\_operand:SI 0 "nonimmediate\_operand" "+do")\
(match\_operand:SI 1 "immediate\_operand" "i")\
(match\_operand:SI 2 "immediate\_operand" "i"))\
(match\_operand:SI 3 "general\_operand" "d"))]\
"TARGET\_68020 && TARGET\_BITFIELD\
&& GET\_CODE (operands\[1]) == CONST\_INT\
&& (INTVAL (operands\[1]) == 8 || INTVAL (operands\[1]) == 16)\
&& GET\_CODE (operands\[2]) == CONST\_INT\
&& INTVAL (operands\[2]) % INTVAL (operands\[1]) == 0\
&& (GET\_CODE (operands\[0]) == REG\
|| ! mode\_dependent\_address\_p (XEXP (operands\[0], 0)))"\
"\*\
{\
if (REG\_P (operands\[0]))\
{\
if (INTVAL (operands\[1]) + INTVAL (operands\[2]) != 32)\
return "bfins %3,\[%c2,%c1]%0";\
}\
else\
operands\[0]\
\= adj\_offsettable\_operand (operands\[0], INTVAL (operands\[2]) / 8);

if (GET\_CODE (operands\[3]) == MEM)\
operands\[3] = adj\_offsettable\_operand (operands\[3],\
(32 - INTVAL (operands\[1])) / 8);\
if (INTVAL (operands\[1]) == 8)\
return "mov%.b %3,%0";\
return "mov%.w %3,%0";\
}")

(define\_insn ""\
\[(set (match\_operand:SI 0 "general\_operand" "=\&d")\
(zero\_extract:SI (match\_operand:SI 1 "nonimmediate\_operand" "do")\
(match\_operand:SI 2 "immediate\_operand" "i")\
(match\_operand:SI 3 "immediate\_operand" "i")))]\
"TARGET\_68020 && TARGET\_BITFIELD\
&& GET\_CODE (operands\[2]) == CONST\_INT\
&& (INTVAL (operands\[2]) == 8 || INTVAL (operands\[2]) == 16)\
&& GET\_CODE (operands\[3]) == CONST\_INT\
&& INTVAL (operands\[3]) % INTVAL (operands\[2]) == 0\
&& (GET\_CODE (operands\[1]) == REG\
|| ! mode\_dependent\_address\_p (XEXP (operands\[1], 0)))"\
"\*\
{\
if (REG\_P (operands\[1]))\
{\
if (INTVAL (operands\[2]) + INTVAL (operands\[3]) != 32)\
return "bfextu \[%c3,%c2]%1,%0";\
}\
else\
operands\[1]\
\= adj\_offsettable\_operand (operands\[1], INTVAL (operands\[3]) / 8);

output\_asm\_insn ("clrl %0", operands);\
if (GET\_CODE (operands\[0]) == MEM)\
operands\[0] = adj\_offsettable\_operand (operands\[0],\
(32 - INTVAL (operands\[1])) / 8);\
if (INTVAL (operands\[2]) == 8)\
return "mov%.b %1,%0";\
return "mov%.w %1,%0";\
}")

(define\_insn ""\
\[(set (match\_operand:SI 0 "general\_operand" "=d")\
(sign\_extract:SI (match\_operand:SI 1 "nonimmediate\_operand" "do")\
(match\_operand:SI 2 "immediate\_operand" "i")\
(match\_operand:SI 3 "immediate\_operand" "i")))]\
"TARGET\_68020 && TARGET\_BITFIELD\
&& GET\_CODE (operands\[2]) == CONST\_INT\
&& (INTVAL (operands\[2]) == 8 || INTVAL (operands\[2]) == 16)\
&& GET\_CODE (operands\[3]) == CONST\_INT\
&& INTVAL (operands\[3]) % INTVAL (operands\[2]) == 0\
&& (GET\_CODE (operands\[1]) == REG\
|| ! mode\_dependent\_address\_p (XEXP (operands\[1], 0)))"\
"\*\
{\
if (REG\_P (operands\[1]))\
{\
if (INTVAL (operands\[2]) + INTVAL (operands\[3]) != 32)\
return "bfexts \[%c3,%c2]%1,%0";\
}\
else\
operands\[1]\
\= adj\_offsettable\_operand (operands\[1], INTVAL (operands\[3]) / 8);

if (INTVAL (operands\[2]) == 8)\
return "mov%.b %1,%0;extb%.l %0";\
return "mov%.w %1,%0;ext%.l %0";\
}")\
;; Bit field instructions, general cases.\
;; "o,d" constraint causes a nonoffsettable memref to match the "o"\
;; so that its address is reloaded.

(define\_insn "extv"\
\[(set (match\_operand:SI 0 "general\_operand" "=d,d")\
(sign\_extract:SI (match\_operand:QI 1 "nonimmediate\_operand" "o,d")\
(match\_operand:SI 2 "general\_operand" "di,di")\
(match\_operand:SI 3 "general\_operand" "di,di")))]\
"TARGET\_68020 && TARGET\_BITFIELD"\
"bfexts \[%c3,%c2]%1,%0")

(define\_insn "extzv"\
\[(set (match\_operand:SI 0 "general\_operand" "=d,d")\
(zero\_extract:SI (match\_operand:QI 1 "nonimmediate\_operand" "o,d")\
(match\_operand:SI 2 "general\_operand" "di,di")\
(match\_operand:SI 3 "general\_operand" "di,di")))]\
"TARGET\_68020 && TARGET\_BITFIELD"\
"bfextu \[%c3,%c2]%1,%0")

(define\_insn ""\
\[(set (zero\_extract:SI (match\_operand:QI 0 "nonimmediate\_operand" "+o,d")\
(match\_operand:SI 1 "general\_operand" "di,di")\
(match\_operand:SI 2 "general\_operand" "di,di"))\
(xor:SI (zero\_extract:SI (match\_dup 0) (match\_dup 1) (match\_dup 2))\
(match\_operand 3 "immediate\_operand" "i,i")))]\
"TARGET\_68020 && TARGET\_BITFIELD\
&& GET\_CODE (operands\[3]) == CONST\_INT\
&& (INTVAL (operands\[3]) == -1\
|| (GET\_CODE (operands\[1]) == CONST\_INT\
&& (\~ INTVAL (operands\[3]) & ((1 << INTVAL (operands\[1]))- 1)) == 0))"\
"\*\
{\
CC\_STATUS\_INIT;\
return "bfchg \[%c2,%c1]%0";\
}")

(define\_insn ""\
\[(set (zero\_extract:SI (match\_operand:QI 0 "nonimmediate\_operand" "+o,d")\
(match\_operand:SI 1 "general\_operand" "di,di")\
(match\_operand:SI 2 "general\_operand" "di,di"))\
(const\_int 0))]\
"TARGET\_68020 && TARGET\_BITFIELD"\
"\*\
{\
CC\_STATUS\_INIT;\
return "bfclr \[%c2,%c1]%0";\
}")

(define\_insn ""\
\[(set (zero\_extract:SI (match\_operand:QI 0 "nonimmediate\_operand" "+o,d")\
(match\_operand:SI 1 "general\_operand" "di,di")\
(match\_operand:SI 2 "general\_operand" "di,di"))\
(const\_int -1))]\
"TARGET\_68020 && TARGET\_BITFIELD"\
"\*\
{\
CC\_STATUS\_INIT;\
return "bfset \[%c2,%c1]%0";\
}")

(define\_insn "insv"\
\[(set (zero\_extract:SI (match\_operand:QI 0 "nonimmediate\_operand" "+o,d")\
(match\_operand:SI 1 "general\_operand" "di,di")\
(match\_operand:SI 2 "general\_operand" "di,di"))\
(match\_operand:SI 3 "general\_operand" "d,d"))]\
"TARGET\_68020 && TARGET\_BITFIELD"\
"bfins %3,\[%c2,%c1]%0")

;; Now recognize bit field insns that operate on registers\
;; (or at least were intended to do so).

(define\_insn ""\
\[(set (match\_operand:SI 0 "general\_operand" "=d")\
(sign\_extract:SI (match\_operand:SI 1 "nonimmediate\_operand" "d")\
(match\_operand:SI 2 "general\_operand" "di")\
(match\_operand:SI 3 "general\_operand" "di")))]\
"TARGET\_68020 && TARGET\_BITFIELD"\
"bfexts \[%c3,%c2]%1,%0")

(define\_insn ""\
\[(set (match\_operand:SI 0 "general\_operand" "=d")\
(zero\_extract:SI (match\_operand:SI 1 "nonimmediate\_operand" "d")\
(match\_operand:SI 2 "general\_operand" "di")\
(match\_operand:SI 3 "general\_operand" "di")))]\
"TARGET\_68020 && TARGET\_BITFIELD"\
"bfextu \[%c3,%c2]%1,%0")

(define\_insn ""\
\[(set (zero\_extract:SI (match\_operand:SI 0 "nonimmediate\_operand" "+d")\
(match\_operand:SI 1 "general\_operand" "di")\
(match\_operand:SI 2 "general\_operand" "di"))\
(const\_int 0))]\
"TARGET\_68020 && TARGET\_BITFIELD"\
"\*\
{\
CC\_STATUS\_INIT;\
return "bfclr \[%c2,%c1]%0";\
}")

(define\_insn ""\
\[(set (zero\_extract:SI (match\_operand:SI 0 "nonimmediate\_operand" "+d")\
(match\_operand:SI 1 "general\_operand" "di")\
(match\_operand:SI 2 "general\_operand" "di"))\
(const\_int -1))]\
"TARGET\_68020 && TARGET\_BITFIELD"\
"\*\
{\
CC\_STATUS\_INIT;\
return "bfset \[%c2,%c1]%0";\
}")

(define\_insn ""\
\[(set (zero\_extract:SI (match\_operand:SI 0 "nonimmediate\_operand" "+d")\
(match\_operand:SI 1 "general\_operand" "di")\
(match\_operand:SI 2 "general\_operand" "di"))\
(match\_operand:SI 3 "general\_operand" "d"))]\
"TARGET\_68020 && TARGET\_BITFIELD"\
"\*\
{\
return "bfins %3,\[%c2,%c1]%0";\
}")\
;; Special patterns for optimizing bit-field instructions.

(define\_insn ""\
\[(set (cc0)\
(zero\_extract:SI (match\_operand:QI 0 "memory\_operand" "o")\
(match\_operand:SI 1 "general\_operand" "di")\
(match\_operand:SI 2 "general\_operand" "di")))]\
"TARGET\_68020 && TARGET\_BITFIELD\
&& GET\_CODE (operands\[1]) == CONST\_INT"\
"\*\
{\
if (operands\[1] == const1\_rtx\
&& GET\_CODE (operands\[2]) == CONST\_INT)\
{\
int width = GET\_CODE (operands\[0]) == REG ? 31 : 7;\
return output\_btst (operands,\
gen\_rtx (CONST\_INT, VOIDmode,\
width - INTVAL (operands\[2])),\
operands\[0],\
insn, 1000);\
/\* Pass 1000 as SIGNPOS argument so that btst will\
not think we are testing the sign bit for an \`and'\
and assume that nonzero implies a negative result. \*/\
}\
if (INTVAL (operands\[1]) != 32)\
cc\_status.flags = CC\_NOT\_NEGATIVE;\
return "bftst \[%c2,%c1]%0";\
}")

(define\_insn ""\
\[(set (cc0)\
(subreg:QI\
(zero\_extract:SI (match\_operand:QI 0 "memory\_operand" "o")\
(match\_operand:SI 1 "general\_operand" "di")\
(match\_operand:SI 2 "general\_operand" "di"))\
0\))]\
"TARGET\_68020 && TARGET\_BITFIELD\
&& GET\_CODE (operands\[1]) == CONST\_INT"\
"\*\
{\
if (operands\[1] == const1\_rtx\
&& GET\_CODE (operands\[2]) == CONST\_INT)\
{\
int width = GET\_CODE (operands\[0]) == REG ? 31 : 7;\
return output\_btst (operands,\
gen\_rtx (CONST\_INT, VOIDmode,\
width - INTVAL (operands\[2])),\
operands\[0],\
insn, 1000);\
/\* Pass 1000 as SIGNPOS argument so that btst will\
not think we are testing the sign bit for an \`and'\
and assume that nonzero implies a negative result. \*/\
}\
if (INTVAL (operands\[1]) != 32)\
cc\_status.flags = CC\_NOT\_NEGATIVE;\
return "bftst \[%c2,%c1]%0";\
}")

(define\_insn ""\
\[(set (cc0)\
(subreg:HI\
(zero\_extract:SI (match\_operand:QI 0 "memory\_operand" "o")\
(match\_operand:SI 1 "general\_operand" "di")\
(match\_operand:SI 2 "general\_operand" "di"))\
0\))]\
"TARGET\_68020 && TARGET\_BITFIELD\
&& GET\_CODE (operands\[1]) == CONST\_INT"\
"\*\
{\
if (operands\[1] == const1\_rtx\
&& GET\_CODE (operands\[2]) == CONST\_INT)\
{\
int width = GET\_CODE (operands\[0]) == REG ? 31 : 7;\
return output\_btst (operands,\
gen\_rtx (CONST\_INT, VOIDmode,\
width - INTVAL (operands\[2])),\
operands\[0],\
insn, 1000);\
/\* Pass 1000 as SIGNPOS argument so that btst will\
not think we are testing the sign bit for an \`and'\
and assume that nonzero implies a negative result. \*/\
}\
if (INTVAL (operands\[1]) != 32)\
cc\_status.flags = CC\_NOT\_NEGATIVE;\
return "bftst \[%c2,%c1]%0";\
}")

;;; now handle the register cases\
(define\_insn ""\
\[(set (cc0)\
(zero\_extract:SI (match\_operand:SI 0 "nonimmediate\_operand" "d")\
(match\_operand:SI 1 "general\_operand" "di")\
(match\_operand:SI 2 "general\_operand" "di")))]\
"TARGET\_68020 && TARGET\_BITFIELD\
&& GET\_CODE (operands\[1]) == CONST\_INT"\
"\*\
{\
if (operands\[1] == const1\_rtx\
&& GET\_CODE (operands\[2]) == CONST\_INT)\
{\
int width = GET\_CODE (operands\[0]) == REG ? 31 : 7;\
return output\_btst (operands,\
gen\_rtx (CONST\_INT, VOIDmode,\
width - INTVAL (operands\[2])),\
operands\[0],\
insn, 1000);\
/\* Pass 1000 as SIGNPOS argument so that btst will\
not think we are testing the sign bit for an \`and'\
and assume that nonzero implies a negative result. \*/\
}\
if (INTVAL (operands\[1]) != 32)\
cc\_status.flags = CC\_NOT\_NEGATIVE;\
return "bftst \[%c2,%c1]%0";\
}")

(define\_insn ""\
\[(set (cc0)\
(subreg:QI\
(zero\_extract:SI (match\_operand:SI 0 "nonimmediate\_operand" "d")\
(match\_operand:SI 1 "general\_operand" "di")\
(match\_operand:SI 2 "general\_operand" "di"))\
0\))]\
"TARGET\_68020 && TARGET\_BITFIELD\
&& GET\_CODE (operands\[1]) == CONST\_INT"\
"\*\
{\
if (operands\[1] == const1\_rtx\
&& GET\_CODE (operands\[2]) == CONST\_INT)\
{\
int width = GET\_CODE (operands\[0]) == REG ? 31 : 7;\
return output\_btst (operands,\
gen\_rtx (CONST\_INT, VOIDmode,\
width - INTVAL (operands\[2])),\
operands\[0],\
insn, 1000);\
/\* Pass 1000 as SIGNPOS argument so that btst will\
not think we are testing the sign bit for an \`and'\
and assume that nonzero implies a negative result. \*/\
}\
if (INTVAL (operands\[1]) != 32)\
cc\_status.flags = CC\_NOT\_NEGATIVE;\
return "bftst \[%c2,%c1]%0";\
}")

(define\_insn ""\
\[(set (cc0)\
(subreg:HI\
(zero\_extract:SI (match\_operand:SI 0 "nonimmediate\_operand" "d")\
(match\_operand:SI 1 "general\_operand" "di")\
(match\_operand:SI 2 "general\_operand" "di"))\
0\))]\
"TARGET\_68020 && TARGET\_BITFIELD\
&& GET\_CODE (operands\[1]) == CONST\_INT"\
"\*\
{\
if (operands\[1] == const1\_rtx\
&& GET\_CODE (operands\[2]) == CONST\_INT)\
{\
int width = GET\_CODE (operands\[0]) == REG ? 31 : 7;\
return output\_btst (operands,\
gen\_rtx (CONST\_INT, VOIDmode,\
width - INTVAL (operands\[2])),\
operands\[0],\
insn, 1000);\
/\* Pass 1000 as SIGNPOS argument so that btst will\
not think we are testing the sign bit for an \`and'\
and assume that nonzero implies a negative result. _/_\
_}_\
_if (INTVAL (operands\[1]) != 32)_\
_cc\_status.flags = CC\_NOT\_NEGATIVE;_\
_return "bftst \[%c2,%c1]%0";_\
_}")_\
_(define\_insn "seq"_\
_\[(set (match\_operand:QI 0 "general\_operand" "=d")_\
_(eq (cc0) (const\_int 0)))]_\
_""_\
_"_\
cc\_status = cc\_prev\_status;\
OUTPUT\_JUMP ("seq %0", "fseq %0", "seq %0");\
")

(define\_insn "sne"\
\[(set (match\_operand:QI 0 "general\_operand" "=d")\
(ne (cc0) (const\_int 0)))]\
""\
"\*\
cc\_status = cc\_prev\_status;\
OUTPUT\_JUMP ("sne %0", "fsneq %0", "sne %0");\
")

(define\_insn "sgt"\
\[(set (match\_operand:QI 0 "general\_operand" "=d")\
(gt (cc0) (const\_int 0)))]\
""\
"\*\
cc\_status = cc\_prev\_status;\
OUTPUT\_JUMP ("sgt %0", "fsgt %0", "and%.b %#0xc,%!;sgt %0");\
")

(define\_insn "sgtu"\
\[(set (match\_operand:QI 0 "general\_operand" "=d")\
(gtu (cc0) (const\_int 0)))]\
""\
"\* cc\_status = cc\_prev\_status;\
return "shi %0"; ")

(define\_insn "slt"\
\[(set (match\_operand:QI 0 "general\_operand" "=d")\
(lt (cc0) (const\_int 0)))]\
""\
"\* cc\_status = cc\_prev\_status;\
OUTPUT\_JUMP ("slt %0", "fslt %0", "smi %0"); ")

(define\_insn "sltu"\
\[(set (match\_operand:QI 0 "general\_operand" "=d")\
(ltu (cc0) (const\_int 0)))]\
""\
"\* cc\_status = cc\_prev\_status;\
return "scs %0"; ")

(define\_insn "sge"\
\[(set (match\_operand:QI 0 "general\_operand" "=d")\
(ge (cc0) (const\_int 0)))]\
""\
"\* cc\_status = cc\_prev\_status;\
OUTPUT\_JUMP ("sge %0", "fsge %0", "spl %0"); ")

(define\_insn "sgeu"\
\[(set (match\_operand:QI 0 "general\_operand" "=d")\
(geu (cc0) (const\_int 0)))]\
""\
"\* cc\_status = cc\_prev\_status;\
return "scc %0"; ")

(define\_insn "sle"\
\[(set (match\_operand:QI 0 "general\_operand" "=d")\
(le (cc0) (const\_int 0)))]\
""\
"\*\
cc\_status = cc\_prev\_status;\
OUTPUT\_JUMP ("sle %0", "fsle %0", "and%.b %#0xc,%!;sle %0");\
")

(define\_insn "sleu"\
\[(set (match\_operand:QI 0 "general\_operand" "=d")\
(leu (cc0) (const\_int 0)))]\
""\
"\* cc\_status = cc\_prev\_status;\
return "sls %0"; ")\
;; Basic conditional jump instructions.

(define\_insn "beq"\
\[(set (pc)\
(if\_then\_else (eq (cc0)\
(const\_int 0))\
(label\_ref (match\_operand 0 "" ""))\
(pc)))]\
""\
"\*\
{\
OUTPUT\_JUMP ("jeq %l0", "fbeq %l0", "jeq %l0");\
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
OUTPUT\_JUMP ("jne %l0", "fbneq %l0", "jne %l0");\
}")

(define\_insn "bgt"\
\[(set (pc)\
(if\_then\_else (gt (cc0)\
(const\_int 0))\
(label\_ref (match\_operand 0 "" ""))\
(pc)))]\
""\
"\*\
OUTPUT\_JUMP ("jgt %l0", "fbgt %l0", "and%.b %#0xc,%!;jgt %l0");\
")

(define\_insn "bgtu"\
\[(set (pc)\
(if\_then\_else (gtu (cc0)\
(const\_int 0))\
(label\_ref (match\_operand 0 "" ""))\
(pc)))]\
""\
"\*\
return "jhi %l0";\
")

(define\_insn "blt"\
\[(set (pc)\
(if\_then\_else (lt (cc0)\
(const\_int 0))\
(label\_ref (match\_operand 0 "" ""))\
(pc)))]\
""\
"\*\
OUTPUT\_JUMP ("jlt %l0", "fblt %l0", "jmi %l0");\
")

(define\_insn "bltu"\
\[(set (pc)\
(if\_then\_else (ltu (cc0)\
(const\_int 0))\
(label\_ref (match\_operand 0 "" ""))\
(pc)))]\
""\
"\*\
return "jcs %l0";\
")

(define\_insn "bge"\
\[(set (pc)\
(if\_then\_else (ge (cc0)\
(const\_int 0))\
(label\_ref (match\_operand 0 "" ""))\
(pc)))]\
""\
"\*\
OUTPUT\_JUMP ("jge %l0", "fbge %l0", "jpl %l0");\
")

(define\_insn "bgeu"\
\[(set (pc)\
(if\_then\_else (geu (cc0)\
(const\_int 0))\
(label\_ref (match\_operand 0 "" ""))\
(pc)))]\
""\
"\*\
return "jcc %l0";\
")

(define\_insn "ble"\
\[(set (pc)\
(if\_then\_else (le (cc0)\
(const\_int 0))\
(label\_ref (match\_operand 0 "" ""))\
(pc)))]\
""\
"\*\
OUTPUT\_JUMP ("jle %l0", "fble %l0", "and%.b %#0xc,%!;jle %l0");\
")

(define\_insn "bleu"\
\[(set (pc)\
(if\_then\_else (leu (cc0)\
(const\_int 0))\
(label\_ref (match\_operand 0 "" ""))\
(pc)))]\
""\
"\*\
return "jls %l0";\
")\
;; Negated conditional jump instructions.

(define\_insn ""\
\[(set (pc)\
(if\_then\_else (eq (cc0)\
(const\_int 0))\
(pc)\
(label\_ref (match\_operand 0 "" ""))))]\
""\
"\*\
{\
OUTPUT\_JUMP ("jne %l0", "fbneq %l0", "jne %l0");\
}")

(define\_insn ""\
\[(set (pc)\
(if\_then\_else (ne (cc0)\
(const\_int 0))\
(pc)\
(label\_ref (match\_operand 0 "" ""))))]\
""\
"\*\
{\
OUTPUT\_JUMP ("jeq %l0", "fbeq %l0", "jeq %l0");\
}")

(define\_insn ""\
\[(set (pc)\
(if\_then\_else (gt (cc0)\
(const\_int 0))\
(pc)\
(label\_ref (match\_operand 0 "" ""))))]\
""\
"\*\
OUTPUT\_JUMP ("jle %l0", "fbngt %l0", "and%.b %#0xc,%!;jle %l0");\
")

(define\_insn ""\
\[(set (pc)\
(if\_then\_else (gtu (cc0)\
(const\_int 0))\
(pc)\
(label\_ref (match\_operand 0 "" ""))))]\
""\
"\*\
return "jls %l0";\
")

(define\_insn ""\
\[(set (pc)\
(if\_then\_else (lt (cc0)\
(const\_int 0))\
(pc)\
(label\_ref (match\_operand 0 "" ""))))]\
""\
"\*\
OUTPUT\_JUMP ("jge %l0", "fbnlt %l0", "jpl %l0");\
")

(define\_insn ""\
\[(set (pc)\
(if\_then\_else (ltu (cc0)\
(const\_int 0))\
(pc)\
(label\_ref (match\_operand 0 "" ""))))]\
""\
"\*\
return "jcc %l0";\
")

(define\_insn ""\
\[(set (pc)\
(if\_then\_else (ge (cc0)\
(const\_int 0))\
(pc)\
(label\_ref (match\_operand 0 "" ""))))]\
""\
"\*\
OUTPUT\_JUMP ("jlt %l0", "fbnge %l0", "jmi %l0");\
")

(define\_insn ""\
\[(set (pc)\
(if\_then\_else (geu (cc0)\
(const\_int 0))\
(pc)\
(label\_ref (match\_operand 0 "" ""))))]\
""\
"\*\
return "jcs %l0";\
")

(define\_insn ""\
\[(set (pc)\
(if\_then\_else (le (cc0)\
(const\_int 0))\
(pc)\
(label\_ref (match\_operand 0 "" ""))))]\
""\
"\*\
OUTPUT\_JUMP ("jgt %l0", "fbnle %l0", "and%.b %#0xc,%!;jgt %l0");\
")

(define\_insn ""\
\[(set (pc)\
(if\_then\_else (leu (cc0)\
(const\_int 0))\
(pc)\
(label\_ref (match\_operand 0 "" ""))))]\
""\
"\*\
return "jhi %l0";\
")\
;; Subroutines of "casesi".

(define\_expand "casesi\_1"\
\[(set (match\_operand:SI 3 "general\_operand" "")\
(plus:SI (match\_operand:SI 0 "general\_operand" "")\
;; Note operand 1 has been negated!\
(match\_operand:SI 1 "immediate\_operand" "")))\
(set (cc0) (compare (match\_operand:SI 2 "general\_operand" "")\
(match\_dup 3)))\
(set (pc) (if\_then\_else (ltu (cc0) (const\_int 0))\
(label\_ref (match\_operand 4 "" "")) (pc)))]\
""\
"")

(define\_expand "casesi\_2"\
\[(set (match\_operand:SI 0 "" "") (mem:HI (match\_operand:SI 1 "" "")))\
;; The USE here is so that at least one jump-insn will refer to the label,\
;; to keep it alive in jump\_optimize.\
(parallel \[(set (pc)\
(plus:SI (pc) (match\_dup 0)))\
(use (label\_ref (match\_operand 2 "" "")))])]\
""\
"")

;; Operand 0 is index (in bytes); operand 1 is minimum, operand 2 the maximum;\
;; operand 3 is CODE\_LABEL for the table;\
;; operand 4 is the CODE\_LABEL to go to if index out of range.\
(define\_expand "casesi"\
;; We don't use these for generating the RTL, but we must describe\
;; the operands here.\
\[(match\_operand:SI 0 "general\_operand" "")\
(match\_operand:SI 1 "immediate\_operand" "")\
(match\_operand:SI 2 "general\_operand" "")\
(match\_operand 3 "" "")\
(match\_operand 4 "" "")]\
""\
"\
{\
rtx table\_elt\_addr;\
rtx index\_diff;

operands\[1] = negate\_rtx (SImode, operands\[1]);\
index\_diff = gen\_reg\_rtx (SImode);\
/\* Emit the first few insns. _/_\
_emit\_insn (gen\_casesi\_1 (operands\[0], operands\[1], operands\[2],_\
_index\_diff, operands\[4]));_\
_/_ Construct a memory address. This may emit some insns. _/_\
_table\_elt\_addr_\
_= memory\_address\_noforce_\
_(HImode,_\
_gen\_rtx (PLUS, Pmode,_\
_gen\_rtx (MULT, Pmode, index\_diff,_\
_gen\_rtx (CONST\_INT, VOIDmode, 2)),_\
_gen\_rtx (LABEL\_REF, VOIDmode, operands\[3])));_\
_/_ Emit the last few insns. \*/\
emit\_insn (gen\_casesi\_2 (gen\_reg\_rtx (HImode), table\_elt\_addr, operands\[3]));\
DONE;\
}")

;; Recognize one of the insns resulting from casesi\_2.\
(define\_insn ""\
\[(set (pc)\
(plus:SI (pc) (match\_operand:HI 0 "general\_operand" "r")))\
(use (label\_ref (match\_operand 1 "" "")))]\
""\
"\*\
return "jmp pc@(2:B)\[%0:W:B]";\
")\
;; Unconditional and other jump instructions\
(define\_insn "jump"\
\[(set (pc)\
(label\_ref (match\_operand 0 "" "")))]\
""\
"\*\
return "jra %l0";\
")

(define\_insn ""\
\[(set (pc)\
(if\_then\_else\
(ne (compare (plus:HI (match\_operand:HI 0 "general\_operand" "g")\
(const\_int -1))\
(const\_int -1))\
(const\_int 0))\
(label\_ref (match\_operand 1 "" ""))\
(pc)))\
(set (match\_dup 0)\
(plus:HI (match\_dup 0)\
(const\_int -1)))]\
""\
"\*\
{\
if (DATA\_REG\_P (operands\[0]))\
return "dbra %0,%l1";\
if (GET\_CODE (operands\[0]) == MEM)\
{\
return "subq%.w %#1,%0;jcc %l1";\
}\
return "subq%.w %#1,%0;cmp%.w %#-1,%0;jne %l1";\
}")

(define\_insn ""\
\[(set (pc)\
(if\_then\_else\
(ne (compare (plus:SI (match\_operand:SI 0 "general\_operand" "g")\
(const\_int -1))\
(const\_int -1))\
(const\_int 0))\
(label\_ref (match\_operand 1 "" ""))\
(pc)))\
(set (match\_dup 0)\
(plus:SI (match\_dup 0)\
(const\_int -1)))]\
""\
"\*\
{\
if (DATA\_REG\_P (operands\[0]))\
return "dbra %0,%l1;clr%.w %0;subq%.l %#1,%0;jcc %l1";\
if (GET\_CODE (operands\[0]) == MEM)\
return "subq%.l %#1,%0;jcc %l1";\
return "subq%.l %#1,%0;cmp%.l %#-1,%0;jne %l1";\
}")

;; dbra patterns that use REG\_NOTES info generated by strength\_reduce.

(define\_insn ""\
\[(set (pc)\
(if\_then\_else\
(ge (plus:SI (match\_operand:SI 0 "general\_operand" "g")\
(const\_int -1))\
(const\_int 0))\
(label\_ref (match\_operand 1 "" ""))\
(pc)))\
(set (match\_dup 0)\
(plus:SI (match\_dup 0)\
(const\_int -1)))]\
"find\_reg\_note (insn, REG\_NONNEG, 0)"\
"\*\
{\
if (DATA\_REG\_P (operands\[0]))\
return "dbra %0,%l1;clrw %0;subql %#1,%0;jcc %l1";\
if (GET\_CODE (operands\[0]) == MEM)\
return "subq%.l %#1,%0;jcc %l1";\
return "subq%.l %#1,%0;cmp%.l %#-1,%0;jne %l1";\
}")

;; Call subroutine with no return value.\
(define\_insn "call"\
\[(call (match\_operand:QI 0 "general\_operand" "o")\
(match\_operand:SI 1 "general\_operand" "g"))]\
""\
"\*\
{\
rtx xoperands\[2];\
int size = XINT(operands\[1],0);

if (size == 0)\
output\_asm\_insn ("sub%.l a0,a0;jbsr %0", operands);\
else\
{\
xoperands\[1] = gen\_rtx (CONST\_INT, VOIDmode, size/4);\
output\_asm\_insn ("mov%.l sp,a0;pea %a1", xoperands);\
output\_asm\_insn ("jbsr %0", operands);\
size = size + 4;\
xoperands\[1] = gen\_rtx (CONST\_INT, VOIDmode, size);\
if (size <= 8)\
output\_asm\_insn ("addq%.l %1,sp", xoperands);\
else if (size < 0x8000)\
output\_asm\_insn ("add%.w %1,sp", xoperands);\
else\
output\_asm\_insn ("add%.l %1,sp", xoperands);\
}\
return "mov%.l a6@(-4),a0";\
}")

;; Call subroutine, returning value in operand 0\
;; (which must be a hard register).\
(define\_insn "call\_value"\
\[(set (match\_operand 0 "" "=rf")\
(call (match\_operand:QI 1 "general\_operand" "o")\
(match\_operand:SI 2 "general\_operand" "g")))]\
""\
"\*\
{\
rtx xoperands\[3];\
int size = XINT(operands\[2],0);

if (size == 0)\
output\_asm\_insn("sub%.l a0,a0;jbsr %1", operands);\
else\
{\
xoperands\[2] = gen\_rtx (CONST\_INT, VOIDmode, size/4);\
output\_asm\_insn ("mov%.l sp,a0;pea %a2", xoperands);\
output\_asm\_insn ("jbsr %1", operands);\
size = size + 4;\
xoperands\[2] = gen\_rtx (CONST\_INT, VOIDmode, size);\
if (size <= 8)\
output\_asm\_insn ("addq%.l %2,sp", xoperands);\
else if (size < 0x8000)\
output\_asm\_insn ("add%.w %2,sp", xoperands);\
else\
output\_asm\_insn ("add%.l %2,sp", xoperands);\
}\
return "mov%.l a6@(-4),a0";\
}")

(define\_insn "nop"\
\[(const\_int 0)]\
""\
"nop")\
;; This should not be used unless the add/sub insns can't be.

(define\_insn ""\
\[(set (match\_operand:SI 0 "general\_operand" "=a")\
(match\_operand:QI 1 "address\_operand" "p"))]\
""\
"lea %a1,%0")\
;; This is the first machine-dependent peephole optimization.\
;; It is useful when a floating value is returned from a function call\
;; and then is moved into an FP register.\
;; But it is mainly intended to test the support for these optimizations.

;Not applicable to Alliant -- floating results are returned in fp0\
;(define\_peephole\
; \[(set (reg:SI 15) (plus:SI (reg:SI 15) (const\_int 4)))\
; (set (match\_operand:DF 0 "register\_operand" "f")\
; (match\_operand:DF 1 "register\_operand" "ad"))]\
; "FP\_REG\_P (operands\[0]) && ! FP\_REG\_P (operands\[1])"\
; "\*\
;{\
; rtx xoperands\[2];\
; xoperands\[1] = gen\_rtx (REG, SImode, REGNO (operands\[1]) + 1);\
; output\_asm\_insn ("mov%.l %1,%@", xoperands);\
; output\_asm\_insn ("mov%.l %1,%-", operands);\
; return "fmove%.d %+,%0";\
;}\
;")

;;- Local variables:\
;;- mode:emacs-lisp\
;;- comment-start: ";;- "\
;;- comment-start-skip: ";+- \*"\
;;- eval: (set-syntax-table (copy-sequence (syntax-table)))\
;;- eval: (modify-syntax-entry ?\[ "(]")\
;;- eval: (modify-syntax-entry ?] ")\[")\
;;- eval: (modify-syntax-entry ?{ "(}")\
;;- eval: (modify-syntax-entry ?} "){")\
;;- End:
