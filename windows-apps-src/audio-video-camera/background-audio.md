---
author: drewbatgit
ms.assetid: 
description: "В этой статье показано, как воспроизводить мультимедиа, когда приложение работает в фоновом режиме."
title: "Воспроизведение мультимедиа в фоновом режиме"
translationtype: Human Translation
ms.sourcegitcommit: c8cbc538e0979f48b657197d59cb94a90bc61210
ms.openlocfilehash: a477827553ac1780ac625deeee08d84ab638d4c2

---

# Воспроизведение мультимедиа в фоновом режиме
В этой статье показано, как настроить приложение, чтобы воспроизведение мультимедиа продолжалось, когда приложение переходит в фоновый режим. Это значит, что даже после того, как пользователь свернет приложение, вернется на начальный экран или выйдет из приложения другим способом, ваше приложение продолжит воспроизводить звук. 

Сценарии воспроизведения звука в фоновом режиме включают следующие элементы.

-   **Долговременные плей-листы.** Пользователь кратковременно вызывает приложение переднего плана, чтобы выбрать и запустить плей-лист. После этого пользователь ожидает, что плей-лист продолжит воспроизведение в фоновом режиме.

-   **Использование переключателя задач:** пользователь кратковременно вызывает приложение переднего плана, чтобы запустить воспроизведение звука, а затем с помощью переключателя задач переключается на другое открытое приложение. Пользователь ожидает, что воспроизведение звука продолжится в фоновом режиме.

В этой статье описано, как повсеместно реализовать воспроизведение приложением звука в фоновом режиме на всех устройствах с Windows, включая мобильные устройства, настольные компьютеры и консоли Xbox.

