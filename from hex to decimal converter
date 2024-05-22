.model small
.stack 100h
.data
    hexStr db 5            ; First byte is buffer length (4 characters max) + 1 for length byte
    hexStrBuffer db 4 dup(0) ; Buffer to store user input (4 hex digits max)
    hexLen db 0            ; Variable to store the length of the input string
    prompt db 'Enter a hexadecimal number (up to 4 digits): $'
    resultMsg db 'The decimal value is: $'
    decResult dw 0         ; Variable to store the result

.code
main proc
    mov ax, @data
    mov ds, ax
    mov es, ax

    ; Display prompt
    lea dx, prompt
    mov ah, 09h
    int 21h

    ; Read user input
    lea dx, hexStr
    mov ah, 0Ah             ; Function to read console input
    int 21h

    ; Calculate length of input string
    mov al, [hexStr + 1]
    mov hexLen, al

    ; Initialize pointers and variables
    lea si, hexStrBuffer    ; Source index pointing to the start of the hex string
    mov al, hexLen
    cbw                    ; Convert byte in AL to word in AX (sign-extend)
    mov cx, ax             ; Move length into CX
    xor bx, bx             ; BX will hold the decimal result
    xor dx, dx             ; Clear DX for MUL operation

convert_loop:
    mov al, [si]            ; Load the current hex character
    inc si                  ; Move to the next character
    call hex_to_dec         ; Convert the hex character to decimal

    ; Multiply the current result by 16 (shift left by 4 bits) and add the new value
    mov dx, bx
    shl bx, 4               ; Multiply BX by 16
    add bx, ax              ; Add the new decimal value to BX

    loop convert_loop       ; Repeat until all hex characters are processed

    ; Store the result
    mov decResult, bx

    ; Display result message
    lea dx, resultMsg
    mov ah, 09h
    int 21h

    ; Convert decimal result to string and display
    call print_decimal

    ; Exit the program
    mov ax, 4C00h
    int 21h

main endp

; Subroutine to convert a hex character to its decimal value
hex_to_dec proc
    cmp al, '0'
    jl invalid_char
    cmp al, '9'
    jg check_alpha
    sub al, '0'
    jmp done

check_alpha:
    cmp al, 'A'
    jl invalid_char
    cmp al, 'F'
    jg invalid_char
    sub al, 'A'
    add al, 10

done:
    ret

invalid_char:
    mov al, 0 ; In case of invalid character, just set it to 0 (error handling can be improved)
    ret

hex_to_dec endp

; Subroutine to print a decimal number stored in BX
print_decimal proc
    ; Convert BX to string
    lea si, hexStr          ; Reusing hexStr buffer for output
    mov cx, 0               ; CX will be the length of the string

convert_to_string:
    xor dx, dx              ; Clear DX
    mov ax, bx
    div word ptr 10
    add dl, '0'
    push dx
    inc cx
    mov bx, ax
    test bx, bx
    jnz convert_to_string

print_string:
    pop dx
    mov ah, 02h
    int 21h
    loop print_string

    ret
print_decimal endp

end main
