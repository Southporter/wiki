#### 

[TOC]

# Notes

###### Functions

```rust
fn main(var1: int32) -> int32 {
    let ret = 0;
    ret // If return is on the last line,
    // no semicolon means that is the value returned
}
```

###### if statement



###### Match

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



###### Enums 

[Book link][1]

Enums in Rust are quite powerful

You can store values inside enums:

```rust
enum IpAddr {
    v4(u8, u8, u8, u8),  // Stores a series of 4 bytes
    v6(String),  // Stores a string
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







###### Links

[1]: https://doc.rust-lang.org/book/ch06-01-defining-an-enum.html