# vax

;;- Machine description for GNU compiler\
;;- Vax Version\
;; Copyright (C) 1987, 1988 Free Software Foundation, Inc.

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
(match\_operand:SI 0 "general\_operand" "g"))]\
""\
"tstl %0")

(define\_insn "tsthi"\
\[(set (cc0)\
(match\_operand:HI 0 "general\_operand" "g"))]\
""\
"tstw %0")

(define\_insn "tstqi"\
\[(set (cc0)\
(match\_operand:QI 0 "general\_operand" "g"))]\
""\
"tstb %0")

(define\_insn "tstdf"\
\[(set (cc0)\
(match\_operand:DF 0 "general\_operand" "gF"))]\
""\
"tst%# %0")

(define\_insn "tstsf"\
\[(set (cc0)\
(match\_operand:SF 0 "general\_operand" "gF"))]\
""\
"tstf %0")

;; Put cmpsi first among compare insns so it matches two CONST\_INT operands.

(define\_insn "cmpsi"\
\[(set (cc0)\
(compare (match\_operand:SI 0 "general\_operand" "g")\
(match\_operand:SI 1 "general\_operand" "g")))]\
""\
"cmpl %0,%1")

(define\_insn "cmphi"\
\[(set (cc0)\
(compare (match\_operand:HI 0 "general\_operand" "g")\
(match\_operand:HI 1 "general\_operand" "g")))]\
""\
"cmpw %0,%1")

(define\_insn "cmpqi"\
\[(set (cc0)\
(compare (match\_operand:QI 0 "general\_operand" "g")\
(match\_operand:QI 1 "general\_operand" "g")))]\
""\
"cmpb %0,%1")

(define\_insn "cmpdf"\
\[(set (cc0)\
(compare (match\_operand:DF 0 "general\_operand" "gF")\
(match\_operand:DF 1 "general\_operand" "gF")))]\
""\
"cmp%# %0,%1")

(define\_insn "cmpsf"\
\[(set (cc0)\
(compare (match\_operand:SF 0 "general\_operand" "gF")\
(match\_operand:SF 1 "general\_operand" "gF")))]\
""\
"cmpf %0,%1")

(define\_insn ""\
\[(set (cc0)\
(and:SI (match\_operand:SI 0 "general\_operand" "g")\
(match\_operand:SI 1 "general\_operand" "g")))]\
""\
"bitl %0,%1")

(define\_insn ""\
\[(set (cc0)\
(and:HI (match\_operand:HI 0 "general\_operand" "g")\
(match\_operand:HI 1 "general\_operand" "g")))]\
""\
"bitw %0,%1")

(define\_insn ""\
\[(set (cc0)\
(and:QI (match\_operand:QI 0 "general\_operand" "g")\
(match\_operand:QI 1 "general\_operand" "g")))]\
""\
"bitb %0,%1")\
(define\_insn "movdf"\
\[(set (match\_operand:DF 0 "general\_operand" "=g")\
(match\_operand:DF 1 "general\_operand" "gF"))]\
""\
"\*\
{\
if (operands\[1] == dconst0\_rtx)\
return "clr%# %0";\
return "mov%# %1,%0";\
}")

(define\_insn "movsf"\
\[(set (match\_operand:SF 0 "general\_operand" "=g")\
(match\_operand:SF 1 "general\_operand" "gF"))]\
""\
"\*\
{\
if (operands\[1] == fconst0\_rtx)\
return "clrf %0";\
return "movf %1,%0";\
}")

;; Some vaxes don't support this instruction.\
;;(define\_insn "movti"\
;; \[(set (match\_operand:TI 0 "general\_operand" "=g")\
;; (match\_operand:TI 1 "general\_operand" "g"))]\
;; ""\
;; "movh %1,%0")

(define\_insn "movdi"\
\[(set (match\_operand:DI 0 "general\_operand" "=g")\
(match\_operand:DI 1 "general\_operand" "g"))]\
""\
"movq %1,%0")

(define\_insn "movsi"\
\[(set (match\_operand:SI 0 "general\_operand" "=g")\
(match\_operand:SI 1 "general\_operand" "g"))]\
""\
"\*\
{\
rtx link;\
if (operands\[1] == const1\_rtx\
&& (link = find\_reg\_note (insn, REG\_WAS\_0, 0))\
/\* Make sure the insn that stored the 0 is still present. _/_\
_&& ! XEXP (link, 0)->volatil_\
_&& GET\_CODE (XEXP (link, 0)) != NOTE_\
_/_ Make sure cross jumping didn't happen here. _/_\
_&& no\_labels\_between\_p (XEXP (link, 0), insn))_\
_/_ Fastest way to change a 0 to a 1. _/_\
_return "incl %0";_\
_if (GET\_CODE (operands\[1]) == SYMBOL\_REF || GET\_CODE (operands\[1]) == CONST)_\
_{_\
_if (push\_operand (operands\[0], SImode))_\
_return "pushab %a1";_\
_return "movab %a1,%0";_\
_}_\
_/_ this is slower than a movl, except when pushing an operand _/_\
_if (operands\[1] == const0\_rtx)_\
_return "clrl %0";_\
_if (GET\_CODE (operands\[1]) == CONST\_INT_\
_&& (unsigned) INTVAL (operands\[1]) >= 64)_\
_{_\
_int i = INTVAL (operands\[1]);_\
_if ((unsigned)(\~i) < 64)_\
_{_\
_operands\[1] = gen\_rtx (CONST\_INT, VOIDmode, \~i);_\
_return "mcoml %1,%0";_\
_}_\
_if ((unsigned)i < 127)_\
_{_\
_operands\[1] = gen\_rtx (CONST\_INT, VOIDmode, 63);_\
_operands\[2] = gen\_rtx (CONST\_INT, VOIDmode, i-63);_\
_return "addl3 %2,%1,%0";_\
_}_\
_/_ trading speed for space \*/\
if ((unsigned)i < 0x100)\
return "movzbl %1,%0";\
if (i >= -0x80 && i < 0)\
return "cvtbl %1,%0";\
if ((unsigned)i < 0x10000)\
return "movzwl %1,%0";\
if (i >= -0x8000 && i < 0)\
return "cvtwl %1,%0";\
}\
if (push\_operand (operands\[0], SImode))\
return "pushl %1";\
return "movl %1,%0";\
}")

(define\_insn "movhi"\
\[(set (match\_operand:HI 0 "general\_operand" "=g")\
(match\_operand:HI 1 "general\_operand" "g"))]\
""\
"\*\
{\
rtx link;\
if (operands\[1] == const1\_rtx\
&& (link = find\_reg\_note (insn, REG\_WAS\_0, 0))\
/\* Make sure the insn that stored the 0 is still present. _/_\
_&& ! XEXP (link, 0)->volatil_\
_&& GET\_CODE (XEXP (link, 0)) != NOTE_\
_/_ Make sure cross jumping didn't happen here. _/_\
_&& no\_labels\_between\_p (XEXP (link, 0), insn))_\
_/_ Fastest way to change a 0 to a 1. _/_\
_return "incw %0";_\
_if (operands\[1] == const0\_rtx)_\
_return "clrw %0";_\
_if (GET\_CODE (operands\[1]) == CONST\_INT_\
_&& (unsigned) INTVAL (operands\[1]) >= 64)_\
_{_\
_int i = INTVAL (operands\[1]);_\
_if ((unsigned)((\~i) & 0xffff) < 64)_\
_{_\
_operands\[1] = gen\_rtx (CONST\_INT, VOIDmode, (\~i) & 0xffff);_\
_return "mcomw %1,%0";_\
_}_\
_if ((unsigned)(i & 0xffff) < 127)_\
_{_\
_operands\[1] = gen\_rtx (CONST\_INT, VOIDmode, 63);_\
_operands\[2] = gen\_rtx (CONST\_INT, VOIDmode, (i-63) & 0xffff);_\
_return "addw3 %2,%1,%0";_\
_}_\
_/_ this is a lot slower, and only saves 1 measly byte! _/_\
_/_ if ((unsigned)i < 0x100)\
return "movzbw %1,%0"; _/_\
_/_ if (i >= -0x80 && i < 0)\
return "cvtbw %1,%0"; \*/\
}\
return "movw %1,%0";\
}")

(define\_insn "movqi"\
\[(set (match\_operand:QI 0 "general\_operand" "=g")\
(match\_operand:QI 1 "general\_operand" "g"))]\
""\
"\*\
{\
if (operands\[1] == const0\_rtx)\
return "clrb %0";\
if (GET\_CODE (operands\[1]) == CONST\_INT\
&& (unsigned) INTVAL (operands\[1]) >= 64)\
{\
int i = INTVAL (operands\[1]);\
if ((unsigned)((\~i) & 0xff) < 64)\
{\
operands\[1] = gen\_rtx (CONST\_INT, VOIDmode, (\~i) & 0xff);\
return "mcomb %1,%0";\
}\
\#if 0\
/\* ASCII alphabetics \*/\
if (((unsigned) INTVAL (operands\[1]) &0xff) < 127)\
{\
operands\[1] = gen\_rtx (CONST\_INT, VOIDmode, 63);\
operands\[2] = gen\_rtx (CONST\_INT, VOIDmode, i-63);\
return "addb3 %2,%1,%0";\
}\
\#endif\
}\
return "movb %1,%0";\
}")

;; The definition of this insn does not really explain what it does,\
;; but it should suffice\
;; that anything generated as this insn will be recognized as one\
;; and that it won't successfully combine with anything.\
(define\_insn "movstrhi"\
\[(set (match\_operand:BLK 0 "general\_operand" "=g")\
(match\_operand:BLK 1 "general\_operand" "g"))\
(use (match\_operand:HI 2 "general\_operand" "g"))\
(clobber (reg:SI 0))\
(clobber (reg:SI 1))\
(clobber (reg:SI 2))\
(clobber (reg:SI 3))\
(clobber (reg:SI 4))\
(clobber (reg:SI 5))]\
""\
"movc3 %2,%1,%0")\
;; Extension and truncation insns.\
;; Those for integer source operand\
;; are ordered widest source type first.

