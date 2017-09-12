---
author: Jwmsft
Description: "Кнопка предоставляет пользователю возможность вызвать немедленное действие."
title: "Кнопки"
label: Buttons
template: detail.hbs
ms.author: jimwalk
ms.date: 05/19/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, uwp
ms.assetid: f04d1a3c-7dcd-4bc8-9586-3396923b312e
pm-contact: kisai
design-contact: kimsea
dev-contact: mitra
doc-status: Published
ms.openlocfilehash: 2cb15d21496c080002411849682278df7f927e41
ms.sourcegitcommit: 10d6736a0827fe813c3c6e8d26d67b20ff110f6c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/22/2017
---
# <a name="buttons"></a>Кнопки
<link rel="stylesheet" href="https://az835927.vo.msecnd.net/sites/uwp/Resources/css/custom.css">

Кнопка предоставляет пользователю возможность вызвать немедленное действие.

> **Важные API-интерфейсы**: [класс Button](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.button.aspx), [класс RepeatButton](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.primitives.repeatbutton.aspx), [событие Click](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.primitives.buttonbase.click.aspx)

![Примеры кнопок](images/controls/button.png)

## <a name="is-this-the-right-control"></a>Выбор правильного элемента управления

Кнопка позволяет пользователю немедленно начать действие, например отправить форму.

Не используйте кнопку, когда вам нужно перейти на другую страницу — вместо нее используйте ссылку. Подробнее см. на странице [Гиперссылки](hyperlinks.md).

> Исключение: для навигации по мастеру используйте кнопки "Вперед" и "Назад". Для других видов навигации на уровень выше или ниже используйте кнопку "Назад".

## <a name="example"></a>Пример

В этом примере используются две кнопки "Разрешить" и "Блокировать" в диалоговом окне, запрашивающем расположение.

![Пример кнопок, используемых в диалоговом окне](images/dialogs/dialog_RS2_two_button.png)

## <a name="create-a-button"></a>Создание кнопки

В этом примере рассматривается реакция кнопки на щелчок.

Создайте кнопку в XAML.

```xaml
<Button Content="Subscribe" Click="SubscribeButton_Click"/>
```

Либо создайте кнопку в коде.

```csharp
Button subscribeButton = new Button();
subscribeButton.Content = "Subscribe";
subscribeButton.Click += SubscribeButton_Click;

// Add the button to a parent container in the visual tree.
stackPanel1.Children.Add(subscribeButton);
```

Обработайте событие "Click".

```csharp
private async void SubscribeButton_Click(object sender, RoutedEventArgs e)
{
    // Call app specific code to subscribe to the service. For example:
    ContentDialog subscribeDialog = new ContentDialog
    {
        Title = "Subscribe to App Service?",
        Content = "Listen, watch, and play in high definition for only $9.99/month. Free to try, cancel anytime.",
        CloseButtonText = "Not Now",
        PrimaryButtonText = "Subscribe",
        SecondaryButtonText = "Try it"
    };

    ContentDialogResult result = await subscribeDialog.ShowAsync();
}
```

### <a name="button-interaction"></a>Взаимодействие с кнопкой

Если коснуться кнопки пальцем или стилусом либо навести на нее указатель и нажать левую кнопку мыши, кнопка вызывает событие [Click](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.primitives.buttonbase.click.aspx). Если для кнопки предусмотрен фокус клавиатуры, при нажатии клавиши ВВОД или ПРОБЕЛ также происходит вызов события "Click".

