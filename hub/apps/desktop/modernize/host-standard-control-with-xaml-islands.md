---
description: Эта статья содержит сведения о размещении стандартного элемента управления UWP в приложении WPF с помощью XAML Islands.
title: Размещение стандартного элемента управления UWP в приложении WPF с помощью XAML Islands
ms.date: 01/24/2020
ms.topic: article
keywords: Windows 10, UWP, Windows Forms, WPF, XAML Islands, заключенные в оболочку элементы управления, стандартные элементы управления, InkCanvas, InkToolbar
ms.author: mcleans
author: mcleanbyron
ms.localizationpriority: medium
ms.custom: 19H1
ms.openlocfilehash: ed6aa406cd1372819c25bd43b59cd416130b09e0
ms.sourcegitcommit: 76e8b4fb3f76cc162aab80982a441bfc18507fb4
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/29/2020
ms.locfileid: "80482509"
---
# <a name="host-a-standard-uwp-control-in-a-wpf-app-using-xaml-islands"></a>Размещение стандартного элемента управления UWP в приложении WPF с помощью XAML Islands

В этой статье описывается два способа размещения стандартного элемента управления UWP (т. е. собственного элемента управления UWP, предоставляемого пакетом Windows SDK) в приложении WPF с использованием [XAML Islands](xaml-islands.md):