(define\_insn "truncsiqi2"\
\[(set (match\_operand:QI 0 "general\_operand" "=g")\
(truncate:QI (match\_operand:SI 1 "general\_operand" "g")))]\
""\
"cvtlb %1,%0")

(define\_insn "truncsihi2"\
\[(set (match\_operand:HI 0 "general\_operand" "=g")\
(truncate:HI (match\_operand:SI 1 "general\_operand" "g")))]\
""\
"cvtlw %1,%0")

(define\_insn "trunchiqi2"\
\[(set (match\_operand:QI 0 "general\_operand" "=g")\
(truncate:QI (match\_operand:HI 1 "general\_operand" "g")))]\
""\
"cvtwb %1,%0")

(define\_insn "extendhisi2"\
\[(set (match\_operand:SI 0 "general\_operand" "=g")\
(sign\_extend:SI (match\_operand:HI 1 "general\_operand" "g")))]\
""\
"cvtwl %1,%0")

(define\_insn "extendqihi2"\
\[(set (match\_operand:HI 0 "general\_operand" "=g")\
(sign\_extend:HI (match\_operand:QI 1 "general\_operand" "g")))]\
""\
"cvtbw %1,%0")

(define\_insn "extendqisi2"\
\[(set (match\_operand:SI 0 "general\_operand" "=g")\
(sign\_extend:SI (match\_operand:QI 1 "general\_operand" "g")))]\
""\
"cvtbl %1,%0")

(define\_insn "extendsfdf2"\
\[(set (match\_operand:DF 0 "general\_operand" "=g")\
(float\_extend:DF (match\_operand:SF 1 "general\_operand" "gF")))]\
""\
"cvtf%# %1,%0")

(define\_insn "truncdfsf2"\
\[(set (match\_operand:SF 0 "general\_operand" "=g")\
(float\_truncate:SF (match\_operand:DF 1 "general\_operand" "gF")))]\
""\
"cvt%#f %1,%0")

(define\_insn "zero\_extendhisi2"\
\[(set (match\_operand:SI 0 "general\_operand" "=g")\
(zero\_extend:SI (match\_operand:HI 1 "general\_operand" "g")))]\
""\
"movzwl %1,%0")

(define\_insn "zero\_extendqihi2"\
\[(set (match\_operand:HI 0 "general\_operand" "=g")\
(zero\_extend:HI (match\_operand:QI 1 "general\_operand" "g")))]\
""\
"movzbw %1,%0")

(define\_insn "zero\_extendqisi2"\
\[(set (match\_operand:SI 0 "general\_operand" "=g")\
(zero\_extend:SI (match\_operand:QI 1 "general\_operand" "g")))]\
""\
"movzbl %1,%0")\
;; Fix-to-float conversion insns.\
;; Note that the ones that start with SImode come first.\
;; That is so that an operand that is a CONST\_INT\
;; (and therefore lacks a specific machine mode).\
;; will be recognized as SImode (which is always valid)\
;; rather than as QImode or HImode.

(define\_insn "floatsisf2"\
\[(set (match\_operand:SF 0 "general\_operand" "=g")\
(float:SF (match\_operand:SI 1 "general\_operand" "g")))]\
""\
"cvtlf %1,%0")

(define\_insn "floatsidf2"\
\[(set (match\_operand:DF 0 "general\_operand" "=g")\
(float:DF (match\_operand:SI 1 "general\_operand" "g")))]\
""\
"cvtl%# %1,%0")

(define\_insn "floathisf2"\
\[(set (match\_operand:SF 0 "general\_operand" "=g")\
(float:SF (match\_operand:HI 1 "general\_operand" "g")))]\
""\
"cvtwf %1,%0")

(define\_insn "floathidf2"\
\[(set (match\_operand:DF 0 "general\_operand" "=g")\
(float:DF (match\_operand:HI 1 "general\_operand" "g")))]\
""\
"cvtw%# %1,%0")

(define\_insn "floatqisf2"\
\[(set (match\_operand:SF 0 "general\_operand" "=g")\
(float:SF (match\_operand:QI 1 "general\_operand" "g")))]\
""\
"cvtbf %1,%0")

(define\_insn "floatqidf2"\
\[(set (match\_operand:DF 0 "general\_operand" "=g")\
(float:DF (match\_operand:QI 1 "general\_operand" "g")))]\
""\
"cvtb%# %1,%0")\
;; Float-to-fix conversion insns.

(define\_insn "fix\_truncsfqi2"\
\[(set (match\_operand:QI 0 "general\_operand" "=g")\
(fix:QI (fix:SF (match\_operand:SF 1 "general\_operand" "gF"))))]\
""\
"cvtfb %1,%0")

(define\_insn "fix\_truncsfhi2"\
\[(set (match\_operand:HI 0 "general\_operand" "=g")\
(fix:HI (fix:SF (match\_operand:SF 1 "general\_operand" "gF"))))]\
""\
"cvtfw %1,%0")

(define\_insn "fix\_truncsfsi2"\
\[(set (match\_operand:SI 0 "general\_operand" "=g")\
(fix:SI (fix:SF (match\_operand:SF 1 "general\_operand" "gF"))))]\
""\
"cvtfl %1,%0")

(define\_insn "fix\_truncdfqi2"\
\[(set (match\_operand:QI 0 "general\_operand" "=g")\
(fix:QI (fix:DF (match\_operand:DF 1 "general\_operand" "gF"))))]\
""\
"cvt%#b %1,%0")

(define\_insn "fix\_truncdfhi2"\
\[(set (match\_operand:HI 0 "general\_operand" "=g")\
(fix:HI (fix:DF (match\_operand:DF 1 "general\_operand" "gF"))))]\
""\
"cvt%#w %1,%0")

(define\_insn "fix\_truncdfsi2"\
\[(set (match\_operand:SI 0 "general\_operand" "=g")\
(fix:SI (fix:DF (match\_operand:DF 1 "general\_operand" "gF"))))]\
""\
"cvt%#l %1,%0")\
;;- All kinds of add instructions.

(define\_insn "adddf3"\
\[(set (match\_operand:DF 0 "general\_operand" "=g")\
(plus:DF (match\_operand:DF 1 "general\_operand" "gF")\
(match\_operand:DF 2 "general\_operand" "gF")))]\
""\
"\*\
{\
if (rtx\_equal\_p (operands\[0], operands\[1]))\
return "add%#2 %2,%0";\
if (rtx\_equal\_p (operands\[0], operands\[2]))\
return "add%#2 %1,%0";\
return "add%#3 %1,%2,%0";\
}")

(define\_insn "addsf3"\
\[(set (match\_operand:SF 0 "general\_operand" "=g")\
(plus:SF (match\_operand:SF 1 "general\_operand" "gF")\
(match\_operand:SF 2 "general\_operand" "gF")))]\
""\
"\*\
{\
if (rtx\_equal\_p (operands\[0], operands\[1]))\
return "addf2 %2,%0";\
if (rtx\_equal\_p (operands\[0], operands\[2]))\
return "addf2 %1,%0";\
return "addf3 %1,%2,%0";\
}")

(define\_insn "addsi3"\
\[(set (match\_operand:SI 0 "general\_operand" "=g")\
(plus:SI (match\_operand:SI 1 "general\_operand" "g")\
(match\_operand:SI 2 "general\_operand" "g")))]\
""\
"\*\
{\
if (rtx\_equal\_p (operands\[0], operands\[1]))\
{\
if (operands\[2] == const1\_rtx)\
return "incl %0";\
if (GET\_CODE (operands\[2]) == CONST\_INT\
&& INTVAL (operands\[2]) == -1)\
return "decl %0";\
if (GET\_CODE (operands\[2]) == CONST\_INT\
&& (unsigned) (- INTVAL (operands\[2])) < 64)\
return "subl2 $%n2,%0";\
if (GET\_CODE (operands\[2]) == CONST\_INT\
&& (unsigned) INTVAL (operands\[2]) >= 64\
&& GET\_CODE (operands\[1]) == REG)\
return "movab %c2(%1),%0";\
return "addl2 %2,%0";\
}\
if (rtx\_equal\_p (operands\[0], operands\[2]))\
return "addl2 %1,%0";\
if (GET\_CODE (operands\[2]) == CONST\_INT\
&& (unsigned) (- INTVAL (operands\[2])) < 64)\
return "subl3 $%n2,%1,%0";\
if (GET\_CODE (operands\[2]) == CONST\_INT\
&& (unsigned) INTVAL (operands\[2]) >= 64\
&& GET\_CODE (operands\[1]) == REG)\
{\
if (push\_operand (operands\[0], SImode))\
return "pushab %c2(%1)";\
return "movab %c2(%1),%0";\
}\
return "addl3 %1,%2,%0";\
}")

(define\_insn "addhi3"\
\[(set (match\_operand:HI 0 "general\_operand" "=g")\
(plus:HI (match\_operand:HI 1 "general\_operand" "g")\
(match\_operand:HI 2 "general\_operand" "g")))]\
""\
"\*\
{\
if (rtx\_equal\_p (operands\[0], operands\[1]))\
{\
if (operands\[2] == const1\_rtx)\
return "incw %0";\
if (GET\_CODE (operands\[1]) == CONST\_INT\
&& INTVAL (operands\[1]) == -1)\
return "decw %0";\
if (GET\_CODE (operands\[2]) == CONST\_INT\
&& (unsigned) (- INTVAL (operands\[2])) < 64)\
return "subw2 $%n2,%0";\
return "addw2 %2,%0";\
}\
if (rtx\_equal\_p (operands\[0], operands\[2]))\
return "addw2 %1,%0";\
if (GET\_CODE (operands\[2]) == CONST\_INT\
&& (unsigned) (- INTVAL (operands\[2])) < 64)\
return "subw3 $%n2,%1,%0";\
return "addw3 %1,%2,%0";\
}")

