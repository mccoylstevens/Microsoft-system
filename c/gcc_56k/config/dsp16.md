# dsp16

;;- Machine description for the Motorola dsp561XX for GNU C compiler\
;; Copyright ( C ) 1988 Free Software Foundation, Inc.\
;;\
;; $Header: /usr1/dsp/cvsroot/source/gcc/config/dsp16.md,v 1.2 91/09/18 15:34:41 jeff Exp $\
;; $Id: dsp16.md,v 1.2 91/09/18 15:34:41 jeff Exp $\
;;\
;; This file is part of GNU CC.

;; GNU CC is free software; you can redistribute it and/or modify\
;; it under the terms of the GNU General Public License as published by\
;; the Free Software Foundation; either version 1, or ( at your option )\
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

;; %letter escapes for operand printing. (via PRINT\_OPERAND).\
;; %d - print the accumulator name, disregard MEM\_IN\_STRUCT kludge.\
;; %e - print 1 after accumulator operand. No affect on non-accum reg.\
;; %f - print x: or y: before memory reference operand.\
;; %g - print alternate of x or y half-register.\
;; %h - print 0 after accumulator operand.\
;; %i - strip the 0/1 off an x or y half-register.\
;; %j - n register associated with r register.\
;; %k - print 2 after accumulator operand.\
;; %m - print the source reg name, without a trailing 0 or 1.\
;; %o - print the accumulator name with a trailing 10.

;; %p - output this as a pointer size constant.\
;; %q - output this as an integer size constant.\
;; %r - output this as a long size constant.

;;\
;; ...........................................................................\
;;\
;; MOVES, LOADS, STORES\
;;\
;; ...........................................................................\
;;

;; allow a PSImode value to be loaded into any register, but preferentially\
;; load it into an A register. The A must come last - otherwise we risk\
;; messing up the code that does subunion stuff.

( define\_expand "movpsi"\
\[ ( set\
( match\_operand:PSI 0 "general\_operand" "" )\
( match\_operand:PSI 1 "general\_operand" "" ))\
]\
""\
"\
{\
if ( MEM == GET\_CODE ( operands\[0] ) &&\
( MEM == GET\_CODE ( operands\[1] ) ||\
CONSTANT\_P ( operands\[1] ) ||\
CONST\_DOUBLE == GET\_CODE ( operands\[1] )))\
{\
operands\[1] = force\_reg ( PSImode, operands\[1] );\
}\
}" )

( define\_insn ""\
\[ ( set\
( match\_operand:PSI 0 "register\_operand" "=_D,SDA,m,SDA,SDA" )_\
_( match\_operand:PSI 1 "general\_operand" "SD,SDA,SDA,m,i" ))_\
_]_\
_""_\
_"_\
{\
return move\_pointer ( operands );\
}" "1" )

( define\_insn ""\
\[ ( set\
( match\_operand:PSI 0 "general\_operand" "=_D,SDA,m,SDA,SDA" )_\
_( match\_operand:PSI 1 "register\_operand" "SD,SDA,SDA,m,i" ))_\
_]_\
_""_\
_"_\
{\
return move\_pointer ( operands );\
}" "1" )

( define\_expand "movqi"\
\[ ( set\
( match\_operand:QI 0 "general\_operand" "" )\
( match\_operand:QI 1 "general\_operand" "" ))\
]\
""\
"\
{\
if ( MEM == GET\_CODE ( operands\[0] ) &&\
( MEM == GET\_CODE ( operands\[1] ) ||\
CONSTANT\_P ( operands\[1] ) ||\
CONST\_DOUBLE == GET\_CODE ( operands\[1] )))\
{\
operands\[1] = force\_reg ( QImode, operands\[1] );\
}\
}" )

( define\_insn ""\
\[ ( set\
( match\_operand:QI 0 "register\_operand" "=_D,S,<>m,SD,SD" )_\
_( match\_operand:QI 1 "general\_operand" "SAD,S&#x41;_&#x44;,_&#x53;_&#x41;_D,<>m,i" ))_\
_]_\
_""_\
_"_\
{\
return move\_singleword ( operands );\
}" "1" )

( define\_insn ""\
\[ ( set\
( match\_operand:QI 0 "general\_operand" "=_D,S,<>m,SD,SD" )_\
_( match\_operand:QI 1 "register\_operand" "SAD,S&#x41;_&#x44;,_&#x53;_&#x41;_D,<>m,i" ))_\
_]_\
_""_\
_"_\
{\
return move\_singleword ( operands );\
}" "1" )

( define\_expand "movsi"\
\[ ( set\
( match\_operand:SI 0 "general\_operand" "" )\
( match\_operand:SI 1 "general\_operand" "" ))\
]\
""\
"\
{\
if ( MEM == GET\_CODE ( operands\[0] ) &&\
( MEM == GET\_CODE ( operands\[1] ) ||\
CONSTANT\_P ( operands\[1] ) ||\
CONST\_DOUBLE == GET\_CODE ( operands\[1] )))\
{\
operands\[1] = force\_reg ( SImode, operands\[1] );\
}\
}" )

( define\_insn ""\
\[ ( set\
( match\_operand:SI 0 "register\_operand" "=_D,S,<>m,SD,SD" )_\
_( match\_operand:SI 1 "general\_operand" "SAD,S&#x41;_&#x44;,_&#x53;_&#x41;_D,<>m,i" ))_\
_]_\
_""_\
_"_\
{\
return move\_singleword ( operands );\
}" "1" )

( define\_insn ""\
\[ ( set\
( match\_operand:SI 0 "general\_operand" "=_D,S,<>m,SD,SD" )_\
_( match\_operand:SI 1 "register\_operand" "SAD,S&#x41;_&#x44;,_&#x53;_&#x41;_D,<>m,i" ))_\
_]_\
_""_\
_"_\
{\
return move\_singleword ( operands );\
}" "1" )

( define\_expand "movdi"\
\[ ( set\
( match\_operand:DI 0 "general\_operand" "" )\
( match\_operand:DI 1 "general\_operand" "" ))\
]\
""\
"\
{\
if ( MEM == GET\_CODE ( operands\[0] ) &&\
( MEM == GET\_CODE ( operands\[1] ) ||\
CONSTANT\_P ( operands\[1] ) ||\
CONST\_DOUBLE == GET\_CODE ( operands\[1] )))\
{\
operands\[1] = force\_reg ( DImode, operands\[1] );\
}\
}" )

( define\_insn ""\
\[ ( set\
( match\_operand:DI 0 "register\_operand" "=\*D,_S,<>m,DS,DS" )_\
_( match\_operand:DI 1 "general\_operand" "DS,DS,DS,<>m,Gi" ))_\
_]_\
_""_\
_"_\
{\
return move\_doubleword ( 0, operands );\
}" "1" )

( define\_insn ""\
\[ ( set\
( match\_operand:DI 0 "general\_operand" "=\*D,_S,<>m,DS,DS" )_\
_( match\_operand:DI 1 "register\_operand" "DS,DS,DS,<>m,Gi" ))_\
_]_\
_""_\
_"_\
{\
return move\_doubleword ( 0, operands );\
}" "1" )

( define\_expand "movsf"\
\[ ( set\
( match\_operand:SF 0 "general\_operand" "" )\
( match\_operand:SF 1 "general\_operand" "" ))\
]\
""\
"\
{\
if ( MEM == GET\_CODE ( operands\[0] ) &&\
( MEM == GET\_CODE ( operands\[1] ) ||\
CONSTANT\_P ( operands\[1] ) ||\
CONST\_DOUBLE == GET\_CODE ( operands\[1] )))\
{\
operands\[1] = force\_reg ( SFmode, operands\[1] );\
}\
}" )

( define\_insn ""\
\[ ( set\
( match\_operand:SF 0 "register\_operand" "=\*D,_S,<>m,DS,DS" )_\
_( match\_operand:SF 1 "general\_operand" "DS,DS,DS,<>m,G" ))_\
_]_\
_""_\
_"_\
{\
return move\_doubleword ( 1, operands );\
}" "1" )

( define\_insn ""\
\[ ( set\
( match\_operand:SF 0 "general\_operand" "=\*D,_S,<>m,DS,DS" )_\
_( match\_operand:SF 1 "register\_operand" "DS,DS,DS,<>m,G" ))_\
_]_\
_""_\
_"_\
{\
return move\_doubleword ( 1, operands );\
}" "1" )

( define\_expand "movdf"\
\[ ( set\
( match\_operand:DF 0 "general\_operand" "" )\
( match\_operand:DF 1 "general\_operand" "" ))\
]\
""\
"\
{\
if ( MEM == GET\_CODE ( operands\[0] ) &&\
( MEM == GET\_CODE ( operands\[1] ) ||\
CONSTANT\_P ( operands\[1] ) ||\
CONST\_DOUBLE == GET\_CODE ( operands\[1] )))\
{\
operands\[1] = force\_reg ( DFmode, operands\[1] );\
}\
}" )

( define\_insn ""\
\[ ( set\
( match\_operand:DF 0 "register\_operand" "=\*D,_S,<>m,DS,DS" )_\
_( match\_operand:DF 1 "general\_operand" "DS,DS,DS,<>m,G" ))_\
_]_\
_""_\
_"_\
{\
return move\_doubleword ( 1, operands );\
}" "1" )

( define\_insn ""\
\[ ( set\
( match\_operand:DF 0 "general\_operand" "=\*D,_S,<>m,DS,DS" )_\
_( match\_operand:DF 1 "register\_operand" "DS,DS,DS,<>m,G" ))_\
_]_\
_""_\
_"_\
{\
return move\_doubleword ( 1, operands );\
}" "1" )

;;\
;; ...........................................................................\
;;\
;; BLOCKMOVES\
;;\
;; ...........................................................................\
;;

( define\_expand "movstrsi"\
\[ ( parallel \[ ( set ( match\_operand:BLK 0 "memory\_operand" "" )\
( match\_operand:BLK 1 "memory\_operand" "" ))\
( use ( match\_operand:SI 2 "general\_operand" "" ))\
( use ( match\_operand:SI 3 "general\_operand" "" )) ] )\
]\
""\
"\
{\
rtx op0\_reg, op1\_reg, cpy\_reg;

```
op0_reg = gen_reg_rtx ( PSImode );
emit_move_insn ( op0_reg, force_reg ( PSImode, XEXP ( operands[0], 0 )));

op1_reg = gen_reg_rtx ( PSImode );
emit_move_insn ( op1_reg, force_reg ( PSImode, XEXP ( operands[1], 0 )));

cpy_reg = gen_reg_rtx ( DImode );

if ( CONST_DOUBLE == GET_CODE ( operands[2] ))
{
operands[2] = gen_rtx ( CONST_INT, VOIDmode, 
		       CONST_DOUBLE_LOW ( operands[2] ));
}

emit_insn (
       gen_rtx ( PARALLEL, VOIDmode,
		gen_rtvec ( 7,
			   gen_rtx ( CLOBBER, VOIDmode, op0_reg ),
			   gen_rtx ( CLOBBER, VOIDmode, op1_reg ),
			   gen_rtx ( USE, VOIDmode, operands[2] ),
			   gen_rtx ( CLOBBER, VOIDmode, cpy_reg ),
			   gen_rtx ( SET, VOIDmode, operands[0],
				    operands[1] ),
			   gen_rtx ( USE, VOIDmode, op0_reg ),
			   gen_rtx ( USE, VOIDmode, op1_reg ))));
DONE;
```

}" )

( define\_insn ""\
\[ ( parallel \[ ( clobber ( match\_operand:PSI 0 "register\_operand" "=A" ))\
( clobber ( match\_operand:PSI 1 "register\_operand" "=A" ))\
( use ( match\_operand:SI 2 "immediate\_operand" "i" ))\
( clobber ( match\_operand:DI 3 "register\_operand" "=_&#x44;_&#x53;" ))\
( set ( match\_operand:BLK 4 "memory\_operand" "" )\
( match\_operand:BLK 5 "memory\_operand" "" ))\
( use ( match\_operand:PSI 6 "register\_operand" "0" ))\
( use ( match\_operand:PSI 7 "register\_operand" "1" )) ] )\
]\
""\
"\*\
{\
operands\[8] = gen\_label\_rtx ( );

```
if ( 'l' == memory_model )
{
if ( IS_SRC_OR_MPY_P ( REGNO ( operands[3] )))
{
    return \"do	#%c2,%l8\;move	l:(%1)+,%m3\;move	%m3,l:(%0)+\\n%l8\";
}
else
{
    return \"do	#%c2,%l8\;move	l:(%1)+,%o3\;move	%o3,l:(%0)+\\n%l8\";
}
}
else /* single mem space. */
{
RETURN_DSP ( \"do	#%c2,%l8\;move	@:(%1)+,%3\;move	%3,@:(%0)+\\n%l8\" );
}
```

}" )

;;\
;; ...........................................................................\
;;\
;; ADDITIONS\
;;\
;; ...........................................................................\
;;

;; We provide quick software emulation via a subroutine that emulates\
;; a floating point instruction.

( define\_insn "adddf3"\
\[ ( set ( match\_operand:DF 0 "general\_operand" "=D" )\
( plus:DF ( match\_operand:DF 1 "general\_operand" "0" )\
( match\_operand:DF 2 "general\_operand" "D" )))\
]\
""\
"\*\
{\
int op0\_is\_a = ( DSP16\_A\_REGNUM == REGNO ( operands\[0] ));\
int op2\_is\_a = ( DSP16\_A\_REGNUM == REGNO ( operands\[2] ));

```
if ( op0_is_a )
{
if ( op2_is_a )
{
    return \"jsr	fadd_aa\";
}
else
{
    return \"jsr	fadd_ba\";
}
}
else
{
if ( op2_is_a )
{
    return \"jsr	fadd_ab\";
}
else
{
    return \"jsr	fadd_bb\";
}
}
```

}" )

( define\_insn "addsf3"\
\[ ( set ( match\_operand:SF 0 "general\_operand" "=D" )\
( plus:SF ( match\_operand:SF 1 "general\_operand" "0" )\
( match\_operand:SF 2 "general\_operand" "D" )))\
]\
""\
"\*\
{\
int op0\_is\_a = ( DSP16\_A\_REGNUM == REGNO ( operands\[0] ));\
int op2\_is\_a = ( DSP16\_A\_REGNUM == REGNO ( operands\[2] ));

```
if ( op0_is_a )
{
if ( op2_is_a )
{
    return \"jsr	fadd_aa\";
}
else
{
    return \"jsr	fadd_ba\";
}
}
else
{
if ( op2_is_a )
{
    return \"jsr	fadd_ab\";
}
else
{
    return \"jsr	fadd_bb\";
}
}
```

}" )

;; Note that we MUST provide for inc/dec with a constant of one. The reload\
;; pass may arbitrarily spill a regsiter post/pre inc/dec and will attempt to\
;; perform the increment on the fly by itself. It does not consult the code\
;; generator to see whether ( PSI <= PSI + CONST\_INT ) is a valid insn.

( define\_insn "addpsi3"\
\[ ( set ( match\_operand:PSI 0 "register\_operand" "=\*D,_A" )_\
_( plus:PSI_\
_( match\_operand:PSI 1 "register\_operand" "0,0" )_\
_( match\_operand:SI 2 "general\_operand" "DS,i" )))_\
_]_\
_""_\
_"_\
{\
if ( which\_alternative )\
{\
switch ( INTVAL ( operands\[2] ))\
{\
case 0:\
return "";

```
case 1:
    return \"move	(%0)+\";
    
case -1:
    return \"move	(%0)-\";
    
default:
    if ( load_n_reg_p ( operands[0], operands[2] ))
    {
	return \"move	#%p2,%j0\;move	(%0)+%j0\";
    }
    else
    {
	return \"move	(%0)+%j0\";
    }
}
}
else
{
return \"add	%2,%0\";
}
```

}" )

