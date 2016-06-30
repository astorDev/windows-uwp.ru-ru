---
author: drewbatgit
ms.assetid: 9BA3F85A-970F-411C-ACB1-B65768B8548A
description: "В этой статье описывается, как быстро вывести поток предварительного просмотра камеры на странице XAML в приложении универсальной платформы Windows (UWP)."
title: "Простой доступ к просмотру камеры"
translationtype: Human Translation
ms.sourcegitcommit: 72abc006de1925c3c06ecd1b78665e72e2ffb816
ms.openlocfilehash: 05e752925c07b0e3720fbdd42d785381aa08b99c

---

# Простой доступ к просмотру камеры

\[ Обновлено для приложений UWP в Windows 10. Статьи о Windows 8.x см. в [архиве](http://go.microsoft.com/fwlink/p/?linkid=619132) \]

В этой статье описывается, как быстро вывести поток предварительного просмотра камеры на странице XAML в приложении универсальной платформы Windows (UWP). Для создания приложения, захватывающего фотографии и видео с камеры, необходимо выполнить такие задачи, как обработка положения устройства и ориентации камеры и настройка параметров кодирования записанного файла. Для некоторых сценариев приложения может потребоваться просто отобразить поток предварительного просмотра камеры, не беспокоясь о других вопросах. В данной статье показано, как это сделать с применением минимального количества кода. Обратите внимание, что необходимо всегда надлежащим образом завершать поток предварительного просмотра после окончания работы с ним, выполняя приведенные ниже действия.

Подробнее о создании приложения для захвата фотографий и видео с камеры, см. [Фото- и видеозахват с помощью MediaCapture](capture-photos-and-video-with-mediacapture.md).

## Добавление объявлений возможностей в манифест приложения

Чтобы ваше приложение получило доступ к камере устройства, необходимо объявить, что оно использует возможности устройства *веб-камеры* и *микрофона*. Если нужно сохранить захваченные фотографии и видео в библиотеке изображений или видео пользователя, следует также объявить возможности *picturesLibrary* и *videosLibrary*.

**Добавление возможностей в манифест приложения**

1.  В Microsoft Visual Studio откройте конструктор манифеста приложения, дважды щелкнув элемент **package.appxmanifest** в **Обозревателе решений**.
2.  Перейдите на вкладку **Возможности**.
3.  Выставьте флажок для пункта **Веб-камера** и поле для параметра **Микрофон**.
4.  Для доступа к библиотеке изображений и видео установите флажки **Библиотека изображений** и **Библиотека видео**.

## Добавление объекта CaptureElement на страницу

Используйте объект [**CaptureElement**](https://msdn.microsoft.com/library/windows/apps/br209278) для отображения потока предварительного просмотра на странице XAML.

[!code-xml[CaptureElement](./code/SimpleCameraPreview_Win10/cs/MainPage.xaml#SnippetCaptureElement)]

## Использование объекта MediaCapture для запуска потока предварительного просмотра

Объект [**MediaCapture**](https://msdn.microsoft.com/library/windows/apps/br241124) — это интерфейс приложения для взаимодействия с камерой устройства. Этот класс входит в пространство имен Windows.Media.Capture. В описанном в этой статье примере используются API из пространств имен [**Windows.ApplicationModel**](https://msdn.microsoft.com/library/windows/apps/br224691) и [System.Threading.Tasks](https://msdn.microsoft.com/library/windows/apps/xaml/system.threading.tasks.aspx) в дополнение ко включенным в шаблон проекта по умолчанию.

Добавьте директивы using, чтобы включить следующие пространства имен в файл .cs вашей страницы.

[!code-cs[SimpleCameraPreviewUsing](./code/SimpleCameraPreview_Win10/cs/MainPage.xaml.cs#SnippetSimpleCameraPreviewUsing)]

Объявите переменную класса для объекта **MediaCapture**.

[!code-cs[DeclareMediaCapture](./code/SimpleCameraPreview_Win10/cs/MainPage.xaml.cs#SnippetDeclareMediaCapture)]

Создайте новый экземпляр класса **MediaCapture** и вызовите метод [**InitializeAsync**](https://msdn.microsoft.com/library/windows/apps/br226598), чтобы инициализировать устройство захвата. Этот метод может завершиться ошибкой, например, на устройствах, не оборудованных камерой, поэтому его необходимо вызывать из блока **try**. Если пользователь отключил доступ к камере в параметрах конфиденциальности устройства, при попытке инициализировать камеру будет вызвано исключение **UnauthorizedAccessException**. Вы также увидите это исключение во время разработки, если в манифест приложения не были добавлены необходимые возможности.

**Важно!** На устройствах некоторых семейств перед тем, как приложение получит доступ к камере устройства, отображается запрос на согласие пользователя. По этой причине следует вызывать только [**MediaCapture.InitializeAsync**](https://msdn.microsoft.com/library/windows/apps/br226598) из основного потока пользовательского интерфейса. Попытка инициализировать камеру через другой поток может привести к сбою инициализации.

Подключите **MediaCapture** к **CaptureElement**, задав свойство [**Source**](https://msdn.microsoft.com/library/windows/apps/br209280). И наконец, запустите предварительный просмотр путем вызова метода [**StartPreviewAsync**](https://msdn.microsoft.com/library/windows/apps/br226613).

[!code-cs[StartPreviewAsync](./code/SimpleCameraPreview_Win10/cs/MainPage.xaml.cs#SnippetStartPreviewAsync)]


## Завершение потока предварительного просмотра

Каждый раз, закончив использование потока предварительного просмотра, необходимо завершить поток и очистить ресурсы захвата, чтобы камера была доступна для использования другим приложениям на устройстве. Для завершения потока предварительного просмотра необходимо выполнить следующие действия:

-   вызвать метод [**StopPreviewAsync**](https://msdn.microsoft.com/library/windows/apps/br226622) для остановки потока предварительного просмотра;
-   установить для свойства [**Source**](https://msdn.microsoft.com/library/windows/apps/br209280) класса **CaptureElement** значение null;
-   вызвать метод [**Dispose**](https://msdn.microsoft.com/library/windows/apps/dn278858) объекта **MediaCapture**, чтобы освободить ресурсы объекта;
-   присвоить переменной-члену **MediaCapture** значение null.

[!code-cs[CleanupCameraAsync](./code/SimpleCameraPreview_Win10/cs/MainPage.xaml.cs#SnippetCleanupCameraAsync)]

Когда пользователь уходит со страницы, остановите поток предварительного просмотра путем переопределения метода [**OnNavigatedFrom**](https://msdn.microsoft.com/library/windows/apps/br227507).

[!code-cs[OnNavigatedFrom](./code/SimpleCameraPreview_Win10/cs/MainPage.xaml.cs#SnippetOnNavigatedFrom)]

Также необходимо надлежащим образом завершать поток предварительного просмотра в случае приостановки вашего приложения. Для этого зарегистрируйте обработчик для события [**Application.Suspending**](https://msdn.microsoft.com/library/windows/apps/br205860) в конструкторе страницы.

[!code-cs[RegisterSuspending](./code/SimpleCameraPreview_Win10/cs/MainPage.xaml.cs#SnippetRegisterSuspending)]

Сначала проверьте в обработчике событий **Suspending**, что страница отображается в классе приложения [**Frame**](https://msdn.microsoft.com/library/windows/apps/br242682), сравнив тип страницы со свойством [**CurrentSourcePageType**](https://msdn.microsoft.com/library/windows/apps/hh702390). Если страница в настоящее время не отображается, событие **OnNavigatedFrom** уже должно быть вызвано, а поток предварительного просмотра остановлен. Если страница отображается, получите объект [**SuspendingDeferral**](https://msdn.microsoft.com/library/windows/apps/br224684) из переданных в обработчик аргументов события, чтобы предотвратить приостановку приложения системой до остановки потока предварительного просмотра. После остановки потока вызовите метод отсрочки [**Complete**](https://msdn.microsoft.com/library/windows/apps/br224685), чтобы разрешить системе продолжить приостановку приложения.

[!code-cs[SuspendingHandler](./code/SimpleCameraPreview_Win10/cs/MainPage.xaml.cs#SnippetSuspendingHandler)]

## Захват неподвижных изображений из потока предварительного просмотра

Получить неподвижное изображение из потока предварительного просмотра захвата мультимедиа в виде [**SoftwareBitmap**](https://msdn.microsoft.com/library/windows/apps/dn887358) несложно. Подробнее об этом см. в разделе [Получение кадра предварительного просмотра](get-a-preview-frame.md).

## Ссылки по теме

* [Фото- и видеосъемка с помощью MediaCapture](capture-photos-and-video-with-mediacapture.md)
* [Получение кадра предварительного просмотра](get-a-preview-frame.md)



<!--HONumber=Jun16_HO4-->


