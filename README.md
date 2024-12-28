# Zig Style Guide

This document provides a style guide for Zig language used by [@smallkirby](https://github.com/smallkirby).

## Preface

- These terms are defined in addition to the ones described in [the official Zig documentation](https://ziglang.org/documentation/master/#Style-Guide).
- These terms are not mandatory. My code may intentionally violate these terms in some cases.

## Import

`@import` should be gouped and sorted by the following order:

1. Standard library
2. Non-standard modules
3. File imports

Within each group, imports should be sorted alphabetically.

```zig
// Standard library
const std = @import("std");
const log = std.log;

// Non-standard modules
const mymod = @import("mymod");
const mymod_some = mymod.some;

// File imports from the same directory
const a = @import("a.zig");
const b = @import("b.zig");
const c = @import("c.zig");
```

## Visibility

Global mutable variable (`var`) must not be public:

```zig
// Bad
pub var foo: u64 = 0;

// Good
var foo: u64 = 0;
pub fn getFoo() u64 {
    return foo;
}
```

Functions in the struct's namespace must be private if possible:

```zig
const S = struct {
  // Bad
  pub fn foo() void {}
  // Good
  fn foo() void {}
};
```

## Naming

Users of a struct must not directly access its field whose name starts with `_`:

```zig
const S = struct {
  // Direct access like `s.foo = 3;` is allowed.
  foo: u64,
  // Direct access like `s._bar = 3;` is not allowed.
  _bar: u64,
};
```

The type of the struct field whose name starts with `__` (double underscore) must be `void`.
This field must not be accessed at runtime.

```zig
const S = packed struct {
  a: u64,
  b: u32,
  __marker: void,
  c: u32,

  const first_block_size = @offsetOf(@This(), "__marker");
};
```

## Literals

[Enum literals](https://ziglang.org/documentation/master/#Enum-Literals) should be used unless it significantly reduces readability.

```zig
const E = enum { a, b, c };

switch (e) {
  // Bad
  E.a: => {},
  E.b: => {},
  E.c: >= {},

  // Good
  .a: => {},
  .b: => {},
  .c: >= {},
}

// Bad
func(E.a);
// Good
func(.a);
```

## Braces

Even when a block body has only one statement, you should wrap it with braces except for `break`, `continue`, or `return`.

```zig
// Bad
if (cond) a += 1;
// Good
if (cond) {
    a += 1;
}
if (cond) break;
if (cond) continue;
if (cond) return;
```

## Doc Comments

All public functions, variables, and constants must have a [doc comment](https://ziglang.org/documentation/master/#Doc-Comments).
Tope-level doc comments are not mandatory:

```zig
// Bad
pub const S = struct {
  pub const c = 3;
  foo: u64,

  pub fn bar() void {}
  fn baz() void {}
}

// Good
/// Comment is mandatory.
pub const S = struct {
  /// Comment is mandatory.
  pub const c = 3;
  foo: u64; // Comment is not mandatory.

  /// Comment is mandatory.
  pub fn bar() void {}
  fn baz() void {} // Comment is not mandatory.
}
```

## Testing

Tests should be placed at the end of the file.

```zig
// Bad
const testing = std.testing;
pub fn foo() void {}
test "foo" {}
pub fn bar() void {}

// Good
pub fn foo() void {}
pub fn bar() void {}

// ====================================================

const testing = std.testing;
test "foo" {}
```
