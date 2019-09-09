---
description: В этой статье описывается размещение пользовательского интерфейса XAML UWP в приложении Win32 C++ для настольных систем.
title: Использование API размещения XAML платформы UWP в приложении Win32 на C++
ms.date: 08/20/2019
ms.topic: article
keywords: Windows 10, UWP, Windows Forms, WPF, Win32, о-ва XAML
ms.author: mcleans
author: mcleanbyron
ms.localizationpriority: medium
ms.custom: 19H1
ms.openlocfilehash: 6c1f45b4bd3da74ea150c05800eba7ec10568894
ms.sourcegitcommit: 6bb794c6e309ba543de6583d96627fbf1c177bef
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/20/2019
ms.locfileid: "69643403"
---
# <a name="using-the-uwp-xaml-hosting-api-in-a-c-win32-app"></a>Использование API размещения XAML платформы UWP в приложении Win32 на C++

Начиная с Windows 10, версия 1903, не относящаяся к UWP настольные приложения (включая C++ Win32, WPF и Windows Forms приложения) может использовать *API размещения UWP XAML* для размещения элементов управления UWP в любом элементе пользовательского интерфейса, связанном с дескриптором окна (HWND). Этот API позволяет приложениям, не относящимся к UWP, использовать новейшие функции пользовательского интерфейса Windows 10, которые доступны только через элементы управления UWP. Например, приложения, не являющиеся классом UWP, могут использовать этот API для размещения элементов управления UWP, использующих [систему разработки Fluent](/windows/uwp/design/fluent-design-system/index) и поддерживающих [Рукописный ввод Windows](/windows/uwp/design/input/pen-and-stylus-interactions).

API хостинга для UWP XAML предоставляет основу для более широкого набора элементов управления, предоставляемых разработчиками для того, чтобы разработчики получили доступ к классским приложениям, не относящимся к UWP. Эта функция называется *островами XAML*. Общие сведения об этой функции см. [в разделе Размещение элементов управления XAML UWP в классических приложениях (острова XAML)](xaml-islands.md).

> [!NOTE]
> Если у вас есть отзывы о островах XAML, создайте новую ошибку в [репозитории Microsoft. Toolkit. Win32](https://github.com/windows-toolkit/Microsoft.Toolkit.Win32/issues) и оставьте свои комментарии. Если вы предпочитаете отправить отзыв в частном порядке, его можно отправить в XamlIslandsFeedback@microsoft.com. Ваши ценные сведения и сценарии критически важны для нас.

## <a name="should-you-use-the-uwp-xaml-hosting-api"></a>Следует ли использовать API размещения UWP XAML?

API хостинга UWP XAML предоставляет низкую инфраструктуру для размещения элементов управления UWP в классических приложениях. Некоторые типы классических приложений имеют возможность использовать альтернативные, более удобные API для выполнения этой задачи.  

* Если у вас есть C++ классическое приложение Win32 и вы хотите разместить элементы управления UWP в своем приложении, необходимо использовать API хостинга для UWP XAML. Для таких типов приложений нет альтернативы.

* Для приложений WPF и Windows Forms мы настоятельно рекомендуем использовать [элементы управления .NET на языке XAML](xaml-islands.md#wpf-and-windows-forms-applications) в наборе средств сообщества Windows, а не напрямую использовать API размещения для класса UWP XAML. Эти элементы управления используют API размещения универсального кода класса UWP для внутреннего использования и реализуют все поведение, которое в противном случае необходимо для самостоятельной работы, если вы используете API размещения в формате UWP XAML напрямую, включая навигацию с помощью клавиатуры и изменение макета.

Так как мы рекомендуем использовать C++ только приложения Win32, использующие API размещения UWP XAML, в этой статье в первую очередь приводятся инструкции и примеры для C++ приложений Win32. Однако можно использовать API размещения UWP XAML в WPF и Windows Forms приложениях, если вы решили. В этой статье рассматривается соответствующий исходный код для [элементов управления ведущего приложения](xaml-islands.md#host-controls) для WPF и Windows Forms в наборе средств сообщества Windows, поэтому можно увидеть, как именно API размещения UWP XAML используются этими элементами управления.

## <a name="prerequisites"></a>Предварительные требования

Для всех островов XAML требуется Windows 10, версия 1903 (или более поздняя) и соответствующая сборка Windows SDK. Чтобы использовать острова XAML в приложении C++ Win32, необходимо сначала настроить проект.

### <a name="support-for-cwinrt"></a>Поддержка C++/WinRT

Убедитесь, что проект поддерживает [ C++/WinRT](/windows/uwp/cpp-and-winrt-apis):

* Для новых проектов можно установить [ C++расширение Visual Studio/WinRT (VSIX)](https://marketplace.visualstudio.com/items?itemName=CppWinRTTeam.cppwinrt101804264) и использовать один из шаблонов проектов C++/WinRT, входящих в это расширение.
* Для существующих проектов пакет NuGet [Microsoft. Windows. кппвинрт](https://www.nuget.org/packages/Microsoft.Windows.CppWinRT/) можно установить в проекте.

Дополнительные сведения об этих параметрах см. в [этой статье](/windows/uwp/cpp-and-winrt-apis/intro-to-using-cpp-with-winrt#visual-studio-support-for-cwinrt-xaml-the-vsix-extension-and-the-nuget-package).

### <a name="configure-your-project-for-app-deployment"></a>Настройка проекта для развертывания приложений

Выберите один из следующих параметров, чтобы подготовить проект к развертыванию.

* **Упакуйте приложение в пакет MSIX**. Упаковка приложения в [пакет MSIX](https://docs.microsoft.com/windows/msix/) предоставляет множество преимуществ при развертывании и запуске.
    1. Установите пакет SDK для Windows 10 версии 1903 (версия 10.0.18362) или более поздней версии.
    2. Упакуйте приложение в пакет MSIX, добавив [проект упаковки приложений Windows](https://docs.microsoft.com/windows/msix/desktop/desktop-to-uwp-packaging-dot-net) в решение и добавив ссылку на проект C++/Win32.

* **Установите пакет Microsoft. Toolkit. Win32. UI. SDK**. Если вы не хотите упаковывать приложение в пакет MSIX, можно установить [Microsoft. Toolkit. Win32. UI. SDK](https://www.nuget.org/packages/Microsoft.Toolkit.Win32.UI.SDK) (Version v 6.0.0-preview7 или более поздней версии). Этот пакет предоставляет несколько ресурсов для сборки и времени выполнения, которые позволяют работать в приложении по работе с XAML. Убедитесь, что выбран параметр **включить предварительные выпуски** , чтобы можно было увидеть последние предварительные версии этого пакета.

> [!NOTE]
> Более ранние версии этих инструкций добавили `maxversiontested` элемент в манифест приложения в проекте. Пока вы используете один из перечисленных выше параметров, вам больше не нужно добавлять этот элемент в манифест.

### <a name="additional-requirements-for-custom-uwp-controls"></a>Дополнительные требования для пользовательских элементов управления UWP

Если вы размещаете пользовательский элемент управления UWP (например, Пользовательский элемент управления, состоящий из нескольких элементов управления UWP, которые работают вместе), необходимо выполнить дополнительные инструкции в [этом разделе](#host-a-custom-uwp-control). Кроме того, необходимо иметь исходный код для пользовательского элемента управления, чтобы его можно было скомпилировать в приложении.

## <a name="architecture-of-the-api"></a>Архитектура API

API размещения платформы UWP XAML включает следующие основные типы среда выполнения Windows и COM-интерфейсы.

|  Тип или интерфейс | Описание |
|--------------------|-------------|
| [WindowsXamlManager](https://docs.microsoft.com/uwp/api/windows.ui.xaml.hosting.windowsxamlmanager) | Этот класс представляет платформу XAML UWP. Этот класс предоставляет один статический метод [инитиализефоркуррентсреад](https://docs.microsoft.com/uwp/api/windows.ui.xaml.hosting.windowsxamlmanager.initializeforcurrentthread) , который инициализирует ПЛАТФОРМУ XAML UWP в текущем потоке в классическом приложении. |
| [DesktopWindowXamlSource](https://docs.microsoft.com/uwp/api/windows.ui.xaml.hosting.desktopwindowxamlsource) | Этот класс представляет экземпляр содержимого класса UWP XAML, которое размещается в вашем классическом приложении. Самым важным элементом этого класса является свойство [Content](https://docs.microsoft.com/uwp/api/windows.ui.xaml.hosting.desktopwindowxamlsource.content) . Это свойство назначается элементу управления [Windows. UI. XAML. UIElement](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement) , который требуется разместить. Этот класс также содержит другие члены для маршрутизации навигации фокуса клавиатуры в и из островов XAML. |
| идесктопвиндовксамлсаурценативе | Этот COM-интерфейс предоставляет метод **аттачтовиндов** , который используется для присоединения ОСТРОВИЧЕСКОГО кода XAML в приложении к родительскому ЭЛЕМЕНТУ пользовательского интерфейса. Каждый объект **десктопвиндовксамлсаурце** реализует этот интерфейс. |
| IDesktopWindowXamlSourceNative2 | Этот COM-интерфейс предоставляет метод **претранслатемессаже** , который позволяет ИНФРАСТРУКТУРЕ XAML UWP правильно обрабатывать определенные сообщения Windows. Каждый объект **десктопвиндовксамлсаурце** реализует этот интерфейс. |

На следующей схеме показана иерархия объектов в острове XAML, размещенном в классическом приложении.

* На базовом уровне находится элемент пользовательского интерфейса в приложении, в котором требуется разместить остров XAML. Этот элемент пользовательского интерфейса должен иметь дескриптор окна (HWND). Примеры элементов пользовательского интерфейса, в которых можно разместить остров XAML, включают [окно](https://docs.microsoft.com/windows/desktop/winmsg/about-windows) для C++ приложений Win32, [System. Windows. Interop. HwndHost](https://docs.microsoft.com/dotnet/api/system.windows.interop.hwndhost) для приложений WPF и [System. windows. Forms. Control](https://docs.microsoft.com/dotnet/api/system.windows.forms.control) для Windows Forms приложений.

* На следующем уровне находится объект **десктопвиндовксамлсаурце** . Этот объект предоставляет инфраструктуру для размещения острова XAML. Ваш код отвечает за создание этого объекта и его присоединение к родительскому элементу пользовательского интерфейса.

* При создании **десктопвиндовксамлсаурце**этот объект автоматически создает собственное дочернее окно для размещения элемента управления UWP. Это собственное дочернее окно в основном является абстрактным от кода, но при необходимости вы можете получить доступ к его дескриптору (HWND).

* Наконец, на верхнем уровне находится элемент управления UWP, который требуется разместить в классическом приложении. Это может быть любой объект UWP, производный от [Windows. UI. XAML. UIElement](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement), включая любой элемент управления UWP, предоставляемый Windows SDK, а также настраиваемые пользовательские элементы управления.

![Архитектура Десктопвиндовксамлсаурце](images/xaml-islands/xaml-hosting-api-rev2.png)

## <a name="related-samples"></a>Связанные примеры

Способ использования API размещения класса UWP XAML в коде зависит от типа приложения, дизайна приложения и других факторов. Чтобы продемонстрировать, как использовать этот API в контексте полного приложения, в этой статье рассматривается код из следующих примеров.

### <a name="c-win32"></a>C++Платформа

В следующих примерах показано, как использовать API размещения UWP XAML в приложении C++ Win32:

* [Пример простой XAML](https://github.com/marb2000/XamlIslands/tree/master/1903_Samples/CppWinRT_Win32_SimpleApp). В этом примере демонстрируется Базовая реализация размещения элемента управления UWP в неупакованном приложении C++ Win32 (то есть приложение, которое не встроено в пакет MSIX).

* [Остров XAML с примером пользовательского элемента управления](https://github.com/marb2000/XamlIslands/tree/master/1903_Samples/CppWinRT_Win32_App). В этом примере демонстрируется полная реализация размещения пользовательского элемента управления UWP в неупакованном приложении C++ Win32, а также обработка других поведений, таких как ввод с клавиатуры и Навигация по фокусу. 

### <a name="wpf-and-windows-forms"></a>WPF и Windows Forms

Элемент управления [виндовсксамлхост](https://docs.microsoft.com/windows/communitytoolkit/controls/wpf-winforms/windowsxamlhost) в наборе Windows Community Toolkit служит эталонным примером использования API размещения UWP в приложениях WPF и Windows Forms. Исходный код доступен в следующих расположениях:

* Для версии элемента управления WPF [перейдите сюда](https://github.com/windows-toolkit/Microsoft.Toolkit.Win32/tree/master/Microsoft.Toolkit.Wpf.UI.XamlHost). Версия WPF является производной от [System. Windows. Interop. HwndHost](https://docs.microsoft.com/dotnet/api/system.windows.interop.hwndhost).

* Для Windows Forms версии элемента управления [перейдите сюда](https://github.com/windows-toolkit/Microsoft.Toolkit.Win32/tree/master/Microsoft.Toolkit.Forms.UI.XamlHost). Версия Windows Forms является производной от [System. Windows. Forms. Control](https://docs.microsoft.com/dotnet/api/system.windows.forms.control).

## <a name="host-a-standard-uwp-control"></a>Размещение стандартного элемента управления UWP

В этом разделе описывается процесс использования API хостинга UWP XAML для размещения стандартного элемента управления UWP (то есть элемента управления, предоставляемого Windows SDK или библиотеки Винуи) в новом C++ приложении Win32. Код основан на [простой образце острова XAML](https://github.com/marb2000/XamlIslands/tree/master/1903_Samples/CppWinRT_Win32_SimpleApp), и в этом разделе обсуждаются некоторые из наиболее важных частей кода. При наличии существующего C++ проекта приложения Win32 можно адаптировать эти шаги и примеры кода для проекта.

### <a name="configure-the-project"></a>Настройка проекта

1. В Visual Studio 2019 с пакетом SDK для Windows 10, версия 1903 (10.0.18362) или более поздней версии создайте новый проект **классического приложения Windows** . Этот проект доступен в фильтрах **C++** проектов, **Windows**и **Desktop** .

2. В **Обозреватель решений**щелкните правой кнопкой мыши узел решения, выберите пункт **перенацелить решение**, выберите **10.0.18362.0** или более поздний выпуск пакета SDK и нажмите кнопку **ОК**.

3. Установите пакет NuGet [Microsoft. Windows. кппвинрт](https://www.nuget.org/packages/Microsoft.Windows.CppWinRT/) :

    1. Щелкните правой кнопкой мыши проект в **Обозреватель решений** и выберите пункт **Управление пакетами NuGet**.
    2. Перейдите на вкладку **Обзор** , найдите пакет [Microsoft. Windows. кппвинрт](https://www.nuget.org/packages/Microsoft.Windows.CppWinRT/) и установите последнюю версию этого пакета.

4. Установите пакет NuGet [Microsoft. Toolkit. Win32. UI. SDK](https://www.nuget.org/packages/Microsoft.Toolkit.Win32.UI.SDK) :

    1. В окне **Диспетчер пакетов NuGet** установите флажок **включить предварительные выпуски** .
    2. Перейдите на вкладку **Обзор** , найдите пакет [Microsoft. Toolkit. Win32. UI. SDK](https://www.nuget.org/packages/Microsoft.Toolkit.Win32.UI.SDK) и установите версию v 6.0.0-preview7 (или более позднюю) этого пакета.

### <a name="use-the-xaml-hosting-api-to-host-a-uwp-control"></a>Использование API размещения XAML для размещения элемента управления UWP

Базовый процесс использования API размещения в XAML для размещения элемента управления UWP выполняется следующим образом.

1. Инициализируйте платформу XAML UWP для текущего потока, прежде чем приложение создаст любой объект [Windows. UI. XAML. UIElement](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement) , который он будет размещать. Это можно сделать несколькими способами, в зависимости от того, когда планируется создать объект [десктопвиндовксамлсаурце](https://docs.microsoft.com/uwp/api/windows.ui.xaml.hosting.desktopwindowxamlsource) , в котором будут размещаться элементы управления.

    * Если приложение создает объект **десктопвиндовксамлсаурце** перед созданием любых объектов **Windows. UI. XAML. UIElement** , которые он будет размещать, эта платформа будет инициализирована автоматически при создании экземпляра  **Объект Десктопвиндовксамлсаурце** . В этом случае вам не нужно добавлять собственный код для инициализации платформы.

    * Однако если приложение создает объекты **Windows. UI. XAML. UIElement** перед созданием объекта **десктопвиндовксамлсаурце** , который будет размещать их, приложение должно вызвать статический [метод Метод Виндовсксамлманажер. Инитиализефоркуррентсреад](https://docs.microsoft.com/uwp/api/windows.ui.xaml.hosting.windowsxamlmanager.initializeforcurrentthread) для явной инициализации платформы XAML UWP перед созданием объектов **Windows. UI. XAML. UIElement** . Приложение обычно вызывает этот метод, когда создается родительский элемент пользовательского интерфейса, на котором размещается **десктопвиндовксамлсаурце** .

    > [!NOTE]
    > Этот метод возвращает объект [виндовсксамлманажер](https://docs.microsoft.com/uwp/api/windows.ui.xaml.hosting.windowsxamlmanager) , содержащий ссылку на ПЛАТФОРМУ XAML UWP. В определенном потоке можно создать столько объектов **виндовсксамлманажер** , сколько нужно. Однако, поскольку каждый объект содержит ссылку на платформу XAML UWP, следует ликвидировать объекты, чтобы обеспечить окончательное освобождение ресурсов XAML.

2. Создайте объект [десктопвиндовксамлсаурце](https://docs.microsoft.com/uwp/api/windows.ui.xaml.hosting.desktopwindowxamlsource) и присоедините его к родительскому ЭЛЕМЕНТУ пользовательского интерфейса в приложении, связанному с маркером окна.

    Для этого необходимо выполнить следующие действия.

    1. Создайте объект **десктопвиндовксамлсаурце** и приведите его к com-интерфейсу **идесктопвиндовксамлсаурценативе** или **IDesktopWindowXamlSourceNative2** .
        > [!NOTE]
        > Эти интерфейсы объявляются в файле заголовка **Windows. UI. XAML. Hosting. десктопвиндовксамлсаурце. h** в Windows SDK. По умолчанию этот файл находится в% ProgramFiles (x86)% \ Windows Kits\10\Include\\< номер\>сборки \ум.

    2. Вызовите метод **аттачтовиндов** интерфейса **идесктопвиндовксамлсаурценативе** или **IDesktopWindowXamlSourceNative2** и передайте в приложение маркер окна родительского элемента пользовательского интерфейса.

    3. Задайте начальный размер внутреннего дочернего окна, содержащегося в **десктопвиндовксамлсаурце**. По умолчанию для этого внутреннего дочернего окна заданы ширина и высота 0. Если размер окна не задан, все элементы управления UWP, добавляемые в **десктопвиндовксамлсаурце** , не будут видны. Чтобы получить доступ к внутреннему дочернему окну в **десктопвиндовксамлсаурце**, используйте свойство **WindowHandle** интерфейса **идесктопвиндовксамлсаурценативе** или **IDesktopWindowXamlSourceNative2** .

3. Наконец, назначьте элемент **Windows. UI. XAML. UIElement** , который требуется разместить, в свойстве [Content](https://docs.microsoft.com/uwp/api/windows.ui.xaml.hosting.desktopwindowxamlsource.content) объекта **десктопвиндовксамлсаурце** .

В следующих шагах и примерах кода показано, как реализовать описанный выше процесс.

1. В папке **исходные файлы** проекта откройте файл **виндовспрожект. cpp** по умолчанию. Удалите все содержимое файла и добавьте следующие `include` инструкции и. `using` В дополнение к стандартным C++ и заголовкам и пространствам имен UWP эти инструкции включают в себя несколько элементов, относящихся к островам XAML.

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

4. Скопируйте следующий код после предыдущего раздела. Этот код определяет [процедуру окна](https://docs.microsoft.com/en-us/windows/win32/learnwin32/writing-the-window-procedure) для окна.

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
    > Вы можете увидеть несколько предупреждений о сборке, `warning C4002:  too many arguments for function-like macro invocation 'GetCurrentTime'` включая `manifest authoring warning 81010002: Unrecognized Element "maxversiontested" in namespace "urn:schemas-microsoft-com:compatibility.v1"`и. Эти предупреждения являются известными проблемами с текущими инструментами и пакетами NuGet, и их можно игнорировать.

Полные примеры, демонстрирующие эти задачи, см. в следующих файлах кода:

* **C++Платформа**
  * См. файл [хелловиндовсдесктоп. cpp](https://github.com/marb2000/XamlIslands/blob/master/1903_Samples/CppWinRT_Win32_SimpleApp/Win32DesktopApp/HelloWindowsDesktop.cpp) в [примере простого XAML](https://github.com/marb2000/XamlIslands/tree/master/1903_Samples/CppWinRT_Win32_SimpleApp).
  * См. файл [ксамлбридже. cpp](https://github.com/marb2000/XamlIslands/blob/master/1903_Samples/CppWinRT_Win32_App/SampleCppApp/XamlBridge.cpp) в [острове XAML с примером пользовательского элемента управления](https://github.com/marb2000/XamlIslands/tree/master/1903_Samples/CppWinRT_Win32_App).

* **WPF** См. файлы [WindowsXamlHostBase.CS](https://github.com/windows-toolkit/Microsoft.Toolkit.Win32/blob/master/Microsoft.Toolkit.Wpf.UI.XamlHost/WindowsXamlHostBase.cs) и [WindowsXamlHost.CS](https://github.com/windows-toolkit/Microsoft.Toolkit.Win32/blob/master/Microsoft.Toolkit.Wpf.UI.XamlHost/WindowsXamlHost.cs) в наборе средств сообщества Windows.  

* **Windows Forms:** См. файлы [WindowsXamlHostBase.CS](https://github.com/windows-toolkit/Microsoft.Toolkit.Win32/blob/master/Microsoft.Toolkit.Forms.UI.XamlHost/WindowsXamlHostBase.cs) и [WindowsXamlHost.CS](https://github.com/windows-toolkit/Microsoft.Toolkit.Win32/blob/master/Microsoft.Toolkit.Forms.UI.XamlHost/WindowsXamlHost.cs) в наборе средств сообщества Windows.

## <a name="host-a-custom-uwp-control"></a>Размещение пользовательского элемента управления UWP

Процесс размещения пользовательского элемента управления UWP (элемент управления, определяемый пользователем или управляемый сторонним лицом) в приложении C++ Win32 начинается с тех же шагов, что и при [размещении стандартного элемента управления](#host-a-standard-uwp-control). Однако для размещения пользовательского элемента управления UWP требуются дополнительные проекты и шаги.

Для размещения пользовательского элемента управления UWP потребуются следующие проекты и компоненты:

* **Проект и исходный код приложения**. Убедитесь, что проект настроен в соответствии с [предварительными требованиями](#prerequisites) для размещения островов XAML.

* **Пользовательский элемент управления UWP**. Вам потребуется исходный код для пользовательского элемента управления UWP, который вы хотите разместить, чтобы его можно было скомпилировать с приложением. Как правило, Пользовательский элемент управления определяется в проекте библиотеки классов UWP, на который вы ссылаетесь в том же решении C++ , что и проект Win32.

* **Проект приложения UWP, определяющий объект ксамлаппликатион**. Проект C++ Win32 должен иметь доступ к экземпляру класса, `Microsoft.Toolkit.Win32.UI.XamlHost.XamlApplication` предоставленному набором средств сообщества Windows. Этот тип выступает в качестве корневого поставщика метаданных для загрузки метаданных пользовательских типов универсального кода пользователя UWP в сборки в текущем каталоге приложения. Для этого рекомендуется добавить **пустой проект приложения (универсальное приложение Windows)** в то же решение, что и проект C++ Win32, и исправить класс по умолчанию `App` в этом проекте.
  > [!NOTE]
  > Решение может содержать только один проект, определяющий `XamlApplication` объект. Все пользовательские элементы управления UWP в приложении совместно используют один `XamlApplication` и тот же объект. Проект, определяющий `XamlApplication` объект, должен включать ссылки на все другие библиотеки и проекты UWP, используемые для размещения элементов управления UWP в области XAML.

Чтобы разместить пользовательский элемент управления UWP в приложении C++ Win32, выполните следующие общие действия.

1. В решении, которое содержит проект C++ классического приложения Win32, добавьте **пустой проект приложения (универсальное приложение для Windows)** и настройте его, следуя подробным инструкциям в [этом разделе](host-custom-control-with-xaml-islands.md#create-a-xamlapplication-object-in-a-uwp-app-project) в пошаговом руководстве по связанным WPF.

2. В том же решении добавьте проект, содержащий исходный код для пользовательского элемента управления XAML UWP (обычно это проект библиотеки классов UWP), и создайте проект.

3. В проекте приложения UWP добавьте ссылку на проект библиотеки классов UWP.

4. В проекте C++ Win32 добавьте ссылку на проект приложения UWP и проект библиотеки классов UWP в свое решение.

5. Выполните процедуру, описанную в разделе [Использование API хостинга XAML для размещения раздела управления UWP](#use-the-xaml-hosting-api-to-host-a-uwp-control) для размещения пользовательского элемента управления в приложении XAML. Назначьте экземпляр пользовательского элемента управления для размещения свойства [Content](https://docs.microsoft.com/uwp/api/windows.ui.xaml.hosting.desktopwindowxamlsource.content) объекта **десктопвиндовксамлсаурце** в коде.

Полный пример для приложения C++ Win32 см. в следующих проектах в разделе [остров XAML с примером настраиваемого элемента управления](https://github.com/marb2000/XamlIslands/tree/master/1903_Samples/CppWinRT_Win32_App).

* [Самплеусерконтрол](https://github.com/marb2000/XamlIslands/tree/master/1903_Samples/CppWinRT_Win32_App/SampleUserControl): В этом проекте реализуется пользовательский элемент управления типа `MyUserControl` UWP XAML с именем, который содержит текстовое поле, несколько кнопок и поле со списком.
* [MyApp](https://github.com/marb2000/XamlIslands/tree/master/1903_Samples/CppWinRT_Win32_App/MyApp): Это проект приложения UWP с описанными выше изменениями.
* [Самплекппапп](https://github.com/marb2000/XamlIslands/tree/master/1903_Samples/CppWinRT_Win32_App/SampleCppApp): Это проект приложения C++ Win32, в котором размещается пользовательский элемент управления XAML UWP в области XAML.

## <a name="handle-keyboard-layout-and-dpi"></a>Работа с клавиатурой, макетом и DPI

В следующих разделах приводятся рекомендации и ссылки на примеры кода, посвященные использованию сценариев клавиатуры и макета.

* [Ввод с клавиатуры](#keyboard-input)
* [Навигация фокуса клавиатуры](#keyboard-focus-navigation)
* [Обработку изменений макета](#handle-layout-changes)
* [Изменение количества точек на дюйм](#handle-dpi-changes)

### <a name="keyboard-input"></a>Ввод с клавиатуры

Чтобы правильно обрабатывать ввод с клавиатуры для каждого острова XAML, приложение должно передать все сообщения Windows в платформу XAML UWP, чтобы определенные сообщения могли обрабатываться правильно. Для этого в каком-то месте приложения, которое может получить доступ к циклу обработки сообщений, приведите объект **десктопвиндовксамлсаурце** для каждого острова XAML к интерфейсу **IDesktopWindowXamlSourceNative2** com. Затем вызовите метод **претранслатемессаже** этого интерфейса и передайте текущее сообщение.

  * **C++ Win32:** : Приложение может вызвать **претранслатемессаже** непосредственно в основном цикле обработки сообщений. Пример см. в файле [ксамлбридже. cpp](https://github.com/marb2000/XamlIslands/blob/master/1903_Samples/CppWinRT_Win32_App/SampleCppApp/XamlBridge.cpp#L6) в [ C++ примере Win32](https://github.com/marb2000/XamlIslands/tree/master/1903_Samples/CppWinRT_Win32_App).

  * **WPF** Приложение может вызвать **претранслатемессаже** из обработчика событий для события [компонентдиспатчер. среадфилтермессаже](https://docs.microsoft.com/dotnet/api/system.windows.interop.componentdispatcher.threadfiltermessage?view=netframework-4.7.2) . Пример см. в файле [WindowsXamlHostBase.Focus.CS](https://github.com/windows-toolkit/Microsoft.Toolkit.Win32/blob/master/Microsoft.Toolkit.Wpf.UI.XamlHost/WindowsXamlHostBase.Focus.cs#L177) в наборе средств сообщества Windows.

  * **Windows Forms:** Приложение может вызвать **претранслатемессаже** из переопределения метода [Control. PreprocessMessage](https://docs.microsoft.com/en-us/dotnet/api/system.windows.forms.control.preprocessmessage?view=netframework-4.7.2) . Пример см. в файле [WindowsXamlHostBase.KeyboardFocus.CS](https://github.com/windows-toolkit/Microsoft.Toolkit.Win32/blob/master/Microsoft.Toolkit.Forms.UI.XamlHost/WindowsXamlHostBase.KeyboardFocus.cs#L100) в наборе средств сообщества Windows.

### <a name="keyboard-focus-navigation"></a>Навигация фокуса клавиатуры

Когда пользователь переходит по элементам пользовательского интерфейса в приложении, используя клавиатуру (например, нажав клавишу **Tab** или клавишу со стрелкой), необходимо программно переместить фокус в объект **десктопвиндовксамлсаурце** и из него. Когда переход от клавиатуры пользователя достигает **десктопвиндовксамлсаурце**, переместите фокус на первый объект [Windows. UI. XAML. UIElement](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement) в порядке навигации для пользовательского интерфейса, продолжайте перемещать фокус на следующие **элементы. Объекты Windows. UI. XAML. UIElement** , когда пользователь циклически переключается по элементам, а затем перемещает фокус обратно из **десктопвиндовксамлсаурце** и в родительский элемент пользовательского интерфейса.  

API хостинга UWP XAML предоставляет несколько типов и членов, помогающих выполнять эти задачи.

* При переходе от клавиатуры к **десктопвиндовксамлсаурце**возникает событие [GotFocus](https://docs.microsoft.com/uwp/api/windows.ui.xaml.hosting.desktopwindowxamlsource.gotfocus) . Обработайте это событие и программным образом перенесите фокус на первый размещенный элемент **Windows. UI. XAML. UIElement** с помощью метода [навигатефокус](https://docs.microsoft.com/uwp/api/windows.ui.xaml.hosting.desktopwindowxamlsource.navigatefocus) .

* Когда пользователь находится на последнем элементе с фокусом в **десктопвиндовксамлсаурце** и нажимает клавишу **Tab** или клавишу со стрелкой, возникает событие [такефокусрекуестед](https://docs.microsoft.com/uwp/api/windows.ui.xaml.hosting.desktopwindowxamlsource.takefocusrequested) . Обработайте это событие и программно перемещайте фокус на следующий сфокусированный элемент в ведущем приложении. Например, в приложении WPF, в котором **десктопвиндовксамлсаурце** размещается в [системе. Windows. Interop. HwndHost](https://docs.microsoft.com/dotnet/api/system.windows.interop.hwndhost), можно использовать метод [MoveFocus](https://docs.microsoft.com/dotnet/api/system.windows.frameworkelement.movefocus) для перемещения фокуса на следующий сфокусированный элемент в ведущем приложении.

Примеры, демонстрирующие, как это сделать в контексте рабочего примера приложения, см. в следующих файлах кода:

  * **C++/Win32**: См. файл [ксамлбридже. cpp](https://github.com/marb2000/XamlIslands/blob/master/1903_Samples/CppWinRT_Win32_App/SampleCppApp/XamlBridge.cpp) в [ C++ примере Win32](https://github.com/marb2000/XamlIslands/tree/master/1903_Samples/CppWinRT_Win32_App).

  * **WPF** См. файл [WindowsXamlHostBase.Focus.CS](https://github.com/windows-toolkit/Microsoft.Toolkit.Win32/blob/master/Microsoft.Toolkit.Wpf.UI.XamlHost/WindowsXamlHostBase.Focus.cs) в наборе средств сообщества Windows.  

  * **Windows Forms:** См. файл [WindowsXamlHostBase.KeyboardFocus.CS](https://github.com/windows-toolkit/Microsoft.Toolkit.Win32/blob/master/Microsoft.Toolkit.Forms.UI.XamlHost/WindowsXamlHostBase.KeyboardFocus.cs) в наборе средств сообщества Windows.

### <a name="handle-layout-changes"></a>Обработку изменений макета

Когда пользователь изменяет размер родительского элемента пользовательского интерфейса, необходимо выполнить все необходимые изменения макета, чтобы элементы управления UWP отображались должным образом. Ниже приведены некоторые важные сценарии, которые следует учитывать.

* В приложении C++ Win32, когда приложение ОБРАБАТЫВАЕТ сообщение WM_SIZE, оно может переместить размещенный остров XAML с помощью функции [SetWindowPos](https://docs.microsoft.com/windows/desktop/api/winuser/nf-winuser-setwindowpos) . Пример см. в файле кода [SampleApp. cpp](https://github.com/marb2000/XamlIslands/blob/master/19H1_Insider_Samples/CppWin32App_With_Island/SampleCppApp/SampleApp.cpp#L191) в [ C++ примере Win32](https://github.com/marb2000/XamlIslands/tree/master/19H1_Insider_Samples/CppWin32App_With_Island).

* Когда родительский элемент пользовательского интерфейса должен получить размер прямоугольной области, необходимой для размещения элемента **Windows. UI. XAML. UIElement** , который вы размещаете в **десктопвиндовксамлсаурце**, вызовите метод [Measure](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement.measure) элемента **Windows. UI. XAML. UIElement.** . Пример:

    * В приложении WPF это можно сделать из метода [MeasureOverride](https://docs.microsoft.com/dotnet/api/system.windows.frameworkelement.measureoverride) [HwndHost](https://docs.microsoft.com/dotnet/api/system.windows.interop.hwndhost) , на котором размещена **десктопвиндовксамлсаурце**. Пример см. в файле [WindowsXamlHostBase.Layout.CS](https://github.com/windows-toolkit/Microsoft.Toolkit.Win32/blob/master/Microsoft.Toolkit.Wpf.UI.XamlHost/WindowsXamlHostBase.Layout.cs) в наборе средств сообщества Windows.

    * В Windows Forms приложении это можно сделать из метода [Жетпреферредсизе](https://docs.microsoft.com/dotnet/api/system.windows.forms.control.getpreferredsize) [элемента управления](https://docs.microsoft.com/dotnet/api/system.windows.forms.control) , размещающего **десктопвиндовксамлсаурце**. Пример см. в файле [WindowsXamlHostBase.Layout.CS](https://github.com/windows-toolkit/Microsoft.Toolkit.Win32/blob/master/Microsoft.Toolkit.Forms.UI.XamlHost/WindowsXamlHostBase.Layout.cs) в наборе средств сообщества Windows.

* При изменении размера родительского элемента пользовательского интерфейса вызовите метод [упорядочения](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement.arrange) корневого **интерфейса Windows. UI. XAML. UIElement** , который вы размещаете в **десктопвиндовксамлсаурце**. Пример:

    * В приложении WPF это можно сделать из метода [ArrangeOverride](https://docs.microsoft.com/dotnet/api/system.windows.frameworkelement.arrangeoverride) объекта [HwndHost](https://docs.microsoft.com/dotnet/api/system.windows.interop.hwndhost) , в котором размещается **десктопвиндовксамлсаурце**. Пример см. в файле [WindowsXamlHost.Layout.CS](https://github.com/windows-toolkit/Microsoft.Toolkit.Win32/blob/master/Microsoft.Toolkit.Wpf.UI.XamlHost/WindowsXamlHostBase.Layout.cs) в наборе средств сообщества Windows.

    * В Windows Forms приложении это можно сделать из обработчика для события [SizeChanged](https://docs.microsoft.com/dotnet/api/system.windows.forms.control.sizechanged) [элемента управления](https://docs.microsoft.com/dotnet/api/system.windows.forms.control) , размещающего **десктопвиндовксамлсаурце**. Пример см. в файле [WindowsXamlHost.Layout.CS](https://github.com/windows-toolkit/Microsoft.Toolkit.Win32/blob/master/Microsoft.Toolkit.Forms.UI.XamlHost/WindowsXamlHostBase.Layout.cs) в наборе средств сообщества Windows.

### <a name="handle-dpi-changes"></a>Изменение количества точек на дюйм

Платформа XAML UWP обрабатывает изменения DPI для размещаемых элементов управления UWP автоматически (например, когда пользователь перетаскивает окно между мониторами с разными DPI на экране). Для оптимальной работы рекомендуется, чтобы Windows Forms, WPF или C++ приложение Win32 были настроены для отслеживания dpi на уровне каждого монитора.

Чтобы настроить приложение для отслеживания dpi на уровне каждого монитора, добавьте в проект [параллельный манифест сборки](https://docs.microsoft.com/windows/desktop/SbsCs/application-manifests) и задайте **\<\>** для элемента дпиаваренесс значение **PerMonitorV2**. Дополнительные сведения об этом значении см. в описании для [DPI_AWARENESS_CONTEXT_PER_MONITOR_AWARE_V2](https://docs.microsoft.com/windows/desktop/hidpi/dpi-awareness-context).

```xml
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<assembly xmlns="urn:schemas-microsoft-com:asm.v1" manifestVersion="1.0">
    <application xmlns="urn:schemas-microsoft-com:asm.v3">
        <windowsSettings>
            <dpiAwareness xmlns="http://schemas.microsoft.com/SMI/2016/WindowsSettings">PerMonitorV2</dpiAwareness>
        </windowsSettings>
    </application>
</assembly>
```

## <a name="troubleshooting"></a>Устранение неполадок

### <a name="error-using-uwp-xaml-hosting-api-in-a-uwp-app"></a>Ошибка при использовании API размещения UWP XAML в приложении UWP

| Проблемы | Разрешение |
|-------|------------|
| Приложение получает **COMException** со следующим сообщением: "Не удается активировать Десктопвиндовксамлсаурце. Этот тип нельзя использовать в приложении UWP. " или "не удается активировать Виндовсксамлманажер. Этот тип нельзя использовать в приложении UWP. " | Эта ошибка означает, что вы пытаетесь использовать API размещения UWP XAML (в частности, вы пытаетесь создать экземпляр типов [десктопвиндовксамлсаурце](https://docs.microsoft.com/uwp/api/windows.ui.xaml.hosting.desktopwindowxamlsource) или [виндовсксамлманажер](https://docs.microsoft.com/uwp/api/windows.ui.xaml.hosting.windowsxamlmanager) ) в приложении UWP. API размещения на языке UWP XAML предназначен только для использования в классических приложениях UWP, таких как WPF, Windows Forms и C++ приложения Win32. |

### <a name="error-trying-to-use-the-windowsxamlmanager-or-desktopwindowxamlsource-types"></a>Ошибка при попытке использовать типы Виндовсксамлманажер или Десктопвиндовксамлсаурце

| Проблемы | Разрешение |
|-------|------------|
| Приложение получает исключение со следующим сообщением: "Виндовсксамлманажер и Десктопвиндовксамлсаурце поддерживаются для приложений, предназначенных для Windows версии 10.0.18226.0 и более поздних версий. Проверьте манифест приложения или манифест пакета и убедитесь, что свойство Макстестедверсион Обновлено. " | Эта ошибка означает, что приложение попыталось использовать типы **виндовсксамлманажер** или **десктопвиндовксамлсаурце** в API размещения UWP XAML, но операционная система не может определить, было ли приложение создано для Windows 10 версии 1903 или более поздней. API размещения на языке UWP XAML впервые появился в виде предварительной версии в более ранней версией Windows 10, но поддерживается только начиная с Windows 10, версия 1903.</p></p>Чтобы устранить эту проблему, создайте пакет MSIX для приложения и запустите его из пакета или установите пакет NuGet [Microsoft. Toolkit. Win32. UI. SDK](https://www.nuget.org/packages/Microsoft.Toolkit.Win32.UI.SDK) в проект. Дополнительные сведения см. в [этом разделе](#configure-your-project-for-app-deployment). |

### <a name="error-attaching-to-a-window-on-a-different-thread"></a>Ошибка при присоединении к окну в другом потоке

| Проблемы | Разрешение |
|-------|------------|
| Приложение получает **COMException** со следующим сообщением: "Сбой метода Аттачтовиндов, так как указанный HWND был создан в другом потоке". | Эта ошибка означает, что приложение вызвало метод **идесктопвиндовксамлсаурценативе:: аттачтовиндов** и передало ему дескриптор HWND окна, созданного в другом потоке. Этот метод необходимо передать в HWND окна, созданного в том же потоке, что и код, из которого вызывается метод. |

### <a name="error-attaching-to-a-window-on-a-different-top-level-window"></a>Ошибка при присоединении к окну в другом окне верхнего уровня

| Проблемы | Разрешение |
|-------|------------|
| Приложение получает **COMException** со следующим сообщением: "Сбой метода Аттачтовиндов, так как указанный HWND отличается от другого окна верхнего уровня, чем HWND, который ранее был передан в Аттачтовиндов в том же потоке". | Эта ошибка означает, что приложение вызвало метод **идесктопвиндовксамлсаурценативе:: аттачтовиндов** и передало ему HWND окна, которое отличается от окна верхнего уровня, чем окно, указанное в предыдущем вызове этого метода. в том же потоке.</p></p>После того как приложение вызовет **аттачтовиндов** в определенном потоке, все остальные объекты [десктопвиндовксамлсаурце](https://docs.microsoft.com/uwp/api/windows.ui.xaml.hosting.desktopwindowxamlsource) в том же потоке могут присоединяться к Windows, которые являются потомками того же окна верхнего уровня, которое было передано при первом вызове метода **Аттачтовиндов**. Когда все объекты **десктопвиндовксамлсаурце** закрываются для конкретного потока, следующий **десктопвиндовксамлсаурце** затем может быть снова подключен к любому окну.</p></p>Чтобы устранить эту проблему, закройте все объекты **десктопвиндовксамлсаурце** , привязанные к другим окнам верхнего уровня в этом потоке, или создайте новый поток для этого **десктопвиндовксамлсаурце**. |

## <a name="related-topics"></a>См. также

* [Элементы управления UWP в классических приложениях](xaml-islands.md)
* [C++Пример XAML-островов Win32](https://github.com/marb2000/XamlIslands/tree/master/19H1_Insider_Samples/CppWin32App_With_Island)
