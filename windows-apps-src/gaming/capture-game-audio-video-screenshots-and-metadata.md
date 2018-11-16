---
author: drewbatgit
ms.assetid: ''
description: Здесь демонстрируется запись звука, видео и метаданных из игры в приложении UWP.
title: Запись звука, видео, снимков экрана и метаданных из игры
ms.author: drewbat
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, игра, запись, звук, видео, метаданные
ms.localizationpriority: medium
ms.openlocfilehash: 6c1641cb4c50b86d7f678bf18fa85ad0215b4b15
ms.sourcegitcommit: e2fca6c79f31e521ba76f7ecf343cf8f278e6a15
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/15/2018
ms.locfileid: "6978303"
---
# <a name="capture-game-audio-video-screenshots-and-metadata"></a>Запись звука, видео, снимков экрана и метаданных из игры
В этой статье рассказывается, как выполнять запись видео, звука и снимков экрана в играх, а также как отправлять метаданные, которые система добавит в записанное и транслируемое мультимедийное содержимое, позволяя вашим и другим приложениям обеспечивать динамичное взаимодействие, синхронизируемое с событиями игрового процесса. 

Существует два разных способа записи процесса игры в приложении UWP. Пользователь может запустить запись, используя встроенный системный пользовательский интерфейс. Содержимое, записанное с помощью этого метода, добавляется в экосистему игр Microsoft, его можно просматривать и отправлять знакомым с помощью системных средств, таких как приложение XBox, и оно не будет непосредственно доступным вашему приложению и пользователям. В начальных разделах этой статьи рассказывается, как включать и отключать реализованные в системе средства записи работы приложения и как получать уведомления, когда запись запускается или останавливается.

