---
title: Использование на визуальном уровне с Windows Forms
description: Ознакомьтесь с приемами с помощью API-интерфейсы Visual слоя в сочетании с существующим содержимым в Windows Forms для создания дополнительных анимаций и эффектов.
ms.date: 03/18/2019
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 0a3aff0bee68b971accd96f895601343726024d0
ms.sourcegitcommit: f0f933d5cf0be734373a7b03e338e65000cc3d80
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/21/2019
ms.locfileid: "65985034"
---
# <a name="using-the-visual-layer-with-windows-forms"></a>Использование на визуальном уровне с Windows Forms

Вы можете использовать API композиции среды выполнения Windows (также называется [визуальный слой](/windows/uwp/composition/visual-layer)) в приложениях Windows Forms для создания современных приложений, света, для пользователей Windows 10.

Полный код для этого руководства можно найти в GitHub: [Пример Windows Forms HelloComposition](https://github.com/Microsoft/Windows.UI.Composition-Win32-Samples/tree/master/dotnet/WinForms/HelloComposition).

## <a name="prerequisites"></a>предварительные требования

UWP, интерфейс API размещения имеет эти условия.

- Мы предполагаем, что у вас есть некоторый опыт работы с разработкой приложений, с помощью Windows Forms и универсальной платформы Windows. См. также:
  - [Приступая к работе с Windows Forms](/dotnet/framework/winforms/getting-started-with-windows-forms)
  - [Начало работы с приложениями Windows 10](/windows/uwp/get-started/)
  - [Давайте расширим приложение рабочего стола для Windows 10](/windows/uwp/porting/desktop-to-uwp-enhance)
- .NET framework 4.7.2 или более поздней версии
- Windows 10 версии 1803 или более поздней версии
- Пакет SDK Windows 10 17134 или более поздней версии

## <a name="how-to-use-composition-apis-in-windows-forms"></a>Как использовать интерфейсы API композиции в Windows Forms

В этом руководстве создайте простой пользовательский Интерфейс Windows Forms и добавление анимированных элементов композиции. Компоненты Windows Forms и композиции хранятся простой, но взаимодействия кода, показанного не зависит от сложности компонентов. Готовое приложение выглядит следующим образом.

![Выполняющееся приложение пользовательского интерфейса](images/visual-layer-interop/wf-comp-interop-app-ui.png)

## <a name="create-a-windows-forms-project"></a>Создайте проект Windows Forms

Первым шагом является создание проекта приложения Windows Forms, который включает в себя определение приложения и главной формы для пользовательского интерфейса.

Чтобы создать новый проект приложения Windows Forms в Visual C# с именем _HelloComposition_:

1. Откройте Visual Studio и выберите **файл** > **New** > **проекта**.<br/>**Новый проект** откроется диалоговое окно.
1. В разделе **установленные** категории, разверните **Visual C#**  узел, а затем выберите **Windows Desktop**.
1. Выберите **приложение Windows Forms (.NET Framework)** шаблона.
1. Введите имя _HelloComposition,_ выбрать Framework **.NET Framework 4.7.2**, затем нажмите кнопку **ОК**.

Visual Studio создаст проект и открывает конструктор для окна приложения по умолчанию с именем Form1.cs.

## <a name="configure-the-project-to-use-windows-runtime-apis"></a>Настройка проекта для использования API среды выполнения Windows

Для использования среды выполнения Windows (WinRT) API-интерфейсов в приложении Windows Forms, необходимо настроить проект Visual Studio для доступа к среде выполнения Windows. Кроме того векторы широко используются API композиции, поэтому необходимо добавить ссылки, необходимые для использования векторов.

Пакеты NuGet доступны обратиться к обоим этим требованиям. Установите последние версии этих пакетов для добавления необходимых ссылок в проект.  

- [Microsoft.Windows.SDK.Contracts](https://www.nuget.org/packages/Microsoft.Windows.SDK.Contracts) (требуется набор формат по умолчанию пакета управления для PackageReference.)
- [System.Numerics.Vectors](https://www.nuget.org/packages/System.Numerics.Vectors/)

> [!NOTE]
> Хотя рекомендуется использовать пакеты NuGet для настройки проекта, можно добавить необходимые ссылки вручную. Дополнительные сведения см. в разделе [рабочего стола в приложении для Windows 10](/windows/uwp/porting/desktop-to-uwp-enhance). В следующей таблице показаны файлы, которые необходимо добавить ссылки на.

|Файл|Location|
|--|--|
|System.Runtime.WindowsRuntime|C:\Windows\Microsoft.NET\Framework\v4.0.30319|
|Windows.Foundation.UniversalApiContract.winmd|C:\Program Files (x86)\Windows Kits\10\References\<*sdk version*>\Windows.Foundation.UniversalApiContract\<*version*>|
|Windows.Foundation.FoundationContract.winmd|C:\Program Files (x86)\Windows Kits\10\References\<*sdk version*>\Windows.Foundation.FoundationContract\<*version*>|
|System.Numerics.Vectors.dll|C:\WINDOWS\Microsoft.Net\assembly\GAC_MSIL\System.Numerics.Vectors\v4.0_4.0.0.0__b03f5f7f11d50a3a|
|System.Numerics.dll|C:\Program Files (x86)\Reference Assemblies\Microsoft\Framework\.NETFramework\v4.7.2|

## <a name="create-a-custom-control-to-manage-interop"></a>Создание пользовательского элемента управления для управления взаимодействия

Для размещения содержимого, созданного с использованием визуальном уровне, создайте пользовательский элемент управления, производный от [управления](/dotnet/api/system.windows.forms.control). Этот элемент управления предоставляет доступ к окну [обработки](/dotnet/api/system.windows.forms.control.handle), что необходимо для создания контейнера для вашего содержимого на визуальном уровне.

Это, где выполняется большая часть конфигурации для размещения API композиции. В этом элементе управления, использовании [службы вызова платформы (PInvoke)](/cpp/dotnet/calling-native-functions-from-managed-code) и [COM-взаимодействия](/dotnet/api/system.runtime.interopservices.comimportattribute) приведение композиции API-интерфейсов в приложении Windows Forms. Дополнительные сведения о PInvoke и COM-взаимодействия, см. в разделе [взаимодействие с неуправляемым кодом](/dotnet/framework/interop/index).

> [!TIP]
> Если вам нужно, проверьте полный код в конце учебника, чтобы убедиться, что весь код находится в нужных местах во время работы с учебником.

1. Добавьте новый файл пользовательского элемента управления в проект, который является производным от [управления](/dotnet/api/system.windows.forms.control).
    - В **обозревателе решений**, щелкните правой кнопкой мыши _HelloComposition_ проекта.
    - В контекстном меню выберите **добавить** > **новый элемент...** .
    - В **Добавление нового элемента** диалоговом окне выберите **пользовательский элемент управления**.
    - Назовите элемент управления _CompositionHost.cs_, затем нажмите кнопку **добавить**. CompositionHost.cs откроется в режиме конструктора.

1. Переключитесь в представление кода для CompositionHost.cs и добавьте следующий код к классу.

    ```csharp
    // Add
    // using Windows.UI.Composition;

    IntPtr hwndHost;
    object dispatcherQueue;
    protected ContainerVisual containerVisual;
    protected Compositor compositor;

    private ICompositionTarget compositionTarget;

    public Visual Child
    {
        set
        {
            if (compositor == null)
            {
                InitComposition(hwndHost);
            }
            compositionTarget.Root = value;
        }
    }
    ```

1. Добавьте код в конструктор.

    В конструкторе, вы вызываете _InitializeCoreDispatcher_ и _InitComposition_ методы. При создании этих методов на следующих шагах.

    ```csharp
    public CompositionHost()
    {
        InitializeComponent();

        // Get the window handle.
        hwndHost = Handle;

        // Create dispatcher queue.
        dispatcherQueue = InitializeCoreDispatcher();

        // Build Composition tree of content.
        InitComposition(hwndHost);
    }

1. Initialize a thread with a [CoreDispatcher](/uwp/api/windows.ui.core.coredispatcher). The core dispatcher is responsible for processing window messages and dispatching events for WinRT APIs. New instances of **Compositor** must be created on a thread that has a CoreDispatcher.
    - Create a method named _InitializeCoreDispatcher_ and add code to set up the dispatcher queue.

    ```csharp
    // Add
    // using System.Runtime.InteropServices;

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

    - Очереди диспетчера требует объявления PInvoke. Поместите это объявление в конце кода для класса. (Мы поместить этот код внутри области, чтобы сохранить код класса аккуратном.)

    ```csharp
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

    #endregion PInvoke declarations
    ```

    Теперь у очереди диспетчера готовы и можно приступить к инициализации и создания содержимого композиции.

1. Инициализировать [композитор](/uwp/api/windows.ui.composition.compositor). Компоновщиком является фабрику, которая создает различные типы в [Windows.UI.Composition](/uwp/api/windows.ui.composition) пространства имен, объединив визуальном уровне, система эффектов и системы анимации. Класс композитор также управляет временем существования объектов, созданных из фабрики.

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
        compositionTarget = (ICompositionTarget)rawObject;

        if (raw == null) { throw new Exception("QI Failed"); }

        containerVisual = compositor.CreateContainerVisual();
        Child = containerVisual;
    }
    ```

    - **ICompositorDesktopInterop** и **ICompositionTarget** требуют COM imports. Поместите следующий код после _CompositionHost_ класса, но внутри объявления пространства имен.

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

## <a name="create-a-custom-control-to-host-composition-elements"></a>Создание пользовательского элемента управления узла композиции элементов

Рекомендуется поместить код, который создает и управляет им в отдельный элемент управления, производный от CompositionHost композиции элементов. Это действие оставляет взаимодействия кода, который вы создали в классе CompositionHost многократного использования.

Здесь можно создать пользовательский элемент управления, производный от CompositionHost. Этот элемент управления добавляется на панель инструментов Visual Studio, поэтому ее можно добавить в форму.

1. Добавьте новый файл пользовательского элемента управления в проект, который является производным от CompositionHost.
    - В **обозревателе решений**, щелкните правой кнопкой мыши _HelloComposition_ проекта.
    - В контекстном меню выберите **добавить** > **новый элемент...** .
    - В **Добавление нового элемента** диалоговом окне выберите **пользовательский элемент управления**.
    - Назовите элемент управления _CompositionHostControl.cs_, затем нажмите кнопку **добавить**. CompositionHostControl.cs откроется в режиме конструктора.

1. В панели свойств для CompositionHostControl.cs конструкторе присвойте **BackColor** свойства **ControlLight**.

    Установка цвета фона является необязательным. Мы делаем его для просмотра пользовательского элемента управления на фоне формы.

1. Переключитесь в представление кода для CompositionHostControl.cs и добавьте в объявление класса для наследования от CompositionHost.

    ```csharp
    class CompositionHostControl : CompositionHost
    ```

1. Измените конструктор, чтобы вызвать базовый конструктор.

    ```csharp
    public CompositionHostControl() : base()
    {

    }
    ```

### <a name="add-composition-elements"></a>Добавьте элементы композиции

С помощью инфраструктуры на месте теперь можно добавить содержимое композиции для пользовательского интерфейса приложения.

В этом примере добавьте код к классу CompositionHostControl, который создает и выполняет анимацию простой [SpriteVisual](/uwp/api/windows.ui.composition.spritevisual).

1. Добавьте элемент композиции.

    В CompositionHostControl.cs добавьте в класс CompositionHostControl этих методов.

    ```csharp
    // Add
    // using Windows.UI.Composition;

    public void AddElement(float size, float offsetX, float offsetY)
    {
        var visual = compositor.CreateSpriteVisual();
        visual.Size = new Vector2(size, size); // Requires references
        visual.Brush = compositor.CreateColorBrush(GetRandomColor());
        visual.Offset = new Vector3(offsetX, offsetY, 0);
        containerVisual.Children.InsertAtTop(visual);

        AnimateSquare(visual, 3);
    }

    private void AnimateSquare(SpriteVisual visual, int delay)
    {
        float offsetX = (float)(visual.Offset.X);
        Vector3KeyFrameAnimation animation = compositor.CreateVector3KeyFrameAnimation();
        float bottom = Height - visual.Size.Y;
        animation.InsertKeyFrame(1f, new Vector3(offsetX, bottom, 0f));
        animation.Duration = TimeSpan.FromSeconds(2);
        animation.DelayTime = TimeSpan.FromSeconds(delay);
        visual.StartAnimation("Offset", animation);
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

## <a name="add-the-control-to-your-form"></a>Добавление элемента управления в форму

Теперь, когда у вас есть пользовательский элемент управления для размещения содержимого композиции, его можно добавить в приложение пользовательского интерфейса. Здесь можно добавить экземпляр CompositionHostControl, созданный на предыдущем шаге. CompositionHostControl автоматически добавляется на панель инструментов Visual Studio в разделе  **_имя_проекта_ компоненты**.

1. В окне конструктора Form1.CS добавьте кнопку в пользовательском Интерфейсе.

    - Перетащите кнопку из панели элементов на форму Form1. Поместите его в левом верхнем углу формы. (См. изображение в начале этого руководства для проверки размещения элементов управления).
    - В панели «Свойства» измените **текст** свойства из _button1_ для _элемент композиции Add_.
    - Изменить размер кнопки, чтобы отобразить весь текст.

    (Дополнительные сведения см. в разделе [как: Добавление элементов управления в Windows Forms](/dotnet/framework/winforms/controls/how-to-add-controls-to-windows-forms).)

1. Добавьте CompositionHostControl пользовательского интерфейса.

    - Перетащите CompositionHostControl из области элементов на форму Form1. Поместите его справа от кнопки.
    - Изменение размера CompositionHost таким образом, заполнить оставшуюся часть формы.

1. События нажатия кнопки дескриптор.

   - В панели «Свойства» щелкните изображением молнии, чтобы переключиться в представление событий.
   - В списке событий выберите **щелкните** событие, тип *Button_Click*, и нажмите клавишу ВВОД.
   - Этот код добавляется в файле Form1.cs:

    ```csharp
    private void Button_Click(object sender, EventArgs e)
    {

    }
    ```

1. Добавление кода для кнопки щелкните обработчик для создания новых элементов.

    - В файле Form1.cs добавьте код для *Button_Click* обработчик событий, вы создали ранее. Этот код вызывает _CompositionHostControl1.AddElement_ для создания нового элемента с размером случайным и смещение. (Автоматически с именем экземпляра CompositionHostControl _compositionHostControl1_ при его перетаскивании на форму.)

    ```csharp
    // Add
    // using System;

    private void Button_Click(object sender, RoutedEventArgs e)
    {
        Random random = new Random();
        float size = random.Next(50, 150);
        float offsetX = random.Next(0, (int)(compositionHostControl1.Width - size));
        float offsetY = random.Next(0, (int)(compositionHostControl1.Height/2 - size));
        compositionHostControl1.AddElement(size, offsetX, offsetY);
    }
    ```

Теперь можно построить и запустить приложение Windows Forms. При нажатии кнопки, вы увидите анимированных квадратов, добавляется к UI.

## <a name="next-steps"></a>Следующие шаги

Более полный пример, который основан на той же инфраструктуре, см. в разделе [пример интеграции Windows Forms Visual слой](https://github.com/Microsoft/Windows.UI.Composition-Win32-Samples/tree/master/dotnet/WinForms/VisualLayerIntegration) на сайте GitHub.

## <a name="additional-resources"></a>Дополнительные ресурсы

- [Приступая к работе с Windows Forms](/dotnet/framework/winforms/getting-started-with-windows-forms) (.NET)
- [Взаимодействие с неуправляемым кодом](/dotnet/framework/interop/) (.NET)
- [Начало работы с приложениями Windows 10](/windows/uwp/get-started/) (UWP)
- [Давайте расширим приложение рабочего стола для Windows 10](/windows/uwp/porting/desktop-to-uwp-enhance) (UWP)
- [Пространство имен Windows.UI.Composition](/uwp/api/windows.ui.composition) (UWP)

## <a name="complete-code"></a>Полный код

Ниже приведен полный код для этого руководства.

### <a name="form1cs"></a>Form1.cs

```csharp
using System;
using System.Windows.Forms;

namespace HelloComposition
{
    public partial class Form1 : Form
    {
        public Form1()
        {
            InitializeComponent();
        }

        private void Button_Click(object sender, EventArgs e)
        {
            Random random = new Random();
            float size = random.Next(50, 150);
            float offsetX = random.Next(0, (int)(compositionHostControl1.Width - size));
            float offsetY = random.Next(0, (int)(compositionHostControl1.Height/2 - size));
            compositionHostControl1.AddElement(size, offsetX, offsetY);
        }
    }
}
```

### <a name="compositionhostcontrolcs"></a>CompositionHostControl.cs

```csharp
using System;
using System.Numerics;
using Windows.UI.Composition;

namespace HelloComposition
{
    class CompositionHostControl : CompositionHost
    {
        public CompositionHostControl() : base()
        {

        }

        public void AddElement(float size, float offsetX, float offsetY)
        {
            var visual = compositor.CreateSpriteVisual();
            visual.Size = new Vector2(size, size); // Requires references
            visual.Brush = compositor.CreateColorBrush(GetRandomColor());
            visual.Offset = new Vector3(offsetX, offsetY, 0);
            containerVisual.Children.InsertAtTop(visual);

            AnimateSquare(visual, 3);
        }

        private void AnimateSquare(SpriteVisual visual, int delay)
        {
            float offsetX = (float)(visual.Offset.X);
            Vector3KeyFrameAnimation animation = compositor.CreateVector3KeyFrameAnimation();
            float bottom = Height - visual.Size.Y;
            animation.InsertKeyFrame(1f, new Vector3(offsetX, bottom, 0f));
            animation.Duration = TimeSpan.FromSeconds(2);
            animation.DelayTime = TimeSpan.FromSeconds(delay);
            visual.StartAnimation("Offset", animation);
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
using System.Windows.Forms;
using Windows.UI.Composition;

namespace HelloComposition
{
    public partial class CompositionHost : Control
    {
        IntPtr hwndHost;
        object dispatcherQueue;
        protected ContainerVisual containerVisual;
        protected Compositor compositor;
        private ICompositionTarget compositionTarget;

        public Visual Child
        {
            set
            {
                if (compositor == null)
                {
                    InitComposition(hwndHost);
                }
                compositionTarget.Root = value;
            }
        }

        public CompositionHost()
        {
            // Get the window handle.
            hwndHost = Handle;

            // Create dispatcher queue.
            dispatcherQueue = InitializeCoreDispatcher();

            // Build Composition tree of content.
            InitComposition(hwndHost);
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

            compositor = new Compositor();
            object iunknown = compositor as object;
            interop = (ICompositorDesktopInterop)iunknown;
            IntPtr raw;
            interop.CreateDesktopWindowTarget(hwndHost, true, out raw);

            object rawObject = Marshal.GetObjectForIUnknown(raw);
            compositionTarget = (ICompositionTarget)rawObject;

            if (raw == null) { throw new Exception("QI Failed"); }

            containerVisual = compositor.CreateContainerVisual();
            Child = containerVisual;
        }

        protected override void OnPaint(PaintEventArgs pe)
        {
            base.OnPaint(pe);
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