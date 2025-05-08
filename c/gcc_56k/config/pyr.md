# pyr

;; Machine description for Pyramid 90 Series for GNU C compiler\
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

;; Instruction patterns. When multiple patterns apply,\
;; the first one in the file is chosen.\
;;\
;; See file "rtl.def" for documentation on define\_insn, match\_\*, et. al.\
;;\
;; cpp macro #define NOTICE\_UPDATE\_CC in file tm.h handles condition code\
;; updates for most instructions.\
;; \* Try using define\_insn instead of some peepholes in more places.\
;; \* Set REG\_NOTES:REG\_EQUIV for cvt\[bh]w loads. This would make the\
;; backward scan in sign\_extend needless.\
;; \* Match (pc) (label\_ref) case in peephole patterns.\
;; \* Should optimize\
;; "cmpX op1,op2; b{eq,ne} LY; ucmpX op1.op2; b{lt,le,gt,ge} LZ"\
;; to\
;; "ucmpX op1,op2; b{eq,ne} LY; b{lt,le,gt,ge} LZ"\
;; by pre-scanning insn and running notice\_update\_cc for them.\
;; \* Is it necessary to do copy\_rtx in the test and compare patterns?\
;; \* Fix true frame pointer omission.\
;; \* Make the jump tables contain branches, not addresses! This would\
;; save us one instruction.\
;; \* Could the compilcated scheme for compares be simplyfied, if we had\
;; no named cmpqi or cmphi patterns, and instead anonymous patterns for\
;; the less-than-word compare cases pyr can handle???\
;; \* The jump insn seems to accept more than just IR addressing. Would\
;; we win by telling GCC? Or can we use movw into the global reg which\
;; is a synonym for pc?\
;; \* More DImode patterns.\
;; \* Scan backwards in "zero\_extendhisi2", "zero\_extendqisi2" to find out\
;; if the extension can be omitted.\
;; \* "divmodsi" with Pyramid "ediv" insn. Is it possible in rtl??\
;; \* Would "rcsp tmpreg; u?cmp\[bh] op1\_regdispl(tmpreg),op2" win in\
;; comparison with the two extensions and single test generated now?\
;; The rcsp insn could be expanded, and moved out of loops by the\
;; optimizer, making 1 (64 bit) insn of 3 (32 bit) insns in loops.\
;; The rcsp insn could be followed by an add insn, making non-displacement\
;; IR addressing sufficient.

;\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\
;\
; Test and Compare Patterns.\
;\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_

; The argument for the rather complicated test and compare expansion\
; scheme, is the irregular pyramid instructions for these operations.\
; 1) Pyramid has different signed and unsigned compares. 2) HImode\
; and QImode integers are memory-memory and immediate-memory only. 3)\
; Unsigned HImode compares doesn't exist. 4) Only certain\
; combinations of addresses are allowed for memory-memory compares.\
; Whenever necessary, in order to fulfill these addressing\
; constraints, the compare operands are swapped.

(define\_expand "tstsi"\
\[(set (cc0)\
(match\_operand:SI 0 "general\_operand" ""))]\
"" "operands\[0] = force\_reg (SImode, operands\[0]);")

(define\_insn ""\
\[(set (cc0)\
(compare (match\_operand:SI 0 "memory\_operand" "m")\
(match\_operand:SI 1 "memory\_operand" "m")))]\
"weird\_memory\_memory (operands\[0], operands\[1])"\
"\*\
{\
rtx br\_insn = NEXT\_INSN (insn);\
RTX\_CODE br\_code;

if (GET\_CODE (br\_insn) != JUMP\_INSN)\
abort();\
br\_code = GET\_CODE (XEXP (XEXP (PATTERN (br\_insn), 1), 0));

weird\_memory\_memory (operands\[0], operands\[1]);

if (swap\_operands)\
{\
cc\_status.flags = CC\_REVERSED;\
if (TRULY\_UNSIGNED\_COMPARE\_P (br\_code))\
{\
cc\_status.mdep = CC\_VALID\_FOR\_UNSIGNED;\
return "ucmpw %0,%1";\
}\
return "cmpw %0,%1";\
}

if (TRULY\_UNSIGNED\_COMPARE\_P (br\_code))\
{\
cc\_status.mdep = CC\_VALID\_FOR\_UNSIGNED;\
return "ucmpw %1,%0";\
}\
return "cmpw %1,%0";\
}")

(define\_insn "cmpsi"\
\[(set (cc0)\
(compare (match\_operand:SI 0 "general\_operand" "r,g")\
(match\_operand:SI 1 "general\_operand" "g,r")))]\
""\
"\*\
{\
rtx br\_insn = NEXT\_INSN (insn);\
RTX\_CODE br\_code;

if (GET\_CODE (br\_insn) != JUMP\_INSN)\
abort();\
br\_code = GET\_CODE (XEXP (XEXP (PATTERN (br\_insn), 1), 0));

if (which\_alternative != 0)\
{\
cc\_status.flags = CC\_REVERSED;\
if (TRULY\_UNSIGNED\_COMPARE\_P (br\_code))\
{\
cc\_status.mdep = CC\_VALID\_FOR\_UNSIGNED;\
return "ucmpw %0,%1";\
}\
return "cmpw %0,%1";\
}

if (TRULY\_UNSIGNED\_COMPARE\_P (br\_code))\
{\
cc\_status.mdep = CC\_VALID\_FOR\_UNSIGNED;\
return "ucmpw %1,%0";\
}\
return "cmpw %1,%0";\
}")

(define\_insn ""\
\[(set (cc0)\
(match\_operand:SI 0 "general\_operand" "r"))]\
""\
"\*\
{\
rtx br\_insn = NEXT\_INSN (insn);\
RTX\_CODE br\_code;

if (GET\_CODE (br\_insn) != JUMP\_INSN)\
abort();\
br\_code = GET\_CODE (XEXP (XEXP (PATTERN (br\_insn), 1), 0));

if (TRULY\_UNSIGNED\_COMPARE\_P (br\_code))\
{\
cc\_status.mdep = CC\_VALID\_FOR\_UNSIGNED;\
return "ucmpw $0,%0";\
}\
return "mtstw %0,%0";\
}")

(define\_expand "cmphi"\
\[(set (cc0)\
(compare (match\_operand:HI 0 "general\_operand" "")\
(match\_operand:HI 1 "general\_operand" "")))]\
""\
"\
{\
extern rtx test\_op0, test\_op1; extern enum machine\_mode test\_mode;\
test\_op0 = copy\_rtx (operands\[0]);\
test\_op1 = copy\_rtx (operands\[1]);\
test\_mode = HImode;\
DONE;\
}")

