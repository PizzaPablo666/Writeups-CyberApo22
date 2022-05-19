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
In the line 29 we have strncmp function, which compares the two strings until the mentioned number (3rd param) and outputs the difference of the first different char, or if they are equal returns "0". 


You can use the [editor on GitHub](https://github.com/PizzaPablo666/Writeups-CyberApo22/edit/gh-pages/index.md) to maintain and preview the content for your website in Markdown files.

Whenever you commit to this repository, GitHub Pages will run [Jekyll](https://jekyllrb.com/) to rebuild the pages in your site, from the content in your Markdown files.

### Markdown

Markdown is a lightweight and easy-to-use syntax for styling your writing. It includes conventions for

```markdown
Syntax highlighted code block

# Header 1
## Header 2
### Header 3

- Bulleted
- List

1. Numbered
2. List

**Bold** and _Italic_ and `Code` text

[Link](url) and ![Image](src)
```

For more details see [Basic writing and formatting syntax](https://docs.github.com/en/github/writing-on-github/getting-started-with-writing-and-formatting-on-github/basic-writing-and-formatting-syntax).

### Jekyll Themes

Your Pages site will use the layout and styles from the Jekyll theme you have selected in your [repository settings](https://github.com/PizzaPablo666/Writeups-CyberApo22/settings/pages). The name of this theme is saved in the Jekyll `_config.yml` configuration file.

### Support or Contact

Having trouble with Pages? Check out our [documentation](https://docs.github.com/categories/github-pages-basics/) or [contact support](https://support.github.com/contact) and we’ll help you sort it out.
