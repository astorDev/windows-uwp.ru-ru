---
Description: Создавайте приложения универсальной платформы Windows и custom/шаблонного элементы управления, которые поддерживают масштабирование текста платформы.
title: Масштабирование текста
label: Text scaling
template: detail.hbs
keywords: Отобразить UWP, текст, масштабирования, доступности, «специальные возможности», «Больше текста», взаимодействие с пользователем, входные данные
ms.date: 08/02/2018
ms.topic: article
ms.localizationpriority: medium
ms.custom: RS5
ms.openlocfilehash: 22ad7a1ac6160fd8b1cfb70c69f299c5d89192d3
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57600819"
---
# <a name="text-scaling"></a>Масштабирование текста

![Пример текста, масштабирование 100 – 225%](images/coretext/text-scaling-news-hero-small.png)  
*Пример текста, масштабирование в Windows 10 (100 – 225%)*

## <a name="overview"></a>Обзор

Чтение текста на экране компьютера (с мобильного устройства для ноутбука на настольный монитор на экране giant Surface Hub) может оказаться сложной задачей для многих людей. И наоборот некоторые пользователи находят размеры шрифтов, используемые в приложениях и веб-сайтов еще больше, чем необходимо.

Чтобы убедиться, что текст как разборчиво максимально для самого широкого круга пользователей, Windows предоставляет возможность пользователям изменить относительный размер шрифта в отдельных приложений и операционной системы. А не с помощью приложения Экранная лупа (который обычно просто увеличивает все содержимое внутри той части экрана и вводит собственные проблем с удобством использования), изменения разрешения экрана или полагаться на масштабирование (которая изменяет размер все, что на основе отображения и просмотра типичных расстояние), пользователь может быстро открыть параметр, чтобы изменить размер только текст, в диапазоне от 100% (размер по умолчанию) на % 225.

## <a name="support"></a>Поддержка

Универсальные приложения Windows (как со стандартным и PWA), обеспечить поддержку текста, масштабирование по умолчанию.

Если приложение универсальной платформы Windows содержит пользовательские элементы управления, поверхностей пользовательского текста, высота элемента управления, жестко, более старых платформ или платформ сторонних производителей, скорее всего нужно внести некоторые обновления, чтобы обеспечить согласованный и полезным для пользователей.  

DirectWrite, GDI и XAML SwapChainPanels не поддерживают изначально масштабирование текста, хотя Win32 поддержка предоставляется только для меню, панелей инструментов и значки.  

<!-- If you want to support text scaling in your application with these frameworks, you’ll need to support the text scaling change event outlined below and provide alternative sizes for your UI and content.   -->

## <a name="user-experience"></a>Взаимодействие с пользователем

Пользователи могут изменять масштаб текста с текста "->" больше "ползунок" Параметры специальных возможностей, "->" концепции и отображения экрана.

![Пример текста, масштабирование 100 – 225%](images/coretext/text-scaling-settings-100-small.png)  
*Масштаб текста, с помощью параметров "->" специальные возможности "->" концепции и отображения экрана*

## <a name="ux-guidance"></a>Руководство по проектированию взаимодействия с пользователем

При изменении размера текста, элементов управления и контейнеров необходимо также изменить размер и обратное течение для размещения текста и его новой структуры. Как упоминалось ранее, в зависимости от приложения, платформы и платформы, значительную часть этой работы выполняется автоматически. Следующее руководство UX рассматриваются случаи, когда нет.

### <a name="use-the-platform-controls"></a>С помощью элементов управления платформы

Говорят, это уже? Стоит повторения: По возможности всегда используйте встроенные элементы управления, в состав различных платформ приложений Windows для получения наиболее полное взаимодействие с пользователем возможных минимальными усилиями.

Например все элементы управления универсальной платформы Windows поддерживает полный текст масштабирование без настройки и шаблонов.

Ниже приведен фрагмент простое приложение UWP, которое включает несколько стандартных текстовых элементов управления.

``` xaml
<Grid>
    <Grid.RowDefinitions>
        <RowDefinition Height="Auto"/>
        <RowDefinition Height="Auto" />
        <RowDefinition Height="Auto"/>
    </Grid.RowDefinitions>
    <TextBlock Grid.Row="0" 
                Style="{ThemeResource TitleTextBlockStyle}"
                Text="Text scaling test" 
                HorizontalTextAlignment="Center" />
    <Grid Grid.Row="1">
        <Grid.ColumnDefinitions>
            <ColumnDefinition Width="Auto"/>
            <ColumnDefinition Width="*"/>
            <ColumnDefinition Width="Auto"/>
        </Grid.ColumnDefinitions>
        <Image Grid.Column="0" 
                Source="Assets/StoreLogo.png" 
                Width="450" Height="450"/>
        <StackPanel Grid.Column="1" 
                    HorizontalAlignment="Center">
            <TextBlock TextWrapping="WrapWholeWords">
                The quick brown fox jumped over the lazy dog.
            </TextBlock>
            <TextBox PlaceholderText="Type something here" />
        </StackPanel>
        <Image Grid.Column="2" 
                Source="Assets/StoreLogo.png" 
                Width="450" Height="450"/>
    </Grid>
    <TextBlock Grid.Row="2" 
                Style="{ThemeResource TitleTextBlockStyle}"
                Text="Text scaling test footer" 
                HorizontalTextAlignment="Center" />
</Grid>
```