(define\_expand "tsthi"\
\[(set (cc0)\
(match\_operand:HI 0 "general\_operand" ""))]\
""\
"\
{\
extern rtx test\_op0; extern enum machine\_mode test\_mode;\
test\_op0 = copy\_rtx (operands\[0]);\
test\_mode = HImode;\
DONE;\
}")

(define\_insn ""\
\[(set (cc0)\
(compare (match\_operand:HI 0 "memory\_operand" "m")\
(match\_operand:HI 1 "memory\_operand" "m")))]\
"weird\_memory\_memory (operands\[0], operands\[1])"\
"\*\
{\
rtx br\_insn = NEXT\_INSN (insn);

if (GET\_CODE (br\_insn) != JUMP\_INSN)\
abort();

weird\_memory\_memory (operands\[0], operands\[1]);

if (swap\_operands)\
{\
cc\_status.flags = CC\_REVERSED;\
return "cmph %0,%1";\
}

return "cmph %1,%0";\
}")

(define\_insn ""\
\[(set (cc0)\
(compare (match\_operand:HI 0 "nonimmediate\_operand" "r,m")\
(match\_operand:HI 1 "nonimmediate\_operand" "m,r")))]\
"(GET\_CODE (operands\[0]) != GET\_CODE (operands\[1]))"\
"\*\
{\
rtx br\_insn = NEXT\_INSN (insn);

if (GET\_CODE (br\_insn) != JUMP\_INSN)\
abort();

if (which\_alternative != 0)\
{\
cc\_status.flags = CC\_REVERSED;\
return "cmph %0,%1";\
}

return "cmph %1,%0";\
}")

(define\_expand "cmpqi"\
\[(set (cc0)\
(compare (match\_operand:QI 0 "general\_operand" "")\
(match\_operand:QI 1 "general\_operand" "")))]\
""\
"\
{\
extern rtx test\_op0, test\_op1; extern enum machine\_mode test\_mode;\
test\_op0 = copy\_rtx (operands\[0]);\
test\_op1 = copy\_rtx (operands\[1]);\
test\_mode = QImode;\
DONE;\
}")

(define\_expand "tstqi"\
\[(set (cc0)\
(match\_operand:QI 0 "general\_operand" ""))]\
""\
"\
{\
extern rtx test\_op0; extern enum machine\_mode test\_mode;\
test\_op0 = copy\_rtx (operands\[0]);\
test\_mode = QImode;\
DONE;\
}")

(define\_insn ""\
\[(set (cc0)\
(compare (match\_operand:QI 0 "memory\_operand" "m")\
(match\_operand:QI 1 "memory\_operand" "m")))]\
"weird\_memory\_memory (operands\[0], operands\[1])"\
"\*\
{\
rtx br\_insn = NEXT\_INSN (insn);\
RTX\_CODE br\_code;

if (GET\_CODE (br\_insn) != JUMP\_INSN)\
abort();\
br\_code = GET\_CODE (XEXP (XEXP (PATTERN (br\_insn), 1), 0));

weird\_memory\_memory (operands\[0], operands\[1]);

if (swap\_operands)\
{\
cc\_status.flags = CC\_REVERSED;\
if (TRULY\_UNSIGNED\_COMPARE\_P (br\_code))\
{\
cc\_status.mdep = CC\_VALID\_FOR\_UNSIGNED;\
return "ucmpb %0,%1";\
}\
return "cmpb %0,%1";\
}

if (TRULY\_UNSIGNED\_COMPARE\_P (br\_code))\
{\
cc\_status.mdep = CC\_VALID\_FOR\_UNSIGNED;\
return "ucmpb %1,%0";\
}\
return "cmpb %1,%0";\
}")

(define\_insn ""\
\[(set (cc0)\
(compare (match\_operand:QI 0 "nonimmediate\_operand" "r,m")\
(match\_operand:QI 1 "nonimmediate\_operand" "m,r")))]\
"(GET\_CODE (operands\[0]) != GET\_CODE (operands\[1]))"\
"\*\
{\
rtx br\_insn = NEXT\_INSN (insn);\
RTX\_CODE br\_code;

if (GET\_CODE (br\_insn) != JUMP\_INSN)\
abort();\
br\_code = GET\_CODE (XEXP (XEXP (PATTERN (br\_insn), 1), 0));

if (which\_alternative != 0)\
{\
cc\_status.flags = CC\_REVERSED;\
if (TRULY\_UNSIGNED\_COMPARE\_P (br\_code))\
{\
cc\_status.mdep = CC\_VALID\_FOR\_UNSIGNED;\
return "ucmpb %0,%1";\
}\
return "cmpb %0,%1";\
}

if (TRULY\_UNSIGNED\_COMPARE\_P (br\_code))\
{\
cc\_status.mdep = CC\_VALID\_FOR\_UNSIGNED;\
return "ucmpb %1,%0";\
}\
return "cmpb %1,%0";\
}")

(define\_expand "bgt"\
\[(set (pc) (if\_then\_else (gt (cc0) (const\_int 0))\
(label\_ref (match\_operand 0 "" "")) (pc)))]\
"" "extend\_and\_branch (SIGN\_EXTEND);")

(define\_expand "blt"\
\[(set (pc) (if\_then\_else (lt (cc0) (const\_int 0))\
(label\_ref (match\_operand 0 "" "")) (pc)))]\
"" "extend\_and\_branch (SIGN\_EXTEND);")

(define\_expand "bge"\
\[(set (pc) (if\_then\_else (ge (cc0) (const\_int 0))\
(label\_ref (match\_operand 0 "" "")) (pc)))]\
"" "extend\_and\_branch (SIGN\_EXTEND);")

(define\_expand "ble"\
\[(set (pc) (if\_then\_else (le (cc0) (const\_int 0))\
(label\_ref (match\_operand 0 "" "")) (pc)))]\
"" "extend\_and\_branch (SIGN\_EXTEND);")

(define\_expand "beq"\
\[(set (pc) (if\_then\_else (eq (cc0) (const\_int 0))\
(label\_ref (match\_operand 0 "" "")) (pc)))]\
"" "extend\_and\_branch (SIGN\_EXTEND);")

(define\_expand "bne"\
\[(set (pc) (if\_then\_else (ne (cc0) (const\_int 0))\
(label\_ref (match\_operand 0 "" "")) (pc)))]\
"" "extend\_and\_branch (SIGN\_EXTEND);")

