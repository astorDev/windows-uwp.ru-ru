---
author: Xansky
ms.assetid: 08b4ae43-69e8-4424-b3c0-a07c93d275c3
description: Узнайте, как перехватывать ошибки AdControl в приложении.
title: Пошаговое руководство по обработке ошибок в JavaScript
ms.author: mhopkins
ms.date: 05/11/2018
ms.topic: article
keywords: windows 10, uwp, рекламные объявления, реклама, обработка ошибок, javascript
ms.localizationpriority: medium
ms.openlocfilehash: 65887bba125d8d7db1b224c1842da8c9ab34f117
ms.sourcegitcommit: 38f06f1714334273d865935d9afb80efffe97a17
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/09/2018
ms.locfileid: "6191285"
---
# <a name="error-handling-in-javascript-walkthrough"></a>Пошаговое руководство по обработке ошибок в JavaScript

В этом руководстве рассматривается перехват связанных с рекламой ошибок в приложении на JavaScript. В этом руководстве используется элемент управления [AdControl](https://docs.microsoft.com/uwp/api/microsoft.advertising.winrt.ui.adcontrol) для отображения баннера, но изложенные в нем общие принципы также применимы к межстраничным и собственным объявлениям.

Эти примеры составлены с учетом того, что у вас есть приложение на JavaScript, которое содержит элемент управления **AdControl**. Пошаговые инструкции по добавлению элемента **AdControl** в приложение см. в разделе [AdControl в HTML 5 и JavaScript](adcontrol-in-html-5-and-javascript.md). Полный пример с проектом, демонстрирующим способы добавления баннерной рекламы в приложение на JavaScript и HTML, см. в разделе [примеров рекламы на GitHub](http://aka.ms/githubads).

1.  В файле default.html file добавьте значение для события **onErrorOccurred**, где свойство **data-win-options**для элемента **AdControl** определяется в **div**. Найдите следующий код в файле default.html.
    ``` HTML
    <div id="myAd" style="position: absolute; top: 53px; left: 0px; width: 300px; height: 250px; z-index: 1"
      data-win-control="MicrosoftNSJS.Advertising.AdControl"
      data-win-options="{applicationId: '3f83fe91-d6be-434d-a0ae-7351c5a997f1', adUnitId: 'test'}">
    </div>
    ```
    После атрибута **adUnitId** добавьте значение для события **onErrorOccurred**.
    ``` HTML
    <div id="myAd" style="position: absolute; top: 53px; left: 0px; width: 300px; height: 250px; z-index: 1"
      data-win-control="MicrosoftNSJS.Advertising.AdControl"
      data-win-options="{applicationId: '3f83fe91-d6be-434d-a0ae-7351c5a997f1', adUnitId: 'test', onErrorOccurred: errorLogger}">
  </div>
  ```

2.  Создайте **div**, который будет отображать текст, чтобы можно было просматривать создаваемые сообщения. Для этого добавьте следующий код для **myAd** после **div**.
    ``` HTML
    <div style="position:absolute; width:100%; height:130px; top:300px; left:0px">
        <b>Ad Events</b><br />
        <div id="adEvents" style="width:100%; height:110px; overflow:auto"></div>
    </div>
    ```

3.  Создайте элемент **AdControl**, который инициирует событие ошибки. Для всех объектов **AdControl** в приложении можно использовать только один ИД приложения, поэтому создание дополнительного элемента управления с другим ИД приложения вызовет ошибку во время выполнения. Для этого после предыдущих добавленных разделов **div** добавьте следующий код в основную часть страницы default.html.
    ``` HTML
    <!-- Because only one applicationId can be used, the following ad control will fire an error event. -->
    <div id="liveAd" style="position: absolute; top:500px; left:0px; width:480px; height:80px"
      data-win-control="MicrosoftNSJS.Advertising.AdControl"
      data-win-options="{applicationId: '00000000-0000-0000-0000-000000000000', adUnitId: 'test', onErrorOccurred: errorLogger }" >
    </div>
    ```

4.  В файле default.js проекта после функции инициализации по умолчанию будет добавлен обработчик событий для **errorLogger**. Прокрутите до конца файла и вставьте следующий код после последней точкой с запятой.
    ``` javascript
    WinJS.Utilities.markSupportedForProcessing(
    window.errorLogger = function (sender, evt) {
        adEvents.innerHTML = (new Date()).toLocaleTimeString() + ": " +
        sender.element.id + " error: " + evt.errorMessage + " error code: " +
        evt.errorCode + "<br>" + adEvents.innerHTML;
        console.log("errorhandler hit. \n");
    });
    ```

5.  Сборка и запуск файла. Вы увидите первоначальное рекламное объявление из созданного ранее примера приложения и текст под этим объявлением с описанием ошибки. Вы не увидите рекламное объявление с идентификатором **liveAd**.

## <a name="related-topics"></a>Связанные разделы

* [Примеры рекламы на GitHub](http://aka.ms/githubads)
