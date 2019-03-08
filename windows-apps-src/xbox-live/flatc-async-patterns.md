---
title: Шаблоны асинхронного вызова интерфейса C API
description: Узнайте, асинхронный интерфейс API C, вызывая шаблоны для XSAPI C API
ms.date: 06/10/2018
ms.topic: article
keywords: Xbox live, xbox, игры, универсальной платформы Windows, windows 10, xbox, один, программа для разработчиков,
ms.localizationpriority: medium
ms.openlocfilehash: edc6248a363b844d94c8fa03ab7ce071cc941908
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57608009"
---
# <a name="calling-pattern-for-xsapi-flat-c-layer-async-calls"></a>Шаблон вызова для XSAPI неструктурированный C слоя асинхронных вызовов

**Асинхронный интерфейс API** — это API, который возвращает быстро, но запускается в **асинхронная задача** и результат возвращается после завершения задачи.

В большинстве игр мало управления через какой поток выполняет **асинхронная задача** и какой поток возвращает результаты при использовании **обратный вызов завершения**.  Некоторые игры разработаны таким образом, чтобы часть кучи затрагивается только из одного потока, чтобы избежать необходимости синхронизации потоков. Если **обратный вызов завершения** не вызывается из потока игры элементы управления, обновление общего состояния с результатом **асинхронная задача** потребует синхронизации потоков.

API-Интерфейс C XSAPI предоставляет новый асинхронный C API, которая предоставляет разработчикам прямого потока управления, при выполнении **асинхронный интерфейс API** вызова, таких как **XblSocialGetSocialRelationshipsAsync()**,  **XblProfileGetUserProfileAsync()** и **XblAchievementsGetAchievementsForTitleIdAsync()**.

Ниже приведен простой пример вызова **XblProfileGetUserProfileAsync** API.

```cpp
AsyncBlock* asyncBlock = new AsyncBlock {};
asyncBlock->queue = asyncQueue;
asyncBlock->context = customDataForCallback;
asyncBlock->callback = [](AsyncBlock* asyncBlock)
{
    XblUserProfile profile;
    if( SUCCEEDED( XblProfileGetUserProfileResult(asyncBlock, &profile) ) )
    {
        printf("Profile retrieved successfully\r\n");
    }
    delete asyncBlock;
};
XblProfileGetUserProfileAsync(asyncBlock, xboxLiveContext, xuid);
```

Чтобы понять этот шаблон вызова, необходимо понять, как использовать **AsyncBlock** и **AsyncQueue**.

* **AsyncBlock** содержит все сведения, относящиеся к **асинхронная задача** и **обратный вызов завершения**.

* **AsyncQueue** позволяет определить, какой поток выполняет **асинхронная задача** и какой поток вызывает AsyncBlock **обратный вызов завершения**.

## <a name="the-asyncblock"></a>**AsyncBlock**

Давайте рассмотрим **AsyncBlock** подробно. Он является структурой, определяются следующим образом:

```cpp
typedef struct AsyncBlock
{
    AsyncCompletionRoutine* callback;
    void* context;
    async_queue_handle_t queue;
} AsyncBlock;
```

**AsyncBlock** содержит:

* *обратный вызов* — необязательная функция обратного вызова, вызываемый после завершения асинхронной работы.  Если не указать функцию обратного вызова, можно дождаться **AsyncBlock** расходует **GetAsyncStatus** и затем получает результаты.
* *контекст* -позволяет передавать данные в функцию обратного вызова.
* *очередь* — это async_queue_handle_t, являющийся дескриптором назначение **AsyncQueue**. Если это значение не задано, используется очередь по умолчанию.

Необходимо создать новый AsyncBlock в куче для каждого асинхронного вызова API.  AsyncBlock должны находиться, пока не будет вызван обратный вызов завершения AsyncBlock и затем его можно удалить.

> [!IMPORTANT]
> **AsyncBlock** должны оставаться в памяти до **асинхронная задача** завершения. Если он выделяется динамически, его можно удалить внутри AsyncBlock **обратный вызов завершения**.

### <a name="waiting-for-asynchronous-task"></a>Ожидание **асинхронной задачи**

Чтобы узнать **асинхронная задача** — это выполнить ряд различных способов:

* AsyncBlock **обратный вызов завершения** называется
* Вызовите **GetAsyncStatus** значением ИСТИНА, чтобы дождаться его завершения.
* Задать waitEvent в **AsyncBlock** и ожидания сигнала события

С помощью **GetAsyncStatus** и waitEvent, **асинхронная задача** считается завершенной после AsyncBlock **обратный вызов завершения** выполняет тем не менее AsyncBlock **обратный вызов завершения** является необязательным.

Один раз **асинхронная задача** будет завершено, можно получить результаты.