(define\_expand "bgtu"\
\[(set (pc) (if\_then\_else (gtu (cc0) (const\_int 0))\
(label\_ref (match\_operand 0 "" "")) (pc)))]\
"" "extend\_and\_branch (ZERO\_EXTEND);")

(define\_expand "bltu"\
\[(set (pc) (if\_then\_else (ltu (cc0) (const\_int 0))\
(label\_ref (match\_operand 0 "" "")) (pc)))]\
"" "extend\_and\_branch (ZERO\_EXTEND);")

(define\_expand "bgeu"\
\[(set (pc) (if\_then\_else (geu (cc0) (const\_int 0))\
(label\_ref (match\_operand 0 "" "")) (pc)))]\
"" "extend\_and\_branch (ZERO\_EXTEND);")

(define\_expand "bleu"\
\[(set (pc) (if\_then\_else (leu (cc0) (const\_int 0))\
(label\_ref (match\_operand 0 "" "")) (pc)))]\
"" "extend\_and\_branch (ZERO\_EXTEND);")

(define\_insn "cmpdf"\
\[(set (cc0)\
(compare (match\_operand:DF 0 "register\_operand" "r")\
(match\_operand:DF 1 "register\_operand" "r")))]\
""\
"cmpd %1,%0")

(define\_insn "cmpsf"\
\[(set (cc0)\
(compare (match\_operand:SF 0 "register\_operand" "r")\
(match\_operand:SF 1 "register\_operand" "r")))]\
""\
"cmpf %1,%0")

(define\_insn "tstdf"\
\[(set (cc0)\
(match\_operand:DF 0 "register\_operand" "r"))]\
""\
"mtstd %0,%0")

(define\_insn "tstsf"\
\[(set (cc0)\
(match\_operand:SF 0 "register\_operand" "r"))]\
""\
"mtstf %0,%0")\
;\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\
;\
; Fixed-point Arithmetic.\
;\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_

(define\_insn "addsi3"\
\[(set (match\_operand:SI 0 "register\_operand" "=r,!r")\
(plus:SI (match\_operand:SI 1 "register\_operand" "%0,r")\
(match\_operand:SI 2 "general\_operand" "g,rJ")))]\
""\
"\*\
{\
if (which\_alternative == 0)\
return "addw %2,%0";\
else\
{\
forget\_cc\_if\_dependent (operands\[0]);\
return REG\_P (operands\[2])\
? "mova (%2)\[%&#x31;_&#x31;],%0" : "mova %a2\[%&#x31;_&#x31;],%0";\
}\
}")

(define\_insn "subsi3"\
\[(set (match\_operand:SI 0 "register\_operand" "=r,r")\
(minus:SI (match\_operand:SI 1 "general\_operand" "0,g")\
(match\_operand:SI 2 "general\_operand" "g,0")))]\
""\
"\* return (which\_alternative == 0) ? "subw %2,%0" : "rsubw %1,%0";")

(define\_insn "mulsi3"\
\[(set (match\_operand:SI 0 "register\_operand" "=r")\
(mult:SI (match\_operand:SI 1 "register\_operand" "%0")\
(match\_operand:SI 2 "general\_operand" "g")))]\
""\
"mulw %2,%0")

(define\_insn "umulsi3"\
\[(set (match\_operand:SI 0 "register\_operand" "=r")\
(umult:SI (match\_operand:SI 1 "register\_operand" "%0")\
(match\_operand:SI 2 "general\_operand" "g")))]\
""\
"umulw %2,%0")

(define\_insn "divsi3"\
\[(set (match\_operand:SI 0 "register\_operand" "=r,r")\
(div:SI (match\_operand:SI 1 "general\_operand" "0,g")\
(match\_operand:SI 2 "general\_operand" "g,0")))]\
""\
"\* return (which\_alternative == 0) ? "divw %2,%0" : "rdivw %1,%0";")

(define\_insn "udivsi3"\
\[(set (match\_operand:SI 0 "register\_operand" "=r")\
(udiv:SI (match\_operand:SI 1 "register\_operand" "0")\
(match\_operand:SI 2 "general\_operand" "g")))]\
""\
"udivw %2,%0")

(define\_insn "modsi3"\
\[(set (match\_operand:SI 0 "register\_operand" "=r")\
(mod:SI (match\_operand:SI 1 "register\_operand" "0")\
(match\_operand:SI 2 "general\_operand" "g")))]\
""\
"modw %2,%0")

(define\_insn "umodsi3"\
\[(set (match\_operand:SI 0 "register\_operand" "=r")\
(umod:SI (match\_operand:SI 1 "register\_operand" "0")\
(match\_operand:SI 2 "general\_operand" "g")))]\
""\
"umodw %2,%0")

(define\_insn "negsi2"\
\[(set (match\_operand:SI 0 "register\_operand" "=r")\
(neg:SI (match\_operand:SI 1 "nonimmediate\_operand" "rm")))]\
""\
"mnegw %1,%0")

(define\_insn "one\_cmplsi2"\
\[(set (match\_operand:SI 0 "register\_operand" "=r")\
(not:SI (match\_operand:SI 1 "nonimmediate\_operand" "rm")))]\
""\
"mcomw %1,%0")

(define\_insn "abssi2"\
\[(set (match\_operand:SI 0 "register\_operand" "=r")\
(abs:SI (match\_operand:SI 1 "nonimmediate\_operand" "rm")))]\
""\
"mabsw %1,%0")\
;\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\
;\
; Floating-point Arithmetic.\
;\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_

(define\_insn "adddf3"\
\[(set (match\_operand:DF 0 "register\_operand" "=r")\
(plus:DF (match\_operand:DF 1 "register\_operand" "%0")\
(match\_operand:DF 2 "register\_operand" "r")))]\
""\
"addd %2,%0")

(define\_insn "addsf3"\
\[(set (match\_operand:SF 0 "register\_operand" "=r")\
(plus:SF (match\_operand:SF 1 "register\_operand" "%0")\
(match\_operand:SF 2 "register\_operand" "r")))]\
""\
"addf %2,%0")

(define\_insn "subdf3"\
\[(set (match\_operand:DF 0 "register\_operand" "=r")\
(minus:DF (match\_operand:DF 1 "register\_operand" "0")\
(match\_operand:DF 2 "register\_operand" "r")))]\
""\
"subd %2,%0")

(define\_insn "subsf3"\
\[(set (match\_operand:SF 0 "register\_operand" "=r")\
(minus:SF (match\_operand:SF 1 "register\_operand" "0")\
(match\_operand:SF 2 "register\_operand" "r")))]\
""\
"subf %2,%0")

