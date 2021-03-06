---
title: Запуск приложения "Люди"
description: В этом разделе приводится описание схемы URI ms-people. Ваше приложение может использовать эту схему URI для запуска приложения "Люди" для конкретных действий.
ms.assetid: 1E604599-26EF-421C-932F-E9935CDB248E
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: ab10acab42ab3f03121a7c5a462cb651b0f3f31b
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57595569"
---
# <a name="launch-the-people-app"></a>Запуск приложения "Люди"

В этом разделе описывается **людей ms:** Схема URI. Ваше приложение может использовать эту схему URI для запуска приложения "Люди" для конкретных действий.

## <a name="ms-people-uri-scheme-reference"></a>ms-people: Схема URI-ссылка

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
<p>Параметры должны вводиться с учетом регистра.</p>
<p>Порядок параметров не имеет значения.</p>
<p>Если совпадений несколько, открывается первое из них.</p>
</div>
<div> 
</div></td>
<td align="left">MS-людей: viewcontact? ContactId =&lt;contactid&gt;&amp;AggregatedId =&lt;aggid&gt;&amp;PhoneNumber = &lt;phonenum&gt;&amp;электронной почты =&lt;электронной почты &gt; &amp;ContactName =&lt;имя&gt;&amp;контакт =&lt;contactobj&gt;</td>
</tr>
<tr class="odd">
<td align="left">При запуске приложения "Люди" открывается страница "Сохранить контакт" для сохранения указанного номера телефона или адреса электронной почты.
<div class="alert">
<p>Параметры должны вводиться с учетом регистра.</p>
<p>Порядок параметров не имеет значения.</p>
</div>
<div>
</div></td>
<td align="left">ms-people:savetocontact?PhoneNumber= &lt;phonenum&gt;&amp;Email=&lt;email&gt;&amp;ContactName=&lt;name&gt;</td>
</tr>
<tr class="even">
<td align="left">Запуск выполняется на странице "Сохранить контакт" в приложении "Люди" для добавления нового контакта.
<div class="alert"><p>Используйте <a href="https://docs.microsoft.com/uwp/api/Windows.System.Launcher#Windows_System_Launcher_LaunchUriForResultsAsync_Windows_Foundation_Uri_Windows_System_LauncherOptions_Windows_Foundation_Collections_ValueSet_">LaunchUriForResultsAsync</a>, чтобы открыть новую страницу сохранения контакта. С помощью <strong>LaunchUriAsync</strong> можно запустить только главную страницу приложения "Люди".</p>
<p>Параметры должны вводиться с учетом регистра.</p>
<p>Порядок параметров не имеет значения.</p>
<p>Можно использовать любые поддерживаемые параметры.</p>
</div>
<div>
</div></td>
<td align="left">ms-people:savecontacttask?PhoneNumber= &lt;phonenum&gt;&amp;Email=&lt;email&gt;&amp;ContactName=&lt;name&gt;</td>
</tr>
</tbody>
</table>

## <a name="ms-peoplesearch-parameter-reference"></a>Справка по параметру ms-people:search:

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
<td align="left"><b>SearchString</b></td>
<td align="left"><p>Необязательно.</p>
<p>Строка поиска для сведений о поиске контакта.</p>
<p>Номер телефона или имя контакта.</p></td>
<td align="left"><p>ms-people:search?SearchString=Smith</p></td>
</tr>
</tbody>
</table>

## <a name="ms-peopleviewcontact-parameter-reference"></a>Справка по параметру ms-people:viewcontact:

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
<td align="left"><b>ContactId</b></td>
<td align="left"><p>Необязательно.</p>
<p>Идентификатор контакта.</p></td>
<td align="left"><p>ms-people:viewcontact?ContactId={ContactId}</p></td>
</tr>
<tr class="even">
<td align="left"><b>PhoneNumber</b></td>
<td align="left"><p>Необязательно.</p>
<p>Номера телефона контакта.</p></td>
<td align="left"><p>ms-people:viewcontact?PhoneNumber=%2014257069326</p></td>
</tr>
<tr class="odd">
<td align="left"><b>Email</b></td>
<td align="left"><p>Необязательно.</p>
<p>Адрес электронной почты контакта.</p></td>
<td align="left"><p>ms-people:viewcontact?Email=johnsmith@contsco.com</p></td>
</tr>
<tr class="even">
<td align="left"><b>ContactName</b></td>
<td align="left"><p>Необязательно.</p>
<p>Имя контакта.</p></td>
<td align="left"><p>ms-people:viewcontact?ContactName=John%20%Smith</p></td>
</tr>
<tr class="odd">
<td align="left"><b>Contact</b></td>
<td align="left"><p>Необязательно.</p>
<p>Объект контакта.</p></td>
<td align="left"><p>ms-people:viewcontact?Contact={Serialized Contact}</p></td>
</tr>
</tbody>
</table>

## <a name="ms-peoplesavetocontact-parameter-reference"></a>Справка по параметру ms-people:savetocontact:

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
<td align="left"><b>PhoneNumber</b></td>
<td align="left"><p>Необязательно.</p>
<p>Номера телефона контакта.</p></td>
<td align="left"><p>ms-people:savetocontact?PhoneNumber=%2014257069326</p></td>
</tr>
<tr class="even">
<td align="left"><b>Email</b></td>
<td align="left"><p>Необязательно.</p>
<p>Адрес электронной почты контакта.</p></td>
<td align="left"><p>ms-people:savetocontact?Email=johnsmith@contsco.com</p></td>
</tr>
<tr class="odd">
<td align="left"><b>ContactName</b></td>
<td align="left"><p>Необязательно.</p>
<p>Имя контакта.</p></td>
<td align="left"><p>ms-people:savetocontact?Email=johnsmith@contsco.com&amp;ContactName= John%20%Smith</p></td>
</tr>
</tbody>
</table>

