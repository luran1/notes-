# Lab 3 Readme

#### Objective

The objective of this lab was to create a process log level and system calls that will ***get*** and ***set*** this log level. 

#### Methodology

## system call and log level implimentation 
First I created 3 new system calls following the documentation criteria. Below are the added calls:

	435	common	get_proc_log_level	__x64_sys_get_proc_log_level
	436	common	set_proc_log_level	__x64_sys_set_proc_log_level
	437	common	proc_log_message	__x64_sys_proc_log_message
	
Then I included the new process log level into ***include/linux/syscalls.h*** as an ****extern unsigned int**** following documentation for unsigned int log level and extern to ensure it was a global variable.
Then I made sure that the system calls were presistent by creating asmlinkages for all the new system calls.
I then went to ***init/main.c*** where I initilized log level to 0. Putting it in this file ensures that it will be initilized when the kernal is made.
Finally I define the system calls within ***kernel/sys.c*** using the proper syntex depending on the number of parameters.
	
	SYSCALL_DEFINE0(get_proc_log_level)
	
The code segment above is the ***get*** syscall. The number following the DEFINE is the number of paramenters for the call. Since the ***get*** call doesn't need any parameters the number proceding DEFINE is 0.
The implimentation of the get function is to simply return the log level. The ***set*** call gets the user ID using:

	int uid = get_current_user()->uid.val;

I check if the user is a superuser by checking if uid == 0. If it does i'll save the new level into log level and return loglevel, else I'll return -1.
The final system call first checked if the level passed was valid (AKA between 0 and 7). If it was I retrived the current process ID through 

	int pid = current->pid;
	
and then using the process ID i got the process name by 
	
	char* name = current->comm;
	
With this data I then checked if the level passed was below or at the log level. If so I used a set of if statments to check which log level was appropriate. if log level was 0, it would be a KERN_EMERG.

## library implimentation 

the library function were simply calling on the appropriate system call. I also defined the libabry fucntion within the header file since the implimentation was so straight forward. 
I created a make file for the libabry which runs all priliminary codes to make the library usable.
