---
author: jwmsft
title: атрибут xLoad
description: xLoad позволяет динамического создания и удаления элемента и его дочерних узлов, что снижает запуска времени и использования памяти.
ms.author: jimwalk
ms.date: 02/08/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 8f34ea43b981de65c81e9cce2b8a896c3577e595
ms.sourcegitcommit: 897a111e8fc5d38d483800288ad01c523e924ef4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/13/2018
ms.locfileid: "610787"
---
# <a name="xload-attribute"></a>Атрибут x:Load

**X: нагрузки** можно использовать для оптимизации запуска, визуального дерева создания и использования памяти приложения XAML. С помощью **X: нагрузки** имеет аналогичную визуальный эффект **видимости**, когда элемент не загружен, освобождается память и внутри небольшой заполнитель используется для отметить его место в дереве visual.

Элемент пользовательского интерфейса, присвоенным с x: нагрузки может быть загружена и выгрузки с помощью кода или с помощью выражения [X: привязки](x-bind-markup-extension.md) . Это полезно для уменьшения влияния на производительность элементов, которые отображаются редко или при определенных условиях. При использовании x: нагрузки в контейнере, например сетки или StackPanel контейнер и все его дочерние элементы загрузке или выгрузке как группа.

Отслеживание отложенные элементы в XAML Framework добавляет около 600 байт использование памяти для каждого элемента, присвоенным с x: нагрузки, с учетом заполнитель. Таким образом существует возможность используйте чем это необходимо этот атрибут, фактически снижает производительность. Рекомендуется использовать только его для элементов, которые должны быть скрыты. Если вы используете x: нагрузки в контейнере, нагрузка на оплачивается только для элемента с помощью атрибута x: нагрузки.

> [!IMPORTANT]
> Атрибут x: нагрузки доступна на 10 Windows, версия 1703 (создателей обновление). Для использования атрибута x:Load минимальная версия, указанная в вашем проекте Visual Studio, должна равняться *Windows 10 Creators Update (10.0, сборка 15063)*.

## <a name="xaml-attribute-usage"></a>Использование атрибутов XAML

``` syntax
<object x:Load="True" .../>
<object x:Load="False" .../>
<object x:Load="{x:Bind Path.to.a.boolean, Mode=OneWay}" .../>
```

## <a name="loading-elements"></a>Загрузка элементов

Существует несколько способов загружать элементы:

- Используйте выражение [X: привязки](x-bind-markup-extension.md) для указания о состоянии. Выражение должен возвращать **значение true** для загрузки и **значение false,** Чтобы выгрузить в элемент.
- Вызовите [**FindName**](https://msdn.microsoft.com/library/windows/apps/br208715) с именем, которое вы определили в элементе.
- Вызовите [**GetTemplateChild**](https://msdn.microsoft.com/library/windows/apps/br209416) с именем, которое вы определили в элементе.
- В [**VisualState**](https://msdn.microsoft.com/library/windows/apps/br209007)используйте [**установщик**](https://msdn.microsoft.com/library/windows/apps/br208817) или **раскадровки** анимации, предназначенное для элемента x: нагрузки.
- Целевой объект выгружен элемент в любой **раскадровки**.

> ПРИМЕЧАНИЕ. Если началось создание экземпляра, он создается в потоке пользовательского интерфейса, и это может вызвать перебои в работе пользовательского интерфейса, если одновременно будет создано слишком много экземпляров.

Когда отложенный элемент создается с помощью одного из описанных ранее способов, происходит несколько событий:

- Инициируется событие [**Loaded**](https://msdn.microsoft.com/library/windows/apps/br208723) для элемента.
- Поле для x: Name значение.
- Вычисляются все привязки x: привязка элемента.
- Если вы зарегистрировались для получения уведомлений об изменении свойств, содержащих отложенные элементы, создается уведомление.

## <a name="unloading-elements"></a>Выгрузка элементов

Чтобы выгрузить элемент:

- Используйте выражение x: привязки для указания о состоянии. Выражение должен возвращать **значение true** для загрузки и **значение false,** Чтобы выгрузить в элемент.
- На странице или пользовательских элементов управления вызовите **UnloadObject** и передать ссылку на объект
- Вызовите **Windows.UI.Xaml.Markup.XamlMarkupHelper.UnloadObject** и передать ссылку на объект

При выгрузке объекта, он будет заменен в дереве с заполнитель. Экземпляр объекта остается в памяти, пока все ссылки выпущенные. API UnloadObject на страницы и пользовательских элементов управления предназначен для освобождения ссылок, хранящихся в создание кода для x: Name и x: привязки. Если содержат дополнительные ссылки в код приложения, которые также требуется нужно освободить.

При выгрузке элемент все состояния, связанный с элементом будут отменены, таким образом при использовании x: нагрузки как оптимизированной версии видимости, затем убедитесь, все состояния применяется с помощью привязки, или повторно применить по коду при загруженных события.

## <a name="restrictions"></a>Ограничения

Ограничения для использования **X: нагрузки** являются:

- Необходимо определить класс [x:Name](x-name-attribute.md), поскольку должна быть возможность найти этот элемент позднее.
- X: нагрузки можно использовать только для типов, которые являются производными от [**элементов интерфейса пользователя**](https://msdn.microsoft.com/library/windows/apps/br208911) или [**FlyoutBase**](https://msdn.microsoft.com/library/windows/apps/dn279249).
- Нельзя использовать x: нагрузки на корневые элементы [**страниц**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.page), [**пользовательских элементов управления**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.usercontrol)или [**DataTemplate**](https://msdn.microsoft.com/library/windows/apps/br242348).
- Нельзя использовать x: нагрузки на элементы в [**ResourceDictionary**](https://msdn.microsoft.com/library/windows/apps/br208794).
- Нельзя использовать x: нагрузки на свободном XAML, загруженный с [**XamlReader.Load**](https://msdn.microsoft.com/library/windows/apps/br228048).
- Перемещение родительского элемента будут очищены все элементы, которые не были загружены.

## <a name="remarks"></a>Комментарии

Можно использовать x: нагрузки на вложенных элементов, однако они должны быть реализована от самого внешнего элемента в.  При попытке реализовать дочерний элемент перед реализацией родительского, будет инициализировано исключение.

Обычно мы рекомендуем откладывать элементы, которые не отображаются в первом кадре. Для того чтобы найти кандидатов для откладывания, лучше всего искать элементы, создаваемые со свернутым свойством [**Visibility**](https://msdn.microsoft.com/library/windows/apps/br208992). Кроме того, элементы для откладывания можно найти в пользовательском интерфейсе, который активируется в результате взаимодействия с пользователем.

С осторожностью откладывайте элементы в классе [**ListView**](https://msdn.microsoft.com/library/windows/apps/br242878): это уменьшает время запуска, но может также сократить производительность сдвига в зависимости от того, что создается в данный момент. Если вам необходимо увеличить производительность сдвига, см. документацию о [расширении разметки {x:Bind}](x-bind-markup-extension.md) и [атрибуте x:Phase](x-phase-attribute.md).

Если [атрибут x: этап](x-phase-attribute.md) используется в сочетании с **X: нагрузки** , при реализуется элемент или дерево элементов, привязок, применяются, включая текущий этап. Этап, указанный для **X: этап** влияют на или управления состоянием загрузки элемента. Когда элемента списка как часть удаленного панорамирования, реализованная элементов будет вести себя так же, как другим элементам среды active и скомпилированной привязки (привязок **{X: привязка}** ), обрабатываются в соответствии с правилами, включая синхронизацию.

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

