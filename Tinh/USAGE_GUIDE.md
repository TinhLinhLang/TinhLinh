# Type System Usage Guide

## Quick Start

### Basic Type Checking

```tinh
import "Tinh/TypeSystem.li"

// Create type checker
let tc: TypeChecker = TypeChecker_new()

// Define variable types
let int_type: Type = Type_new(TYPE_INT32(), "int32")
SymbolTable_define(tc.current_scope, "x", int_type)

// Lookup variable
let sym: Symbol = SymbolTable_lookup(tc.current_scope, "x")
if (sym.type.kind != TYPE_UNKNOWN()) {
    print("Variable x has type: " + Type_to_string(sym.type))
}
```

### Type Inference

```tinh
import "Tinh/TypeInference.li"

// Infer literal types
let t1: Type = infer_literal_type("42")        // int32
let t2: Type = infer_literal_type("true")      // bool
let t3: Type = infer_literal_type("hello")     // str

// Infer operation result types
let int_type: Type = Type_new(TYPE_INT32(), "int32")
let result: Type = infer_binary_type(int_type, int_type, "+")
print("int32 + int32 = " + Type_to_string(result))
```

### Advanced Type Checking

```tinh
import "Tinh/TypeChecker.li"

// Create advanced type checker
let atc: AdvancedTypeChecker = AdvancedTypeChecker_new()

// Register function
let ret_type: Type = Type_new(TYPE_INT32(), "int32")
let sig: FunctionSignature = FunctionSignature_new("add", ret_type)
FunctionSignature_add_param(sig, "a", Type_new(TYPE_INT32(), "int32"))
FunctionSignature_add_param(sig, "b", Type_new(TYPE_INT32(), "int32"))
AdvancedTypeChecker_register_function(atc, sig)

// Register struct
let def: StructDefinition = StructDefinition_new("Point")
StructDefinition_add_field(def, "x", Type_new(TYPE_INT32(), "int32"))
StructDefinition_add_field(def, "y", Type_new(TYPE_INT32(), "int32"))
AdvancedTypeChecker_register_struct(atc, def)

// Check program
let success: bool = AdvancedTypeChecker_check_program(atc, ast)
AdvancedTypeChecker_print_results(atc)
```

## Common Patterns

### Pattern 1: Variable Type Checking

```tinh
func check_variable_declaration(tc: TypeChecker, node: any) {
    // Get declared type
    let var_type: Type = Type_new(TYPE_INT32(), "int32")
    if (!str_eq(node.ty_name, "")) {
        if (str_eq(node.ty_name, "str")) {
            var_type = Type_new(TYPE_STR(), "str")
        }
        // ... other types
    }
    
    // Check initializer
    if (node.initializer != 0) {
        let init_type: Type = TypeChecker_infer_expr(tc, node.initializer)
        if (!types_compatible(var_type, init_type)) {
            TypeChecker_error(tc, "Type mismatch in variable " + node.name)
        }
    }
    
    // Define in symbol table
    SymbolTable_define(tc.current_scope, node.name, var_type)
}
```

### Pattern 2: Function Call Validation

```tinh
func validate_function_call(atc: AdvancedTypeChecker, node: any): Type {
    // Lookup function signature
    let sig: FunctionSignature = AdvancedTypeChecker_lookup_function(atc, node.name)
    if (sig == 0) {
        TypeChecker_error(atc.type_checker, "Undefined function: " + node.name)
        return Type{TYPE_UNKNOWN(), "", false, 0, 0, 0}
    }
    
    // Check argument count
    let arg_count: int32 = ptr_array_len(node.args)
    let param_count: int32 = FunctionSignature_param_count(sig)
    if (arg_count != param_count) {
        TypeChecker_error(atc.type_checker, "Wrong number of arguments")
        return sig.return_type
    }
    
    // Check argument types
    let i: int32 = 0
    while (i < arg_count) {
        let arg: any = ptr_array_get(node.args, i)
        let arg_type: Type = TypeChecker_infer_expr(atc.type_checker, arg)
        let param_type: Type = FunctionSignature_get_param_type(sig, i)
        
        if (!types_compatible(param_type, arg_type)) {
            TypeChecker_error(atc.type_checker, "Type mismatch in argument " + i32_to_str(i))
        }
        i = i + 1
    }
    
    return sig.return_type
}
```

### Pattern 3: Struct Field Access

