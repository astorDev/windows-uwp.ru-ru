---
description: Узнайте, как определять и реализовывать пользовательские свойства зависимостей в приложении среды выполнения Windows на C++, C# или Visual Basic.
title: Пользовательские свойства зависимостей
ms.assetid: 5ADF7935-F2CF-4BB6-B1A5-F535C2ED8EF8
ms.date: 07/12/2018
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
dev_langs:
- csharp
- vb
- cppwinrt
- cpp
ms.openlocfilehash: eadb0d1547387789fc202b833294e761f2faf22c
ms.sourcegitcommit: eb24481869d19704dd7bcf34e5d9f6a9be912670
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/17/2020
ms.locfileid: "79448591"
---
# <a name="custom-dependency-properties"></a>Пользовательские свойства зависимостей

В этом разделе показано, как определять и реализовывать собственные свойства зависимостей для приложения среды выполнения Windows на C++, C# или Visual Basic. Перечислены причины, по которым разработчики приложений и авторы компонентов могут пожелать создавать пользовательские свойства зависимостей. Также описаны действия по реализации пользовательского свойства зависимостей и приведены некоторые рекомендации по повышению производительности, удобства или гибкости свойства зависимостей.

## <a name="prerequisites"></a>Предварительные требования

Мы предполагаем, что вы ознакомились с разделом [Общая информация о свойствах зависимостей](dependency-properties-overview.md) и понимаете свойства зависимостей с точки зрения потребителя существующих свойств зависимостей. Чтобы читать примеры в этом разделе, необходимо также разбираться в языке XAML и знать, как написать простое приложение среды выполнения Windows на C++, C# или Visual Basic.

## <a name="what-is-a-dependency-property"></a>Что такое свойство зависимостей?

Чтобы поддерживать стили, привязку данных, анимацию и значения по умолчанию для свойства, оно должно быть реализовано как свойство зависимостей. Значения свойства зависимостей не хранятся как поля класса, они хранятся в платформе XAML; для ссылки на них используется ключ, который получается при регистрации свойства в системе свойств среды выполнения Windows путем вызова метода [**DependencyProperty.Register**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.dependencyproperty.register).   Свойства зависимостей могут использоваться только типами, производными от [**DependencyObject**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.DependencyObject). Но **DependencyObject** находится довольно высоко в иерархии классов, так что большинство классов, предназначенных для поддержки пользовательского интерфейса и презентаций, могут поддерживать свойства зависимостей. Подробнее о свойствах зависимостей и некоторых терминов и правил их описания в этой документации см. в разделе [Общие сведения о свойствах зависимостей](dependency-properties-overview.md).

Примерами свойств зависимостей в среде выполнения Windows помимо прочих являются [**Control.Background**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.control.background), [**FrameworkElement.Width**](/uwp/api/Windows.UI.Xaml.FrameworkElement.Width) и [**TextBox.Text**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.textbox.text).

В соответствии с соглашением, каждое свойство зависимостей, предоставляемое классом, имеет соответствующее свойство **public static readonly** типа [**DependencyProperty**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.DependencyProperty), которое предоставляется этим же классом и обеспечивает идентификатор для свойства зависимостей. Имя идентификатора формируется следующим образом: имя свойства зависимостей, со строкой «Property», добавленной к концу имени. Например, идентификатором **DependencyProperty**, соответствующим свойству **Control.Background**, является [**Control.BackgroundProperty**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.control.backgroundproperty). Идентификатор сохраняет информацию о свойстве зависимостей, как оно было зарегистрировано, после чего идентификатор можно использовать для других операций, в которые вовлечено свойство зависимостей, например для вызова [**SetValue**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.dependencyobject.setvalue).

## <a name="property-wrappers"></a>Оболочки свойств

Свойства зависимостей обычно имеют реализацию в виде оболочки. Без оболочки единственным способом получения или задания свойств будет использование служебных методов свойств зависимостей [**GetValue**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.dependencyobject.getvalue) и [**SetValue**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.dependencyobject.setvalue) и передача им идентификатора в качестве параметра. Это весьма противоестественный способ использования того, что предположительно является свойством. Но при использовании программы-оболочки ваш код и любой иной код, ссылающийся на свойство зависимостей, может использовать прямолинейный синтаксис объект-свойство, который естественен для используемого языка.

В случае самостоятельной реализации свойства зависимостей, которое должно быть общедоступным и простым для вызова, надо определить и программы-оболочки свойства. Программы-оболочки свойства также полезны для сообщения основной информации о свойстве зависимостей для процессов отражения или статического анализа. А именно, в оболочке мы размещаем атрибуты вроде [**ContentPropertyAttribute**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Markup.ContentPropertyAttribute).

## <a name="when-to-implement-a-property-as-a-dependency-property"></a>Ситуации, когда стоит реализовывать свойство как свойство зависимостей

