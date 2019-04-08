---
description: C++/WinRT можно использовать для разработки классических компонентов COM, так как эта проекция языка помогает создавать классы среды выполнения Windows.
title: Создание COM-компонентов с помощью C++/WinRT
ms.date: 09/06/2018
ms.topic: article
keywords: Windows 10, uwp, standard, c ++, cpp, winrt, проекции, автор, COM, компонент
ms.localizationpriority: medium
ms.custom: RS5
ms.openlocfilehash: e6b77f8be6c75070336ad48f0c6471fc0a824a4c
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57616569"
---
# <a name="author-com-components-with-cwinrt"></a>Создание COM-компонентов с помощью C++/WinRT

[C + +/ WinRT](/windows/uwp/cpp-and-winrt-apis/intro-to-using-cpp-with-winrt) помогут вам создавать классической модели объектов компонента (COM) компонентов (или компонентные классы), так же, как он помогает вам создавать классы среды выполнения Windows. Ниже приведен простой иллюстрации, на которой вы можете проверить работу, если вставьте код в `pch.h` и `main.cpp` нового **консольное приложение Windows (C + +/ WinRT)** проекта.

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

Также см. в разделе [использовать COM-компонентов с использованием C + +/ WinRT](consume-com.md).

## <a name="a-more-realistic-and-interesting-example"></a>Это пример более реалистичных и интересных

В оставшейся части этого раздела описывается создание проекта минимальной консольного приложения, использующего C + +/ WinRT реализовать базовый coclass (COM-компонент или COM-класса) и фабрики класса. Пример приложения показано, как обеспечить всплывающее уведомление с кнопкой обратного вызова и компонентного класса (который реализует **INotificationActivationCallback** COM-интерфейс) позволяет приложению быть запущен и вызывается снова, когда пользователь нажимает эту кнопку на тост.

Дополнительные сведения, относящиеся к функциональной области всплывающих уведомлений можно найти в [отправки локального всплывающего уведомления](/windows/uwp/design/shell/tiles-and-notifications/send-local-toast). Ни один из примеров кода в этом разделе документации использовать C + +/ WinRT, однако таким образом мы рекомендуем, что вы предпочитаете код, показанный в этом разделе.

## <a name="create-a-windows-console-application-project-toastandcallback"></a>Создайте проект консольного приложения Windows (ToastAndCallback)

Начните с создания нового проекта в Microsoft Visual Studio. Создание **Visual C++** > **Windows Desktop** > **консольное приложение Windows (C + +/ WinRT)** проект и назовите его  *ToastAndCallback*.

Откройте `pch.h`и добавьте `#include <unknwn.h>` перед включает C + +/ WinRT заголовки.

```cppwinrt
// pch.h
#pragma once
#include <unknwn.h>
#include <winrt/Windows.Foundation.h>
```

Откройте `main.cpp`и удалить с помощью директив, которые создает шаблон проекта. Вместо них вставьте следующий код (который дает нам библиотек, заголовки и имена типов, которые нам нужно).

```cppwinrt
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
```

## <a name="implement-the-coclass-and-class-factory"></a>Реализуется фабрика как компонентный класс и класс

В C + +/ WinRT, реализации компонентных классов и фабрик классов путем наследования от [ **winrt::implements** ](/uwp/cpp-ref-for-winrt/implements) базовая структура. Сразу после трех-директивы using показанный выше (и перед `main`), вставьте этот код для реализации компонента всплывающее уведомление COM активатор.

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

