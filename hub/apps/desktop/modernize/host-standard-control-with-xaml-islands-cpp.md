---
description: Эта статья содержит сведения о размещении стандартного элемента управления UWP в приложении Win32 на C++ с помощью API размещения XAML.
title: Размещение стандартного элемента управления UWP в приложении Win32 на C++ с помощью XAML Islands.
ms.date: 03/23/2020
ms.topic: article
keywords: Windows 10, UWP, CPP, Win32, XAML Islands, заключенные в оболочку элементы управления, стандартные элементы управления
ms.author: mcleans
author: mcleanbyron
ms.localizationpriority: medium
ms.custom: 19H1
ms.openlocfilehash: 08308c7bca3cd7f39b08c836e43d791a3fda048f
ms.sourcegitcommit: c660def841abc742600fbcf6ed98e1f4f7beb8cc
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/24/2020
ms.locfileid: "80226278"
---
# <a name="host-a-standard-uwp-control-in-a-c-win32-app"></a>Размещение стандартного элемента управления UWP в приложении Win32 на C++

Эта статья содержит сведения о размещении стандартного элемента управления UWP (предоставляемого в пакете Windows SDK) в новом приложении Win32 на C++ с помощью [API размещения XAML UWP](using-the-xaml-hosting-api.md). Этот код основан на [простом примере XAML Island](https://github.com/microsoft/Xaml-Islands-Samples/tree/master/Standalone_Samples/CppWinRT_Basic_Win32App), и в этом разделе показаны наиболее важные части кода. Если у вас есть проект приложения Win32 на C++, вы можете адаптировать под него эти шаги и примеры кода.

> [!NOTE]
> Представленный в этой статье сценарий не поддерживает прямое редактирование разметки XAML для элементов управления UWP, размещенных в приложении. Изменение внешнего вида и поведения размещенных элементов управления UWP в этом сценарии возможно только с помощью кода. См. инструкции по прямому изменению разметки XAML размещенных элементов управления UWP в руководстве по [размещению настраиваемого элемента управления UWP в приложении Win32 на C++](host-custom-control-with-xaml-islands-cpp.md).

## <a name="create-a-desktop-application-project"></a>Создание проекта классического приложения

1. В Visual Studio 2019 с установленным пакетом SDK для Windows 10 версии 1903 (версия 10.0.18362) или более поздней версии создайте проект **Классическое приложение Windows** и присвойте ему имя **MyDesktopWin32App**. Этот тип проекта можно получить, используя фильтры проекта **C++** , **Windows** и **Рабочий стол**.

2. В **обозревателе решений** щелкните правой кнопкой мыши узел решения, выберите команду **Изменить целевую платформу решения**, выберите **10.0.18362.0** или более позднюю версию пакета SDK, а затем нажмите кнопку **ОК**.

3. Установите пакет NuGet [Microsoft.Windows.CppWinRT](https://www.nuget.org/packages/Microsoft.Windows.CppWinRT/), чтобы включить поддержку [C++/WinRT](/windows/uwp/cpp-and-winrt-apis) в проекте.

    1. В **обозревателе решений** щелкните правой кнопкой мыши нужный проект и выберите пункт **Управление пакетами NuGet**.
    2. Перейдите на вкладку **Обзор**, найдите пакет [Microsoft.Windows.CppWinRT](https://www.nuget.org/packages/Microsoft.Windows.CppWinRT/) и установите последнюю версию этого пакета.

    > [!NOTE]
    > Для новых проектов можно также установить расширение [C++/WinRT Visual Studio (VSIX)](https://marketplace.visualstudio.com/items?itemName=CppWinRTTeam.cppwinrt101804264) и использовать один из шаблонов проектов C++/WinRT, входящих в это расширение. Дополнительные сведения см. в [этой статье](/windows/uwp/cpp-and-winrt-apis/intro-to-using-cpp-with-winrt#visual-studio-support-for-cwinrt-xaml-the-vsix-extension-and-the-nuget-package).

4. Установите пакет NuGet [Microsoft.Toolkit.Win32.UI.SDK](https://www.nuget.org/packages/Microsoft.Toolkit.Win32.UI.SDK), выполнив следующие действия:

    1. Убедитесь, что в окне **Диспетчер пакетов NuGet** выбран параметр **Включить предварительные выпуски**.
    2. Перейдите на вкладку **Обзор**, найдите пакет **Microsoft.Toolkit.Win32.UI.SDK** и установите версию не ниже 6.0.0. Этот пакет включает несколько ресурсов сборки и времени выполнения, которые позволяют XAML Islands работать в приложении.

5. Задайте значение `maxVersionTested` в [манифесте приложения](https://docs.microsoft.com/windows/desktop/SbsCs/application-manifests), чтобы включить совместимость приложения с Windows 10 версии 1903 или более поздней.

    1. Если в проекте еще нет манифеста приложения, добавьте в проект новый XML-файл и присвойте ему имя **app.manifest**.
    2. Включите в манифест приложения элемент **compatibility** и дочерние элементы, показанные в следующем примере. Замените атрибут **Id** в элементе **maxVersionTested** номером версии Windows 10, для которой предназначено приложение (это может быть Windows 10 версии 1903 или более поздняя версия).

        ```xml
        <?xml version="1.0" encoding="UTF-8"?>
        <assembly xmlns="urn:schemas-microsoft-com:asm.v1" manifestVersion="1.0">
            <compatibility xmlns="urn:schemas-microsoft-com:compatibility.v1">
                <application>
                    <!-- Windows 10 -->
                    <maxversiontested Id="10.0.18362.0"/>
                    <supportedOS Id="{8e0f7a12-bfb3-4fe8-b9a5-48fd50a15a9a}" />
                </application>
            </compatibility>
        </assembly>
        ```

## <a name="use-the-xaml-hosting-api-to-host-a-uwp-control"></a>Использование API размещения XAML для размещения элемента управления UWP

Базовый процесс использования API размещения XAML для размещения элемента управления UWP включает следующие шаги:

1. Инициализируйте платформу XAML UWP для текущего потока до того, как приложение создаст любой из размещаемых объектов [Windows.UI.Xaml.UIElement](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement). Это можно сделать разными способами в зависимости от того, когда вы будете создавать объект [DesktopWindowXamlSource](https://docs.microsoft.com/uwp/api/windows.ui.xaml.hosting.desktopwindowxamlsource) для размещения элементов управления.

    * Если приложение создаст объект **DesktopWindowXamlSource** раньше, чем любой из размещаемых объектов **Windows.UI.Xaml.UIElement**, инициализация платформы будет выполнена автоматически при создании экземпляра объекта **DesktopWindowXamlSource**. В этом случае вам не потребуется собственный код для инициализации платформы.

    * Но если приложение создаст объекты **Windows.UI.Xaml.UIElement** раньше, чем соответствующий объект **DesktopWindowXamlSource**, приложению нужно будет вызвать статический метод [WindowsXamlManager.InitializeForCurrentThread](https://docs.microsoft.com/uwp/api/windows.ui.xaml.hosting.windowsxamlmanager.initializeforcurrentthread) для явной инициализации платформы UWP XAML перед созданием экземпляров объектов **Windows.UI.Xaml.UIElement**. Обычно лучше всего вызывать этот метод при создании экземпляра того элемента пользовательского интерфейса, в котором создается экземпляр **DesktopWindowXamlSource**.

    > [!NOTE]
    > Этот метод возвращает объект [WindowsXamlManager](https://docs.microsoft.com/uwp/api/windows.ui.xaml.hosting.windowsxamlmanager) со ссылкой на платформу XAML UWP. В любом потоке можно создать любое количество объектов **WindowsXamlManager**. Но так как каждый такой объект содержит ссылку на платформу XAML UWP, объекты нужно удалить для освобождения ресурсов XAML.

2. Создайте объект [DesktopWindowXamlSource](https://docs.microsoft.com/uwp/api/windows.ui.xaml.hosting.desktopwindowxamlsource) и присоедините его к родительскому элементу пользовательского интерфейса в приложении, которое связано с обработчиком окна.

    Для этого выполните следующие действия:

    1. Создайте объект **DesktopWindowXamlSource** и преобразуйте его в интерфейс COM **IDesktopWindowXamlSourceNative** или **IDesktopWindowXamlSourceNative2**.
        > [!NOTE]
        > Эти интерфейсы объявлены в файле заголовка **windows.ui.xaml.hosting.desktopwindowxamlsource.h** в Windows SDK. По умолчанию этот файл находится в папке %programfiles(x86)%\Windows Kits\10\Include\\<номер сборки\>\um.

    2. Вызовите метод **AttachToWindow** интерфейса **IDesktopWindowXamlSourceNative** или **IDesktopWindowXamlSourceNative2**, передав дескриптор окна родительского элемента пользовательского интерфейса в приложении.

    3. Задайте начальный размер для внутреннего дочернего окна, размещенного в **DesktopWindowXamlSource**. По умолчанию этому внутреннему дочернему окну присваиваются нулевые значения высоты и ширины. Если вы не укажете другой размер окна, все добавленные в **DesktopWindowXamlSource** элементы управления UWP будут невидимыми. Чтобы обращаться к внутреннему дочернему окну через **DesktopWindowXamlSource**, используйте свойство **WindowHandle** интерфейса **IDesktopWindowXamlSourceNative** или **IDesktopWindowXamlSourceNative2**.

3. Наконец, присвойте значение размещаемого элемента **Windows.UI.Xaml.UIElement** свойству [Content](https://docs.microsoft.com/uwp/api/windows.ui.xaml.hosting.desktopwindowxamlsource.content) в объекте **DesktopWindowXamlSource**.

В следующих инструкциях и примерах кода показано, как выполнить описанный выше процесс:

1. В папке проекта **Source Files** откройте стандартный файл **MyDesktopWin32App.cpp**. Удалите все содержимое файла и добавьте вместо него следующие инструкции `include` и `using`. В дополнение к стандартным заголовкам и пространствам имен C++ и UWP эти инструкции включают несколько элементов специально для XAML Islands.

    ```cppwinrt
    #include <windows.h>
    #include <stdlib.h>
    #include <string.h>

    #include <winrt/Windows.Foundation.Collections.h>
    #include <winrt/Windows.system.h>
    #include <winrt/windows.ui.xaml.hosting.h>
    #include <windows.ui.xaml.hosting.desktopwindowxamlsource.h>
    #include <winrt/windows.ui.xaml.controls.h>
    #include <winrt/Windows.ui.xaml.media.h>

    using namespace winrt;
    using namespace Windows::UI;
    using namespace Windows::UI::Composition;
    using namespace Windows::UI::Xaml::Hosting;
    using namespace Windows::Foundation::Numerics;
    ```

3. Скопируйте следующий код после предыдущего раздела. Этот код определяет в приложении функцию **WinMain**. Эта функция инициализирует простое окно и применяет API размещения XAML для размещения простого элемента управления UWP **TextBlock** в окне.

    ```cppwinrt
    LRESULT CALLBACK WindowProc(HWND, UINT, WPARAM, LPARAM);

    HWND _hWnd;
    HWND _childhWnd;
    HINSTANCE _hInstance;

    int CALLBACK WinMain(_In_ HINSTANCE hInstance, _In_opt_ HINSTANCE hPrevInstance, _In_ LPSTR lpCmdLine, _In_ int nCmdShow)
    {
        _hInstance = hInstance;

        // The main window class name.
        const wchar_t szWindowClass[] = L"Win32DesktopApp";
        WNDCLASSEX windowClass = { };

        windowClass.cbSize = sizeof(WNDCLASSEX);
        windowClass.lpfnWndProc = WindowProc;
        windowClass.hInstance = hInstance;
        windowClass.lpszClassName = szWindowClass;
        windowClass.hbrBackground = (HBRUSH)(COLOR_WINDOW + 1);

        windowClass.hIconSm = LoadIcon(windowClass.hInstance, IDI_APPLICATION);

        if (RegisterClassEx(&windowClass) == NULL)
        {
            MessageBox(NULL, L"Windows registration failed!", L"Error", NULL);
            return 0;
        }

        _hWnd = CreateWindow(
            szWindowClass,
            L"Windows c++ Win32 Desktop App",
            WS_OVERLAPPEDWINDOW | WS_VISIBLE,
            CW_USEDEFAULT, CW_USEDEFAULT, CW_USEDEFAULT, CW_USEDEFAULT,
            NULL,
            NULL,
            hInstance,
            NULL
        );
        if (_hWnd == NULL)
        {
            MessageBox(NULL, L"Call to CreateWindow failed!", L"Error", NULL);
            return 0;
        }


        // Begin XAML Island section.

        // The call to winrt::init_apartment initializes COM; by default, in a multithreaded apartment.
        winrt::init_apartment(apartment_type::single_threaded);

        // Initialize the XAML framework's core window for the current thread.
        WindowsXamlManager winxamlmanager = WindowsXamlManager::InitializeForCurrentThread();

        // This DesktopWindowXamlSource is the object that enables a non-UWP desktop application 
        // to host UWP controls in any UI element that is associated with a window handle (HWND).
        DesktopWindowXamlSource desktopSource;

        // Get handle to the core window.
        auto interop = desktopSource.as<IDesktopWindowXamlSourceNative>();

        // Parent the DesktopWindowXamlSource object to the current window.
        check_hresult(interop->AttachToWindow(_hWnd));

        // This HWND will be the window handler for the XAML Island: A child window that contains XAML.  
        HWND hWndXamlIsland = nullptr;

        // Get the new child window's HWND. 
        interop->get_WindowHandle(&hWndXamlIsland);

        // Update the XAML Island window size because initially it is 0,0.
        SetWindowPos(hWndXamlIsland, 0, 200, 100, 800, 200, SWP_SHOWWINDOW);

        // Create the XAML content.
        Windows::UI::Xaml::Controls::StackPanel xamlContainer;
        xamlContainer.Background(Windows::UI::Xaml::Media::SolidColorBrush{ Windows::UI::Colors::LightGray() });

        Windows::UI::Xaml::Controls::TextBlock tb;
        tb.Text(L"Hello World from Xaml Islands!");
        tb.VerticalAlignment(Windows::UI::Xaml::VerticalAlignment::Center);
        tb.HorizontalAlignment(Windows::UI::Xaml::HorizontalAlignment::Center);
        tb.FontSize(48);

        xamlContainer.Children().Append(tb);
        xamlContainer.UpdateLayout();
        desktopSource.Content(xamlContainer);

        // End XAML Island section.

        ShowWindow(_hWnd, nCmdShow);
        UpdateWindow(_hWnd);

        //Message loop:
        MSG msg = { };
        while (GetMessage(&msg, NULL, 0, 0))
        {
            TranslateMessage(&msg);
            DispatchMessage(&msg);
        }

        return 0;
    }
    ```

4. Скопируйте следующий код после предыдущего раздела. Этот код определяет в приложении [рабочую процедуру](https://docs.microsoft.com/windows/win32/learnwin32/writing-the-window-procedure).

    ```cppwinrt
    LRESULT CALLBACK WindowProc(HWND hWnd, UINT messageCode, WPARAM wParam, LPARAM lParam)
    {
        PAINTSTRUCT ps;
        HDC hdc;
        wchar_t greeting[] = L"Hello World in Win32!";
        RECT rcClient;

        switch (messageCode)
        {
            case WM_PAINT:
                if (hWnd == _hWnd)
                {
                    hdc = BeginPaint(hWnd, &ps);
                    TextOut(hdc, 300, 5, greeting, wcslen(greeting));
                    EndPaint(hWnd, &ps);
                }
                break;
            case WM_DESTROY:
                PostQuitMessage(0);
                break;

            // Create main window
            case WM_CREATE:
                _childhWnd = CreateWindowEx(0, L"ChildWClass", NULL, WS_CHILD | WS_BORDER, 0, 0, 0, 0, hWnd, NULL, _hInstance, NULL);
                return 0;

            // Main window changed size
            case WM_SIZE:
                // Get the dimensions of the main window's client
                // area, and enumerate the child windows. Pass the
                // dimensions to the child windows during enumeration.
                GetClientRect(hWnd, &rcClient);
                MoveWindow(_childhWnd, 200, 200, 400, 500, TRUE);
                ShowWindow(_childhWnd, SW_SHOW);

                return 0;

                // Process other messages.

            default:
                return DefWindowProc(hWnd, messageCode, wParam, lParam);
                break;
        }

        return 0;
    }
    ```

5. Сохраните этот файл с кодом и выполните приложение. Убедитесь, что в окне приложения отображается элемент управления UWP **TextBlock**.
    > [!NOTE]
    > Возможно, вы увидите несколько предупреждений сборки, таких как `warning C4002:  too many arguments for function-like macro invocation 'GetCurrentTime'` и `manifest authoring warning 81010002: Unrecognized Element "maxversiontested" in namespace "urn:schemas-microsoft-com:compatibility.v1"`. Это известная проблема в текущих версиях средств и пакетов NuGet. Такие предупреждения можно игнорировать.

Полные примеры выполнения этих задач вы найдете в следующих файлах кода.

* **Win32 на C++:**
  * файл [HelloWindowsDesktop.cpp](https://github.com/microsoft/Xaml-Islands-Samples/blob/master/Standalone_Samples/CppWinRT_Basic_Win32App/Win32DesktopApp/HelloWindowsDesktop.cpp);
  * файл [XamlBridge.cpp](https://github.com/microsoft/Xaml-Islands-Samples/blob/master/Samples/Win32/SampleCppApp/XamlBridge.cpp).
* **WPF:** файлы [WindowsXamlHostBase.cs](https://github.com/windows-toolkit/Microsoft.Toolkit.Win32/blob/master/Microsoft.Toolkit.Wpf.UI.XamlHost/WindowsXamlHostBase.cs) и [WindowsXamlHost.cs](https://github.com/windows-toolkit/Microsoft.Toolkit.Win32/blob/master/Microsoft.Toolkit.Wpf.UI.XamlHost/WindowsXamlHost.cs) из набора средств сообщества Windows.  
* **Windows Forms:** файлы [WindowsXamlHostBase.cs](https://github.com/windows-toolkit/Microsoft.Toolkit.Win32/blob/master/Microsoft.Toolkit.Forms.UI.XamlHost/WindowsXamlHostBase.cs) и [WindowsXamlHost.cs](https://github.com/windows-toolkit/Microsoft.Toolkit.Win32/blob/master/Microsoft.Toolkit.Forms.UI.XamlHost/WindowsXamlHost.cs) из набора средств сообщества Windows.

## <a name="package-the-app"></a>Создание пакета приложения

При желании вы можете упаковать приложение в [пакет MSIX](https://docs.microsoft.com/windows/msix) для развертывания. MSIX — это современная технология упаковки приложений для Windows. В ее основе лежат технологии установки MSI, APPX, App-V и ClickOnce.

В приведенных ниже инструкциях рассказывается, как упаковать все компоненты решения в пакет MSIX с помощью [проекта упаковки приложения Windows](https://docs.microsoft.com/windows/msix/desktop/desktop-to-uwp-packaging-dot-net) в Visual Studio 2019. Эти действия требуются, только если нужно упаковать приложение в пакет MSIX.

> [!NOTE]
> Если вы решили не упаковывать приложение в [пакет MSIX](https://docs.microsoft.com/windows/msix) для развертывания, на компьютерах с запущенными приложениями должна быть установлена [среда выполнения Visual C++](https://support.microsoft.com/en-us/help/2977003/the-latest-supported-visual-c-downloads).

1. Добавьте новый [проект упаковки приложений Windows](https://docs.microsoft.com/windows/msix/desktop/desktop-to-uwp-packaging-dot-net) в свое решение. При создании проекта выберите **Windows 10 версии 1903 (10.0; сборка 18362)** в качестве **целевой** и **минимальной** версии.

2. В проекте упаковки щелкните правой кнопкой мыши узел **Приложения** и выберите команду **Добавить ссылку**. В списке проектов выберите проект классического приложения C++ для Win32 и нажмите кнопку **ОК**.

3. Выполните сборку и запустите проект упаковки. Убедитесь, что приложение выполняется и правильно отображает элемент управления UWP.

## <a name="next-steps"></a>Дальнейшие действия

Примеры кода в этой статье помогут вам начать работу с простейшими сценариями размещения стандартного элемента управления UWP в приложении Win32 на C++. В следующих разделах мы опишем расширенные сценарии для вашего приложения.

### <a name="host-a-custom-uwp-control"></a>Размещение настраиваемого элемента управления UWP

Для многих сценариев нужно размещать пользовательский элемент управления UWP XAML, который содержит несколько отдельных совместно работающих элементов управления. Процесс размещения пользовательского элемента управления UWP (который вы определите самостоятельно или получите от третьей стороны) в приложении Win32 на C++ будет более сложным, чем для стандартного элемента управления. Для этого требуется дополнительный код.

См. полное руководство по [размещению настраиваемого элемента управления UWP в приложении Win32 на C++ с помощью API размещения XAML](host-custom-control-with-xaml-islands-cpp.md).

### <a name="advanced-scenarios"></a>Расширенные сценарии

Многим классическим приложениям, в которых используется XAML Islands, потребуется обрабатывать дополнительные сценарии, чтобы обеспечить бесперебойную работу пользовательского интерфейса. Например, в классических приложениях может потребоваться обрабатывать ввод с клавиатуры в XAML Islands, выполнять переход между XAML Islands и другими элементами пользовательского интерфейса, а также изменять макет.

Дополнительные сведения об обработке этих сценариев и связанные примеры кода см. в статье [Расширенные сценарии для XAML Islands в приложениях Win32 на C++](advanced-scenarios-xaml-islands-cpp.md).

## <a name="related-topics"></a>Связанные темы

* [Элементы управления UWP XAML в классических приложениях (XAML Islands)](xaml-islands.md)
* [Использование API размещения XAML платформы UWP в приложении Win32 на C++](using-the-xaml-hosting-api.md)
* [Размещение настраиваемого элемента управления UWP в приложении Win32 на C++](host-custom-control-with-xaml-islands-cpp.md)
* [Расширенные сценарии для XAML Islands в приложениях Win32 на C++](advanced-scenarios-xaml-islands-cpp.md)
* [Примеры кода для XAML Islands](https://github.com/microsoft/Xaml-Islands-Samples)
