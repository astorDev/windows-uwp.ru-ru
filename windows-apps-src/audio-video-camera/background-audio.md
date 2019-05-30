---
ms.assetid: b7333924-d641-4ba5-92a2-65925b44ccaa
description: В этой статье показано, как воспроизводить мультимедиа, когда приложение работает в фоновом режиме.
title: Воспроизведение мультимедиа в фоновом режиме
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 19b3aa80bee643087a0aa92f714349004f6ec1c1
ms.sourcegitcommit: ac7f3422f8d83618f9b6b5615a37f8e5c115b3c4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/29/2019
ms.locfileid: "66359162"
---
# <a name="play-media-in-the-background"></a>Воспроизведение мультимедиа в фоновом режиме
В этой статье показано, как настроить приложение, чтобы воспроизведение мультимедиа продолжалось, когда приложение переходит в фоновый режим. Это значит, что даже после того, как пользователь свернет приложение, вернется на начальный экран или выйдет из приложения другим способом, ваше приложение продолжит воспроизводить звук. 

Сценарии воспроизведения звука в фоновом режиме включают следующие элементы.

-   **Длительные списки воспроизведения:** Пользователь, кратко, появится в приложении переднего плана для выбора и запуска воспроизведения, после чего он ожидает, что список воспроизведения, чтобы продолжить воспроизведение в фоновом режиме.

-   **Используя диспетчер задач:** Пользователь кратко открывает окно приложения переднего плана, чтобы начать воспроизведение звука, а затем переключается на другой открыть приложение, используя диспетчер задач. Пользователь ожидает, что воспроизведение звука продолжится в фоновом режиме.

В этой статье описано, как повсеместно реализовать воспроизведение приложением звука в фоновом режиме на всех устройствах с Windows, включая мобильные устройства, настольные компьютеры и консоли Xbox.

