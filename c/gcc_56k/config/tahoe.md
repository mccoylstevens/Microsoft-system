# tahoe

;;- Machine description for GNU compiler\
;;- Tahoe version\
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

; File: tahoe.md\
;\
; This port made at the University of Buffalo by Devon Bowen,\
; Dale Wiles and Kevin Zachmann.\
;\
; Mail bugs reports or fixes to: gcc@cs.buffalo.edu

; movdi must call the output\_move\_double routine to move it around since\
; the tahoe doesn't efficiently support 8 bit moves.

(define\_insn "movdi"\
\[(set (match\_operand:DI 0 "general\_operand" "=g")\
(match\_operand:DI 1 "general\_operand" "g"))]\
""\
"\*\
{\
CC\_STATUS\_INIT;\
return output\_move\_double (operands);\
}")

; The trick in the movsi is accessing the contents of the sp register. The\
; tahoe doesn't allow you to access it directly so you have to access the\
; address of the top of the stack instead.

(define\_insn "movsi"\
\[(set (match\_operand:SI 0 "general\_operand" "=g")\
(match\_operand:SI 1 "general\_operand" "g"))]\
""\
"\*\
{\
rtx link;\
if (operands\[1] == const1\_rtx\
&& (link = find\_reg\_note (insn, REG\_WAS\_0, 0))\
&& ! XEXP (link, 0)->volatil\
&& GET\_CODE (XEXP (link, 0)) != NOTE\
&& no\_labels\_between\_p (XEXP (link, 0), insn))\
return "incl %0";\
if (GET\_CODE (operands\[1]) == SYMBOL\_REF || GET\_CODE (operands\[1]) == CONST)\
{\
if (push\_operand (operands\[0], SImode))\
return "pushab %a1";\
return "movab %a1,%0";\
}\
if (operands\[1] == const0\_rtx)\
return "clrl %0";\
if (push\_operand (operands\[0], SImode))\
return "pushl %1";\
if (GET\_CODE (operands\[1]) == REG && REGNO (operands\[1]) == 14)\
return "moval (sp),%0";\
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
&& ! XEXP (link, 0)->volatil\
&& GET\_CODE (XEXP (link, 0)) != NOTE\
&& no\_labels\_between\_p (XEXP (link, 0), insn))\
return "incw %0";\
if (operands\[1] == const0\_rtx)\
return "clrw %0";\
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
return "movb %1,%0";\
}")

; movsf has three cases since they can move from one place to another\
; or to/from the fpp and since different instructions are needed for\
; each case. The fpp related instructions don't set the flags properly.

(define\_insn "movsf"\
\[(set (match\_operand:SF 0 "general\_operand" "=g,=a,=g")\
(match\_operand:SF 1 "general\_operand" "g,g,a"))]\
""\
"\*\
{\
CC\_STATUS\_INIT;\
switch (which\_alternative)\
{\
case 0:\
return "movl %1,%0";\
case 1:\
return "ldf %1";\
case 2:\
return "stf %0";\
}\
}")

; movdf has a number of different cases. If it's going to or from\
; the fpp, use the special instructions to do it. If not, use the\
; output\_move\_double function.

(define\_insn "movdf"\
\[(set (match\_operand:DF 0 "general\_operand" "=a,=g,?=g")\
(match\_operand:DF 1 "general\_operand" "g,a,g"))]\
""\
"\*\
{\
CC\_STATUS\_INIT;\
switch (which\_alternative)\
{\
case 0:\
return "ldd %1";\
case 1:\
if (push\_operand (operands\[0], DFmode))\
return "pushd";\
else\
return "std %0";\
case 2:\
return output\_move\_double (operands);\
}\
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
return "addl2 %2,%0";\
}\
if (rtx\_equal\_p (operands\[0], operands\[2]))\
return "addl2 %1,%0";\
if (GET\_CODE (operands\[2]) == CONST\_INT\
&& GET\_CODE (operands\[1]) == REG)\
{\
if (push\_operand (operands\[0], SImode))\
return "pushab %c2(%1)";\
return "movab %c2(%1),%0";\
}\
if (GET\_CODE (operands\[2]) == CONST\_INT\
&& (unsigned) (- INTVAL (operands\[2])) < 64)\
return "subl3 $%n2,%1,%0";\
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
}")

