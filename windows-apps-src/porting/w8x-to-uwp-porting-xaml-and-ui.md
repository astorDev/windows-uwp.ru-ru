---
description: Практика определения пользовательского интерфейса в форме декларативной разметки XAML очень хорошо подходит не только для универсальных приложений для версии 8.1, но и для приложений универсальной платформы Windows (UWP).
title: Перенос XAML среды выполнения Windows 8.x и пользовательского интерфейса в UWP
ms.assetid: 78b86762-7359-474f-b1e3-c2d7cf9aa907
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 829755f6ccba7a076096e4a03555458b98a4b670
ms.sourcegitcommit: ac7f3422f8d83618f9b6b5615a37f8e5c115b3c4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/29/2019
ms.locfileid: "66372239"
---
# <a name="porting-windows-runtime-8x-xaml-and-ui-to-uwp"></a>Перенос XAML среды выполнения Windows 8.x и пользовательского интерфейса в UWP


Предыдущий раздел называется [Устранение неполадок](w8x-to-uwp-troubleshooting.md).

Практика определения пользовательского интерфейса в форме декларативной разметки XAML очень хорошо подходит не только для универсальных приложений для версии 8.1, но и для приложений универсальной платформы Windows (UWP). Вы увидите, что большая часть вашей разметки совместима, хотя может потребоваться внести некоторые изменения в ключи системных ресурсов или используемые настраиваемые шаблоны. Для императивного кода доработка не потребуется или она будет минимальной. Перенести большую часть кода в вашем уровне представления, которая управляет элементами пользовательского интерфейса, также будет крайне просто.

## <a name="imperative-code"></a>Императивный код

Если вы просто хотите дойти до этапа, на котором выполняется сборка вашего проекта, можно закомментировать или снабдить заглушками фрагменты ненужного кода. Затем выполните итерацию, одна проблема одновременно и в следующих статьях в этом разделе (и в предыдущем разделе: [Устранение неполадок](w8x-to-uwp-troubleshooting.md)), пока не порта завершения, и проблем сборки и выполнения ironed-out.

## <a name="adaptiveresponsive-ui"></a>Адаптивный пользовательский интерфейс с быстрым откликом

Так как приложение потенциально может работать на множестве различных устройств, каждое из которых имеет свой размер и разрешение экрана, вам необходимо будет выйти за пределы минимальных действий для переноса приложения. При этом вы, конечно же, захотите адаптировать свой пользовательский интерфейс для придания ему наилучшего вида на этих устройствах. Вы можете использовать адаптивную функцию "Диспетчер визуальных состояний", чтобы динамически определять размер окна и изменять макет. Пример того, как это сделать, представлен в разделе [Адаптивный пользовательский интерфейс](w8x-to-uwp-case-study-bookstore2.md) в примере Bookstore2.

## <a name="back-button-handling"></a>Работа с кнопкой "Назад"

