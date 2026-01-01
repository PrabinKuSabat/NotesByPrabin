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

# Source : Write your own OS

- coredumpctl -o coredump dump ./gdbtest
- target core coredump
- gcore \<filename>
- checkpoint  
  creates a checkpoint at the current position of execution or pc.
- restart <checkpoint_id> will restart from that checkpoint
- info threads
- ![[image-103.png]]symbol-file _extracted-debug-file-name_
- gdbserver _adress:port_, _program-name_
- target remote _adress:port_
- actions
- info reg

> intresting stuff  
> asm("int $3") : manual breakpoint or stall command to system
