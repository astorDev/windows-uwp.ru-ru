---
title: Выбор языка программирования
ms.assetid: 6CA46432-BF03-4B20-9187-565B3503B497
description: Выбор языка программирования
ms.date: 02/08/2017
ms.topic: article
keywords: Windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 037c079881dbb2634b31cc0cf5b9248115dbceef
ms.sourcegitcommit: b52ddecccb9e68dbb71695af3078005a2eb78af1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74259153"
---
# <a name="getting-started-choosing-a-programming-language"></a>Приступая к работе: выбор языка программирования


## <a name="choosing-a-programming-language"></a>Выбор языка программирования

Прежде чем продолжить, необходимо узнать, какие языки программирования можно выбрать при создании приложений универсальной платформы Windows (UWP). В пошаговых руководствах этой статьи используется язык C#, однако для создания приложений UWP вы можете использовать один или несколько других языков программирования (см. [Языки, средства и платформы](https://docs.microsoft.com/previous-versions/windows/apps/dn465799(v=win.10))).

Для разработки можно использовать языки C++, C#, Microsoft Visual Basic и JavaScript. JavaScript использует разметку HTML5 для макета пользовательского интерфейса, а другие языки для описания пользовательских интерфейсов используют разметку *XAML*.

Хотя в этой статье используется язык программирования C#, следует отметить, что остальные языки предоставляют уникальные преимущества, которыми вы, возможно, захотите воспользоваться. Например, если для вашего приложения первоочередной задачей является производительность (особенно это касается приложений, требовательных к графическим ресурсам), рекомендуем выбрать язык C++. Для разработчиков приложений на Visual Basic наилучшим образом подойдет Visual Basic версии Microsoft .NET. Веб-разработчики по достоинству оценят JavaScript с HTML5. См. также следующие разделы.

-   [Создайте первое приложение UWP с помощьюC++](../get-started/create-a-basic-windows-10-app-in-cpp.md)
-   [Создайте первое приложение UWP с помощью C# или Visual Basic](../get-started/create-a-hello-world-app-xaml-universal.md)
-   [Создание первого приложения UWP с помощью JavaScript](../get-started/create-a-hello-world-app-js-uwp.md)

**Обратите внимание** ,  для приложений, использующих трехмерную графику, стандарты OpenGL и OpenGL ES изначально недоступны для приложений UWP. Если вы не желаете переписывать код OpenGL ES в Microsoft DirectX, возможно, вам будет интересно узнать о проекте **Angle**. Angle — это текущий проект, предназначенный для преобразования OpenGL в DirectX путем перевода вызовов API OpenGL в вызовы API DirectX. Подробнее см. в следующих разделах:
-   [Под](https://bugs.chromium.org/p/angleproject/)
-   [Создание первого приложения UWP с помощью DirectX](https://docs.microsoft.com/previous-versions/windows/apps/br229580(v=win.10))
-   [Примеры приложений UWP, использующих DirectX](https://code.msdn.microsoft.com/windowsapps/site/search?f%5B0%5D.Type=Technology&f%5B0%5D.Value=DirectX)
-   [Где находится пакет SDK для DirectX?](https://docs.microsoft.com/windows/desktop/directx-sdk--august-2009-)

## <a name="giving-c-a-go"></a>Попробуйте C#

Как разработчик iOS, вы привыкли использовать Objective-C и Swift. Среди языков программирования Майкрософт наиболее близким к обеим является C#. Мы считаем, что для разработчиков наиболее простым в изучении и использовании является C#, поэтому для пошаговых руководств выбран именно этот язык. Подробнее о языке C#:

-   [Создайте первое приложение UWP с помощью C# или Visual Basic](../get-started/create-a-hello-world-app-xaml-universal.md)
-   [Примеры приложений UWP, использующиеC#](https://code.msdn.microsoft.com/windowsapps/site/search?f%5B0%5D.Type=ProgrammingLanguage&f%5B0%5D.Value=C%23&f%5B0%5D.Text=C%23)
-   [ВнешнегоC#](https://msdn.microsoft.com/library/kx37x362.aspx)

Ниже приведен класс, написанный на языках Objective-C и C#. Вариант на Objective-C показан первым, а вариант на C# — вторым.

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

## <a name="next-step"></a>Дальнейшие действия

[Приступая к работе: обход в Visual Studio](getting-started-getting-around-in-visual-studio.md)
