---
author: msatranjr
title: "Создание простого компонента среды выполнения Windows и его вызов из JavaScript"
description: "В этом пошаговом руководстве описан процесс использования платформы .NET Framework с Visual Basic или C# для создания собственных типов среды выполнения Windows, упакованных в компонент среды выполнения Windows, а также процедура вызова компонента из универсального приложения для Windows, собранного для Windows с использованием JavaScript."
ms.assetid: 1565D86C-BF89-4EF3-81FE-35367DB8D671
ms.sourcegitcommit: 4c32b134c704fa0e4534bc4ba8d045e671c89442
ms.openlocfilehash: c521061d9fdd3eb2c25e3072182fb1d7823f13ba

---

# Пошаговое руководство: создание простого компонента среды выполнения Windows и его вызов из JavaScript


\[ Обновлено для приложений UWP в Windows 10. Статьи о Windows 8.x см. в [архиве](http://go.microsoft.com/fwlink/p/?linkid=619132) \]


В этом пошаговом руководстве описан процесс использования платформы .NET Framework с Visual Basic или C# для создания собственных типов среды выполнения Windows, упакованных в компонент среды выполнения Windows, а также процедура вызова компонента из универсального приложения для Windows, собранного для Windows с использованием JavaScript.

Visual Studio позволяет легко добавлять в приложение компоненты среды выполнения Windows, написанные на языке C# или Visual Basic, и создавать типы среды выполнения Windows, которые можно вызывать из JavaScript. Внутри приложения типы среды выполнения Windows могут использовать любые функции .NET Framework, которые разрешены в универсальном приложении для Windows. (Дополнительные сведения см. в статье [Создание компонентов среды выполнения Windows в C# и Visual Basic](creating-windows-runtime-components-in-csharp-and-visual-basic.md) и [Общие сведения о .NET для приложений Магазина Windows](https://msdn.microsoft.com/library/windows/apps/xaml/mt185501.aspx).) Снаружи члены ваших типов могут предоставлять доступ только к параметрам и возвращаемым значениями типов среды выполнения Windows. При сборке решения Visual Studio создает проект компонента среды выполнения Windows платформы .NET Framework, а затем выполняет шаг сборки, создающий файл метаданных Windows (WinMD). Это компонент среды выполнения Windows, который Visual Studio включает в приложение.

> **Примечание.**  Платформа .NET Framework автоматически сопоставляет некоторые часто используемые типы .NET Framework, такие как простые типы данных и коллекций, с их эквивалентами среды выполнения Windows. Эти типы .NET Framework можно использовать в общедоступном интерфейсе компонента среды выполнения Windows, и они будут представляться пользователям компонента в виде соответствующих типов среды выполнения Windows. См. статью [Создание компонентов среды выполнения Windows в C# и Visual Basic](creating-windows-runtime-components-in-csharp-and-visual-basic.md).

В данном пошаговом руководстве рассмотрены следующие задачи. После завершения первого раздела, в котором описывается процесс настройки приложения Windows для работы с кодом JavaScript, остальные разделы можно проходить в любом порядке.

## Предварительные требования:

-   Windows 10
-   Среда Microsoft Visual Studio 2015 или среда Microsoft Visual Studio Community 2015

## Создание простого класса среды выполнения Windows


В этом разделе описан процесс создания универсального приложения для Windows, разработанного для Windows с использованием JavaScript, а также процедура добавления проекта компонента среды выполнения Windows на языке Visual Basic или C#. В разделе показано, как определять управляемые типы среды выполнения Windows, создавать экземпляры этих типов в коде JavaScript, а также вызывать статические члены и члены экземпляров. Пример приложения намеренно сделан визуально скромным, чтобы не отвлекать внимание читателя от компонента. При желании вы можете сделать его более привлекательным.

1.  В Visual Studio создайте новый проект JavaScript: в строке меню выберите **Файл, Создать, Проект**. В разделе **Установленные шаблоны** диалогового окна **Создать проект** выберите **JavaScript**, затем выберите **Windows**, после чего выберите **Универсальные**. (Если доступ к Windows ограничен, убедитесь, что используется Windows 8 или Windows более поздней версии). Выберите шаблон **Пустое приложение** и в качестве имени проекта введите SampleApp.
2.  Создайте проект компонента: в Обозревателе решений откройте контекстное меню решения SampleApp и выберите **Добавить**, а затем выберите **Создать проект**, чтобы добавить в решение новый проект C# или Visual Basic. В разделе **Установленные шаблоны** диалогового окна **Добавить новый проект** выберите **Visual Basic** или **Visual C#**, после чего выберите **Windows**, а затем выберите **Универсальные**. Выберите шаблон **Компонент среды выполнения Windows**, а в качестве имени проекта укажите **SampleComponent**.
3.  Измените имя класса на **Example**. Обратите внимание, что по умолчанию класс помечен модификатором **public sealed** (**Public NotInheritable** в Visual Basic). Все классы среды выполнения Windows, доступ к которым предоставляется из компонента, должны быть запечатанными.
4.  Добавьте в класс два простых члена — метод **static** (метод **Shared** в Visual Basic) и свойство экземпляра:

    > [!div class="tabbedCodeSnippets"]
    > ```cpp
    > namespace SampleComponent
    > {
    >     public sealed class Example
    >     {
    >         public static string GetAnswer()
    >         {
    >             return "The answer is 42.";
    >         }
    >
    >         public int SampleProperty { get; set; }
    >     }
    > }
    > ```
    > ```vb
    > Public NotInheritable Class Example
    >     Public Shared Function GetAnswer() As String
    >         Return "The answer is 42."
    >     End Function
    >
    >     Public Property SampleProperty As Integer
    > End Class
    > ```

5.  [!div class="tabbedCodeSnippets"]
6.  Необязательно: чтобы разрешить функции IntelliSense для добавляемых членов, в Обозревателе решений откройте контекстное меню проекта SampleComponent и выберите команду **Сборка**. В Обозревателе решений в проекте JavaScript откройте контекстное меню для элемента **Ссылки** и выберите **Добавить ссылку**, чтобы открыть **Диспетчер ссылок**. Выберите **Проекты**, а затем выберите **Решение**.

## Установите флажок для проекта SampleComponent и нажмите кнопку **ОК**, чтобы добавить ссылку.


Вызов компонента из JavaScript Для использования типа среды выполнения Windows в коде JavaScript добавьте следующий код после анонимной функции в конце файла default.js (в папке js проекта), предоставляемого шаблоном Visual Studio.

```javascript
var ex;

function basics1() {
   document.getElementById('output').innerHTML =
        SampleComponent.Example.getAnswer();

    ex = new SampleComponent.Example();

   document.getElementById('output').innerHTML += "<br/>" +
       ex.sampleProperty;

}

function basics2() {
    ex.sampleProperty += 1;
    document.getElementById('output').innerHTML += "<br/>" +
        ex.sampleProperty;
}
```

Этот код должен размещаться после обработчика событий app.oncheckpoint и перед вызовом app.start. Обратите внимание, что регистр первой буквы имени каждого члена меняется с верхнего на нижний. Это преобразование является частью возможностей JavaScript, обеспечивающих естественное использование среды выполнения Windows. В именах пространств имен и классов используется нотация в стиле Pascal. Для имен членов применяется «верблюжий» стиль, кроме имен событий, которые полностью записываются в нижнем регистре. См. раздел [Использование среды выполнения Windows в JavaScript](https://msdn.microsoft.com/library/hh710230.aspx). Правила использования «верблюжьего» стиля могут показаться сложными. Последовательность начальных прописных букв обычно переводится в нижний регистр, но если за тремя прописными буквами следует строчная буква, в нижний регистр переводятся только первые две из них: например, имя члена IDStringKind заменяется на idStringKind.

В Visual Studio можно выполнить сборку проекта компонента среды выполнения Windows, чтобы затем с помощью IntelliSense проверять правильность регистра букв в проекте JavaScript. Подобным образом в .NET Framework имеются возможности, обеспечивающие естественное использование среды выполнения Windows в управляемом коде.

## Эти вопросы рассматриваются в последующих подразделах этой статьи, а также в статьях [Создание компонентов среды выполнения Windows в C# и Visual Basic](creating-windows-runtime-components-in-csharp-and-visual-basic.md) и [ Поддержка приложений Магазина Windows и среды выполнения Windows в .NET Framework](https://msdn.microsoft.com/library/hh694558.aspx).


Создание простого пользовательского интерфейса В проекте JavaScript откройте файл default.html и измените текст блока body, как показано в следующем фрагменте кода.

> Данный код включает полный набор элементов управления для примера приложения, а также задает имена функций для событий нажатия кнопок.

```html
<body>
            <div id="buttons">
            <button id="button1" >Basics 1</button>
            <button id="button2" >Basics 2</button>

            <button id="runtimeButton1">Runtime 1</button>
            <button id="runtimeButton2">Runtime 2</button>

            <button id="returnsButton1">Returns 1</button>
            <button id="returnsButton2">Returns 2</button>

            <button id="events1Button">Events 1</button>

            <button id="btnAsync">Async</button>
            <button id="btnCancel" disabled="disabled">Cancel Async</button>
            <progress id="primeProg" value="25" max="100" style="color: yellow;"></progress>
        </div>
        <div id="output">
        </div>
</body>
```

**Примечание.**  При первом запуске приложения поддерживаются только кнопки Basics1 и Basics2. В проекте JavaScript в папке css откройте файл default.css.

```css
body
{
    -ms-grid-columns: 1fr;
    -ms-grid-rows: 1fr 14fr;
    display: -ms-grid;
}

#buttons {
    -ms-grid-rows: 1fr;
    -ms-grid-columns: auto;
    -ms-grid-row-align: start;
}
#output {
    -ms-grid-row: 2;
    -ms-grid-column: 1;
}
```

Измените раздел body, как показано ниже, и добавьте стили, определяющие расположение кнопок и выходного текста. Теперь добавьте код регистрации прослушивателя событий, добавив предложение then в вызов processAll функции app.onactivated в файле default.js.

```javascript
args.setPromise(WinJS.UI.processAll().then(function () {
    var button1 = document.getElementById("button1");
    button1.addEventListener("click", basics1, false);
    var button2 = document.getElementById("button2");
    button2.addEventListener("click", basics2, false);
}));
```

Замените существующую строку кода, которая вызывает setPromise, и измените ее на следующий код: Этот способ добавления событий в элементы управления HTML удобнее непосредственного добавления обработчика событий нажатия кнопки в HTML.

## См. раздел [Создание приложения «Hello World» (JavaScript)](https://msdn.microsoft.com/library/windows/apps/mt280216).


Сборка и запуск приложения

Перед выполнением сборки измените целевую платформу для всех проектов на ARM, x64 или x86 с учетом характеристик своего компьютера. Чтобы собрать и запустить решение, нажмите клавишу F5.

(Если появляется сообщение об ошибке времени выполнения, указывающее, что компонент SampleComponent не определен, это означает, что отсутствует ссылка на проект библиотеки классов). Visual Studio сначала компилирует библиотеку классов, а затем выполняет задачу MSBuild, которая запускает [Winmdexp.exe (средство экспорта метаданных среды выполнения Windows)](https://msdn.microsoft.com/library/hh925576.aspx) для создания компонента среды выполнения Windows. Компонент добавляется в файл WinMD, содержащий управляемый код и метаданные Windows, описывающие этот код. Программа WinMdExp.exe формирует сообщения об ошибках сборки, если код компонента среды выполнения Windows некорректен, и эти сообщения об ошибках отображаются в интегрированной среде разработки Visual Studio.

Visual Studio добавляет компонент в пакет приложения (файл APPX) для универсального приложения для Windows и создает соответствующий манифест. Нажмите кнопку Basics 1, чтобы показать возвращаемое статическим методом GetAnswer значение в области вывода, создать экземпляр класса Example и отобразить в области вывода значение свойства SampleProperty этого экземпляра.

``` syntax
"The answer is 42."
0
```

Выходные данные приведены здесь: Нажмите кнопку Basics 2, чтобы увеличить значение свойства SampleProperty и показать новое значение в области вывода. Простые типы, такие как строки и числа, можно использовать в качестве типов параметров и возвращаемых значений, а также передавать между управляемым кодом и JavaScript.

> Поскольку в языке JavaScript числа хранятся в формате с плавающей запятой двойной точности, они преобразуются в числовые типы .NET Framework. **Примечание.**  По умолчанию точки останова можно устанавливать только в коде JavaScript.

 

Информацию об отладке кода Visual Basic или C# см. в статье «Создание компонентов среды выполнения Windows в C# и Visual Basic».

## Чтобы остановить отладку и закрыть приложение, перейдите из приложения в Visual Studio и нажмите сочетание клавиш Shift+F5.


Использование среды выполнения Windows в коде JavaScript и в управляемом коде Среду выполнения Windows можно вызвать из кода JavaScript или из управляемого кода. Объекты среды выполнения Windows можно передавать между двумя видами кода, а события можно обрабатывать на каждой из сторон. Тем не менее способы использования типов среды выполнения Windows в этих двух средах несколько различаются, поскольку JavaScript и .NET Framework поддерживают среду выполнения Windows по-разному. В следующем примере эти различия показаны с использованием класса [Windows.Foundation.Collections.PropertySet](https://msdn.microsoft.com/library/windows/apps/windows.foundation.collections.propertyset.aspx). В этом примере создается экземпляр коллекции PropertySet в управляемом коде и регистрируется обработчик событий для отслеживания изменений в коллекции. Затем добавляется код JavaScript, который получает коллекцию, регистрирует собственный обработчик событий и использует коллекцию.

> Наконец, в код добавляется метод, который вносит изменения в коллекцию из управляемого кода и показывает, как JavaScript обрабатывает управляемое исключение. **Внимание!**  В этом примере событие возникает в потоке пользовательского интерфейса. Если событие вызывается в фоновом потоке, например, в асинхронном вызове, необходимо выполнить некоторые дополнительные действия, чтобы код JavaScript мог обработать это событие.

 

Дополнительные сведения см. в разделе [Создание событий в компонентах среды выполнения Windows](raising-events-in-windows-runtime-components.md). В проект SampleComponent добавьте новый класс **public sealed** (класс **Public NotInheritable** в Visual Basic) с именем PropertySetStats. Класс создает программу-оболочку для коллекции PropertySet и обрабатывает ее событие MapChanged. Обработчик событий отслеживает число изменений каждого типа, а метод DisplayStats создает отчет в формате HTML.

> [!div class="tabbedCodeSnippets"]
> ```csharp
> using Windows.Foundation.Collections;
>
> namespace SampleComponent
> {
>     public sealed class PropertySetStats
>     {
>         private PropertySet _ps;
>         public PropertySetStats()
>         {
>             _ps = new PropertySet();
>             _ps.MapChanged += this.MapChangedHandler;
>         }
>
>         public PropertySet PropertySet { get { return _ps; } }
>
>         int[] counts = { 0, 0, 0, 0 };
>         private void MapChangedHandler(IObservableMap<string, object> sender,
>             IMapChangedEventArgs<string> args)
>         {
>             counts[(int)args.CollectionChange] += 1;
>         }
>
>         public string DisplayStats()
>         {
>             StringBuilder report = new StringBuilder("<br/>Number of changes:<ul>");
>             for (int i = 0; i < counts.Length; i++)
>             {
>                 report.Append("<li>" + (CollectionChange)i + ": " + counts[i] + "</li>");
>             }
>             return report.ToString() + "</ul>";
>         }
>     }
> }
> ```
> ```vb
> Imports System.Text
>
> Public NotInheritable Class PropertySetStats
>     Private _ps As PropertySet
>     Public Sub New()
>         _ps = New PropertySet()
>         AddHandler _ps.MapChanged, AddressOf Me.MapChangedHandler
>     End Sub
>
>     Public ReadOnly Property PropertySet As PropertySet
>         Get
>             Return _ps
>         End Get
>     End Property
>
>     Dim counts() As Integer = {0, 0, 0, 0}
>     Private Sub MapChangedHandler(ByVal sender As IObservableMap(Of String, Object),
>         ByVal args As IMapChangedEventArgs(Of String))
>
>         counts(CInt(args.CollectionChange)) += 1
>     End Sub
>
>     Public Function DisplayStats() As String
>         Dim report As New StringBuilder("<br/>Number of changes:<ul>")
>         For i As Integer = 0 To counts.Length - 1
>             report.Append("<li>" & CType(i, CollectionChange).ToString() &
>                           ": " & counts(i) & "</li>")
>         Next
>         Return report.ToString() & "</ul>"
>     End Function
> End Class
> ```

Обратите внимание на дополнительную директиву **using** (директива **Imports** в Visual Basic). Обратите внимание, что ее нужно добавить к имеющимся директивам **using**, а не вместо них.

[!div class="tabbedCodeSnippets"] Обработчик событий работает в соответствии со стандартным шаблоном событий .NET Framework, за тем исключением, что отправитель события (в данном случае это объект PropertySet) приводится к интерфейсу IObservableMap&lt;string, object&gt; (IObservableMap(Of String, Object) в Visual Basic), который является реализацией интерфейса среды выполнения Windows [IObservableMap&lt;K, V&gt;](https://msdn.microsoft.com/library/windows/apps/br226050.aspx). Кроме того, аргументы события представляются в качестве интерфейса, а не в качестве объекта. В файл default.js добавьте функцию Runtime1, как показано ниже.

```javascript
var propertysetstats;

function runtime1() {
    document.getElementById('output').innerHTML = "";

    propertysetstats = new SampleComponent.PropertySetStats();
    var propertyset = propertysetstats.propertySet;

    propertyset.addEventListener("mapchanged", onMapChanged);

    propertyset.insert("FirstProperty", "First property value");
    propertyset.insert("SuperfluousProperty", "Unnecessary property value");
    propertyset.insert("AnotherProperty", "A property value");

    propertyset.insert("SuperfluousProperty", "Altered property value")
    propertyset.remove("SuperfluousProperty");

    document.getElementById('output').innerHTML +=
        propertysetstats.displayStats();
}

function onMapChanged(change) {
    var result
    switch (change.collectionChange) {
        case Windows.Foundation.Collections.CollectionChange.reset:
            result = "All properties cleared";
            break;
        case Windows.Foundation.Collections.CollectionChange.itemInserted:
            result = "Inserted " + change.key + ": '" +
                change.target.lookup(change.key) + "'";
            break;
        case Windows.Foundation.Collections.CollectionChange.itemRemoved:
            result = "Removed " + change.key;
            break;
        case Windows.Foundation.Collections.CollectionChange.itemChanged:
            result = "Changed " + change.key + " to '" +
                change.target.lookup(change.key) + "'";
            break;
        default:
            break;
     }

     document.getElementById('output').innerHTML +=
         "<br/>" + result;
}
```

Этот код создает объект PropertySetStats, получает коллекцию PropertySet и добавляет собственный обработчик событий (функция onMapChanged), чтобы обрабатывать событие MapChanged. После внесения изменений в коллекцию runtime1 вызывает метод DisplayStats, чтобы показать сводную информацию о типах изменений. Способ обработки событий среды выполнения Windows в языке JavaScript значительно отличается от способа их обработки в коде .NET Framework. Обработчик событий JavaScript принимает только один аргумент.

При просмотре этого объекта в отладчике Visual Studio первое свойство задает отправителя. Члены интерфейса аргумента события также отображаются в этом объекте. Чтобы запустить приложение, нажмите клавишу F5.

Если класс не запечатан, появляется сообщение об ошибке «Экспорт незапечатанных типов не поддерживается. Пометьте тип "SampleComponent.Example" как запечатанный». Нажмите кнопку **Runtime 1**.

Обработчик событий будет отображать изменения при добавлении и изменении элементов, а после завершения будет вызван метод DisplayStats, чтобы показать сводные данные.

> [!div class="tabbedCodeSnippets"]
> ```csharp
> public void AddMore()
> {
>     _ps.Add("NewProperty", "New property value");
>     _ps.Add("AnotherProperty", "A property value");
> }
> ```
> ```vb
> Public Sub AddMore()
>     _ps.Add("NewProperty", "New property value")
>     _ps.Add("AnotherProperty", "A property value")
> End Sub
> ```

Чтобы остановить отладку и закрыть приложение, вернитесь в Visual Studio и нажмите сочетание клавиш Shift+F5. Чтобы из управляемого кода добавить в коллекцию PropertySet еще два элемента, добавьте в класс PropertySetStats следующий код: [!div class="tabbedCodeSnippets"] Этот код подчеркивает еще одно различие в использовании типов среды выполнения Windows в двух средах. Если вы будете самостоятельно вводить этот код, вы заметите, что IntelliSense не показывает метод insert, который использовался в коде JavaScript. Вместо этого отображается метод Add, который часто используется в коллекциях .NET Framework. Это связано с тем, что в среде выполнения Windows и .NET Framework часто используемые интерфейсы коллекций имеют различные имена, но одинаковые функции.

При использовании этих интерфейсов в управляемом коде они представляются в виде эквивалентов .NET Framework.

```javascript
function runtime2() {
   try {
      propertysetstats.addMore();
    }
   catch(ex) {
       document.getElementById('output').innerHTML +=
          "<br/><b>" + ex + "<br/>";
   }

   document.getElementById('output').innerHTML +=
       propertysetstats.displayStats();
}
```

Эти вопросы рассматриваются в разделе [Создание компонентов среды выполнения Windows в C# и Visual Basic](creating-windows-runtime-components-in-csharp-and-visual-basic.md).

```javascript
var runtimeButton1 = document.getElementById("runtimeButton1");
runtimeButton1.addEventListener("click", runtime1, false);
var runtimeButton2 = document.getElementById("runtimeButton2");
runtimeButton2.addEventListener("click", runtime2, false);
```

При использовании тех же интерфейсов в коде JavaScript единственное отличие от среды выполнения Windows будет состоять в том, что буквы в верхнем регистре в начале имен членов будут преобразованы в нижний регистр. Наконец, чтобы вызвать метод AddMore с обработкой исключений, добавьте в файл default.js функцию runtime2. Еще раз добавьте код регистрации обработчика событий. Чтобы запустить приложение, нажмите клавишу F5. Нажмите кнопку **Runtime 1**, а затем — кнопку **Runtime 2**. Обработчик событий JavaScript сообщит о первом изменении коллекции.

> Но второе изменение содержит повторяющийся ключ. Пользователи словарей .NET Framework ожидают, что при этом метод Add создаст исключение; именно это и произойдет. JavaScript обрабатывает исключение .NET Framework.

**Примечание.**  Сообщение исключения не может отображаться с помощью кода JavaScript. Текст сообщения заменяется данными трассировки стека.

## Дополнительные сведения см. в разделе «Создание исключений» в статье «Создание компонентов среды выполнения Windows в C# и Visual Basic».


Напротив, когда код JavaScript вызывает метод insert с повторяющимся ключом, значение элемента изменяется. Эта разница в поведении возникает из-за различий в способах, с помощью которых JavaScript и .NET Framework поддерживают среду выполнения Windows, как описано в статье [Создание компонентов среды выполнения Windows в C# и Visual Basic](creating-windows-runtime-components-in-csharp-and-visual-basic.md). Возвращение управляемых типов из компонента Как отмечалось ранее, собственные типы среды выполнения Windows можно свободно передавать в любом направлении между кодом JavaScript и кодом C# или Visual Basic.

В большинстве случаев имена типов и членов в обоих случаях совпадают (кроме имен членов, которые в JavaScript начинаются со строчной буквы). Однако в предыдущем разделе класс PropertySet имел другие члены в управляемом коде. (Например, в коде JavaScript мы вызывали метод insert, а в коде .NET Framework — метод Add). В этом разделе более подробно рассматривается влияние этих различий на типы .NET Framework, передаваемые в код JavaScript.

> [!div class="tabbedCodeSnippets"]
> ```csharp
> public static IDictionary<int, string> GetMapOfNames()
> {
>     Dictionary<int, string> retval = new Dictionary<int, string>();
>     retval.Add(1, "one");
>     retval.Add(2, "two");
>     retval.Add(3, "three");
>     retval.Add(42, "forty-two");
>     retval.Add(100, "one hundred");
>     return retval;
> }
> ```
> ```vb
> Public Shared Function GetMapOfNames() As IDictionary(Of Integer, String)
>     Dim retval As New Dictionary(Of Integer, String)
>     retval.Add(1, "one")
>     retval.Add(2, "two")
>     retval.Add(3, "three")
>     retval.Add(42, "forty-two")
>     retval.Add(100, "one hundred")
>     Return retval
> End Function
> ```

Помимо возврата типов среды выполнения Windows, созданных в разрабатываемом компоненте или переданных в него из JavaScript, можно также возвращать в JavaScript управляемые типы, созданные в управляемом коде, как если бы они были соответствующими типами среды выполнения Windows. Даже в первом, простом, примере класса среды выполнения параметры и возвращаемые значения имели простые типы Visual Basic и C#, которые являются типами .NET Framework. Чтобы продемонстрировать это для коллекции, добавьте в класс Example следующий код, чтобы создать метод, возвращающий универсальный словарь строк с целочисленными индексами:

[!div class="tabbedCodeSnippets"] Обратите внимание, что словарь должен возвращаться в форме интерфейса, реализуемого классом [Dictionary&lt;TKey, TValue&gt;](https://msdn.microsoft.com/library/xfhwa508.aspx) и сопоставляемого с интерфейсом среды выполнения Windows. В данном случае это интерфейс IDictionary&lt;int, string&gt; (IDictionary(Of Integer, String) в Visual Basic).

 

Если тип среды выполнения Windows IMap&lt;int, string&gt; передается управляемому коду, он представляется в виде IDictionary&lt;int, string&gt;, а при передаче управляемого типа в код JavaScript верно обратное.

```javascript
var names;

function returns1() {
    names = SampleComponent.Example.getMapOfNames();
    document.getElementById('output').innerHTML = showMap(names);
}

var ct = 7;

function returns2() {
    if (!names.hasKey(17)) {
        names.insert(43, "forty-three");
        names.insert(17, "seventeen");
    }
    else {
        var err = names.insert("7", ct++);
        names.insert("forty", "forty");
    }
    document.getElementById('output').innerHTML = showMap(names);
}

function showMap(map) {
    var item = map.first();
    var retval = "<ul>";

    for (var i = 0, len = map.size; i < len; i++) {
        retval += "<li>" + item.current.key + ": " + item.current.value + "</li>";
        item.moveNext();
    }
    return retval + "</ul>";
}
```

**Внимание!**  В коде JavaScript используется интерфейс, занимающий первую позицию в списке интерфейсов, если управляемый тип реализует несколько интерфейсов.

```javascript
var returnsButton1 = document.getElementById("returnsButton1");
returnsButton1.addEventListener("click", returns1, false);
var returnsButton2 = document.getElementById("returnsButton2");
returnsButton2.addEventListener("click", returns2, false);
```

Например, если в код JavaScript возвращается тип Dictionary&lt;int, string&gt;, он отображается как IDictionary&lt;int, string&gt; независимо от того, какой интерфейс указан в качестве типа возвращаемого значения. Это означает, что если первый интерфейс не включает член, который отображается в последующих интерфейсах, этот член не будет видимым в JavaScript. Чтобы проверить новый метод и использовать словарь, добавьте в файл default.js функции returns1 и returns2: Добавьте код регистрации события к тому же блоку then, к которому был добавлен другой код регистрации события: Этот код JavaScript позволяет продемонстрировать несколько интересных особенностей. Во-первых, он включает функцию showMap для отображения содержимого словаря в формате HTML.

В коде showMap обратите внимание на шаблон итерации.

В .NET Framework отсутствует метод First универсального интерфейса IDictionary, а размер возвращается свойством Count, а не методом Size. В JavaScript IDictionary&lt;int, string&gt; представляется типом среды выполнения Windows IMap&lt;int, string&gt;. (См. описание интерфейса [IMap&lt;K,V&gt;](https://msdn.microsoft.com/library/windows/apps/br226042.aspx)). В функции returns2, как и в предыдущих примерах, JavaScript вызывает метод Insert (insert на языке JavaScript) для добавления элементов в словарь. Чтобы запустить приложение, нажмите клавишу F5. Для создания и отображения начального содержимого словаря нажмите кнопку **Returns 1**.

Чтобы добавить в словарь еще две записи, нажмите кнопку **Returns 2**. Обратите внимание, что записи отображаются в порядке вставки, как и ожидалось для Dictionary&lt;TKey, TValue&gt;. Если требуется отсортировать их, можно вернуть SortedDictionary&lt;int, string&gt; из GetMapOfNames. (Класс PropertySet, используемый в предыдущих примерах, имеет иную внутреннюю структуру по сравнению с Dictionary&lt;TKey, TValue&gt;.) JavaScript не является строго типизированным языком, поэтому использование строго типизированных универсальных коллекций может приводить к неожиданным результатам. Еще раз нажмите кнопку **Returns 2**. JavaScript преобразует значение «7» в числовое значение 7, а хранящееся в ct число 7 — в строку. Строка «forty» будет преобразована в ноль. Но это только начало.

Нажмите кнопку **Returns 2** еще несколько раз. В управляемом коде метод Add создал бы исключения повторяющихся ключей, даже если бы значения были приведены к правильным типам.

> В то время как метод Insert изменит значение, связанное с имеющимся ключом, и вернет логическое значение, показывающее, добавлен ли новый ключ в словарь.

Именно поэтому значение, связанное с ключом 7, продолжает изменяться.

> [!div class="tabbedCodeSnippets"]
> ```csharp
> public static object GetListOfThis(object obj)
> {
>     Type target = obj.GetType();
>     return Activator.CreateInstance(typeof(List<>).MakeGenericType(target));
> }
> ```
> ```vb
> Public Shared Function GetListOfThis(obj As Object) As Object
>     Dim target As Type = obj.GetType()
>     Return Activator.CreateInstance(GetType(List(Of )).MakeGenericType(target))
> End Function
> ```

Другая непредвиденная особенность — если передать неприсвоенную переменную JavaScript в качестве строкового аргумента, появится строка «undefined».

## Иными словами, следует соблюдать осторожность при передаче типов коллекций .NET Framework в код JavaScript.


**Примечание.**  При работе с большими объемами сцепляемого текста можно повысить эффективность этой операции, переместив код в метод .NET Framework и воспользовавшись классом StringBuilder, как показано в функции showMap. Несмотря на то, что из компонента среды выполнения Windows нельзя предоставлять доступ к собственным универсальным типам, можно возвращать универсальные коллекции .NET Framework в классы среды выполнения Windows с помощью кода примерно следующего вида: [!div class="tabbedCodeSnippets"]

> [!div class="tabbedCodeSnippets"]
> ```csharp
> namespace SampleComponent
> {
>     public sealed class Eventful
>     {
>         public event EventHandler<TestEventArgs> Test;
>         public void OnTest(string msg, long number)
>         {
>             EventHandler<TestEventArgs> temp = Test;
>             if (temp != null)
>             {
>                 temp(this, new TestEventArgs()
>                 {
>                     Value1 = msg,
>                     Value2 = number
>                 });
>             }
>         }
>     }
>
>     public sealed class TestEventArgs
>     {
>         public string Value1 { get; set; }
>         public long Value2 { get; set; }
>     }
> }
> ```
> ```vb
> Public NotInheritable Class Eventful
>     Public Event Test As EventHandler(Of TestEventArgs)
>     Public Sub OnTest(ByVal msg As String, ByVal number As Long)
>         RaiseEvent Test(Me, New TestEventArgs() With {
>                             .Value1 = msg,
>                             .Value2 = number
>                             })
>     End Sub
> End Class
>
> Public NotInheritable Class TestEventArgs
>     Public Property Value1 As String
>     Public Property Value2 As Long
> End Class
> ```

List&lt;T&gt; реализует IList&lt;T&gt;, который представляется в JavaScript как тип среды выполнения Windows (IVector&lt;T&gt;). Объявление событий

Для объявления событий можно использовать стандартные шаблоны событий .NET Framework или другие шаблоны, применяемые в среде выполнения Windows. Платформа .NET Framework поддерживает эквивалентность между делегатом System.EventHandler&lt;TEventArgs&gt; и делегатом среды выполнения Windows EventHandler&lt;T&gt;, поэтому использование EventHandler&lt;TEventArgs&gt; является удобным способом реализации стандартного шаблона .NET Framework.

Чтобы продемонстрировать этот подход, добавьте в проект SampleComponent следующие два класса: [!div class="tabbedCodeSnippets"] Если в среде выполнения Windows предоставляется доступ к событию, то класс аргументов этого события наследуется от System.Object.

```javascript
var ev;

function events1() {
   ev = new SampleComponent.Eventful();
   ev.addEventListener("test", function (e) {
       document.getElementById('output').innerHTML = e.value1;
       document.getElementById('output').innerHTML += "<br/>" + e.value2;
   });
   ev.onTest("Number of feet in a mile:", 5280);
}
```

Он не наследуется от System.EventArgs, как это происходит в .NET Framework, поскольку EventArgs не является типом среды выполнения Windows.

```javascript
var events1Button = document.getElementById("events1Button");
events1Button.addEventListener("click", events1, false);
```

## При объявлении пользовательских методов доступа к событиям (событие объявляется с ключевым словом (**Custom** в Visual Basic) необходимо использовать шаблон событий среды выполнения Windows.


См. раздел [Пользовательские события и методы доступа к событиям в компонентах среды выполнения Windows](custom-events-and-event-accessors-in-windows-runtime-components.md). Чтобы обработать событие Test, добавьте в файл default.js функцию events1. Функция events1 создает функцию обработчика событий для события Test и немедленно вызывает метод OnTest, чтобы вызвать это событие.

Если в теле обработчика событий установить точку останова, можно увидеть, что объект, передаваемый в качестве единственного параметра, содержит исходный объект и оба члена TestEventArgs. Добавьте код регистрации события к тому же блоку then, к которому был добавлен другой код регистрации события: Предоставление доступа к асинхронным операциям

> [!div class="tabbedCodeSnippets"]
> ```csharp
> using System.Runtime.InteropServices.WindowsRuntime;
> using Windows.Foundation;
>
> public static IAsyncOperationWithProgress<IList<long>, double>
> GetPrimesInRangeAsync(long start, long count)
> {
>     if (start < 2 || count < 1) throw new ArgumentException();
>
>     return AsyncInfo.Run<IList<long>, double>((token, progress) =>
>
>         Task.Run<IList<long>>(() =>
>         {
>             List<long> primes = new List<long>();
>             double onePercent = count / 100;
>             long ctProgress = 0;
>             double nextProgress = onePercent;
>
>             for (long candidate = start; candidate < start + count; candidate++)
>             {
>                 ctProgress += 1;
>                 if (ctProgress >= nextProgress)
>                 {
>                     progress.Report(ctProgress / onePercent);
>                     nextProgress += onePercent;
>                 }
>                 bool isPrime = true;
>                 for (long i = 2, limit = (long)Math.Sqrt(candidate); i <= limit; i++)
>                 {
>                     if (candidate % i == 0)
>                     {
>                         isPrime = false;
>                         break;
>                     }
>                 }
>                 if (isPrime) primes.Add(candidate);
>
>                 token.ThrowIfCancellationRequested();
>             }
>             progress.Report(100.0);
>             return primes;
>         }, token)
>     );
> }
> ```
> ```vb
> Imports System.Runtime.InteropServices.WindowsRuntime
>
> Public Shared Function GetPrimesInRangeAsync(ByVal start As Long, ByVal count As Long)
> As IAsyncOperationWithProgress(Of IList(Of Long), Double)
>
>     If (start < 2 Or count < 1) Then Throw New ArgumentException()
>
>     Return AsyncInfo.Run(Of IList(Of Long), Double)( _
>         Function(token, prog)
>             Return Task.Run(Of IList(Of Long))( _
>                 Function()
>                     Dim primes As New List(Of Long)
>                     Dim onePercent As Long = count / 100
>                     Dim ctProgress As Long = 0
>                     Dim nextProgress As Long = onePercent
>
>                     For candidate As Long = start To start + count - 1
>                         ctProgress += 1
>
>                         If ctProgress >= nextProgress Then
>                             prog.Report(ctProgress / onePercent)
>                             nextProgress += onePercent
>                         End If
>
>                         Dim isPrime As Boolean = True
>                         For i As Long = 2 To CLng(Math.Sqrt(candidate))
>                             If (candidate Mod i) = 0 Then
>                                 isPrime = False
>                                 Exit For
>                             End If
>                         Next
>
>                         If isPrime Then primes.Add(candidate)
>
>                         token.ThrowIfCancellationRequested()
>                     Next
>                     prog.Report(100.0)
>                     Return primes
>                 End Function, token)
>         End Function)
> End Function
> ```

Платформа .NET Framework содержит богатый набор средств для асинхронной и параллельной обработки, основанных на классе Task и универсальном классе [Task&lt;TResult&gt;](https://msdn.microsoft.com/library/dd321424.aspx). Для реализации асинхронной обработки на основе задач в компоненте среды выполнения Windows используйте интерфейсы среды выполнения Windows [IAsyncAction](https://msdn.microsoft.com/library/br205781.aspx), [IAsyncActionWithProgress&lt;TProgress&gt;](https://msdn.microsoft.com/library/br205784.aspx), [IAsyncOperation&lt;TResult&gt;](https://msdn.microsoft.com/library/br205802.aspx) и [IAsyncOperationWithProgress&lt;TResult, TProgress&gt;](https://msdn.microsoft.com/library/br205807.aspx). (В среде выполнения Windows операции возвращают результаты, а действия — нет). В этом разделе демонстрируется отменяемая асинхронная операция, которая информирует о ходе выполнения и возвращает результаты.

-   Метод GetPrimesInRangeAsync использует класс [AsyncInfo](https://msdn.microsoft.com/library/system.runtime.interopservices.windowsruntime.asyncinfo.aspx) для создания задачи и подключения его функций отмены и хода выполнения к объекту WinJS.Promise.
-   Сначала добавьте метод GetPrimesInRangeAsync в класс Example: [!div class="tabbedCodeSnippets"] GetPrimesInRangeAsync – очень простой метод поиска простых чисел, который намеренно создан таким.

    -   Нас интересует в первую очередь реализация асинхронной операции, поэтому простота имеет большое значение, а низкая производительность станет дополнительным преимуществом при демонстрации отмены.
    -   Метод GetPrimesInRangeAsync находит простые числа путем простого перебора: каждое число-кандидат делится на все целые числа, которые не превышают квадратный корень из этого числа, а не только на простые числа. Рассмотрим все шаги этого кода:

        > Перед началом асинхронной операции выполните вспомогательные действия, такие как проверка параметров и создание исключений, если заданы недопустимые входные значения. В основе этой реализации лежат метод [AsyncInfo.Run&lt;TResult, TProgress&gt;(Func&lt;CancellationToken, IProgress&lt;TProgress&gt;, Task&lt;TResult&gt;](https://msdn.microsoft.com/library/hh779740.aspx)&gt;) и делегат, который является единственным параметром этого метода.

    -   Делегат должен принимать токен отмены и интерфейс для информирования о ходе выполнения, а также возвращать запущенную задачу, которая использует эти параметры. Если код JavaScript вызывает метод GetPrimesInRangeAsync, выполняются следующие действия (не обязательно в порядке, приведенном ниже): Объект [WinJS.Promise](https://msdn.microsoft.com/library/windows/apps/br211867.aspx) предоставляет функции для обработки возвращаемых результатов, реагирования на отмену и обработки информации о ходе выполнения.
    -   Метод AsyncInfo.Run создает источник отмены и объект, реализующий интерфейс IProgress&lt;T&gt;.
    -   Токен [CancellationToken](https://msdn.microsoft.com/library/system.threading.cancellationtoken.aspx) из источника отмены и интерфейс [IProgress&lt;T&gt;](https://msdn.microsoft.com/library/hh138298.aspx) передаются делегату.

-   **Примечание.**  Если объект Promise не предоставляет функцию для реагирования на отмену, AsyncInfo.Run все равно передает отменяемый токен, и отмена может произойти в любом случае. Если объект Promise не предоставляет функцию для обработки обновлений хода выполнения, AsyncInfo.Run все равно предоставляет объект, реализующий IProgress&lt;T&gt;, но его отчеты игнорируются. Делегат использует метод [Task.Run&lt;TResult&gt;(Func&lt;TResult&gt;, CancellationToken](https://msdn.microsoft.com/library/hh160376.aspx)) для создания запущенной задачи, которая использует токен и интерфейс хода выполнения.

    -   Делегат запущенной задачи предоставляется лямбда-функцией, которая вычисляет требуемый результат. Ниже мы подробнее остановимся на этом.
    -   Метод AsyncInfo.Run создает объект, реализующий интерфейс [IAsyncOperationWithProgress&lt;TResult, TProgress&gt;](https://msdn.microsoft.com/library/windows/apps/br206594.aspx), связывает механизм отмены среды выполнения Windows с источником токена и связывает функцию информирования о ходе выполнения объекта Promise с интерфейсом IProgress&lt;T&gt;. Интерфейс IAsyncOperationWithProgress&lt;TResult, TProgress&gt; возвращается в JavaScript.
-   Лямбда-функция, представляемая запущенной задачей, не принимает никаких аргументов.

Поскольку это лямбда-функция, у нее есть доступ к токену и интерфейсу IProgress.

```javascript
var resultAsync;
function asyncRun() {
    document.getElementById('output').innerHTML = "Retrieving prime numbers.";
    btnAsync.disabled = "disabled";
    btnCancel.disabled = "";

    resultAsync = SampleComponent.Example.getPrimesInRangeAsync(10000000000001, 2500).then(
        function (primes) {
            for (i = 0; i < primes.length; i++)
                document.getElementById('output').innerHTML += " " + primes[i];

            btnCancel.disabled = "disabled";
            btnAsync.disabled = "";
        },
        function () {
            document.getElementById('output').innerHTML += " -- getPrimesInRangeAsync was canceled. -- ";

            btnCancel.disabled = "disabled";
            btnAsync.disabled = "";
        },
        function (prog) {
            document.getElementById('primeProg').value = prog;
        }
    );
}

function asyncCancel() {    
    resultAsync.cancel();
}
```

При вычислении каждого числа-кандидата лямбда-функция выполняет следующие операции:

```javascript
var btnAsync = document.getElementById("btnAsync");
btnAsync.addEventListener("click", asyncRun, false);
var btnCancel = document.getElementById("btnCancel");
btnCancel.addEventListener("click", asyncCancel, false);
```

Проверяет, достигнут ли очередной процентный пункт хода выполнения. Если он достигнут, лямбда-функция вызывает метод IProgress&lt;T&gt;.Report и этот процент передается функции, заданной объектом Promise для информирования о ходе выполнения. С помощью токена отмены создает исключение, если операция была отменена. Если метод [IAsyncInfo.Cancel](https://msdn.microsoft.com/library/windows/apps/windows.foundation.iasyncinfo.cancel.aspx) (который наследуется интерфейсом IAsyncOperationWithProgress&lt;TResult, TProgress&gt;) был вызван, то подключение, настраиваемое методом AsyncInfo.Run, гарантирует, что токен отмены получает уведомление. Когда лямбда-функция возвращает список простых чисел, список передается функции, которая была задана объектом WinJS.Promise для обработки результатов.

Для создания объекта Promise в JavaScript и настройки механизма отмены добавьте в файл default.js функции asyncRun и asyncCancel.

Не забудьте еще раз добавить код регистрации события. Путем вызова асинхронного метода GetPrimesInRangeAsync функция asyncRun создает объект WinJS.Promise Метод then объекта принимает три функции, которые обрабатывают возвращаемый результат, реагируют на ошибки (включая отмену) и обрабатывают отчеты о ходе выполнения. В этом примере возвращенные результаты отображаются в области вывода. Отмена или завершение сбрасывают состояние кнопок, которые запускают и отменяют операцию. Информация о ходе выполнения отображается на соответствующем элементе управления.

## Функция asyncCancel просто вызывает метод Cancel объекта WinJS.Promise.

* [Чтобы запустить приложение, нажмите клавишу F5.](https://msdn.microsoft.com/library/windows/apps/xaml/br230302.aspx)
* [Для запуска асинхронной операции нажмите кнопку **Async**.](https://msdn.microsoft.com/library/windows/apps/xaml/mt185501.aspx)
* [Дальнейший ход событий зависит от быстродействия компьютера.](walkthrough-creating-a-simple-windows-runtime-component-and-calling-it-from-javascript.md)



<!--HONumber=Jun16_HO5-->