;; we need this extra shape because addition commutes.

( define\_insn ""\
\[ ( set ( match\_operand:PSI 0 "register\_operand" "=\*D,_A" )_\
_( plus:PSI_\
_( match\_operand:SI 1 "general\_operand" "DS,i" )_\
_( match\_operand:PSI 2 "register\_operand" "0,0" )))_\
_]_\
_""_\
_"_\
{\
if ( which\_alternative )\
{\
switch ( INTVAL ( operands\[2] ))\
{\
case 0:\
return "";

```
case 1:
    return \"move	(%0)+\";
    
case -1:
    return \"move	(%0)-\";
    
default:
    if ( load_n_reg_p ( operands[0], operands[2] ))
    {
	return \"move	#%p1,%j0\;move	(%0)+%j0\";
    }
    else
    {
	return \"move	(%0)+%j0\";
    }
}
}
else
{
return \"add	%1,%0\";
}
```

}" )

;; this insn is here because insn\_extract does not detect commutable operands.

( define\_insn ""\
\[ ( set ( match\_operand:PSI 0 "register\_operand" "=\*D" )\
( plus:PSI\
( match\_operand:SI 1 "register\_operand" "_&#x44;_&#x53;" )\
( match\_operand:PSI 2 "register\_operand" "0" )))\
]\
""\
"add %1,%0" )

( define\_insn "addsi3"\
\[ ( set ( match\_operand:SI 0 "register\_operand" "=_D" )_\
_( plus:SI ( match\_operand:SI 1 "register\_operand" "0" )_\
_( match\_operand:SI 2 "register\_operand" "SD" )))_\
_]_\
_""_\
_"_\
{\
if ( REGNO ( operands\[2] ) == REGNO ( operands\[0] ))\
{\
return "asl %0";\
}\
else\
{\
return "add %2,%0";\
}\
}" )

;;( define\_insn ""\
;; \[ ( set ( match\_operand:SI 0 "register\_operand" "=_D" )_\
_;; ( plus:SI ( match\_operand:SI 1 "register\_operand" "%0" )_\
_;; ( match\_operand:PSI 2 "register\_operand" "SD" )))_\
_;; ]_\
_;; ""_\
_;; "_\
;;{\
;; if ( REGNO ( operands\[2] ) == REGNO ( operands\[0] ))\
;; {\
;; return "asl %0";\
;; }\
;; else\
;; {\
;; return "add %2,%0";\
;; }\
;;}" )

( define\_insn "adddi3"\
\[ ( set ( match\_operand:DI 0 "register\_operand" "=_D" )_\
_( plus:DI ( match\_operand:DI 1 "register\_operand" "0" )_\
_( match\_operand:DI 2 "register\_operand" "SD" ))) ]_\
_""_\
_"_\
{\
if ( IS\_SRC\_OR\_MPY\_P ( REGNO ( operands\[2] )))\
{\
return "add %i2,%0";\
}\
else\
{\
if ( REGNO ( operands\[2] ) == REGNO ( operands\[0] ))\
{\
return "asl %0";\
}\
else\
{\
return "add %2,%0";\
}\
}\
}" )

;;\
;; ...........................................................................\
;;\
;; SUBTRACTIONS\
;;\
;; ...........................................................................\
;;

;; We provide quick software emulation via a subroutine that emulates\
;; a floating point instruction.

( define\_insn "subdf3"\
\[ ( set ( match\_operand:DF 0 "general\_operand" "=D" )\
( minus:DF ( match\_operand:DF 1 "general\_operand" "0" )\
( match\_operand:DF 2 "general\_operand" "D" )))\
]\
""\
"\*\
{\
int op0\_is\_a = ( DSP16\_A\_REGNUM == REGNO ( operands\[0] ));\
int op2\_is\_a = ( DSP16\_A\_REGNUM == REGNO ( operands\[2] ));

```
if ( op0_is_a )
{
if ( op2_is_a )
{
    return \"jsr	fsub_aa\";
}
else
{
    return \"jsr	fsub_ba\";
}
}
else
{
if ( op2_is_a )
{
    return \"jsr	fsub_ab\";
}
else
{
    return \"jsr	fsub_bb\";
}
}
```

}" )

( define\_insn "subsf3"\
\[ ( set ( match\_operand:SF 0 "general\_operand" "=D" )\
( minus:SF ( match\_operand:SF 1 "general\_operand" "0" )\
( match\_operand:SF 2 "general\_operand" "D" )))\
]\
""\
"\*\
{\
int op0\_is\_a = ( DSP16\_A\_REGNUM == REGNO ( operands\[0] ));\
int op2\_is\_a = ( DSP16\_A\_REGNUM == REGNO ( operands\[2] ));

```
if ( op0_is_a )
{
if ( op2_is_a )
{
    return \"jsr	fsub_aa\";
}
else
{
    return \"jsr	fsub_ba\";
}
}
else
{
if ( op2_is_a )
{
    return \"jsr	fsub_ab\";
}
else
{
    return \"jsr	fsub_bb\";
}
}
```

}" )

;; There are really two ways that subtraction can be used with pointers:\
;; 1) PSI <= PSI - SI, and 2) SI <= PSI - PSI. Because of the way that the\
;; GNU CC is structured in optabs.c, subtraction type (1) must be labeled as\
;; subpsi3. Version 1.7 of optabs.c has code that, when turned on, will allow\
;; you use type (2) as subpsi3. Currently, the shape for type (2) should\
;; never be used: c-typeck.c converts the operands of a (2) to SImode before\
;; the subtraction. This has no effect on us currently, however, if we should\
;; try to use the address alu for PSImode adds and subtracts, it will.

;; Addendum: the shape for (2) could actually be generated by the DSP loop\
;; optimization code in computing the number of loop iterations.

;; Note that we MUST provide for inc/dec with a constant of one. The reload\
;; pass may arbitrarily spill a regsiter post/pre inc/dec and will attempt to\
;; perform the increment on the fly by itself. It does not consult the code\
;; generator to see whether ( PSI <= PSI + CONST\_INT ) is a valid insn.

( define\_insn "subpsi3"\
\[ ( set ( match\_operand:PSI 0 "register\_operand" "=\*D,_A" )_\
_( minus:PSI_\
_( match\_operand:PSI 1 "register\_operand" "0,0" )_\
_( match\_operand:SI 2 "general\_operand" "DS,i" )))_\
_]_\
_""_\
_"_\
{\
if ( which\_alternative )\
{\
switch ( INTVAL ( operands\[2] ))\
{\
case 0:\
return "";

```
case 1:
    return \"move	(%0)-\";
    
case -1:
    return \"move	(%0)+\";
    
default:
    if ( load_n_reg_p ( operands[0], operands[2] ))
    {
	return \"move	#%p2,%j0\;move	(%0)-%j0\";
    }
    else
    {
	return \"move	(%0)-%j0\";
    }
}
}
else
{
return \"sub	%2,%0\";
}
```

}" )

( define\_insn ""\
\[ ( set ( match\_operand:SI 0 "register\_operand" "=\*D" )\
( minus:PSI\
( match\_operand:PSI 1 "register\_operand" "0" )\
( match\_operand:PSI 2 "register\_operand" "_&#x44;_&#x53;" )))\
]\
""\
"sub %2,%0" )

( define\_insn "subsi3"\
\[ ( set ( match\_operand:SI 0 "register\_operand" "=D" )\
( minus:SI ( match\_operand:SI 1 "register\_operand" "0" )\
( match\_operand:SI 2 "register\_operand" "_&#x53;_&#x44;" ))) ]\
""\
"\*\
{\
if ( REGNO ( operands\[2] ) == REGNO ( operands\[0] ))\
{\
return "clr %0";\
}\
else\
{\
return "sub %2,%0";\
}\
}" )

( define\_insn "subdi3"\
\[ ( set ( match\_operand:DI 0 "register\_operand" "=D" )\
( minus:DI ( match\_operand:DI 1 "register\_operand" "0" )\
( match\_operand:DI 2 "register\_operand" "_&#x53;_&#x44;" )))\
]\
""\
"\*\
{\
if ( IS\_SRC\_OR\_MPY\_P ( REGNO ( operands\[2] )))\
{\
return "sub %i2,%0";\
}\
else\
{\
if ( REGNO ( operands\[2] ) == REGNO ( operands\[0] ))\
{\
return "clr %0";\
}\
else\
{\
return "sub %2,%0";\
}\
}\
}" )

;;\
;; ...........................................................................\
;;\
;; MULTIPLICATIONS AND DIVISIONS\
;;\
;; ...........................................................................\
;;

;; We provide quick software emulation via a subroutine that emulates\
;; a floating point instruction.

( define\_insn "muldf3"\
\[ ( set ( match\_operand:DF 0 "general\_operand" "=D" )\
( mult:DF ( match\_operand:DF 1 "general\_operand" "0" )\
( match\_operand:DF 2 "general\_operand" "D" )))\
]\
""\
"\*\
{\
int op0\_is\_a = ( DSP16\_A\_REGNUM == REGNO ( operands\[0] ));\
int op2\_is\_a = ( DSP16\_A\_REGNUM == REGNO ( operands\[2] ));

```
if ( op0_is_a )
{
if ( op2_is_a )
{
    return \"jsr	fmpy_aa\";
}
else
{
    return \"jsr	fmpy_ba\";
}
}
else
{
if ( op2_is_a )
{
    return \"jsr	fmpy_ab\";
}
else
{
    return \"jsr	fmpy_bb\";
}
}
```

}" )

( define\_insn "mulsf3"\
\[ ( set ( match\_operand:SF 0 "general\_operand" "=D" )\
( mult:SF ( match\_operand:SF 1 "general\_operand" "0" )\
( match\_operand:SF 2 "general\_operand" "D" )))\
]\
""\
"\*\
{\
int op0\_is\_a = ( DSP16\_A\_REGNUM == REGNO ( operands\[0] ));\
int op2\_is\_a = ( DSP16\_A\_REGNUM == REGNO ( operands\[2] ));

```
if ( op0_is_a )
{
if ( op2_is_a )
{
    return \"jsr	fmpy_aa\";
}
else
{
    return \"jsr	fmpy_ba\";
}
}
else
{
if ( op2_is_a )
{
    return \"jsr	fmpy_ab\";
}
else
{
    return \"jsr	fmpy_bb\";
}
}
```

}" )

;; We have two shapes for mac+ and two shapes for mac-, because the mult may\
;; turn up as either operand of the plus/minus.

( define\_insn ""\
\[ ( set ( match\_operand:SI 0 "register\_operand" "=D" )\
( plus:SI ( match\_operand:SI 1 "register\_operand" "0" )\
( mult:SI ( match\_operand:SI 2 "register\_operand" "%S" )\
( match\_operand:SI 3 "register\_operand" "R" ))))\
]\
""\
"imac %2,%3,%0" )

( define\_insn ""\
\[ ( set ( match\_operand:SI 0 "register\_operand" "=D" )\
( plus:SI ( mult:SI ( match\_operand:SI 1 "register\_operand" "%S" )\
( match\_operand:SI 2 "register\_operand" "R" ))\
( match\_operand:SI 3 "register\_operand" "0" )))\
]\
""\
"imac %1,%2,%0" )

