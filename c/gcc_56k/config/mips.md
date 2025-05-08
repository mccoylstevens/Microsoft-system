# mips

;; Mips.md Naive version of Machine Description for MIPS\
;; Contributed by A. Lichnewsky, lich@inria.inria.fr\
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

;;\
;;------------------------------------------------------------------------\
;;\
;;\
;; ....................\
;;\
;; Peephole Optimizations for\
;;\
;; ARITHMETIC\
;;\
;; ....................\
;;\
;;- The following peepholes are\
;;- motivated by the fact that\
;;- stack movement result in some\
;;- cases in embarrassing sequences\
;;- of addiu SP,SP,int\
;;- addiu SP,SP,other\_int

```
				;;- --------------------
				;;- REMARK: this would be done better
				;;- by analysis of dependencies in
				;;- basic blocks, prior to REG ALLOC,
				;;- and simplification of trees:
				;;-      (+  (+ REG const) const)
				;;- ->   (+ REG newconst)
				;;- --------------------
```

(define\_peephole\
\[(set (match\_operand:SI 0 "general\_operand" "=r")\
(plus:SI (match\_operand:SI 1 "general\_operand" "r")\
(match\_operand:SI 2 "general\_operand" "IJ")))\
(set (match\_operand:SI 3 "general\_operand" "=r")\
(plus:SI (match\_dup 0)\
(match\_operand:SI 4 "general\_operand" "IJ")))]\
" /\* DATA FLOW SEMANTICS\*/\
(((REGNO (operands\[0])) == (REGNO (operands\[3])))\
||\
dead\_or\_set\_p (insn, operands\[0]))\
&&\
( /\* CONSTRAINTS _/_\
_(GET\_CODE (operands\[2]) == CONST\_INT)_\
_&& ((CONST\_OK\_FOR\_LETTER\_P (INTVAL (operands\[2]), 'I'))_\
_||(CONST\_OK\_FOR\_LETTER\_P (INTVAL (operands\[2]), 'J')))_\
_&& (GET\_CODE (operands\[4]) == CONST\_INT)_\
_&& ((CONST\_OK\_FOR\_LETTER\_P (INTVAL (operands\[4]), 'I'))_\
_||(CONST\_OK\_FOR\_LETTER\_P (INTVAL (operands\[4]), 'J'))))_\
_"_\
_"_\
{\
rtx ops\[3];\
int i;\
i = INTVAL (operands\[2]) + INTVAL (operands\[4]);\
if (i == 0)\
{\
if ((REGNO (operands\[3])) == (REGNO (operands\[1])))\
{\
if ((REGNO (operands\[0])) == (REGNO (operands\[3])))\
return "\t\t# NULL %0 <- %1 + %2; %3 <- %0 + %4";\
else\
return "\t\t# NULL %0 <- %1 + %2; %3 <- %0 + %4 and dead %0";\
}\
else\
return "add%:\t%3,%1,$0\t# %0 <- %1 + %2; %3 <- %0 + %4 and dead %0";\
}\
else\
{\
ops\[0] = operands\[3];\
ops\[1] = operands\[1];\
ops\[2] = gen\_rtx (CONST\_INT, VOIDmode, i);\
output\_asm\_insn ("addi%:\t%0,%1,%2\t# simplification of:", ops);\
return "\t\t\t# %0 <- %1 + %2; %3 <- %0 + %4 and dead %0";\
}\
}")

(define\_peephole\
\[(set (match\_operand:SI 0 "general\_operand" "=r")\
(plus:SI (match\_operand:SI 1 "general\_operand" "r")\
(match\_operand:SI 2 "general\_operand" "IJ")))\
(set (match\_operand:SI 3 "general\_operand" "=r")\
(minus:SI (match\_dup 0)\
(match\_operand:SI 4 "general\_operand" "IJ")))]\
"(((REGNO (operands\[0])) == (REGNO (operands\[3])))\
||\
dead\_or\_set\_p (insn, operands\[0]))\
&&\
( /\* CONSTRAINTS _/_\
_(GET\_CODE (operands\[2]) == CONST\_INT)_\
_&& ((CONST\_OK\_FOR\_LETTER\_P (INTVAL (operands\[2]), 'I'))_\
_||(CONST\_OK\_FOR\_LETTER\_P (INTVAL (operands\[2]), 'J')))_\
_&& (GET\_CODE (operands\[4]) == CONST\_INT)_\
_&& ((CONST\_OK\_FOR\_LETTER\_P (INTVAL (operands\[4]), 'I'))_\
_||(CONST\_OK\_FOR\_LETTER\_P (INTVAL (operands\[4]), 'J'))))_\
_"_\
_"_\
{\
rtx ops\[3];\
int i;\
i = INTVAL (operands\[2]) - INTVAL (operands\[4]);\
if (i == 0)\
{\
if ((REGNO (operands\[3])) == (REGNO (operands\[1])))\
{\
if ((REGNO (operands\[0])) == (REGNO (operands\[3])))\
return "\t\t# NULL %0 <- %1 + %2; %3 <- %0 - %4";\
else\
return "\t\t# NULL %0 <- %1 + %2; %3 <- %0 - %4 and dead %0";\
}\
else\
return "add%:\t%3,%1,$0\t# %0 <- %1 + %2; %3 <- %0 - %4 and dead %0";\
}\
else\
{\
ops\[0] = operands\[3];\
ops\[1] = operands\[1];\
ops\[2] = gen\_rtx (CONST\_INT, VOIDmode, i);\
output\_asm\_insn ("addi%:\t%0,%1,%2\t# simplification of:", ops);\
return "\t\t\t# %0 <- %1 + %2; %3 <- %0 - %4 and dead %0";\
}\
}")

(define\_peephole\
\[(set (match\_operand:SI 0 "general\_operand" "=r")\
(minus:SI (match\_operand:SI 1 "general\_operand" "r")\
(match\_operand:SI 2 "general\_operand" "IJ")))\
(set (match\_operand:SI 3 "general\_operand" "=r")\
(plus:SI (match\_dup 0)\
(match\_operand:SI 4 "general\_operand" "IJ")))]\
"(((REGNO (operands\[0])) == (REGNO (operands\[3])))\
||\
dead\_or\_set\_p (insn, operands\[0]))\
&&\
( /\* CONSTRAINTS _/_\
_(GET\_CODE (operands\[2]) == CONST\_INT)_\
_&& ((CONST\_OK\_FOR\_LETTER\_P (INTVAL (operands\[2]), 'I'))_\
_||(CONST\_OK\_FOR\_LETTER\_P (INTVAL (operands\[2]), 'J')))_\
_&& (GET\_CODE (operands\[4]) == CONST\_INT)_\
_&& ((CONST\_OK\_FOR\_LETTER\_P (INTVAL (operands\[4]), 'I'))_\
_||(CONST\_OK\_FOR\_LETTER\_P (INTVAL (operands\[4]), 'J'))))_\
_"_\
_"_\
{\
rtx ops\[3];\
int i;\
i = (- INTVAL (operands\[2])) + INTVAL (operands\[4]);\
if (i == 0)\
{\
if ((REGNO (operands\[3])) == (REGNO (operands\[1])))\
{\
if ((REGNO (operands\[0])) == (REGNO (operands\[3])))\
return "\t\t# NULL %0 <- %1 - %2; %3 <- %0 + %4";\
else\
return "\t\t# NULL %0 <- %1 - %2; %3 <- %0 + %4 and dead %0";\
}\
else\
return "add%:\t%3,%1,$0\t# %0 <- %1 - %2; %3 <- %0 + %4 and dead %0";\
}\
else\
{\
ops\[0] = operands\[3];\
ops\[1] = operands\[1];\
ops\[2] = gen\_rtx (CONST\_INT, VOIDmode, i);\
output\_asm\_insn ("addi%:\t%0,%1,%2\t# simplification of:", ops);\
return "\t\t\t# %0 <- %1 - %2; %3 <- %0 + %4 and dead %0";\
}\
}")

(define\_peephole\
\[(set (match\_operand:SI 0 "general\_operand" "=r")\
(minus:SI (match\_operand:SI 1 "general\_operand" "r")\
(match\_operand:SI 2 "general\_operand" "IJ")))\
(set (match\_operand:SI 3 "general\_operand" "=r")\
(minus:SI (match\_dup 0)\
(match\_operand:SI 4 "general\_operand" "IJ")))]\
"((REGNO (operands\[0]) == REGNO (operands\[3])\
|| dead\_or\_set\_p (insn, operands\[0]))\
&&\
(GET\_CODE (operands\[2]) == CONST\_INT\
&& (CONST\_OK\_FOR\_LETTER\_P (INTVAL (operands\[2]), 'I')\
|| CONST\_OK\_FOR\_LETTER\_P (INTVAL (operands\[2]), 'J'))\
&& GET\_CODE (operands\[4]) == CONST\_INT\
&& (CONST\_OK\_FOR\_LETTER\_P (INTVAL (operands\[4]), 'I')\
|| CONST\_OK\_FOR\_LETTER\_P (INTVAL (operands\[4]), 'J'))))"\
"\*\
{\
rtx ops\[3];\
int i = - (INTVAL (operands\[2]) + INTVAL (operands\[4]));

if (i == 0)\
{\
if (REGNO (operands\[3]) == REGNO (operands\[1]))\
{\
if (REGNO (operands\[0]) == REGNO (operands\[3]))\
return "\t\t# NULL %0 <- %1 - %2; %3 <- %0 - %4";\
else\
return "\t\t# NULL %0 <- %1 - %2; %3 <- %0 - %4 and dead %0";\
}\
else\
return "add%:\t%3,%1,$0\t# %0 <- %1 - %2; %3 <- %0 - %4 and dead %0";\
}\
else\
{\
ops\[0] = operands\[3];\
ops\[1] = operands\[1];\
ops\[2] = gen\_rtx (CONST\_INT, VOIDmode, i);\
output\_asm\_insn ("addi%:\t%0,%1,%2\t# simplification of:", ops);\
return "\t\t\t# %0 <- %1 - %2; %3 <- %0 - %4 and dead %0";\
}\
}")

\
;;\
;; ....................\
;;\
;; ARITHMETIC\
;;\
;; ....................\
;;

(define\_insn "adddf3"\
\[(set (match\_operand:DF 0 "general\_operand" "=f")\
(plus:DF (match\_operand:DF 1 "general\_operand" "%f")\
(match\_operand:DF 2 "general\_operand" "f")))]\
""\
"add.d\t%0,%1,%2")

(define\_insn "addsf3"\
\[(set (match\_operand:SF 0 "general\_operand" "=f")\
(plus:SF (match\_operand:SF 1 "general\_operand" "%f")\
(match\_operand:SF 2 "general\_operand" "f")))]\
""\
"add.s\t%0,%1,%2")

(define\_insn "addsi3"\
\[(set (match\_operand:SI 0 "register\_operand" "=r")\
(plus:SI (match\_operand:SI 1 "register\_operand" "%r")\
(match\_operand:SI 2 "general\_operand" "rIJ")))]\
""\
"\*\
{\
if (GET\_CODE (operands\[2]) == CONST\_INT)\
{\
if (CONST\_OK\_FOR\_LETTER\_P (INTVAL (operands\[2]), 'I'))\
return "addi%:\t%0,%1,%x2\t#addsi3\t%1,%d2 -> %0";\
else if (CONST\_OK\_FOR\_LETTER\_P (INTVAL (operands\[2]), 'J'))\
return "add%:\t%0,%1,$0\t#addsi3\t%1,%2 -> %0";\
else abort\_with\_insn (insn, "Constant does not fit descriptor");\
}\
else\
return "add%:\t%0,%1,%2\t#addsi3\t%1,%2 -> %0";\
}")

(define\_insn "addhi3"\
\[(set (match\_operand:HI 0 "general\_operand" "=r")\
(plus:HI (match\_operand:HI 1 "general\_operand" "%r")\
(match\_operand:HI 2 "general\_operand" "rIJ")))]\
""\
"\*\
{\
if (GET\_CODE (operands\[2]) == CONST\_INT)\
{\
if (CONST\_OK\_FOR\_LETTER\_P (INTVAL (operands\[2]), 'I'))\
return "addi%:\t%0,%1,%x2\t#addhi3\t%1,%d2 -> %0";\
else if (CONST\_OK\_FOR\_LETTER\_P (INTVAL (operands\[2]), 'J'))\
return "add%:\t%0,%1,$0\t#addhi3\t%1,%2 -> %0";\
else abort\_with\_insn (insn, "Constant does not fit descriptor");\
}\
else\
return "add%:\t%0,%1,%2\t#addhi3 %1,%2 -> %0";

}")

(define\_insn "addqi3"\
\[(set (match\_operand:QI 0 "general\_operand" "=r")\
(plus:QI (match\_operand:QI 1 "general\_operand" "%r")\
(match\_operand:QI 2 "general\_operand" "rIJ")))]\
""\
"\*\
{\
if (GET\_CODE (operands\[2]) == CONST\_INT)\
{\
if (CONST\_OK\_FOR\_LETTER\_P (INTVAL (operands\[2]), 'I'))\
return "addi%:\t%0,%1,%x2\t#addqi3\t%1,%d2 -> %0";\
else if (CONST\_OK\_FOR\_LETTER\_P (INTVAL (operands\[2]), 'J'))\
return "add%:\t%0,%1,$0\t#addqi3\t%1,%2 -> %0";\
else abort\_with\_insn (insn, "Constant does not fit descriptor");\
}\
else\
return "add%:\t%0,%1,%2\t#addqi3 %1,%2 -> %0";\
}")\
;;- All kinds of subtract instructions.

(define\_insn "subdf3"\
\[(set (match\_operand:DF 0 "general\_operand" "=f")\
(minus:DF (match\_operand:DF 1 "general\_operand" "f")\
(match\_operand:DF 2 "general\_operand" "f")))]\
""\
"sub.d\t%0,%1,%2")

(define\_insn "subsf3"\
\[(set (match\_operand:SF 0 "general\_operand" "=f")\
(minus:SF (match\_operand:SF 1 "general\_operand" "f")\
(match\_operand:SF 2 "general\_operand" "f")))]\
""\
"sub.s\t%0,%1,%2")

(define\_insn "subsi3"\
\[(set (match\_operand:SI 0 "general\_operand" "=r")\
(minus:SI (match\_operand:SI 1 "general\_operand" "r")\
(match\_operand:SI 2 "general\_operand" "rIJ")))]\
""\
"\*\
{\
if (GET\_CODE (operands\[2]) == CONST\_INT)\
{\
if (CONST\_OK\_FOR\_LETTER\_P (INTVAL (operands\[2]), 'I'))\
{\
rtx ops\[4];\
ops\[0] = operands\[0];\
ops\[1] = operands\[1];\
ops\[3] = operands\[2];\
ops\[2] = gen\_rtx (CONST\_INT, VOIDmode, -INTVAL (operands\[2]));\
output\_asm\_insn ("addi%:\t%0,%1,%x2\t#subsi3\t%1,%d3 -> %0",\
ops);\
return "";\
}\
else if (CONST\_OK\_FOR\_LETTER\_P (INTVAL (operands\[2]), 'J'))\
return "sub%:\t%0,%1,$0\t#subsi3\t%1,%2 -> %0";\
else abort\_with\_insn (insn, "Constant does not fit descriptor");\
}\
else\
return "sub%:\t%0,%1,%2\t#subsi3 %1,%2 -> %0";\
}")