Другой способ записи мультимедийного содержимого — использовать API-интерфейсы пространства имен **[Windows.Media.AppRecording](https://docs.microsoft.com/uwp/api/windows.media.apprecording)**. Если на устройстве включена возможность записи, ваше приложение можно начать запись игрового процесса, и затем по прошествии некоторого времени вы можете остановить запись, после чего мультимедийное содержимое записывается в файл. Если пользователь включил возможность записи из истории, можно также записывать уже прошедший игровой процесс, указав время начала записи в прошлом и ее длительность. Оба этих метода создают видеофайл, доступный вашему приложению, а также, в зависимости от того, где вы решите сохранять файлы, пользователю. В середине этой статьи подробно рассматривается реализация этих сценариев.

Пространство имен **[Windows.Media.Capture](https://docs.microsoft.com/uwp/api/windows.media.capture)** предоставляет API-интерфейсы для создания метаданных, описывающих записываемый или транслируемый игровой процесс. Сюда могут включаться текстовые и числовые значения с текстовыми метками для обозначения каждого элемента данных. Метаданные могут представлять «событие», которое происходит в определенный момент, например когда пользователь заканчивает круг в гонке, либо «состояние», которое сохраняется некоторое время, например игровая карта, на которой находится игрок. Метаданные записываются в кэш, выделенный вашему приложению системой и управляемый ей. Метаданные добавляются в трансляции и записанные видеофайлы, создаваемые как с помощью встроенных системных средств записи, так и собственных средств записи в приложениях. В заключительных разделах этой статьи рассказывается, как записывать метаданные игрового процесса.

> [!NOTE] 
> Поскольку метаданные игрового процесса могут добавляться в мультимедиа-файлы, которые могут передаваться по сети вне контроля пользователя, не следует включать личные сведения и другую потенциально закрытую информацию в метаданные.


## <a name="enable-and-disable-system-app-capture"></a>Включение и отключение системных средств записи работы приложения
Системная запись работы приложения запускается пользователем с помощью встроенного пользовательского интерфейса. Файлы добавляются в игровую экосистему Windows и недоступны вашему приложению и пользователю иными способами кроме встроенных средств, таких как приложение XBox. Ваше приложение может отключить и включить инициируемую системой запись, что позволяет запретить пользователям записывать определенное содержимое или игровой процесс. 

Чтобы включить или отключить системную запись приложения, просто вызовите статический метод **[AppCapture.SetAllowedAsync](https://docs.microsoft.com/uwp/api/windows.media.capture.appcapture.setallowedasync)** и передайте **false** для отключения записи или **true** для ее включения.

[!code-cpp[SetAppCaptureAllowed](./code/AppRecordingExample/cpp/AppRecordingExample/App.cpp#SnippetSetAppCaptureAllowed)]


## <a name="receive-notifications-when-system-app-capture-starts-and-stops"></a>Получение уведомлений при запуске и остановке системной записи работы приложения
Чтобы получать уведомления, когда системная запись приложения начинается или заканчивается, необходимо сначала получить экземпляр класса **[AppCapture](https://docs.microsoft.com/uwp/api/windows.media.capture.appcapture)**, вызвав фабричный метод **[GetForCurrentView](https://docs.microsoft.com/uwp/api/windows.media.capture.appcapture.GetForCurrentView)**. Затем зарегистрируйте обработчик события **[CapturingChanged](https://docs.microsoft.com/uwp/api/windows.media.capture.appcapture.CapturingChanged)**.

[!code-cpp[RegisterCapturingChanged](./code/AppRecordingExample/cpp/AppRecordingExample/App.cpp#SnippetRegisterCapturingChanged)]

В обработчике для события **CapturingChanged** можно проверить свойства **[IsCapturingAudio](https://docs.microsoft.com/uwp/api/windows.media.capture.appcapture.IsCapturingAudio)** и **[IsCapturingVideo](https://docs.microsoft.com/uwp/api/windows.media.capture.appcapture.IsCapturingVideo)**, чтобы определить, ведется ли запись звука и видео, соответственно. Возможно, потребуется обновление пользовательского интерфейса приложения для отражения текущего состояния записи.

[!code-cpp[OnCapturingChanged](./code/AppRecordingExample/cpp/AppRecordingExample/App.cpp#SnippetOnCapturingChanged)]

## <a name="add-the-windows-desktop-extensions-for-the-uwp-to-your-app"></a>Добавление расширений рабочего стола Windows для UWP в приложение
API-интерфейсы для записи звука и видео и захвата снимков экрана непосредственно из приложения, предлагаемые в пространстве имен **[Windows.Media.AppRecording](https://docs.microsoft.com/uwp/api/windows.media.apprecording)**, не включаются в универсальный контракт API. Для доступа к этим API необходимо добавить ссылку на расширения рабочего стола Windows для UWP в приложение, выполнив следующие действия.

1. В Visual Studio в **обозревателе решений** разверните свой проект UWP и щелкните правой кнопкой мыши папку **Ссылки**, а затем выберите пункт **Добавить ссылку...**. 
2. Разверните узел **универсальных приложений для Windows** и выберите **Расширения**.
3. В списке расширений установите флажок рядом с пунктом **расширения рабочего стола Windows для UWP**, который соответствует целевой сборке для проекта. Для функций трансляции приложения версия должна быть 1709 или более поздней.
4. Нажмите **OК**.

## <a name="get-an-instance-of-apprecordingmanager"></a>Получите экземпляр AppRecordingManager
Класс **[AppRecordingManager](https://docs.microsoft.com/uwp/api/windows.media.apprecording.apprecordingmanager)** является центральным API, который вы будете использовать для управления записью в приложении. Получите экземпляр этого класса, вызвав фабричный метод **[GetDefault](https://docs.microsoft.com/uwp/api/windows.media.apprecording.apprecordingmanager.GetDefault)**. Прежде чем использовать какие-либо API-интерфейсы в пространстве имен **Windows.Media.AppRecording**, следует проверять их наличие на текущем устройстве. API-интерфейсы недоступны на устройствах под управлением версий ОС более ранних, чем Windows 10, версия 1709. Вместо того чтобы проверить наличие определенной версии операционной системы, используйте метод **[ApiInformation.IsApiContractPresent](https://docs.microsoft.com/uwp/api/windows.foundation.metadata.apiinformation.isapicontractpresent)** для запроса *Windows.Media.AppBroadcasting.AppRecordingContract* версии 1.0. Если этот контракт представлен, API-интерфейсы записи доступны на этом устройстве. В примере кода в этой статье сначала проверяется наличие API, а затем проверяется, имеет ли **AppRecordingManager** значение null, перед выполнением последующих операций.

[!code-cpp[GetAppRecordingManager](./code/AppRecordingExample/cpp/AppRecordingExample/App.cpp#SnippetGetAppRecordingManager)]

## <a name="determine-if-your-app-can-currently-record"></a>Определение возможности записи вашего приложения
Существует несколько причин, по которым приложение может в определенный момент не иметь возможности записывать звук или видео; в их число входит несоответствие устройства требованиям к оборудованию для записи и осуществление трансляции другим приложением. Перед началом записи можно проверить, имеет ли ваше приложение возможность записи в данный момент. Вызовите метод **[GetStatus](https://docs.microsoft.com/uwp/api/windows.media.apprecording.apprecordingmanager.GetStatus)** объекта **AppRecordingManager**, а затем проверьте свойство **[CanRecord](https://docs.microsoft.com/uwp/api/windows.media.apprecording.apprecordingstatus.CanRecord)** возвращенного объекта **[AppRecordingStatus](https://docs.microsoft.com/uwp/api/windows.media.apprecording.apprecordingstatus)**. Если **CanRecord** возвращает **false**, это означает, что ваше приложение не может сейчас выполнять запись; для определения причины можно проверить свойство **[Details](https://docs.microsoft.com/uwp/api/windows.media.apprecording.apprecordingstatus.Details)**. В зависимости от причины, возможно, вы решите отобразить состояние пользователю или показать инструкции для включения записи в приложении.



[!code-cpp[CanRecord](./code/AppRecordingExample/cpp/AppRecordingExample/App.cpp#SnippetCanRecord)]

## <a name="manually-start-and-stop-recording-your-app-to-a-file"></a>Запуск и остановка записи работы приложения в файл вручную

Убедившись, что ваше приложение может вести запись, можно начать новую запись, вызвав метод **[StartRecordingToFileAsync](https://docs.microsoft.com/uwp/api/windows.media.apprecording.apprecordingmanager.startrecordingtofileasync)** объекта **AppRecordingManager**.

В следующем примере первый блок **then** выполняется в случае сбоя асинхронной задачи. Второй блок **then** пытается получить доступ к результату задачи, и, если результат равен null, значит, задача завершена. В обоих случаях для обработки результата вызывается вспомогательный метод **OnRecordingComplete**, показанный ниже. 

[!code-cpp[StartRecordToFile](./code/AppRecordingExample/cpp/AppRecordingExample/App.cpp#SnippetStartRecordToFile)]

После завершения операции записи проверьте свойство **[Succeeded](https://docs.microsoft.com/uwp/api/windows.media.apprecording.apprecordingresult.Succeeded)** возвращенного объекта **[AppRecordingResult](https://docs.microsoft.com/uwp/api/windows.media.apprecording.apprecordingresult)**, чтобы определить успешно ли прошла операция записи. Если успешно, вы можете проверить свойство **[IsFileTruncated](https://docs.microsoft.com/uwp/api/windows.media.apprecording.apprecordingresult.IsFileTruncated)**, чтобы определить, не пришлось ли системе принудительно обрезать записанный файл в связи с нехваткой пространства. Вы можете проверить свойство **[Duration](https://docs.microsoft.com/uwp/api/windows.media.apprecording.apprecordingresult.Duration)** для определения фактической продолжительности записанного файла, если файл обрезан, его длительность может быть меньше продолжительности операции записи.

[!code-cpp[OnRecordingComplete](./code/AppRecordingExample/cpp/AppRecordingExample/App.cpp#SnippetOnRecordingComplete)]

Ниже приведены примеры базового кода для запуска и остановки операции записи, показанной в предыдущем примере.

[!code-cpp[CallStartRecordToFile](./code/AppRecordingExample/cpp/AppRecordingExample/App.cpp#SnippetCallStartRecordToFile)]

[!code-cpp[FinishRecordToFile](./code/AppRecordingExample/cpp/AppRecordingExample/App.cpp#SnippetFinishRecordToFile)]

## <a name="record-a-historical-time-span-to-a-file"></a>Запись исторического отрезка в файл
Если пользователь включил возможность записи исторических отрезков для вашего приложения в параметрах системы, вы можете записать отрезок игрового процесса, который проходил ранее. В предыдущем примере в этой статье показано, как убедиться, что ваше приложение в данный момент может записывать игровой процесс. Существует дополнительная проверка для того, чтобы определить, включена ли возможность записи исторического отрезка. Снова вызовите **[GetStatus](https://docs.microsoft.com/uwp/api/windows.media.apprecording.apprecordingmanager.GetStatus)** и проверьте свойство **[CanRecordTimeSpan](https://docs.microsoft.com/uwp/api/windows.media.apprecording.apprecordingstatus.CanRecordTimeSpan)** возвращенного объекта **AppRecordingStatus**. В этом примере также возвращается свойство **[HistoricalBufferDuration](https://docs.microsoft.com/uwp/api/windows.media.apprecording.apprecordingstatus.HistoricalBufferDuration)** объекта **AppRecordingStatus**, которое будет использоваться для определения допустимого времени запуска операции записи.

[!code-cpp[CanRecordTimeSpan](./code/AppRecordingExample/cpp/AppRecordingExample/App.cpp#SnippetCanRecordTimeSpan)]

Чтобы записать исторический отрезок, необходимо указать время начала записи и ее длительность. Время начала предоставляется в виде структуры **[DateTime](https://docs.microsoft.com/uwp/api/windows.foundation.datetime)**. Время начала должно быть раньше текущего времени в пределах продолжительности буфера истории для записи. В этом примере длина буфера определяется в ходе проверки того, включена ли возможность записи из истории, как показано в предыдущем примере кода. Длительность истории для записи предоставляется в виде структуры **[TimeSpan](https://docs.microsoft.com/uwp/api/windows.foundation.timespan)**, и она также должна быть меньше либо равна длительности исторического буфера. После определения нужного времени и продолжительности вызовите **[RecordTimeSpanToFileAsync](https://docs.microsoft.com/uwp/api/windows.media.apprecording.apprecordingmanager.recordtimespantofileasync)** для запуска операции записи.

Как и в случае с ручным запуском и остановкой записи, по завершении записи из истории можно проверить свойство **[Succeeded](https://docs.microsoft.com/uwp/api/windows.media.apprecording.apprecordingresult.Succeeded)** возвращенного объекта **[AppRecordingResult](https://docs.microsoft.com/uwp/api/windows.media.apprecording.apprecordingresult)**, чтобы определить, завершилась ли операция записи успешно, а также можно проверить свойства **[IsFileTruncated](https://docs.microsoft.com/uwp/api/windows.media.apprecording.apprecordingresult.IsFileTruncated)** и **[Duration](https://docs.microsoft.com/uwp/api/windows.media.apprecording.apprecordingresult.Duration)** для определения фактической продолжительности записанного файла, которая может быть меньше заданной продолжительности, если файл обрезан.

[!code-cpp[RecordTimeSpanToFile](./code/AppRecordingExample/cpp/AppRecordingExample/App.cpp#SnippetRecordTimeSpanToFile)]

В примере ниже приведен базовый код для запуска операции записи из истории, показанной в предыдущем примере.

[!code-cpp[CallRecordTimeSpanToFile](./code/AppRecordingExample/cpp/AppRecordingExample/App.cpp#SnippetCallRecordTimeSpanToFile)]

## <a name="save-screenshot-images-to-files"></a>Сохранение изображений снимков экрана в файлы
Приложение может выполнить снимок экрана, который будет сохранять содержимое текущего окна приложения в один файл или несколько файлов изображений с различными кодировками изображений. Для указания кодировок изображения, которые вы хотите использовать, создайте список строковых переменных, где каждый элемент представляет тип изображения. Свойства объекта **[ImageEncodingSubtypes](https://docs.microsoft.com/uwp/api/windows.media.mediaproperties.mediaencodingsubtypes)** предоставляют правильное строковое значение для каждого типа поддерживаемого изображения, например **MediaEncodingSubtypes.Png** или **MediaEncodingSubtypes.JpegXr**.

Инициируйте снимок экрана путем вызова метода **[SaveScreenshotToFilesAsync](https://docs.microsoft.com/uwp/api/windows.media.apprecording.apprecordingmanager.savescreenshottofilesasync)** объекта **AppRecordingManager**. Первый параметр этого метода **StorageFolder** указывает папку сохранения файлов изображений. Второй параметр — это префикс имени файла, к которому система добавит расширение, соответствующее типу сохраняемого изображения, например .png.

Третий параметр **SaveScreenshotToFilesAsync** необходим, чтобы система могла провести необходимое преобразование цветового пространства, если в текущем окне для захвата отображается HDR-содержимое. Если в кадре есть HDR-содержимое, этому параметру следует задать значение **AppRecordingSaveScreenshotOption.HdrContentVisible**. В противном случае используйте **AppRecordingSaveScreenshotOption.None**. Последний параметр метода — список форматов изображений, в которых следует сохранять снимок экрана.

По завершении асинхронного вызова **SaveScreenshotToFilesAsync** возвращается объект **[AppRecordingSavedScreenshotInfo](https://docs.microsoft.com/uwp/api/windows.media.apprecording.apprecordingsavedscreenshotinfo)**, который предоставляет **StorageFile** и соответствующее значение **MediaEncodingSubtypes**, указывающее тип изображения для каждого сохраненного изображения.

[!code-cpp[SaveScreenShotToFiles](./code/AppRecordingExample/cpp/AppRecordingExample/App.cpp#SnippetSaveScreenShotToFiles)]

В примере ниже приведен базовый код для запуска операции снимка экрана, показанной в предыдущем примере.

[!code-cpp[CallSaveScreenShotToFiles](./code/AppRecordingExample/cpp/AppRecordingExample/App.cpp#SnippetCallSaveScreenShotToFiles)]

## <a name="add-game-metadata-for-system-and-app-initiated-capture"></a>Добавление игровых метаданных для системных и инициированных приложением записей
В следующих разделах этой статьи рассматриваются способы предоставления метаданных, которые система будет добавлять в записываемый или транслируемый поток MP4 для игрового процесса. Метаданные могут быть добавлены в мультимедийное содержимое, записываемое с помощью встроенного системного пользовательского интерфейса либо средствами приложения с помощью **AppRecordingManager**. Эти метаданные могут быть извлечены вашими и другими приложениями во время воспроизведения мультимедиа, чтобы предоставлять соответствующую контексту информацию, синхронизируемую с записанным или транслируемым игровым процессом.

### <a name="get-an-instance-of-appcapturemetadatawriter"></a>Получение экземпляра AppCaptureMetadataWriter
Основной класс для управления записанными метаданными приложения — **[AppCaptureMetadataWriter](https://docs.microsoft.com/uwp/api/windows.media.capture.appcapturemetadatawriter)**. Перед инициализацией экземпляра этого класса используйте метод **[ApiInformation.IsApiContractPresent](https://docs.microsoft.com/uwp/api/windows.foundation.metadata.apiinformation.isapicontractpresent)** для запроса *Windows.Media.Capture.AppCaptureMetadataContract* версии 1.0, чтобы убедиться, что этот API доступен на данном устройстве.

[!code-cpp[GetMetadataWriter](./code/AppRecordingExample/cpp/AppRecordingExample/App.cpp#SnippetGetMetadataWriter)]

### <a name="write-metadata-to-the-system-cache-for-your-app"></a>Запись метаданных в кэш системы для вашего приложения
Каждому элементу метаданных задается текстовая метка, идентифицирующая этот элемент, а также соответствующее значение, которое может быть строковым, целочисленным или типа double, а также значение из перечисления **[AppCaptureMetadataPriority](https://docs.microsoft.com/uwp/api/windows.media.capture.appcapturemetadatapriority)**, указывающее относительный приоритет этого элемента данных. Элемент метаданных можно рассматривать как «событие», которое происходит в определенный момент, или «состояние», сохраняющееся в течение периода времени. Метаданные записываются в кэш памяти, выделенный вашему приложению системой и управляемый ей. Система применяет ограничения по размеру кэша памяти метаданных и после достижения предельного значения будет удалять данные на основании приоритета, с которым каждый элемент метаданных был записан. В следующем разделе этой статьи рассказывается, как управлять выделением памяти метаданных приложения.

Стандартное приложение может записывать некоторые метаданные в начале сеанса захвата для создания контекста для последующих данных. Для этого сценария рекомендуется использовать данные мгновенного «события». В этом примере вызываются **[AddStringEvent](https://docs.microsoft.com/uwp/api/windows.media.capture.appcapturemetadatawriter.addstringevent)**, **[AddDoubleEvent](https://docs.microsoft.com/uwp/api/windows.media.capture.appcapturemetadatawriter.adddoubleevent)** и **[AddInt32Event](https://docs.microsoft.com/uwp/api/windows.media.capture.appcapturemetadatawriter.addint32event)** для установки мгновенных значений для каждого типа данных.

[!code-cpp[StartSession](./code/AppRecordingExample/cpp/AppRecordingExample/App.cpp#SnippetStartSession)]

Распространенным сценарием использования данных «состояния», сохраняющегося в течение периода времени, является отслеживание игровой карты, на которой находится игрок. В этом примере вызывается **[StartStringState](https://docs.microsoft.com/uwp/api/windows.media.capture.appcapturemetadatawriter.startstringstate)** для установки значения состояния. 

[!code-cpp[StartMap](./code/AppRecordingExample/cpp/AppRecordingExample/App.cpp#SnippetStartMap)]

Вызовите метод **[StopState](https://docs.microsoft.com/uwp/api/windows.media.capture.appcapturemetadatawriter.stopstate)** для записи того, что определенное состояние закончилось.

[!code-cpp[EndMap](./code/AppRecordingExample/cpp/AppRecordingExample/App.cpp#SnippetEndMap)]

Можно перезаписать состояние, установив новое значение с существующей меткой состояния.

[!code-cpp[LevelUp](./code/AppRecordingExample/cpp/AppRecordingExample/App.cpp#SnippetLevelUp)]

Все открытые в данный момент состояния можно прекратить, вызвав метод **[StopAllStates](https://docs.microsoft.com/uwp/api/windows.media.capture.appcapturemetadatawriter.StopAllStates)**.

[!code-cpp[RaceComplete](./code/AppRecordingExample/cpp/AppRecordingExample/App.cpp#SnippetRaceComplete)]

### <a name="manage-metadata-cache-storage-limit"></a>Управление ограничением хранилища кэша метаданных
Метаданные, которые вы пишете с помощью **AppCaptureMetadataWriter**, кэшируются системой до записи в соответствующий мультимедийный поток. Система определяет ограничения размера для кэша метаданных каждого приложения. После достижения предельного размера кэша система начнет очистку кэшированных метаданных. Система удалит метаданные, которые были записаны со значением приоритета **[AppCaptureMetadataPriority.Informational](https://docs.microsoft.com/uwp/api/windows.media.capture.appcapturemetadatapriority)** перед удалением метаданных с приоритетом **[AppCaptureMetadataPriority.Important](https://docs.microsoft.com/uwp/api/windows.media.capture.appcapturemetadatapriority)**.

В любой момент можно проверить размер свободного пространства в байтах для кэша метаданных вашего приложения, вызвав метод **[RemainingStorageBytesAvailable](https://docs.microsoft.com/uwp/api/windows.media.capture.appcapturemetadatawriter.RemainingStorageBytesAvailable)**. Вы можете задать собственное пороговое значение для своего приложения, после достижения которого можно, например сократить объем метаданных записываемых в кэш. В следующем примере показана простая реализация такого подхода.

[!code-cpp[CheckMetadataStorage](./code/AppRecordingExample/cpp/AppRecordingExample/App.cpp#SnippetCheckMetadataStorage)]

[!code-cpp[ComboExecuted](./code/AppRecordingExample/cpp/AppRecordingExample/App.cpp#SnippetComboExecuted)]

### <a name="receive-notifications-when-the-system-purges-metadata"></a>Получение уведомлений об очистке метаданных системой
Вы можете зарегистрироваться для получения уведомления о начале очистки метаданных для вашего приложения системой путем регистрации обработчика для события **[MetadataPurged](https://docs.microsoft.com/uwp/api/windows.media.capture.appcapturemetadatawriter.MetadataPurged)**.

[!code-cpp[RegisterMetadataPurged](./code/AppRecordingExample/cpp/AppRecordingExample/App.cpp#SnippetRegisterMetadataPurged)]

В обработчике для события **MetadataPurged** можно освободить дополнительное пространство в кэше метаданных, завершив состояния с более низким приоритетом, можно реализовать определяемую приложением логику сокращения объема метаданных, записываемых в кэш, либо можно не выполнять никаких действий и разрешить системе продолжить очистку кэша в зависимости от приоритета, с которым данные были записаны.

[!code-cpp[OnMetadataPurged](./code/AppRecordingExample/cpp/AppRecordingExample/App.cpp#SnippetOnMetadataPurged)]

## <a name="related-topics"></a>Статьи по теме

* [Игры](index.md)
 

 




