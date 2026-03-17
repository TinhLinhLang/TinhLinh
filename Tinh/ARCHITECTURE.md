# Type System Architecture

## Overview

The TinhLinh type system is a comprehensive, multi-layered architecture designed for self-hosting compiler development. It provides static type checking, type inference, and type-aware code generation.

## Architecture Layers

```
┌─────────────────────────────────────────────────────────┐
│                    Application Layer                     │
│              (Compiler, REPL, IDE Tools)                 │
└─────────────────────────────────────────────────────────┘
                            ↓
┌─────────────────────────────────────────────────────────┐
│                  TypedCodeGen.li                         │
│         Type-Aware LLVM IR Code Generation               │
│  • Typed expressions  • Auto casting  • Optimization     │
└─────────────────────────────────────────────────────────┘
                            ↓
┌─────────────────────────────────────────────────────────┐
│                  TypeChecker.li                          │
│           Advanced Type Checking & Validation            │
│  • Function sigs  • Struct defs  • Full AST checking     │
└─────────────────────────────────────────────────────────┘
                            ↓
┌─────────────────────────────────────────────────────────┐
│                TypeInference.li                          │
│              Type Inference & Compatibility              │
│  • Literal inference  • Binary ops  • LLVM mapping       │
└─────────────────────────────────────────────────────────┘
                            ↓
┌─────────────────────────────────────────────────────────┐
│                  TypeSystem.li                           │
│              Core Type System Foundation                 │
│  • Type repr  • Symbol table  • Basic checking           │
└─────────────────────────────────────────────────────────┘
```

## Component Details

### 1. TypeSystem.li - Foundation Layer

**Purpose**: Core type representation and symbol management

**Key Components**:
- `Type`: Represents all type kinds (int32, str, bool, float, any, void, ptr, struct, function)
- `Symbol`: Represents named entities with types
- `SymbolTable`: Manages scoped symbol lookup
- `TypeChecker`: Basic type checking infrastructure

**Responsibilities**:
- Type creation and comparison
- Symbol table operations (define, lookup)
- Scope management (enter/exit)
- Error collection

**Design Decisions**:
- Struct-based representation for simplicity
- Pointer-based type references for complex types
- Linked list of scopes for lexical scoping
- Separate error collection for batch reporting

### 2. TypeInference.li - Inference Layer

**Purpose**: Automatic type inference and compatibility checking

**Key Components**:
- `InferenceContext`: Tracks inferred types for expressions
- Literal inference functions
- Binary/unary operation type rules
- Type compatibility checker
- LLVM type mapper

**Responsibilities**:
- Infer types from literal values
- Determine result types for operations
- Check assignment compatibility
- Map TinhLinh types to LLVM types
- Generate type cast instructions

**Design Decisions**:
- Rule-based inference (no complex algorithms yet)
- Conservative compatibility (explicit over implicit)
- Direct LLVM mapping for efficiency
- Separate cast detection and generation

### 3. TypeChecker.li - Validation Layer

**Purpose**: Comprehensive type checking with semantic validation

**Key Components**:
- `FunctionSignature`: Complete function type information
- `StructDefinition`: Struct field layout and types
- `AdvancedTypeChecker`: Full program type checking

**Responsibilities**:
- Track all function signatures
- Track all struct definitions
- Validate function calls (arg count, types)
- Validate struct operations (field access, init)
- Two-pass checking (collect definitions, then validate)

**Design Decisions**:
- Two-pass algorithm for forward references
- Separate tracking for functions and structs
- Detailed error messages with context
- Integration with basic TypeChecker

### 4. TypedCodeGen.li - Code Generation Layer

**Purpose**: Type-aware LLVM IR generation

**Key Components**:
- `TypedCodeGen`: Enhanced code generator
- Typed expression generation
- Typed binary operations
- Typed function calls
- Type-based optimizations

**Responsibilities**:
- Generate correct LLVM types
- Insert casts automatically
- Optimize based on type information
- Integrate type checking with code generation

**Design Decisions**:
- Wrapper around existing CodeGen
- Type tracking per expression
- Lazy type inference (on-demand)
- Optimization hints based on types

## Data Flow

### Compilation Pipeline

```
Source Code
    ↓
Lexer → Tokens
    ↓
Parser → AST
    ↓
AdvancedTypeChecker
    ├─ Pass 1: Collect function/struct definitions
    ├─ Pass 2: Type check all statements
    └─ Result: Typed AST + Errors
    ↓
TypedCodeGen
    ├─ Type-aware expression generation
    ├─ Automatic cast insertion
    └─ Type-based optimization
    ↓
LLVM IR
```

### Type Checking Flow

```
AST Node
    ↓
TypeChecker_check_stmt()
    ├─ Variable decl → Define in symbol table
    ├─ Expression → TypeChecker_infer_expr()
    ├─ If/While → Check condition type
    ├─ Block → Enter/exit scope
    ├─ Function → Process signature, check body
    └─ Return → Check return type
    ↓
Type Errors or Success
```

### Type Inference Flow

