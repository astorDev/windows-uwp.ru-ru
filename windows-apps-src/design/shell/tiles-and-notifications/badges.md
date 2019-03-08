---
Description: Узнайте, как использовать плитки, индикаторы событий, всплывающие и простые уведомления, чтобы предоставлять точки входа в свои приложения и держать пользователей в курсе последних событий.
title: Уведомления на индикаторах событий для приложений UWP
ms.assetid: 48ee4328-7999-40c2-9354-7ea7d488c538
label: Tiles, badges, and notifications
template: detail.hbs
ms.date: 05/19/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: b41069f1ca964f978ba0cff73f5c34a9db133a10
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57649089"
---
# <a name="badge-notifications-for-uwp-apps"></a>Уведомления на индикаторах событий для приложений UWP

 

<div style="float:left; font-size:80%; text-align:left; margin: 0px 15px 15px 0px;">
<img src="images/badge-example.png" alt="A tile with a numeric badge displaying the number 63 to indicate 63 unread mails." style="padding-bottom:0.0em; margin-bottom: 2px" /><br/>Плитка с отображением числового индикатора событий<br/> число 63 для уведомления о 63 непрочитанных сообщениях.</div>

Индикатор событий передает сводную информацию или информацию о статусе, характерную для приложения. Логотипом может быть число (1–99) или один из глифов, предоставленных системой. Примеры уведомлений, наилучшим образом передаваемых через индикатор событий: состояние сетевого подключения в сетевой игре, состояние пользователя в приложении для обмена сообщениями, количество непрочитанных писем в почтовом приложении и количество новых публикаций в приложении для социальных сетей. 

Уведомления на индикаторах событий появляются на значке вашего приложения в панели задач и в правом нижнем углу его запускающей плитки независимо от того, запущено оно или нет. Индикаторы событий могут отображаться на плитках любого размера.  

> [!NOTE]
> Вы не можете предоставить собственное изображение индикатора. Использовать можно только изображения, предоставленные системой.


## <a name="numeric-badges"></a>Числовые индикаторы событий

<table>
    <tr>
        <th>Значение</th>
        <th>Badge</th>
        <th>XML</th>
    </tr>
    <tr>
        <td>Число от 1 до 99. Значение 0 эквивалентно значению глифа "none" и удаляет индикатор события.</td>
        <td><img src="images/badges/badge-numeric.png" alt="A numeric badge less than 100." /></td>
        <td>`<badge value="1"/>`</td>
    </tr>
    <tr>
        <td>Любое число больше 99.</td>
        <td><img src="images/badges/badge-numeric-greater.png" alt="A numeric badge greater than 99." /></td></td>
        <td>`<badge value="100"/>`</td>
    </tr>    
</table>

## <a name="glyph-badges"></a>Индикаторы событий с глифами состояния
Вместо числа индикатор событий может отображать один из нерасширяемых наборов глифов состояния. 

<table>
<tr>
    <th>Состояние</th>
    <th>Глиф</th>
    <th>XML</th>
</tr>
<tr>
    <td>нет</td>
    <td>(Индикатор событий не отображается).</td>
    <td>`<badge value="none"/>`</td>
</tr>
<tr>
    <td>активность</td>
    <td><img src="images/badges/badge-activity.png" alt="Glyph" /></td>
    <td>`<badge value="activity"/>`</td>
</tr>
<tr>
    <td>будильник</td>
    <td><img src="images/badges/badge-alarm.png" alt="Glyph" /></td>
    <td>`<badge value="alarm"/>`</td>
</tr>
<tr>
    <td>оповещение</td>
    <td><img src="images/badges/badge-alert.png" alt="Glyph" /></td>
    <td>`<badge value="alert"/>`</td>
</tr>
<tr>
    <td>внимание</td>
    <td><img src="images/badges/badge-attention.png" alt="Glyph" /></td>
    <td>`<badge value="attention"/>`</td>
</tr>
<tr>
    <td>доступен</td>
    <td><img src="images/badges/badge-available.png" alt="Glyph" /></td>
    <td>`<badge value="available"/>`</td>
