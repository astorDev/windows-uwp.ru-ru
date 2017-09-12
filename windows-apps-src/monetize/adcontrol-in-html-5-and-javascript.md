---
author: mcleanbyron
ms.assetid: adb2fa45-e18f-4254-bd8b-a749a386e3b4
description: "Узнайте, как использовать класс AdControl для показа баннерной рекламы в приложении на JavaScript или HTML для Windows 10 (UWP), Windows 8.1 или Windows Phone 8.1."
title: "AdControl в HTML 5 и JavaScript"
ms.author: mcleans
ms.date: 06/26/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: "windows 10, uwp, рекламные объявления, реклама, AdControl, рекламный элемент управления, HTML, javascript"
ms.openlocfilehash: 44417516d773ea4faf103f6d4cdaf0bc8f290921
ms.sourcegitcommit: 8c4d50ef819ed1a2f8cac4eebefb5ccdaf3fa898
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/27/2017
---
# <a name="adcontrol-in-html-5-and-javascript"></a>AdControl в HTML 5 и JavaScript

В этом пошаговом руководстве рассказывается, как использовать класс [AdControl](https://msdn.microsoft.com/library/windows/apps/microsoft.advertising.winrt.ui.adcontrol.aspx) для показа баннерной рекламы в приложении на JavaScript или HTML для Windows 10 (UWP), Windows 8.1 или Windows Phone 8.1.

Полный пример с проектом, демонстрирующим способы добавления баннерной рекламы в приложение на JavaScript и HTML, см. в разделе [примеров рекламы на GitHub](http://aka.ms/githubads).

## <a name="prerequisites"></a>Что вам понадобится


* Для приложений UWP: установить [Microsoft Advertising SDK](http://aka.ms/ads-sdk-uwp) с помощью Visual Studio 2015 или более поздней версии.
* Для приложений для Windows 8.1 и Windows Phone 8.1 установите [пакет Microsoft Advertising SDK для Windows и Windows Phone 8.x](http://aka.ms/store-8-sdk) с Visual Studio 2015 или Visual Studio 2013.

> [!NOTE]
> Если вы добавляете баннеры в приложение UWP и у вас установлен Windows 10 SDK версии 10.0.14393 (юбилейное обновление) или более поздняя версия пакета Windows SDK, вам также потребуется установить библиотеку WinJS. Эта библиотека ранее включалась в предыдущие версии Windows SDK для Windows 10, но, начиная с Windows 10 SDK версии 10.0.14393 (юбилейное обновление), ее необходимо устанавливать отдельно. Сведения по установке WinJS см. в разделе [Скачать WinJS](http://try.buildwinjs.com/download/GetWinJS/).

## <a name="code-development"></a>Написание кода

1. В Visual Studio откройте свой проект либо создайте новый.

2. Если ваш проект направлен на работу на **Любом ЦП**, обновите его, чтобы он использовал результаты сборки, предназначенные для определенной архитектуры (например **x86**). Если ваш проект направлен на работу на **Любом ЦП**, вам не удастся надлежащим образом добавить ссылку на Microsoft Advertising в приведенных ниже шагах. Дополнительные сведения см. в разделе [Ошибки, вызванные указанием Любого ЦП как целевого в вашем проекте](known-issues-for-the-advertising-libraries.md#reference_errors).

3.  В **Обозревателе решений** щелкните правой кнопкой мыши элемент **Ссылки** и выберите **Добавить ссылку...**.

4.  В **Диспетчере ссылок** выберите одну из следующих ссылок в зависимости от типа проекта.

    -   Для проекта универсальной платформы Windows (UWP): разверните **Универсальные для Windows**, щелкните **Расширения**, а затем установите флажок **Microsoft Advertising SDK для JavaScript** (версия 10.0).

    -   Для проекта Windows 8.1: разверните **Windows 8.1**, щелкните **Расширения**, а затем установите флажок **Собственный Microsoft Advertising SDK для Windows 8.1 (JS)**.

    -   Для проекта Windows Phone 8.1: разверните **Windows Phone 8.1**, щелкните **Расширения**, а затем установите флажок **Собственный Microsoft Advertising SDK для Windows Phone 8.1 (JS)**.

    ![javascriptaddreference](images/13-f7f6d6a6-161e-4f17-995d-1236d0b5d9f2.png)

5.  В **Диспетчере ссылок** нажмите "ОК".

6.  Откройте файл index.html (или другой html-файл, если это необходимо для вашего проекта).

7.  В разделе **&lt;head&gt;** после ссылок на JavaScript проекта из default.css и main.js добавьте ссылку на ad.js.

    В проекте UWP добавьте следующий код.

    ``` HTML
    <!-- Advertising required references -->
    <script src="//Microsoft.Advertising.JavaScript/ad.js"></script>
    ```

    В проекте для Windows 8.1 или Windows Phone 8.1 добавьте следующий код.

    ``` HTML
    <!-- Advertising required references -->
    <script src="/MSAdvertisingJS/ads/ad.js"></script>
    ```

    > [!NOTE]
    > Эта строка должна располагаться в разделе **&lt;head&gt;** после включения main.js. В противном случае при сборке проекта будет выдано сообщение об ошибке. Если ваш проект ориентирован на Windows 8.1 или Windows Phone 8.1, файл HTML по умолчанию в вашем проекте называется default.html, а не index.html, а файл по умолчанию JavaScript в вашем проекте— default.js, а не main.js.

8.  Измените раздел **&lt;body&gt;** в файле default.html (или другом html-файле, если это необходимо для вашего проекта), включив в него элемент div для **AdControl**. Назначьте свойствам **applicationId** и **adUnitId** в разделе **AdControl** тестовые значения, приведенные в разделе [Значения тестового режима](test-mode-values.md). Кроме того, измените высоту и ширину элемента управления, чтобы эти параметры соответствовали одному из [поддерживаемых размеров объявлений для баннерной рекламы](supported-ad-sizes-for-banner-ads.md).

    > [!NOTE]
    > Каждый элемент **AdControl** имеет соответствующую *группу объявлений*, используемую нашими службами для передачи рекламы этому элементу управления, и каждая группа объявлений состоит из *идентификатора группы объявлений* и *идентификатора приложения*. На этих этапах вы задаете тестовые значения идентификатора группы объявлений и идентификатора приложения для своего элемента управления. Эти тестовые значения можно использовать только в тестовой версии приложения. Перед публикацией приложения в Магазине [тестовые значения необходимо заменить на фактические значения](#release) из Центра разработки для Windows.

    ``` HTML
    <div id="myAd" style="position: absolute; top: 50px; left: 0px; width: 300px; height: 250px; z-index: 1"
        data-win-control="MicrosoftNSJS.Advertising.AdControl"
        data-win-options="{applicationId: '3f83fe91-d6be-434d-a0ae-7351c5a997f1', adUnitId: 'test'}">
    </div>
    ```

9.  Скомпилируйте и запустите приложение, чтобы увидеть его с объявлением.

В следующем примере показан полный файл index.html для простого приложения UWP.

``` HTML
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
      data-win-options="{applicationId: '3f83fe91-d6be-434d-a0ae-7351c5a997f1', adUnitId: 'test'}">
    </div>
    <p>Content goes here</p>
</body>
</html>
```

<span id="release" />
## <a name="release-your-app-with-live-ads-using-windows-dev-center"></a>Выпуск приложения с динамической рекламой с помощью Центра разработки для Windows

1.  На информационной панели Центра разработки перейдите на страницу [Монетизация с помощью рекламы](../publish/monetize-with-ads.md) вашего приложения и [создайте группу объявлений](../monetize/set-up-ad-units-in-your-app.md). В качестве типа группы объявлений укажите **Баннер**. Запишите идентификатор группы объявлений и идентификатор приложения.

2. Если ваше приложение является приложением UWP для Windows 10, вы можете при необходимости включить рекламный посредник для **AdControl**, настроив параметры в разделе [Рекламный посредник](../publish/monetize-with-ads.md#mediation) на странице [Монетизация с помощью рекламы](../publish/monetize-with-ads.md). С помощью рекламного посредника можно максимально увеличить выручку от рекламы и возможности ее продвижения, отображая рекламу от нескольких рекламных сетей, в том числе других платных рекламных сетей, например Taboola и Smaato, и рекламных объявлений для кампаний по продвижению приложения Microsoft.

3.  В своем коде замените тестовые значения (**applicationId** и **adUnitId**) рекламного блока действительными значениями, сгенерированными в Центре разработки.

4.  [Отправьте приложение](../publish/app-submissions.md) в Магазин с помощью информационной панели в Центре разработки.

5.  Изучите [отчеты по показателям рекламы](../publish/advertising-performance-report.md) на информационной панели Центра разработки.             

<span id="manage" />
## <a name="manage-ad-units-for-multiple-ad-controls-in-your-app"></a>Управление группами объявлений для нескольких элементов управления рекламой в приложении

Вы можете использовать несколько объектов **AdControl** в одном приложении (например, каждая страница в вашем приложении может содержать свой объект **AdControl**). В этом случае рекомендуется назначить каждому элементу управления свою группу объявлений. Использование различных групп объявлений для каждого элемента управления позволяет по отдельности [настраивать параметры посредника](../publish/monetize-with-ads.md#mediation) и получать раздельные [данные отчетности](../publish/advertising-performance-report.md) для каждого элемента управления. Это также позволяет нашим службам лучше оптимизировать рекламные объявления, которые мы передаем вашему приложению.

> [!IMPORTANT]
> Одну группу объявлений можно использовать только в одном приложении. Если использовать одну группу объявлений в нескольких приложениях, объявления для этой группы объявлений предоставляться не будут.

## <a name="related-topics"></a>Еще по теме:

* [Примеры рекламы на GitHub](http://aka.ms/githubads)
* [Настройка групп объявлений для приложения](../monetize/set-up-ad-units-in-your-app.md)
