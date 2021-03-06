---
ms.assetid: 9BA3F85A-970F-411C-ACB1-B65768B8548A
description: В этой статье описывается, как быстро вывести поток предварительного просмотра камеры на странице XAML в приложении универсальной платформы Windows (UWP).
title: Отображение просмотра камеры
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 1f35cbab511912bd9cf6616330f3e9e7737189fd
ms.sourcegitcommit: a20457776064c95a74804f519993f36b87df911e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/27/2019
ms.locfileid: "71339749"
---
# <a name="display-the-camera-preview"></a>Отображение просмотра камеры


В этой статье описывается, как быстро вывести поток предварительного просмотра камеры на странице XAML в приложении универсальной платформы Windows (UWP). Для создания приложения, захватывающего фотографии и видео с камеры, необходимо выполнить такие задачи, как обработка положения устройства и ориентации камеры и настройка параметров кодирования записанного файла. Для некоторых сценариев приложения может потребоваться просто отобразить поток предварительного просмотра камеры, не беспокоясь о других вопросах. В данной статье показано, как это сделать с применением минимального количества кода. Обратите внимание, что необходимо всегда надлежащим образом завершать поток предварительного просмотра после окончания работы с ним, выполняя приведенные ниже действия.

Подробнее о создании приложения для захвата фотографий и видео с камеры, см. [Основные принципы фото-, аудио- и видеозахвата с помощью MediaCapture](basic-photo-video-and-audio-capture-with-MediaCapture.md).

## <a name="add-capability-declarations-to-the-app-manifest"></a>Добавление объявлений возможностей в манифест приложения

Чтобы ваше приложение получило доступ к камере устройства, необходимо объявить, что оно использует возможности устройства *webcam* и *microphone*. 

**Добавление возможностей в манифест приложения**

1.  В Microsoft Visual Studio откройте конструктор манифеста приложения, дважды щелкнув элемент **package.appxmanifest**в **Обозревателе решений**.
2.  Перейдите на вкладку **Возможности**.
3.  Выставьте флажок для пункта **Веб-камера** и поле для параметра **Микрофон**.

## <a name="add-a-captureelement-to-your-page"></a>Добавление объекта CaptureElement на страницу

Используйте объект [**CaptureElement**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.CaptureElement) для отображения потока предварительного просмотра на странице XAML.

