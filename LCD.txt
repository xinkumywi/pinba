;###############################################
;   DEMO for writing a string on the display    
;################################################

	ORG 0000H
	ljmp	MAIN	
	
	ORG 0100H
MAIN:
	mov	R0,#0		;second = 0
	mov	R1,#0		;minute = 0
	mov 	R3,#0
	mov	R2,#0		;10ms count
	
	
	acall	INIT_LCD
	
	mov	A,#00H		;print STR_1 on LCD
	mov	DPTR,#STR_1
	mov	R0,#3		;second = 0
	mov	R1,#2		;minute = 0
	mov 	R3,#0
	mov	R2,#1		;10ms count
	;acall	PUTS_LCD
LOOP:	
	push dpl
	push dph
	mov dptr ,#8000H
	mov A ,#1H
	movx @dptr,A
	acall DELAY
	pop dph
	pop dpl
      	cpl  P1.2
	acall	DELAY_20
        cpl  P1.2
	acall	DELAY_20
        cpl  P1.2
	acall	DELAY_20
  	inc R3
    	mov	A,R3		;print STR_1 on LCD
    	clr	c
    	subb	A,#16
    	jnz	LOOP_2
    	mov R3,#0
    	LOOP_2:
    	mov 	A,R3
	mov	DPTR,#STR_1
	acall	PUTS_LCD
	inc R2
    	mov	A,R2		;print STR_1 on LCD
    	clr	c
    	subb	A,#16
    	jnz	LOOP_3
    	mov R2,#0
    	LOOP_3:
    	mov 	A,R2
	mov	DPTR,#STR_2
	acall	PUTS_LCD
	inc R1
    	mov	A,R1		;print STR_1 on LCD
    	clr	c
    	subb	A,#16
    	jnz	LOOP_4
    	mov R1,#0
    	LOOP_4:
    	mov 	A,R1
	mov	DPTR,#STR_3
	acall	PUTS_LCD
	inc R0
    	mov	A,R0		;print STR_1 on LCD
    	clr	c
    	subb	A,#16
    	jnz	LOOP_5
    	mov R0,#0
    	LOOP_5:
    	mov 	A,R0
	mov	DPTR,#STR_4
	acall	PUTS_LCD

        sjmp LOOP
;put a string into LCD module.
;
;input argument : A is position on screen, DPTR is the pointer of string
;used register : R4,R5,R6,R7
;
PUTS_LCD:
	orl	A,#80H
	mov	R4,#0FFH	;0xFF = -1
	push	DPH		
	push	DPL

        acall	WAIT_DISP_READY
	mov	dptr,#8000H	;Set the position on screen
	movx	@dptr,A	

	pop	DPL		
	pop	DPH
_PUTS_LCD_I:			;for(R4 = 0 ; _STR_1[R4]!='\0' ; R4++)
	mov	A,R4
	movc	A, @A+DPTR	;LCM_DR_W = _STR_1[i]
	
	jz	_PUTS_LCD_I_END

	push	DPH
	push	DPL

        
	mov	dptr,#8001H	;Display on LCD
	movx	@dptr,A

	pop	DPL
	pop	DPH

        acall	WAIT_DISP_READY

	inc	R4
              cpl  P1.2
	
  	acall	DELAY_20
  

	ajmp	_PUTS_LCD_I
_PUTS_LCD_I_END:
	ret
INIT_LCD:
	mov	dptr,#8000H	;LCD Addres Mapping 0x8000

	mov	A,#38H		;Set 8-bit I/O, display in 2 line
	movx	@dptr,A		;Delay for a while
	acall	DELAY

	mov	A,#0EH		;Set display cursor, no flashing
	movx	@dptr,A
	
	acall	DELAY
	mov	A,#01H		;Clear display
	movx	@dptr,A
	
	acall	DELAY

   	mov	A,#06H		;Set move cursor in each write
	movx	@dptr,A
	
	acall	DELAY

	ret




WAIT_DISP_READY:                        ; check the busy bit on display
	push dph
	push dpl
	push acc
WAIT_DISP_READY_I:
        mov dptr, #8002h
        movx A, @dptr
        jb Acc.7, WAIT_DISP_READY_I
        pop acc
	pop dpl
	pop dph
        ret
DELAY:
	mov R7,#1		;R7 = Acc
_DELAY_K:
	mov R6,#100		;R6 = 100
_DELAY_J:
	mov R5,#100		;R5 = 100
_DELAY_I:
	djnz R5,_DELAY_I	;decrease R5,If R5!=0 then jump to _DELAY_I
	djnz R6,_DELAY_J	;decrease R6,If R6!=0 then jump to _DELAY_J
	djnz R7,_DELAY_K	;decrease R7,If R7!=0 then jump to _DELAY_K
	ret
DELAY_20:
	mov R7,#20		;R7 = Acc
_DELAY_L:
	mov R6,#100		;R6 = 100
_DELAY_M:
	mov R5,#100		;R5 = 100
_DELAY_N:
	djnz R5,_DELAY_N	;decrease R5,If R5!=0 then jump to _DELAY_I
	djnz R6,_DELAY_M	;decrease R6,If R6!=0 then jump to _DELAY_J
	djnz R7,_DELAY_L	;decrease R7,If R7!=0 then jump to _DELAY_K
	ret
STR_1:
	DB "8"
	DB 00H
STR_2:
	DB "0"
	DB 00H
STR_3:
	DB "5"
	DB 00H
STR_4:
	DB "1"
	DB 00H
end