### <a name="getting-the-result-of-the-asynchronous-task"></a>Получение результата **асинхронной задачи**

Чтобы получить результат, большинство **асинхронный интерфейс API** функции имеют соответствующий \[функция Name\]привести функцию, чтобы получить результат асинхронного вызова. В нашем примере кода **XblProfileGetUserProfileAsync** имеет соответствующий **XblProfileGetUserProfileResult** функции. Эта функция используется для возврата результата функции и действовать соответствующим образом.  См. в разделе документации каждого **асинхронный интерфейс API** функция полные сведения о получении результатов.

## <a name="the-asyncqueue"></a>**AsyncQueue**

**AsyncQueue** позволяет определить, какой поток выполняет **асинхронная задача** и какой поток вызывает AsyncBlock **обратный вызов завершения**.

Вы можете управлять, какой поток выполняет эти операции, задав *режим диспетчеризации*. Доступны три режима диспетчеризации:

* *Вручную* -вручную очереди не переданных автоматически.  Это разработчик должен отправлять их в любом потоке нужные им. Это можно использовать для назначения рабочих или обратного вызова на стороне асинхронного вызова для определенного потока.  Этот вопрос рассматривается более подробно ниже.

* *Пул потоков* - отправок, использование пула потоков.  Пул потоков вызывает вызовы в параллельном режиме, используя вызов на выполнение из очереди, в свою очередь, мере освобождения потоков threadpool.  Это easist для использования, но дает наименьший объем элемента управления, по которому потока используется.

* *Поток фиксированной* — с помощью QueueUserAPC в потоке, создавшем очередь асинхронных отправок. Когда APC пользовательского режима находится в очереди, поток не направляется для вызова функции APC, если он находится в состоянии извещающие процедуры. Поток переходит в состояние извещающие процедуры с помощью **SleepEx**, **SignalObjectAndWait**, **сбой в WaitForSingleObjectEx**, **WaitForMultipleObjectsEx**, или **MsgWaitForMultipleObjectsEx** для выполнения операции извещающие ожидания

Чтобы создать новую **AsyncQueue** вам потребуется вызвать **CreateAsyncQueue**.

```cpp
STDAPI CreateAsyncQueue(
    _In_ AsyncQueueDispatchMode workDispatchMode,
    _In_ AsyncQueueDispatchMode completionDispatchMode,
    _Out_ async_queue_handle_t* queue);
```

где AsyncQueueDispatchMode содержит три диспетчеризации режимы, представленный ранее:

```cpp
typedef enum AsyncQueueDispatchMode
{
    /// <summary>
    /// Async callbacks are invoked manually by DispatchAsyncQueue
    /// </summary>
    AsyncQueueDispatchMode_Manual,

    /// <summary>
    /// Async callbacks are queued to the thread that created the queue
    /// and will be processed when the thread is alertable.
    /// </summary>
    AsyncQueueDispatchMode_FixedThread,

    /// <summary>
    /// Async callbacks are queued to the system thread pool and will
    /// be processed in order by the threadpool.
    /// </summary>
    AsyncQueueDispatchMode_ThreadPool

} AsyncQueueDispatchMode;
```

**workDispatchMode** определяет режим диспетчеризации для потока, который обрабатывает работу в асинхронном режиме, хотя **completionDispatchMode** определяет режим диспетчеризации для потока, который обрабатывает завершение асинхронного программирования операция.

Можно также вызвать **CreateSharedAsyncQueue** для создания **AsyncQueue** с тем же типом очереди, указав идентификатор для очереди.

```cpp
STDAPI CreateSharedAsyncQueue(
    _In_ uint32_t id,
    _In_ AsyncQueueDispatchMode workerMode,
    _In_ AsyncQueueDispatchMode completionMode,
    _Out_ async_queue_handle_t* aQueue);
```

> [!NOTE]
> Есть ли уже очередь с режимами этот идентификатор и диспетчеризации, он понадобится нам.  В противном случае будет создаваться новая очередь.

После создания вашей **AsyncQueue** просто добавьте его **AsyncBlock** для управляют потоками с функциями работы и завершения.
При завершении работы с помощью **AsyncQueue**, обычно при завершении игры, ее можно закрыть с **CloseAsyncQueue**:

```cpp
STDAPI_(void) CloseAsyncQueue(
    _In_ async_queue_handle_t aQueue);
```

### <a name="manually-dispatching-an-asyncqueue"></a>Вручную диспетчеризации **AsyncQueue**

Если использовался режим диспетчеризации вручную очереди для **AsyncQueue** работы или завершения очереди, необходимо будет вручную диспетчеризации.
Сообщите нам сказать, что **AsyncQueue** был создан, устанавливаемый в очередь и очередью завершения отправляемое вручную следующим образом:

