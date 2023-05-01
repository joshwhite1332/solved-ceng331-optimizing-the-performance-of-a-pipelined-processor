Download Link: https://assignmentchef.com/product/solved-ceng331-optimizing-the-performance-of-a-pipelined-processor
<br>
<h1>1          Introduction</h1>

In this homework, you will learn about the design and implementation of a pipelined Y86-64 processor, optimizing both it and a benchmark program to maximize performance. You are allowed to make any semantics-preserving transformation to the benchmark program, or to make enhancements to the pipelined processor, or both. When you have completed the homework, you will have a keen appreciation for the interactions between code and hardware that affect the performance of your programs.

The homework is organized into three parts, each with its own handin. In Part A you will write some simple Y86-64 programs and become familiar with the Y86-64 tools. In Part B, you will extend the SEQ simulator with a new instruction. These two parts will prepare you for Part C, the heart of the homework, where you will optimize the Y86-64 benchmark program and the processor design.

<h1>2          Logistics</h1>

You will work on this homework alone.

Any clarifications and revisions to the assignment will be posted on Piazza course page.

<h1>3          Handout Instructions</h1>

<ol>

 <li>Start by copying the file archlab-handout.tar to a (protected) directory in which you plan to do your work.</li>

 <li>Then give the command: tar xvf archlab-handout.tar. This will cause the following files to be unpacked into the directory: README, Makefile, sim.tar, archlab.pdf, and simguide.pdf.</li>

 <li>Next, give the command tar xvf sim.tar. This will create the directory sim, which contains the Y86-64 tools. You will be doing all of your work inside this directory.</li>

 <li>Finally, change to the sim directory and build the Y86-64 tools:</li>

</ol>

unix&gt; <em>cd sim </em>unix&gt; <em>make clean; make</em>

<h1>4          Part A</h1>

You will be working in directory sim/misc in this part.

Your task is to write and simulate the following three Y86-64 programs. The required behavior of these programs is defined by the example C functions in examples.c. Be sure to put your name and ID in a comment at the beginning of each program. You can test your programs by first assemblying them with the program YAS and then running them with the instruction set simulator YIS.

In all of your Y86-64 functions, you should follow the x86-64 conventions for passing function arguments, using registers, and using the stack. This includes saving and restoring any callee-save registers that you use.

<h2><strong>sum.ys</strong>: Iteratively sum linked list elements</h2>

Write a Y86-64 program sum.ys that iteratively sums the elements of a linked list. Your program should consist of some code that sets up the stack structure, invokes a function, and then halts. In this case, the function should be Y86-64 code for a function (sumlist) that is functionally equivalent to the C sumlist function in Figure 1. Test your program using the following three-element list:

# Sample linked list

.align 8 ele1:

.quad 0x00a

.quad ele2 ele2:

.quad 0x0b0

.quad ele3 ele3:

.quad 0xc00

.quad 0

<h2><strong>rsum.ys</strong>: Recursively sum linked list elements</h2>

Write a Y86-64 program rsum.ys that recursively sums the elements of a linked list. This code should be similar to the code in sum.ys, except that it should use a function rsumlist that recursively sums a list of numbers, as shown with the C function rsumlist in Figure 1. Test your program using the same three-element list you used for testing list.ys.

<ul>

 <li>/* linked list element */</li>

 <li>typedef struct ELE {</li>

 <li>long val;</li>

 <li>struct ELE *next; 5 } *list_ptr;</li>

</ul>

6

<ul>

 <li>/* sum_list – Sum the elements of a linked list */</li>

 <li>long sum_list(list_ptr ls)</li>

 <li>{</li>

 <li>long val = 0;</li>

 <li>while (ls) {</li>

 <li>val += ls-&gt;val;</li>

 <li>ls = ls-&gt;next;</li>

 <li>}</li>

 <li>return val;</li>

 <li>}</li>

</ul>

17

<ul>

 <li>/* rsum_list – Recursive version of sum_list */</li>

 <li>long rsum_list(list_ptr ls)</li>

 <li>{</li>

 <li>if (!ls)</li>

 <li>return 0;</li>

 <li>else {</li>

 <li>long val = ls-&gt;val;</li>

 <li>long rest = rsum_list(ls-&gt;next);</li>

 <li>return val + rest;</li>

 <li>}</li>

 <li>}</li>

</ul>

29

