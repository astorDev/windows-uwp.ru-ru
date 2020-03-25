---
description: В этой статье показано, как разместить пользовательский элемент управления UWP в C++ приложении Win32 с помощью API размещения XAML.
title: Размещение пользовательского элемента управления UWP в приложении C++ Win32 с помощью API размещения XAML
ms.date: 03/23/2020
ms.topic: article
keywords: Windows 10, UWP, C++, Win32, острова XAML, пользовательские элементы управления, пользовательские элементы управления, элементы управления ведущего приложения
ms.author: mcleans
author: mcleanbyron
ms.localizationpriority: medium
ms.custom: 19H1
ms.openlocfilehash: 2f34c9c56cf9db5dfcfd702b97f2d34273b86e6a
ms.sourcegitcommit: c660def841abc742600fbcf6ed98e1f4f7beb8cc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/24/2020
ms.locfileid: "80226318"
---
# <a name="host-a-custom-uwp-control-in-a-c-win32-app"></a>Размещение пользовательского элемента управления UWP в приложении C++ Win32

В этой статье показано, как использовать [API хостинга UWP XAML](using-the-xaml-hosting-api.md) для размещения пользовательского элемента управления XAML UWP в новом C++ приложении Win32. При наличии существующего C++ проекта приложения Win32 можно адаптировать эти шаги и примеры кода для проекта.

Чтобы разместить пользовательский элемент управления XAML UWP, в рамках этого пошагового руководства вы создадите следующие проекты и компоненты:

* **Проект классического приложения Windows**. Этот проект реализует собственное C++ классическое приложение Win32. Вы добавите код в этот проект, использующий API размещения UWP XAML для размещения пользовательского элемента управления XAML UWP.

* **Проект приложения UWP (C++/WinRT)** . Этот проект реализует собственный элемент управления XAML UWP. Он также реализует корневой поставщик метаданных для загрузки метаданных пользовательских типов универсального кода пользователя UWP в проекте.

## <a name="requirements"></a>Требования

