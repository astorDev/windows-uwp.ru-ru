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
keywords: windows 10, uwp
design-contact: Judysa
doc-status: Published
ms.localizationpriority: medium
ms.custom: RS5
ms.openlocfilehash: 52ec77bd6199e0388d9ee9911c7a01e9fbe77fad
ms.sourcegitcommit: cbe7cf620622a5e4df7414f9e38dfecec1cfca99
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2018
ms.locfileid: "7424387"
---
# <a name="icons-for-uwp-apps"></a>Значки для приложений UWP

![Значки изображений заголовка](images/icons/header-icons.png)

Значки— это визуальный ярлык для действий, концепций или продуктов. Передача значения в символическом изображении (значке) позволяет преодолеть языковые барьеры и сэкономить крайне ценный ресурс: пространство на экране. 

Значки могут отображаться в приложениях и за их пределами. 

:::row:::
    :::column:::
        **Icons inside the app**

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

:::row:::
    :::column:::
        ![do](images/do.svg)
        ![icons standard image](images/icons/icons-standard.svg)<br>

        Use an icon for actions, like cut, copy, paste, and save, or for navigation items in a navigation menu.
    :::column-end:::
    :::column:::
        ![don't](images/dont.svg)
        ![icons concept image](images/icons/icons-concept.svg)<br>

        Use an icon if one already exists for the concept you want to represent. (To see whether an icon exists, check the Segoe icon list.)
    :::column-end:::
:::row-end:::

:::row:::
    :::column:::
        ![do](images/do.svg)
        ![icon shopping cart](images/icons/icon-shopping-cart.svg)<br>

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
:::row:::
    :::column:::
        Microsoft provides over 1000 icons in the form of the Segoe MDL2 Assets font. It might not be intuitive to get an icon from a font, but our font display technology means these icons will look crisp and sharp on any display, at any resolution, and at any size. 
    :::column-end:::
    :::column:::
        ![pre-defined icon image](images/icons/predefined-icon.png)
    :::column-end:::
:::row-end:::

### <a name="use-a-font"></a>Использование шрифта.
:::row:::
    :::column:::
        You don't have to use the Segoe MDL2 Assets font--you can use any font the user has installed on their system, such as Wingdings or Webdings.
    :::column-end:::
    :::column:::
        ![wingdings image](images/icons/wingdings.png)
    :::column-end:::
:::row-end:::

### <a name="use-a-scalable-vector-graphics-svg-file"></a>Использование файла векторной графики SVG.
:::row:::
    :::column:::
        SVG resources are ideal for icons, because they always look sharp at any size or resolution. Most drawing applications can export to SVG. 
    :::column-end:::
    :::column:::
        ![SVG image](images/icons/icon-scale.gif)
    :::column-end:::
:::row-end:::

### <a name="use-geometry-objects"></a>Использование объектов Geometry.
:::row:::
    :::column:::
        Like SVG files, geometries are a vector-based resource, so they always look sharp. However, creating a geometry is complicated because you have to individually specify each point and curve. It's really only a good choice if you need to modify the icon while your app is running (to animate it, for example). For instructions, see [Move and draw commands for geometries](../../xaml-platform/move-draw-commands-syntax.md). 
    :::column-end:::
    :::column:::
        ![Geometry objects image](images/icons/geometry-objects.png)
    :::column-end:::
:::row-end:::

### <a name="you-can-also-use-a-bitmap-image-such-as-png-gif-or-jpeg-although-we-dont-recommend-it"></a>Вы также можете использовать растровое изображение (например, в формате PNG, GIF и JPEG),, хотя это не рекомендуется.
:::row:::
    :::column:::
        Bitmap images are created at a specific size, so they have to be scaled up or down depending on how large you want the icon to be and the resolution of the screen. When the image is scaled down (shrunk), it can appear blurry; when it's scaled up, it can appear blocky and pixelated. If you have to use a bitmap image we recommend using a PNG or GIF over a JPEG. 
    :::column-end:::
    :::column:::
        ![don't](images/dont.svg)
        ![Bitmap image](images/icons/bitmap-image.png)
    :::column-end:::
:::row-end:::

## <a name="make-the-icon-do-something"></a>Привязка функции к значку

После создания значка, следующим шагом является упрощение сделать что-то за счет связывания ее с помощью команды или действия навигации. Для этого проще всего добавить значок в кнопку или панель команд. 

![Изображение панели команд](images/icons/app-bar-desktop.svg)

## <a name="create-an-icon-button"></a>Создание кнопки значка

Значок можно поместить в стандартную кнопку. Так как кнопки можно использовать в различных местах, это дает больше гибкости при выборе отображения значка. 

Вот несколько способов добавления значка в кнопку.

:::row:::
    :::column span="2":::
        <b>Step 1</b><br>
        Set the button's font family to `Segoe MDL2 Assets` and its content property to the unicode value of the glyph you want to use:
    :::column-end:::
    :::column:::
        ![Create an icon button step 1](images/icons/create-icon-step-1.svg)
    :::column-end:::
:::row-end:::

```xaml 
<Button FontFamily="Segoe MDL2 Assets" Content="&#xE102;" />
```

:::row:::
    :::column span="2":::
        <b>Step 2</b><br>
        You can use one of the icon element objects: [BitmapIcon](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.bitmapicon),
        [FontIcon](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.fonticon), 
        [PathIcon](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.pathicon), or
        [SymbolIcon](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.symbolicon). This gives you more types of icons to choose from, and enables you to combine icons and other types of content, such as text, if you want:
    :::column-end:::
    :::column:::
        ![Create an icon button step 2](images/icons/icon-text-step-2.svg)
    :::column-end:::
:::row-end:::

```xaml 
<Button>
    <StackPanel>
        <SymbolIcon Symbol="Play" />
        <TextBlock>Play the movie</TextBlock>
    </StackPanel>
</Button>
```

## <a name="create-a-series-of-icons-in-a-command-bar"></a>Создание серии значков на панели команд

:::row:::
    :::column span:::
        When you have a series of commands that go together, such as cut/copy/paste or a set of drawing commands for a photo-editing program, put them together in a [command bar](../controls-and-patterns/app-bars.md). A command bar takes one or more app bar buttons or app bar toggle buttons, each of which represents an action. Each button has an [Icon](https://docs.microsoft.com/en-us/uwp/api/windows.ui.xaml.controls.appbarbutton#Windows_UI_Xaml_Controls_AppBarButton_Icon) property you use to control which icon it displays. There are a variety of ways to specify the icon. 
    :::column-end:::
    :::column:::
        ![Example of a command bar with icons](images/icons/create-icon-command-bar.svg)
    :::column-end:::
:::row-end:::

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
