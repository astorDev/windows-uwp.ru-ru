---
title: Атрибут xLoad
description: Атрибут xLoad позволяет динамически создавать и уничтожать элемент и его дочерние элементы, сокращая время запуска и использование памяти.
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: d85051aabdb7631c5bdb84e08d6d10a0f70d6ede
ms.sourcegitcommit: ac7f3422f8d83618f9b6b5615a37f8e5c115b3c4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/29/2019
ms.locfileid: "66372297"
---
# <a name="xload-attribute"></a>Атрибут x:Load

Атрибут **x:Load** может использоваться для оптимизации запуска, создания визуального дерева и использования памяти в вашем приложении XAML. Использование атрибута **x:Load** имеет визуальный эффект, схожий с атрибутом **Visibility**, за исключением того, что когда элемент не загружен, память освобождается, и внутри нее используется небольшой заполнитель для обозначения его места в визуальном дереве.

Чтобы загрузить или выгрузить элемент пользовательского интерфейса с атрибутом x:Load, можно использовать код или выражение [x: Bind](x-bind-markup-extension.md). Это полезно для уменьшения влияния на производительность элементов, которые отображаются редко или при определенных условиях. При использовании x:Load в контейнере, таком как Grid или StackPanel, контейнер и все его дочерние элементы загружаются или выгружаются в виде группы.

Пояснение по поводу заполнителя: запись отложенных элементов с помощью платформы XAML прибавляет около 600 байт к объему использования памяти для каждого элемента с атрибутом x:Load. Поэтому можно злоупотребить этим атрибутом в степени, в которой реально уменьшается ваша производительность. Мы рекомендуем использовать его только в элементах, которые нужно скрыть. Если x:Load используется в контейнере, то ресурсы затрачиваются только на элемент с атрибутом x:Load.

> [!IMPORTANT]
> Атрибут x: Load доступна, начиная с Windows 10 версии 1703 (Creator Update). Для использования атрибута x:Load минимальная версия, указанная в вашем проекте Visual Studio, должна равняться *Windows 10 Creators Update (10.0, сборка 15063)* .

## <a name="xaml-attribute-usage"></a>Использование атрибутов XAML

``` syntax
<object x:Load="True" .../>
<object x:Load="False" .../>
<object x:Load="{x:Bind Path.to.a.boolean, Mode=OneWay}" .../>
```

## <a name="loading-elements"></a>Загрузка элементов

Предусмотрено несколько различных способов загрузки элементов:

- Используйте выражение [x:Bind](x-bind-markup-extension.md), чтобы указать состояние загрузки. Выражение должно возвращать значение **true** для загрузки и **false** — для выгрузки элемента.
- Вызовите [**FindName**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.frameworkelement.findname) с именем, которое вы определили в элементе.
- Вызовите [**GetTemplateChild**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.control.gettemplatechild) с именем, которое вы определили в элементе.
- В [**VisualState**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.VisualState) используйте анимацию [**Setter**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Setter) или **Storyboard**, которая использует элемент x:Load.
- Используйте выгруженный элемент в любой анимации **Storyboard**.

> ПРИМЕЧАНИЕ. После начала создания экземпляра элемента, он создается в потоке пользовательского интерфейса, поэтому это может привести к пользовательского интерфейса и задержкам, если слишком много будет создано за один раз.

Когда отложенный элемент создается с помощью одного из описанных ранее способов, происходит несколько событий:

- Инициируется событие [**Loaded**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.frameworkelement.loaded) для элемента.
- Задается поле для x:Name.
- Вычисляются все привязки x: Bind для элемента.
- Если вы зарегистрировались для получения уведомлений об изменении свойств, содержащих отложенные элементы, создается уведомление.

## <a name="unloading-elements"></a>Выгрузка элементов

Для выгрузки элемента:

- Используйте выражение x:Bind, чтобы указать состояние загрузки. Выражение должно возвращать значение **true** для загрузки и **false** — для выгрузки элемента.
- В классе Page или UserControl вызовите метод **UnloadObject** и передайте в ссылке на объект.
- Вызовите метод **Windows.UI.Xaml.Markup.XamlMarkupHelper.UnloadObject** и передайте в ссылке на объект.

Когда объект выгружен, в дереве он замещается на заполнитель. Экземпляр объекта остается в памяти до тех пор, пока не будут освобождены все ссылки. API метода UnloadObject в классах Page и UserControl предназначен для освобождения ссылок, хранимых в codegen для x:Name и x:Bind. Если в коде приложения хранятся дополнительные ссылки, их также необходимо освободить.

Когда элемент выгружен, отбрасывается связанное с ним состояние "all", поэтому, если x:Load используется в качестве оптимизированной версии Visibility, убедитесь, что состояние "all" применяется через привязки или повторно применяет с помощью кода при возникновении события Loaded.