(define\_insn "muldf3"\
\[(set (match\_operand:DF 0 "register\_operand" "=r")\
(mult:DF (match\_operand:DF 1 "register\_operand" "%0")\
(match\_operand:DF 2 "register\_operand" "r")))]\
""\
"muld %2,%0")

(define\_insn "mulsf3"\
\[(set (match\_operand:SF 0 "register\_operand" "=r")\
(mult:SF (match\_operand:SF 1 "register\_operand" "%0")\
(match\_operand:SF 2 "register\_operand" "r")))]\
""\
"mulf %2,%0")

(define\_insn "divdf3"\
\[(set (match\_operand:DF 0 "register\_operand" "=r")\
(div:DF (match\_operand:DF 1 "register\_operand" "0")\
(match\_operand:DF 2 "register\_operand" "r")))]\
""\
"divd %2,%0")

(define\_insn "divsf3"\
\[(set (match\_operand:SF 0 "register\_operand" "=r")\
(div:SF (match\_operand:SF 1 "register\_operand" "0")\
(match\_operand:SF 2 "register\_operand" "r")))]\
""\
"divf %2,%0")

(define\_insn "negdf2"\
\[(set (match\_operand:DF 0 "register\_operand" "=r")\
(neg:DF (match\_operand:DF 1 "register\_operand" "r")))]\
""\
"mnegd %1,%0")

(define\_insn "negsf2"\
\[(set (match\_operand:SF 0 "register\_operand" "=r")\
(neg:SF (match\_operand:SF 1 "register\_operand" "r")))]\
""\
"mnegf %1,%0")

(define\_insn "absdf2"\
\[(set (match\_operand:DF 0 "register\_operand" "=r")\
(abs:DF (match\_operand:DF 1 "register\_operand" "r")))]\
""\
"mabsd %1,%0")

(define\_insn "abssf2"\
\[(set (match\_operand:SF 0 "register\_operand" "=r")\
(abs:SF (match\_operand:SF 1 "register\_operand" "r")))]\
""\
"mabsf %1,%0")\
;\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\
;\
; Logical and Shift Instructions.\
;\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_

(define\_insn ""\
\[(set (cc0)\
(and:SI (match\_operand:SI 0 "register\_operand" "%r")\
(match\_operand:SI 1 "general\_operand" "g")))]\
""\
"bitw %1,%0");

(define\_insn "andsi3"\
\[(set (match\_operand:SI 0 "register\_operand" "=r,r")\
(and:SI (match\_operand:SI 1 "register\_operand" "%0,r")\
(match\_operand:SI 2 "general\_operand" "g,K")))]\
""\
"\*\
{\
if (which\_alternative == 0)\
return "andw %2,%0";

cc\_status.flags = CC\_NOT\_NEGATIVE;\
return (INTVAL (operands\[2]) == 255\
? "movzbw %1,%0" : "movzhw %1,%0");\
}")

(define\_insn "andcbsi3"\
\[(set (match\_operand:SI 0 "register\_operand" "=r")\
(and:SI (match\_operand:SI 1 "register\_operand" "0")\
(not:SI (match\_operand:SI 2 "general\_operand" "g"))))]\
""\
"bicw %2,%0")

(define\_insn ""\
\[(set (match\_operand:SI 0 "register\_operand" "=r")\
(and:SI (not:SI (match\_operand:SI 1 "general\_operand" "g"))\
(match\_operand:SI 2 "register\_operand" "0")))]\
""\
"bicw %1,%0")

(define\_insn "iorsi3"\
\[(set (match\_operand:SI 0 "register\_operand" "=r")\
(ior:SI (match\_operand:SI 1 "register\_operand" "%0")\
(match\_operand:SI 2 "general\_operand" "g")))]\
""\
"orw %2,%0")

(define\_insn "xorsi3"\
\[(set (match\_operand:SI 0 "register\_operand" "=r")\
(xor:SI (match\_operand:SI 1 "register\_operand" "%0")\
(match\_operand:SI 2 "general\_operand" "g")))]\
""\
"xorw %2,%0")

; The arithmetic left shift instructions work strangely on pyramids.\
; They fail to modify the sign bit. Therefore, use logic shifts.

(define\_insn "ashlsi3"\
\[(set (match\_operand:SI 0 "register\_operand" "=r")\
(ashift:SI (match\_operand:SI 1 "register\_operand" "0")\
(match\_operand:SI 2 "general\_operand" "rnm")))]\
""\
"\* return output\_shift ("lshlw %2,%0", operands\[2], 32); ")

(define\_insn "ashrsi3"\
\[(set (match\_operand:SI 0 "register\_operand" "=r")\
(ashiftrt:SI (match\_operand:SI 1 "register\_operand" "0")\
(match\_operand:SI 2 "general\_operand" "rnm")))]\
""\
"\* return output\_shift ("ashrw %2,%0", operands\[2], 32); ")

(define\_insn "ashrdi3"\
\[(set (match\_operand:DI 0 "register\_operand" "=r")\
(ashiftrt:DI (match\_operand:DI 1 "register\_operand" "0")\
(match\_operand:SI 2 "general\_operand" "rnm")))]\
""\
"\* return output\_shift ("ashrl %2,%0", operands\[2], 64); ")

(define\_insn "lshrsi3"\
\[(set (match\_operand:SI 0 "register\_operand" "=r")\
(lshiftrt:SI (match\_operand:SI 1 "register\_operand" "0")\
(match\_operand:SI 2 "general\_operand" "rnm")))]\
""\
"\* return output\_shift ("lshrw %2,%0", operands\[2], 32); ")

(define\_insn "rotlsi3"\
\[(set (match\_operand:SI 0 "register\_operand" "=r")\
(rotate:SI (match\_operand:SI 1 "register\_operand" "0")\
(match\_operand:SI 2 "general\_operand" "rnm")))]\
""\
"\* return output\_shift ("rotlw %2,%0", operands\[2], 32); ")

(define\_insn "rotrsi3"\
\[(set (match\_operand:SI 0 "register\_operand" "=r")\
(rotatert:SI (match\_operand:SI 1 "register\_operand" "0")\
(match\_operand:SI 2 "general\_operand" "rnm")))]\
""\
"\* return output\_shift ("rotrw %2,%0", operands\[2], 32); ")\
;\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\
;\
; Fixed and Floating Moves.\
;\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_

;; If the destination is a memory operand, indexed source operands are\
;; disallowed. Big DImode constants are always loaded into a reg pair,\
;; although offsetable memory addresses really could be dealt with.

