---
ms.assetid: eb690f2b-3bf8-4a65-99a4-2a3a8c7760b7
description: В этой статье показано, как взаимодействовать с системными элементами управления транспортировкой мультимедиа.
title: Интеграция с системными элементами управления транспортировкой мультимедиа
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: c89a1901d15d00c7c102157c8f44d6ab96272ef0
ms.sourcegitcommit: a3dc929858415b933943bba5aa7487ffa721899f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/07/2018
ms.locfileid: "8805329"
---
# <a name="integrate-with-the-system-media-transport-controls"></a>Интеграция с системными элементами управления транспортировкой мультимедиа

В этой статье показано, как взаимодействовать с системными элементами управления транспортировкой мультимедиа (SMTC). SMTC— это набор элементов управления, общих для всех устройств с Windows 10 и предоставляющих пользователям единый способ управления воспроизведением мультимедиа во всех выполняемых приложениях, использующих [**MediaPlayer**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Playback.MediaPlayer) для воспроизведения.

Полный пример интеграции с SMTC см. в [примере системных элементов управления транспортировкой мультимедиа на GitHub](https://github.com/Microsoft/Windows-universal-samples/tree/dev/Samples/SystemMediaTransportControls).
                    
## <a name="automatic-integration-with-smtc"></a>Автоматическая интеграция с SMTC
Начиная с Windows 10 версии 1607 приложения UWP, использующие класс [**MediaPlayer**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Playback.MediaPlayer) для воспроизведения мультимедиа, автоматически интегрируются с SMTC по умолчанию. Просто создайте новый экземпляр **MediaPlayer** и назначьте класс [**MediaSource**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Core.MediaSource), [**MediaPlaybackItem**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Playback.MediaPlaybackItem) или [**MediaPlaybackList**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Playback.MediaPlaybackList) свойству [**Source**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Playback.MediaPlayer.Source) проигрывателя, после чего пользователь увидит имя вашего приложения в SMTC и сможет начинать и приостанавливать воспроизведение, а также перемещаться по спискам воспроизведения с помощью элементов управления SMTC. 

Ваше приложение может создавать и использовать несколько объектов **MediaPlayer** одновременно. В SMTC создается отдельная вкладка для каждого активного экземпляра **MediaPlayer** в приложении, поэтому пользователь может переключаться между активными проигрывателями мультимедиа вашего и других выполняемых приложений. Действия элементов управления применяются к проигрывателю мультимедиа, выбранному в SMTC.

Дополнительные сведения об использовании **MediaPlayer** в приложении, включая его привязку к классу [**MediaPlayerElement**](https://msdn.microsoft.com/library/windows/apps/Windows.UI.Xaml.Controls.MediaPlayerElement) на странице XAML, см. в разделе [Воспроизведение аудио и видео с помощью MediaPlayer](play-audio-and-video-with-mediaplayer.md). 

Дополнительные сведения об использовании классов **MediaSource**, **MediaPlaybackItem** и **MediaPlaybackList** см. в разделе [Элементы, списки воспроизведения и звуковые дорожки мультимедиа](media-playback-with-mediasource.md).

## <a name="add-metadata-to-be-displayed-by-the-smtc"></a>Добавление метаданных для отображения в SMTC
Если вам требуется добавить или изменить метаданные, отображаемые для элементов мультимедиа в SMTC, например видео или название песни, обновите свойства отображения класса **MediaPlaybackItem**, представляющего элемент мультимедиа. В первую очередь, получите ссылку на объект [**MediaItemDisplayProperties**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Playback.MediaItemDisplayProperties), вызвав метод [**GetDisplayProperties**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Playback.MediaPlaybackItem.GetDisplayProperties). Затем установите тип мультимедиа (музыка или видео) для элемента с помощью свойства [**Type**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Playback.MediaItemDisplayProperties.Type). После этого вы можете заполнить поля свойств [**MusicProperties**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Playback.MediaItemDisplayProperties.MusicProperties) или [**VideoProperties**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Playback.MediaItemDisplayProperties.VideoProperties) в зависимости от указанного типа мультимедиа. Наконец, обновите метаданные для элемента мультимедиа, вызвав метод [**ApplyDisplayProperties**](https://msdn.microsoft.com/library/windows/apps/mt489923).

[!code-cs[SetVideoProperties](./code/MediaSource_RS1/cs/MainPage.xaml.cs#SnippetSetVideoProperties)]

[!code-cs[SetMusicProperties](./code/MediaSource_RS1/cs/MainPage.xaml.cs#SnippetSetMusicProperties)]

## <a name="use-commandmanager-to-modify-or-override-the-default-smtc-commands"></a>Использование CommandManager для изменения или переопределения команд по умолчанию SMTC
Ваше приложение может изменять или полностью переопределять поведение элементов управления SMTC с помощью класса [**MediaPlaybackCommandManager**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Playback.MediaPlaybackCommandManager). Для каждого экземпляра класса **MediaPlayer** можно получить экземпляр диспетчера команд путем доступа к свойству [**CommandManager**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Playback.MediaPlayer.CommandManager).

Для каждой команды, например команды *Next*, которая по умолчанию переходит к следующему элементу в **MediaPlaybackList**, диспетчер команд предоставляет полученное событие, например [**NextReceived**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Playback.MediaPlaybackCommandManager.NextReceived), и объект, управляющий поведением команды, например [**NextBehavior**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Playback.MediaPlaybackCommandManager.NextBehavior). 

В следующем примере регистрируется обработчик для событий **NextReceived** и [**IsEnabledChanged**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Playback.MediaPlaybackCommandManagerCommandBehavior.IsEnabledChanged) объекта **NextBehavior**.

[!code-cs[AddNextHandler](./code/SMTC_RS1/cs/MainPage.xaml.cs#SnippetAddNextHandler)]

В следующем примере демонстрируется сценарий, в котором приложению требуется отключить команду *Next*, после того как пользователь пролистал пять элементов в списке воспроизведения. При этом перед продолжением воспроизведения содержимого может потребоваться вмешательство пользователя. При каждом вызове события **NextReceived** значение счетчика увеличивается. Как только счетчик достигнет целевого значения, свойству [**EnablingRule**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Playback.MediaPlaybackCommandManagerCommandBehavior.EnablingRule) команды *Next* устанавливается значение [**Never**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Playback.MediaCommandEnablingRule), отключающее команду. 

[!code-cs[NextReceived](./code/SMTC_RS1/cs/MainPage.xaml.cs#SnippetNextReceived)]

Вы также можете установить команде значение **Always**, то есть она всегда будет включена, даже если в списке воспроизведения больше нет элементов (в примере с командой *Next*). Либо вы можете установить команде значение **Auto**, при котором система определяет необходимость включения команды в зависимости от воспроизводимого содержимого.

В описанном выше сценарии приложению в определенный момент потребуется повторно включить команду *Next*. Для этого оно установит свойству **EnablingRule** значение **Auto**.

[!code-cs[EnableNextButton](./code/SMTC_RS1/cs/MainPage.xaml.cs#SnippetEnableNextButton)]

Так как в вашем приложении может быть собственный пользовательский интерфейс для управления воспроизведением на переднем плане, вы можете использовать события [**IsEnabledChanged**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Playback.MediaPlaybackCommandManagerCommandBehavior.IsEnabledChanged) для обновления собственного пользовательского интерфейса в соответствии с SMTC по мере включения и отключения команд путем доступа к свойству [**IsEnabled**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Playback.MediaPlaybackCommandManagerCommandBehavior.IsEnabled) класса [**MediaPlaybackCommandManagerCommandBehavior**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Playback.MediaPlaybackCommandManagerCommandBehavior), переданного в обработчик.

[!code-cs[IsEnabledChanged](./code/SMTC_RS1/cs/MainPage.xaml.cs#SnippetIsEnabledChanged)]

В некоторых случаях вам может потребоваться полностью переопределить поведение команды SMTC. В следующем примере демонстрируется сценарий, в котором приложение использует команды *Next* и *Previous* для переключения между интернет-радиостанциями вместо перехода между музыкальными композициями в текущем списке воспроизведения. Как и в предыдущем примере, при получении команды регистрируется обработчик. В данном случае это событие [**PreviousReceived**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Playback.MediaPlaybackCommandManager.PreviousReceived).

[!code-cs[AddPreviousHandler](./code/SMTC_RS1/cs/MainPage.xaml.cs#SnippetAddPreviousHandler)]

В обработчике **PreviousReceived** сначала получается класс [**Deferral**](https://msdn.microsoft.com/library/windows/apps/Windows.Foundation.Deferral) путем вызова метода [**GetDeferral**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Playback.MediaPlaybackCommandManagerPreviousReceivedEventArgs.GetDeferral) класса [**MediaPlaybackCommandManagerPreviousReceivedEventArgs**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Playback.MediaPlaybackCommandManagerPreviousReceivedEventArgs), переданного в обработчик. Таким образом системе сообщается о необходимости дождаться завершения отсрочки перед выполнением команды. Это крайне важно, если вы собираетесь совершать асинхронные вызовы в обработчике. На этом этапе в примере выполняется вызов пользовательского метода, возвращающего объект **MediaPlaybackItem**, который представляет предыдущую радиостанцию.

Затем проверяется свойство [**Handled**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Playback.MediaPlaybackCommandManagerPreviousReceivedEventArgs.Handled), чтобы убедиться, что событие еще не было обработано другим обработчиком. В противном случае свойству **Handled** устанавливается истинное значение. Так SMTC и любые другие пописанные обработчики узнают, что им не следует предпринимать действия для выполнения этой команды, так как она уже обработана. Затем код устанавливает новый источник для проигрывателя мультимедиа и запускает проигрыватель.

Наконец, для объекта отсрочки вызывается метод [**Complete**](https://msdn.microsoft.com/library/windows/apps/Windows.Foundation.Deferral.Complete), чтобы дать системе понять, что вы завершили обработку команды.

[!code-cs[PreviousReceived](./code/SMTC_RS1/cs/MainPage.xaml.cs#SnippetPreviousReceived)]
                
## <a name="manual-control-of-the-smtc"></a>Ручное управление SMTC
Как уже упоминалось в этой статье, SMTC автоматически обнаруживает и отображает сведения для каждого экземпляра **MediaPlayer**, созданного приложением. Если вам требуется использовать несколько экземпляров **MediaPlayer**, но чтобы SMTC при этом предоставлял одну запись для приложения, необходимо вручную управлять поведением SMTC, не полагаясь на автоматическую интеграцию. Кроме того, если вы используете класс [**MediaTimelineController**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.MediaTimelineController) для управления одним или несколькими проигрывателями мультимедиа, вам потребуется использовать ручную интеграцию SMTC. Помимо этого, если для воспроизведения мультимедиа в вашем приложении используется API, отличный от **MediaPlayer**, например класс [**AudioGraph**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Audio.AudioGraph), вам потребуется реализовать ручную интеграцию SMTC, чтобы пользователь мог управлять вашим приложением с помощью SMTC. Сведения о ручном управлении SMTC см. в разделе [Ручное управление системными элементами управления транспортировкой мультимедиа](system-media-transport-controls.md).



## <a name="related-topics"></a>Связанные статьи
* [Воспроизведение мультимедиа](media-playback.md)
* [Воспроизведение аудио и видео с помощью MediaPlayer](play-audio-and-video-with-mediaplayer.md)
* [Ручное управление системными элементами управления транспортировкой мультимедиа](system-media-transport-controls.md)
* [Пример системных элементов управления транспортировкой мультимедиа на GitHub](https://github.com/Microsoft/Windows-universal-samples/tree/dev/Samples/SystemMediaTransportControls)
 

 




