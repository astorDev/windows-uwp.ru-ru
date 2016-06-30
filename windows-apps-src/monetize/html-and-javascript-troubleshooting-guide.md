---
author: mcleanbyron
ms.assetid: 7a61c328-77be-4614-b117-a32a592c9efe
description: "Сведения о решениях распространенных проблем разработки, связанными с библиотеками Microsoft Advertising в приложениях JavaScript и HTML."
title: "Руководство по устранению неполадок для HTML и JavaScript"
translationtype: Human Translation
ms.sourcegitcommit: cf695b5c20378f7bbadafb5b98cdd3327bcb0be6
ms.openlocfilehash: af4ea6f3360ea85d1c70ec9b757db65ec23c88af


---

# Руководство по устранению неполадок для HTML и JavaScript


\[ Обновлено для приложений UWP в Windows 10. Статьи о Windows 8.x, см. в [архиве](http://go.microsoft.com/fwlink/p/?linkid=619132) \]

В этом разделе содержится описание решений распространенных проблем разработки, связанных с библиотеками Microsoft Advertising в приложениях JavaScript и HTML.

-   [HTML](#html)

    -   [Элемент AdControl не отображается](#html-notappearing)

    -   ["Черный ящик" моргает и исчезает](#html-blackboxblinksdisappears)

    -   [Реклама не обновляется](#html-adsnotrefreshing)

-   [JavaScript](#js)

    -   [Элемент AdControl не отображается](#js-adcontrolnotappearing)

    -   ["Черный ящик" моргает и исчезает](#js-blackboxblinksdisappears)

    -   [Реклама не обновляется](#js-adsnotrefreshing)

## HTML

<span id="html-notappearing"/>
### Элемент AdControl не отображается

1.  Убедитесь, что в файле Package.appxmanifest выбрана возможность **Интернет (клиент)**.

2.  Убедитесь в наличии ссылки JavaScript. При отсутствии ссылки ad.js в разделе &lt;head&gt; (после ссылки default.js) элемент **AdControl** не будет отображаться и в ходе сборки произойдет ошибка.

    Windows 10:

    ``` syntax
    <head>
        …
        <script src="//Microsoft.Advertising.JavaScript/ad.js"></script>
        …
    </head>
    ```

    Windows 8.x:

    ``` syntax
    <head>
        …
        <script src="//Microsoft.Advertising.JavaScript/ads/ad.js"></script>
        …
    </head>
    ```

3.  Проверьте индикаторы приложения и рекламного блока. Эти идентификаторы должны соответствовать идентификатору приложения и идентификатору рекламного блока, полученным в Центре разработки для Windows. Дополнительные сведения см. в разделе [Настройка блоков рекламы в приложении](set-up-ad-units-in-your-app.md).

    ``` syntax
    <div id="myAd" style="position: absolute; top: 50px; left: 0px;
                          width: 250px; height: 250px; z-index: 1"
         data-win-control="MicrosoftNSJS.Advertising.AdControl"
         data-win-options="{applicationId: 'ApplicationID',
                            adUnitId: 'AdUnitID'}">
    </div>
    ```

4.  Проверьте свойства **высоты** и **ширины**. Им должен быть присвоен один из [размеров, поддерживаемых рекламными блоками для рекламных баннеров](supported-ad-sizes-for-banner-ads.md).

    ``` syntax
    <div id="myAd" style="position: absolute; top: 50px; left: 0px;
                          width: 250px; height: 250px; z-index: 1"
         data-win-control="MicrosoftNSJS.Advertising.AdControl"
         data-win-options="{applicationId: 'ApplicationID',
                            adUnitId: 'AdUnitID'}">
    </div>
    ```

5.  Проверьте расположение элемента. Элемент [AdControl](https://msdn.microsoft.com/library/windows/apps/microsoft.advertising.winrt.ui.adcontrol.aspx) должен находиться в пределах видимой области.

6.  Проверьте свойство **visibility**. Это свойство не должно быть свернуто или скрыто. Это свойство может быть задано в одной строке (как показано ниже) или во внешней таблице стилей.

    ``` syntax
    <div id="myAd" style="visibility: visible; position: absolute; top: 1025px;
                          left: 500px; width: 250px; height: 250px; z-index: 1"
         data-win-control="MicrosoftNSJS.Advertising.AdControl"
         data-win-options="{applicationId: 'ApplicationID',
                            adUnitId: 'AdUnitID'}">
    </div>
    ```

7.  Проверьте свойство **position**. Свойству "position" необходимо задать соответствующее значение в зависимости от других свойств элемента (например, свойства "margins" в родительском элементе и свойства "z-index"). Это свойство может быть задано в одной строке (как показано ниже) или во внешней таблице стилей.

    ``` syntax
    <div id="myAd" style="visibility: visible; position: absolute; top: 1025px;
                          left: 500px; width: 250px; height: 250px; z-index: 1"
         data-win-control="MicrosoftNSJS.Advertising.AdControl"
         data-win-options="{applicationId: 'ApplicationID',
                            adUnitId: 'AdUnitID'}">
    </div>
    ```

8.  Проверьте свойство **z-index**. Свойству **z-index** должно быть задано достаточно высокое значение, чтобы элемент **AdControl** всегда отображался поверх других элементов. Это свойство может быть задано в одной строке (как показано ниже) или во внешней таблице стилей.

    ``` syntax
    <div id="myAd" style="visibility: visible; position: absolute; top: 1025px;
                          left: 500px; width: 250px; height: 250px; z-index: 1"
         data-win-control="MicrosoftNSJS.Advertising.AdControl"
         data-win-options="{applicationId: 'ApplicationID',
                            adUnitId: 'AdUnitID'}">
    </div>
    ```

9.  Проверьте внешние таблицы стилей. При задании свойств элемента **AdControl** с помощью внешней таблицы стилей, убедитесь, что все указанные выше свойства установлены правильно.

    ``` syntax
    <div id="myAd" style="visibility: visible; position: absolute; top: 1025px;
                          left: 500px; width: 250px; height: 250px; z-index: 1"
         data-win-control="MicrosoftNSJS.Advertising.AdControl"
         data-win-options="{applicationId: 'ApplicationID',
                            adUnitId: 'AdUnitID'}">
    </div>
    ```

10. Проверьте родительский элемент элемента **AdControl**. Если элемент **AdControl** помещен в родительский элемент, то родительский элемент должен быть активным и видимым.

    ``` syntax
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

12. Динамические значения [ApplicationId](https://msdn.microsoft.com/library/windows/apps/microsoft.advertising.winrt.ui.adcontrol.applicationid.aspx) и [AdUnitId](https://msdn.microsoft.com/library/windows/apps/microsoft.advertising.winrt.ui.adcontrol.adunitid.aspx) не следует проверять в эмуляторе. Чтобы проверить, правильно ли работает элемент **AdControl**, используйте проверочные идентификаторы для **ApplicationId** и **AdUnitId**, расположенные в разделе [Значения тестового режима](test-mode-values.md).

<span id="html-blackboxblinksdisappears"/>
### «Черный ящик» моргает и исчезает

1.  Тщательно проверьте все действия, выполненные в предыдущем разделе [Элемент AdControl не отображается](#html-notappearing).

2.  Обработайте событие **onErrorOccurred** и используйте сообщение, которое передается в обработчик событий, чтобы определить, произошла ли ошибка и какой тип ошибки будет игнорироваться. Дополнительные сведения см. в разделе [Обработка ошибок в пошаговом руководстве по JavaScript](error-handling-in-javascript-walkthrough.md).

    ``` syntax
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

    Наиболее распространенная ошибка, в следствие которой появляется "черный ящик", это — "Реклама недоступна". Эта ошибка означает, что для возврата по запросу реклама недоступна.

3.  Элемент **AdControl** работает нормально. По умолчанию **AdControl** сворачивается, если он не может отобразить рекламу. Если другие элементы являются дочерними по отношению к одному и тому же родительскому элементу, их можно использовать для заполнения пустого пространства, образовавшегося в результате сворачивания элемента **AdControl**, и они будут развернуты при отправке следующего запроса.

<span id="html-adsnotrefreshing"/>
### Реклама не обновляется

1.  Проверьте свойство **isAutoRefreshEnabled**. По умолчанию этому необязательному свойство задано значение "true". Если установлено значение "false", для извлечения другой рекламы необходимо использовать метод **refresh**.

    ``` syntax
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

    ``` syntax
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

    ``` syntax
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
## JavaScript

<span id="js-adcontrolnotappearing"/>
### Элемент AdControl не отображается

1.  Убедитесь, что в файле Package.appxmanifest выбрана возможность **Интернет (клиент)**.

2.  Убедитесь, что создан экземпляр элемента **AdControl**. Если экземпляр элемента **AdControl** не создан, он будет недоступен.

    Следующие фрагменты содержат пример создания экземпляра элемента **AdControl**. Этот HTML-код показывает пример настройки пользовательского интерфейса для элемента **AdControl**

    ``` syntax
    <div id="myAd" style="position: absolute; top: 0px; left: 0px;
                          width: 250px; height: 250px; z-index: 1"
         data-win-control="MicrosoftNSJS.Advertising.AdControl">
    </div>
    ```

    В следующем коде JavaScript демонстрируется пример создания экземпляра элемента **AdControl**

    ``` syntax
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
                …
            }
        }
    }
    ```

3.  Проверьте родительский элемент. Родительский класс **&lt;div&gt;** необходимо правильно назначить, он должен быть активным и видимым.

    ``` syntax
    var adDiv = document.getElementById("myAd");
    var myAdControl = new MicrosoftNSJS.Advertising.AdControl(adDiv, {
        applicationId: "{ApplicationID}",
        adUnitId: "{AdUnitID}"
    });  
    ```

4.  Проверьте индикаторы приложения и рекламного блока. Эти идентификаторы должны соответствовать идентификатору приложения и идентификатору рекламного блока, полученным в Центре разработки для Windows. Дополнительные сведения см. в разделе [Настройка блоков рекламы в приложении](set-up-ad-units-in-your-app.md).

    ``` syntax
    var myAdControl = new MicrosoftNSJS.Advertising.AdControl(adDiv, {
        applicationId: "{ApplicationID}",
        adUnitId: "{AdUnitID}"
    });  
    ```

5.  Проверьте родительский элемент элемента **AdControl**. Родительский элемент должен быть активным и видимым.

6.  Динамические значения **ApplicationId** и **AdUnitId** не следует проверять в эмуляторе. Чтобы проверить, правильно ли работает элемент **AdControl**, используйте проверочные идентификаторы для **ApplicationId** и **AdUnitId**, расположенные в разделе [Значения тестового режима](test-mode-values.md).

<span id="js-blackboxblinksdisappears"/>
### «Черный ящик» моргает и исчезает

1.  Тщательно проверьте все действия, выполненные в разделе [Элемент AdControl не отображается](#js-adcontrolnotappearing).

2.  Обработайте событие **onErrorOccurred** и используйте сообщение, которое передается в обработчик событий, чтобы определить, произошла ли ошибка и какой тип ошибки будет игнорироваться. Дополнительные сведения см. в разделе [Обработка ошибок в пошаговом руководстве по JavaScript](error-handling-in-javascript-walkthrough.md).

    В этом примере показано описана процедура внедрения обработчика ошибок, передающего сообщения об ошибках. Этот фрагмент HTML-кода содержит пример настройки пользовательского интерфейса для отображения сообщений об ошибках.

    ``` syntax
    <div style="position:absolute; width:100%; height:130px; top:300px">
        <b>Ad Events</b><br />
        <div id="adEvents" style="width:100%; height:110px; overflow:auto"></div>
    </div>
    ```

    В этом примере показано, как создать экземпляр элемента **AdControl**. Эта функция будет добавлена в файл app.onactivated.

    ``` syntax
    var myAdControl = new MicrosoftNSJS.Advertising.AdControl(adDiv,
    {
        applicationId: "{ApplicationID}",
        adUnitId: "{AdUnitID}"
    });                
    myAdControl.onErrorOccurred = myAdError;
    ```

    Этот пример показывает, как сообщать об ошибках. Эта функция будет добавлена под самовыполняющуюся функцию в файле default.js.

    ``` syntax
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

    Наиболее распространенная ошибка, в следствие которой появляется "черный ящик", это — "Реклама недоступна". Эта ошибка означает, что для возврата по запросу реклама недоступна.

3.  Элемент **AdControl** работает нормально. Иногда одна и та же реклама может отображаться несколько раз подряд в результате чего может показаться, что рекламные блоки не обновляются.

<span id="js-adsnotrefreshing"/>
### Реклама не обновляется

1.  Проверьте свойство **isAutoRefreshEnabled**. По умолчанию этому необязательному свойство задано значение **true**. Если установлено значение **false**, для извлечения другой рекламы необходимо использовать метод **refresh**.

    В этом примере показано, как использовать свойство **isAutoRefreshEnabled**.

    ``` syntax
    var myAdControl = new MicrosoftNSJS.Advertising.AdControl(adDiv,
    {
      applicationId: "{ApplicationID}",
      adUnitId: "{AdUnitID}",
      isAutoRefreshEnabled: true
    });  
    ```

2.  Проверьте работоспособность вызовов метода **refresh**. При использовании автоматического обновления применять метод **refresh** для извлечения другой рекламы нельзя. При обновлении вручную метод **refresh** следует вызывать только по истечении не менее 30–60 секунд в зависимости от текущего типа подключения для передачи данных, настроенного на устройстве.

    В этом примере показано, как создать класс **div** для элемента **AdControl**.

    ``` syntax
    <div id="myAd" style="position: absolute; top: 0px; left: 0px;
                          width: 250px; height: 250px; z-index: 1"
         data-win-control="MicrosoftNSJS.Advertising.AdControl">
    </div>
    ```

    В этом примере продемонстрировано использование функции **refresh**

    ``` syntax
    var myAdControl = new MicrosoftNSJS.Advertising.AdControl(adDiv,
    {
      applicationId: "{ApplicationID}",
      adUnitId: "{AdUnitID}",
      isAutoRefreshEnabled: false
    });
    …
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

 

 



<!--HONumber=Jun16_HO4-->


