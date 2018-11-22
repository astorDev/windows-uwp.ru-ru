---
author: drewbatgit
ms.assetid: 0309c7a1-8e4c-4326-813a-cbd9f8b8300d
description: В данной статье показано, как создавать и планировать мультимедийные вставки, а также управлять ими в приложении воспроизведения мультимедиа.
title: Создание, планирование мультимедийных вставок и управление ими
ms.author: drewbat
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 0feb7f6771254bf500e4b64fd0e632daad9817e4
ms.sourcegitcommit: 93c0a60cf531c7d9fe7b00e7cf78df86906f9d6e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/21/2018
ms.locfileid: "7569834"
---
# <a name="create-schedule-and-manage-media-breaks"></a>Создание, планирование и управление мультимедийными вставками

В данной статье показано, как создавать и планировать мультимедийные вставки, а также управлять ими в приложении воспроизведения мультимедиа. Мультимедийные вставки обычно используются для вставки аудио- или видеорекламы в мультимедийное содержимое. Начиная с Windows 10 версии 1607 можно использовать класс [**MediaBreakManager**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Playback.MediaBreakManager), чтобы легко и быстро добавлять мультимедийные вставки в любой элемент [**MediaPlaybackItem**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Playback.MediaPlaybackItem), воспроизводимый с помощью [**MediaPlayer**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Playback.MediaPlayer).


Когда запланирована одна или несколько мультимедийных вставок, система автоматически воспроизводит ваше мультимедийное содержимое в указанный момент во время воспроизведения. **MediaBreakManager** предоставляет события, чтобы приложение могло реагировать на начало и завершение мультимедийных вставок при воспроизведении мультимедиа, а также на случаи, когда вставки пропускаются пользователем. Можно также получить доступ к объекту [**MediaPlaybackSession**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Playback.MediaPlaybackSession) для ваших мультимедийных вставок, чтобы контролировать такие события, как обновление хода выполнения загрузки и буферизации.

## <a name="schedule-media-breaks"></a>Планирование мультимедийных вставок
Каждый объект **MediaPlaybackItem** имеет собственный объект [**MediaBreakSchedule**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Playback.MediaBreakSchedule), используемый для настройки мультимедийных вставок, которые будут воспроизводиться при воспроизведении элемента. В первую очередь для использования мультимедийных вставок в своем приложении следует создать объект [**MediaPlaybackItem**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Playback.MediaPlaybackItem) для основного содержимого воспроизведения. 

