---
Description: Используйте класс Аппвиндов для просмотра различных частей приложения в отдельных окнах.
title: Использование класса Аппвиндов для отображения дополнительных окон для приложения
ms.date: 07/19/2019
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 9b89d9100157cf40266bb983e258aa187f65dc93
ms.sourcegitcommit: 789bfe3756c5c47f7324b96f482af636d12c0ed3
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/09/2019
ms.locfileid: "68867462"
---
# <a name="show-multiple-views-with-appwindow"></a>Отображение нескольких представлений с помощью Аппвиндов

[Аппвиндов](/uwp/api/windows.ui.windowmanagement.appwindow) и связанные с ним интерфейсы API упрощают создание приложений с несколькими окнами, позволяя отображать содержимое приложения во вторичных окнах, одновременно работая в одном ПОТОКЕ пользовательского интерфейса в каждом окне.

> [!NOTE]
> Аппвиндов сейчас находится на этапе предварительной версии. Это означает, что вы можете отправлять приложения, использующие Аппвиндов, в магазин, но некоторые компоненты платформы и платформы не работают с Аппвиндов (см. раздел [ограничения](/uwp/api/windows.ui.windowmanagement.appwindow#limitations)).

Здесь мы рассмотрим некоторые сценарии для нескольких окон с помощью примера приложения с именем `HelloAppWindow`. Пример приложения демонстрирует следующие функциональные возможности.

- Открепите элемент управления от главной страницы и откройте его в новом окне.
- Открытие новых экземпляров страницы в новых окнах.
- Программное изменение размера и расположения новых окон в приложении.
- Свяжите Контентдиалог с соответствующим окном в приложении.

![Пример приложения с одним окном](images/hello-app-window-single.png)
  
> _Пример приложения с одним окном_

![Пример приложения с незакрепленной палитрой цветов и дополнительным окном](images/hello-app-window-multi.png)

> _Пример приложения с незакрепленной палитрой цветов и дополнительным окном_

> **Важные API**: [Пространство имен Windows. UI. виндовманажемент](/uwp/api/windows.ui.windowmanagement), [класс аппвиндов](/uwp/api/windows.ui.windowmanagement.appwindow)

## <a name="api-overview"></a>Обзор языка API

Класс [аппвиндов](/uwp/api/windows.ui.windowmanagement.appwindow) и другие интерфейсы API в пространстве имен [виндовманажемент](/uwp/api/windows.ui.windowmanagement) доступны начиная с Windows 10, версии 1903 (пакет SDK 18362). Если приложение предназначено для более ранних версий Windows 10, необходимо [использовать аппликатионвиев для создания вторичных Windows](application-view.md). API-интерфейсы Виндовманажемент все еще находятся в разработке и имеют [ограничения](/uwp/api/windows.ui.windowmanagement.appwindow#limitations) , описанные в документации по API.

Ниже приведены некоторые важные API, используемые для отображения содержимого в Аппвиндов.

### <a name="appwindow"></a>AppWindow

Класс [аппвиндов](/uwp/api/windows.ui.windowmanagement.appwindow) можно использовать для вывода части приложения среда выполнения Windows во вторичном окне. Он похож на концепцию [аппликатионвиев](/uwp/api/windows.ui.viewmanagement.applicationview), но не то же самое в поведении и времени жизни. Основной функцией Аппвиндов является то, что каждый экземпляр использует один и тот же поток обработки пользовательского интерфейса (в том числе диспетчер событий), из которого они были созданы, что упрощает приложения с несколькими окнами.

Вы можете подключать содержимое XAML только к Аппвиндов, но не поддерживать собственное содержимое DirectX или Holographic. Однако можно отобразить [SWAPCHAINPANEL](/uwp/api/windows.ui.xaml.controls.swapchainpanel) XAML, где размещается содержимое DirectX.

### <a name="windowingenvironment"></a>WindowingEnvironment

API [виндовинженвиронмент](/uwp/api/windows.ui.windowmanagement.windowingenvironment) позволяет узнать о среде, в которой представлено ваше приложение, чтобы адаптировать приложение по мере необходимости. В нем описывается тип окна, поддерживаемого средой. Например, `Overlapped` если приложение работает на компьютере или `Tiled` приложение работает на Xbox. Он также предоставляет набор объектов Дисплайрегион, описывающих области, в которых приложение может отображаться на логическом дисплее.

### <a name="displayregion"></a>DisplayRegion

API [дисплайрегион](/uwp/api/windows.ui.windowmanagement.displayregion) описывает область, в которой представление может отображаться для пользователя на логическом дисплее. Например, на настольном ПК это полный экран за вычетом области панели задач. Это необязательное сопоставление 1:1 с физической областью отображения резервного монитора. В одном мониторе может быть несколько областей отображения, или Дисплайрегион может быть настроен на несколько мониторов, если эти мониторы являются однородными во всех аспектах.

### <a name="appwindowpresenter"></a>AppWindowPresenter

API [аппвиндовпресентер](/uwp/api/windows.ui.windowmanagement.appwindowpresenter) позволяет легко переключать окна на заранее определенные конфигурации, такие `FullScreen` как `CompactOverlay`или. Эти конфигурации обеспечивают единообразную работу пользователей на любом устройстве, поддерживающем конфигурацию.

### <a name="uicontext"></a>UIContext

[UIContext](/uwp/api/windows.ui.uicontext) — это уникальный идентификатор для окна приложения или представления. Он создается автоматически, и для получения UIContext можно использовать свойство [UIElement. UIContext](/uwp/api/windows.ui.xaml.uielement.uicontext) . Каждый UIElement в дереве XAML имеет одинаковый UIContext.

 UIContext важен, так как интерфейсы API, такие как [Window. Current](/uwp/api/Windows.UI.Xaml.Window.Current) и Pattern, `GetForCurrentView` полагаются на наличие одного аппликатионвиев или CoreWindow с одним деревом XAML на каждый поток для работы. Это не так при использовании Аппвиндов, поэтому вместо этого используется UIContext для обнаружения определенного окна.

### <a name="xamlroot"></a>XamlRoot

Класс [ксамлрут](/uwp/api/windows.ui.xaml.xamlroot) содержит дерево элементов XAML, подключает его к объекту узла окна (например, [аппвиндов](/uwp/api/windows.ui.windowmanagement.appwindow) или [аппликатионвиев](/uwp/api/windows.ui.viewmanagement.applicationview)) и предоставляет такие сведения, как размер и видимость. Вы не создаете объект Ксамлрут напрямую. Вместо этого он создается при присоединении элемента XAML к Аппвиндов. Затем можно использовать свойство [UIElement. ксамлрут](/uwp/api/windows.ui.xaml.uielement.xamlroot) для получения ксамлрут.

Дополнительные сведения о UIContext и Ксамлрут см. в разделе [Создание переносимого кода в оконных узлах](show-multiple-views.md#make-code-portable-across-windowing-hosts).

## <a name="show-a-new-window"></a>Отобразить новое окно

Давайте взглянем на шаги по отображению содержимого в новом Аппвиндов.

**Отображение нового окна**

1. Вызовите статический метод [аппвиндов. трикреатеасинк](/uwp/api/windows.ui.windowmanagement.appwindow.trycreateasync) , чтобы создать новый [аппвиндов](/uwp/api/windows.ui.windowmanagement.appwindow).

    ```csharp
    AppWindow appWindow = await AppWindow.TryCreateAsync();
    ```

1. Создание содержимого окна.

    Как правило, вы создаете [фрейм](/uwp/api/Windows.UI.Xaml.Controls.Frame)XAML, затем переходите по кадру на [страницу](/uwp/api/Windows.UI.Xaml.Controls.Page) XAML, где вы определили содержимое приложения. Дополнительные сведения о кадрах и страницах см. [в разделе одноранговая Навигация между двумя страницами](../basics/navigate-between-two-pages.md).

    ```csharp
    Frame appWindowContentFrame = new Frame();
    appWindowContentFrame.Navigate(typeof(AppWindowMainPage));
    ```

    Однако можно отобразить любое содержимое XAML в Аппвиндов, а не только кадр и страницу. Например, можно отобразить только один элемент управления, например [ColorPicker](/uwp/api/windows.ui.xaml.controls.colorpicker), или [SwapChainPanel](/uwp/api/windows.ui.xaml.controls.swapchainpanel) , где размещается содержимое DirectX.

1. Вызовите метод [елементкомпоситионпревиев. сетаппвиндовконтент](/api/windows.ui.xaml.hosting.elementcompositionpreview.setappwindowcontent) , чтобы присоединить содержимое XAML к аппвиндов.

    ```csharp
    ElementCompositionPreview.SetAppWindowContent(appWindow, appWindowContentFrame);
    ```

    Вызов этого метода создает объект [ксамлрут](/uwp/api/windows.ui.xaml.xamlroot) и задает его в качестве свойства [Ксамлрут](/uwp/api/windows.ui.xaml.uielement.xamlroot) для указанного UIElement.

    Этот метод можно вызвать только один раз для каждого экземпляра Аппвиндов. После задания содержимого дальнейшие вызовы Сетаппвиндовконтент для этого экземпляра Аппвиндов завершатся ошибкой. Кроме того, если вы попытаетесь отключить содержимое Аппвиндов, передав в него пустой объект UIElement, вызов завершится ошибкой.

1. Вызовите метод [аппвиндов. тришовасинк](/uwp/api/windows.ui.windowmanagement.appwindow.tryshowasync) , чтобы отобразить новое окно.

    ```csharp
    await appWindow.TryShowAsync();
    ```

## <a name="release-resources-when-a-window-is-closed"></a>Освобождение ресурсов при закрытии окна

Следует всегда выполнять обработку события [аппвиндов. Closed](/uwp/api/windows.ui.windowmanagement.appwindow.closed) , чтобы освободить ресурсы XAML (содержимое аппвиндов) и ссылаться на аппвиндов.

```csharp
appWindow.Closed += delegate
{
    appWindowContentFrame.Content = null;
    appWindow = null;
};
```

## <a name="track-instances-of-appwindow"></a>Отслеживание экземпляров Аппвиндов

В зависимости от того, как вы используете несколько окон в приложении, вы можете или не обязаны отследить созданные экземпляры Аппвиндов. В `HelloAppWindow` примере показаны некоторые различные способы использования [аппвиндов](/uwp/api/windows.ui.windowmanagement.appwindow). Здесь мы рассмотрим, почему эти окна должны быть отслеживанием, и как это сделать.

### <a name="simple-tracking"></a>Простое отслеживание

В окне палитры цветов размещается один элемент управления XAML и код для взаимодействия с палитрой выбора цвета, расположенный в `MainPage.xaml.cs` файле. Окно выбора цвета допускает только один экземпляр и является, по сути, расширением `MainWindow`. Чтобы убедиться, что создан только один экземпляр, окно выбора цвета будет отслеживанием с помощью переменной уровня страницы. Прежде чем создавать новое окно палитры цветов, проверьте, существует ли экземпляр, и, если это так, пропустите шаги по созданию нового окна и просто вызовите [тришовасинк](/uwp/api/windows.ui.windowmanagement.appwindow.tryshowasync) в существующем окне.

```csharp
AppWindow colorPickerAppWindow;

// ...

private async void DetachColorPickerButton_Click(object sender, RoutedEventArgs e)
{
    // Create the color picker window.
    if (colorPickerAppWindow == null)
    {
        // ...
        // Create a new window
        colorPickerAppWindow = await AppWindow.TryCreateAsync();
        // ...
    }
    // Show the window.
    await colorPickerAppWindow.TryShowAsync();
}
```

### <a name="track-an-appwindow-instance-in-its-hosted-content"></a>Отслеживание экземпляра Аппвиндов в его размещенном содержимом

В `AppWindowPage` этом окне размещается полная страница XAML, и код для взаимодействия со страницей находится в `AppWindowPage.xaml.cs`. Это позволяет создавать несколько экземпляров, каждая из которых является независимой.

Функциональные возможности страницы позволяют управлять окном, присвоив ему `FullScreen` значение или `CompactOverlay`, а также прослушивать события [аппвиндов. changes](/uwp/api/windows.ui.windowmanagement.appwindow.changed) для отображения сведений о окне. Для вызова этих API-интерфейсов `AppWindowPage` требуется ссылка на экземпляр аппвиндов, на котором он размещен.

Если это все, что вам нужно, можно создать свойство в `AppWindowPage` и присвоить ему экземпляр [аппвиндов](/uwp/api/windows.ui.windowmanagement.appwindow) при его создании.

**AppWindowPage.xaml.cs**

В `AppWindowPage`создайте свойство для хранения ссылки аппвиндов.

```csharp
public sealed partial class AppWindowPage : Page
{
    public AppWindow MyAppWindow { get; set; }

    // ...
}
```

**MainPage.xaml.cs**

В `MainPage`получите ссылку на экземпляр страницы и присвойте вновь созданную аппвиндов свойству в `AppWindowPage`.

```csharp
private async void ShowNewWindowButton_Click(object sender, RoutedEventArgs e)
{
    // Create a new window.
    AppWindow appWindow = await AppWindow.TryCreateAsync();

    // Create a Frame and navigate to the Page you want to show in the new window.
    Frame appWindowContentFrame = new Frame();
    appWindowContentFrame.Navigate(typeof(AppWindowPage));

    // Get a reference to the page instance and assign the
    // newly created AppWindow to the MyAppWindow property.
    AppWindowPage page = (AppWindowPage)appWindowContentFrame.Content;
    page.MyAppWindow = appWindow;

    // ...
}
```

### <a name="tracking-app-windows-using-uicontext"></a>Отслеживание окон приложений с помощью UIContext

Также может потребоваться доступ к экземплярам [аппвиндов](/uwp/api/windows.ui.windowmanagement.appwindow) из других частей приложения. Например, `MainPage` может иметь кнопку "закрыть все", которая закрывает все записанные экземпляры аппвиндов.

В этом случае следует использовать уникальный идентификатор [UIContext](/uwp/api/windows.ui.uicontext) для трассировки экземпляров окна в [словаре](/dotnet/api/system.collections.generic.dictionary-2?view=dotnet-uwp-10.0).

**MainPage.xaml.cs**

В `MainPage`создайте словарь как статическое свойство. Затем добавьте страницу в словарь при ее создании и удалите ее при закрытии страницы. UIContext можно получить из [фрейма](/uwp/api/Windows.UI.Xaml.Controls.Frame) содержимого (`appWindowContentFrame.UIContext`) после вызова [ElementCompositionPreview.SetAppWindowContent](/api/windows.ui.xaml.hosting.elementcompositionpreview.setappwindowcontent).

```csharp
public sealed partial class MainPage : Page
{
    // Track open app windows in a Dictionary.
    public static Dictionary<UIContext, AppWindow> AppWindows { get; set; }
        = new Dictionary<UIContext, AppWindow>();

    // ...

    private async void ShowNewWindowButton_Click(object sender, RoutedEventArgs e)
    {
        // Create a new window.
        AppWindow appWindow = await AppWindow.TryCreateAsync();

        // Create a Frame and navigate to the Page you want to show in the new window.
        Frame appWindowContentFrame = new Frame();
        appWindowContentFrame.Navigate(typeof(AppWindowPage));

        // Attach the XAML content to the window.
        ElementCompositionPreview.SetAppWindowContent(appWindow, appWindowContentFrame);

        // Add the new page to the Dictionary using the UIContext as the Key.
        AppWindows.Add(appWindowContentFrame.UIContext, appWindow);
        appWindow.Title = "App Window " + AppWindows.Count.ToString();

        // When the window is closed, be sure to release
        // XAML resources and the reference to the window.
        appWindow.Closed += delegate
        {
            MainPage.AppWindows.Remove(appWindowContentFrame.UIContext);
            appWindowContentFrame.Content = null;
            appWindow = null;
        };

        // Show the window.
        await appWindow.TryShowAsync();
    }

    private async void CloseAllButton_Click(object sender, RoutedEventArgs e)
    {
        while (AppWindows.Count > 0)
        {
            await AppWindows.Values.First().CloseAsync();
        }
    }
    // ...
}
```

**AppWindowPage.xaml.cs**

Чтобы использовать экземпляр [аппвиндов](/uwp/api/windows.ui.windowmanagement.appwindow) в `AppWindowPage` коде, используйте [UIContext](/uwp/api/windows.ui.uicontext) страницы, чтобы извлечь ее из статического словаря в `MainPage`. Это следует сделать в обработчике событий [загруженной](/uwp/api/windows.ui.xaml.frameworkelement.loaded) страницы, а не в конструкторе, чтобы UIContext не был равен null. UIContext можно получить на странице: `this.UIContext`.

```csharp
public sealed partial class AppWindowPage : Page
{
    AppWindow window;

    // ...
    public AppWindowPage()
    {
        this.InitializeComponent();

        Loaded += AppWindowPage_Loaded;
    }

    private void AppWindowPage_Loaded(object sender, RoutedEventArgs e)
    {
        // Get the reference to this AppWindow that was stored when it was created.
        window = MainPage.AppWindows[this.UIContext];

        // Set up event handlers for the window.
        window.Changed += Window_Changed;
    }
    // ...
}
```

> [!NOTE]
> В `HelloAppWindow` примере показаны оба способа отслеживании окна в `AppWindowPage`, но обычно используется одно или другое, а не оба.

## <a name="request-window-size-and-placement"></a>Размер и размещение окна запроса

Класс [аппвиндов](/uwp/api/windows.ui.windowmanagement.appwindow) имеет несколько методов, которые можно использовать для управления размером и размещением окна. Как предполагается именами методов, система может или не учитывать запрошенные изменения в зависимости от факторов среды.

Вызовите [рекуестсизе](/uwp/api/windows.ui.windowmanagement.appwindow.requestsize) , чтобы указать желаемый размер окна, например.

```csharp
colorPickerAppWindow.RequestSize(new Size(300, 428));
```

Методы управления размещением окон называются _рекуестмове *_ : [Рекуестмовеаджаценттокуррентвиев](/uwp/api/windows.ui.windowmanagement.appwindow.requestmoveadjacenttocurrentview), [рекуестмовеаджаценттовиндов](/uwp/api/windows.ui.windowmanagement.appwindow.requestmoveadjacenttowindow), [рекуестмоверелативетодисплайрегион](/uwp/api/windows.ui.windowmanagement.appwindow.requestmoverelativetodisplayregion), [RequestMoveToDisplayRegion](/uwp/api/windows.ui.windowmanagement.appwindow.requestmovetodisplayregion).

В этом примере код перемещает окно в главное представление, из которого порождено окно.

```csharp
colorPickerAppWindow.RequestMoveAdjacentToCurrentView();
```

Чтобы получить сведения о текущем размере и размещении окна, вызовите метод [onplace](/uwp/api/windows.ui.windowmanagement.appwindow.getplacement). Он возвращает объект [аппвиндовплацемент](/uwp/api/windows.ui.windowmanagement.appwindowplacement) , предоставляющий текущую [дисплайрегион](/uwp/api/windows.ui.windowmanagement.appwindowplacement.displayregion), [смещение](/uwp/api/windows.ui.windowmanagement.appwindowplacement.offset)и [Размер](/uwp/api/windows.ui.windowmanagement.appwindowplacement.size) окна.

Например, можно вызвать этот код, чтобы переместить окно в правый верхний угол экрана. Этот код должен вызываться после отображения окна; в противном случае размер окна, возвращаемый вызовом метода Place, будет иметь значение 0, 0, а смещение будет неправильным.

```csharp
DisplayRegion displayRegion = window.GetPlacement().DisplayRegion;
double displayRegionWidth = displayRegion.WorkAreaSize.Width;
double windowWidth = window.GetPlacement().Size.Width;
int horizontalOffset = (int)(displayRegionWidth - windowWidth);
window.RequestMoveRelativeToDisplayRegion(displayRegion, new Point(horizontalOffset, 0));
```

## <a name="request-a-presentation-configuration"></a>Запрос конфигурации представления

Класс [аппвиндовпресентер](/uwp/api/windows.ui.windowmanagement.appwindowpresenter) позволяет показать [аппвиндов](/uwp/api/windows.ui.windowmanagement.appwindow) , используя предварительно определенную конфигурацию, подходящую для устройства, на котором оно показано. Можно использовать значение [аппвиндовпресентатионконфигуратион](/uwp/api/windows.ui.windowmanagement.appwindowpresentationconfiguration) для размещения окна в `FullScreen` режиме или. `CompactOverlay`

В этом примере показано, как выполнить следующие действия.

- Используйте событие [аппвиндов. Changed](/uwp/api/windows.ui.windowmanagement.appwindow.changed) , чтобы получать уведомления при изменении доступных презентаций окон.
- Используйте свойство [аппвиндов. Presenter](/uwp/api/windows.ui.windowmanagement.appwindow.presenter) для получения текущего [аппвиндовпресентер](/uwp/api/windows.ui.windowmanagement.appwindowpresenter).
- Вызовите [испресентатионсуппортед](/uwp/api/windows.ui.windowmanagement.appwindowpresenter.ispresentationsupported) , чтобы узнать, поддерживается ли конкретный [аппвиндовпресентатионкинд](/uwp/api/windows.ui.windowmanagement.appwindowpresentationkind) .
- Вызовите метод [конфигурации](/uwp/api/windows.ui.windowmanagement.appwindowpresenter.getconfiguration) , чтобы проверить, какой тип конфигурации используется в данный момент.
- Вызовите [рекуестпресентатион](/uwp/api/windows.ui.windowmanagement.appwindowpresenter.requestpresentation) , чтобы изменить текущую конфигурацию.

```csharp
private void Window_Changed(AppWindow sender, AppWindowChangedEventArgs args)
{
    if (args.DidAvailableWindowPresentationsChange)
    {
        EnablePresentationButtons(sender);
    }

    if (args.DidWindowPresentationChange)
    {
        ConfigText.Text = window.Presenter.GetConfiguration().Kind.ToString();
    }

    if (args.DidSizeChange)
    {
        SizeText.Text = window.GetPlacement().Size.ToString();
    }
}

private void EnablePresentationButtons(AppWindow window)
{
    // Check whether the current AppWindowPresenter supports CompactOverlay.
    if (window.Presenter.IsPresentationSupported(AppWindowPresentationKind.CompactOverlay))
    {
        // Show the CompactOverlay button...
        compactOverlayButton.Visibility = Visibility.Visible;
    }
    else
    {
        // Hide the CompactOverlay button...
        compactOverlayButton.Visibility = Visibility.Collapsed;
    }

    // Check whether the current AppWindowPresenter supports FullScreen?
    if (window.Presenter.IsPresentationSupported(AppWindowPresentationKind.FullScreen))
    {
        // Show the FullScreen button...
        fullScreenButton.Visibility = Visibility.Visible;
    }
    else
    {
        // Hide the FullScreen button...
        fullScreenButton.Visibility = Visibility.Collapsed;
    }
}

private void CompactOverlayButton_Click(object sender, RoutedEventArgs e)
{
    if (window.Presenter.GetConfiguration().Kind != AppWindowPresentationKind.CompactOverlay)
    {
        window.Presenter.RequestPresentation(AppWindowPresentationKind.CompactOverlay);
        fullScreenButton.IsChecked = false;
    }
    else
    {
        window.Presenter.RequestPresentation(AppWindowPresentationKind.Default);
    }
}

private void FullScreenButton_Click(object sender, RoutedEventArgs e)
{
    if (window.Presenter.GetConfiguration().Kind != AppWindowPresentationKind.FullScreen)
    {
        window.Presenter.RequestPresentation(AppWindowPresentationKind.FullScreen);
        compactOverlayButton.IsChecked = false;
    }
    else
    {
        window.Presenter.RequestPresentation(AppWindowPresentationKind.Default);
    }
}
```

## <a name="reuse-xaml-elements"></a>Повторное использование элементов XAML

[Аппвиндов](/uwp/api/windows.ui.windowmanagement.appwindow) позволяет использовать несколько деревьев XAML с одним и тем же потоком пользовательского интерфейса. Однако элемент XAML может быть добавлен только один раз в дерево XAML. Если вы хотите переместить часть пользовательского интерфейса из одного окна в другое, необходимо управлять размещением в дереве XAML.

В этом примере показано, как повторно использовать элемент управления [ColorPicker](/uwp/api/windows.ui.xaml.controls.colorpicker) , перемещая его между главным и дополнительным окнами.

Средство выбора цвета объявляется в XAML для `MainPage`, который помещает его `MainPage` в дерево XAML.

```xaml
<StackPanel x:Name="colorPickerContainer" Grid.Column="1" Background="WhiteSmoke">
    <Button Click="DetachColorPickerButton_Click" HorizontalAlignment="Right">
        <FontIcon FontFamily="Segoe MDL2 Assets" Glyph="&#xE2B4;" />
    </Button>
    <ColorPicker x:Name="colorPicker" Margin="12" Width="288"
                 IsColorChannelTextInputVisible="False"
                 ColorChanged="ColorPicker_ColorChanged"/>
</StackPanel>
```

Когда палитра выбора цвета отсоединяется для размещения в новом аппвиндов, сначала необходимо удалить его из `MainPage` дерева XAML, удалив его из родительского контейнера. Хотя этот пример не является обязательным, он также скрывает родительский контейнер.

```csharp
colorPickerContainer.Children.Remove(colorPicker);
colorPickerContainer.Visibility = Visibility.Collapsed;
```

Затем его можно добавить в новое дерево XAML. Здесь сначала создается [Сетка](/uwp/api/windows.ui.xaml.controls.grid) , которая будет родительским контейнером для ColorPicker, и добавляется компонент ColorPicker в качестве дочернего элемента сетки. (Это позволяет легко удалить компонент ColorPicker из этого дерева XAML позже.) Затем вы установите сетку в качестве корневого элемента дерева XAML в новом окне.

```csharp
Grid appWindowRootGrid = new Grid();
appWindowRootGrid.Children.Add(colorPicker);

// Create a new window
colorPickerAppWindow = await AppWindow.TryCreateAsync();

// Attach the XAML content to our window
ElementCompositionPreview.SetAppWindowContent(colorPickerAppWindow, appWindowRootGrid);
```

Когда [аппвиндов](/uwp/api/windows.ui.windowmanagement.appwindow) закрывается, процесс выполняется в противоположном порядке. Сначала удалите компонент [ColorPicker](/uwp/api/windows.ui.xaml.controls.colorpicker) из [сетки](/uwp/api/windows.ui.xaml.controls.grid), а затем добавьте его в качестве дочернего элемента для [StackPanel](/uwp/api/windows.ui.xaml.controls.stackpanel) в `MainPage`.

```csharp
// When the window is closed, be sure to release XAML resources
// and the reference to the window.
colorPickerAppWindow.Closed += delegate
{
    appWindowRootGrid.Children.Remove(colorPicker);
    appWindowRootGrid = null;
    colorPickerAppWindow = null;

    colorPickerContainer.Children.Add(colorPicker);
    colorPickerContainer.Visibility = Visibility.Visible;
};
```

```csharp
private async void DetachColorPickerButton_Click(object sender, RoutedEventArgs e)
{
    ColorPickerContainer.Visibility = Visibility.Collapsed;

    // Create the color picker window.
    if (colorPickerAppWindow == null)
    {
        ColorPickerContainer.Children.Remove(colorPicker);

        Grid appWindowRootGrid = new Grid();
        appWindowRootGrid.Children.Add(colorPicker);

        // Create a new window
        colorPickerAppWindow = await AppWindow.TryCreateAsync();
        colorPickerAppWindow.RequestMoveAdjacentToCurrentView();
        colorPickerAppWindow.RequestSize(new Size(300, 428));
        colorPickerAppWindow.Title = "Color picker";

        // Attach the XAML content to our window
        ElementCompositionPreview.SetAppWindowContent(colorPickerAppWindow, appWindowRootGrid);

        // When the window is closed, be sure to release XAML resources
        // and the reference to the window.
        colorPickerAppWindow.Closed += delegate
        {
            appWindowRootGrid.Children.Remove(colorPicker);
            appWindowRootGrid = null;
            colorPickerAppWindow = null;

            ColorPickerContainer.Children.Add(colorPicker);
            ColorPickerContainer.Visibility = Visibility.Visible;
        };
    }
    // Show the window.
    await colorPickerAppWindow.TryShowAsync();
}
```

## <a name="show-a-dialog-box"></a>Отображение диалогового окна

По умолчанию диалоговые окна содержимого модально связаны с корневым объектом [ApplicationView](/uwp/api/windows.ui.viewmanagement.applicationview). При использовании [контентдиалог](/uwp/api/windows.ui.xaml.controls.contentdialog) в [аппвиндов](/uwp/api/windows.ui.windowmanagement.appwindow)необходимо вручную задать ксамлрут в диалоговом окне в качестве корня узла XAML.

Для этого задайте для свойства [Ксамлрут](/uwp/api/windows.ui.xaml.uielement.xamlroot) контентдиалог тот же [ксамлрут](/uwp/api/windows.ui.xaml.xamlroot) , что и для элемента, который уже находится в аппвиндов. Здесь этот код находится внутри обработчика событий [Click](/uwp/api/windows.ui.xaml.controls.primitives.buttonbase.click) кнопки, поэтому для получения ксамлрут можно использовать кнопку _отправитель_ (нажата кнопка).

```csharp
if (ApiInformation.IsApiContractPresent("Windows.Foundation.UniversalApiContract", 8))
{
    simpleDialog.XamlRoot = ((Button)sender).XamlRoot;
}
```

Если в дополнение к главному окну (Аппликатионвиев) открыто одно или несколько Аппвиндовс, то каждое окно может попытаться открыть диалоговое окно, так как модальное диалоговое окно блокирует только окно, в котором оно было основано. Однако одновременно может быть открыто только один [контентдиалог](/uwp/api/windows.ui.xaml.controls.contentdialog) для каждого потока. Попытка открыть два элемента ContentDialogs породит исключение, даже если они пытаются открыться в отдельных объектах AppWindow.

Чтобы управлять этим, следует по крайней мере открыть диалоговое окно в `try/catch` блоке для перехвата исключения в случае, если другое диалоговое окно уже открыто.

```csharp
try
{
    ContentDialogResult result = await simpleDialog.ShowAsync();
}
catch (Exception)
{
    // The dialog didn't open, probably because another dialog is already open.
}
```

Другим способом управления диалоговыми окнами является отслеживание открытого диалогового окна и его закрытие перед попыткой открыть новое диалоговое окно. Здесь для этой цели создается статическое свойство в `MainPage` методе `CurrentDialog` .

```csharp
public sealed partial class MainPage : Page
{
    // Track the last opened dialog so you can close it if another dialog tries to open.
    public static ContentDialog CurrentDialog { get; set; } = null;

   // ...
}
```

Затем вы проверяете, открыто ли сейчас диалоговое окно, и, если есть, вызовите метод [Hide](/uwp/api/windows.ui.xaml.controls.contentdialog.hide) , чтобы закрыть его. Наконец, назначьте новое диалоговое окно `CurrentDialog`и попытайтесь отобразить его.

```csharp
private async void DialogButton_Click(object sender, RoutedEventArgs e)
{
    ContentDialog simpleDialog = new ContentDialog
    {
        Title = "Content dialog",
        Content = "Dialog box for " + window.Title,
        CloseButtonText = "Ok"
    };

    if (MainPage.CurrentDialog != null)
    {
        MainPage.CurrentDialog.Hide();
    }
    MainPage.CurrentDialog = simpleDialog;

    // Use this code to associate the dialog to the appropriate AppWindow by setting
    // the dialog's XamlRoot to the same XamlRoot as an element that is already
    // present in the AppWindow.
    if (ApiInformation.IsApiContractPresent("Windows.Foundation.UniversalApiContract", 8))
    {
        simpleDialog.XamlRoot = ((Button)sender).XamlRoot;
    }

    try
    {
        ContentDialogResult result = await simpleDialog.ShowAsync();
    }
    catch (Exception)
    {
        // The dialog didn't open, probably because another dialog is already open.
    }
}
```

Если нежелательно, чтобы диалоговое окно было закрыто программно, не присваивайте его как `CurrentDialog`. Здесь показано важное диалоговое окно, которое следует отменять только при нажатии кнопки `Ok`. `MainPage` Поскольку он не назначен в `CurrentDialog`качестве, попытки закрыть его программным способом не выполняются.

```csharp
public sealed partial class MainPage : Page
{
    // Track the last opened dialog so you can close it if another dialog tries to open.
    public static ContentDialog CurrentDialog { get; set; } = null;

    // ...
    private async void DialogButton_Click(object sender, RoutedEventArgs e)
    {
        ContentDialog importantDialog = new ContentDialog
        {
            Title = "Important dialog",
            Content = "This dialog can only be dismissed by clicking Ok.",
            CloseButtonText = "Ok"
        };

        if (MainPage.CurrentDialog != null)
        {
            MainPage.CurrentDialog.Hide();
        }
        // Do not track this dialog as the MainPage.CurrentDialog.
        // It should only be closed by clicking the Ok button.
        MainPage.CurrentDialog = null;

        try
        {
            ContentDialogResult result = await importantDialog.ShowAsync();
        }
        catch (Exception)
        {
            // The dialog didn't open, probably because another dialog is already open.
        }
    }
    // ...
}
```

## <a name="complete-code"></a>Полный код

### <a name="mainpagexaml"></a>MainPage.xaml

```xaml
<Page
    x:Class="HelloAppWindow.MainPage"
    xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
    xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
    xmlns:local="using:HelloAppWindow"
    xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
    xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
    mc:Ignorable="d"
    Background="{ThemeResource ApplicationPageBackgroundThemeBrush}">

    <Grid>
        <Grid.ColumnDefinitions>
            <ColumnDefinition/>
            <ColumnDefinition Width="Auto"/>
        </Grid.ColumnDefinitions>
        <StackPanel VerticalAlignment="Center" HorizontalAlignment="Center">
            <Button x:Name="NewWindowButton" Content="Open new window" 
                    Click="ShowNewWindowButton_Click" Margin="0,12"/>
            <Button Content="Open dialog" Click="DialogButton_Click" 
                    HorizontalAlignment="Stretch"/>
            <Button Content="Close all" Click="CloseAllButton_Click" 
                    Margin="0,12" HorizontalAlignment="Stretch"/>
        </StackPanel>

<StackPanel x:Name="colorPickerContainer" Grid.Column="1" Background="WhiteSmoke">
    <Button Click="DetachColorPickerButton_Click" HorizontalAlignment="Right">
        <FontIcon FontFamily="Segoe MDL2 Assets" Glyph="&#xE2B4;" />
    </Button>
            <ColorPicker x:Name="colorPicker" Margin="12" Width="288"
                 IsColorChannelTextInputVisible="False"
                 ColorChanged="ColorPicker_ColorChanged"/>
        </StackPanel>
    </Grid>
</Page>

```

### <a name="mainpagexamlcs"></a>MainPage.xaml.cs

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using Windows.Foundation;
using Windows.UI;
using Windows.UI.WindowManagement;
using Windows.UI.Xaml;
using Windows.UI.Xaml.Controls;
using Windows.UI.Xaml.Hosting;
using Windows.UI.Xaml.Media;

// The Blank Page item template is documented at https://go.microsoft.com/fwlink/?LinkId=402352&clcid=0x409

namespace HelloAppWindow
{
    /// <summary>
    /// An empty page that can be used on its own or navigated to within a Frame.
    /// </summary>
    public sealed partial class MainPage : Page
    {
        AppWindow colorPickerAppWindow;

        // Track open app windows in a Dictionary.
        public static Dictionary<UIContext, AppWindow> AppWindows { get; set; }
            = new Dictionary<UIContext, AppWindow>();

        // Track the last opened dialog so you can close it if another dialog tries to open.
        public static ContentDialog CurrentDialog { get; set; } = null;

        public MainPage()
        {
            this.InitializeComponent();
        }

        private async void ShowNewWindowButton_Click(object sender, RoutedEventArgs e)
        {
            // Create a new window.
            AppWindow appWindow = await AppWindow.TryCreateAsync();

            // Create a Frame and navigate to the Page you want to show in the new window.
            Frame appWindowContentFrame = new Frame();
            appWindowContentFrame.Navigate(typeof(AppWindowPage));

            // Get a reference to the page instance and assign the
            // newly created AppWindow to the MyAppWindow property.
            AppWindowPage page = (AppWindowPage)appWindowContentFrame.Content;
            page.MyAppWindow = appWindow;
            page.TextColorBrush = new SolidColorBrush(colorPicker.Color);

            // Attach the XAML content to the window.
            ElementCompositionPreview.SetAppWindowContent(appWindow, appWindowContentFrame);

            // Add the new page to the Dictionary using the UIContext as the Key.
            AppWindows.Add(appWindowContentFrame.UIContext, appWindow);
            appWindow.Title = "App Window " + AppWindows.Count.ToString();

            // When the window is closed, be sure to release XAML resources
            // and the reference to the window.
            appWindow.Closed += delegate
            {
                MainPage.AppWindows.Remove(appWindowContentFrame.UIContext);
                appWindowContentFrame.Content = null;
                appWindow = null;
            };

            // Show the window.
            await appWindow.TryShowAsync();
        }

        private async void DialogButton_Click(object sender, RoutedEventArgs e)
        {
            ContentDialog importantDialog = new ContentDialog
            {
                Title = "Important dialog",
                Content = "This dialog can only be dismissed by clicking Ok.",
                CloseButtonText = "Ok"
            };

            if (MainPage.CurrentDialog != null)
            {
                MainPage.CurrentDialog.Hide();
            }
            // Do not track this dialog as the MainPage.CurrentDialog.
            // It should only be closed by clicking the Ok button.
            MainPage.CurrentDialog = null;

            try
            {
                ContentDialogResult result = await importantDialog.ShowAsync();
            }
            catch (Exception)
            {
                // The dialog didn't open, probably because another dialog is already open.
            }
        }

        private async void DetachColorPickerButton_Click(object sender, RoutedEventArgs e)
        {
            // Create the color picker window.
            if (colorPickerAppWindow == null)
            {
                colorPickerContainer.Children.Remove(colorPicker);
                colorPickerContainer.Visibility = Visibility.Collapsed;

                Grid appWindowRootGrid = new Grid();
                appWindowRootGrid.Children.Add(colorPicker);

                // Create a new window
                colorPickerAppWindow = await AppWindow.TryCreateAsync();
                colorPickerAppWindow.RequestMoveAdjacentToCurrentView();
                colorPickerAppWindow.RequestSize(new Size(300, 428));
                colorPickerAppWindow.Title = "Color picker";

                // Attach the XAML content to our window
                ElementCompositionPreview.SetAppWindowContent(colorPickerAppWindow, appWindowRootGrid);

                // Make sure to release the reference to this window, 
                // and release XAML resources, when it's closed
                colorPickerAppWindow.Closed += delegate
                {
                    appWindowRootGrid.Children.Remove(colorPicker);
                    appWindowRootGrid = null;
                    colorPickerAppWindow = null;

                    colorPickerContainer.Children.Add(colorPicker);
                    colorPickerContainer.Visibility = Visibility.Visible;
                };
            }
            // Show the window.
            await colorPickerAppWindow.TryShowAsync();
        }

        private void ColorPicker_ColorChanged(ColorPicker sender, ColorChangedEventArgs args)
        {
            NewWindowButton.Background = new SolidColorBrush(args.NewColor);
        }

        private async void CloseAllButton_Click(object sender, RoutedEventArgs e)
        {
            while (AppWindows.Count > 0)
            {
                await AppWindows.Values.First().CloseAsync();
            }
        }
    }
}

```

### <a name="appwindowpagexaml"></a>Аппвиндовпаже. XAML

```xaml
<Page
    x:Class="HelloAppWindow.AppWindowPage"
    xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
    xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
    xmlns:local="using:HelloAppWindow"
    xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
    xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
    mc:Ignorable="d"
    Background="{ThemeResource ApplicationPageBackgroundThemeBrush}">

    <Grid>
        <TextBlock x:Name="TitleTextBlock" Text="Hello AppWindow!" FontSize="24" HorizontalAlignment="Center" Margin="24"/>

        <StackPanel VerticalAlignment="Center" HorizontalAlignment="Center">
            <Button Content="Open dialog" Click="DialogButton_Click"
                    Width="200" Margin="0,4"/>
            <Button Content="Move window" Click="MoveWindowButton_Click"
                    Width="200" Margin="0,4"/>
            <ToggleButton Content="Compact Overlay" x:Name="compactOverlayButton" Click="CompactOverlayButton_Click"
                          Width="200" Margin="0,4"/>
            <ToggleButton Content="Full Screen" x:Name="fullScreenButton" Click="FullScreenButton_Click"
                          Width="200" Margin="0,4"/>
            <Grid>
                <TextBlock Text="Size:"/>
                <TextBlock x:Name="SizeText" HorizontalAlignment="Right"/>
            </Grid>
            <Grid>
                <TextBlock Text="Presentation:"/>
                <TextBlock x:Name="ConfigText" HorizontalAlignment="Right"/>
            </Grid>
        </StackPanel>
    </Grid>
</Page>
```

### <a name="appwindowpagexamlcs"></a>AppWindowPage.xaml.cs

```csharp
using System;
using Windows.Foundation;
using Windows.Foundation.Metadata;
using Windows.UI;
using Windows.UI.WindowManagement;
using Windows.UI.Xaml;
using Windows.UI.Xaml.Controls;
using Windows.UI.Xaml.Media;

// The Blank Page item template is documented at https://go.microsoft.com/fwlink/?LinkId=234238

namespace HelloAppWindow
{
    /// <summary>
    /// An empty page that can be used on its own or navigated to within a Frame.
    /// </summary>
    public sealed partial class AppWindowPage : Page
    {
        AppWindow window;

        public AppWindow MyAppWindow { get; set; }

        public SolidColorBrush TextColorBrush { get; set; } = new SolidColorBrush(Colors.Black);

        public AppWindowPage()
        {
            this.InitializeComponent();

            Loaded += AppWindowPage_Loaded;
        }

        private void AppWindowPage_Loaded(object sender, RoutedEventArgs e)
        {
            // Get the reference to this AppWindow that was stored when it was created.
            window = MainPage.AppWindows[this.UIContext];

            // Set up event handlers for the window.
            window.Changed += Window_Changed;

            TitleTextBlock.Foreground = TextColorBrush;
        }

        private async void DialogButton_Click(object sender, RoutedEventArgs e)
        {
            ContentDialog simpleDialog = new ContentDialog
            {
                Title = "Content dialog",
                Content = "Dialog box for " + window.Title,
                CloseButtonText = "Ok"
            };

            if (MainPage.CurrentDialog != null)
            {
                MainPage.CurrentDialog.Hide();
            }
            MainPage.CurrentDialog = simpleDialog;

            // Use this code to associate the dialog to the appropriate AppWindow by setting
            // the dialog's XamlRoot to the same XamlRoot as an element that is already 
            // present in the AppWindow.
            if (ApiInformation.IsApiContractPresent("Windows.Foundation.UniversalApiContract", 8))
            {
                simpleDialog.XamlRoot = ((Button)sender).XamlRoot;
            }

            try
            {
                ContentDialogResult result = await simpleDialog.ShowAsync();
            }
            catch (Exception)
            {
                // The dialog didn't open, probably because another dialog is already open.
            }
        }

        private void Window_Changed(AppWindow sender, AppWindowChangedEventArgs args)
        {
            if (args.DidAvailableWindowPresentationsChange)
            {
                EnablePresentationButtons(sender);
            }

            if (args.DidWindowPresentationChange)
            {
                ConfigText.Text = window.Presenter.GetConfiguration().Kind.ToString();
            }

            if (args.DidSizeChange)
            {
                SizeText.Text = window.GetPlacement().Size.ToString();
            }
        }

        private void EnablePresentationButtons(AppWindow window)
        {
            // Check whether the current AppWindowPresenter supports CompactOverlay.
            if (window.Presenter.IsPresentationSupported(AppWindowPresentationKind.CompactOverlay))
            {
                // Show the CompactOverlay button...
                compactOverlayButton.Visibility = Visibility.Visible;
            }
            else
            {
                // Hide the CompactOverlay button...
                compactOverlayButton.Visibility = Visibility.Collapsed;
            }

            // Check whether the current AppWindowPresenter supports FullScreen?
            if (window.Presenter.IsPresentationSupported(AppWindowPresentationKind.FullScreen))
            {
                // Show the FullScreen button...
                fullScreenButton.Visibility = Visibility.Visible;
            }
            else
            {
                // Hide the FullScreen button...
                fullScreenButton.Visibility = Visibility.Collapsed;
            }
        }

        private void CompactOverlayButton_Click(object sender, RoutedEventArgs e)
        {
            if (window.Presenter.GetConfiguration().Kind != AppWindowPresentationKind.CompactOverlay)
            {
                window.Presenter.RequestPresentation(AppWindowPresentationKind.CompactOverlay);
                fullScreenButton.IsChecked = false;
            }
            else
            {
                window.Presenter.RequestPresentation(AppWindowPresentationKind.Default);
            }
        }

        private void FullScreenButton_Click(object sender, RoutedEventArgs e)
        {
            if (window.Presenter.GetConfiguration().Kind != AppWindowPresentationKind.FullScreen)
            {
                window.Presenter.RequestPresentation(AppWindowPresentationKind.FullScreen);
                compactOverlayButton.IsChecked = false;
            }
            else
            {
                window.Presenter.RequestPresentation(AppWindowPresentationKind.Default);
            }
        }

        private void MoveWindowButton_Click(object sender, RoutedEventArgs e)
        {
            DisplayRegion displayRegion = window.GetPlacement().DisplayRegion;
            double displayRegionWidth = displayRegion.WorkAreaSize.Width;
            double windowWidth = window.GetPlacement().Size.Width;
            int horizontalOffset = (int)(displayRegionWidth - windowWidth);
            window.RequestMoveRelativeToDisplayRegion(displayRegion, new Point(horizontalOffset, 0));
        }
    }
}
```

## <a name="related-topics"></a>См. также

- [Отображение нескольких представлений](show-multiple-views.md)
- [Отображение нескольких представлений с помощью Аппликатионвиев](application-view.md)
