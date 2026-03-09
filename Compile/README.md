# TinhLinh Programming Language

A flexible programming language with an LLVM backend.

TinhLinh supports both dynamic and static variables:

- `var` for dynamic variables (similar to Python, JavaScript, Ruby)
- `let` for static variables (similar to C/C++, Java, Go, Zig)
- `const` for values (and function references) that never change

## Features

- Static typing with type inference
- Dynamic variables when you want rapid iteration
- Module system
- LLVM-based code generation
- Self-hosting compiler (in progress)

## Language Syntax

```li
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
- Website: tinhlinhlang.github.io OR linh.kesug.com

