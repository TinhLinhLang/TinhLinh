# Building Type System

## Overview

The TinhLinh type system can be compiled using either:
1. **MiniTinhCompiler** (C++ bootstrap compiler)
2. **TinhCompiler_let.exe** (Self-hosting compiler)

## Prerequisites

### Required Tools
- LLVM toolchain (llc, clang)
- CMake (for building MiniTinhCompiler)
- PowerShell (for build scripts)

### Compilers

#### MiniTinhCompiler (C++ Bootstrap)
Location: `d:\TinhLinh\build\Tinh\MiniTinhCompiler.exe`

Build with CMake:
```bash
cd d:\TinhLinh
mkdir build
cd build
cmake ..
cmake --build . --config Release
```

#### TinhCompiler_let (Self-hosting)
Location: `d:\TinhLinh\TinhCompiler_let.exe`

This is the self-hosting compiler that can compile most Tinh programs.

## Build Scripts

### 1. compile_with_mini.ps1
Compile Tinh files using MiniTinhCompiler:
```powershell
.\compile_with_mini.ps1 <input.li> [output.exe]
```

Example:
```powershell
.\compile_with_mini.ps1 TinhLinh\Tinh\test_simple.li
```

### 2. compile.ps1
Compile Tinh files using self-hosting compiler:
```powershell
.\compile.ps1 <input.li> [output.exe]
```

Example:
```powershell
.\compile.ps1 TinhLinh\Tinh\test_type_system.li
```

### 3. build_type_system.ps1
Automated build and test script:
```powershell
.\build_type_system.ps1
```

This script:
- Checks for available compilers
- Runs simple functionality tests
- Attempts to compile type system modules
- Runs comprehensive tests if successful

## Type System Modules

### Core Modules

1. **TypeSystem.li** (Foundation)
   - Type representation
   - Symbol table
   - Basic type checker
   - ~300 lines

2. **TypeInference.li** (Inference)
   - Literal type inference
   - Binary/unary operation types
   - Type compatibility
   - LLVM type mapping
   - ~200 lines

3. **TypeChecker.li** (Advanced)
   - Function signature tracking
   - Struct definition tracking
   - Full AST type checking
   - Error reporting
   - ~400 lines

4. **TypedCodeGen.li** (Code Generation)
   - Type-aware expression generation
   - Automatic cast insertion
   - Type-based optimizations
   - ~300 lines

5. **test_type_system.li** (Tests)
   - Comprehensive test suite
   - ~400 lines

## Building Individual Modules

### Test Simple Functionality
```powershell
.\compile_with_mini.ps1 TinhLinh\Tinh\test_simple.li
.\test_simple.exe
```

### Build TypeSystem.li
```powershell
# Try with MiniCompiler
.\compile_with_mini.ps1 TinhLinh\Tinh\TypeSystem.li

# Or with self-host compiler
.\compile.ps1 TinhLinh\Tinh\TypeSystem.li
```

### Build and Run Tests
```powershell
.\compile.ps1 TinhLinh\Tinh\test_type_system.li
.\test_type_system.exe
```

## Known Limitations

### MiniTinhCompiler Limitations
The C++ bootstrap compiler (MiniTinhCompiler) has limited support for:
- Complex struct initialization
- Advanced type inference
- Some language features used in type system

**Workaround**: Use the self-hosting compiler (TinhCompiler_let.exe) for full type system compilation.

### Self-hosting Compiler Limitations
The self-hosting compiler cannot yet compile itself due to:
- Complex struct initialization in compiler source
- Some advanced language features

**Status**: ~95% self-hosting achieved with type system

## Compilation Pipeline

### Standard Pipeline
```
Source (.li)
    ↓
Compiler (MiniTinhCompiler or TinhCompiler_let)
    ↓
LLVM IR (.ll)
    ↓
llc (LLVM compiler)
    ↓
Object file (.obj)
    ↓
clang (linker)
    ↓
Executable (.exe)
```

### Type System Integration
```
Source (.li)
    ↓
Lexer → Tokens
    ↓
Parser → AST
    ↓
TypeChecker → Typed AST + Errors
    ↓
TypedCodeGen → LLVM IR
    ↓
llc → Object file
    ↓
clang → Executable
```

## Testing

### Quick Test
```powershell
# Test basic functionality
.\compile_with_mini.ps1 TinhLinh\Tinh\test_simple.li
.\test_simple.exe
```

### Full Test Suite
```powershell
# Compile and run all tests
.\compile.ps1 TinhLinh\Tinh\test_type_system.li
.\test_type_system.exe
```

Expected output:
```
========================================
   TinhLinh Type System Test Suite
========================================

=== Testing Basic Types ===
int32: int32
str: str
bool: bool
...

========================================
   All Tests Completed!
========================================
```

## Troubleshooting

### Error: MiniTinhCompiler not found
**Solution**: Build MiniTinhCompiler with CMake:
```bash
cd d:\TinhLinh\build
cmake --build . --config Release
```

### Error: LLVM tools not found
**Solution**: Ensure LLVM is in PATH:
```powershell
$env:PATH += ";C:\Program Files\LLVM\bin"
```

### Error: Runtime object not found
**Solution**: Build runtime with CMake:
```bash
cd d:\TinhLinh\build
cmake --build . --target TinhRuntimeObj --config Release
```

### Compilation fails with "Unknown feature"
**Solution**: Use self-hosting compiler instead:
```powershell
.\compile.ps1 <input.li>
```

## Performance

### Compilation Times (Approximate)
- test_simple.li: ~1 second
- TypeSystem.li: ~2-3 seconds
- test_type_system.li: ~3-4 seconds
- Full type system: ~10-15 seconds

### Runtime Performance
- Type checking: O(n) where n = AST nodes
- Type inference: O(1) for most operations
- Overhead: ~5-10% increase in compile time

## Integration Example

### Using Type System in Compiler

```tinh
import "TinhLinh/Tinh/TypeChecker.li"

func compile_with_types(source: str): str {
    // Parse
    let lexer: Lexer = Lexer_new(source)
    let tokens: TokenArray = Lexer_scan_tokens(lexer)
    let parser: Parser = Parser_new(tokens)
    let ast: any = Parser_parse_program(parser)
    
    // Type check
    let atc: AdvancedTypeChecker = AdvancedTypeChecker_new()
    let success: bool = AdvancedTypeChecker_check_program(atc, ast)
    
    if (!success) {
        AdvancedTypeChecker_print_results(atc)
        return ""
    }
    
    // Generate code
    let cg: CodeGen = CodeGen_new()
    return CodeGen_run(cg, ast)
}
```

## Next Steps

1. **Test with MiniCompiler**: Verify basic functionality
2. **Build with Self-host**: Compile full type system
3. **Run Tests**: Execute comprehensive test suite
4. **Integrate**: Add type checking to compiler pipeline
5. **Optimize**: Profile and optimize performance

## Resources

- **Documentation**: See README.md, ARCHITECTURE.md, USAGE_GUIDE.md
- **Examples**: See test_type_system.li for usage examples
- **Source**: All modules in TinhLinh/Tinh/

---

**Last Updated**: March 9, 2026
**Status**: Ready for testing and integration
