---
title: Обработка ошибок API на C++
description: Узнайте, как обрабатывать ошибки при выполнении вызову службы Xbox Live в интерфейсах API C++.
ms.assetid: 10b47e68-8b1f-4023-96a4-404f3f6a9850
ms.date: 04/04/2017
ms.topic: article
keywords: Xbox live, xbox, игры, универсальной платформы Windows, windows 10 для настольных ПК, xbox, обработка ошибок
ms.localizationpriority: medium
ms.openlocfilehash: 90fd816f8d44b27c1df0ded9bee6473f642478a9
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57636529"
---
# <a name="c-api-error-handling"></a>Обработка ошибок API на C++

В C++ API вместо создания исключений большинство вызовы будут возвращать xbox_live_result < payload_type > соответствующим образом.

## <a name="xboxliveresult-structure"></a>Структура xbox_live_result
xbox_live_result имеет 3 элемента:
1. Ошибки, возвращаемый операцией,
2. Сообщение об ошибке, используемые для отладки и
3. Полезные данные результата (может быть пустым, если произошла ошибка)»

Можно получить дополнительные сведения о xbox_live_result как а также какие ошибки коды находятся в документации по, Xbox Live.

Эта структура выглядит следующим образом:

```cpp
template<typename T>
class xbox_live_result
{
    const std::error_code& err();
    const std::string& err_message();
    T& payload();
};
```

**Err** -возвращает ошибку.  Будет ссылкой на NULL без ошибок.  Это ведет себя как ошибки действует тем, что примитивное значение можно получить, вызвав value() STL C++.  Вызов message() получить строковое представление.  Таким образом, если код ошибки означает, что «Недопустимый аргумент», затем ```err().message()``` текстовые «Недопустимый аргумент».

**err_message** -описываются ошибки.  Например если **err** — «Недопустимый аргумент», затем **err_message** бы подробнее на какой аргумент является недопустимым.

**полезные данные** -вернуть интересующий элемент.  Для примера рассмотрим ```xbox_live_result<achievement>``` может получить из вызывающего get_achievement.  В этом примере полезные данные будет достижение сам (если сообщение об ошибке не существует).

## <a name="example"></a>Пример

```cpp
// Function which returns an xbox_live_result
xbox_live_result<std::shared_ptr<title_presence_change_subscription>> presenceChangeSubscriptionResult =
xbox::services::presence::subscribe_to_title_presence_change(
    xboxUserId,
    titleId
    );

printf("Error value %d, string %s", achievementResult.err().value(), achievementResult.err().message());

// Would output:
// "0 Success" if successful
// "401 Unauthorized" if auth issue

if (!achievementResult.err())
{
  // Do things on success.  Payload will be populated if applicable.
  std::shared_ptr<title_presence_change_subscription> presenceChangeSubscription = presenceChangeSubscriptionResult->payload();

  // ...
}
else if (achievement.err() == xboxlive_error_code::http_status_403_forbidden)
{
  // Special handling for 403 errors
}
else if (achievementResult.err() == xbox_live_error_condition::auth)
{
  // Handle broad auth failures.  See below section for more info on xbox_live_error_condition
}

```

## <a name="using-xboxliveerrorcondition-to-test-against-broad-error-categories"></a>С помощью xbox_live_error_condition для проверки категории широкий ошибок
В приведенном выше примере мы проверить код ошибки 403 ошибок, а также так называемые ```xbox_live_error_condition::auth```.

 При использовании функции err() xbox_live_result одного теста от коды ошибок по отдельности.  Например для ошибках класса 400 может иметь отдельные тестирования и потока для элемента управления:

* xbox_live_error_code::http_status_400_bad_request
* xbox_live_error_code::http_status_401_unauthorized
* xbox_live_error_code::http_status_403_forbidden
* и т.д.

Но обычно это не требуется делать, и вы хотите протестировать класс ошибок как один.  Поэтому можно протестировать класс ошибок с помощью перечисления, доступные в ```xbox_live_error_condition``` класса.  Мы реализуем перегрузка оператора равенства, который позволит автоматизировать тестирование многие коды ошибок.  В дополнение к ```auth```, делятся на категории, например ```rta``` и ```http```.  Полный список можно найти в *errors.h* или в *xblsdk_cpp.chm*.

Видео, охватывающее это и некоторые другие функции API-интерфейс службы Xbox C++, ознакомьтесь со сведениями наших разговора XFest [видео 2015 Xfest](https://developer.xboxlive.com/en-us/platform/documentlibrary/events/Pages/Xfest2015.aspx) под *XSAPI: C++, исключения не!*
