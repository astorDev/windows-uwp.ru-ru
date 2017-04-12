---
author: drewbatgit
ms.assetid: b7333924-d641-4ba5-92a2-65925b44ccaa
description: "В этой статье показано, как воспроизводить мультимедиа, когда приложение работает в фоновом режиме."
title: "Воспроизведение мультимедиа в фоновом режиме"
ms.author: drewbat
ms.date: 02/08/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, uwp
ms.openlocfilehash: 148bb77f9386864a1b127341aa875beb7123bae9
ms.sourcegitcommit: 909d859a0f11981a8d1beac0da35f779786a6889
translationtype: HT
---
# <a name="play-media-in-the-background"></a>Воспроизведение мультимедиа в фоновом режиме
В этой статье показано, как настроить приложение, чтобы воспроизведение мультимедиа продолжалось, когда приложение переходит в фоновый режим. Это значит, что даже после того, как пользователь свернет приложение, вернется на начальный экран или выйдет из приложения другим способом, ваше приложение продолжит воспроизводить звук. 

Сценарии воспроизведения звука в фоновом режиме включают следующие элементы.

-   **Долговременные плей-листы.** Пользователь кратковременно вызывает приложение переднего плана, чтобы выбрать и запустить плей-лист. После этого пользователь ожидает, что плей-лист продолжит воспроизведение в фоновом режиме.

-   **Использование переключателя задач:** пользователь кратковременно вызывает приложение переднего плана, чтобы запустить воспроизведение звука, а затем с помощью переключателя задач переключается на другое открытое приложение. Пользователь ожидает, что воспроизведение звука продолжится в фоновом режиме.

В этой статье описано, как повсеместно реализовать воспроизведение приложением звука в фоновом режиме на всех устройствах с Windows, включая мобильные устройства, настольные компьютеры и консоли Xbox.

