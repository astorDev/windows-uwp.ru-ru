---
author: mijacobs
Description: Use pop-up animations to show and hide pop-up UI for flyouts or custom pop-up UI elements. Pop-up elements are containers that appear over the app's content and are dismissed if the user taps or clicks outside of the pop-up element.
title: Анимации всплывающих элементов пользовательского интерфейса в приложениях UWP
ms.assetid: 4E9025CE-FC90-4d4c-9DE6-EC6B6F2AD9DF
label: Motion--Pop-up animations
template: detail.hbs
ms.author: mijacobs
ms.date: 05/19/2017
ms.topic: article
keywords: windows10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 1a304df30986c904f19cc2401c9a1fb468514f6f
ms.sourcegitcommit: 4d88adfaf544a3dab05f4660e2f59bbe60311c00
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/12/2018
ms.locfileid: "6462770"
---
# <a name="pop-up-ui-animations"></a>Анимации всплывающих элементов пользовательского интерфейса



Используйте анимацию всплывающих элементов, чтобы отобразить или скрыть всплывающий пользовательский интерфейс для всплывающих элементов или для настраиваемых всплывающих элементов пользовательского интерфейса. Всплывающие элементы представляют собой контейнеры, появляющиеся над содержимым приложения и закрывающиеся, если пользователь нажимает на область вне всплывающего элемента или щелкает ее.

> **Важные API-интерфейсы**: [**класс PopInThemeAnimation**](https://msdn.microsoft.com/library/windows/apps/br210383), [**класс PopupThemeTransition**](https://msdn.microsoft.com/library/windows/apps/hh969172)


## <a name="dos-and-donts"></a>Рекомендации


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

 

 




