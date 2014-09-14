ECE_382-Lab1
============
#A SIMPLE CALCULATOR
##Objectives
The purpose of this lab was to program a calculator on the MSP430 using assembly language. 
##Design
In order to design the calcualtor, a flowchart of the desired performance was created.
![alt tag](https://raw.githubusercontent.com/seanbapty/ECE_382-Lab1/master/flowchartpic.JPG)
##Code
After designing a flowchart, the next step in programming the calculator was to write code. The following code was generated to perform the required functionality.
```
	mov.w#ts, R5
	mov.w #0x0200, R9
	mov.b #0x0001, R10

	mov.b @R5+, R6; value at memory location is put into R6, R5 now points to next value in myProgram
start	mov.b @R5+, R7
	mov.b @R5, R8
	cmp.b #0x11, R7; checks to see if add value is indicated
	jz addition
	cmp.b #0x22, R7; checks to see if subtract is indicated
	jz subtraction
	cmp.b #0x44, R7; checks for clear operation
	jz clear
	cmp.b #0x55, R7; checks for end
	jz end
	cmp.b #0x33, R7
	jz mult

addition
	add R8, R6;
	cmp #255, R6;checks if value exceeds 255
	jhs twofityfiveflag

addflag
	mov.b R6, 0(R9); stores in memory
	inc.w R9; moves R9 to next available RAM memory location
	;add #0x200, R9
	inc.w R5
	jmp start

subtraction
	cmp R6, R8
	jc subsetzero
	sub R8, R6
subflag
	mov.b R6, 0(R9)
	inc.w R9
	inc.w R5
	jmp start

mult
	mov.b R6, R10
	mov.b R6, R11
multLoop
	add R10, R6
	dec.w R11
	jz multLoopEnd
	jmp multLoop
multLoopEnd
	mov.b R6, 0(R9)
	inc.w R9
	inc.w R5
	jmp start
clear
	mov.b #0x00, 0(R9)
	inc.w R9
	inc.w R5
	mov.b R8, R6
	jmp start

end jmp end

twofityfiveflag
	mov.b #255, R6
	jmp addflag

subsetzero
	mov.b #0, R6
	jmp subflag
	```
