---
ms.assetid: C4DB495D-1F91-40EF-A55C-5CABBF3269A2
description: API-интерфейсы в пространстве имен Windows.Media.Editing позволяют быстро разрабатывать приложения, которые дают пользователям возможность создавать композиции мультимедиа из исходных аудио- и видеофайлов.
title: Создание и редактирование мультимедийных композиций
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 4054f1f4ce4db7f158c1297b748ecea8cab83602
ms.sourcegitcommit: ac7f3422f8d83618f9b6b5615a37f8e5c115b3c4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/29/2019
ms.locfileid: "66360739"
---
# <a name="media-compositions-and-editing"></a>Создание и редактирование мультимедийных композиций



В данной статье описывается, как использовать API-интерфейсы в пространстве имен [**Windows.Media.Editing**](https://docs.microsoft.com/uwp/api/Windows.Media.Editing) для быстрой разработки приложений, которые дают пользователям возможность создавать композиции мультимедиа из исходных аудио- и видеофайлов. Компоненты платформы включают возможность программного соединения нескольких видеоклипов, добавление фото- и видеоналожений, добавление фоновых аудиодорожек и применение звуковых и видеоэффектов. Созданные композиции мультимедиа можно преобразовать в неструктурируемый файл мультимедиа для воспроизведения или совместного использования. Кроме того, их можно сериализовать на диск или десериализовать с него, что позволяет пользователю загружать и изменять ранее созданные композиции. Все эти функции предоставляются в интерфейсе среды выполнения Windows, который прост в использовании, а также снижает объем и сложность кода, требуемого для выполнения этих задач, по сравнению с низкоуровневым API-интерфейсом [Microsoft Media Foundation](https://docs.microsoft.com/windows/desktop/medfound/microsoft-media-foundation-sdk) .

## <a name="create-a-new-media-composition"></a>Создание композиции мультимедиа

Класс [**MediaComposition**](https://docs.microsoft.com/uwp/api/Windows.Media.Editing.MediaComposition) — это контейнер для всех клипов мультимедиа, входящих в композицию, который отвечает за окончательную композицию, загрузку с диска и сохранение на диск и предоставляет возможность выполнения предварительной потоковой передачи композиции для ее просмотра в пользовательском интерфейсе. Чтобы использовать **MediaComposition** в своем приложении, добавьте пространство имен [**Windows.Media.Editing**](https://docs.microsoft.com/uwp/api/Windows.Media.Editing), а также [**Windows.Media.Core**](https://docs.microsoft.com/uwp/api/Windows.Media.Core), предоставляющее связанные API-интерфейсы, которые могут вам понадобиться.

[!code-cs[Namespace1](./code/MediaEditing/cs/MainPage.xaml.cs#SnippetNamespace1)]


Получить доступ к объекту **MediaComposition** можно из нескольких точек вашего кода, поэтому вы будете объявлять переменную-член, где он хранится.

[!code-cs[DeclareMediaComposition](./code/MediaEditing/cs/MainPage.xaml.cs#SnippetDeclareMediaComposition)]

Конструктор для **MediaComposition** не принимает никаких аргументов.

[!code-cs[MediaCompositionConstructor](./code/MediaEditing/cs/MainPage.xaml.cs#SnippetMediaCompositionConstructor)]

## <a name="add-media-clips-to-a-composition"></a>Добавление в композицию клипов мультимедиа

Обычно композиции мультимедиа содержат один или несколько видеоклипов. Вы можете использовать [**FileOpenPicker**](https://docs.microsoft.com/uwp/schemas/appxpackage/appxmanifestschema/element-fileopenpicker), чтобы позволить пользователям выбирать видеофайл. После выбора файла создайте новый объект [**MediaClip**](https://docs.microsoft.com/uwp/api/Windows.Media.Editing.MediaClip), в котором будет содержаться видеоклип, вызвав [**MediaClip.CreateFromFileAsync**](https://docs.microsoft.com/uwp/api/windows.media.editing.mediaclip.createfromfileasync). Затем добавьте клип в список [**Clips**](https://docs.microsoft.com/uwp/api/windows.media.editing.mediacomposition.clips) объекта **MediaComposition**.

[!code-cs[PickFileAndAddClip](./code/MediaEditing/cs/MainPage.xaml.cs#SnippetPickFileAndAddClip)]

-   Клипы мультимедиа появятся в **MediaComposition** в том же порядке, в каком они представлены в списке [**Clips**](https://docs.microsoft.com/uwp/api/windows.media.editing.mediacomposition.clips).

-   **MediaClip** можно включить в композицию только один раз. При попытке добавить **MediaClip**, который уже используется в композиции, произойдет ошибка. Для того чтобы использовать видеоклип в композиции несколько раз, вызовите метод [**Clone**](https://docs.microsoft.com/uwp/api/windows.media.editing.mediaclip.clone), позволяющий создать объект **MediaClip**, который затем можно добавить в композицию.

-   Универсальные приложения для Windows не имеют прав доступ ко всей файловой системе. Чтобы сохранить разрешения на доступ к файлу, свойство [**FutureAccessList**](https://docs.microsoft.com/uwp/api/windows.storage.accesscache.storageapplicationpermissions.futureaccesslist) класса [**StorageApplicationPermissions**](https://docs.microsoft.com/uwp/api/Windows.Storage.AccessCache.StorageApplicationPermissions) позволяет вашему приложению хранить запись файла, который был выбран пользователем. Максимальное число записей в **FutureAccessList** равно 1000, поэтому вашему приложению необходимо управлять этим списком, чтобы убедиться, что он не переполнен. Это особенно важно, если вы планируете поддерживать загрузку и изменение ранее созданных композиций.

-   **MediaComposition** поддерживает видеоклипы в формате MP4.

-   Если видеофайл содержит несколько внедренных аудиодорожек, вы можете выбрать, какая из них должна использоваться в композиции, настроив свойство [**SelectedEmbeddedAudioTrackIndex**](https://docs.microsoft.com/uwp/api/windows.media.editing.mediaclip.selectedembeddedaudiotrackindex).

-   Создайте **MediaClip** с одноцветным заполнением всего кадра, вызвав метод [**CreateFromColor**](https://docs.microsoft.com/uwp/api/windows.media.editing.mediaclip.createfromcolor) и указав цвет и длительность клипа.

-   Создайте **MediaClip** на основе файла изображения, вызвав метод [**CreateFromImageFileAsync**](https://docs.microsoft.com/uwp/api/windows.media.editing.mediaclip.createfromimagefileasync) и указав файл изображения и длительность клипа.

-   Создайте **MediaClip** на основе [**IDirect3DSurface**](https://docs.microsoft.com/uwp/api/Windows.Graphics.DirectX.Direct3D11.IDirect3DSurface), вызвав [**CreateFromSurface**](https://docs.microsoft.com/uwp/api/windows.media.editing.mediaclip.createfromsurface) и указав поверхность и длительность клипа.

## <a name="preview-the-composition-in-a-mediaelement"></a>Предварительный просмотр композиции в MediaElement

Чтобы пользователь мог просматривать композицию мультимедиа, добавьте класс [**MediaPlayerElement**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.MediaPlayerElement) в XAML-файл, определяющий ваш пользовательский интерфейс.

[!code-xml[MediaElement](./code/MediaEditing/cs/MainPage.xaml#SnippetMediaElement)]

Объявите переменную-член типа [**MediaStreamSource**](https://docs.microsoft.com/uwp/api/Windows.Media.Core.MediaStreamSource).


[!code-cs[DeclareMediaStreamSource](./code/MediaEditing/cs/MainPage.xaml.cs#SnippetDeclareMediaStreamSource)]

Вызовите метод [**GeneratePreviewMediaStreamSource**](https://docs.microsoft.com/uwp/api/windows.media.editing.mediacomposition.generatepreviewmediastreamsource) объекта **MediaComposition** для создания **MediaStreamSource** для композиции. Создайте объект [**MediaSource**](https://docs.microsoft.com/uwp/api/Windows.Media.Core.MediaSource), вызвав фабричный метод [**CreateFromMediaStreamSource**](https://docs.microsoft.com/uwp/api/windows.media.core.mediasource.createfrommediastreamsource), и назначьте его свойству [**Source**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.mediaplayerelement.source) элемента **MediaPlayerElement**. Теперь композицию можно просматривать в пользовательском интерфейсе.


[!code-cs[UpdateMediaElementSource](./code/MediaEditing/cs/MainPage.xaml.cs#SnippetUpdateMediaElementSource)]

-   Перед вызовом [**GeneratePreviewMediaStreamSource**](https://docs.microsoft.com/uwp/api/windows.media.editing.mediacomposition.generatepreviewmediastreamsource) член **MediaComposition** должен содержать по крайней мере один клип мультимедиа, иначе возвращенный объект будет пустым.

-   Временная шкала **MediaElement**, отражающая изменения в композиции, автоматически не обновляется. Рекомендуется вызвать оба **GeneratePreviewMediaStreamSource** и задайте **MediaPlayerElement** **источника** свойство каждый раз, чтобы внести ряд изменений композиции и необходимо обновить пользовательский Интерфейс.

Когда пользователь покидает страницу, рекомендуется присвоить объекту **MediaStreamSource** и свойству [**Source**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.mediaelement.source) элемента **MediaPlayerElement** значение NULL, чтобы освободить связанные ресурсы.

[!code-cs[OnNavigatedFrom](./code/MediaEditing/cs/MainPage.xaml.cs#SnippetOnNavigatedFrom)]

## <a name="render-the-composition-to-a-video-file"></a>Преобразование композиции в видеофайл

Чтобы преобразовать композицию мультимедиа в неструктурированный видеофайл, которым можно поделиться и который можно просматривать на других устройствах, необходимо использовать API-интерфейсы из пространства имен [**Windows.Media.Transcoding**](https://docs.microsoft.com/uwp/api/Windows.Media.Transcoding). Чтобы обновить интерфейс пользователя в ходе выполнения асинхронной операции, вам понадобятся API-интерфейсы из пространства имен [**Windows.UI.Core**](https://docs.microsoft.com/uwp/api/Windows.UI.Core).

[!code-cs[Namespace2](./code/MediaEditing/cs/MainPage.xaml.cs#SnippetNamespace2)]

После того как пользователь выбрал выходной файл с помощью [**FileSavePicker**](https://docs.microsoft.com/uwp/api/Windows.Storage.Pickers.FileSavePicker), преобразуйте композицию в этот файл, вызвав для объекта **MediaComposition** метод [**RenderToFileAsync**](https://docs.microsoft.com/uwp/api/windows.media.editing.mediacomposition.rendertofileasync). Остальная часть кода в следующем примере просто следует шаблону обработки [**AsyncOperationWithProgress**](https://docs.microsoft.com/previous-versions//br205807(v=vs.85)).

[!code-cs[RenderCompositionToFile](./code/MediaEditing/cs/MainPage.xaml.cs#SnippetRenderCompositionToFile)]

-   [  **MediaTrimmingPreference**](https://docs.microsoft.com/uwp/api/Windows.Media.Editing.MediaTrimmingPreference) позволяет указать приоритет скорости перекодировки в зависимости от точности обрезки соседних клипов мультимедиа. **Fast** приводит к более быстрой перекодировке с меньшей точностью обрезки, **Precise** — к более медленной перекодировке с более точной обрезкой.

## <a name="trim-a-video-clip"></a>Обрезка видеоклипа

Обрезать длительность видеоклипа в композиции можно за счет настройки объекта [**MediaClip**](https://docs.microsoft.com/uwp/api/Windows.Media.Editing.MediaClip) для свойства [**TrimTimeFromStart**](https://docs.microsoft.com/uwp/api/windows.media.editing.mediaclip.trimtimefromstart) или [**TrimTimeFromEnd**](https://docs.microsoft.com/uwp/api/windows.media.editing.mediaclip.trimtimefromend), либо для обоих.

[!code-cs[TrimClipBeforeCurrentPosition](./code/MediaEditing/cs/MainPage.xaml.cs#SnippetTrimClipBeforeCurrentPosition)]

-   Пользователь может указать значения точек начала и конца обрезки в любом пользовательском интерфейсе. В примере, приведенном выше, используется свойство [**Position**](https://docs.microsoft.com/uwp/api/windows.media.playback.mediaplaybacksession.position) члена [**MediaPlaybackSession**](https://docs.microsoft.com/uwp/api/Windows.Media.Playback.MediaPlaybackSession), связанного с **MediaPlayerElement** для определения объекта **MediaClip**, воспроизводимого в композиции в текущей позиции с помощью проверки [**StartTimeInComposition**](https://docs.microsoft.com/uwp/api/windows.media.editing.mediaclip.starttimeincomposition) и [**EndTimeInComposition**](https://docs.microsoft.com/uwp/api/windows.media.editing.mediaclip.endtimeincomposition). Затем свойства **Position** и **StartTimeInComposition** используются повторно для вычисления времени обрезки с начала клипа. **FirstOrDefault** — это расширенный метод из пространства имен **System.Linq**, который упрощает код для операции выбора элементов из списка.
-   Свойство [**OriginalDuration**](https://docs.microsoft.com/uwp/api/windows.media.editing.mediaclip.originalduration) объекта **MediaClip** позволяет узнать длительность клипа мультимедиа без применения обрезки.
-   Свойство [**TrimmedDuration**](https://docs.microsoft.com/uwp/api/windows.media.editing.mediaclip.trimmedduration) позволяет узнать длительность клипа мультимедиа после применения обрезки.
-   Указание значения обрезки, которое превышает исходную длительность клипа, не приводит к возникновению ошибки. Однако если композиция содержит только один клип, который обрезается до нулевой длины вследствие того, что указано большое значение обрезки, последующий вызов [**GeneratePreviewMediaStreamSource**](https://docs.microsoft.com/uwp/api/windows.media.editing.mediacomposition.generatepreviewmediastreamsource) вернет значение NULL, как если бы композиция не содержала ни одного клипа.

## <a name="add-a-background-audio-track-to-a-composition"></a>Добавление фоновой аудиодорожки в композицию

Чтобы добавить в композицию фоновую дорожку, загрузите аудиофайл, а затем создайте объект [**BackgroundAudioTrack**](https://docs.microsoft.com/uwp/api/Windows.Media.Editing.BackgroundAudioTrack), вызвав фабричный метод [**BackgroundAudioTrack.CreateFromFileAsync**](https://docs.microsoft.com/uwp/api/windows.media.editing.backgroundaudiotrack.createfromfileasync). Далее добавьте **BackgroundAudioTrack** в свойство [**BackgroundAudioTracks**](https://docs.microsoft.com/uwp/api/windows.media.editing.mediacomposition.backgroundaudiotracks) композиции.

[!code-cs[AddBackgroundAudioTrack](./code/MediaEditing/cs/MainPage.xaml.cs#SnippetAddBackgroundAudioTrack)]

-   Объект **MediaComposition** поддерживает фоновые звуковых дорожек в следующих форматах: MP3, WAV, FLAC

-   Фоновая аудиодорожка

-   Как и в случае с видеофайлом, для сохранения доступа к файлам в композиции следует использовать класс [**StorageApplicationPermissions**](https://docs.microsoft.com/uwp/api/Windows.Storage.AccessCache.StorageApplicationPermissions).

-   Как и в **MediaClip**, **BackgroundAudioTrack** можно включить в композицию только один раз. При попытке добавить объект **BackgroundAudioTrack**, который уже используется в композиции, произойдет ошибка. Для того чтобы использовать аудиодорожку в композиции несколько раз, вызовите метод [**Clone**](https://docs.microsoft.com/uwp/api/windows.media.editing.mediaclip.clone), позволяющий создать объекты **MediaClip**, которые затем можно добавить в композицию.

-   По умолчанию фоновые аудиодорожки начинают воспроизведение в начале композиции. Если присутствует несколько фоновых дорожек, все они будут воспроизводиться в начале композиции. Чтобы воспроизведение фоновой аудиодорожки начиналось в другое время, установите желаемое смещение времени в свойстве [**Delay**](https://docs.microsoft.com/uwp/api/windows.media.editing.backgroundaudiotrack.delay).

## <a name="add-an-overlay-to-a-composition"></a>Добавление наложения в композицию

Наложения позволяют размещать в композиции несколько слоев видео поверх друг друга. Композиция может содержать несколько слоев наложения, каждый из которых может включать несколько наложений. Создайте объект [**MediaOverlay**](https://docs.microsoft.com/uwp/api/Windows.Media.Editing.MediaOverlay), передав **MediaClip** в его конструктор. Установите положение и прозрачность наложения, а затем создайте новый объект [**MediaOverlayLayer**](https://docs.microsoft.com/uwp/api/Windows.Media.Editing.MediaOverlayLayer) и добавьте **MediaOverlay** в список [**Overlays**](https://docs.microsoft.com/windows/desktop/api/dxgi1_3/nf-dxgi1_3-idxgioutput2-supportsoverlays). Наконец, добавьте **MediaOverlayLayer** в список [**OverlayLayers**](https://docs.microsoft.com/uwp/api/windows.media.editing.mediacomposition.overlaylayers) композиции.

[!code-cs[AddOverlay](./code/MediaEditing/cs/MainPage.xaml.cs#SnippetAddOverlay)]

-   Наложения внутри слоя упорядочены согласно их порядку в списке **Overlays**, в котором они содержатся. Более высокие индексы внутри списка отображаются поверх более низких индексов. Это справедливо и для уровней наложения внутри композиции. Слой с более высоким индексом в списке **OverlayLayers** композиции будет отображаться поверх слоев с более низким индексом.

-   Так как наложения размещаются поверх друг друга, а не воспроизводятся последовательно, по умолчанию все наложения начинают воспроизведение в начале композиции. Чтобы воспроизведение наложений начиналось в другое время, задайте желаемое смещение времени в свойстве [**Delay**](https://docs.microsoft.com/uwp/api/windows.media.editing.mediaoverlay.delay).

## <a name="add-effects-to-a-media-clip"></a>Добавление эффектов в клип мультимедиа

У каждого объекта **MediaClip** в композиции есть список аудио- и видеоэффектов, в который можно добавить несколько эффектов. Эффекты должны реализовывать [**IAudioEffectDefinition**](https://docs.microsoft.com/uwp/api/Windows.Media.Effects.IAudioEffectDefinition) и [**IVideoEffectDefinition**](https://docs.microsoft.com/uwp/api/Windows.Media.Effects.IVideoEffectDefinition), соответственно. Следующий пример использует текущее положение **MediaPlayerElement** для выбора просматриваемого в настоящее время объекта **MediaClip**, а затем создает новый экземпляр [**VideoStabilizationEffectDefinition**](https://docs.microsoft.com/uwp/api/Windows.Media.Core.VideoStabilizationEffectDefinition) и добавляет его в список [**VideoEffectDefinitions**](https://docs.microsoft.com/uwp/api/windows.media.editing.mediaclip.videoeffectdefinitions) клипа мультимедиа.

[!code-cs[AddVideoEffect](./code/MediaEditing/cs/MainPage.xaml.cs#SnippetAddVideoEffect)]

## <a name="save-a-composition-to-a-file"></a>Сохранение композиции в файл

Композиции мультимедиа можно сериализовать в файл, который позднее можно изменить. Выберите выходной файл, а затем вызовите метод [**SaveAsync**](https://docs.microsoft.com/uwp/api/windows.media.editing.mediacomposition.saveasync) объекта [**MediaComposition**](https://docs.microsoft.com/uwp/api/Windows.Media.Editing.MediaComposition), чтобы сохранить композицию.

[!code-cs[SaveComposition](./code/MediaEditing/cs/MainPage.xaml.cs#SnippetSaveComposition)]

## <a name="load-a-composition-from-a-file"></a>Загрузка композиции из файла

Композицию мультимедиа можно десериализовать из файла, чтобы позволить пользователю просмотреть или изменить ее. Выберите файл композиции, а затем вызовите метод [**LoadAsync**](https://docs.microsoft.com/uwp/api/windows.media.editing.mediacomposition.loadasync) объекта [**MediaComposition**](https://docs.microsoft.com/uwp/api/Windows.Media.Editing.MediaComposition), чтобы загрузить композицию.

[!code-cs[OpenComposition](./code/MediaEditing/cs/MainPage.xaml.cs#SnippetOpenComposition)]

-   Если файл мультимедиа из композиции находится в таком месте, к которому у вашего приложения нет доступа, и он не указан в свойстве [**FutureAccessList**](https://docs.microsoft.com/uwp/api/windows.storage.accesscache.storageapplicationpermissions.futureaccesslist) класса [**StorageApplicationPermissions**](https://docs.microsoft.com/uwp/api/Windows.Storage.AccessCache.StorageApplicationPermissions) для вашего приложения, при загрузке композиции произойдет ошибка.

 

 




