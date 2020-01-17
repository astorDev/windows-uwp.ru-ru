---
Description: Хорошие значки согласуются со шрифтовым оформлением и языком проектирования в целом. В них метафоры не смешиваются — они сообщают только то, что нужно, как можно скорее и проще.
title: Значки
ms.assetid: b90ac02d-5467-4304-99bd-292d6272a014
label: Icons
template: detail.hbs
ms.date: 05/02/2018
ms.topic: article
keywords: windows 10, uwp
design-contact: Judysa
doc-status: Published
ms.localizationpriority: medium
ms.custom: RS5
ms.openlocfilehash: e30e9b2bed5cb4c0b7876ff1c597bb7d1243008a
ms.sourcegitcommit: 26bb75084b9d2d2b4a76d4aa131066e8da716679
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/06/2020
ms.locfileid: "75684166"
---
# <a name="icons-for-uwp-apps"></a>Значки для приложений UWP

![Значки изображений заголовка](images/icons/header-icons.png)

Значки — это визуальный ярлык для действий, концепций или продуктов. Передача значения в символическом изображении (значке) позволяет преодолеть языковые барьеры и сэкономить крайне ценный ресурс: пространство на экране. 

Значки могут отображаться в приложениях и за их пределами. 

:::row:::
    :::column:::
        **Значки в приложении**

        ![Значки в приложении](images/icons/inside-icons.png) Внутри приложения используйте значки для представления действия, например, копирования текста или перехода на страницу "Параметры".
    :::column-end:::
    :::column:::
**Значки за пределами приложения**

        ![Значки за пределами приложения](images/icons/outside-icons.jpg) За пределами вашего приложения Windows использует значок приложения, отображаемый в меню "Пуск" и на панели задач. Если пользователь решит закрепить ваше приложение в меню "Пуск", начальная плитка приложения может содержать его значок. Значок приложения отображается в строке заголовка, и вы можете создать экран заставки с логотипом своего приложения.
    :::column-end:::
:::row-end:::

В этой статье описываются значки в вашем приложении. Дополнительные сведения о значках вне приложений (значки приложения) см. в [статье, посвященной значкам плитки и приложения](/windows/uwp/design/shell/tiles-and-notifications/app-assets).

## <a name="when-to-use-icons"></a>Когда использовать значки

Значки позволяют сэкономить место, но когда их следует использовать? 

:::row:::
    :::column:::
        ![Рекомендуется](images/do.svg) ![Стандартное изображение значка](images/icons/icons-standard.svg)<br>

Используйте значок для действий, таких как вырезание, копирование, вставка и сохранение, или для элементов навигации в меню переходов.
    :::column-end:::
    :::column:::
        ![Не рекомендуется](images/dont.svg) ![Концептуальное изображение значка](images/icons/icons-concept.svg)<br>

Используйте значок, если он уже существует, для концепции, которую вам необходимо представить. (Чтобы узнать, существует ли значок, просмотрите список значков Segoe.)
    :::column-end:::
:::row-end:::

:::row:::
    :::column:::
        ![Рекомендуется](images/do.svg) ![Значок корзины для покупок](images/icons/icon-shopping-cart.svg)<br>

Используйте значок, если пользователю легко понять, что он означает, и это очевидно, даже если значок небольшого размера.
    :::column-end:::
    :::column:::
        ![Не рекомендуется](images/dont.svg) ![Концептуальное изображение значка](images/icons/icon-bad-example.png)<br>

Не используйте значок, если его значение непонятно или его пояснение требует сложной формы.
    :::column-end:::
:::row-end:::



## <a name="using-the-right-type-of-icon"></a>Использование правильного типа значка

Существует множество способов создания значка. Вы можете использовать символьный шрифт, например Segoe MDL2 Assets. Вы можете создать собственное векторное изображение. Вы можете даже использовать растровое изображение, хотя это не рекомендуется. Далее представлен обзор различных способов добавления значка в приложение. 

### <a name="use-a-predefined-icon"></a>Использование стандартного значка.
:::row:::
    :::column:::
Корпорация Майкрософт предоставляет более 1000 значков в виде шрифта Segoe MDL2 Assets. Получить значок из шрифта может быть не так просто, но наша технология отображения шрифта гарантирует, что эти значки будут выглядеть четко и резко на любом дисплее, при любом разрешении и в любом размере. Инструкции см. в разделе [Значки Segoe MDL2](segoe-ui-symbol-font.md).
    :::column-end:::
    :::column:::
        ![Изображение предварительно определенного значка](images/icons/predefined-icon.png)
    :::column-end:::
:::row-end:::

### <a name="use-a-font"></a>Использование шрифта.
:::row:::
    :::column:::
Вам не обязательно использовать шрифт Segoe MDL2 Assets, вы можете выбрать любой шрифт, установленный пользователем в системе, например Wingdings или Webdings.
    :::column-end:::
    :::column:::
        ![Изображение wingdings](images/icons/wingdings.png)
    :::column-end:::
