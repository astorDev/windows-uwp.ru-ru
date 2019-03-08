---
title: Добавьте в проект XDK Xbox Live
description: Сведения о добавлении Xbox Live в новый или существующий проект Xbox Developer Kit (XDK).
ms.assetid: fc6f987c-1a87-4ff5-b063-891591aa6653
ms.date: 04/04/2017
ms.topic: article
keywords: Xbox live, xbox, игры, универсальной платформы Windows, windows 10, xbox, один, xdk
ms.localizationpriority: medium
ms.openlocfilehash: f17765b09dcb0b6f5c89d168f2d3d9611a60ffa6
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57649049"
---
# <a name="add-xbox-live-to-a-new-or-existing-xdk-project"></a>Добавьте в проект новый или существующий XDK Xbox Live

В этом разделе описывается, как добавить в проект новый или существующий XDK Xbox Live.

Процесс происходит.

- Настройка среды разработки Xbox One
- Получить ваши идентификаторы
- Настройка консоли разработки
- Добавьте TitleID и SCID в двоичный файл


## <a name="setup-up-your-xbox-one-development-environment"></a>Настройка среды разработки Xbox One
Во-первых настройте консоль с следующем разделе «Параметр Настройка Xbox одной среды разработки» в документации по XDK

## <a name="get-your-ids"></a>Получить ваши идентификаторы

Чтобы включить службы Xbox Live, необходимо получить несколько идентификаторов, чтобы настроить пакет SDK и название. Это можно сделать с помощью тот же процесс.

Будет получать ваши идентификаторы, выполнив процесс [конфигурации службы Xbox Live](../xbox-live-service-configuration.md)

## <a name="configure-your-development-console"></a>Настройка консоли разработки

Получив ваш идентификаторов, выполните [настроить консоль разработки](configure-your-development-console.md) руководство по настройке вашей консоли разработки.

## <a name="add-the-titleid-and-scid-to-your-binary"></a>Добавьте TitleID и SCID в двоичный файл
При «песочницы» настроена на уровне платформы для каждого пакета средств разработки, TitleID и SCID привязаны к конкретного двоичного файла. Чтобы добавить TitleID и SCID двоичный файл, измените Package.appxmanifest для этого двоичного файла, добавив новый узел в <Extensions> , как показано ниже:

```
<Applications>
    ...
    <Application ...>
      ...
      <Extensions>
        <mx:Extension Category="xbox.live">
           <mx:XboxLive TitleId="<your titleID>" PrimaryServiceConfigId="<your SCID>" RequireXboxLive="<boolean indicating Live requirement>" />
        </mx:Extension>
      </Extensions>
   </Application>
</Applications>
```

Дополнительные сведения о файл AppxManifest.xml см шаблоны проектов в Visual Studio для разработки приложений Xbox для одного.

См. в разделе манифеста схема приложения описание приложения схеме манифеста.

**Флаг RequireXboxLive** Если RequireXboxLive, флаг имеет значение true, заголовок не будет запущен, если уровень Windows.Networking.Connectivity соединения возвращает в виде XboxLiveAccess и заголовок очищает проверки подлинности с помощью Xbox Live. Это гарантирует, что заголовок будет принято последние обновления содержимого. Если во время работы заголовок потеряно подключение, заголовок будет приостановлена.

Только заголовки, «Интернет Required» должна пометить RequireXboxLive как true и обратите внимание, что пометки название таким образом не гарантирует, что необходимые службы для заголовка не будут, а также выполнение.