(define\_insn "subhi3"\
\[(set (match\_operand:HI 0 "general\_operand" "=r")\
(minus:HI (match\_operand:HI 1 "general\_operand" "r")\
(match\_operand:HI 2 "general\_operand" "r")))]\
""\
"\*\
{\
if (GET\_CODE (operands\[2]) == CONST\_INT)\
{\
if (CONST\_OK\_FOR\_LETTER\_P (INTVAL (operands\[2]), 'I'))\
{\
rtx ops\[4];\
ops\[0] = operands\[0];\
ops\[1] = operands\[1];\
ops\[3] = operands\[2];\
ops\[2] = gen\_rtx (CONST\_INT, VOIDmode, -INTVAL (operands\[2]));\
output\_asm\_insn ("addi%:\t%0,%1,%x2\t#subhi3\t%1,%d3 -> %0"\
, ops);\
return "";\
}\
else if (CONST\_OK\_FOR\_LETTER\_P (INTVAL (operands\[2]), 'J'))\
return "sub%:\t%0,%1,$0\t#subhi3\t%1,%2 -> %0";\
else abort\_with\_insn (insn, "Constant does not fit descriptor");\
}\
else\
return "sub%:\t%0,%1,%2\t#subhi3 %1,%2 -> %0";\
}")

(define\_insn "subqi3"\
\[(set (match\_operand:QI 0 "general\_operand" "=r")\
(minus:QI (match\_operand:QI 1 "general\_operand" "r")\
(match\_operand:QI 2 "general\_operand" "r")))]\
""\
"\*\
{\
if (GET\_CODE (operands\[2]) == CONST\_INT)\
{\
if (CONST\_OK\_FOR\_LETTER\_P (INTVAL (operands\[2]), 'I'))\
{\
rtx ops\[4];\
ops\[0] = operands\[0];\
ops\[1] = operands\[1];\
ops\[3] = operands\[2];\
ops\[2] = gen\_rtx (CONST\_INT, VOIDmode, -INTVAL (operands\[2]));\
output\_asm\_insn ("addi%:\t%0,%1,%x2\t#subqi3\t%1,%d3 -> %0"\
, ops);\
return "";\
}\
else if (CONST\_OK\_FOR\_LETTER\_P (INTVAL (operands\[2]), 'J'))\
return "sub%:\t%0,%1,$0\t#subqi3\t%1,%2 -> %0";\
else abort\_with\_insn (insn, "Constant does not fit descriptor");\
}\
else\
return "sub%:\t%0,%1,%2\t#subqi3 %1,%2 -> %0";\
}")\
;;- Multiply instructions.

(define\_insn "muldf3"\
\[(set (match\_operand:DF 0 "general\_operand" "=f")\
(mult:DF (match\_operand:DF 1 "general\_operand" "%f")\
(match\_operand:DF 2 "general\_operand" "f")))]\
""\
"mul.d\t%0,%1,%2")

(define\_insn "mulsf3"\
\[(set (match\_operand:SF 0 "general\_operand" "=f")\
(mult:SF (match\_operand:SF 1 "general\_operand" "%f")\
(match\_operand:SF 2 "general\_operand" "f")))]\
""\
"mul.s\t%0,%1,%2")

(define\_insn "mulsi3"\
\[(set (match\_operand:SI 0 "general\_operand" "=r")\
(mult:SI (match\_operand:SI 1 "general\_operand" "%r")\
(match\_operand:SI 2 "general\_operand" "r")))]\
""\
"mul\t%0,%1,%2\t#mulsi3 %1,%2 -> %0")

(define\_insn "mulhi3"\
\[(set (match\_operand:HI 0 "general\_operand" "=r")\
(mult:HI (match\_operand:HI 1 "general\_operand" "%r")\
(match\_operand:HI 2 "general\_operand" "r")))]\
""\
"mul\t%0,%1,%2\t#mulhi3 %1,%2 -> %0")

(define\_insn "mulqi3"\
\[(set (match\_operand:QI 0 "general\_operand" "=r")\
(mult:QI (match\_operand:QI 1 "general\_operand" "%r")\
(match\_operand:QI 2 "general\_operand" "r")))]\
""\
"mul\t%0,%1,%2\t#mulhi3 %1,%2 -> %0")\
;;- Divide instructions.

(define\_insn "divdf3"\
\[(set (match\_operand:DF 0 "general\_operand" "=f")\
(div:DF (match\_operand:DF 1 "general\_operand" "f")\
(match\_operand:DF 2 "general\_operand" "f")))]\
""\
"div.d\t%0,\t%1,%2")

(define\_insn "divsf3"\
\[(set (match\_operand:SF 0 "general\_operand" "=f")\
(div:SF (match\_operand:SF 1 "general\_operand" "f")\
(match\_operand:SF 2 "general\_operand" "f")))]\
""\
"div.s\t%0,%1,%2")

(define\_insn "divsi3"\
\[(set (match\_operand:SI 0 "general\_operand" "=r")\
(div:SI (match\_operand:SI 1 "general\_operand" "r")\
(match\_operand:SI 2 "general\_operand" "r")))]\
""\
"div\t%0,%1,%2\t#divsi3 %1,%2 -> %0")

(define\_insn "divhi3"\
\[(set (match\_operand:HI 0 "general\_operand" "=r")\
(div:HI (match\_operand:HI 1 "general\_operand" "r")\
(match\_operand:HI 2 "general\_operand" "r")))]\
""\
"div\t%0,%1,%2\t#divhi3 %1,%2 -> %0")

(define\_insn "divqi3"\
\[(set (match\_operand:QI 0 "general\_operand" "=r")\
(div:QI (match\_operand:QI 1 "general\_operand" "r")\
(match\_operand:QI 2 "general\_operand" "r")))]\
""\
"div\t%0,%1,%2\t#divqi3 %1,%2 -> %0")\
;;\
;; ....................\
;;\
;; LOGICAL\
;;\
;; ....................\
;;

(define\_insn "anddi3"\
\[(set (match\_operand:DI 0 "general\_operand" "=r")\
(and:DI (match\_operand:DI 1 "general\_operand" "%r")\
(match\_operand:DI 2 "general\_operand" "r")))]\
""\
"\*\
{\
rtx xops\[3];\
if ((REGNO (operands\[0]) != (REGNO (operands\[1]) +1))\
&&\
(REGNO (operands\[0]) != (REGNO (operands\[2]) +1)))\
{\
/\* TAKE CARE OF OVERLAPS \*/\
xops\[0] = gen\_rtx (REG, SImode, REGNO (operands\[0]));\
xops\[1] = gen\_rtx (REG, SImode, REGNO (operands\[1]));\
xops\[2] = gen\_rtx (REG, SImode, REGNO (operands\[2]));\
output\_asm\_insn ("and\t%0,%1,%2\t#anddi %1,%2 -> %0", xops);\
xops\[0] = gen\_rtx (REG, SImode, REGNO (xops\[0])+1);\
xops\[1] = gen\_rtx (REG, SImode, REGNO (xops\[1])+1);\
xops\[2] = gen\_rtx (REG, SImode, REGNO (xops\[2])+1);\
output\_asm\_insn ("and\t%0,%1,%2\t#anddi %1,%2 -> %0", xops);\
}\
else\
abort ();\
return "";\
}")

(define\_insn "andsi3"\
\[(set (match\_operand:SI 0 "general\_operand" "=r,\&r")\
(and:SI (match\_operand:SI 1 "general\_operand" "%r,r")\
(match\_operand:SI 2 "general\_operand" "rJ,K")))]\
""\
"\*\
{\
rtx xops\[3];\
if (GET\_CODE (operands\[2]) == CONST\_INT)\
{\
if (CONST\_OK\_FOR\_LETTER\_P (INTVAL (operands\[2]), 'K'))\
if (INTVAL (operands\[2]) >= 0)\
{\
return "andi\t%0,%1,%x2\t#andsi3\t%1,%d2 -> %0";\
}\
else\
{\
xops\[0] = operands\[0];\
xops\[1] = operands\[1];;\
xops\[2] = gen\_rtx (CONST\_INT, VOIDmode,\
(INTVAL (operands\[2]))>>16);\
output\_asm\_insn ("lui\t%0,%x2\t#load higher part (andsi3)",\
xops);\
xops\[2] = gen\_rtx (CONST\_INT, VOIDmode,\
0xffff & (INTVAL (operands\[2])));\
output\_asm\_insn ("addi%:\t%0,$0,%x2\t#load lower part (andsi3)",\
xops);\
output\_asm\_insn ("and\t%0,%0,%1\t#andsi3", xops);\
return "";\
}\
else if (CONST\_OK\_FOR\_LETTER\_P (INTVAL (operands\[2]), 'J'))\
return "and\t%0,%1,$0\t#andsi3\t%1,%2 -> %0";\
else return "ERROR op2 not zero \t#andsi3\t%1,%2 -> %0";\
}\
else\
return "and\t%0,%1,%2\t#andsi3\t%1,%2 -> %0";\
}")

(define\_insn "andhi3"\
\[(set (match\_operand:HI 0 "general\_operand" "=r")\
(and:HI (match\_operand:HI 1 "general\_operand" "%r")\
(match\_operand:HI 2 "general\_operand" "r")))]\
""\
"and\t%0,%1,%2\t#andhi3 %1,%2 -> %0")

(define\_insn "andqi3"\
\[(set (match\_operand:QI 0 "general\_operand" "=r")\
(and:QI (match\_operand:QI 1 "general\_operand" "%r")\
(match\_operand:QI 2 "general\_operand" "r")))]\
""\
"and\t%0,%1,%2\t#andqi3 %1,%2 -> %0")

(define\_insn "iordi3"\
\[(set (match\_operand:DI 0 "general\_operand" "=r")\
(ior:DI (match\_operand:DI 1 "general\_operand" "%r")\
(match\_operand:DI 2 "general\_operand" "r")))]\
""\
"\*\
{\
rtx xops\[3];\
if ((REGNO (operands\[0]) != (REGNO (operands\[1]) +1))\
&&\
(REGNO (operands\[0]) != (REGNO (operands\[2]) +1)))\
{\
/\* TAKE CARE OF OVERLAPS \*/\
xops\[0] = gen\_rtx (REG, SImode, REGNO (operands\[0]));\
xops\[1] = gen\_rtx (REG, SImode, REGNO (operands\[1]));\
xops\[2] = gen\_rtx (REG, SImode, REGNO (operands\[2]));\
output\_asm\_insn ("or\t%0,%1,%2\t#iordi3 %1,%2 -> %0", xops);\
xops\[0] = gen\_rtx (REG, SImode, REGNO (xops\[0])+1);\
xops\[1] = gen\_rtx (REG, SImode, REGNO (xops\[1])+1);\
xops\[2] = gen\_rtx (REG, SImode, REGNO (xops\[2])+1);\
output\_asm\_insn ("or\t%0,%1,%2\t#iordi3 %1,%2 -> %0", xops);\
}\
else\
abort ();\
return "";\
}")

(define\_insn "iorsi3"\
\[(set (match\_operand:SI 0 "general\_operand" "=r")\
(ior:SI (match\_operand:SI 1 "general\_operand" "%r")\
(match\_operand:SI 2 "general\_operand" "rKJ")))]\
""\
"\*\
{\
if (GET\_CODE (operands\[2]) == CONST\_INT)\
{\
if (CONST\_OK\_FOR\_LETTER\_P (INTVAL (operands\[2]), 'K'))\
{\
return "ori\t%0,%1,%x2\t#iorsi3\t%1,%d2 -> %0";\
}\
else if (CONST\_OK\_FOR\_LETTER\_P (INTVAL (operands\[2]), 'J'))\
return "or\t%0,%1,$0\t#iorsi3\t%1,%2 -> %0";\
else return "ERROR op2 not zero \t#iorsi3\t%1,%2 -> %0";\
}\
else\
return "or\t%0,%1,%2\t#iorsi3\t%1,%2 -> %0";\
}")

(define\_insn "iorhi3"\
\[(set (match\_operand:HI 0 "general\_operand" "=r")\
(ior:HI (match\_operand:HI 1 "general\_operand" "%r")\
(match\_operand:HI 2 "general\_operand" "r")))]\
""\
"or\t%0,%1,%2\t#iorhi3 %1,%2 -> %0")

(define\_insn "iorqi3"\
\[(set (match\_operand:QI 0 "general\_operand" "=r")\
(ior:QI (match\_operand:QI 1 "general\_operand" "%r")\
(match\_operand:QI 2 "general\_operand" "r")))]\
""\
"or\t%0,%1,%2\t#iorqi3 %1,%2 -> %0")

(define\_insn "xordi3"\
\[(set (match\_operand:DI 0 "general\_operand" "=r")\
(xor:DI (match\_operand:DI 1 "general\_operand" "r")\
(match\_operand:DI 2 "general\_operand" "r")))]\
""\
"\*\
{\
rtx xops\[3];\
if ((REGNO (operands\[0]) != (REGNO (operands\[1]) +1))\
&&\
(REGNO (operands\[0]) != (REGNO (operands\[2]) +1)))\
{\
/\* TAKE CARE OF OVERLAPS \*/\
xops\[0] = gen\_rtx (REG, SImode, REGNO (operands\[0]));\
xops\[1] = gen\_rtx (REG, SImode, REGNO (operands\[1]));\
xops\[2] = gen\_rtx (REG, SImode, REGNO (operands\[2]));\
output\_asm\_insn ("xor\t%0,%1,%2\t#xordi3 %1,%2 -> %0", xops);\
xops\[0] = gen\_rtx (REG, SImode, REGNO (xops\[0])+1);\
xops\[1] = gen\_rtx (REG, SImode, REGNO (xops\[1])+1);\
xops\[2] = gen\_rtx (REG, SImode, REGNO (xops\[2])+1);\
output\_asm\_insn ("xor\t%0,%1,%2\t#xordi3 %1,%2 -> %0", xops);\
}\
else\
abort ();\
return "";\
}")

(define\_insn "xorsi3"\
\[(set (match\_operand:SI 0 "general\_operand" "=r")\
(xor:SI (match\_operand:SI 1 "general\_operand" "%r")\
(match\_operand:SI 2 "general\_operand" "rKJ")))]\
""\
"\*\
{\
if (GET\_CODE (operands\[2]) == CONST\_INT)\
{\
if (CONST\_OK\_FOR\_LETTER\_P (INTVAL (operands\[2]), 'K'))\
{\
return "xori\t%0,%1,%x2\t#xorsi3\t%1,%d2 -> %0";\
}\
else if (CONST\_OK\_FOR\_LETTER\_P (INTVAL (operands\[2]), 'J'))\
return "xor\t%0,%1,$0\t#xorsi3\t%1,%2 -> %0";\
else return "ERROR op2 not zero \t#xorsi3\t%1,%2 -> %0";\
}\
else\
return "xor\t%0,%1,%2\t#xorsi3\t%1,%2 -> %0";\
}")

