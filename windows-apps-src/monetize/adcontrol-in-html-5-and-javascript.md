---
ms.assetid: adb2fa45-e18f-4254-bd8b-a749a386e3b4
description: Узнайте, как использовать класс AdControl для показа баннеров в приложении на HTML или JavaScript для Windows 10 (UWP).
title: AdControl в HTML 5 и JavaScript
ms.date: 03/22/2018
ms.topic: article
keywords: windows 10, uwp, рекламные объявления, реклама, AdControl, рекламный элемент управления, HTML, javascript
ms.localizationpriority: medium
ms.openlocfilehash: 08b834343aafb91fee1e75f9df7ed2a752992fa2
ms.sourcegitcommit: b11f305dbf7649c4b68550b666487c77ea30d98f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/27/2018
ms.locfileid: "7839189"
---
# <a name="adcontrol-in-html-5-and-javascript"></a>AdControl в HTML 5 и JavaScript

В этом пошаговом руководстве рассказывается, как использовать класс [AdControl](https://docs.microsoft.com/uwp/api/microsoft.advertising.winrt.ui.adcontrol) для показа баннерной рекламы в приложении UWP на JavaScript или HTML для Windows 10.

Полный пример с проектом, демонстрирующим способы добавления баннерной рекламы в приложение на JavaScript и HTML, см. в разделе [примеров рекламы на GitHub](http://aka.ms/githubads).

## <a name="prerequisites"></a>Что вам понадобится

* Установка [Microsoft Advertising SDK](http://aka.ms/ads-sdk-uwp) с помощью Visual Studio 2015 или более поздней версии Visual Studio. Инструкции по установке см. в [этой статье](install-the-microsoft-advertising-libraries.md).

> [!NOTE]
> Если у вас установлен Windows 10 SDK версии 10.0.14393 (Юбилейное обновление) или более поздняя версия пакета Windows SDK, необходимо также установить библиотеку [WinJS](https://github.com/winjs/winjs) . Эта библиотека ранее включалась в предыдущие версии Windows SDK для Windows 10, но, начиная с Windows 10 SDK версии 10.0.14393 (юбилейное обновление), ее необходимо устанавливать отдельно. 

## <a name="integrate-a-banner-ad-into-your-app"></a>Интеграция собственного баннера в приложение

1. В Visual Studio откройте свой проект либо создайте новый.

    > [!NOTE]
    > Если вы используете существующий проект, откройте файл Package.appxmanifest в проекте и убедитесь, что возможность **Интернет (клиент)** выбрана. Вашему приложению эта возможность требуется для получения тестовых объявлений и настоящей рекламы.

2. Если ваш проект направлен на работу на **Любом ЦП**, обновите его, чтобы он использовал результаты сборки, предназначенные для определенной архитектуры (например, **x86**). Если ваш проект направлен на работу на **Любом ЦП**, вам не удастся надлежащим образом добавить ссылку на Microsoft Advertising в приведенных ниже шагах. Дополнительные сведения см. в разделе [Ошибки, вызванные указанием варианта "Любой ЦП" как целевого в вашем проекте](known-issues-for-the-advertising-libraries.md#reference_errors).

3. Добавьте ссылку на Microsoft Advertising SDK в свой проект.

    1. В **Обозревателе решений** щелкните правой кнопкой мыши элемент **Ссылки** и выберите **Добавить ссылку...**.
    2.  В **Диспетчере ссылок** разверните раздел **Универсальная платформа Windows**, нажмите **Расширения** и выберите флажок рядом с **Microsoft Advertising SDK для JavaScript** (версия 10.0).
    3.  В **диспетчере ссылок** нажмите "ОК".

6.  Откройте файл index.html (или другой html-файл, если это необходимо для вашего проекта).

7.  В разделе **&lt;head&gt;** после ссылок на JavaScript проекта из default.css и main.js добавьте ссылку на ad.js.

    ``` HTML
    <!-- Advertising required references -->
    <script src="//Microsoft.Advertising.JavaScript/ad.js"></script>
    ```

    > [!NOTE]
    > Эта строка должна располагаться в разделе **&lt;head&gt;** после включения main.js. В противном случае при сборке проекта будет выдано сообщение об ошибке.

8.  Измените раздел **&lt;body&gt;** в файле default.html (или другом html-файле, если это необходимо для вашего проекта), включив в него элемент **div** для **AdControl**. Назначьте свойствам **applicationId** и **adUnitId** в разделе **AdControl** [значения тестовой группы объявлений](set-up-ad-units-in-your-app.md#test-ad-units). Кроме того, измените **высоту** и **ширину** элемента управления, чтобы эти параметры соответствовали одному из [поддерживаемых размеров объявлений для баннерной рекламы](supported-ad-sizes-for-banner-ads.md).

    > [!NOTE]
    > Каждый элемент **AdControl** имеет соответствующую *группу объявлений*, используемую нашими службами для передачи рекламы этому элементу управления, и каждая группа объявлений состоит из *идентификатора группы объявлений* и *идентификатора приложения*. На этих этапах вы задаете тестовые значения идентификатора группы объявлений и идентификатора приложения для своего элемента управления. Эти тестовые значения можно использовать только в тестовой версии приложения. Прежде чем публиковать приложение в магазине, необходимо выполнить [Заменить тестовые значения действительными значениями](#release) из центра партнеров.

    ``` HTML
    <div id="myAd" style="position: absolute; top: 50px; left: 0px; width: 300px; height: 250px; z-index: 1"
        data-win-control="MicrosoftNSJS.Advertising.AdControl"
        data-win-options="{applicationId: '3f83fe91-d6be-434d-a0ae-7351c5a997f1', adUnitId: 'test'}">
    </div>
    ```

9.  Скомпилируйте и запустите приложение, чтобы увидеть его с объявлением.

В следующем примере показан полный файл index.html для простого приложения.

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

### <a name="create-an-adcontrol-programmatically-in-javascript"></a>Создание AdControl программным образом на Javascript

Предыдущие действия показывают, как объявить **AdControl** в разметке HTML. Также можно программным способом создавать **AdControl** на JavaScript. В этом примере предполагается, что вы используете существующий элемент **div** в html-коде с идентификатором **myAd**.

> [!div class="tabbedCodeSnippets"]
[!code-javascript[AdControl](./code/AdvertisingSamples/AdControlSamples/js/main.js#DeclareAdControl)]

В этом примере предполагается, что вы уже объявили методы обработчика событий с названиями **myAdError**, **myAdRefreshed** и **myAdEngagedChanged**.

Если вы используете этот код и не видите рекламу, попробуйте задать атрибут **position:relative** в элементе **div**, содержащем **AdControl**. Это переопределяет параметр по умолчанию для **IFrame**. Объявления будут отображаться правильно, если их отображение не отключено значением этого атрибута. Обратите внимание, что новые рекламные блоки, могут быть недоступны до 30 минут.

> [!NOTE]
> Значения *applicationId* и *adUnitId*, приведенные в данном примере, являются [значениями тестового режима](set-up-ad-units-in-your-app.md#test-ad-units). Перед отправкой своего приложения для отправки, необходимо [Заменить эти значения действительными значениями](set-up-ad-units-in-your-app.md#live-ad-units) из центра партнеров.

<span id="release" />

## <a name="release-your-app-with-live-ads"></a>Выпуск приложения с реальной рекламой

1. Убедитесь, что использование вами рекламных баннеров в своем приложении соответствует нашим [рекомендациям для рекламных баннеров](ui-and-user-experience-guidelines.md#guidelines-for-banner-ads).

1.  В центре партнеров перейдите на страницу [Реклама в приложении](../publish/in-app-ads.md) и [Создайте группу объявлений](set-up-ad-units-in-your-app.md#live-ad-units). В качестве типа группы объявлений укажите **Баннер**. Запомните идентификатор группы объявлений и идентификатор приложения.
    > [!NOTE]
    > Значения идентификатора приложения для тестовых рекламных блоков и реальных рекламных блоков UWP имеют разные форматы. Тестовые значения идентификатора приложения представляют собой элементы GUID. При создании реального рекламного блока UWP в центре партнеров, значение идентификатора приложения для рекламного блока всегда соответствует код продукта в магазине для вашего приложения (пример значения код продукта в Store 9nblggh4r315).

2. Вы можете при необходимости включить рекламный посредник для **AdControl**, настроив параметры в разделе [Параметры посредника](../publish/in-app-ads.md#mediation) на странице [Реклама в приложении](../publish/in-app-ads.md). С помощью рекламного посредника можно максимально увеличить выручку от рекламы и возможности ее продвижения, отображая рекламу от нескольких рекламных сетей, в том числе других платных рекламных сетей, например Taboola и Smaato, и рекламных объявлений для кампаний по продвижению приложения Microsoft.

3.  В своем коде замените тестовые значения (**applicationId** и **adUnitId**) действительными значениями, созданными в центре партнеров.

4.  [Отправить приложение](../publish/app-submissions.md) в магазин с помощью центра партнеров.

5.  Просмотрите [отчеты об эффективности рекламы](../publish/advertising-performance-report.md) в центре партнеров.             

<span id="manage" />

## <a name="manage-ad-units-for-multiple-ad-controls-in-your-app"></a>Управление группами объявлений для нескольких элементов управления рекламой в приложении

Вы можете использовать несколько объектов **AdControl** в одном приложении (например, каждая страница в вашем приложении может содержать свой объект **AdControl**). В этом случае рекомендуется назначить каждому элементу управления свою группу объявлений. Использование различных групп объявлений для каждого элемента управления позволяет по отдельности [настраивать параметры посредника](../publish/in-app-ads.md#mediation) и получать раздельные [данные отчетности](../publish/advertising-performance-report.md) для каждого элемента управления. Это также позволяет нашим службам лучше оптимизировать рекламные объявления, которые мы передаем вашему приложению.

> [!IMPORTANT]
> Одну группу объявлений можно использовать только в одном приложении. Если использовать одну группу объявлений в нескольких приложениях, объявления для этой группы объявлений предоставляться не будут.

## <a name="related-topics"></a>Статьи по теме

* [Рекомендации для рекламных баннеров](ui-and-user-experience-guidelines.md#guidelines-for-banner-ads)
* [Примеры рекламы на GitHub](http://aka.ms/githubads)
* [Настройка групп объявлений для приложения](set-up-ad-units-in-your-app.md)
* [Пошаговое руководство по обработке ошибок в JavaScript](error-handling-in-javascript-walkthrough.md)
