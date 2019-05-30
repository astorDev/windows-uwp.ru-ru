---
title: Запуск приложения Microsoft Store
description: В этом разделе описывается схема URI ms-windows-store. Приложение может использовать эту схему URI, чтобы запустить приложение Microsoft Store для определенных страниц в Store.
ms.assetid: 9A9C6576-1637-47D1-AC3B-D1A20D49E0FF
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: fd0e7137f31a8f1620f7937b52efe1ca84a6b99a
ms.sourcegitcommit: ac7f3422f8d83618f9b6b5615a37f8e5c115b3c4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/29/2019
ms.locfileid: "66370787"
---
# <a name="launch-the-microsoft-store-app"></a>Запуск приложения Microsoft Store



В этом разделе описывается **ms-windows-store:** Схема URI. Приложение может использовать эту схему URI, чтобы запустить приложение Microsoft Store для определенных страниц в хранилище с помощью [ **LaunchUriAsync** ](https://docs.microsoft.com/uwp/api/windows.system.launcher.launchuriasync) метод.

В этом примере показано, как открыть Microsoft Store на странице игры.

```cs
bool result = await Windows.System.Launcher.LaunchUriAsync(new Uri("ms-windows-store://navigatetopage/?Id=Games"));
```

## <a name="ms-windows-store-uri-scheme-reference"></a>ms-windows-store: Схема URI-ссылка

<table>
<tr><th>Описание</th><th></th><th>Схема URI</th></tr>
<tr><td>Запускает домашнюю страницу Магазина.</td><td /><td>ms-windows-store://home</td></tr>
<tr><td>Запускает вертикаль верхнего уровня в Магазине.<p>Примечание. Не все пользователи имеют доступ для всех отраслей.</p>
</td><td /><td>
<p>ms-windows-store://navigatetopage/?Id=Apps </p>
<p>ms-windows-store://navigatetopage/?Id=Games</p>
<p>ms-windows-store://navigatetopage/?Id=Music</p>
<p>ms-windows-store://navigatetopage/?Id=Video</p>
<p>ms-windows-store://navigatetopage/?Id=LOB</p>
</td>
</tr>
<tr>
<td rowspan="4">Запускает страницу сведений о продукте (PDP). <p>Идентификатор Store рекомендуется для клиентов в Windows 10 и будет работать на всех версиях ОС, но более ранних способов выполнения этого действия (например: PFN) все еще поддерживается.</p>
<p>Эти значения можно найти в <a href="https://partner.microsoft.com/dashboard">центра партнеров</a> на <a href="https://docs.microsoft.com/windows/uwp/publish/view-app-identity-details">удостоверения приложения</a> странице в разделе управления приложения для каждого приложения.</p>
</td>
<td>
Код продукта в Store <p>(рекомендуется)</p>
</td>
<td>
<p>ms-windows-store://pdp/?ProductId=9WZDNCRFHVJL</p>
</td>
</tr>
<tr>
<td>Имя семейства пакетов (PFN)</td>
<td>ms-windows-store://pdp/?PFN= Microsoft.Office.OneNote_8wekyb3d8bbwe
</td>
</tr>
<tr>
<td>Код продукта (Windows Phone 7.x/8.x)</td>
<td>ms-windows-store://pdp/?PhoneAppId=ca05b3ab-f157-450c-8c49-a1f127f5e71d</td>
</tr>
<tr>
<td>Код продукта (Windows 8.x)</td>
<td>ms-windows-store://pdp/?AppId=f022389f-f3a6-417e-ad23-704fbdf57117
</td>
</tr>
<tr>
<td rowspan="4">Запускает запись обзора для продукта.</td>
<td>Код продукта в Store <p>(рекомендуется)</p></td>
<td>ms-windows-store://review/?ProductId=9WZDNCRFHVJL </td>
</tr>
<tr>
<td>Имя семейства пакетов (PFN)</td>
<td>ms-windows-store://review/?PFN= Microsoft.Office.OneNote_8wekyb3d8bbwe
</td>
</tr>
<tr>
<td>Код продукта (Windows Phone 7.x/8.x)</td>
<td>ms-windows-store://reviewapp/?AppId=ca05b3ab-f157-450c-8c49-a1f127f5e71d </td>
</tr>
<tr>
<td>Код продукта (Windows 8.x)</td>
<td>ms-windows-store://review/?AppId=f022389f-f3a6-417e-ad23-704fbdf57117 </td>
</tr>
<tr>
<td>Запускает поиск продуктов, связанных с расширением файла. </td>
<td />
<td>ms-windows-store://assoc/?FileExt=pdf
</td>
</tr>
<tr>
<td>Запускает поиск продуктов, связанных с протоколом.</td>
<td />
<td>ms-windows-store://assoc/?Protocol=ms-word </td>
</tr>
<tr>
<td>Запускает поиск продуктов, связанных с одним или несколькими тегами. Теги должны разделяться запятыми.
</td>
<td />
<td>
<p>ms-windows-store://assoc/?Tags=Photos_Rich_Media_Edit </p>
<p>ms-windows-store://assoc/?Tags=Photos_Rich_Media_Edit, Camera_Capture_App</p>
</td>
</tr>
<tr>
<td>
Запускает поиск по конкретному запросу. Запрос может содержать пробелы.
</td>
<td />
<td>ms-windows-store://search/?query=OneNote </td>
</tr>
<tr>
<td>Запускает поиск продуктов в категории.</td>
<td />
<td>
<p>ms-windows-store://browse/?type=Apps&amp;cat=Productivity</p>
<p>ms-windows-store://browse/?type=Apps&amp;cat=Health+%26+fitness </p>
</td>
</tr>
<tr>
<td>Запускает поиск продуктов от указанного издателя. Имя может содержать пробелы.
</td>
<td />
<td>ms-windows-store://publisher/?name=Microsoft Corporation
</td>
</tr>
<tr><td>Запускает страницу загрузок и обновлений.</td>
<td />
<td>ms-windows-store://downloadsandupdates </td>
</tr>
<tr>
<td>Запускает страницу параметров Магазина.</td>
<td />
<td>ms-windows-store://settings </td>
</tr>
</table>

 

 
