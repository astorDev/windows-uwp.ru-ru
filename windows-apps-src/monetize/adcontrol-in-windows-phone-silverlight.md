---
author: mcleanbyron
ms.assetid: c0450f7b-5c81-4d8c-92ef-2b1190d18af7
description: "Узнайте, как использовать класс AdControl для показа баннерной рекламы в приложения Silverlight для Windows Phone 8.1 или Windows Phone 8.0."
title: "AdControl в Windows Phone Silverlight"
translationtype: Human Translation
ms.sourcegitcommit: f88a71491e185aec84a86248c44e1200a65ff179
ms.openlocfilehash: f4b519b56e8f656f75405a946c646e9f7b89ba99

---

# <a name="adcontrol-in-windows-phone-silverlight"></a>AdControl в Windows Phone Silverlight

В этом пошаговом руководстве рассказывается, как использовать класс [AdControl](https://msdn.microsoft.com/library/windows/apps/hh524191.aspx) для показа баннерной рекламы в приложении Silverlight для Windows Phone 8.1 и Windows Phone 8.0.

> **Примечание для Windows Phone Silverlight 8.0**&nbsp;&nbsp;Рекламные баннеры по-прежнему поддерживаются для существующих приложений Windows Phone 8.0 Silverlight, которые используют **AdControl** из предыдущего выпуска пакета Universal Ad Client SDK или Microsoft Advertising SDK, и уже доступны в Магазине. Однако баннеры больше не поддерживаются в новых проектах Windows Phone Silverlight 8.0. Кроме того, ряд сценариев отладки и тестирования ограничены в проектах Windows Phone 8.x Silverlight. Дополнительные сведения см. в статье [Показ рекламы в приложениях](display-ads-in-your-app.md#silverlight_support).

## <a name="add-the-advertising-assemblies-to-your-project"></a>Добавление рекламных сборок в проект

Чтобы начать работу, скачайте и установите пакет NuGet, содержащий сборки Microsoft Advertising для Windows Phone Silverlight.

1.  Откройте проект в Visual Studio.

2.  Щелкните **Средства**, наведите курсор на пункт **Диспетчер пакетов NuGet** и щелкните **Консоль диспетчера пакетов**.

3.  В окне **консоли диспетчера пакетов** введите одну из этих команд.

  * Если проект предназначен для Windows Phone 8.0, введите следующую команду.

      > [!div class="tabbedCodeSnippets"]
      ```syntax
      Install-Package Microsoft.Advertising.WindowsPhone.SL80 -Version 6.2.40501.1
      ```

  * Если проект предназначен для Windows Phone 8.1, введите следующую команду.

      > [!div class="tabbedCodeSnippets"]
      ```syntax
      Install-Package Microsoft.Advertising.WindowsPhone.SL81 -Version 8.1.50112
      ```

    На этом этапе все необходимые сборки пакета Microsoft Advertising для Silverlight загружаются в ваш локальный проект с помощью пакетов NuGet, и ссылки на эти сборки автоматически добавляются в ваш проект.

## <a name="code-your-app"></a>Составление кода приложения


1.  Добавьте следующие возможности в узел **Возможности** в файле WMAppManifest.xml.

  > [!div class="tabbedCodeSnippets"]
  ``` syntax
  <Capability Name="ID_CAP_IDENTITY_USER"/>
  <Capability Name="ID_CAP_MEDIALIB_PHOTO"/>
  <Capability Name="ID_CAP_PHONEDIALER"/>
  ```

  Для этого примера узел **Возможности** ** выглядит следующим образом.

  > [!div class="tabbedCodeSnippets"]
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

  > [!div class="tabbedCodeSnippets"]
  ``` xml
  xmlns:UI="clr-namespace:Microsoft.Advertising.Mobile.UI;assembly=Microsoft.Advertising.Mobile.UI"
  ```

  В заголовке страницы будет расположен следующий код.

  > [!div class="tabbedCodeSnippets"]
  ``` xml
  xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
  xmlns:UI="clr-namespace:Microsoft.Advertising.Mobile.UI;assembly=Microsoft.Advertising.Mobile.UI"
  x:Class="PhoneApp1.MainPage"
  ```

6.  В раздел под тегом **Grid** добавьте следующий код для **AdControl**. Задайте свойствам **ApplicationId** и **AdUnitId** тестовые значения, приведенные в разделе [Значения тестового режима](test-mode-values.md) и настройте свойства **Height** и **Width**, чтобы они соответствовали одному из [поддерживаемых размеров объявлений для баннерной рекламы](supported-ad-sizes-for-banner-ads.md).

  > **Примечание.**&nbsp;&nbsp;Перед отправкой приложения на проверку необходимо будет заменить тестовые значения **ApplicationId** и **AdUnitId** действительными значениями.

  > [!div class="tabbedCodeSnippets"]
  ``` xml
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

## <a name="release-your-app-with-live-ads-using-dev-center"></a>Выпуск приложения с динамической рекламой с помощью Центра разработки

1.  На информационной панели Центра разработки, перейдите на страницу **Получение дохода** &gt; **Получение дохода с помощью рекламы** вашего приложения и [создайте автономный блок Microsoft Advertising](../publish/monetize-with-ads.md). В качестве типа рекламного блока укажите **Баннер**. Запишите и идентификатор рекламного блока, и идентификатор приложения.

2.  В своем коде замените тестовые значения (**applicationId** и **adUnitId**) рекламного блока действительными значениями, сгенерированными в Центре разработки.

3.  [Отправьте приложение](../publish/app-submissions.md) в Магазин с помощью информационной панели в Центре разработки.

4.  Изучите [отчеты по показателям рекламы](../publish/advertising-performance-report.md) на информационной панели Центра разработки.


 



<!--HONumber=Dec16_HO2-->