* Visual Studio 2019 версии 16.4.3 или более поздней.
* Windows 10, SDK версии 1903 (версия 10.0.18362) или более поздняя версия.
* [Расширение Visual Studio/WinRT (VSIX), установленное с помощью Visual Studio. C++](https://marketplace.visualstudio.com/items?itemName=CppWinRTTeam.cppwinrt101804264) C++/WinRT — это полностью стандартная проекция языка C++17 для API среды выполнения Windows (WinRT), реализованная как библиотека на основе файлов заголовков и предназначенная для предоставления вам первоклассного доступа к современным интерфейсам API Windows. Дополнительные сведения см. в разделе [ C++/WinRT](https://docs.microsoft.com/windows/uwp/cpp-and-winrt-apis/).

## <a name="create-a-desktop-application-project"></a>Создание проекта классического приложения

1. В Visual Studio создайте новый проект **классического приложения Windows** с именем **MyDesktopWin32App**. Этот шаблон проекта доступен в фильтрах **C++** проектов, **Windows**и **Desktop** .

2. В **Обозреватель решений**щелкните правой кнопкой мыши узел решения, выберите пункт **перенацелить решение**, выберите **10.0.18362.0** или более поздний выпуск пакета SDK и нажмите кнопку **ОК**.

3. Установите пакет NuGet [Microsoft. Windows. кппвинрт](https://www.nuget.org/packages/Microsoft.Windows.CppWinRT/) , чтобы включить поддержку [ C++/WinRT](/windows/uwp/cpp-and-winrt-apis) в проекте:

    1. Щелкните правой кнопкой мыши проект **MyDesktopWin32App** в **Обозреватель решений** и выберите пункт **Управление пакетами NuGet**.
    2. Перейдите на вкладку **Обзор** , найдите пакет [Microsoft. Windows. кппвинрт](https://www.nuget.org/packages/Microsoft.Windows.CppWinRT/) и установите последнюю версию этого пакета.

4. В окне **Управление пакетами NuGet** установите следующие дополнительные пакеты NuGet:

    * [Microsoft. Toolkit. Win32. UI. SDK](https://www.nuget.org/packages/Microsoft.Toolkit.Win32.UI.SDK) (Version v 6.0.0 или более поздней версии). Этот пакет предоставляет несколько ресурсов для сборки и времени выполнения, которые позволяют работать в приложении по работе с XAML.
    * [Microsoft. Toolkit. Win32. UI. ксамлаппликатион](https://www.nuget.org/packages/Microsoft.Toolkit.Win32.UI.XamlApplication) (Version v 6.0.0 или более поздней версии).
    * [Microsoft. вкртфорвардерс. 140](https://www.nuget.org/packages/Microsoft.VCRTForwarders.140).

5. Создайте решение и убедитесь, что оно успешно построено.

## <a name="create-a-uwp-app-project"></a>Создание проекта приложения UWP

Затем добавьте в решение проект приложения **UWP (C++/WinRT)** и внесите некоторые изменения в конфигурацию этого проекта. Далее в этом пошаговом руководстве вы добавите код в этот проект, чтобы реализовать пользовательский элемент управления XAML UWP и определить экземпляр класса [Microsoft. Toolkit. Win32. UI. ксамлхост. ксамлаппликатион](https://github.com/windows-toolkit/Microsoft.Toolkit.Win32/tree/master/Microsoft.Toolkit.Win32.UI.XamlApplication) , предоставляемый набором средств сообщества Windows. Приложение будет использовать этот класс в качестве корневого поставщика метаданных для загрузки метаданных для настраиваемых типов XAML UWP.

1. В **Обозреватель решений**щелкните правой кнопкой мыши узел решения и выберите **Добавить** -> **Новый проект**.

2. Добавьте в решение **пустой проектC++приложения (/WinRT)** . Назовите проект **мювпапп** и убедитесь, что для целевой версии и минимальной версии задано значение **Windows 10, 1903** или более поздней версии.

3. Установите пакет NuGet [Microsoft. Toolkit. Win32. UI. ксамлаппликатион](https://www.nuget.org/packages/Microsoft.Toolkit.Win32.UI.XamlApplication) в проект **мювпапп** :

    1. Щелкните правой кнопкой мыши проект **мювпапп** и выберите пункт **Управление пакетами NuGet**.
    2. Перейдите на вкладку **Обзор** , найдите пакет [Microsoft. Toolkit. Win32. UI. ксамлаппликатион](https://www.nuget.org/packages/Microsoft.Toolkit.Win32.UI.XamlApplication) и установите версию 6.0.0 или более поздней версии этого пакета.

4. Щелкните правой кнопкой мыши узел **мювпапп** и выберите пункт **свойства**. На странице **Общие свойства** ->  **C++/WinRT** задайте следующие свойства и нажмите кнопку **Применить**.

    * Установите **уровень детализации** " **нормальный**".
    * Задайте для параметра **оптимизировано** значение **нет**.

    По завершении страница свойств будет выглядеть следующим образом.

    ![C++Свойства проекта/WinRT](images/xaml-islands/xaml-island-cpp-1.png)

5. На странице **Свойства конфигурации** -> **Общие** в окне Свойства задайте для параметра **тип конфигурации** значение **Динамическая библиотека (. dll)** , а затем нажмите кнопку **ОК** , чтобы закрыть окно Свойства.

    ![Общие свойства проекта](images/xaml-islands/xaml-island-cpp-2.png)

6. Добавьте заполнитель исполняемого файла в проект **мювпапп** . Этот исполняемый файл заполнителя необходим для создания необходимых файлов проекта в Visual Studio и правильной сборки проекта.

    1. В **Обозреватель решений**щелкните правой кнопкой мыши узел проекта **Мювпапп** и выберите **добавить** -> **новый элемент**.
    2. В диалоговом окне **Добавление нового элемента** выберите **служебная программа** на левой странице, а затем выберите **текстовый файл (. txt)** . Введите имя **PlaceHolder. exe** и нажмите кнопку **Добавить**.
      ![добавить текстовый файл](images/xaml-islands/xaml-island-cpp-3.png)
    3. В **Обозреватель решений**выберите файл **заполнителя. exe** . В окне **Свойства** убедитесь, что свойство **Content** имеет значение **true**.
    4. В **Обозреватель решений**щелкните правой кнопкой мыши файл **Package. appxmanifest** в проекте **Мювпапп** , выберите команду **Открыть с помощью**и выберите **Редактор XML (текстовый)** и нажмите кнопку **ОК**.
    5. Найдите элемент **&gt;приложения&lt;** и измените атрибут **Executable** на value `placeholder.exe`. По завершении элемент **&gt;приложения&lt;** должен выглядеть примерно так.

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

    6. Сохраните и закройте файл **Package. appxmanifest** .

7. В **Обозреватель решений**щелкните правой кнопкой мыши узел **мювпапп** и выберите команду **Выгрузить проект**.
8. Щелкните правой кнопкой мыши узел **мювпапп** и выберите **изменить мювпапп. vcxproj**.
9. Найдите элемент `<Import Project="$(VCTargetsPath)\Microsoft.Cpp.Default.props" />` и замените его следующим XML-кодом. Этот XML-файл добавляет несколько новых свойств непосредственно перед элементом.

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
11. В **Обозреватель решений**щелкните правой кнопкой мыши узел **мювпапп** и выберите **Перезагрузить проект**.

## <a name="configure-the-solution"></a>Настройка решения

В этом разделе вы обновите решение, содержащее оба проекта, чтобы настроить зависимости проекта и свойства сборки, необходимые для правильной сборки проектов.

1. В **Обозреватель решений**щелкните правой кнопкой мыши узел решения и добавьте новый XML-файл с именем **Solution. props**.
2. Добавьте следующий XML-код в файл **решения. props** .

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

3. В меню **вид** выберите **Диспетчер свойств** (в зависимости от конфигурации это может быть **представление** -> **другие окна**).
4. В окне **Диспетчер свойств** щелкните правой кнопкой мыши **MyDesktopWin32App** и выберите пункт **Добавить существующую страницу свойств**. Перейдите к только что добавленному файлу **Solution. props** и нажмите кнопку **Открыть**.
5. Повторите предыдущий шаг, чтобы добавить файл **решения. props** в проект **мювпапп** в окне **Диспетчер свойств** .
6. Закройте окно **Диспетчер свойств** .
7. Убедитесь, что изменения страницы свойств были сохранены правильно. В **Обозреватель решений**щелкните правой кнопкой мыши проект **MyDesktopWin32App** и выберите пункт **Свойства**. Щелкните **Свойства конфигурации** -> **женнерал**и убедитесь, что свойства **выходной каталог** и **промежуточный каталог** имеют значения, добавленные в файл **решения. props** . Также можно подтвердить это же значение для проекта **мювпапп** .
    ![свойства проекта](images/xaml-islands/xaml-island-cpp-4.png)

8. В **Обозреватель решений**щелкните правой кнопкой мыши узел решения и выберите **зависимости проекта**. В раскрывающемся списке **проекты** убедитесь, что выбран параметр **MyDesktopWin32App** , и выберите **мювпапп** из списка **зависит** от.
    ![зависимостей проекта](images/xaml-islands/xaml-island-cpp-5.png)

9. Нажмите кнопку **ОК**.

## <a name="add-code-to-the-uwp-app-project"></a>Добавление кода в проект приложения UWP

Теперь все готово к добавлению кода в проект **мювпапп** для выполнения следующих задач:

* Реализуйте собственный элемент управления XAML UWP. Далее в этом пошаговом руководстве вы добавите код, в котором размещается этот элемент управления, в проекте **MyDesktopWin32App** .
* Определите тип, производный от класса [Microsoft. Toolkit. Win32. UI. ксамлхост. ксамлаппликатион](https://github.com/windows-toolkit/Microsoft.Toolkit.Win32/tree/master/Microsoft.Toolkit.Win32.UI.XamlApplication) в наборе средств сообщества Windows. Этот класс выступает в качестве корневого поставщика метаданных для загрузки метаданных для настраиваемых типов XAML UWP.

### <a name="define-a-custom-uwp-xaml-control"></a>Определение пользовательского управляющего элемента управления UWP XAML

1. В **Обозреватель решений**щелкните правой кнопкой мыши **Мювпапп** и выберите **добавить** -> **новый элемент**. Выберите **визуальный C++**  узел в левой области, выберите **пустой пользовательский элемент управления (C++/WinRT)** , назовите его **MyUserControl**и нажмите кнопку **Добавить**.
2. В редакторе XAML замените содержимое файла **MyUserControl. XAML** следующим кодом XAML, а затем сохраните файл.

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

### <a name="define-a-xamlapplication-class"></a>Определение класса Ксамлаппликатион

Затем измените класс **приложения** по умолчанию в проекте **мювпапп** , чтобы он был производным от класса [Microsoft. Toolkit. Win32. UI. ксамлхост. ксамлаппликатион](https://github.com/windows-toolkit/Microsoft.Toolkit.Win32/tree/master/Microsoft.Toolkit.Win32.UI.XamlApplication) , предоставляемого набором средств сообщества Windows. Далее в этом пошаговом руководстве вы обновите проект для настольного компьютера, чтобы создать экземпляр этого класса в качестве корневого поставщика метаданных для загрузки метаданных для настраиваемых типов XAML UWP.

  > [!NOTE]
  > Каждое решение, использующее острова XAML, может содержать только один проект, определяющий объект `XamlApplication`. Все пользовательские элементы управления XAML UWP в приложении совместно используют один и тот же объект `XamlApplication`. 

1. В **Обозреватель решений**щелкните правой кнопкой мыши файл **MainPage. XAML** в проекте **мювпапп** . Нажмите кнопку **Удалить** , а затем **Удалить** , чтобы удалить этот файл пермаментли из проекта.
2. В проекте **мювпапп** разверните файл **app. XAML** .
3. Замените содержимое файлов **app. XAML**, **app. cpp**, **app. h**и файла **app. idl** следующим кодом.

    * **App. XAML**:

        ```xml
        <Toolkit:XamlApplication
            x:Class="MyUWPApp.App"
            xmlns:Toolkit="using:Microsoft.Toolkit.Win32.UI.XamlHost"
            xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
            xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
            xmlns:local="using:MyUWPApp">
        </Toolkit:XamlApplication>
        ```

    * **App. idl**:

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

    * **App. h**:

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

    * **App. cpp**:

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

4. Добавьте новый файл заголовка в проект **мювпапп** с именем **app. base. h**.
5. Добавьте следующий код в файл **app. base. h** , сохраните файл и закройте его.

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

6. Создайте решение и убедитесь, что оно успешно построено.

## <a name="configure-the-desktop-project-to-consume-custom-control-types"></a>Настройка настольного проекта для использования пользовательских типов элементов управления

Прежде чем приложение **MyDesktopWin32App** сможет разместить пользовательский элемент управления XAML UWP в коде XAML, его необходимо настроить для использования пользовательских типов элементов управления из проекта **мювпапп** . Это можно сделать двумя способами. в этом пошаговом руководстве вы можете выбрать любой из этих вариантов.

### <a name="option-1-package-the-app-using-msix"></a>Вариант 1. Упаковка приложения с помощью MSIX

Вы можете упаковать приложение в [пакет MSIX](https://docs.microsoft.com/windows/msix) для развертывания. MSIX — это современная технология упаковки приложений для Windows, которая основана на сочетании технологий установки MSI, appx, App-V и ClickOnce.

1. Добавьте новый [проект упаковки приложений Windows](https://docs.microsoft.com/windows/msix/desktop/desktop-to-uwp-packaging-dot-net) в решение. При создании проекта назовите его **MyDesktopWin32Project** и выберите **Windows 10, версия 1903 (10,0; Сборка 18362)** для **целевой версии** и **минимальной версии**.

2. В проекте упаковки щелкните правой кнопкой мыши узел **приложения** и выберите команду **Добавить ссылку**. В списке проектов установите флажок рядом с проектом **MyDesktopWin32App** и нажмите кнопку **ОК**.
    ![ссылочный проект](images/xaml-islands/xaml-island-cpp-6.png)

> [!NOTE]
> Если вы решили не упаковать приложение в [пакет MSIX](https://docs.microsoft.com/windows/msix) для развертывания, на компьютерах, где выполняется приложение, должна быть установлена [Среда выполнения C++ Visual](https://support.microsoft.com/en-us/help/2977003/the-latest-supported-visual-c-downloads) .

### <a name="option-2-create-an-application-manifest"></a>Вариант 2. Создание манифеста приложения

В приложение можно добавить [манифест приложения](https://docs.microsoft.com/windows/desktop/SbsCs/application-manifests) .

1. Щелкните правой кнопкой мыши проект **MyDesktopWin32App** и выберите **Добавить** -> **новый элемент**. 
2. В диалоговом окне **Добавление нового элемента** щелкните **веб-сайт** в левой области и выберите **XML-файл (. XML)** . 
3. Назовите новый файл **app. manifest** и нажмите кнопку **Добавить**.
4. Замените содержимое нового файла следующим XML-кодом. Этот XML-код регистрирует пользовательские типы элементов управления в проекте **мювпапп** .

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

## <a name="configure-additional-desktop-project-properties"></a>Настройка дополнительных свойств проекта рабочего стола

Затем обновите проект **MyDesktopWin32App** , чтобы определить макрос для дополнительных каталогов включения и настроить дополнительные свойства.

1. В **Обозреватель решений**щелкните правой кнопкой мыши проект **MyDesktopWin32App** и выберите команду **Выгрузить проект**.

2. Щелкните правой кнопкой мыши **MyDesktopWin32App (выгружено)** и выберите **изменить MyDesktopWin32App. vcxproj**.

3. Добавьте следующий XML непосредственно перед закрывающим тегом `</Project>` в конце файла. Затем сохраните и закройте файл.

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

4. В **Обозреватель решений**щелкните правой кнопкой мыши **MyDesktopWin32App (выгружено)** и выберите **Перезагрузить проект**.

5. Щелкните правой кнопкой мыши **MyDesktopWin32App**, выберите пункт **свойства**и щелкните узел **CC++ /** в левой области. Убедитесь, что макрос **дополнительных включаемых каталогов** был определен из файла проекта, сделанного на предыдущем шаге.
    ![параметры вC++ C/Project](images/xaml-islands/xaml-island-cpp-7.png)

6. В диалоговом окне **страницы свойств** разверните узел **средство манифеста** -> **входные и выходные данные**. Задайте для свойства **осведомленность** о соотношении dpi значение **на монитор с высоким DPI**. Если это свойство не задано, может возникнуть ошибка конфигурации манифеста в некоторых сценариях с высоким разрешением.
    ![параметры вC++ C/Project](images/xaml-islands/xaml-island-cpp-8.png)

## <a name="host-the-custom-uwp-xaml-control-in-the-desktop-project"></a>Размещение пользовательского элемента управления XAML UWP в проекте для настольных систем

Наконец, вы готовы добавить код в проект **MyDesktopWin32App** для размещения настраиваемого элемента управления XAML UWP, определенного ранее в проекте **мювпапп** .

1. В проекте **MyDesktopWin32App** откройте файл **Framework. h** и закомментируйте следующую строку кода. По завершении сохраните файл.

    ```cpp
    #define WIN32_LEAN_AND_MEAN
    ```

2. Откройте файл **MyDesktopWin32App. h** и замените содержимое этого файла следующим кодом для ссылки на необходимые C++файлы заголовков/WinRT. По завершении сохраните файл.

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

3. Откройте файл **MyDesktopWin32App. cpp** и добавьте следующий код в раздел `Global Variables:`.

    ```cpp
    winrt::MyUWPApp::App hostApp{ nullptr };
    winrt::Windows::UI::Xaml::Hosting::DesktopWindowXamlSource _desktopWindowXamlSource{ nullptr };
    winrt::MyUWPApp::MyUserControl _myUserControl{ nullptr };
    ```

4. В том же файле добавьте следующий код в раздел `Forward declarations of functions included in this code module:`.

    ```cpp
    void AdjustLayout(HWND);
    ```

5. В том же файле добавьте следующий код сразу после комментария `TODO: Place code here.` в функции `wWinMain`.

    ```cpp
    // TODO: Place code here.
    winrt::init_apartment(winrt::apartment_type::single_threaded);
    hostApp = winrt::MyUWPApp::App{};
    _desktopWindowXamlSource = winrt::Windows::UI::Xaml::Hosting::DesktopWindowXamlSource{};
    ```

6. В том же файле замените функцию `InitInstance` по умолчанию на следующий код.

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

7. В том же файле добавьте следующую новую функцию в конец файла.

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

8. В том же файле выберите функцию `WndProc`. Замените обработчик `WM_DESTROY` по умолчанию в операторе switch на следующий код.

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
10. Создайте решение и убедитесь, что оно успешно построено.

## <a name="test-the-app"></a>Тестирование приложения

Запустите решение и убедитесь, что **MyDesktopWin32App** открывается в следующем окне.

![Приложение MyDesktopWin32App](images/xaml-islands/xaml-island-cpp-9.png)

## <a name="next-steps"></a>Следующие шаги

Многие настольные приложения, на которых размещаются острова XAML, должны будут поддерживать дополнительные сценарии, чтобы обеспечить плавное взаимодействие с пользователем. Например, для настольных приложений может потребоваться управлять вводом с клавиатуры в островах XAML, переходом между островами XAML и другими элементами пользовательского интерфейса, а также изменениями макета.

Дополнительные сведения об обработке этих сценариев и указателей на связанные образцы кода см. [в разделе Расширенные сценарии для островов C++ XAML в приложениях Win32](advanced-scenarios-xaml-islands-cpp.md).

## <a name="related-topics"></a>Связанные разделы

* [Размещение элементов управления XAML UWP в классических приложениях (острова XAML)](xaml-islands.md)
* [Использование API размещения UWP XAML в приложении C++ Win32](using-the-xaml-hosting-api.md)
* [Размещение стандартного элемента управления UWP в приложении C++ Win32](host-standard-control-with-xaml-islands-cpp.md)
* [Расширенные сценарии для островов XAML в C++ приложениях Win32](advanced-scenarios-xaml-islands-cpp.md)
* [Примеры кода островов XAML](https://github.com/microsoft/Xaml-Islands-Samples)
