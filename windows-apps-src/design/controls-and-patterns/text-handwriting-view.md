---
Description: Настройте встроенный рукописный ввод для представления рукописного ввода, который поддерживается текстовыми элементами управления UWP, такими как TextBox, RichEditBox (и элементами управления, такими как AutoSuggestBox, которые обеспечивают аналогичные возможности ввода текста).
title: Ввод текста в представлении рукописного ввода
label: Text input with the handwriting view
template: detail.hbs
ms.date: 10/13/2018
ms.topic: article
keywords: windows 10, uwp
pm-contact: sewen
design-contact: minah.kim
doc-status: Draft
ms.localizationpriority: medium
ms.custom: RS5
ms.openlocfilehash: 9b9d409718a157c55b28fdb3ccaa28caaa295adf
ms.sourcegitcommit: 26bb75084b9d2d2b4a76d4aa131066e8da716679
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/06/2020
ms.locfileid: "75684285"
---
# <a name="text-input-with-the-handwriting-view"></a>Ввод текста в представлении рукописного ввода

![Текстовое поле развертывается при касании пером](images/handwritingview/handwritingview2.gif)

Настройте встроенное представление рукописного ввода для распознавания введенного текста, которое поддерживается текстовыми элементами управления UWP, такими как [TextBox](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.textbox), [RichEditBox](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.richeditbox) и элементами управления, производными от них, такими как [ AutoSuggestBox](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.autosuggestbox).

## <a name="overview"></a>Обзор

Поля ввода текста XAML имеют встроенную поддержку рукописного ввода с использованием [Windows Ink](../input/pen-and-stylus-interactions.md). При касании текстового поля пером Windows текстовое поле превращается в поверхность для рукописного ввода, а не открывается отдельная панель ввода.

Текст распознается, когда пользователь пишет в любом месте текстового поля, а в окнах подбора слов-кандидатов отображаются результаты распознавания. Пользователь может коснуться результата, чтобы выбрать его, или продолжить писать, чтобы выбрать предлагаемое слово-кандидат. Буквальные (побуквенные) результаты распознавания добавляются в окно слов-кандидатов, поэтому распознавание не ограничивается словами из словаря. По мере того, как пользователь пишет, текст преобразуется в рукописный шрифт, создавая ощущение естественного письма.

> [!NOTE]
> Представление рукописного ввода включено по умолчанию, но его можно отключить отдельно для каждого элемента управления и вместо него вернуться к использованию панели ввода текста.

![Текстовое поле с рукописным вводом и предложениями](images/handwritingview/handwritingview-inksuggestion1.gif)

Пользователь может изменить свой текст, используя стандартные жесты и действия:

- _зачеркнуть_ или _вычеркнуть_ — провести линию поверх слова, чтобы удалить слово или его часть;
- _соединить_ — нарисовать дугу между словами, чтобы удалить пробел между ними;
- _вставить_ — нарисовать символ вставки, чтобы вставить пробел;
- _перезапись_ — запись поверх существующего текста для его замены.

![Текстовое поле с коррекцией рукописного ввода](images/handwritingview/handwritingview-inkcorrection1.gif)

## <a name="disable-the-handwriting-view"></a>Отключение представления рукописного ввода

Встроенное представление рукописного ввода по умолчанию включено.

Представление рукописного ввода, если вы уже предоставляете эквивалентную функциональность рукописного ввода в своем приложении, или процесс ввода текста зависит от какого-либо рода форматирования или специальных символов (например, "Tab"), недоступных с помощью рукописного ввода, может потребоваться отключить.

В этом примере мы отключим представление рукописного ввода, задав для свойство [IsHandwritingViewEnabled](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.textbox.ishandwritingviewenabled) элемента управления [TextBox](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.textbox) значение "false". Все элементы управления, которые поддерживают представление рукописного ввода, поддерживают аналогичное свойство.

```xaml
<TextBox Name="SampleTextBox"
    Height="50" Width="500" 
    FontSize="36" FontFamily="Segoe UI" 
    PlaceholderText="Try taping with your pen" 
    IsHandwritingViewEnabled="False">
</TextBox>
```

## <a name="specify-the-alignment-of-the-handwriting-view"></a>Определение выравнивания для представления рукописного ввода