; addsf3 can only add into the fpp register since the fpp is treated\
; as a separate unit in the machine. It also doesn't set the flags at\
; all.

(define\_insn "addsf3"\
\[(set (match\_operand:SF 0 "register\_operand" "=a")\
(plus:SF (match\_operand:SF 1 "register\_operand" "%0")\
(match\_operand:SF 2 "general\_operand" "g")))]\
""\
"\*\
{\
CC\_STATUS\_INIT;\
return "addf %2";\
}")

; adddf3 can only add into the fpp reg since the fpp is treated as a\
; separate entity. Doubles can only be read from a register or memory\
; since a double is not an immediate mode. Flags are not set by this\
; instruction.

(define\_insn "adddf3"\
\[(set (match\_operand:DF 0 "register\_operand" "=a")\
(plus:DF (match\_operand:DF 1 "register\_operand" "%0")\
(match\_operand:DF 2 "general\_operand" "rm")))]\
""\
"\*\
{\
CC\_STATUS\_INIT;\
return "addd %2";\
}")

; Subtraction from the sp (needed by the built in alloc funtion) needs\
; to be different since the sp cannot be directly read on the tahoe.\
; If it's a simple constant, you just use displacment. Otherwise, you\
; push the sp, and then do the subtraction off the stack.

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
if (GET\_CODE (operands\[0]) == REG && REGNO (operands\[0]) == 14)\
if (GET\_CODE (operands\[2]) == CONST\_INT)\
return "movab %n2(sp),sp";\
else\
return "pushab (sp);subl3 %2,(sp),sp";\
return "subl2 %2,%0";\
}\
if (rtx\_equal\_p (operands\[1], operands\[2]))\
return "clrl %0";\
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
if (rtx\_equal\_p (operands\[1], operands\[2]))\
return "clrw %0";\
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
if (rtx\_equal\_p (operands\[1], operands\[2]))\
return "clrb %0";\
return "subb3 %2,%1,%0";\
}")

; subsf3 can only subtract into the fpp accumulator due to the way\
; the fpp reg is limited by the instruction set. This also doesn't\
; bother setting up flags.

(define\_insn "subsf3"\
\[(set (match\_operand:SF 0 "register\_operand" "=a")\
(minus:SF (match\_operand:SF 1 "register\_operand" "0")\
(match\_operand:SF 2 "general\_operand" "g")))]\
""\
"\*\
{\
CC\_STATUS\_INIT;\
return "subf %2";\
}")

; subdf3 is set up to subtract into the fpp reg due to limitations\
; of the fpp instruction set. Doubles can not be immediate. This\
; instruction does not set the flags.

