---
title: Использование визуального уровня на платформе Win32
description: Используйте визуальный уровень для улучшения пользовательского интерфейса классического приложения Win32.
template: detail.hbs
ms.date: 03/18/2019
ms.topic: article
keywords: UWP, отрисовка, Composition, Win32
ms.author: jimwalk
author: jwmsft
ms.localizationpriority: medium
ms.openlocfilehash: c9b4ec38b0dd1f6eca3f43cfded74c6292c08100
ms.sourcegitcommit: 76e8b4fb3f76cc162aab80982a441bfc18507fb4
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/29/2020
ms.locfileid: "66215190"
---
# <a name="using-the-visual-layer-with-win32"></a>Использование визуального уровня на платформе Win32

Вы можете использовать интерфейсы API Composition среды выполнения Windows (также называемые [визуальным уровнем](/windows/uwp/composition/visual-layer)) в приложениях Win32, чтобы создавать современные интерфейсы для пользователей Windows 10.

Полный код для этого руководства доступен на сайте GitHub: [Пример приложения Win32 HelloComposition](https://github.com/Microsoft/Windows.UI.Composition-Win32-Samples/tree/master/cpp/HelloComposition).

Приложения универсальной платформы Windows, которым необходим точный контроль над композицией пользовательского интерфейса, имеют доступ к пространству имен [Windows.UI.Composition](/uwp/api/windows.ui.composition), которое позволяет точно контролировать композицию и отрисовку пользовательского интерфейса. Однако этот API Composition не ограничивается только приложениями UWP. Классические приложения Win32 могут использовать современные системы композиции в UWP и Windows 10.

## <a name="prerequisites"></a>Предварительные условия

Для использования API размещения для UWP накладываются приведенные ниже предварительные требования.

- Предполагается, что у вас есть опыт разработки приложений с помощью Win32 и UWP. См. также:
  - [Get Started with Win32 and C++](/windows/desktop/learnwin32/learn-to-program-for-windows) (Приступая к работе с Win32 и C++)
  - [Приступая к работе с приложениями для Windows 10](/windows/uwp/get-started/)
  - [Улучшение классического приложения для Windows 10](/windows/uwp/porting/desktop-to-uwp-enhance)
- Windows 10 версии 1803 или более поздней версии.
- Пакет SDK для Windows 10 версии 17134 или более поздней версии.

## <a name="how-to-use-composition-apis-from-a-win32-desktop-application"></a>Как использовать интерфейсы API Composition из классического приложения Win32

При работе с этим руководством вы создадите простое приложение Win32 на C++ и добавите в него элементы композиции UWP. Основное внимание уделяется правильной настройке проекта, созданию кода взаимодействия и отрисовке простых элементов с помощью интерфейсов API Windows Composition. Завершенное приложение выглядит следующим образом.

![Пользовательский интерфейс выполняющегося приложения](images/visual-layer-interop/win32-comp-interop-app-ui.png)

## <a name="create-a-c-win32-project-in-visual-studio"></a>Создание проекта C++ для Win32 в Visual Studio

Первым шагом является создание проекта приложения Win32 в Visual Studio.

Чтобы создать проект приложения Win32 на C++ с именем _HelloComposition_, сделайте следующее.

1. Откройте Visual Studio и выберите **Файл** > **Создать** > **Проект**.

    Появится диалоговое окно **Создать проект**.
1. В категории **Установлены** разверните узел **Visual C++** , а затем выберите **Windows Desktop**.
1. Выберите шаблон **Классическое приложение Windows**.
1. Введите имя _HelloComposition_, а затем нажмите кнопку **ОК**.

    Visual Studio создаст проект и откроет редактор для основного файла приложения.

## <a name="configure-the-project-to-use-windows-runtime-apis"></a>Настройка проекта для использования интерфейсов API среды выполнения Windows

Чтобы применить интерфейсы API среды выполнения Windows (WinRT) в приложении Win32, мы используем C++/WinRT. Чтобы добавить поддержку C++/WinRT, необходимо настроить проект Visual Studio.

(Дополнительные сведения см. в разделе [Добавление поддержки C++/WinRT в проект классического приложения для Windows](/windows/uwp/cpp-and-winrt-apis/get-started.md#modify-a-windows-desktop-application-project-to-add-cwinrt-support) статьи "Начало работы с C++/WinRT".)

1. В меню **Проект** откройте свойства проекта (_Свойства HelloComposition_) и убедитесь, что для следующих параметров заданы указанные значения.

    - Для параметра **Конфигурации** выберите _Все конфигурации_. Для параметра **Платформа** выберите _Все платформы_.
    - **Свойства конфигурации** > **Общие** > **Версия Windows SDK** = _10.0.17763.0_ или более поздняя версия.

    ![Настройка версии пакета SDK](images/visual-layer-interop/sdk-version.png)

    - **C/C++**  > **Язык** > **Стандарт языка C++**  = _Стандарт ISO C++ 17 (/stf:c++17)_

    ![Настройка стандарта языка](images/visual-layer-interop/language-standard.png)

    - **Компоновщик** > **Ввод** > **Дополнительные зависимости** — этот раздел должен содержать _windowsapp.lib_. Если эта зависимость отсутствует в списке, добавьте ее.

    ![Добавление зависимости компоновщика](images/visual-layer-interop/linker-dependencies.png)

1. Изменение предварительно скомпилированного заголовка

    - Переименуйте `stdafx.h` и `stdafx.cpp` в `pch.h` и `pch.cpp` соответственно.
    - Установите для свойства проекта **C/C++**  > **Предварительно скомпилированные заголовки** > **Предварительно скомпилированный заголовочный файл** значение *pch.h*.
    - Найдите `#include "stdafx.h"` и замените его `#include "pch.h"` во всех файлах.

        (**Изменить** > **Найти и заменить** > **Найти в файлах**.)

        ![Поиск и замена в stdafx.h](images/visual-layer-interop/replace-stdafx.png)

    - В `pch.h` добавьте `winrt/base.h` и `unknwn.h`.

        ```cppwinrt
        // reference additional headers your program requires here
        #include <unknwn.h>
        #include <winrt/base.h>
        ```

На этом этапе рекомендуется выполнить сборку проекта, чтобы убедиться в отсутствии ошибок, прежде чем продолжить.

## <a name="create-a-class-to-host-composition-elements"></a>Создание класса для размещения элементов композиции

Чтобы разместить содержимое, созданное с помощью визуального уровня, создайте класс (_CompositionHost_) для управления взаимодействием и создания элементов композиции. Это потребует выполнить большую часть настройки для размещения интерфейсов API Composition. Потребуется:

- получить [Compositor](/uwp/api/windows.ui.composition.compositor), который создает объекты и управляет ими в пространстве имен [Windows.UI.Composition](/uwp/api/windows.ui.composition);
- создать [DispatcherQueueController](/uwp/api/windows.system.dispatcherqueuecontroller)/[DispatcherQueue](/uwp/api/windows.system.dispatcherqueue) для управления задачами интерфейсов API WinRT;
- создать [DesktopWindowTarget](/uwp/api/windows.ui.composition.desktop.desktopwindowtarget) и контейнера Composition для отображения объектов композиции.

Мы сделаем этот класс singleton, чтобы избежать проблем с потоками. Например, можно создать только одну очередь диспетчера для каждого потока, поэтому создание второго экземпляра CompositionHost в том же потоке вызовет ошибку.

> [!TIP]
> При необходимости сверьтесь с полным кодом в конце руководства, чтобы убедиться в том, что весь код добавлен в соответствующе места при работе с этим руководством.

1. Добавьте новый файл класса в проект.
    - В **обозревателе решений** щелкните проект _HelloComposition_ правой кнопкой мыши.
    - В контекстном меню выберите **Добавить** > **Класс**.
    - В диалоговом окне **Добавление класса** укажите имя класса, _CompositionHost.cs_, а затем щелкните **Добавить**.

1. Добавьте заголовки и операторы _using_, необходимые для взаимодействия композиции.
    - В начало файла CompositionHost.h добавьте приведенные ниже операторы _include_.

    ```cppwinrt
    #pragma once
    #include <winrt/Windows.UI.Composition.Desktop.h>
    #include <windows.ui.composition.interop.h>
    #include <DispatcherQueue.h>
    ```

    - В начале файла CompositionHost.cpp после каждого оператора _include_ добавьте оператор _using_.

    ```cppwinrt
    using namespace winrt;
    using namespace Windows::System;
    using namespace Windows::UI;
    using namespace Windows::UI::Composition;
    using namespace Windows::UI::Composition::Desktop;
    using namespace Windows::Foundation::Numerics;
    ```

1. Измените класс, чтобы использовать шаблон singleton.
    - В CompositionHost.h сделайте конструктор частным.
    - Объявите общедоступный статический метод _GetInstance_.

    ```cppwinrt
    class CompositionHost
    {
    public:
        ~CompositionHost();
        static CompositionHost* GetInstance();

    private:
        CompositionHost();
    };
    ```

    - В CompositionHost.cpp добавьте определение метода _GetInstance_.

    ```cppwinrt
    CompositionHost* CompositionHost::GetInstance()
    {
        static CompositionHost instance;
        return &instance;
    }
    ```

1. В CompositionHost.h объявите частные переменные-члены DispatcherQueueController и DesktopWindowTarget для Compositor.

    ```cppwinrt
    winrt::Windows::UI::Composition::Compositor m_compositor{ nullptr };
    winrt::Windows::System::DispatcherQueueController m_dispatcherQueueController{ nullptr };
    winrt::Windows::UI::Composition::Desktop::DesktopWindowTarget m_target{ nullptr };
    ```

1. Добавьте общедоступный метод для инициализации объектов взаимодействия композиции.
    > [!NOTE]
    > В методе _Initialize_ вызываются методы _EnsureDispatcherQueue_, _CreateDesktopWindowTarget_ и _CreateCompositionRoot_. Эти методы создаются на следующих шагах.

    - В CompositionHost.h объявите общедоступный метод _Initialize_, который принимает HWND в качестве аргумента.

    ```cppwinrt
    void Initialize(HWND hwnd);
    ```

    - В CompositionHost.cpp добавьте определение метода _Initialize_.

    ```cppwinrt
    void CompositionHost::Initialize(HWND hwnd)
    {
        EnsureDispatcherQueue();
        if (m_dispatcherQueueController) m_compositor = Compositor();

        CreateDesktopWindowTarget(hwnd);
        CreateCompositionRoot();
    }
    ```

1. Создайте очередь диспетчера в потоке, который будет использовать Windows Composition.

    Необходимо создать Compositor в потоке с очередью диспетчера, поэтому этот метод вызывается первым во время инициализации.

    - В CompositionHost.h объявите частный метод _EnsureDispatcherQueue_.

    ```cppwinrt
    void EnsureDispatcherQueue();
    ```

    - В CompositionHost.cpp добавьте определение метода _EnsureDispatcherQueue_.

    ```cppwinrt
    void CompositionHost::EnsureDispatcherQueue()
    {
        namespace abi = ABI::Windows::System;

        if (m_dispatcherQueueController == nullptr)
        {
            DispatcherQueueOptions options
            {
                sizeof(DispatcherQueueOptions), /* dwSize */
                DQTYPE_THREAD_CURRENT,          /* threadType */
                DQTAT_COM_ASTA                  /* apartmentType */
            };

            Windows::System::DispatcherQueueController controller{ nullptr };
            check_hresult(CreateDispatcherQueueController(options, reinterpret_cast<abi::IDispatcherQueueController**>(put_abi(controller))));
            m_dispatcherQueueController = controller;
        }
    }
    ```

1. Зарегистрируйте окно приложения в качестве цели композиции.
    - В CompositionHost.h объявите частный метод _CreateDesktopWindowTarget_, который принимает HWND в качестве аргумента.

    ```cppwinrt
    void CreateDesktopWindowTarget(HWND window);
    ```

    - В CompositionHost.cpp добавьте определение метода _CreateDesktopWindowTarget_.

    ```cppwinrt
    void CompositionHost::CreateDesktopWindowTarget(HWND window)
    {
        namespace abi = ABI::Windows::UI::Composition::Desktop;

        auto interop = m_compositor.as<abi::ICompositorDesktopInterop>();
        DesktopWindowTarget target{ nullptr };
        check_hresult(interop->CreateDesktopWindowTarget(window, false, reinterpret_cast<abi::IDesktopWindowTarget**>(put_abi(target))));
        m_target = target;
    }
    ```

1. Создайте корневой контейнер для размещения визуальных объектов.
    - В CompositionHost.h объявите частный метод _CreateCompositionRoot_.

    ```cppwinrt
    void CreateCompositionRoot();
    ```

    - В CompositionHost.cpp добавьте определение метода _CreateCompositionRoot_.

    ```cppwinrt
    void CompositionHost::CreateCompositionRoot()
    {
        auto root = m_compositor.CreateContainerVisual();
        root.RelativeSizeAdjustment({ 1.0f, 1.0f });
        root.Offset({ 124, 12, 0 });
        m_target.Root(root);
    }
    ```

Выполните сборку проекта, чтобы убедиться в отсутствии ошибок.

Эти методы настраивают компоненты, необходимые для взаимодействия между визуальным уровнем UWP и интерфейсами API Win32. Теперь вы можете добавить содержимое в приложение.

### <a name="add-composition-elements"></a>Добавление элементов композиции

Теперь, когда инфраструктура настроена, можно создать содержимое Composition, которое необходимо отобразить.

В этом примере вы добавите код, создающий квадрат [SpriteVisual](/uwp/api/windows.ui.composition.spritevisual) произвольного цвета с анимацией, которая удаляет его после небольшой задержки.

1. Добавьте элемент композиции.
    - В CompositionHost.h объявите общедоступный метод _AddElement_, который принимает 3 значения **с плавающей запятой** в качестве аргументов.

    ```cppwinrt
    void AddElement(float size, float x, float y);
    ```

    - В CompositionHost.cpp добавьте определение метода _AddElement_.

    ```cppwinrt
    void CompositionHost::AddElement(float size, float x, float y)
    {
        if (m_target.Root())
        {
            auto visuals = m_target.Root().as<ContainerVisual>().Children();
            auto visual = m_compositor.CreateSpriteVisual();

            auto element = m_compositor.CreateSpriteVisual();
            uint8_t r = (double)(double)(rand() % 255);;
            uint8_t g = (double)(double)(rand() % 255);;
            uint8_t b = (double)(double)(rand() % 255);;

            element.Brush(m_compositor.CreateColorBrush({ 255, r, g, b }));
            element.Size({ size, size });
            element.Offset({ x, y, 0.0f, });

            auto animation = m_compositor.CreateVector3KeyFrameAnimation();
            auto bottom = (float)600 - element.Size().y;
            animation.InsertKeyFrame(1, { element.Offset().x, bottom, 0 });

            using timeSpan = std::chrono::duration<int, std::ratio<1, 1>>;

            std::chrono::seconds duration(2);
            std::chrono::seconds delay(3);

            animation.Duration(timeSpan(duration));
            animation.DelayTime(timeSpan(delay));
            element.StartAnimation(L"Offset", animation);
            visuals.InsertAtTop(element);

            visuals.InsertAtTop(visual);
        }
    }
    ```

## <a name="create-and-show-the-window"></a>Создание и отображение окна

Теперь можно добавить кнопку и содержимое композиции UWP в пользовательский интерфейс Win32.

1. В начало файла HelloComposition.cpp добавьте _CompositionHost.h_, определите BTN_ADD и получите экземпляр CompositionHost.

    ```cppwinrt
    #include "CompositionHost.h"

    // #define MAX_LOADSTRING 100 // This is already in the file.
    #define BTN_ADD 1000

    CompositionHost* compHost = CompositionHost::GetInstance();
    ```

1. В методе `InitInstance` измените размер создаваемого окна. (В этой строке измените `CW_USEDEFAULT, 0` на `900, 672`.)

    ```cppwinrt
    HWND hWnd = CreateWindowW(szWindowClass, szTitle, WS_OVERLAPPEDWINDOW,
        CW_USEDEFAULT, 0, 900, 672, nullptr, nullptr, hInstance, nullptr);
    ```

1. В функции WndProc добавьте `case WM_CREATE` в блок параметров _message_. В этом случае инициализируется CompositionHost и создается кнопка.

    ```cppwinrt
    case WM_CREATE:
    {
        compHost->Initialize(hWnd);
        srand(time(nullptr));

        CreateWindow(TEXT("button"), TEXT("Add element"),
            WS_VISIBLE | WS_CHILD | BS_PUSHBUTTON,
            12, 12, 100, 50,
            hWnd, (HMENU)BTN_ADD, nullptr, nullptr);
    }
    break;
    ```

1. Кроме того, в функции WndProc обработайте нажатие кнопки, чтобы добавить элемент композиции в пользовательский интерфейс. 

    Добавьте `case BTN_ADD` в блок параметров _wmId_ в блоке WM_COMMAND.

    ```cppwinrt
    case BTN_ADD: // addButton click
    {
        double size = (double)(rand() % 150 + 50);
        double x = (double)(rand() % 600);
        double y = (double)(rand() % 200);
        compHost->AddElement(size, x, y);
        break;
    }
    ```

Теперь можно выполнить сборку приложения и запустить его. При необходимости сверьтесь с полным кодом в конце руководства, чтобы убедиться в том, что весь код добавлен в соответствующе места.

Если запустить приложение и нажать кнопку, в пользовательском интерфейсе должны отобразиться анимированные квадраты.

## <a name="additional-resources"></a>Дополнительные ресурсы

- [Пример приложения Win32 HelloComposition (GitHub)](https://github.com/Microsoft/Windows.UI.Composition-Win32-Samples/tree/master/cpp/HelloComposition)
- [Get Started with Win32 and C++](/windows/desktop/learnwin32/learn-to-program-for-windows) (Приступая к работе с Win32 и C++)
- [Приступая к работе с приложениями для Windows 10](/windows/uwp/get-started/) (UWP)
- [Улучшение классического приложения для Windows 10](/windows/uwp/porting/desktop-to-uwp-enhance) (UWP)
- [Пространство имен Windows.UI.Composition](/uwp/api/windows.ui.composition) (UWP)

## <a name="complete-code"></a>Полный код

Ниже приведен полный код класса CompositionHost и метода InitInstance.

### <a name="compositionhosth"></a>CompositionHost.h

```cppwinrt
#pragma once
#include <winrt/Windows.UI.Composition.Desktop.h>
#include <windows.ui.composition.interop.h>
#include <DispatcherQueue.h>

class CompositionHost
{
public:
    ~CompositionHost();
    static CompositionHost* GetInstance();

    void Initialize(HWND hwnd);
    void AddElement(float size, float x, float y);

private:
    CompositionHost();

    void CreateDesktopWindowTarget(HWND window);
    void EnsureDispatcherQueue();
    void CreateCompositionRoot();

    winrt::Windows::UI::Composition::Compositor m_compositor{ nullptr };
    winrt::Windows::UI::Composition::Desktop::DesktopWindowTarget m_target{ nullptr };
    winrt::Windows::System::DispatcherQueueController m_dispatcherQueueController{ nullptr };
};
```

### <a name="compositionhostcpp"></a>CompositionHost.cpp

```cppwinrt
#include "pch.h"
#include "CompositionHost.h"

using namespace winrt;
using namespace Windows::System;
using namespace Windows::UI;
using namespace Windows::UI::Composition;
using namespace Windows::UI::Composition::Desktop;
using namespace Windows::Foundation::Numerics;

CompositionHost::CompositionHost()
{
}

CompositionHost* CompositionHost::GetInstance()
{
    static CompositionHost instance;
    return &instance;
}

CompositionHost::~CompositionHost()
{
}

void CompositionHost::Initialize(HWND hwnd)
{
    EnsureDispatcherQueue();
    if (m_dispatcherQueueController) m_compositor = Compositor();

    if (m_compositor)
    {
        CreateDesktopWindowTarget(hwnd);
        CreateCompositionRoot();
    }
}

void CompositionHost::EnsureDispatcherQueue()
{
    namespace abi = ABI::Windows::System;

    if (m_dispatcherQueueController == nullptr)
    {
        DispatcherQueueOptions options
        {
            sizeof(DispatcherQueueOptions), /* dwSize */
            DQTYPE_THREAD_CURRENT,          /* threadType */
            DQTAT_COM_ASTA                  /* apartmentType */
        };

        Windows::System::DispatcherQueueController controller{ nullptr };
        check_hresult(CreateDispatcherQueueController(options, reinterpret_cast<abi::IDispatcherQueueController**>(put_abi(controller))));
        m_dispatcherQueueController = controller;
    }
}

void CompositionHost::CreateDesktopWindowTarget(HWND window)
{
    namespace abi = ABI::Windows::UI::Composition::Desktop;

    auto interop = m_compositor.as<abi::ICompositorDesktopInterop>();
    DesktopWindowTarget target{ nullptr };
    check_hresult(interop->CreateDesktopWindowTarget(window, false, reinterpret_cast<abi::IDesktopWindowTarget**>(put_abi(target))));
    m_target = target;
}

void CompositionHost::CreateCompositionRoot()
{
    auto root = m_compositor.CreateContainerVisual();
    root.RelativeSizeAdjustment({ 1.0f, 1.0f });
    root.Offset({ 124, 12, 0 });
    m_target.Root(root);
}

void CompositionHost::AddElement(float size, float x, float y)
{
    if (m_target.Root())
    {
        auto visuals = m_target.Root().as<ContainerVisual>().Children();
        auto visual = m_compositor.CreateSpriteVisual();

        auto element = m_compositor.CreateSpriteVisual();
        uint8_t r = (double)(double)(rand() % 255);;
        uint8_t g = (double)(double)(rand() % 255);;
        uint8_t b = (double)(double)(rand() % 255);;

        element.Brush(m_compositor.CreateColorBrush({ 255, r, g, b }));
        element.Size({ size, size });
        element.Offset({ x, y, 0.0f, });

        auto animation = m_compositor.CreateVector3KeyFrameAnimation();
        auto bottom = (float)600 - element.Size().y;
        animation.InsertKeyFrame(1, { element.Offset().x, bottom, 0 });

        using timeSpan = std::chrono::duration<int, std::ratio<1, 1>>;

        std::chrono::seconds duration(2);
        std::chrono::seconds delay(3);

        animation.Duration(timeSpan(duration));
        animation.DelayTime(timeSpan(delay));
        element.StartAnimation(L"Offset", animation);
        visuals.InsertAtTop(element);

        visuals.InsertAtTop(visual);
    }
}
```

### <a name="hellocompositioncpp-partial"></a>HelloComposition.cpp (неполный)

```cppwinrt
#include "pch.h"
#include "HelloComposition.h"
#include "CompositionHost.h"

#define MAX_LOADSTRING 100
#define BTN_ADD 1000

CompositionHost* compHost = CompositionHost::GetInstance();

// Global Variables:

// ...
// ... code not shown ...
// ...

BOOL InitInstance(HINSTANCE hInstance, int nCmdShow)
{
   hInst = hInstance; // Store instance handle in our global variable

   HWND hWnd = CreateWindowW(szWindowClass, szTitle, WS_OVERLAPPEDWINDOW,
      CW_USEDEFAULT, 0, 900, 672, nullptr, nullptr, hInstance, nullptr);

// ...
// ... code not shown ...
// ...
}

// ...

LRESULT CALLBACK WndProc(HWND hWnd, UINT message, WPARAM wParam, LPARAM lParam)
{
    switch (message)
    {
// Add this...
    case WM_CREATE:
    {
        compHost->Initialize(hWnd);
        srand(time(nullptr));

        CreateWindow(TEXT("button"), TEXT("Add element"),
            WS_VISIBLE | WS_CHILD | BS_PUSHBUTTON,
            12, 12, 100, 50,
            hWnd, (HMENU)BTN_ADD, nullptr, nullptr);
    }
    break;
// ...
    case WM_COMMAND:
    {
        int wmId = LOWORD(wParam);
        // Parse the menu selections:
        switch (wmId)
        {
        case IDM_ABOUT:
            DialogBox(hInst, MAKEINTRESOURCE(IDD_ABOUTBOX), hWnd, About);
            break;
        case IDM_EXIT:
            DestroyWindow(hWnd);
            break;
// Add this...
        case BTN_ADD: // addButton click
        {
            double size = (double)(rand() % 150 + 50);
            double x = (double)(rand() % 600);
            double y = (double)(rand() % 200);
            compHost->AddElement(size, x, y);
            break;
        }
// ...
        default:
            return DefWindowProc(hWnd, message, wParam, lParam);
        }
    }
    break;
    case WM_PAINT:
    {
        PAINTSTRUCT ps;
        HDC hdc = BeginPaint(hWnd, &ps);
        // TODO: Add any drawing code that uses hdc here...
        EndPaint(hWnd, &ps);
    }
    break;
    case WM_DESTROY:
        PostQuitMessage(0);
        break;
    default:
        return DefWindowProc(hWnd, message, wParam, lParam);
    }
    return 0;
}

// ...
// ... code not shown ...
// ...
```