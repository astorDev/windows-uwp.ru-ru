---
author: mcleanbyron
ms.assetid: 1f970d38-2338-470e-b5ba-811402752fc4
description: "Узнайте, как включить промежуточную рекламу в Windows 10, Windows 8.1 или Windows Phone 8.1 с помощью библиотек Microsoft Advertising в составе пакета SDK Microsoft Store Engagement and Monetization."
title: "Промежуточная реклама"
translationtype: Human Translation
ms.sourcegitcommit: cf695b5c20378f7bbadafb5b98cdd3327bcb0be6
ms.openlocfilehash: 0f159409bb584aacaf66550efe8d147cd8fddd50

---

# Промежуточная реклама


\[ Обновлено для приложений UWP в Windows 10. Статьи о Windows 8.x см. в [архиве](http://go.microsoft.com/fwlink/p/?linkid=619132) \]

В этом пошаговом руководстве показано, как включить промежуточную рекламу в Windows 10, Windows 8.1 или Windows Phone 8.1 с помощью библиотек Microsoft Advertising в составе пакета SDK Microsoft Store Engagement and Monetization.

Полные примеры проектов, демонстрирующие способы добавления промежуточной рекламы в приложения на JavaScript/HTML, а также в приложения на XAML с помощью языков C# and C++, см. в [примерах рекламы на GitHub](http://aka.ms/githubads).

<span id="whatareinterstitialads10"/>
## Что такое промежуточная реклама?

В отличие от рекламных баннеров, промежуточная реклама (или *промежуточные рекламные ролики*) отображаются во весь экран приложения. В играх, в основном, используются две основные формы такой рекламы.

* Рекламу *Paywall* пользователь обязан смотреть с определенной регулярной периодичностью, например между разными уровнями игры:

    ![whatisaninterstitial](images/13-ed0a333b-0fc8-4ca9-a4c8-11e8b4392831.png)

* Реклама *за вознаграждения* отображается тогда, когда пользователь намеренно ищет определенные преимущества, например подсказку или дополнительное время для прохождения уровня, и запускает видеорекламу из пользовательского интерфейса приложения.

    Важно отметить, что этот пакет SDK не обрабатывает никакие элементы пользовательского интерфейса, кроме как во время воспроизведения. См. [передовые практики размещения промежуточной рекламы](ui-and-user-experience-guidelines.md#interstitialbestpractices10), чтобы получить информацию о рекомендуемых и нерекомендуемых действиях при добавлении промежуточной рекламы в приложение.

## Создание приложения с промежуточной рекламой


### Предварительные требования

1.  Установите [пакет SDK Microsoft Store Engagement and Monetization](http://aka.ms/store-em-sdk) для Visual Studio 2015 или Visual Studio 2013.

2.  В Visual Studio откройте свой проект либо создайте новый.

### Написание кода

* [Действия для приложения XAML/.NET](#interstitialadsxaml10)

* [Действие для приложений HTML/JavaScript](#interstitialadshtml10)

* [Действия для C++ (межпрограммное взаимодействие DirectX)](#interstitialadsdirectx10)

<span id="interstitialadsxaml10"/>
### Промежуточная реклама (XAML/.NET)

> **Примечание**. В этом разделе приводятся примеры на C#, однако Visual Basic и C++ также поддерживаются.
 
1. Откройте проект в Visual Studio.
2. В **Диспетчере ссылок** выберите одну из следующих ссылок в зависимости от типа проекта.

    -   Для проекта универсальной платформы Windows (UWP): разверните **Универсальные для Windows**, щелкните **Расширения**, а затем установите флажок **Microsoft Advertising SDK для XAML** (версия 10.0).

    -   Для проекта Windows 8.1: разверните **Windows 8.1**, нажмите **Расширения**, а затем установите флажок **Ad Mediator SDK для XAML в Windows 8.1**. Этот параметр добавляет библиотеки и Microsoft Advertising, и Ad Mediator в проект, но вы можете не обращать внимания на библиотеки Ad Mediator.

    -   Для проекта Windows Phone 8.1: разверните **Windows Phone 8.1**, нажмите **Расширения**, затем установите флажок **Ad Mediator SDK для XAML в Windows Phone 8.1**. Этот параметр добавляет библиотеки и Microsoft Advertising, и Ad Mediator в проект, но вы можете не обращать внимания на библиотеки Ad Mediator.

3.  В коде приложения добавьте следующую ссылку на пространство имен.

    ``` syntax
    using Microsoft.Advertising.WinRT.UI;
    ```

4.  Объявите свойства `MyAppId` и `MyAdUnitId`.

    ``` syntax
    var MyAppId = "<your app id for windows>";
    var MyAdUnitId = "<your adunit for windows";

    // if your code is in a universal solution and resides in the shared project
    // you can opt to use #if WINDOWS_APP or WINDOWS_PHONE_APP to override with different
    // identifiers for each
#if WINDOWS_PHONE_APP
    MyAppId = "<your app id for phone>";
    MyAdUnitId = "<your adunit for phone>";
#endif
    ```

    > **Примечание**. Перед отправкой приложения замените тестовые значения действительными.

5.  Создайте экземпляр [InterstitialAd](https://msdn.microsoft.com/library/windows/apps/microsoft.advertising.winrt.ui.interstitialad.aspx), привяжите к нему все обработчики событий и запросите объявление.

    ``` syntax
    // instantiate an InterstitialAd
    InterstitialAd MyVideoAd = new InterstitialAd();

    // wire up all 4 events, see below for function templates
    MyVideoAd.AdReady += MyVideoAd_AdReady;
    MyVideoAd.ErrorOccurred += MyVideoAd_ErrorOccurred;
    MyVideoAd.Completed += MyVideoAd_Completed;
    MyVideoAd.Cancelled += MyVideoAd_Cancelled;

    // pre-fetch an ad 30-60 seconds before you need it
    MyVideoAd.RequestAd(AdType.Video, MyAppId, MyAdUnitId);
    ```

6.  Покажите объявление в точке кода, где оно должно отображаться, предварительно убедившись в готовности рекламы.

    ``` syntax
    if ((InterstitialAdState.Ready) == (MyVideoAd.State))
    {
      MyVideoAd.Show();
    }
    ```

7.  Определите события и создайте для них код.

    ``` syntax
    void MyVideoAd_AdReady(object sender, object e)
    {
      // code
    }

    void MyVideoAd_ErrorOccurred(object sender, AdErrorEventArgs e)
    {
      // code
    }

    void MyVideoAd_Completed(object sender, object e)
    {  
      // code
    }

    void MyVideoAd_Cancelled(object sender, object e)
    {
      // code
    }
    ```

8.  Назначьте для свойства `MyAppId` тестовое значение, указанное в разделе [Значения тестового режима](test-mode-values.md). Это значение используется только для тестирования; перед публикацией приложения его необходимо заменить реальным значением.

    ``` syntax
    var MyAppId = "d25517cb-12d4-4699-8bdc-52040c712cab";
    ```

9.  Назначьте для свойства `MyAdUnitId` тестовое значение, указанное в разделе [Значения тестового режима](test-mode-values.md). Это значение используется только для тестирования; перед публикацией приложения его необходимо заменить реальным значением.

    ``` syntax
    var MyAdUnitId = "11389925";
    ```

10.  Создайте и протестируйте приложение, чтобы убедиться, что в нем отображается реклама.

<span id="interstitialadshtml10"/>
### Промежуточная реклама (HTML/JavaScript)

В этом примере предполагается, что вы создали проект универсального приложения для JavaScript в Visual Studio 2015 и для конкретного процессора.

1. Откройте проект в Visual Studio.
2.  В **Диспетчере ссылок** выберите одну из следующих ссылок в зависимости от типа проекта.

    -   Для проекта универсальной платформы Windows (UWP): разверните **Универсальные для Windows**, щелкните **Расширения**, а затем установите флажок **Microsoft Advertising SDK для JavaScript** (версия 10.0).

    -   Для проекта Windows 8.1: разверните **Windows 8.1**, щелкните **Расширения**, а затем установите флажок **Собственный Microsoft Advertising SDK для Windows 8.1 (JS)**.

    -   Для проекта Windows 8.1: разверните **Windows Phone 8.1**, щелкните **Расширения**, а затем установите флажок **Собственный Microsoft Advertising SDK для Windows Phone 8.1 (JS)**.

3.  В HTML добавьте следующую ссылки на сценарии.

    ``` syntax
    <script src="//Microsoft.Advertising.JavaScript/ad.js"></script>
    ```

4.  Объявите свойства `myAppId` и `myAdUnitId`.

    ``` syntax
    <script>
      var myAppId = "<your app id>";
      var myAdUnitId = "<your adunit id>";
    </script>
    ```

5.  Создайте экземпляр **InterstitialAd**, привяжите к нему все обработчики событий и запросите объявление.

    ``` syntax
    // instantiate an InterstitialAd
    window.interstitialAd = new MicrosoftNSJS.Advertising.InterstitialAd();

    // wire up all 4 events, see below for function templates
    window.interstitialAd.onAdReady = readyHandler;
    window.interstitialAd.onErrorOccurred = errorHandler;
    window.interstitialAd.onCompleted = completeHandler;
    window.interstitialAd.onCancelled = cancelHandler;

    // pre-fetch an ad 30-60 seconds before you need it
    var myAdType = MicrosoftNSJS.Advertising.InterstitialAdType.video;
    window.interstitialAd.requestAd(myAdType, myAppId, myAdUnitId);
    ```

6.  Покажите объявление в точке кода, где оно должно отображаться, предварительно убедившись в готовности рекламы.

    ``` syntax
    if ((MicrosoftNSJS.Advertising.InterstitialAdState.ready) == (window.interstitialAd.state)) {
             window.interstitialAd.show();
    }
    ```

7.  Определите события и создайте для них код.

    ``` syntax
    function readyHandler(sender) {
      // code
    }

    function errorHandler(sender, args) {
      // code
    }

    function completeHandler(sender) {
      // code
    }

    function cancelHandler(sender) {
      // code
    }
    ```

7.  Назначьте для свойства `MyAppId` тестовое значение, указанное в разделе [Значения тестового режима](test-mode-values.md). Это значение используется только для тестирования; перед публикацией приложения его необходимо заменить реальным значением.

    ``` syntax
    var MyAppId = "d25517cb-12d4-4699-8bdc-52040c712cab";
    ```

8.  Назначьте для свойства `MyAdUnitId` тестовое значение, указанное в разделе [Значения тестового режима](test-mode-values.md). Это значение используется только для тестирования; перед публикацией приложения его необходимо заменить реальным значением.

    ``` syntax
    var MyAdUnitId = "11389925";
    ```

9.  Создайте и протестируйте приложение, чтобы убедиться, что в нем отображается реклама.

<span id="interstitialadsdirectx10"/>
### Промежуточная реклама (C++ и DirectX с межпрограммным взаимодействием XAML)

В этом примере предполагается, что вы создали проект универсального приложения для XAML в Visual Studio 2015 и для определенной архитектуры ЦП.

> **Важно!** Этот код написан на языке C++ в соответствии с требованиями DirectX.

 
1. Откройте проект в Visual Studio.
1.  В **Диспетчере ссылок** выберите одну из следующих ссылок в зависимости от типа проекта.

    -   Для проекта универсальной платформы Windows (UWP): разверните **Универсальные для Windows**, щелкните **Расширения**, а затем установите флажок **Microsoft Advertising SDK для XAML** (версия 10.0).

    -   Для проекта Windows 8.1: разверните **Windows 8.1**, нажмите **Расширения**, а затем установите флажок **Ad Mediator SDK для XAML в Windows 8.1**. Этот параметр добавляет библиотеки и Microsoft Advertising, и Ad Mediator в проект, но вы можете не обращать внимания на библиотеки Ad Mediator.

    -   Для проекта Windows Phone 8.1: разверните **Windows Phone 8.1**, нажмите **Расширения**, затем установите флажок **Ad Mediator SDK для XAML в Windows Phone 8.1**. Этот параметр добавляет библиотеки и Microsoft Advertising, и Ad Mediator в проект, но вы можете не обращать внимания на библиотеки Ad Mediator.

2.  В соответствующем файле заголовка для приложения объявите объект промежуточной рекламы и соответствующие свойства и методы.

    ``` syntax
    Microsoft::Advertising::WinRT::UI::InterstitialAd^ m_ia;
    void OnAdReady(Object^ sender, Object^ args);
    void OnAdCompleted(Object^ sender, Object^ args);
    void OnAdCancelled(Object^ sender, Object^ args);
    void OnAdError (Object^ sender,  Microsoft::Advertising::WinRT::UI::AdErrorEventArgs^ args);
    ```

3.  Объявите свойства `AppId` и `AdUnitId`.

    ``` syntax
    #if WINDOWS_PHONE_APP
    static Platform::String^ IA_APPID = L"<your app id for phone>";
    static Platform::String^ IA_ADUNITID = L"<your ad unit for phone>";
    #endif

    #if WINDOWS_APP
    static Platform::String^ IA_APPID = L"<your app id for windows>";
    static Platform::String^ IA_ADUNITID = L"<your ad unit for windows>";
    #endif
    ```

4.  В CPP-файле добавьте ссылки на пространства имен.

    ``` syntax
    using namespace Microsoft::Advertising::WinRT::UI;
    ```

5.  Создайте экземпляр **InterstitialAd**, привяжите к нему все обработчики событий и запросите объявление.

    ``` syntax
    // Instantiate an InterstitialAd.
    m_ia = ref new InterstitialAd();

    // Wire up all 4 events, see below for function templates.            
    m_ia->AdReady += ref new Windows::Foundation::EventHandler<Platform::Object ^>
        (this, &Simple3DGameXaml::DirectXPage::OnAdReady);
    m_ia->Completed += ref new Windows::Foundation::EventHandler<Platform::Object ^>
        (this, &Simple3DGameXaml::DirectXPage::OnAdCompleted);
    m_ia->Cancelled += ref new Windows::Foundation::EventHandler<Platform::Object ^>
        (this, &Simple3DGameXaml::DirectXPage::OnAdCancelled);
    m_ia->ErrorOccurred += ref new
        Windows::Foundation::EventHandler<Microsoft::Advertising::WinRT::UI::AdErrorEventArgs ^>
            (this, &Simple3DGameXaml::DirectXPage::OnAdError);

    // Pre-fetch an ad 30-60 seconds before you need it.
    m_ia->RequestAd(AdType::Video, IA_APPID, IA_ADUNITID);
    ```

6.  Покажите объявление в точке кода, где оно должно отображаться, предварительно убедившись в готовности рекламы.

    ``` syntax
    if ((InterstitialAdState::Ready == m_ia->State))
    {
        m_ia->Show();
    }
    ```

7.  Определите события и создайте для них код.

    ``` syntax
    void DirectXPage::OnAdReady(Object^ sender, Object^ args)
    {
      // code
    }

    void DirectXPage::OnAdCompleted(Object^ sender, Object^ args)
    {
      // code
    }

    void DirectXPage::OnAdCancelled(Object^ sender, Object^ args)
    {
      // code
    }

    void DirectXPage::OnAdError
      (Object^ sender, Microsoft::Advertising::WinRT::UI::AdErrorEventArgs^ args)
    {
      // code
    }
    ```

8.  Назначьте для свойства `AppId` тестовое значение, указанное в разделе [Значения тестового режима](test-mode-values.md). Это значение используется только для тестирования; перед публикацией приложения его необходимо заменить реальным значением.

    ``` syntax
    static Platform::String^ IA_APPID = L"d25517cb-12d4-4699-8bdc-52040c712cab";
    ```

9.  Назначьте для свойства `AdUnitId` тестовое значение, указанное в разделе [Значения тестового режима](test-mode-values.md). Это значение используется только для тестирования; перед публикацией приложения его необходимо заменить реальным значением.

    ``` syntax
    static Platform::String^ IA_ADUNITID = L"11389925";
    ```

10. Создайте и протестируйте приложение, чтобы убедиться, что в нем отображается реклама.

### Выпуск приложения с динамической рекламой с помощью Центра разработки для Windows

1.  На информационной панели Центра разработки, перейдите на страницу **Получение дохода**&gt;**Получение дохода с помощью рекламы** вашего приложения и [создайте автономный блок Microsoft Advertising](../publish/monetize-with-ads.md). Для типа единицы рекламы укажите **Промежуточная видеореклама**. Запишите и идентификатор рекламного блока, и идентификатор приложения.

2.  В своем коде замените тестовые значения и значения единиц фактическими, созданными в Центре разработки.

3.  [Отправьте приложение](../publish/app-submissions.md) в Магазин с помощью информационной панели в Центре разработки Windows.

4.  Изучите [отчеты по показателям рекламы](../publish/advertising-performance-report.md) на информационной панели Центра разработки.

<span id="interstitialbestpractices10"/>
## Рекомендации для промежуточной рекламы


Дополнительные сведения о том, как эффективно использовать промежуточную рекламу, см. в разделе [Рекомендации по созданию пользовательского интерфейса](ui-and-user-experience-guidelines.md).

<span id="targetplatform10"/>
## Удалите ошибки ссылок: ориентируйтесь на конкретную платформу ЦП (XAML или HTML)


При использовании библиотек Microsoft Libraries невозможно ориентироваться в проекте на **Любой ЦП**. Если проект ориентирован на платформу **Любой ЦП**, может отобразиться предупреждение после добавления ссылки в библиотеки Microsoft Advertising. Чтобы убрать это предупреждение, обновите проект, чтобы использовать сборку определенной архитектуры (например, **x86**). Подробнее см. в статье [Известные проблемы](known-issues-for-the-advertising-libraries.md).

## Связанные разделы


* [Пример кода промежуточной рекламы в C#](interstitial-ad-sample-code-in-c.md)
* [Пример кода промежуточной рекламы в JavaScript](interstitial-ad-sample-code-in-javascript.md)
* [Примеры рекламы на GitHub](http://aka.ms/githubads)

 

 



<!--HONumber=Jun16_HO4-->