( define\_insn ""\
\[ ( set ( match\_operand:SI 0 "register\_operand" "=D" )\
( minus:SI ( match\_operand:SI 1 "register\_operand" "0" )\
( mult:SI ( match\_operand:SI 2 "register\_operand" "%S" )\
( match\_operand:SI 3 "register\_operand" "R" ))))\
]\
""\
"neg %0;imac %2,%3,%0;neg %0"

;; we have a quick (breaks calling convention rules) subr. act as an inst.

( define\_insn "muldi3"\
\[ ( set ( match\_operand:DI 0 "register\_operand" "=D" )\
( mult:DI ( match\_operand:DI 1 "register\_operand" "0" )\
( match\_operand:DI 2 "register\_operand" "D" )))\
]\
""\
"\*\
{\
int op0\_is\_a = ( DSP16\_A\_REGNUM == REGNO ( operands\[0] ));\
int op2\_is\_a = ( DSP16\_A\_REGNUM == REGNO ( operands\[2] ));

```
if ( op0_is_a )
{
if ( op2_is_a )
{
    return \"jsr	lmpy_aa\";
}
else
{
    return \"jsr	lmpy_ba\";
}
}
else
{
if ( op2_is_a )
{
    return \"jsr	lmpy_ab\";
}
else
{
    return \"jsr	lmpy_bb\";
}
}
```

}" )

;; normal, boring mults follow.

( define\_insn "mulsi3"\
\[ ( set ( match\_operand:SI 0 "register\_operand" "=D" )\
( mult:SI ( match\_operand:SI 1 "register\_operand" "%S" )\
( match\_operand:SI 2 "register\_operand" "R" )))\
]\
""\
"impy %2,%1,%0" )

( define\_insn "umulsi3"\
\[ ( set ( match\_operand:SI 0 "register\_operand" "=D" )\
( umult:SI ( match\_operand:SI 1 "register\_operand" "%S" )\
( match\_operand:SI 2 "register\_operand" "R" )))\
]\
""\
"impy %2,%1,%0" )

;; We provide quick software emulation via a subroutine that emulates\
;; a floating point instruction.

( define\_insn "divdi3"\
\[ ( set ( match\_operand:DI 0 "register\_operand" "=D" )\
( div:DI ( match\_operand:DI 1 "register\_operand" "0" )\
( match\_operand:DI 2 "register\_operand" "D" )))\
]\
""\
"\*\
{\
int op0\_is\_a = ( DSP16\_A\_REGNUM == REGNO ( operands\[0] ));\
int op2\_is\_a = ( DSP16\_A\_REGNUM == REGNO ( operands\[2] ));

```
if ( op0_is_a )
{
if ( op2_is_a )
{
    return \"jsr	ldiv_aa\";
}
else
{
    return \"jsr	ldiv_ba\";
}
}
else
{
if ( op2_is_a )
{
    return \"jsr	ldiv_ab\";
}
else
{
    return \"jsr	ldiv_bb\";
}
}
```

}" )

( define\_insn "divdf3"\
\[ ( set ( match\_operand:DF 0 "general\_operand" "=D" )\
( div:DF ( match\_operand:DF 1 "general\_operand" "0" )\
( match\_operand:DF 2 "general\_operand" "D" )))\
]\
""\
"\*\
{\
int op0\_is\_a = ( DSP16\_A\_REGNUM == REGNO ( operands\[0] ));\
int op2\_is\_a = ( DSP16\_A\_REGNUM == REGNO ( operands\[2] ));

```
if ( op0_is_a )
{
if ( op2_is_a )
{
    return \"jsr	fdiv_aa\";
}
else
{
    return \"jsr	fdiv_ba\";
}
}
else
{
if ( op2_is_a )
{
    return \"jsr	fdiv_ab\";
}
else
{
    return \"jsr	fdiv_bb\";
}
}
```

}" )

( define\_insn "divsf3"\
\[ ( set ( match\_operand:SF 0 "general\_operand" "=D" )\
( div:SF ( match\_operand:SF 1 "general\_operand" "0" )\
( match\_operand:SF 2 "general\_operand" "D" )))\
]\
""\
"\*\
{\
int op0\_is\_a = ( DSP16\_A\_REGNUM == REGNO ( operands\[0] ));\
int op2\_is\_a = ( DSP16\_A\_REGNUM == REGNO ( operands\[2] ));

```
if ( op0_is_a )
{
if ( op2_is_a )
{
    return \"jsr	fdiv_aa\";
}
else
{
    return \"jsr	fdiv_ba\";
}
}
else
{
if ( op2_is_a )
{
    return \"jsr	fdiv_ab\";
}
else
{
    return \"jsr	fdiv_bb\";
}
}
```

}" )

( define\_expand "divsi3"\
\[ ( set ( match\_operand:SI 0 "register\_operand" "" )\
( div:SI ( match\_operand:SI 1 "register\_operand" "" )\
( match\_operand:SI 2 "register\_operand" "" )))\
]\
""\
"\
{\
emit\_insn (\
gen\_rtx ( PARALLEL, VOIDmode,\
gen\_rtvec ( 3,\
gen\_rtx ( SET, VOIDmode, operands\[0],\
gen\_rtx ( DIV, SImode,\
operands\[1],\
operands\[2] )),\
gen\_rtx ( CLOBBER, VOIDmode,\
gen\_reg\_rtx ( SImode )),\
gen\_rtx ( CLOBBER, VOIDmode,\
gen\_reg\_rtx ( SImode )))));\
DONE;\
}" )

( define\_insn ""\
\[ ( parallel \[ ( set ( match\_operand:SI 0 "register\_operand" "=D" )\
( div:SI ( match\_operand:SI 1 "register\_operand" "0" )\
( match\_operand:SI 2 "register\_operand" "S" )))\
( clobber ( match\_operand:SI 3 "register\_operand" "=D" ))\
( clobber ( match\_operand:SI 4 "register\_operand" "=S" )) ] )\
]\
""\
"\*\
{\
operands\[5] = gen\_label\_rtx ( );

```
return  \"tfr	%0,%3\;abs	%0\;clr	%0	%e0,%4\;move	%4,%h0\;asl	%0\;rep	#$10\;div	%2,%0\;eor	%2,%3\;jpl	%l5\;neg	%0\\n%l5\;move	%h0,%0\";
```

}" )

;;\
;; ...........................................................................\
;;\
;; NEGATIONS\
;;\
;; ...........................................................................\
;;

;; We provide quick software emulation via a subroutine that emulates\
;; a floating point instruction.

( define\_insn "negdf2"\
\[ ( set ( match\_operand:DF 0 "general\_operand" "=D" )\
( neg:DF ( match\_operand:DF 1 "general\_operand" "0" )))\
]\
""\
"\*\
{\
if ( DSP16\_A\_REGNUM == REGNO ( operands\[0] ))\
{\
return "jsr fneg\_a";\
}\
else\
{\
return "jsr fneg\_b";\
}\
}" )

( define\_insn "negsf2"\
\[ ( set ( match\_operand:SF 0 "general\_operand" "=D" )\
( neg:SF ( match\_operand:SF 1 "general\_operand" "0" )))\
]\
""\
"\*\
{\
if ( DSP16\_A\_REGNUM == REGNO ( operands\[0] ))\
{\
return "jsr fneg\_a";\
}\
else\
{\
return "jsr fneg\_b";\
}\
}" )

( define\_insn "negsi2"\
\[ ( set ( match\_operand:SI 0 "register\_operand" "=D" )\
( neg:SI ( match\_operand:SI 1 "register\_operand" "0" )))\
]\
""\
"neg %0" )

( define\_insn "negdi2"\
\[ ( set ( match\_operand:DI 0 "register\_operand" "=D" )\
( neg:DI ( match\_operand:DI 1 "register\_operand" "0" )))\
]\
""\
"neg %0" )

;;\
;; ...........................................................................\
;;\
;; ABSOLUTE VALUES\
;;\
;; ...........................................................................\
;;

( define\_insn "abssi2"\
\[ ( set ( match\_operand:SI 0 "register\_operand" "=D" )\
( abs:SI ( match\_operand:SI 1 "register\_operand" "0" ) ) ) ]\
""\
"abs %0" )

( define\_insn "absdi2"\
\[ ( set ( match\_operand:DI 0 "register\_operand" "=D" )\
( abs:DI ( match\_operand:DI 1 "register\_operand" "0" ) ) ) ]\
""\
"abs %0" )

;;\
;; ...........................................................................\
;;\
;; LOGICAL AND\
;;\
;; ...........................................................................\
;;

( define\_insn "andsi3"\
\[ ( set ( match\_operand:SI 0 "register\_operand" "=D" )\
( and:SI ( match\_operand:SI 1 "register\_operand" "0" )\
( match\_operand:SI 2 "register\_operand" "S" ) ) ) ]\
""\
"and %2,%0;move %e0,%0" )

( define\_insn "anddi3"\
\[ ( set ( match\_operand:DI 0 "register\_operand" "=D" )\
( and:DI ( match\_operand:DI 1 "register\_operand" "0" )\
( match\_operand:DI 2 "register\_operand" "S" ) ) ) ]\
""\
"\*\
{\
RETURN\_DSP ( "and %g2,%0 %h0,@:(r6);move %e0,%h0;move @:(r6),%e0;and %2,%0;move %e0,@:(r6);move %h0,%0;move @:(r6),%h0" );\
}" )

;;\
;; ...........................................................................\
;;\
;; LOGICAL INCLUSIVE OR\
;;\
;; ...........................................................................\
;;

( define\_insn "iorsi3"\
\[ ( set ( match\_operand:SI 0 "register\_operand" "=D" )\
( ior:SI ( match\_operand:SI 1 "register\_operand" "0" )\
( match\_operand:SI 2 "register\_operand" "S" ) ) ) ]\
""\
"or %2,%0;move %e0,%0" )

( define\_insn "iordi3"\
\[ ( set ( match\_operand:DI 0 "register\_operand" "=D" )\
( ior:DI ( match\_operand:DI 1 "register\_operand" "0" )\
( match\_operand:DI 2 "register\_operand" "S" ) ) ) ]\
""\
"\*\
{\
RETURN\_DSP ( "or %g2,%0 %h0,@:(r6);move %e0,%h0;move @:(r6),%e0;or %2,%0;move %e0,@:(r6);move %h0,%0;move @:(r6),%h0" );\
}" )

;;\
;; ...........................................................................\
;;\
;; LOGICAL EXCLUSIVE OR\
;;\
;; ...........................................................................\
;;

( define\_insn "xorsi3"\
\[ ( set ( match\_operand:SI 0 "register\_operand" "=D" )\
( xor:SI ( match\_operand:SI 1 "register\_operand" "0" )\
( match\_operand:SI 2 "register\_operand" "S" ) ) ) ]\
""\
"eor %2,%0;move %e0,%0" )

( define\_insn "xordi3"\
\[ ( set ( match\_operand:DI 0 "register\_operand" "=D" )\
( xor:DI ( match\_operand:DI 1 "register\_operand" "0" )\
( match\_operand:DI 2 "register\_operand" "S" ) ) ) ]\
""\
"\*\
{\
RETURN\_DSP ( "eor %g2,%0 %h0,@:(r6);move %e0,%h0;move @:(r6),%e0;eor %2,%0;move %e0,@:(r6);move %h0,%0;move @:(r6),%h0" );\
}" )

;;\
;; ...........................................................................\
;;\
;; ONE'S COMPLEMENT\
;;\
;; ...........................................................................\
;;

( define\_insn "one\_cmplsi2"\
\[ ( set ( match\_operand:SI 0 "register\_operand" "=D" )\
( not:SI ( match\_operand:SI 1 "register\_operand" "0" )))\
]\
""\
"not %0;move %e0,%0" )

( define\_insn "one\_cmpldi2"\
\[ ( set ( match\_operand:DI 0 "register\_operand" "=D" )\
( not:DI ( match\_operand:DI 1 "register\_operand" "0" )))\
]\
""\
"\*\
{\
RETURN\_DSP ( "not %0 %h0,@:(r6);move %e0,%h0;move @:(r6),%e0;not %0;move %e0,@:(r6);move %h0,%0;move @:(r6),%h0" );\
}" )

;;\
;; ...........................................................................\
;;\
;; ARITHMETIC SHIFTS\
;;\
;; ...........................................................................\
;;

;; note that we preceed each shift insn by an unnamed insn. the unnamed insn\
;; is used to catch shifts with constant shift counts before said constants\
;; are promoted to registers.

( define\_insn ""\
\[ ( set ( match\_operand:SI 0 "register\_operand" "=D" )\
( ashift:SI ( match\_operand:SI 1 "register\_operand" "0" )\
( match\_operand:SI 2 "immediate\_operand" "i" )))\
]\
""\
"\*\
{\
if ( 1 == INTVAL ( operands\[2] ))\
{\
return "asl %0;move %e0,%0";\
}\
else if ( 2 == INTVAL ( operands\[2] ))\
{\
return "asl %0;asl %0;move %e0,%0";\
}\
else\
{\
operands\[3] = copy\_rtx ( operands\[2] );\
INTVAL ( operands\[3] ) &= 0xfff;

```
if ( TARGET_REP )
{
    return \"rep	#%c3\;asl	%0\;move	%e0,%0\";
}
else
{
    operands[4] = gen_label_rtx ( );
    
    return \"do	#%c3,%l4\;asl	%0\\n%l4\;move	%e0,%0\";
}
}
```

}" )

( define\_insn "ashlsi3"\
\[ ( set ( match\_operand:SI 0 "register\_operand" "=D" )\
( ashift:SI ( match\_operand:SI 1 "register\_operand" "0" )\
( match\_operand:SI 2 "general\_operand" "D" )))\
]\
""\
"\*\
{\
operands\[3] = gen\_label\_rtx ( );

```
if ( TARGET_REP )
{
return \"tst	%2\;jeq	%l3\;rep	%2\;asl	%0\;move	%e0,%0\\n%l3\";
}
else
{
operands[4] = gen_label_rtx ( );

return \"tst	%2\;jeq	%l3\;do	%2,%l4\;asl	%0\\n%l4\;move	%e0,%0\\n%l3\";
}
```

}")

( define\_insn ""\
\[ ( set ( match\_operand:SI 0 "register\_operand" "=D" )\
( ashiftrt:SI ( match\_operand:SI 1 "register\_operand" "0" )\
( match\_operand:SI 2 "immediate\_operand" "i" )))\
]\
""\
"\*\
{\
if ( 1 == INTVAL ( operands\[2] ))\
{\
return "move %e0,%0;asr %0;move %e0,%0";\
}\
else if ( 2 == INTVAL ( operands\[2] ))\
{\
return "move %e0,%0;asr %0;asr %0;move %e0,%0";\
}\
else\
{\
operands\[3] = copy\_rtx ( operands\[2] );\
INTVAL ( operands\[3] ) &= 0xfff;

```
if ( TARGET_REP )
{
    return \"move	%e0,%0\;rep	#%c3\;asr	%0\;move	%e0,%0\";
}
else
{
    operands[4] = gen_label_rtx ( );
    
    return \"move	%e0,%0\;do	#%c3,%l4\;asr	%0\\n%l4\;move	%e0,%0\";
}
}
```

}" )

( define\_insn "ashrsi3"\
\[ ( set ( match\_operand:SI 0 "register\_operand" "=D" )\
( ashiftrt:SI ( match\_operand:SI 1 "register\_operand" "0" )\
( match\_operand:SI 2 "general\_operand" "D" )))\
]\
""\
"\*\
{\
operands\[3] = gen\_label\_rtx ( );

```
if ( TARGET_REP )
{
return \"tst	%2\;jeq	%l3\;move	%e0,%0\;rep	%2\;asr	%0\;move	%e0,%0\\n%l3\";
}
else
{
operands[4] = gen_label_rtx ( );

return \"tst	%2\;jeq	%l3\;move	%e0,%0\;do	%2,%l4\;asr	%0\\n%l4\;move	%e0,%0\\n%l3\";
}
```

}")

( define\_insn ""\
\[ ( set ( match\_operand:DI 0 "register\_operand" "=D" )\
( ashift:DI ( match\_operand:DI 1 "register\_operand" "0" )\
( match\_operand:SI 2 "immediate\_operand" "i" )))\
]\
""\
"\*\
{\
if ( 1 == INTVAL ( operands\[2] ))\
{\
return "asl %0";\
}\
else if ( 2 == INTVAL ( operands\[2] ))\
{\
return "asl %0;asl %0";\
}\
else\
{\
operands\[3] = copy\_rtx ( operands\[2] );\
INTVAL ( operands\[3] ) &= 0xfff;

```
if ( TARGET_REP )
{
    return \"rep	#%c3\;asl	%0\";
}
else
{
    operands[4] = gen_label_rtx ( );
    
    return \"do	#%c3,%l4\;asl	%0\\n%l4\";
}
}
```

}" )

( define\_insn "ashldi3"\
\[ ( set ( match\_operand:DI 0 "register\_operand" "=D" )\
( ashift:DI ( match\_operand:DI 1 "register\_operand" "0" )\
( match\_operand:SI 2 "general\_operand" "D" )))\
]\
""\
"\*\
{\
operands\[3] = gen\_label\_rtx ( );

```
if ( TARGET_REP )
{
return \"tst	%d2\;jeq	%l3\;rep	%2\;asl	%0\\n%l3\";
}
else
{
return \"tst	%d2\;jeq	%l3\;do	%2,%l3\;asl	%0\\n%l3\";
}
```

}")

( define\_insn ""\
\[ ( set ( match\_operand:DI 0 "register\_operand" "=D" )\
( ashiftrt:DI ( match\_operand:DI 1 "register\_operand" "0" )\
( match\_operand:SI 2 "immediate\_operand" "i" )))\
]\
""\
"\*\
{\
if ( 1 == INTVAL ( operands\[2] ))\
{\
RETURN\_DSP ( "move %h0,@:(r6);move %e0,%0;move @:(r6),%h0;asr %0" );\
}\
else if ( 2 == INTVAL ( operands\[2] ))\
{\
RETURN\_DSP ( "move %h0,@:(r6);move %e0,%0;move @:(r6),%h0;asl %0;asr %0" );\
}\
else\
{\
operands\[3] = copy\_rtx ( operands\[2] );\
INTVAL ( operands\[3] ) &= 0xfff;

```
if ( TARGET_REP )
{
    RETURN_DSP ( \"move	%h0,@:(r6)\;move	%e0,%0\;move	@:(r6),%h0\;rep	#%c3\;asr	%0\" );
}
else
{
    operands[4] = gen_label_rtx ( );
    
    RETURN_DSP ( \"move	%h0,@:(r6)\;move	%e0,%0\;move	@:(r6),%h0\;do	#%c3,%l4\;asr	%0\\n%l4\" );
}
}
```

}" )

( define\_insn "ashrdi3"\
\[ ( set ( match\_operand:DI 0 "register\_operand" "=D" )\
( ashiftrt:DI ( match\_operand:DI 1 "register\_operand" "0" )\
( match\_operand:SI 2 "general\_operand" "D" )))\
]\
""\
"\*\
{\
operands\[3] = gen\_label\_rtx ( );

```
if ( TARGET_REP )
{
RETURN_DSP ( \"move	%h0,@:(r6)\;move	%e0,%0\;move	@:(r6),%h0\;tst	%d2\;jeq	%l3\;rep	%2\;asr	%0\\n%l3\" );
}
else
{
RETURN_DSP ( \"move	%h0,@:(r6)\;move	%e0,%0\;move	@:(r6),%h0\;tst	%d2\;jeq	%l3\;do	%2,%l3\;asr	%0\\n%l3\" );
}
```

}")

;;\
;; ...........................................................................\
;;\
;; LOGICAL SHIFTS\
;;\
;; ...........................................................................\
;;

;; note that we preceed each shift insn by an unnamed insn. the unnamed insn\
;; is used to catch shifts with constant shift counts before said constants\
;; are promoted to registers.

( define\_insn ""\
\[ ( set ( match\_operand:SI 0 "register\_operand" "=D" )\
( lshift:SI ( match\_operand:SI 1 "register\_operand" "0" )\
( match\_operand:SI 2 "immediate\_operand" "i" )))\
]\
""\
"\*\
{\
if ( 1 == INTVAL ( operands\[2] ))\
{\
return "lsl %0";\
}\
else if ( 2 == INTVAL ( operands\[2] ))\
{\
return "lsl %0;lsl %0";\
}\
else\
{\
operands\[3] = copy\_rtx ( operands\[2] );\
INTVAL ( operands\[3] ) &= 0xfff;

```
if ( TARGET_REP )
{
    return \"rep	#%c3\;lsl	%0\";
}
else
{
    operands[4] = gen_label_rtx ( );
    
    return \"do	#%c3,%l4\;lsl	%0\\n%l4\";
}
}
```

}" )

( define\_insn "lshlsi3"\
\[ ( set ( match\_operand:SI 0 "register\_operand" "=D" )\
( lshift:SI ( match\_operand:SI 1 "register\_operand" "0" )\
( match\_operand:SI 2 "general\_operand" "D" )))\
]\
""\
"\*\
{\
operands\[3] = gen\_label\_rtx ( );

```
if ( TARGET_REP )
{
return \"tst	%2\;jeq	%l3\;rep	%2\;lsl	%0\\n%l3\";
}
else
{
return \"tst	%2\;jeq	%l3\;do	%2,%l3\;lsl	%0\\n%l3\";
}
```

}")

( define\_insn ""\
\[ ( set ( match\_operand:SI 0 "register\_operand" "=D" )\
( lshiftrt:SI ( match\_operand:SI 1 "register\_operand" "0" )\
( match\_operand:SI 2 "immediate\_operand" "i" )))\
]\
""\
"\*\
{\
if ( 1 == INTVAL ( operands\[2] ))\
{\
return "lsr %0";\
}\
else if ( 2 == INTVAL ( operands\[2] ))\
{\
return "lsr %0;lsr %0";\
}\
else\
{\
operands\[3] = copy\_rtx ( operands\[2] );\
INTVAL ( operands\[3] ) &= 0xfff;

```
if ( TARGET_REP )
{
    return \"rep	#%c3\;lsr	%0\";
}
else
{
    operands[4] = gen_label_rtx ( );
    
    return \"do	#%c3,%l4\;lsr	%0\\n%l4\";
}
}
```

}" )

( define\_insn "lshrsi3"\
\[ ( set ( match\_operand:SI 0 "register\_operand" "=D" )\
( lshiftrt:SI ( match\_operand:SI 1 "register\_operand" "0" )\
( match\_operand:SI 2 "general\_operand" "D" )))\
]\
""\
"\*\
{\
operands\[3] = gen\_label\_rtx ( );

```
if ( TARGET_REP )
{
return \"tst	%2\;jeq	%l3\;rep	%2\;lsr	%0\\n%l3\";
}
else
{
return \"tst	%2\;jeq	%l3\;do	%2,%l3\;lsr	%0\\n%l3\";
}
```

}")

( define\_insn ""\
\[ ( set ( match\_operand:DI 0 "register\_operand" "=D" )\
( lshift:DI ( match\_operand:DI 1 "register\_operand" "0" )\
( match\_operand:SI 2 "immediate\_operand" "i" )))\
]\
""\
"\*\
{\
if ( 1 == INTVAL ( operands\[2] ))\
{\
return "asl %0";\
}\
else if ( 2 == INTVAL ( operands\[2] ))\
{\
return "asl %0;asl %0";\
}\
else\
{\
operands\[3] = copy\_rtx ( operands\[2] );\
INTVAL ( operands\[3] ) &= 0xfff;

```
if ( TARGET_REP )
{
    return \"rep	#%c3\;asl	%0\";
}
else
{	
    operands[4] = gen_label_rtx ( );
    
    return \"do	#%c3,%l4\;asl	%0\\n%l4\";
}
}
```

}" )

( define\_insn "lshldi3"\
\[ ( set ( match\_operand:DI 0 "register\_operand" "=D" )\
( lshift:DI ( match\_operand:DI 1 "register\_operand" "0" )\
( match\_operand:SI 2 "general\_operand" "D" )))\
]\
""\
"\*\
{\
operands\[3] = gen\_label\_rtx ( );

```
if ( TARGET_REP )
{
return \"tst	%d2\;jeq	%l3\;rep	%2\;asl	%0\\n%l3\";
}
else
{
return \"tst	%d2\;jeq	%l3\;do	%2,%l3\;asl	%0\\n%l3\";
}
```

}")

( define\_insn ""\
\[ ( set ( match\_operand:DI 0 "register\_operand" "=D" )\
( lshiftrt:DI ( match\_operand:DI 1 "register\_operand" "0" )\
( match\_operand:SI 2 "immediate\_operand" "i" )))\
]\
""\
"\*\
{\
if ( 1 == INTVAL ( operands\[2] ))\
{\
return "move #0,%k0;asr %0";\
}\
else if ( 2 == INTVAL ( operands\[2] ))\
{\
return "move #0,%k0;asr %0;asr %0";\
}\
else\
{\
operands\[3] = copy\_rtx ( operands\[2] );\
INTVAL ( operands\[3] ) &= 0xfff;

```
if ( TARGET_REP )
{
    return \"move	#0,%k0\;rep	#%c3\;asr	%0\";
}
else
{
    operands[4] = gen_label_rtx ( );
    
    return \"move	#0,%k0\;do	#%c3,%l4\;asr	%0\\n%l4\";
}
}
```

}" )

( define\_insn "lshrdi3"\
\[ ( set ( match\_operand:DI 0 "register\_operand" "=D" )\
( lshiftrt:DI ( match\_operand:DI 1 "register\_operand" "0" )\
( match\_operand:SI 2 "general\_operand" "D" )))\
]\
""\
"\*\
{\
operands\[3] = gen\_label\_rtx ( );

```
if ( TARGET_REP )
{
return \"tst	%d2\;jeq	%l3\;move	#0,%k0\;rep	%2\;asr	%0\\n%l3\";
}
else
{
return \"tst	%d2\;jeq	%l3\;move	#0,%k0\;do	%2,%l3\;asr	%0\\n%l3\";
}
```

}")

;;\
;; ...........................................................................\
;;\
;; ROTATIONS\
;;\
;; ...........................................................................\
;;

;; note that we preceed each shift insn by an unnamed insn. the unnamed insn\
;; is used to catch shifts with constant shift counts before said constants\
;; are promoted to registers.

( define\_insn ""\
\[ ( set ( match\_operand:SI 0 "register\_operand" "=D" )\
( rotate:SI ( match\_operand:SI 1 "register\_operand" "0" )\
( match\_operand:SI 2 "immediate\_operand" "i" )))\
]\
""\
"\*\
{\
if ( 1 == INTVAL ( operands\[2] ))\
{\
return "rol %0";\
}\
else if ( 2 == INTVAL ( operands\[2] ))\
{\
return "rol %0;rol %0";\
}\
else\
{\
operands\[3] = copy\_rtx ( operands\[2] );\
INTVAL ( operands\[3] ) &= 0xfff;

```
if ( TARGET_REP )
{
    return \"rep	#%c3\;rol	%0\";
}
else
{
    operands[4] = gen_label_rtx ( );
    
    return \"do	#%c3,%l4\;rol	%0\\n%l4\";
}
}
```

}" )

( define\_insn "rotlsi3"\
\[ ( set ( match\_operand:SI 0 "register\_operand" "=D" )\
( rotate:SI ( match\_operand:SI 1 "register\_operand" "0" )\
( match\_operand:SI 2 "general\_operand" "D" )))\
]\
""\
"\*\
{\
operands\[3] = gen\_label\_rtx ( );

```
if ( TARGET_REP )
{
return \"tst	%2\;jeq	%l3\;rep	%2\;rol	%0\\n%l3\";
}
else
{
return \"tst	%2\;jeq	%l3\;do	%2,%l3\;rol	%0\\n%l3\";
}
```

}")

( define\_insn ""\
\[ ( set ( match\_operand:SI 0 "register\_operand" "=D" )\
( rotatert:SI ( match\_operand:SI 1 "register\_operand" "0" )\
( match\_operand:SI 2 "immediate\_operand" "i" )))\
]\
""\
"\*\
{\
if ( 1 == INTVAL ( operands\[2] ))\
{\
return "ror %0";\
}\
else if ( 2 == INTVAL ( operands\[2] ))\
{\
return "ror %0;ror %0";\
}\
else\
{\
operands\[3] = copy\_rtx ( operands\[2] );\
INTVAL ( operands\[3] ) &= 0xfff;

```
if ( TARGET_REP )
{
    return \"rep	#%c3\;ror	%0\";
}
else
{
    operands[4] = gen_label_rtx ( );
    
    return \"do	#%c3,%l4\;ror	%0\\n%l4\";
}
}
```

}" )

( define\_insn "rotrsi3"\
\[ ( set ( match\_operand:SI 0 "register\_operand" "=D" )\
( rotatert:SI ( match\_operand:SI 1 "register\_operand" "0" )\
( match\_operand:SI 2 "general\_operand" "D" )))\
]\
""\
"\*\
{\
operands\[3] = gen\_label\_rtx ( );

```
if ( TARGET_REP )
{
return \"tst	%2\;jeq	%l3\;rep	%2\;ror	%0\\n%l3\";
}
else
{
return \"tst	%2\;jeq	%l3\;do	%2,%l3\;ror	%0\\n%l3\";
}
```

}")

;;\
;; ...........................................................................\
;;\
;; COMPARISONS\
;;\
;; ...........................................................................\
;;

;; We provide quick software emulation via a subroutine that emulates\
;; a floating point instruction.

( define\_insn "cmpdf"\
\[ ( set ( cc0 )\
( compare ( match\_operand:DF 0 "register\_operand" "D" )\
( match\_operand:DF 1 "register\_operand" "D" )))\
]\
""\
"\*\
{\
int op0\_is\_a = ( DSP16\_A\_REGNUM == REGNO ( operands\[0] ));\
int op1\_is\_a = ( DSP16\_A\_REGNUM == REGNO ( operands\[1] ));

```
if ( op0_is_a )
{
if ( op1_is_a )
{
    return \"jsr	fcmp_aa\";
}
else
{
    return \"jsr	fcmp_ba\";
}
}
else
{
if ( op1_is_a )
{
    return \"jsr	fcmp_ab\";
}
else
{
    return \"jsr	fcmp_bb\";
}
}
```

}" )

( define\_insn "cmpsf"\
\[ ( set ( cc0 )\
( compare ( match\_operand:SF 0 "register\_operand" "D" )\
( match\_operand:SF 1 "register\_operand" "D" )))\
]\
""\
"\*\
{\
int op0\_is\_a = ( DSP16\_A\_REGNUM == REGNO ( operands\[0] ));\
int op1\_is\_a = ( DSP16\_A\_REGNUM == REGNO ( operands\[1] ));

```
if ( op0_is_a )
{
if ( op1_is_a )
{
    return \"jsr	fcmp_aa\";
}
else
{
    return \"jsr	fcmp_ba\";
}
}
else
{
if ( op1_is_a )
{
    return \"jsr	fcmp_ab\";
}
else
{
    return \"jsr	fcmp_bb\";
}
}
```

}" )

( define\_insn "cmppsi"\
\[ ( set ( cc0 )\
( compare ( match\_operand:PSI 0 "register\_operand" "D" )\
( match\_operand:PSI 1 "register\_operand" "D" )))\
]\
""\
"\*\
{\
extern enum mdep\_cc\_info next\_cc\_use ( );\
extern rtx next\_cc\_relevancy ( );\
rtx relevant\_insn, relevant\_body;

```
/* sometimes this compiler manages attempt ( compare ( reg x ) ( reg x )).
   the condition codes implied by this are obvious. we peek down the insn
   chain and discover the next_cc0_relevancy ( ). if we discover
   that the ccs are being set gratuitiously, we ignore this compare, 
   otherwise we change the cc0 consumer to be a constant load in the case
   of an Scond or a hard branch or nop in the case of a Bcond. */

if ( REGNO ( operands[0] ) == REGNO ( operands[1] ))
{
relevant_insn = next_cc_relevancy ( insn );
relevant_body = PATTERN ( relevant_insn );

if ( JUMP_INSN == GET_CODE ( relevant_insn ))
{
    if (( IF_THEN_ELSE == GET_CODE ( relevant_body =
				    XEXP ( relevant_body, 1 ))) &&
	( CONST_INT != 
	 GET_CODE ( XEXP ( relevant_body, 0 ))))
    {
	if (( GEU == GET_CODE ( XEXP ( relevant_body, 0 ))) ||
	    ( GE == GET_CODE ( XEXP ( relevant_body, 0 ))) ||
	    ( LEU == GET_CODE ( XEXP ( relevant_body, 0 ))) ||
	    ( LE == GET_CODE ( XEXP ( relevant_body, 0 ))) ||
	    ( EQU == GET_CODE ( XEXP ( relevant_body, 0 ))) ||
	    ( EQ == GET_CODE ( XEXP ( relevant_body, 0 ))))
	{
	    /* change ineq into const1_rtx because x == x always. */

	    if ( pc_rtx == XEXP ( XEXP ( PATTERN ( relevant_insn ),
					1 ), 1 ))
	    {
		delete_insn ( relevant_insn );
	    }
	    else
	    {
		INSN_CODE ( relevant_insn ) = -1;

		XEXP ( PATTERN ( relevant_insn ), 1 ) =
		    XEXP ( XEXP ( PATTERN ( relevant_insn ), 1 ), 1 );
	    }
	}
	else
	{
	    /* otherwise change ineq into const0_rtx. */
	    
	    if ( pc_rtx == XEXP ( XEXP ( PATTERN ( relevant_insn ),
					1 ), 1 ))
	    {
		INSN_CODE ( relevant_insn ) = -1;

		XEXP ( PATTERN ( relevant_insn ), 1 ) =
		    XEXP ( XEXP ( PATTERN ( relevant_insn ), 1 ), 2 );
	    }
	    else
	    {
		delete_insn ( relevant_insn );
	    }
	}
    }
}
else if (( INSN == GET_CODE ( relevant_insn )) &&
	 ( PARALLEL == GET_CODE ( relevant_body )) &&
	 ( SET == GET_CODE ( relevant_body = 
			    XVECEXP ( relevant_body, 0, 0 ))) &&
	 ( 0 == strcmp ( \"ee\",
			GET_RTX_FORMAT ( GET_CODE ( XEXP ( relevant_body, 1 ))))) &&
	 ( cc0_rtx == XEXP ( XEXP ( relevant_body, 1 ), 0 )))
{
    if (( GEU == GET_CODE ( XEXP ( relevant_body, 1 ))) ||
	( GE == GET_CODE ( XEXP ( relevant_body, 1 ))) ||
	( LEU == GET_CODE ( XEXP ( relevant_body, 1 ))) ||
	( LE == GET_CODE ( XEXP ( relevant_body, 1 ))) ||
	( EQU == GET_CODE ( XEXP ( relevant_body, 1 ))) ||
	( EQ == GET_CODE ( XEXP ( relevant_body, 1 ))))
    {
	/* change ineq into const1_rtx because x == x always. */

	XEXP ( relevant_body, 1 ) = const1_rtx;
    }
    else 
    {
	/* otherwise change ineq into const0_rtx. */

	XEXP ( relevant_body, 1 ) = const0_rtx;
    }
    INSN_CODE ( relevant_insn ) = -1;
}
return \"\";
}

if ( CC_UNSIGNED == next_cc_use ( insn ))
{
cc_status.mdep = CC_UNSIGNED;
return \"move	#0,%k0\;move	#0,%k1\;cmp	%1,%0\";
}
else
{
cc_status.mdep = CC_SIGNED;
return \"cmp	%1,%0\";
}
```

}" )

( define\_insn ""\
\[ ( set ( cc0 )\
( compare ( match\_operand:SI 0 "register\_operand" "D" )\
( match\_operand:SI 1 "register\_operand" "D" )))\
]\
"UNSIGNED\_COMPARE\_P ( insn )"\
"\*\
{\
extern enum mdep\_cc\_info next\_cc\_use ( );\
extern rtx next\_cc\_relevancy ( );\
rtx relevant\_insn, relevant\_body;

```
/* sometimes this compiler manages attempt ( compare ( reg x ) ( reg x )).
   the condition codes implied by this are obvious. we peek down the insn
   chain and discover the next_cc0_relevancy ( ). if we discover
   that the ccs are being set gratuitiously, we ignore this compare, 
   otherwise we change the cc0 consumer to be a constant load in the case
   of an Scond or a hard branch or nop in the case of a Bcond. */

if ( REGNO ( operands[0] ) == REGNO ( operands[1] ))
{
relevant_insn = next_cc_relevancy ( insn );
relevant_body = PATTERN ( relevant_insn );

if ( JUMP_INSN == GET_CODE ( relevant_insn ))
{
    if (( IF_THEN_ELSE == GET_CODE ( relevant_body =
				    XEXP ( relevant_body, 1 ))) &&
	( CONST_INT != 
	 GET_CODE ( XEXP ( relevant_body, 0 ))))
    {
	if (( GEU == GET_CODE ( XEXP ( relevant_body, 0 ))) ||
	    ( GE == GET_CODE ( XEXP ( relevant_body, 0 ))) ||
	    ( LEU == GET_CODE ( XEXP ( relevant_body, 0 ))) ||
	    ( LE == GET_CODE ( XEXP ( relevant_body, 0 ))) ||
	    ( EQU == GET_CODE ( XEXP ( relevant_body, 0 ))) ||
	    ( EQ == GET_CODE ( XEXP ( relevant_body, 0 ))))
	{
	    /* change ineq into const1_rtx because x == x always. */

	    if ( pc_rtx == XEXP ( XEXP ( PATTERN ( relevant_insn ),
					1 ), 1 ))
	    {
		delete_insn ( relevant_insn );
	    }
	    else
	    {
		INSN_CODE ( relevant_insn ) = -1;

		XEXP ( PATTERN ( relevant_insn ), 1 ) =
		    XEXP ( XEXP ( PATTERN ( relevant_insn ), 1 ), 1 );
	    }
	}
	else
	{
	    /* otherwise change ineq into const0_rtx. */
	    
	    if ( pc_rtx == XEXP ( XEXP ( PATTERN ( relevant_insn ),
					1 ), 1 ))
	    {
		INSN_CODE ( relevant_insn ) = -1;

		XEXP ( PATTERN ( relevant_insn ), 1 ) =
		    XEXP ( XEXP ( PATTERN ( relevant_insn ), 1 ), 2 );
	    }
	    else
	    {
		delete_insn ( relevant_insn );
	    }
	}
    }
}
else if (( INSN == GET_CODE ( relevant_insn )) &&
	 ( PARALLEL == GET_CODE ( relevant_body )) &&
	 ( SET == GET_CODE ( relevant_body = 
			    XVECEXP ( relevant_body, 0, 0 ))) &&
	 ( 0 == strcmp ( \"ee\",
			GET_RTX_FORMAT ( GET_CODE ( XEXP ( relevant_body, 1 ))))) &&
	 ( cc0_rtx == XEXP ( XEXP ( relevant_body, 1 ), 0 )))
{
    if (( GEU == GET_CODE ( XEXP ( relevant_body, 1 ))) ||
	( GE == GET_CODE ( XEXP ( relevant_body, 1 ))) ||
	( LEU == GET_CODE ( XEXP ( relevant_body, 1 ))) ||
	( LE == GET_CODE ( XEXP ( relevant_body, 1 ))) ||
	( EQU == GET_CODE ( XEXP ( relevant_body, 1 ))) ||
	( EQ == GET_CODE ( XEXP ( relevant_body, 1 ))))
    {
	/* change ineq into const1_rtx because x == x always. */

	XEXP ( relevant_body, 1 ) = const1_rtx;
    }
    else 
    {
	/* otherwise change ineq into const0_rtx. */

	XEXP ( relevant_body, 1 ) = const0_rtx;
    }
    INSN_CODE ( relevant_insn ) = -1;
}
return \"\";
}

cc_status.mdep = CC_UNSIGNED;

return \"move	#0,%k0\;move	#0,%k1\;cmp	%1,%0\";
```

}" )

( define\_insn "cmpsi"\
\[ ( set ( cc0 )\
( compare ( match\_operand:SI 0 "register\_operand" "D" )\
( match\_operand:SI 1 "register\_operand" "_&#x53;_&#x44;" )))\
]\
""\
"\*\
{\
extern enum mdep\_cc\_info next\_cc\_use ( );\
extern rtx next\_cc\_relevancy ( );\
rtx relevant\_insn, relevant\_body;

```
/* sometimes this compiler manages attempt ( compare ( reg x ) ( reg x )).
   the condition codes implied by this are obvious. we peek down the insn
   chain and discover the next_cc0_relevancy ( ). if we discover
   that the ccs are being set gratuitiously, we ignore this compare, 
   otherwise we change the cc0 consumer to be a constant load in the case
   of an Scond or a hard branch or nop in the case of a Bcond. */

if ( REGNO ( operands[0] ) == REGNO ( operands[1] ))
{
relevant_insn = next_cc_relevancy ( insn );
relevant_body = PATTERN ( relevant_insn );

if ( JUMP_INSN == GET_CODE ( relevant_insn ))
{
    if (( IF_THEN_ELSE == GET_CODE ( relevant_body =
				    XEXP ( relevant_body, 1 ))) &&
	( CONST_INT != 
	 GET_CODE ( XEXP ( relevant_body, 0 ))))
    {
	if (( GEU == GET_CODE ( XEXP ( relevant_body, 0 ))) ||
	    ( GE == GET_CODE ( XEXP ( relevant_body, 0 ))) ||
	    ( LEU == GET_CODE ( XEXP ( relevant_body, 0 ))) ||
	    ( LE == GET_CODE ( XEXP ( relevant_body, 0 ))) ||
	    ( EQU == GET_CODE ( XEXP ( relevant_body, 0 ))) ||
	    ( EQ == GET_CODE ( XEXP ( relevant_body, 0 ))))
	{
	    /* change ineq into const1_rtx because x == x always. */

	    if ( pc_rtx == XEXP ( XEXP ( PATTERN ( relevant_insn ),
					1 ), 1 ))
	    {
		delete_insn ( relevant_insn );
	    }
	    else
	    {
		INSN_CODE ( relevant_insn ) = -1;

		XEXP ( PATTERN ( relevant_insn ), 1 ) =
		    XEXP ( XEXP ( PATTERN ( relevant_insn ), 1 ), 1 );
	    }
	}
	else
	{
	    /* otherwise change ineq into const0_rtx. */
	    
	    if ( pc_rtx == XEXP ( XEXP ( PATTERN ( relevant_insn ),
					1 ), 1 ))
	    {
		INSN_CODE ( relevant_insn ) = -1;

		XEXP ( PATTERN ( relevant_insn ), 1 ) =
		    XEXP ( XEXP ( PATTERN ( relevant_insn ), 1 ), 2 );
	    }
	    else
	    {
		delete_insn ( relevant_insn );
	    }
	}
    }
}
else if (( INSN == GET_CODE ( relevant_insn )) &&
	 ( PARALLEL == GET_CODE ( relevant_body )) &&
	 ( SET == GET_CODE ( relevant_body = 
			    XVECEXP ( relevant_body, 0, 0 ))) &&
	 ( 0 == strcmp ( \"ee\",
			GET_RTX_FORMAT ( GET_CODE ( XEXP ( relevant_body, 1 ))))) &&
	 ( cc0_rtx == XEXP ( XEXP ( relevant_body, 1 ), 0 )))
{
    if (( GEU == GET_CODE ( XEXP ( relevant_body, 1 ))) ||
	( GE == GET_CODE ( XEXP ( relevant_body, 1 ))) ||
	( LEU == GET_CODE ( XEXP ( relevant_body, 1 ))) ||
	( LE == GET_CODE ( XEXP ( relevant_body, 1 ))) ||
	( EQU == GET_CODE ( XEXP ( relevant_body, 1 ))) ||
	( EQ == GET_CODE ( XEXP ( relevant_body, 1 ))))
    {
	/* change ineq into const1_rtx because x == x always. */

	XEXP ( relevant_body, 1 ) = const1_rtx;
    }
    else 
    {
	/* otherwise change ineq into const0_rtx. */

	XEXP ( relevant_body, 1 ) = const0_rtx;
    }
    INSN_CODE ( relevant_insn ) = -1;
}
return \"\";
}    

cc_status.mdep = CC_SIGNED;

return \"cmp	%1,%0\";
```

}" )

( define\_insn ""\
\[ ( set ( cc0 )\
( compare ( match\_operand:DI 0 "register\_operand" "D" )\
( match\_operand:DI 1 "register\_operand" "D" )))\
]\
"UNSIGNED\_COMPARE\_P( insn )"\
"\*\
{\
extern enum mdep\_cc\_info next\_cc\_use ( );\
extern rtx next\_cc\_relevancy ( );\
rtx relevant\_insn, relevant\_body;

```
/* sometimes this compiler manages attempt ( compare ( reg x ) ( reg x )).
   the condition codes implied by this are obvious. we peek down the insn
   chain and discover the next_cc0_relevancy ( ). if we discover
   that the ccs are being set gratuitiously, we ignore this compare, 
   otherwise we change the cc0 consumer to be a constant load in the case
   of an Scond or a hard branch or nop in the case of a Bcond. */

if ( REGNO ( operands[0] ) == REGNO ( operands[1] ))
{
relevant_insn = next_cc_relevancy ( insn );
relevant_body = PATTERN ( relevant_insn );

if ( JUMP_INSN == GET_CODE ( relevant_insn ))
{
    if (( IF_THEN_ELSE == GET_CODE ( relevant_body =
				    XEXP ( relevant_body, 1 ))) &&
	( CONST_INT != 
	 GET_CODE ( XEXP ( relevant_body, 0 ))))
    {
	if (( GEU == GET_CODE ( XEXP ( relevant_body, 0 ))) ||
	    ( GE == GET_CODE ( XEXP ( relevant_body, 0 ))) ||
	    ( LEU == GET_CODE ( XEXP ( relevant_body, 0 ))) ||
	    ( LE == GET_CODE ( XEXP ( relevant_body, 0 ))) ||
	    ( EQU == GET_CODE ( XEXP ( relevant_body, 0 ))) ||
	    ( EQ == GET_CODE ( XEXP ( relevant_body, 0 ))))
	{
	    /* change ineq into const1_rtx because x == x always. */

	    if ( pc_rtx == XEXP ( XEXP ( PATTERN ( relevant_insn ),
					1 ), 1 ))
	    {
		delete_insn ( relevant_insn );
	    }
	    else
	    {
		INSN_CODE ( relevant_insn ) = -1;

		XEXP ( PATTERN ( relevant_insn ), 1 ) =
		    XEXP ( XEXP ( PATTERN ( relevant_insn ), 1 ), 1 );
	    }
	}
	else
	{
	    /* otherwise change ineq into const0_rtx. */
	    
	    if ( pc_rtx == XEXP ( XEXP ( PATTERN ( relevant_insn ),
					1 ), 1 ))
	    {
		INSN_CODE ( relevant_insn ) = -1;

		XEXP ( PATTERN ( relevant_insn ), 1 ) =
		    XEXP ( XEXP ( PATTERN ( relevant_insn ), 1 ), 2 );
	    }
	    else
	    {
		delete_insn ( relevant_insn );
	    }
	}
    }
}
else if (( INSN == GET_CODE ( relevant_insn )) &&
	 ( PARALLEL == GET_CODE ( relevant_body )) &&
	 ( SET == GET_CODE ( relevant_body = 
			    XVECEXP ( relevant_body, 0, 0 ))) &&
	 ( 0 == strcmp ( \"ee\",
			GET_RTX_FORMAT ( GET_CODE ( XEXP ( relevant_body, 1 ))))) &&
	 ( cc0_rtx == XEXP ( XEXP ( relevant_body, 1 ), 0 )))
{
    if (( GEU == GET_CODE ( XEXP ( relevant_body, 1 ))) ||
	( GE == GET_CODE ( XEXP ( relevant_body, 1 ))) ||
	( LEU == GET_CODE ( XEXP ( relevant_body, 1 ))) ||
	( LE == GET_CODE ( XEXP ( relevant_body, 1 ))) ||
	( EQU == GET_CODE ( XEXP ( relevant_body, 1 ))) ||
	( EQ == GET_CODE ( XEXP ( relevant_body, 1 ))))
    {
	/* change ineq into const1_rtx because x == x always. */

	XEXP ( relevant_body, 1 ) = const1_rtx;
    }
    else 
    {
	/* otherwise change ineq into const0_rtx. */

	XEXP ( relevant_body, 1 ) = const0_rtx;
    }
    INSN_CODE ( relevant_insn ) = -1;
}
return \"\";
}

cc_status.mdep = CC_UNSIGNED;

return \"move	#0,%k0\;move	#0,%k1\;cmp	%1,%0\";
```

}" )

( define\_insn "cmpdi"\
\[ ( set ( cc0 )\
( compare ( match\_operand:DI 0 "register\_operand" "D" )\
( match\_operand:DI 1 "register\_operand" "D" )))\
]\
""\
"\*\
{\
extern enum mdep\_cc\_info next\_cc\_use ( );\
extern rtx next\_cc\_relevancy ( );\
rtx relevant\_insn, relevant\_body;

```
/* sometimes this compiler manages attempt ( compare ( reg x ) ( reg x )).
   the condition codes implied by this are obvious. we peek down the insn
   chain and discover the next_cc0_relevancy ( ). if we discover
   that the ccs are being set gratuitiously, we ignore this compare, 
   otherwise we change the cc0 consumer to be a constant load in the case
   of an Scond or a hard branch or nop in the case of a Bcond. */

if ( REGNO ( operands[0] ) == REGNO ( operands[1] ))
{
relevant_insn = next_cc_relevancy ( insn );
relevant_body = PATTERN ( relevant_insn );

if ( JUMP_INSN == GET_CODE ( relevant_insn ))
{
    if (( IF_THEN_ELSE == GET_CODE ( relevant_body =
				    XEXP ( relevant_body, 1 ))) &&
	( CONST_INT != 
	 GET_CODE ( XEXP ( relevant_body, 0 ))))
    {
	if (( GEU == GET_CODE ( XEXP ( relevant_body, 0 ))) ||
	    ( GE == GET_CODE ( XEXP ( relevant_body, 0 ))) ||
	    ( LEU == GET_CODE ( XEXP ( relevant_body, 0 ))) ||
	    ( LE == GET_CODE ( XEXP ( relevant_body, 0 ))) ||
	    ( EQU == GET_CODE ( XEXP ( relevant_body, 0 ))) ||
	    ( EQ == GET_CODE ( XEXP ( relevant_body, 0 ))))
	{
	    /* change ineq into const1_rtx because x == x always. */

	    if ( pc_rtx == XEXP ( XEXP ( PATTERN ( relevant_insn ),
					1 ), 1 ))
	    {
		delete_insn ( relevant_insn );
	    }
	    else
	    {
		INSN_CODE ( relevant_insn ) = -1;

		XEXP ( PATTERN ( relevant_insn ), 1 ) =
		    XEXP ( XEXP ( PATTERN ( relevant_insn ), 1 ), 1 );
	    }
	}
	else
	{
	    /* otherwise change ineq into const0_rtx. */
	    
	    if ( pc_rtx == XEXP ( XEXP ( PATTERN ( relevant_insn ),
					1 ), 1 ))
	    {
		INSN_CODE ( relevant_insn ) = -1;

		XEXP ( PATTERN ( relevant_insn ), 1 ) =
		    XEXP ( XEXP ( PATTERN ( relevant_insn ), 1 ), 2 );
	    }
	    else
	    {
		delete_insn ( relevant_insn );
	    }
	}
    }
}
else if (( INSN == GET_CODE ( relevant_insn )) &&
	 ( PARALLEL == GET_CODE ( relevant_body )) &&
	 ( SET == GET_CODE ( relevant_body = 
			    XVECEXP ( relevant_body, 0, 0 ))) &&
	 ( 0 == strcmp ( \"ee\",
			GET_RTX_FORMAT ( GET_CODE ( XEXP ( relevant_body, 1 ))))) &&
	 ( cc0_rtx == XEXP ( XEXP ( relevant_body, 1 ), 0 )))
{
    if (( GEU == GET_CODE ( XEXP ( relevant_body, 1 ))) ||
	( GE == GET_CODE ( XEXP ( relevant_body, 1 ))) ||
	( LEU == GET_CODE ( XEXP ( relevant_body, 1 ))) ||
	( LE == GET_CODE ( XEXP ( relevant_body, 1 ))) ||
	( EQU == GET_CODE ( XEXP ( relevant_body, 1 ))) ||
	( EQ == GET_CODE ( XEXP ( relevant_body, 1 ))))
    {
	/* change ineq into const1_rtx because x == x always. */

	XEXP ( relevant_body, 1 ) = const1_rtx;
    }
    else 
    {
	/* otherwise change ineq into const0_rtx. */

	XEXP ( relevant_body, 1 ) = const0_rtx;
    }
    INSN_CODE ( relevant_insn ) = -1;
}
return \"\";
}

cc_status.mdep = CC_SIGNED;

return \"cmp	%1,%0\";
```

}" )

;;\
;; ...........................................................................\
;;\
;; TESTS\
;;\
;; ...........................................................................\
;;

( define\_insn "tstpsi"\
\[ ( set ( cc0 )\
( match\_operand:PSI 0 "register\_operand" "D" )) ]\
""\
"\*\
{\
if ( CC\_UNSIGNED == ( cc\_status.mdep = next\_cc\_use ( insn )))\
{\
return "move #0,%k0;tst %0";\
}\
else\
{\
return "tst %0";\
}\
}" )