(define\_insn "subdf3"\
\[(set (match\_operand:DF 0 "register\_operand" "=a")\
(minus:DF (match\_operand:DF 1 "register\_operand" "0")\
(match\_operand:DF 2 "general\_operand" "rm")))]\
""\
"\*\
{\
CC\_STATUS\_INIT;\
return "subd %2";\
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

; mulsf3 can only multiply into the fpp accumulator due to limitations\
; of the fpp. It also does not set the condition codes properly.

(define\_insn "mulsf3"\
\[(set (match\_operand:SF 0 "register\_operand" "=a")\
(mult:SF (match\_operand:SF 1 "register\_operand" "%0")\
(match\_operand:SF 2 "general\_operand" "g")))]\
""\
"\*\
{\
CC\_STATUS\_INIT;\
return "mulf %2";\
}")

; muldf3 can only multiply into the fpp reg since the fpp is limited\
; from the rest. Doubles may not be immediate mode. This does not set\
; the flags like GCC would expect.

(define\_insn "muldf3"\
\[(set (match\_operand:DF 0 "register\_operand" "=a")\
(mult:DF (match\_operand:DF 1 "register\_operand" "%0")\
(match\_operand:DF 2 "general\_operand" "rm")))]\
""\
"\*\
{\
CC\_STATUS\_INIT;\
return "muld %2";\
}")

(define\_insn "divsi3"\
\[(set (match\_operand:SI 0 "general\_operand" "=g")\
(div:SI (match\_operand:SI 1 "general\_operand" "g")\
(match\_operand:SI 2 "general\_operand" "g")))]\
""\
"\*\
{\
if (rtx\_equal\_p (operands\[1], operands\[2]))\
return "movl $1,%0";\
if (operands\[1] == const0\_rtx)\
return "clrl %0";\
if (GET\_CODE (operands\[2]) == CONST\_INT\
&& INTVAL (operands\[2]) == -1)\
return "mnegl %1,%0";\
if (rtx\_equal\_p (operands\[0], operands\[1]))\
return "divl2 %2,%0";\
return "divl3 %2,%1,%0";\
}")

; divsf3 must divide into the fpp accumulator. Flags are not set by\
; this instruction, so they are cleared.

(define\_insn "divsf3"\
\[(set (match\_operand:SF 0 "register\_operand" "=a")\
(div:SF (match\_operand:SF 1 "register\_operand" "0")\
(match\_operand:SF 2 "general\_operand" "g")))]\
""\
"\*\
{\
CC\_STATUS\_INIT;\
return "divf %2";\
}")

; divdf3 also must divide into the fpp reg so optimization isn't\
; possible. Note that doubles cannot be immediate. The flags here\
; are not set correctly so they must be ignored.

(define\_insn "divdf3"\
\[(set (match\_operand:DF 0 "register\_operand" "=a")\
(div:DF (match\_operand:DF 1 "register\_operand" "0")\
(match\_operand:DF 2 "general\_operand" "rm")))]\
""\
"\*\
{\
CC\_STATUS\_INIT;\
return "divd %2";\
}")

(define\_insn "andsi3"\
\[(set (match\_operand:SI 0 "general\_operand" "=g")\
(and:SI (match\_operand:SI 1 "general\_operand" "g")\
(match\_operand:SI 2 "general\_operand" "g")))]\
""\
"andl3 %2,%1,%0")

(define\_insn "andhi3"\
\[(set (match\_operand:HI 0 "general\_operand" "=g")\
(and:HI (match\_operand:HI 1 "general\_operand" "g")\
(match\_operand:HI 2 "general\_operand" "g")))]\
""\
"andw3 %1,%2,%0")

(define\_insn "andqi3"\
\[(set (match\_operand:QI 0 "general\_operand" "=g")\
(and:QI (match\_operand:QI 1 "general\_operand" "g")\
(match\_operand:QI 2 "general\_operand" "g")))]\
""\
"andb3 %1,%2,%0")

(define\_insn "iorsi3"\
\[(set (match\_operand:SI 0 "general\_operand" "=g")\
(ior:SI (match\_operand:SI 1 "general\_operand" "g")\
(match\_operand:SI 2 "general\_operand" "g")))]\
""\
"orl3 %2,%1,%0")

(define\_insn "iorhi3"\
\[(set (match\_operand:HI 0 "general\_operand" "=g")\
(ior:HI (match\_operand:HI 1 "general\_operand" "g")\
(match\_operand:HI 2 "general\_operand" "g")))]\
""\
"orw3 %2,%1,%0")

(define\_insn "iorqi3"\
\[(set (match\_operand:QI 0 "general\_operand" "=g")\
(ior:QI (match\_operand:QI 1 "general\_operand" "g")\
(match\_operand:QI 2 "general\_operand" "g")))]\
""\
"orb3 %2,%1,%0")

(define\_insn "xorsi3"\
\[(set (match\_operand:SI 0 "general\_operand" "=g")\
(xor:SI (match\_operand:SI 1 "general\_operand" "g")\
(match\_operand:SI 2 "general\_operand" "g")))]\
""\
"xorl3 %1,%2,%0")

(define\_insn "xorhi3"\
\[(set (match\_operand:HI 0 "general\_operand" "=g")\
(xor:HI (match\_operand:HI 1 "general\_operand" "g")\
(match\_operand:HI 2 "general\_operand" "g")))]\
""\
"xorw3 %1,%2,%0")

(define\_insn "xorqi3"\
\[(set (match\_operand:QI 0 "general\_operand" "=g")\
(xor:QI (match\_operand:QI 1 "general\_operand" "g")\
(match\_operand:QI 2 "general\_operand" "g")))]\
""\
"xorb3 %1,%2,%0")

; Shifts on the tahoe are expensive, so try to do an add instead.

(define\_insn "ashlsi3"\
\[(set (match\_operand:SI 0 "general\_operand" "=g")\
(ashift:SI (match\_operand:SI 1 "general\_operand" "g")\
(match\_operand:QI 2 "general\_operand" "g")))]\
""\
"\*\
{\
if (operands\[2] == const1\_rtx && rtx\_equal\_p (operands\[0], operands\[1]))\
{\
CC\_STATUS\_INIT;\
return "addl2 %0,%0";\
}\
return "shal %2,%1,%0";\
}")

(define\_insn "ashrsi3"\
\[(set (match\_operand:SI 0 "general\_operand" "=g")\
(ashiftrt:SI (match\_operand:SI 1 "general\_operand" "g")\
(match\_operand:QI 2 "general\_operand" "g")))]\
""\
"shar %2,%1,%0")

; Shifts are very expensive, so try to do an add if possible.

(define\_insn "lshlsi3"\
\[(set (match\_operand:SI 0 "general\_operand" "=g")\
(lshift:SI (match\_operand:SI 1 "general\_operand" "g")\
(match\_operand:QI 2 "general\_operand" "g")))]\
""\
"\*\
{\
if (operands\[2] == const1\_rtx && rtx\_equal\_p (operands\[0], operands\[1]))\
{\
CC\_STATUS\_INIT;\
return "addl2 %0,%0";\
}\
return "shll %2,%1,%0";\
}")

(define\_insn "lshrsi3"\
\[(set (match\_operand:SI 0 "general\_operand" "=g")\
(lshiftrt:SI (match\_operand:SI 1 "general\_operand" "g")\
(match\_operand:QI 2 "general\_operand" "g")))]\
""\
"shrl %2,%1,%0")

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
"mnegb %1,%0")

