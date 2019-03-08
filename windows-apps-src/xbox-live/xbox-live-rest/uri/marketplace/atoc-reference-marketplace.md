---
title: Коды URI Marketplace
assetID: 27b6035f-84b9-67a8-6a12-85c450d18a58
permalink: en-us/docs/xboxlive/rest/atoc-reference-marketplace.html
description: " Коды URI Marketplace"
ms.date: 10/12/2017
ms.topic: article
keywords: xbox live, xbox, игры, uwp, windows 10, xbox one
ms.localizationpriority: medium
ms.openlocfilehash: 9fd8112c6e16b3e9d9fb70c34381e88ba5aa6273
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57593879"
---
# <a name="marketplace-uris"></a>Коды URI Marketplace

В этом разделе представлены сведения о универсальный идентификатор ресурса (URI) адреса и связанные методы протокола передачи гипертекста (HTTP) от службы Xbox Live для *marketplace* служб, также известный как развлечения Обнаружение служб (EDS).

Эту службу можно использовать только игры, выполняемых на Xbox 360, на устройстве Windows Phone или на сайте Xbox.com.

Домены для таких URI — это eds.xboxlive.com и inventory.xboxlive.com.

<a id="ID4EPB"></a>

 
## <a name="in-this-section"></a>В этом разделе

[/Users/Me/Inventory](uri-inventory.md)

&nbsp;&nbsp;Обращается к набор инвентаризации, сопоставленные указанный пользователь.

[/ users/me/расходных / {itemID}](uri-inventoryconsumablesitemurl.md)

&nbsp;&nbsp;Доступ, доступ к полному набору сведений для конкретному элементу готовых к использованию.

[/Inventory/ {itemID}](uri-inventoryitemurl.md)

&nbsp;&nbsp;Доступ, доступ к полному набору сведений для конкретному элементу.

[/media/{marketplaceId}/crossMediaGroupSearch](uri-localecrossmediagroupsearch.md)

&nbsp;&nbsp;Обращений к элементы из нескольких групп другой носитель.

[/media/{marketplaceId}/browse](uri-medialocalebrowse.md)

&nbsp;&nbsp;Позволяет просматривать для элементов в группе один носитель.

[/Media/ {marketplaceId} / contentRating](uri-medialocalecontentrating.md)

&nbsp;&nbsp;Оценка содержимого маркера доступа.

[/media/{marketplaceId}/details](uri-medialocaledetails.md)

&nbsp;&nbsp;Возвращает обеспечивают сведения и метаданные о один или несколько элементов.

[/Media/ {marketplaceId} / поля](uri-medialocalefields.md)

&nbsp;&nbsp;Обращается к токен поля.

[/media/{marketplaceId}/metadata/mediaGroups](uri-medialocalemetadatamediagroups.md)

&nbsp;&nbsp;Список всех поддерживаемых mediaGroups для данной версии EDS.

[/media/{marketplaceId}/metadata/mediaGroups/{mediagroup}/mediaItemTypes](uri-medialocalemetadatamediagroupsmediaitemtypes.md)

&nbsp;&nbsp;Обращается к доступных mediaItemTypes на каждую группу мультимедиа для данной версии EDS.

[/Media/ {marketplaceId} / metadata/mediaItemTypes / {mediaItemType} / поля](uri-medialocalemetadatamediaitemtypefields.md)

&nbsp;&nbsp;Обращений к поля, из которых можно предположить, данные, для заданного mediaitemtype и данная версия EDS.

[/media/{marketplaceId}/metadata/mediaItemTypes/{mediaitemtype}/queryrefiners](uri-medialocalemetadatamediaitemtypequeryrefiners.md)

&nbsp;&nbsp;Обращается к уточнения запроса для данного носителя тип элемента.

[/Media/ {marketplaceId} / метаданных/mediaItemTypes / {mediaitemtype} /queryrefiners/ {queryrefinername}](uri-medialocalemetadatamediaitemtypequeryrefinersqueryrefinername.md)

&nbsp;&nbsp;Обращается к допустимые значения для заданного запроса уточнения имени и указанного типа носителя элемента.

[/Media/ {marketplaceId} / метаданных/mediaItemTypes / {mediaitemtype} /queryrefiners/ {queryRefiner} / subQueryRefinerValues](uri-medialocalemediaitemtypequeryrefinersubqueryrefinervalues.md)

&nbsp;&nbsp;Доступ к списку вложенных значений для заданного запроса уточнения значения (например, «subgenres заданного жанра»).

[/Media/ {marketplaceId} / metadata/mediaItemTypes](uri-medialocalemetadatamediaitemtypes.md)

&nbsp;&nbsp;Обращается к все поддерживаемые mediaItemTypes для данной версии EDS.

[/Media/ {marketplaceId} / metadata/mediaItemTypes / {mediaitemtype} / sortorders](uri-medialocalemetadatamediaitemtypesortorders.md)

&nbsp;&nbsp;Обращений к доступных сортировку для типа данного mediaitem и данная версия EDS.

[/Media/ {marketplaceId} / singleMediaGroupSearch](uri-medialocalesinglemediagroupsearch.md)

&nbsp;&nbsp;Результаты поиска для элементов в группе один носитель.

<a id="ID4EFD"></a>


## <a name="see-also"></a>См. также

<a id="ID4EHD"></a>


##### <a name="parent"></a>Parent

[Универсальный код ресурса (URI) идентификатора ссылки](../atoc-xboxlivews-reference-uris.md)


<a id="ID4ERD"></a>


##### <a name="further-information"></a>Дополнительные сведения

[Дополнительная справка](../../additional/atoc-xboxlivews-reference-additional.md)