Как правило, нельзя обрабатывать низкоуровневые события [PointerPressed](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.uielement.pointerpressed.aspx) с помощью элемента "Button", поскольку для него предусмотрено поведение "Click". Дополнительные сведения см. в разделе [Общие сведения о событиях и перенаправленных событиях](https://msdn.microsoft.com/library/windows/apps/mt185584.aspx).

Можно выбирать порядок вызова кнопкой события "Click" путем изменения свойства [ClickMode](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.primitives.buttonbase.clickmode.aspx). Значением ClickMode по умолчанию является **Release**. Если для параметра ClickMode выбрано значение **Hover**, событие "Click" невозможно вызвать нажатием клавиши или касанием.


### <a name="button-content"></a>Содержимое кнопки

Кнопка представляет собой [ContentControl](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.contentcontrol.aspx). Ее свойство содержимого XAML — [Content](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.contentcontrol.content.aspx), благодаря чему возможно использование подобного синтаксиса для XAML: `<Button>A button's content</Button>`. В качестве содержимого кнопки можно задать любой объект. Если содержимым является класс [UIElement](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.uielement.aspx), он обрабатывается для просмотра в кнопке. Если содержимым является другой тип объекта, в кнопке отображается его строковое представление.

Здесь класс **StackPanel**, содержащий изображение апельсина и текст, задан в качестве содержимого кнопки.

```xaml
<Button Click="Button_Click"
        Background="LightGray"
        Height="100" Width="80">
    <StackPanel>
        <Image Source="Assets/Photo.png" Height="62"/>
        <TextBlock Text="Photos" Foreground="Black"
                   HorizontalAlignment="Center"/>
    </StackPanel>
</Button>
```

Кнопка выглядит следующим образом.

![Кнопка с содержимым в виде изображения и текста](images/button-orange.png)

## <a name="create-a-repeat-button"></a>Создание кнопки повтора

[RepeatButton](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.primitives.repeatbutton.aspx) — это кнопка, при нажатии которой события [Click](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.primitives.buttonbase.click.aspx) повторяются до тех пор, пока она не будет отпущена. Задайте свойство [Delay](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.primitives.repeatbutton.delay.aspx), чтобы указать время задержки после нажатия кнопки, по прошествии которого начнется повторение действия щелчка. Задайте свойство [Interval](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.primitives.repeatbutton.interval.aspx), чтобы указать время между повторениями действия щелчка. Время для обоих свойств указывается в миллисекундах.

В следующем примере показаны два элемента управления RepeatButton, чьи соответствующие события "Click" используются для увеличения и уменьшения значения, приведенного в блоке текста.

```xaml
<StackPanel>
    <RepeatButton Width="100" Delay="500" Interval="100" Click="Increase_Click">Increase</RepeatButton>
    <RepeatButton Width="100" Delay="500" Interval="100" Click="Decrease_Click">Decrease</RepeatButton>
    <TextBlock x:Name="clickTextBlock" Text="Number of Clicks:" />
</StackPanel>
```

```csharp
private static int _clicks = 0;
private void Increase_Click(object sender, RoutedEventArgs e)
{
    _clicks += 1;
    clickTextBlock.Text = "Number of Clicks: " + _clicks;
}

private void Decrease_Click(object sender, RoutedEventArgs e)
{
    if(_clicks > 0)
    {
        _clicks -= 1;
        clickTextBlock.Text = "Number of Clicks: " + _clicks;
    }
}
```

## <a name="recommendations"></a>Рекомендации

-   Сделайте так, чтобы назначение и состояние кнопки было понятно пользователю.
-   Используйте краткий, конкретный, не требующий разъяснений текст, который четко описывает действие, выполняемое кнопкой. Обычно текст надписи на кнопке состоит из одного слова — как правило, глагола.
-   Если для одного решения предусмотрено несколько кнопок (например, в диалоговом окне подтверждения), расположите эти кнопки в следующем порядке:
    -   ОК/[Выполнить]/Да
    -   [Не выполнять]/Нет
    -   Отмена

    (Где [Выполнить] и [Не выполнять] — конкретные варианты ответа на основную инструкцию.)

-   Если текст надписи на кнопке динамический (например, локализуемый), продумайте, как кнопка сможет изменять свои размеры и как это повлияет на элементы управления, окружающие ее.
-   Для кнопок команд с текстовым содержимым используйте минимальную ширину кнопки.
-   Не используйте узкие, короткие или высокие кнопки команд с текстовым содержимым.
-   Используйте шрифт, заданный по умолчанию, если в соответствии со стилем торговой марки не требуется использовать другой.
-   Для действия, которое требуется выполнить над множеством страниц вашего приложения, лучше использовать [нижнюю панель приложения](app-bars.md), а не повторять соответствующую кнопку на каждой странице.
-   Сделайте одновременно доступными пользователю только одну или две кнопки, например "Принять" и "Отклонить". Если требуется предоставить пользователю больше действий, введите в интерфейс [флажки](checkbox.md) или [переключатели](radio-button.md), с помощью которых пользователь сможет выбрать нужные действия, а затем путем нажатия одной кнопки начать выполнение всех этих действий.
-   Используйте кнопку команды, определенную по умолчанию, для запуска наиболее часто используемого или рекомендуемого действия.
-   При желании параметры кнопок можно изменить. По умолчанию кнопки имеют прямоугольную форму, однако визуальные элементы, создающие внешний вид кнопки, можно изменить. Содержимым кнопки обычно является текст, например, "Принять" или "Отклонить", но его можно заменить на значок или сочетание значка и текста.
-   Сделайте так, чтобы при взаимодействии пользователя с кнопкой состояние и внешний вид кнопки менялись для отображения отклика на действия пользователя. Кнопка может быть, к примеру, в нормальном состоянии, нажатом или неактивном.
-   Определите начало выполнения действия в момент, когда пользователь касается кнопки или нажимает ее. Обычно действие начинает выполняться при отпускании кнопки, но можно также определить начало выполнения действия в момент, когда палец первый раз нажимает кнопку.
-   Не меняйте местами заданные по умолчанию стили "отправить", "сбросить" и "кнопка".
-   Не размещайте слишком много содержимого на одной кнопке. Сделайте содержимое кратким и понятным.

### <a name="recommended-single-button-layout"></a>Рекомендуется макет с одной кнопкой

Если в макете требуется только одна кнопка, она должна быть выровнена либо по левому, либо по правому краю на основе контекста его контейнера.

-   Диалоговые окна только с одной кнопкой должны выравнивать кнопку **по правому краю**. Если диалоговое окно содержит только одну кнопку, убедитесь, что кнопка выполняет безопасное, обратимое действие. Если вы используете [ContentDialog](dialogs.md) и укажете одну кнопку, она будет автоматически выровнена по правому краю.

![Кнопка в диалоговом окне](images/pushbutton_doc_dialog.png)

-   Если кнопка отображается внутри контейнера пользовательского интерфейса (например, во всплывающем уведомлении, всплывающем элементе или представлении элемента списка), следует выровнять кнопку в контейнере **по правому краю**.

![Кнопка внутри контейнера](images/pushbutton_doc_container.png)

-   На страницах, содержащих одну кнопку (например, кнопку "Применить" в нижней части страницы параметров), вы должны выровнять кнопку **по левому краю**. Это гарантирует, что кнопка соответствует остальной части содержимого страницы.

![Кнопка на странице](images/pushbutton_doc_page.png)

## <a name="back-buttons"></a>Кнопки "Назад"

Кнопка "Назад" — это предоставленный системой элемент пользовательского интерфейса для обратной навигации через обратный стек или журнал навигации пользователя. Вы не обязаны создавать собственную кнопку "Назад", но вы можете выполнить определенные действия, чтобы реализовать хорошие возможности обратной навигации. Дополнительные сведения см. в разделе [Журнал и навигация в обратном направлении](../layout/navigation-history-and-backwards-navigation.md)

## <a name="get-the-sample-code"></a>Получить пример кода
*   [Основы создания пользовательского интерфейса XAML](https://github.com/Microsoft/Windows-universal-samples/blob/master/Samples/XamlUIBasics)<br/>
    Ознакомьтесь со всеми элементами управления XAML в интерактивном формате.


## <a name="related-articles"></a>Еще по теме

- [Переключатели](radio-button.md)
- [Тумблеры](toggles.md)
- [Флажки](checkbox.md)
- [Класс Button](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.button.aspx)