; negsf2 can only negate the value already in the fpp accumulator.\
; The value remains in the fpp accumulator. No flags are set.

(define\_insn "negsf2"\
\[(set (match\_operand:SF 0 "register\_operand" "=a,=a")\
(neg:SF (match\_operand:SF 1 "register\_operand" "a,g")))]\
""\
"\*\
{\
CC\_STATUS\_INIT;\
switch (which\_alternative)\
{\
case 0:\
return "negf";\
case 1:\
return "lnf %1";\
}\
}")

; negdf2 can only negate the value already in the fpp accumulator.\
; The value remains in the fpp accumulator. No flags are set.

(define\_insn "negdf2"\
\[(set (match\_operand:DF 0 "register\_operand" "=a,=a")\
(neg:DF (match\_operand:DF 1 "register\_operand" "a,g")))]\
""\
"\*\
{\
CC\_STATUS\_INIT;\
switch (which\_alternative)\
{\
case 0:\
return "negd";\
case 1:\
return "lnd %1";\
}\
}")

; sqrtsf2 tahoe can calculate the square root of a float in the\
; fpp accumulator. The answer remains in the fpp accumulator. No\
; flags are set by this function.

(define\_insn "sqrtsf2"\
\[(set (match\_operand:SF 0 "register\_operand" "=a")\
(sqrt:SF (match\_operand:SF 1 "register\_operand" "0")))]\
""\
"\*\
{\
CC\_STATUS\_INIT;\
return "sqrtf";\
}")

; ffssi2 tahoe instruction gives one less than GCC desired result for\
; any given input. So the increment is necessary here.

(define\_insn "ffssi2"\
\[(set (match\_operand:SI 0 "general\_operand" "=g")\
(ffs:SI (match\_operand:SI 1 "general\_operand" "g")))]\
""\
"ffs %1,%0;incl %0")

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
"mcomb %1,%0")

; cmpsi works fine, but due to microcode problems, the tahoe doesn't\
; properly compare hi's and qi's. Leaving them out seems to be acceptable\
; to the compiler, so they were left out. Compares of the stack are\
; possible, though.

;; Put cmpsi first among compare insns so it matches two CONST\_INT operands.

(define\_insn "cmpsi"\
\[(set (cc0)\
(compare (match\_operand:SI 0 "general\_operand" "g")\
(match\_operand:SI 1 "general\_operand" "g")))]\
""\
"cmpl %0,%1")

; cmpsf similar to vax, but first operand is expected to be in the\
; fpp accumulator.

(define\_insn "cmpsf"\
\[(set (cc0)\
(compare (match\_operand:SF 0 "register\_operand" "a")\
(match\_operand:SF 1 "general\_operand" "g")))]\
""\
"cmpf %1")

; cmpdf similar to vax, but first operand is expected to be in the\
; fpp accumulator. Immediate doubles not allowed.

