---
author: Jwmsft
Description: Вкладки и сводки позволяют пользователям переходить между часто используемым контентом.
title: Вкладки и сводки
ms.assetid: 556BC70D-CF5D-4295-A655-D58163CC1824
label: Tabs and pivots
template: detail.hbs
---
# Вкладки и элемент управления "сводка"

Элементы управления "сводка" и вкладки применяются для перехода к часто используемым категориям контента. Вкладки и сводки состоят из двух или нескольких панелей с контентом с соответствующими заголовками категорий. Заголовки остаются на экране, состояние выделения четко демонстрируется, поэтому пользователи всегда осведомлены, в какой категории они находятся.
![Примеры вкладок](images/HIGSecOne_Tabs.png)

Вкладки являются визуальным вариантом сводок и создаются с помощью элемента управления [**Pivot**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.pivot.aspx). [
              **Пример кода**
            ](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/XamlPivot), демонстрирующий настройку сводок, доступен на GitHub.

<span class="sidebar_heading" style="font-weight: bold;">Важные API</span>

-   [**Класс Pivot**](https://msdn.microsoft.com/library/windows/apps/dn608241)

## Шаблон вкладки/сводки

При создании приложения с шаблоном вкладок или сводок необходимо учитывать несколько ключевых аспектов.

- **Метки заголовка.**  У заголовков может быть значок с текстом или только текст.
- **Выравнивание заголовка.**  Заголовки могут быть выравнены по левому краю или по центру.
- **Навигация верхнего уровня или подуровня.**  Вкладки и сводки можно использовать для любого уровня навигации. При желании [панель навигации](nav-pane.md) может служить верхним уровнем, а вкладки и сводки — дополнительным.
- **Поддержка сенсорных жестов.**  Для устройств, поддерживающих сенсорные жесты, можно использовать один из двух наборов взаимодействия для навигации между категориями контента:
    1. Касание заголовка вкладки или сводки для перехода.
    2. Прокрутка влево или вправо по области содержимого для перехода к смежной категории.

## Примеры:

Элемент управления "сводка" по умолчанию в напоминаниях Кортаны.

![Пример элемента управления "сводка" в напоминаниях Кортаны](images/pivot_cortana-reminders.png)

Шаблон вкладок в приложении "Будильник и часы".

![Пример вкладок в приложении "Будильник и часы"](images/tabs_alarms-and-clock.png)

## Создание элемента управления сводкой

Элемент управления [**Сводка**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.pivot.aspx) предоставляет базовые возможности, описанные в этом разделе.

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

### Элементы Pivot

Pivot представляет собой [**ItemsControl**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.itemscontrol.aspx), поэтому может содержать коллекцию элементов любого типа. Любой элемент, который добавляется в элемент управления "Сводка" и явно не является [**PivotItem**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.pivotitem.aspx), явно упаковывается в элемент PivotItem. Поскольку "Сводка" часто используется для перемещения между страницами содержимого, коллекцию [**Элементы**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.itemscontrol.items.aspx) можно заполнить непосредственно элементами пользовательского интерфейса XAML. Или же можно задать свойство [**ItemsSource**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.itemscontrol.itemssource.aspx) для источника данных. Элементы, связанные в ItemsSource, могут быть любого типа, но если они явно являются PivotItems, необходимо определить параметры [**ItemTemplate**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.itemscontrol.itemtemplate.aspx) и [**HeaderTemplate**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.pivot.headertemplate.aspx), чтобы указать способ отображения элементов.

Чтобы получить или настроить активный элемент Pivot, можно использовать свойство [**SelectedItem**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.pivot.selecteditem.aspx). Чтобы получить или указать индекс активного элемента, используйте свойство [**SelectedIndex**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.pivot.selectedindex.aspx).

### Заголовки Pivot

Чтобы добавить другие элементы управления в заголовок Pivot, можно использовать свойства [**LeftHeader**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.pivot.leftheader.aspx) и [**RightHeader**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.pivot.rightheader.aspx).

### Взаимодействие Pivot

Элемент управления поддерживает следующие сенсорные жесты:

-   Касание элемента сводки приводит к переходу к контенту раздела этого заголовка.
-   Прокрутка влево или вправо по элементу сводки приводит к переходу к смежному разделу.
-   Прокрутка влево или вправо по содержимому раздела приводит к переходу к смежному разделу.

Элемент управления доступен в двух режимах:

**Стационарный**

-   Сводки неподвижны, если все заголовки сводок вписываются в доступное пространство.
-   Касание метки сводки приводит к переходу на соответствующую страницу, но сама сводка не перемещается. Активная сводка выделена.

**Карусель**

-   Карусель сводок, когда все заголовки не помещаются в доступном пространстве.
-   Касание метки сводки приводит к переходу на соответствующую страницу, а метка активной сводки перемещается на первую позицию.
-   Элементы сводки в цикле карусели сменяются от последнего к первому разделу сводки.

## Рекомендации

-   Определяйте выравнивание заголовков вкладок и сводок в зависимости от размера экрана. Для ширины экрана меньше 720 epx обычно лучше всего подходит выравнивание по центру, а выравнивание по левому краю рекомендуется в большинстве случаев для ширины экрана более 720 epx.
-   Не используйте более пяти заголовков в режиме карусели, так как это может запутать пользователя.
-   Используйте шаблон вкладок, только если элементы сводки имеют отдельные значки.
-   Включайте текст в заголовки элементов сводки, чтобы помочь пользователям понять значение каждого раздела сводки. Значки могут быть понятны не всем пользователям.



## Ссылки по теме

[Основы проектирования навигации](https://msdn.microsoft.com/library/windows/apps/dn958438)


<!--HONumber=May16_HO2-->


