---
description: Эта статья содержит сведения о размещении настраиваемого элемента управления UWP в приложении Win32 на C++ с помощью API размещения XAML.
title: Размещение настраиваемого элемента управления UWP в приложении Win32 на C++ с помощью API размещения XAML
ms.date: 03/23/2020
ms.topic: article
keywords: windows 10, uwp, C++, Win32, xaml islands, настраиваемые элементы управления, пользовательские элементы управления, размещение элементов управления
ms.author: mcleans
author: mcleanbyron
ms.localizationpriority: medium
ms.custom: 19H1
ms.openlocfilehash: 93badc28c9c4fa1684836fc4a883e54661e8d4dc
ms.sourcegitcommit: 7112e4ec3f19d46a1fc4d81d1c29fd9c01522610
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/09/2020
ms.locfileid: "80986972"
---
# <a name="host-a-custom-uwp-control-in-a-c-win32-app"></a>Размещение настраиваемого элемента управления UWP в приложении Win32 на C++

Эта статья содержит сведения о размещении настраиваемого элемента управления XAML UWP в новом приложении Win32 на C++ с помощью [API размещения XAML UWP](using-the-xaml-hosting-api.md). Если у вас есть проект приложения Win32 на C++, вы можете адаптировать под него эти шаги и примеры кода.

Чтобы разместить настраиваемый элемент управления XAML UWP, в рамках этого пошагового руководства вы создадите следующие проекты и компоненты:

* **Проект классического приложения для Windows**. Этот проект реализует собственное классическое приложение Win32 на C++. Вы добавите код в этот проект, использующий API размещения XAML UWP для размещения настраиваемого элемента управления XAML UWP.

* **Проект приложения UWP (C++/WinRT)** . Этот проект реализует настраиваемый элемент управления XAML UWP. Он также реализует корневой поставщик метаданных для загрузки метаданных настраиваемых типов XAML UWP в проекте.

## <a name="requirements"></a>Требования