[!code-xml[CaptureElement](./code/SimpleCameraPreview_Win10/cs/MainPage.xaml#SnippetCaptureElement)]



## <a name="use-mediacapture-to-start-the-preview-stream"></a>Использование объекта MediaCapture для запуска потока предварительного просмотра

Объект [**MediaCapture**](https://docs.microsoft.com/uwp/api/Windows.Media.Capture.MediaCapture) — это интерфейс приложения для взаимодействия с камерой устройства. Этот класс входит в пространство имен Windows.Media.Capture. В описанном в этой статье примере используются API из пространств имен [**Windows.ApplicationModel**](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel) и [System.Threading.Tasks](https://docs.microsoft.com/dotnet/api/system.threading.tasks) в дополнение ко включенным в шаблон проекта по умолчанию.

Добавьте директивы using, чтобы включить следующие пространства имен в файл .cs вашей страницы.

[!code-cs[SimpleCameraPreviewUsing](./code/SimpleCameraPreview_Win10/cs/MainPage.xaml.cs#SnippetSimpleCameraPreviewUsing)]

Объявите переменную-член класса для объекта **MediaCapture** и логическую переменную для отслеживания того, выполняется ли предварительный просмотр камерой в данный момент. 

[!code-cs[DeclareMediaCapture](./code/SimpleCameraPreview_Win10/cs/MainPage.xaml.cs#SnippetDeclareMediaCapture)]

Объявите переменную типа [**DisplayRequest**](https://docs.microsoft.com/uwp/api/Windows.System.Display.DisplayRequest), которая будет использоваться для блокировки отключения экрана во время предварительного просмотра.

[!code-cs[DeclareDisplayRequest](./code/SimpleCameraPreview_Win10/cs/MainPage.xaml.cs#SnippetDeclareDisplayRequest)]

Создайте вспомогательный метод для запуска предварительного просмотра с камеры, названный в этом примере **StartPreviewAsync**. В зависимости от сценария работы приложения можно вызвать его из обработчика событий **OnNavigatedTo**, который вызывается при загрузке страницы или при ожидании и запуске предварительного просмотра в ответ на события пользовательского интерфейса.

Создайте новый экземпляр класса **MediaCapture** и вызовите метод [**InitializeAsync**](https://docs.microsoft.com/uwp/api/windows.media.capture.mediacapture.initializeasync), чтобы инициализировать устройство захвата. Этот метод может завершиться ошибкой, например, на устройствах, не оборудованных камерой, поэтому его необходимо вызывать из блока **try**. Если пользователь отключил доступ к камере в параметрах конфиденциальности устройства, при попытке инициализировать камеру будет вызвано исключение **UnauthorizedAccessException**. Вы также увидите это исключение во время разработки, если в манифест приложения не были добавлены необходимые возможности.

**Важно!** На устройствах некоторых семейств перед тем, как приложение получит доступ к камере устройства, отображается запрос на согласие пользователя. По этой причине следует вызывать только [**MediaCapture.InitializeAsync**](https://docs.microsoft.com/uwp/api/windows.media.capture.mediacapture.initializeasync) из основного потока пользовательского интерфейса. Попытка инициализировать камеру через другой поток может привести к сбою инициализации.

Подключите **MediaCapture** к **CaptureElement**, задав свойство [**Source**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.captureelement.source). Запустите предварительный просмотр путем вызова метода [**StartPreviewAsync**](https://docs.microsoft.com/uwp/api/windows.media.capture.mediacapture.startpreviewasync). Этот метод вызовет исключение **FileLoadException**, если другое приложение использует устройство захвата в монопольном режиме. Информацию о прослушивании изменений в монопольном режиме см. в следующем разделе.

Вызовите [**RequestActive**](https://docs.microsoft.com/uwp/api/windows.system.display.displayrequest.requestactive), чтобы не допустить перехода устройства в спящий режим во время предварительного просмотра. В заключение задайте свойству [**DisplayInformation.AutoRotationPreferences**](https://docs.microsoft.com/uwp/api/windows.graphics.display.displayinformation.autorotationpreferences) значение [**Landscape**](https://docs.microsoft.com/uwp/api/Windows.Graphics.Display.DisplayOrientations), чтобы предотвратить поворот интерфейса пользователя и **CaptureElement** при изменении ориентации устройства пользователем. Дополнительные сведения об обработке изменений ориентации устройства см. в разделе [**Handle device orientation with MediaCapture**](handle-device-orientation-with-mediacapture.md).  

[!code-cs[StartPreviewAsync](./code/SimpleCameraPreview_Win10/cs/MainPage.xaml.cs#SnippetStartPreviewAsync)]

## <a name="handle-changes-in-exclusive-control"></a>Обработка изменений в монопольном режиме
Как было сказано в предыдущем разделе, **StartPreviewAsync** вызывает исключение **FileLoadException**, если другое приложение использует устройство захвата в монопольном режиме. Начиная с Windows 10 версии 1703, можно регистрировать обработчик для события [MediaCapture.CaptureDeviceExclusiveControlStatusChanged](https://docs.microsoft.com/uwp/api/Windows.Media.Capture.MediaCapture.CaptureDeviceExclusiveControlStatusChanged), которое возникает при изменении состояния использования устройства в монопольном режиме. В обработчике для этого события проверьте свойство [MediaCaptureDeviceExclusiveControlStatusChangedEventArgs.Status](https://docs.microsoft.com/uwp/api/windows.media.capture.mediacapturedeviceexclusivecontrolstatuschangedeventargs.Status), чтобы узнать о текущем состоянии. Если новое состояние **SharedReadOnlyAvailable**, в данный момент вы не можете запустить предварительный просмотр. В таком случае рекомендуется обновить пользовательский интерфейс для оповещения пользователя. Если новое состояние — **ExclusiveControlAvailable**, вы можете попробовать снова запустить предварительный просмотр камеры.

[!code-cs[ExclusiveControlStatusChanged](./code/SimpleCameraPreview_Win10/cs/MainPage.xaml.cs#SnippetExclusiveControlStatusChanged)]

## <a name="shut-down-the-preview-stream"></a>Завершение потока предварительного просмотра

Каждый раз, закончив использование потока предварительного просмотра, необходимо завершить поток и очистить ресурсы захвата, чтобы камера была доступна для использования другим приложениям на устройстве. Для завершения потока предварительного просмотра необходимо выполнить следующие действия.

-   Если в данный момент камера обеспечивает предварительный просмотр, вызовите [**StopPreviewAsync**](https://docs.microsoft.com/uwp/api/windows.media.capture.mediacapture.stoppreviewasync), чтобы завершить поток предварительного просмотра. Если вызов **StopPreviewAsync** производится, когда предварительная версия не работает, возникнет исключение.
-   Установите для свойства [**Source**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.captureelement.source) класса **CaptureElement** значение null. Используйте метод [**CoreDispatcher.RunAsync**](https://docs.microsoft.com/uwp/api/windows.ui.core.coredispatcher.runasync), чтобы убедиться, что этот вызов выполняется в потоке пользовательского интерфейса.
-   Вызовите метод [**Dispose**](https://docs.microsoft.com/uwp/api/windows.media.capture.mediacapture.dispose) объекта **MediaCapture**, чтобы освободить ресурсы объекта. Снова используйте метод [**CoreDispatcher.RunAsync**](https://docs.microsoft.com/uwp/api/windows.ui.core.coredispatcher.runasync), чтобы убедиться, что этот вызов выполняется в потоке пользовательского интерфейса.
-   Присвойте переменной-члену **MediaCapture** значение null.
-   Вызовите [**RequestRelease**](https://docs.microsoft.com/uwp/api/windows.system.display.displayrequest.requestrelease), чтобы разрешить отключение экрана в отсутствие активности.

[!code-cs[CleanupCameraAsync](./code/SimpleCameraPreview_Win10/cs/MainPage.xaml.cs#SnippetCleanupCameraAsync)]

Когда пользователь уходит со страницы, остановите поток предварительного просмотра путем переопределения метода [**OnNavigatedFrom**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.page.onnavigatedfrom).

[!code-cs[OnNavigatedFrom](./code/SimpleCameraPreview_Win10/cs/MainPage.xaml.cs#SnippetOnNavigatedFrom)]

Также необходимо надлежащим образом завершать поток предварительного просмотра в случае приостановки вашего приложения. Для этого зарегистрируйте обработчик для события [**Application.Suspending**](https://docs.microsoft.com/uwp/api/windows.applicationmodel.core.coreapplication.suspending) в конструкторе страницы.

[!code-cs[RegisterSuspending](./code/SimpleCameraPreview_Win10/cs/MainPage.xaml.cs#SnippetRegisterSuspending)]

Сначала проверьте в обработчике событий **Suspending**, что страница отображается в классе приложения [**Frame**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Frame), сравнив тип страницы со свойством [**CurrentSourcePageType**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.frame.currentsourcepagetype). Если страница в настоящее время не отображается, событие **OnNavigatedFrom** уже должно быть вызвано, а поток предварительного просмотра остановлен. Если страница отображается, получите объект [**SuspendingDeferral**](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.SuspendingDeferral) из переданных в обработчик аргументов события, чтобы предотвратить приостановку приложения системой до остановки потока предварительного просмотра. После остановки потока вызовите метод отсрочки [**Complete**](https://docs.microsoft.com/uwp/api/windows.applicationmodel.suspendingdeferral.complete), чтобы разрешить системе продолжить приостановку приложения.

[!code-cs[SuspendingHandler](./code/SimpleCameraPreview_Win10/cs/MainPage.xaml.cs#SnippetSuspendingHandler)]


## <a name="related-topics"></a>См. также

* [Камера](camera.md)
* [Базовая фотография, видео и запись звука с помощью Медиакаптуре](basic-photo-video-and-audio-capture-with-MediaCapture.md)
* [Получить кадр предварительной версии](get-a-preview-frame.md)
