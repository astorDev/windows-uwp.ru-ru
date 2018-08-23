---
author: serenaz
Description: The Pivot control enables touch-swiping between a small set of content sections.
title: Pivot
template: detail.hbs
ms.author: sezhen
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
ms.openlocfilehash: e8f0fbbfacc3fa4edb602f7505ea1e88f211a81a
ms.sourcegitcommit: 9c79fdab9039ff592edf7984732d300a14e81d92
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/23/2018
ms.locfileid: "2814856"
---
# <a name="pivot"></a>Pivot

Элемент управления [Pivot](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Pivot) позволяет сенсорного ввода считывания между небольшой набор разделов содержимого.

> **Важные API -интерфейсы**: [класс Pivot](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Pivot), [класс NavigationView](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.NavigationView)

## <a name="examples"></a>Примеры.

<table>
<th align="left">Галерея элементов управления XAML<th>
<tr>
<td><img src="images/xaml-controls-gallery-sm.png" alt="XAML controls gallery"></img></td>
<td>
    <p>При наличии установленного приложения <strong style="font-weight: semi-bold">Коллекция элементов управления XAML</strong> , щелкните здесь, чтобы <a href="xamlcontrolsgallery:/item/Pivot">открыть приложение и посмотрите, элемент управления Pivot в действии</a>.</p>
    <ul>
    <li><a href="https://www.microsoft.com/store/productId/9MSVH128X2ZT">Скачать приложение галереи элементов управления XAML (Microsoft Store)</a></li>
    <li><a href="https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/XamlUIBasics">Получить исходный код (GitHub)</a></li>
    </ul>
</td>
</tr>
</table>

Элемент управления Pivot, так же, как [NavigationView](navigationview.md), подчеркивание выбранного элемента.

![Фокус по умолчанию подчеркивает выбранный заголовок](images/pivot_focus_selectedHeader.png)

## <a name="is-this-the-right-control"></a>Выбор подходящего элемента управления

Для достижения распространенных верхней панели навигации и шаблоны вкладок, рекомендуется использовать [NavigationView](navigationview.md), которая автоматически преобразует для различных размеров экрана и позволяет настраивать более высокой версии.

Тем не менее если ваше навигации требуется для считывания сенсорного ввода, рекомендуется использовать Pivot.

Основные различия между элементами управления NavigationView и Pivot являются переполнения поведение по умолчанию и навигации API:

- Сводить обойм переполнения, элементов, а NavigationView в раскрывающемся меню переполнения, чтобы пользователи могут видеть все элементы.
- PIVOT обрабатывает переходов между разделами контента, хотя NavigationView позволяет лучше контролировать поведение навигации.

## <a name="use-navigationview-instead-of-pivot"></a>Использование NavigationView вместо Pivot

Если ваше приложение пользовательский Интерфейс использует управления Pivot, затем можно преобразовать Pivot NavigationView следующим кодом.

Этот код XAML создает NavigationView 3 разделы содержимого, как пример Pivot в разделе [Создание элемента управления pivot](#create-a-pivot-control).

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

NavigationView позволяет лучше контролировать настройки навигации и требует соответствующего кода. Поясняющая выше XAML, используйте следующие кода:

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

Этот код напоминающего интерфейс управления Pivot встроенных навигации, минус считывания сенсорного ввода взаимодействия между разделами контента. Как вы видите, можно также настроить несколько точек, включая анимационной перехода, параметры навигации и возможности стека.

## <a name="create-a-pivot-control"></a>Создание элемента управления "Cводка"

Этот код создает базовый элемент управления Pivot с 3 разделы содержимого.

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