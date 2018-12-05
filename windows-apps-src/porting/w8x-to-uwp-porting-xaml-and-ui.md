---
description: Практика определения пользовательского интерфейса в форме декларативной разметки XAML очень хорошо подходит не только для универсальных приложений для версии 8.1, но и для приложений универсальной платформы Windows (UWP).
title: Перенос XAML среды выполнения Windows 8.x и пользовательского интерфейса в UWP
ms.assetid: 78b86762-7359-474f-b1e3-c2d7cf9aa907
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 5ddeee438e946dd28a78d59d163f59cb1114aa65
ms.sourcegitcommit: d7613c791107f74b6a3dc12a372d9de916c0454b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/05/2018
ms.locfileid: "8737987"
---
# <a name="porting-windows-runtime-8x-xaml-and-ui-to-uwp"></a>Перенос XAML среды выполнения Windows 8.x и пользовательского интерфейса в UWP


Предыдущий раздел называется [Устранение неполадок](w8x-to-uwp-troubleshooting.md).

Практика определения пользовательского интерфейса в форме декларативной разметки XAML очень хорошо преобразуется из универсальных приложений для версии 8.1 в приложения универсальной платформы Windows (UWP). Вы увидите, что большая часть вашей разметки совместима, хотя может потребоваться внести некоторые изменения в ключи системных ресурсов или используемые настраиваемые шаблоны. Для императивного кода доработка не потребуется или она будет минимальной. Перенести большую часть кода в вашем уровне представления, которая управляет элементами пользовательского интерфейса, также будет крайне просто.

## <a name="imperative-code"></a>Императивный код

Если вы просто хотите дойти до этапа, на котором выполняется сборка вашего проекта, можно закомментировать или снабдить заглушками фрагменты ненужного кода. Затем последовательно устраняйте неполадку за неполадкой и см. следующие темы в данном разделе (включая предыдущий раздел [Устранение неполадок](w8x-to-uwp-troubleshooting.md)), пока любые проблемы сборки и выполнения не будут решены и перенос не будет завершен.

## <a name="adaptiveresponsive-ui"></a>Адаптивный пользовательский интерфейс с быстрым откликом

Так как приложение потенциально может работать на множестве различных устройств, каждое из которых имеет свой размер и разрешение экрана, вам необходимо будет выйти за пределы минимальных действий для переноса приложения. При этом вы, конечно же, захотите адаптировать свой пользовательский интерфейс для придания ему наилучшего вида на этих устройствах. Вы можете использовать адаптивную функцию "Диспетчер визуальных состояний", чтобы динамически определять размер окна и изменять макет. Пример того, как это сделать, представлен в разделе [Адаптивный пользовательский интерфейс](w8x-to-uwp-case-study-bookstore2.md) в примере Bookstore2.

## <a name="back-button-handling"></a>Работа с кнопкой "Назад"

