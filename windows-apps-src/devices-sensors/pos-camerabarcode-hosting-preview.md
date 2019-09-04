---
title: Размещение предварительного изображения со сканера штрихкодов на базе камеры
description: Узнайте, как разместить в своем приложении предварительное изображение со сканера штрихкодов на базе камеры.
ms.date: 05/02/2018
ms.topic: article
keywords: Windows 10, UWP, точка обслуживания, POS
ms.localizationpriority: medium
ms.openlocfilehash: b3391a7640e49fb43ac0f7ea33a0fa992c4a7495
ms.sourcegitcommit: 0dec04de501a3db6b22dfd4a320fc09b5c4a21b5
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/04/2019
ms.locfileid: "70243281"
---
# <a name="hosting-a-camera-barcode-scanner-preview-in-your-application"></a>Размещение в приложении предварительного изображения со сканера штрихкодов на базе камеры
## <a name="step-1-setup-your-camera-preview"></a>Шаг 1. Настройка предварительной версии камеры
Первый шаг, необходимый для добавления в приложение предварительного изображения со сканера штрихкодов на базе камеры, можно выполнить, следуя инструкциям в статье [Отображение предварительного изображения с камеры](../audio-video-camera/simple-camera-preview-access.md).  Выполнив этот шаг, вернитесь к этой статье для внесения изменений, связанных с использованием камеры в качестве сканера штрихкодов.

## <a name="step-2-update-capability-declarations"></a>Шаг 2. Обновления объявлений возможностей
Чтобы ваши пользователи не получали запрос согласия на использование микрофона, вы можете исключить его из возможностей, перечисленных в манифесте вашего приложения.

1. В Microsoft Visual Studio откройте конструктор манифеста приложения, дважды щелкнув элемент **package.appxmanifest**в **Обозревателе решений**.
2. Перейдите на вкладку **Возможности**.
3. Снимите флажок **Микрофон**.

 ## <a name="step-3-add-additional-using-directive-for-media-capture"></a>Шаг 3. Добавление дополнительной директивы using для записи мультимедиа

```Csharp
using Windows.Media.Capture;
```

## <a name="step-4-set-up-your-mediacapture-initialization-settings"></a>Шаг 4. Настройка параметров инициализации Медиакаптуре
В приведенном ниже инициализируется [**MediaCaptureInitializationSettings**](https://docs.microsoft.com/uwp/api/windows.media.capture.mediacaptureinitializationsettings). 

```Csharp
 private void InitCaptureSettings()
{
    _captureInitSettings = new MediaCaptureInitializationSettings();
    _captureInitSettings.VideoDeviceId = BarcodeScanner.VideoDeviceId;
    _captureInitSettings.StreamingCaptureMode = StreamingCaptureMode.Video;
    _captureInitSettings.PhotoCaptureSource = PhotoCaptureSource.VideoPreview;
}
```
## <a name="step-5-associate-your-mediacapture-object-with-the-camera-barcode-scanner"></a>Шаг 5. Связывание объекта Медиакаптуре с помощью сканера штрихкода камеры
Замените существующий метод mediaCapture.InitializeAsync() в *StartPreviewAsync()* следующим кодом:

```Csharp
try
    {

        mediaCapture = new MediaCapture();
        await mediaCapture.InitializeAsync(InitCaptureSettings());

        displayRequest.RequestActive();
        DisplayInformation.AutoRotationPreferences = DisplayOrientations.Landscape;
    }
```

> [!TIP]
> Более сложные схемы размещения предварительного изображения с камеры см. в разделе [Отображение просмотра камеры](https://docs.microsoft.com/windows/uwp/audio-video-camera/simple-camera-preview-access#add-capability-declarations-to-the-app-manifest).

## <a name="see-also"></a>См. также

### <a name="samples"></a>Примеры

- [Пример сканера штрихкодов](https://github.com/microsoft/Windows-universal-samples/tree/master/Samples/BarcodeScanner)
