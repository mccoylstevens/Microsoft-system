# convex

;;- Machine description for GNU compiler\
;;- Convex Version\
;; Copyright (C) 1989 Free Software Foundation, Inc.

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

;; Put tstsi first among test insns so it matches a CONST\_INT operand.

(define\_insn "tstsi"\
\[(set (cc0)\
(match\_operand:SI 0 "register\_operand" "r"))]\
""\
"\* return set\_cmp (operands\[0], const0\_rtx, 'w');")

(define\_insn "tsthi"\
\[(set (cc0)\
(match\_operand:HI 0 "register\_operand" "r"))]\
""\
"\* return set\_cmp (operands\[0], const0\_rtx, 'h');")

(define\_expand "tstqi"\
\[(set (match\_dup 1)\
(sign\_extend:SI (match\_operand:QI 0 "register\_operand" "r")))\
(set (cc0)\
(match\_dup 1))]\
""\
"operands\[1] = gen\_reg\_rtx (SImode);")

(define\_insn "tstdi"\
\[(set (cc0)\
(match\_operand:DI 0 "register\_operand" "d"))\
(clobber (reg:DI 1))]\
""\
"\*\
{\
output\_asm\_insn ("ld.l #0,s1");\
return set\_cmp (operands\[0], gen\_rtx (REG, DImode, 1), 'l');\
}")

(define\_expand "tstdf"\
\[(set (cc0)\
(compare (match\_operand:DF 0 "register\_operand" "d")\
(match\_dup 1)))]\
""\
"operands\[1] = force\_reg (DFmode, dconst0\_rtx);")

(define\_insn "tstsf"\
\[(set (cc0)\
(match\_operand:SF 0 "register\_operand" "d"))]\
""\
"\* return set\_cmp (operands\[0], fconst0\_rtx, 's');")

;; Put cmpsi first among compare insns so it matches two CONST\_INT operands.

(define\_insn "cmpsi"\
\[(set (cc0)\
(compare (match\_operand:SI 0 "nonmemory\_operand" "d,a,i,r")\
(match\_operand:SI 1 "nonmemory\_operand" "d,a,r,i")))]\
""\
"\* return set\_cmp (operands\[0], operands\[1], 'w');")

(define\_insn "cmphi"\
\[(set (cc0)\
(compare (match\_operand:HI 0 "nonmemory\_operand" "d,a,r,i")\
(match\_operand:HI 1 "nonmemory\_operand" "d,a,i,r")))]\
""\
"\* return set\_cmp (operands\[0], operands\[1], 'h');")

(define\_insn ""\
\[(set (cc0)\
(compare (sign\_extend:SI (match\_operand:QI 0 "register\_operand" "d"))\
(sign\_extend:SI (match\_operand:QI 1 "register\_operand" "d"))))]\
""\
"\* return set\_cmp (operands\[0], operands\[1], 'b');")

(define\_insn "cmpdi"\
\[(set (cc0)\
(compare (match\_operand:DI 0 "register\_operand" "d")\
(match\_operand:DI 1 "register\_operand" "d")))]\
""\
"\* return set\_cmp (operands\[0], operands\[1], 'l');")

(define\_insn "cmpdf"\
\[(set (cc0)\
(compare (match\_operand:DF 0 "register\_operand" "d")\
(match\_operand:DF 1 "register\_operand" "d")))]\
""\
"\* return set\_cmp (operands\[0], operands\[1], 'd');")

(define\_insn "cmpsf"\
\[(set (cc0)\
(compare (match\_operand:SF 0 "nonmemory\_operand" "dF,d")\
(match\_operand:SF 1 "nonmemory\_operand" "d,F")))]\
""\
"\* return set\_cmp (operands\[0], operands\[1], 's');")\
(define\_insn "movdf"\
\[(set (match\_operand:DF 0 "general\_operand" "=g,d")\
(match\_operand:DF 1 "general\_operand" "d,gG"))]\
""\
"\*\
{\
if (push\_operand (operands\[0], DFmode))\
return "psh.l %1";\
else if (GET\_CODE (operands\[0]) == MEM)\
return "st.l %1,%0";\
else if (GET\_CODE (operands\[1]) == REG)\
return "mov %1,%0";\
else if (GET\_CODE (operands\[1]) == CONST\_DOUBLE && LD\_D\_P (operands\[1]))\
{\
operands\[1] = gen\_rtx (CONST\_INT, VOIDmode,\
const\_double\_high\_int (operands\[1]));\
return "ld.d %1,%0";\
}\
else\
return "ld.l %1,%0";\
}")

(define\_insn "movsf"\
\[(set (match\_operand:SF 0 "general\_operand" "=g,d")\
(match\_operand:SF 1 "general\_operand" "d,gF"))]\
""\
"\*\
{\
if (push\_operand (operands\[0], SFmode))\
return "psh.w %1";\
else if (GET\_CODE (operands\[0]) == MEM)\
return "st.s %1,%0";\
else if (GET\_CODE (operands\[1]) == REG)\
return "mov.s %1,%0";\
else\
return "ld.s %1,%0";\
}")

(define\_insn "movdi"\
\[(set (match\_operand:DI 0 "general\_operand" "=g,d")\
(match\_operand:DI 1 "general\_operand" "d,gG"))]\
""\
"\*\
{\
if (push\_operand (operands\[0], DImode))\
return "psh.l %1";\
else if (GET\_CODE (operands\[0]) == MEM)\
return "st.l %1,%0";\
else if (GET\_CODE (operands\[1]) == REG)\
return "mov %1,%0";\
else if (GET\_CODE (operands\[1]) == CONST\_DOUBLE && LD\_D\_P (operands\[1]))\
{\
operands\[1] = gen\_rtx (CONST\_INT, VOIDmode,\
const\_double\_high\_int (operands\[1]));\
return "ld.d %1,%0";\
}\
else\
return "ld.l %1,%0";\
}")

;; Special case of movsi, needed to express A-reg preference.

(define\_insn ""\
\[(set (match\_operand:SI 0 "push\_operand" "=<")\
(plus:SI (match\_operand:SI 1 "register\_operand" "a")\
(match\_operand:SI 2 "immediate\_operand" "i")))]\
"operands\[1] != stack\_pointer\_rtx"\
"pshea %a2(%1)")