:::row-end:::

### <a name="use-a-scalable-vector-graphics-svg-file"></a>Использование SVG-файла векторной графики.
:::row:::
    :::column:::
Ресурсы SVG идеально подходят для значков, потому они всегда четко выглядят при любом размере или разрешении. Большинство приложений для рисования поддерживает экспорт в SVG-файлы. Инструкции см. в разделе [SVGImageSource](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.imaging.svgimagesource).
    :::column-end:::
    :::column:::
        ![Изображение SVG](images/icons/icon-scale.gif)
    :::column-end:::
:::row-end:::

### <a name="use-geometry-objects"></a>Использование объектов Geometry.
:::row:::
    :::column:::
Подобно SVG-файлам геометрии являются векторными ресурсами, поэтому они всегда выглядят четко. Однако создать геометрию не так просто, так как вам необходимо отдельно указать все точки и кривые. Этот вариант следует использовать, только если вам нужно изменить значок во время выполнения приложения (например, для анимации). Инструкции см. в разделе [Синтаксис команд перемещения и рисования](../../xaml-platform/move-draw-commands-syntax.md). 
    :::column-end:::
    :::column:::
        ![Изображение объектов Geometry](images/icons/geometry-objects.png)
    :::column-end:::
:::row-end:::

### <a name="you-can-also-use-a-bitmap-image-such-as-png-gif-or-jpeg-although-we-dont-recommend-it"></a>Вы также можете использовать растровое изображение (например, в формате PNG, GIF и JPEG), хотя это не рекомендуется.
:::row:::
    :::column:::
Растровые изображения создаются в определенном размере, поэтому их необходимо масштабировать в зависимости от требуемого размера значка и разрешения экрана. При уменьшении (сжатии) размера значка он может оказаться размытым. При увеличении размера он может стать угловатым и пикселизированным. Если вам необходимо использовать растровое изображение, рекомендуется формат PNG или GIF, а не JPEG. 
    :::column-end:::
    :::column:::
        ![Не рекомендуется](images/dont.svg) ![Битовое изображение](images/icons/bitmap-image.png)
    :::column-end:::
:::row-end:::

## <a name="make-the-icon-do-something"></a>Привязка функции к значку

После создания значка следующим шагом является привязка к нему команды или действия перехода. Для этого рекомендуется добавить значок на кнопку или панель команд. 

![Изображение панели команд](images/icons/app-bar-desktop.svg)

## <a name="create-an-icon-button"></a>Создание кнопки значка

Значок можно поместить на стандартную кнопку. Так как кнопки можно использовать в различных местах, это дает больше гибкости при выборе расположения значка. 

Вот несколько способов добавления значка на кнопку.

:::row:::
    :::column span="2":::
        <b>Шаг 1.</b><br>
Установите для семейства шрифтов кнопки значение `Segoe MDL2 Assets`, а для ее свойства Content — значение Юникода глифа, который вы хотите использовать.
    :::column-end:::
    :::column:::
        ![Создание кнопки значка (шаг 1)](images/icons/create-icon-step-1.svg)
    :::column-end:::
:::row-end:::

```xaml 
<Button FontFamily="Segoe MDL2 Assets" Content="&#xE102;" />
```

:::row:::
    :::column span="2":::
        <b>Шаг 2.</b><br>
Можно использовать один из объектов элемента значка: [BitmapIcon](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.bitmapicon), [FontIcon](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.fonticon), [PathIcon](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.pathicon) или [SymbolIcon](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.symbolicon). Это позволяет получить более широкий выбор значков и объединять разные значки и другие типы содержимого, например текст.
    :::column-end:::
    :::column:::
        ![Создание кнопки значка (шаг 2)](images/icons/icon-text-step-2.svg)
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
Если у вас есть несколько похожих команд, таких как вырезать, скопировать и вставить, или набор команд рисования для программы редактирования фотографий, объедините их на [панели команд](../controls-and-patterns/app-bars.md). На панели команд размещается одна или несколько кнопок панели приложения или кнопок-переключателей панели приложения, каждая из которых представляет действие. У каждой кнопки есть свойство [Icon](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.appbarbutton#Windows_UI_Xaml_Controls_AppBarButton_Icon), которое используется для выбора отображаемого значка. Существует множество способов указать значок. 
    :::column-end:::
    :::column:::
        ![Пример панели команд со значками](images/icons/create-icon-command-bar.svg)
    :::column-end:::
:::row-end:::

Проще всего использовать список стандартных значков, которые предоставляем мы, — просто укажите имя значка, например "Назад" или "Стоп", и система его нарисует. 

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



## <a name="related-articles"></a>Похожие статьи

* [Руководство по работе с ресурсами плиток и значков](../shell/tiles-and-notifications/app-assets.md)
