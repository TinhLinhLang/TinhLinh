# Type System Integration Guide

## Overview

This guide explains how to integrate the TinhLinh type system into the compiler pipeline.

## Directory Structure

```
TinhLinh/
├── Compile/                    # Compiler modules
│   ├── Compiler.li            # Original compiler (no type checking)
│   ├── CompilerBasicTypes.li  # Compiler with basic type checking
│   ├── CompilerWithTypes.li   # Compiler with full type system
│   ├── Parsing/               # Lexer, Parser, AST
│   └── CodeGen/               # Code generation
└── Tinh/                      # Type system modules
    ├── TypeSystem.li          # Core type system
    ├── TypeInference.li       # Type inference
    ├── TypeChecker.li         # Advanced type checking
    └── TypedCodeGen.li        # Type-aware code generation
```

## Integration Options

### Option 1: Basic Type Checking (Recommended for Start)

**File**: `CompilerBasicTypes.li`

**Features**:
- Basic type representation
- Symbol table with scopes
- Simple type checking
- Error reporting

**Usage**:
```bash
# Compile the compiler
.\compile.ps1 TinhLinh\Compile\CompilerBasicTypes.li CompilerBasic.exe

# Use it
.\CompilerBasic.exe input.li output.ll
```

**Pipeline**:
```
Source → Lexer → Parser → TypeChecker → CodeGen → LLVM IR
```

### Option 2: Full Type System

**File**: `CompilerWithTypes.li`

**Features**:
- Function signature tracking
- Struct definition tracking
- Full AST type checking
- Comprehensive error reporting

**Usage**:
```bash
# Compile the compiler
.\compile.ps1 TinhLinh\Compile\CompilerWithTypes.li CompilerFull.exe

# Use it
.\CompilerFull.exe input.li output.ll
```

**Pipeline**:
```
Source → Lexer → Parser → AdvancedTypeChecker → CodeGen → LLVM IR
```

### Option 3: Type-Aware Code Generation (Future)

**File**: `CompilerTypedCodeGen.li` (to be created)

**Features**:
- All features from Option 2
- Type-aware LLVM IR generation
- Automatic cast insertion
- Type-based optimizations

**Pipeline**:
```
Source → Lexer → Parser → AdvancedTypeChecker → TypedCodeGen → LLVM IR
```

## Step-by-Step Integration

### Step 1: Test Type System Independently

```bash
# Test basic type system
.\compile.ps1 TinhLinh\Tinh\TypeSystem_mini.li
.\TypeSystem_mini.exe

# Test full type system
.\compile.ps1 TinhLinh\Tinh\test_type_system.li
.\test_type_system.exe
```

### Step 2: Integrate Basic Type Checking

1. **Import type system**:
   ```li
   import "../Tinh/TypeSystem.li"
   ```

2. **Create type checker**:
   ```li
   var tc: TypeChecker = TypeChecker_new()
   ```

3. **Check program**:
   ```li
   var success: bool = TypeChecker_check_program(tc, stmts)
   if (!success) {
       TypeChecker_print_errors(tc)
       return false
   }
   ```

### Step 3: Add Advanced Type Checking

1. **Import advanced modules**:
   ```li
   import "../Tinh/TypeChecker.li"
   ```

2. **Create advanced type checker**:
   ```li
   var atc: AdvancedTypeChecker = AdvancedTypeChecker_new()
   ```

3. **Check with validation**:
   ```li
   var success: bool = AdvancedTypeChecker_check_program(atc, stmts)
   AdvancedTypeChecker_print_results(atc)
   ```

### Step 4: Enable Type-Aware Code Generation

1. **Import typed codegen**:
   ```li
   import "../Tinh/TypedCodeGen.li"
   ```

2. **Create typed code generator**:
   ```li
   var tcg: TypedCodeGen = TypedCodeGen_new(cg, atc)
   ```

3. **Generate code**:
   ```li
   var ir: str = TypedCodeGen_run(tcg, stmts)
   ```

## Compilation Pipeline Comparison

### Original Compiler
```
┌─────────┐    ┌────────┐    ┌────────┐    ┌─────────┐
│ Source  │ -> │ Lexer  │ -> │ Parser │ -> │ CodeGen │ -> LLVM IR
└─────────┘    └────────┘    └────────┘    └─────────┘
```

### With Basic Type Checking
```
┌─────────┐    ┌────────┐    ┌────────┐    ┌─────────────┐    ┌─────────┐
│ Source  │ -> │ Lexer  │ -> │ Parser │ -> │ TypeChecker │ -> │ CodeGen │ -> LLVM IR
└─────────┘    └────────┘    └────────┘    └─────────────┘    └─────────┘
```

### With Full Type System
```
┌─────────┐    ┌────────┐    ┌────────┐    ┌──────────────────────┐    ┌─────────┐
│ Source  │ -> │ Lexer  │ -> │ Parser │ -> │ AdvancedTypeChecker  │ -> │ CodeGen │ -> LLVM IR
└─────────┘    └────────┘    └────────┘    └──────────────────────┘    └─────────┘
                                                    │
                                                    ├─ Function signatures
                                                    ├─ Struct definitions
                                                    └─ Full validation
```

