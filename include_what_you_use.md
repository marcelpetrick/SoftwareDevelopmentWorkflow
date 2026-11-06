# Include What You Use (IWYU) in C++ and Qt projects

**In short:** every source file should include the headers whose declarations it uses, and nothing else. Tools can
find unused and missing `#include`s, but in a real cross-platform C++/Qt project their answers are suggestions, not
facts: only a build of every configuration on every platform decides. Do it for clean, robust code, not for build
speed.

## The rule

- **Include what you use:** if `foo.cpp` uses `std::vector`, it includes `<vector>`, even if another header already
  brings it in.
- **Don't include what you don't use:** an `#include` with no used declaration is removed.
- **Headers stay lean:** a header includes only what its own declarations need; everything else goes into the `.cpp`.
  Prefer a forward declaration (`class Foo;`) where a pointer or reference is enough.

## Why it is worth doing (the chances)

| Benefit | What it means in practice |
|---|---|
| Robustness | A file does not break when some other header stops including something by accident ("works because `a.h` happens to pull in `<map>`"). |
| Less coupling | Changing a header recompiles fewer files; dependencies between modules become visible. |
| Readability | The include list tells you what a file really depends on. |
| Easier refactoring | Moving code, splitting modules or switching libraries (e.g. Qt 5 → Qt 6) hurts less when every file states its own needs. |
| Some build time | Fewer and smaller headers per translation unit. Usually small, see below. |

**Build speed is rarely the reason.** Measured in a real Qt Widgets project (about 2,100 includes in 440 files): 7 %
of the includes were really unused, removing the safe ones cut the preprocessed code by 0.5 %, removing all of them
would have cut 3 %. Almost all preprocessed lines come from Qt and the standard library (a single Qt Widgets file is
130,000-150,000 lines after preprocessing), not from your own headers. Speed comes from other measures:
precompiled headers, ccache/sccache, unity builds, fewer heavy umbrella includes (`<QtWidgets>`), and splitting large
headers.

## The tools

| Tool | What it is | Strengths | Weaknesses |
|---|---|---|---|
| **include-what-you-use** (`iwyu`, `iwyu_tool.py`, `fix_includes.py`) | Clang-based analyser from the IWYU project | analyses a header together with its `.cpp`; says what to add, what to remove and what *replaces* a removed include; can apply a reviewed subset automatically | must match your Clang version (often built from source); needs mapping files for Qt and other libraries, and the shipped Qt mappings are old; noisy on Qt macros |
| **clang-tidy `misc-include-cleaner`** | LLVM's built-in include checker | already installed with clang-tidy; no mapping files; honours IWYU pragmas | looks at headers on their own, which causes false "unused" hits; very noisy "missing" reports for Qt (`Q_OBJECT`, `slots`) |
| **Compiler options** (`-H`, `/showIncludes`, `-M`) | show the include tree | exact, cheap | no judgement, only data |

Practical recommendation: use IWYU as the main tool and `misc-include-cleaner` as a second opinion. Only act on what
**both** report, and only after the builds agree.

Typical IWYU run with a compile database:

```bash
cmake -S . -B build -DCMAKE_EXPORT_COMPILE_COMMANDS=ON
iwyu_tool.py -p build -j8 -- -Xiwyu --mapping_file=qt5_11.imp -Xiwyu --cxx17ns > iwyu.out
# review iwyu.out, then apply only the reviewed lines, e.g. with fix_includes.py --nosafe_headers
```

## Why it is so hard to check

1. **The tools only see one configuration.** They analyse the code the preprocessor keeps for *one* set of
   defines on *one* platform. Everything under `#ifdef _WIN32`, `#ifdef Q_OS_WIN`, a feature flag or a
   product-specific define is invisible. An include used only there looks "unused".
2. **Transitive includes hide the truth.** A file can compile today only because another header includes what it
   needs. Removing an "unused" include from a header can break files that never mention it.
3. **Facade and umbrella headers.** Projects often have headers that only forward others (`config.h` →
   `platform_config.h`, `<QtWidgets>` → everything). The tool wants the real provider; removing the facade without
   adding the provider breaks the build.
4. **Macros and code generators.** Qt's `moc`, `uic` and `rcc` generate code that uses headers your source never
   mentions; macros like `Q_OBJECT` or `Q_DECLARE_METATYPE` hide uses. Tools report them wrongly in both directions.