Если вы реализуете общедоступное свойство чтения/записи в классе и класс является производным [**DependencyObject**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.DependencyObject), свойство можно сделать свойством зависимостей. Иногда достаточно типовой методики резервирования свойства с закрытым полем. Определять свое свойство как свойство зависимостей не всегда необходимо или разумно. Выбор должен зависеть от сценариев, для поддержки которых предназначено свойство.

Реализацию свойства в качестве свойства зависимостей стоит рассмотреть, если оно должно поддерживать одну или несколько следующих функций среды выполнения Windows или приложений среды выполнения Windows.

- Задание свойства через [**Style**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Style)
- Выполнение функции допустимого целевого свойства для привязки данных с [ **{Binding}** ](binding-markup-extension.md)
- Поддержка анимированных значений посредством [**Storyboard**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.Animation.Storyboard)
- Сообщение об изменении значения свойства:
  - действиями самой системы свойств;
  - средой;
  - действиями пользователя;
  - чтением и записью стилей.

## <a name="checklist-for-defining-a-dependency-property"></a>Контрольный список для определения свойства зависимостей

Определение свойства зависимостей можно рассматривать как набор концепций. Эти концепции не обязательно являются этапами процедуры, поскольку в реализации нескольким из них может соответствовать одна строка кода. Данный список предоставляет лишь краткий обзор. Ниже в этом разделе мы расскажем о каждой из концепций подробнее, иллюстрируя их примерами кода на нескольких языках.

- Зарегистрируйте имя свойства в системе свойств (путем вызова функции [**Register**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.dependencyproperty.register)), указав тип владельца и тип значения свойства.
  - Существует обязательный параметр функции [**Register**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.dependencyproperty.register), который принимает метаданные свойства. Укажите для него значение **null** или, если требуется поведение при изменении свойства или значение по умолчанию на основе метаданных, которое можно восстановить вызовом метода [**ClearValue**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.dependencyobject.clearvalue), укажите экземпляр [**PropertyMetadata**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.propertymetadata).
- Определите идентификатор [**DependencyProperty**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.DependencyProperty) как член свойства **public static readonly** в типе владельца.
- Определите свойство-оболочку, следуя модели метода доступа к свойству, применяемой в используемом языке. Имя свойства-оболочки должно совпадать со строкой *name*, используемой в [**Register**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.dependencyproperty.register). Реализуйте методы доступа **get** и **set** для соединения оболочки с заключенным в нее свойством зависимостей, вызывая [**GetValue**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.dependencyobject.getvalue) и [**SetValue**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.dependencyobject.setvalue) и передавая в качестве параметра идентификатор вашего свойства.
- (Необязательно) Разместите в оболочке такие атрибуты как [**ContentPropertyAttribute**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Markup.ContentPropertyAttribute).

> [!NOTE]
> Если вы определяете пользовательское присоединенное свойство, то оболочка обычно опускается. Вместо нее вы создаете метод доступа в другом стиле, который может использоваться обработчиком XAML. См. раздел [Пользовательские присоединенные свойства](custom-attached-properties.md). 

## <a name="registering-the-property"></a>Регистрация свойства

Чтобы свойство стало свойством зависимостей, его необходимо зарегистрировать в хранилище свойств системы свойств среды выполнения Windows.  Чтобы зарегистрировать свойство, вызовите метод [**Register**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.dependencyproperty.register).

