---
title: Команда validate программы winget
description: Эта команда проверяет манифест для отправки программного обеспечения в репозиторий манифестов пакетов сообщества Майкрософт на сайте GitHub.
author: KevinLaMS
ms.author: kevinla
ms.date: 04/28/2020
ms.topic: article
ms.localizationpriority: medium
ms.openlocfilehash: a5772e144a4226be9fbb4a4949aaac1e3d4408e6
ms.sourcegitcommit: d0f479f1955881afb62c2af249db5d0b053b63e5
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/19/2020
ms.locfileid: "83824935"
---
# <a name="validate-command-winget"></a>Команда validate (winget)

[!INCLUDE [preview-note](../../includes/package-manager-preview.md)]

Команда **validate** программы [winget](index.md) проверяет [файл манифеста](../package/manifest.md) для отправки программного обеспечения в **репозиторий манифестов пакетов сообщества Майкрософт** на сайте GitHub. Манифест должен быть YAML-файлом, соответствующим [спецификации](https://github.com/microsoft/winget-pkgs/YamlSpec.md).

## <a name="usage"></a>Использование

`winget validate [--manifest] \<manifest>`

## <a name="arguments"></a>Аргументы

Доступные аргументы перечислены ниже.

| Аргумент  | Описание |
|--------------|-------------|
| **--manifest** |  Путь к манифесту, который необходимо проверить. |
| **-?, --help** |  Позволяет получит дополнительную справку по этой команде. |

## <a name="related-topics"></a>Связанные темы

* [Использование программы winget для установки приложений и управления ими](index.md)
* [Отправка пакетов в Диспетчер пакетов Windows](../package/index.md)
