---
title: POST (/untrustedplatform/users/batch/scids/{scid}/data/{pathAndFileName},{type})
assetID: 6e28d794-b5c6-0b70-6d46-957e8ae6e8ac
permalink: en-us/docs/xboxlive/rest/uri-untrustedplatformusersbatchscidssciddatapathandfilenametype-post.html
description: " POST (/untrustedplatform/users/batch/scids/{scid}/data/{pathAndFileName},{type})"
ms.date: 10/12/2017
ms.topic: article
keywords: xbox live, xbox, игры, uwp, windows 10, xbox one
ms.localizationpriority: medium
ms.openlocfilehash: 3acbefdec9af8c60c3a2bc7ece185c2b83e3b63b
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57631799"
---
# <a name="post-untrustedplatformusersbatchscidssciddatapathandfilenametype"></a>POST (/untrustedplatform/users/batch/scids/{scid}/data/{pathAndFileName},{type})
Скачивает несколько файлов из нескольких пользователей с тем же именем файла. Доменом для таких URI является `titlestorage.xboxlive.com`.
 
  * [Параметры URI](#ID4EX)
  * [Авторизации](#ID4ECB)
  * [Текст запроса](#ID4EPB)
  * [Коды состояния HTTP](#ID4E3C)
  * [Заголовки ответа требуется](#ID4EPAAC)
  * [Заголовки ответа необязательно](#ID4ESBAC)
  * [Текст ответа](#ID4E3CAC)
 
<a id="ID4EX"></a>

 
## <a name="uri-parameters"></a>Параметры универсального кода ресурса (URI)
 
| Параметр| Тип| Описание| 
| --- | --- | --- | 
| scid| guid| Идентификатор конфигурации службы для поиска.| 
| pathAndFileName| Строка| Путь и имя файла для элемента, чтобы получить доступ. Допустимые символы для части пути (вплоть до косой черты) содержат прописные буквы (A-Z), строчные буквы (a – z), цифры (0 – 9), подчеркивание (_) и косая черта (/). Компонент пути может быть пустым. Допустимые символы для части имени файла (все, что после косой черты) содержат прописные буквы (A-Z), строчные буквы (a – z), цифры (0 – 9), подчеркивание (_), точка (.) и дефис (-). Имя файла не может быть пустым, заканчиваться точкой или содержать две точки подряд.| 
| type| Строка| Формат данных. Возможные значения: двоичный json.| 
  
<a id="ID4ECB"></a>

 
## <a name="authorization"></a>Authorization 
 
Запрос должен содержать допустимый заголовок авторизации Xbox LIVE. Если вызывающий объект не может быть доступ к этому ресурсу, служба вернет ответ 403 запрещено. Если заголовок отсутствует или является недопустимым, служба вернет ответ 401 Нет доступа. 
  
<a id="ID4EPB"></a>

 
## <a name="request-body"></a>Тело запроса
 
| Свойство| Тип| Описание| 
| --- | --- | --- | --- | --- | --- | 
| xuids| Массив 64-разрядных целых чисел| Список XUIDs, для которой следует загружать файлы.| 
 
<a id="ID4EQC"></a>

 
### <a name="sample-request"></a>Пример запроса
 

```cpp
{
    "xuids" : 
    [
      12345,
      45678,
      78901
    ]
}
      
```

   
<a id="ID4E3C"></a>

 
## <a name="http-status-codes"></a>Коды состояния HTTP 
 
Служба возвращает один из кодов состояния, в этом разделе, в ответ на запрос, сделанный с помощью этого метода по данному ресурсу. Полный список стандартных кодов состояния HTTP, используемых со службами Xbox Live Services, см. в разделе [коды состояния HTTP стандартный](../../additional/httpstatuscodes.md).
 
| Код| Фраза причины| Описание| 
| --- | --- | --- | --- | --- | --- | --- | --- | --- | 
| 200| ОК | Запрос выполнен успешно.| 
| 201| Создано | Сущность была создана.| 
| 400| Неверный запрос | Службе не удалось распознать неправильно сформированный запрос. Обычно недопустимый параметр.| 
| 401| Недостаточно прав | Запрос требует проверки подлинности пользователя.| 
| 403| Запрещено | Запрос не допускается для пользователя или службы.| 
| 404| Не найден | Не удалось найти указанный ресурс.| 
| 406| Неприемлемо | Версия ресурса не поддерживается.| 
| 408| Время ожидания запроса | Запрос заняло слишком много времени.| 
| 500| Внутренняя ошибка сервера | Сервер обнаружил непредвиденное условие, что помешало ему выполнить запрос.| 
| 503| Служба недоступна | Запрос был отрегулирован, выполните запрос еще раз после повторных попыток клиента значение в секундах (например 5 секунд позже).| 
  
<a id="ID4EPAAC"></a>

 
## <a name="required-response-headers"></a>Заголовки ответа требуется
 
| Заголовок| Описание| 
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | 
| Content-Disposition| Описание содержимого части. «Name» и «filename» части заголовка, XUID пользователя, которому принадлежит этот файл.| 
| HttpStatusCode| Код состояния HTTP, связанные с получения этот конкретный файл.| 
  
<a id="ID4ESBAC"></a>

 
## <a name="optional-response-headers"></a>Заголовки ответа необязательно
 
| Заголовок| Описание| 
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | 
| ETag| ETag — это непрозрачный идентификатор, назначенный веб-сервером для определенной версии в URL-адрес ресурса. При изменении содержимого ресурса, на этот URL-адрес назначается другой ETag.| 
| Content-Type| Если файл был успешно извлечен, это тип содержимого файла.| 
| Content-Range| Если файл был успешно извлечен и является частичной загрузки, это диапазон байтов файла содержится в ответе. | 
  
<a id="ID4E3CAC"></a>

 
## <a name="response-body"></a>Тело ответа
 
Если вызов прошел успешно, служба вернет содержимого запрошенных файлов в нескольких частях ответ.
 
<a id="ID4EGDAC"></a>

 
### <a name="sample-response"></a>Пример ответа 
 

```cpp
HTTP/1.1 200 OK
Transfer-Encoding: chunked
Content-Type: multipart/form-data; boundary=c0a9fd75-d036-4294-8b7b-85fea15a31bb

228
--c0a9fd75-d036-4294-8b7b-85fea15a31bb
Content-Disposition: binary; name="123"; filename="123"
HttpStatusCode: 200
ETag: 0x8CF327717411C31
Content-Type: application/octet-stream

asdf123
--c0a9fd75-d036-4294-8b7b-85fea15a31bb
Content-Disposition: binary; name="456"; filename="456"
HttpStatusCode: 200
ETag: 0x8CF32771E954BB8
Content-Type: application/octet-stream

asdf456
--c0a9fd75-d036-4294-8b7b-85fea15a31bb
Content-Disposition: binary; name="789"; filename="789"
HttpStatusCode: 404


--c0a9fd75-d036-4294-8b7b-85fea15a31bb--

0

```

   
<a id="ID4EUDAC"></a>

 
## <a name="see-also"></a>См. также
 
<a id="ID4EWDAC"></a>

 
##### <a name="parent"></a>Parent 

[/untrustedplatform/users/batch/scids/{scid}/data/{pathAndFileName},{type}](uri-untrustedplatformusersbatchscidssciddatapathandfilenametype.md)

   