(define\_insn ""\
\[(set (match\_operand:DI 0 "memory\_operand" "=m")\
(match\_operand:DI 1 "nonindexed\_operand" "gF"))]\
"(GET\_CODE (operands\[1]) == CONST\_DOUBLE\
? ((CONST\_DOUBLE\_HIGH (operands\[1]) == 0\
&& CONST\_DOUBLE\_LOW (operands\[1]) >= 0)\
|| (CONST\_DOUBLE\_HIGH (operands\[1]) == -1\
&& CONST\_DOUBLE\_LOW (operands\[1]) < 0))\
: 1)"\
"\*\
{\
if (GET\_CODE (operands\[1]) == CONST\_DOUBLE)\
operands\[1] = gen\_rtx (CONST\_INT, VOIDmode,\
CONST\_DOUBLE\_LOW (operands\[1]));\
return "movl %1,%0";\
}")

;; Force the destination to a register, so all source operands are allowed.

(define\_insn "movdi"\
\[(set (match\_operand:DI 0 "general\_operand" "=r")\
(match\_operand:DI 1 "general\_operand" "gF"))]\
""\
"\* return output\_move\_double (operands); ")

;; If the destination is a memory address, indexed source operands are\
;; disallowed.

(define\_insn ""\
\[(set (match\_operand:SI 0 "memory\_operand" "=m")\
(match\_operand:SI 1 "nonindexed\_operand" "g"))]\
""\
"movw %1,%0")

;; Force the destination to a register, so all source operands are allowed.

(define\_insn "movsi"\
\[(set (match\_operand:SI 0 "general\_operand" "=r")\
(match\_operand:SI 1 "general\_operand" "g"))]\
""\
"movw %1,%0")

;; If the destination is a memory address, indexed source operands are\
;; disallowed.

(define\_insn ""\
\[(set (match\_operand:HI 0 "memory\_operand" "=m")\
(match\_operand:HI 1 "nonindexed\_operand" "g"))]\
""\
"\*\
{\
if (REG\_P (operands\[1]))\
return "cvtwh %1,%0"; /\* reg -> mem _/_\
_else_\
_return "movh %1,%0"; /_ mem imm -> mem \*/\
}")

;; Force the destination to a register, so all source operands are allowed.

(define\_insn "movhi"\
\[(set (match\_operand:HI 0 "general\_operand" "=r")\
(match\_operand:HI 1 "general\_operand" "g"))]\
""\
"\*\
{\
if (GET\_CODE (operands\[1]) != MEM)\
return "movw %1,%0"; /\* reg imm -> reg _/_\
_return "cvthw %1,%0"; /_ mem -> reg \*/\
}")

;; If the destination is a memory address, indexed source operands are\
;; disallowed.

(define\_insn ""\
\[(set (match\_operand:QI 0 "memory\_operand" "=m")\
(match\_operand:QI 1 "nonindexed\_operand" "g"))]\
""\
"\*\
{\
if (REG\_P (operands\[1]))\
return "cvtwb %1,%0"; /\* reg -> mem _/_\
_else_\
_return "movb %1,%0"; /_ mem imm -> mem \*/\
}")

;; Force the destination to a register, so all source operands are allowed.

(define\_insn "movqi"\
\[(set (match\_operand:QI 0 "general\_operand" "=r")\
(match\_operand:QI 1 "general\_operand" "g"))]\
""\
"\*\
{\
if (GET\_CODE (operands\[1]) != MEM)\
return "movw %1,%0"; /\* reg imm -> reg _/_\
_return "cvtbw %1,%0"; /_ mem -> reg \*/\
}")

;; If the destination is a memory address, indexed source operands are\
;; disallowed.

(define\_insn ""\
\[(set (match\_operand:DF 0 "memory\_operand" "=m")\
(match\_operand:DF 1 "nonindexed\_operand" "g"))]\
"GET\_CODE (operands\[1]) != CONST\_DOUBLE"\
"movl %1,%0")

;; Force the destination to a register, so all source operands are allowed.

(define\_insn "movdf"\
\[(set (match\_operand:DF 0 "general\_operand" "=r")\
(match\_operand:DF 1 "general\_operand" "gF"))]\
""\
"\* return output\_move\_double (operands); ")

;; If the destination is a memory address, indexed source operands are\
;; disallowed.

(define\_insn ""\
\[(set (match\_operand:SF 0 "memory\_operand" "=m")\
(match\_operand:SF 1 "nonindexed\_operand" "g"))]\
""\
"movw %1,%0")

;; Force the destination to a register, so all source operands are allowed.

(define\_insn "movsf"\
\[(set (match\_operand:SF 0 "general\_operand" "=r")\
(match\_operand:SF 1 "general\_operand" "g"))]\
""\
"movw %1,%0")

(define\_insn ""\
\[(set (match\_operand:SI 0 "register\_operand" "=r")\
(match\_operand:QI 1 "address\_operand" "p"))]\
""\
"\*\
{\
forget\_cc\_if\_dependent (operands\[0]);\
return "mova %a1,%0";\
}")\
;\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\
;\
; Conversion patterns.\
;\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_

;; The trunc patterns are used only when non compile-time constants are used.

(define\_insn "truncsiqi2"\
\[(set (match\_operand:QI 0 "register\_operand" "=r")\
(truncate:QI (match\_operand:SI 1 "nonimmediate\_operand" "rm")))]\
""\
"\*\
{\
if (REG\_P (operands\[0]) && REG\_P (operands\[1])\
&& REGNO (operands\[0]) == REGNO (operands\[1]))\
{\
cc\_status = cc\_prev\_status;\
return "";\
}\
forget\_cc\_if\_dependent (operands\[0]);\
return "movw %1,%0";\
}")

(define\_insn "truncsihi2"\
\[(set (match\_operand:HI 0 "register\_operand" "=r")\
(truncate:HI (match\_operand:SI 1 "nonimmediate\_operand" "rm")))]\
""\
"\*\
{\
if (REG\_P (operands\[0]) && REG\_P (operands\[1])\
&& REGNO (operands\[0]) == REGNO (operands\[1]))\
{\
cc\_status = cc\_prev\_status;\
return "";\
}\
forget\_cc\_if\_dependent (operands\[0]);\
return "movw %1,%0";\
}")