Для универсальных 8.1 приложения, приложения среды выполнения Windows 8.x и приложения Windows Phone Store используются различные подходы для пользовательского интерфейса, можно показать и события, которые вы обрабатываете для кнопки «Назад». Однако для приложений Windows 10, единый подход можно использовать в приложении. На мобильных устройствах эта кнопка выполняет роль емкостной кнопки на устройстве или кнопки в оболочке. На настольном ПК кнопка добавляется во внешнее оформление приложения, когда в приложении возможны переходы назад, и отображается в строке заголовка оконных приложений или на панели задач для режима планшета. Событие кнопки "Назад" — это универсальная концепция во всех семействах устройств, и кнопки, реализованные в аппаратном или программном обеспечении, создают одно и то же событие [**BackRequested**](https://docs.microsoft.com/uwp/api/windows.ui.core.systemnavigationmanager.backrequested).

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

Не нужно изменять код, который интегрируется с чудо-кнопки, но вам нужно добавить некоторые пользовательского интерфейса в приложение вместо панели чудо-кнопки, которая не является частью оболочки Windows 10. Uwp 8.1 приложение, работающее в Windows 10 имеет свой собственный замены пользовательского интерфейса, предоставляемые к просмотру системы chrome в заголовке окна приложения.

## <a name="controls-and-control-styles-and-templates"></a>Элементы управления, стили и шаблоны элементов управления

Uwp 8.1 приложение, работающее в Windows 10 будет сохранять 8.1 внешний вид и поведение по отношению к элементам управления. Но при переносе этого приложения в приложении Windows 10, существуют некоторые различия в внешнего вида и поведения, которые следует учитывать. Архитектура и Разработка элементов управления является по сути неизменными для приложений Windows 10, поэтому изменения, главным образом вокруг усовершенствование язык, упрощения и удобство использования.

**Примечание**    PointerOver визуальное состояние применима в пользовательские стили и шаблоны в приложениях Windows 10 и в приложениях среды выполнения Windows 8.x, но не в приложениях Windows Phone Store. По этой причине (и, из-за ключи системных ресурсов, которые поддерживаются для приложений Windows 10) рекомендуется повторно использовать пользовательские стили и шаблоны из приложений среды выполнения Windows 8.x, когда вы переносите приложения для Windows 10.
Если вы хотите быть уверены, что пользовательские стили и шаблоны при использовании последний набор визуальных состояний и, какую пользу получили от улучшения производительности, по умолчанию, стили и шаблоны, а затем изменить копию нового шаблона по умолчанию Windows 10 и повторно применить к настройки. Одним из примеров улучшения производительности является то, что были удалены все элементы **Border**, ранее содержащие в себе **ContentPresenter** или панель, и теперь дочерний элемент отображает границу.

Далее приводится несколько более конкретных примеров изменений элементов управления.

| Название элемента управления | Изменение |
|--------------|--------|
| **Элемент управления AppBar**   | Если вы используете **AppBar** управления ([**CommandBar** ](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.AppBar) рекомендуется вместо этого), то оно не скрыто по умолчанию в приложении Windows 10. Это можно контролировать с помощью свойства [**AppBar.ClosedDisplayMode**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.appbar.closeddisplaymode). |
| **AppBar**, [**CommandBar**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.AppBar) | В приложении Windows 10 **AppBar** и [ **CommandBar** ](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.AppBar) имеют **увеличить** кнопки (многоточие). |
| [**Панели команд**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.AppBar) | В приложении среды выполнения Windows 8.x, дополнительные команды из [ **CommandBar** ](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.AppBar) всегда видимы. В приложении Windows Phone Store и в приложении Windows 10 не отображаются, пока не откроется на панели команд. |
| [**Панели команд**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.AppBar) | Для приложения Магазина Windows Phone значение [**CommandBar.IsSticky**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.appbar.issticky) не влияет на функцию исчезновения панели. Для приложения Windows 10 если **IsSticky** имеет значение true, затем **CommandBar** игнорирует жест света отклонить. |
| [**Панели команд**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.AppBar) | В приложении Windows 10 [ **CommandBar** ](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.AppBar) не обрабатывает [ **EdgeGesture.Completed** ](https://docs.microsoft.com/uwp/api/windows.ui.input.edgegesture.completed) , ни [  **UIElement.RightTapped** ](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement.righttapped) события. Он также не реагирует на касание и проведение пальцем вверх. Вы по-прежнему можете обрабатывать эти события и настроить [**IsOpen**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.appbar.isopen). |
| [**DatePicker**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.DatePicker), [**TimePicker**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.TimePicker) | Посмотрите, как будет выглядеть ваше приложение с визуальными изменениями [**DatePicker**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.DatePicker) и [**TimePicker**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.TimePicker). Для приложений Windows 10, выполняющихся на мобильном устройстве эти элементы управления больше не перейти на страницу выбора, но вместо этого использовать всплывающее окно маловажные свет. |
| [**DatePicker**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.DatePicker), [**TimePicker**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.TimePicker) | В приложении Windows 10, нельзя поместить [ **DatePicker** ](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.DatePicker) или [ **TimePicker** ](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.TimePicker) внутри всплывающего. Если вы хотите, чтобы эти элементы управления отображались во всплывающем элементе управления, можете воспользоваться [**DatePickerFlyout**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.DatePickerFlyout) и [**TimePickerFlyout**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.TimePickerFlyout). |
| **GridView**, **ListView** | Сведения о **GridView**/**ListView** см. в разделе [Изменения GridView в и ListView](#gridview-and-listview-changes). |
| [**Центр**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Hub) | В приложении Магазина Windows Phone элемент управления [**Hub**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Hub) создает оболочку от последнего раздела к первому. В приложении среды выполнения Windows 8.x и в приложении Windows 10 разделы концентратора обтекает. |
| [**Центр**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Hub) | В приложении Магазина Windows Phone фоновое изображение элемента управления [**Hub**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Hub) перемещается в параллаксе относительно главных разделов. В приложении среды выполнения Windows 8.x и в приложении Windows 10 фокусировки не используется. |
| [**Центр**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Hub)  | В универсальном приложении версии 8.1 из-за свойства [**HubSection.IsHeaderInteractive**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.hubsection.isheaderinteractive) заголовок раздела и глиф шеврона, отображаемый рядом с ним, становятся интерактивными. В приложении Windows 10 нет интерактивных affordance, «Показать больше» рядом с заголовком, но сам заголовок не является интерактивным. **IsHeaderInteractive** по-прежнему определяет, вызывает ли взаимодействие событие [**Hub.SectionHeaderClick**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.hub.sectionheaderclick). |
| **MessageDialog** | Рекомендуется вместо **MessageDialog** использовать более гибкий [**ContentDialog**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.ContentDialog). См. также пример [Основы создания пользовательского интерфейса XAML](https://go.microsoft.com/fwlink/p/?linkid=619992). |
| **ListPickerFlyout**, **PickerFlyout**  | **ListPickerFlyout** и **PickerFlyout** являются устаревшими для приложения Windows 10. Для простого всплывающего элемента используйте [**MenuFlyout**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.MenuFlyout). Для более сложных элементов используйте [**Flyout**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Flyout). |
| [**PasswordBox**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.PasswordBox) | [ **PasswordBox.IsPasswordRevealButtonEnabled** ](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.passwordbox.ispasswordrevealbuttonenabled) свойство является устаревшим в приложении Windows 10, и его установка не оказывает влияния. Используйте [ **PasswordBox.PasswordRevealMode** ](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.passwordbox.passwordrevealmode) вместо этого, который по умолчанию **Показать** (в котором глиф в виде глаза отображаются, как в приложении среды выполнения Windows 8.x). См. также раздел [Руководство по полям паролей](https://docs.microsoft.com/windows/uwp/controls-and-patterns/password-box). |
| [**PIVOT**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Pivot) | Элемент управления [**Pivot**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Pivot) теперь универсален; его использование больше не ограничивается мобильными устройствами. |
| [**Окна поиска**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.SearchBox) | Хотя [**SearchBox**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.searchbox.) реализуется во всех семействах устройств, он не полностью использует свои функции в мобильных устройствах. См. раздел [SearchBox заменен на AutoSuggestBox](#searchbox-deprecated-in-favor-of-autosuggestbox). |
| **Элемент управления SemanticZoom** | Сведения о **SemanticZoom** см. в разделе [Изменения SemanticZoom](#semanticzoom-changes). |
| [**ScrollViewer**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.ScrollViewer)  | Некоторые свойства [**ScrollViewer**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.ScrollViewer) по умолчанию изменились. [**HorizontalScrollMode** ](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.scrollviewer.horizontalscrollmode) — **автоматически**, [ **VerticalScrollMode** ](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.scrollviewer.verticalscrollmode) — **автоматически**и [ **Параметр ZoomMode** ](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.scrollviewer.zoommode) — **отключено**. Если новые значения по умолчанию не подходят для вашего приложения, вы можете изменить их в стиле и как локальные значения в самом элементе управления.  |
| [**Текстовое поле**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.TextBox) | В приложении среды выполнения Windows 8.x, проверка орфографии по умолчанию отключены для [ **TextBox**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.TextBox). В приложении Windows Phone Store и в приложении Windows 10 оно включено по умолчанию. |
| [**Текстовое поле**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.TextBox) | Размер шрифта по умолчанию для [**TextBox**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.TextBox) изменен с 11 на 15. |
| [**Текстовое поле**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.TextBox) | Значение по умолчанию [**TextBox.TextReadingOrder**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.textblock.textreadingorder) изменилось с **Default** на **DetectFromContent**. Если это нежелательно, используйте **UseFlowDirection**. Использовать значение **Default** не рекомендуется. |
| Various | Цвет применяется к приложениям Windows Phone Store и для приложений Windows 10, но не приложений среды выполнения Windows 8.x.  |

Дополнительные сведения об элементах управления приложения UWP см. в разделах [Распределение элементов управления по функциям](https://docs.microsoft.com/windows/uwp/controls-and-patterns/controls-by-function)[Список элементов управления](https://docs.microsoft.com/windows/uwp/design/controls-and-patterns/) и [Руководство по элементам управления](https://developer.microsoft.com/windows/design/controls-patterns).

##  <a name="design-language-in-windows10"></a>Язык проектирования в Windows 10

Есть некоторые небольшие, но важные различия в язык проектирования приложения универсальной 8.1 и Windows 10. Все сведения см. в разделе [Дизайн](https://developer.microsoft.com/en-us/windows/apps/design). Несмотря на изменения языка дизайна, принципы дизайна остаются прежними: будьте внимательны к деталям, при этом всегда стремитесь к простоте и фокусируйте внимание на содержимом, а не внешнем оформлении, уменьшая количество визуальных элементов и сохраняя характерные особенности цифрового домена, используйте визуальную иерархию (особенно для шрифтового оформления), проектируйте, используя сетку, оживляйте интерфейс с помощью плавных анимаций.

## <a name="effective-pixels-viewing-distance-and-scale-factors"></a>Эффективные пиксели, расстояние от экрана и коэффициенты масштабирования

Раньше пиксели представления были способом отделить размер и макет элементов пользовательского интерфейса от фактического физического размера и разрешения устройств. Теперь же пиксели представления стали эффективными пикселями. Далее приведено объяснение этого термина.

Термин «разрешение» относится к измерению плотности пикселей, а не, как обычно считается, количества пикселей. «Эффективное разрешение» — это способ, которым физические пиксели, образующие изображение или глиф разрешают для глаза данные различия в расстоянии от экрана и физическом размере пикселей устройства (плотность пикселей обратна физическому размеру пикселей). Эффективное разрешение — это хорошая метрика для создания взаимодействия на ее основе, поскольку оно ориентируется на пользователя. Поняв все факторы и контролируя размер элементов пользовательского интерфейса, вы можете сделать взаимодействие с пользователем комфортным.

Количество эффективных пикселей в ширину разное для различных устройств, от 320 epx для самых маленьких устройств и до 1024 epx для мониторов скромных размеров и выходит далеко за этот предел. Вам потребуется всего лишь продолжать использовать элементы с автоматически устанавливаемым размером и динамические панели макета, как и раньше. В некоторых случаях в свойствах элементов пользовательского интерфейса в разметке XAML задается фиксированный размер. Коэффициент масштабирования автоматически применяется к приложению в зависимости от устройства, на котором оно запускается, и от настроенных пользователем параметров экрана. Коэффициент масштабирования служит для сохранения представления элементами пользовательского интерфейса цели сенсорного ввода (и чтения) с более или менее постоянным размером для пользователя устройств с различными размерами экрана. И вместе с динамическим макетом ваш пользовательский интерфейс не будет просто оптически масштабироваться на разных устройствах. Вместо этого он будет выполнять необходимые действия для размещения соответствующего объема содержимого в доступном пространстве.

Чтобы ваше приложение будет наилучшим образом выглядело на любых устройствах, мы рекомендуем создать каждый растровый ресурс в различных размерах для определенного коэффициента масштабирования. Если вы предоставите ресурсы с масштабом 100 %, 200 % и 400 % (в такой последовательности), это обеспечит отличные результаты в большинстве случаев при любых промежуточных коэффициентах масштабирования.

**Примечание**  Если для какой-либо причине вы невозможно создание ресурсов в более чем один размер, а затем создать масштаба 100%. В Microsoft Visual Studio шаблон проекта по умолчанию для приложений UWP предоставляет ресурсы официальной фирменной символики (изображения плиток и эмблемы) только в одном размере, но масштаб при этом не 100 %. При создании ресурсов для своего приложения следуйте рекомендациям в этом разделе и предусмотрите размеры 100 %, 200 % и 400 %, а также используйте пакеты ресурсов.

В случае использования сложного изображения, возможно, следует предусмотреть еще больше размеров. Если используются векторные изображения, тогда процесс создания высококачественных ресурсов при любом масштабе относительно прост.

Мы не рекомендуем попытаться поддерживают все коэффициенты масштабирования, что полный список коэффициенты масштабирования для приложений Windows 10 — 100%, 125%, 150%, 200%, 250%, 300% до 400%. Если вы все-таких их предоставляете, Магазин выберет правильные ресурсы для каждого устройства, и только они будут загружены. Магазин выбирает ресурсы для загрузки на основе DPI устройства. Можно повторно использовать ресурсы из приложения среды выполнения Windows 8.x с коэффициенты масштабирования, например 140% и 220%, но приложение работает в одной из новых коэффициенты масштабирования, и поэтому масштабирование растрового изображения будет неизбежно. Проверьте ваше приложение на различных устройствах, чтобы убедиться, что результаты вас устраивают.

Вы можете повторно использовать разметки XAML из приложения среды выполнения Windows 8.x где значения литерала измерения используются в разметке (возможно, чтобы размер фигуры или другие элементы, например для оформления). Но в некоторых случаях увеличить коэффициент масштабирования используется на устройстве для приложения Windows 10, чем для приложений Uwp 8.1 (например, 150% используется где раньше 140%, а используется 200%, где был 180%). Таким образом Если обнаружится, что эти литералы теперь слишком велики в Windows 10, затем повторите умножения 0,8. Подробнее см. в разделе [Адаптивный дизайн 101 для приложений UWP](https://docs.microsoft.com/windows/uwp/layout/screen-sizes-and-breakpoints-for-responsive-design).

## <a name="gridview-and-listview-changes"></a>Изменения GridView в и ListView

В методы задания стилей [**GridView**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.GridView) по умолчанию были внесены некоторые изменения для реализации вертикальной прокрутки этого элемента управления (вместо горизонтальной прокрутки, которая раньше использовалась по умолчанию). Если копия стиля по умолчанию в вашем проекте была изменена, то в вашей копии изменения применены не будут, и вам придется вносить их вручную. Ниже представлен список этих изменений.

-   Метод задания для [**ScrollViewer.HorizontalScrollBarVisibility**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.scrollviewer.horizontalscrollbarvisibility) изменен с **Auto** на **Disabled**.
-   Метод задания для [**ScrollViewer.VerticalScrollBarVisibility**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.scrollviewer.verticalscrollbarvisibility) изменен с **Disabled** на **Auto**.
-   Метод задания для [**ScrollViewer.HorizontalScrollMode**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.scrollviewer.horizontalscrollmode) изменен с **Enabled** на **Disabled**.
-   Метод задания для [**ScrollViewer.VerticalScrollMode**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.scrollviewer.verticalscrollmode) изменен с **Disabled** на **Enabled**.
-   В методе задания для [**ItemsPanel**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.itemscontrol.itemspanel) значение [**ItemsWrapGrid.Orientation**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.itemswrapgrid.orientation) изменено с **Vertical** на **Horizontal**.

Если это последнее изменение (изменение **Orientation**) кажется вам противоречивым, помните, что мы говорим о сетке с переносом. Горизонтально ориентированная сетка с переносом (новое значение) похожа на систему письменности, где текст размещается по горизонтали и переносится вниз на следующую строку до конца страницы. Такая страница текста прокручивается по вертикали. И наоборот, вертикально ориентированная сетка с переносом (предыдущее значение) аналогична системе письменности, где текст размещается по вертикали, и поэтому такая страница прокручивается по горизонтали.

Ниже приведены аспекты [ **GridView** ](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.GridView) и [ **ListView** ](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.ListView) , внести изменения, или не поддерживаются в Windows 10.

-   [ **IsSwipeEnabled** ](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.listviewbase.isswipeenabled) свойства (только для среды выполнения Windows 8.x приложения) не поддерживается для приложений Windows 10. Этот API по-прежнему присутствует, но его нельзя настраивать. Поддерживаются все предыдущие жесты выделения, кроме прокрутки вниз (этот жест не поддерживается, поскольку данные показывают, что он не обнаруживаемый) и щелчка правой кнопкой мыши (этот действие сохраняется для отображения контекстного меню).
-   [ **ReorderMode** ](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.listviewbase.reordermode) свойства (только для приложений Windows Phone Store) не поддерживается для приложений Windows 10. Этот API по-прежнему присутствует, но его нельзя настраивать. Вместо этого установите для параметров [**AllowDrop**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement.allowdrop) и [**CanReorderItems**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.listviewbase.canreorderitems) значение "true" в **GridView** или **ListView**. Тогда пользователь сможет упорядочивать элементы с помощью жеста нажатия и удерживания (или перетаскивания).
-   При разработке для Windows 10 используйте [ **ListViewItemPresenter** ](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Primitives.ListViewItemPresenter) вместо [ **GridViewItemPresenter** ](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Primitives.GridViewItemPresenter) в контейнере элемента стиль, как для [ **ListView** ](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.ListView) и [ **GridView**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.GridView). В случае изменения копии стилей контейнера элементов по умолчанию вы получите нужный тип.
-   Выбор визуальных элементов были изменены для приложения Windows 10. Если установить для параметра [**SelectionMode**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.listviewbase.selectionmode) значение **Multiple**, флажок будет по умолчанию добавляться для каждого элемента. Значение по умолчанию для элементов **ListView** означает, что флажок будет расположен рядом с элементом, и в результате пространство, занимаемое оставшейся частью элемента, будет немного уменьшено и сдвинуто. Для элементов **GridView**, флажок накладывается поверх элемента по умолчанию. Но, в любом случае, вы можете настроить макет (встроенный или наложение) флажков (свойство [**CheckMode**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.primitives.listviewitempresenter.checkmode)) и их отображение (свойство [**SelectionCheckMarkVisualEnabled**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.primitives.listviewitempresenter.selectioncheckmarkvisualenabled)) в элементе [**ListViewItemPresenter**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.primitives.listviewitempresenter) внутри стиля контейнера элементов, как описано в следующем примере.
-   В Windows 10 [ **ContainerContentChanging** ](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.listviewbase.containercontentchanging) событие вызывается дважды каждого элемента во время виртуализации пользовательского интерфейса: один раз для reclaim и один раз для повторного использования. Если [**InRecycleQueue**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.containercontentchangingeventargs.inrecyclequeue) имеет значение **true** и у вас нет конкретной работы по восстановлению, вы можете сразу же выйти из обработчика событий, зная, что в случае повторного использования данного элемента снова будет осуществлен вход (при этом **InRecycleQueue** будет иметь значение **false**).

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

-   После удаления жестов прокрутки вниз и щелчка правой кнопкой мыши для осуществления выбора (по причинам, указанным выше) модель взаимодействия изменилась. Одним из последствий стало то, что события [**ItemClick**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.listviewbase.itemclick) и [**SelectionChanged**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.primitives.selector.selectionchanged) больше не являются взаимно исключающими. Для приложения Windows 10 просмотрите ваших сценариев и решить, следует ли внедрять «Выбор» или «вызов» модель взаимодействия. Подробнее см. в разделе [Изменение режима взаимодействия](https://docs.microsoft.com/previous-versions/windows/apps/hh780625(v=win.10)).
-   Некоторые изменения свойств, используемые для реализации стиля [**ListViewItemPresenter**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.primitives.listviewitempresenter). Новые свойства: [**CheckBoxBrush**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.primitives.listviewitempresenter.checkboxbrush), [**PressedBackground**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.primitives.listviewitempresenter.pressedbackground), [**SelectedPressedBackground**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.primitives.listviewitempresenter.selectedpressedbackground) и [**FocusSecondaryBorderBrush**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.primitives.listviewitempresenter.focussecondaryborderbrush). Свойства, которые учитываются для приложения Windows 10, [ **Padding** ](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.primitives.listviewitempresenter.padding) (использовать [ **ContentMargin** ](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.primitives.listviewitempresenter.contentmargin) вместо этого), [ **CheckHintBrush**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.primitives.listviewitempresenter.checkhintbrush), [ **CheckSelectingBrush**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.primitives.listviewitempresenter.checkselectingbrush), [ **PointerOverBackgroundMargin** ](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.primitives.listviewitempresenter.pointeroverbackgroundmargin), [ **ReorderHintOffset**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.primitives.listviewitempresenter.reorderhintoffset), [ **SelectedBorderThickness**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.primitives.listviewitempresenter.selectedborderthickness), и [  **SelectedPointerOverBorderBrush**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.primitives.listviewitempresenter.selectedpointeroverborderbrush).

В этой таблице описаны изменения визуальных состояний и группы визуальных состояний в шаблонах элементов управления [**ListViewItem**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.ListViewItem) и [**GridViewItem**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.GridViewItem).

| 8.1                 |                         | Windows 10        |                     |
|---------------------|-------------------------|-------------------|---------------------|
| CommonStates        |                         | CommonStates      |                     |
|                     | Обычный                  |                   | Обычный              |
|                     | PointerOver             |                   | PointerOver         |
|                     | Pressed                 |                   | Pressed             |
|                     | PointerOverPressed      |                   | [недоступно]       |
|                     | Отключено                |                   | [недоступно]       |
|                     | [недоступно]           |                   | PointerOverSelected |
|                     | [недоступно]           |                   | Selected            |
|                     | [недоступно]           |                   | PressedSelected     |
| [недоступно]       |                         | DisabledStates    |                     |
|                     | [недоступно]           |                   | Отключено            |
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

Если используется настраиваемый шаблон элементов управления [**ListViewItem**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.ListViewItem) или [**GridViewItem**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.GridViewItem), пересмотрите его с учетом вышеуказанных изменений. Рекомендуется начать заново с изменения копии нового шаблона по умолчанию и повторного применения ваших настроек. Если по какой-либо причине вы не можете этого сделать и вам требуется изменить существующий шаблон, ознакомьтесь с некоторыми общими инструкциями, приведенными далее.

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

Вы можете повторно использовать файл Resources.resw из проекта универсального приложения для версии 8.1 в проекте приложения UWP. После копирования файла добавьте его в проект и задайте для параметра **Действие при сборке** значение **PRIResource**, а для параметра **Копировать в выходной каталог** — **Не копировать**. В разделе [**ResourceContext.QualifierValues**](https://docs.microsoft.com/uwp/api/windows.applicationmodel.resources.core.resourcecontext.qualifiervalues) описывается загрузка ресурсов для определенного семейства устройств с учетом коэффициента выбора ресурсов для семейств устройств.

## <a name="play-to"></a>Воспроизведение на устройстве

API-интерфейсы в [ **Windows.Media.PlayTo** ](https://docs.microsoft.com/uwp/api/Windows.Media.PlayTo) пространства имен являются устаревшими для приложений Windows 10 для [ **Windows.Media.Casting** ](https://docs.microsoft.com/uwp/api/Windows.Media.Casting) API-интерфейсы.

## <a name="resource-keys-and-textblock-style-sizes"></a>Ключи ресурса и размеры стиля TextBlock

Язык проектирования развивалась для Windows 10, и поэтому были изменены некоторые системные стили. В некоторых случаях вам может потребоваться обновить визуальные элементы представлений, чтобы они соответствовали свойствам стилей, которые изменились.

В других случаях ключи ресурсов больше не поддерживаются. Редактор разметки XAML в Visual Studio выделяет ссылки на ключи ресурсов, которые не могут быть разрешены. Например, редактор разметки XAML подчеркнет ссылку к ключу стиля `ListViewItemTextBlockStyle` волнистой линией красного цвета. Если она не будет исправлена, приложение немедленно завершит работу при попытке развернуть его в эмуляторе или на устройстве. Поэтому важно обращать внимание на правильность разметки XAML. И вы увидите, что Visual Studio — это отличный инструмент для нахождения подобных проблем.

Для ключей, которые по-прежнему поддерживаются, изменения языка дизайна означают, что изменились свойства, заданные некоторыми стилями. Например `TitleTextBlockStyle` задает **FontSize** для 14.667px в приложениях среды выполнения Windows 8.x и 18.14px в приложении Windows Phone Store. Однако же стиль устанавливает **FontSize** для гораздо большего размера 24 точки в приложении Windows 10. Проверьте свои разработки и макеты и используйте соответствующие стили в нужных местах. Дополнительные сведения см. в разделе [Рекомендации по шрифтам](https://docs.microsoft.com/windows/uwp/controls-and-patterns/fonts) и [Оформление приложений UWP](https://developer.microsoft.com/en-us/windows/apps/design).

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

Хотя [**SearchBox**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.searchbox.) реализуется во всех семействах устройств, он не полностью использует свои функции в мобильных устройствах. Используйте [**AutoSuggestBox**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.AutoSuggestBox) при выполнении любого поиска. Ниже описана типичная реализация поиска с помощью **AutoSuggestBox**.

Когда пользователь начинает вводить текст, создается событие **TextChanged** с причиной **UserInput**. Затем вы можете заполнить список вариантов текста и выбрать **ItemsSource** элемента [**AutoSuggestBox**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.AutoSuggestBox). Когда пользователь переходит по списку, создается событие **SuggestionChosen** (если вы задали **TextMemberDisplayPath**, в текстовое поле автоматически вводится указанное свойство). Когда пользователь подтверждает выбор с помощью клавиши Enter, создается событие **QuerySubmitted**, и в этот момент вы можете выполнить соответствующее действие с выбранным вариантом (в данном случае, скорее всего, это будет переход на другую страницу с более подробной информацией об указанном содержимом). Обратите внимание, что свойства **LinguisticDetails** и **Language** элемента **SearchBoxQuerySubmittedEventArgs** больше не поддерживаются (для поддержки этой функции предусмотрены аналогичные API). **KeyModifiers** также больше не поддерживаются.

[**AutoSuggestBox** ](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.AutoSuggestBox) также реализована поддержка Редакторы метода ввода (IME). Кроме того, можно отобразить значок поиска, если это необходимо (взаимодействие со значком приведет к вызову события **QuerySubmitted**).

```xml
   <AutoSuggestBox ... >
        <AutoSuggestBox.QueryIcon>
            <SymbolIcon Symbol="Find"/>
        </AutoSuggestBox.QueryIcon>
    </AutoSuggestBox>
```

См. также раздел [Пример переноса AutoSuggestBox](https://go.microsoft.com/fwlink/p/?linkid=619996).

## <a name="semanticzoom-changes"></a>Изменения SemanticZoom

Жест уменьшения масштаба для [**SemanticZoom**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.SemanticZoom) упрощен в модели Windows Phone. Теперь для уменьшения масштаба нужно коснуться заголовка группы или щелкнуть его (поэтому на настольных компьютерах больше не отображается кнопка со знаком минус, которая использовалась для уменьшения масштаба). Теперь мы бесплатно получаем одинаковое поведение на всех устройствах. Одно незначительное отличие от модели Windows Phone состоит в том, что уменьшенное представление (список переходов) заменяет увеличенное представление, а не накладывается на него. По этой причине вы можете удалить все полупрозрачные фоны из уменьшенных представлений.

В приложении Windows Phone Store уменьшенное представление расширяется до размера экрана. В приложении среды выполнения Windows 8.x и в приложении Windows 10, размер уменьшенное представление ограничен до границ **SemanticZoom** элемента управления.

В приложении Магазина Windows Phone содержимое за представлением с уменьшенным масштабом (в z-порядке) просвечивается, если представление с уменьшенным масштабом имеет какую-либо прозрачность в своем фоне. В приложении среды выполнения Windows 8.x и в приложении Windows 10 ничего не отображается за уменьшенного представления.

В приложении среды выполнения Windows 8.x при повторной активации и деактивации приложения, уменьшенное представление закрывается (если он был отображается), и вместо этого отображается — увеличенное представление. В приложении Windows Phone Store и в приложении Windows 10 если он был отображается уменьшенное представление будет оставаться отображения.

В приложении Windows Phone Store и в приложении Windows 10 уменьшенное представление закрывается при нажатии кнопки «Назад». Для среды выполнения Windows 8.x приложения нет без обработки встроенной кнопки "Назад", поэтому вопрос не применяется.

## <a name="settings"></a>Параметры

Среда выполнения Windows 8.x **SettingsPane** класс не подходит для Windows 10. Вместо этого, в дополнение к построению страницы параметров, необходимо предоставить пользователям способ получать доступ к ней из вашего приложения. Рекомендуется представить страницу параметров приложения на верхнем уровне, как последний закрепленный элемент на панели навигации, но ниже приводится полный набор доступных вариантов.

-   Панель навигации. Страница параметров должна быть последним элементом в навигационном списке вариантов выбора и закреплена внизу.
-   Панель приложения или панель инструментов (в представлении вкладки или макете сводки). Страница параметров должна быть последним элементом во всплывающем меню панели приложения или панели инструментов. Не рекомендуется, чтобы страница параметров была одним из элементов верхнего уровня на панели навигации.
-   Hub. Страница параметров должна быть расположена внутри всплывающего меню (возможно, из меню панели приложения или меню панели инструментов в макете Центра).

Также не рекомендуется, чтобы располагать параметры на панели основных и подробных данных.

Страница параметров должна полностью заполнять окно приложения и также располагаться в разделе сведений о приложении и отзывов. Рекомендации по проектированию страницы параметров см. в разделе [Рекомендации по параметрам приложений](https://docs.microsoft.com/windows/uwp/app-settings/guidelines-for-app-settings).

## <a name="text"></a>Text

Текст (или шрифтовое оформление) является важным аспектом для приложения UWP. Во время переноса, возможно, необходимо будет пересмотреть визуальные элементы в представлениях, чтобы они соответствовали новому языку дизайна. Используйте эти иллюстрации, чтобы найти доступные стили системы UWP **TextBlock**. Найти те, которые соответствуют стили Windows Phone Silverlight, которые вы использовали. Кроме того можно создавать собственные универсальные стили и скопировать свойства из системы стилей Windows Phone Silverlight в биты.

![стили системы textblock для приложений для windows 10](images/label-uwp10stylegallery.png) <br/>Системные стили TextBlock для приложений Windows 10

В приложениях среды выполнения Windows 8.x и Windows Phone Store семейство шрифтов по умолчанию — глобальный пользовательский интерфейс. В приложении Windows 10 семейство шрифтов по умолчанию — Segoe UI. В результате метрика шрифта в приложении может выглядеть иначе. Если вы хотите воспроизвести вид текста 8.1, то можно настроить собственную метрику с помощью свойств, например, [**LineHeight**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.textblock.lineheight) и [**LineStackingStrategy**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.textblock.linestackingstrategy).

В приложениях среды выполнения Windows 8.x и Windows Phone Store, имеет значение языка по умолчанию для языка сборки или en-us. В приложении Windows 10 язык по умолчанию имеет значение языка Топ-приложения (откат шрифтов). Вы можете явно установить [**FrameworkElement.Language**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.frameworkelement.language), но вам будет удобнее использовать резервный вариант шрифта, если вы не установите значение для этого свойства.

Дополнительные сведения см. в разделе [Рекомендации по шрифтам](https://docs.microsoft.com/windows/uwp/controls-and-patterns/fonts) и [Оформление приложений UWP](https://go.microsoft.com/fwlink/p/?LinkID=533896). Сведения об изменениях элементов управления текстом также см. в разделе [Элементы управления](#controls-and-control-styles-and-templates) выше.

## <a name="theme-changes"></a>Изменения тем

Для универсальных приложений версии 8.1 по умолчанию используется темная тема. Для устройств Windows 10 тему по умолчанию был изменен, но вы можете управлять используемой темы, объявив запрошенной темы в файле App.xaml. Например, чтобы на всех устройствах отображалась темная тема, добавьте `RequestedTheme="Dark"` к корневому элементу Application.

## <a name="tiles-and-toasts"></a>Плитки и всплывающие уведомления

Плитки и всплывающие уведомления шаблоны, которые вы сейчас используете будут продолжать работать в приложении Windows 10. Однако есть и новые, адаптивные шаблоны, описанные в разделе [Уведомления, плитки, всплывающие уведомления и индикаторы событий](https://docs.microsoft.com/windows/uwp/controls-and-patterns/tiles-badges-notifications).

Ранее на настольных компьютерах всплывающее уведомление было временным сообщением. Через какое-то время или после его игнорирования оно исчезало и больше было не доступно. Если всплывающее уведомление игнорируется или временно закрывается в Windows Phone, оно поступает в центр поддержки. Теперь центр поддержки доступен не только семейству мобильных устройств.

Чтобы отправить всплывающее уведомление, больше не требуется декларировать возможность.

## <a name="window-size"></a>Размер окна

Для универсальных приложений версии 8.1 элемент манифеста приложения [**ApplicationView**](https://docs.microsoft.com/uwp/schemas/appxpackage/appxmanifestschema2013/element-applicationview) используется для объявления минимальной ширины окна. В приложении UWP можно задать минимальный размер окна (ширину и высоту) с помощью императивного кода. Стандартный минимальный размер — 500 x 320 эфф. пикселей. Это также самый маленький из допустимых минимальных размеров. Самый большой из допустимых минимальных размеров — 500 x 500 эффективных пикселей.

```csharp
   Windows.UI.ViewManagement.ApplicationView.GetForCurrentView().SetPreferredMinSize
        (new Size { Width = 500, Height = 500 });
```

Следующий раздел называется [Перенос для ввода-вывода, устройства и модели приложения](w8x-to-uwp-input-and-sensors.md).