(define\_insn "xorhi3"\
\[(set (match\_operand:HI 0 "general\_operand" "=r")\
(xor:HI (match\_operand:HI 1 "general\_operand" "%r")\
(match\_operand:HI 2 "general\_operand" "r")))]\
""\
"xor\t%0,%1,%2\t#xorhi3 %1,%2 -> %0")

(define\_insn "xorqi3"\
\[(set (match\_operand:QI 0 "general\_operand" "=r")\
(xor:QI (match\_operand:QI 1 "general\_operand" "%r")\
(match\_operand:QI 2 "general\_operand" "r")))]\
""\
"xor\t%0,%1,%2\t#xorqi3 %1,%2 -> %0")\
;;\
;; ....................\
;;\
;; TRUNCATION\
;;\
;; ....................

;; Extension and truncation insns.\
;; Those for integer source operand\
;; are ordered widest source type first.

(define\_insn "truncsiqi2"\
\[(set (match\_operand:QI 0 "general\_operand" "=r")\
(truncate:QI (match\_operand:SI 1 "general\_operand" "r")))]\
""\
"andi\t%0,%1,0xff\t#truncsiqi2\t %1 -> %0")

(define\_insn "truncsihi2"\
\[(set (match\_operand:HI 0 "general\_operand" "=r")\
(truncate:HI (match\_operand:SI 1 "general\_operand" "r")))]\
""\
"\*\
output\_asm\_insn ("sll\t%0,%1,0x10\t#truncsihi2\t %1 -> %0",\
operands);\
return "sra\t%0,%0,0x10\t#truncsihi2\t %1 -> %0";\
")

(define\_insn "trunchiqi2"\
\[(set (match\_operand:QI 0 "general\_operand" "=r")\
(truncate:QI (match\_operand:HI 1 "general\_operand" "r")))]\
""\
"andi\t%0,%1,0xff\t#trunchiqi2\t %1 -> %0")

(define\_insn "truncdfsf2"\
\[(set (match\_operand:SF 0 "general\_operand" "=f")\
(float\_truncate:SF (match\_operand:DF 1 "general\_operand" "f")))]\
""\
"cvt.s.d\t%0,%1\t#truncdfsf2\t %1 -> %0")\
;;\
;; ....................\
;;\
;; ZERO EXTENSION\
;;\
;; ....................

;; Extension insns.\
;; Those for integer source operand\
;; are ordered widest source type first.

(define\_insn "zero\_extendhisi2"\
\[(set (match\_operand:SI 0 "general\_operand" "=r,r")\
(zero\_extend:SI (match\_operand:HI 1 "general\_operand" "r,m")))]\
""\
"\*\
{\
if (which\_alternative == 0)\
{\
output\_asm\_insn ("sll\t%0,%1,0x10\t#zero\_extendhisi2\t %1 -> %0",\
operands);\
return "srl\t%0,%0,0x10\t#zero\_extendhisi2\t %1 -> %0";\
}\
else\
return "lhu\t%0,%1\t#zero extendhisi2 %1 -> %0";\
}")

(define\_insn "zero\_extendqihi2"\
\[(set (match\_operand:HI 0 "general\_operand" "=r")\
(zero\_extend:HI (match\_operand:QI 1 "general\_operand" "r")))]\
""\
"\*\
output\_asm\_insn ("sll\t%0,%1,0x18\t#zero\_extendqihi2\t %1 -> %0",\
operands);\
return "srl\t%0,%0,0x18\t#zero\_extendqihi2\t %1 -> %0";\
")

(define\_insn "zero\_extendqisi2"\
\[(set (match\_operand:SI 0 "general\_operand" "=r,r")\
(zero\_extend:SI (match\_operand:QI 1 "general\_operand" "r,m")))]\
""\
"\*\
{\
if (which\_alternative == 0)\
{\
return "andi\t%0,%1,0xff\t#zero\_extendqisi2\t %1 -> %0";\
}\
else\
return "lbu\t%0,%1\t#zero extendqisi2 %1 -> %0";\
}")

\
;;\
;; ....................\
;;\
;; SIGN EXTENSION\
;;\
;; ....................

;; Extension insns.\
;; Those for integer source operand\
;; are ordered widest source type first.

(define\_insn "extendhisi2"\
\[(set (match\_operand:SI 0 "general\_operand" "=r,r")\
(sign\_extend:SI (match\_operand:HI 1 "general\_operand" "r,m")))]\
""\
"\*\
{\
if (which\_alternative == 0)\
{\
output\_asm\_insn ("sll\t%0,%1,0x10\t#sign extendhisi2\t %1 -> %0",\
operands);\
return "sra\t%0,%0,0x10\t#sign extendhisi2\t %1 -> %0";\
}\
else\
return "lh\t%0,%1\t#sign extendhisi2 %1 -> %0";\
}")

(define\_insn "extendqihi2"\
\[(set (match\_operand:HI 0 "general\_operand" "=r")\
(sign\_extend:HI (match\_operand:QI 1 "general\_operand" "r")))]\
""\
"\*\
output\_asm\_insn ("sll\t%0,%1,0x18\t#sign extendqihi2\t %1 -> %0",\
operands);\
return "sra\t%0,%0,0x18\t#sign extendqihi2\t %1 -> %0";\
")

(define\_insn "extendqisi2"\
\[(set (match\_operand:SI 0 "general\_operand" "=r,r")\
(sign\_extend:SI (match\_operand:QI 1 "general\_operand" "r,m")))]\
""\
"\*\
{\
if (which\_alternative == 0)\
{\
output\_asm\_insn ("sll\t%0,%1,0x18\t#sign extendqisi2\t %1 -> %0",\
operands);\
return "sra\t%0,%0,0x18\t#sign extendqisi2\t %1 -> %0";\
}\
else\
return "lb\t%0,%1\t#sign extendqisi2 %1 -> %0";\
}")

(define\_insn "extendsfdf2"\
\[(set (match\_operand:DF 0 "general\_operand" "=f")\
(float\_extend:DF (match\_operand:SF 1 "general\_operand" "f")))]\
""\
"cvt.d.s\t%0,%1\t#extendsfdf2\t %1 -> %0")

;;\
;; ....................\
;;\
;; CONVERSIONS\
;;\
;; ....................

(define\_insn "fix\_truncdfsi2"\
\[(set (match\_operand:SI 0 "general\_operand" "=r")\
(fix:SI (fix:DF (match\_operand:DF 1 "general\_operand" "f"))))\
(clobber (reg:DF 44))]\
""\
"trunc.w.d\t$f12,%1,%0\t#fix\_truncdfsi2\t%1 -> %0;mfc1\t%0,$f12\t#fix\_truncdfsi2\t%1 -> %0")

(define\_insn "fix\_truncsfsi2"\
\[(set (match\_operand:SI 0 "general\_operand" "=r")\
(fix:SI (fix:SF (match\_operand:SF 1 "general\_operand" "f"))))\
(clobber (reg:DF 44))]\
""\
"trunc.w.s\t$f12,%1,%0\t#fix\_truncsfsi2\t%1 -> %0;mfc1\t%0,$f12\t#fix\_truncsfsi2\t%1 -> %0")

(define\_insn "floatsidf2"\
\[(set (match\_operand:DF 0 "general\_operand" "=f")\
(float:DF (match\_operand:SI 1 "general\_operand" "r")))]\
""\
"mtc1\t%1,%0\t#floatsidf2\t%1 -> %0;cvt.d.w\t%0,%0\t#floatsidf2\t%1 -> %0")

(define\_insn "floatsisf2"\
\[(set (match\_operand:SF 0 "general\_operand" "=f")\
(float:SF (match\_operand:SI 1 "general\_operand" "r")))]\
""\
"mtc1\t%1,%0\t#floatsisf2\t%1 -> %0;cvt.s.w\t%0,%0\t#floatsisf2\t%1 -> %0")\
;;- Wild things used when\
;;- unions make double and int\
;;- overlap.\
;;-\
;;- This must be supported\
;;- since corresponding code\
;;- gets generated

(define\_insn ""\
\[(set (subreg:DF (match\_operand:DI 0 "register\_operand" "=ry") 0)\
(match\_operand:DF 1 "register\_operand" "rf"))\
(clobber (match\_operand 2 "register\_operand" "rf"))]\
""\
"\*\
{\
rtx xops\[2];\
xops\[0] = gen\_rtx (REG, SImode, REGNO (operands\[0]));\
\#ifndef DECSTATION\
xops\[1] = gen\_rtx (REG, DFmode, REGNO (operands\[1])+1);\
\#else\
xops\[1] = gen\_rtx (REG, DFmode, REGNO (operands\[1]));\
\#endif\
output\_asm\_insn ("mfc1\t%0,%1\t# %1 -> (subreg:DF %0 0) \t likely Union"\
, xops);\
xops\[0] = gen\_rtx (REG, SImode, REGNO (operands\[0])+1);\
\#ifndef DECSTATION\
xops\[1] = gen\_rtx (REG, DFmode, REGNO (operands\[1]));\
\#else\
xops\[1] = gen\_rtx (REG, DFmode, REGNO (operands\[1])+1);\
\#endif\
output\_asm\_insn ("mfc1\t%0,%1\t# %1 -> (subreg:DF %0 0) "\
, xops);\
return ("");\
}")

(define\_insn ""\
\[(set (subreg:DF (match\_operand:DI 0 "register\_operand" "=ry") 0)\
(match\_operand:DF 1 "register\_operand" "rf"))]\
""\
"\*\
{\
rtx xops\[2];\
xops\[0] = gen\_rtx (REG, SImode, REGNO (operands\[0]));\
\#ifndef DECSTATION\
xops\[1] = gen\_rtx (REG, DFmode, REGNO (operands\[1])+1);\
\#else\
xops\[1] = gen\_rtx (REG, DFmode, REGNO (operands\[1]));\
\#endif\
output\_asm\_insn ("mfc1\t%0,%1\t# %1 -> (subreg:DF %0 0) \t likely Union"\
, xops);\
xops\[0] = gen\_rtx (REG, SImode, REGNO (operands\[0])+1);\
\#ifndef DECSTATION\
xops\[1] = gen\_rtx (REG, DFmode, REGNO (operands\[1]));\
\#else\
xops\[1] = gen\_rtx (REG, DFmode, REGNO (operands\[1])+1);\
\#endif\
output\_asm\_insn ("mfc1\t%0,%1\t# %1 -> (subreg:DF %0 0) "\
,xops);\
return ("");\
}")

(define\_insn ""\
\[(set (match\_operand:DF 0 "register\_operand" "=rf")\
(subreg:DF (match\_operand:DI 1 "register\_operand" "ry") 0))\
(clobber (match\_operand 2 "register\_operand" "rf"))]\
""\
"\*\
{\
rtx xops\[2];\
xops\[0] = gen\_rtx (REG, DFmode, REGNO (operands\[0]));\
\#ifndef DECSTATION\
xops\[1] = gen\_rtx (REG, SImode, REGNO (operands\[1])+1);\
\#else\
xops\[1] = gen\_rtx (REG, SImode, REGNO (operands\[1]));\
\#endif\
output\_asm\_insn ("mtc1\t%1,%0\t# (subreg:DF %1) -> %0 \t"\
, xops);\
xops\[0] = gen\_rtx (REG, DFmode, REGNO (operands\[0])+1);\
\#ifndef DECSTATION\
xops\[1] = gen\_rtx (REG, SImode, REGNO (operands\[1]));\
\#else\
xops\[1] = gen\_rtx (REG, SImode, REGNO (operands\[1])+1);\
\#endif\
output\_asm\_insn ("mtc1\t%1,%0\t# (subreg:DF %1) -> %0 \t"\
, xops);\
return ("");\
}")

(define\_insn ""\
\[(set (match\_operand:DF 0 "register\_operand" "=rf")\
(subreg:DF (match\_operand:DI 1 "register\_operand" "ry") 0))]\
""\
"\*\
{\
rtx xops\[2];\
xops\[0] = gen\_rtx (REG, DFmode, REGNO (operands\[0]));\
\#ifndef DECSTATION\
xops\[1] = gen\_rtx (REG, SImode, REGNO (operands\[1])+1);\
\#else\
xops\[1] = gen\_rtx (REG, SImode, REGNO (operands\[1]));\
\#endif\
output\_asm\_insn ("mtc1\t%1,%0\t# (subreg:DF %1) -> %0 \t"\
, xops);\
xops\[0] = gen\_rtx (REG, DFmode, REGNO (operands\[0])+1);\
\#ifndef DECSTATION\
xops\[1] = gen\_rtx (REG, SImode, REGNO (operands\[1]));\
\#else\
xops\[1] = gen\_rtx (REG, SImode, REGNO (operands\[1])+1);\
\#endif\
output\_asm\_insn ("mtc1\t%1,%0\t# (subreg:DF %1) -> %0 \t"\
, xops);

return ("");\
}")\
;;\
;; ....................\
;;\
;; MOVES\
;;\
;; and\
;;\
;; LOADS AND STORES\
;;\
;; ....................

