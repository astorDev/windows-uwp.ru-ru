---
Description: Настройка представления встроенных рукописного ввода для рукописного ввода для ввода текста, поддерживается UWP текст такими элементами управления TextBox RichEditBox (и элементы управления, например AutoSuggestBox, которые предоставляют аналогичные возможности ввода текста).
title: Текстовое поле с представлением рукописного ввода
label: Text input with the handwriting view
template: detail.hbs
ms.date: 10/13/2018
ms.topic: article
keywords: windows 10, uwp
pm-contact: sewen
design-contact: minah.kim
doc-status: Draft
ms.localizationpriority: medium
ms.custom: RS5
ms.openlocfilehash: f7b31898e6a90410e4edc73ee36f71a7e4d94155
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57634919"
---
# <a name="text-input-with-the-handwriting-view"></a>Текстовое поле с представлением рукописного ввода

![Текстовое поле разворачивается при касании пером](images/handwritingview/handwritingview2.gif)

Настройка представления встроенных рукописного ввода для рукописного ввода для ввода текста, поддерживаемый текстовых элементов управления универсальной платформы Windows, такие как [TextBox](https://docs.microsoft.com/en-us/uwp/api/windows.ui.xaml.controls.textbox), [RichEditBox](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.richeditbox), и элементы управления, производных от этих, таких как [ AutoSuggestBox](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.autosuggestbox).

## <a name="overview"></a>Обзор

XAML полей ввода признаков встроенную поддержку ввода с помощью пера [рукописного ввода Windows](../input/pen-and-stylus-interactions.md). При касании в текстовое поле с помощью пера Windows, текстовое поле преобразует в поверхность рукописного ввода, не открывая отдельные панели ввода.

Текст распознается как пользователь пишет в любом месте в текстовом поле и потенциальный, что окно отображается в результатах распознавания речи. Пользователь может коснуться результата, чтобы выбрать его, или продолжить писать, чтобы выбрать предлагаемое слово-кандидат. Буквальные (побуквенные) результаты распознавания добавляются в окно слов-кандидатов, поэтому распознавание не ограничивается словами из словаря. Когда пользователь пишет, текст преобразуется в рукописный шрифт, создавая ощущение естественного письма.

> [!NOTE]
> Представление рукописного ввода включен по умолчанию, но можно отключить его на основе элементов управления и вернуться к панели ввода текста, вместо этого.

![Текстовое поле с рукописного ввода и предложения](images/handwritingview/handwritingview-inksuggestion1.gif)

Пользователь может изменить свой текст, используя стандартные жесты и действия, такие как:

- _зачеркнуть_ или _вычеркнуть_ — провести линию поверх слова, чтобы удалить слово или его часть
- _соединить_ — нарисовать дугу между словами, чтобы удалить пробел между ними
- _вставить_ — нарисовать символ вставки, чтобы вставить пробел
- _перезапись_ — запись поверх существующего текста для его замены

![Текстовое поле с коррекцией рукописного ввода](images/handwritingview/handwritingview-inkcorrection1.gif)

## <a name="disable-the-handwriting-view"></a>Отключить представление рукописного ввода

Представление встроенные рукописного ввода включена по умолчанию.

Может потребоваться отключить представление рукописного ввода в том случае, если уже предоставляют функциональные возможности, аналогичные рукописного ввода в текст в приложении, или процесс ввода текста зависит от какого-либо рода форматирования или специальных символов (например, вкладка «») не доступны через рукописного ввода.

В этом примере мы отключим представление рукописного ввода, задав [IsHandwritingViewEnabled](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.textbox.ishandwritingviewenabled) свойство [TextBox](https://docs.microsoft.com/en-us/uwp/api/windows.ui.xaml.controls.textbox) элемента управления значение false. Все элементы управления, которые поддерживают представление рукописного ввода поддерживает аналогичное свойство.

```xaml
<TextBox Name="SampleTextBox"
    Height="50" Width="500" 
    FontSize="36" FontFamily="Segoe UI" 
    PlaceholderText="Try taping with your pen" 
    IsHandwritingViewEnabled="False">
</TextBox>
```

## <a name="specify-the-alignment-of-the-handwriting-view"></a>Задать выравнивание представления рукописного ввода

Представление рукописного ввода располагается над основной текстовый элемент управления и изменяются, чтобы вместить настройки рукописного ввода пользователя (см. в разделе **устройства на "->" Параметры "->" пера & рукописного ввода Windows -> рукописного ввода "->" размер шрифта при записи непосредственно в текстовое поле**). Представление также автоматически выравнивается относительно текстового элемента управления и его расположение в приложении.

Пользовательский Интерфейс приложения не обратное течение для размещения большего размера элемента управления, поэтому система может привести к представлении, чтобы скрывать важные пользовательского интерфейса.

Здесь мы покажем, как использовать [PlacementAlignment](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.handwritingview.placementalignment) свойство [TextBox](https://docs.microsoft.com/en-us/uwp/api/windows.ui.xaml.controls.textbox) [HandwritingView](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.handwritingview) для указания, какие привязки на основной текстовый элемент управления используется для выравнивания представление рукописного ввода.

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

## <a name="disable-auto-completion-candidates"></a>Отключить автоматическое завершение кандидатов

Всплывающее окно подсказки текста включена по умолчанию для обеспечения распознавания кандидатов, из которых пользователь может выбрать, если неверный Топ список верхнего рукописного ввода.

Если приложение уже предоставляет функциональные возможности распознавания надежными, пользовательские, можно использовать [AreCandidatesEnabled](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.handwritingview.arecandidatesenabled) свойство, позволяющее отключить встроенные предложения, как показано в следующем примере.

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

## <a name="use-handwriting-font-preferences"></a>Использовать параметры шрифта рукописного ввода

Пользователь может выбрать из предварительно определенную коллекцию на основе рукописного ввода шрифтов, используемое для отрисовки текста, в зависимости от распознавание рукописного ввода (см. в разделе **устройства на "->" Параметры "->" пера & рукописного ввода Windows -> рукописного ввода "->" шрифта при использовании рукописного ввода**).

> [!NOTE]
> Пользователи могут даже создавать шрифта, в зависимости от руки.
> [!VIDEO https://www.youtube.com/embed/YRR4qd4HCw8]

Приложения можно откройте этот параметр и используйте выбранный шрифт для распознанного текста в элементе управления текст.

В этом примере мы прослушивать [TextChanged](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.textbox.textchanged) событие [TextBox](https://docs.microsoft.com/en-us/uwp/api/windows.ui.xaml.controls.textbox) и применить пользователя выбранный шрифт, если произошло изменение текста из HandwritingView (или шрифт по умолчанию, если это не так).

```csharp
private void SampleTextBox_TextChanged(object sender, TextChangedEventArgs e)
{
    ((TextBox)sender).FontFamily = 
        ((TextBox)sender).HandwritingView.IsOpen ?
            new FontFamily(PenAndInkSettings.GetDefault().FontFamilyName) : 
            new FontFamily("Segoe UI");
}
```

## <a name="access-the-handwritingview-in-composite-controls"></a>Доступ к HandwritingView в составных элементов управления

Составных элементов управления, использующих [TextBox](https://docs.microsoft.com/en-us/uwp/api/windows.ui.xaml.controls.textbox) или [RichEditBox](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.richeditbox) элементы управления, такие как [AutoSuggestBox](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.autosuggestbox) также поддерживают [HandwritingView](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.handwritingview).

Чтобы получить доступ к [HandwritingView](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.handwritingview) в составной элемент управления, использовать [VisualTreeHelper](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.visualtreehelper) API.

В следующем фрагменте XAML отображает [AutoSuggestBox](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.autosuggestbox) элемента управления.

```xaml
<AutoSuggestBox Name="SampleAutoSuggestBox" 
    Height="50" Width="500" 
    PlaceholderText="Auto Suggest Example" 
    FontSize="16" FontFamily="Segoe UI"  
    Loaded="SampleAutoSuggestBox_Loaded">
</AutoSuggestBox>
```

В соответствующий код программной части, мы покажем, как отключить [HandwritingView](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.handwritingview) на [AutoSuggestBox](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.autosuggestbox).

1. Во-первых мы обрабатывать события Loaded приложения где мы вызываем функцию FindInnerTextBox для запуска прохождения визуального дерева.

    ```csharp
    private void SampleAutoSuggestBox_Loaded(object sender, RoutedEventArgs e)
    {
        if (FindInnerTextBox((AutoSuggestBox)sender))
            autoSuggestInnerTextBox.IsHandwritingViewEnabled = false;
    }
    ```

2. Затем мы начинаем, прохода визуального дерева (начиная с AutoSuggestBox) в функции FindInnerTextBox вызовом FindVisualChildByName.

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

3. И, наконец эта функция выполняет итерацию по визуальному дереву пока не будет получено текстового поля.

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

## <a name="reposition-the-handwritingview"></a>Изменить положение HandwritingView

В некоторых случаях может потребоваться убедиться, что [HandwritingView](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.handwritingview) описываются элементы пользовательского интерфейса, в противном случае может оказаться.

Здесь мы создадим текстовое поле, которое поддерживает режим диктовки (реализовано путем помещения в StackPanel текстовое поле и кнопку речевые сообщения).

![TextBox с диктовки](images/handwritingview/textbox-with-dictation.png)

Как StackPanel превосходит TextBox [HandwritingView](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.handwritingview) не может скрывать все составные cotnrol.

![TextBox с диктовки](images/handwritingview/textbox-with-dictation-handwritingview.png)

Чтобы решить эту проблему, установите свойство PlacementTarget [HandwritingView](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.handwritingview) к элементу пользовательского интерфейса, к которому он должны быть выровнены.

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

Можно также задать размер [HandwritingView](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.handwritingview), который может быть полезно при необходимо убедиться в представлении не скрывать важные пользовательского интерфейса.

Как и в предыдущем примере мы создадим текстовое поле, которое поддерживает режим диктовки (реализовано путем помещения в StackPanel текстовое поле и кнопку речевые сообщения).

![TextBox с диктовки](images/handwritingview/textbox-with-dictation.png)

В этом случае мы стремимся обеспечить диктовки кнопки всегда является видимым.

![TextBox с диктовки](images/handwritingview/textbox-with-dictation-handwritingview-resize.png)

Чтобы сделать это, мы привязать свойство MaxWidth [HandwritingView](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.handwritingview) по ширине элемента пользовательского интерфейса, его следует скрывать.

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
                PlacementTarget="{Binding ElementName=DictationBox}“
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

## <a name="reposition-custom-ui"></a>Изменить положение пользовательского интерфейса

Если у вас есть пользовательский Интерфейс, который отображается в ответ на ввод текста, такие как информационное всплывающее окно, может потребоваться изменить положение пользовательский, поэтому он не скрывать представление рукописного ввода.

![Текстовое поле с помощью пользовательского интерфейса](images/handwritingview/textbox-with-customui.png)

В следующем примере показано, как для прослушивания [Opened](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.handwritingview.opened), [закрыто](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.handwritingview.closed
), и [SizeChanged](https://docs.microsoft.com/uwp/api/windows.ui.xaml.frameworkelement.sizechanged) события [HandwritingView](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.handwritingview) для задания положение элемента [всплывающее окно](https://docs.microsoft.com/uwp/api/windows.ui.popups).

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

## <a name="retemplate-the-handwritingview-control"></a>Retemplate HandwritingView элемент управления

Так как все элементы управления XAML framework позволяет настраивать визуальную структуру и визуальное поведение элемента [HandwritingView](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.handwritingview) собственными потребностями.

Чтобы просмотреть полный пример создания пользовательского шаблона извлечения [создавать элементы управления пользовательского транспорта](https://docs.microsoft.com/windows/uwp/design/controls-and-patterns/custom-transport-controls) руководства или [пример пользовательского элемента управления Правка](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/CustomEditControl).