(define\_insn "addqi3"\
\[(set (match\_operand:QI 0 "general\_operand" "=g")\
(plus:QI (match\_operand:QI 1 "general\_operand" "g")\
(match\_operand:QI 2 "general\_operand" "g")))]\
""\
"\*\
{\
if (rtx\_equal\_p (operands\[0], operands\[1]))\
{\
if (operands\[2] == const1\_rtx)\
return "incb %0";\
if (GET\_CODE (operands\[1]) == CONST\_INT\
&& INTVAL (operands\[1]) == -1)\
return "decb %0";\
if (GET\_CODE (operands\[2]) == CONST\_INT\
&& (unsigned) (- INTVAL (operands\[2])) < 64)\
return "subb2 $%n2,%0";\
return "addb2 %2,%0";\
}\
if (rtx\_equal\_p (operands\[0], operands\[2]))\
return "addb2 %1,%0";\
if (GET\_CODE (operands\[2]) == CONST\_INT\
&& (unsigned) (- INTVAL (operands\[2])) < 64)\
return "subb3 $%n2,%1,%0";\
return "addb3 %1,%2,%0";\
}")\
;;- All kinds of subtract instructions.

(define\_insn "subdf3"\
\[(set (match\_operand:DF 0 "general\_operand" "=g")\
(minus:DF (match\_operand:DF 1 "general\_operand" "gF")\
(match\_operand:DF 2 "general\_operand" "gF")))]\
""\
"\*\
{\
if (rtx\_equal\_p (operands\[0], operands\[1]))\
return "sub%#2 %2,%0";\
return "sub%#3 %2,%1,%0";\
}")

(define\_insn "subsf3"\
\[(set (match\_operand:SF 0 "general\_operand" "=g")\
(minus:SF (match\_operand:SF 1 "general\_operand" "gF")\
(match\_operand:SF 2 "general\_operand" "gF")))]\
""\
"\*\
{\
if (rtx\_equal\_p (operands\[0], operands\[1]))\
return "subf2 %2,%0";\
return "subf3 %2,%1,%0";\
}")

(define\_insn "subsi3"\
\[(set (match\_operand:SI 0 "general\_operand" "=g")\
(minus:SI (match\_operand:SI 1 "general\_operand" "g")\
(match\_operand:SI 2 "general\_operand" "g")))]\
""\
"\*\
{\
if (rtx\_equal\_p (operands\[0], operands\[1]))\
{\
if (operands\[2] == const1\_rtx)\
return "decl %0";\
return "subl2 %2,%0";\
}\
return "subl3 %2,%1,%0";\
}")

(define\_insn "subhi3"\
\[(set (match\_operand:HI 0 "general\_operand" "=g")\
(minus:HI (match\_operand:HI 1 "general\_operand" "g")\
(match\_operand:HI 2 "general\_operand" "g")))]\
""\
"\*\
{\
if (rtx\_equal\_p (operands\[0], operands\[1]))\
{\
if (operands\[2] == const1\_rtx)\
return "decw %0";\
return "subw2 %2,%0";\
}\
return "subw3 %2,%1,%0";\
}")

(define\_insn "subqi3"\
\[(set (match\_operand:QI 0 "general\_operand" "=g")\
(minus:QI (match\_operand:QI 1 "general\_operand" "g")\
(match\_operand:QI 2 "general\_operand" "g")))]\
""\
"\*\
{\
if (rtx\_equal\_p (operands\[0], operands\[1]))\
{\
if (operands\[2] == const1\_rtx)\
return "decb %0";\
return "subb2 %2,%0";\
}\
return "subb3 %2,%1,%0";\
}")\
;;- Multiply instructions.

(define\_insn "muldf3"\
\[(set (match\_operand:DF 0 "general\_operand" "=g")\
(mult:DF (match\_operand:DF 1 "general\_operand" "gF")\
(match\_operand:DF 2 "general\_operand" "gF")))]\
""\
"\*\
{\
if (rtx\_equal\_p (operands\[0], operands\[1]))\
return "mul%#2 %2,%0";\
if (rtx\_equal\_p (operands\[0], operands\[2]))\
return "mul%#2 %1,%0";\
return "mul%#3 %1,%2,%0";\
}")

(define\_insn "mulsf3"\
\[(set (match\_operand:SF 0 "general\_operand" "=g")\
(mult:SF (match\_operand:SF 1 "general\_operand" "gF")\
(match\_operand:SF 2 "general\_operand" "gF")))]\
""\
"\*\
{\
if (rtx\_equal\_p (operands\[0], operands\[1]))\
return "mulf2 %2,%0";\
if (rtx\_equal\_p (operands\[0], operands\[2]))\
return "mulf2 %1,%0";\
return "mulf3 %1,%2,%0";\
}")

(define\_insn "mulsi3"\
\[(set (match\_operand:SI 0 "general\_operand" "=g")\
(mult:SI (match\_operand:SI 1 "general\_operand" "g")\
(match\_operand:SI 2 "general\_operand" "g")))]\
""\
"\*\
{\
if (rtx\_equal\_p (operands\[0], operands\[1]))\
return "mull2 %2,%0";\
if (rtx\_equal\_p (operands\[0], operands\[2]))\
return "mull2 %1,%0";\
return "mull3 %1,%2,%0";\
}")

(define\_insn "mulhi3"\
\[(set (match\_operand:HI 0 "general\_operand" "=g")\
(mult:HI (match\_operand:HI 1 "general\_operand" "g")\
(match\_operand:HI 2 "general\_operand" "g")))]\
""\
"\*\
{\
if (rtx\_equal\_p (operands\[0], operands\[1]))\
return "mulw2 %2,%0";\
if (rtx\_equal\_p (operands\[0], operands\[2]))\
return "mulw2 %1,%0";\
return "mulw3 %1,%2,%0";\
}")

(define\_insn "mulqi3"\
\[(set (match\_operand:QI 0 "general\_operand" "=g")\
(mult:QI (match\_operand:QI 1 "general\_operand" "g")\
(match\_operand:QI 2 "general\_operand" "g")))]\
""\
"\*\
{\
if (rtx\_equal\_p (operands\[0], operands\[1]))\
return "mulb2 %2,%0";\
if (rtx\_equal\_p (operands\[0], operands\[2]))\
return "mulb2 %1,%0";\
return "mulb3 %1,%2,%0";\
}")\
;;- Divide instructions.

(define\_insn "divdf3"\
\[(set (match\_operand:DF 0 "general\_operand" "=g")\
(div:DF (match\_operand:DF 1 "general\_operand" "gF")\
(match\_operand:DF 2 "general\_operand" "gF")))]\
""\
"\*\
{\
if (rtx\_equal\_p (operands\[0], operands\[1]))\
return "div%#2 %2,%0";\
return "div%#3 %2,%1,%0";\
}")

(define\_insn "divsf3"\
\[(set (match\_operand:SF 0 "general\_operand" "=g")\
(div:SF (match\_operand:SF 1 "general\_operand" "gF")\
(match\_operand:SF 2 "general\_operand" "gF")))]\
""\
"\*\
{\
if (rtx\_equal\_p (operands\[0], operands\[1]))\
return "divf2 %2,%0";\
return "divf3 %2,%1,%0";\
}")

(define\_insn "divsi3"\
\[(set (match\_operand:SI 0 "general\_operand" "=g")\
(div:SI (match\_operand:SI 1 "general\_operand" "g")\
(match\_operand:SI 2 "general\_operand" "g")))]\
""\
"\*\
{\
if (rtx\_equal\_p (operands\[0], operands\[1]))\
return "divl2 %2,%0";\
return "divl3 %2,%1,%0";\
}")

(define\_insn "divhi3"\
\[(set (match\_operand:HI 0 "general\_operand" "=g")\
(div:HI (match\_operand:HI 1 "general\_operand" "g")\
(match\_operand:HI 2 "general\_operand" "g")))]\
""\
"\*\
{\
if (rtx\_equal\_p (operands\[0], operands\[1]))\
return "divw2 %2,%0";\
return "divw3 %2,%1,%0";\
}")

(define\_insn "divqi3"\
\[(set (match\_operand:QI 0 "general\_operand" "=g")\
(div:QI (match\_operand:QI 1 "general\_operand" "g")\
(match\_operand:QI 2 "general\_operand" "g")))]\
""\
"\*\
{\
if (rtx\_equal\_p (operands\[0], operands\[1]))\
return "divb2 %2,%0";\
return "divb3 %2,%1,%0";\
}")

;This is left out because it is very slow;\
;we are better off programming around the "lack" of this insn.\
;(define\_insn "divmoddisi4"\
; \[(set (match\_operand:SI 0 "general\_operand" "=g")\
; (div:SI (match\_operand:DI 1 "general\_operand" "g")\
; (match\_operand:SI 2 "general\_operand" "g")))\
; (set (match\_operand:SI 3 "general\_operand" "=g")\
; (mod:SI (match\_operand:DI 1 "general\_operand" "g")\
; (match\_operand:SI 2 "general\_operand" "g")))]\
; ""\
; "ediv %2,%1,%0,%3")\
;; Bit-and on the vax is done with a clear-bits insn.\
(define\_expand "andsi3"\
\[(set (match\_operand:SI 0 "general\_operand" "=g")\
(and:SI (match\_operand:SI 1 "general\_operand" "g")\
(not:SI (match\_operand:SI 2 "general\_operand" "g"))))]\
""\
"\
{\
extern rtx expand\_unop ();\
if (GET\_CODE (operands\[2]) == CONST\_INT)\
operands\[2] = gen\_rtx (CONST\_INT, VOIDmode, \~INTVAL (operands\[2]));\
else\
operands\[2] = expand\_unop (SImode, one\_cmpl\_optab, operands\[2], 0, 1);\
}")

