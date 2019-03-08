---
title: Обработка ошибок API WinRT
description: Узнайте, как обрабатывать ошибки при выполнении Xbox Live вызов службы с помощью API-интерфейсы WinRT.
ms.assetid: b4f04798-df91-430e-90f0-83b5a48b9ab2
ms.date: 04/04/2017
ms.topic: article
keywords: Xbox live, xbox, игры, универсальной платформы Windows, windows 10 для настольных ПК, xbox, обработка ошибок
ms.localizationpriority: medium
ms.openlocfilehash: e72dfa0b6f98284c240cf6af2dde02439d694b48
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57598639"
---
# <a name="winrt-api-error-handling"></a>Обработка ошибок API WinRT

В WinRT API, который можно вызвать из C + +/ CX, C#, или Javascript, ошибки выводятся с помощью исключений, поэтому необходимо использовать блоки try/catch для обработки ошибок.

Обратите внимание, что для асинхронных вызовов, требуются два блока try/catch следующим образом:

```cpp
    try
    {
        auto asyncOp = xboxLiveContext->TitleStorageService->UploadBlobAsync(
            blobMetadata,
            blobBuffer,
            TitleStorageETagMatchCondition::NotUsed,
            DEFAULT_UPLOAD_BLOCK_SIZE
            );

        create_task(asyncOp)
        .then([this, ui]( task<TitleStorageBlobMetadata^> t )
        {
            try
            {
                TitleStorageBlobMetadata^ blobMetadata = t.get();

                ui->Log(L"UploadBlobAsync succeeded");
                PrintBlobMetadata(ui, blobMetadata);

                SaveNewETag(blobMetadata->ETag);
            }
            catch (Platform::Exception^ ex)
            {
                // This could happen if there's a network error or the service returns an error
                ui->Log("The async task of UploadBlobAsync failed with", ex->ToString());
            }
        });
    }
    catch (Platform::Exception^ ex)
    {
        // This could happen if there's invalid args sent to the API
        ui->Log("The API call to UploadBlobAsync failed with", ex->ToString());
    }
```

Асинхронные методы XSAPI есть немного кода, который выполняется синхронно, когда вызывается метод.  Синхронный код работает как проверка входных аргументов и запуск асинхронной операции или действия.  Таким образом даже вызов асинхронных методов можно вызвать исключение – несмотря на то, что это не должно произойти для обычных сценариев (т. е. Ошибка программиста, не сетевая ошибка).  Пожалуйста, не понимали, каким этой возможности и программы очень осторожно, проверка входных данных и тщательного тестирования кода во время разработки.  Поместите try/catch вокруг сам вызов, или одна на более высоком уровне помещается в стек вызовов, важно использовать одну.

## <a name="help-my-game-crashes-when-i-call-xyz-xbox-service-api"></a>Справка! Игре аварийно завершает работу, когда я вызываю API службы Xbox XYZ

Так происходит сбой игры и стек вызовов, — говорит он — это вызов API службы Xbox, но?

```cpp
msvcr110.dll!Concurrency::details::_ReportUnobservedException() Line 2455    C++
Social110Release.exe!Concurrency::details::_ExceptionHolder::~_ExceptionHolder() Line 915    C++
Social110Release.exe!Concurrency::details::_Task_impl_base::~_Task_impl_base() Line 1488    C++
Social110Release.exe!Concurrency::details::_Task_impl<Microsoft::Xbox::Services::Social::XboxUserProfile ^ __ptr64>::`scalar deleting destructor'(unsigned int)    C++
Social110Release.exe!Concurrency::task<Microsoft::Xbox::Services::Social::XboxUserProfile ^ __ptr64>::_ContinuationTaskHandle<Microsoft::Xbox::Services::Social::XboxUserProfile ^ __ptr64,void,<lambda_8571b6148830c0805feee6ba9e76a692>,std::integral_constant<bool,1>,Concurrency::details::_TypeSelectorNoAsync>::`scalar deleting destructor'(unsigned int)    C++
msvcr110.dll!Concurrency::details::_TaskCollection::_NotifyCompletedChoreAndFree(Concurrency::details::_UnrealizedChore * pChore) Line 1171    C++
msvcr110.dll!Concurrency::details::_UnrealizedChore::_UnstructuredChoreWrapper(Concurrency::details::_UnrealizedChore * pChore) Line 373    C++
msvcr110.dll!Concurrency::details::InternalContextBase::Dispatch(Concurrency::DispatchState * pDispatchState) Line 1716    C++
msvcr110.dll!Concurrency::details::FreeThreadProxy::Dispatch() Line 203    C++
msvcr110.dll!Concurrency::details::ThreadProxy::ThreadProxyMain(void * lpParameter) Line 174    C++
ntdll.dll!RtlUserThreadStart(long (void *) * StartAddress, void * Argument) Line 822    C++
```

