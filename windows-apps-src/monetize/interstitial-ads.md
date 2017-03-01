---
author: mcleanbyron
ms.assetid: 1f970d38-2338-470e-b5ba-811402752fc4
description: "Узнайте, как включить промежуточную рекламу в Windows 10, Windows 8.1 или Windows Phone 8.1 с помощью библиотек Microsoft Advertising в составе пакета Microsoft Store Services SDK."
title: "Промежуточная реклама"
ms.author: mcleans
ms.date: 02/08/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: "windows 10, uwp, реклама, рекламные объявления, промежуточная"
translationtype: Human Translation
ms.sourcegitcommit: c6b64cff1bbebc8ba69bc6e03d34b69f85e798fc
ms.openlocfilehash: fb1cdd018ed496b59290825ee84d10ac70a0e46d
ms.lasthandoff: 02/07/2017

---

# <a name="interstitial-ads"></a>Промежуточная реклама

В этом пошаговом руководстве показано, как включить промежуточную рекламу в Windows 10, Windows 8.1 или Windows Phone 8.1 с помощью библиотек Microsoft Advertising в составе пакета Microsoft Store Services SDK.

Полные примеры проектов, демонстрирующие способы добавления промежуточной рекламы в приложения на JavaScript/HTML, а также в приложения на XAML с помощью языков C# и C++, см. в [примерах рекламы на GitHub](http://aka.ms/githubads).

<span id="whatareinterstitialads10"/>
## <a name="what-are-interstitial-ads"></a>Что такое промежуточная реклама?

В отличие от рекламных баннеров, промежуточная реклама (или *промежуточные рекламные ролики*) отображаются во весь экран приложения. В играх, в основном, используются две основные формы такой рекламы.

* Рекламу *Paywall* пользователь обязан смотреть с определенной регулярной периодичностью, например между разными уровнями игры:

    ![whatisaninterstitial](images/13-ed0a333b-0fc8-4ca9-a4c8-11e8b4392831.png)

