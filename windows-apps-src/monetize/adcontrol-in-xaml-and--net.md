---
author: mcleanbyron
ms.assetid: 4e7c2388-b94e-4828-a104-14fa33f6eb2d
description: "Узнайте, как использовать класс AdControl для показа баннерной рекламы в приложении на XAML для Windows 10 (UWP), Windows 8.1 или Windows Phone 8.1."
title: "AdControl в XAML и .NET"
ms.author: mcleans
ms.date: 06/26/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: "windows 10, uwp, объявления, реклама, AdControl, элемент управления рекламным объявлением, XAML, .net, пошаговое руководство"
ms.openlocfilehash: be273ca4c17edb4affa5e0abb4b3317b03893280
ms.sourcegitcommit: 8c4d50ef819ed1a2f8cac4eebefb5ccdaf3fa898
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/27/2017
---
# <a name="adcontrol-in-xaml-and-net"></a>AdControl в XAML и .NET


В этом пошаговом руководстве рассказывается, как использовать класс [AdControl](https://msdn.microsoft.com/library/windows/apps/microsoft.advertising.winrt.ui.adcontrol.aspx) для показа баннерной рекламы в приложении на XAML для Windows 10 (UWP), Windows 8.1 или Windows Phone 8.1.

Полный пример с проектом, демонстрирующим способы добавления баннерной рекламы в приложение на XAML с использованием C# и C++, см. в разделе [примеров рекламы на GitHub](http://aka.ms/githubads).

## <a name="prerequisites"></a>Что вам понадобится

* Для приложений UWP: установить [Microsoft Advertising SDK](http://aka.ms/ads-sdk-uwp) с помощью Visual Studio 2015 или более поздней версии.
* Для приложений для Windows 8.1 и Windows Phone 8.1 установите [пакет Microsoft Advertising SDK для Windows и Windows Phone 8.x](http://aka.ms/store-8-sdk) с Visual Studio 2015 или Visual Studio 2013.

## <a name="code-development"></a>Написание кода

1. В Visual Studio откройте свой проект либо создайте новый.

2. Если ваш проект направлен на работу на **Любом ЦП**, обновите его, чтобы он использовал результаты сборки, предназначенные для определенной архитектуры (например **x86**). Если ваш проект направлен на работу на **Любом ЦП**, вам не удастся надлежащим образом добавить ссылку на Microsoft Advertising в приведенных ниже шагах. Дополнительные сведения см. в разделе [Ошибки, вызванные указанием Любого ЦП как целевого в вашем проекте](known-issues-for-the-advertising-libraries.md#reference_errors).

1.  В **Обозревателе решений** щелкните правой кнопкой мыши элемент **Ссылки** и выберите **Добавить ссылку...**.

2.  В **Диспетчере ссылок** выберите одну из следующих ссылок в зависимости от типа проекта.

    -   Для проекта универсальной платформы Windows (UWP): разверните **Универсальные для Windows**, щелкните **Расширения**, а затем установите флажок **Microsoft Advertising SDK для XAML** (версия 10.0).

    -   Для проекта Windows 8.1: разверните **Windows 8.1**, нажмите **Расширения**, а затем установите флажок **Ad Mediator SDK для XAML в Windows 8.1**. Этот параметр добавляет библиотеки и Microsoft Advertising, и Ad Mediator в проект, но вы можете не обращать внимания на библиотеки Ad Mediator.

    -   Для проекта Windows Phone 8.1: разверните **Windows Phone 8.1**, нажмите **Расширения**, затем установите флажок **Ad Mediator SDK для XAML в Windows Phone 8.1**. Этот параметр добавляет библиотеки и Microsoft Advertising, и Ad Mediator в проект, но вы можете не обращать внимания на библиотеки Ad Mediator.

    ![addreferences](images/13-a84c026e-b283-44f2-8816-f950a1ef89aa.png)

3.  В **Диспетчере ссылок** нажмите "ОК".

4.  Измените код XAML для страницы, где вы размещаете рекламу, для включения пространства имен **Microsoft.Advertising.WinRT.UI**. Например, в образце приложения по умолчанию, созданного Visual Studio (с названием MyAdFundedWindows10AppXAML в этом приложении), страницей XAML является **MainPage.xaml**.

    Раздел **Page** файла MainPage.xaml, созданного Visual Studio, содержит следующий код.

    ``` xml
    <Page
      x:Class="MyAdFundedWindows10AppXAML.MainPage"
      xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
      xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
      xmlns:local="using:MyAdFundedWindows10AppXAML"
      xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
      xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
      mc:Ignorable="d">
      <Grid Background="{ThemeResource ApplicationPageBackgroundThemeBrush}">
      </Grid>
    </Page>
    ```

    Добавьте ссылку на пространство имен **Microsoft.Advertising.WinRT.UI**, чтобы раздел **Page** файла MainPage.xaml содержал следующий код.

    ``` xml
    <Page
      x:Class="MyAdFundedWindows10AppXAML.MainPage"
      xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
      xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
      xmlns:local="using:MyAdFundedWindows10AppXAML"
      xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
      xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
      xmlns:UI="using:Microsoft.Advertising.WinRT.UI"
      mc:Ignorable="d">
      <Grid Background="{ThemeResource ApplicationPageBackgroundThemeBrush}">
      </Grid>
    </Page>
    ```

5. В раздел под тегом **Grid** добавьте код для **AdControl**. Назначьте свойствам [AdUnitId](https://msdn.microsoft.com/library/windows/apps/microsoft.advertising.winrt.ui.adcontrol.adunitid.aspx) и [ApplicationId](https://msdn.microsoft.com/library/windows/apps/microsoft.advertising.winrt.ui.adcontrol.applicationid.aspx) в разделе **Page** тестовые значения, приведенные в разделе [Значения тестового режима](test-mode-values.md). Кроме того, измените высоту и ширину элемента управления, чтобы эти параметры соответствовали одному из [поддерживаемых размеров объявлений для баннерной рекламы](supported-ad-sizes-for-banner-ads.md).

    > [!NOTE]
    > Каждый элемент **AdControl** имеет соответствующую *группу объявлений*, используемую нашими службами для передачи рекламы этому элементу управления, и каждая группа объявлений состоит из *идентификатора группы объявлений* и *идентификатора приложения*. На этих этапах вы задаете тестовые значения идентификатора группы объявлений и идентификатора приложения для своего элемента управления. Эти тестовые значения можно использовать только в тестовой версии приложения. Перед публикацией приложения в Магазине [тестовые значения необходимо заменить на фактические значения](#release) из Центра разработки для Windows.

    Полностью код раздела под тегом **Grid** выглядит следующим образом.

    ``` xml
    <Grid Background="{StaticResource ApplicationPageBackgroundThemeBrush}">
        <UI:AdControl ApplicationId="3f83fe91-d6be-434d-a0ae-7351c5a997f1"
            AdUnitId="test"
            HorizontalAlignment="Left"
            Height="250"
            VerticalAlignment="Top"
            Width="300"/>
    </Grid>
    ```

    Полный код для файла MainPage.xaml должен выглядеть так.

    ``` xml
    <Page
      x:Class="MyAdFundedWindows10AppXAML.MainPage"
      xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
      xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
      xmlns:local="using:MyAdFundedWindows10AppXAML"
      xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
      xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
      xmlns:UI="using:Microsoft.Advertising.WinRT.UI"
      mc:Ignorable="d">
      <Grid Background="{StaticResource ApplicationPageBackgroundThemeBrush}">
            <UI:AdControl ApplicationId="3f83fe91-d6be-434d-a0ae-7351c5a997f1"
                  AdUnitId="test"
                  HorizontalAlignment="Left"
                  Height="250"
                  VerticalAlignment="Top"
                  Width="300"/>
      </Grid>
    </Page>
    ```

6.  Скомпилируйте и запустите приложение, чтобы увидеть его с объявлением.

<span id="release" />
## <a name="release-your-app-with-live-ads-using-windows-dev-center"></a>Выпуск приложения с динамической рекламой с помощью Центра разработки для Windows

1.  На информационной панели Центра разработки перейдите на страницу [Монетизация с помощью рекламы](../publish/monetize-with-ads.md) вашего приложения и [создайте группу объявлений](../monetize/set-up-ad-units-in-your-app.md). В качестве типа рекламного блока укажите **Баннер**. Запишите и идентификатор рекламного блока, и идентификатор приложения.

2. Если ваше приложение является приложением UWP для Windows 10, вы можете при необходимости включить рекламный посредник для **AdControl**, настроив параметры в разделе [Рекламный посредник](../publish/monetize-with-ads.md#mediation) на странице [Монетизация с помощью рекламы](../publish/monetize-with-ads.md). С помощью рекламного посредника можно максимально увеличить выручку от рекламы и возможности ее продвижения, отображая рекламу от нескольких рекламных сетей, в том числе других платных рекламных сетей, например Taboola и Smaato, и рекламных объявлений для кампаний по продвижению приложения Microsoft.

3.  В своем коде замените тестовые значения (**ApplicationId** и **AdUnitId**) рекламного блока действительными значениями, сгенерированными в Центре разработки.

4.  [Отправьте приложение](../publish/app-submissions.md) в Магазин с помощью информационной панели в Центре разработки.

5.  Изучите [отчеты по показателям рекламы](../publish/advertising-performance-report.md) на информационной панели Центра разработки.

<span id="manage" />
## <a name="manage-ad-units-for-multiple-ad-controls-in-your-app"></a>Управление группами объявлений для нескольких элементов управления рекламой в приложении

Вы можете использовать несколько объектов **AdControl** в одном приложении (например, каждая страница в вашем приложении может содержать свой объект **AdControl**). В этом случае рекомендуется назначить каждому элементу управления свою группу объявлений. Использование различных групп объявлений для каждого элемента управления позволяет по отдельности [настраивать параметры посредника](../publish/monetize-with-ads.md#mediation) и получать раздельные [данные отчетности](../publish/advertising-performance-report.md) для каждого элемента управления. Это также позволяет нашим службам лучше оптимизировать рекламные объявления, которые мы передаем вашему приложению.

> [!IMPORTANT]
> Одну группу объявлений можно использовать только в одном приложении. Если использовать одну группу объявлений в нескольких приложениях, объявления для этой группы объявлений предоставляться не будут.

## <a name="notes"></a>Примечания

* C#: см. [пример свойств XAML](xaml-properties-example.md), чтобы ознакомиться со способами назначения обработчиков событиям **AdControl**. Затем ознакомьтесь с примером кода [событий AdControl в C#](adcontrol-events-in-c.md), где приводятся обработчики событий, написанные на C#.

* C++: текущий выпуск библиотек Microsoft Advertising поддерживает C++. Класс **AdControl** реализован в неуправляемом C++ и не загружает .NET CLR. Примеры кода, демонстрирующие использование **AdControl** на языке C++, см. в [примерах рекламы на GitHub](http://aka.ms/githubads).

* Visual Basic: см. [пример свойств XAML](xaml-properties-example.md), чтобы ознакомиться со способами назначения обработчиков событиям **AdControl**.

* Обработка ошибок. Чтобы узнать, как обрабатывать ошибки, см. раздел [обработка ошибок AdControl](adcontrol-error-handling.md).

## <a name="related-topics"></a>Ссылки по теме

* [Примеры рекламы на GitHub](http://aka.ms/githubads)
* [Настройка групп объявлений для приложения](../monetize/set-up-ad-units-in-your-app.md)
