---
title: Использование визуального уровня с Windows Forms
description: Узнайте о методах использования API-интерфейсов визуального уровня в сочетании с существующим Windows Forms содержимым для создания сложных анимаций и эффектов.
ms.date: 03/18/2019
ms.topic: article
keywords: windows 10, uwp
ms.author: jimwalk
author: jwmsft
ms.localizationpriority: medium
ms.openlocfilehash: 9da9dee48beef6e3c1cd38ffbe9761ed89fd940d
ms.sourcegitcommit: 93d0b2996b4742b33cd6d641e036f42672cf5238
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/23/2019
ms.locfileid: "69999640"
---
# <a name="using-the-visual-layer-with-windows-forms"></a>Использование визуального уровня с Windows Forms

Вы можете использовать среда выполнения Windows интерфейсы API композиции (также называемые [визуальным слоем](/windows/uwp/composition/visual-layer)) в приложениях Windows Forms, чтобы создавать современные возможности для пользователей Windows 10.

Полный код для этого руководства доступен на сайте GitHub: [Windows Forms пример хеллокомпоситион](https://github.com/Microsoft/Windows.UI.Composition-Win32-Samples/tree/master/dotnet/WinForms/HelloComposition).

## <a name="prerequisites"></a>Предварительные требования

В API размещения UWP имеются следующие необходимые компоненты.

- Предположим, что у вас есть опыт разработки приложений с помощью Windows Forms и UWP. См. также:
  - [начало работы с Windows Forms](/dotnet/framework/winforms/getting-started-with-windows-forms)
  - [Начало работы с приложениями Windows 10](/windows/uwp/get-started/)
  - [Улучшите классическое приложение для Windows 10](/windows/uwp/porting/desktop-to-uwp-enhance)
- .NET Framework 4.7.2 или более поздней версии
- Windows 10 версии 1803 или более поздней
- Windows 10 SDK 17134 или более поздней версии

## <a name="how-to-use-composition-apis-in-windows-forms"></a>Использование API-интерфейсов композиции в Windows Forms

В этом руководстве вы создадите простой Windows Forms пользовательский интерфейс и добавите в него анимированные элементы композиции. Как компонент Windows Forms, так и компоненты композиции хранятся просто, но код взаимодействия отображается одинаково независимо от сложности компонентов. Законченное приложение выглядит следующим образом.

![Пользовательский интерфейс выполняющегося приложения](images/visual-layer-interop/wf-comp-interop-app-ui.png)

## <a name="create-a-windows-forms-project"></a>Создание проекта Windows Forms

Первым шагом является создание проекта Windows Forms приложения, включающего определение приложения и основную форму пользовательского интерфейса.

Чтобы создать новый проект приложения Windows Forms в Visual C# с именем _хеллокомпоситион_:

1. Откройте Visual Studio и выберите **файл** > **создать** > **проект**.<br/>Откроется диалоговое окно **Новый проект** .
1. В категории **установленные** разверните узел **визуальный C#**  элемент, а затем выберите **Рабочий стол Windows**.
1. Выберите шаблон **Windows Forms приложение (.NET Framework)** .
1. Введите имя _хеллокомпоситион,_ выберите Framework **.NET Framework 4.7.2**, а затем нажмите кнопку **ОК**.

Visual Studio создаст проект и откроет конструктор для окна приложения по умолчанию с именем Form1.cs.

## <a name="configure-the-project-to-use-windows-runtime-apis"></a>Настройка проекта для использования среда выполнения Windows API

Чтобы использовать API среда выполнения Windows (WinRT) в приложении Windows Forms, необходимо настроить проект Visual Studio для доступа к среда выполнения Windows. Кроме того, векторы широко используются API-интерфейсами компоновки, поэтому необходимо добавить ссылки, необходимые для использования векторов.

Пакеты NuGet доступны для решения этих задач. Установите последние версии этих пакетов, чтобы добавить необходимые ссылки в проект.  

- [Microsoft. Windows. SDK. Contracts](https://www.nuget.org/packages/Microsoft.Windows.SDK.Contracts) (требуется формат управления пакетами по умолчанию, установленный в PackageReference.)
- [System. Numerics. Vectors](https://www.nuget.org/packages/System.Numerics.Vectors/)

> [!NOTE]
> Хотя мы рекомендуем использовать пакеты NuGet для настройки проекта, можно добавить необходимые ссылки вручную. Дополнительные сведения см. в разделе [расширение классических приложений для Windows 10](/windows/uwp/porting/desktop-to-uwp-enhance). В следующей таблице приведены файлы, на которые необходимо добавить ссылки.

|Файл|Location|
|--|--|
|System.Runtime.WindowsRuntime|C:\Windows\Microsoft.NET\Framework\v4.0.30319|
|Windows.Foundation.UniversalApiContract.winmd|C:\Program Files (x86) \Windows\<Kits\10\References*SDK версия*>\<*версия* \виндовс.фаундатион.универсалапиконтракт>|
|Windows.Foundation.FoundationContract.winmd|C:\Program Files (x86) \Windows\<Kits\10\References*SDK версия*>\<*версия* \виндовс.фаундатион.фаундатионконтракт>|
|System. Numerics. Vectors. dll|C:\WINDOWS\Microsoft.Net\assembly\GAC_MSIL\System.Numerics.Vectors\v4.0_4.0.0.0__b03f5f7f11d50a3a|
|System. Numerics. dll|C:\Program Files (x86) \Reference\.ассемблиес\микрософт\фрамеворк NETFramework\v4.7.2|

## <a name="create-a-custom-control-to-manage-interop"></a>Создание пользовательского элемента управления для управления взаимодействием

Для размещения содержимого, созданного с помощью визуального слоя, создается пользовательский элемент управления, производный от [Control](/dotnet/api/system.windows.forms.control). Этот элемент управления предоставляет доступ к [обработчику](/dotnet/api/system.windows.forms.control.handle)окна, который требуется для создания контейнера для содержимого визуального уровня.

Здесь вы выполняете большую часть настройки для API-интерфейсов компоновки размещения. В этом элементе управления используются [службы вызова платформы (PInvoke)](/cpp/dotnet/calling-native-functions-from-managed-code) и [COM-взаимодействие](/dotnet/api/system.runtime.interopservices.comimportattribute) для переноса api композиции в приложение Windows Forms. Дополнительные сведения об PInvoke и COM-взаимодействии см. в разделе взаимодействие [с неуправляемым кодом](/dotnet/framework/interop/index).

> [!TIP]
> При необходимости проверьте полный код в конце руководства, чтобы убедиться в том, что весь код находится в нужном месте при работе с учебником.

1. Добавьте новый файл пользовательского элемента управления в проект, производный от [Control](/dotnet/api/system.windows.forms.control).
    - В **Обозреватель решений**щелкните правой кнопкой мыши проект _хеллокомпоситион_ .
    - В контекстном меню выберите **Добавить** > **новый элемент...** .
    - В диалоговом окне **Добавление нового элемента** выберите **Пользовательский элемент управления**.
    - Присвойте элементу управления имя _CompositionHost.CS_, а затем нажмите кнопку **Добавить**. CompositionHost.cs откроется в представление конструирования.

1. Перейдите в представление кода для CompositionHost.cs и добавьте следующий код в класс.

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

    В конструкторе вызываются методы _инитиализекоредиспатчер_ и _иниткомпоситион_ . Эти методы создаются на следующих шагах.

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
    ```

1. Инициализируйте поток с помощью [CoreDispatcher](/uwp/api/windows.ui.core.coredispatcher). Основной диспетчер отвечает за обработку сообщений окон и диспетчеризации событий для API-интерфейсов WinRT. Новые экземпляры **компоновщика** должны быть созданы в потоке с CoreDispatcher.
    - Создайте метод с именем _инитиализекоредиспатчер_ и добавьте код для настройки очереди Dispatcher.

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

    - Для очереди диспетчера требуется объявление PInvoke. Поместите это объявление в конец кода для класса. (Мы разместите этот код внутри региона, чтобы код класса оставался в нужной области.)

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

    Теперь очередь Dispatcher готова и может начать инициализацию и создание содержимого компоновки.

1. Инициализируйте [Компоновщик](/uwp/api/windows.ui.composition.compositor). Компоновщик — это фабрика, которая создает разнообразные типы в пространстве имен [Windows. UI. композиция](/uwp/api/windows.ui.composition) , охватывающем визуальный уровень, систему эффектов и систему анимации. Класс компоновщика также управляет временем существования объектов, созданных из фабрики.

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

    - Для **икомпоситордесктопинтероп** и **икомпоситионтаржет** требуются импорты com. Поместите этот код после класса _компоситионхост_ , но внутри объявления пространства имен.

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

## <a name="create-a-custom-control-to-host-composition-elements"></a>Создание пользовательского элемента управления для размещения элементов композиции

Рекомендуется разместить код, который создает элементы композиции и управляет ими, в отдельном элементе управления, производном от Компоситионхост. Это позволяет использовать код взаимодействия, созданный в классе Компоситионхост, для повторного использования.

Здесь создается пользовательский элемент управления, производный от Компоситионхост. Этот элемент управления добавляется в панель элементов Visual Studio, чтобы его можно было добавить в форму.

1. Добавьте новый файл пользовательского элемента управления в проект, производный от Компоситионхост.
    - В **Обозреватель решений**щелкните правой кнопкой мыши проект _хеллокомпоситион_ .
    - В контекстном меню выберите **Добавить** > **новый элемент...** .
    - В диалоговом окне **Добавление нового элемента** выберите **Пользовательский элемент управления**.
    - Присвойте элементу управления имя _CompositionHostControl.CS_, а затем нажмите кнопку **Добавить**. CompositionHostControl.cs откроется в представление конструирования.

1. В области Свойства представления конструктора CompositionHostControl.cs задайте для свойства **BackColor** значение **контроллигхт**.

    Задать цвет фона необязательно. Это делается здесь, чтобы пользовательский элемент управления можно было видеть в виде фона формы.

1. Перейдите в представление кода для CompositionHostControl.cs и обновите объявление класса, чтобы оно было производным от Компоситионхост.

    ```csharp
    class CompositionHostControl : CompositionHost
    ```

1. Обновите конструктор, чтобы вызвать базовый конструктор.

    ```csharp
    public CompositionHostControl() : base()
    {

    }
    ```

### <a name="add-composition-elements"></a>Добавление элементов композиции

Теперь, когда инфраструктура существует, вы можете добавить в пользовательский интерфейс приложения составное содержимое.

В этом примере вы добавляете код в класс Компоситионхостконтрол, который создает и анимируется простой [спритевисуал](/uwp/api/windows.ui.composition.spritevisual).

1. Добавьте элемент композиции.

    В CompositionHostControl.cs добавьте эти методы в класс Компоситионхостконтрол.

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

Теперь, когда у вас есть пользовательский элемент управления для размещения содержимого композиции, его можно добавить в пользовательский интерфейс приложения. Здесь вы добавляете экземпляр Компоситионхостконтрол, созданный на предыдущем шаге. Компоситионхостконтрол автоматически добавляется в панель элементов Visual Studio в разделе **компоненты _имени проекта_** .

1. В режиме конструктора Form1.CS добавьте кнопку в пользовательский интерфейс.

    - Перетащите кнопку из панели элементов на форму Form1. Поместите его в левый верхний угол формы. (Чтобы проверить размещение элементов управления, см. образ в начале руководства.)
    - На панели Свойства измените свойство **Text** с _Button1_ на _Add элемент композиции_.
    - Измените размер кнопки, чтобы показать весь текст.

    (Дополнительные сведения см. в [разделе как Добавьте элементы управления в](/dotnet/framework/winforms/controls/how-to-add-controls-to-windows-forms)Windows Forms.)

1. Добавьте Компоситионхостконтрол в пользовательский интерфейс.

    - Перетащите Компоситионхостконтрол из панели элементов на форму Form1. Поместите его справа от кнопки.
    - Измените размер Компоситионхост, чтобы он заполнил оставшуюся часть формы.

1. Обрабатывает событие нажатия кнопки.

   - На панели «Свойства» щелкните молнию, чтобы перейти к представлению «события».
   - В списке событий выберите событие **щелчка** , введите *Button_Click*и нажмите клавишу ВВОД.
   - Этот код добавляется в Form1.cs:

    ```csharp
    private void Button_Click(object sender, EventArgs e)
    {

    }
    ```

1. Добавьте код для обработчика нажатия кнопки, чтобы создать новые элементы.

    - В Form1.cs добавьте код в созданный ранее обработчик событий *Button_Click* . Этот код вызывает _CompositionHostControl1. адделемент_ для создания нового элемента со случайно сформированным размером и смещением. (Экземпляр Компоситионхостконтрол был автоматически назван _compositionHostControl1_ при перетаскивании его на форму.)

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

Теперь вы можете создать и запустить приложение Windows Forms. При нажатии кнопки вы увидите, что в пользовательский интерфейс добавлены анимированные квадраты.

## <a name="next-steps"></a>Следующие шаги

Более полный пример, построенный на той же инфраструктуре, см. в разделе [Windows Forms пример интеграции визуального уровня](https://github.com/Microsoft/Windows.UI.Composition-Win32-Samples/tree/master/dotnet/WinForms/VisualLayerIntegration) на сайте GitHub.

## <a name="additional-resources"></a>Дополнительные ресурсы

- [Начало работы с Windows Forms](/dotnet/framework/winforms/getting-started-with-windows-forms) Платформе
- [Взаимодействие с неуправляемым кодом](/dotnet/framework/interop/) Платформе
- [Начало работы с приложениями Windows 10](/windows/uwp/get-started/) UWP
- [Улучшите классическое приложение для Windows 10](/windows/uwp/porting/desktop-to-uwp-enhance) UWP
- [Пространство имен Windows. UI. композиция](/uwp/api/windows.ui.composition) UWP

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