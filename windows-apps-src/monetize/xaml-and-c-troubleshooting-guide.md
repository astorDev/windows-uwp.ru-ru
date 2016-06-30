---
author: mcleanbyron
ms.assetid: 141900dd-f1d3-4432-ac8b-b98eaa0b0da2
description: "Сведения о решении распространенных проблем разработки при использовании библиотек Microsoft Advertising в приложениях XAML."
title: "Руководство по устранению неполадок в XAML и C#"
translationtype: Human Translation
ms.sourcegitcommit: cf695b5c20378f7bbadafb5b98cdd3327bcb0be6
ms.openlocfilehash: ef9ad8f8056b17793d7ad8230e410e014edf2c95

---

# Руководство по устранению неполадок в XAML и C#

\[ Обновлено для приложений UWP в Windows 10. Статьи о Windows 8.x, см. в [архиве](http://go.microsoft.com/fwlink/p/?linkid=619132) \]

В этом разделе содержится описание решений распространенных проблем разработки, связанных с библиотеками Microsoft Advertising, в приложениях XAML.

-   [XAML](#xaml)

    -   [Элемент AdControl не отображается](#xaml-notappearing)

    -   [«Черный ящик» моргает и исчезает](#xaml-blackboxblinksdisappears)

    -   [Реклама не обновляется](#xaml-adsnotrefreshing)

-   [C#](#csharp)

    -   [Элемент AdControl не отображается](#csharp-adcontrolnotappearing)

    -   [«Черный ящик» моргает и исчезает](#csharp-blackboxblinksdisappears)

    -   [Реклама не обновляется](#csharp-adsnotrefreshing)

<span id="xaml"/>
## XAML

<span id="xaml-notappearing"/>
### Элемент AdControl не отображается

1.  Убедитесь, что в файле Package.appxmanifest выбрана возможность **Интернет (клиент)**.

2.  Проверьте идентификаторы приложения и рекламного блока. Эти идентификаторы должны соответствовать идентификатору приложения и идентификатору рекламного блока, полученным в Центре разработки для Windows. Дополнительные сведения см. в разделе [Настройка блоков рекламы в приложении](set-up-ad-units-in-your-app.md).

    ``` syntax
    <UI:AdControl AdUnitId="{AdUnitID}" ApplicationId="{ApplicationID}"
                  Width="728" Height="90" />
    ```

3.  Проверьте свойства **Height** и **Width**. Им должен быть присвоен один из [размеров, поддерживаемых рекламными блоками для рекламных баннеров](supported-ad-sizes-for-banner-ads.md).

    ``` syntax
    <UI:AdControl AdUnitId="{AdUnitID}"
                  ApplicationId="{ApplicationID}"
                  Width="728" Height="90" />
    ```

4.  Проверьте расположение элемента. Элемент [AdControl](https://msdn.microsoft.com/library/windows/apps/microsoft.advertising.winrt.ui.adcontrol.aspx) должен находиться в пределах видимой области.

5.  Проверьте свойство **Visibility**. Необязательному свойству **Visibility** не должно быть присвоено значение Collapsed или Hidden. Это свойство может быть задано в одной строке (как показано ниже) или во внешней таблице стилей.

    ``` syntax
    <UI:AdControl AdUnitId="{AdUnitID}"
                  ApplicationId="{ApplicationID}"
                  Visibility="Visible"
                  Width="728" Height="90" />
    ```

6.  Проверьте свойство **IsEnabled**. Необязательному свойству `IsEnabled` должно быть присвоено значение `True`.

    ``` syntax
    <UI:AdControl AdUnitId="{AdUnitID}"
                  ApplicationId="{ApplicationID}"
                  IsEnabled="True"
                  Width="728" Height="90" />
    ```

7.  Проверьте родительский элемент элемента **AdControl**. Если элемент **AdControl** помещен в родительский элемент, то родительский элемент должен быть активным и видимым.

    ``` syntax
    <StackPanel>
        <UI:AdControl AdUnitId="{AdUnitID}"
                      ApplicationId="{ApplicationID}"
                      Width="728" Height="90" />
    </StackPanel>
    ```

8.  Убедитесь, что элемент **AdControl** отображается в окне просмотра. Для правильного отображения элемента **AdControl** он должен быть видимым для рекламы.

9.  Динамические значения **ApplicationId** и **AdUnitId** не следует проверять в эмуляторе. Чтобы проверить, правильно ли работает элемент **AdControl**, используйте проверочные идентификаторы для **ApplicationId** и **AdUnitId**, расположенные в разделе [Значения тестового режима](test-mode-values.md).

<span id="xaml-blackboxblinksdisappears"/>
### «Черный ящик» моргает и исчезает

1.  Тщательно проверьте все действия, выполненные в предыдущем разделе [Элемент AdControl не отображается](#xaml-notappearing).

2.  Обработайте событие **ErrorOccurred** и используйте сообщение, которое передается в обработчик событий, чтобы определить, произошла ли ошибка и какой тип ошибки будет обрабатываться. Дополнительную информацию см. в разделе [Пошаговое руководство по обработке ошибок в XAML/C#](error-handling-in-xamlc-walkthrough.md).

    В этом примере демонстрируется обработчик событий **ErrorOccurred**. Первый фрагмент кода представляет собой разметку пользовательского интерфейса XAML.

    ``` syntax
    <UI:AdControl AdUnitId="{AdUnitID}"
                  ApplicationId="{ApplicationID}"
                  Width="728" Height="90"
                  ErrorOccurred="adControl_ErrorOccurred" />

    <TextBlock x:Name="TextBlock1" TextWrapping="Wrap" Width="500" Height="250" />
    ```

    В этом примере демонстрируется соответствующий код.

    ``` syntax
    private void adControl_ErrorOccurred(object sender,               
        Microsoft.Advertising.WinRT.UI.AdErrorEventArgs e)
    {
        TextBlock1.Text = e.Error.Message;
    }
    ```

    Наиболее распространенная ошибка, в следствие которой появляется «черный ящик», это — «Реклама недоступна». Эта ошибка означает, что для возврата по запросу реклама недоступна.

3.  Элемент [AdControl](https://msdn.microsoft.com/library/windows/apps/microsoft.advertising.winrt.ui.adcontrol.aspx) работает нормально.

    По умолчанию **AdControl** сворачивается, если он не может отобразить рекламу. Если другие элементы являются дочерними по отношению к одному и тому же родительскому элементу, их можно использовать для заполнения пустого пространства, образовавшегося в результате сворачивания элемента **AdControl**, и они будут развернуты при отправке следующего запроса.

<span id="xaml-adsnotrefreshing"/>
### Реклама не обновляется

1.  Проверьте свойство [IsAutoRefreshEnabled](https://msdn.microsoft.com/library/windows/apps/microsoft.advertising.winrt.ui.adcontrol.isautorefreshenabled.aspx). По умолчанию этому необязательному свойству задано значение **True**. Если установлено значение **False**, для извлечения другой рекламы необходимо использовать метод [Refresh](https://msdn.microsoft.com/library/windows/apps/microsoft.advertising.winrt.ui.adcontrol.refresh.aspx).

    ``` syntax
    <UI:AdControl AdUnitId="{AdUnitID}"
                  ApplicationId="{ApplicationID}"
                  Width="728" Height="90"
                  IsAutoRefreshEnabled="True" />
    ```

2.  Проверьте работоспособность вызовов метода **Refresh**. При использовании автоматического обновления применять метод **Refresh** для извлечения другой рекламы нельзя. При обновлении вручную метод **Refresh** следует вызывать только по истечении не менее 30–60 секунд в зависимости от текущего типа подключения для передачи данных, настроенного на устройстве.

    В следующем фрагменте кода показан пример использования метода **Refresh**. Первый фрагмент кода представляет собой разметку пользовательского интерфейса XAML.

    ``` syntax
    <UI:AdControl x:Name="adControl1"
                  AdUnitId="{AdUnit_ID}"
                  ApplicationId="{ApplicationID}"
                  Width="728" Height="90"
                  IsAutoRefreshEnabled="False" />
    ```

    Данный фрагмент кода показывает пример кода C#, лежащего в основе разметки пользовательского интерфейса.

    ``` syntax
    public Ads()
    {
        var timer = new DispatcherTimer() { Interval = TimeSpan.FromSeconds(60) };
        timer.Tick += (s, e) => adControl1.Refresh();
        timer.Start();
    }
    ```

3.  Элемент **AdControl** работает нормально. Иногда одна и та же реклама может отображаться несколько раз подряд, в результате чего может показаться, что рекламные блоки не обновляются.

<span id="csharp"/>
## C\# #

<span id="csharp-adcontrolnotappearing"/>
### Элемент AdControl не отображается

1.  Убедитесь, что в файле Package.appxmanifest выбрана возможность **Интернет (клиент)**.

2.  Убедитесь, что создан экземпляр элемента **AdControl**. Если экземпляр элемента **AdControl** не создан, он будет недоступен.

    ``` syntax
    using Microsoft.Advertising.WinRT.UI;

    namespace App1
    {
        public sealed partial class MainPage : Page
        {
            AdControl adControl;

            public MainPage()
            {
                this.InitializeComponent();

                adControl = new AdControl()
                {
                    ApplicationId = "{ApplicationID}",
                    AdUnitId = "{AdUnitID}",
                    Height = 90,
                    Width = 728
                };
            }
        }
    }
    ```

3.  Проверьте идентификаторы приложения и рекламного блока. Эти идентификаторы должны соответствовать идентификатору приложения и идентификатору рекламного блока, полученным в Центре разработки для Windows. Дополнительные сведения см. в разделе [Настройка блоков рекламы в приложении](set-up-ad-units-in-your-app.md).

    ``` syntax
    adControl = new AdControl();
    adControl.ApplicationId = "{ApplicationID}";adControl.AdUnitId = "{AdUnitID}";
    adControl.Height = 90;
    adControl.Width = 728;
    ```

4.  Проверьте значения параметров **Height** и **Width**. Им должен быть присвоен один из [размеров, поддерживаемых рекламными блоками для рекламных баннеров](supported-ad-sizes-for-banner-ads.md).

    ``` syntax
    adControl = new AdControl();
    adControl.ApplicationId = "{ApplicationID}";
    adControl.AdUnitId = "{AdUnitID}";
    adControl.Height = 90;adControl.Width = 728;
    ```

5.  Убедитесь, что объект **AdControl** добавлен к родительскому элементу. Для отображения объект **AdControl** должен быть добавлен в качестве дочернего к родительскому элементу управления (например, **StackPanel** или **Grid**).

    ``` syntax
    ContentPanel.Children.Add(adControl);
    ```

6.  Проверьте значение параметра **Margin**. Элемент **AdControl** должен находиться в пределах видимой области.

7.  Проверьте свойство **Visibility**. Необязательному свойству **Visibility** должно быть присвоено значение **Visible**.

    ``` syntax
    adControl = new AdControl();
    adControl.ApplicationId = "{ApplicationID}";
    adControl.AdUnitId = "{AdUnitID}";
    adControl.Height = 90;
    adControl.Width = 728;
    adControl.Visibility = System.Windows.Visibility.Visible;
    ```

8.  Проверьте свойство **IsEnabled**. Необязательному свойству **IsEnabled** должно быть присвоено значение **True**.

    ``` syntax
    adControl = new AdControl();
    adControl.ApplicationId = "{ApplicationID}";
    adControl.AdUnitId = "{AdUnitID}";
    adControl.Height = 90;
    adControl.Width = 728;
    adControl.IsEnabled = True;
    ```

9.  Проверьте родительский элемент элемента **AdControl**. Родительский элемент должен быть активным и видимым.

10. Динамические значения **ApplicationId** и **AdUnitId** не следует проверять в эмуляторе. Чтобы проверить, правильно ли работает элемент **AdControl**, используйте проверочные идентификаторы для **ApplicationId** и **AdUnitId**, расположенные в разделе [Значения тестового режима](test-mode-values.md).

<span id="csharp-blackboxblinksdisappears"/>
### «Черный ящик» моргает и исчезает

1.  Тщательно проверьте все действия, выполненные в разделе [Элемент AdControl не отображается](#csharp-adcontrolnotappearing) выше.

2.  Обработайте событие **ErrorOccurred** и используйте сообщение, которое передается в обработчик событий, чтобы определить, произошла ли ошибка и какой тип ошибки будет обрабатываться. Дополнительную информацию см. в разделе [Пошаговое руководство по обработке ошибок в XAML/C#](error-handling-in-xamlc-walkthrough.md).

    В следующих примерах показан базовый код, необходимый для реализации вызова ошибки. Этот код XAML определяет элемент **TextBlock** для отображения сообщения об ошибке.

    ``` syntax
    <TextBlock x:Name="TextBlock1" TextWrapping="Wrap" Width="500" Height="250" />
    ```

    Эти код C# получает сообщение об ошибке и отображает его в элементе **TextBlock**.

    ``` syntax
    using Microsoft.Advertising.WinRT.UI;

    namespace App1
    {
        public partial class MainPage : Page
        {
            AdControl adControl;

            public MainPage()
            {
                this.InitializeComponent();

                adControl = new AdControl();
                adControl.ApplicationId = "{ApplicationID}";
                adControl.AdUnitId = "{AdUnitID}";
                adControl.Height = 90;
                adControl.Width = 728;
                adControl.ErrorOccurred += (s,e) =>
                {
                    TextBlock1.Text = e.Error.Message;
                };
            }
        }
    }
    ```

    Наиболее распространенная ошибка, в следствие которой появляется «черный ящик», это — «Реклама недоступна». Эта ошибка означает, что для возврата по запросу реклама недоступна.

3.  Элемент **AdControl** работает нормально. Иногда одна и та же реклама может отображаться несколько раз подряд, в результате чего может показаться, что рекламные блоки не обновляются.

<span id="csharp-adsnotrefreshing"/>
### Реклама не обновляется

1.  Проверьте свойство **IsAutoRefreshEnabled**. По умолчанию этому необязательному свойству задано значение **True**. Если установлено значение **False**, для извлечения другой рекламы необходимо использовать метод **Refresh**.

    В следующем примере показано, как использовать свойство **isAutoRefreshEnabled**.

    ``` syntax
    adControl = new AdControl();
    adControl.ApplicationId = "{ApplicationID}";
    adControl.AdUnitId = "{AdUnitID}";
    adControl.Height = 90;
    adControl.Width = 728;
    adControl.IsAutoRefreshEnabled = true;
    ```

2.  Проверьте работоспособность вызовов метода **Refresh**. При использовании автоматического обновления применять метод **Refresh** для извлечения другой рекламы нельзя. При обновлении вручную метод **Refresh** следует вызывать только по истечении не менее 30–60 секунд в зависимости от текущего типа подключения для передачи данных, настроенного на устройстве.

    В следующем примере показан вызов метода **Refresh**.

    ``` syntax
    public MainPage()
    {
        InitializeComponent();

        adControl = new AdControl();
        adControl.ApplicationId = "{ApplicationID}";
        adControl.AdUnitId = "{AdUnitID}";
        adControl.Height = 90;
        adControl.Width = 728;
        adControl.IsAutoRefreshEnabled = false;

        ContentPanel.Children.Add(adControl);

        var timer = new DispatcherTimer() { Interval = TimeSpan.FromSeconds(60) };
        timer.Tick += (s, e) => adControl.Refresh();
        timer.Start();
    }
    ```

3.  Элемент **AdControl** работает нормально. Иногда одна и та же реклама может отображаться несколько раз подряд, в результате чего может показаться, что рекламные блоки не обновляются.

 

 



<!--HONumber=Jun16_HO4-->


