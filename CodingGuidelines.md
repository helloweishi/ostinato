#summary Coding conventions and style guide for the Ostinato project

# Coding Guidelines #
Please follow these conventions while contributing code to the project. Existing code may not follow all of the below - when modifying such code, the thumb rule is that you should follow the convention in the surrounding code i.e. **be consistent with surrounding code**.

## General ##
  * Ostinato is cross-platform. Please ensure that the code compiles and works on all supported platforms. While that may sound like lot of work, in practice it's not. While writing code, make sure you use standard functions and for any platform dependency, use Qt provided APIs. If you are writing platform specific code though, please take the effort to build and run on all (or as many as possible) platforms - you can use VMs

  * Make sure code compiles for the minimal set and minimum versions of the pre-requisites as specified in the BuildingFromSource page. This is especially important when using Qt/Protobuf APIs as newer versions of the libraries may have introduced APIs not available in an older but supported version.

## Line Length/Endings ##
  * Lines exceeding 80-cols should be broken up into multiple lines.
  * Unix-style line-endings i.e. LF, not CR-LF

## Copyright/Licensing ##
  * All new files should include the standard copyright/licensing boilerplate at the top. As per project policy, copyright remains with the author, so use your name as the copyright holder. For large scale modifications to existing files - you can optionally choose to add a `"Copyright (C) year yourName"` line to the header

## Braces ##
  * Both starting and ending curly braces on its own line
  * Use braces for control statements only when the body contains more than one line

## Comments ##
  * Use C++ style comments only for single line comments
  * Multi line comments should use C-style comments

## Indents/Whitespace ##
  * Indent with spaces, **NOT** tabs
  * Four spaces per indent level
  * Use blank lines to group logically related statements, where suitable
  * Not more than one blank line
  * Single space after a keyword and before paranthesis
  * No space befoe comma/semicolon, single space after
  * Single space on both sides of operators except `'*'`, `'/'`
  * No space immediately after a starting paranthesis or before a closing paranthesis
```
// Example
for (i = 0; i <= 32; i++) 
{
    x = a + b*c - (d + e)/f;
}
```

## Naming Conventions ##
  * All identifiers use camelCase e.g. `packetLength`
  * Do **NOT** use underscores! (except in .proto files and their generated code)
  * Constants and enum values start with `'k'` e.g. `kMaxSize`
  * Classes start with a capitalisedLetter e.g. `TcpProtocol`
  * Objects and variables are simple camelCase e.g. `payloadProtocol, minLength`
  * Class member variables end with a underscore e.g. `count_`

## Basic Types ##
  * Use only standard C types (`int, short, char` etc.)
  * For specific bit-width based variables (e.g. 32-bit), use Qt provided types - `quint32, quint64` etc.
  * Do **NOT** define your own basic types

## Header Files ##
  * All header files must include a `#ifndef - #define - #endif` header guard to prevent multiple inclusion

  * A .h file must be standalone and therefore include all dependent header files to avoid hidden dependencies. The recommended order of #include (in both .h and .cpp) is therefore - local to global. Specifically,
    1. related .h file (of .cpp file)
    1. other ostinato .h files
    1. library .h files (Qt/Protobuf/Pcap etc.)
    1. standard system C/C++ headers
  * Separate groups of header files by a blank line
  * Within the group use alphabetic order