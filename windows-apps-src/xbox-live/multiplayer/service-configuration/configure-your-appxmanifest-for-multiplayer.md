---
title: Настройка AppXManifest вашей для многопользовательскую
description: Узнайте, как настройка AppXManifest вашей UWP для включения Xbox Live многопользовательские приглашения.
ms.assetid: 72f179e7-4705-4161-9b8a-4d6a1a05b8f7
ms.date: 04/04/2017
ms.topic: article
keywords: Xbox live, xbox, игры, универсальной платформы Windows, windows 10, xbox, один, протокол активации, многопользовательскую
ms.localizationpriority: medium
ms.openlocfilehash: 13b04a86fdc4e4f661dd1c181dda7d9c9e4c1c8a
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57646029"
---
# <a name="configure-your-appxmanifest-for-multiplayer"></a>Настройка AppXManifest вашей для многопользовательскую

Необходимо внести некоторые обновления для appxmanifest-файл в проект Visual Studio, если выполняются следующие условия:
- При разработке UWP
- Вы хотите реализовать возможность игроки смогут приглашать других пользователей для заголовка

Если не выполнить этот шаг, название не будет активировано, когда получателей проигрыватель принимает приглашение для воспроизведения протокола.

## <a name="open-your-packageappxmanifest"></a>Откройте ваш файл Package.appxmanifest

Файл Package.appxmanifest обычно находится в том же каталоге, что файл решения проекта Visual Studio.  Или его можно найти в обозревателе решений.

![](../../images/multiplayer/multiplayer_open_appxmanifest.png)

## <a name="add-new-entry"></a>Добавьте новую запись

Необходимо добавить следующую команду, чтобы ```<Extensions>``` элемента под ```<Applications>``` в файле Package.appxmanifest

```
<Extensions>
  <uap:Extension Category="windows.protocol">
    <uap:Protocol Name="ms-xbl-multiplayer" />
  </uap:Extension>
</Extensions>
```

Например:

![](../../images/multiplayer/multiplayer_appxmanifest_changes.png)

Сохраните и перестройте название.  Узнайте, как использовать диспетчер многопользовательскую реализовать возможность приглашать игроков название, см. в разделе [многопользовательскую играть с друзьями](../multiplayer-manager/play-multiplayer-with-friends.md)
