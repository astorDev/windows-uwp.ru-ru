---
author: mcleanbyron
ms.assetid: 08b4ae43-69e8-4424-b3c0-a07c93d275c3
description: "Узнайте, как перехватывать ошибки AdControl в приложении."
title: "Пошаговое руководство по обработке ошибок в JavaScript"
translationtype: Human Translation
ms.sourcegitcommit: cf695b5c20378f7bbadafb5b98cdd3327bcb0be6
ms.openlocfilehash: d26a8efeb253c6c793d8edd21d7452bbf15da261


---

# Пошаговое руководство по обработке ошибок в JavaScript


\[ Обновлено для приложений UWP в Windows 10. Статьи о Windows 8.x см. в [архиве](http://go.microsoft.com/fwlink/p/?linkid=619132) \]

В этом разделе рассматривается перехват ошибок [AdControl](https://msdn.microsoft.com/library/windows/apps/microsoft.advertising.winrt.ui.adcontrol.aspx) в приложении.

Эти примеры составлены с учетом того, что у вас есть приложение JavaScript/HTML, которое содержит элемент управления **AdControl**. Пошаговые инструкции по добавлению элемента **AdControl** в приложение см. в разделе [AdControl в HTML 5 и JavaScript](adcontrol-in-html-5-and-javascript.md). Полный пример с проектом, демонстрирующим способы добавления баннерной рекламы в приложение на JavaScript и HTML, см. в разделе [примеров рекламы на GitHub](http://aka.ms/githubads).

1.  В файле default.html file добавьте значение для события **onErrorOccurred**, где свойство **data-win-options**для элемента **AdControl** определяется в **div**. Найдите следующий код в файле default.html.

    ``` syntax
    <div id="myAd" style="position: absolute; top: 53px; left: 0px; width: 300px; height: 250px; z-index: 1"
         data-win-control="MicrosoftNSJS.Advertising.AdControl"
         data-win-options="{applicationId: '3f83fe91-d6be-434d-a0ae-7351c5a997f1', adUnitId: '10865270'}">
    </div>
    ```

    После **adUnitId** добавьте значение для события **onErrorOccurred**.

    ``` syntax
    onErrorOccurred: errorLogger
    ```

    Ниже приведен полный код для **div**.

    ``` syntax
    <div id="myAd" style="position: absolute; top: 53px; left: 0px; width: 300px; height: 250px; z-index: 1"
         data-win-control="MicrosoftNSJS.Advertising.AdControl"
         data-win-options="{applicationId: '3f83fe91-d6be-434d-a0ae-7351c5a997f1', adUnitId: '10865270', onErrorOccurred: errorLogger}">
    </div>
    ```

2.  Создайте **div**, который будет отображать текст, чтобы можно было просматривать создаваемые сообщения. Для этого добавьте следующий код для **myAd** после **div**.

    ``` syntax
    <div style="position:absolute; width:100%; height:130px; top:300px; left:0px">
        <b>Ad Events</b><br />
        <div id="adEvents" style="width:100%; height:110px; overflow:auto"></div>
    </div>
    ```

3.  Создайте элемент **AdControl**, который инициирует событие ошибки. Для всех объектов **AdControl** в приложении можно использовать только один ИД приложения, поэтому создание дополнительного элемента управления с другим ИД приложения вызовет ошибку во время выполнения. Для этого после предыдущих добавленных разделов **div** добавьте следующий код в основную часть страницы default.html.

    ``` syntax
    <!-- since only one applicationId can be used, the following ad control will fire an error event -->
    <div id="liveAd" style="position: absolute; top:500px; left:0px; width:480px; height:80px"
        data-win-control="MicrosoftNSJS.Advertising.AdControl"
        data-win-options="{applicationId: '00000000-0000-0000-0000-000000000000',
        adUnitId: '10865270', onErrorOccurred: errorLogger }" >
    </div>
    ```

4.  В файле default.js проекта после функции инициализации по умолчанию будет добавлен обработчик событий для **errorLogger**. Прокрутите до конца файла и вставьте следующий код после последней точкой с запятой.

    ``` syntax
    WinJS.Utilities.markSupportedForProcessing(
    window.errorLogger = function (sender, evt) {
        adEvents.innerHTML = (new Date()).toLocaleTimeString() + ": " +
        sender.element.id + " error: " + evt.errorMessage + " error code: " +
        evt.errorCode + "<br>" + adEvents.innerHTML;
        console.log("errorhandler hit. \n");
    });
    ```

5.  Сборка и запуск файла.

Вы увидите первоначальное рекламное объявление из созданного ранее примера приложения и текст под этим объявлением с описанием ошибки. Вы не увидите рекламное объявление с идентификатором **liveAd**.

## Связанные разделы

* [Примеры рекламы на GitHub](http://aka.ms/githubads)

 



<!--HONumber=Jun16_HO4-->


