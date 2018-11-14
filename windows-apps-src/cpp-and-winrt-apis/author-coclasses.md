---
author: stevewhims
description: C + +/ WinRT помогут вам создать классических компонентов COM, так же, как оно поможет вам создавать классы среды выполнения Windows.
title: Создание COM-компонентов с помощью C++/WinRT
ms.author: stwhi
ms.date: 09/06/2018
ms.topic: article
keywords: Windows 10, uwp, стандартная, c ++, cpp, winrt, проекция, автора, COM, компонент
ms.localizationpriority: medium
ms.openlocfilehash: 82ff87e4621d7dda3c3fd37d95d3d8d7812024e6
ms.sourcegitcommit: 38f06f1714334273d865935d9afb80efffe97a17
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/09/2018
ms.locfileid: "6199364"
---
# <a name="author-com-components-with-cwinrt"></a>Создание COM-компонентов с помощью C++/WinRT

[C + +/ WinRT](/windows/uwp/cpp-and-winrt-apis/intro-to-using-cpp-with-winrt) помогут вам создать классической модели компонентных объектов (COM) компонентов (или компонентных классов), так же, как оно поможет вам создавать классы среды выполнения Windows. Вот простой иллюстрацию, на которой можно проверить, вставьте код в `pch.h` и `main.cpp` нового **консольного приложения Windows (C + +/ WinRT)** проекта.

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

См. также [Использование COM-компонентов с помощью C + +/ WinRT](consume-com.md).

## <a name="a-more-realistic-and-interesting-example"></a>Более реалистичные и интересные примере

Далее в этой статье выполняется создание проекта минимальный консольного приложения, использующего C + +/ WinRT для реализации компонентный базовый класс (COM-компонент или класс COM) и фабрики классов. Пример приложения показано, как предоставлять всплывающее уведомление с кнопкой обратного вызова на нем, а компонентного (в котором реализован интерфейс **INotificationActivationCallback** COM) позволяет приложению для запуска и под названием "Назад" при пользователя нажимает эту кнопку на всплывающем уведомлении.

Дополнительную информацию об области всплывающее уведомление функции можно найти на [Отправка локального всплывающего уведомления](/windows/uwp/design/shell/tiles-and-notifications/send-local-toast). Ни один из в примерах кода в этом разделе документации использовать C + +/ WinRT, поэтому мы рекомендуем, что вы предпочитаете код, показанный в этом разделе.

## <a name="create-a-windows-console-application-project-toastandcallback"></a>Создание проекта консольного приложения Windows (ToastAndCallback)

Начните с создания нового проекта в Microsoft Visual Studio. Создание **Visual C++** > **Рабочего стола Windows** > **консольного приложения Windows (C + +/ WinRT)** проект и назовите его *ToastAndCallback*.

Откройте `pch.h`и добавьте `#include <unknwn.h>` перед включает в себя для C + +/ WinRT заголовков.

```cppwinrt
// pch.h
#pragma once
#include <unknwn.h>
#include <winrt/Windows.Foundation.h>
```

Откройте `main.cpp`и удалите-директивы using, создает шаблон проекта. Вставьте следующий код (который получаем библиотеки, заголовки и имена типов, которые нам нужно) в свое место.

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

## <a name="implement-the-coclass-and-class-factory"></a>Реализовать фабрику компонентного и класса

В C + +/ WinRT, можно реализовать компонентных классов и фабрики классов на основе базовой структуры [**winrt::implements**](/uwp/cpp-ref-for-winrt/implements) . Сразу после трех-директивы using приведенный выше (и перед `main`), вставьте этот код для реализации компонента активатор уведомлений COM всплывающего уведомления.

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

