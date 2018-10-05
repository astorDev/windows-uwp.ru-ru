---
author: andrewleader
Description: This article describes how to send a local tile notification to a primary tile and a secondary tile using adaptive tile templates.
title: Отправка локального уведомления на плитке
ms.assetid: D34B0514-AEC6-4C41-B318-F0985B51AF8A
template: detail.hbs
ms.author: mijacobs
ms.date: 05/19/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 7e91d4bd481188f4d29af68af2c4572b26d446ae
ms.sourcegitcommit: 63cef0a7805f1594984da4d4ff2f76894f12d942
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/05/2018
ms.locfileid: "4384800"
---
# <a name="send-a-local-tile-notification"></a>Отправка локального уведомления на плитке
 

Основные плитки приложений в Windows 10 определяются в манифесте приложения, а вспомогательные плитки создаются программно и определяются кодом приложения. В этой статье описывается, как с помощью шаблонов адаптивных плиток отправлять локальное уведомление на плитке на основную и вспомогательную плитку. (Локальное уведомление — это уведомление, отправляемое из кода приложения, тогда как уведомление другого типа отправляется принудительно (push-уведомление) или поступает с веб-сервера).

![стандартная плитка и плитка с уведомлением](images/sending-local-tile-01.png)

> [!NOTE] 
>Узнайте о [создании адаптивных плиток](create-adaptive-tiles.md) и [схеме содержимого плиток](../tiles-and-notifications/tile-schema.md).

 

## <a name="install-the-nuget-package"></a>Установка пакета NuGet


