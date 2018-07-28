---
layout: post
title: Return Oriented Programming Tutorial
category: Deep Learning
tags:
- Education
---


---

Hi exploit writers! In this post, I’m writing a tutorial on a technique called Return Oriented Programming. If you’re not familiar with standard stack overflows and Return-To-Libc, you should definitely get those down before reading this tutorial. I’ll go into a little refresher before getting into the details of how to execute a Return Oriented Programming(ROP for short) exploit. First, I’ll go into a brief background of stack exploitation, and the progression to ROP.

# Stack Overflows

Stack overflows happen when there is an unbounded write into a buffer. Here's a nice picture to represent what's happening.
<br>
<br>
![otw-behemoth]({{ site.baseurl }}/stack_overflow.png){:height="850px" width="750px"}
<br>
<br>
<br>
This is an example of a function foo which has an unbounded write into a buffer. Normally, the only part of the stack allocated for foo is the area between ESP(stack pointer) and EBP(base pointer), which is called the stack frame. However, because foo used an insecure function, the user can write past the stack frame, into other important parts of the program. The most important part is the Return Address, right below Saved EBP in the diagram above. The Return Address is where the program returns to after calling foo. If you can overwrite the return address, you can make the program return to wherever you want, which can cause all sorts of damage.
<br>
<br>
Here's an analogy I like to use. Imagine you've left your home to run some errands, and you're using Google Maps to navigate you to an unknown area. But after you're done with your errands, you need to set your Return Address back to your home. What if I, as a malevolent actor, had the ability to overwrite your Return Address WITHOUT YOU KNOWING, and made you drive into the Atlantic Ocean? That would be pretty bad. In a standard stack overflow, you write your own malevolent code, and set the return address to where that code is located. The program then executes your malicious code - pretty bad stuff. However, this is normally mitigated with a little defense called DEP, short for Data Execution Prevention, which I'll go into next.

# DEP

Data Execution Prevention marks certain regions as non-executable. This means that even if you write your code somewhere and set the return address to the code, the program sees that the memory region the code is located in is marked as non-executable, and doesn't execute it. In the previous Google Maps analogy, the equivalent would be Google Maps seeing that the Atlantic Ocean isn't a valid region to go, and wouldn't allow you to go there (Not exactly what happens, but good enough for this tutorial). However, for every defense, there's a way to bypass it. Enter- ROP!

# ROP

ROP is different in that rather than overwriting the return address with your own code, you overwrite it to point to pieces of code that already exist within the program. These pieces of code are referred to as gadgets - they can be entire functions or individual assembly instructions. By chaining together these gadgets, you can create new, unintended(and usually malicious) actions. Think of the phrase "The Apatosaurus likes to eat grass. The T-Rex eats the Apatosaurus". By chaining together specific words in the sentence, I can create a sentence with an entirely new meaning - "grass eats the T-Rex". Similarly, you can do this with code - by chaining together pieces of code that already exist within the program, you can create entirely new functionality. Sometimes, just one gadget is enough. Other times, you need dozens. This tutorial will provide an easier tutorial where the gadgets are actual function calls. First, we need to see what happens when we overwrite the return address with another function, and how we can utilize this.

### The Stack for ROP

Normally, when you want to call a function, you use the following format:


1. *Function you want to call(Let’s call this Function1)*
2. *Exit function*
3. *Arguments to Function1*

Function1 will look past the next instruction for its arguments - If Function1 is called at spot 1, it will look for it’s arguments in spot 3, if it’s called in spot 2, it will look for the arguments in spot 4, etc. Directly AFTER Function1 is the next function that will be called - normally, this is a call to exit the program, so if Function1 is called in spot 1, the the next function is in spot 2. However, what if instead of exiting, we want to call another function(called Function2) for malicious purposes? There is a problem here in passing the arguments for Function2 - let’s see this through an example. In this example, we want to call Function2 after Function1, and Function1 takes in two arguments.

1. *Function1*
2. *Function2*
3. *Argument to Function1*
4. *Argument to Function1*

