---
Description: Для просмотра различных частей приложения в отдельных окнах следует использовать класс AppWindow.
title: Использование класса AppWindow для отображения дополнительных окон приложения
ms.date: 07/19/2019
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 9b89d9100157cf40266bb983e258aa187f65dc93
ms.sourcegitcommit: 789bfe3756c5c47f7324b96f482af636d12c0ed3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/09/2019
ms.locfileid: "68867462"
---
# <a name="show-multiple-views-with-appwindow"></a>Отображение нескольких представлений с помощью AppWindow

Класс [AppWindow](/uwp/api/windows.ui.windowmanagement.appwindow) и связанные с ним интерфейсы API упрощает создание многооконных приложений, позволяя отображать содержимое приложения в дополнительных окнах и использовать один поток пользовательского интерфейса для всех окон.

> [!NOTE]
> AppWindow сейчас находится на этапе предварительной версии. Это означает, что вы можете отправлять приложения, использующие AppWindow, в Store, но некоторые компоненты платформ не работают с AppWindow (см. раздел [Ограничения](/uwp/api/windows.ui.windowmanagement.appwindow#limitations)).

Здесь показан ряд сценариев использования нескольких окон на основе примера приложения `HelloAppWindow`. Этот пример приложения демонстрирует следующие функциональные возможности:

- открепление элемента управления от главной страницы и его открытие в новом окне;
- открытие новых экземпляров страницы в новых окнах;
- программное изменение размера и расположения новых окон в приложении;
- привязка ContentDialog к соответствующему окну в приложении.

![Пример приложения с одним окном](images/hello-app-window-single.png)
  
> _Пример приложения с одним окном_

![Пример приложения с открепленной палитрой и дополнительным окном](images/hello-app-window-multi.png)

> _Пример приложения с открепленной палитрой и дополнительным окном_

> **Важные API**: [пространство имен Windows.UI.WindowManagement](/uwp/api/windows.ui.windowmanagement), [класс AppWindow](/uwp/api/windows.ui.windowmanagement.appwindow).

## <a name="api-overview"></a>Обзор языка API

Класс [AppWindow](/uwp/api/windows.ui.windowmanagement.appwindow) и другие интерфейсы API в пространстве имен [WindowManagement](/uwp/api/windows.ui.windowmanagement) доступны, начиная с Windows 10 версии 1903 (пакет SDK 18362). Если приложение нацелено на более ранние версии Windows 10, [для создания дополнительных окон необходимо использовать ApplicationView](application-view.md). Интерфейсы API WindowManagement все еще находятся в разработке и имеют [ограничения](/uwp/api/windows.ui.windowmanagement.appwindow#limitations), как описано в справочных материалах по API.

Ниже приведены некоторые важные API, используемые для отображения содержимого в AppWindow.

### <a name="appwindow"></a>AppWindow

Класс [AppWindow](/uwp/api/windows.ui.windowmanagement.appwindow) можно использовать для вывода части приложения среды выполнения Windows в дополнительном окне. Его принцип действия схож с [ApplicationView](/uwp/api/windows.ui.viewmanagement.applicationview), но поведение и время существования отличаются. Основной функционально возможностью AppWindow является то, что каждый экземпляр использует тот же поток обработки пользовательского интерфейса (в том числе диспетчер событий), из которого он был создан, что упрощает многооконные приложения.

Вы можете подключить к AppWindow только содержимое XAML. Собственное содержимое DirectX или Holographic не поддерживается. Однако можно отобразить [SwapChainPanel](/uwp/api/windows.ui.xaml.controls.swapchainpanel) XAML, в котором размещено содержимое DirectX.

### <a name="windowingenvironment"></a>WindowingEnvironment

API [WindowingEnvironment](/uwp/api/windows.ui.windowmanagement.windowingenvironment) позволяет узнать о среде, в которой представлено приложение, чтобы адаптировать приложение, если это необходимо. В нем описывается тип окна, поддерживаемого средой. Например, указывается `Overlapped`, если приложение работает на компьютере, или `Tiled`, если приложение работает на Xbox. Он также предоставляет набор объектов DisplayRegion, описывающих области, в которых приложение может отображаться на логическом дисплее.

### <a name="displayregion"></a>DisplayRegion

API [DisplayRegion](/uwp/api/windows.ui.windowmanagement.displayregion) описывает область, в которой представление может отображаться для пользователя на логическом дисплее. Например, на компьютере это полный экран за вычетом области панели задач. Эта область не обязательно полностью совпадает с физической областью отображения используемого монитора. На одном мониторе может быть несколько областей отображения, или DisplayRegion можно настроить для охвата нескольких мониторов, если эти мониторы обладают одинаковыми характеристиками.

### <a name="appwindowpresenter"></a>AppWindowPresenter

API [AppWindowPresenter](/uwp/api/windows.ui.windowmanagement.appwindowpresenter) позволяет легко включать для окон предварительно определенные конфигурации, такие как `FullScreen` и `CompactOverlay`. Эти конфигурации обеспечивают единообразный пользовательский интерфейс на любом устройстве, поддерживающем конфигурацию.

### <a name="uicontext"></a>UIContext

[UIContext](/uwp/api/windows.ui.uicontext) — это уникальный идентификатор окна или представления приложения. Он создается автоматически, и для получения UIContext можно использовать свойство [UIElement.UIContext](/uwp/api/windows.ui.xaml.uielement.uicontext). Каждый UIElement в дереве XAML имеет одинаковый UIContext.

 UIContext важен, так как такие интерфейсы API, как [Window.Current](/uwp/api/Windows.UI.Xaml.Window.Current) и шаблон `GetForCurrentView`, используют для работы один экземпляр ApplicationView или CoreWindow с одним деревом XAML на поток. Это не так при использовании AppWindow, поэтому вместо этого для идентификации определенного окна используется UIContext.

### <a name="xamlroot"></a>XamlRoot

Класс [XamlRoot](/uwp/api/windows.ui.xaml.xamlroot) содержит дерево элементов XAML, подключает его к объекту размещения окна (например, [AppWindow](/uwp/api/windows.ui.windowmanagement.appwindow) или [ApplicationView](/uwp/api/windows.ui.viewmanagement.applicationview)) и предоставляет такие сведения, как размер и видимость. Вы не создаете объект XamlRoot напрямую. Вместо этого он создается при подключении элемента XAML к AppWindow. Затем можно использовать свойство [UIElement.XamlRoot](/uwp/api/windows.ui.xaml.uielement.xamlroot), чтобы получить XamlRoot.

Дополнительные сведения о UIContext и XamlRoot см. в разделе [Создание кода, переносимого между окнами содержимого](show-multiple-views.md#make-code-portable-across-windowing-hosts).

## <a name="show-a-new-window"></a>Отображение нового окна

Давайте рассмотрим шаги по отображению содержимого в новом экземпляре AppWindow.

**Отображение нового окна**

1. Вызовите статический метод [AppWindow.TryCreateAsync](/uwp/api/windows.ui.windowmanagement.appwindow.trycreateasync), чтобы создать новый экземпляр [AppWindow](/uwp/api/windows.ui.windowmanagement.appwindow).

    ```csharp
    AppWindow appWindow = await AppWindow.TryCreateAsync();
    ```

1. Создайте содержимое окна.

    Как правило, сначала создается [Frame](/uwp/api/Windows.UI.Xaml.Controls.Frame) XAML, а затем выполняется переход Frame в объект [Page](/uwp/api/Windows.UI.Xaml.Controls.Page) XAML, в котором вы определили содержимое приложения. Подробные сведения о фреймах и страницах см. в статье [Реализация навигации между двумя страницами](../basics/navigate-between-two-pages.md).

    ```csharp
    Frame appWindowContentFrame = new Frame();
    appWindowContentFrame.Navigate(typeof(AppWindowMainPage));
    ```

    Тем не менее, в AppWindow можно отобразить любое содержимое XAML, не только кадр и страницу. Например, можно отобразить только один элемент управления, например [ColorPicker](/uwp/api/windows.ui.xaml.controls.colorpicker), или можно отобразить [SwapChainPanel](/uwp/api/windows.ui.xaml.controls.swapchainpanel) с содержимым DirectX.

1. Вызовите метод [ElementCompositionPreview.SetAppWindowContent](/api/windows.ui.xaml.hosting.elementcompositionpreview.setappwindowcontent), чтобы подключить содержимое XAML к AppWindow.

    ```csharp
    ElementCompositionPreview.SetAppWindowContent(appWindow, appWindowContentFrame);
    ```

    Вызов этого метода создает объект [XamlRoot](/uwp/api/windows.ui.xaml.xamlroot) и задает его в качестве свойства [XamlRoot](/uwp/api/windows.ui.xaml.uielement.xamlroot) для указанного UIElement.

    Этот метод можно вызвать только один раз для каждого экземпляра AppWindow. После задания содержимого дальнейшие вызовы SetAppWindowContent для этого экземпляра AppWindow завершатся ошибкой. Кроме того, если вы попытаетесь отключить содержимое AppWindow, передав в него объект UIElement со значением NULL, вызов завершится ошибкой.

1. Вызовите метод [AppWindow.TrySho](/uwp/api/windows.ui.windowmanagement.appwindow.tryshowasync), чтобы отобразить новое окно.

    ```csharp
    await appWindow.TryShowAsync();
    ```

## <a name="release-resources-when-a-window-is-closed"></a>Освобождение ресурсов при закрытии окна

Следует всегда обрабатывать событие [AppWindow.Closed](/uwp/api/windows.ui.windowmanagement.appwindow.closed), чтобы освободить ресурсы XAML (содержимое AppWindow) и ссылки на AppWindow.

```csharp
appWindow.Closed += delegate
{
    appWindowContentFrame.Content = null;
    appWindow = null;
};
```

## <a name="track-instances-of-appwindow"></a>Отслеживание экземпляров AppWindow

В зависимости от того, как вы используете несколько окон в приложении, вы можете отслеживать созданные экземпляры AppWindow или не делать этого. В примере `HelloAppWindow` показано несколько разных способов использования [AppWindow](/uwp/api/windows.ui.windowmanagement.appwindow). Здесь мы рассмотрим, почему эти окна нужно отслеживать, и как это сделать.

### <a name="simple-tracking"></a>Простое отслеживание

В окне палитры размещается один элемент управления XAML, и весь код для взаимодействия с палитрой находится в файле `MainPage.xaml.cs`. Окно палитры допускает только один экземпляр и является, по сути, расширением `MainWindow`. Чтобы убедиться, что создан только один экземпляр, окно палитры отслеживается с помощью переменной уровня страницы. Прежде чем создать новое окно палитры цветов, следует проверить, существует ли его экземпляр, и если это так, пропустить шаги по созданию окна и просто вызвать [TryShowAsync](/uwp/api/windows.ui.windowmanagement.appwindow.tryshowasync) для существующего окна.

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

### <a name="track-an-appwindow-instance-in-its-hosted-content"></a>Отслеживание экземпляра AppWindow в размещенном в нем содержимом

В окне `AppWindowPage` размещается полная страница XAML, а код для взаимодействия со страницей находится в `AppWindowPage.xaml.cs`. Это позволяет создать несколько экземпляров, каждый из которых работает независимо.

Функциональные возможности страницы позволяют управлять окном, устанавливать для него свойство `FullScreen` или `CompactOverlay`, а также ожидать передачи событий [AppWindow.Changed](/uwp/api/windows.ui.windowmanagement.appwindow.changed) для отображения сведений о окне. Чтобы вызвать эти API, `AppWindowPage` требуется ссылка на экземпляр AppWindow, в котором он размещен.

Если это все, что вам нужно, можно создать свойство в `AppWindowPage` и присвоить ему экземпляр [AppWindow](/uwp/api/windows.ui.windowmanagement.appwindow) при создании.

**AppWindowPage.xaml.cs**

В `AppWindowPage` создайте свойство для хранения ссылки на AppWindow.

```csharp
public sealed partial class AppWindowPage : Page
{
    public AppWindow MyAppWindow { get; set; }

    // ...
}
```

**MainPage.xaml.cs**

В `MainPage` получите ссылку на экземпляр страницы и присвойте вновь созданный экземпляр AppWindow свойству в `AppWindowPage`.

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

### <a name="tracking-app-windows-using-uicontext"></a>Отслеживание окон приложения с помощью UIContext

Может также потребоваться обращаться к экземплярам [AppWindow](/uwp/api/windows.ui.windowmanagement.appwindow) из других частей приложения. Например, на странице `MainPage` может быть кнопка "Закрыть все", которая закрывает все отслеженные экземпляры AppWindow.

В этом случае следует использовать уникальный идентификатор [UIContext](/uwp/api/windows.ui.uicontext), чтобы отслеживать экземпляры окна в [Dictionary](/dotnet/api/system.collections.generic.dictionary-2?view=dotnet-uwp-10.0).

**MainPage.xaml.cs**

В `MainPage` создайте Dictionary как статическое свойство. Затем добавьте страницу в Dictionary при ее создании и удалите ее при закрытии страницы. Вы можете получить UIContext из содержимого [кадра](/uwp/api/Windows.UI.Xaml.Controls.Frame) (`appWindowContentFrame.UIContext`) после вызова [ElementCompositionPreview.SetAppWindowContent](/api/windows.ui.xaml.hosting.elementcompositionpreview.setappwindowcontent).

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

Чтобы использовать экземпляр [AppWindow](/uwp/api/windows.ui.windowmanagement.appwindow) в коде `AppWindowPage`, используйте [UIContext](/uwp/api/windows.ui.uicontext) страницы, чтобы извлечь его из статического словаря в `MainPage`. Это необходимо сделать в обработчике событий [Loaded](/uwp/api/windows.ui.xaml.frameworkelement.loaded) страницы, а не в конструкторе, чтобы UIContext не имел значение NULL. UIContext можно получить из Page: `this.UIContext`.

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
> В примере `HelloAppWindow` показаны оба способа отслеживания окна в `AppWindowPage`, но обычно используется один из способов, а не оба.

## <a name="request-window-size-and-placement"></a>Запрос размера и расположения окна

Класс [AppWindow](/uwp/api/windows.ui.windowmanagement.appwindow) содержит несколько методов, которые можно использовать для управления размером и размещением окна. Как следует из имен методов, система может учитывать или не учитывать запрошенные изменения в зависимости от факторов среды.

Вызовите [RequestSize](/uwp/api/windows.ui.windowmanagement.appwindow.requestsize), чтобы указать желаемый размер окна, как показано ниже.

```csharp
colorPickerAppWindow.RequestSize(new Size(300, 428));
```

Методы управления размещением окон называются _RequestMove*_ : [RequestMoveAdjacentToCurrentView](/uwp/api/windows.ui.windowmanagement.appwindow.requestmoveadjacenttocurrentview), [RequestMoveAdjacentToWindow](/uwp/api/windows.ui.windowmanagement.appwindow.requestmoveadjacenttowindow), [RequestMoveRelativeToDisplayRegion](/uwp/api/windows.ui.windowmanagement.appwindow.requestmoverelativetodisplayregion), [RequestMoveToDisplayRegion](/uwp/api/windows.ui.windowmanagement.appwindow.requestmovetodisplayregion).

В этом примере код помещает окно рядом с главным представлением, из которого это окно было порождено.

```csharp
colorPickerAppWindow.RequestMoveAdjacentToCurrentView();
```

Чтобы получить сведения о текущих размере и расположении окна, вызовите [GetPlacement](/uwp/api/windows.ui.windowmanagement.appwindow.getplacement). Он возвращает объект [AppWindowPlacement](/uwp/api/windows.ui.windowmanagement.appwindowplacement), который предоставляет текущие значения [DisplayRegion](/uwp/api/windows.ui.windowmanagement.appwindowplacement.displayregion), [Offset](/uwp/api/windows.ui.windowmanagement.appwindowplacement.offset) и [Size](/uwp/api/windows.ui.windowmanagement.appwindowplacement.size) окна.

Например, можно вызвать этот код, чтобы переместить окно в правый верхний угол экрана. Этот код должен вызываться после отображения окна. В противном случае размер окна, возвращаемый вызовом метода GetPlacement, будет иметь значение 0,0, а смещение будет неправильным.

```csharp
DisplayRegion displayRegion = window.GetPlacement().DisplayRegion;
double displayRegionWidth = displayRegion.WorkAreaSize.Width;
double windowWidth = window.GetPlacement().Size.Width;
int horizontalOffset = (int)(displayRegionWidth - windowWidth);
window.RequestMoveRelativeToDisplayRegion(displayRegion, new Point(horizontalOffset, 0));
```

## <a name="request-a-presentation-configuration"></a>Запрос конфигурации представления

Класс [AppWindowPresenter](/uwp/api/windows.ui.windowmanagement.appwindowpresenter) позволяет показать окно [AppWindow](/uwp/api/windows.ui.windowmanagement.appwindow), используя предварительно определенную конфигурацию, подходящую для устройства, на котором оно отображается. Можно использовать значение [AppWindowPresentationConfiguration](/uwp/api/windows.ui.windowmanagement.appwindowpresentationconfiguration), чтобы перевести окно в режим `FullScreen` или `CompactOverlay`.

Этот пример показывает, как сделать следующее.

- Используйте событие [AppWindow.Changed](/uwp/api/windows.ui.windowmanagement.appwindow.changed), чтобы получать уведомления при изменении доступных представлений окна.
- Используйте свойство [AppWindow.Presenter](/uwp/api/windows.ui.windowmanagement.appwindow.presenter), чтобы получить текущее значение [AppWindowPresenter](/uwp/api/windows.ui.windowmanagement.appwindowpresenter).
- Вызовите [IsPresentationSupported](/uwp/api/windows.ui.windowmanagement.appwindowpresenter.ispresentationsupported), чтобы узнать, поддерживается ли определенный тип [AppWindowPresentationKind](/uwp/api/windows.ui.windowmanagement.appwindowpresentationkind).
- Вызовите [GetConfiguration](/uwp/api/windows.ui.windowmanagement.appwindowpresenter.getconfiguration), чтобы проверить, какой тип конфигурации используется в данный момент.
- Вызовите [RequestPresentation](/uwp/api/windows.ui.windowmanagement.appwindowpresenter.requestpresentation), чтобы изменить текущую конфигурацию.

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

## <a name="reuse-xaml-elements"></a>Многократно использование элементов XAML

Класс [AppWindow](/uwp/api/windows.ui.windowmanagement.appwindow) позволяет использовать несколько деревьев XAML в одном потоке пользовательского интерфейса. Однако элемент XAML может быть добавлен в дерево XAML только один раз. Если вы хотите переместить часть пользовательского интерфейса из одного окна в другое, необходимо управлять ее размещением в дереве XAML.

В этом примере показано, как повторно использовать элемент управления [ColorPicker](/uwp/api/windows.ui.xaml.controls.colorpicker) при его перемещении между главным и дополнительным окнами.

Палитра объявлена в коде XAML для `MainPage`, который помещает ее в дерево XAML `MainPage`.

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

Когда палитра отсоединяется для отображения в новом окне AppWindow, сначала необходимо удалить ее из дерева XAML `MainPage`, удалив ее из родительского контейнера. Хотя это не обязательно, этот пример также скрывает родительский контейнер.

```csharp
colorPickerContainer.Children.Remove(colorPicker);
colorPickerContainer.Visibility = Visibility.Collapsed;
```

Затем его можно добавить в новое дерево XAML. Здесь сначала следует создать объект [Grid](/uwp/api/windows.ui.xaml.controls.grid), который будет родительским контейнером для ColorPicker, и добавить ColorPicker в качестве дочернего элемента Grid. (Это позволит легко удалить ColorPicker из этого дерева XAML позже.) Затем следует задать Grid в качестве корневого элемента дерева XAML в новом окне.

```csharp
Grid appWindowRootGrid = new Grid();
appWindowRootGrid.Children.Add(colorPicker);

// Create a new window
colorPickerAppWindow = await AppWindow.TryCreateAsync();

// Attach the XAML content to our window
ElementCompositionPreview.SetAppWindowContent(colorPickerAppWindow, appWindowRootGrid);
```

Когда [AppWindow](/uwp/api/windows.ui.windowmanagement.appwindow) закрывается, процесс выполняется в обратном порядке. Сначала удалите объект [ColorPicker](/uwp/api/windows.ui.xaml.controls.colorpicker) из [Grid](/uwp/api/windows.ui.xaml.controls.grid), а затем добавьте его в качестве дочернего элемента [StackPanel](/uwp/api/windows.ui.xaml.controls.stackpanel) в `MainPage`.

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

По умолчанию диалоговые окна содержимого модально связаны с корневым объектом [ApplicationView](/uwp/api/windows.ui.viewmanagement.applicationview). При использовании [ContentDialog](/uwp/api/windows.ui.xaml.controls.contentdialog) в объекте [AppWindow](/uwp/api/windows.ui.windowmanagement.appwindow) необходимо вручную задать XamlRoot для диалогового окна, указав корень узла XAML.

Для этого задайте для свойства [XamlRoot](/uwp/api/windows.ui.xaml.uielement.xamlroot) элемента ContentDialog то же значение [XamlRoot](/uwp/api/windows.ui.xaml.xamlroot), что и у элемента, уже заданного в AppWindow. Здесь этот код находится в обработчике события [Click](/uwp/api/windows.ui.xaml.controls.primitives.buttonbase.click), поэтому для получения XamlRoot можно использовать _отправитель_ (нажатую кнопку).

```csharp
if (ApiInformation.IsApiContractPresent("Windows.Foundation.UniversalApiContract", 8))
{
    simpleDialog.XamlRoot = ((Button)sender).XamlRoot;
}
```

Если в дополнение к главному окну (ApplicationView) открыт один или несколько экземпляров AppWindow, то каждый из них может попытаться открыть диалоговое окно, так как модальное диалоговое окно блокирует только окно, которым оно было порождено. Однако в потоке может существовать только один элемент [ContentDialog](/uwp/api/windows.ui.xaml.controls.contentdialog) одновременно. Попытка открыть два элемента ContentDialogs породит исключение, даже если они пытаются открыться в отдельных объектах AppWindow.

Для управления этим необходимо по крайней мере открыть диалоговое окно в блоке `try/catch`, чтобы перехватить исключение в случае, если уже открыто другое диалоговое окно.

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

Другим способом управления диалоговыми окнами является отслеживание открытого диалогового окна и его закрытие перед попыткой открыть новое диалоговое окно. Здесь для этой цели в `MainPage` создается статическое свойство `CurrentDialog`.

```csharp
public sealed partial class MainPage : Page
{
    // Track the last opened dialog so you can close it if another dialog tries to open.
    public static ContentDialog CurrentDialog { get; set; } = null;

   // ...
}
```

Затем следует проверить, открыто ли сейчас диалоговое окно, и если открыто, — вызвать метод [Hide](/uwp/api/windows.ui.xaml.controls.contentdialog.hide), чтобы закрыть его. Наконец, назначьте новое диалоговое окно `CurrentDialog` и попытайтесь показать его.

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

Если нежелательно закрывать диалоговое окно программно, не назначайте его `CurrentDialog`. Здесь `MainPage` отображает важное диалоговое окно, которое следует закрыть, только если нажата кнопка `Ok`. Так как оно не назначено в качестве `CurrentDialog`, попытка закрыть его программным способом не выполняется.

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

### <a name="appwindowpagexaml"></a>AppWindowPage.xaml

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

## <a name="related-topics"></a>Связанные темы

- [Отображение нескольких представлений](show-multiple-views.md)
- [Отображение нескольких представлений с помощью ApplicationView](application-view.md)
