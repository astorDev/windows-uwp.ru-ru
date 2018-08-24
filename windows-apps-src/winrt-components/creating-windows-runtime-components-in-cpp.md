---
author: msatranjr
title: Создание компонентов среды выполнения Windows на C++
description: В этом разделе показано, как использовать C + +/ CX, чтобы создать компонент среды выполнения Windows, который является компонентом, который может быть вызван из приложения универсальные Windows, созданных с помощью C#, Visual Basic, C++ или Javascript.
ms.assetid: F7E06AA2-DCEC-427E-BD5D-9CA2A0ED2612
ms.author: misatran
ms.date: 05/14/2018
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: b5515d0ed5dc6e200c7c4fc9a7785c993d4cab59
ms.sourcegitcommit: c6d6f8b54253e79354f8db14e5cf3b113a3e5014
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/24/2018
ms.locfileid: "2843600"
---
# <a name="creating-windows-runtime-components-in-ccx"></a>Создание компонентов среды выполнения Windows на C++/CX
> [!NOTE]
> В этом разделе представлена вспомогательная информация для поддержки приложений на C++/CX. Однако в новых приложениях мы рекомендуем использовать [C++/WinRT](../cpp-and-winrt-apis/intro-to-using-cpp-with-winrt.md). C++/WinRT — это полностью стандартная проекция языка C++17 для API среды выполнения Windows (WinRT), реализованная как библиотека на основе файлов заголовков и предназначенная для предоставления вам первоклассного доступа к современным API-интерфейсам Windows. Чтобы узнать, как создать компонент среды выполнения Windows, с помощью C + +/ WinRT, видеть [создавать события в C + +/ WinRT](../cpp-and-winrt-apis/author-events.md).

В этом разделе показано, как использовать C + +/ CX, чтобы создать компонент среды выполнения Windows, который является компонентом, который может быть вызван из приложения универсальные Windows, созданных с помощью C#, Visual Basic, C++ или Javascript.

Существует несколько причин для создания компонента среды выполнения Windows.
- Использование повышенной производительности C++ при сложных или требующих большого объема вычислений операциях.
- Повторное использование уже написанного и протестированного кода.

