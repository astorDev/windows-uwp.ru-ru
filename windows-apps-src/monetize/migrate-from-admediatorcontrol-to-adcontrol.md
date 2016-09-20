---
author: mcleanbyron
ms.assetid: 9621641A-7462-425D-84CC-101877A738DA
description: "Узнайте, как перейти с AdMediatorControl на AdControl в приложениях UWP."
title: "Переход с AdMediatorControl на AdControl для приложений UWP"
translationtype: Human Translation
ms.sourcegitcommit: 07baa54990ec31dc0cb9c289f9f0222754da9d7c
ms.openlocfilehash: 3abef943530cc756de117edccc5ab16e5f178604

---

# Переход с AdMediatorControl на AdControl для приложений UWP

Предыдущие выпуски пакетов SDK от Майкрософт позволили приложения универсальной платформы Windows (UWP) отображать рекламные баннеры с помощью класса **AdMediatorControl**, благодаря чему разработчики смогли оптимизировать свой доход от рекламы путем показа рекламных баннеров из наших партнерских сетей (AOL и AppNexus), а также AdDuplex. Пакет [Microsoft Store Services SDK](http://aka.ms/store-em-sdk) больше не поддерживает класс **AdMediatorControl**. Если у вас есть приложение, в котором используется класс **AdMediatorControl** из предыдущего пакета SDK, и вы хотите перенести его в приложение UWP, в котором используется [Microsoft Store Services SDK](http://aka.ms/store-em-sdk), следуйте инструкциям в этой статье, чтобы обновить код для использования класса **AdControl** вместо **AdMediatorControl**. Вы также можете настроить приложение в качестве рекламного посредника для AdDuplex при помощи пропорционального или приоритетного подхода.

>**Примечание**.&nbsp;&nbsp;Примеры кода в этой статье предоставлены исключительно в демонстрационных целях. Вам может потребоваться внести изменения в примеры кода для их использования в вашем приложении.

## Необходимые условия

* опубликованное в Магазине Windows приложение UWP, в котором используется AdMediatorControl;
* компьютер для разработки с установленными Visual Studio 2015 и [Microsoft Store Services SDK](http://aka.ms/store-em-sdk);
* если вы планируете выполнять роль рекламного посредника для AdDuplex, вам также потребуется установить пакет [AdDuplex Windows 10 SDK](https://visualstudiogallery.msdn.microsoft.com/6930860a-e64b-4b46-9d72-62d7fddda077) на компьютере для разработки.

  >**Примечание**.&nbsp;&nbsp;В качестве альтернативы запуску установщика пакета SDK AdDuplex по ссылке выше вы можете установить библиотеки AdDuplex для проекта приложения UWP в Visual Studio 2015. Откройте проект приложения UWP в Visual Studio 2015, нажмите **Проект** > **Управление проектами NuGet**, найдите пакет NuGet с именем **AdDuplexWin10** и установите его.

## Шаг 1. Получение идентификаторов приложения и рекламных блоков

При переносе кода для использования класса **AdControl** необходимо знать идентификаторы приложения и рекламных блоков. Проще всего получить самые свежие идентификаторы из файла конфигурации посредника.

1. Войдите на информационную панель Центра разработки для Windows и щелкните приложение, в котором используется **AdMediatorControl**.
2. Нажмите **Монетизация**, а затем— **Получение дохода с помощью рекламы**.
3. В разделе **Рекламный посредник Windows** щелкните ссылку **Скачать конфигурацию посредника** и откройте файл AdMediator.config в текстовом редакторе, таком как "Блокнот".
4. В файле найдите элемент ```<AdAdapterInfo>``` с дочерним элементом ```<Name>MicrosoftAdvertising</Name>```. Этот раздел содержит конфигурацию для платной рекламы Майкрософт.
5. В элементе ```<AdAdapterInfo>``` найдите элементы ```<Property>```, содержащие элементы ```<Key>``` со значениями **WApplicationId** и **WAdUnitId**. В примере ниже значения элементов ```<Value>``` взяты в качестве примера.

  ```xml
  <Metadata>
      <Property>
          <Key>WApplicationId</Key>
          <Value>364d4938-c0f5-4c3d-8aae-090206211dc9</Value>
      </Property>
      <Property>
          <Key>WAdUnitId</Key>
          <Value>301568</Value>
      </Property>
  </Metadata>
  ```
6. Скопируйте оба значения элементов ```<Value>```. Они пригодятся в дальнейшем. Эти значения содержат идентификаторы приложения и рекламного блока для немобильного рекламного блока для платной рекламы Майкрософт.
5. В том же элементе ```<AdAdapterInfo>``` найдите элементы ```<Property>```, содержащие элементы ```<Key>``` со значениями **MApplicationId** и **MAdUnitId**. В примере ниже значения элементов ```<Value>``` взяты в качестве примера.

  ```xml
  <Metadata>
      <Property>
          <Key>MApplicationId</Key>
          <Value>e2cf8388-7018-4a11-8ab0de90f2a7a401</Value>
      </Property>
      <Property>
          <Key>MAdUnitId</Key>
          <Value>301056</Value>
      </Property>
  </Metadata>
  ```
6. Скопируйте оба значения элементов ```<Value>```. Они пригодятся в дальнейшем. Эти значения содержат идентификаторы приложения и рекламного блока для мобильного рекламного блока для платной рекламы Майкрософт.
7. Если вы используете [собственную рекламу](../publish/about-house-ads.md), найдите элемент ```<AdAdapterInfo>``` с дочерним элементом ```<Name>MicrosoftAdvertisingHouse</Name>```. В этом элементе найдите элементы ```<Key>``` со значениями **MAdUnitId** и **WAdUnitId** и сохраните значения соответствующих элементов ```<Value>```. Они пригодятся в дальнейшем. Это идентификаторы мобильных и немобильных рекламных блоков для собственной рекламы Майкрософт соответственно.
8. Если вы используете AdDuplex, найдите элемент ```<AdAdapterInfo>``` с дочерним элементом ```<Name>AdDuplex</Name>```. В этом элементе найдите элементы ```<Key>``` со значениями **AppKey** и **AdUnitId** и сохраните значения соответствующих элементов ```<Value>```. Они пригодятся в дальнейшем. Это ключ приложения AdDuplex и идентификатор рекламного блока соответственно.

## Шаг 2. Обновление кода приложения

Получив идентификаторы приложения и рекламных блоков, вы готовы приступить к обновлению кода приложения для использования **AdControl** вместо **AdMediatorControl**.

### Только платная реклама Майкрософт

Если в своей конфигурации рекламного посредника вы используете только платную рекламу Майкрософт, выполните следующие действия.

  >**Примечание**.&nbsp;&nbsp;Эти действия предполагают, что страница приложения, на которой вы планируете показывать рекламу, содержит пустую сетку с именем **myAdGrid**, например: ```<Grid x:Name="myAdGrid"/>```. Выполняя эти действия, вы создадите и настроите элементы управления рекламой полностью в виде кода, а не XAML.

1. Откройте проект приложения UWP в Visual Studio.
2.  В **Обозревателе решений** щелкните правой кнопкой мыши элемент **Ссылки** и выберите **Добавить ссылку...**.
В **Диспетчере ссылок** разверните раздел **Универсальная платформа Windows**, нажмите **Расширения** и выберите флажок рядом с **SDK Microsoft Advertising для XAML** (версия 10.0).
3. В **Диспетчере ссылок** нажмите "ОК".
4. Удалите объявление **AdMediatorControl** из XAML и удалите весь код, в котором используется этот объект **AdMediatorControl**, включая любые связанные обработчики событий.
5. Откройте файл кода для **Страницы** приложения, на которой вы планируете показывать рекламу.
6. Добавьте следующий оператор в верхнюю часть файла с кодом, если его еще нет.

  ```csharp
  using Microsoft.Advertising.WinRT.UI;
  using Windows.System.Profile;
  ```
7. Добавьте следующие постоянные объявления в класс **Page**.

  ```csharp
  private const int AD_WIDTH = <tbd>;
  private const int AD_HEIGHT = <tbd>;
  private const string WAPPLICATIONID = "<tbd>";
  private const string WADUNITID = "<tbd>";
  private const string MAPPLICATIONID = "<tbd>";
  private const string MADUNITID = "<tbd>";
  ```
7. В каждом из постоянных объявлений замените значения ```<tbd>```:
  * **AD_WIDTH** и **AD_HEIGHT**. Назначьте этим свойствам один из [поддерживаемых размеров рекламных баннеров]( https://msdn.microsoft.com/windows/uwp/monetize/supported-ad-sizes-for-banner-ads).
  * **WAPPLICATIONID** и **WADUNITID**. Назначьте этим свойствам значения **WApplicationId** и **WAdUnitId** для платной рекламы Майкрософт, полученные ранее из файла конфигурации посредника (это значения для немобильного рекламного блока для платной рекламы).
  * **MAPPLICATIONID** и **MADUNITID**. Назначьте этим свойствам значения **MApplicationId** и **MAdUnitId** для платной рекламы Майкрософт, полученные ранее из файла конфигурации посредника (это значения для мобильного рекламного блока для платной рекламы).

8. Добавьте следующие переменные объявления в класс **Page**.

  ```csharp
  // Declare an AdControl.
  private AdControl myAdControl = null;

  // Application ID and ad unit ID values for Microsoft advertising. By default,
  // assign these to non-mobile ad unit info.
  private string myAppId = WAPPLICATIONID;
  private string myAdUnitId = WADUNITID;
  ```
5. Добавьте следующий код в конструктор класса **Page** после вызова метода **InitializeComponent()**.

  ```csharp
  myAdGrid.Width = AD_WIDTH;
  myAdGrid.Height = AD_HEIGHT;

  // For mobile device families, use the mobile ad unit info.
  if ("Windows.Mobile" == AnalyticsInfo.VersionInfo.DeviceFamily)
  {
      myAppId = MAPPLICATIONID;
      myAdUnitId = MADUNITID;
  }

  // Initialize the AdControl.
  myAdControl = new AdControl();
  myAdControl.ApplicationId = myAppId;
  myAdControl.AdUnitId = myAdUnitId;
  myAdControl.Width = AD_WIDTH;
  myAdControl.Height = AD_HEIGHT;
  myAdControl.IsAutoRefreshEnabled = true;

  myAdGrid.Children.Add(myAdControl);
  ```  

### Платная и собственная реклама Майкрософт и AdDuplex

Если вы используете собственную рекламу Майкрософт или AdDuplex и платную рекламу Майкрософт и планируете продолжать выполнять роль рекламного посредника для AdDuplex, выполните действия в этом разделе. Эти примеры кода поддерживают AdDuplex и собственную рекламу Майкрософт. Если вы используете AdDuplex без собственной рекламы Майкрософт или наоборот, удалите код, неактуальный для вашего сценария.

  >**Примечание**.&nbsp;&nbsp;Эти действия предполагают, что страница приложения, на которой вы планируете показывать рекламу, содержит пустую сетку с именем **myAdGrid**, например: ```<Grid x:Name="myAdGrid"/>```. Выполняя эти действия, вы создадите и настроите элементы управления рекламой полностью в виде кода, а не XAML.

1. Откройте проект приложения UWP в Visual Studio.
2.  В **Обозревателе решений** щелкните правой кнопкой мыши элемент **Ссылки** и выберите **Добавить ссылку...**.
В **Диспетчере ссылок** разверните раздел **Универсальная платформа Windows**, нажмите **Расширения** и выберите флажок рядом с **SDK Microsoft Advertising для XAML** (версия 10.0).
3. В **Диспетчере ссылок** нажмите "ОК".
4. Удалите объявление **AdMediatorControl** из XAML и удалите весь код, в котором используется этот объект **AdMediatorControl**, включая любые связанные обработчики событий.
5. Откройте файл кода для **Страницы** приложения, на которой вы планируете показывать рекламу.
6. Добавьте следующие операторы в верхнюю часть файла с кодом, если их еще нет.

  ```csharp
  using Windows.System.UserProfile;
  using Microsoft.Advertising.WinRT.UI;
  using Windows.System.Profile;
  ```
7. Добавьте следующие постоянные объявления в класс **Page**.

  ```csharp
  private const int AD_WIDTH = <tbd>;
  private const int AD_HEIGHT = <tbd>;
  private const int HOUSE_AD_WEIGHT = <tbd>;
  private const int AD_REFRESH_SECONDS = 35;
  private const int MAX_ERRORS_PER_REFRESH = 3;
  private const string WAPPLICATIONID = "<tbd>";
  private const string WADUNITID_PAID = "<tbd>";
  private const string WADUNITID_HOUSE = "<tbd>";
  private const string MAPPLICATIONID = "<tbd>";
  private const string MADUNITID_PAID = "<tbd>";
  private const string MADUNITID_HOUSE = "<tbd>";
  private const string ADDUPLEX_APPKEY = "<tbd>";
  private const string ADDUPLEX_ADUNIT = "<tbd>";
  ```
4. Для каждого постоянного объявления, назначенного ```<tbd>```, замените ```<tbd>``` реальным значением для вашего сценария.
  * **AD_WIDTH** и **AD_HEIGHT**. Назначьте этим свойствам один из [поддерживаемых размеров рекламных баннеров]( https://msdn.microsoft.com/windows/uwp/monetize/supported-ad-sizes-for-banner-ads).
  * **HOUSE_AD_WEIGHT**. Назначьте этому свойству значение в виде целого числа от 0 до 100, указывающее пропорциональное значение, которое требуется применить к собственной рекламе Майкрософт относительно платной рекламы Майкрософт (где 0 означает полное отсутствие показа собственной рекламы, а 100— показ только собственной рекламы).
  * **WAPPLICATIONID** и **WADUNITID_PAID**. Назначьте этим свойствам значения **WApplicationId** и **WAdUnitId** для платной рекламы Майкрософт, полученные ранее из файла конфигурации посредника (это значения для немобильного рекламного блока для платной рекламы).
  * **WADUNITID_HOUSE**. Назначьте этому свойству значение **WAdUnitId** для собственной рекламы Майкрософт, полученное ранее из файла конфигурации посредника (это значение для немобильного рекламного блока для собственной рекламы).
  * **MAPPLICATIONID** и **MADUNITID_PAID**. Назначьте этим свойствам значения **MApplicationId** и **MAdUnitId** для платной рекламы Майкрософт, полученные ранее из файла конфигурации посредника (это значения для мобильного рекламного блока для платной рекламы).
  * **WADUNITID_HOUSE**. Назначьте этому свойству значение **WAdUnitId** для собственной рекламы Майкрософт, полученное ранее из файла конфигурации посредника (это значение для немобильного рекламного блока для собственной рекламы).
  * **ADDUPLEX_APPKEY** и **ADDUPLEX_ADUNIT**. Назначьте этим свойствам значения ключа приложения AdDuplex и идентификаторов рекламных блоков, полученные ранее из файла конфигурации посредника.
5. Добавьте следующие переменные объявления в класс **Page**.
  ```csharp
  // Dispatch timer to fire at each ad refresh interval.
  private DispatcherTimer myAdRefreshTimer = new DispatcherTimer();

  // Global variables used for mediation decisions.
  private Random randomGenerator = new Random();
  private int errorCountCurrentRefresh = 0;  // Prevents infinite redirects.
  private int adDuplexWeight = 0;            // Will be set by GetAdDuplexWeight().

  // Microsoft and AdDuplex controls for banner ads.
  private AdControl myMicrosoftBanner = null;
  private AdDuplex.AdControl myAdDuplexBanner = null;

  // Application ID and ad unit ID values for Microsoft advertising. By default,
  // assign these to non-mobile ad unit info.
  private string myMicrosoftAppId = WAPPLICATIONID;
  private string myMicrosoftPaidUnitId = WADUNITID_PAID;
  private string myMicrosoftHouseUnitId = WADUNITID_HOUSE;
  ```
5. Добавьте следующий код в конструктор класса **Page** после вызова метода **InitializeComponent()**.
  ```csharp
  myAdGrid.Width = AD_WIDTH;
  myAdGrid.Height = AD_HEIGHT;
  adDuplexWeight = GetAdDuplexWeight();
  RefreshBanner();

  // Start the timer to refresh the banner at the desired interval.
  myAdRefreshTimer.Interval = new TimeSpan(0, 0, AD_REFRESH_SECONDS);
  myAdRefreshTimer.Tick += myAdRefreshTimer_Tick;
  myAdRefreshTimer.Start();

  // For mobile device families, use the mobile ad unit info.
  if ("Windows.Mobile" == AnalyticsInfo.VersionInfo.DeviceFamily)
  {
      myMicrosoftAppId = MAPPLICATIONID;
      myMicrosoftPaidUnitId = MADUNITID_PAID;
      myMicrosoftHouseUnitId = MADUNITID_HOUSE;
  }
  ```
6. Наконец, добавьте следующие методы в класс **Page**. Эти методы создают объекты Microsoft **AdControl** и AdDuplex **AdControl** и используют генератор случайных чисел вместе с данными пропорциональными значениями для регулярного обновления рекламных баннеров в этих элементах управления.
  ```csharp
  private int GetAdDuplexWeight()
  {
      // TODO: Change this logic to fit your needs.
      // This example uses Microsoft ads first in Canada and Mexico, then
      // AdDuplex as fallback. In France, AdDuplex is first. In other regions,
      // this example uses a weighted average approach, with 50% to AdDuplex.

      int returnValue = 0;
      switch (GlobalizationPreferences.HomeGeographicRegion)
      {
          case "CA":
          case "MX":
              returnValue = 0;
              break;
          case "FR":
              returnValue = 100;
              break;
          default:
              returnValue = 50;
              break;
      }
      return returnValue;
  }

  private void ActivateMicrosoftBanner()
  {
      // Return if you hit the error limit for this refresh interval.
      if (errorCountCurrentRefresh >= MAX_ERRORS_PER_REFRESH)
      {
          myAdGrid.Visibility = Visibility.Collapsed;
          return;
      }

      // Use random number generator and house ads weight to determine whether
      // to use paid ads or house ads. Paid is the default. You could alternatively
      // write a method similar to GetAdDuplexWeight and override by region.
      string myAdUnit = myMicrosoftPaidUnitId;
      int houseWeight = HOUSE_AD_WEIGHT;
      int randomInt = randomGenerator.Next(0, 100);
      if (randomInt < houseWeight)
      {
          myAdUnit = myMicrosoftHouseUnitId;
      }

      // Hide the AdDuplex control if it is showing.
      if (null != myAdDuplexBanner)
      {
          myAdDuplexBanner.Visibility = Visibility.Collapsed;
      }

      // Initialize or display the Microsoft control.
      if (null == myMicrosoftBanner)
      {
          myMicrosoftBanner = new AdControl();
          myMicrosoftBanner.ApplicationId = myMicrosoftAppId;
          myMicrosoftBanner.AdUnitId = myAdUnit;
          myMicrosoftBanner.Width = AD_WIDTH;
          myMicrosoftBanner.Height = AD_HEIGHT;
          myMicrosoftBanner.IsAutoRefreshEnabled = false;

          myMicrosoftBanner.AdRefreshed += myMicrosoftBanner_AdRefreshed;
          myMicrosoftBanner.ErrorOccurred += myMicrosoftBanner_ErrorOccurred;

          myAdGrid.Children.Add(myMicrosoftBanner);
      }
      else
      {
          myMicrosoftBanner.AdUnitId = myAdUnit;
          myMicrosoftBanner.Visibility = Visibility.Visible;
          myMicrosoftBanner.Refresh();
      }
  }

  private void ActivateAdDuplexBanner()
  {
      // Return if you hit the error limit for this refresh interval.
      if (errorCountCurrentRefresh >= MAX_ERRORS_PER_REFRESH)
      {
          myAdGrid.Visibility = Visibility.Collapsed;
          return;
      }

      // Hide the Microsoft control if it is showing.
      if (null != myMicrosoftBanner)
      {
          myMicrosoftBanner.Visibility = Visibility.Collapsed;
      }

      // Initialize or display the AdDuplex control.
      if (null == myAdDuplexBanner)
      {
          myAdDuplexBanner = new AdDuplex.AdControl();
          myAdDuplexBanner.AppKey = ADDUPLEX_APPKEY;
          myAdDuplexBanner.AdUnitId = ADDUPLEX_ADUNIT;
          myAdDuplexBanner.Width = AD_WIDTH;
          myAdDuplexBanner.Height = AD_HEIGHT;
          myAdDuplexBanner.RefreshInterval = AD_REFRESH_SECONDS;

          myAdDuplexBanner.AdLoaded += myAdDuplexBanner_AdLoaded;
          myAdDuplexBanner.AdCovered += myAdDuplexBanner_AdCovered;
          myAdDuplexBanner.AdLoadingError += myAdDuplexBanner_AdLoadingError;
          myAdDuplexBanner.NoAd += myAdDuplexBanner_NoAd;

          myAdGrid.Children.Add(myAdDuplexBanner);
      }
      myAdDuplexBanner.Visibility = Visibility.Visible;
  }

  private void myAdRefreshTimer_Tick(object sender, object e)
  {
      RefreshBanner();
  }

  private void RefreshBanner()
  {
      // Reset the error counter for this refresh interval and
      // make sure the ad grid is visible.
      errorCountCurrentRefresh = 0;
      myAdGrid.Visibility = Visibility.Visible;

      // Display ad from AdDuplex.
      if (100 == adDuplexWeight)
      {
          ActivateAdDuplexBanner();
      }
      // Display Microsoft ad.
      else if (0 == adDuplexWeight)
      {
          ActivateMicrosoftBanner();
      }
      // Use weighted approach.
      else
      {
          int randomInt = randomGenerator.Next(0, 100);
          if (randomInt < adDuplexWeight) ActivateAdDuplexBanner();
          else ActivateMicrosoftBanner();
      }
  }

  private void myMicrosoftBanner_AdRefreshed(object sender, RoutedEventArgs e)
  {
      // Add your code here as necessary.
  }

  private void myMicrosoftBanner_ErrorOccurred(object sender, AdErrorEventArgs e)
  {
      errorCountCurrentRefresh++;
      ActivateAdDuplexBanner();
  }

  private void myAdDuplexBanner_AdLoaded(object sender, AdDuplex.Banners.Models.BannerAdLoadedEventArgs e)
  {
      // Add your code here as necessary.
  }

  private void myAdDuplexBanner_NoAd(object sender, AdDuplex.Common.Models.NoAdEventArgs e)
  {
      errorCountCurrentRefresh++;
      ActivateMicrosoftBanner();
  }

  private void myAdDuplexBanner_AdLoadingError(object sender, AdDuplex.Common.Models.AdLoadingErrorEventArgs e)
  {
      errorCountCurrentRefresh++;
      ActivateMicrosoftBanner();
  }

  private void myAdDuplexBanner_AdCovered(object sender,   AdDuplex.Banners.Core.AdCoveredEventArgs e)
  {
      errorCountCurrentRefresh++;
      ActivateMicrosoftBanner();
  }
  ```



<!--HONumber=Sep16_HO1-->