<ul>

 <li>/* copy_block – Copy src to dest and return xor checksum of src */</li>

 <li>long copy_block(long *src, long *dest, long len)</li>

 <li>{</li>

 <li>long result = 0;</li>

 <li>while (len &gt; 0) {</li>

 <li>long val = *src++;</li>

 <li>*dest++ = val;</li>

 <li>result ˆ= val;</li>

 <li>len–;</li>

 <li>}</li>

 <li>return result;</li>

 <li>}</li>

</ul>

Figure 1: <strong>C versions of the Y86-64 solution functions. </strong>Seesim/misc/examples.c

<h2><strong>copy.ys</strong>: Copy a source block to a destination block</h2>

Write a program (copy.ys) that copies a block of words from one part of memory to another (nonoverlapping area) area of memory, computing the checksum (Xor) of all the words copied.

Your program should consist of code that sets up a stack frame, invokes a function copyblock, and then halts. The function should be functionally equivalent to the C function copyblock shown in Figure Figure 1. Test your program using the following three-element source and destination blocks:

.align 8 # Source block src:

.quad 0x00a

.quad 0x0b0

.quad 0xc00

# Destination block dest:

.quad 0x111

.quad 0x222

.quad 0x333

<h1>5          Part B</h1>

You will be working in directory sim/seq in this part.

Your task in Part B is to extend the SEQ processor to support the iaddq, described in Homework problems 4.51 and 4.52. To add this instructions, you will modify the file seq-full.hcl, which implements the version of SEQ described in the CS:APP3e textbook. In addition, it contains declarations of some constants that you will need for your solution.

Your HCL file must begin with a header comment containing the following information:

<ul>

 <li>Your name and ID.</li>

 <li>A description of the computations required for the iaddq instruction. Use the descriptions of irmovq and OPq in Figure 4.18 in the CS:APP3e text as a guide.</li>

</ul>

<h2>Building and Testing Your Solution</h2>

Once you have finished modifying the seq-full.hcl file, then you will need to build a new instance of the SEQ simulator (ssim) based on this HCL file, and then test it:

<ul>

 <li><em>Building a new simulator. </em>You can use make to build a new SEQ simulator:</li>

</ul>

unix&gt; <em>make VERSION=full</em>

This builds a version of ssim that uses the control logic you specified in seq-full.hcl. To save typing, you can assign VERSION=full in the Makefile.

<ul>

 <li><em>Testing your solution on a simple Y86-64 program. </em>For your initial testing, we recommend running simple programs such as asumi.yo (testing iaddq) in TTY mode, comparing the results against the ISA simulation:</li>

</ul>

unix&gt; <em>./ssim -t ../y86-code/asumi.yo</em>

If the ISA test fails, then you should debug your implementation by single stepping the simulator in GUI mode: unix&gt; <em>./ssim -g ../y86-code/asumi.yo</em>

<ul>

 <li><em>Retesting your solution using the benchmark programs. </em>Once your simulator is able to correctly execute small programs, then you can automatically test it on the Y86-64 benchmark programs in ../y86-code: unix&gt; <em>(cd ../y86-code; make testssim)</em></li>

</ul>

This will run ssim on the benchmark programs and check for correctness by comparing the resulting processor state with the state from a high-level ISA simulation. Note that none of these programs test the added instructions. You are simply making sure that your solution did not inject errors for the original instructions. See file ../y86-code/README file for more details.

<ul>

 <li><em>Performing regression tests. </em>Once you can execute the benchmark programs correctly, then you should run the extensive set of regression tests in ../ptest. To test everything except iaddq and leave: unix&gt; <em>(cd ../ptest; make SIM=../seq/ssim)</em></li>

</ul>

To test your implementation of iaddq: unix&gt; <em>(cd ../ptest; make SIM=../seq/ssim TFLAGS=-i)</em>

For more information on the SEQ simulator refer to the handout <em>CS:APP3e Guide to Y86-64 Processor Simulators </em>(simguide.pdf).

<h1>6          Part C</h1>

You will be working in directory sim/pipe in this part.

The ncopy function in Figure 2 copies a len-element integer array src to a non-overlapping dst, returning a count of the number of positive integers contained in src. Figure 3 shows the baseline Y86-64 version of ncopy. The file pipe-full.hcl contains a copy of the HCL code for PIPE, along with a declaration of the constant value IIADDQ.

<ul>

 <li>/*</li>

 <li>* ncopy – copy src to dst, returning number of positive ints 3 * contained in src array.</li>

 <li>*/</li>

 <li>word_t ncopy(word_t *src, word_t *dst, word_t len)</li>

 <li>{</li>

 <li>word_t count = 0;</li>

 <li>word_t val;</li>

</ul>

9

<ul>

 <li>while (len &gt; 0) {</li>

 <li>val = *src++;</li>

 <li>*dst++ = val;</li>

 <li>if (val &gt; 0)</li>

 <li>count++;</li>

 <li>len–;</li>

 <li>}</li>

 <li>return count;</li>

 <li>}</li>

