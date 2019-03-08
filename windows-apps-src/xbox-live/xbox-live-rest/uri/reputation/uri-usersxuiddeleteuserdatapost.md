---
title: POST (/users/xuid({xuid})/deleteuserdata)
assetID: 8be13ff9-5d42-43a1-f2fa-d550d845a552
permalink: en-us/docs/xboxlive/rest/uri-usersxuiddeleteuserdatapost.html
description: " POST (/users/xuid({xuid})/deleteuserdata)"
ms.date: 10/12/2017
ms.topic: article
keywords: xbox live, xbox, игры, uwp, windows 10, xbox one
ms.localizationpriority: medium
ms.openlocfilehash: dab43079dbba3729ff39f3a2116c377c3b73142a
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57604069"
---
# <a name="post-usersxuidxuiddeleteuserdata"></a>POST (/users/xuid({xuid})/deleteuserdata)
Полностью сбрасывает данные репутации для тестового пользователя. Только для тестирования.

  * ["Примечания"](#ID4EQ)
  * [Параметры URI](#ID4E5)
  * [Авторизации](#ID4EJB)
  * [Требуемые заголовки запросов](#ID4E3B)
  * [Коды состояния HTTP](#ID4EHC)
  * [Текст ответа](#ID4EJF)

<a id="ID4EQ"></a>


## <a name="remarks"></a>Замечания

Вызов этого API удалит все элементы на отзыв и репутации данные от пользователя. Партнеры могут вызывать этот API для любой "песочницы", за исключением розничной торговли. Команда принудительного применения может вызывать этот API с любой идентификатор "песочницы".

Доменом для таких URI является `reputation.xboxlive.com`. Этот URI всегда вызывается через порт 10443.

<a id="ID4E5"></a>


## <a name="uri-parameters"></a>Параметры универсального кода ресурса (URI)

| Параметр| Тип| Описание|
| --- | --- | --- |
| xuid| 64-разрядного целого числа без знака| Xbox пользователя идентификатор (XUID) пользователя, в которых данные удаляются.|

<a id="ID4EJB"></a>


## <a name="authorization"></a>Authorization

Для песочницы розничной торговли **PartnerClaim** от принудительного применения группы.

Для всех других "песочницы" **PartnerClaim** и **SandboxIdClaim**.

<a id="ID4E3B"></a>


## <a name="required-request-headers"></a>Требуемые заголовки запросов

**Тип содержимого: application/json** и **X-Xbl-контракт-Version** (текущая версия — 101).

<a id="ID4EHC"></a>


## <a name="http-status-codes"></a>Коды состояния HTTP

Служба возвращает один из кодов состояния, в этом разделе, в ответ на запрос, сделанный с помощью этого метода по данному ресурсу. Полный список стандартных кодов состояния HTTP, используемых со службами Xbox Live Services, см. в разделе [коды состояния HTTP стандартный](../../additional/httpstatuscodes.md).

| Код| Фраза причины| Описание|
| --- | --- | --- | --- | --- | --- |
| 200| ОК| Сеанс был успешно извлечен.|
| 400| Неверный запрос| Службе не удалось распознать неправильно сформированный запрос. Обычно недопустимый параметр.|
| 401| Недостаточно прав| Запрос требует проверки подлинности пользователя.|
| 404| Не найден| Не удалось найти указанный ресурс.|
| 500| Внутренняя ошибка сервера| Сервер обнаружил непредвиденное условие, что помешало ему выполнить запрос.|
| 503| Служба недоступна| Запрос был отрегулирован, выполните запрос еще раз после повторных попыток клиента значение в секундах (например 5 секунд позже).|

<a id="ID4EJF"></a>


## <a name="response-body"></a>Тело ответа

Ни одна операция выполнена успешно; в противном случае [ServiceError (JSON)](../../json/json-serviceerror.md) документа.

<a id="ID4EWF"></a>


## <a name="see-also"></a>См. также

<a id="ID4EYF"></a>


##### <a name="parent"></a>Parent

[/Users/xuid({xuid})/deleteuserdata](uri-usersxuiddeleteuserdata.md)