Мы рекомендуем устанавливать [пакет библиотеки уведомлений NuGet](https://www.nuget.org/packages/Microsoft.Toolkit.Uwp.Notifications/), который упрощает процесс за счет создания полезных данных плитки с объектами вместо необработанных XML-данных.

Примеры встроенного кода в этой статье предназначены для C# с использованием библиотеки уведомлений. (Описание процесса создания собственного кода XML с его примерами без библиотеки уведомлений см. в конце статьи).

## <a name="add-namespace-declarations"></a>Добавление объявлений пространств имен


Чтобы получить доступ к API для работы с плитками, добавьте пространство имен [**Windows.UI.Notifications**](https://docs.microsoft.com/uwp/api/Windows.UI.Notifications). Мы также рекомендуем добавлять пространство имен **Microsoft.Toolkit.Uwp.Notifications**, чтобы можно было использовать вспомогательные API для работы с плитками (чтобы получить доступ к этим API, необходимо установить пакет NuGet [Библиотеки уведомлений](https://www.nuget.org/packages/Microsoft.Toolkit.Uwp.Notifications/)).

```csharp
using Windows.UI.Notifications;
using Microsoft.Toolkit.Uwp.Notifications; // Notifications library
```

## <a name="create-the-notification-content"></a>Создание содержимого уведомления


В Windows 10 полезные данные плитки определяются с помощью шаблонов адаптивных плиток, которые позволяют создавать собственные визуальные макеты для уведомлений. (Дополнительные сведения об операциях, которые можно выполнять с адаптивными плитками, см. в статье [Создание адаптивных плиток](create-adaptive-tiles.md)).

В этом примере кода создается содержимое адаптивной плитки для средних и широких плиток.

```csharp
// In a real app, these would be initialized with actual data
string from = "Jennifer Parker";
string subject = "Photos from our trip";
string body = "Check out these awesome photos I took while in New Zealand!";
 
 
// Construct the tile content
TileContent content = new TileContent()
{
    Visual = new TileVisual()
    {
        TileMedium = new TileBinding()
        {
            Content = new TileBindingContentAdaptive()
            {
                Children =
                {
                    new AdaptiveText()
                    {
                        Text = from
                    },

                    new AdaptiveText()
                    {
                        Text = subject,
                        HintStyle = AdaptiveTextStyle.CaptionSubtle
                    },

                    new AdaptiveText()
                    {
                        Text = body,
                        HintStyle = AdaptiveTextStyle.CaptionSubtle
                    }
                }
            }
        },

        TileWide = new TileBinding()
        {
            Content = new TileBindingContentAdaptive()
            {
                Children =
                {
                    new AdaptiveText()
                    {
                        Text = from,
                        HintStyle = AdaptiveTextStyle.Subtitle
                    },

                    new AdaptiveText()
                    {
                        Text = subject,
                        HintStyle = AdaptiveTextStyle.CaptionSubtle
                    },

                    new AdaptiveText()
                    {
                        Text = body,
                        HintStyle = AdaptiveTextStyle.CaptionSubtle
                    }
                }
            }
        }
    }
};
```

При отображении на средней плитке содержимое уведомления выглядит следующим образом:

![содержимое уведомления на средней плитке](images/sending-local-tile-02.png)

## <a name="create-the-notification"></a>Создание уведомления на плитке


После создания содержимого уведомления необходимо создать новое уведомление [**TileNotification**](https://docs.microsoft.com/uwp/api/Windows.UI.Notifications.TileNotification). Конструктор **TileNotification** принимает объект [**XmlDocument**](https://docs.microsoft.com/uwp/api/windows.data.xml.dom.xmldocument) среды выполнения Windows, который можно получить из метода **TileContent.GetXml** с использованием [Библиотеки уведомлений](https://www.nuget.org/packages/Microsoft.Toolkit.Uwp.Notifications/).

В этом примере кода создается уведомление для новой плитки.

```csharp
// Create the tile notification
var notification = new TileNotification(content.GetXml());
```

## <a name="set-an-expiration-time-for-the-notification-optional"></a>Задание времени окончания срока действия уведомления (необязательно)


По умолчанию локальные уведомления на плитках и индикаторах событий не истекают, тогда как запланированные, периодические и push-уведомления истекают через три дня. Так как содержание плитки не должно сохраняться дольше, чем нужно, рекомендуется установить срок действия на все уведомления на плитках и индикаторах событий, используя время, значимое для вашего приложения.

В этом примере кода создается уведомление, срок действия которого истекает через десять минут, после чего оно удаляется из плитки.

```csharp
tileNotification.ExpirationTime = DateTimeOffset.UtcNow.AddMinutes(10);
```

## <a name="send-the-notification"></a>Отправка уведомления


Несмотря на то, что локальная отправка уведомления плитки является простым процессом, отправка уведомления на основную или вспомогательную плитку выполняется несколько иначе.

**Основная плитка**

Чтобы отправить уведомление на основную плитку, используйте [**TileUpdateManager**](https://docs.microsoft.com/uwp/api/Windows.UI.Notifications.TileUpdateManager) для создания средства обновления основной плитки и отправьте уведомление, вызвав метод «Update». Независимо от того, отображается основная плитка приложения или нет, она всегда существует, таким образом на нее можно отправлять уведомления, даже если она не закреплена. Когда пользователь закрепит основную плитку, тогда и отобразятся отправленные вами уведомления.

В этом примере кода уведомление отправляется на основную плитку.


```csharp
// Send the notification to the primary tile
TileUpdateManager.CreateTileUpdaterForApplication().Update(notification);
```

**Вспомогательная плитка**

Чтобы отправить уведомление на вспомогательную плитку, сначала убедитесь, что вспомогательная плитка существует. Если создать средство обновления плитки для вспомогательной плитки, которой не существует (например, если пользователь открепил вспомогательную плитку), будет выдано исключение. Вы можете использовать [**SecondaryTile.Exists**](https://docs.microsoft.com/uwp/api/Windows.UI.StartScreen.SecondaryTile#Windows_UI_StartScreen_SecondaryTile_Exists_System_String_)(tileId), чтобы определить, закреплена ли вспомогательная плитка, а затем создать средство обновления плитки для вспомогательной плитки и отправить уведомление.

В этом примере кода уведомление отправляется на вспомогательную плитку.

```csharp
// If the secondary tile is pinned
if (SecondaryTile.Exists("MySecondaryTile"))
{
    // Get its updater
    var updater = TileUpdateManager.CreateTileUpdaterForSecondaryTile("MySecondaryTile");
 
    // And send the notification
    updater.Update(notification);
}
```

![стандартная плитка и плитка с уведомлением](images/sending-local-tile-01.png)

## <a name="clear-notifications-on-the-tile-optional"></a>Очистка уведомлений на плитке (необязательно)


В большинстве случаев после взаимодействия пользователя с этим содержимым уведомление необходимо очистить. Например, когда пользователь запускает ваше приложение, может потребоваться удалить все уведомления с плитки. Если уведомления зависят от времени, рекомендуется задать время окончания срока действия уведомления, а не выполнять непосредственно очистку уведомления.

В этом примере кода выполняется очистка уведомления плитки для основной плитки. То же самое можно сделать для дополнительных плиток, создав для них средство обновления плитки.

```csharp
TileUpdateManager.CreateTileUpdaterForApplication().Clear();
```

Если для плитки включена очередь уведомлений, в которой имеются элементы, вызов метода Clear приводит к очистке очереди. Тем не менее уведомление нельзя очистить с помощью сервера приложений; уведомления может очищать только локальный код приложения.

Периодические или push-уведомления могут только добавлять новые уведомления или заменять существующие уведомления. Локальный вызов метода Clear очистит плитку независимо от типа уведомлений (push-уведомления, периодические или локальные уведомления). Запланированные уведомления, которые еще не появлялись, не очищаются с помощью этого метода.

![плитка с уведомлением и плитка после ее очищения](images/sending-local-tile-03.png)

## <a name="next-steps"></a>Следующие этапы


**Использование очереди уведомлений**

Теперь, когда вы выполнили первое обновление плитки, вы можете расширить функциональность своей плитки, включив [очередь уведомлений](https://msdn.microsoft.com/library/windows/apps/xaml/hh868234).

**Другие методы доставки уведомлений**

В этой статье описывается процесс отправки обновления плитки в виде уведомления. Чтобы изучить другие методы доставки уведомлений, включая запланированные, периодические и push-уведомления, см. раздел [Доставка уведомлений](choosing-a-notification-delivery-method.md).

**Метод доставки XmlEncode**

Если вы не используете [Библиотеку уведомлений](https://www.nuget.org/packages/Microsoft.Toolkit.Uwp.Notifications/), этот метод доставки уведомлений является альтернативным решением.


```csharp
public string XmlEncode(string text)
{
    StringBuilder builder = new StringBuilder();
    using (var writer = XmlWriter.Create(builder))
    {
        writer.WriteString(text);
    }

    return builder.ToString();
}
```

## <a name="code-examples-without-notifications-library"></a>Примеры кода без библиотеки уведомлений


Если вы предпочитаете работать с необработанным кодом XML, а не с пакетом NuGet [Библиотеки уведомлений](https://www.nuget.org/packages/Microsoft.Toolkit.Uwp.Notifications/), вместо первых трех примеров кода, приведенных в этой статье, используйте следующие альтернативные примеры кода. Остальные примеры кода можно использовать с [Библиотекой уведомлений](https://www.nuget.org/packages/Microsoft.Toolkit.Uwp.Notifications/) или с необработанным кодом XML.

Добавление объявлений пространств имен

```csharp
using Windows.UI.Notifications;
using Windows.Data.Xml.Dom;
```

Создание содержимого уведомления

```csharp
// In a real app, these would be initialized with actual data
string from = "Jennifer Parker";
string subject = "Photos from our trip";
string body = "Check out these awesome photos I took while in New Zealand!";
 
 
// TODO - all values need to be XML escaped
 
 
// Construct the tile content as a string
string content = $@"
<tile>
    <visual>
 
        <binding template='TileMedium'>
            <text>{from}</text>
            <text hint-style='captionSubtle'>{subject}</text>
            <text hint-style='captionSubtle'>{body}</text>
        </binding>
 
        <binding template='TileWide'>
            <text hint-style='subtitle'>{from}</text>
            <text hint-style='captionSubtle'>{subject}</text>
            <text hint-style='captionSubtle'>{body}</text>
        </binding>
 
    </visual>
</tile>";
```

Создание уведомления на плитке

```csharp
// Load the string into an XmlDocument
XmlDocument doc = new XmlDocument();
doc.LoadXml(content);
 
// Then create the tile notification
var notification = new TileNotification(doc);
```

## <a name="related-topics"></a>Ссылки по теме

* [Создание адаптивных плиток](create-adaptive-tiles.md)
* [Схема содержимого плитки](../tiles-and-notifications/tile-schema.md)
* [Библиотека уведомлений](https://www.nuget.org/packages/Microsoft.Toolkit.Uwp.Notifications/)
* [Полный пример кода на GitHub](https://github.com/WindowsNotifications/quickstart-sending-local-tile-win10)
* [**Пространство имен Windows.UI.Notifications**](https://docs.microsoft.com/uwp/api/Windows.UI.Notifications)
* [Использование очереди уведомлений (XAML)](https://msdn.microsoft.com/library/windows/apps/xaml/hh868234)
* [Доставка уведомлений](choosing-a-notification-delivery-method.md)
 

 




