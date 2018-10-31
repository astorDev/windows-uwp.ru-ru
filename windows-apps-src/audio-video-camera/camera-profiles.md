---
author: drewbatgit
ms.assetid: 42A06423-670F-4CCC-88B7-3DCEEDDEBA57
description: В этой статье рассказывается, как использовать профили камеры для обнаружения возможностей различных устройств видеозахвата и управления ими. Сюда входят такие задачи, как выбор профилей, которые поддерживают определенные значения разрешения и частоты кадров, профилей, которые поддерживают одновременный доступ к нескольким камерам, и профилей, которые поддерживают HDR.
title: Обнаружение и выбор возможностей камеры с помощью профилей камеры
ms.author: drewbat
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 1af7453e8bfea973a67dc878438050accc01fb4c
ms.sourcegitcommit: ca96031debe1e76d4501621a7680079244ef1c60
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/31/2018
ms.locfileid: "5836756"
---
# <a name="discover-and-select-camera-capabilities-with-camera-profiles"></a>Обнаружение и выбор возможностей камеры с помощью профилей камеры



В этой статье рассказывается, как использовать профили камеры для обнаружения возможностей различных устройств видеозахвата и управления ими. Сюда входят такие задачи, как выбор профилей, которые поддерживают определенные значения разрешения и частоты кадров, профилей, которые поддерживают одновременный доступ к нескольким камерам, и профилей, которые поддерживают HDR.

> [!NOTE] 
> В этой статье используются понятия и код из статьи [Основные принципы фото-, аудио- и видеозахвата с помощью MediaCapture](basic-photo-video-and-audio-capture-with-MediaCapture.md), в которой описаны этапы реализации основных принципов фото- и видеозахвата. Мы рекомендуем ознакомиться с базовым шаблоном захвата мультимедиа в этой статье, прежде чем перейти к более сложным сценариям захвата. Код в этой статье подразумевает, что ваше приложение уже содержит экземпляр MediaCapture, инициализированный надлежащим образом.

 

## <a name="about-camera-profiles"></a>О профилях камеры