(define\_insn "movdi"\
\[(set (match\_operand:DI 0 "general\_operand" "=r,\*r,\*m")\
(match\_operand:DI 1 "general\_operand" "r,\*miF,_r"))]_\
_""_\
_"_\
{\
extern rtx change\_address ();\
extern rtx plus\_constant ();

if (which\_alternative == 0)\
{\
rtx xops\[2];\
if (REGNO (operands\[0]) != (REGNO (operands\[1]) +1))\
{\
/\* TAKE CARE OF OVERLAPS _/_\
_xops\[0] = gen\_rtx (REG, SImode, REGNO (operands\[0]));_\
_xops\[1] = gen\_rtx (REG, SImode, REGNO (operands\[1]));_\
_output\_asm\_insn ("add%:\t%0,$0,%1\t#movdi %1 -> %0", xops);_\
_xops\[0] = gen\_rtx (REG, SImode, REGNO (xops\[0])+1);_\
_xops\[1] = gen\_rtx (REG, SImode, REGNO (xops\[1])+1);_\
_output\_asm\_insn ("add%:\t%0,$0,%1\t#movdi %1 -> %0", xops);_\
_}_\
_else_\
_{_\
_/_ TAKE CARE OF OVERLAPS _/_\
_xops\[0] = gen\_rtx (REG, SImode, REGNO (operands\[0])+1);_\
_xops\[1] = gen\_rtx (REG, SImode, REGNO (operands\[1])+1);_\
_output\_asm\_insn ("add%:\t%0,$0,%1\t#movdi %1 -> %0", xops);_\
_xops\[0] = gen\_rtx (REG, SImode, REGNO (xops\[0])-1);_\
_xops\[1] = gen\_rtx (REG, SImode, REGNO (xops\[1])-1);_\
_output\_asm\_insn ("add%:\t%0,$0,%1\t#movdi %1 -> %0", xops);_\
_}_\
_return "";_\
_}_\
_else if (which\_alternative == 1)_\
_{_\
_/_ No overlap here _/_\
_rtx xops\[2];_\
_xops\[0] = gen\_rtx (REG, SImode, REGNO (operands\[0]));_\
_if (GET\_CODE (operands\[1]) == MEM)_\
_{_\
_xops\[1] = gen\_rtx (MEM, SImode, XEXP (operands\[1], 0));_\
_output\_asm\_insn ("lw\t%0,%1\t#movdi %1 (mem) -> %0", xops);_\
_}_\
_else if (CONSTANT\_P (operands\[1]))_\
_{_\
_#ifdef WORDS\_BIG\_ENDIAN_\
_xops\[1] = const0\_rtx;_\
_#else_\
_xops\[1] = operands\[1];_\
_#endif_\
_output\_load\_immediate (xops);_\
_}_\
_else if (GET\_CODE (operands\[1]) == CONST\_DOUBLE)_\
_{_\
_xops\[1] = gen\_rtx (CONST\_INT, VOIDmode,_\
_CONST\_DOUBLE\_LOW (operands\[1]));_\
_output\_load\_immediate (xops);_\
_}_\
_else_\
_abort ();_\
_xops\[0] = gen\_rtx (REG, SImode, REGNO (operands\[0])+1);_\
_if (offsettable\_memref\_p (operands\[1]))_\
_{_\
_xops\[1] = adj\_offsettable\_operand (gen\_rtx (MEM, SImode,_\
_XEXP (operands\[1], 0)),_\
_4);_\
_output\_asm\_insn ("lw\t%0,%1\t#movdi %1(mem) -> %0", xops);_\
_}_\
_else if (GET\_CODE (operands\[1]) == MEM)_\
_{_\
_abort ();_\
_output\_asm\_insn ("lw\t%0,%1\t#movdi %1(mem) -> %0", xops);_\
_}_\
_else if (CONSTANT\_P (operands\[1]))_\
_{_\
_#ifdef WORDS\_BIG\_ENDIAN_\
_xops\[1] = operands\[1];_\
_#else_\
_xops\[1] = const0\_rtx;_\
_#endif_\
_output\_load\_immediate (xops);_\
_}_\
_else if (GET\_CODE (operands\[1]) == CONST\_DOUBLE)_\
_{_\
_xops\[1] = gen\_rtx (CONST\_INT, VOIDmode,_\
_CONST\_DOUBLE\_HIGH (operands\[1]));_\
_output\_load\_immediate (xops);_\
_}_\
_else abort ();_\
_return "";_\
_}_\
_else_\
_{_\
_/_ No overlap here \*/\
rtx xops\[2];\
xops\[1] = gen\_rtx (REG, SImode, REGNO (operands\[1]));\
if (GET\_CODE (operands\[0]) == MEM)\
xops\[0] = gen\_rtx (MEM, SImode, XEXP (operands\[0], 0));\
else abort ();\
output\_asm\_insn ("sw\t%1,%0\t#movdi %1 -> %0(mem)", xops);\
xops\[1] = gen\_rtx (REG, SImode, REGNO (operands\[1])+1);\
if (offsettable\_memref\_p (operands\[0]))\
{\
xops\[0] = adj\_offsettable\_operand (gen\_rtx (MEM, SImode,\
XEXP (operands\[0], 0)),\
4\);\
output\_asm\_insn ("sw\t%1,%0\t#movdi %1 -> %0 (mem)", xops);\
}\
else if (GET\_CODE (operands\[0]) == MEM)\
{\
abort ();\
output\_asm\_insn ("sw\t%1,%0\t#movdi %1(mem) -> %0", xops);\
}\
else abort ();\
return "";\
}\
}")\
(define\_insn "movsi"\
\[(set (match\_operand:SI 0 "general\_operand" "=r,r,m,r,r,m,\*r")\
(match\_operand:SI 1 "general\_operand" "r,m,r,i,J,J,_p"))]_\
_""_\
_"_\
{\
if (which\_alternative >= 4)\
{\
if (which\_alternative == 4)\
return "add%:\t%0,$0,$0\t#movsi\t%1 == 0 -> %0";\
else if (which\_alternative == 5)\
return "sw\t$0,%0\t#movsi\t%1 == 0 -> %0";\
else if (which\_alternative == 6)\
{\
rtx xops\[4];\
register rtx addr;\
xops\[0] = operands\[0];\
addr = operands\[1];\
if ((GET\_CODE (addr) == PLUS)\
&& (GET\_CODE (XEXP (addr, 0)) == REG))\
{\
xops\[1] = XEXP (addr, 0);\
xops\[2] = XEXP (addr, 1);\
xops\[3] = addr;\
output\_asm\_insn ("addiu\t%0,%1,%2\t#movsi (\*p)\t%a3 -> %0", xops);\
return "";\
}\
else abort ();\
}\
}\
else if ((GET\_CODE (operands\[0]) == REG) && (GET\_CODE (operands\[1]) == REG))\
return "add%:\t%0,$0,%1\t#movsi\t%1 -> %0 ";\
else if (GET\_CODE (operands\[0]) == REG\
&&\
GET\_CODE (operands\[1]) == CONST\_INT)\
{\
output\_load\_immediate (operands);\
return "";\
}\
else if (GET\_CODE (operands\[0]) == REG)\
{\
if (GET\_CODE (operands\[1]) == SYMBOL\_REF\
|| GET\_CODE (operands\[1]) == LABEL\_REF)\
return "la\t%0,%a1\t#movsi %a1 -> %0";\
else if (GET\_CODE (operands\[1]) == CONST)\
return "la\t%0,%a1\t#movsi %a1(AExp) -> %0";\
else\
return "lw\t%0,%1\t#movsi %1 -> %0";\
}\
else\
return "sw\t%1,%0\t#movsi %1 -> %0";\
}")

(define\_insn ""\
\[(set (match\_operand:SI 0 "register\_operand" "=\*r")\
(match\_operand:SI 1 "address\_operand" "_p"))]_\
_"FIXED\_FRAME\_PTR\_REL\_P (operands\[1])"_\
_"_\
{\
rtx xops\[4];\
register rtx addr;\
xops\[0] = operands\[0];\
addr = operands\[1];\
if (GET\_CODE (addr) == PLUS\
&& GET\_CODE (XEXP (addr, 0)) == REG)\
{\
xops\[1] = XEXP (addr, 0);\
xops\[2] = XEXP (addr, 1);\
xops\[3] = addr;\
output\_asm\_insn ("addiu\t%0,%1,%2\t#movsi (_p)\t%a3 -> %0", xops);_\
_return "";_\
_}_\
_else_\
_abort ();_\
_}")_\
_(define\_insn "movhi"_\
_\[(set (match\_operand:HI 0 "general\_operand" "=r,r,m,r,r,m")_\
_(match\_operand:HI 1 "general\_operand" "r,m,r,i,J,J"))]_\
_""_\
_"_\
{\
if (which\_alternative == 4)\
return "add%:\t%0,$0,$0\t#movhi\t%1 == 0 -> %0";\
else if (which\_alternative == 5)\
return "sh\t$0,%0\t#movhi\t%1 == 0 -> %0";\
else if (GET\_CODE (operands\[0]) == REG && GET\_CODE (operands\[1]) == REG)\
return "add%:\t%0,$0,%1\t#move.h %1 -> %0 ";\
else if (GET\_CODE (operands\[0]) == REG\
&& GET\_CODE (operands\[1]) == CONST\_INT)\
return "addi%:\t%0,$0,%x1\t# movhi %1 -> %0";\
else if (GET\_CODE (operands\[0]) == REG)\
return "lh\t%0,%1\t#movhi %1 -> %0";\
else\
return "sh\t%1,%0\t#movhi %1 -> %0";\
}")

(define\_insn "movqi"\
\[(set (match\_operand:QI 0 "general\_operand" "=r,r,m,r,r,m")\
(match\_operand:QI 1 "general\_operand" "r,m,r,i,J,J"))]\
""\
"\*\
{\
if (which\_alternative == 4)\
return "add%:\t%0,$0,$0\t#movqi\t%1 == 0 -> %0";\
else if (which\_alternative == 5)\
return "sb\t$0,%0\t#movqi\t%1 == 0 -> %0";\
else if (GET\_CODE (operands\[0]) == REG && GET\_CODE (operands\[1]) == REG)\
return "add%:\t%0,$0,%1\t#move.b %1 -> %0 ";

if ((GET\_CODE (operands\[0]) == REG )\
&&\
(GET\_CODE (operands\[1]) == CONST\_INT))\
return "addi%:\t%0,$0,%x1\t# movqi %1 -> %0";

/\* Should say that I am not padding high order\
\*\* bits correctly_/_\
_else if (GET\_CODE (operands\[0]) == REG)_\
_return "lb\t%0,%1\t#movqi %1 -> %0";_\
_else_\
_return "sb\t%1,%0\t#movqi %1 -> %0";_\
_}")_\
_(define\_insn "movsf"_\
_\[(set (match\_operand:SF 0 "general\_operand" "=f,rf,m,f,!rf")_\
_(match\_operand:SF 1 "general\_operand" "f,m,rf,F,rf"))_\
_(clobber (reg:SI 24))]_\
_""_\
_"_\
{\
if (GET\_CODE (operands\[0]) == REG && GET\_CODE (operands\[1]) == REG)\
{\
if (REGNO (operands\[0]) >= 32)\
{\
if (REGNO (operands\[1]) >= 32)\
return "mov.s %0,%1\t#movsf %1 -> %0 ";\
return "mtc1 %1,%0";\
}\
if (REGNO (operands\[1]) >= 32)\
return "mfc1 %0,%1";\
return "add%: %0,$0,%1";\
}

if (GET\_CODE (operands\[0]) == REG\
&& GET\_CODE (operands\[1]) == CONST\_DOUBLE)\
{\
rtx xops\[3];\
xops\[0] = operands\[0];\
xops\[1] = gen\_rtx (CONST\_INT, VOIDmode,\
(XINT (operands\[1], 0))>>16);\
xops\[2] = gen\_rtx (CONST\_INT, VOIDmode,\
XINT (operands\[1], 0));\
output\_asm\_insn ("lui\t$24,%x1\t#move high part of %2", xops);\
xops\[1] = gen\_rtx (CONST\_INT, VOIDmode,\
(XINT (operands\[1], 0)) & 0xff);\
output\_asm\_insn ("ori\t$24,%x1\t#move low part of %2", xops);\
output\_asm\_insn ("mtc1\t$24,%0", xops);

```
  xops[0] = gen_rtx (REG, SFmode, REGNO (xops[0])+1);
  xops[1] = gen_rtx (CONST_INT, VOIDmode,
		 (XINT (operands[1], 1))>>16);
  xops[2] = gen_rtx (CONST_INT, VOIDmode,
		 XINT (operands[1], 1));
  output_asm_insn (\"lui\\t$24,%x1\\t#move  high part of %2\", xops);
  xops[1] = gen_rtx (CONST_INT, VOIDmode,
		 (XINT (operands[1], 1)) & 0xff);
  output_asm_insn (\"ori\\t$24,%x1\\t#move low part of %2\", xops);

  output_asm_insn (\"mtc1\\t$24,%0\", xops);
  return \"cvt.s.d\\t %0,%0 \";
}
```

/\* Should say that I am not padding high order\
\*\* bits correctly\
\*/\
else if (GET\_CODE (operands\[0]) == REG)\
{\
if (REGNO (operands\[0]) < 32)\
return "lw\t %0,%1\t#movsf %1 -> %0";\
return "l.s\t %0,%1\t#movsf %1 -> %0";\
}\
else\
{\
if (REGNO (operands\[1]) < 32)\
return "sw\t %1,%0\t#movsf %1 -> %0";\
return "s.s\t %1,%0\t#movsf %1 -> %0";\
}\
}")

;; ---

(define\_insn "movdf"\
\[(set (match\_operand:DF 0 "general\_operand" "=f,f,m,f,\*f,\*y")\
(match\_operand:DF 1 "general\_operand" "f,m,f,F,\*y,_f"))_\
_(clobber (reg:SI 24))]_\
_""_\
_"_\
{\
if (which\_alternative > 3)\
{\
rtx xops\[2];

```
  if (REGNO (operands[1]) == 6)
{
  xops[0] =  gen_rtx (REG, DFmode, REGNO (operands[0]));
```

\#ifndef DECSTATION\
xops\[1] = gen\_rtx (REG, SImode, REGNO (operands\[1])+1);\
\#else\
xops\[1] = gen\_rtx (REG, SImode, REGNO (operands\[1]));\
\#endif\
output\_asm\_insn ("mtc1\t%1,%0\t# %1 -> %0 \tcalling sequence trick"\
, xops);\
xops\[0] = gen\_rtx (REG, DFmode, REGNO (operands\[0])+1);\
\#ifndef DECSTATION\
xops\[1] = gen\_rtx (REG, SImode, REGNO (operands\[1]));\
\#else\
xops\[1] = gen\_rtx (REG, SImode, REGNO (operands\[1])+1);\
\#endif\
output\_asm\_insn ("mtc1\t%1,%0\t# %1 -> %0 \tcalling sequence trick"\
, xops);\
return ("");\
}\
else if (REGNO (operands\[0]) == 6)\
{\
xops\[0] = gen\_rtx (REG, SImode, REGNO (operands\[0]));\
\#ifndef DECSTATION\
xops\[1] = gen\_rtx (REG, DFmode, REGNO (operands\[1])+1);\
\#else\
xops\[1] = gen\_rtx (REG, SImode, REGNO (operands\[1]));\
\#endif\
output\_asm\_insn ("mfc1\t%0,%1\t# %1 -> %0 \tcalling sequence trick"\
, xops);\
xops\[0] = gen\_rtx (REG, SImode, REGNO (operands\[0])+1);\
\#ifndef DECSTATION\
xops\[1] = gen\_rtx (REG, DFmode, REGNO (operands\[1]));\
\#else\
xops\[1] = gen\_rtx (REG, SImode, REGNO (operands\[1])+1);\
\#endif\
output\_asm\_insn ("mfc1\t%0,%1\t# %1 -> %0 \tcalling sequence trick"\
, xops);\
return ("");\
}\
else\
abort\_with\_insn (insn,\
"Matched \*y constraint and register number wrong");\
}\
else\
{\
if (GET\_CODE (operands\[0]) == REG && GET\_CODE (operands\[1]) == REG)\
return "mov.d\t%0,%1\t#movdf %1 -> %0 ";

```
  if (GET_CODE (operands[0]) == REG
  && GET_CODE (operands[1]) == CONST_DOUBLE)
{
  rtx xops[3];
  xops[0] = operands[0];
  xops[1] = gen_rtx (CONST_INT, VOIDmode,
		     (XINT (operands[1], 0))>>16);
  xops[2] = gen_rtx (CONST_INT, VOIDmode,
		     XINT (operands[1], 0));
  output_asm_insn (\"lui\\t$24,%x1\\t#move  high part of %2\", xops);
  xops[1] = gen_rtx (CONST_INT, VOIDmode,
		     (XINT (operands[1], 0)) & 0xff);
  output_asm_insn (\"ori\\t$24,%x1\\t#move low part of %2\", xops);
  output_asm_insn (\"mtc1\\t%0,$24\", xops);

  xops[0] = gen_rtx (REG, SFmode, REGNO (xops[0])+1);
  xops[1] = gen_rtx (CONST_INT, VOIDmode,
		     (XINT (operands[1], 1))>>16);
  xops[2] = gen_rtx (CONST_INT, VOIDmode,
		     XINT (operands[1], 1));
  output_asm_insn (\"lui\\t$24,%x1\\t#move  high part of %2\", xops);
  xops[1] = gen_rtx (CONST_INT, VOIDmode,
		     (XINT (operands[1], 1)) & 0xff);
  output_asm_insn (\"ori\\t$24,%x1\\t#move low part of %2\", xops);

  output_asm_insn (\"mtc1\\t%0,$24\", xops);
  return \"# \";

}
  else if (GET_CODE (operands[0]) == REG)
return \"l.d\\t %0,%1\\t#movdf %1 -> %0\";
  else
return \"s.d\\t %1,%0\\t#movdf %1 -> %0\";
}
```

}")