(define\_expand "andhi3"\
\[(set (match\_operand:HI 0 "general\_operand" "=g")\
(and:HI (match\_operand:HI 1 "general\_operand" "g")\
(not:HI (match\_operand:HI 2 "general\_operand" "g"))))]\
""\
"\
{\
extern rtx expand\_unop ();\
rtx op = operands\[2];\
if (GET\_CODE (op) == CONST\_INT)\
operands\[2] = gen\_rtx (CONST\_INT, VOIDmode,\
((1 << 16) - 1) & \~INTVAL (op));\
else\
operands\[2] = expand\_unop (HImode, one\_cmpl\_optab, op, 0, 1);\
}")

(define\_expand "andqi3"\
\[(set (match\_operand:QI 0 "general\_operand" "=g")\
(and:QI (match\_operand:QI 1 "general\_operand" "g")\
(not:QI (match\_operand:QI 2 "general\_operand" "g"))))]\
""\
"\
{\
extern rtx expand\_unop ();\
rtx op = operands\[2];\
if (GET\_CODE (op) == CONST\_INT)\
operands\[2] = gen\_rtx (CONST\_INT, VOIDmode,\
((1 << 8) - 1) & \~INTVAL (op));\
else\
operands\[2] = expand\_unop (QImode, one\_cmpl\_optab, op, 0, 1);\
}")

(define\_insn "andcbsi3"\
\[(set (match\_operand:SI 0 "general\_operand" "=g")\
(and:SI (match\_operand:SI 1 "general\_operand" "g")\
(not:SI (match\_operand:SI 2 "general\_operand" "g"))))]\
""\
"\*\
{\
if (rtx\_equal\_p (operands\[0], operands\[1]))\
return "bicl2 %2,%0";\
return "bicl3 %2,%1,%0";\
}")

(define\_insn "andcbhi3"\
\[(set (match\_operand:HI 0 "general\_operand" "=g")\
(and:HI (match\_operand:HI 1 "general\_operand" "g")\
(not:HI (match\_operand:HI 2 "general\_operand" "g"))))]\
""\
"\*\
{\
if (rtx\_equal\_p (operands\[0], operands\[1]))\
return "bicw2 %2,%0";\
return "bicw3 %2,%1,%0";\
}")

(define\_insn "andcbqi3"\
\[(set (match\_operand:QI 0 "general\_operand" "=g")\
(and:QI (match\_operand:QI 1 "general\_operand" "g")\
(not:QI (match\_operand:QI 2 "general\_operand" "g"))))]\
""\
"\*\
{\
if (rtx\_equal\_p (operands\[0], operands\[1]))\
return "bicb2 %2,%0";\
return "bicb3 %2,%1,%0";\
}")

;; The following are needed because constant propagation can\
;; create them starting from the bic insn patterns above.

(define\_insn ""\
\[(set (match\_operand:SI 0 "general\_operand" "=g")\
(and:SI (match\_operand:SI 1 "general\_operand" "g")\
(match\_operand:SI 2 "general\_operand" "g")))]\
"GET\_CODE (operands\[2]) == CONST\_INT"\
"\*\
{ operands\[2] = gen\_rtx (CONST\_INT, VOIDmode, \~INTVAL (operands\[2]));\
if (rtx\_equal\_p (operands\[1], operands\[0]))\
return "bicl2 %2,%0";\
return "bicl3 %2,%1,%0";\
}")

(define\_insn ""\
\[(set (match\_operand:HI 0 "general\_operand" "=g")\
(and:HI (match\_operand:HI 1 "general\_operand" "g")\
(match\_operand:HI 2 "general\_operand" "g")))]\
"GET\_CODE (operands\[2]) == CONST\_INT"\
"\*\
{ operands\[2] = gen\_rtx (CONST\_INT, VOIDmode, 0xffff & \~INTVAL (operands\[2]));\
if (rtx\_equal\_p (operands\[1], operands\[0]))\
return "bicw2 %2,%0";\
return "bicw3 %2,%1,%0";\
}")

(define\_insn ""\
\[(set (match\_operand:QI 0 "general\_operand" "=g")\
(and:QI (match\_operand:QI 1 "general\_operand" "g")\
(match\_operand:QI 2 "general\_operand" "g")))]\
"GET\_CODE (operands\[2]) == CONST\_INT"\
"\*\
{ operands\[2] = gen\_rtx (CONST\_INT, VOIDmode, 0xff & \~INTVAL (operands\[2]));\
if (rtx\_equal\_p (operands\[1], operands\[0]))\
return "bicb2 %2,%0";\
return "bicb3 %2,%1,%0";\
}")\
;;- Bit set instructions.

(define\_insn "iorsi3"\
\[(set (match\_operand:SI 0 "general\_operand" "=g")\
(ior:SI (match\_operand:SI 1 "general\_operand" "g")\
(match\_operand:SI 2 "general\_operand" "g")))]\
""\
"\*\
{\
if (rtx\_equal\_p (operands\[0], operands\[1]))\
return "bisl2 %2,%0";\
if (rtx\_equal\_p (operands\[0], operands\[2]))\
return "bisl2 %1,%0";\
return "bisl3 %2,%1,%0";\
}")

(define\_insn "iorhi3"\
\[(set (match\_operand:HI 0 "general\_operand" "=g")\
(ior:HI (match\_operand:HI 1 "general\_operand" "g")\
(match\_operand:HI 2 "general\_operand" "g")))]\
""\
"\*\
{\
if (rtx\_equal\_p (operands\[0], operands\[1]))\
return "bisw2 %2,%0";\
if (rtx\_equal\_p (operands\[0], operands\[2]))\
return "bisw2 %1,%0";\
return "bisw3 %2,%1,%0";\
}")

(define\_insn "iorqi3"\
\[(set (match\_operand:QI 0 "general\_operand" "=g")\
(ior:QI (match\_operand:QI 1 "general\_operand" "g")\
(match\_operand:QI 2 "general\_operand" "g")))]\
""\
"\*\
{\
if (rtx\_equal\_p (operands\[0], operands\[1]))\
return "bisb2 %2,%0";\
if (rtx\_equal\_p (operands\[0], operands\[2]))\
return "bisb2 %1,%0";\
return "bisb3 %2,%1,%0";\
}")

;;- xor instructions.

(define\_insn "xorsi3"\
\[(set (match\_operand:SI 0 "general\_operand" "=g")\
(xor:SI (match\_operand:SI 1 "general\_operand" "g")\
(match\_operand:SI 2 "general\_operand" "g")))]\
""\
"\*\
{\
if (rtx\_equal\_p (operands\[0], operands\[1]))\
return "xorl2 %2,%0";\
if (rtx\_equal\_p (operands\[0], operands\[2]))\
return "xorl2 %1,%0";\
return "xorl3 %2,%1,%0";\
}")

(define\_insn "xorhi3"\
\[(set (match\_operand:HI 0 "general\_operand" "=g")\
(xor:HI (match\_operand:HI 1 "general\_operand" "g")\
(match\_operand:HI 2 "general\_operand" "g")))]\
""\
"\*\
{\
if (rtx\_equal\_p (operands\[0], operands\[1]))\
return "xorw2 %2,%0";\
if (rtx\_equal\_p (operands\[0], operands\[2]))\
return "xorw2 %1,%0";\
return "xorw3 %2,%1,%0";\
}")

(define\_insn "xorqi3"\
\[(set (match\_operand:QI 0 "general\_operand" "=g")\
(xor:QI (match\_operand:QI 1 "general\_operand" "g")\
(match\_operand:QI 2 "general\_operand" "g")))]\
""\
"\*\
{\
if (rtx\_equal\_p (operands\[0], operands\[1]))\
return "xorb2 %2,%0";\
if (rtx\_equal\_p (operands\[0], operands\[2]))\
return "xorb2 %1,%0";\
return "xorb3 %2,%1,%0";\
}")\
(define\_insn "negdf2"\
\[(set (match\_operand:DF 0 "general\_operand" "=g")\
(neg:DF (match\_operand:DF 1 "general\_operand" "gF")))]\
""\
"mneg%# %1,%0")

(define\_insn "negsf2"\
\[(set (match\_operand:SF 0 "general\_operand" "=g")\
(neg:SF (match\_operand:SF 1 "general\_operand" "gF")))]\
""\
"mnegf %1,%0")

(define\_insn "negsi2"\
\[(set (match\_operand:SI 0 "general\_operand" "=g")\
(neg:SI (match\_operand:SI 1 "general\_operand" "g")))]\
""\
"mnegl %1,%0")

(define\_insn "neghi2"\
\[(set (match\_operand:HI 0 "general\_operand" "=g")\
(neg:HI (match\_operand:HI 1 "general\_operand" "g")))]\
""\
"mnegw %1,%0")

(define\_insn "negqi2"\
\[(set (match\_operand:QI 0 "general\_operand" "=g")\
(neg:QI (match\_operand:QI 1 "general\_operand" "g")))]\
""\
"mnegb %1,%0")\
(define\_insn "one\_cmplsi2"\
\[(set (match\_operand:SI 0 "general\_operand" "=g")\
(not:SI (match\_operand:SI 1 "general\_operand" "g")))]\
""\
"mcoml %1,%0")

(define\_insn "one\_cmplhi2"\
\[(set (match\_operand:HI 0 "general\_operand" "=g")\
(not:HI (match\_operand:HI 1 "general\_operand" "g")))]\
""\
"mcomw %1,%0")

