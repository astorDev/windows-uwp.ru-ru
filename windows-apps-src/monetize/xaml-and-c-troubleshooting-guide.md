---
author: mcleanbyron
ms.assetid: 141900dd-f1d3-4432-ac8b-b98eaa0b0da2
description: Сведения о решении распространенных проблем разработки при использовании библиотек Microsoft Advertising в приложениях XAML.
title: Руководство по устранению неполадок в XAML и C#
ms.author: mcleans
ms.date: 08/23/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, uwp, рекламные объявления, реклама, AdControl, устранение неполадок, XAML, c#
ms.localizationpriority: medium
ms.openlocfilehash: d20f816bc6e4010daf01ebad87c0138df0f1243d
ms.sourcegitcommit: fbdc9372dea898a01c7686be54bea47125bab6c0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/08/2018
ms.locfileid: "4428307"
---
# <a name="xaml-and-c-troubleshooting-guide"></a>Руководство по устранению неполадок в XAML и C#

В этом разделе содержится описание решений распространенных проблем разработки, связанных с библиотеками Microsoft Advertising, в приложениях XAML.

* [XAML](#xaml)
  * [Элемент AdControl не отображается](#xaml-notappearing)
  * [«Черный ящик» моргает и исчезает](#xaml-blackboxblinksdisappears)
  * [Реклама не обновляется](#xaml-adsnotrefreshing)

* [C#](#csharp)
  * [Элемент AdControl не отображается](#csharp-adcontrolnotappearing)
  * [«Черный ящик» моргает и исчезает](#csharp-blackboxblinksdisappears)
  * [Реклама не обновляется](#csharp-adsnotrefreshing)

<span id="xaml"/>

## <a name="xaml"></a>XAML

<span id="xaml-notappearing"/>

### <a name="adcontrol-not-appearing"></a>Элемент AdControl не отображается

1.  Убедитесь, что в файле Package.appxmanifest выбрана возможность **Интернет (клиент)**.

2.  Проверьте идентификаторы приложения и рекламного блока. Эти идентификаторы должны соответствовать идентификатору приложения и идентификатору рекламного блока, полученным в Центре разработки для Windows. Дополнительные сведения см. в разделе [Настройка блоков рекламы в приложении](set-up-ad-units-in-your-app.md#live-ad-units).

    > [!div class="tabbedCodeSnippets"]
    ``` xml
    <UI:AdControl AdUnitId="{AdUnitID}" ApplicationId="{ApplicationID}"
                  Width="728" Height="90" />
    ```

3.  Проверьте свойства **Height** и **Width**. Им должен быть присвоен один из [размеров, поддерживаемых рекламными блоками для рекламных баннеров](supported-ad-sizes-for-banner-ads.md).

    > [!div class="tabbedCodeSnippets"]
    ``` xml
    <UI:AdControl AdUnitId="{AdUnitID}"
                  ApplicationId="{ApplicationID}"
                  Width="728" Height="90" />
    ```

4.  Проверьте расположение элемента. Элемент [AdControl](https://docs.microsoft.com/uwp/api/microsoft.advertising.winrt.ui.adcontrol) должен находиться в пределах видимой области.

5.  Проверьте свойство **Visibility**. Необязательному свойству **Visibility** не должно быть присвоено значение Collapsed или Hidden. Это свойство может быть задано в одной строке (как показано ниже) или во внешней таблице стилей.

    > [!div class="tabbedCodeSnippets"]
    ``` xml
    <UI:AdControl AdUnitId="{AdUnitID}"
                  ApplicationId="{ApplicationID}"
                  Visibility="Visible"
                  Width="728" Height="90" />
    ```

6.  Проверьте родительский элемент элемента **AdControl**. Если элемент **AdControl** помещен в родительский элемент, то родительский элемент должен быть активным и видимым.

    > [!div class="tabbedCodeSnippets"]
    ``` xml
    <StackPanel>
        <UI:AdControl AdUnitId="{AdUnitID}"
                      ApplicationId="{ApplicationID}"
                      Width="728" Height="90" />
    </StackPanel>
    ```

7.  Убедитесь, что элемент **AdControl** отображается в окне просмотра. Для правильного отображения элемента **AdControl** он должен быть видимым для рекламы.

8.  Динамические значения **ApplicationId** и **AdUnitId** не следует проверять в эмуляторе. Чтобы проверить, правильно ли работает элемент **AdControl**, используйте [тестовые значения](set-up-ad-units-in-your-app.md#test-ad-units) для **ApplicationId** и **AdUnitId**.

<span id="xaml-blackboxblinksdisappears"/>

### <a name="black-box-blinks-and-disappears"></a>«Черный ящик» моргает и исчезает

1.  Тщательно проверьте все действия, выполненные в предыдущем разделе [Элемент AdControl не отображается](#xaml-notappearing).

2.  Обработайте событие **ErrorOccurred** и используйте сообщение, которое передается в обработчик событий, чтобы определить, произошла ли ошибка и какой тип ошибки будет обрабатываться. Дополнительную информацию см. в разделе [Пошаговое руководство по обработке ошибок в XAML/C#](error-handling-in-xamlc-walkthrough.md).

    В этом примере демонстрируется обработчик событий **ErrorOccurred**. Первый фрагмент кода представляет собой разметку пользовательского интерфейса XAML.

    > [!div class="tabbedCodeSnippets"]
    ``` xml
    <UI:AdControl AdUnitId="{AdUnitID}"
                  ApplicationId="{ApplicationID}"
                  Width="728" Height="90"
                  ErrorOccurred="adControl_ErrorOccurred" />
    <TextBlock x:Name="TextBlock1" TextWrapping="Wrap" Width="500" Height="250" />
    ```

    В этом примере демонстрируется соответствующий код C#.

    > [!div class="tabbedCodeSnippets"]
    ``` cs
    private void adControl_ErrorOccurred(object sender,               
        Microsoft.Advertising.WinRT.UI.AdErrorEventArgs e)
    {
        TextBlock1.Text = e.ErrorMessage;
    }
    ```

    Наиболее распространенная ошибка, в следствие которой появляется "черный ящик", это— "Реклама недоступна". Эта ошибка означает, что для возврата по запросу реклама недоступна.

3.  Элемент [AdControl](https://docs.microsoft.com/uwp/api/microsoft.advertising.winrt.ui.adcontrol) работает нормально.

    По умолчанию **AdControl** сворачивается, если он не может отобразить рекламу. Если другие элементы являются дочерними по отношению к одному и тому же родительскому элементу, их можно использовать для заполнения пустого пространства, образовавшегося в результате сворачивания элемента **AdControl**, и они будут развернуты при отправке следующего запроса.

<span id="xaml-adsnotrefreshing"/>

### <a name="ads-not-refreshing"></a>Реклама не обновляется

1.  Проверьте свойство [IsAutoRefreshEnabled](https://docs.microsoft.com/uwp/api/microsoft.advertising.winrt.ui.adcontrol.isautorefreshenabled). По умолчанию этому необязательному свойству задано значение **True**. Если установлено значение **False**, для извлечения другой рекламы необходимо использовать метод [Refresh](https://docs.microsoft.com/uwp/api/microsoft.advertising.winrt.ui.adcontrol.refresh).

    > [!div class="tabbedCodeSnippets"]
    ``` xml
    <UI:AdControl AdUnitId="{AdUnitID}"
                  ApplicationId="{ApplicationID}"
                  Width="728" Height="90"
                  IsAutoRefreshEnabled="True" />
    ```

2.  Проверьте работоспособность вызовов метода **Refresh**. При использовании автоматического обновления применять метод **Refresh** для извлечения другой рекламы нельзя. При обновлении вручную метод **Refresh** следует вызывать только по истечении не менее 30–60 секунд в зависимости от текущего типа подключения для передачи данных, настроенного на устройстве.

    В следующем фрагменте кода показан пример использования метода **Refresh**. Первый фрагмент кода представляет собой разметку пользовательского интерфейса XAML.

    > [!div class="tabbedCodeSnippets"]
    ``` xml
    <UI:AdControl x:Name="adControl1"
                  AdUnitId="{AdUnit_ID}"
                  ApplicationId="{ApplicationID}"
                  Width="728" Height="90"
                  IsAutoRefreshEnabled="False" />
    ```

    Данный фрагмент кода показывает пример кода C#, лежащего в основе разметки пользовательского интерфейса.

    > [!div class="tabbedCodeSnippets"]
    ``` cs
    public RefreshAds()
    {
        var timer = new DispatcherTimer() { Interval = TimeSpan.FromSeconds(60) };
        timer.Tick += (s, e) => adControl1.Refresh();
        timer.Start();
    }
    ```

3.  Элемент **AdControl** работает нормально. Иногда одна и та же реклама может отображаться несколько раз подряд, в результате чего может показаться, что рекламные блоки не обновляются.

<span id="csharp"/>

## <a name="c"></a>C\# #

<span id="csharp-adcontrolnotappearing"/>

### <a name="adcontrol-not-appearing"></a>Элемент AdControl не отображается

1.  Убедитесь, что в файле Package.appxmanifest выбрана возможность **Интернет (клиент)**.

2.  Убедитесь, что создан экземпляр элемента **AdControl**. Если экземпляр элемента **AdControl** не создан, он будет недоступен.

    > [!div class="tabbedCodeSnippets"]
    [!code-cs[AdControl](./code/AdvertisingSamples/AdControlSamples/cs/MiscellaneousSnippets.cs#Snippet1)]

3.  Проверьте идентификаторы приложения и рекламного блока. Эти идентификаторы должны соответствовать идентификатору приложения и идентификатору рекламного блока, полученным в Центре разработки для Windows. Дополнительные сведения см. в разделе [Настройка блоков рекламы в приложении](set-up-ad-units-in-your-app.md#live-ad-units).

    > [!div class="tabbedCodeSnippets"]
    ``` cs
    adControl = new AdControl();
    adControl.ApplicationId = "{ApplicationID}";adControl.AdUnitId = "{AdUnitID}";
    adControl.Height = 90;
    adControl.Width = 728;
    ```

4.  Проверьте значения параметров **Height** и **Width**. Им должен быть присвоен один из [размеров, поддерживаемых рекламными блоками для рекламных баннеров](supported-ad-sizes-for-banner-ads.md).

    > [!div class="tabbedCodeSnippets"]
    ``` cs
    adControl = new AdControl();
    adControl.ApplicationId = "{ApplicationID}";
    adControl.AdUnitId = "{AdUnitID}";
    adControl.Height = 90;adControl.Width = 728;
    ```

5.  Убедитесь, что объект **AdControl** добавлен к родительскому элементу. Для отображения объект **AdControl** должен быть добавлен в качестве дочернего к родительскому элементу управления (например, **StackPanel** или **Grid**).

    > [!div class="tabbedCodeSnippets"]
    ``` cs
    ContentPanel.Children.Add(adControl);
    ```

6.  Проверьте значение параметра **Margin**. Элемент **AdControl** должен находиться в пределах видимой области.

7.  Проверьте свойство **Visibility**. Необязательному свойству **Visibility** должно быть присвоено значение **Visible**.

    > [!div class="tabbedCodeSnippets"]
    ``` cs
    adControl = new AdControl();
    adControl.ApplicationId = "{ApplicationID}";
    adControl.AdUnitId = "{AdUnitID}";
    adControl.Height = 90;
    adControl.Width = 728;
    adControl.Visibility = System.Windows.Visibility.Visible;
    ```

8.  Проверьте родительский элемент элемента **AdControl**. Родительский элемент должен быть активным и видимым.

9. Динамические значения **ApplicationId** и **AdUnitId** не следует проверять в эмуляторе. Чтобы проверить, правильно ли работает элемент **AdControl**, используйте [тестовые значения](set-up-ad-units-in-your-app.md#test-ad-units) для **ApplicationId** и **AdUnitId**.

<span id="csharp-blackboxblinksdisappears"/>

### <a name="black-box-blinks-and-disappears"></a>«Черный ящик» моргает и исчезает

1.  Тщательно проверьте все действия, выполненные в разделе [Элемент AdControl не отображается](#csharp-adcontrolnotappearing) выше.

2.  Обработайте событие **ErrorOccurred** и используйте сообщение, которое передается в обработчик событий, чтобы определить, произошла ли ошибка и какой тип ошибки будет обрабатываться. Дополнительную информацию см. в разделе [Пошаговое руководство по обработке ошибок в XAML/C#](error-handling-in-xamlc-walkthrough.md).

    В следующих примерах показан базовый код, необходимый для реализации вызова ошибки. Этот код XAML определяет элемент **TextBlock** для отображения сообщения об ошибке.

    > [!div class="tabbedCodeSnippets"]
    ``` xml
    <TextBlock x:Name="TextBlock1" TextWrapping="Wrap" Width="500" Height="250" />
    ```

    Эти код C# получает сообщение об ошибке и отображает его в элементе **TextBlock**.

    > [!div class="tabbedCodeSnippets"]
    [!code-cs[AdControl](./code/AdvertisingSamples/AdControlSamples/cs/MiscellaneousSnippets.cs#Snippet2)]

    Наиболее распространенная ошибка, в следствие которой появляется «черный ящик», это— «Реклама недоступна». Эта ошибка означает, что для возврата по запросу реклама недоступна.

3.  Элемент **AdControl** работает нормально. Иногда одна и та же реклама может отображаться несколько раз подряд, в результате чего может показаться, что рекламные блоки не обновляются.

<span id="csharp-adsnotrefreshing"/>

### <a name="ads-not-refreshing"></a>Реклама не обновляется

1.  Проверьте, задано ли для свойства [IsAutoRefreshEnabled](https://docs.microsoft.com/uwp/api/microsoft.advertising.winrt.ui.adcontrol.isautorefreshenabled.aspx) вашего элемента **AdControl** значение false. По умолчанию этому необязательному свойство задано значение **true**. Если установлено значение **false**, для извлечения другой рекламы необходимо использовать метод **Refresh**.

2.  Проверьте работоспособность вызовов метода [Refresh](https://docs.microsoft.com/uwp/api/microsoft.advertising.winrt.ui.adcontrol.refresh.aspx). При использовании автоматического обновления (для свойства **IsAutoRefreshEnabled** задано значение **true**) применять метод **Refresh** для извлечения другой рекламы нельзя. При обновлении вручную (для свойства **IsAutoRefreshEnabled** задано значение **false**) метод **Refresh** следует вызывать только по истечении не менее 30–60 секунд в зависимости от текущего типа подключения для передачи данных, настроенного на устройстве.

    В следующем примере показан вызов метода **Refresh**.

    > [!div class="tabbedCodeSnippets"]
    [!code-cs[AdControl](./code/AdvertisingSamples/AdControlSamples/cs/MiscellaneousSnippets.cs#Snippet3)]

3.  Элемент **AdControl** работает нормально. Иногда одна и та же реклама может отображаться несколько раз подряд, в результате чего может показаться, что рекламные блоки не обновляются.

 

 
