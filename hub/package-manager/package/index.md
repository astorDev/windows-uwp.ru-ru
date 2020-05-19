---
title: Отправка пакетов в Диспетчер пакетов Windows
description: ''
author: denelon
ms.author: denelon
ms.date: 04/29/2020
ms.topic: overview
ms.localizationpriority: medium
ms.openlocfilehash: e83088c5a6b2755a8ce7f08e513d09f877580db8
ms.sourcegitcommit: d0f479f1955881afb62c2af249db5d0b053b63e5
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/19/2020
ms.locfileid: "83580091"
---
# <a name="submit-packages-to-windows-package-manager"></a>Отправка пакетов в Диспетчер пакетов Windows

[!INCLUDE [preview-note](../../includes/package-manager-preview.md)]

Если вы независимый поставщик программного обеспечения (ISV), вы можете использовать Диспетчер пакетов Windows в качестве канала распространения пакетов программного обеспечения, содержащих ваши приложения. В настоящее время Диспетчер пакетов Windows поддерживает установщики в следующих форматах: MSIX, MSI и EXE.

Чтобы отправить пакеты программного обеспечения в Диспетчер пакетов Windows, выполните следующие действия.

1. [Создайте манифест пакета, который предоставляет сведения о приложении](manifest.md). Манифесты — это YAML-файлы, созданные в соответствии со схемой Диспетчера пакетов Windows.
2. [Отправьте манифест в репозиторий Диспетчера пакетов Windows](repository.md). Это репозиторий с открытым исходным кодом на сайте GitHub. Он содержит коллекцию манифестов, к которым может получить доступ программа **winget**.

## <a name="related-topics"></a>Связанные темы

* [Использование программы winget](../winget/index.md)
* [Создание манифеста пакета](manifest.md)
* [Отправка манифеста в репозиторий](repository.md)