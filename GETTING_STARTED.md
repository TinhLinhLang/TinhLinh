# Getting Started with TinhLinh

## 🚀 Quick Start Guide

Welcome to **TinhLinh** - a modern, self-hosting programming language! This guide will help you get up and running in minutes.

## 📋 Prerequisites

### Required Tools
- **LLVM 14+** - For compilation backend
- **Clang** - For linking compiled programs  
- **Git** - For cloning the repository

### Platform Support
- ✅ **Windows** (Primary support)
- 🚧 **Linux** (In development)
- 🚧 **macOS** (In development)

## 📦 Installation

### Option 1: Download Release (Recommended)
1. Go to [GitHub Releases](https://github.com/TinhLinhLang/TinhLinh/releases)
2. Download the latest `v0.0.1-bootstrap` release
3. Extract to your preferred directory

### Option 2: Clone from Source
```bash
git clone https://github.com/TinhLinhLang/TinhLinh.git
cd TinhLinh
```

## 🎯 Your First TinhLinh Program

### 1. Create a Hello World Program
Create a file called `hello.li`:

```li
func main(): int32 {
    print("Hello, TinhLinh!")
    print("Welcome to the future of programming!")
    return 0
}
```

### 2. Compile Your Program
```bash
# Step 1: Compile to LLVM IR
./Compiler.exe hello.li hello.ll

# Step 2: Compile LLVM IR to object file
llc -filetype=obj hello.ll -o hello.obj

# Step 3: Link to create executable
clang -o hello.exe hello.obj

# Step 4: Run your program
./hello.exe
```

### 3. Expected Output
```
Hello, TinhLinh!
Welcome to the future of programming!
```

## 📚 Language Basics

### Variables and Types
```li
// Basic types
let age: int32 = 25
let name: str = "Alice"
let pi: float32 = 3.14159
let isActive: bool = true

// Type inference (optional type annotations)
let count = 42        // Inferred as int32
let message = "Hi!"   // Inferred as str
```

### Functions
```li
// Simple function
func greet(name: str): str {
    return "Hello, " + name + "!"
}

// Function with multiple parameters
func add(a: int32, b: int32): int32 {
    return a + b
}

// Using functions
func main(): int32 {
    let result = add(5, 3)
    let greeting = greet("World")
    print(greeting)
    print("5 + 3 = ", i32_to_str(result))
    return 0
}
```

### Control Flow
```li
func demo_control_flow(): void {
    let x = 10
    
    // If-else
    if (x > 5) {
        print("x is greater than 5")
    } else {
        print("x is 5 or less")
    }
    
    // While loop
    while (x > 0) {
        print("Countdown: ", i32_to_str(x))
        x = x - 1
    }
    
    // Switch statement
    switch (x) {
        case 0: print("Zero!")
        case 1: print("One!")
        default: print("Something else")
    }
}
```

### Structs and Data Types
```li
// Define a struct
struct Person {
    name: str
    age: int32
    email: str
}

// Use the struct
func create_person(): Person {
    let person = Person{
        "Alice Johnson",
        30,
        "alice@example.com"
    }
    return person
}

func main(): int32 {
    let p = create_person()
    print("Name: ", p.name)
    print("Age: ", i32_to_str(p.age))
    return 0
}
```

## 🔧 Advanced Features

### Modules and Imports
Create `math_utils.li`:
```li
export func square(x: int32): int32 {
    return x * x
}

export func cube(x: int32): int32 {
    return x * x * x
}
```

Use in `main.li`:
```li
import "math_utils.li"

func main(): int32 {
    let num = 5
    print("5 squared = ", i32_to_str(square(num)))
    print("5 cubed = ", i32_to_str(cube(num)))
    return 0
}
```

### Arrays and Collections
```li
func array_demo(): void {
    // Create an array
    let numbers = [1, 2, 3, 4, 5]
    
    // Access elements
    let first = numbers[0]
    print("First element: ", i32_to_str(first))
    
    // Array length
    let length = len(numbers)
    print("Array length: ", i32_to_str(length))
}
```

## 🛠️ Development Workflow

### Recommended Project Structure
```
my_project/
├── src/
│   ├── main.li          # Entry point
│   ├── utils/
│   │   └── helpers.li   # Utility functions
│   └── models/
│       └── data.li      # Data structures
├── build/               # Compiled outputs
└── README.md
```

### Build Script Example
Create `build.ps1` (Windows) or `build.sh` (Linux/macOS):

```powershell
# Windows PowerShell
Write-Host "Building TinhLinh project..." -ForegroundColor Green

# Compile main program
./Compiler.exe src/main.li build/main.ll
llc -filetype=obj build/main.ll -o build/main.obj
clang -o build/my_program.exe build/main.obj

Write-Host "Build complete! Run: ./build/my_program.exe" -ForegroundColor Green
```

## 🐛 Troubleshooting

### Common Issues

**Issue**: `Compiler.exe not found`
- **Solution**: Ensure you have the bootstrap compiler in your PATH or current directory

**Issue**: `llc: command not found`
- **Solution**: Install LLVM and ensure it's in your PATH

**Issue**: `clang: command not found`  
- **Solution**: Install Clang compiler

**Issue**: Compilation errors
- **Solution**: Check syntax carefully - TinhLinh is statically typed
- Ensure all variables are declared with proper types
- Check that all functions have return statements

### Getting Help
- 📖 **Documentation**: Check the `/Docs` folder
- 🐛 **Bug Reports**: Open an issue on GitHub
- 💬 **Community**: Join our discussions on GitHub
- 📧 **Email**: linhprogramminglanguage@gmail.com

## 🎯 Next Steps

1. **Explore Examples**: Check the `/examples` directory (coming soon)
2. **Read the Language Guide**: See `LANGUAGE_REFERENCE.md`
3. **Try Advanced Features**: Experiment with structs, modules, and arrays
4. **Join the Community**: Contribute to the project on GitHub
5. **Build Something Cool**: Create your first TinhLinh application!

## 🚀 Ready to Code!

You're now ready to start programming in TinhLinh! The language is designed to be familiar to developers coming from C-family languages while providing modern features and safety.

**Happy coding!** 🎉

---

*For more advanced topics, see our full documentation at [github.com/TinhLinhLang/TinhLinh](https://github.com/TinhLinhLang/TinhLinh)*