```
Expression Node
    ↓
TypeChecker_infer_expr()
    ├─ Literal → infer_literal_type()
    ├─ Variable → Lookup in symbol table
    ├─ Binary → infer_binary_type()
    ├─ Unary → infer_unary_type()
    ├─ Call → Lookup function signature
    ├─ Field → Lookup struct definition
    └─ Struct init → Validate fields
    ↓
Type
```

## Type System Rules

### Type Hierarchy

```
Type
├── Primitive
│   ├── int32 (4 bytes, i32)
│   ├── bool (1 byte, i1)
│   ├── float (4 bytes, float)
│   └── void (no value)
├── Pointer
│   ├── str (ptr to i8 array)
│   ├── ptr (generic pointer)
│   └── any (generic pointer)
└── Composite
    ├── struct (named struct type)
    └── function (function type)
```

### Compatibility Rules

1. **Exact Match**: Same type kind and name
2. **Any Type**: `any` accepts all types
3. **Pointer Compatibility**: All pointers are compatible
4. **Numeric Compatibility**: int32 and float (with explicit cast)

### Casting Rules

1. **Implicit Casts**: None (all casts explicit)
2. **Pointer ↔ Integer**: ptrtoint / inttoptr
3. **Numeric Casts**: sitofp / fptosi (future)
4. **Struct Casts**: Not allowed

## Memory Layout

### Type Structure (24 bytes)
```
Offset | Field         | Size | Type
-------|---------------|------|--------
0      | kind          | 4    | int32
4      | name          | 8    | ptr
12     | is_pointer    | 1    | bool
13     | (padding)     | 3    | -
16     | element_type  | 8    | ptr
24     | return_type   | 8    | ptr
32     | param_types   | 8    | ptr
```

### Symbol Structure (32 bytes)
```
Offset | Field         | Size | Type
-------|---------------|------|--------
0      | name          | 8    | ptr
8      | type          | 8    | ptr
16     | is_function   | 1    | bool
17     | is_global     | 1    | bool
18     | (padding)     | 6    | -
```

### SymbolTable Structure (16 bytes)
```
Offset | Field         | Size | Type
-------|---------------|------|--------
0      | symbols       | 8    | ptr (array)
8      | parent        | 8    | ptr
```

## Performance Characteristics

### Time Complexity

- **Type Creation**: O(1)
- **Type Comparison**: O(1) for primitives, O(n) for structs
- **Symbol Lookup**: O(m) where m = scope depth
- **Type Inference**: O(1) for most operations
- **Full Type Check**: O(n) where n = AST nodes
- **Code Generation**: O(n) where n = AST nodes

### Space Complexity

- **Type**: 40 bytes per type
- **Symbol**: 32 bytes per symbol
- **SymbolTable**: 16 bytes + symbols array
- **FunctionSignature**: 32 bytes + params
- **StructDefinition**: 24 bytes + fields

### Optimization Opportunities

1. **Type Caching**: Cache frequently used types
2. **Symbol Interning**: Intern symbol names
3. **Lazy Inference**: Defer inference until needed
4. **Constant Folding**: Fold constants during type check
5. **Dead Code Elimination**: Remove unreachable code

## Error Handling

### Error Categories

1. **Type Mismatch**: Incompatible types in operation
2. **Undefined Symbol**: Variable/function not found
3. **Argument Count**: Wrong number of arguments
4. **Field Access**: Invalid field access
5. **Return Type**: Wrong return type

### Error Reporting

```tinh
TypeChecker_error(tc, "Type mismatch: expected int32, got str")
```

Errors are collected and reported at end:
```tinh
TypeChecker_print_errors(tc)
```

## Extension Points

### Adding New Types

1. Add type kind constant in TypeSystem.li
2. Add type creation function
3. Add type checking rules
4. Add LLVM mapping in TypeInference.li
5. Add code generation in TypedCodeGen.li

### Adding New Operations

1. Add inference rule in TypeInference.li
2. Add validation in TypeChecker.li
3. Add code generation in TypedCodeGen.li

### Adding New Language Features

1. Add AST node kind
2. Add type checking in TypeChecker_check_stmt()
3. Add type inference in TypeChecker_infer_expr()
4. Add code generation in TypedCodeGen

## Testing Strategy

### Unit Tests
- Test each type operation
- Test symbol table operations
- Test type inference rules
- Test compatibility checking

### Integration Tests
- Test full type checking pipeline
- Test with real AST nodes
- Test error reporting

### End-to-End Tests
- Compile real programs
- Verify generated LLVM IR
- Test self-compilation

## Future Enhancements

### Phase 1: Generics
- Generic type parameters
- Type constraints
- Monomorphization

### Phase 2: Advanced Inference
- Hindley-Milner type inference
- Type variables
- Unification algorithm

### Phase 3: Advanced Types
- Union types
- Intersection types
- Dependent types

### Phase 4: Memory Safety
- Lifetime analysis
- Borrow checking
- Ownership tracking

## References

- LLVM Type System: https://llvm.org/docs/LangRef.html#type-system
- Type Theory: Pierce, "Types and Programming Languages"
- Type Inference: Damas-Milner Algorithm
- Compiler Design: Aho, "Compilers: Principles, Techniques, and Tools"

---

**Document Version**: 1.0
**Last Updated**: March 9, 2026
**Status**: Complete
