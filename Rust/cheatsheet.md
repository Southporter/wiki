[TOC]

# Notes

### General

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



#### Modules

Access via absolute path:

```rust
crate::module_a::submodule_aa::func_a();
```

Or relative path:

```rust
// in the same file as module a
// or in which module a is in scope
module_a::submodule_aa::func_a();

// in submodule_aa, super refers to parent module
super::submodule_ab::func_b();
```



###### Public vs Private

All constructs are private by default.

Privacy applies looking down, but not up, which means parents can't access the private items of children, but children can access parent items.

```rust
mod top_level {
    pub mod second_level {
        pub fn accessable_fn() {
            // This is allowed
            super::private_second_level::private_fn()
            // And can access other private methods
            unaccessable_fn()
        }
        
        fn unaccessable_fn() {}
    }
    mod private_second_level {
        fn private_fn() {}
    }
}

pub fn lib_fn_here() {
    // This works
    top_level::second_level::accessable_fn()
    // this won't compile
    top_level::private_second_level::private_fn
}
```

###### Same Name

```rust
use std::fmt::Result;
use std::io::Result as IoResult;
```

Can rename using `as`.

###### Grouping imports

```rust
use std::cmp::Ordering;
use std::io;

// Verses
use std::{cmp::Ordering, io};
```

With self:

```rust
use std::io;
use std::io::Write;

// Verses
use std::io::{self, Write}
// Can now use io::Module
```



###### Idiomatic `use`

* don't `use` functions. `use` the containing module.
* do `use` structs, enums
* `pub use` allows for re-exporting interal modules
* `use` the glob operator (`*`) to bring all public items **sparingly**. (Often used in testing)

###### Spliting into files

```rust
// src/lib.rs
pub mod top_level;

// src/lib/top_level.rs
pub mod second_level;
fn private_fn() {
    // snip
}

// src/lib/top_level/second_level.rs
pub fn public_fn() {
    // snip
}
```



### Collections

#### Vector

###### new

```rust
use std::collections::Vec;

let v: Vec<i32> = Vec::new();
let v = vec![1, 2, 3];
```

###### Item Access

Don't use index value if you can help it. Introduces bugs

```rust
let v = vec![1, 2, 3, 4];
// DON'T do
let third: &i32 = &v[2];
// DO
match v.get(2) {
    Some(third) => {},
    None => {},
}

```

###### Iterate

```rust
for i in &v {...}
for i in &mut v {
    ...access mutable with *i
}
```



###### notes

Dropping a vec drops the contents (lifecycle)

#### String

###### Iterate

```rust
for c in "string".chars() {}
for b in "string".bytes() {}
```

#### HashMap

###### new

```rust
use std::collections::HashMap;

let mut h = HashMap::new();
```

###### Iterate

```rust
for (key, value) in &hash {...}
```

###### Insert

```rust
let mut scores = HashMap::new();

scores.insert(String::from("blue"), 10);

// Only inserts 50 if blue does not have a value
scores.entry(String::from("blue")).or_insert(50);
```



### Error Handling

#### panic

Panic is unrecoverable.

```rust
panic!("This stops execution immediately")
```

#### Result

```rust
// returns Result<File, Err>
let f = File.open("file.txt");

let f = match f {
    Ok(file) => file,
    Err(error) => match error.kind() {
        ...Check different kinds of errors
        _ => {}, //default case
    }
}
```

###### Propagating Errors

The `?` operator propagates errors down.

```rust
let f = File::open("hello.txt")?;
```

It can only be used in functions that return `Result`.

###### Result in `main()`

```rust
// Box<dyn Error> is generic for any error
fn main() -> Result<(), Box<dyn Error>> {
    // -- Do Stuff That might error --
    fn_that_might_error()?;

    Ok(()) // Means success
}
```

### Generics

In function signature:

```rust
fn largest<T>(list: &[T]) -> T {}
fn combine<T, U, V>(first: &T, second: &U) -> V {}
```

In structs and impls:

```rust
struct Point<T> {
    x: T,
    y: T,
}
// x and y can be of different types
struct Point<T, U> {
    x: T,
    y: U,
}

impl<T> Point<T> {
    fn x(&self) -> &T { &self.x }
}
impl Point<f32> {
    // Implement some feature only on f32 versions of point
    fn distance_from_origin(&self) -> f32 {
        (self.x.powi(2) + self.y.powi(2)).sqrt()
    }
}
```

in Enums:

```rust
enum Option<T> {
    Some(T),
    None,
}
enum Result<T, E> {
    Ok(T),
    Err(E),
}
```

Generics have no impact on runtime. During compilation, the different variants used are broken out into concrete implementations by the compiler.

#### Traits

Traits are almost like interfaces.

Definition:

```rust
pub trait Summary {
    fn summarize(&self) -> String;
}
```

Implementing on a type:

```rust
pub struct Article {
    pub headline: String,
    pub location: String,
    pub author: String,
}

impl Summary for Article {
    fn summarize(&self) -> String {
        format!("{}, by {}", self.headline, self.author)
    }
}
```

**Note**: You can only implement `pub` traits that are currently __in scope__ AND at least the trait or the struct is __local__.



Can do default implementations:

```rust
pub trait Summary {
    fn summarize_author(&self) -> String;
    fn summarize(&self) -> String {
        String::from("(Read more from {})", self.summarize_author())
    }
}
```



###### In Generics

Can use as type of parameter:

```rust
pub fn notify(item: impl Summary) {}
```

and in generics:

```rust
pub fn notify<T: Summary>(item: T) {}
```

and can be combined:

```rust
pub fn notify<T: Summary + Display>(item: T) {}
```

and when there are a lot:

```rust
fn some_fn<T, U>(t: T, u: U) -> i32
    where T: Display + Clone,
          U: Clone + Debug
{}
```

###### Returning Traits

```rust
fn gen_article() -> impl Summary {}
```

However, this does **not** allow you return different types:

```rust
fn gen_article(type: i32) -> impl Summary {
    // NOT ALLOWED
    if (type == 0) {
        Article {...}
    } else {
        Tweet {...}
    }
}
```



### Lifetimes

Lifetimes only apply to references. (i.e. `&str` and `&mut str`)

Lifetimes are identified with a `'`. (i.e. `'a`, `'b`, etc.)

Lifetimes prevent dangling pointers, variables out of scope

Lifetime annotation:

```rust
fn longest<'a>(x: &'a str, y: &'a str) -> &'a str {}
```

Means that both x and y should have the same lifetime.

Lifetimes don't have to apply to all parameters equally:

```rust
fn longest<'a, 'b>(x: &'a str, y: &'b str, z &str) -> 'a { x }
```

Lifetimes in `struct`s if you are borrowing:

```rust
struct BorrowedString<'a> {
    part: &'a str,
}
```

But this means that the `struct` can only live as long as the borrowed value

Most lifetimes can be extrapolated by the compiler. If the compiler cannot extrapolate, then the programmer needs to be explicit. Extrapolation (read Elision) rules:

* Each reference gets its own lifetime
* If there is only one input lifetime, that lifetime is assigned to all output lifetimes
* If `&self` is one of the inputs, the lifetime of `self` is the output lifetime(s)

If after following these rules the compiler still cannot figure it out, it is kicked out to the programmer.

###### Static lifetime

`'static` allows for a lifetime that lasts the duration of the program. All string literals automatically get the `'static` lifetime.



### Testing

```rust
#[cfg(test)]
mod tests {
    #[test]
    fn it_works() {
        assert_eq!(true, true);
        assert_ne!(3, 4);
        assert!(true); // Checks if param is true
        assert_eq!("hello".contains("he"), "Reson goes here");
    }
    #[test]
    #[should_panic(expected = "Message goes here (optional)")] // For code that panics
    fn panic_here() {}
    
    #[test]
    fn get_result() -> Result<(), String> {
        ...
    }
    
    #[test]
    #[ignore]
    fn ignored_test() {}
}
```

Running with `--nocapture` will allow the internal output to be seen

`cargo test -- --nocapture`

`cargo test name_of_test` to run only that test

`cargo test part` runs tests with names that contain the word `part`

`cargo test -- --ingored` runs only ignored tests



### Closures

```rust
let closure = |arg1, arg2| {
    // Do something with args
    (arg1, arg2)
};
// Can annotate, but don't have to
let closure = |arg1: u32, arg2: String| -> u32 {}
// Don't have to have {} if it is simple enough
let add_one = |x| x + 1;
```

This does NOT allow you to call the closure with 2 different types:

```rust
let closure = |x| x;
// NOT allowed
let s = closure(String::from("Hello"));
let n = closure(2);
```

###### Using Closures as variables

Closure Traits: `Fn`, `FnMut`, or `FnOnce`

```rust
struct ClosureHolder<T>
    where T: Fn(u32) -> u32
{
    calculate: T,
    value: Option<u32>,
}
```

* `Fn`: Borrows immutably
* `FnMut`: Borrows all values mutably: use `move ||` when defining a mutable closure
* `FnOnce`: Consumes vlaues (takes ownership)



### Iterators

Only requres you to implement the `next` method.

Method returns `Some` if there is an item, and `None` when there are none left.

* `iter` for immutable references
* `into_iter` for getting ownership
* `iter_mut` for mutable references

As long as you implement the `next` method, you get a lot for free:

* `.zip()`
* `.map(Fn)`
* `.filter(Fn)`
* `.collect()`
* `.sum()`
* etc.



Closures and Iterators are Zero-cost abstractions.



### Documentation

`///` Describes a documentation comment block.

`//!` Is also a documentation block not attached. It is typically used for create comments in `src/lib.rs` or inside a module to describe the module as a whole

It supports Markdown

Place it just before what it is documentation



Run `cargo doc --open` to build the HTML from your comments

###### Common Sections

* Examples
* Panics
* Errors
* Safety (is the function `unsafe` at all)

Putting code blocks in your documentation gets you free tests:

```rust
/// ```
/// assert_eq!(pkg::add_one(5), 6);
/// ```
pub fn add_one(x: i32) -> i32 {
    x + 1
}
```

then running `cargo test` will run the assert.

###### Publishing

Publishing is permanent. Cannot be overwritten or removed.

Can prevent future use of a bad versuion with `cargo yank --vers x.x.x`.

Undo the yank with `cargo yank --vers x.x.x --undo`



### Workspaces

Allows you to develop multiple related packages in tandem. Like monorepos (npm's lerna)???

Provides one `target` directory for the whole project.

Dependencies are still managed in the packages, but are unified in the workspace. This allows for all packages to be compatible with each other.

Can run cargo command only in one package with the `-p` flag.
Publishing must be done from each crate's directory.



### Smart Pointers

Must implement the `Deref` and `Drop` traits

###### Common

* `Box<T>` allocates to the heap
* `Rc<T>` Counting type, allows multiple ownership
* `Ref<T>`, `RefMut<T>` enforces borrowing at runtime, not compile time.

###### `Box<T>`

Most simple. No performance overhead. But no extra capabilities.

```rust
fn main() {
    let b = Box::new(5); // 5 is on the heap
    let six = b + 1; // Can reference like a normal variable
}
```

Deallocation is automagic with `Box<T>`

###### Custom

```rust
struct MyPointer<T>(T);

impl<T> MyPointer<T> {
    fn new(x: T) -> MyPointer<T> {
        MyPointer(x)
    }
}

use std::ops::Deref;

impl<T> Deref for MyPointer<T> {
    type Target = T;
    
    fn deref(&self) -> &T {
        &self.0
    }
}

impl<T> Drop for MyPointer<T> {
    type Target = T;

    fn drop(&mut self) {
        // Do something to clean up
    }
}
```

###### Coercion

Rust will coerce any smart reference down to a dereference for us. Allows us to write this:

```rust
fn hello(name: &str) {
    println!("Hello, {}!", name);
}
```

To work with any smart pointer to a str equivalent.



###### Drop

You can drop early with `std::mem::drop`

```rust
use std::mem::drop;
// --snip
drop(c);
```



#### `Rc<T>`

Counts the references.  Allows multiple ownership.

Use ONLY in single-threaded.



#### `RefCell<T>`

Allows for mutation of data even if there are immutable references to the data.

Not multiple ownership, only single.

Enforces borrowing at `runtime` not compile time. Causes a panic if you break the rules.

Use ONLY in single-threaded.

Still only allows many immutable borrows OR one mutable borrow at a time. But it is checked at runtime instead.

Other types that provide interior mutability:
`Cell<T>` and `Mutex<T>`. `Cell<T>` is like `RefCell<T>` except the value is copied in and out. `Mutex<T>` is the thread safe version.

###### Cycles

If you have something similar to `RefCell<Rc<T>>`, you can create cycles. This causes memory leaks. Rust won't catch them. You have to catch them using standard tools.

You can also use `Rc::downgrade` instead of `Rc::clone` to create `Weak<T>` pointers.



#### Links

[1]: https://doc.rust-lang.org/book/ch06-01-defining-an-enum.html