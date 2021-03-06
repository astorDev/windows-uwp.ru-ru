---
ms.assetid: 34C00F9F-2196-46A3-A32F-0067AB48291B
description: В этой статье описываются рекомендации по использованию асинхронных методов в расширениях компонентов Visual C++ (C++/CX) с помощью класса task, определенного в пространстве имен concurrency файла ppltasks.h.
title: Асинхронное программирование на языке C++
ms.date: 05/14/2018
ms.topic: article
keywords: windows 10, uwp, потоки, синхронный, C++
ms.localizationpriority: medium
ms.openlocfilehash: 73268a2aab0212277fb1caa158220cb7dbc180bf
ms.sourcegitcommit: ef723e3d6b1b67213c78da696838a920c66d5d30
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/02/2020
ms.locfileid: "82730064"
---
# <a name="asynchronous-programming-in-ccx"></a>Асинхронное программирование на языке C++/CX
> [!NOTE]
> В этом разделе представлена вспомогательная информация для поддержки приложений на C++/CX. Однако в новых приложениях мы рекомендуем использовать [C++/WinRT](../cpp-and-winrt-apis/intro-to-using-cpp-with-winrt.md). C++/WinRT — это полностью стандартная проекция языка C++17 для API среды выполнения Windows (WinRT), реализованная как библиотека на основе файлов заголовков и предназначенная для предоставления вам первоклассного доступа к современным интерфейсам API Windows.

В этой статье описываются рекомендации по использованию асинхронных методов в расширениях компонентов Visual C++ (C++/CX) с помощью класса `task`, определенного в пространстве имен `concurrency` файла ppltasks.h.

## <a name="universal-windows-platform-uwp-asynchronous-types"></a>Асинхронные типы универсальной платформы для Windows (UWP)
Универсальная платформа Windows (UWP) имеет четкую модель вызова асинхронных методов и предоставляет типы для использования таких методов. Если вы не знакомы с асинхронной моделью UWP, перед чтением этой статьи ознакомьтесь со статьей [Асинхронное программирование][AsyncProgramming].

Хотя асинхронные API-интерфейсы среда выполнения Windows можно использовать непосредственно в C++, предпочтительным подходом является использование [**класса Task**][task-class] и связанных с ним типов и функций, которые содержатся в пространстве имен [**Concurrency**][concurrencyNamespace] и определены в `<ppltasks.h>`. **concurrency::task** — это тип общего назначения, но если используется параметр компилятора **/ZW**, необходимый для компонентов и приложений универсальной платформы для Windows (UWP), то данный класс инкапсулирует асинхронные типы UWP. Благодаря этому легче:

-   создавать цепочки из нескольких синхронных и асинхронных операций;

-   обрабатывать исключения в цепочках задач;

-   выполнять отмену в цепочках задач;

-   запускать отдельные задачи в нужном контексте потока или подразделении.

Эта статья содержит базовое руководство по использованию класса **task** с асинхронными API UWP. Более полную документацию по **задаче** и ее связанным методам, включая [**Создание\_задачи**][createTask], см. в разделе [параллелизм задач (среда выполнения с параллелизмом)][taskParallelism]. 

## <a name="consuming-an-async-operation-by-using-a-task"></a>Запуск асинхронных операций с помощью задач
В следующем примере показано, как с помощью класса task использовать метод **async**, который возвращает интерфейс [**IAsyncOperation**][IAsyncOperation] и результатом действия которого является некоторое значение. Ниже перечислены основные шаги.

1.  Вызовите метод `create_task` и передайте ему объект **IAsyncOperation^**.

2.  Вызовите функцию-член [**task::then**][taskThen] класса task и задайте лямбда-функцию, которая будет вызвана, когда завершится асинхронная операция.

