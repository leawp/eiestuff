# eiestuff

```assembler
.include "m8def.inc" 

nop 


ldi r16, 99 	; ilosc liczb i liczba

ldi r26, 0x60 	; adres rejestr X
clr r27 		; kasuj r27

petla:
	st X+, r16 			; zapisz r16 do adresu w X i zinkrementuj
	dec r16 			; odejmij 1 od r16
	brne petla 			; wykonaj jesli SREG Z jest nie podniesiony 

st X, r16 				; zapisz ostatnia wartosc do SRAM

nop ; nic nie rob 






ldi r26, 0x60 			; zapisz adres do r26
clr r27 				; zeruj r27 

ldi r28, 0xD0 			; zapisz adres do r28
clr r29 				; kasuj r29 

; inicjalizacja stosu
ldi r16, high(RAMEND)
out SPH, r16
ldi r16, low(RAMEND)
out SPL, r16

ldi r18, 100 				; ilosc operacji ktore bedziemy wykonywac 
filtr: 

	ld r16, X+ 				; zapisz to co jest w adresie z X+ do r16

	rcall parzyste 			; skocz do parzyste
	rcall podzielnecztery 	; skocz do podzielnecztery
	rcall bit3_ustawiony 	; skocz do bit3_ustawiony
	rcall bit3_1ibit1_0 	; skocz do bit3_1ibit1_0 
	rcall bit3_1lubbit1_0
	rcall w_przedziale
	rcall poza_przedzialem

	petelka:
	dec r18 				; zdekrementuj r18 o 1 
	brne filtr 				; skocz do filtr jesli nie jest zapalona flaga SREG Z 


rjmp finish



parzyste:
	sbrc r16, 0  			; jezeli na bicie 0 jest 0 to przeskocz nastepna komende
	ret ;urn					; wroc wyzej 
	st Y+, r16 				; zapisz do adresu w Y to co jest w r16 i zinkrementuj wartosc w Y
	rjmp petelka 				; skocz do filtru 


podzielnecztery:
	mov r19, r16			; kopia
	andi r19, 0b00000011
	sbrc r19, 1
	ret
	sbrc r19, 0
	ret
	st Y+, r16
	rjmp petelka

bit3_ustawiony:
	sbrs r16, 3 
	ret
	st Y+, r16
	rjmp petelka

bit3_1ibit1_0:
	sbrs r16, 3 
	ret
	sbrc r16, 1
	ret 
	st Y+, r16
	rjmp petelka

bit3_1lubbit1_0:
	sbrc r16, 3
	brne zapisz

	sbrs r16, 1
	brne zapisz

	ret
	zapisz:
		st Y+, r16
		rjmp petelka

w_przedziale:
	cpi r16, 5
	brlo koncz
	cpi r16, 26
	brge koncz

	rjmp zapisz

	koncz:
	ret
	
poza_przedzialem:
	cpi r16, 5
	brlo zapisz
	cpi r16, 26
	brge zapisz
	ret

finish:2
nop

nop
```