5. **Templates and inline code.** Uses inside templates are only visible when instantiated; a header may need an
   include for a template that is only instantiated in a `.cpp`.
6. **Other consumers.** If other projects compile your headers or sources (a submodule, a library, a plugin), they
   may rely on your transitive includes, with their own defines.
7. **Compilers differ.** Standard library headers include each other differently in libstdc++, libc++ and MSVC's
   STL, and even between versions. `<string>` may bring `<cstdint>` on one compiler and not on another.

## Windows vs. Linux: what differs

| Topic | Linux (GCC/Clang) | Windows (MinGW/MSVC) |
|---|---|---|
| Code paths | `#ifdef __linux__`, POSIX headers (`<unistd.h>`) | `#ifdef _WIN32`, `<windows.h>`, SetupAPI, HID, vendor SDKs |
| File names | case-sensitive: `#include "Windows.h"` fails if the file is `windows.h` | case-insensitive: wrong case compiles |
| Standard library | libstdc++ / libc++ transitive includes | MSVC STL or MinGW's libstdc++: other transitive includes |
| Implicit types | `<cstdint>` often already pulled in | `int32_t` may be missing unless `<cstdint>` is included explicitly |
| `<windows.h>` | – | defines macros (`min`, `max`, `ERROR`, `DELETE`) that clash with your names; include order matters |
| Tool support | IWYU and clang-tidy run natively | analysers usually run on Linux only; Windows code needs a Windows (or cross) build to check |

Consequence: an include cleanup that builds on Linux is not proven on Windows. Without a Windows machine, a
**cross syntax check** helps: compile every file with the MinGW cross compiler (`x86_64-w64-mingw32-g++` or
`i686-w64-mingw32-g++`) and `-fsyntax-only`, with the Windows defines and include paths. It catches missing
declarations, but not link errors or MSVC-specific behaviour.

## The dangers

- **Broken builds in configurations nobody tried:** another platform, another preset, a product define, a debug or
  sanitizer build, a consumer project.
- **Silent behaviour changes (rare but real):** a removed header can change which overload or template
  specialisation is visible, or which macro value applies. The code still compiles but does something else.
- **Churn without value:** thousands of "add this include" changes create huge diffs and merge conflicts in every
  open branch for little gain.
- **Tool-driven code:** blindly applying `fix_includes.py` produces include lists that follow the tool's mapping
  mistakes (e.g. private Qt headers, wrong providers).

## How to do it safely (checklist)

1. **List every configuration** that must keep building: platforms, compilers, build types, presets, feature and
   product defines, sanitizers, consumer projects.
2. **Run both tools** on a compile database and keep only the intersection of their "unused" lists.
3. **Sort by risk:** an include in a `.cpp` with no platform or product `#if` is low risk; an include in a header, or
   in a file with `#ifdef` code the analyser did not see, needs a human to read the hidden code.
4. **Remove only, in small batches** (one module per commit). Do not add hundreds of "missing" includes in the
   same change; add one only where a removal needs it.
5. **Build every configuration after each batch:** all presets, the cross syntax check for the other platform, the
   consumer projects, the unit tests. Restore any removal that breaks something instead of patching around it.
6. **Mark the intentional cases** with pragmas so the tools stop reporting them: `// IWYU pragma: keep` (needed
   although it looks unused) and `// IWYU pragma: export` (facade headers).
7. **Measure if you claim speed:** use a load-independent number (preprocessed lines or CPU time) and several
   interleaved runs; wall-clock time on a shared machine varies by 20 % or more.
8. **Keep it going:** new code should follow the rule from the start; a CI gate on IWYU is usually too noisy, but a
   review checklist item ("includes what it uses, no unused includes") works.

## Rules of thumb for new code

- Include the header that declares what you use, not one that happens to bring it in.
- Prefer `<QString>`, `<QWidget>` over the umbrella headers `<QtCore>`, `<QtWidgets>`.
- In headers: forward-declare classes used only as pointers or references; include in the `.cpp`.
- Use the C++ headers (`<cstdint>`, `<cstring>`), not the C ones (`<stdint.h>`, `<string.h>`).
- Put platform-specific includes inside the same `#ifdef` as the code that needs them.
- Keep the include order consistent (own header first, then project, then third-party, then standard) so missing
  includes in your own header show up immediately.
