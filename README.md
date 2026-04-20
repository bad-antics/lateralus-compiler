# Lateralus Compiler

A self-hosting compiler for the Lateralus programming language, written entirely in Lateralus. Covers every stage from lexing through code generation and linking.

## Architecture

The compiler is organized into distinct phases, each in its own module.

**Lexer** — tokenization with support for string interpolation, number literals, comments, and whitespace handling. Includes keyword tables and specialized lexers for strings and numbers.

**Parser** — multiple parsing strategies: recursive descent, Pratt (precedence climbing), LL(1), LR, Earley, and parser combinators. Grammar rules define the full Lateralus syntax.

**AST** — typed AST nodes with source spans, visitor pattern, builder, printer, and tree transformations.

**Analysis** — semantic passes including type checking, type inference (Hindley-Milner with unification), control flow analysis, data flow analysis, liveness analysis, reaching definitions, scope resolution, and symbol table construction.

**IR** — SSA-form intermediate representation with basic blocks, phi nodes, IR builder, and pretty printer.

**Optimization** — constant folding, dead code elimination, inlining, loop optimization (invariant code motion, unrolling), common subexpression elimination, strength reduction, alias analysis, peephole optimization, escape analysis, devirtualization, vectorization, mem2reg, and a configurable optimizer pipeline.

**Compiler Passes** — borrow checker, lifetime checker, trait resolution, monomorphization, closure conversion, macro expansion, pattern match compilation, tail call optimization, CFG construction and simplification, SSA construction, phi elimination, instruction scheduling, register allocation (linear scan with spilling), ABI lowering, code generation, linking, IR printing, and diagnostics.

**Code Generation** — instruction selection, register allocation, calling conventions, stack frame layout, ELF writer.

**Runtime** — bytecode VM, stack machine, garbage collector, module loader, REPL, error recovery.

**WASM** — WebAssembly code generation with type mapping and module builder.

**Linker** — dead code elimination, section layout, symbol resolution.

**Debug Info** — DWARF writer and source maps.

**Diagnostics** — error reporting with suggestions, lint passes.

## Building

```
ltl build src/passes/parser.ltl
ltl test
```

## Module Map

```
lexer/          Tokenization and lexical analysis
parser/         Parsing strategies and grammar
ast/            Abstract syntax tree
analysis/       Semantic analysis passes
ir/             SSA intermediate representation
optimization/   Optimization passes
src/passes/     Compiler pipeline passes
codegen/        Native code generation
runtime/        VM and runtime support
wasm/           WebAssembly backend
linker/         Linking and dead code elimination
debuginfo/      Debug information
diagnostics/    Error reporting and lints
```

## License

MIT