---
Description: Learn how Win32 C++ WRL apps can send local toast notifications and handle the user clicking the toast.
title: Отправка локального всплывающего уведомлений из классических приложений C++ WRL
label: Send a local toast notification from desktop C++ WRL apps
template: detail.hbs
ms.date: 03/7/2018
ms.topic: article
keywords: windows 10, uwp, win32, настольный компьютер, всплывающие уведомления, отправка уведомления, отправка локального уведомления, мост для классических приложений, C++, cpp, cplusplus, WRL
ms.localizationpriority: medium
ms.openlocfilehash: 8508ed18e216270b901a5462d80238fae0763982
ms.sourcegitcommit: 681c70f964210ab49ac5d06357ae96505bb78741
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/27/2018
ms.locfileid: "7713840"
---
# <a name="send-a-local-toast-notification-from-desktop-c-wrl-apps"></a>Отправка локального всплывающего уведомлений из классических приложений C++ WRL

Классические приложения (мост для классических приложений и классические приложения Win32) могут отправлять интерактивные всплывающие уведомления так же, как приложения универсальной платформы Windows (UWP). Однако существует несколько специальных действий для классических приложений, связанных с другими схемами активации и потенциальным отсутствием идентификатора пакета, если вы не используете мост для классических приложений.

> [!IMPORTANT]
> Если вы создаете приложение UWP, см. [документацию по UWP](send-local-toast.md). Сведения для других языков см. в разделе [C# для настольных компьютеров](send-local-toast-desktop.md).


## <a name="step-1-enable-the-windows-10-sdk"></a>Шаг 1. Включение Windows 10 SDK

Если вы не включили Windows 10 SDK для вашего приложения Win32, вам необходимо сделать это в первую очередь. Для этого требуется выполнить несколько шагов...

1. Добавьте `runtimeobject.lib` в **Дополнительные зависимости**
2. Выберите Windows 10 SDK в качестве цели

Щелкните проект правой кнопкой мыши и выберите пункт **Свойства**.

В верхней части меню **Конфигурация** выберите пункт **Все конфигурации**, чтобы применить следующее изменение к отладке и выпуску.

В разделе **Компоновщик -> Ввод** добавьте `runtimeobject.lib` в **Дополнительные зависимости**.

Затем в разделе **Общие** убедитесь, что в поле **Версия Windows SDK** указана версия 10.0 или более поздняя (не Windows 8.1).


## <a name="step-2-copy-compat-library-code"></a>Шаг 2. Копирование кода библиотеки compat

Скопируйте файлы [DesktopNotificationManagerCompat.h](https://raw.githubusercontent.com/WindowsNotifications/desktop-toasts/master/CPP-WRL/DesktopToastsCppWrlApp/DesktopNotificationManagerCompat.h) и [DesktopNotificationManagerCompat.cpp](https://raw.githubusercontent.com/WindowsNotifications/desktop-toasts/master/CPP-WRL/DesktopToastsCppWrlApp/DesktopNotificationManagerCompat.cpp) из GitHub в ваш проект. Библиотека compat значительно упрощает уведомления для классических приложений. Для выполнения следующих инструкций требуется библиотека compat.

Если вы используете предварительно скомпилированные заголовки, добавьте `#include "stdafx.h"` как первую строку файла DesktopNotificationManagerCompat.cpp.


## <a name="step-3-include-the-header-files-and-namespaces"></a>Шаг 3. Добавление файлов заголовков и пространств имен

Добавьте файл заголовка библиотеки compat, а также файлы заголовков и пространства имен, связанные с использованием API-интерфейсов всплывающих уведомлений UWP.

```cpp
#include "DesktopNotificationManagerCompat.h"
#include "NotificationActivationCallback.h"
#include <windows.ui.notifications.h>

using namespace ABI::Windows::Data::Xml::Dom;
using namespace ABI::Windows::UI::Notifications;
using namespace Microsoft::WRL;
```


## <a name="step-4-implement-the-activator"></a>Шаг 4. Реализация активатора

Вам необходимо реализовать обработчик для активации всплывающего уведомления, чтобы когда пользователь щелкал всплывающее уведомление, ваше приложение могло что-то сделать. Это необходимо, чтобы всплывающее уведомление сохранялось в центре уведомлений (так как всплывающее уведомление можно щелкнуть после закрытия вашего приложения). Этот класс можно разместить в любом месте в проекте.

Реализуйте интерфейс **INotificationActivationCallback**, как показано ниже, включая UUID, а также вызовите метод **CoCreatableClass**, чтобы отметить класс как поддерживающий создание с помощью COM. Для UUID создайте уникальный идентификатор GUID с помощью одного из множества веб-генераторов GUID. С помощью идентификатора GUID CLSID (идентификатор класса) центр уведомлений определяет, какой класс активировать, используя COM.

```cpp
// The UUID CLSID must be unique to your app. Create a new GUID if copying this code.
class DECLSPEC_UUID("replaced-with-your-guid-C173E6ADF0C3") NotificationActivator WrlSealed WrlFinal
    : public RuntimeClass<RuntimeClassFlags<ClassicCom>, INotificationActivationCallback>
{
public:
    virtual HRESULT STDMETHODCALLTYPE Activate(
        _In_ LPCWSTR appUserModelId,
        _In_ LPCWSTR invokedArgs,
        _In_reads_(dataCount) const NOTIFICATION_USER_INPUT_DATA* data,
        ULONG dataCount) override
    {
        // TODO: Handle activation
    }
};

// Flag class as COM creatable
CoCreatableClass(NotificationActivator);
```


## <a name="step-5-register-with-notification-platform"></a>Шаг 5. Регистрация на платформе уведомлений

Затем вам необходимо зарегистрироваться на платформе уведомлений. Требуется выполнить различные действия, которые зависят от того, используете ли вы мост для классических приложений или Win32. Если вы поддерживаете оба варианта, необходимо выполнить оба шага (тем не менее нет необходимости в разветвлении кода, так как наша библиотека делает это автоматически).


### <a name="desktop-bridge"></a>Мост для классических приложений

Если вы используете мост для классических приложений (или поддерживаете оба варианта) в файл **Package.appxmanifest** добавьте:

1. Объявление для **xmlns:com**
2. Объявление для **xmlns:desktop**
3. В атрибуте **IgnorableNamespaces** : **com** и **desktop**
4. **com:Extension** для COM-активатора с использованием GUID из шага 4. Не забудьте добавить `Arguments="-ToastActivated"`, чтобы знать, что запуск произошел из всплывающего уведомления
5. **desktop:Extension** для **windows.toastNotificationActivation**, чтобы объявить CLSID активатора уведомления (GUID из шага 4).

**Package.appxmanifest**

```xml
<Package
  ...
  xmlns:com="http://schemas.microsoft.com/appx/manifest/com/windows10"
  xmlns:desktop="http://schemas.microsoft.com/appx/manifest/desktop/windows10"
  IgnorableNamespaces="... com desktop">
  ...
  <Applications>
    <Application>
      ...
      <Extensions>

        <!--Register COM CLSID LocalServer32 registry key-->
        <com:Extension Category="windows.comServer">
          <com:ComServer>
            <com:ExeServer Executable="YourProject\YourProject.exe" Arguments="-ToastActivated" DisplayName="Toast activator">
              <com:Class Id="replaced-with-your-guid-C173E6ADF0C3" DisplayName="Toast activator"/>
            </com:ExeServer>
          </com:ComServer>
        </com:Extension>

        <!--Specify which CLSID to activate when toast clicked-->
        <desktop:Extension Category="windows.toastNotificationActivation">
          <desktop:ToastNotificationActivation ToastActivatorCLSID="replaced-with-your-guid-C173E6ADF0C3" /> 
        </desktop:Extension>

      </Extensions>
    </Application>
  </Applications>
 </Package>
```


### <a name="classic-win32"></a>Классическое приложение Win32

Если вы используете классическое приложение Win32 (или поддерживать оба варианта), вам необходимо объявить идентификатор модели пользователя приложения (AUMID), а также CLSID всплывающего уведомления (идентификатор GUID из шага 4) для ярлыка приложения в меню "Пуск".

Выберите уникальный AUMID для идентификации приложения Win32. Обычно он указывается в формате [Имя_компании].[Имя_приложения], но вам необходимо убедиться, что он является уникальным для всех приложений (вы можете добавьте в конец ряд цифр).

#### <a name="step-51-wix-installer"></a>Шаг 5.1. Установщик WiX

Если вы используете WiX в качестве установщика, измените файл **Product.wxs**, чтобы добавить два свойства ярлыка в ярлык меню "Пуск", как показано ниже. Убедитесь, что ваш идентификатор GUID из шага 4 заключен в `{}`, как показано ниже.

**Product.wxs**

```xml
<Shortcut Id="ApplicationStartMenuShortcut" Name="Wix Sample" Description="Wix Sample" Target="[INSTALLFOLDER]WixSample.exe" WorkingDirectory="INSTALLFOLDER">
                    
    <!--AUMID-->
    <ShortcutProperty Key="System.AppUserModel.ID" Value="YourCompany.YourApp"/>
    
    <!--COM CLSID-->
    <ShortcutProperty Key="System.AppUserModel.ToastActivatorCLSID" Value="{replaced-with-your-guid-C173E6ADF0C3}"/>
    
</Shortcut>
```

> [!IMPORTANT]
> Чтобы фактически использовать уведомления, необходимо установить приложение с помощью установщика перед отладкой в обычном режиме, чтобы ярлык в меню "Пуск" с вашими AUMID и CLSID существовал. После создания ярлыка в меню "Пуск" вы можете выполнить отладку, нажав клавишу F5 в Visual Studio.


#### <a name="step-52-register-aumid-and-com-server"></a>Шаг 5.2. Регистрация AUMID и COM-сервера

Затем, независимо от установщика, в коде запуска приложения (перед вызовом API-интерфейсов уведомлений) вызовите метод **RegisterAumidAndComServer**, указав класс активатора из шага 4 и идентификатор AUMID, который используется выше.

```cpp
// Register AUMID and COM server (for Desktop Bridge apps, this no-ops)
hr = DesktopNotificationManagerCompat::RegisterAumidAndComServer(L"YourCompany.YourApp", __uuidof(NotificationActivator));
```

Если вы поддерживаете мост для классических приложений и классический режим Win32, вы можете вызывать этот метод в любом случае. Если вы используете мост для классических приложений, этот метод просто немедленно возвращается. Разветвление кода не требуется.

Этот метод позволяет вызвать API-интерфейсы библиотеки compat для отправки уведомлений и управления ими, не указывая постоянно AUMID. Он также вставляет раздел реестра LocalServer32 для COM-сервера.


## <a name="step-6-register-com-activator"></a>Шаг 6. Регистрация активатора COM

Для моста для классических приложений и для классических приложений Win32 необходимо зарегистрировать тип активатора уведомлений, чтобы вы могли обрабатывать активацию всплывающих уведомлений.

В коде запуска приложения вызовите следующий метод **RegisterActivator**. Это необходимо, чтобы вы получали активацию всплывающих уведомлений.

```cpp
// Register activator type
hr = DesktopNotificationManagerCompat::RegisterActivator();
```


## <a name="step-7-send-a-notification"></a>Шаг 7. Отправка уведомления

Отправка уведомлений осуществляется так же, как для приложений UWP, за исключением того, что вы будете использовать **DesktopNotificationManagerCompat** для создания **ToastNotifier**. Библиотека compat автоматически осуществляет разветвление кода для моста для классических приложений и Win32. Для классических приложений Win32 библиотека compat кэширует AUMID, указанный при вызове **RegisterAumidAndComServer**, чтобы вам не нужно было думать о том, следует ли предоставлять AUMID или нет.

Убедитесь, что вы используете привязку **ToastGeneric**, как показано ниже, так как предыдущие шаблоны всплывающих уведомлений для Windows 8.1 не активируют ваш активатор уведомлений COM, созданный на шаге 4.

> [!IMPORTANT]
> HTTP-изображения поддерживаются только в приложениях моста для классических приложений с поддержкой Интернета в манифесте. Классические приложения Win32 не поддерживают образы HTTP-изображения, вам необходимо скачать изображение и добавить их в локальные данные приложения и ссылаться на него локально.

```cpp
// Construct XML
ComPtr<IXmlDocument> doc;
hr = DesktopNotificationManagerCompat::CreateXmlDocumentFromString(
    L"<toast><visual><binding template='ToastGeneric'><text>Hello world</text></binding></visual></toast>",
    &doc);
if (SUCCEEDED(hr))
{
    // See full code sample to learn how to inject dynamic text, buttons, and more

    // Create the notifier
    // Classic Win32 apps MUST use the compat method to create the notifier
    ComPtr<IToastNotifier> notifier;
    hr = DesktopNotificationManagerCompat::CreateToastNotifier(&notifier);
    if (SUCCEEDED(hr))
    {
        // Create the notification itself (using helper method from compat library)
        ComPtr<IToastNotification> toast;
        hr = DesktopNotificationManagerCompat::CreateToastNotification(doc, &toast);
        if (SUCCEEDED(hr))
        {
            // And show it!
            hr = notifier->Show(toast.Get());
        }
    }
}
```

> [!IMPORTANT]
> Классические приложения Win32 не могут использовать устаревшие шаблоны всплывающих уведомлений (например, ToastText02). При указании COM CLSID активация традиционных шаблонов завершится ошибкой. Необходимо использовать шаблоны ToastGeneric для Windows 10, как показано выше.


## <a name="step-8-handling-activation"></a>Шаг 8. Обработка активации

Когда пользователь щелкает всплывающее уведомление или нажимает кнопки в нем, вызывается метод **Activate** класса **NotificationActivator**.

Внутри метода Activate можно проанализировать аргументы, указанные во всплывающем уведомлении, и получить данные, введенные или выбранные пользователем, и затем активировать приложение соответствующим образом.

> [!NOTE]
> Метод **Activate** вызывается в потоке, отдельном от основного.

```cpp
// The GUID must be unique to your app. Create a new GUID if copying this code.
class DECLSPEC_UUID("replaced-with-your-guid-C173E6ADF0C3") NotificationActivator WrlSealed WrlFinal
    : public RuntimeClass<RuntimeClassFlags<ClassicCom>, INotificationActivationCallback>
{
public: 
    virtual HRESULT STDMETHODCALLTYPE Activate(
        _In_ LPCWSTR appUserModelId,
        _In_ LPCWSTR invokedArgs,
        _In_reads_(dataCount) const NOTIFICATION_USER_INPUT_DATA* data,
        ULONG dataCount) override
    {
        std::wstring arguments(invokedArgs);
        HRESULT hr = S_OK;

        // Background: Quick reply to the conversation
        if (arguments.find(L"action=reply") == 0)
        {
            // Get the response user typed.
            // We know this is first and only user input since our toasts only have one input
            LPCWSTR response = data[0].Value;

            hr = DesktopToastsApp::SendResponse(response);
        }

        else
        {
            // The remaining scenarios are foreground activations,
            // so we first make sure we have a window open and in foreground
            hr = DesktopToastsApp::GetInstance()->OpenWindowIfNeeded();
            if (SUCCEEDED(hr))
            {
                // Open the image
                if (arguments.find(L"action=viewImage") == 0)
                {
                    hr = DesktopToastsApp::GetInstance()->OpenImage();
                }

                // Open the app itself
                // User might have clicked on app title in Action Center which launches with empty args
                else
                {
                    // Nothing to do, already launched
                }
            }
        }

        if (FAILED(hr))
        {
            // Log failed HRESULT
        }

        return S_OK;
    }

    ~NotificationActivator()
    {
        // If we don't have window open
        if (!DesktopToastsApp::GetInstance()->HasWindow())
        {
            // Exit (this is for background activation scenarios)
            exit(0);
        }
    }
};

// Flag class as COM creatable
CoCreatableClass(NotificationActivator);
```

Для поддержки запуска, когда ваше приложение закрыто, в функции WinMain необходимо определить, происходит ли запуск из всплывающего уведомления или нет. При запуске из всплывающего уведомления, будет присутствовать аргумент "-ToastActivated". В этом случае вам следует остановить любой обычный код активации запуска и разрешить вашей **NotificationActivator** обработку запуска окон запуска при необходимости.

```cpp
// Main function
int WINAPI wWinMain(_In_ HINSTANCE hInstance, _In_opt_ HINSTANCE, _In_ LPWSTR cmdLineArgs, _In_ int)
{
    RoInitializeWrapper winRtInitializer(RO_INIT_MULTITHREADED);

    HRESULT hr = winRtInitializer;
    if (SUCCEEDED(hr))
    {
        // Register AUMID and COM server (for Desktop Bridge apps, this no-ops)
        hr = DesktopNotificationManagerCompat::RegisterAumidAndComServer(L"WindowsNotifications.DesktopToastsCpp", __uuidof(NotificationActivator));
        if (SUCCEEDED(hr))
        {
            // Register activator type
            hr = DesktopNotificationManagerCompat::RegisterActivator();
            if (SUCCEEDED(hr))
            {
                DesktopToastsApp app;
                app.SetHInstance(hInstance);

                std::wstring cmdLineArgsStr(cmdLineArgs);

                // If launched from toast
                if (cmdLineArgsStr.find(TOAST_ACTIVATED_LAUNCH_ARG) != std::string::npos)
                {
                    // Let our NotificationActivator handle activation
                }

                else
                {
                    // Otherwise launch like normal
                    app.Initialize(hInstance);
                }

                app.RunMessageLoop();
            }
        }
    }

    return SUCCEEDED(hr);
}
```


### <a name="activation-sequence-of-events"></a>Последовательность событий активации

Последовательность активации выглядит следующим образом...

Если приложение уже запущено:

1. Вызывается метод **Activate** в вашем **NotificationActivator**

Если приложение не запущено:

1. Ваше приложение будет запущено с помощью EXE-файла, и вы получаете аргументы командной строки "-ToastActivated"
2. Вызывается метод **Activate** в вашем **NotificationActivator**


### <a name="foreground-vs-background-activation"></a>Активация на переднем плане или в фоне
Для классических приложений активации на переднем плане и в фоне обрабатывается одинаково: вызывается ваш активатор COM. Код вашего приложения определяет, следует ли отобразить окно или просто выполнить определенные действия, а затем завершить работу. Таким образом, указав **activationType** как **background** в содержимом всплывающего уведомления, вы ничего не поменяете.


## <a name="step-9-remove-and-manage-notifications"></a>Шаг 9. Удаление уведомлений и управление ими

Удаление уведомлений и управление ими осуществляется так же, как и для приложений UWP. Однако мы рекомендуем использовать библиотеку compat для получения **DesktopNotificationHistoryCompat**, чтобы не предоставлять AUMID, если вы используете классический режим Win32.

```cpp
std::unique_ptr<DesktopNotificationHistoryCompat> history;
auto hr = DesktopNotificationManagerCompat::get_History(&history);
if (SUCCEEDED(hr))
{
    // Remove a specific toast
    hr = history->Remove(L"Message2");

    // Clear all toasts
    hr = history->Clear();
}
```


## <a name="step-10-deploying-and-debugging"></a>Шаг 10. Развертывание и отладка

Сведения о развертывании и отладке приложения моста для классических приложений см. в разделе [Запуск, отладка и тестирование упакованного классического приложения](/porting/desktop-to-uwp-debug.md).

Для развертывания и отладки классического приложения Win32 необходимо установить приложение с помощью установщика перед отладкой в обычном режиме, чтобы ярлык в меню "Пуск" с вашими AUMID и CLSID существовал. После создания ярлыка в меню "Пуск" вы можете выполнить отладку, нажав клавишу F5 в Visual Studio.

Если ваши уведомления просто не отображаются в классическом приложении Win32 (и исключения не вызываются), это, скорее всего, означает, что ярлык в меню "Пуск" отсутствует (установите приложение с помощью установщика), или AUMID, используемый в коде, не соответствует AUMID в ярлыке меню "Пуск".

Если ваши уведомления отображаются, но не сохраняются в центре уведомлений (исчезают после закрытия всплывающего окна), активатор COM реализован неправильно.

Если вы установили как версию для моста для классических приложений, так версию для Win32, обратите внимание, что приложение моста для классических приложений будет иметь приоритет при обработке активации всплывающих уведомлений. Это означает, что всплывающие уведомления из классического приложения Win32 по-прежнему запускают приложение моста для классических приложений при нажатии. При удалении приложения моста для классических приложений активация будет обрабатываться классическим приложением Win32.

Если вы получаете `HRESULT 0x800401f0 CoInitialize has not been called.`, не забудьте вызвать `CoInitialize(nullptr)` в приложении перед вызовом API-интерфейсов.

Если вы получаете `HRESULT 0x8000000e A method was called at an unexpected time.` при вызове API-интерфейсов Compat, это, скорее всего, означает, что вы не вызвали требуемые методы Register (или приложение моста для классических приложений не выполняется в контексте моста для классических приложений).

Если вы получаете множество ошибок компиляции `unresolved external symbol`, вы, скорее всего, забыли добавить `runtimeobject.lib` в раздел **Дополнительные зависимости** на шаге 1 (или добавили его только в конфигурацию отладки, но не выпуска).


## <a name="handling-older-versions-of-windows"></a>Обработка более ранних версий Windows

Если вы поддерживаете Windows 8.1 или более ранние версии, вам необходимо во время выполнения проверить, используется ли Windows 10 перед вызовом любого API-интерфейса **DesktopNotificationManagerCompat** или перед отправкой всплывающих уведомлений ToastGeneric.

Всплывающие уведомления появились в Windows 8, но они использовали [устаревшие шаблоны всплывающего уведомлений](https://docs.microsoft.com/en-us/previous-versions/windows/apps/hh761494(v=win.10)), такие как ToastText01. Активация обрабатывалась событием **Activated** в памяти в классе **ToastNotification**, так как всплывающие уведомления представляли собой не сохраняющиеся всплывающие окна. В Windows 10 появились [интерактивные всплывающие уведомления ToastGeneric](adaptive-interactive-toasts.md), а также центр уведомлений, в котором уведомления сохраняются в течение нескольких дней. После появления центра уведомлений потребовалось использовать активатор COM, чтобы всплывающие уведомления могли активироваться через несколько дней после создания.

| ОС | ToastGeneric | Активатор COM | Устаревшие шаблоны всплывающих уведомлений |
| -- | ------------ | ------------- | ---------------------- |
| Windows 10 | Поддерживается | Поддерживается | Поддерживается (но не активирует COM-сервер) |
| Windows 8.1 и 8 | Н/Д | Н/Д | Поддерживается |
| Windows 7 и более ранние версии | Н/Д | Н/Д | Н/Д |

Чтобы узнать, используется ли Windows 10, добавьте заголовок `<VersionHelpers.h>` и проверьте метод **IsWindows10OrGreater**. Если он возвращает значение true, продолжайте вызывать все методы, описанные в этой документации. 

```cpp
#include <VersionHelpers.h>

if (IsWindows10OrGreater())
{
    // Running Windows 10, continue with sending Windows 10 toasts!
}
```


## <a name="known-issues"></a>Известные проблемы

**ИСПРАВЛЕНО. Приложение не получает фокус после щелчка всплывающего уведомления**: в сборках 15063 и более ранних версиях права переднего плана не передавались приложению при активации COM-сервера. Поэтому приложение просто мигало при попытке переместить его на передний план. В то время решения этой проблемы не существовало. Мы исправили это в сборках 16299 и более поздних версиях.


## <a name="resources"></a>Ресурсы

* [Полный пример кода на GitHub](https://github.com/WindowsNotifications/desktop-toasts)
* [Всплывающие уведомления из классических приложений](toast-desktop-apps.md)
* [Документация по содержимому всплывающего уведомления](adaptive-interactive-toasts.md)