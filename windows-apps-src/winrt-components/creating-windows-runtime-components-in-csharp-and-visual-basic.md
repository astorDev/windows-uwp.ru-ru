---
title: Создание компонентов среды выполнения Windows в C# и Visual Basic
description: Начиная с .NET Framework 4.5 можно создавать собственные типы среды выполнения Windows, упакованные в компонент среды выполнения Windows, с помощью управляемого кода.
ms.assetid: A5672966-74DF-40AB-B01E-01E3FCD0AD7A
ms.date: 12/04/2018
ms.topic: article
dev_langs:
- csharp
- vb
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: b4f5a2de5c3fa5564b4e4389cfc0806fd5d2844f
ms.sourcegitcommit: 49d58bc66c1c9f2a4f81473bcb25af79e2b1088d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/11/2018
ms.locfileid: "8928113"
---
# <a name="creating-windows-runtime-components-in-c-and-visual-basic"></a>Создание компонентов среды выполнения Windows в C# и Visual Basic
Начиная с .NET Framework 4.5, можно использовать управляемый код для создания собственных типов среды выполнения Windows и упаковать их в компоненте среды выполнения Windows. Компонент можно использовать в приложениях универсальной платформы Windows (UWP), написанных на C++, JavaScript, Visual Basic или C#. В этом разделе описываются правила создания компонента и рассматриваются некоторые аспекты поддержки среды выполнения Windows в .NET Framework. Как правило, такая поддержка разрабатывается таким образом, чтобы быть прозрачной для разработчиков .NET Framework. Однако при создании компонента, использующего JavaScript или C++, следует учитывать различия в том, как эти языки поддерживают среду выполнения Windows.

Если вы создаете компонент для использования только в приложениях UWP, написанных на Visual Basic или C#, и он не содержит элементы управления UWP, затем onsider, используя шаблон **Библиотеки классов** вместо шаблона проекта **Компонента среды выполнения Windows** в Microsoft Visual Studio. У простой библиотеки классов меньше ограничений.

## <a name="declaring-types-in-windows-runtime-components"></a>Объявление типов в компонентах среды выполнения Windows

