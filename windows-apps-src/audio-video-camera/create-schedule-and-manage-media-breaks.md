---
ms.assetid: 0309c7a1-8e4c-4326-813a-cbd9f8b8300d
description: В этой статье показано, как создавать и планировать мультимедийные вставки, а также управлять ими в приложении воспроизведения мультимедиа.
title: Создание, планирование и управление перерывами при воспроизведении мультимедиа
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 23cb51ec6b2c6c2560c0a1f6d583ba916768254c
ms.sourcegitcommit: 6f32604876ed480e8238c86101366a8d106c7d4e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/21/2019
ms.locfileid: "67318564"
---
# <a name="create-schedule-and-manage-media-breaks"></a>Создание, планирование и управление перерывами при воспроизведении мультимедиа

В этой статье показано, как создавать и планировать мультимедийные вставки, а также управлять ими в приложении воспроизведения мультимедиа. Мультимедийные вставки обычно используются для вставки аудио- или видеорекламы в мультимедийное содержимое. Начиная с Windows 10 версии 1607 можно использовать класс [**MediaBreakManager**](https://docs.microsoft.com/uwp/api/Windows.Media.Playback.MediaBreakManager), чтобы легко и быстро добавлять мультимедийные вставки в любой элемент [**MediaPlaybackItem**](https://docs.microsoft.com/uwp/api/Windows.Media.Playback.MediaPlaybackItem), воспроизводимый с помощью [**MediaPlayer**](https://docs.microsoft.com/uwp/api/Windows.Media.Playback.MediaPlayer).


Когда запланирована одна или несколько мультимедийных вставок, система автоматически воспроизводит ваше мультимедийное содержимое в указанный момент во время воспроизведения. **MediaBreakManager** предоставляет события, чтобы приложение могло реагировать на начало и завершение мультимедийных вставок при воспроизведении мультимедиа, а также на случаи, когда вставки пропускаются пользователем. Можно также получить доступ к объекту [**MediaPlaybackSession**](https://docs.microsoft.com/uwp/api/Windows.Media.Playback.MediaPlaybackSession) для ваших мультимедийных вставок, чтобы контролировать такие события, как обновление хода выполнения загрузки и буферизации.

## <a name="schedule-media-breaks"></a>Планирование мультимедийных вставок
Каждый объект **MediaPlaybackItem** имеет собственный объект [**MediaBreakSchedule**](https://docs.microsoft.com/uwp/api/Windows.Media.Playback.MediaBreakSchedule), используемый для настройки мультимедийных вставок, которые будут воспроизводиться при воспроизведении элемента. В первую очередь для использования мультимедийных вставок в своем приложении следует создать объект [**MediaPlaybackItem**](https://docs.microsoft.com/uwp/api/Windows.Media.Playback.MediaPlaybackItem) для основного содержимого воспроизведения. 

[!code-cs[MoviePlaybackItem](./code/MediaBreaks_RS1/cs/MainPage.xaml.cs#SnippetMoviePlaybackItem)]

Дополнительные сведения о работе с **MediaPlaybackItem**, [**MediaPlaybackList**](https://docs.microsoft.com/uwp/api/Windows.Media.Playback.MediaPlaybackList) и другими основными API воспроизведения мультимедиа см. в разделе [Элементы, списки воспроизведения и звуковые дорожки мультимедиа](media-playback-with-mediasource.md).

В следующем примере показано, как добавить вставку перед началом воспроизведения в объект **MediaPlaybackItem**; это означает, что система будет воспроизводить мультимедийную вставку перед воспроизведением элемента воспроизведения, к которому относится вставка. Сначала создается новый экземпляр объекта [**MediaBreak**](https://docs.microsoft.com/uwp/api/Windows.Media.Playback.MediaBreak). В этом примере конструктор вызывается с параметром [**MediaBreakInsertionMethod.Interrupt**](https://docs.microsoft.com/uwp/api/Windows.Media.Playback.MediaBreakInsertionMethod); это означает, что во время воспроизведения мультимедийной вставки воспроизведение основного содержимого приостанавливается. 

Далее создается новый элемент **MediaPlaybackItem** для содержимого, которое будет воспроизводиться во время вставки, например рекламы. Для свойства [**CanSkip**](https://docs.microsoft.com/uwp/api/windows.media.playback.mediaplaybackitem.canskip) этого элемента воспроизведения задается значение false. Это означает, что пользователь не сможет пропустить этот элемент с помощью встроенных элементов управления мультимедиа. Однако приложение все равно может пропустить мультимедийную вставку программным образом, вызвав метод [**SkipCurrentBreak**](https://docs.microsoft.com/uwp/api/windows.media.playback.mediabreakmanager.skipcurrentbreak). 

Свойство [**PlaybackList**](https://docs.microsoft.com/uwp/api/windows.media.playback.mediabreak.playbacklist) мультимедийной вставки представляет собой [**MediaPlaybackList**](https://docs.microsoft.com/uwp/api/Windows.Media.Playback.MediaPlaybackList), который позволяет воспроизводить несколько элементов мультимедиа в виде списка воспроизведения. Добавьте один или несколько объектов **MediaPlaybackItem** из коллекции **Items** списка, чтобы включить их в список воспроизведения мультимедийной вставки.

Наконец, запланируйте мультимедийную вставку с помощью свойства [**BreakSchedule**](https://docs.microsoft.com/uwp/api/windows.media.playback.mediaplaybackitem.breakschedule) элемента воспроизведения основного содержимого. Укажите, что вставка должна воспроизводиться перед началом элемента, назначив ее свойству [**PrerollBreak**](https://docs.microsoft.com/uwp/api/windows.media.playback.mediabreakschedule.prerollbreak) объекта расписания.

[!code-cs[PreRollBreak](./code/MediaBreaks_RS1/cs/MainPage.xaml.cs#SnippetPreRollBreak)]

Теперь можно воспроизвести основной элемент мультимедиа, и созданная мультимедийная вставка будет воспроизводиться перед основным содержимым. Создайте новый объект [**MediaPlayer**](https://docs.microsoft.com/uwp/api/Windows.Media.Playback.MediaPlayer) и, если требуется, задайте для свойства [**AutoPlay**](https://docs.microsoft.com/uwp/api/windows.media.playback.mediaplayer.autoplay) значение true, чтобы запускать воспроизведение автоматически. Задайте для свойства [**Source**](https://docs.microsoft.com/uwp/api/windows.media.playback.mediaplayer.source) объекта **MediaPlayer** элемент воспроизведения основного содержимого. Это необязательно, но вы можете назначить объект **MediaPlayer** объекту [**MediaPlayerElement**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.MediaPlayerElement) для отображения мультимедиа на странице XAML. Дополнительные сведения об использовании объекта **MediaPlayer** см. в разделе [Воспроизведение аудио и видео с помощью MediaPlayer](play-audio-and-video-with-mediaplayer.md).

[!code-cs[Play](./code/MediaBreaks_RS1/cs/MainPage.xaml.cs#SnippetPlay)]

Вставка после завершения воспроизведения, которая воспроизводится после завершения воспроизведения элемента **MediaPlaybackItem**, содержащего основное содержимое, добавляется таким же образом, как и вставка перед началом воспроизведения, за исключением того, что объект **MediaBreak** назначается свойству [**PostrollBreak**](https://docs.microsoft.com/uwp/api/windows.media.playback.mediabreakschedule.postrollbreak).

[!code-cs[PostRollBreak](./code/MediaBreaks_RS1/cs/MainPage.xaml.cs#SnippetPostRollBreak)]

Можно также запланировать одну или несколько вставок во время воспроизведения, которые воспроизводятся в указанный момент во время воспроизведения основного содержимого. В следующем примере объект [**MediaBreak**](https://docs.microsoft.com/uwp/api/Windows.Media.Playback.MediaBreak) создается с помощью перегруженного конструктора, принимающего объект **TimeSpan**, который указывает время в течение воспроизведения основного элемента мультимедиа, когда будет воспроизводиться вставка. Здесь снова [**MediaBreakInsertionMethod.Interrupt**](https://docs.microsoft.com/uwp/api/Windows.Media.Playback.MediaBreakInsertionMethod) указывает, что во время воспроизведения вставки воспроизведение основного содержимого будет приостановлено. Вставка во время воспроизведения добавляется в расписание вызовом метода [**InsertMidrollBreak**](https://docs.microsoft.com/uwp/api/windows.media.playback.mediabreakschedule.insertmidrollbreak). Доступный только для чтения текущий список вставок во время воспроизведения можно получить, обратившись к свойству [**MidrollBreaks**](https://docs.microsoft.com/uwp/api/windows.media.playback.mediabreakschedule.midrollbreaks).

[!code-cs[MidrollBreak](./code/MediaBreaks_RS1/cs/MainPage.xaml.cs#SnippetMidrollBreak)]

В следующем примере вставки во время воспроизведения показано использование метода вставки [**MediaBreakInsertionMethod.Replace**](https://docs.microsoft.com/uwp/api/Windows.Media.Playback.MediaBreakInsertionMethod), который означает, что система будет продолжать обработку основного содержимого во время воспроизведения вставки. Этот вариант обычно используется приложениями прямой потоковой передачи мультимедиа, когда во время рекламы не требуется приостанавливать воспроизведение основного содержимого, чтобы не отставать от прямого эфира. 

В этом примере также используется перегруженный конструктор [**MediaPlaybackItem**](https://docs.microsoft.com/uwp/api/Windows.Media.Playback.MediaPlaybackItem), который принимает два параметра [**TimeSpan**](https://docs.microsoft.com/uwp/api/Windows.Foundation.TimeSpan). Первый параметр указывает начальную точку в элементе мультимедийной вставки, с которой начнется воспроизведение. Второй параметр задает длительность воспроизведения элемента мультимедийной вставки. Таким образом, в следующем примере воспроизведение **MediaBreak** начнется через 20 минут после начала основного содержимого. Воспроизведение мультимедийного элемента начнется в точке, отстоящей на 30 секунд от начала элемента мультимедийной вставки, и будет длиться 15 секунд, после чего продолжится воспроизведение основного содержимого.

[!code-cs[MidrollBreak2](./code/MediaBreaks_RS1/cs/MainPage.xaml.cs#SnippetMidrollBreak2)]

## <a name="skip-media-breaks"></a>Пропуск мультимедийных вставок
Как указано ранее в этой статье, можно задать свойство [**CanSkip**](https://docs.microsoft.com/uwp/api/windows.media.playback.mediaplaybackitem.canskip) объекта **MediaPlaybackItem**, чтобы запретить пользователям пропускать содержимое с помощью встроенных элементов управления. Однако можно в любой момент вызвать из кода метод [**SkipCurrentBreak**](https://docs.microsoft.com/uwp/api/windows.media.playback.mediabreakmanager.skipcurrentbreak), чтобы пропустить текущую вставку.

[!code-cs[SkipButtonClick](./code/MediaBreaks_RS1/cs/MainPage.xaml.cs#SnippetSkipButtonClick)]

## <a name="handle-mediabreak-events"></a>Обработка событий MediaBreak

Имеется несколько событий, связанных с мультимедийными вставками, которые можно зарегистрировать для выполнения действий на основе изменения состояния мультимедийных вставок.

[!code-cs[RegisterMediaBreakEvents](./code/MediaBreaks_RS1/cs/MainPage.xaml.cs#SnippetRegisterMediaBreakEvents)]

Событие [**BreakStarted**](https://docs.microsoft.com/uwp/api/windows.media.playback.mediabreakmanager.breakstarted) возникает, когда начинается мультимедийная вставка. Можно обновить пользовательский интерфейс, чтобы пользователь знал, что воспроизводится мультимедийная вставка. В этом примере используется объект [**MediaBreakStartedEventArgs**](https://docs.microsoft.com/uwp/api/Windows.Media.Playback.MediaBreakStartedEventArgs), передаваемый в обработчик для получения ссылки на начавшуюся мультимедийную вставку. Затем свойство [**CurrentItemIndex**](https://docs.microsoft.com/uwp/api/windows.media.playback.mediaplaybacklist.currentitemindex) используется для определения воспроизводимого элемента мультимедиа из списка воспроизведения мультимедийной вставки. Далее обновляется пользовательский интерфейс, чтобы показать пользователю индекс текущей рекламы и количество рекламных роликов, оставшихся во вставке. Помните, что обновление пользовательского интерфейса должно выполняться в потоке пользовательского интерфейса, поэтому вызов следует производить из вызова метода [**RunAsync**](https://docs.microsoft.com/uwp/api/windows.ui.core.coredispatcher.runasync). 

[!code-cs[BreakStarted](./code/MediaBreaks_RS1/cs/MainPage.xaml.cs#SnippetBreakStarted)]

[**BreakEnded** ](https://docs.microsoft.com/uwp/api/windows.media.playback.mediabreakmanager.breakended) возникает, когда все элементы мультимедиа в разрыва завершении воспроизведения или был пропущен. Обработчик этого события можно использовать для обновления пользовательского интерфейса, указывающего, что содержимое мультимедийной вставки больше не воспроизводится.

[!code-cs[BreakEnded](./code/MediaBreaks_RS1/cs/MainPage.xaml.cs#SnippetBreakEnded)]

Событие **BreakSkipped** возникает, когда пользователь нажимает кнопку *Далее* во встроенном пользовательском интерфейсе во время воспроизведения элемента, для которого свойство [**CanSkip**](https://docs.microsoft.com/uwp/api/windows.media.playback.mediaplaybackitem.canskip) имеет значение true, или при пропуске вставки из кода путем вызова метода [**SkipCurrentBreak**](https://docs.microsoft.com/uwp/api/windows.media.playback.mediabreakmanager.skipcurrentbreak).

В следующем примере свойство [**Source**](https://docs.microsoft.com/uwp/api/windows.media.playback.mediaplayer.source) объекта **MediaPlayer** используется для получения ссылки на элемент мультимедиа для основного содержимого. Пропущенная мультимедийная вставка входит в расписание вставок для этого элемента. Далее код проверяет, задано ли пропущенной мультимедийной вставке свойство расписания [**PrerollBreak**](https://docs.microsoft.com/uwp/api/windows.media.playback.mediabreakschedule.prerollbreak). Если да, то была пропущена вставка перед началом воспроизведения; в таком случае создается новая вставка, воспроизведение которой планируется через 10 минут после начала основного содержимого.

[!code-cs[BreakSkipped](./code/MediaBreaks_RS1/cs/MainPage.xaml.cs#SnippetBreakSkipped)]

[**BreaksSeekedOver** ](https://docs.microsoft.com/uwp/api/windows.media.playback.mediabreakmanager.breaksseekedover) возникает при прохождении позиции воспроизведения мультимедиа основного элемента по расписанию один или несколько разрыва мультимедиа. В следующем примере проверяется, не произошел ли переход более чем через одну мультимедийную вставку при прокрутке положения воспроизведения вперед, а также не была ли выполнена прокрутка вперед менее чем на 10 минут. Если эти условия выполнены, первая вставка, через которую был выполнен переход (она получается из коллекции [**SeekedOverBreaks**](https://docs.microsoft.com/uwp/api/windows.media.playback.mediabreakseekedovereventargs.seekedoverbreaks), предоставленной аргументами события), немедленно воспроизводится с помощью вызова метода [**PlayBreak**](https://docs.microsoft.com/uwp/api/windows.media.playback.mediabreakmanager.playbreak) объекта **MediaPlayer.BreakManager**.

[!code-cs[BreakSeekedOver](./code/MediaBreaks_RS1/cs/MainPage.xaml.cs#SnippetBreakSeekedOver)]


## <a name="access-the-current-playback-session"></a>Доступ к текущему сеансу воспроизведения
Объект [**MediaPlaybackSession**](https://docs.microsoft.com/uwp/api/Windows.Media.Playback.MediaPlaybackSession) использует класс **MediaPlayer** для предоставления данных и событий, связанных с текущим воспроизводимым мультимедийным содержимым. [  **MediaBreakManager**](https://docs.microsoft.com/uwp/api/Windows.Media.Playback.MediaBreakManager) также имеет объект **MediaPlaybackSession**, к которому можно обращаться для получения данных и событий, связанных с конкретным воспроизводимым содержимым мультимедийной вставки. Сведения, которые можно получить из сеанса воспроизведения, включают в себя текущее состояние воспроизведения (воспроизводится или приостановлено) и текущее положение воспроизведения в рамках содержимого. Свойства [**NaturalVideoWidth**](https://docs.microsoft.com/uwp/api/windows.media.playback.mediaplaybacksession.naturalvideowidth) и [**NaturalVideoHeight**](https://docs.microsoft.com/uwp/api/windows.media.playback.mediaplaybacksession.naturalvideoheight), а также [**NaturalVideoSizeChanged**](https://docs.microsoft.com/uwp/api/windows.media.playback.mediaplaybacksession.naturalvideosizechanged) можно использовать для настройки пользовательского интерфейса видео, если мультимедийная вставка и основное содержимое имеют разное соотношение сторон. Можно также получать такие события, как [**BufferingStarted**](https://docs.microsoft.com/uwp/api/windows.media.playback.mediaplaybacksession.bufferingstarted), [**BufferingEnded**](https://docs.microsoft.com/uwp/api/windows.media.playback.mediaplaybacksession.bufferingended) и [**DownloadProgressChanged**](https://docs.microsoft.com/uwp/api/windows.media.playback.mediaplaybacksession.downloadprogresschanged), которые предоставляют ценную телеметрии о производительности приложения.

В следующем примере регистрируется обработчик **события BufferingProgressChanged**; в обработчике события пользовательский интерфейс обновляется для отображения текущего хода выполнения буферизации.

[!code-cs[RegisterBufferingProgressChanged](./code/MediaBreaks_RS1/cs/MainPage.xaml.cs#SnippetRegisterBufferingProgressChanged)]

[!code-cs[BufferingProgressChanged](./code/MediaBreaks_RS1/cs/MainPage.xaml.cs#SnippetBufferingProgressChanged)]

## <a name="related-topics"></a>См. также
* [Воспроизведение мультимедиа](media-playback.md)
* [Воспроизведение аудио и видео с MediaPlayer](play-audio-and-video-with-mediaplayer.md)
* [Ручное управление мультимедиа транспорта системных элементов управления](system-media-transport-controls.md)

 

 




