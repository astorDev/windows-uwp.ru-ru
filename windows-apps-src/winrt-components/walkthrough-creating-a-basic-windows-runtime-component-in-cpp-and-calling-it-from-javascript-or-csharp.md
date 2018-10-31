---
author: msatranjr
title: Создание компонента среды выполнения Windows на C++/CX и его вызов из JavaScript или C#
description: В этом пошаговом руководстве описан процесс создания базового компонента среды выполнения Windows, являющегося библиотекой DLL, которую можно вызвать из JavaScript, C# или VisualBasic.
ms.assetid: 764CD9C6-3565-4DFF-88D7-D92185C7E452
ms.author: misatran
ms.date: 05/14/2018
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 8d56e220a65d0de261ab0cc64b8ac417e1f480eb
ms.sourcegitcommit: ca96031debe1e76d4501621a7680079244ef1c60
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/31/2018
ms.locfileid: "5838365"
---
# <a name="walkthrough-creating-a-windows-runtime-component-in-ccx-and-calling-it-from-javascript-or-c"></a>Пошаговое руководство: создание компонента среды выполнения Windows на C++/CX и его вызов из JavaScript или C#
> [!NOTE]
> В этом разделе представлена вспомогательная информация для поддержки приложений на C++/CX. Однако в новых приложениях мы рекомендуем использовать [C++/WinRT](../cpp-and-winrt-apis/intro-to-using-cpp-with-winrt.md). C++/WinRT — это полностью стандартная проекция языка C++17 для API среды выполнения Windows (WinRT), реализованная как библиотека на основе файлов заголовков и предназначенная для предоставления вам первоклассного доступа к современным API-интерфейсам Windows. Чтобы узнать, как создать компонент среды выполнения Windows, с помощью C + +/ WinRT, см. в разделе [Создание событий в C + +/ WinRT](../cpp-and-winrt-apis/author-events.md).

В этом пошаговом руководстве описан процесс создания базового компонента среды выполнения Windows, являющегося библиотекой DLL, которую можно вызвать из JavaScript, C# или VisualBasic. Прежде чем приступить к изучению этого пошагового руководства, убедитесь, что вы знакомы с такими понятиями, как абстрактный двоичный интерфейс (ABI), ссылочные классы и расширения компонентов Visual C++, которые упрощают работу со ссылочными классами. Дополнительные сведения см. в статье [Создание компонентов среды выполнения Windows в C++](creating-windows-runtime-components-in-cpp.md) и [Справочник по языку Visual C++ (C++/CX)](https://msdn.microsoft.com/library/windows/apps/xaml/hh699871.aspx).

## <a name="creating-the-c-component-dll"></a>Создание библиотеки DLL компонента C++
В этом примере сначала создается проект компонента, но можно сначала создать проект JavaScript. Порядок не имеет значения.

Обратите внимание, что основной класс компонента содержит примеры определений свойств и методов, а также объявление события. Они включаются в проект только для демонстрации самого процесса создания. В данном примере они не требуются, и мы заменим весь созданный код нашим собственным кодом.

## **<a name="to-create-the-c-component-project"></a>Создание проекта компонента C++**
В строке меню Visual Studio выберите **Файл, Создать, Проект**.

В левой области диалогового окна **Новый проект** разверните узел **Visual C++** и выберите узел универсальных приложений для Windows.

На центральной панели выберите **Компонент среды выполнения Windows**, а затем назовите проект WinRT\_CPP.

Нажмите кнопку **ОК**.

