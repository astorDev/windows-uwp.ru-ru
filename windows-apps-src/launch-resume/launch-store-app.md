---
title: Запуск приложения Microsoft Store
description: В этом разделе описывается схема URI ms-windows-store. Ваше приложение может использовать эту схему URI для запуска приложения Microsoft Store на определенных страницах в магазине.
ms.assetid: 9A9C6576-1637-47D1-AC3B-D1A20D49E0FF
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: f64a290443ed5e45a5379b13f70dcc1ea2f57fa9
ms.sourcegitcommit: d7613c791107f74b6a3dc12a372d9de916c0454b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/05/2018
ms.locfileid: "8751969"
---
# <a name="launch-the-microsoft-store-app"></a>Запуск приложения Microsoft Store



В этом разделе описывается схема URI **ms-windows-store:**. Ваше приложение может использовать эту схему URI для запуска приложения Microsoft Store на определенных страницах в магазине с помощью метода [**LaunchUriAsync**](https://msdn.microsoft.com/library/windows/apps/hh701476) .

В этом примере показано, как открыть страницу игр в магазине.

```cs
bool result = await Windows.System.Launcher.LaunchUriAsync(new Uri("ms-windows-store://navigatetopage/?Id=Games"));
```

## <a name="ms-windows-store-uri-scheme-reference"></a>Справка по схеме URI ms-windows-store:

<table>
<tr><th>описание</th><th></th><th>Схема URI</th></tr>
<tr><td>Запускает домашнюю страницу Магазина.</td><td /><td>ms-windows-store://home</td></tr>
<tr><td>Запускает вертикаль верхнего уровня в Магазине.<p>Примечание. Не у всех пользователей есть доступ ко всем вертикалям.</p>
</td><td /><td>
<p>ms-windows-store://navigatetopage/?Id=Apps </p>
<p>ms-windows-store://navigatetopage/?Id=Games</p>
<p>ms-windows-store://navigatetopage/?Id=Music</p>
<p>ms-windows-store://navigatetopage/?Id=Video</p>
<p>ms-windows-store://navigatetopage/?Id=LOB</p>
</td>
</tr>
<tr>
<td rowspan="4">Запускает страницу сведений о продукте (PDP). <p>Код продукта в Магазине рекомендуется использовать клиентам с Windows 10, он работает во всех версиях ОС, однако предыдущие методы решения этой задачи (например, с помощью PFN) по-прежнему поддерживаются.</p>
<p>Эти значения можно найти в [Центре партнеров](https://partner.microsoft.com/dashboard) на странице " <a href="https://msdn.microsoft.com/library/windows/apps/mt148561.aspx">удостоверение приложения</a> " в разделе управления приложениями для каждого приложения.</p>
</td>
<td>
Код продукта в Магазине <p>(рекомендуется)</p>
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
<td>ms-windows-store://pdp/?PhoneAppId=ca05b3ab-f157-450c-8c49-a1f127f5e71d </td>
</tr>
<tr>
<td>Код продукта (Windows 8.x)</td>
<td>ms-windows-store://pdp/?AppId=f022389f-f3a6-417e-ad23-704fbdf57117
</td>
</tr>
<tr>
<td rowspan="4">Запускает запись обзора для продукта.</td>
<td>Код продукта в Магазине <p>(рекомендуется)</p></td>
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

 

 