Реализация выше компонентного применяется та же схема, представленный в [Создание API-интерфейсов в C + +/ WinRT](/windows/uwp/cpp-and-winrt-apis/author-apis#if-youre-not-authoring-a-runtime-class). Таким образом можно использовать этот же способ для реализации COM-интерфейсов, а также интерфейсы среды выполнения Windows. COM-компонентов и классов среды выполнения Windows предоставляют своих компонентов через интерфейсы. Каждый COM-интерфейс в конечном счете является производным от интерфейса [**интерфейс IUnknown**](https://msdn.microsoft.com/library/windows/desktop/ms680509) . Среда выполнения Windows основывается на COM&mdash;одно различие, который интерфейсов среды выполнения Windows наследоваться от [**интерфейса IInspectable**](/windows/desktop/api/inspectable/nn-inspectable-iinspectable) (и **IInspectable** является производным от **IUnknown**).

В компонентного в приведенном выше коде мы реализуем метод **INotificationActivationCallback::Activate** , который функция, которая вызывается, когда пользователь нажимает кнопку обратного вызова на всплывающем уведомлении. Но перед вызовом эту функцию, необходимо создать экземпляр компонентного, и это задание **IClassFactory::CreateInstance** функции.

Компонентный класс, который мы только что реализован называется *активатор COM* для уведомлений, и он имеет его идентификатор класса (CLSID) в виде `callback_guid` идентификатор ( **GUID**типа), вы см. в разделе выше. Мы будем использовать этот идентификатор позже, в виде главное меню и запись реестра Windows. Активатор COM CLSID и путь к его связанные COM-сервера (это путь к исполняемому файлу, который мы создаем здесь) — это механизм, по которому всплывающее уведомление знает, что класс для создания экземпляра при нажатии кнопки его обратного вызова (ли уведомление о нажатии в центре уведомлений или нет).

## <a name="best-practices-for-implementing-com-methods"></a>Рекомендации по реализации методов COM

Методы для обработки ошибок и управление ресурсами можно перейти помощь в виде руки. Это более простой и удобной использовать исключения, чем коды ошибок. И если использовать альтернативные идиом (RAII) — Получение ресурса — инициализация, затем можно избежать явно наличие коды ошибок и явное освобождение ресурсов. Такие явные проверки сделать код более замысловата, чем это необходимо, и дает ошибок немало места, чтобы скрыть. Вместо этого используйте RAII и исключения/catch исключения. Таким образом, ресурс распределения обработкой исключений, и ваш код прост.

Тем не менее не должна разрешать исключения для экранирования вашей реализации методов COM. Вы можете убедитесь, что с помощью `noexcept` описатель на COM-методы. Это ОК для исключения в любом месте в граф вызова метода, до тех пор, пока их обработки перед выходом из метода. Если вы используете `noexcept`, но затем разрешите исключение escape-метода, а затем завершает приложение.

## <a name="add-helper-types-and-functions"></a>Добавить вспомогательные типы и функции

На этом этапе мы добавим некоторые вспомогательные типы и функции вносит в остальной части кода использование. Таким образом, перед `main`, добавьте следующий код.

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

## <a name="implement-the-remaining-functions-and-the-wmain-entry-point-function"></a>Реализация остальные функции и функции точки входа wmain

Шаблон проекта создает `main` функции для вас. Удалите `main` функции, а вместо него вставьте этот код описания, который содержит код для регистрации вашего компонентный класс, а затем для доставки уведомления может обратного вызова приложения.

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

## <a name="how-to-test-the-example-application"></a>Проверка пример приложения

Сборка приложения и запустите его по крайней мере один раз от имени администратора для привести к регистрации и другие настройки кода для запуска. Ли вы используете его от имени администратора, а затем нажмите клавишу ранее выпущенные "для привести к всплывающего уведомления для отображения. Затем можно нажать кнопку **обратного вызова ToastAndCallback** непосредственно из всплывающего уведомления, что щелчкам вверх или в центре уведомлений, и ваше приложение будет запущена, компонентного создан и **INotificationActivationCallback :: Активировать** метод выполнен.

## <a name="in-process-com-server"></a>В процессе COM-сервера

Пример приложения *ToastAndCallback* выше функционирует как COM-сервер локальной (или вне процесса). Это состояние обозначается раздел реестра [LocalServer32](/windows/desktop/com/localserver32) Windows, используемого для регистрации CLSID его компонентного класса. Локальный сервер COM размещается его coclass(es) внутри исполняемого двоичного файла ( `.exe`).

Кроме того (и несомненно, наиболее вероятно), вы можете выбрать для размещения вашего coclass(es) внутри библиотеки динамической компоновки ( `.dll`). COM-сервер в виде библиотеки DLL, называется внутри процесса COM-сервера и обозначается CLSID регистрируется с помощью реестра [InprocServer32](/windows/desktop/com/inprocserver32) Windows.

### <a name="create-a-dynamic-link-library-dll-project"></a>Создание проекта библиотеки динамической компоновки (DLL)

Можно начинать задачу создания внутрипроцессного COM-сервера, создав новый проект в Microsoft Visual Studio. Создание **Visual C++** > **Рабочего стола Windows** > проекта**Библиотеки динамической компоновки (DLL)** .

Для добавления C + +/ WinRT поддержки в новый проект, выполните действия, описанные в [Изменение проекта приложения рабочего стола Windows для добавления C + +/ WinRT поддержки](/windows/uwp/cpp-and-winrt-apis/get-started#modify-a-windows-desktop-application-project-to-add-cwinrt-support).

### <a name="implement-the-coclass-class-factory-and-in-proc-server-exports"></a>Реализация компонентного, фабрики классов и экспорт в процессе сервера

Откройте `dllmain.cpp`и добавьте к нему текст кода, показано ниже.

Если у вас уже есть библиотеку DLL, которая реализует C + +/ WinRT среды выполнения Windows классы, то необходимо уже функции **DllCanUnloadNow** , показанные ниже. Если вы хотите добавить компонентных классов с этой библиотекой DLL, вы можете добавить функцию **DllGetClassObject** .

Если нет существующего кода [Библиотеки шаблонов C++ (WRL) среды выполнения Windows](/cpp/windows/windows-runtime-cpp-template-library-wrl) , чтобы остаться совместимы с, то вы можете удалить частей WRL из кода, показанного.

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

### <a name="support-for-weak-references"></a>Поддержка слабых ссылок

См. также [слабые ссылки в C + +/ WinRT](weak-references.md#weak-references-in-cwinrt).

C + +/ WinRT (в частности, шаблон базовой структуры [**winrt::implements**](/uwp/cpp-ref-for-winrt/implements) ) реализует [**IWeakReferenceSource**](/windows/desktop/api/weakreference/nn-weakreference-iweakreferencesource) для вас, если ваш тип реализует [**IInspectable**](/windows/desktop/api/inspectable/nn-inspectable-iinspectable) (или любой интерфейс, который является производным от **IInspectable**).

Это связано с **IWeakReferenceSource** и [**IWeakReference**](/windows/desktop/api/weakreference/nn-weakreference-iweakreference) предназначены для типов среды выполнения Windows. Таким образом можно включить поддержки слабых ссылок для компонентного вашего класса, достаточно добавить **winrt::Windows::Foundation::IInspectable** (или интерфейс, который является производным от **IInspectable**) к вашей реализации.

```cppwinrt
struct MyCoclass : winrt::implements<MyCoclass, IMyComInterface, winrt::Windows::Foundation::IInspectable>
{
    //  ...
};
```

## <a name="important-apis"></a>Важные API
* [Интерфейс IInspectable](/windows/desktop/api/inspectable/nn-inspectable-iinspectable)
* [Интерфейс IUnknown interface](https://msdn.microsoft.com/library/windows/desktop/ms680509)
* [Шаблон структуры winrt::implements](/uwp/cpp-ref-for-winrt/implements)

## <a name="related-topics"></a>Статьи по теме
* [Создание API-интерфейсов в C++/WinRT](/windows/uwp/cpp-and-winrt-apis/author-apis)
* [Использование COM-компонентов с помощью C++/WinRT](consume-com.md)
* [Отправка локального всплывающего уведомления](/windows/uwp/design/shell/tiles-and-notifications/send-local-toast)