Представление рукописного ввода располагается над основным текстовым элементом управления и его размер изменяется, чтобы вместить настройки рукописного ввода пользователя (см. **Настройки -> Устройства -> Перо и Windows Ink -> Рукописный ввод -> Размер шрифта при записи непосредственно в текстовом поле**). Представление также автоматически выравнивается относительно текстового элемента управления и его расположения в приложении.

Пользовательский интерфейс приложения не обновляется, чтобы вместить больший элемент управления, поэтому система может вынудить представление перекрыть важный элемент пользовательского интерфейса.

Здесь мы покажем, как использовать свойство [PlacementAlignment](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.handwritingview.placementalignment) элемента управления [TextBox](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.textbox)[HandwritingView](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.handwritingview), чтобы указать, какая привязка в базовом текстовом элементе управления используется для выравнивания представления рукописного ввода.

```xaml
<TextBox Name="SampleTextBox"
    Height="50" Width="500" 
    FontSize="36" FontFamily="Segoe UI" 
    PlaceholderText="Try taping with your pen">
        <TextBox.HandwritingView>
            <HandwritingView PlacementAlignment="TopLeft"/>
        </TextBox.HandwritingView>
</TextBox>
```

## <a name="disable-auto-completion-candidates"></a>Отключение автоматического завершения слов-кандидатов

Всплывающее окно с предложениями текста включено по умолчанию, чтобы предоставить список лучших слов-кандидатов для распознавания, из которых пользователь может выбрать нужное слово, если главный кандидат не подходит.

Если ваше приложение уже предоставляет надежные, настраиваемые функции распознавания, вы можете использовать свойство [AreCandidatesEnabled](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.handwritingview.arecandidatesenabled), позволяющее отключить встроенные предложения, как показано в следующем примере.

```xaml
<TextBox Name="SampleTextBox"
    Height="50" Width="500" 
    FontSize="36" FontFamily="Segoe UI" 
    PlaceholderText="Try taping with your pen">
        <TextBox.HandwritingView>
            <HandwritingView AreCandidatesEnabled="False"/>
        </TextBox.HandwritingView>
</TextBox>
```

## <a name="use-handwriting-font-preferences"></a>Использование настроек рукописного шрифта

Пользователь может выбрать шрифт из заранее определенной коллекции рукописных шрифтов, который будет использоваться для отображения текста при распознавании рукописного ввода (см. **Настройки -> Устройства ->Перо и Windows Ink -> Рукописный ввод -> Шрифт при использовании рукописного ввода**).

