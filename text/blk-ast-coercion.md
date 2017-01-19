% AST Coercion

The Rust parser is not very robust in the face of `tt` substitutions.  Problems can arise when the parser is expecting a particular grammar construct and *instead* finds a lump of substituted `tt` tokens.  Rather than attempt to parse them, it will often just *give up*.  In these cases, it is necessary to employ an AST coercion.

```rust
# #![allow(dead_code)]
# 
macro_rules! as_expr { ($e:expr) => {$e} }
macro_rules! as_item { ($i:item) => {$i} }
macro_rules! as_pat  { ($p:pat) =>  {$p} }
macro_rules! as_stmt { ($s:stmt) => {$s} }
# 
# as_item!{struct Dummy;}
# 
# fn main() {
#     as_stmt!(let as_pat!(_) = as_expr!(42));
# }
```

These coercions are often used with [push-down accumulation] macros in order to get the parser to treat the final `tt` sequence as a particular kind of grammar construct.

Note that this specific set of macros is determined by what macros are allowed to expand to, *not* what they are able to capture.  That is, because macros cannot appear in type position[^issue-27245], you cannot have an `as_ty!` macro.

[push-down accumulation]: pat-push-down-accumulation.html

[^issue-27245]: See [Issue #27245](https://github.com/rust-lang/rust/issues/27245).