```tinh
func check_field_access(atc: AdvancedTypeChecker, node: any): Type {
    // Get base type
    let base_type: Type = TypeChecker_infer_expr(atc.type_checker, node.left)
    
    // Must be struct
    if (base_type.kind != TYPE_STRUCT()) {
        TypeChecker_error(atc.type_checker, "Field access on non-struct")
        return Type{TYPE_UNKNOWN(), "", false, 0, 0, 0}
    }
    
    // Lookup struct definition
    let struct_def: StructDefinition = AdvancedTypeChecker_lookup_struct(atc, base_type.name)
    if (struct_def == 0) {
        TypeChecker_error(atc.type_checker, "Unknown struct: " + base_type.name)
        return Type{TYPE_UNKNOWN(), "", false, 0, 0, 0}
    }
    
    // Get field type
    let field_type: Type = StructDefinition_get_field_type(struct_def, node.name)
    if (field_type.kind == TYPE_UNKNOWN()) {
        TypeChecker_error(atc.type_checker, "No such field: " + node.name)
    }
    
    return field_type
}
```

### Pattern 4: Type-Aware Code Generation

```tinh
func generate_typed_binary_op(tcg: TypedCodeGen, node: any): str {
    // Infer operand types
    let left_type: Type = TypedCodeGen_get_expr_type(tcg, node.left)
    let right_type: Type = TypedCodeGen_get_expr_type(tcg, node.right)
    
    // Generate operands
    let left_val: str = TypedCodeGen_generate_typed_expr(tcg, node.left)
    let right_val: str = TypedCodeGen_generate_typed_expr(tcg, node.right)
    
    // Infer result type
    let result_type: Type = infer_binary_type(left_type, right_type, node.value)
    let llvm_type: str = Type_to_llvm(result_type)
    
    // Generate instruction
    let res: str = CodeGen_next_temp(tcg.codegen)
    if (str_eq(node.value, "+")) {
        CodeGen_emit(tcg.codegen, res + " = add " + llvm_type + " " + left_val + ", " + right_val)
    }
    // ... other operators
    
    return res
}
```

## Integration with Compiler

### Step 1: Add Type Checking to Compiler

```tinh
import "Compile/Compiler.li"
import "Tinh/TypeChecker.li"

func compile_file_with_types(input_path: str, output_path: str): bool {
    // Lex and parse
    var content: str = readFile(input_path)
    var lexer: Lexer = Lexer_new(content)
    var tokens: TokenArray = Lexer_scan_tokens(lexer)
    var parser: Parser = Parser_new(tokens)
    var stmts: any = Parser_parse_program(parser)
    
    // Type check
    var atc: AdvancedTypeChecker = AdvancedTypeChecker_new()
    var success: bool = AdvancedTypeChecker_check_program(atc, stmts)
    
    if (!success) {
        print("Type checking failed:")
        AdvancedTypeChecker_print_results(atc)
        return false
    }
    
    print("Type checking passed")
    
    // Generate code
    var cg: CodeGen = CodeGen_new()
    var ir: str = CodeGen_run(cg, stmts)
    writeFile(output_path, ir)
    
    return true
}
```

### Step 2: Use TypedCodeGen

```tinh
import "Tinh/TypedCodeGen.li"

func compile_with_typed_codegen(input_path: str, output_path: str): bool {
    // Lex and parse
    var content: str = readFile(input_path)
    var lexer: Lexer = Lexer_new(content)
    var tokens: TokenArray = Lexer_scan_tokens(lexer)
    var parser: Parser = Parser_new(tokens)
    var stmts: any = Parser_parse_program(parser)
    
    // Create typed code generator
    var cg: CodeGen = CodeGen_new()
    var atc: AdvancedTypeChecker = AdvancedTypeChecker_new()
    var tcg: TypedCodeGen = TypedCodeGen_new(cg, atc)
    
    // Generate code (includes type checking)
    var ir: str = TypedCodeGen_run(tcg, stmts)
    
    if (str_eq(ir, "")) {
        return false
    }
    
    writeFile(output_path, ir)
    return true
}
```

## Best Practices

### 1. Always Check Return Values

```tinh
let sym: Symbol = SymbolTable_lookup(table, "x")
if (sym.type.kind == TYPE_UNKNOWN()) {
    // Handle undefined variable
    print("Error: Variable not found")
    return
}
// Use sym safely
```

### 2. Use Two-Pass Checking for Forward References

```tinh
// Pass 1: Collect all definitions
let i: int32 = 0
while (i < n) {
    let node: any = ptr_array_get(ast, i)
    if (node.n_kind == STMT_FUNC()) {
        AdvancedTypeChecker_process_function(atc, node)
    }
    i = i + 1
}

// Pass 2: Type check bodies
i = 0
while (i < n) {
    let node: any = ptr_array_get(ast, i)
    TypeChecker_check_stmt(tc, node)
    i = i + 1
}
```

### 3. Collect All Errors Before Reporting

```tinh
// Don't stop at first error
if (!types_compatible(expected, actual)) {
    TypeChecker_error(tc, "Type mismatch")
    // Continue checking
}

// Report all errors at end
if (TypeChecker_has_errors(tc)) {
    TypeChecker_print_errors(tc)
    return false
}
```

### 4. Use Scopes Properly