Эти стеки вызовов являются довольно запутанным.  Параллелизм, параллелизм...  Ох Microsoft::Xbox::Services::Social::XboxUserProfile находится в верхней части стека вызовов, поэтому, должны быть связаны с прозрачностью.  На самом деле это стек вызовов, созданный с помощью вызова GetUserProfileAsync для недопустимый идентификатор пользователя Xbox.  Пример кода, вызвавшего этот стек вызовов выглядит следующим образом:

```cpp
    auto pAsyncOp = requester->ProfileService->GetUserProfileAsync("abc123"); //passing invalid Xbox User Id;

    create_task( pAsyncOp )
    .then( [this] (task<XboxUserProfile^> resultTask)
    {
        // Oops, I forgot my exception handling code here.
        // If I don't call resultTask.get() and catch any potential exception it may throw,
        // then PPL will report an unobserved exception.  That unobserved exception will cause your
        // app to crash.
    });
```

Содержит ли ваш стек вызовов Concurrency::_ReportUnobservedException()?  Еще раз взгляните на выше стек вызовов.  Это важно.  Если ваш стек вызовов содержит параллелизма:: _ReportUnobservedException() затем вы обнаружили ошибку в коде игр.

PPL создает задачи, которые может следовать другие задачи.  В приведенном выше примере create_task() создает задачу для вызова GetUserProfileAsync() и .then() создает следующую задачу.  Они часто называются как предшествующей задачи (первый из них) и продолжение (второго).  В примере продолжение отсутствует обработка ошибок.  Среда выполнения завершает приложение, если задача создает исключение, и исключение не перехватывается или один из ее продолжений задачи.

Когда дело доходит до задач продолжения, обратите внимание, что фактически два различных типа.  Один вид, основанное на задаче продолжение предыдущей задаче принимает в качестве входного аргумента.  Эта задача всегда выполняется, даже если предшествующая задача вызывает исключение.  Чтобы получить результат предшествующей задачи, необходимо вызвать .get() на аргумент.  Второй, основанное на значении, принимающий выходные данные предыдущей задачи непосредственно — действительно синтаксическим сахаром, за исключением первых — продолжений на основе значения не будут выполняться вообще, если предшествующая задача вызывает исключение.

Рекомендация.  Для предотвращения сбоев, используйте продолжение на основе задач, в конце цепочки продолжения и заключить все параллелизма:: task::get() или параллелизма:: task::wait() вызовов в блоки try/catch для обработки ошибок, которые могут быть восстановлены из.

Давайте рассмотрим несколько примеров:

Пример продолжение на основе задач

```cpp
    create_task( pAsyncOp )
    .then( [this] (task<XboxUserProfile^> resultTask)   // Task-based continuation
    {
        try
        {
            XboxUserProfile^ result = resultTask.get();

            // success, do something
        }
        catch (Platform::Exception^ ex)
        {
            // concurrency::task::get threw an exception
            // safely handle the error here
        }
    });
```

Пример продолжение на основе значения

```cpp
    create_task( pAsyncOp )
    .then( [this] (XboxUserProfile^ result) // Value-based continuation
    {
        // The task completed successfully, do something here.
        // if the task didn't complete successfully, you'd better have a task-based
        // continuation at the end of the continuation chain or the app will crash.
    })
    .then( [this] (task<void> previousTask) // Task-based continuation
    {
        try
        {
            // DO NOT IGNORE THIS THINKING IT'S NOT IMPORTANT.

            // call concurrency::task::get and handle any unobserved exception
            // so the application doesn't crash.
            previousTask.get();

            // success, continue
        }
        catch (Platform::Exception^ ex)
        {
            // concurrency::task::get threw an exception
            // safely handle the error here
            // By handling this exception, you ensure your application will not
            // crash when calling Xbox Service APIs
        }
    });
```

