---
title: VerifyStringResult (JSON)
assetID: 272c688e-179e-c7e9-086b-e76d0d4bcb57
permalink: en-us/docs/xboxlive/rest/json-verifystringresult.html
description: " VerifyStringResult (JSON)"
ms.date: 10/12/2017
ms.topic: article
keywords: xbox live, xbox, игры, uwp, windows 10, xbox one
ms.localizationpriority: medium
ms.openlocfilehash: b01793222be80efccdca1f24f5226a2e9ff78064
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57599489"
---
# <a name="verifystringresult-json"></a>VerifyStringResult (JSON)
Привести коды, соответствующий каждой строке, отправляется [/system/strings/validate](../uri/stringserver/uri-systemstringsvalidate.md).
<a id="ID4ER"></a>


## <a name="verifystringresult"></a>VerifyStringResult

Объект VerifyStringResult имеет следующей спецификации.

| Участник| Тип| Описание|
| --- | --- | --- |
| Код результата| 32-разрядного целого числа без знака| Обязательный. Значение HResult, соответствующий код для отправки строки.|
| offendingString| Строка| Обязательный. Строковое значение, строки, подлежащие отмене.|

<a id="ID4EXB"></a>


## <a name="sample-json-syntax"></a>Образец синтаксиса JSON


```json
{
    "verifyStringResult":
    [
        {"resultCode": "1", "offendingString": "badword"},
        {"resultCode": "0", "offendingString": ""},
        {"resultCode": "0", "offendingString": ""},
        {"resultCode": "0", "offendingString": ""},
    ]
}

```


#### <a name="common-hresult-values"></a>Часто встречающихся значений HResult

| Значение| Имя ошибки|
| --- | --- | --- | --- | --- |
| 0| Успешно|
| 1| Оскорбительное строки|
| 2| Слишком длинная строка|
| 3| Неизвестная ошибка|

<a id="ID4ELD"></a>


## <a name="see-also"></a>См. также

<a id="ID4END"></a>


##### <a name="parent"></a>Parent

[Ссылка на объект JavaScript Object Notation (JSON)](atoc-xboxlivews-reference-json.md)


<a id="ID4EXD"></a>


##### <a name="reference"></a>Справочные материалы

[POST (/ system/строки/validate)](../uri/stringserver/uri-systemstringsvalidatepost.md)