* Реклама *за вознаграждения* отображается тогда, когда пользователь намеренно ищет определенные преимущества, например подсказку или дополнительное время для прохождения уровня, и запускает видеорекламу из пользовательского интерфейса приложения.

    Важно отметить, что этот пакет SDK не обрабатывает никакие элементы пользовательского интерфейса, кроме как во время воспроизведения. См. [передовые практики размещения промежуточной рекламы](ui-and-user-experience-guidelines.md#interstitialbestpractices10), чтобы получить информацию о рекомендуемых и нерекомендуемых действиях при добавлении промежуточной рекламы в приложение.

## <a name="build-an-app-with-interstitial-ads"></a>Создание приложения с промежуточной рекламой

Чтобы реализовать отображение промежуточной рекламы в вашем приложении, следуйте инструкциям для конкретного типа проекта:

* [XAML/.NET](#interstitialadsxaml10)
* [HTML/JavaScript](#interstitialadshtml10)
* [C++ (межпрограммное взаимодействие DirectX)](#interstitialadsdirectx10)

<span/>
### <a name="prerequisites"></a>Необходимые условия

* Для приложений UWP: установите [пакет Microsoft Store Services SDK](http://aka.ms/store-em-sdk) с Visual Studio 2015.
* При работе с приложениями для Windows 8.1 и Windows Phone 8.1 установите [пакет Microsoft Advertising SDK для Windows и Windows Phone 8.x](http://aka.ms/store-8-sdk) с Visual Studio 2015 или Visual Studio 2013.

<span id="interstitialadsxaml10"/>
###<a name="xamlnet"></a>XAML/.NET

В этом разделе приводятся примеры кода на C#, однако Visual Basic и C++ также поддерживаются для создания проектов XAML/.NET. Полный пример кода на C# см. в разделе [Пример кода промежуточной рекламы в C#](interstitial-ad-sample-code-in-c.md).

1. Откройте проект в Visual Studio.

2. В **Диспетчере ссылок** выберите одну из следующих ссылок в зависимости от типа проекта.

  * Для проекта универсальной платформы Windows (UWP): разверните **Универсальные для Windows**, щелкните **Расширения**, а затем установите флажок **Microsoft Advertising SDK для XAML** (версия 10.0).

  * Для проекта Windows 8.1: разверните **Windows 8.1**, нажмите **Расширения**, а затем установите флажок **Ad Mediator SDK для XAML в Windows 8.1**. Этот параметр добавляет библиотеки и Microsoft Advertising, и Ad Mediator в проект, но вы можете не обращать внимания на библиотеки Ad Mediator.

  * Для проекта Windows Phone 8.1: разверните **Windows Phone 8.1**, нажмите **Расширения**, затем установите флажок **Ad Mediator SDK для XAML в Windows Phone 8.1**. Этот параметр добавляет библиотеки Microsoft Advertising и Ad Mediator в проект, но вы можете не обращать внимания на библиотеки Ad Mediator.

3.  В соответствующий файл с кодом в вашем приложении (например, в файл MainPage.xaml.cs или в файл с кодом для другой страницы) добавьте следующую ссылку на пространство имен.

  > [!div class="tabbedCodeSnippets"]
  [!code-cs[InterstitialAd](./code/AdvertisingSamples/InterstitialAdSamples/cs/MainPage.xaml.cs#Snippet1)]

4.  В соответствующем месте вашего приложения (например, в ```MainPage``` или на другой странице) объявите объект [InterstitialAd](https://msdn.microsoft.com/library/windows/apps/microsoft.advertising.winrt.ui.interstitialad.aspx) и несколько строковых полей, которые будут представлять собой идентификатор приложения и идентификатор рекламного блока вашей промежуточной рекламы. Следующий код присваивает поля `myAppId` и `myAdUnitId` тестовым значениям, представленным в разделе [Значения тестового режима](test-mode-values.md). Эти значения используются только для тестирования. Их необходимо заменить на фактические значения из Центра разработки для Windows перед публикацией приложения.

  > [!div class="tabbedCodeSnippets"]
  [!code-cs[InterstitialAd](./code/AdvertisingSamples/InterstitialAdSamples/cs/MainPage.xaml.cs#Snippet2)]

5.  В коде, который выполняется при запуске (например, в конструкторе для страницы), создайте объект **InterstitialAd** и привяжите обработчики событий к событиям этого объекта.

  > [!div class="tabbedCodeSnippets"]
  [!code-cs[InterstitialAd](./code/AdvertisingSamples/InterstitialAdSamples/cs/MainPage.xaml.cs#Snippet3)]

6.  Приблизительно за 30–60 секунд до того, как должна будет отобразиться промежуточная реклама, используйте метод [RequestAd](https://msdn.microsoft.com/library/windows/apps/microsoft.advertising.winrt.ui.interstitialad.requestad.aspx), чтобы предварительно получить рекламное объявление. Этого времени будет достаточно для того, чтобы запросить и подготовить рекламное объявление до его отображения.

  > [!div class="tabbedCodeSnippets"]
  [!code-cs[InterstitialAd](./code/AdvertisingSamples/InterstitialAdSamples/cs/MainPage.xaml.cs#Snippet4)]

6.  Проверьте то место вашего когда, в котором должна отображаться ваша реклама, на предмет того, что реклама **InterstitialAd** готова к отображению, а затем отобразите ее, используя метод [Show](https://msdn.microsoft.com/library/windows/apps/microsoft.advertising.winrt.ui.interstitialad.show.aspx).

  > [!div class="tabbedCodeSnippets"]
  [!code-cs[InterstitialAd](./code/AdvertisingSamples/InterstitialAdSamples/cs/MainPage.xaml.cs#Snippet5)]

7.  Определите обработчики событий для объекта **InterstitialAd**.

  > [!div class="tabbedCodeSnippets"]
  [!code-cs[InterstitialAd](./code/AdvertisingSamples/InterstitialAdSamples/cs/MainPage.xaml.cs#Snippet6)]

8.  Создайте и протестируйте приложение, чтобы убедиться, что в нем отображается реклама.

<span id="interstitialadshtml10"/>
###<a name="htmljavascript"></a>HTML/JavaScript

В приведенных ниже инструкциях подразумевается, что вы уже создали проект универсального приложения для JavaScript в Visual Studio 2015 и для конкретного процессора. Полный пример кода см. в разделе [Пример кода промежуточной рекламы в JavaScript](interstitial-ad-sample-code-in-javascript.md).

1. Откройте проект в Visual Studio.

2.  В **Диспетчере ссылок** выберите одну из следующих ссылок в зависимости от типа проекта.

  * Для проекта универсальной платформы Windows (UWP): разверните **Универсальные для Windows**, щелкните **Расширения**, а затем установите флажок **Microsoft Advertising SDK для JavaScript** (версия 10.0).

  * Для проекта Windows 8.1: разверните **Windows 8.1**, щелкните **Расширения**, а затем установите флажок **Собственный Microsoft Advertising SDK для Windows 8.1 (JS)**.

  * Для проекта Windows 8.1: разверните **Windows Phone 8.1**, щелкните **Расширения**, а затем установите флажок **Собственный Microsoft Advertising SDK для Windows Phone 8.1 (JS)**.

3.  В разделе **&lt;head&gt;** HTML-файла в проекте после ссылок на JavaScript проекта из файлов default.css и default.js добавьте ссылку на файл ad.js. В проекте UWP добавьте следующую ссылку.

  > [!div class="tabbedCodeSnippets"]
  ``` html
  <script src="//Microsoft.Advertising.JavaScript/ad.js"></script>
  ```

  В проекте для Windows 8.1 или Windows Phone 8.1 добавьте следующую ссылку.

  > [!div class="tabbedCodeSnippets"]
  ``` html
  <script src="/MSAdvertisingJS/ads/ad.js"></script>
  ```

4.  В файле JS проекта объявите объект [InterstitialAd](https://msdn.microsoft.com/library/windows/apps/microsoft.advertising.winrt.ui.interstitialad.aspx), а также несколько полей, которые будут содержать идентификатор приложения и идентификатор рекламного блока вашей промежуточной рекламы. Следующий код присваивает поля `applicationId` и `adUnitId` тестовым значениям, представленным в разделе [Значения тестового режима](test-mode-values.md). Эти значения используются только для тестирования. Их необходимо заменить на фактические значения из Центра разработки для Windows перед публикацией приложения.

  > [!div class="tabbedCodeSnippets"]
  [!code-javascript[InterstitialAd](./code/AdvertisingSamples/InterstitialAdSamples/js/script.js#Snippet1)]

5.  В коде, который выполняется при запуске (например, в конструкторе для страницы), создайте объект **InterstitialAd** и привяжите обработчики событий к событиям этого объекта.

  > [!div class="tabbedCodeSnippets"]
  [!code-javascript[InterstitialAd](./code/AdvertisingSamples/InterstitialAdSamples/js/script.js#Snippet2)]

5. Приблизительно за 30–60 секунд до того, как должна будет отобразиться промежуточная реклама, используйте метод [RequestAd](https://msdn.microsoft.com/library/windows/apps/microsoft.advertising.winrt.ui.interstitialad.requestad.aspx), чтобы предварительно получить рекламное объявление. Этого времени будет достаточно для того, чтобы запросить и подготовить рекламное объявление до его отображения.

  > [!div class="tabbedCodeSnippets"]
  [!code-javascript[InterstitialAd](./code/AdvertisingSamples/InterstitialAdSamples/js/script.js#Snippet3)]

6.  Проверьте то место вашего когда, в котором должна отображаться ваша реклама, на предмет того, что реклама **InterstitialAd** готова к отображению, а затем отобразите ее, используя метод [Show](https://msdn.microsoft.com/library/windows/apps/microsoft.advertising.winrt.ui.interstitialad.show.aspx).

  > [!div class="tabbedCodeSnippets"]
  [!code-javascript[InterstitialAd](./code/AdvertisingSamples/InterstitialAdSamples/js/samples.js#Snippet4)]

7.  Определите обработчики событий для объекта **InterstitialAd**.

  > [!div class="tabbedCodeSnippets"]
  [!code-javascript[InterstitialAd](./code/AdvertisingSamples/InterstitialAdSamples/js/samples.js#Snippet5)]

9.  Создайте и протестируйте приложение, чтобы убедиться, что в нем отображается реклама.

<span id="interstitialadsdirectx10"/>
###<a name="c-directx-interop"></a>C++ (межпрограммное взаимодействие DirectX)

В этом примере предполагается, что вы уже создали проект **приложения DirectX и XAML (универсальное приложение для Windows)** на C++ в Visual Studio 2015 и для определенной архитектуры ЦП.
 
1. Откройте проект в Visual Studio.

1.  В **Диспетчере ссылок** выберите одну из следующих ссылок в зависимости от типа проекта.

  * Для проекта универсальной платформы Windows (UWP): разверните **Универсальные для Windows**, щелкните **Расширения**, а затем установите флажок **Microsoft Advertising SDK для XAML** (версия 10.0).

  * Для проекта Windows 8.1: разверните **Windows 8.1**, нажмите **Расширения**, а затем установите флажок **Ad Mediator SDK для XAML в Windows 8.1**. Этот параметр добавляет библиотеки и Microsoft Advertising, и Ad Mediator в проект, но вы можете не обращать внимания на библиотеки Ad Mediator.

  * Для проекта Windows Phone 8.1: разверните **Windows Phone 8.1**, нажмите **Расширения**, затем установите флажок **Ad Mediator SDK для XAML в Windows Phone 8.1**. Этот параметр добавляет библиотеки Microsoft Advertising и Ad Mediator в проект, но вы можете не обращать внимания на библиотеки Ad Mediator.

2.  В соответствующем файле заголовка для приложения (например, в файле DirectXPage.xaml.h) объявите объект [InterstitialAd](https://msdn.microsoft.com/library/windows/apps/microsoft.advertising.winrt.ui.interstitialad.aspx) и соответствующие методы обработчика событий.  

  > [!div class="tabbedCodeSnippets"]
  [!code-cpp[InterstitialAd](./code/AdvertisingSamples/InterstitialAdSamples/cpp/DirectXPage.xaml.h#Snippet1)]

3.  В этом же файле заголовка объявите несколько строковых полей, которые будут представлять собой идентификатор приложения и идентификатор рекламного блока вашей промежуточной рекламы. Следующий код присваивает поля `myAppId` и `myAdUnitId` тестовым значениям, представленным в разделе [Значения тестового режима](test-mode-values.md). Эти значения используются только для тестирования. Их необходимо заменить на фактические значения из Центра разработки для Windows перед публикацией приложения.

  > [!div class="tabbedCodeSnippets"]
  [!code-cpp[InterstitialAd](./code/AdvertisingSamples/InterstitialAdSamples/cpp/DirectXPage.xaml.h#Snippet2)]

4.  В файле CPP, в который вы хотите добавить код для отображения промежуточной рекламы, добавьте следующую ссылку на пространство имен. В следующих примерах кода предполагается, что вы добавляете код в файл DirectXPage.xaml.cpp вашего приложения.

  > [!div class="tabbedCodeSnippets"]
  [!code-cpp[InterstitialAd](./code/AdvertisingSamples/InterstitialAdSamples/cpp/DirectXPage.xaml.cpp#Snippet3)]

6.  В коде, который выполняется при запуске (например, в конструкторе для страницы), создайте объект **InterstitialAd** и привяжите обработчики событий к событиям этого объекта. В следующем примере кода ```InterstitialAdSamplesCpp``` представляет собой пространство имен для вашего проекта. Измените это имя с учетом содержания вашего кода.

  > [!div class="tabbedCodeSnippets"]
  [!code-cpp[InterstitialAd](./code/AdvertisingSamples/InterstitialAdSamples/cpp/DirectXPage.xaml.cpp#Snippet4)]

7. Приблизительно за 30–60 секунд до того, как должна будет отобразиться промежуточная реклама, используйте метод [RequestAd](https://msdn.microsoft.com/library/windows/apps/microsoft.advertising.winrt.ui.interstitialad.requestad.aspx), чтобы предварительно получить рекламное объявление. Этого времени будет достаточно для того, чтобы запросить и подготовить рекламное объявление до его отображения.

  > [!div class="tabbedCodeSnippets"]
  [!code-cpp[InterstitialAd](./code/AdvertisingSamples/InterstitialAdSamples/cpp/DirectXPage.xaml.cpp#Snippet5)]

7.  Проверьте то место вашего когда, в котором должна отображаться ваша реклама, на предмет того, что реклама **InterstitialAd** готова к отображению, а затем отобразите ее, используя метод [Show](https://msdn.microsoft.com/library/windows/apps/microsoft.advertising.winrt.ui.interstitialad.show.aspx).

  > [!div class="tabbedCodeSnippets"]
  [!code-cpp[InterstitialAd](./code/AdvertisingSamples/InterstitialAdSamples/cpp/DirectXPage.xaml.cpp#Snippet6)]

8.  Определите обработчики событий для объекта **InterstitialAd**.

  > [!div class="tabbedCodeSnippets"]
  [!code-cpp[InterstitialAd](./code/AdvertisingSamples/InterstitialAdSamples/cpp/DirectXPage.xaml.cpp#Snippet7)]

9. Создайте и протестируйте приложение, чтобы убедиться, что в нем отображается реклама.

<span/>
### <a name="release-your-app-with-live-ads-using-windows-dev-center"></a>Выпуск приложения с динамической рекламой с помощью Центра разработки для Windows

1.  На информационной панели Центра разработки перейдите на страницу **Монетизация** &gt; **Монетизировать с помощью рекламы** вашего приложения и [создайте рекламный блок](../publish/monetize-with-ads.md). Для типа единицы рекламы укажите **Промежуточная видеореклама**. Запишите идентификатор рекламного блока и идентификатор приложения.

2.  В своем коде замените тестовые значения и значения единиц фактическими, созданными в Центре разработки.

3.  [Отправьте приложение](../publish/app-submissions.md) в Магазин с помощью информационной панели в Центре разработки для Windows.

4.  Изучите [отчеты по показателям рекламы](../publish/advertising-performance-report.md) на информационной панели Центра разработки.

<span id="interstitialbestpractices10"/>
## <a name="interstitial-best-practices-and-policies"></a>Рекомендации и правила для промежуточной рекламы


Дополнительные сведения о том, как эффективно использовать промежуточную рекламу, и о действующих правилах, которые необходимо выполнять, см. в разделе [Рекомендации и правила для промежуточной рекламы](ui-and-user-experience-guidelines.md#interstitialbestpractices10).

<span id="targetplatform10"/>
## <a name="remove-reference-errors-target-a-specific-cpu-platform-xaml-and-html"></a>Удалите ошибки ссылок: ориентируйтесь на конкретную платформу ЦП (XAML или HTML)


При использовании библиотек Microsoft Libraries невозможно ориентироваться в проекте на **Любой ЦП**. Если проект ориентирован на платформу **Любой ЦП**, может отобразиться предупреждение после добавления ссылки в библиотеки Microsoft Advertising. Чтобы убрать это предупреждение, обновите проект, чтобы использовать сборку определенной архитектуры (например, **x86**). Подробнее см. в статье [Известные проблемы](known-issues-for-the-advertising-libraries.md).

## <a name="related-topics"></a>Связанные разделы


* [Пример кода промежуточной рекламы в C#](interstitial-ad-sample-code-in-c.md)
* [Пример кода промежуточной рекламы в JavaScript](interstitial-ad-sample-code-in-javascript.md)
* [Примеры рекламы на GitHub](http://aka.ms/githubads)

 

 

