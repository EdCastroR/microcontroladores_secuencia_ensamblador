LIST p = 16f887
 
 N EQU 0x00 ; crear constante
 cont1 EQU 0x23 ; guardar variable en el puesto 23
 cont2 EQU 0x24 ; guardar variable en el puesto 24
 
 VAR1 EQU 0x20 ; crear variable y guardar en 0x20
 VAR2 EQU 0x21
 CONT EQU 0x22
     ORG 0x00 ; memoria del programa
         GOTO INICIO ; PRIMERA INSTRUCCION - VAYA A INICIO
	 
INICIO
	 
    BCF STATUS, RP1
    BSF STATUS, RP0
    MOVLW 0x71 
    MOVWF OSCCON
    CLRF TRISD ;PUERTO D SEAN SALIDAS
    
    BSF STATUS, RP1 ; PARA IR AL BANK 3 DONDE ESTA EL ANSSEL
    CLRF ANSEL ; DESABILITAR ENTRADAS ANALOGICAS
    
    BCF STATUS, RP0 ; RP0 = 0
    BCF STATUS, RP1 ; RP1 = 0 ASI VOY AL BANK 0
    
    MOVLW 0x00
    MOVWF PORTD ;  PORTD TODAS SALIDAS
    
    MOVLW 0x80 ; MOVER ESTE VALOR A W
    MOVWF VAR1 ; PASAR EL VALOR DE W A VAR1
    
    MOVLW 0x01
    MOVWF VAR2
    
    MOVLW 0x04 ; hasta donde tiene que ir el primer loop.
    MOVWF CONT
    
SECUENCIA1
    MOVF VAR1,0 ; mover la variable VAR1 a el registro W
    IORWF VAR2 ,0 ; W or VAR2 y el resultado a el registro w. para que inicien al mismo tiempo
    MOVWF PORTD ; el resultado guardado en W muevo a el puerto d
    
    
LOOP1
    CALL RETARDO
    CALL RETARDO
    CALL RETARDO
    CALL RETARDO
    RRF VAR1 ; VAR1 HACIA LA DERECHA 
    MOVF VAR1, 0 ; Mover VAR1 a W
    IORLW 0x80 ; SUMAR EL BIT 7 A VAR1
    MOVWF VAR1 ; Guardar el resultado en VAR1
    
    RLF VAR2 ; VAR2 HACIA LA IZQ
    INCF VAR2 ; VAR2 DECREMENTA 1 A 1

    DECFSZ CONT ; DECREMENTAR EL CONT HASTA 0
    GOTO SECUENCIA1
 
    
    
MOVLW 0x08 ; NUEVO VALOR INICIAL DE VAR1
MOVWF VAR1
    
MOVLW 0x10 ; NUEVO VALOR INICIAL DE VAR2
MOVWF VAR2
    
SECUENCIA2
    MOVF VAR1,0 ; mover la variable VAR1 a el registro W
    IORWF VAR2 ,0 ; W or VAR2 y el resultado a el registro w. para que inicien al mismo tiempo
    MOVWF PORTD ; el resultado guardado en W muevo a el puerto d
    
LOOP2
    CALL RETARDO
    CALL RETARDO
    CALL RETARDO
    CALL RETARDO
    RRF VAR1 
    MOVF VAR1, 0 ; Mover VAR2 a W
    IORLW 0x08 ; SUMAR CON EL 0000 1000
    MOVWF VAR1 ; Guardar el resultado en VAR1
   
    RLF VAR2
    MOVF VAR2, 0 ; Mover VAR2 a W
    IORLW 0x10 ; SUMAR CON EL 0001 0000
    MOVWF VAR2 ; Guardar el resultado en VAR1
   
    
    BTFSS PORTD, 7 ; CUANDO EL BIT 7 ESTE EN 1, EVITA A GOTO SEC2
    GOTO SECUENCIA2
    
    
    
    
    
    
    
    
RETARDO
    MOVLW N
    MOVWF cont1
    
REP_1
    MOVLW N
    MOVWF cont2
    
REP_2
    DECFSZ cont2 , 1 ;el valor se guarda en el conbtador 2.
     GOTO REP_2
    DECFSZ cont1 , 1
    GOTO REP_1
    RETURN
   end