Реализация компонентного класса выше используется тот же шаблон, представленный в [API-интерфейсы автора с использованием C + +/ WinRT](/windows/uwp/cpp-and-winrt-apis/author-apis#if-youre-not-authoring-a-runtime-class). Таким образом можно использовать ту же методику для реализации COM-интерфейсов, а также интерфейсы среды выполнения Windows. COM-компонентов и классов среды выполнения Windows предоставлять свои функции через интерфейс. Каждый интерфейс COM в конечном счете является производным от [ **интерфейса IUnknown** ](https://msdn.microsoft.com/library/windows/desktop/ms680509) интерфейс. Среда выполнения Windows основана на COM&mdash;одно различие, что интерфейсы среды выполнения Windows в конечном счете являются производными от [ **интерфейс IInspectable** ](/windows/desktop/api/inspectable/nn-inspectable-iinspectable) (и **IInspectable**  является производным от **IUnknown**).

В компонентном классе, в приведенном выше коде, мы реализуем **INotificationActivationCallback::Activate** метод, который является функция, которая вызывается, когда пользователь нажимает кнопку обратного вызова на всплывающее уведомление. Но перед вызовом этой функции, необходимо создать экземпляр компонентного класса, и это задача из **IClassFactory::CreateInstance** функции.

Компонентный класс, который мы только что реализован называется *активатор COM* для уведомлений и он имеет его идентификатор класса (CLSID) в виде `callback_guid` идентификатор (типа **GUID**), вы см. в разделе выше. Мы будем использовать этот идентификатор позже, в виде главное меню и записи в реестре Windows. Активатор COM CLSID и путь к его связанный COM-сервера (это путь к исполняемому файлу, который мы создаем здесь) — это механизм, по которому всплывающее уведомление знает, какому классу для создания экземпляра при нажатии кнопки его обратного вызова (ли щелчке уведомления центра поддержки или нет).

## <a name="best-practices-for-implementing-com-methods"></a>Рекомендации по реализации методы COM

Методы для обработки ошибок, а также для управления ресурсами можно перейти в наличии идут рука об руку. Это более простой и удобной для использования исключений, чем коды ошибок. И если у вас имеется идиомы — Получение ресурса — инициализация (RAII), можно предотвратить явно осуществляется проверка наличия коды ошибок и явно освобождая ресурсы. Такие явных проверок сделать код более запутанным, чем необходимо, а также ошибок довольно многие части, чтобы скрыть. Вместо этого используйте RAII и throw и catch исключений. Таким образом, ваши ресурсы распределяются по исключений, а ваш код прост.

Тем не менее не должен разрешить исключения для экранирования реализации метода COM. Можно обеспечить с помощью `noexcept` описатель на ваши методы COM. Это нормально для исключения, где угодно в графе вызовов метода, до тех пор, пока их обработки перед выходом из метода. Если вы используете `noexcept`, но затем разрешить исключение для экранирования ваш метод, то приложение будет завершено.

## <a name="add-helper-types-and-functions"></a>Добавить вспомогательные типы и функции

На этом этапе мы добавим некоторые вспомогательные типы и функции, сделанные в остальной части кода использование. В этом случае перед `main`, добавьте следующий код.

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

## <a name="implement-the-remaining-functions-and-the-wmain-entry-point-function"></a>Реализовать остальные функции и функцию точки входа wmain

Шаблон проекта создает `main` функции для вас. Удаление, `main` функции и вместо него вставьте этот код со списком, который включает код для регистрации вашей компонентного класса, а затем для доставки всплывающие из обратного вызова приложения.

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
        std::wcout << this_app_name << L" is running" << (runningAsAdmin ? L" (Administrator)." : L".") << std::endl;

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

## <a name="how-to-test-the-example-application"></a>Проверка примера приложения

Постройте приложение и запустите его хотя бы один раз учетные данные администратора, чтобы вызвать регистрации и другие настройки, выполнение кода. Ли вы используете его от имени администратора, а затем нажмите 'T' заставить всплывающее уведомление для отображения. Вы также можете щелкнуть **обратный вызов ToastAndCallback** кнопку напрямую от всплывающее уведомление, которое всплывает, или из центра поддержки и приложение запустится, компонентный класс создан и  **INotificationActivationCallback::Activate** метод, выполняемый.

## <a name="in-process-com-server"></a>Внутрипроцессный сервер COM

*ToastAndCallback* приведенное выше приложение работает как локальная (или out-of-process) COM-сервера. Это состояние обозначается [LocalServer32](/windows/desktop/com/localserver32) раздел реестра Windows, который используется для регистрации CLSID его компонентного класса. Локальный сервер COM. размещает его coclass(es) двоичный исполняемый файл ( `.exe`).

Кроме того (и возможно более вероятно), можно выбрать для размещения вашего coclass(es) внутри библиотеки динамической компоновки ( `.dll`). COM-сервера в виде библиотеки DLL, известна как внутрипроцессный сервер COM, а также обозначается CLSID регистрируемого с помощью [InprocServer32](/windows/desktop/com/inprocserver32) раздел реестра Windows.

### <a name="create-a-dynamic-link-library-dll-project"></a>Создайте проект библиотеки динамической компоновки (DLL)

Можно начать задачу создания внутрипроцессного COM-сервера, создав новый проект в Microsoft Visual Studio. Создание **Visual C++** > **Windows Desktop** > **библиотеки динамической компоновки (DLL)** проекта.

Чтобы добавить C + +/ WinRT поддержка в новый проект, выполните действия, описанные в [изменить проект приложения Windows Desktop Добавление C + +/ WinRT поддержка](/windows/uwp/cpp-and-winrt-apis/get-started#modify-a-windows-desktop-application-project-to-add-cwinrt-support).

### <a name="implement-the-coclass-class-factory-and-in-proc-server-exports"></a>Реализации компонентного класса, фабрика класса и экспорты внутрипроцессный сервер

Откройте `dllmain.cpp`и добавьте в него листинг кода, показано ниже.

Если у вас уже есть библиотеку DLL, реализующую C + +/ классы среды выполнения Windows WinRT, вы уже можете получить **DllCanUnloadNow** показанную ниже функцию. Если вы хотите добавить компонентных классов для библиотеки DLL, то можно добавить **DllGetClassObject** функции.

При отсутствии существующих [библиотеки шаблонов C++ (WRL) среды выполнения Windows](/cpp/windows/windows-runtime-cpp-template-library-wrl) частей кода, что вы хотите сохраняется совместимость с, а затем WRL можно удалить из кода, показанного.

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
            *ppvObject = winrt::make<MyCoclass>().get();
            return S_OK;
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

### <a name="support-for-weak-references"></a>Поддержка слабые ссылки

Также см. в разделе [Weak ссылок в C + +/ WinRT](weak-references.md#weak-references-in-cwinrt).

C + +/ WinRT (в частности, [ **winrt::implements** ](/uwp/cpp-ref-for-winrt/implements) базовая структура шаблона) реализует [ **IWeakReferenceSource** ](/windows/desktop/api/weakreference/nn-weakreference-iweakreferencesource) для вас Если вашей Введите реализует [ **IInspectable** ](/windows/desktop/api/inspectable/nn-inspectable-iinspectable) (или любой интерфейс, который является производным от **IInspectable**).

Это обусловлено **IWeakReferenceSource** и [ **IWeakReference** ](/windows/desktop/api/weakreference/nn-weakreference-iweakreference) предназначены для типов среды выполнения Windows. Таким образом, можно включить поддержку слабых ссылок для вашей coclass путем простого добавления **winrt::Windows::Foundation::IInspectable** (или интерфейс, который является производным от **IInspectable**) для вашей реализации.

```cppwinrt
struct MyCoclass : winrt::implements<MyCoclass, IMyComInterface, winrt::Windows::Foundation::IInspectable>
{
    //  ...
};
```

## <a name="important-apis"></a>Важные API
* [Интерфейс IInspectable](/windows/desktop/api/inspectable/nn-inspectable-iinspectable)
* [Интерфейс IUnknown](https://msdn.microsoft.com/library/windows/desktop/ms680509)
* [Структура шаблона WinRT::Implements](/uwp/cpp-ref-for-winrt/implements)

## <a name="related-topics"></a>Статьи по теме
* [Создание интерфейсов API с помощью C++/WinRT](/windows/uwp/cpp-and-winrt-apis/author-apis)
* [Использование компонентов COM с помощью C++/WinRT](consume-com.md)
* [Отправка локального всплывающего уведомления](/windows/uwp/design/shell/tiles-and-notifications/send-local-toast)