> [!NOTE]
> В этой статье используется код, адаптированный из [примера воспроизведения звука в фоновом режиме на платформе UWP](https://go.microsoft.com/fwlink/p/?LinkId=800141).

## <a name="explanation-of-one-process-model"></a>Описание модели одного процесса
В Windows 10 версии 1607 представлена новая модель одного процесса, которая значительно упрощает работу с фоновым звуком. Ранее приложение должно было управлять фоновым процессом, в дополнение к приложению переднего плана, а затем вручную передавать изменения состояния между двумя процессами. В новой модели вы просто добавляете возможность фонового звука в манифест приложения, и оно автоматически будет продолжать воспроизводить звук после перехода в фоновый режим. Два новых события жизненных цикла приложения, [**EnteredBackground**](https://docs.microsoft.com/uwp/api/windows.applicationmodel.core.coreapplication.enteredbackground) и [**LeavingBackground**](https://docs.microsoft.com/uwp/api/windows.applicationmodel.core.coreapplication.leavingbackground), позволяют приложению определить, когда оно переходит в фоновый режим и выходит из него. Когда приложение переходит в фоновый режим или выходит из него, ограничения памяти, применяемые системой, могут изменяться, поэтому вы можете использовать эти события, чтобы узнать текущий объем используемой памяти и освободить ресурсы, чтобы не нарушать ограничение.

За счет устранения сложных механизмов взаимодействия между процессами и управления состоянием новая модель позволяет гораздо быстрее реализовать фоновое воспроизведение звука, значительно сократив объем кода. Однако модель с двумя процессами по-прежнему поддерживается в текущем выпуске для обеспечения обратной совместимости. Подробнее: [Старая модель воспроизведения звука в фоновом режиме](legacy-background-media-playback.md).

## <a name="requirements-for-background-audio"></a>Требования для фонового воспроизведения звука
Приложение должно соответствовать следующим требованиям для воспроизведения звука в фоновом режиме.

* Добавьте возможность **Воспроизведение мультимедиа в фоновом режиме** в манифест приложения, как описано далее в этой статье.
* Если приложение отключает автоматическую интеграцию **MediaPlayer** с системными элементами управления транспортировкой мультимедиа (SMTC), например если свойству [**CommandManager.IsEnabled**](https://docs.microsoft.com/uwp/api/windows.media.playback.mediaplaybackcommandmanager.isenabled) присвоено значение false, то необходимо вручную реализовать интеграцию с SMTC для использования функции воспроизведения мультимедиа в фоновом режиме. Кроме того, следует вручную включить интеграцию с SMTC, если вы используете API-интерфейс, отличный от **MediaPlayer**, такой как [**AudioGraph**](https://docs.microsoft.com/uwp/api/Windows.Media.Audio.AudioGraph), чтобы продолжать воспроизводить звук после перехода приложения в фоновый режим. Минимальные требования к интеграции SMTC описаны в разделе "Использование системных элементов управления транспортом мультимедиа для воспроизведения звука в фоновом режиме" статьи [Ручное управление системными элементами управления воспроизведением мультимедиа](system-media-transport-controls.md).
* Когда приложение работает в фоновом режиме, вам нужно следить за тем, чтобы не превышать ограничения использования памяти, заданные системой для фоновых приложений. Рекомендации по управлению памятью в фоновом режиме представлены далее в этой статье.

## <a name="background-media-playback-manifest-capability"></a>Возможность воспроизведения мультимедиа в фоновом режиме в манифесте
Чтобы включить фоновое воспроизведение звука, следует добавить возможность воспроизведения мультимедиа в фоновом режиме в файл манифеста приложения, Package.appxmanifest. 

**Для добавления возможностей в манифесте приложения с помощью конструктора манифестов**

1.  В Microsoft Visual Studio откройте конструктор манифеста приложения, дважды щелкнув элемент **package.appxmanifest**в **Обозревателе решений**.
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

## <a name="handle-transitioning-between-foreground-and-background"></a>Обработка перехода между передним планом и фоновым режимом
Когда приложение переходит с переднего плана в фоновый режим, создается событие [**EnteredBackground**](https://docs.microsoft.com/uwp/api/windows.applicationmodel.core.coreapplication.enteredbackground). Когда приложение возвращается на передний план, вызывается событие [**LeavingBackground**](https://docs.microsoft.com/uwp/api/windows.applicationmodel.core.coreapplication.leavingbackground). Поскольку это события жизненного цикла приложения, вам следует зарегистрировать обработчики этих событий при создании приложения. Для этого в шаблоне проекта по умолчанию нужно добавить его в конструктор класса **App** в файле App.xaml.cs. 

[!code-cs[RegisterEvents](./code/BackgroundAudio_RS1/cs/App.xaml.cs#SnippetRegisterEvents)]

Создайте переменную для отслеживания состояния приложения (передний план или фоновый режим).

[!code-cs[DeclareBackgroundMode](./code/BackgroundAudio_RS1/cs/App.xaml.cs#SnippetDeclareBackgroundMode)]

Когда вызывается событие [**EnteredBackground**](https://docs.microsoft.com/uwp/api/windows.applicationmodel.core.coreapplication.enteredbackground), задайте переменной отслеживания значение, чтобы указать, что сейчас приложение работает в фоновом режиме. В событии **EnteredBackground** не следует выполнять длительные задачи, так как это может привести к замедлению перехода приложения в фоновый режиме.

[!code-cs[EnteredBackground](./code/BackgroundAudio_RS1/cs/App.xaml.cs#SnippetEnteredBackground)]

В обработчике событий [**LeavingBackground**](https://docs.microsoft.com/uwp/api/windows.applicationmodel.core.coreapplication.leavingbackground) следует задать переменную отслеживания, чтобы указать, что приложение больше не работает в фоновом режиме.

[!code-cs[LeavingBackground](./code/BackgroundAudio_RS1/cs/App.xaml.cs#SnippetLeavingBackground)]

### <a name="memory-management-requirements"></a>Требования к управлению памятью
Самая важная часть обработки перехода между фоном и передним планом — управление памятью, которую использует ваше приложение. Так как в фоновом режиме объем ресурсов памяти, доступных приложению, уменьшается, вы также должна зарегистрироваться для прослушивания событий [**AppMemoryUsageIncreased**](https://docs.microsoft.com/uwp/api/windows.system.memorymanager.appmemoryusageincreased) и [**AppMemoryUsageLimitChanging**](https://docs.microsoft.com/uwp/api/windows.system.memorymanager.appmemoryusagelimitchanging). Когда эти события возникают, следует сравнить текущий объем памяти, занимаемой приложением, с текущим ограничением и при необходимости уменьшить его. Сведения об уменьшении объема используемой памяти в фоновом режиме см. в разделе [Освобождение памяти при переходе приложения в фоновый режим](../launch-resume/reduce-memory-usage.md).

## <a name="network-availability-for-background-media-apps"></a>Доступность сети для мультимедиа-приложений в фоновом режиме
Все источники мультимедиа, использующие сеть и не созданные из потока или файла, сохраняют активное подключение при получении удаленного содержимого, и отключают его в противном случае. [**MediaStreamSource**](https://docs.microsoft.com/uwp/api/Windows.Media.Core.MediaStreamSource), в частности, зависит от приложения, чтобы должным образом сообщают о правильном диапазоне буферизованной на платформы с помощью [ **SetBufferedRange**](https://docs.microsoft.com/uwp/api/windows.media.core.mediastreamsource.setbufferedrange). После полной буферизации всего содержимого сеть больше не будет зарезервирована со стороны приложения.

Если вам необходимо выполнить сетевые вызовы, которые будут происходить в фоновом режиме, когда мультимедиа не загружается, для них необходимо создать оболочку в соответствующей задаче, такой как [**MaintenanceTrigger**](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Background.MaintenanceTrigger) или [**TimeTrigger**](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Background.TimeTrigger). Дополнительные сведения см. в разделе [Поддержка приложения с помощью фоновых задач](https://docs.microsoft.com/windows/uwp/launch-resume/support-your-app-with-background-tasks).

## <a name="related-topics"></a>См. также
* [Воспроизведение мультимедиа](media-playback.md)
* [Воспроизведение аудио и видео с MediaPlayer](play-audio-and-video-with-mediaplayer.md)
* [Интеграция с носителя системы элементы управления транспортировкой](integrate-with-systemmediatransportcontrols.md)
* [Фон аудио образца](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/BackgroundMediaPlayback)

 

 




