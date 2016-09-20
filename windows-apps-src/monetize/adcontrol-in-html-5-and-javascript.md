---
author: mcleanbyron
ms.assetid: adb2fa45-e18f-4254-bd8b-a749a386e3b4
description: "Узнайте, как использовать класс AdControl для показа баннерной рекламы в приложении на JavaScript или HTML для Windows 10 (UWP), Windows 8.1 или Windows Phone 8.1."
title: "AdControl в HTML 5 и JavaScript"
ms.sourcegitcommit: cf695b5c20378f7bbadafb5b98cdd3327bcb0be6
ms.openlocfilehash: 6e96b085132126a2c3e7b0b0b86124aba4cd651e

---

# AdControl в HTML 5 и JavaScript


\[ Обновлено для приложений UWP в Windows 10. Статьи для Windows 8.x см. в [архиве](http://go.microsoft.com/fwlink/p/?linkid=619132) \]

В этом пошаговом руководстве рассказывается, как использовать класс [AdControl](https://msdn.microsoft.com/library/windows/apps/microsoft.advertising.winrt.ui.adcontrol.aspx) для показа баннерной рекламы в приложении на JavaScript или HTML для Windows 10 (UWP), Windows 8.1 или Windows Phone 8.1. В этом руководстве не используется **AdMediatorControl** и рекламный посредник.

Полный пример с проектом, демонстрирующим способы добавления баннерной рекламы в приложение на JavaScript и HTML, см. в разделе [примеров рекламы на GitHub](http://aka.ms/githubads).

## Необходимые условия


* Установите [пакет SDK Microsoft Store Engagement and Monetization](http://aka.ms/store-em-sdk) для Visual Studio 2015 или Visual Studio 2013.

> 
            **Примечание** Если вы установили предварительную сборку Windows 10 Anniversary SDK 14295 или более позднюю версию в Visual Studio 2015, вам также потребуется установить библиотеку WinJS. Эта библиотека ранее включалась в предыдущие версии Windows SDK для Windows 10, но, начиная с предварительной сборки Windows 10 Anniversary SDK 14295, ее необходимо устанавливать отдельно. Сведения по установке WinJS см. в разделе [Скачать WinJS](http://try.buildwinjs.com/download/GetWinJS/).

## Написание кода

1. В Visual Studio откройте свой проект либо создайте новый.

2. Если ваш проект направлен на работу на **Любом ЦП**, обновите его, чтобы он использовал результаты сборки, предназначенные для определенной архитектуры (например **x86**). Если ваш проект направлен на работу на **Любом ЦП**, вам не удастся надлежащим образом добавить ссылку на Microsoft Advertising в приведенных ниже шагах. Дополнительные сведения см. в разделе [Ошибки, вызванные указанием Любого ЦП как целевого в вашем проекте](known-issues-for-the-advertising-libraries.md#reference_errors).

3.  В **Обозревателе решений** щелкните правой кнопкой мыши элемент **Ссылки** и выберите **Добавить ссылку...**.

4.  В **Диспетчере ссылок** выберите одну из следующих ссылок в зависимости от типа проекта.

    -   Для проекта универсальной платформы Windows (UWP): разверните **Универсальные для Windows**, щелкните **Расширения**, а затем установите флажок **Microsoft Advertising SDK для JavaScript** (версия 10.0).

    -   Для проекта Windows 8.1: разверните **Windows 8.1**, щелкните **Расширения**, а затем установите флажок **Собственный Microsoft Advertising SDK для Windows 8.1 (JS)**.

    -   Для проекта Windows 8.1: разверните **Windows Phone 8.1**, щелкните **Расширения**, а затем установите флажок **Собственный Microsoft Advertising SDK для Windows Phone 8.1 (JS)**.

    ![javascriptaddreference](images/13-f7f6d6a6-161e-4f17-995d-1236d0b5d9f2.png)

    > 
            **Примечание**  Это изображение относится к Visual Studio 2015, где создается проект UWP для Windows 10. Если выполняется сборка приложения для Windows 8.1 или Windows Phone 8.1 либо с использованием Visual Studio 2013, экран будет выглядеть иначе.

5.  В **Диспетчере ссылок** нажмите "ОК".

6.  Откройте файл default.html (или другой html-файл, если это необходимо для вашего проекта).

7.  В разделе **&lt;head&gt;** после ссылок на JavaScript проекта из default.css и default.js добавьте ссылку на ad.js.

    В проекте UWP добавьте следующий код.

    ``` syntax
    <!-- Microsoft advertising required references -->
    <script src="//Microsoft.Advertising.JavaScript/ad.js"></script>
    ```

    В проекте для Windows 8.1 или Windows Phone 8.1 добавьте следующий код.

    ``` syntax
    <!-- Microsoft advertising required references -->
    <script src="/MSAdvertisingJS/ads/ad.js"></script>
    ```

    > 
            **Примечание**   Эта строка должна располагаться в разделе **&lt;head&gt;** после включения default.js; в противном случае будет выдано сообщение об ошибке при сборке проекта.

8.  Измените раздел **&lt;body&gt;** в файле default.html (или другом html-файле, если это необходимо для вашего проекта), включив в него элемент div для **AdControl**. Задайте свойства **applicationId** и **adUnitId** в **AdControl**, чтобы проверить значения, предоставляемые в разделе [Значения тестового режима](test-mode-values.md), и измените высоту и ширину этого элемента управления, чтобы он соответствовал одному из [поддерживаемых размеров баннерной рекламы](supported-ad-sizes-for-banner-ads.md).

    > **Примечание**  
    Перед отправкой приложения на проверку необходимо будет заменить тестовые значения **applicationId** и **adUnitId** действительными значениями.

    ``` syntax
    <div id="myAd" style="position: absolute; top: 50px; left: 0px; width: 300px; height: 250px; z-index: 1"
          data-win-control="MicrosoftNSJS.Advertising.AdControl"
          data-win-options="{applicationId: '3f83fe91-d6be-434d-a0ae-7351c5a997f1', adUnitId: '10865270'}">
    </div>
    ```

9.  Скомпилируйте и запустите приложение, чтобы увидеть его с объявлением.

## Выпуск приложения с динамической рекламой с помощью Центра разработки для Windows


1.  На информационной панели Центра разработки для Windows, перейдите на страницу **Получение дохода**&gt;**Получение дохода с помощью рекламы** вашего приложения и [создайте автономный блок Microsoft Advertising](../publish/monetize-with-ads.md). В качестве типа рекламного блока укажите **Баннер**. Запишите и идентификатор рекламного блока, и идентификатор приложения.

2.  В своем коде замените тестовые значения (**applicationId** и **adUnitId**) рекламного блока действительными значениями, сгенерированными в Центре разработки.

3.  
            [Отправьте приложение](../publish/app-submissions.md) в Магазин с помощью информационной панели в Центре разработки.

4.  Изучите [отчеты по показателям рекламы](../publish/advertising-performance-report.md) на информационной панели Центра разработки.

## Полный код default.html для примера проекта UWP


``` syntax
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8" />
    <title>My_Windows_10_Ad_Funded_JavaScript_App</title>

    <!-- WinJS references -->
    <link href="//Microsoft.WinJS.2.0.Preview/css/ui-dark.css" rel="stylesheet" />
    <script src="//Microsoft.WinJS.2.0.Preview/js/base.js"></script>
    <script src="//Microsoft.WinJS.2.0.Preview/js/ui.js"></script>

    <!-- My_Windows_10_Ad_Funded_JavaScript_App references -->
    <link href="/css/default.css" rel="stylesheet" />
    <script src="/js/default.js"></script>

    <!-- Microsoft advertising required references -->
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

## Ссылки по теме

* [Примеры рекламы на GitHub](http://aka.ms/githubads)
 

 



<!--HONumber=Jun16_HO4-->


