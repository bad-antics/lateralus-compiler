# Lateralus Compiler

> Compiler construction toolkit written entirely in Lateralus (`.ltl`) — lexers, parsers, AST, IR, optimizations, and code generation.

[![Lateralus](https://img.shields.io/badge/Written_in-Lateralus-7c5cfc?style=flat-square)](https://github.com/bad-antics/lateralus-lang)
[![License](https://img.shields.io/badge/License-MIT-34d399?style=flat-square)](LICENSE)

## Architecture

```
Source Code (.ltl)
        |
        v
  [ Lexer ] --------> [ Parser ] --------> [ AST ]
    tokenizer            recursive            typed nodes
    keywords             pratt                expressions
    strings              earley               statements
    numbers              lr/ll(1)
                                                |
                                                v
                   [ Optimizer ] <-------- [ IR Builder ]
                     const fold              SSA form
                     dead code               basic blocks
                     inlining                phi nodes
                     peephole
                         |
                         v
                    [ Codegen ] --------> [ Runtime ]
                      reg alloc            bytecode VM
                      instr sel            stack machine
                      ELF writer           GC
                      stack frame          REPL
```

## Module Map

### Lexer — `lexer/`

| File | Description |
|:-----|:------------|
| `tokenizer.ltl` | Main tokenization loop — converts source to token stream |
| `token_types.ltl` | Token enum: keywords, operators, literals, delimiters |
| `keyword_table.ltl` | Reserved word lookup table with perfect hashing |
| `string_lexer.ltl` | String literal lexer with interpolation and escape sequences |
| `number_lexer.ltl` | Integer, float, hex, binary, and octal literal parsing |
| `comment_stripper.ltl` | Single-line (`//`) and block (`/* */`) comment removal |
| `whitespace.ltl` | Significant whitespace handling and indentation tracking |
| `lexer_rules.ltl` | Configurable lexer rule definitions |

### Parser — `parser/`

| File | Description |
|:-----|:------------|
| `recursive_descent.ltl` | Hand-written recursive descent parser (primary) |
| `pratt_parser.ltl` | Pratt parser for operator precedence expressions |
| `precedence_climbing.ltl` | Alternative precedence climbing algorithm |
| `parser_combinators.ltl` | Combinator library: `seq`, `alt`, `many`, `optional` |
| `earley_parser.ltl` | Earley parser for ambiguous grammar analysis |
| `lr_parser.ltl` | LR(1) parser with table-driven automaton |
| `ll1_parser.ltl` | LL(1) predictive parser with FIRST/FOLLOW sets |
| `grammar_rules.ltl` | Lateralus grammar definition in BNF-like DSL |

### AST — `ast/`

| File | Description |
|:-----|:------------|
| `nodes.ltl` | AST node types: expressions, statements, declarations |
| `visitor.ltl` | Visitor pattern for tree traversal and transformation |
| `printer.ltl` | Pretty-printer for AST debugging output |
| `types.ltl` | Type system nodes: generics, traits, bounds |

### IR — `ir/`

| File | Description |
|:-----|:------------|
| `ir_builder.ltl` | Lowers AST to intermediate representation |
| `ir_nodes.ltl` | IR instruction set: load, store, branch, call, phi |
| `ssa_form.ltl` | SSA construction with dominance frontiers |
| `phi_nodes.ltl` | Phi node insertion and variable renaming |
| `basic_blocks.ltl` | Control flow graph with basic block splitting |
| `ir_printer.ltl` | Human-readable IR output for debugging |

### Analysis — `analysis/`

| File | Description |
|:-----|:------------|
| `type_checker.ltl` | Hindley-Milner type inference with unification |
| `borrow_checker.ltl` | Ownership and borrowing verification |
| `lifetime_analysis.ltl` | Lifetime inference and validation |
| `control_flow.ltl` | CFG construction and reachability analysis |
| `data_flow.ltl` | Reaching definitions, live variables, available expressions |

### Optimization — `optimization/`

| File | Description |
|:-----|:------------|
| `const_folding.ltl` | Compile-time constant expression evaluation |
| `dead_code.ltl` | Unreachable code elimination via CFG analysis |
| `inlining.ltl` | Function inlining with cost heuristics |
| `common_subexpr.ltl` | CSE via value numbering |
| `loop_opt.ltl` | LICM, loop unrolling, strength reduction |
| `peephole.ltl` | Pattern-based local instruction simplification |
| `strength_reduce.ltl` | Replace expensive ops with cheaper equivalents |
| `alias_analysis.ltl` | Points-to analysis for memory disambiguation |

### Code Generation — `codegen/`

| File | Description |
|:-----|:------------|
| `register_alloc.ltl` | Graph-coloring register allocator |
| `instruction_sel.ltl` | Tree-pattern matching instruction selection |
| `stack_frame.ltl` | Stack frame layout and calling convention |
| `elf_writer.ltl` | ELF binary output for x86_64 targets |

### Runtime — `runtime/`

| File | Description |
|:-----|:------------|
| `bytecode_vm.ltl` | Stack-based bytecode virtual machine |
| `stack_machine.ltl` | Low-level stack machine interpreter |
| `gc_simple.ltl` | Mark-and-sweep garbage collector |
| `module_loader.ltl` | Module resolution and dynamic loading |
| `error_recovery.ltl` | Runtime error handling and panic recovery |
| `repl.ltl` | Interactive Read-Eval-Print Loop |

## Example: Pipeline Through the Compiler

```lateralus
// This source code:
let result = [1, 2, 3]
    |> map(|x| x * 2)
    |> filter(|x| x > 3)
    |> sum

// Lexer produces tokens:
// LET, IDENT("result"), EQ, LBRACKET, INT(1), COMMA, ...
// PIPELINE, IDENT("map"), LPAREN, PIPE, IDENT("x"), PIPE, ...

// Parser builds AST:
// LetDecl { name: "result", value: PipelineExpr { ... } }

// IR lowers to SSA:
// %1 = alloc_array [i64; 3]
// %2 = call @map(%1, closure_0)
// %3 = call @filter(%2, closure_1)
// %4 = call @sum(%3)

// Optimizer folds constants:
// %1 = const_array [2, 4, 6]
// %2 = call @filter(%1, closure_1)
// %3 = call @sum(%2)

// Codegen emits x86_64 or bytecode
```

## Building

```bash
# Install Lateralus
pip install lateralus-lang

# Compile a module
lateralus compile lexer/tokenizer.ltl

# Run the REPL
lateralus run runtime/repl.ltl

# Run all modules through the type checker
lateralus check **/*.ltl
```

## Part of the Lateralus Ecosystem

- **[lateralus-lang](https://github.com/bad-antics/lateralus-lang)** — Compiler, VM, stdlib, REPL
- **[Playground](https://bad-antics.github.io/lateralus/playground/)** — Try in browser
- **[Tutorials](https://github.com/bad-antics/lateralus-tutorials)** — 25 chapters
- **[Rosetta Code](https://github.com/bad-antics/lateralus-rosetta)** — 220 solutions
- **[VS Code Extension](https://marketplace.visualstudio.com/items?itemName=lateralus.lateralus-lang)** — Syntax highlighting

## License

MIT