( define\_insn "tstsi"\
\[ ( set ( cc0 )\
( match\_operand:SI 0 "register\_operand" "D" )) ]\
""\
"\*\
{\
if ( CC\_UNSIGNED == ( cc\_status.mdep = next\_cc\_use ( insn )))\
{\
return "move #0,%k0;tst %0";\
}\
else\
{\
return "tst %0";\
}\
}" )

( define\_insn "tstdi"\
\[ ( set ( cc0 )\
( match\_operand:DI 0 "general\_operand" "D" )) ]\
""\
"\*\
{\
if ( CC\_UNSIGNED == ( cc\_status.mdep = next\_cc\_use ( insn )))\
{\
return "move #0,%k0;tst %0";\
}\
else\
{\
return "tst %0";\
}\
}" )

;;\
;; ...........................................................................\
;;\
;; SCALAR CONVERSIONS USING TRUNCATION\
;;\
;; ...........................................................................\
;;

( define\_insn "truncsipsi2"\
\[ ( set ( match\_operand:PSI 0 "register\_operand" "=A" )\
( truncate:PSI\
( match\_operand:SI 1 "register\_operand" "=_&#x53;_&#x44;" )))\
]\
""\
"move %e1,%0" )

( define\_insn "truncdisi2"\
\[ ( set ( match\_operand:SI 0 "general\_operand" "=_&#x53;_&#x44;" )\
( truncate:SI\
( match\_operand:DI 1 "general\_operand" "_&#x53;_&#x44;" )))\
]\
""\
"\*\
{\
switch ( which\_alternative )\
{\
case 0:\
if ( IS\_SRC\_OR\_MPY\_P ( REGNO ( operands\[1] )))\
{\
return "move %g1,%0";\
}\
else\
{\
return "move %h1,%0";\
}\
}\
}")

