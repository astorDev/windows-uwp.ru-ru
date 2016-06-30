---
author: payzer
title: "Отключение режима мыши"
description: 
area: Xbox
ms.sourcegitcommit: bdf7a32d2f0673ab6c176a775b805eff2b7cf437
ms.openlocfilehash: bd7780f105f86d7d292c5db2535ad40af07d6e4f

---

# Отключение режима мыши
Режим мыши по умолчанию включен для всех приложений. Это означает, что все приложения, которые не отключили этот режим, получат указатель мыши (подобно браузеру Edge на консоли). Настоятельно рекомендуется отключать эту функцию и выполнять оптимизацию для навигации с помощью направляемого контроллера.   
   
## HTML   
Для отключения режима мыши добавьте следующий код в файл JavaScript в своем приложении:   
   
```code
navigator.gamepadInputEmulation = "keyboard";
```   

## XAML    
Для отключения режима мыши добавьте следующий код в файл JavaScript в своем приложении:   
   
```code
public App() {
        this.InitializeComponent();
        this.RequiresPointerMode = Windows.UI.Xaml.ApplicationRequiresPointerMode.WhenRequested;
        this.Suspending += OnSuspending;
        }
```

## C++/DirectX   
Если вы пишете приложение на C++/DirectX, то ничего делать не надо. Режим мыши применяется только к приложениям на HTML и XAML.



<!--HONumber=Jun16_HO4-->


