---
title: Параметры разбивки по страницам
assetID: f8d059fd-30e7-be60-0a46-c9a833c400c6
permalink: en-us/docs/xboxlive/rest/pagingparameters.html
description: " Параметры разбивки по страницам"
ms.date: 10/12/2017
ms.topic: article
keywords: xbox live, xbox, игры, uwp, windows 10, xbox one
ms.localizationpriority: medium
ms.openlocfilehash: fe80e1666f9eab8caf7e0bbdb2b537bd7661c9a9
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57627299"
---
# <a name="paging-parameters"></a>Параметры разбивки по страницам
 
Некоторые URI службы Xbox Live возвращают коллекции объектов JavaScript Object Notation (JSON). Эти коллекции можно просматривать постранично, указав параметры разбиения на страницы как часть строки запроса, подключенного к URI. Ниже приведен полный список параметров разбиения на страницы. Все коды URI, разрешить параметры разбиения по страницам, связаны в нижней части этой страницы.
 
<a id="ID4E2"></a>

 
## <a name="query-string-parameters"></a>Параметры строки запроса 
 
| Параметр| Обязательно| Тип| Описание| 
| --- | --- | --- | --- | 
| continuationToken| Нет| Строка| Возвращает элементы, начиная с токен данного продолжения. | 
| maxItems| Нет| 32-разрядное знаковое целое число| Максимальное количество элементов, возвращаемых из коллекции, которые можно объединять с <b>skipItems</b> и <b>continuationToken</b> можно получить диапазон элементов. Служба может предоставлять значение по умолчанию, если <b>maxItems</b> отсутствует и может вернуть меньше, чем <b>maxItems</b>, даже если на последней странице результаты еще не были возвращены. | 
| skipItems| Нет| 32-разрядное знаковое целое число| Возвращает элементы, начиная с версии после заданного числа элементов. Например <b>skipItems = «3»</b> будет извлекать элементы получены, начиная с четвертого элемента. | 
  
<a id="ID4EDD"></a>

 
## <a name="see-also"></a>См. также
 
<a id="ID4EFD"></a>

 
##### <a name="parent"></a>Parent  

[Дополнительная справка](atoc-xboxlivews-reference-additional.md)

  
<a id="ID4ERD"></a>

 
##### <a name="reference--get-usersxuidxuidachievementsuriachievementsuri-achievementsusersxuidachievementsgetv2md"></a>Справочник по [GET (/users/xuid({xuid})/achievements)](../uri/achievements/uri-achievementsusersxuidachievementsgetv2.md)

   