Камеры на различных устройствах поддерживают разные возможности, в том числе позволяют использовать набор поддерживаемых разрешений захвата, определять частоту кадров видеозахвата, а также применять функцию HDR и видеозахвата с переменной частотой кадров (при наличии). С помощью инфраструктуры для захвата мультимедиа универсальной платформы Windows (UWP) этот набор возможностей сохраняется в профиле [**MediaCaptureVideoProfileMediaDescription**](https://msdn.microsoft.com/library/windows/apps/dn926695). Профиль камеры, представленный объектом [**MediaCaptureVideoProfile**](https://msdn.microsoft.com/library/windows/apps/dn926694), содержит три коллекции описания мультимедиа: для захвата фотографий, для видеозахвата и для предварительного просмотра видео.

Перед инициализацией объекта [MediaCapture](capture-photos-and-video-with-mediacapture.md) вы можете отправить запрос в устройства захвата на текущем устройстве, чтобы узнать, какие профили поддерживаются. При выборе поддерживаемого профиля вы можете быть уверены, что устройство захвата поддерживает все возможности, указанные в описаниях мультимедиа профиля. Благодаря этому вам не понадобится методом проб и ошибок определять комбинации возможностей, поддерживаемых на определенном устройстве.

[!code-cs[BasicInitExample](./code/BasicMediaCaptureWin10/cs/MainPage.xaml.cs#SnippetBasicInitExample)]

Примеры кода в этой статье заменяют эту минимальную инициализацию функцией обнаружения профилей камеры с поддержкой нескольких возможностей, которые затем используются для инициализации устройства захвата мультимедиа.

## <a name="find-a-video-device-that-supports-camera-profiles"></a>Как найти видеоустройство с поддержкой профилей камеры

Прежде чем выполнять поиск поддерживаемых профилей камеры, необходимо найти устройство захвата, которое поддерживает их использование. В приведенном ниже примере вспомогательный метод **GetVideoProfileSupportedDeviceIdAsync** предусматривает использование метода [**DeviceInformaion.FindAllAsync**](https://msdn.microsoft.com/library/windows/apps/br225432) для извлечения списка всех доступных устройств видеозахвата. Он перебирает все устройства в списке и с помощью статического метода [**IsVideoProfileSupported**](https://msdn.microsoft.com/library/windows/apps/dn926714) проверяет каждое устройство, чтобы определить, поддерживает ли оно профили видео. Кроме того, свойство [**EnclosureLocation.Panel**](https://msdn.microsoft.com/library/windows/apps/br229906) позволяет указать, какую камеру необходимо выбрать на каждом устройстве: переднюю или заднюю.

Если устройство, поддерживающее профили камеры, находится на определенной панели, то возвращается значение [**Id**](https://msdn.microsoft.com/library/windows/apps/br225437), содержащее строку с идентификатором устройства.

[!code-cs[GetVideoProfileSupportedDeviceIdAsync](./code/BasicMediaCaptureWin10/cs/MainPage.xaml.cs#SnippetGetVideoProfileSupportedDeviceIdAsync)]

Если вспомогательный метод **GetVideoProfileSupportedDeviceIdAsync** вместо идентификатора устройства возвращает значение null или пустую строку, значит на выбранной панели нет устройств, которые поддерживают профили камеры. В этом случае необходимо инициализировать устройство захвата мультимедиа без применения профилей.

[!code-cs[GetDeviceWithProfileSupport](./code/BasicMediaCaptureWin10/cs/MainPage.xaml.cs#SnippetGetDeviceWithProfileSupport)]

## <a name="select-a-profile-based-on-supported-resolution-and-frame-rate"></a>Как выбрать профиль в зависимости от поддерживаемого разрешения и частоты кадров

Чтобы выбрать профиль с определенными функциями, например с возможностью достичь выбранного разрешения и частоты кадров, необходимо сначала вызвать указанный выше вспомогательный метод и с его помощью получить идентификатор устройства захвата с поддержкой профилей камеры.

Создайте объект [**MediaCaptureInitializationSettings**](https://msdn.microsoft.com/library/windows/apps/br226573) с выбранным идентификатором устройства. Вызовите статический метод [**MediaCapture.FindAllVideoProfiles**](https://msdn.microsoft.com/library/windows/apps/dn926708), чтобы получить список всех профилей камеры, поддерживаемых устройством.

В этом примере метод запроса Linq, содержащийся в пространстве имен **System.Linq**, используется для выбора профиля, содержащего объект [**SupportedRecordMediaDescription**](https://msdn.microsoft.com/library/windows/apps/dn926705), свойства [**Width**](https://msdn.microsoft.com/library/windows/apps/dn926700), [**Height**](https://msdn.microsoft.com/library/windows/apps/dn926697), и [**FrameRate**](https://msdn.microsoft.com/library/windows/apps/dn926696) которого соответствуют запрошенным значениям. Если найдено соответствие, для свойств **MediaCaptureInitializationSettings** [**VideoProfile**](https://msdn.microsoft.com/library/windows/apps/dn926679) и [**RecordMediaDescription**](https://msdn.microsoft.com/library/windows/apps/dn926678) задаются анонимные значения, возвращенные запросом Linq. Если совпадений не найдено, используется профиль по умолчанию.

[!code-cs[FindWVGA30FPSProfile](./code/BasicMediaCaptureWin10/cs/MainPage.xaml.cs#SnippetFindWVGA30FPSProfile)]

После того как вы указали нужный профиль камеры в **MediaCaptureInitializationSettings**, просто вызовите метод [**InitializeAsync**](https://msdn.microsoft.com/library/windows/apps/br226598) в объекте захвата мультимедиа, чтобы настроить этот параметр в профиле.

[!code-cs[InitCaptureWithProfile](./code/BasicMediaCaptureWin10/cs/MainPage.xaml.cs#SnippetInitCaptureWithProfile)]

## <a name="use-media-frame-source-groups-to-get-profiles"></a>Использование групп источников кадров мультимедиа для получения профилей

Начиная с Windows 10 версии 1803 вы можете использовать класс [**MediaFrameSourceGroup**](https://docs.microsoft.com/uwp/api/windows.media.capture.frames.mediaframesourcegroup) для получения профилей камеры с определенными возможностями перед инициализацией объекта **MediaCapture**. Групп источников кадров позволяют производителям устройств, представлять группы датчиков или получать возможности как одно виртуальное устройство. Это позволяет реализовать сценарии вычислительной фотографии, такие как совместное использование камер глубины и цветных камер, но также может использоваться для выбора профилей камеры для простых сценариев захвата. Дополнительные сведения об использовании **MediaFrameSourceGroup** см. в разделе [Обработка кадров мультимедиа с помощью MediaFrameReader](process-media-frames-with-mediaframereader.md).

В примере метода ниже показано, как использовать объекты **MediaFrameSourceGroup**, чтобы найти профиль камеры, который поддерживает известный профиль видео, например с поддержкой HDR или переменной последовательности фотографий. Сначала вызовите метод [**MediaFrameSourceGroup.FindAllAsync**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Capture.Frames.MediaFrameSourceGroup.FindAllAsync), чтобы получить список всех групп источников кадров мультимедиа, доступных на текущем устройстве. В цикле пройдитесь по каждой группе источников и вызовите метод [**MediaCapture.FindKnownVideoProfiles**](https://docs.microsoft.com/uwp/api/windows.media.capture.mediacapture.findknownvideoprofiles), чтобы получить список всех профилей видео для текущей группы источников, которые поддерживают указанный профиль (в данном случае это HDR с фотографией WCG). Если найден профиль, который соответствует критериям, создайте объект **MediaCaptureInitializationSettings** и укажите значение **VideoProfile**, чтобы выбрать профиль, а в значении **VideoDeviceId** свойства **Id** укажите текущую группу источников кадров мультимедиа. Например, в этот метод можно передать значение **KnownVideoProfile.HdrWithWcgVideo**, чтобы получить параметры захвата мультимедиа, которые поддерживают видео в формате HDR. Передайте **KnownVideoProfile.VariablePhotoSequence** для получения параметров, которые поддерживают переменную последовательность фотографий.

 [!code-cs[FindKnownVideoProfile](./code/BasicMediaCaptureWin10/cs/MainPage.xaml.cs#SnippetFindKnownVideoProfile)]

## <a name="use-known-profiles-to-find-a-profile-that-supports-hdr-video-legacy-technique"></a>Как найти профиль с поддержкой видео HDR с помощью известных профилей (старый способ)

> [!NOTE] 
> API-интерфейсы, описанные в этом разделе, являются устаревшими начиная с Windows 10 версии 1803. См. предыдущий раздел **Использование групп источников кадров мультимедиа для получения профилей**.

Выбор профиля, который поддерживает HDR, начинается так же, как и другие сценарии. Создайте **MediaCaptureInitializationSettings** и строку для идентификатора устройства захвата. Добавьте логическую переменную, которая будет отслеживать, поддерживается ли видео HDR.

[!code-cs[GetHdrProfileSetup](./code/BasicMediaCaptureWin10/cs/MainPage.xaml.cs#SnippetGetHdrProfileSetup)]

Используйте приведенный выше вспомогательный метод **GetVideoProfileSupportedDeviceIdAsync**, чтобы получить идентификатор устройства захвата, поддерживающего профили камеры.

[!code-cs[FindDeviceHDR](./code/BasicMediaCaptureWin10/cs/MainPage.xaml.cs#SnippetFindDeviceHDR)]

Статический метод [**MediaCapture.FindKnownVideoProfiles**](https://msdn.microsoft.com/library/windows/apps/dn926710) возвращает профили камеры, поддерживаемые указанным устройством, которое классифицировано выбранным значением [**KnownVideoProfile**](https://msdn.microsoft.com/library/windows/apps/dn948843). В этом сценарии с помощью значения **VideoRecording** возвращаются только профили камеры, которые поддерживают видеозапись.

Просмотрите возвращенный список профилей камеры в цикле. В каждом профиле камеры просмотрите каждый класс [**VideoProfileMediaDescription**](https://msdn.microsoft.com/library/windows/apps/dn926695) и проверьте, установлено ли для свойства [**IsHdrVideoSupported**](https://msdn.microsoft.com/library/windows/apps/dn926698) значение «true». Когда будет найдено подходящее описание мультимедиа, прервите цикл и назначьте объекты профиля и описания объекту **MediaCaptureInitializationSettings**.

[!code-cs[FindHDRProfile](./code/BasicMediaCaptureWin10/cs/MainPage.xaml.cs#SnippetFindHDRProfile)]

## <a name="determine-if-a-device-supports-simultaneous-photo-and-video-capture"></a>Как определить, поддерживает ли устройство синхронный фото- и видеозахват

Многие устройства поддерживают одновременный захват фотографий и видео. Чтобы определить, поддерживает ли устройство захвата эту функцию, вызовите метод [**MediaCapture.FindAllVideoProfiles**](https://msdn.microsoft.com/library/windows/apps/dn926708), чтобы получить все профили камеры, поддерживаемые устройством. Используйте запрос ссылки, чтобы найти профиль, который содержит по крайней мере по одной записи для [**SupportedPhotoMediaDescription**](https://msdn.microsoft.com/library/windows/apps/dn926703) и [**SupportedRecordMediaDescription**](https://msdn.microsoft.com/library/windows/apps/dn926705). Если это так, профиль поддерживает синхронный захват.

[!code-cs[GetPhotoAndVideoSupport](./code/BasicMediaCaptureWin10/cs/MainPage.xaml.cs#SnippetGetPhotoAndVideoSupport)]

Этот запрос можно уточнить для поиска профилей, которые кроме одновременной записи видео поддерживают определенные разрешения или другие возможности. Вы также можете использовать для получения профилей, поддерживающих синхронный захват, метод [**MediaCapture.FindKnownVideoProfiles**](https://msdn.microsoft.com/library/windows/apps/dn926710) и значение [**BalancedVideoAndPhoto**](https://msdn.microsoft.com/library/windows/apps/dn948843). Однако обратите внимание, что при отправке запросов всем профилям можно получить более полные результаты.

## <a name="related-topics"></a>Связанные статьи

* [Камера](camera.md)
* [Основные принципы фото-, аудио- и видеозахвата с помощью MediaCapture](basic-photo-video-and-audio-capture-with-MediaCapture.md)
 

 




