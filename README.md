<div align="center">

## A Portable DLL/SO Solution


</div>

### Description

The following section is a survey and overview of the means to provide portable &#8216;plug-in&#8217; capabilities for applications. &#8216;Plug-In&#8217;s are compiled libraries from which symbols (functions, variables, objects) can be imported during run-time.
 
### More Info
 


<span>             |<span>
---                |---
**Submitted On**   |
**By**             |[Gushing Wound](https://github.com/Planet-Source-Code/PSCIndex/blob/master/ByAuthor/gushing-wound.md)
**Level**          |Intermediate
**User Rating**    |4.2 (46 globes from 11 users)
**Compatibility**  |C\+\+ \(general\), Microsoft Visual C\+\+, Borland C\+\+, UNIX C\+\+
**Category**       |[System Services/ Functions](https://github.com/Planet-Source-Code/PSCIndex/blob/master/ByCategory/system-services-functions__3-23.md)
**World**          |[C / C\+\+](https://github.com/Planet-Source-Code/PSCIndex/blob/master/ByWorld/c-c.md)
**Archive File**   |[](https://github.com/Planet-Source-Code/gushing-wound-a-portable-dll-so-solution__3-746/archive/master.zip)





### Source Code


<B><FONT SIZE=7><P ALIGN="CENTER"><A NAME="_Toc492199092">DLL's and SO's: Portable Solutions</A></P></DIR>
</DIR>
</B></FONT><FONT SIZE=2><P>The following section is a survey and overview of the means to provide &#8216;plug-in&#8217; capabilities for applications. &#8216;Plug-In&#8217;s are compiled libraries from which symbols (functions, variables, objects) can be imported during run-time. On the Windows NT platform, these files are referred to as "dynamically linked libraries" and carry the extension ".dll". On the SGI machines (IRIX/UNIX), these files are referred to as "shared objects" and carry the extension ".so". The functionality and interface to these objects are very similar on both platforms. This section will overview both platforms&#8217; implementation, highlighting similarities and differences between the two formats, and demonstrating preprocessor macros which will allow source files to compile correctly on both platforms. Only an overview is given as the actual mechanism each platform uses to implement this functionality is beyond the scope of this section. The usage and creation of these files will be described from the environment of Microsoft Visual Studio for Windows NT and a command line environment for SGI. This overview information is provided only to meet the requirements of the RFS simulator, and is not an in-depth discussion of the complete capabilities of DLL and SO libraries.</P>
</FONT><B><FONT FACE="Arial" SIZE=4><P><A NAME="_Toc463531285"><A NAME="_Toc492199093">Overview</A></A></P>
</B></FONT><FONT SIZE=2><P>A dynamic link library (DLL) and a shared object (SO) provide the functionality needed to provided &#8216;plug-in&#8217; capabilities to an application on the NT and UNIX platforms respectively. Implementation on these platforms is very similar, often differing by only a function name or a include file. The process to compile a linked library on both platforms is as follows</P>
<P>Creating, Linking and Compiling the DLL or SO:</P>
<UL>
<LI>Write the code for the DLL or SO. Identify the functions or variables that are to be available for the calling process. </LI>
<LI>Compile the source code into an object file. </LI>
<LI>Link that object file into either a DLL or SO.</LI></UL>
<P>Accessing the DLL or SO from a Calling Process:</P>
<UL>
<LI>Load the DLL or SO </LI>
<LI>Get a pointer to the exported function or variable </LI>
<LI>Utilize the exported function or variable </LI>
<LI>Close the library</LI></UL>
</FONT><B><FONT FACE="Arial" SIZE=4><P><A NAME="_Toc463531286"><A NAME="_Toc492199094">Creating, Linking, and Compiling the DLL or SO</A></A></P><DIR>
<DIR>
</FONT><FONT SIZE=2><P><A NAME="_Toc463531287">Source Files</A></P>
</B><P>All Unix object files are candidates for inclusion into a shared object library. No special export statements need to added to code to indicate exportable symbols, since all symbols are available to an interrogating process (the process which loads the SO/DLL).</P>
<P>In Windows NT, however, only the specified symbols will be exported (i.e., available to an interrogating process). Exportable objects are indicated by the including the keyword <I>&#8216;__declspec(dllexport)&#8217;</I>. The following examples demonstrate how to export variables and functions.</P>
</FONT><I><FONT SIZE=2 COLOR="#0000ff"><P>__declspec( dllexport ) void MyCFunc();</I></FONT><FONT SIZE=2>&#9;</FONT><I><FONT SIZE=2 COLOR="#008000">/* exporting function MyCFunc */</P>
</FONT><FONT SIZE=2 COLOR="#0000ff"><P>__declspec (dllexport) int MyVariable; </I></FONT><FONT SIZE=2>&#9;</FONT><I><FONT SIZE=2 COLOR="#008000">/* exporting variable MyVariable */</P>
</I></FONT><B><FONT SIZE=2><P><A NAME="_Toc463531288">Linking Objects into DLL/SO</A></P>
</B><P>Both DLL and SO files are linked from compiled object files. Under Visual Studio 6.0, a "Win32 DLL Project" can be created. All objects created from the source files in the project will be linked into the target DLL. Under Unix, the linking of object code into a shared library can be accomplished using the <I>&#8216;-shared&#8217;</I> option of the linker executable <I>&#8216;ld&#8217;</I>. For example, the following command line can be used:</P>
</FONT><FONT FACE="Courier New" SIZE=1 COLOR="#0000ff"><P>ld -64 -shared greetings.o -o greetings.so</P>
</FONT><FONT SIZE=2><P>This command (from the man pages) will compile a greetings.so shared object library from the greetings.o object file. The option <I>&#8216;-64&#8217;</I> indicates 64 bit code, and affects the search path for the library (see below).</P></DIR>
</DIR>
</FONT><B><FONT FACE="Arial" SIZE=4><P><A NAME="_Toc463531289"><A NAME="_Toc492199095">Accessing the DLL or SO from a Calling Process</A></A></P>
</FONT><FONT SIZE=2><P><A NAME="_Toc463531290">System Header and Library Files</A></P>
</B><P>To use the shared objects in Unix, the include directive <I>&#8216;#include &lt;dlfcn.h&gt;&#8217;</I> must be used. Under Windows NT, the include directive <I>&#8216;#include &lt;windows.h&gt;&#8217;</I> must be used.</P>
<B><P><A NAME="_Toc463531291">Loading the DLL/SO</A></P>
</B><P>In Unix, loading the SO file can be accomplished from the function <I>dlopen()</I>. The function prototype is</P><DIR>
<DIR>
</FONT><FONT FACE="Courier New" SIZE=1 COLOR="#0000ff"><P>void* dlopen( const char *pathname, int mode )</P></DIR>
</DIR>
</FONT><FONT SIZE=2><P>The argument pathname is either the absolute or relative (from the current directory) path and filename of the .SO file to load. The argument mode is either the symbol RTLD_LAZY or RTLD_NOW. RTLD_LAZY will locate symbols in the file given by pathname as they are referenced, while RTLD_NOW will locate all symbols before returning. The function <I>dlopen()</I> will return a pointer to the handle to the opened library, or NULL if there is an error.</P>
<P>Under Windows, the function to load a library is given by</P><DIR>
<DIR>
</FONT><FONT FACE="Courier New" SIZE=1 COLOR="#0000ff"><P>HINSTANCE LoadLibrary( LPCTSTR lpLibFileName );</P></DIR>
</DIR>
</FONT><FONT SIZE=2><P>In this case, the filename of executable module is pointed to by the argument lpLibFileName. This function returns a handle to the DLL (of type HISTANCE), or NULL if there is an error.</P>
<B><P><A NAME="_Toc463531292">Search Paths for Dynamic/Shared Libraries</A></P>
</B><P>Under Unix, the shared object will be searched for in the following places.</P>
<OL>
<LI>In the directory specified by the pathname argument to <I>dlopen()</I> if it is not a simple file name (i.e. it contains a character). In this case, the exact file is the only placed searched; steps two through four below are ignored.</LI>
<LI>In any path specified via the -rpath argument to ld(1) when the executable was statically linked.</LI>
<LI>In any directory specified by the environment variable LD_LIBRARY_PATH. If LD_LIBRARY_PATH is not set, 64-bit programs will also examine the variable LD_LIBRARY64_PATH, and new 32-bit ABI programs will examine the variable LD_LIBRARYN32_PATH to determine if an ABI-specific path has been specified. All three of these variables will be ignored if the process is running <I>setuid</I> or <I>setgid</I>.</LI>
<LI>The default search paths will be used. These are /usr/lib:/lib for 32-bit programs, /usr/lib64:/lib64 for 64-bit programs, and /usr/lib32:/lib32 for new 32-bit ABI programs.</LI></OL>
<P>&nbsp;</P>
<P>Under Windows, the shared object will be searched for in the following places.</P>
<OL>
<LI>The directory from which the application loaded. </LI>
<LI>The current directory. </LI>
<LI>Windows 95 and Windows 98: The Windows system directory. Use <I>theGetSystemDirectory</I> function to get the path of this directory.</LI>
<LI>Windows NT: The 32-bit Windows system directory. Use the <I>GetSystemDirectory</I> function to get the path of this directory. The name of this directory is SYSTEM32. </LI>
<LI>Windows NT: The 16-bit Windows system directory. There is no function that obtains the path of this directory, but it is searched. The name of this directory is SYSTEM. </LI>
<LI>The Windows directory. Use <I>theGetWindowsDirectory</I> function to get the path of this directory. </LI>
<LI>The directories that are listed in the PATH environment variable.</LI></OL>
<B><P><A NAME="_Toc463531293">Run-Time Access of Symbols from the DLL/SO</A></P>
</B><P>Under Unix, symbols can be referenced from a SO once the library is loaded using <I>dlopen()</I>. The function <I>dlsym()</I> will return a pointer to a symbol in the library.</P><DIR>
<DIR>
</FONT><FONT FACE="Courier New" SIZE=1 COLOR="#0000ff"><P>void* dlsym( void* handle, const char *name);</P></DIR>
</DIR>
</FONT><FONT SIZE=2><P>The handle argument is the handle to the library returned by <I>dlopen()</I>. The name argument is a string containing the name of the symbol. The function returns a pointer to the symbol if it is found, and NULL if not or if there is an error.</P>
<P>Under Windows, the functions can be accessed with a call to <I>GetProcAddress()</I>.</P><DIR>
<DIR>
</FONT><FONT FACE="Courier New" SIZE=1 COLOR="#0000ff"><P>FARPROC GetProcAddress( HMODULE hModule, LPCSTR lpProcName);</P></DIR>
</DIR>
</FONT><FONT SIZE=2><P>The argument hModule is the handle to the module returned from <I>LoadLibrary()</I>. The argument lpProcName is the string containing the name of the function. This procedure returns the function pointer to the procedure is successful, else it returns NULL.</P>
<P>For example, suppose the function <I>getProductOf()</I> is defined in a Unix .so file, and is exported in a Windows .dll file. The prototype of the function is given by</P><DIR>
<DIR>
</FONT><FONT FACE="Courier New" SIZE=1 COLOR="#0000ff"><P>float getProductOf( float number1, float number2 );</P></DIR>
</DIR>
</FONT><FONT SIZE=2><P>A <I>typedef</I> to the function pointer of this function is given by</P><DIR>
<DIR>
</FONT><FONT FACE="Courier New" SIZE=1 COLOR="#0000ff"><P>typedef float (*LPFunctionType)(float, float);</P></DIR>
</DIR>
</FONT><FONT SIZE=2><P>This, of course, is the same for both platforms. Once the DLL/SO is loaded as described in the previous section, access to the function looks like (m_libraryHandle is the handle returned by <I>dlopen</I> in Unix or <I>LoadLibrary</I> in Windows)</P><DIR>
<DIR>
</FONT><FONT FACE="Courier New" SIZE=1 COLOR="#0000ff"><P>LPFunctionType functionptr;</P>
<P>&#9;</P>
<P>// UNIX</P>
<P>functionptr = (LPFunctionType)dlsym(m_libraryHandle, " getProductOf");</P>
<P>// NT</P>
<P>functionptr = (LPFunctionType) GetProcAddress(m_libraryHandle, "getProductOf");</P></DIR>
</DIR>
</FONT><B><FONT SIZE=2><P><A NAME="_Toc463531294">Closing the DLL/SO</A></P>
</B><P>Closing the library is accomplished in Unix using the function <I>dlclose</I>, and in Windows using the function <I>FreeLibrary</I>. <B>Note that these function return either a 0 or a non-zero value, but Windows returns 0 if there is an error. Unix returns 0 if successful.</P>
</B><P>In Unix, the library is closed with a call to <I>dlclose</I>.</P><DIR>
<DIR>
</FONT><FONT FACE="Courier New" SIZE=1 COLOR="#0000ff"><P>int dlclose( void *handle );</P></DIR>
</DIR>
</FONT><FONT SIZE=2><P>The argument handle is the handle to the opened SO file (the handle returned by <I>dlopen</I>). This function returns 0 if successful, a non-zero value if not successful.</P>
<P>In Windows NT, the library is closed using the function Free Library.</P><DIR>
<DIR>
</FONT><FONT FACE="Courier New" SIZE=1 COLOR="#0000ff"><P>BOOL FreeLibrary( HMODULE hLibModule );</P></DIR>
</DIR>
</FONT><FONT SIZE=2><P>The argument hLibModule is the handle to the loaded DLL library module. This function returns a non-zero value if the library closes successfully, and a 0 if there is an error.</P>
</FONT><B><P><A NAME="_Toc463531295"><A NAME="_Toc492199096">MultiPlatform Example:</A></A></P>
</B><FONT SIZE=2><P>This example is a modification of the man pages of <I>dlopen()</I> and has not been compiled for testing. Note that this code is for example purposes, and is not robust (error-handling routines have been omitted, etc).</P>
<P>dltry.c:<BR>
------------------</P><DIR>
<DIR>
</FONT><FONT FACE="Courier New" SIZE=1 COLOR="#0000ff"><P>#ifdef FOR_UNIX</P>
<P>#include &lt;dlfcn.h&gt;</P>
<P>#define GetFunctionFromModule dlsym</P>
<P>#define CloseModule dlclose</P>
<P>typedef (void*) LpHandleType;</P>
<P>#else</P>
<P>#include &lt;windows.h&gt;</P>
<P>#define GetFunctionFromModule GetProcAddress</P>
<P>#define CloseModule FreeLibrary</P>
<P>typedef HINSTANCE LpHandleType;</P>
<P>#endif</P>
<P>typedef int (*xamplefuncptr)(int);</P>
<P>int main()</P>
<P>{</P>
<P>LpHandleType handle;</P>
<P>int i;</P>
<P>xamplefuncptr fptr;</P>
<P>/* open the library- machine specific */</P>
<P>#ifdef FOR_UNIX</P>
<P>handle = dlopen("./greetings.so", RTLD_LAZY);</P>
<P>#else</P>
<P>handle = LoadLibrary("./greetings.dll");</P>
<P>#endif</P>
<P>/* get function is same thanks to macros */</P>
<P>fptr = (xamplefuncptr) GetFunctionFromModule(handle, "greetings");</P>
<P>&nbsp;</P>
<P>i = (*fptr)(3);</P>
<P>/* close the module */</P>
<P>/* note, CloseModule returns 0 if error on NT, if success on UNIX */</P>
<P>CloseModule( handle );</P>
<P>return 0;</P>
<P>}</P></DIR>
</DIR>
</FONT><FONT SIZE=2><P>greetings.c:<BR>
----------------------</P><DIR>
<DIR>
</FONT><FONT FACE="Courier New" SIZE=1 COLOR="#0000ff"><P>#include &lt;stdio.h&gt;</P>
<P>#ifdef FOR_UNIX</P>
<P>int greetings(int num_greetings)</P>
<P>#else</P>
<P>#include &lt;windows.h&gt;</P>
<P>int __declspec(dllexport) greetings(int num_greetings)</P>
<P>#endif</P>
<P>{</P>
<P>int i;</P>
<P>for (i=0; i &lt; num_greetings; i++)</P>
<P>printf ("hello world0);</P>
<P>return 1;</P>
<P>}</P></DIR>
</DIR>
</FONT><FONT SIZE=2><P>Command Line- UNIX:<BR>
----------------------</P><DIR>
<DIR>
</FONT><FONT FACE="Courier New" SIZE=1 COLOR="#0000ff"><P>% cc -32 -c -DFOR_UNIX dltry.c greetings.c</P>
<P>% ld -32 -shared greetings.o -o greetings.so</P>
<P>% cc -32 dltry.o</P>
<P>% setenv LD_LIBRARY_PATH .</P>
<P>% a.out</P>
<P>hello world</P>
<P>hello world</P>
<P>hello world</P>
</FONT><FONT SIZE=2><P>&nbsp;</P></DIR>
</DIR>
</FONT>