Имеется Третье решение — используется продолжение, основанное на значении полностью, но вызывать .get() или .wait() в другом потоке и перехватывайте это исключение существует.  Ниже приведен простой пример:

```cpp
    auto getProfileTask = create_task( pAsyncOp )
    .then( [this] (XboxUserProfile^ result) // Value-based continuation
    {
        // The task completed successfully, do something here.
    });
    // Note the lack of a task-based continuation with error handling at the end

    // You may call .get() or .wait() on a value-based only chain, but
    // must ensure you surround the call in a try/catch block and handle errors
    try
    {
        getProfileTask.get();     // or getProfileTask.wait();
    }
    catch (Platform::Exception^ ex)
    {
        // concurrency::task::get threw an exception
        // safely handle the error here
        // By handling this exception, you ensure your application will not
        // crash when calling Xbox Service APIs
    }
```

**Если вы не используете PPL** Если вы используете AsyncOperationCompletionHandler или AsyncActionCompletionHandler вместо PPL, то у вас также есть некоторые аспекты обработки ошибок, при неправильном использовании, приведет к сбоям приложения.  Ниже приведен пример, демонстрирующий способ обработки ошибок

```cpp
    try
    {
        // Example is making a service call with an invalid XboxUserId which will result in an error.
        // The completion handler properly detects the error and does not crash the app.
        requester->ProfileService->GetUserProfileAsync("abc123")->Completed
            = ref new AsyncOperationCompletedHandler<XboxUserProfile^>([=] (IAsyncOperation<XboxUserProfile^>^ operation, Windows::Foundation::AsyncStatus status)
        {
            if( status == Windows::Foundation::AsyncStatus::Completed)
            {
                // Always check the AsyncStatus before calling GetResults().
                // If status is not AsyncStatus::Completed, calls to operation->GetResults()
                // may throw an exception.
                // You can also surround this call in a try/catch block for added safety.

                XboxUserProfile^ result = operation->GetResults();

                // success, do something with the result
            }
            else if( status == Windows::Foundation::AsyncStatus::Error )
            {
                // Failed
            }
        });
    }
    catch ( Platform::COMException^ ex )
    {
        // What is this try/catch block for?
        //
        // Xbox Service APIs do have some code that runs synchronously and errors need
        // to be safely handled.  In this example, if “” was passed instead of “abc123”,
        // then an invalid argument exception would be thrown when calling GetUserProfileAsync
    // See the next section for more a more detailed explanation.
        //
        // Note: this catch block will NOT catch exceptions thrown within the completion handler.
    }
```

**GetNextAsync() и исключения** с разбиением на страницы API-интерфейсы?  Все объекты AchievementResult, LeaderboardResult, InventoryItemResult и TitleStorageBlobMetadataResult содержать метод GetNextAsync() для запроса следующей страницы результатов.  Есть особый случай, если доступно больше нет данных, который вызывает исключение при вызове GetNextAsync().  Это исключение вызывается при синхронном выполнении GetNextAsync().  В этом случае метод GetNextAsync вызывает INET_E_DATA_NOT_AVAILABLE (0x800C0007).

Рекомендация. Убедитесь, что заключение в оболочку GetNextAsync() метод вызывает в блок try/catch и корректно обрабатывать случаи, INET_E_DATA_NOT_AVAILABLE.

```cpp
    try
    {
        // AchievementResult^ LastResult

        // Get next page of achievement results
        if(LastResult != nullptr)
        {
            auto getNextPage = LastResult->GetNextAsync(10);

            // create_task( getNextPage ) ...
        }
    }
    catch (Platform::Exception^ ex)
    {
        if (ex->HResult == INET_E_DATA_NOT_AVAILABLE)
        {
                // we hit the end of the achievements
        }
        else
        {
            // failed for unexpected reason
        }
    }
```
