---
title: POST (/system/strings/validate)
assetID: 6a59bc0b-8edd-87bf-efaf-f16efa3bedf7
permalink: en-us/docs/xboxlive/rest/uri-systemstringsvalidatepost.html
description: " POST (/system/strings/validate)"
ms.date: 10/12/2017
ms.topic: article
keywords: xbox live, xbox, игры, uwp, windows 10, xbox one
ms.localizationpriority: medium
ms.openlocfilehash: 70e86567f449674c7a046e072437d9ee715dc6d6
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57595509"
---
# <a name="post-systemstringsvalidate"></a>POST (/system/strings/validate)
Принимает массив строк для проверки и возвращает массив результатов одинакового размера. Доменом для таких URI является `client-strings.xboxlive.com`.
 
  * ["Примечания"](#ID4EV)
  * [Требуемые заголовки запросов](#ID4EIB)
  * [Текст запроса](#ID4ELC)
  * [Коды состояния HTTP](#ID4E4C)
  * [Текст ответа](#ID4ETF)
 
<a id="ID4EV"></a>

 
## <a name="remarks"></a>Замечания
 
Каждый результат указывает, использует ли соответствующую строку приемлемо в Xbox LIVE и содержит строку, вызывающую ошибку, если применимо.
 
Одинаковые строки всегда будет давать одинаковые результаты. Если вы получаете не успешный результат, анализа результат и соответствующим образом изменить строку.
 
 

> [!NOTE] 
> Полученный в результате <b>VerifyStringResult</b> вернет только первое слово обнаружена ошибка в строке. Могут существовать дополнительные слова в строке, вызвавший ошибку. Если вы планируете заменить виновного слов, образуя из строки можно использовать, следует заменить неправильные word или подстрока и повторная проверка строку для поиска дополнительных виновного подстроки.  

 
  
<a id="ID4EIB"></a>

 
## <a name="required-request-headers"></a>Требуемые заголовки запросов
 
| Заголовок| Описание| 
| --- | --- | --- | 
| Authorization| Токен проверки подлинности. Пример. XBL3.0 x = [hash]; [токен].| 
| x-xbl контракт version| Версия контракта API целое число. Для этого API должен быть 1 или 2.| 
  
<a id="ID4ELC"></a>

 
## <a name="request-body"></a>Тело запроса
 
Тело запроса представляет собой массив строк, нет ограничений на размер массива и 512 символов в строке.
 
<a id="ID4ETC"></a>

 
### <a name="sample-request"></a>Пример запроса
 

```cpp
{
    "stringstoVerify":
    [
        "Poppycock",
        "The quick brown fox jumped over the lazy dog.",
        "Hey, want to hang out?",
        "oh noes"
    ]
}
      
```

   
<a id="ID4E4C"></a>

 
## <a name="http-status-codes"></a>Коды состояния HTTP
 
Служба возвращает один из кодов состояния, в этом разделе, в ответ на запрос, сделанный с помощью этого метода по данному ресурсу. Полный список стандартных кодов состояния HTTP, используемых со службами Xbox Live Services, см. в разделе [коды состояния HTTP стандартный](../../additional/httpstatuscodes.md).
 
| Код| Фраза причины| Описание| 
| --- | --- | --- | --- | --- | --- | 
| 200| ОК| Все строки обработаны успешно. Это означает, что все строки были положительные значения HRESULT.| 
| 401| Недостаточно прав| Запрос требует проверки подлинности пользователя.| 
| 403| Запрещено| Запрос не допускается для пользователя или службы.| 
| 406| Неприемлемо| Отсутствует <b>Content-type: application/json</b> заголовка.| 
| 408| Время ожидания запроса| Службе не удалось распознать неправильно сформированный запрос. Обычно недопустимый параметр.| 
  
<a id="ID4ETF"></a>

 
## <a name="response-body"></a>Тело ответа
 
Возвращает массив [VerifyStringResult (JSON)](../../json/json-verifystringresult.md), такого же размера, что и массив запроса.
  
<a id="ID4EAG"></a>

 
## <a name="see-also"></a>См. также
 
<a id="ID4ECG"></a>

 
##### <a name="parent"></a>Parent 

[/System/strings/Validate](uri-systemstringsvalidate.md)

   