;;\
;; ...........................................................................\
;;\
;; SCALAR CONVERSIONS USING ZERO EXTENSION\
;;\
;; ...........................................................................\
;;

( define\_expand "zero\_extendqisi2"\
\[ ( set ( match\_operand:SI 0 "register\_operand" "" )\
( zero\_extend:SI\
( match\_operand:QI 1 "register\_operand" "" )))\
]\
""\
"\
{\
if ( SUBREG == GET\_CODE ( operands\[1] ))\
{\
operands\[1] = copy\_rtx ( SUBREG\_REG ( operands\[1] ));\
}\
else\
{\
operands\[1] = copy\_rtx ( operands\[1] );\
}\
operands\[1] = gen\_rtx ( SUBREG, SImode, operands\[1], 0 );

```
emit_insn ( gen_rtx ( SET, VOIDmode, operands[0], operands[1] ));

DONE;
```

}" )

( define\_insn "zero\_extendpsisi2"\
\[ ( set ( match\_operand:SI 0 "general\_operand" "=_&#x44;_&#x53;" )\
( zero\_extend:SI\
( match\_operand:PSI 1 "general\_operand" "_&#x41;_&#x44;_S" )))_\
_]_\
_""_\
_"_\
{\
/\* we may want to add mem-ref as an opt. later. \*/

```
if ( REGNO ( operands[0] ) == REGNO ( operands[1] ))
{
return \"\";
}

return \"move	%1,%0\";
```

}")

