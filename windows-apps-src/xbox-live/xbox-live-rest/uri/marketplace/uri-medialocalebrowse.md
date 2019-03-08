---
title: /media/{marketplaceId}/browse
assetID: 4fedc780-b3c2-c83b-e7af-9e18666a4771
permalink: en-us/docs/xboxlive/rest/uri-medialocalebrowse.html
description: " /media/{marketplaceId}/browse"
ms.date: 10/12/2017
ms.topic: article
keywords: xbox live, xbox, игры, uwp, windows 10, xbox one
ms.localizationpriority: medium
ms.openlocfilehash: f692fb66580e20ffeefb3595b8cf9d795f504311
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57589689"
---
# <a name="mediamarketplaceidbrowse"></a>/media/{marketplaceId}/browse
Позволяет просматривать для элементов в группе один носитель. Обзор API позволяет клиентам осуществлять поиск элементов в группу один носитель. Страниц данных может осуществляться без последовательности с помощью параметра skipItems вместо того чтобы использовать маркер продолжения.
 
Этот API также позволяет просматривать в дочерние элементы данного элемента. Например передав идентификатор и параметр MediaItemType для игр Xbox 360, это позволяет просматривать и diltering в дочерних элементах этого элемента, например элементов аватар или DLC для игры.
 
Этот API принимает уточнения запроса.
 
Некоторые сценарии для получения дочерних элементов:
 
   * Альбом для дорожек
   * Ряды Сезоны
   * Времена года, чтобы эпизоды
   * Отслеживать видео, музыки
   * Исполнитель для альбомов
   * Игры для надстройки игры (DLC, аватар, темы, и т.д.)
  
Доменом для таких URI является `eds.xboxlive.com`.
 
  * [Параметры URI](#ID4EMB)
 
<a id="ID4EMB"></a>

 
## <a name="uri-parameters"></a>Параметры универсального кода ресурса (URI)
 
| Параметр| Тип| Описание| 
| --- | --- | --- | 
| marketplaceId| Строка| Обязательный. Строковое значение, полученное от <b>Windows.Xbox.ApplicationModel.Store.Configuration.MarketplaceId</b>.| 
  
<a id="ID4ENC"></a>

 
## <a name="valid-methods"></a>Допустимые методы

[Получение (media / {marketplaceId} или обзор)](uri-medialocalebrowseget.md)

&nbsp;&nbsp;Позволяет просматривать для элементов в группе один носитель. 
 
<a id="ID4EXC"></a>

 
## <a name="see-also"></a>См. также
 
<a id="ID4EZC"></a>

 
##### <a name="parent"></a>Parent 

[Идентификаторы URI Marketplace](atoc-reference-marketplace.md)

  
<a id="ID4EDD"></a>

 
##### <a name="further-information"></a>Дополнительные сведения 

[Общие заголовки EDS](../../additional/edscommonheaders.md)

 [Параметры ТАБЛИЦ](../../additional/edsparameters.md)

 [EDS запросить уточнения](../../additional/edsqueryrefiners.md)

 [Дополнительная справка](../../additional/atoc-xboxlivews-reference-additional.md)

   