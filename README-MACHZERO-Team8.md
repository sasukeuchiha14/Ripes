# Mach Zero Hackathon: Ripes

- **Upstream repo:** https://github.com/mortbopet/Ripes
- **Our fork:** https://github.com/sasukeuchiha14/Ripes

Ripes is a visual RISC-V processor simulator and assembly editor, built with C++ and Qt 6.

---

## Round 1: Picking a repo and an issue ✅

- We looked at several repos and picked **Ripes**: https://github.com/mortbopet/Ripes
- We went through the open issues and found none worth solving. Most were small UI fixes, for example:
  - dark mode not switching on under Linux
  - text drawn twice when you click on it in dark mode
- So we hunted for our own bug and found one: **the assembler evaluates expressions right-to-left instead of using operator precedence (BODMAS).**
  - Example: `10 - 2 - 3` is evaluated as `10 - (2 - 3) = 11` instead of `5`, and `2 * 3 + 4` as `2 * (3 + 4) = 14` instead of `10`.

## Round 2: Validating the issue ✅ / Working on the fix 🚧

- **We confirmed the bug** in the current source.
- **It was intentional, but only for the assembler.** The author's note in [`src/assembler/expreval.h`](src/assembler/expreval.h) says the parser is *"right-associative … no operator precedence is implemented"* and is *"mainly intended to be used by the assembler … and as such not by the user"*.
- **Users still hit it.** The evaluator ships in a release, and any expression a user writes in assembly (for example in `.equ` or in immediates) goes through it. Neither the code nor the user documentation has been updated to say so.
- **It's still relevant.** There are two possible fixes:
  1. **Fix the evaluator:** add proper operator precedence and left-to-right evaluation (BODMAS) *(our chosen direction)*, or
  2. **Update the documentation** so users know expressions are right-associative and need explicit parentheses.
- **Done so far:**
  - Set up the toolchain and compiled Ripes from source on Windows (MSVC + Qt 6.8.3).
  - Ran the app and traced the evaluation path through the source (`src/assembler/expreval.cpp`, `parseRight` / `parseLeft`).
- **In progress:** changing `expreval.cpp` to respect operator precedence and associativity.

## Round 3: TBD

---

## Building and running (Windows)

You need the following installed: Visual Studio Build Tools (C++ workload), Qt 6.8.3 `msvc2022_64` with Qt Charts in `C:\Qt\6.8.3\msvc2022_64`, and CMake and Ninja (`pip install cmake ninja`). Also fetch the submodules with `git submodule update --init --recursive`.

To build and run, double-click or run:

```bat
build_and_run.bat
```

The script sets up MSVC, configures `build-msvc/` the first time, builds only what changed, copies the Qt DLLs, and launches `Ripes.exe`.