( define\_insn "zero\_extendsidi2"\
\[ ( set ( match\_operand:DI 0 "general\_operand" "=&_&#x53;_&#x44;" )\
( zero\_extend:DI\
( match\_operand:SI 1 "general\_operand" "_&#x53;_&#x44;" )))\
]\
""\
"\*\
{\
if ( DST\_REGS == REGNO\_REG\_CLASS ( REGNO ( operands\[0] )))\
{\
return "clr %0;move %1,%h0";\
}\
else\
{\
return "move %1,%0;move #0,%g0";\
}\
}")

;;\
;; ...........................................................................\
;;\
;; SCALAR CONVERSIONS USING SIGN EXTENSION\
;;\
;; ...........................................................................\
;;

( define\_expand "extendqisi2"\
\[ ( set ( match\_operand:SI 0 "register\_operand" "" )\
( sign\_extend:SI\
( match\_operand:QI 1 "register\_operand" "" )))\
]\
""\
"\
{\
if ( SUBREG == GET\_CODE ( operands\[1] ))\
{\
operands\[1] = copy\_rtx ( SUBREG\_REG ( operands\[1] ));\
}\
else\
{\
operands\[1] = copy\_rtx ( operands\[1] );\
}\
operands\[1] = gen\_rtx ( SUBREG, SImode, operands\[1], 0 );

```
emit_insn ( gen_rtx ( SET, VOIDmode, operands[0], operands[1] ));

DONE;
```

}" )

( define\_insn "extendsidi2"\
\[ ( set ( match\_operand:DI 0 "general\_operand" "=S" )\
( sign\_extend:DI\
( match\_operand:SI 1 "general\_operand" "D" )))\
]\
""\
"move %1,%0;move %k1,%g0" )

( define\_insn "extendpsisi2"\
\[ ( set ( match\_operand:SI 0 "general\_operand" "=D" )\
( sign\_extend:SI\
( match\_operand:PSI 1 "general\_operand" "_&#x41;_&#x53;" )))\
]\
""\
"\*\
{\
/\* we may want to add mem-ref as an opt. later. \*/

```
if ( DST_REGS == REGNO_REG_CLASS ( REGNO ( operands[0] )) &&
ADDR_REGS != REGNO_REG_CLASS ( REGNO ( operands[1] )) &&
! MEM_IN_STRUCT_P ( operands[0] ) &&
! MEM_IN_STRUCT_P ( operands[1] ))
{
return \"tfr	%1,%0\";
}
else
{
return \"move	%1,%0\";
}
```

}")

;;\
;; ...........................................................................\
;;\
;; CONVERSIONS BETWEEN FLOAT AND DOUBLE\
;;\
;; ...........................................................................\
;;

;;\
;; ...........................................................................\
;;\
;; CONVERSIONS BETWEEN FLOATING POINT AND INTEGER\
;;\
;; ...........................................................................\
;;

;; Using the signed routine at this point - this is a bug.

( define\_insn "floatunssidf2"\
\[ ( set ( match\_operand:DF 0 "register\_operand" "=D" )\
( unsigned\_float:DF\
( match\_operand:SI 1 "register\_operand" "0" )))\
]\
""\
"\*\
{\
if ( DSP16\_A\_REGNUM == REGNO ( operands\[0] ))\
{\
return "jsr floatsidf\_a";\
}\
else\
{\
return "jsr floatsidf\_b";\
}\
}" )

;; Using the signed routine at this point - this is a bug.

( define\_insn "floatunssisf2"\
\[ ( set ( match\_operand:SF 0 "register\_operand" "=D" )\
( unsigned\_float:SF\
( match\_operand:SI 1 "register\_operand" "0" )))\
]\
""\
"\*\
{\
if ( DSP16\_A\_REGNUM == REGNO ( operands\[0] ))\
{\
return "jsr floatsidf\_a";\
}\
else\
{\
return "jsr floatsidf\_b";\
}\
}" )

( define\_insn "floatsidf2"\
\[ ( set ( match\_operand:DF 0 "register\_operand" "=D" )\
( float:DF\
( match\_operand:SI 1 "register\_operand" "0" )))\
]\
""\
"\*\
{\
if ( DSP16\_A\_REGNUM == REGNO ( operands\[0] ))\
{\
return "jsr floatsidf\_a";\
}\
else\
{\
return "jsr floatsidf\_b";\
}\
}" )

( define\_insn "floatsisf2"\
\[ ( set ( match\_operand:SF 0 "register\_operand" "=D" )\
( float:SF\
( match\_operand:SI 1 "register\_operand" "0" )))\
]\
""\
"\*\
{\
if ( DSP16\_A\_REGNUM == REGNO ( operands\[0] ))\
{\
return "jsr floatsidf\_a";\
}\
else\
{\
return "jsr floatsidf\_b";\
}\
}" )

( define\_insn "fixdfsi2"\
\[ ( set ( match\_operand:SI 0 "register\_operand" "=D" )\
( fix:SI\
( match\_operand:DF 1 "register\_operand" "0" )))\
]\
""\
"\*\
{\
if ( DSP16\_A\_REGNUM == REGNO ( operands\[0] ))\
{\
return "jsr fixdfsi\_a";\
}\
else\
{\
return "jsr fixdfsi\_b";\
}\
}" )

( define\_insn "fixsfsi2"\
\[ ( set ( match\_operand:SI 0 "register\_operand" "=D" )\
( fix:SI\
( match\_operand:SF 1 "register\_operand" "0" )))\
]\
""\
"\*\
{\
if ( DSP16\_A\_REGNUM == REGNO ( operands\[0] ))\
{\
return "jsr fixdfsi\_a";\
}\
else\
{\
return "jsr fixdfsi\_b";\
}\
}" )

( define\_insn "fixunsdfsi2"\
\[ ( set ( match\_operand:SI 0 "register\_operand" "=D" )\
( unsigned\_fix:SI\
( match\_operand:DF 1 "register\_operand" "0" )))\
]\
""\
"\*\
{\
if ( DSP16\_A\_REGNUM == REGNO ( operands\[0] ))\
{\
return "jsr fixunsdfsi\_a";\
}\
else\
{\
return "jsr fixunsdfsi\_b";\
}\
}" )

( define\_insn "fixunssfsi2"\
\[ ( set ( match\_operand:SI 0 "register\_operand" "=D" )\
( unsigned\_fix:SI\
( match\_operand:SF 1 "register\_operand" "0" )))\
]\
""\
"\*\
{\
if ( DSP16\_A\_REGNUM == REGNO ( operands\[0] ))\
{\
return "jsr fixunsdfsi\_a";\
}\
else\
{\
return "jsr fixunsdfsi\_b";\
}\
}" )

;; Using the signed routine at this point - this is a bug.

( define\_insn "floatunsdidf2"\
\[ ( set ( match\_operand:DF 0 "register\_operand" "=D" )\
( unsigned\_float:DF\
( match\_operand:DI 1 "register\_operand" "0" )))\
]\
""\
"\*\
{\
if ( DSP16\_A\_REGNUM == REGNO ( operands\[0] ))\
{\
return "jsr floatdidf\_a";\
}\
else\
{\
return "jsr floatdidf\_b";\
}\
}" )

;; Using the signed routine at this point - this is a bug.

( define\_insn "floatunsdisf2"\
\[ ( set ( match\_operand:SF 0 "register\_operand" "=D" )\
( unsigned\_float:SF\
( match\_operand:DI 1 "register\_operand" "0" )))\
]\
""\
"\*\
{\
if ( DSP16\_A\_REGNUM == REGNO ( operands\[0] ))\
{\
return "jsr floatdidf\_a";\
}\
else\
{\
return "jsr floatdidf\_b";\
}\
}" )

( define\_insn "floatdidf2"\
\[ ( set ( match\_operand:DF 0 "register\_operand" "=D" )\
( float:DF\
( match\_operand:DI 1 "register\_operand" "0" )))\
]\
""\
"\*\
{\
if ( DSP16\_A\_REGNUM == REGNO ( operands\[0] ))\
{\
return "jsr floatdidf\_a";\
}\
else\
{\
return "jsr floatdidf\_b";\
}\
}" )

( define\_insn "floatdisf2"\
\[ ( set ( match\_operand:SF 0 "register\_operand" "=D" )\
( float:SF\
( match\_operand:DI 1 "register\_operand" "0" )))\
]\
""\
"\*\
{\
if ( DSP16\_A\_REGNUM == REGNO ( operands\[0] ))\
{\
return "jsr floatdidf\_a";\
}\
else\
{\
return "jsr floatdidf\_b";\
}\
}" )

( define\_insn "fixdfdi2"\
\[ ( set ( match\_operand:DI 0 "register\_operand" "=D" )\
( fix:DI\
( match\_operand:DF 1 "register\_operand" "0" )))\
]\
""\
"\*\
{\
if ( DSP16\_A\_REGNUM == REGNO ( operands\[0] ))\
{\
return "jsr fixdfdi\_a";\
}\
else\
{\
return "jsr fixdfdi\_b";\
}\
}" )

( define\_insn "fixsfdi2"\
\[ ( set ( match\_operand:DI 0 "register\_operand" "=D" )\
( fix:DI\
( match\_operand:SF 1 "register\_operand" "0" )))\
]\
""\
"\*\
{\
if ( DSP16\_A\_REGNUM == REGNO ( operands\[0] ))\
{\
return "jsr fixdfdi\_a";\
}\
else\
{\
return "jsr fixdfdi\_b";\
}\
}" )

( define\_insn "fixunsdfdi2"\
\[ ( set ( match\_operand:DI 0 "register\_operand" "=D" )\
( unsigned\_fix:DI\
( match\_operand:DF 1 "register\_operand" "0" )))\
]\
""\
"\*\
{\
if ( DSP16\_A\_REGNUM == REGNO ( operands\[0] ))\
{\
return "jsr fixunsdfdi\_a";\
}\
else\
{\
return "jsr fixunsdfdi\_b";\
}\
}" )

( define\_insn "fixunssfdi2"\
\[ ( set ( match\_operand:DI 0 "register\_operand" "=D" )\
( unsigned\_fix:DI\
( match\_operand:SF 1 "register\_operand" "0" )))\
]\
""\
"\*\
{\
if ( DSP16\_A\_REGNUM == REGNO ( operands\[0] ))\
{\
return "jsr fixunsdfdi\_a";\
}\
else\
{\
return "jsr fixunsdfdi\_b";\
}\
}" )

( define\_expand "fix\_truncdfsi2"\
\[ ( set ( match\_operand:SI 0 "register\_operand" "=D" )\
( fix:SI\
( match\_operand:DF 1 "register\_operand" "0" )))\
]\
""\
"" )

