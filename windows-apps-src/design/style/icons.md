---
Description: Хорошие значки согласуются со шрифтовым оформлением и языком проектирования в целом. В них метафоры не смешиваются — они сообщают только то, что нужно, как можно скорее и проще.
title: Значки
ms.assetid: b90ac02d-5467-4304-99bd-292d6272a014
label: Icons
template: detail.hbs
ms.date: 05/02/2018
ms.topic: article
keywords: windows 10, uwp
design-contact: Judysa
doc-status: Published
ms.localizationpriority: medium
ms.custom: RS5
ms.openlocfilehash: 5e464251200812e79474d05d9d0a680b49167871
ms.sourcegitcommit: 7da28cf4f4e8390bc9a21a9488b03af39271cbbe
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2019
ms.locfileid: "64564544"
---
# <a name="icons-for-uwp-apps"></a>Значки для приложений UWP

![Значки изображений заголовка](images/icons/header-icons.png)

Значки — это визуальный ярлык для действий, концепций или продуктов. Передача значения в символическом изображении (значке) позволяет преодолеть языковые барьеры и сэкономить крайне ценный ресурс: пространство на экране. 

Значки могут отображаться в приложениях и за их пределами. 

:::row:::
    :::column:::
        **Icons inside the app**

        ![icons inside the app](images/icons/inside-icons.png)
Внутри приложения используйте значки для представления действие, например копирование текста или перейти к странице "Параметры".
    :::column-end:::
    :::column:::
**Значки за пределами приложения**

        ![icons outside the app](images/icons/outside-icons.jpg)
За пределами вашего приложения Windows использует значок для представления приложения в меню "Пуск" и на панели задач. Если пользователь выберет закрепить приложение меню "Пуск", плитку приложения может включать от значка приложения. В строке заголовка отображается значок приложения, и вы можете создать экран-заставка с логотип вашего приложения.
    :::column-end:::
:::row-end:::

В этой статье описываются значки в вашем приложении. Дополнительные сведения о значках вне приложений (значки приложения) см. в [статье, посвященной значкам плитки и приложения](/windows/uwp/design/shell/tiles-and-notifications/app-assets).

## <a name="when-to-use-icons"></a>Когда использовать значки

Значки позволяют сэкономить место, но когда их следует использовать? 

:::row:::
    :::column:::
        ![do](images/do.svg)
        ![icons standard image](images/icons/icons-standard.svg)<br>

