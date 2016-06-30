---
author: jwmsft
title: "Атрибут xDeferLoadStrategy"
description: "xDeferLoadStrategy задерживает создание элемента и его дочерних объектов, сокращая тем самым время запуска и немного увеличивая объем использования памяти. Каждый задействованный элемент прибавляет около 600 байт к объему использования памяти."
ms.assetid: E763898E-13FF-4412-B502-B54DBFE2D4E4
translationtype: Human Translation
ms.sourcegitcommit: 98b9bca2528c041d2fdfc6a0adead321737932b4
ms.openlocfilehash: b989a31439444f06dacb86adb186f853d1637f6c

---

# Атрибут x:DeferLoadStrategy

\[ Обновлено для приложений UWP в Windows 10. Статьи о Windows 8.x см. в [архиве](http://go.microsoft.com/fwlink/p/?linkid=619132) \]

**x:DeferLoadStrategy="Lazy"** задерживает создание элемента и его дочерних объектов, сокращая тем самым время запуска и немного увеличивая объем использования памяти. Каждый задействованный элемент прибавляет около 600 байт к объему использования памяти. Чем большее дерево элементов вы отложите, тем больше времени вы сэкономите при запуске, однако за счет увеличения потребления памяти. Поэтому можно злоупотребить этим атрибутом в степени, в которой уменьшается ваша производительность.

## Использование атрибутов XAML

``` syntax
<object x:DeferLoadStrategy="Lazy" .../>
```

## Комментарии

Предусмотрены следующие ограничения по использованию **x:DeferLoadStrategy**:

-   Необходимо определить [x:Имя](x-name-attribute.md) , поскольку должна быть возможность найти этот элемент после.
-   Только [**UIElement**](https://msdn.microsoft.com/library/windows/apps/br208911) можно пометить как отложенный, за исключением типов, полученных из [**FlyoutBase**](https://msdn.microsoft.com/library/windows/apps/dn279249).
-   Корневые элементы нельзя откладывать в [**Page**](https://msdn.microsoft.com/en-us/library/windows/apps/windows.ui.xaml.controls.page), [**UserControls**](https://msdn.microsoft.com/en-us/library/windows/apps/windows.ui.xaml.controls.usercontrol) и [**DataTemplate**](https://msdn.microsoft.com/library/windows/apps/br242348).
-   Элементы в [**ResourceDictionary**](https://msdn.microsoft.com/library/windows/apps/br208794) нельзя откладывать.
-   Функция не поддерживает свободные страницы XAML, загружаемые с помощью [**XamlReader.Load**](https://msdn.microsoft.com/library/windows/apps/br228048).
-   Перемещение родительского элемента приведет к удалению всех элементов, которые не были реализованы.

Предусмотрено несколько различных способов реализации отложенных элементов:

-   Путем вызова [**FindName**](https://msdn.microsoft.com/library/windows/apps/br208715) с именем, определенным в элементе.
-   Путем вызова [**GetTemplateChild**](https://msdn.microsoft.com/library/windows/apps/br209416) с именем, определенным в элементе.
-   В [**VisualState**](https://msdn.microsoft.com/library/windows/apps/br209007) используйте анимацию [**Setter**](https://msdn.microsoft.com/library/windows/apps/br208817) или **Storyboard**, которая использует отложенный элемент.
-   Используйте отложенный элемент в любой анимации **Storyboard**.
-   Применяйте привязку, использующую отложенный элемент.
-   ПРИМЕЧАНИЕ. Если началось создание экземпляра, он создается в потоке пользовательского интерфейса, и это может вызвать перебои в работе пользовательского интерфейса, если одновременно будет создано слишком много экземпляров.

Когда отложенный элемент создается с помощью одного из описанных выше методов, происходит несколько событий.

-   Вызывается событие [**Loaded**](https://msdn.microsoft.com/library/windows/apps/br208723) для элемента.
-   Анализируются все привязки элемента.
-   Если в приложении предусмотрено получение уведомления об изменениях свойств, содержащих отложенные элементы, создается уведомление.

Вы можете вложить отложенные элементы, но они должны быть реализованы из крайнего элемента.  При попытке реализовать дочерний элемент перед реализацией родительского, будет создано исключение.

Обычно рекомендуется откладывать операции, которые не отображаются в первом кадре.  Для того чтобы найти кандидатов для откладывания, лучше всего искать элементы, создаваемые со свернутым [**Visibility**](https://msdn.microsoft.com/library/windows/apps/br208992).  Также элементы для откладывания можно найти во второстепенном пользовательском интерфейсе (пользовательский интерфейс, который активируется в результате взаимодействия с пользователем).  

С осторожностью откладывайте элементы в сценарии [**ListView**](https://msdn.microsoft.com/library/windows/apps/br242878): это уменьшает время запуска, но может также сократить производительность сдвига в зависимости от того, что создается в данный момент.  Если вам необходимо увеличить производительность сдвига, см. документацию о [расширении разметки {x:Bind}](x-bind-markup-extension.md) и [атрибуте x:Phase](x-phase-attribute.md).

Если [атрибут x:Phase](x-phase-attribute.md) используется в сочетании с **x:DeferLoadStrategy**, когда реализуется элемент или дерево элементов, привязки будет применены до текущего этапа, включая его. Этап, определенный для **x:Phase**, не повлияет и не изменит отсрочку элемента. Когда элемент списка используется повторно в процессе сдвига, реализованные элементы будут вести себя так же, как другие активные элементы, и скомпилированные привязки (привязки **{x:Bind}**) будут обрабатываться с использованием тех же правил, включая фазирование.

Общая рекомендация заключается в том, что следует измерять приложения до и после выполнения операций, чтобы убедиться в наличии требуемой производительности.

## Пример:

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
    this.FindName("DeferredGrid"); // This will realize the deferred grid
}
```




<!--HONumber=Jun16_HO4-->