(define\_insn "cmpdf"\
\[(set (cc0)\
(compare (match\_operand:DF 0 "register\_operand" "a")\
(match\_operand:DF 1 "general\_operand" "rm")))]\
""\
"cmpd %1")

;; Put tstsi first among test insns so it matches a CONST\_INT operand.

(define\_insn "tstsi"\
\[(set (cc0)\
(match\_operand:SI 0 "general\_operand" "g"))]\
""\
"tstl %0")

; Small tests from memory are normal, but testing from registers don't\
; expand the data properly. So test in this case does a convert and tests\
; the new register data from the stack.

(define\_insn "tsthi"\
\[(set (cc0)\
(match\_operand:HI 0 "general\_operand" "m,?r"))]\
""\
"\*\
{\
switch (which\_alternative)\
{\
case 0:\
return "tstw %0";\
case 1:\
return "pushl %0;cvtwl 2(sp),(sp);tstl (sp)+";\
}\
}")

; Small tests from memory are normal, but testing from registers don't\
; expand the data properly. So test in this case does a convert and tests\
; the new register data from the stack.

(define\_insn "tstqi"\
\[(set (cc0)\
(match\_operand:QI 0 "general\_operand" "m,?r"))]\
""\
"\*\
{\
switch (which\_alternative)\
{\
case 0:\
return "tstb %0";\
case 1:\
return "pushl %0;cvtbl 3(sp),(sp);tstl (sp)+";\
}\
}")

; tstsf compares a given value to a value already in the fpp accumulator.\
; No flags are set by this so ignore them.

(define\_insn "tstsf"\
\[(set (cc0)\
(match\_operand:SF 0 "register\_operand" "a"))]\
""\
"tstf")

; tstdf compares a given value to a value already in the fpp accumulator.\
; immediate doubles not allowed. Flags are ignored after this.

(define\_insn "tstdf"\
\[(set (cc0)\
(match\_operand:DF 0 "register\_operand" "a"))]\
""\
"tstd")

; movstrhi tahoe instruction does not load registers by itself like\
; the vax counterpart does. registers 0-2 must be primed by hand.\
; we have loaded the registers in the order: dst, src, count.

(define\_insn "movstrhi"\
\[(set (match\_operand:BLK 0 "general\_operand" "p")\
(match\_operand:BLK 1 "general\_operand" "p"))\
(use (match\_operand:HI 2 "general\_operand" "g"))\
(clobber (reg:SI 0))\
(clobber (reg:SI 1))\
(clobber (reg:SI 2))]\
""\
"movab %0,r1;movab %1,r0;movl %2,r2;movblk")

; floatsisf2 on tahoe converts the long from reg/mem into the fpp\
; accumulator. There are no hi and qi counterparts. Flags are not\
; set correctly here.

(define\_insn "floatsisf2"\
\[(set (match\_operand:SF 0 "register\_operand" "=a")\
(float:SF (match\_operand:SI 1 "general\_operand" "g")))]\
""\
"\*\
{\
CC\_STATUS\_INIT;\
return "cvlf %1";\
}")

; floatsidf2 on tahoe converts the long from reg/mem into the fpp\
; accumulator. There are no hi and qi counterparts. Flags are not\
; set correctly here.

(define\_insn "floatsidf2"\
\[(set (match\_operand:DF 0 "register\_operand" "=a")\
(float:DF (match\_operand:SI 1 "general\_operand" "g")))]\
""\
"\*\
{\
CC\_STATUS\_INIT;\
return "cvld %1";\
}")

; fix\_truncsfsi2 to convert a float to long, tahoe must have the float\
; in the fpp accumulator. Flags are not set here.

(define\_insn "fix\_truncsfsi2"\
\[(set (match\_operand:SI 0 "general\_operand" "=g")\
(fix:SI (fix:SF (match\_operand:SF 1 "register\_operand" "a"))))]\
""\
"\*\
{\
CC\_STATUS\_INIT;\
return "cvfl %0";\
}")

; fix\_truncsfsi2 to convert a double to long, tahoe must have the double\
; in the fpp accumulator. Flags are not set here.

(define\_insn "fix\_truncdfsi2"\
\[(set (match\_operand:SI 0 "general\_operand" "=g")\
(fix:SI (fix:DF (match\_operand:DF 1 "register\_operand" "a"))))]\
""\
"\*\
{\
CC\_STATUS\_INIT;\
return "cvdl %0";\
}")