Для языков Microsoft .NET (C# и Microsoft Visual Basic) мы вызываем [**Register**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.dependencyproperty.register) внутри тела класса (внутри класса, но вне любых определений членов). Идентификатор предоставляется как возвращаемое значение метода [**Register**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.dependencyproperty.register). Вызов метода [**Register**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.dependencyproperty.register) обычно выполняется в виде статического конструктора или в процессе инициализации свойства **public static readonly** типа [**DependencyProperty**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.DependencyProperty) в рамках вашего класса. Это свойство предоставляет идентификатор для свойства зависимостей. Вот примеры вызова [**Register**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.dependencyproperty.register).

> [!NOTE]
> Регистрация свойства зависимостей как части определения свойства идентификатора является обычной реализацией, но можно также зарегистрировать свойство зависимостей в статическом конструкторе класса. Такой подход может иметь смысл, если для инициализации свойства зависимостей необходимо более одной строки кода.

Для C++кода/CX можно использовать параметры разделения реализации между заголовком и файлом кода. Обычным вариантом является объявление самого идентификатора как свойства **public static** в заголовке, с реализацией **get**, но без **set**. Реализация **get** ссылается на закрытое поле, которым является неинициализированный экземпляр [**DependencyProperty**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.DependencyProperty). Также можно объявить оболочки и реализации **get** и **set** для оболочки. В этом случае заголовок включает некоторую минимальную реализацию. Если оболочке нужно определение объекта среды выполнения Windows, выполните его также и в заголовке. Поместите вызов функции [**Register**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.dependencyproperty.register) в файл кода во вспомогательную функцию, которая выполняется только в момент первой инициализации приложения. Используйте возвращаемое значение **Register** для заполнения статических, но еще не инициализированных идентификаторов, объявленных в заголовочном файле, которым первоначально было присвоено значение **nullptr** в корневой области файла реализации.

```csharp
public static readonly DependencyProperty LabelProperty = DependencyProperty.Register(
  "Label",
  typeof(String),
  typeof(ImageWithLabelControl),
  new PropertyMetadata(null)
);
```

```vb
Public Shared ReadOnly LabelProperty As DependencyProperty = 
    DependencyProperty.Register("Label", 
      GetType(String), 
      GetType(ImageWithLabelControl), 
      New PropertyMetadata(Nothing))
```

```cppwinrt
// ImageWithLabelControl.idl
namespace ImageWithLabelControlApp
{
    runtimeclass ImageWithLabelControl : Windows.UI.Xaml.Controls.Control
    {
        ImageWithLabelControl();
        static Windows.UI.Xaml.DependencyProperty LabelProperty{ get; };
        String Label;
    }
}

// ImageWithLabelControl.h
...
private:
    static Windows::UI::Xaml::DependencyProperty m_labelProperty;
...

// ImageWithLabelControl.cpp
...
Windows::UI::Xaml::DependencyProperty ImageWithLabelControl::m_labelProperty =
    Windows::UI::Xaml::DependencyProperty::Register(
        L"Label",
        winrt::xaml_typename<winrt::hstring>(),
        winrt::xaml_typename<ImageWithLabelControlApp::ImageWithLabelControl>(),
        Windows::UI::Xaml::PropertyMetadata{ nullptr }
);
...
```

```cpp
//.h file
//using namespace Windows::UI::Xaml::Controls;
//using namespace Windows::UI::Xaml::Interop;
//using namespace Windows::UI::Xaml;
//using namespace Platform;

public ref class ImageWithLabelControl sealed : public Control
{
private:
    static DependencyProperty^ _LabelProperty;
...
public:
    static void RegisterDependencyProperties();
    static property DependencyProperty^ LabelProperty
    {
        DependencyProperty^ get() {return _LabelProperty;}
    }
...
};

//.cpp file
using namespace Windows::UI::Xaml;
using namespace Windows::UI::Xaml.Interop;

DependencyProperty^ ImageWithLabelControl::_LabelProperty = nullptr;

// This function is called from the App constructor in App.xaml.cpp
// to register the properties
void ImageWithLabelControl::RegisterDependencyProperties()
{ 
    if (_LabelProperty == nullptr)
    { 
        _LabelProperty = DependencyProperty::Register(
          "Label", Platform::String::typeid, ImageWithLabelControl::typeid, nullptr);
    } 
}
```

> [!NOTE]
> Для кода C++/CX причина, по которой у вас есть закрытое поле и открытое свойство только для чтения, которое служит для указания [**DependencyProperty**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.DependencyProperty) , так что другие вызывающие объекты, использующие свойство зависимостей, также могут использовать API-интерфейсы служебной программы, требующие, чтобы идентификатор был открытым. Если оставить идентификатор закрытым, то другие пользователи не смогут использовать служебные API. Примеры таких API и сценариев включают [**GetValue**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.dependencyobject.getvalue) или [**SetValue**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.dependencyobject.setvalue) (по выбору), [**ClearValue**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.dependencyobject.clearvalue), [**GetAnimationBaseValue**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.dependencyobject.getanimationbasevalue), [**SetBinding**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.frameworkelement.setbinding) и [**Setter.Property**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.setter.property). Для этого невозможно использовать открытое поле, поскольку правила метаданных среды выполнения Windows не допускают открытые поля.

## <a name="dependency-property-name-conventions"></a>Соглашения об именовании свойств зависимостей

Для свойств зависимостей существуют соглашения об именовании; следуйте им, если не возникает каких-либо исключительных обстоятельств. У самого свойства зависимостей имеется простое имя («Label» в предыдущем примере), которое задается как первый параметр функции [**Register**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.dependencyproperty.register). Это имя должно быть уникально внутри каждого регистрирующего типа, и это требование уникальности также относится к любым унаследованным членам. Свойства зависимостей, унаследованные через базовые типы, уже считаются частью регистрирующего типа; имена унаследованных свойств нельзя зарегистрировать снова.

> [!WARNING]
> Хотя здесь имя может быть любым строковым идентификатором, допустимым в программировании для выбранного языка, обычно требуется возможность задать свойство зависимости в XAML тоже. Для задания в XAML выбранное имя свойства должно быть допустимым именем XAML. Подробнее см. в разделе [Обзор XAML](xaml-overview.md).

При создании свойства-идентификатора соедините имя свойства в том виде, в котором оно было зарегистрировано, с суффиксом «Property» (например, «LabelProperty»). Данное свойство является идентификатором для свойства зависимостей и используется в качестве входных данных для вызовов [**SetValue**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.dependencyobject.setvalue) и [**GetValue**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.dependencyobject.getvalue), выполняемых в ваших оболочках свойств. Оно также используется системой свойств и другими обработчиками XAML, такими как [ **{x:Bind}** ](x-bind-markup-extension.md)

## <a name="implementing-the-wrapper"></a>Реализация оболочки

Программе-оболочке свойства следует вызывать [**GetValue**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.dependencyobject.getvalue) в реализации **get** и [**SetValue**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.dependencyobject.setvalue) в реализации **set**.

> [!WARNING]
> Во всех, но исключительных обстоятельствах реализация оболочек должна выполнять только операции [**GetValue**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.dependencyobject.getvalue) и [**SetValue**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.dependencyobject.setvalue) . В ином случае поведение при задании свойства через разметку XAML и при его задании через код будет различным. Для эффективности средство синтаксического анализа XAML обходит программы-оболочки при установке свойств зависимостей; оно обменивается данными с резервным хранилищем через **SetValue**.

```csharp
public String Label
{
    get { return (String)GetValue(LabelProperty); }
    set { SetValue(LabelProperty, value); }
}
```

```vb
Public Property Label() As String
    Get
        Return DirectCast(GetValue(LabelProperty), String) 
    End Get 
    Set(ByVal value As String)
        SetValue(LabelProperty, value)
    End Set
End Property
```

```cppwinrt
// ImageWithLabelControl.h
...
winrt::hstring Label()
{
    return winrt::unbox_value<winrt::hstring>(GetValue(m_labelProperty));
}

void Label(winrt::hstring const& value)
{
    SetValue(m_labelProperty, winrt::box_value(value));
}
...
```

```cpp
//using namespace Platform;
public:
...
  property String^ Label
  {
    String^ get() {
      return (String^)GetValue(LabelProperty);
    }
    void set(String^ value) {
      SetValue(LabelProperty, value);
    }
  }
```

## <a name="property-metadata-for-a-custom-dependency-property"></a>Метаданные свойства для пользовательского свойства зависимостей

Когда свойству зависимостей назначаются метаданные свойства, эти же метаданные применяются к этому свойству для каждого экземпляра типа, которому принадлежит свойство, или его подклассов. В метаданных свойства можно указать два поведения:

- значение по умолчанию, назначаемое системой свойств всем случаям данного свойства;
- статический метод обратного вызова, автоматически вызываемый в системе свойств при обнаружении изменения значения свойства.

### <a name="calling-register-with-property-metadata"></a>Вызов метода Register с метаданными свойства

В предыдущих примерах вызова [**DependencyProperty.Register**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.dependencyproperty.register) мы передавали параметру *propertyMetadata* значение null. Чтобы свойство зависимостей предоставляло значение по умолчанию или использовало обратный вызов при изменении свойства, необходимо определить экземпляр [**PropertyMetadata**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.PropertyMetadata), который предоставляет одну или обе эти возможности.

Как правило, вы предоставляете [**PropertyMetadata**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.PropertyMetadata) как внутренне созданный экземпляр в параметрах для [**DependencyProperty.Register**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.dependencyproperty.register).

> [!NOTE]
> При определении реализации [**креатедефаултвалуекаллбакк**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.createdefaultvaluecallback) необходимо использовать служебный метод [**PropertyMetadata. Create**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.propertymetadata.create) вместо вызова конструктора [**PropertyMetadata**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.PropertyMetadata) для определения экземпляра **PropertyMetadata** .

Следующий пример отличается от предыдущих примеров [**DependencyProperty.Register**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.dependencyproperty.register) ссылкой на экземпляр [**PropertyMetadata**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.PropertyMetadata) с помощью значения [**PropertyChangedCallback**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.propertychangedcallback). Реализация обратного вызова OnLabelChanged показана далее в этом разделе.

```csharp
public static readonly DependencyProperty LabelProperty = DependencyProperty.Register(
  "Label",
  typeof(String),
  typeof(ImageWithLabelControl),
  new PropertyMetadata(null,new PropertyChangedCallback(OnLabelChanged))
);
```

```vb
Public Shared ReadOnly LabelProperty As DependencyProperty =
    DependencyProperty.Register("Label",
      GetType(String),
      GetType(ImageWithLabelControl),
      New PropertyMetadata(
        Nothing, new PropertyChangedCallback(AddressOf OnLabelChanged)))
```

```cppwinrt
// ImageWithLabelControl.cpp
...
Windows::UI::Xaml::DependencyProperty ImageWithLabelControl::m_labelProperty =
    Windows::UI::Xaml::DependencyProperty::Register(
        L"Label",
        winrt::xaml_typename<winrt::hstring>(),
        winrt::xaml_typename<ImageWithLabelControlApp::ImageWithLabelControl>(),
        Windows::UI::Xaml::PropertyMetadata{ nullptr, Windows::UI::Xaml::PropertyChangedCallback{ &ImageWithLabelControl::OnLabelChanged } }
);
...
```

```cpp
DependencyProperty^ ImageWithLabelControl::_LabelProperty =
    DependencyProperty::Register("Label",
    Platform::String::typeid,
    ImageWithLabelControl::typeid,
    ref new PropertyMetadata(nullptr,
      ref new PropertyChangedCallback(&ImageWithLabelControl::OnLabelChanged))
    );
```

### <a name="default-value"></a>Значение по умолчанию

Вы можете указать значение по умолчанию для свойства зависимостей, чтобы это свойство, если оно не задано, всегда возвращало определенное значение по умолчанию. Это значение может отличаться от значения по умолчанию, присущего типу этого свойства.

Если значение по умолчанию не указано, то значением по умолчанию для свойства зависимостей будет null для ссылочного типа, значение по умолчанию типа для типа значения или примитив языка (например, 0 для целого числа или пустая строка для строки). Основная причина для установки значения по умолчанию состоит в том, что это значение восстанавливается при вызове [**ClearValue**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.dependencyobject.clearvalue) на свойстве. Установка значений по умолчанию для отдельных свойств может быть удобнее установки значений по умолчанию в конструкторах, особенно для типов значений. Однако для ссылочных типов следует убедиться, что установка значения по умолчанию не создает непредвиденного шаблона одноэлементного объекта. Подробнее см. далее в разделе [Рекомендации](#best-practices).

```cppwinrt
// ImageWithLabelControl.cpp
...
Windows::UI::Xaml::DependencyProperty ImageWithLabelControl::m_labelProperty =
    Windows::UI::Xaml::DependencyProperty::Register(
        L"Label",
        winrt::xaml_typename<winrt::hstring>(),
        winrt::xaml_typename<ImageWithLabelControlApp::ImageWithLabelControl>(),
        Windows::UI::Xaml::PropertyMetadata{ winrt::box_value(L"default label"), Windows::UI::Xaml::PropertyChangedCallback{ &ImageWithLabelControl::OnLabelChanged } }
);
...
```

> [!NOTE]
> Не регистрироваться со значением по умолчанию [**UnsetValue**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.dependencyproperty.unsetvalue). Это может запутать объект-получатель свойств и повлечет непредвиденные последствия внутри системы свойств.

### <a name="createdefaultvaluecallback"></a>CreateDefaultValueCallback

В некоторых случаях необходимо определить свойства зависимостей для объектов, которые используются в нескольких потоках пользовательского интерфейса. Это требуется, например, когда вы определяете объект данных или элемент управления, которые используются в нескольких приложениях. Вы можете разрешить обмен объектом между разными потоками пользовательского интерфейса, предоставив реализацию [**CreateDefaultValueCallback**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.createdefaultvaluecallback), а не экземпляр значения по умолчанию, привязанный к потоку, зарегистрировавшему это свойство. По сути, [**CreateDefaultValueCallback**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.createdefaultvaluecallback) определяет фабрику для значений по умолчанию. Значение, возвращаемое **CreateDefaultValueCallback**, всегда ассоциируется с текущим потоком пользовательского интерфейса **CreateDefaultValueCallback**, использующим этот объект.

Чтобы определить метаданные, задающие [**CreateDefaultValueCallback**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.createdefaultvaluecallback), необходимо вызвать метод [**PropertyMetadata.Create**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.propertymetadata.create) для возврата экземпляра метаданных. Конструкторы [**PropertyMetadata**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.PropertyMetadata) не имеют подписи, включающей параметр **CreateDefaultValueCallback**.

Обычный шаблон реализации для [**CreateDefaultValueCallback**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.createdefaultvaluecallback): создание нового класса [**DependencyObject**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.DependencyObject), установка специального значения для каждого свойства **DependencyObject** равным желаемому значению по умолчанию, а затем возврат нового класса как ссылки **Object** с помощью возвращаемого значения метода **CreateDefaultValueCallback**.

### <a name="property-changed-callback-method"></a>Метод обратного вызова при изменении свойства

Для определения взаимодействия свойства с иными свойствами зависимостей либо обновления внутреннего свойства или состояния объекта при изменении данного свойства можно задать метод обратного вызова при изменении свойства. Выполнение такого обратного вызова означает, что система свойств обнаружила фактическое изменение значения свойства. Метод обратного вызова является статическим, поэтому параметр *d* метода обратного вызова важен, так как он говорит нам, какой экземпляр класса сообщил об изменении. Типичная реализация использует свойство [**NewValue**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.dependencypropertychangedeventargs.newvalue) данных события и обрабатывает это значение тем или иным образом, обычно выполняя какое-то еще изменение объекта, переданного как *d*. Дополнительные ответы на изменение свойства включают отклонение значения, сообщаемого свойством **NewValue**, восстановление значения [**OldValue**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.dependencypropertychangedeventargs.oldvalue) или присвоение значению программного ограничения, примененного к **NewValue**.

Следующий пример показывает реализацию [**PropertyChangedCallback**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.propertychangedcallback). Он реализует метод, ссылки на который можно было заметить в предыдущих примерах [**Register**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.dependencyproperty.register), в качестве части аргументов создания для [**PropertyMetadata**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.PropertyMetadata). Ситуация, которой касается данный обратный вызов, заключается в том, что у класса также имеется вычисляемое свойство только для чтения под названием HasLabelValue (реализация не показана). При каждой переоценке значения свойства Label вызывается метод обратного вызова, и обратный вызов обеспечивает синхронизацию между независимым вычисленным значением и изменениями в свойстве зависимостей.

```csharp
private static void OnLabelChanged(DependencyObject d, DependencyPropertyChangedEventArgs e) {
    ImageWithLabelControl iwlc = d as ImageWithLabelControl; //null checks omitted
    String s = e.NewValue as String; //null checks omitted
    if (s == String.Empty)
    {
        iwlc.HasLabelValue = false;
    } else {
        iwlc.HasLabelValue = true;
    }
}
```

```vb
    Private Shared Sub OnLabelChanged(d As DependencyObject, e As DependencyPropertyChangedEventArgs)
        Dim iwlc As ImageWithLabelControl = CType(d, ImageWithLabelControl) ' null checks omitted
        Dim s As String = CType(e.NewValue,String) ' null checks omitted
        If s Is String.Empty Then
            iwlc.HasLabelValue = False
        Else
            iwlc.HasLabelValue = True
        End If
    End Sub
```

```cppwinrt
void ImageWithLabelControl::OnLabelChanged(Windows::UI::Xaml::DependencyObject const& d, Windows::UI::Xaml::DependencyPropertyChangedEventArgs const& e)
{
    auto iwlc{ d.as<ImageWithLabelControlApp::ImageWithLabelControl>() };
    auto s{ winrt::unbox_value<winrt::hstring>(e.NewValue()) };
    iwlc.HasLabelValue(s.size() != 0);
}
```

```cpp
static void OnLabelChanged(DependencyObject^ d, DependencyPropertyChangedEventArgs^ e)
{
    ImageWithLabelControl^ iwlc = (ImageWithLabelControl^)d;
    Platform::String^ s = (Platform::String^)(e->NewValue);
    if (s->IsEmpty()) {
        iwlc->HasLabelValue=false;
    }
}
```

### <a name="property-changed-behavior-for-structures-and-enumerations"></a>Реакция на событие изменения свойства для структур и перечислений

Если тип [**DependencyProperty**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.DependencyProperty) является перечислением или структурой, обратный вызов может выполняться, даже если внутренние значения структуры или перечисления не менялись. Этим он отличается от примитивов системы, например строки, где обратный вызов выполняется только в случае, если значение было изменено. Это побочный эффект, оказываемый на эти значения внутренними операциями упаковки-преобразования и распаковки-преобразования. Если вы используете метод [**PropertyChangedCallback**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.propertychangedcallback) для свойства со значением, представленным перечислением или структурой, необходимо сравнить [**OldValue**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.dependencypropertychangedeventargs.oldvalue) и [**NewValue**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.dependencypropertychangedeventargs.newvalue), приведя значения самостоятельно и используя перегруженные операторы сравнения, доступные для только что приведенных значений. Или, если такой оператор недоступен (что возможно в случае пользовательской структуры), может потребоваться сравнить индивидуальные значения. Если в результате значения не меняются, то, как правило, предпринимать ничего не нужно.

```csharp
private static void OnVisibilityValueChanged(DependencyObject d, DependencyPropertyChangedEventArgs e) {
    if ((Visibility)e.NewValue != (Visibility)e.OldValue)
    {
        //value really changed, invoke your changed logic here
    } // else this was invoked because of boxing, do nothing
}
```

```vb
Private Shared Sub OnVisibilityValueChanged(d As DependencyObject, e As DependencyPropertyChangedEventArgs)
    If CType(e.NewValue,Visibility) != CType(e.OldValue,Visibility) Then
        '  value really changed, invoke your changed logic here
    End If
    '  else this was invoked because of boxing, do nothing
End Sub
```

```cppwinrt
static void OnVisibilityValueChanged(Windows::UI::Xaml::DependencyObject const& d, Windows::UI::Xaml::DependencyPropertyChangedEventArgs const& e)
{
    auto oldVisibility{ winrt::unbox_value<Windows::UI::Xaml::Visibility>(e.OldValue()) };
    auto newVisibility{ winrt::unbox_value<Windows::UI::Xaml::Visibility>(e.NewValue()) };

    if (newVisibility != oldVisibility)
    {
        // The value really changed; invoke your property-changed logic here.
    }
    // Otherwise, OnVisibilityValueChanged was invoked because of boxing; do nothing.
}
```

```cpp
static void OnVisibilityValueChanged(DependencyObject^ d, DependencyPropertyChangedEventArgs^ e)
{
    if ((Visibility)e->NewValue != (Visibility)e->OldValue)
    {
        //value really changed, invoke your changed logic here
    } 
    // else this was invoked because of boxing, do nothing
    }
}
```

## <a name="best-practices"></a>Рекомендации

При определении своего свойства зависимостей учитывайте следующие рекомендации.

### <a name="dependencyobject-and-threading"></a>DependencyObject и использование потоков

Все экземпляры [**DependencyObject**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.DependencyObject) должны создаваться в потоке пользовательского интерфейса, связанном с текущей версией [**Window**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Window), которая отображается приложением среды выполнения Windows. Хотя каждый экземпляр **DependencyObject** должен создаваться в основном потоке пользовательского интерфейса, доступ к объектам может обеспечиваться при помощи диспетчерских ссылок из других потоков вызовом [**Dispatcher**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.dependencyobject.dispatcher).

Потоковые аспекты [**DependencyObject**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.DependencyObject) имеют отношение к данному вопросу, поскольку фактически только код, выполняемый в потоке пользовательского интерфейса, может изменить или хотя бы прочитать значение свойства зависимостей. Проблем с потоками обычно можно избежать в стандартном коде пользовательского интерфейса, который обеспечивает корректное использование шаблонов **async** и фоновых рабочих потоков. Как правило, проблемы, связанные с потоками **DependencyObject**, возникают при определении ваших собственных типов **DependencyObject** и попытке использовать их для формирования источников данных либо в других сценариях, где использование **DependencyObject** может быть неуместно.

### <a name="avoiding-unintentional-singletons"></a>Избежание незапланированных одноэлементных объектов

Незапланированный одноэлементный объект может возникнуть, если вы объявляете свойство зависимостей, принимающее ссылочный тип, и вызываете конструктор для этого типа в коде, устанавливающем [**PropertyMetadata**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.PropertyMetadata). В результате все случаи использования свойства зависимостей разделяют единственный экземпляр **PropertyMetadata** и пытаются совместно использовать единственный ссылочный тип, сконструированный вами. Тогда любые подсвойства этого типа значения, устанавливаемые через свойство зависимостей, распространятся на другие объекты путями, которые, возможно, не были предусмотрены.

Для установки исходных значений для свойства зависимостей ссылочного типа можно использовать конструкторы классов, если нужно значение, отличное от null, но следует учитывать, что эти значения будут считаться локальными значениями в целях [общих сведений о свойствах зависимостей](dependency-properties-overview.md). В таком случае правильнее будет использовать шаблон, если класс поддерживает шаблоны. Другой способ избежать одноэлементной схемы, но все же предоставить полезное значение по умолчанию — предоставить статическое свойство на ссылочном типе, дающее подходящие установки по умолчанию для значений класса.

### <a name="collection-type-dependency-properties"></a>Свойства зависимостей типа коллекции

У свойств зависимостей типа коллекции есть свои дополнительные проблемы реализации, которые следует рассмотреть.

Свойства зависимостей типа коллекции довольно редки в API среды выполнения Windows. В большинстве случаев коллекции можно использовать там, где элементы относятся к подклассу [**DependencyObject**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.DependencyObject), но само свойство коллекции реализовано как свойство среды CLR или C++. Это вызвано тем, что коллекции не обязательно подходят некоторым типовым сценариям, включающим свойства зависимостей. Например:

- Мы обычно не анимируем коллекции.
- Мы обычно не выполняем предварительного заполнения элемента в коллекции стилями или шаблоном.
- Хотя привязка к коллекциям является распространенным сценарием, коллекция не обязана быть свойством зависимостей, чтобы быть источником привязки. Для целевых объектов привязки более типично использование подклассов [**ItemsControl**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.ItemsControl) или [**DataTemplate**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.DataTemplate) для поддержки элементов коллекции или использования схем моделей просмотра. Подробнее о привязке коллекций и к коллекциям см. в разделе [Подробно о привязке данных](https://docs.microsoft.com/windows/uwp/data-binding/data-binding-in-depth).
- Для уведомлений об изменении коллекций лучше использовать такие интерфейсы, как **INotifyPropertyChanged** или **INotifyCollectionChanged**, либо наследовать тип коллекции от [**ObservableCollection&lt;T&gt;** ](https://docs.microsoft.com/dotnet/api/system.collections.objectmodel.observablecollection-1).

Тем не менее сценарии для свойств зависимостей типа коллекции существуют. Следующие три раздела предоставляют некоторые рекомендации по реализации свойства зависимостей типа коллекции.

### <a name="initializing-the-collection"></a>Инициализация коллекции

При создании свойства зависимостей можно установить его значение по умолчанию с помощью метаданных. Но остерегайтесь использования одноэлементной статической коллекции в качестве значения по умолчанию. Вместо этого следует преднамеренно установить значение коллекции на уникальную коллекцию (экземпляров) в качестве части логики конструктора классов для класса, которому принадлежит свойство коллекции.

### <a name="change-notifications"></a>Уведомления об изменениях

Определение коллекции как свойства зависимостей не предоставляет автоматически уведомления об изменениях для элементов в коллекции за счет обращения к методу обратного вызова PropertyChanged системы свойств. Если необходимы уведомления для коллекций или элементов коллекций (например, для сценариев привязки данных), реализуйте интерфейс **INotifyPropertyChanged** или **INotifyCollectionChanged** interface. Дополнительные сведения см. в статье [Подробно о привязке данных](https://docs.microsoft.com/windows/uwp/data-binding/data-binding-in-depth).

### <a name="dependency-property-security-considerations"></a>Вопросы безопасности свойств зависимостей

Определяйте свойства зависимостей как открытые свойства. Определяйте идентификаторы свойств зависимостей как члены **public static readonly**. Даже если попытаться объявить иные уровни доступа, допускаемые языком (например, **protected**), доступ к свойству зависимостей всегда будет возможен через идентификатор в сочетании с API системы свойств. Объявление идентификатора свойств зависимостей внутренним или закрытым не будет работать, поскольку без него не сможет правильно работать система свойств.

Свойства-оболочки по сути предназначены лишь для удобства пользователей. Механизмы безопасности, применяемые к оболочкам, можно обойти, вызвав [**GetValue**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.dependencyobject.getvalue) или [**SetValue**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.dependencyobject.setvalue). То есть свойства оболочек лучше держать общедоступными; иначе обычным клиентам будет сложнее обратиться к ним, и при этом не возникнет никаких существенных улучшений безопасности.

В среде выполнения Windows пользовательское свойство зависимостей нельзя зарегистрировать как свойство только для чтения.

### <a name="dependency-properties-and-class-constructors"></a>Свойства зависимостей и конструкторы классов

Общий принцип состоит в том, что конструкторы классов не должны вызывать виртуальные методы. Это связано с тем, что конструкторы можно вызывать для выполнения базовой инициализации конструктора производного класса, а вход в виртуальный метод через конструктор может произойти, когда конструируемый экземпляр объекта еще не до конца проинициализирован. При создании потомков любого класса, который сам является производным от [**DependencyObject**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.DependencyObject), следует помнить, что сама система свойств вызывает и предоставляет виртуальные методы, как часть своих внутренних услуг. Во избежание потенциальных проблем с инициализацией во время выполнения не устанавливайте значения свойств зависимостей внутри конструкторов или классов.

### <a name="registering-the-dependency-properties-for-ccx-apps"></a>Регистрация свойств зависимостей в приложениях на C++/CX

Чтобы реализовать регистрацию свойства в C++/CX, придется приложить больше усилий, чем в C#, так как имеется разделение на заголовочный файл и файл реализации, а также потому, что инициализацию в корневой области файла реализации выполнять не рекомендуется. (Расширения C++ визуальных компонентовC++(/CX) помещает статический код инициализатора из корневой области непосредственно в **DllMain**, тогда как C# компиляторы применяют статические инициализаторы к классам и, таким образом, устраняют проблемы с блокировкой нагрузки **DllMain** .). Лучше всего объявить вспомогательную функцию (для каждого класса), которая будет выполнять регистрацию всех свойств зависимостей для класса. Далее для каждого пользовательского класса, используемого в приложении, необходимо дать ссылку на вспомогательную функцию регистрации, которая предоставляется каждым требуемым пользовательским классом. Вызывайте каждую вспомогательную функцию регистрации один раз в [**конструкторе приложений**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.application.-ctor) (`App::App()`) перед `InitializeComponent`. Этот конструктор выполняется только в случае, когда впервые возникает ссылка на приложение. При возобновлении работы приостановленного приложения этот конструктор не выполняется. Кроме того, как было показано в предыдущем примере регистрации для C++, важно выполнять проверку на **nullptr** каждого вызова [**Register**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.dependencyproperty.register) — это гарантия того, что на вызывающей стороне, обращающейся к функции, регистрация свойства не будет выполнена дважды. Без этой проверки повторная регистрация может вызвать аварийное завершение приложения, поскольку имя свойства будет дублироваться. Этот шаблон реализации можно посмотреть в [примере пользовательских и настраиваемых элементов управления XAML](https://github.com/microsoftarchive/msdn-code-gallery-microsoft/tree/master/Official%20Windows%20Platform%20Sample/XAML%20user%20and%20custom%20controls%20sample) (если нужен вариант этого примера для C++/CX).

## <a name="related-topics"></a>Связанные разделы

- [**DependencyObject**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.DependencyObject)
- [**DependencyProperty. Register**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.dependencyproperty.register)
- [Общие сведения о свойствах зависимостей](dependency-properties-overview.md)
- [Пример пользовательских и настраиваемых элементов управления XAML](https://github.com/microsoftarchive/msdn-code-gallery-microsoft/tree/master/Official%20Windows%20Platform%20Sample/XAML%20user%20and%20custom%20controls%20sample)
 