(define\_insn "extendhisi2"\
\[(set (match\_operand:SI 0 "general\_operand" "=r,m")\
(sign\_extend:SI (match\_operand:HI 1 "nonimmediate\_operand" "rm,r")))]\
""\
"\*\
{\
extern int optimize;\
if (optimize && REG\_P (operands\[0]) && REG\_P (operands\[1])\
&& REGNO (operands\[0]) == REGNO (operands\[1])\
&& already\_sign\_extended (insn, HImode, operands\[0]))\
{\
cc\_status = cc\_prev\_status;\
return "";\
}\
return "cvthw %1,%0";\
}")

(define\_insn "extendqisi2"\
\[(set (match\_operand:SI 0 "general\_operand" "=r,m")\
(sign\_extend:SI (match\_operand:QI 1 "nonimmediate\_operand" "rm,r")))]\
""\
"\*\
{\
extern int optimize;\
if (optimize && REG\_P (operands\[0]) && REG\_P (operands\[1])\
&& REGNO (operands\[0]) == REGNO (operands\[1])\
&& already\_sign\_extended (insn, QImode, operands\[0]))\
{\
cc\_status = cc\_prev\_status;\
return "";\
}\
return "cvtbw %1,%0";\
}")

; Pyramid doesn't have insns _called_ "cvtbh" or "movzbh".\
; But we can cvtbw/movzbw into a register, where there is no distinction\
; between words and halfwords.

(define\_insn "extendqihi2"\
\[(set (match\_operand:HI 0 "register\_operand" "=r")\
(sign\_extend:HI (match\_operand:QI 1 "nonimmediate\_operand" "rm")))]\
""\
"cvtbw %1,%0")

(define\_insn "zero\_extendhisi2"\
\[(set (match\_operand:SI 0 "register\_operand" "=r")\
(zero\_extend:SI (match\_operand:HI 1 "nonimmediate\_operand" "rm")))]\
""\
"\*\
{\
cc\_status.flags = CC\_NOT\_NEGATIVE;\
return "movzhw %1,%0";\
}")

(define\_insn "zero\_extendqisi2"\
\[(set (match\_operand:SI 0 "register\_operand" "=r")\
(zero\_extend:SI (match\_operand:QI 1 "nonimmediate\_operand" "rm")))]\
""\
"\*\
{\
cc\_status.flags = CC\_NOT\_NEGATIVE;\
return "movzbw %1,%0";\
}")

(define\_insn "zero\_extendqihi2"\
\[(set (match\_operand:HI 0 "register\_operand" "=r")\
(zero\_extend:HI (match\_operand:QI 1 "nonimmediate\_operand" "rm")))]\
""\
"\*\
{\
cc\_status.flags = CC\_NOT\_NEGATIVE;\
return "movzbw %1,%0";\
}")

(define\_insn "extendsfdf2"\
\[(set (match\_operand:DF 0 "general\_operand" "=r,m")\
(float\_extend:DF (match\_operand:SF 1 "nonimmediate\_operand" "rm,r")))]\
""\
"cvtfd %1,%0")

(define\_insn "truncdfsf2"\
\[(set (match\_operand:SF 0 "general\_operand" "=r,m")\
(float\_truncate:SF (match\_operand:DF 1 "nonimmediate\_operand" "rm,r")))]\
""\
"cvtdf %1,%0")

(define\_insn "floatsisf2"\
\[(set (match\_operand:SF 0 "general\_operand" "=r,m")\
(float:SF (match\_operand:SI 1 "nonimmediate\_operand" "rm,r")))]\
""\
"cvtwf %1,%0")

(define\_insn "floatsidf2"\
\[(set (match\_operand:DF 0 "general\_operand" "=r,m")\
(float:DF (match\_operand:SI 1 "nonimmediate\_operand" "rm,r")))]\
""\
"cvtwd %1,%0")

(define\_insn "fix\_truncsfsi2"\
\[(set (match\_operand:SI 0 "general\_operand" "=r,m")\
(fix:SI (fix:SF (match\_operand:SF 1 "nonimmediate\_operand" "rm,r"))))]\
""\
"cvtfw %1,%0")

(define\_insn "fix\_truncdfsi2"\
\[(set (match\_operand:SI 0 "general\_operand" "=r,m")\
(fix:SI (fix:DF (match\_operand:DF 1 "nonimmediate\_operand" "rm,r"))))]\
""\
"cvtdw %1,%0")\
;\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\
;\
; Flow Control Patterns.\
;\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_

;; Prefer "br" to "jump" for unconditional jumps, since it's faster.\
;; (The assembler can manage with out-of-range branches.)

(define\_insn "jump"\
\[(set (pc)\
(label\_ref (match\_operand 0 "" "")))]\
""\
"br %l0")

(define\_insn ""\
\[(set (pc)\
(if\_then\_else (match\_operator 0 "relop" \[(cc0) (const\_int 0)])\
(label\_ref (match\_operand 1 "" ""))\
(pc)))]\
""\
"\*\
{\
extern int optimize;\
if (optimize)\
switch (GET\_CODE (operands\[0]))\
{\
case EQ: case NE:\
break;\
case LT: case LE: case GE: case GT:\
if (cc\_prev\_status.mdep == CC\_VALID\_FOR\_UNSIGNED)\
return 0;\
break;\
case LTU: case LEU: case GEU: case GTU:\
if (cc\_prev\_status.mdep != CC\_VALID\_FOR\_UNSIGNED)\
return 0;\
break;\
}

return "b%N0 %l1";\
}")

(define\_insn ""\
\[(set (pc)\
(if\_then\_else (match\_operator 0 "relop" \[(cc0) (const\_int 0)])\
(pc)\
(label\_ref (match\_operand 1 "" ""))))]\
""\
"\*\
{\
extern int optimize;\
if (optimize)\
switch (GET\_CODE (operands\[0]))\
{\
case EQ: case NE:\
break;\
case LT: case LE: case GE: case GT:\
if (cc\_prev\_status.mdep == CC\_VALID\_FOR\_UNSIGNED)\
return 0;\
break;\
case LTU: case LEU: case GEU: case GTU:\
if (cc\_prev\_status.mdep != CC\_VALID\_FOR\_UNSIGNED)\
return 0;\
break;\
}

return "b%C0 %l1";\
}")

(define\_insn "call"\
\[(call (match\_operand:QI 0 "memory\_operand" "m")\
(match\_operand:SI 1 "immediate\_operand" "n"))]\
""\
"call %0")

(define\_insn "call\_value"\
\[(set (match\_operand 0 "" "=r")\
(call (match\_operand:QI 1 "memory\_operand" "m")\
(match\_operand:SI 2 "immediate\_operand" "n")))]\
;; Operand 2 not really used on Pyramid architecture.\
""\
"call %1")