## <a name="restrictions"></a>Ограничения

Предусмотрены следующие ограничения по использованию **x:Load**:

- Необходимо определить [x: Name](x-name-attribute.md) для элемента, поскольку должен быть способ поиска элемента в более поздней версии.
- Атрибут x:Load может использоваться только для типов, наследованных от классов [**UIElement**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.UIElement) или [**FlyoutBase**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Primitives.FlyoutBase).
- Атрибут x:Load не может использоваться для корневых элементов в классах [**Page**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.page), [**UserControl**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.usercontrol) или [**DataTemplate**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.DataTemplate).
- Атрибут x:Load не может использоваться для элементов в классе [**ResourceDictionary**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.ResourceDictionary).
- Атрибут x:Load не может использоваться на свободной странице XAML, загруженной с помощью метода [**XamlReader.Load**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.markup.xamlreader.load).
- Перемещение родительского элемента приведет к удалению всех элементов, которые не были загружены.

## <a name="remarks"></a>Примечания

Вы можете использовать атрибут x:Load для вложенных элементов, но они должны быть реализованы из крайнего элемента.  При попытке реализовать дочерний элемент перед реализацией родительского, будет инициализировано исключение.

Обычно мы рекомендуем откладывать элементы, которые не отображаются в первом кадре. Для того чтобы найти кандидатов для откладывания, лучше всего искать элементы, создаваемые со свернутым [**Visibility**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement.visibility). Кроме того, элементы для откладывания можно найти в пользовательском интерфейсе, который активируется в результате взаимодействия с пользователем.

С осторожностью откладывайте элементы в классе [**ListView**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.ListView): это уменьшает время запуска, но может также сократить производительность сдвига в зависимости от того, что создается в данный момент. Если вам необходимо увеличить производительность сдвига, см. документацию о [расширении разметки {x:Bind}](x-bind-markup-extension.md) и [атрибуте x:Phase](x-phase-attribute.md).

Если [атрибут x:Phase](x-phase-attribute.md) используется в сочетании с **x:Load**, то, когда реализуется элемент или дерево элементов, привязки применяются до текущего этапа, включая его. Этап, определенный для **x:Phase**, не влияет и не изменяет состояние загрузки элемента. Когда элемент списка используется повторно в процессе сдвига, реализованные элементы будут вести себя так же, как другие активные элементы, и скомпилированные привязки (привязки **{x:Bind}** ) будут обрабатываться с использованием тех же правил, включая фазирование.

Общая рекомендация заключается в том, что следует измерять производительность приложения до и после выполнения операций, чтобы убедиться в наличии требуемой производительности.

## <a name="example"></a>Пример

```xml
<StackPanel>
    <Grid x:Name="DeferredGrid" x:Load="False">
        <Grid.RowDefinitions>
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
        </Grid.RowDefinitions>
        <Grid.ColumnDefinitions>
            <ColumnDefinition Width="Auto" />
            <ColumnDefinition Width="Auto" />
        </Grid.ColumnDefinitions>

        <Rectangle Height="100" Width="100" Fill="Orange" Margin="0,0,4,4"/>
        <Rectangle Height="100" Width="100" Fill="Green" Grid.Column="1" Margin="4,0,0,4"/>
        <Rectangle Height="100" Width="100" Fill="Blue" Grid.Row="1" Margin="0,4,4,0"/>
        <Rectangle Height="100" Width="100" Fill="Gold" Grid.Row="1" Grid.Column="1" Margin="4,4,0,0"
                   x:Name="one" x:Load="{x:Bind (x:Boolean)CheckBox1.IsChecked, Mode=OneWay}"/>
        <Rectangle Height="100" Width="100" Fill="Silver" Grid.Row="1" Grid.Column="1" Margin="4,4,0,0"
                   x:Name="two" x:Load="{x:Bind Not(CheckBox1.IsChecked), Mode=OneWay}"/>
    </Grid>

    <Button Content="Load elements" Click="LoadElements_Click"/>
    <Button Content="Unload elements" Click="UnloadElements_Click"/>
    <CheckBox x:Name="CheckBox1" Content="Swap Elements" />
</StackPanel>
```

```csharp
// This is used by the bindings between the rectangles and check box.
private bool Not(bool? value) { return !(value==true); }

private void LoadElements_Click(object sender, RoutedEventArgs e)
{
    // This will load the deferred grid, but not the nested
    // rectangles that have x:Load attributes.
    this.FindName("DeferredGrid"); 
}

private void UnloadElements_Click(object sender, RoutedEventArgs e)
{
     // This will unload the grid and all its child elements.
     this.UnloadObject(DeferredGrid);
}
```

