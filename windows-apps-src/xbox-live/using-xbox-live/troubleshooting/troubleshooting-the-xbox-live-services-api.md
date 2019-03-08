---
title: Устранение неполадок службы Xbox Live API
description: Узнайте, как в журнал Дополнительные сведения об ошибке во время устранения неполадок с помощью API-интерфейсы Xbox Live.
ms.assetid: 3827bba1-902f-4f2d-ad51-af09bd9354c4
ms.date: 04/04/2017
ms.topic: article
keywords: Xbox live, xbox, игры, универсальной платформы Windows, windows 10, xbox, один, устранение неполадок, ошибка, журнал
ms.localizationpriority: medium
ms.openlocfilehash: 67735d83e5ff301ee4434a6917fce814cabe8309
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57621419"
---
# <a name="troubleshooting-the-xbox-live-apis"></a>Устранение неполадок интерфейсы API Xbox Live

## <a name="code"></a>Код

Это трудно диагностировать сбой при использовании только ошибки из уровня API-службы Xbox Live. Дополнительные сведения об ошибке, например ведение журнала для всех вызовов RESTful — может быть недоступен для сервера. Для прослушивания к этим данным, подключить ответа средства ведения журнала и включения трассировки отладки. Ведение журнала ответа позволяет см. в разделе трафика и веб-службы коды ответов HTTP, это обычно бывает так удобно, как трассировки Fiddler.

### <a name="c"></a>C++

В следующем примере кода включает ведение журнала ответ и задает уровень ошибки отладки значению "подробный" (можно также задать уровень ошибки отладки ошибки для отображения только трассировки неудачных вызовов, или значение Выкл. Чтобы отключить трассировку). Итоговый вывод отладки отправляется в область вывода при запуске проекта в Visual Studio.  

```cpp

        // Set up debug tracing to the Output window in Visual Studio.
            xbox::services::system::xbox_live_services_settings::get_singleton_instance()->set_diagnostics_trace_level(
                xbox_services_diagnostics_trace_level::verbose
                );
```

Вы также можете перенаправлять выходные данные отладки в файле журнала следующим образом:

```cpp

        // Set up debug tracing of the Xbox Live Services API traffic to the game UI.
        m_xboxLiveContext->Settings->EnableServiceCallRoutedEvents = true;
        m_xboxLiveContext->Settings->ServiceCallRouted += ref new
        Windows::Foundation::EventHandler<Microsoft::Xbox::Services::XboxServiceCallRoutedEventArgs^>(
            [=] ( Platform::Object^, Microsoft::Xbox::Services::XboxServiceCallRoutedEventArgs^ args )
            {
                gameUI->Log(L"[URL]: " + args->HttpMethod + " " + args->Url->AbsoluteUri);
                gameUI->Log(L"");
                gameUI->Log(L"[Response]: " + args->HttpStatus.ToString() + " " + args->ResponseBody);
            });

```
