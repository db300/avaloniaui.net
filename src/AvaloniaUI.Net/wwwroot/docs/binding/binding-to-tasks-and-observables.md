---
Title: Binding to Tasks and Observables
Order: 40
---

You can subscribe to the result of a task or an observable by using the `^` stream binding operator.

可以使用`^`流绑定运算符订阅任务或可观察对象的结果。

For example if `DataContext.Name` is an `IObservable<string>` then the following example will bind
to the length of each string produced by the observable as each value is produced

例如，如果`DataContext.Name`是`IObservable<string>`，则下面的示例随着每个值产生，将绑定到可观察对象产生的每个字符串的长度

```xml
<TextBlock Text="{Binding Name^.Length}"/>
```
