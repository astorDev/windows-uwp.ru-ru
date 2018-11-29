---
ms.assetid: 4e7c2388-b94e-4828-a104-14fa33f6eb2d
description: Узнайте, как использовать класс AdControl для показа баннеров в приложении на XAML для Windows 10 (UWP).
title: AdControl в XAML и .NET
ms.date: 03/22/2018
ms.topic: article
keywords: windows 10, uwp, объявления, реклама, AdControl, элемент управления рекламным объявлением, XAML, .net, пошаговое руководство
ms.localizationpriority: medium
ms.openlocfilehash: 161718bb88d7588db345f3de26de2a955ca6620e
ms.sourcegitcommit: b5c9c18e70625ab770946b8243f3465ee1013184
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/28/2018
ms.locfileid: "7980332"
---
# <a name="adcontrol-in-xaml-and-net"></a>AdControl в XAML и .NET


В этом пошаговом руководстве рассказывается, как использовать класс [AdControl](https://docs.microsoft.com/uwp/api/microsoft.advertising.winrt.ui.adcontrol) для показа баннерной рекламы в приложении UWP на XAML для Windows 10, реализованном с использованием C#.

> [!NOTE]
> Microsoft Advertising SDK также поддерживает приложения XAML, которые реализуются с использованием C++. Полный пример с проектом см. в разделе [примеры рекламы на GitHub](http://aka.ms/githubads).

## <a name="prerequisites"></a>Что вам понадобится

* Установка [Microsoft Advertising SDK](http://aka.ms/ads-sdk-uwp) с помощью Visual Studio 2015 или более поздней версии Visual Studio. Инструкции по установке см. в [этой статье](install-the-microsoft-advertising-libraries.md).

## <a name="integrate-a-banner-ad-into-your-app"></a>Интеграция собственного баннера в приложение

1. В Visual Studio откройте свой проект либо создайте новый.

    > [!NOTE]
    > Если вы используете существующий проект, откройте файл Package.appxmanifest в проекте и убедитесь, что возможность **Интернет (клиент)** выбрана. Вашему приложению эта возможность требуется для получения тестовых объявлений и настоящей рекламы.

2. Если ваш проект направлен на работу на **Любом ЦП**, обновите его, чтобы он использовал результаты сборки, предназначенные для определенной архитектуры (например, **x86**). Если ваш проект направлен на работу на **Любом ЦП**, вам не удастся надлежащим образом добавить ссылку на Microsoft Advertising в приведенных ниже шагах. Дополнительные сведения см. в разделе [Ошибки, вызванные указанием варианта "Любой ЦП" как целевого в вашем проекте](known-issues-for-the-advertising-libraries.md#reference_errors).

3. Добавьте ссылку на Microsoft Advertising SDK в свой проект.

    1. В **Обозревателе решений** щелкните правой кнопкой мыши элемент **Ссылки** и выберите **Добавить ссылку...**.
    2.  В **Диспетчере ссылок** разверните раздел **Универсальная платформа Windows**, нажмите **Расширения** и выберите флажок рядом с **SDK Microsoft Advertising для XAML** (версия 10.0).
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

5. В раздел под тегом **Grid** добавьте код для **AdControl**. Назначьте свойствам [ApplicationId](https://docs.microsoft.com/uwp/api/microsoft.advertising.winrt.ui.adcontrol.applicationid) и [AdUnitId](https://docs.microsoft.com/uwp/api/microsoft.advertising.winrt.ui.adcontrol.adunitid) значения [тестовой группы объявлений](set-up-ad-units-in-your-app.md#test-ad-units). Кроме того, измените **высоту** и **ширину** элемента управления, чтобы эти параметры соответствовали одному из [поддерживаемых размеров объявлений для баннерной рекламы.](supported-ad-sizes-for-banner-ads.md)

    > [!NOTE]
    > Каждый элемент **AdControl** имеет соответствующую *группу объявлений*, используемую нашими службами для передачи рекламы этому элементу управления, и каждая группа объявлений состоит из *идентификатора группы объявлений* и *идентификатора приложения*. На этих этапах вы задаете тестовые значения идентификатора группы объявлений и идентификатора приложения для своего элемента управления. Эти тестовые значения можно использовать только в тестовой версии приложения. Прежде чем публиковать приложение в магазине, необходимо выполнить [Заменить тестовые значения действительными значениями](#release) из центра партнеров.

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

## <a name="release-your-app-with-live-ads"></a>Выпуск приложения с реальной рекламой

1. Убедитесь, что использование вами рекламных баннеров в своем приложении соответствует нашим [рекомендациям для рекламных баннеров](ui-and-user-experience-guidelines.md#guidelines-for-banner-ads).

2.  В центре партнеров перейдите на страницу [Реклама в приложении](../publish/in-app-ads.md) и [Создайте группу объявлений](set-up-ad-units-in-your-app.md#live-ad-units). В качестве типа группы объявлений укажите **Баннер**. Запомните идентификатор группы объявлений и идентификатор приложения.
    > [!NOTE]
    > Значения идентификатора приложения для тестовых рекламных блоков и реальных рекламных блоков UWP имеют разные форматы. Тестовые значения идентификатора приложения представляют собой элементы GUID. При создании реального рекламного блока UWP в центре партнеров, значение идентификатора приложения для рекламного блока всегда соответствует код продукта в магазине для вашего приложения (пример значения код продукта в Store 9nblggh4r315).

3. Вы можете при необходимости включить рекламный посредник для **AdControl**, настроив параметры в разделе [Параметры посредника](../publish/in-app-ads.md#mediation) на странице [Реклама в приложении](../publish/in-app-ads.md). С помощью рекламного посредника можно максимально увеличить выручку от рекламы и возможности ее продвижения, отображая рекламу от нескольких рекламных сетей, в том числе других платных рекламных сетей, например Taboola и Smaato, и рекламных объявлений для кампаний по продвижению приложения Microsoft.

4.  В своем коде замените тестовые значения (**ApplicationId** и **AdUnitId**) действительными значениями, созданными в центре партнеров.

5.  [Отправить приложение](../publish/app-submissions.md) в магазин с помощью центра партнеров.

6.  Просмотрите [отчеты об эффективности рекламы](../publish/advertising-performance-report.md) в центре партнеров.

<span id="manage" />

## <a name="manage-ad-units-for-multiple-ad-controls-in-your-app"></a>Управление группами объявлений для нескольких элементов управления рекламой в приложении

Вы можете использовать несколько объектов **AdControl** в одном приложении (например, каждая страница в вашем приложении может содержать свой объект **AdControl**). В этом случае рекомендуется назначить каждому элементу управления свою группу объявлений. Использование различных групп объявлений для каждого элемента управления позволяет по отдельности [настраивать параметры посредника](../publish/in-app-ads.md#mediation) и получать раздельные [данные отчетности](../publish/advertising-performance-report.md) для каждого элемента управления. Это также позволяет нашим службам лучше оптимизировать рекламные объявления, которые мы передаем вашему приложению.

> [!IMPORTANT]
> Одну группу объявлений можно использовать только в одном приложении. Если использовать одну группу объявлений в нескольких приложениях, объявления для этой группы объявлений предоставляться не будут.

## <a name="related-topics"></a>Статьи по теме

* [Рекомендации для рекламных баннеров](ui-and-user-experience-guidelines.md#guidelines-for-banner-ads)
* [Пошаговое руководство по обработке ошибок XAML/C#](error-handling-in-xamlc-walkthrough.md).
* [Примеры рекламы на GitHub](http://aka.ms/githubads)
* [Настройка групп объявлений для приложения](set-up-ad-units-in-your-app.md)