So here, Function1 is in spot 1, and the arguments to Function1 are in spots 3 and 4. This is all well and good, but what happens when Function2 is called? Function2 is called in spot 2. Thus, if it requires arguments, it will look in spot 4, and the next function that is called will be in spot 3. Normally, this doesn’t happen because the next function that is called is the exit function, which stops all execution. However, in this case we want to call something else.
The problem is, Function2 will look for its arguments in spot 4 but spot 4 is still occupied by an argument to Function1! The program will also try to execute whatever is in spot 3 after Function2, but that’s also an argument to Function1! What to do? The key here, is before calling Function2, to call a separate function that will remove the arguments to Function1 first. Observe:

1. *Function1*
2. *Function to remove arguments to function1*
3. *Argument to Function1*
4. *Function2*
5. *Function to remove argument for Function2*
6. *Argument for Function2*
7. *Function3*

etc.

This allows us to multiple function calls together. When Function1 is called, it is called with the arguments after the next immediate instruction. When Function1 is finished executing, the program returns to the instruction immediately after where Function1 was called. So if Function1 is called at #1, it is passed the arguments at #3. When Function1 is finished executing, the program returns to instruction #2. After Function2 is called, we remove the arguments for Function2, and continue for however many functions we need to call. The instruction used to remove the arguments is usually in the form pop;pop;ret. The number of "pops" corresponds to the number of arguments that need to be removed. If there are two arguments that need to be removed, you'll need a gadget that is pop;pop;ret;. If there is only one argument, then pop;ret; is what you need. Here’s an example of what happens step by step.

### Step 1
1. *Function1 ← First, Function1 is called, with the arguments in spot 3*
2. *Function to remove arguments to function1*
3. *Argument to Function1*
4. *Function2*
5. *Function to remove argument for Function2*
6. *Argument for Function2*
7. *Function3*

### Step 2
1. *Function1* 
2. *Function to remove arguments to function1 ← After Function1 finishes, this is called. It doesn’t take any arguments. It removes the Argument in spot 3.*
3. *Argument to Function1*
4. *Function2*
5. *Function to remove argument for Function2*
6. *Argument for Function2*
7. *Function3*

### Step 3
1. *Function1* 
2. *Function to remove arguments to function1* 
3. *Function2 ←Now, Function2 is in the next spot. Before, this was in spot 4, and the argument to Function1 was in spot 3, but it was removed and everything below was shifted up.* 
4. *Function to remove argument for Function2*
5. *rgument for Function2*
6. *Function3*

### Step 4
1. *Function1* 
2. *Function to remove arguments to function1* 
3. *Function2 ←Function2 is now called with the Arguments to Function2, which are located in spot 5.*
4. *Function to remove argument for Function2*
5. *Argument for Function2*
6. *Function3*

### Step 5
1. *Function1*
2. *unction to remove arguments to function1* 
3. *Function2* 
4. *Function to remove argument for Function2 ← After Function2 is called, we need to repeat the process we did for Function1. We call a function to remove the arguments for Function2.*
5. *Argument for Function2*
6. *Function3*

### Step 6
1. *Function1* 
2. *Function to remove arguments to function1* 
3. *Function2* 
4. *Function to remove argument for Function2* 
5. *Function3 ← Before, the arguments to Function2 were in this spot, but now it’s Function3. Function 3 is now called. Continue this process for however many functions you want to call.*

 Without further ado, let's jump into an example!

# Example
Here's the vulnerable program. It's taken from a site called CodeArcana - this specific example is my favorite way to teach ROP.

{% highlight c %}
char string[100];

void exec_string() {
   system(string);
}
void add_bin(int magic) {
   if (magic == 0xdeadbeef) {
       strcat(string, "/bin");
   }
}
void add_sh(int magic1, int magic2) {
   if (magic1 == 0xcafebabe && magic2 == 0x0badf00d) {
       strcat(string, "/sh");
   }
}
void vulnerable_function(char* string) {
   char buffer[100];
   strcpy(buffer, string);
}

int main(int argc, char** argv) {
   string[0] = 0;
   vulnerable_function(argv[1]);
   return 0;
}

{% endhighlight %}


The vulnerable function is strcpy - it allows the unbounded write into a buffer that makes this exploit possible. Here you can see that we can overwrite the return address(stored in EIP) with 0x42424242. This essentially means that we told the program to return to the address 0x42424242 after executing the function. Because there is nothing located at the address 0x42424242, the program crashes with a Segmentation fault error.


