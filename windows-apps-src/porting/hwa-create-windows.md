---
author: seksenov
title: "Размещенные веб-приложения. Преобразование веб-приложения в приложение для Windows с помощью Visual Studio"
description: "Веб-сайт можно превратить в приложение UWP для Windows 10 с помощью Visual Studio"
kw: Hosted Web Apps tutorial, Porting to Windows 10 with Visual Studio, How to convert website to Windows, How to add website to Windows Store, Packaging web application for Microsoft Store, Test Windows 10 native features and runtime APIs with CodePen, How to use Windows Cortana Live Tiles Built-in Camera on my Website with remote JavaScript
translationtype: Human Translation
ms.sourcegitcommit: 9dc441422637fe6984f0ab0f036b2dfba7d61ec7
ms.openlocfilehash: 1cb4757896c9fecf05224a26949cd6e2ddffdb45

---

# <a name="convert-your-web-application-to-a-universal-windows-platform-uwp-app"></a>Преобразование веб-приложения в приложение универсальной платформы Windows (UWP)

Узнайте, как быстро создать приложение UWP для Windows 10, имея только URL-адрес веб-сайта. 

> [!NOTE]
> Следующие инструкции рассчитаны на применение на платформе разработки для Windows. Пользователям компьютеров Mac следует ознакомиться с разделом [Инструкции по использованию платформы разработки для Mac](/hwa-create-mac.md).

## <a name="what-you-need-to-develop-on-windows"></a>Необходимые инструменты для разработки на Windows

- [Visual Studio 2015.](https://www.visualstudio.com/) Бесплатная полнофункциональная среда Visual Studio Community 2015 включает в себя средства разработчика для Windows 10, шаблоны универсальных приложений, редактор кода, мощный отладчик, эмуляторы Windows Mobile. Также поддерживается большое число языков и множество других функций, готовых для использования в рабочей среде.
- (Необязательно) [Изолированный пакет Windows SDK для Windows 10.](https://dev.windows.com/downloads/windows-10-sdk) Если в качестве среды разработки используется не Visual Studio 2015, можно загрузить установщик изолированного пакета SDK для Windows 10. Обратите внимание, что вам не нужно устанавливать пакет SDK, если вы используете среду Visual Studio 2015, так как этот пакет уже входит в нее.

## <a name="step-1-pick-a-website-url"></a>Шаг 1. Выберите URL-адрес веб-сайта
Выберите существующий веб-сайт, который будет отлично работать в качестве одностраничного приложения. Крайне желательно, чтобы вы были владельцем или разработчиком сайта — тогда вы сможете вносить любые необходимые изменения. Если не можете подобрать подходящий URL-адрес, используйте в качестве веб-сайта вот этот [пример Codepen](http://codepen.io/seksenov/pen/wBbVyb/?editors=101). Скопируйте URL-адрес вашего сайта или URL-адрес примера Codepen, чтобы использовать его в ходе работы с этим руководством. 

![Шаг 1. Выберите URL-адрес веб-сайта](images/hwa-to-uwp/windows_step1.png)

## <a name="step-2-create-a-blank-javascript-app"></a>Шаг 2. Создайте пустое приложение JavaScript.

Запустите Visual Studio.
1. Щелкните пункт **Файл**.
2. Щелкните **Создать проект**.
3. В разделе **JavaScript** перейдите в раздел **Универсальное приложение Windows** и выберите **Пустое приложение (универсальное приложение Windows)**.

![Шаг 2. Создайте пустое приложение JavaScript.](images/hwa-to-uwp/windows_step2.png)

## <a name="step-3-delete-any-packaged-code"></a>Шаг 3. Удалите весь пакетный код

Так как это размещенное веб-приложение, содержимое которого предоставляется удаленным сервером, вам не потребуется большая часть локальных файлов приложения, которые присутствуют в составе шаблона JavaScript по умолчанию. Удалите все локальные HTML-, JavaScript- или CSS-ресурсы. Все, что должно остаться, — это файл `package.appxmanifest`, с помощью которого настраивается приложение, и ресурсы изображений.

![Шаг 3. Удалите весь пакетный код](images/hwa-to-uwp/windows_step3.png)

## <a name="step-4-set-the-start-page-url"></a>Шаг 4. Задайте URL-адрес начальной страницы

1. Откройте файл `package.appxmanifest`.
2. На вкладке **Приложение** найдите текстовое поле **Начальная страница**.
3. Замените `default.html` на URL-адрес веб-сайта.

![Шаг 4. Задайте URL-адрес начальной страницы](images/hwa-to-uwp/windows_step4.png)

## <a name="step-5-define-the-boundaries-of-your-web-app"></a>Шаг 5. Определите границы веб-приложения

Правила универсального кода ресурса для содержимого приложения (ACUR) определяют, каким удаленным URL-адресам разрешен доступ к вашему приложению и универсальным API для Windows. Правила ACUR потребуется добавить как минимум для начальной страницы и всех ресурсов, используемых этой страницей. Дополнительные сведения об ACUR [см. здесь](./hwa-access-features.md).
1. Откройте файл `package.appxmanifest`.
2. Выберите вкладку **URI содержимого**.
3. Добавьте все необходимые URI для начальной страницы.

Пример.
```
1. http://codepen.io/seksenov/pen/wBbVyb/?editors=101
2. http://*.codepen.io/
```
4. Для параметра **Доступ WinRT** каждого добавленного URI установите значение **Все**.

![Шаг 5. Определите границы веб-приложения](images/hwa-to-uwp/windows_step5.png)

## <a name="step-6-run-your-app"></a>Шаг 6. Запустите приложение

Теперь у вас есть полнофункциональное приложение для Windows 10, имеющее доступ к универсальным API для Windows.

Если вы используете пример Codepen, нажмите кнопку **Toast Notification** (Всплывающее уведомление), чтобы вызвать API Windows из размещенного сценария.

![Шаг 6. Запустите приложение](images/hwa-to-uwp/windows_step6.png)

## <a name="bonus-add-camera-capture"></a>Дополнительно: добавление захвата с камеры

Чтобы включить захват с камеры, скопируйте и вставьте приведенный ниже код на JavaScript. Если вы используете собственный веб-сайт, создайте кнопку для вызова метода `cameraCapture()`. Если вы используете пример Codepen, кнопка уже присутствует в коде HTML. Нажмите кнопку и сделайте снимок.

```JavaScript
function cameraCapture() {
  if(typeof Windows != 'undefined') {
   var captureUI = new Windows.Media.Capture.CameraCaptureUI();
   //Set the format of the picture that's going to be captured (.png, .jpg, ...)
   captureUI.photoSettings.format = Windows.Media.Capture.CameraCaptureUIPhotoFormat.png;
   //Pop up the camera UI to take a picture
   captureUI.captureFileAsync(Windows.Media.Capture.CameraCaptureUIMode.photo).then(function (capturedItem) {
      // Do something with the picture
   });
  }
}
```

## <a name="related-topics"></a>Связанные разделы

- [Улучшение веб-приложения путем обращения к функциям универсальной платформы Windows (UWP)](hwa-access-features.md)
- [Руководство по работе с приложениями универсальной платформы Windows (UWP)](http://go.microsoft.com/fwlink/p/?LinkID=397871)
- [Загружаемые ресурсы проектирования для приложений Магазина Windows](https://msdn.microsoft.com/library/windows/apps/xaml/bg125377.aspx)



<!--HONumber=Dec16_HO1-->


