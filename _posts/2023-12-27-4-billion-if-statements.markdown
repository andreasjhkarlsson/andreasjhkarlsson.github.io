---
layout: post
title:  "4 billion if statements"
date:   2023-12-27 16:20:00 +0100
categories: jekyll update
---

I recently stumbled upon this screenshot while researching social media on the train. Of course, it was followed by a cascade of spiteful comments, criticizing this fresh programmer’s attempt to solve a classical problem in computer science. The modulus operation. 

![TikTok screenshot](/assets/images/GCPVDa1WYAAoBut.jpg){: width="250" }

Now, in a world where AI is replacing programmers by the minute, taking their jobs and revolutionizing the way we think about code, maybe we should be more open to the thoughts of the fresh new blood of the industry? In fact, the above code is a perfect example of a time-memory tradeoff. You’re trading off your time and at the same time, the computers memory and time as well! Truly a marvelous algorithm!

So I went to work to explore this idea of checking if a number is odd or even by only using comparisons to see how well it works in a real world scenario. Since I’m a great believer in performant code I decided to implement this in the C programming language as it’s by far the fastest language on the planet to this day (thanks to the visionary genius Dennis Richie). 

So I started composing

{% highlight c %}
/* Copyright 2023. All unauthorized distribution of this source code 
   will be persecuted to the fullest extent of the law*/
#include <stdio.h>
#include <stdint.h>
#include <stdlib.h>
int main(int argc, char* argv[])
{
    uint8_t number = atoi(argv[1]); // No problems here
    if (number == 0)
        printf("even\n");
    if (number == 1)
        printf("odd\n");
    if (number == 2)
        printf("even\n");
    if (number == 3)
        printf("odd\n");
    if (number == 4)
        printf("even\n");
    if (number == 5)
        printf("odd\n");
    if (number == 6)
        printf("even\n");
    if (number == 7)
        printf("odd\n");
    if (number == 8)
        printf("even\n");
    if (number == 9)
        printf("odd\n");
    if (number == 10)
        printf("even\n");
}
{% endhighlight %}

Beautiful! Lets compile the code, disabling optimizations with /Od to make sure that the pesky compiler doesn’t interfere with our algorithm. After compiling we can do a quick test of the program we get some positive results:

{% highlight powershell %}
PS > cl.exe /Od program.c
PS > .\program.exe 0 
even
PS > .\program.exe 4
even
PS > .\program.exe 3
odd
PS > .\program.exe 7
odd
{% endhighlight %}

However, after doing some further testing I found some problems:

{% highlight powershell %}
PS > .\program.exe 50
PS > .\program.exe 11
PS > .\program.exe 99
{% endhighlight %}

No output! It seems that the program only works for numbers under 11! Going back to the code we can find the issue right after the last if statement, we need more if statements!

Now, this is a time-memory tradeoff, but my time on this earth is limited so I decided to meta-program the if statements using a programmer program in a different programming language. To compensate for this cheating I decided to use the slowest language on the planet, Python (thanks to the visionary genius of Ross van der Gussom). 

{% highlight python %}
print("/* Copyright 2023. All unauthorized distribution of this source code")
print("   will be persecuted to the fullest extent of the law*/")

print("#include <stdio.h>")
print("#include <stdint.h>")
print("#include <stdlib.h>")

print("int main(int argc, char* argv[])")
print("{")
print("    uint8_t number = atoi(argv[1]); // No problems here")

for i in range(2**8):
    print("    if (number == "+str(i)+")")
    if i % 2 == 0:
        print("        printf(\"even\\n\");")
    else:
        print("        printf(\"odd\\n\");")

print("}")
{% endhighlight %}

Nice! Now we can generate a program that solves the even-odd problem for all 8-bit integers!
{% highlight powershell %}
PS > python programmer.py > program.c
PS > cl.exe /Od program.c
PS > .\program.exe 99
odd
PS > .\program.exe 50
even
PS > .\program.exe 240
even
PS > .\program.exe 241
odd
{% endhighlight %}

Would you look at that! It works flawlessly! Now, let’s scale it up to 16 bit!

{% highlight python %}
print("    uint16_t number = atoi(argv[1]); // No problems here")
…
for i in range(2**16):
{% endhighlight %}

This gives a nice and thick c file of around 130k lines. Nothing really when looking back at some of the code bases I’ve worked on over the years. Let’s compile!

{% highlight powershell %}
PS > python programmer.py > program.c
PS > cl.exe /Od program.c
PS > .\program.exe 21000
even
PS > .\program.exe 3475 
odd
PS > .\program.exe 3   
odd
PS > .\program.exe 65001
odd
PS > .\program.exe 65532
even
{% endhighlight %}

Beautiful! Our algorithm seems to scale with the data! The executable is around 2 MB, but that’s no match for my beefy gaming rig with a whopping 31.8 GB of memory. 

Now, 16 bit is a very cool bitwidth, but as we all know, 32 bit is the holy grail of computing and is the final bitwidth that we need to solve all practical engineering and scientific problems. After all, IPv4 is still standing stronger than ever, 60 years after it was deemed deprecated due to so called [“address exhaustion”](https://en.wikipedia.org/wiki/IPv4_address_exhaustion).

So without further ado, lets scale to our final size. 32 bit is only 65536 times as many numbers as 16 bit, what could go wrong?

{% highlight python %}
print("    uint32_t number = atoi(argv[1]); // No problems here")
…
for i in range(2**32):
{% endhighlight %}

So I let the mighty snake do its work and after getting a cup of coffee and getting back to check on the program 48 hours later I was left with a beautiful c file, almost 330 GB in size! Almost certainly among the largest c files in history. My fingers were trembling when I entered the next command, surely MSVC had never before encountered such powerful source code. After abusing the pagefile of my poor, powerful computer for half an hour the following was spat out:

{% highlight powershell %}
PS > cl /Od program.c
Microsoft (R) C/C++ Optimizing Compiler Version 19.32.31329 for x64
Copyright (C) Microsoft Corporation.  All rights reserved.

program.c
program.c(134397076): warning C4049: compiler limit: terminating line number emission
program.c(134397076): note: Compiler limit for line number is 16777215
program.c(41133672): fatal error C1060: compiler is out of heap space
{% endhighlight %}

Pathetic!

And not only did the compiler fail us, but when looking into the limits of the Portable Executable format (.exe) for windows, I discovered that it cannot handle more than a [measly 4 GB](https://stackoverflow.com/questions/6976693/what-is-the-maximum-size-of-a-pe-file-on-64-bit-windows)! With more than 4 billion comparisons needed to be encoded into the executable, this is a major obstacle for implementing our algorithm. Even if each comparison would use less than a single byte we would still be too heavy.

However, bad compilers and file formats should not stop us from achieving our dream. After all, all what a compiler does is writing some fancy machine code into a file and the file format is just some structure telling the OS how to put the binary code into memory. Really, we can do that ourselves.

Let's start by writing an IsEven function in [x86-64 assembly](https://en.wikipedia.org/wiki/X86_assembly_language) as it’s the native language of my Intel powered machine. It looks something like this:

{% highlight assembly %}
; Argument is stored in ECX, return value in EAX
XOR EAX, EAX ; Set eax to zero (return value for odd number)
CMP ECX, 0h ; Compare arg to 0 
JNE 3h ; Skip next two instructions if it wasn't equal
INC EAX ; It was even, set even return value (1)
RET ; Return
CMP ECX, 1h ; Compare arg to 1
JNE 2 ; Skip next instruction if not equal
RET ; Odd return value already in EAX, just RET
; add the next 2...2^32-1 comparisons here
RET ; Fallback return
{% endhighlight %}

Not really correct asm, but it doesn’t matter much, because we’re gonna compile it into machine code manually.

How did I do this? Well I jumped online, using a mix of my early life experience coding emulators and hacking and looked into the x86(-64) architecture manuals to figure out the correct opcodes and format for each instruction.

… Just kidding, that’s horrible. I asked ChatGPT what the correct [opcode](https://en.wikipedia.org/wiki/Opcode) was for each instruction and lucky for us it didn’t hallucinate any new extensions to x86-64.

So now we just write a “compiler” to output this code. Note that we will write the opcodes we got from the AI for the instructions directly. Here’s how it looks in our friend python:

{% highlight python %}
import struct

with open('isEven.bin', 'wb') as file:
   
    file.write(b"\x31\xC0")                     # XOR EAX, EAX

    for i in range(2**32):
        ib = struct.pack("<I", i)               # Encode i as 32 bit little endian integer

        file.write(b"\x81\xF9" + ib)            # CMP ECX, i

        if i%2 == 0:
            file.write(b"\x75\x03")             # JNE +3
            file.write(b"\xFF\xC0")             # INC EAX
            file.write(b"\xC3")                 # RET
        else:
            file.write(b"\x75\x01")             # JNE +1
            file.write(b"\xC3")                 # RET

    file.write(b"\xC3")                         # Fallback RET
{% endhighlight %}

While we somewhat deviated from the original vision of the TikTok post, the essence remains the same. We create a long, long, long list of if-statements to determine if any number is even or odd, ignoring any arithmetic operation that would help out.

Running this gives us a nice 40 GB file which contains all 4.2 billion comparisons needed to determine if any 32 bit number is even or odd! Now we just need to write our host program that can load and use these instructions. For added performance (it is very important), I decided to map the file into the address space instead of reading all of it. By doing this, we can just pretend that the entire file is already in memory and let the poor OS deal with fitting a 40 GB blob into virtual memory. After mapping the file with READ and EXECUTE permissions we can call into the code by using a function pointer. It looks like this:

{% highlight c %}
#include <stdio.h>
#include <Windows.h>
#include <stdint.h>

int main(int argc, char* argv[])
{
    uint32_t number = atoi(argv[1]); // No problems here

    // Open code file
    HANDLE binFile = CreateFileA(
                        "isEven.bin",
                        GENERIC_READ | GENERIC_EXECUTE, FILE_SHARE_READ,
                        NULL,
                        OPEN_EXISTING,
                        FILE_ATTRIBUTE_NORMAL,
                        NULL);
   
    // Get 64 bit size of file
    LARGE_INTEGER codeSize;
    GetFileSizeEx(binFile, &codeSize);

    // Create memory map of the file
    HANDLE mapping = CreateFileMapping(
                        binFile,
                        NULL,
                        PAGE_EXECUTE_READ,
                        0,
                        0,
                        NULL);

    // Get a pointer to the code
    LPVOID code = MapViewOfFile(
                    mapping,FILE_MAP_EXECUTE | FILE_MAP_READ,
                    0,
                    0,
                    codeSize.QuadPart);

    // Create a function that points to the code
    int (*isEven)(int) = (int(*)(int))code;

    if (isEven(number))
        printf("even\n");
    else
        printf("odd\n");

    CloseHandle(binFile);
}
{% endhighlight %}

And there we go! We now have everything to check if any 32 bit number is even or odd. Let’s take it for a spin:

{% highlight powershell %}
PS >.\program.exe 300
even
PS >.\program.exe 0
even
PS >.\program.exe 1000000
even
PS >.\program.exe 100000007
odd
PS >.\program.exe 400000000
even
PS >.\program.exe 400000001
odd
PS >.\program.exe 400000006
even
PS >.\program.exe 4200000000
odd <---- WRONG!
{% endhighlight %}

Almost! Seems like the algorithm has some issues with signedness, any value over 2^31 seems to give random results. Sad!

Let’s fix the final bug.

It turns out that atoi cannot deal with unsigned pureness, so it failed to parse our big boy numbers. Replacing it with strtoul fixes everything.

{% highlight c %}
uint32_t number = strtoul(argv[1], NULL, 10);// No problems here
{% endhighlight %}

{% highlight powershell %}
PS >.\program.exe 4200000000
even
PS >.\program.exe 4200000001
odd
{% endhighlight %}

As a side note, the program is amazingly performant. For small numbers the results are instantaneous and for the large number close to the 2^32 limit the result is still returned in around 10 seconds. Considering the computer has to read 40 GB of data from disk, map it to physical memory and then let the CPU has a rip of it without many chances of caching is honestly quite mind blowing. For reference, the computer is a Core i5 12600K with 32 GB memory and the files are residing on a M.2 SSD disk. While calculating, the peak read speed I saw from the SSD was around 800 MB/s (which doesn’t really make sense as that should give execution speeds at 40+ seconds, but computers are magical so who knows what is going on).

And there we have it! The Internet proven wrong once again, not only can you actually write a fully functioning and performant program in the manner of the TikTok post, but it’s also very fun. 
