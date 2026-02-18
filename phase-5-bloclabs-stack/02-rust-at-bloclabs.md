# Rust at BlocLabs

> Rust is for when Go isn't fast enough or safe enough. Use it surgically.

---

## When We Use Rust

| Use Case | Why Rust |
|----------|----------|
| Cryptographic operations | Zero-cost abstractions, no GC pauses |
| WASM modules | First-class WebAssembly target |
| Performance-critical parsers | Memory safety without garbage collection |
| Low-level blockchain interaction | Direct memory control, no runtime overhead |

**Default to Go.** Reach for Rust when you need: zero-cost abstractions, no garbage collector, WebAssembly, or guaranteed memory safety without runtime cost.

---

## Ownership — The Core Mental Model

Rust's superpower: the compiler prevents memory bugs at compile time.

```rust
fn main() {
    let s1 = String::from("hello");
    let s2 = s1;            // s1 is MOVED to s2
    // println!("{}", s1);  // ❌ Compile error! s1 no longer valid
    println!("{}", s2);     // ✅ s2 owns the data
}
```

**Three rules:**
1. Each value has exactly one owner
2. When the owner goes out of scope, the value is dropped
3. You can borrow (reference) but the compiler enforces rules

### Borrowing
```rust
fn calculate_length(s: &String) -> usize {  // Borrows, doesn't own
    s.len()
}

let s = String::from("hello");
let len = calculate_length(&s);  // Pass reference
println!("{} has {} chars", s, len);  // s is still valid
```

### Mutable References
```rust
fn append_world(s: &mut String) {
    s.push_str(", world");
}

let mut s = String::from("hello");
append_world(&mut s);
// Only ONE mutable reference at a time — prevents data races at compile time
```

---

## Error Handling — Result and Option

```rust
// Result for operations that can fail
fn parse_amount(input: &str) -> Result<f64, ParseError> {
    let amount: f64 = input.parse().map_err(|_| ParseError::InvalidFormat)?;
    if amount < 0.0 {
        return Err(ParseError::NegativeAmount);
    }
    Ok(amount)
}

// Option for values that might not exist
fn find_user(id: &str) -> Option<User> {
    users.get(id).cloned()
}

// The ? operator propagates errors
fn process_transaction(input: &str) -> Result<Receipt, AppError> {
    let amount = parse_amount(input)?;  // Returns early on error
    let user = find_user("123").ok_or(AppError::UserNotFound)?;
    Ok(Receipt { amount, user })
}
```

---

## Pattern Matching

```rust
enum ChainType {
    EVM { chain_id: u64 },
    Solana,
    Cosmos { prefix: String },
}

fn get_explorer_url(chain: &ChainType) -> String {
    match chain {
        ChainType::EVM { chain_id: 1 } => "https://etherscan.io".into(),
        ChainType::EVM { chain_id: 137 } => "https://polygonscan.com".into(),
        ChainType::EVM { chain_id } => format!("https://blockscan.com/{}", chain_id),
        ChainType::Solana => "https://solscan.io".into(),
        ChainType::Cosmos { prefix } => format!("https://mintscan.io/{}", prefix),
    }
}
```

---

## Cargo — Package Manager

```bash
cargo new my-project         # Create new project
cargo build                  # Compile
cargo run                    # Compile and run
cargo test                   # Run tests
cargo fmt                    # Format code
cargo clippy                 # Lint (catches common mistakes)
cargo bench                  # Benchmarks
cargo doc --open             # Generate and open docs
```

---

## Go vs Rust — Decision Guide

| Factor | Go | Rust |
|--------|-----|------|
| Learning curve | Days | Weeks-months |
| Compile time | Fast | Slow |
| Runtime performance | Fast (with GC pauses) | Fastest (no GC) |
| Memory safety | GC handles it | Compiler enforces it |
| Concurrency | Goroutines (easy) | async/await + ownership (harder) |
| Ecosystem | Large, mature | Growing, strong for systems |
| Team familiarity | High | Lower |
| Best for | APIs, services, tools | Performance-critical, WASM, crypto |

**BlocLabs rule:** Write it in Go first. Profile it. If a specific component needs more performance, rewrite THAT component in Rust.

---

## Resources

- [The Rust Book](https://doc.rust-lang.org/book/)
- [Rustlings](https://github.com/rust-lang/rustlings) — interactive exercises
- [Rust by Example](https://doc.rust-lang.org/rust-by-example/)
- [Comprehensive Rust](https://google.github.io/comprehensive-rust/) — Google's Rust course

---

*← [Go at BlocLabs](01-go-at-bloclabs.md) · [JavaScript & TypeScript →](03-javascript-typescript.md)*
