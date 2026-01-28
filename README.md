# Go Linter Written in Rust

## 1. About the Project

This project aims to design and implement a linter for the Go programming language. The linter is implemented in Rust and uses Tree-sitter as its parsing backend. Its primary purpose is to detect common programming mistakes and bad practices in Go source code, with a particular focus (in the thesis work) on rules derived from the book *100 Go Mistakes and How to Avoid Them* by Teiva Harsanyi.

Unlike compiler-based analyzers that rely on language-specific ASTs and type-checking infrastructure, this project performs analysis on a concrete syntax tree (CST). This design choice allows the linter to remain decoupled from the Go compiler toolchain while still providing precise diagnostics and source-level feedback.

The project is structured as a standalone command-line tool that processes one or more Go source files and reports detected issues in a human-readable format. The implementation emphasizes modular rule design, extensibility, and clarity of analysis.

---

## 2. How It Works

The linter follows a multi-stage static analysis pipeline:

### 2.1 Input Processing

The tool accepts one or more Go source files or directories as input via a command-line interface. Files are read as plain text and passed to the parsing stage.

### 2.2 Parsing

The Go source code is parsed using [Tree-sitter](https://github.com/tree-sitter/tree-sitter) through its Rust bindings. Tree-sitter produces a CST.

### 2.3 Syntax Tree Analysis

The generated CST serves as the primary data structure for analysis. The linter traverses the tree and applies a set of independent linting rules. Each rule inspects specific node patterns and, when necessary, derives limited contextual information such as variable scopes or control-flow hints.

### 2.4 Rule Execution

Linting rules are implemented as separate modules. Each rule operates on the CST and may:

- Match specific syntax patterns
- Track identifiers and scopes
- Analyze local control flow
- Emit diagnostics when a violation is detected

### 2.5 Diagnostics and Output

All diagnostics produced by the rules are collected and reported to the user. Each diagnostic includes:

- File name
- Source location (line and column)
- Rule identifier
- Descriptive message

### Pipeline Summary

```
Go source code → Tree-sitter parsing → Concrete syntax tree → Rule-based analysis → Diagnostics output
```

---

## 3. Linting Rules

The initial version of the linter focuses on a core set of widely accepted and commonly implemented linting rules.

### Planned Rules

`Unused Variables`  
Detects variables that are declared but never referenced within their scope.

`Ignored Error Values`
Reports function calls that return an error value which is neither assigned nor checked.

`Variable Shadowing`
Detects redeclarations of variables in inner scopes that shadow variables from outer scopes, with particular emphasis on error variables.

`Defer Statements Inside Loops`
Warns when `defer` statements are used inside loops, which can lead to delayed resource release and unintended behavior.

`Duplicate Imports/Unused Imports`
Detects and reports duplicate or unused imports.

`Unreachable Code`
Detects statements that appear after control-flow terminating statements such as `return` or `panic`.

`Empty Code Blocks`
Reports empty blocks in constructs such as `if`, `for`, or `switch` statements.

### Implementation Notes

Each rule is implemented independently and operates directly on the concrete syntax tree or on lightweight contextual structures derived from it.

---

## 4. CLI Tool

The linting will primarily be done via CLI.

**CLI syntax to be defined later.**

---

## 5. Expanding for Thesis Work

The thesis extension of this project will focus on rules and mistakes from the popular book *100 Go Mistakes and How to Avoid Them* by Teiva Harsanyi.
The following rules will be designed and implemented from the book:
`#1 Unintended variable shadowing` - Focuses on preventing variable shadowing, especially when redeclaring variable with the short assignment operator (:=). Which leads to compiled code, but variables are nil
`#8 any says nothing` - any similarly to TypeScript allows to skip static type checking, which is for a statically typed language really bad. Functions that accept/return any data should be refactored into concrete type implementation.
`#9 Being confused about when to use generics` - Implementation will focus on one use case, that is when calling a method of the type argument, meaning the linter will warn to swap out generic to a concrete type argument.
`#10 Not being aware of the possible problem with type embedding` - Will warn about specific use case - when a mutex is embedded - to avoid mutex accessibility from the client
`#13 Creating utility packages` - Warns about commonly used package names that does not give information about the code they contain - utils, shared, common, etc.
`#14 Ignoring package name collisions` - Prevent any type of package name collision with variable name (in any scope)
`#18 Neglecting integer overflows` - Tries to find and warn about potential integer overflows from source code
`#21 Inefficient slice initilization` - Enforces to provide length and capacity at slice initilization
`#22 Being confused about nil vs. empty slices` - Will recommend to change []string{} to []string initilization
`#23 Not properly chekcing if a slice is empty` - Instead of slice != nil it recommends to use len(slice) == 0
`#24 Not making slice copies correctly` - Aligns with rule 21, will check whether the destination slice has atleast the length of source slice during copying
`#25 Unexpected side effect using slice append` - Since, multiple variables can modify the same backed array in the slice's internal implementation, it will try to prevent this to avoid runtime errors. Will try to make it similarly to Rust's ownership implementation (multiple variables cant have ownership of the same slice)

NOTE: Explanation of rule selection/elimination is explained in the RULE-SELECTION-PROCESS.md
---

## References

- [Tree-sitter](https://github.com/tree-sitter/tree-sitter)
- Harsanyi, T. (2022). *100 Go Mistakes and How to Avoid Them*. Manning Publications.