> [!NOTE]
> В этой статье используется код, адаптированный из [примера воспроизведения звука в фоновом режиме на платформе UWP](http://go.microsoft.com/fwlink/p/?LinkId=800141).

## <a name="explanation-of-one-process-model"></a>Описание модели одного процесса
В Windows 10 версии 1607 представлена новая модель одного процесса, которая значительно упрощает работу с фоновым звуком. Ранее приложение должно было управлять фоновым процессом, в дополнение к приложению переднего плана, а затем вручную передавать изменения состояния между двумя процессами. В новой модели вы просто добавляете возможность фонового звука в манифест приложения, и оно автоматически будет продолжать воспроизводить звук после перехода в фоновый режим. Два новых события жизненных цикла приложения, [**EnteredBackground**](https://msdn.microsoft.com/library/windows/apps/Windows.ApplicationModel.Core.CoreApplication.EnteredBackground) и [**LeavingBackground**](https://msdn.microsoft.com/library/windows/apps/Windows.ApplicationModel.Core.CoreApplication.LeavingBackground), позволяют приложению определить, когда оно переходит в фоновый режим и выходит из него. Когда приложение переходит в фоновый режим или выходит из него, ограничения памяти, применяемые системой, могут изменяться, поэтому вы можете использовать эти события, чтобы узнать текущий объем используемой памяти и освободить ресурсы, чтобы не нарушать ограничение.

За счет устранения сложных механизмов взаимодействия между процессами и управления состоянием новая модель позволяет гораздо быстрее реализовать фоновое воспроизведение звука, значительно сократив объем кода. Однако модель с двумя процессами по-прежнему поддерживается в текущем выпуске для обеспечения обратной совместимости. Подробнее: [Старая модель воспроизведения звука в фоновом режиме](legacy-background-media-playback.md).

## <a name="requirements-for-background-audio"></a>Требования для фонового воспроизведения звука
Приложение должно соответствовать следующим требованиям для воспроизведения звука в фоновом режиме.

* Добавьте возможность **Воспроизведение мультимедиа в фоновом режиме** в манифест приложения, как описано далее в этой статье.
* Если приложение отключает автоматическую интеграцию **MediaPlayer** с системными элементами управления транспортировкой мультимедиа (SMTC), например если свойству [**CommandManager.IsEnabled**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Playback.MediaPlaybackCommandManager.IsEnabled) присвоено значение false, то необходимо вручную реализовать интеграцию с SMTC для использования функции воспроизведения мультимедиа в фоновом режиме. Кроме того, следует вручную включить интеграцию с SMTC, если вы используете API-интерфейс, отличный от **MediaPlayer**, такой как [**AudioGraph**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Audio.AudioGraph), чтобы продолжать воспроизводить звук после перехода приложения в фоновый режим. Минимальные требования к интеграции SMTC описаны в разделе "Использование системных элементов управления транспортом мультимедиа для воспроизведения звука в фоновом режиме" статьи [Ручное управление системными элементами управления воспроизведением мультимедиа](system-media-transport-controls.md).
* Когда приложение работает в фоновом режиме, вам нужно следить за тем, чтобы не превышать ограничения использования памяти, заданные системой для фоновых приложений. Рекомендации по управлению памятью в фоновом режиме представлены далее в этой статье.

## <a name="background-media-playback-manifest-capability"></a>Возможность воспроизведения мультимедиа в фоновом режиме в манифесте
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

##<a name="handle-transitioning-between-foreground-and-background"></a>Обработка перехода между передним планом и фоновым режимом
Когда приложение переходит с переднего плана в фоновый режим, создается событие [**EnteredBackground**](https://msdn.microsoft.com/library/windows/apps/Windows.ApplicationModel.Core.CoreApplication.EnteredBackground). Когда приложение возвращается на передний план, вызывается событие [**LeavingBackground**](https://msdn.microsoft.com/library/windows/apps/Windows.ApplicationModel.Core.CoreApplication.LeavingBackground). Поскольку это события жизненного цикла приложения, вам следует зарегистрировать обработчики этих событий при создании приложения. Для этого в шаблоне проекта по умолчанию нужно добавить его в конструктор класса **App** в файле App.xaml.cs. 

[!code-cs[RegisterEvents](./code/BackgroundAudio_RS1/cs/App.xaml.cs#SnippetRegisterEvents)]

Создайте переменную для отслеживания состояния приложения (передний план или фоновый режим).

[!code-cs[DeclareBackgroundMode](./code/BackgroundAudio_RS1/cs/App.xaml.cs#SnippetDeclareBackgroundMode)]

Когда вызывается событие [**EnteredBackground**](https://msdn.microsoft.com/library/windows/apps/Windows.ApplicationModel.Core.CoreApplication.EnteredBackground), задайте переменной отслеживания значение, чтобы указать, что сейчас приложение работает в фоновом режиме. В событии **EnteredBackground** не следует выполнять длительные задачи, так как это может привести к замедлению перехода приложения в фоновый режиме.

[!code-cs[EnteredBackground](./code/BackgroundAudio_RS1/cs/App.xaml.cs#SnippetEnteredBackground)]

В обработчике событий [**LeavingBackground**](https://msdn.microsoft.com/library/windows/apps/Windows.ApplicationModel.Core.CoreApplication.LeavingBackground) следует задать переменную отслеживания, чтобы указать, что приложение больше не работает в фоновом режиме.

[!code-cs[LeavingBackground](./code/BackgroundAudio_RS1/cs/App.xaml.cs#SnippetLeavingBackground)]

### <a name="memory-management-requirements"></a>Требования к управлению памятью
Самая важная часть обработки перехода между фоном и передним планом — управление памятью, которую использует ваше приложение. Так как в фоновом режиме объем ресурсов памяти, доступных приложению, уменьшается, вы также должна зарегистрироваться для прослушивания событий [**AppMemoryUsageIncreased**](https://msdn.microsoft.com/library/windows/apps/Windows.System.MemoryManager.AppMemoryUsageIncreased) и [**AppMemoryUsageLimitChanging**](https://msdn.microsoft.com/library/windows/apps/Windows.System.MemoryManager.AppMemoryUsageLimitChanging). Когда эти события возникают, следует сравнить текущий объем памяти, занимаемой приложением, с текущим ограничением и при необходимости уменьшить его. Сведения об уменьшении объема используемой памяти в фоновом режиме см. в разделе [Освобождение памяти при переходе приложения в фоновый режим](../launch-resume/reduce-memory-usage.md).

## <a name="network-availability-for-background-media-apps"></a>Доступность сети для мультимедиа-приложений в фоновом режиме
Все источники мультимедиа, использующие сеть и не созданные из потока или файла, сохраняют активное подключение при получении удаленного содержимого, и отключают его в противном случае. В частности, [**MediaStreamSource**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Core.MediaStreamSource) ждет, что приложение будет предоставлять буферизированный диапазон платформе с помощью [**SetBufferedRange**](https://msdn.microsoft.com/library/windows/apps/dn282762). После полной буферизации всего содержимого сеть больше не будет зарезервирована со стороны приложения.

Если вам необходимо выполнить сетевые вызовы, которые будут происходить в фоновом режиме, когда мультимедиа не загружается, для них необходимо создать оболочку в соответствующей задаче, такой как [**ApplicationTrigger**](https://msdn.microsoft.com/library/windows/apps/Windows.ApplicationModel.Background.ApplicationTrigger), [**MaintenanceTrigger**](https://msdn.microsoft.com/library/windows/apps/Windows.ApplicationModel.Background.MaintenanceTrigger) или [**TimeTrigger**](https://msdn.microsoft.com/library/windows/apps/Windows.ApplicationModel.Background.TimeTrigger). Дополнительные сведения см. в разделе [Поддержка приложения с помощью фоновых задач](https://msdn.microsoft.com/windows/uwp/launch-resume/support-your-app-with-background-tasks).

## <a name="related-topics"></a>Связанные статьи
* [Воспроизведение мультимедиа](media-playback.md)
* [Воспроизведение аудио и видео с помощью MediaPlayer](play-audio-and-video-with-mediaplayer.md)
* [Интеграция с системными элементами управления транспортировкой мультимедиа](integrate-with-systemmediatransportcontrols.md)
* [Пример воспроизведения звука в фоновом режиме](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/BackgroundMediaPlayback)

 

 