(define\_insn ""\
\[(set (match\_operand:SF 0 "general\_operand" "=f,f,m")\
(match\_operand:SF 1 "general\_operand" "f,m,f"))]\
""\
"\*\
{\
if (GET\_CODE (operands\[0]) == REG && GET\_CODE (operands\[1]) == REG)\
return "mov.s %0,%1\t#movsf %1 -> %0 ";

if (GET\_CODE (operands\[0]) == REG)\
return "l.s\t %0,%1\t#movsf %1 -> %0";\
else\
return "s.s\t %1,%0\t#movsf %1 -> %0";\
}")

;; ---

(define\_insn ""\
\[(set (match\_operand:DF 0 "general\_operand" "=f,f,m,\*f,\*y")\
(match\_operand:DF 1 "general\_operand" "f,m,f,\*y,_f"))]_\
_""_\
_"_\
{\
if (which\_alternative > 2)\
{\
rtx xops\[2];\
if (REGNO (operands\[1]) == 6)\
{\
xops\[0] = gen\_rtx (REG, DFmode, REGNO (operands\[0]));\
\#ifndef DECSTATION\
xops\[1] = gen\_rtx (REG, SImode, REGNO (operands\[1])+1);\
\#else\
xops\[1] = gen\_rtx (REG, SImode, REGNO (operands\[1]));\
\#endif\
output\_asm\_insn ("mtc1\t%1,%0\t# %1 -> %0 \tcalling sequence trick"\
, xops);\
xops\[0] = gen\_rtx (REG, DFmode, REGNO (operands\[0])+1);\
\#ifndef DECSTATION\
xops\[1] = gen\_rtx (REG, SImode, REGNO (operands\[1]));\
\#else\
xops\[1] = gen\_rtx (REG, SImode, REGNO (operands\[1])+1);\
\#endif\
output\_asm\_insn ("mtc1\t%1,%0\t# %1 -> %0 \tcalling sequence trick"\
, xops);\
return ("");\
}\
else if (REGNO (operands\[0]) == 6)\
{\
xops\[0] = gen\_rtx (REG, SImode, REGNO (operands\[0]));\
\#ifndef DECSTATION\
xops\[1] = gen\_rtx (REG, DFmode, REGNO (operands\[1])+1);\
\#else\
xops\[1] = gen\_rtx (REG, SImode, REGNO (operands\[1]));\
\#endif\
output\_asm\_insn ("mfc1\t%0,%1\t# %1 -> %0 \tcalling sequence trick"\
, xops);\
xops\[0] = gen\_rtx (REG, SImode, REGNO (operands\[0])+1);\
\#ifndef DECSTATION\
xops\[1] = gen\_rtx (REG, DFmode, REGNO (operands\[1]));\
\#else\
xops\[1] = gen\_rtx (REG, SImode, REGNO (operands\[1])+1);\
\#endif\
output\_asm\_insn ("mfc1\t%0,%1\t# %1 -> %0 \tcalling sequence trick"\
, xops);\
return ("");\
}\
else\
abort\_with\_insn (insn,\
"Matched \*y constraint and register number wrong");\
}\
else\
{\
if ((GET\_CODE (operands\[0]) == REG) && (GET\_CODE (operands\[1]) == REG))\
return "mov.d\t%0,%1\t#movdf %1 -> %0 ";

```
  if (GET_CODE (operands[0]) == REG)
return \"l.d\\t %0,%1\\t#movdf %1 -> %0\";
  else
return \"s.d\\t %1,%0\\t#movdf %1 -> %0\";
}
```

}")\
(define\_insn ""\
\[(set (match\_operand:SI 0 "general\_operand" "=r")\
(match\_operand:QI 1 "general\_operand" "r"))]\
""\
"andi\t%0,%1,0xff\t#Wild zero\_extendqisi2\t %1 -> %0")

\
;;\
;; ....................\
;;\
;; OTHER ARITHMETIC AND SHIFT\
;;\
;; ....................

(define\_insn "ashlqi3"\
\[(set (match\_operand:QI 0 "general\_operand" "=r")\
(ashift:QI (match\_operand:QI 1 "general\_operand" "r")\
(match\_operand:SI 2 "arith32\_operand" "ri")))]\
""\
"\*\
{\
if (GET\_CODE (operands\[2]) == CONST\_INT)\
{\
operands\[2] = gen\_rtx (CONST\_INT, VOIDmode, (XINT (operands\[2], 0))& 0x1f);\
output\_asm\_insn ("sll\t%0,%1,%2\t#ashlqi3\t (%1<<%2) -> %0", operands);\
}\
else\
output\_asm\_insn ("sll\t%0,%1,%2\t#ashlqi3\t (%1<<%2) -> %0 (asm syntax)",\
operands);\
return "andi\t%0,%0,0xff\t#ashlqi3";\
}")

(define\_insn "ashlhi3"\
\[(set (match\_operand:HI 0 "general\_operand" "=r")\
(ashift:HI (match\_operand:HI 1 "general\_operand" "r")\
(match\_operand:SI 2 "arith32\_operand" "ri")))]\
""\
"\*\
{\
if (GET\_CODE (operands\[2]) == CONST\_INT)\
{\
operands\[2] = gen\_rtx (CONST\_INT, VOIDmode, (XINT (operands\[2], 0))& 0x1f);\
output\_asm\_insn ("sll\t%0,%1,%2\t#ashlhi3\t (%1<<%2) -> %0",\
operands);\
}\
else\
output\_asm\_insn ("sll\t%0,%1,%2\t#ashlhi3\t (%1<<%2) -> %0 (asm syntax)",\
operands);\
return "sll\t%0,%0,0x10;sra\t%0,%0,0x10\t#ashlhi3";\
}")

(define\_insn "ashlsi3"\
\[(set (match\_operand:SI 0 "general\_operand" "=r")\
(ashift:SI (match\_operand:SI 1 "general\_operand" "r")\
(match\_operand:SI 2 "arith32\_operand" "ri")))]\
""\
"\*\
{\
if (GET\_CODE (operands\[2]) == CONST\_INT)\
{\
operands\[2] = gen\_rtx (CONST\_INT, VOIDmode, (XINT (operands\[2], 0))& 0x1f);\
return "sll\t%0,%1,%2\t#ashlsi3\t (%1<<%2) -> %0";\
}\
return "sll\t%0,%1,%2\t#ashlsi3\t (%1<<%2) -> %0 (asm syntax)";\
}")

(define\_insn "ashrsi3"\
\[(set (match\_operand:SI 0 "general\_operand" "=r")\
(ashiftrt:SI (match\_operand:SI 1 "general\_operand" "r")\
(match\_operand:SI 2 "arith32\_operand" "ri")))]\
""\
"\*\
{\
if (GET\_CODE (operands\[2]) == CONST\_INT)\
{\
operands\[2] = gen\_rtx (CONST\_INT, VOIDmode, (XINT (operands\[2], 0))& 0x1f);\
return "sra\t%0,%1,%2\t#ashrsi3\t (%1>>%2) -> %0";\
}\
return "sra\t%0,%1,%2\t#ashrsi3\t (%1>>%2) -> %0 (asm syntax for srav)";\
}")

(define\_insn "lshrsi3"\
\[(set (match\_operand:SI 0 "general\_operand" "=r")\
(lshiftrt:SI (match\_operand:SI 1 "general\_operand" "r")\
(match\_operand:SI 2 "arith32\_operand" "rn")))]\
""\
"\*\
{\
if (GET\_CODE (operands\[2]) == CONST\_INT)\
{\
operands\[2] = gen\_rtx (CONST\_INT, VOIDmode, (XINT (operands\[2], 0))& 0x1f);\
return "srl\t%0,%1,%2\t#lshrsi3\t (%1>>%2) -> %0";\
}\
return "srl\t%0,%1,%2\t#lshrsi3\t (%1>>%2) -> %0 (asm syntax)";\
}")

(define\_insn "negsi2"\
\[(set (match\_operand:SI 0 "general\_operand" "=r")\
(neg:SI (match\_operand:SI 1 "general\_operand" "r")))]\
""\
"sub%:\t%0,$0,%1\t#negsi2")

(define\_insn "negdf2"\
\[(set (match\_operand:DF 0 "general\_operand" "=f")\
(neg:DF (match\_operand:DF 1 "general\_operand" "f")))]\
""\
"neg.d\t%0,%1\t#negdf2")

(define\_insn "negsf2"

\[(set (match\_operand:SF 0 "general\_operand" "=f")\
(neg:SF (match\_operand:SF 1 "general\_operand" "f")))]\
""\
"neg.s\t%0,%1\t#negsf2")

(define\_insn "one\_cmplsi2"\
\[(set (match\_operand:SI 0 "general\_operand" "=r")\
(not:SI (match\_operand:SI 1 "general\_operand" "r")))]\
""\
"nor\t%0,$0,%1\t#one\_cmplsi2")

(define\_insn "one\_cmplhi2"\
\[(set (match\_operand:HI 0 "general\_operand" "=r")\
(not:HI (match\_operand:HI 1 "general\_operand" "r")))]\
""\
"nor\t%0,$0,%1\t#one\_cmplhi2")

(define\_insn "one\_cmplqi2"\
\[(set (match\_operand:QI 0 "general\_operand" "=r")\
(not:QI (match\_operand:QI 1 "general\_operand" "r")))]\
""\
"nor\t%0,$0,%1\t#one\_cmplqi2")\
;;\
;; ....................\
;;\
;; BRANCHES\
;;\
;; ....................

```
				;;- MERGED CMPSI + BEQ
```

(define\_peephole\
\[(set (cc0)\
(compare (match\_operand:SI 0 "general\_operand" "r")\
(match\_operand:SI 1 "general\_operand" "r")))\
(set (pc)\
(if\_then\_else (eq (cc0)\
(const\_int 0))\
(label\_ref (match\_operand 2 "" ""))\
(pc)))]\
""\
"beq \t%0,%1,%l2\t#beq MIPS primitive insn")

```
				;;- MERGED CMPSI + INV BEQ
```

(define\_peephole\
\[(set (cc0)\
(compare (match\_operand:SI 0 "general\_operand" "r")\
(match\_operand:SI 1 "general\_operand" "r")))\
(set (pc)\
(if\_then\_else (ne (cc0)\
(const\_int 0))\
(pc)\
(label\_ref (match\_operand 2 "" ""))))]\
""\
"beq \t%0,%1,%l2\t#beq inverted primitive insn")\
;;- MERGED CMPSI + BNE\
(define\_peephole\
\[(set (cc0)\
(compare (match\_operand:SI 0 "general\_operand" "r")\
(match\_operand:SI 1 "general\_operand" "r")))\
(set (pc)\
(if\_then\_else (ne (cc0)\
(const\_int 0))\
(label\_ref (match\_operand 2 "" ""))\
(pc)))]\
""\
"bne \t%0,%1,%l2\t#bne MIPS primitive insn")

```
				;;- MERGED CMPSI + INV BNE
```

(define\_peephole\
\[(set (cc0)\
(compare (match\_operand:SI 0 "general\_operand" "r")\
(match\_operand:SI 1 "general\_operand" "r")))\
(set (pc)\
(if\_then\_else (eq (cc0)\
(const\_int 0))\
(pc)\
(label\_ref (match\_operand 2 "" ""))))]\
""\
"bne \t%0,%1,%l2\t#bne inverted primitive insn")

```
				;;- MERGED CMPSI + BGT
```

(define\_peephole\
\[(set (cc0)\
(compare (match\_operand:SI 0 "general\_operand" "r")\
(match\_operand:SI 1 "general\_operand" "r")))\
(set (pc)\
(if\_then\_else (gt (cc0)\
(const\_int 0))\
(label\_ref (match\_operand 2 "" ""))\
(pc)))]\
""\
"bgt \t%0,%1,%l2\t#bgt MIPS composite insn")

```
				;;- MERGED CMPSI + INV BGT
```

(define\_peephole\
\[(set (cc0)\
(compare (match\_operand:SI 0 "general\_operand" "r")\
(match\_operand:SI 1 "general\_operand" "r")))\
(set (pc)\
(if\_then\_else (le (cc0)\
(const\_int 0))\
(pc)\
(label\_ref (match\_operand 2 "" ""))))]\
""\
"bgt \t%0,%1,%l2\t#bgt inverted composite insn")\
;;- MERGED CMPSI + BLT\
(define\_peephole\
\[(set (cc0)\
(compare (match\_operand:SI 0 "general\_operand" "r")\
(match\_operand:SI 1 "general\_operand" "r")))\
(set (pc)\
(if\_then\_else (lt (cc0)\
(const\_int 0))\
(label\_ref (match\_operand 2 "" ""))\
(pc)))]\
""\
"blt \t%0,%1,%l2\t#blt MIPS composite insn")

```
				;;- MERGED CMPSI + INV BLT
```

(define\_peephole\
\[(set (cc0)\
(compare (match\_operand:SI 0 "general\_operand" "r")\
(match\_operand:SI 1 "general\_operand" "r")))\
(set (pc)\
(if\_then\_else (ge (cc0)\
(const\_int 0))\
(pc)\
(label\_ref (match\_operand 2 "" ""))))]\
""\
"blt \t%0,%1,%l2\t#blt inverted composite insn")\
;;- MERGED CMPSI + BGE\
(define\_peephole\
\[(set (cc0)\
(compare (match\_operand:SI 0 "general\_operand" "r")\
(match\_operand:SI 1 "general\_operand" "r")))\
(set (pc)\
(if\_then\_else (ge (cc0)\
(const\_int 0))\
(label\_ref (match\_operand 2 "" ""))\
(pc)))]\
""\
"bge \t%0,%1,%l2\t#bge composite insn")

```
				;;- MERGED CMPSI + INV BGE
```