( define\_expand "fix\_truncsfsi2"\
\[ ( set ( match\_operand:SI 0 "register\_operand" "=D" )\
( fix:SI\
( match\_operand:SF 1 "register\_operand" "0" )))\
]\
""\
"" )

( define\_expand "fixuns\_truncdfsi2"\
\[ ( set ( match\_operand:SI 0 "register\_operand" "=D" )\
( unsigned\_fix:SI\
( match\_operand:DF 1 "register\_operand" "0" )))\
]\
""\
"" )

( define\_expand "fixuns\_truncsfsi2"\
\[ ( set ( match\_operand:SI 0 "register\_operand" "=D" )\
( unsigned\_fix:SI\
( match\_operand:SF 1 "register\_operand" "0" )))\
]\
""\
"" )

( define\_expand "fix\_truncdfdi2"\
\[ ( set ( match\_operand:DI 0 "register\_operand" "=D" )\
( fix:DI\
( match\_operand:DF 1 "register\_operand" "0" )))\
]\
""\
"" )

( define\_expand "fix\_truncsfdi2"\
\[ ( set ( match\_operand:DI 0 "register\_operand" "=D" )\
( fix:DI\
( match\_operand:SF 1 "register\_operand" "0" )))\
]\
""\
"" )

( define\_expand "fixuns\_truncdfdi2"\
\[ ( set ( match\_operand:DI 0 "register\_operand" "=D" )\
( unsigned\_fix:DI\
( match\_operand:DF 1 "register\_operand" "0" )))\
]\
""\
"" )

( define\_expand "fixuns\_truncsfdi2"\
\[ ( set ( match\_operand:DI 0 "register\_operand" "=D" )\
( unsigned\_fix:DI\
( match\_operand:SF 1 "register\_operand" "0" )))\
]\
""\
"" )

;;\
;; ...........................................................................\
;;\
;; CONDITIONAL JUMPS\
;;\
;; ...........................................................................\
;;

( define\_insn "do"\
\[ ( parallel\
\[ ( clobber ( cc0 ))\
( use ( match\_operand:SI 0 "general\_operand" "_&#x44;_&#x53;,J" ))\
( set ( pc ) ( if\_then\_else ( eq ( cc0 ) ( const\_int 0 ))\
( label\_ref ( match\_operand 1 "" "" ))\
( pc )))\
] )\
]\
""\
"\*\
{\
extern void record\_address\_regs\_used ( );

```
/* we must scan forward and make sure that there is at least one non-do
 * insn between this insn and the target label. If not, 86 the loop. 
 * BTW, a nop instruction doesn't count.
 */
rtx scan = insn;

while ( scan && ( operands[1] != scan ))
{
if (( INSN == GET_CODE ( scan )) && 
    ( const0_rtx != PATTERN ( scan )))
{
    break;
}
    scan = NEXT_INSN ( scan );
}

if ( ! scan )
{
/* do without corresponding od ! */
abort ( );
}
if (( scan == operands[1] ) ||
( which_alternative && ( 1 == INTVAL ( operands[0] ))))
{
/* we're not really doing a do; record that no conflicts are 
 * possible.
 */
record_address_regs_used ( const0_rtx );

/* decrement the number of uses at the target label. */

-- LABEL_NUSES ( operands[1] );

return \"\";
}

/* record potential r/n register conflicts. */
record_address_regs_used ( PATTERN ( scan ));

if ( which_alternative )
{
return \"do	#%c0,%l1\";
}
else
{
if ( IS_SRC_OR_MPY_P ( REGNO ( operands[0] )))
{
    return \"do	%0,%l1\";
}
else
{
    return \"do	%e0,%l1\";
}
}
```

}" )

;; This shape should match the back-branch insn and produce no real code.\
;; It is a placeholder so that the compiler knows that the do loop target\
;; label is implicitly a branch.

( define\_insn "od"\
\[ ( parallel\
\[ ( clobber ( cc0 ))\
( set ( pc ) ( match\_operand 0 "" "" ))\
] )\
]\
""\
"\*\
{\
rtx scan = PREV\_INSN ( insn );

```
while ( scan && 
   (( INSN != GET_CODE ( scan )) || 
    /* ignore latent nop insns! */
    ( const0_rtx == PATTERN ( scan ))) &&
   ( JUMP_INSN != GET_CODE ( scan )))
{
scan = PREV_INSN ( scan );
}
if ( ! scan )
{
/* od without corresponding do ! */
abort ( );
}
if ( JUMP_INSN == GET_CODE ( scan ))
{
/* pop the stack - no chance of conflict. */
(void) conflicting_address_regs_set_p ( const0_rtx );

/* but hey- auto bonejob! we can't have a jmp as the last insn
   within the DO! */

return \"nop\";
}
if ( INSN == GET_CODE ( scan ))
{
/* if we have a conflict, emit a noop to sw interlock. */
if ( conflicting_address_regs_set_p ( PATTERN ( scan )))
{
    return \"nop\";
}
}
return \"\";
```

}" )

( define\_insn ""\
\[ ( parallel \[ ( set ( match\_operand:SI 0 "register\_operand" "=D,D" )\
( match\_operand:SI 1 "general\_operand" "i,!_&#x44;_&#x53;_A" ))_\
_( clobber_\
_( match\_operand:SI 2 "register\_operand" "=D&#x53;_&#x41;,_&#x44;_&#x53;_A" ))_\
_])_\
_]_\
_""_\
_"_\
{\
switch ( which\_alternative )\
{\
case 0:\
if ( 0 == INTVAL ( operands\[1] ))\
{\
return "clr %0";\
}\
else\
{\
return "move #>1,%0";\
}

```
case 1:
if ( DST_REGS == REGNO_REG_CLASS ( REGNO ( operands[1] )))
{
    return \"move	%e1,%0\";
}
else
{
    return \"move	%1,%0\";
}
}
```

}" )

( define\_expand "seq"\
\[ ( set ( match\_operand:SI 0 "register\_operand" "=D" )\
( eq ( cc0 ) ( const\_int 0 ))) ]\
""\
"\
{\
rtx clobber\_exp = gen\_rtx ( CLOBBER, VOIDmode, gen\_reg\_rtx ( SImode ));\
rtx set\_exp = gen\_rtx ( SET, VOIDmode, operands\[0],\
gen\_rtx ( EQ, VOIDmode, cc0\_rtx, const0\_rtx ));

```
emit_insn ( gen_rtx ( PARALLEL, VOIDmode,
		 gen_rtvec ( 2, set_exp, clobber_exp )));
DONE;
```

}" )

( define\_insn ""\
\[ ( parallel \[ ( set ( match\_operand:SI 0 "register\_operand" "=\&D" )\
( eq ( cc0 ) ( const\_int 0 )))\
( clobber ( match\_operand:SI 1 "register\_operand" "=_&#x53;_&#x44;" ))\
])\
]\
""\
"move #>1,%1;move #0,%0;teq %1,%0" )

( define\_insn "beq"\
\[ ( set ( pc )\
( if\_then\_else ( eq ( cc0 )\
( const\_int 0 ) )\
( label\_ref ( match\_operand 0 "" "" ) )\
( pc ) ) ) ]\
""\
"jeq %l0" )

( define\_expand "sequ"\
\[ ( set ( match\_operand:SI 0 "register\_operand" "=D" )\
( equ ( cc0 ) ( const\_int 0 ))) ]\
""\
"\
{\
rtx clobber\_exp = gen\_rtx ( CLOBBER, VOIDmode, gen\_reg\_rtx ( SImode ));\
rtx set\_exp = gen\_rtx ( SET, VOIDmode, operands\[0],\
gen\_rtx ( EQU, VOIDmode, cc0\_rtx, const0\_rtx ));

```
emit_insn ( gen_rtx ( PARALLEL, VOIDmode,
		 gen_rtvec ( 2, set_exp, clobber_exp )));
DONE;
```

}" )

( define\_insn ""\
\[ ( parallel \[ ( set ( match\_operand:SI 0 "register\_operand" "=\&D" )\
( equ ( cc0 ) ( const\_int 0 )))\
( clobber ( match\_operand:SI 1 "register\_operand" "=_&#x53;_&#x44;" ))\
])\
]\
""\
"move #>1,%1;move #0,%0;teq %1,%0" )

( define\_insn "bequ"\
\[ ( set ( pc )\
( if\_then\_else ( equ ( cc0 )\
( const\_int 0 ) )\
( label\_ref ( match\_operand 0 "" "" ) )\
( pc ) ) ) ]\
""\
"jeq %l0" )

( define\_expand "sne"\
\[ ( set ( match\_operand:SI 0 "register\_operand" "=D" )\
( ne ( cc0 ) ( const\_int 0 ))) ]\
""\
"\
{\
rtx clobber\_exp = gen\_rtx ( CLOBBER, VOIDmode, gen\_reg\_rtx ( SImode ));\
rtx set\_exp = gen\_rtx ( SET, VOIDmode, operands\[0],\
gen\_rtx ( NE, VOIDmode, cc0\_rtx, const0\_rtx ));

```
emit_insn ( gen_rtx ( PARALLEL, VOIDmode,
		 gen_rtvec ( 2, set_exp, clobber_exp )));
DONE;
```

}" )

( define\_insn ""\
\[ ( parallel \[ ( set ( match\_operand:SI 0 "register\_operand" "=\&D" )\
( ne ( cc0 ) ( const\_int 0 )))\
( clobber ( match\_operand:SI 1 "register\_operand" "=_&#x53;_&#x44;" ))\
])\
]\
""\
"move #>1,%1;move #0,%0;tne %1,%0" )

( define\_insn "bne"\
\[ ( set ( pc )\
( if\_then\_else ( ne ( cc0 )\
( const\_int 0 ) )\
( label\_ref ( match\_operand 0 "" "" ) )\
( pc ) ) ) ]\
""\
"jne %l0" )

( define\_expand "sneu"\
\[ ( set ( match\_operand:SI 0 "register\_operand" "=D" )\
( neu ( cc0 ) ( const\_int 0 ))) ]\
""\
"\
{\
rtx clobber\_exp = gen\_rtx ( CLOBBER, VOIDmode, gen\_reg\_rtx ( SImode ));\
rtx set\_exp = gen\_rtx ( SET, VOIDmode, operands\[0],\
gen\_rtx ( NEU, VOIDmode, cc0\_rtx, const0\_rtx ));

```
emit_insn ( gen_rtx ( PARALLEL, VOIDmode,
		 gen_rtvec ( 2, set_exp, clobber_exp )));
DONE;
```

}" )

( define\_insn ""\
\[ ( parallel \[ ( set ( match\_operand:SI 0 "register\_operand" "=\&D" )\
( neu ( cc0 ) ( const\_int 0 )))\
( clobber ( match\_operand:SI 1 "register\_operand" "=_&#x53;_&#x44;" ))\
])\
]\
""\
"move #>1,%1;move #0,%0;tne %1,%0" )

( define\_insn "bneu"\
\[ ( set ( pc )\
( if\_then\_else ( neu ( cc0 )\
( const\_int 0 ) )\
( label\_ref ( match\_operand 0 "" "" ) )\
( pc ) ) ) ]\
""\
"jne %l0" )

( define\_expand "sgt"\
\[ ( set ( match\_operand:SI 0 "register\_operand" "=D" )\
( gt ( cc0 ) ( const\_int 0 ))) ]\
""\
"\
{\
rtx clobber\_exp = gen\_rtx ( CLOBBER, VOIDmode, gen\_reg\_rtx ( SImode ));\
rtx set\_exp = gen\_rtx ( SET, VOIDmode, operands\[0],\
gen\_rtx ( GT, VOIDmode, cc0\_rtx, const0\_rtx ));

```
emit_insn ( gen_rtx ( PARALLEL, VOIDmode,
		 gen_rtvec ( 2, set_exp, clobber_exp )));
DONE;
```

}" )

( define\_insn ""\
\[ ( parallel \[ ( set ( match\_operand:SI 0 "register\_operand" "=\&D" )\
( gt ( cc0 ) ( const\_int 0 )))\
( clobber ( match\_operand:SI 1 "register\_operand" "=_&#x53;_&#x44;" ))\
])\
]\
""\
"move #>1,%1;move #0,%0;tgt %1,%0" )

( define\_insn "bgt"\
\[ ( set ( pc )\
( if\_then\_else ( gt ( cc0 )\
( const\_int 0 ) )\
( label\_ref ( match\_operand 0 "" "" ) )\
( pc ) ) ) ]\
""\
"jgt %l0" )

( define\_expand "sgtu"\
\[ ( set ( match\_operand:SI 0 "register\_operand" "=D" )\
( gtu ( cc0 ) ( const\_int 0 ))) ]\
""\
"\
{\
rtx clobber\_exp = gen\_rtx ( CLOBBER, VOIDmode, gen\_reg\_rtx ( SImode ));\
rtx set\_exp = gen\_rtx ( SET, VOIDmode, operands\[0],\
gen\_rtx ( GTU, VOIDmode, cc0\_rtx, const0\_rtx ));

```
emit_insn ( gen_rtx ( PARALLEL, VOIDmode,
		 gen_rtvec ( 2, set_exp, clobber_exp )));
DONE;
```

}" )

( define\_insn ""\
\[ ( parallel \[ ( set ( match\_operand:SI 0 "register\_operand" "=\&D" )\
( gtu ( cc0 ) ( const\_int 0 )))\
( clobber ( match\_operand:SI 1 "register\_operand" "=_&#x53;_&#x44;" ))\
])\
]\
""\
"move #>1,%1;move #0,%0;tgt %1,%0" )

( define\_insn "bgtu"\
\[ ( set ( pc )\
( if\_then\_else ( gtu ( cc0 )\
( const\_int 0 ) )\
( label\_ref ( match\_operand 0 "" "" ) )\
( pc ) ) ) ]\
""\
"jgt %l0" )

( define\_expand "slt"\
\[ ( set ( match\_operand:SI 0 "register\_operand" "=D" )\
( lt ( cc0 ) ( const\_int 0 ))) ]\
""\
"\
{\
rtx clobber\_exp = gen\_rtx ( CLOBBER, VOIDmode, gen\_reg\_rtx ( SImode ));\
rtx set\_exp = gen\_rtx ( SET, VOIDmode, operands\[0],\
gen\_rtx ( LT, VOIDmode, cc0\_rtx, const0\_rtx ));

```
emit_insn ( gen_rtx ( PARALLEL, VOIDmode,
		 gen_rtvec ( 2, set_exp, clobber_exp )));
DONE;
```

}" )

( define\_insn ""\
\[ ( parallel \[ ( set ( match\_operand:SI 0 "register\_operand" "=\&D" )\
( lt ( cc0 ) ( const\_int 0 )))\
( clobber ( match\_operand:SI 1 "register\_operand" "=_&#x53;_&#x44;" ))\
])\
]\
""\
"move #>1,%1;move #0,%0;tlt %1,%0" )

( define\_insn "blt"\
\[ ( set ( pc )\
( if\_then\_else ( lt ( cc0 )\
( const\_int 0 ) )\
( label\_ref ( match\_operand 0 "" "" ) )\
( pc ) ) ) ]\
""\
"jlt %l0" )

( define\_expand "sltu"\
\[ ( set ( match\_operand:SI 0 "register\_operand" "=D" )\
( ltu ( cc0 ) ( const\_int 0 ))) ]\
""\
"\
{\
rtx clobber\_exp = gen\_rtx ( CLOBBER, VOIDmode, gen\_reg\_rtx ( SImode ));\
rtx set\_exp = gen\_rtx ( SET, VOIDmode, operands\[0],\
gen\_rtx ( LTU, VOIDmode, cc0\_rtx, const0\_rtx ));

```
emit_insn ( gen_rtx ( PARALLEL, VOIDmode,
		 gen_rtvec ( 2, set_exp, clobber_exp )));
DONE;
```

}" )

