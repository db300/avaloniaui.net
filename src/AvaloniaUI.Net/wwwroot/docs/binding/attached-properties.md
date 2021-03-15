---
Title: Creating and binding Attached Properties
Order: 90
---

When you need more or let's say foreign properties on avalonia elements, then attached properties are
the right thing to use. They can also be used to create so called behaviors to generally modify the hosted 
gui components. This can be utilized to bind a command to an event for instance.

如果需要更多的资源或让我们说说关于Avalonia元素的外来属性，那么附加属性是正确的选择。它们也可以用于创建所谓的行为，通常用来修改托管的gui组件。例如，这可用于将命令绑定到事件。

Here we present an example of how to use a command in an MVVM compatible way and bind it to an event.

这里提供一个示例，说明如何以MVVM兼容的方式使用命令并将其绑定到事件。

It may not be the ideal solution as there are projects such as [Avalonia Behaviors](https://github.com/wieslawsoltes/AvaloniaBehaviors)
where this is properly done. But it illustrates the following two learnings:

这可能不是理想的解决方案，因为有些项目，例如[Avalonia Behaviors](https://github.com/wieslawsoltes/AvaloniaBehaviors)），已正确完成。但是，它说明了以下两个方面的经验：

* How to create attached properties in AvaloniaUI
* 如何在AvaloniaUI中创建附加属性
* How to use them in a MVVM way.
* 如何以MVVM方式使用它们。

First we have to create our attached property. The method `AvaloniaProperty.RegisterAttached` is used for that purpose.
Note that by convention the **public static** CLR-property for the attached property is named *XxxxProperty*.
Also note that by convention the name (the parameter) of the attaced property is *Xxxx* without the *Property*.
And finally note that by convention one must provide two **public static** methods called *SetXxxx(element,value)* and *GetXxxx(element)*.

首先，我们必须创建我们的附加属性。方法`AvaloniaProperty.RegisterAttached`用于该目的。请注意，按照惯例，附加属性的**公共静态**CLR属性命名为*XxxxProperty*。还要注意，按照惯例，附加属性的名称（参数）为*Xxxx*而不包含*Property*。最后要注意的是，按照惯例，必须提供两个**公共静态**方法，分别称为*SetXxxx(element, value)*和*GetXxxx(element)*。

This call ensures that the property has a type, an owner type and one where it may be used.

此调用可确保该属性具有一种类型，一个所有者类型以及一个可以在其中使用的属性。

The verify method can be used to clean up a value that is being set. Either by returning the corrected value or discard the process by returning `AvaloniaProperty.UnsetValue`. Or one can perform special tasks with the element that the property is hosted by. The getter and seter methods should always just set the value an never to anything beyond. In fact they will usually
never be called as the Binding system will recognize the convention and set the proerties directly where they are stored. 

验证方法可用于清除正在设置的值。通过返回校正后的值，或者通过返回`AvaloniaProperty.UnsetValue`来放弃该过程。或者可以使用承载该属性的元素来执行特殊任务。getter和seter方法应始终仅将值设置为永不对任何其他值进行设置。实际上，它们通常不会被调用，因为绑定系统会识别约定并直接在存储它们的位置设置属性。

In this example file we create two attached properties that interact with each other: A *Command* property and a *CommandParameter* that is used by when invoking the command.

在此示例文件中，创建两个相互交互的附加属性：一个*Command*属性和一个调用命令时使用的*CommandParameter*。

```cs
/// <summary>
/// Container class for attached properties. Must inherit from <see cref="AvaloniaObject"/>.
/// </summary>
public class DoubleTappedBehav : AvaloniaObject
{
    /// <summary>
    /// Identifies the <seealso cref="CommandProperty"/> avalonia attached property.
    /// </summary>
    /// <value>Provide an <see cref="ICommand"/> derived object or binding.</value>
    public static readonly AttachedProperty<ICommand> CommandProperty = AvaloniaProperty.RegisterAttached<DoubleTappedBehav, Interactive, ICommand>(
        "Command", default(ICommand), false, BindingMode.OneTime, ValidateCommand);

    /// <summary>
    /// Identifies the <seealso cref="CommandParameterProperty"/> avalonia attached property.
    /// Use this as the parameter for the <see cref="CommandProperty"/>.
    /// </summary>
    /// <value>Any value of type <see cref="object"/>.</value>
    public static readonly AttachedProperty<object> CommandParameterProperty = AvaloniaProperty.RegisterAttached<DoubleTappedBehav, Interactive, object>(
        "CommandParameter", default(object), false, BindingMode.OneWay, null);


    /// <summary>
    /// The coerce value function. Returns the final (probably corrected result).
    /// can be used to perform actions during assign.
    /// </summary>
    private static ICommand ValidateCommand(Interactive element, ICommand commandValue)
    {
        Interactive interactElem = element;
        if (interactElem != null)
        {
            if (commandValue != null)
            {
                // Add non-null value
                interactElem.AddHandler(InputElement.DoubleTappedEvent, Handler);
            }
            else
            {
                // remove prev value
                interactElem.RemoveHandler(InputElement.DoubleTappedEvent, Handler);
            }
        }

        return commandValue;

        // local handler fcn
        void Handler(object s, RoutedEventArgs e)
        {
            // This is how we get the parameter off of the gui element.
            object commandParameter = interactElem.GetValue(CommandParameterProperty);
            if (commandValue?.CanExecute(commandParameter) == true)
            {
                commandValue.Execute(commandParameter);
            }
        }
    }


    /// <summary>
    /// Accessor for Attached property <see cref="CommandProperty"/>.
    /// </summary>
    public static void SetCommand(AvaloniaObject element, ICommand commandValue)
    {
        element.SetValue(CommandProperty, commandValue);
    }

    /// <summary>
    /// Accessor for Attached property <see cref="CommandProperty"/>.
    /// </summary>
    public static ICommand GetCommand(AvaloniaObject element)
    {
        return element.GetValue(CommandProperty);
    }

    /// <summary>
    /// Accessor for Attached property <see cref="CommandParameterProperty"/>.
    /// </summary>
    public static void SetCommandParameter(AvaloniaObject element, object parameter)
    {
        element.SetValue(CommandParameterProperty, parameter);
    }

    /// <summary>
    /// Accessor for Attached property <see cref="CommandParameterProperty"/>.
    /// </summary>
    public static object GetCommandParameter(AvaloniaObject element)
    {
        return element.GetValue(CommandParameterProperty);
    }
}

```

In the verify method we utilize the routed event system to attach a new handler. Note that the handler should be detached, again.
The value of the property is requested by the normal program mechanisms using `GetValue()` method.

在验证方法中，我们利用路由事件系统来附加新的处理程序。请注意，应再次分离处理程序。属性值是由使用`GetValue()`方法的常规程序机制请求。

This example UI shows how to use the attached property. After making the namespace known to the xaml compiler it can be used
by qualifying it with a dot. Then bindings can be used. 

此示例UI展示了如何使用附加属性。使xaml编译器知道名称空间后，可以通过使用点来使用。然后可以使用绑定。

```xml
<UserControl xmlns="https://github.com/avaloniaui"
             xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
             xmlns:loc="clr-namespace:MyApp.Behaviors"
             x:Class="MyApp.Views.TestView">
    <ListBox Items="{Binding Accounts}"
             SelectedIndex="{Binding SelectedAccountIdx, Mode=TwoWay}"
             loc:DoubleTappedBehav.Command="{Binding EditCommand}"
             loc:DoubleTappedBehav.CommandParameter="test77"
             >
      <ListBox.ItemTemplate>
        <DataTemplate>
          <TextBlock Text="{Binding }" />          
        </DataTemplate>
      </ListBox.ItemTemplate>
    </ListBox>
</UserControl>
```

Although the `CommandParameter` only uses a static value, it can be used with binding, too.
When used with this view model, the `EditCommandExecuted` will run once a double click happens.

尽管`CommandParameter`仅使用静态值，但也可以将其与绑定一起使用。与该视图模型一起使用时，`EditCommandExecuted`将在双击时运行一次。

```cs
public class TestViewModel : ReactiveObject
{
    public ObservableCollection<Profile> Accounts { get; } = new ObservableCollection<Profile>();

    public ReactiveCommand<object, Unit> EditCommand { get; set; }

    public TestViewModel()
    {
        EditCommand = ReactiveCommand.CreateFromTask<object, Unit>(EditProfileExecuted);
    }

    private async Task<Unit> EditCommandExecuted(object p)
    {
        // p contains "test77"

        return Unit.Default;
    }
}
```
