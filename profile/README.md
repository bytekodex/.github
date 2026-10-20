# Bytekodex

We turn source code into pictures of bytecode.

Send a Java, Kotlin or Groovy snippet to the bot, and it compiles the code and replies with a syntax
highlighted image of the resulting bytecode — methods, constant pool, locals, and how many opcodes it all
took.

## Projects

| Project | What it is |
| --- | --- |
| [bytekodex-platform](https://github.com/bytekodex/bytekodex-platform) | The engine, in Rust. Reads JVM class files (and later .NET IL) directly, turns them into a platform-neutral token stream, and renders that stream to an image. Exposes a C ABI. |
| [bytekodex-telegram](https://github.com/bytekodex/bytekodex-telegram) | The bot, in Go. Detects the language, drives compilation across language and runtime versions, and talks to the platform over its C ABI. |

Two repositories are archived and kept only as references for the rewrite:
[bytekodex-painter](https://github.com/bytekodex/bytekodex-painter) (the original ANTLR and Skia based
painter) and [bytekodex-antlr](https://github.com/bytekodex/bytekodex-antlr) (its grammars).

## Design notes

The bytecode reader does not shell out to `javap`. It parses the class file itself, which is roughly three
orders of magnitude faster, removes the runtime dependency on a JDK, and gives access to everything `javap`
only prints on request — the constant pool, local variable tables, stack map frames.

Everything platform-specific lives in a frontend that emits a shared token IR; the renderer knows about
colors and glyphs, never about opcodes. That is what lets a second bytecode format be added without touching
the drawing code.
