---
description: В этой статье показано, как разместить стандартный элемент управления UWP в приложении WPF с помощью XAML-островов.
title: Размещение стандартного элемента управления UWP в приложении WPF с помощью XAML-островов
ms.date: 08/20/2019
ms.topic: article
keywords: Windows 10, UWP, Windows Forms, WPF, острова XAML, обтекаемые элементы управления, стандартные элементы управления, InkCanvas, Инктулбар
ms.author: mcleans
author: mcleanbyron
ms.localizationpriority: medium
ms.custom: 19H1
ms.openlocfilehash: cdaaa20b28a7f181467f6047bc93350ec40b366a
ms.sourcegitcommit: f34deba1d4460d85ed08fe9648999fe03ff6a3dd
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/26/2019
ms.locfileid: "71317073"
---
# <a name="host-a-standard-uwp-control-in-a-wpf-app-using-xaml-islands"></a>Размещение стандартного элемента управления UWP в приложении WPF с помощью XAML-островов

В этой статье показано два способа размещения стандартного элемента управления UWP (то есть первого элемента управления UWP, предоставляемого Windows SDK или библиотеки Винуи) в приложении WPF с помощью [XAML-островов](xaml-islands.md):

* Здесь показано, как разместить элементы управления UWP [InkCanvas](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.InkCanvas) и [инктулбар](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.inktoolbar) с помощью [упакованных элементов управления](xaml-islands.md#wrapped-controls) в наборе средств сообщества Windows. Эти элементы управления заключают интерфейс и функциональные возможности небольшого набора полезных элементов управления UWP. Их можно добавить непосредственно в область конструктора проекта WPF или Windows Forms, а затем использовать их как любой другой элемент управления WPF или Windows Forms в конструкторе.

* Здесь также показано, как разместить элемент управления UWP [календарвиев](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.CalendarView) с помощью элемента управления [виндовсксамлхост](https://docs.microsoft.com/windows/communitytoolkit/controls/wpf-winforms/windowsxamlhost) в наборе средств сообщества Windows. Поскольку в качестве упакованных элементов управления доступны только небольшие наборы элементов управления UWP, можно использовать [виндовсксамлхост](https://docs.microsoft.com/windows/communitytoolkit/controls/wpf-winforms/windowsxamlhost) для размещения любого другого стандартного элемента управления UWP.

Для размещения элемента управления UWP в приложении WPF потребуются следующие компоненты. В этой статье приводятся инструкции по созданию каждого из этих компонентов.

* Проект и исходный код для приложения WPF.
* Проект приложения UWP, определяющий экземпляр `Microsoft.Toolkit.Win32.UI.XamlHost.XamlApplication` класса из набора средств сообщества Windows Community.
  > [!NOTE]
  > Чтобы обеспечить хорошую работу приложения во всех сценариях использования XAML, проект WPF (или Windows Forms) должен иметь доступ к `XamlApplication` объекту. Этот объект выступает в качестве корневого поставщика метаданных для загрузки метаданных для типов XAML UWP в сборках в текущем каталоге приложения. Для этого рекомендуется добавить **пустой проект приложения (универсальное приложение Windows)** в то же решение, что и проект WPF (или Windows Forms), и изменить класс по умолчанию `App` в этом проекте на производный от. `XamlApplication`
  >
  > Хотя этот шаг не требуется для упрощенных сценариев остров XAML, таких как размещение основного элемента управления UWP, приложению WPF требуется этот `XamlApplication` объект для поддержки всего спектра сценариев остров XAML, включая размещение пользовательских элементов управления UWP. Рекомендуется всегда добавлять проект UWP и определять `XamlApplication` объект в любом решении, в котором используются острова XAML. Решение может содержать только один проект, определяющий `XamlApplication` объект. Все пользовательские элементы управления UWP в приложении совместно используют один `XamlApplication` и тот же объект.

Хотя в этой статье показано, как разместить элементы управления UWP в приложении WPF, процесс аналогичен для Windows Forms приложения.

## <a name="create-a-wpf-project"></a>Создание проекта WPF

Прежде чем приступить к работе, выполните эти инструкции, чтобы создать проект WPF и настроить его для размещения островов XAML. При наличии существующего проекта WPF можно адаптировать эти шаги и примеры кода для проекта.

1. В Visual Studio 2019 создайте новый проект **приложения WPF (.NET Framework)** или **WPF App (.NET Core)** . Если вы хотите создать проект **приложения WPF (.NET Core)** , сначала необходимо установить последнюю версию [пакета SDK для .NET Core 3](https://dotnet.microsoft.com/download/dotnet-core/3.0).

2. Убедитесь, что [ссылки на пакет](https://docs.microsoft.com/nuget/consume-packages/package-references-in-project-files) включены:

    1. В Visual Studio щелкните **Сервис-> диспетчер пакетов NuGet — параметры диспетчера пакетов >** .
    2. Убедитесь, что **PackageReference** выбран для **формата управления пакетами по умолчанию**.

3. Щелкните правой кнопкой мыши проект WPF в **Обозреватель решений** и выберите **Управление пакетами NuGet**.

4. В окне **Диспетчер пакетов NuGet** установите флажок **включить предварительные выпуски** .

5. Перейдите на вкладку **Обзор** , найдите пакет [Microsoft. Toolkit. WPF. UI. Controls](https://www.nuget.org/packages/Microsoft.Toolkit.Wpf.UI.Controls) (Version v 6.0.0-preview7 или более поздней версии) и установите пакет. Этот пакет предоставляет все необходимое для использования упакованных элементов управления UWP для WPF (включая [InkCanvas](https://docs.microsoft.com/windows/communitytoolkit/controls/wpf-winforms/inkcanvas) и [инктулбар](https://docs.microsoft.com/windows/communitytoolkit/controls/wpf-winforms/inktoolbar) и элемент управления [виндовсксамлхост](https://docs.microsoft.com/windows/communitytoolkit/controls/wpf-winforms/windowsxamlhost) ).
    > [!NOTE]
    > Windows Forms приложения должны использовать пакет [Microsoft. Toolkit. Forms. UI. Controls](https://www.nuget.org/packages/Microsoft.Toolkit.Forms.UI.Controls) (Version v 6.0.0-preview7 или более поздней версии).

6. Настройте решение для конкретной платформы, например x86 или x64. Большинство сценариев островов XAML не поддерживаются в проектах, предназначенных для **любого ЦП**.

    1. В **Обозреватель решений**щелкните правой кнопкой мыши узел решения и выберите **свойства** -> **конфигурации** -> свойства**Configuration Manager**. 
    2. В разделе **Активная платформа решения**выберите **создать**. 
    3. В диалоговом окне **Новая платформа решения** выберите **x64** или **x86** и нажмите кнопку **ОК**. 
    4. Закройте открытые диалоговые окна.

## <a name="create-a-xamlapplication-object-in-a-uwp-app-project"></a>Создание объекта Ксамлаппликатион в проекте приложения UWP

Затем добавьте проект приложения UWP в то же решение, что и проект WPF. Вы измените класс по умолчанию `App` в этом проекте, чтобы он был производным `Microsoft.Toolkit.Win32.UI.XamlHost.XamlApplication` от класса, предоставляемого набором средств сообщества Windows. Хотя этот шаг не требуется для тривиальных сценариев остров XAML, таких как размещение одного первого элемента управления UWP, приложению WPF требуется этот `XamlApplication` объект для поддержки всего спектра сценариев остров XAML. Рекомендуется всегда добавлять этот проект в любое решение, в котором используются острова XAML.

1. В **Обозреватель решений**щелкните правой кнопкой мыши узел решения и выберите команду **Добавить** -> **Новый проект**.
2. Добавьте проект **Пустое приложение (универсальная платформа Windows)** в свое решение. Убедитесь, что для целевой версии и минимальной версии задано значение **Windows 10, версия 1903** или более поздняя.
3. В проекте приложения UWP установите пакет NuGet [Microsoft. Toolkit. Win32. UI. ксамлаппликатион](https://www.nuget.org/packages/Microsoft.Toolkit.Win32.UI.XamlApplication) (Version v 6.0.0-preview7 или более поздней версии).
4. Откройте файл **app. XAML** и замените содержимое этого файла следующим кодом XAML. Замените `MyUWPApp` на пространство имен проекта приложения UWP.

    ```xml
    <xaml:XamlApplication
        x:Class="MyUWPApp.App"
        xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
        xmlns:xaml="using:Microsoft.Toolkit.Win32.UI.XamlHost"
        xmlns:local="using:MyUWPApp">
    </xaml:XamlApplication>
    ```

5. Откройте файл **app.XAML.CS** и замените содержимое этого файла следующим кодом. Замените `MyUWPApp` на пространство имен проекта приложения UWP.

    ```csharp
    namespace MyUWPApp
    {
        sealed partial class App : Microsoft.Toolkit.Win32.UI.XamlHost.XamlApplication
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

3. В файле **MainWindow. XAML** замените существующий `<Grid>` элемент следующим кодом XAML. Этот XAML добавляет элемент управления [InkCanvas](https://docs.microsoft.com/windows/communitytoolkit/controls/wpf-winforms/inkcanvas) и [инктулбар](https://docs.microsoft.com/windows/communitytoolkit/controls/wpf-winforms/inktoolbar) (с префиксом **Controls** , который был определен ранее в качестве пространства имен) `<Grid>`в.

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

7. Нахождение `MainWindow()` конструктора. Добавьте следующую строку кода после `InitializeComponent()` метода и сохраните файл кода.

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

4. В файле **MainWindow. XAML** замените существующий `<Grid>` элемент следующим кодом XAML. Этот XAML добавляет строку в сетку и добавляет объект [виндовсксамлхост](https://docs.microsoft.com/windows/communitytoolkit/controls/wpf-winforms/windowsxamlhost) в последнюю строку. Для размещения элемента управления [календарвиев](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.CalendarView) UWP этот XAML присваивает `InitialTypeName` свойству полное имя элемента управления. Этот XAML также определяет обработчик событий для `ChildChanged` события, который вызывается при подготовке к просмотру размещенного элемента управления.

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

10. Добавьте в `MainWindow` класс `ChildChanged` следующий метод обработчика событий и сохраните файл кода. После подготовки к просмотру элемента управления ведущего приложения этот обработчик событий запускается и создает простой обработчик событий для `SelectedDatesChanged` события элемента управления Calendar.

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

При необходимости можно упаковать приложение WPF в [пакет MSIX](https://docs.microsoft.com/windows/msix) для развертывания. MSIX — это современная технология упаковки приложений для Windows, которая основана на сочетании технологий установки MSI, APPX, App-V и ClickOnce.

В следующих инструкциях показано, как упаковать все компоненты в решении в пакете MSIX с помощью [проекта упаковки приложений Windows](https://docs.microsoft.com/windows/msix/desktop/desktop-to-uwp-packaging-dot-net) в Visual Studio 2019. Эти действия необходимы только в том случае, если требуется упаковать приложение WPF в пакет MSIX.

1. Добавьте новый [проект упаковки приложений Windows](https://docs.microsoft.com/windows/msix/desktop/desktop-to-uwp-packaging-dot-net) в решение. При создании проекта выберите **Windows 10, версия 1903 (10,0; Сборка 18362)** для **целевой версии** и **минимальной версии**.

2. В проекте упаковки щелкните правой кнопкой мыши узел **приложения** и выберите команду **Добавить ссылку**. В списке проектов выберите проект WPF в решении и нажмите кнопку **ОК**.

3. Если проект WPF предназначен для .NET Core 3, необходимо изменить файл проекта упаковки. В настоящее время эти изменения необходимы для упаковки приложений WPF, предназначенных для .NET Core 3, и для размещения элементов управления UWP.

    1. В обозреватель решений щелкните правой кнопкой мыши узел проект упаковки и выберите пункт **изменить файл проекта**.
    2. Найдите в этом файле элемент `<Import Project="$(WapProjPath)\Microsoft.DesktopBridge.targets" />`. Замените этот элемент следующим XML-кодом.

        ``` xml
        <ItemGroup>
            <SDKReference Include="Microsoft.VCLibs,Version=14.0">
            <TargetedSDKConfiguration Condition="'$(Configuration)'!='Debug'">Retail</TargetedSDKConfiguration>
            <TargetedSDKConfiguration Condition="'$(Configuration)'=='Debug'">Debug</TargetedSDKConfiguration>
            <TargetedSDKArchitecture>$(PlatformShortName)</TargetedSDKArchitecture>
            <Implicit>true</Implicit>
            </SDKReference>
        </ItemGroup>
        <Import Project="$(WapProjPath)\Microsoft.DesktopBridge.targets" />
        <Target Name="_StompSourceProjectForWapProject" BeforeTargets="_ConvertItems">
            <ItemGroup>
            <_TemporaryFilteredWapProjOutput Include="@(_FilteredNonWapProjProjectOutput)" />
            <_FilteredNonWapProjProjectOutput Remove="@(_TemporaryFilteredWapProjOutput)" />
            <_FilteredNonWapProjProjectOutput Include="@(_TemporaryFilteredWapProjOutput)">
                <SourceProject></SourceProject>
                <TargetPath Condition="'%(FileName)%(Extension)'=='resources.pri'">app_resources.pri</TargetPath>
            </_FilteredNonWapProjProjectOutput>
            </ItemGroup>
        </Target>
        ```

    3. Сохраните файл проекта и закройте его.

4. Настройте решение для конкретной платформы, например x86 или x64. Это необходимо для сборки приложения WPF в пакет MSIX с помощью проекта упаковки приложений Windows.

    1. В **Обозреватель решений**щелкните правой кнопкой мыши узел решения и выберите **свойства** -> **конфигурации** -> свойства**Configuration Manager**.
    2. В разделе **Активная платформа решения**выберите **x64** или **x86**.
    3. В строке проекта WPF в столбце **платформа** выберите **создать**.
    4. В диалоговом окне **Новая платформа решения** выберите **x64** или **x86** (та же платформа, которая была выбрана для **активной платформы решения**) и нажмите кнопку **ОК**.
    5. Закройте открытые диалоговые окна.

5. Создайте и запустите проект упаковки. Убедитесь, что WPF выполняется и пользовательский элемент управления UWP отображается ожидаемым образом.

## <a name="related-topics"></a>См. также

* [Элементы управления UWP в классических приложениях](xaml-islands.md)
* [InkCanvas](https://docs.microsoft.com/windows/communitytoolkit/controls/wpf-winforms/inkcanvas)
* [InkToolbar](https://docs.microsoft.com/windows/communitytoolkit/controls/wpf-winforms/inktoolbar)
* [виндовсксамлхост](https://docs.microsoft.com/windows/communitytoolkit/controls/wpf-winforms/windowsxamlhost)
