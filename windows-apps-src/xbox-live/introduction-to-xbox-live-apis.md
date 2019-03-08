---
title: Введение в API-интерфейсы Xbox Live
description: Дополнительные сведения о различных моделей API, которые можно использовать для взаимодействия со службой Xbox Live.
ms.assetid: 5918c3a2-6529-4f07-b44d-51f9861f91ec
ms.date: 06/05/2018
ms.topic: article
keywords: xbox live, xbox, игры, uwp, windows 10, xbox one
ms.localizationpriority: medium
ms.openlocfilehash: 1f52e379b524952c3361b432a577a7137b02155b
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57657699"
---
# <a name="introduction-to-xbox-live-apis"></a>Введение в API-интерфейсы Xbox Live

## <a name="use-xbox-live-services"></a>Используйте службы Xbox Live

Существует два способа для получения сведений от службы Xbox Live:

- Использование API на стороне клиента, вызывается API-службы Xbox Live (**XSAPI**)
- Вызовите **конечные точки Xbox Live REST** напрямую

Преимущества использования API службы Xbox Live (**XSAPI**) включают:

- Сведения о проверки подлинности, кодировки и HTTP, отправки и получения решены для вас.
- Аргументы и данных возвращается из, программы-оболочки API обрабатывается в собственных данных типов; Таким образом не нужно выполнять кодирование и декодирование JSON.
- Прямой вызов веб-служб включает несколько асинхронных шагов, которые инкапсулирует оболочки API; Это упрощает код title для чтения и записи.
- Некоторые функции, такие как запись события игры, доступна только в XSAPI.

Преимущества использования **конечные точки Xbox Live REST** непосредственно включают:

- Возможность вызова из веб-службы Xbox Live конечных точек
- Возможность вызова конечных точек, которые не включены в XSAPI.  XSAPI включает только интерфейсы API, которые мы считаем, что будет использовать игры, так что-то отсутствует позволяют нам об этом форумов.
- Некоторые функциональные возможности, доступные через конечные точки REST может не иметь соответствующего XSAPI оболочку.

Игры и приложения не ограничены использованием только одного из следующих методов. Можно использовать оболочку XSAPI и по-прежнему напрямую вызывать конечные точки REST, при необходимости.

## <a name="xbox-live-services-api-overview"></a>Общие сведения об API службы Live Xbox ##

API службы Xbox Live (**XSAPI**) предоставляет три набора клиентских интерфейсов API, поддерживающих широкий спектр сценариев клиентов:

- [XSAPI WinRT API](#xsapi-winrt-based-api)
- [API на основе XSAPI C ++ 11](#xsapi-c11-based-api)
- [API на основе XSAPI C](#xsapi-c-based-api) (**New начиная с июня 2018**)

Сравнение API-интерфейсы:

### <a name="xsapi-winrt-based-api"></a>API на основе XSAPI WinRT

- Поддерживает приложения, написанные с использованием C + +/ CX, C#и JavaScript.
    - C + +/ CX является расширением Microsoft C++ для упрощения программирования WinRT, используют ^ как указатели WinRT.
- Поддерживает приложения, предназначенные для платформы Xbox один XDK, а Windows платформы Универсальной x86, x64 и архитектуры ARM.
- Ошибки обрабатываются с помощью исключений на всех языках, включая C + +/ CX.
- C + +/ WinRT также поддерживается.  Дополнительные сведения о C + +/ WinRT можно найти в [https://moderncpp.com/2016/10/13/cppwinrt-available-on-github/](https://moderncpp.com/2016/10/13/cppwinrt-available-on-github/)

Ниже приведен пример вызова API WinRT XSAPI, с помощью C + +/ WinRT:

```c++
winrt::Windows::Xbox::System::User cppWinrtUser = winrt::Windows::Xbox::System::User::Users().GetAt(0);
winrt::Microsoft::Xbox::Services::XboxLiveContext xblContext(cppWinrtUser);
```

Если вы хотите смешивать C + +/ CX и C + +/ WinRT, как вы, перенос кода, это слишком можно сделать, но немного сложнее.  
Ниже приведен пример вызова API WinRT XSAPI, с помощью C + +/ WinRT, учитывая C + +/ CX пользователя ^ объекта.

```c++
::Windows::Xbox::System::User^ user1 = ::Windows::Xbox::System::User::Users->GetAt(0);
winrt::Windows::Xbox::System::User cppWinrtUser(nullptr);
winrt::copy_from(cppWinrtUser, reinterpret_cast<winrt::ABI::Windows::Xbox::System::IUser*>(user1));
winrt::Microsoft::Xbox::Services::XboxLiveContext xblContext(cppWinrtUser);
```


### <a name="xsapi-c11-based-api"></a>API на основе XSAPI C ++ 11

- Использует кросс-платформенный ISO standard C ++ 11
- Поддерживает приложения, написанные на C++
- Поддерживает приложения, предназначенные для платформы Xbox один XDK, а Windows платформы Универсальной x86, x64 и архитектуры ARM.
- Ошибки обрабатываются через std::error_code.
- C ++ 11 на основе API-Интерфейс является рекомендованным интерфейсом API для игровых движков C++ для повышения производительности и Улучшенная отладка.
- Если вы находитесь в Xbox Live Creators Program, до включения заголовка XSAPI определите XBOX_LIVE_CREATORS_SDK. Это ограничивает контактную зону API только теми, которые могут использоваться разработчиками в Xbox Live Creators Program и изменении метода входа в систему для работы, для которых в Creators program.  Например:

```c++
#define XBOX_LIVE_CREATORS_SDK
#include "xsapi\services.h"
```

- C + +/ WinRT также поддерживается.  Дополнительные сведения о C + +/ WinRT можно найти в [https://moderncpp.com/2016/10/13/cppwinrt-available-on-github/](https://moderncpp.com/2016/10/13/cppwinrt-available-on-github/)

Чтобы использовать C + +/ WinRT, с помощью API C++ XSAPI, прежде чем включать заголовок XSAPI определить XSAPI_CPPWINRT.  Например:

```c++
#define XSAPI_CPPWINRT
#include "xsapi\services.h"
```

Ниже приведен пример вызова API XSAPI C++, с помощью C + +/ WinRT:

```c++
winrt::Windows::Xbox::System::User cppWinrtUser = winrt::Windows::Xbox::System::User::Users().GetAt(0);
std::shared_ptr<xbox::services::xbox_live_context> xboxLiveContext = std::make_shared<xbox::services::xbox_live_context>(cppWinrtUser);
```

### <a name="xsapi-c-based-api"></a>API на основе XSAPI C

- Позволяет заголовки для управления выделения памяти при вызове XSAPI.
- Позволяет заголовки получить полный контроль потока обработки при вызове XSAPI.
- Использует HTTP библиотеку, libHttpClient, для разработки игр.

Дополнительные сведения см. в разделе [введение в API-интерфейсы Xbox Live C](xsapi-flat-c.md).