(define\_insn "movsi"\
\[(set (match\_operand:SI 0 "general\_operand" "=g,r,<")\
(match\_operand:SI 1 "general\_operand" "r,g,io"))]\
""\
"\*\
{\
if (push\_operand (operands\[0], SImode))\
{\
if (GET\_CODE (operands\[1]) == REG)\
return "psh.w %1";\
else\
return "pshea %a1";\
}\
if (GET\_CODE (operands\[0]) == MEM)\
return "st.w %1,%0";\
if (GET\_CODE (operands\[1]) != REG)\
return "ld.w %1,%0";\
if (S\_REG\_P (operands\[0]) && S\_REG\_P (operands\[1]))\
return "mov.w %1,%0";\
return "mov %1,%0";\
}")

(define\_insn "movhi"\
\[(set (match\_operand:HI 0 "general\_operand" "=g,r")\
(match\_operand:HI 1 "general\_operand" "r,g"))]\
""\
"\*\
{\
if (push\_operand (operands\[0], HImode))\
abort ();\
else if (GET\_CODE (operands\[0]) == MEM)\
return "st.h %1,%0";\
else if (GET\_CODE (operands\[1]) == REG)\
{\
if (S\_REG\_P (operands\[0]) && S\_REG\_P (operands\[1]))\
return "mov.w %1,%0";\
else\
return "mov %1,%0";\
}\
else if (GET\_CODE (operands\[1]) == CONST\_INT)\
return "ld.w %1,%0";\
else\
return "ld.h %1,%0";\
}")

(define\_insn "movqi"\
\[(set (match\_operand:QI 0 "general\_operand" "=g,r")\
(match\_operand:QI 1 "general\_operand" "r,g"))]\
""\
"\*\
{\
if (push\_operand (operands\[0], QImode))\
abort ();\
else if (GET\_CODE (operands\[0]) == MEM)\
return "st.b %1,%0";\
else if (GET\_CODE (operands\[1]) == REG)\
{\
if (S\_REG\_P (operands\[0]) && S\_REG\_P (operands\[1]))\
return "mov.w %1,%0";\
else\
return "mov %1,%0";\
}\
else if (GET\_CODE (operands\[1]) == CONST\_INT)\
return "ld.w %1,%0";\
else\
return "ld.b %1,%0";\
}")\
;; Extension and truncation insns.\
;; Those for integer source operand\
;; are ordered widest source type first.

(define\_insn "truncsiqi2"\
\[(set (match\_operand:QI 0 "register\_operand" "=d,a")\
(truncate:QI (match\_operand:SI 1 "register\_operand" "d,a")))]\
""\
"cvtw.b %1,%0")

(define\_insn "truncsihi2"\
\[(set (match\_operand:HI 0 "register\_operand" "=d,a")\
(truncate:HI (match\_operand:SI 1 "register\_operand" "d,a")))]\
""\
"cvtw.h %1,%0")

(define\_insn "trunchiqi2"\
\[(set (match\_operand:QI 0 "register\_operand" "=r")\
(truncate:QI (match\_operand:HI 1 "register\_operand" "0")))]\
""\
"")

(define\_insn "truncdisi2"\
\[(set (match\_operand:SI 0 "register\_operand" "=d")\
(truncate:SI (match\_operand:DI 1 "register\_operand" "d")))]\
""\
"cvtl.w %1,%0")

(define\_insn "extendsidi2"\
\[(set (match\_operand:DI 0 "register\_operand" "=d")\
(sign\_extend:DI (match\_operand:SI 1 "register\_operand" "d")))]\
""\
"cvtw.l %1,%0")

(define\_insn "extendhisi2"\
\[(set (match\_operand:SI 0 "register\_operand" "=d,a")\
(sign\_extend:SI (match\_operand:HI 1 "register\_operand" "d,a")))]\
""\
"cvth.w %1,%0")

(define\_insn "extendqihi2"\
\[(set (match\_operand:HI 0 "register\_operand" "=d,a")\
(sign\_extend:HI (match\_operand:QI 1 "register\_operand" "d,a")))]\
""\
"cvtb.w %1,%0")

(define\_insn "extendqisi2"\
\[(set (match\_operand:SI 0 "register\_operand" "=d,a")\
(sign\_extend:SI (match\_operand:QI 1 "register\_operand" "d,a")))]\
""\
"cvtb.w %1,%0")

(define\_insn "extendsfdf2"\
\[(set (match\_operand:DF 0 "register\_operand" "=d")\
(float\_extend:DF (match\_operand:SF 1 "register\_operand" "d")))]\
""\
"cvts.d %1,%0")

(define\_insn "truncdfsf2"\
\[(set (match\_operand:SF 0 "register\_operand" "=d")\
(float\_truncate:SF (match\_operand:DF 1 "register\_operand" "d")))]\
""\
"cvtd.s %1,%0")

(define\_insn "zero\_extendhisi2"\
\[(set (match\_operand:SI 0 "register\_operand" "=r")\
(zero\_extend:SI (match\_operand:HI 1 "register\_operand" "0")))]\
""\
"and #0xffff,%0")

(define\_insn "zero\_extendqihi2"\
\[(set (match\_operand:HI 0 "register\_operand" "=r")\
(zero\_extend:HI (match\_operand:QI 1 "register\_operand" "0")))]\
""\
"and #0xff,%0")

(define\_insn "zero\_extendqisi2"\
\[(set (match\_operand:SI 0 "register\_operand" "=r")\
(zero\_extend:SI (match\_operand:QI 1 "register\_operand" "0")))]\
""\
"and #0xff,%0")

(define\_insn "zero\_extendsidi2"\
\[(set (match\_operand:DI 0 "register\_operand" "=d")\
(zero\_extend:DI (match\_operand:SI 1 "register\_operand" "0")))]\
""\
"ld.u #0,%0")\
;; Fix-to-float conversion insns.\
;; Note that the ones that start with SImode come first.\
;; That is so that an operand that is a CONST\_INT\
;; (and therefore lacks a specific machine mode).\
;; will be recognized as SImode (which is always valid)\
;; rather than as QImode or HImode.

