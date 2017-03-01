---
author: mcleanbyron
ms.assetid: 48a1ef86-8514-4af8-9c93-81e869d36de7
description: "Узнайте, как программным способом создавать **AdControl** на JavaScript."
title: "Создание AdControl на Javascript"
ms.author: mcleans
ms.date: 02/08/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: "Windows 10, uwp, рекламы, реклама, AdControl, javascript"
translationtype: Human Translation
ms.sourcegitcommit: c6b64cff1bbebc8ba69bc6e03d34b69f85e798fc
ms.openlocfilehash: d2ceafb9781ca2b9cd640e65d9bb420f0bf37928
ms.lasthandoff: 02/07/2017

---

# <a name="create-an-adcontrol-in-javascript"></a>Создание AdControl на Javascript




В примерах в данной статье показано, как программным способом создать [AdControl](https://msdn.microsoft.com/library/windows/apps/microsoft.advertising.winrt.ui.adcontrol.aspx) на JavaScript. В этой статье предполагается, что вы уже добавили в свой проект ссылки, необходимые для использования **AdControl**. Дополнительные сведения, включая подробное пошаговое руководство по созданию и инициализации **AdControl** в разметке HTML вместо JavaScript, см. в разделе [AdControl в HTML 5 и JavaScript](adcontrol-in-html-5-and-javascript.md).

## <a name="html-div-for-an-adcontrol"></a>Элемент div разметки HTML для AdControl

**AdControl** требует наличия элемента **div** на html-странице, где будет показываться объявление. Ниже приводится код с примером такого элемента **div**.

> [!div class="tabbedCodeSnippets"]
``` html
<div id="myAd" style="position: absolute; top: 50px; left: 0px; width: 300px; height: 250px; z-index: 1"
    data-win-control="MicrosoftNSJS.Advertising.AdControl">
</div>
```

## <a name="javascript-for-creating-an-adcontrol"></a>JavaScript для создания AdControl

В следующем примере предполагается, что вы используете существующий элемент **div** в html-коде с идентификатором **myAd**.

Создайте экземпляр **AdControl** в функции **app.onactivated**.

> [!div class="tabbedCodeSnippets"]
[!code-javascript[AdControl](./code/AdvertisingSamples/AdControlSamples/js/main.js#DeclareAdControl)]

В этом примере предполагается, что вы уже объявили методы обработчика событий с названиями **myAdError**, **myAdRefreshed** и **myAdEngagedChanged**.

>**Примечание.**&nbsp;&nbsp;Значения *applicationId* и *adUnitId*, приведенные в данном примере, являются [значениями тестового режима](test-mode-values.md). Перед отправкой приложения необходимо [заменить эти тестовые значения действительными значениями](set-up-ad-units-in-your-app.md) из Центра разработки для Windows.

Если вы используете этот код и не видите рекламу, попробуйте задать атрибут **position:relative** в элементе **div**, содержащем **AdControl**. Это переопределяет параметр по умолчанию для **IFrame**. Объявления будут отображаться правильно, если их отображение не отключено значением этого атрибута. Обратите внимание, что новые рекламные блоки, могут быть недоступны до 30 минут.

## <a name="related-topics"></a>Связанные разделы

* [Примеры рекламы на GitHub](http://aka.ms/githubads)

 

 