```cpp
CreateAsyncQueue(
    AsyncQueueDispatchMode_Manual,
    AsyncQueueDispatchMode_Manual,
    &queue);
```

Для распределения заданий, которой назначено **AsyncQueueDispatchMode_Manual** необходимо отправлять с помощью **DispatchAsyncQueue** функции.

```cpp
STDAPI_(bool) DispatchAsyncQueue(
    _In_ async_queue_handle_t queue,
    _In_ AsyncQueueCallbackType type,
    _In_ uint32_t timeoutInMs);
```

* *очередь* -для распределения заданий в очереди
* *Тип* — это экземпляр **AsyncQueueCallbackType** enum
* *timeoutInMs* -uint32_t времени ожидания в миллисекундах.

Существует два типа обратного вызова, определяется **AsyncQueueCallbackType** перечисления:

```cpp
typedef enum AsyncQueueCallbackType
{
    /// <summary>
    /// Async work callbacks
    /// </summary>
    AsyncQueueCallbackType_Work,

    /// <summary>
    /// Completion callbacks after work is done
    /// </summary>
    AsyncQueueCallbackType_Completion
} AsyncQueueCallbackType;
```

### <a name="when-to-call-dispatchasyncqueue"></a>Когда вызывать **DispatchAsyncQueue**

Чтобы проверить, когда очередь получила новый элемент можно вызвать **AddAsyncQueueCallbackSubmitted** задать обработчик событий, чтобы позволить кода знать, что работы или завершения готовы к отправке в связи.

```cpp
STDAPI AddAsyncQueueCallbackSubmitted(
    _In_ async_queue_handle_t queue,
    _In_opt_ void* context,
    _In_ AsyncQueueCallbackSubmitted* callback,
    _Out_ uint32_t* token);
```

**AddAsyncQueueCallbackSubmitted** принимает следующие параметры:

* *очередь* -очереди асинхронной отправке обратного вызова для.
* *контекст* -указатель на данные, которые нужно передать методу обратного вызова отправки.
* *обратный вызов* -функция, которая будет вызываться при отправке нового обратного вызова в очередь.
* *токен* -маркер, который будет использоваться в последующий вызов **RemoveAsynceCallbackSubmitted** для удаления обратного вызова.

Например, вот вызов **AddAsyncQueueCallbackSubmitted**:

`AddAsyncQueueCallbackSubmitted(queue, nullptr, HandleAsyncQueueCallback, &m_callbackToken);`

Соответствующий **AsyncQueueCallbackSubmitted** обратного вызова может быть реализован следующим образом:

```cpp
void CALLBACK HandleAsyncQueueCallback(
    _In_ void* context,
    _In_ async_queue_handle_t queue,
    _In_ AsyncQueueCallbackType type)
{
    switch (type)
    {
    case AsyncQueueCallbackType::AsyncQueueCallbackType_Work:
        ReleaseSemaphore(g_workReadyHandle, 1, nullptr);
        break;
    }
}
```

Затем в фоновом потоке можно прослушивать Этот семафор пробуждения и вызвать **DispatchAsyncQueue**.

```cpp
DWORD WINAPI BackgroundWorkThreadProc(LPVOID lpParam)
{
    HANDLE hEvents[2] =
    {
        g_workReadyHandle.get(),
        g_stopRequestedHandle.get()
    };

    async_queue_handle_t queue = static_cast<async_queue_handle_t>(lpParam);

    bool stop = false;
    while (!stop)
    {
        DWORD dwResult = WaitForMultipleObjectsEx(2, hEvents, false, INFINITE, false);
        switch (dwResult)
        {
        case WAIT_OBJECT_0: 
            // Background work is ready to be dispatched
            DispatchAsyncQueue(queue, AsyncQueueCallbackType_Work, 0);
            break;

        case WAIT_OBJECT_0 + 1:
        default:
            stop = true;
            break;
        }
    }

    CloseAsyncQueue(queue);
    return 0;
}
```

Рекомендуется использовать реализовать с помощью объекта семафора Win32.  Если вместо реализации с помощью объекта события Win32, то необходимо убедиться, не такие как пропустите все события с кодом:

```cpp
    case WAIT_OBJECT_0: 
        // Background work is ready to be dispatched
        DispatchAsyncQueue(queue, AsyncQueueCallbackType_Work, 0);        
        
        if (!IsAsyncQueueEmpty(queue, AsyncQueueCallbackType_Work))
        {
            // If there's more pending work, then set the event to process them
            SetEvent(g_workReadyHandle.get());
        }
        break;
```


Можно просмотреть пример рекомендации по интеграции async в [социальных сетей AsyncIntegration.cpp пример C](https://github.com/Microsoft/xbox-live-api/blob/master/InProgressSamples/Social/Xbox/C/AsyncIntegration.cpp)

