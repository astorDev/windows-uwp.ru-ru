---
title: GET (/inventory/{itemID})
assetID: d3ca14a5-0214-ef42-091e-3f05f2a3482d
permalink: en-us/docs/xboxlive/rest/uri-inventoryitemurlget.html
description: " GET (/inventory/{itemID})"
ms.date: 10/12/2017
ms.topic: article
keywords: xbox live, xbox, игры, uwp, windows 10, xbox one
ms.localizationpriority: medium
ms.openlocfilehash: 446197eb20820304088ddac4a6379fa3b2510873
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57606699"
---
# <a name="get-inventoryitemid"></a>GET (/inventory/{itemID})
Предоставляет полный набор подробных сведений для конкретному элементу. Доменом для таких URI является `inventory.xboxlive.com`.
 
  * ["Примечания"](#ID4EX)
  * [Параметры URI](#ID4EAB)
  * [Текст ответа](#ID4ELB)
 
<a id="ID4EX"></a>

 
## <a name="remarks"></a>Замечания
 
Политика не проверяет, в случае принудительного применения или фильтрация будет выполняться как часть этого вызова.
  
<a id="ID4EAB"></a>

 
## <a name="uri-parameters"></a>Параметры универсального кода ресурса (URI)
 
| Параметр| Тип| Описание| 
| --- | --- | --- | 
| Идентификатор элемента| Строка| Идентификатор, уникально для каждого пользователя для элемента единственного числа инвентаризации| 
  
<a id="ID4ELB"></a>

 
## <a name="response-body"></a>Тело ответа
 
<a id="ID4ERB"></a>

 
### <a name="sample-response"></a>Пример ответа
 
Ответ на запрос GET, при условии, что он проходит проверку подлинности и назначается контекст соответствующей авторизации является элементом одной с полный набор свойств элементов.
 

```cpp
{inventoryItem}
         
```

   
<a id="ID4E4B"></a>

 
## <a name="see-also"></a>См. также
 
<a id="ID4E6B"></a>

 
##### <a name="parent"></a>Parent 

[GET (/inventory/ {itemID})](uri-inventoryget.md)

  
<a id="ID4EJC"></a>

 
##### <a name="further-information"></a>Дополнительные сведения 

[Общие заголовки EDS](../../additional/edscommonheaders.md)

 [Параметры ТАБЛИЦ](../../additional/edsparameters.md)

 [EDS запросить уточнения](../../additional/edsqueryrefiners.md)

 [Идентификаторы URI Marketplace](atoc-reference-marketplace.md)

 [Дополнительная справка](../../additional/atoc-xboxlivews-reference-additional.md)

   