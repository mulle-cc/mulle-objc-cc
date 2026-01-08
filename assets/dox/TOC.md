# mulle-objc-cc Library Documentation for AI

## 1. Introduction & Purpose

**mulle-objc-cc** is a build system configuration project that establishes mulle-clang as the default Objective-C compiler for all dependencies in a mulle-sde project. It is NOT a library with runtime components, but rather a build-time configuration that propagates compiler settings across the dependency chain.

**Key Features:**
- Sets `COBJC` (C Objective-C) compiler variable to mulle-clang
- Configures compiler definitions for multiple platforms (Unix, Windows, MinGW)
- Supports musl and cosmopolitan libc variants
- Transparently enables mulle-objc runtime compilation for all dependencies
- No runtime library code; zero dependencies

## 2. Key Concepts & Design Philosophy

- **Build Configuration Only**: Not executed at runtime; purely affects build process
- **Transparent Integration**: Once added to project, all dependencies automatically use mulle-clang
- **Platform Abstraction**: Provides definitions for Unix, Windows (MSVC), and MinGW platforms
- **Compiler Variants**: Supports standard mulle-clang and musl/cosmopolitan variants
- **Minimal Overhead**: Simple CMake definitions; installs to `share/mulle-craft/definition/`

## 3. Core API & Data Structures

### Build System Integration

**Purpose**: This project provides no runtime API. Instead, it installs build definitions for mulle-craft.

**Installed Components:**

#### `definition/` (Linux/Unix)
- CMake configuration files
- Compiler setting: `COBJC=mulle-clang`
- Used by subsequent dependencies to compile with mulle-clang

#### `definition.mingw/` (MinGW - Windows cross-compile)
- MinGW-specific compiler settings
- Static linking configuration
- Musl libc variant support

#### `definition.windows/` (Native Windows - MSVC)
- Windows-specific compiler settings
- MSVC integration path
- Platform-specific flags

### Build System Variables Set

**COBJC Variable**: 
- Default: `mulle-clang` (C compiler targeting Objective-C)
- Used by downstream mulle-craft builds
- Affects all projects added after mulle-objc-cc

**Effect on Dependency Chain:**
1. Project adds mulle-objc-cc to .mulle/etc/sourcetree/config
2. Build system installs mulle-objc-cc definitions
3. All subsequent projects see `COBJC=mulle-clang`
4. All dependencies compile with mulle-clang (not gcc/clang)
5. All generated code targets mulle-objc runtime

### Installation Paths

```
share/mulle-craft/definition/
├── definition          # Primary Unix definition
├── definition.mingw    # MinGW cross-compile
└── definition.windows  # Windows MSVC
```

## 4. Performance Characteristics

**Not Applicable** - Build configuration only, no runtime performance impact.

**Build-Time Impact:**
- Installation: O(1) - simple file copy
- Configuration Load: O(1) - CMake variable set
- Compiler Selection: O(1) - environment variable lookup

## 5. AI Usage Recommendations & Patterns

### Best Practices

- **Add Early**: Add mulle-objc-cc to project dependencies BEFORE other Objective-C projects
- **Mark Correctly**: Use `--marks no-header,no-link` when adding (it's config, not a library)
- **Single Placement**: Only add once per project; adding multiple times has no extra effect
- **Dependency Position**: Place in sourcetree before projects that need mulle-clang

### Common Pitfalls

- **Late Addition**: Adding mulle-objc-cc after other ObjC projects may not affect them (depends on rebuild)
- **Missing Marks**: Adding without `--marks no-header,no-link` causes build errors (config is not a library)
- **Platform Assumptions**: Verify correct definition is used for your platform (Unix vs Windows vs MinGW)
- **Compiler Installed**: mulle-objc-cc does NOT install mulle-clang; compiler must be installed separately

### Idiomatic Usage

**Pattern 1: Adding to Project**
```bash
# Correct way: add with appropriate marks
mulle-sde add --marks no-header,no-link --github mulle-cc mulle-objc-cc
```

**Pattern 2: Verifying Configuration**
```bash
# After adding, rebuild and check compiler used
mulle-sde craft
# Verify in build output: compiler should be mulle-clang
```

**Pattern 3: Using with Musl**
```bash
# No special action needed; mulle-objc-cc provides both standard and musl variants
# Build system automatically selects correct definition
mulle-sde craft
```

## 6. Integration Examples

### Example 1: Basic Project Setup with mulle-objc-cc

```bash
# Create new mulle project
mulle-sde init -d MulleObjC myproject
cd myproject

# Add mulle-objc-cc first (before other ObjC projects)
mulle-sde add --marks no-header,no-link --github mulle-cc mulle-objc-cc

# Add Foundation and other ObjC libraries
mulle-sde add --github mulle-objc MulleObjC
mulle-sde add --github mulle-objc MulleFoundation

# Build - all projects now use mulle-clang automatically
mulle-sde craft
```

### Example 2: Multi-Platform Build Configuration

```bash
# mulle-objc-cc provides definitions for:

# Linux/Unix: uses definition/
# - COBJC=mulle-clang
# - Standard compilation

# Windows with MinGW: uses definition.mingw/
# - COBJC=mulle-clang
# - Static linking forced
# - Musl variant available

# Windows with MSVC: uses definition.windows/
# - COBJC=mulle-clang
# - MSVC-compatible settings

# Same .mulle/etc/sourcetree/config works for all platforms
# mulle-craft automatically selects platform-specific definition
mulle-sde craft --platform mingw  # Uses definition.mingw/
mulle-sde craft                   # Uses platform-default (usually definition/)
```

### Example 3: Verifying Compiler Configuration

```bash
# After adding mulle-objc-cc, check build log:
mulle-sde clean build
cd build
cmake --build . 2>&1 | grep -i "mulle-clang"

# Should show mulle-clang being invoked, not gcc or standard clang
# Output example:
# /path/to/mulle-clang -c -o obj/file.o src/file.m
```

### Example 4: Custom Build Definition Extension (Advanced)

```bash
# Custom projects can extend mulle-objc-cc definitions
# Place in: .mulle/share/mulle-craft/definition/

# Example: .mulle/share/mulle-craft/definition/custom
# Sets additional flags on top of mulle-objc-cc:
# COBJC_FLAGS=-fstrict-aliasing -fno-exceptions

# mulle-craft combines definitions:
# 1. Built-in platform definition
# 2. mulle-objc-cc definition (sets COBJC=mulle-clang)
# 3. Custom definition (adds flags)
```

## 7. Dependencies

- **None** - This is a build configuration project
- Assumes mulle-clang compiler is installed separately
- Works with mulle-craft build system

**Runtime Dependencies of Projects Using mulle-objc-cc:**
- mulle-objc-runtime (at runtime, for compiled projects)
- mulle-c (for generated code)

**Note**: mulle-objc-cc itself has NO code dependencies; it only provides build configuration files.