## <a name="ms-peoplesavecontacttask-parameter-reference"></a>Справка по параметру ms-people:savecontacttask:

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

</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left"><b>Компания</b></td>
<td align="left"><p>Необязательно.</p>
<p>Название компании контакта.</p></td>

</tr>
<tr class="even">
<td align="left"><b>FirstName</b></td>
<td align="left"><p>Необязательно.</p>
<p>Имя контакта.</p></td>
</tr>

<tr class="odd">
<td align="left"><b>HomeAddressCity</b></td>
<td align="left"><p>Необязательно.</p>
<p>Город проживания.</p></td>

</tr>
<tr class="even">
<td align="left"><b>HomeAddressCountry</b></td>
<td align="left"><p>Необязательно.</p>
<p>Страна или регион проживания.</p></td>

</tr>
<tr class="odd">
<td align="left"><b>HomeAddressState</b></td>
<td align="left"><p>Необязательно.</p>
<p>Состояние домашнего адреса.</p></td>

</tr>
<tr class="even">
<td align="left"><b>HomeAddressStreet</b></td>
<td align="left"><p>Необязательно.</p>
<p>Улица проживания.</p></td>
</tr>

<tr class="odd">
<td align="left"><b>HomeAddressZipCode</b></td>
<td align="left"><p>Необязательно.</p>
<p>Почтовый индекс домашнего адреса.</p></td>

</tr>
<tr class="even">
<td align="left"><b>homePhone</b></td>
<td align="left"><p>Необязательно.</p>
<p>Номера телефона контакта.</p></td>
</tr>

<tr class="odd">
<td align="left"><b>Должность</b></td>
<td align="left"><p>Необязательно.</p>
<p>Должность контакта.</p></td>
</tr>

<tr class="even">
<td align="left"><b>Фамилия</b></td>
<td align="left"><p>Необязательно.</p>
<p>Фамилия контакта.</p></td>
</tr>

<tr class="odd">
<td align="left"><b>Отчество</b></td>
<td align="left"><p>Необязательно.</p>
<p>Имя отчества контакта.</p></td>
</tr>

<tr class="even">
<td align="left"><b>MobilePhone</b></td>
<td align="left"><p>Необязательно.</p>
<p>Номера мобильного телефона контакта.</p></td>
</tr>

<tr class="odd">
<td align="left"><b>псевдоним</b></td>
<td align="left"><p>Необязательно.</p>
<p>Псевдоним контакта.</p></td>
</tr>

<tr class="even">
<td align="left"><b>Примечания</b></td>
<td align="left"><p>Необязательно.</p>
<p>Примечания о контакте.</p></td>
</tr>

<tr class="odd">
<td align="left"><b>OtherEmail</b></td>
<td align="left"><p>Необязательно.</p>
<p>Другой адрес электронной почты контакта.</p></td>
</tr>

<tr class="even">
<td align="left"><b>PersonalEmail</b></td>
<td align="left"><p>Необязательно.</p>
<p>Личный адрес электронной почты контакта.</p></td>
</tr>

<tr class="odd">
<td align="left"><b>Суффикс</b></td>
<td align="left"><p>Необязательно.</p>
<p>Суффикс контакта.</p></td>
</tr>

<tr class="even">
<td align="left"><b>Заголовок</b></td>
<td align="left"><p>Необязательно.</p>
<p>Форма обращения к контакту.</p></td>
</tr>

<tr class="odd">
<td align="left"><b>Веб-сайта</b></td>
<td align="left"><p>Необязательно.</p>
<p>Веб-сайт контакта.</p></td>
</tr>

<tr class="even">
<td align="left"><b>WorkAddressCity</b></td>
<td align="left"><p>Необязательно.</p>
<p>Город рабочего адреса.</p></td>
</tr>

<tr class="odd">
<td align="left"><b>WorkAddressCountry</b></td>
<td align="left"><p>Необязательно.</p>
<p>Страна или регион рабочего адреса.</p></td>
</tr>

<tr class="even">
<td align="left"><b>WorkAddressState</b></td>
<td align="left"><p>Необязательно.</p>
<p>Состояние рабочего адреса.</p></td>
</tr>

<tr class="odd">
<td align="left"><b>WorkAddressStreet</b></td>
<td align="left"><p>Необязательно.</p>
<p>Улица рабочего адреса.</p></td>
</tr>

<tr class="even">
<td align="left"><b>WorkAddressZipCode</b></td>
<td align="left"><p>Необязательно.</p>
<p>Почтовый индекс рабочего адреса.</p></td>
</tr>

<tr class="odd">
<td align="left"><b>Рабочий</b></td>
<td align="left"><p>Необязательно.</p>
<p>Рабочий адрес электронной почты контакта.</p></td>
</tr>

<tr class="even">
<td align="left"><b>WorkPhone</b></td>
<td align="left"><p>Необязательно.</p>
<p>Номера рабочего телефона контакта.</p></td>
</tr>
</tbody>
</table>