</ul>

Figure 2: <strong>C version of the ncopy function. </strong>Seesim/pipe/ncopy.c.

Your task in Part C is to modify ncopy.ys and pipe-full.hcl with the goal of making ncopy.ys run as fast as possible.

You will be handing in two files: pipe-full.hcl and ncopy.ys. Each file should begin with a header comment with the following information:

<ul>

 <li>Your name and ID.</li>

 <li>A high-level description of your code. In each case, describe how and why you modified your code.</li>

</ul>

<h2>Coding Rules</h2>

You are free to make any modifications you wish, with the following constraints:

<ul>

 <li>Your ncopy.ys function must work for arbitrary array sizes. You might be tempted to hardwire your solution for 64-element arrays by simply coding 64 copy instructions, but this would be a bad idea because we will be grading your solution based on its performance on arbitrary arrays.</li>

 <li>Your ncopy.ys function must run correctly with YIS. By correctly, we mean that it must correctly copy the src block <em>and </em>return (in %rax) the correct number of positive integers.</li>

 <li>The assembled version of your ncopy file must not be more than 1000 bytes long. You can check the length of any program with the ncopy function embedded using the provided script check-len.pl:</li>

</ul>

unix&gt; <em>./check-len.pl &lt; ncopy.yo</em>

1 ################################################################## 2 # ncopy.ys – Copy a src block of len words to dst.

<ul>

 <li># Return the number of positive words (&gt;0) contained in src.</li>

 <li>#</li>

 <li># Include your name and ID here.</li>

 <li>#</li>

 <li># Describe how and why you modified the baseline code.</li>

 <li>#</li>

 <li>##################################################################</li>

 <li># Do not modify this portion 11 # Function prologue.</li>

</ul>

12 # %rdi = src, %rsi = dst, %rdx = len 13 ncopy:

14

<ul>

 <li>##################################################################</li>

 <li># You can modify this portion</li>

 <li># Loop header</li>

 <li>xorq %rax,%rax # count = 0; 19      andq %rdx,%rdx   # len &lt;= 0?</li>

</ul>

20                                jle Done                                                      # if so, goto Done:

21

22 Loop:             mrmovq (%rdi), %r10         # read val from src… 23      rmmovq %r10, (%rsi)            # …and store it to dst 24    andq %r10, %r10 # val &lt;= 0?

<ul>

 <li>jle Npos # if so, goto Npos:</li>

 <li>irmovq $1, %r10</li>

 <li>addq %r10, %rax # count++</li>

 <li>Npos: irmovq $1, %r10</li>

 <li>subq %r10, %rdx # len–</li>

 <li>irmovq $8, %r10</li>

 <li>addq %r10, %rdi # src++</li>

 <li>addq %r10, %rsi # dst++</li>

 <li>andq %rdx,%rdx # len &gt; 0?</li>

 <li>jg Loop # if so, goto Loop:</li>

 <li>##################################################################</li>

 <li># Do not modify the following section of code 37 # Function epilogue.</li>

 <li>Done:</li>

 <li>ret</li>

 <li>##################################################################</li>

 <li># Keep the following label at the end of your function 42 End:</li>

</ul>

Figure 3: <strong>Baseline Y86-64 version of the ncopy function. </strong>Seesim/pipe/ncopy.ys.

<ul>

 <li>Your pipe-full.hcl implementation must pass the regression tests in ../y86-code and ../ptest (without the -i flag that tests iaddq).</li>

</ul>

Other than that, you are free to implement the iaddq instruction if you think that will help. You may make any semantics preserving transformations to the ncopy.ys function, such as reordering instructions, replacing groups of instructions with single instructions, deleting some instructions, and adding other instructions. You may find it useful to read about loop unrolling in Section 5.8 of CS:APP3e.

<h2>Building and Running Your Solution</h2>

In order to test your solution, you will need to build a driver program that calls your ncopy function. We have provided you with the gen-driver.pl program that generates a driver program for arbitrary sized input arrays. For example, typing

unix&gt; <em>make drivers</em>

will construct the following two useful driver programs:

<ul>

 <li>yo: A <em>small driver program </em>that tests an ncopy function on small arrays with 4 elements. If your solution is correct, then this program will halt with a value of 2 in register %rax after copying the src array.</li>

 <li>yo: A <em>large driver program </em>that tests an ncopy function on larger arrays with 63 elements. If your solution is correct, then this program will halt with a value of 31 (0x1f) in register %rax after copying the src array.</li>