(define\_peephole\
\[(set (cc0)\
(compare (match\_operand:SI 0 "general\_operand" "r")\
(match\_operand:SI 1 "general\_operand" "r")))\
(set (pc)\
(if\_then\_else (lt (cc0)\
(const\_int 0))\
(pc)\
(label\_ref (match\_operand 2 "" ""))))]\
""\
"bge \t%0,%1,%l2\t#bge inverted composite insn")\
;;- MERGED CMPSI + BLE\
(define\_peephole\
\[(set (cc0)\
(compare (match\_operand:SI 0 "general\_operand" "r")\
(match\_operand:SI 1 "general\_operand" "r")))\
(set (pc)\
(if\_then\_else (le (cc0)\
(const\_int 0))\
(label\_ref (match\_operand 2 "" ""))\
(pc)))]\
""\
"ble \t%0,%1,%l2\t#ble composite insn")

```
				;;- MERGED CMPSI + INV BLE
```

(define\_peephole\
\[(set (cc0)\
(compare (match\_operand:SI 0 "general\_operand" "r")\
(match\_operand:SI 1 "general\_operand" "r")))\
(set (pc)\
(if\_then\_else (gt (cc0)\
(const\_int 0))\
(pc)\
(label\_ref (match\_operand 2 "" ""))))]\
""\
"ble \t%0,%1,%l2\t#ble inverted composite insn")

\
;;- MERGED CMPSI + BGT\
(define\_peephole\
\[(set (cc0)\
(compare (match\_operand:SI 0 "general\_operand" "r")\
(match\_operand:SI 1 "general\_operand" "r")))\
(set (pc)\
(if\_then\_else (gtu (cc0)\
(const\_int 0))\
(label\_ref (match\_operand 2 "" ""))\
(pc)))]\
""\
"bgtu \t%0,%1,%l2\t#bgtu MIPS composite insn")

```
				;;- MERGED CMPSI + INV BGT
```

(define\_peephole\
\[(set (cc0)\
(compare (match\_operand:SI 0 "general\_operand" "r")\
(match\_operand:SI 1 "general\_operand" "r")))\
(set (pc)\
(if\_then\_else (leu (cc0)\
(const\_int 0))\
(pc)\
(label\_ref (match\_operand 2 "" ""))))]\
""\
"bgtu \t%0,%1,%l2\t#bgtu inverted composite insn")\
;;- MERGED CMPSI + INV BLTU\
(define\_peephole\
\[(set (cc0)\
(compare (match\_operand:SI 0 "general\_operand" "r")\
(match\_operand:SI 1 "general\_operand" "r")))\
(set (pc)\
(if\_then\_else (geu (cc0)\
(const\_int 0))\
(pc)\
(label\_ref (match\_operand 2 "" ""))))]\
""\
"bltu \t%0,%1,%l2\t#blt inverted composite insn")\
;;- MERGED CMPSI + BGEU\
(define\_peephole\
\[(set (cc0)\
(compare (match\_operand:SI 0 "general\_operand" "r")\
(match\_operand:SI 1 "general\_operand" "r")))\
(set (pc)\
(if\_then\_else (geu (cc0)\
(const\_int 0))\
(label\_ref (match\_operand 2 "" ""))\
(pc)))]\
""\
"bgeu \t%0,%1,%l2\t#bgeu composite insn")

```
				;;- MERGED CMPSI + INV BGEU
```

(define\_peephole\
\[(set (cc0)\
(compare (match\_operand:SI 0 "general\_operand" "r")\
(match\_operand:SI 1 "general\_operand" "r")))\
(set (pc)\
(if\_then\_else (ltu (cc0)\
(const\_int 0))\
(pc)\
(label\_ref (match\_operand 2 "" ""))))]\
""\
"bgeu \t%0,%1,%l2\t#bgeu inverted composite insn")\
;;- MERGED CMPSI + BLE\
(define\_peephole\
\[(set (cc0)\
(compare (match\_operand:SI 0 "general\_operand" "r")\
(match\_operand:SI 1 "general\_operand" "r")))\
(set (pc)\
(if\_then\_else (leu (cc0)\
(const\_int 0))\
(label\_ref (match\_operand 2 "" ""))\
(pc)))]\
""\
"bleu \t%0,%1,%l2\t#bleu composite insn")

```
				;;- MERGED CMPSI + INV BLE
```

(define\_peephole\
\[(set (cc0)\
(compare (match\_operand:SI 0 "general\_operand" "r")\
(match\_operand:SI 1 "general\_operand" "r")))\
(set (pc)\
(if\_then\_else (gtu (cc0)\
(const\_int 0))\
(pc)\
(label\_ref (match\_operand 2 "" ""))))]\
""\
"bleu \t%0,%1,%l2\t#bleu inverted composite insn")\
;;- MERGED CMPSI + BLTU\
(define\_peephole\
\[(set (cc0)\
(compare (match\_operand:SI 0 "general\_operand" "r")\
(match\_operand:SI 1 "general\_operand" "r")))\
(set (pc)\
(if\_then\_else (ltu (cc0)\
(const\_int 0))\
(label\_ref (match\_operand 2 "" ""))\
(pc)))]\
""\
"bltu \t%0,%1,%l2\t#bltu MIPS composite insn")

```
				;;- MERGED CMPSI + INV BLTU
```

(define\_peephole\
\[(set (cc0)\
(compare (match\_operand:SI 0 "general\_operand" "r")\
(match\_operand:SI 1 "general\_operand" "r")))\
(set (pc)\
(if\_then\_else (geu (cc0)\
(const\_int 0))\
(pc)\
(label\_ref (match\_operand 2 "" ""))))]\
""\
"bltu \t%0,%1,%l2\t#bltu inverted composite insn")\
;;- FLOATING POINT CASES\
;;- --------------------

```
				;;- MERGED CMPSF + BEQ
```

(define\_peephole\
\[(set (cc0)\
(compare (match\_operand:SF 0 "general\_operand" "f")\
(match\_operand:SF 1 "general\_operand" "f")))\
(set (pc)\
(if\_then\_else (eq (cc0)\
(const\_int 0))\
(label\_ref (match\_operand 2 "" ""))\
(pc)))]\
""\
"c.eq.s\t%0,%1\t# Merged CMPSF + BEQ ;bc1t\t%l2\t# Merged CMPSF + BEQ ")

```
				;;- MERGED CMPSF + INV BEQ
```

(define\_peephole\
\[(set (cc0)\
(compare (match\_operand:SF 0 "general\_operand" "f")\
(match\_operand:SF 1 "general\_operand" "f")))\
(set (pc)\
(if\_then\_else (ne (cc0)\
(const\_int 0))\
(pc)\
(label\_ref (match\_operand 2 "" ""))))]\
""\
"c.eq.s\t%0,%1\t# Merged CMPSF + BEQ ;bc1t\t%l2\t# Merged CMPSF + BEQ ")

```
				;;- MERGED CMPSF + BNE
```

(define\_peephole\
\[(set (cc0)\
(compare (match\_operand:SF 0 "general\_operand" "f")\
(match\_operand:SF 1 "general\_operand" "f")))\
(set (pc)\
(if\_then\_else (ne (cc0)\
(const\_int 0))\
(label\_ref (match\_operand 2 "" ""))\
(pc)))]\
""\
"c.eq.s\t%0,%1\t# Merged CMPSF + BNE ;bc1f\t%l2\t# Merged CMPSF + BNE ")

```
				;;- MERGED CMPSF + INV BNE
```

(define\_peephole\
\[(set (cc0)\
(compare (match\_operand:SF 0 "general\_operand" "f")\
(match\_operand:SF 1 "general\_operand" "f")))\
(set (pc)\
(if\_then\_else (eq (cc0)\
(const\_int 0))\
(pc)\
(label\_ref (match\_operand 2 "" ""))))]\
""\
"c.eq.s\t%0,%1\t# Merged CMPSF + I.BNE ;bc1f\t%l2\t# Merged CMPSF +I.BNE ")

```
				;;- MERGED CMPSF + BGT
```

(define\_peephole\
\[(set (cc0)\
(compare (match\_operand:SF 0 "general\_operand" "f")\
(match\_operand:SF 1 "general\_operand" "f")))\
(set (pc)\
(if\_then\_else (gt (cc0)\
(const\_int 0))\
(label\_ref (match\_operand 2 "" ""))\
(pc)))]\
""\
"c.le.s\t%0,%1\t# Merged CMPSF + BGT ;bc1f\t%l2\t# Merged CMPSF + BGT ")

```
				;;- MERGED CMPSF + INV BGT
```

(define\_peephole\
\[(set (cc0)\
(compare (match\_operand:SF 0 "general\_operand" "f")\
(match\_operand:SF 1 "general\_operand" "f")))\
(set (pc)\
(if\_then\_else (le (cc0)\
(const\_int 0))\
(pc)\
(label\_ref (match\_operand 2 "" ""))))]\
""\
"c.le.s\t%0,%1\t# Merged CMPSF + I.BGT ;bc1f\t%l2\t# Merged CMPSF +I. BGT ")

```
				;;- MERGED CMPSF + BLT
```

(define\_peephole\
\[(set (cc0)\
(compare (match\_operand:SF 0 "general\_operand" "f")\
(match\_operand:SF 1 "general\_operand" "f")))\
(set (pc)\
(if\_then\_else (lt (cc0)\
(const\_int 0))\
(label\_ref (match\_operand 2 "" ""))\
(pc)))]\
""\
"c.lt.s\t%0,%1\t# Merged CMPSF + BLT ;bc1t\t%l2\t# Merged CMPSF + BLT ")

```
				;;- MERGED CMPSF + INV BLT
```

(define\_peephole\
\[(set (cc0)\
(compare (match\_operand:SF 0 "general\_operand" "f")\
(match\_operand:SF 1 "general\_operand" "f")))\
(set (pc)\
(if\_then\_else (ge (cc0)\
(const\_int 0))\
(pc)\
(label\_ref (match\_operand 2 "" ""))))]\
""\
"c.lt.s\t%0,%1\t# Merged CMPSF + I.BLT ;bc1t\t%l2\t# Merged CMPSF + I.BLT ")

```
				;;- MERGED CMPSF + BGE
```

(define\_peephole\
\[(set (cc0)\
(compare (match\_operand:SF 0 "general\_operand" "f")\
(match\_operand:SF 1 "general\_operand" "f")))\
(set (pc)\
(if\_then\_else (ge (cc0)\
(const\_int 0))\
(label\_ref (match\_operand 2 "" ""))\
(pc)))]\
""\
"c.lt.s\t%0,%1\t# Merged CMPSF + BGE ;bc1f\t%l2\t# Merged CMPSF + BGE")

```
				;;- MERGED CMPSF + INV BGE
```

(define\_peephole\
\[(set (cc0)\
(compare (match\_operand:SF 0 "general\_operand" "f")\
(match\_operand:SF 1 "general\_operand" "f")))\
(set (pc)\
(if\_then\_else (lt (cc0)\
(const\_int 0))\
(pc)\
(label\_ref (match\_operand 2 "" ""))))]\
""\
"c.lt.s\t%0,%1\t# Merged CMPSF +INV BGE ;bc1f\t%l2\t# Merged CMPSF +INV BGE ")\
;;- MERGED CMPSF + BLE\
(define\_peephole\
\[(set (cc0)\
(compare (match\_operand:SF 0 "general\_operand" "f")\
(match\_operand:SF 1 "general\_operand" "f")))\
(set (pc)\
(if\_then\_else (le (cc0)\
(const\_int 0))\
(label\_ref (match\_operand 2 "" ""))\
(pc)))]\
""\
"c.le.s\t%0,%1\t# Merged CMPSF + BLE ;bc1t\t%l2\t# Merged CMPSF + BLE ")

```
				;;- MERGED CMPSF + INV BLE
```

(define\_peephole\
\[(set (cc0)\
(compare (match\_operand:SF 0 "general\_operand" "f")\
(match\_operand:SF 1 "general\_operand" "f")))\
(set (pc)\
(if\_then\_else (gt (cc0)\
(const\_int 0))\
(pc)\
(label\_ref (match\_operand 2 "" ""))))]\
""\
"c.le.s\t%0,%1\t# Merged CMPSF + INV BLE ;bc1t\t%l2\t# Merged CMPSF +I. BLE")

```
				;;- DOUBLE FLOATING POINT CASES
				;;- --------------------

				;;- MERGED CMPDF + BEQ
```

(define\_peephole\
\[(set (cc0)\
(compare (match\_operand:DF 0 "general\_operand" "f")\
(match\_operand:DF 1 "general\_operand" "f")))\
(set (pc)\
(if\_then\_else (eq (cc0)\
(const\_int 0))\
(label\_ref (match\_operand 2 "" ""))\
(pc)))]\
""\
"c.eq.d\t%0,%1\t# Merged CMPDF + BEQ ;bc1t\t%l2\t# Merged CMPDF + BEQ ")

```
				;;- MERGED CMPDF + INV BEQ
```

(define\_peephole\
\[(set (cc0)\
(compare (match\_operand:DF 0 "general\_operand" "f")\
(match\_operand:DF 1 "general\_operand" "f")))\
(set (pc)\
(if\_then\_else (ne (cc0)\
(const\_int 0))\
(pc)\
(label\_ref (match\_operand 2 "" ""))))]\
""\
"c.eq.d\t%0,%1\t# Merged CMPDF + I.BEQ ;bc1t\t%l2\t# Merged CMPDF +I. BEQ ")

```
				;;- MERGED CMPDF + BNE
```

(define\_peephole\
\[(set (cc0)\
(compare (match\_operand:DF 0 "general\_operand" "f")\
(match\_operand:DF 1 "general\_operand" "f")))\
(set (pc)\
(if\_then\_else (ne (cc0)\
(const\_int 0))\
(label\_ref (match\_operand 2 "" ""))\
(pc)))]\
""\
"c.eq.d\t%0,%1\t# Merged CMPDF + BNE ;bc1f\t%l2\t# Merged CMPDF + BNE ")

```
				;;- MERGED CMPDF + INV BNE
```

(define\_peephole\
\[(set (cc0)\
(compare (match\_operand:DF 0 "general\_operand" "f")\
(match\_operand:DF 1 "general\_operand" "f")))\
(set (pc)\
(if\_then\_else (eq (cc0)\
(const\_int 0))\
(pc)\
(label\_ref (match\_operand 2 "" ""))))]\
""\
"c.eq.d\t%0,%1\t# Merged CMPDF +I. BNE ;bc1f\t%l2\t# Merged CMPDF +I BNE")

```
				;;- MERGED CMPDF + BGT
```

(define\_peephole\
\[(set (cc0)\
(compare (match\_operand:DF 0 "general\_operand" "f")\
(match\_operand:DF 1 "general\_operand" "f")))\
(set (pc)\
(if\_then\_else (gt (cc0)\
(const\_int 0))\
(label\_ref (match\_operand 2 "" ""))\
(pc)))]\
""\
"c.le.d\t%0,%1\t# Merged CMPDF + BGT ;bc1f\t%l2\t# Merged CMPDF + BGT ")

```
				;;- MERGED CMPDF + INV BGT
```

