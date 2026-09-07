# GATE CSE Master Question Bank — 770 Curated PYQs

## Bank design

- **Coverage:** 70 questions from each of the 11 GATE CSE subjects = **770 questions**.
- **Source window:** GATE CSE 2016–2026, plus one clearly marked 2015 Compiler Design supplement because that subject has only 69 distinct questions in 2016–2026. MCQ, MSQ, NAT, one-mark and two-mark questions are included.
- **Difficulty policy:** source-tagged hard questions, then medium/normal and unlabelled questions, are prioritised. Source-tagged easy questions are used only when required to complete a 70-question subject quota.
- **Weekly sequencing:** 77 blocks of 10 questions. Each block contains 10 distinct subjects; every subject is omitted in exactly 7 blocks and appears in exactly 70 questions overall.
- **Randomisation:** a fixed random seed is used so the sequence is stable and auditable, while preserving weekly subject balance.
- **No answers or solutions are included.**

## Selection summary

| Subject | Selected | Hard | Medium/normal | Unlabelled | Easy included | 2015 supplement |
|---|---:|---:|---:|---:|---:|---:|
| Algorithms | 70 | 0 | 13 | 57 | 0 | 0 |
| Compiler Design | 70 | 0 | 12 | 46 | 12 | 1 |
| Computer Networks | 70 | 0 | 12 | 58 | 0 | 0 |
| Computer Organization and Architecture | 70 | 0 | 15 | 55 | 0 | 0 |
| Databases | 70 | 0 | 16 | 54 | 0 | 0 |
| Digital Logic | 70 | 0 | 18 | 49 | 3 | 0 |
| Engineering Mathematics | 70 | 4 | 33 | 33 | 0 | 0 |
| General Aptitude | 70 | 0 | 26 | 44 | 0 | 0 |
| Operating Systems | 70 | 1 | 14 | 55 | 0 | 0 |
| Programming and Data Structures | 70 | 1 | 22 | 47 | 0 | 0 |
| Theory of Computation | 70 | 2 | 15 | 53 | 0 | 0 |

## Week 1 — 10 questions

**Subject omitted this week:** Digital Logic

### 1.1. Engineering Mathematics — GATE CSE 2016, Set 2, Question 04

**First appearance:** GATE CSE 2016, Set 2  
**Question type:** MCQ  
**Marks:** 1  
**Source difficulty label:** medium

Consider the systems, each consisting of                                         linear equations in    variables.

  I. If              , then all such systems have a solution.
 II. If              , then none of these systems has a solution.
III. If              , then there exists a system which has a solution.

Which one of the following is CORRECT?

 A.      and      are true.                                                                    B. Only and       are true.
 C. Only     is true.                                                                          D. None of them is true.

### 1.2. General Aptitude — GATE CSE 2025, Set 2, Question 9

**First appearance:** GATE CSE 2025, Set 2  
**Question type:** NAT  
**Marks:** 2  
**Source difficulty label:** unlabelled

In the Given figure, PQRS is a square of side cm and PLMN is a rectangle. The corner L of the rectangle
  is on the side QR. Side MN of the rectangle passes through the corner S of the square.

  What is the area (in    ) of the rectangle PLMN?
  Note: The figure shown is representative.

  A.                                          B.                        C.               D.

### 1.3. Computer Networks — GATE CSE 2017, Set 1, Question 32

**First appearance:** GATE CSE 2017, Set 1  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** medium

A computer network uses polynomials over           for error checking with bits as information bits and
  uses              as the generator polynomial to generate the check bits. In this network, the message
            is transmitted as:

  A.                                           B.                                          C.           D.

### 1.4. Programming and Data Structures — GATE CSE 2017, Set 1, Question 53

**First appearance:** GATE CSE 2017, Set 1  
**Question type:** NAT  
**Marks:** 2  
**Source difficulty label:** medium

Consider the following C program.
 #include<stdio.h>

 #include<string.h>

 void printlength(char *s, char *t) {
   unsigned int c=0;
   int len = ((strlen(s) - strlen(t)) > c) ? strlen(s) : strlen(t);
   printf("%d\n", len);
 }

 void main() {
   char *x = "abc";
   char *y = "defgh";
   printlength(x,y);
 }

Recall that        is defined in                                    as returning a value of type                , which is an unsigned int. The output
of the program is __________ .

### 1.5. Computer Organization and Architecture — GATE CSE 2018, Question 23

**First appearance:** GATE CSE 2018  
**Question type:** NAT  
**Marks:** 1  
**Source difficulty label:** medium

A         wide main memory unit with a capacity of          is built using               DRAM chips. The
  number of rows of memory cells in the DRAM chip is      . The time taken to perform one refresh operation is
                     . The refresh period is                 The percentage (rounded to the closest integer) of the
  time available for performing the memory read/write operations in the main memory unit is _________.

### 1.6. Databases — GATE CSE 2025, Set 2, Question 43

**First appearance:** GATE CSE 2025, Set 2  
**Question type:** MSQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

Consider the database transactions T1 and T2 , and data items X and Y . Which of the schedule(s) is/are
  conflict serializable?

  A. R1(X), W2(X), W1(Y), W2(Y), R1(X), W1(X), COMMIT(T2), COMMIT(T1)
  B. W2(X), R1(X), W2(Y), W1(Y), R1(X), COMMIT(T2), W1(X), COMMIT(T1)
  C. R1(X), W1(Y), W2(X), W2(Y), R1(X), W1(X), COMMIT(T1), COMMIT(T2)
  D. W2(X), R1(X), W1(Y), W2(Y), R1(X), COMMIT(T2), W1(X), COMMIT(T1)

### 1.7. Operating Systems — GATE CSE 2026, Set 1, Question 19

**First appearance:** GATE CSE 2026, Set 1  
**Question type:** MSQ  
**Marks:** 1  
**Source difficulty label:** unlabelled

With respect to deadlocks in an operating system, which of the following statements is/are FALSE?

   A. Banker's algorithm is used to prevent deadlocks
   B. Deadlock formation can be prevented by ensuring that the hold and wait condition is not allowed
   C. An assignment edge in a resource allocation graph is marked from a process to a resource
   D. A safe state guarantees that all processes can finish without formation of a deadlock

### 1.8. Compiler Design — GATE CSE 2025, Set 1, Question 3

**First appearance:** GATE CSE 2025, Set 1  
**Question type:** MCQ  
**Marks:** 1  
**Source difficulty label:** easy

Which ONE of the following techniques used in compiler code optimization uses live variable analysis?

  A. Run-time        function                             call                              B. Register assignment to variables
     management
  C. Strength reduction                                                                     D. Constant folding

### 1.9. Algorithms — GATE CSE 2016, Set 1, Question 39

**First appearance:** GATE CSE 2016, Set 1  
**Question type:** NAT  
**Marks:** 2  
**Source difficulty label:** medium

Let be a complete undirected graph on vertices, having edges with weights being                                                       and .
The maximum possible weight that a minimum weight spanning tree of can have is __________

### 1.10. Theory of Computation — GATE CSE 2016, Set 1, Question 43

**First appearance:** GATE CSE 2016, Set 1  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** medium

Consider the transition diagram of a PDA given below with input alphabet            and stack alphabet
            . is the initial stack symbol. Let denote the language accepted by the PDA

Which one of the following is TRUE?

A.                            and is not accepted by any finite automata
B.                                            and is not accepted by any deterministic PDA
C.        is not accepted by any Turing machine that halts on every input
D.                                            and is deterministic context-free


## Week 2 — 10 questions

**Subject omitted this week:** Computer Networks

### 2.1. Algorithms — GATE CSE 2020, Question 31

**First appearance:** GATE CSE 2020  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

Let               be a weighted undirected graph and let be a Minimum Spanning Tree (MST) of
maintained using adjacency lists. Suppose a new weighed edge                     is added to . The worst
case time complexity of determining if is still an MST of the resultant graph is

 A.                                                                                             B.
 C.                                                                                             D.

### 2.2. Databases — GATE CSE 2017, Set 1, Question 16

**First appearance:** GATE CSE 2017, Set 1  
**Question type:** MCQ  
**Marks:** 1  
**Source difficulty label:** medium

The following functional dependencies hold true for the relational schema                                      :

Which of the following is irreducible equivalent for this set of functional dependencies?

 A.                                          B.                             C.                     D.

### 2.3. Digital Logic — GATE CSE 2017, Set 2, Question 27

**First appearance:** GATE CSE 2017, Set 2  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** medium

If                   are Boolean variables, then which one of the following is INCORRECT?

 A.                                                                                          B.
 C.                                                                                          D.

### 2.4. Computer Organization and Architecture — GATE CSE 2020, Question 3

**First appearance:** GATE CSE 2020  
**Question type:** MCQ  
**Marks:** 1  
**Source difficulty label:** unlabelled

Consider the following statements.

  I. Daisy chaining is used to assign priorities in attending interrupts.
 II. When a device raises a vectored interrupt, the CPU does polling to identify the source of interrupt.
III. In polling, the CPU periodically checks the status bits to know if any device needs its attention.
IV. During DMA, both the CPU and DMA controller can be bus masters at the same time.

Which of the above statements is/are TRUE?

 A. I and II only                                                                               B. I and IV only
 C. I and III only                                                                               D. III only

### 2.5. Compiler Design — GATE CSE 2024, Set 2, Question 42

**First appearance:** GATE CSE 2024, Set 2  
**Question type:** MSQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

​Consider a context-free grammar                                  with the following           rules.

Let            . Let                                                          denote the number of times            occur in     , respectively. Which of the
following statements is/are TRUE?

 A.                                                                                            B.
 C.                                                                                            D.

### 2.6. Engineering Mathematics — GATE CSE 2017, Set 1, Question 31

**First appearance:** GATE CSE 2017, Set 1  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** medium

Let     be       real valued square symmetric matrix of rank                                     with                             Consider the
following statements.

  I. One eigenvalue must be in
 II. The eigenvalue with the largest magnitude must be strictly greater than

Which of the above statements about eigenvalues of                                   is/are necessarily CORRECT?

 A. Both I and II                             B. I only                             C. II only                    D. Neither I nor II

### 2.7. General Aptitude — GATE CSE 2016, Set 2, Question 04

**First appearance:** GATE CSE 2016, Set 2  
**Question type:** MCQ  
**Marks:** 1  
**Source difficulty label:** medium

Pick the odd one from the following options.

  A.                                            B.                                   C.               D.

### 2.8. Programming and Data Structures — GATE CSE 2018, Question 3

**First appearance:** GATE CSE 2018  
**Question type:** MCQ  
**Marks:** 1  
**Source difficulty label:** medium

A queue is implemented using a non-circular singly linked list. The queue has a head pointer and a tail
pointer, as shown in the figure. Let     denote the number of nodes in the queue. Let 'enqueue' be
implemented by inserting a new node at the head, and 'dequeue' be implemented by deletion of a node from the
tail.

Which one of the following is the time complexity of the most time-efficient implementation of 'enqueue' and
'dequeue, respectively, for this data structure?

 A.                                                                                           B.
 C.                                                                                           D.

### 2.9. Theory of Computation — GATE CSE 2019, Question 34

**First appearance:** GATE CSE 2019  
**Question type:** MSQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

Consider the following sets:
  S1: Set of all recursively enumerable languages over the alphabet

  S2: Set of all syntactically valid C programs
  S3: Set of all languages over the alphabet
  S4: Set of all non-regular languages over the alphabet
  Which of the above sets are uncountable?

  A. S1 and S2                                 B. S3 and S4                             C. S2 and S3                   D. S1 and S4

### 2.10. Operating Systems — GATE CSE 2022, Question 54

**First appearance:** GATE CSE 2022  
**Question type:** NAT  
**Marks:** 2  
**Source difficulty label:** unlabelled

Consider a demand paging system with four page frames (initially empty) and                                                                    page replacement
  policy. For the following page reference string

  the page fault rate, defined as the ratio of number of page faults to the number of memory accesses
                                      is _____________.


## Week 3 — 10 questions

**Subject omitted this week:** Databases

### 3.1. Digital Logic — GATE CSE 2025, Set 2, Question 33

**First appearance:** GATE CSE 2025, Set 2  
**Question type:** MSQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

​Given the following Karnaugh Map for a Boolean function                                          :

Which one or more of the following Boolean expression(s) represent(s)                                 ?

 A.
 B.
 C.
 D.

### 3.2. Computer Networks — GATE CSE 2025, Set 1, Question 12

**First appearance:** GATE CSE 2025, Set 1  
**Question type:** MSQ  
**Marks:** 1  
**Source difficulty label:** unlabelled

Consider the -way handshaking protocol for TCP connection establishment. Let the three packets
exchanged during the connection establishment be denoted as                , and     , in order. Which of the
following option(s) is/are TRUE with respect to TCP header flags that are set in the packets?

 A.                                                                                B.
 C.        :                                                                       D.

### 3.3. Computer Organization and Architecture — GATE CSE 2017, Set 1, Question 51

**First appearance:** GATE CSE 2017, Set 1  
**Question type:** NAT  
**Marks:** 2  
**Source difficulty label:** medium

Consider a -way set associative cache with     blocks and uses         replacement. Initially the cache is
  empty. Conflict misses are those misses which occur due to the contention of multiple blocks for the same
  cache set. Compulsory misses occur due to first time access to the block. The following sequence of access to
  memory blocks :

  is repeated                times. The number of conflict misses experienced by the cache is _________ .

### 3.4. Compiler Design — GATE CSE 2023, Question 1

**First appearance:** GATE CSE 2023  
**Question type:** MCQ  
**Marks:** 1  
**Source difficulty label:** unlabelled

Consider the following statements regarding the front-end and back-end of a compiler.
S1: The front-end includes phases that are independent of the target hardware.
S2: The back-end includes phases that are specific to the target hardware.
S3: The back-end includes phases that are specific to the programming language used in the source code.
Identify the CORRECT option.

 A. Only           is TRUE.                                                                    B. Only    and      are TRUE.
 C.              , and    are all TRUE.                                                        D. Only    and      are TRUE.

### 3.5. Algorithms — GATE CSE 2017, Set 2, Question 38

**First appearance:** GATE CSE 2017, Set 2  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

Consider the following C function
 int fun(int n) {
    int i, j;
    for(i=1; i<=n; i++) {

         for (j=1; j<n; j+=i) {
            printf("%d %d", i, j);
         }
     }
 }

Time complexity of                       in terms of           notation is

 A.                                                                              B.
 C.                                                                              D.

### 3.6. General Aptitude — GATE CSE 2016, Set 1, Question 10

**First appearance:** GATE CSE 2016, Set 1  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** medium

In a process, the number of cycles to failure decreases exponentially with an increase in load. At a load of
       units, it takes                   cycles for failure. When the load is halved, it takes                            for failure.The load
  for which the failure will happen in                                                 is _____________.

  A.                                             B.                                           C.               D.

### 3.7. Engineering Mathematics — GATE CSE 2016, Set 1, Question 04

**First appearance:** GATE CSE 2016, Set 1  
**Question type:** NAT  
**Marks:** 1  
**Source difficulty label:** medium

A probability density function on the interval                                            is given by        and outside this interval the value of the
  function is zero. The value of is _________.

### 3.8. Theory of Computation — GATE CSE 2018, Question 6

**First appearance:** GATE CSE 2018  
**Question type:** MCQ  
**Marks:** 1  
**Source difficulty label:** medium

Let   be an NFA with states. Let be the number of states of a minimal DFA which is equivalent to                                                                    .
  Which one of the following is necessarily true?

  A.                                              B.                                                C.                                      D.

### 3.9. Programming and Data Structures — GATE CSE 2018, Question 29

**First appearance:** GATE CSE 2018  
**Question type:** NAT  
**Marks:** 2  
**Source difficulty label:** medium

#include<stdio.h>
 void fun1(char* s1, char* s2){
    char* temp;
    temp = s1;
    s1 = s2;
    s2 = temp;
 }
 void fun2(char** s1, char** s2){
    char* temp;
    temp = *s1;
    *s1 = *s2;
    *s2 = temp;
 }
 int main(){
    char *str1="Hi", *str2 = "Bye";
    fun1(str1, str2); printf("%s %s", str1, str2);
    fun2(&str1, &str2); printf("%s %s", str1, str2);
    return 0;
 }

The output of the program above is:

 A.                                                                                            B.
 C.                                                                                            D.

### 3.10. Operating Systems — GATE CSE 2019, Question 33

**First appearance:** GATE CSE 2019  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

Assume that in a certain computer, the virtual addresses are     bits long and the physical addresses are
bits long. The memory is word addressible. The page size is kB and the word size is bytes. The
Translation Look-aside Buffer (TLB) in the address translation path has            valid entries. At most how many
distinct virtual addresses can be translated without any TLB miss?

 A.                                                                                            B.
 C.                                                                                            D.


## Week 4 — 10 questions

**Subject omitted this week:** Algorithms

### 4.1. Programming and Data Structures — GATE CSE 2018, Question 46

**First appearance:** GATE CSE 2018  
**Question type:** NAT  
**Marks:** 2  
**Source difficulty label:** unlabelled

The number of possible min-heaps containing each value from                                                      exactly once is _______

### 4.2. Operating Systems — GATE CSE 2018, Question 9

**First appearance:** GATE CSE 2018  
**Question type:** MCQ  
**Marks:** 1  
**Source difficulty label:** medium

The following are some events that occur after a device controller issues an interrupt while process                                                  is
  under execution.

       P. The processor pushes the process status of onto the control stack
       Q. The processor finishes the execution of the current instruction
       R. The processor executes the interrupt service routine
       S. The processor pops the process status of from the control stack
       T. The processor loads the new PC value based on the interrupt

  Which of the following is the correct order in which the events above occur?

  A. QPTRS                                         B. PTRSQ                                       C. TRPQS                      D. QTPRS

### 4.3. Engineering Mathematics — GATE CSE 2025, Set 1, Question 38

**First appearance:** GATE CSE 2025, Set 1  
**Question type:** MSQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

Which of the following predicate logic formulae/formula is/are CORRECT representation(s) of the statement:
"Everyone has exactly one mother"?

The meanings of the predicates used are:

      mother                       is the mother of
      noteq                       and are not equal

 A.
 B.
 C.
 D.

### 4.4. General Aptitude — GATE CSE 2026, Set 1, Question 7

**First appearance:** GATE CSE 2026, Set 1  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

In Panel of the figure below, the front view and top view of a structure are shown. Which one of the
   structures shown in Panel possesses the views shown in Panel

   A.                                           B.                      C.               D.

### 4.5. Compiler Design — GATE CSE 2018, Question 38

**First appearance:** GATE CSE 2018  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** medium

Consider the following parse tree for the expression a#b c d#e#f, involving two binary operators                      and #.

  Which one of the following is correct for the given parse tree?

  A. $ has higher precedence and is left associative; # is right associative
  B. # has higher precedence and is left associative; $ is right associative
  C. $ has higher precedence and is left associative; # is left associative
  D. $ has higher precedence and is right associative; # is left associative

### 4.6. Digital Logic — GATE CSE 2016, Set 2, Question 09

**First appearance:** GATE CSE 2016, Set 2  
**Question type:** NAT  
**Marks:** 1  
**Source difficulty label:** medium

Let     be the number of distinct -bit integers in     complement representation. Let                                             be the number of
distinct -bit integers in sign magnitude representation Then        is______.

### 4.7. Computer Networks — GATE CSE 2022, Question 47

**First appearance:** GATE CSE 2022  
**Question type:** NAT  
**Marks:** 2  
**Source difficulty label:** unlabelled

Consider a network with three routers                                                   shown in the figure below. All the links have cost of unity.

The routers exchange distance vector routing information and have converged on the routing tables, after which the
link        fails. Assume that and     send out routing updates at random times, each at the same average rate.
The probability of a routing loop formation (rounded off to one decimal place) between and        leading to count-
to-infinity problem, is _______________.

### 4.8. Computer Organization and Architecture — GATE CSE 2017, Set 1, Question 49

**First appearance:** GATE CSE 2017, Set 1  
**Question type:** NAT  
**Marks:** 2  
**Source difficulty label:** medium

Consider a RISC machine where each instruction is exactly bytes long. Conditional and unconditional
  branch instructions use PC-relative addressing mode with Offset specified in bytes to the target location of
  the branch instruction. Further the Offset is always with respect to the address of the next instruction in the program
  sequence. Consider the following instruction sequence

  If the target of the branch instruction is                               then the decimal value of the Offset is ____________ .

### 4.9. Theory of Computation — GATE CSE 2019, Question 7

**First appearance:** GATE CSE 2019  
**Question type:** MCQ  
**Marks:** 1  
**Source difficulty label:** unlabelled

If     is a regular language over                                              , which one of the following languages is NOT regular?

 A.
 B.
 C.                                                                   such that
 D.                                                                   such that

### 4.10. Databases — GATE CSE 2026, Set 1, Question 55

**First appearance:** GATE CSE 2026, Set 1  
**Question type:** NAT  
**Marks:** 2  
**Source difficulty label:** unlabelled

Consider a relational database schema with a relation           . If         and                                                          are the only
  two candidate keys of the relation , then the number of superkeys of relation    is                                                      . (answer in
  integer)


## Week 5 — 10 questions

**Subject omitted this week:** Digital Logic

### 5.1. Theory of Computation — GATE CSE 2023, Question 4

**First appearance:** GATE CSE 2023  
**Question type:** MCQ  
**Marks:** 1  
**Source difficulty label:** unlabelled

Consider the Deterministic Finite-state Automaton (    )    shown below. The         runs on the alphabet
      , and has the set of states           , with being the start state and being the only final state.

Which one of the following regular expressions correctly describes the language accepted by

 A.                                           B.                                              C.                          D.

### 5.2. Operating Systems — GATE CSE 2026, Set 2, Question 41

**First appearance:** GATE CSE 2026, Set 2  
**Question type:** MSQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

Consider three processes          , and    running identical code, as shown in the pseudocode below.
and are two binary semaphores initialized to and , respectively.       is a shared variable initialized to .
Each line in the pseudocode is executed atomically.
      Pseudocode of P1, P2, and P3
            Wait(A);
            Print(*);
            X = X+1;
            If (X == 2)
                {
                  Print($);
                  Signal(B);
                }
            Signal(A);
            Wait(B);
            Print(#);
            Signal(B);

Assume that any of the three processes can start to execute first and context switching can happen between these
processes at any arbitrary time and in any arbitrary order.

Which of the following patterns is/are possible to be generated as an outcome of the execution of these three
processes?

 A.                                         B.                                       C.                     D.

            **$*###                                    **$#*##                              **$##*#                ***$###

### 5.3. General Aptitude — GATE CSE 2020, Question 9

**First appearance:** GATE CSE 2020  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

Two straight lines are drawn perpendicular to each other in                                                    plane. If   and     are the acute angles
  the straight lines make with the  axis, then       is ________.

  A.                                            B.                                             C.                            D.

### 5.4. Databases — GATE CSE 2022, Question 15

**First appearance:** GATE CSE 2022  
**Question type:** MSQ  
**Marks:** 1  
**Source difficulty label:** unlabelled

Consider the following three relations in a relational database.

Which of the following relational algebra expressions return the set of                                      who own all the brands?

 A.
 B.
 C.
 D.

### 5.5. Compiler Design — GATE CSE 2022, Question 3

**First appearance:** GATE CSE 2022  
**Question type:** MCQ  
**Marks:** 1  
**Source difficulty label:** unlabelled

Which one of the following statements is

 A. The              parser for a grammar cannot have reduce-reduce conflict if the                                                    parser for   does not
    have reduce-reduce conflict.
 B. Symbol table is accessed only during the lexical analysis phase.
 C. Data flow analysis is necessary for run-time memory management.
 D.         parsing is sufficient for deterministic context-free languages.

### 5.6. Programming and Data Structures — GATE CSE 2024, Set 2, Question 23

**First appearance:** GATE CSE 2024, Set 2  
**Question type:** MSQ  
**Marks:** 1  
**Source difficulty label:** unlabelled

​Consider the following                 function definition.
        int fX(char *a) {

        char *b = a;

        while (*b)

           b++;

        return b - a; }

  Which of the following statements is/are TRUE?

   A. The function call fX("abcd") will always return a value
   B. Assuming a character array c is declared as char c[]= "abcd" in main (), the function call fX(c) will always return
      a value
   C. The code of the function will not compile
   D. Assuming a character pointer c is declared as char *c="abcd" in main (), the function call fX(c) will always return
      a value

### 5.7. Engineering Mathematics — GATE CSE 2017, Set 1, Question 28

**First appearance:** GATE CSE 2017, Set 1  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** medium

The value of

 A. is                                                  B. is                          C. is                       D. does not exist

### 5.8. Algorithms — GATE CSE 2021, Set 2, Question 49

**First appearance:** GATE CSE 2021, Set 2  
**Question type:** NAT  
**Marks:** 2  
**Source difficulty label:** unlabelled

Consider the following                                 program
       #include <stdio.h>
       int foo(int x, int y, int q)
          {
             if ((x<=0) && (y<=0))
             return q;
             if (x<=0)
             return foo(x, y-q, q);
             if (y<=0)
             return foo(x-q, y, q);
             return foo(x, y-q, q) + foo(x-q, y, q);
          }
       int main( )
       {
          int r = foo(15, 15, 10);
          printf(“%d”, r);
          return 0;
       }

  The output of the program upon execution is _________

### 5.9. Computer Organization and Architecture — GATE CSE 2024, Set 2, Question 21

**First appearance:** GATE CSE 2024, Set 2  
**Question type:** MSQ  
**Marks:** 1  
**Source difficulty label:** unlabelled

​An instruction format has the following structure:
                               Instruction Number: Opcode destination reg, source reg- , source reg-

                       Consider the following sequence of instructions to be executed in a pipelined processor:

Which of the following statements is/are TRUE?

 A. There is a RAW dependency on                                          between               and
 B. There is a WAR dependency on                                          between               and
 C. There is a RAW dependency on                                          between               and
 D. There is a WAW dependency on                                          between               and

### 5.10. Computer Networks — GATE CSE 2018, Question 54

**First appearance:** GATE CSE 2018  
**Question type:** NAT  
**Marks:** 2  
**Source difficulty label:** unlabelled

Consider an IP packet with a length of                 that includes a             header and a
  TCP header. The packet is forwarded to an         router that supports a Maximum Transmission Unit (MTU)
  of           . Assume that the length of the IP header in all the outgoing fragments of this packet is                                    .
  Assume that the fragmentation offset value stored in the first fragment is .

  The fragmentation offset value stored in the third fragment is ________.


## Week 6 — 10 questions

**Subject omitted this week:** Digital Logic

### 6.1. Engineering Mathematics — GATE CSE 2016, Set 1, Question 05

**First appearance:** GATE CSE 2016, Set 1  
**Question type:** NAT  
**Marks:** 1  
**Source difficulty label:** medium

Two eigenvalues of a                            real matrix             are               and . The determinant of       is _______

### 6.2. Operating Systems — GATE CSE 2021, Set 1, Question 46

**First appearance:** GATE CSE 2021, Set 1  
**Question type:** MSQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

Consider the following pseudocode, where is a semaphore initialized to in line                                                 and         is a
shared variable initialized to in line . Assume that the increment operation in line                                         is    atomic.
      1. int counter = 0;
      2. Semaphore S = init(5);
      3. void parop(void)
      4. {
      5.      wait(S);
      6.      wait(S);
      7.      counter++;
      8.      signal(S);
      9.      signal(S);
      10. }

If five threads execute the function                                         concurrently, which of the following program behavior(s) is/are possible?

 A. The value of          is after all the threads successfully complete the execution of
 B. The value of          is after all the threads successfully complete the execution of
 C. The value of          is after all the threads successfully complete the execution of
 D. There is a deadlock involving all the threads

### 6.3. General Aptitude — GATE CSE 2016, Set 2, Question 07

**First appearance:** GATE CSE 2016, Set 2  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** medium

Computers were invented for performing only high-end useful computations. However, it is no
understatement that they have taken over our world today. The internet, for example, is ubiquitous. Many
believe that the internet itself is an unintended consequence of the original invention. With the advent of mobile
computing on our phones, a whole new dimension is now enabled. One is left wondering if all these developments
are good or, more importantly, required.
Which of the statement(s) below is/are logically valid and can be inferred from the above paragraph?
(i) The author believes that computers are not good for us.
(ii) Mobile computers and the internet are both intended inventions.

 A. (i) only                                                           B. (ii) only
 C. Both (i) and (ii)                                                  D. Neither (i) nor (ii)

### 6.4. Compiler Design — GATE CSE 2021, Set 2, Question 51

**First appearance:** GATE CSE 2021, Set 2  
**Question type:** NAT  
**Marks:** 2  
**Source difficulty label:** unlabelled

Consider the following augmented grammar with                                                                  as the set of terminals.

Let                                                             . The number of items in the set                                          is ___________

### 6.5. Algorithms — GATE CSE 2026, Set 1, Question 39

**First appearance:** GATE CSE 2026, Set 1  
**Question type:** MSQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

Let           be a simple, undirected, edge-weighted graph with unique edge weights.
  Which of the following statements about the minimum spanning trees (MST) of is/are true?

  A. In every cycle of                     , the edge with the largest weight in is not in any MST
  B. In every cycle of                     , the edge with the smallest weight in is in every MST
  C. For every vertex                         , the edge with the largest weight incident on is not in any MST
  D. For every vertex                         , the edge with the smallest weight incident on is in every MST

### 6.6. Theory of Computation — GATE CSE 2017, Set 2, Question 39

**First appearance:** GATE CSE 2017, Set 2  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

L e t denote the transition function and                                denote the extended transition function of the -NFA whose
transition table is given below:

Then                        is

 A.                                                                                 B.
 C.                                                                                 D.

### 6.7. Programming and Data Structures — GATE CSE 2018, Question 32

**First appearance:** GATE CSE 2018  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** medium

Consider the following C code. Assume that unsigned long int type length is                   bits.
 unsigned long int fun(unsigned long int n) {
      unsigned long int i, j=0, sum = 0;
      for( i=n; i>1; i=i/2) j++;
      for( ; j>1; j=j/2) sum++;
      return sum;
 }

The value returned when we call fun with the input                                      is:

 A.                                           B.                                        C.      D.

### 6.8. Computer Networks — GATE CSE 2017, Set 2, Question 18

**First appearance:** GATE CSE 2017, Set 2  
**Question type:** NAT  
**Marks:** 1  
**Source difficulty label:** unlabelled

Consider socket API on a Linux machine that supports connected UDP sockets. A connected UDP socket is
  a UDP socket on which connect function has already been called. Which of the following statements is/are
  CORRECT?

   I. A connected UDP socket can be used to communicate with multiple peers simultaneously.
  II. A process can successfully call connect function again for an already connected UDP socket.

  A.     only                                   B.       only                               C. Both and                   D. Neither nor

### 6.9. Computer Organization and Architecture — GATE CSE 2016, Set 2, Question 30

**First appearance:** GATE CSE 2016, Set 2  
**Question type:** NAT  
**Marks:** 2  
**Source difficulty label:** medium

Suppose the functions   and can be computed in and nanoseconds by functional units             and   ,
respectively. Given two instances of      and two instances of       , it is required to implement the
computation            for           . Ignoring all other delays, the minimum time required to complete this
computation is ____________ nanoseconds.

### 6.10. Databases — GATE CSE 2025, Set 2, Question 44

**First appearance:** GATE CSE 2025, Set 2  
**Question type:** MSQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

Consider the following relational schema:
Students (                                    name: string, age: integer, cgpa: real)
Courses (                                          cname: string, credits: integer)
Enrolled (                                                                        grade: string)
Which of the following options is/are correct SQL query/queries to retrieve the names of the students enrolled in
course number (i.e., courseno)       ?
      A. SELECT S.name
        FROM Students S
        WHERE EXISTS (SELECT * FROM Enrolled E
              WHERE E.courseno = 1470
                    AND E.rollno = S.rollno);

      B. SELECT S.name
        FROM Students S
        WHERE SIZEOF (SELECT * FROM Enrolled E
              WHERE E.courseno = 1470
                   AND E.rollno = S.rollno) > 0;

      C. SELECT S.name
       FROM Students S
       WHERE 0 < (SELECT COUNT(*)
             FROM Enrolled E
             WHERE E.courseno = 1470
                  AND E.rollno = S.rollno);

      D. SELECT S.name
       FROM Students S NATURAL JOIN Enrolled E
       WHERE E.courseno = 1470;


## Week 7 — 10 questions

**Subject omitted this week:** General Aptitude

### 7.1. Compiler Design — GATE CSE 2015, Set 1, Question 13

**First appearance:** GATE CSE 2015, Set 1  
**Question type:** MCQ  
**Marks:** 1  
**Source difficulty label:** medium

Which one of the following is TRUE at any valid state in shift-reduce parsing?

 A. Viable prefixes appear only at the bottom of the stack and not inside
 B. Viable prefixes appear only at the top of the stack and not inside
 C. The stack contains only a set of viable prefixes
 D. The stack never contains viable prefixes

### 7.2. Databases — GATE CSE 2021, Set 2, Question 6

**First appearance:** GATE CSE 2021, Set 2  
**Question type:** MSQ  
**Marks:** 1  
**Source difficulty label:** unlabelled

Consider the following statements                                      and           about the relational data model:

             : A relation scheme can have at most one foreign key.
             : A foreign key in a relation scheme cannot be used to refer to tuples of

  Which one of the following choices is correct?

  A. Both      and   are true                                                                       B.    is true and     is false
  C.    is false and   is true                                                                      D. Both     and      are false

### 7.3. Algorithms — GATE CSE 2020, Question 48

**First appearance:** GATE CSE 2020  
**Question type:** NAT  
**Marks:** 2  
**Source difficulty label:** unlabelled

Consider the following C functions.

       int tob (int b, int* arr) {
                                                                                                    int pp(int a, int b) {
          int i;
                                                                                                       int arr[20];
          for (i = 0; b>0; i++) {
                                                                                                       int i, tot = 1, ex, len;
             if (b%2) arr [i] = 1;
                                                                                                       ex = a;
             else      arr[i] = 0;
                                                                                                       len = tob(b, arr);
             b = b/2;
                                                                                                       for (i=0; i<len ; i++) {
          }
                                                                                                           if (arr[i] ==1)
          return (i);
                                                                                                               tot = tot * ex;
       }
                                                                                                           ex= ex*ex;
                                                                                                       }
                                                                                                    return (tot) ;
                                                                                                    }

The value returned by                                     is _______.

### 7.4. Engineering Mathematics — GATE CSE 2018, Question 1

**First appearance:** GATE CSE 2018  
**Question type:** MCQ  
**Marks:** 1  
**Source difficulty label:** medium

Which one of the following is a closed form expression for the generating function of the sequence                                                      ,
  where                                     ?

  A.                                              B.                                           C.                              D.

### 7.5. Programming and Data Structures — GATE CSE 2016, Set 2, Question 40

**First appearance:** GATE CSE 2016, Set 2  
**Question type:** NAT  
**Marks:** 2  
**Source difficulty label:** medium

The number of ways in which the numbers                                                             can be inserted in an empty binary search tree,
such that the resulting tree has height , is _________.

Note: The height of a tree with a single node is .

### 7.6. Digital Logic — GATE CSE 2025, Set 1, Question 50

**First appearance:** GATE CSE 2025, Set 1  
**Question type:** MSQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

Consider the given sequential circuit designed using D-Flip-flops. The circuit is initialized with some value
(initial state). The number of distinct states the circuit will go through before returning back to the initial state
is ___________. (Answer in integer)

### 7.7. Computer Networks — GATE CSE 2016, Set 1, Question 53

**First appearance:** GATE CSE 2016, Set 1  
**Question type:** NAT  
**Marks:** 2  
**Source difficulty label:** medium

An IP datagram of size            arrives at a router. The router has to forward this packet on a link whose
MTU (maximum transmission unit) is              . Assume that the size of the IP header is

The number of fragments that the IP datagram will be divided into for transmission is________.

### 7.8. Computer Organization and Architecture — GATE CSE 2021, Set 2, Question 53

**First appearance:** GATE CSE 2021, Set 2  
**Question type:** NAT  
**Marks:** 2  
**Source difficulty label:** unlabelled

Consider a pipelined processor with           stages,                       ,                           ,
               ,                         , and                   . Each stage of the pipeline, except the
    stage, takes one cycle. Assume that the       stage merely decodes the instruction and the register read is
performed in the      stage. The    stage takes one cycle for     instruction and two cycles for      instruction.
Ignore pipeline register latencies.

Consider the following sequence of                               instructions:

Assume that every            instruction is data-dependent on the         instruction just before it and every
instruction (except the first       ) is data-dependent on the     instruction just before it. The         defined as
follows.

The            achieved in executing the given instruction sequence on the pipelined processor (rounded to
decimal places) is _____________

### 7.9. Operating Systems — GATE CSE 2017, Set 2, Question 51

**First appearance:** GATE CSE 2017, Set 2  
**Question type:** NAT  
**Marks:** 2  
**Source difficulty label:** unlabelled

Consider the set of process with arrival time (in milliseonds), CPU burst time (in millisecods) and priority ( is
the highest priority) shown below. None of the process have I/O burst time

The average waiting time (in milli seconds) of all the process using premtive priority scheduling algorithm is ______

### 7.10. Theory of Computation — GATE CSE 2017, Set 1, Question 10

**First appearance:** GATE CSE 2017, Set 1  
**Question type:** MCQ  
**Marks:** 1  
**Source difficulty label:** medium

Consider the following context-free grammar over the alphabet                           with   as the start symbol:

Which one of the following represents the language generated by the above grammar?

 A.
 B.
 C.
 D.


## Week 8 — 10 questions

**Subject omitted this week:** Programming and Data Structures

### 8.1. General Aptitude — GATE CSE 2025, Set 2, Question 10

**First appearance:** GATE CSE 2025, Set 2  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

The diagram below shows a river system consisting of segments, marked                            and     It
  splits the land into zones, marked                       and    . We need to connect these zones using the
  least number of bridges, Out of the following options, which one is correct?

  Note: The figure shown is representative.

  A. Bridges on                    and                                           B. Bridges on   and
  C. Bridges on                      and                                         D. Bridges on      and

### 8.2. Engineering Mathematics — GATE CSE 2024, Set 1, Question 39

**First appearance:** GATE CSE 2024, Set 1  
**Question type:** MSQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

​Let be any                           matrix, where                        . Which of the following statements is/are TRUE about the system
 of linear equations                        ?

 A. There exist at least          linearly independent solutions to this system
 B. There exist          linearly independent vectors such that every solution is a linear combination of these vectors
 C. There exists a non-zero solution in which at least          variables are
 D. There exists a solution in which at least variables are non-zero

### 8.3. Databases — GATE CSE 2016, Set 1, Question 23

**First appearance:** GATE CSE 2016, Set 1  
**Question type:** MCQ  
**Marks:** 1  
**Source difficulty label:** medium

A database of research articles in a journal uses the following schema.

The primary key is '
and the following functional dependencies exist in the schema.

The database is redesigned to use the following schemas

Which is the weakest normal form that the new database satisfies, but the old one does not?

 A.                                         B.                                          C.                                   D.

### 8.4. Computer Networks — GATE CSE 2021, Set 1, Question 29

**First appearance:** GATE CSE 2021, Set 1  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

Assume             that     a       -bit      Hamming codeword consisting of -bit data and                     check bits               is
                                                  , where the data bits and the check bits are given in the following tables:

  Which one of the following choices gives the correct values of                                          and ?

  A.      is     and      is                 B.      is       and        is                     C.   is   and   is     D.   is   and   is

### 8.5. Theory of Computation — GATE CSE 2018, Question 35

**First appearance:** GATE CSE 2018  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** medium

Consider the following languages:

  I.
 II.
III.
IV.

Which of the above languages are context-free?

 A. I and IV only                            B. I and II only                             C. II and III only            D. II and IV only

### 8.6. Digital Logic — GATE CSE 2023, Question 22

**First appearance:** GATE CSE 2023  
**Question type:** NAT  
**Marks:** 1  
**Source difficulty label:** unlabelled

A particular number is written as                                 in radix- representation. The same number in radix- representation
is _____________.

### 8.7. Compiler Design — GATE CSE 2020, Question 9

**First appearance:** GATE CSE 2020  
**Question type:** MCQ  
**Marks:** 1  
**Source difficulty label:** unlabelled

Consider the following statements.

  I. Symbol table is accessed only during lexical analysis and syntax analysis.
 II. Compilers for programming languages that support recursion necessarily need heap storage for memory
     allocation in the run-time environment.

III. Errors violating the condition ‘any variable must be declared before its use’ are detected during syntax analysis.

  Which of the above statements is/are TRUE?

 A. I only                                                                                     B. I and III only
 C. II only                                                                                     D. None of I, II and III

### 8.8. Algorithms — GATE CSE 2026, Set 2, Question 28

**First appearance:** GATE CSE 2026, Set 2  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

Consider an array of integers of size . The indices of                                run from    to . An algorithm is to be designed
to check whether satisfies the condition given below.
                                         such that
Which one of the following gives the worst case time complexity of the fastest algorithm that can be designed for
the problem?

 A.                                                                              B.
 C.                                                                              D.

### 8.9. Operating Systems — GATE CSE 2021, Set 2, Question 42

**First appearance:** GATE CSE 2021, Set 2  
**Question type:** MSQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

Consider the following multi-threaded code segment (in a mix of C and pseudo-code), invoked by two
processes    and     , and each of the processes spawns two threads and :
      int x = 0; // global
      Lock L1; // global
      main () {
         create a thread to execute foo(); // Thread T1
         create a thread to execute foo(); // Thread T2
         wait for the two threads to finish execution;
         print(x);}

      foo() {
        int y = 0;
        Acquire L1;
        x = x + 1;
        y = y + 1;
        Release L1;
        print (y);}

Which of the following statement(s) is/are correct?

 A. Both     and      will print the value of as
 B. At least of    and       will print the value of as
 C. At least one of the threads will print the value of as
 D. Both     and , in both the processes, will print the value of                               as

### 8.10. Computer Organization and Architecture — GATE CSE 2017, Set 2, Question 29

**First appearance:** GATE CSE 2017, Set 2  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** medium

In a two-level cache system, the access times of  and     caches are and clock cycles, respectively.
The miss penalty from the     cache to main memory is   clock cycles. The miss rate of cache is twice
that of   . The average memory access time (AMAT) of this cache system is cycles. The miss rates of                                             and
    respectively are

 A.             and                           B.             and                         C.       and               D.          and


## Week 9 — 10 questions

**Subject omitted this week:** Computer Organization and Architecture

### 9.1. Digital Logic — GATE CSE 2020, Question 28

**First appearance:** GATE CSE 2020  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** medium

Consider the Boolean function                                        .

      Which one of the following minterm lists represents the circuit given above?

  A.                                                                                            B.
  C.                                                                                            D.

### 9.2. Engineering Mathematics — GATE CSE 2026, Set 2, Question 4

**First appearance:** GATE CSE 2026, Set 2  
**Question type:** MSQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

The probability density function                                   of a random variable          which takes real values is

Which one of the following statements is correct about the random variable                                          ?

 A.      is an exponential random                                                           B.    is a normal random variable
    variable
 C.    is a Poisson random variable                                                         D.    is a uniform random variable

### 9.3. General Aptitude — GATE CSE 2021, Set 1, Question 7

**First appearance:** GATE CSE 2021, Set 1  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

₹                                ₹

  Details of prices of two items and are presented in the above table. The ratio of cost of item      to cost of item
     is      . Discount is calculated as the difference between the marked price and the selling price. The profit
  percentage is calculated as the ratio of the difference between selling price and cost, to the cost

  The discount on item                      , as a percentage of its marked price, is _______

  A.                                              B.                                      C.                         D.

### 9.4. Databases — GATE CSE 2017, Set 2, Question 44

**First appearance:** GATE CSE 2017, Set 2  
**Question type:** NAT  
**Marks:** 2  
**Source difficulty label:** unlabelled

Two transactions                  and          are given as

where         denotes a       operation by transaction  on a variable      and         denotes a        operation
by transaction   on a variable . The total number of conflict serializable schedules that can be formed by   and
   is ______

### 9.5. Operating Systems — GATE CSE 2021, Set 2, Question 48

**First appearance:** GATE CSE 2021, Set 2  
**Question type:** NAT  
**Marks:** 2  
**Source difficulty label:** unlabelled

Consider a three-level page table to translate a                                                bit virtual address to a physical address as shown
below:

The page size is                   bytes and page table entry size at every level is bytes. A process     is
currently using                 bytes virtual memory which is mapped to             of physical memory. The
minimum amount of memory required for the page table of across all levels is _________    .

### 9.6. Computer Networks — GATE CSE 2019, Question 29

**First appearance:** GATE CSE 2019  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

Suppose that in an IP-over-Ethernet network, a machine X wishes to find the MAC address of another
machine Y in its subnet. Which one of the following techniques can be used for this?

 A. X sends an ARP request packet to the local gateway’s IP address which then finds the MAC address of Y and
    sends to X
 B. X sends an ARP request packet to the local gateway’s MAC address which then finds the MAC address of Y
    and sends to X
 C. X sends an ARP request packet with broadcast MAC address in its local subnet
 D. X sends an ARP request packet with broadcast IP address in its local subnet

### 9.7. Theory of Computation — GATE CSE 2026, Set 2, Question 37

**First appearance:** GATE CSE 2026, Set 2  
**Question type:** MSQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

Consider the following two finite automata                                     and        .

Which of the following statements is/are true?

 A.
 B.                  is a proper subset of
 C.
 D.                                          consists of all strings in                          whose length is divisible by

### 9.8. Algorithms — GATE CSE 2026, Set 1, Question 31

**First appearance:** GATE CSE 2026, Set 1  
**Question type:** NAT  
**Marks:** 2  
**Source difficulty label:** unlabelled

Let           be an undirected, edge-weighted graph with integer weights. The weight of a path is the sum
of the weights of the edges in that path. The length of a path is the number of edges in that path.

Let        be a vertex in . For every     and for every      , let      denote the weight of a shortest path
(in terms of weight) from to of length at most . If there is no path from to of length at most , then
            .

Consider the statements:

S1: For every                        and                                                 .

S2: For every                                     , if              is part of a shortest path (in terms of weight) from                to , then for every
                                         .

Which one of the following options is correct?

 A. Only             is true                                                                   B. Only    is true
 C. Both             and     are true                                                          D. Neither     nor   is true

### 9.9. Compiler Design — GATE CSE 2018, Question 8

**First appearance:** GATE CSE 2018  
**Question type:** MCQ  
**Marks:** 1  
**Source difficulty label:** easy

Which one of the following statements is FALSE?

 A. Context-free grammar can be used to specify both lexical and syntax rules
 B. Type checking is done before parsing
 C. High-level language programs can be translated to different Intermediate Representations
 D. Arguments to a function can be passed using the program stack

### 9.10. Programming and Data Structures — GATE CSE 2025, Set 2, Question 52

**First appearance:** GATE CSE 2025, Set 2  
**Question type:** NAT  
**Marks:** 2  
**Source difficulty label:** unlabelled

Consider the following C program:
      #include<stdio.h>
      int main(){
          int a;
          int arr[5] = {30,50,10};
          int *ptr;
          ptr = &arr[0] + 1;
          a = *ptr;
          (*ptr)++;
          ptr++;
          printf("%d", a + (*ptr) + arr[1]);
          return 0;
      }

The output of the above program is _________. (Answer in integer)


## Week 10 — 10 questions

**Subject omitted this week:** Compiler Design

### 10.1. Programming and Data Structures — GATE CSE 2025, Set 2, Question 3

**First appearance:** GATE CSE 2025, Set 2  
**Question type:** MSQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

Consider a binary tree                         in which every node has either zero or two children. Let                         be the number of
nodes in .
Which ONE of the following is the number of nodes in                                          that have exactly two children?

 A.                                               B.                                     C.                           D.

### 10.2. Computer Networks — GATE CSE 2022, Question 50

**First appearance:** GATE CSE 2022  
**Question type:** NAT  
**Marks:** 2  
**Source difficulty label:** unlabelled

Consider the data transfer using       over a       link. Assuming that the maximum segment lifetime
       is set to              the minimum number of bits required for the sequence number field of the
       header, to prevent the sequence number space from wrapping around during the                                                                    is
________________.

### 10.3. Algorithms — GATE CSE 2019, Question 37

**First appearance:** GATE CSE 2019  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

There are unsorted arrays:                  . Assume that is odd.Each of                    contains
distinct elements. There are no common elements between any two arrays. The worst-case time complexity
of computing the median of the medians of               is

 A.                                                                              B.
 C.                                                                              D.

### 10.4. Theory of Computation — GATE CSE 2017, Set 1, Question 37

**First appearance:** GATE CSE 2017, Set 1  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** medium

Consider the context-free grammars over the alphabet                                                      given below.      and    are non-terminals.

The language                                         is

 A. Finite                                                                                       B. Not finite but regular
 C. Context-Free but not regular                                                                 D. Recursive but not context-free

### 10.5. Computer Organization and Architecture — GATE CSE 2017, Set 1, Question 11

**First appearance:** GATE CSE 2017, Set 1  
**Question type:** MCQ  
**Marks:** 1  
**Source difficulty label:** unlabelled

Consider the                struct defined below:
 struct data {
    int marks [100];
    char grade;
    int cnumber;
 };
 struct data student;

The base address of student is available in register                                      . The field student.grade can be accessed efficiently using:

 A. Post-increment addressing mode,
 B. Pre-decrement addressing mode,
 C. Register direct addressing mode,
 D. Index addressing mode,         , where                                        is an offset represented in   complement               representation

### 10.6. Digital Logic — GATE CSE 2016, Set 2, Question 08

**First appearance:** GATE CSE 2016, Set 2  
**Question type:** MCQ  
**Marks:** 1  
**Source difficulty label:** medium

Let,                                               where                                are Boolean variables, and   is the XOR operator.
Which one of the following must always be TRUE?

 A.
 B.
 C.
 D.

### 10.7. Databases — GATE CSE 2025, Set 2, Question 36

**First appearance:** GATE CSE 2025, Set 2  
**Question type:** MSQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

​Consider the following relational schema along with all the functional dependencies that hold on them.

 Which of the following statement(s) is/are TRUE?

 A.          is in NF                                                                           B.     is in NF
 C.          is NOT in             NF                                                           D.     is NOT in   NF

### 10.8. Operating Systems — GATE CSE 2025, Set 1, Question 28

**First appearance:** GATE CSE 2025, Set 1  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

A computer has two processors,            and      . Four processes                    with CPU bursts of
          , and    milliseconds, respectively, arrive at the same time and these are the only processes in
the system. The scheduler uses non-preemptive priority scheduling, with priorities decided as follows:

          uses priority of execution for the processes as,                                                                    , i.e.,   and   have highest and
      lowest priorities, respectively.
          uses priority of execution for the processes as,                                                                    , i.e.,   and   have highest and
      lowest priorities, respectively.

A process      is scheduled to a processor     , if the processor is free and no other process    is waiting with higher
priority. At any given point of time, a process can be allocated to any one of the free processors without violating
the execution priority rules. Ignore the context switch time. What will be the average waiting time of the processes in
milliseconds?

 A.                                              B.                                              C.                              D.

### 10.9. General Aptitude — GATE CSE 2018, Question 3

**First appearance:** GATE CSE 2018  
**Question type:** MCQ  
**Marks:** 1  
**Source difficulty label:** medium

The area of a square is . What is the area of the circle which has the diagonal of the square as its
  diameter?

  A.                                            B.                                             C.                            D.

### 10.10. Engineering Mathematics — GATE CSE 2019, Question 35

**First appearance:** GATE CSE 2019  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** medium

Consider the first order predicate formula                                     :

Here                 denotes that                                      , where         and        are integers. Consider the following sets:

                Set of all positive integers
                Set of all integers

Which of the above sets satisfy                             ?

 A.        and                                    B.        and                                   C.        and              D.         and


## Week 11 — 10 questions

**Subject omitted this week:** Databases

### 11.1. Programming and Data Structures — GATE CSE 2022, Question 33

**First appearance:** GATE CSE 2022  
**Question type:** NAT  
**Marks:** 2  
**Source difficulty label:** unlabelled

What is printed by the following                                         program?
      #include<stdio.h>

      int main (int argc, char *argv[])

      {

              int a[3][3][3] =

              {{1, 2, 3, 4, 5, 6, 7, 8, 9},
               {10, 11, 12, 13, 14, 15, 16, 17, 18},
               {19, 20, 21, 22, 23, 24, 25, 26, 27}};

          int i = 0, j = 0, k = 0;

          for ( i = 0; i < 3; i ++) {

              for ( k = 0; k < 3; k++)

                 printf(“%d”, a[i][j][k]);

              printf (“\n”);

          }

          return 0;

      }

 A.

 B.

 C.

 D.

### 11.2. Theory of Computation — GATE CSE 2025, Set 1, Question 18

**First appearance:** GATE CSE 2025, Set 1  
**Question type:** MSQ  
**Marks:** 1  
**Source difficulty label:** unlabelled

A regular language is accepted by a non-deterministic finite automaton (NFA) with                                              states. Which of the
following statement(s) is/are FALSE?

 A.   may have an accepting NFA with      states.
 B.   may have an accepting DFA with      states.
 C. There exists a DFA with    states that accepts                                              .
 D. Every DFA that accepts has        states.

### 11.3. Digital Logic — GATE CSE 2017, Set 2, Question 1

**First appearance:** GATE CSE 2017, Set 2  
**Question type:** MCQ  
**Marks:** 1  
**Source difficulty label:** unlabelled

The representation of the value of a          unsigned integer                                             in hexadecimal number system is         .
The representation of the value of   in octal number system is

 A.                                             B.                                           C.                           D.

### 11.4. Operating Systems — GATE CSE 2026, Set 1, Question 53

**First appearance:** GATE CSE 2026, Set 1  
**Question type:** NAT  
**Marks:** 2  
**Source difficulty label:** unlabelled

Consider the following program snippet. Assume that the program compiles and runs successfully. Further,
assume that the fork() system call is always successful in creating a process.
      int main () {
         int i;
         for (i = 0; i < 3; i++) {
            if (fork() == 0) {
                continue;
            }
            break;
         }
         printf("Hello!");
         return 0;
      }

The total number of times that the printf statement gets executed is                                            . (answer in integer)

### 11.5. Compiler Design — GATE CSE 2021, Set 1, Question 5

**First appearance:** GATE CSE 2021, Set 1  
**Question type:** MCQ  
**Marks:** 1  
**Source difficulty label:** unlabelled

Consider the following statements.

                 Every           grammar is unambiguous but there are certain unambiguous grammars that are not
                   .
                For any context-free grammar, there is a parser that takes at most time to parse a string of length .

Which one of the following options is correct?

 A.        is true and            is false                                                    B.     is false and   is true
 C.        is true and            is true                                                     D.     is false and   is false

### 11.6. General Aptitude — GATE CSE 2016, Set 2, Question 10

**First appearance:** GATE CSE 2016, Set 2  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** medium

A.                                                                                                  B.
 C.                                                                                                  D.

### 11.7. Computer Networks — GATE CSE 2024, Set 1, Question 6

**First appearance:** GATE CSE 2024, Set 1  
**Question type:** NAT  
**Marks:** 1  
**Source difficulty label:** unlabelled

A user starts browsing a webpage hosted at a remote server. The browser opens a single TCP connection
to fetch the entire webpage from the server. The webpage consists of a top-level index page with multiple
embedded image objects. Assume that all caches (e.g., DNS cache, browser cache) are all initially empty. The
following packets leave the user's computer in some order.

   i. HTTP GET request for the index page
  ii. DNS request to resolve the web server's name to its IP address
 iii. HTTP GET request for an image object
 iv. TCP SYN to open a connection to the web server

Which one of the following is the CORRECT chronological order (earliest in time to latest) of the packets leaving the
computer?

 A.                                                                                         B.
 C.                                                                                         D.

### 11.8. Engineering Mathematics — GATE CSE 2018, Question 27

**First appearance:** GATE CSE 2018  
**Question type:** NAT  
**Marks:** 2  
**Source difficulty label:** medium

Let           be the set of natural numbers. Consider the following sets,

                Set of Rational numbers (positive and negative)
                Set of functions from       to
                Set of functions from    to
                Set of finite subsets of

  Which of the above sets are countable?

  A.         and       only                          B.         and        only                     C.        and   only                 D.      and   only

### 11.9. Computer Organization and Architecture — GATE CSE 2017, Set 1, Question 54

**First appearance:** GATE CSE 2017, Set 1  
**Question type:** NAT  
**Marks:** 2  
**Source difficulty label:** medium

A cache memory unit with capacity of      words and block size of words is to be designed. If it is designed
as a direct mapped cache, the length of the        field is    bits. If the cache unit is now designed as a -
way set-associative cache, the length of the      field is ____________ bits.

### 11.10. Algorithms — GATE CSE 2021, Set 2, Question 26

**First appearance:** GATE CSE 2021, Set 2  
**Question type:** MSQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

Consider the string                                                . Each letter in the string must be assigned a binary code satisfying the
  following properties:

  1. For any two letters, the code assigned to one letter must not be a prefix of the code assigned to the other letter.
  2. For any two letters of the same frequency, the letter which occurs earlier in the dictionary order is assigned a
     code whose length is at most the length of the code assigned to the other letter.

  Among the set of all binary code assignments which satisfy the above two properties, what is the minimum length
  of the encoded string?

  A.                                                   B.                                    C.                         D.


## Week 12 — 10 questions

**Subject omitted this week:** Operating Systems

### 12.1. Computer Networks — GATE CSE 2021, Set 2, Question 7

**First appearance:** GATE CSE 2021, Set 2  
**Question type:** MCQ  
**Marks:** 1  
**Source difficulty label:** unlabelled

Consider the three-way handshake mechanism followed during             connection establishment between
hosts      and . Let      and     be two random -bit starting sequence numbers chosen by          and
respectively. Suppose    sends a         connection request message to    with a       segment having     bit
     ,       number      , and          bit     . Suppose    accepts the connection request. Which one of the
following choices represents the information present in the    segment header that is sent by to ?

    A.             bit          ,             number                              ,           bit         ,      number     ,         bit
    B.             bit          ,             number                              ,           bit         ,      number     ,         bit
    C.             bit          ,             number                   ,                bit      ,            number        ,         bit
    D.             bit          ,             number                   ,                bit      ,            number    ,       bit

### 12.2. Algorithms — GATE CSE 2022, Question 48

**First appearance:** GATE CSE 2022  
**Question type:** NAT  
**Marks:** 2  
**Source difficulty label:** unlabelled

Let           be a directed graph, where                                                               is the set of vertices and     is the set of
directed edges, as defined by the following adjacency matrix

           indicates a directed edge from node to node A                         of    rooted at         is
defined as a subgraph of such that the undirected version of is a tree, and contains a directed path from
to every other vertex in          The number of such directed spanning trees rooted at vertex            is
__________________.

### 12.3. Digital Logic — GATE CSE 2021, Set 2, Question 5

**First appearance:** GATE CSE 2021, Set 2  
**Question type:** MCQ  
**Marks:** 1  
**Source difficulty label:** unlabelled

Which one of the following circuits implements the Boolean function given below?
                                                                                         , where     is the   minterm.

 A.                                                                                             B.

 C.                                                                                             D.

### 12.4. Engineering Mathematics — GATE CSE 2016, Set 2, Question 28

**First appearance:** GATE CSE 2016, Set 2  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** hard

Consider a set    of    different compounds in a chemistry lab. There is a subset of                                                        of   compounds,
each of which reacts with exactly compounds of . Consider the following statements:

  I. Each compound in U \ S reacts with an odd number of compounds.
 II. At least one compound in U \ S reacts with an odd number of compounds.
III. Each compound in U \ S reacts with an even number of compounds.

Which one of the above statements is ALWAYS TRUE?

 A. Only I                                   B. Only II                                        C. Only III                     D. None.

### 12.5. Computer Organization and Architecture — GATE CSE 2022, Question 14

**First appearance:** GATE CSE 2022  
**Question type:** MSQ  
**Marks:** 1  
**Source difficulty label:** unlabelled

Let      and                     be two set associative cache organizations that use      algorithm for cache block
replacement.                     is a write back cache and        is a write through cache. Which of the following
statements is/are

A. Each cache block in          and       has a dirty bit.
B. Every write hit in      leads to a data transfer from cache to main memory.
C. Eviction of a block from        will not lead to data transfer from cache to main memory.
D. A read miss in        will never lead to eviction of a dirty block from

### 12.6. Databases — GATE CSE 2026, Set 2, Question 36

**First appearance:** GATE CSE 2026, Set 2  
**Question type:** MSQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

An index in a DBMS is said to be dense if an index entry appears for every search-key value in the indexed
  file. Otherwise it is called a sparse index. Consider the following two statements.

       : A hash index must be a dense index
       :A     tree index can be a sparse index

  Which one of the following options is correct?

  A. Both     and                 are true                                                      B. Both      and   are false
  C.    is true and                is false                                                     D.    is false and   is true

### 12.7. Compiler Design — GATE CSE 2021, Set 2, Question 38

**First appearance:** GATE CSE 2021, Set 2  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

For a statement                  in a program, in the context of liveness analysis, the following sets are defined:
                 : the set of variables used in
             : the set of variables that are live at the entry of
                  : the set of variables that are live at the exit of
  Consider a basic block that consists of two statements,                                              followed by   . Which one of the following statements is
  correct?

  A.
  B.
  C.
  D.

### 12.8. Programming and Data Structures — GATE CSE 2024, Set 2, Question 26

**First appearance:** GATE CSE 2024, Set 2  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

​What is the output of the following                              program?
      #include <stdio.h>
      int main() {
      double a[2]={20.0,25.0},* p,* q;
      p=a ;
      q=p+1 ;
      printf("%d,%d", (int) (q-p),( int)(* q- * p));
      return 0;}

 A.                                              B.                                          C.         D.

### 12.9. General Aptitude — GATE CSE 2016, Set 1, Question 03

**First appearance:** GATE CSE 2016, Set 1  
**Question type:** MCQ  
**Marks:** 1  
**Source difficulty label:** medium

Archimedes said, "Give me a lever long enough and a fulcrum on which to place it, and I will move the
world."
The sentence above is an example of a ____________ statement.

 A. figurative                                   B. collateral                                C. literal              D. figurine

### 12.10. Theory of Computation — GATE CSE 2024, Set 1, Question 51

**First appearance:** GATE CSE 2024, Set 1  
**Question type:** NAT  
**Marks:** 2  
**Source difficulty label:** unlabelled

Consider the following two regular expressions over the alphabet                                        :

The total number of strings of length less than or equal to , which are neither in                              nor in , is ________.


## Week 13 — 10 questions

**Subject omitted this week:** Programming and Data Structures

### 13.1. Theory of Computation — GATE CSE 2020, Question 51

**First appearance:** GATE CSE 2020  
**Question type:** NAT  
**Marks:** 2  
**Source difficulty label:** unlabelled

Consider the following language.

                                 number of ’s in                       divisible by            but not divisible by

The minimum number of states in DFA that accepts                                                      is _________

### 13.2. Computer Networks — GATE CSE 2020, Question 55

**First appearance:** GATE CSE 2020  
**Question type:** NAT  
**Marks:** 2  
**Source difficulty label:** unlabelled

Consider a        connection between a client and a server with the following specifications; the round trip
time is ms, the size of the receiver advertised window is      KB, slow-start threshold at the client is KB,
and the maximum segment size is KB. The connection is established at time                . Assume that there are no
timeouts and errors during transmission. Then the size of the congestion window (in       ) at time      ms after all
acknowledgements are processed is _______

### 13.3. Operating Systems — GATE CSE 2021, Set 1, Question 25

**First appearance:** GATE CSE 2021, Set 1  
**Question type:** NAT  
**Marks:** 1  
**Source difficulty label:** unlabelled

Three processes arrive at time zero with       bursts of        and     milliseconds. If the scheduler has
prior knowledge about the length of the     bursts, the minimum achievable average waiting time for these
three processes in a non-preemptive scheduler (rounded to nearest integer) is _____________ milliseconds.

### 13.4. Computer Organization and Architecture — GATE CSE 2021, Set 1, Question 22

**First appearance:** GATE CSE 2021, Set 1  
**Question type:** NAT  
**Marks:** 1  
**Source difficulty label:** unlabelled

Consider a computer system with a byte-addressable primary memory of size     bytes. Assume the
computer system has a direct-mapped cache of size     (     =   bytes), and each cache block is of
size   bytes.

The size of the tag field is __________ bits.

### 13.5. Databases — GATE CSE 2021, Set 2, Question 40

**First appearance:** GATE CSE 2021, Set 2  
**Question type:** MSQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

Suppose the following functional dependencies hold on a relation                                        with attributes             , and   :

Which of the following functional dependencies can be inferred from the above functional dependencies?

 A.                                           B.                                           C.                        D.

### 13.6. General Aptitude — GATE CSE 2024, Set 2, Question 6

**First appearance:** GATE CSE 2024, Set 2  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

​Sequence the following sentences in a coherent passage.

    : This fortuitous geological event generated a colossal amount of energy and heat that resulted in the rocks rising
  to an average height of         across the contact zone.

    : Thus, the geophysicists tend to think of the Himalayas as an active geological event rather than as a static
  geological feature.
     : The natural process of the cooling of this massive edifice absorbed large quantities of atmospheric carbon
  dioxide, altering the earth's atmosphere and making it better suited for life.

   : Many millennia ago, a breakaway chunk of bedrock from the Antarctic Plate collided with the massive Eurasian

  Plate.

  A.                                           B.                                     C.                         D.

### 13.7. Algorithms — GATE CSE 2021, Set 1, Question 30

**First appearance:** GATE CSE 2021, Set 1  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

Consider the following recurrence relation.

Which one of the following options is correct?

 A.                                                                                           B.
 C.                                                                                           D.

### 13.8. Digital Logic — GATE CSE 2026, Set 2, Question 24

**First appearance:** GATE CSE 2026, Set 2  
**Question type:** NAT  
**Marks:** 1  
**Source difficulty label:** unlabelled

The -bit                                  single precision representation of a number is                                                  . The number in decimal
  representation is                         . (rounded off to two decimal places)

### 13.9. Engineering Mathematics — GATE CSE 2016, Set 1, Question 28

**First appearance:** GATE CSE 2016, Set 1  
**Question type:** NAT  
**Marks:** 2  
**Source difficulty label:** medium

A function                                 , defined on the set of positive integers                 , satisfies the following properties:

                                                  if        is even

                                                       if    is odd

Let                                                  be the set of distinct values that                    takes. The maximum possible size of       is
___________.

### 13.10. Compiler Design — GATE CSE 2024, Set 2, Question 55

**First appearance:** GATE CSE 2024, Set 2  
**Question type:** NAT  
**Marks:** 2  
**Source difficulty label:** unlabelled

Consider the following augmented grammar, which is to be parsed with a                                                   parser. The set of terminals
is

  Let                                                               . The number of items in the set                                     is __________.


## Week 14 — 10 questions

**Subject omitted this week:** Algorithms

### 14.1. Compiler Design — GATE CSE 2026, Set 2, Question 31

**First appearance:** GATE CSE 2026, Set 2  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

Consider the canonical        parsing of the grammar below using terminals                                                                  and non-terminals
                with as the start symbol.

  Which one of the following options gives the number of shift-reduce conflicts that will occur in the                                                         ACTION
  table?

  A.                                             B.                                              C.                                 D.

### 14.2. Engineering Mathematics — GATE CSE 2018, Question 28

**First appearance:** GATE CSE 2018  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** medium

Consider the first-order logic sentence

where                        is a quantifier-free first-order logic formula using only predicate symbols, and possibly
equality, but no function symbols. Suppose has a model with a universe containing elements.
Which one of the following statements is necessarily true?

 A. There exists at least one model of with universe of size less than or equal to
 B. There exists no model of with universe of size less than or equal to
 C. There exists no model of with universe size of greater than
 D. Every model of has a universe of size equal to

### 14.3. General Aptitude — GATE CSE 2018, Question 10

**First appearance:** GATE CSE 2018  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** medium

A six sided unbiased die with four green faces and two red faces is rolled seven times. Which of the
following combinations is the most likely outcome of the experiment?

 A. Three green faces and four red                                                            B. Four green faces and three red
    faces.                                                                                       faces.
 C. Five green faces and two red                                                              D. Six green faces and one red face
    faces.

### 14.4. Digital Logic — GATE CSE 2026, Set 1, Question 38

**First appearance:** GATE CSE 2026, Set 1  
**Question type:** MSQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

Consider a Boolean function                              with the following minterm expression:

Which of the following options is/are the minimal sum-of-products expression(s) of                                              ?

 A.
 B.
 C.
 D.

### 14.5. Computer Organization and Architecture — GATE CSE 2024, Set 1, Question 43

**First appearance:** GATE CSE 2024, Set 1  
**Question type:** MSQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

Consider two set-associative cache memory architectures:       , which uses the write back policy, and
      , which uses the write through policy. Both of them use the         (Least Recently Used) block
replacement policy. The cache memory is connected to the main memory. Which of the following statements is/are
TRUE?

A. A read miss in                         never evicts a dirty block
B. A read miss in                         never triggers a write back operation of a cache block to main memory
C. A write hit in                       can modify the value of the dirty bit of a cache block
D. A write miss in                         always writes the victim cache block to main memory before loading the missed block to
   the cache

### 14.6. Theory of Computation — GATE CSE 2017, Set 1, Question 38

**First appearance:** GATE CSE 2017, Set 1  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** medium

Consider the following languages over the alphabet                                                  . Let                                   and
                            .

Which of the following are context-free languages?

  I.
 II.

 A. I only                                   B. II only                           C. I and II                     D. Neither I nor II

### 14.7. Operating Systems — GATE CSE 2017, Set 2, Question 08

**First appearance:** GATE CSE 2017, Set 2  
**Question type:** MCQ  
**Marks:** 1  
**Source difficulty label:** medium

In a file allocation system, which of the following allocation scheme(s) can be used if no external
fragmentation is allowed?

 1. Contiguous
 2. Linked
 3. Indexed

 A.      and         only                         B.        only                             C.      only                     D.   and    only

### 14.8. Programming and Data Structures — GATE CSE 2017, Set 2, Question 36

**First appearance:** GATE CSE 2017, Set 2  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

The pre-order traversal of a binary search tree is given by                                                                               . Then the
post-order traversal of this tree is

 A.
 B.

 C.
 D.

### 14.9. Computer Networks — GATE CSE 2026, Set 1, Question 34

**First appearance:** GATE CSE 2026, Set 1  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

A TCP sender successfully establishes a connection with a TCP receiver and starts the transmission of
segments. The TCP congestion control mechanism's slow-start threshold is set to            segments.
Assume that the round-trip time is fixed at millisecond. Assume that the sender always has data to send, the
segments are numbered from , and no segment is lost. Let denote the time (in milliseconds) at which the
transmission of segment number        starts.

Which one of the following options is correct?

  A.                                                                                                B.
  C.                                                                                                D.

### 14.10. Databases — GATE CSE 2019, Question 32

**First appearance:** GATE CSE 2019  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

Let the set of functional dependencies                                                            hold on a relation schema
               .    is not in BCNF. Suppose                                is decomposed into two schemas     and , where
           and               .

Consider the two statements given below.

  I. Both and are in BCNF
 II. Decomposition of into and                                    is dependency preserving and lossless

Which of the above statements is/are correct?

 A. Both I and II                             B. I only                                    C. II only                D. Neither I nor II


## Week 15 — 10 questions

**Subject omitted this week:** Computer Organization and Architecture

### 15.1. Databases — GATE CSE 2020, Question 14

**First appearance:** GATE CSE 2020  
**Question type:** MCQ  
**Marks:** 1  
**Source difficulty label:** unlabelled

Which one of the following is used to represent the supporting many-one relationships of a weak entity set in
an entity-relationship diagram?

 A. Diamonds with double/bold border                                        B. Rectangles     with     double/bold
                                                                               border
 C. Ovals with double/bold border                                           D. Ovals that    contain    underlined
                                                                               identifiers

### 15.2. Programming and Data Structures — GATE CSE 2025, Set 1, Question 52

**First appearance:** GATE CSE 2025, Set 1  
**Question type:** NAT  
**Marks:** 2  
**Source difficulty label:** unlabelled

Let              be a datatype for an implementation of linked list defined as follows:
      typedef struct list {
      int data;
      struct list *next;
      } LIST;

Suppose a program has created two linked lists,    and  , whose contents are given in the figure below (code for
creating   and    is not provided here).     contains nodes, and    contains nodes.

Consider the following C program segment that modifies the list     . The number of nodes that will be there in
after the execution of the code segment is __________. (Answer in integer)

      int find (int query, LIST *list) {
      while (list != NULL) {
      if(list->data == query) return 1 ;
      list = list->next;
      }
      return 0 ;
      }
      int main (){
      ... ... ...
      ptr1=L1; ptr2=L2;
      while (ptr1->next != NULL){
      query = ptr1->next->data;
      if (find (query, L2))
      ptr1->next = ptr1->next->next;
      else ptr1 = ptr1->next;
      }
      ... .... ...
      return 0;
      }

### 15.3. Theory of Computation — GATE CSE 2025, Set 1, Question 35

**First appearance:** GATE CSE 2025, Set 1  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

Consider the following two languages over the alphabet                                                , where   and   are natural numbers.

Which ONE of the following statements is CORRECT?

 A. Both     and     are context-free languages.
 B.    is a context-free language but     is not a context-free language.
 C.    is not a context-free language but     is a context-free language.
 D. Neither     nor    are context-free languages.

### 15.4. Algorithms — GATE CSE 2017, Set 2, Question 50

**First appearance:** GATE CSE 2017, Set 2  
**Question type:** NAT  
**Marks:** 2  
**Source difficulty label:** unlabelled

A message is made up entirely of characters from the set                                                                             . The table of probabilities
  for each of the characters is shown below:

  If a message of                             characters over             is encoded using Huffman coding, then the expected length of the encoded

  message in bits is ______.

### 15.5. General Aptitude — GATE CSE 2016, Set 1, Question 05

**First appearance:** GATE CSE 2016, Set 1  
**Question type:** MCQ  
**Marks:** 1  
**Source difficulty label:** medium

A cube is built using   cubic blocks of side one unit. After it is built, one cubic block is removed from every
  corner of the cube. The resulting surface area of the body (in square units) after the removal is ________.

   a.                                               b.                                                c.             d.

### 15.6. Compiler Design — GATE CSE 2026, Set 1, Question 32

**First appearance:** GATE CSE 2026, Set 1  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

Consider the control flow graph shown in the figure.

  Which one of the following options correctly lists the set of redundant expressions (common subexpressions) in the
  basic blocks B and B ?

  Note: All the variables are integers.

  A. B4:                                   B5:
  B. B4:                                  B5:
  C. B4:                                   B5:
  D. B4:                                  B5:

### 15.7. Operating Systems — GATE CSE 2024, Set 1, Question 52

**First appearance:** GATE CSE 2024, Set 1  
**Question type:** NAT  
**Marks:** 2  
**Source difficulty label:** unlabelled

Consider a memory management system that uses a page size of           . Assume that both the physical and
virtual addresses start from . Assume that the pages         , and are stored in the page frames           ,
and , respectively. The physical address (in decimal format) corresponding to the virtual address      (in decimal
format) is ___________.

### 15.8. Engineering Mathematics — GATE CSE 2025, Set 2, Question 32

**First appearance:** GATE CSE 2025, Set 2  
**Question type:** MSQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

​Let       be the set of all functions from                                                 to            . Define the binary relation          on     as follows:
                   if and only if                                                                              , where            .
Which of the following statement(s) is/are TRUE?

 A.       is a symmetric relation
 B.             is a partial order
 C.             is a lattice
 D.       is an equivalence relation

### 15.9. Computer Networks — GATE CSE 2024, Set 2, Question 44

**First appearance:** GATE CSE 2024, Set 2  
**Question type:** NAT  
**Marks:** 2  
**Source difficulty label:** unlabelled

Consider a          connection operating at a point of time with the congestion window of size
(Maximum Segment Size), when a timeout occurs due to packet loss. Assuming that all the segments
transmitted in the next two         (Round Trip Time) are acknowledged correctly, the congestion window size ( in
      ) during the third      will be __________.

### 15.10. Digital Logic — GATE CSE 2016, Set 1, Question 30

**First appearance:** GATE CSE 2016, Set 1  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** medium

Consider the two cascade                           to        multiplexers as shown in the figure .

The minimal sum of products form of the output                                           is

 A.                                                                                             B.
 C.                                                                                             D.


## Week 16 — 10 questions

**Subject omitted this week:** Computer Networks

### 16.1. Compiler Design — GATE CSE 2020, Question 24

**First appearance:** GATE CSE 2020  
**Question type:** NAT  
**Marks:** 1  
**Source difficulty label:** unlabelled

Consider the following grammar.

The number of reduction steps taken by a bottom-up parser while accepting the string                                            is ___________.

### 16.2. General Aptitude — GATE CSE 2017, Set 1, Question 9

**First appearance:** GATE CSE 2017, Set 1  
**Question type:** NAT  
**Marks:** 2  
**Source difficulty label:** unlabelled

Arun, Gulab, Neel and Shweta must choose one shirt each from a pile of four shirts coloured red, pink, blue
  and white respectively. Arun dislikes the colour red and Shweta dislikes the colour white. Gulab and Neel
  like all the colours. In how many different ways can they choose the shirts so that no one has a shirt with a colour
  he or she dislikes?

  A.                                               B.                                C.                        D.

### 16.3. Programming and Data Structures — GATE CSE 2016, Set 2, Question 36

**First appearance:** GATE CSE 2016, Set 2  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** medium

Consider the following New-order strategy for traversing a binary tree:

      Visit the root;
      Visit the right subtree using New-order;
      Visit the left subtree using New-order;

The New-order traversal of the expression tree corresponding to the reverse polish expression
 34*5-2^67*1+-

is given by:

 A.
 B.
 C.
 D.

### 16.4. Databases — GATE CSE 2026, Set 1, Question 21

**First appearance:** GATE CSE 2026, Set 1  
**Question type:** MSQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

In the context of relational database normalization, which of the following statements is/are true?

 A. It is always possible to obtain a dependency-preserving NF decomposition of a relation
 B. It is always possible to obtain a dependency-preserving NF decomposition of a relation
 C. It is not always possible to obtain a dependency-preserving BCNF decomposition of a relation
 D. It is not always possible to obtain a dependency-preserving NF decomposition of a relation

### 16.5. Algorithms — GATE CSE 2026, Set 1, Question 40

**First appearance:** GATE CSE 2026, Set 1  
**Question type:** MSQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

Consider the following pseudocode for depth-first search (DFS) algorithm which takes a directed graph
           as input, where    and      are the discovery time and finishing time, respectively, of the vertex
        .

  Suppose that the input directed graph             is a directed acyclic graph (DAG).
  For an edge             , which of the following options will NEVER be correct?

  A.                                                                                               B.
  C.                                                                                               D.

### 16.6. Computer Organization and Architecture — GATE CSE 2026, Set 1, Question 50

**First appearance:** GATE CSE 2026, Set 1  
**Question type:** NAT  
**Marks:** 2  
**Source difficulty label:** unlabelled

The EX stage of a pipelined processor performs the memory read operations for LOAD instructions, and the
  operations for the arithmetic and logic instructions. Let        denote the time taken by the EX stage to
  perform the operation for an instruction. For each instruction type, the values of           and      (the number of
  instructions of that type in a sequence of   instructions for a program P ), are given in the table below.

  The duration of the pipeline clock cycle is                              nanosecond. Assume that the latch time for the interstage buffers in the
  pipeline is negligible.

  When program is executed, the number of clock cycles for which the pipeline is stalled due to structural hazards
  in the EX stage is . (answer in integer)

### 16.7. Theory of Computation — GATE CSE 2017, Set 2, Question 25

**First appearance:** GATE CSE 2017, Set 2  
**Question type:** NAT  
**Marks:** 1  
**Source difficulty label:** unlabelled

The minimum possible number of states of a deterministic finite automaton that accepts the regular
language = {       |                ,                   } is ______________ .

### 16.8. Digital Logic — GATE CSE 2024, Set 1, Question 18

**First appearance:** GATE CSE 2024, Set 1  
**Question type:** MSQ  
**Marks:** 1  
**Source difficulty label:** unlabelled

Consider the circuit shown below where the gates may have propagation delays. Assume that all signal
  transitions occur instantaneously and that wires have no delays. Which of the following statements about the
  circuit is/are CORRECT?

  A. With no propagation delays, the output is always logic Zero
  B. With no propagation delays, the output is always logic One
  C. With propagation delays, the output can have a transient logic One after                                    transitions from logic Zero to logic
     One
  D. With propagation delays, the output can have a transient logic Zero after                                   transitions from logic One to logic
     Zero

### 16.9. Operating Systems — GATE CSE 2016, Set 1, Question 20

**First appearance:** GATE CSE 2016, Set 1  
**Question type:** MCQ  
**Marks:** 1  
**Source difficulty label:** medium

Consider an arbitrary set of CPU-bound processes with unequal CPU burst lengths submitted at the same
time to a computer system. Which one of the following process scheduling algorithms would minimize the
average waiting time in the ready queue?

 A. Shortest remaining time first
 B. Round-robin with the time quantum less than the shortest CPU burst
 C. Uniform random

 D. Highest priority first with priority proportional to CPU burst length

### 16.10. Engineering Mathematics — GATE CSE 2024, Set 2, Question 37

**First appearance:** GATE CSE 2024, Set 2  
**Question type:** MSQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

Let   be an         matrix over the set of all real numbers . Let                                                                   be a matrix obtained from             by
swapping two rows. Which of the following statements is/are TRUE?

 A. The determinant of is the negative of the determinant of
 B. If is invertible, then is also invertible
 C. If is symmetric, then is also symmetric
 D. If the trace of is zero, then the trace of is also zero


## Week 17 — 10 questions

**Subject omitted this week:** General Aptitude

### 17.1. Computer Organization and Architecture — GATE CSE 2022, Question 44

**First appearance:** GATE CSE 2022  
**Question type:** MSQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

Consider a system with          direct mapped data cache with a block size of          The system has a
  physical address space of            and a word length of         During the execution of a program, four
  data words             and are accessed in that order       times                           Hence, there are
  accesses to data cache altogether. Assume that the data cache is initially empty and no other data words are
  accessed by the program. The addresses of the first bytes of            and are
  and             respectively. For the execution of the above program, which of the following statements is/are
          with respect to the data cache?

  A. Every access to is a hit.
  B. Once     is brought to the cache it is never evicted.
  C. At the end of the execution only and reside in the cache.
  D. Every access to evicts from the cache.

### 17.2. Algorithms — GATE CSE 2021, Set 2, Question 55

**First appearance:** GATE CSE 2021, Set 2  
**Question type:** NAT  
**Marks:** 2  
**Source difficulty label:** unlabelled

In a directed acyclic graph with a source vertex , the                 of a directed path is defined to be the
  product of the weights of the edges on the path. Further, for a vertex other than , the quality-score of is
  defined to be the maximum among the quality-scores of all the paths from to . The quality-score of is assumed
  to be .

  The sum of the quality-scores of all vertices on the graph shown above is _______

### 17.3. Computer Networks — GATE CSE 2017, Set 1, Question 14

**First appearance:** GATE CSE 2017, Set 1  
**Question type:** MCQ  
**Marks:** 1  
**Source difficulty label:** unlabelled

Consider a TCP client and a TCP server running on two different machines. After completing data transfer,
the TCP client calls close to terminate the connection and a FIN segment is sent to the TCP server. Server-
side TCP responds by sending an ACK, which is received by the client-side TCP. As per the TCP connection state
diagram               , in which state does the client-side TCP connection wait for the FIN from the server-side
TCP?

 A. LAST-ACK                                  B. TIME-WAIT                    C. FIN-WAIT-                    D. FIN-WAIT-

### 17.4. Programming and Data Structures — GATE CSE 2025, Set 2, Question 23

**First appearance:** GATE CSE 2025, Set 2  
**Question type:** NAT  
**Marks:** 1  
**Source difficulty label:** unlabelled

int x=126,y=105;
      do {
         if(x>y) x=x-y;
         else y=y-x;
      } while(x!=y);
      printf("%d",x);

The output of the given C code segment is _________. (Answer in integer)

### 17.5. Operating Systems — GATE CSE 2019, Question 39

**First appearance:** GATE CSE 2019  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

Consider the following snapshot of a system running concurrent processes. Process is holding
instances of a resource ,             . Assume that all instances of are currently in use. Further, for all ,
process can place a request for at most additional instances of while holding the        instances it already has.
Of the processes, there are exactly two processes and such that                      . Which one of the following
conditions guarantees that no other process apart from and can complete execution?

 A.
 B.
 C.
 D.

### 17.6. Digital Logic — GATE CSE 2023, Question 35

**First appearance:** GATE CSE 2023  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

Consider the                              single precision floating point numbers                                                         and
Which one of the following corresponds to the product of these numbers                                                                          represented in the
         single precision format?

 A.                                            B.                                          C.                                        D.

### 17.7. Databases — GATE CSE 2025, Set 1, Question 11

**First appearance:** GATE CSE 2025, Set 1  
**Question type:** MSQ  
**Marks:** 1  
**Source difficulty label:** unlabelled

Consider the following     tree with nodes, in which a node can store at most key values The value                            is
now inserted in the    tree. Which of the following options(s) is/are CORRECT?

 A. None of the nodes will split.
 B. At least one node will split and redistribute.

 C. The total number of nodes will remain same.
 D. The height of the tree will increase.

### 17.8. Compiler Design — GATE CSE 2024, Set 1, Question 16

**First appearance:** GATE CSE 2024, Set 1  
**Question type:** MSQ  
**Marks:** 1  
**Source difficulty label:** easy

Which of the following is/are Bottom-Up Parser(s)?

  A. Shift-reduce Parser                                                                        B. Predictive Parser
  C. LL      Parser                                                                             D. LR Parser

### 17.9. Theory of Computation — GATE CSE 2023, Question 14

**First appearance:** GATE CSE 2023  
**Question type:** MSQ  
**Marks:** 1  
**Source difficulty label:** unlabelled

Which of the following statements is/are

 A. The intersection of two regular languages is regular.
 B. The intersection of two context-free languages is context-free.
 C. The intersection of two recursive languages is recursive.
 D. The intersection of two recursively enumerable languages is recursively enumerable.

### 17.10. Engineering Mathematics — GATE CSE 2017, Set 2, Question 31

**First appearance:** GATE CSE 2017, Set 2  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** hard

For            any              discrete              random               variable               ,      with       probability       mass       function
                                                                         ,    and                           ,     define   the      polynomial    function
                                    . For a certain discrete random variable                                    , there exists a scalar              such that
                                           . The expectation of                    is

 A.                                                                                               B.
 C.                                                                                               D. Not expressible in terms of    and
                                                                                                      alone


## Week 18 — 10 questions

**Subject omitted this week:** Engineering Mathematics

### 18.1. Programming and Data Structures — GATE CSE 2019, Question 52

**First appearance:** GATE CSE 2019  
**Question type:** NAT  
**Marks:** 2  
**Source difficulty label:** unlabelled

Consider the following C program:
       #include <stdio.h>
       int main() {
          float sum = 0.0, j=1.0, i=2.0;
          while (i/j > 0.0625) {
             j=j+j;
             sum=sum+i/j;
             printf("%f\n", sum);
          }
          return 0;
       }

The number of times the variable sum will be printed, when the above program is executed, is _________

### 18.2. Operating Systems — GATE CSE 2021, Set 1, Question 15

**First appearance:** GATE CSE 2021, Set 1  
**Question type:** MSQ  
**Marks:** 1  
**Source difficulty label:** unlabelled

Consider a linear list based directory implementation in a file system. Each directory is a list of nodes, where
each node contains the file name along with the file metadata, such as the list of pointers to the data blocks.
Consider a given directory      .
Which of the following operations will necessarily require a full scan of                                             for successful completion?

 A. Creation of a new file in                                                                     B. Deletion of an existing file from
 C. Renaming of an existing file in                                                               D. Opening of an existing file in

### 18.3. Computer Organization and Architecture — GATE CSE 2026, Set 2, Question 46

**First appearance:** GATE CSE 2026, Set 2  
**Question type:** NAT  
**Marks:** 2  
**Source difficulty label:** unlabelled

Consider a system with MB physical memory and a word length of byte. The system uses a direct
  mapped cache, with block numbers starting from . The word with physical address             is mapped to
  the cache block number      . The maximum possible size of the cache (in KB ) for this configuration is                                                    .
  (answer in integer)

  Note:                       and

### 18.4. Compiler Design — GATE CSE 2017, Set 2, Question 05

**First appearance:** GATE CSE 2017, Set 2  
**Question type:** MCQ  
**Marks:** 1  
**Source difficulty label:** easy

Match the following according to input (from the left column) to the compiler phase (in the right column) that
processes it:

 A.                                                                                             B.
 C.                                                                                             D.

### 18.5. General Aptitude — GATE CSE 2025, Set 1, Question 6

**First appearance:** GATE CSE 2025, Set 1  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

"I put the brown paper in my pocket along with the chalks, and possibly other things. I suppose every one
must have reflected how primeval and how poetical are the things that one carries in one's pocket: the
pocket-knife, for instance the type of all human tools, the infant of the sword. Once I planned to write a book of
poems entirely about the things in my pocket. But I found it would be too long: and the age of the great epics is
past."
(From G.K. Chesterton's "A Piece of Chalk")
Based only on the information provided in the above passage, which one of the following statements is true?

 A. The author of the passage carries a mirror in his pocket to reflect upon things.
 B. The author of the passage had decided to write a poem on epics.
 C. The pocket-knife is described as the infant of the sword.
 D. Epics are described as too inconvenient to write.

### 18.6. Theory of Computation — GATE CSE 2017, Set 1, Question 39

**First appearance:** GATE CSE 2017, Set 1  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** hard

Let        and be finite alphabets and let be a symbol outside both                                              and . Let be a total function from
      to     . We say is computable if there exists a Turing machine                                            which given an input       , always
halts with                on its tape. Let                  denote the language                                              . Which of the following statements is
true:

 A. is computable if and only if   is recursive.
 B. is computable if and only if   is recursively enumerable.
 C. If is computable then    is recursive, but not conversely.
 D. If is computable then    is recursively enumerable, but not conversely.

### 18.7. Digital Logic — GATE CSE 2017, Set 2, Question 42

**First appearance:** GATE CSE 2017, Set 2  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

The next state table of a                        bit saturating up-counter is given below.

The counter is built as a synchronous sequential circuit using                                          flip-flops. The expressions for    and    are

 A.
 B.
 C.
 D.

### 18.8. Databases — GATE CSE 2016, Set 2, Question 22

**First appearance:** GATE CSE 2016, Set 2  
**Question type:** MCQ  
**Marks:** 1  
**Source difficulty label:** medium

Suppose a database schedule involves transactions                     . Construct the precedence graph of
 with vertices representing the transactions and edges representing the conflicts. If is serializable, which
one of the following orderings of the vertices of the precedence graph is guaranteed to yield a serial schedule?

 A. Topological order                                                     B. Depth-first order
 C. Breadth-first order                                                   D. Ascending order of the transaction
                                                                             indices

### 18.9. Algorithms — GATE CSE 2022, Question 39

**First appearance:** GATE CSE 2022  
**Question type:** MSQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

Consider a simple undirected weighted graph       all of whose edge weights are distinct. Which of the
following statements about the minimum spanning trees of is/are

A. The edge with the second smallest weight is always part of any minimum spanning tree of
B. One or both of the edges with the third smallest and the fourth smallest weights are part of any minimum
   spanning tree of
C. Suppose            be such that      and        . Consider the edge with the minimum weight such that one of
   its vertices is in and the other in      Such an edge will always be part of any minimum spanning tree of
D.     can have multiple minimum spanning trees.

### 18.10. Computer Networks — GATE CSE 2016, Set 2, Question 53

**First appearance:** GATE CSE 2016, Set 2  
**Question type:** NAT  
**Marks:** 2  
**Source difficulty label:** medium

A network has a data transmission bandwidth of            bits per second. It uses CSMA/CD in the MAC
  layer. The maximum signal propagation time from one node to another node is              microseconds.
  The minimum size of a frame in the network is __________ bytes.


## Week 19 — 10 questions

**Subject omitted this week:** Theory of Computation

### 19.1. Algorithms — GATE CSE 2024, Set 1, Question 32

**First appearance:** GATE CSE 2024, Set 1  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

Consider the following recurrence relation:

Which one of the following options is CORRECT?

 A.                                                                                           B.
 C.                                                                                           D.

### 19.2. Compiler Design — GATE CSE 2024, Set 2, Question 33

**First appearance:** GATE CSE 2024, Set 2  
**Question type:** MSQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

Consider the following expression:                                   . The following sequence shows the list of
  triples representing the given expression, with entries missing for triples        , and   .

  Which one of the following options fills in the missing entries CORRECTLY?

  A.
  B.
  C.
  D.

### 19.3. Operating Systems — GATE CSE 2016, Set 2, Question 49

**First appearance:** GATE CSE 2016, Set 2  
**Question type:** NAT  
**Marks:** 2  
**Source difficulty label:** medium

Consider a non-negative counting semaphore . The operation               decrements , and          increments
  . During an execution,            operations and            operations are issued in some order. The largest
initial value of for which at least one       operation will remain blocked is _______

### 19.4. Digital Logic — GATE CSE 2021, Set 1, Question 28

**First appearance:** GATE CSE 2021, Set 1  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

Consider a -bit counter, designed using                                       flip-flops, as shown below:

Assuming the initial state of the counter given by                                                as   , what are the next three states?

 A.                                                B.                                              C.                       D.

### 19.5. Databases — GATE CSE 2018, Question 42

**First appearance:** GATE CSE 2018  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** medium

Consider the following four relational schemas. For each schema , all non-trivial functional dependencies are
listed, The bolded attributes are the respective primary keys.
                     Registration(rollno, courses)
Field ‘courses’ is a set-valued attribute containing the set of courses a student has registered for.
Non-trivial functional dependency
rollno          courses
                     Registration (rollno, coursid, email)
Non-trivial functional dependencies:
rollno, courseid                 email
email           rollno
                      Registration (rollno, courseid, marks, grade)
Non-trivial functional dependencies:
rollno, courseid,                marks, grade
marks            grade
                       Registration (rollno, courseid, credit)
Non-trivial functional dependencies:
rollno, courseid                 credit
courseid             credit
Which one of the relational schemas above is in                             but not in     ?

 A.                                          B.                             C.                     D.

### 19.6. Computer Networks — GATE CSE 2021, Set 2, Question 45

**First appearance:** GATE CSE 2021, Set 2  
**Question type:** MSQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

Consider a computer network using the distance vector routing algorithm in its network layer. The partial
topology of the network is shown below.

The objective is to find the shortest-cost path from the router          to routers    and . Assume that    does not
initially know the shortest routes to and . Assume that has three neighbouring routers denoted as ,            and
   . During one iteration, measures its distance to its neighbours , , and as , and , respectively. Router
    gets routing vectors from its neighbours that indicate that the distance to router from routers ,    and are
  , and , respectively. The routing vector also indicates that the distance to router       from routers ,   and
are , and respectively. Which of the following statement(s) is/are correct with respect to the new routing table
o , after updation during this iteration?

 A. The distance from to will be stored as
 B. The distance from to will be stored as
 C. The next hop router for a packet from to                                             is
 D. The next hop router for a packet from to                                             is

### 19.7. Programming and Data Structures — GATE CSE 2020, Question 22

**First appearance:** GATE CSE 2020  
**Question type:** NAT  
**Marks:** 1  
**Source difficulty label:** unlabelled

Consider the following C program.
      #include <stdio.h>
      int main () {
         int a[4] [5] = {{1, 2, 3, 4, 5},
                    {6, 7,8, 9, 10},
                    {11, 12, 13, 14, 15},
                    {16, 17,18, 19, 20}};
         printf(“%d\n”, *(*(a+**a+2)+3));
         return(0);
      }

The output of the program is _______.

### 19.8. General Aptitude — GATE CSE 2016, Set 1, Question 09

**First appearance:** GATE CSE 2016, Set 1  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** medium

If                                             , which of the following is a factor of                         ?

  A.                                                 B.                                             C.               D.

### 19.9. Engineering Mathematics — GATE CSE 2021, Set 2, Question 33

**First appearance:** GATE CSE 2021, Set 2  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** medium

A bag has red balls and black balls. All balls are identical except for their colours. In a trial, a ball is
randomly drawn from the bag, its colour is noted and the ball is placed back into the bag along with another
ball of the same colour. Note that the number of balls in the bag will increase by one, after the trial. A sequence of
four such trials is conducted. Which one of the following choices gives the probability of drawing a red ball in the
fourth trial?

 A.

 B.

 C.

 D.

### 19.10. Computer Organization and Architecture — GATE CSE 2026, Set 2, Question 43

**First appearance:** GATE CSE 2026, Set 2  
**Question type:** MSQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

To keep track of free blocks in a file system, one of the two approaches is generally used - using bitmaps
  (bit vectors) or using linked lists. Consider that the linked list approach is used to keep track of free blocks in
  a file system. Assume that the disk size is         GB , block size is KB , and block numbers used are -bit long. A
  single pointer of size bytes is used in each block of the list to point to the next block of the list. The number of
  blocks required to hold the free disk block numbers is               . (answer in integer)

  Note:                       and


## Week 20 — 10 questions

**Subject omitted this week:** General Aptitude

### 20.1. Computer Networks — GATE CSE 2024, Set 2, Question 13

**First appearance:** GATE CSE 2024, Set 2  
**Question type:** MSQ  
**Marks:** 1  
**Source difficulty label:** unlabelled

Node    has a       connection open to node . The packets from       to                              go through an intermediate
router . Ethernet switch is the first switch on the network path between                              and . Consider a packet sent
from to over this connection.

Which of the following statements is/are TRUE about the destination                            and           addresses on this packet at the
time it leaves ?

 A. The destination                   address is the    address of
 B. The destination                   address is the    address of
 C. The destination                      address is the        address of
 D. The destination                      address is the        address of

### 20.2. Engineering Mathematics — GATE CSE 2016, Set 2, Question 29

**First appearance:** GATE CSE 2016, Set 2  
**Question type:** NAT  
**Marks:** 2  
**Source difficulty label:** medium

The value of the expression                                                         in the range       to   , is ________.

### 20.3. Algorithms — GATE CSE 2025, Set 2, Question 27

**First appearance:** GATE CSE 2025, Set 2  
**Question type:** MSQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

Let   be an edge-weighted undirected graph with positive edge weights. Suppose a positive constant                                            is
added to the weight of every edge.

Which ONE of the following statements is TRUE about the minimum spanning trees (MSTs) and shortest paths
(SPs) in before and after the edge weight update?

 A. Every MST remains an MST, and every SP remains an SP.
 B. MSTs need not remain MSTs, and every SP remains an SP.
 C. Every MST remains an MST, and SPs need not remain SPs.
 D. MSTs need not remain MSTs, and SPs need not remain SPs.

### 20.4. Databases — GATE CSE 2017, Set 2, Question 49

**First appearance:** GATE CSE 2017, Set 2  
**Question type:** NAT  
**Marks:** 2  
**Source difficulty label:** medium

In a     Tree , if the search-key value is bytes long , the block size is                     bytes and the pointer size is
, then the maximum order of the       Tree is ____

### 20.5. Operating Systems — GATE CSE 2024, Set 1, Question 30

**First appearance:** GATE CSE 2024, Set 1  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

Consider the following two threads       and       that update two shared variables and . Assume that
initially         . Though context switching between threads can happen at any time, each statement of
     or   is executed atomically without interruption.

  Which one of the following options lists all the possible combinations of values of a and b after both T1 and T2
  finish execution?

  A.
  B.
  C.
  D.

### 20.6. Theory of Computation — GATE CSE 2021, Set 2, Question 17

**First appearance:** GATE CSE 2021, Set 2  
**Question type:** NAT  
**Marks:** 1  
**Source difficulty label:** unlabelled

Consider the following deterministic finite automaton

The number of strings of length                         accepted by the above automaton is ___________

### 20.7. Computer Organization and Architecture — GATE CSE 2025, Set 2, Question 45

**First appearance:** GATE CSE 2025, Set 2  
**Question type:** NAT  
**Marks:** 2  
**Source difficulty label:** unlabelled

Given a computing system with two levels of cache (L1 and L2) and a main memory. The first level (L1)
  cache access time is nanosecond (ns) and the "hit rate" for L1 cache is            while the processor is
  accessing the data from L1 cache. Whereas, for the second level (L2) cache, the "hit rate" is       and the "miss
  penalty" for transferring data from L2 cache to L1 cache is ns . The "miss penalty" for the data to be transferred
  from main memory to L2 cache is         ns .

  Then the average memory access time in this system in nanoseconds is __________ . (rounded off to one decimal
  place)

### 20.8. Compiler Design — GATE CSE 2021, Set 2, Question 13

**First appearance:** GATE CSE 2021, Set 2  
**Question type:** MSQ  
**Marks:** 1  
**Source difficulty label:** easy

In the context of compilers, which of the following is/are                                                an intermediate representation of the source
program?

 A. Three address code                                                                            B. Abstract Syntax Tree
 C. Control Flow Graph                                                                            D. Symbol table

### 20.9. Digital Logic — GATE CSE 2026, Set 1, Question 11

**First appearance:** GATE CSE 2026, Set 1  
**Question type:** MSQ  
**Marks:** 1  
**Source difficulty label:** unlabelled

Consider the following Boolean expression of a function                                                   :

  Which of the following expressions is/are equivalent to                                               ?

  A.                                                                                                   B.
  C.                                                                                                   D.

### 20.10. Programming and Data Structures — GATE CSE 2017, Set 2, Question 55

**First appearance:** GATE CSE 2017, Set 2  
**Question type:** NAT  
**Marks:** 2  
**Source difficulty label:** unlabelled

Consider the following C program.
 #include<stdio.h>
 #include<string.h>
 int main() {
    char* c="GATECSIT2017";
    char* p=c;
    printf("%d", (int)strlen(c+2[p]-6[p]-1));
    return 0;
 }

The output of the program is _______


## Week 21 — 10 questions

**Subject omitted this week:** Computer Organization and Architecture

### 21.1. Databases — GATE CSE 2016, Set 2, Question 51

**First appearance:** GATE CSE 2016, Set 2  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** medium

Consider the following database schedule with two transactions                          and     .

Where        denotes a read operation by transaction                            on a variable   ,         denotes a write operation by
on a variable and    denotes an abort by transaction                        .
Which one of the following statements about the above schedule is TRUE?

 A.       is non-recoverable.                                             B.     is recoverable,    but    has   a
                                                                             cascading abort.
 C.      does not have a cascading                                        D.   is strict.
      abort.

### 21.2. Theory of Computation — GATE CSE 2021, Set 2, Question 47

**First appearance:** GATE CSE 2021, Set 2  
**Question type:** MSQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

Which of the following regular expressions represent(s) the set of all binary numbers that are divisible by

three? Assume that the string                        is divisible by three.

 A.                                                                                                B.
 C.                                                                                                D.

### 21.3. Engineering Mathematics — GATE CSE 2026, Set 1, Question 47

**First appearance:** GATE CSE 2026, Set 1  
**Question type:** MSQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

Let   be an undirected graph, which is a path on                                            vertices. The number of matchings in     is    .
  (answer in integer)

### 21.4. Compiler Design — GATE CSE 2021, Set 1, Question 4

**First appearance:** GATE CSE 2021, Set 1  
**Question type:** MCQ  
**Marks:** 1  
**Source difficulty label:** medium

Consider the following statements.

                 The sequence of procedure calls corresponds to a preorder traversal of the activation tree.
                 The sequence of procedure returns corresponds to a postorder traversal of the activation tree.

  Which one of the following options is correct?

  A.        is true and             is false                                            B.         is false and      is true
  C.        is true and             is true                                             D.         is false and      is false

### 21.5. General Aptitude — GATE CSE 2018, Question 2

**First appearance:** GATE CSE 2018  
**Question type:** MCQ  
**Marks:** 1  
**Source difficulty label:** medium

A ________ investigation can sometimes yield new facts, but typically organized ones are more successful.
The word that best fills the blank in the above sentence is

 A.                                              B.                                           C.                      D.

### 21.6. Operating Systems — GATE CSE 2024, Set 2, Question 27

**First appearance:** GATE CSE 2024, Set 2  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

Consider a single processor system with four processes                    and , represented as given below,
where for each process the first value is its arrival time, and the second value is its   burst time.
                                                                     , and                  .

Which one of the following options gives the average waiting times when preemptive Shortest Remaining Time First
         and Non-Preemptive Shortest Job First                         scheduling algorithms are applied to the
processes?

 A.
 B.
 C.
 D.

### 21.7. Programming and Data Structures — GATE CSE 2016, Set 2, Question 35

**First appearance:** GATE CSE 2016, Set 2  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** medium

The following function computes                                for positive integers      and   .
 int exp (int X, int Y) {
     int res =1, a = X, b = Y;

      while (b != 0) {
         if (b % 2 == 0) {a = a * a; b = b/2; }
         else       {res = res * a; b = b - 1; }
      }
      return res;
 }

Which one of the following conditions is TRUE before every iteration of the loop?

 A.                                                                                B.
 C.                                                                                D.

### 21.8. Algorithms — GATE CSE 2026, Set 2, Question 29

**First appearance:** GATE CSE 2026, Set 2  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

Consider a table , where the elements                           , represent the cost of the optimal solutions of
different subproblems of a problem that is being solved using a dynamic programming algorithm. The
recursive formulation to compute the table entries is as follows:

  Consider the following two algorithms to compute entries of . Assume that for both the algorithms, for all
                       has been initialized to .
  Algorithm   : For

  Algorithm                For

  Algorithm                           is said to be correct if and only if it calculates the correct values of                 , for all
                       , (as per the recursive formulation) at the end of the execution of the algorithm .

  Which one of the following statements is true?

  A. Both algorithms      and      are correct
  B. Algorithm     is correct, but algorithm     is incorrect
  C. Algorithm     is correct, but algorithm     is incorrect
  D. Both algorithms      and      are incorrect

### 21.9. Computer Networks — GATE CSE 2025, Set 2, Question 8

**First appearance:** GATE CSE 2025, Set 2  
**Question type:** MCQ  
**Marks:** 1  
**Source difficulty label:** unlabelled

A machine receives an                            datagram. The protocol field of the                     header has the protocol number of a
  protocol .
  Which ONE of the following is NOT a possible candidate for                                     ?

  A. Internet Control Message Protocol                                                     B. Internet    Group    Management
     (ICMP)                                                                                   (IGMP)
  C. Open Shortest Path First (OSPF)                                                       D. Routing Information Protocol (RIP)

### 21.10. Digital Logic — GATE CSE 2024, Set 2, Question 40

**First appearance:** GATE CSE 2024, Set 2  
**Question type:** MSQ  
**Marks:** 2  
**Source difficulty label:** medium

Consider -variable functions                                                    expressed in sum-of-minterms form as given below.

  With respect to the circuit given above, which of the following options is/are CORRECT?

  A.                                                                                            B.
  C.                                                                                            D.


## Week 22 — 10 questions

**Subject omitted this week:** Engineering Mathematics

### 22.1. Digital Logic — GATE CSE 2019, Question 6

**First appearance:** GATE CSE 2019  
**Question type:** MCQ  
**Marks:** 1  
**Source difficulty label:** unlabelled

Which one of the following is NOT a valid identity?

 A.                                                                                                  B.
 C.                                                                                                  D.

### 22.2. Operating Systems — GATE CSE 2024, Set 2, Question 15

**First appearance:** GATE CSE 2024, Set 2  
**Question type:** MSQ  
**Marks:** 1  
**Source difficulty label:** unlabelled

Consider a process                     running on a         . Which one or more of the following events will always trigger a
  context switch by the                    that results in process moving to a non-running state (e.g., ready, blocked)?

  A.    makes a blocking system call to read a block of data from the disk
  B.    tries to access a page that is in the swap space, triggering a page fault
  C. An interrupt is raised by the disk to deliver data requested by some other process
  D. A timer interrupt is raised by the hardware

### 22.3. Databases — GATE CSE 2021, Set 1, Question 33

**First appearance:** GATE CSE 2021, Set 1  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

Consider the relation                                                                 with the following functional dependencies.

Consider the decomposition of the relation                                            into the constituent relations according to the following two
decomposition schemes.

Which one of the following options is correct?

 A.    is a lossless decomposition, but     is a lossy decomposition
 B.    is a lossy decomposition, but    is a lossless decomposition
 C. Both     and      are lossless decompositions
 D. Both     and      are lossy decompositions

### 22.4. Computer Organization and Architecture — GATE CSE 2023, Question 31

**First appearance:** GATE CSE 2023  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

Consider the given -code and its corresponding assembly code, with a few operands          being
unknown. Some useful information as well as the semantics of each unique assembly instruction is
annotated as inline comments in the code. The memory is byte-addressable.

                                                                            ;assembly code (; indicates comments)
                                                                            ;r1-r5 are 32-bit integer registers
       //C-code                                                             ;initialize r1=0, r2=10
                                                                            ;initialize r3, r4 with base address of a, b

                                                                            L01: jeq r1, r2, end ;if(r1==r2) goto end
                                                                            L02: lw, r5, 0(r4) ;r5 <- Memory[r4+0]
       int a[10], b[10], i;                                                 L03: shl, r5, r5, U1 ;r5 <- r5 << U1
        // int is 32 bit                                                    L04: sw, r5, 0(r3) ;Memory[r3+0] <- r5
        for(i=0; i<10; i++)                                                 L05: add, r3, r3, U2 ;r3 <- r3+U2
          a[i] = b[i] * 8;                                                  L06: add, r4, r4, U3
                                                                            L07: add, r1, r1, 1
                                                                            L08: jmp U4         ;goto U4
                                                                            L09: end

Which one of the following options is a                                                       replacement for operands in the position
in the above assembly code?

 A.                                             B.                                            C.                           D.

### 22.5. General Aptitude — GATE CSE 2025, Set 1, Question 9

**First appearance:** GATE CSE 2025, Set 1  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

A square paper, shown in figure , is folded along the dotted lines as shown in the figures      and      .
Then a few cuts are made as shown in figure        . Which one of the following patterns will be obtained
when the paper is unfolded?
Note: The figures shown are representative.

 ​

 A.

 B.

 C.

 D.

### 22.6. Theory of Computation — GATE CSE 2021, Set 1, Question 38

**First appearance:** GATE CSE 2021, Set 1  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

Consider the following language:

Which one of the following deterministic finite automata accepts

                                                                                    B.
 A.

 C.
                                                                                                D.

### 22.7. Programming and Data Structures — GATE CSE 2022, Question 34

**First appearance:** GATE CSE 2022  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

What is printed by the following                                    program?
       #include<stdio.h>

       int main(int argc, char *argv[]) {

           char a = ‘P’;

           char b = ‘x’;

           char c = (a&b) + ‘*’;

           char d = (a|b) – ‘-’;

           char e = (a^b) + ‘+’;

           printf(“%c %c %c\n”, c, d, e);

           return 0;

       }

                 encoding for relevant characters is given below

  A.                                          B.                                 C.                           D.

### 22.8. Compiler Design — GATE CSE 2017, Set 1, Question 43

**First appearance:** GATE CSE 2017, Set 1  
**Question type:** NAT  
**Marks:** 2  
**Source difficulty label:** medium

Consider the following grammar:

      stmt   if expr then expr else expr; stmt | Ò
      expr   term relop term | term
      term   id | number
      id   a|b|c
      number

where relop is a relational operator e.g.,         Ò refers to the empty statement, and if, then, else are
terminals.
Consider a program following the above grammar containing ten if terminals. The number of control flow paths in
   is________ . For example. the program
if   then   else
has control flow paths.          and       .

### 22.9. Algorithms — GATE CSE 2016, Set 2, Question 13

**First appearance:** GATE CSE 2016, Set 2  
**Question type:** MCQ  
**Marks:** 1  
**Source difficulty label:** medium

Assume that the algorithms considered here sort the input sequences in ascending order. If the input is
already in the ascending order, which of the following are TRUE?

  I. Quicksort runs in                            time
 II. Bubblesort runs in                             time
III. Mergesort runs in                           time
IV. Insertion sort runs in                            time

 A. I and II only                               B. I and III only                          C. II and IV only              D. I and IV only

### 22.10. Computer Networks — GATE CSE 2024, Set 1, Question 19

**First appearance:** GATE CSE 2024, Set 1  
**Question type:** MSQ  
**Marks:** 1  
**Source difficulty label:** unlabelled

​    client   successfully establishes a connection to          server . Let     denote the sequence
number in the      sent from to . Let          denote the acknowledgement number in the SYN ACK from
  to . Which of the following statements is/are CORRECT?

    A. The sequence number    is chosen randomly by
    B. The sequence number    is always for a new connection
    C. The acknowledgement number     is equal to
    D. The acknowledgement number     is equal to


## Week 23 — 10 questions

**Subject omitted this week:** Computer Networks

### 23.1. General Aptitude — GATE CSE 2024, Set 2, Question 10

**First appearance:** GATE CSE 2024, Set 2  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

In the              array shown below, each cell of the first three rows has either a cross              or a number.

  The number in a cell represents the count of the immediate neighboring cells (left, right, top, bottom, diagonals)
  NOT having a cross (    ). Given that the last row has no crosses  , the sum of the four numbers to be filled in
  the last row is

  A.                                            B.                                   C.               D.

### 23.2. Engineering Mathematics — GATE CSE 2023, Question 16

**First appearance:** GATE CSE 2023  
**Question type:** MSQ  
**Marks:** 1  
**Source difficulty label:** hard

Geetha has a conjecture about integers, which is of the form

where            is a statement about integers, and                                   is a statement about pairs of integers. Which of the following (one or

more) option(s) would imply Geetha's conjecture?

 A.
 B.
 C.
 D.

### 23.3. Compiler Design — GATE CSE 2026, Set 1, Question 17

**First appearance:** GATE CSE 2026, Set 1  
**Question type:** MSQ  
**Marks:** 1  
**Source difficulty label:** unlabelled

Consider the following C statements:
       char *str1 = "Hello; /* Statement S1 */
       char *str2 = "Hello;"; /* Statement S2 */
       int *str3 = "Hello"; /* Statement S3 */

  Which of the following options is/are correct?

  A.        and     have syntactic errors
  B.        has a lexical error and    has a syntactic error
  C.        has a lexical error and    has a semantic error
  D.        has a syntactic error and     has a semantic error

### 23.4. Algorithms — GATE CSE 2026, Set 1, Question 13

**First appearance:** GATE CSE 2026, Set 1  
**Question type:** MSQ  
**Marks:** 1  
**Source difficulty label:** unlabelled

Let be an odd number greater than                                           . Consider a binary minheap with   elements stored in an array
  whose index starts from .

  Which of the following indices of                                  do/does NOT correspond to any leaf node of the minheap?

  A.                                                  B.                                         C.                          D.

### 23.5. Theory of Computation — GATE CSE 2025, Set 2, Question 42

**First appearance:** GATE CSE 2025, Set 2  
**Question type:** MSQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

Let                           . For                 , and                  , let                denote the number of occurrences of   in .
Which one or more of the following option(s) define(s) regular language(s)?

 A.
 B.
 C.                                                                          , and
 D.                                                                          , and

### 23.6. Operating Systems — GATE CSE 2026, Set 2, Question 45

**First appearance:** GATE CSE 2026, Set 2  
**Question type:** NAT  
**Marks:** 2  
**Source difficulty label:** unlabelled

Consider contiguous allocation of physical memory to processes using variable partitioning scheme.
  Suppose there are holes in the memory of sizes                 ,                                       , and
      KB. Assume that no two holes are adjacent. Two processes       of size     KB and      of size KB arrive in that
  order, and they are allocated memory using the best-fit technique. After allocating space to      and  , the number
  of holes of size less than KB is         . (answer in integer)

  Note:

### 23.7. Programming and Data Structures — GATE CSE 2023, Question 36

**First appearance:** GATE CSE 2023  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

Let    be a priority queue for maintaining a set of elements. Suppose is implemented using a max-heap
  data structure. The operation                           extracts and deletes the maximum element from .
  The operation                      inserts a new element      in . The properties of a max-heap are preserved at
  the end of each of these operations.

  When     contains                         elements, which one of the following statements about the worst case running time of these
  two operations is

  A. Both                                                    and                                        run in      .
  B. Both                                                    and                                        run in              .
  C.                                                    runs in              whereas                                    runs in      .
  D.                                                    runs in              whereas                                    runs in          .

### 23.8. Computer Organization and Architecture — GATE CSE 2023, Question 23

**First appearance:** GATE CSE 2023  
**Question type:** NAT  
**Marks:** 1  
**Source difficulty label:** unlabelled

Consider a -stage pipelined processor having a delay of            (nanoseconds),        , and        for the
first, second, and the third stages, respectively. Assume that there is no other delay and the processor does
not suffer from any pipeline hazards. Also assume that one instruction is fetched every cycle.

The total execution time for executing                                          instructions on this processor is _____________

### 23.9. Databases — GATE CSE 2020, Question 13

**First appearance:** GATE CSE 2020  
**Question type:** MCQ  
**Marks:** 1  
**Source difficulty label:** unlabelled

Consider a relational database containing the following schemas.

The primary key of each table is indicated by underlining the constituent fields.
      SELECT s.sno, s.sname
      FROM Suppliers s, Catalogue c
      WHERE s.sno=c.sno AND
        cost > (SELECT AVG (cost)
             FROM Catalogue
             WHERE pno = ‘P4’
             GROUP BY pno) ;

The number of rows returned by the above SQL query is

 A.                                          B.                     C.              D.

### 23.10. Digital Logic — GATE CSE 2025, Set 1, Question 14

**First appearance:** GATE CSE 2025, Set 1  
**Question type:** MSQ  
**Marks:** 1  
**Source difficulty label:** unlabelled

Let        be a -variable Boolean function that produces output as     when at least two of the input variables
are        . Which of the following statement(s) is/are CORRECT, where            are Boolean variables?

 A.
 B.
 C.                                                                  AND
 D.


## Week 24 — 10 questions

**Subject omitted this week:** Operating Systems

### 24.1. Engineering Mathematics — GATE CSE 2026, Set 2, Question 53

**First appearance:** GATE CSE 2026, Set 2  
**Question type:** NAT  
**Marks:** 2  
**Source difficulty label:** unlabelled

Suppose an unbiased coin is tossed times. Each coin toss is independent of all previous coin tosses. Let
      be the event that among the second, fourth, and sixth coin tosses, there are at least two heads. Let
  be the event that among the first, second, third, and fifth coin tosses, there are equal number of heads and tails.

  The conditional probability                                           is equal to                    . (rounded off to one decimal place)

### 24.2. Databases — GATE CSE 2024, Set 2, Question 46

**First appearance:** GATE CSE 2024, Set 2  
**Question type:** NAT  
**Marks:** 2  
**Source difficulty label:** unlabelled

A functional dependency                                              is termed as a useful functional dependency if and only if it satisfies
all the following three conditions:

         is not the empty set.
         is not the empty set.
      Intersection of and is the empty set.

For a relation              with         attributes, the total number of possible useful functional dependencies is __________.

### 24.3. Programming and Data Structures — GATE CSE 2016, Set 2, Question 34

**First appearance:** GATE CSE 2016, Set 2  
**Question type:** NAT  
**Marks:** 2  
**Source difficulty label:** medium

A complete binary min-heap is made by including each integer in                 exactly once. The depth of a
node in the heap is the length of the path from the root of the heap to that node. Thus, the root is at depth .
The maximum depth at which integer can appear is _________.

### 24.4. Compiler Design — GATE CSE 2026, Set 1, Question 18

**First appearance:** GATE CSE 2026, Set 1  
**Question type:** MSQ  
**Marks:** 1  
**Source difficulty label:** unlabelled

Which of the following statements is/are true?

  A.        parser uses backtracking
  B. For a grammar to be        , it must be left-recursive
  C. For a grammar to be        , it must be left-factored
  D. The        parsers are more powerful than the SLR parsers

### 24.5. Algorithms — GATE CSE 2020, Question 49

**First appearance:** GATE CSE 2020  
**Question type:** NAT  
**Marks:** 2  
**Source difficulty label:** unlabelled

Consider a graph                                        , where                        ,                                        , and
weight of the edge                             is         . The weight of minimum spanning tree of              is _________

### 24.6. General Aptitude — GATE CSE 2020, Question 8

**First appearance:** GATE CSE 2020  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

The figure below shows an annular ring with outer and inner as and , respectively. The annular space
  has been painted in the form of blue colour circles touching the outer and inner periphery of annular space. If
  maximum number of circles can be painted, then the unpainted area available in annular space is _____.

  A.                                                                                      B.
  C.                                                                                      D.

### 24.7. Theory of Computation — GATE CSE 2023, Question 29

**First appearance:** GATE CSE 2023  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

Consider the context-free grammar                                below

where           and        are non-terminals, and                     and         are terminal symbols. The starting non-terminal is .
Which one of the following statements is

 A. The language generated by                            is
 B. The language generated by                            is
 C. The language generated by                            is
 D. The language generated by                            is not a regular language

### 24.8. Computer Networks — GATE CSE 2023, Question 7

**First appearance:** GATE CSE 2023  
**Question type:** NAT  
**Marks:** 1  
**Source difficulty label:** unlabelled

Suppose two hosts are connected by a point-to-point link and they are configured to use
  protocol for reliable data transfer. Identify in which one of the following scenarios, the utilization of the link is
  the lowest.

  A. Longer link length and lower transmission rate
  B. Longer link length and higher transmission rate
  C. Shorter link length and lower transmission rate
  D. Shorter link length and higher transmission rate

### 24.9. Computer Organization and Architecture — GATE CSE 2019, Question 1

**First appearance:** GATE CSE 2019  
**Question type:** MCQ  
**Marks:** 1  
**Source difficulty label:** medium

A certain processor uses a fully associative cache of size   kB, The cache block size is bytes. Assume
that the main memory is byte addressable and uses a -bit address. How many bits are required for the
Tag and the Index fields respectively in the addresses generated by the processor?

 A.        bits and       bits                                                           B.   bits and   bits
 C.        bits and       bits                                                           D.   bits and   bits

### 24.10. Digital Logic — GATE CSE 2018, Question 4

**First appearance:** GATE CSE 2018  
**Question type:** MCQ  
**Marks:** 1  
**Source difficulty label:** medium

Let     and    denote the Exclusive OR and Exclusive NOR operations, respectively. Which one of the
following is NOT CORRECT?

 A.
 B.
 C.
 D.


## Week 25 — 10 questions

**Subject omitted this week:** Databases

### 25.1. Algorithms — GATE CSE 2026, Set 1, Question 52

**First appearance:** GATE CSE 2026, Set 1  
**Question type:** NAT  
**Marks:** 2  
**Source difficulty label:** unlabelled

The following sequence corresponds to the preorder traversal of a binary search tree                                             :

  The position of the element                           in the postorder traversal of               is       . (answer in integer)

  Note: The position begins with .

### 25.2. General Aptitude — GATE CSE 2026, Set 2, Question 8

**First appearance:** GATE CSE 2026, Set 2  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

Figures    and     represent intercity highway systems. The black dots represent cities and the line
  segments between them represent intercity highways.
  A salesperson needs to make a trip. She needs to start from a city, visit each of the remaining cities exactly once,
  and finally return to the same city from which she started.

  Which one of the following options is then true?

  A. Such a trip is possible for , but not for      .
  B. Such a trip is possible for    , but not for .
  C. Such a trip is possible for both     and     .
  D. Such a trip is possible neither for    nor for                                   .

### 25.3. Programming and Data Structures — GATE CSE 2016, Set 2, Question 12

**First appearance:** GATE CSE 2016, Set 2  
**Question type:** NAT  
**Marks:** 1  
**Source difficulty label:** medium

The value printed by the following program is _______.
      void f (int * p, int m) {
          m = m + 5;
         *p = *p + m;
          return;
      }
      void main () {
       int i=5, j=10;

          f (&i, j);
          printf ("%d", i+j);
      }

### 25.4. Theory of Computation — GATE CSE 2025, Set 1, Question 49

**First appearance:** GATE CSE 2025, Set 1  
**Question type:** MSQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

Consider a finite state machine (FSM) with one input  and one output , represented by the given state
  transition table. The minimum number of states required to realize this FSM is _______. (Answer in
  integer).

### 25.5. Operating Systems — GATE CSE 2024, Set 1, Question 47

**First appearance:** GATE CSE 2024, Set 1  
**Question type:** NAT  
**Marks:** 2  
**Source difficulty label:** unlabelled

Consider the following code snippet using the fork () and wait () system calls. Assume that the code
compiles and runs correctly, and that the system calls run successfully without any errors.

      int x=3;

      while (x>0){

      fork ();

      printf("hello");

      wait (NULL) ;

      X-- ;

      }

The total number of times the printf statement is executed is __________.

### 25.6. Digital Logic — GATE CSE 2023, Question 11

**First appearance:** GATE CSE 2023  
**Question type:** MCQ  
**Marks:** 1  
**Source difficulty label:** unlabelled

The output of a -input multiplexer is connected back to one of its inputs as shown in the figure.

Match the functional equivalence of this circuit to one of the following options.

 A.       Flip-flop                             B.         Latch                              C. Half-adder              D. Demultiplexer

### 25.7. Computer Networks — GATE CSE 2026, Set 2, Question 48

**First appearance:** GATE CSE 2026, Set 2  
**Question type:** NAT  
**Marks:** 2  
**Source difficulty label:** unlabelled

Consider a new TCP connection between a sender and a receiver. The receiver advertised window is
  constant at     KB, the maximum segment size (MSS) is KB, and the slow start threshold for TCP
  congestion control is    KB. Assume that there are no timeouts or duplicate acknowledgements. The number of
  rounds of transmission required for the congestion control algorithm of the TCP connection to reach the congestion
  avoidance phase is       . (answer in integer)

  Note:

### 25.8. Engineering Mathematics — GATE CSE 2016, Set 1, Question 1

**First appearance:** GATE CSE 2016, Set 1  
**Question type:** NAT  
**Marks:** 1  
**Source difficulty label:** medium

Let                  represents the following propositions.

               is a composite number.
               is a perfect square.
               is a prime number.

The integer                    which satisfies                                                     is ____________.

### 25.9. Compiler Design — GATE CSE 2016, Set 1, Question 46

**First appearance:** GATE CSE 2016, Set 1  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** medium

Consider the following Syntax Directed Translation Scheme                                                     , with non-terminals   and
terminals      .

Using the above                          , the output printed by a bottom-up parser, for the input                          is:

 A.                                                                                         B.
 C.                                                                                         D. syntax error

### 25.10. Computer Organization and Architecture — GATE CSE 2024, Set 1, Question 20

**First appearance:** GATE CSE 2024, Set 1  
**Question type:** MSQ  
**Marks:** 1  
**Source difficulty label:** unlabelled

Consider a -stage pipelined processor with Instruction Fetch (IF), Instruction Decode (ID), Execute (EX),
Memory Access (MEM), and Register Writeback (WB) stages. Which of the following statements about
forwarding is/are CORRECT?

 A. In a pipelined execution, forwarding means the result from a source stage of an earlier instruction is passed on
    to the destination stage of a later instruction
 B. In forwarding, data from the output of the MEM stage can be passed on to the input of the EX stage of the next
    instruction
 C. Forwarding cannot prevent all pipeline stalls
 D. Forwarding does not require any extra hardware to retrieve the data from the pipeline stages


## Week 26 — 10 questions

**Subject omitted this week:** Digital Logic

### 26.1. Algorithms — GATE CSE 2017, Set 1, Question 26

**First appearance:** GATE CSE 2017, Set 1  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** medium

Let                be      connected, undirected, edge-weighted graph. The weights of the edges in                                                are
positive and distinct. Consider the following statements:

  I. Minimum Spanning Tree of is always unique.
 II. Shortest path between any two vertices of is always unique.

Which of the above statements is/are necessarily true?

 A. I only                                         B. II only                                C. both I and II            D. neither I nor II

### 26.2. Compiler Design — GATE CSE 2019, Question 43

**First appearance:** GATE CSE 2019  
**Question type:** NAT  
**Marks:** 2  
**Source difficulty label:** unlabelled

Consider the augmented grammar given below:

Let                                                                The number of items in the set            is______

### 26.3. Programming and Data Structures — GATE CSE 2017, Set 1, Question 36

**First appearance:** GATE CSE 2017, Set 1  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** medium

Consider the C functions foo and bar given below:
 int foo(int val) {
    int x=0;
    while(val > 0) {
       x = x + foo(val--);

     }
     return val;
 }

 int bar(int val) {
    int x = 0;
    while(val > 0) {
       x= x + bar(val-1);
    }
    return val;
 }

Invocations of                          and              will result in:

 A. Return of           and         respectively.                                               B. Infinite   loop    and     abnormal
                                                                                                   termination respectively.
 C. Abnormal termination and infinite                                                            D. Both terminating abnormally.
    loop respectively.

### 26.4. Computer Organization and Architecture — GATE CSE 2026, Set 1, Question 44

**First appearance:** GATE CSE 2026, Set 1  
**Question type:** MSQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

Consider a system that has a cache memory unit and a memory management unit (MMU). The address
input to the cache memory is a physical address. The MMU has a translation lookaside buffer (TLB).
Assume that when a page is evicted from the main memory, the corresponding blocks in the cache are marked as
invalid.

For a given memory reference, which of the following sequences of events can NEVER happen?

 A. TLB miss, Page table hit, Cache                                                                 B. TLB hit, Page table miss, Cache
    hit                                                                                                hit
 C. TLB miss, Page table miss, Cache                                                                D. TLB miss, Page table miss, Cache
    hit                                                                                                miss

### 26.5. Engineering Mathematics — GATE CSE 2022, Question 42

**First appearance:** GATE CSE 2022  
**Question type:** MSQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

Which of the properties hold for the adjacency matrix                                        of a simple undirected unweighted graph having
vertices?

 A. The diagonal entries of      are the degrees of the vertices of the graph.
 B. If the graph is connected, then none of the entries of              can be zero.
 C. If the sum of all the elements of is at most              then the graph must be acyclic.
 D. If there is at least a in each of     rows and columns, then the graph must be connected.

### 26.6. Databases — GATE CSE 2024, Set 1, Question 36

**First appearance:** GATE CSE 2024, Set 1  
**Question type:** MSQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

Consider the following read-write schedule                                   over three transactions                     , and        , where the subscripts in
the schedule indicate transaction IDs:

Which of the following transaction schedules is/are conflict equivalent to ?

 A.                                          B.                                                  C.                              D.

### 26.7. General Aptitude — GATE CSE 2021, Set 2, Question 8

**First appearance:** GATE CSE 2021, Set 2  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

The number of students in three classes is in the ratio                                                    . If     students are added to each class, the
ratio changes to            .
The total number of students in all the three classes in the beginning was:

 A.                                                B.                                     C.                                   D.

### 26.8. Computer Networks — GATE CSE 2026, Set 2, Question 23

**First appearance:** GATE CSE 2026, Set 2  
**Question type:** NAT  
**Marks:** 1  
**Source difficulty label:** unlabelled

If an IP network uses a subnet mask of                , the maximum number of IP addresses that can be
  assigned to network interfaces is   . (answer in integer)

### 26.9. Theory of Computation — GATE CSE 2016, Set 2, Question 16

**First appearance:** GATE CSE 2016, Set 2  
**Question type:** NAT  
**Marks:** 1  
**Source difficulty label:** medium

The number of states in the minimum sized DFA that accepts the language defined by the regular
expression.

is ________.

### 26.10. Operating Systems — GATE CSE 2018, Question 39

**First appearance:** GATE CSE 2018  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** medium

In a system, there are three types of resources:     and . Four processes       ,   ,    and     execute
  concurrently. At the outset, the processes have declared their maximum resource requirements using a
  matrix named Max as given below. For example, Max[          ] is the maximum number of instances of     that
  would require. The number of instances of the resources allocated to the various processes at any given state is
  given by a matrix named Allocation.
  Consider a state of the system with the Allocation matrix as shown below, and in which                                                         instances of   and
  instances of are only resources available.

  From the perspective of deadlock avoidance, which one of the following is true?

   A. The system is in                       state
   B. The system is not in                       state, but would be                          if one more instance of                 were available
   C. The system is not in                       state, but would be                          if one more instance of                 were available
   D. The system is not in                       state, but would be                          if one more instance of                 were available


## Week 27 — 10 questions

**Subject omitted this week:** Algorithms

### 27.1. Computer Networks — GATE CSE 2026, Set 2, Question 55

**First appearance:** GATE CSE 2026, Set 2  
**Question type:** NAT  
**Marks:** 2  
**Source difficulty label:** unlabelled

It is necessary to design a link-layer protocol between two hosts that are directly connected over a lossless
  link of length         kilometers. Assume that the link bandwidth is          bits per second and that the
  propagation delay in the link is nanoseconds per meter. Every transmitted data byte is assigned a unique
  sequence number.

  Let           be the minimum number of bits needed for the sequence number field in the protocol header such that

    i. the sequence numbers do not wrap around before                                                 seconds, and
   ii. the maximum utilization of the link is achieved.

  The value of              is             . (answer in integer)

### 27.2. Compiler Design — GATE CSE 2026, Set 1, Question 43

**First appearance:** GATE CSE 2026, Set 1  
**Question type:** MSQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

​Consider the following two syntax-directed definitions                                            and   for type declarations.

           SDD1
 Grammar
         Semantic Rules
 (G1)

                            SDD2
 Grammar
                      Semantic Rules
 (G2)

     is the start symbol, and int, float and id are the three terminals. The non-terminal    is the same as      and the
  non-terminal      is the same as . Here, the subscript is used to differentiate the grammar symbols on the two
  sides of a production. The function put updates the symbol table with the type information for an identifier.
  Let and be the languages specified by grammars                                                       and    , respectively.
  Which of the following statements is/are true?

  A. The languages and are the same
  B.        is -attributed and contains only synthesized attributes
  C.        is -attributed and contains only inherited attributes
  D. The specifications of       and         are such that the same entries get added to the symbol table

### 27.3. General Aptitude — GATE CSE 2018, Question 9

**First appearance:** GATE CSE 2018  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** medium

In the figure below,                                               is equal to _____

  A.                                                                                          B.
  C.                                                                                          D.

### 27.4. Computer Organization and Architecture — GATE CSE 2026, Set 2, Question 47

**First appearance:** GATE CSE 2026, Set 2  
**Question type:** NAT  
**Marks:** 2  
**Source difficulty label:** unlabelled

A non-pipelined instruction execution unit that operates at       GHz clock takes an average of clock cycles
  to complete the execution of an instruction. To improve the performance, the system was pipelined with a
  goal of achieving an average throughput of one instruction per clock cycle. However, it could operate only at
  GHz due to pipeline overheads. While executing a program in the pipelined design,                   of instructions
  encountered a stall of cycles due to pipeline hazards. The speed-up obtained by the pipelined design over the
  non-pipelined one for this program is            (rounded off to two decimal places)

  Note:

### 27.5. Theory of Computation — GATE CSE 2016, Set 2, Question 43

**First appearance:** GATE CSE 2016, Set 2  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** medium

Consider the following languages:

Which one of the following is TRUE?

 A. Both    and      are context-free.
 B.    is context-free while    is not context-free.
 C.    is context-free while    is not context-free.
 D. Neither    nor     is context-free.

### 27.6. Engineering Mathematics — GATE CSE 2023, Question 18

**First appearance:** GATE CSE 2023  
**Question type:** MSQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

Let

be a real-valued function.
Which of the following statements is/are

 A.          does              not     have        a      local                           B.      has a local maximum.
       maximum.

 C.         does                 not     have        a     local                               D.        has a local minimum.
      minimum.

### 27.7. Programming and Data Structures — GATE CSE 2020, Question 46

**First appearance:** GATE CSE 2020  
**Question type:** NAT  
**Marks:** 2  
**Source difficulty label:** unlabelled

Consider the following C functions.

       int fun1(int n) {                                                                          int fun2(int n) {
           static int i= 0;                                                                          static int i= 0;
           if (n > 0) {                                                                              if (n>0) {
              ++i;                                                                                      i = i+ fun1 (n) ;
             fun1(n-1);                                                                                 fun2(n-1) ;
          }                                                                                         }
         return (i);                                                                              return (i);
       }                                                                                          }

The return value of                             is _________

### 27.8. Operating Systems — GATE CSE 2020, Question 35

**First appearance:** GATE CSE 2020  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

Consider the following five disk access requests of the form (request id, cylinder number) that are present in
the disk scheduler queue at a given time.

  Assume the head is positioned at cylinder                          . The scheduler follows Shortest Seek Time First scheduling to
  service the requests.
  Which one of the following statements is FALSE?

  A.   is serviced before .
  B.    is serviced after ,but before .
  C. The head reverses its direction of movement between servicing of                     and   .
  D.   is serviced before .

### 27.9. Databases — GATE CSE 2021, Set 2, Question 21

**First appearance:** GATE CSE 2021, Set 2  
**Question type:** NAT  
**Marks:** 1  
**Source difficulty label:** unlabelled

A data file consisting of             student-records is stored on a hard disk with block size of         bytes.
The data file is sorted on the primary key          . The size of a record pointer for this disk is bytes. Each
student-record has a candidate key attribute called            of size    bytes. Suppose an index file with records
consisting of two fields,          value and the record pointer the corresponding student record, is built and stored
on the same disk. Assume that the records of data file and index file are not split across disk blocks. The number of
blocks in the index file is ________

### 27.10. Digital Logic — GATE CSE 2021, Set 1, Question 6

**First appearance:** GATE CSE 2021, Set 1  
**Question type:** MCQ  
**Marks:** 1  
**Source difficulty label:** medium

Let the representation of a number in base                                            be           . What is the hexadecimal representation of the
number?

 A.                                             B.                                           C.                           D.


## Week 28 — 10 questions

**Subject omitted this week:** Compiler Design

### 28.1. Algorithms — GATE CSE 2017, Set 1, Question 48

**First appearance:** GATE CSE 2017, Set 1  
**Question type:** NAT  
**Marks:** 2  
**Source difficulty label:** medium

Let    be an array of      numbers consisting of a sequence of 's followed by a sequence of 's. The
  problem is to find the smallest index such that     is by probing the minimum number of locations in .
  The worst case number of probes performed by an optimal algorithm is ____________.

### 28.2. Databases — GATE CSE 2018, Question 11

**First appearance:** GATE CSE 2018  
**Question type:** MCQ  
**Marks:** 1  
**Source difficulty label:** medium

In an Entity-Relationship (ER) model, suppose is a many-to-one relationship from entity set E1 to entity
set E2. Assume that E1 and E2 participate totally in and that the cardinality of E1 is greater than the
cardinality of E2.
Which one of the following is true about                            ?

 A. Every entity in E1 is associated with exactly one entity in E2
 B. Some entity in E1 is associated with more than one entity in E2
 C. Every entity in E2 is associated with exactly one entity in E1
 D. Every entity in E2 is associated with at most one entity in E1

### 28.3. General Aptitude — GATE CSE 2017, Set 2, Question 3

**First appearance:** GATE CSE 2017, Set 2  
**Question type:** MCQ  
**Marks:** 1  
**Source difficulty label:** medium

There are five buildings called , , ,       and                                          in a row (not necessarily in that order). is to the West
  of . is to the East of      and the West of .                                          is to the West of . Which is the building in the middle?

  A.                                            B.                                          C.                        D.

### 28.4. Digital Logic — GATE CSE 2021, Set 2, Question 44

**First appearance:** GATE CSE 2021, Set 2  
**Question type:** MSQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

If the numerical value of a -byte unsigned integer on a little endian computer is more than that on a big
  endian computer, which of the following choices represent(s) the unsigned integer on a little endian
  computer?

  A.                                             B.            C.                    D.

### 28.5. Engineering Mathematics — GATE CSE 2023, Question 43

**First appearance:** GATE CSE 2023  
**Question type:** MSQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

Consider a random experiment where two fair coins are tossed. Let be the event that denotes          on
  both the throws,   be the event that denotes           on the first throw, and be the event that denotes
          on the second throw. Which of the following statements is/are

  A.       and           are independent.                                                           B.   and    are independent.
  C.       and           are independent.                                                           D.

### 28.6. Operating Systems — GATE CSE 2020, Question 53

**First appearance:** GATE CSE 2020  
**Question type:** NAT  
**Marks:** 2  
**Source difficulty label:** unlabelled

Consider a paging system that uses -level page table residing in main memory and a                    for address
translation. Each main memory access takes              ns and           lookup takes      ns. Each page transfer
to/from the disk takes          ns. Assume that the         hit ratio is      , page fault rate is    . Assume that for
      of the total page faults, a dirty page has to be written back to disk before the required page is read from disk.
       update time is negligible. The average memory access time in ns (round off to decimal places) is
___________

### 28.7. Programming and Data Structures — GATE CSE 2020, Question 41

**First appearance:** GATE CSE 2020  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

In a balanced binary search tree with elements, what is the worst case time complexity of reporting all
elements in range     ? Assume that the number of reported elements is .

 A.                                                                                                B.
 C.                                                                                                D.

### 28.8. Computer Organization and Architecture — GATE CSE 2018, Question 50

**First appearance:** GATE CSE 2018  
**Question type:** NAT  
**Marks:** 2  
**Source difficulty label:** unlabelled

The instruction pipeline of a RISC processor has the following stages: Instruction Fetch       , Instruction
Decode          , Operand Fetch      , Perform Operation          and Writeback       , The      ,    ,
and        stages take clock cycle each for every instruction. Consider a sequence of     instructions. In the
stage,      instructions take clock cycles each,     instructions take clock cycles each, and the remaining
instructions take clock cycle each. Assume that there are no data hazards and no control hazards.

The number of clock cycles required for completion of execution of the sequence of instruction is _____.

### 28.9. Theory of Computation — GATE CSE 2025, Set 1, Question 34

**First appearance:** GATE CSE 2025, Set 1  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

Consider the following two languages over the alphabet                                                      :

Which ONE of the following statements is CORRECT?

 A. Both     and      are regular languages.
 B.    is a regular language but       is not a regular language.
 C.    is not a regular language but       is a regular language.
 D. Neither     nor     is a regular language.

### 28.10. Computer Networks — GATE CSE 2022, Question 45

**First appearance:** GATE CSE 2022  
**Question type:** MSQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

Consider routing table of an organization’s router shown below:

Which of the following prefixes in                                     notation can be collectively used to correctly aggregate all of the subnets
in the routing table?

 A.                                                                                       B.
 C.                                                                                       D.


## Week 29 — 10 questions

**Subject omitted this week:** Databases

### 29.1. Programming and Data Structures — GATE CSE 2019, Question 53

**First appearance:** GATE CSE 2019  
**Question type:** NAT  
**Marks:** 2  
**Source difficulty label:** unlabelled

Consider the following C program:
      #include <stdio.h>
      int main()
      {
         int a[] = {2, 4, 6, 8, 10};
         int i, sum=0, *b=a+4;
         for (i=0; i<5; i++)
            sum=sum+(*b-i)-*(b-i);
         printf("%d\n", sum);
         return 0;
      }

The output of the above C program is _______

### 29.2. Algorithms — GATE CSE 2023, Question 46

**First appearance:** GATE CSE 2023  
**Question type:** NAT  
**Marks:** 2  
**Source difficulty label:** unlabelled

Let                . Let    denote the powerset of . Consider an undirected graph whose vertex set is
   . For any                     is an edge in     if and only if (i)      , and (ii) either     or        .
For any vertex    in , the set of all possible orderings in which the vertices of can be visited in a Breadth First
Search         starting from is denoted by          .

If     denotes the empty set, then the cardinality of                                           is ______________.

### 29.3. Computer Networks — GATE CSE 2021, Set 2, Question 54

**First appearance:** GATE CSE 2021, Set 2  
**Question type:** NAT  
**Marks:** 2  
**Source difficulty label:** unlabelled

Consider a network using the pure            medium access control protocol, where each frame is of length
          bits. The channel transmission rate is Mbps (         bits per second). The aggregate number of
  transmissions across all the nodes (including new frame transmissions and retransmitted frames due to collisions)
  is modelled as a Poisson process with a rate of        frames per second. Throughput is defined as the average
  number of frames successfully transmitted per second. The throughput of the network (rounded to the nearest
  integer) is ______________

### 29.4. Theory of Computation — GATE CSE 2016, Set 1, Question 42

**First appearance:** GATE CSE 2016, Set 1  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** medium

Consider the following context-free grammars;

Which one of the following pairs of languages is generated by                 and   ,respectively?

 A.                                                  and
 B.                                                    and
 C.                                                  and
 D.                                                    and

### 29.5. General Aptitude — GATE CSE 2021, Set 1, Question 8

**First appearance:** GATE CSE 2021, Set 1  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

There are five bags each containing identical sets of ten distinct chocolates. One chocolate is picked from
each bag.
The probability that at least two chocolates are identical is __________

 A.                                            B.                                        C.                               D.

### 29.6. Engineering Mathematics — GATE CSE 2023, Question 45

**First appearance:** GATE CSE 2023  
**Question type:** MSQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

Let     be a simple, finite, undirected graph with vertex set                 . Let       denote the maximum
degree of     and let                  denote the set of all possible colors. Color the vertices of using the
following greedy strategy: for
                                             : no neighbour of                      is colored
Which of the following statements is/are

 A. This procedure results in a proper vertex coloring of .
 B. The number of colors used is at most             .
 C. The number of colors used is at most        .
 D. The number of colors used is equal to the chromatic number of                                  .

### 29.7. Digital Logic — GATE CSE 2026, Set 2, Question 49

**First appearance:** GATE CSE 2026, Set 2  
**Question type:** NAT  
**Marks:** 2  
**Source difficulty label:** unlabelled

Consider the digital circuit shown below with two input lines  and , two select lines   and , and an
  output line . The blocks      and   represent active high     decoder and -to- multiplexer, respectively.
  Out of     possible input combinations, the number of combinations that produce          is          (answer in
  integer)
  Note: One input combination is an instance of [                                              ].

### 29.8. Computer Organization and Architecture — GATE CSE 2024, Set 1, Question 46

**First appearance:** GATE CSE 2024, Set 1  
**Question type:** NAT  
**Marks:** 2  
**Source difficulty label:** unlabelled

A given program has           load/store instructions. Suppose the ideal        (cycles per instruction) without
any memory stalls is . The program exhibits          miss rate on instruction cache and       miss rate on data
cache. The miss penalty is         cycles. The speedup (rounded off to two decimal places) achieved with a perfect
cache (i.e., with NO data or instruction cache misses) is __________.

### 29.9. Operating Systems — GATE CSE 2023, Question 17

**First appearance:** GATE CSE 2023  
**Question type:** MSQ  
**Marks:** 1  
**Source difficulty label:** unlabelled

Which one or more of the following                                         scheduling algorithms can potentially cause starvation?

 A. First-in First-Out                                                                                B. Round Robin
 C. Priority Scheduling                                                                               D. Shortest Job First

### 29.10. Compiler Design — GATE CSE 2025, Set 2, Question 12

**First appearance:** GATE CSE 2025, Set 2  
**Question type:** MCQ  
**Marks:** 1  
**Source difficulty label:** unlabelled

​Given the following syntax directed translation rules:

      Rule 1:
      Rule 2:
      Rule 3:

Which ONE is the CORRECT option among the following?

 A. Rule      is -attributed and -attributed; Rule is -attributed and not -attributed; Rule is neither -
    attributed nor -attributed.
 B. Rule is neither -attributed not -attributed; Rule is -attributed and -attributed; Rule is -attributed
    and -attributed.
 C. Rule is neither -attributed nor -attributed; Rule is not -attributed and is -attributed; Rule is -
    attributed and -attributed.
 D. Rule is -attributed and not -attributed; Rule is not -attributed and is -attributed; Rule is -attributed
    and -attributed.


## Week 30 — 10 questions

**Subject omitted this week:** Compiler Design

### 30.1. General Aptitude — GATE CSE 2020, Question 10

**First appearance:** GATE CSE 2020  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

The total revenue of a company during                  is shown in the bar graph. If the total expenditure of
  the company in each year is     million rupees, then the aggregate profit or loss (in percentage) on the total
  expenditure of the company during                 is ___________.

  A.               profit                                                                             B.               loss
  C.            profit                                                                                D.           loss

### 30.2. Computer Organization and Architecture — GATE CSE 2020, Question 43

**First appearance:** GATE CSE 2020  
**Question type:** NAT  
**Marks:** 2  
**Source difficulty label:** unlabelled

Consider a non-pipelined processor operating at       GHz. It takes clock cycles to complete an instruction.
You are going to make a - stage pipeline out of this processor. Overheads associated with pipelining force
you to operate the pipelined processor at GHz. In a given program, assume that             are memory instructions,
      are ALU instructions and the rest are branch instructions.       of the memory instructions cause stalls of
clock cycles each due to cache misses and          of the branch instructions cause stalls of cycles each. Assume
that there are no stalls associated with the execution of ALU instructions. For this program, the speedup achieved
by the pipelined processor over the non-pipelined processor (round off to decimal places) is_____________.

### 30.3. Databases — GATE CSE 2016, Set 1, Question 51

**First appearance:** GATE CSE 2016, Set 1  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** medium

Consider the following two phase locking protocol. Suppose a transaction accesses (for read or write
operations), a certain set of objects          . This is done in the following manner:

                 . acquires exclusive locks to                          in increasing order of their addresses.
                 . The required operations are performed .
                 . All locks are released

This protocol will

 A.    guarantee serializability and deadlock-freedom
 B.    guarantee neither serializability nor deadlock-freedom
 C.    guarantee serializability but not deadlock-freedom
 D.    guarantee deadlock-freedom but not serializability.

### 30.4. Operating Systems — GATE CSE 2018, Question 10

**First appearance:** GATE CSE 2018  
**Question type:** MCQ  
**Marks:** 1  
**Source difficulty label:** medium

Consider a process executing on an operating system that uses demand paging. The average time for a
memory access in the system is      units if the corresponding memory page is available in memory, and
units if the memory access causes a page fault. It has been experimentally measured that the average time taken
for a memory access in the process is    units.
Which one of the following is the correct expression for the page fault rate experienced by the process.

 A.                                          B.                                         C.   D.

### 30.5. Engineering Mathematics — GATE CSE 2024, Set 2, Question 41

**First appearance:** GATE CSE 2024, Set 2  
**Question type:** MSQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

Let        be an undirected connected graph in which every edge has a positive integer weight. Suppose that

every spanning tree in                       has even weight. Which of the following statements is/are TRUE for every such
graph ?

 A. All edges in have even weight
 B. All edges in have even weight        all edges in have odd weight
 C. In each cycle in , all edges in have even weight
 D. In each cycle in , either all edges in have even weight      all edges in                                                have odd weight

### 30.6. Algorithms — GATE CSE 2016, Set 2, Question 41

**First appearance:** GATE CSE 2016, Set 2  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** medium

In an adjacency list representation of an undirected simple graph                  , each edge       has two
adjacency list entries:   in the adjacency list of , and    in the adjacency list of . These are called twins
of each other. A twin pointer is a pointer from an adjacency list entry to its twin. If        and         , and the
memory size is not a constraint, what is the time complexity of the most efficient algorithm to set the twin pointer in
each entry in each adjacency list?

 A.                                                                    B.
 C.                                                                    D.

### 30.7. Computer Networks — GATE CSE 2016, Set 2, Question 24

**First appearance:** GATE CSE 2016, Set 2  
**Question type:** MCQ  
**Marks:** 1  
**Source difficulty label:** medium

In an Ethernet local area network, which one of the following statements is TRUE?

  A. A station stops to sense the channel once it starts transmitting a frame.
  B. The purpose of the jamming signal is to pad the frames that are smaller than the minimum frame size.
  C. A station continues to transmit the packet even after the collision is detected.
  D. The exponential back off mechanism reduces the probability of collision on retransmissions.

### 30.8. Programming and Data Structures — GATE CSE 2019, Question 40

**First appearance:** GATE CSE 2019  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

Consider the following statements:

  I. The smallest element in a max-heap is always at a leaf node
 II. The second largest element in a max-heap is always a child of a root node
III. A max-heap can be constructed from a binary search tree in        time
IV. A binary search tree can be constructed from a max-heap in         time

Which of the above statements are TRUE?

 A. I, II and III                                 B. I, II and IV                                 C. I, III and IV            D. II, III and IV

### 30.9. Theory of Computation — GATE CSE 2022, Question 2

**First appearance:** GATE CSE 2022  
**Question type:** MCQ  
**Marks:** 1  
**Source difficulty label:** unlabelled

Which one of the following regular expressions correctly represents the language of the finite automaton
given below?

A.
B.
C.
D.

### 30.10. Digital Logic — GATE CSE 2019, Question 22

**First appearance:** GATE CSE 2019  
**Question type:** NAT  
**Marks:** 1  
**Source difficulty label:** unlabelled

Two numbers are chosen independently and uniformly at random from the set
The probability (rounded off to decimal places) that their   (unsigned) binary representations have the
same most significant bit is ___________.


## Week 31 — 10 questions

**Subject omitted this week:** Engineering Mathematics

### 31.1. Computer Organization and Architecture — GATE CSE 2016, Set 2, Question 50

**First appearance:** GATE CSE 2016, Set 2  
**Question type:** NAT  
**Marks:** 2  
**Source difficulty label:** medium

A file system uses an in-memory cache to cache disk blocks. The miss rate of the cache is shown in the
figure. The latency to read a block from the cache is ms and to read a block from the disk is          ms.
Assume that the cost of checking whether a block exists in the cache is negligible. Available cache sizes are in
multiples of   MB.

The smallest cache size required to ensure an average read latency of less than ms is _________ MB.

### 31.2. Theory of Computation — GATE CSE 2022, Question 38

**First appearance:** GATE CSE 2022  
**Question type:** MSQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

Consider the following languages:

Which of the following statements is/are

 A.    is not context-free but       and      are deterministic context-free.
 B. Neither     nor      is context-free.
 C.         and             all are context-free.
 D. Neither     nor its complement is context-free.

### 31.3. General Aptitude — GATE CSE 2023, Question 9

**First appearance:** GATE CSE 2023  
**Question type:** NAT  
**Marks:** 2  
**Source difficulty label:** unlabelled

and       are functions of and , respectively, and                                               for all values of   and . Which one
of the following options is necessarily     for a and

 A.                   and                                                           B.                    constant
 C.                    constant              and                                    D.
      constant

### 31.4. Digital Logic — GATE CSE 2025, Set 2, Question 24

**First appearance:** GATE CSE 2025, Set 2  
**Question type:** MSQ  
**Marks:** 1  
**Source difficulty label:** unlabelled

In a -bit ripple counter, if the period of the waveform at the last flip-flop is                                                microseconds, then the
  frequency of the ripple counter in kHz is _________. (Answer in integer)

### 31.5. Computer Networks — GATE CSE 2016, Set 1, Question 55

**First appearance:** GATE CSE 2016, Set 1  
**Question type:** NAT  
**Marks:** 2  
**Source difficulty label:** medium

A sender uses the Stop-and-Wait           protocol for reliable transmission of frames. Frames are of size
       bytes and the transmission rate at the sender is                                            Size of an
  acknowledgment is      bytes and the transmission rate at the receiver is           The one-way propagation delay
  is    milliseconds.

  Assuming no frame is lost, the sender throughput is ________ bytes/ second.

### 31.6. Programming and Data Structures — GATE CSE 2016, Set 1, Question 35

**First appearance:** GATE CSE 2016, Set 1  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** medium

What will be the output of the following                                    program?
 void count (int n) {
   static int d=1;

     printf ("%d",n);
     printf ("%d",d);
     d++;
     if (n>1) count (n-1);
     printf ("%d",d);

 }

 void main(){
   count (3);
 }

 A.
 B.
 C.
 D.

### 31.7. Compiler Design — GATE CSE 2024, Set 2, Question 11

**First appearance:** GATE CSE 2024, Set 2  
**Question type:** MCQ  
**Marks:** 1  
**Source difficulty label:** easy

​Consider the following two sets:

Which one of the following options is the CORRECT match from Set                                                to Set   ?

 A.
 B.
 C.
 D.

### 31.8. Databases — GATE CSE 2017, Set 1, Question 23

**First appearance:** GATE CSE 2017, Set 1  
**Question type:** NAT  
**Marks:** 1  
**Source difficulty label:** unlabelled

Consider a database that has the relation schema EMP (EmpId, EmpName, and DeptName). An instance of
the schema EMP and a SQL query on it are given below:

 SELECT AVG(EC.Num)
 FROM EC
 WHERE (DeptName, Num) IN
   (SELECT DeptName, COUNT(EmpId) AS
             EC(DeptName, Num)
   FROM EMP
   GROUP BY DeptName)

The output of executing the SQL query is _____________ .

### 31.9. Operating Systems — GATE CSE 2024, Set 1, Question 15

**First appearance:** GATE CSE 2024, Set 1  
**Question type:** MSQ  
**Marks:** 1  
**Source difficulty label:** unlabelled

Which of the following process state transitions is/are NOT possible?

 A. Running to Ready                                                                                  B. Waiting to Running
 C. Ready to Waiting                                                                                  D. Running to Terminated

### 31.10. Algorithms — GATE CSE 2026, Set 1, Question 14

**First appearance:** GATE CSE 2026, Set 1  
**Question type:** MSQ  
**Marks:** 1  
**Source difficulty label:** unlabelled

Consider a hash table                    that is initially empty. The hash table is maintained using open
  addressing with linear probing. The hash function used is                          .
  Consider the following sequence of insertions performed on                                                  :

  Which of the following positions in the hash table is/are empty after these insertions are performed?

  A.                                            B.                                               C.                           D.


## Week 32 — 10 questions

**Subject omitted this week:** General Aptitude

### 32.1. Computer Organization and Architecture — GATE CSE 2025, Set 2, Question 29

**First appearance:** GATE CSE 2025, Set 2  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

For a direct-mapped cache, bits are used for the tag field and    bits are used to index into a cache block.
  The size of each cache block is one byte. Assume that there is no other information stored for each cache
  block.

  Which ONE of the following is the CORRECT option for the sizes of the main memory and the cache memory in
  this system (byte addressable), respectively?

  A.       KB and          KB                 B.            KB and             KB              C.      KB and   KB   D.   KB and   KB

### 32.2. Theory of Computation — GATE CSE 2019, Question 31

**First appearance:** GATE CSE 2019  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

Which one of the following languages over                                           is NOT context-free?

 A.
 B.
 C.
 D.

### 32.3. Computer Networks — GATE CSE 2017, Set 2, Question 20

**First appearance:** GATE CSE 2017, Set 2  
**Question type:** NAT  
**Marks:** 1  
**Source difficulty label:** unlabelled

The maximum number of                              router addresses that can be listed in the record route (RR) option field of an
       header is______.

### 32.4. Operating Systems — GATE CSE 2026, Set 2, Question 44

**First appearance:** GATE CSE 2026, Set 2  
**Question type:** MSQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

A system has a Translation Lookaside Buffer (TLB) that has a reach of MB. TLB reach is defined as the
  total amount of physical memory that can be accessed through the TLB entries. The paging system uses
  pages of size KB. The virtual address space is      GB and physical address space is GB. If each TLB entry
  stores a -bit process id, page number, frame number, and a -bit control field, then the size of the TLB (in bytes) is
          . (answer in integer)

  Note:

### 32.5. Programming and Data Structures — GATE CSE 2026, Set 2, Question 40

**First appearance:** GATE CSE 2026, Set 2  
**Question type:** MSQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

Consider a stack     and a queue . Both of them are initially empty and have the capacity to store ten
elements each. The elements           , and arrive one by one, in that order. When an element arrives, it
is assigned either to (pushed on ) or to      (enqueued to      ). Once all the five elements are stored, the output
is generated in two steps. First, stack S is emptied by popping all elements. Then queue             is emptied by
dequeueing all elements. The output obtained by following this process is        .

Given the output, the objective is to predict whether an element was assigned to                                     or    .

Which of the following options is/are possible valid assignment(s) of the elements?

Note: In the options, the notation                                 denotes that element            was assigned to   and       denotes that element
was assigned to .

 A.                                                                                       B.
 C.                                                                                       D.

### 32.6. Digital Logic — GATE CSE 2024, Set 1, Question 54

**First appearance:** GATE CSE 2024, Set 1  
**Question type:** MSQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

Consider a digital logic circuit consisting of three -to- multiplexers                                             , and      as shown below.
and     are inputs of      .      and     are inputs of   .      , and                                       are select lines of       , and     ,
respectively.

For an instance of inputs                                                                    , and          , the number of combinations of          that
give the output        is ____________.

### 32.7. Compiler Design — GATE CSE 2019, Question 3

**First appearance:** GATE CSE 2019  
**Question type:** MCQ  
**Marks:** 1  
**Source difficulty label:** unlabelled

Which one of the following kinds of derivation is used by LR parsers?

 A. Leftmost                                                                         B. Leftmost in reverse
 C. Rightmost                                                                        D. Rightmost in reverse

### 32.8. Databases — GATE CSE 2024, Set 2, Question 10

**First appearance:** GATE CSE 2024, Set 2  
**Question type:** MCQ  
**Marks:** 1  
**Source difficulty label:** unlabelled

In the context of owner and weak entity sets in the           (Entity-Relationship) data model, which one of the
following statements is TRUE?

 A. The weak entity set MUST have total participation in the identifying relationship
 B. The owner entity set MUST have total participation in the identifying relationship
 C. Both weak and owner entity sets MUST have total participation in the identifying relationship
 D. Neither weak entity set nor owner entity set MUST have total participation in the identifying relationship

### 32.9. Algorithms — GATE CSE 2025, Set 2, Question 19

**First appearance:** GATE CSE 2025, Set 2  
**Question type:** MSQ  
**Marks:** 1  
**Source difficulty label:** unlabelled

Which of the following statements regarding Breadth First Search (BFS) and Depth First Search (DFS) on
  an undirected simple graph is/are TRUE?

  A. A DFS tree of is a Shortest Path tree of .
  B. Every non-tree edge of with respect to a DFS tree is a forward/back edge.
  C. If        is a non-tree edge of with respect to a BFS tree, then the distances from the source vertex                                   to   and
        in the BFS tree are within   of each other.
  D. Both BFS and DFS can be used to find the connected components of .

### 32.10. Engineering Mathematics — GATE CSE 2021, Set 1, Question 43

**First appearance:** GATE CSE 2021, Set 1  
**Question type:** MSQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

A relation           is said to be circular if                           and                together imply            .
Which of the following options is/are correct?

 A. If a relation            is reflexive and symmetric, then is an equivalence relation.
 B. If a relation            is circular and symmetric, then is an equivalence relation.
 C. If a relation            is reflexive and circular, then is an equivalence relation.
 D. If a relation            is transitive and circular, then is an equivalence relation.


## Week 33 — 10 questions

**Subject omitted this week:** Engineering Mathematics

### 33.1. Algorithms — GATE CSE 2025, Set 1, Question 33

**First appearance:** GATE CSE 2025, Set 1  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

Let          be an undirected and unweighted graph with   vertices. Let      denote the number of
  edges in a shortest path between vertices and in . Let the maximum value of                   such
  that      , be . Let be any breadth-first-search tree of . Which ONE of the given options is CORRECT for
  every such graph ?

  A. The height of                   is exactly             .                                                B. The height of   is exactly    .
  C. The height of                   is at least            .                                                D. The height of   is at least   .

### 33.2. Compiler Design — GATE CSE 2025, Set 1, Question 2

**First appearance:** GATE CSE 2025, Set 1  
**Question type:** MCQ  
**Marks:** 1  
**Source difficulty label:** easy

Which ONE of the following statements is FALSE regarding the symbol table?

  A. Symbol table is responsible for keeping track of the scope of variables.
  B. Symbol table can be implemented using a binary search tree.
  C. Symbol table is not required after the parsing phase.
  D. Symbol table is created during the lexical analysis phase.

### 33.3. Theory of Computation — GATE CSE 2022, Question 13

**First appearance:** GATE CSE 2022  
**Question type:** MSQ  
**Marks:** 1  
**Source difficulty label:** unlabelled

Which of the following statements is/are

 A. Every subset of a recursively enumerable language is recursive.
 B. If a language and its complement are both recursively enumerable, then                                                                    must be recursive.
 C. Complement of a context-free language must be recursive.
 D. If     and   are regular, then      must be deterministic context-free.

### 33.4. Digital Logic — GATE CSE 2026, Set 1, Question 12

**First appearance:** GATE CSE 2026, Set 1  
**Question type:** MSQ  
**Marks:** 1  
**Source difficulty label:** unlabelled

Consider the -bit signed integers      and                                                  represented using the sign-magnitude form. The binary
representations of and are as follows:

Which of the following operations to compute                                        result(s) in an arithmetic overflow?

 A.                                              B.                                             C.                           D.

### 33.5. General Aptitude — GATE CSE 2021, Set 1, Question 9

**First appearance:** GATE CSE 2021, Set 1  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

Given below are two statements                                      and , and two conclusions and

                                  All bacteria are microorganisms.
                                  All pathogens are microorganisms.

                                   Some pathogens are bacteria.
                                    All pathogens are not bacteria.

Based on the above statements and conclusions, which one of the following options is logically                                                             ?

 A. Only conclusion is correct                                                                          B. Only conclusion is correct
 C. Either conclusion or is correct                                                                     D. Neither conclusion      nor     is
                                                                                                           correct

### 33.6. Databases — GATE CSE 2019, Question 14

**First appearance:** GATE CSE 2019  
**Question type:** MCQ  
**Marks:** 1  
**Source difficulty label:** unlabelled

Which one of the following statements is NOT correct about the                        tree data structure used for creating an
index of a relational database table?

 A.     Tree is a height-balanced tree
 B. Non-leaf nodes have pointers to data records
 C. Key values in each node are kept in sorted order
 D. Each leaf node has a pointer to the next leaf node

### 33.7. Operating Systems — GATE CSE 2025, Set 2, Question 38

**First appearance:** GATE CSE 2025, Set 2  
**Question type:** MSQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

consists of all active processes in an operating system.
                                          consists of single instances of distinct types of resources in the system.
  The resource allocation graph has the following assignment and claim edges.
  Assignment edges:                                                                                       (the assignment edge    means resource
     is assigned to process                       , and so on for others)
  Claim edges:                                                                                                (the claim edge    means process
  is waiting for resource                   , and so on for others)
  Which of the following statement(s) is/are CORRECT?

  A. Aborting              makes the system deadlock free.
  B. Aborting              makes the system deadlock free.
  C. Aborting              makes the system deadlock free.
  D. Aborting              and    makes the system deadlock free.

### 33.8. Computer Organization and Architecture — GATE CSE 2023, Question 32

**First appearance:** GATE CSE 2023  
**Question type:** NAT  
**Marks:** 2  
**Source difficulty label:** unlabelled

A kilobyte        byte-addressable memory is realized using four         memory blocks. Two input address
  lines                 are connected to the chip select      port of these memory blocks through a decoder
  as shown in the figure. The remaining ten input address lines from            are connected to the address port of
  these blocks. The chip select     is active high.

  The input memory addresses                 in decimal, for the starting locations              of each block
  (indicated as             in the figure) are among the options given below. Which one of the following options
  is

  A.                                          B.                                               C.              D.

### 33.9. Programming and Data Structures — GATE CSE 2024, Set 2, Question 38

**First appearance:** GATE CSE 2024, Set 2  
**Question type:** MSQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

​Let   and             be two stacks.    has capacity of elements.     has capacity of                                 elements.     already has
    elements:                       , and    , whereas    is empty, as shown below.

 Only the following three operations are available:

      PushToS2: Pop the top element from S1 and push it on S2.
      PushToS1: Pop the top element from S2 and push it on S1.
      GenerateOutput: Pop the top element from S1 and output it to the user.

 Note that the pop operation is not allowed on an empty stack and the push operation is not allowed on a full stack.

 Which of the following output sequences can be generated by using the above operations?

 A.                                                                                         B.
 C.                                                                                         D.

### 33.10. Computer Networks — GATE CSE 2021, Set 1, Question 49

**First appearance:** GATE CSE 2021, Set 1  
**Question type:** NAT  
**Marks:** 2  
**Source difficulty label:** unlabelled

Consider the sliding window flow-control protocol operating between a sender and a receiver over a full-
duplex error-free link. Assume the following:

      The time taken for processing the data frame by the receiver is negligible.
      The time taken for processing the acknowledgement frame by the sender is negligible.
      The sender has infinite number of frames available for transmission.
      The size of the data frame is          bits and the size of the acknowledgement frame is                 bits.
      The link data rate in each direction is Mbps (            bits per second).
      One way propagation delay of the link is         milliseconds.

The minimum value of the sender's window size in terms of the number of frames, (rounded to the nearest integer)
needed to achieve a link utilization of  is_____________.


## Week 34 — 10 questions

**Subject omitted this week:** General Aptitude

### 34.1. Computer Networks — GATE CSE 2016, Set 1, Question 54

**First appearance:** GATE CSE 2016, Set 1  
**Question type:** NAT  
**Marks:** 2  
**Source difficulty label:** medium

For a host machine that uses the token bucket algorithm for congestion control, the token bucket has a
  capacity of               and the maximum output rate is                  per         . Tokens arrive at a rate
  to sustain output at a rate of               per         . The token bucket is currently full and the machine needs
  to send                  of data. The minimum time required to transmit the data is _____________              .

### 34.2. Compiler Design — GATE CSE 2020, Question 33

**First appearance:** GATE CSE 2020  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

Consider the productions              and           . Each of the five non-terminals                 and                            has
two attributes: is a synthesized attribute, and is an inherited attribute. Consider the following rules.

      Rule                                                                       and
      Rule                                             and

Which one of the following is TRUE?

 A. Both Rule   and Rule      are                           -                          B. Only Rule   is   -attributed.
    attributed.
 C. Only Rule is -attributed.                                                          D. Neither Rule     nor Rule       is   -
                                                                                          attributed.

### 34.3. Computer Organization and Architecture — GATE CSE 2020, Question 21

**First appearance:** GATE CSE 2020  
**Question type:** NAT  
**Marks:** 1  
**Source difficulty label:** unlabelled

A direct mapped cache memory of MB has a block size of           bytes. The cache has an access time of
ns and a hit rate of   . During a cache miss, it takes 0 ns to bring the first word of a block from the main
memory, while each subsequent word takes ns. The word size is         bits. The average memory access time in ns
(round off to decimal place) is______.

### 34.4. Operating Systems — GATE CSE 2016, Set 2, Question 48

**First appearance:** GATE CSE 2016, Set 2  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** medium

Consider the following two-process synchronization solution.

The shared variable turn is initialized to zero. Which one of the following is TRUE?

 A. This is a correct two- process synchronization solution.
 B. This solution violates mutual exclusion requirement.
 C. This solution violates progress requirement.
 D. This solution violates bounded wait requirement.

### 34.5. Engineering Mathematics — GATE CSE 2026, Set 1, Question 45

**First appearance:** GATE CSE 2026, Set 1  
**Question type:** MSQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

An undirected, unweighted, simple graph                                                  is said to be -colorable if there exists a function
                 such that for every                                                             .
  Which of the following statements about -colorable graphs is/are true?

  A. If is -colorable, then may contain cycles of odd length
  B. If is -colorable, then may contain cycles of even length
  C. An optimal algorithm for testing whether  is -colorable runs in time                                                    , if   is represented as an
     adjacency list
  D. An optimal algorithm for testing whether is -colorable runs in time                                                     , if   is represented as an
     adjacency list

### 34.6. Programming and Data Structures — GATE CSE 2017, Set 1, Question 35

**First appearance:** GATE CSE 2017, Set 1  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** medium

Consider the following two functions.
 void fun1(int n) {
   if(n == 0) return;
   printf("%d", n);
   fun2(n - 2);
   printf("%d", n);
 }
 void fun2(int n) {
   if(n == 0) return;
   printf("%d", n);
   fun1(++n);
   printf("%d", n);
 }

The output printed when                                   is called is

 A.
 B.
 C.
 D.

### 34.7. Algorithms — GATE CSE 2023, Question 19

**First appearance:** GATE CSE 2023  
**Question type:** MSQ  
**Marks:** 1  
**Source difficulty label:** unlabelled

Let   and be functions of natural numbers given by                                                             and               Which of the following
statements is/are

 A.                                                                                           B.
 C.                                                                                           D.

### 34.8. Digital Logic — GATE CSE 2025, Set 2, Question 40

**First appearance:** GATE CSE 2025, Set 2  
**Question type:** MSQ  
**Marks:** 2  
**Source difficulty label:** easy

Which of the following Boolean algebraic equation(s) is/are CORRECT?

 A.
 B.
 C.
 D.

### 34.9. Databases — GATE CSE 2026, Set 2, Question 32

**First appearance:** GATE CSE 2026, Set 2  
**Question type:** NAT  
**Marks:** 2  
**Source difficulty label:** unlabelled

In the context of schema normalization in relational DBMS, consider a set of functional dependencies. The
  set of all functional dependencies implied by      is called the closure of . To compute the closure of ,
  Armstrong's Axioms can be applied. Consider            , and    as sets of attributes over a relational schema. The
  three rules of Armstrong's Axioms are described as follows.
  Reflexivity: If      , then
  Augmentation: If           , then             for any Z
  Transitivity: If         and        , then

  The additional rule of Union is defined as follows.
  Union: If          and          , then
  It can be proved that the additional rule of Union is also implied by the three rules of Armstrong's Axioms. Listed
  below are four combinations of these three rules. Which one of these combinations is both necessary and sufficient
  for the proof?

  A. Reflexivity,              Augmentation,               and                                 B. Reflexivity and Augmentation
     Transitivity
  C. Transitivity                                                                             D. Augmentation and Transitivity

### 34.10. Theory of Computation — GATE CSE 2025, Set 2, Question 50

**First appearance:** GATE CSE 2025, Set 2  
**Question type:** NAT  
**Marks:** 2  
**Source difficulty label:** unlabelled

Let                                . For            , let                              be the product of symbols in                  modulo 7. We take
                        , where       is the null string.

  For example,                                                                               .

  Define                                                            .

  The number of states in a minimum state DFA for                                           is __________ . (Answer in integer)


## Week 35 — 10 questions

**Subject omitted this week:** Programming and Data Structures

### 35.1. Computer Organization and Architecture — GATE CSE 2023, Question 54

**First appearance:** GATE CSE 2023  
**Question type:** NAT  
**Marks:** 2  
**Source difficulty label:** unlabelled

A n -way set associative cache of size                                                                              is used in a system with   -bit
address. The address is sub-divided into                                                           and

The number of bits in the                              is ___________.

### 35.2. Operating Systems — GATE CSE 2023, Question 12

**First appearance:** GATE CSE 2023  
**Question type:** MSQ  
**Marks:** 1  
**Source difficulty label:** unlabelled

Which one or more of the following need to be saved on a context switch from one thread                                             of a process
  to another thread     of the same process?

  A. Page table base register                                                                B. Stack pointer
  C. Program counter                                                                         D. General purpose registers

### 35.3. General Aptitude — GATE CSE 2023, Question 10

**First appearance:** GATE CSE 2023  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

Which one of the options best describes the transformation of the -dimensional figure        to   , and then to
   , as shown?

  A.                               A clockwise rotation by       about an axis perpendicular to the plane of the figure
                                   A reflection along a horizontal line
  B.                               A counter clockwise rotation by       about an axis perpendicular to the plane of the figure
                                   A reflection along a horizontal line
  C.                               A clockwise rotation by       about an axis perpendicular to the plane of the figure
                                   A reflection along a vertical line
  D.                               A counter clockwise rotation by        about an axis perpendicular to the plane of the figure
                                   A reflection along a vertical line

### 35.4. Algorithms — GATE CSE 2021, Set 2, Question 39

**First appearance:** GATE CSE 2021, Set 2  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

For constants                           and                  , consider the following recurrence defined on the non-negative integers:

Which one of the following options is correct about the recurrence                                     ?

 A. If               is                    , then                is
 B. If               is                   , then                is

 C. If               is                           for some                      , then       is
 D. If               is                       , then       is

### 35.5. Digital Logic — GATE CSE 2023, Question 34

**First appearance:** GATE CSE 2023  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

A Boolean digital circuit is composed using two -input multiplexers                       and one -input
multiplexer      as shown in the figure.           are the inputs of the multiplexers            and could
be connected to either or    The select lines of the multiplexers are connected to Boolean variables
as shown.

Which one of the following set of values of                                                                             will realise the Boolean function

 A.                                                                                           B.
 C.                                                                                           D.

### 35.6. Theory of Computation — GATE CSE 2026, Set 2, Question 38

**First appearance:** GATE CSE 2026, Set 2  
**Question type:** MSQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

Let                                and let                                                             .
Which of the following constraints ensure(s) that the language                                               is context-free?

 A.                                          B.                and                        C.               and                  D.

### 35.7. Compiler Design — GATE CSE 2017, Set 2, Question 32

**First appearance:** GATE CSE 2017, Set 2  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

Consider the following expression grammar                                       :

Which of the following grammars is not left recursive, but is equivalent to                         ?

 A.                                            B.                                   C.                  D.

### 35.8. Computer Networks — GATE CSE 2024, Set 2, Question 18

**First appearance:** GATE CSE 2024, Set 2  
**Question type:** MSQ  
**Marks:** 1  
**Source difficulty label:** unlabelled

​Which of the following statements about                                               fragmentation is/are TRUE?

 A. The fragmentation of an     datagram is performed only at the source of the datagram
 B. The fragmentation of an      datagram is performed at any      router which finds that the size of the datagram
    to be transmitted exceeds the
 C. The reassembly of fragments is performed only at the destination of the datagram
 D. The reassembly of fragments is performed at all intermediate routers along the path from the source to the
    destination

### 35.9. Engineering Mathematics — GATE CSE 2026, Set 1, Question 3

**First appearance:** GATE CSE 2026, Set 1  
**Question type:** MSQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

For                 , the maximum multiplicity of any eigenvalue of an                                            matrix with elements from        is

  A.                                                    B.                                         C.                               D.

### 35.10. Databases — GATE CSE 2024, Set 1, Question 34

**First appearance:** GATE CSE 2024, Set 1  
**Question type:** MSQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

The symbol        indicates functional dependency in the context of a relational database. Which of the
following options is/are TRUE?

 A.                                       implies
 B.                                       implies
 C.                            and                    implies
 D.                   and                       implies


## Week 36 — 10 questions

**Subject omitted this week:** Computer Organization and Architecture

### 36.1. Computer Networks — GATE CSE 2018, Question 55

**First appearance:** GATE CSE 2018  
**Question type:** NAT  
**Marks:** 2  
**Source difficulty label:** unlabelled

Consider a simple communication system where multiple nodes are connected by a shared broadcast
  medium (like Ethernet or wireless). The nodes in the system use the following carrier-sense based medium
  access protocol. A node that receives a packet to transmit will carrier-sense the medium for units of time. If the
  node does not detect any other transmission, it starts transmitting its packet in the next time unit. If the node detects
  another transmission, it waits until this other transmission finishes, and then begins to carrier-sense for time units
  again. Once they start to transmit, nodes do not perform any collision detection and continue transmission even if a
  collision occurs. All transmissions last for    units of time. Assume that the transmission signal travels at the speed
  of     meters per unit time in the medium.

  Assume that the system has two nodes     and , located at a distance meters from each other.        start
  transmitting a packet at time after successfully completing its carrier-sense phase. Node has a packet to

  transmit at time                     and begins to carrier-sense the medium.

  The maximum distance (in meters, rounded to the closest integer) that allows                                            to successfully avoid a collision
  between its proposed transmission and ’s ongoing transmission is _______.

### 36.2. Engineering Mathematics — GATE CSE 2022, Question 40

**First appearance:** GATE CSE 2022  
**Question type:** MSQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

The following simple undirected graph is referred to as the Peterson graph.

  Which of the following statements is/are

  A. The chromatic number of the graph is
  B. The graph has a Hamiltonian path.
  C. The following graph is isomorphic to the Peterson graph.

  D. The size of the largest independent set of the given graph is                                           (A subset of vertices of a graph form an
     independent set if no two vertices of the subset are adjacent.)

### 36.3. Compiler Design — GATE CSE 2024, Set 2, Question 19

**First appearance:** GATE CSE 2024, Set 2  
**Question type:** MSQ  
**Marks:** 1  
**Source difficulty label:** unlabelled

​Which of the following statements is/are FALSE?

 A. An attribute grammar is a syntax-directed definition          in which the functions in the semantic rules have no
    side effects
 B. The attributes in a -attributed definition cannot always be evaluated in a depth-first order
 C. Synthesized attributes can be evaluated by a bottom-up parser as the input is parsed
 D. All -attributed definitions based on          grammar can be evaluated using a bottom-up parsing strategy

### 36.4. General Aptitude — GATE CSE 2026, Set 2, Question 7

**First appearance:** GATE CSE 2026, Set 2  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

Two tiles are missing in Panel . Which one of the options in Panel                                            is the appropriate choice for the
missing tiles?

 A.                                           B.                           C.   D.

### 36.5. Operating Systems — GATE CSE 2022, Question 28

**First appearance:** GATE CSE 2022  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

Which one of the following statements is

  A. The          performs an associative search in parallel on all its valid entries using page number of incoming
     virtual address.
  B. If the virtual address of a word given by     has a        hit, but the subsequent search for the word results in
     a cache miss, then the word will always be present in the main memory.
  C. The memory access time using a given inverted page table is always same for all incoming virtual addresses.
  D. In a system that uses hashed page tables, if two distinct virtual addresses       and    map to the same value
     while hashing, then the memory access time of these addresses will not be the same.

### 36.6. Theory of Computation — GATE CSE 2026, Set 1, Question 42

**First appearance:** GATE CSE 2026, Set 1  
**Question type:** MSQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

Consider the following context-free grammar                                   .

In the above grammar,                         is the start symbol,                      and           are terminal symbols, and        and    are non-terminal
symbols.

Let              be the language generated by the grammar                                      . For a string               , let      be the number of   's in
 and               be the number of 's in .

Which of the following statements is/are true?

 A. There is a string                              such that
 B. For every string
 C. There is a string                              such that
 D. For every string

### 36.7. Digital Logic — GATE CSE 2017, Set 1, Question 33

**First appearance:** GATE CSE 2017, Set 1  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** medium

Consider a combination of    and   flip-flops connected as shown below. The output of the     flip-flop is
connected to the input of the flip-flop and the output of the flip-flop is connected to the input of the
flip-flop.

Initially, both                 and            are set to           (before the        clock cycle). The outputs

 A.                  after the                cycle are            and after the       cycle are    respectively.
 B.                  after the                cycle are            and after the       cycle are    respectively.
 C.                  after the                cycle are            and after the       cycle are    respectively.
 D.                  after the                cycle are            and after the       cycle are    respectively.

### 36.8. Databases — GATE CSE 2024, Set 2, Question 16

**First appearance:** GATE CSE 2024, Set 2  
**Question type:** MSQ  
**Marks:** 1  
**Source difficulty label:** unlabelled

​Which of the following file organizations is/are                                        efficient for the scan operation in    ?

  A. Sorted                                                                                     B. Heap
  C. Unclustered tree index                                                                     D. Unclustered hash index

### 36.9. Algorithms — GATE CSE 2024, Set 1, Question 50

**First appearance:** GATE CSE 2024, Set 1  
**Question type:** NAT  
**Marks:** 2  
**Source difficulty label:** unlabelled

The number of edges present in the forest generated by the                                                       traversal of an undirected graph   with
    vertices is . The number of connected components in                                                     is __________.

### 36.10. Programming and Data Structures — GATE CSE 2021, Set 2, Question 35

**First appearance:** GATE CSE 2021, Set 2  
**Question type:** MSQ  
**Marks:** 2  
**Source difficulty label:** medium

Consider the following                               program:
      #include <stdio.h>
      #include <stdlib.h>
      struct Node{
            int value;
            struct Node *next;};
      int main( ) {
         struct Node *boxE, *head, *boxN; int index=0;
         boxE=head= (struct Node *) malloc(sizeof(struct Node));
         head → value = index;
         for (index =1; index<=3; index++){
            boxN = (struct Node *) malloc (sizeof(struct Node));
            boxE → next = boxN;
            boxN → value = index;
            boxE = boxN; }
      for (index=0; index<=3; index++) {
         printf(“Value at index %d is %d\n”, index, head → value);
         head = head → next;
         printf(“Value at index %d is %d\n”, index+1, head → value); } }

Which one of the following statements below is correct about the program?

 A. Upon execution, the program creates a linked-list of five nodes
 B. Upon execution, the program goes into an infinite loop
 C. It has a missing       which will be reported as an error by the compiler
 D. It dereferences an uninitialized pointer that may result in a run-time error


## Week 37 — 10 questions

**Subject omitted this week:** Theory of Computation

### 37.1. Programming and Data Structures — GATE CSE 2016, Set 1, Question 38

**First appearance:** GATE CSE 2016, Set 1  
**Question type:** NAT  
**Marks:** 1  
**Source difficulty label:** medium

Consider the weighted undirected graph with                                             vertices, where the weight of edge              is given by the
  entry     in the matrix .

      W=

  The largest possible integer value of , for which at least one shortest path between some pair of vertices will
  contain the edge with weight is ___________.

### 37.2. Compiler Design — GATE CSE 2022, Question 55

**First appearance:** GATE CSE 2022  
**Question type:** NAT  
**Marks:** 2  
**Source difficulty label:** unlabelled

Consider the following grammar along with translation rules.

Here     and    are operators and     is a token that represents an integer and      represents the corresponding
integer value. The set of non-terminals is               and a subscripted non-terminal indicates an instance of the
non-terminal.

Using this translation scheme, the computed value of                                                for root of the parse tree for the expression
                      is ________________.

### 37.3. Engineering Mathematics — GATE CSE 2016, Set 1, Question 26

**First appearance:** GATE CSE 2016, Set 1  
**Question type:** NAT  
**Marks:** 2  
**Source difficulty label:** medium

The coefficient of                   in                                                     is ___________.

### 37.4. Databases — GATE CSE 2026, Set 2, Question 10

**First appearance:** GATE CSE 2026, Set 2  
**Question type:** MCQ  
**Marks:** 1  
**Source difficulty label:** unlabelled

Consider concurrent execution of two transactions    and     in a DBMS, both of which access a data
object . For these two transactions to not conflict on , which one of the following statements must be
true?

 A. Both    and   only read                                                                     B.    reads and     writes
 C.    writes and     reads                                                                     D. Both    and  write

### 37.5. Digital Logic — GATE CSE 2026, Set 1, Question 26

**First appearance:** GATE CSE 2026, Set 1  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

Consider the real valued variables          and  represented using the IEEE      singleprecision floating-
  point format. The binary representations of   and in hexadecimal notation are as follows:

  Let                           .
  Which one of the following is the binary representation of                                                  , in hexadecimal notation?

  A.                                                B.                                              C.                                 D.

### 37.6. Computer Networks — GATE CSE 2024, Set 2, Question 28

**First appearance:** GATE CSE 2024, Set 2  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

Which one of the following                                  prefixes exactly represents the range of                 addresses           to
             ?

  A.                                                                                       B.
  C.                                                                                       D.

### 37.7. Computer Organization and Architecture — GATE CSE 2026, Set 2, Question 8

**First appearance:** GATE CSE 2026, Set 2  
**Question type:** MCQ  
**Marks:** 1  
**Source difficulty label:** unlabelled

Consider the following two statements about interrupt handling mechanisms in a CPU.

    : In non-vectored interrupt mechanism, it usually takes more time to start the Interrupt Service Routine (ISR)
  when compared to that in a vectored interrupt mechanism.

     : In daisy-chain interrupt mechanism, the CPU polls all the input devices individually to determine the source of
  the interrupt.

  Which one of the following options is correct with respect to                                      and

  A. Both     and                 are true                                                    B. Both      and   are false
  C.    is true and                is false                                                   D.    is false and   is true

### 37.8. General Aptitude — GATE CSE 2021, Set 2, Question 9

**First appearance:** GATE CSE 2021, Set 2  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

The number of units of a product sold in three different years and the respective net profits are presented in the
  figure above. The cost/unit in Year was ₹ , which was half the cost/unit in Year . The cost/unit in Year was
  one-third of the cost/unit in Year . Taxes were paid on the selling price at      ,    , and      respectively for
  the three years. Net profit is calculated as the difference between the selling price and the sum of cost and taxes
  paid in that year.
  The ratio of the selling price in Year                            to the selling price in Year   is _________.

  A.                                           B.                                            C.                    D.

### 37.9. Operating Systems — GATE CSE 2022, Question 32

**First appearance:** GATE CSE 2022  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

Consider four processes               and scheduled on a          as per round robin algorithm with a time
  quantum of           The processes arrive in the order               all at time         There is exactly one
  context switch from to       exactly one context switch from to     and exactly two context switches from      to
  There is no context switch from to      Switching to a ready process after the termination of another process is also
  considered a context switch. Which one of the following is         possible as         burst time                  of
  these processes?

  A.                                                                                                 B.
  C.                                                                                                 D.

### 37.10. Algorithms — GATE CSE 2016, Set 2, Question 38

**First appearance:** GATE CSE 2016, Set 2  
**Question type:** NAT  
**Marks:** 2  
**Source difficulty label:** medium

Let               and    be four matrices of dimensions                                                                       and     , respectively.
  The minimum number of scalar multiplications required to find the product                                                            using the basic
  matrix multiplication method is _________.


## Week 38 — 10 questions

**Subject omitted this week:** Theory of Computation

### 38.1. Databases — GATE CSE 2019, Question 11

**First appearance:** GATE CSE 2019  
**Question type:** MCQ  
**Marks:** 1  
**Source difficulty label:** unlabelled

Consider the following two statements about database transaction schedules:

  I. Strict two-phase locking protocol generates conflict serializable schedules that are also recoverable.
 II. Timestamp-ordering concurrency control protocol with Thomas’ Write Rule can generate                                           view
     serializable schedules that are not conflict serializable

Which of the above statements is/are TRUE?

 A. I only                                   B. II only                 C. Both I and II            D. Neither I nor II

### 38.2. Computer Networks — GATE CSE 2025, Set 2, Question 26

**First appearance:** GATE CSE 2025, Set 2  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

Suppose we are transmitting frames between two nodes using Stop-and-Wait protocol. The frame size is
         bits. The transmission rate of the channel is           bps (bits/second) and the propagation delay
  between the two nodes is        milliseconds. Assume that the processing times at the source and destination are
  negligible. Also, assume that the size of the acknowledgement packet is negligible. Which ONE of the following
  most accurately gives the channel utilization for the above scenario in percentage?

  A.                                             B.                                              C.                  D.

### 38.3. Computer Organization and Architecture — GATE CSE 2021, Set 2, Question 19

**First appearance:** GATE CSE 2021, Set 2  
**Question type:** NAT  
**Marks:** 1  
**Source difficulty label:** unlabelled

Consider a set-associative cache of size                            bytes with cache block size of      bytes.
Assume that the cache is byte-addressable and a            -bit address is used for accessing the cache. If the
width of the tag field is bits, the associativity of the cache is _________

### 38.4. Engineering Mathematics — GATE CSE 2024, Set 1, Question 42

**First appearance:** GATE CSE 2024, Set 1  
**Question type:** MSQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

Consider the operators and      defined by                                                                               , for positive integers. Which of the
  following statements is/are TRUE?

  A. Operator              obeys the associative law
  B. Operator               obeys the associative law
  C. Operator              over the operator obeys the distributive law
  D. Operator               over the operator obeys the distributive law

### 38.5. Algorithms — GATE CSE 2026, Set 2, Question 27

**First appearance:** GATE CSE 2026, Set 2  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

Let be a weighted directed acyclic graph with        edges and vertices. Given       and a source vertex in
    , which one of the following options gives the worst case time complexity of the fastest algorithm to find the
  lengths of shortest paths from to all vertices that are reachable from in

  A.                                                                                               B.

  C.                                                                                                   D.

### 38.6. Operating Systems — GATE CSE 2021, Set 2, Question 14

**First appearance:** GATE CSE 2021, Set 2  
**Question type:** MSQ  
**Marks:** 1  
**Source difficulty label:** unlabelled

Which of the following statement(s) is/are correct in the context of                                            scheduling?

 A. Turnaround time includes waiting time
 B. The goal is to only maximize       utilization and minimize throughput
 C. Round-robin policy can be used even when the             time required by each of the processes is not known
    apriori
 D. Implementing preemptive scheduling needs hardware support

### 38.7. Digital Logic — GATE CSE 2025, Set 2, Question 39

**First appearance:** GATE CSE 2025, Set 2  
**Question type:** MSQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

Three floating point numbers         , and   are stored in three registers                                                             , and       , respectively in
            single precision format as given below in hexadecimal:

  Which of the following option(s) is/are CORRECT?

  A.                                                B.                                              C.                                 D.

### 38.8. Programming and Data Structures — GATE CSE 2016, Set 2, Question 15

**First appearance:** GATE CSE 2016, Set 2  
**Question type:** MCQ  
**Marks:** 1  
**Source difficulty label:** medium

items are stored in a sorted doubly linked list. For a delete operation, a pointer is provided to the record to
be deleted. For a decrease-key operation, a pointer is provided to the record on which the operation is to be
performed.
An algorithm performs the following operations on the list in this order:      delete,                         insert,
find, and      decrease-key. What is the time complexity of all these operations put together?

 A.                                             B.                                               C.       D.

### 38.9. Compiler Design — GATE CSE 2016, Set 2, Question 45

**First appearance:** GATE CSE 2016, Set 2  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** easy

Which one of the following grammars is free from left recursion?

 A.                                            B.                       C.                              D.

### 38.10. General Aptitude — GATE CSE 2016, Set 2, Question 09

**First appearance:** GATE CSE 2016, Set 2  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** medium

In a                rectangle grid shown below, each cell is rectangle. How many rectangles can be observed in the
  grid?

  A.                                               B.                                C.                        D.


## Week 39 — 10 questions

**Subject omitted this week:** Programming and Data Structures

### 39.1. Compiler Design — GATE CSE 2016, Set 2, Question 19

**First appearance:** GATE CSE 2016, Set 2  
**Question type:** MCQ  
**Marks:** 1  
**Source difficulty label:** easy

Match the following:

 A.                                                                                             B.
 C.                                                                                             D.

### 39.2. Theory of Computation — GATE CSE 2017, Set 2, Question 04

**First appearance:** GATE CSE 2017, Set 2  
**Question type:** MCQ  
**Marks:** 1  
**Source difficulty label:** unlabelled

Let       be any two context-free languages and                                                 be any regular language. Then which of the following
is/are CORRECT?

  I.                  is context-free
 II.        is context-free
III.             is context-free
IV.               is context-free

 A. I, II and IV only                        B. I and III only                                 C. II and IV only                      D. I only

### 39.3. Algorithms — GATE CSE 2019, Question 26

**First appearance:** GATE CSE 2019  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

Consider the following C function.
       void convert (int n ) {
            if (n<0)
                printf{“%d”, n);
            else {
                convert(n/2);
                printf(“%d”, n%2);
            }
       }

Which one of the following will happen when the function convert is called with any positive integer                            as argument?

 A. It will print the binary representation of and terminate
 B. It will print the binary representation of in the reverse order and terminate
 C. It will print the binary representation of but will not terminate
 D. It will not print anything and will not terminate

### 39.4. Engineering Mathematics — GATE CSE 2016, Set 2, Question 06

**First appearance:** GATE CSE 2016, Set 2  
**Question type:** NAT  
**Marks:** 1  
**Source difficulty label:** medium

Suppose that the eigenvalues of matrix                                  are        . The determinant of             is _________.

### 39.5. Databases — GATE CSE 2024, Set 2, Question 17

**First appearance:** GATE CSE 2024, Set 2  
**Question type:** MSQ  
**Marks:** 1  
**Source difficulty label:** unlabelled

​Which of the following statements about the Two Phase Locking (                                                     ) protocol is/are TRUE?

  A.      permits only serializable schedules
  B. With      , a transaction always locks the data item being read or written just before every operation and always
     releases the lock just after the operation
  C. With      , once a lock is released on any data item inside a transaction, no more locks on any data item can be
     obtained inside that transaction
  D. A deadlock is possible with

### 39.6. Computer Organization and Architecture — GATE CSE 2026, Set 1, Question 4

**First appearance:** GATE CSE 2026, Set 1  
**Question type:** NAT  
**Marks:** 1  
**Source difficulty label:** unlabelled

Match each addressing mode in                                        with a data element or an element of a data structure (in a high-level
language) in       :

  A.
  B.
  C.
  D.

### 39.7. General Aptitude — GATE CSE 2017, Set 2, Question 9

**First appearance:** GATE CSE 2017, Set 2  
**Question type:** MCQ  
**Marks:** 1  
**Source difficulty label:** medium

The number of roots of                                                             in the range                   is

  A.                                                B.                                             C.                             D.

### 39.8. Computer Networks — GATE CSE 2017, Set 2, Question 34

**First appearance:** GATE CSE 2017, Set 2  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

Consider the binary code that consists of only four valid codewords as given below:

Let the minimum Hamming distance of the code and the maximum number of erroneous bits that can be
corrected by the code be . Then the values of and are

 A.              and                          B.                 and              C.          and              D.       and

### 39.9. Operating Systems — GATE CSE 2016, Set 2, Question 47

**First appearance:** GATE CSE 2016, Set 2  
**Question type:** NAT  
**Marks:** 2  
**Source difficulty label:** medium

Consider the following processes, with the arrival time and the length of the CPU burst given in milliseconds.
The scheduling algorithm used is preemptive shortest remaining-time first.

The average turn around time of these processes is ___________ milliseconds.

### 39.10. Digital Logic — GATE CSE 2024, Set 1, Question 37

**First appearance:** GATE CSE 2024, Set 1  
**Question type:** MSQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

Consider a Boolean expression given by                                                                              .
Which of the following statements is/are CORRECT?

 A.                                                                                              B.
 C.                       is independent of input                                                D.                     is independent of input


## Week 40 — 10 questions

**Subject omitted this week:** Compiler Design

### 40.1. Digital Logic — GATE CSE 2016, Set 1, Question 8

**First appearance:** GATE CSE 2016, Set 1  
**Question type:** NAT  
**Marks:** 1  
**Source difficulty label:** medium

We want to design a synchronous counter that counts the sequence                             and then
repeats. The minimum number of   flip-flops required to implement this counter is _____________.

### 40.2. Computer Networks — GATE CSE 2021, Set 1, Question 44

**First appearance:** GATE CSE 2021, Set 1  
**Question type:** MSQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

A             server application is programmed to listen on port number                          on host     .A       client is connected
to the            server over the network.
Consider that while the                             connection was active, the server machine crashed and rebooted. Assume that the
client does not use the                             keepalive timer. Which of the following behaviors is/are possible?

 A. If the client was waiting to receive a packet, it may wait indefinitely
 B. The          server application on can listen on after reboot
 C. If the client sends a packet after the server reboot, it will receive a                            segment
 D. If the client sends a packet after the server reboot, it will receive a                           segment

### 40.3. Theory of Computation — GATE CSE 2024, Set 2, Question 52

**First appearance:** GATE CSE 2024, Set 2  
**Question type:** MSQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

L e t                be      the       language represented by the regular expression                          and
                                               , where   denotes the length of string . The number of strings in
which are also in                   is _________.

### 40.4. Operating Systems — GATE CSE 2016, Set 1, Question 48

**First appearance:** GATE CSE 2016, Set 1  
**Question type:** NAT  
**Marks:** 2  
**Source difficulty label:** medium

Cylinder a disk queue with requests for      to blocks on cylinders                                                The C-
LOOK scheduling algorithm is used. The head is initially at cylinder number                      , moving towards larger
cylinder numbers on its servicing pass. The cylinders are numbered from to                        . The total head movement (in
number of cylinders) incurred while servicing these requests is__________.

### 40.5. General Aptitude — GATE CSE 2025, Set 1, Question 7

**First appearance:** GATE CSE 2025, Set 1  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

In the diagram, the lines QR and ST are parallel to each other. The shortest distance between these two
  lines is half the shortest distance between the point P and line QR. What is the ratio of the area of the
  triangle PST to the area of the trapezium SQRT?
  Note: The figure shown is representative.

  A.                                               B.                                      C.                    D.

### 40.6. Computer Organization and Architecture — GATE CSE 2025, Set 1, Question 17

**First appearance:** GATE CSE 2025, Set 1  
**Question type:** MSQ  
**Marks:** 1  
**Source difficulty label:** unlabelled

A partial data path of a processor is given in the figure, where         and      are -bit registers. Which
option(s) is/are CORRECT related to arithmetic operations using the data path as shown?

 A. The data path can implement arithmetic operations involving two registers.
 B. The data path can implement arithmetic operations involving one register and one immediate value.

  C. The data path can implement arithmetic operations involving two immediate values.
  D. The data path can only implement arithmetic operations involving one register and one immediate value.

### 40.7. Programming and Data Structures — GATE CSE 2025, Set 1, Question 51

**First appearance:** GATE CSE 2025, Set 1  
**Question type:** NAT  
**Marks:** 2  
**Source difficulty label:** unlabelled

#include <stdio.h>

       int foo(int S[],int size) {
          if(size == 0) return 0;
          if(size == 1) return 1;
          if(S[0] != S[1]) return 1+foo(S+1,size-1);
          return foo(S+1,size-1);
       }
       int main() {
          int A[]={0,1,2,2,2,0,0,1,1};
          printf("%d",foo(A,9));
          return 0;
       }

  The value printed by the given                          program is ___________. (Answer in integer)

### 40.8. Engineering Mathematics — GATE CSE 2016, Set 2, Question 02

**First appearance:** GATE CSE 2016, Set 2  
**Question type:** NAT  
**Marks:** 1  
**Source difficulty label:** medium

Let      be a polynomial and              be its derivative. If the degree of                                                        is   , then
the degree of                is __________.

### 40.9. Algorithms — GATE CSE 2020, Question 40

**First appearance:** GATE CSE 2020  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

Let                         be a directed, weighted graph with weight function                                             . For some function
                    , for each edge          , define        as                                                        .
Which one of the options completes the following sentence so that it is TRUE?
“The shortest paths in                   under           are shortest paths under                 too,_____________”.

 A. for every
 B. if and only if              is positive
 C. if and only if              is negative
 D. if and only if   is the distance from to                                          in the graph obtained by adding a new vertex   to   and edges of
    zero weight from to every vertex of

### 40.10. Databases — GATE CSE 2026, Set 1, Question 20

**First appearance:** GATE CSE 2026, Set 1  
**Question type:** MSQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

Let         and be the attributes of a relation in a relational schema. Let                                                                indicate functional
  dependency in the context of a relational database, where                   .

  Which of the following options is/are always true?

  A. If                                 and                           , then
  B. If                               , then                            or
  C. If                              and                          , then
  D. If                           , then


## Week 41 — 10 questions

**Subject omitted this week:** Digital Logic

### 41.1. Engineering Mathematics — GATE CSE 2021, Set 1, Question 35

**First appearance:** GATE CSE 2021, Set 1  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** hard

Consider the two statements.

                     There exist random variables and                                   such that
                     For all random variables   and

Which one of the following choices is correct?

 A. Both      and    are true                                                                     B.    is true, but    is false
 C.    is false, but   is true                                                                    D. Both     and      are false

### 41.2. Theory of Computation — GATE CSE 2017, Set 1, Question 34

**First appearance:** GATE CSE 2017, Set 1  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** medium

If     is a grammar with productions

where           is the start variable, then which one of the following strings is not generated by                           ?

 A.                                          B.                                   C.                              D.

### 41.3. Databases — GATE CSE 2016, Set 2, Question 21

**First appearance:** GATE CSE 2016, Set 2  
**Question type:** MCQ  
**Marks:** 1  
**Source difficulty label:** medium

B+ Trees are considered BALANCED because.

 A. The lengths of the paths from the root to all leaf nodes are all equal.
 B. The lengths of the paths from the root to all leaf nodes differ from each other by at most .
 C. The number of children of any two non-leaf sibling nodes differ by at most .
 D. The number of records in any two leaf nodes differ by at most .

### 41.4. Computer Networks — GATE CSE 2017, Set 2, Question 09

**First appearance:** GATE CSE 2017, Set 2  
**Question type:** MCQ  
**Marks:** 1  
**Source difficulty label:** unlabelled

Consider the following statements about the routing protocols. Routing Information Protocol (RIP) and Open
Shortest Path First (OSPF) in an     network.

  I. RIP uses distance vector routing
 II. RIP packets are sent using UDP
III. OSPF packets are sent using TCP
IV. OSPF operation is based on link-state routing

Which of the above statements are CORRECT?

 A. I and IV only                               B. I, II and III only          C. I, II and IV only               D. II, III and IV only

### 41.5. Operating Systems — GATE CSE 2022, Question 16

**First appearance:** GATE CSE 2022  
**Question type:** MSQ  
**Marks:** 1  
**Source difficulty label:** unlabelled

Which of the following statements is/are                                            with respect to deadlocks?

 A. Circular wait is a necessary condition for the formation of deadlock.
 B. In a system where each resource has more than one instance, a cycle in its wait-for graph indicates the
    presence of a deadlock.
 C. If the current allocation of resources to processes leads the system to unsafe state, then deadlock will
    necessarily occur.
 D. In the resource-allocation graph of a system, if every edge is an assignment edge, then the system is not in
    deadlock state.

### 41.6. Compiler Design — GATE CSE 2017, Set 2, Question 6

**First appearance:** GATE CSE 2017, Set 2  
**Question type:** MCQ  
**Marks:** 1  
**Source difficulty label:** unlabelled

Which of the following statements about parser is/are CORRECT?

  I.                      is more powerful than
 II.            is more powerful than
III.            is more powerful than

 A. I only                                     B. II only                          C. III only                 D. II and III only

### 41.7. Computer Organization and Architecture — GATE CSE 2024, Set 2, Question 47

**First appearance:** GATE CSE 2024, Set 2  
**Question type:** NAT  
**Marks:** 2  
**Source difficulty label:** unlabelled

A processor with      general purpose registers uses a -bit instruction format. The instruction format
consists of an opcode field, an addressing mode field, two register operand fields, and a -bit scalar field. If
  addressing modes are to be supported, the maximum number of unique opcodes possible for every addressing
mode is ___________.

### 41.8. Programming and Data Structures — GATE CSE 2018, Question 21

**First appearance:** GATE CSE 2018  
**Question type:** NAT  
**Marks:** 1  
**Source difficulty label:** unlabelled

Consider the following                    program:
 #include<stdio.h>

 int counter=0;

 int calc (int a, int b) {
       int c;
       counter++;
       if(b==3) return (a*a*a);
       else {
              c = calc(a, b/3);
              return (c*c*c);
       }
 }

 int main() {
      calc(4, 81);
      printf("%d", counter);
 }

The output of this program is ______.

### 41.9. Algorithms — GATE CSE 2024, Set 1, Question 35

**First appearance:** GATE CSE 2024, Set 1  
**Question type:** MSQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

Let be a directed graph and a depth first search                                                             spanning tree in that is rooted at a vertex .
Suppose is also a breadth first search       tree in                                                      , rooted at . Which of the following statements
is/are TRUE for every such graph and tree ?

 A. There are no back-edges in with respect to the tree
 B. There are no cross-edges in with respect to the tree
 C. There are no forward-edge in with respect to the tree
 D. The only edges in are the edges in

### 41.10. General Aptitude — GATE CSE 2016, Set 1, Question 08

**First appearance:** GATE CSE 2016, Set 1  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** medium

Consider the following statements relating to the level of poker play of four players                                                            .

   I.       always beats
  II.       always beats
 III.      loses to only sometimes.
 IV.        always loses to

  Which of the following can be logically inferred from the above statements?

    i.      is likely to beat all the three other players
   ii.     is the absolute worst player in the set

  A. (i). only                                                                                        B. (ii) only
  C. (i) and (ii) only'                                                                               D. neither (i) nor (ii)


## Week 42 — 10 questions

**Subject omitted this week:** Theory of Computation

### 42.1. Engineering Mathematics — GATE CSE 2025, Set 2, Question 34

**First appearance:** GATE CSE 2025, Set 2  
**Question type:** MSQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

Consider a system of linear equations                                                 where                         and          . Suppose    has an LU
  decomposition,          , where

  Which of the following statement(s) is/are TRUE?

  A. The system              can be solved by first solving        and then                                                         .
  B. If is invertible, then both and are invertible.
  C. If is singular, then at least one of the diagonal elements of is zero.
  D. If is symmetric, then both and are symmetric.

### 42.2. Compiler Design — GATE CSE 2024, Set 1, Question 29

**First appearance:** GATE CSE 2024, Set 1  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

Consider the following pseudo-code.
      L 1: t1 = -1

       L 2: t2 = 0

       L 3: t3 = 0

       L 4: t4 = 4 * t3

       L 5: t5 = 4 * t2

       L 6: t6 = t5 * M

       L 7: t7 = t4 + t6

       L 8: t8 = a[t7]

       L 9: if t8 <= max goto L11

       L 10: t1 = t8

       L 11: t3 = t3 + 1

       L 12: if t3 < M goto L4

       L 13: t2 = t2 + 1

       L 14: if t2 < N goto L3

       L 15: max = t1

  Which one of the following options CORRECTLY specifies the number of basic blocks and the number of
  instructions in the largest basic block, respectively?

  A.      and                                 B.      and                          C.   and       D.   and

### 42.3. Algorithms — GATE CSE 2021, Set 2, Question 23

**First appearance:** GATE CSE 2021, Set 2  
**Question type:** MSQ  
**Marks:** 1  
**Source difficulty label:** unlabelled

Consider the following                                    function:
      int SomeFunction (int x, int y)
      {
         if ((x==1) || (y==1)) return 1;
         if (x==y) return x;
         if (x > y) return SomeFunction(x-y, y);
         if (y > x) return SomeFunction (x, y-x);

      }

The value returned by                                                                   is __________

### 42.4. Computer Organization and Architecture — GATE CSE 2026, Set 1, Question 28

**First appearance:** GATE CSE 2026, Set 1  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

The size of the physical address space of a processor is       bytes. The capacity of a cache memory unit is
     bytes. The cache block size is    bytes. The cache memory unit can be built as a direct mapped cache
or as a -way set-associative cache, where               and               . Let the length of the TAG field be                                           bits
for the direct mapped cache, and    bits for the set-associative cache.

Which one of the following options is true?

 A.                                            B.                                              C.                           D.

### 42.5. Operating Systems — GATE CSE 2025, Set 2, Question 37

**First appearance:** GATE CSE 2025, Set 2  
**Question type:** MSQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

Consider a demand paging system with three frames, and the following page reference string: 1 2 3 4 5 4 1
  6 4 5 1 3 2 . The contents of the frames are as follows initially and after each reference (from left to right):

  The *-marked references cause page replacements.
  Which one or more of the following could be the page replacement policy/policies in use?

  A. Least Recently Used page replacement policy
  B. Least Frequently Used page replacement policy
  C. Most Frequently Used page replacement policy
  D. Optimal page replacement policy

### 42.6. Databases — GATE CSE 2021, Set 1, Question 23

**First appearance:** GATE CSE 2021, Set 1  
**Question type:** NAT  
**Marks:** 1  
**Source difficulty label:** unlabelled

A relation         in a relational database has   tuples. The attribute has integer values ranging from
  to , and the attribute has integer values ranging from to . Assume that the attributes and are
independently distributed.

The estimated number of tuples in the output of                                     is ____________.

### 42.7. Digital Logic — GATE CSE 2022, Question 30

**First appearance:** GATE CSE 2022  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

Consider a digital display system           shown in the figure that displays the contents of register      A
           code word is used to load a word in        either from or from           is a         word memory
  segment and is a          word register file. Based on the value of mode bit         selects an input word to load in
        and interface with the corresponding bits in the code word to choose the addressed word. Which one of the
  following represents the functionality of      and

  A.       is              multiplexer                            is              multiplexer      is    multiplexer
  B.       is                decoder                               is              decoder          is   encoder
  C.       is                decoder                               is              decoder          is   multiplexer
  D.       is              de-multiplexer                          is             de-multiplexer    is   multiplexer

### 42.8. General Aptitude — GATE CSE 2022, Question 4

**First appearance:** GATE CSE 2022  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

Given below are four statements.

      Statement                  All students are inquisitive.
      Statement                  Some students are inquisitive.
      Statement                  No student is inquisitive.
      Statement                  Some students are not inquisitive.

From the given four statements, find the two statements that                                                                               simultaneously, assuming
that there is at least one student in the class.

 A. Statement                and Statement                                                              B. Statement      and Statement
 C. Statement                and Statement                                                              D. Statement      and Statement

### 42.9. Computer Networks — GATE CSE 2016, Set 1, Question 25

**First appearance:** GATE CSE 2016, Set 1  
**Question type:** MCQ  
**Marks:** 1  
**Source difficulty label:** medium

Which of the following is/are example(s) of stateful application layer protocol?

   i.
  ii.
 iii.
 iv.

 A.        and         only                  B.          and            only                  C.        and   only   D.   only

### 42.10. Programming and Data Structures — GATE CSE 2024, Set 1, Question 33

**First appearance:** GATE CSE 2024, Set 1  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

Consider a binary min-heap containing     distinct elements. Let be the index (in the underlying array) of
the maximum element stored in the heap. The number of possible values of is

 A.                                               B.                                              C.                          D.


## Week 43 — 10 questions

**Subject omitted this week:** Programming and Data Structures

### 43.1. Databases — GATE CSE 2018, Question 41

**First appearance:** GATE CSE 2018  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** medium

Consider the relations          and                                            , where          is a primary key and        is a foreign key
referencing   . Consider the query

Let LOJ denote the natural left outer-join operation. Assume that and                                    contain no null values.
Which of the following is NOT equivalent to                                ?

 A.                                                                                      B.
 C.                                                                                      D.

### 43.2. Operating Systems — GATE CSE 2021, Set 1, Question 11

**First appearance:** GATE CSE 2021, Set 1  
**Question type:** MSQ  
**Marks:** 1  
**Source difficulty label:** unlabelled

In the context of operating systems, which of the following statements is/are correct with respect to paging?

 A. Paging helps solve the issue of external fragmentation
 B. Page size has no impact on internal fragmentation
 C. Paging incurs memory overheads
 D. Multi-level paging is necessary to support pages of different sizes

### 43.3. General Aptitude — GATE CSE 2024, Set 2, Question 9

**First appearance:** GATE CSE 2024, Set 2  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

A cube is to be cut into pieces of equal size and shape. Here, each cut should be straight and it should not
  stop till it reaches the other end of the cube.

  The minimum number of such cuts required is

  A.                                           B.                                   C.                      D.

### 43.4. Engineering Mathematics — GATE CSE 2024, Set 1, Question 41

**First appearance:** GATE CSE 2024, Set 1  
**Question type:** MSQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

The chromatic number of a graph is the minimum number of colours used in a proper colouring of the graph.
Let be any graph with vertices and chromatic number . Which of the following statements is/are always
TRUE?

 A.       contains a complete subgraph with                                 vertices

  B.       contains an independent set of size at least
  C.       contains at least            edges
  D.       contains a vertex of degree at least

### 43.5. Digital Logic — GATE CSE 2024, Set 2, Question 39

**First appearance:** GATE CSE 2024, Set 2  
**Question type:** MSQ  
**Marks:** 2  
**Source difficulty label:** easy

Which of the following is/are EQUAL to                                        in radix -         (i.e., base - ) notation?

 A.        in radix -10                                                                              B.   in radix -8
 C.        in radix -16                                                                              D.   in radix -7

### 43.6. Algorithms — GATE CSE 2021, Set 2, Question 8

**First appearance:** GATE CSE 2021, Set 2  
**Question type:** MSQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

What is the worst-case number of arithmetic operations performed by recursive binary search on a sorted
  array of size ?

  A.                                                                                                  B.
  C.                                                                                                  D.

### 43.7. Computer Networks — GATE CSE 2024, Set 2, Question 45

**First appearance:** GATE CSE 2024, Set 2  
**Question type:** NAT  
**Marks:** 2  
**Source difficulty label:** unlabelled

Consider an Ethernet segment with a transmission speed of                  and a maximum segment length of
     meters. If the speed of propagation of the signal in the medium is                        , then the
minimum frame size (in bits) required for collision detection is ___________.

### 43.8. Theory of Computation — GATE CSE 2021, Set 2, Question 9

**First appearance:** GATE CSE 2021, Set 2  
**Question type:** MCQ  
**Marks:** 1  
**Source difficulty label:** unlabelled

Let               be an arbitrary regular language accepted by a minimal                                                            with states. Which one of
the following languages must necessarily be accepted by a minimal        with                                                       states?

 A.                                             B.                                                C.                                D.

### 43.9. Compiler Design — GATE CSE 2024, Set 1, Question 49

**First appearance:** GATE CSE 2024, Set 1  
**Question type:** NAT  
**Marks:** 2  
**Source difficulty label:** unlabelled

Let                    be a context-free grammar in Chomsky Normal Form with                                                                    and
containing   variable symbols including the start symbol . The string                                                             is derivable from .
The number of steps (application of rules) in the derivation     is __________.

### 43.10. Computer Organization and Architecture — GATE CSE 2020, Question 4

**First appearance:** GATE CSE 2020  
**Question type:** MCQ  
**Marks:** 1  
**Source difficulty label:** unlabelled

Consider the following data path diagram.

Consider an instruction:           . The following steps are used to execute it over the given data path.
Assume that PC is incremented appropriately. The subscripts   and    indicate read and write operations,
respectively.

 1.
 2.
 3.
 4.
 5.

Which one of the following is the correct order of execution of the above steps?

 A.                                           B.                                    C.   D.


## Week 44 — 10 questions

**Subject omitted this week:** Algorithms

### 44.1. Computer Organization and Architecture — GATE CSE 2022, Question 7

**First appearance:** GATE CSE 2022  
**Question type:** MCQ  
**Marks:** 1  
**Source difficulty label:** unlabelled

Which one of the following facilitates transfer of bulk data from hard disk to main memory with the highest
throughput?

 A.        based                      transfer                                              B. Interrupt driven      transfer
 C. Polling based                      transfer                                             D. Programmed         transfer

### 44.2. Digital Logic — GATE CSE 2020, Question 19

**First appearance:** GATE CSE 2020  
**Question type:** NAT  
**Marks:** 1  
**Source difficulty label:** unlabelled

A multiplexer is placed between a group of        registers and an accumulator to regulate data movement
such that at any given point in time the content of only one register will move to the accumulator. The
number of select lines needed for the multiplexer is ______.

### 44.3. Databases — GATE CSE 2017, Set 2, Question 19

**First appearance:** GATE CSE 2017, Set 2  
**Question type:** NAT  
**Marks:** 1  
**Source difficulty label:** medium

Consider the following tables                                 and

In table    P is the primary key and Q is the foreign key referencing R in table     with on-delete cascade and on-
update cascade. In table      R is the primary key and S is the foreign key referencing P in table    with on-delete
set NULL and on-update cascade. In order to delete record              from the table      the number of additional
records that need to be deleted from table     is _______

### 44.4. Programming and Data Structures — GATE CSE 2026, Set 2, Question 50

**First appearance:** GATE CSE 2026, Set 2  
**Question type:** NAT  
**Marks:** 2  
**Source difficulty label:** unlabelled

Consider the following ANSI-C program.
        #include <stdio.h>
        int main(){
           int *ptr, a, b, c;
           a=5; b=11; c=20;
           ptr=&a; *ptr=c; ptr=&c;
           a=*(&b); c=*ptr-a;
           printf("%d",c);
           return(0);
        }

  The output of this program is                                . (answer in integer)

  Note: Assume that the program compiles and runs successfully.

### 44.5. Theory of Computation — GATE CSE 2021, Set 2, Question 41

**First appearance:** GATE CSE 2021, Set 2  
**Question type:** MSQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

For a string           , we define                to be the reverse of          . For example, if                 then                  .

Which of the following languages is/are context-free?

 A.                                                                                              B.
 C.                                                                                              D.

### 44.6. Compiler Design — GATE CSE 2021, Set 2, Question 3

**First appearance:** GATE CSE 2021, Set 2  
**Question type:** MCQ  
**Marks:** 1  
**Source difficulty label:** unlabelled

Consider the following                                  program:
      int main () {
         Integer x;
         return 0;
      }

Which one of the following phases in a seven-phase                                           compiler will throw an error?

 A. Lexical analyzer                                                                           B. Syntax analyzer
 C. Semantic analyzer                                                                          D. Machine dependent optimizer

### 44.7. Computer Networks — GATE CSE 2019, Question 28

**First appearance:** GATE CSE 2019  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

Consider three machines M, N, and P with IP addresses                         , and
respectively. The subnet mask is set to            for all the three machines. Which one of the
following is true?

 A. M, N, and P all belong to the same                                                    B. Only M and N belong to the same
    subnet                                                                                   subnet
 C. Only N and P belong to the same                                                       D. M, N, and P belong to three
    subnet                                                                                   different subnets

### 44.8. Engineering Mathematics — GATE CSE 2017, Set 1, Question 30

**First appearance:** GATE CSE 2017, Set 1  
**Question type:** MSQ  
**Marks:** 2  
**Source difficulty label:** medium

Let and                be two vectors in    whose Euclidean norms satisfy                                                 . What is the value of    such
  that                        bisects the angle between and ?

  A.                                                   B.                                        C.                           D.

### 44.9. Operating Systems — GATE CSE 2026, Set 1, Question 54

**First appearance:** GATE CSE 2026, Set 1  
**Question type:** NAT  
**Marks:** 2  
**Source difficulty label:** unlabelled

Consider a CPU that has to execute two types of processes. The first type, Actuators (A), requires a CPU
burst of seconds. The second type, Controllers (C), requires a CPU burst of seconds. A new process of
type A arrives at time         ,          , and   (in seconds). Similarly, a new process of type C arrives at time
                    , and    (in seconds). The CPU scheduling policy is First Come First Serve (FCFS). The first
process of type A starts running at         seconds. The average waiting time (in seconds) for the   processes is
         . (rounded off to one decimal place)

### 44.10. General Aptitude — GATE CSE 2023, Question 6

**First appearance:** GATE CSE 2023  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

The country of Zombieland is in distress since more than        of its working population is suffering from
  serious health issues. Studies conducted by competent health experts concluded that a complete lack of
  physical exercise among its working population was one of the leading causes of their health issues. As one of the
  measures to address the problem, the Government of Zombieland has decided to provide monetary incentives to
  those who ride bicycles to work.

  Based only on the information provided above, which one of the following statements can be logically inferred with
  certainty?

  A. All the working population of Zombieland will henceforth ride bicycles to work.
  B. Riding bicycles will ensure that all of the working population of Zombieland is free of health issues.
  C. The health experts suggested to the Government of Zombieland to declare riding bicycles as mandatory.
  D. The Government of Zombieland believes that riding bicycles is a form of physical exercise.


## Week 45 — 10 questions

**Subject omitted this week:** Programming and Data Structures

### 45.1. Theory of Computation — GATE CSE 2020, Question 32

**First appearance:** GATE CSE 2020  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

Consider the following languages.

Which one of the following is TRUE?

 A.         is regular and     is context- free.
 B.         context- free but not regular and                           is context-free.

 C. Neither    nor     is context- free.
 D.    context- free but     is not context-free.

### 45.2. Operating Systems — GATE CSE 2023, Question 13

**First appearance:** GATE CSE 2023  
**Question type:** MSQ  
**Marks:** 1  
**Source difficulty label:** unlabelled

Which one or more of the following options guarantee that a computer system will transition from user mode
to kernel mode?

 A. Function Call                              B. malloc Call                                   C. Page Fault           D. System Call

### 45.3. Computer Networks — GATE CSE 2018, Question 25

**First appearance:** GATE CSE 2018  
**Question type:** NAT  
**Marks:** 1  
**Source difficulty label:** medium

Consider a long-lived       session with an end-to-end bandwidth of               bits-per-second The
  session starts with a sequence number of         . The minimum time (in seconds, rounded to the closet
  integer) before this sequence number can be used again is _________.

### 45.4. Databases — GATE CSE 2017, Set 2, Question 17

**First appearance:** GATE CSE 2017, Set 2  
**Question type:** MCQ  
**Marks:** 1  
**Source difficulty label:** medium

An ER model of a database consists of entity types     and . These are connected by a relationship
which does not have its own attribute. Under which one of the following conditions, can the relational table
for R be merged with that of A?

 A. Relationship                 is one-to-many and the participation of        in   is total
 B. Relationship                 is one-to-many and the participation of        in   is partial
 C. Relationship                 is many-to-one and the participation of        in   is total
 D. Relationship                 is many-to-one and the participation of        in   is partial

### 45.5. Algorithms — GATE CSE 2026, Set 2, Question 20

**First appearance:** GATE CSE 2026, Set 2  
**Question type:** NAT  
**Marks:** 1  
**Source difficulty label:** unlabelled

The       keys                                are inserted into a hash table using the hash function
                   . The collisions are resolved by chaining. After all the keys are inserted, the length of the
  longest chain is     . (answer in integer)

### 45.6. Compiler Design — GATE CSE 2025, Set 2, Question 41

**First appearance:** GATE CSE 2025, Set 2  
**Question type:** MSQ  
**Marks:** 2  
**Source difficulty label:** easy

Consider two grammars                              and             with the production rules given below:

where                                               are the terminals.
Which of the following option(s) is/are CORRECT?

 A.           is not                      and                is                                B.        is        and         is not

 C.         and         are not                                                                D.       and   are ambiguous.

### 45.7. General Aptitude — GATE CSE 2017, Set 1, Question 1

**First appearance:** GATE CSE 2017, Set 1  
**Question type:** MCQ  
**Marks:** 1  
**Source difficulty label:** medium

After Rajendra Chola returned from his voyage to Indonesia, he ________ to visit the temple in Thanjavur.

  A. was wishing                                  B. is wishing                               C. wished                     D. had wished

### 45.8. Engineering Mathematics — GATE CSE 2016, Set 2, Question 03

**First appearance:** GATE CSE 2016, Set 2  
**Question type:** NAT  
**Marks:** 1  
**Source difficulty label:** medium

The minimum number of colours that is sufficient to vertex-colour any planar graph is ________.

### 45.9. Digital Logic — GATE CSE 2021, Set 1, Question 24

**First appearance:** GATE CSE 2021, Set 1  
**Question type:** NAT  
**Marks:** 1  
**Source difficulty label:** unlabelled

Consider the following representation of a number in                                                              single-precision floating point format with a
bias of    .

Here                 and         denote the sign, exponent, and fraction components of the floating point representation.

The decimal value corresponding to the above representation (rounded to                                                            decimal places) is ____________.

### 45.10. Computer Organization and Architecture — GATE CSE 2026, Set 1, Question 49

**First appearance:** GATE CSE 2026, Set 1  
**Question type:** NAT  
**Marks:** 2  
**Source difficulty label:** unlabelled

Consider a hard disk with a rotational speed of              rpm. The time to move the read/write head from a
  track to its adjacent track is millisecond. Initially, the head is on track . The number of sectors per track is
       . The sector size is      bytes. It is necessary to transfer data from     randomly located sectors in each of the
  following tracks in the order:       and .

  The total time for the data transfer (in milliseconds) from the hard disk is                                                   . (rounded off to one decimal
  place)


## Week 46 — 10 questions

**Subject omitted this week:** Databases

### 46.1. Algorithms — GATE CSE 2024, Set 2, Question 49

**First appearance:** GATE CSE 2024, Set 2  
**Question type:** NAT  
**Marks:** 2  
**Source difficulty label:** unlabelled

The number of distinct minimum-weight spanning trees of the following graph is

### 46.2. Compiler Design — GATE CSE 2023, Question 50

**First appearance:** GATE CSE 2023  
**Question type:** NAT  
**Marks:** 2  
**Source difficulty label:** unlabelled

Consider the syntax directed translation given by the following grammar and semantic rules. Here
and are non-terminals.        is the starting non-terminal, and     and are lexical tokens corresponding to
input letters                          respectively.         denotes the synthesized attribute (a numeric value)
associated with a non-terminal              and      denote occurrences of    and     on the right hand side of a
production, respectively. For the tokens and                   and          .

The value computed by the translation scheme for the input string

is ____________. (Rounded off to three decimal places)

### 46.3. Computer Organization and Architecture — GATE CSE 2016, Set 2, Question 33

**First appearance:** GATE CSE 2016, Set 2  
**Question type:** NAT  
**Marks:** 2  
**Source difficulty label:** medium

Consider a                       (gigahertz) processor with a three stage pipeline and stage latencies                      and     such

that                                       . If the longest pipeline stage is split into two pipeline stages of equal latency , the
new frequency is __________                                   , ignoring delays in the pipeline registers.

### 46.4. Theory of Computation — GATE CSE 2016, Set 2, Question 18

**First appearance:** GATE CSE 2016, Set 2  
**Question type:** MCQ  
**Marks:** 1  
**Source difficulty label:** medium

Consider the following types of languages:   : Regular,                                            : Context-free,   : Recursive,   : Recursively
  enumerable. Which of the following is/are TRUE ?

      I.               is recursively enumerable.

 II.                  is recursive.
III.                  is context-free.
IV.                   is context-free.

 A. I only.                                  B. I and III only.                                C. I and IV only.                      D. I, II and III only.

### 46.5. Operating Systems — GATE CSE 2024, Set 2, Question 14

**First appearance:** GATE CSE 2024, Set 2  
**Question type:** MSQ  
**Marks:** 1  
**Source difficulty label:** unlabelled

Which of the following tasks is/are the responsibility/responsibilities of the memory management unit
        in a system with paging-based memory management?

 A. Allocate a new page table for a newly created process
 B. Translate a virtual address to a physical address using the page table
 C. Raise a trap when a virtual address is not found in the page table
 D. Raise a trap when a process tries to write to a page marked with read-only permission in the page table

### 46.6. Programming and Data Structures — GATE CSE 2017, Set 2, Question 37

**First appearance:** GATE CSE 2017, Set 2  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

Consider the C program fragment below which is meant to divide                                      by   using repeated subtractions. The
variables , , and are all unsigned int.
 while (r >= y) {
   r=r-y;
   q=q+1;
 }

  Which of the following conditions on the variables                                  and   before the execution of the fragment will ensure that
  the loop terminated in a state satisfying the condition                                           ?

  A.
  B.
  C.
  D.

### 46.7. Engineering Mathematics — GATE CSE 2018, Question 15

**First appearance:** GATE CSE 2018  
**Question type:** NAT  
**Marks:** 1  
**Source difficulty label:** medium

Two people,        and , decide to independently roll two identical dice, each with faces, numbered to .
The person with the lower number wins. In case of a tie, they roll the dice repeatedly until there is no tie.
Define a trial as a throw of the dice by     and      Assume that all numbers on each dice are equi-probable and
that all trials are independent. The probability (rounded to decimal places) that one of them wins on the third trial
is ____

### 46.8. General Aptitude — GATE CSE 2025, Set 2, Question 8

**First appearance:** GATE CSE 2025, Set 2  
**Question type:** NAT  
**Marks:** 2  
**Source difficulty label:** unlabelled

Which one of the following options is correct for the given data in the table?

  A.
  B.
  C.
  D.

### 46.9. Digital Logic — GATE CSE 2016, Set 1, Question 07

**First appearance:** GATE CSE 2016, Set 1  
**Question type:** NAT  
**Marks:** 1  
**Source difficulty label:** medium

The               complement representation of an integer is                                                                            its decimal
representation is ____________

### 46.10. Computer Networks — GATE CSE 2024, Set 1, Question 26

**First appearance:** GATE CSE 2024, Set 1  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

Consider a network path               between nodes and via router . Node sends a file of size
bytes to via this path by splitting the file into chunks of    bytes each. Node sends these chunks one
after the other without any wait time between the successive chunk transmissions. Assume that the size of extra
headers added to these chunks is negligible, and that the chunk size is less than the .
Each of the links      and        has a bandwidth of               , and negligible propagation latency. Router
  immediately transmits every packet it receives from     to , with negligible processing and queueing delays.
Router can simultaneously receive on               and transmit on             .
Assume               starts transmitting the chunks at time                    .
Which one of the following options gives the time (in seconds, rounded off to                                     decimal places) at which   receives
all the chunks of the file?

 A.                                             B.                             C.                                 D.


## Week 47 — 10 questions

**Subject omitted this week:** General Aptitude

### 47.1. Programming and Data Structures — GATE CSE 2025, Set 1, Question 53

**First appearance:** GATE CSE 2025, Set 1  
**Question type:** NAT  
**Marks:** 2  
**Source difficulty label:** unlabelled

Consider the following C program:
      #include <stdio.h>
      int gate (int n) {
      int d, t, newnum, turn;

      newnum = turn = 0; t=1;
      while (n>=t) t *= 10;
      t /=10;
      while (t>0) {
      d = n/t;
      n = n%t;
      t /= 10;
      if (turn) newnum = 10*newnum + d;
      turn = (turn + 1) % 2;
      }
      return newnum;
      }
      int main () {
      printf ("%d", gate(14362));
      return 0;
      }

The value printed by the given                       program is ________. (Answer in integer)

### 47.2. Computer Networks — GATE CSE 2026, Set 2, Question 33

**First appearance:** GATE CSE 2026, Set 2  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

Consider the transmission of data bits                 over a link that uses Cyclic Redundancy Check (CRC)
  code for error detection. If the generator bit pattern is given to be      , which one of the following options
  shows the remainder bit pattern appended to the data bits before transmission?

  A.                                         B.                                            C.                        D.

### 47.3. Computer Organization and Architecture — GATE CSE 2025, Set 2, Question 51

**First appearance:** GATE CSE 2025, Set 2  
**Question type:** NAT  
**Marks:** 2  
**Source difficulty label:** unlabelled

An application executes                number of instructions in        seconds. There are four types of
  instructions, the details of which are given in the table. The duration of a clock cycle in nanoseconds is
  __________. (rounded off to one decimal place)

### 47.4. Compiler Design — GATE CSE 2023, Question 27

**First appearance:** GATE CSE 2023  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

Consider the control flow graph shown.

  Which one of the following choices correctly lists the set of live variables at the exit point of each basic block?

  A.                                                                                              B.
  C.                                                                                              D.

### 47.5. Digital Logic — GATE CSE 2021, Set 1, Question 42

**First appearance:** GATE CSE 2021, Set 1  
**Question type:** MSQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

Consider the following Boolean expression.

Which of the following Boolean expressions is/are equivalent to                                           (complement of    )?

 A.
 B.
 C.
 D.

### 47.6. Databases — GATE CSE 2017, Set 1, Question 42

**First appearance:** GATE CSE 2017, Set 1  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** medium

In a database system, unique timestamps are assigned to each transaction using Lamport's logical clock.
  Let           and        be the timestamps of transactions      and    respectively. Besides,     holds a
  lock on the resource  and     has requested a conflicting lock on the same resource     The following algorithm is
  used to prevent deadlocks in the database system assuming that a killed transaction is restarted with the same
  timestamp.

                                              if                                       then

                                                             is killed

                                              else           waits.

  Assume any transaction that is not killed terminates eventually. Which of the following is TRUE about the database
  system that uses the above algorithm to prevent deadlocks?

  A. The database system is both deadlock-free and starvation-free.
  B. The database system is deadlock-free, but not starvation-free.
  C. The database system is starvation-free, but not deadlock-free.
  D. The database system is neither deadlock-free nor starvation-free.

### 47.7. Operating Systems — GATE CSE 2019, Question 41

**First appearance:** GATE CSE 2019  
**Question type:** NAT  
**Marks:** 2  
**Source difficulty label:** unlabelled

Consider the following four processes with arrival times (in milliseconds) and their length of CPU bursts (in
milliseconds) as shown below:

These processes are run on a single processor using preemptive Shortest Remaining Time First scheduling
algorithm. If the average waiting time of the processes is millisecond, then the value of is _____

### 47.8. Algorithms — GATE CSE 2018, Question 48

**First appearance:** GATE CSE 2018  
**Question type:** NAT  
**Marks:** 2  
**Source difficulty label:** unlabelled

Consider the weights and values of items listed below. Note that there is only one unit of each item.

The task is to pick a subset of these items such that their total weight is no more than   Kgs and their total value is
maximized. Moreover, no item may be split. The total value of items picked by an optimal algorithm is denoted by
    . A greedy algorithm sorts the items by their value-to-weight ratios in descending order and packs them
greedily, starting from the first item in the ordered list. The total value of items picked by the greedy algorithm is
denoted by           .

The value of                                     is ____

### 47.9. Engineering Mathematics — GATE CSE 2026, Set 2, Question 52

**First appearance:** GATE CSE 2026, Set 2  
**Question type:** MSQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

The determinant of a                                    matrix              is . The value of the determinant of                      is          . (answer in integer)

### 47.10. Theory of Computation — GATE CSE 2017, Set 2, Question 41

**First appearance:** GATE CSE 2017, Set 2  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

Let        be the language represented by regular expression . Let    be the language generated by a
context free grammar . Let            be the language accepted by a Turing machine    . Which of the
following decision problems are undecidable?

  I. Given a regular expression and a string , is           ?
 II. Given a context-free grammar , is
III. Given a context-free grammar , is            for some alphabet                                                 ?
IV. Given a Turing machine     and a string , is          ?

 A. I and IV only                            B. II and III only                            C. II, III and IV only               D. III and IV only


## Week 48 — 10 questions

**Subject omitted this week:** Theory of Computation

### 48.1. Compiler Design — GATE CSE 2022, Question 19

**First appearance:** GATE CSE 2022  
**Question type:** NAT  
**Marks:** 1  
**Source difficulty label:** unlabelled

Consider the augmented grammar with                                                           as the set of terminals.

If  is the set of two                                     items                                           , then                           contains exactly
______________ items.

### 48.2. Algorithms — GATE CSE 2016, Set 2, Question 11

**First appearance:** GATE CSE 2016, Set 2  
**Question type:** NAT  
**Marks:** 1  
**Source difficulty label:** medium

Breadth First Search (BFS) is started on a binary tree beginning from the root vertex. There is a vertex at a
distance four from the root. If is the   vertex in this BFS traversal, then the maximum possible value of
is __________

### 48.3. Databases — GATE CSE 2022, Question 29

**First appearance:** GATE CSE 2022  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

Let          and         denote read and write operations on a data element                                                             by a transaction
respectively. Consider the schedule with four transactions.

Which one of the following serial schedules is conflict equivalent to

 A.                                                                                                   B.
 C.                                                                                                   D.

### 48.4. Programming and Data Structures — GATE CSE 2022, Question 52

**First appearance:** GATE CSE 2022  
**Question type:** NAT  
**Marks:** 2  
**Source difficulty label:** unlabelled

Consider the queues     containing four elements and       containing none (shown as the              in the
figure). The only operations allowed on these two queues are                              and
The minimum number of               operations on      required to place the elements of    in    in reverse order
(shown as the             in the figure) without using any additional storage is________________.

### 48.5. General Aptitude — GATE CSE 2025, Set 1, Question 10

**First appearance:** GATE CSE 2025, Set 1  
**Question type:** NAT  
**Marks:** 2  
**Source difficulty label:** unlabelled

A shop has distinct flavors of ice-cream. One can purchase any number of scoops of any flavor. The order
  in which the scoops are purchased is inconsequential. If one wants to purchase scoops of ice-cream, in
  how many ways can one make that purchase?

  A.                                                B.                                             C.               D.

### 48.6. Digital Logic — GATE CSE 2020, Question 20

**First appearance:** GATE CSE 2020  
**Question type:** NAT  
**Marks:** 1  
**Source difficulty label:** unlabelled

If there are input lines and output lines for a decoder that is used to uniquely address a byte
  addressable KB RAM, then the minimum value of      is ________ .

### 48.7. Engineering Mathematics — GATE CSE 2022, Question 24

**First appearance:** GATE CSE 2022  
**Question type:** NAT  
**Marks:** 2  
**Source difficulty label:** unlabelled

The value of the following limit is ________________.

### 48.8. Operating Systems — GATE CSE 2024, Set 1, Question 14

**First appearance:** GATE CSE 2024, Set 1  
**Question type:** MSQ  
**Marks:** 1  
**Source difficulty label:** unlabelled

Which of the following statements about threads is/are TRUE?

 A. Threads can only be implemented in kernel space
 B. Each thread has its own file descriptor table for open files
 C. All the threads belonging to a process share a common stack
 D. Threads belonging to a process are by default not protected from each other

### 48.9. Computer Organization and Architecture — GATE CSE 2026, Set 2, Question 42

**First appearance:** GATE CSE 2026, Set 2  
**Question type:** MSQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

Consider a system with a processor and a KB direct mapped cache with block size of    bytes. The
  system has a    MB physical memory. Four words       , and S are accessed by the processor in the
  same    order     times. That is, there are a total of        memory references in the sequence

  Assume that the cache memory is initially empty. The physical addresses of the words are given below (                                word
      byte).

  Which of the following statements is/are true?
  Note:             and

  A. Every access to results in a cache miss
  B. Every access to results in a cache hit
  C. Every access to results in a cache miss
  D. Except the first access to , all subsequent accesses to                                              result in cache hits

### 48.10. Computer Networks — GATE CSE 2026, Set 1, Question 35

**First appearance:** GATE CSE 2026, Set 1  
**Question type:** NAT  
**Marks:** 2  
**Source difficulty label:** unlabelled

Consider the implementation of sliding window protocol over a lossless link, with a window size of
frames, where each frame is of size             bits (including header). The bandwidth of the link is
                        and the one-way propagation delay is      milliseconds. Assume that processing times at
the sender and receiver are zero and the transmission time of acknowledgements is also zero. Which one of the
following options gives the minimum size of  (in number of frames) required to achieve       link utilization?

 A.                                             B.                                              C.                D.


## Week 49 — 10 questions

**Subject omitted this week:** Databases

### 49.1. Compiler Design — GATE CSE 2019, Question 19

**First appearance:** GATE CSE 2019  
**Question type:** NAT  
**Marks:** 1  
**Source difficulty label:** unlabelled

Consider the grammar given below:

  Let                 and      be indexed as follows:

  Compute the FOLLOW set of the non-terminal B and write the index values for the symbols in the FOLLOW set in
  the descending order.(For example, if the FOLLOW set is          , then the answer should be      )

### 49.2. Computer Networks — GATE CSE 2022, Question 25

**First appearance:** GATE CSE 2022  
**Question type:** NAT  
**Marks:** 1  
**Source difficulty label:** unlabelled

Consider the resolution of the domain name                  by a        resolver. Assume that no resource
records are cached anywhere across the         servers and that iterative query mechanism is used in the
resolution. The number of          query-response pairs involved in completely resolving the domain name is
________________.

### 49.3. Digital Logic — GATE CSE 2022, Question 31

**First appearance:** GATE CSE 2022  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

Consider three floating point numbers     and stored in registers        and     respectively as per
            single precision floating point format. The          content stored in these registers
                        are as follows.

Which one of the following is

 A.                                            B.                                          C.                                        D.

### 49.4. Computer Organization and Architecture — GATE CSE 2016, Set 2, Question 32

**First appearance:** GATE CSE 2016, Set 2  
**Question type:** NAT  
**Marks:** 2  
**Source difficulty label:** medium

The width of the physical address on a machine is                                        bits. The width of the tag field in a     KB -way set
associative cache is ________ bits.

### 49.5. Engineering Mathematics — GATE CSE 2018, Question 17

**First appearance:** GATE CSE 2018  
**Question type:** NAT  
**Marks:** 1  
**Source difficulty label:** medium

Consider a matrix                                    where                                                  . Note that      denotes the transpose of . The

largest eigenvalue of                     is ____

### 49.6. Programming and Data Structures — GATE CSE 2020, Question 47

**First appearance:** GATE CSE 2020  
**Question type:** NAT  
**Marks:** 2  
**Source difficulty label:** unlabelled

Consider the array representation of a binary min-heap containing    elements. The minimum number of
comparisons required to find the maximum in the heap is ___________.

### 49.7. Operating Systems — GATE CSE 2024, Set 1, Question 44

**First appearance:** GATE CSE 2024, Set 1  
**Question type:** NAT  
**Marks:** 2  
**Source difficulty label:** unlabelled

Consider a     GB hard disk with     storage surfaces. There are          sectors per track and each sector
holds      bytes of data. The number of cylinders in the hard disk is _________.

### 49.8. General Aptitude — GATE CSE 2026, Set 2, Question 9

**First appearance:** GATE CSE 2026, Set 2  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

The figure in Panel below is a grid of cells with four rows and four columns. The numbers on the top and on
the left represent the number of cells that are to be shaded in that column and row, respectively. Which one
of the options shown in Panel below represents the grid shaded correctly?

 A.                                           B.                           C.   D.

### 49.9. Algorithms — GATE CSE 2018, Question 31

**First appearance:** GATE CSE 2018  
**Question type:** NAT  
**Marks:** 2  
**Source difficulty label:** unlabelled

Assume that multiplying a matrix   of dimension      with another matrix       of dimension     requires
       scalar multiplications. Computing the product of      matrices                     can be done by
  parenthesizing in different ways. Define          as an explicitly computed pair for a given paranthesization if
  they are directly multiplied. For example, in the matrix multiplication chain                            using
  parenthesization                                 and        are only explicitly computed pairs.
  Consider a matrix multiplication chain                  , where matrices                   and are of dimensions
                                    and             , respectively. In the parenthesization of                 that
  minimizes the total number of scalar multiplications, the explicitly computed pairs is/are

  A.             and               only                                                               B.         only
  C.             only                                                                                 D.         and    only

### 49.10. Theory of Computation — GATE CSE 2016, Set 1, Question 16

**First appearance:** GATE CSE 2016, Set 1  
**Question type:** MCQ  
**Marks:** 1  
**Source difficulty label:** medium

Which of the following languages is generated by the given grammar?

 A.
 B.
 C.

 D.


## Week 50 — 10 questions

**Subject omitted this week:** Digital Logic

### 50.1. Theory of Computation — GATE CSE 2022, Question 37

**First appearance:** GATE CSE 2022  
**Question type:** MSQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

Consider the following languages:

Note that              is the reversal of the string                         Which of the following is/are

 A.       and     are regular.                                                                     B.      and          are context-free.
 C.        is regular and      is context-                                                         D.      and          are context-free but not
      free.                                                                                             regular.

### 50.2. Engineering Mathematics — GATE CSE 2017, Set 2, Question 47

**First appearance:** GATE CSE 2017, Set 2  
**Question type:** NAT  
**Marks:** 2  
**Source difficulty label:** medium

If the ordinary generating function of a sequence                                                        is       ,       then             is equal to
  ___________ .

### 50.3. Programming and Data Structures — GATE CSE 2023, Question 25

**First appearance:** GATE CSE 2023  
**Question type:** NAT  
**Marks:** 1  
**Source difficulty label:** unlabelled

The integer value printed by the                                    program given below is _______________
       #include<stdio.h>

       int funcp(){
          static int x = 1;
          x++;
          return x;
       }

       int main(){
          int x,y;
          x = funcp();
          y = funcp()+x;
          printf("%d\n", (x+y));
          return 0;
       }

### 50.4. Databases — GATE CSE 2021, Set 1, Question 13

**First appearance:** GATE CSE 2021, Set 1  
**Question type:** MSQ  
**Marks:** 1  
**Source difficulty label:** unlabelled

Suppose a database system crashes again while recovering from a previous crash. Assume checkpointing
is not done by the database either during the transactions or during recovery.
Which of the following statements is/are correct?

 A. The same undo and redo list will be used while recovering again
 B. The system cannot recover any further
 C. All the transactions that are already undone and redone will not be recovered again
 D. The database will become inconsistent

### 50.5. Computer Networks — GATE CSE 2024, Set 1, Question 48

**First appearance:** GATE CSE 2024, Set 1  
**Question type:** NAT  
**Marks:** 2  
**Source difficulty label:** unlabelled

Consider the entries shown below in the forwarding table of an      router. Each entry consists of an prefix
and the corresponding next hop router for packets whose destination          address matches the prefix. The
notation " / " in a prefix indicates a subnet mask with the most significant bits set to .

This         router         forwards                 packets             each         to    hosts.     The     addresses of the hosts are
                                                                                     and             . The number of packets forwarded via the next
hop router              is __________.

### 50.6. General Aptitude — GATE CSE 2017, Set 1, Question 10

**First appearance:** GATE CSE 2017, Set 1  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** medium

A contour line joins locations having the same height above the mean sea level. The following is a contour
  plot of a geographical region. Contour lines are shown at    m intervals in this plot. If in a flood, the water
  level rises to    m, which of the villages              get submerged?

  A.                                            B.                                               C.                   D.

### 50.7. Algorithms — GATE CSE 2016, Set 1, Question 40

**First appearance:** GATE CSE 2016, Set 1  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** medium

is an undirected simple graph in which each edge has a distinct weight, and                            is a particular
edge of              . Which of the following statements about the minimum spanning trees                                        of    is/are
TRUE?

  I. If     is the lightest edge of some cycle in , then every MST of includes .
 II. If     is the heaviest edge of some cycle in , then every MST of excludes .

 A. I only.                                  B. II only.                                  C. Both I and II.   D. Neither I nor II.

### 50.8. Operating Systems — GATE CSE 2017, Set 1, Question 40

**First appearance:** GATE CSE 2017, Set 1  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** medium

Recall that Belady's anomaly is that the page-fault rate may increase as the number of allocated frames
increases. Now, consider the following statements:

        : Random page replacement algorithm (where a page chosen at random is replaced) suffers from Belady's
      anomaly.
        : LRU page replacement algorithm suffers from Belady's anomaly.

Which of the following is CORRECT?

 A.        is true,        is true                                                              B.         is true,    is false
 C.        is false,        is true                                                             D.         is false,    is false

### 50.9. Computer Organization and Architecture — GATE CSE 2017, Set 1, Question 50

**First appearance:** GATE CSE 2017, Set 1  
**Question type:** NAT  
**Marks:** 2  
**Source difficulty label:** medium

Instruction execution in a processor is divided into            stages, Instruction Fetch ( I F ) , Instruction
Decode (ID), Operand fetch (OF), Execute (EX), and Write Back (WB). These stages take 5, 4, 20, 10 and 3
nanoseconds (ns) respectively. A pipelined implementation of the processor requires buffering between each pair
of consecutive stages with a delay of 2 ns. Two pipelined implementation of the processor are contemplated:

  i. a naive pipeline implementation (NP) with stages and
 ii. an efficient pipeline (EP) where the OF stage is divided into stages                                      and     with execution times of 12
     ns and 8 ns respectively.

The speedup (correct to two decimal places) achieved by EP over NP in executing                                    independent instructions with
no hazards is _________ .

### 50.10. Compiler Design — GATE CSE 2021, Set 2, Question 30

**First appearance:** GATE CSE 2021, Set 2  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

​Consider the following                                  code segment:
         z=x + 3 + y->f1 + y->f2;
         for (i = 0; i < 200; i = i + 2)
         {
            if (z > i)
            {
                p = p + x + 3;
                q = q + y->f1;
            } else
            {

               p = p + y->f2;
               q = q + x + 3;
           }
       }

  Assume that the variable points to a        (allocated on the heap) containing two fields                                         and      , and the local
  variables               and are allotted registers. Common sub-expression elimination                                                    optimization is
  applied on the code. The number of addition and the dereference operations (of the form                                                or         ) in the
  optimized code, respectively, are:

  A.           and                               B.           and                      C.        and                 D.      and


## Week 51 — 10 questions

**Subject omitted this week:** Computer Networks

### 51.1. Databases — GATE CSE 2024, Set 1, Question 10

**First appearance:** GATE CSE 2024, Set 1  
**Question type:** MCQ  
**Marks:** 1  
**Source difficulty label:** unlabelled

Let be the specification: "Instructors teach courses. Students register for courses. Courses are allocated
classrooms. Instructors guide students." Which one of the following   diagrams CORRECTLY represents
  ?

 A.                                           B.         C.                          D.

### 51.2. Operating Systems — GATE CSE 2018, Question 53

**First appearance:** GATE CSE 2018  
**Question type:** NAT  
**Marks:** 2  
**Source difficulty label:** unlabelled

Consider a storage disk with       platters (numbered as            and ) ,       cylinders (numbered as
              ), and     sectors per track (numbered as              ). The following disk requests of the
form [sector number, cylinder number, platter number] are received by the disk controller at the same time:

Currently head is positioned at sector number         of cylinder , and is moving towards higher cylinder numbers.
The average power dissipation in moving the head over               cylinders is    milliwatts and for reversing the
direction of the head movement once is          milliwatts. Power dissipation associated with rotational latency and
switching of head between different platters is negligible.

The total power consumption in milliwatts to satisfy all of the above disk requests using the Shortest Seek Time
First disk scheduling algorithm is _____

### 51.3. Computer Organization and Architecture — GATE CSE 2025, Set 1, Question 43

**First appearance:** GATE CSE 2025, Set 1  
**Question type:** NAT  
**Marks:** 2  
**Source difficulty label:** unlabelled

A computer has a memory hierarchy consisting of two-level cache            and     and a main memory. If the
  processor needs to access data from memory, it first looks into          cache. If the data is not found in
  cache, it goes to     cache. If it fails to get the data from   cache, it goes to main memory, where the data is
  definitely available. Hit rates and access times of various memory units are shown in the figure. The average
  memory access time in nanoseconds            is ________. (rounded off to two decimal places)

### 51.4. General Aptitude — GATE CSE 2016, Set 2, Question 01

**First appearance:** GATE CSE 2016, Set 2  
**Question type:** MCQ  
**Marks:** 1  
**Source difficulty label:** medium

The man who is now Municipal Commissioner worked as ________________.

  A. the security guard at a university                                                      B. a security guard at the university
  C. a security guard at university                                                          D. the security guard at the university

### 51.5. Compiler Design — GATE CSE 2024, Set 2, Question 30

**First appearance:** GATE CSE 2024, Set 2  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

Consider the following context-free grammar where the start symbol is                                                   and the set of terminals is
             .

  The following is a partially-filled                                parsing table.

  Which one of the following options represents the CORRECT combination for the numbered cells in the parsing
  table?

  Note: In the options, "blank" denotes that the corresponding cell is empty.

  A.
  B.
  C.                                                                       blank           blank
  D.                                                                       blank           blank

### 51.6. Digital Logic — GATE CSE 2020, Question 29

**First appearance:** GATE CSE 2020  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

Consider three registers    ,   , and     that store numbers in        single precision floating point
format. Assume that         and     contain the values (in hexadecimal notation)                and
              respectively.
If                   , what is the value stored in                        ?

 A.                                            B.                                             C.                                           D.

### 51.7. Theory of Computation — GATE CSE 2023, Question 30

**First appearance:** GATE CSE 2023  
**Question type:** MSQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

Consider the pushdown automaton                  below, which runs on the input alphabet           , has stack
alphabet         , and has three states            , with being the start state. A transition from state to
state , labelled          , where is an input symbol or      is a stack symbol, and is a string of stack symbols,
represents the fact that in state , the        can read from the input, with     on the top of its stack, pop  from
the stack, push in the string on the stack, and go to state . In the initial configuration, the stack has only the
symbol in it. The             accepts by empty stack.

Which one of the following options correctly describes the language accepted by

A.                                 and
B.
C.                                 and
D.

### 51.8. Algorithms — GATE CSE 2024, Set 1, Question 31

**First appearance:** GATE CSE 2024, Set 1  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

An array                                                  is heapified. Which one of the following options
  represents the first three elements in the heapified array?

  A.                                             B.                                  C.                          D.

### 51.9. Programming and Data Structures — GATE CSE 2021, Set 1, Question 37

**First appearance:** GATE CSE 2021, Set 1  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

​Consider the following                                    program.
      #include <stdio.h>
      int main()
      {
         int i, j, count;
         count=0;
         i=0;
         for (j=-3; j<=3; j++)
         {
            if (( j >= 0) && (i++))
                count = count + j;
         }
         count = count +i;
         printf("%d", count);
         return 0;
      }

Which one of the following options is correct?

 A. The program will not compile successfully
 B. The program will compile successfully and output                                         when executed
 C. The program will compile successfully and output                                        when executed
 D. The program will compile successfully and output                                         when executed

### 51.10. Engineering Mathematics — GATE CSE 2016, Set 2, Question 01

**First appearance:** GATE CSE 2016, Set 2  
**Question type:** NAT  
**Marks:** 1  
**Source difficulty label:** medium

Consider the following expressions:

   i.
  ii.
 iii.
 iv.
  v.

The number of expressions given above that are logically implied by                                                       is ___________.


## Week 52 — 10 questions

**Subject omitted this week:** Compiler Design

### 52.1. General Aptitude — GATE CSE 2019, Question 9

**First appearance:** GATE CSE 2019  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

In a college, there are three student clubs,      students are only in the Drama club,       students are only in
the Dance club,        students are only in Maths club,      students are in both Drama and Dance clubs,
students are in both Dance and Maths clubs, students are in both Drama and Maths clubs, and students are in
all clubs. If     of the students in the college are not in any of these clubs, then the total number of students in the
college is _____.

 A.                                             B.                                       C.        D.

### 52.2. Engineering Mathematics — GATE CSE 2026, Set 2, Question 21

**First appearance:** GATE CSE 2026, Set 2  
**Question type:** MSQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

Consider the system of linear equations given below.

Suppose the values of and                                       are chosen such that the system of linear equations produce multiple solutions.
Then the product of and is                                         . (answer in integer)

### 52.3. Databases — GATE CSE 2024, Set 1, Question 25

**First appearance:** GATE CSE 2024, Set 1  
**Question type:** NAT  
**Marks:** 1  
**Source difficulty label:** unlabelled

Consider the following two relations,                                                and         :

The total number of tuples obtained by evaluating the following expression

                                          is ___________.

### 52.4. Theory of Computation — GATE CSE 2016, Set 2, Question 44

**First appearance:** GATE CSE 2016, Set 2  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

Consider the following languages.

                                                                                                        ,
                                                                                                       and
                                                      ,

where for each Turing machine                                       denotes a specific encoding of             . Which one of the following is TRUE?

A.          is recursive and                       are not recursive
B.          is recursive and                       are not recursive
C.               are recursive and                   is not recursive
D.                   are recursive

### 52.5. Algorithms — GATE CSE 2017, Set 1, Question 04

**First appearance:** GATE CSE 2017, Set 1  
**Question type:** MCQ  
**Marks:** 1  
**Source difficulty label:** medium

Consider the following functions from positive integers to real numbers:

  ,   , ,     ,   .
The CORRECT arrangement of the above functions in increasing order of asymptotic complexity is:

 A.             ,         ,       ,      ,                                               B.      ,   ,        ,     ,
 C.       ,         ,         ,          ,                                               D.      ,       ,    ,     ,

### 52.6. Computer Networks — GATE CSE 2020, Question 25

**First appearance:** GATE CSE 2020  
**Question type:** NAT  
**Marks:** 1  
**Source difficulty label:** unlabelled

Assume that you have made a request for a web page through your web browser to a web server. Initially
the browser cache is empty. Further, the browser is configured to send     requests in non-persistent
mode. The web page contains text and five very small images.The minimum number of      connections required
to display the web page completely in your browser is__________.

### 52.7. Computer Organization and Architecture — GATE CSE 2020, Question 30

**First appearance:** GATE CSE 2020  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

A computer system with a word length of   bits has a  MB byte- addressable main memory and a      KB,
 -way set associative cache memory with a block size of    bytes. Consider the following four physical
addresses represented in hexadecimal notation.

                                          ,
                                      ,
                                          ,

Which one of the following is TRUE?

A.          and           are mapped to different cache sets.
B.          and           are mapped to the same cache set.
C.          and           are mapped to the same cache set.
D.          and           are mapped to the same cache set.

### 52.8. Operating Systems — GATE CSE 2019, Question 42

**First appearance:** GATE CSE 2019  
**Question type:** NAT  
**Marks:** 2  
**Source difficulty label:** unlabelled

The index node (inode) of a Unix -like file system has    direct, one single-indirect and one double-indirect
pointers. The disk block size is kB, and the disk block address is -bits long. The maximum possible file
size is (rounded off to decimal place) ____ GB

### 52.9. Programming and Data Structures — GATE CSE 2017, Set 1, Question 08

**First appearance:** GATE CSE 2017, Set 1  
**Question type:** MCQ  
**Marks:** 1  
**Source difficulty label:** medium

Consider the C code fragment given below.
 typedef struct node {
    int data;
    node* next;
 } node;

 void join(node* m, node* n) {
   node* p = n;
   while(p->next != NULL) {
      p = p->next;
   }
   p->next = m;
 }

Assuming that m and n point to valid NULL-terminated linked lists, invocation of join will

 A. append list m to the end of list n for all inputs.
 B. either cause a null pointer dereference or append list m to the end of list n.
 C. cause a null pointer dereference for all inputs.
 D. append list n to the end of list m for all inputs.

### 52.10. Digital Logic — GATE CSE 2018, Question 22

**First appearance:** GATE CSE 2018  
**Question type:** NAT  
**Marks:** 1  
**Source difficulty label:** medium

Consider the sequential circuit shown in the figure, where both flip-flops used are positive edge-triggered
flip-flops.

The number of states in the state transition diagram of this circuit that have a transition back to the same state on
some value of "in" is ____


## Week 53 — 10 questions

**Subject omitted this week:** Computer Networks

### 53.1. Programming and Data Structures — GATE CSE 2016, Set 1, Question 34

**First appearance:** GATE CSE 2016, Set 1  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** medium

The following function computes the maximum value contained in an integer array                 of size   .

 int max (int *p,int n) {
    int a = 0, b=n-1;

     while (__________) {
        if (p[a]<= p[b]) {a = a+1;}
        else          {b = b-1;}
     }
     return p[a];
 }

The missing loop condition is:

 A.                                                                                    B.
 C.                                                                                    D.

### 53.2. Computer Organization and Architecture — GATE CSE 2022, Question 51

**First appearance:** GATE CSE 2022  
**Question type:** MSQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

A processor                    operating at
                                         has a standard       stage         instruction pipeline having a base
                                  of one without any pipeline hazards. For a given program         that has
  branch instructions, control hazards incur cycles stall for every branch. A new version of the processor
  operating at same clock frequency has an additional branch predictor unit            that completely eliminates stalls
  for correctly predicted branches. There is neither any savings nor any additional stalls for wrong predictions. There
  are no structural hazards and data hazards for        and      If the        has a prediction accuracy of         the
  speed up                                             obtained by     over      in executing is _______________.

### 53.3. General Aptitude — GATE CSE 2024, Set 1, Question 10

**First appearance:** GATE CSE 2024, Set 1  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

​The least number of squares to be added in the figure to make                              a line of symmetry is

  A.                                                 B.                       C.                           D.

### 53.4. Algorithms — GATE CSE 2023, Question 44

**First appearance:** GATE CSE 2023  
**Question type:** MSQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

Consider functions                                        and                           expressed in pseudocode as follows:

       Function_1
         while n>1 do
                                                                                                   Function_2
           for i=1 to n do
                                                                                                    for i = 1 to 100 * n do
              x = x + 1;
                                                                                                       x = x + 1;
           end for
                                                                                                    end for
           n = ⌊n/2⌋;
         end while

Let                  and        denote the number of times the statement                                                      is executed in              and
                       respectively.
Which of the following statements is/are

 A.                                                                                           B.
 C.                                                                                           D.

### 53.5. Digital Logic — GATE CSE 2017, Set 1, Question 21

**First appearance:** GATE CSE 2017, Set 1  
**Question type:** NAT  
**Marks:** 1  
**Source difficulty label:** medium

Consider the Karnaugh map given below, where                                               represents "don't care" and blank represents .

Assume for all inputs                                     , the respective complements             are also available. The above logic is
implemented using -input                                   gates only. The minimum number of gates required is ____________ .

### 53.6. Engineering Mathematics — GATE CSE 2018, Question 44

**First appearance:** GATE CSE 2018  
**Question type:** NAT  
**Marks:** 2  
**Source difficulty label:** unlabelled

Consider Guwahati,        and Delhi      whose temperatures can be classified as high         , medium
   and low   . Let           denote the probability that Guwahati has high temperature. Similarly,       and

         denotes the probability of Guwahati having medium and low temperatures respectively. Similarly, we use
                     and          for Delhi. The following table gives the conditional probabilities for Delhi’s
  temperature given Guwahati’s temperature.

  Consider the first row in the table above. The first entry denotes that if Guwahati has high temperature            then
  the probability of Delhi also having a high temperature      is       i.e.,                      . Similarly, the next
  two entries are                                                  . Similarly for the other rows.

  If it is known that                             , and                 , then the probability (correct to two decimal
  places) that Guwahati has high temperature given that Delhi has high temperature is ________.

### 53.7. Theory of Computation — GATE CSE 2023, Question 9

**First appearance:** GATE CSE 2023  
**Question type:** MCQ  
**Marks:** 1  
**Source difficulty label:** unlabelled

Consider the following definition of a lexical token                                                for an identifier in a programming language, using
extended regular expressions:

Which one of the following Non-deterministic Finite-state Automata with -transitions accepts the set of valid
identifiers? (A double-circle denotes a final state)

 A.                                                                                               B.

                                                                                                  D.
 C.

### 53.8. Compiler Design — GATE CSE 2025, Set 2, Question 30

**First appearance:** GATE CSE 2025, Set 2  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

​Given a Context-Free Grammar                                as follows:

  Which ONE of the following statements is TRUE?

  A.       is neither                            nor
  B.       is         , not
  C.       is
  D.       is           , also

### 53.9. Databases — GATE CSE 2021, Set 1, Question 32

**First appearance:** GATE CSE 2021, Set 1  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

Let       and         denote read and write operations respectively on a data item                                                        by a transaction    .
Consider the following two schedules.

Which one of the following options is correct?

 A.    is conflict serializable, and      is not conflict serializable
 B.    is not conflict serializable, and      is conflict serializable
 C. Both     and      are conflict serializable
 D. Niether     nor      is conflict serializable

### 53.10. Operating Systems — GATE CSE 2017, Set 2, Question 33

**First appearance:** GATE CSE 2017, Set 2  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** medium

A system shares tape drives. The current allocation and maximum requirement of tape drives for that
processes are shown below:

Which of the following best describes current state of the system?

 A. Safe, Deadlocked                                                                          B. Safe, Not Deadlocked
 C. Not Safe, Deadlocked                                                                      D. Not Safe, Not Deadlocked


## Week 54 — 10 questions

**Subject omitted this week:** Compiler Design

### 54.1. Digital Logic — GATE CSE 2023, Question 33

**First appearance:** GATE CSE 2023  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

Consider a sequential digital circuit consisting of                                            flip-flops and flip-flops as shown in the figure.
is the clock input to the circuit. At the beginning,                                                 and   have values     and respectively.

Which one of the given values of                                                             can         be obtained with this digital circuit?

 A.                                                B.                                              C.                       D.

### 54.2. General Aptitude — GATE CSE 2016, Set 2, Question 05

**First appearance:** GATE CSE 2016, Set 2  
**Question type:** MCQ  
**Marks:** 1  
**Source difficulty label:** medium

In a quadratic function, the value of the product of the roots                                               is . Find the value of

   A.                                              B.                                     C.                                 D.

### 54.3. Databases — GATE CSE 2019, Question 51

**First appearance:** GATE CSE 2019  
**Question type:** NAT  
**Marks:** 2  
**Source difficulty label:** unlabelled

A relational database contains two tables Student and Performance as shown below:

The primary key of the Student table is Roll_no. For the performance table, the columns Roll_no. and Subject_code
together form the primary key. Consider the SQL query given below:
      SELECT S.Student_name, sum(P.Marks)
      FROM Student S, Performance P
      WHERE P.Marks >84
      GROUP BY S.Student_name;

The number of rows returned by the above SQL query is ________

### 54.4. Engineering Mathematics — GATE CSE 2026, Set 1, Question 10

**First appearance:** GATE CSE 2026, Set 1  
**Question type:** MSQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

Let        . Consider an                                matrix            with its elements from      . Let the vector
be in the null space of  .

Which of the following options is/are always correct?

 A. Determinant of      is
 B. Determinant of      is
 C. Rank of    is
 D. There are at least two non-zero vectors in the null space of

### 54.5. Operating Systems — GATE CSE 2021, Set 1, Question 14

**First appearance:** GATE CSE 2021, Set 1  
**Question type:** MSQ  
**Marks:** 1  
**Source difficulty label:** unlabelled

Which of the following standard                             library functions will always invoke a system call when executed from a
  single-threaded process in a                                          operating system?

  A.                                             B.                                          C.                             D.

### 54.6. Computer Networks — GATE CSE 2021, Set 1, Question 45

**First appearance:** GATE CSE 2021, Set 1  
**Question type:** MSQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

Consider two hosts                      and      connected through a router . The maximum transfer unit                      value of the
link between and                        is        bytes, and between and is         bytes.
A              segment of size         bytes was transferred from    to     through , with     identification value as
                Assume that the       header size is    bytes. Further, the packet is allowed to be fragmented, i.e.,
                            flag in the    header is    set by .
Which of the following statements is/are correct?

 A. Two fragments are created at and the         datagram size carrying the second fragment is                                 bytes.
 B. If the second fragment is lost, will resend the fragment with the    identification value
 C. If the second fragment is lost, is required to resend the whole        segment.
 D.         destination port can be determined by analysing     the second fragment.

### 54.7. Algorithms — GATE CSE 2016, Set 1, Question 11

**First appearance:** GATE CSE 2016, Set 1  
**Question type:** MSQ  
**Marks:** 2  
**Source difficulty label:** medium

Consider the following directed graph:

  The number of different topological orderings of the vertices of the graph is _____________.

### 54.8. Programming and Data Structures — GATE CSE 2017, Set 1, Question 55

**First appearance:** GATE CSE 2017, Set 1  
**Question type:** NAT  
**Marks:** 2  
**Source difficulty label:** medium

The output of executing the following C program is _______________ .
 #include<stdio.h>

 int total(int v) {
    static int count = 0;
    while(v) {
       count += v&1;
       v >>= 1;
    }
    return count;
 }

 void main() {
   static int x=0;
   int i=5;
   for(; i>0; i--) {
      x = x + total(i);
   }
   printf("%d\n", x);
 }

### 54.9. Theory of Computation — GATE CSE 2016, Set 2, Question 42

**First appearance:** GATE CSE 2016, Set 2  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** medium

Consider the following two statements:

  I. If all states of an NFA are accepting states then the language accepted by the NFA is                            .
 II. There exists a regular language such that for all languages ,            is regular.

Which one of the following is CORRECT?

 A. Only I is true                                                                  B. Only II is true
 C. Both I and II are true                                                          D. Both I and II are false

### 54.10. Computer Organization and Architecture — GATE CSE 2017, Set 2, Question 45

**First appearance:** GATE CSE 2017, Set 2  
**Question type:** NAT  
**Marks:** 2  
**Source difficulty label:** unlabelled

The read access times and the hit ratios for different caches in a memory hierarchy are as given below:

The read access time of main memory in                        . Assume that the caches use the referred-word-first
read policy and the write-back policy. Assume that all the caches are direct mapped caches. Assume that the dirty
bit is always for all the blocks in the caches. In execution of a program,     of memory reads are for instruction
fetch and       are for memory operand fetch. The average read access time in nanoseconds (up to decimal
places) is _________


## Week 55 — 10 questions

**Subject omitted this week:** General Aptitude

### 55.1. Programming and Data Structures — GATE CSE 2016, Set 1, Question 41

**First appearance:** GATE CSE 2016, Set 1  
**Question type:** NAT  
**Marks:** 2  
**Source difficulty label:** hard

Let denote a queue containing sixteen numbers and be an empty stack.            returns the element
at the head of the queue without removing it from . Similarly    returns the element at the top of
without removing it from . Consider the algorithm given below.
 while Q is not Empty do
  if S is Empty OR Top(S) $\le$ Head (Q) then
     x:= Dequeue (Q);
     Push (S, x);
  else
     x:= Pop(S);
     Enqueue (Q, x);
  end
 end

The maximum possible number of iterations of the while loop in the algorithm is _______.

### 55.2. Computer Organization and Architecture — GATE CSE 2024, Set 2, Question 51

**First appearance:** GATE CSE 2024, Set 2  
**Question type:** NAT  
**Marks:** 2  
**Source difficulty label:** unlabelled

A processor uses a -bit instruction format and supports byte-addressable memory access. The           of the
processor has      distinct instructions. The instructions are equally divided into two types, namely -type
and -type, whose formats are shown below.

R - type Instruction Format:

I - type Instruction Format:

In the             ,    bit is used to distinguish between -type and -type instructions and the remaining bits
indicate the operation. The processor has      architectural registers, and all register fields in the instructions are of
equal size.

Let         be the number of bits used to encode the           field,   be the number of bits used to encode the
                  field, and be the number of bits used to encode the immediate value/address field. The value of
                is __________.

### 55.3. Computer Networks — GATE CSE 2025, Set 1, Question 30

**First appearance:** GATE CSE 2025, Set 1  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

A packet with the destination      address                                               arrives at a router whose routing table is shown.
Which interface will the packet be forwarded to?

 A.                                           B.                                    C.                         D.

### 55.4. Theory of Computation — GATE CSE 2024, Set 1, Question 13

**First appearance:** GATE CSE 2024, Set 1  
**Question type:** MSQ  
**Marks:** 1  
**Source difficulty label:** unlabelled

​Let                 be two regular languages and                                  a language which is not regular.
Which of the following statements is/are always TRUE?

 A.                       if      and           only           if                                      B.          is not regular

 C.        is not regular                                                                              D.          is regular

### 55.5. Digital Logic — GATE CSE 2021, Set 2, Question 52

**First appearance:** GATE CSE 2021, Set 2  
**Question type:** NAT  
**Marks:** 2  
**Source difficulty label:** unlabelled

Consider a Boolean function                                                such that

The number of literals in the minimal sum-of-products expression of                                                 is _________

### 55.6. Operating Systems — GATE CSE 2025, Set 2, Question 48

**First appearance:** GATE CSE 2025, Set 2  
**Question type:** NAT  
**Marks:** 2  
**Source difficulty label:** unlabelled

A computer system supports a logical address space of          bytes. It uses two-level hierarchical paging with
  a page size of         bytes. A logical address is divided into a -bit index to the outer page table, an offset
  within the page of the inner page table, and an offset within the desired page. Each entry of the inner page table
  uses eight bytes. All the pages in the system have the same size.

  The value of           is _________. (Answer in integer)

### 55.7. Algorithms — GATE CSE 2018, Question 43

**First appearance:** GATE CSE 2018  
**Question type:** NAT  
**Marks:** 2  
**Source difficulty label:** unlabelled

Let   be a graph with      vertices, with each vertex labelled by a distinct permutation of the numbers
                There is an edge between vertices and if and only if the label of can be obtained by
  swapping two adjacent numbers in the label of . Let denote the degree of a vertex in , and denote the
  number of connected components in . Then,               ______.

### 55.8. Engineering Mathematics — GATE CSE 2023, Question 39

**First appearance:** GATE CSE 2023  
**Question type:** MSQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

Let               be an onto (or surjective) function, where                                              and     are nonempty sets. Define an
equivalence relation on the set as

where                           . Let                                             be the set of all the equivalence classes under         . Define a new
mapping                            as

Which of the following statements is/are

 A.       is NOT well-defined.                                                              B.      is an onto (or surjective)
                                                                                               function.
 C.      is a one-to-one (or injective)                                                     D.    is a bijective function.
      function.

### 55.9. Compiler Design — GATE CSE 2016, Set 1, Question 36

**First appearance:** GATE CSE 2016, Set 1  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** medium

What will be the output of the following pseudo-code when parameters are passed by reference and
dynamic scoping is assumed?
 a = 3;
 void n(x) { x = x * a; print (x); }
 void m(y) { a = 1 ; a = y - a; n(a); print (a); }
 void main () { m(a); }

 A.                                            B.                                         C.                         D.

### 55.10. Databases — GATE CSE 2025, Set 1, Question 5

**First appearance:** GATE CSE 2025, Set 1  
**Question type:** MCQ  
**Marks:** 1  
**Source difficulty label:** unlabelled

A schedule of three database transactions                                             , and    is shown.              and         denote read and
write of data item       by transaction                                                 . The transaction          aborts at the end. Which other
transaction(s) will be required to be rolled back?

 A. Only                                      B. Only                                         C. Both        and       D. Neither      nor


## Week 56 — 10 questions

**Subject omitted this week:** Databases

### 56.1. General Aptitude — GATE CSE 2022, Question 7

**First appearance:** GATE CSE 2022  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

In a recently conducted national entrance test, boys constituted                                                of those who appeared for the test.
  Girls constituted the remaining candidates and they accounted for                                               of the qualified candidates.
  Which one of the following is the correct logical inference based on the information provided in the above passage?

  A. Equal number of boys and girls qualified
  B. Equal number of boys and girls appeared for the test
  C. The number of boys who appeared for the test is less than the number of girls who appeared
  D. The number of boys who qualified the test is less than the number of girls who qualified

### 56.2. Operating Systems — GATE CSE 2024, Set 2, Question 43

**First appearance:** GATE CSE 2024, Set 2  
**Question type:** NAT  
**Marks:** 2  
**Source difficulty label:** unlabelled

Consider a disk with the following specifications: rotation speed of         RPM, average seek time of
milliseconds,        sectors/track,      -byte sectors. A file has content stored in       sectors located
randomly on the disk. Assuming average rotational latency, the total time (in seconds, rounded off to    decimal
places) to read the entire file from the disk is

### 56.3. Programming and Data Structures — GATE CSE 2025, Set 2, Question 28

**First appearance:** GATE CSE 2025, Set 2  
**Question type:** MSQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

A meld operation on two instances of a data structure combines them into one single instance of the same
  data structure. Consider the following data structures:
  P. Unsorted doubly linked list with pointers to the head node and tail node of the list.
  Q. Min-heap implemented using an array.
  R. Binary Search Tree.
  Which ONE of the following options gives the worst-case time complexities for meld operation on instances of size
   of these data structures?

  A.
  B.       :
  C.       :
  D.       :

### 56.4. Engineering Mathematics — GATE CSE 2016, Set 2, Question 27

**First appearance:** GATE CSE 2016, Set 2  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** medium

Which one of the following well-formed formulae in predicate calculus is NOT valid ?

 A.
 B.
 C.
 D.

### 56.5. Digital Logic — GATE CSE 2017, Set 1, Question 9

**First appearance:** GATE CSE 2017, Set 1  
**Question type:** MCQ  
**Marks:** 1  
**Source difficulty label:** unlabelled

When two          numbers               and             in 's complement representation (with                                            and     as
the least significant bits) are added using a ripple-carry adder, the sum bits obtained are                                                  and the
carry bits are           . An overflow is said to have occurred if

 A. the carry bit                    is

 B. all the carry bits                                       are
 C.                                                           is

 D.                                                           is

### 56.6. Theory of Computation — GATE CSE 2022, Question 36

**First appearance:** GATE CSE 2022  
**Question type:** MSQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

Which of the following is/are undecidable?

  A. Given two Turing machines    and                                           decide if
  B. Given a Turing machine    decide if                                           is regular.
  C. Given a Turing machine    decide if                                       accepts all strings.
  D. Given a Turing machine    decide if                                       takes more than                       steps on every string.

### 56.7. Compiler Design — GATE CSE 2018, Question 37

**First appearance:** GATE CSE 2018  
**Question type:** MSQ  
**Marks:** 2  
**Source difficulty label:** medium

A lexical analyzer uses the following patterns to recognize three tokens                                  , and   over the alphabet
           .

  Note that ‘ ’ means or occurrence of the symbol                                  Note also that the analyzer outputs the token that matches
  the longest possible prefix.
  If the string                       is processed by the analyzer, which one of the following is the sequence of tokens it outputs?

  A.                                              B.                          C.                            D.

### 56.8. Algorithms — GATE CSE 2018, Question 47

**First appearance:** GATE CSE 2018  
**Question type:** NAT  
**Marks:** 2  
**Source difficulty label:** unlabelled

Consider the following undirected graph                                 :

Choose a value for that will maximize the number of minimum weight spanning trees (MWSTs) of                                   . The number
of MWSTs of for this value of is ____.

### 56.9. Computer Organization and Architecture — GATE CSE 2017, Set 1, Question 25

**First appearance:** GATE CSE 2017, Set 1  
**Question type:** NAT  
**Marks:** 1  
**Source difficulty label:** unlabelled

Consider a two-level cache hierarchy with         and     caches. An application incurs  memory accesses
per instruction on average. For this application, the miss rate of   cache is     ; the cache experiences,
on average,      misses per         instructions. The miss rate of     expressed correct to two decimal places is
________.

### 56.10. Computer Networks — GATE CSE 2018, Question 14

**First appearance:** GATE CSE 2018  
**Question type:** MCQ  
**Marks:** 1  
**Source difficulty label:** medium

Consider the following statements regarding the slow start phase of the TCP congestion control algorithm.
Note that cwnd stands for the TCP congestion window and MSS window denotes the Maximum Segments
Size:

   i. The cwnd increases by MSS on every successful acknowledgment
  ii. The cwnd approximately doubles on every successful acknowledgment
 iii. The cwnd increases by MSS every round trip time
 iv. The cwnd approximately doubles every round trip time

Which one of the following is correct?

 A. Only             and      are true                                                            B. Only   and   are true
 C. Only              is true                                                                     D. Only   and   are true


## Week 57 — 10 questions

**Subject omitted this week:** Computer Organization and Architecture

### 57.1. Engineering Mathematics — GATE CSE 2016, Set 2, Question 05

**First appearance:** GATE CSE 2016, Set 2  
**Question type:** NAT  
**Marks:** 1  
**Source difficulty label:** medium

Suppose that a shop has an equal number of LED bulbs of two different types. The probability of an LED
bulb lasting more than       hours given that it is of Type is   , and given that it is of Type is . The
probability that an LED bulb chosen uniformly at random lasts more than     hours is _________.

### 57.2. Operating Systems — GATE CSE 2024, Set 2, Question 36

**First appearance:** GATE CSE 2024, Set 2  
**Question type:** MSQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

Consider a multi-threaded program with two threads        and    . The threads share two semaphores:
(initialized to ) and   (initialized to ). The threads also share a global variable (initialized to ). The
threads execute the code shown below.
      //code of T 1
      wait (s1);
      x = x+1;
      print (x);
      wait (s2);
      signal(s1);

      // code of T2
      wait (s1);
      x= x+1;
      print (x) ;
      signal (s2);
      signal (s1);

Which of the following outcomes is/are possible when threads                                                 and    execute concurrently?

 A.         runs first and prints                          runs next and prints
 B.         runs first and prints                          runs next and prints
 C.         runs first and prints                          does not print anything (deadlock)
 D.         runs first and prints                          does not print anything (deadlock)

### 57.3. Algorithms — GATE CSE 2016, Set 1, Question 14

**First appearance:** GATE CSE 2016, Set 1  
**Question type:** MCQ  
**Marks:** 1  
**Source difficulty label:** medium

Let be a weighted connected undirected graph with distinct positive edge weights. If every edge weight is
increased by the same value, then which of the following statements is/are TRUE?

          : Minimum spanning tree of does not change.
          : Shortest path between any pair of vertices does not change.

 A.       only                               B.       only                                C. Neither    nor   D. Both     and

### 57.4. General Aptitude — GATE CSE 2022, Question 10

**First appearance:** GATE CSE 2022  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

A plot of land must be divided between four families. They want their individual plots to be similar in shape,
  not necessarily equal in area. The land has equally spaced poles, marked as dots in the below figure. Two
  ropes,      and     are already present and cannot be moved.
  What is the least number of               straight ropes needed to create the desired plots? A single rope can pass
  through three poles that are aligned in a straight line.

  A.                                                 B.                                          C.                          D.

### 57.5. Digital Logic — GATE CSE 2026, Set 2, Question 18

**First appearance:** GATE CSE 2026, Set 2  
**Question type:** MSQ  
**Marks:** 1  
**Source difficulty label:** unlabelled

In a system, numbers are represented using -bit two's complement form. Consider four numbers
                                    and            in the system. Which of the following operations
will result in arithmetic overflow?

 A.                                             B.                                           C.                              D.

### 57.6. Compiler Design — GATE CSE 2025, Set 1, Question 36

**First appearance:** GATE CSE 2025, Set 1  
**Question type:** MSQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

Which of the following statement(s) is/are TRUE while computing                                                and            during top down parsing
  by a compiler?

  A. For a production              will be added to          .
  B. If there is any input right end marker, it will be added to                                              , where     is the start symbol.
  C. For a production              will be added to Follow      .
  D. If there is any input right end marker, it will be added to                                                , where     is the start symbol.

### 57.7. Databases — GATE CSE 2021, Set 2, Question 32

**First appearance:** GATE CSE 2021, Set 2  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

Let be the following schedule of operations of three transactions                                                    ,    and         in a relational database
system:

Consider the statements                      and          below:

         : is conflict-serializable.
         : If  commits before        finishes, then                               is recoverable.

Which one of the following choices is correct?

 A. Both and are true                                                                                 B.   is true and is false
 C.   is false and is true                                                                            D. Both and are false

### 57.8. Computer Networks — GATE CSE 2026, Set 1, Question 46

**First appearance:** GATE CSE 2026, Set 1  
**Question type:** MSQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

An ISP having an address block                  assigns a block of      IP addresses to a client, using the
  classless internet domain routing (CIDR) super-netting approach. Which of the following address blocks can
  be assigned by the ISP?

  A.                                                                                                 B.
  C.                                                                                                 D.

### 57.9. Theory of Computation — GATE CSE 2018, Question 52

**First appearance:** GATE CSE 2018  
**Question type:** NAT  
**Marks:** 2  
**Source difficulty label:** unlabelled

Given a language                 , define             as follows:

The order of a language     is defined as the smallest                                            such that              . Consider the language         over
alphabet   accepted by the following automaton.

The order of               is ________.

### 57.10. Programming and Data Structures — GATE CSE 2016, Set 2, Question 37

**First appearance:** GATE CSE 2016, Set 2  
**Question type:** NAT  
**Marks:** 2  
**Source difficulty label:** medium

Consider the following program:
 int f (int * p, int n)
 {
    if (n <= 1) return 0;
    else return max (f (p+1, n-1), p[0] - p[1]);
 }
  int main ()
 {
    int a[] = {3, 5, 2, 6, 4};
    printf(" %d", f(a, 5));
 }

Note:                        returns the maximum of                      and .
The value printed by this program is ________.


## Week 58 — 10 questions

**Subject omitted this week:** Engineering Mathematics

### 58.1. Compiler Design — GATE CSE 2024, Set 1, Question 28

**First appearance:** GATE CSE 2024, Set 1  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

Consider the following grammar , with                                              as the start symbol. The grammar              has three incomplete
  productions denoted by      , and  .

  The set of terminals is                                           . The FIRST and FOLLOW sets of the different non-terminals are as follows.

  Which one of the following options CORRECTLY fills in the incomplete productions?

  A. (1)                      (2)                  (3)
  B. (1)                      (2)                  (3)
  C. (1)                      (2)                    (3)
  D. (1)                      (2)                    (3)

### 58.2. Computer Networks — GATE CSE 2020, Question 38

**First appearance:** GATE CSE 2020  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

An organization requires a range of IP address to assign one to each of its                computers. The
organization has approached an Internet Service Provider (ISP) for this task. The ISP uses CIDR and serves
the requests from the available IP address space                  . The ISP wants to assign an address space to
the organization which will minimize the number of routing entries in the ISP’s router using route aggregation.
Which of the following address spaces are potential candidates from which the ISP can allot any one of the
organization?

  I.
 II.
III.
IV.

 A.     and          only                   B.         and           only            C.        and   only        D.   and      only

### 58.3. Operating Systems — GATE CSE 2016, Set 1, Question 50

**First appearance:** GATE CSE 2016, Set 1  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** hard

Consider the following proposed solution for the critical section problem. There are       processes :
               . In the code, function returns an integer not smaller than any of its arguments .For all
     is initialized to zero.
Code for             ;
 do {
   c[i]=1; t[i]= pmax (t[0],....,t[n-1])+1; c[i]=0;
   for every j != i in {0,....,n-1} {
       while (c[j]);
       while (t[j] != 0 && t[j] <=t[i]);
   }
   Critical Section;
   t[i]=0;

    Remainder Section;

 } while (true);

Which of the following is TRUE about the above solution?

 A. At most one process can be in the critical section at any time
 B. The bounded wait condition is satisfied
 C. The progress condition is satisfied
 D. It cannot cause a deadlock

### 58.4. Databases — GATE CSE 2017, Set 1, Question 46

**First appearance:** GATE CSE 2017, Set 1  
**Question type:** NAT  
**Marks:** 2  
**Source difficulty label:** medium

Consider a database that has the relation schema CR(StudentName, CourseName). An instance of the
schema CR is as given below.

The following query is made on the database.

The number of rows in                        is ______________ .

### 58.5. Computer Organization and Architecture — GATE CSE 2024, Set 2, Question 1

**First appearance:** GATE CSE 2024, Set 2  
**Question type:** MCQ  
**Marks:** 1  
**Source difficulty label:** unlabelled

Consider a computer with a              processor. Its        controller can transfer bytes in cycle from a
device to main memory through cycle stealing at regular intervals. Which one of the following is the data
transfer rate (in bits per second) of the       controller if   of the processor cycles are used for    ?

  A.                                             B.                                      C.                     D.

### 58.6. Theory of Computation — GATE CSE 2017, Set 1, Question 22

**First appearance:** GATE CSE 2017, Set 1  
**Question type:** NAT  
**Marks:** 1  
**Source difficulty label:** unlabelled

Consider the language given by the regular expression               over the alphabet     . The
smallest number of states needed in a deterministic finite-state automaton (DFA) accepting     is
___________ .

### 58.7. General Aptitude — GATE CSE 2023, Question 7

**First appearance:** GATE CSE 2023  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

Consider two functions of time

where
Now consider the following two statements:

  i. For some                                            .
 ii. There exists a                 such that                        for all    .

Which one of the following options is

 A. only (i) is correct                                                             B. only (ii) is correct
 C. both (i) and (ii) are correct                                                   D. neither (i) nor (ii) is correct

### 58.8. Digital Logic — GATE CSE 2022, Question 8

**First appearance:** GATE CSE 2022  
**Question type:** MCQ  
**Marks:** 1  
**Source difficulty label:** unlabelled

Let             and     be two          registers that store numbers in    complement form. For the operation
                  which one of the following values of     and   gives an arithmetic overflow?

 A.                        and                                                                       B.            and
 C.                        and                                                                       D.            and

### 58.9. Algorithms — GATE CSE 2021, Set 2, Question 46

**First appearance:** GATE CSE 2021, Set 2  
**Question type:** MSQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

Consider the following directed graph:

Which of the following is/are correct about the graph?

 A. The graph does not have a topological order
 B. A depth-first traversal starting at vertex classifies three directed edges as back edges
 C. The graph does not have a strongly connected component
 D. For each pair of vertices and , there is a directed path from to

### 58.10. Programming and Data Structures — GATE CSE 2021, Set 2, Question 16

**First appearance:** GATE CSE 2021, Set 2  
**Question type:** NAT  
**Marks:** 1  
**Source difficulty label:** unlabelled

Consider a complete binary tree with nodes. Let            denote the set of first elements obtained by
performing Breadth-First Search          starting from the root. Let      denote the set of first elements
obtained by performing Depth-First Search         starting from the root.

The value of                            is _____________


## Week 59 — 10 questions

**Subject omitted this week:** Computer Organization and Architecture

### 59.1. Digital Logic — GATE CSE 2019, Question 50

**First appearance:** GATE CSE 2019  
**Question type:** NAT  
**Marks:** 2  
**Source difficulty label:** medium

What is the minimum number of -input NOR gates required to implement a -variable function expressed
in sum-of-minterms form as                                   Assume that all the inputs and their
complements are available. Answer: _______

### 59.2. Algorithms — GATE CSE 2026, Set 2, Question 39

**First appearance:** GATE CSE 2026, Set 2  
**Question type:** MSQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

Consider a binary search tree (BST) with leaf nodes              . Given any node , the key present in the
  node is denoted as         . All the keys present in the given BST are distinct. The keys belong to the set of
  real numbers.

  For a node , let                               denote the node that is its inorder successor. If a node                              does not have an inorder
  successor, then                                  is       . As there are no duplicates, if                                            is not        , then
                                           .

  Corresponding to every leaf node                                that has a non-NULL                       , a new key        with the following property is to
  be inserted into the BST.

  Let       represent the list of all such new keys to be inserted into the BST.
  Which of the following statements is/are true?

  A.    cannot have any duplicates
  B.    will have at least one element
  C. After inserting all keys from , the height of the BST can increase at most by one
  D. Number of nodes in the BST will double after inserting all keys from

### 59.3. General Aptitude — GATE CSE 2018, Question 8

**First appearance:** GATE CSE 2018  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

In a party,       of the invited guests are male and    are female.If      of the invited guests attended the
  party and if all the invited female guests attended, what would be the ratio of males to females among the
  attendees in the party?

  A.                                             B.                                           C.               D.

### 59.4. Programming and Data Structures — GATE CSE 2024, Set 1, Question 38

**First appearance:** GATE CSE 2024, Set 1  
**Question type:** MSQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

​Consider the following                      function definition.
      int f (int x, int y){
      for (int i=0 ; i<y ; i++ ) {
      x= x + x + y;
      }
      return x;
      }

Which of the following statements is/are TRUE about the above function?

 A. If the inputs are                                          , then the return value is greater than
 B. If the inputs are                                          , then the return value is greater than
 C. If the inputs are                                          , then the return value is less than
 D. If the inputs are                                          , then the return value is greater than

### 59.5. Computer Networks — GATE CSE 2023, Question 40

**First appearance:** GATE CSE 2023  
**Question type:** MSQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

Suppose you are asked to design a new reliable byte-stream transport protocol like         This protocol,
named         , runs over a             network with Round Trip Time of            milliseconds and the
maximum segment lifetime of minutes.
Which of the following is/are valid lengths of the Sequence Number field in the                                                              header?

    A.        bits                                   B.         bits                                 C.       bits               D.         bits

### 59.6. Theory of Computation — GATE CSE 2023, Question 53

**First appearance:** GATE CSE 2023  
**Question type:** MSQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

Consider the language                          over the alphabet                            , given below:

  The minimum number of states in a Deterministic Finite-State Automaton                                                                     for   is ____________.

### 59.7. Engineering Mathematics — GATE CSE 2017, Set 1, Question 47

**First appearance:** GATE CSE 2017, Set 1  
**Question type:** NAT  
**Marks:** 2  
**Source difficulty label:** medium

The number of integers between                                    and            (both inclusive) that are divisible by    or   or    is ____________
.

### 59.8. Databases — GATE CSE 2024, Set 2, Question 35

**First appearance:** GATE CSE 2024, Set 2  
**Question type:** NAT  
**Marks:** 2  
**Source difficulty label:** unlabelled

The relation schema, Person (pid, city), describes the city of residence for every person uniquely identified
by pid. The following relational algebra operators are available: selection, projection, cross product, and
rename.
To find the list of cities where at least persons reside, using the above operators, the minimum number of cross
product operations that must be used is

 A.                                              B.                                     C.               D.

### 59.9. Compiler Design — GATE CSE 2021, Set 1, Question 50

**First appearance:** GATE CSE 2021, Set 1  
**Question type:** MSQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

Consider the following                   code segment:
       a = b + c;
       e = a + 1;

       d = b + c;
       f = d + 1;
       g = e + f;

  In a compiler, this code segment is represented internally as a directed acyclic graph                                                 . The number of
  nodes in the        is _____________

### 59.10. Operating Systems — GATE CSE 2016, Set 1, Question 49

**First appearance:** GATE CSE 2016, Set 1  
**Question type:** NAT  
**Marks:** 2  
**Source difficulty label:** medium

Consider a computer system with ten physical page frames. The system is provided with an access
sequence                                    , where each         is a distinct virtual page number. The
difference in the number of page faults between the last-in-first-out page replacement policy and the optimal page
replacement policy is_________.


## Week 60 — 10 questions

**Subject omitted this week:** Operating Systems

### 60.1. General Aptitude — GATE CSE 2018, Question 6

**First appearance:** GATE CSE 2018  
**Question type:** NAT  
**Marks:** 2  
**Source difficulty label:** medium

In appreciation of the social improvements completed in a town, a wealthy philanthropist decided to gift
          to each male senior citizen in the town and        to each female senior citizen. Altogether, there

  were 300 senior citizens eligible for this gift. However, only                                    of the eligible men and       of the eligible women
  claimed the gift.How much money (in Rupees) did the philanthropist give away in total?

  A.                                                   B.                                C.                         D.

### 60.2. Algorithms — GATE CSE 2026, Set 1, Question 30

**First appearance:** GATE CSE 2026, Set 1  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

Let    be the set of all integers from to . Consider any order of insertion of the elements of                                                   into a
  binary search tree that creates a complete binary tree.

  Which one of the following elements can NEVER be the third element that is inserted?

  A.                                             B.                                          C.                           D.

### 60.3. Programming and Data Structures — GATE CSE 2025, Set 2, Question 53

**First appearance:** GATE CSE 2025, Set 2  
**Question type:** NAT  
**Marks:** 2  
**Source difficulty label:** unlabelled

Consider the following C program:
      #include <stdio.h>

      int g(int n) {
         return (n+10);
      }

      int f(int n) {
         return g(n*2);
      }

      int main() {
         int sum, n;
         sum=0;
         for (n=1; n<3; n++)
             sum += g(f(n));
         printf ("%d", sum);
         return 0;
      }

The output of the given C program is ___________. (Answer in integer)

### 60.4. Digital Logic — GATE CSE 2025, Set 1, Question 32

**First appearance:** GATE CSE 2025, Set 1  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** easy

Consider the following four variable Boolean function in sum-of-product form

where the value of the function is computed by considering                as a -bit binary number, where       denotes
the most significant bit and      denotes the least significant bit. Note that there are no don't care terms. Which ONE
of the following options is the CORRECT minimized Boolean expression for ?

 A.                                                                                               B.
 C.                                                                                               D.

### 60.5. Computer Networks — GATE CSE 2017, Set 1, Question 45

**First appearance:** GATE CSE 2017, Set 1  
**Question type:** NAT  
**Marks:** 2  
**Source difficulty label:** medium

The values of parameters for the Stop-and-Wait ARQ protocol are as given below:

       Bit rate of the transmission channel   Mbps.
       Propagation delay from sender to receiver                                        ms.

       Time to process a frame          ms.
       Number of bytes in the information frame          .
       Number of bytes in the acknowledge frame        .
       Number of overhead bytes in the information frame                                                 .

  Assume there are no transmission errors. Then, the transmission efficiency (expressed in percentage) of the Stop-
  and-Wait ARQ protocol for the above parameters is _____________ (correct to decimal places).

### 60.6. Compiler Design — GATE CSE 2019, Question 36

**First appearance:** GATE CSE 2019  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

Consider the following grammar and the semantic actions to support the inherited type declaration attributes.
Let                      , and   be the placeholders for the non-terminals            or    in the following
table:

Which one of the following are appropriate choices for                                                 and     ?

 A.

 B.
 C.
 D.

### 60.7. Databases — GATE CSE 2025, Set 1, Question 37

**First appearance:** GATE CSE 2025, Set 1  
**Question type:** MSQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

Consider            a       relational              schema           team                                        ,     with   functional      dependencies
                                                          .

  The relation team is decomposed into two relations,                                                                  and                    . Which of the following
  statement(s) is/are TRUE?

  A. The relation team is NOT in BCNF                                                           B. The relations        and   are in
     .                                                                                             BCNF.
  C. The decomposition constitutes a                                                            D. The relation team is NOT in NF .
     lossless join.

### 60.8. Engineering Mathematics — GATE CSE 2026, Set 1, Question 36

**First appearance:** GATE CSE 2026, Set 1  
**Question type:** MSQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

Let                        be defined as follows:

Which of the following statements is/are true?

 A.      has a local maximum                                                                             B.   has a local minimum
 C.       is continuous over                                                                             D.    is not differentiable over

### 60.9. Theory of Computation — GATE CSE 2019, Question 48

**First appearance:** GATE CSE 2019  
**Question type:** NAT  
**Marks:** 2  
**Source difficulty label:** hard

Let  be the set of all bijections from           to     , where                                                                      denotes the identity function, i.e.
              . Let denote composition on functions. For a string                                                                                                   , let
                             . Consider the language                                                                                       . The minimum number of states in
  any DFA accepting is _______

### 60.10. Computer Organization and Architecture — GATE CSE 2018, Question 51

**First appearance:** GATE CSE 2018  
**Question type:** NAT  
**Marks:** 2  
**Source difficulty label:** unlabelled

A       processor             has          integer
                                    registers                          and      floating point registers
                                      It uses a
                                          instruction format. There are four categories of instructions:
                                              and    category consists of four instructions, each with integer
register operands                  category consists of eight instructions, each with floating point register
operands                  category consists of fourteen instructions, each with one integer register operand and
one floating point register operand                       category consists of   instructions, each with a floating
point register operand

The maximum value of                         is _________.


## Week 61 — 10 questions

**Subject omitted this week:** Theory of Computation

### 61.1. Computer Organization and Architecture — GATE CSE 2023, Question 24

**First appearance:** GATE CSE 2023  
**Question type:** NAT  
**Marks:** 1  
**Source difficulty label:** unlabelled

A keyboard connected to a computer is used at a rate of keystroke per second. The computer system
polls the keyboard every            (milli seconds) to check for a keystroke and consumes               (micro
seconds) for each poll. If it is determined after polling that a key has been pressed, the system consumes an
additional       to process the keystroke. Let       denote the fraction of a second spent in polling and processing
a keystroke.

In an alternative implementation, the system uses interrupts instead of polling. An interrupt is raised for every
keystroke. It takes a total of      for servicing an interrupt and processing a keystroke. Let denote the fraction
of a second spent in servicing the interrupt and processing a keystroke.

  The ratio              is _____________. (Rounded off to one decimal place)

### 61.2. Databases — GATE CSE 2020, Question 36

**First appearance:** GATE CSE 2020  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

Consider a relational table                           that is in               , but not in BCNF. Which one of the following statements is
TRUE?

 A.   has a nontrivial functional dependency          , where   is not a superkey and is a prime attribute.
 B.    has a nontrivial functional dependency           , where   is not a superkey and   is a non-prime attribute
    and      is not a proper subset of any key.
 C.    has a nontrivial functional dependency           , where   is not a superkey and   is a non-prime attribute
    and      is a proper subset of some key
 D. A cell in holds a set instead of an atomic value.

### 61.3. General Aptitude — GATE CSE 2016, Set 1, Question 04

**First appearance:** GATE CSE 2016, Set 1  
**Question type:** MCQ  
**Marks:** 1  
**Source difficulty label:** medium

If 'relftaga' means carefree, 'otaga' means careful and 'fertaga' means careless, which of the following could
  mean 'aftercare'?

  A. zentaga                                      B. tagafer.                                     C. tagazen.                    D. relffer.

### 61.4. Operating Systems — GATE CSE 2022, Question 53

**First appearance:** GATE CSE 2022  
**Question type:** NAT  
**Marks:** 2  
**Source difficulty label:** unlabelled

Consider two files systems and , that use contiguous allocation and linked allocation, respectively. A file
of size     blocks is already stored in and also in . Now, consider inserting a new block in the middle of
the file (between         and       block), whose data is already available in the memory. Assume that there are
enough free blocks at the end of the file and that the file control blocks are already in memory. Let the number of
disk accesses required to insert a block in the middle of the file in  and are        and   , respectively, then the
value of          is__________________.

### 61.5. Programming and Data Structures — GATE CSE 2025, Set 2, Question 35

**First appearance:** GATE CSE 2025, Set 2  
**Question type:** MSQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

Consider a stack data structure into which we can PUSH and POP records. Assume that each record
pushed in the stack has a positive integer key and that all keys are distinct.

We wish to augment the stack data structure with an                                           time MIN operation that returns a pointer to the record
with smallest key present in the stack

 1. without deleting the corresponding record, and
 2. without increasing the complexities of the standard stack operations.

Which one or more of the following approach(es) can achieve it?

 A. Keep with every record in the stack, a pointer to the record with the smallest key below it.
 B. Keep a pointer to the record with the smallest key in the stack.
 C. Keep an auxiliary array in which the key values of the records in the stack are maintained in sorted order.
 D. Keep a Min-Heap in which the key values of the records in the stack are maintained.

### 61.6. Digital Logic — GATE CSE 2019, Question 30

**First appearance:** GATE CSE 2019  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** medium

Consider three -variable functions                                      , and          , which are expressed in sum-of-minterms as

For the following circuit with one AND gate and one XOR gate the output function                                       can be expressed as:

 A.                                                                                          B.
 C.                                                                                          D.

### 61.7. Computer Networks — GATE CSE 2022, Question 12

**First appearance:** GATE CSE 2022  
**Question type:** MCQ  
**Marks:** 1  
**Source difficulty label:** unlabelled

Consider an enterprise network with two Ethernet segments, a web server and a firewall, connected via
  three routers as shown below.

What is the number of subnets inside the enterprise network?

 A.                                           B.                                          C.            D.

### 61.8. Compiler Design — GATE CSE 2025, Set 2, Question 11

**First appearance:** GATE CSE 2025, Set 2  
**Question type:** MCQ  
**Marks:** 1  
**Source difficulty label:** unlabelled

Consider the following statements about the use of backpatching in a compiler for intermediate code
  generation:

   I. Backpatching can be used to generate code for Boolean expression in one pass.
  II. Backpatching can be used to generate code for flow-of-control statements in one pass.

  Which ONE of the folloeing options is CORRECT?

  A. Only I is correct                                                                           B. Only II is correct
  C. Both I and II are correct                                                                   D. Neither I nor II is correct

### 61.9. Algorithms — GATE CSE 2018, Question 45

**First appearance:** GATE CSE 2018  
**Question type:** NAT  
**Marks:** 2  
**Source difficulty label:** unlabelled

Consider the following program written in pseudo-code. Assume that                                      and   are integers.
  Count (x, y) {
    if (y !=1 ) {
        if (x !=1) {
            print("*");
            Count (x/2, y);
        }
        else {
            y=y-1;
            Count (1024, y);
        }
    }
  }

  The number of times that the                                        statement is executed by the call                         is _____

### 61.10. Engineering Mathematics — GATE CSE 2026, Set 1, Question 37

**First appearance:** GATE CSE 2026, Set 1  
**Question type:** MSQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

Let           be a simple, undirected graph. A vertex cover of    is a subset                                       such that for every
                     or       . Let the size of the smallest vertex cover in                                  be . Let be any vertex
cover of size .

  For a vertex                  , which of the following constraints will always ensure that                             ?

  A. The degree of is at least
  B. The vertex is on a path of length
  C. The vertex is on a cycle of length
  D. The vertex is a part of a clique of size


## Week 62 — 10 questions

**Subject omitted this week:** Compiler Design

### 62.1. Programming and Data Structures — GATE CSE 2016, Set 1, Question 37

**First appearance:** GATE CSE 2016, Set 1  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** medium

An operator delete       for a binary heap data structure is to be designed to delete the item in the -th node.
Assume that the heap is implemented in an array and refers to the -th index of the array. If the heap tree
has depth (number of edges on the path from the root to the farthest leaf ), then what is the time complexity to re-
fix the heap efficiently after the removal of the element?

 A.                                                                                       B.   but not
 C.                  but not                                                              D.      but not

### 62.2. Digital Logic — GATE CSE 2024, Set 1, Question 3

**First appearance:** GATE CSE 2024, Set 1  
**Question type:** MCQ  
**Marks:** 1  
**Source difficulty label:** unlabelled

Consider a system that uses bits for representing signed integers in 's complement format. In this
system, two integers        and     are represented as =            and =          . Which one of the following
operations will result in either an arithmetic overflow or an arithmetic underflow?

 A.                                              B.                                             C.                           D.

### 62.3. Theory of Computation — GATE CSE 2024, Set 1, Question 40

**First appearance:** GATE CSE 2024, Set 1  
**Question type:** MSQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

Consider the              -state             .    accepting the language                                              shown below. For any string
                        let              be the number of     in and                                   be the number of 1 's in .

Which of the following statements is/are FALSE?

 A. States and are distinguishable in
 B. States and are distinguishable in
 C. States and are distinguishable in
 D. Any string with                is in

### 62.4. Computer Networks — GATE CSE 2017, Set 2, Question 35

**First appearance:** GATE CSE 2017, Set 2  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

Consider two hosts    and , connected by a single direct link of rate        . The distance between
  the two hosts is           and the propagation speed along the link is             . Host   sends a
  file of              as one large message to host continuously. Let the transmission and propagation delays
  be                and               respectively. Then the value of and are

  A.                  and                      B.                 and                    C.        and   D.   and

### 62.5. General Aptitude — GATE CSE 2017, Set 2, Question 10

**First appearance:** GATE CSE 2017, Set 2  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** medium

An air pressure contour line joins locations in a region having the same atmospheric pressure. The following
  is an air pressure contour plot of a geographical region. Contour lines are shown at     bar intervals in this
  plot.
  If the possibility of a thunderstorm is given by how fast air pressure rises or drops over a region, which of the
  following regions is most likely to have a thunderstorm?

  A.                                            B.                                               C.                   D.

### 62.6. Operating Systems — GATE CSE 2023, Question 28

**First appearance:** GATE CSE 2023  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

Consider the two functions                               and                shown below.
      incr(){         decr(){
         wait(s);        wait(s);
         X = X+1;          X = X-1;
         signal(s);      signal(s);
      }             }

There are threads each invoking                                  once, and          threads each invoking   once, on the same shared variable
 . The initial value of is
Suppose there are two implementations of the semaphore                                      as follows:
            is a binary semaphore initialized to
            is a counting semaphore initialized to
Let           be the values of                               at the end of execution of all the threads with implementations
respectively.

Which one of the following choices corresponds to the minimum possible values of                                       respectively?

 A.                                         B.                                       C.                     D.

### 62.7. Algorithms — GATE CSE 2021, Set 1, Question 47

**First appearance:** GATE CSE 2021, Set 1  
**Question type:** MSQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

Consider a                           hashing approach for -bit integer keys:

  1. There is a main hash table of size .
  2. The least significant bits of a key is used to index into the main hash table.
  3. Initially, the main hash table entries are empty.
  4. Thereafter, when more keys are hashed into it, to resolve collisions, the set of all keys corresponding to a main
     hash table entry is organized as a binary tree that grows on demand.
  5. First, the       least significant bit is used to divide the keys into left and right subtrees.
  6. To resolve more collisions, each node of the binary tree is further sub-divided into left and right subtrees based
     on the        least significant bit.
  7. A split is done only if it is needed, i.e., only when there is a collision.

  Consider the following state of the hash table.

  Which of the following sequences of key insertions can cause the above state of the hash table (assume the keys
  are in decimal notation)?

  A.                                                                       B.

  C.                                                                                      D.

### 62.8. Engineering Mathematics — GATE CSE 2023, Question 41

**First appearance:** GATE CSE 2023  
**Question type:** MSQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

Let        be a set and                denote the powerset of                        .
Define a binary operation                       on           as follows:

  Let                            . Which of the following statements about                                       is/are correct?

  A.    is a group.
  B. Every element in                        has an inverse, but  is NOT a group.
  C. For every                              the inverse of is the complement of .
  D. For every                              the inverse of is .

### 62.9. Computer Organization and Architecture — GATE CSE 2016, Set 2, Question 31

**First appearance:** GATE CSE 2016, Set 2  
**Question type:** NAT  
**Marks:** 2  
**Source difficulty label:** medium

Consider a processor with     registers and an instruction set of size twelve. Each instruction has five distinct
fields, namely, opcode, two source register identifiers, one destination register identifier, and twelve-bit
immediate value. Each instruction must be stored in memory in a byte-aligned fashion. If a program has
instructions, the amount of memory (in bytes) consumed by the program text is _________.

### 62.10. Databases — GATE CSE 2025, Set 1, Question 29

**First appearance:** GATE CSE 2025, Set 1  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

Consider two relations describing teams and players in a sports league:

        teams(tid, tname): tid, tname are team-id and team-name, respectively
        players(pid,pname,tid): pid, pname, and tid denote player-id, playername and the team-id of the player,
        respectively

  Which ONE of the following tuple relational calculus queries returns the name of the players who play for the team
  having tname as '     ?

  A.        . pname               players                        teams
  B.        . pname               teams                         players
  C.        . pname               players                        teams         . tname
  D.        . pname               teams                         players        . tname


## Week 63 — 10 questions

**Subject omitted this week:** Computer Organization and Architecture

### 63.1. Digital Logic — GATE CSE 2019, Question 8

**First appearance:** GATE CSE 2019  
**Question type:** MCQ  
**Marks:** 1  
**Source difficulty label:** unlabelled

Consider                where        and Z are all in sign-magnitude form. X and Y are each represented in
bits. To avoid overflow, the representation of would require a minimum of:

 A.      bits                                   B.             bits                          C.         bits              D.     bits

### 63.2. General Aptitude — GATE CSE 2026, Set 1, Question 8

**First appearance:** GATE CSE 2026, Set 1  
**Question type:** NAT  
**Marks:** 2  
**Source difficulty label:** unlabelled

For positive real numbers                        and            , the function                is defined as:
                                                   . The max function is defined as:

  The graph below shows the plot of a function                                           versus      .
             can be expressed as                             .

  A.                                                                                            B.
  C.                                                                                            D.

### 63.3. Theory of Computation — GATE CSE 2021, Set 1, Question 51

**First appearance:** GATE CSE 2021, Set 1  
**Question type:** NAT  
**Marks:** 2  
**Source difficulty label:** unlabelled

In a pushdown automaton                                                              , a transition of the form,

where                     ,                       , and                              , represents

Consider the following pushdown automaton over the input alphabet                                                  and stack alphabet   .

The number of strings of length                           accepted by the above pushdown automaton is ___________

### 63.4. Algorithms — GATE CSE 2026, Set 2, Question 22

**First appearance:** GATE CSE 2026, Set 2  
**Question type:** MSQ  
**Marks:** 1  
**Source difficulty label:** unlabelled

Consider an array                                     . Suppose the merge sort algorithm is executed on
  array to sort it in increasing order. The merge sort algorithm will carry out a total of merge operations.

  A merge operation on sorted left array    and sorted right array    is said to be void if the output of the merge
  operation is the elements of array followed by the elements of array .

  The number of void merge operations among these                                             merge operations is      . (answer in integer)

### 63.5. Computer Networks — GATE CSE 2021, Set 2, Question 34

**First appearance:** GATE CSE 2021, Set 2  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

Consider the cyclic redundancy check              based error detecting scheme having the generator
  polynomial                  . Suppose the message                            is to be transmitted. Check
  bits         are appended at the end of the message by the transmitter using the above             scheme. The
  transmitted bit string is denoted by                    . The value of the checkbit sequence         is

  A.                                         B.                                            C.                        D.

### 63.6. Programming and Data Structures — GATE CSE 2026, Set 2, Question 9

**First appearance:** GATE CSE 2026, Set 2  
**Question type:** NAT  
**Marks:** 1  
**Source difficulty label:** unlabelled

Consider the following three ANSI-C programs,                    and   .

Which one of the following statements is true?

 A. Only      will compile without any error
 B. Only      will compile without any error
 C. Only      will compile without any error
 D. All three programs            and     will compile without any error

### 63.7. Operating Systems — GATE CSE 2025, Set 1, Question 41

**First appearance:** GATE CSE 2025, Set 1  
**Question type:** NAT  
**Marks:** 2  
**Source difficulty label:** unlabelled

A disk of size            bytes is divided into blocks of     bytes. A file is stored in the disk using linked
  allocation. In linked allocation, each data block reserves bytes to store the pointer to the next data block.
  The link part of the last data block contains a NULL pointer (also of bytes). Suppose a file of       bytes needs to
  be stored in the disk. Assume,                and         . The amount of space in bytes that will be wasted due to
  internal fragmentation is __________. (Answer in integer)

### 63.8. Databases — GATE CSE 2023, Question 52

**First appearance:** GATE CSE 2023  
**Question type:** NAT  
**Marks:** 2  
**Source difficulty label:** unlabelled

Consider a database of fixed-length records, stored as an ordered file. The database has                  records,
with each record being       bytes, of which the primary key occupies       bytes. The data file is block-aligned
in that each data record is fully contained within a block. The database is indexed by a primary index file, which is
also stored as a block-aligned ordered file. The figure below depicts this indexing scheme.

  Suppose the block size of the file system is         bytes, and a pointer to a block occupies bytes. The system
  uses binary search on the index file to search for a record with a given key. You may assume that a binary search
  on an index file of blocks takes         block accesses in the worst case.
  Given a key, the number of block accesses required to identify the block in the data file that may contain a record
  with the key, in the worst case, is _____________.

### 63.9. Engineering Mathematics — GATE CSE 2016, Set 1, Question 27

**First appearance:** GATE CSE 2016, Set 1  
**Question type:** NAT  
**Marks:** 2  
**Source difficulty label:** medium

Consider the recurrence relation                                                                     . Let                 . The value of     is
__________.

### 63.10. Compiler Design — GATE CSE 2021, Set 1, Question 31

**First appearance:** GATE CSE 2021, Set 1  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

Consider the following context-free grammar where the set of terminals is                                     .

The following is a partially-filled                                parsing table.

Which one of the following choices represents the correct combination for the numbered cells in the parsing table
(“blank” denotes that the corresponding cell is empty)?

 A.
 B.
C.
D.


## Week 64 — 10 questions

**Subject omitted this week:** Engineering Mathematics

### 64.1. Compiler Design — GATE CSE 2017, Set 1, Question 17

**First appearance:** GATE CSE 2017, Set 1  
**Question type:** MCQ  
**Marks:** 1  
**Source difficulty label:** easy

Consider the following grammar:

What is FOLLOW( )?

 A.                                          B.                               C.                           D.

### 64.2. Computer Networks — GATE CSE 2025, Set 1, Question 47

**First appearance:** GATE CSE 2025, Set 1  
**Question type:** NAT  
**Marks:** 2  
**Source difficulty label:** unlabelled

Suppose a message of size         bytes is transmitted from a source to a destination using IPv4 protocol
  via two routers as shown in the figure. Each router has a defined maximum transmission unit (MTU) as
  shown in the figure, including IP header. The number of fragments that will be delivered to the destination is
  _________. (Answer in integer)

### 64.3. Operating Systems — GATE CSE 2021, Set 2, Question 43

**First appearance:** GATE CSE 2021, Set 2  
**Question type:** MSQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

Consider a computer system with multiple shared resource types, with one instance per resource type. Each
  instance can be owned by only one process at a time. Owning and freeing of resources are done by holding
  a global lock   . The following scheme is used to own a resource instance:
       function OWNRESOURCE(Resource R)
         Acquire lock L // a global lock
         if R is available then
            Acquire R
            Release lock L
         else
            if R is owned by another process P then
            Terminate P, after releasing all resources owned by P
            Acquire R
            Restart P
            Release lock L
            end if
         end if
       end function

  Which of the following choice(s) about the above scheme is/are correct?

  A. The scheme ensures that deadlocks will not occur
  B. The scheme may lead to live-lock
  C. The scheme may lead to starvation
  D. The scheme violates the mutual exclusion property

### 64.4. Theory of Computation — GATE CSE 2017, Set 2, Question 40

**First appearance:** GATE CSE 2017, Set 2  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

Consider the following languages.

Which of the following are CORRECT?

  I.        is context free but not regular
 II.        is not context free
III.        is not context free but recursive
IV.         is deterministic context free

 A. I, II and IV only                        B. II and III only                           C. I and IV only              D. III and IV only

### 64.5. Algorithms — GATE CSE 2024, Set 2, Question 32

**First appearance:** GATE CSE 2024, Set 2  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

Consider an array    that contains                                  positive integers. A subarray of   is defined to be a sequence of array
locations with consecutive indices.

The code snippet given below has been written to compute the length of the longest subarray of                                      that contains
at most two distinct integers. The code has two missing expressions labelled and    .
      int first=0, second=0, len1=0, len2=0, maxlen=0;
      for (int i=0; i < n; i++) {
         if (X[i] == first) {
             len2++; len1++;
         } else if (X[i] == second) {
             len2++;
             len1 =       (P)     ;

             second = first;

             } else {
                len2 =        (Q)       ;

                 len1 = 1; second = first;
             }
             if (len2 > maxlen) {
                 maxlen = len2;
             }
             first = X[i];
       }

  Which one of the following options gives the CORRECT missing expressions?
  (Hint: At the end of the -th iteration, the value of         is the length of the longest subarray ending with      that
  contains all equal values, and        is the length of the longest subarray ending with       that contains at most two
  distinct values.)

  A.
  B.
  C.
  D.

### 64.6. Databases — GATE CSE 2017, Set 2, Question 46

**First appearance:** GATE CSE 2017, Set 2  
**Question type:** NAT  
**Marks:** 2  
**Source difficulty label:** unlabelled

Consider the following database table named                      .

Consider the following SQL query:
      SELECT ta.player FROM top_scorer AS ta
      WHERE ta.goals >ALL (SELECT tb.goals
        FROM top_scorer AS tb
        WHERE tb.country = 'Spain')
      AND ta.goals > ANY (SELECT tc.goals
        FROM top_scorer AS tc
        WHERE tc.country='Germany')

The number of tuples returned by the above SQL query is ______

### 64.7. Computer Organization and Architecture — GATE CSE 2016, Set 1, Question 32

**First appearance:** GATE CSE 2016, Set 1  
**Question type:** NAT  
**Marks:** 2  
**Source difficulty label:** medium

The stage delays in a -stage pipeline are                and      picoseconds. The first stage (with delay
     picoseconds) is replaced with a functionality equivalent design involving two stages with respective
delays     and      picoseconds. The throughput increase of the pipeline is ___________ percent.

### 64.8. Digital Logic — GATE CSE 2017, Set 2, Question 28

**First appearance:** GATE CSE 2017, Set 2  
**Question type:** NAT  
**Marks:** 2  
**Source difficulty label:** unlabelled

G i ve n                                                   ; where   represents the 'don't-care'
  condition in Karnaugh maps. Which of the following is a minimum product-of-sums (POS) form of
               ?

  A.                                                                                                  B.
  C.                                                                                                  D.

### 64.9. General Aptitude — GATE CSE 2026, Set 2, Question 3

**First appearance:** GATE CSE 2026, Set 2  
**Question type:** NAT  
**Marks:** 2  
**Source difficulty label:** unlabelled

A day can only be cloudy or sunny. The probability of a day being cloudy is     , independent of the condition
on other days. What is the probability that in any given four days, there will be three cloudy days and one
sunny day?

 A.                                                  B.                         C.                         D.

### 64.10. Programming and Data Structures — GATE CSE 2018, Question 20

**First appearance:** GATE CSE 2018  
**Question type:** NAT  
**Marks:** 1  
**Source difficulty label:** unlabelled

The postorder traversal of a binary tree is                         . The inorder traversal of the same tree is
                       . The height of a tree is the length of the longest path from the root to any leaf. The
height of the binary tree above is _____


## Week 65 — 10 questions

**Subject omitted this week:** Operating Systems

### 65.1. General Aptitude — GATE CSE 2017, Set 2, Question 7

**First appearance:** GATE CSE 2017, Set 2  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** medium

There are three boxes. One contains apples, another contains oranges and the last one contains both
apples and oranges. All three are known to be incorrectly labeled. If you are permitted to open just one box
and then pull out and inspect only one fruit, which box would you open to determine the contents of all three boxes?

 A. The box labeled ‘Apples’                                                                     B. The box labeled ‘Apples   and
                                                                                                    Oranges’
 C. The box labeled ‘Oranges’                                                                    D. Cannot be determined

### 65.2. Digital Logic — GATE CSE 2018, Question 33

**First appearance:** GATE CSE 2018  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** medium

Consider the unsigned 8-bit fixed point binary number representation, below,

  where the position of the primary point is between  and     . Assume     is the most significant bit. Some of the
  decimal numbers listed below cannot be represented exactly in the above representation:

    i.
   ii.
  iii.
  iv.

  Which one of the following statements is true?

  A. None of          can be exactly represented
  B. Only cannot be exactly represented
  C. Only    and cannot be exactly represented
  D. Only and cannot be exactly represented

### 65.3. Theory of Computation — GATE CSE 2021, Set 1, Question 12

**First appearance:** GATE CSE 2021, Set 1  
**Question type:** MSQ  
**Marks:** 1  
**Source difficulty label:** unlabelled

Let      denote an encoding of an automaton                                               . Suppose that                 . Which of the following
languages is/are    recursive?

A.                                is a             such that
B.                                is a             such that
C.                                is a             such that
D.                                is a             such that

### 65.4. Databases — GATE CSE 2025, Set 2, Question 17

**First appearance:** GATE CSE 2025, Set 2  
**Question type:** MSQ  
**Marks:** 1  
**Source difficulty label:** unlabelled

An audit of a banking transactions system has found that on an earlier occasion, two joint holders of account
   attempted simultaneous transfers of Rs.             each from account   to account . Both transactions
read the same value, Rs.         , as the initial balance in and were allowed to go through. was credited Rs.
       twice. was debited only once and ended up with a balance of Rs.          .

Which of the following properties is/are certain to have been violated by the system?

 A. Atomicity                                 B. Consistency                                  C. Isolation             D. Durability

### 65.5. Algorithms — GATE CSE 2025, Set 2, Question 10

**First appearance:** GATE CSE 2025, Set 2  
**Question type:** MSQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

​Consider an unordered list of                              distinct integers.
What is the minimum number of element comparisons required to find an integer in the list that is NOT the
largest in the list?

 A.                                             B.                                         C.                             D.

### 65.6. Computer Organization and Architecture — GATE CSE 2021, Set 2, Question 20

**First appearance:** GATE CSE 2021, Set 2  
**Question type:** NAT  
**Marks:** 1  
**Source difficulty label:** unlabelled

Consider a computer system with          support. The         module is transferring one -bit character in
one        cycle from a device to memory through cycle stealing at regular intervals. Consider a
processor. If      processor cycles are used for      , the data transfer rate of the device is __________ bits per
second.

### 65.7. Engineering Mathematics — GATE CSE 2018, Question 26

**First appearance:** GATE CSE 2018  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** medium

Consider a matrix P whose only eigenvectors are the multiples of                                                        .

Consider the following statements.

  I. P does not have an inverse
 II. P has a repeated eigenvalue
III. P cannot be diagonalized

Which one of the following options is correct?

 A. Only I and III are necessarily true                                                               B. Only II is necessarily true
 C. Only I and II are necessarily true                                                                D. Only II and III are necessarily true

### 65.8. Compiler Design — GATE CSE 2024, Set 1, Question 27

**First appearance:** GATE CSE 2024, Set 1  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

​Consider the following syntax-directed definition                                              .

Given                         as the input, which one of the following options is the                                 value computed by the
(in the attribute                 )?

 A.                                            B.                                             C.                 D.

### 65.9. Computer Networks — GATE CSE 2023, Question 55

**First appearance:** GATE CSE 2023  
**Question type:** NAT  
**Marks:** 2  
**Source difficulty label:** unlabelled

The forwarding table of a router is shown below.

A packet addressed to a destination address                                                     arrives at the router. It will be forwarded to the
interface with __________.

### 65.10. Programming and Data Structures — GATE CSE 2019, Question 46

**First appearance:** GATE CSE 2019  
**Question type:** NAT  
**Marks:** 2  
**Source difficulty label:** unlabelled

Let be a full binary tree with leaves. (A full binary tree has every level full.) Suppose two leaves and
of are chosen uniformly and independently at random. The expected value of the distance between and
  in  (ie., the number of edges in the unique path between           and ) is (rounded off to decimal places)
_________.


## Week 66 — 10 questions

**Subject omitted this week:** Theory of Computation

### 66.1. Engineering Mathematics — GATE CSE 2017, Set 1, Question 19

**First appearance:** GATE CSE 2017, Set 1  
**Question type:** MSQ  
**Marks:** 1  
**Source difficulty label:** medium

Let    be a Gaussian random variable with mean 0 and variance . Let                                               =        where
  is the maximum of and . The median of is ______________ .

### 66.2. Digital Logic — GATE CSE 2021, Set 2, Question 4

**First appearance:** GATE CSE 2021, Set 2  
**Question type:** MCQ  
**Marks:** 1  
**Source difficulty label:** unlabelled

The format of the single-precision floating point representation of a real number as per the
standard is as follows:

Which one of the following choices is correct with respect to the smallest normalized positive number represented
using the standard?

 A. exponent                                    and mantissa
 B. exponent                                    and mantissa
 C. exponent                                    and mantissa

 D. exponent                                    and mantissa

### 66.3. Operating Systems — GATE CSE 2025, Set 1, Question 44

**First appearance:** GATE CSE 2025, Set 1  
**Question type:** NAT  
**Marks:** 2  
**Source difficulty label:** unlabelled

In optimal page replacement algorithm, information about all future page references is available to the
operating system (OS). A modification of the optimal page replacement algorithm is as follows:

The OS correctly predicts only up to next                                   page references (including the current page) at the time of allocating a
frame to a page.

A process accesses the pages in the following order of page numbers:

If the system has three memory frames that are initially empty, the number of page faults that will occur during
execution of the process is __________. (Answer in integer)

### 66.4. Compiler Design — GATE CSE 2016, Set 1, Question 45

**First appearance:** GATE CSE 2016, Set 1  
**Question type:** NAT  
**Marks:** 2  
**Source difficulty label:** medium

The attribute of three arithmetic operators in some programming language are given below.

The value of the expression                                                     in this language is ________.

### 66.5. General Aptitude — GATE CSE 2024, Set 1, Question 9

**First appearance:** GATE CSE 2024, Set 1  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

A rectangular paper of                  is folded times. Each fold is made along the line of symmetry, which
is perpendicular to its long edge. The perimeter of the final folded sheet (in ) is

 A.                                           B.                    C.          D.

### 66.6. Databases — GATE CSE 2020, Question 37

**First appearance:** GATE CSE 2020  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

Consider a schedule of transactions                               and          :

Here, RX stands for “Read(X)” and WX stands for “Write(X)”. Which one of the following schedules is conflict
equivalent to the above schedule?

 A.

 B.

 C.

 D.

### 66.7. Computer Organization and Architecture — GATE CSE 2024, Set 2, Question 48

**First appearance:** GATE CSE 2024, Set 2  
**Question type:** NAT  
**Marks:** 2  
**Source difficulty label:** unlabelled

A non-pipelined instruction execution unit operating at       takes an average of cycles to execute an
instruction of a program . The unit is then redesigned to operate on a -stage pipeline at        . Assume
that the ideal throughput of the pipelined unit is instruction per cycle. In the execution of program ,
instructions incur an average of cycles stall due to data hazards and         instructions incur an average of
cycles stall due to control hazards. The speedup (rounded off to one decimal place) obtained by the pipelined
design over the non-pipelined design is ____________.

### 66.8. Algorithms — GATE CSE 2016, Set 2, Question 39

**First appearance:** GATE CSE 2016, Set 2  
**Question type:** NAT  
**Marks:** 2  
**Source difficulty label:** medium

The given diagram shows the flowchart for a recursive function         . Assume that all statements, except for
the recursive calls, have       time complexity. If the worst case time complexity of this function is       ,
then the least possible value (accurate up to two decimal positions) of is ________.
Flow chart for Recursive Function                                     .

### 66.9. Computer Networks — GATE CSE 2026, Set 1, Question 9

**First appearance:** GATE CSE 2026, Set 1  
**Question type:** MSQ  
**Marks:** 1  
**Source difficulty label:** unlabelled

Which of the following statements is/are true with respect to the interaction of a web browser with a web
server using HTTP     ?

 A. HTTP       facilitates downloading multiple objects of the same webpage over the same TCP connection, if the
    objects are stored in the same server
 B. HTTP       facilitates downloading multiple objects of the same webpage over the same TCP connection, even if
    they are stored in different servers
 C. HTTP        facilitates sending a request for downloading one object without waiting for a previously requested
    object to be downloaded completely
 D. HTTP       facilitates downloading multiple webpages on the same server to be downloaded over a single TCP
    connection

### 66.10. Programming and Data Structures — GATE CSE 2024, Set 2, Question 29

**First appearance:** GATE CSE 2024, Set 2  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

You are given a set     of distinct integers. A binary search tree is created by inserting all elements of
one by one, starting with an empty tree. The tree       follows the convention that, at each node, all values
stored in the left subtree of the node are smaller than the value stored at the node. You are not aware of the
sequence in which these values were inserted into , and you do not have access to .
Which one of the following statements is TRUE?

 A. Inorder traversal of can be determined from
 B. Root node of can be determined from
 C. Preorder traversal of can be determined from
 D. Postorder traversal of can be determined from


## Week 67 — 10 questions

**Subject omitted this week:** Algorithms

### 67.1. Databases — GATE CSE 2021, Set 1, Question 27

**First appearance:** GATE CSE 2021, Set 1  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

The following relation records the age of                                               employees of a company, where          (indicating the
employee number) is the key:

Consider the following relational algebra expression:

What does the above expression generate?

 A. Employee numbers of only those employees whose age is the maximum
 B. Employee numbers of only those employees whose age is more than the age of exactly one other employee
 C. Employee numbers of all employees whose age is not the minimum
 D. Employee numbers of all employees whose age is the minimum

### 67.2. Compiler Design — GATE CSE 2016, Set 1, Question 19

**First appearance:** GATE CSE 2016, Set 1  
**Question type:** NAT  
**Marks:** 1  
**Source difficulty label:** medium

Consider the following code segment.
  x = u - t;
  y = x * v;
  x = y + w;
  y = t - z;
  y = x * y;

  The minimum number of total variables required to convert the above code segment to static single assignment
  form is __________.

### 67.3. Theory of Computation — GATE CSE 2021, Set 2, Question 28

**First appearance:** GATE CSE 2021, Set 2  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

Suppose we want to design a synchronous circuit that processes a string of ’s and ’s. Given a string, it
produces another string by replacing the first in any subsequence of consecutive ’s by a . Consider the
following example.

A Mealy Machine is a state machine where both the next state and the output are functions of the present state and
the current input.
The above mentioned circuit can be designed as a two-state Mealy machine. The states in the Mealy machine can
be represented using Boolean values and . We denote the current state, the next state, the next incoming bit,
and the output bit of the Mealy machine by the variables    and respectively.
Assume the initial state of the Mealy machine is .
What are the Boolean expressions corresponding to and                                                in terms of   and ?

 A.                                                                                             B.

 C.                                                                                             D.

### 67.4. Digital Logic — GATE CSE 2025, Set 2, Question 22

**First appearance:** GATE CSE 2025, Set 2  
**Question type:** NAT  
**Marks:** 1  
**Source difficulty label:** unlabelled

The following two signed                               's complement numbers (multiplicand M and multiplier Q) are being multiplied using
  Booth's algorithm:

  M:                                               and Q:

  The total number of addition and subtraction operations to be performed is_____________. (Answer in integer)

### 67.5. Operating Systems — GATE CSE 2020, Question 50

**First appearance:** GATE CSE 2020  
**Question type:** NAT  
**Marks:** 2  
**Source difficulty label:** unlabelled

Consider the following set of processes, assumed to have arrived at time . Consider the CPU scheduling
algorithms Shortest Job First (SJF) and Round Robin (RR). For RR, assume that the processes are
scheduled in the order               .

If the time quantum for RR is ms, then the absolute value of the difference between the average turnaround times
(in ms) of SJF and RR (round off to decimal places is_______

### 67.6. Programming and Data Structures — GATE CSE 2026, Set 2, Question 51

**First appearance:** GATE CSE 2026, Set 2  
**Question type:** NAT  
**Marks:** 2  
**Source difficulty label:** unlabelled

Consider the following ANSI-C function.
       int func(int start, int end){
             int length=end+1-start;
             if((length<1)||(start<0)||(end<0)){ return(0); }
             if(length%3==0){
                 return(func(start+1, end));
             }else if(length%3==1){
                 return(1+func(start, end-1));
             }else {
                 return(func(start+2, end));
             }
       }

  The maximum possible value that can be returned from this function is                                    . (answer in integer)

  Note: Ignore syntax errors (if any) in the function.

### 67.7. Computer Networks — GATE CSE 2024, Set 1, Question 55

**First appearance:** GATE CSE 2024, Set 1  
**Question type:** NAT  
**Marks:** 2  
**Source difficulty label:** unlabelled

Consider sending an      datagram of size         bytes (including    bytes of      header) from a sender to a
receiver over a path of two links with a router between them. The first link (sender to router) has an
(Maximum Transmission Unit) size of        bytes, while the second link (router to receiver) has an       size of
bytes. The number of fragments that would be delivered at the receiver is ____________.

### 67.8. Computer Organization and Architecture — GATE CSE 2024, Set 1, Question 45

**First appearance:** GATE CSE 2024, Set 1  
**Question type:** NAT  
**Marks:** 2  
**Source difficulty label:** unlabelled

The baseline execution time of a program on a            single core machine is      nanoseconds (     . The
  code corresponding to            of the execution time can be fully parallelized. The overhead for using an
  additional core is          when running on a multicore system. Assume that all cores in the multicore system run
  their share of the parallelized code for an equal amount of time.

  The number of cores that minimize the execution time of the program is __________.

### 67.9. Engineering Mathematics — GATE CSE 2024, Set 2, Question 50

**First appearance:** GATE CSE 2024, Set 2  
**Question type:** MSQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

The chromatic number of a graph is the minimum number of colours used in a proper colouring of the graph.
  The chromatic number of the following graph is __________.

### 67.10. General Aptitude — GATE CSE 2021, Set 2, Question 10

**First appearance:** GATE CSE 2021, Set 2  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

Six students                                                     , with distinct heights, compare their heights and make the following
observations.

                                :        is taller than .
                                    :      is the shortest of all.
                                        : is taller than only one student.
                                        : is taller than but is not the tallest

The number of students that are taller than                                          is the same as the number of students shorter than ____________.

 A.                                               B.                                        C.                        D.


## Week 68 — 10 questions

**Subject omitted this week:** Programming and Data Structures

### 68.1. Databases — GATE CSE 2024, Set 1, Question 12

**First appearance:** GATE CSE 2024, Set 1  
**Question type:** MSQ  
**Marks:** 1  
**Source difficulty label:** unlabelled

Which of the following statements about a relation                                             in first normal form   is/are TRUE?

 A.        can have a multi-attribute key
 B.        cannot have a foreign key
 C.        cannot have a composite attribute
 D.        cannot have more than one candidate key

### 68.2. Engineering Mathematics — GATE CSE 2016, Set 2, Question 26

**First appearance:** GATE CSE 2016, Set 2  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** medium

A binary relation                on                     is defined as follows:                   if        or        . Consider the following
propositions:

                 is reflexive.
                 is transitive.

Which one of the following statements is TRUE?

 A. Both and are true.                                                                                  B.   is true and is false.
 C.   is false and is true.                                                                             D. Both and are false.

### 68.3. Theory of Computation — GATE CSE 2024, Set 2, Question 31

**First appearance:** GATE CSE 2024, Set 2  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

​Let       be the -state                      with -transitions shown in the diagram below.

Which one of the following regular expressions represents the language accepted by                 ?

 A.                                                                                           B.
 C.                                                                                           D.

### 68.4. Computer Networks — GATE CSE 2024, Set 1, Question 21

**First appearance:** GATE CSE 2024, Set 1  
**Question type:** MSQ  
**Marks:** 1  
**Source difficulty label:** unlabelled

Which of the following fields is/are modified in the     header of a packet going out of a network address
translation        device from an internal network to an external network?

 A. Source                                                                                           B. Destination
 C. Header Checksum                                                                                  D. Total Length

### 68.5. Algorithms — GATE CSE 2025, Set 2, Question 31

**First appearance:** GATE CSE 2025, Set 2  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

An array           of length with distinct elements is said to be bitonic if there is an index            such that
                 is sorted in the non-decreasing order and                is sorted in the non-increasing order.

  Which ONE of the following represents the best possible asymptotic bound for the worst-case number of
  comparisons by an algorithm that searches for an element in a bitonic array ?

  A.                                                                                                    B.
  C.                                                                                                    D.

### 68.6. Operating Systems — GATE CSE 2018, Question 40

**First appearance:** GATE CSE 2018  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** medium

Consider the following solution to the producer-consumer synchronization problem. The shared buffer size is
  . Three semaphores            ,       and        are defined with respective initial values of      and .
Semaphore           denotes the number of available slots in the buffer, for the consumer to read from. Semaphore
     denotes the number of available slots in the buffer, for the producer to write to. The placeholder variables,
denoted by , ,          and , in the code below can be assigned either               or    . The valid semaphore
operations are:        and            .

Which one of the following assignments tp                                  ,   ,   and        will yield the correct solution?

 A.
 B.
 C.
 D.

### 68.7. Computer Organization and Architecture — GATE CSE 2021, Set 2, Question 27

**First appearance:** GATE CSE 2021, Set 2  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

Assume a two-level inclusive cache hierarchy,                                                and   , where   is the larger of the two. Consider the
following statements.

        : Read misses in a write through    cache do not result in writebacks of dirty lines to the
        : Write allocate policy must be used in conjunction with write through caches and no-write allocate policy is
      used with writeback caches.

Which of the following statements is correct?

 A.        is true and           is false                                                     B.     is false and    is true
 C.        is true and           is true                                                      D.     is false and    is false

### 68.8. Digital Logic — GATE CSE 2016, Set 2, Question 07

**First appearance:** GATE CSE 2016, Set 2  
**Question type:** MSQ  
**Marks:** 1  
**Source difficulty label:** medium

Consider an eight-bit ripple-carry adder for computing the sum of and , where       and                                       are integers
  represented in 's complement form. If the decimal value of is one, the decimal value of                                      that leads to
  the longest latency for the sum to stabilize is ___________

### 68.9. General Aptitude — GATE CSE 2016, Set 1, Question 07

**First appearance:** GATE CSE 2016, Set 1  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** medium

Indian currency notes show the denomination indicated in at least seventeen languages. If this is not an
indication of the nation's diversity, nothing else is.
Which of the following can be logically inferred from the above sentences?

 A. India is a country of exactly seventeen languages.
 B. Linguistic pluralism is the only indicator of a nation's diversity.
 C. Indian currency notes have sufficient space for all the Indian languages.
 D. Linguistic pluralism is strong evidence of India's diversity.

### 68.10. Compiler Design — GATE CSE 2017, Set 1, Question 12

**First appearance:** GATE CSE 2017, Set 1  
**Question type:** MCQ  
**Marks:** 1  
**Source difficulty label:** medium

Consider the following intermediate program in three address code
  p=a-b
  q=p*c
  p=u*v
  q=p+q

  Which one of the following corresponds to a static single assignment form of the above code?

  A. p1 = a - b                                B. p3 = a - b                                   C. p1 = a - b             D. p1 = a - b

       q1 = p1 * c                                     q4 = p3 * c                                  q1 = p2 * c              q1 = p * c

       p1 = u * v                                      p4 = u * v                                   p3 = u * v               p2 = u * v

       q1 = p1 + q1                                    q5 = p4 + q4                                 q2 = p4 + q3             q2 = p + q


## Week 69 — 10 questions

**Subject omitted this week:** Operating Systems

### 69.1. Digital Logic — GATE CSE 2019, Question 4

**First appearance:** GATE CSE 2019  
**Question type:** MCQ  
**Marks:** 1  
**Source difficulty label:** unlabelled

In       -bit ’s complement representation, the decimal number                                                 is:

 A.                                                                                               B.
 C.                                                                                               D.

### 69.2. Computer Networks — GATE CSE 2023, Question 15

**First appearance:** GATE CSE 2023  
**Question type:** MSQ  
**Marks:** 1  
**Source difficulty label:** unlabelled

Which of the following statements is/are                                                  about the
routing protocol used in the Internet?

 A.                  implements Bellman-Ford algorithm to find shortest paths.
 B.                  uses Dijkstra's shortest path algorithm to implement least-cost path routing.
 C.                  is used as an inter-domain routing protocol.
 D.                  implements hierarchical routing.

### 69.3. Programming and Data Structures — GATE CSE 2026, Set 1, Question 24

**First appearance:** GATE CSE 2026, Set 1  
**Question type:** NAT  
**Marks:** 1  
**Source difficulty label:** unlabelled

Consider the following program in C:
      #include <stdio.h>
      void func(int i, int j) {
         if(i < j) {
             int i = 0;

               while (i < 10) {
                 j += 2;
                 i++;
               }
             }
             printf("%d", i);
       }
       int main() {
          int i = 9, j = 10;
          func(i, j);
          return 0;
       }

  The output of the program is                                 . (answer in integer)

  Note: Assume that the program compiles and runs successfully.

### 69.4. General Aptitude — GATE CSE 2024, Set 2, Question 8

**First appearance:** GATE CSE 2024, Set 2  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

The pie charts depict the shares of various power generation technologies in the total electricity generation
of a country for the years     and       .

The renewable sources of electricity generation consist of Hydro, Solar and Wind. Assuming that the total electricity
generated remains the same from             to      , what is the percentage increase in the share of the renewable
sources of electricity generation over this period?

 A.                                           B.                               C.                             D.

### 69.5. Computer Organization and Architecture — GATE CSE 2020, Question 44

**First appearance:** GATE CSE 2020  
**Question type:** NAT  
**Marks:** 2  
**Source difficulty label:** unlabelled

A processor has      registers and uses -bit instruction format. It has two types of instructions: I-type and
R-type. Each I-type instruction contains an opcode, a register name, and a -bit immediate value. Each R-
type instruction contains an opcode and two register names. If there are distinct I-type opcodes, then the
maximum number of distinct R-type opcodes is _______.

### 69.6. Theory of Computation — GATE CSE 2021, Set 2, Question 12

**First appearance:** GATE CSE 2021, Set 2  
**Question type:** MSQ  
**Marks:** 1  
**Source difficulty label:** unlabelled

Let     be a regular language and                                   be a context-free language. Which of the following languages is/are
context-free?

 A.                                                                                                B.
 C.                                                                                                D.

### 69.7. Engineering Mathematics — GATE CSE 2018, Question 16

**First appearance:** GATE CSE 2018  
**Question type:** NAT  
**Marks:** 1  
**Source difficulty label:** medium

The value of                                               correct to three decimal places (assuming that           ) is ____

### 69.8. Algorithms — GATE CSE 2026, Set 1, Question 51

**First appearance:** GATE CSE 2026, Set 1  
**Question type:** NAT  
**Marks:** 2  
**Source difficulty label:** unlabelled

Consider the recursive functions represented by the following code segment:
       int bar(int n) {
          if (n == 1) return 0;
          else return 1 + bar(n/2);
       }
       int foo(int n) {
          if (n == 1) return 1;
          else return 1 + foo(bar(n));
       }

  The smallest positive integer n for which                                       returns      is    . (answer in integer)

  Note: Ignore syntax errors (if any) in the function.

### 69.9. Databases — GATE CSE 2026, Set 1, Question 33

**First appearance:** GATE CSE 2026, Set 1  
**Question type:** NAT  
**Marks:** 2  
**Source difficulty label:** unlabelled

Consider a relational database schema with two relations                                          and           .
  Let                                                                             be a tuple relational calculus expression.
  Which one of the following relational algebraic expressions is equivalent to                                ?

  A.                                                                                     B.
  C.                                                                                     D.

### 69.10. Compiler Design — GATE CSE 2021, Set 1, Question 26

**First appearance:** GATE CSE 2021, Set 1  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

Consider the following grammar (that admits a series of declarations, followed by expressions) and the
associated syntax directed translation     actions, given as pseudo-code

With respect to the above grammar, which one of the following choices is correct?

 A. The actions can be used to correctly type-check any syntactically correct program
 B. The actions can be used to type-check syntactically correct integer variable declarations and integer
    expressions
 C. The actions can be used to type-check syntactically correct boolean variable declarations and boolean
    expressions.
 D. The actions will lead to an infinite loop


## Week 70 — 10 questions

**Subject omitted this week:** Computer Networks

### 70.1. Compiler Design — GATE CSE 2026, Set 2, Question 19

**First appearance:** GATE CSE 2026, Set 2  
**Question type:** MSQ  
**Marks:** 1  
**Source difficulty label:** unlabelled

Which of the following grammars is/are ambiguous?

  A.                                                                                                 B.
  C.                                                                                                 D.

### 70.2. Programming and Data Structures — GATE CSE 2026, Set 1, Question 29

**First appearance:** GATE CSE 2026, Set 1  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

Consider the following code snippet in C language that computes the number of nodes in a non-empty singly
linked list pointed to by the pointer variable head.
      struct node{
         int elt;
         struct node *next;
      };
      int getListSize (struct node *head)
      {
         if( E1 ) return 1;
         return E2;
      }

Which one of the following options gives the correct replacements for the expressions           and

 A.            E1: head == NULL
               E2: 1 + getListSize(head)

 B.            E1: head->next == NULL
               E2: 1 + getListSize(head->next)

 C.            E1: head == NULL
               E2: 1 + getListSize(head->next)

 D.            E1: head->next == NULL
               E2: 1 + getListSize(head)

### 70.3. Engineering Mathematics — GATE CSE 2017, Set 1, Question 3

**First appearance:** GATE CSE 2017, Set 1  
**Question type:** MCQ  
**Marks:** 1  
**Source difficulty label:** medium

Let                       be scalars, not all zero, such that                                     = 0 where      are column vectors in       .

Consider the set of linear equations

where                                         and                           . The set of equations has

 A. a unique solution at                                   where          denotes a -dimensional vector of all 1.
 B. no solution
 C. infinitely many solutions
 D. finitely many solutions

### 70.4. Databases — GATE CSE 2019, Question 55

**First appearance:** GATE CSE 2019  
**Question type:** NAT  
**Marks:** 2  
**Source difficulty label:** unlabelled

Consider the following relations                                                              and        .

How many tuples will be returned by the following relational algebra query?

Answer: ________

### 70.5. Theory of Computation — GATE CSE 2020, Question 26

**First appearance:** GATE CSE 2020  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

Which of the following languages are undecidable? Note that                                                        indicates encoding of the Turing machine
  M.

  A.       ,      , and     only                                                                    B.           and     only
  C.           and     only                                                                         D.       ,      , and     only

### 70.6. Digital Logic — GATE CSE 2016, Set 1, Question 33

**First appearance:** GATE CSE 2016, Set 1  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** medium

Consider a carry look ahead adder for adding two -bit integers, built using gates of fan-in at most two. The
  time to perform addition using this adder is

  A.                                                                                     B.
  C.                                                                                     D.      )

### 70.7. Operating Systems — GATE CSE 2023, Question 47

**First appearance:** GATE CSE 2023  
**Question type:** NAT  
**Marks:** 2  
**Source difficulty label:** unlabelled

Consider the following two-dimensional array                                            in the         programming language, which is stored in row-
  major order:
         int D[128][128];

  Demand paging is used for allocating memory and each physical page frame holds         elements of the array
  The Least Recently Used           page-replacement policy is used by the operating system. A total of  physical
  page frames are allocated to a process which executes the following code snippet:
         for (int i = 0; i < 128; i++)
            for (int j = 0; j < 128; j++)
               D[j][i] *= 10;

  The number of page faults generated during the execution of this code snippet is _______________.

### 70.8. Computer Organization and Architecture — GATE CSE 2022, Question 23

**First appearance:** GATE CSE 2022  
**Question type:** NAT  
**Marks:** 1  
**Source difficulty label:** unlabelled

A cache memory that has a hit rate of         has an access latency            and miss penalty          An
optimization is done on the cache to reduce the miss rate. However, the optimization results in an increase
of cache access latency to         whereas the miss penalty is not affected. The minimum hit rate (rounded off to
two decimal places) needed after the optimization such that it should not increase the average memory access time
is _______________.

### 70.9. Algorithms — GATE CSE 2021, Set 1, Question 48

**First appearance:** GATE CSE 2021, Set 1  
**Question type:** NAT  
**Marks:** 2  
**Source difficulty label:** unlabelled

Consider the following                                    function:
      int SimpleFunction(int Y[], int n, int x)
      {
         int total = Y[0], loopIndex;
         for (loopIndex=1; loopIndex<=n-1; loopIndex++)
            total=x*total +Y[loopIndex];
         return total;
      }

Let          be an array of                       elements with                              , for all    such that               . The value returned by
                                               is __________

### 70.10. General Aptitude — GATE CSE 2016, Set 1, Question 02

**First appearance:** GATE CSE 2016, Set 1  
**Question type:** MCQ  
**Marks:** 1  
**Source difficulty label:** medium

A rewording of something written or spoken is a __________.

  A. paraphrase                                B. paradox                                 C. paradigm    D. paraffin


## Week 71 — 10 questions

**Subject omitted this week:** Algorithms

### 71.1. Operating Systems — GATE CSE 2020, Question 34

**First appearance:** GATE CSE 2020  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

Each of a set of processes executes the following code using two semaphores and initialized to and
 , respectively. Assume that     is a shared variable initialized to and not used in CODE SECTION P.

      wait(a); count=count+1;
      if (count==n) signal (b);
      signal (a); wait (b) ; signal (b);

What does the code achieve?

 A. It ensures that no process executes CODE SECTION Q before every process has finished CODE SECTION P.
 B. It ensures that atmost two processes are in CODE SECTION Q at any time.
 C. It ensures that all processes execute CODE SECTION P mutually exclusively.
 D. It ensures that at most        processes are in CODE SECTION P at any time.

### 71.2. Engineering Mathematics — GATE CSE 2017, Set 2, Question 21

**First appearance:** GATE CSE 2017, Set 2  
**Question type:** NAT  
**Marks:** 1  
**Source difficulty label:** medium

Consider                       the                       set                                      under           partial        ordering

The Hasse diagram of the partial order                                  is shown below.

  The minimum number of ordered pairs that need to be added to                                                       to make               a lattice is ______

### 71.3. Computer Organization and Architecture — GATE CSE 2019, Question 45

**First appearance:** GATE CSE 2019  
**Question type:** NAT  
**Marks:** 2  
**Source difficulty label:** unlabelled

A certain processor deploys a single-level cache. The cache block size is words and the word size is
bytes. The memory system uses a -MHz clock. To service a cache miss, the memory controller first takes
  cycle to accept the starting address of the block, it then takes cycles to fetch all the eight words of the block,
and finally transmits the words of the requested block at the rate of word per cycle. The maximum bandwidth for
the memory system when the program running on the processor issues a series of read operations is ______
       bytes/sec.

### 71.4. Theory of Computation — GATE CSE 2020, Question 7

**First appearance:** GATE CSE 2020  
**Question type:** MCQ  
**Marks:** 1  
**Source difficulty label:** medium

Which one of the following regular expressions represents the set of all binary strings with an odd number of
 s?

 A.                                                                                             B.
 C.                                                                                             D.

### 71.5. Digital Logic — GATE CSE 2017, Set 2, Question 12

**First appearance:** GATE CSE 2017, Set 2  
**Question type:** MCQ  
**Marks:** 1  
**Source difficulty label:** unlabelled

Given the following binary number in                                -bit (single precision)                                 format :

The decimal value closest to this floating-point number is :

 A.                                            B.                                             C.                                           D.

### 71.6. Compiler Design — GATE CSE 2016, Set 2, Question 46

**First appearance:** GATE CSE 2016, Set 2  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** medium

A student wrote two context-free grammars G1 and G2 for generating a single C-like array declaration. The
dimension of the array is at least one. For example,
                int a[10] [3];

The grammars use D as the start symbol, and use six terminal symbols int ; id [ ] num.

Which of the grammars correctly generate the declaration mentioned above?

 A. Both G1 and G2                             B. Only G1                           C. Only G2          D. Neither G1 nor G2

### 71.7. Databases — GATE CSE 2025, Set 2, Question 47

**First appearance:** GATE CSE 2025, Set 2  
**Question type:** NAT  
**Marks:** 2  
**Source difficulty label:** unlabelled

In a   - tree where each node can hold at most four key values, a root to leaf path consists of the following
nodes:

The -marked keys signify that these are data entries in a leaf.

Assume that a pointer between keys          and     points to a subtree containing keys in                             , and that when a leaf
is created, the smallest key in it is copied up into its parent.

A record with key value                      is inserted into the          - tree.

The smallest key value in the parent of the leaf that contains                             is __________. (Answer in integer)

### 71.8. General Aptitude — GATE CSE 2016, Set 2, Question 02

**First appearance:** GATE CSE 2016, Set 2  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** medium

Nobody knows how the Indian cricket team is going to cope with the difficult and seamer-friendly wickets in
  Australia.
  Choose the option which is closest in meaning to the underlined phrase in the above sentence.

  A. Put up with.                                                                           B. Put in with.
  C. Put down to.                                                                           D. Put up against.

### 71.9. Programming and Data Structures — GATE CSE 2016, Set 1, Question 10

**First appearance:** GATE CSE 2016, Set 1  
**Question type:** MCQ  
**Marks:** 1  
**Source difficulty label:** medium

A queue is implemented using an array such that ENQUEUE and DEQUEUE operations are performed
efficiently. Which one of the following statements is CORRECT ( refers to the number of items in the

queue) ?

 A. Both operations can be performed in       time.
 B. At most one operation can be performed in       time but the worst case time for the operation will be               .
 C. The worst case time complexity for both operations will be    .
 D. Worst case time complexity for both operations will be

### 71.10. Computer Networks — GATE CSE 2019, Question 49

**First appearance:** GATE CSE 2019  
**Question type:** NAT  
**Marks:** 2  
**Source difficulty label:** unlabelled

Consider that     machines need to be connected in a LAN using -port Ethernet switches. Assume that
  these switches do not have any separate uplink ports. The minimum number of switches needed is ______


## Week 72 — 10 questions

**Subject omitted this week:** Engineering Mathematics

### 72.1. Programming and Data Structures — GATE CSE 2017, Set 2, Question 43

**First appearance:** GATE CSE 2017, Set 2  
**Question type:** NAT  
**Marks:** 2  
**Source difficulty label:** unlabelled

Consider the following snippet of a C program. Assume that swap                                   exchanges the content of
and :
 int main () {
    int array[] = {3, 5, 1, 4, 6, 2};
    int done =0;
    int i;
    while (done==0) {
       done =1;
       for (i=0; i<=4; i++) {
           if (array[i] < array[i+1]) {
               swap(&array[i], &array[i+1]);
               done=0;
           }
       }
       for (i=5; i>=1; i--) {
           if (array[i] > array[i-1]) {
               swap(&array[i], &array[i-1]);
               done =0;
           }
       }
    }
    printf(“%d”, array[3]);
 }

The output of the program is _______

### 72.2. Operating Systems — GATE CSE 2024, Set 2, Question 54

**First appearance:** GATE CSE 2024, Set 2  
**Question type:** NAT  
**Marks:** 2  
**Source difficulty label:** unlabelled

Consider a      -bit system with          page size and page table entries of size bytes each. Assume
                 bytes. The OS uses a -level page table for memory management, with the page table
  containing an outer page directory and an inner page table. The             allocates a page for the outer page directory
  upon process creation. The        uses demand paging when allocating memory for the inner page table, i.e., a page
  of the inner page table is allocated only if it contains at least one valid page table entry.

  An active process in this system accesses         unique pages during its execution, and none of the pages are
  swapped out to disk. After it completes the page accesses, let denote the minimum and denote the maximum
  number of pages across the two levels of the page table of the process.

  The value of                     is ___________.

### 72.3. Digital Logic — GATE CSE 2024, Set 2, Question 4

**First appearance:** GATE CSE 2024, Set 2  
**Question type:** MCQ  
**Marks:** 1  
**Source difficulty label:** unlabelled

​The format of a single-precision floating-point number as per the                                                              standard is:

Choose the largest floating-point number among the following options.

A.

B.

C.

D.

### 72.4. Computer Networks — GATE CSE 2016, Set 2, Question 25

**First appearance:** GATE CSE 2016, Set 2  
**Question type:** MCQ  
**Marks:** 1  
**Source difficulty label:** medium

Identify the correct sequence in which the following packets are transmitted on the network by a host when a
browser requests a webpage from a remote server, assuming that the host has just been restarted.

 A. HTTP GET request, DNS query,                                                B. DNS query, HTTP GET request,
    TCP SYN                                                                        TCP SYN
 C. DNS query, TCP SYN, HTTP GET                                                D. TCP SYN, DNS query, HTTP GET
    request.                                                                       request.

### 72.5. Computer Organization and Architecture — GATE CSE 2017, Set 2, Question 53

**First appearance:** GATE CSE 2017, Set 2  
**Question type:** NAT  
**Marks:** 2  
**Source difficulty label:** unlabelled

Consider a machine with a byte addressable main memory of      bytes divided into blocks of size  bytes.
Assume that a direct mapped cache having      cache lines is used with this machine. The size of the tag
field in bits is _______

### 72.6. Algorithms — GATE CSE 2026, Set 2, Question 15

**First appearance:** GATE CSE 2026, Set 2  
**Question type:** MSQ  
**Marks:** 1  
**Source difficulty label:** unlabelled

Which of the following can be recurrence relation(s) corresponding to an algorithm with time complexity
    ?

 A.                                                                                      B.
 C.                                                                                      D.

### 72.7. Theory of Computation — GATE CSE 2021, Set 2, Question 36

**First appearance:** GATE CSE 2021, Set 2  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

​Consider the following two statements about regular languages:

           : Every infinite regular language contains an undecidable language as a subset.
           : Every finite language is regular.

  Which one of the following choices is correct?

  A. Only             is true                                                                       B. Only    is true
  C. Both             and     are true                                                              D. Neither     nor          is true

### 72.8. General Aptitude — GATE CSE 2024, Set 2, Question 7

**First appearance:** GATE CSE 2024, Set 2  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

A person sold two different items at the same price. He made                                             profit in one item, and   loss in the
  other item. In selling these two items, the person made a total of

  A.            profit                            B.           profit                     C.     loss                D.     loss

### 72.9. Compiler Design — GATE CSE 2024, Set 1, Question 23

**First appearance:** GATE CSE 2024, Set 1  
**Question type:** MSQ  
**Marks:** 1  
**Source difficulty label:** unlabelled

Consider the operator precedence and associativity rules for the integer arithmetic operators given in the
  table below.

  The value of the expression                                                                                   as per the above rules is ________.

### 72.10. Databases — GATE CSE 2017, Set 1, Question 41

**First appearance:** GATE CSE 2017, Set 1  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** medium

Consider a database that has the relation schemas EMP(EmpId, EmpName, DeptId), and DEPT(DeptName,
  DeptId). Note that the DeptId can be permitted to be NULL in the relation EMP. Consider the following
  queries on the database expressed in tuple relational calculus.

    I. { | u         EMP(t[EmpName] = u[EmpName]                    v   DEPT(t[DeptId]       v[DeptId]))}
   II. { | u         EMP(t[EmpName] = u[EmpName]                    v   DEPT(t[DeptId]       v[DeptId]))}
  III. { | u         EMP(t[EmpName] = u[EmpName]                    v   DEPT(t[DeptId]       v[DeptId]))}

  Which of the above queries are safe?

  A. I and II only                         B. I and III only            C. II and III only          D. I, II and III


## Week 73 — 10 questions

**Subject omitted this week:** Operating Systems

### 73.1. Computer Organization and Architecture — GATE CSE 2021, Set 1, Question 53

**First appearance:** GATE CSE 2021, Set 1  
**Question type:** NAT  
**Marks:** 2  
**Source difficulty label:** unlabelled

A five-stage pipeline has stage delays of                    and       nanoseconds. The registers that are
used between the pipeline stages have a delay of nanoseconds each.
The total time to execute     independent instructions on this pipeline, assuming there are no pipeline stalls, is
_______ nanoseconds.

### 73.2. Theory of Computation — GATE CSE 2026, Set 1, Question 41

**First appearance:** GATE CSE 2026, Set 1  
**Question type:** MSQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

Let         and           be two languages over a finite alphabet, such that                                                    and     are regular languages.

Which of the following statements is/are always true?

 A.        is regular                                                                              B.               is regular
 C.        is context-free                                                                         D.         is context-free

### 73.3. Engineering Mathematics — GATE CSE 2021, Set 2, Question 24

**First appearance:** GATE CSE 2021, Set 2  
**Question type:** MSQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

Suppose that                       is a              matrix such that every solution of the equation                                is a scalar multiple of
                                           . The rank of                 is __________

### 73.4. Algorithms — GATE CSE 2018, Question 30

**First appearance:** GATE CSE 2018  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** medium

Let   be a simple undirected graph. Let       be a depth first search tree of                                                 . Let      be a breadth first
search tree of . Consider the following statements.

  I. No edge of is a cross edge with respect to    . (A cross edge in is between two nodes neither of which is
     an ancestor of the other in  ).
 II. For every edge        of , if is at depth and is at depth in    , then          .

Which of the statements above must necessarily be true?

 A. I only                                    B. II only                                      C. Both I and II               D. Neither I nor II

### 73.5. Programming and Data Structures — GATE CSE 2023, Question 37

**First appearance:** GATE CSE 2023  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

Consider the                function               and the binary tree shown.
      typedef struct node {
         int val;
         struct node *left, *right;
      } node;

      int foo(node *p) {
         int retval;
         if (p == NULL)
             return 0;
         else {
             retval = p->val + foo(p->left) + foo(p->right);
             printf("%d ", retval);
             return retval;
         }
      }

When                 is called with a pointer to the root node of the given binary tree, what will it print?

 A.                                                                                           B.
 C.                                                                                           D.

### 73.6. Databases — GATE CSE 2020, Question 54

**First appearance:** GATE CSE 2020  
**Question type:** NAT  
**Marks:** 2  
**Source difficulty label:** unlabelled

Consider a database implemented using          tree for file indexing and installed on a disk drive with block
size of       . The size of search key is          and the size of tree/disk pointer is        . Assume that
the database has one million records. Also assume that no node of the        tree and no records are present initially
in main memory. Consider that each record fits into one disk block. The minimum number of disk accesses required
to retrieve any record in the database is _______

### 73.7. Digital Logic — GATE CSE 2017, Set 1, Question 7

**First appearance:** GATE CSE 2017, Set 1  
**Question type:** MCQ  
**Marks:** 1  
**Source difficulty label:** unlabelled

The n-bit fixed-point representation of an unsigned real number       uses                                                      bits for the fraction part. Let
            . The range of decimal values for in this representation is

  A.      to                                                                                            B.      to
  C. 0 to                                                                                               D. 0 to

### 73.8. Computer Networks — GATE CSE 2022, Question 49

**First appearance:** GATE CSE 2022  
**Question type:** NAT  
**Marks:** 2  
**Source difficulty label:** unlabelled

Consider a                       link between an earth station (sender) and a satellite (receiver) at an altitude of
                         The    signal    propagates    at  a    speed    of                   The      time     taken
                                                                     for the receiver to completely receive a packet of
                       transmitted by the sender is _______________.

### 73.9. General Aptitude — GATE CSE 2021, Set 1, Question 10

**First appearance:** GATE CSE 2021, Set 1  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

Some people suggest anti-obesity measures        such as displaying calorie information in restaurant
menus. Such measures sidestep addressing the core problems that cause obesity: poverty and income
inequality.
Which one of the following statements summarizes the passage?

 A. The proposed          addresses the core problems that cause obesity
 B. If obesity reduces, poverty will naturally reduce, since obesity causes poverty
 C.         are addressing the core problems and are likely to succeed
 D.         are addressing the problem superficially

### 73.10. Compiler Design — GATE CSE 2025, Set 1, Question 42

**First appearance:** GATE CSE 2025, Set 1  
**Question type:** NAT  
**Marks:** 2  
**Source difficulty label:** easy

Refer to the given -address code sequence. This code sequence is split into basic blocks. The number of
  basic blocks is ________. (Answer in integer)
         1001: i = 1
         1002: j = 1
         1003: t1 = 10*i
         1004: t2 = t1+j
         1005: t3 = 8*t2
         1006: t4 = t3-88
         1007: a[t4] = 0.0
         1008: j = j+1
         1009: if j <= 10 goto 1003
         1010: i = i+1
         1011: if i <= 10 goto 1002
         1012: i = 1
         1013: t5 = i-1
         1014: t6 = 88*t5
         1015: a[t6] = 1.0
         1016: i = i+1
         1017: if i <= 10 goto 1013


## Week 74 — 10 questions

**Subject omitted this week:** Algorithms

### 74.1. Programming and Data Structures — GATE CSE 2025, Set 1, Question 16

**First appearance:** GATE CSE 2025, Set 1  
**Question type:** MSQ  
**Marks:** 1  
**Source difficulty label:** unlabelled

Which of the following statement(s) is/are TRUE for any binary search tree (BST) having                      distinct integers?

 A. The maximum length of a path from the root node to any other node is                                 .
 B. An inorder traversal will always produce a sorted sequence of elements.
 C. Finding an element takes              time in the worst case.
 D. Every BST is also a Min-Heap.

### 74.2. Computer Networks — GATE CSE 2026, Set 2, Question 12

**First appearance:** GATE CSE 2026, Set 2  
**Question type:** MCQ  
**Marks:** 1  
**Source difficulty label:** unlabelled

Which one of the following protocols may need to broadcast some of its messages?

 A.                                          B.                                               C.                     D.

### 74.3. Operating Systems — GATE CSE 2023, Question 48

**First appearance:** GATE CSE 2023  
**Question type:** NAT  
**Marks:** 2  
**Source difficulty label:** unlabelled

Consider a computer system with -bit virtual addressing using multi-level tree-structured page tables with
   levels for virtual to physical address translation. The page size is                       and a page
table entry at any of the levels occupies bytes.

The value of             is ______________.

### 74.4. Computer Organization and Architecture — GATE CSE 2016, Set 1, Question 31

**First appearance:** GATE CSE 2016, Set 1  
**Question type:** NAT  
**Marks:** 2  
**Source difficulty label:** medium

The size of the data count register of a       controller is        . The processor needs to transfer a file of
         kilobytes from disk to main memory. The memory is byte addressable. The minimum number of
times the        controller needs to get the control of the system bus from the processor to transfer the file from the
disk to main memory is _________.

### 74.5. General Aptitude — GATE CSE 2025, Set 2, Question 7

**First appearance:** GATE CSE 2025, Set 2  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

If IMAGE and FIELD are coded as FHBNJ and EMFJG respectively then, which one among the given
  options is the most appropriate code for BEACH?

  A. CEADP                                      B. IDBFC                                    C. JGIBC                  D. IBCEC

### 74.6. Engineering Mathematics — GATE CSE 2022, Question 41

**First appearance:** GATE CSE 2022  
**Question type:** MSQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

Consider the following recurrence:

Then, which of the following statements is/are

 A.                                                                                         B.
 C.                                                                                         D.

### 74.7. Digital Logic — GATE CSE 2026, Set 2, Question 30

**First appearance:** GATE CSE 2026, Set 2  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

Consider the following -variable Boolean function

Consider     as MSB,    as LSB. Which one of the following options represents the minimal sum of products form
for the above function?
Note:          is OR operation, is AND operation, is NOT operation

 A.                                                                                               B.
 C.                                                                                               D.

### 74.8. Databases — GATE CSE 2022, Question 21

**First appearance:** GATE CSE 2022  
**Question type:** NAT  
**Marks:** 1  
**Source difficulty label:** unlabelled

Consider a relation                                                  with the following three functional dependencies.

The number of superkeys in the relation                                        is ______________ .

### 74.9. Theory of Computation — GATE CSE 2026, Set 1, Question 15

**First appearance:** GATE CSE 2026, Set 1  
**Question type:** MSQ  
**Marks:** 1  
**Source difficulty label:** unlabelled

Consider the following grammar where                                is the start symbol, and           and   are terminal symbols.

  Which of the following statements is/are true?

  A. The grammar is ambiguous
  B. The string   has two distinct derivations in this grammar
  C. The string     has only one rightmost derivation
  D. The language generated by the grammar is undecidable

### 74.10. Compiler Design — GATE CSE 2026, Set 2, Question 35

**First appearance:** GATE CSE 2026, Set 2  
**Question type:** NAT  
**Marks:** 2  
**Source difficulty label:** unlabelled

Consider the control flow graph given below.

  Which one of the following options is the set of live variables at the exit point of each basic block?

  A.
  B.           :    ,      :     d, e ,            :       a, c, f ,        :
  C.
  D.


## Week 75 — 10 questions

**Subject omitted this week:** Operating Systems

### 75.1. Algorithms — GATE CSE 2021, Set 1, Question 40

**First appearance:** GATE CSE 2021, Set 1  
**Question type:** MSQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

Define     to be the maximum amount earned by cutting a rod of length meters into one or more pieces of
integer length and selling them. For  , let   denote the selling price of a rod whose length is meters.
Consider the array of prices:

Which of the following statements is/are correct about                                    ?

 A.
 B.
 C.         is achieved by three different solutions
 D.         cannot be achieved by a solution consisting of three pieces

### 75.2. General Aptitude — GATE CSE 2026, Set 1, Question 9

**First appearance:** GATE CSE 2026, Set 1  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

In the       summer Olympics' Javelin throw finals, Neeraj Chopra exhibited a spectacular performance to
win the gold medal. The silver medal was won by Jakub Vadlejch and the bronze medal was won by Vitezlav
Vesely. There were six rounds of throws with each athlete having one throw per round. The best of all the throws of
each athlete is considered for the medal. Following were the observations about the throws:

   i. The first and second rounds were dominated by Neeraj Chopra with a gold medal performance in his second
      throw, while the other two athletes did not have any medal winning throws in these rounds.
  ii. The throws in the last round by both Jakub Vadlejch and Vitezlav Vesely were fouls and were not considered for
      scoring.
 iii. After four rounds, Vitezlav Vesely was in the second position and could not improve upon his best throw in the
      succeeding rounds.
 iv. In the fourth round, the throw by Jakub Vadlejch was the best in that round.

In which round did Vitezlav Vesely have his best throw?

 A. Third                                      B. Fourth                                 C. Fifth                    D. Sixth

### 75.3. Computer Networks — GATE CSE 2016, Set 2, Question 55

**First appearance:** GATE CSE 2016, Set 2  
**Question type:** NAT  
**Marks:** 2  
**Source difficulty label:** medium

Consider a               bits/second satellite communication link with one way propagation delay of
milliseconds. Selective retransmission (repeat) protocol is used on this link to send data with a frame size of
  kilobyte. Neglect the transmission time of acknowledgement. The minimum number of bits required for the
sequence number field to achieve          utilization is ________.

### 75.4. Engineering Mathematics — GATE CSE 2016, Set 1, Question 29

**First appearance:** GATE CSE 2016, Set 1  
**Question type:** NAT  
**Marks:** 2  
**Source difficulty label:** medium

Consider the following experiment.
Step 1. Flip a fair coin twice.
Step 2. If the outcomes are (TAILS, HEADS) then output                and stop.
Step 3. If the outcomes are either (HEADS, HEADS) or (HEADS, TAILS), then output                       and stop.
Step 4. If the outcomes are (TAILS, TAILS), then go to Step
The probability that the output of the experiment is            is (up to two decimal places)

### 75.5. Databases — GATE CSE 2022, Question 46

**First appearance:** GATE CSE 2022  
**Question type:** NAT  
**Marks:** 2  
**Source difficulty label:** unlabelled

Consider the relational database with the following four schemas and their respective instances.

      Student(sNo, sName, dNo) Dept(dNo, dName)
      Course(cNo, cName, dNo) Register(sNo, cNo)

      SELECT * FROM Student AS S WHERE NOT EXIST

        (SELECT cNo FROM Course WHERE dNo = “D01”

                EXCEPT

         SELECT cNo FROM Register WHERE sNo = S.sNo)

The number of rows returned by the above                                      query is ____________.

### 75.6. Programming and Data Structures — GATE CSE 2019, Question 27

**First appearance:** GATE CSE 2019  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

Consider the following C program:
       #include <stdio.h>
       int r() {
             static int num=7;
             return num--;
       }
       int main() {
             for (r();r();r())
                  printf(“%d”,r());
             return 0;
       }

Which one of the following values will be displayed on execution of the programs?

 A.                                           B.                                        C.      D.

### 75.7. Theory of Computation — GATE CSE 2016, Set 1, Question 18

**First appearance:** GATE CSE 2016, Set 1  
**Question type:** MCQ  
**Marks:** 1  
**Source difficulty label:** medium

Which one of the following regular expressions represents the language: the set of all binary strings having
two consecutive 's and two consecutive 's?

 A.                                                                                             B.
 C.                                                                                             D.

### 75.8. Compiler Design — GATE CSE 2017, Set 1, Question 52

**First appearance:** GATE CSE 2017, Set 1  
**Question type:** NAT  
**Marks:** 2  
**Source difficulty label:** medium

Consider the expression                               . Let     be the minimum number of registers required
  by an optimal code generation (without any register spill) algorithm for a load/store architecture, in which

  A. only load and store instructions can have memory operands and
  B. arithmetic instructions can have only register or immediate operands.

  ​The value of            is _____________ .

### 75.9. Computer Organization and Architecture — GATE CSE 2026, Set 2, Question 34

**First appearance:** GATE CSE 2026, Set 2  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

Consider a processor that has      general purpose registers and it uses -byte instruction format for all its
instructions. Variable-sized opcodes are permitted. There are three different types of instructions; M-type, R-
type, and C-type. Each M-type instruction has register operands and a -bit immediate operand. Each Rtype
instruction has register operands. Each C-type instruction has a register operand and a -bit offset value. If there
are unique M-type opcodes and unique R-type opcodes, which one of the following options gives the maximum

  number of unique opcodes possible for C-type instructions?

  A.                                             B.                                             C.                              D.

### 75.10. Digital Logic — GATE CSE 2026, Set 1, Question 27

**First appearance:** GATE CSE 2026, Set 1  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

Consider a -bit saturating up/down counter that performs the saturating up count when the input   is ,
and the saturating down count when is . The Next State table of the counter is as shown. The counter is
built as a synchronous sequential circuit using D flip-flops.
 Input          Cureent State                      Next State

Which one of the following options corresponds to the expressions for the inputs of the D flip-flops,                                             and   ?

 A.
 B.
 C.
 D.


## Week 76 — 10 questions

**Subject omitted this week:** Digital Logic

### 76.1. Algorithms — GATE CSE 2017, Set 2, Question 30

**First appearance:** GATE CSE 2017, Set 2  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

Consider the recurrence function

Then                 in terms of          notation is

 A.                                                                                               B.

 C.                                                                                           D.

### 76.2. Engineering Mathematics — GATE CSE 2021, Set 1, Question 54

**First appearance:** GATE CSE 2021, Set 1  
**Question type:** MSQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

A sender       transmits a signal, which can be one of the two kinds:                                         and   with probabilities      and
  respectively, to a receiver    .
  In the graph below, the weight of edge           is the probability of receiving when is transmitted, where
                . For example, the probability that the received signal is given the transmitted signal was , is
      .

  If the received signal is                         the probability that the transmitted signal was                      (rounded to       decimal places) is
  __________.

### 76.3. Computer Organization and Architecture — GATE CSE 2018, Question 34

**First appearance:** GATE CSE 2018  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** medium

The size of the physical address space of a processor is          bytes. The word length is   bytes. The
capacity of cache memory is       bytes. The size of each cache block is         words. For a -way set-
associative cache memory, the length (in number of bits) of the tag field is

 A.                                                                                      B.
 C.                                                                                      D.

### 76.4. Theory of Computation — GATE CSE 2026, Set 1, Question 16

**First appearance:** GATE CSE 2026, Set 1  
**Question type:** MSQ  
**Marks:** 1  
**Source difficulty label:** unlabelled

Let           be a nondeterministic finite automaton (NFA) with                                         states over a finite alphabet.

  Which of the following options CANNOT be the number of states in the minimal deterministic finite automaton
  (DFA) that is equivalent to  ?

  A.                                           B.                                                C.                              D.

### 76.5. Programming and Data Structures — GATE CSE 2018, Question 2

**First appearance:** GATE CSE 2018  
**Question type:** MCQ  
**Marks:** 1  
**Source difficulty label:** medium

Consider the following C program:
 #include<stdio.h>
 struct Ournode{
    char x, y, z;
 };
 int main() {
    struct Ournode p={'1', '0', 'a'+2};
    struct Ournode *q=&p;
    printf("%c, %c", *((char*)q+1), *((char*)q+2));
    return 0;
 }

The output of this program is:

 A. 0, c                                    B. 0, a+2                                  C. '0', 'a+2'   D. '0', 'c'

### 76.6. Databases — GATE CSE 2016, Set 2, Question 52

**First appearance:** GATE CSE 2016, Set 2  
**Question type:** NAT  
**Marks:** 2  
**Source difficulty label:** medium

Consider the following database table named water_schemes:

The number of tuples returned by the following SQL query is _________.
 with total (name, capacity) as
    select district_name, sum (capacity)
    from water_schemes
    group by district_name
 with total_avg (capacity) as
   select avg (capacity)
   from total
 select name
   from total, total_avg
   where total.capacity $\ge$ total_avg.capacity

### 76.7. Operating Systems — GATE CSE 2017, Set 1, Question 27

**First appearance:** GATE CSE 2017, Set 1  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** medium

A multithreaded program         executes with number of threads and uses number of locks for ensuring
mutual exclusion while operating on shared memory locations. All locks in the program are non-reentrant,
i.e., if a thread holds a lock , then it cannot re-acquire lock without releasing it. If a thread is unable to acquire a
lock, it blocks until the lock becomes available. The minimum value of and the minimum value of together for
which execution of can result in a deadlock are:

 A.                                                                                      B.
 C.                                                                                      D.

### 76.8. Computer Networks — GATE CSE 2023, Question 42

**First appearance:** GATE CSE 2023  
**Question type:** MSQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

Suppose in a web browser, you click on the                              . The browser cache is empty. The
              for this     is not cached in your local host, so a       lookup is triggered (by the local
  server deployed on your local host) over the -tier        hierarchy in an iterative mode. No resource records are
  cached anywhere across all       servers.

  Let         denote the round trip time between your local host and        servers in the        hierarchy. The round
  trip time between the local host and the web server hosting                      is also equal to      . The
  file associated with the        is small enough to have negligible transmission time and negligible rendering time by

  your web browser, which references                                  equally small objects on the same web server.

  Which of the following statements is/are                                                 about the minimum elapsed time between clicking on the
       and your browser fully rendering it?

  A.                  s, in case of non-persistent                             with parallel           connections.
  B.                  s, in case of persistent                            with pipelining.
  C.                  s, in case of non-persistent                             with parallel           connections.
  D.                  s, in case of persistent                            with pipelining.

### 76.9. General Aptitude — GATE CSE 2025, Set 2, Question 6

**First appearance:** GATE CSE 2025, Set 2  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

​Based only on the conversation below, identify the logically correct inference:
   "Even if I had known that you were in the hospital, I would not have gone there to see you", Ramya told
   Josephine.

  A. Ramya knew that Josephine was in the hospital.
  B. Ramya did not know that Josephine was in the hospital.
  C. Ramya and Josephine were once close friends; but now, they are not.
  D. Josephine was in the hospital due to an injury to her leg.

### 76.10. Compiler Design — GATE CSE 2023, Question 26

**First appearance:** GATE CSE 2023  
**Question type:** NAT  
**Marks:** 2  
**Source difficulty label:** unlabelled

Consider the following program:

                                                                                               int f2 (int X)
        int main()                                                                             {
        {                                                                                         f3();
                                                          int f1 ()                                                                    int f3 ()
           f1 ();                                                                                 if (X==1);
                                                          {                                                                            {
           f2(2);                                                                                     return f1 ();
                                                             return(1);                                                                   return (5);
           f3();                                                                                  else
                                                          }                                                                            }
           return (0);                                                                                return (X * f2 (X - 1));
        }
                                                                                               }

  Which one of the following options represents the activation tree corresponding to the main function?

                                                                                        B.
  A.

                                                                                        D.
  C.


## Week 77 — 10 questions

**Subject omitted this week:** Computer Networks

### 77.1. Databases — GATE CSE 2024, Set 1, Question 11

**First appearance:** GATE CSE 2024, Set 1  
**Question type:** MCQ  
**Marks:** 1  
**Source difficulty label:** unlabelled

In a      tree, the requirement of at least half-full                      node occupancy is relaxed for which one of the
following cases?

 A. Only the root node                                                   B. All leaf nodes
 C. All internal nodes                                                   D. Only the leftmost leaf node

### 77.2. Algorithms — GATE CSE 2025, Set 2, Question 49

**First appearance:** GATE CSE 2025, Set 2  
**Question type:** NAT  
**Marks:** 2  
**Source difficulty label:** unlabelled

Consider the following algorithm someAlgo that takes an undirected graph                                               as input.
                                               someAlgo (G)

 1. Let be any vertex in . Run BFS on         starting at . Let be a vertex in   at maximum distance from as
    given by the BFS.
 2. Run BFS on      again with as the starting vertex. Let be the vertex at maximum distance from as given by
    the BFS.
 3. Output the distance between and in .

The output of someAlgo (                             ) for the tree shown in the given figure is ___________. (Answer in integer)

### 77.3. Programming and Data Structures — GATE CSE 2021, Set 1, Question 41

**First appearance:** GATE CSE 2021, Set 1  
**Question type:** MSQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

An                      in a connected graph is a vertex such that removing the vertex and its incident
edges disconnects the graph into two or more connected components.
Let       be a              tree obtained by doing                            in a connected undirected graph       .
Which of the following options is/are correct?

 A. Root of can never be an articulation point in .
 B. Root of is an articulation point in if and only if it has or more children.
 C. A leaf of can be an articulation point in .
 D. If is an articulation point in such that is an ancestor of in and is a descendent of                                                   in   , then all
    paths from to in must pass through .

### 77.4. Digital Logic — GATE CSE 2018, Question 49

**First appearance:** GATE CSE 2018  
**Question type:** NAT  
**Marks:** 2  
**Source difficulty label:** unlabelled

Consider the minterm list form of a Boolean function                                            given below.

Here,    denotes a minterm and                                  denotes a don't care term. The number of essential prime implicants of the
function is ___

### 77.5. Operating Systems — GATE CSE 2019, Question 17

**First appearance:** GATE CSE 2019  
**Question type:** NAT  
**Marks:** 1  
**Source difficulty label:** unlabelled

The following C program is executed on a Unix/Linux system :
       #include<unistd.h>
       int main()

      {
          int i;
          for(i=0; i<10; i++)
             if(i%2 == 0)
                 fork();
          return 0;
      }

The total number of child processes created is ________________ .

### 77.6. Computer Organization and Architecture — GATE CSE 2021, Set 1, Question 55

**First appearance:** GATE CSE 2021, Set 1  
**Question type:** NAT  
**Marks:** 2  
**Source difficulty label:** unlabelled

Consider the following instruction sequence where registers                                                    and     are general purpose and
                denotes the content at the memory location

Assume that the content of the memory location            is , and the content of the register     is     . The
content of each of the memory locations from            to       is . The instruction sequence starts from the
memory location      . All the numbers are in decimal format. Assume that the memory is byte addressable.

After the execution of the program, the content of memory location                                             is ____________

### 77.7. General Aptitude — GATE CSE 2026, Set 1, Question 10

**First appearance:** GATE CSE 2026, Set 1  
**Question type:** MCQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

An unbiased six-faced dice whose faces are marked with numbers                       , and is rolled twice in
succession and the number on the top face is recorded each time. The probability that the number
appearing in the second roll is an integer multiple of the number appearing in the first roll is

 A.                                            B.                                        C.                               D.

### 77.8. Engineering Mathematics — GATE CSE 2022, Question 43

**First appearance:** GATE CSE 2022  
**Question type:** MSQ  
**Marks:** 2  
**Source difficulty label:** unlabelled

​Which of the following is/are the eigenvector(s) for the matrix given below?

 A.                                                                                              B.

 C.                                                                                              D.

### 77.9. Theory of Computation — GATE CSE 2020, Question 8

**First appearance:** GATE CSE 2020  
**Question type:** MCQ  
**Marks:** 1  
**Source difficulty label:** unlabelled

Consider the following statements.

  I. If         is regular, then both     and    must be regular.
 II. The class of regular languages is closed under infinite union.

Which of the above statements is/are TRUE?

 A. I only                                                                                             B. II only
 C. Both I and II                                                                                       D. Neither I nor II

### 77.10. Compiler Design — GATE CSE 2026, Set 2, Question 25

**First appearance:** GATE CSE 2026, Set 2  
**Question type:** NAT  
**Marks:** 1  
**Source difficulty label:** unlabelled

A lexical analyzer uses the following token definitions

  For the string given below,

  the number of tokens (excluding ws) that will be produced by the lexical analyzer is                           . (answer in integer)
