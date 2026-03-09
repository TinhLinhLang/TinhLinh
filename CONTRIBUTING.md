# Contributing to TinhLinh Compiler

Thank you for your interest in contributing to TinhLinh!

## 🚀 Getting Started

### Prerequisites
- Git
- LLVM (for code generation)
- Clang (for linking)
- Basic understanding of compilers

### Setup Development Environment

1. Clone the repository:
```bash
git clone https://github.com/TinhLinhLang/TinhLinh.git
cd TinhLinh
```

2. Explore the codebase:
```
Compile/
├── Parsing/      # Lexer, Parser, AST
├── CodeGen/      # LLVM IR generation
├── Bytecode/     # Bytecode generation
└── Module/       # Module system
```

## 📝 Code Style

### Naming Conventions
- **Functions**: `snake_case`
  ```tinh
  func parse_expression() { }
  ```

- **Types**: `PascalCase`
  ```tinh
  struct ASTNode { }
  ```

- **Variables**: `snake_case`
  ```tinh
  let token_count: int32 = 0
  ```

- **Constants**: `UPPER_CASE` (as functions)
  ```tinh
  func EOF(): int32 { return 0 }
  ```

### Syntax Guidelines
- Use `let` for static typing: `let x: int32 = 5`
- Use `var` for dynamic typing: `var x = 5`
- Use `const` for constants: `const MAX: int32 = 100`
- Always include type annotations for function parameters and return types

### Comments
```tinh
// Single line comment for brief explanations

/* Multi-line comment
   for longer descriptions
   or documentation */
```

## 🐛 Reporting Issues

### Bug Reports
Include:
- TinhLinh version
- Operating system
- Steps to reproduce
- Expected vs actual behavior
- Code sample (if applicable)

### Feature Requests
Include:
- Use case description
- Proposed syntax (if applicable)
- Why this feature is needed
- Potential implementation approach

## 🔧 Development Workflow

### 1. Fork & Branch
```bash
git checkout -b feature/my-new-feature
# or
git checkout -b fix/issue-123
```

### 2. Make Changes
- Follow code style guidelines
- Add comments for complex logic
- Keep commits focused and atomic

### 3. Test Your Changes
- Test with existing test cases
- Add new tests if needed
- Ensure no regressions

### 4. Commit
```bash
git commit -m "feat: add support for generic types

- Implement generic type parsing
- Add type parameter resolution
- Update code generation for generics"
```

### Commit Message Format
```
<type>: <subject>

<body>

<footer>
```

Types:
- `feat`: New feature
- `fix`: Bug fix
- `docs`: Documentation changes
- `style`: Code style changes (formatting)
- `refactor`: Code refactoring
- `test`: Adding tests
- `chore`: Maintenance tasks

### 5. Push & Pull Request
```bash
git push origin feature/my-new-feature
```

Then create a Pull Request on GitHub with:
- Clear description of changes
- Link to related issues
- Screenshots (if UI changes)

## 🎯 Priority Areas

### High Priority
1. **Type System Implementation**
   - Full semantic analysis
   - Type inference
   - Generic types

2. **Self-Hosting**
   - Fix struct initialization
   - Enable compiler to compile itself
   - Verify iterative compilation

3. **Error Messages**
   - Better error reporting
   - Source location tracking
   - Helpful suggestions

### Medium Priority
1. **Standard Library**
   - Collections (Array, Map, Set)
   - String utilities
   - File I/O
   - Math functions

2. **Optimization**
   - Dead code elimination
   - Constant folding
   - Inline expansion

3. **Documentation**
   - Language specification
   - API documentation
   - Tutorials and examples

### Low Priority
1. **Tooling**
   - Language Server Protocol (LSP)
   - Syntax highlighting
   - Debugger integration

2. **Package Manager**
   - Dependency resolution
   - Package registry
   - Version management

## 📚 Resources

### Learning Materials
- [LLVM Tutorial](https://llvm.org/docs/tutorial/)
- [Crafting Interpreters](https://craftinginterpreters.com/)
- [Dragon Book](https://en.wikipedia.org/wiki/Compilers:_Principles,_Techniques,_and_Tools)

### Project Documentation
- `ARCHITECTURE.md` - Compiler architecture
- `README.md` - Project overview
- `Compile/` - Source code with inline comments

## 💬 Communication

- **Issues**: For bugs and feature requests
- **Discussions**: For questions and ideas
- **Pull Requests**: For code contributions

## 📜 License

By contributing, you agree that your contributions will be licensed under the MIT License.

## 🙏 Thank You!

Every contribution, no matter how small, helps make TinhLinh better!
