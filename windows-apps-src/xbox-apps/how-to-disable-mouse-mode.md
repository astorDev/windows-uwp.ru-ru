---
author: payzer
title: Отключение режима мыши
description: Инструкции по отключению режима мыши по умолчанию.
ms.author: wdg-dev-content
ms.date: 02/08/2017
ms.topic: article
keywords: Windows 10, uwp
ms.assetid: e57ee4e6-7807-4943-a933-c2b4dc80fc01
ms.localizationpriority: medium
ms.openlocfilehash: 9cc6989103442deb364654ec006c0fe4b1455511
ms.sourcegitcommit: 70ab58b88d248de2332096b20dbd6a4643d137a4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/02/2018
ms.locfileid: "5937227"
---
# <a name="how-to-disable-mouse-mode"></a>Отключение режима мыши
Режим мыши включен по умолчанию для всех приложений; это означает, что все приложения, которые явно не отключили указатель мыши, получают его (аналогично указателю в браузере Edge на консоли). Настоятельно рекомендуется отключать эту функцию и выполнять оптимизацию для навигации с помощью направляемого контроллера.   
   
## <a name="html"></a>HTML   
Для включения навигации с помощью направляемого контроллера в приложении для универсальной платформы Windows (UWP) на языке JavaScript используйте библиотеку JavaScript [TVHelpers DirectionalNavigation](https://github.com/Microsoft/TVHelpers/wiki/Using-DirectionalNavigation). Включите JavaScript-файл для работы с направленной навигацией в пакет приложения и добавьте ссылку на этот файл во все HTML-страницы, на которых требуется использовать навигацию с помощью направляемого контроллера.

```code
<script src="directionalnavigation-1.0.0.0.js"></script>
```
Подробнее см. в [вики-статье, посвященной направленной навигации](https://github.com/Microsoft/TVHelpers/wiki/Using-DirectionalNavigation).

Если вместо этого вы хотите отключить режим мыши и использовать модель DOM или API-интерфейсы WinRT для игрового контроллера напрямую, выполните следующие действия для всех страниц, на которых требуется это сделать. 
   
```code
navigator.gamepadInputEmulation = "gamepad";
```   

   По умолчанию это свойство имеет значение `mouse`, которое включает режим мыши. Если для свойства установить значение `keyboard`, то режим мыши будет отключен и вместо него при получении входных сигналов с игрового контроллера будут генерироваться события клавиатуры модели DOM. Если для свойства установить значение `gamepad`, то режим мыши будет отключен, события клавиатуры модели DOM генерироваться не будут, и вы сможете просто использовать модель DOM или API-интерфейсы WinRT для игрового контроллера.

## <a name="xaml"></a>XAML    
Чтобы отключить режим мыши, добавьте следующий код в конструктор для вашего приложения.   
   
```code
public App() {
        this.InitializeComponent();
        this.RequiresPointerMode = Windows.UI.Xaml.ApplicationRequiresPointerMode.WhenRequested;
        this.Suspending += OnSuspending;
}
```

## <a name="cdirectx"></a>C++/DirectX   
Если вы пишете приложение на C++/DirectX, то ничего делать не нужно. Режим мыши применяется только к приложениям на HTML и XAML.

## <a name="see-also"></a>См. также
- [Рекомендации для Xbox](tailoring-for-xbox.md)
- [Приложения UWP для Xbox One](index.md)

