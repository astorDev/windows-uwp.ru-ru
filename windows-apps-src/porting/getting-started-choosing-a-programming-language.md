---
author: mcleblanc
title: "Выбор языка программирования"
ms.assetid: 6CA46432-BF03-4B20-9187-565B3503B497
description: "Выбор языка программирования"
ms.author: markl
ms.date: 02/08/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, uwp
ms.openlocfilehash: b20066ffbd32984d20fdbdcc0915ef10332c6f12
ms.sourcegitcommit: 909d859a0f11981a8d1beac0da35f779786a6889
translationtype: HT
---
# <a name="getting-started-choosing-a-programming-language"></a>Начало работы: выбор языка программирования

\[ Обновлено для приложений UWP в Windows 10. Статьи о Windows 8.x, см. в [архиве](http://go.microsoft.com/fwlink/p/?linkid=619132) \]

## <a name="choosing-a-programming-language"></a>Выбор языка программирования

Прежде чем продолжить, необходимо узнать, какие языки программирования можно выбрать при создании приложений универсальной платформы Windows (UWP). В пошаговых руководствах этой статьи используется язык C#, однако для создания приложений UWP вы можете использовать один или несколько других языков программирования (см. [Языки, средства и платформы](https://msdn.microsoft.com/library/windows/apps/dn465799)).

Для разработки можно использовать языки C++, C#, Microsoft Visual Basic и JavaScript. JavaScript использует разметку HTML5 для макета пользовательского интерфейса, а другие языки для описания пользовательских интерфейсов используют разметку *XAML*.

Хотя в этой статье используется язык программирования C#, следует отметить, что остальные языки предоставляют уникальные преимущества, которыми вы, возможно, захотите воспользоваться. Например, если для вашего приложения первоочередной задачей является производительность (особенно это касается приложений, требовательных к графическим ресурсам), рекомендуем выбрать язык C++. Для разработчиков приложений на Visual Basic наилучшим образом подойдет Visual Basic версии Microsoft .NET. Веб-разработчики по достоинству оценят JavaScript с HTML5. См. также следующие разделы.

-   [Создание первого приложения Магазина Windows на C++](https://msdn.microsoft.com/library/windows/apps/hh974580)
-   [Создание первого приложения Магазина Windows на C# или Visual Basic](https://msdn.microsoft.com/library/windows/apps/hh974581)
-   [Создание первого приложения Магазина Windows на JavaScript](https://msdn.microsoft.com/library/windows/apps/br211385)
-   [Создание первого приложения Магазина Windows Phone на C# или Visual Basic](http://go.microsoft.com/fwlink/p/?LinkID=397877)
-   [WinJS в Windows Phone 8.1](http://go.microsoft.com/fwlink/p/?LinkID=397879)

**Примечание.** Для приложений, использующих трехмерную графику: стандарты OpenGL и OpenGL ES, по умолчанию не являются доступными для приложений UWP. Если вы не желаете переписывать код OpenGL ES в Microsoft DirectX, возможно, вам будет интересно узнать о проекте **Angle**. Angle — это текущий проект, предназначенный для преобразования OpenGL в DirectX путем перевода вызовов API OpenGL в вызовы API DirectX. Подробнее см. в следующих разделах:
-   [Angle](https://code.google.com/p/angleproject/)
-   [Создание первого приложения Магазина Windows с поддержкой DirectX](https://msdn.microsoft.com/library/windows/apps/br229580)
-   [Образцы приложений Магазина Windows, использующих DirectX](http://go.microsoft.com/fwlink/p/?LinkId=263603)
-   [Где находится пакет SDK для DirectX?](https://msdn.microsoft.com/library/windows/desktop/ee663275)

## <a name="giving-c-a-go"></a>Попробуйте C#

Как разработчик iOS, вы привыкли использовать Objective-C и Swift. Среди языков программирования Майкрософт наиболее близким к обеим является C#. Мы считаем, что для разработчиков наиболее простым в изучении и использовании является C#, поэтому для пошаговых руководств выбран именно этот язык. Подробнее о языке C#:

-   [Создание первого приложения Магазина Windows на C# или Visual Basic](https://msdn.microsoft.com/library/windows/apps/hh974581)
-   [Примеры приложений Магазина Windows, использующих C#](http://go.microsoft.com/fwlink/p/?LinkId=263453)
-   [Visual C#](http://go.microsoft.com/fwlink/p/?LinkId=263450)

Ниже приведен класс, написанный на языках Objective-C и C#. Вариант на Objective-C показан первым, а вариант на C#— вторым.

```obj-c
// Objective-C header: SampleClass.h.

#import <Foundation/Foundation.h>

@interface SampleClass : NSObject {
    BOOL localVariable;
}

@property (nonatomic) BOOL localVariable;

-(int) addThis: (int) firstNumber andThis: (int) secondNumber;

@end
```

```obj-c
// Objective-C implementation.

#import "SampleClass.h"

@implementation SampleClass

@synthesize localVariable = _localVariable;

- (id)init {
    self = [super init];
    if (self) {
        localVariable = true;
    }
    return self;
}

-(int) addThis: (int) firstNumber andThis: (int) secondNumber {
    return firstNumber + secondNumber;
}

@end
```

```obj-c
// Objective-C usage.

SampleClass *mySampleClass = [[SampleClass alloc] init];
mySampleClass.localVariable = false;
int result = [mySampleClass addThis:1 andThis:2];
```

Теперь для версии C#. Вы увидите, что аналогично Swift, для заголовка и реализации не используются отдельные файлы.

```csharp
// C# header and implementation.

using System;

namespace MyApp  // Defines this code' s scope.
{
    class SampleClass
    {
        private bool localVariable;

        public SampleClass() // Constructor.
        {
            localVariable = true;
        }

        public bool myLocalVariable // Property.
        {
            get
            {
                return localVariable;
            }
            set
            {
                localVariable = value; 
            }
        }

        public int AddTwoNumbers(int numberOne, int numberTwo)
        {
            return numberOne + numberTwo;
        }        
    }
}
```

```csharp
// C# usage.

SampleClass mySampleClass = new SampleClass();
mySampleClass.myLocalVariable = false;
int result = mySampleClass.AddTwoNumbers(1, 2);
```

C# — это простой язык для выбора. Он содержит много классов и платформ поддержки, составляющих .NET. Вы с радостью мгновенно приступите к написанию кода без квадратной скобки перед глазами!

## <a name="next-step"></a>Следующий шаг

[Приступая к работе: введение в Visual Studio](getting-started-getting-around-in-visual-studio.md)
