---
Description: Создание приложений Windows и пользовательских или шаблонных элементов управления, поддерживающих масштабирование текста платформы.
title: Масштабирование текста
label: Text scaling
template: detail.hbs
keywords: UWP, текст, масштабирование, Специальные возможности, "Простота доступа", отображение, "увеличить текст", взаимодействие с пользователем, ввод
ms.date: 08/02/2018
ms.topic: article
ms.localizationpriority: medium
ms.custom: RS5
ms.openlocfilehash: 4db3af0d2ec0ce1dbd0866f569ad9bf9b0392aa8
ms.sourcegitcommit: 0dee502484df798a0595ac1fe7fb7d0f5a982821
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/08/2020
ms.locfileid: "82970569"
---
# <a name="text-scaling"></a>Масштабирование текста

![Пример масштабирования текста 100% до 225%](images/coretext/text-scaling-news-hero-small.png)  
*Пример масштабирования текста в Windows 10 (100% до 225%)*

## <a name="overview"></a>Обзор

Чтение текста на экране компьютера (с переносного устройства на портативный компьютер на настольный монитор до огромного экрана Surface Hub) может быть трудной задачей для многих пользователей. И наоборот, некоторые пользователи находят размеры шрифтов, используемые в приложениях и веб-сайтах, больше, чем требуется.

Чтобы обеспечить максимально удобочитаемость текста для широкого спектра пользователей, Windows предоставляет пользователям возможность изменять относительный размер шрифта как для операционной системы, так и для отдельных приложений. Вместо использования приложения "Экранная лупа" (которое обычно просто увеличивает все в пределах области экрана и создает собственные проблемы с удобством использования), изменения разрешения экрана или использования масштабирования (которое изменяет размер всего на основе дисплея и типичного расстояния просмотра) пользователь может быстро получить доступ к настройке для изменения размера только текста в диапазоне от 100 % (размер по умолчанию) до 225 %.

## <a name="support"></a>Поддержка

Универсальные приложения Windows (как Standard, так и PWA) поддерживают масштабирование текста по умолчанию.

Если приложение Windows содержит пользовательские элементы управления, пользовательские текстовые области, жестко закодированные контрольные значения, более старые платформы или сторонние платформы, вам, скорее всего, придется внести некоторые изменения, чтобы обеспечить единообразное и полезное взаимодействие с пользователями.  

DirectWrite, GDI и XAML Свапчаинпанелс изначально не поддерживают масштабирование текста, а поддержка Win32 ограничена меню, значками и панелями инструментов.  

<!-- If you want to support text scaling in your application with these frameworks, you’ll need to support the text scaling change event outlined below and provide alternative sizes for your UI and content.   -->

## <a name="user-experience"></a>Взаимодействие с пользователем

Пользователи могут настроить масштабирование текста с помощью ползунка увеличить размер текста на странице Параметры, > простоту доступа > экране или экрана.

![Пример масштабирования текста 100% до 225%](images/coretext/text-scaling-settings-100-small.png)  
*Параметр масштабирования текста из параметров — > простоту в доступе > экрана и отображения*

## <a name="ux-guidance"></a>Руководство по проектированию взаимодействия с пользователем

При изменении размера текста элементы управления и контейнеры также должны изменять размер и перекомпоновку для размещения текста и его нового макета. Как упоминалось ранее, в зависимости от приложения, платформы и платформы, большая часть этой работы выполняется за вас. В следующих руководствах по UX рассматриваются случаи, когда это не так.

### <a name="use-the-platform-controls"></a>Использование элементов управления платформой

Мы сказали, что это уже так? Это стоит повторять: по возможности всегда используйте встроенные элементы управления, предоставляемые с различными платформами приложений Windows, чтобы получить наиболее полный пользовательский интерфейс для минимальных усилий.

Например, все текстовые элементы управления UWP поддерживают возможности полнотекстового масштабирования без каких бы то ни было настроек или шаблонов.

Ниже приведен фрагмент кода из базового приложения UWP, включающего несколько стандартных элементов управления "текст":

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

![Масштабирование текста с 100% на 225%](images/coretext/text-scaling.gif)  
*Масштабирование анимированного текста*