(define\_insn "one\_cmplqi2"\
\[(set (match\_operand:QI 0 "general\_operand" "=g")\
(not:QI (match\_operand:QI 1 "general\_operand" "g")))]\
""\
"mcomb %1,%0")\
;; Arithmetic right shift on the vax works by negating the shift count.\
(define\_expand "ashrsi3"\
\[(set (match\_operand:SI 0 "general\_operand" "=g")\
(ashift:SI (match\_operand:SI 1 "general\_operand" "g")\
(match\_operand:QI 2 "general\_operand" "g")))]\
""\
"\
{\
operands\[2] = negate\_rtx (QImode, operands\[2]);\
}")

(define\_insn "ashlsi3"\
\[(set (match\_operand:SI 0 "general\_operand" "=g")\
(ashift:SI (match\_operand:SI 1 "general\_operand" "g")\
(match\_operand:QI 2 "general\_operand" "g")))]\
""\
"\*\
{\
if (operands\[2] == const1\_rtx && rtx\_equal\_p (operands\[0], operands\[1]))\
return "addl2 %0,%0";\
if (GET\_CODE (operands\[1]) == REG\
&& GET\_CODE (operands\[2]) == CONST\_INT)\
{\
int i = INTVAL (operands\[2]);\
if (i == 1)\
return "addl3 %1,%1,%0";\
if (i == 2)\
return "moval 0\[%1],%0";\
if (i == 3)\
return "movad 0\[%1],%0";\
}\
return "ashl %2,%1,%0";\
}")

;; Arithmetic right shift on the vax works by negating the shift count.\
(define\_expand "ashrdi3"\
\[(set (match\_operand:DI 0 "general\_operand" "=g")\
(ashift:DI (match\_operand:DI 1 "general\_operand" "g")\
(match\_operand:QI 2 "general\_operand" "g")))]\
""\
"\
{\
operands\[2] = negate\_rtx (QImode, operands\[2]);\
}")

(define\_insn "ashldi3"\
\[(set (match\_operand:DI 0 "general\_operand" "=g")\
(ashift:DI (match\_operand:DI 1 "general\_operand" "g")\
(match\_operand:QI 2 "general\_operand" "g")))]\
""\
"ashq %2,%1,%0")

;; Rotate right on the vax works by negating the shift count.\
(define\_expand "rotrsi3"\
\[(set (match\_operand:SI 0 "general\_operand" "=g")\
(rotate:SI (match\_operand:SI 1 "general\_operand" "g")\
(match\_operand:QI 2 "general\_operand" "g")))]\
""\
"\
{\
operands\[2] = negate\_rtx (QImode, operands\[2]);\
}")

(define\_insn "rotlsi3"\
\[(set (match\_operand:SI 0 "general\_operand" "=g")\
(rotate:SI (match\_operand:SI 1 "general\_operand" "g")\
(match\_operand:QI 2 "general\_operand" "g")))]\
""\
"rotl %2,%1,%0")

;This insn is probably slower than a multiply and an add.\
;(define\_insn ""\
; \[(set (match\_operand:SI 0 "general\_operand" "=g")\
; (mult:SI (plus:SI (match\_operand:SI 1 "general\_operand" "g")\
; (match\_operand:SI 2 "general\_operand" "g"))\
; (match\_operand:SI 3 "general\_operand" "g")))]\
; ""\
; "index %1,$0x80000000,$0x7fffffff,%3,%2,%0")\
;; Special cases of bit-field insns which we should\
;; recognize in preference to the general case.\
;; These handle aligned 8-bit and 16-bit fields,\
;; which can usually be done with move instructions.

(define\_insn ""\
\[(set (zero\_extract:SI (match\_operand:SI 0 "general\_operand" "+ro")\
(match\_operand:SI 1 "immediate\_operand" "i")\
(match\_operand:SI 2 "immediate\_operand" "i"))\
(match\_operand:SI 3 "general\_operand" "g"))]\
"GET\_CODE (operands\[1]) == CONST\_INT\
&& (INTVAL (operands\[1]) == 8 || INTVAL (operands\[1]) == 16)\
&& GET\_CODE (operands\[2]) == CONST\_INT\
&& INTVAL (operands\[2]) % INTVAL (operands\[1]) == 0\
&& (GET\_CODE (operands\[0]) == REG\
|| ! mode\_dependent\_address\_p (XEXP (operands\[0], 0)))"\
"\*\
{\
if (REG\_P (operands\[0]))\
{\
if (INTVAL (operands\[2]) != 0)\
return "insv %3,%2,%1,%0";\
}\
else\
operands\[0]\
\= adj\_offsettable\_operand (operands\[0], INTVAL (operands\[2]) / 8);

if (INTVAL (operands\[1]) == 8)\
return "movb %3,%0";\
return "movw %3,%0";\
}")

(define\_insn ""\
\[(set (match\_operand:SI 0 "general\_operand" "=\&g")\
(zero\_extract:SI (match\_operand:SI 1 "general\_operand" "ro")\
(match\_operand:SI 2 "immediate\_operand" "i")\
(match\_operand:SI 3 "immediate\_operand" "i")))]\
"GET\_CODE (operands\[2]) == CONST\_INT\
&& (INTVAL (operands\[2]) == 8 || INTVAL (operands\[2]) == 16)\
&& GET\_CODE (operands\[3]) == CONST\_INT\
&& INTVAL (operands\[3]) % INTVAL (operands\[2]) == 0\
&& (GET\_CODE (operands\[1]) == REG\
|| ! mode\_dependent\_address\_p (XEXP (operands\[1], 0)))"\
"\*\
{\
if (REG\_P (operands\[1]))\
{\
if (INTVAL (operands\[3]) != 0)\
return "extzv %3,%2,%1,%0";\
}\
else\
operands\[1]\
\= adj\_offsettable\_operand (operands\[1], INTVAL (operands\[3]) / 8);

if (INTVAL (operands\[2]) == 8)\
return "movzbl %1,%0";\
return "movzwl %1,%0";\
}")

(define\_insn ""\
\[(set (match\_operand:SI 0 "general\_operand" "=g")\
(sign\_extract:SI (match\_operand:SI 1 "general\_operand" "ro")\
(match\_operand:SI 2 "immediate\_operand" "i")\
(match\_operand:SI 3 "immediate\_operand" "i")))]\
"GET\_CODE (operands\[2]) == CONST\_INT\
&& (INTVAL (operands\[2]) == 8 || INTVAL (operands\[2]) == 16)\
&& GET\_CODE (operands\[3]) == CONST\_INT\
&& INTVAL (operands\[3]) % INTVAL (operands\[2]) == 0\
&& (GET\_CODE (operands\[1]) == REG\
|| ! mode\_dependent\_address\_p (XEXP (operands\[1], 0)))"\
"\*\
{\
if (REG\_P (operands\[1]))\
{\
if (INTVAL (operands\[3]) != 0)\
return "extv %3,%2,%1,%0";\
}\
else\
operands\[1]\
\= adj\_offsettable\_operand (operands\[1], INTVAL (operands\[3]) / 8);

if (INTVAL (operands\[2]) == 8)\
return "cvtbl %1,%0";\
return "cvtwl %1,%0";\
}")\
;; Register-only SImode cases of bit-field insns.

(define\_insn ""\
\[(set (cc0)\
(compare\
(sign\_extract:SI (match\_operand:SI 0 "general\_operand" "r")\
(match\_operand:SI 1 "general\_operand" "g")\
(match\_operand:SI 2 "general\_operand" "g"))\
(match\_operand:SI 3 "general\_operand" "g")))]\
""\
"cmpv %2,%1,%0,%3")

(define\_insn ""\
\[(set (cc0)\
(compare\
(zero\_extract:SI (match\_operand:SI 0 "general\_operand" "r")\
(match\_operand:SI 1 "general\_operand" "g")\
(match\_operand:SI 2 "general\_operand" "g"))\
(match\_operand:SI 3 "general\_operand" "g")))]\
""\
"cmpzv %2,%1,%0,%3")

(define\_insn ""\
\[(set (match\_operand:SI 0 "general\_operand" "=g")\
(sign\_extract:SI (match\_operand:SI 1 "general\_operand" "r")\
(match\_operand:SI 2 "general\_operand" "g")\
(match\_operand:SI 3 "general\_operand" "g")))]\
""\
"extv %3,%2,%1,%0")

(define\_insn ""\
\[(set (match\_operand:SI 0 "general\_operand" "=g")\
(zero\_extract:SI (match\_operand:SI 1 "general\_operand" "r")\
(match\_operand:SI 2 "general\_operand" "g")\
(match\_operand:SI 3 "general\_operand" "g")))]\
""\
"extzv %3,%2,%1,%0")

;; Non-register cases.\
;; nonimmediate\_operand is used to make sure that mode-ambiguous cases\
;; don't match these (and therefore match the cases above instead).

(define\_insn ""\
\[(set (cc0)\
(compare\
(sign\_extract:SI (match\_operand:QI 0 "nonimmediate\_operand" "rm")\
(match\_operand:SI 1 "general\_operand" "g")\
(match\_operand:SI 2 "general\_operand" "g"))\
(match\_operand:SI 3 "general\_operand" "g")))]\
""\
"cmpv %2,%1,%0,%3")

(define\_insn ""\
\[(set (cc0)\
(compare\
(zero\_extract:SI (match\_operand:QI 0 "nonimmediate\_operand" "rm")\
(match\_operand:SI 1 "general\_operand" "g")\
(match\_operand:SI 2 "general\_operand" "g"))\
(match\_operand:SI 3 "general\_operand" "g")))]\
""\
"cmpzv %2,%1,%0,%3")

(define\_insn "extv"\
\[(set (match\_operand:SI 0 "general\_operand" "=g")\
(sign\_extract:SI (match\_operand:QI 1 "nonimmediate\_operand" "rm")\
(match\_operand:SI 2 "general\_operand" "g")\
(match\_operand:SI 3 "general\_operand" "g")))]\
""\
"extv %3,%2,%1,%0")

