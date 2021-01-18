---
layout: post
title: Programming in C# for People Who Like Vim
tags: c-sharp, vim
---

As somoene working in the mobile gaming industry, I wanted to learn more about the nuts and bolts of game making. To get hands-on experience, I am taking [C# Programming for Unity Game Development](https://www.coursera.org/specializations/programming-unity-game-development) from [Cousera](www.coursera.org/). The first step in learning any programming language is to setup the development environment. The course instructor suggested using either [Visual Studio](https://visualstudio.microsoft.com/downloads/) or [MonoDevelop](https://visualstudio.microsoft.com/downloads/), but I am a fan of Vim. In this post, I will document my initial experience in programming with C# from the terminal on macOS.

1. Installing Mono
   
   In order to compile C# code, you would need Mono, ["a free and open source .NET Framework-compatible software framework"](https://en.wikipedia.org/wiki/Mono_(software)). Using brew, you can install Mono via the following command:

   ``` bash
   brew install mono
   ```

2. Hello World on C#
   
   Here is my first program in C#:


   ```csharp
   using System;
   
   namespace HelloWorld 
   {
       class MainClass
       {
           public static void Main(string[] args)
           {
               Console.WriteLine("Hello, World!");
           }
       }
   }
   ```

   You can compile it with the *csc* command:
   ``` bash
   csc HelloWorld.cs
   ```
   Now, you can run the code with *mono* command
   ``` bash
   mono HelloWorld.exe
   ```

3. Compiling with DLL

    If the project you are working on has [dynamic link library (DLL)](https://docs.microsoft.com/en-us/troubleshoot/windows-client/deployment/dynamic-link-library), you can compile the code with the [*-reference* option](https://docs.microsoft.com/en-us/dotnet/csharp/language-reference/compiler-options/listed-by-category)

    ``` bash
    csc -reference:foo.DLL Program.cs
    ```

## Final Thoughts

So far, writing C# code on macOS seems straighforward. Though, the course invovles using [Unity](https://unity.com/). I might end up needing the functionalities in Visual Studio later.


