---
Title: Binding in a Control Template
Order: 50
---

When you're creating a control template and you want to bind to the templated parent you can use:

创建控件模板并绑定到模板化父对象时，可以使用：

```xml
<TextBlock Name="tb" Text="{TemplateBinding Caption}"/>

<!-- Which is the same as -->
<TextBlock Name="tb" Text="{Binding Caption, RelativeSource={RelativeSource TemplatedParent}}"/>
```

Although the two syntaxes shown here are equivalent in this case, there is a difference.
`TemplateBinding` accepts only a single property rather than a property path, so if you want
to bind using a property path you must use the second syntax:

尽管此处显示的两种语法在这种情况下是等效的，但还是有所不同。`TemplateBinding`仅接受单个属性，而不接受属性路径，因此，如果要使用属性路径进行绑定，则必须使用第二种语法：

```xml
<!-- This WON'T work as TemplateBinding only accepts single properties -->
<TextBlock Name="tb" Text="{TemplateBinding Caption.Length}"/>

<!-- Instead this syntax must be used in this case -->
<TextBlock Name="tb" Text="{Binding Caption.Length, RelativeSource={RelativeSource TemplatedParent}}"/>
```