(define\_insn "truncsihi2"\
\[(set (match\_operand:HI 0 "general\_operand" "=g")\
(truncate:HI (match\_operand:SI 1 "general\_operand" "g")))]\
""\
"cvtlw %1,%0")

(define\_insn "truncsiqi2"\
\[(set (match\_operand:QI 0 "general\_operand" "=g")\
(truncate:QI (match\_operand:SI 1 "general\_operand" "g")))]\
""\
"cvtlb %1,%0")

(define\_insn "trunchiqi2"\
\[(set (match\_operand:QI 0 "general\_operand" "=g")\
(truncate:QI (match\_operand:HI 1 "general\_operand" "g")))]\
""\
"cvtwb %1,%0")

; The fpp related instructions don't set flags, so ignore them\
; after this instruction.

(define\_insn "truncdfsf2"\
\[(set (match\_operand:SF 0 "register\_operand" "=a")\
(float\_truncate:SF (match\_operand:DF 1 "register\_operand" "0")))]\
""\
"\*\
{\
CC\_STATUS\_INIT;\
return "cvdf";\
}")

; This monster is to cover for the Tahoe's nasty habit of not extending\
; a number if the source is in a register. (It just moves it!) Case 0 is\
; a normal extend from memory. Case 1 does the extension from the top of\
; the stack. Extension from the stack doesn't set the flags right since\
; the moval changes them.

(define\_insn "extendhisi2"\
\[(set (match\_operand:SI 0 "general\_operand" "=g,?=g")\
(sign\_extend:SI (match\_operand:HI 1 "general\_operand" "m,r")))]\
""\
"\*\
{\
switch (which\_alternative)\
{\
case 0:\
return "cvtwl %1,%0";\
case 1:\
if (push\_operand(operands\[0], SImode))\
return "pushl %1;cvtwl 2(sp),(sp)";\
else\
{\
CC\_STATUS\_INIT;\
return "pushl %1;cvtwl 2(sp),%0;moval 4(sp),sp";\
}\
}\
}")

; This monster is to cover for the Tahoe's nasty habit of not extending\
; a number if the source is in a register. (It just moves it!) Case 0 is\
; a normal extend from memory. Case 1 does the extension from the top of\
; the stack. Extension from the stack doesn't set the flags right since\
; the moval changes them.

(define\_insn "extendqisi2"\
\[(set (match\_operand:SI 0 "general\_operand" "=g,?=g")\
(sign\_extend:SI (match\_operand:QI 1 "general\_operand" "m,r")))]\
""\
"\*\
{\
switch (which\_alternative)\
{\
case 0:\
return "cvtbl %1,%0";\
case 1:\
if (push\_operand(operands\[0], SImode))\
return "pushl %1;cvtbl 3(sp),(sp)";\
else\
{\
CC\_STATUS\_INIT;\
return "pushl %1;cvtbl 3(sp),%0;moval 4(sp),sp";\
}\
}\
}")

; This monster is to cover for the Tahoe's nasty habit of not extending\
; a number if the source is in a register. (It just moves it!) Case 0 is\
; a normal extend from memory. Case 1 does the extension from the top of\
; the stack. Extension from the stack doesn't set the flags right since\
; the moval changes them.

(define\_insn "extendqihi2"\
\[(set (match\_operand:HI 0 "general\_operand" "=g,?=g")\
(sign\_extend:HI (match\_operand:QI 1 "general\_operand" "m,r")))]\
""\
"\*\
{\
switch (which\_alternative)\
{\
case 0:\
return "cvtbw %1,%0";\
case 1:\
if (push\_operand(operands\[0], SImode))\
return "pushl %1;cvtbw 3(sp),2(sp)";\
else {\
CC\_STATUS\_INIT;\
return "pushl %1;cvtbw 3(sp),%0;moval 4(sp),sp";\
}\
}\
}")

; extendsfdf2 tahoe uses the fpp accumulator to do the extension.\
; It takes a float and loads it up directly as a double.

(define\_insn "extendsfdf2"\
\[(set (match\_operand:DF 0 "register\_operand" "=a")\
(float\_extend:DF (match\_operand:SF 1 "general\_operand" "g")))]\
""\
"\*\
{\
CC\_STATUS\_INIT;\
return "ldfd %1";\
}")

