# GDB Commands

- disable \<br no.>
- dprintf \<line no.>, _printing condition_, variable
  ex: dprintf 8, "var=%d\n" , var
- watch _variable_
  stops when the variable value changes. 
  ex: watch $sp
- help
  gather info about a command 
- obscure
  
- apropos 
  searches all the "helps" for the command
- pytpe
  prints definition of the variable type 
- x/FMT ADDRESS
   
- display 
  prints the value of exression each time the program stops
- bt 
  backtrace
- frame
  select a frame from the backtrace list
- tbreak
- continue \<num>
- set history save on
  set history filename /.gdb_history
- list
  lists 10 lines from current point of exec
- ![[image-99.png]]
- ![[image-101.png]]
- ![[image-102.png]]