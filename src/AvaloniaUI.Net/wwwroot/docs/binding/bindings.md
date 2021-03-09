---
Title: Bindings-绑定
Order: 10
---

You bind in XAML using the `{Binding}` markup extension. By using bindings (assuming you've
implemented [change notifications](change-notifications)) any changes to the data context will
automatically be updated in the control.

在XAML中使用`{Binding}`标记扩展进行绑定。通过使用绑定（假设已经实现了[change notifications-变更通知](change-notifications)），data context的任何变更都将在控件中自动更新。

By default a binding binds to a property on the [`DataContext`](datacontext), e.g.:

默认情况下，binding绑定到[`DataContext`](datacontext)的属性，例如：

```xml
<!-- Binds to the TextBlock's DataContext.Name property -->
<TextBlock Text="{Binding Name}"/>

<!-- Which is the same as ('Path' is optional) -->
<TextBlock Text="{Binding Path=Name}"/>
```

An empty binding binds to DataContext itself

空binding绑定到DataContext本身

```xml
<!-- Binds to the TextBlock's DataContext property -->
<TextBlock Text="{Binding}"/>

<!-- Which is the same as -->
<TextBlock Text="{Binding .}"/>
```

We call the property on the control the binding _target_ and the property on the `DataContext` the
binding _source_.

控件的属性称为绑定 _目标_，`DataContext`的属性称为绑定 _源_。

## Binding Path

The binding path above can be a single property, or it can be a chain of properties. For example
if the object assigned to the `DataContext` has a `Student` property, and the value of this property
has a `Name`, you can bind to the student name using:

上面的绑定路径可以是单个属性，也可以是属性链。例如，如果分配给`DataContext`的对象具有`Student`属性，而该属性的值具有`Name`，则可以使用以下方式绑定到student name：

```xml
<TextBlock Text="{Binding Student.Name}"/>
```

You can also include array/list indexers in binding paths:

也可以在绑定路径中包括数组/列表索引器：

```xml
<TextBlock Text="{Binding Students[0].Name}"/>
```

## Binding Modes

You can change the behavior of a `{Binding}` by specifing a binding `Mode`:

可以通过指定绑定`Mode`来更改`{Binding}`的行为：

```
<TextBlock Text="{Binding Name, Mode=OneTime}">
```

The available binding modes are:

可用的绑定模式为：

| Mode | Description |
| ---- | ----------- |
| `OneWay` | Changes to the source are automatically propagated to the target |
| `TwoWay` | Changes to the source are automatically propagated to the target and vice-versa |
| `OneTime` | The value from the source is propagated at initialization to the target and subsequent changes are ignored |
| `OneWayToSource` | Changes to the target are propagated to the source |
| `Default` | The binding mode is based on the property |

| 模式 | 描述 |
| ---- | ----------- |
| `OneWay` | 对源的更改会自动传播到目标 |
| `TwoWay` | 对源的更改会自动传播到目标，反之亦然 |
| `OneTime` | 来自源的值在初始化时传播到目标，随后的更改被忽略 |
| `OneWayToSource` | 对目标的更改将传播到源 |
| `Default` | 绑定模式基于属性 |

The `Default` mode is assumed if one is not specified. This mode is generally `OneWay` for control
properties that do not change due to user input (e.g. `TextBlock.Text`) and `TwoWay` for control
properties that _do_ change due to user input (e.g. `TextBox.Text`).

如果没有指定则假定`Default`模式。该模式通常对于不会由于用户输入发生改变的控件属性是`OneWay`（例如`TextBlock.Text`），对于会由于用户输入发生改变的控制属性是`TwoWay`（例如`TextBox.Text`）。
