---
title: Создание манифеста пакета
description: ''
author: denelon
ms.author: denelon
ms.date: 04/29/2020
ms.topic: article
ms.localizationpriority: medium
ms.openlocfilehash: 054c8cf7fc104b78f0397f4d1536e1130668f4f8
ms.sourcegitcommit: 645cb099128072cfc905ec80b38bd280b98c9037
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/22/2020
ms.locfileid: "83825145"
---
# <a name="create-your-package-manifest"></a>Создание манифеста пакета

[!INCLUDE [preview-note](../../includes/package-manager-preview.md)]

Если вы хотите отправить пакет программного обеспечения в [репозиторий Диспетчера пакетов Windows](repository.md), сначала создайте манифест пакета. Манифест — это YAML-файл, описывающий устанавливаемое приложение.

В этой статье описывается содержимое манифеста пакета для Диспетчера пакетов Windows.

## <a name="yaml-basics"></a>Основы YAML

Формат YAML был выбран для манифестов пакетов из-за его относительной простоты чтения для человека и согласованности с другими средствами разработки Майкрософт. Если вы не знакомы с синтаксисом YAML, вы можете быстро ознакомиться с основами в [этой статье](https://learnxinyminutes.com/docs/yaml/).

> [!NOTE]
> Манифесты для Диспетчера пакетов Windows в настоящее время поддерживают не все функции YAML. Среди неподдерживаемых функций YAML привязки, сложные ключи и наборы.

## <a name="conventions"></a>Соглашения

В этой статье используется следующий подход:

* Слева от `:` находится ключевое слово literal, используемое в определениях манифеста.
* Справа от `:` расположен тип данных. Тип данных может быть примитивным, например **string**, или ссылкой на обширную структуру, определенную в других местах этой статьи.
* Нотация `[` *тип_данных* `]` указывает на массив указанного типа данных. Например, `[ string ]` — это массив строк.
* Нотация `{` *тип_данных* `:` *тип_данных* `}` указывает на сопоставление одного типа данных с другим. Например, `{ string: string }` — это сопоставление двух строковых типов.

## <a name="manifest-contents"></a>Содержимое манифеста

Манифест пакета должен включать набор обязательных элементов. Он также может содержать дополнительные элементы, которые могут помочь улучшить взаимодействие с пользователем при установке программного обеспечения. В этом разделе содержатся краткие сводки по требуемой схеме манифеста и полные схемы манифестов с соответствующими примерами.

В каждом поле в файле манифеста должна использоваться нотация в стиле Pascal. При этом поля не должны дублироваться.

Полный список и описания элементов манифеста см. в спецификации манифеста в репозитории [https://github.com/microsoft/winget-cli](https://github.com/microsoft/winget-cli).

### <a name="minimal-required-schema"></a>Минимальная необходимая схема

#### <a name="minimal-required-schema"></a>[Минимальная необходимая схема](#tab/minschema/)

```yaml
Id: string # publisher.package format
Publisher: string # the name of the publisher
Name: string # the name of the application
Version: string # version numbering format
License: string # the open source license or copyright
InstallerType: string # enumeration of supported installer types (exe, msi, msix, inno, wix, nullsoft, appx)
Installers:
  - Arch: string # enumeration of supported architectures
  - URL: string # path to download installation file
  - Sha256: string # SHA256 calculated from installer
ManifestVersion: 0.1.0
```

#### <a name="example"></a>[Пример](#tab/minexample/)

```yaml
Id: microsoft.teams
Publisher: Microsoft Corporation
Name: Microsoft Teams
Version: 1.3.0.4461
License: Copyright (c) Microsoft Corporation. All rights reserved.
InstallerType: exe
Installers:
  - Arch: x64
    Url: https://statics.teams.cdn.office.net/production-windows-x64/1.3.00.4461/Teams_windows_x64.exe
    Sha256: 712f139d71e56bfb306e4a7b739b0e1109abb662dfa164192a5cfd6adb24a4e1
ManifestVersion: 0.1.0
```

* * *

### <a name="complete-schema"></a>Полная схема

#### <a name="complete-schema"></a>[Полная схема](#tab/compschema/)

```yaml
Id: string # publisher.package format
Publisher: string # the name of the publisher
Name: string # the name of the application
AppMoniker: string # the common name someone may use to search for the package
Version: string # version numbering format for package version
Channel: string # a string representing the flight ring
License: string # the open source license or copyright
LicenseUrl: string # valid secure URL to license
MinOSVersion: string # version numbering format for minimum version of Windows supported
Description: string # description of the package
Homepage: string # valid secure URL for the package
Tags: list # additional strings a user would use to search for the package
FileExtensions: list # list of file extensions the package could support
Protocols: list # list of protocols the package provides a handler for
Commands: list # list of commands or aliases the user would use to run the package
InstallerType: string # enumeration of supported installer types (exe, msi, msix)
Custom: string # custom switches passed to the installer
Silent: string # switches passed to the installer for silent installation
SilentWithProgress: string # switches passed to the installer for non-interactive install
Interactive: string # experimental
Language: string # experimental
Log: string # specifies log redirection switches and path
InstallLocation: string # specifies alternate location to install package
Installers: # nested map of keys for specific installer
  - Arch: string # enumeration of supported architectures
  - URL: string # path to download installation file
  - Sha256: string # SHA256 calculated from installer
  - SignatureSha256: string # SHA256 calculated from signature file's hash of MSIX file
  - Switches: # collection of entries to override root keys
  - Scope: string # experimental
  - SystemAppId: string # experimental
Localization: # nested map of keys for localization
  - Language: string # locale for display fields and localized URLs
ManifestVersion: string # version number format for manifest version
```

#### <a name="good-example"></a>[Пример правильной схемы](#tab/good/)

```yaml
Id: microsoft.teams
Publisher: Microsoft Corporation
Name: Microsoft Teams
Version: 1.3.0.4461
License: Copyright (c) Microsoft Corporation. All rights reserved.
LicenseUrl: https://docs.microsoft.com/en-us/MicrosoftTeams/assign-teams-licenses
InstallerType: exe
Installers:
  - Arch: x64
    Url: https://statics.teams.cdn.office.net/production-windows-x64/1.3.00.4461/Teams_windows_x64.exe
    Sha256: 712f139d71e56bfb306e4a7b739b0e1109abb662dfa164192a5cfd6adb24a4e1
ManifestVersion: 0.1.0
```

#### <a name="better-example"></a>[Пример улучшенной схемы](#tab/better/)

```yaml
Id: microsoft.teams
Publisher: Microsoft Corporation
Name: Microsoft Teams
Version: 1.3.0.4461
AppMoniker: teams
MinOSVersion: 10.0.0.0
Description: The hub for teamwork in Microsoft 365
Homepage: https://www.microsoft.com/microsoft/teams
License: Copyright (c) Microsoft Corporation. All rights reserved.
LicenseUrl: https://docs.microsoft.com/en-us/MicrosoftTeams/assign-teams-licenses
InstallerType: exe
Installers:
  - Arch: x64
    Url: https://statics.teams.cdn.office.net/production-windows-x64/1.3.00.4461/Teams_windows_x64.exe
    Sha256: 712f139d71e56bfb306e4a7b739b0e1109abb662dfa164192a5cfd6adb24a4e1
ManifestVersion: 0.1.0
```

* * *

## <a name="tips-and-best-practices"></a>Советы и рекомендации

* Для наилучшего взаимодействия с клиентами при поиске и установке программного обеспечения помимо обязательной схемы рекомендуется добавить как можно больше необязательных элементов. Например, поле `AppMoniker` является необязательным. Однако если его включить, при выполнении команды [search](../winget/search.md) клиенты увидят результаты, связанные со значением `AppMoniker` (например, **vscode** для **Visual Studio Code**). Если имеется только одно приложение с указанным значением `AppMoniker`, клиенты могут установить приложение, указав моникер вместо полного идентификатора.
* Значение `Id` должно быть уникальным. Нельзя отправлять приложение несколько раз с одинаковым идентификатором пакета. Старайтесь не использовать пробелы, так как из-за них пользователям придется заключать `Id` в кавычки при использовании клиента [winget](../index.md).
* Избегайте создания нескольких папок издателя. Например, не создавайте Contoso Ltd, если папка Contoso уже существует. Кроме того, при создании папок не следует использовать пробелы.
* По возможности отправляйте все пакеты в режиме автоматической установки. Если у вас есть исполняемый файл, который не поддерживает автоматическую установку, взаимодействие с пользователем осложнится.
* Ограничьте длину строк в манифесте до 100 символов до разрыва строки.
* Если для указанной версии пакета существует несколько типов установщика, под каждый раздел `Installers` можно поместить экземпляр `InstallerType`.
