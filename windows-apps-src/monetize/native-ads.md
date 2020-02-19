---
description: Узнайте, как добавлять собственную рекламу в приложения UWP.
title: Собственные объявления
ms.date: 02/18/2020
ms.topic: article
keywords: windows 10, uwp, рекламные объявления, реклама, элемент управления рекламой, собственная реклама
ms.localizationpriority: medium
ms.openlocfilehash: 7a2022db39c27453dd3c1e9e1eb63914b25a637b
ms.sourcegitcommit: 6af7ce0e3c27f8e52922118deea1b7aad0ae026e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/19/2020
ms.locfileid: "77463936"
---
# <a name="native-ads"></a>Собственные объявления

>[!WARNING]
> Начиная с 1 июня 2020 г. платформа Microsoft AD монетизацию для приложений Windows UWP будет выключена. [Подробнее](https://aka.ms/ad-monetization-shutdown)

Собственные объявления — это формат на основе рекламных компонентов, где каждая часть рекламного объявления (например, заголовок, изображение, описание и призыв к действию) доставляется приложение в виде отдельного элемента. Эти элементы можно интегрировать в приложение с использованием собственных шрифтов, цветов, анимации и других компонентов пользовательского интерфейса, чтобы предоставлять пользователям цельную ненавязчивую рекламу, соответствующую вашему приложению по внешнему виду и общему впечатлению, а также получать высокий доход от рекламы.

Рекламодателям собственные объявления предоставляют высококачественное размещение, поскольку взаимодействие с рекламой интегрировано с приложением, и поэтому пользователи чаще реагируют на объявления такого типа.

> [!NOTE]
> Собственные объявления в настоящее время поддерживаются только для приложений UWP на основе XAML для Windows 10. Поддержка для приложений UWP, написанных на HTML и JavaScript, намечена в последующих выпусках Microsoft Advertising SDK.

## <a name="prerequisites"></a>Предварительные требования

* Установка [Microsoft Advertising SDK](https://marketplace.visualstudio.com/items?itemName=AdMediator.MicrosoftAdvertisingSDK) с помощью Visual Studio 2015 или более поздней версии Visual Studio. Инструкции по установке см. в [этой статье](install-the-microsoft-advertising-libraries.md).

## <a name="integrate-a-native-ad-into-your-app"></a>Интеграция собственного рекламного объявления в приложение

Следуйте этим инструкциям, чтобы интегрировать собственную рекламу в свое приложение и убедиться, что ваша реализация собственной рекламы отображает тестовое объявление.

1. В Visual Studio откройте свой проект либо создайте новый.
    > [!NOTE]
    > Если вы используете существующий проект, откройте файл Package.appxmanifest в проекте и убедитесь, что возможность **Интернет (клиент)** выбрана. Вашему приложению эта возможность требуется для получения тестовых объявлений и настоящей рекламы.

2. Если ваш проект направлен на работу на **Любом ЦП**, обновите его, чтобы он использовал результаты сборки, предназначенные для определенной архитектуры (например **x86**). Если ваш проект направлен на работу на **Любом ЦП**, вам не удастся надлежащим образом добавить ссылку на Microsoft Advertising SDK в приведенных ниже шагах. Дополнительные сведения см. в разделе [Ошибки, вызванные указанием Любого ЦП как целевого в вашем проекте](known-issues-for-the-advertising-libraries.md#reference_errors).

3. Добавьте ссылку на Microsoft Advertising SDK в свой проект.

    1. В **Обозревателе решений** щелкните правой кнопкой мыши элемент **Ссылки** и выберите **Добавить ссылку...** .
    2.  В **Диспетчере ссылок** разверните раздел **Универсальная платформа Windows**, нажмите **Расширения** и выберите флажок рядом с **SDK Microsoft Advertising для XAML** (версия 10.0).
    3.  В **диспетчере ссылок** нажмите "ОК".

4. В соответствующий файл с кодом в вашем приложении (например, в файл MainPage.xaml.cs или в файл с кодом для другой страницы) добавьте следующие ссылки на пространство имен.

    [!code-csharp[NativeAd](./code/AdvertisingSamples/NativeAdSamples/cs/MainPage.xaml.cs#Namespaces)]

5.  В соответствующем месте вашего приложения (например, в ```MainPage``` или на другой странице) объявите объект [NativeAdsManagerV2](https://docs.microsoft.com/uwp/api/microsoft.advertising.winrt.ui.nativeadsmanagerv2) и несколько строковых полей, которые будут представлять собой идентификатор приложения и идентификатор группы объявлений вашей собственной рекламы. Следующий код присваивает полям `myAppId` и `myAdUnitId`[тестовые значения](set-up-ad-units-in-your-app.md#test-ad-units) для собственной рекламы.
    > [!NOTE]
    > Каждый элемент **NativeAdsManagerV2** имеет соответствующую *группу объявлений*, используемую нашими службами для передачи рекламы элементу управления собственной рекламой, и каждая группа объявлений состоит из *идентификатора группы объявлений* и *идентификатора приложения*. На этих этапах вы задаете тестовые значения идентификатора группы объявлений и идентификатора приложения для своего элемента управления. Эти тестовые значения можно использовать только в тестовой версии приложения. Перед публикацией приложения в хранилище [эти тестовые значения необходимо заменить на значения в реальном времени](#release) из центра партнеров.

    [!code-csharp[NativeAd](./code/AdvertisingSamples/NativeAdSamples/cs/MainPage.xaml.cs#Variables)]

6.  В коде, который выполняется при запуске (например, в конструкторе для страницы), создайте объект **NativeAdsManagerV2** и привяжите обработчики событий к событиям **AdReady** и **ErrorOccurred** этого объекта.

    [!code-csharp[NativeAd](./code/AdvertisingSamples/NativeAdSamples/cs/MainPage.xaml.cs#ConfigureNativeAd)]

7.  Когда вы будете готовы показать собственную рекламу, вызовите метод **RequestAd** для получения рекламы.

    [!code-csharp[NativeAd](./code/AdvertisingSamples/NativeAdSamples/cs/MainPage.xaml.cs#RequestAd)]

8.  Когда собственная реклама готова для вашего приложения, вызывается ваш обработчик событий [AdReady](https://docs.microsoft.com/uwp/api/microsoft.advertising.winrt.ui.nativeadsmanagerv2.adready), и объект [NativeAdV2](https://docs.microsoft.com/uwp/api/microsoft.advertising.winrt.ui.nativeadv2), представляющий собственную рекламу передается параметру *e*. Используйте свойства **NativeAdV2**, чтобы получить все элементы собственной рекламы и отобразить их на странице. Не забудьте также вызвать метод **RegisterAdContainer**, чтобы зарегистрировать элемент пользовательского интерфейса, который действует как контейнер для собственной рекламы. Это требуется для правильного подсчета показов рекламных объявлений и нажатий по ним.
    > [!NOTE]
    > Некоторые элементы собственной рекламы являются обязательными и должны всегда отображаться в приложении. Подробнее см. в разделе [Рекомендации для собственных объявлений](ui-and-user-experience-guidelines.md#guidelines-for-native-ads).

    Например, предположим, что ваше приложение содержит ```MainPage``` (или другую страницу) со следующей панелью **StackPanel**. Эта **StackPanel** содержит ряд элементов управления, которые используются для отображения различных элементов собственного объявления, включая название, описание, изображения, текст *спонсируется* и кнопку, на которой будет отображаться текст *призыва к действию*.

    ``` xml
    <StackPanel x:Name="NativeAdContainer" Background="#555555" Width="Auto" Height="Auto"
                Orientation="Vertical">
        <Image x:Name="AdIconImage" HorizontalAlignment="Left" VerticalAlignment="Center"
               Margin="20,20,20,20"/>
        <TextBlock x:Name="TitleTextBlock" HorizontalAlignment="Left" VerticalAlignment="Center"
               Text="The ad title will go here" FontSize="24" Foreground="White" Margin="20,0,0,10"/>
        <TextBlock x:Name="DescriptionTextBlock" HorizontalAlignment="Left" VerticalAlignment="Center"
                   Foreground="White" TextWrapping="Wrap" Text="The ad description will go here"
                   Margin="20,0,0,0" Visibility="Collapsed"/>
        <Image x:Name="MainImageImage" HorizontalAlignment="Left"
               VerticalAlignment="Center" Margin="20,20,20,20" Visibility="Collapsed"/>
        <Button x:Name="CallToActionButton" Background="Gray" Foreground="White"
                HorizontalAlignment="Left" VerticalAlignment="Center" Width="Auto" Height="Auto"
                Content="The call to action text will go here" Margin="20,20,20,20"
                Visibility="Collapsed"/>
        <StackPanel x:Name="SponsoredByStackPanel" Orientation="Horizontal" Margin="20,20,20,20">
            <TextBlock x:Name="SponsoredByTextBlock" Text="The ad sponsored by text will go here"
                       FontSize="24" Foreground="White" Margin="20,0,0,0" HorizontalAlignment="Left"
                       VerticalAlignment="Center" Visibility="Collapsed"/>
            <Image x:Name="IconImageImage" Margin="40,20,20,20" HorizontalAlignment="Left"
                   VerticalAlignment="Center" Visibility="Collapsed"/>
        </StackPanel>
    </StackPanel>
    ```

    В следующем примере кода показан обработчик событий **AdReady**, который отображает каждый элемент собственной рекламы в элементах управления в **StackPanel**, а затем вызывает метод **RegisterAdContainer**, чтобы зарегистрировать **StackPanel**. В этом коде предполагается, что он запускается из файла кода программной части для страницы, которая содержит **StackPanel**.

    [!code-csharp[NativeAd](./code/AdvertisingSamples/NativeAdSamples/cs/MainPage.xaml.cs#AdReady)]

9.  Определите обработчик событий для события **ErrorOccurred** для обработки ошибок, связанных со встроенной рекламой. Следующий пример записывает сведения об ошибке в окно **вывода** Visual Studio во время тестирования.

    [!code-csharp[NativeAd](./code/AdvertisingSamples/NativeAdSamples/cs/MainPage.xaml.cs#ErrorOccurred)]

10.  Скомпилируйте и запустите приложение, чтобы увидеть его с тестовым объявлением.

<span id="release" />

## <a name="release-your-app-with-live-ads"></a>Выпуск приложения с реальной рекламой

После подтверждения того, что ваша реализация собственной рекламы успешно показывает тестовое объявление, выполните следующие действия для настройки вашего приложения для отображения реальных рекламных объявлений и отправки обновленного приложения в Магазин.

1.  Убедитесь, что ваша реализация собственной рекламы соответствует нашим [рекомендациям для собственных объявлений](ui-and-user-experience-guidelines.md#guidelines-for-native-ads).

2.  В центре партнеров перейдите на страницу [рекламных объявлений в приложении](../publish/in-app-ads.md) и [Создайте модуль AD](set-up-ad-units-in-your-app.md#live-ad-units). В качестве типа группы объявлений укажите **Собственная**. Запишите идентификатор рекламного блока и идентификатор приложения.
    > [!NOTE]
    > Значения идентификатора приложения для тестовых рекламных блоков и реальных рекламных блоков UWP имеют разные форматы. Тестовые значения идентификатора приложения представляют собой элементы GUID. При создании активной единицы AD UWP в центре партнеров значение идентификатора приложения для единицы AD всегда совпадает с ИДЕНТИФИКАТОРом магазина для вашего приложения (пример значения идентификатора магазина выглядит как 9NBLGGH4R315).

3. Вы можете при необходимости включить рекламный посредник для собственной рекламы, настроив параметры в разделе [Параметры посредника](../publish/in-app-ads.md#mediation) на странице [Реклама в приложении](../publish/in-app-ads.md). Рекламный посредник позволяет увеличить прибыль от рекламы и рекламные возможности приложений путем показа объявлений из множества популярных рекламных сетей.

4.  В коде замените значения тестовой единицы тестирования (то есть параметры *applicationId* и *адунитид* конструктора [NativeAdsManagerV2](https://docs.microsoft.com/uwp/api/microsoft.advertising.winrt.ui.nativeadsmanagerv2.-ctor) ) на значения Live, созданные в центре партнеров.

5.  [Отправьте приложение](../publish/app-submissions.md) в магазин с помощью центра партнеров.

6.  Изучите [отчеты о производительности рекламы](../publish/advertising-performance-report.md) в центре партнеров.

## <a name="manage-ad-units-for-multiple-native-ads-in-your-app"></a>Управление группами объявлений для нескольких собственных объявлений в приложении

В одном приложении можно использовать несколько позиций для собственных объявлений. В этом случае рекомендуется назначить каждой позиции собственного объявления свою группу объявлений. Использование различных групп объявлений для собственных объявлений позволяет по отдельности [настраивать параметры посредника](../publish/in-app-ads.md#mediation) и получать раздельные [данные отчетности](../publish/advertising-performance-report.md) для каждого элемента управления. Это также позволяет нашим службам лучше оптимизировать рекламные объявления, которые мы передаем вашему приложению.

> [!IMPORTANT]
> Одну группу объявлений можно использовать только в одном приложении. Если использовать одну группу объявлений в нескольких приложениях, объявления для этой группы объявлений предоставляться не будут.

## <a name="related-topics"></a>Связанные разделы

* [Рекомендации по использованию собственных рекламных объявлений](ui-and-user-experience-guidelines.md#guidelines-for-native-ads)
* [Реклама в приложении](../publish/in-app-ads.md)
* [Настройка единиц AD для приложения](set-up-ad-units-in-your-app.md)
