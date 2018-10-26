---
author: payzer
title: Отключение масштабирования
description: Инструкции по отключению коэффициента масштабирования по умолчанию.
ms.author: wdg-dev-content
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp
ms.assetid: 6e68c1fc-a407-4c0b-b0f4-e445ccb72ff3
ms.localizationpriority: medium
ms.openlocfilehash: 82b42b25d3894a82e92af9a520ee5f951a5ba344
ms.sourcegitcommit: 6cc275f2151f78db40c11ace381ee2d35f0155f9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/26/2018
ms.locfileid: "5563523"
---
# <a name="how-to-turn-off-scaling"></a>Отключение масштабирования   
По умолчанию XAML-приложения масштабируются до 200%, а HTML-приложения — до 150%. Коэффициент масштабирования по умолчанию можно отключить. В результате приложение будет использовать фактические размеры устройства в пикселях (1910 x 1080 пикселей).   
   
## <a name="html"></a>HTML   
Вы можете отказаться от коэффициента масштабирования, воспользовавшись следующим фрагментом кода. 
   
```
var result = Windows.UI.ViewManagement.ApplicationViewScaling.trySetDisableLayoutScaling(true);
```

Также можно использовать подходящий для веб-страниц метод:   

```   
@media (max-height: 1080px) {   
    @-ms-viewport {   
        height: 1080px;   
    }   
}   
```

## <a name="xaml"></a>XAML
Вы можете отказаться от коэффициента масштабирования, воспользовавшись следующим фрагментом кода.   
   
```
bool result = Windows.UI.ViewManagement.ApplicationViewScaling.TrySetDisableLayoutScaling(true);
```
   
## <a name="directxc"></a>DirectX/C++   
Приложения на DirectX/C++ не масштабируются. Автоматическое масштабирование распространяется только на HTML- и XAML-приложения.  

## <a name="see-also"></a>См. также
- [Рекомендации для Xbox](tailoring-for-xbox.md)
- [Приложения UWP для Xbox One](index.md)