</ul>

Each time you modify your ncopy.ys program, you can rebuild the driver programs by typing

unix&gt; <em>make drivers</em>

Each time you modify your pipe-full.hcl file, you can rebuild the simulator by typing

unix&gt; <em>make psim VERSION=full</em>

If you want to rebuild the simulator and the driver programs, type

unix&gt; <em>make VERSION=full</em>

To test your solution in GUI mode on a small 4-element array, type

unix&gt; <em>./psim -g sdriver.yo</em>

To test your solution on a larger 63-element array, type

unix&gt; <em>./psim -g ldriver.yo</em>

Once your simulator correctly runs your version of ncopy.ys on these two block lengths, you will want to perform the following additional tests:

<ul>

 <li><em>Testing your driver files on the ISA simulator. </em>Make sure that your ncopy.ys function works properly with YIS:</li>

</ul>

unix&gt; <em>make drivers </em>unix&gt; <em>../misc/yis sdriver.yo</em>

<ul>

 <li><em>Testing your code on a range of block lengths with the ISA simulator. </em>The Perl script correctness.pl generates driver files with block lengths from 0 up to some limit (default 65), plus some larger sizes. It simulates them (by default with YIS), and checks the results. It generates a report showing the status for each block length:</li>

</ul>

unix&gt; <em>./correctness.pl</em>

This script generates test programs where the result count varies randomly from one run to another, and so it provides a more stringent test than the standard drivers.

If you get incorrect results for some length <em>K</em>, you can generate a driver file for that length that includes checking code, and where the result varies randomly:

unix&gt; <em>./gen-driver.pl -f ncopy.ys -n </em><em>K </em><em>-rc &gt; driver.ys </em>unix&gt; <em>make driver.yo </em>unix&gt; <em>../misc/yis driver.yo</em>

The program will end with register %rax having the following value:

<strong>0xaaaa </strong>: All tests pass.

<strong>0xbbbb </strong>: Incorrect count

<strong>0xcccc </strong>: Function ncopy is more than 1000 bytes long.

<strong>0xdddd </strong>: Some of the source data was not copied to its destination.

<strong>0xeeee </strong>: Some word just before or just after the destination region was corrupted.

<ul>

 <li><em>Testing your pipeline simulator on the benchmark programs. </em>Once your simulator is able to correctly execute sdriver.ys and ldriver.ys, you should test it against the Y86-64 benchmark programs in ../y86-code: unix&gt; <em>(cd ../y86-code; make testpsim)</em></li>

</ul>

This will run psim on the benchmark programs and compare results with YIS.

<ul>

 <li><em>Testing your pipeline simulator with extensive regression tests. </em>Once you can execute the benchmark programs correctly, then you should check it with the regression tests in ../ptest. For example, if your solution implements the iaddq instruction, then</li>

</ul>

unix&gt; <em>(cd ../ptest; make SIM=../pipe/psim TFLAGS=-i)</em>

<ul>

 <li><em>Testing your code on a range of block lengths with the pipeline simulator. </em>Finally, you can run the same code tests on the pipeline simulator that you did earlier with the ISA simulator</li>

</ul>

unix&gt; <em>./correctness.pl -p</em>

<h1>7          Evaluation</h1>

The homework is worth 100 points: 20 points for Part A, 20 points for Part B, and 60 points for Part C.

<h2>Part A</h2>

Part A is worth 20 points, 7+7+6 points for Y86-64 solution program. Each solution program will be evaluated for correctness, including proper handling of the stack and registers, as well as functional equivalence with the example C functions in examples.c.

The programs sum.ys and rsum.ys will be considered correct if the graders do not spot any errors in them, and their respective sumlist and rsumlist functions return the sum 0xcba in register %rax.

The program copy.ys will be considered correct if the graders do not spot any errors in them, and the copyblock function returns the sum 0xcba in register %rax, copies the three 64-bit values 0x00a, 0x0b, and 0xc to the 24 bytes beginning at address dest, and does not corrupt other memory locations.

<h2>Part B</h2>

This part of the homework is worth 20 points:

<ul>

 <li>5 points for your description of the computations required for the iaddq instruction.</li>

 <li>5 points for passing the benchmark regression tests in y86-code, to verify that your simulator still correctly executes the benchmark suite.</li>

 <li>10 points for passing the regression tests in ptest for iaddq.</li>

</ul>

<h2>Part C</h2>

