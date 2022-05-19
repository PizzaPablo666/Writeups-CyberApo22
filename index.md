## Space pirate: Going Deeper

This task was really interesting, because many struggled with this containing me for 2-3 days to understand it. And the trick was simple but elegant :D 
So first of all we are downloading the task files, which contain sp_going_deeper ELF x86_64 executable, glibc library for the executable to load some external functions and flag.txt (HTB{f4k3_fl4g_4_t35t1ng}).  

First we are going to connect to the remote host to understand what suprises wait for us. 
```
$ nc <ip> <port>
$ telnet <ip> <port>
```
After connecting we can see this banner :O 
<img width="722" alt="Screen Shot 2022-05-17 at 15 42 14" src="https://user-images.githubusercontent.com/24619999/169314379-0f36db24-a22e-4579-9cf3-df98426cb732.png">


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