(define\_insn "floatsisf2"\
\[(set (match\_operand:SF 0 "register\_operand" "=d")\
(float:SF (match\_operand:SI 1 "register\_operand" "d")))]\
""\
"cvtw.s %1,%0")

(define\_insn "floatdisf2"\
\[(set (match\_operand:SF 0 "register\_operand" "=d")\
(float:SF (match\_operand:DI 1 "register\_operand" "d")))]\
""\
"cvtl.s %1,%0")

(define\_insn "floatsidf2"\
\[(set (match\_operand:DF 0 "register\_operand" "=d")\
(float:DF (match\_operand:SI 1 "register\_operand" "d")))]\
"TARGET\_C2"\
"cvtw.d %1,%0")

(define\_insn "floatdidf2"\
\[(set (match\_operand:DF 0 "register\_operand" "=d")\
(float:DF (match\_operand:DI 1 "register\_operand" "d")))]\
""\
"cvtl.d %1,%0")\
;; Float-to-fix conversion insns.

(define\_insn "fix\_truncsfsi2"\
\[(set (match\_operand:SI 0 "register\_operand" "=d")\
(fix:SI (fix:SF (match\_operand:SF 1 "register\_operand" "d"))))]\
""\
"cvts.w %1,%0")

(define\_insn "fix\_truncsfdi2"\
\[(set (match\_operand:DI 0 "register\_operand" "=d")\
(fix:DI (fix:SF (match\_operand:SF 1 "register\_operand" "d"))))]\
""\
"cvts.l %1,%0")

(define\_insn "fix\_truncdfsi2"\
\[(set (match\_operand:SI 0 "register\_operand" "=d")\
(fix:SI (fix:DF (match\_operand:DF 1 "register\_operand" "d"))))]\
""\
"\*\
{\
if (TARGET\_C2)\
return "cvtd.w %1,%0";\
return "cvtd.l %1,%0";\
}")

(define\_insn "fix\_truncdfdi2"\
\[(set (match\_operand:DI 0 "register\_operand" "=d")\
(fix:DI (fix:DF (match\_operand:DF 1 "register\_operand" "d"))))]\
""\
"cvtd.l %1,%0")\
;;- All kinds of add instructions.

(define\_insn "adddf3"\
\[(set (match\_operand:DF 0 "register\_operand" "=d")\
(plus:DF (match\_operand:DF 1 "register\_operand" "%0")\
(match\_operand:DF 2 "register\_operand" "d")))]\
""\
"add.d %2,%0")

(define\_insn "addsf3"\
\[(set (match\_operand:SF 0 "register\_operand" "=d")\
(plus:SF (match\_operand:SF 1 "register\_operand" "%0")\
(match\_operand:SF 2 "nonmemory\_operand" "dF")))]\
""\
"add.s %2,%0")

(define\_insn "adddi3"\
\[(set (match\_operand:DI 0 "register\_operand" "=d")\
(plus:DI (match\_operand:DI 1 "register\_operand" "%0")\
(match\_operand:DI 2 "register\_operand" "d")))]\
""\
"add.l %2,%0")

;; special case of addsi3, needed to specify an A reg for the destination\
;; when the source is a sum involving FP or AP.

(define\_insn ""\
\[(set (match\_operand:SI 0 "register\_operand" "=a")\
(plus:SI (match\_operand:SI 1 "register\_operand" "%a")\
(match\_operand:SI 2 "immediate\_operand" "i")))]\
"operands\[1] == frame\_pointer\_rtx || operands\[1] == arg\_pointer\_rtx"\
"ldea %a2(%1),%0")

(define\_insn "addsi3"\
\[(set (match\_operand:SI 0 "register\_operand" "=d,a,a")\
(plus:SI (match\_operand:SI 1 "nonmemory\_operand" "%0,0,a")\
(match\_operand:SI 2 "nonmemory\_operand" "di,ri,i")))]\
""\
"\* switch (which\_alternative)\
{\
case 0:\
case 1:\
return "add.w %2,%0";\
case 2:\
if ((TARGET\_C2 || A\_REG\_P (operands\[0]))\
&& operands\[1] != stack\_pointer\_rtx)\
return "ldea %a2(%1),%0";\
else\
return "mov %1,%0;add.w %2,%0";\
}")

(define\_insn "addhi3"\
\[(set (match\_operand:HI 0 "register\_operand" "=d,a")\
(plus:HI (match\_operand:HI 1 "register\_operand" "%0,0")\
(match\_operand:HI 2 "nonmemory\_operand" "di,ai")))]\
""\
"add.h %2,%0")

(define\_insn "addqi3"\
\[(set (match\_operand:QI 0 "register\_operand" "=d")\
(plus:QI (match\_operand:QI 1 "register\_operand" "%0")\
(match\_operand:QI 2 "register\_operand" "d")))]\
""\
"add.b %2,%0")\
;;- All kinds of subtract instructions.

(define\_insn "subdf3"\
\[(set (match\_operand:DF 0 "register\_operand" "=d")\
(minus:DF (match\_operand:DF 1 "register\_operand" "0")\
(match\_operand:DF 2 "register\_operand" "d")))]\
""\
"sub.d %2,%0")

(define\_insn "subsf3"\
\[(set (match\_operand:SF 0 "register\_operand" "=d")\
(minus:SF (match\_operand:SF 1 "register\_operand" "0")\
(match\_operand:SF 2 "nonmemory\_operand" "dF")))]\
""\
"sub.s %2,%0")

(define\_insn "subdi3"\
\[(set (match\_operand:DI 0 "register\_operand" "=d")\
(minus:DI (match\_operand:DI 1 "register\_operand" "0")\
(match\_operand:DI 2 "register\_operand" "d")))]\
""\
"sub.l %2,%0")

(define\_insn "subsi3"\
\[(set (match\_operand:SI 0 "register\_operand" "=d,a")\
(minus:SI (match\_operand:SI 1 "register\_operand" "0,0")\
(match\_operand:SI 2 "nonmemory\_operand" "di,ai")))]\
""\
"sub.w %2,%0")

