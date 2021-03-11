---
Title: Binding from Code-从代码绑定
Order: 50
---

Binding from code in Avalonia works somewhat differently to WPF/UWP. At the low level, Avalonia's
binding system is based on Reactive Extensions' `IObservable` which is then built upon by XAML
bindings (which can also be instantiated in code).

Avalonia中通过代码进行绑定与WPF/UWP有所不同。在较低的层次上，Avalonia的绑定系统基于Reactive Extensions `IObservable`，然后建立于XAML绑定（也可以在代码中实例化）。

## Subscribing to Changes to a Property

You can subscribe to changes on a property by calling the `GetObservable`
method. This returns an `IObservable<T>` which can be used to listen for changes
to the property:

可以通过调用`GetObservable`方法来订阅属性的更改。这将返回一个`IObservable<T>`，可用于监听该属性的更改：

```csharp
    var textBlock = new TextBlock();
    var text = textBlock.GetObservable(TextBlock.TextProperty);
```

Each property that can be subscribed to has a static readonly field called
`[PropertyName]Property` which is passed to `GetObservable` in order to
subscribe to the property's changes.

每个可被订阅的属性都有一个`[PropertyName]Property`的静态只读字段，该字段将传递给`GetObservable`以订阅该属性的更改。

`IObservable` (part of Reactive Extensions, or rx for short) is out of scope
for this guide, but here's an example which uses the returned observable to
print a message with the changing property values to the console:

IObservable （Reactive Extensions的一部分，或简称rx）在本指南的范围之外，但这是一个示例，该示例使用返回的observable将具有更改的属性值的消息打印到控制台：

```csharp
    var textBlock = new TextBlock();
    var text = textBlock.GetObservable(TextBlock.TextProperty);
    text.Subscribe(value => Console.WriteLine(value + " Changed"));
```

When the returned observable is subscribed, it will return the current value
of the property immediately and then push a new value each time the property
changes. If you don't want the current value, you can use the rx `Skip`
operator:

订阅返回的observable时，它将立即返回属性的当前值，然后每次属性更改时都推入新值。如果您不希望使用当前值，则可以使用rxSkip 运算符：

```csharp
    var text = textBlock.GetObservable(TextBlock.TextProperty).Skip(1);
```

## Binding to an observable

You can bind a property to an observable using the `AvaloniaObject.Bind` method:

您可以使用以下AvaloniaObject.Bind方法将属性绑定到可观察对象：

```csharp
// We use an Rx Subject here so we can push new values using OnNext
var source = new Subject<string>();
var textBlock = new TextBlock();

// Bind TextBlock.Text to source
var subscription = textBlock.Bind(TextBlock.TextProperty, source);

// Set textBlock.Text to "hello"
source.OnNext("hello");
// Set textBlock.Text to "world!"
source.OnNext("world!");

// Terminate the binding
subscription.Dispose();
```

Notice that the `Bind` method returns an `IDisposable` which can be used to terminate the binding.
If you never call this, then then binding will automatically terminate when the observable finishes
via `OnCompleted` or `OnError`.

请注意，该Bind方法返回IDisposable，可用于终止绑定。如果您从未调用过此方法，则当可观察到的结束时，通过OnCompleted或绑定将自动终止OnError。

## Setting a binding in an object initializer

It is often useful to set up bindings in object initializers. You can do this using the indexer:

在对象初始化程序中设置绑定通常很有用。您可以使用索引器执行此操作：

```csharp
var source = new Subject<string>();
var textBlock = new TextBlock
{
    Foreground = Brushes.Red,
    MaxWidth = 200,
    [!TextBlock.TextProperty] = source.ToBinding(),
};
```

Using this method you can also easily bind a property on one control to a property on another:

使用此方法，您还可以轻松地将一个控件上的属性绑定到另一个控件上的属性：

