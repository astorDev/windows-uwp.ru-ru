---
author: QuinnRadich
Description: The Pivot control enables touch-swiping between a small set of content sections.
title: Сводка
template: detail.hbs
ms.author: quradic
ms.date: 06/19/2018
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, uwp
pm-contact: yulikl
design-contact: kimsea
dev-contact: llongley
doc-status: Published
ms.localizationpriority: medium
ms.openlocfilehash: 5bb6ed36c772e5ae80a3cb801b4b6b36bb1ab18c
ms.sourcegitcommit: 82c3fc0b06ad490c3456ad18180a6b23ecd9c1a7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/24/2018
ms.locfileid: "5480890"
---
# <a name="pivot"></a>Сводка

Элемент управления ["Сводка"](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Pivot) позволяет сенсорный ввод прокрутка между небольшой набор разделы содержимого.

> **Важные API -интерфейсы**: [класс Pivot](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Pivot), [класс NavigationView](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.NavigationView)

## <a name="examples"></a>Примеры.

<table>
<th align="left">Галерея элементов управления XAML<th>
<tr>
<td><img src="images/xaml-controls-gallery-sm.png" alt="XAML controls gallery"></img></td>
<td>
    <p>Если у вас установлено приложение <strong style="font-weight: semi-bold">Галереи элементов управления XAML</strong> , щелкните здесь, чтобы <a href="xamlcontrolsgallery:/item/Pivot">открыть приложение и увидеть Pivot в действии</a>.</p>
    <ul>
    <li><a href="https://www.microsoft.com/store/productId/9MSVH128X2ZT">Скачать приложение галереи элементов управления XAML (Microsoft Store)</a></li>
    <li><a href="https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/XamlUIBasics">Получить исходный код (GitHub)</a></li>
    </ul>
</td>
</tr>
</table>

Элемент управления "Сводка", так же, как [NavigationView](navigationview.md)подчеркивает выбранный элемент.

![Фокус по умолчанию подчеркивает выбранный заголовок](images/pivot_focus_selectedHeader.png)

## <a name="is-this-the-right-control"></a>Выбор подходящего элемента управления

Для достижения распространенных верхней навигационной и шаблоны вкладки, мы рекомендуем использовать [NavigationView](navigationview.md), который автоматически адаптируется к различным размерам экрана и позволяет более настройки.

Тем не менее если навигации требуется прокрутка сенсорного ввода, мы рекомендуем использовать "Сводка".

Основные различия между элементами управления NavigationView и "Сводка" — это поведение по умолчанию переполнения и навигации API:

- Сводки переполнения обойм, которые элементов, когда NavigationView использует раскрывающееся меню переполнения, чтобы пользователи могли видеть все элементы.
- "Сводка" обрабатывает навигацию между разделы содержимого, когда NavigationView позволяет больше контроля над поведение навигации с помощью.

## <a name="use-navigationview-instead-of-pivot"></a>Использовать NavigationView вместо "Сводка"

Если пользовательский Интерфейс приложения используется элемент управления "Сводка", затем можно преобразовать "Сводка" для NavigationView следующим кодом.