</tr>
<tr>
    <td>отошел</td>
    <td><img src="images/badges/badge-away.png" alt="Glyph" /></td>
    <td>`<badge value="away"/>`</td>
</tr>
<tr>
    <td>занят</td>
    <td><img src="images/badges/badge-busy.png" alt="Glyph" /></td>
    <td>`<badge value="busy"/>`</td>
</tr>
<tr>
    <td>ошибка</td>
    <td><img src="images/badges/badge-error.png" alt="Glyph" /></td>
    <td>`<badge value="error"/>`</td>
</tr>
<tr>
    <td>newMessage</td>
    <td><img src="images/badges/badge-newMessage.png" alt="Glyph" /></td>
    <td>`<badge value="newMessage"/>`</td>
</tr>
<tr>
    <td>пауза</td>
    <td><img src="images/badges/badge-paused.png" alt="Glyph" /></td>
    <td>`<badge value="paused"/>`</td>
</tr>
<tr>
    <td>воспроизведение</td>
    <td><img src="images/badges/badge-playing.png" alt="Glyph" /></td>
    <td>`<badge value="playing"/>`</td>
</tr>
<tr>
    <td>недоступен</td>
    <td><img src="images/badges/badge-unavailable.png" alt="Glyph" /></td>
    <td>`<badge value="unavailable"/>`</td>
</tr>
</table>

## <a name="create-a-badge"></a>Создание индикатора событий

Эти примеры демонстрируют Создание обновление индикаторов событий.

### <a name="create-a-numeric-badge"></a>Создание числового индикатора событий

````csharp
private void setBadgeNumber(int num)
{

    // Get the blank badge XML payload for a badge number
    XmlDocument badgeXml = 
        BadgeUpdateManager.GetTemplateContent(BadgeTemplateType.BadgeNumber);

    // Set the value of the badge in the XML to our number
    XmlElement badgeElement = badgeXml.SelectSingleNode("/badge") as XmlElement;
    badgeElement.SetAttribute("value", num.ToString());

    // Create the badge notification
    BadgeNotification badge = new BadgeNotification(badgeXml);

    // Create the badge updater for the application
    BadgeUpdater badgeUpdater = 
        BadgeUpdateManager.CreateBadgeUpdaterForApplication();

    // And update the badge
    badgeUpdater.Update(badge);

}
````

### <a name="create-a-glyph-badge"></a>Создание индикатора событий с глифом
````csharp
private void updateBadgeGlyph()
{
    string badgeGlyphValue = "alert";

    // Get the blank badge XML payload for a badge glyph
    XmlDocument badgeXml = 
        BadgeUpdateManager.GetTemplateContent(BadgeTemplateType.BadgeGlyph);

    // Set the value of the badge in the XML to our glyph value
    Windows.Data.Xml.Dom.XmlElement badgeElement = 
        badgeXml.SelectSingleNode("/badge") as Windows.Data.Xml.Dom.XmlElement;
    badgeElement.SetAttribute("value", badgeGlyphValue);

    // Create the badge notification
    BadgeNotification badge = new BadgeNotification(badgeXml);

    // Create the badge updater for the application
    BadgeUpdater badgeUpdater = 
        BadgeUpdateManager.CreateBadgeUpdaterForApplication();

    // And update the badge
    badgeUpdater.Update(badge);

}
````

### <a name="clear-a-badge"></a>Очистка индикатора событий

````csharp
private void clearBadge()
{
    BadgeUpdateManager.CreateBadgeUpdaterForApplication().Clear();
}
````

## <a name="get-the-sample-code"></a>Получить пример кода

* [Пример уведомлений](https://github.com/Microsoft/Windows-universal-samples/blob/master/Samples/Notifications)<br/> Демонстрирует, как создавать интерактивные плитки, отправлять обновления индикатора событий и отображать всплывающие уведомления. 

## <a name="related-articles"></a>Связанные статьи

* [Адаптивные и интерактивные всплывающие уведомления](adaptive-interactive-toasts.md)
* [Создание плиток](creating-tiles.md)
* [Создание адаптивных плиток](create-adaptive-tiles.md)
