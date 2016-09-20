---
author: drewbatgit
ms.assetid: 7DBEE5E2-C3EC-4305-823D-9095C761A1CD
description: "В данной статье рассказывается, как захватить переменную последовательность фотографий (то есть несколько снимков, быстро идущих один за другим) и настроить для каждого снимка различные параметры фокусировки, вспышки, ISO, экспозиции и коррекции экспозиции."
title: "Переменная последовательность фотографий"
ms.sourcegitcommit: 6530fa257ea3735453a97eb5d916524e750e62fc
ms.openlocfilehash: c6be6c0ea255c38bba65550ae44c7f88c140ca0f

---

# Переменная последовательность фотографий

\[ Обновлено для приложений UWP в Windows10. Статьи о Windows8.x см. в [архиве](http://go.microsoft.com/fwlink/p/?linkid=619132) \]


В данной статье рассказывается, как захватить переменную последовательность фотографий (то есть несколько снимков, быстро идущих один за другим) и настроить для каждого снимка различные параметры фокусировки, вспышки, ISO, экспозиции и коррекции экспозиции. Эта функция позволяет использовать такие сценарии, как создание изображений с помощью технологии High Dynamic Range (HDR).

Если вам необходимо захватывать изображения с использованием технологии HDR, но вы не хотите создавать собственный алгоритм обработки, вы можете применять API [**AdvancedPhotoCapture**](https://msdn.microsoft.com/library/windows/apps/mt181386), чтобы использовать встроенные в ОСWindows возможности HDR. Дополнительные сведения см. в статье [Захват фотографий с использованием технологии High Dynamic Range (HDR)](high-dynamic-range-hdr-photo-capture.md).

**Примечание.**  
В этой статье используются понятия и код из статьи [Фото- и видеозахват с помощью MediaCapture](capture-photos-and-video-with-mediacapture.md), в которой описаны этапы реализации основных принципов фото- и видеозахвата. Мы рекомендуем ознакомиться с базовым шаблоном захвата мультимедиа в этой статье, прежде чем перейти к более сложным сценариям захвата. Код в этой статье подразумевает, что ваше приложение уже содержит экземпляр MediaCapture, инициализированный надлежащим образом.

## Настройка приложения для захвата переменной последовательности фотографий

Помимо пространств имен, необходимых для основных функций захвата мультимедиа, для реализации функции захвата переменной последовательности фотографий необходимы указанные ниже пространства имен.

[!code-cs[VPSUsing](./code/BasicMediaCaptureWin10/cs/MainPage.xaml.cs#SnippetVPSUsing)]

Объявите переменную-член для хранения объекта [**VariablePhotoSequenceCapture**](https://msdn.microsoft.com/library/windows/apps/dn652564), который используется для инициализации функции захвата последовательности фотографий. Объявите массив объектов [**SoftwareBitmap**](https://msdn.microsoft.com/library/windows/apps/dn887358) для хранения каждого захваченного изображения в последовательности. Кроме того, объявите массив для хранения объекта [**CapturedFrameControlValues**](https://msdn.microsoft.com/library/windows/apps/dn608020) для каждого кадра. Он может использоваться в вашем алгоритме обработки изображений для определения того, какие параметры были использованы для захвата каждого кадра. И, наконец, объявите индекс, который будет использоваться для отслеживания того, какое изображение в последовательности захватывается в данный момент.

[!code-cs[VPSMemberVariables](./code/BasicMediaCaptureWin10/cs/MainPage.xaml.cs#SnippetVPSMemberVariables)]

## Подготовка к захвату переменной последовательности фотографий

После успешной инициализации [MediaCapture](capture-photos-and-video-with-mediacapture.md) убедитесь, что текущее устройство поддерживает захват переменных последовательностей фотографий. Для этого получите экземпляр [**VariablePhotoSequenceController**](https://msdn.microsoft.com/library/windows/apps/dn640573) из объекта [**VideoDeviceController**](https://msdn.microsoft.com/library/windows/apps/br226825) для захвата мультимедиа и проверьте свойство [**Supported**](https://msdn.microsoft.com/library/windows/apps/dn640580).

[!code-cs[IsVPSSupported](./code/BasicMediaCaptureWin10/cs/MainPage.xaml.cs#SnippetIsVPSSupported)]

Получите объект [**FrameControlCapabilities**](https://msdn.microsoft.com/library/windows/apps/dn652548) из контроллера переменной последовательности фотографий. У этого объекта имеется свойство для каждого параметра, который можно настроить для каждого кадра в последовательности фотографий. К ним относятся следующие:

-   [**Exposure**](https://msdn.microsoft.com/library/windows/apps/dn652552)
-   [**ExposureCompensation**](https://msdn.microsoft.com/library/windows/apps/dn652560)
-   [**Flash**](https://msdn.microsoft.com/library/windows/apps/dn652566)
-   [**Focus**](https://msdn.microsoft.com/library/windows/apps/dn652570)
-   [**IsoSpeed**](https://msdn.microsoft.com/library/windows/apps/dn652574)
-   [**PhotoConfirmation**](https://msdn.microsoft.com/library/windows/apps/dn652578)

В этом примере показано, как настроить разные значения коррекции экспозиции для каждого кадра. Чтобы убедиться, что функция коррекции экспозиции поддерживается для последовательности фотографий в текущем устройстве, проверьте свойство [**Supported**](https://msdn.microsoft.com/library/windows/apps/dn278905) объекта [**FrameExposureCompensationCapabilities**](https://msdn.microsoft.com/library/windows/apps/dn652628), к которому можно получить доступ с помощью свойства **ExposureCompensation**.

[!code-cs[IsExposureCompensationSupported](./code/BasicMediaCaptureWin10/cs/MainPage.xaml.cs#SnippetIsExposureCompensationSupported)]

Создайте объект [**FrameController**](https://msdn.microsoft.com/library/windows/apps/dn652582) для каждого кадра, который необходимо захватить. В этом примере выполняется захват трех кадров. Задайте значения для элементов управления, которые необходимо изменять для каждого кадра. Затем очистите коллекцию [**DesiredFrameControllers**](https://msdn.microsoft.com/library/windows/apps/dn640574) объекта **VariablePhotoSequenceController** и добавьте в коллекцию контроллер каждого кадра.

[!code-cs[InitFrameControllers](./code/BasicMediaCaptureWin10/cs/MainPage.xaml.cs#SnippetInitFrameControllers)]

Создайте объект [**ImageEncodingProperties**](https://msdn.microsoft.com/library/windows/apps/hh700993), чтобы настроить кодировку, которую необходимо использовать для захваченных изображений. Вызовите статический метод [**MediaCapture.PrepareVariablePhotoSequenceCaptureAsync**](https://msdn.microsoft.com/library/windows/apps/dn608097), передав ему свойства кодирования. Этот метод возвращает объект [**VariablePhotoSequenceCapture**](https://msdn.microsoft.com/library/windows/apps/dn652564). И, наконец, зарегистрируйте обработчики событий [**PhotoCaptured**](https://msdn.microsoft.com/library/windows/apps/dn652573) и [**Stopped**](https://msdn.microsoft.com/library/windows/apps/dn652585).

[!code-cs[PrepareVPS](./code/BasicMediaCaptureWin10/cs/MainPage.xaml.cs#SnippetPrepareVPS)]

## Запуск захвата переменной последовательности фотографий

Чтобы запустить захват переменной последовательности фотографий, вызовите метод [**VariablePhotoSequenceCapture.StartAsync**](https://msdn.microsoft.com/library/windows/apps/dn652577). Обязательно инициализируйте массивы для хранения захваченных изображений и значений элемента управления для работы с кадрами и присвойте текущему индексу значение 0. В приложении настройте переменную состояния записи и обновите пользовательский интерфейс, чтобы в процессе захвата невозможно было запустить еще одну операцию захвата.

[!code-cs[StartVPSCapture](./code/BasicMediaCaptureWin10/cs/MainPage.xaml.cs#SnippetStartVPSCapture)]

## Получение захваченных кадров

Для каждого захваченного кадра создается событие [**PhotoCaptured**](https://msdn.microsoft.com/library/windows/apps/dn652573). Сохраните значения элемента управления для работы с кадрами и захваченное изображение для кадра, а затем увеличьте текущий индекс кадра на единицу. В этом примере показано, как получить представление [**SoftwareBitmap**](https://msdn.microsoft.com/library/windows/apps/dn887358) для каждого кадра. Дополнительные сведения об использовании **SoftwareBitmap** см. в статье [Обработка изображений](imaging.md).

[!code-cs[OnPhotoCaptured](./code/BasicMediaCaptureWin10/cs/MainPage.xaml.cs#SnippetOnPhotoCaptured)]

## Обработка завершения захвата переменной последовательности фотографий

После захвата всех кадров последовательности создается событие [**Stopped**](https://msdn.microsoft.com/library/windows/apps/dn652585). Обновите состояние записи вашего приложения и пользовательский интерфейс, чтобы пользователь мог инициировать новые операции захвата. На этом этапе можно передать захваченные изображения и значения элемента управления для работы с кадрами в код обработки изображений.

[!code-cs[OnStopped](./code/BasicMediaCaptureWin10/cs/MainPage.xaml.cs#SnippetOnStopped)]

## Обновление контроллеров кадров

Если вам необходимо захватить еще одну переменную последовательность фотографий с различными параметрами для каждого кадра, то нет необходимости выполнять полную повторную инициализацию **VariablePhotoSequenceCapture**. Достаточно либо очистить коллекцию [**DesiredFrameControllers**](https://msdn.microsoft.com/library/windows/apps/dn640574) и добавить новые контроллеры кадров, либо изменить существующие значения контроллера кадра. В примере ниже показано, как проверить объект [**FrameFlashCapabilities**](https://msdn.microsoft.com/library/windows/apps/dn652657), чтобы убедиться, что текущее устройство поддерживает вспышку и управление ее мощностью для кадров переменной последовательности фотографий. Если это так, то каждый кадр будет обновлен, чтобы использовать вспышку на 100% ее мощности. Значения коррекции экспозиции, ранее установленные для каждого кадра, будут по-прежнему активными.

[!code-cs[UpdateFrameControllers](./code/BasicMediaCaptureWin10/cs/MainPage.xaml.cs#SnippetUpdateFrameControllers)]

## Очистка захвата переменной последовательности фотографий

После окончания захвата переменных последовательностей фотографий или в случае приостановки вашего приложения очистите объект переменной последовательности фотографий, вызвав метод [**FinishAsync**](https://msdn.microsoft.com/library/windows/apps/dn652569). Отмените регистрацию обработчиков событий для объекта и присвойте ему значение null.

[!code-cs[CleanUpVPS](./code/BasicMediaCaptureWin10/cs/MainPage.xaml.cs#SnippetCleanUpVPS)]

## Ссылки по теме

* [Захват фотографий и видео с помощью MediaCapture](capture-photos-and-video-with-mediacapture.md)
 

 







<!--HONumber=Jun16_HO4-->


