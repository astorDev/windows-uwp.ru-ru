---
author: mcleanbyron
ms.assetid: c0450f7b-5c81-4d8c-92ef-2b1190d18af7
description: Узнайте, как использовать класс AdControl для показа баннерной рекламы в приложения Silverlight для Windows Phone 8.1 или Windows Phone 8.0.
title: AdControl в Silverlight для Windows Phone

---

# AdControl в Silverlight для Windows Phone


\[ Обновлено для приложений UWP в Windows 10. Статьи для Windows 8.x см. в [архиве](http://go.microsoft.com/fwlink/p/?linkid=619132) \]

В этом пошаговом руководстве рассказывается, как использовать класс [AdControl](https://msdn.microsoft.com/library/windows/apps/hh524191.aspx) для показа баннерной рекламы в приложении Silverlight для Windows Phone 8.1 и Windows Phone 8.0.

## Необходимые условия

*  Установите [пакет SDK Microsoft Store Engagement and Monetization](http://aka.ms/store-em-sdk) для Visual Studio 2015 или Visual Studio 2013.


## Добавьте ссылки на сборку рекламы

Сборки пакета Microsoft Advertising для проектов Silverlight на Windows Phone не устанавливаются локально вместе с пакетом Microsoft Store Engagement and Monetization SDK. Прежде чем вы сможете начать изменение кода, необходимо сначала задействовать **Подключенные службы** с поддержкой рекламного посредничества в пакете Microsoft Store Engagement and Monetization SDK, чтобы скачать эти сборки и ссылаться на них в проекте.

1.  В Visual Studio выберите **Проект** и **Добавить подключенную службу**.

2.  В диалоговом окне **Добавить подключенную службу** выберите **Рекламный посредник**, а затем выберите **Настройка**.

3.  Нажмите **Выбрать рекламные сети**, выберите только **Microsoft Advertising**.

    На этом этапе все необходимые сборки пакета Microsoft Advertising для Silverlight загружаются в ваш локальный проект с помощью пакетов NuGet, и ссылки на эти сборки автоматически добавляются в ваш проект. Ссылка на сборку рекламного посредничества также добавляется в проект. Ссылку на сборку рекламного посредника вам надо будет удалить на более позднем этапе, поскольку она не требуется для этого сценария.

4.  В диалоговом окне **Выбрать рекламные сети** выберите **ОК**. Снова нажмите **ОК** на следующей странице подтверждения **Состояние получения** и затем нажмите **Добавить** для закрытия диалогового окна **Ad Mediator**.

5.  В **Обозревателе решений** разверните узел **Ссылки**. Щелкните правой кнопкой мыши **Microsoft.AdMediator.WindowsPhone81SL.MicrosoftAdvertising** и нажмите **Удалить**. Эта ссылка на сборку не требуется для данного сценария.

## Составление кода приложения


1.  Добавьте следующие возможности в узел **Возможности** в файле WMAppManifest.xml.

    ``` syntax
    <Capability Name="ID_CAP_IDENTITY_USER"/>
    <Capability Name="ID_CAP_MEDIALIB_PHOTO"/>
    <Capability Name="ID_CAP_PHONEDIALER"/>
    ```

    Для этого примера узел **Возможности** ** выглядит следующим образом.

    ``` syntax
        <Capabilities>
          <Capability Name="ID_CAP_NETWORKING"/>
          <Capability Name="ID_CAP_MEDIALIB_AUDIO"/>
          <Capability Name="ID_CAP_MEDIALIB_PLAYBACK"/>
          <Capability Name="ID_CAP_SENSORS"/>
          <Capability Name="ID_CAP_WEBBROWSERCOMPONENT"/>
          <Capability Name="ID_CAP_IDENTITY_USER"/>
          <Capability Name="ID_CAP_MEDIALIB_PHOTO"/>
          <Capability Name="ID_CAP_PHONEDIALER"/>
        </Capabilities>
    ```

2.  (Необязательно) Сохраните проект. Нажмите значок **Сохранить все** или выберите в меню **Файл** пункт **Сохранить все**.

3.  Добавьте возможность **Интернет (клиент и сервер)** в файл Package.appxmanifest вашего проекта. В **Обозревателе решений** дважды щелкните по **Package.appxmanifest**.

    ![wp81silverlightmarkup\-solutionexplorer\-packageappxmanifest](images/13-b98c2a1a-69c3-4018-be0a-6ce010e703e7.jpg)

    В **Редакторе** нажмите вкладку **Возможности**. Установите флажок **Интернет (клиент и сервер)**.

4.  (Необязательно) Сохраните проект. Нажмите значок **Сохранить все** или выберите в меню **Файл** пункт **Сохранить все**.

5.  Внесите исправления в разметку Silverlight в файле MainPage.xaml для включения пространства имен **Microsoft.Advertising.Mobile.UI**.

    ``` syntax
    xmlns:UI="clr-namespace:Microsoft.Advertising.Mobile.UI;assembly=Microsoft.Advertising.Mobile.UI"
    ```

    В заголовке страницы будет расположен следующий код.

    ``` syntax
        xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
        xmlns:UI="clr-namespace:Microsoft.Advertising.Mobile.UI;assembly=Microsoft.Advertising.Mobile.UI"
        x:Class="PhoneApp1.MainPage"
    ```

6.  В раздел под тегом **Grid** добавьте следующий код для **AdControl**. Задайте свойствам **ApplicationId** и **AdUnitId** тестовые значения, приведенные в разделе [Значения тестового режима](test-mode-values.md) и настройте свойства **Height** и **Width**, чтобы они соответствовали одному из [поддерживаемых размеров объявлений для баннерной рекламы](supported-ad-sizes-for-banner-ads.md).

    > **Примечание**  
    Перед отправкой приложения на проверку необходимо будет заменить тестовые значения **ApplicationId** и **AdUnitId** действительными значениями.

    ``` syntax
    <Grid x:Name="ContentPanel" Grid.Row="1">

      <UI:AdControl
             ApplicationId="3f83fe91-d6be-434d-a0ae-7351c5a997f1"
             AdUnitId="10865270"
             HorizontalAlignment="Left"
             Height="80"
             VerticalAlignment="Top"
             Width="480"/>

    </Grid>
    ```

7.  Соберите и запустите проект. Убедитесь, что ваше приложение отображает объявление, подобное следующему.

    ![wp81silverlight\-emulatorwithad](images/13-8db1492f-ae1d-439b-9b78-bed8e22fe996.jpg)

## Выпуск приложения с динамической рекламой с помощью Центра разработки


1.  На информационной панели Центра разработки для Windows, перейдите на страницу **Получение дохода**&gt;**Получение дохода с помощью рекламы** вашего приложения и [создайте автономный блок Microsoft Advertising](../publish/monetize-with-ads.md). В качестве типа рекламного блока укажите **Баннер**. Запишите и идентификатор рекламного блока, и идентификатор приложения.

2.  В своем коде замените тестовые значения (**applicationId** и **adUnitId**) рекламного блока действительными значениями, сгенерированными в Центре разработки.

3.  [Отправьте приложение](../publish/app-submissions.md) в Магазин с помощью информационной панели в Центре разработки.

4.  Изучите [отчеты по показателям рекламы](../publish/advertising-performance-report.md) на информационной панели Центра разработки.


 


<!--HONumber=May16_HO2-->