[!code-cs[MoviePlaybackItem](./code/MediaBreaks_RS1/cs/MainPage.xaml.cs#SnippetMoviePlaybackItem)]

Дополнительные сведения о работе с **MediaPlaybackItem**, [**MediaPlaybackList**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Playback.MediaPlaybackList) и другими основными API воспроизведения мультимедиа см. в разделе [Элементы, списки воспроизведения и звуковые дорожки мультимедиа](media-playback-with-mediasource.md).

В следующем примере показано, как добавить вставку перед началом воспроизведения в объект **MediaPlaybackItem**; это означает, что система будет воспроизводить мультимедийную вставку перед воспроизведением элемента воспроизведения, к которому относится вставка. Сначала создается новый экземпляр объекта [**MediaBreak**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Playback.MediaBreak). В этом примере конструктор вызывается с параметром [**MediaBreakInsertionMethod.Interrupt**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Playback.MediaBreakInsertionMethod); это означает, что во время воспроизведения мультимедийной вставки воспроизведение основного содержимого приостанавливается. 

Далее создается новый элемент **MediaPlaybackItem** для содержимого, которое будет воспроизводиться во время вставки, например рекламы. Для свойства [**CanSkip**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Playback.MediaPlaybackItem.CanSkip) этого элемента воспроизведения задается значение false. Это означает, что пользователь не сможет пропустить этот элемент с помощью встроенных элементов управления мультимедиа. Однако приложение все равно может пропустить мультимедийную вставку программным образом, вызвав метод [**SkipCurrentBreak**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Playback.MediaBreakManager.SkipCurrentBreak). 

Свойство [**PlaybackList**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Playback.MediaBreak.PlaybackList) мультимедийной вставки представляет собой [**MediaPlaybackList**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Playback.MediaPlaybackList), который позволяет воспроизводить несколько элементов мультимедиа в виде списка воспроизведения. Добавьте один или несколько объектов **MediaPlaybackItem** из коллекции **Items** списка, чтобы включить их в список воспроизведения мультимедийной вставки.

Наконец, запланируйте мультимедийную вставку с помощью свойства [**BreakSchedule**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Playback.MediaPlaybackItem.BreakSchedule) элемента воспроизведения основного содержимого. Укажите, что вставка должна воспроизводиться перед началом элемента, назначив ее свойству [**PrerollBreak**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Playback.MediaBreakSchedule.PrerollBreak) объекта расписания.

[!code-cs[PreRollBreak](./code/MediaBreaks_RS1/cs/MainPage.xaml.cs#SnippetPreRollBreak)]

Теперь можно воспроизвести основной элемент мультимедиа, и созданная мультимедийная вставка будет воспроизводиться перед основным содержимым. Создайте новый объект [**MediaPlayer**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Playback.MediaPlayer) и, если требуется, задайте для свойства [**AutoPlay**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Playback.MediaPlayer.AutoPlay) значение true, чтобы запускать воспроизведение автоматически. Задайте для свойства [**Source**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Playback.MediaPlayer.Source) объекта **MediaPlayer** элемент воспроизведения основного содержимого. Это необязательно, но вы можете назначить объект **MediaPlayer** объекту [**MediaPlayerElement**](https://msdn.microsoft.com/library/windows/apps/Windows.UI.Xaml.Controls.MediaPlayerElement) для отображения мультимедиа на странице XAML. Дополнительные сведения об использовании объекта **MediaPlayer** см. в разделе [Воспроизведение аудио и видео с помощью MediaPlayer](play-audio-and-video-with-mediaplayer.md).

[!code-cs[Play](./code/MediaBreaks_RS1/cs/MainPage.xaml.cs#SnippetPlay)]

Вставка после завершения воспроизведения, которая воспроизводится после завершения воспроизведения элемента **MediaPlaybackItem**, содержащего основное содержимое, добавляется таким же образом, как и вставка перед началом воспроизведения, за исключением того, что объект **MediaBreak** назначается свойству [ **PostrollBreak**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Playback.MediaBreakSchedule.PostrollBreak).

[!code-cs[PostRollBreak](./code/MediaBreaks_RS1/cs/MainPage.xaml.cs#SnippetPostRollBreak)]

Можно также запланировать одну или несколько вставок во время воспроизведения, которые воспроизводятся в указанный момент во время воспроизведения основного содержимого. В следующем примере объект [**MediaBreak**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Playback.MediaBreak) создается с помощью перегруженного конструктора, принимающего объект **TimeSpan**, который указывает время в течение воспроизведения основного элемента мультимедиа, когда будет воспроизводиться вставка. Здесь снова [**MediaBreakInsertionMethod.Interrupt**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Playback.MediaBreakInsertionMethod) указывает, что во время воспроизведения вставки воспроизведение основного содержимого будет приостановлено. Вставка во время воспроизведения добавляется в расписание вызовом метода [**InsertMidrollBreak**](https://msdn.microsoft.com/library/windows/apps/mt670692). Доступный только для чтения текущий список вставок во время воспроизведения можно получить, обратившись к свойству [**MidrollBreaks**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Playback.MediaBreakSchedule.MidrollBreaks).

[!code-cs[MidrollBreak](./code/MediaBreaks_RS1/cs/MainPage.xaml.cs#SnippetMidrollBreak)]

В следующем примере вставки во время воспроизведения показано использование метода вставки [**MediaBreakInsertionMethod.Replace**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Playback.MediaBreakInsertionMethod), который означает, что система будет продолжать обработку основного содержимого во время воспроизведения вставки. Этот вариант обычно используется приложениями прямой потоковой передачи мультимедиа, когда во время рекламы не требуется приостанавливать воспроизведение основного содержимого, чтобы не отставать от прямого эфира. 

В этом примере также используется перегруженный конструктор [**MediaPlaybackItem**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Playback.MediaPlaybackItem), который принимает два параметра [**TimeSpan**](https://msdn.microsoft.com/library/windows/apps/Windows.Foundation.TimeSpan). Первый параметр указывает начальную точку в элементе мультимедийной вставки, с которой начнется воспроизведение. Второй параметр задает длительность воспроизведения элемента мультимедийной вставки. Таким образом, в следующем примере воспроизведение **MediaBreak** начнется через 20 минут после начала основного содержимого. Воспроизведение мультимедийного элемента начнется в точке, отстоящей на 30 секунд от начала элемента мультимедийной вставки, и будет длиться 15 секунд, после чего продолжится воспроизведение основного содержимого.

[!code-cs[MidrollBreak2](./code/MediaBreaks_RS1/cs/MainPage.xaml.cs#SnippetMidrollBreak2)]

## <a name="skip-media-breaks"></a>Пропуск мультимедийных вставок
Как указано ранее в этой статье, можно задать свойство [**CanSkip**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Playback.MediaPlaybackItem.CanSkip) объекта **MediaPlaybackItem**, чтобы запретить пользователям пропускать содержимое с помощью встроенных элементов управления. Однако можно в любой момент вызвать из кода метод [**SkipCurrentBreak**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Playback.MediaBreakManager.SkipCurrentBreak), чтобы пропустить текущую вставку.

[!code-cs[SkipButtonClick](./code/MediaBreaks_RS1/cs/MainPage.xaml.cs#SnippetSkipButtonClick)]

## <a name="handle-mediabreak-events"></a>Обработка событий MediaBreak

Имеется несколько событий, связанных с мультимедийными вставками, которые можно зарегистрировать для выполнения действий на основе изменения состояния мультимедийных вставок.

[!code-cs[RegisterMediaBreakEvents](./code/MediaBreaks_RS1/cs/MainPage.xaml.cs#SnippetRegisterMediaBreakEvents)]

Событие [**BreakStarted**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Playback.MediaBreakManager.BreakStarted) возникает, когда начинается мультимедийная вставка. Можно обновить пользовательский интерфейс, чтобы пользователь знал, что воспроизводится мультимедийная вставка. В этом примере используется объект [**MediaBreakStartedEventArgs**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Playback.MediaBreakStartedEventArgs), передаваемый в обработчик для получения ссылки на начавшуюся мультимедийную вставку. Затем свойство [**CurrentItemIndex**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Playback.MediaPlaybackList.CurrentItemIndex) используется для определения воспроизводимого элемента мультимедиа из списка воспроизведения мультимедийной вставки. Далее обновляется пользовательский интерфейс, чтобы показать пользователю индекс текущей рекламы и количество рекламных роликов, оставшихся во вставке. Помните, что обновление пользовательского интерфейса должно выполняться в потоке пользовательского интерфейса, поэтому вызов следует производить из вызова метода [**RunAsync**](https://msdn.microsoft.com/library/windows/apps/hh750317). 

[!code-cs[BreakStarted](./code/MediaBreaks_RS1/cs/MainPage.xaml.cs#SnippetBreakStarted)]

Событие [**BreakEnded**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Playback.MediaBreakManager.BreakEnded) возникает после завершения воспроизведения всех элементов мультимедиа из вставки или после их пропуска. Обработчик этого события можно использовать для обновления пользовательского интерфейса, указывающего, что содержимое мультимедийной вставки больше не воспроизводится.

[!code-cs[BreakEnded](./code/MediaBreaks_RS1/cs/MainPage.xaml.cs#SnippetBreakEnded)]

Событие **BreakSkipped** возникает, когда пользователь нажимает кнопку *Далее* во встроенном пользовательском интерфейсе во время воспроизведения элемента, для которого свойство [**CanSkip**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Playback.MediaPlaybackItem.CanSkip) имеет значение true, или при пропуске вставки из кода путем вызова метода [**SkipCurrentBreak**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Playback.MediaBreakManager.SkipCurrentBreak).

В следующем примере свойство [**Source**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Playback.MediaPlayer.Source) объекта **MediaPlayer** используется для получения ссылки на элемент мультимедиа для основного содержимого. Пропущенная мультимедийная вставка входит в расписание вставок для этого элемента. Далее код проверяет, задано ли пропущенной мультимедийной вставке свойство расписания [**PrerollBreak**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Playback.MediaBreakSchedule.PrerollBreak). Если да, то была пропущена вставка перед началом воспроизведения; в таком случае создается новая вставка, воспроизведение которой планируется через 10 минут после начала основного содержимого.

[!code-cs[BreakSkipped](./code/MediaBreaks_RS1/cs/MainPage.xaml.cs#SnippetBreakSkipped)]

Событие [**BreaksSeekedOver**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Playback.MediaBreakManager.BreaksSeekedOver) возникает, когда при прокрутке положение воспроизведения основного элемента мультимедиа переходит через запланированное время для одной или нескольких мультимедийных вставок. В следующем примере проверяется, не произошел ли переход более чем через одну мультимедийную вставку при прокрутке положения воспроизведения вперед, а также не была ли выполнена прокрутка вперед менее чем на 10 минут. Если эти условия выполнены, первая вставка, через которую был выполнен переход (она получается из коллекции [**SeekedOverBreaks**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Playback.MediaBreakSeekedOverEventArgs.SeekedOverBreaks), предоставленной аргументами события), немедленно воспроизводится с помощью вызова метода [**PlayBreak**](https://msdn.microsoft.com/library/windows/apps/mt670689) объекта **MediaPlayer.BreakManager**.

[!code-cs[BreakSeekedOver](./code/MediaBreaks_RS1/cs/MainPage.xaml.cs#SnippetBreakSeekedOver)]


## <a name="access-the-current-playback-session"></a>Доступ к текущему сеансу воспроизведения
Объект [**MediaPlaybackSession**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Playback.MediaPlaybackSession) использует класс **MediaPlayer** для предоставления данных и событий, связанных с текущим воспроизводимым мультимедийным содержимым. [**MediaBreakManager**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Playback.MediaBreakManager) также имеет объект **MediaPlaybackSession**, к которому можно обращаться для получения данных и событий, связанных с конкретным воспроизводимым содержимым мультимедийной вставки. Сведения, которые можно получить из сеанса воспроизведения, включают в себя текущее состояние воспроизведения (воспроизводится или приостановлено) и текущее положение воспроизведения в рамках содержимого. Свойства [**NaturalVideoWidth**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Playback.MediaPlaybackSession.NaturalVideoWidth) и [**NaturalVideoHeight**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Playback.MediaPlaybackSession.NaturalVideoHeight), а также [**NaturalVideoSizeChanged**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Playback.MediaPlaybackSession.NaturalVideoSizeChanged) можно использовать для настройки пользовательского интерфейса видео, если мультимедийная вставка и основное содержимое имеют разное соотношение сторон. Можно также получать такие события, как [**BufferingStarted**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Playback.MediaPlaybackSession.BufferingStarted), [**BufferingEnded**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Playback.MediaPlaybackSession.BufferingEnded) и [**DownloadProgressChanged**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Playback.MediaPlaybackSession.DownloadProgressChanged), которые предоставляют ценную телеметрии о производительности приложения.

В следующем примере регистрируется обработчик **события BufferingProgressChanged**; в обработчике события пользовательский интерфейс обновляется для отображения текущего хода выполнения буферизации.

[!code-cs[RegisterBufferingProgressChanged](./code/MediaBreaks_RS1/cs/MainPage.xaml.cs#SnippetRegisterBufferingProgressChanged)]

[!code-cs[BufferingProgressChanged](./code/MediaBreaks_RS1/cs/MainPage.xaml.cs#SnippetBufferingProgressChanged)]

## <a name="related-topics"></a>Смежные разделы
* [Воспроизведение мультимедиа](media-playback.md)
* [Воспроизведение аудио и видео с помощью MediaPlayer](play-audio-and-video-with-mediaplayer.md)
* [Ручное управление системными элементами управления воспроизведением мультимедиа](system-media-transport-controls.md)

 

 




