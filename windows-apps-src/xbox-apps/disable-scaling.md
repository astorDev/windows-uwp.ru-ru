---
author: payzer
title: "Отключение масштабирования"
description: 
area: Xbox
translationtype: Human Translation
ms.sourcegitcommit: 192de32bf3afd11cd375655ad92d194ccb09dae1
ms.openlocfilehash: 307606bc290e9c5268fc5a37b72770d6b1ada4da

---

# Отключение масштабирования   
По умолчанию XAML-приложения масштабируются до 200%, а HTML-приложения — до 150%. Коэффициент масштабирования по умолчанию можно отключить. В результате приложение будет использовать фактические размеры устройства в пикселях (1910 x 1080 пикселей).   
   
## HTML   
Вы можете отказаться от коэффициента масштабирования, воспользовавшись следующим фрагментом кода. 
   
`var result = Windows.UI.ViewManagement.ApplicationViewScaling.trySetDisableLayoutScaling(true);` 

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



<!--HONumber=Jul16_HO1-->