Для универсальных 8.1 приложениях, приложений среды выполнения Windows 8.x и Windows Phone имеют разные подходы к пользовательского интерфейса, отображается и события, которые обрабатывают для кнопки "Назад". Но для Windows10 приложений, вы можете использовать единый подход в приложении. На мобильных устройствах эта кнопка выполняет роль емкостной кнопки на устройстве или кнопки в оболочке. На настольном ПК кнопка добавляется во внешнее оформление приложения, когда в приложении возможны переходы назад, и отображается в строке заголовка оконных приложений или на панели задач для режима планшета. Событие кнопки "Назад"—это универсальная концепция во всех семействах устройств, и кнопки, реализованные в аппаратном или программном обеспечении, создают одно и то же событие [**BackRequested**](https://msdn.microsoft.com/library/windows/apps/dn893596).

Приведенный ниже пример работает для всех семейств устройств и хорошо подходит в случаях, когда ко всем страницам применяется один и тот же метод обработки и подтверждать переходы (например, для предупреждения о несохраненных изменениях) не нужно.

```csharp
   // app.xaml.cs

    protected override void OnLaunched(LaunchActivatedEventArgs e)
    {
        [...]

        Windows.UI.Core.SystemNavigationManager.GetForCurrentView().BackRequested += App_BackRequested;
        rootFrame.Navigated += RootFrame_Navigated;
    }

    private void RootFrame_Navigated(object sender, NavigationEventArgs e)
    {
        Frame rootFrame = Window.Current.Content as Frame;

        // Note: On device families that have no title bar, setting AppViewBackButtonVisibility can safely execute 
        // but it will have no effect. Such device families provide back button UI for you.
        if (rootFrame.CanGoBack)
        {
            Windows.UI.Core.SystemNavigationManager.GetForCurrentView().AppViewBackButtonVisibility = 
                Windows.UI.Core.AppViewBackButtonVisibility.Visible;
        }
        else
        {
            Windows.UI.Core.SystemNavigationManager.GetForCurrentView().AppViewBackButtonVisibility = 
                Windows.UI.Core.AppViewBackButtonVisibility.Collapsed;
        }
    }

    private void App_BackRequested(object sender, Windows.UI.Core.BackRequestedEventArgs e)
    {
        Frame rootFrame = Window.Current.Content as Frame;

        if (rootFrame.CanGoBack)
        {
            rootFrame.GoBack();
        }
    }
```

Существует также единый подход для всех семейств устройств для программного выхода из приложения.

```csharp
   Windows.UI.Xaml.Application.Current.Exit();
```

## <a name="charms"></a>Чудо-кнопки

Вам не нужно изменить какие-либо код, интегрируемый с чудо-кнопки, но вам нужно добавить некоторые элементы пользовательского интерфейса в приложение вступили в силу панели чудо-кнопки, которая не является частью Windows10 оболочки. Универсальное приложение версии 8.1 на Windows10 имеет собственный пользовательский Интерфейс, предоставляемый системой в заголовке окна приложения.

## <a name="controls-and-control-styles-and-templates"></a>Элементы управления, стили и шаблоны элементов управления

Универсальное приложение версии 8.1 на Windows10 будет сохранять 8.1 внешний вид и принципы работы элементов управления. Однако при переносе этого приложения в приложение Windows10 есть некоторые отличия внешнего вида и поведения следует учитывать. Архитектура и оформление элементов управления Windows10 приложений, поэтому изменения в основном вокруг [язык дизайна](#design-language-in-windows-10), упрощений и улучшения удобства использования.

**Примечание**  визуальное состояние PointerOver используется в пользовательские стили или шаблоны в Windows10 приложений и в приложениях среды выполнения Windows 8.x, но не в приложениях магазина Windows Phone. По этой причине (из-за и ключи ресурсов системы, которые поддерживаются для приложений, Windows10) мы рекомендуем повторно использовать пользовательские стили или шаблоны из приложений среды выполнения Windows 8.x при переносе приложения для Windows10.
Если вы хотите быть уверены, что пользовательские стили или шаблоны используют последний набор визуальных состояний и них максимально применяются улучшения производительности, внесенные в стили или шаблоны по умолчанию, то отредактируйте копию нового шаблона по умолчанию Windows10 и повторно примените вашего настройки к ней. Одним из примеров улучшения производительности является то, что были удалены все элементы **Border**, ранее содержащие в себе **ContentPresenter** или панель, и теперь дочерний элемент отображает границу.

Далее приводится несколько более конкретных примеров изменений элементов управления.

| Название элемента управления | Изменение |
|--------------|--------|
| **AppBar**   | Если вы используете элементы управления **AppBar** (вместо него рекомендуется[**CommandBar**](https://msdn.microsoft.com/library/windows/apps/hh701927) ), тогда он не является скрытым по умолчанию в приложении для Windows10. Это можно контролировать с помощью свойства [**AppBar.ClosedDisplayMode**](https://msdn.microsoft.com/library/windows/apps/dn633872). |
| **AppBar**, [**CommandBar**](https://msdn.microsoft.com/library/windows/apps/hh701927) | В приложении для Windows10 **AppBar** и [**CommandBar**](https://msdn.microsoft.com/library/windows/apps/hh701927) имеют **более см. в разделе** кнопки (многоточие). |
| [**CommandBar**](https://msdn.microsoft.com/library/windows/apps/hh701927) | В приложении среды выполнения Windows 8.x вспомогательные команды [**CommandBar**](https://msdn.microsoft.com/library/windows/apps/hh701927) всегда были видимы. В приложении магазина Windows Phone и приложении для Windows10 они не отображаются до открытия панели команд. |
| [**CommandBar**](https://msdn.microsoft.com/library/windows/apps/hh701927) | Для приложения Магазина WindowsPhone значение [**CommandBar.IsSticky**](https://msdn.microsoft.com/library/windows/apps/hh701944) не влияет на функцию исчезновения панели. Для Windows10 приложения, если **IsSticky** задано значение true, а затем **CommandBar** параметра на жест исчезновения. |
| [**CommandBar**](https://msdn.microsoft.com/library/windows/apps/hh701927) | В приложении для Windows10 [**CommandBar**](https://msdn.microsoft.com/library/windows/apps/hh701927) не обрабатывает события [**EdgeGesture.Completed**](https://msdn.microsoft.com/library/windows/apps/hh701622) , ни [**UIElement.RightTapped**](https://msdn.microsoft.com/library/windows/apps/br208984) . Он также не реагирует на касание и проведение пальцем вверх. Вы по-прежнему можете обрабатывать эти события и настроить [**IsOpen**](https://msdn.microsoft.com/library/windows/apps/hh701939). |
| [**DatePicker**](https://msdn.microsoft.com/library/windows/apps/dn298584), [**TimePicker**](https://msdn.microsoft.com/library/windows/apps/dn299280) | Посмотрите, как будет выглядеть ваше приложение с визуальными изменениями [**DatePicker**](https://msdn.microsoft.com/library/windows/apps/dn298584) и [**TimePicker**](https://msdn.microsoft.com/library/windows/apps/dn299280). Для Windows10 приложение, запущенное на мобильном устройстве эти элементы управления больше не перейти на страницу выбора, но вместо этого используйте исчезновения всплывающего окна. |
| [**DatePicker**](https://msdn.microsoft.com/library/windows/apps/dn298584), [**TimePicker**](https://msdn.microsoft.com/library/windows/apps/dn299280) | В приложении для Windows10 невозможно разместить [**DatePicker**](https://msdn.microsoft.com/library/windows/apps/dn298584) и [**TimePicker**](https://msdn.microsoft.com/library/windows/apps/dn299280) внутри всплывающего элемента. Если вы хотите, чтобы эти элементы управления отображались во всплывающем элементе управления, затем можно использовать [**DatePickerFlyout**](https://msdn.microsoft.com/library/windows/apps/dn625013) и [**TimePickerFlyout**](https://msdn.microsoft.com/library/windows/apps/dn608313). |
| **GridView**, **ListView** | Сведения о **GridView**/**ListView** см. в разделе [Изменения GridView в и ListView](#gridview-and-listview-changes). |
| [**Hub**](https://msdn.microsoft.com/library/windows/apps/dn251843) | В приложении Магазина Windows Phone элемент управления [**Hub**](https://msdn.microsoft.com/library/windows/apps/dn251843) создает оболочку от последнего раздела к первому. В приложении среды выполнения Windows 8.x и в приложении для Windows10 главных разделов не создают оболочку. |
| [**Hub**](https://msdn.microsoft.com/library/windows/apps/dn251843) | В приложении Магазина Windows Phone фоновое изображение элемента управления [**Hub**](https://msdn.microsoft.com/library/windows/apps/dn251843) перемещается в параллаксе относительно главных разделов. В приложении среды выполнения Windows 8.x и в приложении для Windows10 параллакс не используется. |
| [**Hub**](https://msdn.microsoft.com/library/windows/apps/dn251843)  | В универсальном приложении версии 8.1 из-за свойства [**HubSection.IsHeaderInteractive**](https://msdn.microsoft.com/library/windows/apps/dn251917) заголовок раздела и глиф шеврона, отображаемый рядом с ним, становятся интерактивными. В приложении для Windows10 кроме заголовка существует интерактивная возможность «Подробнее», но сам заголовок не является интерактивным. **IsHeaderInteractive** по-прежнему определяет, вызывает ли взаимодействие событие [**Hub.SectionHeaderClick**](https://msdn.microsoft.com/library/windows/apps/dn251953). |
| **MessageDialog** | Рекомендуется вместо **MessageDialog** использовать более гибкий [**ContentDialog**](https://msdn.microsoft.com/library/windows/apps/dn633972). См. также пример [Основы создания пользовательского интерфейса XAML](http://go.microsoft.com/fwlink/p/?linkid=619992). |
| **ListPickerFlyout**, **PickerFlyout**  | **ListPickerFlyout** и **PickerFlyout** являются устаревшими для Windows10 приложения. Для простого всплывающего элемента используйте [**MenuFlyout**](https://msdn.microsoft.com/library/windows/apps/dn299030). Для более сложных элементов используйте [**Flyout**](https://msdn.microsoft.com/library/windows/apps/dn279496). |
| [**PasswordBox**](https://msdn.microsoft.com/library/windows/apps/br227519) | Свойство [**PasswordBox.IsPasswordRevealButtonEnabled**](https://msdn.microsoft.com/library/windows/apps/hh702579) рекомендуется использовать в приложении для Windows10 и его установка не оказывает воздействия. Вместо этого используйте [**PasswordBox.PasswordRevealMode**](https://msdn.microsoft.com/library/windows/apps/dn890867) по умолчанию для **временного просмотра** (в котором глиф глаза отображается, как в приложении среды выполнения Windows 8.x). См. также раздел [Руководство по полям паролей](https://msdn.microsoft.com/library/windows/apps/dn596103). |
| [**Pivot**](https://msdn.microsoft.com/library/windows/apps/dn608241) | Элемент управления [**Pivot**](https://msdn.microsoft.com/library/windows/apps/dn608241) теперь универсален, его использование больше не ограничивается мобильными устройствами. |
| [**SearchBox**](https://msdn.microsoft.com/library/windows/apps/dn252771) | Хотя [**SearchBox**](https://msdn.microsoft.com/library/windows/apps/dn252803) реализуется во всех семействах устройств, он не полностью использует свои функции в мобильных устройствах См. раздел [SearchBox заменен на AutoSuggestBox](#searchbox-deprecated-in-favor-of-autosuggestbox). |
| **SemanticZoom** | Сведения о **SemanticZoom** см. в разделе [Изменения SemanticZoom](#semanticzoom-changes). |
| [**ScrollViewer**](https://msdn.microsoft.com/library/windows/apps/br209527)  | Некоторые свойства [**ScrollViewer**](https://msdn.microsoft.com/library/windows/apps/br209527) по умолчанию изменились. [**HorizontalScrollMode**](https://msdn.microsoft.com/library/windows/apps/br209549) равно **Auto**, [**VerticalScrollMode**](https://msdn.microsoft.com/library/windows/apps/br209589) — **Auto** и [**ZoomMode**](https://msdn.microsoft.com/library/windows/apps/br209601) — **Отключено**. Если новые значения по умолчанию не подходят для вашего приложения, вы можете изменить их в стиле и как локальные значения в самом элементе управления.  |
| [**TextBox**](https://msdn.microsoft.com/library/windows/apps/br209683) | В приложении среды выполнения Windows 8.x проверку орфографии отключена по умолчанию для [**текстового поля**](https://msdn.microsoft.com/library/windows/apps/br209683). В приложении магазина Windows Phone и приложении для Windows10 она включена по умолчанию. |
| [**TextBox**](https://msdn.microsoft.com/library/windows/apps/br209683) | Размер шрифта по умолчанию для [**TextBox**](https://msdn.microsoft.com/library/windows/apps/br209683) изменен с 11 на 15. |
| [**TextBox**](https://msdn.microsoft.com/library/windows/apps/br209683) | Значение по умолчанию [**TextBox.TextReadingOrder**](https://msdn.microsoft.com/library/windows/apps/dn252859) изменилось с **Default** на **DetectFromContent**. Если это нежелательно, используйте **UseFlowDirection**. Использовать значение **Default** не рекомендуется. |
| Various | Цвет элементов применяется к приложениям магазина Windows Phone и приложениям Windows10, но не для приложений среды выполнения Windows 8.x.  |

Дополнительные сведения об элементах управления приложения UWP см. в разделах [Распределение элементов управления по функциям](https://msdn.microsoft.com/library/windows/apps/mt185405) [Список элементов управления](https://msdn.microsoft.com/library/windows/apps/mt185406) и [Руководство по элементам управления](https://msdn.microsoft.com/library/windows/apps/dn611856).

##  <a name="design-language-in-windows10"></a>Язык дизайна в Windows10

Существуют некоторые небольшие, но важные отличия в языке дизайна между универсальных приложений версии 8.1 и Windows10. Все сведения см. в разделе [Дизайн](http://dev.windows.com/design). Несмотря на изменения языка дизайна, принципы дизайна остаются прежними: будьте внимательны к подробным сведениям, но всегда стремитесь к простоте и фокусируйте внимание на содержимом, а не внешнем оформлении, уменьшая количество визуальных элементов и сохраняя аутентичность цифрового домена; используйте визуальную иерархию особенно для шрифтового оформления; проектируйте, используя сетку; а также реализуйте свои идеи с помощью плавных анимаций.

## <a name="effective-pixels-viewing-distance-and-scale-factors"></a>Эффективные пиксели, расстояние от экрана и коэффициенты масштабирования

Раньше пиксели представления были способом отделить размер и макет элементов пользовательского интерфейса от фактического физического размера и разрешения устройств. Теперь же пиксели представления стали эффективными пикселями. Далее приведено объяснение этого термина.

Термин «разрешение» относится к измерению плотности пикселей, а не, как обычно считается, количества пикселей. «Эффективное разрешение» — это способ, которым физические пиксели, образующие изображение или глиф разрешают для глаза данные различия в расстоянии от экрана и физическом размере пикселей устройства (плотность пикселей обратна физическому размеру пикселей). Эффективное разрешение — это хорошая метрика для создания взаимодействия на ее основе, поскольку оно ориентируется на пользователя. Поняв все факторы и контролируя размер элементов пользовательского интерфейса, вы можете сделать взаимодействие с пользователем комфортным.

Количество эффективных пикселей в ширину разное для различных устройств, от 320epx для самых маленьких устройств и до 1024epx для мониторов скромных размеров и выходит далеко за этот предел. Вам потребуется всего лишь продолжать использовать элементы с автоматически устанавливаемым размером и динамические панели макета, как и раньше. В некоторых случаях в свойствах элементов пользовательского интерфейса в разметке XAML задается фиксированный размер. Коэффициент масштабирования автоматически применяется к приложению в зависимости от устройства, на котором оно запускается, и от настроенных пользователем параметров экрана. Коэффициент масштабирования служит для сохранения представления элементами пользовательского интерфейса цели сенсорного ввода (и чтения) с более или менее постоянным размером для пользователя устройств с различными размерами экрана. И вместе с динамическим макетом ваш пользовательский интерфейс не будет просто оптически масштабироваться на разных устройствах. Вместо этого он будет выполнять необходимые действия для размещения соответствующего объема содержимого в доступном пространстве.

Чтобы ваше приложение будет наилучшим образом выглядело на любых устройствах, мы рекомендуем создать каждый растровый ресурс в различных размерах для определенного коэффициента масштабирования. Если вы предоставите ресурсы с масштабом 100%, 200% и 400% (в такой последовательности), это обеспечит отличные результаты в большинстве случаев при любых промежуточных коэффициентах масштабирования.

**Примечание**Если по какой-либо причине вы не более одного размера, создайте ресурсы масштабом 100%. В Microsoft Visual Studio шаблон проекта по умолчанию для приложений UWP предоставляет ресурсы официальной фирменной символики (изображения плиток и эмблемы) только в одном размере, но масштаб при этом не 100%. При создании ресурсов для своего приложения следуйте рекомендациям в этом разделе и предусмотрите размеры 100%, 200% и 400%, а также используйте пакеты ресурсов.

В случае использования сложного изображения, возможно, следует предусмотреть еще больше размеров. Если используются векторные изображения, тогда процесс создания высококачественных ресурсов при любом масштабе относительно прост.

Мы не рекомендуем пытаться поддерживать все коэффициенты масштабирования, что полный список коэффициенты масштабирования в Windows10 — 100%, 125%, 150%, 200%, 250%, 300% и 400%. Если вы все-таких их предоставляете, Магазин выберет правильные ресурсы для каждого устройства, и только они будут загружены. Магазин выбирает ресурсы для загрузки на основе DPI устройства. Можно повторно использовать ресурсы из вашего приложения среды выполнения Windows 8.x в коэффициенты масштабирования, например 140% и 220%, но ваше приложение будет выполняться в одном из новых коэффициенты масштабирования и поэтому некоторые масштабирование растрового изображения будет неизбежно. Проверьте ваше приложение на различных устройствах, чтобы убедиться, что результаты вас устраивают.

Вы можете повторно использовать разметку XAML из приложения среды выполнения Windows 8.x где Если значения литеральных измерений используются в разметке (например, фигуры размеров или другие элементы, возможно, для типографии). Но в некоторых случаях более крупный коэффициент масштабирования используется на устройстве для Windows10 приложения, чем для универсальных приложений версии 8.1 (например, 150% используется вместо 140%, куда 200% используется вместо 180%). Таким образом Если вы обнаружите, что литеральные значения находятся в Windows10 слишком большими, попробуйте умножить их на 0,8. Подробнее см. в разделе [Адаптивный дизайн 101 для приложений UWP](https://msdn.microsoft.com/library/windows/apps/dn958435).

## <a name="gridview-and-listview-changes"></a>Изменения GridView в и ListView

В методы задания стилей [**GridView**](https://msdn.microsoft.com/library/windows/apps/br242705) по умолчанию были внесены некоторые изменения для реализации вертикальной прокрутки этого элемента управления (вместо горизонтальной прокрутки, которая раньше использовалась по умолчанию). Если копия стиля по умолчанию в вашем проекте была изменена, то в вашей копии изменения применены не будут, и вам придется вносить их вручную. Ниже представлен список этих изменений.

-   Метод задания для [**ScrollViewer.HorizontalScrollBarVisibility**](https://msdn.microsoft.com/library/windows/apps/br209547) изменен с **Auto** на **Disabled**.
-   Метод задания для [**ScrollViewer.VerticalScrollBarVisibility**](https://msdn.microsoft.com/library/windows/apps/br209587) изменен с **Disabled** на **Auto**.
-   Метод задания для [**ScrollViewer.HorizontalScrollMode**](https://msdn.microsoft.com/library/windows/apps/br209549) изменен с **Enabled** на **Disabled**.
-   Метод задания для [**ScrollViewer.VerticalScrollMode**](https://msdn.microsoft.com/library/windows/apps/br209589) изменен с **Disabled** на **Enabled**.
-   В методе задания для [**ItemsPanel**](https://msdn.microsoft.com/library/windows/apps/br242826) значение [**ItemsWrapGrid.Orientation**](https://msdn.microsoft.com/library/windows/apps/dn298907) изменено с **Vertical** на **Horizontal**.

Если это последнее изменение (изменение **Orientation**) кажется вам противоречивым, помните, что мы говорим о сетке с переносом. Горизонтально ориентированная сетка с переносом (новое значение) похожа на систему письменности, где текст размещается по горизонтали и переносится вниз на следующую строку до конца страницы. Такая страница текста прокручивается по вертикали. И наоборот, вертикально ориентированная сетка с переносом (предыдущее значение) аналогична системе письменности, где текст размещается по вертикали, и поэтому такая страница прокручивается по горизонтали.

Ниже приведены аспекты [**GridView**](https://msdn.microsoft.com/library/windows/apps/br242705) и [**ListView**](https://msdn.microsoft.com/library/windows/apps/br242878) , у измените или не поддерживаются в Windows10.

-   Свойство [**IsSwipeEnabled**](https://msdn.microsoft.com/library/windows/apps/hh702518) (только для среды выполнения Windows 8.x приложений) не поддерживается для Windows10 приложений. Этот API по-прежнему присутствует, но его нельзя настраивать. Поддерживаются все предыдущие жесты выделения, кроме прокрутки вниз (этот жест не поддерживается, поскольку данные показывают, что он не обнаруживаемый) и щелчка правой кнопкой мыши (этот действие сохраняется для отображения контекстного меню).
-   Свойство [**ReorderMode**](https://msdn.microsoft.com/library/windows/apps/dn625099) (только для приложений магазина Windows Phone) не поддерживается для Windows10 приложений. Этот API по-прежнему присутствует, но его нельзя настраивать. Вместо этого установите для параметров [**AllowDrop**](https://msdn.microsoft.com/library/windows/apps/br208912) и [**CanReorderItems**](https://msdn.microsoft.com/library/windows/apps/br242882) значение "true" в **GridView** или **ListView**. Тогда пользователь сможет упорядочивать элементы с помощью жеста нажатия и удерживания (или перетаскивания).
-   При разработке для Windows10, используйте [**ListViewItemPresenter**](https://msdn.microsoft.com/library/windows/apps/dn298500) вместо [**GridViewItemPresenter**](https://msdn.microsoft.com/library/windows/apps/dn279298) в стиле контейнера элементов, как для [**ListView**](https://msdn.microsoft.com/library/windows/apps/br242878) и [**GridView**](https://msdn.microsoft.com/library/windows/apps/br242705). В случае изменения копии стилей контейнера элементов по умолчанию вы получите нужный тип.
-   Изменены визуальные элементы выбора для Windows10 приложения. Если установить для параметра [**SelectionMode**](https://msdn.microsoft.com/library/windows/apps/br242915) значение **Multiple**, флажок будет по умолчанию добавляться для каждого элемента. Значение по умолчанию для элементов **ListView** означает, что флажок будет расположен рядом с элементом, и в результате пространство, занимаемое оставшейся частью элемента, будет немного уменьшено и сдвинуто. Для элементов **GridView**, флажок накладывается поверх элемента по умолчанию. Но, в любом случае, вы можете настроить макет (встроенный или наложение) флажков (свойство [**CheckMode**](https://msdn.microsoft.com/library/windows/apps/dn913923)) и их отображение (свойство [**SelectionCheckMarkVisualEnabled**](https://msdn.microsoft.com/library/windows/apps/dn298541)) в элементе [**ListViewItemPresenter**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.primitives.listviewitempresenter.aspx) внутри стиля контейнера элементов, как описано в следующем примере.
-   В Windows10, [**ContainerContentChanging**](https://msdn.microsoft.com/library/windows/apps/dn298914) событие вызывается дважды для каждого элемента во время применения виртуализации пользовательского интерфейса: один раз для восстановления и один раз для последующего использования. Если [**InRecycleQueue**](https://msdn.microsoft.com/library/windows/apps/dn279443) имеет значение **true** и у вас нет конкретной работы по восстановлению, вы можете сразу же выйти из обработчика событий, зная, что в случае повторного использования данного элемента снова будет осуществлен вход (при этом **InRecycleQueue** будет иметь значение **false**).

```xml
<Style x:Key="CustomItemContainerStyle" TargetType="ListViewItem|GridViewItem">
    ...
    <Setter.Value>
        <ControlTemplate TargetType="ListViewItem|GridViewItem">
            <ListViewItemPresenter CheckMode="Inline|Overlay" ... />
        </ControlTemplate>
    </Setter.Value>
    ...
</Style>
```

![ListViewItemPresenter со встроенным флажком](images/w8x-to-uwp-case-studies/ui-listviewbase-cb-inline.jpg)

ListViewItemPresenter со встроенным флажком

![ListViewItemPresenter с наложенным флажком](images/w8x-to-uwp-case-studies/ui-listviewbase-cb-overlay.jpg)

ListViewItemPresenter с наложенным флажком

-   После удаления жестов прокрутки вниз и щелчка правой кнопкой мыши для осуществления выбора (по причинам, указанным выше) модель взаимодействия изменилась. Одним из последствий стало то, что события [**ItemClick**](https://msdn.microsoft.com/library/windows/apps/br242904) и [**SelectionChanged**](https://msdn.microsoft.com/library/windows/apps/br209776) больше не являются взаимно исключающими. Для Windows10 приложения просмотрите свои сценарии и решить, следует ли использовать «выбора» или «вызова» модели взаимодействия. Подробнее см. в разделе [Изменение режима взаимодействия](https://msdn.microsoft.com/library/windows/apps/xaml/hh780625).
-   Некоторые изменения свойств, используемые для реализации стиля [**ListViewItemPresenter**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.primitives.listviewitempresenter.aspx). Новые свойства: [**CheckBoxBrush**](https://msdn.microsoft.com/library/windows/apps/dn913905), [**PressedBackground**](https://msdn.microsoft.com/library/windows/apps/dn913931), [**SelectedPressedBackground**](https://msdn.microsoft.com/library/windows/apps/dn913937) и [**FocusSecondaryBorderBrush**](https://msdn.microsoft.com/library/windows/apps/dn898370). Свойства, которые игнорируются для Windows10 приложения, [**заполнения**](https://msdn.microsoft.com/library/windows/apps/dn424775) (использовать [**ContentMargin**](https://msdn.microsoft.com/library/windows/apps/dn424773) ), [**CheckHintBrush**](https://msdn.microsoft.com/library/windows/apps/dn298504), [**CheckSelectingBrush**](https://msdn.microsoft.com/library/windows/apps/dn298506), [**PointerOverBackgroundMargin**](https://msdn.microsoft.com/library/windows/apps/dn424778), [**ReorderHintOffset**](https://msdn.microsoft.com/library/windows/apps/dn298528), [** SelectedBorderThickness**](https://msdn.microsoft.com/library/windows/apps/dn298533)и [**SelectedPointerOverBorderBrush**](https://msdn.microsoft.com/library/windows/apps/dn298539).

В этой таблице описаны изменения визуальных состояний и группы визуальных состояний в шаблонах элементов управления [**ListViewItem**](https://msdn.microsoft.com/library/windows/apps/br242919) и [**GridViewItem**](https://msdn.microsoft.com/library/windows/apps/hh738501).

| 8.1                 |                         | Windows 10        |                     |
|---------------------|-------------------------|-------------------|---------------------|
| CommonStates        |                         | CommonStates      |                     |
|                     | Normal                  |                   | Normal              |
|                     | PointerOver             |                   | PointerOver         |
|                     | Pressed                 |                   | Pressed             |
|                     | PointerOverPressed      |                   | [недоступно]       |
|                     | Disabled                |                   | [недоступно]       |
|                     | [недоступно]           |                   | PointerOverSelected |
|                     | [недоступно]           |                   | Selected            |
|                     | [недоступно]           |                   | PressedSelected     |
| [недоступно]       |                         | DisabledStates    |                     |
|                     | [недоступно]           |                   | Disabled            |
|                     | [недоступно]           |                   | Enabled             |
| SelectionHintStates |                         | [недоступно]     |                     |
|                     | VerticalSelectionHint   |                   | [недоступно]       |
|                     | HorizontalSelectionHint |                   | [недоступно]       |
|                     | NoSelectionHint         |                   | [недоступно]       |
| [недоступно]       |                         | MultiSelectStates |                     |
|                     | [недоступно]           |                   | MultiSelectDisabled |
|                     | [недоступно]           |                   | MultiSelectEnabled  |
| SelectionStates     |                         | [недоступно]     |                     |
|                     | Unselecting             |                   | [недоступно]       |
|                     | Unselected              |                   | [недоступно]       |
|                     | UnselectedPointerOver   |                   | [недоступно]       |
|                     | UnselectedSwiping       |                   | [недоступно]       |
|                     | Selecting               |                   | [недоступно]       |
|                     | Selected                |                   | [недоступно]       |
|                     | SelectedSwiping         |                   | [недоступно]       |
|                     | SelectedUnfocused       |                   | [недоступно]       |

Если используется настраиваемый шаблон элементов управления [**ListViewItem**](https://msdn.microsoft.com/library/windows/apps/br242919) или [**GridViewItem**](https://msdn.microsoft.com/library/windows/apps/hh738501), пересмотрите его с учетом вышеуказанных изменений. Рекомендуется начать заново с изменения копии нового шаблона по умолчанию и повторного применения ваших настроек. Если по какой-либо причине вы не можете этого сделать и вам требуется изменить существующий шаблон, ознакомьтесь с некоторыми общими инструкциями, приведенными далее.

-   Добавление новой группы визуальных состояний MultiSelectStates.
-   Добавление нового визуального состояния MultiSelectDisabled.
-   Добавление нового визуального состояния MultiSelectEnabled.
-   Добавление новой группы визуальных состояний DisabledStates.
-   Добавление нового визуального состояния Enabled.
-   В группе визуальных состояний CommonStates удалите визуальное состояние PointerOverPressed.
-   Переместите визуальное состояние Disabled в группу визуальных состояний DisabledStates.
-   Добавление нового визуального состояния PointerOverSelected.
-   Добавление нового визуального состояния PressedSelected.
-   Удаление группы визуальных состояний SelectedHintStates.
-   Переместите визуальное состояние Selected из группы визуальных состояний в группу визуальных состояний CommonStates.
-   Удаление целой группы визуальных состояний SelectionStates.

## <a name="localization-and-globalization"></a>Локализация и глобализация

Вы можете повторно использовать файл Resources.resw из проекта универсального приложения для версии 8.1 в проекте приложения UWP. После копирования файла добавьте его в проект и задайте для параметра **Действие при сборке** значение **PRIResource**, а для параметра **Копировать в выходной каталог** — **Не копировать**. В разделе [**ResourceContext.QualifierValues**](https://msdn.microsoft.com/library/windows/apps/br206071) описывается загрузка ресурсов для определенного семейства устройств с учетом коэффициента выбора ресурсов для семейств устройств.

## <a name="play-to"></a>Воспроизведение на устройстве

API-интерфейсы в пространстве имен [**Windows.Media.PlayTo**](https://msdn.microsoft.com/library/windows/apps/br207025) устаревших для приложений Windows10 пользу [**Windows.Media.Casting**](https://msdn.microsoft.com/library/windows/apps/dn972568) API-интерфейсы.

## <a name="resource-keys-and-textblock-style-sizes"></a>Ключи ресурса и размеры стиля TextBlock

Язык дизайна эволюционировал для Windows10 и поэтому некоторые системные стили были также изменены. В некоторых случаях вам может потребоваться обновить визуальные элементы представлений, чтобы они соответствовали свойствам стилей, которые изменились.

В других случаях ключи ресурсов больше не поддерживаются. Редактор разметки XAML в Visual Studio выделяет ссылки на ключи ресурсов, которые не могут быть разрешены. Например, редактор разметки XAML подчеркнет ссылку к ключу стиля `ListViewItemTextBlockStyle` волнистой линией красного цвета. Если она не будет исправлена, приложение немедленно завершит работу при попытке развернуть его в эмуляторе или на устройстве. Поэтому важно обращать внимание на правильность разметки XAML. И вы увидите, что Visual Studio— это отличный инструмент для нахождения подобных проблем.

Для ключей, которые по-прежнему поддерживаются, изменения языка дизайна означают, что изменились свойства, заданные некоторыми стилями. Например `TitleTextBlockStyle` задает **FontSize** 14,667 пикселя в приложении среды выполнения Windows 8.x и 18,14 пикселя в приложении магазина Windows Phone. Однако тот же стиль задает **FontSize** гораздо большее значение 24 пикселя в приложении для Windows10. Проверьте свои разработки и макеты и используйте соответствующие стили в нужных местах. Дополнительные сведения см. в разделе [Рекомендации по шрифтам](https://msdn.microsoft.com/library/windows/apps/hh700394.aspx) и [Оформление приложений UWP](http://dev.windows.com/design).

Далее приведен полный список ключей, которые больше не поддерживаются.

-   CheckBoxAndRadioButtonMinWidthSize
-   CheckBoxAndRadioButtonTextPaddingThickness
-   ComboBoxFlyoutListPlaceholderTextOpacity
-   ComboBoxFlyoutListPlaceholderTextThemeMargin
-   ComboBoxHighlightedBackgroundThemeBrush
-   ComboBoxHighlightedBorderThemeBrush
-   ComboBoxHighlightedForegroundThemeBrush
-   ComboBoxInlinePlaceholderTextForegroundThemeBrush
-   ComboBoxInlinePlaceholderTextThemeFontWeight
-   ComboBoxItemDisabledThemeOpacity
-   ComboBoxItemHighContrastBackgroundThemeMargin
-   ComboBoxItemMinHeightThemeSize
-   ComboBoxPlaceholderTextBlockStyle
-   ComboBoxPlaceholderTextThemeMargin
-   CommandBarBackgroundThemeBrush
-   CommandBarForegroundThemeBrush
-   ContentDialogButton1HostPadding
-   ContentDialogButton2HostPadding
-   ContentDialogButtonsMinHeight
-   ContentDialogContentLandscapeWidth
-   ContentDialogContentMinHeight
-   ContentDialogDimmingColor
-   ContentDialogTitleMinHeight
-   ControlContextualInfoTextBlockStyle
-   ControlHeaderContentPresenterStyle
-   ControlHeaderTextBlockStyle
-   FlyoutContentPanelLandscapeThemeMargin
-   FlyoutContentPanelPortraitThemeMargin
-   GrabberMargin
-   GridViewItemMargin
-   GridViewItemPlaceholderBackgroundThemeBrush
-   GroupHeaderTextBlockStyle
-   HeaderContentPresenterStyle
-   HighContrastBlack
-   HighContrastWhite
-   HubHeaderCharacterSpacing
-   HubHeaderFontSize
-   HubHeaderMarginThickness
-   HubSectionHeaderCharacterSpacing
-   HubSectionHeaderFontSize
-   HubSectionHeaderMarginThickness
-   HubSectionMarginThickness
-   InlineWindowPlayPauseMargin
-   ItemTemplate
-   LeftFullWindowMargin
-   LeftMargin
-   ListViewEmptyStaticTextBlockStyle
-   ListViewItemContentTextBlockStyle
-   ListViewItemContentTranslateX
-   ListViewItemMargin
-   ListViewItemMultiselectCheckBoxMargin
-   ListViewItemSubheaderTextBlockStyle
-   ListViewItemTextBlockStyle
-   MediaControlPanelAudioThemeBrush
-   MediaControlPanelPhoneVideoThemeBrush
-   MediaControlPanelVideoThemeBrush
-   MediaControlPanelVideoThemeColor
-   MediaControlPlayPauseThemeBrush
-   MediaControlTimeRowThemeBrush
-   MediaControlTimeRowThemeColor
-   MediaDownloadProgressIndicatorThemeBrush
-   MediaErrorBackgroundThemeBrush
-   MediaTextThemeBrush
-   MenuFlyoutBackgroundThemeBrush
-   MenuFlyoutBorderThemeBrush
-   MenuFlyoutLandscapeThemePadding
-   MenuFlyoutLeftLandscapeBorderThemeThickness
-   MenuFlyoutPortraitBorderThemeThickness
-   MenuFlyoutPortraitThemePadding
-   MenuFlyoutRightLandscapeBorderThemeThickness
-   MessageDialogContentStyle
-   MessageDialogTitleStyle
-   MinimalWindowMargin
-   PasswordBoxCheckBoxThemeMargin
-   PhoneAccentBrush
-   PhoneBackgroundBrush
-   PhoneBackgroundColor
-   PhoneBaseBlackColor
-   PhoneBaseHighColor
-   PhoneBaseLowColor
-   PhoneBaseLowSolidColor
-   PhoneBaseMediumHighColor
-   PhoneBaseMediumMidColor
-   PhoneBaseMediumMidSolidColor
-   PhoneBaseMidColor
-   PhoneBaseWhiteColor
-   PhoneBorderThickness
-   PhoneButtonBasePressedForegroundBrush
-   PhoneButtonContentPadding
-   PhoneButtonFontWeight
-   PhoneButtonMinHeight
-   PhoneButtonMinWidth
-   PhoneChromeBrush
-   PhoneChromeColor
-   PhoneControlBackgroundColor
-   PhoneControlDisabledColor
-   PhoneControlForegroundColor
-   PhoneDisabledBrush
-   PhoneDisabledColor
-   PhoneFontFamilyLight
-   PhoneFontFamilySemiBold
-   PhoneForegroundBrush
-   PhoneForegroundColor
-   PhoneHighContrastSelectedBackgroundThemeBrush
-   PhoneHighContrastSelectedForegroundThemeBrush
-   PhoneImagePlaceholderColor
-   PhoneLowBrush
-   PhoneMidBrush
-   PhonePageBackgroundColor
-   PhonePivotLockedTranslation
-   PhonePivotUnselectedItemOpacity
-   PhoneRadioCheckBoxBorderBrush
-   PhoneRadioCheckBoxBrush
-   PhoneRadioCheckBoxCheckBrush
-   PhoneRadioCheckBoxPressedBrush
-   PhoneStrokeThickness
-   PhoneTextHighColor
-   PhoneTextLowColor
-   PhoneTextMidColor
-   PhoneTextOverAccentColor
-   PhoneTouchTargetLargeOverhang
-   PhoneTouchTargetOverhang
-   PivotHeaderItemPadding
-   PlaceholderContentPresenterStyle
-   ProgressBarHighContrastAccentBarThemeBrush
-   ProgressBarIndeterminateRectagleThemeSize
-   ProgressBarRectangleStyle
-   ProgressRingActiveBackgroundOpacity
-   ProgressRingElipseThemeMargin
-   ProgressRingElipseThemeSize
-   ProgressRingTextForegroundThemeBrush
-   ProgressRingTextThemeMargin
-   ProgressRingThemeSize
-   RichEditBoxTextThemeMargin
-   RightFullWindowMargin
-   RightMargin
-   ScrollBarMinThemeHeight
-   ScrollBarMinThemeWidth
-   ScrollBarPanningThumbThemeHeight
-   ScrollBarPanningThumbThemeWidth
-   SliderThumbDisabledBorderThemeBrush
-   SliderTrackBorderThemeBrush
-   SliderTrackDisabledBorderThemeBrush
-   TextBoxBackgroundColor
-   TextBoxBorderColor
-   TextBoxDisabledHeaderForegroundThemeBrush
-   TextBoxFocusedBackgroundThemeBrush
-   TextBoxForegroundColor
-   TextBoxPlaceholderColor
-   TextControlHeaderMarginThemeThickness
-   TextControlHeaderMinHeightSize
-   TextStyleExtraExtraLargeFontSize
-   TextStyleExtraLargePlusFontSize
-   TextStyleMediumFontSize
-   TextStyleSmallFontSize
-   TimeRemainingElementMargin

## <a name="searchbox-deprecated-in-favor-of-autosuggestbox"></a>SearchBox заменен на AutoSuggestBox.

Хотя [**SearchBox**](https://msdn.microsoft.com/library/windows/apps/dn252803) реализуется во всех семействах устройств, он не полностью использует свои функции в мобильных устройствах. Используйте [**AutoSuggestBox**](https://msdn.microsoft.com/library/windows/apps/dn633874) при выполнении любого поиска. Ниже описана типичная реализация поиска с помощью **AutoSuggestBox**.

Когда пользователь начинает вводить текст, создается событие **TextChanged** с причиной **UserInput**. Затем вы можете заполнить список вариантов текста и выбрать **ItemsSource** элемента [**AutoSuggestBox**](https://msdn.microsoft.com/library/windows/apps/dn633874). Когда пользователь переходит по списку, создается событие **SuggestionChosen** (если вы задали **TextMemberDisplayPath**, в текстовое поле автоматически вводится указанное свойство). Когда пользователь подтверждает выбор с помощью клавиши Enter, создается событие **QuerySubmitted**, и в этот момент вы можете выполнить соответствующее действие с выбранным вариантом (в данном случае, скорее всего, это будет переход на другую страницу с более подробной информацией об указанном содержимом). Обратите внимание, что свойства **LinguisticDetails** и **Language** элемента **SearchBoxQuerySubmittedEventArgs** больше не поддерживаются (для поддержки этой функции предусмотрены аналогичные API). **KeyModifiers** также больше не поддерживаются.

[**AutoSuggestBox**](https://msdn.microsoft.com/library/windows/apps/dn633874) также поддерживает редакторы методов ввода (IME). Кроме того, можно отобразить значок поиска, если это необходимо (взаимодействие со значком приведет к вызову события **QuerySubmitted**).

```xml
   <AutoSuggestBox ... >
        <AutoSuggestBox.QueryIcon>
            <SymbolIcon Symbol="Find"/>
        </AutoSuggestBox.QueryIcon>
    </AutoSuggestBox>
```

См. также раздел [Пример переноса AutoSuggestBox](http://go.microsoft.com/fwlink/p/?linkid=619996).

## <a name="semanticzoom-changes"></a>Изменения SemanticZoom

Жест уменьшения масштаба для [**SemanticZoom**](https://msdn.microsoft.com/library/windows/apps/hh702601) упрощен на модели WindowsPhone. Теперь для уменьшения масштаба нужно коснуться заголовка группы или щелкнуть его (поэтому на настольных компьютерах больше не отображается кнопка со знаком минус, которая использовалась для уменьшения масштаба). Теперь мы бесплатно получаем одинаковое поведение на всех устройствах. Одно незначительное отличие от модели Windows Phone состоит в том, что уменьшенное представление (список переходов) заменяет увеличенное представление, а не накладывается на него. По этой причине вы можете удалить все полупрозрачные фоны из уменьшенных представлений.

В приложении магазина Windows Phone, viewexpands уменьшенное к thesize экрана. В приложении среды выполнения Windows 8.x и в приложении для Windows10 размер уменьшенного представления ограничивается границами элемента управления **SemanticZoom** .

В приложении Магазина Windows Phone содержимое за представлением с уменьшенным масштабом (в z-порядке) просвечивается, если представление с уменьшенным масштабом имеет какую-либо прозрачность в своем фоне. В приложении среды выполнения Windows 8.x и в приложении для Windows10 ничего не видны за представлением с уменьшенным масштабом.

В приложении среды выполнения Windows 8.x при деактивации и повторном включении приложения с уменьшенным масштабом закрывается (если оно отображалось) и вместо него отображается в увеличенном масштабе. В приложении магазина Windows Phone и приложении для Windows10 уменьшенное представление останется отображаться, если оно отображалось.

В приложении магазина Windows Phone и приложении для Windows10 уменьшенным масштабом закрывается при нажатии кнопки "Назад". Для приложений среды выполнения Windows 8.x нет не обрабатывает встроенные кнопки "Назад", поэтому вопрос не применяется.

## <a name="settings"></a>Параметры

Класс среды выполнения Windows 8.x **SettingsPane** не подходит для Windows10. Вместо этого, в дополнение к построению страницы параметров, необходимо предоставить пользователям способ получать доступ к ней из вашего приложения. Рекомендуется представить страницу параметров приложения на верхнем уровне, как последний закрепленный элемент на панели навигации, но ниже приводится полный набор доступных вариантов.

-   Панель навигации. Страница параметров должна быть последним элементом в навигационном списке вариантов выбора и закреплена внизу.
-   Панель приложения или панель инструментов (в представлении вкладки или макете сводки). Страница параметров должна быть последним элементом во всплывающем меню панели приложения или панели инструментов. Не рекомендуется, чтобы страница параметров была одним из элементов верхнего уровня на панели навигации.
-   Hub. Страница параметров должна быть расположена внутри всплывающего меню (возможно, из меню панели приложения или меню панели инструментов в макете Центра).

Также не рекомендуется, чтобы располагать параметры на панели основных и подробных данных.

Страница параметров должна полностью заполнять окно приложения и также располагаться в разделе сведений о приложении и отзывов. Рекомендации по проектированию страницы параметров см. в разделе [Рекомендации по параметрам приложений](https://msdn.microsoft.com/library/windows/apps/hh770544).

## <a name="text"></a>Текст

Текст (или шрифтовое оформление) является важным аспектом для приложения UWP. Во время переноса, возможно, необходимо будет пересмотреть визуальные элементы в представлениях, чтобы они соответствовали новому языку дизайна. Используйте эти иллюстрации, чтобы найти доступные стили системы UWP **TextBlock**. Найдите те, которые соответствуют используемым стилям WindowsPhone Silverlight. Кроме того можно создать собственные универсальные стили и скопировать в них свойства из системных стилей WindowsPhone Silverlight.

![Стили системы TextBlock для приложений для Windows 10](images/label-uwp10stylegallery.png) <br/>Стили системы TextBlock для Windows10 приложений

В приложениях среды выполнения Windows 8.x и магазина Windows Phone семейством шрифтов по умолчанию является Global User Interface. В приложении Windows10 семейством шрифтов по умолчанию является Segoe UI. В результате метрика шрифта в приложении может выглядеть иначе. Если вы хотите воспроизвести вид текста 8.1, то можно настроить собственную метрику с помощью свойств, например, [**LineHeight**](https://msdn.microsoft.com/library/windows/apps/br209671) и [**LineStackingStrategy**](https://msdn.microsoft.com/library/windows/apps/br244362).

В приложениях среды выполнения Windows 8.x и Windows Phone, языка по умолчанию имеет значение язык сборки или en-us. В приложении для Windows10 язык по умолчанию задано значение язык главного приложения (резервный вариант шрифта). Вы можете явно установить [**FrameworkElement.Language**](https://msdn.microsoft.com/library/windows/apps/hh702066), но вам будет удобнее использовать резервный вариант шрифта, если вы не установите значение для этого свойства.

Дополнительные сведения см. в разделе [Рекомендации по шрифтам](https://msdn.microsoft.com/library/windows/apps/hh700394.aspx) и [Оформление приложений UWP](http://go.microsoft.com/fwlink/p/?LinkID=533896). Сведения об изменениях элементов управления текстом также см. в разделе [Элементы управления](#controls-and-control-styles-and-templates) выше.

## <a name="theme-changes"></a>Изменения тем

Для универсальных приложений версии 8.1 по умолчанию используется темная тема. Для устройств Windows10 тема по умолчанию было изменено, но вы можете управлять используемой темой, объявив запрошенную тему в файле App.xaml. Например, чтобы на всех устройствах отображалась темная тема, добавьте `RequestedTheme="Dark"` к корневому элементу Application.

## <a name="tiles-and-toasts"></a>Плитки и всплывающие уведомления

Плитки и всплывающие уведомления шаблоны, которые вы используете в данный момент будет и дальше работать в приложении Windows10. Однако есть и новые, адаптивные шаблоны, описанные в разделе [Уведомления, плитки, всплывающие уведомления и индикаторы событий](https://msdn.microsoft.com/library/windows/apps/mt185606).

Ранее на настольных компьютерах всплывающее уведомление было временным сообщением. Через какое-то время или после его игнорирования оно исчезало и больше было не доступно. Если всплывающее уведомление игнорируется или временно закрывается в Windows Phone, оно поступает в центр поддержки. Теперь центр поддержки доступен не только семейству мобильных устройств.

Чтобы отправить всплывающее уведомление, больше не требуется декларировать возможность.

## <a name="window-size"></a>Размер окна

Для универсальных приложений версии 8.1 элемент манифеста приложения [**ApplicationView**](https://msdn.microsoft.com/library/windows/apps/dn391667) используется для объявления минимальной ширины окна. В приложении UWP можно задать минимальный размер окна (ширину и высоту) с помощью императивного кода. Стандартный минимальный размер— 500x320эфф. пикселей. Это также самый маленький из допустимых минимальных размеров. Самый большой из допустимых минимальных размеров—500 x 500 эфф. пикселей.

```csharp
   Windows.UI.ViewManagement.ApplicationView.GetForCurrentView().SetPreferredMinSize
        (new Size { Width = 500, Height = 500 });
```

Следующий раздел называется [Перенос для ввода-вывода, устройств и моделей приложений](w8x-to-uwp-input-and-sensors.md).