``` cpp
#include <ppltasks.h>
using namespace concurrency;
using namespace Windows::Devices::Enumeration;
...
void App::TestAsync()
{    
    //Call the *Async method that starts the operation.
    IAsyncOperation<DeviceInformationCollection^>^ deviceOp =
        DeviceInformation::FindAllAsync();

    // Explicit construction. (Not recommended)
    // Pass the IAsyncOperation to a task constructor.
    // task<DeviceInformationCollection^> deviceEnumTask(deviceOp);

    // Recommended:
    auto deviceEnumTask = create_task(deviceOp);

    // Call the task's .then member function, and provide
    // the lambda to be invoked when the async operation completes.
    deviceEnumTask.then( [this] (DeviceInformationCollection^ devices )
    {       
        for(int i = 0; i < devices->Size; i++)
        {
            DeviceInformation^ di = devices->GetAt(i);
            // Do something with di...          
        }       
    }); // end lambda
    // Continue doing work or return...
}
```

Задача, созданная и возвращенная функцией [**task::then**][taskThen], называется *задачей-продолжением*. Входным аргументом для пользовательской лямбда-функции в данном случае является результат, который возвращает операция задачи после завершения. Этот же результат был бы получен при вызове [**IAsyncOperation::GetResults**](https://docs.microsoft.com/uwp/api/Windows.Foundation.IAsyncOperation_TResult_#Windows_Foundation_IAsyncOperation_1_GetResults), если бы вы непосредственно использовали интерфейс **IAsyncOperation**.

Возврат из метода [**task::then**][taskThen] выполняется немедленно, а его делегат не выполняется до тех пор, пока асинхронная операция не завершится успешно. Если в этом примере во время выполнения асинхронной операции возникнет исключение или она завершится в отмененном состоянии (в результате запроса на отмену), задача-продолжение не будет выполняться. Позже мы рассмотрим, как писать задачи-продолжения, которые выполняются, даже если предыдущая задача завершилась неудачно или была отменена.

Хотя вы объявляете переменную task в локальном стеке, она самостоятельно управляет своим временем существования и не удаляется до тех пор, пока все операции не будут завершены и все ссылки на нее не исчезнут из области видимости, даже если возврат из метода происходит до завершения операции.

## <a name="creating-a-chain-of-tasks"></a>Создание цепочки задач
Одной из распространенных практик асинхронного программирования является задание последовательности операций, называемой *цепочкой задач*, в которой каждая задача-продолжение выполняется только после завершения предыдущей. В некоторых случаях входным параметром для дополнительной задачи является результат выполнения предыдущей задачи (называемой также *задачей-предшественником*). Используя метод [**task::then**][taskThen], вы можете создавать цепочки задач простым и наглядным способом. Этот метод возвращает **task<T>**, где **T** — тип значения, возвращаемого лямбда-функцией. В цепочку задач можно составить несколько продолжений:`myTask.then(…).then(…).then(…);`

Цепочки задач удобно использовать, когда задача-продолжение создает новую асинхронную операцию. Такая задача называется асинхронной. В следующем примере показана цепочка, состоящая из двух задач-продолжений. Начальная задача получает дескриптор существующего файла. Когда эта операция завершается, первая задача-продолжение запускает новую асинхронную операцию, удаляющую файл. Когда эта операция завершается, запускается вторая задача-продолжение, отображающая подтверждение.

``` cpp
#include <ppltasks.h>
using namespace concurrency;
...
void App::DeleteWithTasks(String^ fileName)
{    
    using namespace Windows::Storage;
    StorageFolder^ localFolder = ApplicationData::Current->LocalFolder;
    auto getFileTask = create_task(localFolder->GetFileAsync(fileName));

    getFileTask.then([](StorageFile^ storageFileSample) ->IAsyncAction^ {       
        return storageFileSample->DeleteAsync();
    }).then([](void) {
        OutputDebugString(L"File deleted.");
    });
}
```

Приведенный пример иллюстрирует четыре важных момента.

-   Первая задача-продолжение преобразует объект [**IAsyncAction^**][IAsyncAction] в **task<void>** и возвращает **task**.

-   Вторая задача-продолжение не производит обработку ошибок, а потому в качестве параметра принимает **void**, а не **task<void>**. Эта задача-продолжение основывается на значении предыдущей.

-   Вторая задача-продолжение не выполняется до тех пор, пока не закончится операция [**DeleteAsync**][deleteAsync].

-   Поскольку вторая задача-продолжение основывается на значении предыдущей, возникновение исключения в операции, запускаемой вызовом функции [**DeleteAsync**][deleteAsync], приведет к тому, что вторая задача-продолжение не запустится.

**Примечание**  . Создание цепочки задач — лишь один из способов использования класса **Task** для составления асинхронных операций. Операции можно также создавать с помощью операторов **&&** Join и Choice. **||** Дополнительные сведения см. в разделе [Параллелизм задач (среда выполнения с параллелизмом)][taskParallelism].

## <a name="lambda-function-return-types-and-task-return-types"></a>Типы значений, которые возвращают лямбда-функция и задача
В задаче-продолжении тип значения, которое возвращает лямбда-функция, упаковывается в объект **task**. Если лямбда-функция возвращает значение типа **double**, то задача-продолжение возвращает значение типа **task<double>**. Но объект task разработан таким образом, что не возвращает без необходимости значения вложенных типов. Если лямбда-функция возвращает значение **IAsyncOperation<SyndicationFeed^>^**, задача-продолжение возвращает значение **task<SyndicationFeed^>**, а не **task<task<SyndicationFeed^>>** или **task<IAsyncOperation<SyndicationFeed^>^>^**. Этот процесс называется *асинхронной распаковкой*. Он также обеспечивает завершение асинхронной операции в задаче-продолжении до того, как будет вызвана следующая задача-продолжение.

Обратите внимание, что в приведенном выше примере задача возвращает **task<void>**, хотя ее лямбда-функция вернула объект [**IAsyncInfo**][IAsyncInfo]. В следующей таблице приводятся типы значений, возвращаемых лямбда-функцией, и соответствующие им типы значений, возвращаемых задачей, в которую вложена лямбда-функция.

| | |
|--------------------------------------------------------|---------------------|
| Тип возвращаемого значения лямбда-функции                                     | `.then`Тип возвращаемого значения |
| TResult                                                | задачи<TResult> |
| IAsyncOperation<TResult>^                        | задачи<TResult> |
| IAsyncOperationWithProgress<TResult, TProgress>^ | задачи<TResult> |
|IAsyncAction^                                           | задачи<void>    |
| IAsyncActionWithProgress<TProgress>^             |задачи<void>     |
| задачи<TResult>                                    |задачи<TResult>  |


## <a name="canceling-tasks"></a>Отмена задач
Часто бывает нужно дать пользователю возможность отменить асинхронную операцию. А иногда возникает необходимость отменить асинхронную операцию программным путем извне цепочки задач. Хотя каждый \* **асинхронный** тип возвращаемого значения имеет метод [**Cancel**][IAsyncInfoCancel] , который он наследует от [**иасинЦинфо**][IAsyncInfo], неудобно предоставлять его внешним методам. Предпочтительным способом поддержки отмены в цепочке задач является использование [**источника токена\_отмены\_**](https://docs.microsoft.com/cpp/parallel/concrt/reference/cancellation-token-source-class) для создания [**токена отмены\_**](https://docs.microsoft.com/cpp/parallel/concrt/reference/cancellation-token-class), а затем передача маркера конструктору начальной задачи. Если асинхронная задача создается с токеном отмены и вызывается [**токен\_\_отмены Source:: Cancel**](https://docs.microsoft.com/cpp/parallel/concrt/reference/cancellation-token-source-class?view=vs-2017) , задача автоматически вызывает **Cancel** для операции **IAsync\* ** и передает запрос об отмене в цепочку продолжения. Следующий псевдокод иллюстрирует этот подход.

``` cpp
//Class member:
cancellation_token_source m_fileTaskTokenSource;

// Cancel button event handler:
m_fileTaskTokenSource.cancel();

// task chain
auto getFileTask2 = create_task(documentsFolder->GetFileAsync(fileName),
                                m_fileTaskTokenSource.get_token());
//getFileTask2.then ...
```

При отмене задачи исключение [**отмененной задачи\_**][taskCanceled] распространяется вниз по цепочке задач. Задачи-продолжения, основанные на предыдущих значениях, просто не запустятся, а в безусловных задачах-продолжениях возникнет исключение при вызове [**task::get**][taskGet]. При возникновении продолжения обработки ошибок убедитесь, что он явно перехватывает исключение **Отмена задачи\_** . (Это исключение не является производным от [**Platform::Exception**](https://docs.microsoft.com/cpp/cppcx/platform-exception-class).)

Отмена выполняется для всех задач. Если ваша задача-продолжение не просто вызывает метод UWP, а выполняет какие-то длительные операции, необходимо периодически проверять состояние маркера отмены и останавливать выполнение при отмене. После очистки всех ресурсов, которые были выделены в продолжении, вызовите [**\_Отмена текущей\_задачи**](https://docs.microsoft.com/cpp/parallel/concrt/reference/concurrency-namespace-functions?view=vs-2017) , чтобы отменить эту задачу и распространить отмену до любых продолжений на основе значений. Другой пример: вы можете создать цепочку задач, представляющую результат операции [**FileSavePicker**](https://docs.microsoft.com/uwp/api/Windows.Storage.Pickers.FileSavePicker). Если пользователь нажимает кнопку **Отмена**, метод [**IAsyncInfo::Cancel**][IAsyncInfoCancel] не вызывается. Вместо этого операция успешно завершается, но возвращает **nullptr**. Продолжение может проверить входной параметр и вызвать **отмену\_\_текущей задачи** , если входные данные имеют значение **nullptr**.

Подробнее см. в разделе [Отмена в библиотеке параллельных шаблонов](https://docs.microsoft.com/cpp/parallel/concrt/cancellation-in-the-ppl).

## <a name="handling-errors-in-a-task-chain"></a>Обработка ошибок в цепочках задач
Если вы хотите, чтобы задача-продолжение выполнялась, даже если задача-предшественник отменена или в ней возникло исключение, сделайте задачу-продолжение безусловной, указав в качестве входного параметра для ее лямбда-функции **task<TResult>** или **task<void>** (если лямбда-функция задачи-предшественника возвращает [**IAsyncAction^**][IAsyncAction]).

Для обработки ошибок и отмены в цепочке задач нет необходимости делать каждую задачу-продолжение безусловной или помещать каждую операцию, которая может вызвать исключение, в блок `try…catch`. Вместо этого вы можете добавить одну безусловную задачу-продолжение в конец цепочки и обрабатывать все ошибки в ней. Любое исключение — это включает в себя исключение " [**\_задача отменена**][taskCanceled] " — распространяет цепочку задач и обходит все продолжения на основе значений, чтобы вы могли обработать ее в продолжении по задаче обработки ошибок. Перепишем предыдущий пример с использованием безусловной задачи-продолжения, обрабатывающей ошибки:

``` cpp
#include <ppltasks.h>
void App::DeleteWithTasksHandleErrors(String^ fileName)
{    
    using namespace Windows::Storage;
    using namespace concurrency;

    StorageFolder^ documentsFolder = KnownFolders::DocumentsLibrary;
    auto getFileTask = create_task(documentsFolder->GetFileAsync(fileName));

    getFileTask.then([](StorageFile^ storageFileSample)
    {       
        return storageFileSample->DeleteAsync();
    })

    .then([](task<void> t)
    {

        try
        {
            t.get();
            // .get() didn' t throw, so we succeeded.
            OutputDebugString(L"File deleted.");
        }
        catch (Platform::COMException^ e)
        {
            //Example output: The system cannot find the specified file.
            OutputDebugString(e->Message->Data());
        }

    });
}
```

В безусловной задаче-продолжении для получения результатов задачи мы вызываем функцию-член [**task::get**][taskGet]. Нам все еще нужно вызывать **task::get**, даже если операция имела тип [**IAsyncAction**][IAsyncAction] и не возвращала результат, так как **task::get** также перехватывает все исключения, проходящие по цепочке задач. Если входная задача сохраняет исключение, то оно возникает при вызове **task::get**. Если вы не вызываете **Task:: Get**или не используете продолжение на основе задачи в конце цепочки или не перехватите созданного типа исключения, то при удалении всех ссылок на задачу возникает **исключение\_незамеченной задачи\_** .

Перехватывайте только те исключения, которые можете обработать. Если в приложении возникает ошибка, которую не удается устранить, лучше дать программе аварийно завершиться, чем оставить ее работать в неизвестном состоянии. Кроме того, в общем случае не пытайтесь перехватить **исключение незамеченной\_задачи\_** . Оно предназначено главным образом для диагностики. При возникновении **исключения\_незамеченной задачи\_** оно обычно указывает на ошибку в коде. Причиной этого часто является либо исключение, которое должно быть обработано, либо неустранимое исключение, вызванное какой-то другой ошибкой в коде.

## <a name="managing-the-thread-context"></a>Управление контекстом потока
Пользовательский интерфейс приложений UWP работает в однопотоковом подразделении (STA). Задача, лямбда-функция которой возвращает [**IAsyncAction**][IAsyncAction] или [**IAsyncOperation**][IAsyncOperation], поддерживает подразделения. Если задача создается в однопотоковом подразделении, то если вы не укажете иное, в нем же по умолчанию работают и все ее продолжения. Другими словами, вся цепочка задач наследует модель работы в подразделении от родительской задачи. Это упрощает взаимодействие с пользовательским интерфейсом, доступ к которому возможен только из STA.

Например, в функции-члене любого класса, представляющего страницу XAML, в приложении UWP вы можете заполнить элемент управления [**ListBox**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.ListBox) в методе [**task::then**][taskThen], не используя объект [**Dispatcher**](https://docs.microsoft.com/uwp/api/Windows.UI.Core.CoreDispatcher).

``` cpp
#include <ppltasks.h>
void App::SetFeedText()
{    
    using namespace Windows::Web::Syndication;
    using namespace concurrency;
    String^ url = "http://windowsteamblog.com/windows_phone/b/wmdev/atom.aspx";
    SyndicationClient^ client = ref new SyndicationClient();
    auto feedOp = client->RetrieveFeedAsync(ref new Uri(url));

    create_task(feedOp).then([this]  (SyndicationFeed^ feed)
    {
        m_TextBlock1->Text = feed->Title->Text;
    });
}
```

Если задача не возвращает [**IAsyncAction**][IAsyncAction] или [**IAsyncOperation**][IAsyncOperation], то она не поддерживает подразделения и по умолчанию ее задачи-продолжения работают в первом доступном фоновом потоке.

Можно переопределить контекст потока по умолчанию для любой из видов задач, используя перегрузку [**Task:: then**][taskThen] , которая принимает [**контекст\_продолжения\_задачи**](https://docs.microsoft.com/cpp/parallel/concrt/reference/task-continuation-context-class). Например, в некоторых случаях для задачи, поддерживающей подразделения, желательно запланировать работу задачи-продолжения в фоновом потоке. В этом случае можно передать [**контекст продолжения\_\_задачи:: использовать\_произвольный**][useArbitrary] параметр, чтобы запланировать работу задачи в следующем доступном потоке в многопоточном апартаменте. Это может повысить производительность задачи-продолжения, поскольку ее работу не нужно синхронизировать с другими действиями в потоке пользовательского интерфейса.

В следующем примере показано, когда можно указать [**контекст продолжения\_\_задачи:\_использовать произвольный**][useArbitrary] параметр, а также показывает, как контекст продолжения по умолчанию полезен для синхронизации параллельных операций с коллекциями, не являющимися потокобезопасными. В приведенном фрагменте кода мы циклически обрабатываем список URL-адресов для RSS-каналов и для каждого адреса запускаем асинхронную операцию, чтобы получить данные этого веб-канала. Мы не можем контролировать порядок, в котором обрабатываются веб-каналы, и это нас не интересует. После завершения каждой операции [**RetrieveFeedAsync**](https://docs.microsoft.com/uwp/api/windows.web.syndication.isyndicationclient.retrievefeedasync) первая задача-продолжение принимает объект [**SyndicationFeed^**](https://docs.microsoft.com/uwp/api/Windows.Web.Syndication.SyndicationFeed) и использует его для инициализации установленного объекта `FeedData^` приложения. Так как каждая из этих операций независима от других, мы можем ускорить процесс, указав **контекст продолжения\_\_задачи:: использовать\_произвольный** контекст продолжения. Но после инициализации каждого объекта `FeedData` нам нужно добавить его в [**Vector**](https://docs.microsoft.com/cpp/cppcx/platform-collections-vector-class), который не является потокобезопасной коллекцией. Поэтому мы создаем продолжение и указываем [**контекст продолжения\_\_задачи::\_использовать Current**](https://docs.microsoft.com/cpp/parallel/concrt/reference/task-continuation-context-class?view=vs-2017) , чтобы гарантировать, что все вызовы, добавляемые в один и тот же контекст однопотокового подразделения (ASTA), будут выполняться в одном приложении. [**Append**](https://docs.microsoft.com/uwp/api/windows.foundation.collections.ivector_t_.append) Так [**как\_контекст\_продолжения задачи: использовать\_значение по умолчанию**](https://docs.microsoft.com/cpp/parallel/concrt/reference/task-continuation-context-class?view=vs-2017) является контекстом по умолчанию, нам не нужно явно указывать его, но мы делаем это для ясности.

``` cpp
#include <ppltasks.h>
void App::InitDataSource(Vector<Object^>^ feedList, vector<wstring> urls)
{
                using namespace concurrency;
    SyndicationClient^ client = ref new SyndicationClient();

    std::for_each(std::begin(urls), std::end(urls), [=,this] (std::wstring url)
    {
        // Create the async operation. feedOp is an
        // IAsyncOperationWithProgress<SyndicationFeed^, RetrievalProgress>^
        // but we don't handle progress in this example.

        auto feedUri = ref new Uri(ref new String(url.c_str()));
        auto feedOp = client->RetrieveFeedAsync(feedUri);

        // Create the task object and pass it the async operation.
        // SyndicationFeed^ is the type of the return value
        // that the feedOp operation will eventually produce.

        // Then, initialize a FeedData object by using the feed info. Each
        // operation is independent and does not have to happen on the
        // UI thread. Therefore, we specify use_arbitrary.
        create_task(feedOp).then([this]  (SyndicationFeed^ feed) -> FeedData^
        {
            return GetFeedData(feed);
        }, task_continuation_context::use_arbitrary())

        // Append the initialized FeedData object to the list
        // that is the data source for the items collection.
        // This all has to happen on the same thread.
        // By using the use_default context, we can append
        // safely to the Vector without taking an explicit lock.
        .then([feedList] (FeedData^ fd)
        {
            feedList->Append(fd);
            OutputDebugString(fd->Title->Data());
        }, task_continuation_context::use_default())

        // The last continuation serves as an error handler. The
        // call to get() will surface any exceptions that were raised
        // at any point in the task chain.
        .then( [this] (task<void> t)
        {
            try
            {
                t.get();
            }
            catch(Platform::InvalidArgumentException^ e)
            {
                //TODO handle error.
                OutputDebugString(e->Message->Data());
            }
        }); //end task chain

    }); //end std::for_each
}
```

Вложенные задачи (то есть задачи, которые создаются внутри задачи-продолжения) не наследуют у исходной задачи модель работы в подразделении.

## <a name="handing-progress-updates"></a>Обработка информации о ходе выполнения операции
Методы, поддерживающие [**IAsyncOperationWithProgress**](https://docs.microsoft.com/uwp/api/Windows.Foundation.IAsyncOperationWithProgress_TResult_TProgress_) или [**IAsyncActionWithProgress**](https://docs.microsoft.com/uwp/api/Windows.Foundation.IAsyncActionWithProgress_TProgress_), могут периодически передавать информацию о ходе выполнения операции. Эта информация передается независимо от состояния задач и задач-продолжений. Нужно просто указать делегат для свойства [**Progress**](https://docs.microsoft.com/uwp/api/Windows.Foundation.IAsyncOperationWithProgress_TResult_TProgress_) объекта. Этот делегат обычно используется для обновления индикатора выполнения в пользовательском интерфейсе.

## <a name="related-topics"></a>Связанные темы
* [Создание асинхронных операций на языке C++/CX для приложений UWP](https://docs.microsoft.com/cpp/parallel/concrt/creating-asynchronous-operations-in-cpp-for-windows-store-apps)
* [Справочник по языку Visual C++](https://docs.microsoft.com/cpp/cppcx/visual-c-language-reference-c-cx)
* [Асинхронное программирование][AsyncProgramming]
* [Параллелизм задач (среда выполнения с параллелизмом)][taskParallelism]
* [concurrency::task](/cpp/parallel/concrt/reference/task-class)

<!-- LINKS -->
[AsyncProgramming]: <https://docs.microsoft.com/windows/uwp/threading-async/asynchronous-programming-universal-windows-platform-apps> "AsyncProgramming"
[concurrencyNamespace]: <https://docs.microsoft.com/cpp/parallel/concrt/reference/concurrency-namespace> "Пространство имен Concurrency"
[createTask]: <https://docs.microsoft.com/cpp/parallel/concrt/reference/concurrency-namespace-functions#create_task> "CreateTask"
[deleteAsync]: <https://msdn.microsoft.com/library/windows/apps/BR227199> "DeleteAsync"
[IAsyncAction]: <https://msdn.microsoft.com/library/windows/apps/windows.foundation.iasyncaction.aspx> "IAsyncAction"
[IAsyncOperation]: <https://msdn.microsoft.com/library/windows/apps/BR206598> "IAsyncOperation"
[IAsyncInfo]: <https://msdn.microsoft.com/library/windows/apps/BR206587> "IAsyncInfo"
[IAsyncInfoCancel]: <https://msdn.microsoft.com/library/windows/apps/windows.foundation.iasyncinfo.cancel> "IAsyncInfoCancel"
[taskCanceled]: <https://docs.microsoft.com/cpp/parallel/concrt/reference/task-canceled-class> "TaskCancelled"
[task-class]: <https://docs.microsoft.com/cpp/parallel/concrt/reference/task-class#get> "Класс Task"
[taskGet]: <https://msdn.microsoft.com/library/windows/apps/xaml/hh750017.aspx> "TaskGet"
[taskParallelism]: <https://docs.microsoft.com/cpp/parallel/concrt/task-parallelism-concurrency-runtime> "Параллельное выполнение задач"
[taskThen]: <https://docs.microsoft.com/cpp/parallel/concrt/reference/task-class#then> "TaskThen"
[useArbitrary]: <https://msdn.microsoft.com/library/windows/apps/xaml/hh750036.aspx> "UseArbitrary"
