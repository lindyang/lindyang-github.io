---
title: "Rust"
date: 2023-07-28T14:30:33+08:00
tags: [rust]
---

[多个 struct 之间的 lifetime 关联](https://stackoverflow.com/questions/27589054/what-is-the-correct-way-to-use-lifetimes-with-a-struct-in-rust)
```
struct Game<'a> {
    handlers: Vec<&'a str>,
}


impl<'b> Game<'b> {
    fn match_str(&mut self, label: &'b str) {
        self.handlers.push(label);
    }
}
```

[如何正确的使用 lifetime 标记](https://stackoverflow.com/questions/58422765/how-to-infer-an-appropriate-lifetime-for-an-implementation-using-lifetimes-for-s)
```
struct C;

struct B<'b> {
    c: &'b C,
}

struct A<'a> {
    b: B<'a>,
    c: &'a C
}

impl<'a> A<'a> {
    fn new(c: &'a C) -> A<'a> {
        A {c: c, b: B{c: c}}
    }
}

fn main() {
    let c1 = C;
    let _ = A::new(&c1);
}
```


[PhantomData](https://magiclen.org/rust-phantomdata/)


### 关于生命周期的一些思考
```
struct X<'a>(&'a i32);

struct Y<'a, 'b>(&'a X<'b>);

fn main() {
    let z = 100;
    //taking the inner field out of a temporary
    let z1 = ((Y(&X(&z))).0).0;
    assert!(*z1 == z);
}
```

The purpose of lifetime parameters is to prevent the function or struct from unifying those parameters into a single (inferred) lifetime, so the borrow checker can distinguish between them
```
struct Foo<'a, 'b> {
    x: &'a i32,
    y: &'b i32,
}

fn main() {
    let x = 1;
    let v;
    {
        let y = 2;
        let f = Foo { x: &x, y: &y };
        v = f.x;
    }
    println!("{}", *v);
}
```

```
use std::fmt::Display;

fn longest_with_an_announcement<'a, T>(
    x: &'a str,
    y: &'a str,
    ann: T,
) -> &'a str
where
    T: Display,
{
    println!("Announcement! {}", ann);
    if x.len() > y.len() {
        x
    } else {
        y
    }
}
```

### lifetime_coercion
```
// Here, Rust infers a lifetime that is as short as possible.
// The two references are then coerced to that lifetime.
fn multiply<'a>(first: &'a i32, second: &'a i32) -> i32 {
    first * second
}

// `<'a: 'b, 'b>` reads as lifetime `'a` is at least as long as `'b`.
// Here, we take in an `&'a i32` and return a `&'b i32` as a result of coercion.
fn choose_first<'a: 'b, 'b>(first: &'a i32, _: &'b i32) -> &'b i32 {
    first
}

fn main() {
    let first = 2; // Longer lifetime

    {
        let second = 3; // Shorter lifetime

        println!("The product is {}", multiply(&first, &second));
        println!("{} is the first", choose_first(&first, &second));
    };
}
```

[树](https://www.136.la/jingpin/show-121507.html)
```
use std::{cell::RefCell, rc::Rc};

struct TreeNode<T> {
    value: T,
    left: Option<Rc<RefCell<TreeNode<T>>>>,
    right: Option<Rc<RefCell<TreeNode<T>>>>,
}

fn main() {
    let mut root = TreeNode {
        value: 0,
        left: None,
        right: None,
    };
    let left_node = TreeNode {
        value: 1,
        left: None,
        right: None,
    };
    let right_node = TreeNode {
        value: 2,
        left: None,
        right: None,
    };
    root.left = Some(Rc::new(RefCell::new(left_node)));
    root.right = Some(Rc::new(RefCell::new(right_node)));

    if let Some(ref mut x) = root.left {
        x.borrow_mut().value = 4;
        println!("{}", x.borrow().value);
        // result: 4
    }
}
```
