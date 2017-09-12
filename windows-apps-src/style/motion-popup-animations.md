---
author: mijacobs
Description: "Используйте анимацию всплывающих элементов, чтобы отобразить или скрыть всплывающий пользовательский интерфейс для всплывающих элементов или для настраиваемых всплывающих элементов пользовательского интерфейса. Всплывающие элементы представляют собой контейнеры, появляющиеся над содержимым приложения и закрывающиеся, если пользователь нажимает на область вне всплывающего элемента или щелкает ее."
title: "Анимации всплывающих элементов пользовательского интерфейса в приложениях UWP"
ms.assetid: 4E9025CE-FC90-4d4c-9DE6-EC6B6F2AD9DF
label: Motion--Pop-up animations
template: detail.hbs
ms.author: mijacobs
ms.date: 05/19/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows10, uwp
ms.openlocfilehash: c91e5cd3d4bad1b29d070f4750beb3dd95b3c5dc
ms.sourcegitcommit: 10d6736a0827fe813c3c6e8d26d67b20ff110f6c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/22/2017
---
# <a name="pop-up-ui-animations"></a>Анимации всплывающих элементов пользовательского интерфейса

<link rel="stylesheet" href="https://az835927.vo.msecnd.net/sites/uwp/Resources/css/custom.css">

Используйте анимацию всплывающих элементов, чтобы отобразить или скрыть всплывающий пользовательский интерфейс для всплывающих элементов или для настраиваемых всплывающих элементов пользовательского интерфейса. Всплывающие элементы представляют собой контейнеры, появляющиеся над содержимым приложения и закрывающиеся, если пользователь нажимает на область вне всплывающего элемента или щелкает ее.

<div class="important-apis" >
<b>Важные API</b><br/>
<ul>
<li>[**Класс PopInThemeAnimation**](https://msdn.microsoft.com/library/windows/apps/br210383)</li>
<li>[**Класс PopupThemeTransition**](https://msdn.microsoft.com/library/windows/apps/hh969172)</li>
</ul>
</div>


## <a name="dos-and-donts"></a>Возможности и ограничения


-   Используйте анимации всплывающих элементов для отображения или скрытия настраиваемых всплывающих элементов пользовательского интерфейса, не включенных в саму страницу приложения. В стандартные элементы управления, предоставляемые Windows, уже встроены анимации.
-   Не используйте анимации всплывающих элементов для всплывающих подсказок или диалоговых окон.
-   Не используйте анимации всплывающих элементов для отображения или скрытия пользовательского интерфейса в основном содержимом вашего приложения; используйте только анимации всплывающих элементов для отображения или скрытия всплывающего контейнера, который отображается поверх основного содержимого приложения.

## <a name="related-articles"></a>Связанные разделы

* [Обзор анимаций](https://msdn.microsoft.com/library/windows/apps/mt187350)
* [Анимация всплывающего пользовательского интерфейса](https://msdn.microsoft.com/library/windows/apps/xaml/jj649433)
* [Краткое руководство: анимация пользовательского интерфейса с помощью анимаций библиотеки](https://msdn.microsoft.com/library/windows/apps/xaml/hh452703)
* [**Класс PopInThemeAnimation**](https://msdn.microsoft.com/library/windows/apps/br210383)
* [**Класс PopOutThemeAnimation**](https://msdn.microsoft.com/library/windows/apps/br210391)
* [**Класс PopupThemeTransition**](https://msdn.microsoft.com/library/windows/apps/hh969172)

 

 




