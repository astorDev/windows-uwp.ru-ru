---
description: В этой статье показано, как разместить стандартный элемент управления UWP в приложении WPF с помощью XAML-островов.
title: Размещение стандартного элемента управления UWP в приложении WPF с помощью XAML-островов
ms.date: 01/24/2020
ms.topic: article
keywords: Windows 10, UWP, Windows Forms, WPF, острова XAML, обтекаемые элементы управления, стандартные элементы управления, InkCanvas, Инктулбар
ms.author: mcleans
author: mcleanbyron
ms.localizationpriority: medium
ms.custom: 19H1
ms.openlocfilehash: d6e8704d61589731f09de7c16b0eae987d593968
ms.sourcegitcommit: 0426013dc04ada3894dd41ea51ed646f9bb17f6d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2020
ms.locfileid: "78853021"
---
# <a name="host-a-standard-uwp-control-in-a-wpf-app-using-xaml-islands"></a>Размещение стандартного элемента управления UWP в приложении WPF с помощью XAML-островов

В этой статье показано два способа размещения стандартного элемента управления UWP (то есть первого элемента управления UWP, предоставляемого Windows SDK или библиотеки Винуи) в приложении WPF с помощью [XAML-островов](xaml-islands.md):

* Здесь показано, как разместить элементы управления UWP [InkCanvas](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.InkCanvas) и [инктулбар](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.inktoolbar) с помощью [упакованных элементов управления](xaml-islands.md#wrapped-controls) в наборе средств сообщества Windows. Эти элементы управления заключают интерфейс и функциональные возможности небольшого набора полезных элементов управления UWP. Их можно добавить непосредственно в область конструктора проекта WPF или Windows Forms, а затем использовать их как любой другой элемент управления WPF или Windows Forms в конструкторе.

* Здесь также показано, как разместить элемент управления UWP [календарвиев](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.CalendarView) с помощью элемента управления [виндовсксамлхост](https://docs.microsoft.com/windows/communitytoolkit/controls/wpf-winforms/windowsxamlhost) в наборе средств сообщества Windows. Поскольку в качестве упакованных элементов управления доступны только небольшие наборы элементов управления UWP, можно использовать [виндовсксамлхост](https://docs.microsoft.com/windows/communitytoolkit/controls/wpf-winforms/windowsxamlhost) для размещения любого другого стандартного элемента управления UWP.

Хотя в этой статье показано, как разместить элементы управления UWP в приложении WPF, процесс аналогичен для Windows Forms приложения.

## <a name="required-components"></a>Необходимые компоненты

Для размещения элемента управления UWP в приложении WPF (или Windows Forms) в решении потребуются следующие компоненты. В этой статье приводятся инструкции по созданию каждого из этих компонентов.

* **Проект и исходный код приложения**. Использование элемента управления [виндовсксамлхост](https://docs.microsoft.com/windows/communitytoolkit/controls/wpf-winforms/windowsxamlhost) для размещения стандартных первичных элементов управления UWP поддерживается в приложениях, предназначенных для .NET Framework или .NET Core 3.

* **Проект приложения UWP, определяющий класс корневого приложения, производного от ксамлаппликатион**. Проект WPF или Windows Forms должен иметь доступ к экземпляру класса [Microsoft. Toolkit. Win32. UI. ксамлхост. ксамлаппликатион](https://github.com/windows-toolkit/Microsoft.Toolkit.Win32/tree/master/Microsoft.Toolkit.Win32.UI.XamlApplication) , предоставляемого набором средств сообщества Windows. Этот объект выступает в качестве корневого поставщика метаданных для загрузки метаданных пользовательских типов универсального кода пользователя UWP в сборки в текущем каталоге приложения.

    Для этого рекомендуется добавить **пустой проект приложения (универсальное приложение Windows)** в то же решение, что и проект WPF или Windows Forms, изменить класс `App` по умолчанию в этом проекте на производный от `XamlApplication`, а затем создать экземпляр этого объекта в коде точки входа для вашего приложения.

    > [!NOTE]
    > Хотя этот компонент не требуется для упрощенных сценариев остров XAML, таких как размещение основного элемента управления UWP, приложению требуется этот `XamlApplication` объект для поддержки всего спектра сценариев остров XAML, включая размещение пользовательских элементов управления UWP. Поэтому рекомендуется всегда определять объект `XamlApplication` в любом решении, в котором используются острова XAML.

    > [!NOTE]
    > Решение может содержать только один проект, определяющий объект `XamlApplication`. Все настраиваемые элементы управления UWP в приложении совместно используют один и тот же объект `XamlApplication`. Проект, определяющий объект `XamlApplication`, должен включать ссылки на все другие библиотеки и проекты UWP, которые используются для размещения элементов управления UWP в острове XAML.

## <a name="create-a-wpf-project"></a>Создание проекта WPF

Прежде чем приступить к работе, выполните эти инструкции, чтобы создать проект WPF и настроить его для размещения островов XAML. При наличии существующего проекта WPF можно адаптировать эти шаги и примеры кода для проекта.

1. В Visual Studio 2019 создайте новый проект **приложения WPF (.NET Framework)** или **WPF App (.NET Core)** . Если вы хотите создать проект **приложения WPF (.NET Core)** , сначала необходимо установить последнюю версию [пакета SDK для .NET Core 3](https://dotnet.microsoft.com/download/dotnet-core/3.0).

2. Убедитесь, что [ссылки на пакет](https://docs.microsoft.com/nuget/consume-packages/package-references-in-project-files) включены:

    1. В Visual Studio щелкните **Сервис-> диспетчер пакетов NuGet — параметры диспетчера пакетов >** .
    2. Убедитесь, что **PackageReference** выбран для **формата управления пакетами по умолчанию**.

3. Щелкните правой кнопкой мыши проект WPF в **Обозреватель решений** и выберите **Управление пакетами NuGet**.

4. В окне **Диспетчер пакетов NuGet** установите флажок **включить предварительные выпуски** .

5. Перейдите на вкладку **Обзор** , найдите пакет [Microsoft. Toolkit. WPF. UI. Controls](https://www.nuget.org/packages/Microsoft.Toolkit.Wpf.UI.Controls) (Version v 6.0.0 или более поздней версии) и установите пакет. Этот пакет предоставляет все необходимое для использования упакованных элементов управления UWP для WPF (включая [InkCanvas](https://docs.microsoft.com/windows/communitytoolkit/controls/wpf-winforms/inkcanvas) и [инктулбар](https://docs.microsoft.com/windows/communitytoolkit/controls/wpf-winforms/inktoolbar) и элемент управления [виндовсксамлхост](https://docs.microsoft.com/windows/communitytoolkit/controls/wpf-winforms/windowsxamlhost) ).
    > [!NOTE]
    > Windows Forms приложения должны использовать пакет [Microsoft. Toolkit. Forms. UI. Controls](https://www.nuget.org/packages/Microsoft.Toolkit.Forms.UI.Controls) (Version v 6.0.0 или более поздней версии).

6. Настройте решение для конкретной платформы, например x86 или x64. Большинство сценариев островов XAML не поддерживаются в проектах, предназначенных для **любого ЦП**.

    1. В **Обозреватель решений**щелкните правой кнопкой мыши узел решения и выберите **свойства** -> **Свойства конфигурации** -> **Configuration Manager**. 
    2. В разделе **Активная платформа решения**выберите **создать**. 
    3. В диалоговом окне **Новая платформа решения** выберите **x64** или **x86** и нажмите кнопку **ОК**. 
    4. Закройте открытые диалоговые окна.

## <a name="define-a-xamlapplication-class-in-a-uwp-app-project"></a>Определение класса Ксамлаппликатион в проекте приложения UWP

Затем добавьте проект приложения UWP в то же решение, что и проект WPF. Вы измените класс `App` по умолчанию в этом проекте, чтобы он был производным от класса [Microsoft. Toolkit. Win32. UI. ксамлхост. ксамлаппликатион](https://github.com/windows-toolkit/Microsoft.Toolkit.Win32/tree/master/Microsoft.Toolkit.Win32.UI.XamlApplication) , предоставляемого набором средств сообщества Windows. Дополнительные сведения о назначении этого класса см. в [этом разделе](#required-components).

1. В **Обозреватель решений**щелкните правой кнопкой мыши узел решения и выберите **Добавить** -> **Новый проект**.
2. Добавьте проект **Пустое приложение (универсальная платформа Windows)** в свое решение. Убедитесь, что для целевой версии и минимальной версии задано значение **Windows 10, версия 1903** или более поздняя.
3. В проекте приложения UWP установите пакет NuGet [Microsoft. Toolkit. Win32. UI. ксамлаппликатион](https://www.nuget.org/packages/Microsoft.Toolkit.Win32.UI.XamlApplication) (Version v 6.0.0 или более поздней версии).
4. Откройте файл **app. XAML** и замените содержимое этого файла следующим кодом XAML. Замените `MyUWPApp` пространством имен проекта приложения UWP.

    ```xml
    <xaml:XamlApplication
        x:Class="MyUWPApp.App"
        xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
        xmlns:xaml="using:Microsoft.Toolkit.Win32.UI.XamlHost"
        xmlns:local="using:MyUWPApp">
    </xaml:XamlApplication>
    ```

5. Откройте файл **app.XAML.CS** и замените содержимое этого файла следующим кодом. Замените `MyUWPApp` пространством имен проекта приложения UWP.

    ```csharp
    namespace MyUWPApp
    {
        public sealed partial class App : Microsoft.Toolkit.Win32.UI.XamlHost.XamlApplication
        {
            public App()
            {
                this.Initialize();
            }
        }
    }
    ```

6. Удалите файл **MainPage. XAML** из проекта приложения UWP.
7. Создайте проект приложения UWP.
8. В проекте WPF щелкните правой кнопкой мыши узел **зависимости** и добавьте ссылку на проект приложения UWP.

## <a name="instantiate-the-xamlapplication-object-in-the-entry-point-of-your-wpf-app"></a>Создание экземпляра объекта Ксамлаппликатион в точке входа приложения WPF

Затем добавьте код в точку входа для приложения WPF, чтобы создать экземпляр класса `App`, который вы только что определили в проекте UWP (это класс, который теперь является производным от `XamlApplication`). Дополнительные сведения о назначении этого объекта см. в [этом разделе](#required-components).

1. В проекте WPF щелкните правой кнопкой мыши узел проекта, выберите **добавить** -> **новый элемент**, а затем выберите **класс**. Назовите **программу** класса и нажмите кнопку **Добавить**.

2. Замените созданный класс `Program` следующим кодом, а затем сохраните файл. Замените `MyUWPApp` пространством имен проекта приложения UWP и замените `MyWPFApp` пространством имен проекта приложения WPF.

    ```csharp
    public class Program
    {
        [System.STAThreadAttribute()]
        public static void Main()
        {
            using (new MyUWPApp.App())
            {
                MyWPFApp.App app = new MyWPFApp.App();
                app.InitializeComponent();
                app.Run();
            }
        }
    }
    ```

3. Щелкните правой кнопкой мыши узел проекта и выберите пункт **Свойства**.

4. На вкладке **приложение** свойств щелкните раскрывающийся список **начальный объект** и выберите полное имя класса `Program`, добавленного на предыдущем шаге. 
    > [!NOTE]
    > По умолчанию проекты WPF определяют `Main` функцию точки входа в созданном файле кода, который не предназначен для изменения. Этот шаг изменяет точку входа для проекта на метод `Main` нового класса `Program`, который позволяет добавить код, который выполняется как можно раньше в процессе запуска приложения. 

5. Сохраните изменения в свойствах проекта.

## <a name="host-an-inkcanvas-and-inktoolbar-by-using-wrapped-controls"></a>Размещение InkCanvas и Инктулбар с помощью упакованных элементов управления

Теперь, когда вы настроили проект для использования островов XAML UWP, теперь все готово к добавлению в приложение элементов управления UWP с оболочкой класса [InkCanvas](https://docs.microsoft.com/windows/communitytoolkit/controls/wpf-winforms/inkcanvas) и [инктулбар](https://docs.microsoft.com/windows/communitytoolkit/controls/wpf-winforms/inktoolbar) .

1. В **Обозреватель решений**откройте файл **MainWindow. XAML** .

2. В элементе **Window** в верхней части файла XAML добавьте следующий атрибут. Он ссылается на пространство имен XAML для элемента управления UWP с оболочкой [InkCanvas](https://docs.microsoft.com/windows/communitytoolkit/controls/wpf-winforms/inkcanvas) и [инктулбар](https://docs.microsoft.com/windows/communitytoolkit/controls/wpf-winforms/inktoolbar) .

    ```xml
    xmlns:Controls="clr-namespace:Microsoft.Toolkit.Wpf.UI.Controls;assembly=Microsoft.Toolkit.Wpf.UI.Controls"
    ```

    После добавления этого атрибута элемент **Window** должен выглядеть примерно так, как показано далее.

    ```xml
    <Window xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
            xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
            xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
            xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
            xmlns:local="clr-namespace:WpfApp"
            xmlns:Controls="clr-namespace:Microsoft.Toolkit.Wpf.UI.Controls;assembly=Microsoft.Toolkit.Wpf.UI.Controls"
            x:Class="WpfApp.MainWindow"
            mc:Ignorable="d"
            Title="MainWindow" Height="800" Width="800">
    ```

3. В файле **MainWindow. XAML** замените существующий элемент `<Grid>` следующим кодом XAML. Этот XAML добавляет элемент управления [InkCanvas](https://docs.microsoft.com/windows/communitytoolkit/controls/wpf-winforms/inkcanvas) и [инктулбар](https://docs.microsoft.com/windows/communitytoolkit/controls/wpf-winforms/inktoolbar) (с префиксом, определенным **ранее в качестве** пространства имен) в `<Grid>`.

    ```xml
    <Grid Margin="10,50,10,10">
        <Grid.RowDefinitions>
            <RowDefinition Height="Auto"/>
            <RowDefinition Height="Auto"/>
        </Grid.RowDefinitions>
        <Controls:InkToolbar x:Name="myInkToolbar" TargetInkCanvas="{x:Reference myInkCanvas}" Grid.Row="0" Width="300"
            Height="50" Margin="10,10,10,10" HorizontalAlignment="Left" VerticalAlignment="Top" />
        <Controls:InkCanvas x:Name="myInkCanvas" Grid.Row="1" HorizontalAlignment="Left" Width="600" Height="400"
            Margin="10,10,10,10" VerticalAlignment="Top" />
    </Grid>
    ```

    > [!NOTE]
    > Кроме того, можно добавить эти и другие элементы управления с оболочкой в окно, перетащив их из раздела инструментарий **сообщества Windows** на **панель элементов** в конструктор.

4. Сохраните файл **MainWindow. XAML** .

    Если у вас есть устройство, которое поддерживает цифровое перо, например поверхность, и вы используете эту лабораторную работу на физическом компьютере, теперь можно создать и запустить приложение и нарисовать цифровой рукописный ввод на экране с помощью пера. Однако если у вас нет устройства, поддерживающего перо, и вы пытаетесь подписаться с помощью мыши, ничего не произойдет. Это происходит потому, что элемент управления **InkCanvas** включен по умолчанию только для цифровых перьев. Однако это поведение можно изменить.

5. Откройте файл **MainWindow.XAML.CS** .

6. Добавьте следующее объявление пространства имен в начало файла:

    ```csharp
    using Microsoft.Toolkit.Win32.UI.Controls.Interop.WinRT;
    ```

7. Нахождение конструктора `MainWindow()`. Добавьте следующую строку кода после метода `InitializeComponent()` и сохраните файл кода.

    ```csharp
    this.myInkCanvas.InkPresenter.InputDeviceTypes = CoreInputDeviceTypes.Mouse | CoreInputDeviceTypes.Pen;
    ```

    Объект **InkPresenter** можно использовать для настройки интерфейса рукописного ввода по умолчанию. Этот код использует свойство **инпутдевицетипес** для включения мыши, а также для ввода с помощью пера.

8. Снова нажмите клавишу F5, чтобы перестроить и запустить приложение в отладчике. Если вы используете компьютер с мышью, убедитесь, что на холсте рукописного ввода можно нарисовать какой-либо элемент с помощью мыши.

## <a name="host-a-calendarview-by-using-the-host-control"></a>Размещение Календарвиев с помощью элемента управления ведущего приложения

Теперь, когда вы добавили в приложение элементы управления UWP ( [InkCanvas](https://docs.microsoft.com/windows/communitytoolkit/controls/wpf-winforms/inkcanvas) и [инктулбар](https://docs.microsoft.com/windows/communitytoolkit/controls/wpf-winforms/inktoolbar) ), вы можете использовать элемент управления [виндовсксамлхост](https://docs.microsoft.com/windows/communitytoolkit/controls/wpf-winforms/windowsxamlhost) , чтобы добавить [календарвиев](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.CalendarView) в приложение.

> [!NOTE]
> Элемент управления [виндовсксамлхост](https://docs.microsoft.com/windows/communitytoolkit/controls/wpf-winforms/windowsxamlhost) предоставляется пакетом [Microsoft. Toolkit. WPF. UI. ксамлхост](https://www.nuget.org/packages/Microsoft.Toolkit.Wpf.UI.XamlHost) . Этот пакет включен в пакет [Microsoft. Toolkit. WPF. UI. Controls](https://www.nuget.org/packages/Microsoft.Toolkit.Wpf.UI.Controls) , который вы установили ранее.

1. В **Обозреватель решений**откройте файл **MainWindow. XAML** .

2. В элементе **Window** в верхней части файла XAML добавьте следующий атрибут. Он ссылается на пространство имен XAML для элемента управления [виндовсксамлхост](https://docs.microsoft.com/windows/communitytoolkit/controls/wpf-winforms/windowsxamlhost) .

    ```xml
    xmlns:xamlhost="clr-namespace:Microsoft.Toolkit.Wpf.UI.XamlHost;assembly=Microsoft.Toolkit.Wpf.UI.XamlHost"
    ```

    После добавления этого атрибута элемент **Window** должен выглядеть примерно так, как показано далее.

    ```xml
    <Window xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
            xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
            xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
            xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
            xmlns:local="clr-namespace:WpfApp"
            xmlns:Controls="clr-namespace:Microsoft.Toolkit.Wpf.UI.Controls;assembly=Microsoft.Toolkit.Wpf.UI.Controls"
            xmlns:xamlhost="clr-namespace:Microsoft.Toolkit.Wpf.UI.XamlHost;assembly=Microsoft.Toolkit.Wpf.UI.XamlHost"
            x:Class="WpfApp.MainWindow"
            mc:Ignorable="d"
            Title="MainWindow" Height="800" Width="800">
    ```

4. В файле **MainWindow. XAML** замените существующий элемент `<Grid>` следующим кодом XAML. Этот XAML добавляет строку в сетку и добавляет объект [виндовсксамлхост](https://docs.microsoft.com/windows/communitytoolkit/controls/wpf-winforms/windowsxamlhost) в последнюю строку. Для размещения элемента управления [КАЛЕНДАРВИЕВ](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.CalendarView) UWP этот XAML задает свойству `InitialTypeName` полное имя элемента управления. Этот XAML также определяет обработчик событий для события `ChildChanged`, которое вызывается при подготовке к просмотру размещенного элемента управления.

    ```xml
    <Grid Margin="10,50,10,10">
        <Grid.RowDefinitions>
            <RowDefinition Height="Auto"/>
            <RowDefinition Height="Auto"/>
            <RowDefinition Height="Auto"/>
        </Grid.RowDefinitions>
        <Controls:InkToolbar x:Name="myInkToolbar" TargetInkCanvas="{x:Reference myInkCanvas}" Grid.Row="0" Width="300"
            Height="50" Margin="10,10,10,10" HorizontalAlignment="Left" VerticalAlignment="Top" />
        <Controls:InkCanvas x:Name="myInkCanvas" Grid.Row="1" HorizontalAlignment="Left" Width="600" Height="400" 
            Margin="10,10,10,10" VerticalAlignment="Top" />
        <xamlhost:WindowsXamlHost x:Name="myCalendar" InitialTypeName="Windows.UI.Xaml.Controls.CalendarView" Grid.Row="2" 
              Margin="10,10,10,10" Width="600" Height="300" ChildChanged="MyCalendar_ChildChanged"  />
    </Grid>
    ```

5. Сохраните файл **MainWindow. XAML** и откройте файл **MainWindow.XAML.CS** .

7. Добавьте следующее объявление пространства имен в начало файла:

    ```csharp
    using Microsoft.Toolkit.Wpf.UI.XamlHost;
    ```

10. Добавьте следующий метод обработчика событий `ChildChanged` в класс `MainWindow` и сохраните файл кода. После подготовки к просмотру элемента управления ведущего приложения этот обработчик событий запускается и создает простой обработчик событий для `SelectedDatesChanged` события элемента управления Calendar.

    ```csharp
    private void MyCalendar_ChildChanged(object sender, EventArgs e)
    {
        WindowsXamlHost windowsXamlHost = (WindowsXamlHost)sender;

        Windows.UI.Xaml.Controls.CalendarView calendarView =
            (Windows.UI.Xaml.Controls.CalendarView)windowsXamlHost.Child;

        if (calendarView != null)
        {
            calendarView.SelectedDatesChanged += (obj, args) =>
            {
                if (args.AddedDates.Count > 0)
                {
                    MessageBox.Show("The user selected a new date: " + 
                        args.AddedDates[0].DateTime.ToString());
                }
            };
        }
    }
    ```

11. Снова нажмите клавишу F5, чтобы перестроить и запустить приложение в отладчике. Убедитесь, что элемент управления Calendar теперь отображается в нижней части окна.

## <a name="package-the-app"></a>Упаковка приложения

При необходимости можно упаковать приложение WPF в [пакет MSIX](https://docs.microsoft.com/windows/msix) для развертывания. MSIX — это современная технология упаковки приложений для Windows, которая основана на сочетании технологий установки MSI, appx, App-V и ClickOnce.

В следующих инструкциях показано, как упаковать все компоненты в решении в пакете MSIX с помощью [проекта упаковки приложений Windows](https://docs.microsoft.com/windows/msix/desktop/desktop-to-uwp-packaging-dot-net) в Visual Studio 2019. Эти действия необходимы только в том случае, если требуется упаковать приложение WPF в пакет MSIX.

1. Добавьте новый [проект упаковки приложений Windows](https://docs.microsoft.com/windows/msix/desktop/desktop-to-uwp-packaging-dot-net) в решение. При создании проекта выберите **Windows 10, версия 1903 (10,0; Сборка 18362)** для **целевой версии** и **минимальной версии**.

2. В проекте упаковки щелкните правой кнопкой мыши узел **приложения** и выберите команду **Добавить ссылку**. В списке проектов выберите проект WPF в решении и нажмите кнопку **ОК**.

3. Настройте решение для конкретной платформы, например x86 или x64. Это необходимо для сборки приложения WPF в пакет MSIX с помощью проекта упаковки приложений Windows.

    1. В **Обозреватель решений**щелкните правой кнопкой мыши узел решения и выберите **свойства** -> **Свойства конфигурации** -> **Configuration Manager**.
    2. В разделе **Активная платформа решения**выберите **x64** или **x86**.
    3. В строке проекта WPF в столбце **платформа** выберите **создать**.
    4. В диалоговом окне **Новая платформа решения** выберите **x64** или **x86** (та же платформа, которая была выбрана для **активной платформы решения**) и нажмите кнопку **ОК**.
    5. Закройте открытые диалоговые окна.

5. Создайте и запустите проект упаковки. Убедитесь, что WPF выполняется и пользовательский элемент управления UWP отображается ожидаемым образом.

## <a name="related-topics"></a>Связанные разделы

* [Размещение элементов управления XAML UWP в классических приложениях (острова XAML)](xaml-islands.md)
* [Примеры кода островов XAML](https://github.com/microsoft/Xaml-Islands-Samples)
* [InkCanvas](https://docs.microsoft.com/windows/communitytoolkit/controls/wpf-winforms/inkcanvas)
* [InkToolbar](https://docs.microsoft.com/windows/communitytoolkit/controls/wpf-winforms/inktoolbar)
* [WindowsXamlHost](https://docs.microsoft.com/windows/communitytoolkit/controls/wpf-winforms/windowsxamlhost)
