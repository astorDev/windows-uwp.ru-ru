---
title: Выбор языка программирования
ms.assetid: 6CA46432-BF03-4B20-9187-565B3503B497
description: Выбор языка программирования
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 48cea8c1a3a3c35a53a3376c48e3192c94caf709
ms.sourcegitcommit: bf600a1fb5f7799961914f638061986d55f6ab12
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/05/2019
ms.locfileid: "9049291"
---
# <a name="getting-started-choosing-a-programming-language"></a>Начало работы: выбор языка программирования


## <a name="choosing-a-programming-language"></a>Выбор языка программирования

Прежде чем продолжить, необходимо узнать, какие языки программирования можно выбрать при создании приложений универсальной платформы Windows (UWP). В пошаговых руководствах этой статьи используется язык C#, однако для создания приложений UWP вы можете использовать один или несколько других языков программирования (см. [Языки, средства и платформы](https://msdn.microsoft.com/library/windows/apps/dn465799)).

Для разработки можно использовать языки C++, C#, Microsoft Visual Basic и JavaScript. JavaScript использует разметку HTML5 для макета пользовательского интерфейса, а другие языки для описания пользовательских интерфейсов используют разметку *XAML*.

Хотя в этой статье используется язык программирования C#, следует отметить, что остальные языки предоставляют уникальные преимущества, которыми вы, возможно, захотите воспользоваться. Например, если для вашего приложения первоочередной задачей является производительность (особенно это касается приложений, требовательных к графическим ресурсам), рекомендуем выбрать язык C++. Для разработчиков приложений на Visual Basic наилучшим образом подойдет Visual Basic версии Microsoft .NET. Веб-разработчики по достоинству оценят JavaScript с HTML5. См. также следующие разделы.

-   [Создание первого приложения UWP на C++](../get-started/create-a-basic-windows-10-app-in-cpp.md)
-   [Создание первого приложения UWP на C# или Visual Basic](../get-started/create-a-hello-world-app-xaml-universal.md)
-   [Создание первого приложения UWP на JavaScript](../get-started/create-a-hello-world-app-js-uwp.md)

**Примечание**для приложений, использующих трехмерную графику: стандарты OpenGL и OpenGL ES не являются доступными для приложений UWP. Если вы не желаете переписывать код OpenGL ES в Microsoft DirectX, возможно, вам будет интересно узнать о проекте **Angle**. Angle — это текущий проект, предназначенный для преобразования OpenGL в DirectX путем перевода вызовов API OpenGL в вызовы API DirectX. Подробнее см. в следующих разделах:
-   [Angle](https://code.google.com/p/angleproject/)
-   [Создание первого приложения UWP с использованием DirectX](https://msdn.microsoft.com/library/windows/apps/br229580)
-   [Примеры приложений UWP, использующие DirectX](https://go.microsoft.com/fwlink/p/?LinkId=263603)
-   [Где находится пакет SDK для DirectX?](https://msdn.microsoft.com/library/windows/desktop/ee663275)

## <a name="giving-c-a-go"></a>Попробуйте C#

Как разработчик iOS, вы привыкли использовать Objective-C и Swift. Среди языков программирования Майкрософт наиболее близким к обеим является C#. Мы считаем, что для разработчиков наиболее простым в изучении и использовании является C#, поэтому для пошаговых руководств выбран именно этот язык. Подробнее о языке C#:

-   [Создание первого приложения UWP на C# или Visual Basic](../get-started/create-a-hello-world-app-xaml-universal.md)
-   [Примеры приложений UWP, использующих C#](https://go.microsoft.com/fwlink/p/?LinkId=263453)
-   [Visual C#](https://go.microsoft.com/fwlink/p/?LinkId=263450)

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
