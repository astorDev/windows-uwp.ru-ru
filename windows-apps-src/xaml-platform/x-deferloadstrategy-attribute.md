---
title: Атрибут xDeferLoadStrategy
description: xDeferLoadStrategy задерживает создание элемента и его дочерних объектов, сокращая тем самым время запуска и немного увеличивая объем использования памяти. Каждый задействованный элемент прибавляет около 600 байт к объему использования памяти.
ms.assetid: E763898E-13FF-4412-B502-B54DBFE2D4E4
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: f445b186c42095bfdf6d10fa7960b78691ced792
ms.sourcegitcommit: ac7f3422f8d83618f9b6b5615a37f8e5c115b3c4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/29/2019
ms.locfileid: "66371095"
---
# <a name="xdeferloadstrategy-attribute"></a>Атрибут x:DeferLoadStrategy

> [!IMPORTANT]
> Начиная с Windows 10 версии 1703 (Creators Update), атрибут **x:DeferLoadStrategy** заменен на [**атрибут x:Load**](x-load-attribute.md). Использование `x:Load="False"` эквивалентно `x:DeferLoadStrategy="Lazy"`, но предоставляется возможность выгрузить пользовательский интерфейс при необходимости. Подробнее см. в разделе [Атрибут x:Load](x-load-attribute.md).

**x:DeferLoadStrategy="Lazy"** можно использовать для оптимизации производительности сценариев запуска и создания дерева приложения XAML. **x:DeferLoadStrategy="Lazy"** задерживает создание элемента и его дочерних объектов, сокращая время запуска и объем использования памяти за счет отсутствия необходимости в создании элементов. Это полезно для уменьшения влияния на производительность элементов, которые отображаются редко или при определенных условиях. Элемент будет реализован при обращении к нему из кода или VisualStateManager.

При этом запись отложенных элементов прибавляет около 600 байт к объему использования памяти для каждого элемента. Чем большее дерево элементов вы отложите, тем больше времени вы сэкономите при запуске, однако за счет увеличения потребления памяти. Поэтому можно злоупотребить этим атрибутом в степени, в которой уменьшается ваша производительность.

## <a name="xaml-attribute-usage"></a>Использование атрибутов XAML

``` syntax
<object x:DeferLoadStrategy="Lazy" .../>
```

## <a name="remarks"></a>Примечания

Предусмотрены следующие ограничения по использованию **x:DeferLoadStrategy**:

- Необходимо определить [x: Name](x-name-attribute.md) для элемента, поскольку должен быть способ поиска элемента в более поздней версии.
- Вы можете отложить только те типы, которые наследованы от классов [**UIElement**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.UIElement) или [**FlyoutBase**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Primitives.FlyoutBase).
- Вы не можете отложить корневые элементы в [**Page**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.page), [**UserControl**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.usercontrol) и [**DataTemplate**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.DataTemplate).
- Вы не можете отложить элементы в [**ResourceDictionary**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.ResourceDictionary).
- Вы не можете отложить свободные страницы, загружаемые с помощью [**XamlReader.Load**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.markup.xamlreader.load).
- Перемещение родительского элемента приведет к удалению всех элементов, которые не были реализованы.

Предусмотрено несколько различных способов реализации отложенных элементов:

- Вызовите [**FindName**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.frameworkelement.findname) с именем, которое вы определили в элементе.
- Вызовите [**GetTemplateChild**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.control.gettemplatechild) с именем, которое вы определили в элементе.
- В [**VisualState**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.VisualState) используйте анимацию [**Setter**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Setter) или **Storyboard**, которая использует отложенный элемент.
- Используйте отложенный элемент в любой анимации **Storyboard**.
- Используйте привязку, использующую отложенный элемент.

> ПРИМЕЧАНИЕ. После начала создания экземпляра элемента, он создается в потоке пользовательского интерфейса, поэтому это может привести к пользовательского интерфейса и задержкам, если слишком много будет создано за один раз.

Когда отложенный элемент создается с помощью одного из описанных ранее способов, происходит несколько событий:

- Инициируется событие [**Loaded**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.frameworkelement.loaded) для элемента.
- Анализируются все привязки элемента.
- Если вы зарегистрировались для получения уведомлений об изменении свойств, содержащих отложенные элементы, создается уведомление.

Вы можете вложить отложенные элементы, но они должны быть реализованы из крайнего элемента.  При попытке реализовать дочерний элемент перед реализацией родительского, будет инициализировано исключение.

Обычно мы рекомендуем откладывать элементы, которые не отображаются в первом кадре. Для того чтобы найти кандидатов для откладывания, лучше всего искать элементы, создаваемые со свернутым [**Visibility**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement.visibility). Кроме того, элементы для откладывания можно найти в пользовательском интерфейсе, который активируется в результате взаимодействия с пользователем.

С осторожностью откладывайте элементы в классе [**ListView**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.ListView): это уменьшает время запуска, но может также сократить производительность сдвига в зависимости от того, что создается в данный момент. Если вам необходимо увеличить производительность сдвига, см. документацию о [расширении разметки {x:Bind}](x-bind-markup-extension.md) и [атрибуте x:Phase](x-phase-attribute.md).

Если [атрибут x:Phase](x-phase-attribute.md) используется в сочетании с **x:DeferLoadStrategy**, то, когда реализуется элемент или дерево элементов, привязки применяются до текущего этапа, включая его. Этап, определенный для **x:Phase**, не влияет и не изменяет отсрочку элемента. Когда элемент списка используется повторно в процессе сдвига, реализованные элементы будут вести себя так же, как другие активные элементы, а скомпилированные привязки (привязки **{x:Bind}** ) будут обрабатываться с использованием тех же правил, включая фазирование.

Общая рекомендация заключается в том, что следует измерять производительность приложения до и после выполнения операций, чтобы убедиться в наличии требуемой производительности.

## <a name="example"></a>Пример

```xml
<Grid x:Name="DeferredGrid" x:DeferLoadStrategy="Lazy">
    <Grid.RowDefinitions>
        <RowDefinition Height="Auto" />
        <RowDefinition Height="Auto" />
    </Grid.RowDefinitions>
    <Grid.ColumnDefinitions>
        <ColumnDefinition Width="Auto" />
        <ColumnDefinition Width="Auto" />
    </Grid.ColumnDefinitions>

    <Rectangle Height="100" Width="100" Fill="#F65314" Margin="0,0,4,4" />
    <Rectangle Height="100" Width="100" Fill="#7CBB00" Grid.Column="1" Margin="4,0,0,4" />
    <Rectangle Height="100" Width="100" Fill="#00A1F1" Grid.Row="1" Margin="0,4,4,0" />
    <Rectangle Height="100" Width="100" Fill="#FFBB00" Grid.Row="1" Grid.Column="1" Margin="4,4,0,0" />
</Grid>
<Button x:Name="RealizeElements" Content="Realize Elements" Click="RealizeElements_Click"/>
```

```csharp
private void RealizeElements_Click(object sender, RoutedEventArgs e)
{
    // This will realize the deferred grid.
    this.FindName("DeferredGrid");
}
```
