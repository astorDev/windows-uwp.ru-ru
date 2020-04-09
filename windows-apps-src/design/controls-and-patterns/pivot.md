---
Description: Элемент управления Pivot позволяет прокручивать касанием небольшой набор разделов содержимого.
title: Сводка
template: detail.hbs
ms.date: 06/19/2018
ms.topic: article
keywords: windows 10, uwp
pm-contact: yulikl
design-contact: kimsea
dev-contact: llongley
doc-status: Published
ms.localizationpriority: medium
ms.openlocfilehash: 6d55c24dbf84e16e0bb4dedc9eaf2b89982e2993
ms.sourcegitcommit: af4050f69168c15b0afaaa8eea66a5ee38b88fed
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/21/2020
ms.locfileid: "80081622"
---
# <a name="pivot"></a>Сводка

Элемент управления [Pivot](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Pivot) позволяет прокручивать касанием небольшой набор разделов содержимого.

![Фокус по умолчанию подчеркивает выбранный заголовок](images/pivot_focus_selectedHeader.png)

**Получение библиотеки пользовательского интерфейса Windows**

|  |  |
| - | - |
| ![Логотип WinUI](images/winui-logo-64x64.png) | Библиотека пользовательского интерфейса Windows 2.2 или более поздних версий содержит новый шаблон для этого элемента управления, который использует закругленные углы. Дополнительные сведения см. в разделе о [радиусе угла](/windows/uwp/design/style/rounded-corner). WinUI — это пакет NuGet, содержащий новые элементы управления и функции пользовательского интерфейса для приложений UWP. Дополнительные сведения, включая инструкции по установке, см. в описании [библиотеки пользовательского интерфейса Windows](https://docs.microsoft.com/uwp/toolkits/winui/). |

> **API платформы**: [класс Pivot](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Pivot), [класс NavigationView](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.NavigationView).

## <a name="examples"></a>Примеры

<table>
<th align="left">XAML Controls Gallery<th>
<tr>
<td><img src="images/xaml-controls-gallery-app-icon-sm.png" alt="XAML controls gallery"></img></td>
<td>
    <p>Если у вас установлено приложение <strong style="font-weight: semi-bold">галереи элементов управления XAML</strong>, щелкните здесь, чтобы <a href="xamlcontrolsgallery:/item/Pivot">открыть приложение и увидеть элемент управления Pivot в действии</a>.</p>
    <ul>
    <li><a href="https://www.microsoft.com/store/productId/9MSVH128X2ZT">Получить приложение XAML Controls Gallery (Microsoft Store)</a></li>
    <li><a href="https://github.com/Microsoft/Xaml-Controls-Gallery">Получить исходный код (GitHub)</a></li>
    </ul>
</td>
</tr>
</table>

Элемент управления Pivot, так же, как [NavigationView](navigationview.md), подчеркивает выбранный элемент.

![Фокус по умолчанию подчеркивает выбранный заголовок](images/pivot_focus_selectedHeader.png)

## <a name="is-this-the-right-control"></a>Выбор правильного элемента управления

Для создания общих шаблонов навигации и вкладок верхнего уровня мы рекомендуем использовать класс [NavigationView](navigationview.md), который автоматически адаптируется под различные размеры экрана и предоставляет более широкие возможности настройки.

Тем не менее если для навигации требуется прокручивание касанием, мы рекомендуем использовать Pivot.

Другие основные различия между элементами управления NavigationView и Pivot — поведение переполнения по умолчанию и API навигации:

- В элементе Pivot при переполнении элементы образуют карусель, а в NavigationView для них используется раскрывающееся меню, чтобы пользователи могли видеть все элементы.
- Pivot обрабатывает навигацию между разделами содержимого, а NavigationView позволяет лучше контролировать поведение навигации.

## <a name="use-navigationview-instead-of-pivot"></a>Использование NavigationView вместо Pivot

Если в пользовательском интерфейсе приложения используется элемент управления Pivot, позднее его можно преобразовать в NavigationView с помощью приведенного ниже кода.

Этот код XAML создает элемент управления NavigationView с тремя разделами содержимого, как показано в примере в разделе [о создании элемента управления Pivot](#create-a-pivot-control).

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

NavigationView предоставляет больше контроля над настройкой навигации и требует соответствующего кода программной части. В дополнение к указанному выше коду XAML используйте следующий код программной части:

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

Этот код имитирует встроенную навигацию элемента управления Pivot, за исключением прокручивания касанием разделов содержимого. Тем не менее, как вы видите, можно также настроить несколько аспектов, включая анимированный переход, параметры навигации и функции стека.

## <a name="create-a-pivot-control"></a>Создание элемента управления "Cводка"

Этот код создает базовый элемент управления Pivot с тремя разделами содержимого.

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

Pivot представляет собой элемент [ItemsControl](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.ItemsControl), поэтому может содержать коллекцию элементов любого типа. Любой элемент, который добавляется в элемент управления Pivot и явно не является элементом [PivotItem](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.PivotItem), неявно упаковывается в элемент PivotItem. Так как элемент Pivot часто используется для перемещения между страницами содержимого, коллекцию [Items](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.itemscontrol.items) можно заполнить непосредственно элементами пользовательского интерфейса XAML. Или же можно указать в свойстве [ItemsSource](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.itemscontrol.itemssource) источник данных. Элементы, связанные в ItemsSource, могут быть любого типа, но если они не являются явно элементами PivotItems, необходимо определить параметры [ItemTemplate](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.itemscontrol.itemtemplate) и [HeaderTemplate](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.pivot.headertemplate), чтобы указать способ отображения элементов.

Чтобы получить или настроить активный элемент Pivot, можно использовать свойство [SelectedItem](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.pivot.selecteditem). Чтобы получить или указать индекс активного элемента, используйте свойство [SelectedIndex](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.pivot.selectedindex).

### <a name="pivot-headers"></a>Заголовки Pivot

Чтобы добавить другие элементы управления в заголовок Pivot, можно использовать свойства [LeftHeader](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.pivot.leftheader) и [RightHeader](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.pivot.rightheader).

Например, можно добавить [CommandBar](https://docs.microsoft.com/windows/uwp/design/controls-and-patterns/app-bars) в свойство RightHeader элемента Pivot.

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
- Элементы сводки в цикле карусели сменяются от последнего к первому разделу сводки.

> **Примечание.** Заголовки элементов Pivot не должны прокручиваться в виде карусели на устройствах [с большим экраном](../devices/designing-for-tv.md). Присвойте свойству [IsHeaderItemsCarouselEnabled](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Pivot.IsHeaderItemsCarouselEnabled) значение **false**, если приложение выполняется на консоли Xbox.

## <a name="recommendations"></a>Рекомендации

- Не используйте более пяти заголовков в режиме карусели, так как это может запутать пользователя.

## <a name="get-the-sample-code"></a>Получение примера кода

- [Пример из коллекции элементов управления XAML](https://github.com/Microsoft/Xaml-Controls-Gallery) — ознакомьтесь со всеми элементами управления XAML в интерактивном режиме.

## <a name="related-topics"></a>Связанные темы

- [Класс Pivot](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Pivot)
- [Основы проектирования навигации](../basics/navigation-basics.md)