(define\_insn "subhi3"\
\[(set (match\_operand:HI 0 "register\_operand" "=d,a")\
(minus:HI (match\_operand:HI 1 "register\_operand" "0,0")\
(match\_operand:HI 2 "nonmemory\_operand" "di,ai")))]\
""\
"sub.h %2,%0")

(define\_insn "subqi3"\
\[(set (match\_operand:QI 0 "register\_operand" "=d")\
(minus:QI (match\_operand:QI 1 "register\_operand" "0")\
(match\_operand:QI 2 "register\_operand" "d")))]\
""\
"sub.b %2,%0")\
;;- Multiply instructions.

(define\_insn "muldf3"\
\[(set (match\_operand:DF 0 "register\_operand" "=d")\
(mult:DF (match\_operand:DF 1 "register\_operand" "%0")\
(match\_operand:DF 2 "register\_operand" "d")))]\
""\
"mul.d %2,%0")

(define\_insn "mulsf3"\
\[(set (match\_operand:SF 0 "register\_operand" "=d")\
(mult:SF (match\_operand:SF 1 "register\_operand" "%0")\
(match\_operand:SF 2 "nonmemory\_operand" "dF")))]\
""\
"mul.s %2,%0")

(define\_insn "muldi3"\
\[(set (match\_operand:DI 0 "register\_operand" "=d")\
(mult:DI (match\_operand:DI 1 "register\_operand" "%0")\
(match\_operand:DI 2 "register\_operand" "d")))]\
""\
"mul.l %2,%0")

(define\_insn "mulsi3"\
\[(set (match\_operand:SI 0 "register\_operand" "=d,a")\
(mult:SI (match\_operand:SI 1 "register\_operand" "%0,0")\
(match\_operand:SI 2 "nonmemory\_operand" "di,ai")))]\
""\
"mul.w %2,%0")

(define\_insn "mulhi3"\
\[(set (match\_operand:HI 0 "register\_operand" "=d,a")\
(mult:HI (match\_operand:HI 1 "register\_operand" "%0,0")\
(match\_operand:HI 2 "nonmemory\_operand" "di,ai")))]\
""\
"mul.h %2,%0")

(define\_insn "mulqi3"\
\[(set (match\_operand:QI 0 "register\_operand" "=d")\
(mult:QI (match\_operand:QI 1 "register\_operand" "%0")\
(match\_operand:QI 2 "register\_operand" "d")))]\
""\
"mul.b %2,%0")\
;;- Divide instructions.

(define\_insn "divdf3"\
\[(set (match\_operand:DF 0 "register\_operand" "=d")\
(div:DF (match\_operand:DF 1 "register\_operand" "0")\
(match\_operand:DF 2 "register\_operand" "d")))]\
""\
"div.d %2,%0")

(define\_insn "divsf3"\
\[(set (match\_operand:SF 0 "register\_operand" "=d")\
(div:SF (match\_operand:SF 1 "register\_operand" "0")\
(match\_operand:SF 2 "nonmemory\_operand" "dF")))]\
""\
"div.s %2,%0")

(define\_insn "divdi3"\
\[(set (match\_operand:DI 0 "register\_operand" "=d")\
(div:DI (match\_operand:DI 1 "register\_operand" "0")\
(match\_operand:DI 2 "register\_operand" "d")))]\
""\
"div.l %2,%0")

(define\_insn "udivdi3"\
\[(set (match\_operand:DI 0 "register\_operand" "=d")\
(udiv:DI (match\_operand:DI 1 "register\_operand" "d")\
(match\_operand:DI 2 "register\_operand" "d")))]\
""\
"psh.l %2;psh.l %1;callq udiv64;pop.l %0;add.w #8,sp")

(define\_insn "divsi3"\
\[(set (match\_operand:SI 0 "register\_operand" "=d,a")\
(div:SI (match\_operand:SI 1 "register\_operand" "0,0")\
(match\_operand:SI 2 "nonmemory\_operand" "di,ai")))]\
""\
"div.w %2,%0")

(define\_insn "divhi3"\
\[(set (match\_operand:HI 0 "register\_operand" "=d,a")\
(div:HI (match\_operand:HI 1 "register\_operand" "0,0")\
(match\_operand:HI 2 "nonmemory\_operand" "di,ai")))]\
""\
"div.h %2,%0")

(define\_insn "divqi3"\
\[(set (match\_operand:QI 0 "register\_operand" "=d")\
(div:QI (match\_operand:QI 1 "register\_operand" "0")\
(match\_operand:QI 2 "register\_operand" "d")))]\
""\
"div.b %2,%0")\
;; - and, or, xor

(define\_insn "anddi3"\
\[(set (match\_operand:DI 0 "register\_operand" "=d")\
(and:DI (match\_operand:DI 1 "register\_operand" "%0")\
(match\_operand:DI 2 "register\_operand" "d")))]\
""\
"and %2,%0")

(define\_insn "andsi3"\
\[(set (match\_operand:SI 0 "register\_operand" "=d,a")\
(and:SI (match\_operand:SI 1 "register\_operand" "%0,0")\
(match\_operand:SI 2 "nonmemory\_operand" "di,ai")))]\
""\
"and %2,%0")

(define\_insn "andhi3"\
\[(set (match\_operand:HI 0 "register\_operand" "=d,a")\
(and:HI (match\_operand:HI 1 "register\_operand" "%0,0")\
(match\_operand:HI 2 "nonmemory\_operand" "di,ai")))]\
""\
"and %2,%0")

(define\_insn "andqi3"\
\[(set (match\_operand:QI 0 "register\_operand" "=d,a")\
(and:QI (match\_operand:QI 1 "register\_operand" "%0,0")\
(match\_operand:QI 2 "nonmemory\_operand" "di,ai")))]\
""\
"and %2,%0")

;;- Bit set instructions.

(define\_insn "iordi3"\
\[(set (match\_operand:DI 0 "register\_operand" "=d")\
(ior:DI (match\_operand:DI 1 "register\_operand" "%0")\
(match\_operand:DI 2 "register\_operand" "d")))]\
""\
"or %2,%0")

