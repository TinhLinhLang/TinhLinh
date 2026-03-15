# Features To Implement in Self-Hosted Compiler

## 1. Type Casting (DONE in Bootstrap, TODO in Self-Hosted)

### Current Status
- ✅ Implemented in `Tinh/MiniTinhCompiler.cpp` (bootstrap compiler)
- ❌ Not yet in `TinhLinh/Compile/CodeGen/CodeGen.li` (self-hosted)

### Implementation in Self-Hosted Compiler

**File**: `TinhLinh/Compile/CodeGen/CodeGen.li`

**Location**: In `CodeGen_generate_expr()` function, inside `EXPR_CALL()` handling (around line 432)

**Code to Add** (before the `get_function_signature` call):

```tinh
// Type casting functions: int32(), bool(), ptr()
if (str_eq(node.name, "int32")) {
    let n: int32 = ptr_array_len(node.args)
    if (n != 1) { return "0" }
    let arg_val: str = CodeGen_generate_expr(cg, ptr_array_get(node.args, 0))
    if (cg.last_temp_is_ptr == 1) {
        let res: str = CodeGen_next_temp(cg)
        CodeGen_emit(cg, res + " = ptrtoint ptr " + arg_val + " to i32")
        CodeGen_set_last_type_i32(cg)
        return res
    }
    CodeGen_set_last_type_i32(cg)
    return arg_val
}

if (str_eq(node.name, "bool")) {
    let n: int32 = ptr_array_len(node.args)
    if (n != 1) { return "0" }
    let arg_val: str = CodeGen_generate_expr(cg, ptr_array_get(node.args, 0))
    let res: str = CodeGen_next_temp(cg)
    if (cg.last_temp_is_ptr == 1) {
        let cmp_temp: str = CodeGen_next_temp(cg)
        CodeGen_emit(cg, cmp_temp + " = icmp ne ptr " + arg_val + ", null")
        CodeGen_emit(cg, res + " = zext i1 " + cmp_temp + " to i32")
    } else {
        let cmp_temp: str = CodeGen_next_temp(cg)
        CodeGen_emit(cg, cmp_temp + " = icmp ne i32 " + arg_val + ", 0")
        CodeGen_emit(cg, res + " = zext i1 " + cmp_temp + " to i32")
    }
    CodeGen_set_last_type_i32(cg)
    return res
}

if (str_eq(node.name, "ptr")) {
    let n: int32 = ptr_array_len(node.args)
    if (n != 1) { return "0" }
    let arg_val: str = CodeGen_generate_expr(cg, ptr_array_get(node.args, 0))
    if (cg.last_temp_is_ptr == 1) { return arg_val }
    let res: str = CodeGen_next_temp(cg)
    CodeGen_emit(cg, res + " = inttoptr i32 " + arg_val + " to ptr")
    CodeGen_set_last_type_ptr(cg)
    return res
}
```

**Status**: ✅ ADDED to CodeGen.li

## 2. Enum Dot Notation (DONE in Bootstrap, TODO in Self-Hosted)

### Current Status
- ✅ Implemented in `Tinh/MiniTinhCompiler.cpp` (bootstrap compiler)
- ❌ Not yet in self-hosted compiler

### Implementation Strategy

This is more complex and requires changes in multiple files:

#### Step 1: Track Enum Definitions
**File**: `TinhLinh/Compile/Semantic/SemanticChecker.li` or create new `EnumRegistry.li`

Need to store:
- Enum name → List of members
- Member name → Value (int32)

#### Step 2: Modify Parser
**File**: `TinhLinh/Compile/Parsing/Parser/Parser.li`

In `Parser_parse_postfix()`, when encountering DOT after IDENTIFIER:
- Check if base identifier is an enum name (starts with uppercase)
- If yes, create special node with qualified name: `EnumName.Member`

#### Step 3: Modify CodeGen
**File**: `TinhLinh/Compile/CodeGen/CodeGen.li`

In `EXPR_VARIABLE()` handling:
- Check if variable name contains '.'
- If yes, split into enum name and member name
- Look up value in enum registry
- Return constant value

### Alternative: Simple Implementation

For now, use the bootstrap compiler (`Tinh/MiniTinhCompiler.cpp`) which already has full support for:
- Type casting: `int32()`, `bool()`, `ptr()`
- Enum dot notation: `EnumName.Member`

The self-hosted compiler can be updated later when it's more stable.

## 3. Testing

### Type Casting Tests
```tinh
func main() {
    let x: int32 = 42
    let y: int32 = int32(x)  // Should work
    
    let p: any = ptr(100)
    let back: int32 = int32(p)  // Should work
    
    let b: int32 = bool(1)  // Should work
}
```

### Enum Tests
```tinh
enum Color {
    Red,
    Green,
    Blue
}

func main() {
    let c: int32 = Color.Red  // Should be 0
    print(c)
}
```

## Priority

1. **HIGH**: Type casting in self-hosted compiler (partially done)
2. **MEDIUM**: Enum dot notation in self-hosted compiler
3. **LOW**: Advanced type casting (int8, checked casts)

## Notes

- Bootstrap compiler (`Tinh/MiniTinhCompiler.cpp`) is the reference implementation
- Self-hosted compiler is still under development
- For production use, rely on bootstrap compiler for now
