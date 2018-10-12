---
author: TylerMSFT
title: Создание и регистрация фоновой задачи, выполняемой вне процесса
description: Создайте класс фоновой задачи, выполняемой вне процесса, и зарегистрируйте его для выполнения, когда приложение не работает на переднем плане.
ms.assetid: 4F98F6A3-0D3D-4EFB-BA8E-30ED37AE098B
ms.author: twhitney
ms.date: 07/02/2018
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: Windows 10, uwp, фоновой задачи
ms.localizationpriority: medium
dev_langs:
- csharp
- cppwinrt
- cpp
ms.openlocfilehash: a599fdef47bb681ef4909fe5bba2a01a1687ba66
ms.sourcegitcommit: 933e71a31989f8063b020746fdd16e9da94a44c4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2018
ms.locfileid: "4539560"
---
# <a name="create-and-register-an-out-of-process-background-task"></a>Создание и регистрация фоновой задачи, выполняемой вне процесса

**Важные API**

-   [**IBackgroundTask**](https://msdn.microsoft.com/library/windows/apps/br224794)
-   [**BackgroundTaskBuilder**](https://msdn.microsoft.com/library/windows/apps/br224768)
-   [**BackgroundTaskCompletedEventHandler**](https://msdn.microsoft.com/library/windows/apps/br224781)

Создайте класс фоновой задачи и зарегистрируйте его выполнение, когда приложение не работает на переднем плане. В этом разделе рассказывается, как создать и зарегистрировать фоновую задачу, которая будет запускаться в отдельном процессе, а не в процессе вашего приложения. Руководство по реализации выполнения фоновой задачи непосредственно в приложении переднего плана см. в разделе [Создание и регистрация фоновой задачи, выполняемой внутри процесса](create-and-register-an-inproc-background-task.md).

> [!NOTE]
> Если фоновая задача используется для воспроизведения мультимедиа в фоновом режиме, см. раздел [Воспроизведение мультимедиа в фоновом режиме](https://msdn.microsoft.com/windows/uwp/audio-video-camera/background-audio), где приведены сведения об улучшениях в Windows 10 версии 1607, которые значительно упрощают работу.

## <a name="create-the-background-task-class"></a>Создание класса фоновой задачи

Для выполнения кода в фоновом режиме можно создавать классы, в которых реализован интерфейс [**IBackgroundTask**](https://msdn.microsoft.com/library/windows/apps/br224794). Этот код выполняется при активации определенного события, используя, например, [**SystemTrigger**](https://msdn.microsoft.com/library/windows/apps/br224839) или [**MaintenanceTrigger**](https://msdn.microsoft.com/library/windows/apps/hh700517).

Далее будет показано, как создать новый класс, реализующий интерфейс [**IBackgroundTask**](https://msdn.microsoft.com/library/windows/apps/br224794).

1.  Создайте новый проект для фоновых задач и добавьте его в решение. Для этого щелкните правой кнопкой мыши узел решений в **Обозревателе решений** и выберите **Добавить** \> **Новый проект**. Затем выберите тип проекта **Компонента среды выполнения Windows** , имя проекта и нажмите кнопку ОК.
2.  Создайте ссылку на проект фоновых задач в проекте приложения UWP. Для C# или с приложением на C++ в проекте приложения щелкните правой кнопкой мыши на **ссылки** и выберите **Добавить новую ссылку**. В разделе **Решение** выберите **Проекты**, затем выберите имя своего проекта фоновых задач и нажмите **ОК**.
3.  В проекте фоновой задачи добавьте новый класс, реализующий интерфейс [**IBackgroundTask**](/uwp/api/Windows.ApplicationModel.Background.IBackgroundTask) . Метод [**IBackgroundTask.Run**](/uwp/api/windows.applicationmodel.background.ibackgroundtask.run) — это обязательная точка входа, будет вызываться при активации указанного события; Этот метод необходим в каждой фоновой задаче.

> [!NOTE]
> Класс фоновой задачи сам&mdash;и все остальные классы в фоновой задаче&mdash;должны быть **открытые** классы, которые являются **запечатанными** (или **конечного**).

В следующем примере кода показана очень простая отправная точка для класса фоновой задачи.

```csharp
// ExampleBackgroundTask.cs
using Windows.ApplicationModel.Background;

namespace Tasks
{
    public sealed class ExampleBackgroundTask : IBackgroundTask
    {
        public void Run(IBackgroundTaskInstance taskInstance)
        {
            
        }        
    }
}
```

```cppwinrt
// First, add ExampleBackgroundTask.idl, and then build.
// ExampleBackgroundTask.idl
namespace Tasks
{
    [default_interface]
    runtimeclass ExampleBackgroundTask : Windows.ApplicationModel.Background.IBackgroundTask
    {
        ExampleBackgroundTask();
    }
}

// ExampleBackgroundTask.h
#pragma once

#include "ExampleBackgroundTask.g.h"

namespace winrt::Tasks::implementation
{
    struct ExampleBackgroundTask : ExampleBackgroundTaskT<ExampleBackgroundTask>
    {
        ExampleBackgroundTask() = default;

        void Run(Windows::ApplicationModel::Background::IBackgroundTaskInstance const& taskInstance);
    };
}

namespace winrt::Tasks::factory_implementation
{
    struct ExampleBackgroundTask : ExampleBackgroundTaskT<ExampleBackgroundTask, implementation::ExampleBackgroundTask>
    {
    };
}

// ExampleBackgroundTask.cpp
#include "pch.h"
#include "ExampleBackgroundTask.h"

namespace winrt::Tasks::implementation
{
    void ExampleBackgroundTask::Run(Windows::ApplicationModel::Background::IBackgroundTaskInstance const& taskInstance)
    {
        throw hresult_not_implemented();
    }
}
```

```cpp
// ExampleBackgroundTask.h
#pragma once

using namespace Windows::ApplicationModel::Background;

namespace Tasks
{
    public ref class ExampleBackgroundTask sealed : public IBackgroundTask
    {

    public:
        ExampleBackgroundTask();

        virtual void Run(IBackgroundTaskInstance^ taskInstance);
        void OnCompleted(
            BackgroundTaskRegistration^ task,
            BackgroundTaskCompletedEventArgs^ args
        );
    };
}

// ExampleBackgroundTask.cpp
#include "ExampleBackgroundTask.h"

using namespace Tasks;

void ExampleBackgroundTask::Run(IBackgroundTaskInstance^ taskInstance)
{
}
```

4.  Если в вашей фоновой задаче выполняется асинхронный код, для нее необходимо использовать задержку. Если вы не используете задержку, то процесс фоновой задачи может неожиданно возвращает метод **Run** перед все асинхронные работа выполняется до завершения.

Запросите задержку в метод **Run** перед вызовом асинхронного метода. Сохраните отсрочку элемента данных класса таким образом, чтобы его можно получить доступ из асинхронного метода. Объявите задержку завершенной после выполнения асинхронного кода.

Следующий пример кода получает задержку, сохраняет его и освобождает ее после выполнения асинхронного кода.

```csharp
BackgroundTaskDeferral _deferral; // Note: defined at class scope so that we can mark it complete inside the OnCancel() callback if we choose to support cancellation
public async void Run(IBackgroundTaskInstance taskInstance)
{
    _deferral = taskInstance.GetDeferral()
    //
    // TODO: Insert code to start one or more asynchronous methods using the
    //       await keyword, for example:
    //
    // await ExampleMethodAsync();
    //

    _deferral.Complete();
}
```

```cppwinrt
// ExampleBackgroundTask.h
...
private:
    Windows::ApplicationModel::Background::BackgroundTaskDeferral m_deferral{ nullptr };

// ExampleBackgroundTask.cpp
...
Windows::Foundation::IAsyncAction ExampleBackgroundTask::Run(
    Windows::ApplicationModel::Background::IBackgroundTaskInstance const& taskInstance)
{
    m_deferral = taskInstance.GetDeferral(); // Note: defined at class scope so that we can mark it complete inside the OnCancel() callback if we choose to support cancellation.
    // TODO: Modify the following line of code to call a real async function.
    co_await ExampleCoroutineAsync(); // Run returns at this point, and resumes when ExampleCoroutineAsync completes.
    m_deferral.Complete();
}
```

```cpp
void ExampleBackgroundTask::Run(IBackgroundTaskInstance^ taskInstance)
{
    m_deferral = taskInstance->GetDeferral(); // Note: defined at class scope so that we can mark it complete inside the OnCancel() callback if we choose to support cancellation.

    //
    // TODO: Modify the following line of code to call a real async function.
    //       Note that the task<void> return type applies only to async
    //       actions. If you need to call an async operation instead, replace
    //       task<void> with the correct return type.
    //
    task<void> myTask(ExampleFunctionAsync());

    myTask.then([=]() {
        m_deferral->Complete();
    });
}
```

> [!NOTE]
> В C# асинхронные методы вашей фоновой задачи можно вызвать с помощью ключевых слов **async/await**. В C + +/ CX, аналогичный результат можно получить при помощи цепочки задач.

Подробнее о шаблонах асинхронных операций см. в разделе [Асинхронное программирование](https://msdn.microsoft.com/library/windows/apps/mt187335). Дополнительные примеры использования задержек для предотвращения преждевременной остановки фоновой задачи см. в [примере фоновой задачи](http://go.microsoft.com/fwlink/p/?LinkId=618666).

Следующие действия выполняются в одном из классов вашего приложения (например, MainPage.xaml.cs).

> [!NOTE]
> Вы также можете создать функцию для регистрации фоновых задач&mdash;см. в разделе [Регистрация фоновой задачи](register-a-background-task.md). В этом случае вместо выполнения следующих трех шагов, можно просто создать триггер и предоставить их функции регистрации, указав имя задачи, точку входа задачи и (необязательно) условие.

## <a name="register-the-background-task-to-run"></a>Регистрация фоновой задачи для запуска

1.  Узнайте, как ли фоновая задача уже зарегистрирована с помощью свойства [**BackgroundTaskRegistration.AllTasks**](https://msdn.microsoft.com/library/windows/apps/br224787) итерации. Это важный шаг: если приложение не проверяет, зарегистрирована ли уже фоновая задача, оно может выполнить регистрацию несколько раз, вызывая проблемы производительности и полное использование доступного задаче времени ЦП до завершения работы.

В следующем примере выполняется итерация по свойству **AllTasks** и устанавливается флаг переменной, значение true, если задача уже зарегистрирована.

```csharp
var taskRegistered = false;
var exampleTaskName = "ExampleBackgroundTask";

foreach (var task in BackgroundTaskRegistration.AllTasks)
{
    if (task.Value.Name == exampleTaskName)
    {
        taskRegistered = true;
        break;
    }
}
```

```cppwinrt
std::wstring exampleTaskName{ L"ExampleBackgroundTask" };

auto allTasks{ Windows::ApplicationModel::Background::BackgroundTaskRegistration::AllTasks() };

bool taskRegistered{ false };
for (auto const& task : allTasks)
{
    if (task.Value().Name() == exampleTaskName)
    {
        taskRegistered = true;
        break;
    }
}

// The code in the next step goes here.
```

```cpp
boolean taskRegistered = false;
Platform::String^ exampleTaskName = "ExampleBackgroundTask";

auto iter = BackgroundTaskRegistration::AllTasks->First();
auto hascur = iter->HasCurrent;

while (hascur)
{
    auto cur = iter->Current->Value;

    if(cur->Name == exampleTaskName)
    {
        taskRegistered = true;
        break;
    }

    hascur = iter->MoveNext();
}
```

2.  Если фоновая задача еще не зарегистрирована, используйте [**BackgroundTaskBuilder**](https://msdn.microsoft.com/library/windows/apps/br224768) для создания экземпляра фоновой задачи. Точка входа задачи является именем класса фоновых задач, перед которым располагается пространство имен.

Триггер фоновой задачи определяет, когда должна быть запущена фоновая задача. Список возможных триггеров см. в статье [**SystemTrigger**](https://msdn.microsoft.com/library/windows/apps/br224839).

Например этот код создает новую фоновую задачу и запускает при срабатывании триггера **TimeZoneChanged** ее:

```csharp
var builder = new BackgroundTaskBuilder();

builder.Name = exampleTaskName;
builder.TaskEntryPoint = "Tasks.ExampleBackgroundTask";
builder.SetTrigger(new SystemTrigger(SystemTriggerType.TimeZoneChange, false));
```

```cppwinrt
if (!taskRegistered)
{
    Windows::ApplicationModel::Background::BackgroundTaskBuilder builder;
    builder.Name(exampleTaskName);
    builder.TaskEntryPoint(L"Tasks.ExampleBackgroundTask");
    builder.SetTrigger(Windows::ApplicationModel::Background::SystemTrigger{
        Windows::ApplicationModel::Background::SystemTriggerType::TimeZoneChange, false });
    // The code in the next step goes here.
}
```

```cpp
auto builder = ref new BackgroundTaskBuilder();

builder->Name = exampleTaskName;
builder->TaskEntryPoint = "Tasks.ExampleBackgroundTask";
builder->SetTrigger(ref new SystemTrigger(SystemTriggerType::TimeZoneChange, false));
```

3.  Вы можете добавить условие, чтобы контролировать, в какой момент времени после возникновения события триггера запустится ваша задача (не обязательно). Например, если вы не хотите, чтобы задача запускалась в отсутствие пользователя, используйте условие **UserPresent**. Список возможных условий см. в статье [**SystemConditionType**](https://msdn.microsoft.com/library/windows/apps/br224835).

Следующий пример кода назначает условие, при котором необходимо присутствие пользователя:

```csharp
builder.AddCondition(new SystemCondition(SystemConditionType.UserPresent));
```

```cppwinrt
builder.AddCondition(Windows::ApplicationModel::Background::SystemCondition{ Windows::ApplicationModel::Background::SystemConditionType::UserPresent });
// The code in the next step goes here.
```

```cpp
builder->AddCondition(ref new SystemCondition(SystemConditionType::UserPresent));
```

4.  Зарегистрируйте фоновую задачу, вызвав метод Register в объекте [**BackgroundTaskBuilder **](https://msdn.microsoft.com/library/windows/apps/br224768). Сохраните результат выполнения [**BackgroundTaskRegistration**](https://msdn.microsoft.com/library/windows/apps/br224786), чтобы использовать его в следующем шаге.

Следующий код регистрирует фоновую задачу и сохраняет результат.

```csharp
BackgroundTaskRegistration task = builder.Register();
```

```cppwinrt
Windows::ApplicationModel::Background::BackgroundTaskRegistration task{ builder.Register() };
```

```cpp
BackgroundTaskRegistration^ task = builder->Register();
```

> [!NOTE]
> Универсальные приложения для Windows должны вызвать [**RequestAccessAsync**](https://msdn.microsoft.com/library/windows/apps/hh700485) перед регистрацией любых типов фоновых триггеров.

Чтобы универсальное приложение для Windows продолжало корректно работать после выпуска обновления, необходимо использовать триггер **ServicingComplete** (см. раздел [SystemTriggerType](https://msdn.microsoft.com/library/windows/apps/br224839)), чтобы внести любые изменения в конфигурацию после обновления, такие как перенос базы данных приложения и регистрация фоновых задач. В настоящий момент рекомендуется отменить регистрацию фоновых задач, связанных с предыдущей версией приложения (см. раздел [**RemoveAccess**](https://msdn.microsoft.com/library/windows/apps/hh700471)), и регистрировать фоновые задачи для новой версии приложения (см. раздел [**RequestAccessAsync**](https://msdn.microsoft.com/library/windows/apps/hh700485)).

Дополнительные сведения см. в разделе [Руководство по фоновым задачам](guidelines-for-background-tasks.md).

## <a name="handle-background-task-completion-using-event-handlers"></a>Обработка завершения фоновой задачи с помощью обработчиков событий

Следует зарегистрировать метод с помощью [**BackgroundTaskCompletedEventHandler**](https://msdn.microsoft.com/library/windows/apps/br224781), чтобы ваше приложение могло получить результаты от фоновой задачи. При запуске или возобновлении приложения будет вызываться помеченный метод, если фоновая задача завершилась с момента последней работы приложения на переднем плане. (Метод OnCompleted будет вызван немедленно, если фоновая задача завершается во время работы приложения на переднем плане в настоящее время.)

1.  Создайте метод OnCompleted для обработки завершения фоновых задач. Например, результат фоновой задачи может быть причиной обновления пользовательского интерфейса. Представленный здесь объем памяти метода необходим для метода обработчика событий OnCompleted, даже если в этом примере не используется параметр *args*.

Следующий пример кода распознает завершение фоновой задачи и вызывает пример метода (принимающего строку сообщения) для обновления пользовательского интерфейса.

```csharp
private void OnCompleted(IBackgroundTaskRegistration task, BackgroundTaskCompletedEventArgs args)
{
    var settings = Windows.Storage.ApplicationData.Current.LocalSettings;
    var key = task.TaskId.ToString();
    var message = settings.Values[key].ToString();
    UpdateUI(message);
}
```

```cppwinrt
void UpdateUI(winrt::hstring const& message)
{
    MyTextBlock().Dispatcher().RunAsync(Windows::UI::Core::CoreDispatcherPriority::Normal, [=]()
    {
        MyTextBlock().Text(message);
    });
}

void OnCompleted(
    Windows::ApplicationModel::Background::BackgroundTaskRegistration const& sender,
    Windows::ApplicationModel::Background::BackgroundTaskCompletedEventArgs const& /* args */)
{
    // You'll previously have inserted this key into local settings.
    auto settings{ Windows::Storage::ApplicationData::Current().LocalSettings().Values() };
    auto key{ winrt::to_hstring(sender.TaskId()) };
    auto message{ winrt::unbox_value<winrt::hstring>(settings.Lookup(key)) };

    UpdateUI(message);
}
```

```cpp
void MainPage::OnCompleted(BackgroundTaskRegistration^ task, BackgroundTaskCompletedEventArgs^ args)
{
    auto settings = ApplicationData::Current->LocalSettings->Values;
    auto key = task->TaskId.ToString();
    auto message = dynamic_cast<String^>(settings->Lookup(key));
    UpdateUI(message);
}
```

> [!NOTE]
> Обновления пользовательского интерфейса должны выполняться асинхронно, чтобы избежать остановки потока пользовательского интерфейса. Пример см. в методе UpdateUI в [образце фоновой задачи](http://go.microsoft.com/fwlink/p/?LinkId=618666).

2.  Вернитесь к тому месту, где вы регистрировали фоновую задачу. После этой строки кода добавьте новый объект [**BackgroundTaskCompletedEventHandler**](https://msdn.microsoft.com/library/windows/apps/br224781). Предоставьте свой метод OnCompleted в качестве параметра для конструктора **BackgroundTaskCompletedEventHandler**.

Следующий пример кода добавляет [**BackgroundTaskCompletedEventHandler**](https://msdn.microsoft.com/library/windows/apps/br224781) в [**BackgroundTaskRegistration**](https://msdn.microsoft.com/library/windows/apps/br224786).

```csharp
task.Completed += new BackgroundTaskCompletedEventHandler(OnCompleted);
```

```cppwinrt
task.Completed({ this, &MainPage::OnCompleted });
```

```cpp
task->Completed += ref new BackgroundTaskCompletedEventHandler(this, &MainPage::OnCompleted);
```

## <a name="declare-in-the-app-manifest-that-your-app-uses-background-tasks"></a>Объявите в манифесте приложения, что ваше приложение использует фоновые задачи

Чтобы приложение могло выполнять фоновые задачи, вам необходимо сперва объявить каждую такую задачу в манифесте приложения. Если ваше приложение пытается зарегистрировать фоновую задачу с помощью триггера, не перечисленных в манифесте, регистрации фоновой задачи, приведет к сбою с ошибку «класс среды выполнения не зарегистрирован».

1.  Откройте конструктор манифеста пакета, запустив файл Package.appxmanifest.
2.  Перейдите на вкладку **Объявления**.
3.  В раскрывающемся списке **Доступные объявления** выберите **Фоновые задачи** и щелкните **Добавить**.
4.  Установите флажок **Системное событие**.
5.  В **точка входа:** textbox, введите пространство имен и имя вашего фонового класса в этом примере использовано Tasks.ExampleBackgroundTask.
6.  Закройте конструктор манифестов.

Следующий элемент расширений добавляется в файл Package.appxmanifest для регистрации фоновой задачи:

```xml
<Extensions>
  <Extension Category="windows.backgroundTasks" EntryPoint="Tasks.ExampleBackgroundTask">
    <BackgroundTasks>
      <Task Type="systemEvent" />
    </BackgroundTasks>
  </Extension>
</Extensions>
```

## <a name="summary-and-next-steps"></a>Сводка и дальнейшие действия

Теперь вы понимаете, как создавать класс фоновой задачи, как регистрировать фоновую задачу из приложения и как сделать так, чтобы приложение распознавало ее завершение. Вы также знаете, как обновить манифест приложения, чтобы приложение могло успешно регистрировать фоновые задачи.

> [!NOTE]
> Скачайте [пример фоновой задачи](http://go.microsoft.com/fwlink/p/?LinkId=618666), чтобы увидеть похожие примеры кода в контексте полного и надежного приложения UWP, использующего фоновые задачи.

В статьях ниже можно найти справочник по API, концептуальное руководство по фоновым задачам и подробные инструкции по созданию приложений, использующих фоновые задачи.

## <a name="related-topics"></a>Связанные разделы

**Учебные статьи с подробными сведениями о фоновых задачах**

* [Реагирование на системные события с помощью фоновых задач](respond-to-system-events-with-background-tasks.md)
* [Регистрация фоновой задачи](register-a-background-task.md)
* [Задание условий выполнения фоновой задачи](set-conditions-for-running-a-background-task.md)
* [Использование триггера обслуживания](use-a-maintenance-trigger.md)
* [Обработка отмененной фоновой задачи](handle-a-cancelled-background-task.md)
* [Отслеживание хода выполнения и завершения фоновых задач](monitor-background-task-progress-and-completion.md)
* [Запуск фоновой задачи по таймеру](run-a-background-task-on-a-timer-.md)
* [Создание и регистрация фоновой задачи, выполняемой внутри процесса](create-and-register-an-inproc-background-task.md).
* [Преобразование фоновой задачи, выполняемой вне процесса, в фоновую задачу внутри процесса](convert-out-of-process-background-task.md)  

**Руководство по фоновым задачам**

* [Руководство по работе с фоновыми задачами](guidelines-for-background-tasks.md)
* [Отладка фоновой задачи](debug-a-background-task.md)
* [Вызов событий приостановки, возобновления и фоновых событий в приложениях UWP (во время отладки)](http://go.microsoft.com/fwlink/p/?linkid=254345)

**Справочник по API для фоновых задач**

* [**Windows.ApplicationModel.Background**](https://msdn.microsoft.com/library/windows/apps/br224847)