Внутри компонента типы среды выполнения Windows можно использовать любые функции .NET Framework, разрешенные в приложении UWP. Дополнительные сведения см. в разделе [.NET для приложений UWP](https://msdn.microsoft.com/library/windows/apps/mt185501).

Снаружи члены ваших типов могут предоставлять только типы среды выполнения Windows для соответствующих параметров и возвращаемых значений. Ниже перечислены ограничения, действующие для типов .NET Framework, которые доступны в компоненте среды выполнения Windows.

- Поля, параметры и возвращаемые значения всех открытых типов и членов в ваших компонентах должны относиться к типам среды выполнения Windows. Это ограничение включает типы среды выполнения Windows, которые вы создаете, а также типы, предоставляемые средой выполнения Windows. Кроме того, оно включает некоторые типы .NET Framework. Включение этих типов является частью поддержки .NET Framework предоставляет, обеспечивающих естественное использование среды выполнения Windows в управляемом коде&mdash;отображается ваш код использует привычные типы .NET Framework вместо соответствующих типов среды выполнения Windows. Например, можно использовать простые типы .NET Framework, такие как **Int32** и **Double**, некоторые фундаментальные типы, такие как **DateTimeOffset** и **Uri**, и некоторые часто используемые универсальные типы интерфейсов например **IEnumerable&lt;T &gt; ** (IEnumerable (Of T) в Visual Basic) и **IDictionary&lt;TKey, TValue&gt;**. Обратите внимание, что аргументы этих общих типов должны представлять типы среды выполнения Windows. Эти вопросы рассматриваются в разделах [типов передачи среды выполнения Windows в управляемом коде](#passing-windows-runtime-types-to-managed-code) и [Передача управляемых типов в среду выполнения Windows](#passing-managed-types-to-the-windows-runtime), далее в этом разделе.

- Открытые классы и интерфейсы могут содержать методы, свойства и события. Можно объявить делегаты для событий или использовать **EventHandler&lt;T&gt; ** делегат. Открытые классы и интерфейсы не могут:
    - быть универсальными;
    - Реализуйте интерфейс, который не является интерфейс среды выполнения Windows (тем не менее, можно создать собственные интерфейсы среды выполнения Windows и реализовать их).
    - Класс, производный от типов, которые не входят в среде выполнения Windows, таким как **System.Exception** и **System.EventArgs**.

- Корневое пространство имен всех открытых типов должно совпадать с именем сборки, а имя сборки не должно начинаться на «Windows».

    > **Совет**. По умолчанию проекты Visual Studio имеют имена пространств имен, которые совпадают с именами сборок. В Visual Basic оператор Namespace для данного пространства имен по умолчанию в коде не отображается.

- Открытые структуры не могут иметь членов, отличных от открытых полей, а эти поля должны иметь тип значения или строковый тип.
- Открытые классы должны быть **запечатанными** (**NotInheritable** в Visual Basic). Если модель программирования требует полиморфизма, можно создать открытый интерфейс и реализовать этот интерфейс в классах, которые должны быть полиморфными.

## <a name="debugging-your-component"></a>Отладка компонента

Если ваше приложение UWP и компонент построены с использованием управляемого кода, затем можно отлаживать оба одновременно.

При тестировании компонента как части приложения UWP на C++, в то же время можно отлаживать управляемый и машинный код. По умолчанию может отлаживаться только машинный код.

## <a name="to-debug-both-native-c-code-and-managed-code"></a>Одновременная отладка машинного кода C++ и управляемого кода
1.  Откройте контекстное меню проекта Visual C++ и выберите пункт **Свойства**.
2.  На страницах свойств в разделе **Свойства конфигурации** выберите **Отладка**.
3.  Выберите **Тип отладчика** и в раскрывающемся списке измените значение **Только машинный код** на **Смешанный (управляемый и машинный)**. Нажмите кнопку **ОК**.
4.  Установите точки останова в машинном и управляемом коде.

При тестировании компонента как части приложения UWP на JavaScript, решение по умолчанию находится в режиме отладки JavaScript. В Visual Studio невозможно отлаживать JavaScript и управляемый код одновременно.

## <a name="to-debug-managed-code-instead-of-javascript"></a>Отладка управляемого кода вместо кода JavaScript
1.  Откройте контекстное меню проекта JavaScript и выберите пункт **Свойства**.
2.  На страницах свойств в разделе **Свойства конфигурации** выберите **Отладка**.
3.  Выберите **Тип отладчика** и в раскрывающемся списке замените значение **Только скрипт** на **Только управляемый код**. Нажмите кнопку **ОК**.
4.  Установите точки останова в управляемом коде и выполните отладку обычным образом.

## <a name="passing-windows-runtime-types-to-managed-code"></a>Передача типов среды выполнения Windows в управляемый код
Как упоминалось ранее в разделе [объявление типов в компонентах среды выполнения Windows](#declaring-types-in-windows-runtime-components), некоторые типы .NET Framework могут встречаться в сигнатурах членов открытых классов. Это является частью возможностей .NET Framework, обеспечивающих естественное использование среды выполнения Windows в управляемом коде. Эти возможности включают простые типы, а также некоторые классы и интерфейсы. Когда компонент используется из JavaScript или из кода C++, важно знать, как типы .NET Framework представляются в вызывающий код. Примеры с JavaScript см. в разделе [ Пошаговое руководство. Создание простого компонента в C# или Visual Basic и его вызов из кода JavaScript](walkthrough-creating-a-simple-windows-runtime-component-and-calling-it-from-javascript.md). В этом разделе рассматриваются часто используемые типы.

В .NET Framework простые типы, такие как **Int32** структура имеет множество полезных свойств и методов, таких как метод **TryParse** . В среде выполнения Windows, напротив, простые типы имеют только поля. При передаче этих типов в управляемый код они представляются типами .NET Framework, и свойства и методы таких типов .NET Framework можно использовать обычным образом. В следующем списке перечислены подстановки, которые автоматически выполняются в интегрированной среде разработки:

-   Для примитивов среды выполнения Windows **Int32** **Int64**, **один**, **Double**, **Boolean**, **String** (Неизменяемая коллекция символов Юникода), **Enum**, **UInt32**, **UInt64**и **Guid **, используйте тип то же имя в пространстве имен System.
-   **UInt8**используется **тип System.Byte**.
-   **Вместо Char16**используется **тип System.Char**.
-   Вместо интерфейса **IInspectable** используется **тип System.Object**.

Если C# или Visual Basic имеется ключевое слово для какого-либо из этих типов, затем можно использовать ключевое слово языка вместо.

Помимо простых типов в управляемом коде в форме эквивалентов из .NET Framework используются некоторые распространенные типы среды выполнения Windows. Например предположим, ваш код JavaScript используется класс **Windows.Foundation.Uri** , и вы хотите передать методу C# или Visual Basic. Эквивалентным типом в управляемом коде будет класс .NET Framework **System.Uri** , и это типом параметра метода. Можно легко понять, в каких случаях тип среды выполнения Windows представляется типом .NET Framework, поскольку при написании управляемого кода система IntelliSense в Visual Studio скрывает тип среды выполнения Windows и представляет эквивалентный тип .NET Framework. (Обычно имена таких двух типов совпадают. Тем не менее Обратите внимание, что структура **Windows.Foundation.DateTime** отображается в управляемом коде как **System.DateTimeOffset** , а не как **System.DateTime**.)

Для некоторых часто используемых типов коллекций сопоставление устанавливается между интерфейсами, которые реализуются типом среды выполнения Windows, и интерфейсами, которые реализуются соответствующим типом .NET Framework. Как и в случае вышеупомянутых типов, параметры объявляются с использованием типов .NET Framework. Это позволяет скрывать некоторые различия между типами и делает написание кода .NET Framework более естественным.

В следующей таблице перечислены наиболее распространенные из этих типов универсальных интерфейсов, а также другие сопоставления часто используемых классов и интерфейсов. Полный список типов среды выполнения Windows, которые сопоставляет .NET Framework см. в разделе [.NET Framework сопоставления типов среды выполнения Windows](net-framework-mappings-of-windows-runtime-types.md).

| Среда выполнения Windows                                  | .NET Framework                                    |
|-|-|
| IIterable&lt;T&gt;                               | IEnumerable&lt;T&gt;                              |
| IVector&lt;T&gt;                                 | IList&lt;T&gt;                                    |
| IVectorView&lt;T&gt;                             | IReadOnlyList&lt;T&gt;                            |
| IMap&lt;K, V&gt;                                 | IDictionary&lt;TKey, TValue&gt;                   |
| IMapView&lt;K, V&gt;                             | IReadOnlyDictionary&lt;TKey, TValue&gt;           |
| IKeyValuePair&lt;K, V&gt;                        | KeyValuePair&lt;TKey, TValue&gt;                  |
| IBindableIterable                                | IEnumerable                                       |
| IBindableVector                                  | IList                                             |
| Windows.UI.Xaml.Data.INotifyPropertyChanged      | System.ComponentModel.INotifyPropertyChanged      |
| Windows.UI.Xaml.Data.PropertyChangedEventHandler | System.ComponentModel.PropertyChangedEventHandler |
| Windows.UI.Xaml.Data.PropertyChangedEventArgs    | System.ComponentModel.PropertyChangedEventArgs    |

Если тип реализует несколько интерфейсов, в качестве типа параметра или типа возвращаемого значения члена можно использовать любой из этих интерфейсов. Например, можно передавать или возвращать **словарь&lt;int, string&gt; ** (**Dictionary (Of Integer, String)** в Visual Basic) как **IDictionary&lt;int, string&gt;**, **IReadOnlyDictionary&lt;int, string &gt; **, или **IEnumerable&lt;System.Collections.Generic.KeyValuePair&lt;TKey, TValue&gt;**.

> [!IMPORTANT]
> JavaScript используется интерфейс, занимающий первую позицию в списке интерфейсов, реализуемых управляемым типом. Например, если вы вернете **словарь&lt;int, string&gt; ** код JavaScript, он отображается как **IDictionary&lt;int, string&gt; ** независимо от того, какой интерфейс указан в качестве возвращаемого типа. Это означает, что если первый интерфейс не включает член, который отображается в последующих интерфейсах, этот член не будет видимым в JavaScript.

В среде выполнения Windows **IMap&lt;K, V&gt; ** и **IMapView&lt;K, V&gt; ** осуществляется с помощью IKeyValuePair. При их передаче в управляемом коде, они отображаются в виде **IDictionary&lt;TKey, TValue&gt; ** и **IReadOnlyDictionary&lt;TKey, TValue&gt;**, поэтому вы используете **System.Collections.Generic.KeyValuePair&lt;TKey, TValue&gt; ** перечислить их.

Представление интерфейсов в управляемом коде влияет на представление типов, реализующих эти интерфейсы. Например, класс **PropertySet** реализует **IMap&lt;K, V&gt;**, который представлен в управляемом коде в качестве **IDictionary&lt;TKey, TValue&gt;**. **PropertySet** отображается как если бы он реализован **IDictionary&lt;TKey, TValue&gt; ** вместо **IMap&lt;K, V&gt;**, поэтому в управляемом коде его у метод **Add** , который ведет себя как метод **Add** в словарях .NET Framework. Он не отображается метод **Insert** у. Этот пример в разделе [Пошаговое руководство: создание простого компонента в C# или Visual Basic и его вызов из JavaScript](walkthrough-creating-a-simple-windows-runtime-component-and-calling-it-from-javascript.md).

## <a name="passing-managed-types-to-the-windows-runtime"></a>Передача управляемых типов в среду выполнения Windows

Как было описано в предыдущем разделе, некоторые типы среды выполнения Windows могут представляться типами .NET Framework в сигнатурах методов компонентов или в сигнатурах членов среды выполнения Windows при их использовании в интегрированной среде разработки. При передаче типов .NET Framework в эти члены или использовании их в качестве возвращаемых значений в членах компонентов они представляются на другой стороне как соответствующие типы среды выполнения Windows. Примеры последствий вызова компонента из кода JavaScript см. в разделе "Возвращение управляемых типов из компонента" статьи [Пошаговое руководство. Создание простого компонента в C# или Visual Basic и его вызов из кода JavaScript](walkthrough-creating-a-simple-windows-runtime-component-and-calling-it-from-javascript.md).

## <a name="overloaded-methods"></a>Перегруженные методы

В среде выполнения Windows методы можно перегружать. Однако если объявляется несколько перегруженных методов с одинаковым числом параметров, необходимо применить атрибут [**Windows.Foundation.Metadata.DefaultOverloadAttribute**](/uwp/api/windows.foundation.metadata.defaultoverloadattribute) только к одной из этих перегрузок. Это будет единственная перегрузка, которую можно вызывать из JavaScript. Например, в следующем коде перегрузка, которая принимает значение **int** (**Integer** в Visual Basic), является перегрузкой по умолчанию.

```csharp
public string OverloadExample(string s)
{
    return s;
}

[Windows.Foundation.Metadata.DefaultOverload()]
public int OverloadExample(int x)
{
    return x;
}
```

```vb
Public Function OverloadExample(ByVal s As String) As String
    Return s
End Function

<Windows.Foundation.Metadata.DefaultOverload> _
Public Function OverloadExample(ByVal x As Integer) As Integer
    Return x
End Function
```

> [ВАЖНЫЕ] JavaScript позволяет передавать в **OverloadExample**любое значение и приводит это значение к типу, который требуется параметром. Можно вызвать **OverloadExample** со «forty-two», «42» или 42.3, но все эти значения передаются перегруженному методу по умолчанию. В предыдущем примере перегруженный метод по умолчанию возвращает значения 0, 42 и 42 соответственно.

Нельзя применять атрибут e **DefaultOverloadAttribut**к конструкторам. Все конструкторы в классе должны иметь различное число параметров.

## <a name="implementing-istringable"></a>Реализация IStringable

Начиная с Windows 8.1, среда выполнения Windows включает интерфейс **IStringable** , единственный метод которого, **IStringable.ToString**, обеспечивает базовую поддержку форматирования, сравнимую с **методом Object.ToString**. Если вы все-таки решите реализовывать **IStringable** в общем управляемом типе, экспортированном в компонент среды выполнения Windows, применяются следующие ограничения:

-   Интерфейс **IStringable** можно определить только в отношении «класс реализует интерфейс», как в следующем коде на языке C#:

    ```cs
    public class NewClass : IStringable
    ```

    или в следующем коде Visual Basic:

    ```vb
    Public Class NewClass : Implements IStringable
    ```

-   Нельзя реализовать **IStringable** в интерфейсе.
-   Нельзя объявить параметр типа **IStringable**.
-   **IStringable** не может быть возвращаемым типом метода, свойства или поля.
-   Нельзя скрывать реализацию **IStringable** от базовых классов с помощью определения метода примерно следующего:

    ```cs
    public class NewClass : IStringable
    {
       public new string ToString()
       {
          return "New ToString in NewClass";
       }
    }
    ```

    Вместо этого реализация **IStringable.ToString** должна всегда переопределять реализацию базового класса. Реализацию **ToString** можно скрыть только путем вызова его в строго типизированном экземпляре класса.

> [!NOTE]
> В самых разных условиях вызовы из машинного кода в управляемый тип, реализующий **IStringable** или скрывающий свою реализацию **ToString** может привести к неожиданному поведению.

## <a name="asynchronous-operations"></a>Асинхронные операции

Чтобы реализовать асинхронный метод в компоненте, добавьте «Async» в конце имени метода и верните один из интерфейсов среды выполнения Windows, которые представляют асинхронные действия и операции: **IAsyncAction**, **IAsyncActionWithProgress&lt; TProgress&gt;**, **IAsyncOperation&lt;TResult&gt;**, или **IAsyncOperationWithProgress&lt;TResult, TProgress&gt;**.

Можно использовать задачи .NET Framework (класс [**задачи**](/dotnet/api/system.threading.tasks.task) и универсальный [**задачи&lt;TResult&gt; **](/dotnet/api/system.threading.tasks.task-1) класс) для реализации асинхронных методов. Необходимо вернуть задачу, которая представляет текущую операцию, например задачу, возвращаемую из асинхронного метода, написанные на C# или Visual Basic, или задачу, возвращаемую из метода [**Task.Run**](/dotnet/api/system.threading.tasks.task.run) . При создании задачи с помощью конструктора необходимо перед возвращением задачи вызвать метод [Task.Start](/dotnet/api/system.threading.tasks.task.start).

Метод, который использует `await` (`Await` в Visual Basic) требует `async` ключевое слово (`Async` в Visual Basic). Если такой метод из компонента среды выполнения Windows предоставляется, применяются `async` ключевое слово делегату, передаваемому в метод **Run** .

Для асинхронных действий и операций, которые не поддерживают отчеты об отмене или ходе выполнения, можно использовать метод расширения [WindowsRuntimeSystemExtensions.AsAsyncAction](https://msdn.microsoft.com/library/system.windowsruntimesystemextensions.asasyncaction.aspx) или [AsAsyncOperation&lt;TResult&gt;](https://msdn.microsoft.com/library/hh779745.aspx), чтобы заключить задачу в соответствующий интерфейс. Например, следующий код реализует асинхронный метод с помощью **Task.Run&lt;TResult&gt; ** запускающего задачу. **AsAsyncOperation&lt;TResult&gt; ** метод расширения Возвращает задачу в качестве асинхронной операции среды выполнения Windows.

```csharp
public static IAsyncOperation<IList<string>> DownloadAsStringsAsync(string id)
{
    return Task.Run<IList<string>>(async () =>
    {
        var data = await DownloadDataAsync(id);
        return ExtractStrings(data);
    }).AsAsyncOperation();
}
```

```vb
Public Shared Function DownloadAsStringsAsync(ByVal id As String) _
     As IAsyncOperation(Of IList(Of String))

    Return Task.Run(Of IList(Of String))(
        Async Function()
            Dim data = Await DownloadDataAsync(id)
            Return ExtractStrings(data)
        End Function).AsAsyncOperation()
End Function
```

Следующий код JavaScript показывает, как можно вызывать метод с помощью объекта [**WinJS.Promise**](https://msdn.microsoft.com/library/windows/apps/br211867.aspx) . Функция, которая передается методу then, выполняется при завершении асинхронного вызова. Параметр stringList содержит список строк, возвращаемый методом **DownloadAsStringAsync** , а функция выполняет необходимые.

```javascript
function asyncExample(id) {

    var result = SampleComponent.Example.downloadAsStringAsync(id).then(
        function (stringList) {
            // Place code that uses the returned list of strings here.
        });
}
```

Для асинхронных действий и операций, которые поддерживают отмены и хода выполнения используйте класса [**AsyncInfo**](/dotnet/api/system.runtime.interopservices.windowsruntime) создавать запущенную задачу и реализовывать отмены и хода выполнения функции отмены и хода выполнения отчеты о функций соответствующего интерфейса среды выполнения Windows. Пример, в котором реализована поддержка отчетов об отмене и ходе выполнения, см. в статье [Пошаговое руководство. Создание простого компонента в C# или Visual Basic и его вызов из кода JavaScript](walkthrough-creating-a-simple-windows-runtime-component-and-calling-it-from-javascript.md).

Обратите внимание, что можно использовать методы класса **AsyncInfo** , даже если асинхронный метод не поддерживает отмены или отчет о состоянии. При использовании лямбда-функции Visual Basic или анонимного метода C# не указывайте параметры токена и [**IProgress&lt;T&gt; **](https://msdn.microsoft.com/library/hh138298.aspx) интерфейса. При использовании лямбда-функции C# указывайте параметр токена, но игнорируйте его. Предыдущий пример, в котором используется AsAsyncOperation&lt;TResult&gt; метод, выглядит следующим образом, при использовании [**AsyncInfo.Run&lt;TResult&gt;(Func&lt;CancellationToken, Task&lt;TResult&gt;**](https://msdn.microsoft.com/library/hh779740.aspx)) метод Вместо этого перегрузки.

```csharp
public static IAsyncOperation<IList<string>> DownloadAsStringsAsync(string id)
{
    return AsyncInfo.Run<IList<string>>(async (token) =>
    {
        var data = await DownloadDataAsync(id);
        return ExtractStrings(data);
    });
}
```

```vb
Public Shared Function DownloadAsStringsAsync(ByVal id As String) _
    As IAsyncOperation(Of IList(Of String))

    Return AsyncInfo.Run(Of IList(Of String))(
        Async Function()
            Dim data = Await DownloadDataAsync(id)
            Return ExtractStrings(data)
        End Function)
End Function
```

При создании асинхронного метода, отмене и ходе, попробуйте добавить перегруженные параметров для токена отмены или **IProgress&lt;T&gt; ** интерфейса.

## <a name="throwing-exceptions"></a>Создание исключений

Можно создавать исключения любого типа, включенного в .NET для приложений для Windows. В компоненте среды выполнения Windows нельзя объявлять собственные открытые типы исключений, но можно объявлять и создавать неоткрытые типы.

Если компонент не обрабатывает исключение, соответствующее исключение передается в код, который вызвал данный компонент. Представление этого исключения на вызывающей стороне зависит от того, каким образом язык вызова поддерживает среду выполнения Windows.

-   В JavaScript исключение представляется объектом, в котором сообщение исключения заменено на трассировку стека. При отладке приложения в Visual Studio исходный текст сообщения можно видеть в диалоговом окне исключений отладчика с пометкой "Сведения WinRT". Исходный текст сообщения недоступен из кода JavaScript.

    > **Совет**.В настоящее время трассировка стека содержит тип управляемого исключения, однако анализировать трассировку для определения типа исключения не рекомендуется. Вместо этого лучше использовать значение HRESULT, описанное далее в этом разделе.

-   В C++ исключение является исключением платформы. Если свойство HResult управляемого исключения может быть сопоставлено значению HRESULT для конкретного исключения платформы, используется конкретное исключение; в противном случае [**Platform::COMException**](https://msdn.microsoft.com/library/windows/apps/xaml/hh710414.aspx) возникает исключение. Текст сообщения управляемого исключения недоступен в коде C++. Если было создано конкретное исключение платформы, отображается текст сообщения по умолчанию для этого типа исключений; в противном случае текст не отображается. См. статью [Исключения (C++/CX)](https://msdn.microsoft.com/library/windows/apps/xaml/hh699896.aspx).
-   В C# и Visual Basic используется обычное управляемое исключение.

При создании в компоненте исключения можно упростить его обработку в вызывающем коде JavaScript или C++, если создавать исключения неоткрытых типов, свойство HResult которых связано с конкретным компонентом. HRESULT доступен вызывающему объекту через свойство number объекта исключения JavaScript и C++ вызывающему объекту через свойство [**COMException::HResult**](https://msdn.microsoft.com/library/windows/apps/xaml/hh710415.aspx) .

> [!NOTE]
> Используйте отрицательное значение для вашего HRESULT. Положительное значение интерпретируется как успех, и исключение в вызывающем коде JavaScript или C++ не создается.

## <a name="declaring-and-raising-events"></a>Объявление и вызов событий

При объявлении типа, в котором будут храниться данные вашего события, создавайте класс, производный от Object, а не от EventArgs, поскольку EventArgs не является типом среды выполнения Windows. Используйте [**EventHandler&lt;TEventArgs&gt; **](https://msdn.microsoft.com/library/db0etb8x.aspx) в качестве типа события и тип аргумента события в качестве аргумента универсального типа. Вызов событий осуществляется так же, как и в приложениях .NET Framework.

Когда компонент среды выполнения Windows вызывается из JavaScript или C++, событие выполняется в соответствии с шаблоном событий среды выполнения Windows, ожидаемым этими языками. Когда компонент вызывается из C# или Visual Basic, событие представляется обычным событием .NET Framework. Пример приведен в статье [Пошаговое руководство. Создание простого компонента в C# или Visual Basic и его вызов из кода JavaScript]().

При реализации пользовательских методов доступа к событиям (событие объявляется с ключевым словом **Custom** в Visual Basic) в такой реализации необходимо соблюдать шаблон событий среды выполнения Windows. См. раздел [Пользовательские события и методы доступа к событиям в компонентах среды выполнения Windows](custom-events-and-event-accessors-in-windows-runtime-components.md). Обратите внимание, что при обработке события в коде C# или Visual Basic оно все равно представляется обычным событием .NET Framework.

## <a name="next-steps"></a>Дальнейшие действия

После создания компонента среды выполнения Windows для собственного использования вы можете обнаружить, что заключенная в нем функциональность будет полезна и другим разработчикам. Есть два способа упаковки компонента для распространения среди других разработчиков. См. раздел [Распространение управляемого компонента среды выполнения Windows](https://msdn.microsoft.com/library/jj614475.aspx).

Дополнительные сведения о функциях языка Visual Basic и C# и поддержке платформой .NET Framework среды выполнения Windows можно найти в статье [Справочник по языкам Visual Basic и C#](https://msdn.microsoft.com/library/windows/apps/xaml/br212458.aspx).

## <a name="related-topics"></a>Еще по теме
* [Платформа .NET для приложений UWP](https://msdn.microsoft.com/library/windows/apps/mt185501)
* [Пошаговое руководство. Создание простого компонента среды выполнения Windows и его вызов из кода JavaScript](walkthrough-creating-a-simple-windows-runtime-component-and-calling-it-from-javascript.md)
