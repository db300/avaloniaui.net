---
Title: Binding to Controls-绑定到控件
Order: 20
---

As well as binding to a control's [`DataContext`](datacontext) you can also bind to other
controls.

除了绑定到控件的[`DataContext`](datacontext)之外，也可以绑定到其他控件。

> Note that when you do this, the binding source is to the _control itself_ not the control's
  `DataContext`. If you want to bind to the control's `DataContext` then you'll need to
  specify that in the binding path.

> 请注意，执行此操作时，绑定源是 _控件本身_，而不是控件的`DataContext`。如果要绑定到控件的`DataContext`，则需要在绑定路径中指定。

## Binding to a named control

If you want to bind to a property on another named control, you can use the control name
prefixed by a `#` character.

如果要绑定到另一个有命名控件的属性，则可以使用以`#`为前缀的控件名称。

```xml
<TextBox Name="other">

<!-- Binds to the Text property of the "other" control -->
<TextBlock Text="{Binding #other.Text}"/>
```

This is the equivalent to the long-form binding that will be familiar to WPF and UWP users:

这等效于WPF和UWP用户熟悉的长格式绑定：

```xml
<TextBox Name="other">
<TextBlock Text="{Binding Text, ElementName=other}"/>
```

Avalonia supports both syntaxes but the short-form `#` syntax is less verbose.

Avalonia支持这两种语法，但短格式`#`语法不太冗长。

## Binding to an Ancestor

You can bind to the logical parent of the target using the `$parent` symbol:

可以使用`$parent`符号绑定到目标的逻辑父级：

```xml
<Border Tag="Hello World!">
  <TextBlock Text="{Binding $parent.Tag}"/>
</Border>
```

Or to an ancestor by adding an indexer to the `$parent` symbol:

或通过向`$parent`符号添加索引器来绑定到祖先：

```xml
<Border Tag="Hello World!">
  <Border>
    <TextBlock Text="{Binding $parent[1].Tag}"/>
  </Border>
</Border>
```

The indexer is 0-based so `$parent[0]` is equivalent to `$parent`.

索引器基于0，因此`$parent[0]`等效于`$parent`。

You can also bind to an ancestor by type:

也可以通过类型绑定到祖先：

```xml
<Border Tag="Hello World!">
  <Decorator>
    <TextBlock Text="{Binding $parent[Border].Tag}"/>
  </Decorator>
</Border>
```

Finally, you can combine the indexer and the type:

最后，可以结合索引器和类型：

```xml
<Border Tag="Hello World!">
  <Border>
    <Decorator>
    <TextBlock Text="{Binding $parent[Border;1].Tag}"/>
    </Decorator>
  </Border>
</Border>
```

If you need to include a XAML namespace in the ancestor type, you can do that using the `:`
character as normal:

如果需要在祖先类型中包括XAML名称空间，可以使用`:`字符：

```xml
<local:MyControl Tag="Hello World!">
  <Decorator>
    <TextBlock Text="{Binding $parent[local:MyControl].Tag}"/>
  </Decorator>
</local:MyControl>
```

> Avalonia also supports WPF/UWP's `RelativeSource` syntax which does something similar, but is
  _not_ the same. `RelativeSource` works on the _visual_ tree whereas the syntax given here works
  on the _logical_ tree.

> Avalonia也支持WPF/UWP的`RelativeSource`语法，该语法具有相似之处，但不尽相同。`RelativeSource`在 _visual_ tree上工作，而此处给出的语法在 _logical_ tree上工作。
