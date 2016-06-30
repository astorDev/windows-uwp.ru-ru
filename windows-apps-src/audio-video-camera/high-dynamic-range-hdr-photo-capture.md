---
author: drewbatgit
ms.assetid: 0186EA01-8446-45BA-A109-C5EB4B80F368
description: "Используйте класс AdvancedPhotoCapture для захвата фотографий с использованием технологии High Dynamic Range (HDR)."
title: "Фотозахват HDR (High Dynamic Range)"
translationtype: Human Translation
ms.sourcegitcommit: 6530fa257ea3735453a97eb5d916524e750e62fc
ms.openlocfilehash: 3015aa4338ddb0c0a006eb631026261a4453f376

---

# Фотозахват HDR (High Dynamic Range)

\[ Обновлено для приложений UWP в Windows 10. Статьи, касающиеся Windows 8.x, см. в разделе [Архив](http://go.microsoft.com/fwlink/p/?linkid=619132) \]


Используйте класс [**AdvancedPhotoCapture**](https://msdn.microsoft.com/library/windows/apps/mt181386) для захвата фотографий с использованием технологии High Dynamic Range (HDR). Кроме того, этот API дает возможность получить опорный кадр из захвата HDR, прежде чем завершить обработку окончательного изображения.

Другие статьи, связанные с захватом HDR:

-   Вы можете использовать [**SceneAnalysisEffect**](https://msdn.microsoft.com/library/windows/apps/dn948902), чтобы разрешить системе оценить содержимое потока предварительного просмотра захвата мультимедиа и определить, улучшит ли обработка HDR результат захвата. Дополнительные сведения см. в разделе [Анализ сцен для захвата мультимедиа](scene-analysis-for-media-capture.md).

-   Используйте [**HdrVideoControl**](https://msdn.microsoft.com/library/windows/apps/dn926680) для захвата видео с помощью алгоритма обработки HDR, встроенного в систему Windows. Дополнительные сведения см. в статье [Доступ к элементам управления видеозахватом на устройстве](capture-device-controls-for-video-capture.md).

-   Вы можете использовать [**VariablePhotoSequenceCapture**](https://msdn.microsoft.com/library/windows/apps/dn652564) для захвата последовательности фотографий (при чем для каждой из них можно указать особые параметры захвата) и внедрения собственного алгоритма HDR или другого алгоритма обработки. Дополнительные сведения см. в статье [Переменная последовательность фотографий](variable-photo-sequence.md).

**Примечание.**
-   Одновременный захват фотографий и видео с помощью **AdvancedPhotoCapture** не поддерживается.

-   Использование вспышки камеры во время расширенной фотосъемки не поддерживается.

**Примечание.** Эта статья основана на понятиях и коде, описанных в статье [Захват фотографий и видео с помощью MediaCapture](capture-photos-and-video-with-mediacapture.md), в которой указаны действия для реализации основных принципов фото- и видеозахвата. Мы рекомендуем ознакомиться с базовым шаблоном захвата мультимедиа в этой статье, прежде чем перейти к более сложным сценариям захвата. Код в этой статье подразумевает, что ваше приложение уже содержит экземпляр MediaCapture, инициализированный надлежащим образом.

## Пространства имен фотозахвата HDR

Для использования фотозахвата HDR ваше приложение должно включать следующие пространства имен помимо пространств имен, необходимых для основного захвата мультимедиа.

[!code-cs[HDRPhotoUsing](./code/BasicMediaCaptureWin10/cs/MainPage.xaml.cs#SnippetHDRPhotoUsing)]


## Определение возможности фотозахвата HDR на текущем устройстве

Описанный в этой статье способ захвата HDR выполняется с помощью объекта [**AdvancedPhotoCapture**](https://msdn.microsoft.com/library/windows/apps/mt181386). Не все устройства поддерживают фотозахват HDR с помощью **AdvancedPhotoCapture**. Определите, поддерживает ли этот метод устройство, на котором в настоящее время запущено приложение. Для этого необходимо получить свойство [**VideoDeviceController**](https://msdn.microsoft.com/library/windows/apps/br226825) объекта **MediaCapture**, а затем получить свойство [**AdvancedPhotoControl**](https://msdn.microsoft.com/library/windows/apps/mt147840). Просмотрите коллекцию контроллера видеоустройств [**SupportedModes**](https://msdn.microsoft.com/library/windows/apps/mt147844), чтобы узнать, включает ли она [**AdvancedPhotoMode.Hdr**](https://msdn.microsoft.com/library/windows/apps/mt147845). Если да, то захват HDR с использованием **AdvancedPhotoCapture** поддерживается.

[!code-cs[HdrSupported](./code/BasicMediaCaptureWin10/cs/MainPage.xaml.cs#SnippetHdrSupported)]

## Настройка и подготовка объекта AdvancedPhotoCapture

Поскольку необходим доступ к экземпляру [**AdvancedPhotoCapture**](https://msdn.microsoft.com/library/windows/apps/mt181386) из нескольких расположений в пределах вашего кода, нужно объявить переменную-член для удержания объекта.

[!code-cs[DeclareAdvancedCapture](./code/BasicMediaCaptureWin10/cs/MainPage.xaml.cs#SnippetDeclareAdvancedCapture)]

После того как вы инициализировали объект **MediaCapture**, создайте в своем приложении объект [**AdvancedPhotoCaptureSettings**](https://msdn.microsoft.com/library/windows/apps/mt147837) и установите режим [**AdvancedPhotoMode.Hdr**](https://msdn.microsoft.com/library/windows/apps/mt147845). Вызовите метод [**Configure**](https://msdn.microsoft.com/library/windows/apps/mt147841) объекта [**AdvancedPhotoControl**](https://msdn.microsoft.com/library/windows/apps/mt147840), передав созданный объект **AdvancedPhotoCaptureSettings**.

Вызовите метод [**PrepareAdvancedPhotoCaptureAsync**](https://msdn.microsoft.com/library/windows/apps/mt181403) объекта **MediaCapture**, передав объект [**ImageEncodingProperties**](https://msdn.microsoft.com/library/windows/apps/hh700993) с указанием типа кодирования, которое должно использоваться в захвате. Класс **ImageEncodingProperties** предоставляет статические методы для создания кодирования изображений, которые поддерживаются **MediaCapture**.

**PrepareAdvancedPhotoCaptureAsync** возвращает объект [**AdvancedPhotoCapture**](https://msdn.microsoft.com/library/windows/apps/mt181386), который будет использоваться для инициализации фотозахвата. Вы можете использовать этот объект, чтобы зарегистрировать обработчики событий для [**OptionalReferencePhotoCaptured**](https://msdn.microsoft.com/library/windows/apps/mt181392) и [**AllPhotosCaptured**](https://msdn.microsoft.com/library/windows/apps/mt181387), которые описываются далее в этой статье.

[!code-cs[CreateAdvancedCaptureAsync](./code/BasicMediaCaptureWin10/cs/MainPage.xaml.cs#SnippetCreateAdvancedCaptureAsync)]

## Захват фотографий с технологией HDR

Чтобы захватить фотографию с расширенным динамическим диапазоном (HDR), вызовите метод [**CaptureAsync**](https://msdn.microsoft.com/library/windows/apps/mt181388) объекта [**AdvancedPhotoCapture**](https://msdn.microsoft.com/library/windows/apps/mt181386). Этот метод возвращает объект [**AdvancedCapturedPhoto**](https://msdn.microsoft.com/library/windows/apps/mt181378), предоставляющий созданную фотографию в свойстве [**Frame**](https://msdn.microsoft.com/library/windows/apps/mt181382).

[!code-cs[CaptureHdrPhotoAsync](./code/BasicMediaCaptureWin10/cs/MainPage.xaml.cs#SnippetCaptureHdrPhotoAsync)]

**ConvertOrientationToPhotoOrientation** и **ReencodeAndSavePhotoAsync** — это вспомогательные методы, которые рассматриваются в статье [Фото- и видеозахват с помощью MediaCapture](capture-photos-and-video-with-mediacapture.md) как часть основного сценария захвата мультимедиа.

## Получение дополнительного опорного кадра

При обработке HDR захватывается несколько кадров. После захвата всех кадров они составляются в одно изображение. Вы можете получить доступ к кадру после его захвата, но до завершения обработки HDR, обработав событие [**OptionalReferencePhotoCaptured**](https://msdn.microsoft.com/library/windows/apps/mt181392). Если вас интересует только конечный результат фотографии с расширенным динамическим диапазоном (HDR), этого делать не нужно.

**Важно!**
            [
              **OptionalReferencePhotoCaptured**
            ](https://msdn.microsoft.com/library/windows/apps/mt181392) не вызывается на устройствах, которые поддерживают аппаратный режим HDR, а потому не создают опорные кадры. В приложении должны предусматриваться случаи, при которых это событие не возникает.

Поскольку опорный кадр поступает из контекста вызова **CaptureAsync**, предусмотрен механизм для передачи контекстной информации обработчику **OptionalReferencePhotoCaptured**. Сначала нужно определить объект, который будет содержать контекстные данные. При этом имя и содержимое этого объекта зависит только от вас. В этом примере определяется объект, который содержит члены для отслеживания имени файла и ориентации камеры при захвате.

[!code-cs[AdvancedCaptureContext](./code/BasicMediaCaptureWin10/cs/MainPage.xaml.cs#SnippetAdvancedCaptureContext)]

Создайте экземпляр объекта контекста, добавьте его члены, а затем передайте его в перегрузку [**CaptureAsync**](https://msdn.microsoft.com/library/windows/apps/mt181388), где объект принимается в качестве параметра.

[!code-cs[CaptureWithContext](./code/BasicMediaCaptureWin10/cs/MainPage.xaml.cs#SnippetCaptureWithContext)]

В обработчике событий [**OptionalReferencePhotoCaptured**](https://msdn.microsoft.com/library/windows/apps/mt181392) приведите свойство [**Context**](https://msdn.microsoft.com/library/windows/apps/mt181405) объекта [**OptionalReferencePhotoCapturedEventArgs**](https://msdn.microsoft.com/library/windows/apps/mt181404) к классу объектов контекста. В этом примере изменяется имя файла для разделения изображения опорного кадра и окончательного изображения с расширенным динамическим диапазоном (HDR), а затем вызывается вспомогательный метод **ReencodeAndSavePhotoAsync**, чтобы сохранить изображение.

[!code-cs[OptionalReferencePhotoCaptured](./code/BasicMediaCaptureWin10/cs/MainPage.xaml.cs#SnippetOptionalReferencePhotoCaptured)]

## Получение уведомления после захвата всех кадров

Фотозахват с технологией HDR предполагает два шага. Сначала захватывается несколько кадров, а затем кадры обрабатываются и создается окончательное HDR-изображение. Инициировать другой захват во время захвата исходных кадров HDR нельзя. Однако это можно сделать после захвата всех кадров, но до завершающей обработки HDR. Событие [**AllPhotosCaptured**](https://msdn.microsoft.com/library/windows/apps/mt181387) возникает после завершения захвата HDR, уведомляя вас о том, что вы можете инициировать другой захват. Обычный сценарий — отключить кнопку захвата пользовательского интерфейса в начале захвата с HDR, а затем снова включить ее при вызове **AllPhotosCaptured**.

[!code-cs[AllPhotosCaptured](./code/BasicMediaCaptureWin10/cs/MainPage.xaml.cs#SnippetAllPhotosCaptured)]

## Удаление объекта AdvancedPhotoCapture

Когда приложение завершит захват, прежде чем ликвидировать объект **MediaCapture**, необходимо завершить работу объекта [**AdvancedPhotoCapture**](https://msdn.microsoft.com/library/windows/apps/mt181386). Для этого вызовите [**FinishAsync**](https://msdn.microsoft.com/library/windows/apps/mt181391) и установите для переменной-члена значение Null.

[!code-cs[CleanUpAdvancedPhotoCapture](./code/BasicMediaCaptureWin10/cs/MainPage.xaml.cs#SnippetCleanUpAdvancedPhotoCapture)]

## Связанные разделы

* [Захват фотографий и видео с помощью MediaCapture](capture-photos-and-video-with-mediacapture.md)



<!--HONumber=Jun16_HO4-->


