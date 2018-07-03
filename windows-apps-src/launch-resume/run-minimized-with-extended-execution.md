---
author: TylerMSFT
description: Узнайте, как использовать расширенный сеанс выполнения для обеспечения работы приложения в свернутом состоянии
title: Задержка приостановки приложения с помощью расширенного сеанса выполнения
ms.author: twhitney
ms.date: 02/08/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, uwp, расширенное выполнение, свернутый, ExtendedExecutionSession, фоновая задача, жизненный цикл приложения, экран блокировки
ms.assetid: e6a6a433-5550-4a19-83be-bbc6168fe03a
ms.localizationpriority: medium
ms.openlocfilehash: 302c6125852654d931088de41a20e4b42adb9b52
ms.sourcegitcommit: 3500825bc2e5698394a8b1d2efece7f071f296c1
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/09/2018
ms.locfileid: "1862083"
---
# <a name="postpone-app-suspension-with-extended-execution"></a>Задержка приостановки приложения с помощью расширенного сеанса выполнения

В этой статье рассказывается, как использовать расширенный сеанс выполнения, когда работа приложения приостановлена, чтобы оно могло выполняться в свернутом состоянии или на экране блокировки.

Когда пользователь сворачивает приложение или переключается на другую задачу, его работа приостанавливается.  Память приложения задействуется, но код не выполняется. Эта схема применима ко всем выпускам операционной системы, в которой есть визуальный интерфейс пользователя. Дополнительные сведения о процессах, происходящих при приостановке приложения, см. в разделе [Жизненный цикл приложения](app-lifecycle.md). 

Бывают случаи, когда необходимо, чтобы приложение выполнялось, а не приостанавливало работу, когда пользователь покидает его или при сворачивании приложения. Например, приложение для подсчета шагов должно работать и отслеживать шаги, даже когда пользователь переходит к использованию других приложений. 

Если требуется, чтобы приложение продолжало работу, это может обеспечить операционная система либо приложение может отправить запрос, чтобы продолжить выполнение. Например, при воспроизведении звука в фоновом режиме операционная система может поддерживать работу приложения дольше, если выполнить шаги из раздела [Воспроизведение мультимедиа в фоновом режиме](../audio-video-camera/background-audio.md). В противном случае необходимо вручную запросить больше времени для выполнения приложения. Количество времени, которое может быть получено для фонового выполнения, может составлять несколько минут, но вы должны быть готовы обработать сеанс, который может быть отозван в любое время. Эти ограничения времени жизненного цикла приложения отключены, пока приложение работает в режиме отладки. По этой причине важно проверить средство расширенного сеанса выполнения и другие инструменты, использующиеся для откладывания приостановки приложения не в режиме отладки, или путем использования событий жизненного цикла, доступных в Visual Studio. 
 
