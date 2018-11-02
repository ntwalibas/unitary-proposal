# Avalon - a classical/quantum programming language

This proposal to the unitary fund is for exploring the creation of a classical/quantum programming language.  
The [Avalon Programming Language](https://github.com/avalon-lang/avaloniq) is a working programming language that allows the creation of qubits and the application of quantum gates to those qubits.  
In seeking funding, it is my goal to port the existing interpreter to QUIL since as an instruction set architecture it is the closest that Avalon will get to compile for real hardware in the near future.  
The larger programming community benefits by having a programming language  similar to Python in syntax and to Haskell in semantics (up to a certain level : algebraic data types).

## Introduction

Programming quantum computers currently requires the use of SDKs largely written in Python.  
While this is convinient for immediate prototyping, it doesn't lend itself for programming in the large.  
Microsoft has made excellent work with Q# but it requires linking with a C# program to act as driver.  
In one language, Avalon provides both the classical components as well as quantum constructs.

As of this writing, Avalon allows the creation of 1-Qubit quantum gates and in a week's time, it will be possible to have controlled gates parametrized by 1-Qubit gates.
Qubit data types have a one-to-one correspondance with bit types which is excellent since when generating QUIL code, I can focus on those two data types to get started.

The Avalon programming language is a statically typed language with type inference where it is possible to deduce the complete type of a value else the type must be provided (such as using the `None` value constructor).
It is based on algebraic data types with pattern matching to deconstruct values. Unlike Haskell, Avalon doesn't offer referential transparency. Except for select data types, the values contained
in variables can be changed.  

The syntax is close to that of Python while the declaration of type constructors is close to that of Haskell in syntax.  
This should make the language easier on newcomers to get acquainted with.

## Objective in creating the language

While SDKs are certainly useful is their own right in offering an interface where no languages are available, having a fully-featured language that directly targets the stack of interest
is most certainly preferable since it offers a clear syntax, predictable semantics that are made visible by the syntax and enforced by the compiler (e.g. in Avalon, a variable containing qubits cannot be an r-value to an assignment expression in keeping with the no-cloning theorem).  

And the most important aspect of preferring a fully featured programming language over an SDK is the ease of learning for users already familiar with similar languages.  
And that is the primary objective of Avalon: **make quantum computers programming available to the regular developer**.

It goes without saying that higher-level programming languages are much easier to work with than assembly code.

## Features to be ported to the Rigetti stack

Currently, the language offers many native types: qubits, bits, integers, double-precision floating point numbers, strings, tuples, lists and maps.  
If we consider the Rigetti 8Q-Agave quantum processor with 8 qubits as a reasonable target for code generation, 
a lot of the native data types would not fit into available registers.  
Therefore, as initial native data types, only qubits, bits and integers will be ported.

The language also offers the possiblity to create user data types (even generic ones) and considering that the whole generated program is to run on the Rigetti stack,
it is not wise to enable this feature at the moment.

Avalon functions can also be parametrized (as in Haskell) and they even have a feature we call *extended overloading* where two functions with the same names and parameters can have different return types.
Depending on what I am able to accomplish (efficiently) with the Rigetti stack, some of those features may be disabled.

## Timeline

In 4 months, users on Linux systems will be able to generate QUIL code and run it on the Rigetti stack. They can already test the interpreter as of this writing.  
The 4 months will be used as follow:  

- 1 month to port native data types
- 2 months to port function declarations and variable declarations.
- 1 month to write a tutorial and a reference documentation (this has already started at [Avalon documentation](https://avalon-lang.readthedocs.io)).

## Challenges

What exactly are the semantics of quantum constructs in a high-level programming language?  
At the moment, here are the existing semantics and the questions they raise where applicable:

- Variables initialized with qubits cannot modified later.
- A variable with qubits cannot be used as an r-value of an assignment (this in keeping with no-cloning).
- References to qubits cannot be dereferenced (also in keeping with no-cloning).
- A variable with qubits cannot be passed by value to a function only by reference. This restriction exists as well to enforce the no-cloning theorem 
but also the language semantics. A quantum variable simply initializes a quantum register with specific data. To work with that data, we use references 
since it is external data. But the user might ask: why then not declare the quantum variable as a reference to begin with qubits (a la C++ *constant reference to temporary*)?
- Qubits can not be used in value constructors nor can they parametrize generic type constructors (such as the `maybe(a)` type). This restriction exists to simplify things but 
if we were to lift it, how do we manage the memory layout of value constructors?
- Only 1-Qubit gates and controlled gates of 1-Qubit gates can be created by the user. All other gates can derive from those. As is often said, a language cannot be made more powerful 
by reducing the number of features it offers. Is there wisdom in allowing the creation of arbitrary (unitary) gates as QUIL allows?
- All bit and qubit types are static in the sense that their size is fixed at compile time. How do we evolve this without having a noodle soup of data types? Are refinement types the answer? What is the amount of work required to make that a reality? Shall we go all the way to dependent types?
- The language strives to limit the number possible runtime errors (no allowance of undefined behavior for instance) through the type system, how do we use this to
disallow the application of unitary gates on a qubit after it has been measured?

Here is what is missing but is absolutely important to make the language more useful:

- Multi-qubits types are not currently indexable. This is important in order allow applying controlled gates to the same variable, for instance, without splitting a single variable into 1-Qubit variables. What are the semantics of indexing?

## Post-completion of the project

Understanding the semantics of quantum types and functions that work on them is of interest.  
Publishing a paper that details the decisions that guided the semantics of Avalon is not without merit and something I look forward to doing at the end of the project.

## References

[Quil Instruction Set Architecture](https://arxiv.org/abs/1608.03355)  
[Avalon Programming Language](https://github.com/avalon-lang/avaloniq)
