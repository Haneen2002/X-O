# X-O
xo project in assembly language
                
; You may customize this and other start-up templates ; 
; The location of this template is c:\emu8086\inc\0_com_template.txt;

org 100h 

include emu8086.inc 

.data 

position db ?
game_start_message db "    TIC TAC TOE     ",13,10,0
player1_message db "player 1, enter a number: ",0
player2_message db "player 2, enter a number: ",0
 
invalid_input_message db "invalid input, try again: ",0 
filled_position_message db "filled position, try again: ",0

board equ 0x500 

.code 
    
start: 

    lea si,game_start_message
    call print_string
    putc 0dh
    putc 0ah
    print "player 1 (x) - player 2 (o)"
    putc 0dh
    putc 0ah
    putc 0dh
    putc 0ah

    mov bx,board       
    mov cx,9
    mov al,'1' 
    
init_board:

    ;initialization of the board with characters '1'.....'9'
    mov [bx],al
    inc al
    inc bx 
loop init_board

mov cx,0

main_loop:

    
    call show_board  
    call find_line
    putc 0dh
    putc 0ah
    lea si,player1_message
    call print_string
    call get_input
    mov byte[bx],'x'
    call CLEAR_SCREEN
    call show_board 
    call find_line
    putc 0dh
    putc 0ah
    lea si,player2_message
    call print_string
    call get_input
    mov byte[bx],'o'
    call CLEAR_SCREEN
    jmp main_loop
    
show_board:
    mov bx,board
    call show_row
    call show_divider
    mov bx,board+3
    call show_row
    call show_divider
    mov bx,board+6
    call show_row   
    ret    
    
show_row:
    call show_square 
    putc 032
    putc '|'
    putc 032
    call show_square
    putc 032
    putc '|'   
    putc 032
    call show_square
    putc 0dh
    putc 0ah
    ret
    
show_divider:
 
    putc '_'
    putc '_'
    putc '_'
    putc '_'
    putc '_'
    putc '_'
    putc '_'
    putc '_'
    putc '_' 
    putc 0dh
    putc 0ah
    putc 0dh
    putc 0ah
    ret
    
show_square:
    mov al,[bx]
    inc bx
    putc al
    ret    
    
get_input:   
    
    mov dx,2 ; 2 refers to the orginal character entered + null character
    mov di, offset position
    call GET_STRING 
    mov al,position
    cmp al,0x1b
    je exit_dos
    cmp al,'1'             
    jc invalid_input ;we got character smaller than '1' , try again 
    cmp al,':'
    jnc invalid_input ;we got character greater than '9' , try again 
    cmp al,'1'
    je  pos1 
    cmp al,'2'
    je  pos2
    cmp al,'3'
    je  pos3  
    cmp al,'4'
    je  pos4
    cmp al,'5'
    je  pos5
    cmp al,'6'
    je  pos6 
    cmp al,'7'
    je  pos7
    cmp al,'8'
    je  pos8
    cmp al,'9'
    je  pos9
    
exit_input:

    putc 0ah
    putc 0dh
    ret

invalid_input:

    putc 0dh
    putc 0ah
    lea si,invalid_input_message
    call print_string
    jmp get_input   ;when we used call, an error happened
    
pos1:
    mov bx,board 
    cmp [bx],'o' 
        je filled_position
    cmp [bx],'x'
        je filled_position
    inc cx        
    jmp exit_input
pos2: 
    mov bx,board+1
    cmp [bx],'o'
        je filled_position
    cmp [bx],'x'
        je filled_position
    inc cx        
    jmp exit_input
pos3:
    mov bx,board+2
    cmp [bx],'o'
        je filled_position
    cmp [bx],'x'
        je filled_position
    inc cx       
    jmp exit_input
pos4:
    mov bx,board+3
    cmp [bx],'o'
        je filled_position
    cmp [bx],'x'
        je filled_position
    inc cx       
    jmp exit_input
pos5: 
    mov bx,board+4 
    cmp [bx],'o'
        je filled_position
    cmp [bx],'x'
        je filled_position
    inc cx        
    jmp exit_input
pos6:
    mov bx,board+5
    cmp [bx],'o'
        je filled_position
    cmp [bx],'x'
        je filled_position
    inc cx        
    jmp exit_input
pos7: 
    mov bx,board+6
    cmp [bx],'o'
        je filled_position
    cmp [bx],'x'
        je filled_position
    inc cx        
    jmp exit_input
pos8: 
    mov bx,board+7
    cmp [bx],'o'
        je filled_position
    cmp [bx],'x'
        je filled_position
    inc cx        
    jmp exit_input
pos9: 
    mov bx,board+8
    cmp [bx],'o'
        je filled_position
    cmp [bx],'x'
        je filled_position
    inc cx        
    jmp exit_input
    
filled_position:

    putc 0dh
    putc 0ah
    lea si,filled_position_message
    call print_string
    jmp get_input   ;when we used call, an error happened    
            
    
find_line:

    ;first row
    mov al,[board]
    cmp al,[board+1]
    jne p03
    cmp al,[board+2]
    je won
    
p03:
    ;leftmost column
    cmp al,[board+3]
    jne p04
    cmp al,[board+6]
    je won
    
p04:
    ;first diagonal
    cmp al,[board+4]
    jne p05
    cmp al,[board+8]
    je won
    
p05:
    ;second row , reload value
    
    mov al,[board+3]
    cmp al,[board+4]
    jne p06
    cmp al,[board+5]
    je won
p06:
    ;third row , reload value
    
    mov al,[board+6]
    cmp al,[board+7]
    jne p01
    cmp al,[board+8]
    je won
                
p01:
    ;middle column , reload value
    
    mov al,[board+1]
    cmp al,[board+4]
    jne p02
    cmp al,[board+7]
    je won 
p02:
    ;rightmost column , reload value
    
    mov al,[board+2]
    cmp al,[board+5]
    jne p07
    cmp al,[board+8]
    je won
    
p07:
    ;second diagonal
    cmp al,[board+4]
    jne p08
    cmp al,[board+6]
    je won
    
p08:
    cmp cx,9
    je game_over
    ret              
    
won: 

   cmp al,'x'
   je player1_won
   
   jmp player2_won
   
player1_won:  

    putc 0dh
    putc 0ah
    print 'congratulations, player 1 win '
    mov al,0x01
    putc al     ;ascii for happy face
    putc 0dh
    putc 0ah
    jmp exit_dos
       
   
player2_won:
     
    putc 0dh
    putc 0ah
    print 'congratulations, player 2 win '
    mov al,0x01   ;ascii for happy face
    putc al     
    putc 0dh
    putc 0ah
    jmp exit_dos
        

game_over:

    putc 0dh
    putc 0ah
    print 'Game Draw'
    jmp exit_dos    
    
exit_dos:

    int 0x20                         

ret
DEFINE_PRINT_STRING
DEFINE_CLEAR_SCREEN
DEFINE_GET_STRING
end