```csharp
var textBlock1 = new TextBlock();
var textBlock2 = new TextBlock
{
    Foreground = Brushes.Red,
    MaxWidth = 200,
    [!TextBlock.TextProperty] = textBlock1[!TextBlock.TextProperty],
};
```

Of course the indexer can be used outside object initializers too:

当然，索引器也可以在对象初始化程序之外使用：

```csharp
textBlock2[!TextBlock.TextProperty] = textBlock1[!TextBlock.TextProperty];
```

The only downside of this syntax is that no `IDisposable` is returned. If you need to manually
terminate the binding then you should use the `Bind` method.

这种语法的唯一缺点是没有IDisposable返回。如果需要手动终止绑定，则应使用该Bind方法。

## Transforming binding values

Because we're working with observables, we can easily transform the values we're binding!

因为我们正在使用可观察变量，所以我们可以轻松地转换我们绑定的值！

```csharp
var source = new Subject<string>();
var textBlock = new TextBlock
{
    Foreground = Brushes.Red,
    MaxWidth = 200,
    [!TextBlock.TextProperty] = source.Select(x => "Hello " + x).ToBinding(),
};
```

## Using XAML bindings from code

Sometimes when you want the additional features that XAML bindings provide, it's easier to use XAML bindings from code. For example, using only observables you could bind to a property on `DataContext` like this:

有时，当您需要XAML绑定提供的其他功能时，可以更轻松地使用代码中的XAML绑定。例如，仅使用可观察对象，就可以DataContext像这样绑定到一个属性：

```csharp
var textBlock = new TextBlock();
var viewModelProperty = textBlock.GetObservable(TextBlock.DataContext)
    .OfType<MyViewModel>()
    .Select(x => x?.Name);
textBlock.Bind(TextBlock.TextProperty, viewModelProperty);
```

However, it might be preferable to use a XAML binding in this case:

但是，在这种情况下，最好使用XAML绑定：

```csharp
var textBlock = new TextBlock
{
    [!TextBlock.TextProperty] = new Binding("Name")
};
```

Or, if you need an `IDisposable` to terminate the binding:

或者，如果您需要IDisposable终止绑定，请执行以下操作：

```csharp
var textBlock = new TextBlock();
var subscription = textBlock.Bind(TextBlock.TextProperty, new Binding("Name"));

subscription.Dispose();
```

## Subscribing to a Property on Any Object

The `GetObservable` method returns an observable that tracks changes to a property on a single
instance. However, if you're writing a control you may want to implement an `OnPropertyChanged`
method which isn't tied to an instance of an object.

该GetObservable方法返回一个可观察的对象，该对象可跟踪单个实例上属性的更改。但是，如果要编写控件，则可能要实现一种OnPropertyChanged 与对象实例无关的方法。

To do this you can subscribe to 
[`AvaloniaProperty.Changed`](/api/Avalonia/AvaloniaProperty/65237C52) which is an observable which
fires _every time the property is changed on any instance_.

为此，您可以订阅 AvaloniaProperty.Changed一个observable，该observable每次在任何实例上更改属性时都会触发。

> In WPF this is done by passing a static `PropertyChangedCallback` to the `DependencyProperty`
  registration method, but this only allows the control author to register a property changed
  callback.

> 在WPF中，这是通过将静态变量传递PropertyChangedCallback给DependencyProperty 注册方法来完成的，但这仅允许控件作者注册属性已更改的回调。

In addition there is an `AddClassHandler` extension method which can automatically route the 
event to a method on your control.

此外，还有一个AddClassHandler扩展方法，可以将事件自动路由到控件上的方法。

For example if you want to listen to changes to your control's `Foo` property
you'd do it like this:

例如，如果您想听控件Foo属性的更改，可以这样：

```csharp
static MyControl()
{
    FooProperty.Changed.AddClassHandler<MyControl>(x => x.FooChanged);
}

private void FooChanged(AvaloniaPropertyChangedEventArgs e)
{
    // The 'e' parameter describes what's changed.
}
```
