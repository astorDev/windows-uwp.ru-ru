---
title: Команда hash программы winget
description: Создает хэш SHA256 для установщика.
author: KevinLaMS
ms.author: kevinla
ms.date: 04/28/2020
ms.topic: article
ms.localizationpriority: medium
ms.openlocfilehash: 2f7b2074a9d6f2a01fe5a74f7f081ceeedcccec9
ms.sourcegitcommit: d0f479f1955881afb62c2af249db5d0b053b63e5
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/19/2020
ms.locfileid: "83824955"
---
# <a name="hash-command-winget"></a>Команда hash (winget)

[!INCLUDE [preview-note](../../includes/package-manager-preview.md)]

Команда **hash** программы [winget](index.md) создает хэш SHA256 для установщика. Эта команда используется, если необходимо создать [файл манифеста](../package/manifest.md) для отправки программного обеспечения в **репозиторий манифестов пакетов сообщества Майкрософт** на сайте GitHub. Кроме того, команда **hash** также поддерживает создание хэша сертификата SHA256 для MSIX-файлов.

## <a name="usage"></a>Использование

`winget hash [-f] \<file> [\<options>]`

Вложенная команда **hash** может выполняться только для локального файла. Чтобы использовать вложенную команду **hash**, скачайте установщик в известное расположение. Затем передайте путь к файлу во вложенную команду **hash** в качестве аргумента.

## <a name="arguments"></a>Аргументы

Доступны следующие аргументы.

| Аргумент  | Описание |
|--------------|-------------|
| **-f,--file** |  Путь к файлу для хэширования. |
| **-m,--msix**  | Указывает, что команда hash также создаст SignatureSha256 хеша SHA 256 для использования с установщиками MSIX. |
| **-?, --help** |  Позволяет получить дополнительную справку по этой команде. |

## <a name="related-topics"></a>Связанные темы

* [Использование программы winget для установки приложений и управления ими](index.md)
* [Отправка пакетов в Диспетчер пакетов Windows](../package/index.md)
