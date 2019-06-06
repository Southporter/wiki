[TOC]

# Notes

###### Functions

```rust
fn main(var1: int32) -> int32 {
    let ret = var1;
    ret // If return is on the last line,
    // no semicolon means that is the value returned
}
```

#### Match

Match is rust's switch statement.

```rust
use std::cmp::Ordering;
fn isEqual(standard: u32, candidate: u32) -> bool {
    match standard.cmp(&candidate) {
        Ordering::Equal => true,
        _ => false  // _ is the default case
    }   
}
```

Cases can also be blocks.

```rust
match num.cmp(&another_num) {
    Ordering::Equal => {
        println!("They are equal");
        return true;
    }
}
```

Default case is `_`

```rust
let some_u8_value = 0u8;
match some_u8_value {
    1 => println!("odd"),
    3 => println!("odd"),
    5 => println!("odd"),
    7 => println!("odd"),
    _ => println!("even"),
}
```

###### if let

If let lets you handle a single case with out having to enumerate the other options.

```rust
let penny = Coin::Penny;
if let Coin::Penny = penny {
    println!("Found a lucky penny!");
} else {
    println!("It's not a penny...");
}
```



#### Enums 

[Book link][1]

Enums in Rust are quite powerful

You can store values inside enums:

```rust
enum IpAddr {
    // Enum values must start with a capital
    V4(u8, u8, u8, u8),  // Stores a series of 4 bytes
    V6(String),  // Stores a string
}
```

And can implement methods on the enum:

```rust
impl IpAddr {
    fn connect(&self) {
        // Connect to an address
    }
}
```

Enums can contain other enums:

```rust
enum UsState {
    Alabama,
    Alaska,
    ...
}
enum Coin {
    ...
    Quarter(UsState),
}
```

##### Option\<T\>

Option is Rust's version of null handling. Instead of handling null pointers and running into Null Pointer Exceptions, Rust returns an Option\<T\> that forces you to handle both cases: when you have a value (Some) or no value (None).

```rust
fn extract(x: Option<i32>) -> i32 {
    match x {
        None => 0,
        Some(i) => i,
    }
}
```











#### Links

[1]: https://doc.rust-lang.org/book/ch06-01-defining-an-enum.html