---
author: jwmsft
description: В настоящем разделе разъясняются методики реализации присоединенного свойства XAML в качестве свойства зависимостей и определения соглашения о методе доступа, необходимого для использования присоединенного свойства в языке XAML.
title: Пользовательские присоединенные свойства
ms.assetid: E9C0C57E-6098-4875-AA3E-9D7B36E160E0
ms.author: jimwalk
ms.date: 07/18/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
dev_langs:
- csharp
- vb
- cppwinrt
- cpp
ms.openlocfilehash: de2d319f536b2c9d25d53e0b1ec2886831c66e12
ms.sourcegitcommit: 4d88adfaf544a3dab05f4660e2f59bbe60311c00
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/13/2018
ms.locfileid: "6459542"
---
# <a name="custom-attached-properties"></a>Пользовательские присоединенные свойства

*Присоединенное свойство* является концепцией языка XAML. Как правило, присоединенные свойства определяются как специализированная форма свойства зависимостей. В настоящем разделе разъясняются методики реализации присоединенного свойства как свойства зависимостей и определения соглашения о методе доступа, необходимого для использования вашего присоединенного свойства в языке XAML.

## <a name="prerequisites"></a>Необходимые условия

Мы предполагаем, что вы понимаете свойства зависимостей с позиции потребителя существующих свойств зависимостей, а также знакомы с разделом [Общая информация о свойствах зависимостей](dependency-properties-overview.md). Вам также следует предварительно ознакомиться с разделом [Общая информация о присоединенных свойствах](attached-properties-overview.md). Чтобы читать примеры в этом разделе, необходимо также разбираться в языке XAML и знать, как написать простое приложение среды выполнения Windows на C++, C# или Visual Basic.

## <a name="scenarios-for-attached-properties"></a>Сценарии для присоединенных свойств

