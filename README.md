ECE_382-Lab1
============
#A SIMPLE CALCULATOR
##Objectives
The purpose of this lab was to program a calculator on the MSP430 using assembly language. 
##Design
In order to design the calcualtor, a flowchart of the desired performance was created.
![alt tag](https://raw.githubusercontent.com/seanbapty/ECE_382-Lab1/master/flowchartpic.JPG)
##Code
After designing a flowchart, the next step in programming the calculator was to write code. The following code was generated to add, subtract, and clear numbers from ROM up to 255. The result is stored in RAM.
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
	add R8, R6
	mov.b R6, 0(R9); stores in memory
	inc.w R9; moves R9 to next available RAM memory location
	inc.w R5
	jmp start

subtraction
	sub R8, R6
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
```
In order to obtain further functionality, the following code was added to check if the inputs were greater than the operable maximum of 255.
```
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

twofityfiveflag
	mov.b #255, R6
	jmp addflag

subsetzero
	mov.b #0, R6
	jmp subflag
```
##Debugging
The main bug in the program occured while trying to write the result of the operation to RAM. The confusing part about this was that the register pointing to RAM must be incremented word-wise (inc.w), however when moving the value to RAM, only a byte should be moved (mov.b).
```
	mov.b R6, 0(R9); stores in memory
	inc.w R9; moves R9 to next available RAM memory location
```
By only moving a byte at a time into RAM, this makes sure there are no "holes" in the memory and all the values are stored at sequential locations.
##Testing
For this lab, the following test cases were provided.
```
0x11, 0x11, 0x11, 0x11, 0x11, 0x44, 0x22, 0x22, 0x22, 0x11, 0xCC, 0x55
Result: 0x22, 0x33, 0x00, 0x00, 0xCC
```
and for the additional functionality,
```
0x11, 0x11, 0x11, 0x11, 0x11, 0x11, 0x11, 0x11, 0xDD, 0x44, 0x08, 0x22, 0x09, 0x44, 0xFF, 0x22, 0xFD, 0x55
Result: 0x22, 0x33, 0x44, 0xFF, 0x00, 0x00, 0x00, 0x02
```
The source code above was run with these cases and several other "fringe" cases (operations that either just exceed the maximum 255 or minimum 0, or are just inside this limit).
