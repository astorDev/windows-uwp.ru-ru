---
title: Создание и регистрация фоновой задачи WinMain
description: Создайте фоновую задачу COM, которая может выполняться в основном процессе или вне процесса, если упакованное приложение WinMain не запущено.
ms.assetid: 8CBD4986-6E65-4374-BC7C-C38908E417E1
ms.date: 03/27/2020
ms.topic: article
keywords: Windows 10, мост для настольных компьютеров, упакованная подписанная упаковка, WinMain, фоновая задача
ms.localizationpriority: medium
dev_langs:
- csharp
- cppwinrt
ms.openlocfilehash: 14c447312361166148da6a5a8c2b20165fabbe97
ms.sourcegitcommit: df0cd9c82d1c0c17ccde424e3c4a6ff680c31a35
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/01/2020
ms.locfileid: "80487528"
---
# <a name="create-and-register-a-winmain-com-background-task"></a>Создание и регистрация фоновой задачи WinMain COM

> [!TIP]
> метод Баккграундтаскбуилдер. Сеттаскентрипоинтклсид доступен начиная с Windows 10, версия 2004.

> [!NOTE]
> этот сценарий неприменим к упакованным приложениям WinMain. В приложениях UWP возникают ошибки, пытающиеся реализовать этот сценарий.

**Важные API**

-   [**IBackgroundTask**](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Background.IBackgroundTask)
-   [**баккграундтаскбуилдер**](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Background.BackgroundTaskBuilder)

Создайте класс фоновой задачи COM и зарегистрируйте его для запуска в приложении с полным доверием, упакованным в приложение WinMain, в ответ на триггеры. Фоновые задачи можно использовать для предоставления функциональных возможностей, когда приложение приостановлено или не выполняется. В этом разделе показано, как создать и зарегистрировать фоновую задачу, которая может выполняться в основном процессе приложения или в другом процессе.

## <a name="create-the-background-task-class"></a>Создание класса фоновой задачи

Для выполнения кода в фоновом режиме можно создавать классы, в которых реализован интерфейс [**IBackgroundTask**](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Background.IBackgroundTask). Этот код выполняется при запуске определенного события с помощью, например, [**системтригжер**](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Background.SystemTriggerType) или [**тиметригжер**](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Background.TimeTrigger).

Ниже показано, как создать новый класс, реализующий интерфейс [**IBackgroundTask**](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Background.IBackgroundTask) , и добавить его в основной процесс.

