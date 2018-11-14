---
author: jwmsft
title: атрибут xLoad
description: xLoad позволяет динамическое создание и уничтожение элемента и его дочерних объектов, сокращая использования памяти и время запуска. 
ms.author: jimwalk
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: d9659d183c020c579aa0a21fe179a69c1d9997c5
ms.sourcegitcommit: f2c9a050a9137a473f28b613968d5782866142c6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/10/2018
ms.locfileid: "6272955"
---
# <a name="xload-attribute"></a>Атрибут x:Load

Можно использовать **x: Load** оптимизировать запуска, создания визуального дерева и использования памяти приложения XAML. С помощью **x: Load** действует аналогично visual **видимость**, за исключением того, что когда элемент не загружается, память освобождается и внутри небольшой заполнитель используется для индикации своего места в визуальном дереве.

Элемент пользовательского интерфейса, с помощью x: Load атрибутами может быть загружены и выгружена через код или с помощью выражения [x: Bind](x-bind-markup-extension.md) . Это полезно для уменьшения влияния на производительность элементов, которые отображаются редко или при определенных условиях. При использовании x: Load в контейнере, например сетки или StackPanel, контейнер и все его дочерние элементы загружаются или выгружен в качестве группы.

Отслеживание отложенных элементов платформой XAML добавляет около 600 байт к объему использования памяти для каждого элемента, атрибутами с x: Load, с учетом заполнитель. Поэтому можно злоупотребить этим атрибутом в степени, в которой фактически уменьшается ваша производительность. Мы рекомендуем только использовать его для элементов, которые должны быть скрыты. Если вы используете x: Load в контейнере, излишней выплачивается только для элемента с помощью атрибута x: Load.

> [!IMPORTANT]
> Атрибут x: Load доступен, начиная с Windows 10 версии 1703 (Creators Update). Для использования атрибута x:Load минимальная версия, указанная в вашем проекте Visual Studio, должна равняться *Windows 10 Creators Update (10.0, сборка 15063)*.

## <a name="xaml-attribute-usage"></a>Использование атрибутов XAML

``` syntax
<object x:Load="True" .../>
<object x:Load="False" .../>
<object x:Load="{x:Bind Path.to.a.boolean, Mode=OneWay}" .../>
```

## <a name="loading-elements"></a>Загрузку элементов

Существует несколько различных способов для загрузки элементов:

- Используйте выражения [x: Bind](x-bind-markup-extension.md) для определения состояния загрузки. Выражение должно возвращать **значение true** для загрузки и **false** для выгрузки элемента.
- Вызовите [**FindName**](https://msdn.microsoft.com/library/windows/apps/br208715) с именем, которое вы определили в элементе.
- Вызовите [**GetTemplateChild**](https://msdn.microsoft.com/library/windows/apps/br209416) с именем, которое вы определили в элементе.
- В [**VisualState**](https://msdn.microsoft.com/library/windows/apps/br209007)используйте анимацию [**Setter**](https://msdn.microsoft.com/library/windows/apps/br208817) или **раскадровку** , использующую x: Load элемент.
- Целевой элемент выгружен в любой **раскадровки**.

> ПРИМЕЧАНИЕ. Если началось создание экземпляра, он создается в потоке пользовательского интерфейса, и это может вызвать перебои в работе пользовательского интерфейса, если одновременно будет создано слишком много экземпляров.

Когда отложенный элемент создается с помощью одного из описанных ранее способов, происходит несколько событий:

- Инициируется событие [**Loaded**](https://msdn.microsoft.com/library/windows/apps/br208723) для элемента.
- Задайте в поле для x: Name.
- Анализируются все привязки x: Bind для элемента.
- Если вы зарегистрировались для получения уведомлений об изменении свойств, содержащих отложенные элементы, создается уведомление.

## <a name="unloading-elements"></a>Выгрузка элементов

Для выгрузки элемент:

- Используйте выражения x: Bind для определения состояния загрузки. Выражение должно возвращать **значение true** для загрузки и **false** для выгрузки элемента.
- В страницы или пользовательского элемента управления вызовите метод **UnloadObject** и передайте ссылка на объект
- Вызовите метод **Windows.UI.Xaml.Markup.XamlMarkupHelper.UnloadObject** и передайте ссылка на объект

Когда выгружается объект, он будет заменено в дереве с заполнитель. Экземпляр объекта останется в памяти, пока не будут выпущены все ссылки. API UnloadObject на страницы или пользовательского элемента управления предназначен для освобождения ссылок, занятые создание кода для x: Name и x: Bind. Если содержат дополнительные ссылки в коде приложения им также потребуется выходить.

Когда элемент выгружается, все состояния, связанный с элементом, будут удалены, поэтому при использовании x: Load как оптимизированной версии видимость, затем обеспечить все состояния применяется через привязки или применяется кодом повторно, когда возникает событие.

## <a name="restrictions"></a>Ограничения

Ограничения по использованию **x: Load** являются:

- Необходимо определить [x: Name](x-name-attribute.md)для элемента, поскольку должна быть возможность найти этот элемент позднее.
- X: Load можно использовать только для типов, которые являются производными от [**UIElement**](https://msdn.microsoft.com/library/windows/apps/br208911) или [**FlyoutBase**](https://msdn.microsoft.com/library/windows/apps/dn279249).
- X: Load нельзя использовать в корневых элементах, [**страницы**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.page), [**UserControl**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.usercontrol)или [**DataTemplate**](https://msdn.microsoft.com/library/windows/apps/br242348).
- Невозможно использовать x: Load элементов в [**ResourceDictionary**](https://msdn.microsoft.com/library/windows/apps/br208794).
- Нельзя использовать x: Load на свободные страницы XAML, загружаемые с помощью [**XamlReader.Load**](https://msdn.microsoft.com/library/windows/apps/br228048).
- Перемещение родительского элемента приведет к удалению все элементы, которые не были загружены.

## <a name="remarks"></a>Комментарии

Можно использовать x: Load вложенных элементов, но они должны быть реализованы из крайнего элемента. При попытке реализовать дочерний элемент перед реализацией родительского, будет инициализировано исключение.

Обычно мы рекомендуем откладывать элементы, которые не отображаются в первом кадре.Для того чтобы найти кандидатов для откладывания, лучше всего искать элементы, создаваемые со свернутым свойством [**Visibility**](https://msdn.microsoft.com/library/windows/apps/br208992). Кроме того, элементы для откладывания можно найти в пользовательском интерфейсе, который активируется в результате взаимодействия с пользователем.

С осторожностью откладывайте элементы в классе [**ListView**](https://msdn.microsoft.com/library/windows/apps/br242878): это уменьшает время запуска, но может также сократить производительность сдвига в зависимости от того, что создается в данный момент. Если вам необходимо увеличить производительность сдвига, см. документацию о [расширении разметки {x:Bind}](x-bind-markup-extension.md) и [атрибуте x:Phase](x-phase-attribute.md).

Если [атрибут x: Phase](x-phase-attribute.md) используется в сочетании с **x: Load** затем, когда реализуется элемент или дерево элементов, привязки применяются до текущего этапа, включая. Этап, определенный для **x: Phase** влияют на или состояния загрузки элемента управления. Когда элемент списка используется повторно в процессе сдвига, реализованные элементы будут вести себя так же, как другие активные элементы, и скомпилированные привязки (привязки **{x: Bind}** ) будут обрабатываться с использованием тех же правил, включая фазирование.

Общая рекомендация заключается в том, что следует измерять производительность приложения до и после выполнения операций, чтобы убедиться в наличии требуемой производительности.

## <a name="example"></a>Пример:

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