(define\_insn "return"\
\[(return)]\
""\
"\*\
{\
if (get\_frame\_size () + current\_function\_pretend\_args\_size\
\+ current\_function\_args\_size != 0\
|| current\_function\_calls\_alloca)\
{\
int dealloc\_size = current\_function\_pretend\_args\_size;\
if (current\_function\_pops\_args)\
dealloc\_size += current\_function\_args\_size;\
operands\[0] = gen\_rtx (CONST\_INT, VOIDmode, dealloc\_size);\
return "retd %0";\
}\
else\
return "ret";\
}")

(define\_insn "tablejump"\
\[(set (pc) (match\_operand:SI 0 "register\_operand" "r"))\
(use (label\_ref (match\_operand 1 "" "")))]\
""\
"jump (%0)")

(define\_insn "nop"\
\[(const\_int 0)]\
""\
"movw gr0,gr0 # nop")\
;\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\
;\
; Peep-hole Optimization Patterns.\
;\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_

;; Optimize fullword move followed by a test of the moved value.

(define\_peephole\
\[(set (match\_operand:SI 0 "register\_operand" "=r")\
(match\_operand:SI 1 "nonimmediate\_operand" "rm"))\
(set (cc0) (match\_operand:SI 2 "nonimmediate\_operand" "rm"))]\
"rtx\_equal\_p (operands\[2], operands\[0])\
|| rtx\_equal\_p (operands\[2], operands\[1])"\
"\*\
cc\_status.flags |= CC\_NO\_OVERFLOW;\
return "mtstw %1,%0";\
")

;; Same for HI and QI mode move-test as well.

(define\_peephole\
\[(set (match\_operand:HI 0 "register\_operand" "=r")\
(match\_operand:HI 1 "nonimmediate\_operand" "rm"))\
(set (match\_operand:SI 2 "register\_operand" "=r")\
(sign\_extend:SI (match\_operand:HI 3 "nonimmediate\_operand" "rm")))\
(set (cc0) (match\_dup 2))]\
"dead\_or\_set\_p (insn, operands\[2])\
&& (rtx\_equal\_p (operands\[3], operands\[0])\
|| rtx\_equal\_p (operands\[3], operands\[1]))"\
"\*\
cc\_status.flags |= CC\_NO\_OVERFLOW;\
return "cvthw %1,%0";\
")

(define\_peephole\
\[(set (match\_operand:QI 0 "register\_operand" "=r")\
(match\_operand:QI 1 "nonimmediate\_operand" "rm"))\
(set (match\_operand:SI 2 "register\_operand" "=r")\
(sign\_extend:SI (match\_operand:QI 3 "nonimmediate\_operand" "rm")))\
(set (cc0) (match\_dup 2))]\
"dead\_or\_set\_p (insn, operands\[2])\
&& (rtx\_equal\_p (operands\[3], operands\[0])\
|| rtx\_equal\_p (operands\[3], operands\[1]))"\
"\*\
cc\_status.flags |= CC\_NO\_OVERFLOW;\
return "cvtbw %1,%0";\
")

;; Optimize loops with an incremented/decremented variable.

(define\_peephole\
\[(set (match\_operand:SI 0 "register\_operand" "=r")\
(plus:SI (match\_dup 0)\
(const\_int -1)))\
(set (cc0)\
(compare (match\_operand:SI 1 "register\_operand" "r")\
(match\_operand:SI 2 "nonmemory\_operand" "ri")))\
(set (pc)\
(if\_then\_else (match\_operator:SI 3 "signed\_comparison"\
\[(cc0) (const\_int 0)])\
(label\_ref (match\_operand 4 "" ""))\
(pc)))]\
"(GET\_CODE (operands\[2]) == CONST\_INT\
? (unsigned)INTVAL (operands\[2]) + 32 >= 64\
: 1) && (rtx\_equal\_p (operands\[0], operands\[1])\
|| rtx\_equal\_p (operands\[0], operands\[2]))"\
"\*\
if (rtx\_equal\_p (operands\[0], operands\[1]))\
{\
output\_asm\_insn ("dcmpw %2,%0", operands);\
return "b%N3 %l4";\
}\
else\
{\
output\_asm\_insn ("dcmpw %1,%0", operands);\
return "b%R3 %l4";\
}\
")

(define\_peephole\
\[(set (match\_operand:SI 0 "register\_operand" "=r")\
(plus:SI (match\_dup 0)\
(const\_int 1)))\
(set (cc0)\
(compare (match\_operand:SI 1 "register\_operand" "r")\
(match\_operand:SI 2 "nonmemory\_operand" "ri")))\
(set (pc)\
(if\_then\_else (match\_operator:SI 3 "signed\_comparison"\
\[(cc0) (const\_int 0)])\
(label\_ref (match\_operand 4 "" ""))\
(pc)))]\
"(GET\_CODE (operands\[2]) == CONST\_INT\
? (unsigned)INTVAL (operands\[2]) + 32 >= 64\
: 1) && (rtx\_equal\_p (operands\[0], operands\[1])\
|| rtx\_equal\_p (operands\[0], operands\[2]))"\
"\*\
if (rtx\_equal\_p (operands\[0], operands\[1]))\
{\
output\_asm\_insn ("icmpw %2,%0", operands);\
return "b%N3 %l4";\
}\
else\
{\
output\_asm\_insn ("icmpw %1,%0", operands);\
return "b%R3 %l4";\
}\
")

;; Combine two word moves with consequtive operands into one long move.\
;; Also combines immediate moves, if the high-order destination operand\
;; is loaded with 0 or -1 and the low-order destination operand is loaded\
;; with a constant with the same sign.