* Visual Studio 2019 версии 16.4.3 и более поздних версий.
* Windows 10 версии 1903 SDK (версия 10.0.18362) или более поздней версии.
* [Расширение Visual Studio (VSIX) C++/WinRT](https://marketplace.visualstudio.com/items?itemName=CppWinRTTeam.cppwinrt101804264), установленное с помощью Visual Studio. C++/WinRT — это полностью стандартная проекция языка C++17 для API среды выполнения Windows (WinRT), реализованная как библиотека на основе файлов заголовков и предназначенная для предоставления вам первоклассного доступа к современным интерфейсам API Windows. Дополнительные сведения см. в статье [C++/WinRT](https://docs.microsoft.com/windows/uwp/cpp-and-winrt-apis/).

## <a name="create-a-desktop-application-project"></a>Создание проекта классического приложения

1. В Visual Studio создайте проект **классического приложения для Windows** с именем **MyDesktopWin32App**. Доступ к шаблону этого проекта можно получить, используя фильтры для проекта **Рабочий стол**, **C++** и **Windows**.

2. В **обозревателе решений** щелкните правой кнопкой мыши узел решения, выберите команду **Изменить целевую платформу решения**, выберите **10.0.18362.0** или более позднюю версию пакета SDK, а затем нажмите кнопку **ОК**.

3. Установите пакет NuGet [Microsoft.Windows.CppWinRT](https://www.nuget.org/packages/Microsoft.Windows.CppWinRT/), чтобы включить поддержку [C++/WinRT](/windows/uwp/cpp-and-winrt-apis) в проекте.

    1. Щелкните правой кнопкой мыши проект **MyDesktopWin32App** в **обозревателе решений** и выберите пункт **Manage NuGet Packages** (Управление пакетами NuGet).
    2. Перейдите на вкладку **Обзор**, найдите пакет [Microsoft.Windows.CppWinRT](https://www.nuget.org/packages/Microsoft.Windows.CppWinRT/) и установите последнюю версию этого пакета.

4. В окне **Manage NuGet Packages** (Управление пакетами NuGet) установите следующие дополнительные пакеты NuGet:

    * [Microsoft.Toolkit.Win32.UI.SDK](https://www.nuget.org/packages/Microsoft.Toolkit.Win32.UI.SDK) (версии 6.0.0 или более поздней). Этот пакет включает несколько ресурсов сборки и времени выполнения, которые позволяют XAML Islands работать в приложении.
    * [Microsoft.Toolkit.Win32.UI.XamlApplication](https://www.nuget.org/packages/Microsoft.Toolkit.Win32.UI.XamlApplication) (версии 6.0.0 или более поздней). Пакет определяет класс [Microsoft.Toolkit.Win32.UI.XamlHost.XamlApplication](https://github.com/windows-toolkit/Microsoft.Toolkit.Win32/tree/master/Microsoft.Toolkit.Win32.UI.XamlApplication), который будет использоваться позднее в рамках этого пошагового руководства.
    * [Microsoft.VCRTForwarders.140](https://www.nuget.org/packages/Microsoft.VCRTForwarders.140).

5. Запустите сборку решения и убедитесь, что она проходит успешно.

## <a name="create-a-uwp-app-project"></a>Создание проекта приложения UWP

Теперь добавьте проект приложения **UWP (C++/WinRT)** в решение и внесите некоторые изменения в конфигурацию этого проекта. Далее в этом пошаговом руководстве вы добавите код в этот проект, чтобы внедрить настраиваемый элемент управления XAML UWP и определить экземпляр класса [Microsoft.Toolkit.Win32.UI.XamlHost.XamlApplication](https://github.com/windows-toolkit/Microsoft.Toolkit.Win32/tree/master/Microsoft.Toolkit.Win32.UI.XamlApplication). 

1. В **обозревателе решений** щелкните правой кнопкой мыши узел решения и выберите команду **Добавить** -> **Новый проект**.

2. Добавьте в решение проект **пустого приложения (C++/WinRT)** . Присвойте проекту имя **MyUWPApp** и убедитесь, что в качестве целевой и минимальной версии используется **Windows 10 версии 1903** или более поздней.

3. Установите пакет NuGet [Microsoft.Toolkit.Win32.UI.XamlApplication](https://www.nuget.org/packages/Microsoft.Toolkit.Win32.UI.XamlApplication) в проект **MyUWPApp**. Пакет определяет класс [Microsoft.Toolkit.Win32.UI.XamlHost.XamlApplication](https://github.com/windows-toolkit/Microsoft.Toolkit.Win32/tree/master/Microsoft.Toolkit.Win32.UI.XamlApplication), который будет использоваться позднее в рамках этого пошагового руководства.

    1. Щелкните правой кнопкой мыши проект **MyUWPApp** и выберите пункт **Manage NuGet Packages** (Управление пакетами NuGet).
    2. Перейдите на вкладку **Обзор**, найдите пакет [Microsoft.Toolkit.Win32.UI.XamlApplication](https://www.nuget.org/packages/Microsoft.Toolkit.Win32.UI.XamlApplication) и установите версию 6.0.0 или более позднюю версию этого пакета.

4. Щелкните правой кнопкой мыши узел **MyUWPApp** и выберите пункт **Свойства**. Перейдите на страницу **Общие свойства** -> **C++/WinRT**, задайте следующие свойства, а затем нажмите кнопку **Применить**.

    * Задайте для параметра **Уровень детализации** значение **обычный**.
    * Задайте для параметра **Оптимизирован** значение **Нет**.

    По завершении страница свойств должна выглядеть следующим образом:

    ![Свойства проекта C++/WinRT](images/xaml-islands/xaml-island-cpp-1.png)

5. Перейдите на страницу **Свойства конфигурации** -> **Общие** в окне свойств, установите для параметра **Тип конфигурации** значение **Динамическая библиотека (.dll)** , а затем нажмите кнопку **ОК**, чтобы закрыть окно свойств.

    ![Общие свойства проекта](images/xaml-islands/xaml-island-cpp-2.png)

6. Добавьте исполняемый файл заполнителя в проект **MyUWPApp**. Этот исполняемый файл заполнителя необходим для создания необходимых файлов проекта и правильной сборки проекта в Visual Studio.

    1. В **обозревателе решений** щелкните правой кнопкой мыши узел проекта **MyUWPApp** и выберите элементы **Добавить** -> **Новый элемент**.
    2. В диалоговом окне **Добавление нового элемента** выберите раздел **Служебная программа** на панели слева, а затем щелкните **Текстовый файл (.txt)** . Введите имя **placeholder.exe** и нажмите кнопку **Добавить**.
      ![Добавление текстового файла](images/xaml-islands/xaml-island-cpp-3.png)
    3. В **обозревателе решений** выберите файл **placeholder.exe**. В окне **Свойства** убедитесь, что для свойства **Содержимое** установлено значение **True**.
    4. В **обозревателе решений** щелкните правой кнопкой мыши файл **Package.appxmanifest** в проекте **MyUWPApp**, выберите команду **Открыть с помощью**, а затем **Редактор (текстовый) XML** и нажмите кнопку **ОК**.
    5. Найдите элемент **&lt;Приложение&gt;** и измените значение для атрибута **Исполняемый файл** на `placeholder.exe`. По завершении элемент **&lt;Приложение&gt;** должен выглядеть примерно так:

        ```xml
        <Application Id="App" Executable="placeholder.exe" EntryPoint="MyUWPApp.App">
          <uap:VisualElements DisplayName="MyUWPApp" Description="Project for a single page C++/WinRT Universal Windows Platform (UWP) app with no predefined layout"
            Square150x150Logo="Assets\Square150x150Logo.png" Square44x44Logo="Assets\Square44x44Logo.png" BackgroundColor="transparent">
            <uap:DefaultTile Wide310x150Logo="Assets\Wide310x150Logo.png">
            </uap:DefaultTile>
            <uap:SplashScreen Image="Assets\SplashScreen.png" />
          </uap:VisualElements>
        </Application>
        ```

    6. Сохраните и закройте файл **Package.appxmanifest**.

7. В **обозревателе решений** щелкните правой кнопкой мыши узел **MyUWPApp** и выберите команду **Выгрузить проект**.
8. Щелкните правой кнопкой мыши узел **MyUWPApp** и выберите **MyUWPApp.vcxproj**.
9. Найдите элемент `<Import Project="$(VCTargetsPath)\Microsoft.Cpp.Default.props" />` и замените его ниже указанным XML-кодом. Этот XML-код добавляет несколько новых свойств непосредственно перед элементом.

    ```xml
    <PropertyGroup Label="Globals">
        <WindowsAppContainer>true</WindowsAppContainer>
        <AppxGeneratePriEnabled>true</AppxGeneratePriEnabled>
        <ProjectPriIndexName>App</ProjectPriIndexName>
        <AppxPackage>true</AppxPackage>
    </PropertyGroup>
    <Import Project="$(VCTargetsPath)\Microsoft.Cpp.Default.props" />
    ```

10. Сохраните и закройте файл проекта.
11. В **обозревателе решений** щелкните правой кнопкой мыши узел **MyUWPApp** и выберите команду **Перезагрузить проект**.

## <a name="configure-the-solution"></a>Настройка решения

В этом разделе вы обновите решение, содержащее все проекты, чтобы настроить зависимости проекта и свойства сборки, необходимые для правильной сборки проектов.

1. В **обозревателе решений** щелкните правой кнопкой мыши узел решения и добавьте новый XML-файл с именем **Solution.props**.
2. Добавьте следующий XML-код в файл **Solution.props**.

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <Project xmlns="http://schemas.microsoft.com/developer/msbuild/2003">
      <PropertyGroup>
        <IntDir>$(SolutionDir)\obj\$(Platform)\$(Configuration)\$(MSBuildProjectName)\</IntDir>
        <OutDir>$(SolutionDir)\bin\$(Platform)\$(Configuration)\$(MSBuildProjectName)\</OutDir>
        <GeneratedFilesDir>$(IntDir)Generated Files\</GeneratedFilesDir>
      </PropertyGroup>
    </Project>
    ```

3. В меню **Вид** щелкните **Диспетчер свойств** (в зависимости от конфигурации он может находиться в разделе **Вид** -> **Другие окна**).
4. В окне **диспетчера свойств** щелкните правой кнопкой мыши **MyDesktopWin32App** и выберите команду **Добавить существующую страницу свойств**. Перейдите в только что добавленный файл **Solution.props** и щелкните **Открыть**.
5. Повторите предыдущий шаг, чтобы добавить файл **Solution.props** в проект **MyUWPApp** в окне **диспетчера свойств**.
6. Закройте окно **диспетчера свойств**.
7. Убедитесь, что изменения на странице свойств сохранены правильно. В **обозревателе решений** щелкните правой кнопкой мыши проект **MyDesktopWin32App** и выберите пункт **Свойства**. Выберите элементы **Свойства конфигурации** -> **Общие** и убедитесь, что в свойствах **Выходной каталог** и **Промежуточный каталог** заданы значения, добавленные в файл **Solution.props**. Вы также можете проверить значения для проекта **MyUWPApp**.
    ![Свойства проекта](images/xaml-islands/xaml-island-cpp-4.png)

8. В **обозревателе решений** щелкните правой кнопкой мыши узел решения и выберите пункт **Зависимости проектов**. В раскрывающемся списке **Проекты** убедитесь, что выбран **MyDesktopWin32App**, и выберите **MyUWPApp** в списке **Depends On** (Зависит от).
    ![Зависимости проекта](images/xaml-islands/xaml-island-cpp-5.png)

9. Нажмите кнопку **ОК**.

## <a name="add-code-to-the-uwp-app-project"></a>Добавление кода в проект приложения UWP

Теперь вы готовы добавить код в проект **MyUWPApp**, чтобы выполнить следующие задачи:

* внедрите настраиваемый элемент управления XAML UWP. Далее в этом пошаговом руководстве вы добавите код, который размещает этот элемент управления в проекте **MyDesktopWin32App**.
* Определите тип, производный от класса [Microsoft.Toolkit.Win32.UI.XamlHost.XamlApplication](https://github.com/windows-toolkit/Microsoft.Toolkit.Win32/tree/master/Microsoft.Toolkit.Win32.UI.XamlApplication) в наборе средств сообщества Windows.

### <a name="define-a-custom-uwp-xaml-control"></a>Определение настраиваемого элемента управления XAML UWP

1. В **обозревателе решений** щелкните правой кнопкой мыши **MyUWPApp** и выберите **Добавить** -> **Новый элемент**. Выберите узел **Visual C++** в левой области, затем **Blank User Control (C++/WinRT)** (Пустой пользовательский элемент управления (C++/WinRT)), назовите его **MyUserControl** и щелкните **Добавить**.
2. В редакторе XAML замените содержимое файла **MyUserControl.XAML** на следующий XAML-код, а затем сохраните файл:

    ```xml
    <UserControl
        x:Class="MyUWPApp.MyUserControl"
        xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
        xmlns:local="using:MyUWPApp"
        xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
        xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
        mc:Ignorable="d">

        <StackPanel HorizontalAlignment="Center" Spacing="10" 
                    Padding="20" VerticalAlignment="Center">
            <TextBlock HorizontalAlignment="Center" TextWrapping="Wrap" 
                           Text="Hello from XAML Islands" FontSize="30" />
            <TextBlock HorizontalAlignment="Center" Margin="15" TextWrapping="Wrap"
                           Text="😍❤💋🌹🎉😎�🐱‍👤" FontSize="16" />
            <Button HorizontalAlignment="Center" 
                    x:Name="Button" Click="ClickHandler">Click Me</Button>
        </StackPanel>
    </UserControl>
    ```

### <a name="define-a-xamlapplication-class"></a>Определение класса XamlApplication

Затем замените класс **App** по умолчанию в проекте **MyUWPApp** производным от класса [Microsoft.Toolkit.Win32.UI.XamlHost.XamlApplication](https://github.com/windows-toolkit/Microsoft.Toolkit.Win32/tree/master/Microsoft.Toolkit.Win32.UI.XamlApplication), предоставляемым в наборе средств сообщества Windows. Этот класс поддерживает интерфейс [IXamlMetadaraProvider](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Markup.IXamlMetadataProvider), который позволяет приложению обнаруживать и загружать метаданные для настраиваемых элементов управления UWP XAML в сборках в текущем каталоге приложения во время выполнения. Этот класс также инициализирует платформу UWP XAML для текущего потока. Позднее в рамках этого пошагового руководства вы обновите проект классического приложения, чтобы создать экзепляр этого класса.

  > [!NOTE]
  > Каждое решение, использующее XAML Islands, может содержать только один проект, определяющий объект `XamlApplication`. Все настраиваемые элементы управления XAML UWP в приложении используют один и тот же объект `XamlApplication`. 

1. В **обозревателе решений** щелкните правой кнопкой мыши файл **MainPage.xaml** в проекте **MyUWPApp**. Выберите команду **Убрать**, а затем **Удалить**, чтобы удалить этот файл из проекта.
2. В проекте **MyUWPApp** разверните файл **App.xaml**.
3. Замените содержимое файлов **App.xaml**, **App.cpp**, **App.h** и **App.idl** кодом, указанным ниже.

    * **App.xaml**:

        ```xml
        <Toolkit:XamlApplication
            x:Class="MyUWPApp.App"
            xmlns:Toolkit="using:Microsoft.Toolkit.Win32.UI.XamlHost"
            xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
            xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
            xmlns:local="using:MyUWPApp">
        </Toolkit:XamlApplication>
        ```

    * **App.idl**:

        ```IDL
        namespace MyUWPApp
        {
             [default_interface]
             runtimeclass App : Microsoft.Toolkit.Win32.UI.XamlHost.XamlApplication
             {
                App();
             }
        }
        ```

    * **App.h**:

        ```cpp
        #pragma once
        #include "App.g.h"
        #include "App.base.h"
        namespace winrt::MyUWPApp::implementation
        {
            class App : public AppT2<App>
            {
            public:
                App();
                ~App();
            };
        }
        namespace winrt::MyUWPApp::factory_implementation
        {
            class App : public AppT<App, implementation::App>
            {
            };
        }
        ```

    * **App.cpp**:

        ```cpp
        #include "pch.h"
        #include "App.h"
        using namespace winrt;
        using namespace Windows::UI::Xaml;
        namespace winrt::MyUWPApp::implementation
        {
            App::App()
            {
                Initialize();
                AddRef();
                m_inner.as<::IUnknown>()->Release();
            }
            App::~App()
            {
                Close();
            }
        }
        ```

4. Добавьте новый файл заголовка в проект **MyUWPApp** с именем **app.base.h**.
5. Добавьте следующий код в файл **app.base.h**, сохраните файл и закройте его.

    ```cpp
    #pragma once
    namespace winrt::MyUWPApp::implementation
    {
        template <typename D, typename... I>
        struct App_baseWithProvider : public App_base<D, ::winrt::Windows::UI::Xaml::Markup::IXamlMetadataProvider>
        {
            using IXamlType = ::winrt::Windows::UI::Xaml::Markup::IXamlType;
            IXamlType GetXamlType(::winrt::Windows::UI::Xaml::Interop::TypeName const& type)
            {
                return AppProvider()->GetXamlType(type);
            }
            IXamlType GetXamlType(::winrt::hstring const& fullName)
            {
                return AppProvider()->GetXamlType(fullName);
            }
            ::winrt::com_array<::winrt::Windows::UI::Xaml::Markup::XmlnsDefinition> GetXmlnsDefinitions()
            {
                return AppProvider()->GetXmlnsDefinitions();
            }
        private:
            bool _contentLoaded{ false };
            std::shared_ptr<XamlMetaDataProvider> _appProvider;
            std::shared_ptr<XamlMetaDataProvider> AppProvider()
            {
                if (!_appProvider)
                {
                    _appProvider = std::make_shared<XamlMetaDataProvider>();
                }
                return _appProvider;
            }
        };
        template <typename D, typename... I>
        using AppT2 = App_baseWithProvider<D, I...>;
    }
    ```

6. Запустите сборку решения и убедитесь, что она проходит успешно.

## <a name="configure-the-desktop-project-to-consume-custom-control-types"></a>Настройка проекта классического приложения для использования типов настраиваемых элементов управления

Прежде чем приложение **MyDesktopWin32App** разместит настраиваемый элемент управления XAML UWP в XAML Island, в приложении необходимо настроить использование типов настраиваемых элементов управления из проекта **MyUWPApp**. Это можно сделать двумя способами, и после ознакомления с этим пошаговым руководством вы можете выбрать любой из них.

### <a name="option-1-package-the-app-using-msix"></a>Вариант 1. Упаковка приложения с использованием MSIX

Вы можете упаковать приложение в [пакет MSIX](https://docs.microsoft.com/windows/msix) для развертывания. MSIX — это современная технология упаковки приложений для Windows. В ее основе лежат технологии установки MSI, APPX, App-V и ClickOnce.

1. Добавьте новый [проект упаковки приложений Windows](https://docs.microsoft.com/windows/msix/desktop/desktop-to-uwp-packaging-dot-net) в свое решение. При создании проекта назовите его **MyDesktopWin32Project** и выберите **Windows 10 версии 1903 (10.0; сборка 18362)** в качестве **целевой** и **минимальной** версии.

2. В проекте упаковки щелкните правой кнопкой мыши узел **Приложения** и выберите команду **Добавить ссылку**. В списке проектов установите флажок рядом с проектом **MyDesktopWin32App** и нажмите кнопку **ОК**.
    ![Ссылочный проект](images/xaml-islands/xaml-island-cpp-6.png)

> [!NOTE]
> Если вы решили не упаковывать приложение в [пакет MSIX](https://docs.microsoft.com/windows/msix) для развертывания, на компьютерах с запущенными приложениями должна быть установлена [среда выполнения Visual C++](https://support.microsoft.com/en-us/help/2977003/the-latest-supported-visual-c-downloads).

### <a name="option-2-create-an-application-manifest"></a>Вариант 2. Создание манифеста приложения

Можно добавить [манифест](https://docs.microsoft.com/windows/desktop/SbsCs/application-manifests) в приложение.

1. Щелкните правой кнопкой мыши проект **MyDesktopWin32App** и выберите элементы **Добавить** -> **Новый элемент**. 
2. В диалоговом окне **Добавление нового элемента** щелкните **Web** (Веб) в левой области и выберите **XML-файл (.xml)** . 
3. Назовите новый файл **app.manifest** и щелкните **Добавить**.
4. Замените содержимое нового файла XML-кодом, указанным ниже. Этот XML-код регистрирует типы настраиваемых элементов управления в проекте **MyUWPApp**.

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <assembly
     xmlns="urn:schemas-microsoft-com:asm.v1"
     xmlns:asmv3="urn:schemas-microsoft-com:asm.v3"
     manifestVersion="1.0">
      <asmv3:file name="MyUWPApp.dll">
        <activatableClass
            name="MyUWPApp.App"
            threadingModel="both"
            xmlns="urn:schemas-microsoft-com:winrt.v1" />
        <activatableClass
            name="MyUWPApp.XamlMetadataProvider"
            threadingModel="both"
            xmlns="urn:schemas-microsoft-com:winrt.v1" />
        <activatableClass
            name="MyUWPApp.MyUserControl"
            threadingModel="both"
            xmlns="urn:schemas-microsoft-com:winrt.v1" />
      </asmv3:file>
    </assembly>
    ```

## <a name="configure-additional-desktop-project-properties"></a>Настройка дополнительных свойств проекта классического приложения

Далее обновите проект **MyDesktopWin32App**, чтобы определить макрос для дополнительных каталогов включаемых файлов и настроить дополнительные свойства.

1. В **обозревателе решений** щелкните правой кнопкой мыши проект **MyDesktopWin32App** и выберите команду **Выгрузить проект**.

2. Щелкните правой кнопкой мыши **MyDesktopWin32App (Unloaded)** (MyDesktopWin32App (Выгружен)) и выберите команду **Edit MyDesktopWin32App.vcxproj** (Изменить MyDesktopWin32App.vcxproj).

3. Добавьте XML-код, указанный ниже, напрямую перед закрывающим тегом `</Project>` в конце файла. Затем сохраните и закройте файл.

    ```xml
      <!-- Configure these for your UWP project -->
      <PropertyGroup>
        <AppProjectName>MyUWPApp</AppProjectName>
      </PropertyGroup>
      <PropertyGroup>
        <AppIncludeDirectories>$(SolutionDir)\obj\$(Platform)\$(Configuration)\$(AppProjectName)\;$(SolutionDir)\obj\$(Platform)\$(Configuration)\$(AppProjectName)\Generated Files\;</AppIncludeDirectories>
      </PropertyGroup>
      <ItemGroup>
        <ProjectReference Include="..\$(AppProjectName)\$(AppProjectName).vcxproj" />
      </ItemGroup>
      <!-- End Section-->
    ```

4. В **обозревателе решений** щелкните правой кнопкой мыши **MyDesktopWin32App (Unloaded)** (MyDesktopWin32App (Выгружен)) и выберите команду **Перезагрузить проект**.

5. Щелкните правой кнопкой мыши **MyDesktopWin32App**, выберите пункт **Свойства** и щелкните узел **C/C++** на левой панели. Убедитесь, что макрос **Дополнительные каталоги включаемых файлов** определен на основе измененного на предыдущем шаге файла проекта.

    ![Параметры проекта C/C++](images/xaml-islands/xaml-island-cpp-7.png)

6. В диалоговом окне **Страницы свойств** разверните **Инструмент манифеста** -> **Вход и выход**. Задайте для свойства **Поддержка DPI** значение **Поддержка высокого DPI по мониторам**. Если это свойство не задано, может возникнуть ошибка конфигурации манифеста в некоторых сценариях с высоким количеством точек на дюйм.

    ![Параметры проекта C/C++](images/xaml-islands/xaml-island-cpp-8.png)

## <a name="host-the-custom-uwp-xaml-control-in-the-desktop-project"></a>Размещение настраиваемого элемента управления XAML UWP в проекте классического приложения

Теперь можно добавить код в проект **MyDesktopWin32App** для размещения настраиваемого элемента управления XAML UWP, определенного ранее в проекте **MyUWPApp**.

1. В проекте **MyDesktopWin32App** откройте файл **framework.h** и закомментируйте строку кода, указанную ниже. По завершении сохраните файл.

    ```cpp
    #define WIN32_LEAN_AND_MEAN
    ```

2. Откройте файл **MyDesktopWin32App.h** и замените содержимое этого файла кодом, указанным ниже, чтобы создать ссылки на необходимые заголовочные файлы C++/WinRT. По завершении сохраните файл.

    ```cpp
    #pragma once

    #include "resource.h"
    #include <winrt/Windows.Foundation.Collections.h>
    #include <winrt/Windows.system.h>
    #include <winrt/windows.ui.xaml.hosting.h>
    #include <windows.ui.xaml.hosting.desktopwindowxamlsource.h>
    #include <winrt/windows.ui.xaml.controls.h>
    #include <winrt/Windows.ui.xaml.media.h>
    #include <winrt/Windows.UI.Core.h>
    #include <winrt/MyUWPApp.h>

    using namespace winrt;
    using namespace Windows::UI;
    using namespace Windows::UI::Composition;
    using namespace Windows::UI::Xaml::Hosting;
    using namespace Windows::Foundation::Numerics;
    using namespace Windows::UI::Xaml::Controls;
    ```

3. Откройте файл **MyDesktopWin32App.cpp** и добавьте следующий код в раздел `Global Variables:`:

    ```cpp
    winrt::MyUWPApp::App hostApp{ nullptr };
    winrt::Windows::UI::Xaml::Hosting::DesktopWindowXamlSource _desktopWindowXamlSource{ nullptr };
    winrt::MyUWPApp::MyUserControl _myUserControl{ nullptr };
    ```

4. В том же файле добавьте следующий код в раздел `Forward declarations of functions included in this code module:`:

    ```cpp
    void AdjustLayout(HWND);
    ```

5. В том же файле добавьте следующий код сразу после комментария `TODO: Place code here.` в функции `wWinMain`:

    ```cpp
    // TODO: Place code here.
    winrt::init_apartment(winrt::apartment_type::single_threaded);
    hostApp = winrt::MyUWPApp::App{};
    _desktopWindowXamlSource = winrt::Windows::UI::Xaml::Hosting::DesktopWindowXamlSource{};
    ```

6. В том же файле замените функцию `InitInstance` по умолчанию на следующий код:

    ```cpp
    BOOL InitInstance(HINSTANCE hInstance, int nCmdShow)
    {
        hInst = hInstance; // Store instance handle in our global variable

        HWND hWnd = CreateWindowW(szWindowClass, szTitle, WS_OVERLAPPEDWINDOW,
            CW_USEDEFAULT, 0, CW_USEDEFAULT, 0, nullptr, nullptr, hInstance, nullptr);

        if (!hWnd)
        {
            return FALSE;
        }

        // Begin XAML Islands walkthrough code.
        if (_desktopWindowXamlSource != nullptr)
        {
            auto interop = _desktopWindowXamlSource.as<IDesktopWindowXamlSourceNative>();
            check_hresult(interop->AttachToWindow(hWnd));
            HWND hWndXamlIsland = nullptr;
            interop->get_WindowHandle(&hWndXamlIsland);
            RECT windowRect;
            ::GetWindowRect(hWnd, &windowRect);
            ::SetWindowPos(hWndXamlIsland, NULL, 0, 0, windowRect.right - windowRect.left, windowRect.bottom - windowRect.top, SWP_SHOWWINDOW);
            _myUserControl = winrt::MyUWPApp::MyUserControl();
            _desktopWindowXamlSource.Content(_myUserControl);
        }
        // End XAML Islands walkthrough code.

        ShowWindow(hWnd, nCmdShow);
        UpdateWindow(hWnd);

        return TRUE;
    }
    ```

7. В том же файле добавьте следующую новую функцию в конец файла:

    ```cpp
    void AdjustLayout(HWND hWnd)
    {
        if (_desktopWindowXamlSource != nullptr)
        {
            auto interop = _desktopWindowXamlSource.as<IDesktopWindowXamlSourceNative>();
            HWND xamlHostHwnd = NULL;
            check_hresult(interop->get_WindowHandle(&xamlHostHwnd));
            RECT windowRect;
            ::GetWindowRect(hWnd, &windowRect);
            ::SetWindowPos(xamlHostHwnd, NULL, 0, 0, windowRect.right - windowRect.left, windowRect.bottom - windowRect.top, SWP_SHOWWINDOW);
        }
    }
    ```

8. В том же файле найдите функцию `WndProc`. Замените обработчик `WM_DESTROY` по умолчанию в операторе switch на следующий код:

    ```cpp
    case WM_DESTROY:
        PostQuitMessage(0);
        if (_desktopWindowXamlSource != nullptr)
        {
            _desktopWindowXamlSource.Close();
            _desktopWindowXamlSource = nullptr;
        }
        break;
    case WM_SIZE:
        AdjustLayout(hWnd);
        break;
    ```

9. Сохраните файл.
10. Запустите сборку решения и убедитесь, что она проходит успешно.

## <a name="test-the-app"></a>Тестирование приложения

Запустите решение и убедитесь, что **MyDesktopWin32App** открывается в следующем окне:

![Приложение MyDesktopWin32App](images/xaml-islands/xaml-island-cpp-9.png)

## <a name="next-steps"></a>Дальнейшие действия

Многим классическим приложениям, в которых используется XAML Islands, потребуется обрабатывать дополнительные сценарии, чтобы обеспечить бесперебойную работу пользовательского интерфейса. Например, в классических приложениях может потребоваться обрабатывать ввод с клавиатуры в XAML Islands, выполнять переход между XAML Islands и другими элементами пользовательского интерфейса, а также изменять макет.

Дополнительные сведения об обработке этих сценариев и связанные примеры кода см. в статье [Расширенные сценарии для XAML Islands в приложениях Win32 на C++](advanced-scenarios-xaml-islands-cpp.md).

## <a name="related-topics"></a>Связанные темы

* [Элементы управления UWP XAML в классических приложениях (XAML Islands)](xaml-islands.md)
* [Использование API размещения XAML платформы UWP в приложении Win32 на C++](using-the-xaml-hosting-api.md)
* [Размещение стандартного элемента управления UWP в приложении Win32 на C++](host-standard-control-with-xaml-islands-cpp.md)
* [Расширенные сценарии для XAML Islands в приложениях Win32 на C++](advanced-scenarios-xaml-islands-cpp.md)
* [Примеры кода для XAML Islands](https://github.com/microsoft/Xaml-Islands-Samples)