Для этого требуется создать класс [ExtendedExecutionSession](https://msdn.microsoft.com/library/windows/apps/windows.applicationmodel.extendedexecution.extendedexecutionsession.aspx), чтобы запросить больше времени на завершение работы приложения в фоновом режиме. Тип создаваемого класса **ExtendedExecutionSession** определяется перечислением [ExtendedExecutionReason](https://msdn.microsoft.com/library/windows/apps/windows.applicationmodel.extendedexecution.extendedexecutionreason.aspx), которое вы предоставляете при создании класса. У перечисления **ExtendedExecutionReason** есть три значения: **Unspecified, LocationTracking** и **SavingData**. Только один сеанс **ExtendedExecutionSession**можно запросить в любое время. Попытка создать другой сеанс, когда другой сеанс активен, вызовет исключение 0x8007139F в конструкторе **ExtendedExecutionSession** с сообщением о том, что состояние группы или ресурса является неподходящим для выполнения запрошенной операции. Не используйте [ExtendedExecutionForegroundSession](https://msdn.microsoft.com/library/windows/apps/windows.applicationmodel.extendedexecution.foreground.extendedexecutionforegroundsession.aspx) и [ExtendedExecutionForegroundReason](https://msdn.microsoft.com/library/windows/apps/windows.applicationmodel.extendedexecution.foreground.extendedexecutionforegroundreason.aspx). Они требуют ограниченных возможностей и не доступны для использования в приложениях Магазина.

## <a name="run-while-minimized"></a>Выполнение при сворачивании

Существует два случая, когда можно использовать расширенный сеанс выполнения.
- В любой момент во время рутинного выполнения на переднем плане, когда приложение находится в состоянии выполнения.
- После того как приложение получит событие приостановки (операционная система переводит приложение в приостановленное состояние) в обработчике событий приостановки приложения.

Код для этих двух случаев является одинаковым, но приложение ведет себя немного по-разному в каждом случае. В первом случае приложение остается в состоянии выполнения даже при возникновении события, которое обычно приводит к запуску приостановки (например, переход пользователя из приложения). Приложение никогда не получит событие приостановки, пока активен расширенный сеанс выполнения. При сбросе расширенного сеанса выполнения приложение снова может быть приостановлено.

Во втором случае, если приложение переходит в приостановленное состояние, оно остается в состоянии приостановки в течение расширенного сеанса выполнения. После окончания расширенного сеанса выполнения приложение переходит в приостановленное состояние без дальнейшего уведомления.

Используйте **ExtendedExecutionReason.Unspecified** при создании класса **ExtendedExecutionSession**, чтобы запросить дополнительное время, перед тем как приложение перейдет в фоновый режим, для реализации таких сценариев, как обработка мультимедиа, компиляция проекта или поддержание сетевого подключения в активном состоянии. На настольных компьютерах под управлением Windows 10 Домашняя, Windows 10 Pro, Windows 10 Корпоративная и Windows 10 для образовательных учреждений следует использовать этот подход, если требуется, чтобы приложение не приостанавливало работу при его сворачивании.

При запуске продолжительного процесса запросите расширенный сеанс выполнения, чтобы отложить переход приложения в состояние **Suspending (Приостановка)**, который в противном случае произойдет при переходе приложения в фоновый режим. На настольных устройствах к расширенным сеансам выполнения, созданным с помощью **ExtendedExecutionReason.Unspecified**, применяется ограничение по времени в зависимости от типа питания. Если устройство подключено к розетке, ограничение к продолжительности расширенного сеанса выполнения не применяется. Если устройство работает от батареи, продолжительность расширенного сеанса выполнения может составить до 10 минут в фоновом режиме.

При использовании планшета или ноутбука пользователь может добиться такой же длительности выполнения приложения при его сворачивании за счет уровня заряда батареи, если пункт **Разрешить приложению выполнять фоновые задачи** выбран в разделе **Использование заряда батареи конкретными приложениями**. (Чтобы найти этот параметр на ноутбуке, перейдите в **Параметры** > **Система** > **Батарея** > **Использование заряда батареи конкретными приложениями** (ссылка под процентом заряда аккумулятора) > выберите приложение > отключите **Управляется Windows** > выберите **Разрешить приложению выполнять фоновые задачи**.  

Во всех выпусках ОС подобные расширенные сеансы выполнения останавливаются, когда устройство переходит в режим ожидания с подключением. На мобильных устройствах под управлением Windows 10 Mobile подобные расширенные сеансы выполнения будут работать пока включен экран. При отключении экрана устройство сразу же попытается перейти в режим ожидания с подключением и пониженным энергопотреблением. На настольных устройствах сеанс будет продолжать работать при появлении экрана блокировки. Устройство не будет переходить в режим ожидания с подключением в течение некоторого времени после отключения экрана. В выпуске операционной системы для Xbox устройство переходит в режим ожидания с подключением через один час при условии, что пользователь не менял значение, заданное по умолчанию.

## <a name="track-the-users-location"></a>Отслеживание местоположения пользователя

Задайте значение **ExtendedExecutionReason.LocationTracking** при создании класса **ExtendedExecutionSession**, если требуется, чтобы ваше приложение регулярно получало данные о местоположении из класса [GeoLocator](https://msdn.microsoft.com/library/windows/apps/windows.devices.geolocation.geolocator.aspx). Приложениям для занятий спортом и навигации требуется постоянно отслеживать местоположение пользователя, поэтому при работе с ними всегда следует задавать это значение.

Расширенный сеанс выполнения для отслеживания расположения может выполняться любое время, в том числе при блокировке экрана на мобильном устройстве. Однако на одном устройстве может быть запущен только один такой сеанс. Расширенный сеанс выполнения для отслеживания местоположения можно запросить, только когда приложение находится на переднем плане и в состоянии **Running (Выполнение)**. Это гарантирует, что пользователь будет осведомлен о том, что приложение инициализировало расширенный сеанс выполнения для отслеживания местоположения. Также можно использовать класс GeoLocator, когда приложение находится в фоновом режиме с помощью фоновой задачи или службы приложения без запроса расширенного сеанса выполнения для отслеживания местоположения.

## <a name="save-critical-data-locally"></a>Локальное сохранение важных данных

Задайте значение **ExtendedExecutionReason.SavingData** при создании класса **ExtendedExecutionSession**, чтобы данные пользователя сохранялись, когда это требуется. В противном случае, если данные не будут сохранены перед завершением работы приложения, они будут утеряны, что негативно отразиться на удобстве работы пользователя.

Не используйте подобные сеансы, чтобы продлить жизненный цикл приложения для отправки или скачивания данных. Если вам нужно отправить данные, запросите [фоновую передачу данных](https://msdn.microsoft.com/windows/uwp/networking/background-transfers) или зарегистрируйте класс **MaintenanceTrigger**, который будет отвечать за передачу данных при наличии питания от сети переменного тока. Расширенный сеанс выполнения **ExtendedExecutionReason.SavingData** можно запросить, когда приложение находится на переднем плане и в состоянии **Running (Выполнение)** или в фоновом режиме и в состоянии **Suspending (Приостановка)**.

Состояние **Suspending (Приостановка)**— это последняя возможность приложения продолжать выполнение в течение его жизненного цикла, перед тем как его работа будет завершена. **ExtendedExecutionReason.SavingData**— это единственный тип сеанса **ExtendedExecutionSession**, который можно запросить в состоянии **Suspending**. Отправка запроса на расширенный сеанс выполнения **ExtendedExecutionReason.SavingData**, когда приложение находится в состоянии **Suspending (Приостановка)**, создает условия для возникновения потенциальной проблемы, о которой вам следует знать. Если расширенный сеанс выполнения запрашивается, когда приложение находится в состоянии **Suspending (Приостановка)** и пользователь запрашивает повторный запуск приложения, запуск может занять много времени. Это обусловлено тем, что время расширенного сеанса выполнения должно истечь до того, как старый экземпляр приложения сможет быть закрыт, чтобы можно было запустить новый. Сохранение пользовательской среды обеспечивается за счет продления времени запуска приложения.

## <a name="request-disposal-and-revocation"></a>Запрос, ликвидация и отзыв

Существует три основных способа взаимодействия с расширенным сеансом выполнения: запрос, ликвидация и отзыв.  Процесс создания запроса смоделирован в следующем примере кода.

### <a name="request"></a>Запрос

```csharp
var newSession = new ExtendedExecutionSession();
newSession.Reason = ExtendedExecutionReason.Unspecified;
newSession.Revoked += SessionRevoked;
ExtendedExecutionResult result = await newSession.RequestExtensionAsync();

switch (result)
{
    case ExtendedExecutionResult.Allowed:
        DoLongRunningWork();
        break;

    default:
    case ExtendedExecutionResult.Denied:
        DoShortRunningWork();
        break;
}
```
[См. пример кода](https://github.com/Microsoft/Windows-universal-samples/blob/master/Samples/ExtendedExecution/cs/Scenario1_UnspecifiedReason.xaml.cs#L81-L110)  

При вызове метода **RequestExtensionAsync** выполняется проверка операционной системы на предмет того, подтвердил ли пользователь работу приложения в фоновом режиме, а также достаточно ли в системе ресурсов для активации выполнения приложения в фоновом режиме. Только один сеанс будет одобрен для приложения в любой момент времени, из-за чего поступление последующих вызовов к **RequestExtensionAsync** будет приводить к отклонению сеанса.

Вы можете проверить класс [BackgroundExecutionManager](https://msdn.microsoft.com/library/windows/apps/windows.applicationmodel.background.backgroundexecutionmanager.aspx) заранее, чтобы узнать значение перечисления [BackgroundAccessStatus](https://msdn.microsoft.com/library/windows/apps/windows.applicationmodel.background.backgroundaccessstatus.aspx?f=255&MSPPError=-2147217396), задаваемое пользователем и указывающее на то, может ли приложение выполняться в фоновом режиме. Дополнительные сведения об этих пользовательских параметрах см. в разделе [Фоновые процессы и электропитание](https://blogs.windows.com/buildingapps/2016/08/01/battery-awareness-and-background-activity/#XWK8mEgWD7JHvC10.97).

Перечисление **ExtendedExecutionReason** указывает на процесс, который ваше приложение выполняет в фоновом режиме. Строка **Description (Описание)**— это строка в читаемом формате, содержащая объяснение того, почему вашему приложению требуется выполнить конкретный процесс. Эта строка не отображается для пользователя, но может стать доступна в будущих выпусках Windows. Чтобы обеспечить корректную остановку расширенного сеанса выполнения, когда пользователь или система решат прекратить работу приложения в фоновом режиме, необходимо использовать обработчик событий отзыва **Revoked**.

### <a name="revoked"></a>Отзыв

Если приложение запущено в расширенном сеансе выполнения, а системе необходимо остановить фоновый процесс из-за того, что приложению переднего плана требуются ресурсы, то этот сеанс отзывается. Расширенный сеанс выполнения прерывается только после запуска обработчика событий **Revoked (Отзыв)**.

После запуска события **Revoked (Отзыв)** для расширенного сеанса выполнения **ExtendedExecutionReason.SavingData** у приложения есть одна секунда на то, чтобы завершить выполняемый процесс и приостановку— **Suspending**.

Отзыв может возникать по многим причинам: по достижении временного предела выполнения, квоты электроэнергии, выделенной для работы фонового процесса, либо когда требуется высвободить память, чтобы пользователь смог запустить новое приложение в фоновом режиме.

Ниже приведен пример обработчика событий Revoked (Отзыв):

```cs
private async void SessionRevoked(object sender, ExtendedExecutionRevokedEventArgs args)
{
    await Dispatcher.RunAsync(CoreDispatcherPriority.Normal, () =>
    {
        switch (args.Reason)
        {
            case ExtendedExecutionRevokedReason.Resumed:
                rootPage.NotifyUser("Extended execution revoked due to returning to foreground.", NotifyType.StatusMessage);
                break;

            case ExtendedExecutionRevokedReason.SystemPolicy:
                rootPage.NotifyUser("Extended execution revoked due to system policy.", NotifyType.StatusMessage);
                break;
        }

        EndExtendedExecution();
    });
}
```
[См. пример кода](https://github.com/Microsoft/Windows-universal-samples/blob/master/Samples/ExtendedExecution/cs/Scenario1_UnspecifiedReason.xaml.cs#L124-L141)

### <a name="dispose"></a>Ликвидация

Последний шаг— это ликвидация расширенного сеанса выполнения. Сеансы, а также любые другие потребляющие память ресурсы, необходимо ликвидировать, чтобы электроэнергия, используемая приложением во время ожидания закрытия сеанса, не шла в зачет квоты на электроэнергию для работы самого приложения. С целью сохранения как можно большего объема квоты на электроэнергию для приложения, необходимо ликвидировать сеанс, когда работа с ним завершена, чтобы приложение смогло быстрее перейти в состояние **Suspended (Приостановка)**.

Самостоятельная ликвидация сеанса, а не ожидание возникновения события отзыва позволяет снизить интенсивность использования квоты на электроэнергию для вашего приложения. Это значит, что ваше приложение сможет выполняться в фоновом режиме дольше во время следующих сеансов, так как у вас будет больший объем квоты на электроэнергию. Чтобы можно было вызвать метод **Dispose (Ликвидация)** для объекта **ExtendedExecutionSession**, на него должна быть установлена ссылка до завершения процесса.

Фрагмент кода, который ликвидирует расширенный сеанс выполнения.

```cs
void ClearExtendedExecution(ExtendedExecutionSession session)
{
    if (session != null)
    {
        session.Revoked -= SessionRevoked;
        session.Dispose();
        session = null;
    }
}
```
[См. пример кода](https://github.com/Microsoft/Windows-universal-samples/blob/master/Samples/ExtendedExecution/cs/Scenario1_UnspecifiedReason.xaml.cs#L49-L63)

Для одного приложения одновременно может быть запущен только один расширенный сеанс выполнения класса **ExtendedExecutionSession**. Многие приложения используют асинхронные задачи для выполнения сложных процессов, которым требуется доступ к таким ресурсам, как память, сеть или сетевые службы. Если для выполнения процесса требуется несколько асинхронных задач, необходимо учитывать состояние каждой из этих задач, перед тем как ликвидировать сеанс **ExtendedExecutionSession** и приостановить приложение. Для этого необходимо установить ссылку для подсчета количества задач, которые все еще выполняются, и не ликвидировать сеанс, пока это значение не достигнет нуля.

Ниже приведен пример кода, осуществляющий управление несколькими задачами в ходе работы расширенного сеанса выполнения. Дополнительные сведения о том, как использовать эту возможность в вашем приложении, см. в примере кода по ссылке ниже.

```cs
static class ExtendedExecutionHelper
{
    private static ExtendedExecutionSession session = null;
    private static int taskCount = 0;

    public static bool IsRunning
    {
        get
        {
            if (session != null)
            {
                return true;
            }
            else
            {
                return false;
            }
        }
    }

    public static async Task<ExtendedExecutionResult> RequestSessionAsync(ExtendedExecutionReason reason, TypedEventHandler<object, ExtendedExecutionRevokedEventArgs> revoked, String description)
    {
        // The previous Extended Execution must be closed before a new one can be requested.       
        ClearSession();

        var newSession = new ExtendedExecutionSession();
        newSession.Reason = reason;
        newSession.Description = description;
        newSession.Revoked += SessionRevoked;

        // Add a revoked handler provided by the app in order to clean up an operation that had to be halted prematurely
        if(revoked != null)
        {
            newSession.Revoked += revoked;
        }

        ExtendedExecutionResult result = await newSession.RequestExtensionAsync();

        switch (result)
        {
            case ExtendedExecutionResult.Allowed:
                session = newSession;
                break;
            default:
            case ExtendedExecutionResult.Denied:
                newSession.Dispose();
                break;
        }
        return result;
    }

    public static void ClearSession()
    {
        if (session != null)
        {
            session.Dispose();
            session = null;
        }

        taskCount = 0;
    }

    public static Deferral GetExecutionDeferral()
    {
        if (session == null)
        {
            throw new InvalidOperationException("No extended execution session is active");
        }

        taskCount++;
        return new Deferral(OnTaskCompleted);
    }

    private static void OnTaskCompleted()
    {
        if (taskCount > 0)
        {
            taskCount--;
        }
        
        //If there are no more running tasks than end the extended lifetime by clearing the session
        if (taskCount == 0 && session != null)
        {
            ClearSession();
        }
    }

    private static void SessionRevoked(object sender, ExtendedExecutionRevokedEventArgs args)
    {
        //The session has been prematurely revoked due to system constraints, ensure the session is disposed
        if (session != null)
        {
            session.Dispose();
            session = null;
        }
        
        taskCount = 0;
    }
}
```
[См. пример кода](https://github.com/Microsoft/Windows-universal-samples/blob/master/Samples/ExtendedExecution/cs/Scenario4_MultipleTasks.xaml.cs)

## <a name="ensure-that-your-app-uses-resources-well"></a>Проверка использования ресурсов приложением

Настройка использования памяти и электроэнергии вашим приложением является важным шагом для обеспечения того, что операционная система будет разрешать вашему приложению выполняться, когда оно больше не будет работать на переднем плане. Используйте [API-интерфейсы управления памятью](https://msdn.microsoft.com/library/windows/apps/windows.system.memorymanager.aspx), чтобы узнать, сколько памяти потребляет ваше приложение. Чем больше памяти потребляет ваше приложение, тем сложнее операционной системе поддерживать его выполнение, когда другое приложение работает на переднем плане. Пользователь полностью контролирует все фоновые действия, которые может выполнять ваше приложение, и видит, как ваше приложение влияет на расход заряда батареи.

Используйте метод [BackgroundExecutionManager.RequestAccessAsync](https://msdn.microsoft.com/library/windows/apps/windows.applicationmodel.background.backgroundexecutionmanager.aspx), чтобы определить, принял ли пользователь решение ограничить фоновую активность вашего приложения. Следите за расходом заряда батареи и запускайте приложения в фоновом режиме только тогда, когда необходимо завершить интересующее пользователя действие.

## <a name="see-also"></a>См. также

[Пример кода расширенного сеанса выполнения](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/ExtendedExecution)  
[Жизненный цикл приложения](https://msdn.microsoft.com/windows/uwp/launch-resume/app-lifecycle)  
[Жизненный цикл приложения — сохранение приложений в активном состоянии с помощью фоновых задач и расширенного сеанса выполнения](https://msdn.microsoft.com/en-us/magazine/mt590969.aspx)
[Управление памятью при переходе приложения в фоновый режим](https://msdn.microsoft.com/windows/uwp/launch-resume/reduce-memory-usage)  
[Фоновая передача данных](https://msdn.microsoft.com/windows/uwp/networking/background-transfers)  
[Осведомленность о состоянии аккумулятора и фоновые действия](https://blogs.windows.com/buildingapps/2016/08/01/battery-awareness-and-background-activity/#I2bkQ6861TRpbRjr.97)  
[Класс MemoryManager](https://msdn.microsoft.com/library/windows/apps/windows.system.memorymanager.aspx)  
[Воспроизведение мультимедиа в фоновом режиме](https://msdn.microsoft.com/windows/uwp/audio-video-camera/background-audio)  