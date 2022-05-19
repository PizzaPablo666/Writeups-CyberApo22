## Space pirate: Going Deeper

This pwn task was really interesting, because many struggled with this containing me for 2-3 days to understand it. And the trick was simple but elegant :D 
So first of all we are downloading the task files, which contain sp_going_deeper ELF x86_64 executable, glibc library for the executable to load some external functions and flag.txt (HTB{f4k3_fl4g_4_t35t1ng}).  

First we are going to connect to the remote host to understand what suprises wait for us. 
```
$ nc <ip> <port>
$ telnet <ip> <port>
```
After connecting we can see this banner :O  
  
<img width="722" alt="Screen Shot 2022-05-17 at 15 42 14" src="https://user-images.githubusercontent.com/24619999/169314379-0f36db24-a22e-4579-9cf3-df98426cb732.png">

In the banner there is an interesting monitor/laptop in which it is said there is a "goldenfang" user which changed secret_pass.txt name to flag.txt. 
And we got 3 options.
1. Disable mechanisms 
2. Login
3. Exit 

We totally don't want to exit, so we will go ahread with the first two.  
  
<img width="354" alt="Screen Shot 2022-05-17 at 15 42 55" src="https://user-images.githubusercontent.com/24619999/169315995-5c9bd182-b35f-498f-a76a-01407652e74c.png">

First option gives us "Input" prompt, and the second option gives us "Login" prompt, as the answer for all the inputs there was "Authentication failed!" output and connection was terminated.

So to understand the executable more deeper, we should analyze it and see what is contains. 
We can use **strings** command to see human readable strings in the executable.
```
$ strings <filename>
```
For out file:  
 <img width="390" alt="Screen Shot 2022-05-17 at 15 43 18" src="https://user-images.githubusercontent.com/24619999/169318761-ef0056cf-2269-4f95-b4d5-dc2f46e5e096.png">
 
We see that the command outputs really interesting and valuable information from the executable.  
  
<img width="635" alt="Screen Shot 2022-05-17 at 15 43 40" src="https://user-images.githubusercontent.com/24619999/169323256-1b99c52d-7e6e-43c4-b3b5-59b9fb82d12c.png">

We can see some string, which (we hope) is not misleading.
After trying to input that string "DRAEGER15th30n34nd0nly4dm1n15tr4t0R0fth15sp4c3cr4ft" the answer was the same "Auth fail!" , connection termination. 
So we need to __**GO DEEPER**__.
For going deeper we need to disassemble the executable and decompile it (to see the C code). I have used Ghidra for this, IDA is also cool for this kind of stuff. 
By loading the file and analyzing it, I have found a function called admin_panel in the executable. Let's see in details.  
  
<img width="751" alt="Screen Shot 2022-05-17 at 15 44 35" src="https://user-images.githubusercontent.com/24619999/169325626-8b012aa3-ffee-47df-a120-c3110d831c99.png">  

Let's understand what is written in the code. 
So we have a function called admin_panel, which gets 3 parameters (the params are 1, 2, 3 for disaplaying the options). The function has a local_38 char array with 40 bytes. In the line 10 it prints the below part of the banner with the options using the params. Then we have a while loop which forces us to input 1 2 or 3, and if we input 1 or 2 (doesn't matter), then the input goes to the same variable local_38. In the line 27 we can see that from our input 57 bytes can be read, but our local_38 variable is 40 byte array, the fun part is that this doesn't matter :DDD  
In the line 28 we can see an if statement which compares the params, we can't change params anyway because they are consts, so we go ahead, and here we gooooo 3:)  
In the line 29 we have strncmp function, which compares the two strings until the mentioned number (3rd param) and outputs the difference of the first different char, or if they are equal returns "0".  If strncmp function returns 0, we are getting the admin panel and the flag.
At first sight when we input the __**STRING**__, it should work and give us the admin panel. But it didn't work. So I have emulated this little part of code in C language to test some stuff.  
  
<img width="682" alt="Screen Shot 2022-05-17 at 15 45 20" src="https://user-images.githubusercontent.com/24619999/169328802-f223731c-5de6-468e-9da0-f1d810ec6075.png">

The above code does the same as the admin_panel's that specific part, this is done for better understanding and what ouput do we get. (Of course this could be done with gdb, but I tried this way because wasn't much familiar with gdb functionality yet).  
After inserting the __**STRING**__ as an input to my code, I got the return of the function "-10" :O , whaaaaaa? 

<img width="467" alt="Screen Shot 2022-05-17 at 15 46 02" src="https://user-images.githubusercontent.com/24619999/169329869-7a123b14-f6ca-4a64-b5af-b0654bd40d2e.png">

Then I have started to look at ascii codes, it didn't help much, but I tried to input tab at the end of the __**STRING**__, then tried "\0", but the return value wasn't "0", after some research __**BINGO**__, it was all about the "Enter", the "Enter" was interpreted as "\n" in the char of array, so we needed to have the EOT, which creates EOF condition for our input, the EOT we could have if after inputting the string we inputted CTRL+D, not "Enter. And BOOM, we got it.  
  
<img width="464" alt="Screen Shot 2022-05-17 at 15 46 33" src="https://user-images.githubusercontent.com/24619999/169333082-dc32ebd5-2ff8-4601-8196-cb129173ed91.png">  
  
First I have tried it in my local environment. And it work!  
  
<img width="562" alt="Screen Shot 2022-05-17 at 15 46 56" src="https://user-images.githubusercontent.com/24619999/169333752-5f096f95-5d66-4169-bf50-5abea2c01662.png">  
  
After this tried in the remote env, and yeey we got the flag. 
<img width="744" alt="Screen Shot 2022-05-17 at 16 03 47" src="https://user-images.githubusercontent.com/24619999/169334655-77068a8f-3a5a-412d-b2eb-a198abb9ec11.png">
