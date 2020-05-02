---
title: Использование визуального уровня с помощью WPF
description: Изучите методы, позволяющие использовать интерфейсы API визуального уровня в сочетании с существующим содержимым WPF, чтобы создавать сложные анимации и эффекты.
ms.date: 03/18/2019
ms.topic: article
keywords: windows 10, uwp
ms.author: jimwalk
author: jwmsft
ms.localizationpriority: medium
ms.openlocfilehash: a2f30ba67acc12d622acd09f9fae872ee2058a2f
ms.sourcegitcommit: 76e8b4fb3f76cc162aab80982a441bfc18507fb4
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/29/2020
ms.locfileid: "66215157"
---
# <a name="using-the-visual-layer-with-wpf"></a>Использование визуального уровня с помощью WPF

Вы можете использовать интерфейсы API Composition среды выполнения Windows (также называемые [визуальным уровнем](/windows/uwp/composition/visual-layer)) в приложениях Windows Presentation Foundation (WPF), чтобы создавать современные интерфейсы для пользователей Windows 10.

Полный код для этого руководства доступен на сайте GitHub: [Пример приложения WPF HelloComposition](https://github.com/Microsoft/Windows.UI.Composition-Win32-Samples/tree/master/dotnet/WPF/HelloComposition).

## <a name="prerequisites"></a>Предварительные условия

Для использования API размещения XAML для UWP накладываются приведенные ниже предварительные требования.

- Предполагается, что у вас есть опыт разработки приложений с помощью WPF и UWP. См. также:
  - [Приступая к работе (WPF)](/dotnet/framework/wpf/getting-started/)
  - [Приступая к работе с приложениями для Windows 10](/windows/uwp/get-started/)
  - [Улучшение классического приложения для Windows 10](/windows/uwp/porting/desktop-to-uwp-enhance)
- .NET Framework 4.7.2 или более поздней версии.
- Windows 10 версии 1803 или более поздней версии.
- Пакет SDK для Windows 10 версии 17134 или более поздней версии.

## <a name="how-to-use-composition-apis-in-wpf"></a>Использование интерфейсов API Composition в WPF

При работе с этим руководством вы создадите пользовательский интерфейс приложения WPF и добавите в него анимированные элементы Composition. Компоненты WPF и компоненты Composition простые, но приведенный код взаимодействия одинаков независимо от сложности компонентов. Завершенное приложение выглядит следующим образом.

![Пользовательский интерфейс выполняющегося приложения](images/visual-layer-interop/wpf-comp-interop-app-ui.png)

## <a name="create-a-wpf-project"></a>Создание проекта WPF

Первым шагом является создание проекта приложения WPF, включающего в себя определение приложения и страницу XAML для пользовательского интерфейса.

Чтобы создать проект приложения WPF на Visual C# с именем _HelloComposition_, сделайте следующее.

1. Откройте Visual Studio и выберите **Файл** > **Создать** > **Проект**.

    Появится диалоговое окно **Создать проект**.
1. В категории **Установлены** разверните узел **Visual C#** , а затем выберите **Windows Desktop**.
1. Выберите шаблон **Приложение WPF (.NET Framework)** .
1. Введите имя _HelloComposition_, выберите платформу **.NET Framework 4.7.2**, а затем нажмите кнопку **ОК**.

    Visual Studio создаст проект и откроет конструктор для окна приложения по умолчанию, MainWindow.xaml.

## <a name="configure-the-project-to-use-windows-runtime-apis"></a>Настройка проекта для использования интерфейсов API среды выполнения Windows

Чтобы использовать API среды выполнения Windows (WinRT) в приложении WPF, необходимо настроить проект Visual Studio для доступа к среде выполнения Windows. Кроме того, API Composition активно используют векторы, поэтому нужно добавить ссылки, необходимые для использования векторов.

Для решения этих задач предоставляются соответствующие пакеты NuGet. Установите последние версии этих пакетов, чтобы добавить необходимые ссылки в проект.  

- [Microsoft.Windows.SDK.Contracts](https://www.nuget.org/packages/Microsoft.Windows.SDK.Contracts) (требуется задать PackageReference в качестве формата управления пакетами по умолчанию).
- [System.Numerics.Vectors](https://www.nuget.org/packages/System.Numerics.Vectors/).

> [!NOTE]
> Хотя мы рекомендуем использовать для настройки проекта пакеты NuGet, необходимые ссылки можно добавить вручную. Дополнительные сведения: [Улучшение классического приложения для Windows 10](/windows/uwp/porting/desktop-to-uwp-enhance). В следующей таблице приведены файлы, на которые необходимо добавить ссылки.

|Файл|Расположение|
|--|--|
|System.Runtime.WindowsRuntime|C:\Windows\Microsoft.NET\Framework\v4.0.30319|
|Windows.Foundation.UniversalApiContract.winmd|C:\Program Files (x86)\Windows Kits\10\References\<*версия пакета SDK*>\Windows.Foundation.UniversalApiContract\<*версия*>|
|Windows.Foundation.FoundationContract.winmd|C:\Program Files (x86)\Windows Kits\10\References\<*версия пакета SDK*>\Windows.Foundation.FoundationContract\<*версия*>|
|System.Numerics.Vectors.dll|C:\WINDOWS\Microsoft.Net\assembly\GAC_MSIL\System.Numerics.Vectors\v4.0_4.0.0.0__b03f5f7f11d50a3a|
|System.Numerics.dll|C:\Program Files (x86)\Reference Assemblies\Microsoft\Framework\.NETFramework\v4.7.2|

## <a name="configure-the-project-to-be-per-monitor-dpi-aware"></a>Настройка проекта для поддержки определения DPI для каждого монитора

Содержимое визуального уровня, добавляемое в приложение, не масштабируется автоматически в соответствии с параметрами DPI экрана, на котором оно отображается. Для приложения необходимо включить определение DPI для каждого монитора, а затем убедиться, что код, используемый для создания содержимого визуального уровня, учитывает текущее количество точек на дюйм при запуске приложения. Сейчас мы настроим проект для поддержки определения DPI для каждого монитора. В последующих разделах мы покажем, как использовать сведения о DPI для масштабирования содержимого визуального уровня.

Приложения WPF по умолчанию поддерживают системное определение DPI, но для них необходимо объявить в файле app.manifest поддержку определения DPI для каждого монитора. Чтобы включить в файле манифеста приложения определение DPI для каждого монитора на уровне Windows, сделайте следующее.

1. В **обозревателе решений** щелкните проект _HelloComposition_ правой кнопкой мыши.
1. В контекстном меню выберите **Добавить** > **Новый элемент**.
1. В диалоговом окне **Добавление нового элемента** выберите "Файл манифеста приложения", а затем нажмите кнопку **Добавить**. (Можно оставить имя по умолчанию.)
1. В файле app.manifest найдите приведенный ниже код XML и раскомментируйте его.

    ```xaml
    <application xmlns="urn:schemas-microsoft-com:asm.v3">
        <windowsSettings>
          <dpiAware xmlns="http://schemas.microsoft.com/SMI/2005/WindowsSettings">true</dpiAware>
        </windowsSettings>
      </application>
    ```

1. Добавьте этот параметр после открывающего тега `<windowsSettings>`.

    ```xaml
          <dpiAwareness xmlns="http://schemas.microsoft.com/SMI/2016/WindowsSettings">PerMonitor</dpiAwareness>
    ```

1. Также в файле app.config необходимо задать параметр **DoNotScaleForDpiChanges**.

    Откройте файл app.config и добавьте приведенный ниже код XML в элемент `<configuration>`.

    ```xml
    <runtime>
      <AppContextSwitchOverrides value="Switch.System.Windows.DoNotScaleForDpiChanges=false"/>
    </runtime>
    ```

> [!NOTE]
> Параметр **AppContextSwitchOverrides** можно задать только один раз. Если он уже задан в вашем приложении, необходимо отделить этот параметр точкой с запятой в атрибуте value.

(Дополнительные сведения приведены в [руководстве для разработчиков и примерах определения DPI для каждого монитора](https://github.com/Microsoft/WPF-Samples/tree/master/PerMonitorDPI) на сайте GitHub.)

## <a name="create-an-hwndhost-derived-class-to-host-composition-elements"></a>Создание производного класса HwndHost для размещения элементов композиции

Для размещения содержимого, созданного с помощью визуального уровня, необходимо создать класс, производный от [HwndHost](/dotnet/api/system.windows.interop.hwndhost). Здесь потребуется выполнить большую часть настройки для размещения интерфейсов API Composition. В этом классе для добавления интерфейсов API Composition в приложение WPF используются [службы вызова платформы (PInvoke)](/cpp/dotnet/calling-native-functions-from-managed-code) и [взаимодействие COM](/dotnet/api/system.runtime.interopservices.comimportattribute). Дополнительные сведения о PInvoke и взаимодействии COM см. в разделе [Взаимодействие с неуправляемым кодом](/dotnet/framework/interop/index).

> [!TIP]
> При необходимости сверьтесь с полным кодом в конце руководства, чтобы убедиться в том, что весь код добавлен в соответствующе места при работе с этим руководством.

1. Добавьте в проект файл нового класса, производного от [HwndHost](/dotnet/api/system.windows.interop.hwndhost).
    - В **обозревателе решений** щелкните проект _HelloComposition_ правой кнопкой мыши.
    - В контекстном меню выберите **Добавить** > **Класс**.
    - В диалоговом окне **Добавление нового элемента** укажите имя класса, _CompositionHost.cs_, а затем щелкните **Добавить**.
1. В CompositionHost.cs измените определение класса, чтобы сделать его производным от **HwndHost**.

    ```csharp
    // Add
    // using System.Windows.Interop;

    namespace HelloComposition
    {
        class CompositionHost : HwndHost
        {
        }
    }
    ```

1. Добавьте в класс приведенный ниже код и конструктор.

    ```csharp
    // Add
    // using Windows.UI.Composition;

    IntPtr hwndHost;
    int hostHeight, hostWidth;
    object dispatcherQueue;
    ICompositionTarget compositionTarget;

    public Compositor Compositor { get; private set; }

    public Visual Child
    {
        set
        {
            if (Compositor == null)
            {
                InitComposition(hwndHost);
            }
            compositionTarget.Root = value;
        }
    }

    internal const int
      WS_CHILD = 0x40000000,
      WS_VISIBLE = 0x10000000,
      LBS_NOTIFY = 0x00000001,
      HOST_ID = 0x00000002,
      LISTBOX_ID = 0x00000001,
      WS_VSCROLL = 0x00200000,
      WS_BORDER = 0x00800000;

    public CompositionHost(double height, double width)
    {
        hostHeight = (int)height;
        hostWidth = (int)width;
    }
    ```

1. Переопределите методы **BuildWindowCore** и **DestroyWindowCore**.
    > [!NOTE]
    > В BuildWindowCore вызываются методы _InitializeCoreDispatcher_ и _InitComposition_. Эти методы создаются на следующих шагах.

    ```csharp
    // Add
    // using System.Runtime.InteropServices;

    protected override HandleRef BuildWindowCore(HandleRef hwndParent)
    {
        // Create Window
        hwndHost = IntPtr.Zero;
        hwndHost = CreateWindowEx(0, "static", "",
                                  WS_CHILD | WS_VISIBLE,
                                  0, 0,
                                  hostWidth, hostHeight,
                                  hwndParent.Handle,
                                  (IntPtr)HOST_ID,
                                  IntPtr.Zero,
                                  0);

        // Create Dispatcher Queue
        dispatcherQueue = InitializeCoreDispatcher();

        // Build Composition tree of content
        InitComposition(hwndHost);

        return new HandleRef(this, hwndHost);
    }

    protected override void DestroyWindowCore(HandleRef hwnd)
    {
        if (compositionTarget.Root != null)
        {
            compositionTarget.Root.Dispose();
        }
        DestroyWindow(hwnd.Handle);
    }
    ```

    - Для [CreateWindowEx](/windows/desktop/api/winuser/nf-winuser-createwindowexa) и [DestroyWindow](/windows/desktop/api/winuser/nf-winuser-destroywindow) требуется объявление PInvoke. Поместите это объявление в конец блока кода класса.

    ```csharp
    #region PInvoke declarations

    [DllImport("user32.dll", EntryPoint = "CreateWindowEx", CharSet = CharSet.Unicode)]
    internal static extern IntPtr CreateWindowEx(int dwExStyle,
                                                  string lpszClassName,
                                                  string lpszWindowName,
                                                  int style,
                                                  int x, int y,
                                                  int width, int height,
                                                  IntPtr hwndParent,
                                                  IntPtr hMenu,
                                                  IntPtr hInst,
                                                  [MarshalAs(UnmanagedType.AsAny)] object pvParam);

    [DllImport("user32.dll", EntryPoint = "DestroyWindow", CharSet = CharSet.Unicode)]
    internal static extern bool DestroyWindow(IntPtr hwnd);

    #endregion PInvoke declarations
    ```

1. Инициализируйте поток с помощью [CoreDispatcher](/uwp/api/windows.ui.core.coredispatcher). Основной диспетчер отвечает за обработку сообщений окон и диспетчеризацию событий интерфейсов API WinRT. Новые экземпляры **CoreDispatcher** должны создаваться в потоке с CoreDispatcher.
    - Создайте метод _InitializeCoreDispatcher_ и добавьте код для настройки очереди диспетчера.

    ```csharp
    private object InitializeCoreDispatcher()
    {
        DispatcherQueueOptions options = new DispatcherQueueOptions();
        options.apartmentType = DISPATCHERQUEUE_THREAD_APARTMENTTYPE.DQTAT_COM_STA;
        options.threadType = DISPATCHERQUEUE_THREAD_TYPE.DQTYPE_THREAD_CURRENT;
        options.dwSize = Marshal.SizeOf(typeof(DispatcherQueueOptions));

        object queue = null;
        CreateDispatcherQueueController(options, out queue);
        return queue;
    }
    ```

    - Для очереди диспетчера также требуется объявление PInvoke. Поместите это объявление в область _объявлений PInvoke_, созданную на предыдущем шаге.

    ```csharp
    //typedef enum DISPATCHERQUEUE_THREAD_APARTMENTTYPE
    //{
    //    DQTAT_COM_NONE,
    //    DQTAT_COM_ASTA,
    //    DQTAT_COM_STA
    //};
    internal enum DISPATCHERQUEUE_THREAD_APARTMENTTYPE
    {
        DQTAT_COM_NONE = 0,
        DQTAT_COM_ASTA = 1,
        DQTAT_COM_STA = 2
    };

    //typedef enum DISPATCHERQUEUE_THREAD_TYPE
    //{
    //    DQTYPE_THREAD_DEDICATED,
    //    DQTYPE_THREAD_CURRENT
    //};
    internal enum DISPATCHERQUEUE_THREAD_TYPE
    {
        DQTYPE_THREAD_DEDICATED = 1,
        DQTYPE_THREAD_CURRENT = 2,
    };

    //struct DispatcherQueueOptions
    //{
    //    DWORD dwSize;
    //    DISPATCHERQUEUE_THREAD_TYPE threadType;
    //    DISPATCHERQUEUE_THREAD_APARTMENTTYPE apartmentType;
    //};
    [StructLayout(LayoutKind.Sequential)]
    internal struct DispatcherQueueOptions
    {
        public int dwSize;

        [MarshalAs(UnmanagedType.I4)]
        public DISPATCHERQUEUE_THREAD_TYPE threadType;

        [MarshalAs(UnmanagedType.I4)]
        public DISPATCHERQUEUE_THREAD_APARTMENTTYPE apartmentType;
    };

    //HRESULT CreateDispatcherQueueController(
    //  DispatcherQueueOptions options,
    //  ABI::Windows::System::IDispatcherQueueController** dispatcherQueueController
    //);
    [DllImport("coremessaging.dll", EntryPoint = "CreateDispatcherQueueController", CharSet = CharSet.Unicode)]
    internal static extern IntPtr CreateDispatcherQueueController(DispatcherQueueOptions options,
                                            [MarshalAs(UnmanagedType.IUnknown)]
                                            out object dispatcherQueueController);
    ```

    Теперь очередь диспетчера готова, и можно начать инициализацию и создание содержимого Composition.

1. Инициализируйте [Compositor](/uwp/api/windows.ui.composition.compositor). Compositor — это фабрика, которая создает разные типы в пространстве имен [Windows.UI.Composition](/uwp/api/windows.ui.composition), охватывающем визуальные элементы, систему эффектов и систему анимации. Класс Compositor также управляет временем существования объектов, созданных из фабрики.

    ```csharp
    private void InitComposition(IntPtr hwndHost)
    {
        ICompositorDesktopInterop interop;

        compositor = new Compositor();
        object iunknown = compositor as object;
        interop = (ICompositorDesktopInterop)iunknown;
        IntPtr raw;
        interop.CreateDesktopWindowTarget(hwndHost, true, out raw);

        object rawObject = Marshal.GetObjectForIUnknown(raw);
        ICompositionTarget target = (ICompositionTarget)rawObject;

        if (raw == null) { throw new Exception("QI Failed"); }
    }
    ```

    - Для **ICompositorDesktopInterop** и **ICompositionTarget** требуется импорт COM. Поместите этот код после класса _CompositionHost_ внутри объявления пространства имен.

    ```csharp
    #region COM Interop

    /*
    #undef INTERFACE
    #define INTERFACE ICompositorDesktopInterop
        DECLARE_INTERFACE_IID_(ICompositorDesktopInterop, IUnknown, "29E691FA-4567-4DCA-B319-D0F207EB6807")
        {
            IFACEMETHOD(CreateDesktopWindowTarget)(
                _In_ HWND hwndTarget,
                _In_ BOOL isTopmost,
                _COM_Outptr_ IDesktopWindowTarget * *result
                ) PURE;
        };
    */
    [ComImport]
    [Guid("29E691FA-4567-4DCA-B319-D0F207EB6807")]
    [InterfaceType(ComInterfaceType.InterfaceIsIUnknown)]
    public interface ICompositorDesktopInterop
    {
        void CreateDesktopWindowTarget(IntPtr hwndTarget, bool isTopmost, out IntPtr test);
    }

    //[contract(Windows.Foundation.UniversalApiContract, 2.0)]
    //[exclusiveto(Windows.UI.Composition.CompositionTarget)]
    //[uuid(A1BEA8BA - D726 - 4663 - 8129 - 6B5E7927FFA6)]
    //interface ICompositionTarget : IInspectable
    //{
    //    [propget] HRESULT Root([out] [retval] Windows.UI.Composition.Visual** value);
    //    [propput] HRESULT Root([in] Windows.UI.Composition.Visual* value);
    //}

    [ComImport]
    [Guid("A1BEA8BA-D726-4663-8129-6B5E7927FFA6")]
    [InterfaceType(ComInterfaceType.InterfaceIsIInspectable)]
    public interface ICompositionTarget
    {
        Windows.UI.Composition.Visual Root
        {
            get;
            set;
        }
    }

    #endregion COM Interop
    ```

## <a name="create-a-usercontrol-to-add-your-content-to-the-wpf-visual-tree"></a>Создание UserControl для добавления содержимого в визуальное дерево WPF

Последним шагом в настройке инфраструктуры, необходимой для размещения содержимого Composition, является добавление HwndHost в визуальное дерево WPF.

### <a name="create-a-usercontrol"></a>Создание UserControl

С помощью UserControl удобно упаковывать код, который создает содержимое Composition и управляет им, а также легко добавлять содержимое в код XAML.

1. Добавьте файл нового пользовательского элемента управления в проект.
    - В **обозревателе решений** щелкните проект _HelloComposition_ правой кнопкой мыши.
    - В контекстном меню выберите **Добавить** > **Пользовательский элемент управления**.
    - В диалоговом окне **Добавление нового элемента** укажите имя пользовательского элемента управления, _CompositionHostControl.xaml_, а затем щелкните **Добавить**.

    Будут созданы файлы CompositionHostControl.xaml и CompositionHostControl.xaml.cs, которые добавляются в проект.
1. В файле CompositionHostControl.xaml замените теги `<Grid> </Grid>` приведенным элементом **Border**. Это контейнер XAML, предназначенный для класса HwndHost.

    ```xaml
    <Border Name="CompositionHostElement"/>
    ```

В коде пользовательского элемента управления следует создать экземпляр класса CompositionHost, созданного на предыдущем шаге, и добавить его в качестве дочернего элемента _CompositionHostElement_ — элемента Border, созданного в странице XAML.

1. В CompositionHostControl.xaml.cs добавьте частные переменные для объектов, которые будут использоваться в коде Composition. Добавьте их после определения класса.

    ```csharp
    CompositionHost compositionHost;
    Compositor compositor;
    Windows.UI.Composition.ContainerVisual containerVisual;
    DpiScale currentDpi;
    ```

1. Добавьте обработчик для события **Loaded** пользовательского элемента управления. Здесь следует настроить экземпляр CompositionHost.

    - В конструкторе подсоедините обработчик событий, как показано здесь (`Loaded += CompositionHostControl_Loaded;`).

    ```csharp
    public CompositionHostControl()
    {
        InitializeComponent();
        Loaded += CompositionHostControl_Loaded;
    }
    ```

    - Добавьте метод обработчика событий *CompositionHostControl_Loaded*.
    ```csharp
    private void CompositionHostControl_Loaded(object sender, RoutedEventArgs e)
    {
        // If the user changes the DPI scale setting for the screen the app is on,
        // the CompositionHostControl is reloaded. Don't redo this set up if it's
        // already been done.
        if (compositionHost is null)
        {
            currentDpi = VisualTreeHelper.GetDpi(this);

            compositionHost =
                new CompositionHost(ControlHostElement.ActualHeight, ControlHostElement.ActualWidth);
            ControlHostElement.Child = compositionHost;
            compositor = compositionHost.Compositor;
            containerVisual = compositor.CreateContainerVisual();
            compositionHost.Child = containerVisual;
        }
    }
    ```

    В этом методе следует настроить объекты, которые будут использоваться в коде Composition. Ниже коротко описано, что происходит.

    - Во-первых, убедитесь, что настройка выполняется только один раз, проверив, существует ли экземпляр CompositionHost.

    ```csharp
    // If the user changes the DPI scale setting for the screen the app is on,
    // the CompositionHostControl is reloaded. Don't redo this set up if it's
    // already been done.
    if (compositionHost is null)
    {

    }
    ```

    - Получите текущее значение DPI. Оно используется для правильного масштабирования элементов Composition.

    ```csharp
    currentDpi = VisualTreeHelper.GetDpi(this);
    ```

    - Создайте экземпляр CompositionHost и назначьте его дочерним элементом Border, _CompositionHostElement_.

    ```csharp
    compositionHost =
        new CompositionHost(ControlHostElement.ActualHeight, ControlHostElement.ActualWidth);
    ControlHostElement.Child = compositionHost;
    ```

    - Получите Compositor из CompositionHost.

    ```csharp
    compositor = compositionHost.Compositor;
    ```

    - Используйте Compositor для создания визуального элемента контейнера. Это контейнер Composition, в который добавляются элементы Composition.

    ```csharp
    containerVisual = compositor.CreateContainerVisual();
    compositionHost.Child = containerVisual;
    ```

### <a name="add-composition-elements"></a>Добавление элементов композиции

Теперь, когда инфраструктура настроена, можно создать содержимое Composition, которое необходимо отобразить.

В этом примере вы добавите код, который создает и анимирует простой квадратный элемент [SpriteVisual](/uwp/api/windows.ui.composition.spritevisual).

1. Добавьте элемент композиции. В CompositionHostControl.xaml.cs добавьте приведенные ниже методы в класс CompositionHostControl.

    ```csharp
    // Add
    // using System.Numerics;

    public void AddElement(float size, float offsetX, float offsetY)
    {
        var visual = compositor.CreateSpriteVisual();
        visual.Size = new Vector2(size, size);
        visual.Scale = new Vector3((float)currentDpi.DpiScaleX, (float)currentDpi.DpiScaleY, 1);
        visual.Brush = compositor.CreateColorBrush(GetRandomColor());
        visual.Offset = new Vector3(offsetX * (float)currentDpi.DpiScaleX, offsetY * (float)currentDpi.DpiScaleY, 0);

        containerVisual.Children.InsertAtTop(visual);

        AnimateSquare(visual, 3);
    }

    private void AnimateSquare(SpriteVisual visual, int delay)
    {
        float offsetX = (float)(visual.Offset.X); // Already adjusted for DPI.

        // Adjust values for DPI scale, then find the Y offset that aligns the bottom of the square
        // with the bottom of the host container. This is the value to animate to.
        var hostHeightAdj = CompositionHostElement.ActualHeight * currentDpi.DpiScaleY;
        var squareSizeAdj = visual.Size.Y * currentDpi.DpiScaleY;
        float bottom = (float)(hostHeightAdj - squareSizeAdj);

        // Create the animation only if it's needed.
        if (visual.Offset.Y != bottom)
        {
            Vector3KeyFrameAnimation animation = compositor.CreateVector3KeyFrameAnimation();
            animation.InsertKeyFrame(1f, new Vector3(offsetX, bottom, 0f));
            animation.Duration = TimeSpan.FromSeconds(2);
            animation.DelayTime = TimeSpan.FromSeconds(delay);
            visual.StartAnimation("Offset", animation);
        }
    }

    private Windows.UI.Color GetRandomColor()
    {
        Random random = new Random();
        byte r = (byte)random.Next(0, 255);
        byte g = (byte)random.Next(0, 255);
        byte b = (byte)random.Next(0, 255);
        return Windows.UI.Color.FromArgb(255, r, g, b);
    }
    ```

### <a name="handle-dpi-changes"></a>Обработка изменений количества точек на дюйм

В коде для добавления и анимации элемента учитывается текущее значение DPI при создании элементов, но также необходимо учитывать изменения значения DPI во время работы приложения. Вы можете обрабатывать событие [HwndHost.DpiChanged](/dotnet/api/system.windows.interop.hwndhost.dpichanged), чтобы получать уведомления об изменениях и корректировать вычисления с учетом нового значения DPI.

1. Добавьте приведенный ниже код в метод CompositionHostControl_Loaded (ниже последней строки), чтобы подключить обработчик событий DpiChanged.

    ```csharp
    compositionHost.DpiChanged += CompositionHost_DpiChanged;
    ```

1. Добавьте метод обработчика событий _CompositionHostDpiChanged_. Этот код корректирует масштаб и смещение каждого элемента, а также повторно вычисляет все незавершенные анимации.

    ```csharp
    private void CompositionHost_DpiChanged(object sender, DpiChangedEventArgs e)
    {
        currentDpi = e.NewDpi;
        Vector3 newScale = new Vector3((float)e.NewDpi.DpiScaleX, (float)e.NewDpi.DpiScaleY, 1);

        foreach (SpriteVisual child in containerVisual.Children)
        {
            child.Scale = newScale;
            var newOffsetX = child.Offset.X * ((float)e.NewDpi.DpiScaleX / (float)e.OldDpi.DpiScaleX);
            var newOffsetY = child.Offset.Y * ((float)e.NewDpi.DpiScaleY / (float)e.OldDpi.DpiScaleY);
            child.Offset = new Vector3(newOffsetX, newOffsetY, 1);

            // Adjust animations for DPI change.
            AnimateSquare(child, 0);
        }
    }
    ```

## <a name="add-the-user-control-to-your-xaml-page"></a>Добавление пользовательского элемента управления на страницу XAML

Теперь можно добавить пользовательский элемент управления в пользовательский интерфейс XAML.

1. В файле MainWindow.xaml задайте для окна высоту 600 и ширину 840.
1. Добавьте XAML для пользовательского интерфейса. В файле MainWindow.xaml добавьте этот код XAML между корневыми тегами `<Grid> </Grid>`.

    ```xaml
    <Grid.ColumnDefinitions>
        <ColumnDefinition Width="210"/>
        <ColumnDefinition Width="600"/>
        <ColumnDefinition/>
    </Grid.ColumnDefinitions>
    <Grid.RowDefinitions>
        <RowDefinition Height="46"/>
        <RowDefinition/>
    </Grid.RowDefinitions>
    <Button Content="Add composition element" Click="Button_Click"
            Grid.Row="1" Margin="12,0"
            VerticalAlignment="Top" Height="40"/>
    <TextBlock Text="Composition content" FontSize="20"
               Grid.Column="1" Margin="0,12,0,4"
               HorizontalAlignment="Center"/>
    <local:CompositionHostControl x:Name="CompositionHostControl1"
                                  Grid.Row="1" Grid.Column="1"
                                  VerticalAlignment="Top"
                                  Width="600" Height="500"
                                  BorderBrush="LightGray"
                                  BorderThickness="3"/>
    ```

1. Добавьте обработку нажатия кнопки для создания новых элементов. (Событие Click уже подсоединено в XAML.)

    В MainWindow.xaml.cs добавьте метод обработчика событий *Button_Click*. Этот код вызывает _CompositionHost.AddElement_ для создания нового элемента со случайно заданным размером и смещением.

    ```csharp
    // Add
    // using System;

    private void Button_Click(object sender, RoutedEventArgs e)
    {
        Random random = new Random();
        float size = random.Next(50, 150);
        float offsetX = random.Next(0, (int)(CompositionHostControl1.ActualWidth - size));
        float offsetY = random.Next(0, (int)(CompositionHostControl1.ActualHeight/2 - size));
        CompositionHostControl1.AddElement(size, offsetX, offsetY);
    }
    ```

Теперь выполните сборку приложения WPF и запустите его. При необходимости сверьтесь с полным кодом в конце руководства, чтобы убедиться в том, что весь код добавлен в соответствующе места.

Если запустить приложение и нажать кнопку, в пользовательском интерфейсе должны отобразиться анимированные квадраты.

## <a name="next-steps"></a>Дальнейшие действия

Более полный [пример интеграции визуального уровня в WPF](https://github.com/Microsoft/Windows.UI.Composition-Win32-Samples/tree/master/dotnet/WPF/VisualLayerIntegration), основанный на той же инфраструктуре, доступен на сайте GitHub.

## <a name="additional-resources"></a>Дополнительные ресурсы

- [Приступая к работе (WPF)](/dotnet/framework/wpf/getting-started/) (.NET)
- [Взаимодействие с неуправляемым кодом](/dotnet/framework/interop/) (.NET)
- [Приступая к работе с приложениями для Windows 10](/windows/uwp/get-started/) (UWP)
- [Улучшение классического приложения для Windows 10](/windows/uwp/porting/desktop-to-uwp-enhance) (UWP)
- [Пространство имен Windows.UI.Composition](/uwp/api/windows.ui.composition) (UWP)

## <a name="complete-code"></a>Полный код

Ниже приведен полный код для этого руководства.

### <a name="mainwindowxaml"></a>MainWindow.xaml

```xaml
<Window x:Class="HelloComposition.MainWindow"
        xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
        xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
        xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
        xmlns:local="clr-namespace:HelloComposition"
        mc:Ignorable="d"
        Title="MainWindow" Height="600" Width="840">
    <Grid>
        <Grid.ColumnDefinitions>
            <ColumnDefinition Width="210"/>
            <ColumnDefinition Width="600"/>
            <ColumnDefinition/>
        </Grid.ColumnDefinitions>
        <Grid.RowDefinitions>
            <RowDefinition Height="46"/>
            <RowDefinition/>
        </Grid.RowDefinitions>
        <Button Content="Add composition element" Click="Button_Click"
                Grid.Row="1" Margin="12,0"
                VerticalAlignment="Top" Height="40"/>
        <TextBlock Text="Composition content" FontSize="20"
                   Grid.Column="1" Margin="0,12,0,4"
                   HorizontalAlignment="Center"/>
        <local:CompositionHostControl x:Name="CompositionHostControl1"
                                      Grid.Row="1" Grid.Column="1"
                                      VerticalAlignment="Top"
                                      Width="600" Height="500"
                                      BorderBrush="LightGray" BorderThickness="3"/>
    </Grid>
</Window>
```

### <a name="mainwindowxamlcs"></a>MainWindow.xaml.cs

```csharp
using System;
using System.Windows;

namespace HelloComposition
{
    /// <summary>
    /// Interaction logic for MainWindow.xaml
    /// </summary>
    public partial class MainWindow : Window
    {
        public MainWindow()
        {
            InitializeComponent();
        }

        private void Button_Click(object sender, RoutedEventArgs e)
        {
            Random random = new Random();
            float size = random.Next(50, 150);
            float offsetX = random.Next(0, (int)(CompositionHostControl1.ActualWidth - size));
            float offsetY = random.Next(0, (int)(CompositionHostControl1.ActualHeight/2 - size));
            CompositionHostControl1.AddElement(size, offsetX, offsetY);
        }
    }
}
```

### <a name="compositionhostcontrolxaml"></a>CompositionHostControl.xaml

```xaml
<UserControl x:Class="HelloComposition.CompositionHostControl"
             xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
             xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
             xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
             xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
             xmlns:local="clr-namespace:HelloComposition"
             mc:Ignorable="d"
             d:DesignHeight="450" d:DesignWidth="800">
    <Border Name="CompositionHostElement"/>
</UserControl>
```

### <a name="compositionhostcontrolxamlcs"></a>CompositionHostControl.xaml.cs

```csharp
using System;
using System.Numerics;
using System.Windows;
using System.Windows.Controls;
using System.Windows.Media;
using Windows.UI.Composition;

namespace HelloComposition
{
    /// <summary>
    /// Interaction logic for CompositionHostControl.xaml
    /// </summary>
    public partial class CompositionHostControl : UserControl
    {
        CompositionHost compositionHost;
        Compositor compositor;
        Windows.UI.Composition.ContainerVisual containerVisual;
        DpiScale currentDpi;

        public CompositionHostControl()
        {
            InitializeComponent();
            Loaded += CompositionHostControl_Loaded;
        }

        private void CompositionHostControl_Loaded(object sender, RoutedEventArgs e)
        {
            // If the user changes the DPI scale setting for the screen the app is on,
            // the CompositionHostControl is reloaded. Don't redo this set up if it's
            // already been done.
            if (compositionHost is null)
            {
                currentDpi = VisualTreeHelper.GetDpi(this);

                compositionHost = new CompositionHost(CompositionHostElement.ActualHeight, CompositionHostElement.ActualWidth);
                CompositionHostElement.Child = compositionHost;
                compositor = compositionHost.Compositor;
                containerVisual = compositor.CreateContainerVisual();
                compositionHost.Child = containerVisual;
            }
        }

        protected override void OnDpiChanged(DpiScale oldDpi, DpiScale newDpi)
        {
            base.OnDpiChanged(oldDpi, newDpi);
            currentDpi = newDpi;
            Vector3 newScale = new Vector3((float)newDpi.DpiScaleX, (float)newDpi.DpiScaleY, 1);

            foreach (SpriteVisual child in containerVisual.Children)
            {
                child.Scale = newScale;
                var newOffsetX = child.Offset.X * ((float)newDpi.DpiScaleX / (float)oldDpi.DpiScaleX);
                var newOffsetY = child.Offset.Y * ((float)newDpi.DpiScaleY / (float)oldDpi.DpiScaleY);
                child.Offset = new Vector3(newOffsetX, newOffsetY, 1);

                // Adjust animations for DPI change.
                AnimateSquare(child, 0);
            }
        }

        public void AddElement(float size, float offsetX, float offsetY)
        {
            var visual = compositor.CreateSpriteVisual();
            visual.Size = new Vector2(size, size);
            visual.Scale = new Vector3((float)currentDpi.DpiScaleX, (float)currentDpi.DpiScaleY, 1);
            visual.Brush = compositor.CreateColorBrush(GetRandomColor());
            visual.Offset = new Vector3(offsetX * (float)currentDpi.DpiScaleX, offsetY * (float)currentDpi.DpiScaleY, 0);

            containerVisual.Children.InsertAtTop(visual);

            AnimateSquare(visual, 3);
        }

        private void AnimateSquare(SpriteVisual visual, int delay)
        {
            float offsetX = (float)(visual.Offset.X); // Already adjusted for DPI.

            // Adjust values for DPI scale, then find the Y offset that aligns the bottom of the square
            // with the bottom of the host container. This is the value to animate to.
            var hostHeightAdj = CompositionHostElement.ActualHeight * currentDpi.DpiScaleY;
            var squareSizeAdj = visual.Size.Y * currentDpi.DpiScaleY;
            float bottom = (float)(hostHeightAdj - squareSizeAdj);

            // Create the animation only if it's needed.
            if (visual.Offset.Y != bottom)
            {
                Vector3KeyFrameAnimation animation = compositor.CreateVector3KeyFrameAnimation();
                animation.InsertKeyFrame(1f, new Vector3(offsetX, bottom, 0f));
                animation.Duration = TimeSpan.FromSeconds(2);
                animation.DelayTime = TimeSpan.FromSeconds(delay);
                visual.StartAnimation("Offset", animation);
            }
        }

        private Windows.UI.Color GetRandomColor()
        {
            Random random = new Random();
            byte r = (byte)random.Next(0, 255);
            byte g = (byte)random.Next(0, 255);
            byte b = (byte)random.Next(0, 255);
            return Windows.UI.Color.FromArgb(255, r, g, b);
        }
    }
}

```

### <a name="compositionhostcs"></a>CompositionHost.cs

```csharp
using System;
using System.Runtime.InteropServices;
using System.Windows.Interop;
using Windows.UI.Composition;

namespace HelloComposition
{
    class CompositionHost : HwndHost
    {
        IntPtr hwndHost;
        int hostHeight, hostWidth;
        object dispatcherQueue;
        ICompositionTarget compositionTarget;

        public Compositor Compositor { get; private set; }

        public Visual Child
        {
            set
            {
                if (Compositor == null)
                {
                    InitComposition(hwndHost);
                }
                compositionTarget.Root = value;
            }
        }

        internal const int
          WS_CHILD = 0x40000000,
          WS_VISIBLE = 0x10000000,
          LBS_NOTIFY = 0x00000001,
          HOST_ID = 0x00000002,
          LISTBOX_ID = 0x00000001,
          WS_VSCROLL = 0x00200000,
          WS_BORDER = 0x00800000;

        public CompositionHost(double height, double width)
        {
            hostHeight = (int)height;
            hostWidth = (int)width;
        }

        protected override HandleRef BuildWindowCore(HandleRef hwndParent)
        {
            // Create Window
            hwndHost = IntPtr.Zero;
            hwndHost = CreateWindowEx(0, "static", "",
                                      WS_CHILD | WS_VISIBLE,
                                      0, 0,
                                      hostWidth, hostHeight,
                                      hwndParent.Handle,
                                      (IntPtr)HOST_ID,
                                      IntPtr.Zero,
                                      0);

            // Create Dispatcher Queue
            dispatcherQueue = InitializeCoreDispatcher();

            // Build Composition Tree of content
            InitComposition(hwndHost);

            return new HandleRef(this, hwndHost);
        }

        protected override void DestroyWindowCore(HandleRef hwnd)
        {
            if (compositionTarget.Root != null)
            {
                compositionTarget.Root.Dispose();
            }
            DestroyWindow(hwnd.Handle);
        }

        private object InitializeCoreDispatcher()
        {
            DispatcherQueueOptions options = new DispatcherQueueOptions();
            options.apartmentType = DISPATCHERQUEUE_THREAD_APARTMENTTYPE.DQTAT_COM_STA;
            options.threadType = DISPATCHERQUEUE_THREAD_TYPE.DQTYPE_THREAD_CURRENT;
            options.dwSize = Marshal.SizeOf(typeof(DispatcherQueueOptions));

            object queue = null;
            CreateDispatcherQueueController(options, out queue);
            return queue;
        }

        private void InitComposition(IntPtr hwndHost)
        {
            ICompositorDesktopInterop interop;

            Compositor = new Compositor();
            object iunknown = Compositor as object;
            interop = (ICompositorDesktopInterop)iunknown;
            IntPtr raw;
            interop.CreateDesktopWindowTarget(hwndHost, true, out raw);

            object rawObject = Marshal.GetObjectForIUnknown(raw);
            compositionTarget = (ICompositionTarget)rawObject;

            if (raw == null) { throw new Exception("QI Failed"); }
        }

        #region PInvoke declarations

        //typedef enum DISPATCHERQUEUE_THREAD_APARTMENTTYPE
        //{
        //    DQTAT_COM_NONE,
        //    DQTAT_COM_ASTA,
        //    DQTAT_COM_STA
        //};
        internal enum DISPATCHERQUEUE_THREAD_APARTMENTTYPE
        {
            DQTAT_COM_NONE = 0,
            DQTAT_COM_ASTA = 1,
            DQTAT_COM_STA = 2
        };

        //typedef enum DISPATCHERQUEUE_THREAD_TYPE
        //{
        //    DQTYPE_THREAD_DEDICATED,
        //    DQTYPE_THREAD_CURRENT
        //};
        internal enum DISPATCHERQUEUE_THREAD_TYPE
        {
            DQTYPE_THREAD_DEDICATED = 1,
            DQTYPE_THREAD_CURRENT = 2,
        };

        //struct DispatcherQueueOptions
        //{
        //    DWORD dwSize;
        //    DISPATCHERQUEUE_THREAD_TYPE threadType;
        //    DISPATCHERQUEUE_THREAD_APARTMENTTYPE apartmentType;
        //};
        [StructLayout(LayoutKind.Sequential)]
        internal struct DispatcherQueueOptions
        {
            public int dwSize;

            [MarshalAs(UnmanagedType.I4)]
            public DISPATCHERQUEUE_THREAD_TYPE threadType;

            [MarshalAs(UnmanagedType.I4)]
            public DISPATCHERQUEUE_THREAD_APARTMENTTYPE apartmentType;
        };

        //HRESULT CreateDispatcherQueueController(
        //  DispatcherQueueOptions options,
        //  ABI::Windows::System::IDispatcherQueueController** dispatcherQueueController
        //);
        [DllImport("coremessaging.dll", EntryPoint = "CreateDispatcherQueueController", CharSet = CharSet.Unicode)]
        internal static extern IntPtr CreateDispatcherQueueController(DispatcherQueueOptions options,
                                                [MarshalAs(UnmanagedType.IUnknown)]
                                               out object dispatcherQueueController);


        [DllImport("user32.dll", EntryPoint = "CreateWindowEx", CharSet = CharSet.Unicode)]
        internal static extern IntPtr CreateWindowEx(int dwExStyle,
                                                      string lpszClassName,
                                                      string lpszWindowName,
                                                      int style,
                                                      int x, int y,
                                                      int width, int height,
                                                      IntPtr hwndParent,
                                                      IntPtr hMenu,
                                                      IntPtr hInst,
                                                      [MarshalAs(UnmanagedType.AsAny)] object pvParam);

        [DllImport("user32.dll", EntryPoint = "DestroyWindow", CharSet = CharSet.Unicode)]
        internal static extern bool DestroyWindow(IntPtr hwnd);


        #endregion PInvoke declarations

    }
    #region COM Interop

    /*
    #undef INTERFACE
    #define INTERFACE ICompositorDesktopInterop
        DECLARE_INTERFACE_IID_(ICompositorDesktopInterop, IUnknown, "29E691FA-4567-4DCA-B319-D0F207EB6807")
        {
            IFACEMETHOD(CreateDesktopWindowTarget)(
                _In_ HWND hwndTarget,
                _In_ BOOL isTopmost,
                _COM_Outptr_ IDesktopWindowTarget * *result
                ) PURE;
        };
    */
    [ComImport]
    [Guid("29E691FA-4567-4DCA-B319-D0F207EB6807")]
    [InterfaceType(ComInterfaceType.InterfaceIsIUnknown)]
    public interface ICompositorDesktopInterop
    {
        void CreateDesktopWindowTarget(IntPtr hwndTarget, bool isTopmost, out IntPtr test);
    }

    //[contract(Windows.Foundation.UniversalApiContract, 2.0)]
    //[exclusiveto(Windows.UI.Composition.CompositionTarget)]
    //[uuid(A1BEA8BA - D726 - 4663 - 8129 - 6B5E7927FFA6)]
    //interface ICompositionTarget : IInspectable
    //{
    //    [propget] HRESULT Root([out] [retval] Windows.UI.Composition.Visual** value);
    //    [propput] HRESULT Root([in] Windows.UI.Composition.Visual* value);
    //}

    [ComImport]
    [Guid("A1BEA8BA-D726-4663-8129-6B5E7927FFA6")]
    [InterfaceType(ComInterfaceType.InterfaceIsIInspectable)]
    public interface ICompositionTarget
    {
        Windows.UI.Composition.Visual Root
        {
            get;
            set;
        }
    }

    #endregion COM Interop
}
```