# TinhLinh Compiler Architecture

## Overview

TinhLinh is a self-hosting compiler written in TinhLinh itself, targeting LLVM IR.

## Compilation Pipeline

```
Source Code (.li)
    ↓
[Lexer] → Tokens
    ↓
[Parser] → AST (Abstract Syntax Tree)
    ↓
[Semantic Analysis] → Typed AST
    ↓
[Code Generator] → LLVM IR (.ll)
    ↓
[LLVM] → Object File (.obj)
    ↓
[Linker] → Executable (.exe)
```

## Module Structure

### 1. Compile/Parsing/
**Lexer** (`Lexer/Lexer.li`)
- Tokenization of source code
- Keyword recognition
- Operator handling
- String literal processing

**Parser** (`Parser/Parser.li`)
- Recursive descent parsing
- Expression precedence handling
- Statement parsing
- AST construction

**AST** (`AST/ASTNode.li`)
- Node type definitions
- Tree structure
- Node manipulation utilities

**Semantic Analysis** (`Semantic/SemanticAnalyzer.li`)
- Type checking
- Symbol table management
- Scope resolution
- Error reporting

### 2. Compile/CodeGen/
**Code Generator** (`CodeGen.li`)
- LLVM IR generation
- Type mapping (TinhLinh → LLVM)
- Function code generation
- Expression code generation
- Statement code generation

### 3. Compile/Bytecode/
**Bytecode Emitter** (`BytecodeEmitter.li`)
- Bytecode generation (alternative backend)
- Instruction encoding
- Optimization passes

### 4. Compile/Module/
**Module Manager** (`ModuleManager.li`)
- Import/export handling
- Module resolution
- Dependency management

## Key Design Decisions

### Static Typing with `let`
```tinh
let x: int32 = 5    // Static typing
var x = 5           // Dynamic typing (future)
const PI = 3.14     // Constant
```

### Function-based Constants
Token and AST kinds are defined as functions instead of enums for bootstrap compatibility:
```tinh
func EOF(): int32 { return 0 }
func IDENTIFIER(): int32 { return 1 }
```

### Prefixed Struct Fields
Fields are prefixed to avoid name collisions during bootstrap:
```tinh
struct Token {
    var t_kind: int32
    var t_lexeme: str
    var t_line: int32
}
```

### Manual Type Offsets
Struct field offsets are manually calculated due to lack of full type system:
```tinh
func get_field_offset(name: str): int32 {
    if (str_eq(name, "t_kind")) { return 0 }
    if (str_eq(name, "t_lexeme")) { return 1 }
    // ...
}
```

## Type System

### Primitive Types
- `int32` - 32-bit signed integer
- `str` - String (pointer to char array)
- `bool` - Boolean (i1 in LLVM)
- `float` - Floating point
- `any` - Generic pointer type

### Composite Types
- `struct` - User-defined structures
- `array` - Fixed-size arrays (planned)
- `slice` - Dynamic arrays (planned)

## Code Generation Strategy

### Expression Generation
1. Traverse AST recursively
2. Generate temporary variables (`%t1`, `%t2`, ...)
3. Emit LLVM instructions
4. Return result register

### Statement Generation
1. Handle control flow (if/while/for)
2. Generate labels for branches
3. Emit phi nodes for SSA form
4. Handle variable declarations

### Function Generation
1. Emit function signature
2. Create entry block
3. Allocate local variables
4. Generate function body
5. Ensure return statement

## Current Limitations

### 1. Struct Initialization
**Problem**: Cannot determine field types at codegen time
**Impact**: Blocks self-compilation
**Solution**: Need full type system

### 2. Type Inference
**Problem**: Limited type inference, mostly manual annotations
**Impact**: Verbose code
**Solution**: Implement Hindley-Milner type inference

### 3. Error Messages
**Problem**: Basic error reporting
**Impact**: Hard to debug
**Solution**: Add source location tracking and better messages

## Future Improvements

1. **Full Type System**
   - Semantic analysis pass
   - Type inference
   - Generic types

2. **Optimization**
   - Dead code elimination
   - Constant folding
   - Inline expansion

3. **Standard Library**
   - Collections (Array, Map, Set)
   - I/O operations
   - String manipulation
   - Math functions

4. **Tooling**
   - Language server (LSP)
   - Debugger integration
   - Package manager

## References

- LLVM IR: https://llvm.org/docs/LangRef.html
- Compiler Design: Dragon Book
- Type Systems: TAPL (Types and Programming Languages)