> [!NOTE]
> Пользователи могут даже создать шрифт, основанный на их собственном почерке.
> [!VIDEO https://www.youtube.com/embed/YRR4qd4HCw8]

Ваше приложение может получить доступ к этому параметру и использовать выбранный шрифт для распознанного текста в текстовом элементе управления.

В этом примере мы ожидаем событие [TextChanged](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.textbox.textchanged) элемента управления [TextBox](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.textbox) и применяем выбранный пользователем шрифт, если изменение текста происходит из HandwritingView (или шрифт по умолчанию, если шрифт не выбран).

```csharp
private void SampleTextBox_TextChanged(object sender, TextChangedEventArgs e)
{
    ((TextBox)sender).FontFamily = 
        ((TextBox)sender).HandwritingView.IsOpen ?
            new FontFamily(PenAndInkSettings.GetDefault().FontFamilyName) : 
            new FontFamily("Segoe UI");
}
```

## <a name="access-the-handwritingview-in-composite-controls"></a>Доступ к HandwritingView в составных элементах управления

Составные элементы управления, которые используют элементы управления [TextBox](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.textbox) или [RichEditBox](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.richeditbox), такие как [AutoSuggestBox](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.autosuggestbox), также поддерживают [HandwritingView](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.handwritingview).

Чтобы получить доступ к [HandwritingView](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.handwritingview) в составном элементе управления, используйте API [VisualTreeHelper](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.visualtreehelper).

В следующем фрагменте XAML отображается элемент управления [AutoSuggestBox](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.autosuggestbox).

```xaml
<AutoSuggestBox Name="SampleAutoSuggestBox" 
    Height="50" Width="500" 
    PlaceholderText="Auto Suggest Example" 
    FontSize="16" FontFamily="Segoe UI"  
    Loaded="SampleAutoSuggestBox_Loaded">
</AutoSuggestBox>
```

В соответствующем фрагменте кода мы покажем, как отключить [HandwritingView](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.handwritingview) в [AutoSuggestBox](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.autosuggestbox).

1. Сначала мы обрабатываем событие Loaded приложения, в котором мы вызываем функцию FindInnerTextBox для запуска обхода визуального дерева.

    ```csharp
    private void SampleAutoSuggestBox_Loaded(object sender, RoutedEventArgs e)
    {
        if (FindInnerTextBox((AutoSuggestBox)sender))
            autoSuggestInnerTextBox.IsHandwritingViewEnabled = false;
    }
    ```

2. Затем мы начинаем итерацию по визуальному дереву (начиная с AutoSuggestBox) в функции FindInnerTextBox с помощью вызова FindVisualChildByName.

    ```csharp
    private bool FindInnerTextBox(AutoSuggestBox autoSuggestBox)
    {
        if (autoSuggestInnerTextBox == null)
        {
            // Cache textbox to avoid multiple tree traversals. 
            autoSuggestInnerTextBox = 
                (TextBox)FindVisualChildByName<TextBox>(autoSuggestBox);
        }
        return (autoSuggestInnerTextBox != null);
    }
    ```

3. Наконец, эта функция перебирает визуальное дерево до тех пор, пока не будет получен TextBox.

    ```csharp
    private FrameworkElement FindVisualChildByName<T>(DependencyObject obj)
    {
        FrameworkElement element = null;
        int childrenCount = 
            VisualTreeHelper.GetChildrenCount(obj);
        for (int i = 0; (i < childrenCount) && (element == null); i++)
        {
            FrameworkElement child = 
                (FrameworkElement)VisualTreeHelper.GetChild(obj, i);
            if ((child.GetType()).Equals(typeof(T)) || (child.GetType().GetTypeInfo().IsSubclassOf(typeof(T))))
            {
                element = child;
            }
            else
            {
                element = FindVisualChildByName<T>(child);
            }
        }
        return (element);
    }
    ```

## <a name="reposition-the-handwritingview"></a>Изменение положения HandwritingView

В некоторых случаях вам необходимо убедиться, что [HandwritingView](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.handwritingview) содержит элементы пользовательского интерфейса, которых в противном случае могло и не быть.

Здесь мы создаем элемент TextBox, который поддерживает диктовку (реализуется путем помещения TextBox и кнопки диктовки в StackPanel).

![TextBox с диктовкой](images/handwritingview/textbox-with-dictation.png)

Поскольку StackPanel теперь больше, чем TextBox, [HandwritingView](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.handwritingview) может не перекрывать весь составной элемент управления.

![TextBox с диктовкой](images/handwritingview/textbox-with-dictation-handwritingview.png)

Чтобы решить эту проблему, установите для свойства PlacementTarget [HandwritingView](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.handwritingview) значение элемента пользовательского интерфейса, по которому он должен быть выровнен.

```xaml
<StackPanel Name="DictationBox" 
    Orientation="Horizontal" 
    VerticalAlignment="Top" 
    HorizontalAlignment="Left" 
    BorderThickness="1" BorderBrush="DarkGray" 
    Height="55" Width="500" Margin="50">
    <TextBox Name="DictationTextBox" 
        Width="450" BorderThickness="0" 
        FontSize="24" VerticalAlignment="Center">
        <TextBox.HandwritingView>
            <HandwritingView PlacementTarget="{Binding ElementName=DictationBox}"/>
        </TextBox.HandwritingView>
    </TextBox>
    <Button Name="DictationButton" 
        Height="48" Width="48" 
        FontSize="24" 
        FontFamily="Segoe MDL2 Assets" 
        Content="&#xE720;" 
        Background="White" Foreground="DarkGray"     Tapped="DictationButton_Tapped" />
</StackPanel>
```

## <a name="resize-the-handwritingview"></a>Изменение размера HandwritingView

Вы также можете задать размер [HandwritingView](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.handwritingview), что может быть полезно, когда вам нужно убедиться, что представление не перекрывает важный элемент пользовательского интерфейса.

Как и в предыдущем примере, мы создаем TextBox, который поддерживает диктовку (реализуется путем помещения TextBox и кнопки диктовки в StackPanel).

![TextBox с диктовкой](images/handwritingview/textbox-with-dictation.png)

В этом случае мы хотим убедиться, что кнопка диктовки всегда видна.

![TextBox с диктовкой](images/handwritingview/textbox-with-dictation-handwritingview-resize.png)

Для этого мы привязываем свойство MaxWidth [HandwritingView](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.handwritingview) к ширине элемента пользовательского интерфейса, который он закрывает.

```xaml
<StackPanel Name="DictationBox" 
    Orientation="Horizontal" 
    VerticalAlignment="Top" 
    HorizontalAlignment="Left" 
    BorderThickness="1" 
    BorderBrush="DarkGray" 
    Height="55" Width="500" 
    Margin="50">
    <TextBox Name="DictationTextBox" 
        Width="450" 
        BorderThickness="0" 
        FontSize="24" 
        VerticalAlignment="Center">
        <TextBox.HandwritingView>
            <HandwritingView 
                PlacementTarget="{Binding ElementName=DictationBox}"
                MaxWidth="{Binding ElementName=DictationTextBox, Path=Width"/>
        </TextBox.HandwritingView>
    </TextBox>
    <Button Name="DictationButton" 
        Height="48" Width="48" 
        FontSize="24" 
        FontFamily="Segoe MDL2 Assets" 
        Content="&#xE720;" 
        Background="White" Foreground="DarkGray" 
        Tapped="DictationButton_Tapped" />
</StackPanel>
```

## <a name="reposition-custom-ui"></a>Изменение положения пользовательского элемента интерфейса

Если у вас есть пользовательский элемент интерфейса, который появляется в ответ на ввод текста, например, информационное всплывающее окно, вам может потребоваться изменить положение этого элемента интерфейса, чтобы он не перекрывал представление рукописного ввода.

![TextBox с пользовательским элементом интерфейса](images/handwritingview/textbox-with-customui.png)

В следующем примере показано, как прослушивать события [Opened](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.handwritingview.opened), [Closed](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.handwritingview.closed) и [SizeChanged](https://docs.microsoft.com/uwp/api/windows.ui.xaml.frameworkelement.sizechanged)[HandwritingView](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.handwritingview), чтобы задать положение [Popup](https://docs.microsoft.com/uwp/api/windows.ui.popups).

```csharp
private void Search_HandwritingViewOpened(
    HandwritingView sender, HandwritingPanelOpenedEventArgs args)
{
    UpdatePopupPositionForHandwritingView();
}

private void Search_HandwritingViewClosed(
    HandwritingView sender, HandwritingPanelClosedEventArgs args)
{
    UpdatePopupPositionForHandwritingView();
}

private void Search_HandwritingViewSizeChanged(
    object sender, SizeChangedEventArgs e)
{
    UpdatePopupPositionForHandwritingView();
}

private void UpdatePopupPositionForHandwritingView()
{
if (CustomSuggestionUI.IsOpen)
    CustomSuggestionUI.VerticalOffset = GetPopupVerticalOffset();
}

private double GetPopupVerticalOffset()
{
    if (SearchTextBox.HandwritingView.IsOpen)
        return (SearchTextBox.Margin.Top + SearchTextBox.HandwritingView.ActualHeight);
    else
        return (SearchTextBox.Margin.Top + SearchTextBox.ActualHeight);    
}
```

## <a name="retemplate-the-handwritingview-control"></a>Замена шаблона элемента управления HandwritingView

Как и во всех элементах управления XAML, вы можете настроить как визуальную структуру, так и визуальное поведение элемента [HandwritingView](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.handwritingview) для ваших конкретных требований.

Чтобы увидеть полный пример создания пользовательского шаблона с практическими рекомендациями, см. статьи [Create custom transport controls](https://docs.microsoft.com/windows/uwp/design/controls-and-patterns/custom-transport-controls) (Создание пользовательских элементов управления транспортировкой) и [Custom Edit Control sample](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/CustomEditControl) (Пример пользовательского элемента управления для редактирования текста).








