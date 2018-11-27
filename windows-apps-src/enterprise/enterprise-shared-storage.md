---
ms.assetid: B48E21AB-0EA5-444B-8333-393DD8D1B76D
title: Общее корпоративное хранилище
description: Общее корпоративное хранилище определяет местоположение локальных данных для бизнес-приложений в целях совместного их использования.
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 006507d4665f5578310b8d3e31fb8f7fba4117a2
ms.sourcegitcommit: 681c70f964210ab49ac5d06357ae96505bb78741
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/26/2018
ms.locfileid: "7696994"
---
# <a name="enterprise-shared-storage"></a>Общее корпоративное хранилище

Общее хранилище состоит из двух местоположений, в которых приложения с ограниченной возможностью **enterpriseDeviceLockdown** и корпоративный сертификат имеют полный доступ на чтение и запись. Обратите внимание, что возможность **enterpriseDeviceLockdown** позволяет приложениям использовать API блокировки устройства и предоставляет им доступ к корпоративным папкам общего хранилища. Дополнительные сведения об интерфейсе API можно получить, ознакомившись с информацией о пространстве имен [**Windows.Embedded.DeviceLockdown**](http://go.microsoft.com/fwlink/?LinkId=699331).  

Эти местоположения заданы на локальном диске:
- \Data\SharedData\Enterprise\Persistent
- \Data\SharedData\Enterprise\Non-Persistent

## <a name="scenarios"></a>Сценарии

Общее корпоративное хранилище поддерживает следующие сценарии.

- Вы можете предоставить общий доступ к данным в рамках экземпляра приложения, между экземплярами одного приложения или даже между несколькими приложениями, если у них есть соответствующие возможности и сертификат.
- Вы можете сохранить данные на локальном жестком диске в папке \Data\SharedData\Enterprise\Persistent, которые сохранятся даже после перезагрузки устройства.
- Управление файлами, включая чтение, запись и удаление файлов на устройстве через службу управления мобильными устройствами (MDM). Дополнительные сведения о том, как использовать общее корпоративное хранилище через службу MDM см. в разделе [EnterpriseExtFileSystem CSP](http://go.microsoft.com/fwlink/?LinkId=699333).

## <a name="access-enterprise-shared-storage"></a>Доступ к общему корпоративному хранилищу

В следующем примере показано, как объявить возможность доступа к общему корпоративному хранилищу в манифесте пакета, а также как получить доступ к папкам общего хранилища с помощью класса Windows.Storage.StorageFolder.

Включите следующую возможность в манифест пакета приложения:

```xml
<Package
  xmlns="http://schemas.microsoft.com/appx/manifest/foundation/windows10"
  xmlns:mp="http://schemas.microsoft.com/appx/2014/phone/manifest"
  xmlns:uap="http://schemas.microsoft.com/appx/manifest/uap/windows10"
  xmlns:rescap="http://schemas.microsoft.com/appx/manifest/foundation/windows10/restrictedcapabilities"
  IgnorableNamespaces="uap mp rescap">

…

<Capabilities>
    <rescap:Capability Name="enterpriseDeviceLockdown"/>
</Capabilities>
```

Чтобы получить доступ к местоположению общих данных, ваше приложение должно использовать следующий код.

```csharp
using System;
using System.Collections.Generic;
using System.Diagnostics;
using Windows.Storage;

…

// Get the Enterprise Shared Storage folder.
var enterprisePersistentFolderRoot = @"C:\Data\SharedData\Enterprise\Persistent";

StorageFolder folder =
    await StorageFolder.GetFolderFromPathAsync(enterprisePersistentFolderRoot);

// Get the files in the folder.
IReadOnlyList<StorageFile> sortedItems =
    await folder.GetFilesAsync();

// Iterate over the results and print the list of files
// to the Visual Studio Output window.
foreach (StorageFile file in sortedItems)
    Debug.WriteLine(file.Name + ", " + file.DateCreated);
```

