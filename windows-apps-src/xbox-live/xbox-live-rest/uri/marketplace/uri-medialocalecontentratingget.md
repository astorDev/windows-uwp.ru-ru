---
title: GET (/media/{marketplaceId}/contentRating)
assetID: e677acdb-d905-3bea-b0ca-6d8becd663c0
permalink: en-us/docs/xboxlive/rest/uri-medialocalecontentratingget.html
description: " GET (/media/{marketplaceId}/contentRating)"
ms.date: 10/12/2017
ms.topic: article
keywords: xbox live, xbox, игры, uwp, windows 10, xbox one
ms.localizationpriority: medium
ms.openlocfilehash: 8d1cb9d09de8671d4cd3d61e96a8335412237e5c
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57641589"
---
# <a name="get-mediamarketplaceidcontentrating"></a>GET (/media/{marketplaceId}/contentRating)
Получите токен оценки содержимого. Доменом для таких URI является `eds.xboxlive.com`.
 
  * ["Примечания"](#ID4EV)
  * [Параметры URI](#ID4ELB)
  * [Параметры строки запроса](#ID4EWB)
 
<a id="ID4EV"></a>

 
## <a name="remarks"></a>Замечания
 
Принудительное применение родительский контроль над содержимым дочерние элементы могут см. в разделе, является замысловатой задачей. Не только каждого типа элемента мультимедиа имеет свою собственную систему оценки, но эти системы оценки может меняться от страны. Это означает, что существуют различные части данных, необходимо указать, чтобы правильно отфильтровать все элементы.
 
Вместо указания всех параметров во всех вызовах API, этот API создает значение, передаваемое в **combinedContentRating** параметров на других языках программирования и по-прежнему устанавливать те же сведения. Это позволяет упростить API-интерфейсы в использовании и обслуживании, как несколько параметров, передаваемых в этот API объединяются в одно значение для повторного использования для других API-интерфейсов.
 
Несмотря на то, что со временем может меняться точных значений, возвращаемая этим API, им следует менять очень редко (например, между выпуски служб обнаружения развлечения (EDS)) и таким образом, можно кэшировать для длительных периодов времени. Любой API принятия **combinedContentRating** параметр даст информативное сообщение об ошибке, если переданное значение является недопустимым, что это указывает, что вызывающий объект просто должен вызывать этот API еще раз, чтобы получить обновленное значение. Если API принимает **combinedContentRating** параметра, а исключения, не указан, фильтрация содержимого не будет выполняться на основе родительского контроля. 

> [!NOTE] 
> Это не значит, что возвращается только «безопасных» содержимое — это означает, что возвращается все содержимое, включая потенциально откровенный контент. 


  
<a id="ID4ELB"></a>

 
## <a name="uri-parameters"></a>Параметры универсального кода ресурса (URI)
 
| Параметр| Тип| Описание| 
| --- | --- | --- | --- | 
| marketplaceId| Строка| Обязательный. Строковое значение, полученное от <b>Windows.Xbox.ApplicationModel.Store.Configuration.MarketplaceId</b>.| 
  
<a id="ID4EWB"></a>

 
## <a name="query-string-parameters"></a>Параметры строки запроса
 
| Параметр| Тип| Описание| 
| --- | --- | --- | --- | --- | --- | --- | 
| filterExplicit| Логическое значение| Необязательно. Фильтрует явные музыки.| 
| filterFamilyOnlyApps| Логическое значение| Необязательно. Фильтровать приложения, не помечен как семейство понятное.| 
| filterUnrated| Логическое значение| Необязательно. Определяет, следует ли включать содержимое, которое без оценки в ответе.| 
| maxGameRating| 32-разрядное знаковое целое число| Необязательно. Фильтрует игры.| 
| maxMovieRating| 32-разрядное знаковое целое число| Необязательно. Фильтрует фильмы выше определенного уровня.| 
| maxTVRating| 32-разрядное знаковое целое число| Необязательно. Фильтрует TV.| 
  
<a id="ID4E5D"></a>

 
## <a name="see-also"></a>См. также
 
<a id="ID4EAE"></a>

 
##### <a name="parent"></a>Parent 

[/Media/ {marketplaceId} / contentRating](uri-medialocalecontentrating.md)

  
<a id="ID4EKE"></a>

 
##### <a name="further-information"></a>Дополнительные сведения 

[Общие заголовки EDS](../../additional/edscommonheaders.md)

 [Параметры ТАБЛИЦ](../../additional/edsparameters.md)

 [EDS запросить уточнения](../../additional/edsqueryrefiners.md)

 [Идентификаторы URI Marketplace](atoc-reference-marketplace.md)

 [Дополнительная справка](../../additional/atoc-xboxlivews-reference-additional.md)

   