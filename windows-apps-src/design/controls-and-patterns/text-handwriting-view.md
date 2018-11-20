---
author: Karl-Bridge-Microsoft
Description: Customize the built-in handwriting view for ink to text input that is supported by UWP text controls such as the TextBox, RichEditBox (and controls like the AutoSuggestBox that provide a similar text input experience).
title: Ввод текста с представлением рукописного ввода
label: Text input with the handwriting view
template: detail.hbs
ms.author: kbridge
ms.date: 10/13/18
ms.topic: article
keywords: windows 10, uwp
pm-contact: sewen
design-contact: minah.kim
doc-status: Draft
ms.localizationpriority: medium
ms.custom: RS5
ms.openlocfilehash: dd0031affaf5ae48553479660d91ae610e7f1acb
ms.sourcegitcommit: ed0304b8a214c03b8aab74b8ef12c9f82b8e3c5f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/19/2018
ms.locfileid: "7291283"
---
# <a name="text-input-with-the-handwriting-view"></a>Ввод текста с представлением рукописного ввода

![Текстовое поле разворачивается при касании пером](images/handwritingview/handwritingview2.gif)

Настройка представления встроенных рукописного ввода для рукописного ввода для ввода текста, поддерживаемых элементов управления текстом UWP, таких как [TextBox](https://docs.microsoft.com/en-us/uwp/api/windows.ui.xaml.controls.textbox), [RichEditBox](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.richeditbox), и элементы управления, производные от этих например [AutoSuggestBox](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.autosuggestbox).

## <a name="overview"></a>Обзор

Поля для ввода текста XAML функции реализована поддержка ввод с помощью [Windows Ink](../input/pen-and-stylus-interactions.md)с помощью пера. Когда пользователь касается в поле текстового ввода с помощью пера Windows, текстовое поле преобразуется в поверхности рукописного ввода, а не в отдельной панели ввода.

Текст распознается, когда пользователь пишет в любом месте в текстовое поле и кандидат, что окна отображаются результаты распознавания. Пользователь может коснуться результата, чтобы выбрать его, или продолжить писать, чтобы выбрать предлагаемое слово-кандидат. Буквальные (побуквенные) результаты распознавания добавляются в окно слов-кандидатов, поэтому распознавание не ограничивается словами из словаря. Когда пользователь пишет, текст преобразуется в рукописный шрифт, создавая ощущение естественного письма.

> [!NOTE]
> По умолчанию включена вид рукописного ввода, но вы можете отключить его для каждого элемента управления отдельно и вместо этого вернуться к панели ввода текста.

![Текстовое поле с помощью пера и предложения](images/handwritingview/handwritingview-inksuggestion1.gif)

Пользователь может изменить свой текст, используя стандартные жесты и действия, такие как:

- _зачеркнуть_ или _вычеркнуть_ — провести линию поверх слова, чтобы удалить слово или его часть
- _соединить_ — нарисовать дугу между словами, чтобы удалить пробел между ними
- _вставить_ — нарисовать символ вставки, чтобы вставить пробел
- _перезапись_ — запись поверх существующего текста для его замены

![Текстовое поле с коррекцией рукописного ввода](images/handwritingview/handwritingview-inkcorrection1.gif)

## <a name="disable-the-handwriting-view"></a>Отключить представление рукописного ввода

По умолчанию включена вид встроенных рукописного ввода.

Может потребоваться отключить представление рукописного ввода, если предоставляется эквивалентные функции рукописного ввода в текст в приложении или работы ввода текста зависит от каких-либо форматирования или специальных знаков (например, вкладки) не доступно рукописного ввода.

В этом примере мы отключить представление рукописного ввода, задав для свойства [IsHandwritingViewEnabled](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.textbox.ishandwritingviewenabled) элемента управления [TextBox](https://docs.microsoft.com/en-us/uwp/api/windows.ui.xaml.controls.textbox) значение "false". Все элементы управления текстом, поддерживающие представление рукописного ввода поддержки аналогично свойства.

```xaml
<TextBox Name="SampleTextBox"
    Height="50" Width="500" 
    FontSize="36" FontFamily="Segoe UI" 
    PlaceholderText="Try taping with your pen" 
    IsHandwritingViewEnabled="False">
</TextBox>
```

## <a name="specify-the-alignment-of-the-handwriting-view"></a>Задать выравнивание представления рукописного ввода

Представление рукописного ввода находится над основной текстовый элемент управления и размера для реализации пользовательских настроек рукописного ввода (см. в разделе параметров **-> устройства -> перо и Windows Ink рукописного ввода, "->" -> размер шрифта, при написании непосредственно в текстовое поле **). Представления также автоматически выровнена относительно текстовый элемент управления и его расположение в приложении.

Пользовательского интерфейса приложения не адаптируется для размещения больших элемента управления, система может привести к представление, чтобы загородить важные пользовательского интерфейса.

Здесь мы покажем, как использовать свойство [PlacementAlignment](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.handwritingview.placementalignment) [TextBox](https://docs.microsoft.com/en-us/uwp/api/windows.ui.xaml.controls.textbox) [HandwritingView](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.handwritingview) для указания того, какие привязки на основной текстовый элемент управления используется для выравнивания представление рукописного ввода.

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

## <a name="disable-auto-completion-candidates"></a>Отключите автоматическое заполнение кандидаты

Всплывающее окно вариантов текста включена по умолчанию предоставить кандидаты распознавания, из которых пользователь может выбрать на случай, если основные кандидат неправильный список верхней рукописного ввода.

Если ваше приложение уже предоставляет надежную, функциональные возможности настраиваемого распознавания, воспользуйтесь свойство [AreCandidatesEnabled](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.handwritingview.arecandidatesenabled) отключить встроенную предложения, как показано в следующем примере.

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

## <a name="use-handwriting-font-preferences"></a>Использовать предпочтения шрифта рукописного ввода

Пользователь может выбрать из предопределенных набор шрифтов на основе рукописного ввода для использования при отрисовки текст, в зависимости от распознавания рукописного ввода (см. в разделе **Параметры -> устройства -> перо и Windows Ink рукописного ввода, "->" -> шрифта, при использовании рукописного ввода**).

> [!NOTE]
> Пользователи могут даже создавать шрифтов, в зависимости от собственные рукописного ввода.
> [!VIDEO https://www.youtube.com/embed/YRR4qd4HCw8]

Ваше приложение может получить доступ к этот параметр и выбранный шрифт для распознанный текст в текстовом элементе управления.

В этом примере мы прослушивать событие [TextChanged](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.textbox.textchanged) [TextBox](https://docs.microsoft.com/en-us/uwp/api/windows.ui.xaml.controls.textbox) и применить шрифт выбранного пользователя, если изменение текста, происходят из HandwritingView (или шрифта по умолчанию, если это не так).

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

Составные элементы управления, которые также используют [TextBox](https://docs.microsoft.com/en-us/uwp/api/windows.ui.xaml.controls.textbox) или [RichEditBox](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.richeditbox) элементы управления, такие как [AutoSuggestBox](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.autosuggestbox) поддерживает [HandwritingView](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.handwritingview).

Для доступа к [HandwritingView](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.handwritingview) в составной элемент управления, используйте [VisualTreeHelper](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.visualtreehelper) API.

В следующем фрагменте кода XAML отображает элемент управления [AutoSuggestBox](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.autosuggestbox) .

```xaml
<AutoSuggestBox Name="SampleAutoSuggestBox" 
    Height="50" Width="500" 
    PlaceholderText="Auto Suggest Example" 
    FontSize="16" FontFamily="Segoe UI"  
    Loaded="SampleAutoSuggestBox_Loaded">
</AutoSuggestBox>
```

В соответствующий код программной части мы покажем, как отключить [HandwritingView](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.handwritingview) на [AutoSuggestBox](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.autosuggestbox).

1. Во-первых мы обрабатываем событие Loaded приложения где мы вызываем функцию FindInnerTextBox для запуска обход визуального дерева.

    ```csharp
    private void SampleAutoSuggestBox_Loaded(object sender, RoutedEventArgs e)
    {
        if (FindInnerTextBox((AutoSuggestBox)sender))
            autoSuggestInnerTextBox.IsHandwritingViewEnabled = false;
    }
    ```

2. Затем мы начинаем, выполнить итерацию по визуальному дереву (начиная с AutoSuggestBox) в функцию FindInnerTextBox с помощью вызова FindVisualChildByName.

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

3. И, наконец эта функция повторяет по визуальному дереву, пока извлекается TextBox.

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

## <a name="reposition-the-handwritingview"></a>Изменения положения HandwritingView

В некоторых случаях может потребоваться убедитесь, что [HandwritingView](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.handwritingview) рассматриваются элементов пользовательского интерфейса, в противном случае возможно, он не.

Здесь мы создаем TextBox, который поддерживает диктовки (реализован помещением текстовое поле и кнопку диктовки в StackPanel).

![TextBox с диктовки](images/handwritingview/textbox-with-dictation.png)

По мере StackPanel, теперь больше, чем TextBox [HandwritingView](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.handwritingview) не может загородить все составные cotnrol.

![TextBox с диктовки](images/handwritingview/textbox-with-dictation-handwritingview.png)

Чтобы решить эту, задайте свойство PlacementTarget [HandwritingView](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.handwritingview) элементом пользовательского интерфейса, к которому следует выравнивать.

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

Можно также задать размер [HandwritingView](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.handwritingview), которые могут быть полезны при необходимо убедиться, что представление не загородить важные пользовательского интерфейса.

Как и в предыдущем примере мы создаем TextBox, который поддерживает диктовки (реализован помещением текстовое поле и кнопку диктовки в StackPanel).

![TextBox с диктовки](images/handwritingview/textbox-with-dictation.png)

В этом случае необходимо убедиться, что кнопка диктовки всегда виден.

![TextBox с диктовки](images/handwritingview/textbox-with-dictation-handwritingview-resize.png)

Для этого мы привязываем свойство MaxWidth [HandwritingView](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.handwritingview) по ширине элемента пользовательского интерфейса, он должен загородить.

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

## <a name="reposition-custom-ui"></a>Настраиваемый пользовательский Интерфейс перемещения

Если у вас есть настраиваемый пользовательский Интерфейс, который отображается в ответ на ввод текста, такие как информационные всплывающее окно, может потребоваться изменить положение этого интерфейса, поэтому он не загородить представление рукописного ввода.

![TextBox с помощью пользовательского интерфейса](images/handwritingview/textbox-with-customui.png)

Приведенный ниже показано, как для прослушивания событий [SizeChanged](https://docs.microsoft.com/uwp/api/windows.ui.xaml.frameworkelement.sizechanged) , [Opened](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.handwritingview.opened)и [Closed](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.handwritingview.closed
)из [HandwritingView](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.handwritingview) , чтобы установить положение [всплывающего окна](https://docs.microsoft.com/uwp/api/windows.ui.popups).

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

## <a name="retemplate-the-handwritingview-control"></a>Изменить шаблон элемента управления HandwritingView

Как и все элементы управления платформы XAML, вы можете настроить визуальную структуру и визуальное поведение [HandwritingView](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.handwritingview) для определенных требований.

Чтобы просмотреть полный пример создания пользовательского шаблона Посмотрите инструкции [Создание пользовательских элементов управления транспортировкой](https://docs.microsoft.com/windows/uwp/design/controls-and-patterns/custom-transport-controls) или [Пример пользовательского элемента управления для редактирования](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/CustomEditControl).








