允许在trait中引入一个占位符类型，而具体的类型将在实现该trait时确定。

```rust
trait MyTrait {
    type AssociatedType;

    // 可以在trait中使用关联类型
    fn do_something(&self) -> Self::AssociatedType;
}

struct MyStruct;

impl MyTrait for MyStruct {
    type AssociatedType = u32;

    fn do_something(&self) -> Self::AssociatedType {
        // 实现具体逻辑
        42
    }
}
```

当使用关联类型时，不需要在每个实现中指定具体的类型，因为每个实现只能选择一次关联类型的具体类型。这使得代码更加简洁和易于使用。下面是一个可运行的例子，展示了如何使用关联类型来定义一个简单的迭代器：

```rust
pub trait Iterator {
    type Item;

    fn next(&mut self) -> Option<Self::Item>;
}

struct Counter {
    count: u32,
    max: u32,
}

impl Iterator for Counter {
    type Item = u32;

    fn next(&mut self) -> Option<Self::Item> {
        if self.count < self.max {
            let result = Some(self.count);
            self.count += 1;
            result
        } else {
            None
        }
    }
}

fn main() {
    let mut counter = Counter { count: 0, max: 5 };

    while let Some(num) = counter.next() {
        println!("{}", num);
    }
}
```

在这个例子中，定义了一个简单的迭代器 trait `Iterator`，其中有一个关联类型 `Item`。然后，实现了一个名为 `Counter` 的结构体，并为其实现了 `Iterator` trait。在 `Counter` 的实现中，我们指定了关联类型 `Item` 的具体类型为 `u32`。

在 `main` 函数中，我们创建了一个 `Counter` 对象，并使用 `while let` 循环来遍历迭代器的元素。每次迭代调用 `counter.next()`，它会返回一个 `Some(num)` 值，其中 `num` 是当前计数器的值，直到计数器超过了指定的最大值，返回 `None` 结束循环。

通过使用关联类型，我们不需要在每个实现中指定具体的类型，而是在 trait 中定义一次，并且在实现中选择具体的类型。这使得代码更加简洁和灵活。