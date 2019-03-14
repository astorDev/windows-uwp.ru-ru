---
title: GET (/media/{marketplaceId}/fields)
assetID: 1d535344-8522-0fd1-4daa-cd0f0a0f1121
permalink: en-us/docs/xboxlive/rest/uri-medialocalefieldsget.html
description: " GET (/media/{marketplaceId}/fields)"
ms.date: 10/12/2017
ms.topic: article
keywords: xbox live, xbox, игры, uwp, windows 10, xbox one
ms.localizationpriority: medium
ms.openlocfilehash: cc3ae8abfe04b7a0b9728d07b9488f9ed7c27816
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57606679"
---
# <a name="get-mediamarketplaceidfields"></a>GET (/media/{marketplaceId}/fields)
Получает токен поля. Доменом для таких URI является `eds.xboxlive.com`.
 
  * ["Примечания"](#ID4EV)
  * [Параметры URI](#ID4EGC)
  * [Параметры строки запроса](#ID4ERC)
 
<a id="ID4EV"></a>

 
## <a name="remarks"></a>Замечания
 
API-интерфейсы, по умолчанию службы обнаружения развлечения (EDS) возвращает минимальное очень небольшой набор полей для каждого элемента:
 
   * Тип элемента мультимедиа
   * Группа мультимедиа
   * Id
   * Имя
  
Чтобы получить дополнительные сведения, примите API-интерфейсы **поля** параметр, указывающий, какие дополнительные части данных должны быть возвращены. Так как существует множество возможных полей, указав свое имя в полном объеме для каждого вызова API будет значительно Раздувание запроса. Вместо этого имена могут быть переданы в этот API, который позволит создать гораздо меньшее значение, можно передать в другие API-интерфейсы.
 
Для любого API, который принимает этот параметр указанное значение должно быть надмножеством все поля во всех типах элемента указанный носитель. Укажите разные наборы полей для различных элементов типов носителей невозможна. Тем не менее если поле применяется один элемент тип носителя, но не другой, он будет отображаться только на носителе типов элементов где данные существуют (например, в том случае, если «AvatarBodyType» включается в вызове [получить (/media/ {marketplaceId} / поля)](uri-medialocalefields.md), только AvatarItems будет содержать поле).
 
Значения, возвращаемые из этого API, высокой кэшируемые — на самом деле, они не должны изменять за исключением между развертываниями EDS. Рекомендуется, при необходимости кэширования кэша длиться не дольше, чем сеанс пользователя.
 
А не только реальные имена полей, этот API принимает «all» является допустимым значением. Это создаст значение, содержащее каждого поля, которое можно указать. Только для разработки, отладки и тестирования рекомендуется использовать значение «все».
 
Кроме того, можно отправить `desired={list of fields separated by a '.'}` к любому интерфейсу API, который принимает **поля** токена.
 
Нельзя передавать в обоих **требуемой** и **поля** друг с другом.
  
<a id="ID4EGC"></a>

 
## <a name="uri-parameters"></a>Параметры универсального кода ресурса (URI)
 
| Параметр| Тип| Описание| 
| --- | --- | --- | 
| marketplaceId| Строка| Обязательный. Строковое значение, полученное от <b>Windows.Xbox.ApplicationModel.Store.Configuration.MarketplaceId</b>.| 
  
<a id="ID4ERC"></a>

 
## <a name="query-string-parameters"></a>Параметры строки запроса
 
| Параметр| Тип| Описание| 
| --- | --- | --- | --- | --- | --- | 
| требуемый| Строка| Обязательный. "." -запятыми список полей, которые должны быть возвращены в дополнение к минимальный набор. Не все поля, указанные гарантированно возвращаться для каждого объекта (данных может просто не существовать для некоторых элементов), но нет полей, за пределами минимальный набор, не указанных здесь будет возвращаться. | 
  
<a id="ID4EMD"></a>

 
## <a name="see-also"></a>См. также
 
<a id="ID4EOD"></a>

 
##### <a name="parent"></a>Parent 

[/Media/ {marketplaceId} / поля](uri-medialocalefields.md)

  
<a id="ID4EYD"></a>

 
##### <a name="further-information"></a>Дополнительные сведения 

[Общие заголовки EDS](../../additional/edscommonheaders.md)

 [Параметры ТАБЛИЦ](../../additional/edsparameters.md)

 [EDS запросить уточнения](../../additional/edsqueryrefiners.md)

 [Идентификаторы URI Marketplace](atoc-reference-marketplace.md)

 [Дополнительная справка](../../additional/atoc-xboxlivews-reference-additional.md)

   