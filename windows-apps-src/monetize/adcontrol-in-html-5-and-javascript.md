---
author: mcleanbyron
ms.assetid: adb2fa45-e18f-4254-bd8b-a749a386e3b4
description: "Узнайте, как использовать класс AdControl для показа баннерной рекламы в приложении на JavaScript или HTML для Windows 10 (UWP), Windows 8.1 или Windows Phone 8.1."
title: "AdControl в HTML 5 и JavaScript"
ms.author: mcleans
ms.date: 02/08/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: "Windows 10, uwp, рекламы, реклама, AdControl, javascript, HTML"
translationtype: Human Translation
ms.sourcegitcommit: c6b64cff1bbebc8ba69bc6e03d34b69f85e798fc
ms.openlocfilehash: cda74aaf6301f0cc04c5a9ae5c2aad5cf43d8b7e
ms.lasthandoff: 02/07/2017

---

# <a name="adcontrol-in-html-5-and-javascript"></a>AdControl в HTML 5 и JavaScript

В этом пошаговом руководстве рассказывается, как использовать класс [AdControl](https://msdn.microsoft.com/library/windows/apps/microsoft.advertising.winrt.ui.adcontrol.aspx) для показа баннерной рекламы в приложении на JavaScript или HTML для Windows 10 (UWP), Windows 8.1 или Windows Phone 8.1. В этом руководстве не используется **AdMediatorControl** и рекламный посредник.

Полный пример с проектом, демонстрирующим способы добавления баннерной рекламы в приложение на JavaScript и HTML, см. в разделе [примеров рекламы на GitHub](http://aka.ms/githubads).

## <a name="prerequisites"></a>Необходимые компоненты


* Для приложений UWP: установите [пакет SDK служб Microsoft Store](http://aka.ms/store-em-sdk) с Visual Studio 2015.
* Для приложений для Windows 8.1 и Windows Phone 8.1 установите [пакет Microsoft Advertising SDK для Windows и Windows Phone 8.x](http://aka.ms/store-8-sdk) с Visual Studio 2015 или Visual Studio 2013.

> **Примечание.**&nbsp;&nbsp;Если вы установили предварительную сборку Windows 10 Anniversary SDK 14295 или более позднюю версию в Visual Studio 2015, вам также потребуется установить библиотеку WinJS. Эта библиотека ранее включалась в предыдущие версии Windows SDK для Windows 10, но, начиная с предварительной сборки Windows 10 Anniversary SDK 14295, ее необходимо устанавливать отдельно. Сведения по установке WinJS см. в разделе [Скачать WinJS](http://try.buildwinjs.com/download/GetWinJS/).

## <a name="code-development"></a>Написание кода

1. В Visual Studio откройте свой проект либо создайте новый.

2. Если ваш проект направлен на работу на **Любом ЦП**, обновите его, чтобы он использовал результаты сборки, предназначенные для определенной архитектуры (например **x86**). Если ваш проект направлен на работу на **Любом ЦП**, вам не удастся надлежащим образом добавить ссылку на Microsoft Advertising в приведенных ниже шагах. Дополнительные сведения см. в разделе [Ошибки, вызванные указанием Любого ЦП как целевого в вашем проекте](known-issues-for-the-advertising-libraries.md#reference_errors).

3.  В **Обозревателе решений** щелкните правой кнопкой мыши элемент **Ссылки** и выберите **Добавить ссылку...**.

4.  В **Диспетчере ссылок** выберите одну из следующих ссылок в зависимости от типа проекта.

    -   Для проекта универсальной платформы Windows (UWP): разверните **Универсальные для Windows**, щелкните **Расширения**, а затем установите флажок **Microsoft Advertising SDK для JavaScript** (версия 10.0).

    -   Для проекта Windows 8.1: разверните **Windows 8.1**, щелкните **Расширения**, а затем установите флажок **Собственный Microsoft Advertising SDK для Windows 8.1 (JS)**.

    -   Для проекта Windows 8.1: разверните **Windows Phone 8.1**, щелкните **Расширения**, а затем установите флажок **Собственный Microsoft Advertising SDK для Windows Phone 8.1 (JS)**.

    ![javascriptaddreference](images/13-f7f6d6a6-161e-4f17-995d-1236d0b5d9f2.png)

    > **Примечание.**&nbsp;&nbsp;Это изображение относится к Visual Studio 2015, где создается проект UWP для Windows 10. Если выполняется сборка приложения для Windows 8.1 или Windows Phone 8.1 либо с использованием Visual Studio 2013, экран будет выглядеть иначе.

5.  В **диспетчере ссылок** нажмите "ОК".

6.  Откройте файл index.html (или другой html-файл, если это необходимо для вашего проекта).

7.  В разделе **&lt;head&gt;** после ссылок на JavaScript проекта из default.css и main.js добавьте ссылку на ad.js.

  В проекте UWP добавьте следующий код.

  > [!div class="tabbedCodeSnippets"]
  ``` html
  <!-- Microsoft advertising required references -->
  <script src="//Microsoft.Advertising.JavaScript/ad.js"></script>
  ```

  В проекте для Windows 8.1 или Windows Phone 8.1 добавьте следующий код.

  > [!div class="tabbedCodeSnippets"]
  ``` html
  <!-- Microsoft advertising required references -->
  <script src="/MSAdvertisingJS/ads/ad.js"></script>
  ```

  <span/>
  >**Примечание.**&nbsp;&nbsp;Эта строка должна располагаться в разделе **&lt;head&gt;** после включения main.js; в противном случае будет выдано сообщение об ошибке при сборке проекта. Если ваш проект ориентирован на Windows 8.1 или Windows Phone 8.1, файл HTML по умолчанию в вашем проекте называется default.html, а не index.html, а файл по умолчанию JavaScript в вашем проекте — default.js, а не main.js.

8.  Измените раздел **&lt;body&gt;** в файле default.html (или другом html-файле, если это необходимо для вашего проекта), включив в него элемент div для **AdControl**. Задайте свойства **applicationId** и **adUnitId** в **AdControl**, чтобы проверить значения, предоставляемые в разделе [Значения тестового режима](test-mode-values.md), и измените высоту и ширину этого элемента управления, чтобы он соответствовал одному из [поддерживаемых размеров баннерной рекламы](supported-ad-sizes-for-banner-ads.md).

  > **Примечание.**&nbsp;&nbsp;Перед отправкой приложения на проверку необходимо будет заменить тестовые значения **applicationId** и **adUnitId** действительными значениями.

  > [!div class="tabbedCodeSnippets"]
  ``` html
  <div id="myAd" style="position: absolute; top: 50px; left: 0px; width: 300px; height: 250px; z-index: 1"
        data-win-control="MicrosoftNSJS.Advertising.AdControl"
        data-win-options="{applicationId: '3f83fe91-d6be-434d-a0ae-7351c5a997f1', adUnitId: '10865270'}">
  </div>
  ```

9.  Скомпилируйте и запустите приложение, чтобы увидеть его с объявлением.

## <a name="release-your-app-with-live-ads-using-windows-dev-center"></a>Выпуск приложения с динамической рекламой с помощью Центра разработки для Windows


1.  На информационной панели Центра разработки, перейдите на страницу **Получение дохода** &gt; **Получение дохода с помощью рекламы** вашего приложения и [создайте автономный блок Microsoft Advertising](../publish/monetize-with-ads.md). В качестве типа рекламного блока укажите **Баннер**. Запишите и идентификатор рекламного блока, и идентификатор приложения.

2.  В своем коде замените тестовые значения (**applicationId** и **adUnitId**) рекламного блока действительными значениями, сгенерированными в Центре разработки.

3.  [Отправьте приложение](../publish/app-submissions.md) в Магазин с помощью информационной панели в Центре разработки.

4.  Изучите [отчеты по показателям рекламы](../publish/advertising-performance-report.md) на информационной панели Центра разработки.

## <a name="complete-indexhtml-for-a-sample-uwp-project"></a>Полный код index.html для примера проекта UWP

> [!div class="tabbedCodeSnippets"]
``` html
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8" />
    <title>AdControlExampleApp</title>
    <!-- WinJS references -->
    <link href="lib/winjs-4.0.1/css/ui-light.css" rel="stylesheet" />
    <script src="lib/winjs-4.0.1/js/base.js"></script>
    <script src="lib/winjs-4.0.1/js/ui.js"></script>
    <!-- AdControlExampleApp references -->
    <link href="css/default.css" rel="stylesheet" />
    <script src="js/main.js"></script>
    <!-- Required reference for AdControl -->
    <script src="//Microsoft.Advertising.JavaScript/ad.js"></script>
</head>
<body>
    <div id="myAd" style="position: absolute; top: 50px; left: 0px; width: 300px; height: 250px; z-index: 1"
      data-win-control="MicrosoftNSJS.Advertising.AdControl"
      data-win-options="{applicationId: '3f83fe91-d6be-434d-a0ae-7351c5a997f1', adUnitId: '10865270'}">
    </div>
    <p>Content goes here</p>
</body>
</html>
```

## <a name="related-topics"></a>Связанные статьи

* [Примеры рекламы на GitHub](http://aka.ms/githubads)
 

 