(define\_insn "iorsi3"\
\[(set (match\_operand:SI 0 "register\_operand" "=d,a")\
(ior:SI (match\_operand:SI 1 "register\_operand" "%0,0")\
(match\_operand:SI 2 "nonmemory\_operand" "di,ai")))]\
""\
"or %2,%0")

(define\_insn "iorhi3"\
\[(set (match\_operand:HI 0 "register\_operand" "=d,a")\
(ior:HI (match\_operand:HI 1 "register\_operand" "%0,0")\
(match\_operand:HI 2 "nonmemory\_operand" "di,ai")))]\
""\
"or %2,%0")

(define\_insn "iorqi3"\
\[(set (match\_operand:QI 0 "register\_operand" "=d,a")\
(ior:QI (match\_operand:QI 1 "register\_operand" "%0,0")\
(match\_operand:QI 2 "nonmemory\_operand" "di,ai")))]\
""\
"or %2,%0")

;;- xor instructions.

(define\_insn "xordi3"\
\[(set (match\_operand:DI 0 "register\_operand" "=d")\
(xor:DI (match\_operand:DI 1 "register\_operand" "%0")\
(match\_operand:DI 2 "register\_operand" "d")))]\
""\
"xor %2,%0")

(define\_insn "xorsi3"\
\[(set (match\_operand:SI 0 "register\_operand" "=d,a")\
(xor:SI (match\_operand:SI 1 "register\_operand" "%0,0")\
(match\_operand:SI 2 "nonmemory\_operand" "di,ai")))]\
""\
"xor %2,%0")

(define\_insn "xorhi3"\
\[(set (match\_operand:HI 0 "register\_operand" "=d,a")\
(xor:HI (match\_operand:HI 1 "register\_operand" "%0,0")\
(match\_operand:HI 2 "nonmemory\_operand" "di,ai")))]\
""\
"xor %2,%0")

(define\_insn "xorqi3"\
\[(set (match\_operand:QI 0 "register\_operand" "=d,a")\
(xor:QI (match\_operand:QI 1 "register\_operand" "%0,0")\
(match\_operand:QI 2 "nonmemory\_operand" "di,ai")))]\
""\
"xor %2,%0")\
(define\_insn "negdf2"\
\[(set (match\_operand:DF 0 "register\_operand" "=d")\
(neg:DF (match\_operand:DF 1 "register\_operand" "d")))]\
""\
"neg.d %1,%0")

(define\_insn "negsf2"\
\[(set (match\_operand:SF 0 "register\_operand" "=d")\
(neg:SF (match\_operand:SF 1 "register\_operand" "d")))]\
""\
"neg.s %1,%0")

(define\_insn "negdi2"\
\[(set (match\_operand:DI 0 "register\_operand" "=d")\
(neg:DI (match\_operand:DI 1 "register\_operand" "d")))]\
""\
"neg.l %1,%0")

(define\_insn "negsi2"\
\[(set (match\_operand:SI 0 "register\_operand" "=d,a")\
(neg:SI (match\_operand:SI 1 "register\_operand" "d,a")))]\
""\
"neg.w %1,%0")

(define\_insn "neghi2"\
\[(set (match\_operand:HI 0 "register\_operand" "=d,a")\
(neg:HI (match\_operand:HI 1 "register\_operand" "d,a")))]\
""\
"neg.h %1,%0")

(define\_insn "negqi2"\
\[(set (match\_operand:QI 0 "register\_operand" "=d")\
(neg:QI (match\_operand:QI 1 "register\_operand" "d")))]\
""\
"neg.b %1,%0")\
(define\_insn "one\_cmpldi2"\
\[(set (match\_operand:DI 0 "register\_operand" "=d")\
(not:DI (match\_operand:DI 1 "register\_operand" "d")))]\
""\
"not %1,%0")

(define\_insn "one\_cmplsi2"\
\[(set (match\_operand:SI 0 "register\_operand" "=d,a")\
(not:SI (match\_operand:SI 1 "register\_operand" "d,a")))]\
""\
"not %1,%0")

(define\_insn "one\_cmplhi2"\
\[(set (match\_operand:HI 0 "register\_operand" "=d,a")\
(not:HI (match\_operand:HI 1 "register\_operand" "d,a")))]\
""\
"not %1,%0")

(define\_insn "one\_cmplqi2"\
\[(set (match\_operand:QI 0 "register\_operand" "=d,a")\
(not:QI (match\_operand:QI 1 "register\_operand" "d,a")))]\
""\
"not %1,%0")\
;;- shifts\
;;\
;; Convex shift instructions are unsigned.\
;; To make signed right shifts:\
;; for SImode, sign extend to DImode and shift, works for 0..32\
;; for DImode, shift and then extend the sign, works for 0..63\
;;\
;; It is very sad that DImode right shift 64 fails, but I don't see\
;; any reasonable way to handle it. ANSI only requires up to 63.

(define\_insn ""\
\[(set (match\_operand:SI 0 "register\_operand" "=r")\
(ashift:SI (match\_operand:SI 1 "register\_operand" "0")\
(match\_operand:SI 2 "immediate\_operand" "i")))]\
"INTVAL (operands\[2]) >= 0"\
"\*\
{\
if (operands\[2] == const1\_rtx)\
return "add.w %0,%0";\
else if (TARGET\_C2 && S\_REG\_P (operands\[0]))\
return "shf.w %2,%0";\
else\
return "shf %2,%0";\
}")

(define\_insn "ashlsi3"\
\[(set (match\_operand:SI 0 "register\_operand" "=d")\
(ashift:SI (match\_operand:SI 1 "register\_operand" "0")\
(match\_operand:SI 2 "nonmemory\_operand" "di")))]\
""\
"\*\
{\
if (operands\[2] == const1\_rtx)\
return "add.w %0,%0";\
else if (GET\_CODE (operands\[2]) == CONST\_INT && INTVAL (operands\[2]) >= 0)\
return TARGET\_C2 ? "shf.w %2,%0" : "shf %2,%0";\
else\
return "cvtw.l %0,%0;shf %2,%0";\
}")

(define\_expand "ashrsi3"\
\[(set (match\_operand:SI 0 "register\_operand" "=d")\
(ashift:SI (match\_operand:SI 1 "register\_operand" "0")\
(match\_operand:SI 2 "nonmemory\_operand" "di")))]\
""\
"operands\[2] = negate\_rtx (SImode, operands\[2]);")

