# Mini C Compiler

This project is a mini compiler for WLP4, a small, C-like procedural language. It processes WLP4 source code (defined below) through a full compilation pipeline, from lexical analysis to the final generation of executable MIPS machine code.

## The WLP4 Language

WLP4 is a simplified, statically-typed language designed with a minimal feature set to focus on core compiler concepts. It is a heavily stripped down version of C.

*   **Main Entry Point**: Programs must define a `wain` function as their entry point, which can accept either two `int` parameters or an `int*` and an `int`
*   **Data Types**: The language is strictly typed, supporting `int` (integers) and `int*` (integer pointers)
*   **Operators**: It includes basic arithmetic (`+`, `-`, `*`, `/`, `%`), relational (`==`, `!=`, `<`, `>`, `<=`, `>=`), and pointer dereferencing/address-of (`*`, `&`) operators. Logical operators like `&&` or `||` are not supported and must be implemented with nested `if` statements
*   **Control Flow**: Program flow is controlled using `if-else` blocks and `while` loops. More complex structures like `else if` or `for` loops are not part of the language
*   **Memory Management**: Dynamic memory is managed via `new int[n]` for allocation and `delete [] expr` for deallocation. A failed allocation returns `NULL` rather than throwing an exception
*   **I/O**: Console input and output are handled by three built-in functions: `getchar()`, `putchar(c)`, and `println(i)`

## Compiler Architecture

The compiler is implemented as a multi-stage pipeline, where the output of each stage serves as the input for the next.

### 1. Lexical Analysis (Scanning)
The process begins by scanning the raw WLP4 source code and converting it into a stream of tokens. A deterministic finite automaton (DFA) implements the **maximal munch** algorithm to ensure the longest valid token is always matched 
*   Key files: `wlp4ScanningDFA.cc`, `wlp4MaximalMunch.cc`

### 2. Syntactic Analysis (Parsing)
The parser receives the token stream and validates it against the language's context-free grammar (CFG). If the code is syntactically valid, a parse tree is constructed to represent the program's structure
*   Key files: `wlp4Parser.cc`, `wlp4CFG.cc`, `treeNode.cc`

### 3. Semantic Analysis
The semantic analyzer traverses the parse tree to enforce context-sensitive rules, such as:
*   **Type Checking**: Verifying that operators and function calls are used with compatible data types.
*   **Scope and Declarations**: Ensuring that all variables and procedures are declared before use and respecting scope rules
*   Key files: `wlp4SemanticAnalysis.cc`

### 4. MIPS Code Generation
After the code is fully validated, the parse tree is translated into MIPS assembly code. This stage maps the language's high-level constructs to low-level MIPS instructions
*   Key files: `wlp4Synthesis.cc`, `asmCodeGenerator.cc`

### 5. MIPS Assembler
In the final stage, a two-pass assembler converts the generated MIPS assembly into executable binary machine code
*   Key files: `assembler.cc`, `mipsSynthesis.cc`, `mipsMaximalMunch.cc`

## Project Structure

The codebase is organized by compiler stage and functionality:
*   **`main.cc`**: The main driver that orchestrates the compilation pipeline
*   **`wlp4...` files**: The compiler front-end, responsible for processing WLP4 source code and creating MIPS assembly (scanning, parsing, semantic analysis, code generation)
*   **`mips...` files**: Components for the MIPS-specific back-end, including the assembler and machine code generation
*   **`...Instruction.cc` / `.h` files**: Define structures for parsing and representing different formats of MIPS instructions (e.g., branches, register-based operations)
*   **`asmCodeGenerator.cc`**: A dedicated module for encapsulating assembly code generation logic
*   **`assembler.cc`**: The MIPS-to-binary assembler
