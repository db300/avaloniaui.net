---
Title: Change Notifications-变更通知
Order: 5
---

## Property Changes

In order for Avalonia to know when a property on a view model has changed, the view model must
implement change notifications. The easiest way to do this is to use `ReactiveUI` and make your
view model class inherit from `ReactiveObject`.

为了使Avalonia知道view model的属性发生更改，该view model必须实现变更通知。最简单的方法是使用`ReactiveUI`并使view model类继承`ReactiveObject`。

You then add a setter for each property which calls `RaiseAndSetIfChanged`:

然后为每个调用`RaiseAndSetIfChanged`的属性添加一个setter ：

```csharp
using ReactiveUI;

public class MyViewModel : ReactiveObject
{
    private string caption;

    public string Caption
    {
        get => caption;
        set => this.RaiseAndSetIfChanged(ref caption, value);
    }
}
```

For more information see the [ReactiveUI documentation](https://reactiveui.net/docs/handbook/view-models/).

有关更多信息，请参见[ReactiveUI文档](https://reactiveui.net/docs/handbook/view-models/)。

If you don't want a dependency on ReactiveUI, you can implement 
[`INotifyPropertyChanged` manually](https://docs.microsoft.com/en-us/dotnet/api/system.componentmodel.inotifypropertychanged).

如果不想依赖ReactiveUI，则可以手动实现[`INotifyPropertyChanged`](https://docs.microsoft.com/en-us/dotnet/api/system.componentmodel.inotifypropertychanged)。

## Collection Changes

Collections also need to implement change notifications. There are a number of collection which
do this for you out of the box:

集合也需要实现变更通知。开箱即用的集合有很多：

- [ObservableCollection](https://docs.microsoft.com/en-us/dotnet/api/system.collections.objectmodel.observablecollection-1)
is available in the .NET base class library
- .NET基类库中提供了[ObservableCollection](https://docs.microsoft.com/en-us/dotnet/api/system.collections.objectmodel.observablecollection-1)
- [DynamicData](https://github.com/reactiveui/DynamicData) for more advanced scenarios
- [DynamicData](https://github.com/reactiveui/DynamicData)适用于更高级的方案
- [ReactiveList](https://reactiveui.net/docs/handbook/obsolete/collections/reactive-list) in
ReactiveUI (deprecated in favor of DynamicData)
- ReactiveUI中的[ReactiveList](https://reactiveui.net/docs/handbook/obsolete/collections/reactive-list)（不推荐，建议使用DynamicData）
- `AvaloniaList` is shipped with Avalonia, but note that its API may change in future so for the
moment it's recommended to not use it at the application level
- `AvaloniaList`随Avalonia一起提供，但请注意，其API将来可能会更改，因此建议暂时不要在应用程序级别使用它

If you want to implement collection change notifications yourself you can implement
[`INotifyCollectionChanged`](https://docs.microsoft.com/en-us/dotnet/api/system.collections.specialized.inotifycollectionchanged)

如果想自己实现集合变更通知，则可以实现[`INotifyCollectionChanged`](https://docs.microsoft.com/en-us/dotnet/api/system.collections.specialized.inotifycollectionchanged)