Этот код XAML создает NavigationView с 3 разделами содержимого, как и в примере Pivot в [Создать элемент управления "Сводка"](#create-a-pivot-control).

```xaml
<NavigationView x:Name="rootNavigationView" Header="Category Title"
 ItemInvoked="NavView_ItemInvoked">
    <NavigationView.MenuItems>
        <NavigationViewItem Content="Section 1" x:Name="Section1Content" />
        <NavigationViewItem Content="Section 2" x:Name="Section2Content" />
        <NavigationViewItem Content="Section 3" x:Name="Section3Content" />
    </NavigationView.MenuItems>
</NavigationView>

<Page x:Class="AppName.Section1Page">
    <TextBlock Text="Content of section 1."/>
</Page>

<Page x:Class="AppName.Section2Page">
    <TextBlock Text="Content of section 2."/>
</Page>

<Page x:Class="AppName.Section3Page">
    <TextBlock Text="Content of section 3."/>
</Page>
```

NavigationView обеспечивает больший контроль над настройки навигации и требует соответствующего кода программной части. Для описания выше XAML, используйте следующий код программной части:

```csharp
private void NavView_ItemInvoked(NavigationView sender, NavigationViewItemInvokedEventArgs args)
{
    FrameNavigationOptions navOptions = new FrameNavigationOptions();
    navOptions.TransitionInfoOverride = new SlideNavigationTransitionInfo() {
         SlideNavigationTransitionDirection=args.RecommendedNavigationTransitionInfo
    };

    navOptions.IsNavigationStackEnabled = False;

    switch (item.Name)
    {
        case "Section1Content":
            ContentFrame.NavigateToType(typeof(Section1Page), null, navOptions);
            break;

        case "Section2Content":
            ContentFrame.NavigateToType(typeof(Section2Page), null, navOptions);
            break;

        case "Section3Content":
            ContentFrame.NavigateToType(typeof(Section3Page), null, navOptions);
            break;
    }  
}
```

Этот код имитирует взаимодействия при навигации встроенный элемент управления Pivot, минус прокрутка сенсорного взаимодействия между разделами содержимого. Как видно, можно также настроить несколько точек, включая анимированный переход, параметров навигации и возможности стека.

## <a name="create-a-pivot-control"></a>Создание элемента управления "Cводка"

Этот код создает базовый элемент управления "Сводка" с 3 разделами содержимого.

```xaml
<Pivot x:Name="rootPivot" Title="Category Title">
    <PivotItem Header="Section 1">
        <!--Pivot content goes here-->
        <TextBlock Text="Content of section 1."/>
    </PivotItem>
    <PivotItem Header="Section 2">
        <!--Pivot content goes here-->
        <TextBlock Text="Content of section 2."/>
    </PivotItem>
    <PivotItem Header="Section 3">
        <!--Pivot content goes here-->
        <TextBlock Text="Content of section 3."/>
    </PivotItem>
</Pivot>
```

### <a name="pivot-items"></a>Элементы Pivot

Pivot представляет собой [ItemsControl](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.itemscontrol.aspx), поэтому может содержать коллекцию элементов любого типа. Любой элемент, который добавляется в элемент управления Pivot и явно не является [PivotItem](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.pivotitem.aspx), явно упаковывается в элемент PivotItem. Поскольку "Сводка" часто используется для перемещения между страницами содержимого, коллекцию [Элементы](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.itemscontrol.items.aspx) можно заполнить непосредственно элементами пользовательского интерфейса XAML. Или же можно задать свойство [ItemsSource](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.itemscontrol.itemssource.aspx) для источника данных. Элементы, связанные в ItemsSource, могут быть любого типа, но если они явно являются PivotItems, необходимо определить параметры [ItemTemplate](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.itemscontrol.itemtemplate.aspx) и [HeaderTemplate](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.pivot.headertemplate.aspx), чтобы указать способ отображения элементов.

Чтобы получить или настроить активный элемент Pivot, можно использовать свойство [SelectedItem](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.pivot.selecteditem.aspx). Чтобы получить или указать индекс активного элемента, используйте свойство [SelectedIndex](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.pivot.selectedindex.aspx).

### <a name="pivot-headers"></a>Заголовки Pivot

Чтобы добавить другие элементы управления в заголовок Pivot, можно использовать свойства [LeftHeader](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.pivot.leftheader.aspx) и [RightHeader](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.pivot.rightheader.aspx).

Например, можно добавить [CommandBar](https://docs.microsoft.com/en-us/windows/uwp/controls-and-patterns/app-bars) в RightHeader для Pivot.

```xaml
<Pivot>
    <Pivot.RightHeader>
        <CommandBar>
                <AppBarButton Icon="Add"/>
                <AppBarSeparator/>
                <AppBarButton Icon="Edit"/>
                <AppBarButton Icon="Delete"/>
                <AppBarSeparator/>
                <AppBarButton Icon="Save"/>
        </CommandBar>
    </Pivot.RightHeader>
</Pivot>
```

### <a name="pivot-interaction"></a>Взаимодействие Pivot

Элемент управления поддерживает следующие сенсорные жесты:

- Касание элемента сводки приводит к переходу к контенту раздела этого заголовка.
- Прокрутка влево или вправо по элементу сводки приводит к переходу к смежному разделу.
- Прокрутка влево или вправо по содержимому раздела приводит к переходу к смежному разделу.

Элемент управления доступен в двух режимах:

**Стационарный**

- Сводки неподвижны, если все заголовки сводок вписываются в доступное пространство.
- Касание метки сводки приводит к переходу на соответствующую страницу, но сама сводка не перемещается. Активная сводка выделена.

**Карусель**

- Карусель сводок, когда все заголовки не помещаются в доступном пространстве.
- Касание метки сводки приводит к переходу на соответствующую страницу, а метка активной сводки перемещается на первую позицию.
- Элементы Pivot в цикле карусели сменяются от последнего к первому разделу сводки.

> **Примечание.** Заголовки Pivot не должны прокручиваться на устройствах [с большим экраном](../devices/designing-for-tv.md). Присвойте свойству [IsHeaderItemsCarouselEnabled](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Pivot.IsHeaderItemsCarouselEnabled) значение **false**, если приложение выполняется на консоли Xbox.

## <a name="recommendations"></a>Рекомендации

- Не используйте более пяти заголовков в режиме карусели, так как это может запутать пользователя.

## <a name="get-the-sample-code"></a>Получить пример кода

- [Образец галереи элементов управления XAML](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/XamlUIBasics) — ознакомьтесь со всеми элементами управления XAML в интерактивном формате.

## <a name="related-topics"></a>Смежные разделы

- [Класс Pivot](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Pivot)
- [Основы проектирования навигации](../basics/navigation-basics.md)