( define\_insn ""\
\[ ( parallel \[ ( set ( match\_operand:SI 0 "register\_operand" "=\&D" )\
( ltu ( cc0 ) ( const\_int 0 )))\
( clobber ( match\_operand:SI 1 "register\_operand" "=_&#x53;_&#x44;" ))\
])\
]\
""\
"move #>1,%1;move #0,%0;tlt %1,%0" )

( define\_insn "bltu"\
\[ ( set ( pc )\
( if\_then\_else ( ltu ( cc0 )\
( const\_int 0 ) )\
( label\_ref ( match\_operand 0 "" "" ) )\
( pc ) ) ) ]\
""\
"jlt %l0" )

( define\_expand "sge"\
\[ ( set ( match\_operand:SI 0 "register\_operand" "=D" )\
( ge ( cc0 ) ( const\_int 0 ))) ]\
""\
"\
{\
rtx clobber\_exp = gen\_rtx ( CLOBBER, VOIDmode, gen\_reg\_rtx ( SImode ));\
rtx set\_exp = gen\_rtx ( SET, VOIDmode, operands\[0],\
gen\_rtx ( GE, VOIDmode, cc0\_rtx, const0\_rtx ));

```
emit_insn ( gen_rtx ( PARALLEL, VOIDmode,
		 gen_rtvec ( 2, set_exp, clobber_exp )));
DONE;
```

}" )

( define\_insn ""\
\[ ( parallel \[ ( set ( match\_operand:SI 0 "register\_operand" "=\&D" )\
( ge ( cc0 ) ( const\_int 0 )))\
( clobber ( match\_operand:SI 1 "register\_operand" "=_&#x53;_&#x44;" ))\
])\
]\
""\
"move #>1,%1;move #0,%0;tge %1,%0" )

( define\_insn "bge"\
\[ ( set ( pc )\
( if\_then\_else ( ge ( cc0 )\
( const\_int 0 ) )\
( label\_ref ( match\_operand 0 "" "" ) )\
( pc ) ) ) ]\
""\
"jge %l0" )

( define\_expand "sgeu"\
\[ ( set ( match\_operand:SI 0 "register\_operand" "=D" )\
( geu ( cc0 ) ( const\_int 0 ))) ]\
""\
"\
{\
rtx clobber\_exp = gen\_rtx ( CLOBBER, VOIDmode, gen\_reg\_rtx ( SImode ));\
rtx set\_exp = gen\_rtx ( SET, VOIDmode, operands\[0],\
gen\_rtx ( GEU, VOIDmode, cc0\_rtx, const0\_rtx ));

```
emit_insn ( gen_rtx ( PARALLEL, VOIDmode,
		 gen_rtvec ( 2, set_exp, clobber_exp )));
DONE;
```

}" )

( define\_insn ""\
\[ ( parallel \[ ( set ( match\_operand:SI 0 "register\_operand" "=\&D" )\
( geu ( cc0 ) ( const\_int 0 )))\
( clobber ( match\_operand:SI 1 "register\_operand" "=_&#x53;_&#x44;" ))\
])\
]\
""\
"move #>1,%1;move #0,%0;tge %1,%0" )

( define\_insn "bgeu"\
\[ ( set ( pc )\
( if\_then\_else ( geu ( cc0 )\
( const\_int 0 ) )\
( label\_ref ( match\_operand 0 "" "" ) )\
( pc ) ) ) ]\
""\
"jge %l0" )

( define\_expand "sle"\
\[ ( set ( match\_operand:SI 0 "register\_operand" "=D" )\
( le ( cc0 ) ( const\_int 0 ))) ]\
""\
"\
{\
rtx clobber\_exp = gen\_rtx ( CLOBBER, VOIDmode, gen\_reg\_rtx ( SImode ));\
rtx set\_exp = gen\_rtx ( SET, VOIDmode, operands\[0],\
gen\_rtx ( LE, VOIDmode, cc0\_rtx, const0\_rtx ));

```
emit_insn ( gen_rtx ( PARALLEL, VOIDmode,
		 gen_rtvec ( 2, set_exp, clobber_exp )));
DONE;
```

}" )

( define\_insn ""\
\[ ( parallel \[ ( set ( match\_operand:SI 0 "register\_operand" "=\&D" )\
( le ( cc0 ) ( const\_int 0 )))\
( clobber ( match\_operand:SI 1 "register\_operand" "=_&#x53;_&#x44;" ))\
])\
]\
""\
"move #>1,%1;move #0,%0;tle %1,%0" )

( define\_insn "ble"\
\[ ( set ( pc )\
( if\_then\_else ( le ( cc0 )\
( const\_int 0 ) )\
( label\_ref ( match\_operand 0 "" "" ) )\
( pc ) ) ) ]\
""\
"jle %l0" )

( define\_expand "sleu"\
\[ ( set ( match\_operand:SI 0 "register\_operand" "=D" )\
( leu ( cc0 ) ( const\_int 0 ))) ]\
""\
"\
{\
rtx clobber\_exp = gen\_rtx ( CLOBBER, VOIDmode, gen\_reg\_rtx ( SImode ));\
rtx set\_exp = gen\_rtx ( SET, VOIDmode, operands\[0],\
gen\_rtx ( LEU, VOIDmode, cc0\_rtx, const0\_rtx ));

```
emit_insn ( gen_rtx ( PARALLEL, VOIDmode,
		 gen_rtvec ( 2, set_exp, clobber_exp )));
DONE;
```

}" )

( define\_insn ""\
\[ ( parallel \[ ( set ( match\_operand:SI 0 "register\_operand" "=\&D" )\
( leu ( cc0 ) ( const\_int 0 )))\
( clobber ( match\_operand:SI 1 "register\_operand" "=_&#x53;_&#x44;" ))\
])\
]\
""\
"move #>1,%1;move #0,%0;tle %1,%0" )

( define\_insn "bleu"\
\[ ( set ( pc )\
( if\_then\_else ( leu ( cc0 )\
( const\_int 0 ) )\
( label\_ref ( match\_operand 0 "" "" ) )\
( pc ) ) ) ]\
""\
"jle %l0" )

;;\
;; ...........................................................................\
;;\
;; CONDITIONAL JUMPS WITH INVERTED REGISTER ALLOCATION\
;;\
;; ...........................................................................\
;;

( define\_insn ""\
\[ ( set ( pc )\
( if\_then\_else ( eq ( cc0 )\
( const\_int 0 ) )\
( pc )\
( label\_ref ( match\_operand 0 "" "" ) ) ) ) ]\
""\
"jne %l0" )

( define\_insn ""\
\[ ( set ( pc )\
( if\_then\_else ( equ ( cc0 )\
( const\_int 0 ) )\
( pc )\
( label\_ref ( match\_operand 0 "" "" ) ) ) ) ]\
""\
"jne %l0" )

( define\_insn ""\
\[ ( set ( pc )\
( if\_then\_else ( ne ( cc0 )\
( const\_int 0 ) )\
( pc )\
( label\_ref ( match\_operand 0 "" "" ) ) ) ) ]\
""\
"jeq %l0" )

( define\_insn ""\
\[ ( set ( pc )\
( if\_then\_else ( neu ( cc0 )\
( const\_int 0 ) )\
( pc )\
( label\_ref ( match\_operand 0 "" "" ) ) ) ) ]\
""\
"jeq %l0" )

( define\_insn ""\
\[ ( set ( pc )\
( if\_then\_else ( gt ( cc0 )\
( const\_int 0 ) )\
( pc )\
( label\_ref ( match\_operand 0 "" "" ) ) ) ) ]\
""\
"jle %l0" )

( define\_insn ""\
\[ ( set ( pc )\
( if\_then\_else ( gtu ( cc0 )\
( const\_int 0 ) )\
( pc )\
( label\_ref ( match\_operand 0 "" "" ) ) ) ) ]\
""\
"jle %l0" )

( define\_insn ""\
\[ ( set ( pc )\
( if\_then\_else ( lt ( cc0 )\
( const\_int 0 ) )\
( pc )\
( label\_ref ( match\_operand 0 "" "" ) ) ) ) ]\
""\
"jge %l0" )

( define\_insn ""\
\[ ( set ( pc )\
( if\_then\_else ( ltu ( cc0 )\
( const\_int 0 ) )\
( pc )\
( label\_ref ( match\_operand 0 "" "" ) ) ) ) ]\
""\
"jge %l0" )

( define\_insn ""\
\[ ( set ( pc )\
( if\_then\_else ( ge ( cc0 )\
( const\_int 0 ) )\
( pc )\
( label\_ref ( match\_operand 0 "" "" ) ) ) ) ]\
""\
"jlt %l0" )

( define\_insn ""\
\[ ( set ( pc )\
( if\_then\_else ( geu ( cc0 )\
( const\_int 0 ) )\
( pc )\
( label\_ref ( match\_operand 0 "" "" ) ) ) ) ]\
""\
"jlt %l0" )

( define\_insn ""\
\[ ( set ( pc )\
( if\_then\_else ( le ( cc0 )\
( const\_int 0 ) )\
( pc )\
( label\_ref ( match\_operand 0 "" "" ) ) ) ) ]\
""\
"jgt %l0" )

( define\_insn ""\
\[ ( set ( pc )\
( if\_then\_else ( leu ( cc0 )\
( const\_int 0 ) )\
( pc )\
( label\_ref ( match\_operand 0 "" "" ) ) ) ) ]\
""\
"jgt %l0" )

;;\
;; ...........................................................................\
;;\
;; CONDITIONALY STORE ZERO OR NON-ZERO\
;;\
;; ...........................................................................\
;;

;;\
;; ...........................................................................\
;;\
;; UNCONDITIONAL JUMP\
;;\
;; ...........................................................................\
;;

( define\_insn "jump"\
\[ ( set ( pc )\
( label\_ref ( match\_operand 0 "" "" ) ) ) ]\
""\
"jmp %l0" )

;;\
;; ...........................................................................\
;;\
;; TABLE JUMP ( SWITCH IMPLEMENTATION )\
;;\
;; ...........................................................................\
;;

( define\_insn "tablejump"\
\[ ( set ( pc ) ( match\_operand:PSI 0 "register\_operand" "A" ) )\
( use ( label\_ref ( match\_operand 1 "" "" ) ) ) ]\
""\
"jmp (%0)" )

;;\
;; ...........................................................................\
;;\
;; SUBROUTINE CALLS\
;;\
;; ...........................................................................\
;;

( define\_expand "call"\
\[ ( call ( match\_operand:PSI 0 "general\_operand" "mA" )\
( match\_operand:SI 1 "immediate\_operand" "i" ))\
]\
""\
"\
{\
emit\_insn (\
gen\_rtx ( PARALLEL,\
VOIDmode,\
gen\_rtvec ( 1,\
gen\_rtx ( CALL,\
VOIDmode,\
operands\[0], operands\[1] ))));\
DONE;\
}" )

( define\_insn ""\
\[ ( parallel \[ ( call ( match\_operand:PSI 0 "general\_operand" "mA" )\
( match\_operand:SI 1 "immediate\_operand" "i" )) ] )\
]\
""\
"\*\
{\
clear\_n\_reg\_values ( );

```
switch ( INTVAL ( operands[1] ))
{
case 0:
return \"jsr	%0\";

case 1:
return \"jsr	%0\;move	(r6)-\";

case 2:
return \"jsr	%0\;move	(r6)-\;move	(r6)-\";

default:
return \"jsr	%0\;move	#%p1,n6\;move	(r6)-n6\";
}
```

}" )

( define\_expand "call\_value"\
\[ ( set ( match\_operand 0 "" "" )\
( call ( match\_operand:PSI 1 "general\_operand" "mA" )\
( match\_operand:SI 2 "immediate\_operand" "i" )))\
]\
""\
"\
{\
emit\_insn (\
gen\_rtx ( PARALLEL, VOIDmode,\
gen\_rtvec ( 1,\
gen\_rtx ( SET, VOIDmode,\
operands\[0],\
gen\_rtx ( CALL, VOIDmode,\
operands\[1],\
operands\[2] )))));\
DONE;\
}" )

( define\_insn ""\
\[ ( parallel \[ ( set ( match\_operand 0 "" "" )\
( call ( match\_operand:PSI 1 "general\_operand" "mA" )\
( match\_operand:SI 2 "immediate\_operand" "i" )))\
] )\
]\
""\
"\*\
{\
clear\_n\_reg\_values ( );

```
switch ( INTVAL ( operands[2] ))
{
case 0:
return \"jsr	%1\";

case 1:
return \"jsr	%1\;move	(r6)-\";

case 2:
return \"jsr	%1\;move	(r6)-\;move	(r6)-\";

default:
return \"jsr	%1\;move	#%p2,n6\;move	(r6)-n6\";
}
```

}" )

;;\
;; ...........................................................................\
;;\
;; NOP\
;;\
;; ...........................................................................\
;;

( define\_insn "nop"\
\[ ( const\_int 0 ) ]\
""\
"\*\
{\
rtx peek = PREV\_INSN ( insn );

```
while (( peek ) && 
   (( NOTE == GET_CODE ( peek )) ||
    (( JUMP_INSN == GET_CODE ( peek )) &&
     ( PARALLEL == GET_CODE ( PATTERN ( peek ))))))
{
peek = PREV_INSN ( peek );
}
if (( ! peek ) || ( CODE_LABEL != GET_CODE ( peek )) ||
( ! LABEL_NUSES ( peek )))
{
return \"\";
}

peek = NEXT_INSN ( insn );
while (( peek ) && 
   (( NOTE == GET_CODE ( peek )) ||
    (( JUMP_INSN == GET_CODE ( peek )) &&
     ( PARALLEL == GET_CODE ( PATTERN ( peek ))))))
{
peek = NEXT_INSN ( peek );
}
if (( ! peek ) || 
( ! LABEL_NUSES ( peek )) ||
(( CODE_LABEL != GET_CODE ( peek )) && 
 ( const0_rtx != PATTERN ( peek ))))
{
return \"\";
}

return \"nop\";
```

}" )

;; This insn is needed because jump.c doesn't realize that this is really\
;; a nop.

( define\_insn "" \[ ( set ( pc ) ( pc )) ] "" "" )

( define\_insn "probe"\
\[ ( parallel \[ ( const\_int 0 ) ] ) ]\
""\
"\*\
{\
if ( TARGET\_STACK\_CHECK )\
{\
return "jsr F\_\_stack\_check";\
}\
else\
{\
return "";\
}\
}" )

;;\
;; ...........................................................................\
;;\
;; PEEPHOLE OPTIMIZATIONS\
;;\
;; ...........................................................................\
;;

;; this sequence can be introduced by the do loop generation code. a\
;; reasonable data flow analysis and optimization phase will (in the future)\
;; clean it up.

( define\_peephole\
\[ ( set ( match\_operand:SI 0 "register\_operand" "=\*D" )\
( minus:SI ( match\_operand:SI 1 "register\_operand" "0" )\
( match\_operand:SI 2 "register\_operand" "_&#x53;_&#x44;" )))\
( set ( match\_dup 0 )\
( plus:SI ( match\_dup 1 )\
( match\_dup 2 )))\
]\
"( ! rtx\_equal\_p (operands\[0], operands\[2] ))"\
"" )

;;- Local variables:\
;;- mode:emacs-lisp\
;;- comment-start: ";;- "\
;;- eval: ( set-syntax-table ( copy-sequence ( syntax-table ) ) )\
;;- eval: ( modify-syntax-entry ?\[ "(]" )\
;;- eval: ( modify-syntax-entry ?] ")\[" )\
;;- eval: ( modify-syntax-entry ?{ "(}" )\
;;- eval: ( modify-syntax-entry ?} "){" )\
;;- End:
