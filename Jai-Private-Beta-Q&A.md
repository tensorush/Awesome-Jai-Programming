# First Q&amp;A Discussion with Jai Private Beta Users (12.05.2020)
[![YouTube](https://img.shields.io/badge/YouTube%20sorce-red?logo=youtube)](https://www.youtube.com/watch?v=Wp9XD5FKZ2c)

[YouTube Video](https://www.youtube.com/watch?v=Wp9XD5FKZ2c)

[Twitch Stream](https://www.twitch.tv/videos/826727768)

## Part I. Jon's Notes on the Latest Features

### Module Handling

The two extremes are **the bulkiness & rigidity of C/C++ modules** and **the unpredictability & granularity of JavaScript libraries:**

- In C/C++ compiler-coupled header files evolved from serving as an interface with an OS to unreadable, impossible to refactor and often redundant high-level precompiled libraries;
- In JavaScript the dependencies are updated at runtime with all the new versions of its minuscule libraries. As a result, a year-old code will certainly be crashing down on you.

The solution is having a modules and a build subfolder in your app folder preserving the compatibility and putting the control in the hands of a programmer instead of a PL or an OS. 

The meta-programming options for module handling that Jai offers is a compromise between updating system headers and using your modules subfolder.

After the Beta Release, different options for module handling will, in part, make it easier for people to exchange module code and also write meta-programs to download modules from a server.

### Selective Module Compiling

It would be helpful to compile only the modules that are being used from both the internal and external libraries. This would require parsing everything, which can be fast, as opposed to type checking and code generation, which take longer.

### Meta-Program Checking Warnings

While compiling a meta-program runs to check the program with minimal compiler warnings. The warnings are implemented as a user-level library.

As of now, some basic things are not checked for, though, such as implicit casts:

```jsx
// Declare & assign a variable with type inference
zork: 69105;

// Assign a variable
zork = 69105;

// Common mistake: declaring & assigning a new variable,
// instead of assigning an outer-scope variable
zork: string = "in-joke";

if {
    zork := "69105";
}
```

## Part II. Q&A Discussion

### 1) Allocators

Allocators are functions under the Context Base struct (program context) which is defined in one of the compiler modules — Preload.jai. The default allocator has enough arguments to do free malloc, realloc, free the entire arena, add tracking to the allocation you've just made, etc. The modes supported now are ALLOCATE, RESIZE and FREE, but you can extend them with values of your own and write custom allocators.

Unlike custom allocators in C/C++ which imply changing all the source code, Jai allows to create wrappers of the default allocator and specify the necessary allocator while making the same alloc function call.

### 2) Mac OS Build and Android's ARM64 Support

These are on the list, but not officially supported right now. However, someone on the pilot team at one point managed to target the ARM64 Platform and it might still work if you provide the right LLVM target triple and build options.

### 3) Open Beta

It definitely shouldn't go open until the core features are implemented and until certain compiler bugs stop being reported, especially because of compile-time function execution, object packaging, etc. Before the Open Beta, there will probably be invite codes distributed by Private Beta users.

### 4) Reporting Bugs

Send them one at a time to Jon's e-mail.

### 5) Underspecified Language

For instance, if you have a bunch of C files in a folder, they don't make a C program, because you also need a definition of what these files are to compile them. So, now you need a makefile and for that, you need the Make language, which often isn't enough, so you need a couple of other programming languages just to do your C programming, not counting transferring to other OSs. This is exactly what downloading all the modules from the Internet before compiling is, and if they go missing or suffer an unlucky update — there appears a question mark in your program.

In an ideally specified language, though, all you need to compile is a program and a compiler.

### 6) Closures

Just like an object, a closure is a certain instantiated thing with certain data that has its own separate lifetime. And, honestly, with these costs, it's not worth doing if you can help it. However, it is still on the roadmap to provide closures in the future and avoiding what C++ did.

In C++ a closure is essentially a functor struct with an overloaded parenthesis operator, which means it's not actually a function type, so any time you need a negative function or a closure, you need that special thing. And, of course, in C++ a member function is also a different type, so you have C-style functions, member functions and closures which are totally different things.

The way to avoid it is by having the standard function type in a language be a fat pointer so that every function has a data pointer that it can refer to. For example, talking about allocators, you always pass an allocator and allocator data, because you might have several different heaps, the same allocator as a heap-handling function and allocator data as a pointer to the base of a heap, which is different for all those heaps. So, even without closures very often you want to have a data pointer be a function pointer type, which is also true with thread safety in general.

Historically, in C if you wanted to do Q-Sort and you needed more data, you would make your Q-Sort function read global variables with extra values, which is bad for thread safety. So you put the extra data into thread-local storage, which is a hit on performance and not without its own error tendencies.

### 7) Possible Changes

- Autocast;
- Dereferencing pointers;
- Implicit Casting: adding an implicit pointer dereference, which isn't scary with a compile-time check for it, just like an array bounds check;
- Later on, the plans are to do a "Question Everything" approach to syntax, especially in places where most mistakes occur.

### 8) Standard Meta-Program

The structure is to be figured out. Right now, the compiler distribution includes the Basic module with the compiler essentials and some side modules like Vulkan.

### 9) Code Hot Loading

Here you start getting into the OS or workflow details that can go wrong. Someone could do it as a meta-program, the goal is to make such things as easy as possible.

### 10) Good Code

Good code should be as general as possible and handle as many situations as possible, which also means that the code has to be as complex as possible. This reflects in libraries having too much redundant code. What should be done is combining the universality of simple code with the ad hoc complex code.

### 11) Struct Literals with Dynamic Values

The idea is to make a macro or a function for that. First, the macro system has to have more powerful features, though.

The two options are having universal struct literal syntax for either the ones with static or with dynamic values. The current idea is to leave literals as is and later see whether to add dynamic initialization through a macro or build it into the compiler.

### 12) Sockets

Not available yet as Thekla, Inc., apart from a Jai compiler, is currently developing a single-player Sokoban-type game, but it would definitely be great to have sockets together with a networking module some time later.

### 13) Single Underscore

Single underscore is currently employed as a variable like in C, but could possibly be changed to serve as a blank identifier for junk values like in Golang, if enough people want it.

### 14) User corrections

Typo fixes and decentralized content additions are very welcome.

### 15) Documentation

Launching a Wiki is an option.

### 16) Procedure Declaration Notes

Procedure Declaration Notes could be upgraded to a strongly typed version of notes and not only be string notes, but also struct literals. With such an upgrade namespace collisions would be handled correctly.

```jsx
// Procedure declaration notes can be put on declarations and structs
private_beta = true; @b1                     // String note
open_beta = false;   @@Type{Field: Variable} // Potential struct literal
```

### 17) Parallel Programming

Unannounced, but brewing feature.

### 18) Thread Functionality

Thread functionality is currently based on function calls and there are no real plans to change that.

Different languages have different threading primitives and most languages optimize for solving simple problems, not for helping with hard ones.

The hard problem is how to actually use all of the processors' cores. If you do something like co-routines or the like — these are not high-performance constructs.

Concurrency can be a rigorous computer science term or it can be a way to avoid saying parallelism when parallelism is what you actually care about in all practical cases. This reflects in many languages having all of the concurrency primitives, but really no parallelism help at all.

### 19) Complex Numbers & 16-bit Half-precision Float

Presently, no intention to add any of these. Scalars is really the point to stop, for all the rest you have structs. The 16-bit half-precision float is becoming popular on some hardware, however it doesn't matter too much right this second.

### 20) Debugging

Jon uses Remedy to debug the compiler. Long-term goal is to provide some default visualization tools that could augment debuggers.

### 21) Graphics Engine

The custom graphics engine that has evolved through the production of The Witness and is currently employed for the development of the Sokoban game will be given out for free with the stripped down version of the latest game, enough to play around, but not to duplicate the game.

### 22) Autocast

People complain about the type conversion compilation errors when porting games from C++ with the Autocast, which are caused by the syntax of " xx ".

### 23) Signed F64 Bias

There are several reasons for that default choice:

- Big enough not to worry about overflow;
- Enables to load 2+ GB files;
- Signed to avoid underflowing surprises.

That being said it is better to use smaller numbers in a data structure that gets frequently instanced. 

One of the big mistakes of C is not being able to identify the size of an integer, for example.

### 24) LLVM Backend

LLVM Backend will remain a supporting part of the Jai compiler for the foreseeable future, even though its a giant, expensive cargo aeroplane of modules. In addition, as of right now, the LLVM does Auto Vectorization, but Hand Vectorization is still to be added.

### 25) Intrinsics

The better idea is to avoid writing intrinsics and instead implement AVX-512 instructions by injecting some Inline Assembly into your Jai code.

### 26) Memory Safety

Rust language is so intrusive because it requires all of the information to ensure memory safety. The solution for a less demanding safety checking at runtime is to replace your heap allocator with the one that puts every allocation on its own pages, and on FREE it turns off read permission from the pages so that there's no way of dereferencing it after freeing. You can do similar things for buffer overruns, the drawbacks are that these are often slow and paid-for.

### 27) Compile-time Arguments for Libraries

There are module parameters already, they remain undocumented, though.

### 28) CMD Option to Switch Between Release and Debug Builds

The default meta-program could be taking some options and there wouldn't be extra things built into the compiler making it much lighter. That could really solve some things.

### 29) Building Libraries

You can already build libraries statically or DLL, same as C binaries. You would lose the power of the language, though. If a function is compiled, you can't polymorph it or bake the arguments and the meta program can't inspect it either.

### 30) Compile Speed

Most modern languages think they are using all your cores to compile by compiling a bunch of separate object files and linking them at the end, but they are not. Most of the time what most cores are doing is waste.

To all the game industry, if you want your game to run fast, the last thing that you would do is take all your scene data from different parts of a scene, write it out to disk, have a separate process read it in from disk, put it together, draw it on a screen and try to do that at 240 times a second. Like, nobody would do that. But for historical reasons, we have decided that's the way to compile things and convinced ourselves that it's efficient as it provides parallelism.

For example, compile time of C++ is largely dominated by including massively redundant header files and then deduplicating that massively redundant information at link time. But even without that it wouldn't be too fast because it's writing all those files out to disk and then linking them.

Jai could be made faster still by parallelizing the compiler, which isn't easy. 

### 31) Error Reporting

You need a slightly more sophisticated tracking within the compiler for good error reporting.

C/C++ reports multiple errors, but most of them are garbage, not revealing the real problem.

To report multiple error messages correctly you need to know what you did as a stop gap in order to continue compilation in the face of the first error and just not report further errors when that happens.

Right now there are parsing report errors if they occur in different files.

### 32) Entry Point

Like if there's a hashbang at the top of a file, then the default is no output. A better solution is to set a variable that we started with a hashbang and the default meta-program can, then, set the build type to no output.

### 33) Targeting Consoles

Yes, there are plans. Console vendors generally don't like someone talking about these things. The currently developed game under Jai will probably ship on multiple consoles.

### 34) Multi-platform Graphics Engine API

Yes!

### 35) Operator Overloading

C++ sure suffers from a lot of bad operator overloading. However, there's also good overloading when operating on vectors and matrices.

### 36) Removed Feature

Memory ownership on data structures was removed because you shouldn't have that many independently managed pieces of memory. It's bad to malloc a ton of nodes to remember membership or ownership about. It's much better to have temporary storage, where we don't worry about ownership, rather we track it.

### 37) Discriminated Union

Same as in C, it's a struct with a slot and an enum in it, which you happen to know. People who worry about discriminated unions tend to also worry about non-nullable references, which is probably the one to worry not having in the language right now. But actually that reference is going to be null at some point anyway.

### 38) X64 Compile-time Execution Environment

If meaning to interpret x64 machine code, then — no. But if meaning to JIT compile and run that — it's feasible.

### 39) Strong Typedefs

There are no right now, but they will be added.
