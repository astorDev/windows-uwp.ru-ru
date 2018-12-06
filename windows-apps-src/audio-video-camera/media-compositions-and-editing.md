---
ms.assetid: C4DB495D-1F91-40EF-A55C-5CABBF3269A2
description: API-интерфейсы в пространстве имен Windows.Media.Editing позволяют быстро разрабатывать приложения, которые дают пользователям возможность создавать композиции мультимедиа из исходных аудио- и видеофайлов.
title: Создание и редактирование мультимедийных композиций
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 1e342094509dd5d8fb06657d147ac6468a5f8cd6
ms.sourcegitcommit: d7613c791107f74b6a3dc12a372d9de916c0454b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/05/2018
ms.locfileid: "8751624"
---
# <a name="media-compositions-and-editing"></a>Создание и редактирование мультимедийных композиций



В данной статье описывается, как использовать API-интерфейсы в пространстве имен [**Windows.Media.Editing**](https://msdn.microsoft.com/library/windows/apps/dn640565) для быстрой разработки приложений, которые дают пользователям возможность создавать композиции мультимедиа из исходных аудио- и видеофайлов. Компоненты платформы включают возможность программного соединения нескольких видеоклипов, добавление фото- и видеоналожений, добавление фоновых аудиодорожек и применение звуковых и видеоэффектов. Созданные композиции мультимедиа можно преобразовать в неструктурируемый файл мультимедиа для воспроизведения или совместного использования. Кроме того, их можно сериализовать на диск или десериализовать с него, что позволяет пользователю загружать и изменять ранее созданные композиции. Все эти функции предоставляются в интерфейсе среды выполнения Windows, который прост в использовании, а также снижает объем и сложность кода, требуемого для выполнения этих задач, по сравнению с низкоуровневым API-интерфейсом [Microsoft Media Foundation](https://msdn.microsoft.com/library/windows/desktop/ms694197) .

## <a name="create-a-new-media-composition"></a>Создание композиции мультимедиа

Класс [**MediaComposition**](https://msdn.microsoft.com/library/windows/apps/dn652646) — это контейнер для всех клипов мультимедиа, входящих в композицию, который отвечает за окончательную композицию, загрузку с диска и сохранение на диск и предоставляет возможность выполнения предварительной потоковой передачи композиции для ее просмотра в пользовательском интерфейсе. Чтобы использовать **MediaComposition** в своем приложении, добавьте пространство имен [**Windows.Media.Editing**](https://msdn.microsoft.com/library/windows/apps/dn640565), а также [**Windows.Media.Core**](https://msdn.microsoft.com/library/windows/apps/dn278962), предоставляющее связанные API-интерфейсы, которые могут вам понадобиться.

[!code-cs[Namespace1](./code/MediaEditing/cs/MainPage.xaml.cs#SnippetNamespace1)]


Получить доступ к объекту **MediaComposition** можно из нескольких точек вашего кода, поэтому вы будете объявлять переменную-член, где он хранится.

[!code-cs[DeclareMediaComposition](./code/MediaEditing/cs/MainPage.xaml.cs#SnippetDeclareMediaComposition)]

Конструктор для **MediaComposition** не принимает никаких аргументов.

[!code-cs[MediaCompositionConstructor](./code/MediaEditing/cs/MainPage.xaml.cs#SnippetMediaCompositionConstructor)]

## <a name="add-media-clips-to-a-composition"></a>Добавление в композицию клипов мультимедиа

Обычно композиции мультимедиа содержат один или несколько видеоклипов. Вы можете использовать [**FileOpenPicker**](https://msdn.microsoft.com/library/windows/apps/hh738369), чтобы позволить пользователям выбирать видеофайл. После выбора файла создайте новый объект [**MediaClip**](https://msdn.microsoft.com/library/windows/apps/dn652596), в котором будет содержаться видеоклип, вызвав [**MediaClip.CreateFromFileAsync**](https://msdn.microsoft.com/library/windows/apps/dn652607). Затем добавьте клип в список [**Clips**](https://msdn.microsoft.com/library/windows/apps/dn652648) объекта **MediaComposition**.

[!code-cs[PickFileAndAddClip](./code/MediaEditing/cs/MainPage.xaml.cs#SnippetPickFileAndAddClip)]

-   Клипы мультимедиа появятся в **MediaComposition** в том же порядке, в каком они представлены в списке [**Clips**](https://msdn.microsoft.com/library/windows/apps/dn652648).

-   **MediaClip** можно включить в композицию только один раз. При попытке добавить **MediaClip**, который уже используется в композиции, произойдет ошибка. Для того чтобы использовать видеоклип в композиции несколько раз, вызовите метод [**Clone**](https://msdn.microsoft.com/library/windows/apps/dn652599), позволяющий создать объект **MediaClip**, который затем можно добавить в композицию.

-   Универсальные приложения для Windows не имеют прав доступ ко всей файловой системе. Чтобы сохранить разрешения на доступ к файлу, свойство [**FutureAccessList**](https://msdn.microsoft.com/library/windows/apps/br207457) класса [**StorageApplicationPermissions**](https://msdn.microsoft.com/library/windows/apps/br207456) позволяет вашему приложению хранить запись файла, который был выбран пользователем. Максимальное число записей в **FutureAccessList** равно 1000, поэтому вашему приложению необходимо управлять этим списком, чтобы убедиться, что он не переполнен. Это особенно важно, если вы планируете поддерживать загрузку и изменение ранее созданных композиций.

-   **MediaComposition** поддерживает видеоклипы в формате MP4.

-   Если видеофайл содержит несколько внедренных аудиодорожек, вы можете выбрать, какая из них должна использоваться в композиции, настроив свойство [**SelectedEmbeddedAudioTrackIndex**](https://msdn.microsoft.com/library/windows/apps/dn652627).

-   Создайте **MediaClip** с одноцветным заполнением всего кадра, вызвав метод [**CreateFromColor**](https://msdn.microsoft.com/library/windows/apps/dn652605) и указав цвет и длительность клипа.

-   Создайте **MediaClip** на основе файла изображения, вызвав метод [**CreateFromImageFileAsync**](https://msdn.microsoft.com/library/windows/apps/dn652610) и указав файл изображения и длительность клипа.

-   Создайте **MediaClip** на основе [**IDirect3DSurface**](https://msdn.microsoft.com/library/windows/apps/dn965505), вызвав [**CreateFromSurface**](https://msdn.microsoft.com/library/dn764774) и указав поверхность и длительность клипа.

## <a name="preview-the-composition-in-a-mediaelement"></a>Предварительный просмотр композиции в MediaElement

Чтобы пользователь мог просматривать композицию мультимедиа, добавьте класс [**MediaPlayerElement**](https://msdn.microsoft.com/library/windows/apps/Windows.UI.Xaml.Controls.MediaPlayerElement) в XAML-файл, определяющий ваш пользовательский интерфейс.

[!code-xml[MediaElement](./code/MediaEditing/cs/MainPage.xaml#SnippetMediaElement)]

Объявите переменную-член типа [**MediaStreamSource**](https://msdn.microsoft.com/library/windows/apps/dn282716).


[!code-cs[DeclareMediaStreamSource](./code/MediaEditing/cs/MainPage.xaml.cs#SnippetDeclareMediaStreamSource)]

Вызовите метод [**GeneratePreviewMediaStreamSource**](https://msdn.microsoft.com/library/windows/apps/dn652674) объекта **MediaComposition** для создания **MediaStreamSource** для композиции. Создайте объект [**MediaSource**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Core.MediaSource), вызвав фабричный метод [**CreateFromMediaStreamSource**](https://msdn.microsoft.com/library/windows/apps/dn930907), и назначьте его свойству [**Source**](https://msdn.microsoft.com/library/windows/apps/Windows.UI.Xaml.Controls.MediaPlayerElement.Source) элемента **MediaPlayerElement**. Теперь композицию можно просматривать в пользовательском интерфейсе.


[!code-cs[UpdateMediaElementSource](./code/MediaEditing/cs/MainPage.xaml.cs#SnippetUpdateMediaElementSource)]

-   Перед вызовом [**GeneratePreviewMediaStreamSource**](https://msdn.microsoft.com/library/windows/apps/dn652674) член **MediaComposition** должен содержать по крайней мере один клип мультимедиа, иначе возвращенный объект будет пустым.

-   Временная шкала **MediaElement**, отражающая изменения в композиции, автоматически не обновляется. Рекомендуется вызывать **GeneratePreviewMediaStreamSource** и задавать свойство **Source** для **MediaPlayerElement** каждый раз, когда в композицию вносится набор изменений и необходимо обновить пользовательский интерфейс.

Когда пользователь покидает страницу, рекомендуется присвоить объекту **MediaStreamSource** и свойству [**Source**](https://msdn.microsoft.com/library/windows/apps/br227419) элемента **MediaPlayerElement** значение NULL, чтобы освободить связанные ресурсы.

[!code-cs[OnNavigatedFrom](./code/MediaEditing/cs/MainPage.xaml.cs#SnippetOnNavigatedFrom)]

## <a name="render-the-composition-to-a-video-file"></a>Преобразование композиции в видеофайл

Чтобы преобразовать композицию мультимедиа в неструктурированный видеофайл, которым можно поделиться и который можно просматривать на других устройствах, необходимо использовать API-интерфейсы из пространства имен [**Windows.Media.Transcoding**](https://msdn.microsoft.com/library/windows/apps/br207105). Чтобы обновить интерфейс пользователя в ходе выполнения асинхронной операции, вам понадобятся API-интерфейсы из пространства имен [**Windows.UI.Core**](https://msdn.microsoft.com/library/windows/apps/br208383).

[!code-cs[Namespace2](./code/MediaEditing/cs/MainPage.xaml.cs#SnippetNamespace2)]

После того как пользователь выбрал выходной файл с помощью [**FileSavePicker**](https://msdn.microsoft.com/library/windows/apps/br207871), преобразуйте композицию в этот файл, вызвав для объекта **MediaComposition** метод [**RenderToFileAsync**](https://msdn.microsoft.com/library/windows/apps/dn652690). Остальная часть кода в следующем примере просто следует шаблону обработки [**AsyncOperationWithProgress**](https://msdn.microsoft.com/library/windows/desktop/br205807).

[!code-cs[RenderCompositionToFile](./code/MediaEditing/cs/MainPage.xaml.cs#SnippetRenderCompositionToFile)]

-   [**MediaTrimmingPreference**](https://msdn.microsoft.com/library/windows/apps/dn640561) позволяет указать приоритет скорости перекодировки в зависимости от точности обрезки соседних клипов мультимедиа. **Fast** приводит к более быстрой перекодировке с меньшей точностью обрезки, **Precise** — к более медленной перекодировке с более точной обрезкой.

## <a name="trim-a-video-clip"></a>Обрезка видеоклипа

Обрезать длительность видеоклипа в композиции можно за счет настройки объекта [**MediaClip**](https://msdn.microsoft.com/library/windows/apps/dn652596) для свойства [**TrimTimeFromStart**](https://msdn.microsoft.com/library/windows/apps/dn652637) или [**TrimTimeFromEnd**](https://msdn.microsoft.com/library/windows/apps/dn652634), либо для обоих.

[!code-cs[TrimClipBeforeCurrentPosition](./code/MediaEditing/cs/MainPage.xaml.cs#SnippetTrimClipBeforeCurrentPosition)]

-   Пользователь может указать значения точек начала и конца обрезки в любом пользовательском интерфейсе. В примере, приведенном выше, используется свойство [**Position**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Playback.MediaPlaybackSession.Position) члена [**MediaPlaybackSession**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Playback.MediaPlaybackSession), связанного с **MediaPlayerElement** для определения объекта **MediaClip**, воспроизводимого в композиции в текущей позиции с помощью проверки [**StartTimeInComposition**](https://msdn.microsoft.com/library/windows/apps/dn652629) и [**EndTimeInComposition**](https://msdn.microsoft.com/library/windows/apps/dn652618). Затем свойства **Position** и **StartTimeInComposition** используются повторно для вычисления времени обрезки с начала клипа. **FirstOrDefault** — это расширенный метод из пространства имен **System.Linq**, который упрощает код для операции выбора элементов из списка.
-   Свойство [**OriginalDuration**](https://msdn.microsoft.com/library/windows/apps/dn652625) объекта **MediaClip** позволяет узнать длительность клипа мультимедиа без применения обрезки.
-   Свойство [**TrimmedDuration**](https://msdn.microsoft.com/library/windows/apps/dn652631) позволяет узнать длительность клипа мультимедиа после применения обрезки.
-   Указание значения обрезки, которое превышает исходную длительность клипа, не приводит к возникновению ошибки. Однако если композиция содержит только один клип, который обрезается до нулевой длины вследствие того, что указано большое значение обрезки, последующий вызов [**GeneratePreviewMediaStreamSource**](https://msdn.microsoft.com/library/windows/apps/dn652674) вернет значение NULL, как если бы композиция не содержала ни одного клипа.

## <a name="add-a-background-audio-track-to-a-composition"></a>Добавление фоновой аудиодорожки в композицию

Чтобы добавить в композицию фоновую дорожку, загрузите аудиофайл, а затем создайте объект [**BackgroundAudioTrack**](https://msdn.microsoft.com/library/windows/apps/dn652544), вызвав фабричный метод [**BackgroundAudioTrack.CreateFromFileAsync**](https://msdn.microsoft.com/library/windows/apps/dn652561). Далее добавьте **BackgroundAudioTrack** в свойство [**BackgroundAudioTracks**](https://msdn.microsoft.com/library/windows/apps/dn652647) композиции.

[!code-cs[AddBackgroundAudioTrack](./code/MediaEditing/cs/MainPage.xaml.cs#SnippetAddBackgroundAudioTrack)]

-   **MediaComposition** поддерживает фоновые аудиодорожки в формате MP3, WAV и FLAC.

-   Фоновая аудиодорожка

-   Как и в случае с видеофайлом, для сохранения доступа к файлам в композиции следует использовать класс [**StorageApplicationPermissions**](https://msdn.microsoft.com/library/windows/apps/br207456).

-   Как и в **MediaClip**, **BackgroundAudioTrack** можно включить в композицию только один раз. При попытке добавить объект **BackgroundAudioTrack**, который уже используется в композиции, произойдет ошибка. Для того чтобы использовать аудиодорожку в композиции несколько раз, вызовите метод [**Clone**](https://msdn.microsoft.com/library/windows/apps/dn652599), позволяющий создать объекты **MediaClip**, которые затем можно добавить в композицию.

-   По умолчанию фоновые аудиодорожки начинают воспроизведение в начале композиции. Если присутствует несколько фоновых дорожек, все они будут воспроизводиться в начале композиции. Чтобы воспроизведение фоновой аудиодорожки начиналось в другое время, установите желаемое смещение времени в свойстве [**Delay**](https://msdn.microsoft.com/library/windows/apps/dn652563).

## <a name="add-an-overlay-to-a-composition"></a>Добавление наложения в композицию

Наложения позволяют размещать в композиции несколько слоев видео поверх друг друга. Композиция может содержать несколько слоев наложения, каждый из которых может включать несколько наложений. Создайте объект [**MediaOverlay**](https://msdn.microsoft.com/library/windows/apps/dn764793), передав **MediaClip** в его конструктор. Установите положение и прозрачность наложения, а затем создайте новый объект [**MediaOverlayLayer**](https://msdn.microsoft.com/library/windows/apps/dn764795) и добавьте **MediaOverlay** в список [**Overlays**](https://msdn.microsoft.com/library/windows/desktop/dn280411). Наконец, добавьте **MediaOverlayLayer** в список [**OverlayLayers**](https://msdn.microsoft.com/library/windows/apps/dn764791) композиции.

[!code-cs[AddOverlay](./code/MediaEditing/cs/MainPage.xaml.cs#SnippetAddOverlay)]

-   Наложения внутри слоя упорядочены согласно их порядку в списке **Overlays**, в котором они содержатся. Более высокие индексы внутри списка отображаются поверх более низких индексов. Это справедливо и для уровней наложения внутри композиции. Слой с более высоким индексом в списке **OverlayLayers** композиции будет отображаться поверх слоев с более низким индексом.

-   Так как наложения размещаются поверх друг друга, а не воспроизводятся последовательно, по умолчанию все наложения начинают воспроизведение в начале композиции. Чтобы воспроизведение наложений начиналось в другое время, задайте желаемое смещение времени в свойстве [**Delay**](https://msdn.microsoft.com/library/windows/apps/dn764810).

## <a name="add-effects-to-a-media-clip"></a>Добавление эффектов в клип мультимедиа

У каждого объекта **MediaClip** в композиции есть список аудио- и видеоэффектов, в который можно добавить несколько эффектов. Эффекты должны реализовывать [**IAudioEffectDefinition**](https://msdn.microsoft.com/library/windows/apps/dn608044) и [**IVideoEffectDefinition**](https://msdn.microsoft.com/library/windows/apps/dn608047), соответственно. Следующий пример использует текущее положение **MediaPlayerElement** для выбора просматриваемого в настоящее время объекта **MediaClip**, а затем создает новый экземпляр [**VideoStabilizationEffectDefinition**](https://msdn.microsoft.com/library/windows/apps/dn926762) и добавляет его в список [**VideoEffectDefinitions**](https://msdn.microsoft.com/library/windows/apps/dn652643) клипа мультимедиа.

[!code-cs[AddVideoEffect](./code/MediaEditing/cs/MainPage.xaml.cs#SnippetAddVideoEffect)]

## <a name="save-a-composition-to-a-file"></a>Сохранение композиции в файл

Композиции мультимедиа можно сериализовать в файл, который позднее можно изменить. Выберите выходной файл, а затем вызовите метод [**SaveAsync**](https://msdn.microsoft.com/library/windows/apps/dn640554) объекта [**MediaComposition**](https://msdn.microsoft.com/library/windows/apps/dn652646), чтобы сохранить композицию.

[!code-cs[SaveComposition](./code/MediaEditing/cs/MainPage.xaml.cs#SnippetSaveComposition)]

## <a name="load-a-composition-from-a-file"></a>Загрузка композиции из файла

Композицию мультимедиа можно десериализовать из файла, чтобы позволить пользователю просмотреть или изменить ее. Выберите файл композиции, а затем вызовите метод [**LoadAsync**](https://msdn.microsoft.com/library/windows/apps/dn652684) объекта [**MediaComposition**](https://msdn.microsoft.com/library/windows/apps/dn652646), чтобы загрузить композицию.

[!code-cs[OpenComposition](./code/MediaEditing/cs/MainPage.xaml.cs#SnippetOpenComposition)]

-   Если файл мультимедиа из композиции находится в таком месте, к которому у вашего приложения нет доступа, и он не указан в свойстве [**FutureAccessList**](https://msdn.microsoft.com/library/windows/apps/br207457) класса [**StorageApplicationPermissions**](https://msdn.microsoft.com/library/windows/apps/br207456) для вашего приложения, при загрузке композиции произойдет ошибка.

 

 