* В ней описывается, как разместить элементы управления UWP [InkCanvas](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.InkCanvas) и [InkToolbar](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.inktoolbar), используя [заключенные в оболочку элементы управления](xaml-islands.md#wrapped-controls) в наборе средств сообщества Windows. Эти элементы управления заключают интерфейс и функциональные возможности небольшого набора полезных элементов управления UWP в оболочку. Вы можете добавить их напрямую в область разработки вашего проекта WPF или Windows Forms, а затем использовать их как и любой другой элемент управления WPF или Windows Forms в конструкторе.

* Здесь также показано, как разместить элемент управления UWP [CalendarView](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.CalendarView) с помощью элемента управления [WindowsXamlHost](https://docs.microsoft.com/windows/communitytoolkit/controls/wpf-winforms/windowsxamlhost) из набора средств сообщества Windows. Так как в качестве заключенных в оболочку элементов управления доступен только небольшой набор элементов управления UWP, вы можете использовать [WindowsXamlHost](https://docs.microsoft.com/windows/communitytoolkit/controls/wpf-winforms/windowsxamlhost) для размещения любого другого стандартного элемента управления UWP.

Хотя в этой статье показано, как разместить элементы управления UWP в приложении WPF, для приложения Windows Forms процесс является аналогичным.

## <a name="required-components"></a>Необходимые компоненты

Чтобы разместить элемент управления UWP в приложении WPF (или Windows Forms), вам потребуются указанные ниже компоненты в решении. Эта статья содержит инструкции по созданию каждого из этих компонентов.

* **Код проекта и исходный код приложения**. Использование элемента управления [WindowsXamlHost](https://docs.microsoft.com/windows/communitytoolkit/controls/wpf-winforms/windowsxamlhost) для размещения стандартных собственных элементов управления UWP поддерживается в приложениях, предназначенных для .NET Framework или .NET Core 3.

* **Проект приложения UWP, определяющий корневой класс Application, производный от XamlApplication**. В проекте WPF или Windows Forms должен быть доступ к экземпляру класса [Microsoft.Toolkit.Win32.UI.XamlHost.XamlApplication](https://github.com/windows-toolkit/Microsoft.Toolkit.Win32/tree/master/Microsoft.Toolkit.Win32.UI.XamlApplication), предоставленному в наборе средств сообщества Windows, чтобы он мог обнаруживать и загружать настраиваемые элементы управления UWP XAML. Для этого рекомендуется определить объект в отдельном проекте приложения UWP, который является частью решения приложения WPF или Windows Forms. 

    > [!NOTE]
    > Хотя для размещения собственного элемента управления UWP объект `XamlApplication` не требуется, приложению необходим этот объект для поддержки всех сценариев XAML Islands, в том числе для размещения настаиваемых элементов управления UWP. Таким образом, мы рекомендуем всегда определять объект `XamlApplication` в любом решении, в котором вы используете XAML Islands.

    > [!NOTE]
    > Решение может содержать только один проект, определяющий объект `XamlApplication`. Все настраиваемые элементы управления UWP в приложении используют один и тот же объект `XamlApplication`. Проект, определяющий объект `XamlApplication`, должен содержать ссылки на все другие библиотеки и проекты UWP, которые используются для размещения элементов управления UWP в XAML Island.

## <a name="create-a-wpf-project"></a>Создание проекта WPF

Перед началом работы выполните следующие инструкции, чтобы создать проект WPF и настроить его для размещения XAML Islands. Если у вас есть проект WPF, вы можете адаптировать под него эти шаги и примеры кода.

1. В Visual Studio 2019 создайте проект **приложения WPF (.NET Framework)** или **приложения WPF (.NET Core)** . Если вы хотите создать проект **приложения WPF (.NET Core)** , сначала необходимо установить последнюю версию [пакета SDK для .NET Core 3](https://dotnet.microsoft.com/download/dotnet-core/3.0).

2. Убедитесь, что [ссылки на пакеты](https://docs.microsoft.com/nuget/consume-packages/package-references-in-project-files) активны:

    1. В Visual Studio выберите элементы **Сервис -> Диспетчер пакетов NuGet -> Параметры диспетчера пакетов**.
    2. Убедитесь, что для **формата управления пакетами по умолчанию** установлено значение **PackageReference**.

3. В **обозревателе решений** щелкните правой кнопкой мыши проект WPF и выберите пункт **Manage NuGet Packages** (Управление пакетами NuGet).

4. Убедитесь, что в окне **Диспетчер пакетов NuGet** выбран параметр **Включить предварительные выпуски**.

5. Откройте вкладку **Обзор**, найдите пакет [Microsoft.Toolkit.Wpf.UI.Controls](https://www.nuget.org/packages/Microsoft.Toolkit.Wpf.UI.Controls) (версии 6.0.0 или более поздней) и установите его. Этот пакет содержит все необходимые компоненты для использования заключенных в оболочку элементов управления UWP для WPF (в том числе элементы управления [InkCanvas](https://docs.microsoft.com/windows/communitytoolkit/controls/wpf-winforms/inkcanvas), [InkToolbar](https://docs.microsoft.com/windows/communitytoolkit/controls/wpf-winforms/inktoolbar) и [WindowsXamlHost](https://docs.microsoft.com/windows/communitytoolkit/controls/wpf-winforms/windowsxamlhost).
    > [!NOTE]
    > В приложениях Windows Forms необходимо использовать пакет [Microsoft.Toolkit.Forms.UI.Controls](https://www.nuget.org/packages/Microsoft.Toolkit.Forms.UI.Controls) (версии 6.0.0 или более поздней).

6. Настройте решение для конкретной платформы, например x86 или x64. Большинство сценариев XAML Islands не поддерживаются в проектах, предназначенных для **любого ЦП**.

    1. В **обозревателе решений** щелкните правой кнопкой мыши узел решения и выберите элементы **Свойства** -> **Свойства конфигурации** -> **Диспетчер конфигураций**. 
    2. В списке **Active solution platform** (Активная платформа решения) выберите пункт **Создать**. 
    3. В диалоговом окне **Создание платформы решения** выберите **x64** или **x86** и нажмите кнопку **ОК**. 
    4. Закройте открытые диалоговые окна.

## <a name="define-a-xamlapplication-class-in-a-uwp-app-project"></a>Определение класса XamlApplication в проекте приложения UWP

Далее добавьте проект приложения UWP в решение и измените стандартный класс `App` в этом проекте, чтобы он был производным от класса [Microsoft.Toolkit.Win32.UI.XamlHost.XamlApplication](https://github.com/windows-toolkit/Microsoft.Toolkit.Win32/tree/master/Microsoft.Toolkit.Win32.UI.XamlApplication), предоставленного в наборе средств сообщества Windows. Этот класс поддерживает интерфейс [IXamlMetadaraProvider](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Markup.IXamlMetadataProvider), который позволяет приложению обнаруживать и загружать метаданные для настраиваемых элементов управления UWP XAML в сборках в текущем каталоге приложения во время выполнения. Этот класс также инициализирует платформу UWP XAML для текущего потока.

> [!NOTE]
> Хотя для размещения собственного элемента управления UWP этот шаг не требуется, приложению требуется объект `XamlApplication` для поддержки всех сценариев XAML Islands, в том числе для размещения настаиваемых элементов управления UWP. Таким образом, мы рекомендуем всегда определять объект `XamlApplication` в любом решении, в котором вы используете XAML Islands.

1. В **обозревателе решений** щелкните правой кнопкой мыши узел решения и выберите команду **Добавить** -> **Новый проект**.
2. Добавьте проект **Пустое приложение (универсальное приложение Windows)** в свое решение. Убедитесь, что в качестве целевой и самой ранней версии используется **Windows 10 версии 1903** или более поздней.
3. В проекте приложения UWP установите пакет NuGet [Microsoft.Toolkit.Win32.UI.XamlApplication](https://www.nuget.org/packages/Microsoft.Toolkit.Win32.UI.XamlApplication) (версии 6.0.0 или более поздней).
4. Откройте файл **App.xaml** и замените его содержимое приведенным ниже кодом XAML. Замените `MyUWPApp` пространством имен проекта приложения UWP.

    ```xml
    <xaml:XamlApplication
        x:Class="MyUWPApp.App"
        xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
        xmlns:xaml="using:Microsoft.Toolkit.Win32.UI.XamlHost"
        xmlns:local="using:MyUWPApp">
    </xaml:XamlApplication>
    ```

5. Откройте файл **App.xaml.cs** и замените его содержимое приведенным ниже кодом. Замените `MyUWPApp` пространством имен проекта приложения UWP.

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

6. Удалите файл **MainPage.xaml** из проекта приложения UWP.
7. Создайте проект приложения UWP.
8. В проекте WPF щелкните правой кнопкой мыши узел **Зависимости** и добавьте ссылку на проект приложения UWP.

## <a name="instantiate-the-xamlapplication-object-in-the-entry-point-of-your-wpf-app"></a>Создание экземпляра объекта XamlApplication в точке входа приложения WPF

Далее добавьте код к точке входа приложения WPF, чтобы создать экземпляр класса `App`, определенного в проекте UWP (этот класс является производным от `XamlApplication`).

1. В проекте WPF щелкните правой кнопкой мыши узел проекта, выберите **Добавить** -> **Новый элемент**, а затем щелкните **Класс**. Назовите класс **Program** и щелкните **Добавить**.

2. Замените созданный класс `Program` кодом ниже, а затем сохраните файл. Замените `MyUWPApp` пространством имен проекта приложения UWP, а `MyWPFApp` — пространством имен проекта приложения WPF.

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

4. На вкладке **Приложение** в диалоговом окне "Свойства" щелкните раскрывающийся список **Объект запуска** и выберите полное имя класса `Program`, добавленного на предыдущем шаге. 
    > [!NOTE]
    > По умолчанию проекты WPF определяют функцию точки входа `Main` в созданном файле кода, который нельзя изменить. На этом шаге точка входа проекта изменяется на метод `Main` нового класса `Program`, что позволяет добавить код, который как можно раньше выполняется в процессе запуска приложения. 

5. Сохраните изменения в свойствах проекта.

## <a name="host-an-inkcanvas-and-inktoolbar-by-using-wrapped-controls"></a>Размещение InkCanvas и InkToolbar с использованием заключенных в оболочку элементов управления

Теперь, когда вы настроили в проекте XAML Islands для UWP, вы можете добавить в приложение заключенные в оболочку элементы управления UWP [InkCanvas](https://docs.microsoft.com/windows/communitytoolkit/controls/wpf-winforms/inkcanvas) и [InkToolbar](https://docs.microsoft.com/windows/communitytoolkit/controls/wpf-winforms/inktoolbar).

1. В **обозревателе решений** откройте файл **MainWindow.xaml**.

2. В элементе **Окно** в верхней части XAML-файла добавьте следующий атрибут. Он указывает на пространство имен XAML, в котором находятся заключенные в оболочку элементы управления UWP [InkCanvas](https://docs.microsoft.com/windows/communitytoolkit/controls/wpf-winforms/inkcanvas) и [InkToolbar](https://docs.microsoft.com/windows/communitytoolkit/controls/wpf-winforms/inktoolbar):

    ```xml
    xmlns:Controls="clr-namespace:Microsoft.Toolkit.Wpf.UI.Controls;assembly=Microsoft.Toolkit.Wpf.UI.Controls"
    ```

    Когда вы добавите этот атрибут, элемент **Window** будет выглядеть следующим образом:

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

3. В файле **MainWindow.xaml** замените имеющийся элемент `<Grid>` следующим XAML-кодом. Этот код XAML добавляет элементы управления [InkCanvas](https://docs.microsoft.com/windows/communitytoolkit/controls/wpf-winforms/inkcanvas) и [InkToolbar](https://docs.microsoft.com/windows/communitytoolkit/controls/wpf-winforms/inktoolbar) (с префиксом ключевого слова **Controls**, которое вы определили ранее как пространство имен) в элемент `<Grid>`.

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
    > Вы также можете добавить эти и другие заключенные в оболочку элементы управления в окно, перетащив их из раздела **набора средств сообщества Windows** на **панели элементов** в конструктор.

4. Сохраните файл **MainWindow.xaml**.

    Если у вас есть устройство, которое поддерживает цифровое перо, например Surface, и вы выполняете инструкции из этого учебника на физическом компьютере, то вы можете создать приложение, запустить его и использовать цифровой рукописный ввод на экране с помощью пера. Если же у вас нет устройства, поддерживающего ввод пером, попытка расписаться с помощью мыши не даст результата. Это связано с тем, что элемент управления **InkCanvas** по умолчанию включен только для цифровых перьев. Но пользователь может изменить это поведение.

5. Откройте файл **MainWindow.xaml.cs**.

6. В верхней части файла добавьте следующее объявление пространства имен:

    ```csharp
    using Microsoft.Toolkit.Win32.UI.Controls.Interop.WinRT;
    ```

7. Найдите конструктор `MainWindow()`. Добавьте приведенную ниже строку кода после метода `InitializeComponent()` и сохраните файл кода.

    ```csharp
    this.myInkCanvas.InkPresenter.InputDeviceTypes = CoreInputDeviceTypes.Mouse | CoreInputDeviceTypes.Pen;
    ```

    Вы можете настроить интерфейс рукописного ввода по умолчанию, используя объект **InkPresenter**. Этот код использует свойство **InputDeviceTypes** для поддержки мыши в качестве устройства рукописного ввода.

8. Снова нажмите клавишу F5, чтобы повторно скомпилировать приложение и открыть его в отладчике. Если вы используете компьютер с мышью, убедитесь, что с помощью мыши вы можете нарисовать что-либо в области холста для рукописного ввода.

## <a name="host-a-calendarview-by-using-the-host-control"></a>Размещение CalendarView с использованием размещаемого элемента управления

После добавления в приложение заключенных в оболочку элементов управления UWP [InkCanvas](https://docs.microsoft.com/windows/communitytoolkit/controls/wpf-winforms/inkcanvas) и [InkToolbar](https://docs.microsoft.com/windows/communitytoolkit/controls/wpf-winforms/inktoolbar) вы можете использовать элемент управления [WindowsXamlHost](https://docs.microsoft.com/windows/communitytoolkit/controls/wpf-winforms/windowsxamlhost) для добавления [CalendarView](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.CalendarView) в приложение.

> [!NOTE]
> Элемент управления [WindowsXamlHost](https://docs.microsoft.com/windows/communitytoolkit/controls/wpf-winforms/windowsxamlhost) предоставляется с пакетом [Microsoft.Toolkit.Wpf.UI.XamlHost](https://www.nuget.org/packages/Microsoft.Toolkit.Wpf.UI.XamlHost). Этот пакет входит в установленный ранее пакет [Microsoft.Toolkit.Wpf.UI.Controls](https://www.nuget.org/packages/Microsoft.Toolkit.Wpf.UI.Controls).

1. В **обозревателе решений** откройте файл **MainWindow.xaml**.

2. В элементе **Окно** в верхней части XAML-файла добавьте следующий атрибут. Он указывает на пространство имен XAML для элемента управления [WindowsXamlHost](https://docs.microsoft.com/windows/communitytoolkit/controls/wpf-winforms/windowsxamlhost).

    ```xml
    xmlns:xamlhost="clr-namespace:Microsoft.Toolkit.Wpf.UI.XamlHost;assembly=Microsoft.Toolkit.Wpf.UI.XamlHost"
    ```

    Когда вы добавите этот атрибут, элемент **Window** будет выглядеть следующим образом:

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

4. В файле **MainWindow.xaml** замените имеющийся элемент `<Grid>` следующим XAML-кодом. Этот код XAML добавляет строку в сетку, а также добавляет объект [WindowsXamlHost](https://docs.microsoft.com/windows/communitytoolkit/controls/wpf-winforms/windowsxamlhost) в последнюю строку. Чтобы разместить элемент управления UWP [CalendarView](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.CalendarView), этот код XAML задает для свойства `InitialTypeName` полное имя элемента управления. Этот код XAML также определяет обработчик событий для события `ChildChanged`, которое происходит при преобразовании размещенного элемента управления для просмотра.

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

5. Сохраните файл **MainWindow.xaml** и откройте файл **MainWindow.xaml.cs**.

7. В верхней части файла добавьте следующее объявление пространства имен:

    ```csharp
    using Microsoft.Toolkit.Wpf.UI.XamlHost;
    ```

10. Добавьте следующий метод обработчика событий `ChildChanged` в класс `MainWindow` и сохраните файл кода. После преобразования размещенного элемента управления для просмотра этот обработчик событий запускается и создает простой обработчик событий для события `SelectedDatesChanged` элемента управления "Календарь".

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

11. Снова нажмите клавишу F5, чтобы повторно скомпилировать приложение и открыть его в отладчике. Убедитесь, что элемент управления "Календарь" теперь отображается в нижней части окна.

## <a name="package-the-app"></a>Создание пакета приложения

При необходимости вы можете упаковать приложение WPF в [пакет MSIX](https://docs.microsoft.com/windows/msix) для развертывания. MSIX — это современная технология упаковки приложений для Windows. В ее основе лежат технологии установки MSI, APPX, App-V и ClickOnce.

В приведенных ниже инструкциях рассказывается, как упаковать все компоненты решения в пакет MSIX с помощью [проекта упаковки приложения Windows](https://docs.microsoft.com/windows/msix/desktop/desktop-to-uwp-packaging-dot-net) в Visual Studio 2019. Эти действия необходимы только в том случае, если нужно упаковать приложение WPF в пакет MSIX.

> [!NOTE]
> Если вы решили не упаковывать приложение в [пакет MSIX](https://docs.microsoft.com/windows/msix) для развертывания, на компьютерах с запущенными приложениями должна быть установлена [среда выполнения Visual C++](https://support.microsoft.com/en-us/help/2977003/the-latest-supported-visual-c-downloads).

1. Добавьте новый [проект упаковки приложений Windows](https://docs.microsoft.com/windows/msix/desktop/desktop-to-uwp-packaging-dot-net) в свое решение. При создании проекта выберите **Windows 10 версии 1903 (10.0; сборка 18362)** в качестве **целевой** и **минимальной** версии.

2. В проекте упаковки щелкните правой кнопкой мыши узел **Приложения** и выберите команду **Добавить ссылку**. В списке проектов выберите проект WPF в решении и нажмите кнопку **ОК**.

3. Настройте решение для конкретной платформы, например x86 или x64. Это необходимо для выполнения сборки приложения WPF в пакет MSIX с помощью проекта упаковки приложений Windows.

    1. В **обозревателе решений** щелкните правой кнопкой мыши узел решения и выберите элементы **Свойства** -> **Свойства конфигурации** -> **Диспетчер конфигураций**.
    2. В списке **Active solution platform** (Активная платформа решения) выберите пункт **x64** или **x86**.
    3. В строке проекта WPF в столбце **Платформа** щелкните **Создать**.
    4. В диалоговом окне **Создание платформы решения** выберите **x64** или **x86** (ту же платформу, которую вы выбрали в списке **Active solution platform** (Активная платформа решения)) и нажмите кнопку **ОК**.
    5. Закройте открытые диалоговые окна.

5. Выполните сборку и запустите проект упаковки. Убедитесь, что WPF выполняется и настраиваемый элемент управления UWP отображается должным образом.

## <a name="related-topics"></a>Связанные темы

* [Элементы управления UWP XAML в классических приложениях (XAML Islands)](xaml-islands.md)
* [Примеры кода для XAML Islands](https://github.com/microsoft/Xaml-Islands-Samples)
* [InkCanvas](https://docs.microsoft.com/windows/communitytoolkit/controls/wpf-winforms/inkcanvas)
* [InkToolbar](https://docs.microsoft.com/windows/communitytoolkit/controls/wpf-winforms/inktoolbar)
* [WindowsXamlHost](https://docs.microsoft.com/windows/communitytoolkit/controls/wpf-winforms/windowsxamlhost)
