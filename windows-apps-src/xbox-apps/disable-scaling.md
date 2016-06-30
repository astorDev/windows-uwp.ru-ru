---
author: payzer
title: "Отключение масштабирования"
description: 
area: Xbox
ms.sourcegitcommit: 2fcccb9a045aad268afde615d31f8faa002b8a87
ms.openlocfilehash: 65416dd2b6c8656078b63c316f3972cda9c792fc

---

# Отключение масштабирования   
По умолчанию XAML-приложения масштабируются до 200%, а HTML-приложения — до 150%. Коэффициент масштабирования по умолчанию можно отключить. В результате приложение будет использовать фактические размеры устройства в пикселях (1910 x 1080 пикселей).   
   
## HTML   
Вы можете отказаться от коэффициента масштабирования, воспользовавшись следующим фрагментом кода. 
   
`bool result = Windows.UI.ViewManagement.ApplicationViewScaling.TrySetDisableLayoutScaling(true);` 

Также можно использовать подходящий для веб-страниц метод:   

```   
@media (max-height: 1080px) {   
    @-ms-viewport {   
        height: 1080px;   
    }   
}   
```

## XAML
Вы можете отказаться от коэффициента масштабирования, воспользовавшись следующим фрагментом кода.   
   
`bool result = Windows.UI.ViewManagement.ApplicationViewScaling.TrySetDisableLayoutScaling(true);`   
   
## DirectX/C++   
Приложения на DirectX/C++ не масштабируются. Автоматическое масштабирование распространяется только на HTML- и XAML-приложения.   



<!--HONumber=Jun16_HO4-->