(define\_insn "lshlsi3"\
\[(set (match\_operand:SI 0 "register\_operand" "=d,a")\
(lshift:SI (match\_operand:SI 1 "register\_operand" "0,0")\
(match\_operand:SI 2 "nonmemory\_operand" "di,ai")))]\
""\
"\*\
{\
if (operands\[2] == const1\_rtx)\
return "add.w %0,%0";\
if (S\_REG\_P (operands\[0]))\
{\
if (TARGET\_C2)\
return "shf.w %2,%0";\
else if (GET\_CODE (operands\[2]) == CONST\_INT\
&& INTVAL (operands\[2]) >= 0)\
return "shf %2,%0";\
else\
return "ld.u #0,%0;shf %2,%0";\
}\
return "shf %2,%0";\
}")

(define\_expand "lshrsi3"\
\[(set (match\_operand:SI 0 "register\_operand" "=d")\
(lshift:SI (match\_operand:SI 1 "register\_operand" "0")\
(match\_operand:SI 2 "nonmemory\_operand" "di")))]\
""\
"operands\[2] = negate\_rtx (SImode, operands\[2]);")

;; signed a >> b is\
;; ((a >> b) ^ signbit) - signbit\
;; where signbit is (1 << 63) >> b

(define\_expand "ashldi3"\
\[(match\_operand:DI 0 "register\_operand" "=d")\
(match\_operand:DI 1 "register\_operand" "0")\
(match\_operand:SI 2 "nonmemory\_operand" "di")\
(match\_dup 3)]\
""\
"\
{\
if (GET\_CODE (operands\[2]) == CONST\_INT && INTVAL (operands\[2]) >= 0)\
emit\_insn (gen\_rtx (SET, VOIDmode, operands\[0],\
gen\_rtx (LSHIFT, DImode, operands\[1], operands\[2])));\
else if (GET\_CODE (operands\[2]) == CONST\_INT && INTVAL (operands\[2]) < 0)\
{\
int rshift = - INTVAL (operands\[2]);\
operands\[3] = force\_reg\
(DImode,\
immed\_double\_const (1 << (63 - rshift), 1 << (31 - rshift), DImode));\
emit\_insn (gen\_rtx (SET, VOIDmode, operands\[0],\
gen\_rtx (LSHIFT, DImode, operands\[1], operands\[2])));\
emit\_insn (gen\_rtx (SET, VOIDmode, operands\[0],\
gen\_rtx (XOR, DImode, operands\[0], operands\[3])));\
emit\_insn (gen\_rtx (SET, VOIDmode, operands\[0],\
gen\_rtx (MINUS, DImode, operands\[0], operands\[3])));\
}\
else\
{\
operands\[3] =\
force\_reg (DImode, immed\_double\_const (0, 1 << 31, DImode));\
emit\_insn (gen\_rtx (SET, VOIDmode, operands\[0],\
gen\_rtx (LSHIFT, DImode, operands\[1], operands\[2])));\
emit\_insn (gen\_rtx (SET, VOIDmode, operands\[3],\
gen\_rtx (LSHIFT, DImode, operands\[3], operands\[2])));\
emit\_insn (gen\_rtx (SET, VOIDmode, operands\[0],\
gen\_rtx (XOR, DImode, operands\[0], operands\[3])));\
emit\_insn (gen\_rtx (SET, VOIDmode, operands\[0],\
gen\_rtx (MINUS, DImode, operands\[0], operands\[3])));\
}\
DONE;\
}")

(define\_expand "ashrdi3"\
\[(match\_operand:DI 0 "register\_operand" "=d")\
(match\_operand:DI 1 "register\_operand" "0")\
(match\_operand:SI 2 "nonmemory\_operand" "di")]\
""\
"\
{\
emit\_insn (gen\_ashldi3 (operands\[0], operands\[1],\
negate\_rtx (SImode, operands\[2])));\
DONE;\
}")

(define\_insn "lshldi3"\
\[(set (match\_operand:DI 0 "register\_operand" "=d")\
(lshift:DI (match\_operand:DI 1 "register\_operand" "0")\
(match\_operand:SI 2 "nonmemory\_operand" "di")))]\
""\
"shf %2,%0")

(define\_expand "lshrdi3"\
\[(set (match\_operand:DI 0 "register\_operand" "=d")\
(lshift:DI (match\_operand:DI 1 "register\_operand" "0")\
(match\_operand:SI 2 "nonmemory\_operand" "di")))]\
""\
"operands\[2] = negate\_rtx (SImode, operands\[2]);")\
;; \_\_builtin instructions

(define\_insn "sqrtdf2"\
\[(set (match\_operand:DF 0 "register\_operand" "=d")\
(sqrt:DF (match\_operand:DF 1 "register\_operand" "0")))]\
"TARGET\_C2"\
"sqrt.d %0")

(define\_insn "sqrtsf2"\
\[(set (match\_operand:SF 0 "register\_operand" "=d")\
(sqrt:SF (match\_operand:SF 1 "register\_operand" "0")))]\
"TARGET\_C2"\
"sqrt.s %0")

(define\_insn ""\
\[(set (match\_operand:SI 0 "register\_operand" "=d")\
(minus:SI (ffs:SI (match\_operand:SI 1 "register\_operand" "d"))\
(const\_int 1)))]\
""\
"tzc %1,%0;le.w #32,%0;jbrs.f .+6;ld.w #-1,%0")

(define\_expand "ffssi2"\
\[(set (match\_operand:SI 0 "register\_operand" "=d")\
(minus:SI (ffs:SI (match\_operand:SI 1 "register\_operand" "d"))\
(const\_int 1)))\
(set (match\_dup 0)\
(plus:SI (match\_dup 0)\
(const\_int 1)))]\
""\
"")

(define\_insn "abssf2"\
\[(set (match\_operand:SF 0 "register\_operand" "=d")\
(abs:SF (match\_operand:SF 1 "register\_operand" "0")))]\
""\
"and #0x7fffffff,%0")

