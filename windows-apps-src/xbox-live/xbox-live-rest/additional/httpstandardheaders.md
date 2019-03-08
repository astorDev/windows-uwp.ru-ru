---
title: Стандартные заголовки HTTP-запросов и ответов
assetID: a5f8fd96-9393-5234-04ad-837e5c117c92
permalink: en-us/docs/xboxlive/rest/httpstandardheaders.html
description: " Стандартные заголовки HTTP-запросов и ответов"
ms.date: 10/12/2017
ms.topic: article
keywords: xbox live, xbox, игры, uwp, windows 10, xbox one
ms.localizationpriority: medium
ms.openlocfilehash: ca97e82365eab40266b3ffdd84924f71289eede6
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57636519"
---
# <a name="standard-http-request-and-response-headers"></a>Стандартные заголовки HTTP-запросов и ответов
 
<a id="ID4ES"></a>

 
## <a name="request-headers"></a>Заголовки запроса
 
В следующей таблице перечислены стандартные заголовки HTTP, используемый при создании запросов к Xbox Live Services.
 
| Заголовок| Значение| Описание| 
| --- | --- | --- | 
| x-xbl контракт version| 1| Версия контракта API. Требуется во всех запросах Xbox Live Services.| 
| Authorization| STSTokenString| Токен проверки подлинности STS. Значение для этого заголовка извлекается из <b>GetTokenAndSignatureResult.Token</b> свойство. | 
| Content-Type| Application/xml, application/json, multipart/form-data или application/x-www-формы-urlencoded| Указывает тип содержимого, передаваемый с запросом.| 
| Content-Length| Целое значение| Задает длину данных, передаваемых в запросе POST.| 
| Примите языка | Строка| Указывает, как локализовать любой строки, возвращаемые. См. в разделе <a href="https://msdn.microsoft.com/en-us/library/bb975829.aspx">Advanced программирования Xbox 360</a> список сочетаний допустимый язык.| 
  
<a id="ID4E6C"></a>

 
## <a name="response-headers"></a>Заголовки ответа
 
В следующей таблице перечислены стандартный заголовок HTTP, используемый в ответах Xbox Live Services.
 
| Заголовок| Значение| Описание| 
| --- | --- | --- | --- | --- | --- | 
| Content-Type| application/xml, application/json| Указывает тип содержимого, возвращаемых.| 
| Content-Length| Целое значение| Задает длину данных, возвращаемых.| 
  
<a id="ID4EEE"></a>

 
## <a name="see-also"></a>См. также
 
<a id="ID4EGE"></a>

 
##### <a name="parent"></a>Parent  

[Дополнительная справка](atoc-xboxlivews-reference-additional.md)

   