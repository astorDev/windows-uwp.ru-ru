---
ms.assetid: 58af5e9d-37a1-4f42-909c-db7cb02a0d12
description: В этой статье рассказывается, как воспроизводить мультимедиа в универсальном приложении для Windows с помощью MediaPlayer.
title: Воспроизведение аудио и видео с помощью MediaPlayer
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 4d33a2bf1505618dca4e0e54c2bd9a534f58bcfc
ms.sourcegitcommit: c01c29cd97f1cbf050950526e18e15823b6a12a0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/05/2018
ms.locfileid: "8704684"
---
# <a name="play-audio-and-video-with-mediaplayer"></a>Воспроизведение аудио и видео с помощью MediaPlayer

В этой статье рассказывается, как воспроизводить мультимедиа в универсальном приложении для Windows с помощью класса [**MediaPlayer**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Playback.MediaPlayer). В Windows 10 версии 1607 внесены значительные улучшения в интерфейсы API воспроизведения мультимедиа, включая упрощенную состоящую из одного процесса методику фонового воспроизведения звука, автоматическую интеграцию с системными элементами управления транспортировкой мультимедиа (SMTC), возможность синхронизации нескольких проигрывателей мультимедиа, возможность использования поверхности Windows.UI.Composition и удобный интерфейс создания и планирования мультимедийных вставок в содержимое. Для реализации этих улучшений рекомендуется использовать класс **MediaPlayer** вместо **MediaElement** для воспроизведения мультимедийного содержимого. Добавлен облегченный элемент управления XAML [**MediaPlayerElement**](https://msdn.microsoft.com/library/windows/apps/Windows.UI.Xaml.Controls.MediaPlayerElement), предназначенный для отрисовки мультимедийного содержимого на странице XAML. Многие API состояния и управления воспроизведением, предоставляемые классом **MediaElement**, теперь доступны с помощью нового объекта [**MediaPlaybackSession**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Playback.MediaPlaybackSession). **MediaElement** продолжает выполнять функцию поддержки обратной совместимости, но дополнительные возможности в этот класс добавляться не будут.

Эта статья содержит подробный разбор функций **MediaPlayer**, которые обычно используются в приложениях для воспроизведения мультимедиа. Обратите внимание, что **MediaPlayer** использует класс [**MediaSource**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Core.MediaSource) в качестве контейнера для всех элементов мультимедиа. Этот класс позволяет загружать и воспроизводить мультимедиа из множества различных источников, включая локальные файлы, потоки в памяти и сетевые источники, с использованием одного интерфейса. Также существуют классы более высокого уровня, совместимые с **MediaSource**, например [**MediaPlaybackItem**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Playback.MediaPlaybackItem) и [**MediaPlaybackList**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Playback.MediaPlaybackList), которые предоставляют расширенные возможности, такие как списки воспроизведения, и обеспечивают способность работать с источниками мультимедиа, содержащими несколько дорожек видео-, аудио- и метаданных. Подробные сведения о классе **MediaSource** и связанных с ним API см. в разделе [Элементы, плей-листы и звуковые дорожки мультимедиа](media-playback-with-mediasource.md).

> [!NOTE] 
> В выпусках Windows 10 N и Windows 10 KN нет компонентов мультимедиа, необходимых для использования **MediaPlayer** для воспроизведения. Эти компоненты можно установить вручную. Дополнительные сведения см. в разделе [Пакет дополнительных компонентов для работы с мультимедиа для выпусков Windows10N и Windows10KN](https://support.microsoft.com/en-us/help/3010081/media-feature-pack-for-windows-10-n-and-windows-10-kn-editions).

## <a name="play-a-media-file-with-mediaplayer"></a>Воспроизведение файла мультимедиа с помощью MediaPlayer  
Простое воспроизведение мультимедиа с помощью **MediaPlayer** реализовать очень легко. Сначала создайте новый экземпляр класса **MediaPlayer**. Ваше приложение может содержать несколько активных экземпляров **MediaPlayer** одновременно. Затем задайте свойству [**Source**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Playback.MediaPlayer.Source) проигрывателя значение объекта, реализующего [**IMediaPlaybackSource**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Playback.IMediaPlaybackSource), такого как [**MediaSource**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Core.MediaSource), [**MediaPlaybackItem**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Playback.MediaPlaybackItem) или [**MediaPlaybackList**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Playback.MediaPlaybackList). В этом примере **MediaSource** создается из файла в локальном хранилище приложения, затем из источника создается **MediaPlaybackItem** и далее назначается свойству **Source** проигрывателя.

В отличие от **MediaElement**, **MediaPlayer** по умолчанию не начинает воспроизведение автоматически. Начать воспроизведение можно путем вызова [**Play**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Playback.MediaPlayer.Play), путем присвоения свойству [**AutoPlay**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Playback.MediaPlayer.AutoPlay) значения true, а также можно дождаться, пока пользователь не начнет воспроизведение с помощью встроенных элементов управления мультимедиа.

[!code-cs[SimpleFilePlayback](./code/MediaPlayer_RS1/cs/MainPage.xaml.cs#SnippetSimpleFilePlayback)]

Когда приложение завершает использование **MediaPlayer**, необходимо вызвать метод [**Close**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Playback.MediaPlayer.Close) (соответствующий **Dispose** в C#), чтобы высвободить ресурсы, используемые проигрывателем.

[!code-cs[CloseMediaPlayer](./code/MediaPlayer_RS1/cs/MainPage.xaml.cs#SnippetCloseMediaPlayer)]

## <a name="use-mediaplayerelement-to-render-video-in-xaml"></a>Использование MediaPlayerElement для отрисовки видео в XAML
Вы можете воспроизводить мультимедиа в **MediaPlayer** без отображения в XAML, но для многих приложений с воспроизведением мультимедиа потребуется отрисовка мультимедиа на странице XAML. Для этого используйте облегченный элемент управления [**MediaPlayerElement**](https://msdn.microsoft.com/library/windows/apps/Windows.UI.Xaml.Controls.MediaPlayerElement). Как и **MediaElement**, **MediaPlayerElement** позволяет указать, следует ли отображать встроенные элементы управления транспортировкой.

[!code-xml[MediaPlayerElementXAML](./code/MediaPlayer_RS1/cs/MainPage.xaml#SnippetMediaPlayerElementXAML)]

Вы можете задать экземпляр **MediaPlayer**, к которому привязан элемент управления, вызвав метод [**SetMediaPlayer**](https://msdn.microsoft.com/library/windows/apps/mt708764).

[!code-cs[SetMediaPlayer](./code/MediaPlayer_RS1/cs/MainPage.xaml.cs#SnippetSetMediaPlayer)]

Вы можете также задать источник воспроизведения в **MediaPlayerElement**, и этот элемент автоматически создаст новый экземпляр **MediaPlayer**, получить доступ к которому можно с помощью свойства [**MediaPlayer**](https://msdn.microsoft.com/library/windows/apps/Windows.UI.Xaml.Controls.MediaPlayerElement.MediaPlayer).

[!code-cs[GetPlayerFromElement](./code/MediaPlayer_RS1/cs/MainPage.xaml.cs#SnippetGetPlayerFromElement)]

> [!NOTE] 
> Если вы отключите [**MediaPlaybackCommandManager**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Playback.MediaPlaybackCommandManager) в объекте [**MediaPlayer**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Playback.MediaPlayer), установив для свойства [**IsEnabled**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Playback.MediaPlaybackCommandManager.IsEnabled) значение false, это нарушит связь между свойством [**TransportControls**](https://msdn.microsoft.com/library/windows/apps/Windows.UI.Xaml.Controls.MediaPlayerElement.TransportControls) объекта **MediaPlayer**, предоставляемым классом **MediaPlayerElement**, поэтому встроенные элементы управления транспортом больше не будут автоматически управлять воспроизведением мультимедиа. Вместо этого вам нужно будет реализовать собственные элементы управления для управления воспроизведением **MediaPlayer**.

## <a name="common-mediaplayer-tasks"></a>Типичные задачи, связанные с MediaPlayer
В этом разделе рассказывается, как использовать некоторые функции **MediaPlayer**.

### <a name="set-the-audio-category"></a>Задание категории аудио
Задайте свойству [**AudioCategory**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Playback.MediaPlayer.AudioCategory) объекта **MediaPlayer** одно из значений из перечисления [**MediaPlayerAudioCategory**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Playback.MediaPlayerAudioCategory), чтобы сообщить системе, какой тип мультимедиа воспроизводится. Игры должны относить свои потоки музыки к категории **GameMedia**, чтобы звук музыки в игре автоматически отключался, если другое приложение воспроизводит музыку в фоновом режиме. Приложения для музыки и видео должны относить свои потоки к категории **Media** или **Movie**, чтобы у них был приоритет перед потоками **GameMedia**.

[!code-cs[SetAudioCategory](./code/MediaPlayer_RS1/cs/MainPage.xaml.cs#SnippetSetAudioCategory)]

### <a name="output-to-a-specific-audio-endpoint"></a>Вывод на определенную конечную точку аудио
По умолчанию вывод звука из **MediaPlayer** направляется на конечную точку системы по умолчанию, но вы можете указать собственную конечную точку аудио, которую **MediaPlayer** будет использовать для вывода. В примере ниже [**MediaDevice.GetAudioRenderSelector**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Devices.MediaDevice.GetAudioRenderSelector) возвращает строку, которая уникальным образом идентифицирует категорию обработки аудио для устройств. Далее вызывается метод [**FindAllAsync**](https://msdn.microsoft.com/library/windows/apps/Windows.Devices.Enumeration.DeviceInformation.FindAllAsync) класса [**DeviceInformation**](https://msdn.microsoft.com/library/windows/apps/Windows.Devices.Enumeration.DeviceInformation), чтобы получить список всех доступных устройств выбранного типа. Можно программным способом определить, какое устройство требуется использовать, или добавить возвращаемые устройства в [**ComboBox**](https://msdn.microsoft.com/library/windows/apps/Windows.UI.Xaml.Controls.ComboBox), чтобы пользователь мог выбрать устройство.

[!code-cs[SetAudioEndpointEnumerate](./code/MediaPlayer_RS1/cs/MainPage.xaml.cs#SnippetSetAudioEndpointEnumerate)]

В событии [**SelectionChanged**](https://msdn.microsoft.com/library/windows/apps/Windows.UI.Xaml.Controls.Primitives.Selector.SelectionChanged) для поля со списком устройств свойству [**AudioDevice**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Playback.MediaPlayer.AudioDevice) объекта **MediaPlayer** задано значение выбранного устройства, которое хранилось в свойстве [**Tag**](https://msdn.microsoft.com/library/windows/apps/Windows.UI.Xaml.FrameworkElement.Tag) объекта **ComboBoxItem**.

[!code-cs[SetAudioEndpontSelectionChanged](./code/MediaPlayer_RS1/cs/MainPage.xaml.cs#SnippetSetAudioEndpontSelectionChanged)]

### <a name="playback-session"></a>Сеанс воспроизведения
Как описано ранее в этой статье, множество функций, которые предоставляются классом **MediaElement**, перенесены в класс [**MediaPlaybackSession**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Playback.MediaPlaybackSession). Сюда относятся сведения о состоянии воспроизведения проигрывателя, такие как текущая позиция воспроизведения, включение режима паузы проигрывателя, а также текущая скорость воспроизведения. **MediaPlaybackSession** также предоставляет ряд событий, предназначенных для уведомления о смене состояния, включая текущую загрузку буфера и процент загрузки воспроизводимого содержимого, а также фактический размер и соотношение сторон воспроизводимого сейчас видео.

В следующем примере показано, как реализовать обработчик нажатия кнопки, который перематывает содержимое на 10 секунд вперед. Сначала с помощью свойства [**PlaybackSession**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Playback.MediaPlayer.PlaybackSession) создается объект **MediaPlaybackSession** для проигрывателя. Затем свойству [**Position**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Playback.MediaPlaybackSession.Position) задается текущая позиция воспроизведения с добавлением 10 секунд.

[!code-cs[SkipForwardClick](./code/MediaPlayer_RS1/cs/MainPage.xaml.cs#SnippetSkipForwardClick)]

Следующий пример иллюстрирует использование кнопки-переключателя для выбора обычной или двукратной скорости воспроизведения путем изменения свойства [**PlaybackRate**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Playback.MediaPlaybackSession.PlaybackRate) этого сеанса.

[!code-cs[SpeedChecked](./code/MediaPlayer_RS1/cs/MainPage.xaml.cs#SnippetSpeedChecked)]

Начиная с Windows 10 версии 1803 можно настроить поворот видео в **MediaPlayer** с шагом 90 градусов.

[!code-cs[SetRotation](./code/MediaPlayer_RS1/cs/MainPage.xaml.cs#SnippetSetRotation)]

### <a name="detect-expected-and-unexpected-buffering"></a>Обнаружение ожидаемой и непредвиденной буферизации
Объект **MediaPlaybackSession**, описанный в предыдущем разделе предоставляет два события для обнаружения момента начала и окончания буферизации проигрываемого в данный момент файла мультимедиа: **[BufferingStarted](https://docs.microsoft.com/uwp/api/windows.media.playback.mediaplaybacksession.BufferingStarted)** и **[BufferingEnded](https://docs.microsoft.com/uwp/api/windows.media.playback.mediaplaybacksession.BufferingEnded)**. Это позволяет показывать в пользовательском интерфейсе состояние буферизации. Момент начала буферизации ожидается при первом открытии файла мультимедиа, а также при включении пользователем нового элемента в списке воспроизведения. Непредвиденная буферизации может произойти при ухудшения скорости сетевой передачи или при возникновении технических сбоев в системе управления содержимым. Начиная с RS3, можно использовать событие **BufferingStarted**, чтобы определить, является событие буферизации ожидаемым или непредвиденным, и прерывает ли оно воспроизведение. Эти сведения можно использовать в качестве данных телеметрии для вашего приложения или службы доставки содержимого. 

Зарегистрируйте обработчики для событий **BufferingStarted** и **BufferingEnded**, чтобы получать уведомления о состояния буферизации.

[!code-cs[RegisterBufferingHandlers](./code/MediaPlayer_RS1/cs/MainPage.xaml.cs#SnippetRegisterBufferingHandlers)]

В обработчике события **BufferingStarted** приведите аргументы, переданных событию, к типу **[MediaPlaybackSessionBufferingStartedEventArgs](https://docs.microsoft.com/uwp/api/windows.media.playback.mediaplaybacksessionbufferingstartedeventargs)** и проверьте свойство **[IsPlaybackInterruption](https://docs.microsoft.com/uwp/api/windows.media.playback.mediaplaybacksessionbufferingstartedeventargs.IsPlaybackInterruption)**. Если это значение равно true, то буферизация, ставшее причиной события, является непредвиденным и прерывает воспроизведение. В противном случае — это ожидаемая буферизация. 

[!code-cs[BufferingHandlers](./code/MediaPlayer_RS1/cs/MainPage.xaml.cs#SnippetBufferingHandlers)]


### <a name="pinch-and-zoom-video"></a>Сжатие и растяжение видео
**MediaPlayer** позволяет определить в видеосодержимом прямоугольник источника, который подлежит отрисовке, что позволяет увеличивать определенную область видеоизображения. Прямоугольник определяется относительно стандартного прямоугольника (0,0,1,1), где 0,0 соответствует верхнему левому углу кадра, а 1,1 задает полную ширину и высоту кадра. Например, чтобы установить прямоугольник увеличения в верхнюю правую часть отображаемого видео, необходимо задать прямоугольник (.5,0,.5,.5).  Важно проверить устанавливаемые значения, чтобы ваш прямоугольник источника находился в пределах (0,0,1,1) стандартного прямоугольника. Попытка установить значения за пределами этого диапазона приведет к возникновению исключения.

Для реализации сжатия и растяжения с помощью мультисенсорных жестов сначала нужно определить, какие жесты вы хотите поддерживать. В этом примере запрашиваются жесты масштабирования и перехода. Событие [**ManipulationDelta**](https://msdn.microsoft.com/library/windows/apps/Windows.UI.Xaml.UIElement.ManipulationDelta) возникает, когда выполняется один из запрошенных жестов. Событие [**DoubleTapped**](https://msdn.microsoft.com/library/windows/apps/Windows.UI.Xaml.UIElement.DoubleTapped) будет использоваться для сброса масштабирования до полного кадра. 

[!code-cs[RegisterPinchZoomEvents](./code/MediaPlayer_RS1/cs/MainPage.xaml.cs#SnippetRegisterPinchZoomEvents)]

Затем создайте объект **Rect**, который будет хранить текущий прямоугольник источника для масштабирования.

[!code-cs[DeclareSourceRect](./code/MediaPlayer_RS1/cs/MainPage.xaml.cs#SnippetDeclareSourceRect)]

Обработчик **ManipulationDelta** подстраивает либо масштаб, либо перемещение прямоугольника масштабирования. Если значение изменения масштаба не равно 1, это означает, что пользователь выполнил жест сжатия. Если значение больше 1, прямоугольник источника необходимо уменьшить для увеличения содержимого. Если значение меньше 1, то прямоугольник источника необходимо сделать больше для уменьшения масштаба. Перед установкой новых значений масштабирования проверятся целевой прямоугольник, чтобы он находится в пределах (0,0,1,1).

Если значение масштабирования равно 1, обрабатывается жест перемещения. Прямоугольник просто перемещается на количество пикселей в жесте, разделенное на ширину и высоту элемента управления. И снова образуемый прямоугольник проверяется на предмет полного размещения в границах (0,0,1.1).

Наконец, свойству [**NormalizedSourceRect**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Playback.MediaPlaybackSession.NormalizedSourceRect) класса **MediaPlaybackSession** задается только что подогнанный прямоугольник, определяя область видеокадра, которую необходимо отобразить.

[!code-cs[ManipulationDelta](./code/MediaPlayer_RS1/cs/MainPage.xaml.cs#SnippetManipulationDelta)]

В обработчике событий [**DoubleTapped**](https://msdn.microsoft.com/library/windows/apps/Windows.UI.Xaml.UIElement.DoubleTapped) прямоугольник источника возвращается к значениям (0,0,1,1), что приводит к отрисовке всего видеокадра.

[!code-cs[DoubleTapped](./code/MediaPlayer_RS1/cs/MainPage.xaml.cs#SnippetDoubleTapped)]

### <a name="handling-policy-based-playback-degradation"></a>Обработка снижения качества воспроизведения на основе политик

В некоторых случаях система уменьшить качество воспроизведения элемента мультимедиа, например, уменьшив разрешение (ограничением) на основе заданной политики, а не из-за проблемы с производительностью. Например, качество видео может снижено системой, если воспроизведение выполняется с использованием неподписанного видеодрайвера. Вы можете вызвать метод [**MediaPlaybackSession.GetOutputDegradationPolicyState**](https://docs.microsoft.com/uwp/api/windows.media.playback.mediaplaybacksession.getoutputdegradationpolicystate#Windows_Media_Playback_MediaPlaybackSession_GetOutputDegradationPolicyState), чтобы определить выполняется ли ухудшение качества на основе политики и почему это происходит, после чего уведомить пользователя или записать причину этого события для сбора телеметрических данных.

В следующем примере показана реализация обработчика события **MediaPlayer.MediaOpened**, которое возникает при открытии нового элемента мультимедиа в средстве воспроизведения. Вызывается метод **GetOutputDegradationPolicyState** переданного в обработчик объекта **MediaPlayer**. Значение [**VideoConstrictionReason**](https://docs.microsoft.com/uwp/api/windows.media.playback.mediaplaybacksessionoutputdegradationpolicystate.videoconstrictionreason#Windows_Media_Playback_MediaPlaybackSessionOutputDegradationPolicyState_VideoConstrictionReason) — это причину политики, из-за которой было ограничено воспроизведение видео. Если значение не равно **None**, то этот пример записывает причину снижения качества для сбора телеметрических данных. В этом примере также показана установка скорости воспроизводимого объекта **AdaptiveMediaSource** до наименьшего значения в целях экономии трафика, так как видео ограничено и в любом случае не будет воспроизводится в высоком разрешении. Дополнительные сведения об использовании **AdaptiveMediaSource** см. в разделе [Адаптивная потоковая передача](adaptive-streaming.md).

[!code-cs[PolicyDegradation](./code/MediaPlayer_RS1/cs/MainPage.xaml.cs#SnippetPolicyDegradation)]
        
## <a name="use-mediaplayersurface-to-render-video-to-a-windowsuicomposition-surface"></a>Использование MediaPlayerSurface для отрисовки видео на поверхности Windows.UI.Composition
Начиная c Windows 10 версии 1607, можно использовать **MediaPlayer** для отрисовки видео на [**ICompositionSurface**](https://msdn.microsoft.com/library/windows/apps/Windows.UI.Composition.ICompositionSurface), что позволяет проигрывателю взаимодействовать с интерфейсами API в пространстве имен [**Windows.UI.Composition**](https://msdn.microsoft.com/library/windows/apps/Windows.UI.Composition). Среда построения позволяет работать с изображениями на визуальном уровне между XAML и низкоуровневыми API графики DirectX. Это делает возможным реализацию таких сценариев, как отрисовка видео в любом элементе управления XAML. Подробные сведения об использовании API построения см. в разделе [Визуальный уровень](https://msdn.microsoft.com/windows/uwp/composition/visual-layer).

В следующем примере показано, как отрисовывать содержимое видеопроигрывателя на элементе управления [**Canvas**](https://msdn.microsoft.com/library/windows/apps/Windows.UI.Xaml.Controls.Canvas). В этом примере используются следующие характерные для проигрывателя мультимедиа вызовы: [**SetSurfaceSize**](https://msdn.microsoft.com/library/windows/apps/mt489968) и [**GetSurface**](https://msdn.microsoft.com/library/windows/apps/mt489963). **SetSurfaceSize** сообщает системе размер буфера, который необходимо выделить для отрисовки содержимого. **GetSurface** принимает [**Compositor**](https://msdn.microsoft.com/library/windows/apps/Windows.UI.Composition.Compositor) в качестве аргумента и запрашивает экземпляр класса [**MediaPlayerSurface**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Playback.MediaPlayerSurface). Этот класс предоставляет доступ к объектам **MediaPlayer** и **Compositor**, используемым для создания поверхности, и предоставляет саму поверхность с помощью свойства [**CompositionSurface**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Playback.MediaPlayerSurface.CompositionSurface).

Остаток кода в этом примере создает объект [**SpriteVisual**](https://msdn.microsoft.com/library/windows/apps/Windows.UI.Composition.SpriteVisual), на котором отрисовывается видео, и устанавливает размер в соответствии с размером элемента холста, который будет отображать визуальный элемент. Затем из [**MediaPlayerSurface**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Playback.MediaPlayerSurface) создается [**CompositionBrush**](https://msdn.microsoft.com/library/windows/apps/Windows.UI.Composition.CompositionBrush) и назначается свойству [**Brush**](https://msdn.microsoft.com/library/windows/apps/Windows.UI.Composition.SpriteVisual.Brush) визуального элемента. Затем создается объект [**ContainerVisual**](https://msdn.microsoft.com/library/windows/apps/Windows.UI.Composition.ContainerVisual), и **SpriteVisual** вставляется в верхней части его визуального дерева. Наконец, вызывается [**SetElementChildVisual**](https://msdn.microsoft.com/library/windows/apps/mt608981) для назначения визуального элемента контейнера объекту **Canvas**.

[!code-cs[Compositor](./code/MediaPlayer_RS1/cs/MainPage.xaml.cs#SnippetCompositor)]
        
## <a name="use-mediatimelinecontroller-to-synchronize-content-across-multiple-players"></a>Используйте MediaTimelineController для синхронизации содержимого в нескольких проигрывателях.
Как отмечено ранее в этой статье, ваше приложение может иметь несколько активных объектов **MediaPlayer** одновременно. По умолчанию каждый создаваемый вами объект **MediaPlayer** работает независимо. В некоторых случаях, например при синхронизации дорожки комментария с видео, может потребоваться синхронизировать состояние, положение воспроизведения и скорость воспроизведения нескольких проигрывателей. Начиная c Windows 10 версии 1607, можно реализовать это поведение с помощью класса [**MediaTimelineController**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.MediaTimelineController).

### <a name="implement-playback-controls"></a>Реализация элементов управления воспроизведением
В следующем примере показано, как использовать **MediaTimelineController** для управления двумя экземплярами **MediaPlayer**. Во-первых, создается каждый экземпляр **MediaPlayer**, а в качестве **Source** задается файл мультимедиа. После этого создается новый **MediaTimelineController**. Для каждого **MediaPlayer** отключается соответствующий ему [**MediaPlaybackCommandManager**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Playback.MediaPlaybackCommandManager) путем установки значения false для свойства [**IsEnabled**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Playback.MediaPlaybackCommandManager.IsEnabled). Затем в свойстве [**TimelineController**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Playback.MediaPlayer.TimelineController) указывается контроллер временной шкалы.

[!code-cs[DeclareMediaTimelineController](./code/MediaPlayer_RS1/cs/MainPage.xaml.cs#SnippetDeclareMediaTimelineController)]

[!code-cs[SetTimelineController](./code/MediaPlayer_RS1/cs/MainPage.xaml.cs#SnippetSetTimelineController)]

**Внимание!** [**MediaPlaybackCommandManager**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Playback.MediaPlaybackCommandManager) обеспечивает автоматическую интеграцию **MediaPlayer** и системных элементов управления транспортом мультимедиа (SMTC), однако эта автоматическая интеграция не может использоваться с проигрывателями мультимедиа, которыми управляет **MediaTimelineController**. Поэтому перед установкой контроллера временной шкалы проигрывателя необходимо отключить менеджер команд для проигрывателя мультимедиа. Невыполнение этого условия приведет к созданию исключения со следующим сообщением: "Attaching Media Timeline Controller is blocked because of the current state of the object" (Прикрепление контроллера временной шкалы мультимедиа заблокировано в связи с текущим состоянием объекта). Подробные сведения об интеграции проигрывателя с SMTC см. в разделе [Интеграция с системными элементами управления транспортировкой мультимедиа](integrate-with-systemmediatransportcontrols.md). Если вы используете **MediaTimelineController**, управление SMTC по-прежнему можно осуществлять вручную. Дополнительные сведения см. в разделе [Ручное управление системными элементами управления воспроизведением мультимедиа](system-media-transport-controls.md).

После прикрепления **MediaTimelineController** к одному или нескольким проигрывателям мультимедиа управлять состоянием воспроизведения можно с помощью методов, предоставляемых контроллером. В следующем примере производится вызов [**Start**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.MediaTimelineController.Start), чтобы начать воспроизведение с начала мультимедийного содержимого на всех связанных проигрывателях.

[!code-cs[PlayButtonClick](./code/MediaPlayer_RS1/cs/MainPage.xaml.cs#SnippetPlayButtonClick)]

Этот пример иллюстрирует приостановку и возобновление воспроизведения на всех подключенных проигрывателях.

[!code-cs[PauseButtonClick](./code/MediaPlayer_RS1/cs/MainPage.xaml.cs#SnippetPauseButtonClick)]

Чтобы выполнить перемотку вперед на всех подключенных проигрывателях, задайте скорость воспроизведения выше 1.

[!code-cs[FastForwardButtonClick](./code/MediaPlayer_RS1/cs/MainPage.xaml.cs#SnippetFastForwardButtonClick)]

В следующем примере показано, как использовать элемент управления **Slider**, чтобы отображать текущую позицию воспроизведения для контроллера временной шкалы относительно длительности содержимого одного из подключенных проигрывателей мультимедиа. Сначала создается новый объект **MediaSource** и регистрируется обработчик для события [**OpenOperationCompleted**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Core.MediaSource.OpenOperationCompleted) источника мультимедиа. 

[!code-cs[CreateSourceWithOpenCompleted](./code/MediaPlayer_RS1/cs/MainPage.xaml.cs#SnippetCreateSourceWithOpenCompleted)]

Обработчик **OpenOperationCompleted** используется как возможность для определения продолжительности содержимого источника мультимедиа. После определения длительности максимальное значение элемента управления **Slider** устанавливается равным общему числу секунд в элементе мультимедиа. Значение задается внутри вызова [**RunAsync**](https://msdn.microsoft.com/library/windows/apps/hh750317), чтобы обеспечить выполнение в потоке пользовательского интерфейса.

[!code-cs[DeclareDuration](./code/MediaPlayer_RS1/cs/MainPage.xaml.cs#SnippetDeclareDuration)]

[!code-cs[OpenCompleted](./code/MediaPlayer_RS1/cs/MainPage.xaml.cs#SnippetOpenCompleted)]

Далее регистрируется обработчик для события [**PositionChanged**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.MediaTimelineController.PositionChanged) контроллера временной шкалы. Этот вызов производится системой периодически, примерно 4 раза в секунду.

[!code-cs[RegisterPositionChanged](./code/MediaPlayer_RS1/cs/MainPage.xaml.cs#SnippetRegisterPositionChanged)]

В обработчике **PositionChanged** значение ползунка обновляется для отражения текущей позиции контроллера временной шкалы.

[!code-cs[PositionChanged](./code/MediaPlayer_RS1/cs/MainPage.xaml.cs#SnippetPositionChanged)]

### <a name="offset-the-playback-position-from-the-timeline-position"></a>Смещение позиции воспроизведения относительно позиции временной шкалы
В некоторых случаях может потребоваться смещение позиции воспроизведения одного или нескольких проигрывателей, связанных с контроллером временной шкалы, относительно других проигрывателей. Этого можно достичь, задав свойство [**TimelineControllerPositionOffset**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Playback.MediaPlayer.TimelineControllerPositionOffset) объекта **MediaPlayer**, для которого требуется смещение. В следующем примере используются значения длительности содержимого двух проигрывателей мультимедиа для установки минимального и максимального значений двух ползунков управления в соответствии с длительностью элемента мультимедиа в отрицательном и положительном направлениях.  

[!code-cs[OffsetSliders](./code/MediaPlayer_RS1/cs/MainPage.xaml.cs#SnippetOffsetSliders)]

В событии [**ValueChanged**](https://msdn.microsoft.com/library/windows/apps/Windows.UI.Xaml.Controls.Primitives.RangeBase.ValueChanged) для каждого ползунка свойству **TimelineControllerPositionOffset** каждого проигрывателя задается соответствующее значение.

[!code-cs[TimelineOffset](./code/MediaPlayer_RS1/cs/MainPage.xaml.cs#SnippetTimelineOffset)]

Обратите внимание, что если значение смещения проигрывателя попадает на отрицательную позицию воспроизведения, содержимое останется в режиме паузы до достижения нулевого смещения, а затем начнется воспроизведение. Аналогичным образом, если значение смещения попадает на позицию воспроизведения, превышающую продолжительность элемента мультимедиа, будет отображаться последний кадр, как в случае достижения одним проигрывателем конца содержимого.

## <a name="play-spherical-video-with-mediaplayer"></a>Воспроизведение сферической проекции видео с помощью MediaPlayer
Начиная с Windows 10 версии 1703, **MediaPlayer** поддерживает равноугольную проекцию для сферического воспроизведения видео. Видео в сферической проекции ничем не отличается от обычного плоского видео в том плане, что **MediaPlayer** выполняет отрисовку видео, если поддерживается кодирование. Если сферическое видео содержит тег метаданных, который указывает, что для видео используется равноугольная проекция, **MediaPlayer** может выполнить отрисовку с учетом указанного поля зрения и ориентации представления. Это позволяет реализовывать такие сценарии, как воспроизведение видео в виртуальной реальности в видеошлеме или панорамирование сферического видео с помощью мыши или клавиатуры.

Чтобы воспроизвести сферическое видео, выполните инструкции по воспроизведению видео, приведенные ранее в этой статье. Дополнительный шаг состоит в регистрации обработчика для события [**MediaPlayer.MediaOpened**])https://docs.microsoft.com/uwp/api/Windows.Media.Playback.MediaPlayer#Windows_Media_Playback_MediaPlayer_MediaOpened). Это событие позволяет включать параметры воспроизведения сферического видео и управлять ими.

[!code-cs[OpenSphericalVideo](./code/MediaPlayer_RS1/cs/MainPage.xaml.cs#SnippetOpenSphericalVideo)]

В обработчике **MediaOpened** в первую очередь необходимо проверить формат кадра недавно открытого мультимедийного элемента с помощью свойства [**PlaybackSession.SphericalVideoProjection.FrameFormat**](https://docs.microsoft.com/uwp/api/windows.media.playback.mediaplaybacksphericalvideoprojection.FrameFormat). Если значение равно [**SphericaVideoFrameFormat.Equirectangular**](https://docs.microsoft.com/uwp/api/windows.media.mediaproperties.sphericalvideoframeformat), система может автоматически проецировать видео. Сначала задайте для свойства [**PlaybackSession.SphericalVideoProjection.IsEnabled**](https://docs.microsoft.com/uwp/api/windows.media.playback.mediaplaybacksphericalvideoprojection.IsEnabled) значение **true**. Также можно настроить другие свойства, например ориентацию представления и поле зрения, которые проигрыватель мультимедиа будет использовать при проецировании видео. В этом примере для поля зрения задано значение 120 градусов с помощью свойства [**HorizontalFieldOfViewInDegrees**](https://docs.microsoft.com/uwp/api/windows.media.playback.mediaplaybacksphericalvideoprojection.HorizontalFieldOfViewInDegrees).

Если видео сферическое, но формат проекции отличается от равноугольного, можно реализовать собственный алгоритм проецирования в режиме сервера кадров. Этот режим позволяет проигрывателю мультимедиа получать и обрабатывать отдельные кадры.

[!code-cs[SphericalMediaOpened](./code/MediaPlayer_RS1/cs/MainPage.xaml.cs#SnippetSphericalMediaOpened)]

В следующем примере кода показано, как изменить ориентацию представления для сферического видео с помощью клавиш со стрелками влево и вправо.

[!code-cs[SphericalOnKeyDown](./code/MediaPlayer_RS1/cs/MainPage.xaml.cs#SnippetSphericalOnKeyDown)]

Если ваше приложение поддерживает списки воспроизведения видео, в пользовательском интерфейсе можно определить элементы воспроизведения, содержащие сферическое видео. Списки воспроизведения мультимедиа подробно описаны в статье, [Элементы, списки воспроизведения и звуковые дорожки мультимедиа](media-playback-with-mediasource.md). В следующем примере показано, как создать новый список воспроизведения, добавить элемент и зарегистрировать обработчик для события  [**MediaPlaybackItem.VideoTracksChanged**](https://docs.microsoft.com/uwp/api/windows.media.playback.mediaplaybackitem.VideoTracksChanged), которое возникает во время разрешения видеодорожек для элемента мультимедиа.

[!code-cs[SphericalList](./code/MediaPlayer_RS1/cs/MainPage.xaml.cs#SnippetSphericalList)]

В обработчике события **VideoTracksChanged** получите свойства кодирования всех добавленных видеодорожек, вызвав метод [**VideoTrack.GetEncodingProperties**](https://docs.microsoft.com/uwp/api/windows.media.core.videotrack.GetEncodingProperties). Если значение свойства кодирования [**SphericalVideoFrameFormat**](https://docs.microsoft.com/uwp/api/windows.media.mediaproperties.videoencodingproperties.SphericalVideoFrameFormat) отличается от [**SphericaVideoFrameFormat.None**](https://docs.microsoft.com/uwp/api/windows.media.mediaproperties.sphericalvideoframeformat), то видеодорожка содержит сферическое видео. При необходимости можно внести соответствующие изменения в пользовательский интерфейс.

[!code-cs[SphericalTracksChanged](./code/MediaPlayer_RS1/cs/MainPage.xaml.cs#SnippetSphericalTracksChanged)]

## <a name="use-mediaplayer-in-frame-server-mode"></a>Использование MediaPlayer в режиме сервера кадров
Начиная с Windows 10 версии 1703, можно использовать **MediaPlayer** в режиме сервера кадров. В этом режиме **MediaPlayer** не выполняет автоматическую отрисовку кадров для соответствующего элемента **MediaPlayerElement**. Вместо этого ваше приложение копирует текущий кадр из **MediaPlayer** в объект, реализующий [**IDirect3DSurface**](https://docs.microsoft.com/uwp/api/windows.graphics.directx.direct3d11.idirect3dsurface). Основной сценарий, который реализует эта функция, заключается обработке видеокадров, предоставляемых **MediaPlayer**, с помощью построителей текстуры. Ваше приложение отвечает за отображение каждого кадра после обработки, например отображение кадра в элементе управления XAML [**Image**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.image).

В следующем примере инициализируется новый **MediaPlayer** и загружается видео. Затем регистрируется обработчик для события [**VideoFrameAvailable**](https://docs.microsoft.com/uwp/api/windows.media.playback.mediaplayer.VideoFrameAvailable). Чтобы включить режим сервера кадров, задайте для свойства объекта **MediaPlayer** [**IsVideoFrameServerEnabled**](https://docs.microsoft.com/uwp/api/windows.media.playback.mediaplayer.IsVideoFrameServerEnabled) значение **true**. Наконец, начните воспроизведение мультимедиа, вызвав метод [**Play**](https://docs.microsoft.com/uwp/api/windows.media.playback.mediaplayer.Play).

[!code-cs[FrameServerInit](./code/MediaPlayer_RS1/cs/MainPage.xaml.cs#SnippetFrameServerInit)]

В следующем примере показан обработчик для события **VideoFrameAvailable**, использующий [Win2D](https://github.com/Microsoft/Win2D) для добавления простого эффекта размытия в каждый кадр видео. Затем обработанные кадры отображаются в элементе управления XAML [Image](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.image).

Каждый раз, когда вызывается обработчик события **VideoFrameAvailable**, метод [**CopyFrameToVideoSurface**](https://docs.microsoft.com/uwp/api/windows.media.playback.mediaplayer.copyframetovideosurface) используется для копирования содержимого кадра в [**IDirect3DSurface**](https://docs.microsoft.com/uwp/api/windows.graphics.directx.direct3d11.idirect3dsurface). Также можно использовать [**CopyFrameToStereoscopicVideoSurfaces**](https://docs.microsoft.com/uwp/api/windows.media.playback.mediaplayer.copyframetostereoscopicvideosurfaces) для копирования трехмерного содержимого в две плоскости, с отдельной обработкой данных для левого и правого глаза. Для получения объекта, реализующего **IDirect3DSurface**, в этом примере создается объект [**SoftwareBitmap**](https://docs.microsoft.com/uwp/api/windows.graphics.imaging.softwarebitmap). Этот объект впоследствии используется для создания объекта Win2D **CanvasBitmap**, реализующего необходимый интерфейс. **CanvasImageSource**— это объект Win2D, который можно использовать в качестве источника для элемента управления **Image**, поэтому создается новый объект и указывается в качестве источника для элемента управления **Image**, в котором отображается содержимое. Далее создается **CanvasDrawingSession**. Win2D использует этот объект для отрисовки эффекта размытия.

После создания экземпляров всех необходимых объектов вызывается метод **CopyFrameToVideoSurface**, который копирует текущий кадр из **MediaPlayer** в **CanvasBitmap**. Затем создается объект Win2D **GaussianBlurEffect**, а **CanvasBitmap** указывается в качестве источника операции. Наконец, вызывается функция **CanvasDrawingSession.DrawImage** для рисования исходного изображения с примененным эффектом размытия. Это изображение передается в **CanvasImageSource**, связанный с элементом управления **Image**, и отрисовывается в пользовательском интерфейсе.

[!code-cs[VideoFrameAvailable](./code/MediaPlayer_RS1/cs/MainPage.xaml.cs#SnippetVideoFrameAvailable)]

Дополнительные сведения об использовании Win2D см. в [репозитории Win2D в GitHub](https://github.com/Microsoft/Win2D). Чтобы протестировать приведенный выше пример кода, необходимо добавить в проект пакет Win2D NuGet (см. инструкции ниже).

**Добавление пакета Win2D NuGet в проект эффекта**

1.  В **обозревателе решений** щелкните правой кнопкой мыши проект и выберите **Управление пакетами NuGet**.
2.  В верхней части окна выберите вкладку **Обзор**.
3.  В поле поиска введите **Win2D**.
4.  Выберите **Win2D.uwp**, а затем— **Установить** в области справа.
5.  В диалоговом окне **Просмотр изменений** отобразится назначенный для установки пакет. Нажмите кнопку **ОК**.
6.  Примите условия лицензии пакета.

## <a name="detect-and-respond-to-audio-level-changes-by-the-system"></a>Обнаружение изменения громкости звука системой и реагирование на это событие
Начиная с Windows 10 версии 1803, ваше приложение может определить момент, когда система уменьшает громкость звука или отключает его в **MediaPlayer**, который осуществляет воспроизведение содержимого. Например, система может снизить уровень звука, если включается будильник. Система отключает звук приложения, когда оно переходит в фоновый режим, если приложение не объявило возможность *backgroundMediaPlayback* в манифесте приложения. В классе [**AudioStateMonitor**](https://docs.microsoft.comuwp/api/windows.media.audio.audiostatemonitor) можно зарегистрировать обработчик события, отправляемого когда система изменяет громкость звукового потока. Обратитесь к свойству **AudioStateMonitor** объекта **MediaPlayer** и зарегистрируйте обработчик события [**SoundLevelChanged**](https://docs.microsoft.com/uwp/api/windows.media.audio.audiostatemonitor.soundlevelchanged), чтобы получить уведомление про изменение системой громкости звука для **MediaPlayer**.

[!code-cs[RegisterAudioStateMonitor](./code/MediaPlayer_RS1/cs/MainPage.xaml.cs#SnippetRegisterAudioStateMonitor)]

При обработке события **SoundLevelChanged**, вы можете выполнить различные в зависимости от типа воспроизводимого содержимого. Если в данный момент воспроизводится музыка, то, возможно, стоит не прерывать ее воспроизведение на время отключения звука. Однако, если идет воспроизведение подкаста, то, пожалуй, стоит приостановить процесс на время отключения звука, чтобы пользователь не пропустил часть прослушиваемого материала.

В этом примере объявляется переменная, в которой отслеживается не воспроизводится ли сейчас подкаст. Предполагается, что вы будете задавать правильное значение переменной при выборе содержимого для **MediaPlayer**. Мы также сделали переменную класса, чтобы отслеживать момент программной приостановки воспроизведения после изменения громкости звука.

[!code-cs[AudioStateVars](./code/MediaPlayer_RS1/cs/MainPage.xaml.cs#SnippetAudioStateVars)]

В обработчике события **SoundLevelChanged** проверьте свойство [**SoundLevel**](https://docs.microsoft.com/uwp/api/windows.media.audio.audiostatemonitor.soundlevel) отправителя **AudioStateMonitor** и выясните каков новый уровень громкости. В этом примере проверяется, выставлен ли полный уровень громкости. Это означает, что система отменила выключение звука или уровень громкости был уменьшен, но идет воспроизведение не подкаста. Если любое из этих условий верно, а содержимое ранее было приостановлено программным способом, то воспроизведение возобновляется. Если звук был отключен, или если воспроизводится подкаст, и задана низкий уровень громкости, то воспроизведение приостанавливается и в переменной указывается, что пауза произведена программным способом.

[!code-cs[SoundLevelChanged](./code/MediaPlayer_RS1/cs/MainPage.xaml.cs#SnippetSoundLevelChanged)]

Пользователь может выбрать — приостановить или продолжить воспроизведение, даже если звук был приглушен системой. В этом примере показаны обработчики событий для кнопок воспроизведения и паузы. В обработчике нажатия кнопки паузы происходит следующее: если воспроизведение уже была приостановлено программно, то мы меняем значение переменной, чтобы указать, что это пользователь приостановил воспроизведение. В обработчике нажатия кнопки воспроизведения, мы возобновляем процесс и сбрасываем значение переменной.

[!code-cs[ButtonUserClick](./code/MediaPlayer_RS1/cs/MainPage.xaml.cs#SnippetButtonUserClick)]

## <a name="related-topics"></a>Статьи по теме
* [Воспроизведение мультимедиа](media-playback.md)
* [Элементы, плей-листы и звуковые дорожки мультимедиа](media-playback-with-mediasource.md)
* [Интеграция с системными элементами управления транспортировкой мультимедиа](integrate-with-systemmediatransportcontrols.md)
* [Создание, планирование и управление перерывами при воспроизведении мультимедиа](create-schedule-and-manage-media-breaks.md)
* [Воспроизведение мультимедиа в фоновом режиме](background-audio.md)





 

 