![segfault]({{ site.baseurl }}/eip_overwrite.png){:height="750px" width="600px"}

<br>
<br>
The program has been compiled with DEP, meaning we can't immediately write malicious code and jump to it - it won't be executed that way. We'll need to be a little bit more clever...

Luckily, everything we need has been provided in the source code. We want to execute the function exec_string, where string is set to "/bin/sh". However, to do that, we first need to call add_bin with the argument 0xdeadbeef, and then add_sh with the argument 0xcafebabe and 0xbadf00d, and finally, call exec_string. So here's how the exploit should look like.

1. *Address of add_bin ← Function that we want to call*
2. *Address of pop;ret; ← Remove the argument to add_bin*
3. *0xdeadbeef ← Argument to add_bin*
4. *Address of add_sh ← The next function that we want to call*
5. *Address of pop;pop;ret ← Remove both of the arguments to add_sh*
6. *0xcafebabe ← Argument to add_sh*
7. *0xbadf00d ← Argument to add_sh*
8. *Address of exec_string ← Final*

So when the exploit is ran, the following steps should take place.

1. *The return address for vulnerable_function is overwritten with the address of add_bin*
2. *add_bin is called with the argument 0xdeadbeef. This concatenates the empty string with "/bin"*
3. *0xdeadbeef is removed from the stack*
4. *add_sh is called with the arguments 0xcafebabe and 0xbadf00d. This concatenates "/bin" with "/sh". The string is now "/bin/sh"*
5. *0xcafebabe and 0xbadf00d are removed from the stack*
6. *exec_string is called, which executes system("/bin/sh")*
7. *PROFIT*

Success! We've now exploited an executable using ROP. Go get yourself some ice cream.

# Impact
  For those of you reading this that are newer to binary exploitation might wonder what the impact of this is. In many other forms of security(Web security, network security), successful exploitation leads directly to access you didn’t have before, for example, gaining access to a machine you didn't have before, or gaining access to a web server's database. Binary exploitation is the same - if computer is hosting a service susceptible to ROP, you can gain unauthorized access to the computer. Finding a vulnerability in popular software can also be extremely effective in offensive security. Let’s take the example where you have a vulnerability within Microsoft Word which allows you to execute arbitrary code.

  If there was this vulnerability within Microsoft Word, we could execute arbitrary code within Word documents. This would allow us to run code on an unsuspecting user’s machine whenever they open a malicious Word document. In addition to gaining initial access, these vulnerabilities can also grant you additional privileges on the machine you are on.

  On Windows, users are separated into standard users and Administrators. Administrators have the ability to change whatever they want on the computer, while standard users are limited in what they can do. If you are a standard user and  exploit an administrative program, you have administrative privileges, which gives you the ability to cause more damage - for example, you could set firewall rules to weaken the machine for later attacks.

# Defenses
  Fortunately, there are defenses against ROP that can be implemented. Standard ROP is defeated by ASLR, short for Address Space Layout Randomization. ASLR is slightly self-explanatory - it randomizes all the addresses every time the program is run. As shown in this post, ROP relies on the use of certain gadgets to be located within the code. While we can sometimes debug the program to find the addresses for all the gadgets we need, we can't reliably write an exploit if the addresses are changing without some other way of knowing where gadgets are located. There are also guards to directly prevent calling functions that shouldn't be called, called CFG(Control Flow Guard) and CFI(Control Flow Integrity). We'll take a look at some of those defenses and how we can bypass them in later blog posts.

# Conclusion

Hopefully after this post, ROP makes a little bit more sense to you now. Almost everything these days is compiled with DEP and ASLR - we've learned a solid technique for bypassing DEP here - stay tuned for how to bypass ASLR. Thanks for reading!

# Sources

+ Xpnsec. “ROP Primer - Walkthrough of Level 0.” XPN Security, 27 Jan. 2017, xpnsec.tumblr.com/post/156417043491/rop-primer-walkthrough-of-level-0.
+ “Windows Exploit Development - Part 2: Intro to Stack Based Overflows.” Security Sift, 27 July 2015, www.securitysift.com/windows-exploit-development-part-2-intro-stack-overflow/.
+ “Code Arcana.” Code Arcana ATOM, codearcana.com/posts/2013/05/28/introduction-to-return-oriented-programming-rop.html.