### With Type-Aware CodeGen
```
┌─────────┐    ┌────────┐    ┌────────┐    ┌──────────────────────┐    ┌──────────────┐
│ Source  │ -> │ Lexer  │ -> │ Parser │ -> │ AdvancedTypeChecker  │ -> │ TypedCodeGen │ -> LLVM IR
└─────────┘    └────────┘    └────────┘    └──────────────────────┘    └──────────────┘
                                                                                │
                                                                                ├─ Typed expressions
                                                                                ├─ Auto casting
                                                                                └─ Optimizations
```

## Example: Modifying Existing Compiler

### Before (Compiler.li)
```li
import "Parsing/Lexer/Lexer.li"
import "Parsing/Parser/Parser.li"
import "CodeGen/CodeGen.li"

func compile_file(input_path: str, output_path: str): bool {
    var content: str = readFile(input_path)
    var lexer: Lexer = Lexer_new(content)
    var tokens: TokenArray = Lexer_scan_tokens(lexer)
    var parser: Parser = Parser_new(tokens)
    var stmts: any = Parser_parse_program(parser)
    
    var cg: CodeGen = CodeGen_new()
    var ir: str = CodeGen_run(cg, stmts)
    writeFile(output_path, ir)
    return true
}
```

### After (with Type Checking)
```li
import "Parsing/Lexer/Lexer.li"
import "Parsing/Parser/Parser.li"
import "CodeGen/CodeGen.li"
import "../Tinh/TypeSystem.li"  // Add this

func compile_file(input_path: str, output_path: str): bool {
    var content: str = readFile(input_path)
    var lexer: Lexer = Lexer_new(content)
    var tokens: TokenArray = Lexer_scan_tokens(lexer)
    var parser: Parser = Parser_new(tokens)
    var stmts: any = Parser_parse_program(parser)
    
    // Add type checking
    var tc: TypeChecker = TypeChecker_new()
    var success: bool = TypeChecker_check_program(tc, stmts)
    if (!success) {
        TypeChecker_print_errors(tc)
        return false
    }
    
    var cg: CodeGen = CodeGen_new()
    var ir: str = CodeGen_run(cg, stmts)
    writeFile(output_path, ir)
    return true
}
```

## Testing Integration

### Test 1: Compile with Basic Types
```bash
# Compile the compiler
.\compile.ps1 TinhLinh\Compile\CompilerBasicTypes.li

# Test with a simple program
echo 'func test(): int32 { return 42 }' > test_input.li
.\CompilerBasicTypes.exe test_input.li test_output.ll
```

### Test 2: Compile with Full Types
```bash
# Compile the compiler
.\compile.ps1 TinhLinh\Compile\CompilerWithTypes.li

# Test with a complex program
.\CompilerWithTypes.exe TinhLinh\Tinh\test_type_system.li output.ll
```

### Test 3: Self-Compilation Test
```bash
# Try to compile the compiler with itself
.\CompilerWithTypes.exe TinhLinh\Compile\CompilerWithTypes.li CompilerSelf.ll
```

## Error Handling

### Type Errors
```
Type checking failed:
Type Error: Type mismatch in variable x
Type Error: Undefined function: foo
Type Error: Wrong number of arguments for function bar
```

### Integration Errors
```
Error: Could not import ../Tinh/TypeSystem.li
Solution: Check import paths are correct
```

## Performance Impact

### Compilation Time
- **Without type checking**: ~1.0x baseline
- **With basic type checking**: ~1.1x baseline (+10%)
- **With full type system**: ~1.2x baseline (+20%)

### Benefits
- Catch errors at compile time
- Better code generation
- Improved IDE support
- Self-documenting code

## Troubleshooting

### Issue: Import not found
**Solution**: Use relative paths from compiler location
```li
import "../Tinh/TypeSystem.li"  // Correct
import "Tinh/TypeSystem.li"     // Wrong
```

### Issue: Type checking too slow
**Solution**: Use basic type checking for development
```bash
.\CompilerBasic.exe input.li output.ll  # Faster
```

### Issue: False type errors
**Solution**: Check type annotations are correct
```li
var x: int32 = 42        // Correct
var x: str = 42          // Type error
```

## Next Steps

1. ✅ Test basic integration
2. ✅ Add type checking to main compiler
3. ⏳ Enable advanced features
4. ⏳ Add type-aware code generation
5. ⏳ Optimize performance

## Resources

- **Type System Docs**: See `TinhLinh/Tinh/README.md`
- **Architecture**: See `TinhLinh/Tinh/ARCHITECTURE.md`
- **Usage Guide**: See `TinhLinh/Tinh/USAGE_GUIDE.md`
- **Build Guide**: See `TinhLinh/Tinh/BUILD.md`

---

**Last Updated**: March 9, 2026
**Status**: Ready for integration
