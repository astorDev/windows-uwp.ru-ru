---
title: Использование визуального уровня на платформе Windows Forms
description: Изучите методы использования интерфейсов API визуального уровня в сочетании с существующим содержимым Windows Forms, чтобы создавать сложные анимации и эффекты.
ms.date: 03/18/2019
ms.topic: article
keywords: windows 10, uwp
ms.author: jimwalk
author: jwmsft
ms.localizationpriority: medium
ms.openlocfilehash: 9da9dee48beef6e3c1cd38ffbe9761ed89fd940d
ms.sourcegitcommit: 93d0b2996b4742b33cd6d641e036f42672cf5238
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/23/2019
ms.locfileid: "69999640"
---
# <a name="using-the-visual-layer-with-windows-forms"></a>Использование визуального уровня на платформе Windows Forms

Вы можете использовать интерфейсы API Composition среды выполнения Windows (также называемые [визуальным уровнем](/windows/uwp/composition/visual-layer)) в приложениях Windows Forms, чтобы создавать современные интерфейсы для пользователей Windows 10.

Полный код для этого руководства доступен на сайте GitHub: [Пример приложения Windows Forms HelloComposition](https://github.com/Microsoft/Windows.UI.Composition-Win32-Samples/tree/master/dotnet/WinForms/HelloComposition)

## <a name="prerequisites"></a>Предварительные условия

Для использования API размещения для UWP накладываются приведенные ниже предварительные требования.

- Предполагается, что у вас есть опыт разработки приложений с помощью Windows Forms и UWP. См. также:
  - [Приступая к работе с Windows Forms](/dotnet/framework/winforms/getting-started-with-windows-forms)
  - [Приступая к работе с приложениями для Windows 10](/windows/uwp/get-started/)
  - [Улучшение классического приложения для Windows 10](/windows/uwp/porting/desktop-to-uwp-enhance)
- .NET Framework 4.7.2 или более поздней версии.
- Windows 10 версии 1803 или более поздней версии.
- Пакет SDK для Windows 10 версии 17134 или более поздней версии.

## <a name="how-to-use-composition-apis-in-windows-forms"></a>Как использовать интерфейсы API Composition в Windows Forms

При работе с этим руководством вы создадите пользовательский интерфейс приложения Windows Forms и добавите в него анимированные элементы Composition. Компоненты Windows Forms и компоненты Composition простые, но приведенный код взаимодействия одинаков независимо от сложности компонентов. Завершенное приложение выглядит следующим образом.

![Пользовательский интерфейс выполняющегося приложения](images/visual-layer-interop/wf-comp-interop-app-ui.png)

## <a name="create-a-windows-forms-project"></a>Создание проекта Windows Forms

Первым шагом является создание проекта приложения Windows Forms, включающего в себя определение приложения и главную форму для пользовательского интерфейса.

Чтобы создать проект приложения Windows Forms на Visual C# с именем _HelloComposition_, сделайте следующее.

1. Откройте Visual Studio и выберите **Файл** > **Создать** > **Проект**.<br/>Появится диалоговое окно **Создать проект**.
1. В категории **Установлены** разверните узел **Visual C#** , а затем выберите **Windows Desktop**.
1. Выберите шаблон **Приложение Windows Forms (.NET Framework)** .
1. Введите имя _HelloComposition_, выберите платформу **.NET Framework 4.7.2**, а затем нажмите кнопку **ОК**.

Visual Studio создаст проект и откроет конструктор для окна приложения по умолчанию, Form1.xaml.

## <a name="configure-the-project-to-use-windows-runtime-apis"></a>Настройка проекта для использования интерфейсов API среды выполнения Windows

Чтобы использовать API среды выполнения Windows (WinRT) в приложении Windows Forms, необходимо настроить проект Visual Studio для доступа к среде выполнения Windows. Кроме того, API Composition активно используют векторы, поэтому нужно добавить ссылки, необходимые для использования векторов.

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

## <a name="create-a-custom-control-to-manage-interop"></a>Создание пользовательского элемента управления для управления взаимодействием

Для размещения содержимого, созданного с помощью визуального слоя, создается пользовательский элемент управления, производный от [Control](/dotnet/api/system.windows.forms.control). Этот элемент управления предоставляет доступ к [дескриптору](/dotnet/api/system.windows.forms.control.handle) окна, который нужен, чтобы создать контейнер для содержимого визуального уровня.

Здесь потребуется выполнить большую часть настройки для размещения интерфейсов API Composition. В этом элементе управления для добавления интерфейсов API Composition в приложение Windows Forms используются [службы вызова платформы (PInvoke)](/cpp/dotnet/calling-native-functions-from-managed-code) и [взаимодействие COM](/dotnet/api/system.runtime.interopservices.comimportattribute). Дополнительные сведения о PInvoke и взаимодействии COM см. в разделе [Взаимодействие с неуправляемым кодом](/dotnet/framework/interop/index).

> [!TIP]
> При необходимости сверьтесь с полным кодом в конце руководства, чтобы убедиться в том, что весь код добавлен в соответствующе места при работе с этим руководством.

1. Добавьте в проект новый файл пользовательского элемента управления, производного от [Control](/dotnet/api/system.windows.forms.control).
    - В **обозревателе решений** щелкните проект _HelloComposition_ правой кнопкой мыши.
    - В контекстном меню выберите **Добавить** > **Новый элемент**.
    - В окне **Добавление нового элемента** выберите **Пользовательский элемент управления**.
    - Присвойте элементу управления имя _CompositionHost.cs_, а затем щелкните **Добавить**. Файл CompositionHost.cs откроется в представлении конструктора.

1. Перейдите к представлению кода для CompositionHost.cs и добавьте приведенный ниже код в класс.

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

    В этом конструкторе вызываются методы _InitializeCoreDispatcher_ и _InitComposition_. Эти методы создаются на следующих шагах.

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

1. Инициализируйте поток с помощью [CoreDispatcher](/uwp/api/windows.ui.core.coredispatcher). Основной диспетчер отвечает за обработку сообщений окон и диспетчеризацию событий интерфейсов API WinRT. Новые экземпляры **Compositor** должны создаваться в потоке с CoreDispatcher.
    - Создайте метод _InitializeCoreDispatcher_ и добавьте код для настройки очереди диспетчера.

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

    - Для очереди диспетчера требуется объявление PInvoke. Поместите это объявление в конец блока кода класса. (Мы разместим этот код внутри области, чтобы сделать код класса аккуратным.)

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

    Теперь очередь диспетчера готова, и можно начать инициализацию и создание содержимого Composition.

1. Инициализируйте [Compositor](/uwp/api/windows.ui.composition.compositor). Compositor — это фабрика, которая создает разные типы в пространстве имен [Windows.UI.Composition](/uwp/api/windows.ui.composition), охватывающем визуальный уровень, систему эффектов и систему анимации. Класс Compositor также управляет временем существования объектов, созданных из фабрики.

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

## <a name="create-a-custom-control-to-host-composition-elements"></a>Создание пользовательского элемента управления для размещения элементов композиции

Рекомендуется разместить код, который создает элементы композиции и управляет ими, в отдельном элементе управления, производном от CompositionHost. Это позволяет многократно использовать код взаимодействия, созданный в классе CompositionHost.

Здесь создается пользовательский элемент управления, производный от CompositionHost. Этот элемент управления добавляется на панель элементов Visual Studio, чтобы его можно было добавить в форму.

1. Добавьте в проект новый файл пользовательского элемента управления, производного от CompositionHost.
    - В **обозревателе решений** щелкните проект _HelloComposition_ правой кнопкой мыши.
    - В контекстном меню выберите **Добавить** > **Новый элемент**.
    - В окне **Добавление нового элемента** выберите **Пользовательский элемент управления**.
    - Присвойте элементу управления имя _CompositionHostControl.cs_, а затем щелкните **Добавить**. Файл CompositionHostControl.cs откроется в представлении конструктора.

1. В области "Свойства" представления конструктора для CompositionHostControl.cs задайте для свойства **BackColor** значение **ControlLight**.

    Задавать цвет фона необязательно. Мы делаем это здесь, чтобы пользовательский элемент управления выделялся на фоне формы.

1. Переключитесь на представление кода CompositionHostControl.cs и обновите объявление класса, чтобы он стал производным от CompositionHost.

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

Теперь, когда инфраструктура настроена, можно добавить содержимое Composition в пользовательский интерфейс приложения.

В этом примере вы добавите в класс CompositionHostControl код, который создает и анимирует простой элемент [SpriteVisual](/uwp/api/windows.ui.composition.spritevisual).

1. Добавьте элемент композиции.

    В CompositionHostControl.cs добавьте приведенные ниже методы в класс CompositionHostControl.

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

Теперь, когда у вас есть пользовательский элемент управления для размещения содержимого Composition, его можно добавить в пользовательский интерфейс приложения. Здесь следует добавить экземпляр CompositionHostControl, созданный на предыдущем шаге. CompositionHostControl автоматически добавляется на панель элементов Visual Studio в области **_имя проекта_ Компоненты**.

1. В представлении конструктора для Form1.CS добавьте кнопку в пользовательский интерфейс.

    - Перетащите элемент Button с панели элементов на Form1. Поместите его в левый верхний угол формы. (Чтобы проверить расположение элементов управления, посмотрите на картинку в начале руководства.)
    - В области "Свойства" измените значение свойства **Text** с _Button1_ на _Add composition element_.
    - Измените размер элемента Button, чтобы вместить весь текст.

    (Дополнительные сведения: [Практическое руководство. Добавление элементов управления в формы Windows Forms](/dotnet/framework/winforms/controls/how-to-add-controls-to-windows-forms).)

1. Добавьте элемент CompositionHostControl в пользовательский интерфейс.

    - Перетащите CompositionHostControl из панели элементов на форму Form1. Поместите его справа от Button.
    - Измените размер CompositionHost, чтобы заполнить оставшуюся часть формы.

1. Обработайте событие нажатия кнопки.

   - В области "Свойства" щелкните значок молнии, чтобы перейти к представлению "События".
   - Из списка событий выберите событие **Click**, введите *Button_Click* и нажмите клавишу ВВОД.
   - Этот код добавится в Form1.cs.

    ```csharp
    private void Button_Click(object sender, EventArgs e)
    {

    }
    ```

1. Добавьте в обработчик нажатия кнопки код для создания новых элементов.

    - В Form1.cs добавьте код в обработчик событий *Button_Click*, созданный ранее. Этот код вызывает _CompositionHostControl1.AddElement_ для создания нового элемента со случайно заданным размером и смещением. (Экземпляр CompositionHostControl был автоматически назван _compositionHostControl1_ при перетаскивании на форму.)

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

Теперь выполните сборку приложения Windows Forms и запустите его. Если вы нажмете кнопку, в пользовательском интерфейсе должны появиться анимированные квадраты.

## <a name="next-steps"></a>Дальнейшие действия

Более полный [пример интеграции визуального уровня в Windows Forms](https://github.com/Microsoft/Windows.UI.Composition-Win32-Samples/tree/master/dotnet/WinForms/VisualLayerIntegration), основанный на той же инфраструктуре, доступен на сайте GitHub.

## <a name="additional-resources"></a>Дополнительные ресурсы

- [Приступая к работе с Windows Forms](/dotnet/framework/winforms/getting-started-with-windows-forms) (.NET)
- [Взаимодействие с неуправляемым кодом](/dotnet/framework/interop/) (.NET)
- [Приступая к работе с приложениями для Windows 10](/windows/uwp/get-started/) (UWP)
- [Улучшение классического приложения для Windows 10](/windows/uwp/porting/desktop-to-uwp-enhance) (UWP)
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