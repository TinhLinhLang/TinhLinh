# TinhLinh Programming Language

A modern, statically-typed programming language with Go-like syntax and LLVM backend.

## Features

- Static typing with type inference
- Module system
- LLVM-based code generation
- Self-hosting compiler (in progress)

## Project Structure

```
TinhLinh/
├── Compile/           # Compiler implementation
│   ├── Bytecode/      # Bytecode generation
│   ├── CodeGen/       # LLVM IR code generation
│   ├── Module/        # Module management
│   ├── Parsing/       # Lexer, Parser, AST
│   └── Compiler.li    # Main compiler entry point
├── Linh/              # Language runtime
├── Runtime/           # Runtime support
└── Tinh/              # Core language features
```

## Language Syntax

```tinh
// Variables with static typing
let x: int32 = 42
let name: str = "TinhLinh"

// Functions
func add(a: int32, b: int32): int32 {
    return a + b
}

// Control flow
if (x > 0 and x < 100) {
    print("In range")
}

while (x > 0) {
    x = x - 1
}
```

## Building

See parent repository for build instructions.

## Status

In Development - Working towards full self-hosting

## License

See LICENSE file in parent repository.

## Links

- Main Repository: [TinhLinhLang](https://github.com/TinhLinhLang)
- Documentation: Coming soon
- Website: Coming soon