Используйте значок для действий, таких как вырезание, копирование и вставка и сохранить или для элементов навигации в меню переходов.
    :::column-end:::
    :::column:::
        ![don't](images/dont.svg)
        ![icons concept image](images/icons/icons-concept.svg)<br>

Используйте значок, если он уже существует для концепция, которую необходимо представить. (Чтобы узнать, существует ли значок, проверьте список значок Segoe.)
    :::column-end:::
:::row-end:::

:::row:::
    :::column:::
        ![do](images/do.svg)
        ![icon shopping cart](images/icons/icon-shopping-cart.svg)<br>

Используйте значок, если облегчает пользователю понять, что означает значок, и это достаточно простым, чтобы быть снимите при небольших размерах.
    :::column-end:::
    :::column:::
        ![dont](images/dont.svg)
        ![icons concept image](images/icons/icon-bad-example.png)<br>

Не используйте значок, если его значение не очистить, или чего понятно, требует сложной фигуры.
    :::column-end:::
:::row-end:::



## <a name="using-the-right-type-of-icon"></a>Использование правильного типа значка

Существует множество способов создания значка. Вы можете использовать символьный шрифт, например Segoe MDL2 Assets. Можно создать свой собственный образ на основе векторов. Вы можете даже использовать растровое изображение, хотя это не рекомендуется. Далее представлен обзор различных способов добавления значка в приложение. 

### <a name="use-a-predefined-icon"></a>Использование стандартного значка.
:::row:::
    :::column:::
Корпорация Майкрософт предоставляет более чем 1000 значки в виде шрифт Segoe MDL2 активы. Получить значок из шрифта может быть не так просто, но наша технология отображения шрифта гарантирует, что эти значки будут выглядеть четко и резко на любом дисплее, при любом разрешении и в любом размере. Инструкции см. в разделе [Segoe MDL2 значки](segoe-ui-symbol-font.md).
    :::column-end:::
    :::column:::
        ![pre-defined icon image](images/icons/predefined-icon.png)
    :::column-end:::
:::row-end:::

### <a name="use-a-font"></a>Использование шрифта.
:::row:::
    :::column:::
Нет необходимости использовать шрифт Segoe MDL2 активы--можно использовать любой шрифт, пользователь установил на системе, например Wingdings или Webdings.
    :::column-end:::
    :::column:::
        ![wingdings image](images/icons/wingdings.png)
    :::column-end:::
:::row-end:::

### <a name="use-a-scalable-vector-graphics-svg-file"></a>Использование файла векторной графики SVG.
:::row:::
    :::column:::
Ресурсы SVG идеально подходят для значков, так как они всегда производят sharp в любого размера и разрешения. Большинство приложений для рисования поддерживают экспорт файлов в формате SVG. Инструкции см. в разделе [SVGImageSource](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.imaging.svgimagesource).
    :::column-end:::
    :::column:::
        ![SVG image](images/icons/icon-scale.gif)
    :::column-end:::
:::row-end:::

### <a name="use-geometry-objects"></a>Использование объектов Geometry.
:::row:::
    :::column:::
Подобно файлам SVG геометрические фигуры являются ресурсов на основе вектора, поэтому они всегда производят sharp. Однако создать геометрию не так просто, так как вам необходимо отдельно указать все точки и кривые. Этот вариант следует использовать, только если вам нужно изменить значок во время выполнения приложения (например, для анимации). Инструкции см. в разделе [Команды перемещения и рисования для геометрии](../../xaml-platform/move-draw-commands-syntax.md). 
    :::column-end:::
    :::column:::
        ![Geometry objects image](images/icons/geometry-objects.png)
    :::column-end:::
:::row-end:::

### <a name="you-can-also-use-a-bitmap-image-such-as-png-gif-or-jpeg-although-we-dont-recommend-it"></a>Вы также можете использовать растровое изображение (например, в формате PNG, GIF и JPEG),, хотя это не рекомендуется.
:::row:::
    :::column:::
Растровые изображения создаются определенного размера, поэтому они должны масштабироваться вверх или вниз в зависимости от размера значка требуется и разрешение экрана. При уменьшении (сжатии) размера значка, он может оказаться размытым. При увеличении размера, он может стать угловато и пикселизированным. Если вам необходимо использовать растровое изображение, рекомендуется формат PNG или GIF, а не JPEG. 
    :::column-end:::
    :::column:::
        ![don't](images/dont.svg)
        ![Bitmap image](images/icons/bitmap-image.png)
    :::column-end:::
:::row-end:::

## <a name="make-the-icon-do-something"></a>Привязка функции к значку

У вас есть значок, следующим шагом после добиться того, что-то с команды или действия, связанного с операцией. Для этого рекомендуется добавить значок для кнопки или панели команд. 

![Изображение панели команд](images/icons/app-bar-desktop.svg)

## <a name="create-an-icon-button"></a>Создание кнопки значка

Значок можно поместить в стандартную кнопку. Так как кнопки можно использовать в различных местах, это дает больше гибкости при выборе отображения значка. 

Вот несколько способов добавления значка в кнопку.

:::row:::
    :::column span="2":::
        <b>Step 1</b><br>
Установить семейство шрифтов кнопки в `Segoe MDL2 Assets` и его свойство содержимого значение Юникода глифа, который вы хотите использовать:
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
Можно использовать один из объектов значок элемента: [BitmapIcon](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.bitmapicon), [FontIcon](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.fonticon), [PathIcon](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.pathicon), или [SymbolIcon](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.symbolicon). Это дает дополнительные типы значков на выбор и позволяет объединять значки и другие типы содержимого, такие как текст, если вы хотите, чтобы:
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
При наличии ряд команд, которые используются вместе, такие как вырезания/копирования/вставки или набор команд для программы редактирования изображений, рисования поместите их в [панели команд](../controls-and-patterns/app-bars.md). На панели команд размещается одна или несколько кнопок панели приложения или кнопок-переключателей панели приложения, каждая из которых представляет действие. У каждой кнопки есть свойство [Icon](https://docs.microsoft.com/en-us/uwp/api/windows.ui.xaml.controls.appbarbutton#Windows_UI_Xaml_Controls_AppBarButton_Icon), которое используется для выбора отображаемого значка. Существует множество способов указать значок. 
    :::column-end:::
    :::column:::
        ![Example of a command bar with icons](images/icons/create-icon-command-bar.svg)
    :::column-end:::
:::row-end:::

Проще всего использовать список стандартных значков, которые предоставляем мы, — просто укажите имя значка, например "Назад" или "Стоп", и система его нарисует: 

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
+ [BitmapIcon](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.bitmapicon) — основой значка служит файл растрового изображения с указанным **Uri**.
+ [PathIcon](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.pathicon) — основой значка служат данные [Path](/uwp/api/windows.ui.xaml.shapes.path).

Дополнительные сведения о панелях команд см. в [статье, посвященной панели команд](../controls-and-patterns/app-bars.md). 



## <a name="related-articles"></a>Связанные статьи

* [Рекомендации по плитки и значок ресурсов](../shell/tiles-and-notifications/app-assets.md)
