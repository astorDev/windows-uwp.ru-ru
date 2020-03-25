---
description: В этой статье показано, как разместить стандартный элемент управления UWP в C++ приложении Win32 с помощью API размещения XAML.
title: Размещение стандартного элемента управления UWP в приложении C++ Win32 с помощью XAML-островов
ms.date: 03/23/2020
ms.topic: article
keywords: Windows 10, UWP, CPP, Win32, острова XAML, элементы управления с оболочкой, стандартные элементы управления
ms.author: mcleans
author: mcleanbyron
ms.localizationpriority: medium
ms.custom: 19H1
ms.openlocfilehash: 08308c7bca3cd7f39b08c836e43d791a3fda048f
ms.sourcegitcommit: c660def841abc742600fbcf6ed98e1f4f7beb8cc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/24/2020
ms.locfileid: "80226278"
---
# <a name="host-a-standard-uwp-control-in-a-c-win32-app"></a>Размещение стандартного элемента управления UWP в приложении C++ Win32

В этой статье показано, как использовать [API хостинга UWP XAML](using-the-xaml-hosting-api.md) для размещения стандартного элемента управления UWP (т. е. элемента управления, предоставленного Windows SDK) в новом C++ приложении Win32. Код основан на [простой образце острова XAML](https://github.com/microsoft/Xaml-Islands-Samples/tree/master/Standalone_Samples/CppWinRT_Basic_Win32App), и в этом разделе обсуждаются некоторые из наиболее важных частей кода. При наличии существующего C++ проекта приложения Win32 можно адаптировать эти шаги и примеры кода для проекта.

> [!NOTE]
> Сценарий, продемонстрированный в этой статье, не поддерживает непосредственное редактирование разметки XAML для элементов управления UWP, размещенных в приложении. В этом сценарии ограничиваются изменение внешнего вида и поведения размещенных элементов управления UWP с помощью кода. Инструкции, позволяющие изменить разметку XAML непосредственно при размещении элементов управления UWP, см. [в разделе Размещение пользовательского элемента C++ управления UWP в приложении Win32](host-custom-control-with-xaml-islands-cpp.md).

## <a name="create-a-desktop-application-project"></a>Создание проекта классического приложения

1. В Visual Studio 2019 с пакетом SDK для Windows 10, версия 1903 (10.0.18362) или более поздней версии создайте новый проект **классического приложения Windows** и назовите его **MyDesktopWin32App**. Этот тип проекта доступен в фильтрах **C++** проектов, **Windows**и **Desktop** .

2. В **Обозреватель решений**щелкните правой кнопкой мыши узел решения, выберите пункт **перенацелить решение**, выберите **10.0.18362.0** или более поздний выпуск пакета SDK и нажмите кнопку **ОК**.

3. Установите пакет NuGet [Microsoft. Windows. кппвинрт](https://www.nuget.org/packages/Microsoft.Windows.CppWinRT/) , чтобы включить поддержку [ C++/WinRT](/windows/uwp/cpp-and-winrt-apis) в проекте:

    1. Щелкните правой кнопкой мыши проект в **Обозреватель решений** и выберите пункт **Управление пакетами NuGet**.
    2. Перейдите на вкладку **Обзор** , найдите пакет [Microsoft. Windows. кппвинрт](https://www.nuget.org/packages/Microsoft.Windows.CppWinRT/) и установите последнюю версию этого пакета.

    > [!NOTE]
    > Для новых проектов можно также установить [ C++расширение Visual Studio/WinRT (VSIX)](https://marketplace.visualstudio.com/items?itemName=CppWinRTTeam.cppwinrt101804264) и использовать один из шаблонов проектов C++/WinRT, входящих в это расширение. Дополнительные сведения см. в [этой статье](/windows/uwp/cpp-and-winrt-apis/intro-to-using-cpp-with-winrt#visual-studio-support-for-cwinrt-xaml-the-vsix-extension-and-the-nuget-package).

4. Установите пакет NuGet [Microsoft. Toolkit. Win32. UI. SDK](https://www.nuget.org/packages/Microsoft.Toolkit.Win32.UI.SDK) :

    1. В окне **Диспетчер пакетов NuGet** установите флажок **включить предварительные выпуски** .
    2. Перейдите на вкладку **Обзор** , найдите пакет **Microsoft. Toolkit. Win32. UI. SDK** и установите версию v 6.0.0 (или более позднюю) этого пакета. Этот пакет предоставляет несколько ресурсов для сборки и времени выполнения, которые позволяют работать в приложении по работе с XAML.

5. Задайте значение `maxVersionTested` в [манифесте приложения](https://docs.microsoft.com/windows/desktop/SbsCs/application-manifests) , чтобы указать, что приложение совместимо с Windows 10, 1903 или более поздней версии.

    1. Если в проекте еще нет манифеста приложения, добавьте новый XML-файл в проект и назовите его **app. manifest**.
    2. В манифест приложения включите элемент **Compatibility** и дочерние элементы, показанные в следующем примере. Замените атрибут **ID** элемента **maxversiontested укажите установленную** на номер версии Windows 10 для целевой платформы (это должна быть windows 10, версия 1903 или более поздняя).

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

## <a name="use-the-xaml-hosting-api-to-host-a-uwp-control"></a>Использование API размещения XAML для размещения элемента управления UWP

Базовый процесс использования API размещения в XAML для размещения элемента управления UWP выполняется следующим образом.

1. Инициализируйте платформу XAML UWP для текущего потока, прежде чем приложение создаст любой объект [Windows. UI. XAML. UIElement](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement) , который он будет размещать. Это можно сделать несколькими способами, в зависимости от того, когда планируется создать объект [десктопвиндовксамлсаурце](https://docs.microsoft.com/uwp/api/windows.ui.xaml.hosting.desktopwindowxamlsource) , в котором будут размещаться элементы управления.

    * Если приложение создает объект **десктопвиндовксамлсаурце** перед созданием любых объектов **Windows. UI. XAML. UIElement** , которые он будет размещать, эта платформа будет инициализирована при создании экземпляра объекта **десктопвиндовксамлсаурце** . В этом случае вам не нужно добавлять собственный код для инициализации платформы.

    * Однако если приложение создает объекты **Windows. UI. XAML. UIElement** перед созданием объекта **десктопвиндовксамлсаурце** , который будет размещать их, приложение должно вызвать статический метод [виндовсксамлманажер. инитиализефоркуррентсреад](https://docs.microsoft.com/uwp/api/windows.ui.xaml.hosting.windowsxamlmanager.initializeforcurrentthread) , чтобы явным образом инициализировать платформу XAML UWP перед созданием объектов **Windows. UI. XAML. UIElement** . Приложение обычно вызывает этот метод, когда создается родительский элемент пользовательского интерфейса, на котором размещается **десктопвиндовксамлсаурце** .

    > [!NOTE]
    > Этот метод возвращает объект [виндовсксамлманажер](https://docs.microsoft.com/uwp/api/windows.ui.xaml.hosting.windowsxamlmanager) , содержащий ссылку на ПЛАТФОРМУ XAML UWP. В определенном потоке можно создать столько объектов **виндовсксамлманажер** , сколько нужно. Однако, поскольку каждый объект содержит ссылку на платформу XAML UWP, следует ликвидировать объекты, чтобы обеспечить окончательное освобождение ресурсов XAML.

2. Создайте объект [десктопвиндовксамлсаурце](https://docs.microsoft.com/uwp/api/windows.ui.xaml.hosting.desktopwindowxamlsource) и присоедините его к родительскому ЭЛЕМЕНТУ пользовательского интерфейса в приложении, связанному с маркером окна.

    Для этого необходимо выполнить следующие действия.

    1. Создайте объект **десктопвиндовксамлсаурце** и приведите его к com-интерфейсу **идесктопвиндовксамлсаурценативе** или **IDesktopWindowXamlSourceNative2** .
        > [!NOTE]
        > Эти интерфейсы объявляются в файле заголовка **Windows. UI. XAML. Hosting. десктопвиндовксамлсаурце. h** в Windows SDK. По умолчанию этот файл находится в% ProgramFiles (x86)% \ Windows Kits\10\Include\\< номер сборки\>\ум.

    2. Вызовите метод **аттачтовиндов** интерфейса **идесктопвиндовксамлсаурценативе** или **IDesktopWindowXamlSourceNative2** и передайте в приложение маркер окна родительского элемента пользовательского интерфейса.

    3. Задайте начальный размер внутреннего дочернего окна, содержащегося в **десктопвиндовксамлсаурце**. По умолчанию для этого внутреннего дочернего окна заданы ширина и высота 0. Если размер окна не задан, все элементы управления UWP, добавляемые в **десктопвиндовксамлсаурце** , не будут видны. Чтобы получить доступ к внутреннему дочернему окну в **десктопвиндовксамлсаурце**, используйте свойство **WindowHandle** интерфейса **идесктопвиндовксамлсаурценативе** или **IDesktopWindowXamlSourceNative2** .

3. Наконец, назначьте элемент **Windows. UI. XAML. UIElement** , который требуется разместить, в свойстве [Content](https://docs.microsoft.com/uwp/api/windows.ui.xaml.hosting.desktopwindowxamlsource.content) объекта **десктопвиндовксамлсаурце** .

В следующих шагах и примерах кода показано, как реализовать описанный выше процесс.

1. В папке **исходные файлы** проекта откройте файл **MyDesktopWin32App. cpp** по умолчанию. Удалите все содержимое файла и добавьте следующие инструкции `include` и `using`. В дополнение к стандартным C++ и заголовкам и пространствам имен UWP эти инструкции включают в себя несколько элементов, относящихся к островам XAML.

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

3. Скопируйте следующий код после предыдущего раздела. Этот код определяет функцию **WinMain** для приложения. Эта функция инициализирует основное окно и использует API размещения XAML для размещения простого элемента управления **TEXTBLOCK** UWP в окне.

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

4. Скопируйте следующий код после предыдущего раздела. Этот код определяет [процедуру окна](https://docs.microsoft.com/windows/win32/learnwin32/writing-the-window-procedure) для окна.

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

5. Сохраните файл кода, а затем выполните сборку и запуск приложения. Убедитесь, что в окне приложения отображается элемент управления **TextBlock** элемента UWP.
    > [!NOTE]
    > Вы можете увидеть несколько предупреждений сборки, в том числе `warning C4002:  too many arguments for function-like macro invocation 'GetCurrentTime'` и `manifest authoring warning 81010002: Unrecognized Element "maxversiontested" in namespace "urn:schemas-microsoft-com:compatibility.v1"`. Эти предупреждения являются известными проблемами с текущими инструментами и пакетами NuGet, и их можно игнорировать.

Полные примеры, демонстрирующие эти задачи, см. в следующих файлах кода:

* **C++Платформа**
  * См. файл [хелловиндовсдесктоп. cpp](https://github.com/microsoft/Xaml-Islands-Samples/blob/master/Standalone_Samples/CppWinRT_Basic_Win32App/Win32DesktopApp/HelloWindowsDesktop.cpp) .
  * См. файл [ксамлбридже. cpp](https://github.com/microsoft/Xaml-Islands-Samples/blob/master/Samples/Win32/SampleCppApp/XamlBridge.cpp) .
* **WPF:** См. файлы [WindowsXamlHostBase.CS](https://github.com/windows-toolkit/Microsoft.Toolkit.Win32/blob/master/Microsoft.Toolkit.Wpf.UI.XamlHost/WindowsXamlHostBase.cs) и [WindowsXamlHost.CS](https://github.com/windows-toolkit/Microsoft.Toolkit.Win32/blob/master/Microsoft.Toolkit.Wpf.UI.XamlHost/WindowsXamlHost.cs) в наборе средств сообщества Windows.  
* **Windows Forms:** См. файлы [WindowsXamlHostBase.CS](https://github.com/windows-toolkit/Microsoft.Toolkit.Win32/blob/master/Microsoft.Toolkit.Forms.UI.XamlHost/WindowsXamlHostBase.cs) и [WindowsXamlHost.CS](https://github.com/windows-toolkit/Microsoft.Toolkit.Win32/blob/master/Microsoft.Toolkit.Forms.UI.XamlHost/WindowsXamlHost.cs) в наборе средств сообщества Windows.

## <a name="package-the-app"></a>Упаковка приложения

При необходимости можно упаковать приложение в [пакет MSIX](https://docs.microsoft.com/windows/msix) для развертывания. MSIX — это современная технология упаковки приложений для Windows, которая основана на сочетании технологий установки MSI, appx, App-V и ClickOnce.

В следующих инструкциях показано, как упаковать все компоненты в решении в пакете MSIX с помощью [проекта упаковки приложений Windows](https://docs.microsoft.com/windows/msix/desktop/desktop-to-uwp-packaging-dot-net) в Visual Studio 2019. Эти действия необходимы только в том случае, если требуется упаковать приложение в пакет MSIX.

> [!NOTE]
> Если вы решили не упаковать приложение в [пакет MSIX](https://docs.microsoft.com/windows/msix) для развертывания, на компьютерах, где выполняется приложение, должна быть установлена [Среда выполнения C++ Visual](https://support.microsoft.com/en-us/help/2977003/the-latest-supported-visual-c-downloads) .

1. Добавьте новый [проект упаковки приложений Windows](https://docs.microsoft.com/windows/msix/desktop/desktop-to-uwp-packaging-dot-net) в решение. При создании проекта выберите **Windows 10, версия 1903 (10,0; Сборка 18362)** для **целевой версии** и **минимальной версии**.

2. В проекте упаковки щелкните правой кнопкой мыши узел **приложения** и выберите команду **Добавить ссылку**. В списке проектов выберите в решении проект классического приложения C++/Win32 и нажмите кнопку **ОК**.

3. Создайте и запустите проект упаковки. Убедитесь, что приложение запущено и отображает элементы управления UWP, как ожидалось.

## <a name="next-steps"></a>Следующие шаги

Примеры кода в этой статье помогут вам начать работу с базовым сценарием размещения стандартного элемента управления UWP в приложении C++ Win32. В следующих разделах представлены дополнительные сценарии, которые может потребоваться поддерживать приложению.

### <a name="host-a-custom-uwp-control"></a>Размещение пользовательского элемента управления UWP

Во многих случаях может потребоваться разместить пользовательский элемент управления Windows UWP, содержащий несколько отдельных элементов управления, которые работают вместе. Процесс размещения пользовательского элемента управления UWP (элемент управления, определяемый пользователем или управляемый сторонними разработчиками) в приложении C++ Win32 является более сложным, чем размещение стандартного элемента управления и требует дополнительного кода.

Полное пошаговое руководство см. [в разделе Размещение пользовательского элемента управления C++ UWP в приложении Win32 с помощью API размещения XAML](host-custom-control-with-xaml-islands-cpp.md).

### <a name="advanced-scenarios"></a>Расширенные сценарии

Многие настольные приложения, на которых размещаются острова XAML, должны будут поддерживать дополнительные сценарии, чтобы обеспечить плавное взаимодействие с пользователем. Например, для настольных приложений может потребоваться управлять вводом с клавиатуры в островах XAML, переходом между островами XAML и другими элементами пользовательского интерфейса, а также изменениями макета.

Дополнительные сведения об обработке этих сценариев и указателей на связанные образцы кода см. [в разделе Расширенные сценарии для островов C++ XAML в приложениях Win32](advanced-scenarios-xaml-islands-cpp.md).

## <a name="related-topics"></a>Связанные разделы

* [Размещение элементов управления XAML UWP в классических приложениях (острова XAML)](xaml-islands.md)
* [Использование API размещения UWP XAML в приложении C++ Win32](using-the-xaml-hosting-api.md)
* [Размещение пользовательского элемента управления UWP в приложении C++ Win32](host-custom-control-with-xaml-islands-cpp.md)
* [Расширенные сценарии для островов XAML в C++ приложениях Win32](advanced-scenarios-xaml-islands-cpp.md)
* [Примеры кода островов XAML](https://github.com/microsoft/Xaml-Islands-Samples)