## **<a name="to-add-an-activatable-class-to-the-component"></a>Добавление активируемого класса в компонент**
Активируемый класс — это класс, который может создаваться в клиентском коде с помощью выражения **new** (**New** в Visual Basic и **ref new** в C++). В вашем компоненте он объявляется как **public ref class sealed**. В действительности файлы Class1.h и Class1.cpp уже содержат ссылочный класс. Имя можно изменить, однако в данном примере мы будет использовать имя по умолчанию Class1. При необходимости в компоненте можно задать дополнительные ссылочные классы или регулярные классы. Дополнительные сведения о ссылочных классах см. в статье [Система типов (C++/CX)](https://msdn.microsoft.com/library/windows/apps/hh755822.aspx).

Добавьте следующие директивы \#include в файл Class1.h:

```cpp
#include <collection.h>
#include <ppl.h>
#include <amp.h>
#include <amp_math.h>
```

collection.h — это заголовочный файл для конкретных классов C++, таких как класс Platform::Collections::Vector и класс Platform::Collections::Map, которые реализуют независимые от языка интерфейсы, определенные средой выполнения Windows. Заголовки amp используются для выполнения вычислений в графическом процессоре (GPU). Они не имеют эквивалентов среды выполнения Windows, и это нормально, так как они являются закрытыми. Как правило, по соображениям производительности следует использовать код C++ стандарта ISO и стандартные библиотеки внутри компонента; в типах среды выполнения Windows должен использоваться только интерфейс среды выполнения Windows.

## <a name="to-add-a-delegate-at-namespace-scope"></a>Добавление делегата в область пространства имен
Делегат — это конструкция, которая определяет параметры и тип возвращаемого значения для методов. Событие — это экземпляр указанного типа делегата, и любой метод обработчика событий, который подписан на событие, должен иметь сигнатуру, которая определена в делегате. Следующий код определяет тип делегата, который принимает значение типа int и возвращает значение типа void. Затем код объявляет открытое событие event этого типа; это позволяет коду клиента предоставлять методы, которые вызываются при возникновении события.

Добавьте следующее объявление делегата в область пространства имен в Class1.h непосредственно перед объявлением Class1.

```cpp
public delegate void PrimeFoundHandler(int result);
```

Если код не выравнивается правильно при вставке в Visual Studio, просто нажмите клавиши CTRL+K+D, чтобы исправить отступы по всему файлу.

## <a name="to-add-the-public-members"></a>Добавление открытых членов
Класс предоставляет три открытых метода и одно открытое событие. Первый метод является синхронным, поскольку он всегда выполняется очень быстро. Поскольку выполнение других двух методов может занять некоторое время, они являются асинхронными, чтобы они не блокировали поток пользовательского интерфейса. Эти методы возвращают IAsyncOperationWithProgress и IAsyncActionWithProgress. Первый интерфейс определяет асинхронный метод, который возвращает результат, а второй — асинхронный метод, который возвращает значение void. Эти интерфейсы также позволяют клиентскому коду получать обновления в ходе выполнения операции.

```cpp
public:

        // Synchronous method.
        Windows::Foundation::Collections::IVector<double>^  ComputeResult(double input);

        // Asynchronous methods
        Windows::Foundation::IAsyncOperationWithProgress<Windows::Foundation::Collections::IVector<int>^, double>^
            GetPrimesOrdered(int first, int last);
        Windows::Foundation::IAsyncActionWithProgress<double>^ GetPrimesUnordered(int first, int last);

        // Event whose type is a delegate "class"
        event PrimeFoundHandler^ primeFoundEvent;

```
## <a name="to-add-the-private-members"></a>Добавление закрытых членов
Класс содержит три закрытых члена: два вспомогательных метода для численных вычислений и объект CoreDispatcher, который используется для маршалирования вызовов событий из рабочих потоков в поток пользовательского интерфейса.

```cpp
private:
        bool is_prime(int n);
        Windows::UI::Core::CoreDispatcher^ m_dispatcher;
```

## <a name="to-add-the-header-and-namespace-directives"></a>Добавление директив заголовков и пространств имен
В файле Class1.cpp добавьте следующие директивы #include:

```cpp
#include <ppltasks.h>
#include <concurrent_vector.h>
```

Теперь добавьте следующие директивы using, чтобы получить необходимые пространства имен:

```cpp
using namespace concurrency;
using namespace Platform::Collections;
using namespace Windows::Foundation::Collections;
using namespace Windows::Foundation;
using namespace Windows::UI::Core;
```

## <a name="to-add-the-implementation-for-computeresult"></a>Добавление реализации ComputeResult
В Class1.cpp добавьте реализацию следующего метода. Этот метод выполняется синхронно в вызывающем потоке, но очень быстро, поскольку он использует C++ AMP для параллелизации вычислений в графическом процессоре. Дополнительные сведения см. в разделе «Общие сведения о C++ AMP». Результаты добавляются в конкретный тип Platform::Collections::Vector<T>, который неявно преобразуется в Windows::Foundation::Collections::IVector<T> при возврате.

```cpp
//Public API
IVector<double>^ Class1::ComputeResult(double input)
{
    // Implement your function in ISO C++ or
    // call into your C++ lib or DLL here. This example uses AMP.
    float numbers[] = { 1.0, 10.0, 60.0, 100.0, 600.0, 10000.0 };
    array_view<float, 1> logs(6, numbers);

    // See http://msdn.microsoft.com/en-us/library/hh305254.aspx
    parallel_for_each(
        logs.extent,
        [=] (index<1> idx) restrict(amp)
    {
        logs[idx] = concurrency::fast_math::log10(logs[idx]);
    }
    );

    // Return a Windows Runtime-compatible type across the ABI
    auto res = ref new Vector<double>();
    int len = safe_cast<int>(logs.extent.size());
    for(int i = 0; i < len; i++)
    {      
        res->Append(logs[i]);
    }

    // res is implicitly cast to IVector<double>
    return res;
}
```
## <a name="to-add-the-implementation-for-getprimesordered-and-its-helper-method"></a>Добавление реализации GetPrimesOrdered и ее вспомогательного метода
В Class1.cpp добавьте реализации GetPrimesOrdered и вспомогательного метода is_prime. GetPrimesOrdered использует класс concurrent_vector и цикл функции parallel_for для разделения работы и использования максимального объема ресурсов компьютера, на котором выполняется программа для получения результатов. После вычисления, сохранения и сортировки результатов они добавляются в Platform::Collections::Vector<T> и возвращаются в виде Windows::Foundation::Collections::IVector<T> в клиентский код.

Обратите внимание на код для отслеживания хода выполнения, который позволяет клиенту подключить индикатор выполнения или другой пользовательский интерфейс для представления пользователю информации о том, сколько времени займет выполнение операции. Получение информации о ходе выполнения имеет свой недостаток. Событие должно произойти на стороне компонента и быть обработано в потоке пользовательского интерфейса, а значение хода выполнения должно быть сохранено в каждой итерации. Одним из способов уменьшить затраты является ограничение периодичности возникновения события отслеживания хода выполнения. Если затраты по-прежнему неприемлемо высоки, или если оценка длительности операции не представляется возможной, рассмотрите возможность использования кольца хода выполнения, которое показывает, что операция выполняется, но не отображает время, оставшееся до ее завершения.

```cpp
// Determines whether the input value is prime.
bool Class1::is_prime(int n)
{
    if (n < 2)
        return false;
    for (int i = 2; i < n; ++i)
    {
        if ((n % i) == 0)
            return false;
    }
    return true;
}

// This method computes all primes, orders them, then returns the ordered results.
IAsyncOperationWithProgress<IVector<int>^, double>^ Class1::GetPrimesOrdered(int first, int last)
{
    return create_async([this, first, last]
    (progress_reporter<double> reporter) -> IVector<int>^ {
        // Ensure that the input values are in range.
        if (first < 0 || last < 0) {
            throw ref new InvalidArgumentException();
        }
        // Perform the computation in parallel.
        concurrent_vector<int> primes;
        long operation = 0;
        long range = last - first + 1;
        double lastPercent = 0.0;

        parallel_for(first, last + 1, [this, &primes, &operation,
            range, &lastPercent, reporter](int n) {

                // Increment and store the number of times the parallel
                // loop has been called on all threads combined. There
                // is a performance cost to maintaining a count, and
                // passing the delegate back to the UI thread, but it's
                // necessary if we want to display a determinate progress
                // bar that goes from 0 to 100%. We can avoid the cost by
                // setting the ProgressBar IsDeterminate property to false
                // or by using a ProgressRing.
                if(InterlockedIncrement(&operation) % 100 == 0)
                {
                    reporter.report(100.0 * operation / range);
                }

                // If the value is prime, add it to the local vector.
                if (is_prime(n)) {
                    primes.push_back(n);
                }
        });

        // Sort the results.
        std::sort(begin(primes), end(primes), std::less<int>());        
        reporter.report(100.0);

        // Copy the results to a Vector object, which is
        // implicitly converted to the IVector return type. IVector
        // makes collections of data available to other
        // Windows Runtime components.
        return ref new Vector<int>(primes.begin(), primes.end());
    });
}
```

## <a name="to-add-the-implementation-for-getprimesunordered"></a>Добавление реализации GetPrimesUnordered
Последним шагом по созданию компонента C++ является добавление реализации метода GetPrimesUnordered в Class1.cpp. Этот метод возвращает каждый результат по мере его нахождения, не дожидаясь нахождения всех результатов. Каждый результат возвращается в обработчике событий и отображается в пользовательском интерфейсе в реальном времени. Опять же, обратите внимание на то, что используется отслеживание хода выполнения. Этот метод также использует вспомогательный метод is_prime.

```cpp
// This method returns no value. Instead, it fires an event each time a
// prime is found, and passes the prime through the event.
// It also passes progress info.
IAsyncActionWithProgress<double>^ Class1::GetPrimesUnordered(int first, int last)
{

    auto window = Windows::UI::Core::CoreWindow::GetForCurrentThread();
    m_dispatcher = window->Dispatcher;


    return create_async([this, first, last](progress_reporter<double> reporter) {

        // Ensure that the input values are in range.
        if (first < 0 || last < 0) {
            throw ref new InvalidArgumentException();
        }

        // In this particular example, we don't actually use this to store
        // results since we pass results one at a time directly back to
        // UI as they are found. However, we have to provide this variable
        // as a parameter to parallel_for.
        concurrent_vector<int> primes;
        long operation = 0;
        long range = last - first + 1;
        double lastPercent = 0.0;

        // Perform the computation in parallel.
        parallel_for(first, last + 1,
            [this, &primes, &operation, range, &lastPercent, reporter](int n)
        {
            // Store the number of times the parallel loop has been called  
            // on all threads combined. See comment in previous method.
            if(InterlockedIncrement(&operation) % 100 == 0)
            {
                reporter.report(100.0 * operation / range);
            }

            // If the value is prime, pass it immediately to the UI thread.
            if (is_prime(n))
            {                
                // Since this code is probably running on a worker
                // thread, and we are passing the data back to the
                // UI thread, we have to use a CoreDispatcher object.
                m_dispatcher->RunAsync( CoreDispatcherPriority::Normal,
                    ref new DispatchedHandler([this, n, operation, range]()
                {
                    this->primeFoundEvent(n);

                }, Platform::CallbackContext::Any));

            }
        });
        reporter.report(100.0);
    });
}
```

## <a name="creating-a-javascript-client-app"></a>Создание клиентского приложения JavaScript
Если требуется создать клиент C#, этот раздел можно пропустить.

## <a name="to-create-a-javascript-project"></a>Создание проекта JavaScript
В Обозревателе решений в контекстном меню узла «Решение» выберите **Добавить, Новый проект**.

Разверните узел JavaScript (он может быть вложен в узел **Другие языки**) и выберите пункт **Пустое приложение (универсальные приложения для Windows)**.

Примите имя по умолчанию App1, нажав кнопку **ОК**.

Откройте контекстное меню узла проекта App1 и выберите пункт **Назначить запускаемым проектом**.

Добавьте ссылку на проект в WinRT_CPP:

В контекстном меню узла «Ссылки» выберите **Добавить ссылку**.

В левой области диалогового окна «Диспетчер ссылок» выберите **Проекты**, а затем выберите **Решение**.

В центральной области выберите WinRT_CPP, а затем нажмите кнопку **OK**.

## <a name="to-add-the-html-that-invokes-the-javascript-event-handlers"></a>Добавление кода HTML, вызывающего обработчики событий JavaScript
Вставьте следующий код HTML в узел <body> страницы default.html:

```HTML
<div id="LogButtonDiv">
     <button id="logButton">Logarithms using AMP</button>
 </div>
 <div id="LogResultDiv">
     <p id="logResult"></p>
 </div>
 <div id="OrderedPrimeButtonDiv">
     <button id="orderedPrimeButton">Primes using parallel_for with sort</button>
 </div>
 <div id="OrderedPrimeProgress">
     <progress id="OrderedPrimesProgressBar" value="0" max="100"></progress>
 </div>
 <div id="OrderedPrimeResultDiv">
     <p id="orderedPrimes">
         Primes found (ordered):
     </p>
 </div>
 <div id="UnorderedPrimeButtonDiv">
     <button id="ButtonUnordered">Primes returned as they are produced.</button>
 </div>
 <div id="UnorderedPrimeDiv">
     <progress id="UnorderedPrimesProgressBar" value="0" max="100"></progress>
 </div>
 <div id="UnorderedPrime">
     <p id="unorderedPrimes">
         Primes found (unordered):
     </p>
 </div>
 <div id="ClearDiv">
     <button id="Button_Clear">Clear</button>
 </div>
```

## <a name="to-add-styles"></a>Добавление стилей
В файле default.css удалите стиль body, а затем добавьте следующие стили:

```css
#LogButtonDiv {
border: orange solid 1px;
-ms-grid-row: 1; /* default is 1 */
-ms-grid-column: 1; /* default is 1 */
}
#LogResultDiv {
background: black;
border: red solid 1px;
-ms-grid-row: 1;
-ms-grid-column: 2;
}
#UnorderedPrimeButtonDiv, #OrderedPrimeButtonDiv {
border: orange solid 1px;
-ms-grid-row: 2;   
-ms-grid-column:1;
}
#UnorderedPrimeProgress, #OrderedPrimeProgress {
border: red solid 1px;
-ms-grid-column-span: 2;
height: 40px;
}
#UnorderedPrimeResult, #OrderedPrimeResult {
border: red solid 1px;
font-size:smaller;
-ms-grid-row: 2;
-ms-grid-column: 3;
-ms-overflow-style:scrollbar;
}
```

## <a name="to-add-the-javascript-event-handlers-that-call-into-the-component-dll"></a>Добавление обработчиков событий JavaScript, вызывающих библиотеку DLL компонента
Добавьте в конец файла default.js следующие функции. Эти функции вызываются при нажатии соответствующих кнопок на главной странице. Обратите внимание, как JavaScript активирует класс C++, а затем вызывает его методы и использует возвращаемые значения для заполнения меток HTML.

```JavaScript
var nativeObject = new WinRT_CPP.Class1();

function LogButton_Click() {

    var val = nativeObject.computeResult(0);
    var result = "";

    for (i = 0; i < val.length; i++) {
        result += val[i] + "<br/>";
    }

    document.getElementById('logResult').innerHTML = result;
}

function ButtonOrdered_Click() {
    document.getElementById('orderedPrimes').innerHTML = "Primes found (ordered): ";

    nativeObject.getPrimesOrdered(2, 10000).then(
        function (v) {
            for (var i = 0; i < v.length; i++)
                document.getElementById('orderedPrimes').innerHTML += v[i] + " ";
        },
        function (error) {
            document.getElementById('orderedPrimes').innerHTML += " " + error.description;
        },
        function (p) {
            var progressBar = document.getElementById("OrderedPrimesProgressBar");
            progressBar.value = p;
        });
}

function ButtonUnordered_Click() {
    document.getElementById('unorderedPrimes').innerHTML = "Primes found (unordered): ";
    nativeObject.onprimefoundevent = handler_unordered;

    nativeObject.getPrimesUnordered(2, 10000).then(
        function () { },
        function (error) {
            document.getElementById("unorderedPrimes").innerHTML += " " + error.description;
        },
        function (p) {
            var progressBar = document.getElementById("UnorderedPrimesProgressBar");
            progressBar.value = p;
        });
}

var handler_unordered = function (n) {
    document.getElementById('unorderedPrimes').innerHTML += n.target.toString() + " ";
};

function ButtonClear_Click() {

    document.getElementById('logResult').innerHTML = "";
    document.getElementById("unorderedPrimes").innerHTML = "";
    document.getElementById('orderedPrimes').innerHTML = "";
    document.getElementById("UnorderedPrimesProgressBar").value = 0;
    document.getElementById("OrderedPrimesProgressBar").value = 0;
}
```

Добавьте код для добавления прослушивателей событий, заменив существующий вызов WinJS.UI.processAll в app.onactivated в файле default.js следующим кодом, который реализует регистрацию событий в блоке then. Подробное описание этой процедуры см. в разделе Создание приложения «Hello World» (JavaScript).

```JavaScript
args.setPromise(WinJS.UI.processAll().then( function completed() {
    var logButton = document.getElementById("logButton");
    logButton.addEventListener("click", LogButton_Click, false);
    var orderedPrimeButton = document.getElementById("orderedPrimeButton");
    orderedPrimeButton.addEventListener("click", ButtonOrdered_Click, false);
    var buttonUnordered = document.getElementById("ButtonUnordered");
    buttonUnordered.addEventListener("click", ButtonUnordered_Click, false);
    var buttonClear = document.getElementById("Button_Clear");
    buttonClear.addEventListener("click", ButtonClear_Click, false);
}));
```

Чтобы запустить приложение, нажмите клавишу F5.

## <a name="creating-a-c-client-app"></a>Создание клиентского приложения C#

## <a name="to-create-a-c-project"></a>Создание проекта C#
В Обозревателе решений в контекстном меню узла «Решение» выберите **Добавить, Новый проект**.

Разверните узел Visual C# (он может быть вложен в узел **Другие языки**), выберите в левой области **Windows**, после чего **Универсальные**, а затем выберите в средней области **Пустое приложение**.

Присвойте приложению имя CS_Client, после чего нажмите кнопку **OK**.

Откройте контекстное меню узла проекта CS_Client и выберите пункт **Назначить запускаемым проектом**.

Добавьте ссылку на проект в WinRT_CPP:

В контекстном меню узла **Ссылки** выберите **Добавить ссылку**.

В левой области диалогового окна **Диспетчер ссылок** выберите **Проекты** а затем выберите **Решение**.

В центральной области выберите WinRT_CPP, а затем нажмите кнопку **OK**.

## <a name="to-add-the-xaml-that-defines-the-user-interface"></a>Добавление кода XAML, определяющего пользовательский интерфейс
Скопируйте следующий код в элемент Grid в файле MainPage.xaml.

```xaml
<ScrollViewer>
            <StackPanel Width="1400">

                <Button x:Name="Button1" Width="340" Height="50"  Margin="0,20,20,20" Content="Synchronous Logarithm Calculation" FontSize="16" Click="Button1_Click_1"/>
                <TextBlock x:Name="Result1" Height="100" FontSize="14"></TextBlock>
            <Button x:Name="PrimesOrderedButton" Content="Prime Numbers Ordered" FontSize="16" Width="340" Height="50" Margin="0,20,20,20" Click="PrimesOrderedButton_Click_1"></Button>
            <ProgressBar x:Name="PrimesOrderedProgress" IsIndeterminate="false" Height="40"></ProgressBar>
                <TextBlock x:Name="PrimesOrderedResult" MinHeight="100" FontSize="10" TextWrapping="Wrap"></TextBlock>
            <Button x:Name="PrimesUnOrderedButton" Width="340" Height="50" Margin="0,20,20,20" Click="PrimesUnOrderedButton_Click_1" Content="Prime Numbers Unordered" FontSize="16"></Button>
            <ProgressBar x:Name="PrimesUnOrderedProgress" IsIndeterminate="false" Height="40" ></ProgressBar>
            <TextBlock x:Name="PrimesUnOrderedResult" MinHeight="100" FontSize="10" TextWrapping="Wrap"></TextBlock>

            <Button x:Name="Clear_Button" Content="Clear" HorizontalAlignment="Left" Margin="0,20,20,20" VerticalAlignment="Top" Width="341" Click="Clear_Button_Click" FontSize="16"/>
        </StackPanel>
</ScrollViewer>
```

## <a name="to-add-the-event-handlers-for-the-buttons"></a>Добавление обработчиков событий к кнопкам
В Обозревателе решений откройте файл MainPage.xaml.cs (Файл может быть вложен в mainpage.xaml). Добавьте директиву using для System.Text, а затем добавьте обработчик событий для вычисления логарифма в классе MainPage.

```csharp
private void Button1_Click_1(object sender, RoutedEventArgs e)
{
    // Create the object
    var nativeObject = new WinRT_CPP.Class1();

    // Call the synchronous method. val is an IList that
    // contains the results.
    var val = nativeObject.ComputeResult(0);
    StringBuilder result = new StringBuilder();
    foreach (var v in val)
    {
        result.Append(v).Append(System.Environment.NewLine);
    }
    this.Result1.Text = result.ToString();
}
```

Добавьте обработчик событий для упорядоченных результатов:

```csharp
async private void PrimesOrderedButton_Click_1(object sender, RoutedEventArgs e)
{
    var nativeObject = new WinRT_CPP.Class1();

    StringBuilder sb = new StringBuilder();
    sb.Append("Primes found (ordered): ");

    PrimesOrderedResult.Text = sb.ToString();

    // Call the asynchronous method
    var asyncOp = nativeObject.GetPrimesOrdered(2, 100000);

    // Before awaiting, provide a lambda or named method
    // to handle the Progress event that is fired at regular
    // intervals by the asyncOp object. This handler updates
    // the progress bar in the UI.
    asyncOp.Progress = (asyncInfo, progress) =>
        {
            PrimesOrderedProgress.Value = progress;
        };

    // Wait for the operation to complete
    var asyncResult = await asyncOp;

    // Convert the results to strings
    foreach (var result in asyncResult)
    {
        sb.Append(result).Append(" ");
    }

    // Display the results
    PrimesOrderedResult.Text = sb.ToString();
}
```

Добавьте обработчик событий для неупорядоченных результатов и для кнопки, которая очищает результаты, чтобы код можно было запускать снова.

```csharp
private void PrimesUnOrderedButton_Click_1(object sender, RoutedEventArgs e)
{
    var nativeObject = new WinRT_CPP.Class1();

    StringBuilder sb = new StringBuilder();
    sb.Append("Primes found (unordered): ");
    PrimesUnOrderedResult.Text = sb.ToString();

    // primeFoundEvent is a user-defined event in nativeObject
    // It passes the results back to this thread as they are produced
    // and the event handler that we define here immediately displays them.
    nativeObject.primeFoundEvent += (n) =>
    {
        sb.Append(n.ToString()).Append(" ");
        PrimesUnOrderedResult.Text = sb.ToString();
    };

    // Call the async method.
    var asyncResult = nativeObject.GetPrimesUnordered(2, 100000);

    // Provide a handler for the Progress event that the asyncResult
    // object fires at regular intervals. This handler updates the progress bar.
    asyncResult.Progress += (asyncInfo, progress) =>
        {
            PrimesUnOrderedProgress.Value = progress;
        };
}

private void Clear_Button_Click(object sender, RoutedEventArgs e)
{
    PrimesOrderedProgress.Value = 0;
    PrimesUnOrderedProgress.Value = 0;
    PrimesUnOrderedResult.Text = "";
    PrimesOrderedResult.Text = "";
    Result1.Text = "";
}
```

## <a name="running-the-app"></a>Запуск приложения
Выберите проект C# или проект JavaScript в качестве запускаемого проекта, открыв контекстное меню узла проекта в Обозревателе решений и выбрав команду **Назначить запускаемым проектом**. Затем нажмите клавишу F5, чтобы запустить программу с отладкой, или CTRL+F5, чтобы запустить ее без отладки.

## <a name="inspecting-your-component-in-object-browser-optional"></a>Проверка компонентов в Обозревателе объектов (необязательно)
В Обозревателе объектов можно проверять все типы среды выполнения Windows, определенные в файлах WinMD. Сюда входят типы из пространства имен «Platform», а также из пространства имен по умолчанию. Однако, поскольку типы в пространстве имен Platform::Collections определены в файле заголовков collections.h, а не в файле WinMD, они не отображаются в Обозревателе объектов.

## **<a name="to-inspect-a-component"></a>Проверка компонента**
В строке меню выберите **Вид, Обозреватель объектов** (Ctrl+Alt+J).

На левой панели Обозревателя объектов разверните узел WinRT\_CPP, чтобы увидеть типы и методы, которые определены в компоненте.

## <a name="debugging-tips"></a>Советы по отладке
Для более эффективной отладки скачайте символы отладки с открытых серверов символов Майкрософт.

## **<a name="to-download-debugging-symbols"></a>Скачивание символов отладки**
В строке меню выберите **Сервис, Параметры**.

В диалоговом окне **Параметры** разверните узел **Отладка** и выберите **Символы**.

Установите флажок **Серверы символов Майкрософт** и нажмите кнопку **OK**.

Первоначальное скачивание символов может занять некоторое время. Для повышения производительности при следующем нажатии клавиши F5 укажите локальный каталог, в котором следует кэшировать символы.

При отладке решения JavaScript, содержащего библиотеку DLL компонента, можно настроить отладчик для пошагового выполнения скрипта или машинного кода в компоненте, однако нельзя отлаживать эти части одновременно. Чтобы изменить параметр, откройте контекстное меню узла проекта JavaScript в Обозревателе решений и выберите пункт **Свойства, Отладка, Тип отладчика**.

Обязательно выберите соответствующие возможности в конструкторе пакетов. Открыть конструктор пакетов можно, запустив файл Package.appxmanifest. Например, если вы пытаетесь программным образом обратиться к файлам в папке «Рисунки», не забудьте установить флажок **Библиотека изображений** в области **Возможности** конструктора пакетов.

Если коду JavaScript не удается распознать открытые свойства или методы в компоненте, убедитесь, что в JavaScript используется «верблюжий» стиль имен. Например, метод `ComputeResult` C++ следует вызывать из JavaScript как `computeResult`.

При удалении проекта компонента среды выполнения Windows C++ из решения необходимо также вручную удалить ссылку на проект из проекта JavaScript. Невыполнение этого требования приведет к невозможности последующей отладки и сборки. При необходимости можно добавить ссылку на сборку в библиотеку DLL.

## <a name="related-topics"></a>Смежные разделы
* [Создание компонентов среды выполнения Windows на C++/CX](creating-windows-runtime-components-in-cpp.md)
