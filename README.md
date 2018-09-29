


 		     +--------------------------+
       	     |	     COE127L / C1       |
		     | PROJECT 2: USER PROGRAMS	|
		     | 	   DESIGN DOCUMENT     	|
		     +--------------------------+

---- GROUP 7 ----

>> Fill in the names and email addresses of your group members.

ISHIKAWA, CHIERI <cishikawa@mymail.mapua.edu.ph>
JAVIER, RUPPERT IAN <rirjavier@mymail.mapua.edu.ph>
YANG, RETH JERON <rjyang@mymail.mapua.edu.ph>


---- PRELIMINARIES ----

>> This Design Document is still on progress.

>> One of the materials used are the source codes on the 
>> PintOS of Stanford University,
>> http://www.stanford.edu/class/cs140/projects/pintos/pintos.tar.gz

			   ARGUMENT PASSING
			   ================

----- DATA STRUCTURES ----

>> A1: Copy here the declaration of each new or changed `struct' or
>> `struct' member, global or static variable, `typedef', or
>> enumeration.  Identify the purpose of each in 25 words or less.

None

---- ALGORITHMS ----

>> A2: Briefly describe how you implemented argument parsing.  How do
>> you arrange for the elements of argv[] to be in the right order?
>> How do you avoid overflowing the stack page?

How to implement argument parsing?
----------------------------------
The most important part was to setup the stack. We did it inside setup_stack ()
after page is installed, when the stack has been initialized.

Process_execute provides file_name, including command and arguments
string. First, we separated the first token and the rest, which are command and
arguments. We use command as the new thread's name, and pass down the arguments
string to start_process(), load() and setup_stack(). We think it’s implementable
since we can always get the command name from thread->name when needed, like
when load the ELF executable. 

When setting up the stack, we memcpy the argument string and then the command
name which is actually the thread name in our case. Then add alignment, scan the
string backward to get each token and push its address into the page underneath
the alignment to generate argv[], finally argv, argc and return address.

Way of arranging for the elements of argv[] to be in the right order.
--------------------------------------------------------------------
We scan through the argument string backwards, so that the first token we get is
the last argument, the last token we get is the first argument. We can just keep
decreasing esp pointer to setup the argv[] elements. 

How to avoid overflowing the stack page?
----------------------------------------
The thing is we decided not to check the esp pointer until it fails. Our
implementation didn’t pre-count how much space do we need, just go through
everything, make the change, like add another argv element, when necessary. But
this leaves us two way to deal with overflowing, one is checking esp’s validity
every time before use it, the other one is letting it fails, and we handle it in
the page fault exception, which is exit(-1) the running thread whenever the
address is invalid. We chose the latter approach since the first approach seems
have too much burden and it make sense to terminate the process if it provides
too much arguments.

---- RATIONALE ----

>> A3: Why does Pintos implement strtok_r() but not strtok()?

The only difference between strtok_r() and strtok() is that the save_ptr
(placeholder) in strtok_r() is provided by the caller. In pintos, the kernel
separates commands into command line (executable name) and arguments. So we need
to put the address of the arguments somewhere we can reach later.

>> A4: In Pintos, the kernel separates commands into a executable name
>> and arguments.  In Unix-like systems, the shell does this
>> separation.  Identify at least two advantages of the Unix approach.

1) Shortening the time inside kernel
2) Robust checking. Checking whether the executable is there before passing it
to kernel to avoid kernel fail. Checking whether the arguments are over the
limit. 
3) Once it can separate the commands, it can do advanced pre-processing, acting
more like an interpreter not only an interface. Like passing more than 1 set
of command line at a time, i.e. cd; mkdir tmp; touch test; and pipe.

			     SYSTEM CALLS
			     ============

---- DATA STRUCTURES ----

>> B1: Copy here the declaration of each new or changed `struct' or
>> `struct' member, global or static variable, `typedef', or
>> enumeration.  Identify the purpose of each in 25 words or less.

>> B2: Describe how file descriptors are associated with open files.
>> Are file descriptors unique within the entire OS or just within a
>> single process?

---- ALGORITHMS ----

>> B3: Describe your code for reading and writing user data from the
>> kernel.

>> B4: Suppose a system call causes a full page (4,096 bytes) of data
>> to be copied from user space into the kernel.  What is the least
>> and the greatest possible number of inspections of the page table
>> (e.g. calls to pagedir_get_page()) that might result?  What about
>> for a system call that only copies 2 bytes of data?  Is there room
>> for improvement in these numbers, and how much?

>> B5: Briefly describe your implementation of the "wait" system call
>> and how it interacts with process termination.

>> B6: Any access to user program memory at a user-specified address
>> can fail due to a bad pointer value.  Such accesses must cause the
>> process to be terminated.  System calls are fraught with such
>> accesses, e.g. a "write" system call requires reading the system
>> call number from the user stack, then each of the call's three
>> arguments, then an arbitrary amount of user memory, and any of
>> these can fail at any point.  This poses a design and
>> error-handling problem: how do you best avoid obscuring the primary
>> function of code in a morass of error-handling?  Furthermore, when
>> an error is detected, how do you ensure that all temporarily
>> allocated resources (locks, buffers, etc.) are freed?  In a few
>> paragraphs, describe the strategy or strategies you adopted for
>> managing these issues.  Give an example.

---- SYNCHRONIZATION ----

>> B7: The "exec" system call returns -1 if loading the new executable
>> fails, so it cannot return before the new executable has completed
>> loading.  How does your code ensure this?  How is the load
>> success/failure status passed back to the thread that calls "exec"?

>> B8: Consider parent process P with child process C.  How do you
>> ensure proper synchronization and avoid race conditions when P
>> calls wait(C) before C exits?  After C exits?  How do you ensure
>> that all resources are freed in each case?  How about when P
>> terminates without waiting, before C exits?  After C exits?  Are
>> there any special cases?

---- RATIONALE ----

>> B9: Why did you choose to implement access to user memory from the
>> kernel in the way that you did?

>> B10: What advantages or disadvantages can you see to your design
>> for file descriptors?

>> B11: The default tid_t to pid_t mapping is the identity mapping.
>> If you changed it, what advantages are there to your approach?

			   SURVEY QUESTIONS
			   ================

Answering these questions is optional, but it will help us improve the
course in future quarters.  Feel free to tell us anything you
want--these questions are just to spur your thoughts.  You may also
choose to respond anonymously in the course evaluations at the end of
the quarter.

>> In your opinion, was this assignment, or any one of the three problems
>> in it, too easy or too hard?  Did it take too long or too little time?

>> Did you find that working on a particular part of the assignment gave
>> you greater insight into some aspect of OS design?

>> Is there some particular fact or hint we should give students in
>> future quarters to help them solve the problems?  Conversely, did you
>> find any of our guidance to be misleading?

>> Do you have any suggestions for the TAs to more effectively assist
>> students, either for future quarters or the remaining projects?

>> Any other comments?
