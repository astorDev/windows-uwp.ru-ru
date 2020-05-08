---
Description: Используйте анимацию всплывающих элементов, чтобы отобразить или скрыть всплывающий пользовательский интерфейс для всплывающих элементов или для настраиваемых всплывающих элементов пользовательского интерфейса. Всплывающие элементы представляют собой контейнеры, появляющиеся над содержимым приложения и закрывающиеся, если пользователь нажимает на область вне всплывающего элемента или щелкает ее.
title: Анимации всплывающих элементов пользовательского интерфейса
ms.assetid: 4E9025CE-FC90-4d4c-9DE6-EC6B6F2AD9DF
label: Motion--Pop-up animations
template: detail.hbs
ms.date: 05/19/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 78adc32dccc66b98e413796d9cfe20b2a158261e
ms.sourcegitcommit: 0dee502484df798a0595ac1fe7fb7d0f5a982821
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/08/2020
ms.locfileid: "82970539"
---
# <a name="pop-up-ui-animations"></a>Анимации всплывающих элементов пользовательского интерфейса



Используйте анимацию всплывающих элементов, чтобы отобразить или скрыть всплывающий пользовательский интерфейс для всплывающих элементов или для настраиваемых всплывающих элементов пользовательского интерфейса. Всплывающие элементы представляют собой контейнеры, появляющиеся над содержимым приложения и закрывающиеся, если пользователь нажимает на область вне всплывающего элемента или щелкает ее.

> **Важные API-интерфейсы**: [**класс PopInThemeAnimation**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.Animation.PopInThemeAnimation), [**класс PopupThemeTransition**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.Animation.PopupThemeTransition)


## <a name="dos-and-donts"></a>Что рекомендуется и что не рекомендуется делать


-   Используйте анимации всплывающих элементов для отображения или скрытия настраиваемых всплывающих элементов пользовательского интерфейса, не включенных в саму страницу приложения. В стандартные элементы управления, предоставляемые Windows, уже встроены анимации.
-   Не используйте анимации всплывающих элементов для всплывающих подсказок или диалоговых окон.
-   Не используйте анимации всплывающих элементов для отображения или скрытия пользовательского интерфейса в основном содержимом вашего приложения; используйте только анимации всплывающих элементов для отображения или скрытия всплывающего контейнера, который отображается поверх основного содержимого приложения.

## <a name="related-articles"></a>Похожие статьи

* [Обзор анимаций](https://docs.microsoft.com/windows/uwp/graphics/animations-overview)
* [Анимация всплывающего пользовательского интерфейса](https://docs.microsoft.com/previous-versions/windows/apps/jj649433(v=win.10))
* [Краткое руководство: анимация пользовательского интерфейса с помощью анимаций библиотеки](https://docs.microsoft.com/previous-versions/windows/apps/hh452703(v=win.10))
* [**Класс PopInThemeAnimation**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.Animation.PopInThemeAnimation)
* [**Класс PopOutThemeAnimation**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.Animation.PopOutThemeAnimation)
* [**Класс PopupThemeTransition**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.Animation.PopupThemeTransition)

 

 




