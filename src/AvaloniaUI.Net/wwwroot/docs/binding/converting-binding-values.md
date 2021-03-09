---
Title: Converting Binding Values-转换绑定值
Order: 30
---

## Negating Values

Often you will need to negate a value that you're binding to. A frequent use for this is to show
or hide and control or to enable/disable it. You can negate a binding value by prepending a "bang"
operator: `!`.

经常需要取反绑定的值。经常使用这种的情况是显示或隐藏以及控制或启用/禁用它。可以通过加前缀运算符`!`取反绑定值。

For example you might want to show one control when another control is disabled.

例如，可能想在禁用另一个控件时显示一个控件。

```xml
<StackPanel>
  <TextBox Name="input" IsEnabled="{Binding AllowInput}"/>
  <TextBlock IsVisible="{Binding !AllowInput}">Sorry, no can do!</TextBlock>
</StackPanel>
```

Negation also works when binding to non-boolean values. First of all, the value is converted to a
boolean using `Convert.ToBoolean` and the result from this is negated. Because the integer value
`0` is considered `false` and all other integer values are considered `true`, you can use this
to show a message when a collection is empty:

绑定到非布尔值时，取反也有效。首先，使用`Convert.ToBoolean`将值转换为布尔值，然后将其结果取反。因为整型值`0`认为是`false`，所有其他整型值认为是`true`，所以可以利用这一点在集合为空时显示消息：

```xml
<Panel>
  <ListBox Items="{Binding Items}"/>
  <TextBlock IsVisible="{Binding !Items.Count}">No results found</TextBlock>
</Panel>
```

A "double-bang" can be used to convert a non-boolean value to a boolean value. For example to
hide a control when a collection is empty:

“双爆炸”可用于将非布尔值转换为布尔值。例如，当集合为空时隐藏控件：

```xml
<Panel>
  <ListBox Items="{Binding Items}" IsVisible="{Binding !!Items.Count}"/>
</Panel>
```

## Binding Converters

For more advanced conversions, Avalonia supports
[`IValueConverter `](https://docs.microsoft.com/en-gb/dotnet/api/system.windows.data.ivalueconverter?view=netframework-4.7.1)
the same as other XAML frameworks.

对于更高级的转换，Avalonia支持 IValueConverter 与其他XAML框架相同的功能。

> Note: The `IValueConverter` interface is not available in .NET standard 2.0 so we ship our own,
  in the `Avalonia.Data.Converters` namespace.

> 注意：该IValueConverter接口在.NET standard 2.0中不可用，因此我们在Avalonia.Data.Converters名称空间中提供了自己的接口。

Usage is identical to other XAML frameworks:

用法与其他XAML框架相同：

```xml
<Window xmlns="https://github.com/avaloniaui"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
        xmlns:local="clr-namespace:ExampleApp;assembly=ExampleApp">

  <Window.Resources>
    <local:MyConverter x:Key="myConverter"/>
  </Window.Resources>

  <TextBlock Text="{Binding Value, Converter={StaticResource myConverter}}"/>
</Window>
```

## Built-in Converters

Avalonia supplies a number of built-in value converters for common scenarios:

Avalonia为常见情况提供了许多内置的值转换器：

| Converter | Description |
| ---- | ----------- |
| `StringConverters.IsNullOrEmpty` | Returns `true` if the input string is null or empty |
| `StringConverters.IsNotNullOrEmpty` | Returns `false` if the input string is null or empty |
| `ObjectConverters.IsNull` | Returns `true` if the input is null |
| `ObjectConverters.IsNotNull` | Returns `false` if the input is null |
| `BoolConverters.And` | A multi-value converter that returns `true` if all inputs are true. |

| Converter | Description |
| ---- | ----------- |
| `StringConverters.IsNullOrEmpty` | 返回true输入字符串是否为null或为空 |
| `StringConverters.IsNotNullOrEmpty` | 返回false输入字符串是否为null或为空 |
| `ObjectConverters.IsNull` | true如果输入为null，则返回 |
| `ObjectConverters.IsNotNull` | false如果输入为null，则返回 |
| `BoolConverters.And` | 一个多值转换器，true如果所有输入都为true ，则返回。 |

### Examples

Hiding a `TextBlock` if the bound text is null or empty:

TextBlock如果绑定的文本为null或为空，则隐藏a ：

```csharp
<TextBlock Text="{Binding MyText}"
           IsVisible="{Binding MyText, Converter={x:Static StringConverters.IsNotNullOrEmpty}}"/>
```

Hiding a `ContentControl` if the bound content is null or empty:

ContentControl如果绑定的内容为null或为空，则隐藏a ：

```csharp
<ContentControl Content="{Binding MyContent}"
                IsVisible="{Binding MyContent, Converter={x:Static ObjectConverters.IsNotNull}}"/>
```