(define\_expand "absdf2"\
\[(set (subreg:DI (match\_operand:DF 0 "register\_operand" "=d") 0)\
(and:DI (subreg:DI (match\_operand:DF 1 "register\_operand" "d") 0)\
(match\_dup 2)))]\
""\
"operands\[2] = force\_reg (DImode,\
immed\_double\_const (-1, 0x7fffffff, DImode));")\
(define\_insn "jump"\
\[(set (pc)\
(label\_ref (match\_operand 0 "" "")))]\
""\
"jbr %l0")

(define\_insn "beq"\
\[(set (pc)\
(if\_then\_else (eq (cc0)\
(const\_int 0))\
(label\_ref (match\_operand 0 "" ""))\
(pc)))]\
""\
"\* return gen\_cmp (operands\[0], "eq", 't'); ")

(define\_insn "bne"\
\[(set (pc)\
(if\_then\_else (ne (cc0)\
(const\_int 0))\
(label\_ref (match\_operand 0 "" ""))\
(pc)))]\
""\
"\* return gen\_cmp (operands\[0], "eq", 'f'); ")

(define\_insn "bgt"\
\[(set (pc)\
(if\_then\_else (gt (cc0)\
(const\_int 0))\
(label\_ref (match\_operand 0 "" ""))\
(pc)))]\
""\
"\* return gen\_cmp (operands\[0], "le", 'f'); ")

(define\_insn "bgtu"\
\[(set (pc)\
(if\_then\_else (gtu (cc0)\
(const\_int 0))\
(label\_ref (match\_operand 0 "" ""))\
(pc)))]\
""\
"\* return gen\_cmp (operands\[0], "leu", 'f'); ")

(define\_insn "blt"\
\[(set (pc)\
(if\_then\_else (lt (cc0)\
(const\_int 0))\
(label\_ref (match\_operand 0 "" ""))\
(pc)))]\
""\
"\* return gen\_cmp (operands\[0], "lt", 't'); ")

(define\_insn "bltu"\
\[(set (pc)\
(if\_then\_else (ltu (cc0)\
(const\_int 0))\
(label\_ref (match\_operand 0 "" ""))\
(pc)))]\
""\
"\* return gen\_cmp (operands\[0], "ltu", 't'); ")

(define\_insn "bge"\
\[(set (pc)\
(if\_then\_else (ge (cc0)\
(const\_int 0))\
(label\_ref (match\_operand 0 "" ""))\
(pc)))]\
""\
"\* return gen\_cmp (operands\[0], "lt", 'f'); ")

(define\_insn "bgeu"\
\[(set (pc)\
(if\_then\_else (geu (cc0)\
(const\_int 0))\
(label\_ref (match\_operand 0 "" ""))\
(pc)))]\
""\
"\* return gen\_cmp (operands\[0], "ltu", 'f'); ")

(define\_insn "ble"\
\[(set (pc)\
(if\_then\_else (le (cc0)\
(const\_int 0))\
(label\_ref (match\_operand 0 "" ""))\
(pc)))]\
""\
"\* return gen\_cmp (operands\[0], "le", 't'); ")

(define\_insn "bleu"\
\[(set (pc)\
(if\_then\_else (leu (cc0)\
(const\_int 0))\
(label\_ref (match\_operand 0 "" ""))\
(pc)))]\
""\
"\* return gen\_cmp (operands\[0], "leu", 't'); ")\
(define\_insn ""\
\[(set (pc)\
(if\_then\_else (eq (cc0)\
(const\_int 0))\
(pc)\
(label\_ref (match\_operand 0 "" ""))))]\
""\
"\* return gen\_cmp (operands\[0], "eq", 'f'); ")

(define\_insn ""\
\[(set (pc)\
(if\_then\_else (ne (cc0)\
(const\_int 0))\
(pc)\
(label\_ref (match\_operand 0 "" ""))))]\
""\
"\* return gen\_cmp (operands\[0], "eq", 't'); ")

(define\_insn ""\
\[(set (pc)\
(if\_then\_else (gt (cc0)\
(const\_int 0))\
(pc)\
(label\_ref (match\_operand 0 "" ""))))]\
""\
"\* return gen\_cmp (operands\[0], "le", 't'); ")

(define\_insn ""\
\[(set (pc)\
(if\_then\_else (gtu (cc0)\
(const\_int 0))\
(pc)\
(label\_ref (match\_operand 0 "" ""))))]\
""\
"\* return gen\_cmp (operands\[0], "leu", 't'); ")

(define\_insn ""\
\[(set (pc)\
(if\_then\_else (lt (cc0)\
(const\_int 0))\
(pc)\
(label\_ref (match\_operand 0 "" ""))))]\
""\
"\* return gen\_cmp (operands\[0], "lt", 'f'); ")

(define\_insn ""\
\[(set (pc)\
(if\_then\_else (ltu (cc0)\
(const\_int 0))\
(pc)\
(label\_ref (match\_operand 0 "" ""))))]\
""\
"\* return gen\_cmp (operands\[0], "ltu", 'f'); ")

(define\_insn ""\
\[(set (pc)\
(if\_then\_else (ge (cc0)\
(const\_int 0))\
(pc)\
(label\_ref (match\_operand 0 "" ""))))]\
""\
"\* return gen\_cmp (operands\[0], "lt", 't'); ")

(define\_insn ""\
\[(set (pc)\
(if\_then\_else (geu (cc0)\
(const\_int 0))\
(pc)\
(label\_ref (match\_operand 0 "" ""))))]\
""\
"\* return gen\_cmp (operands\[0], "ltu", 't'); ")

(define\_insn ""\
\[(set (pc)\
(if\_then\_else (le (cc0)\
(const\_int 0))\
(pc)\
(label\_ref (match\_operand 0 "" ""))))]\
""\
"\* return gen\_cmp (operands\[0], "le", 'f'); ")

(define\_insn ""\
\[(set (pc)\
(if\_then\_else (leu (cc0)\
(const\_int 0))\
(pc)\
(label\_ref (match\_operand 0 "" ""))))]\
""\
"\* return gen\_cmp (operands\[0], "leu", 'f'); ")\
;; - Calls\
;;\
;; arg count word may be omitted to save a push and let gcc try to\
;; combine the arg list pop. RETURN\_POPS\_ARGS from tm.h decides this.

