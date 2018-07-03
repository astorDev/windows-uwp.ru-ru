---
author: mijacobs
Description: Good icons harmonize with typography and with the rest of the design language. They don’t mix metaphors, and they communicate only what’s needed, as speedily and simply as possible.
title: Значки
ms.assetid: b90ac02d-5467-4304-99bd-292d6272a014
label: Icons
template: detail.hbs
ms.author: mijacobs
ms.date: 05/02/2018
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, uwp
design-contact: Judysa
doc-status: Published
ms.localizationpriority: medium
ms.openlocfilehash: 077967c37f76c8f1d0942f365344de65db13b041
ms.sourcegitcommit: ce45a2bc5ca6794e97d188166172f58590e2e434
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/06/2018
ms.locfileid: "1983577"
---
# <a name="icons-for-uwp-apps"></a>Значки для приложений UWP

![Значки изображений заголовка](images/icons/header-icons.png)

Значки— это визуальный ярлык для действий, концепций или продуктов. Передача значения в символическом изображении (значке) позволяет преодолеть языковые барьеры и сэкономить крайне ценный ресурс: пространство на экране. 

Значки могут отображаться в приложениях и за их пределами. 

:::row::: :::column::: **Значки в приложении**

        ![icons inside the app](images/icons/inside-icons.png)
        Inside your app, you use icons to represent an action, such as copying text or navigating to the settings page.
    :::column-end:::
    :::column:::
        **Icons outside the app**

        ![icons outside the app](images/icons/outside-icons.jpg)
         Outside your app, Windows uses an icon to represent your app in the start menu and in the taskbar. If the user chooses to pin your app to the start menu, your app's start tile can feature your app's icon. Your app's icon appears in the title bar and you can choose to create a splash screen with your app's logo.
    :::column-end:::
:::row-end:::

В этой статье описываются значки в вашем приложении. Дополнительные сведения о значках вне приложений (значки приложения) см. в [статье, посвященной значкам плитки и приложения](/windows/uwp/design/shell/tiles-and-notifications/app-assets).

## <a name="when-to-use-icons"></a>Когда использовать значки

Значки позволяют сэкономить место, но когда их следует использовать? 