![Анимированный текст, масштабирование 100 – 225%](images/coretext/text-scaling.gif)  
*Масштабирование анимированный текст*

### <a name="use-auto-sizing"></a>Использование автоматического изменения размера

Не указывайте абсолютные размеры элементов управления. Когда это возможно, позвольте платформе изменение размера элементов управления автоматически на основании параметров пользователя и устройства.  

В этом фрагменте кода из предыдущего примера мы используем `Auto` и `*` значения ширины для набора столбцов сетки, а также ускоряют выполнение платформы измените макет приложения на основе размера элементов, содержащихся в сетке.

``` xaml
<Grid.ColumnDefinitions>
    <ColumnDefinition Width="Auto"/>
    <ColumnDefinition Width="*"/>
    <ColumnDefinition Width="Auto"/>
</Grid.ColumnDefinitions>
```

### <a name="use-text-wrapping"></a>Использовать перенос текста

Чтобы убедиться, что макет приложения, как гибкость и адаптируемость максимально, включите обтекание текстом в любом элементе управления, содержащий текст (многие элементы управления не поддерживают обтекание текстом по умолчанию).

Если вы не укажете обтекание текстом, платформа использует другие методы для настройки макета, включая обрезки (см. предыдущий пример).

Здесь мы используем `AcceptsReturn` и `TextWrapping` свойства текстового поля, чтобы обеспечить разметку как можно более гибкой.

``` xaml
<TextBox PlaceholderText="Type something here" 
          AcceptsReturn="True" TextWrapping="Wrap" />
```

![Анимированный текст, масштабирование 100 – 225% с обтекание текстом](images/coretext/text-scaling-textwrap.gif)  
*Анимированный текст, масштабирование с помощью обтекание текстом*

### <a name="specify-text-trimming-behavior"></a>Укажите поведение обрезки текста

Если обтекания текстом не является предпочтительным поведение, большинство текстовые элементы управления позволяют обрезать текст или указать значок многоточия для поведение усечения текста. Обрезки предпочтительнее многоточие как многоточие требуют дополнительного места сами.

> [!NOTE]
> Если требуется обрезать текст, обрезать конца строки, а не в начале.

В этом примере показано, как текст в TextBlock с использованием [TextTrimming](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.textblock.texttrimming) свойство.

``` xaml
<TextBlock TextTrimming="Clip">
    The quick brown fox jumped over the lazy dog.
</TextBlock>
```

![Текст, масштабирование 100 – 225% с обрезки текста](images/coretext/text-scaling-clipping-small.png)  
*Текст, масштабирование с использованием обрезки текста*

### <a name="use-a-tooltip"></a>Использовать компонент tooltip

Если вы обрезки текста, используйте компонент tooltip для предоставления пользователям полный текст.

Здесь мы добавить подсказку в блок текста, который не поддерживает обтекание текстом:

``` xaml
<TextBlock TextTrimming="Clip">
    <ToolTipService.ToolTip>
        <ToolTip Content="The quick brown fox jumped over the lazy dog."/>
    </ToolTipService.ToolTip>
    The quick brown fox jumped over the lazy dog.
</TextBlock>
```

### <a name="dont-scale-font-based-icons-or-symbols"></a>Не масштабируются на основе шрифта значки или символы

При использовании значков на основе шрифта для выделения, оформление, отключение масштабирования на эти символы.

Задайте [IsTextScaleFactorEnabled](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.control.istextscalefactorenabled) свойства `false` большинство XAML управляет.

### <a name="support-text-scaling-natively"></a>Допускают текстовые, масштабирование в собственном коде

Обрабатывать [TextScaleFactorChanged](https://docs.microsoft.com/uwp/api/windows.ui.viewmanagement.uisettings.textscalefactorchanged) UISettings системных событий в настраиваемые framework и элементов управления. Это событие возникает каждый раз, когда пользователь задает коэффициент масштабирования текста в системе.

## <a name="summary"></a>Сводка

В этом разделе представлен обзор масштабирования в Windows текста и включает руководство UX и разработчиков о том, как настроить взаимодействие с пользователем.

## <a name="related-articles"></a>Связанные статьи

### <a name="api-reference"></a>Справочник по API

- [IsTextScaleFactorEnabled](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.control.istextscalefactorenabled)
- [TextScaleFactorChanged](https://docs.microsoft.com/uwp/api/windows.ui.viewmanagement.uisettings.textscalefactorchanged)