### <a name="use-auto-sizing"></a>Использовать Автоподбор размера

Не указывайте абсолютные размеры элементов управления. По возможности позвольте платформе изменять размер элементов управления автоматически на основе параметров пользователя и устройства.  

В этом фрагменте кода из предыдущего примера мы используем значения `Auto` ширины `*` и для набора столбцов сетки и предоставляем платформу для настройки макета приложения на основе размера элементов, содержащихся в сетке.

``` xaml
<Grid.ColumnDefinitions>
    <ColumnDefinition Width="Auto"/>
    <ColumnDefinition Width="*"/>
    <ColumnDefinition Width="Auto"/>
</Grid.ColumnDefinitions>
```

### <a name="use-text-wrapping"></a>Использовать перенос текста

Чтобы макет приложения был максимально гибким и адаптируемым, включите перенос текста в любой элемент управления, содержащий текст (многие элементы управления не поддерживают перенос текста по умолчанию).

Если не указать перенос текста, платформа использует другие методы для настройки макета, включая обрезку (см. предыдущий пример).

Здесь мы используем свойства текстового `AcceptsReturn` поля `TextWrapping` и, чтобы обеспечить максимально гибкую структуру макета.

``` xaml
<TextBox PlaceholderText="Type something here" 
          AcceptsReturn="True" TextWrapping="Wrap" />
```

![Масштабирование текста с 100% на 225% с переносом текста](images/coretext/text-scaling-textwrap.gif)  
*Масштабирование текста с переносом текста*

### <a name="specify-text-trimming-behavior"></a>Определение поведения обрезки текста

Если перенос текста не является предпочтительным, большинство текстовых элементов управления позволяют либо обрезать текст, либо задать многоточие для поведения обрезки текста. Обрезка является предпочтительным для эллипсов, так как эллипсы занимают место.

> [!NOTE]
> Если нужно обрезать текст, обрежьте конец строки, а не начало.

В этом примере показано, как вырезать текст в TextBlock с помощью свойства [TextTrimming](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.textblock.texttrimming) .

``` xaml
<TextBlock TextTrimming="Clip">
    The quick brown fox jumped over the lazy dog.
</TextBlock>
```

![Масштабирование текста 100% на 225% с вырезанием текста](images/coretext/text-scaling-clipping-small.png)  
*Масштабирование текста с вырезанием текста*

### <a name="use-a-tooltip"></a>Использование подсказки

При обрезке текста используйте подсказку, чтобы предоставить пользователям полный текст.

Здесь мы добавим подсказку к TextBlock, которая не поддерживает перенос текста:

``` xaml
<TextBlock TextTrimming="Clip">
    <ToolTipService.ToolTip>
        <ToolTip Content="The quick brown fox jumped over the lazy dog."/>
    </ToolTipService.ToolTip>
    The quick brown fox jumped over the lazy dog.
</TextBlock>
```

### <a name="dont-scale-font-based-icons-or-symbols"></a>Не масштабировать значки или символы на основе шрифта

При использовании значков на основе шрифта для выделения или оформления отключите масштабирование этих символов.

Задайте `false` для свойства [истекстскалефакторенаблед](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.control.istextscalefactorenabled) значение для большинства элементов управления XAML.

### <a name="support-text-scaling-natively"></a>Встроенная поддержка масштабирования текста

Обработайте системное событие [текстскалефакторчанжед](https://docs.microsoft.com/uwp/api/windows.ui.viewmanagement.uisettings.textscalefactorchanged) уисеттингс в пользовательской инфраструктуре и элементах управления. Это событие возникает каждый раз, когда пользователь устанавливает коэффициент масштабирования текста в своей системе.

## <a name="summary"></a>Сводка

В этом разделе приводятся общие сведения о поддержке масштабирования текста в Windows, а также рекомендации по настройке интерфейса пользователя и разработчика.

## <a name="related-articles"></a>Похожие статьи

### <a name="api-reference"></a>Справочник по API

- [истекстскалефакторенаблед](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.control.istextscalefactorenabled)
- [текстскалефакторчанжед](https://docs.microsoft.com/uwp/api/windows.ui.viewmanagement.uisettings.textscalefactorchanged)