This part of the homework is worth 60 points: You will not receive any credit if either your code for ncopy.ys or your modified simulator fails any of the tests described earlier.

<ul>

 <li>10 points each for your descriptions in the headers of ncopy.ys and pipe-full.hcl and the quality of these implementations.</li>

 <li>40 points for performance. To receive credit here, your solution must be correct, as defined earlier. That is, ncopy runs correctly with YIS, and pipe-full.hcl passes all tests in y86-code and ptest.</li>

</ul>

We will express the performance of your function in units of <em>cycles per element </em>(CPE). That is, if the simulated code requires <em>C </em>cycles to copy a block of <em>N </em>elements, then the CPE is <em>C/N</em>. The PIPE simulator displays the total number of cycles required to complete the program. The baseline version of the ncopy function running on the standard PIPE simulator with a large 63-element array requires 956 cycles to copy 63 elements, for a CPE of 956<em>/</em>63=15<em>.</em>18.

Since some cycles are used to set up the call to ncopy and to set up the loop within ncopy, you will find that you will get different values of the CPE for different block lengths (generally the CPE will drop as <em>N </em>increases). We will therefore evaluate the performance of your function by computing the average of the CPEs for blocks ranging from 1 to 64 elements. You can use the Perl script benchmark.pl in the pipe directory to run simulations of your ncopy.ys code over a range of block lengths and compute the average CPE. Simply run the command

unix&gt; <em>./benchmark.pl</em>

to see what happens. For example, the baseline version of the ncopy function has CPE values ranging between 29<em>.</em>00 and 14<em>.</em>27, with an average of 15<em>.</em>18. Note that this Perl script does not check for the correctness of the answer. Use the script correctness.pl for this.

For 40 points(Speed-up): The 3 student with the lowest amount of CPEs (highest speedup) will receive 40 points. Rest of the grades will be scaled accordingly, based on their standing in between highest cpe score of the top 3 student and base line cpe count(15.18).

By default, benchmark.pl and correctness.pl compile and test ncopy.ys. Use the -f argument to specify a different file name. The -h flag gives a complete list of the command line arguments.

<h1>8          Handin Instructions</h1>

<ul>

 <li>You will submit your solutions as a single tar file named eXXXXXXX .tar to Cow ,where XXXXXXX is your 7digit student ID.eXXXXXXX .tar file MUST contain exactly the follow- ing files:</li>

 <li>You will be handing in three sets of files:

  <ul>

   <li>Part A: sum.ys, rsum.ys, and copy.ys.</li>

   <li>Part B: seq-full.hcl.</li>

   <li>Part C: ncopy.ys and pipe-full.hcl.</li>

  </ul></li>

 <li>Make sure you have included your name and ID in a comment at the top of each of your handin files.</li>

</ul>

<h1>9          Hints</h1>

<ul>

 <li>By design, both sdriver.yo and ldriver.yo are small enough to debug with in GUI mode. We find it easiest to debug in GUI mode, and suggest that you use it.</li>

 <li>In order to compile the simulator with GUI mode enabled, Tcl/Tk libraries are needeed. These libraries are installed on ineks. Also, TKLIBS and TKINC variables in the Makefiles are configured for ineks. If you want to work on your own personal computer you should have Tcl/Tk libraries installed on your system and configure TKLIBS and TKINC variables in the Makefiles.</li>

 <li>If you want to work on ineks by connecting them from outside of the department and use GUI of the simulator, you should first connect to login machine with the following command (XXXXXXX is your 7 digit student ID)</li>

</ul>

unix&gt; ssh -X -p 8085 <a href="/cdn-cgi/l/email-protection" class="__cf_email__" data-cfemail="03665b5b5b5b5b5b5b436f6c646a6d2d60666d642d6e6677762d6667762d7771">[email protected]</a>

and then connect to an inek again with -X parameter (Command is given for inek5, but you can work on any ineks excluding inek0.):

unix&gt; ssh -X <a href="/cdn-cgi/l/email-protection" class="__cf_email__" data-cfemail="d3b68b8b8b8b8b8b8b93babdb6b8e6">[email protected]</a>

<ul>

 <li>With some X servers, the “Program Code” window begins life as a closed icon when you run psim or ssim in GUI mode. Simply click on the icon to expand the window.</li>

 <li>With some Microsoft Windows-based X servers, the “Memory Contents” window will not automatically resize itself. You’ll need to resize the window by hand.</li>

 <li>The psim and ssim simulators terminate with a segmentation fault if you ask them to execute a file that is not a valid Y86-64 object file.</li>

</ul>