(define\_insn "extzv"\
\[(set (match\_operand:SI 0 "general\_operand" "=g")\
(zero\_extract:SI (match\_operand:QI 1 "nonimmediate\_operand" "rm")\
(match\_operand:SI 2 "general\_operand" "g")\
(match\_operand:SI 3 "general\_operand" "g")))]\
""\
"extzv %3,%2,%1,%0")

(define\_insn "insv"\
\[(set (zero\_extract:SI (match\_operand:QI 0 "general\_operand" "+g")\
(match\_operand:SI 1 "general\_operand" "g")\
(match\_operand:SI 2 "general\_operand" "g"))\
(match\_operand:SI 3 "general\_operand" "g"))]\
""\
"insv %3,%2,%1,%0")

(define\_insn ""\
\[(set (zero\_extract:SI (match\_operand:SI 0 "register\_operand" "+r")\
(match\_operand:SI 1 "general\_operand" "g")\
(match\_operand:SI 2 "general\_operand" "g"))\
(match\_operand:SI 3 "general\_operand" "g"))]\
""\
"insv %3,%2,%1,%0")\
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
"jeql %l0")

(define\_insn "bne"\
\[(set (pc)\
(if\_then\_else (ne (cc0)\
(const\_int 0))\
(label\_ref (match\_operand 0 "" ""))\
(pc)))]\
""\
"jneq %l0")

(define\_insn "bgt"\
\[(set (pc)\
(if\_then\_else (gt (cc0)\
(const\_int 0))\
(label\_ref (match\_operand 0 "" ""))\
(pc)))]\
""\
"jgtr %l0")

(define\_insn "bgtu"\
\[(set (pc)\
(if\_then\_else (gtu (cc0)\
(const\_int 0))\
(label\_ref (match\_operand 0 "" ""))\
(pc)))]\
""\
"jgtru %l0")

(define\_insn "blt"\
\[(set (pc)\
(if\_then\_else (lt (cc0)\
(const\_int 0))\
(label\_ref (match\_operand 0 "" ""))\
(pc)))]\
""\
"jlss %l0")

(define\_insn "bltu"\
\[(set (pc)\
(if\_then\_else (ltu (cc0)\
(const\_int 0))\
(label\_ref (match\_operand 0 "" ""))\
(pc)))]\
""\
"jlssu %l0")

(define\_insn "bge"\
\[(set (pc)\
(if\_then\_else (ge (cc0)\
(const\_int 0))\
(label\_ref (match\_operand 0 "" ""))\
(pc)))]\
""\
"jgeq %l0")

(define\_insn "bgeu"\
\[(set (pc)\
(if\_then\_else (geu (cc0)\
(const\_int 0))\
(label\_ref (match\_operand 0 "" ""))\
(pc)))]\
""\
"jgequ %l0")

(define\_insn "ble"\
\[(set (pc)\
(if\_then\_else (le (cc0)\
(const\_int 0))\
(label\_ref (match\_operand 0 "" ""))\
(pc)))]\
""\
"jleq %l0")

(define\_insn "bleu"\
\[(set (pc)\
(if\_then\_else (leu (cc0)\
(const\_int 0))\
(label\_ref (match\_operand 0 "" ""))\
(pc)))]\
""\
"jlequ %l0")\
(define\_insn ""\
\[(set (pc)\
(if\_then\_else (eq (cc0)\
(const\_int 0))\
(pc)\
(label\_ref (match\_operand 0 "" ""))))]\
""\
"jneq %l0")

(define\_insn ""\
\[(set (pc)\
(if\_then\_else (ne (cc0)\
(const\_int 0))\
(pc)\
(label\_ref (match\_operand 0 "" ""))))]\
""\
"jeql %l0")

(define\_insn ""\
\[(set (pc)\
(if\_then\_else (gt (cc0)\
(const\_int 0))\
(pc)\
(label\_ref (match\_operand 0 "" ""))))]\
""\
"jleq %l0")

(define\_insn ""\
\[(set (pc)\
(if\_then\_else (gtu (cc0)\
(const\_int 0))\
(pc)\
(label\_ref (match\_operand 0 "" ""))))]\
""\
"jlequ %l0")

(define\_insn ""\
\[(set (pc)\
(if\_then\_else (lt (cc0)\
(const\_int 0))\
(pc)\
(label\_ref (match\_operand 0 "" ""))))]\
""\
"jgeq %l0")

(define\_insn ""\
\[(set (pc)\
(if\_then\_else (ltu (cc0)\
(const\_int 0))\
(pc)\
(label\_ref (match\_operand 0 "" ""))))]\
""\
"jgequ %l0")

(define\_insn ""\
\[(set (pc)\
(if\_then\_else (ge (cc0)\
(const\_int 0))\
(pc)\
(label\_ref (match\_operand 0 "" ""))))]\
""\
"jlss %l0")

(define\_insn ""\
\[(set (pc)\
(if\_then\_else (geu (cc0)\
(const\_int 0))\
(pc)\
(label\_ref (match\_operand 0 "" ""))))]\
""\
"jlssu %l0")

(define\_insn ""\
\[(set (pc)\
(if\_then\_else (le (cc0)\
(const\_int 0))\
(pc)\
(label\_ref (match\_operand 0 "" ""))))]\
""\
"jgtr %l0")

(define\_insn ""\
\[(set (pc)\
(if\_then\_else (leu (cc0)\
(const\_int 0))\
(pc)\
(label\_ref (match\_operand 0 "" ""))))]\
""\
"jgtru %l0")\
;; Recognize jlbs and jlbc insns.\
;; These come before the jbc and jbs recognizers so these will be preferred.

(define\_insn ""\
\[(set (pc)\
(if\_then\_else\
(ne (and:SI (match\_operand:SI 0 "general\_operand" "g")\
(const\_int 1))\
(const\_int 0))\
(label\_ref (match\_operand 1 "" ""))\
(pc)))]\
"GET\_CODE (operands\[0]) != MEM\
|| ! mode\_dependent\_address\_p (XEXP (operands\[0], 0))"\
"jlbs %0,%l1")

(define\_insn ""\
\[(set (pc)\
(if\_then\_else\
(eq (and:SI (match\_operand:SI 0 "general\_operand" "g")\
(const\_int 1))\
(const\_int 0))\
(label\_ref (match\_operand 1 "" ""))\
(pc)))]\
"GET\_CODE (operands\[0]) != MEM\
|| ! mode\_dependent\_address\_p (XEXP (operands\[0], 0))"\
"jlbc %0,%l1")

(define\_insn ""\
\[(set (pc)\
(if\_then\_else\
(ne (and:SI (match\_operand:SI 0 "general\_operand" "g")\
(const\_int 1))\
(const\_int 0))\
(pc)\
(label\_ref (match\_operand 1 "" ""))))]\
"GET\_CODE (operands\[0]) != MEM\
|| ! mode\_dependent\_address\_p (XEXP (operands\[0], 0))"\
"jlbc %0,%l1")

(define\_insn ""\
\[(set (pc)\
(if\_then\_else\
(eq (and:SI (match\_operand:SI 0 "general\_operand" "g")\
(const\_int 1))\
(const\_int 0))\
(pc)\
(label\_ref (match\_operand 1 "" ""))))]\
"GET\_CODE (operands\[0]) != MEM\
|| ! mode\_dependent\_address\_p (XEXP (operands\[0], 0))"\
"jlbs %0,%l1")

;; These four entries allow a jlbc or jlbs to be made\
;; by combination with a bic.\
(define\_insn ""\
\[(set (pc)\
(if\_then\_else\
(ne (and:SI (match\_operand:SI 0 "general\_operand" "g")\
(not:SI (const\_int -2)))\
(const\_int 0))\
(label\_ref (match\_operand 1 "" ""))\
(pc)))]\
"GET\_CODE (operands\[0]) != MEM\
|| ! mode\_dependent\_address\_p (XEXP (operands\[0], 0))"\
"jlbs %0,%l1")

(define\_insn ""\
\[(set (pc)\
(if\_then\_else\
(eq (and:SI (match\_operand:SI 0 "general\_operand" "g")\
(not:SI (const\_int -2)))\
(const\_int 0))\
(label\_ref (match\_operand 1 "" ""))\
(pc)))]\
"GET\_CODE (operands\[0]) != MEM\
|| ! mode\_dependent\_address\_p (XEXP (operands\[0], 0))"\
"jlbc %0,%l1")

(define\_insn ""\
\[(set (pc)\
(if\_then\_else\
(ne (and:SI (match\_operand:SI 0 "general\_operand" "g")\
(not:SI (const\_int -2)))\
(const\_int 0))\
(pc)\
(label\_ref (match\_operand 1 "" ""))))]\
"GET\_CODE (operands\[0]) != MEM\
|| ! mode\_dependent\_address\_p (XEXP (operands\[0], 0))"\
"jlbc %0,%l1")

(define\_insn ""\
\[(set (pc)\
(if\_then\_else\
(eq (and:SI (match\_operand:SI 0 "general\_operand" "g")\
(not:SI (const\_int -2)))\
(const\_int 0))\
(pc)\
(label\_ref (match\_operand 1 "" ""))))]\
"GET\_CODE (operands\[0]) != MEM\
|| ! mode\_dependent\_address\_p (XEXP (operands\[0], 0))"\
"jlbs %0,%l1")\
;; Recognize jbs and jbc instructions.

(define\_insn ""\
\[(set (pc)\
(if\_then\_else\
(ne (sign\_extract:SI (match\_operand:QI 0 "general\_operand" "g")\
(const\_int 1)\
(match\_operand:SI 1 "general\_operand" "g"))\
(const\_int 0))\
(label\_ref (match\_operand 2 "" ""))\
(pc)))]\
""\
"jbs %1,%0,%l2")

