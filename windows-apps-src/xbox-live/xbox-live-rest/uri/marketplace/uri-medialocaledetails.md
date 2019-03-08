---
title: /media/{marketplaceId}/details
assetID: bc8758ed-2f90-b501-5c3f-6f253f02d754
permalink: en-us/docs/xboxlive/rest/uri-medialocaledetails.html
description: " /media/{marketplaceId}/details"
ms.date: 10/12/2017
ms.topic: article
keywords: xbox live, xbox, игры, uwp, windows 10, xbox one
ms.localizationpriority: medium
ms.openlocfilehash: f58e5247c3fd52e84a3a9bab28c6926f74e864e3
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57655709"
---
# <a name="mediamarketplaceiddetails"></a>/media/{marketplaceId}/details
Возвращает обеспечивают сведения и метаданные о один или несколько элементов. Доменом для таких URI является `eds.xboxlive.com`.
 
Сведения, API отличается от связанных API и обзор API (при passin идентификатор), что эти API возвращаемые сведения о других элементах, которые связаны с Идентификатором fiven, явно или неявно, то время как сведения о API возвращает Дополнительные сведения о один и тот же элемент.
 
Несколько идентификаторов типов элементов другие средства передачи можно передать в один вызов (условии, что они не типа ProviderContentID - см. ниже), но все они должны принадлежать к той же группе мультимедиа. Тем не менее существует несколько сценариев клиента, где вызывающая сторона не знает, группа мультимедиа. API-Интерфейс поддерживает это, позволяя sepcial значение «Неизвестно» для группы носитель в следующих ситуациях:
 
   * Тип_идентификатора = XboxHexTitle, которое создаст AppType или GameType элементов
   * Тип_идентификатора = ProviderContentId, которое создаст MovieType или TVType элементов
  
На следующей диаграмме показано всего сопоставление какие идентификатора можно предоставить типы групп, которые мультимедиа:
 
| Тип идентификатора| Тип| GameType| MovieType| MusicArtistType| MusicType| TVType| WebVideoType| Неизвестно| 
| --- | --- | --- | --- | --- | --- | --- | --- | --- | 
| Канонические| Y| Y| Y| Y| Y| Y| Y| N| 
| ZuneCatalog| N| N| Y| Y| Y| Y| N| N| 
| ZuneMediaInstance| N| N| Y| N| Y| Y| N| N| 
| Предложение| Y| Y| Y| N| Y| Y| N| N| 
| AMG| N| N| N| N| Y| N| N| N| 
| MediaNet| N| N| N| N| Y| N| N| N| 
| XboxHexTitle| Y| Y| N| N| N| N| N| Y| 
| ProviderContentId| N| N| Y| N| N| Y| N| Y| 
 
  * [Параметр заметки](#ID4EEH)
  * [Параметры URI](#ID4EUH)
 
<a id="ID4EEH"></a>

 
## <a name="parameter-notes"></a>Параметр заметки
 
<a id="ID4EIH"></a>

 
### <a name="providercontentid"></a>ProviderContentId
 
Используется для поиска поставщика определенным идентификатором. Например: Netflix или Hulu идентификатора.
 
Когда Тип_идентификатора ProviderContentId, принимается только одно значение. Это обусловлено ProviderContentIds — это единственный тип идентификатора, который может содержать "." символов. Так как "." символ также имеет разделитель, который мы используем между идентификаторами, неоднозначность между Какова delimieter между идентификаторами и что является частью идентификатора. Остальная часть API работает так же, как для ProviderContentIds, за исключением функцию массового поиска.
   
<a id="ID4EUH"></a>

 
## <a name="uri-parameters"></a>Параметры универсального кода ресурса (URI)
 
| Параметр| Тип| Описание| 
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | 
| marketplaceId| Строка| Обязательный. Строковое значение, полученное от <b>Windows.Xbox.ApplicationModel.Store.Configuration.MarketplaceId</b>.| 
  
<a id="ID4EWAAC"></a>

 
## <a name="valid-methods"></a>Допустимые методы

[Получение (/media/ {marketplaceId} / сведения)](uri-medialocaledetailsget.md)

&nbsp;&nbsp;Возвращает обеспечивают сведения и метаданные о один или несколько элементов. 
 
<a id="ID4EABAC"></a>

 
## <a name="see-also"></a>См. также
 
<a id="ID4ECBAC"></a>

 
##### <a name="parent"></a>Parent 

[Идентификаторы URI Marketplace](atoc-reference-marketplace.md)

  
<a id="ID4EMBAC"></a>

 
##### <a name="further-information"></a>Дополнительные сведения 

[Общие заголовки EDS](../../additional/edscommonheaders.md)

 [Параметры ТАБЛИЦ](../../additional/edsparameters.md)

 [EDS запросить уточнения](../../additional/edsqueryrefiners.md)

 [Дополнительная справка](../../additional/atoc-xboxlivews-reference-additional.md)

   