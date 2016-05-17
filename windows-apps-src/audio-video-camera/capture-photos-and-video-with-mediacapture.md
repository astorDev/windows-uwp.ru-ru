---
author: drewbatgit
ms.assetid: 1361E82A-202F-40F7-9239-56F00DFCA54B
description: В этой статье описываются этапы захвата фотографий и видео с помощью API MediaCapture, в том числе инициализация и завершение работы MediaCapture, а также управление изменениями ориентации устройства.
title: Захват фотографий и видео с помощью MediaCapture
---

# Захват фотографий и видео с помощью MediaCapture

\[ Обновлено для приложений UWP в Windows 10. Статьи, касающиеся Windows 8.x, см. в разделе [архив](http://go.microsoft.com/fwlink/p/?linkid=619132) \]


В этой статье описываются этапы захвата фотографий и видео с помощью API [**MediaCapture**](https://msdn.microsoft.com/library/windows/apps/br241124), в том числе инициализация и завершение работы **MediaCapture**, а также управление изменениями ориентации устройства.

Интерфейс **MediaCapture** предназначен для поддержки приложений, которые требуют низкоуровневого управления процессом захвата мультимедиа и реализуют сценарии, в которых необходимы дополнительные возможности захвата. Для работы с **MediaCapture** также нужно предоставить собственный пользовательский интерфейс захвата. Если ваше приложение используется только для фото- и видеозахвата и не применяет усовершенствованные методы захвата, [**CameraCaptureUI**](https://msdn.microsoft.com/library/windows/apps/br241030) позволит захватывать фотографии и видеоизображения с помощью всего нескольких строк кода. Подробнее см. в статье [Фото- и видеозахват с помощью CameraCaptureUI](capture-photos-and-video-with-cameracaptureui.md).

В этой статье используется код, адаптированный из [примера CameraStarterKit](http://go.microsoft.com/fwlink/?LinkId=619479) Вы можете скачать этот пример, чтобы просмотреть код в контексте или использовать пример как отправную точку для настройки собственного приложения.

## Настройка проекта

### Как добавить объявление возможностей в манифест приложения

Чтобы ваше приложение получило доступ к камере устройства, необходимо объявить, что оно использует возможности устройства *веб-камеры* и *микрофона*. Если нужно сохранить захваченные фотографии и видео в библиотеке изображений или видео пользователя, следует также объявить возможности *picturesLibrary* и *videosLibrary*.

**Добавление возможностей в манифест приложения**

1.  В Microsoft Visual Studio откройте конструктор манифеста приложения, дважды щелкнув элемент **package.appxmanifest** в **Обозревателе решений**.
2.  Перейдите на вкладку **Возможности**.
3.  Выставьте флажок для пункта **Веб-камера** и поле для параметра **Микрофон**.
4.  Для доступа к библиотеке изображений и видео установите флажки **Библиотека изображений** и **Библиотека видео**.

### Как добавлять директивы using для API захвата мультимедиа

В приведенном ниже коде показаны пространства имен, на которые ссылается пример кода в этой статье, а также описаны функции каждого пространства имен.

[!code-cs[Использование](./code/BasicMediaCaptureWin10/cs/MainPage.xaml.cs#SnippetUsing)]

## Инициализация объекта MediaCapture

Класс [**MediaCapture**](https://msdn.microsoft.com/library/windows/apps/br241124) в пространстве имен [**Windows.Media.Capture**](https://msdn.microsoft.com/library/windows/apps/br226738) — это базовый интерфейс для всех операций захвата мультимедиа. Как правило, приложения объявляют переменную этого типа, охватывающую одну страницу. Поскольку ваше приложение должно отслеживать текущее состояние **MediaCapture**, необходимо объявить логические переменные для инициализации, предварительного просмотра и записи состояния объекта.

[!code-cs[MediaCaptureVariables](./code/BasicMediaCaptureWin10/cs/MainPage.xaml.cs#SnippetMediaCaptureVariables)]

Чтобы правильно выбрать ориентацию видео при предварительном просмотре, создайте переменные-члены. Они позволят отслеживать, используется ли внешняя камера и выполняет ли приложение зеркальное отображение потока предварительного просмотра. Если видеоканал захватывает изображение пользователя, для более естественного взаимодействия с ним ваше приложение должно зеркально отображать поток предварительного просмотра.

[!code-cs[PreviewVariables](./code/BasicMediaCaptureWin10/cs/MainPage.xaml.cs#SnippetPreviewVariables)]

В приведенном ниже примере метод инициализирует объект захвата мультимедиа. Сначала код выполняет поиск устройства видеозахвата, которое можно использовать для захвата мультимедиа. Затем инициализируется объект **MediaCapture** и регистрируются обработчики его событий. Далее с помощью идентификатора устройства видеозахвата создается объект [**MediaCaptureInitializationSettings**](https://msdn.microsoft.com/library/windows/desktop/hh802710). После этого инициализируется **MediaCapture** путем вызова [**InitializeAsync**](https://msdn.microsoft.com/library/windows/apps/br226598).

[!code-cs[InitializeCameraAsync](./code/BasicMediaCaptureWin10/cs/MainPage.xaml.cs#SnippetInitializeCameraAsync)]

-   Метод [**DeviceInformation.FindAllAsync**](https://msdn.microsoft.com/library/windows/apps/br225432) можно использовать для поиска всех устройств указанного типа. В этом примере передается значение перечисления **DeviceClass.VideoCapture**. Это позволяет методу возвращать только устройства видеозахвата. Обратите внимание, что устройство видеозахвата используется для захвата как видео, так и фотографий.

-   **FindAllAsync** возвращает объект [**DeviceInformationCollection**](https://msdn.microsoft.com/library/windows/apps/br225395), содержащий объект [**DeviceInformation**](https://msdn.microsoft.com/library/windows/apps/br225393) для каждого найденного устройства указанного типа. Метод расширения **FirstOrDefault** из пространства имен **System.Linq** обеспечивает удобный синтаксис для выбора элементов из списка на основе определенных условий. При первом вызове **DeviceInformation** метод пытается выбрать первую переменную [**EnclosureLocation.Panel**](https://msdn.microsoft.com/library/windows/apps/br229906) в списке со значением параметра **Panel.Back**. Это значение указывает на то, что камера находится на задней панели корпуса устройства. Если на задней панели нет камеры, используется первая камера, к которой удалось получить доступ.

-   Если при инициализации не указан идентификатор устройства [**MediaCaptureInitializationSettings**](https://msdn.microsoft.com/library/windows/apps/br226573), система выберет первое устройство во внутреннем списке устройств.

-   С помощью метода [**MediaCapture.InitializeAsync**](https://msdn.microsoft.com/library/windows/apps/br226598) объект инициализируется для использования указанного устройства захвата. Его вызов выполняется внутри блока **try**, поскольку он выдает исключение **UnauthorizedAccessException**, если пользователь отказал вызывающему приложению в доступе к камере. Если вызов выполнен успешно, для переменной **\_isInitialized** задается значение true. В результате при последующих вызовах метода можно определять, инициализировано ли устройство захвата.

- **Важно!** На устройствах некоторых семейств перед тем, как приложение получит доступ к камере устройства, отображается запрос на согласие пользователя. По этой причине следует вызывать только [**MediaCapture.InitializeAsync**](https://msdn.microsoft.com/library/windows/apps/br226598) из основного потока пользовательского интерфейса. Попытка инициализировать камеру через другой поток может привести к сбою инициализации.

-   Если инициализация устройства захвата прошла успешно, переменные будут показывать, является ли устройство захвата внешним или оно расположено на передней панели устройства. Эти значения будут использоваться для правильной ориентировки предварительного просмотра захваченного содержимого для пользователя. После этого будет обновлен пользовательский интерфейс. В нем будут отображаться данные о том, что доступно захваченное содержимое и запущен поток предварительного просмотра с устройства захвата. Все эти задачи выполняются во вспомогательных методах, описанных далее в этой статье.

## Как запустить предварительный просмотр захваченного содержимого

Чтобы пользователь видел, какое содержимое захватывается, нужно предоставить ему возможность предварительно просматривать в пользовательском интерфейсе изображение на устройстве видеозахвата.

**Внимание** Предварительный просмотр захвата необходим, чтобы устройство захвата могло включить автоматический фокус, автоматическую выдержку и автоматический баланс белого.

Для активации предварительного просмотра захвата предназначен элемент управления [**CaptureElement**](https://msdn.microsoft.com/library/windows/apps/br209278). Ниже приведен пример кода XAML, определяющего элемент захвата.

[!code-xml[CaptureElement](./code/BasicMediaCaptureWin10/cs/MainPage.xaml#SnippetCaptureElement)]

Пользователям необходимо, чтобы экран оставался включенным, пока они осуществляют предварительный просмотр экрана видеозахвата. Для этого необходимо создать объект [**DisplayRequest**](https://msdn.microsoft.com/library/windows/apps/br241816). Объявите значение этой переменной с областью страницы, чтобы она сохранялась на протяжении всего сеанса захвата.

[!code-cs[DisplayRequest](./code/BasicMediaCaptureWin10/cs/MainPage.xaml.cs#SnippetDisplayRequest)]

Приведенный ниже метод предназначен для запуска предварительного просмотра захвата мультимедиа. Сначала он вызывает метод [**RequestActive**](https://msdn.microsoft.com/library/windows/apps/br241818) в классе [**DisplayRequest**](https://msdn.microsoft.com/library/windows/apps/br241816), чтобы экран оставался активным. Далее запускается предварительный просмотр путем вызова [**StartPreviewAsync**](https://msdn.microsoft.com/library/windows/apps/br226613).

[!code-cs[StartPreviewAsync](./code/BasicMediaCaptureWin10/cs/MainPage.xaml.cs#SnippetStartPreviewAsync)]

-   В объекте **DisplayRequest** вызывается метод [**RequestActive**](https://msdn.microsoft.com/library/windows/apps/br241818), чтобы отправить системе запрос на постоянное включение экрана.

-   Свойство [**Source**](https://msdn.microsoft.com/library/windows/apps/br227419) **CaptureElement** задается в объекте **MediaCapture** приложения и позволяет определить источник предварительного просмотра.

-   Свойство [**FlowDirection**](https://msdn.microsoft.com/library/windows/apps/br208716) предоставляется инфраструктурой XAML для поддержки двусторонних пользовательских интерфейсов. Если установить для направления текста **CaptureElement** значение [**FlowDirection.RightToLeft**](https://msdn.microsoft.com/library/windows/apps/br242397), при предварительном просмотре видео будет в горизонтальном положении. Этот способ используется, чтобы предварительный просмотр отображался в правильном направлении с точки зрения пользователя, если устройство захвата находится на передней панели устройства.

-   Метод [**StartPreviewAsync**](https://msdn.microsoft.com/library/windows/apps/br226613) запускает отображение потока предварительного просмотра в **CaptureElement**. Если предварительный просмотр начался успешно, переменная **\_isPreviewing** сообщает об этом другим компонентам приложения. Затем вызывается вспомогательный метод, позволяющий настроить поворот при предварительном просмотре. Этот метод описан в следующем разделе.

## Как определить ориентацию экрана и устройства

Если приложение для захвата мультимедиа выполняется на мобильном устройстве, например телефоне или планшете, на некоторые аспекты его работы влияет текущая ориентация устройства. Это корректность поворота потока предварительного просмотра с камеры и правильные настройки кодировки захваченных изображений и видео, которые позволяют пользователю просматривать содержимое в правильной ориентации.

Термин "ориентация экрана" обозначает способ поворота системой страницы XAML на устройстве, чтобы она правильно отображалась для пользователя. Термин "ориентация устройства" относится к ориентации устройства и, следовательно, камеры в пространстве. Оба типа ориентации важны для приложения захвата мультимедиа. Чтобы настроить ориентацию экрана, объявите и инициализируйте переменную для класса [**DisplayInformation**](https://msdn.microsoft.com/library/windows/apps/dn264258), ограниченную страницей. Объявите еще одну переменную типа [**DisplayOrientations**](https://msdn.microsoft.com/library/windows/apps/br226142) для отслеживания текущей ориентации экрана. Кроме того, объявите переменные [**SimpleOrientationSensor**](https://msdn.microsoft.com/library/windows/apps/br206400) и [**SimpleOrientation**](https://msdn.microsoft.com/library/windows/apps/br206399) для отслеживания ориентации устройства.

[!code-cs[DisplayInformationAndOrientation](./code/BasicMediaCaptureWin10/cs/MainPage.xaml.cs#SnippetDisplayInformationAndOrientation)]

Приведенные ниже вспомогательные методы регистрируют обработчиков событий [**DisplayInformation.OrientationChanged**](https://msdn.microsoft.com/library/windows/apps/dn264268) и [**SimpleOrientationSensor.OrientationChanged**](https://msdn.microsoft.com/library/windows/apps/br206407) и отменяют их регистрацию, а также инициализируют переменные для отслеживания текущей ориентации. Обратите внимание, что не все устройства оборудованы [**SimpleOrientationSensor**](https://msdn.microsoft.com/library/windows/apps/br206400), поэтому необходимо проверить его наличие перед регистрацией обработчика или попыткой получить данные о текущей ориентацию.

[!code-cs[RegisterOrientationEventHandlers](./code/BasicMediaCaptureWin10/cs/MainPage.xaml.cs#SnippetRegisterOrientationEventHandlers)]

[!code-cs[UnregisterOrientationEventHandlers](./code/BasicMediaCaptureWin10/cs/MainPage.xaml.cs#SnippetUnregisterOrientationEventHandlers)]

В обработчике событий **SimpleOrientationSensor.OrientationChanged** для события обновите переменную ориентации устройства в соответствии с текущей ориентацией. Если устройство обращено вверх или вниз, обновлять ориентацию не нужно.

[!code-cs[SimpleOrientationChanged](./code/BasicMediaCaptureWin10/cs/MainPage.xaml.cs#SnippetSimpleOrientationChanged)]

В обработчике событий для события **DisplayInformation.OrientationChanged** обновите переменную ориентации экрана в соответствии с текущей ориентацией. Если устройство захвата работает в режиме предварительного просмотра, обновите значение поворота потока предварительного просмотра. Сведения о вспомогательном методе **SetPreviewRotationAsync** приведены в следующем разделе.

[!code-cs[DisplayOrientationChanged](./code/BasicMediaCaptureWin10/cs/MainPage.xaml.cs#SnippetDisplayOrientationChanged)]

## Как настроить поворот предварительного просмотра захваченного мультимедиа

Пользователям необходимо, чтобы элементы управления пользовательского интерфейса вращались при изменении ориентации мобильного устройства. Благодаря этому текст в интерфейсе выравнивается по вертикали и легко читается. Однако для элемента управления **CaptureElement** изменение ориентации предварительного просмотра видео при вращении устройства обычно не требуется. Чтобы обеспечить пользователям удобство работы, поток предварительного просмотра должен вращаться в соответствии с ориентацией устройства.

Кроме того, необходимо, чтобы ориентация потока предварительного просмотра выражалась в градусах. Приведенный ниже вспомогательный метод преобразует значения перечисления [**DisplayOrientations**](https://msdn.microsoft.com/library/windows/apps/br226142) в градусы.

[!code-cs[ConvertDisplayOrientationToDegrees](./code/BasicMediaCaptureWin10/cs/MainPage.xaml.cs#SnippetConvertDisplayOrientationToDegrees)]

И этот вспомогательный метод преобразовывает значение перечисления [**SimpleOrientation**](https://msdn.microsoft.com/library/windows/apps/br206399), которое используется объектом [**SimpleOrientationSensor**](https://msdn.microsoft.com/library/windows/apps/br206400) для выражения вращения устройства, в градусы.

[!code-cs[ConvertDeviceOrientationToDegrees](./code/BasicMediaCaptureWin10/cs/MainPage.xaml.cs#SnippetConvertDeviceOrientationToDegrees)]

При низкоуровневом управлении поворот потока фактически выполняется платформой Microsoft Media Foundation. Его значение определяется с помощью атрибута [MF\_MT\_VIDEO\_ROTATION](https://msdn.microsoft.com/library/windows/desktop/hh162880). Поскольку приложение функционирует в среде выполнения Windows, поворот задается с помощью GUID для атрибута, а не имени атрибута. Определите указанный ниже GUID, чтобы идентифицировать атрибут поворота видеоизображения.

[!code-cs[RotationKey](./code/BasicMediaCaptureWin10/cs/MainPage.xaml.cs#SnippetRotationKey)]

С помощью приведенного ниже метода можно настроить поворот потока предварительного просмотра. Метод [**GetMediaStreamProperties**](https://msdn.microsoft.com/library/windows/apps/br211995) класса [**VideoDeviceController**](https://msdn.microsoft.com/library/windows/apps/br226825) захвата мультимедиа возвращает набор свойств, составленный из пар "ключ/значение". [
             **MediaStreamType.VideoPreview**
	   ](https://msdn.microsoft.com/library/windows/apps/br226640) указывает на то, что требуется настроить свойства для потока предварительного просмотра видео, а не для потока видеозаписи или звукового потока. Набор свойств — это интерфейс общего назначения, который позволяет задавать свойства потока. Однако в этой задаче в качестве ключа свойства добавляется указанный выше GUID поворота видео, а в качестве значения указывается требуемая ориентация видеопотока в градусах. Метод [
              **SetEncodingPropertiesAsync**
            ](https://msdn.microsoft.com/library/windows/apps/dn297781) добавляет в свойства кодировки новые значения. Напоминаем, **MediaStreamType.VideoPreview** позволяет задать свойства для потока предварительного просмотра видео.

[!code-cs[SetPreviewRotation](./code/BasicMediaCaptureWin10/cs/MainPage.xaml.cs#SnippetSetPreviewRotation)]

-   На устройствах с внешними камерами пользователям не требуется, чтобы поток камеры поворачивался при вращении устройства.

-   Если видео в режиме предварительного просмотра зеркально отображается для камеры на передней панели, направление поворота необходимо обратить, чтобы оно соответствовало положению устройства.

-   Некоторые устройства (как правило, телефоны), поддерживают значение ориентации для свойства [**DisplayInformation.AutoRotationPreferences**](https://msdn.microsoft.com/library/windows/apps/dn264259), например [**DisplayOrientations.Landscape**](https://msdn.microsoft.com/library/windows/apps/br226142), при использовании которого экран вращается вместе с устройством. Это значение необходимо задать, так как оно обеспечивает удобство работы на устройствах с поддержкой этой функции. Однако для устройств, которые не поддерживают настройки автоматического поворота, следует внедрить в приложение описанный выше шаблон.

-   В предыдущих выпусках метод [**SetPreviewRotation**](https://msdn.microsoft.com/library/windows/apps/br226611) был единственным способом вращения потока предварительного просмотра. Этот метод все еще присутствует в поверхности API для поддержки существующих приложений, но его возможностей недостаточно, поэтому его не следует использовать для новых приложений.

## Фотозахват

Описанный ниже метод используется для захвата фотографий с помощью метода [**CapturePhotoToStreamAsync**](https://msdn.microsoft.com/library/windows/apps/hh700840) и передачи указанных свойств кодировки и объекта [**InMemoryRandomAccessStream**](https://msdn.microsoft.com/library/windows/apps/br241720), содержащего результат захвата. Класс [**ImageEncodingProperties**](https://msdn.microsoft.com/library/windows/apps/hh700993) содержит вспомогательные методы, например [**CreateJpeg**](https://msdn.microsoft.com/library/windows/apps/hh700994), которые генерируют свойства кодировки для типов файлов, поддерживаемых устройством захвата мультимедиа.

[!code-cs[TakePhotoAsync](./code/BasicMediaCaptureWin10/cs/MainPage.xaml.cs#SnippetTakePhotoAsync)]

Перед сохранением фотографии в файл необходимо правильно определить ее ориентацию. Объект **MediaCapture** не содержит данных об ориентации устройства, поэтому он кодирует данные захваченной фотографии с учетом ориентации устройства захвата по умолчанию. В этом случае пользователи могут быть недовольны захваченными фотографиями из-за их неправильной ориентации. Приведенные ниже вспомогательные методы определяют правильную ориентацию фотографий, а затем сохраняют файл в этой ориентации.

Вспомогательный объект **GetCameraOrientation** начинает работу с текущей ориентацией устройства, а затем поворачивает это значение в зависимости от ориентации пользовательского устройства и расположения камеры. Если камера находится на передней панели устройства, как показано в этом примере с помощью переменной **\_mirroringPreview**, ориентацию камеры следует обратить.

[!code-cs[GetCameraOrientation](./code/BasicMediaCaptureWin10/cs/MainPage.xaml.cs#SnippetGetCameraOrientation)]

Приведенный ниже вспомогательный метод преобразует значения перечисления [**SimpleOrientation**](https://msdn.microsoft.com/library/windows/apps/br206399), используемые датчиком положения в пространстве, в эквивалентные значения [**PhotoOrientation**](https://msdn.microsoft.com/library/windows/apps/hh965476), которые используются кодировщиком точечного рисунка для сохранения файла.

[!code-cs[ConvertOrientationToPhotoOrientation](./code/BasicMediaCaptureWin10/cs/MainPage.xaml.cs#SnippetConvertOrientationToPhotoOrientation)]

На последнем этапе захваченная фотография кодируется и сохраняется. Создайте объект [**BitmapDecoder**](https://msdn.microsoft.com/library/windows/apps/br226176) входного потока, содержащий данные о захватываемой фотографии. Создайте класс [**StorageFile**](https://msdn.microsoft.com/library/windows/apps/br227171) и откройте его для чтения и записи. Создайте объект [**BitmapEncoder**](https://msdn.microsoft.com/library/windows/apps/br226206), передав ему выходной файл и декодер, содержащий данные изображения. Создайте [**BitmapPropertySet**](https://msdn.microsoft.com/library/windows/apps/hh974338) и добавьте новое свойство. Ключ для свойства System.Photo.Orientation указывает на то, что свойство представляет ориентацию фотографии. Значением является ранее рассчитанное значение [**PhotoOrientation**](https://msdn.microsoft.com/library/windows/apps/hh965476). Вызовите метод [**SetPropertiesAsync**](https://msdn.microsoft.com/library/windows/apps/br226252), чтобы обновить свойства в кодировщике, а затем вызовите [**FlushAsync**](https://msdn.microsoft.com/library/dn237883), чтобы записать фотографию в файл хранилища.

[!code-cs[ReencodeAndSavePhotoAsync](./code/BasicMediaCaptureWin10/cs/MainPage.xaml.cs#SnippetReencodeAndSavePhotoAsync)]

-   Если задать свойство точечного рисунка System.Photo.Orientation, ориентация фотографии будет закодирована в метаданных файла. При этом фактические данные изображения не кодируются другим способом. Подробнее о встраивании метаданных в файлы изображений см. [Метаданные изображения](image-metadata.md).

-   Дополнительные сведения о работе с изображениями, включая их кодирование и декодирование, см. [Обработка изображений](imaging.md).

## Видеозахват

Чтобы приступить к захвату видео, создайте файл хранилища, в который будет записано видео. Далее создайте класс [**MediaEncodingProfile**](https://msdn.microsoft.com/library/windows/apps/hh701026), который переменная **MediaCapture** будет использовать для кодирования видео в файл. Класс **MediaEncodingProfile** предоставляет доступ к методам, предназначенным для создания профилей кодирования для поддерживаемых форматов видео, например [**CreateMp4**](https://msdn.microsoft.com/library/windows/apps/hh701078). Чтобы настроить корректный поворот видео в градусах, используйте описанные выше вспомогательные методы. В отличие от сценария фотозахвата, данные поворота видео кодируются в поток параметром **MediaCapture**. Чтобы сведения о повороте присутствовали в профиле кодирования, добавьте их в коллекцию [**VideoEncodingProperties.Properties**](https://msdn.microsoft.com/library/windows/apps/hh701254). В качестве ключа используется определенный ранее GUID поворота видео, а значением выступает поворот в градусах. Далее вызовите метод [**MediaCapture.StartRecordToStorageFileAsync**](https://msdn.microsoft.com/library/windows/apps/hh700863) и укажите свойства кодирования и выходной файл, чтобы начать запись.

[!code-cs[StartRecordingAsync](./code/BasicMediaCaptureWin10/cs/MainPage.xaml.cs#SnippetStartRecordingAsync)]

Чтобы остановить запись, вызовите [**MediaCapture.StopRecordAsync**](https://msdn.microsoft.com/library/windows/apps/br226623).

[!code-cs[StopRecordingAsync](./code/BasicMediaCaptureWin10/cs/MainPage.xaml.cs#SnippetStopRecordingAsync)]

Обработчик события [**MediaCapture.RecordLimitationExceeded**](https://msdn.microsoft.com/library/windows/apps/hh973312) регистрируется при инициализации **MediaCapture**. В обработчике вызовите метод **StopRecordingAsync**, чтобы остановить видеозапись.

[!code-cs[RecordLimitationExceeded](./code/BasicMediaCaptureWin10/cs/MainPage.xaml.cs#SnippetRecordLimitationExceeded)]

## Приостановка и возобновление видеозахвата

В некоторых случаях необходимо временно приостановить и возобновить текущий видеозахват, а не останавливать его и начинать заново. Чтобы приостановить запись, вызовите метод [**PauseRecordAsync**](https://msdn.microsoft.com/library/windows/apps/dn858102). Если указать [**MediaCapturePauseBehavior.RetainHardwareResources**](https://msdn.microsoft.com/library/windows/apps/dn926686), приложение не сможет получить снимки на устройствах, которые не поддерживают синхронный фото- и видеозахват, пока видео приостановлено. О том, как определить, поддерживает ли устройство синхронный фото- и видеозахват, читайте в статье [Профили камеры](camera-profiles.md).

[!code-cs[PauseRecordingAsync](./code/BasicMediaCaptureWin10/cs/MainPage.xaml.cs#SnippetPauseRecordingAsync)]

Чтобы продолжить приостановленный видеозахват, вызовите метод [**ResumeRecordAsync**](https://msdn.microsoft.com/library/windows/apps/dn858103).

[!code-cs[ResumeRecordingAsync](./code/BasicMediaCaptureWin10/cs/MainPage.xaml.cs#SnippetResumeRecordingAsync)]

## Очистка ресурсов захвата

Очень важно правильно завершать работу с ресурсами захвата мультимедиа и освобождать их. Если этого не сделать, после закрытия приложения камера может функционировать некорректно, что негативно отразится на удобстве работы пользователей. В следующих разделах описаны действия, которые необходимо предпринять для завершения работы камеры.

### Как завершить предварительный просмотр захваченного содержимого

Чтобы завершить предварительный просмотр захваченного содержимого, сначала вызовите метод [**MediaCapture.StopPreviewAsync**](https://msdn.microsoft.com/library/windows/apps/br226622). Установите для свойства [**Source**](https://msdn.microsoft.com/library/windows/apps/br227419) класса [**MediaElement**](https://msdn.microsoft.com/library/windows/apps/br242926) значение null. Затем вызовите метод [**RequestRelease**](https://msdn.microsoft.com/library/windows/apps/br241819) для переменной [**DisplayRequest**](https://msdn.microsoft.com/library/windows/apps/br241816), чтобы разрешить системе отключить экран при необходимости.

[!code-cs[StopPreviewAsync](./code/BasicMediaCaptureWin10/cs/MainPage.xaml.cs#SnippetStopPreviewAsync)]

-   Вы не можете получить доступ к пользовательскому интерфейсу из другого потока, поэтому задавать свойство **MediaElement.Source** и вызывать **RequestRelease** необходимо с помощью метода [**CoreDispatcher.RunAsync**](https://msdn.microsoft.com/library/windows/apps/hh750317), чтобы вызовы выполнялись в потоке пользовательского интерфейса.

### Как завершить работу объекта MediaCapture и ликвидировать его

Перед ликвидацией объекта **MediaCapture** остановите все текущие операции записи и поток предварительного просмотра, вызвав описанные в предыдущих разделах вспомогательные методы. После этого удалите все обработчики событий, зарегистрированные с помощью **MediaCapture**, вызовите метод [**Dispose**](https://msdn.microsoft.com/library/windows/apps/dn278858), чтобы освободить все ресурсы, связанные с объектом, и установите для переменной **MediaCapture**

[!code-cs[CleanupCameraAsync](./code/BasicMediaCaptureWin10/cs/MainPage.xaml.cs#SnippetCleanupCameraAsync)]

Этот метод для завершения работы и ликвидации объекта **MediaCapture** следует вызывать из обработчика события [**MediaCapture.Failed**](https://msdn.microsoft.com/library/windows/apps/br226593).

[!code-cs[CaptureFailed](./code/BasicMediaCaptureWin10/cs/MainPage.xaml.cs#SnippetCaptureFailed)]

### Как управлять событиями жизненного цикла приложения и навигации по страницам

События жизненного цикла приложения позволяют приложению инициализировать и освобождать ресурсы. Это особенно важно для события **Application.Suspending**, где приложению необходимо правильно ликвидировать ресурсы захвата мультимедиа.

Обработчики событий жизненного цикла приложения можно регистрировать в конструкторе страницы.

[!code-cs[RegisterAppLifetimeEvents](./code/BasicMediaCaptureWin10/cs/MainPage.xaml.cs#SnippetRegisterAppLifetimeEvents)]

В обработчике события **Application.Suspending** необходимо отменить регистрацию обработчиков событий ориентации устройства и экрана, а также завершить работу объекта **MediaCapture**. Событие [**SystemMediaTransportControls.PropertyChanged**](https://msdn.microsoft.com/library/windows/apps/dn278720), которое не зарегистрировано в этом обработчике, необходимо для другой задачи, связанной с жизненным циклом приложения, как описано далее в этой статье.

Внимание Необходимо запросить приостановку задержки, вызвав метод [**SuspendingOperation.GetDeferral**](https://msdn.microsoft.com/library/windows/apps/br224690) в начале приостановки обработчика события. В результате этого система будет ожидать сигнала о завершении операции, прежде чем демонтировать приложение. Это необходимо, поскольку операции завершения работы **MediaCapture** асинхронны. По этой причине обработчик события **Application.Suspending** может завершить работу до прекращения работы камеры.

[!code-cs[После завершения асинхронных вызовов необходимо устранить задержку, вызвав метод [**SuspendingDeferral.Complete**](https://msdn.microsoft.com/library/windows/apps/br224685).](./code/BasicMediaCaptureWin10/cs/MainPage.xaml.cs#SnippetSuspending)]

Suspending

[!code-cs[В обработчике события **Application.Resuming** необходимо зарегистрировать обработчики событий ориентации устройства и экрана, зарегистрировать событие **SystemMediaTransportControls.PropertyChanged** и инициализировать объект **MediaCapture**.](./code/BasicMediaCaptureWin10/cs/MainPage.xaml.cs#SnippetResuming)]

Возобновление

[!code-cs[Событие [**OnNavigatedTo**](https://msdn.microsoft.com/library/windows/apps/br227508) дает возможность впервые зарегистрировать обработчики событий ориентации устройства и экрана и инициализировать объект **MediaCapture**.](./code/BasicMediaCaptureWin10/cs/MainPage.xaml.cs#SnippetOnNavigatedTo)]

OnNavigatedTo

[!code-cs[Если в приложении несколько страниц, необходимо очистить объекты захвата мультимедиа в обработчике событий [**OnNavigatingFrom**](https://msdn.microsoft.com/library/windows/apps/br227509).](./code/BasicMediaCaptureWin10/cs/MainPage.xaml.cs#SnippetOnNavigatingFrom)]

OnNavigatingFrom Чтобы приложение корректно работало на устройствах с поддержкой нескольких открытых окон, оно должно отвечать в свернутом и восстановленном виде. Для этого необходимо обработать событие [**SystemMediaTransportControls.PropertyChanged**](https://msdn.microsoft.com/library/windows/apps/dn278720).

[!code-cs[Инициализируйте переменную-член для хранения ссылки на объект [**SystemMediaTransportControls**](https://msdn.microsoft.com/library/windows/apps/dn278677) в приложении.](./code/BasicMediaCaptureWin10/cs/MainPage.xaml.cs#SnippetDeclareSystemMediaTransportControls)]

DeclareSystemMediaTransportControls Код, который регистрирует и отменяет регистрацию события **PropertyChanged**, необходимо добавить в события жизненного цикла приложения, как показано в примерах выше. В обработчике события проверьте, было ли событие запущено в результате изменения свойства [**SystemMediaTransportControlsProperty.SoundLevel**](https://msdn.microsoft.com/library/windows/apps/dn278721). Если это так, проверьте значение свойства. Если значением является [**SoundLevel.Muted**](https://msdn.microsoft.com/library/windows/apps/hh700852), приложение было свернуто и необходимо соответствующим образом очистить ресурсы захвата мультимедиа. В противном случае событие сигнализирует о том, что окно приложения восстановлено и следует снова запустить инициализацию ресурсов захвата мультимедиа.

[!code-cs[Доступ к свойству **SoundLevel** следует получать в потоке пользовательского интерфейса, поэтому код в этом методе содержится в вызове метода [**Dispatcher.RunAsync**](https://msdn.microsoft.com/library/windows/apps/hh750317).](./code/BasicMediaCaptureWin10/cs/MainPage.xaml.cs#SnippetSystemMediaControlsPropertyChanged)]

## SystemMediaControlsPropertyChanged

### Дополнительные сведения о пользовательском интерфейсе для захвата мультимедиа

Настройка автоматического поворота Как упоминалось в предыдущем разделе, посвященном повороту потока предварительного просмотра, некоторые устройства поддерживают параметр [**DisplayInformation.AutoRotationPreferences**](https://msdn.microsoft.com/library/windows/apps/dn264259), который предотвращает поворот страниц, в том числе элемента [**CaptureElement**](https://msdn.microsoft.com/library/windows/apps/br209278), в котором отображается содержимое для предварительного просмотра, при вращении устройства. Это обеспечивает удобство работы пользователей на устройствах, которые поддерживают такую возможность. Установить это значение можно при запуске приложения или в начале предварительного просмотра.

[!code-cs[Обратите внимание, что поворот при предварительном просмотре необходимо внедрить для устройств, которые не поддерживают настройки автоповорота.](./code/BasicMediaCaptureWin10/cs/MainPage.xaml.cs#SnippetSetAutoRotationPreferences)]

### SetAutoRotationPreferences

Управление ориентацией элемента пользовательского интерфейса Обычно пользователям необходимо, чтобы элементы пользовательского интерфейса в приложении камеры, например кнопки для фото- и видеозахвата, вращались вместе с устройством при предварительном просмотре видео. Описанный ниже метод иллюстрирует использование двух приведенных ранее вспомогательных методов для правильной ориентации кнопок в пользовательском интерфейсе камеры. Его необходимо вызывать из обработчиков событий [**DisplayInformation.OrientationChanged**](https://msdn.microsoft.com/library/windows/apps/dn264268) и [**SimpleOrientationSensor.OrientationChanged**](https://msdn.microsoft.com/library/windows/apps/br206407), а также при первом запуске приложения.

[!code-cs[Реализация может быть разной в зависимости от пользовательского интерфейса приложения.](./code/BasicMediaCaptureWin10/cs/MainPage.xaml.cs#SnippetUpdateButtonOrientation)]

UpdateButtonOrientation

[!code-cs[При завершении работы приложения или переходе на страницу, не связанную с захватом мультимедиа, установите для параметра автоповорота значение [**None**](https://msdn.microsoft.com/library/windows/apps/br226142), чтобы пользовательский интерфейс вращался правильно.](./code/BasicMediaCaptureWin10/cs/MainPage.xaml.cs#SnippetRevertAutoRotationPreferences)]

### RevertAutoRotationPreferences

Поддержка синхронного фото- и видеозахвата API [**Windows.Media.Capture**](https://msdn.microsoft.com/library/windows/apps/br226738) позволяет одновременно захватывать фотографии и видео на устройствах, которые поддерживают эту функцию. Для краткости в этом примере используется свойство [**ConcurrentRecordAndPhotoSupported**](https://msdn.microsoft.com/library/windows/apps/dn278843), чтобы определить, поддерживается ли синхронный захват видео и фото, но более надежный и рекомендуемый способ это делать — использовать профили камеры.

Дополнительные сведения см. в разделе [Профили камеры](camera-profiles.md). Приведенный ниже вспомогательный метод обновляет элементы управления приложения, чтобы они соответствовали текущему состоянию захвата.

[!code-cs[Вызывайте этот метод при изменении состояния захвата приложения, например при запуске или остановке видеозахвата.](./code/BasicMediaCaptureWin10/cs/MainPage.xaml.cs#SnippetUpdateCaptureControls)]

### UpdateCaptureControls

Поддержка функций пользовательского интерфейса на мобильных устройствах Приведенный в этой статье код работает в универсальном приложении для Windows. С помощью нескольких дополнительных строк кода можно воспользоваться дополнительными функциями пользовательского интерфейса, которые доступны только на мобильных устройствах.

**Чтобы использовать их, добавьте в проект ссылку на Microsoft Mobile Extension SDK для универсальной платформы приложений.**

1.  Как добавить ссылку на мобильный SDK расширения для поддержки кнопки камеры

2.  В **обозревателе решений** щелкните правой кнопкой мыши **Ссылки** и выберите **Добавить ссылку**.

3.  Разверните узел **универсальных приложений для Windows** и выберите **Расширения**.

Установите флажок **Microsoft Desktop Extension SDK для универсальной платформы приложений**. На мобильных устройствах доступен элемент управления [**StatusBar**](https://msdn.microsoft.com/library/windows/apps/dn633864), который предоставляет пользователю сведения о состоянии устройства. Этот элемент занимает место на экране, что может повлиять на возможности пользовательского интерфейса для захвата мультимедиа. Строку строка состояния можно скрыть с помощью метода [**HideAsync**](https://msdn.microsoft.com/library/windows/apps/dn610339), однако его следует вызывать из условного блока, в котором метод [**ApiInformation.IsTypePresent**](https://msdn.microsoft.com/library/windows/apps/dn949016) определяет доступность API. Этот метод возвратит значение true только на мобильных устройствах, которые поддерживают строку состояния.

[!code-cs[Строку состояния необходимо скрывать при запуске приложения и во время предварительного просмотра с камеры.](./code/BasicMediaCaptureWin10/cs/MainPage.xaml.cs#SnippetHideStatusBar)]

HideStatusBar

[!code-cs[Когда приложение завершает работу или пользователь покидает страницу захвата мультимедиа, этот элемент управления можно снова сделать видимым.](./code/BasicMediaCaptureWin10/cs/MainPage.xaml.cs#SnippetShowStatusBar)]

ShowStatusBar На некоторых мобильных устройствах есть специальная кнопка камеры, которую некоторые пользователи применяют вместо элемента управления на экране. Чтобы получать уведомления о нажатии кнопки камеры, зарегистрируйте обработчик события [**HardwareButtons.CameraPressed**](https://msdn.microsoft.com/library/windows/apps/dn653805).

[!code-cs[Поскольку этот API доступен только на мобильных устройствах, перед попыткой получить к нему доступ необходимо снова использовать **IsTypePresent**, чтобы убедиться, что API поддерживается на текущем устройстве.](./code/BasicMediaCaptureWin10/cs/MainPage.xaml.cs#SnippetPhoneUsing)]

[!code-cs[PhoneUsing](./code/BasicMediaCaptureWin10/cs/MainPage.xaml.cs#SnippetRegisterCameraButtonHandler)]

RegisterCameraButtonHandler

[!code-cs[В обработчике события **CameraPressed** можно начать фотозахват.](./code/BasicMediaCaptureWin10/cs/MainPage.xaml.cs#SnippetCameraPressed)]

CameraPressed

[!code-cs[Когда приложение завершает работу или пользователь покидает страницу захвата мультимедиа, отмените регистрацию обработчика кнопки.](./code/BasicMediaCaptureWin10/cs/MainPage.xaml.cs#SnippetUnregisterCameraButtonHandler)]

UnregisterCameraButtonHandler **Примечание.** Эта статья адресована разработчикам приложений для Windows 10 на базе универсальной платформы Windows (UWP).

## При разработке приложений для Windows 8.x или Windows Phone 8.x см. раздел [архивной документации](http://go.microsoft.com/fwlink/p/?linkid=619132)

* [Связанные темы](camera-profiles.md)
* [Профили камеры](high-dynamic-range-hdr-photo-capture.md)
* [Фотозахват HDR (High Dynamic Range)](capture-device-controls-for-photo-and-video-capture.md)
* [Доступ к элементам управления фото- и видеозахватом на устройстве](capture-device-controls-for-video-capture.md)
* [Доступ к элементам управления видеозахватом на устройстве](effects-for-video-capture.md)
* [Эффекты для видеозахвата](scene-analysis-for-media-capture.md)
* [Анализ сцен для захвата мультимедиа](variable-photo-sequence.md)
* [Переменная последовательность фотографий](get-a-preview-frame.md)
* [Получение кадра предварительного просмотра](http://go.microsoft.com/fwlink/?LinkId=619479)


<!--HONumber=May16_HO2-->