(define\_insn "call"\
\[(call (match\_operand:QI 0 "general\_operand" "g")\
(match\_operand:SI 1 "general\_operand" "g"))]\
""\
"\*\
{\
if (! RETURN\_POPS\_ARGS (ignoreme))\
{\
if (operands\[1] == const0\_rtx)\
return "calls %0";\
if (! reg\_mentioned\_p (arg\_pointer\_rtx, operands\[0]))\
return "mov sp,ap;calls %0;ld.w 12(fp),ap";\
operands\[0] = XEXP (operands\[0], 0);\
return "ld.w %0,a1;mov sp,ap;calls (a1);ld.w 12(fp),ap";\
}\
operands\[1] = gen\_rtx (CONST\_INT, VOIDmode, (INTVAL (operands\[1]) + 3)/ 4);\
if (! reg\_mentioned\_p (arg\_pointer\_rtx, operands\[0]))\
return "mov sp,ap;pshea %a1;calls %0;ld.w 12(fp),ap;add.w #4\*%a1+4,sp";\
operands\[0] = XEXP (operands\[0], 0);\
return "ld.w %0,a1;mov sp,ap;pshea %a1;calls (a1);ld.w 12(fp),ap;add.w #4\*%a1+4,sp";\
}")

(define\_insn "call\_value"\
\[(set (match\_operand 0 "" "=g")\
(call (match\_operand:QI 1 "general\_operand" "g")\
(match\_operand:SI 2 "general\_operand" "g")))]\
""\
"\*\
{\
if (! RETURN\_POPS\_ARGS (ignoreme))\
{\
if (operands\[2] == const0\_rtx)\
return "calls %1";\
if (! reg\_mentioned\_p (arg\_pointer\_rtx, operands\[1]))\
return "mov sp,ap;calls %1;ld.w 12(fp),ap";\
operands\[1] = XEXP (operands\[1], 0);\
return "ld.w %1,a1;mov sp,ap;calls (a1);ld.w 12(fp),ap";\
}\
operands\[2] = gen\_rtx (CONST\_INT, VOIDmode, (INTVAL (operands\[2]) + 3) / 4);\
if (! reg\_mentioned\_p (arg\_pointer\_rtx, operands\[1]))\
return "mov sp,ap;pshea %a2;calls %1;ld.w 12(fp),ap;add.w #4\*%a2+4,sp";\
operands\[1] = XEXP (operands\[1], 0);\
return "ld.w %1,a1;mov sp,ap;pshea %a2;calls (a1);ld.w 12(fp),ap;add.w #4\*%a2+4,sp";\
}")

(define\_insn "return"\
\[(return)]\
""\
"rtn")

(define\_insn "nop"\
\[(const\_int 0)]\
""\
"nop")

(define\_insn "tablejump"\
\[(set (pc) (match\_operand:SI 0 "address\_operand" "p"))\
(use (label\_ref (match\_operand 1 "" "")))]\
""\
"jmp %a0")\
;; - fix up the code generated for bit field tests

;; cc0 = (x >> k1) & k2 --> cc0 = x & (k2 << k1)\
;; cc0 = (x << k1) & k2 --> cc0 = x & (k2 >> k1)\
;; provided k2 and (k2 << k1) don't include the sign bit

(define\_peephole\
\[(set (match\_operand:SI 0 "register\_operand" "r")\
(lshift:SI (match\_dup 0)\
(match\_operand 1 "immediate\_operand" "i")))\
(set (match\_dup 0)\
(and:SI (match\_dup 0)\
(match\_operand 2 "immediate\_operand" "i")))\
(set (cc0) (match\_dup 0))]\
"dead\_or\_set\_p (insn, operands\[0])\
&& GET\_CODE (operands\[1]) == CONST\_INT\
&& GET\_CODE (operands\[2]) == CONST\_INT\
&& INTVAL (operands\[2]) >= 0\
&& (INTVAL (operands\[2]) << INTVAL (operands\[1])) >= 0"\
"\*\
{\
operands\[2] = gen\_rtx (CONST\_INT, VOIDmode,\
INTVAL (operands\[2]) >> INTVAL (operands\[1]));\
output\_asm\_insn ("and %2,%0", operands);\
return set\_cmp (operands\[0], const0\_rtx, 'w');\
}")

;; same as above where x is (y & 0xff...) caused by a zero extend

(define\_peephole\
\[(set (match\_operand:SI 0 "register\_operand" "r")\
(zero\_extend:SI (match\_operand 1 "register\_operand" "0")))\
(set (match\_dup 0)\
(lshift:SI (match\_dup 0)\
(match\_operand 2 "immediate\_operand" "i")))\
(set (match\_dup 0)\
(and:SI (match\_dup 0)\
(match\_operand 3 "immediate\_operand" "i")))\
(set (cc0) (match\_dup 0))]\
"dead\_or\_set\_p (insn, operands\[0])\
&& REGNO (operands\[0]) == REGNO (operands\[1])\
&& GET\_CODE (operands\[2]) == CONST\_INT\
&& GET\_CODE (operands\[3]) == CONST\_INT\
&& (INTVAL (operands\[3]) << INTVAL (operands\[2])) >= 0"\
"\*\
{\
operands\[3] = gen\_rtx (CONST\_INT, VOIDmode,\
(INTVAL (operands\[3]) >> INTVAL (operands\[2])) &\
\~((-1) << GET\_MODE\_BITSIZE (GET\_MODE (operands\[1]))));\
output\_asm\_insn ("and %3,%0", operands);\
return set\_cmp (operands\[0], const0\_rtx, 'w');\
}")\
;;- Local variables:\
;;- mode:emacs-lisp\
;;- comment-start: ";;- "\
;;- eval: (set-syntax-table (copy-sequence (syntax-table)))\
;;- eval: (modify-syntax-entry ?\[ "(]")\
;;- eval: (modify-syntax-entry ?] ")\[")\
;;- eval: (modify-syntax-entry ?{ "(}")\
;;- eval: (modify-syntax-entry ?} "){")\
;;- End:
