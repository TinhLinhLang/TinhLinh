# Tinh Linh Programming Language

**Tinh Linh** is a modern programming language developed by the Sao Tin Developer Team, aiming for simplicity, modern syntax, and extensibility.

## Repository Overview

This repository (`https://github.com/TinhLinhLang/TinhLinh`) contains the complete Tinh Linh language implementation:

> **📝 Note**: For complete development history and repository evolution details, see [REPOSITORY_HISTORY.md](REPOSITORY_HISTORY.md).

- **Tinh**: AOT (Ahead-of-Time) compiler with LLVM backend - optimized for production deployment
- **Linh**: Interpreter implementation - provides fast development and testing capabilities
- **Shared Components** (`TinhLinh/Compile/`): Common code used by both Tinh and Linh
  - Lexer, Parser, AST
  - Semantic analysis
  - Module system
  - Type system
- **Lithium (.li)**: The language specification - defines syntax and semantics

## Architecture

```
TinhLinh/
├── Compile/          # Shared components (Lexer, Parser, AST, Semantic)
│   ├── Parsing/      # Lexer, Parser, AST
│   ├── Semantic/     # Semantic analysis
│   ├── Module/       # Module system
│   └── TypeInfo/     # Type information
├── Tinh/             # Compiler-specific (LLVM CodeGen, AOT compilation)
│   └── (CodeGen, Runtime, etc.)
├── Linh/             # Interpreter-specific (Bytecode, VM, etc.)
│   └── (Interpreter implementation)
└── README.md         # This file
```

## Features

- **Dual execution modes**: Compile (Tinh) or Interpret (Linh)
- **LLVM-based compilation**: High-performance native code generation (Tinh)
- **Fast interpretation**: Quick iteration and development (Linh)
- **Static typing with type inference**: Catch errors at compile time
- **Module system**: Organize code with imports and exports
- **Self-hosting compiler**: ✅ Achieved! Tinh can compile itself
- **Modern syntax**: Clean and intuitive language design
- **Variable declarations**: Supports `let`, `const`, and `var` keywords
- **Switch-case statements**: Pattern matching support

## Language Syntax (Lithium Specification)

```li
// Variables with static typing
let x: int32 = 42
let name: str = "Tinh Linh"

// Constants
const PI: float = 3.14159

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

// Switch-case
switch (x) {
    case 1: print("One")
    case 2: print("Two")
    default: print("Other")
}

// Modules
import "path/to/module.li"
export func public_function(): int32 {
    return 42
}
```

## Building

### Requirements

- CMake >= 3.15
- C++17 compiler (MSVC, GCC, Clang)
- LLVM 14+ (for Tinh compiler)
- Clang (for linking compiled programs)

### Build Instructions

```sh
mkdir build
cd build
cmake ..
cmake --build . --config Release
```

The build will create:
- `MiniTinhCompiler.exe` - Bootstrap compiler (C++ implementation)
- Runtime library for linking
- (Linh interpreter - if enabled)

## Usage

### Using Tinh Compiler

Compile a Lithium (.li) source file to native code:

```sh
# Compile to LLVM IR
./build/Tinh/MiniTinhCompiler.exe your_program.li output.ll

# Compile LLVM IR to object file
llc -filetype=obj output.ll -o output.obj

# Link with runtime
clang -o your_program.exe output.obj runtime.obj

# Run
./your_program.exe
```

### Using Linh Interpreter

(Documentation coming soon)

## Status

### Tinh Compiler
✅ **Self-Hosting Achieved!** (March 11, 2026)

The Tinh compiler can now compile itself:
- Boolean type handling fixed
- LLVM IR generation validated
- All type checks passing
- Ready for production use

See [SELF_HOSTING_VERIFIED.md](SELF_HOSTING_VERIFIED.md) for details.

### Linh Interpreter
🚧 In Development

## License

Apache License 2.0 - See [LICENSE](LICENSE) file for details.

## Links

- **Repository**: [github.com/TinhLinhLang/TinhLinh](https://github.com/TinhLinhLang/TinhLinh)
- **Website**: [tinhlinhlang.github.io](https://tinhlinhlang.github.io) OR [linh.kesug.com](https://linh.kesug.com) / [tinh.kesug.com](https://tinh.kesug.com)
- **Email**: linhprogramminglanguage@gmail.com
- **Facebook**: [Tinh Linh Lang](https://www.facebook.com/people/Tinh-Linh-Lang/61576609030665/)

## Contributing

Contributions, bug reports, and ideas are welcome! Please open an issue or pull request on GitHub.

## Contact

For questions or support, please:
- Open an issue on GitHub
- Email us at linhprogramminglanguage@gmail.com
- Visit our website

---

© 2025-2026 Sao Tin Developer Team. All rights reserved.