; movz works fine from memory but not from register for the same reasons\
; the cvt instructions don't work right. So we use the normal instruction\
; from memory and we use an and to simulate it from register. This is faster\
; than pulling it off the stack.

(define\_insn "zero\_extendhisi2"\
\[(set (match\_operand:SI 0 "general\_operand" "=g,?=g")\
(zero\_extend:SI (match\_operand:HI 1 "general\_operand" "m,r")))]\
""\
"\*\
{\
switch (which\_alternative)\
{\
case 0:\
return "movzwl %1,%0";\
case 1:\
return "andl3 $0xffff,%1,%0";\
}\
}")

; movz works fine from memory but not from register for the same reasons\
; the cvt instructions don't work right. So we use the normal instruction\
; from memory and we use an and to simulate it from register. This is faster\
; than pulling it off the stack.

(define\_insn "zero\_extendqihi2"\
\[(set (match\_operand:HI 0 "general\_operand" "=g,?=g")\
(zero\_extend:HI (match\_operand:QI 1 "general\_operand" "m,r")))]\
""\
"\*\
{\
switch (which\_alternative)\
{\
case 0:\
return "movzbw %1,%0";\
case 1:\
return "andw3 $0xff,%1,%0";\
}\
}")

; movz works fine from memory but not from register for the same reasons\
; the cvt instructions don't work right. So we use the normal instruction\
; from memory and we use an and to simulate it from register. This is faster\
; than pulling it off the stack.

(define\_insn "zero\_extendqisi2"\
\[(set (match\_operand:SI 0 "general\_operand" "=g,?=g")\
(zero\_extend:SI (match\_operand:QI 1 "general\_operand" "m,r")))]\
""\
"\*\
{\
switch (which\_alternative)\
{\
case 0:\
return "movzbl %1,%0";\
case 1:\
return "andl3 $0xff,%1,%0";\
}\
}")

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
"jlequ %l0")

; GCC does not account for register mask/argc longword. Thus the number\
; for the call = number bytes for args + 4

(define\_insn "call"\
\[(call (match\_operand:QI 0 "general\_operand" "g")\
(match\_operand:QI 1 "general\_operand" "g"))]\
""\
"\*\
{\
operands\[1] = gen\_rtx (CONST\_INT, VOIDmode, (INTVAL (operands\[1]) + 4));\
return "calls %1,%0";\
}")

; GCC does not account for register mask/argc longword. Thus the number\
; for the call = number bytes for args + 4

(define\_insn "call\_value"\
\[(set (match\_operand 0 "" "=g")\
(call (match\_operand:QI 1 "general\_operand" "g")\
(match\_operand:QI 2 "general\_operand" "g")))]\
""\
"\*\
{\
operands\[2] = gen\_rtx (CONST\_INT, VOIDmode, (INTVAL (operands\[2]) + 4));\
return "calls %2,%1";\
}")

(define\_insn "nop"\
\[(const\_int 0)]\
""\
"nop")

(define\_insn "return"\
\[(return)]\
""\
"ret")

; casesi, extracted from the vax code. The instructions are\
; very similar. Tahoe requires that the table be word aligned. GCC\
; places the table immediately after, thus the alignment directive.

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
"casel %0,%1,%2;.align %@")

(define\_insn "jump"\
\[(set (pc)\
(label\_ref (match\_operand 0 "" "")))]\
""\
"jbr %l0")

;; This is the list of all the non-standard insn patterns

; This is used to access the address of a byte. This is similar to\
; movqi, but the second operand had to be "address\_operand" type, so\
; it had to be an unnamed one.

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

; This is used to access the address of a word. This is similar to\
; movhi, but the second operand had to be "address\_operand" type, so\
; it had to be an unnamed one.

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

; This is used to access the address of a long. This is similar to\
; movsi, but the second operand had to be "address\_operand" type, so\
; it had to be an unnamed one.

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

; The tahoe doesn't have an 8 byte indexed move address command\
; and GCC needs it. To work around it, double the index (%2) and\
; then use the 4 byte indexed move address command.\
;\
;(define\_insn ""\
; \[(set (match\_operand:SI 0 "general\_operand" "=g")\
; (plus:SI (match\_operand:SI 1 "general\_operand" "g")\
; (mult:SI (match\_operand:SI 2 "register\_operand" "r")\
; (const\_int 8))))]\
; ""\
; "\*\
;{\
; if (GET\_CODE (operands\[0]) == REG &&\
; REGNO (operands\[0]) == REGNO (operands\[2])) {\
; return "shll $3,%2,%2;addl3 %1,%2,%0";\
; } else {\
; return "shll $3,%2,%2;addl3 %1,%2,%0;shrl $3,%2,%2"; }\
;}")