Вы можете создавать присоединенное свойство в случае необходимости организации механизма настройки свойств для классов, отличных от определяющего класса. В этой части наиболее распространенными сценариями являются представление и поддержка служб. Примерами существующих свойств представления могут служить [**Canvas.ZIndex**](https://msdn.microsoft.com/library/windows/apps/hh759773) и [**Canvas.Top**](https://msdn.microsoft.com/library/windows/apps/hh759772). В сценарии представления элементы, существующие в качестве дочерних по отношению к элементам управления представлением, могут выражать требования представления к соответствующим родительским элементам отдельно, когда каждый элемент устанавливает одно значение свойства, которое соответствующий родитель определяет как присоединенное свойство. Примером сценария поддержки служб в API среды выполнения Windows является набор присоединенных свойств [**ScrollViewer**](https://msdn.microsoft.com/library/windows/apps/br209527), например [**ScrollViewer.IsZoomChainingEnabled**](https://msdn.microsoft.com/library/windows/apps/br209561).

> [!WARNING]
> Существующее ограничение реализации XAML среды выполнения Windows состоит в том, что вы не можете анимировать пользовательского присоединенного свойства.

## <a name="registering-a-custom-attached-property"></a>Регистрация пользовательского присоединенного свойства

Если вы определяете присоединенное свойство строго для использования на других типах, класс, в котором регистрируется данное свойство, необязательно должен наследовать от [**DependencyObject**](https://msdn.microsoft.com/library/windows/apps/br242356). Однако обязателен целевой параметр для использования **DependencyObject** методом доступа в случае отработки типовой модели, в которой ваше присоединенное свойство является также свойством зависимостей, что позволяет использовать резервное хранилище свойств.

Определите свое присоединенное свойство как свойство зависимостей, объявив свойство **public** **static** **readonly** типа [**DependencyProperty**](https://msdn.microsoft.com/library/windows/apps/br242362). Данное свойство определяется при помощи возвращаемого значения метода [**RegisterAttached**](https://msdn.microsoft.com/library/windows/apps/hh701833). Имя данного свойства должно соответствовать имени присоединенного свойства, указанному вами в качестве параметра **RegisterAttached** *name*, с добавлением строки Property в конце. Таково установленное соглашение для присвоения имен идентификаторам свойств зависимостей в отношении свойств, которые они представляют.

Главной областью, в которой определение пользовательского присоединенного свойства отличается от пользовательского свойства зависимостей, является способ определения методов доступа или программ-оболочек. Вместо использования методики программы-оболочки, описанной в [пользовательские свойства зависимостей](custom-dependency-properties.md), необходимо также предоставить статические **получить *** PropertyName* и **задать *** PropertyName* методы, как методы доступа для присоединенного свойства. Данные методы доступа используются в основном средством синтаксического анализа языка XAML, хотя для задания значений в сценариях без применения языка XAML их может использовать и любой другой абонент.

> [!IMPORTANT]
> Если вы не указываете методы доступа определены неправильно, процессор XAML не может получить доступ к вашего присоединенного свойства, и любой пользователь, который пытается использовать его, скорее всего, будут получать к ошибке средства синтаксического анализа XAML. Кроме того, инструменты проектирования и программирования зачастую используют соглашения \*Property для присвоения имен идентификаторам при обнаружении пользовательских свойств зависимостей в сборке, на которую выполняется ссылка.

## <a name="accessors"></a>Методы доступа

Подпись для метода доступа **Get**_PropertyName_ должна быть следующей.

`public static` _valueType_ **Get**_PropertyName_ `(DependencyObject target)`

Для Microsoft Visual Basic.

`Public Shared Function Get`_PropertyName_`(ByVal target As DependencyObject) As `_valueType_`)`

Объект *target* может иметь более конкретный тип в вашей реализации, однако он должен наследовать от [**DependencyObject**](https://msdn.microsoft.com/library/windows/apps/br242356). Возвращаемое значение *valueType* также может иметь более конкретный тип в вашей реализации. Базовый тип **Object** приемлем, но часто требуется усилить безопасность типа с помощью своего присоединенного свойства. Рекомендованной методикой обеспечения безопасности типа является использование ввода набором в подписях методов getter и setter.

Подпись для **задать *** PropertyName* доступа должна быть следующей.

`public static void Set`_PropertyName_` (DependencyObject target , `_valueType_` value)`

Для Visual Basic.

`Public Shared Sub Set`_PropertyName_` (ByVal target As DependencyObject, ByVal value As `_valueType_`)`

Объект *target* может иметь более конкретный тип в вашей реализации, однако он должен наследовать от [**DependencyObject**](https://msdn.microsoft.com/library/windows/apps/br242356). Объект *value* и его *valueType* могут иметь более конкретный тип в вашей реализации. Помните, что для данного метода значением является ввод, поступающий от процессора XAML при обнаружении вашего присоединенного свойства в разметке. Должны иметь место преобразование типа или поддержка расширения существующей разметки для используемого вами типа, чтобы необходимый тип мог создаваться из значения атрибута (являющегося, в конечном итоге, не более чем строкой). Базовый тип **Object** допускается, но часто требуется усилить безопасность типа. Для этого поместите проверку типа в методы доступа.

> [!NOTE]
> Можно также определить присоединенное свойство, в котором предназначено для использования в синтаксисе элементов свойства. В этом случае не требуется преобразование типов для значений, однако нужно обеспечить возможность создания запланированных значений в XAML. [**VisualStateManager.VisualStateGroups**](https://msdn.microsoft.com/library/windows/apps/hh738505)— это пример существующего присоединенного свойства, которое поддерживает только использование элементов свойства.

## <a name="code-example"></a>Пример кода

Данный пример иллюстрирует регистрацию свойства зависимостей (при помощи метода [**RegisterAttached**](https://msdn.microsoft.com/library/windows/apps/hh701833)), а также использование методов доступа **Get** и **Set** для пользовательского присоединенного свойства. В приведенном примере именем присоединенного свойства является `IsMovable`. Таким образом, методам доступа должны присваиваться имена `GetIsMovable` и `SetIsMovable`. Владельцем присоединенного свойства является класс службы `GameService`, не имеющий собственного пользовательского интерфейса. Он только предоставляет службы присоединенных свойств, когда используется присоединенное свойство **GameService.IsMovable**.

Процедура определения присоединенного свойства в C + +/ CX немного сложнее. Необходимо определить отношения между заголовочным файлом и файлом кода. Кроме того, идентификатор следует представить как свойство с единственным методом доступа **get** по причинам, изложенным в разделе [Пользовательские свойства зависимостей](custom-dependency-properties.md). В C + +/ CX, необходимо определить эти отношения свойство поле явно вместо того чтобы полагаться на скрытое резервирование **readonly** .NET и неявного резервирования простых свойств. Также необходимо выполнить регистрацию присоединенного свойства во вспомогательной функции, которая выполняется только один раз, при первом запуске приложения, но до загрузки XAML-страниц, которым требуется присоединенное свойство. Вспомогательные функции, регистрирующие свойства, для всех свойств зависимостей и присоединенных свойств обычно вызываются из конструктора **App** / [**Application**](https://msdn.microsoft.com/library/windows/apps/br242325) в коде файла app.xaml.

```csharp
public class GameService : DependencyObject
{
    public static readonly DependencyProperty IsMovableProperty = 
    DependencyProperty.RegisterAttached(
      "IsMovable",
      typeof(Boolean),
      typeof(GameService),
      new PropertyMetadata(false)
    );
    public static void SetIsMovable(UIElement element, Boolean value)
    {
        element.SetValue(IsMovableProperty, value);
    }
    public static Boolean GetIsMovable(UIElement element)
    {
        return (Boolean)element.GetValue(IsMovableProperty);
    }
}
```

```vb
Public Class GameService
    Inherits DependencyObject

    Public Shared ReadOnly IsMovableProperty As DependencyProperty = 
        DependencyProperty.RegisterAttached("IsMovable",  
        GetType(Boolean), 
        GetType(GameService), 
        New PropertyMetadata(False))

    Public Shared Sub SetIsMovable(ByRef element As UIElement, value As Boolean)
        element.SetValue(IsMovableProperty, value)
    End Sub

    Public Shared Function GetIsMovable(ByRef element As UIElement) As Boolean
        GetIsMovable = CBool(element.GetValue(IsMovableProperty))
    End Function
End Class
```

```cppwinrt
// GameService.idl
namespace UserAndCustomControls
{
    runtimeclass GameService : Windows.UI.Xaml.DependencyObject
    {
        GameService();
        static Windows.UI.Xaml.DependencyProperty IsMovableProperty{ get; };
        Boolean IsMovable;
    }
}

// GameService.h
...
    bool IsMovable(){ return winrt::unbox_value<bool>(GetValue(m_IsMovableProperty)); }
    void IsMovable(bool value){ SetValue(m_IsMovableProperty, winrt::box_value(value)); }
    Windows::UI::Xaml::DependencyProperty IsMovableProperty(){ return m_IsMovableProperty; }

private:
    static Windows::UI::Xaml::DependencyProperty m_IsMovableProperty;
...

// GameService.cpp
...
Windows::UI::Xaml::DependencyProperty GameService::m_IsMovableProperty =
    Windows::UI::Xaml::DependencyProperty::RegisterAttached(
        L"IsMovable",
        winrt::xaml_typename<bool>(),
        winrt::xaml_typename<UserAndCustomControls::GameService>(),
        Windows::UI::Xaml::PropertyMetadata{ winrt::box_value(false) }
);
...
```

```cpp
// GameService.h
#pragma once

#include "pch.h"
//namespace WUX = Windows::UI::Xaml;

namespace UserAndCustomControls {
    public ref class GameService sealed : public WUX::DependencyObject {
    private:
        static WUX::DependencyProperty^ _IsMovableProperty;
    public:
        GameService::GameService();
        void GameService::RegisterDependencyProperties();
        static property WUX::DependencyProperty^ IsMovableProperty
        {
            WUX::DependencyProperty^ get() {
                return _IsMovableProperty;
            }
        };
        static bool GameService::GetIsMovable(WUX::UIElement^ element) {
            return (bool)element->GetValue(_IsMovableProperty);
        };
        static void GameService::SetIsMovable(WUX::UIElement^ element, bool value) {
            element->SetValue(_IsMovableProperty,value);
        }
    };
}

// GameService.cpp
#include "pch.h"
#include "GameService.h"

using namespace UserAndCustomControls;

using namespace Platform;
using namespace Windows::Foundation;
using namespace Windows::Foundation::Collections;
using namespace Windows::UI::Xaml;
using namespace Windows::UI::Xaml::Controls;
using namespace Windows::UI::Xaml::Data;
using namespace Windows::UI::Xaml::Documents;
using namespace Windows::UI::Xaml::Input;
using namespace Windows::UI::Xaml::Interop;
using namespace Windows::UI::Xaml::Media;

GameService::GameService() {};

GameService::RegisterDependencyProperties() {
    DependencyProperty^ GameService::_IsMovableProperty = DependencyProperty::RegisterAttached(
         "IsMovable", Platform::Boolean::typeid, GameService::typeid, ref new PropertyMetadata(false));
}
```

## <a name="using-your-custom-attached-property-in-xaml"></a>Применение пользовательского присоединенного свойства в языке XAML

После определения присоединенного свойства и включения членов его поддержки как составляющей настраиваемого типа необходимо выполнить определения, доступные для использования XAML. Для этого следует сопоставить пространство имен XAML, которое будет ссылаться на пространство имен кода, содержащее соответствующий класс. При определении присоединенного свойства как части библиотеки необходимо включить данную библиотеку как часть пакета приложения для данного приложения.

Сопоставление пространства имен XML для языка XAML помещается, как правило, в корневой элемент страницы XAML. Например, для класса с именем `GameService` в пространстве имен `UserAndCustomControls`, содержащем определения присоединенного свойства, которые показаны в предыдущих фрагментах кода, данное сопоставление может выглядеть следующим образом.

```xaml
<UserControl
  xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
  xmlns:uc="using:UserAndCustomControls"
  ... >
```

Используя такое сопоставление, вы можете установить свое присоединенное свойство `GameService.IsMovable` на любой элемент, соответствующий целевому определению, включая существующий тип, определяемый средой выполнения Windows.

```xaml
<Image uc:GameService.IsMovable="True" .../>
```

В случае установки вашего свойства на элемент, включенный в то же сопоставленное пространство имен XML, вы также должны включить данный префикс в имя присоединенного свойства. Причина этого заключается в том, что префикс квалифицирует тип владельца. Атрибут присоединенного свойства не может планово располагаться как элемент в том же пространстве имен XML, куда включается данный атрибут, несмотря на то, что по обычным правилам для XML атрибуты могут наследовать пространство имен элементов. Например, в случае установки `GameService.IsMovable` на настраиваемый тип `ImageWithLabelControl` (определение не показано), даже если они оба определены в одном пространстве имен кода, сопоставленном с одним префиксом, XAML, тем не менее, будет иметь следующий вид.

```xaml
<uc:ImageWithLabelControl uc:GameService.IsMovable="True" .../>
```

> [!NOTE]
> Если вы создаете пользовательский Интерфейс на XAML с помощью C++, необходимо включить заголовок для настраиваемого типа, определяющий присоединенное свойство, в любое время, на странице XAML использует этот тип. Каждая страница XAML имеет сопоставленный заголовок поддерживающего кода .xaml.h. Именно в него следует включить (с помощью **\#include**) заголовок для определения типа владельца присоединенного свойства.

## <a name="value-type-of-a-custom-attached-property"></a>Тип значения пользовательского присоединенного свойства

Тип, используемый в качестве типа значения пользовательского присоединенного свойства, влияет на его использование, определение, либо и на то, и на другое. Тип значения присоединенного свойства объявляется в нескольких местах: в подписях методов доступа **Get** и **Set**, а также в качестве параметра *propertyType* вызова [**RegisterAttached**](https://msdn.microsoft.com/library/windows/apps/hh701833).

Наиболее распространенным типом значения для присоединенных свойств (пользовательских или иных) является простая строка. Причиной этого является то, что присоединенные свойства, как правило, предназначены для использования атрибутами языка XAML, а использование строки в качестве типа значения способствует облегченному представлению этих свойств. Другие примитивы, имеющие собственные методы преобразования в строку, например целое число, число двойной точности или значение перечисления, также часто используются в качестве типов значения для присоединенных свойств. Вы можете использовать и другие типы значения, не поддерживающие собственное преобразование в строку, в качестве значения присоединенного свойства. Однако это влечет за собой необходимость выбора использования либо реализации следующего:

- Вы можете оставить присоединенное свойство без изменений, однако данное присоединенное свойство сможет поддерживать использование только там, где оно является элементом свойства, а его значение объявлено как элемент объекта. В этом случае тип свойства однозначно должен поддерживать использование языка XAML как элемент объекта. Для существующих ссылочных классов среды выполнения Windows выполните проверку синтаксиса XAML, чтобы убедиться, что данный тип поддерживает использование элемента объекта языка XAML.
- Можно оставить присоединенное свойство без изменений, но использовать его следует только в применении атрибута, следуя методике ссылок языка XAML, например **Binding** или **StaticResource**, которые могут выражаться строкой.

## <a name="more-about-the-canvasleft-example"></a>Подробнее о примере **Canvas.Left**

В более ранних примерах использования присоединенного свойства мы показали различные способы задания значения присоединенного свойства [**Canvas.Left**](https://msdn.microsoft.com/library/windows/apps/hh759771). Но что это меняет во взаимодействии [**Canvas**](https://msdn.microsoft.com/library/windows/apps/br209267) с вашим объектом и когда это происходит? Мы рассмотрим этот пример ниже, поскольку, если вы реализуете присоединенное свойство, интересно посмотреть, что еще типичный класс— владелец присоединенного свойства будет делать со значениями своего присоединенного свойства, если он обнаружит их на других объектах.

Основная функция [**Canvas**](https://msdn.microsoft.com/library/windows/apps/br209267)— контейнер макета с абсолютным позиционированием в пользовательском интерфейсе. Дочерние элементы **Canvas** хранятся в свойстве [**Children**](https://msdn.microsoft.com/library/windows/apps/br227514), определенном в базовом классе. Из всех панелей **Canvas** является единственной, использующей абсолютное позиционирование. Добавление свойств, которые могут иметь отношение только к **Canvas**, и тех частных случаев, где элементы **UIElement** являются дочерними элементами элемента **UIElement**, приведет только к раздуванию объектной модели общего элемента [**UIElement**](https://msdn.microsoft.com/library/windows/apps/br208911). Определение свойств элемента управления макета **Canvas** в виде присоединенных свойств, которыми может воспользоваться любой элемент **UIElement**, позволяет поддерживать более четкую объектную модель.

Для практического применения в качестве панели класс [**Canvas**](https://msdn.microsoft.com/library/windows/apps/br209267) переопределяет методы [**Measure**](https://msdn.microsoft.com/library/windows/apps/br208952) и [**Arrange**](https://msdn.microsoft.com/library/windows/apps/br208914) уровня платформы. Именно здесь **Canvas** проверяет значения присоединенных свойств своих дочерних элементов. Частично шаблоны обоих методов **Measure** и **Arrange** представляют собой цикл, производящий итерации на любом содержимом, а у панели есть свойство [**Children**](https://msdn.microsoft.com/library/windows/apps/br227514), явно демонстрирующее дочерние элементы панели. Таким образом, поведение макета **Canvas** перебирает все дочерние элементы и выполняет статические вызовы методов [**Canvas.GetLeft**](https://msdn.microsoft.com/library/windows/apps/br209269) и [**Canvas.GetTop**](https://msdn.microsoft.com/library/windows/apps/br209270) для каждого дочернего элемента, чтобы убедиться в том, что присоединенные свойства содержат ненулевые значения (значение по умолчанию— 0). Затем эти значения используются для абсолютного позиционирования каждого дочернего элемента в доступном пространстве макета **Canvas** в соответствии со значениями, предоставляемыми каждым дочерним элементом, и фиксируются с помощью метода **Arrange**.

Код выглядит приблизительно следующим образом.

```syntax
protected override Size ArrangeOverride(Size finalSize)
{
    foreach (UIElement child in Children)
    {
        double x = (double) Canvas.GetLeft(child);
        double y = (double) Canvas.GetTop(child);
        child.Arrange(new Rect(new Point(x, y), child.DesiredSize));
    }
    return base.ArrangeOverride(finalSize); 
    // real Canvas has more sophisticated sizing
}
```

> [!NOTE]
> Дополнительные сведения о работе панелей см. [Обзор пользовательских панелей XAML](https://msdn.microsoft.com/library/windows/apps/mt228351).

## <a name="related-topics"></a>Ссылки по теме

* [**RegisterAttached**](https://msdn.microsoft.com/library/windows/apps/hh701833)
* [Общие сведения о присоединенных свойствах](attached-properties-overview.md)
* [Пользовательские свойства зависимостей](custom-dependency-properties.md)
* [Обзор языка XAML](xaml-overview.md)
