---
author: Xansky
ms.assetid: 7a61c328-77be-4614-b117-a32a592c9efe
description: Сведения о решениях распространенных проблем разработки, связанными с библиотеками Microsoft Advertising в приложениях JavaScript и HTML.
title: Руководство по устранению неполадок для HTML и JavaScript
ms.author: mhopkins
ms.date: 08/23/2017
ms.topic: article
keywords: windows 10, uwp, рекламные объявления, реклама, AdControl, устранение неполадок, HTML, javascript
ms.localizationpriority: medium
ms.openlocfilehash: 38f0b36769d13d119965e7d15c5812b9ba1d6ecd
ms.sourcegitcommit: bdc40b08cbcd46fc379feeda3c63204290e055af
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/08/2018
ms.locfileid: "6155063"
---
# <a name="html-and-javascript-troubleshooting-guide"></a>Руководство по устранению неполадок для HTML и JavaScript

В этом разделе содержится описание решений для распространенных проблем разработки, связанных с библиотеками Microsoft Advertising в приложениях JavaScript и HTML.

* [HTML](#html)
  * [Элемент AdControl не отображается](#html-notappearing)
  * [«Черный ящик» моргает и исчезает](#html-blackboxblinksdisappears)
  * [Реклама не обновляется](#html-adsnotrefreshing)

* [JavaScript](#js)
  * [Элемент AdControl не отображается](#js-adcontrolnotappearing)
  * [«Черный ящик» моргает и исчезает](#js-blackboxblinksdisappears)
  * [Реклама не обновляется](#js-adsnotrefreshing)

## <a name="html"></a>HTML

<span id="html-notappearing"/>

### <a name="adcontrol-not-appearing"></a>Элемент AdControl не отображается

1.  Убедитесь, что в файле Package.appxmanifest выбрана возможность **Интернет (клиент)**.

2.  Убедитесь в наличии ссылки JavaScript. При отсутствии ссылки ad.js в разделе &lt;head&gt; (после ссылки default.js) элемент **AdControl** не будет отображаться и в ходе сборки произойдет ошибка.

    > [!div class="tabbedCodeSnippets"]
    ``` html
    <head>
        ...
        <script src="//Microsoft.Advertising.JavaScript/ad.js"></script>
        ...
    </head>
    ```

3.  Проверьте идентификаторы приложения и группы объявлений. Эти идентификаторы должны соответствовать Идентификатору приложения и идентификатор рекламного блока, полученным в центре партнеров. Дополнительные сведения см. в разделе [Настройка блоков рекламы в приложении](set-up-ad-units-in-your-app.md#live-ad-units).

    > [!div class="tabbedCodeSnippets"]
    ``` html
    <div id="myAd" style="position: absolute; top: 50px; left: 0px;
                          width: 250px; height: 250px; z-index: 1"
         data-win-control="MicrosoftNSJS.Advertising.AdControl"
         data-win-options="{applicationId: 'ApplicationID',
                            adUnitId: 'AdUnitID'}">
    </div>
    ```

4.  Проверьте свойства **высоты** и **ширины**. Им должен быть присвоен один из [размеров, поддерживаемых рекламными блоками для рекламных баннеров](supported-ad-sizes-for-banner-ads.md).

    > [!div class="tabbedCodeSnippets"]
    ``` html
    <div id="myAd" style="position: absolute; top: 50px; left: 0px;
                          width: 250px; height: 250px; z-index: 1"
         data-win-control="MicrosoftNSJS.Advertising.AdControl"
         data-win-options="{applicationId: 'ApplicationID',
                            adUnitId: 'AdUnitID'}">
    </div>
    ```

5.  Проверьте расположение элемента. Элемент [AdControl](https://docs.microsoft.com/uwp/api/microsoft.advertising.winrt.ui.adcontrol) должен находиться в пределах видимой области.

6.  Проверьте свойство **visibility**. Это свойство не должно быть свернуто или скрыто. Это свойство может быть задано в одной строке (как показано ниже) или во внешней таблице стилей.

    > [!div class="tabbedCodeSnippets"]
    ``` html
    <div id="myAd" style="visibility: visible; position: absolute; top: 1025px;
                          left: 500px; width: 250px; height: 250px; z-index: 1"
         data-win-control="MicrosoftNSJS.Advertising.AdControl"
         data-win-options="{applicationId: 'ApplicationID',
                            adUnitId: 'AdUnitID'}">
    </div>
    ```

7.  Проверьте свойство **position**. Свойству "position" необходимо задать соответствующее значение в зависимости от других свойств элемента (например, свойства "margins" в родительском элементе и свойства "z-index"). Это свойство может быть задано в одной строке (как показано ниже) или во внешней таблице стилей.

    > [!div class="tabbedCodeSnippets"]
    ``` html
    <div id="myAd" style="visibility: visible; position: absolute; top: 1025px;
                          left: 500px; width: 250px; height: 250px; z-index: 1"
         data-win-control="MicrosoftNSJS.Advertising.AdControl"
         data-win-options="{applicationId: 'ApplicationID',
                            adUnitId: 'AdUnitID'}">
    </div>
    ```

8.  Проверьте свойство **z-index**. Свойству **z-index** должно быть задано достаточно высокое значение, чтобы элемент **AdControl** всегда отображался поверх других элементов. Это свойство может быть задано в одной строке (как показано ниже) или во внешней таблице стилей.

    > [!div class="tabbedCodeSnippets"]
    ``` html
    <div id="myAd" style="visibility: visible; position: absolute; top: 1025px;
                          left: 500px; width: 250px; height: 250px; z-index: 1"
         data-win-control="MicrosoftNSJS.Advertising.AdControl"
         data-win-options="{applicationId: 'ApplicationID',
                            adUnitId: 'AdUnitID'}">
    </div>
    ```

9.  Проверьте внешние таблицы стилей. При задании свойств элемента **AdControl** с помощью внешней таблицы стилей, убедитесь, что все указанные выше свойства установлены правильно.

    > [!div class="tabbedCodeSnippets"]
    ``` html
    <div id="myAd" style="visibility: visible; position: absolute; top: 1025px;
                          left: 500px; width: 250px; height: 250px; z-index: 1"
         data-win-control="MicrosoftNSJS.Advertising.AdControl"
         data-win-options="{applicationId: 'ApplicationID',
                            adUnitId: 'AdUnitID'}">
    </div>
    ```

10. Проверьте родительский элемент элемента **AdControl**. Если элемент **AdControl** помещен в родительский элемент, то родительский элемент должен быть активным и видимым.

    > [!div class="tabbedCodeSnippets"]
    ``` html
    <div style="position: absolute; width: 500px; height: 500px;">
        <div id="myAd" style="position: relative; top: 0px; left: 100px;
                              width: 250px; height: 250px; z-index: 1"
             data-win-control="MicrosoftNSJS.Advertising.AdControl"
             data-win-options="{applicationId: 'ApplicationID',
                                adUnitId: 'AdUnitID'}">
        </div>
    </div>
    ```

11. Убедитесь, что элемент **AdControl** отображается в окне просмотра. Для правильного отображения элемента **AdControl** он должен быть видимым для рекламы.

12. Динамические значения [ApplicationId](https://docs.microsoft.com/uwp/api/microsoft.advertising.winrt.ui.adcontrol.applicationid) и [AdUnitId](https://docs.microsoft.com/uwp/api/microsoft.advertising.winrt.ui.adcontrol.adunitid) не следует проверять в эмуляторе. Чтобы проверить, правильно ли работает элемент **AdControl**, используйте [тестовые значения](set-up-ad-units-in-your-app.md#test-ad-units) для **ApplicationId** и **AdUnitId**.

<span id="html-blackboxblinksdisappears"/>

### <a name="black-box-blinks-and-disappears"></a>«Черный ящик» моргает и исчезает

1.  Тщательно проверьте все действия, выполненные в предыдущем разделе [Элемент AdControl не отображается](#html-notappearing).

2.  Обработайте событие **onErrorOccurred** и используйте сообщение, которое передается в обработчик событий, чтобы определить, произошла ли ошибка и какой тип ошибки будет игнорироваться. Дополнительные сведения см. в разделе [Обработка ошибок в пошаговом руководстве по JavaScript](error-handling-in-javascript-walkthrough.md).

    > [!div class="tabbedCodeSnippets"]
    ``` html
    <div id="myAd" style="position: absolute; top: 0px; left: 0px;
                          width: 728px; height: 90px; z-index: 1"
         data-win-control="MicrosoftNSJS.Advertising.AdControl"
         data-win-options="{applicationId: 'ApplicationID',
                            adUnitId: 'AdUnitID',
                            onErrorOccurred: errorLogger}">
    </div>
    <div style="position:absolute; width:100%; height:130px; top:300px; left:0px">
        <b>Ad Events</b><br />
        <div id="adEvents" style="width:100%; height:110px; overflow:auto"></div>
    </div>
    ```

    Наиболее распространенная ошибка, в следствие которой появляется "черный ящик", это— "Реклама недоступна". Эта ошибка означает, что для возврата по запросу реклама недоступна.

3.  Элемент **AdControl** работает нормально. По умолчанию **AdControl** сворачивается, если он не может отобразить рекламу. Если другие элементы являются дочерними по отношению к одному и тому же родительскому элементу, их можно использовать для заполнения пустого пространства, образовавшегося в результате сворачивания элемента **AdControl**, и они будут развернуты при отправке следующего запроса.

<span id="html-adsnotrefreshing"/>

### <a name="ads-not-refreshing"></a>Реклама не обновляется

1.  Проверьте свойство **isAutoRefreshEnabled**. По умолчанию этому необязательному свойству задано значение "true". Если установлено значение "false", для извлечения другой рекламы необходимо использовать метод **refresh**.

    > [!div class="tabbedCodeSnippets"]
    ``` html
    <div id="myAd" style="position: absolute; top: 0px; left: 0px;
                          width: 250px; height: 250px; z-index: 1"
         data-win-control="MicrosoftNSJS.Advertising.AdControl"
         data-win-options="{ applicationId: 'ApplicationID',
                            adUnitId: 'AdUnitID',
                            onErrorOccurred: errorLogger,
                            isAutoRefreshEnabled: true}">
    </div>
    ```

2.  Проверьте работоспособность вызовов метода **refresh**. При использовании автоматического обновления применять метод **refresh** для извлечения другой рекламы нельзя. При обновлении вручную метод **refresh** следует вызывать только по истечении не менее 30–60 секунд в зависимости от текущего типа подключения для передачи данных, настроенного на устройстве.

    В этом примере демонстрируется использование метода **refresh**. В следующем HTML-коде приведен пример создания экземпляра элемента **AdControl**, когда свойству **isAutoRefreshEnabled** задано значение "false".

    > [!div class="tabbedCodeSnippets"]
    ``` html
    <div id="myAd" style="position: absolute; top: 0px; left: 0px;
                          width: 250px; height: 250px; z-index: 1"
         data-win-control="MicrosoftNSJS.Advertising.AdControl"
         data-win-options="{ applicationId: 'ApplicationID',
                            adUnitId: 'AdUnitID',
                            onErrorOccurred: errorLogger,
                            isAutoRefreshEnabled: false}">
    </div>
    ```

    В этом примере демонстрируется использование функции **refresh**.

    > [!div class="tabbedCodeSnippets"]
    ``` javascript
    args.setPromise(WinJS.UI.processAll()
        .then(function (args) {
            window.setInterval(function()
            {
                document.getElementById("myAd").winControl.refresh();
            }, 60000)
        })
    );
    ```

3.  Элемент **AdControl** работает нормально. Иногда одна и та же реклама может отображаться несколько раз подряд в результате чего может показаться, что рекламные блоки не обновляются.

<span id="js"/>

## <a name="javascript"></a>JavaScript

<span id="js-adcontrolnotappearing"/>

### <a name="adcontrol-not-appearing"></a>Элемент AdControl не отображается

1.  Убедитесь, что в файле Package.appxmanifest выбрана возможность **Интернет (клиент)**.

2.  Убедитесь, что создан экземпляр элемента **AdControl**. Если экземпляр элемента **AdControl** не создан, он будет недоступен.

    Следующие фрагменты содержат пример создания экземпляра элемента **AdControl**. Этот HTML-код показывает пример настройки пользовательского интерфейса для элемента **AdControl**

    > [!div class="tabbedCodeSnippets"]
    ``` html
    <div id="myAd" style="position: absolute; top: 0px; left: 0px;
                          width: 250px; height: 250px; z-index: 1"
         data-win-control="MicrosoftNSJS.Advertising.AdControl">
    </div>
    ```

    В следующем коде JavaScript демонстрируется пример создания экземпляра элемента **AdControl**

    > [!div class="tabbedCodeSnippets"]
    ``` javascript
    app.onactivated = function (args) {
        if (args.detail.kind === activation.ActivationKind.launch) {
            if (args.detail.previousExecutionState !==
                    activation.ApplicationExecutionState.terminated)
            {
                var adDiv = document.getElementById("myAd");
                var myAdControl = new MicrosoftNSJS.Advertising.AdControl(adDiv,
                {
                    applicationId: "{ApplicationID}",
                    adUnitId: "{AdUnitID}"
                 });                
                 myAdControl.onErrorOccurred = myAdError;
            } else {
                ...
            }
        }
    }
    ```

3.  Проверьте родительский элемент. Родительский класс **&lt;div&gt;** необходимо правильно назначить, он должен быть активным и видимым.

    > [!div class="tabbedCodeSnippets"]
    ``` javascript
    var adDiv = document.getElementById("myAd");
    var myAdControl = new MicrosoftNSJS.Advertising.AdControl(adDiv, {
        applicationId: "{ApplicationID}",
        adUnitId: "{AdUnitID}"
    });  
    ```

4.  Проверьте индикаторы приложения и рекламного блока. Эти идентификаторы должны соответствовать Идентификатору приложения и идентификатор рекламного блока, полученным в центре партнеров. Дополнительные сведения см. в разделе [Настройка блоков рекламы в приложении](set-up-ad-units-in-your-app.md#live-ad-units).

    > [!div class="tabbedCodeSnippets"]
    ``` javascript
    var myAdControl = new MicrosoftNSJS.Advertising.AdControl(adDiv, {
        applicationId: "{ApplicationID}",
        adUnitId: "{AdUnitID}"
    });  
    ```

5.  Проверьте родительский элемент элемента **AdControl**. Родительский элемент должен быть активным и видимым.

6.  Динамические значения **ApplicationId** и **AdUnitId** не следует проверять в эмуляторе. Чтобы проверить, правильно ли работает элемент **AdControl**, используйте [тестовые значения](set-up-ad-units-in-your-app.md#test-ad-units) для **ApplicationId** и **AdUnitId**.

<span id="js-blackboxblinksdisappears"/>

### <a name="black-box-blinks-and-disappears"></a>«Черный ящик» моргает и исчезает

1.  Тщательно проверьте все действия, выполненные в разделе [Элемент AdControl не отображается](#js-adcontrolnotappearing).

2.  Обработайте событие **onErrorOccurred** и используйте сообщение, которое передается в обработчик событий, чтобы определить, произошла ли ошибка и какой тип ошибки будет игнорироваться. Дополнительные сведения см. в разделе [Обработка ошибок в пошаговом руководстве по JavaScript](error-handling-in-javascript-walkthrough.md).

    В этом примере показано описана процедура внедрения обработчика ошибок, передающего сообщения об ошибках. Этот фрагмент HTML-кода содержит пример настройки пользовательского интерфейса для отображения сообщений об ошибках.

    > [!div class="tabbedCodeSnippets"]
    ``` html
    <div style="position:absolute; width:100%; height:130px; top:300px">
        <b>Ad Events</b><br />
        <div id="adEvents" style="width:100%; height:110px; overflow:auto"></div>
    </div>
    ```

    В этом примере показано, как создать экземпляр элемента **AdControl**. Эта функция будет добавлена в файл app.onactivated.

    > [!div class="tabbedCodeSnippets"]
    ``` javascript
    var myAdControl = new MicrosoftNSJS.Advertising.AdControl(adDiv,
    {
        applicationId: "{ApplicationID}",
        adUnitId: "{AdUnitID}"
    });                
    myAdControl.onErrorOccurred = myAdError;
    ```

    Этот пример показывает, как сообщать об ошибках. Эта функция будет добавлена под самовыполняющуюся функцию в файле default.js.

    > [!div class="tabbedCodeSnippets"]
    ``` javascript
    WinJS.Utilities.markSupportedForProcessing
    (
        window.errorLogger = function (sender, evt)
        {
            adEvents.innerHTML = (new Date()).toLocaleTimeString() + ": " +
            sender.element.id + " error: " + evt.errorMessage + " error code: " +
            evt.errorCode + "<br>" + adEvents.innerHTML;
        }
    );
    ```

    Наиболее распространенная ошибка, в следствие которой появляется "черный ящик", это— "Реклама недоступна". Эта ошибка означает, что для возврата по запросу реклама недоступна.

3.  Элемент **AdControl** работает нормально. Иногда одна и та же реклама может отображаться несколько раз подряд, в результате чего может показаться, что рекламные блоки не обновляются.

<span id="js-adsnotrefreshing"/>

### <a name="ads-not-refreshing"></a>Реклама не обновляется

1.  Проверьте, задано ли для свойства [IsAutoRefreshEnabled](https://docs.microsoft.com/uwp/api/microsoft.advertising.winrt.ui.adcontrol.isautorefreshenabled.aspx) вашего элемента **AdControl** значение false. По умолчанию этому необязательному свойство задано значение **true**. Если установлено значение **false**, для извлечения другой рекламы необходимо использовать метод **Refresh**.

2.  Проверьте работоспособность вызовов метода [Refresh](https://docs.microsoft.com/uwp/api/microsoft.advertising.winrt.ui.adcontrol.refresh.aspx). При использовании автоматического обновления (для свойства **IsAutoRefreshEnabled** задано значение **true**) применять метод **Refresh** для извлечения другой рекламы нельзя. При обновлении вручную (значением **IsAutoRefreshEnabled** является **false**) метод **Refresh** следует вызывать только по истечении не менее 30–60 секунд в зависимости от текущего типа подключения для передачи данных, настроенного на устройстве.

    В этом примере показано, как создать класс **div** для элемента **AdControl**.

    > [!div class="tabbedCodeSnippets"]
    ``` html
    <div id="myAd" style="position: absolute; top: 0px; left: 0px;
                          width: 250px; height: 250px; z-index: 1"
         data-win-control="MicrosoftNSJS.Advertising.AdControl">
    </div>
    ```

    В этом примере продемонстрировано использование функции **Refresh**

    > [!div class="tabbedCodeSnippets"]
    ``` javascript
    var myAdControl = new MicrosoftNSJS.Advertising.AdControl(adDiv,
    {
      applicationId: "{ApplicationID}",
      adUnitId: "{AdUnitID}",
      isAutoRefreshEnabled: false
    });
    ...
    args.setPromise(WinJS.UI.processAll()
        .then(function (args) {
            window.setInterval(function()
            {
                document.getElementById("myAd").winControl.refresh();
            }, 60000)
        })
    );
    ```

3.  Элемент **AdControl** работает нормально. Иногда одна и та же реклама может отображаться несколько раз подряд, в результате чего может показаться, что рекламные блоки не обновляются.

 

 