(define\_peephole\
\[(set (match\_operand:SI 0 "general\_operand" "=g")\
(match\_operand:SI 1 "general\_operand" "g"))\
(set (match\_operand:SI 2 "general\_operand" "=g")\
(match\_operand:SI 3 "general\_operand" "g"))]\
"movdi\_possible (operands)"\
"\*\
output\_asm\_insn ("# COMBINE movw %1,%0", operands);\
output\_asm\_insn ("# COMBINE movw %3,%2", operands);\
movdi\_possible (operands);\
if (CONSTANT\_P (operands\[1]))\
return (swap\_operands) ? "movl %3,%0" : "movl %1,%2";

return (swap\_operands) ? "movl %1,%0" : "movl %3,%2";\
")

;; Optimize certain tests after memory stores.

(define\_peephole\
\[(set (match\_operand 0 "memory\_operand" "=m")\
(match\_operand 1 "register\_operand" "r"))\
(set (match\_operand:SI 2 "register\_operand" "=r")\
(sign\_extend:SI (match\_dup 1)))\
(set (cc0)\
(match\_dup 2))]\
"dead\_or\_set\_p (insn, operands\[2])"\
"\*\
cc\_status.flags |= CC\_NO\_OVERFLOW;\
if (GET\_MODE (operands\[0]) == QImode)\
return "cvtwb %1,%0";\
else\
return "cvtwh %1,%0";\
")\
;\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\
;\
; DImode Patterns.\
;\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_

(define\_expand "extendsidi2"\
\[(set (subreg:SI (match\_operand:DI 0 "register\_operand" "=r") 1)\
(match\_operand:SI 1 "general\_operand" "g"))\
(set (subreg:SI (match\_dup 0) 0)\
(subreg:SI (match\_dup 0) 1))\
(set (subreg:SI (match\_dup 0) 0)\
(ashiftrt:SI (subreg:SI (match\_dup 0) 0)\
(const\_int 31)))]\
""\
"")

(define\_insn "adddi3"\
\[(set (match\_operand:DI 0 "register\_operand" "=r")\
(plus:DI (match\_operand:DI 1 "register\_operand" "%0")\
(match\_operand:DI 2 "nonmemory\_operand" "rF")))]\
""\
"\*\
{\
rtx xoperands\[2];\
CC\_STATUS\_INIT;\
xoperands\[0] = gen\_rtx (REG, SImode, REGNO (operands\[0]) + 1);\
if (REG\_P (operands\[2]))\
xoperands\[1] = gen\_rtx (REG, SImode, REGNO (operands\[2]) + 1);\
else\
{\
xoperands\[1] = gen\_rtx (CONST\_INT, VOIDmode,\
CONST\_DOUBLE\_LOW (operands\[2]));\
operands\[2] = gen\_rtx (CONST\_INT, VOIDmode,\
CONST\_DOUBLE\_HIGH (operands\[2]));\
}\
output\_asm\_insn ("addw %1,%0", xoperands);\
return "addwc %2,%0";\
}")

(define\_insn "subdi3"\
\[(set (match\_operand:DI 0 "register\_operand" "=r")\
(minus:DI (match\_operand:DI 1 "register\_operand" "0")\
(match\_operand:DI 2 "nonmemory\_operand" "rF")))]\
""\
"\*\
{\
rtx xoperands\[2];\
CC\_STATUS\_INIT;\
xoperands\[0] = gen\_rtx (REG, SImode, REGNO (operands\[0]) + 1);\
if (REG\_P (operands\[2]))\
xoperands\[1] = gen\_rtx (REG, SImode, REGNO (operands\[2]) + 1);\
else\
{\
xoperands\[1] = gen\_rtx (CONST\_INT, VOIDmode,\
CONST\_DOUBLE\_LOW (operands\[2]));\
operands\[2] = gen\_rtx (CONST\_INT, VOIDmode,\
CONST\_DOUBLE\_HIGH (operands\[2]));\
}\
output\_asm\_insn ("subw %1,%0", xoperands);\
return "subwb %2,%0";\
}")

(define\_insn "iordi3"\
\[(set (match\_operand:DI 0 "register\_operand" "=r")\
(ior:DI (match\_operand:DI 1 "register\_operand" "%0")\
(match\_operand:DI 2 "nonmemory\_operand" "rF")))]\
""\
"\*\
{\
rtx xoperands\[2];\
CC\_STATUS\_INIT;\
xoperands\[0] = gen\_rtx (REG, SImode, REGNO (operands\[0]) + 1);\
if (REG\_P (operands\[2]))\
xoperands\[1] = gen\_rtx (REG, SImode, REGNO (operands\[2]) + 1);\
else\
{\
xoperands\[1] = gen\_rtx (CONST\_INT, VOIDmode,\
CONST\_DOUBLE\_LOW (operands\[2]));\
operands\[2] = gen\_rtx (CONST\_INT, VOIDmode,\
CONST\_DOUBLE\_HIGH (operands\[2]));\
}\
output\_asm\_insn ("orw %1,%0", xoperands);\
return "orw %2,%0";\
}")

(define\_insn "anddi3"\
\[(set (match\_operand:DI 0 "register\_operand" "=r")\
(and:DI (match\_operand:DI 1 "register\_operand" "%0")\
(match\_operand:DI 2 "nonmemory\_operand" "rF")))]\
""\
"\*\
{\
rtx xoperands\[2];\
CC\_STATUS\_INIT;\
xoperands\[0] = gen\_rtx (REG, SImode, REGNO (operands\[0]) + 1);\
if (REG\_P (operands\[2]))\
xoperands\[1] = gen\_rtx (REG, SImode, REGNO (operands\[2]) + 1);\
else\
{\
xoperands\[1] = gen\_rtx (CONST\_INT, VOIDmode,\
CONST\_DOUBLE\_LOW (operands\[2]));\
operands\[2] = gen\_rtx (CONST\_INT, VOIDmode,\
CONST\_DOUBLE\_HIGH (operands\[2]));\
}\
output\_asm\_insn ("andw %1,%0", xoperands);\
return "andw %2,%0";\
}")

(define\_insn "xordi3"\
\[(set (match\_operand:DI 0 "register\_operand" "=r")\
(xor:DI (match\_operand:DI 1 "register\_operand" "%0")\
(match\_operand:DI 2 "nonmemory\_operand" "rF")))]\
""\
"\*\
{\
rtx xoperands\[2];\
CC\_STATUS\_INIT;\
xoperands\[0] = gen\_rtx (REG, SImode, REGNO (operands\[0]) + 1);\
if (REG\_P (operands\[2]))\
xoperands\[1] = gen\_rtx (REG, SImode, REGNO (operands\[2]) + 1);\
else\
{\
xoperands\[1] = gen\_rtx (CONST\_INT, VOIDmode,\
CONST\_DOUBLE\_LOW (operands\[2]));\
operands\[2] = gen\_rtx (CONST\_INT, VOIDmode,\
CONST\_DOUBLE\_HIGH (operands\[2]));\
}\
output\_asm\_insn ("xorw %1,%0", xoperands);\
return "xorw %2,%0";\
}")\
;;- Local variables:\
;;- mode:emacs-lisp\
;;- comment-start: ";;- "\
;;- eval: (set-syntax-table (copy-sequence (syntax-table)))\
;;- eval: (modify-syntax-entry ?] ")\[")\
;;- eval: (modify-syntax-entry ?{ "(}")\
;;- eval: (modify-syntax-entry ?} "){")\
;;- End:
