---
layout: post
title:  Is Rust a language of the future?
date:   2023-12-07 20:48:03 +0000
categories: rust programming data engineering
banner:
  image: https://github.com/BenhamOT/BenhamOT.github.io/blob/main/docs/assets/images/rust-crab.png
tags: rust programming data-engineering
---

### Overview:

This blog post provides an introduction to Rust, covering some distinguishing features of the language. I talk through one of the
main features that sets Rust aside from other languages, the principle of ownership. There's an overview
of how I started learning the language and where to find the material I used to get myself going. Finally, I finish
by exploring its adoption generally, how it's used in the data space and my future predictions.

### Introduction:

About 9 months ago I had separated conversations with a couple of mates I used to work with. Coincidentally,
both of them were telling me about this programming language, Rust, they’d recently become interested in.
Slightly skeptical at first, I started doing my own research and pretty quickly I was sold. Having spent the vast
majority of my professional career almost exclusively using Python, I was in the market for learning another language.

### Rust:

This section regurgitates a lot of the same info that you’d find in any intro article about Rust, but I felt
the blog post wouldn't be complete enough without it.

#### Memory Safety:

One of Rust's standout features is its emphasis on memory safety. Traditional programming languages like
C and C++ provide low-level control over memory management but this often leaves room for bugs and errors
that can be exploited. Rust eliminates these common issues by employing
a strong static type system and strict borrowing and ownership rules; they'll be more on borrowing and ownership later in the post.
Collectively, these features ensure that memory-related
bugs are caught at compile-time (Rust is a compiled language like C, C++, C# and Go among others), leading to
more reliable and secure code.

#### High Performance:

While safety is paramount, Rust is also very performant. Rust code that can be as fast and efficient as C or C++. There
is also a handy concept called zero-cost abstractions which means that you're able to use higher level abstractions when
writing your code, without taking a performance hit. 

#### Concurrency Made (sort of) Easy:

Rust offers robust support for concurrency through its ownership model (more on this further down) and the concept of 
"fearless concurrency". With Rust's built-in concurrency primitives, developers can write concurrent code that they can be confident in.

#### Developer-Friendly Tooling:

Something I found surprisingly pleasant when I started learning Rust was how accessible the learning material is.
In my experience, many programming languages that have been around for a longer time have a less obvious
starting point in terms of the best book/course etc to choose. With Rust there appeared to be a clear way
to get started; the [Rust website][rust-site] has lots of helpful resources, including a [book][book], 
[programming exercises][exercises] and [examples][examples]. I found this to be pretty much everything you 
need to go from no experience to a level of skill where you can start building your own projects.

[rust-site]: https://www.rust-lang.org
[book]: https://doc.rust-lang.org/book/
[exercises]: https://github.com/rust-lang/rustlings/
[examples]: https://doc.rust-lang.org/rust-by-example/

In addition to the helpful and centralised learning resources, Rust also has an excellent package manager and 
build system, Cargo. Cargo makes it easy to manage dependencies and set up projects. Coming from a Python background
where there are arguably too many options for installing libraries and managing dependencies, it was quite refreshing to
have a single tool.

It certainly isn't all plain sailing though. Someone new to the language is likely 
to encounter a few confused moments along the way because Rust is notoriously quite difficult to learn in comparison to many other popular
programming languages (Python or Go, for example). In my experience this is mainly down to the ownership ... 

### Ownership.

In some programming languages you generally don't have to think about memory
management e.g. Python, in others you have to manage it all manually yourself (c++). Rust adopts a third 
approach to memory management, ownership. Coming from a Python background, I’d never had to think about 
memory management before, but I now appreciate how understanding ownership has helped me get a better grasp of
programming at a deeper level.

In Rust, every value has a variable that is its "owner." You can borrow references to a value but only in a 
way that ensures the owner retains control (You'll see & appear in the code when a value is being borrowed).
Rust also has a system of lifetimes that ensures references are valid for a specific scope. 

Here's an analogy that should better explain the above. Imagine the ownership system in Rust as a party. At 
the party, each piece of data (value) is like a gift, and 
each partygoer (variable) is the owner of a specific gift. The owner is responsible for taking care of their 
gift, passing it around, or giving it to someone else. At any given time, only one person owns a gift. If you want 
to give your gift to someone else, you must first relinquish ownership. If you want to show your gift to someone
without giving it away, you can let them borrow it. However, you ensure that you don't lose ownership, and they'll give 
it back when they're done. Finally, the lifetime of the party determines how long a gift can be passed 
around. Once the party is over, everyone returns their gifts to the rightful owners.

Below is a simple example of some Rust code that demonstrates how ownership works in practice. If you want to run the
code you can use the Rust [installation guide][install] to get you started. You can then pick/create a new directory and run 
`cargo init`. Once you've done that you should be able to run `cargo run`.

[install]: https://www.rust-lang.org/tools/install 

```rust
fn main() {
    // Creating a String and taking ownership
    let original_string = String::from("Hello, ownership!");

    // Passing ownership of original_string to a function
    take_ownership(original_string);

    // Uncommenting the line below will result in a compilation error because ownership of original_string has moved
    // println!("Original string: {}", original_string);

    // Creating an integer
    let number = 42;
    
    // Borrowing a reference to the integer. & is used to specify that we're passing in a reference and not transferring ownership
    print_reference(&number);

    // The original variable can still be used after borrowing its reference because the print_reference function didn't take ownership
    println!("Original number: {}", number);
}

fn take_ownership(s: String) {
    // s is the owner of the String passed in
    println!("Received ownership: {}", s);
    // The s String will be dropped when this function scope ends
}

fn print_reference(num: &i32) {
    // num is a reference to an integer 
    println!("Received reference: {}", num);
    // The original variable can still be used after borrowing its reference
}
```

If you choose to do more research into Rust and the ownership system you'll soon come across the stack and the heap, 
which underpin memory management.

The stack is a region of memory that follows the Last In, First Out principle. It's used for 
storing data with a known, fixed size at compile time. A helpful analogy that you'll find in the free [Rust book][book] 
is thinking about _the stack_ as a stack of plates. You stack plates on top of each other and when you want to
take a plate off the stack you need to take the plate from the top of the pile. On the other hand, the heap
is a region of memory that is more flexible. It is used for storing data with an unknown size at compile
time or a size that might change during the program's execution. Data on the heap is allocated and 
deallocated dynamically. As you might guess, assessing data on the stack is faster than accessing data on the heap. 


### General adoption

Rust has been on the rise for a number of years now; it's been the most loved language in the Stackoverflow develop
survey for the last 7 years in a row. It's also been adopted by some of the largest, most influential 
tech companies/organisations around. Here are a few recent highlights:  

* A Microsoft VP recently announced on X (twitter) that Microsoft would be investing $10m to make Rust a first class language. They also donated 1$ 
to the Rust foundation.
* Rust has been adopted in the linux kernel, the core of the linux operating system.
* Rust is relied upon at AWS for a range of critical services. For example, both Lambda and Fargate are built on top of 
Firecracker, an open-source virtualization technology which enables you to create microVMs. Firecracker was developed at AWS and
written in Rust.
* AWS has just released the [Rust AWS SDK][rust-aws-sdk].

[rust-aws-sdk]: https://docs.aws.amazon.com/sdk-for-rust/latest/dg/getting-started.html

Rust is also starting to make waves in the data industry. Anyone that's ever worked in the data space, 
whether that be an analyst, scientist or engineer, is likely to have come across the python library, Pandas.
[Polars][polars] is a new library, written in Rust with a python interface, that's slowly eating into the Pandas market share.
Polars is really fast, memory efficient and arguably (I'm not sure there's much of an argument) more enjoyable to use than pandas. 

The core of [Pydantic][pydantic], the python data validation library has recently been re-written in Rust and, according to the
[github page][pydantic-core], it's now 17x faster than the original version. An interesting start-up called 
[Pathway][pathway] that I recently came across are using Rust with a python interface to create a
real-time data processing framework designed for LLM applications, among other things.

[polars]: https://pola.rs
[pydantic]: https://docs.pydantic.dev/latest/
[pydantic-core]: https://github.com/pydantic/pydantic-core
[pathway]: https://pathway.com

### Predictions

I think we're likely to see Rust start to displace some low level programming languages like C/C++ and eat into the parts of the
market dominated by the JVM (Java Virtual Machine) based languages (Java, Scala, etc) that underpin many popular data processing 
frameworks (Spark, flink, kafka). From a data engineering perspective, I think we'll slowly see more and more things being
written in Rust with a python interface or SDK.

Is it likely to change a data professionals day to day? Probably not dramatically. 
Given the fairly steep learning curve and the challenges around fast prototyping, I think we'll continue to see python dominate
for the most part when it comes to writing code day to day. However, I'd say there's a good changes that some new 
data processing frameworks and libraries will continue popping up, all powered by Rust (think 
spark but written in Rust instead of Java). Watch this space. 

### The End

This blog post just scratches the surface of Rust. Thankfully, if you're interested, there are some excellent free
resources that you can find online. The best place to start is https://www.rust-lang.org. 