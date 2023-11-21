
对于特质（trait）来说，使用`'static`生命周期表示特质对象可以在整个程序的运行期间保持有效。

`'static` 生命周期是最长的生命周期，它表示该对象在程序的整个运行期间都有效，并不会被销毁。

例子：下面的代码中，这意味着实现了`Metrics`特质的类型的对象可以在任何时间点创建，并在整个程序的执行过程中一直存在。

```rust
pub trait Metrics: 'static + Send + Sync {
    /// Metrics descriptor.
    const DESCRIPTOR: MetricGroupDescriptor;

    #[doc(hidden)] // implementation detail
    fn visit_metrics(&self, visitor: MetricsVisitor<'_>);
}
```