:::row::: :::column::: ![do](images/do.svg) ![Стандартное изображение значка](images/icons/icons-standard.svg)<br>

        Use an icon for actions, like cut, copy, paste, and save, or for navigation items in a navigation menu.
    :::column-end:::
    :::column:::
        ![don't](images/dont.svg)
        ![icons concept image](images/icons/icons-concept.svg)<br>

        Use an icon if one already exists for the concept you want to represent. (To see whether an icon exists, check the Segoe icon list.)
    :::column-end:::
:::row-end:::

:::row::: :::column::: ![do](images/do.svg) ![Значок корзины](images/icons/icon-shopping-cart.svg)<br>

        Use an icon if it's easy for the user to understand what the icon means and it's simple enough to be clear at small sizes.
    :::column-end:::
    :::column:::
        ![dont](images/dont.svg)
        ![icons concept image](images/icons/icon-bad-example.png)<br>

        Don't use an icon if its meaning isn't clear, or if making it clear requires a complex shape.
    :::column-end:::
:::row-end:::



## <a name="using-the-right-type-of-icon"></a>Использование правильного типа значка

Существует множество способов создания значка. Вы можете использовать символьный шрифт, например Segoe MDL2 Assets. Вы можете создать собственное векторное изображение. Вы можете даже использовать растровое изображение, хотя это не рекомендуется. Далее представлен обзор различных способов добавления значка в приложение. 

### <a name="use-a-predefined-icon"></a>Использование стандартного значка.
:::row::: :::column::: Корпорация Майкрософт предоставляет более 1000 значков в виде шрифта Segoe MDL2 Assets. Получить значок из шрифта может быть не так просто, но наша технология отображения шрифта гарантирует, что эти значки будут выглядеть четко и резко на любом дисплее, при любом разрешении и в любом размере. :::column-end::: :::column::: ![стандартное изображение значка](images/icons/predefined-icon.png) :::column-end::: :::row-end:::

### <a name="use-a-font"></a>Использование шрифта.
:::row::: :::column::: Вам необязательно использовать шрифт Segoe MDL2 Assets, вы можете выбрать любой шрифт, установленный пользователем в системе, например Wingdings или Webdings.
:::column-end::: :::column::: ![изображение wingdings](images/icons/wingdings.png) :::column-end::: :::row-end:::

### <a name="use-a-scalable-vector-graphics-svg-file"></a>Использование файла векторной графики SVG.
:::row::: :::column::: Ресурсы SVG идеально подходят для значков, потому они всегда четко выглядят при любом размере или разрешении. Большинство приложений для рисования поддерживают экспорт файлов в формате SVG. :::column-end::: :::column::: ![Изображение SVG](images/icons/icon-scale.gif) :::column-end::: :::row-end:::

### <a name="use-geometry-objects"></a>Использование объектов Geometry.
:::row::: :::column::: Подобно SVG-файлам геометрии являются векторными ресурсами, поэтому они всегда выглядят четко. Однако создать геометрию не так просто, так как вам необходимо отдельно указать все точки и кривые. Этот вариант следует использовать, только если вам нужно изменить значок во время выполнения приложения (например, для анимации). Инструкции см. в разделе [Команды перемещения и рисования для геометрии](../../xaml-platform/move-draw-commands-syntax.md). :::column-end::: :::column::: ![изображение объектов Geometry](images/icons/geometry-objects.png) :::column-end::: :::row-end:::

### <a name="you-can-also-use-a-bitmap-image-such-as-png-gif-or-jpeg-although-we-dont-recommend-it"></a>Вы также можете использовать растровое изображение (например, в формате PNG, GIF и JPEG),, хотя это не рекомендуется.
:::row::: :::column::: Растровые изображения создаются в определенном размере, поэтому их необходимо масштабировать в зависимости от требуемого размера значка и разрешения экрана. При уменьшении (сжатии) размера значка, он может оказаться размытым. При увеличении размера, он может стать угловато и пикселизированным. Если вам необходимо использовать растровое изображение, рекомендуется формат PNG или GIF, а не JPEG. :::column-end::: :::column::: ![don't](images/dont.svg) ![Растровое изображение](images/icons/bitmap-image.png) :::column-end::: :::row-end:::

## <a name="make-the-icon-do-something"></a>Привязка функции к значку

После создания значка следующим шагом является привязка к нему команды или действия навигации. Для этого рекомендуется добавить значок в кнопку или панель команд. 

![Изображение панели команд](images/icons/app-bar-desktop.svg)

## <a name="create-an-icon-button"></a>Создание кнопки значка

Значок можно поместить в стандартную кнопку. Так как кнопки можно использовать в различных местах, это дает больше гибкости при выборе отображения значка. 

Вот несколько способов добавления значка в кнопку.

:::row::: :::column span="2"::: <b>Шаг 1</b><br>
        Выберите для кнопки семейство шрифтов `Segoe MDL2 Assets` и задайте свойству content значение Юникода, соответствующее глифу, который вы хотите использовать :::column-end::: :::column::: ![Создание кнопки значка, шаг 1](images/icons/create-icon-step-1.svg) :::column-end::: :::row-end:::

```xaml 
<Button FontFamily="Segoe MDL2 Assets" Content="&#xE102;" />
```

:::row::: :::column span="2"::: <b>Шаг 2</b><br>
        Вы можете использовать один из объектов элемента значка: [BitmapIcon](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.bitmapicon), [FontIcon](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.fonticon), [PathIcon](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.pathicon) и [SymbolIcon](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.symbolicon). Это позволяет получить более широкий выбор значков и объединять разные значки и другие типы содержимого, например текст: want: :::column-end::: :::column::: ![Создание кнопки значка, шаг 2](images/icons/icon-text-step-2.svg) :::column-end::: :::row-end:::

```xaml 
<Button>
    <StackPanel>
        <SymbolIcon Symbol="Play" />
        <TextBlock>Play the movie</TextBlock>
    </StackPanel>
</Button>
```

## <a name="create-a-series-of-icons-in-a-command-bar"></a>Создание серии значков на панели команд

:::row::: :::column span::: Если у вас есть несколько похожих команд, таких как вырезать, скопировать и вставить, или набор команд рисования для программы редактирования фотографий, объедините их на [панели команд](../controls-and-patterns/app-bars.md). На панели команд размещается одна или несколько кнопок панели приложения или кнопок-переключателей панели приложения, каждая из которых представляет действие. У каждой кнопки есть свойство [Icon](https://docs.microsoft.com/en-us/uwp/api/windows.ui.xaml.controls.appbarbutton#Windows_UI_Xaml_Controls_AppBarButton_Icon), которое используется для выбора отображаемого значка. Существует множество способов указать значок. :::column-end::: :::column::: ![Пример панели команд со значками](images/icons/create-icon-command-bar.svg) :::column-end::: :::row-end:::

Проще всего использовать список стандартных значков, которые предоставляем мы,— просто укажите имя значка, например "Назад" или "Стоп", и система его нарисует: 

``` xaml
<CommandBar>
    <AppBarToggleButton Icon="Shuffle" Label="Shuffle" Click="AppBarButton_Click" />
    <AppBarToggleButton Icon="RepeatAll" Label="Repeat" Click="AppBarButton_Click"/>
    <AppBarSeparator/>
    <AppBarButton Icon="Back" Label="Back" Click="AppBarButton_Click"/>
    <AppBarButton Icon="Stop" Label="Stop" Click="AppBarButton_Click"/>
    <AppBarButton Icon="Play" Label="Play" Click="AppBarButton_Click"/>
    <AppBarButton Icon="Forward" Label="Forward" Click="AppBarButton_Click"/>
</CommandBar>

```
Полный список имен значков см. в разделе [Перечисление Symbol](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.symbol). 

Существуют и другие способы указания значков для кнопок на панели команд:

+ [FontIcon](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.fonticon) — основой значка служит глиф из указанного семейства шрифтов.
+ [BitmapIcon](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.bitmapicon)— основой значка служит файл растрового изображения с указанным **Uri**.
+ [PathIcon](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.pathicon)— основой значка служат данные [Path](/uwp/api/windows.ui.xaml.shapes.path).

Дополнительные сведения о панелях команд см. в [статье, посвященной панели команд](../controls-and-patterns/app-bars.md). 



## <a name="related-articles"></a>Статьи по теме

* [Руководство по работе с ресурсами плиток и значков](../shell/tiles-and-notifications/app-assets.md)
