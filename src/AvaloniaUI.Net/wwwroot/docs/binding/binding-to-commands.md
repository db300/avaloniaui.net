---
Title: Binding to Commands
Order: 35
---
Controls that carry out an action, such as [`Button`](/api/Avalonia.Controls/Button/D29AE9A9) have
a `Command` property which can be bound to an 
[`ICommand`](https://docs.microsoft.com/en-gb/dotnet/api/system.windows.input.icommand?view=netstandard-2.0).
When the control is activated (e.g. when a button is clicked) the `ICommand.Execute` method will
be called.

执行动作的控件，例如[`Button`](/api/Avalonia.Controls/Button/D29AE9A9)具有`Command`属性可以绑定到`ICommand`。当控件被激活时（例如，单击按钮时），`ICommand.Execute`方法将被调用。

A good implementation of `ICommand` can be found in ReactiveUI's 
[`ReactiveCommand`](https://reactiveui.net/docs/handbook/commands/). If you've created your application
using the [Avalonia MVVM Application](/docs/quickstart/create-new-project) template then this will
be available by default. See the [ReactiveUI](https://reactiveui.net/docs/handbook/commands/)
documentation for more information.

在ReactiveUI的[`ReactiveCommand`](https://reactiveui.net/docs/handbook/commands/)中可以找到`ICommand`的良好实现。使用[Avalonia MVVM Application](/docs/quickstart/create-new-project)模板创建的应用程序，默认情况下都可用。有关更多信息，请参见[ReactiveUI](https://reactiveui.net/docs/handbook/commands/)文档。

An example:

一个例子：

```csharp
namespace Example
{
    public class MainWindowViewModel : ViewModelBase
    {
        public MainWindowViewModel()
        {
            DoTheThing = ReactiveCommand.Create(RunTheThing);
        }

        public ReactiveCommand<Unit, Unit> DoTheThing { get; }

        void RunTheThing()
        {
            // Code for executing the command here.
        }
    }
}
```

```xml
<Window xmlns="https://github.com/avaloniaui">
    <Button Command="{Binding DoTheThing}">Do the thing!</Button>
</Window>
```

## CommandParameter

You can also pass a parameter to the command using the `CommandParameter` property:

也可以使用`CommandParameter`属性将参数传递给命令：

```csharp
namespace Example
{
    public class MainWindowViewModel : ViewModelBase
    {
        public MainWindowViewModel()
        {
            DoTheThing = ReactiveCommand.Create<string>(RunTheThing);
        }

        public ReactiveCommand<string, Unit> DoTheThing { get; }

        void RunTheThing(string parameter)
        {
            // Code for executing the command here.
        }
    }
}
```

```xml
<Window xmlns="https://github.com/avaloniaui">
    <Button Command="{Binding DoTheThing}" CommandParameter="Hello World">Do the thing!</Button>
</Window>
```

Note that no type conversion is carried out on `CommandParameter`, so if you need you type
parameter to be something other than `string` you must supply an object of that type in XAML.
For example to pass an `int` parameter you could use:

请注意，`CommandParameter`不会进行类型转换，因此，如果需要键入非`string`类型的参数，则必须在XAML中提供该类型的对象。例如，要传递`int`参数，可以使用：

```xml
<Window xmlns="https://github.com/avaloniaui"
        xmlns:sys="clr-namespace:System;assembly=mscorlib">
    <Button Command="{Binding DoTheThing}">
        <Button.CommandParameter>
            <sys:Int32>42</sys:Int32>
        </Button.CommandParameter>
        Do the thing!
    </Button>
</Window>
```

Like any other property, `CommandParameter` can also be bound.

像其他任何属性一样，`CommandParameter`也可以被绑定。

## Binding To Methods

### ICommand.Execute

Sometimes you just want to call a method when a button is clicked without the ceremony of creating
a command. You can do that too!

有时，只想在单击按钮时调用一个方法，而不声明命令，也是可以的！

```csharp
namespace Example
{
    public class MainWindowViewModel : ViewModelBase
    {
        public void RunTheThing(string parameter)
        {
            // Code for executing the command here.
        }
    }
}
```

```xml
<Window xmlns="https://github.com/avaloniaui">
  <Button Command="{Binding RunTheThing}" CommandParameter="Hello World">Do the thing!</Button>
</Window>
```

### ICommand.CanExecute

If you need to have execution dependent on CommandParameter or your ViewModel property, you can define a named method formed by the prefix "Can" and the name of your execution method; the method will accept an object parameter which is the CommandParameter and return a Boolean which determines if the method is executable.

如果需要依赖CommandParameter或ViewModel属性执行命令，可以定义一个由前缀“Can”和执行方法名称组成命名的方法。该方法将接受一个对象参数（即CommandParameter），并返回一个布尔值，该布尔值决定该方法是否可执行。

```csharp
namespace Example
{
    public class MainWindowViewModel : ViewModelBase
    {
        public void RunTheThing(string parameter)
        {
            // Code for executing the command here.
        }

        bool CanRunTheThing(/* CommandParameter */object parameter)
        {
            return parameter != null;
        }
    }
}
```

#### Trigger ICommand.CanExecute

if you want trigger CanExecute from your ViewModel, you have to decorate it with one or more DependsOn attributes, where propertyName is the name of the property that will activate the CanExceute method when it changes value.

如果要从ViewModel触发CanExecute，则必须使用一个或多个DependsOn属性对其进行修饰，其中propertyName是将在更改值时激活CanExceute方法的属性的名称。

```csharp
namespace Example
{
    public class MainWindowViewModel : ViewModelBase
    {
        bool _isTheThingEnabled = true;

        bool IsTheThingEnabled
        {
            get
            {
               return  _isTheThingEnabled;
            }
            set
            {
                if(value == _isTheThingEnabled)
                   return;
                _isTheThingEnabled = value;
                PropertyChanged?
                    .Invoke(this, new PropertyChangedEventArgs(nameof(IsTheThingEnabled)));
            }
        }

        public void RunTheThing(string parameter)
        {
            // Code for executing the command here.
        }

        [DependsOn(nameof(IsTheThingEnabled))]
        bool CanRunTheThing(/* CommandParameter */object parameter)
        {
            return IsTheThingEnabled && parameter != null;
        }
    }
}
```
