---
Description: В этой статье описано, как создать компонент среды выполнения Windows, реализующий интерфейс IBasicVideoEffect, который позволяет создавать пользовательские эффекты для видеопотоков.
MS-HAID: dev\_audio\_vid\_camera.custom\_video\_effects
MSHAttr: PreferredLib:/library/windows/apps
Search.Product: eADQiWindows 10XVcnh
title: Пользовательские видеоэффекты
ms.date: 02/08/2017
ms.topic: article
keywords: Windows 10, uwp
ms.assetid: 40a6bd32-a756-400f-ba34-2c5f507262c0
ms.localizationpriority: medium
ms.openlocfilehash: 1be4bf71d99bd6560ce4ed753b55dacdfcceb868
ms.sourcegitcommit: b52ddecccb9e68dbb71695af3078005a2eb78af1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74257196"
---
# <a name="custom-video-effects"></a>Пользовательские видеоэффекты




В этой статье описано, как создать компонент среды выполнения Windows, реализующий интерфейс [**IBasicVideoEffect**](https://docs.microsoft.com/uwp/api/Windows.Media.Effects.IBasicVideoEffect) для создания пользовательских эффектов для видеопотоков. Пользовательские эффекты можно использовать с несколькими различными API среды выполнения Windows, включая [[MediaCapture]](https://docs.microsoft.com/uwp/api/Windows.Media.Capture.MediaCapture), который предоставляет доступ к камере устройства, и [**MediaComposition**](https://docs.microsoft.com/uwp/api/Windows.Media.Editing.MediaComposition), который позволяет создавать сложные композиции из мультимедийных клипов.

## <a name="add-a-custom-effect-to-your-app"></a>Добавление пользовательского эффекта в приложение


Пользовательский видеоэффект определяется в классе, реализующем интерфейс [**IBasicVideoEffect**](https://docs.microsoft.com/uwp/api/Windows.Media.Effects.IBasicVideoEffect). Этот класс невозможно включить непосредственно в проект приложения. Вместо этого необходимо использовать компонент среды выполнения Windows для размещения вашего класса видеоэффекта.

**Добавление среда выполнения Windows компонента для видеоролика**

1.  Открыв свое решение в Microsoft Visual Studio, перейдите в меню **Файл** и выберите пункт **Добавить-&gt;Новый проект**.
2.  Выберите тип проекта **Компонент среды выполнения Windows (универсальное приложение)** .
3.  Для соответствия этому примеру задайте проекту название *VideoEffectComponent*. Это название будет использоваться в коде далее.
4.  Нажмите кнопку **ОК**.
5.  Шаблон проекта создает класс под названием Class1.cs. В **Обозревателе решений** щелкните правой кнопкой мыши по значку для Class1.cs и выберите **Переименовать**.
6.  Переименуйте файл в *ExampleVideoEffect.cs*. Visual Studio отобразит уведомление с вопросом о том, не следует ли обновить все упоминания с использованием нового имени. нажмите кнопку **Да**.
7.  Откройте **ExampleVideoEffect.cs** и дополните определение класса для реализации интерфейса [**IBasicVideoEffect**](https://docs.microsoft.com/uwp/api/Windows.Media.Effects.IBasicVideoEffect).

[!code-cs[ImplementIBasicVideoEffect](./code/VideoEffect_Win10/cs/VideoEffectComponent/ExampleVideoEffect.cs#SnippetImplementIBasicVideoEffect)]


Для получения доступа ко всем типам, используемым в примерах в этой статье, необходимо добавить следующие пространства имен в файл класса эффекта.

[!code-cs[EffectUsing](./code/VideoEffect_Win10/cs/VideoEffectComponent/ExampleVideoEffect.cs#SnippetEffectUsing)]


## <a name="implement-the-ibasicvideoeffect-interface-using-software-processing"></a>Реализация интерфейса IBasicVideoEffect путем программной обработки


Ваш видеоэффект должен реализовывать все методы и свойства интерфейса [**IBasicVideoEffect**](https://docs.microsoft.com/uwp/api/Windows.Media.Effects.IBasicVideoEffect). Этот раздел содержит простую пошаговую реализацию этого интерфейса, для которой используется программная обработка.

### <a name="close-method"></a>Метод Close

Система вызывает метод [**Close**](https://docs.microsoft.com/uwp/api/windows.media.effects.ibasicvideoeffect.close) для вашего класса, когда соответствующий эффект должен будет завершить работу. Этот метод следует использовать для утилизации всех созданных вами ресурсов. Аргументом этого метода является [**MediaEffectClosedReason**](https://docs.microsoft.com/uwp/api/Windows.Media.Effects.MediaEffectClosedReason), и он позволяет узнать, был ли эффект закрыт в нормальном режиме либо произошла ошибка, либо эффект не поддерживает необходимый формат кодирования.

[!code-cs[Close](./code/VideoEffect_Win10/cs/VideoEffectComponent/ExampleVideoEffect.cs#SnippetClose)]


### <a name="discardqueuedframes-method"></a>Метод DiscardQueuedFrames

Метод [**DiscardQueuedFrames**](https://docs.microsoft.com/uwp/api/windows.media.effects.ibasicvideoeffect.discardqueuedframes) вызывается, когда ваш эффект необходимо сбросить. Типовой сценарий такой ситуации — ваш эффект хранит ранее обработанные кадры для использования в обработке текущего кадра. При вызове этого метода необходимо утилизировать набор предыдущих сохраненных вами кадров. Этот метод можно использовать для сброса любого состояния, связанного с предыдущими кадрами, а не только с накопленными видеокадрами.


[!code-cs[DiscardQueuedFrames](./code/VideoEffect_Win10/cs/VideoEffectComponent/ExampleVideoEffect.cs#SnippetDiscardQueuedFrames)]



### <a name="isreadonly-property"></a>Свойство IsReadOnly

Свойство [**IsReadOnly**](https://docs.microsoft.com/uwp/api/windows.media.effects.ibasicvideoeffect.isreadonly) позволяет системе узнать, будет ли ваш эффект записывать данные в выходной поток эффекта. Если ваше приложение не изменяет видеокадры, например применяется эффект, который только анализирует видеокадры, этому свойству следует задать значение true, и это позволит системе эффективно копировать поток ввода кадров в поток вывода кадров за вас.

> [!TIP]
> Если свойству [**IsReadOnly**](https://docs.microsoft.com/uwp/api/windows.media.effects.ibasicvideoeffect.isreadonly) задано значение true, система копирует кадр ввода в кадр вывода перед вызовом [**ProcessFrame**](https://docs.microsoft.com/uwp/api/windows.media.effects.ibasicvideoeffect.processframe). Присвоение свойству **IsReadOnly** значения true не лишает вас возможности производить запись в кадры вывода эффекта в методе **ProcessFrame**.


[!code-cs[IsReadOnly](./code/VideoEffect_Win10/cs/VideoEffectComponent/ExampleVideoEffect.cs#SnippetIsReadOnly)]

### <a name="setencodingproperties-method"></a>Метод SetEncodingProperties

Система вызывает [**SetEncodingProperties**](https://docs.microsoft.com/uwp/api/windows.media.effects.ibasicvideoeffect.setencodingproperties) на эффекте, чтобы предоставить вам информацию о свойствах кодирования для видеопотока, на котором работает эффект. Этот метод также предоставляет ссылку на устройство Direct3D, используемое для аппаратной отрисовки. Использование такого устройства показано в примере аппаратной обработки ниже в этой статье.

[!code-cs[SetEncodingProperties](./code/VideoEffect_Win10/cs/VideoEffectComponent/ExampleVideoEffect.cs#SnippetSetEncodingProperties)]


### <a name="supportedencodingproperties-property"></a>Свойство SupportedEncodingProperties

Система проверяет свойство [**SupportedEncodingProperties**](https://docs.microsoft.com/uwp/api/windows.media.effects.ibasicvideoeffect.supportedencodingproperties), чтобы определить, какие свойства кодирования поддерживаются вашим эффектом. Обратите внимание, что если потребитель вашего эффекта не может кодировать видео с использованием указанных вами свойств, он вызовет метод [**Close**](https://docs.microsoft.com/uwp/api/windows.media.effects.ibasicvideoeffect.close) на вашем эффекте и удалит этот эффект из видеоконвейера.


[!code-cs[SupportedEncodingProperties](./code/VideoEffect_Win10/cs/VideoEffectComponent/ExampleVideoEffect.cs#SnippetSupportedEncodingProperties)]


> [!NOTE] 
> Если вы вернете пустой список объектов [**VideoEncodingProperties**](https://docs.microsoft.com/uwp/api/Windows.Media.MediaProperties.VideoEncodingProperties) при вызове **SupportedEncodingProperties**, система по умолчанию перейдет на кодирование ARGB32.

 

### <a name="supportedmemorytypes-property"></a>Свойство SupportedMemoryTypes

Система проверяет свойство [**SupportedMemoryTypes**](https://docs.microsoft.com/uwp/api/windows.media.effects.ibasicvideoeffect.supportedmemorytypes), чтобы определить, будет ли ваш эффект получать доступ к видеокадрам в программной памяти или в аппаратной памяти (GPU). Если вы вернете [**MediaMemoryTypes.Cpu**](https://docs.microsoft.com/uwp/api/Windows.Media.Effects.MediaMemoryTypes), вашему эффекту будут передаваться кадры ввода и вывода, содержащие данные изображений в объектах [**SoftwareBitmap**](https://docs.microsoft.com/uwp/api/Windows.Graphics.Imaging.SoftwareBitmap). Если вы вернете **MediaMemoryTypes.Gpu**, вашему эффекту будут передаваться кадры ввода и вывода, содержащие данные изображений в объектах [**IDirect3DSurface**](https://docs.microsoft.com/uwp/api/Windows.Graphics.DirectX.Direct3D11.IDirect3DSurface).

[!code-cs[SupportedMemoryTypes](./code/VideoEffect_Win10/cs/VideoEffectComponent/ExampleVideoEffect.cs#SnippetSupportedMemoryTypes)]


> [!NOTE]
> Если вы укажете [**MediaMemoryTypes.GpuAndCpu**](https://docs.microsoft.com/uwp/api/Windows.Media.Effects.MediaMemoryTypes), система будет использовать память либо GPU, либо системы, в зависимости от того, какой вариант будет эффективнее для конвейера. При использовании этого значения необходимо провести проверку в методе [**ProcessFrame**](https://docs.microsoft.com/uwp/api/windows.media.effects.ibasicvideoeffect.processframe), чтобы узнать, содержит ли передаваемый методу объект [**SoftwareBitmap**](https://docs.microsoft.com/uwp/api/Windows.Graphics.Imaging.SoftwareBitmap) или [**IDirect3DSurface**](https://docs.microsoft.com/uwp/api/Windows.Graphics.DirectX.Direct3D11.IDirect3DSurface) какие-либо данные, и затем соответствующим образом обработать кадр.

 

### <a name="timeindependent-property"></a>Свойство TimeIndependent

Свойство [**TimeIndependent**](https://docs.microsoft.com/uwp/api/windows.media.effects.ibasicvideoeffect.timeindependent) позволяет системе узнать, не требуется ли вашему эффекту однородная синхронизация. Если установлено значение true, система может использовать оптимизации, которые улучшают работу эффекта.

[!code-cs[TimeIndependent](./code/VideoEffect_Win10/cs/VideoEffectComponent/ExampleVideoEffect.cs#SnippetTimeIndependent)]

### <a name="setproperties-method"></a>Метод SetProperties

Метод [**SetProperties**](https://docs.microsoft.com/uwp/api/windows.media.imediaextension.setproperties) позволяет использующему ваш эффект приложению настраивать параметры эффекта. Свойства передаются в виде сопоставления имен свойств и значений [**IPropertySet**](https://docs.microsoft.com/uwp/api/Windows.Foundation.Collections.IPropertySet).


[!code-cs[SetProperties](./code/VideoEffect_Win10/cs/VideoEffectComponent/ExampleVideoEffect.cs#SnippetSetProperties)]


Этот простой пример уменьшает яркость пикселей в каждом видеокадре до определенного значения. Свойство объявляется, и TryGetValue используется для получения значения, заданного вызывающим приложением. Если значение не задано, по умолчанию используется значение 0,5.

[!code-cs[FadeValue](./code/VideoEffect_Win10/cs/VideoEffectComponent/ExampleVideoEffect.cs#SnippetFadeValue)]


### <a name="processframe-method"></a>Метод ProcessFrame

Именно в методе [**ProcessFrame**](https://docs.microsoft.com/uwp/api/windows.media.effects.ibasicvideoeffect.processframe) ваш эффект изменяет данные изображения видеопотока. Метод вызывается один раз на кадр, и ему передается объект [**ProcessVideoFrameContext**](https://docs.microsoft.com/uwp/api/Windows.Media.Effects.ProcessVideoFrameContext). Этот объект содержит входной объект [**VideoFrame**](https://docs.microsoft.com/uwp/api/Windows.Media.VideoFrame), содержащий входящий кадр, который требуется обработать, и выходной объект **VideoFrame**, в который вы записываете данные изображения, которые затем будут переданы остальным компонентам видеоконвейера. Каждый из этих объектов **VideoFrame** объектов имеет свойство [**SoftwareBitmap**](https://docs.microsoft.com/uwp/api/windows.media.videoframe.softwarebitmap) и свойство [**Direct3DSurface**](https://docs.microsoft.com/uwp/api/windows.media.videoframe.direct3dsurface), но то, какое из этих двух свойств можно использовать, определяется значением, возвращаемым из свойства [**SupportedMemoryTypes**](https://docs.microsoft.com/uwp/api/windows.media.effects.ibasicvideoeffect.supportedmemorytypes).

В этом примере показана простая реализация метода **ProcessFrame** с использованием программной обработки. Дополнительные сведения о работе с объектами [**SoftwareBitmap**](https://docs.microsoft.com/uwp/api/Windows.Graphics.Imaging.SoftwareBitmap) см. в разделе [Обработка изображений](imaging.md). Пример реализации **ProcessFrame** с помощью аппаратной обработки приводится далее в этой статье.

Для доступа к буферу данных **SoftwareBitmap** требуется COM-взаимодействие, поэтому необходимо включить пространство имен **System.Runtime.InteropServices** в файл класса вашего эффекта.

[!code-cs[COMUsing](./code/VideoEffect_Win10/cs/VideoEffectComponent/ExampleVideoEffect.cs#SnippetCOMUsing)]


Добавьте следующий код в пространство имен вашего эффекта для импорта интерфейса доступа к буферу изображения.

[!code-cs[COMImport](./code/VideoEffect_Win10/cs/VideoEffectComponent/ExampleVideoEffect.cs#SnippetCOMImport)]


> [!NOTE]
> Поскольку этот метод получает доступ к необработанному буферу изображений в его исходной форме, необходимо в настройках проекта разрешить использование небезопасного кода.
> 1.  В обозревателе решений щелкните правой кнопкой мыши по проекту VideoEffectComponent решений и выберите **Свойства**.
> 2.  Выберите вкладку **Сборка**.
> 3.  Установите флажок **Разрешить небезопасный код**.

 

Теперь вы можете добавить реализацию метода **ProcessFrame**. Сначала этот метод получает объект [**BitmapBuffer**](https://docs.microsoft.com/uwp/api/Windows.Graphics.Imaging.BitmapBuffer) от программных растровых изображений и ввода, и вывода. Обратите внимание, что кадр вывода открывается для записи, а кадр ввода — для чтения. Далее выполняется получение [**IMemoryBufferReference**](https://docs.microsoft.com/uwp/api/Windows.Foundation.IMemoryBufferReference) для каждого буфера путем вызова [**CreateReference**](https://docs.microsoft.com/uwp/api/windows.graphics.imaging.bitmapbuffer.createreference). Затем производится получение фактических данных буфера путем преобразования объектов **IMemoryBufferReference** в описанный выше интерфейс COM-взаимодействия **IMemoryByteAccess** и последующего вызова **GetBuffer**.

Теперь, когда буферы данных получены, можно выполнять чтение из буфера ввода и запись в буфер вывода. Компоновку буфера можно получить путем вызова [**GetPlaneDescription**](https://docs.microsoft.com/uwp/api/windows.graphics.imaging.bitmapbuffer.getplanedescription), что предоставит информацию о ширине, шаге и начальном смещении буфера. Параметр бит на пиксель определяется свойствами кодирования, ранее заданными с помощью метода [**SetEncodingProperties**](https://docs.microsoft.com/uwp/api/windows.media.effects.ibasicvideoeffect.setencodingproperties). Информация о формате буфера используется для поиска индекса ввода в буфер для каждого пикселя. Значение пикселей из исходного буфера копируется в целевой буфер, а значения цвета при этом умножаются свойством FadeValue, заданным для этого эффекта, чтобы уменьшить яркость на указанную величину.

[!code-cs[ProcessFrameSoftwareBitmap](./code/VideoEffect_Win10/cs/VideoEffectComponent/ExampleVideoEffect.cs#SnippetProcessFrameSoftwareBitmap)]


## <a name="implement-the-ibasicvideoeffect-interface-using-hardware-processing"></a>Реализация интерфейса IBasicVideoEffect путем аппаратной обработки


Создание пользовательского видеоэффекта с помощью аппаратной (GPU) обработки выполняется практически так же, как с использованием программной обработки, описанной выше. В этом разделе показаны несколько отличий в эффекте, использующем аппаратную обработку. В этом примере используется API среды выполнения Windows Win2D. Подробную информацию об использовании Win2D см. в [документации по Win2D](https://microsoft.github.io/Win2D/html/Introduction.htm).

Выполните следующие действия, чтобы добавить пакет Win2D NuGet в проект, созданный согласно описанию в разделе **Добавление пользовательского эффекта в приложение** в начале этой статьи.

**Добавление пакета NuGet Win2D в проект Effect**

1.  В **обозревателе решений** щелкните правой кнопкой мыши по проекту **VideoEffectComponent** и выберите **Управление пакетами NuGet**.
2.  В верхней части окна выберите вкладку **Обзор**.
3.  В поле поиска введите **Win2D**.
4.  Выберите **Win2D.uwp**, а затем — **Установить** в области справа.
5.  В диалоговом окне **Просмотр изменений** отобразится назначенный для установки пакет. Нажмите кнопку **ОК**.
6.  Примите условия лицензии пакета.

Помимо пространства имен, включенных в процедуру начальной настройки проекта, вам потребуется добавить следующие пространства имен, предоставляемые Win2D.

[!code-cs[UsingWin2D](./code/VideoEffect_Win10/cs/VideoEffectComponent/ExampleVideoEffectWin2D.cs#SnippetUsingWin2D)]


Поскольку этот эффект будет использовать память GPU для работы с данными изображений, следует возвращать [**MediaMemoryTypes.Gpu**](https://docs.microsoft.com/uwp/api/Windows.Media.Effects.MediaMemoryTypes) из свойства [**SupportedMemoryTypes**](https://docs.microsoft.com/uwp/api/windows.media.effects.ibasicvideoeffect.supportedmemorytypes).

[!code-cs[SupportedMemoryTypesWin2D](./code/VideoEffect_Win10/cs/VideoEffectComponent/ExampleVideoEffectWin2D.cs#SnippetSupportedMemoryTypesWin2D)]


Установите свойства кодирования, которые ваш эффект будет поддерживать с использованием свойства [**SupportedEncodingProperties**](https://docs.microsoft.com/uwp/api/windows.media.effects.ibasicvideoeffect.supportedencodingproperties). При работе с Win2D следует использовать кодирование ARGB32.

[!code-cs[SupportedEncodingPropertiesWin2D](./code/VideoEffect_Win10/cs/VideoEffectComponent/ExampleVideoEffectWin2D.cs#SnippetSupportedEncodingPropertiesWin2D)]


Используйте метод [**SetEncodingProperties**](/uwp/api/windows.graphics.imaging.softwarebitmap.convert) для создания нового объекта Win2D **CanvasDevice** из объекта [**IDirect3DDevice**](https://docs.microsoft.com/uwp/api/Windows.Graphics.DirectX.Direct3D11.IDirect3DDevice), переданного в метод.

[!code-cs[SetEncodingPropertiesWin2D](./code/VideoEffect_Win10/cs/VideoEffectComponent/ExampleVideoEffectWin2D.cs#SnippetSetEncodingPropertiesWin2D)]


Реализация [**SetProperties**](https://docs.microsoft.com/uwp/api/windows.media.imediaextension.setproperties) идентична предыдущему примеру с программной обработкой. В данном примере используется свойство **BlurAmount** для настройки эффекта размытия Win2D.

[!code-cs[SetPropertiesWin2D](./code/VideoEffect_Win10/cs/VideoEffectComponent/ExampleVideoEffectWin2D.cs#SnippetSetPropertiesWin2D)]

[!code-cs[BlurAmountWin2D](./code/VideoEffect_Win10/cs/VideoEffectComponent/ExampleVideoEffectWin2D.cs#SnippetBlurAmountWin2D)]


Последним шагом является реализация метода [**ProcessFrame**](https://docs.microsoft.com/uwp/api/windows.media.effects.ibasicvideoeffect.processframe), который действительно обрабатывает данные изображения.

С помощью интерфейсов API Win2D создается **CanvasBitmap** из свойства [**Direct3DSurface**](https://docs.microsoft.com/uwp/api/windows.media.videoframe.direct3dsurface) кадра ввода. **CanvasRenderTarget** создается из свойства **Direct3DSurface** кадра вывода, а **CanvasDrawingSession** создается из этого целевого объекта прорисовки. Новый эффект Win2D **GaussianBlurEffect** инициализируется с помощью свойства **BlurAmount**, которое наш эффект предоставляет посредством [**SetProperties**](https://docs.microsoft.com/uwp/api/windows.media.imediaextension.setproperties). В завершение вызывается метод **CanvasDrawingSession.DrawImage** для отображения растрового изображения ввода в целевом объекте прорисовки с использованием эффекта размытия.

[!code-cs[ProcessFrameWin2D](./code/VideoEffect_Win10/cs/VideoEffectComponent/ExampleVideoEffectWin2D.cs#SnippetProcessFrameWin2D)]


## <a name="adding-your-custom-effect-to-your-app"></a>Добавление пользовательского эффекта в приложение


Для использования вашего видеоэффекта из приложения необходимо добавить ссылку на проект эффекта в приложение.

1.  В обозревателе решений внутри проекта приложения щелкните правой кнопкой мыши по разделу **Ссылки** и выберите **Добавить ссылку**.
2.  Разверните вкладку **Проекты**, выберите **Решение** и установите флажок для имени вашего проекта эффекта. В этом примере используется имя *VideoEffectComponent*.
3.  Нажмите кнопку **ОК**.

### <a name="add-your-custom-effect-to-a-camera-video-stream"></a>Добавьте ваш пользовательский эффект к потоку видеокамеры

Выполнив инструкции из статьи [Удобный доступ к предварительному просмотру на камере](simple-camera-preview-access.md), можно настроить простой поток предварительного просмотра с камеры. Выполнение этих шагов позволит создать инициализированный объект [**MediaCapture**](https://docs.microsoft.com/uwp/api/Windows.Media.Capture.MediaCapture), который используется для доступа к видеопотоку камеры.

Чтобы добавить настраиваемый видеоэффект в тот же поток камеры, сначала создайте новый объект [**VideoEffectDefinition**](https://docs.microsoft.com/uwp/api/Windows.Media.Effects.VideoEffectDefinition), передав пространство имен и имя класса для соответствующего эффекта. Затем вызовите методAddVideoEffect[**объекта**MediaCapture](https://docs.microsoft.com/uwp/api/windows.media.capture.mediacapture.addvideoeffectasync), чтобы добавить эффект в указанный поток. В этом примере значение параметра [**MediaStreamType.VideoPreview**](https://docs.microsoft.com/uwp/api/Windows.Media.Capture.MediaStreamType) используется, чтобы указать, что эффект необходимо добавить в поток предварительного просмотра. Если приложение поддерживает видеофиксацию, для добавления эффекта в поток фиксации можно воспользоваться параметром **MediaStreamType.VideoRecord**. **AddVideoEffect** возвращает объект [**IMediaExtension**](https://docs.microsoft.com/uwp/api/Windows.Media.IMediaExtension), который представляет пользовательский эффект. Для настройки конфигурации нужного эффекта можно воспользоваться методом SetProperties.

После добавления эффекта вызывается [**StartPreviewAsync**](https://docs.microsoft.com/uwp/api/windows.media.capture.mediacapture.startpreviewasync) для запуска потока предварительного просмотра.

[!code-cs[AddVideoEffectAsync](./code/VideoEffect_Win10/cs/VideoEffect_Win10/MainPage.xaml.cs#SnippetAddVideoEffectAsync)]



### <a name="add-your-custom-effect-to-a-clip-in-a-mediacomposition"></a>Добавление настраиваемого эффекта в клип с помощью MediaComposition

Общие инструкции по созданию мультимедийных композиций из видеоклипов см. в разделе [Мультимедийные композиции и редактирование](media-compositions-and-editing.md). В следующем фрагменте кода показано создание простой мультимедийной композиции с использованием настраиваемого видеоэффекта. Объект [**MediaClip**](https://docs.microsoft.com/uwp/api/Windows.Media.Editing.MediaClip) создается вызовом метода [**CreateFromFileAsync**](https://docs.microsoft.com/uwp/api/windows.media.editing.mediaclip.createfromfileasync), который передает видеофайл, созданный пользователем с помощью средства [**FileOpenPicker**](https://docs.microsoft.com/uwp/api/Windows.Storage.Pickers.FileOpenPicker), а клип добавляется в новый объект [**MediaComposition**](https://docs.microsoft.com/uwp/api/Windows.Media.Editing.MediaComposition). Затем создается новый объект [**VideoEffectDefinition**](https://docs.microsoft.com/uwp/api/Windows.Media.Effects.VideoEffectDefinition), который передает пространство имен и имя класса вашего эффекта конструктору. Наконец. определение эффекта добавляется в коллекцию [**VideoEffectDefinitions**](https://docs.microsoft.com/uwp/api/windows.media.editing.mediaclip.videoeffectdefinitions) объекта **MediaClip**.


[!code-cs[AddEffectToComposition](./code/VideoEffect_Win10/cs/VideoEffect_Win10/MainPage.xaml.cs#SnippetAddEffectToComposition)]


## <a name="related-topics"></a>См. также
* [Простой доступ к предварительной версии камеры](simple-camera-preview-access.md)
* [Создание и редактирование мультимедиа](media-compositions-and-editing.md)
* [Документация по Win2D](https://microsoft.github.io/Win2D/html/Introduction.htm)
* [Воспроизведение мультимедиа](media-playback.md)