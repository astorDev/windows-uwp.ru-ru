---
title: Автоматизация запуска приложений универсальной платформы Windows (UWP) в Windows 10
description: Разработчики могут использовать активацию по протоколу и активацию при запуске для автоматизации запуска своих приложений или игр UWP для автоматического тестирования.
ms.topic: article
ms.localizationpriority: medium
ms.date: 02/08/2017
ms.openlocfilehash: fb68b4bbd1b751591e9f336efe5dad3c22b3bf92
ms.sourcegitcommit: 49d58bc66c1c9f2a4f81473bcb25af79e2b1088d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/11/2018
ms.locfileid: "8937203"
---
# <a name="automate-launching-windows-10-uwp-apps"></a>Автоматизация запуска приложений UWP для Windows 10

## <a name="introduction"></a>Введение

Разработчикам предлагается несколько вариантов действий для автоматического запуска приложений универсальной платформы Windows (UWP). В этом документе мы рассмотрим методы запуска приложения с помощью активации протокола и активации запуска.

*Активация протокола* позволяет приложению зарегистрироваться в качестве обработчика для определенного протокола. 

*Активация запуска* является стандартным способом запуска приложения, таким как запуск с плитки приложения.

Каждый методом активации предусматривает возможность использования командной строки или средства запуска приложений. Для всех методов активации действует условие: если приложение в настоящий момент выполняется, активация выведет его на передний план (что повторно активирует его) и предоставит ему новые аргументы активации. Это обеспечивает гибкость для использования команд активации, чтобы передавать приложению новые сообщения. Важно отметить, что для запуска недавно обновленного приложения таким методом активации проект должен быть скомпилирован и развернут. 

## <a name="protocol-activation"></a>Активация протокола

Выполните следующие действия для настройки активации протокола для приложений. 

1. Откройте файл **Package.appxmanifest** в Visual Studio.
2. Выберите вкладку **Объявления**.
3. В выпадающем списке **Доступные объявления** выберите **Протокол**, затем выберите **Добавить**.
4. В разделе **Свойства** в поле **Имя** введите уникальное имя для запуска приложения. 

    ![Активация протокола](images/automate-uwp-apps-1.png)

5. Сохраните файл и разверните проект. 
6. После развертывания проекта необходимо выполнить активацию протокола. 
7. Перейдите в **Панель управления\Все элементы панели управления\Программы по умолчанию** и выберите **Сопоставление типов файлов или протоколов конкретным программам**. Прокрутите экран до раздела **Протоколы**, чтобы убедиться, что протокол включен в список. 

После активации протокола существует 2 варианта (командная строка или средства запуска приложений) для активации приложения с помощью протокола. 

### <a name="command-line"></a>Командная строка

Приложение может активироваться протоколом с помощью командной строки, где за началом команды следует заданное ранее имя протокола, двоеточие (":") и все остальные параметры. Параметры могут представлять собой любую произвольную строковую переменную; однако, чтобы воспользоваться возможностями универсального кода ресурса (URI), рекомендуется использовать стандартный формат URI. 

  ```
  scheme://username:password@host:port/path.extension?query#fragment
  ```