; Bit test longword instruction, same as vax.

(define\_insn ""\
\[(set (cc0)\
(and:SI (match\_operand:SI 0 "general\_operand" "g")\
(match\_operand:SI 1 "general\_operand" "g")))]\
""\
"bitl %0,%1")

; Bit test word instructions, same as vax.

(define\_insn ""\
\[(set (cc0)\
(and:HI (match\_operand:HI 0 "general\_operand" "g")\
(match\_operand:HI 1 "general\_operand" "g")))]\
""\
"bitw %0,%1")

; Bit test instructions, same as vax.

(define\_insn ""\
\[(set (cc0)\
(and:QI (match\_operand:QI 0 "general\_operand" "g")\
(match\_operand:QI 1 "general\_operand" "g")))]\
""\
"bitb %0,%1")

; bne counterpart. In case GCC reverses the conditional.

(define\_insn ""\
\[(set (pc)\
(if\_then\_else (eq (cc0)\
(const\_int 0))\
(pc)\
(label\_ref (match\_operand 0 "" ""))))]\
""\
"jneq %l0")

; beq counterpart. In case GCC reverses the conditional.

(define\_insn ""\
\[(set (pc)\
(if\_then\_else (ne (cc0)\
(const\_int 0))\
(pc)\
(label\_ref (match\_operand 0 "" ""))))]\
""\
"jeql %l0")

; ble counterpart. In case GCC reverses the conditional.

(define\_insn ""\
\[(set (pc)\
(if\_then\_else (gt (cc0)\
(const\_int 0))\
(pc)\
(label\_ref (match\_operand 0 "" ""))))]\
""\
"jleq %l0")

; bleu counterpart. In case GCC reverses the conditional.

(define\_insn ""\
\[(set (pc)\
(if\_then\_else (gtu (cc0)\
(const\_int 0))\
(pc)\
(label\_ref (match\_operand 0 "" ""))))]\
""\
"jlequ %l0")

; bge counterpart. In case GCC reverses the conditional.

(define\_insn ""\
\[(set (pc)\
(if\_then\_else (lt (cc0)\
(const\_int 0))\
(pc)\
(label\_ref (match\_operand 0 "" ""))))]\
""\
"jgeq %l0")

; bgeu counterpart. In case GCC reverses the conditional.

(define\_insn ""\
\[(set (pc)\
(if\_then\_else (ltu (cc0)\
(const\_int 0))\
(pc)\
(label\_ref (match\_operand 0 "" ""))))]\
""\
"jgequ %l0")

; blt counterpart. In case GCC reverses the conditional.

(define\_insn ""\
\[(set (pc)\
(if\_then\_else (ge (cc0)\
(const\_int 0))\
(pc)\
(label\_ref (match\_operand 0 "" ""))))]\
""\
"jlss %l0")

; bltu counterpart. In case GCC reverses the conditional.

(define\_insn ""\
\[(set (pc)\
(if\_then\_else (geu (cc0)\
(const\_int 0))\
(pc)\
(label\_ref (match\_operand 0 "" ""))))]\
""\
"jlssu %l0")

; bgt counterpart. In case GCC reverses the conditional.

(define\_insn ""\
\[(set (pc)\
(if\_then\_else (le (cc0)\
(const\_int 0))\
(pc)\
(label\_ref (match\_operand 0 "" ""))))]\
""\
"jgtr %l0")

; bgtu counterpart. In case GCC reverses the conditional.

(define\_insn ""\
\[(set (pc)\
(if\_then\_else (leu (cc0)\
(const\_int 0))\
(pc)\
(label\_ref (match\_operand 0 "" ""))))]\
""\
"jgtru %l0")

; casesi alternate form as found in vax code. This form is to\
; compensate for the table's offset being no distance (0 displacement)

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
"casel %0,$0,%1;.align %@")

; casesi alternate form as found in vax code. Another form to\
; compensate for the table's offset being no distance (0 displacement)

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
"casel %0,$0,%1 ;.align %@")
