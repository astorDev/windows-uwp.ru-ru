---
ms.assetid: F28162D4-AACC-4EE0-B243-5878F870F87F
description: Обработка поддерживаемых системой подсказок метаданных во время воспроизведения мультимедиа
title: Поддерживаемые системой синхронизированные подсказки метаданных
ms.date: 04/18/2017
ms.topic: article
keywords: Windows 10, uwp, метаданные, подсказки, голосовые функции, глава
ms.localizationpriority: medium
ms.openlocfilehash: 2b3753e92524e300252930f48433f91e175353c9
ms.sourcegitcommit: bf600a1fb5f7799961914f638061986d55f6ab12
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/04/2019
ms.locfileid: "9046111"
---
# <a name="system-supported-timed-metadata-cues"></a>Поддерживаемые системой синхронизированные подсказки метаданных
В этой статье описано, как использовать несколько форматов синхронизированных метаданных, которые можно внедрять в файлы или потоки мультимедиа. Приложения UWP можно регистрировать для событий, вызываемых конвейером мультимедиа во время воспроизведения при обнаружении этих подсказок метаданных. С помощью класса [**DataCue**](https://docs.microsoft.com/uwp/api/Windows.Media.Core.DataCue) приложения могут реализовывать собственные пользовательские подсказки метаданных, но в этой статье рассматриваются несколько стандартов метаданных, которые автоматически определяются конвейером мультимедиа, в том числе:

* субтитры на основе образа в формате VobSub;
* подсказки голосовых функций, включая границы слова, границы предложения и закладки на языке SSML;
* подсказки главы;
* расширенные комментарии M3U;
* теги ID3;
* поля emsg фрагментированных файлов MP4.


В этой статье используются понятия, описанные в статье [Элементы, списки воспроизведения и звуковые дорожки мультимедиа](media-playback-with-mediasource.md). Они включают основы работы с классами [**MediaSource**](https://docs.microsoft.com/uwp/api/windows.media.core.mediasource), [**MediaPlaybackItem**](https://docs.microsoft.com/uwp/api/windows.media.playback.mediaplaybackitem) и [**TimedMetadataTrack**](https://msdn.microsoft.com/library/windows/apps/dn956580), а также общие рекомендации по использованию синхронизированных метаданных в приложении.

Базовые шаги по реализации одинаковы для всех разнообразных типов синхронизированных метаданных, описанных в этой статье:

1. Создайте [**MediaSource**](https://docs.microsoft.com/uwp/api/windows.media.core.mediasource), а затем [**MediaPlaybackItem**](https://docs.microsoft.com/uwp/api/windows.media.playback.mediaplaybackitem) для воспроизведения содержимого.
2. Зарегистрируйтесь для события [**MediaPlaybackItem.TimedMetadataTracksChanged**](https://docs.microsoft.com/uwp/api/windows.media.playback.mediaplaybackitem.TimedMetadataTracksChanged), которое происходит, когда конвейер мультимедиа разрешает вложенные дорожки элемента мультимедиа.
3. Зарегистрируйтесь для событий [**TimedMetadataTrack.CueEntered**](https://docs.microsoft.com/uwp/api/windows.media.core.timedmetadatatrack.CueEntered) и [**TimedMetadataTrack.CueExited**](https://docs.microsoft.com/uwp/api/windows.media.core.timedmetadatatrack.CueExited) для синхронизированных дорожек метаданных, которые вы хотите использовать.
4. В обработчике событий **CueEntered** обновите свой пользовательский интерфейс на основе метаданных, переданных в аргументах события. Вы можете обновить пользовательский интерфейс повторно (например, чтобы удалить текущий текст субтитров) в событии **CueExited**.

В этой статье обработка каждого типа метаданных продемонстрирована на примере конкретного сценария, но можно обрабатывать (или игнорировать) различные типы метаданных с помощью общего кода. Можно проверять свойство [**TimedMetadataKind**](https://docs.microsoft.com/uwp/api/windows.media.core.timedmetadatatrack.TimedMetadataKind) объекта [**TimedMetadataTrack**](https://docs.microsoft.com/uwp/api/windows.media.core.timedmetadatatrack) на нескольких этапах процесса. Таким образом, например, можно зарегистрироваться для события **CueEntered** для дорожек метаданных, которые имеют значение **TimedMetadataKind.ImageSubtitle**, но не для дорожек со значением **TimedMetadataKind.Speech**. Вместо этого можно зарегистрировать обработчик для всех типов дорожек метаданных, а затем проверить значение **TimedMetadataKind** внутри обработчика **CueEntered**, чтобы определить действие, которое нужно выполнить в ответ на подсказку.

## <a name="image-based-subtitles"></a>Субтитры на основе образа
Начиная с Windows 10 версии 1703, приложения UWP могут поддерживать внешние субтитры на основе образа в формате VobSub. Чтобы использовать эту функцию, сначала создайте объект [**MediaSource**](https://docs.microsoft.com/uwp/api/windows.media.core.mediasource) для мультимедийного содержимого, для которого будут отображаться субтитры на основе образа. Затем создайте объект [**TimedTextSource**](https://docs.microsoft.com/uwp/api/windows.media.core.timedtextsource), вызвав [**CreateFromUriWithIndex**](https://docs.microsoft.com/uwp/api/windows.media.core.timedtextsource.CreateFromUriWithIndex) или [**CreateFromStreamWithIndex**](https://docs.microsoft.com/uwp/api/windows.media.core.timedtextsource.CreateFromStreamWithIndex) и передав URI или файл SUB с данными субтитров на основе образа и файл IDX, содержащий сведения о синхронизации для субтитров. Добавьте **TimedTextSource** в **MediaSource**, внеся его в коллекцию [**ExternalTimedTextSources**](https://docs.microsoft.com/uwp/api/windows.media.core.mediasource.ExternalTimedTextSources) источника. Создайте [**MediaPlaybackItem**](https://docs.microsoft.com/uwp/api/windows.media.playback.mediaplaybackitem) из **MediaSource**.

[!code-cs[ImageSubtitleLoadContent](./code/MediaSource_RS1/cs/MainPage_Cues.xaml.cs#SnippetImageSubtitleLoadContent)]

Зарегистрируйтесь для событий метаданных субтитров на основе образа с помощью объекта **MediaPlaybackItem**, созданного на предыдущем шаге. В этом примере для регистрации для событий используется вспомогательный метод **RegisterMetadataHandlerForImageSubtitles**. Лямбда-выражение используется для реализации обработчика для события [**TimedMetadataTracksChanged**](https://docs.microsoft.com/uwp/api/windows.media.playback.mediaplaybackitem.TimedMetadataTracksChanged). Оно возникает, когда система обнаруживает изменение в дорожках метаданных, связанных с **MediaPlaybackItem**. В некоторых случаях дорожки метаданных могут быть доступны при первоначальном разрешении элемента воспроизведения, поэтому вне обработчика **TimedMetadataTracksChanged** мы также просматриваем доступные дорожки метаданных и вызываем **RegisterMetadataHandlerForImageSubtitles**.

[!code-cs[ImageSubtitleTracksChanged](./code/MediaSource_RS1/cs/MainPage_Cues.xaml.cs#SnippetImageSubtitleTracksChanged)]

После того как выполнена регистрация для событий метаданных субтитров на основе образа, **MediaItem** назначается [**MediaPlayer**](https://docs.microsoft.com/uwp/api/windows.media.playback.mediaplayer) для воспроизведения в [**MediaPlayerElement**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.mediaplayerelement).

[!code-cs[ImageSubtitlePlay](./code/MediaSource_RS1/cs/MainPage_Cues.xaml.cs#SnippetImageSubtitlePlay)]

Во вспомогательном методе **RegisterMetadataHandlerForImageSubtitles** получите экземпляр класса [**TimedMetadataTrack**](https://docs.microsoft.com/uwp/api/windows.media.core.timedmetadatatrack) путем индексации элемента **MediaPlaybackItem** в коллекцию **TimedMetadataTracks**. Зарегистрируйтесь для события [**CueEntered**](https://docs.microsoft.com/uwp/api/windows.media.core.timedmetadatatrack.CueEntered) и события [**CueExited**](https://docs.microsoft.com/uwp/api/windows.media.core.timedmetadatatrack.CueExited). Затем необходимо вызвать [**SetPresentationMode**](https://docs.microsoft.com/uwp/api/windows.media.playback.mediaplaybacktimedmetadatatracklist.SetPresentationMode) в коллекции **TimedMetadataTracks** элемента воспроизведения, чтобы указать системе, что приложение хочет получать события подсказок метаданных для этого элемента воспроизведения.

[!code-cs[RegisterMetadataHandlerForImageSubtitles](./code/MediaSource_RS1/cs/MainPage_Cues.xaml.cs#SnippetRegisterMetadataHandlerForImageSubtitles)]

В обработчике для события **CueEntered** можно проверить свойство [**TimedMetadataKind**](https://docs.microsoft.com/uwp/api/windows.media.core.timedmetadatatrack.TimedMetadataKind) объекта [**TimedMetadataTrack**](https://docs.microsoft.com/uwp/api/windows.media.core.timedmetadatatrack), переданного в обработчик, чтобы увидеть, относятся ли метаданные к субтитрам на основе образа. Это необходимо, если вы используете один и тот же обработчик событий подсказок для нескольких типов метаданных. Если связанная дорожка метаданных имеет тип **TimedMetadataKind.ImageSubtitle**, приведите подсказку данных, содержащуюся в свойстве **Cue** [**MediaCueEventArgs**](https://docs.microsoft.com/uwp/api/windows.media.core.mediacueeventargs), к [**ImageCue**](https://docs.microsoft.com/uwp/api/windows.media.core.imagecue). Свойство [**SoftwareBitmap**](https://docs.microsoft.com/uwp/api/windows.media.core.imagecue.SoftwareBitmap) **ImageCue** содержит представление образа субтитров [**SoftwareBitmap**](https://docs.microsoft.com/uwp/api/windows.graphics.imaging.softwarebitmap). Создайте [**SoftwareBitmapSource**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.imaging.softwarebitmapsource) и вызовите [**SetBitmapAsync**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.imaging.softwarebitmapsource.SetBitmapAsync), чтобы назначить изображение элементу управления [**Image**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.image) на XAML. Свойства [**Extent**](https://docs.microsoft.com/uwp/api/windows.media.core.imagecue.Extent) и [**Position**](https://docs.microsoft.com/uwp/api/windows.media.core.imagecue.Position) **ImageCue** предоставляют сведения о размере и положении образа субтитров.

[!code-cs[ImageSubtitleCueEntered](./code/MediaSource_RS1/cs/MainPage_Cues.xaml.cs#SnippetImageSubtitleCueEntered)]

## <a name="speech-cues"></a>Подсказки голосовых функций
Начиная с Windows 10 версии 1703, приложения UWP можно регистрировать для получения событий в ответ на границы слов, границы предложений и закладки на языке SSML в воспроизводимых мультимедиа. Это позволяет воспроизводить звуковые потоки, созданные с помощью класса [**SpeechSynthesizer**](https://docs.microsoft.com/uwp/api/Windows.Media.SpeechSynthesis.SpeechSynthesizer), и обновлять пользовательский интерфейс на основе этих событий, например отображать текст воспроизводимого в данный момент слова или предложения.

В примере в этом разделе используется переменная-член класса для хранения текстовой строки, которая будет синтезирована и воспроизведена.

[!code-cs[SpeechInputText](./code/MediaSource_RS1/cs/MainPage_Cues.xaml.cs#SnippetSpeechInputText)]

Создайте новый экземпляр класса **SpeechSynthesizer**. Для параметров синтезатора [**IncludeWordBoundaryMetadata**](https://docs.microsoft.com/uwp/api/windows.media.speechsynthesis.speechsynthesizeroptions.IncludeWordBoundaryMetadata) и [**IncludeSentenceBoundaryMetadata**](https://docs.microsoft.com/uwp/api/windows.media.speechsynthesis.speechsynthesizeroptions.IncludeSentenceBoundaryMetadata) задайте значение **true**. Так вы укажете, что необходимо включить метаданные в созданный поток мультимедиа. Вызовите [**SynthesizeTextToStreamAsync**](https://docs.microsoft.com/uwp/api/Windows.Media.SpeechSynthesis.SpeechSynthesizer.SynthesizeTextToStreamAsync), чтобы создать поток, содержащий синтезированную речь и соответствующие метаданные. Создайте [**MediaSource**](https://docs.microsoft.com/uwp/api/windows.media.core.mediasource) и [**MediaPlaybackItem**](https://docs.microsoft.com/uwp/api/windows.media.playback.mediaplaybackitem) из синтезированного потока.

[!code-cs[SynthesizeSpeech](./code/MediaSource_RS1/cs/MainPage_Cues.xaml.cs#SnippetSynthesizeSpeech)]

Зарегистрируйтесь для событий метаданных голосовых функций с помощью объекта **MediaPlaybackItem**. В этом примере для регистрации для событий используется вспомогательный метод **RegisterMetadataHandlerForSpeech**. Лямбда-выражение используется для реализации обработчика для события [**TimedMetadataTracksChanged**](https://docs.microsoft.com/uwp/api/windows.media.playback.mediaplaybackitem.TimedMetadataTracksChanged). Оно возникает, когда система обнаруживает изменение в дорожках метаданных, связанных с **MediaPlaybackItem**.  В некоторых случаях дорожки метаданных могут быть доступны при первоначальном разрешении элемента воспроизведения, поэтому вне обработчика **TimedMetadataTracksChanged** мы также просматриваем доступные дорожки метаданных и вызываем **RegisterMetadataHandlerForSpeech**.

[!code-cs[SpeechTracksChanged](./code/MediaSource_RS1/cs/MainPage_Cues.xaml.cs#SnippetSpeechTracksChanged)]

После того как будет выполнена регистрация для событий метаданных голосовых функций на основе образа, **MediaItem** назначается [**MediaPlayer**](https://docs.microsoft.com/uwp/api/windows.media.playback.mediaplayer) для воспроизведения в [**MediaPlayerElement**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.mediaplayerelement).

[!code-cs[SpeechPlay](./code/MediaSource_RS1/cs/MainPage_Cues.xaml.cs#SnippetSpeechPlay)]

Во вспомогательном методе **RegisterMetadataHandlerForSpeech** получите экземпляр класса [**TimedMetadataTrack**](https://docs.microsoft.com/uwp/api/windows.media.core.timedmetadatatrack) путем индексации элемента **MediaPlaybackItem** в коллекцию **TimedMetadataTracks**. Зарегистрируйтесь для события [**CueEntered**](https://docs.microsoft.com/uwp/api/windows.media.core.timedmetadatatrack.CueEntered) и события [**CueExited**](https://docs.microsoft.com/uwp/api/windows.media.core.timedmetadatatrack.CueExited). Затем необходимо вызвать [**SetPresentationMode**](https://docs.microsoft.com/uwp/api/windows.media.playback.mediaplaybacktimedmetadatatracklist.SetPresentationMode) в коллекции **TimedMetadataTracks** элемента воспроизведения, чтобы указать системе, что приложение хочет получать события подсказок метаданных для этого элемента воспроизведения.

[!code-cs[RegisterMetadataHandlerForWords](./code/MediaSource_RS1/cs/MainPage_Cues.xaml.cs#SnippetRegisterMetadataHandlerForWords)]

В обработчике для события **CueEntered** можно проверить свойство [**TimedMetadataKind**](https://docs.microsoft.com/uwp/api/windows.media.core.timedmetadatatrack.TimedMetadataKind) объекта [**TimedMetadataTrack**](https://docs.microsoft.com/uwp/api/windows.media.core.timedmetadatatrack), переданного в обработчик, чтобы увидеть, относятся ли метаданные к голосовым функциям. Это необходимо, если вы используете один и тот же обработчик событий подсказок для нескольких типов метаданных. Если связанная дорожка метаданных имеет тип **TimedMetadataKind.Speech**, приведите подсказку данных, содержащуюся в свойстве **Cue** [**MediaCueEventArgs**](https://docs.microsoft.com/uwp/api/windows.media.core.mediacueeventargs), к [**SpeechCue**](https://docs.microsoft.com/uwp/api/windows.media.core.speechcue). Тип подсказок голосовых функций, включенных в дорожку метаданных, определяется путем проверки свойства **Label**. Значением этого свойства будет "SpeechWord" для границ слов, "SpeechSentence" для границ предложений или "SpeechBookmark" для закладок SSML. В этом примере мы проверяем наличие значения "SpeechWord". Если оно найдено, свойства [**StartPositionInInput**](https://docs.microsoft.com/uwp/api/windows.media.core.speechcue.StartPositionInInput) и [**EndPositionInInput**](https://docs.microsoft.com/uwp/api/windows.media.core.speechcue.EndPositionInInput) **SpeechCue** используются для определения расположения слова, которое в данный момент воспроизводится, в пределах введенного текста. В этом примере каждое слово просто выводится в отладочный вывод.

[!code-cs[SpeechWordCueEntered](./code/MediaSource_RS1/cs/MainPage_Cues.xaml.cs#SnippetSpeechWordCueEntered)]

## <a name="chapter-cues"></a>Подсказки главы
Начиная с Windows 10 версии 1703, можно приложения UWP могут регистрироваться на подсказки, которые соответствуют главам внутри элемента мультимедиа. Чтобы использовать эту функцию, создайте объект [**MediaSource**](https://docs.microsoft.com/uwp/api/windows.media.core.mediasource) для мультимедийного содержимого, а затем создайте [**MediaPlaybackItem**](https://docs.microsoft.com/uwp/api/windows.media.playback.mediaplaybackitem) из **MediaSource**.

[!code-cs[ChapterCueLoadContent](./code/MediaSource_RS1/cs/MainPage_Cues.xaml.cs#SnippetChapterCueLoadContent)]

Зарегистрируйтесь для событий метаданных глав с помощью объекта **MediaPlaybackItem**, созданного на предыдущем шаге. В этом примере для регистрации для событий используется вспомогательный метод **RegisterMetadataHandlerForChapterCues**. Лямбда-выражение используется для реализации обработчика для события [**TimedMetadataTracksChanged**](https://docs.microsoft.com/uwp/api/windows.media.playback.mediaplaybackitem.TimedMetadataTracksChanged). Оно возникает, когда система обнаруживает изменение в дорожках метаданных, связанных с **MediaPlaybackItem**. В некоторых случаях дорожки метаданных могут быть доступны при первоначальном разрешении элемента воспроизведения, поэтому вне обработчика **TimedMetadataTracksChanged** мы также просматриваем доступные дорожки метаданных и вызываем **RegisterMetadataHandlerForChapterCues**.

[!code-cs[ChapterCueTracksChanged](./code/MediaSource_RS1/cs/MainPage_Cues.xaml.cs#SnippetChapterCueTracksChanged)]

После того как выполнена регистрация событий метаданных глав, **MediaItem** назначается [**MediaPlayer**](https://docs.microsoft.com/uwp/api/windows.media.playback.mediaplayer) для воспроизведения в [**MediaPlayerElement**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.mediaplayerelement).

[!code-cs[ChapterCuePlay](./code/MediaSource_RS1/cs/MainPage_Cues.xaml.cs#SnippetChapterCuePlay)]

Во вспомогательном методе **RegisterMetadataHandlerForChapterCues** получите экземпляр класса [**TimedMetadataTrack**](https://docs.microsoft.com/uwp/api/windows.media.core.timedmetadatatrack) путем индексации элемента **MediaPlaybackItem** в коллекцию **TimedMetadataTracks**. Зарегистрируйтесь для события [**CueEntered**](https://docs.microsoft.com/uwp/api/windows.media.core.timedmetadatatrack.CueEntered) и события [**CueExited**](https://docs.microsoft.com/uwp/api/windows.media.core.timedmetadatatrack.CueExited). Затем необходимо вызвать [**SetPresentationMode**](https://docs.microsoft.com/uwp/api/windows.media.playback.mediaplaybacktimedmetadatatracklist.SetPresentationMode) в коллекции **TimedMetadataTracks** элемента воспроизведения, чтобы указать системе, что приложение хочет получать события подсказок метаданных для этого элемента воспроизведения.

[!code-cs[RegisterMetadataHandlerForChapterCues](./code/MediaSource_RS1/cs/MainPage_Cues.xaml.cs#SnippetRegisterMetadataHandlerForChapterCues)]

В обработчике события **CueEntered** вы можете проверить свойство [**TimedMetadataKind**](https://docs.microsoft.com/uwp/api/windows.media.core.timedmetadatatrack.TimedMetadataKind) объекта [**TimedMetadataTrack**](https://docs.microsoft.com/uwp/api/windows.media.core.timedmetadatatrack), переданного в обработчик и посмотреть, относятся ли эти метаданные к подсказкам главы. Этот обязательное действие, если вы используете один обработчик для событий о метаданных разного типа. Если связанная дорожка метаданных имеет тип **TimedMetadataKind.Chapter**, приведите подсказку данных, содержащуюся в свойстве **Cue** [**MediaCueEventArgs**](https://docs.microsoft.com/uwp/api/windows.media.core.mediacueeventargs), к [**ChapterCue**](https://docs.microsoft.com/uwp/api/windows.media.core.chaptercue). Свойство [**Title**](https://docs.microsoft.com/uwp/api/windows.media.core.chaptercue.Title) **ChapterCue** содержит заголовок главы, которая была только что достигнута при воспроизведении.

[!code-cs[ChapterCueEntered](./code/MediaSource_RS1/cs/MainPage_Cues.xaml.cs#SnippetChapterCueEntered)]

### <a name="seek-to-the-next-chapter-using-chapter-cues"></a>Переход к следующей главе с помощью подсказок глав
Помимо получения уведомлений при изменении текущей главы в воспроизводимом элементе, с помощью подсказок глав можно также искать следующую главу в воспроизводимом элементе. В показанном ниже примере метод принимает аргументы [**MediaPlayer**](https://docs.microsoft.com/uwp/api/windows.media.playback.mediaplayer) и [**MediaPlaybackItem**](https://docs.microsoft.com/uwp/api/windows.media.playback.mediaplaybackitem), которые представляют воспроизводимый сейчас элемент мультимедиа. В коллекции [**TimedMetadataTracks**](https://docs.microsoft.com/uwp/api/windows.media.playback.mediaplaybackitem.TimedMetadataTracks) выполняется поиск, чтобы определить, есть ли дорожки со свойством [**TimedMetadataKind**](https://docs.microsoft.com/uwp/api/windows.media.core.timedmetadatatrack.TimedMetadataKind), имеющим значение [**TimedMetadataTrack**](https://docs.microsoft.com/uwp/api/windows.media.core.timedmetadatatrack) для **TimedMetadataKind.Chapter**.  Если дорожка главы найдена, метод выполняет цикл на каждой подсказке в коллекции [**Cues**](https://docs.microsoft.com/uwp/api/windows.media.core.timedmetadatatrack.Cues) дорожки, чтобы найти первую подсказку со значением [**StartTime**](https://docs.microsoft.com/uwp/api/windows.media.core.chaptercue.StartTime), превышающим текущее значение [**Position**](https://docs.microsoft.com/uwp/api/windows.media.playback.mediaplaybacksession.Position) сеанса воспроизведения проигрывателя мультимедиа. Когда будет найдена правильная подсказка, обновится положение сеанса воспроизведения и обновится название главы в пользовательском интерфейсе.

[!code-cs[GoToNextChapter](./code/MediaSource_RS1/cs/MainPage_Cues.xaml.cs#SnippetGoToNextChapter)]

## <a name="extended-m3u-comments"></a>Расширенные комментарии M3U
Начиная с Windows 10 версии 1703, для приложений UWP можно регистрировать подсказки, которые соответствуют комментариям в расширенном файле манифеста M3U. В этом примере используется [**AdaptiveMediaSource**](https://docs.microsoft.com/uwp/api/windows.media.streaming.adaptive.adaptivemediasource) для воспроизведения мультимедиа. Дополнительные сведения см. в статье [Адаптивная потоковая передача](adaptive-streaming.md). Создайте **AdaptiveMediaSource** для содержимого, вызвав [**CreateFromUriAsync**](https://docs.microsoft.com/uwp/api/windows.media.streaming.adaptive.adaptivemediasource.CreateFromUriAsync) или [**CreateFromStreamAsync**](https://docs.microsoft.com/uwp/api/windows.media.streaming.adaptive.adaptivemediasource.CreateFromStreamAsync). Создайте объект [**MediaSource**](https://docs.microsoft.com/uwp/api/windows.media.core.mediasource), вызвав [**CreateFromAdaptiveMediaSource**](https://docs.microsoft.com/uwp/api/windows.media.core.mediasource.CreateFromAdaptiveMediaSource), а затем создайте [**MediaPlaybackItem**](https://docs.microsoft.com/uwp/api/windows.media.playback.mediaplaybackitem) из **MediaSource**.

[!code-cs[EXTM3ULoadContent](./code/MediaSource_RS1/cs/MainPage_Cues.xaml.cs#SnippetEXTM3ULoadContent)]

Зарегистрируйтесь для событий метаданных M3U с помощью объекта **MediaPlaybackItem**, созданного на предыдущем шаге. В этом примере для регистрации для событий используется вспомогательный метод **RegisterMetadataHandlerForEXTM3UCues**. Лямбда-выражение используется для реализации обработчика для события [**TimedMetadataTracksChanged**](https://docs.microsoft.com/uwp/api/windows.media.playback.mediaplaybackitem.TimedMetadataTracksChanged). Оно возникает, когда система обнаруживает изменение в дорожках метаданных, связанных с **MediaPlaybackItem**. В некоторых случаях дорожки метаданных могут быть доступны при первоначальном разрешении элемента воспроизведения, поэтому вне обработчика **TimedMetadataTracksChanged** мы также просматриваем доступные дорожки метаданных и вызываем **RegisterMetadataHandlerForEXTM3UCues**.

[!code-cs[EXTM3UCueTracksChanged](./code/MediaSource_RS1/cs/MainPage_Cues.xaml.cs#SnippetEXTM3UCueTracksChanged)]

После регистрации на события о метаданных M3U, **MediaItem** назначается объекту [**MediaPlayer**](https://docs.microsoft.com/uwp/api/windows.media.playback.mediaplayer) для последующего воспроизведения в [**MediaPlayerElement**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.mediaplayerelement).

[!code-cs[EXTM3UCuePlay](./code/MediaSource_RS1/cs/MainPage_Cues.xaml.cs#SnippetEXTM3UCuePlay)]

Во вспомогательном методе **RegisterMetadataHandlerForEXTM3UCues** получите экземпляр класса [**TimedMetadataTrack**](https://docs.microsoft.com/uwp/api/windows.media.core.timedmetadatatrack) путем индексации элемента **MediaPlaybackItem** в коллекции **TimedMetadataTracks**. Проверьте свойство DispatchType дорожки метаданных, которое будет иметь значение "EXTM3U", если дорожка представляет комментарии M3U. Зарегистрируйтесь для события [**CueEntered**](https://docs.microsoft.com/uwp/api/windows.media.core.timedmetadatatrack.CueEntered) и события [**CueExited**](https://docs.microsoft.com/uwp/api/windows.media.core.timedmetadatatrack.CueExited). Затем необходимо вызвать [**SetPresentationMode**](https://docs.microsoft.com/uwp/api/windows.media.playback.mediaplaybacktimedmetadatatracklist.SetPresentationMode) в коллекции **TimedMetadataTracks** элемента воспроизведения, чтобы указать системе, что приложение хочет получать события подсказок метаданных для этого элемента воспроизведения.

[!code-cs[RegisterMetadataHandlerForEXTM3UCues](./code/MediaSource_RS1/cs/MainPage_Cues.xaml.cs#SnippetRegisterMetadataHandlerForEXTM3UCues)]

В обработчике для события **CueEntered** приведите подсказку данных, содержащуюся в свойстве **Cue** [**MediaCueEventArgs**](https://docs.microsoft.com/uwp/api/windows.media.core.mediacueeventargs), к [**DataCue**](https://docs.microsoft.com/uwp/api/windows.media.core.datacue).  Убедитесь, что **DataCue** и свойство [**Data**](https://docs.microsoft.com/uwp/api/windows.media.core.datacue.Data) подсказки не равны Null. Расширенные комментарии EMU указываются в формате UTF-16 c прямым порядком байтов и заканчиваются символом NULL. Создайте новый **DataReader** для считывания данных подсказок, вызвав [**DataReader.FromBuffer**](https://docs.microsoft.com/uwp/api/windows.storage.streams.datareader.FromBuffer). Задайте для свойства [**UnicodeEncoding**](https://docs.microsoft.com/uwp/api/windows.storage.streams.datareader.UnicodeEncoding) ридера значение [**Utf16LE**](https://docs.microsoft.com/uwp/api/windows.storage.streams.unicodeencoding) для считывания данных в нужном формате. Вызовите [**ReadString**](https://docs.microsoft.com/uwp/api/windows.storage.streams.datareader.ReadString) для считывания данных, указав половину длины поля **Data**, так как каждый символ имеет размер два байта, и отнимите один, чтобы удалить конечный символ NULL. В этом примере комментарий M3U просто записывается в отладочный вывод.

[!code-cs[EXTM3UCueEntered](./code/MediaSource_RS1/cs/MainPage_Cues.xaml.cs#SnippetEXTM3UCueEntered)]

## <a name="id3-tags"></a>Теги ID3
Начиная с Windows 10 версии 1703, для приложений UWP можно регистрировать подсказки, которые соответствуют тегам ID3, внутри содержимого Http Live Streaming (HLS). В этом примере используется [**AdaptiveMediaSource**](https://docs.microsoft.com/uwp/api/windows.media.streaming.adaptive.adaptivemediasource) для воспроизведения мультимедиа. Дополнительные сведения см. в статье [Адаптивная потоковая передача](adaptive-streaming.md). Создайте **AdaptiveMediaSource** для содержимого, вызвав [**CreateFromUriAsync**](https://docs.microsoft.com/uwp/api/windows.media.streaming.adaptive.adaptivemediasource.CreateFromUriAsync) или [**CreateFromStreamAsync**](https://docs.microsoft.com/uwp/api/windows.media.streaming.adaptive.adaptivemediasource.CreateFromStreamAsync). Создайте объект [**MediaSource**](https://docs.microsoft.com/uwp/api/windows.media.core.mediasource), вызвав [**CreateFromAdaptiveMediaSource**](https://docs.microsoft.com/uwp/api/windows.media.core.mediasource.CreateFromAdaptiveMediaSource), а затем создайте [**MediaPlaybackItem**](https://docs.microsoft.com/uwp/api/windows.media.playback.mediaplaybackitem) из **MediaSource**.

[!code-cs[EXTM3ULoadContent](./code/MediaSource_RS1/cs/MainPage_Cues.xaml.cs#SnippetEXTM3ULoadContent)]

Зарегистрируйтесь для событий тегов ID3 с помощью объекта **MediaPlaybackItem**, созданного на предыдущем шаге. В этом примере для регистрации для событий используется вспомогательный метод **RegisterMetadataHandlerForID3Cues**. Лямбда-выражение используется для реализации обработчика для события [**TimedMetadataTracksChanged**](https://docs.microsoft.com/uwp/api/windows.media.playback.mediaplaybackitem.TimedMetadataTracksChanged). Оно возникает, когда система обнаруживает изменение в дорожках метаданных, связанных с **MediaPlaybackItem**. В некоторых случаях дорожки метаданных могут быть доступны при первоначальном разрешении элемента воспроизведения, поэтому вне обработчика **TimedMetadataTracksChanged** мы также просматриваем доступные дорожки метаданных и вызываем **RegisterMetadataHandlerForID3Cues**.

[!code-cs[ID3LoadContent](./code/MediaSource_RS1/cs/MainPage_Cues.xaml.cs#SnippetID3LoadContent)]

После регистрации на события о метаданных ID3 объект **MediaItem** назначается [**MediaPlayer**](https://docs.microsoft.com/uwp/api/windows.media.playback.mediaplayer) для последующего воспроизведения в [**MediaPlayerElement**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.mediaplayerelement).

[!code-cs[ID3CuePlay](./code/MediaSource_RS1/cs/MainPage_Cues.xaml.cs#SnippetID3CuePlay)]


Во вспомогательном методе **RegisterMetadataHandlerForID3Cues** получите экземпляр класса [**TimedMetadataTrack**](https://docs.microsoft.com/uwp/api/windows.media.core.timedmetadatatrack) путем индексации элемента **MediaPlaybackItem** в коллекцию **TimedMetadataTracks**. Проверьте свойство DispatchType дорожки метаданных, значение которого будет содержать строку GUID "15260DFFFF49443320FF49443320000F", если дорожка представляет теги ID3. Зарегистрируйтесь для события [**CueEntered**](https://docs.microsoft.com/uwp/api/windows.media.core.timedmetadatatrack.CueEntered) и события [**CueExited**](https://docs.microsoft.com/uwp/api/windows.media.core.timedmetadatatrack.CueExited). Затем необходимо вызвать [**SetPresentationMode**](https://docs.microsoft.com/uwp/api/windows.media.playback.mediaplaybacktimedmetadatatracklist.SetPresentationMode) в коллекции **TimedMetadataTracks** элемента воспроизведения, чтобы указать системе, что приложение хочет получать события подсказок метаданных для этого элемента воспроизведения.

[!code-cs[RegisterMetadataHandlerForID3Cues](./code/MediaSource_RS1/cs/MainPage_Cues.xaml.cs#SnippetRegisterMetadataHandlerForID3Cues)]

В обработчике для события **CueEntered** приведите подсказку данных, содержащуюся в свойстве **Cue** [**MediaCueEventArgs**](https://docs.microsoft.com/uwp/api/windows.media.core.mediacueeventargs), к [**DataCue**](https://docs.microsoft.com/uwp/api/windows.media.core.datacue).  Убедитесь, что **DataCue** и свойство [**Data**](https://docs.microsoft.com/uwp/api/windows.media.core.datacue.Data) подсказки не равны Null. Расширенные комментарии ЕС предоставляются в виде необработанных байт в транспортном потока (см. [http://id3.org/id3v2.4.0-structure](https://id3.org/id3v2.4.0-structure)). Создайте новый **DataReader** для считывания данных подсказок, вызвав [**DataReader.FromBuffer**](https://docs.microsoft.com/uwp/api/windows.storage.streams.datareader.FromBuffer).  В этом примере значения заголовка из тега ID3 считываются из данных подсказки и записываются в отладочные данные.

[!code-cs[ID3CueEntered](./code/MediaSource_RS1/cs/MainPage_Cues.xaml.cs#SnippetID3CueEntered)]

## <a name="fragmented-mp4-emsg-boxes"></a>Поля emsg фрагментированного файла MP4
Начиная с Windows 10 версии 1703, можно регистрировать приложения DWP для подсказок, которые соответствуют полям emsg в потоках фрагментированных файлов MP4. Этот тип метаданных используют, например, поставщики содержимого, чтобы дать сигнал клиентским приложениям воспроизвести рекламное объявление во время потоковой передачи в реальном времени. В этом примере используется [**AdaptiveMediaSource**](https://docs.microsoft.com/uwp/api/windows.media.streaming.adaptive.adaptivemediasource) для воспроизведения мультимедиа. Дополнительные сведения см. в статье [Адаптивная потоковая передача](adaptive-streaming.md). Создайте **AdaptiveMediaSource** для содержимого, вызвав [**CreateFromUriAsync**](https://docs.microsoft.com/uwp/api/windows.media.streaming.adaptive.adaptivemediasource.CreateFromUriAsync) или [**CreateFromStreamAsync**](https://docs.microsoft.com/uwp/api/windows.media.streaming.adaptive.adaptivemediasource.CreateFromStreamAsync). Создайте объект [**MediaSource**](https://docs.microsoft.com/uwp/api/windows.media.core.mediasource), вызвав [**CreateFromAdaptiveMediaSource**](https://docs.microsoft.com/uwp/api/windows.media.core.mediasource.CreateFromAdaptiveMediaSource), а затем создайте [**MediaPlaybackItem**](https://docs.microsoft.com/uwp/api/windows.media.playback.mediaplaybackitem) из **MediaSource**.

[!code-cs[EmsgLoadContent](./code/MediaSource_RS1/cs/MainPage_Cues.xaml.cs#SnippetEmsgLoadContent)]

Зарегистрируйтесь для событий полей emsg с помощью объекта **MediaPlaybackItem**, созданного на предыдущем шаге. В этом примере для регистрации для событий используется вспомогательный метод **RegisterMetadataHandlerForEmsgCues**. Лямбда-выражение используется для реализации обработчика для события [**TimedMetadataTracksChanged**](https://docs.microsoft.com/uwp/api/windows.media.playback.mediaplaybackitem.TimedMetadataTracksChanged). Оно возникает, когда система обнаруживает изменение в дорожках метаданных, связанных с **MediaPlaybackItem**. В некоторых случаях дорожки метаданных могут быть доступны при первоначальном разрешении элемента воспроизведения, поэтому вне обработчика **TimedMetadataTracksChanged** мы также просматриваем доступные дорожки метаданных и вызываем **RegisterMetadataHandlerForEmsgCues**.

[!code-cs[ID3LoadContent](./code/MediaSource_RS1/cs/MainPage_Cues.xaml.cs#SnippetID3LoadContent)]

После того как зарегистрированы события метаданных полей emsg, **MediaItem** назначается [**MediaPlayer**](https://docs.microsoft.com/uwp/api/windows.media.playback.mediaplayer) для воспроизведения в [**MediaPlayerElement**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.mediaplayerelement).

[!code-cs[EmsgCuePlay](./code/MediaSource_RS1/cs/MainPage_Cues.xaml.cs#SnippetEmsgCuePlay)]


Во вспомогательном методе **RegisterMetadataHandlerForEmsgCues** получите экземпляр класса [**TimedMetadataTrack**](https://docs.microsoft.com/uwp/api/windows.media.core.timedmetadatatrack) путем индексации элемента **MediaPlaybackItem** в коллекцию **TimedMetadataTracks**. Проверьте свойство DispatchType дорожки метаданных, которое будет иметь значение "emsg:mp4", если дорожка представляет поля emsg. Зарегистрируйтесь для события [**CueEntered**](https://docs.microsoft.com/uwp/api/windows.media.core.timedmetadatatrack.CueEntered) и события [**CueExited**](https://docs.microsoft.com/uwp/api/windows.media.core.timedmetadatatrack.CueExited). Затем необходимо вызвать [**SetPresentationMode**](https://docs.microsoft.com/uwp/api/windows.media.playback.mediaplaybacktimedmetadatatracklist.SetPresentationMode) в коллекции **TimedMetadataTracks** элемента воспроизведения, чтобы указать системе, что приложение хочет получать события подсказок метаданных для этого элемента воспроизведения.


[!code-cs[RegisterMetadataHandlerForEmsgCues](./code/MediaSource_RS1/cs/MainPage_Cues.xaml.cs#SnippetRegisterMetadataHandlerForEmsgCues)]


В обработчике для события **CueEntered** приведите подсказку данных, содержащуюся в свойстве **Cue** [**MediaCueEventArgs**](https://docs.microsoft.com/uwp/api/windows.media.core.mediacueeventargs), к [**DataCue**](https://docs.microsoft.com/uwp/api/windows.media.core.datacue).  Убедитесь, что значение объекта **DataCue**— не NULL. Свойства поля emsg предоставляются конвейером мультимедиа как настраиваемые свойства в коллекции [**Properties**](https://docs.microsoft.com/uwp/api/windows.media.core.datacue.Properties) объекта DataCue. В этом примере выполняется попытка извлечь несколько различных значений свойств с помощью метода **[TryGetValue](https://docs.microsoft.com/uwp/api/windows.foundation.collections.propertyset.trygetvalue)**. Если этот метод возвращает значение NULL, это означает, что запрошенное свойство отсутствует в поле emsg, поэтому устанавливается значение по умолчанию.

В следующей части примера демонстрируется сценарий запуска воспроизведения рекламы. Это происходит, когда в свойстве *scheme_id_uri*, полученного на предыдущем шаге, указано значение "urn:scte:scte35:2013:xml" (см. [http://dashif.org/identifiers/event-schemes/](https://dashif.org/identifiers/event-schemes/)). Обратите внимание, что согласно стандарту рекомендуется отправлять этот emsg несколько раз для обеспечения избыточности, поэтому в данном примере поддерживается список идентификаторов emsg, которые уже были обработаны, и обрабатывает только новые сообщения. Создайте новый **DataReader** для считывания данных подсказок, вызвав [**DataReader.FromBuffer**](https://docs.microsoft.com/uwp/api/windows.storage.streams.datareader.FromBuffer), и задайте кодировку UTF-8, установив свойство [**UnicodeEncoding**](https://docs.microsoft.com/uwp/api/windows.storage.streams.datareader.UnicodeEncoding), а затем считайте данные. В этом примере полезные данные сообщения записываются в отладочные данные. Реальное приложение использовало бы полезные данные для планирования воспроизведения рекламного объявления.

[!code-cs[EmsgCueEntered](./code/MediaSource_RS1/cs/MainPage_Cues.xaml.cs#SnippetEmsgCueEntered)]


## <a name="related-topics"></a>Статьи по теме

* [Воспроизведение мультимедиа](media-playback.md)
* [Элементы мультимедиа, списки воспроизведения и дорожки](media-playback-with-mediasource.md)


 