При создании решения, содержащего проект JavaScript или .NET и проект компонента среды выполнения Windows, файлы проекта JavaScript и скомпилированная библиотека DLL объединяются в один пакет, отладку которого вы можете выполнить локально в имитаторе или удаленно на связанном устройстве. Вы также можете распространять проект компонента отдельно в виде пакета SDK расширения. Дополнительную информацию см. в разделе [Создание пакета средств разработки](https://msdn.microsoft.com/library/hh768146.aspx).

В общем случае при создании кода C + +/ компонент CX использовать регулярное библиотеки C++ и встроенных типов, за исключением на границе абстрактный двоичный интерфейс (ABI) где передаче данных из кода в другой пакет .winmd. Существует, используйте типы среды выполнения Windows и специальный синтаксис C + +/ CX поддерживает для создания и работы этих типов. Кроме того, в вашей C + +/ CX кода, типы использования, например делегат и события для реализации событий, которые вызвали из компонента и обрабатываться в JavaScript, Visual Basic, C++ или C#. Дополнительные сведения о C + +/ синтаксис CX увидеть [Справочник по языку Visual C++ (C + +/ CX)](https://msdn.microsoft.com/library/windows/apps/xaml/hh699871.aspx).

## <a name="casing-and-naming-rules"></a>Регистр символов и правила именования

### <a name="javascript"></a>JavaScript
В языке JavaScript учитывается регистр символов. Поэтому необходимо следовать следующим соглашениям об использовании регистров.

-   При создании ссылок на пространства имен и классы C++ используйте тот же стиль, который используется в C++.
-   При вызове методов используйте "верблюжий" стиль, даже если в компоненте C++ имя метода написано прописными буквами. Например, метод GetDate() C++ следует вызывать из JavaScript как getDate().
-   Активируемые имена классов и пространств имен не могут содержать символы Юникода.

### <a name="net"></a>.NET
В языках .NET действуют их обычные правила использования регистров.

## <a name="instantiating-the-object"></a>Создание экземпляра объекта
Через границу интерфейса ABI можно передавать только типы среды выполнения Windows. Если компонент содержит тип, подобный std::wstring в качестве возвращаемого типа или параметра открытого метода, компилятор создает ошибку. Компонент расширений Visual C++ (C + +/ CX) встроенных типов включают обычным скаляры, такие как int и типа double, а также их int32 typedef эквивалентами float64 и т. д. Дополнительные сведения см. в разделе [Система типов (C++/CX)](https://msdn.microsoft.com/library/windows/apps/hh755822.aspx).

```cpp
// ref class definition in C++
public ref class SampleRefClass sealed
{
    // Class members...

    // #include <valarray>
public:
    double LogCalc(double input)
    {
        // Use C++ standard library as usual.
        return std::log(input);
    }

};
```

```javascript
//Instantiation in JavaScript (requires "Add reference > Project reference")
var nativeObject = new CppComponent.SampleRefClass();
```

```csharp
//Call a method and display result in a XAML TextBlock
var num = nativeObject.LogCalc(21.5);
ResultText.Text = num.ToString();
```

## <a name="ccx-built-in-types-library-types-and-windows-runtime-types"></a>C + +/ CX встроенных типов, библиотека типов и типов среды выполнения Windows
Экземпляр активируемого класса (также называемого классом ссылки) может быть создан из другого языка, например из JavaScript, C# или Visual Basic. Чтобы компонент можно было использовать в другом языке, он должен содержать по крайней мере один активируемый класс.

Компонент среды выполнения Windows может содержать несколько открытых активируемых классов, а также дополнительные классы, которые доступны только для внутреннего использования в компоненте. Применить атрибут [WebHostHidden](https://msdn.microsoft.com/library/windows/apps/windows.foundation.metadata.webhosthiddenattribute.aspx) C + +/ CX типы, которые не должны быть видны JavaScript.

Все открытые классы должны располагаться в одном и том же корневом пространстве имен, имя которого совпадает с именем файла метаданных компонента. Например, экземпляр класса с именем A.B.C.MyClass может быть создан, только если он определен в файле метаданных с именем A.winmd, A.B.winmd или A.B.C.winmd. Имя DLL-файла не обязательно должно соответствовать имени WINMD-файла.

Клиентский код создает экземпляр компонента с помощью ключевого слова **new** (**New** в Visual Basic) точно так же, как для любого другого класса.

Активируемый класс должен быть объявлен как **открытый запечатанный класс ссылки**. Ключевое слово **ref class** указывает компилятору, что нужно создать класс как тип, совместимый со средой выполнения Windows, а ключевое слово sealed запрещает наследование от этого класса. Среда выполнения Windows в настоящее время не поддерживает обобщенную модель наследования; ограниченная модель наследования поддерживает создание пользовательских элементов управления XAML. Дополнительные сведения см. в разделе [Классы и структуры ссылки (C++/CX)](https://msdn.microsoft.com/library/windows/apps/xaml/hh699870.aspx).

C + +/ CX, все числовые примитивы определены в пространстве имен по умолчанию. Пространство имен [платформа](https://msdn.microsoft.com/library/windows/apps/xaml/hh710417.aspx) содержит C + +/ CX классы, которые специфичны для среды выполнения Windows введите системы. К ним относятся классы [Platform::String](https://msdn.microsoft.com/library/windows/apps/xaml/hh755812.aspx) и [Platform::Object](https://msdn.microsoft.com/library/windows/apps/xaml/hh748265.aspx). Конкретные типы коллекций, такие как [Platform::Collections::Map](https://msdn.microsoft.com/library/windows/apps/xaml/hh441508.aspx) и [Platform::Collections::Vector](https://msdn.microsoft.com/library/windows/apps/xaml/hh441570.aspx), определяются в пространстве имен [Platform::Collections](https://msdn.microsoft.com/library/windows/apps/xaml/hh710418.aspx). Открытые интерфейсы, реализуемые этими типами, определяются в пространстве имен [Windows::Foundation::Collections (C++/CX)](https://msdn.microsoft.com/library/windows/apps/xaml/hh441496.aspx). Именно эти типы интерфейсов используются кодом JavaScript, C# и Visual Basic. Дополнительные сведения см. в разделе [Система типов (C++/CX)](https://msdn.microsoft.com/library/windows/apps/hh755822.aspx).

## <a name="method-that-returns-a-value-of-built-in-type"></a>Метод, возвращающий значение встроенного типа
```cpp
    // #include <valarray>
public:
    double LogCalc(double input)
    {
        // Use C++ standard library as usual.
        return std::log(input);
    }
```

```javascript
//Call a method
var nativeObject = new CppComponent.SampleRefClass;
var num = nativeObject.logCalc(21.5);
document.getElementById('P2').innerHTML = num;
```

## <a name="method-that-returns-a-custom-value-struct"></a>Метод, возвращающий пользовательскую структуру значения
```cpp
namespace CppComponent
{
    // Custom struct
    public value struct PlayerData
    {
        Platform::String^ Name;
        int Number;
        double ScoringAverage;
    };

    public ref class Player sealed
    {
    private:
        PlayerData m_player;
    public:
        property PlayerData PlayerStats
        {
            PlayerData get(){ return m_player; }
            void set(PlayerData data) {m_player = data;}
        }
    };
}
```

Для передачи через интерфейс ABI структуры выбранные пользователем значения, определите объект JavaScript, который имеет же членов, что значение структуры, определенный в C + +/ CX. Затем передайте этот объект в качестве аргумента для C + +/ CX метод, чтобы объект неявно преобразуется в C + +/ CX типа.

```javascript
// Get and set the value struct
function GetAndSetPlayerData() {
    // Create an object to pass to C++
    var myData =
        { name: "Bob Homer", number: 12, scoringAverage: .357 };
    var nativeObject = new CppComponent.Player();
    nativeObject.playerStats = myData;

    // Retrieve C++ value struct into new JavaScript object
    var myData2 = nativeObject.playerStats;
    document.getElementById('P3').innerHTML = myData.name + " , " + myData.number + " , " + myData.scoringAverage.toPrecision(3);
}
```

Другой способ состоит в определении класса, который реализует интерфейс IPropertySet (не показан).

На языках .NET, просто создания переменной типа, определенный в C + +/ CX компонента.

```csharp
private void GetAndSetPlayerData()
{
    // Create a ref class
    var player = new CppComponent.Player();

    // Create a variable of a value struct
    // type that is defined in C++
    CppComponent.PlayerData myPlayer;
    myPlayer.Name = "Babe Ruth";
    myPlayer.Number = 12;
    myPlayer.ScoringAverage = .398;

    // Set the property
    player.PlayerStats = myPlayer;

    // Get the property and store it in a new variable
    CppComponent.PlayerData myPlayer2 = player.PlayerStats;
    ResultText.Text += myPlayer.Name + " , " + myPlayer.Number.ToString() +
        " , " + myPlayer.ScoringAverage.ToString();
}
```

## <a name="overloaded-methods"></a>Перегруженные методы
C + +/ CX ref открытый класс может содержать перегруженные методы, но JavaScript с ограниченными возможность различать перегруженные методы. Например, он может найти разницу между следующими сигнатурами:

```cpp
public ref class NumberClass sealed
{
public:
    int GetNumber(int i);
    int GetNumber(int i, Platform::String^ str);
    double GetNumber(int i, MyData^ d);
};
```

Однако ему не удастся найти разницу между следующими:

```cpp
int GetNumber(int i);
double GetNumber(double d);
```

В случаях неоднозначности можно добиться того, что код JavaScript всегда будет вызывать конкретную перегрузку, применив атрибут [Windows::Foundation::Metadata::DefaultOverload](https://msdn.microsoft.com/library/windows/apps/windows.foundation.metadata.defaultoverloadattribute.aspx) к сигнатуре метода в файле заголовка.

Этот код JavaScript всегда вызывает перегрузку с атрибутом:

```javascript
var nativeObject = new CppComponent.NumberClass();
var num = nativeObject.getNumber(9);
document.getElementById('P4').innerHTML = num;
```

## <a name="net"></a>.NET
Языки .NET распознает перегрузки в C + +/ класс ref CX так же, как и в любой класс .NET Framework.

## <a name="datetime"></a>DateTime
В среде выполнения в Windows объект [Windows::Foundation::DateTime](https://msdn.microsoft.com/library/windows/apps/windows.foundation.datetime.aspx) является лишь 64-разрядным знаковым целым числом, представляющим количество 100-наносекундных интервалов до или после 1 января 1601 г. У объекта Windows:Foundation::DateTime нет методов. Вместо этого в каждом языке создается проекция DateTime, соответствующая этому языку: объект Date в JavaScript и типы System.DateTime и System.DateTimeOffset в .NET Framework.

```cpp
public  ref class MyDateClass sealed
{
public:
    property Windows::Foundation::DateTime TimeStamp;
    void SetTime(Windows::Foundation::DateTime dt)
    {
        auto cal = ref new Windows::Globalization::Calendar();
        cal->SetDateTime(dt);
        TimeStamp = cal->GetDateTime(); // or TimeStamp = dt;
    }
};
```

При передаче значения даты и времени от C + +/ CX JavaScript, JavaScript принимает в качестве объект Date и отображает его по умолчанию как строка длинной даты.

```javascript
function SetAndGetDate() {
    var nativeObject = new CppComponent.MyDateClass();

    var myDate = new Date(1956, 4, 21);
    nativeObject.setTime(myDate);

    var myDate2 = nativeObject.timeStamp;

    //prints long form date string
    document.getElementById('P5').innerHTML = myDate2;

}
```

Если язык .NET передает объект System.DateTime C + +/ компонент CX метод принимает его как Windows::Foundation::DateTime. Когда компонент передает Windows::Foundation::DateTime методу .NET Framework, метод .NET Framework принимает его как DateTimeOffset.

```csharp
private void DateTimeExample()
{
    // Pass a System.DateTime to a C++ method
    // that takes a Windows::Foundation::DateTime
    DateTime dt = DateTime.Now;
    var nativeObject = new CppComponent.MyDateClass();
    nativeObject.SetTime(dt);

    // Retrieve a Windows::Foundation::DateTime as a
    // System.DateTimeOffset
    DateTimeOffset myDate = nativeObject.TimeStamp;

    // Print the long-form date string
    ResultText.Text += myDate.ToString();
}
```

## <a name="collections-and-arrays"></a>Коллекции и массивы
Коллекции всегда передаются через границу ABI в виде дескрипторов типов среды выполнения Windows, таких как Windows::Foundation::Collections::IVector^ и Windows::Foundation::Collections::IMap^. Например, если возвращается дескриптор типа Platform::Collections::Map, он будет неявно преобразован в Windows::Foundation::Collections::IMap^. Интерфейсы коллекции определены в пространстве имен отдельно от C + +/ CX классы, обеспечивающие реализации. Эти интерфейсы используются в языках JavaScript и .NET. Дополнительные сведения см. в разделах [Коллекции (C++/CX)](https://msdn.microsoft.com//library/windows/apps/hh700103.aspx) и [Классы Array и WriteOnlyArray (C++/CX)](https://msdn.microsoft.com/library/windows/apps/hh700131.aspx).

## <a name="passing-ivector"></a>Передача IVector
```cpp
// Windows::Foundation::Collections::IVector across the ABI.
//#include <algorithm>
//#include <collection.h>
Windows::Foundation::Collections::IVector<int>^ SortVector(Windows::Foundation::Collections::IVector<int>^ vec)
{
    std::sort(begin(vec), end(vec));
    return vec;
}
```

```javascript
var nativeObject = new CppComponent.CollectionExample();
// Call the method to sort an integer array
var inVector = [14, 12, 45, 89, 23];
var outVector = nativeObject.sortVector(inVector);
var result = "Sorted vector to array:";
for (var i = 0; i < outVector.length; i++)
{
    outVector[i];
    result += outVector[i].toString() + ",";
}
document.getElementById('P6').innerHTML = result;
```

В языках .NET IVector&lt;T&gt; представляется как IList&lt;T&gt;.

```csharp
private void SortListItems()
{
    IList<int> myList = new List<int>();
    myList.Add(5);
    myList.Add(9);
    myList.Add(17);
    myList.Add(2);

    var nativeObject = new CppComponent.CollectionExample();
    IList<int> mySortedList = nativeObject.SortVector(myList);

    foreach (var item in mySortedList)
    {
        ResultText.Text += " " + item.ToString();
    }
}
```

## <a name="passing-imap"></a>Передача IMap
```cpp
// #include <map>
//#include <collection.h>
Windows::Foundation::Collections::IMap<int, Platform::String^> ^GetMap(void)
{    
    Windows::Foundation::Collections::IMap<int, Platform::String^> ^ret =
        ref new Platform::Collections::Map<int, Platform::String^>;
    ret->Insert(1, "One ");
    ret->Insert(2, "Two ");
    ret->Insert(3, "Three ");
    ret->Insert(4, "Four ");
    ret->Insert(5, "Five ");
    return ret;
}
```

```javascript
// Call the method to get the map
var outputMap = nativeObject.getMap();
var mStr = "Map result:" + outputMap.lookup(1) + outputMap.lookup(2)
    + outputMap.lookup(3) + outputMap.lookup(4) + outputMap.lookup(5);
document.getElementById('P7').innerHTML = mStr;
```

В языках .NET IMap представляется как IDictionary&lt;K, V&gt;.

```csharp
private void GetDictionary()
{
    var nativeObject = new CppComponent.CollectionExample();
    IDictionary<int, string> d = nativeObject.GetMap();
    ResultText.Text += d[2].ToString();
}
```

## <a name="properties"></a>Свойства
Класс общедоступных ref в C + +/ CX компонент расширений предоставляет открытых элементов данных в виде свойств, с помощью свойства ключевого слова. Это понятие идентично свойствам в .NET Framework. Простое свойство похоже на элемент данных, поскольку его функциональность является неявной. Нетривиальное свойство имеет явные методы доступа get и set и закрытую переменную с именем, которая является «резервным хранилищем» для значения. В этом примере закрытая переменная-член \_propertyAValue является резервным хранилищем для свойства PropertyA. Свойство может инициировать событие при изменении значения свойства, а клиентское приложение может зарегистрироваться для получения этого события.

```cpp
//Properties
public delegate void PropertyChangedHandler(Platform::Object^ sender, int arg);
public ref class PropertyExample  sealed
{
public:
    PropertyExample(){}

    // Event that is fired when PropertyA changes
    event PropertyChangedHandler^ PropertyChangedEvent;

    // Property that has custom setter/getter
    property int PropertyA
    {
        int get() { return m_propertyAValue; }
        void set(int propertyAValue)
        {
            if (propertyAValue != m_propertyAValue)
            {
                m_propertyAValue = propertyAValue;
                // Fire event. (See event example below.)
                PropertyChangedEvent(this, propertyAValue);
            }
        }
    }

    // Trivial get/set property that has a compiler-generated backing store.
    property Platform::String^ PropertyB;

private:
    // Backing store for propertyA.
    int m_propertyAValue;
};
```

```javascript
var nativeObject = new CppComponent.PropertyExample();
var propValue = nativeObject.propertyA;
document.getElementById('P8').innerHTML = propValue;

//Set the string property
nativeObject.propertyB = "What is the meaning of the universe?";
document.getElementById('P9').innerHTML += nativeObject.propertyB;
```

Языки .NET доступ к свойствам на собственный C + +/ CX объект так же, как если бы в объекте .NET Framework.

```csharp
private void GetAProperty()
{
    // Get the value of the integer property
    // Instantiate the C++ object
    var obj = new CppComponent.PropertyExample();

    // Get an integer property
    var propValue = obj.PropertyA;
    ResultText.Text += propValue.ToString();

    // Set a string property
    obj.PropertyB = " What is the meaning of the universe?";
    ResultText.Text += obj.PropertyB;

}
```

## <a name="delegates-and-events"></a>Делегаты и события
Делегат — это тип среды выполнения Windows, представляющий объект функции. Делегаты можно использовать в связи с событиями, обратными вызовами и асинхронными вызовами методов, чтобы задать действие, которое будет выполнено позже. Подобно объекту функции, делегат обеспечивает безопасность типа, позволяя компилятору проверять тип возвращаемого значения и типы параметров функции. Объявление делегата напоминает сигнатуру функции, реализация аналогична определению класса, а его вызов похож на вызов функции.

## <a name="adding-an-event-listener"></a>Добавление прослушивателя событий
Можно использовать ключевое слово event для объявления открытого члена конкретного типа делегата. Клиентский код подписывается на событие с помощью стандартных механизмов, которые содержатся в используемом языке.

```cpp
public:
    event SomeHandler^ someEvent;
```

В этом примере используется тот же код C++, что и в предыдущем разделе, посвященном свойствам.

```javascript
function Button_Click() {
    var nativeObj = new CppComponent.PropertyExample();
    // Define an event handler method
    var singlecasthandler = function (ev) {
        document.getElementById('P10').innerHTML = "The button was clicked and the value is " + ev;
    };

    // Subscribe to the event
    nativeObj.onpropertychangedevent = singlecasthandler;

    // Set the value of the property and fire the event
    var propValue = 21;
    nativeObj.propertyA = 2 * propValue;

}
```

В языках .NET подписка на событие в компоненте C++ не отличается от подписки на событие в классе .NET Framework:

```csharp
//Subscribe to event and call method that causes it to be fired.
private void TestMethod()
{
    var objWithEvent = new CppComponent.PropertyExample();
    objWithEvent.PropertyChangedEvent += objWithEvent_PropertyChangedEvent;

    objWithEvent.PropertyA = 42;
}

//Event handler method
private void objWithEvent_PropertyChangedEvent(object __param0, int __param1)
{
    ResultText.Text = "the event was fired and the result is " +
         __param1.ToString();
}
```

## <a name="adding-multiple-event-listeners-for-one-event"></a>Добавление нескольких прослушивателей событий для одного события
В JavaScript имеется метод addEventListener, который позволяет нескольким обработчикам подписываться на одно событие.

```cpp
public delegate void SomeHandler(Platform::String^ str);

public ref class LangSample sealed
{
public:
    event SomeHandler^ someEvent;
    property Platform::String^ PropertyA;

    // Method that fires an event
    void FireEvent(Platform::String^ str)
    {
        someEvent(Platform::String::Concat(str, PropertyA->ToString()));
    }
    //...
};
```

```javascript
// Add two event handlers
var multicast1 = function (ev) {
    document.getElementById('P11').innerHTML = "Handler 1: " + ev.target;
};
var multicast2 = function (ev) {
    document.getElementById('P12').innerHTML = "Handler 2: " + ev.target;
};

var nativeObject = new CppComponent.LangSample();
//Subscribe to the same event
nativeObject.addEventListener("someevent", multicast1);
nativeObject.addEventListener("someevent", multicast2);

nativeObject.propertyA = "42";

// This method should fire an event
nativeObject.fireEvent("The answer is ");
```

В C# любое количество обработчиков событий может подписаться на событие с помощью оператора +=, как показано в предыдущем примере.

## <a name="enums"></a>Перечисления
Перечисление среды выполнения Windows в C + +/ CX объявлен с помощью перечисления открытый класс. примерно так же с областью enum в стандартный C++.

```cpp
public enum class Direction {North, South, East, West};

public ref class EnumExampleClass sealed
{
public:
    property Direction CurrentDirection
    {
        Direction  get(){return m_direction; }
    }

private:
    Direction m_direction;
};
```

Значения перечисления передаются между C + +/ CX и JavaScript, как целые числа. При необходимости можно объявить объект JavaScript, который содержит такие же значения именованного C + +/ CX enum и используйте ее в качестве образом.

```javascript
var Direction = { 0: "North", 1: "South", 2: "East", 3: "West" };
//. . .

var nativeObject = new CppComponent.EnumExampleClass();
var curDirection = nativeObject.currentDirection;
document.getElementById('P13').innerHTML =
Direction[curDirection];
```

Перечисления поддерживаются как в C#, так и в Visual Basic. Эти языки обрабатывают открытый класс перечисления C++ так же, как и перечисление .NET Framework.

## <a name="asynchronous-methods"></a>Асинхронные методы
Чтобы использовать асинхронные методы, предоставляемые другими объектами среды выполнения Windows, используйте [класс task (среда выполнения с параллелизмом)](https://msdn.microsoft.com/library/hh750113.aspx). Дополнительные сведения см. в разделе [Параллелизм задач (среда выполнения с параллелизмом)](https://msdn.microsoft.com/library/dd492427.aspx).

Для реализации асинхронных методов в C + +/ CX, используйте функцию [create\_async](https://msdn.microsoft.com/library/hh750102.aspx) , определенный в ppltasks.h. Дополнительные сведения можно [Создание асинхронной операции в C + +/ CX приложений UWP](https://msdn.microsoft.com/library/vstudio/hh750082.aspx). Пример приведен [Пошаговое руководство: Создание базовой среды выполнения Windows компонент в C + +/ CX и вызов из JavaScript или C#](walkthrough-creating-a-basic-windows-runtime-component-in-cpp-and-calling-it-from-javascript-or-csharp.md). Использование языков .NET C + +/ CX асинхронные методы так же, как если бы асинхронного метода, определенного в .NET Framework.

## <a name="exceptions"></a>Исключения
Можно создавать исключения любого типа, определенного в среде выполнения Windows. От типов исключений среды выполнения Windows нельзя наследовать пользовательские типы. Однако можно создать исключение COMException и предоставить пользовательский объект HRESULT, который может быть доступен для кода, перехватывающего исключение. Способы задания пользовательского сообщения в исключении COMException не предусмотрены.

## <a name="debugging-tips"></a>Советы по отладке
При отладке решения JavaScript, содержащего библиотеку DLL компонента, можно настроить отладчик для пошагового выполнения скрипта или машинного кода в компоненте, однако нельзя отлаживать эти части одновременно. Чтобы изменить этот параметр, разверните узел проекта JavaScript в обозревателе решений, а затем последовательно выберите пункты Свойства, Отладка, Тип отладчика.

Обязательно выберите соответствующие возможности в конструкторе пакетов. Например, если вы пытаетесь открыть файл образа в библиотеке изображений пользователя с помощью интерфейсов API среды выполнения Windows, необходимо установить флажок Библиотека изображений в области Возможности конструктора манифестов.

Если коду JavaScript не удается распознать открытые свойства или методы в компоненте, убедитесь, что в JavaScript используется "верблюжий" стиль имен. Например, LogCalc c + +/ CX метод должен ссылаться как logCalc в JavaScript.

При удалении C + +/ проект компонента среды выполнения Windows CX из решения, необходимо вручную удалить ссылку проекта из JavaScript project. Невыполнение этого требования приведет к невозможности последующей отладки и сборки. При необходимости можно добавить ссылку на сборку в библиотеку DLL.

## <a name="related-topics"></a>Еще по теме
* [Пошаговое руководство: Создание базовой среды выполнения Windows компонент в C + +/ CX и вызов из JavaScript или C#](walkthrough-creating-a-basic-windows-runtime-component-in-cpp-and-calling-it-from-javascript-or-csharp.md)