(define\_insn ""\
\[(set (pc)\
(if\_then\_else\
(eq (sign\_extract:SI (match\_operand:QI 0 "general\_operand" "g")\
(const\_int 1)\
(match\_operand:SI 1 "general\_operand" "g"))\
(const\_int 0))\
(label\_ref (match\_operand 2 "" ""))\
(pc)))]\
""\
"jbc %1,%0,%l2")

(define\_insn ""\
\[(set (pc)\
(if\_then\_else\
(ne (sign\_extract:SI (match\_operand:QI 0 "general\_operand" "g")\
(const\_int 1)\
(match\_operand:SI 1 "general\_operand" "g"))\
(const\_int 0))\
(pc)\
(label\_ref (match\_operand 2 "" ""))))]\
""\
"jbc %1,%0,%l2")

(define\_insn ""\
\[(set (pc)\
(if\_then\_else\
(eq (sign\_extract:SI (match\_operand:QI 0 "general\_operand" "g")\
(const\_int 1)\
(match\_operand:SI 1 "general\_operand" "g"))\
(const\_int 0))\
(pc)\
(label\_ref (match\_operand 2 "" ""))))]\
""\
"jbs %1,%0,%l2")

(define\_insn ""\
\[(set (pc)\
(if\_then\_else\
(ne (sign\_extract:SI (match\_operand:SI 0 "general\_operand" "r")\
(const\_int 1)\
(match\_operand:SI 1 "general\_operand" "g"))\
(const\_int 0))\
(label\_ref (match\_operand 2 "" ""))\
(pc)))]\
"GET\_CODE (operands\[0]) != MEM\
|| ! mode\_dependent\_address\_p (XEXP (operands\[0], 0))"\
"jbs %1,%0,%l2")

(define\_insn ""\
\[(set (pc)\
(if\_then\_else\
(eq (sign\_extract:SI (match\_operand:SI 0 "general\_operand" "r")\
(const\_int 1)\
(match\_operand:SI 1 "general\_operand" "g"))\
(const\_int 0))\
(label\_ref (match\_operand 2 "" ""))\
(pc)))]\
"GET\_CODE (operands\[0]) != MEM\
|| ! mode\_dependent\_address\_p (XEXP (operands\[0], 0))"\
"jbc %1,%0,%l2")

(define\_insn ""\
\[(set (pc)\
(if\_then\_else\
(eq (and:SI (match\_operand:SI 0 "general\_operand" "g")\
(match\_operand:SI 1 "general\_operand" "g"))\
(const\_int 0))\
(pc)\
(label\_ref (match\_operand 2 "" ""))))]\
"GET\_CODE (operands\[1]) == CONST\_INT\
&& exact\_log2 (INTVAL (operands\[1])) >= 0\
&& (GET\_CODE (operands\[0]) != MEM\
|| ! mode\_dependent\_address\_p (XEXP (operands\[0], 0)))"\
"\*\
{\
operands\[1]\
\= gen\_rtx (CONST\_INT, VOIDmode, exact\_log2 (INTVAL (operands\[1])));\
return "jbs %1,%0,%l2";\
}")

(define\_insn ""\
\[(set (pc)\
(if\_then\_else\
(eq (and:SI (match\_operand:SI 0 "general\_operand" "g")\
(match\_operand:SI 1 "general\_operand" "g"))\
(const\_int 0))\
(label\_ref (match\_operand 2 "" ""))\
(pc)))]\
"GET\_CODE (operands\[1]) == CONST\_INT\
&& exact\_log2 (INTVAL (operands\[1])) >= 0\
&& (GET\_CODE (operands\[0]) != MEM\
|| ! mode\_dependent\_address\_p (XEXP (operands\[0], 0)))"\
"\*\
{\
operands\[1]\
\= gen\_rtx (CONST\_INT, VOIDmode, exact\_log2 (INTVAL (operands\[1])));\
return "jbc %1,%0,%l2";\
}")

(define\_insn ""\
\[(set (pc)\
(if\_then\_else\
(ne (and:SI (match\_operand:SI 0 "general\_operand" "g")\
(match\_operand:SI 1 "general\_operand" "g"))\
(const\_int 0))\
(pc)\
(label\_ref (match\_operand 2 "" ""))))]\
"GET\_CODE (operands\[1]) == CONST\_INT\
&& exact\_log2 (INTVAL (operands\[1])) >= 0\
&& (GET\_CODE (operands\[0]) != MEM\
|| ! mode\_dependent\_address\_p (XEXP (operands\[0], 0)))"\
"\*\
{\
operands\[1]\
\= gen\_rtx (CONST\_INT, VOIDmode, exact\_log2 (INTVAL (operands\[1])));\
return "jbc %1,%0,%l2";\
}")

(define\_insn ""\
\[(set (pc)\
(if\_then\_else\
(ne (and:SI (match\_operand:SI 0 "general\_operand" "g")\
(match\_operand:SI 1 "general\_operand" "g"))\
(const\_int 0))\
(label\_ref (match\_operand 2 "" ""))\
(pc)))]\
"GET\_CODE (operands\[1]) == CONST\_INT\
&& exact\_log2 (INTVAL (operands\[1])) >= 0\
&& (GET\_CODE (operands\[0]) != MEM\
|| ! mode\_dependent\_address\_p (XEXP (operands\[0], 0)))"\
"\*\
{\
operands\[1]\
\= gen\_rtx (CONST\_INT, VOIDmode, exact\_log2 (INTVAL (operands\[1])));\
return "jbs %1,%0,%l2";\
}")

(define\_insn ""\
\[(set (pc)\
(if\_then\_else\
(ne (sign\_extract:SI (match\_operand:SI 0 "general\_operand" "r")\
(const\_int 1)\
(match\_operand:SI 1 "general\_operand" "g"))\
(const\_int 0))\
(pc)\
(label\_ref (match\_operand 2 "" ""))))]\
"GET\_CODE (operands\[0]) != MEM\
|| ! mode\_dependent\_address\_p (XEXP (operands\[0], 0))"\
"jbc %1,%0,%l2")

(define\_insn ""\
\[(set (pc)\
(if\_then\_else\
(eq (sign\_extract:SI (match\_operand:SI 0 "general\_operand" "r")\
(const\_int 1)\
(match\_operand:SI 1 "general\_operand" "g"))\
(const\_int 0))\
(pc)\
(label\_ref (match\_operand 2 "" ""))))]\
"GET\_CODE (operands\[0]) != MEM\
|| ! mode\_dependent\_address\_p (XEXP (operands\[0], 0))"\
"jbs %1,%0,%l2")\
;; Subtract-and-jump and Add-and-jump insns.\
;; These are not used when output is for the Unix assembler\
;; because it does not know how to modify them to reach far.

;; Normal sob insns.

(define\_insn ""\
\[(set (pc)\
(if\_then\_else\
(gt (plus:SI (match\_operand:SI 0 "general\_operand" "+g")\
(const\_int -1))\
(const\_int 0))\
(label\_ref (match\_operand 1 "" ""))\
(pc)))\
(set (match\_dup 0)\
(plus:SI (match\_dup 0)\
(const\_int -1)))]\
"!TARGET\_UNIX\_ASM"\
"jsobgtr %0,%l1")

(define\_insn ""\
\[(set (pc)\
(if\_then\_else\
(ge (plus:SI (match\_operand:SI 0 "general\_operand" "+g")\
(const\_int -1))\
(const\_int 0))\
(label\_ref (match\_operand 1 "" ""))\
(pc)))\
(set (match\_dup 0)\
(plus:SI (match\_dup 0)\
(const\_int -1)))]\
"!TARGET\_UNIX\_ASM"\
"jsobgeq %0,%l1")

;; Reversed sob insns.

(define\_insn ""\
\[(set (pc)\
(if\_then\_else\
(le (plus:SI (match\_operand:SI 0 "general\_operand" "+g")\
(const\_int -1))\
(const\_int 0))\
(pc)\
(label\_ref (match\_operand 1 "" ""))))\
(set (match\_dup 0)\
(plus:SI (match\_dup 0)\
(const\_int -1)))]\
"!TARGET\_UNIX\_ASM"\
"jsobgtr %0,%l1")

(define\_insn ""\
\[(set (pc)\
(if\_then\_else\
(lt (plus:SI (match\_operand:SI 0 "general\_operand" "+g")\
(const\_int -1))\
(const\_int 0))\
(pc)\
(label\_ref (match\_operand 1 "" ""))))\
(set (match\_dup 0)\
(plus:SI (match\_dup 0)\
(const\_int -1)))]\
"!TARGET\_UNIX\_ASM"\
"jsobgeq %0,%l1")

;; Normal aob insns.\
(define\_insn ""\
\[(set (pc)\
(if\_then\_else\
(lt (compare (plus:SI (match\_operand:SI 0 "general\_operand" "+g")\
(const\_int 1))\
(match\_operand:SI 1 "general\_operand" "g"))\
(const\_int 0))\
(label\_ref (match\_operand 2 "" ""))\
(pc)))\
(set (match\_dup 0)\
(plus:SI (match\_dup 0)\
(const\_int 1)))]\
"!TARGET\_UNIX\_ASM"\
"jaoblss %1,%0,%l2")

(define\_insn ""\
\[(set (pc)\
(if\_then\_else\
(le (compare (plus:SI (match\_operand:SI 0 "general\_operand" "+g")\
(const\_int 1))\
(match\_operand:SI 1 "general\_operand" "g"))\
(const\_int 0))\
(label\_ref (match\_operand 2 "" ""))\
(pc)))\
(set (match\_dup 0)\
(plus:SI (match\_dup 0)\
(const\_int 1)))]\
"!TARGET\_UNIX\_ASM"\
"jaobleq %1,%0,%l2")

