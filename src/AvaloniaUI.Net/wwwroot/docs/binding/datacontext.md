---
Title: The DataContext
Order: 0
---

The [`Control.DataContext`](/api/Avalonia.Controls/Control/D29AE9A9) property
describes where controls will look by default for values when binding. The data context will usually
be set for top-level controls such as [`Window`](http://avaloniaui.net/api/Avalonia.Controls/Window)
and child controls will inherit this data context.

[`Control.DataContext`](/api/Avalonia.Controls/Control/D29AE9A9)属性描述控件在绑定时默认情况下将在何处查找值。通常为顶级控件设置data context，例如[`Window`](http://avaloniaui.net/api/Avalonia.Controls/Window)，然后子控件将继承该data context。

When using the MVVM pattern, the data context will usually be an instance of a view model.

使用MVVM模式时，data context通常是view model的实例。

If you created your application with the [Avalonia MVVM Application](/docs/quickstart/create-new-project) template
then you will see something like this in your `Program.cs` file:

如果使用[Avalonia MVVM Application](/docs/quickstart/create-new-project)模板创建了应用程序，则在`Program.cs`文件中将看到以下内容：

```csharp
private static void AppMain(Application app, string[] args)
{
    var window = new MainWindow
    {
        DataContext = new MainWindowViewModel(),
    };

    app.Run(window);
}
```

This means that when the `MainWindow` is created, a new instance of `MainWindowViewModel` will be
created and assigned to the window's `DataContext` property. From here all bindings will by default
bind to properties on this object:

这意味着当`MainWindow`被创建时，将创建`MainWindowViewModel`的一个新实例，并将其分配给window的`DataContext`属性。从这开始，所有绑定将默认绑定到该对象的属性：

```xml
<Window>
    <Button Content="{Binding ButtonCaption}"/>
</Window>
```

Will bind the `Button`'s `Content` to `Window.DataContext.ButtonCaption`.

将绑定`Button`的`Content`到`Window.DataContext.ButtonCaption`。

## Binding DataContext

When binding `DataContext`, the `DataContext` of the parent control is used as the source of the binding:

绑定`DataContext`时，父控件的`DataContext`用作绑定源：

```xml
<Window>
    <!-- Will bind `DataContext` to `Window.DataContext.Content -->
    <StackPanel DataContext="{Binding Content}"/>
</Window>
```

Controls that display content based on a [data template](/docs/templates/datatemplate) will automatically
set the `DataContext` for the controls in the template. For example `ContentControl`:

基于[data template](/docs/templates/datatemplate)显示内容的控件将自动为模板中的控件设置`DataContext`。例如`ContentControl`：

```xml
<Window>
    <ContentControl DataContext="{Binding Content}">
        <ContentControl.ContentTemplate>
            <DataTemplate>
                <!-- Will bind `Text` to `Window.DataContext.Content.Header -->
                <TextBlock Text="{Binding Header}"/>
            </DataTemplate>
        </ContentControl.ContentTemplate>
    </ContentControl>
</Window>
```
