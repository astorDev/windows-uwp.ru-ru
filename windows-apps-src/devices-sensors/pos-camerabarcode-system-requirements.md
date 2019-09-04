---
title: Системные требования к камерам, используемым в качестве сканера штрихкодов
description: В этой статье перечислены требования к камерам, используемым из приложения UWP в качестве сканера штрихкодов.
ms.date: 05/02/2018
ms.topic: article
keywords: Windows 10, UWP, точка обслуживания, POS
ms.localizationpriority: medium
ms.openlocfilehash: 4eed59e302bc34e93d21adef794de02427f2933e
ms.sourcegitcommit: 0dec04de501a3db6b22dfd4a320fc09b5c4a21b5
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/04/2019
ms.locfileid: "70243274"
---
# <a name="camera-barcode-scanner-system-requirements"></a>Системные требования к камерам, используемым в качестве сканера штрихкодов
Начиная с Windows 10 версии 1803 можно считывать штрихкоды через объектив стандартной камеры из универсального приложения для Windows.

## <a name="supported-windows-editions"></a>Поддерживаемые выпуски Windows
- Windows 10 Профессиональная в S-режиме
- Windows 10 Профессиональная
- Windows 10 Корпоративная
- Windows 10 IOT Базовая


## <a name="webcam-requirements"></a>Требования к веб-камере
| Category      | Рекомендации           | Комментарии |
| ------------- | ------------------------ | -------- |
| Фокус         | Автоматическая фокусировка               | Фиксированный фокус не рекомендуется |
| Разрешение    | 1920 x 1440 или выше    | Наилучшие результаты были получены на камерах, обеспечивающих разрешение 1920 x 1440 или выше.  Некоторые камеры с более низким разрешением способны считывать стандартные штрихкоды, если штрихкод напечатан достаточно крупно. Штрихкоды с более тонкими элементами могут требовать камер с более высоким разрешением. |
|

## <a name="see-also"></a>См. также

### <a name="samples"></a>Примеры

- [Пример сканера штрихкодов](https://github.com/microsoft/Windows-universal-samples/tree/master/Samples/BarcodeScanner)