(define\_peephole\
\[(set (cc0)\
(compare (match\_operand:DF 0 "general\_operand" "f")\
(match\_operand:DF 1 "general\_operand" "f")))\
(set (pc)\
(if\_then\_else (le (cc0)\
(const\_int 0))\
(pc)\
(label\_ref (match\_operand 2 "" ""))))]\
""\
"c.le.d\t%0,%1\t# Merged CMPDF + I. BGT ;bc1f\t%l2\t# Merged CMPDF + I. BGT ")

```
				;;- MERGED CMPDF + BLT
```

(define\_peephole\
\[(set (cc0)\
(compare (match\_operand:DF 0 "general\_operand" "f")\
(match\_operand:DF 1 "general\_operand" "f")))\
(set (pc)\
(if\_then\_else (lt (cc0)\
(const\_int 0))\
(label\_ref (match\_operand 2 "" ""))\
(pc)))]\
""\
"c.lt.d\t%0,%1\t# Merged CMPDF + BLT ;bc1t\t%l2\t# Merged CMPDF + BLT ")

```
				;;- MERGED CMPDF + INV BLT
```

(define\_peephole\
\[(set (cc0)\
(compare (match\_operand:DF 0 "general\_operand" "f")\
(match\_operand:DF 1 "general\_operand" "f")))\
(set (pc)\
(if\_then\_else (ge (cc0)\
(const\_int 0))\
(pc)\
(label\_ref (match\_operand 2 "" ""))))]\
""\
"c.lt.d\t%0,%1\t# Merged CMPDF + I.BLT ;bc1t\t%l2\t# Merged CMPDF + I.BLT")

```
				;;- MERGED CMPDF + BGE
```

(define\_peephole\
\[(set (cc0)\
(compare (match\_operand:DF 0 "general\_operand" "f")\
(match\_operand:DF 1 "general\_operand" "f")))\
(set (pc)\
(if\_then\_else (ge (cc0)\
(const\_int 0))\
(label\_ref (match\_operand 2 "" ""))\
(pc)))]\
""\
"c.lt.d\t%0,%1\t# Merged CMPDF + BGE ;bc1f\t%l2\t# Merged CMPDF + BGE")

```
				;;- MERGED CMPDF + INV BGE
```

(define\_peephole\
\[(set (cc0)\
(compare (match\_operand:DF 0 "general\_operand" "f")\
(match\_operand:DF 1 "general\_operand" "f")))\
(set (pc)\
(if\_then\_else (lt (cc0)\
(const\_int 0))\
(pc)\
(label\_ref (match\_operand 2 "" ""))))]\
""\
"c.lt.d\t%0,%1\t# Merged CMPDF +I. BGE ;bc1f\t%l2\t# Merged CMPDF +I. BGE")\
;;- MERGED CMPDF + BLE\
(define\_peephole\
\[(set (cc0)\
(compare (match\_operand:DF 0 "general\_operand" "f")\
(match\_operand:DF 1 "general\_operand" "f")))\
(set (pc)\
(if\_then\_else (le (cc0)\
(const\_int 0))\
(label\_ref (match\_operand 2 "" ""))\
(pc)))]\
""\
"c.le.d\t%0,%1\t# Merged CMPDF + BLE ;bc1t\t%l2\t# Merged CMPDF + BLE ")

```
				;;- MERGED CMPDF + INV BLE
```

(define\_peephole\
\[(set (cc0)\
(compare (match\_operand:DF 0 "general\_operand" "f")\
(match\_operand:DF 1 "general\_operand" "f")))\
(set (pc)\
(if\_then\_else (gt (cc0)\
(const\_int 0))\
(pc)\
(label\_ref (match\_operand 2 "" ""))))]\
""\
"c.le.d\t%0,%1\t# Merged CMPDF +I. BLE ;bc1t\t%l2\t# Merged CMPDF + I.BLE ")

\
;;\
;; ....................\
;;\
;; COMPARISONS\
;;\
;; ....................

```
				;;- Order is significant here
				;;- because there are untyped
				;;- comparisons generated by
				;;- the optimizer
                                    ;;- (set (cc0)
                                    ;;-      (compare (const_int 2)
                                    ;;-           (const_int 1)))
```

(define\_insn "cmpsi"\
\[(set (cc0)\
(compare (match\_operand:SI 0 "register\_operand" "r")\
(match\_operand:SI 1 "register\_operand" "r")))]\
""\
"\*\
compare\_collect (SImode, operands\[0], operands\[1]);\
return " #\tcmpsi\t%0,%1";\
")

;; These patterns are hopelessly invalid, because\
;; comparing subword values properly requires extending them.

;; (define\_insn "cmphi"\
;; \[(set (cc0)\
;; (compare (match\_operand:HI 0 "general\_operand" "r")\
;; (match\_operand:HI 1 "general\_operand" "r")))]\
;; ""\
;; "\*\
;; compare\_collect (HImode, operands\[0], operands\[1]);\
;; return " #\tcmphi\t%0,%1";\
;; ")\
;;\
;; (define\_insn "cmpqi"\
;; \[(set (cc0)\
;; (compare (match\_operand:QI 0 "general\_operand" "r")\
;; (match\_operand:QI 1 "general\_operand" "r")))]\
;; ""\
;; "\*\
;; compare\_collect (QImode, operands\[0], operands\[1]);\
;; return " #\tcmpqi\t%0,%1";\
;; ")

(define\_insn "cmpdf"\
\[(set (cc0)\
(compare (match\_operand:DF 0 "register\_operand" "f")\
(match\_operand:DF 1 "register\_operand" "f")))]\
""\
"\*\
compare\_collect (DFmode, operands\[0], operands\[1]);\
return " #\tcmpdf\t%0,%1" ;\
")

(define\_insn "cmpsf"\
\[(set (cc0)\
(compare (match\_operand:SF 0 "register\_operand" "f")\
(match\_operand:SF 1 "register\_operand" "f")))]\
""\
"\*\
compare\_collect (SFmode, operands\[0], operands\[1]);\
return " #\tcmpsf\t%0,%1" ;\
")

(define\_insn ""\
\[(set (cc0)\
(match\_operand:QI 0 "general\_operand" "r"))]\
""\
"\*\
compare\_collect (QImode, operands\[0], gen\_rtx (REG, QImode, 0));\
return " #\t (set (cc0)\t%0)";\
")

(define\_insn ""\
\[(set (cc0)\
(match\_operand:HI 0 "general\_operand" "r"))]\
""\
"\*\
compare\_collect (HImode, operands\[0], gen\_rtx (REG, HImode, 0));\
return " #\t (set (cc0)\t%0)";\
")

(define\_insn ""\
\[(set (cc0)\
(match\_operand:SI 0 "general\_operand" "r"))]\
""\
"\*\
compare\_collect (SImode, operands\[0], gen\_rtx (REG, SImode, 0));\
return " #\t (set (cc0)\t%0)";\
")\
;;\
;; ....................\
;;\
;; BRANCHES\
;;\
;; ....................

(define\_insn "jump"\
\[(set (pc)\
(label\_ref (match\_operand 0 "" "")))]\
""\
"\*\
{\
if (GET\_CODE (operands\[0]) == REG)\
return "j\t%%0\t#jump %l0 (jr not asm syntax)";\
else\
return "j\t%l0\t#jump %l0";\
}")

(define\_insn "beq"\
\[(set (pc)\
(if\_then\_else (eq (cc0)\
(const\_int 0))\
(label\_ref (match\_operand 0 "" ""))\
(pc)))]\
""\
"\*\
{\
rtx br\_ops\[3];\
enum machine\_mode mode;\
compare\_restore (br\_ops, \&mode, insn);\
br\_ops\[2] = operands\[0];\
if (mode == DFmode)\
{\
output\_asm\_insn ("c.eq.d\t%0,%1\t#beq", br\_ops);\
output\_asm\_insn ("bc1t\t%2\t#beq", br\_ops);\
}\
else if (mode == SFmode)\
{\
output\_asm\_insn ("c.eq.s\t%0,%1\t#beq", br\_ops);\
output\_asm\_insn ("bc1t\t%2\t#beq", br\_ops);\
}\
else\
{\
output\_asm\_insn ("beq\t%0,%1,%2\t#beq", br\_ops);\
}\
return "";\
}\
")

(define\_insn "bne"\
\[(set (pc)\
(if\_then\_else (ne (cc0)\
(const\_int 0))\
(label\_ref (match\_operand 0 "" ""))\
(pc)))]\
""\
"\*\
{\
rtx br\_ops\[3];\
enum machine\_mode mode;\
compare\_restore (br\_ops, \&mode, insn);\
br\_ops\[2] = operands\[0];\
if (mode == DFmode)\
{\
output\_asm\_insn ("c.eq.d\t%0,%1\t#bne", br\_ops);\
output\_asm\_insn ("bc1f\t%2\t#bne", br\_ops);\
}\
else if (mode == SFmode)\
{\
output\_asm\_insn ("c.eq.s\t%0,%1\t#bne", br\_ops);\
output\_asm\_insn ("bc1f\t%2\t#bne", br\_ops);\
}\
else\
{\
output\_asm\_insn ("bne\t%0,%1,%2\t#bne", br\_ops);\
}\
return "";\
}

")

(define\_insn "bgt"\
\[(set (pc)\
(if\_then\_else (gt (cc0)\
(const\_int 0))\
(label\_ref (match\_operand 0 "" ""))\
(pc)))]\
""\
"\*\
{\
rtx br\_ops\[3];\
enum machine\_mode mode;\
compare\_restore (br\_ops, \&mode, insn);\
br\_ops\[2] = operands\[0];\
if (mode == DFmode)\
{\
output\_asm\_insn ("c.le.d\t%0,%1\t#bgt branch %0 > %1", br\_ops);\
output\_asm\_insn ("bc1f\t%2\t#bgt", br\_ops);\
}\
else if (mode == SFmode)\
{\
output\_asm\_insn ("c.le.s\t%0,%1\t#bgt branch %0 > %1", br\_ops);\
output\_asm\_insn ("bc1f\t%2\t#bgt", br\_ops);\
}\
else\
{\
output\_asm\_insn ("bgt\t%0,%1,%2\t#bgt", br\_ops);\
}\
return "";\
}

")

(define\_insn "blt"\
\[(set (pc)\
(if\_then\_else (lt (cc0)\
(const\_int 0))\
(label\_ref (match\_operand 0 "" ""))\
(pc)))]\
""\
"\*\
{\
rtx br\_ops\[3];\
enum machine\_mode mode;\
compare\_restore (br\_ops, \&mode, insn);\
br\_ops\[2] = operands\[0];\
if (mode == DFmode)\
{\
output\_asm\_insn ("c.lt.d\t%0,%1\t#blt", br\_ops);\
output\_asm\_insn ("bc1t\t%2\t#blt", br\_ops);\
}\
else if (mode == SFmode)\
{\
output\_asm\_insn ("c.lt.s\t%0,%1\t#blt", br\_ops);\
output\_asm\_insn ("bc1t\t%2\t#blt", br\_ops);\
}\
else\
{\
output\_asm\_insn ("blt\t%0,%1,%2\t#blt", br\_ops);\
}\
return " #\tblt \t%l0\t#blt";\
}\
")\
(define\_insn "bgtu"\
\[(set (pc)\
(if\_then\_else (gtu (cc0)\
(const\_int 0))\
(label\_ref (match\_operand 0 "" ""))\
(pc)))]\
""\
"\*\
{\
rtx br\_ops\[3];\
enum machine\_mode mode;\
compare\_restore (br\_ops, \&mode, insn);\
br\_ops\[2] = operands\[0];\
if (mode == DFmode)\
{\
output\_asm\_insn ("c.le.d\t%0,%1\t#bgtu", br\_ops);\
output\_asm\_insn ("bc1f\t%2\t#bgtu", br\_ops);\
}\
else if (mode == SFmode)\
{\
output\_asm\_insn ("c.le.s\t%0,%1\t#bgtu", br\_ops);\
output\_asm\_insn ("bc1f\t%2\t#bgtu", br\_ops);\
}\
else\
{\
output\_asm\_insn ("bgtu\t%0,%1,%2\t#bgtu", br\_ops);\
}\
return " #\tbgtu \t%l0\t#bgtu";\
}

")

(define\_insn "bltu"\
\[(set (pc)\
(if\_then\_else (ltu (cc0)\
(const\_int 0))\
(label\_ref (match\_operand 0 "" ""))\
(pc)))]\
""\
"\*\
{\
rtx br\_ops\[3];\
enum machine\_mode mode;\
compare\_restore (br\_ops, \&mode, insn);\
br\_ops\[2] = operands\[0];\
if (mode == DFmode)\
{\
output\_asm\_insn ("c.lt.d\t%0,%1\t#bltu", br\_ops);\
output\_asm\_insn ("bc1t\t%2\t#bltu", br\_ops);\
}\
else if (mode == SFmode)\
{\
output\_asm\_insn ("c.lt.s\t%0,%1\t#bltu", br\_ops);\
output\_asm\_insn ("bc1t\t%2\t#bltu", br\_ops);\
}\
else\
{\
output\_asm\_insn ("bltu\t%0,%1,%2\t#bltu", br\_ops);\
}\
return "";\
}\
")

(define\_insn "bge"\
\[(set (pc)\
(if\_then\_else (ge (cc0)\
(const\_int 0))\
(label\_ref (match\_operand 0 "" ""))\
(pc)))]\
""\
"\*\
{\
rtx br\_ops\[3];\
enum machine\_mode mode;\
compare\_restore (br\_ops, \&mode, insn);\
br\_ops\[2] = operands\[0];\
if (mode == DFmode)\
{\
output\_asm\_insn ("c.lt.d\t%0,%1\t#bge", br\_ops);\
output\_asm\_insn ("bc1f\t%2\t#bge", br\_ops);\
}\
else if (mode == SFmode)\
{\
output\_asm\_insn ("c.lt.s\t%0,%1\t#bge", br\_ops);\
output\_asm\_insn ("bc1f\t%2\t#bge", br\_ops);\
}\
else\
{\
output\_asm\_insn ("bge\t%0,%1,%2\t#bge", br\_ops);\
}\
return "";\
}\
")

(define\_insn "bgeu"\
\[(set (pc)\
(if\_then\_else (geu (cc0)\
(const\_int 0))\
(label\_ref (match\_operand 0 "" ""))\
(pc)))]\
""\
"\*\
{\
rtx br\_ops\[3];\
enum machine\_mode mode;\
compare\_restore (br\_ops, \&mode, insn);\
br\_ops\[2] = operands\[0];\
if (mode == DFmode)\
{\
output\_asm\_insn ("c.lt.d\t%0,%1\t#bgeu", br\_ops);\
output\_asm\_insn ("bc1f\t%2\t#bgeu", br\_ops);\
}\
else if (mode == SFmode)\
{\
output\_asm\_insn ("c.lt.s\t%0,%1\t#bgeu", br\_ops);\
output\_asm\_insn ("bc1f\t%2\t#bgeu", br\_ops);\
}\
else\
{\
output\_asm\_insn ("bgeu\t%0,%1,%2\t#bgeu", br\_ops);\
}\
return "";\
}\
")

