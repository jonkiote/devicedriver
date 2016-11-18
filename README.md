# devicedriver
(Operating Systems: Project 2)
<h3><a href="https://laulima.hawaii.edu/access/content/attachment/MAN.75233.201710/Assignments/870596c8-9d52-4fd1-a5af-a3d7e34feadc/EE468PDevDriverInstructions.pdf">From EE468PDevDriverInstructions.pdf</a></h3>
<br>
<h2>Assignment Instructions</h2>
<br>
You will build and test a Linux device driver.<br>
<br>
As a warm up, build the memory driver described in the attached document “Writing device drivers in Linux” that 
allows a user to read and write a single character to it.<br>
<br>
You should also try the hello world driver too as a warm up.<br>
<br>
Most of this document comes from:  http://www.freesoftwaremagazine.com/articles/drivers_linux <br>
<br>
At the web site, look at the last driver since it shows how to write a driver which has external hardware.<br>
<br>
To get a better idea of what's going on, you can read the first three chapters of Linux Device Drivers, Third Edition:<br>
http://lwn.net/Kernel/LDD3/<br>
<br>
Fortunately, this book is online. The fourth chapter is useful too since it covers debugging.<br>
<br>
Another useful link is<br>
http://www.tldp.org/LDP/lkmpg/2.6/html/lkmpg.html<br>
<br>
ASSIGNMENT:<br>
<br>
Build a memory device driver where the device is a stack of a maximum of 10 bytes.  Initially, the stack is empty.  When the stack is written to, the device will push ASCII lower-case alphabets from the user buffer into the stack.  For example, it will push bytes 'a' and 'k' but will not push bytes 'A', 'K', '*', or '2'. If the buffer becomes full then the driver will stop and return. <br>
<br>
When reading the device driver, the driver will pull the bytes out of the stack and put it into the user buffer.  If the stack becomes empty then the driver will stop and return.<br>
<br>
You must work in groups of two or three.  If you cannot find a group, let me know.<br>
<br>
SUBMISSION INSTRUCTIONS<br>
Note:  The following submission instructions may change.  But the following is good for testing for now.<br>
<br>
EE 468 Device Driver Project <br>
<br>
Submission instructions <br>
<br>
1.  Attached is a test program, test.c 
    This program will write and read characters to your 
    device, and display them using printf.  You may 
    have to modify the test program so that your device 
    will work with it.  For example, you may have to 
    change the path in the "open" call to your particular 
    device.<br>
    Note that the test program will first clear your device 
    with a read.  Then goes through phases, where each 
    phase will write characters into the device, then 
    read characters into a buffer (buffer_out).  Also note 
    that buffer_out is initialized to a string of spaces. 
    Then we can use the printf on buffer_out to print what's 
    been read from the device, i.e., the characters read 
    from the device will fill the beginning characters in 
    buffer_out and the rest of the characters will be spaces.<br>
    <br> 
2.  Once you have your device working with the test 
    program, run the test program.  You should have displays 
    on your console from the printfs.  Run the test 
    program again but direct the display output to a file, e.g., 
    ./a.out > ee468proj3output <br>
    <br>
3.  Put in a folder,  <br>
    *  Your display output, e.g., ee468proj2output <br>
    *  Source code of your device driver module <br>
    *  Source code of your test.c program <br>
    *  Makefile, so by typing make your code will compile and your module is ready to be inserted. <br>
    <br>
    Your folder should have the name:  <br>
YourLastName-FirstName-Project3 
    Tarball and gzip it and upload into laulima. 
------------------ 
End of instructions --------------------------- 
You can find the following file in laulima
/* EE 468:  Test for device driver project 
 * 
 * You will likely have to change the open() since your 
 * device name may be different. 
 * 
 * The test will write and read character strings into 
 * the device.  Then it displays, using printf, the 
 * results of the reads.  Record these outputs from 
 * the printfs into a file. 
 */ 
#include <stdio.h> 
#include <fcntl.h> 
#include <sys/stat.h> 
#include <assert.h> 
#include <unistd.h> 
#include <string.h> 
int main() 
{ 
int fp; 
char buffer_in[100]; 
char buffer_out[100]; 
int i; 
char c; 
fp = open("/dev/myDev", O_RDWR); /* Change "myDev" to your own device. */ 
read(fp,buffer_out,10); /* Clear out your device */   
strcpy(buffer_in,"abcdefghijklmnopqrstuvwxyz"); /* Initialize buffer_in */ 
write(fp, buffer_in, 10); /* This fills the device buffer */ 
strcpy(buffer_out,"                           ");  /* Initializes buffer_out*/ 
                                     /* with spaces */ 
read(fp,buffer_out,10);   
printf("Write and read 10 characters:  %s\
n\n",buffer_out); 
write(fp, buffer_in, 5); /* Fill half the device */  
strcpy(buffer_out,"                           "); 
read(fp,buffer_out,2);   /* Read two characters */ 
printf("Write 5 chars and read 2:  %s\
n\n",buffer_out); 
write(fp, buffer_in, 2); /* Fill in two more characters */ 
strcpy(buffer_out,"                           "); 
read(fp,buffer_out,5);   /* Read all characters in device  */ 
printf("Write 2 and read 5 chars:  
%s\  n\n",buffer_out); 
write(fp, buffer_in, 20);  
strcpy(buffer_out,"                           "); 
read(fp,buffer_out,20);    
printf("Write 20 chars (overflow to at most 10) and read 20 (should read only 10, 
underflow):  %s\
n\n",buffer_out); 
printf("End 
of test\
n"); 
} 