```tinh
// Enter scope for block
TypeChecker_enter_scope(tc)

// Check statements in block
let i: int32 = 0
while (i < n) {
    TypeChecker_check_stmt(tc, ptr_array_get(block.body, i))
    i = i + 1
}

// Exit scope
TypeChecker_exit_scope(tc)
```

### 5. Cache Type Information

```tinh
// Store inferred types to avoid recomputation
TypedCodeGen_set_expr_type(tcg, expr, inferred_type)

// Retrieve cached type
let cached: Type = TypedCodeGen_get_expr_type(tcg, expr)
```

## Debugging Tips

### 1. Print Type Information

```tinh
func debug_print_type(t: Type) {
    print("Type: " + Type_to_string(t))
    print("  Kind: " + i32_to_str(t.kind))
    print("  Name: " + t.name)
    print("  Is pointer: " + i32_to_str(t.is_pointer))
}
```

### 2. Trace Symbol Lookup

```tinh
func debug_lookup(table: SymbolTable, name: str): Symbol {
    print("Looking up: " + name)
    let sym: Symbol = SymbolTable_lookup(table, name)
    if (sym.type.kind == TYPE_UNKNOWN()) {
        print("  Not found")
    } else {
        print("  Found: " + Type_to_string(sym.type))
    }
    return sym
}
```

### 3. Validate AST Structure

```tinh
func validate_ast_node(node: any) {
    if (node == 0) {
        print("ERROR: Null AST node")
        return
    }
    print("Node kind: " + i32_to_str(node.n_kind))
    print("Node line: " + i32_to_str(node.n_line))
}
```

## Common Errors and Solutions

### Error: "Undefined variable"

**Cause**: Variable not in symbol table

**Solution**: Ensure variable is defined before use
```tinh
SymbolTable_define(tc.current_scope, var_name, var_type)
```

### Error: "Type mismatch"

**Cause**: Incompatible types in operation

**Solution**: Check type compatibility or add explicit cast
```tinh
if (!types_compatible(target, source)) {
    if (needs_cast(source, target)) {
        value = generate_cast(source, target, value)
    }
}
```

### Error: "Wrong number of arguments"

**Cause**: Function call with incorrect arg count

**Solution**: Ensure function signature matches call
```tinh
let param_count: int32 = FunctionSignature_param_count(sig)
let arg_count: int32 = ptr_array_len(node.args)
if (arg_count != param_count) {
    // Error
}
```

### Error: "Unknown struct type"

**Cause**: Struct not registered

**Solution**: Register struct before use
```tinh
let def: StructDefinition = StructDefinition_new("MyStruct")
// Add fields...
AdvancedTypeChecker_register_struct(atc, def)
```

## Performance Tips

### 1. Minimize Symbol Lookups

```tinh
// Bad: Multiple lookups
let t1: Type = SymbolTable_lookup(table, "x").type
let t2: Type = SymbolTable_lookup(table, "x").type

// Good: Single lookup
let sym: Symbol = SymbolTable_lookup(table, "x")
let t1: Type = sym.type
let t2: Type = sym.type
```

### 2. Use Flat Scopes When Possible

```tinh
// Avoid deep nesting
// Prefer flat function-level scopes
```

### 3. Cache Frequently Used Types

```tinh
// Create once, reuse
let int_type: Type = Type_new(TYPE_INT32(), "int32")
let str_type: Type = Type_new(TYPE_STR(), "str")
```

## Testing Your Type System Integration

```tinh
// Test basic types
func test_basic() {
    let tc: TypeChecker = TypeChecker_new()
    let int_type: Type = Type_new(TYPE_INT32(), "int32")
    SymbolTable_define(tc.current_scope, "x", int_type)
    let sym: Symbol = SymbolTable_lookup(tc.current_scope, "x")
    print("Test passed: " + i32_to_str(sym.type.kind == TYPE_INT32()))
}

// Test type inference
func test_inference() {
    let t: Type = infer_literal_type("42")
    print("Test passed: " + i32_to_str(t.kind == TYPE_INT32()))
}

// Test function signatures
func test_functions() {
    let atc: AdvancedTypeChecker = AdvancedTypeChecker_new()
    let sig: FunctionSignature = FunctionSignature_new("test", Type_new(TYPE_INT32(), "int32"))
    AdvancedTypeChecker_register_function(atc, sig)
    let found: FunctionSignature = AdvancedTypeChecker_lookup_function(atc, "test")
    print("Test passed: " + i32_to_str(found != 0))
}
```

## Next Steps

1. Read `ARCHITECTURE.md` for detailed design
2. Study `test_type_system.li` for examples
3. Integrate into your compiler pipeline
4. Add custom types for your language features
5. Extend with advanced features (generics, inference)

---

**Guide Version**: 1.0
**Last Updated**: March 9, 2026
**For Questions**: See README.md and ARCHITECTURE.md
