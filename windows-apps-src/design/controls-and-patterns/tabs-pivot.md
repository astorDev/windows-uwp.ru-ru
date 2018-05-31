---
author: serenaz
Description: Tabs and pivots enable users to navigate between frequently accessed content.
title: Вкладки и сводки
ms.assetid: 556BC70D-CF5D-4295-A655-D58163CC1824
label: Tabs and pivots
template: detail.hbs
ms.author: sezhen
ms.date: 05/19/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, uwp
pm-contact: yulikl
design-contact: kimsea
dev-contact: llongley
doc-status: Published
ms.localizationpriority: medium
ms.openlocfilehash: 09e88fd070f7e7517e55d6f57563dd7608696770
ms.sourcegitcommit: 2470c6596d67e1f5ca26b44fad56a2f89773e9cc
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/22/2018
ms.locfileid: "1675481"
---
# <a name="pivot-and-tabs"></a>Вкладки и сводки



Элементы управления "Сводка" и вкладки применяются для перехода к часто используемым категориям контента. Сводки позволяют переходить между несколькими областями контента и применяют текстовые заголовки для обозначения различных разделов контента.

> **Важные API-интерфейсы**: [класс Pivot](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Pivot)

![Пример элемента управления Pivot](images/pivot_Hero_main.png)

