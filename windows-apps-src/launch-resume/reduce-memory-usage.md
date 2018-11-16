---
author: TylerMSFT
ms.assetid: 3a3ea86e-fa47-46ee-9e2e-f59644c0d1db
description: В этой статье рассказывается о том, как уменьшить потребление памяти, когда приложение переходит в фоновый режим.
title: Уменьшение потребления памяти при переходе приложения в фоновый режим
ms.author: twhitney
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: eef1edc4e5c725756cdef788bf555f706621741d
ms.sourcegitcommit: e2fca6c79f31e521ba76f7ecf343cf8f278e6a15
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/16/2018
ms.locfileid: "6990022"
---
# <a name="free-memory-when-your-app-moves-to-the-background"></a>Освобождение памяти при переходе приложения в фоновый режим

В этой статье рассказывается о том, как уменьшить потребление приложением, когда оно переходит в фоновое состояние, чтобы приложение не было приостановлено или, возможно, завершено.

## <a name="new-background-events"></a>Новые фоновые события

В Windows 10 версии 1607 впервые представлены два новых события жизненного цикла приложений: [**EnteredBackground**](https://msdn.microsoft.com/library/windows/apps/Windows.ApplicationModel.Core.CoreApplication.EnteredBackground) и [**LeavingBackground**](https://msdn.microsoft.com/library/windows/apps/Windows.ApplicationModel.Core.CoreApplication.LeavingBackground). Эти события позволяют приложению определить, когда оно входит в фоновый режим и выходит из него.

Когда приложение переходит в фоновый режим, принудительные системные ограничения памяти могут измениться. Используйте эти события, чтобы проверить текущее потребление памяти и свободные ресурсы и не превысить лимит. В случае превышения лимита работа вашего приложения будет приостановлена и, возможно, завершена, пока оно находится в фоновом режиме.

### <a name="events-for-controlling-your-apps-memory-usage"></a>События для контроля использования памяти приложения

[MemoryManager.AppMemoryUsageLimitChanging](https://msdn.microsoft.com/library/windows/apps/windows.system.memorymanager.appmemoryusagelimitchanging.aspx) создается сразу перед тем, как поменяется лимит общей памяти, доступной приложению. Например, если приложение переходит в фоновый режим и выполняется на Xbox, лимит памяти меняется с 1024МБ до 128МБ.  
Очень важно правильно обработать это событие, чтобы платформа не приостановила и не завершила работу приложения.

[MemoryManager.AppMemoryUsageIncreased](https://msdn.microsoft.com/library/windows/apps/windows.system.memorymanager.appmemoryusageincreased.aspx) создается, если потребление памяти приложением выросло до более высокого значения в перечислении [AppMemoryUsageLevel](https://msdn.microsoft.com/library/windows/apps/windows.system.appmemoryusagelevel.aspx). Например, с **Low** на **Medium**. Обрабатывать это событие не обязательно, но рекомендуется, поскольку приложение по-прежнему не должно превышать лимит.

[MemoryManager.AppMemoryUsageDecreased](https://msdn.microsoft.com/library/windows/apps/windows.system.memorymanager.appmemoryusagedecreased.aspx) создается, если потребление памяти приложением снизилось до более низкого значения в перечислении **AppMemoryUsageLevel**. Например, с **High** до **Low**. Обрабатывать это событие необязательно, однако оно указывает на то, что приложение при необходимости может иметь возможность выделить дополнительную память.

## <a name="handle-the-transition-between-foreground-and-background"></a>Обработка перехода между передним планом и фоновым режимом

Когда приложение переходит с переднего плана в фоновый режим, создается событие [**EnteredBackground**](https://msdn.microsoft.com/library/windows/apps/Windows.ApplicationModel.Core.CoreApplication.EnteredBackground). Когда приложение возвращается на передний план, вызывается событие [**LeavingBackground**](https://msdn.microsoft.com/library/windows/apps/Windows.ApplicationModel.Core.CoreApplication.LeavingBackground). Во время создания приложения можно регистрировать обработчики для этих событий. Для этого в шаблоне проекта по умолчанию это выполняется в конструкторе классов **приложений** в файле App.xaml.cs.

Так как работа в фоновом режиме уменьшает объем памяти, доступный приложению, вам также следует зарегистрировать события [**AppMemoryUsageIncreased**](https://msdn.microsoft.com/library/windows/apps/Windows.System.MemoryManager.AppMemoryUsageIncreased)и [**AppMemoryUsageLimitChanging**](https://msdn.microsoft.com/library/windows/apps/Windows.System.MemoryManager.AppMemoryUsageLimitChanging), которые можно использовать, чтобы проверять текущий объем используемой памяти и текущее ограничение. Обработчики этих событий показаны в следующих примерах. Дополнительные сведения о жизненном цикле приложений UWP см. в разделе [Жизненный цикл приложения](..//launch-resume/app-lifecycle.md).

[!code-cs[RegisterEvents](./code/ReduceMemory/cs/App.xaml.cs#SnippetRegisterEvents)]

Когда вызывается событие [**EnteredBackground**](https://msdn.microsoft.com/library/windows/apps/Windows.ApplicationModel.Core.CoreApplication.EnteredBackground), задайте переменной отслеживания значение, чтобы указать, что сейчас приложение работает в фоновом режиме. Это полезно, когда вы создаете код для уменьшения использования памяти.

[!code-cs[EnteredBackground](./code/ReduceMemory/cs/App.xaml.cs#SnippetEnteredBackground)]

Когда приложение переходит в фоновый режим, система снижает ограничение доступной ему памяти, чтобы у приложения на переднем плане было достаточно ресурсов.

Обработчик события [**AppMemoryUsageLimitChanging**](https://msdn.microsoft.com/library/windows/apps/Windows.System.MemoryManager.AppMemoryUsageLimitChanging) позволяет приложению узнать, что объем доступной памяти уменьшен, а также предоставляет новое ограничение в аргументах события, переданных в обработчик. Сравните свойство [**MemoryManager.AppMemoryUsage**](https://msdn.microsoft.com/library/windows/apps/Windows.System.MemoryManager.AppMemoryUsage), которое предоставляет сведения о текущем используемом объеме памяти приложения, и свойство [**NewLimit**](https://msdn.microsoft.com/library/windows/apps/Windows.System.AppMemoryUsageLimitChangingEventArgs.NewLimit) аргументов события, которое определяет новое ограничение. Если вы превышаете ограничение, необходимо сократить потребление памяти.

В данном примере это делается во вспомогательном методе **ReduceMemoryUsage**, определенного ниже в этой статье.

[!code-cs[MemoryUsageLimitChanging](./code/ReduceMemory/cs/App.xaml.cs#SnippetMemoryUsageLimitChanging)]

> [!NOTE]
> В некоторых конфигурациях устройства приложение может работать при превышении нового ограничения памяти, пока система не столкнется с дефицитом ресурсов, а в других конфигурациях это невозможно. Так, на консолях Xbox приложения будут приостановлены или закрыты, если они не уменьшат объем используемой памяти в течение 2 секунд. Это значит, что для оптимальной работы на самом широком спектре устройств используйте это событие, чтобы снизить объем используемых ресурсов в течение двух секунд после возникновения события.

Бывает так, что использование ресурсов памяти приложением в настоящее время, после первого перехода в фоновый режим, находится в допустимых пределах памяти для фоновых приложений, впоследствии расходование памяти увеличивается и начинает приближаться к лимиту. Обработчик события [**AppMemoryUsageIncreased**](https://msdn.microsoft.com/library/windows/apps/Windows.System.MemoryManager.AppMemoryUsageIncreased) позволяет проверить ваше текущий объем используемых ресурсов и, при необходимости, освободить память.

Если значение [**AppMemoryUsageLevel**](https://msdn.microsoft.com/library/windows/apps/Windows.System.AppMemoryUsageLevel) равно **High** или **OverLimit**, уменьшите объем используемой памяти. В этом примере процесс реализуется вспомогательным методом **ReduceMemoryUsage**. Вы также можете подписаться события [**AppMemoryUsageDecreased**](https://msdn.microsoft.com/library/windows/apps/Windows.System.MemoryManager.AppMemoryUsageDecreased), чтобы убедиться, что ваше приложение не превышает ограничение, и при необходимости выделить дополнительные ресурсы.

[!code-cs[MemoryUsageIncreased](./code/ReduceMemory/cs/App.xaml.cs#SnippetMemoryUsageIncreased)]

**ReduceMemoryUsage** — это вспомогательный метод , который вы можете реализовать, чтобы освободить память, когда приложение превышает ограничение для приложений, работающих в фоновом режиме. Способ освобождения памяти зависит от конкретного приложения, но в общем случае рекомендуется освобождать ресурсы пользовательского интерфейса и другие ресурсы, связанные с визуализацией. Для этого убедитесь, что работаете в фоновом режиме, затем задайте для свойства [**Содержимое**](https://msdn.microsoft.com/library/windows/apps/Windows.UI.Xaml.Window.Content) окна вашего приложения значение `null` и отмените регистрацию обработчиков событий ИП, а затем удалите любые существующие ссылки на страницу. Если вы не отмените регистрацию своих обработчиков событий пользовательского интерфейса и не удалите все ссылки на страницу, ресурсы страницы освобождены не будут. Затем вызовите метод **GC.Collect**, чтобы немедленно воспользоваться освободившейся памятью. Обычно сбор мусора не запускается принудительно, потому что система берет эту заботу на себя. В этом конкретном случае мы сокращаем объем памяти, выделенной этому приложению, при его переходе в фоновый режим, чтобы снизить вероятность того, что система завершит работу приложения для высвобождения памяти.

[!code-cs[UnloadViewContent](./code/ReduceMemory/cs/App.xaml.cs#SnippetUnloadViewContent)]

Если содержимое окна собирается, каждый Frame начинает процесс отключения. Если в визуальном дереве объектов в разделе содержимого окна есть страницы, будут вызываться события Unloaded. Страницы невозможно полностью удалить из памяти, если не удалить все ссылки на них. В обратном вызове Unloaded выполните следующие действия, чтобы быстро освободить память.
* Очистите все крупные структуры данных на странице и присвойте им значение `null`.
* Отмените регистрацию всех обработчиков событий, в которых есть методы обратного вызова на странице. Зарегистрируйте эти обратные вызовы в обработчике события Loaded для страницы. Событие Loaded наступает, когда пользовательский интерфейс восстанавливается, а страница добавляется в визуальное дерево объектов.
* Вызовите метод `GC.Collect` в конце обратного вызова Unloaded, чтобы быстро собрать мусор всех крупных структур данных, которым вы присвоили значение `null`. Опять же, обычно сбор мусора не запускается принудительно, потому что система берет эту заботу на себя. В этом конкретном случае мы сокращаем объем памяти, выделенной этому приложению, при его переходе в фоновый режим, чтобы снизить вероятность того, что система завершит работу приложения для высвобождения памяти.

[!code-cs[MainPageUnloaded](./code/ReduceMemory/cs/App.xaml.cs#SnippetMainPageUnloaded)]

В обработчике событий [**LeavingBackground**](https://msdn.microsoft.com/library/windows/apps/Windows.ApplicationModel.Core.CoreApplication.LeavingBackground) следует задать переменную отслеживания (`isInBackgroundMode`), чтобы указать, что приложение больше не работает в фоновом режиме. Затем проверьте, не присвоено ли свойству [**Content**](https://msdn.microsoft.com/library/windows/apps/Windows.UI.Xaml.Window.Content) текущего окна значение `null`, что происходит, если вы удалили представления приложения, чтобы очистить память в фоновом режиме. Если содержимого окна имеет значение `null`, перестройте представление приложения. В этом примере содержимое окна создано во вспомогательном методе **CreateRootFrame**.

[!code-cs[LeavingBackground](./code/ReduceMemory/cs/App.xaml.cs#SnippetLeavingBackground)]

Вспомогательный метод **CreateRootFrame** воссоздает содержимое представления приложения. Код в этом методе почти идентичен коду обработчика [**OnLaunched**](https://msdn.microsoft.com/library/windows/apps/br242335), представленного в шаблоне проекта. Единственное отличие состоит в том, что обработчик **Launching** определяет предыдущее состояние выполнения на основе свойства [**PreviousExecutionState**](https://msdn.microsoft.com/library/windows/apps/Windows.ApplicationModel.Activation.LaunchActivatedEventArgs.PreviousExecutionState) объекта [**LaunchActivatedEventArgs**](https://msdn.microsoft.com/library/windows/apps/Windows.ApplicationModel.Activation.LaunchActivatedEventArgs), а метод **CreateRootFrame** просто получает предыдущее состояние, переданное в качестве аргумента. Чтобы не дублировать код, можно выполнить рефакторинг кода обработчика событий **Launching** по умолчанию, чтобы вызывать **CreateRootFrame**.

[!code-cs[CreateRootFrame](./code/ReduceMemory/cs/App.xaml.cs#SnippetCreateRootFrame)]

## <a name="guidelines"></a>Руководства

### <a name="moving-from-the-foreground-to-the-background"></a>Переход с переднего плана в фоновый режим

При перемещении приложения с переднего плана в фоновый режим система действует от имени приложения, чтобы освободить ресурсы, которые не требуются в фоновом режиме. Например, платформы ИП удаляют кэшированные текстуры, а подсистема видео освобождает память, выделенную от имени приложения. Однако приложению по-прежнему необходимо тщательно отслеживать использование памяти, чтобы его работа не была приостановлена или прекращена системой.

Когда приложения перемещается с переднего плана в фоновый режим, оно сначала получает событие **EnteredBackground**, а затем— **AppMemoryUsageLimitChanging**.

- **Используйте** событие **EnteredBackground**,чтобы освободить ресурсы ИП, которые, насколько вам известно, не нужны приложению в фоновом режиме. Например, можно освободить изображение обложки для композиции.
- **Используйте** событие **AppMemoryUsageLimitChanging**, чтобы убедиться, что приложение потребляет меньше памяти, чем указано в лимите для фонового режима. В противном случае не забудьте освободить ресурсы. Если этого не сделать, работа приложения может быть приостановлена или прекращена в соответствии с политикой для конкретного устройства.
- **Вызывайте** сборщик мусора вручную, если приложение превышает лимит использования памяти, когда создается событие **AppMemoryUsageLimitChanging**.
- **Используйте** событие **AppMemoryUsageIncreased**, чтобы продолжить мониторинг использования памяти приложения, работая в фоновом режиме, если вы допускаете, что этот показатель может измениться. Если **AppMemoryUsageLevel** имеет значение **High** или **OverLimit**, не забудьте освободить ресурсы.
- **Оцените целесообразность** освобождения ресурсов ИП в обработчике событий **AppMemoryUsageLimitChanging** вместо **EnteredBackground** в качестве средства оптимизации производительности. Используйте логическое значение, заданное в обработчиках событий **EnteredBackground/LeavingBackground**, чтобы отследить, работает ли приложение в фоновом режиме или на переднем плане. Затем в обработчике событий **AppMemoryUsageLimitChanging** можно освободить ресурсы ИП, если **AppMemoryUsage** превышает лимит и приложение работает в фоновом режиме (на основе логического значения).
- **Не** выполняйте длительные операции в событии **EnteredBackground**, поскольку в этом случае переключение между приложениями может казаться пользователю медленнее.

### <a name="moving-from-the-background-to-the-foreground"></a>Переход с фонового режима на передний план

Когда приложения перемещаются с фонового режима на передний план, приложение сначала получит событие **AppMemoryUsageLimitChanging**, а затем— **LeavingBackground**.

- **Используйте** событие **LeavingBackground**, чтобы восстановить ресурсы ИП, освобожденные приложением при переходе в фоновый режим.

## <a name="related-topics"></a>Связанные статьи

* [Пример воспроизведения мультимедиа в фоновом режиме](http://go.microsoft.com/fwlink/p/?LinkId=800141)— показывает, как освободить память при переходе приложения в фоновый режим.
* [Диагностические инструменты](https://blogs.msdn.microsoft.com/visualstudioalm/2015/01/16/diagnostic-tools-debugger-window-in-visual-studio-2015/)— используйте диагностические инструменты, чтобы наблюдать за событиями сбора мусора и убедиться, что приложение освобождает память ожидаемым образом.
