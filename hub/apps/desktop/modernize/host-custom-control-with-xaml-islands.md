---
description: В этой статье показано, как разместить пользовательский элемент управления UWP в приложении WPF с помощью XAML-островов.
title: Размещение пользовательского элемента управления UWP в приложении WPF с помощью XAML-островов
ms.date: 08/20/2019
ms.topic: article
keywords: Windows 10, UWP, Windows Forms, WPF, острова XAML, пользовательские элементы управления, пользовательские элементы управления, элементы управления ведущего приложения
ms.author: mcleans
author: mcleanbyron
ms.localizationpriority: medium
ms.custom: 19H1
ms.openlocfilehash: 3c14cfaefcf10aa051e3054d5df2e6da9fd77602
ms.sourcegitcommit: f34deba1d4460d85ed08fe9648999fe03ff6a3dd
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/26/2019
ms.locfileid: "71317066"
---
# <a name="host-a-custom-uwp-control-in-a-wpf-app-using-xaml-islands"></a>Размещение пользовательского элемента управления UWP в приложении WPF с помощью XAML-островов

В этой статье показано, как использовать элемент управления [виндовсксамлхост](https://docs.microsoft.com/windows/communitytoolkit/controls/wpf-winforms/windowsxamlhost) в наборе средств сообщества Windows для размещения пользовательского элемента управления UWP в приложении WPF, предназначенном для .NET Core 3. Пользовательский элемент управления содержит несколько первых элементов управления UWP из Windows SDK и привязывает свойство в одном из элементов управления UWP к строке в приложении WPF. В этой статье также показано, как разместить первый элемент управления UWP из [библиотеки винуи](https://docs.microsoft.com/uwp/toolkits/winui/).

Хотя в этой статье показано, как это сделать в приложении WPF, процесс аналогичен для Windows Forms приложения. Общие сведения о размещении элементов управления UWP в приложениях WPF и Windows Forms см. в [этой статье](xaml-islands.md#wpf-and-windows-forms-applications).

## <a name="overview"></a>Обзор

Для размещения пользовательского элемента управления UWP в приложении WPF потребуются следующие компоненты. В этой статье приводятся инструкции по созданию каждого из этих компонентов.

* **Проект и исходный код для приложения WPF**. Использование элемента управления [виндовсксамлхост](https://docs.microsoft.com/windows/communitytoolkit/controls/wpf-winforms/windowsxamlhost) для размещения пользовательских элементов управления UWP поддерживается только в приложениях WPF и Windows Forms, предназначенных для .NET Core 3. Этот сценарий не поддерживается в приложениях, предназначенных для .NET Framework.

* **Пользовательский элемент управления UWP**. Вам потребуется исходный код для пользовательского элемента управления UWP, который вы хотите разместить, чтобы его можно было скомпилировать с приложением. Как правило, Пользовательский элемент управления определяется в проекте библиотеки классов UWP, на который вы ссылаетесь в том же решении, что и проект WPF (или Windows Forms).

* **Проект приложения UWP, определяющий объект ксамлаппликатион**. Проект WPF (или Windows Forms) должен иметь доступ к экземпляру класса, `Microsoft.Toolkit.Win32.UI.XamlHost.XamlApplication` предоставленного набором средств сообщества Windows. Этот объект выступает в качестве корневого поставщика метаданных для загрузки метаданных пользовательских типов универсального кода пользователя UWP в сборки в текущем каталоге приложения. Для этого рекомендуется добавить **пустой проект приложения (универсальное приложение Windows)** в то же решение, что и проект WPF (или Windows Forms), и исправить класс по умолчанию `App` в этом проекте.
  > [!NOTE]
  > Решение может содержать только один проект, определяющий `XamlApplication` объект. Все пользовательские элементы управления UWP в приложении совместно используют один `XamlApplication` и тот же объект. Проект, определяющий `XamlApplication` объект, должен включать ссылки на все другие библиотеки и проекты UWP, используемые для размещения элементов управления UWP в области XAML.

## <a name="create-a-wpf-project"></a>Создание проекта WPF

Прежде чем приступить к работе, выполните эти инструкции, чтобы создать проект WPF и настроить его для размещения островов XAML. При наличии существующего проекта WPF можно адаптировать эти шаги и примеры кода для проекта.

> [!NOTE]
> При наличии существующего проекта, предназначенного для .NET Framework, необходимо перенести проект в .NET Core 3. Дополнительные сведения см. в [этой серии блогов](https://devblogs.microsoft.com/dotnet/migrating-a-sample-wpf-app-to-net-core-3-part-1/).

1. Установите последнюю версию [пакета SDK для .NET Core 3](https://dotnet.microsoft.com/download/dotnet-core/3.0), если вы еще этого не сделали.

2. В Visual Studio 2019 создайте новый проект **приложения WPF (.NET Core)** .

3. Убедитесь, что [ссылки на пакет](https://docs.microsoft.com/nuget/consume-packages/package-references-in-project-files) включены:

    1. В Visual Studio щелкните **Сервис-> диспетчер пакетов NuGet — параметры диспетчера пакетов >** .
    2. Убедитесь, что **PackageReference** выбран для **формата управления пакетами по умолчанию**.

4. Щелкните правой кнопкой мыши проект WPF в **Обозреватель решений** и выберите **Управление пакетами NuGet**.

5. В окне **Диспетчер пакетов NuGet** установите флажок **включить предварительные выпуски** .

6. Перейдите на вкладку **Обзор** , найдите пакет [Microsoft. Toolkit. WPF. UI. ксамлхост](https://www.nuget.org/packages/Microsoft.Toolkit.Wpf.UI.XamlHost) (Version v 6.0.0-preview7 или более поздней версии) и установите пакет. Этот пакет предоставляет все необходимое для использования элемента управления **виндовсксамлхост** для размещения элемента управления UWP, включая другие связанные пакеты NuGet.
    > [!NOTE]
    > Windows Forms приложения должны использовать пакет [Microsoft. Toolkit. Forms. UI. ксамлхост](https://www.nuget.org/packages/Microsoft.Toolkit.Forms.UI.XamlHost) (Version v 6.0.0-preview7 или более поздней версии).

7. Настройте решение для конкретной платформы, например x86 или x64. Пользовательские элементы управления UWP не поддерживаются в проектах, предназначенных для **любого ЦП**.

    1. В **Обозреватель решений**щелкните правой кнопкой мыши узел решения и выберите **свойства** -> **конфигурации** -> свойства**Configuration Manager**. 
    2. В разделе **Активная платформа решения**выберите **создать**. 
    3. В диалоговом окне **Новая платформа решения** выберите **x64** или **x86** и нажмите кнопку **ОК**. 
    4. Закройте открытые диалоговые окна.

## <a name="create-a-xamlapplication-object-in-a-uwp-app-project"></a>Создание объекта Ксамлаппликатион в проекте приложения UWP

Затем добавьте проект приложения UWP в то же решение, что и проект WPF. Вы измените класс по умолчанию `App` в этом проекте, чтобы он был производным `Microsoft.Toolkit.Win32.UI.XamlHost.XamlApplication` от класса, предоставляемого набором средств сообщества Windows. Объект **виндовсксамлхост** в приложении WPF должен иметь этот `XamlApplication` объект для размещения пользовательских элементов управления UWP.

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

## <a name="create-a-custom-uwp-control"></a>Создание пользовательского элемента управления UWP

Чтобы разместить пользовательский элемент управления UWP в приложении WPF, необходимо иметь исходный код для элемента управления, чтобы его можно было компилировать с приложением. Обычно пользовательские элементы управления определяются в проекте библиотеки классов UWP для простоты переноса.

В этом разделе будет определен простой пользовательский элемент управления UWP в новом проекте библиотеки классов. Можно также определить пользовательский элемент управления UWP в проекте приложения UWP, созданном в предыдущем разделе. Однако эти действия выполняются в отдельном проекте библиотеки классов для наглядных целей, так как обычно для обеспечения переносимости реализуются пользовательские элементы управления.

Если у вас уже есть пользовательский элемент управления, его можно использовать вместо элемента управления, показанного здесь. Однако вам по-прежнему потребуется настроить проект, содержащий элемент управления, как показано в этих шагах.

1. В **Обозреватель решений**щелкните правой кнопкой мыши узел решения и выберите команду **Добавить** -> **Новый проект**.
2. Добавьте в решение проект **библиотеки классов (универсальной платформы Windows)** . Убедитесь, что для целевой версии и минимальной версии задано значение **Windows 10, версия 1903** или более поздняя.
3. Щелкните правой кнопкой мыши файл проекта и выберите команду **Выгрузить проект**. Снова щелкните правой кнопкой мыши файл проекта и выберите команду **изменить**.
4. Перед закрывающим `</Project>` элементом добавьте следующий XML-код, чтобы отключить несколько свойств, а затем сохраните файл проекта. Эти свойства должны быть включены для размещения пользовательского элемента управления UWP в приложении WPF (или Windows Forms).

    ```xml
    <PropertyGroup>
      <EnableTypeInfoReflection>false</EnableTypeInfoReflection>
      <EnableXBindDiagnostics>false</EnableXBindDiagnostics>
    </PropertyGroup>
    ```

5. Щелкните правой кнопкой мыши файл проекта и выберите **Перезагрузить проект**.
6. Удалите файл **Class1.CS** по умолчанию и добавьте новый элемент **пользовательского элемента управления** в проект.
7. В файле XAML для пользовательского элемента управления добавьте следующий `StackPanel` элемент в качестве дочернего по умолчанию. `Grid` В этом примере добавляется ``TextBlock`` элемент управления, а затем в ``XamlIslandMessage`` поле привязывается ``Text`` атрибут этого элемента управления.

    ```xml
    <StackPanel Background="LightCoral">
        <TextBlock>This is a simple custom UWP control</TextBlock>
        <Rectangle Fill="Blue" Height="100" Width="100"/>
        <TextBlock Text="{x:Bind XamlIslandMessage}" FontSize="50"></TextBlock>
    </StackPanel>
    ```

8. В файле кода программной части для пользовательского элемента управления добавьте `XamlIslandMessage` поле в класс User Control, как показано ниже.

    ```csharp
    public sealed partial class MyUserControl : UserControl
    {
        public string XamlIslandMessage { get; set; }

        public MyUserControl()
        {
            this.InitializeComponent();
        }
    }
    ```

9. Создайте проект библиотеки классов UWP.
10. В проекте WPF щелкните правой кнопкой мыши узел **зависимости** и добавьте ссылку на проект библиотеки классов UWP.
11. В проекте приложения UWP, который вы настроили ранее, щелкните правой кнопкой мыши узел **ссылки** и добавьте ссылку на проект библиотеки классов UWP.
12. Перестройте все решение и убедитесь, что все проекты успешно построены.

## <a name="host-the-custom-uwp-control-in-your-wpf-app"></a>Размещение пользовательского элемента управления UWP в приложении WPF

1. В **Обозреватель решений**РАЗВЕРНИТЕ проект WPF и откройте файл MainWindow. XAML или другое окно, в котором требуется разместить пользовательский элемент управления.
2. В файле XAML добавьте следующее объявление пространства имен в `<Window>` элемент.

    ```xml
    xmlns:xaml="clr-namespace:Microsoft.Toolkit.Wpf.UI.XamlHost;assembly=Microsoft.Toolkit.Wpf.UI.XamlHost"
    ```

3. В том же файле добавьте в `<Grid>` элемент следующий элемент управления. `InitialTypeName` Измените атрибут на полное имя пользовательского элемента управления в проекте библиотеки классов UWP.

    ```xml
    <xaml:WindowsXamlHost InitialTypeName="UWPClassLibrary.MyUserControl" ChildChanged="WindowsXamlHost_ChildChanged" />
    ```

4. Откройте файл кода программной части и добавьте в `Window` класс следующий код. Этот код определяет `ChildChanged` обработчик событий, который присваивает значение ``XamlIslandMessage`` поля пользовательского элемента управления UWP значению `WPFMessage` поля в приложении WPF. Замените `UWPClassLibrary.MyUserControl` на полное имя пользовательского элемента управления в проекте библиотеки классов UWP.

    ```csharp
    private void WindowsXamlHost_ChildChanged(object sender, EventArgs e)
    {
        // Hook up x:Bind source.
        global::Microsoft.Toolkit.Wpf.UI.XamlHost.WindowsXamlHost windowsXamlHost =
            sender as global::Microsoft.Toolkit.Wpf.UI.XamlHost.WindowsXamlHost;
        global::UWPClassLibrary.MyUserControl userControl =
            windowsXamlHost.GetUwpInternalObject() as global::UWPClassLibrary.MyUserControl;

        if (userControl != null)
        {
            userControl.XamlIslandMessage = this.WPFMessage;
        }
    }

    public string WPFMessage
    {
        get
        {
            return "Binding from WPF to UWP XAML";
        }
    }
    ```

6. Выполните сборку и запуск приложения и убедитесь, что пользовательский элемент управления UWP отображается ожидаемым образом.

## <a name="add-a-control-from-the-winui-library-to-the-custom-control"></a>Добавление элемента управления из библиотеки Винуи в пользовательский элемент управления

Традиционно элементы управления UWP были выпущены как часть ОС Windows 10 и стали доступны разработчикам с помощью Windows SDK. [Библиотека винуи](https://docs.microsoft.com/uwp/toolkits/winui/) является альтернативным подходом, в котором обновленные версии элементов управления универсальной платформы Windows (UWP) из Windows SDK распределяются в пакете NuGet, который не привязан к выпускам Windows SDK. Эта библиотека также включает новые элементы управления, которые не являются частью Windows SDK и платформы UWP по умолчанию. Дополнительные сведения см. в нашей [стратегии винуи Library](https://github.com/microsoft/microsoft-ui-xaml/blob/master/docs/roadmap.md) .

В этом разделе показано, как добавить элемент управления UWP из библиотеки Винуи в пользовательский элемент управления, чтобы можно было разместить этот элемент управления в приложении WPF. 

1. В проекте приложения UWP установите последнюю предварительную версию пакета NuGet [Microsoft. UI. XAML](https://www.nuget.org/packages/Microsoft.UI.Xaml) .
    > [!NOTE]
    > Убедитесь, что *установлена последняя Предварительная версия.* Сейчас будут работать только предварительные версии этого пакета, если вы решили упаковать приложение в [пакет MSIX](https://docs.microsoft.com/windows/msix) для развертывания.

2. В файле App. XAML в этом проекте добавьте в `<xaml:Application>` элемент следующий дочерний элемент.

    ```xml
    <Application.Resources>
        <XamlControlsResources xmlns="using:Microsoft.UI.Xaml.Controls" />
    </Application.Resources>
    ```

    После добавления этого элемента содержимое этого файла будет выглядеть примерно так.

    ```xml
    <xaml:XamlApplication
        x:Class="MyUWPApp.App"
        xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
        xmlns:xaml="using:Microsoft.Toolkit.Win32.UI.XamlHost"
        xmlns:local="using:MyUWPApp">
        <Application.Resources>
            <XamlControlsResources xmlns="using:Microsoft.UI.Xaml.Controls" />
        </Application.Resources>
    </xaml:XamlApplication>
    ```

3. В проекте библиотеки классов UWP установите последнюю предварительную версию пакета NuGet [Microsoft. UI. XAML](https://www.nuget.org/packages/Microsoft.UI.Xaml) (той же версии, которая была установлена в проекте приложения UWP).

4. В том же проекте откройте файл XAML для пользовательского элемента управления и добавьте следующее объявление пространства имен в `<UserControl>` элемент.

    ```xml
    xmlns:winui="using:Microsoft.UI.Xaml.Controls"
    ```

5. В том же файле добавьте `<winui:RatingControl />` элемент в качестве дочернего элемента `<StackPanel>`для. Этот элемент добавляет экземпляр класса [ратингконтрол](https://docs.microsoft.com/uwp/api/microsoft.ui.xaml.controls.ratingcontrol?view=winui-2.2) из библиотеки винуи. После добавления этого элемента объект `<StackPanel>` должен выглядеть примерно так, как показано далее.

    ```xml
    <StackPanel Background="LightCoral">
        <TextBlock>This is a simple custom UWP control</TextBlock>
        <Rectangle Fill="Blue" Height="100" Width="100"/>
        <TextBlock Text="{x:Bind XamlIslandMessage}" FontSize="50"></TextBlock>
        <winui:RatingControl />
    </StackPanel>
    ```

6. Выполните сборку и запуск приложения и убедитесь, что новый элемент управления рейтингом отображается ожидаемым образом.

## <a name="package-the-app"></a>Упаковка приложения

При необходимости можно упаковать приложение WPF в [пакет MSIX](https://docs.microsoft.com/windows/msix) для развертывания. MSIX — это современная технология упаковки приложений для Windows, которая основана на сочетании технологий установки MSI, APPX, App-V и ClickOnce.

В следующих инструкциях показано, как упаковать все компоненты в решении в пакете MSIX с помощью [проекта упаковки приложений Windows](https://docs.microsoft.com/windows/msix/desktop/desktop-to-uwp-packaging-dot-net) в Visual Studio 2019. Эти действия необходимы только в том случае, если требуется упаковать приложение WPF в пакет MSIX. Обратите внимание, что в настоящее время эти шаги включают некоторые обходные пути, характерные для сценария размещения пользовательских элементов управления UWP.

1. Добавьте новый [проект упаковки приложений Windows](https://docs.microsoft.com/windows/msix/desktop/desktop-to-uwp-packaging-dot-net) в решение. При создании проекта выберите **Windows 10, версия 1903 (10,0; Сборка 18362)** для **целевой версии** и **минимальной версии**.

2. В проекте упаковки щелкните правой кнопкой мыши узел **приложения** и выберите команду **Добавить ссылку**. В списке проектов выберите проект WPF в решении и нажмите кнопку **ОК**.

3. Измените файл проекта упаковки. В настоящее время эти изменения необходимы для упаковки приложений WPF, предназначенных для .NET Core 3, и для которых требуются основные острова XAML.

    1. В обозреватель решений щелкните правой кнопкой мыши узел проект упаковки и выберите пункт **изменить файл проекта**.
    2. Найдите в этом файле элемент `<Import Project="$(WapProjPath)\Microsoft.DesktopBridge.targets" />`. Замените этот элемент следующим XML-кодом. В настоящее время эти изменения необходимы для упаковки приложений WPF, предназначенных для .NET Core 3, и для размещения элементов управления UWP.

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

4. Измените манифест пакета, чтобы он ссылался на правильное изображение экрана-заставки по умолчанию. В настоящее время это решение требуется для упаковки приложений WPF, в которых размещаются пользовательские элементы управления UWP.

    1. В проекте упаковки щелкните правой кнопкой мыши файл **Package. appxmanifest** и выберите пункт **Просмотреть код**.
    2. В файле щелкните следующий элемент.

        ```<uap:SplashScreen Image="Images\SplashScreen.png" />```

    3. Измените этот элемент на:

        ```<uap:SplashScreen Image="Images\SplashScreen.scale-200.png" />```

    4. Сохраните файл **Package. appxmanifest** и закройте его.

5. Измените файл проекта WPF. В настоящее время эти изменения необходимы для упаковки приложений WPF, в которых размещаются настраиваемые элементы управления UWP.

    1. В обозреватель решений щелкните правой кнопкой мыши узел проекта WPF и выберите команду **Выгрузить проект**.
    2. Щелкните правой кнопкой мыши узел проекта WPF и выберите пункт **изменить**.
    3. Поместите последний `</PropertyGroup>` закрывающий тег в файл и добавьте следующий XML сразу после этого тега.

        ``` xml
        <PropertyGroup>
          <AssetTargetFallback>uap10.0.18362</AssetTargetFallback>
        </PropertyGroup>
        ```

    4. Сохраните файл проекта и закройте его.
    5. Щелкните правой кнопкой мыши узел проекта WPF и выберите **Перезагрузить проект**.

6. Создайте и запустите проект упаковки. Убедитесь, что WPF выполняется и пользовательский элемент управления UWP отображается ожидаемым образом.

## <a name="related-topics"></a>См. также

* [Элементы управления UWP в классических приложениях](xaml-islands.md)
* [виндовсксамлхост](https://docs.microsoft.com/windows/communitytoolkit/controls/wpf-winforms/windowsxamlhost)