> [!NOTE]
> В этой статье используется код, адаптированный из [примера воспроизведения звука в фоновом режиме на платформе UWP](http://go.microsoft.com/fwlink/p/?LinkId=800141).

## Описание модели одного процесса.
В Windows 10 версии 1607 представлена новая модель одного процесса, которая значительно упрощает работу с фоновым звуком. Ранее приложение должно было управлять фоновым процессом, в дополнение к приложению переднего плана, а затем вручную передавать изменения состояния между двумя процессами. В новой модели вы просто добавляете возможность фонового звука в манифест приложения, и оно автоматически будет продолжать воспроизводить звук после перехода в фоновый режим. Два новых события жизненных цикла приложения, [**EnteredBackground**](https://msdn.microsoft.com/library/windows/apps/Windows.ApplicationModel.Core.CoreApplication.EnteredBackground) и [**LeavingBackground**](https://msdn.microsoft.com/library/windows/apps/Windows.ApplicationModel.Core.CoreApplication.LeavingBackground), позволяют приложению определить, когда оно переходит в фоновый режим и выходит из него. Когда приложение переходит в фоновый режим или выходит из него, ограничения памяти, применяемые системой, могут изменяться, поэтому вы можете использовать эти события, чтобы узнать текущий объем используемой памяти и освободить ресурсы, чтобы не нарушать ограничение.

За счет устранения сложных механизмов взаимодействия между процессами и управления состоянием новая модель позволяет гораздо быстрее реализовать фоновое воспроизведение звука, значительно сократив объем кода. Однако модель с двумя процессами по-прежнему поддерживается в текущем выпуске для обеспечения обратной совместимости. Подробнее: [Старая модель воспроизведения звука в фоновом режиме](background-audio.md).

## Требования для фонового воспроизведения звука
Приложение должно соответствовать следующим требованиям для воспроизведения звука в фоновом режиме.

* Добавьте возможность **Воспроизведение мультимедиа в фоновом режиме** в манифест приложения, как описано далее в этой статье.
* Если приложение отключает автоматическую интеграцию **MediaPlayer** с системными элементами управления транспортировкой мультимедиа (SMTC), например если свойству [**CommandManager.IsEnabled**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Playback.MediaPlaybackCommandManager.IsEnabled) присвоено значение false, то необходимо вручную реализовать интеграцию с SMTC для использования функции воспроизведения мультимедиа в фоновом режиме. Кроме того, следует вручную включить интеграцию с SMTC, если вы используете API-интерфейс, отличный от **MediaPlayer**, такой как [**AudioGraph**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Audio.AudioGraph), чтобы продолжать воспроизводить звук после перехода приложения в фоновый режим. Минимальные требования к интеграции SMTC описаны в разделе "Использование системных элементов управления транспортом мультимедиа для воспроизведения звука в фоновом режиме" статьи [Ручное управление системными элементами управления воспроизведением мультимедиа](system-media-transport-controls.md).
* Когда приложение работает в фоновом режиме, вам нужно следить за тем, чтобы не превышать ограничения использования памяти, заданные системой для фоновых приложений. Рекомендации по управлению памятью в фоновом режиме представлены далее в этой статье.

## Возможность воспроизведения мультимедиа в фоновом режиме в манифесте
Чтобы включить фоновое воспроизведение звука, следует добавить возможность воспроизведения мультимедиа в фоновом режиме в файл манифеста приложения, Package.appxmanifest. 

**Добавление возможностей в манифест приложения с помощью конструктора манифестов**

1.  В Microsoft Visual Studio откройте конструктор манифеста приложения, дважды щелкнув элемент **package.appxmanifest** в **Обозревателе решений**.
2.  Перейдите на вкладку **Возможности**.
3.  Установите флажок **Воспроизведение мультимедиа в фоновом режиме**.

Чтобы вручную добавить возможность в XML-файле манифеста приложения, сначала убедитесь, что в элементе **Package** задан префикс пространства имен *uap3*. Если это не так, добавьте его, как показано ниже.
```xml
<Package
  xmlns="http://schemas.microsoft.com/appx/manifest/foundation/windows10"
  xmlns:mp="http://schemas.microsoft.com/appx/2014/phone/manifest"
  xmlns:uap="http://schemas.microsoft.com/appx/manifest/uap/windows10"
  xmlns:uap3="http://schemas.microsoft.com/appx/manifest/uap/windows10/3"
  IgnorableNamespaces="uap uap3 mp">
```

Затем добавьте возможность *backgroundMediaPlayback* в элемент **Capabilities**:
```xml
<Capabilities>
    <uap3:Capability Name="backgroundMediaPlayback"/>
</Capabilities>
```

##Обработка перехода между передним планом и фоновым режимом
Когда приложение переходит с переднего плана в фоновый режим, создается событие [**EnteredBackground**](https://msdn.microsoft.com/library/windows/apps/Windows.ApplicationModel.Core.CoreApplication.EnteredBackground). Когда приложение возвращается на передний план, вызывается событие [**LeavingBackground**](https://msdn.microsoft.com/library/windows/apps/Windows.ApplicationModel.Core.CoreApplication.LeavingBackground). Поскольку это события жизненного цикла приложения, вам следует зарегистрировать обработчики этих событий при создании приложения. Для этого в шаблоне проекта по умолчанию нужно добавить его в конструктор класса **App** в файле App.xaml.cs. Так как работа в фоновом режиме уменьшает объем памяти, доступный приложению, вам также следует зарегистрировать события [**AppMemoryUsageIncreased**](https://msdn.microsoft.com/library/windows/apps/Windows.System.MemoryManager.AppMemoryUsageIncreased)и [**AppMemoryUsageLimitChanging**](https://msdn.microsoft.com/library/windows/apps/Windows.System.MemoryManager.AppMemoryUsageLimitChanging), которые будут использоваться, чтобы проверять текущий объем используемой памяти и текущее ограничение. Обработчики этих событий показаны в следующих примерах. Дополнительные сведения о жизненном цикле приложений UWP см. в разделе [Жизненный цикл приложения](../\launch-resume\app-lifecycle.md).

[!code-cs[RegisterEvents](./code/BackgroundAudio_RS1/cs/App.xaml.cs#SnippetRegisterEvents)]

Создайте переменную для отслеживания состояния приложения (передний план или фоновый режим).

[!code-cs[DeclareBackgroundMode](./code/BackgroundAudio_RS1/cs/App.xaml.cs#SnippetDeclareBackgroundMode)]

Когда вызывается событие [**EnteredBackground**](https://msdn.microsoft.com/library/windows/apps/Windows.ApplicationModel.Core.CoreApplication.EnteredBackground), задайте переменной отслеживания значение, чтобы указать, что сейчас приложение работает в фоновом режиме. В событии **EnteredBackground** не следует выполнять длительные задачи, так как это может привести к замедлению перехода приложения в фоновый режиме.

[!code-cs[EnteredBackground](./code/BackgroundAudio_RS1/cs/App.xaml.cs#SnippetEnteredBackground)]

Когда приложение переходит в фоновый режим, система снижает ограничение доступной ему памяти, чтобы у приложения на переднем плане было достаточно ресурсов. Обработчик события [**AppMemoryUsageLimitChanging**](https://msdn.microsoft.com/library/windows/apps/Windows.System.MemoryManager.AppMemoryUsageLimitChanging) позволяет приложению узнать, что объем доступной памяти уменьшен, а также предоставляет новое ограничение в аргументах события, переданных в обработчик. Сравните свойство [**MemoryManager.AppMemoryUsage**](https://msdn.microsoft.com/library/windows/apps/Windows.System.MemoryManager.AppMemoryUsage), которое предоставляет сведения о текущем используемом объеме памяти приложения, и свойство [**NewLimit**](https://msdn.microsoft.com/library/windows/apps/Windows.System.AppMemoryUsageLimitChangingEventArgs.NewLimit) аргументов события, которое определяет новое ограничение. Если вы превышаете ограничение, необходимо сократить потребление памяти. В данном примере это делается во вспомогательном методе **ReduceMemoryUsage**, определенного ниже в этой статье.

[!code-cs[MemoryUsageLimitChanging](./code/BackgroundAudio_RS1/cs/App.xaml.cs#SnippetMemoryUsageLimitChanging)]

> [!NOTE] 
> В некоторых конфигурациях устройства приложение может работать при превышении нового ограничения памяти, пока система не столкнется с дефицитом ресурсов, а в других конфигурациях это невозможно. Так, на консолях Xbox приложения будут приостановлены или закрыты, если они не уменьшат объем используемой памяти в течение 2 секунд. Это значит, что для оптимальной работы на самом широком спектре устройств используйте это событие, чтобы снизить объем используемых ресурсов в течение двух секунд после возникновения события.


При первом переходе приложения в фоновый режим объем используемой памяти может не превышать ограничение, но впоследствии объем ресурсов увеличивается и начинает приближаться к пределу. Обработчик события [**AppMemoryUsageIncreased**](https://msdn.microsoft.com/library/windows/apps/Windows.System.MemoryManager.AppMemoryUsageIncreased) позволяет проверить ваше текущий объем используемых ресурсов и, при необходимости освободить память. Если значение [**AppMemoryUsageLevel**](https://msdn.microsoft.com/library/windows/apps/Windows.System.AppMemoryUsageLevel) равно **High** или **OverLimit**, уменьшите объем используемой памяти. Напоминаем, в этом примере процесс реализуется вспомогательным методом **ReduceMemoryUsage**. Вы также можете подписаться события [**AppMemoryUsageDecreased**](https://msdn.microsoft.com/library/windows/apps/Windows.System.MemoryManager.AppMemoryUsageDecreased), чтобы убедиться, что ваше приложение не превышает ограничение, и при необходимости выделить дополнительные ресурсы.

[!code-cs[MemoryUsageIncreased](./code/BackgroundAudio_RS1/cs/App.xaml.cs#SnippetMemoryUsageIncreased)]

**ReduceMemoryUsage** — это вспомогательный метод , который вы можете реализовать, чтобы освободить память, когда приложение превышает ограничение для приложений, работающих в фоновом режиме. Способ освобождения памяти зависит от конкретного приложения, но в общем случае рекомендуется освобождать ресурсы пользовательского интерфейса и другие ресурсы, связанные с визуализацией. Во-первых, убедитесь, что приложение работает в фоновом режиме, и присвойте свойству [**Content**](https://msdn.microsoft.com/library/windows/apps/Windows.UI.Xaml.Window.Content) окна приложения в значение NULL. Вызовите метод **GC.Collect**, чтобы система могла выделить освобожденную память.

[!code-cs[UnloadViewContent](./code/BackgroundAudio_RS1/cs/App.xaml.cs#SnippetUnloadViewContent)]

Если содержимое окна собираются, каждый кадр начинает процесс отключения. Если в визуальном дереве объектов в разделе содержимого окна есть страницы, будут вызываться события [**Unloaded**](https://msdn.microsoft.com/library/windows/apps/Windows.UI.Xaml.FrameworkElement.Unloaded). Страницы невозможно полностью удалить из памяти, если не удалить все ссылки на них. В обратном вызове **Unloaded** выполните следующие действия, чтобы быстро освободить память.
* Очистите все крупные структуры данных на странице и присвойте им значение NULL.
* Отмените регистрацию всех обработчиков событий, в которых есть методы обратного вызова на странице. Зарегистрируйте эти обратные вызовы в обработчике события Loaded для страницы. Событие Loaded наступает, когда пользовательский интерфейс восстанавливается, а страница добавляется в визуальное дерево объектов.
* Вызовите метод **GC.Collect** в конце обратного вызова Unloaded, чтобы быстро собрать мусор всех крупных структур данных, которым вы присвоили значение NULL.

[!code-cs[Unloaded](./code/BackgroundAudio_RS1/cs/MainPage.xaml.cs#SnippetUnloaded)]

В обработчике событий [**LeavingBackground**](https://msdn.microsoft.com/library/windows/apps/Windows.ApplicationModel.Core.CoreApplication.LeavingBackground) следует задать переменную отслеживания, чтобы указать, что приложение больше не работает в фоновом режиме. Затем проверьте, не присвоено ли свойству [**Content**](https://msdn.microsoft.com/library/windows/apps/Windows.UI.Xaml.Window.Content) текущего окна значение NULL, что происходит, если вы удалили представления приложения, чтобы очистить память в фоновом режиме. Если содержимого окна имеет значение NULL, перестройте представление приложения. В этом примере содержимое окна создано во вспомогательном методе **CreateRootFrame**.

[!code-cs[LeavingBackground](./code/BackgroundAudio_RS1/cs/App.xaml.cs#SnippetLeavingBackground)]

Вспомогательный метод **CreateRootFrame** воссоздает содержимое представления приложения. Код в этом методе идентичен коду обработчика [**OnLaunched**](https://msdn.microsoft.com/library/windows/apps/br242335), представленного в шаблоне проекта. Единственное отличие состоит в том, что обработчик **Launching** определяет предыдущее состояние выполнения на основе свойства [**PreviousExecutionState**](https://msdn.microsoft.com/library/windows/apps/Windows.ApplicationModel.Activation.LaunchActivatedEventArgs.PreviousExecutionState) объекта [**LaunchActivatedEventArgs**](https://msdn.microsoft.com/library/windows/apps/Windows.ApplicationModel.Activation.LaunchActivatedEventArgs), а метод **CreateRootFrame** просто получает предыдущее состояние, переданное в качестве аргумента. Чтобы не дублировать код, вы можете выполнить рефакторинг кода обработчика событий **Launching** по умолчанию, чтобы вызывать **CreateRootFrame**.

[!code-cs[CreateRootFrame](./code/BackgroundAudio_RS1/cs/App.xaml.cs#SnippetCreateRootFrame)]

## Доступность сети для мультимедиа-приложений в фоновом режиме
Все источники мультимедиа, использующие сеть и не созданные из потока или файла, сохраняют активное подключение при получении удаленного содержимого, и отключают его в противном случае. [
              В частности, **MediaStreamSource**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Core.MediaStreamSource) ждет, что приложение будет предоставлять буферизированный диапазон платформе с помощью [**SetBufferedRange**](https://msdn.microsoft.com/library/windows/apps/dn282762). После полной буферизации всего содержимого сеть больше не будет зарезервирована со стороны приложения.

Если вам необходимо выполнить сетевые вызовы, которые будут происходить в фоновом режиме, когда мультимедиа не загружается, для них необходимо создать оболочку в соответствующей задаче, такой как [**ApplicationTrigger**](https://msdn.microsoft.com/library/windows/apps/Windows.ApplicationModel.Background.ApplicationTrigger), [**MaintenanceTrigger**](https://msdn.microsoft.com/library/windows/apps/Windows.ApplicationModel.Background.MaintenanceTrigger) или [**TimeTrigger**](https://msdn.microsoft.com/library/windows/apps/Windows.ApplicationModel.Background.TimeTrigger). Дополнительные сведения см. в разделе [Поддержка приложения с помощью фоновых задач](https://msdn.microsoft.com/en-us/windows/uwp/launch-resume/support-your-app-with-background-tasks).

## Связанные статьи
* [Воспроизведение мультимедиа](media-playback.md)
* [Воспроизведение аудио и видео с помощью MediaPlayer](play-audio-and-video-with-mediaplayer.md)
* [Интеграция с системными элементами управления транспортировкой мультимедиа](integrate-with-systemmediatransportcontrols.md)
* [Пример воспроизведения звука в фоновом режиме](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/BackgroundMediaPlayback)

 

 







<!--HONumber=Aug16_HO3-->


