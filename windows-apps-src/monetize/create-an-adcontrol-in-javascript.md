---
author: mcleanbyron
ms.assetid: 48a1ef86-8514-4af8-9c93-81e869d36de7
description: "Узнайте, как программным способом создавать **AdControl** на JavaScript."
title: "Создание AdControl на Javascript"
ms.sourcegitcommit: cf695b5c20378f7bbadafb5b98cdd3327bcb0be6
ms.openlocfilehash: 481f9d785181ca197debdb807bb0b0c7b4168632


---

# Создание AdControl на Javascript


\[ Обновлено для приложений UWP в Windows 10. Статьи для Windows 8.x см. в [архиве](http://go.microsoft.com/fwlink/p/?linkid=619132) \]

В этом примере показано, как программным способом создавать [AdControl](https://msdn.microsoft.com/library/windows/apps/microsoft.advertising.winrt.ui.adcontrol.aspx) на JavaScript.

## HTML div для AdControl

**AdControl** требует наличия элемента **div** на html-странице, где будет показываться объявление. Ниже приводится код с примером такого элемента **div**.

``` syntax
<div id="myAd" style="position: absolute; top: 50px; left: 0px; width: 300px; height: 250px; z-index: 1"
    data-win-control="MicrosoftNSJS.Advertising.AdControl">
</div>
```

## JavaScript для создания AdControl

В следующем примере предполагается, что вы используете существующий элемент **div** в html-коде с идентификатором **myAd**.

Создайте экземпляр **AdControl** в функции **app.onactivated**.

``` syntax
// TODO: This application has been newly launched. Initialize
// your application here.
var adDiv = document.getElementById("myAd");
var myAdControl = new MicrosoftNSJS.Advertising.AdControl(adDiv,
    {
        applicationId: "3f83fe91-d6be-434d-a0ae-7351c5a997f1",
        adUnitId: "10865270"
    });
myAdControl.isAutoRefreshEnabled = false;
myAdControl.onErrorOccurred = myAdError;
myAdControl.onAdRefreshed = myAdRefreshed;
myAdControl.onEngagedChanged = myAdEngagedChanged;
```

Эти значения являются примерами. В своем коде вы зададите значения для этих функций и свойств, подходящие для вашего приложения.

Если вы используете этот код и не видите рекламу, попробуйте задать атрибут **position:relative** в элементе **div**, содержащем **AdControl**. Это переопределяет параметр по умолчанию для **IFrame**. Объявления будут отображаться правильно, если их отображение не отключено значением этого атрибута. Обратите внимание, что новые рекламные блоки, могут быть недоступны до 30 минут.

## Ссылки по теме

* [Примеры рекламы на GitHub](http://aka.ms/githubads)

 

 



<!--HONumber=Jun16_HO4-->


