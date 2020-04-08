---
description: Эта статья содержит сведения о размещении настраиваемого элемента управления UWP в приложении WPF, использующем XAML Islands.
title: Размещение настраиваемого элемента управления UWP в приложении WPF, использующем XAML Islands
ms.date: 01/24/2020
ms.topic: article
keywords: windows 10, uwp, windows forms, wpf, xaml islands, настраиваемые элементы управления, пользовательские элементы управления, размещение элементов управления
ms.author: mcleans
author: mcleanbyron
ms.localizationpriority: medium
ms.custom: 19H1
ms.openlocfilehash: b1ac53e0a6b6e01cd2129e2b1893f91fae2ef0fe
ms.sourcegitcommit: c660def841abc742600fbcf6ed98e1f4f7beb8cc
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/24/2020
ms.locfileid: "80218604"
---
# <a name="host-a-custom-uwp-control-in-a-wpf-app-using-xaml-islands"></a>Размещение настраиваемого элемента управления UWP в приложении WPF, использующем XAML Islands

Эта статья содержит сведения об использовании элемента управления [WindowsXamlHost](https://docs.microsoft.com/windows/communitytoolkit/controls/wpf-winforms/windowsxamlhost) в наборе средств сообщества Windows для размещения настраиваемого элемента управления универсальной платформы Windows (UWP) в приложении WPF, предназначенном для .NET Core 3. Настраиваемые элементы управления содержат несколько основных элементов управления UWP из пакета Windows SDK. Кроме того, они связывают свойство в одном из элементов управления UWP со строкой приложения WPF. В этой статье также показано, как разместить элемент управления UWP из библиотеки [WinUI](https://docs.microsoft.com/uwp/toolkits/winui/).

Хотя в этой статье показано, как выполнить эти действия в приложении WPF, для приложения Windows Forms процесс является аналогичным. Общие сведения о размещении элементов управления UWP в приложениях WPF и Windows Forms см. в [этой статье](xaml-islands.md#wpf-and-windows-forms-applications).

## <a name="required-components"></a>Необходимые компоненты

Чтобы разместить настраиваемый элемент управления UWP в приложении WPF (или Windows Forms), вам потребуются указанные ниже компоненты в решении. Эта статья содержит инструкции по созданию каждого из этих компонентов.

* **Код проекта и исходный код приложения**. Использование элемента управления [WindowsXamlHost](https://docs.microsoft.com/windows/communitytoolkit/controls/wpf-winforms/windowsxamlhost) для размещения настраиваемых элементов управления UWP поддерживается только в приложениях, предназначенных для .NET Core 3. Этот сценарий не поддерживается в приложениях, предназначенных для .NET Framework.

* **Настраиваемый элемент управления UWP**. Для настраиваемого элемента управления UWP, который необходимо разместить, необходим исходный код, чтобы скомпилировать его в приложение. Как правило, настраиваемый элемент управления определяется в проекте библиотеки классов UWP, на которую вы ссылаетесь в решении с проектом WPF или Windows Forms.

* **Проект приложения UWP, определяющий корневой класс приложения, производный от XamlApplication**. В проекте WPF или Windows Forms должен быть доступ к экземпляру класса [Microsoft.Toolkit.Win32.UI.XamlHost.XamlApplication](https://github.com/windows-toolkit/Microsoft.Toolkit.Win32/tree/master/Microsoft.Toolkit.Win32.UI.XamlApplication), предоставленному в наборе средств сообщества Windows. Для этого рекомендуется определить объект в отдельном проекте приложения UWP, который является частью решения приложения WPF или Windows Forms. Этот объект выступает в качестве поставщика корневых метаданных для загрузки метаданных настраиваемых типов XAML UWP в сборках текущего каталога приложения.

    > [!NOTE]
    > Решение может содержать только один проект, определяющий объект `XamlApplication`. Все настраиваемые элементы управления UWP в приложении используют один и тот же объект `XamlApplication`. Проект, определяющий объект `XamlApplication`, должен содержать ссылки на все другие библиотеки и проекты UWP, которые используются для размещения элементов управления UWP в XAML Islands.

## <a name="create-a-wpf-project"></a>Создание проекта WPF

Перед началом работы выполните следующие инструкции, чтобы создать проект WPF и настроить его для размещения XAML Islands. Если у вас есть проект WPF, вы можете адаптировать под него эти шаги и примеры кода.

> [!NOTE]
> Если у вас есть проект, предназначенный для .NET Framework, его необходимо перенести на .NET Core 3. Дополнительные сведения см. в [этой записи блога](https://devblogs.microsoft.com/dotnet/migrating-a-sample-wpf-app-to-net-core-3-part-1/).

1. Если вы еще не сделали этого, установите последнюю версию [пакета SDK для .NET Core 3](https://dotnet.microsoft.com/download/dotnet-core/3.0).

2. В Visual Studio 2019 создайте проект **приложения WPF (.NET Core)** .

3. Убедитесь, что [ссылки на пакеты](https://docs.microsoft.com/nuget/consume-packages/package-references-in-project-files) активны:

    1. В Visual Studio выберите элементы **Сервис -> Диспетчер пакетов NuGet -> Параметры диспетчера пакетов**.
    2. Убедитесь, что для **формата управления пакетами по умолчанию** установлено значение **PackageReference**.

4. В **обозревателе решений** щелкните правой кнопкой мыши проект WPF и выберите пункт **Управление пакетами NuGet**.

5. Убедитесь, что в окне **Диспетчер пакетов NuGet** выбран параметр **Включить предварительные выпуски**.

6. Откройте вкладку **Обзор**, найдите пакет [Microsoft.Toolkit.Wpf.UI.XamlHost](https://www.nuget.org/packages/Microsoft.Toolkit.Wpf.UI.XamlHost) (версии 6.0.0 или более поздней) и установите его. Этот пакет содержит все необходимые компоненты для размещения элемента управления UWP, в том числе другие связанные пакеты NuGet, с помощью элемента управления **WindowsXamlHost**.
    > [!NOTE]
    > В приложениях Windows Forms необходимо использовать пакет [Microsoft.Toolkit.Forms.UI.XamlHost](https://www.nuget.org/packages/Microsoft.Toolkit.Forms.UI.XamlHost) (версии 6.0.0 или более поздней).

7. Настройте решение для конкретной платформы, например x86 или x64. Настраиваемые элементы управления UWP не поддерживаются в проектах, предназначенных для **любого ЦП**.

    1. В **обозревателе решений** щелкните правой кнопкой мыши узел решения и выберите элементы **Свойства** -> **Свойства конфигурации** -> **Диспетчер конфигураций**.
    2. В разделе **Active solution platform** (Активная платформа решения) щелкните **Создать**. 
    3. В диалоговом окне **Создание платформы решения** выберите **x64** или **x86** и нажмите кнопку **ОК**. 
    4. Закройте открытые диалоговые окна.

## <a name="define-a-xamlapplication-class-in-a-uwp-app-project"></a>Определение класса XamlApplication в проекте приложения UWP

Далее добавьте проект приложения UWP в решение и измените стандартный класс `App` в этом проекте, чтобы он был производным от класса [Microsoft.Toolkit.Win32.UI.XamlHost.XamlApplication](https://github.com/windows-toolkit/Microsoft.Toolkit.Win32/tree/master/Microsoft.Toolkit.Win32.UI.XamlApplication), предоставленного в наборе средств сообщества Windows. Приложение будет использовать этот класс в качестве поставщика корневых метаданных для загрузки метаданных настраиваемых типов XAML UWP в сборках текущего каталога приложения.

1. В **обозревателе решений** щелкните правой кнопкой мыши узел решения и выберите команду **Добавить** -> **Новый проект**.
2. Добавьте проект **Пустое приложение (универсальная платформа Windows)** в свое решение. Убедитесь, что в качестве целевой и самой ранней версии используется **Windows 10 версии 1903** или более поздней.
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
7. Очистите проект приложения UWP, а затем выполните его сборку.
8. В проекте WPF щелкните правой кнопкой мыши узел **Зависимости** и добавьте ссылку на проект приложения UWP.

## <a name="instantiate-the-xamlapplication-object-in-the-entry-point-of-your-wpf-app"></a>Создание объекта XamlApplication в точке входа приложения WPF

Далее добавьте код к точке входа приложения WPF, чтобы создать экземпляр класса `App`, определенного в проекте UWP (это класс является производным от `XamlApplication`). Этот объект выступает в качестве поставщика корневых метаданных для загрузки метаданных настраиваемых типов XAML UWP в сборках текущего каталога приложения.

1. В проекте WPF щелкните правой кнопкой мыши узел проекта, выберите **Добавить** -> **Новый элемент**, а затем щелкните **Класс**. Назовите класс **Program** и щелкните **Добавить**.

2. Замените созданный класс `Program` указанным ниже кодом, а затем сохраните файл. Замените `MyUWPApp` пространством имен проекта приложения UWP, а затем замените `MyWPFApp` пространством имен проекта приложения WPF.

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

4. На вкладке **Приложение** диалогового окна "Свойства" щелкните раскрывающееся меню **Объект запуска** и выберите полное имя класса `Program`, добавленного на предыдущем шаге. 
    > [!NOTE]
    > По умолчанию проекты WPF определяют функцию точки входа `Main` в созданном файле кода, который нельзя изменить. На этом шаге точка входа проекта изменяется на метод `Main` нового класса `Program`. Этот метод позволяет добавлять код, который как можно раньше выполняется в процессе запуска приложения. 

5. Сохраните изменения в свойствах проекта.

## <a name="create-a-custom-uwp-control"></a>Создание настраиваемого элемента управления UWP

Чтобы разместить настраиваемый элемент управления UWP в приложении WPF, необходим исходный код элемента управления, чтобы скомпилировать его в приложение. Обычно, чтобы облегчить перенос, настраиваемые элементы управления определяются в проекте библиотеки классов UWP.

В этом разделе вы определите простой пользовательский элемент управления UWP в новом проекте библиотеки классов. Вы также можете определить настраиваемый элемент управления UWP в проекте приложения UWP, созданном в предыдущем разделе. Но для демонстрации эти действия выполняются в отдельном проекте библиотеки классов, так как это типичный способ внедрения настраиваемых элементов управления, чтобы обеспечить возможность переноса.

Если у вас уже есть настраиваемый элемент управления, его можно использовать вместо элемента управления, показанного здесь. Но вам все равно необходимо настроить проект, содержащий элемент управления, как показано в этих шагах.

1. В **обозревателе решений** щелкните правой кнопкой мыши узел решения и выберите команду **Добавить** -> **Новый проект**.
2. Добавьте проект **библиотеки классов (универсальная платформа Windows)** в свое решение. Убедитесь, что в качестве целевой и самой ранней версии используется **Windows 10 версии 1903** или более поздней.
3. Щелкните правой кнопкой мыши файл проекта и выберите команду **Выгрузить проект**. Снова щелкните правой кнопкой мыши файл проекта и выберите команду **Изменить**.
4. Перед закрывающим элементом `</Project>` добавьте указанный ниже XML-код, чтобы отключить несколько свойств, а затем сохраните файл проекта. Эти свойства должны быть включены для размещения настраиваемого элемента управления UWP в приложении WPF (или Windows Forms).

    ```xml
    <PropertyGroup>
      <EnableTypeInfoReflection>false</EnableTypeInfoReflection>
      <EnableXBindDiagnostics>false</EnableXBindDiagnostics>
    </PropertyGroup>
    ```

5. Щелкните правой кнопкой мыши файл проекта и выберите команду **Перезагрузить проект**.
6. Удалите стандартный файл **Class1.cs** и добавьте новый **пользовательский элемент управления** в проект.
7. В файле XAML для пользовательского элемента управления добавьте следующий объект `StackPanel` в качестве дочернего элемента `Grid` по умолчанию. В этом примере добавляется элемент управления ``TextBlock``, а затем атрибут ``Text`` этого элемента управления привязывается к полю ``XamlIslandMessage``.

    ```xml
    <StackPanel Background="LightCoral">
        <TextBlock>This is a simple custom UWP control</TextBlock>
        <Rectangle Fill="Blue" Height="100" Width="100"/>
        <TextBlock Text="{x:Bind XamlIslandMessage}" FontSize="50"></TextBlock>
    </StackPanel>
    ```

8. В файле кода программной части пользовательского интерфейса добавьте поле `XamlIslandMessage` к классу пользовательского элемента управления, как показано ниже.

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

9. Выполните сборку проекта библиотеки классов UWP.
10. В проекте WPF щелкните правой кнопкой мыши узел **Зависимости** и добавьте ссылку на проект библиотеки классов UWP.
11. В ранее настроенном проекте приложения UWP щелкните правой кнопкой мыши узел **Ссылки** и добавьте ссылку на проект библиотеки классов UWP.
12. Перестройте все решение и убедитесь, что все проекты успешно построены.

## <a name="host-the-custom-uwp-control-in-your-wpf-app"></a>Размещение настраиваемого элемента управления UWP в приложении WPF

1. В **обозревателе решений** разверните проект WPF и откройте файл MainWindow.xaml или другое окно, где вам необходимо разместить настраиваемый элемент управления.
2. В файле XAML добавьте следующее объявление пространства имен в элемент `<Window>`.

    ```xml
    xmlns:xaml="clr-namespace:Microsoft.Toolkit.Wpf.UI.XamlHost;assembly=Microsoft.Toolkit.Wpf.UI.XamlHost"
    ```

3. В том же файле добавьте указанный ниже элемент управления в элемент `<Grid>`. Измените атрибут `InitialTypeName` на полное имя пользовательского элемента управления в проекте библиотеки классов UWP.

    ```xml
    <xaml:WindowsXamlHost InitialTypeName="UWPClassLibrary.MyUserControl" ChildChanged="WindowsXamlHost_ChildChanged" />
    ```

4. Откройте файл кода программной части и добавьте указанный ниже код в класс `Window`. Этот код определяет обработчик событий `ChildChanged`, который присваивает значение поля ``XamlIslandMessage`` настраиваемого элемента управления UWP в качестве значения поля `WPFMessage` в приложении WPF. Измените `UWPClassLibrary.MyUserControl` на полное имя пользовательского элемента управления в проекте библиотеки классов UWP.

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

6. Выполните сборку и запустите приложение, а затем убедитесь, что пользовательский элемент управления UWP отображается должным образом.

## <a name="add-a-control-from-the-winui-library-to-the-custom-control"></a>Добавление элемента управления из библиотеки WinUI в настраиваемый элемент управления

В большинстве случаев элементы управления UWP выпущены в составе ОС Windows 10. Разработчики могут использовать их через пакет Windows SDK. В качестве альтернативного варианта можно использовать [библиотеку WinUI](https://docs.microsoft.com/uwp/toolkits/winui/). Здесь обновленные версии элементов управления UWP из пакета Windows SDK распределены в пакете NuGet, который не связан с выпусками пакета Windows SDK. Эта библиотека также содержит новые элементы управления, которые не входят в состав пакета Windows SDK и платформы UWP по умолчанию. Дополнительные сведения см. в [схеме библиотек WinUI](https://github.com/microsoft/microsoft-ui-xaml/blob/master/docs/roadmap.md).

В этом разделе показано, как добавить элемент управления UWP из библиотеки WinUI в пользовательский элемент управления, чтобы вы могли разместить его в приложении WPF.

1. В проекте приложения UWP установите последнюю версию пакета NuGet [Microsoft.UI.Xaml](https://www.nuget.org/packages/Microsoft.UI.Xaml).

2. В файле App.xaml этого проекта добавьте следующий дочерний элемент в элемент `<xaml:XamlApplication>`.

    ```xml
    <Application.Resources>
        <XamlControlsResources xmlns="using:Microsoft.UI.Xaml.Controls" />
    </Application.Resources>
    ```

    После добавления этого элемента содержимое файла должно выглядеть следующим образом:

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

3. В проекте библиотеки классов UWP установите последнюю версию пакета NuGet [Microsoft.UI.Xaml](https://www.nuget.org/packages/Microsoft.UI.Xaml) (той же версии, которая установлена в проекте приложения UWP).

4. В том же проекте откройте файл XAML пользовательского элемента управления и добавьте следующее объявление пространства имен в элемент `<UserControl>`.

    ```xml
    xmlns:winui="using:Microsoft.UI.Xaml.Controls"
    ```

5. В том же файле добавьте элемент `<winui:RatingControl />` в качестве дочернего элемента `<StackPanel>`. Этот элемент добавляет экземпляр класса [RatingControl](https://docs.microsoft.com/uwp/api/microsoft.ui.xaml.controls.ratingcontrol?view=winui-2.2) из библиотеки WinUI. После добавления этого элемента `<StackPanel>` должен выглядеть следующим образом.

    ```xml
    <StackPanel Background="LightCoral">
        <TextBlock>This is a simple custom UWP control</TextBlock>
        <Rectangle Fill="Blue" Height="100" Width="100"/>
        <TextBlock Text="{x:Bind XamlIslandMessage}" FontSize="50"></TextBlock>
        <winui:RatingControl />
    </StackPanel>
    ```

6. Выполните сборку и запустите приложение, а затем убедитесь, что новый элемент управления для оценки отображается должным образом.

## <a name="package-the-app"></a>Создание пакета приложения

При необходимости вы можете упаковать приложение WPF в [пакет MSIX](https://docs.microsoft.com/windows/msix) для развертывания. MSIX — это современная технология упаковки приложений для Windows. В ее основе лежат технологии установки MSI, APPX, App-V и ClickOnce.

В приведенных ниже инструкциях рассказывается, как упаковать все компоненты решения в пакет MSIX с помощью [проекта упаковки приложения Windows](https://docs.microsoft.com/windows/msix/desktop/desktop-to-uwp-packaging-dot-net) в Visual Studio 2019. Эти действия необходимы только в том случае, если нужно упаковать приложение WPF в пакет MSIX. Обратите внимание, что в настоящее время эти шаги предусматривают некоторые обходные пути, которые зависят от сценария размещения настраиваемых элементов управления UWP.

> [!NOTE]
> Если вы решили не упаковывать приложение в [пакет MSIX](https://docs.microsoft.com/windows/msix) для развертывания, на компьютерах с запущенными приложениями должна быть установлена [среда выполнения Visual C++](https://support.microsoft.com/en-us/help/2977003/the-latest-supported-visual-c-downloads).

1. Добавьте новый [проект упаковки приложений Windows](https://docs.microsoft.com/windows/msix/desktop/desktop-to-uwp-packaging-dot-net) в свое решение. При создании проекта выберите **Windows 10 версии 1903 (10.0; сборка 18362)** в качестве **целевой** и **минимальной** версии.

2. В проекте упаковки щелкните правой кнопкой мыши узел **Приложения** и выберите команду **Добавить ссылку**. В списке проектов выберите проект WPF в решении и нажмите кнопку **ОК**.

3. Измените файл проекта WPF. В настоящее время эти изменения необходимы для создания пакетов приложений WPF, в которых размещены настраиваемые элементы управления UWP.

    1. В обозревателе решений щелкните правой кнопкой узел проекта WPF и выберите команду **Выгрузить проект**.
    2. Щелкните правой кнопкой мыши узел проекта WPF и выберите команду **Изменить**.
    3. Найдите последний закрывающий тег `</PropertyGroup>` в файле и добавьте следующий XML-код сразу после этого тега.

        ``` xml
        <PropertyGroup>
          <AssetTargetFallback>uap10.0.18362</AssetTargetFallback>
        </PropertyGroup>
        ```

    4. Сохраните файл проекта и закройте его.
    5. Щелкните правой кнопкой мыши узел проекта WPF и выберите команду **Перезагрузка проекта**.

4. Выполните сборку и запустите проект упаковки. Убедитесь, что WPF выполняется и настраиваемый элемент управления UWP отображается должным образом.

## <a name="related-topics"></a>Связанные темы

* [Элементы управления UWP XAML в классических приложениях (XAML Islands)](xaml-islands.md)
* [Примеры кода для XAML Islands](https://github.com/microsoft/Xaml-Islands-Samples)
* [WindowsXamlHost](https://docs.microsoft.com/windows/communitytoolkit/controls/wpf-winforms/windowsxamlhost)