Вкладки — это визуальный вариант сводки, в котором сочетание значков и текста или только значки используются для подчеркивания контента раздела. Вкладки создаются с помощью элемента управления [Pivot](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.pivot.aspx). В [примере Pivot](http://go.microsoft.com/fwlink/p/?LinkId=619903) показано, как настроить элемент управления "Сводка" с использованием шаблона вкладок.

![Пример набора вкладок](images/tabs.png) 

## <a name="the-pivot-control"></a>Элемент управления Pivot

При создании приложения со сводкой необходимо учитывать несколько ключевых аспектов.

- **Метки заголовка.**  У заголовков может быть значок с текстом, только текст или только значок.
- **Выравнивание заголовка.**  Заголовки могут быть выравнены по левому краю или по центру.
- **Навигация верхнего уровня или подуровня.**  Сводки можно использовать для любого уровня навигации. При желании [представление навигации](navigationview.md) может служить верхним уровнем, а сводки — дополнительным.
- **Поддержка сенсорных жестов.**  Для устройств, поддерживающих сенсорные жесты, можно использовать один из двух наборов взаимодействия для навигации между категориями контента.
    1. Касание заголовка вкладки или сводки для перехода.
    2. Прокрутка влево или вправо по области содержимого для перехода к смежной категории.

## <a name="examples"></a>Примеры

<table>
<th align="left">Галерея элементов управления XAML<th>
<tr>
<td><img src="images/xaml-controls-gallery-sm.png" alt="XAML controls gallery"></img></td>
<td>
    <p>Если у вас установлено приложение <strong style="font-weight: semi-bold">галереи элементов управления XAML</strong>, щелкните здесь, чтобы <a href="xamlcontrolsgallery:/item/Pivot">открыть приложение и увидеть Pivot в действии</a>.</p>
    <ul>
    <li><a href="https://www.microsoft.com/store/productId/9MSVH128X2ZT">Скачать приложение галереи элементов управления XAML (Microsoft Store)</a></li>
    <li><a href="https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/XamlUIBasics">Получить исходный код (GitHub)</a></li>
    </ul>
</td>
</tr>
</table>

Элемент управления "Сводка" на телефоне.

![Пример сводки](images/pivot_example.png)

Шаблон вкладок в приложении "Будильник и часы".

![Пример шаблона вкладок в приложении "Будильник и часы"](images/tabs_alarms-and-clock.png)

## <a name="create-a-pivot-control"></a>Создание элемента управления "Cводка"

Элемент управления [Pivot](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Pivot) предоставляет базовые возможности, описанные в этом разделе.

Этот XAML создает базовый элемент управления сводкой с 3 разделами содержимого.

```xaml
<Pivot x:Name="rootPivot" Title="Pivot Title">
    <PivotItem Header="Pivot Item 1">
        <!--Pivot content goes here-->
        <TextBlock Text="Content of pivot item 1."/>
    </PivotItem>
    <PivotItem Header="Pivot Item 2">
        <!--Pivot content goes here-->
        <TextBlock Text="Content of pivot item 2."/>
    </PivotItem>
    <PivotItem Header="Pivot Item 3">
        <!--Pivot content goes here-->
        <TextBlock Text="Content of pivot item 3."/>
    </PivotItem>
</Pivot>
```

### <a name="pivot-items"></a>Элементы Pivot

Pivot представляет собой [ItemsControl](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.itemscontrol.aspx), поэтому может содержать коллекцию элементов любого типа. Любой элемент, который добавляется в элемент управления Pivot и явно не является [PivotItem](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.pivotitem.aspx), явно упаковывается в элемент PivotItem. Поскольку "Сводка" часто используется для перемещения между страницами содержимого, коллекцию [Элементы](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.itemscontrol.items.aspx) можно заполнить непосредственно элементами пользовательского интерфейса XAML. Или же можно задать свойство [ItemsSource](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.itemscontrol.itemssource.aspx) для источника данных. Элементы, связанные в ItemsSource, могут быть любого типа, но если они явно являются PivotItems, необходимо определить параметры [ItemTemplate](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.itemscontrol.itemtemplate.aspx) и [HeaderTemplate](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.pivot.headertemplate.aspx), чтобы указать способ отображения элементов.

Чтобы получить или настроить активный элемент Pivot, можно использовать свойство [SelectedItem](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.pivot.selecteditem.aspx). Чтобы получить или указать индекс активного элемента, используйте свойство [SelectedIndex](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.pivot.selectedindex.aspx).

### <a name="pivot-headers"></a>Заголовки Pivot

Чтобы добавить другие элементы управления в заголовок Pivot, можно использовать свойства [LeftHeader](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.pivot.leftheader.aspx) и [RightHeader](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.pivot.rightheader.aspx).

Например, можно добавить [CommandBar](https://docs.microsoft.com/en-us/windows/uwp/controls-and-patterns/app-bars) в RightHeader для Pivot.

![Пример панели команд в правом заголовке элемента управления Pivot](images/PivotHeader.png)

```xaml
<Pivot>
    <Pivot.RightHeader>
        <CommandBar OverflowButtonVisibility="Collapsed" Background="Transparent">
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

-   Касание элемента сводки приводит к переходу к контенту раздела этого заголовка.
-   Прокрутка влево или вправо по элементу сводки приводит к переходу к смежному разделу.
-   Прокрутка влево или вправо по содержимому раздела приводит к переходу к смежному разделу.

![Пример прокрутки влево контента раздела](images/pivot_w_hand.png)

Элемент управления доступен в двух режимах:

**Стационарный**

-   Сводки неподвижны, если все заголовки сводок вписываются в доступное пространство.
-   Касание метки сводки приводит к переходу на соответствующую страницу, но сама сводка не перемещается. Активная сводка выделена.

**Карусель**

-   Карусель сводок, когда все заголовки не помещаются в доступном пространстве.
-   Касание метки сводки приводит к переходу на соответствующую страницу, а метка активной сводки перемещается на первую позицию.
-   Элементы Pivot в цикле карусели сменяются от последнего к первому разделу сводки.

> **Примечание.** Заголовки Pivot не должны прокручиваться на устройствах [с большим экраном](../devices/designing-for-tv.md). Присвойте свойству [IsHeaderItemsCarouselEnabled](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Pivot.IsHeaderItemsCarouselEnabled) значение **false**, если приложение выполняется на консоли Xbox.

### <a name="pivot-focus"></a>Фокус сводки

По умолчанию фокус клавиатуры на заголовке сводки представлен подчеркиванием.

![Фокус по умолчанию подчеркивает выбранный заголовок](images/pivot_focus_selectedHeader.png)

Приложения с настроенной сводкой, которые применяют подчеркивание в визуальных элементах выбора заголовка, могут использовать новое свойство [HeaderFocusVisualPlacement](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.pivot.HeaderFocusVisualPlacement), чтобы изменить поведение по умолчанию. Если `HeaderFocusVisualPlacement="ItemHeaders"`, фокус будет вокруг всей панели заголовков.

![Параметр ItemsHeader рисует прямоугольник фокуса вокруг всех заголовков сводки](images/pivot_focus_headers.png)

## <a name="recommendations"></a>Рекомендации

- Определяйте выравнивание заголовков вкладок и сводок в зависимости от размера экрана. Для ширины экрана меньше 720 epx обычно лучше всего подходит выравнивание по центру, а выравнивание по левому краю рекомендуется в большинстве случаев для ширины экрана более 720 epx.
- Не используйте более пяти заголовков в режиме карусели, так как это может запутать пользователя.
- Используйте шаблон вкладок, только если элементы сводки имеют отдельные значки.
- Включайте текст в заголовки элементов сводки, чтобы помочь пользователям понять значение каждого раздела сводки. Значки могут быть понятны не всем пользователям.

## <a name="get-the-sample-code"></a>Получить пример кода

- В [примере Pivot](http://go.microsoft.com/fwlink/p/?LinkId=619903) показано, как настроить элемент управления "Сводка" с использованием шаблона вкладок.
- [Образец галереи элементов управления XAML](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/XamlUIBasics) — ознакомьтесь со всеми элементами управления XAML в интерактивном формате.

## <a name="related-topics"></a>Смежные разделы

- [Класс Pivot](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Pivot)
- [Основы проектирования навигации](../basics/navigation-basics.md)
