---
author: TylerMSFT
title: "Запуск приложения \"Люди\""
description: "В этом разделе приводится описание схемы URI ms-people. Ваше приложение может использовать эту схему URI для запуска приложения \"Люди\" для конкретных действий."
ms.assetid: 1E604599-26EF-421C-932F-E9935CDB248E
ms.sourcegitcommit: 39a012976ee877d8834b63def04e39d847036132
ms.openlocfilehash: fd3c38dd0b6df2f430d7be4c40e7131d4ae98616

---

# Запуск приложения "Люди"


\[ Обновлено для приложений UWP в Windows10. Статьи о Windows 8.x см. в [архиве](http://go.microsoft.com/fwlink/p/?linkid=619132) \]


В этом разделе приводится описание схемы URI **ms-people:**. Ваше приложение может использовать эту схему URI для запуска приложения «Люди» для конкретных действий.

## Справка по схеме URI ms-people:


<table>
<colgroup>
<col width="50%" />
<col width="50%" />
</colgroup>
<thead>
<tr class="header">
<th align="left">Результаты</th>
<th align="left">Схема URI</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left">Другие приложения смогут запускать главную страницу приложения «Люди».</td>
<td align="left">ms-people:</td>
</tr>
<tr class="even">
<td align="left">Другие приложения смогут запускать страницу параметров приложения «Люди».</td>
<td align="left">ms-people:settings</td>
</tr>
<tr class="odd">
<td align="left">Другие приложения смогут использовать строку поиска и открывать страницу с результатами в приложении «Люди».
<div class="alert">
**Примечание.**  
<p>Параметры должны вводиться с учетом регистра.</p>
<p>Если вы укажете синтаксис неправильно или пропустите значение search string, по умолчанию будет открываться полный список контактов без фильтрации.</p>
</div>
<div>
 
</div></td>
<td align="left">ms-people:search?SearchString=&lt;contactsearchinfo&gt;</td>
</tr>
<tr class="even">
<td align="left">При запуске открывается карточка найденного контакта или временная карточка, если контакт не найден. Если входной параметр не указан, при запуске приложения «Люди» открывается список контактов.
<div class="alert">
**Примечание.**  
<p>Параметры должны вводиться с учетом регистра.</p>
<p>Порядок параметров не имеет значения.</p>
<p>Если совпадений несколько, открывается первое из них.</p>
</div>
<div>
 
</div></td>
<td align="left">ms-people:viewcontact:?ContactId=&lt;contactid&gt;&amp;AggregatedId=&lt;aggid&gt;&amp;PhoneNumber= &lt;phonenum&gt;&amp;Email=&lt;email&gt;&amp;ContactName=&lt;name&gt;&amp;Contact=&lt;contactobj&gt;</td>
</tr>
<tr class="odd">
<td align="left">При запуске приложения "Люди" открывается страница "Сохранить контакт" для сохранения указанного номера телефона или адреса электронной почты.
<div class="alert">
**Примечание.**  
<p>Параметры должны вводиться с учетом регистра.</p>
<p>Порядок параметров не имеет значения.</p>
</div>
<div>
 
</div></td>
<td align="left">ms-people:savetocontact?PhoneNumber= &lt;phonenum&gt;&amp;Email=&lt;email&gt;&amp;ContactName=&lt;name&gt;</td>
</tr>
</tbody>
</table>

 

## Справка по параметру ms-people:search:


<table>
<colgroup>
<col width="33%" />
<col width="33%" />
<col width="33%" />
</colgroup>
<thead>
<tr class="header">
<th align="left">Параметр</th>
<th align="left">Описание</th>
<th align="left">Пример</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left">**SearchString**</td>
<td align="left"><p>Необязательный.</p>
<p>Строка поиска для сведений о поиске контакта.</p>
<p>Номер телефона или имя контакта.</p></td>
<td align="left"><p>ms-people:search?SearchString=Smith</p></td>
</tr>
</tbody>
</table>

 

## Справка по параметру ms-people:viewcontact:


<table>
<colgroup>
<col width="33%" />
<col width="33%" />
<col width="33%" />
</colgroup>
<thead>
<tr class="header">
<th align="left">Параметр</th>
<th align="left">Описание</th>
<th align="left">Пример</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left">**ContactId**</td>
<td align="left"><p>Необязательный.</p>
<p>Идентификатор контакта.</p></td>
<td align="left"><p>ms-people:viewcontact?ContactId={ContactId}</p></td>
</tr>
<tr class="even">
<td align="left">**PhoneNumber**</td>
<td align="left"><p>Необязательный.</p>
<p>Номера телефона контакта.</p></td>
<td align="left"><p>ms-people:viewcontact?PhoneNumber=%2014257069326</p></td>
</tr>
<tr class="odd">
<td align="left">**Электронная почта**</td>
<td align="left"><p>Необязательный.</p>
<p>Адрес электронной почты контакта.</p></td>
<td align="left"><p>ms-people:viewcontact?Email=johnsmith@contsco.com</p></td>
</tr>
<tr class="even">
<td align="left">**ContactName**</td>
<td align="left"><p>Необязательный.</p>
<p>Имя контакта.</p></td>
<td align="left"><p>ms-people:viewcontact?ContactName=John%20%Smith</p></td>
</tr>
<tr class="odd">
<td align="left">**Contact**</td>
<td align="left"><p>Необязательный.</p>
<p>Объект контакта.</p></td>
<td align="left"><p>ms-people:viewcontact?Contact={Serialized Contact}</p></td>
</tr>
</tbody>
</table>

 

## Справка по параметру ms-people:savetocontact:


<table>
<colgroup>
<col width="33%" />
<col width="33%" />
<col width="33%" />
</colgroup>
<thead>
<tr class="header">
<th align="left">Параметр</th>
<th align="left">Описание</th>
<th align="left">Пример</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left">**PhoneNumber**</td>
<td align="left"><p>Необязательный.</p>
<p>Номера телефона контакта.</p></td>
<td align="left"><p>ms-people:savetocontact?PhoneNumber=%2014257069326</p></td>
</tr>
<tr class="even">
<td align="left">**Электронная почта**</td>
<td align="left"><p>Необязательный.</p>
<p>Адрес электронной почты контакта.</p></td>
<td align="left"><p>ms-people:savetocontact?Email=johnsmith@contsco.com</p></td>
</tr>
<tr class="odd">
<td align="left">**ContactName**</td>
<td align="left"><p>Необязательный.</p>
<p>Имя контакта.</p></td>
<td align="left"><p>ms-people:savetocontact?Email=johnsmith@contsco.com&amp;ContactName= John%20%Smith</p></td>
</tr>
</tbody>
</table>

 

 

 



<!--HONumber=Jun16_HO5-->