Объект Uri содержит методы анализа строки URI в этом формате. Дополнительные сведения см. в разделе [Класс Uri (MSDN)](https://msdn.microsoft.com/library/windows/apps/windows.foundation.uri.aspx). 

Примеры:

  ```
  >start bingnews:
  >start myapplication:protocol-parameter
  >start myapplication://single-player/level3?godmode=1&ammo=200
  ```

Активация протокола с использованием командной строки поддерживает символы юникода с ограничением в 2038 символов для простого URI. 

### <a name="launcher-application"></a>Средство запуска приложений

Для запуска создайте отдельное приложение, поддерживающее API WinRT. Код C++ для запуска путем активации протокола в средстве запуска приводится в следующем примере, где **PackageURI** — URI для приложения с любым набором аргументов; например `myapplication:` или `myapplication:protocol activation arguments`.

```
bool ProtocolLaunchURI(Platform::String^ URI)
{
       IAsyncOperation<bool>^ protocolLaunchAsyncOp;
       try
       {
              protocolLaunchAsyncOp = Windows::System::Launcher::LaunchUriAsync(ref new 
Uri(URI));
       }
       catch (Platform::Exception^ e)
       {
              Platform::String^ dbgStr = "ProtocolLaunchURI Exception Thrown: " 
+ e->ToString() + "\n";
              OutputDebugString(dbgStr->Data());
              return false;
       }

       concurrency::create_task(protocolLaunchAsyncOp).wait();

       if (protocolLaunchAsyncOp->Status == AsyncStatus::Completed)
       {
              bool LaunchResult = protocolLaunchAsyncOp->GetResults();
              Platform::String^ dbgStr = "ProtocolLaunchURI " + URI 
+ " completed. Launch result " + LaunchResult + "\n";
              OutputDebugString(dbgStr->Data());
              return LaunchResult;
       }
       else
       {
              Platform::String^ dbgStr = "ProtocolLaunchURI " + URI + " failed. Status:" 
+ protocolLaunchAsyncOp->Status.ToString() + " ErrorCode:" 
+ protocolLaunchAsyncOp->ErrorCode.ToString() + "\n";
              OutputDebugString(dbgStr->Data());
              return false;
       }
}
```
Активация протокола с помощью средства запуска имеет те же ограничения по аргументам, что и активация протокола с использованием командной строки. Оба метода поддерживают символы юникода с ограничением в 2038 символов для простого URI. 

## <a name="launch-activation"></a>Активация запуска

Также можно запустить приложение с помощью активации запуска. Настройка не требуется, но необходим идентификатор пользовательской модели (AUMID) приложения UWP. AUMID — имя семейства пакетов с идущими за ним восклицательным знаком и идентификатором приложения. 

Лучший способ получения имени семейства пакетов — выполнить следующие действия.

1. Откройте файл **Package.appxmanifest**.
2. На вкладке введите **Пакет** введите **Имя пакета**.

    ![Активация запуска](images/automate-uwp-apps-2.png)

3. Если **имя семейства пакетов** не указано в списке, откройте PowerShell и запустите `>get-appxpackage MyPackageName`, чтобы найти **PackageFamilyName**.

Идентификатор приложения можно найти в файле **Package.appxmanifest** (открытом в представлении XML) в разделе `<Applications>`.

### <a name="command-line"></a>Командная строка

Средство выполнения активации запуска приложения UWP устанавливается с пакетом SDK для Windows 10. Его можно выполнить в командной строке, указав в качестве аргумента AUMID приложения, которое требуется запустить.

```
C:\Program Files (x86)\Windows Kits\10\App Certification Kit\microsoft.windows.softwarelogo.appxlauncher.exe <AUMID>
```

Он выглядит примерно так:

```
"C:\Program Files (x86)\Windows Kits\10\App Certification Kit\microsoft.windows.softwarelogo.appxlauncher.exe" MyPackageName_ph1m9x8skttmg!AppId
```

Этот вариант не поддерживает аргументы командной строки. 

### <a name="launcher-application"></a>Средство запуска приложений

Вы можете создать отдельное приложение, поддерживающее модель COM, которую необходимо использовать для запуска. В следующем примере приводится код C++ для запуска с помощью активации запуска в средстве запуска. С помощью этого кода можно создать объект **ApplicationActivationManager** объект и вызвать **ActivateApplication**, передав ранее найденный AUMID со всеми аргументами. Дополнительные сведения о других параметрах см. в разделе [Метод IApplicationActivationManager::ActivateApplication (MSDN)](https://msdn.microsoft.com/library/windows/desktop/hh706903(v=vs.85).aspx).

```
#include <ShObjIdl.h>
#include <atlbase.h>

HRESULT LaunchApp(LPCWSTR AUMID)
{
     HRESULT hr = CoInitializeEx(nullptr, COINIT_APARTMENTTHREADED);
     if (FAILED(hr))
     {
            wprintf(L"LaunchApp %s: Failed to init COM. hr = 0x%08lx \n", AUMID, hr);
     }
     {
            CComPtr<IApplicationActivationManager> AppActivationMgr = nullptr;
            if (SUCCEEDED(hr))
            {
                   hr = CoCreateInstance(CLSID_ApplicationActivationManager, nullptr,  
CLSCTX_LOCAL_SERVER, IID_PPV_ARGS(&AppActivationMgr));
                   if (FAILED(hr))
                   {
                         wprintf(L"LaunchApp %s: Failed to create Application Activation 
Manager. hr = 0x%08lx \n", AUMID, hr);
                   }
            }
            if (SUCCEEDED(hr))
            {
                   DWORD pid = 0;
                   hr = AppActivationMgr->ActivateApplication(AUMID, nullptr, AO_NONE, 
&pid);
                   if (FAILED(hr))
                   {
                         wprintf(L"LaunchApp %s: Failed to Activate App. hr = 0x%08lx 
\n", AUMID, hr);
                   }
            }
     }
     CoUninitialize();
     return hr;
}
```

Следует отметить, что этот метод поддерживает передачу аргументов, что отличает его от предыдущего метода запуска (с использованием командной строки).

## <a name="accepting-arguments"></a>Принятие аргументов

Чтобы принять передаваемые при активации приложения UWP аргументы, необходимо добавить определенный код в приложение. Для определения того, выполнена ли активация протокола или активация запуска, переопределите событие **OnActivated** и проверьте тип аргумента, а затем получите необработанную строку или предварительно проанализированные значения объекта Uri. 

В этом примере показано, как получить необработанную строку.

```
void OnActivated(IActivatedEventArgs^ args)
{
        // Check for launch activation
        if (args->Kind == ActivationKind::Launch)
        {
            auto launchArgs = static_cast<LaunchActivatedEventArgs^>(args); 
            Platform::String^ argval = launchArgs->Arguments;
            // Manipulate arguments …
        }

        // Check for protocol activation
        if (args->Kind == ActivationKind::Protocol)
        {
            auto protocolArgs = static_cast< ProtocolActivatedEventArgs^>(args);
            Platform::String^ argval = protocolArgs->Uri->ToString();
            // Manipulate arguments …
        }
}
```

## <a name="summary"></a>Сводка
В целом, можно использовать различные методы для запуска приложения UWP. В зависимости от требований и вариантов использования определенные методы могут быть более удобны. 

## <a name="see-also"></a>См. также
- [UWP на Xbox One](index.md)