(define\_insn "ble"\
\[(set (pc)\
(if\_then\_else (le (cc0)\
(const\_int 0))\
(label\_ref (match\_operand 0 "" ""))\
(pc)))]\
""\
"\*\
{\
rtx br\_ops\[3];\
enum machine\_mode mode;\
compare\_restore (br\_ops, \&mode, insn);\
br\_ops\[2] = operands\[0];\
if (mode == DFmode)\
{\
output\_asm\_insn ("c.le.d\t%0,%1\t#ble", br\_ops);\
output\_asm\_insn ("bc1t\t%2\t#ble", br\_ops);\
}\
else if (mode == SFmode)\
{\
output\_asm\_insn ("c.le.s\t%0,%1\t#ble", br\_ops);\
output\_asm\_insn ("bc1t\t%2\t#ble", br\_ops);\
}\
else\
{\
output\_asm\_insn ("ble\t%0,%1,%2\t#ble", br\_ops);\
}\
return "";\
}\
")

(define\_insn "bleu"\
\[(set (pc)\
(if\_then\_else (leu (cc0)\
(const\_int 0))\
(label\_ref (match\_operand 0 "" ""))\
(pc)))]\
""\
"\*\
{\
rtx br\_ops\[3];\
enum machine\_mode mode;\
compare\_restore (br\_ops, \&mode, insn);\
br\_ops\[2] = operands\[0];\
if (mode == DFmode)\
{\
output\_asm\_insn ("c.le.d\t%0,%1\t#ble", br\_ops);\
output\_asm\_insn ("bc1t\t%2\t#ble", br\_ops);\
}\
else if (mode == SFmode)\
{\
output\_asm\_insn ("c.le.s\t%0,%1\t#ble", br\_ops);\
output\_asm\_insn ("bc1t\t%2\t#ble", br\_ops);\
}\
else\
{\
output\_asm\_insn ("bleu\t%0,%1,%2\t#bleu", br\_ops);\
}\
return " #\tbleu \t%l0\t#bleu";\
}\
")\
(define\_insn ""\
\[(set (pc)\
(if\_then\_else (ne (cc0)\
(const\_int 0))\
(pc)\
(label\_ref (match\_operand 0 "" ""))))]\
""\
"\*\
{\
rtx br\_ops\[3];\
enum machine\_mode mode;\
compare\_restore (br\_ops, \&mode, insn);\
br\_ops\[2] = operands\[0];\
if (mode == DFmode)\
{\
output\_asm\_insn ("c.eq.d\t%0,%1\t#beq", br\_ops);\
output\_asm\_insn ("bc1t\t%2\t#beq", br\_ops);\
}\
else if (mode == SFmode)\
{\
output\_asm\_insn ("c.eq.s\t%0,%1\t#beq", br\_ops);\
output\_asm\_insn ("bc1t\t%2\t#beq", br\_ops);\
}\
else\
{\
output\_asm\_insn ("beq\t%0,%1,%2\t#beq Inv.", br\_ops);\
}\
return "";\
}\
")

(define\_insn ""\
\[(set (pc)\
(if\_then\_else (eq (cc0)\
(const\_int 0))\
(pc)\
(label\_ref (match\_operand 0 "" ""))))]\
""\
"\*\
{\
rtx br\_ops\[3];\
enum machine\_mode mode;\
compare\_restore (br\_ops, \&mode, insn);\
br\_ops\[2] = operands\[0];\
if (mode == DFmode)\
{\
output\_asm\_insn ("c.eq.d\t%0,%1\t#bne", br\_ops);\
output\_asm\_insn ("bc1f\t%2\t#bne", br\_ops);\
}\
else if (mode == SFmode)\
{\
output\_asm\_insn ("c.eq.s\t%0,%1\t#bne", br\_ops);\
output\_asm\_insn ("bc1f\t%2\t#beq", br\_ops);\
}\
else\
{\
output\_asm\_insn ("bne\t%0,%1,%2\t#bne Inv.", br\_ops);\
}\
return "";\
}

")

(define\_insn ""\
\[(set (pc)\
(if\_then\_else (le (cc0)\
(const\_int 0))\
(pc)\
(label\_ref (match\_operand 0 "" ""))))]\
""\
"\*\
{\
rtx br\_ops\[3];\
enum machine\_mode mode;\
compare\_restore (br\_ops, \&mode, insn);\
br\_ops\[2] = operands\[0];\
if (mode == DFmode)\
{\
output\_asm\_insn ("c.le.d\t%0,%1\t#bgt", br\_ops);\
output\_asm\_insn ("bc1f\t%2\t#beq", br\_ops);\
}\
else if (mode == SFmode)\
{\
output\_asm\_insn ("c.le.s\t%0,%1\t#bgt", br\_ops);\
output\_asm\_insn ("bc1f\t%2\t#beq", br\_ops);\
}\
else\
{\
output\_asm\_insn ("bgt\t%0,%1,%2\t#bgt Inv.", br\_ops);\
}\
return "";\
}\
")

(define\_insn ""\
\[(set (pc)\
(if\_then\_else (leu (cc0)\
(const\_int 0))\
(pc)\
(label\_ref (match\_operand 0 "" ""))))]\
""\
"\*\
{\
rtx br\_ops\[3];\
enum machine\_mode mode;\
compare\_restore (br\_ops, \&mode, insn);\
br\_ops\[2] = operands\[0];\
if (mode == DFmode)\
{\
output\_asm\_insn ("c.le.d\t%0,%1\t#bgt", br\_ops);\
output\_asm\_insn ("bc1f\t%2\t#beq", br\_ops);\
}\
else if (mode == SFmode)\
{\
output\_asm\_insn ("c.le.s\t%0,%1\t#bgt", br\_ops);\
output\_asm\_insn ("bc1f\t%2\t#beq", br\_ops);\
}\
else\
{\
output\_asm\_insn ("bgtu\t%0,%1,%2\t#bgtu Inv.", br\_ops);\
}\
return " #\tbgtu \t%l0\t#bgtu";\
}\
")

(define\_insn ""\
\[(set (pc)\
(if\_then\_else (ge (cc0)\
(const\_int 0))\
(pc)\
(label\_ref (match\_operand 0 "" ""))))]\
""\
"\*\
{\
rtx br\_ops\[3];\
enum machine\_mode mode;\
compare\_restore (br\_ops, \&mode, insn);\
br\_ops\[2] = operands\[0];\
if (mode == DFmode)\
{\
output\_asm\_insn ("c.lt.d\t%0,%1\t#blt", br\_ops);\
output\_asm\_insn ("bc1t\t%2\t#beq", br\_ops);\
}\
else if (mode == SFmode)\
{\
output\_asm\_insn ("c.lt.s\t%0,%1\t#blt", br\_ops);\
output\_asm\_insn ("bc1t\t%2\t#beq", br\_ops);\
}\
else\
{\
output\_asm\_insn ("blt\t%0,%1,%2\t#blt Inv.", br\_ops);\
}\
return "";\
}\
")

(define\_insn ""\
\[(set (pc)\
(if\_then\_else (geu (cc0)\
(const\_int 0))\
(pc)\
(label\_ref (match\_operand 0 "" ""))))]\
""\
"\*\
{\
rtx br\_ops\[3];\
enum machine\_mode mode;\
compare\_restore (br\_ops, \&mode, insn);\
br\_ops\[2] = operands\[0];\
if (mode == DFmode)\
{\
output\_asm\_insn ("c.lt.d\t%0,%1\t#bltu", br\_ops);\
output\_asm\_insn ("bc1t\t%2\t#bltu", br\_ops);\
}\
else if (mode == SFmode)\
{\
output\_asm\_insn ("c.lt.s\t%0,%1\t#bltu", br\_ops);\
output\_asm\_insn ("bc1t\t%2\t#bltu", br\_ops);\
}\
else\
{\
output\_asm\_insn ("bltu\t%0,%1,%2\t#bltu Inv.", br\_ops);\
}\
return " #\tbltu \t%l0\t#bltu";\
}\
")

(define\_insn ""\
\[(set (pc)\
(if\_then\_else (lt (cc0)\
(const\_int 0))\
(pc)\
(label\_ref (match\_operand 0 "" ""))))]\
""\
"\*\
{\
rtx br\_ops\[3];\
enum machine\_mode mode;\
compare\_restore (br\_ops, \&mode, insn);\
br\_ops\[2] = operands\[0];\
if (mode == DFmode)\
{\
output\_asm\_insn ("c.lt.d\t%0,%1\t#bge", br\_ops);\
output\_asm\_insn ("bc1f\t%2\t#bge (DF) Inv.", br\_ops);\
}\
else if (mode == SFmode)\
{\
output\_asm\_insn ("c.lt.s\t%0,%1\t#bge", br\_ops);\
output\_asm\_insn ("bc1f\t%2\t#bge (SF) Inv.", br\_ops);\
}\
else\
{\
output\_asm\_insn ("bge\t%0,%1,%2\t#bge Inv.", br\_ops);\
}\
return "";\
}\
")

(define\_insn ""\
\[(set (pc)\
(if\_then\_else (ltu (cc0)\
(const\_int 0))\
(pc)\
(label\_ref (match\_operand 0 "" ""))))]\
""\
"\*\
{\
rtx br\_ops\[3];\
enum machine\_mode mode;\
compare\_restore (br\_ops, \&mode, insn);\
br\_ops\[2] = operands\[0];\
if (mode == DFmode)\
{\
output\_asm\_insn ("c.lt.d\t%0,%1\t#bge", br\_ops);\
output\_asm\_insn ("bc1f\t%2\t#bgeu (DF) Inv.", br\_ops);\
}\
else if (mode == SFmode)\
{\
output\_asm\_insn ("c.lt.s\t%0,%1\t#bge", br\_ops);\
output\_asm\_insn ("bc1f\t%2\t#bgeu (SF )Inv.", br\_ops);\
}\
else\
{\
output\_asm\_insn ("bgeu\t%0,%1,%2\t#bgeu Inv.", br\_ops);\
}\
return " #\tbgeu \t%l0\t#bgeu";\
}\
")

(define\_insn ""\
\[(set (pc)\
(if\_then\_else (gt (cc0)\
(const\_int 0))\
(pc)\
(label\_ref (match\_operand 0 "" ""))))]\
""\
"\*\
{\
rtx br\_ops\[3];\
enum machine\_mode mode;\
compare\_restore (br\_ops, \&mode, insn);\
br\_ops\[2] = operands\[0];\
if (mode == DFmode)\
{\
output\_asm\_insn ("c.le.d\t%0,%1\t#ble", br\_ops);\
output\_asm\_insn ("bc1t\t%2\t#ble", br\_ops);\
}\
else if (mode == SFmode)\
{\
output\_asm\_insn ("c.le.s\t%0,%1\t#ble", br\_ops);\
output\_asm\_insn ("bc1t\t%2\t#ble", br\_ops);\
}\
else\
{\
output\_asm\_insn ("ble\t%0,%1,%2\t#ble Inv.", br\_ops);\
}\
return "";\
}\
")

(define\_insn ""\
\[(set (pc)\
(if\_then\_else (gtu (cc0)\
(const\_int 0))\
(pc)\
(label\_ref (match\_operand 0 "" ""))))]\
""\
"\*\
{\
rtx br\_ops\[3];\
enum machine\_mode mode;\
compare\_restore (br\_ops, \&mode, insn);\
br\_ops\[2] = operands\[0];\
if (mode == DFmode)\
{\
output\_asm\_insn ("c.le.d\t%0,%1\t#bleu", br\_ops);\
output\_asm\_insn ("bc1t\t%2\t#bleu", br\_ops);\
}\
else if (mode == SFmode)\
{\
output\_asm\_insn ("c.le.s\t%0,%1\t#bleu", br\_ops);\
output\_asm\_insn ("bc1t\t%2\t#bleu", br\_ops);\
}\
else\
{\
output\_asm\_insn ("bleu\t%0,%1,%2\t#bleu Inv.", br\_ops);\
}\
return "";\
}\
")

(define\_insn "tablejump"\
\[(set (pc)\
(match\_operand:SI 0 "general\_operand" "r"))\
(use (label\_ref (match\_operand 1 "" "")))]\
""\
"j\t%0\t# tablejump, label %l1\t (jr not asm syntax)")\
;;\
;; ....................\
;;\
;; LINKAGE\
;;\
;; ....................

(define\_insn "call"\
\[(call (match\_operand 0 "general\_operand" "g")\
(match\_operand 1 "general\_operand" "g"))\
(clobber (reg:SI 31))]\
""\
"\*\
{\
if (GET\_CODE (XEXP (operands\[0], 0)) == SYMBOL\_REF)\
return "jal\t%0\t# call with %1 arguments";\
else\
{\
operands\[0] = XEXP (operands\[0], 0);\
return "jal\t$31,%0\t# call with %1 arguments (reg)";\
}\
}" )

(define\_expand "call\_value"\
\[(set (match\_operand 0 "" "=rf")\
(call (match\_operand:SI 1 "memory\_operand" "m")\
(match\_operand 2 "" "i")))\
(clobber (reg:SI 31))]\
;; operand 3 is next\_arg\_register\
""\
"\
{\
rtx fn\_rtx, nregs\_rtx;\
rtvec vec;

fn\_rtx = operands\[1];

nregs\_rtx = const0\_rtx;

vec = gen\_rtvec (2,\
gen\_rtx (SET, VOIDmode, operands\[0],\
gen\_rtx (CALL, VOIDmode, fn\_rtx, nregs\_rtx)),\
gen\_rtx (CLOBBER, VOIDmode, gen\_rtx (REG, SImode, 31)));

emit\_call\_insn (gen\_rtx (PARALLEL, VOIDmode, vec));\
DONE;\
}")

(define\_insn ""\
\[(set (match\_operand 0 "general\_operand" "=g,f")\
(call (match\_operand 1 "general\_operand" "g,g")\
(match\_operand 2 "general\_operand" "g,g")))\
(clobber (match\_operand 3 "general\_operand" "g,g"))]\
""\
"\*\
{\
if (GET\_CODE (XEXP (operands\[1], 0)) == SYMBOL\_REF)\
return "jal\t%1\t# call %1 regle 2-call (VOIDmode)";\
else\
{\
operands\[1] = XEXP (operands\[1], 0);\
return "jal\t$31,%1\t# call %1 regle 2-call (VOIDmode, reg)";\
}\
}")

(define\_insn "nop"\
\[(const\_int 0)]\
""\
"nop")

(define\_expand "probe"\
\[(set (reg:SI 29) (minus:SI (reg:SI 29) (const\_int 4)))\
(set (mem:SI (reg:SI 29)) (const\_int 0))\
(set (reg:SI 29) (plus:SI (reg:SI 29) (const\_int 4)))]\
""\
"")\
;;\
;;- Local variables:\
;;- mode:emacs-lisp\
;;- comment-start: ";;- "\
;;- eval: (set-syntax-table (copy-sequence (syntax-table)))\
;;- eval: (modify-syntax-entry ?\[ "(]")\
;;- eval: (modify-syntax-entry ?] ")\[")\
;;- eval: (modify-syntax-entry ?{ "(}")\
;;- eval: (modify-syntax-entry ?} "){")\
;;- End:
