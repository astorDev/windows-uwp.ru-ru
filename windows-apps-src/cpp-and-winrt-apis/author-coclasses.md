---
description: C++/WinRT можно использовать для разработки классических компонентов COM, так как эта проекция языка помогает создавать классы среды выполнения Windows.
title: Создание компонентов COM с помощью C++/WinRT
ms.date: 04/24/2019
ms.topic: article
keywords: windows 10, UWP, standard, c++, cpp, winrt, projection, author, COM, component
ms.localizationpriority: medium
ms.custom: RS5
ms.openlocfilehash: 8da62908d33c053cee4ba3f55645be9dbdcaada9
ms.sourcegitcommit: b9268ca84af56ee1c4f4ac0314e2452193369f01
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/17/2019
ms.locfileid: "68293372"
---
# <a name="author-com-components-with-cwinrt"></a>Создание компонентов COM с помощью C++/WinRT

[C++/WinRT](/windows/uwp/cpp-and-winrt-apis/intro-to-using-cpp-with-winrt) можно использовать для разработки классических компонентов модели COM (или компонентных классов), так как эта проекция языка помогает создавать классы среды выполнения Windows. Дале показано, как это сделать.

## <a name="how-cwinrt-behaves-by-default-with-respect-to-com-interfaces"></a>Поведение C++/WinRT по умолчанию при использовании COM-интерфейсов

В C++/ WinRT [**winrt::implements**](/uwp/cpp-ref-for-winrt/implements) является базовым шаблоном, от которого напрямую или косвенно наследуются классы среды выполнения и фабрики активации.

По умолчанию **winrt::implements** поддерживает только интерфейсы на основе [**IInspectable**](/windows/win32/api/inspectable/nn-inspectable-iinspectable) и автоматически игнорирует классические COM-интерфейсы. Любые вызовы **QueryInterface** (QI) классических COM-интерфейсов завершаются ошибкой **E_NOINTERFACE**.

Далее показано, как справиться с этой ситуацией, но сначала ознакомьтесь с примером кода, демонстрирующим, что происходит по умолчанию.

```idl
// Sample.idl
runtimeclass Sample
{
    Sample();
    void DoWork();
}

// Sample.h
#include "pch.h"
#include <shobjidl.h> // Needed only for this file.

namespace winrt::MyProject
{
    struct Sample : implements<Sample, IInitializeWithWindow>
    {
        IFACEMETHOD(Initialize)(HWND hwnd);
        void DoWork();
    }
}
```

А вот код клиента для получения класса **Sample**.

```cppwinrt
// Client.cpp
Sample sample; // Construct a Sample object via its projection.

// This next line crashes, because the QI for IInitializeWithWindow fails.
sample.as<IInitializeWithWindow>()->Initialize(hwnd); 
```

Хорошо то, что для вызова **winrt::implements**, обеспечивающего поддержку классических COM-интерфейсов, требуется просто добавить `unknwn.h`, прежде чем добавлять заголовки C++/WinRT.

Это можно сделать явно или косвенно, добавив некоторые другие файлы заголовка, например `ole2.h`. Один из рекомендуемых методов — добавить файл заголовка `wil\cppwinrt.h`, который является частью [библиотеки реализации Windows (WIL)](https://github.com/Microsoft/wil). Использование файла заголовка `wil\cppwinrt.h` гарантирует добавление `unknwn.h` перед `winrt/base.h`, а также обеспечивает взаимное распознавание исключений и кодов ошибок в C++/WinRT и WIL.

## <a name="a-simple-example-of-a-com-component"></a>Простой пример COM-компонента

Ниже приведен простой пример COM-компонента, написанного с использованием C++/WinRT. Это полный пример мини-приложения, работу которого можно проверить, вставив код в `pch.h` и `main.cpp` нового проекта **консольного приложения для Windows (C++/WinRT)** .

```cppwinrt
// pch.h
#pragma once
#include <unknwn.h>
#include <winrt/Windows.Foundation.h>

// main.cpp : Defines the entry point for the console application.
#include "pch.h"

struct __declspec(uuid("ddc36e02-18ac-47c4-ae17-d420eece2281")) IMyComInterface : ::IUnknown
{
    virtual HRESULT __stdcall Call() = 0;
};

using namespace winrt;
using namespace Windows::Foundation;

int main()
{
    winrt::init_apartment();

    struct MyCoclass : winrt::implements<MyCoclass, IPersist, IStringable, IMyComInterface>
    {
        HRESULT __stdcall Call() noexcept override
        {
            return S_OK;
        }

        HRESULT __stdcall GetClassID(CLSID* id) noexcept override
        {
            *id = IID_IPersist; // Doesn't matter what we return, for this example.
            return S_OK;
        }

        winrt::hstring ToString()
        {
            return L"MyCoclass as a string";
        }
    };

    auto mycoclass_instance{ winrt::make<MyCoclass>() };
    CLSID id{};
    winrt::check_hresult(mycoclass_instance->GetClassID(&id));
    winrt::check_hresult(mycoclass_instance.as<IMyComInterface>()->Call());
}
```

Ознакомьтесь также со статьей [Использование компонентов COM с помощью C++/WinRT](consume-com.md).

## <a name="a-more-realistic-and-interesting-example"></a>Более реалистичный и интересный пример

В остальной части этой статьи описан процесс создания минимального проекта консольного приложения, использующего C++/WinRT для реализации базового компонентного класса (компонента COM или класса COM) и фабрики классов. В примере приложения показано, как доставить всплывающее уведомление с помощью кнопки обратного вызова, а компонентный класс (который реализует интерфейс COM **INotificationActivationCallback**) позволяет запускать и вызывать приложение, когда пользователь нажимает эту кнопку во всплывающем уведомлении.

Дополнительные сведения об области применения компонента всплывающего уведомления можно найти в статье [Отправка локального всплывающего уведомления](/windows/uwp/design/shell/tiles-and-notifications/send-local-toast). Однако ни один из примеров кода в этом разделе документации не использует C++/WinRT, поэтому мы рекомендуем вам использовать код, приведенный в этой статье.

## <a name="create-a-windows-console-application-project-toastandcallback"></a>Создание проекта консольного приложения для Windows (ToastAndCallback)

Начните с создания проекта в Microsoft Visual Studio. Создайте проект **консольного приложения для Windows (C++/WinRT)** и назовите его *ToastAndCallback*.

Откройте файл `pch.h` и добавьте `#include <unknwn.h>` перед добавлениями для любого заголовка C++/WinRT. Ниже приведен результат. Вы можете заменить содержимое файла `pch.h` этим фрагментом.

```cppwinrt
// pch.h
#pragma once
#include <unknwn.h>
#include <winrt/Windows.Foundation.h>
```

Откройте файл `main.cpp` и удалите директивы using, которые создает шаблон проекта. Вместо них вставьте следующий код (с помощью которого можно получить необходимые библиотеки, заголовки и имена типов). Ниже приведен результат. Вы можете заменить содержимое файла `main.cpp` этим фрагментом (мы также удалили код из `main` во фрагменте ниже, так как позже мы заменим эту функцию).

```cppwinrt
// main.cpp : Defines the entry point for the console application.

#include "pch.h"

#pragma comment(lib, "advapi32")
#pragma comment(lib, "ole32")
#pragma comment(lib, "shell32")

#include <iomanip>
#include <iostream>
#include <notificationactivationcallback.h>
#include <propkey.h>
#include <propvarutil.h>
#include <shlobj.h>
#include <winrt/Windows.UI.Notifications.h>
#include <winrt/Windows.Data.Xml.Dom.h>

using namespace winrt;
using namespace Windows::Data::Xml::Dom;
using namespace Windows::UI::Notifications;

int main() { }
```

Сборка проекта еще не будет выполнена. После добавления кода вам будет предложено выполнить сборку и запустить проект.

## <a name="implement-the-coclass-and-class-factory"></a>Реализация компонентного класса и фабрики классов

В C++/WinRT вы реализуете компонентные классы и фабрики классов на основе базовой структуры [**winrt::implements**](/uwp/cpp-ref-for-winrt/implements). Вставьте этот код сразу после трех директив using, показанных выше (и перед `main`), чтобы реализовать компонент активатора COM всплывающего уведомления.

```cppwinrt
static constexpr GUID callback_guid // BAF2FA85-E121-4CC9-A942-CE335B6F917F
{
    0xBAF2FA85, 0xE121, 0x4CC9, {0xA9, 0x42, 0xCE, 0x33, 0x5B, 0x6F, 0x91, 0x7F}
};

std::wstring const this_app_name{ L"ToastAndCallback" };

struct callback : winrt::implements<callback, INotificationActivationCallback>
{
    HRESULT __stdcall Activate(
        LPCWSTR app,
        LPCWSTR args,
        [[maybe_unused]] NOTIFICATION_USER_INPUT_DATA const* data,
        [[maybe_unused]] ULONG count) noexcept final
    {
        try
        {
            std::wcout << this_app_name << L" has been called back from a notification." << std::endl;
            std::wcout << L"Value of the 'app' parameter is '" << app << L"'." << std::endl;
            std::wcout << L"Value of the 'args' parameter is '" << args << L"'." << std::endl;
            return S_OK;
        }
        catch (...)
        {
            return winrt::to_hresult();
        }
    }
};

struct callback_factory : implements<callback_factory, IClassFactory>
{
    HRESULT __stdcall CreateInstance(
        IUnknown* outer,
        GUID const& iid,
        void** result) noexcept final
    {
        *result = nullptr;

        if (outer)
        {
            return CLASS_E_NOAGGREGATION;
        }

        return make<callback>()->QueryInterface(iid, result);
    }

    HRESULT __stdcall LockServer(BOOL) noexcept final
    {
        return S_OK;
    }
};
```

Представленная выше реализация компонентного класса выполняется по той же схеме, которая продемонстрирована в статье [Создание интерфейсов API с помощью C++/WinRT](/windows/uwp/cpp-and-winrt-apis/author-apis#if-youre-not-authoring-a-runtime-class). Это значит, что вы можете реализовать интерфейсы COM и интерфейсы среды выполнения Windows по тому же принципу. Компоненты COM и классы среды выполнения Windows предоставляют доступ к функциям с помощью интерфейсов. Каждый интерфейс COM в конечном итоге наследуется от [**интерфейса IUnknown**](https://docs.microsoft.com/windows/desktop/api/unknwn/nn-unknwn-iunknown). Среда выполнения Windows основана на COM с одним исключением — интерфейсы среды выполнения Windows в конечном итоге наследуются от [**интерфейса IInspectable**](/windows/desktop/api/inspectable/nn-inspectable-iinspectable) (а **IInspectable** — от **IUnknown**).

В компонентном классе в приведенном выше коде мы реализуем метод **INotificationActivationCallback::Activate**. Этот метод является функцией, которая вызывается, когда пользователь нажимает кнопку обратного вызова во всплывающем уведомлении. Но прежде чем можно будет вызвать эту функцию, необходимо создать экземпляр компонентного класса. Это задача функции **IClassFactory::CreateInstance**.

Компонентный класс, который мы только что реализовали, известен как *активатор COM* для уведомлений. Он имеет свой идентификатор класса (CLSID) в виде идентификатора `callback_guid` (типа **GUID**), который можно увидеть выше. Мы будем использовать этот идентификатор позже — в виде ярлыка меню "Пуск" и записи реестра Windows. Идентификатор CLSID активатора COM и путь к связанному с ним серверу COM (который является путем к исполняемому файлу, который мы здесь создаем) — это механизм, с помощью которого всплывающее уведомление получает сведения о классе, для которого нужно создать экземпляр при нажатии кнопки обратного вызова (независимо от того, нажато ли уведомление в Центре уведомлений).

## <a name="best-practices-for-implementing-com-methods"></a>Рекомендации для реализации методов COM

Методики для обработки ошибок и управления ресурсами можно применять совместно. Удобнее и практичнее использовать исключения, а не коды ошибок. Если вы используете идиому "получение ресурса — инициализация" (RAII), можно предотвратить выполнение явной проверки на коды ошибок и явного освобождения ресурсов. Такие явные проверки делают ваш код еще более запутанным, и в результате ошибки могут остаться незамеченными. Вместо этого используйте RAII и создавайте исключения. Таким образом, выделения ресурсов будут защищены от исключений, а код будет простым.

Однако вы не должны позволять исключениям избегать реализации своего метода COM. Это можно обеспечить, используя описатель `noexcept` в методах COM. Исключения можно создавать в любом расположении графика вызовов метода до тех пор, пока вы обрабатываете их до выхода из метода. Если вы используете параметр `noexcept`, а затем разрешаете исключению избежать метод, работа приложения будет завершена.

## <a name="add-helper-types-and-functions"></a>Добавление вспомогательных типов и функций

На этом этапе мы добавим несколько вспомогательных типов и функций, которые используются в остальной части кода. Итак, добавьте следующий код непосредственно перед функцией `main`.

```cppwinrt
struct prop_variant : PROPVARIANT
{
    prop_variant() noexcept : PROPVARIANT{}
    {
    }

    ~prop_variant() noexcept
    {
        clear();
    }

    void clear() noexcept
    {
        WINRT_VERIFY_(S_OK, ::PropVariantClear(this));
    }
};

struct registry_traits
{
    using type = HKEY;

    static void close(type value) noexcept
    {
        WINRT_VERIFY_(ERROR_SUCCESS, ::RegCloseKey(value));
    }

    static constexpr type invalid() noexcept
    {
        return nullptr;
    }
};

using registry_key = winrt::handle_type<registry_traits>;

std::wstring get_module_path()
{
    std::wstring path(100, L'?');
    uint32_t path_size{};
    DWORD actual_size{};

    do
    {
        path_size = static_cast<uint32_t>(path.size());
        actual_size = ::GetModuleFileName(nullptr, path.data(), path_size);

        if (actual_size + 1 > path_size)
        {
            path.resize(path_size * 2, L'?');
        }
    } while (actual_size + 1 > path_size);

    path.resize(actual_size);
    return path;
}

std::wstring get_shortcut_path()
{
    std::wstring format{ LR"(%ProgramData%\Microsoft\Windows\Start Menu\Programs\)" };
    format += (this_app_name + L".lnk");

    auto required{ ::ExpandEnvironmentStrings(format.c_str(), nullptr, 0) };
    std::wstring path(required - 1, L'?');
    ::ExpandEnvironmentStrings(format.c_str(), path.data(), required);
    return path;
}
```

## <a name="implement-the-remaining-functions-and-the-wmain-entry-point-function"></a>Реализация остальных функций и функции точки входа wmain

Удалите функцию `main` и вместо нее вставьте этот фрагмент кода, который включает в себя код для регистрации компонентного класса и код для доставки всплывающего уведомления, с помощью которого можно обратно запустить приложение.

```cppwinrt
void register_callback()
{
    DWORD registration{};

    winrt::check_hresult(::CoRegisterClassObject(
        callback_guid,
        make<callback_factory>().get(),
        CLSCTX_LOCAL_SERVER,
        REGCLS_SINGLEUSE,
        &registration));
}

void create_shortcut()
{
    auto link{ winrt::create_instance<IShellLink>(CLSID_ShellLink) };
    std::wstring module_path{ get_module_path() };
    winrt::check_hresult(link->SetPath(module_path.c_str()));

    auto store = link.as<IPropertyStore>();
    prop_variant value;
    winrt::check_hresult(::InitPropVariantFromString(this_app_name.c_str(), &value));
    winrt::check_hresult(store->SetValue(PKEY_AppUserModel_ID, value));
    value.clear();
    winrt::check_hresult(::InitPropVariantFromCLSID(callback_guid, &value));
    winrt::check_hresult(store->SetValue(PKEY_AppUserModel_ToastActivatorCLSID, value));

    auto file{ store.as<IPersistFile>() };
    std::wstring shortcut_path{ get_shortcut_path() };
    winrt::check_hresult(file->Save(shortcut_path.c_str(), TRUE));

    std::wcout << L"In " << shortcut_path << L", created a shortcut to " << module_path << std::endl;
}

void update_registry()
{
    std::wstring key_path{ LR"(SOFTWARE\Classes\CLSID\{????????-????-????-????-????????????})" };
    ::StringFromGUID2(callback_guid, key_path.data() + 23, 39);
    key_path += LR"(\LocalServer32)";
    registry_key key;

    winrt::check_win32(::RegCreateKeyEx(
        HKEY_CURRENT_USER,
        key_path.c_str(),
        0,
        nullptr,
        0,
        KEY_WRITE,
        nullptr,
        key.put(),
        nullptr));
    ::RegDeleteValue(key.get(), nullptr);

    std::wstring path{ get_module_path() };

    winrt::check_win32(::RegSetValueEx(
        key.get(),
        nullptr,
        0,
        REG_SZ,
        reinterpret_cast<BYTE const*>(path.c_str()),
        static_cast<uint32_t>((path.size() + 1) * sizeof(wchar_t))));

    std::wcout << L"In " << key_path << L", registered local server at " << path << std::endl;
}

void create_toast()
{
    XmlDocument xml;

    std::wstring toastPayload
    {
        LR"(
<toast>
  <visual>
    <binding template='ToastGeneric'>
      <text>)"
    };
    toastPayload += this_app_name;
    toastPayload += LR"(
      </text>
    </binding>
  </visual>
  <actions>
    <action content='Call back )";
    toastPayload += this_app_name;
    toastPayload += LR"(
' arguments='the_args' activationKind='Foreground' />
  </actions>
</toast>)";
    xml.LoadXml(toastPayload);

    ToastNotification toast{ xml };
    ToastNotifier notifier{ ToastNotificationManager::CreateToastNotifier(this_app_name) };
    notifier.Show(toast);
    ::Sleep(50); // Give the callback chance to display.
}

void LaunchedNormally(HANDLE, INPUT_RECORD &, DWORD &);
void LaunchedFromNotification(HANDLE, INPUT_RECORD &, DWORD &);

int wmain(int argc, wchar_t * argv[], wchar_t * /* envp */[])
{
    winrt::init_apartment();

    register_callback();

    HANDLE consoleHandle{ ::GetStdHandle(STD_INPUT_HANDLE) };
    INPUT_RECORD buffer{};
    DWORD events{};
    ::FlushConsoleInputBuffer(consoleHandle);

    if (argc == 1)
    {
        LaunchedNormally(consoleHandle, buffer, events);
    }
    else if (argc == 2 && wcscmp(argv[1], L"-Embedding") == 0)
    {
        LaunchedFromNotification(consoleHandle, buffer, events);
    }
}

void LaunchedNormally(HANDLE consoleHandle, INPUT_RECORD & buffer, DWORD & events)
{
    try
    {
        bool runningAsAdmin{ ::IsUserAnAdmin() == TRUE };
        std::wcout << this_app_name << L" is running" << (runningAsAdmin ? L" (administrator)." : L" (NOT as administrator).") << std::endl;

        if (runningAsAdmin)
        {
            create_shortcut();
            update_registry();
        }

        std::wcout << std::endl << L"Press 'T' to display a toast notification (press any other key to exit)." << std::endl;

        ::ReadConsoleInput(consoleHandle, &buffer, 1, &events);
        if (towupper(buffer.Event.KeyEvent.uChar.UnicodeChar) == L'T')
        {
            create_toast();
        }
    }
    catch (winrt::hresult_error const& e)
    {
        std::wcout << L"Error: " << e.message().c_str() << L" (" << std::hex << std::showbase << std::setw(8) << static_cast<uint32_t>(e.code()) << L")" << std::endl;
    }
}

void LaunchedFromNotification(HANDLE consoleHandle, INPUT_RECORD & buffer, DWORD & events)
{
    ::Sleep(50); // Give the callback chance to display its message.
    std::wcout << std::endl << L"Press any key to exit." << std::endl;
    ::ReadConsoleInput(consoleHandle, &buffer, 1, &events);
}
```

## <a name="how-to-test-the-example-application"></a>Метод проверки примера приложения

Создайте приложение, а затем запустите его хотя бы один раз от имени администратора, чтобы запустить код регистрации и других настроек. Это можно сделать, запустив Visual Studio от имени администратора, а затем запустив приложение из Visual Studio. Щелкните Visual Studio правой кнопкой мыши на панели задач, чтобы отобразить список переходов. Щелкните Visual Studio правой кнопкой мыши в списке переходов и выберите **Запуск от имени администратора**. Примите запрос и откройте проект. Когда вы запускаете приложение, отображается сообщение, в котором указано, запущено ли приложение от имени администратора. Если это не так, регистрация и другие настройки не будут выполняться. Эту регистрацию и другие настройки необходимо запустить хотя бы один раз, чтобы приложение работало правильно.

Независимо от того, запущено ли приложение от имени администратора, нажмите клавишу T, чтобы отобразилось всплывающее уведомление. Затем вы можете нажать кнопку **Call back ToastAndCallback** (Обратный вызов ToastAndCallback) непосредственно из всплывающего уведомления или из центра уведомлений. Таким образом запустится приложение, будет создан экземпляр компонентного класса и будет выполнен метод **INotificationActivationCallback::Activate**.

## <a name="in-process-com-server"></a>Внутрипроцессный сервер COM

Приведенный выше пример приложения *ToastAndCallback* функционирует как локальный (или внепроцессный) сервер COM. На это указывает раздел реестра Windows [LocalServer32](/windows/desktop/com/localserver32), который используется для регистрации CLSID компонентного класса. Локальный сервер COM размещает свои компонентные классы в исполняемом двоичном файле (`.exe`).

Кроме того (и, возможно, более вероятно), вы можете разместить компонентные классы внутри библиотеки DLL (`.dll`). Сервер COM в форме DLL называется внутрипроцессным сервером COM. Он обозначается идентификаторами CLSID, зарегистрированными с помощью раздела реестра Windows [InprocServer32](/windows/desktop/com/inprocserver32).

### <a name="create-a-dynamic-link-library-dll-project"></a>Создание проекта библиотеки динамической компоновки (DLL)

Вы можете начать задачу создания внутрипроцессного сервера COM, создав проект в Microsoft Visual Studio. Создайте проект **Visual C++**  > **Классическое приложение для Windows** > **Библиотека динамической компоновки (DLL)** .

Чтобы добавить поддержку C++/WinRT в новый проект, выполните действия, описанные в разделе [Добавление поддержки C++/WinRT в проекте классического приложения для Windows](/windows/uwp/cpp-and-winrt-apis/get-started#modify-a-windows-desktop-application-project-to-add-cwinrt-support).

### <a name="implement-the-coclass-class-factory-and-in-proc-server-exports"></a>Реализация компонентного класса, фабрики класса и операций экспорта внутрипроцессного сервера

Откройте файл `dllmain.cpp` и добавьте в него приведенный ниже текст кода.

Если у вас уже есть библиотека DLL, которая реализует классы среды выполнения Windows C++/WinRT, то у вас уже будет функция **DllCanUnloadNow**, показанная ниже. Если вы хотите добавить компонентные классы в эту библиотеку DLL, вы можете добавить функцию **DllGetClassObject**.

Если у вас нет кода [библиотеки шаблонов C++ для среды выполнения Windows (WRL)](/cpp/windows/windows-runtime-cpp-template-library-wrl), с которым вы хотите обеспечить совместимость, вы можете удалить части WRL из приведенного кода.

```cppwinrt
// dllmain.cpp

struct MyCoclass : winrt::implements<MyCoclass, IPersist>
{
    HRESULT STDMETHODCALLTYPE GetClassID(CLSID* id) noexcept override
    {
        *id = IID_IPersist; // Doesn't matter what we return, for this example.
        return S_OK;
    }
};

struct __declspec(uuid("85d6672d-0606-4389-a50a-356ce7bded09"))
    MyCoclassFactory : winrt::implements<MyCoclassFactory, IClassFactory>
{
    HRESULT STDMETHODCALLTYPE CreateInstance(IUnknown *pUnkOuter, REFIID riid, void **ppvObject) noexcept override
    {
        try
        {
            return winrt::make<MyCoclass>()->QueryInterface(riid, ppvObject);
        }
        catch (...)
        {
            return winrt::to_hresult();
        }
    }

    HRESULT STDMETHODCALLTYPE LockServer(BOOL fLock) noexcept override
    {
        // ...
        return S_OK;
    }

    // ...
};

HRESULT __stdcall DllCanUnloadNow()
{
#ifdef _WRL_MODULE_H_
    if (!::Microsoft::WRL::Module<::Microsoft::WRL::InProc>::GetModule().Terminate())
    {
        return S_FALSE;
    }
#endif

    if (winrt::get_module_lock())
    {
        return S_FALSE;
    }

    winrt::clear_factory_cache();
    return S_OK;
}

HRESULT __stdcall DllGetClassObject(GUID const& clsid, GUID const& iid, void** result)
{
    try
    {
        *result = nullptr;

        if (clsid == __uuidof(MyCoclassFactory))
        {
            return winrt::make<MyCoclassFactory>()->QueryInterface(iid, result);
        }

#ifdef _WRL_MODULE_H_
        return ::Microsoft::WRL::Module<::Microsoft::WRL::InProc>::GetModule().GetClassObject(clsid, iid, result);
#else
        return winrt::hresult_class_not_available().to_abi();
#endif
    }
    catch (...)
    {
        return winrt::to_hresult();
    }
}
```

### <a name="support-for-weak-references"></a>Поддержка для слабых ссылок

Ознакомьтесь также с разделом [Слабые ссылки в C++/WinRT](weak-references.md#weak-references-in-cwinrt).

C++/WinRT (в частности, шаблон базовой структуры [**winrt::Implements**](/uwp/cpp-ref-for-winrt/implements)) реализует [**IWeakReferenceSource**](/windows/desktop/api/weakreference/nn-weakreference-iweakreferencesource), если тип реализует [**IInspectable**](/windows/desktop/api/inspectable/nn-inspectable-iinspectable) (или любой интерфейс, который является производным от **IInspectable**).

Это связано с тем, что **IWeakReferenceSource** и [**IWeakReference**](/windows/desktop/api/weakreference/nn-weakreference-iweakreference) предназначены для типов среды выполнения Windows. Таким образом, вы можете включить поддержку слабых ссылок для своего компонентного класса, просто добавив **winrt::Windows::Foundation::IInspectable** (или интерфейс, который является производным от **IInspectable**) к вашей реализации.

```cppwinrt
struct MyCoclass : winrt::implements<MyCoclass, IMyComInterface, winrt::Windows::Foundation::IInspectable>
{
    //  ...
};
```

## <a name="important-apis"></a>Важные API
* [Интерфейс IInspectable](/windows/desktop/api/inspectable/nn-inspectable-iinspectable)
* [Интерфейс IUnknown](https://docs.microsoft.com/windows/desktop/api/unknwn/nn-unknwn-iunknown)
* [Шаблон структуры winrt::implements](/uwp/cpp-ref-for-winrt/implements)

## <a name="related-topics"></a>Статьи по теме
* [Создание интерфейсов API с помощью C++/WinRT](/windows/uwp/cpp-and-winrt-apis/author-apis)
* [Использование компонентов COM с помощью C++/WinRT](consume-com.md)
* [Отправка локального всплывающего уведомления](/windows/uwp/design/shell/tiles-and-notifications/send-local-toast)