;; Reverse aob insns.\
(define\_insn ""\
\[(set (pc)\
(if\_then\_else\
(ge (compare (plus:SI (match\_operand:SI 0 "general\_operand" "+g")\
(const\_int 1))\
(match\_operand:SI 1 "general\_operand" "g"))\
(const\_int 0))\
(pc)\
(label\_ref (match\_operand 2 "" ""))))\
(set (match\_dup 0)\
(plus:SI (match\_dup 0)\
(const\_int 1)))]\
"!TARGET\_UNIX\_ASM"\
"jaoblss %1,%0,%l2")

(define\_insn ""\
\[(set (pc)\
(if\_then\_else\
(gt (compare (plus:SI (match\_operand:SI 0 "general\_operand" "+g")\
(const\_int 1))\
(match\_operand:SI 1 "general\_operand" "g"))\
(const\_int 0))\
(pc)\
(label\_ref (match\_operand 2 "" ""))))\
(set (match\_dup 0)\
(plus:SI (match\_dup 0)\
(const\_int 1)))]\
"!TARGET\_UNIX\_ASM"\
"jaobleq %1,%0,%l2")

;; Something like a sob insn, but compares against -1.\
;; This finds `while (foo--)' which was changed to` while (--foo != -1)'.

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
"decl %0;jgequ %l1")\
;; Note that operand 1 is total size of args, in bytes,\
;; and what the call insn wants is the number of words.\
(define\_insn "call"\
\[(call (match\_operand:QI 0 "general\_operand" "g")\
(match\_operand:QI 1 "general\_operand" "g"))]\
""\
"\*\
if (INTVAL (operands\[1]) > 255 \* 4)\
/\* Vax \`calls' really uses only one byte of #args, so pop explicitly. \*/\
return "calls $0,%0;addl2 %1,sp";\
operands\[1] = gen\_rtx (CONST\_INT, VOIDmode, (INTVAL (operands\[1]) + 3)/ 4);\
return "calls %1,%0";\
")

(define\_insn "call\_value"\
\[(set (match\_operand 0 "" "=g")\
(call (match\_operand:QI 1 "general\_operand" "g")\
(match\_operand:QI 2 "general\_operand" "g")))]\
""\
"\*\
if (INTVAL (operands\[2]) > 255 \* 4)\
/\* Vax \`calls' really uses only one byte of #args, so pop explicitly. \*/\
return "calls $0,%1;addl2 %2,sp";\
operands\[2] = gen\_rtx (CONST\_INT, VOIDmode, (INTVAL (operands\[2]) + 3)/ 4);\
return "calls %2,%1";\
")

(define\_insn "return"\
\[(return)]\
""\
"ret")

(define\_insn "nop"\
\[(const\_int 0)]\
""\
"nop")

(define\_insn "casesi"\
\[(set (pc)\
(if\_then\_else (le (minus:SI (match\_operand:SI 0 "general\_operand" "g")\
(match\_operand:SI 1 "general\_operand" "g"))\
(match\_operand:SI 2 "general\_operand" "g"))\
(plus:SI (sign\_extend:SI\
(mem:HI (plus:SI (pc)\
(minus:SI (match\_dup 0)\
(match\_dup 1)))))\
(label\_ref:SI (match\_operand 3 "" "")))\
(pc)))]\
""\
"casel %0,%1,%2")

;; This used to arise from the preceding by simplification\
;; if operand 1 is zero. Perhaps it is no longer necessary.\
(define\_insn ""\
\[(set (pc)\
(if\_then\_else (le (match\_operand:SI 0 "general\_operand" "g")\
(match\_operand:SI 1 "general\_operand" "g"))\
(plus:SI (sign\_extend:SI\
(mem:HI (plus:SI (pc)\
(minus:SI (match\_dup 0)\
(const\_int 0)))))\
(label\_ref:SI (match\_operand 3 "" "")))\
(pc)))]\
""\
"casel %0,$0,%1")

;; This arises from the preceding by simplification if operand 1 is zero.\
(define\_insn ""\
\[(set (pc)\
(if\_then\_else (le (match\_operand:SI 0 "general\_operand" "g")\
(match\_operand:SI 1 "general\_operand" "g"))\
(plus:SI (sign\_extend:SI\
(mem:HI (plus:SI (pc)\
(match\_dup 0))))\
(label\_ref:SI (match\_operand 3 "" "")))\
(pc)))]\
""\
"casel %0,$0,%1")

;; This arises from casesi if operand 0 is a constant, in range.\
(define\_insn ""\
\[(set (pc)\
(plus:SI (sign\_extend:SI\
(mem:HI (plus:SI (pc)\
(match\_operand:SI 0 "general\_operand" "g"))))\
(label\_ref:SI (match\_operand 3 "" ""))))]\
""\
"casel %0,$0,%0")

;; This arises from the above if both operands are the same.\
(define\_insn ""\
\[(set (pc)\
(plus:SI (sign\_extend:SI (mem:HI (pc)))\
(label\_ref:SI (match\_operand 3 "" ""))))]\
""\
"casel $0,$0,$0")\
;;- load or push effective address\
;; These come after the move and add/sub patterns\
;; because we don't want pushl $1 turned into pushad 1.\
;; or addl3 r1,r2,r3 turned into movab 0(r1)\[r2],r3.

(define\_insn ""\
\[(set (match\_operand:SI 0 "general\_operand" "=g")\
(match\_operand:QI 1 "address\_operand" "p"))]\
""\
"\*\
{\
if (push\_operand (operands\[0], SImode))\
return "pushab %a1";\
return "movab %a1,%0";\
}")

(define\_insn ""\
\[(set (match\_operand:SI 0 "general\_operand" "=g")\
(match\_operand:HI 1 "address\_operand" "p"))]\
""\
"\*\
{\
if (push\_operand (operands\[0], SImode))\
return "pushaw %a1";\
return "movaw %a1,%0";\
}")

(define\_insn ""\
\[(set (match\_operand:SI 0 "general\_operand" "=g")\
(match\_operand:SI 1 "address\_operand" "p"))]\
""\
"\*\
{\
if (push\_operand (operands\[0], SImode))\
return "pushal %a1";\
return "moval %a1,%0";\
}")

(define\_insn ""\
\[(set (match\_operand:SI 0 "general\_operand" "=g")\
(match\_operand:SF 1 "address\_operand" "p"))]\
""\
"\*\
{\
if (push\_operand (operands\[0], SImode))\
return "pushaf %a1";\
return "movaf %a1,%0";\
}")

(define\_insn ""\
\[(set (match\_operand:SI 0 "general\_operand" "=g")\
(match\_operand:DF 1 "address\_operand" "p"))]\
""\
"\*\
{\
if (push\_operand (operands\[0], SImode))\
return "pushad %a1";\
return "movad %a1,%0";\
}")\
;; Optimize extzv ...,z; andl2 ...,z\
;; with other operands constant.\
(define\_peephole\
\[(set (match\_operand:SI 0 "general\_operand" "g")\
(zero\_extract:SI (match\_operand:SI 1 "general\_operand" "g")\
(match\_operand:SI 2 "general\_operand" "g")\
(match\_operand:SI 3 "general\_operand" "g")))\
(set (match\_operand:SI 4 "general\_operand" "g")\
(and:SI (match\_dup 0)\
(match\_operand:SI 5 "general\_operand" "g")))]\
"GET\_CODE (operands\[2]) == CONST\_INT\
&& GET\_CODE (operands\[3]) == CONST\_INT\
&& (INTVAL (operands\[2]) + INTVAL (operands\[3])) == 32\
&& GET\_CODE (operands\[5]) == CONST\_INT\
&& dead\_or\_set\_p (insn, operands\[0])"\
"\*\
{\
unsigned long mask = INTVAL (operands\[5]);\
operands\[3] = gen\_rtx (CONST\_INT, VOIDmode, -INTVAL (operands\[3]));

if ((floor\_log2 (mask) + 1) >= INTVAL (operands\[2]))\
mask &= ((1 << INTVAL (operands\[2])) - 1);

operands\[5] = gen\_rtx (CONST\_INT, VOIDmode, \~mask);\
if (push\_operand (operands\[4], SImode))\
{\
output\_asm\_insn ("rotl %3,%1,%0", operands);\
return "bicl3 %5,%0,%4";\
}\
else\
{\
output\_asm\_insn ("rotl %3,%1,%4", operands);\
return "bicl2 %5,%4";\
}\
}")

;; Optimize andl3 x,y,z; extzv z,....,z

(define\_peephole\
\[(set (match\_operand:SI 0 "general\_operand" "g")\
(and:SI (match\_operand:SI 1 "general\_operand" "g")\
(match\_operand:SI 2 "general\_operand" "g")))\
(set (match\_operand 3 "general\_operand" "g")\
(zero\_extract:SI (match\_dup 0)\
(match\_operand:SI 4 "general\_operand" "g")\
(match\_operand:SI 5 "general\_operand" "g")))]\
"GET\_CODE (operands\[2]) == CONST\_INT\
&& GET\_CODE (operands\[4]) == CONST\_INT\
&& GET\_CODE (operands\[5]) == CONST\_INT\
&& (INTVAL (operands\[4]) + INTVAL (operands\[5])) == 32\
&& dead\_or\_set\_p (insn, operands\[0])"\
"\*\
{\
unsigned long mask = INTVAL (operands\[2]);

mask &= \~((1 << INTVAL (operands\[5])) - 1);\
operands\[2] = gen\_rtx (CONST\_INT, VOIDmode, \~mask);

operands\[5] = gen\_rtx (CONST\_INT, VOIDmode, -INTVAL (operands\[5]));

if (rtx\_equal\_p (operands\[0], operands\[1]))\
output\_asm\_insn ("bicl2 %2,%0", operands);\
else\
output\_asm\_insn ("bicl3 %2,%1,%0", operands);\
return "rotl %5,%0,%3";\
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