1.  [**Эти инструкции**](https://docs.microsoft.com/windows/apps/desktop/modernize/desktop-to-uwp-enhance) можно использовать для указания справочника по API-интерфейсам WinRT в упакованном решении для приложений WinMain. Это необходимо для использования IBackgroundTask и связанных API.
2.  В этом новом классе реализуйте интерфейс [**IBackgroundTask**](/uwp/api/Windows.ApplicationModel.Background.IBackgroundTask) . Метод [**IBackgroundTask. Run**](/uwp/api/windows.applicationmodel.background.ibackgroundtask.run) является обязательной точкой входа, которая будет вызываться при активации указанного события. Этот метод является обязательным в каждой фоновой задаче.

> [!NOTE]
> Класс фоновой задачи&mdash;и все остальные классы в проекте фоновой задачи&mdash;должны быть **открытыми**.

В следующем примере кода показан базовый класс фоновой задачи, который подсчитывает начальные значения и записывает его в файл до тех пор, пока запрос не будет отменен.

В C++примере/WinRT класс фоновой задачи реализуется как coclass [**com**](https://docs.microsoft.com/windows/uwp/cpp-and-winrt-apis/author-coclasses#implement-the-coclass-and-class-factory).


<details>
<summary>Пример кода фоновой задачи</summary>
<p>

```csharp

using System;
using System.IO; // Path
using System.Threading; // EventWaitHandle
using System.Collections.Generic; // Queue
using System.Runtime.InteropServices; // Guid, RegistrationServices
using Windows.ApplicationModel.Background; // IBackgroundTask

namespace PackagedWinMainBackgroundTaskSample
{
    // {14C5882B-35D3-41BE-86B2-5106269B97E6} is GUID to register this task with BackgroundTaskBuilder. Generate a random GUID before implementing.
    [ComVisible(true)]
    [ClassInterface(ClassInterfaceType.None)]
    [Guid("14C5882B-35D3-41BE-86B2-5106269B97E6")]
    [ComSourceInterfaces(typeof(IBackgroundTask))]
    public class SampleTask : IBackgroundTask
    {
        private volatile int cleanupTask; // flag used to indicate to Run method that it should exit
        private Queue<int> numbersQueue; // the data structure holding the set of primes in memory

        private const int maxPrimeNumber = 1000000000; // the number up to which task will attempt to calculate primes
        private const int queueDepthToWrite = 10; // how frequently this task should flush its queue of primes
        private const string numbersQueueFile = "numbersQueue.log"; // the file to write to relative to AppData

        public SampleTask()
        {
            cleanupTask = 0;
            numbersQueue = new Queue<int>(queueDepthToWrite);
        }

        /// <summary>
        /// This method writes all the numbers in the current queue to the specified file.
        /// </summary>
        private void FlushNumbersToFile(Queue<int> queueToWrite)
        {
            string logPath = Path.Combine(ApplicationData.Current.LocalFolder.Path,
                                        System.Diagnostics.Process.GetCurrentProcess().ProcessName);

            if (!Directory.Exists(logPath))
            {
                Directory.CreateDirectory(logPath);
            }

            logPath = Path.Combine(logPath, numbersQueueFile);

            const string delimiter = ", ";
            UnicodeEncoding unicodeEncoding = new UnicodeEncoding();
            // convert the queue to a list of comma separated values.
            string stringToWrite = String.Join(delimiter, queueToWrite);
            // Add the comma at the end.
            stringToWrite += delimiter;

            File.AppendAllText(logPath, stringToWrite);
        }

        /// <summary>
        /// This method determines if the specified number is a prime number.
        /// </summary>
        private bool IsPrimeNumber(int dividend)
        {
            bool isPrime = true;
            for (int divisor = dividend - 1; divisor > 1; divisor -= 1)
            {
                if ((dividend % divisor) == 0)
                {
                    isPrime = false;
                    break;
                }
            }

            return isPrime;
        }

        /// <summary>
        /// Given the current number, this method calculates the next prime number (excluding the specified number).
        /// </summary>
        private int GetNextPrime(int previousNumber)
        {
            int currentNumber = previousNumber + 1;
            while (!IsPrimeNumber(currentNumber))
            {
                currentNumber += 1;
            }

            return currentNumber;
        }

        /// <summary>
        /// This method is the main entry point for the background task. The system will believe this background task
        /// is complete when this method returns.
        /// </summary>
        [MTAThread]
        public void Run(IBackgroundTaskInstance taskInstance)
        {
            // Start with the first applicable number.
            int currentNumber = 1;

            taskDeferral = taskInstance.GetDeferral();

            // Wire the cancellation handler.
            taskInstance.Canceled += this.OnCanceled;

            // Set the progress to indicate this task has started
            taskInstance.Progress = 10;

            // Calculate primes until a cancellation has been requested or until
            // the maximum number is reached.
            while ((cleanupTask == 0) && (currentNumber < maxPrimeNumber)) {
                // Compute the next prime number and add it to our queue.
                currentNumber = GetNextPrime(currentNumber);
                numbersQueue.Enqueue(currentNumber);
                // Once the queue is filled to its max size, flush the numbers to the file.
                if (numbersQueue.Count >= queueDepthToWrite)
                {
                    FlushNumbersToFile(numbersQueue);
                    numbersQueue.Clear();
                }
            }

            // Flush any remaining numbers to the file as part of cleanup.
            FlushNumbersToFile(numbersQueue);

            if (taskDeferral != null)
            {
                taskDeferral.Complete();
            }
        }

        /// <summary>
        /// This method is signaled when the system requests the background task be canceled. This method will signal
        /// to the Run method to clean up and return.
        /// </summary>
        [MTAThread]
        public void OnCanceled(IBackgroundTaskInstance taskInstance, BackgroundTaskCancellationReason cancellationReason)
        {
            cleanupTask = 1;
        }
    }
}

```

```cppwinrt

#include <unknwn.h>
#include <winrt/Windows.Foundation.h>
#include <winrt/Windows.Foundation.Collections.h>
#include <winrt/Windows.ApplicationModel.Background.h>

using namespace winrt;
using namespace winrt::Windows::Foundation;
using namespace winrt::Windows::Foundation::Collections;
using namespace winrt::Windows::ApplicationModel::Background;

namespace PackagedWinMainBackgroundTaskSample {

    // Note insert unique UUID.
    struct __declspec(uuid("14C5882B-35D3-41BE-86B2-5106269B97E6"))
    SampleTask : implements<SampleTask, IBackgroundTask>
    {
        const unsigned int MaximumPotentialPrime = 1000000000;
        volatile bool isCanceled = false;
        BackgroundTaskDeferral taskDeferral = nullptr;

        void __stdcall Run (_In_ IBackgroundTaskInstance taskInstance)
        {
            taskInstance.Canceled({ this, &SampleTask::OnCanceled });

            taskDeferral = taskInstance.GetDeferral();

            unsigned int currentPrimeNumber = 1;
            while (!isCanceled && (currentPrimeNumber < MaximumPotentialPrime))
            {
                currentPrimeNumber = GetNextPrime(currentPrimeNumber);
            }

            taskDeferral.Complete();
        }

        void __stdcall OnCanceled (_In_ IBackgroundTaskInstance, _In_ BackgroundTaskCancellationReason)
        {
            isCanceled = true;
        }
    };

    struct TaskFactory : implements<TaskFactory, IClassFactory>
    {
        HRESULT __stdcall CreateInstance (_In_opt_ IUnknown* aggregateInterface, _In_ REFIID interfaceId, _Outptr_ VOID** object) noexcept final
        {
            if (aggregateInterface != NULL) {
                return CLASS_E_NOAGGREGATION;
            }

            return make<SampleTask>().as(interfaceId, object);
        }

        HRESULT __stdcall LockServer (BOOL) noexcept final
        {
            return S_OK;
        }
    };
}

```

</p>
</details>


## <a name="add-the-support-code-to-instantiate-the-com-class"></a>Добавление кода поддержки для создания экземпляра класса COM

Чтобы фоновая задача была активирована в полноценном приложении WinMain, класс фоновой задачи должен иметь код поддержки, позволяющий COM понять, как запустить процесс приложения, если он не запущен, а затем понять, какой экземпляр процесса в настоящее время это сервер для обработки новых активаций для этой фоновой задачи.

1.  COM должен понять, как запустить процесс приложения, если он еще не запущен. Процесс приложения, в котором размещен код фоновой задачи, должен быть объявлен в манифесте пакета. В следующем примере кода показано, как **самплетаск** размещается внутри **самплебаккграундапп. exe**. При запуске фоновой задачи, когда ни один процесс не выполняется, **самплебаккграундапп. exe** будет запущен с аргументами процесса **"-стартсамплетасксервер"** .

```xml

<Extensions>
  <com:Extension Category="windows.comServer">
    <com:ComServer>
      <com:ExeServer Executable="SampleBackgroundApp\SampleBackgroundApp.exe" DisplayName="SampleBackgroundApp" Arguments="-StartSampleTaskServer">
        <com:Class Id="14C5882B-35D3-41BE-86B2-5106269B97E6" DisplayName="Sample Task" />
      </com:ExeServer>
    </com:ComServer>
  </com:Extension>
</Extensions>

```

2.  После запуска процесса с помощью правых аргументов он должен сообщить COM, что это текущий COM-сервер для новых экземпляров Самплетаск. В следующем примере кода показано, как процесс приложения должен зарегистрироваться в COM. Обратите внимание, что эти примеры показывают, как процесс будет объявлять себя как сервер COM для Самплетаск по крайней мере для одного экземпляра, который должен быть завершен до выхода. Это необязательно, и обработка фоновой задачи может запустить основные функции процесса.

```csharp

class SampleTaskServer
{
    SampleTaskServer()
    {
        comRegistrationToken = 0;
        waitHandle = new EventWaitHandle(false, EventResetMode.AutoReset);
    }

    ~SampleTaskServer()
    {
        Stop();
    }

    public void Start()
    {
        RegistrationServices registrationServices = new RegistrationServices();
        comRegistrationToken = registrationServices.RegisterTypeForComClients(typeof(SampleTask), RegistrationClassContext.LocalServer, RegistrationConnectionType.MultipleUse);

        // Either have the background task signal this handle when it completes, or never signal this handle to keep this
        // process as the COM server until the process is closed.
        waitHandle.WaitOne();
    }

    public void Stop()
    {
        if (comRegistrationToken != 0)
        {
            RegistrationServices registrationServices = new RegistrationServices();
            registrationServices.UnregisterTypeForComClients(registrationCookie);
        }

        waitHandle.Set();
    }

    private int comRegistrationToken;
    private EventWaitHandle waitHandle;
}

var sampleTaskServer = new SampleTaskServer();
sampleTaskServer.Start();

```

```cppwinrt

class SampleTaskServer
{
public:
    SampleTaskServer()
    {
        waitHandle = EventWaitHandle(false, EventResetMode::AutoResetEvent);
        comRegistrationToken = 0;
    }

    ~SampleTaskServer()
    {
        Stop();
    }

    void Start()
    {
        try
        {
            com_ptr<IClassFactory> taskFactory = make<TaskFactory>();

            winrt::check_hresult(CoRegisterClassObject(__uuidof(SampleTask),
                                                       taskFactory.get(),
                                                       CLSCTX_LOCAL_SERVER,
                                                       REGCLS_MULTIPLEUSE,
                                                       &comRegistrationToken));

            // Either have the background task signal this handle when it completes, or never signal this handle to
            // keep this process as the COM server until the process is closed.
            waitHandle.WaitOne();

        }
        catch (...)
        {
            // Indicate an error has been encountered.
        }
    }

    void Stop()
    {
        if (comRegistrationToken != 0)
        {
            CoRevokeClassObject(comRegistrationToken);
        }

        waitHandle.Set();
    }

private:
    DWORD comRegistrationToken;
    EventWaitHandle waitHandle;
};

SampleTaskServer sampleTaskServer;
sampleTaskServer.Start();

```


## <a name="register-the-background-task-to-run"></a>Регистрация фоновой задачи для запуска

1.  Определите, зарегистрирована ли фоновая задача путем прохода по свойству [**баккграундтаскрегистратион. аллтаскс**](https://docs.microsoft.com/uwp/api/windows.applicationmodel.background.backgroundtaskregistration.alltasks) . *Этот шаг важен*. Если приложение не проверяет наличие существующих регистраций фоновых задач, оно может легко зарегистрировать задачу несколько раз, вызывая проблемы с производительностью и кардинально отличается доступное время ЦП задачи до завершения работы. Приложение может использовать ту же точку входа для выполнения всех фоновых задач и использовать другие свойства, такие как [**имя**](https://docs.microsoft.com/uwp/api/windows.applicationmodel.background.backgroundtaskregistration.name#Windows_ApplicationModel_Background_BackgroundTaskRegistration_Name) или [**taskId**](https://docs.microsoft.com/uwp/api/windows.applicationmodel.background.backgroundtaskregistration.taskid#Windows_ApplicationModel_Background_BackgroundTaskRegistration_TaskId) , назначенные [**баккграундтаскрегистратион**](https://docs.microsoft.com/uwp/api/windows.applicationmodel.background.backgroundtaskregistration) , чтобы решить, какую работу следует выполнить.

В следующем примере выполняется итерация свойства **аллтаскс** и устанавливается значение true для переменной флага, если задача уже зарегистрирована.

```csharp

var taskRegistered = false;
var sampleTaskName = "SampleTask";

foreach (var task in BackgroundTaskRegistration.AllTasks)
{
    if (task.Value.Name == sampleTaskName)
    {
        taskRegistered = true;
        break;
    }
}

// The code in the next step goes here.

```

```cppwinrt

bool taskRegistered = false;
std::wstring sampleTaskName = L"SampleTask";
auto allTasks = BackgroundTaskRegistration::AllTasks();

for (auto const& task : allTasks)
{
    if (task.Value().Name() == sampleTaskName)
    {
        taskRegistered = true;
        break;
    }
}

// The code in the next step goes here.

```

1.  Если фоновая задача еще не зарегистрирована, используйте [**BackgroundTaskBuilder**](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Background.BackgroundTaskBuilder) для создания экземпляра фоновой задачи. Точка входа задачи является именем класса фоновых задач, перед которым располагается пространство имен.

Триггер фоновой задачи определяет, когда должна быть запущена фоновая задача. Список возможных триггеров см. в разделе пространство имен [**Windows. ApplicationModel. Background**](https://docs.microsoft.com/uwp/api/windows.applicationmodel.background) .

> [!NOTE]
> Для упакованных фоновых задач WinMain поддерживаются только подмножество триггеров.

Например, этот код создает новую фоновую задачу и настраивает ее для запуска в течение 15-минутного [**тиметригжер**]():

```csharp

if (!taskRegistered)
{
    var builder = new BackgroundTaskBuilder();

    builder.Name = sampleTaskName;
    builder.SetTaskEntryPointClsid(typeof(SampleTask).GUID);
    builder.SetTrigger(new TimeTrigger(15, false));
}

// The code in the next step goes here.

```

```cppwinrt

if (!taskRegistered)
{
    BackgroundTaskBuilder builder;

    builder.Name(sampleTaskName);
    builder.SetTaskEntryPointClsid(__uuidof(SampleTask));
    builder.SetTrigger(TimeTrigger(15, false));
}

// The code in the next step goes here.

```

1.  Вы можете добавить условие, чтобы контролировать, в какой момент времени после возникновения события триггера запустится ваша задача (не обязательно). Например, если вы не хотите, чтобы задача выполнялась, пока Интернет не станет доступен, используйте условие **интернетаваилабле**. Список возможных условий см. в статье [**SystemConditionType**](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Background.SystemConditionType).

Следующий пример кода назначает условие, при котором необходимо присутствие пользователя:

```csharp
builder.AddCondition(new SystemCondition(SystemConditionType.InternetAvailable));
// The code in the next step goes here.
```

```cppwinrt
builder.AddCondition(SystemCondition{ SystemConditionType::InternetAvailable });
// The code in the next step goes here.
```

4.  Зарегистрируйте фоновую задачу, вызвав метод Register в объекте [**BackgroundTaskBuilder**](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Background.BackgroundTaskBuilder). Сохраните результат выполнения [**BackgroundTaskRegistration**](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Background.BackgroundTaskRegistration), чтобы использовать его в следующем шаге. Обратите внимание, что функция Register может возвращать ошибки в форме исключений. Не забудьте вызвать Register в блоке try-catch.

Следующий код регистрирует фоновую задачу и сохраняет результат.

```csharp

try
{
    var task = builder.Register();
}
catch (...)
{
    // Indicate an error was encountered.
}

```

```cppwinrt

try
{
    auto task = builder.Register();
}
catch (...)
{
    // Indicate an error was encountered.
}

```

## <a name="bringing-it-all-together"></a>Совместная работа

В следующих примерах кода показан полный код, необходимый для выполнения и регистрации фоновой задачи COM WinMain:

<details>
<summary>Завершение манифеста пакета приложения WinMain</summary>
<p>

```xml

<?xml version="1.0" encoding="utf-8"?>
<Package
  xmlns="http://schemas.microsoft.com/appx/manifest/foundation/windows10"
  xmlns:uap="http://schemas.microsoft.com/appx/manifest/uap/windows10"
  xmlns:rescap="http://schemas.microsoft.com/appx/manifest/foundation/windows10/restrictedcapabilities"
  xmlns:com="http://schemas.microsoft.com/appx/manifest/com/windows10"
  IgnorableNamespaces="uap rescap com">

  <Identity
    Name="SamplePackagedWinMainBackgroundApp"
    Publisher="CN=Contoso"
    Version="1.0.0.0" />

  <Properties>
    <DisplayName>SamplePackagedWinMainBackgroundApp</DisplayName>
    <PublisherDisplayName>Contoso</PublisherDisplayName>
    <Logo>Images\StoreLogo.png</Logo>
  </Properties>

  <Dependencies>
    <TargetDeviceFamily Name="Windows.Desktop" MinVersion="10.0.19041.0" MaxVersionTested="10.0.19041.0" />
  </Dependencies>

  <Resources>
    <Resource Language="x-generate"/>
  </Resources>

  <Applications>
    <Application Id="App"
                 Executable="SampleBackgroundApp\$targetnametoken$.exe"
                 EntryPoint="$targetentrypoint$">

      <uap:VisualElements
        DisplayName="SampleBackgroundApp"
        Description="SampleBackgroundApp"
        BackgroundColor="transparent"
        Square150x150Logo="Images\Square150x150Logo.png"
        Square44x44Logo="Images\Square44x44Logo.png">
        <uap:DefaultTile Wide310x150Logo="Images\Wide310x150Logo.png" />
        <uap:SplashScreen Image="Images\SplashScreen.png" />
      </uap:VisualElements>

      <Extensions>
        <com:Extension Category="windows.comServer">
          <com:ComServer>
            <com:ExeServer Executable="SampleBackgroundApp\SampleBackgroundApp.exe" DisplayName="SampleBackgroundApp" Arguments="-StartSampleTaskServer">
              <com:Class Id="14C5882B-35D3-41BE-86B2-5106269B97E6" DisplayName="Sample Task" />
            </com:ExeServer>
          </com:ComServer>
        </com:Extension>
      </Extensions>
    </Application>
  </Applications>

  <Capabilities>
  <rescap:Capability Name="runFullTrust" />
  </Capabilities>
</Package>

```

</p>
</details>


<details>
<summary>Пример кода для завершения фоновой задачи</summary>
<p>

```csharp

using System;
using System.IO; // Path
using System.Threading; // EventWaitHandle
using System.Collections.Generic; // Queue
using System.Runtime.InteropServices; // Guid, RegistrationServices
using Windows.ApplicationModel.Background; // IBackgroundTask

namespace PackagedWinMainBackgroundTaskSample
{
    // Background task implementation.
    // {14C5882B-35D3-41BE-86B2-5106269B97E6} is GUID to register this task with BackgroundTaskBuilder. Generate a random GUID before implementing.
    [ComVisible(true)]
    [ClassInterface(ClassInterfaceType.None)]
    [Guid("14C5882B-35D3-41BE-86B2-5106269B97E6")]
    [ComSourceInterfaces(typeof(IBackgroundTask))]
    public class SampleTask : IBackgroundTask
    {
        private volatile int cleanupTask; // flag used to indicate to Run method that it should exit
        private Queue<int> numbersQueue; // the data structure holding the set of primes in memory

        private const int maxPrimeNumber = 1000000000; // the number up to which task will attempt to calculate primes
        private const int queueDepthToWrite = 10; // how frequently this task should flush its queue of primes
        private const string numbersQueueFile = "numbersQueue.log"; // the file to write to relative to AppData

        public SampleTask()
        {
            cleanupTask = 0;
            numbersQueue = new Queue<int>(queueDepthToWrite);
        }

        /// <summary>
        /// This method writes all the numbers in the current queue to the specified file.
        /// </summary>
        private void FlushNumbersToFile(Queue<int> queueToWrite)
        {
            string logPath = Path.Combine(ApplicationData.Current.LocalFolder.Path,
                                        System.Diagnostics.Process.GetCurrentProcess().ProcessName);

            if (!Directory.Exists(logPath))
            {
                Directory.CreateDirectory(logPath);
            }

            logPath = Path.Combine(logPath, numbersQueueFile);

            const string delimiter = ", ";
            UnicodeEncoding unicodeEncoding = new UnicodeEncoding();
            // convert the queue to a list of comma separated values.
            string stringToWrite = String.Join(delimiter, queueToWrite);
            // Add the comma at the end.
            stringToWrite += delimiter;

            File.AppendAllText(logPath, stringToWrite);
        }

        /// <summary>
        /// This method determines if the specified number is a prime number.
        /// </summary>
        private bool IsPrimeNumber(int dividend)
        {
            bool isPrime = true;
            for (int divisor = dividend - 1; divisor > 1; divisor -= 1)
            {
                if ((dividend % divisor) == 0)
                {
                    isPrime = false;
                    break;
                }
            }

            return isPrime;
        }

        /// <summary>
        /// Given the current number, this method calculates the next prime number (excluding the specified number).
        /// </summary>
        private int GetNextPrime(int previousNumber)
        {
            int currentNumber = previousNumber + 1;
            while (!IsPrimeNumber(currentNumber))
            {
                currentNumber += 1;
            }

            return currentNumber;
        }

        /// <summary>
        /// This method is the main entry point for the background task. The system will believe this background task
        /// is complete when this method returns.
        /// </summary>
        [MTAThread]
        public void Run(IBackgroundTaskInstance taskInstance)
        {
            // Start with the first applicable number.
            int currentNumber = 1;

            taskDeferral = taskInstance.GetDeferral();

            // Wire the cancellation handler.
            taskInstance.Canceled += this.OnCanceled;

            // Set the progress to indicate this task has started
            taskInstance.Progress = 10;

            // Calculate primes until a cancellation has been requested or until
            // the maximum number is reached.
            while ((cleanupTask == 0) && (currentNumber < maxPrimeNumber)) {
                // Compute the next prime number and add it to our queue.
                currentNumber = GetNextPrime(currentNumber);
                numbersQueue.Enqueue(currentNumber);
                // Once the queue is filled to its max size, flush the numbers to the file.
                if (numbersQueue.Count >= queueDepthToWrite)
                {
                    FlushNumbersToFile(numbersQueue);
                    numbersQueue.Clear();
                }
            }

            // Flush any remaining numbers to the file as part of cleanup.
            FlushNumbersToFile(numbersQueue);

            if (taskDeferral != null)
            {
                taskDeferral.Complete();
            }
        }

        /// <summary>
        /// This method is signaled when the system requests the background task be canceled. This method will signal
        /// to the Run method to clean up and return.
        /// </summary>
        [MTAThread]
        public void OnCanceled(IBackgroundTaskInstance taskInstance, BackgroundTaskCancellationReason cancellationReason)
        {
            cleanupTask = 1;
        }
    }


    // COM server startup code.
    class SampleTaskServer
    {
        SampleTaskServer()
        {
            comRegistrationToken = 0;
            waitHandle = new EventWaitHandle(false, EventResetMode.AutoReset);
        }

        ~SampleTaskServer()
        {
            Stop();
        }

        public void Start()
        {
            RegistrationServices registrationServices = new RegistrationServices();
            comRegistrationToken = registrationServices.RegisterTypeForComClients(typeof(SampleTask), RegistrationClassContext.LocalServer, RegistrationConnectionType.MultipleUse);

            // Either have the background task signal this handle when it completes, or never signal this handle to keep this
            // process as the COM server until the process is closed.
            waitHandle.WaitOne();
        }

        public void Stop()
        {
            if (comRegistrationToken != 0)
            {
                RegistrationServices registrationServices = new RegistrationServices();
                registrationServices.UnregisterTypeForComClients(registrationCookie);
            }

            waitHandle.Set();
        }

        private int comRegistrationToken;
        private EventWaitHandle waitHandle;
    }


    // Background task registration code.
    class SampleTaskRegistrar
    {
        public static void Register()
        {
            var taskRegistered = false;
            var sampleTaskName = "SampleTask";

            foreach (var task in BackgroundTaskRegistration.AllTasks)
            {
                if (task.Value.Name == sampleTaskName)
                {
                    taskRegistered = true;
                    break;
                }
            }

            if (!taskRegistered)
            {
                var builder = new BackgroundTaskBuilder();

                builder.Name = sampleTaskName;
                builder.SetTaskEntryPointClsid(typeof(SampleTask).GUID);
                builder.SetTrigger(new TimeTrigger(15, false));
            }

            try
            {
                var task = builder.Register();
            }
            catch (...)
            {
                // Indicate an error was encountered.
            }
        }
    }


    // Application entry point.
    static class Program
    {
        [MTAThread]
        static void Main()
        {
            string[] commandLineArgs = Environment.GetCommandLineArgs();
            if (commandLineArgs.Length < 2)
            {
                // Open the WPF UI when no arguments are specified.
            }
            else
            {
                if (commandLineArgs.Contains("-RegisterSampleTask", StringComparer.InvariantCultureIgnoreCase))
                {
                    SampleTaskRegistrar.Register();
                }

                if (commandLineArgs.Contains("-StartSampleTaskServer", StringComparer.InvariantCultureIgnoreCase))
                {
                    var sampleTaskServer = new SampleTaskServer();
                    sampleTaskServer.Start();
                }
            }

            return;
        }
    }
}

```

```cppwinrt

#include <unknwn.h>
#include <winrt/Windows.Foundation.h>
#include <winrt/Windows.Foundation.Collections.h>
#include <winrt/Windows.ApplicationModel.Background.h>

using namespace winrt;
using namespace winrt::Windows::Foundation;
using namespace winrt::Windows::Foundation::Collections;
using namespace winrt::Windows::ApplicationModel::Background;

namespace PackagedWinMainBackgroundTaskSample
{
    // Background task implementation.
    // {14C5882B-35D3-41BE-86B2-5106269B97E6} is GUID to register this task with BackgroundTaskBuilder. Generate a random GUID before implementing.
    struct __declspec(uuid("14C5882B-35D3-41BE-86B2-5106269B97E6"))
    SampleTask : implements<SampleTask, IBackgroundTask>
    {
        const unsigned int maxPrimeNumber = 1000000000;
        volatile bool isCanceled = false;
        BackgroundTaskDeferral taskDeferral = nullptr;

        void __stdcall Run (_In_ IBackgroundTaskInstance taskInstance)
        {
            taskInstance.Canceled({ this, &SampleTask::OnCanceled });

            taskDeferral = taskInstance.GetDeferral();

            unsigned int currentPrimeNumber = 1;
            while (!isCanceled && (currentPrimeNumber < maxPrimeNumber))
            {
                currentPrimeNumber = GetNextPrime(currentPrimeNumber);
            }

            taskDeferral.Complete();
        }

        void __stdcall OnCanceled (_In_ IBackgroundTaskInstance, _In_ BackgroundTaskCancellationReason)
        {
            isCanceled = true;
        }
    };

    struct TaskFactory : implements<TaskFactory, IClassFactory>
    {
        HRESULT __stdcall CreateInstance (_In_opt_ IUnknown* aggregateInterface, _In_ REFIID interfaceId, _Outptr_ VOID** object) noexcept final
        {
            if (aggregateInterface != nullptr) {
                return CLASS_E_NOAGGREGATION;
            }

            return make<SampleTask>().as(interfaceId, object);
        }

        HRESULT __stdcall LockServer (BOOL) noexcept final
        {
            return S_OK;
        }
    };


    // COM server startup code.
    class SampleTaskServer
    {
    public:
        SampleTaskServer()
        {
            waitHandle = EventWaitHandle(false, EventResetMode::AutoResetEvent);
            comRegistrationToken = 0;
        }

        ~SampleTaskServer()
        {
            Stop();
        }

        void Start()
        {
            try
            {
                com_ptr<IClassFactory> taskFactory = make<TaskFactory>();

                winrt::check_hresult(CoRegisterClassObject(__uuidof(SampleTask),
                                                           taskFactory.get(),
                                                           CLSCTX_LOCAL_SERVER,
                                                           REGCLS_MULTIPLEUSE,
                                                           &comRegistrationToken));

                // Either have the background task signal this handle when it completes, or never signal this handle to
                // keep this process as the COM server until the process is closed.
                waitHandle.WaitOne();

            }
            catch (...)
            {
                // Indicate an error has been encountered.
            }
        }

        void Stop()
        {
            if (comRegistrationToken != 0)
            {
                CoRevokeClassObject(comRegistrationToken);
            }

            waitHandle.Set();
        }

    private:
        DWORD comRegistrationToken;
        EventWaitHandle waitHandle;
    };


    // Background task registration code.
    class SampleTaskRegistrar
    {
        public static void Register()
        {
            bool taskRegistered = false;
            std::wstring sampleTaskName = L"SampleTask";
            auto allTasks = BackgroundTaskRegistration::AllTasks();

            for (auto const& task : allTasks)
            {
                if (task.Value().Name() == sampleTaskName)
                {
                    taskRegistered = true;
                    break;
                }
            }

            if (!taskRegistered)
            {
                BackgroundTaskBuilder builder;

                builder.Name(sampleTaskName);
                builder.SetTaskEntryPointClsid(__uuidof(SampleTask));
                builder.SetTrigger(TimeTrigger(15, false));
            }

            try
            {
                auto task = builder.Register();
            }
            catch (...)
            {
                // Indicate an error was encountered.
            }
        }
    }

}

using namespace PackagedWinMainBackgroundTaskSample;

// Application entry point.
int wmain(_In_ int argc, _In_reads_(argc) const wchar** argv)
{
    unsigned int argumentIndex;

    winrt::init_apartment();

    if (argc <= 1)
    {
        return E_INVALIDARG;
    }

    for (argumentIndex = 0; argumentIndex < argc ; argumentIndex += 1)
    {
        if (_wcsnicmp(L"RegisterSampleTask",
                      argv[argumentIndex],
                      wcslen(L"RegisterSampleTask")) == 0)
        {
            SampleTaskRegistrar::Register();
        }

        if (_wcsnicmp(L"StartSampleTaskServer",
                      argv[argumentIndex],
                      wcslen(L"StartSampleTaskServer")) == 0)
        {
            SampleTaskServer sampleTaskServer;
            sampleTaskServer.Start();
        }
    }

    return S_OK;
}

```

</p>
</details>


## <a name="remarks"></a>Примечания

В отличие от приложений универсальной платформы Windows, которые могут выполнять фоновые задачи в современных ждущих режимах, приложения WinMain не могут запускать код с более низких фаз в современных ждущих режимах. Дополнительные сведения см. в [современных ждущих режимах](https://docs.microsoft.com/windows-hardware/design/device-experiences/modern-standby) .

В статьях ниже можно найти справочник по API, концептуальное руководство по фоновым задачам и подробные инструкции по созданию приложений, использующих фоновые задачи.

## <a name="related-topics"></a>Связанные разделы

* [Реагирование на системные события с помощью фоновых задач](respond-to-system-events-with-background-tasks.md)
* [Регистрация фоновой задачи](register-a-background-task.md)
* [Задание условий выполнения фоновой задачи](set-conditions-for-running-a-background-task.md)
* [Использование триггера обслуживания](use-a-maintenance-trigger.md)
* [Обработка отмененной фоновой задачи](handle-a-cancelled-background-task.md)
* [Отслеживание хода выполнения и завершения фоновых задач](monitor-background-task-progress-and-completion.md)
* [Запуск фоновой задачи по таймеру](run-a-background-task-on-a-timer-.md)
* [Создание и регистрация внутрипроцессной фоновой задачи](create-and-register-an-inproc-background-task.md)
* [Преобразование незавершенного фонового задания в фоновую задачу внутри процесса](convert-out-of-process-background-task.md)

**Руководство по фоновым задачам**

* [Руководство по работе с фоновыми задачами](guidelines-for-background-tasks.md)
* [Отладка фоновой задачи](debug-a-background-task.md)
* [Как активировать события приостановки, возобновления и фоновых событий в приложениях UWP (при отладке)](https://msdn.microsoft.com/library/windows/apps/hh974425(v=vs.110).aspx)

**Справочник по API фоновых задач**

* [**Windows. ApplicationModel. Background**](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Background)
