---
Description: Анимация угасания используется для ввода элементов в поле зрения и вывода из него. Двумя распространенными анимациями исчезания являются плавное увеличение и плавное уменьшение.
title: Анимации исчезания в приложениях UWP
ms.assetid: 975E5EE3-EFBE-4159-8D10-3C94143DD07F
label: Motion--fades
template: detail.hbs
ms.date: 05/19/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 3d8642e911a3ad4275e0a7a0f147ca9d70f415b0
ms.sourcegitcommit: ac7f3422f8d83618f9b6b5615a37f8e5c115b3c4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/29/2019
ms.locfileid: "66366789"
---
# <a name="fade-animations"></a>Анимации исчезания



Анимация угасания используется для ввода элементов в поле зрения и вывода из него. Двумя распространенными анимациями исчезания являются плавное увеличение и плавное уменьшение.

> **Важные API**: [**Класс FadeInThemeAnimation**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.Animation.FadeInThemeAnimation), [ **FadeOutThemeAnimation-класс**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.Animation.FadeOutThemeAnimation)


## <a name="dos-and-donts"></a>Возможности и ограничения


-   При переходе приложения между несвязанными элементами или элементами, содержащими большой объем текста, используйте исчезание с последующим появлением. Это позволит исходящему объекту полностью исчезнуть, прежде чем входящий объект станет видимым.
-   Отобразите входящий элемент или элементы поверх исходящих элементов, если размер элементов остается постоянным и требуется сделать так, чтобы пользователь думал, что смотрит на один и тот же элемент. После завершения прорисовки входящего элемента исходящий элемент можно удалить. Этот вариант работает, только если исходящий элемент полностью закрывается входящим элементом.
-   Избегайте анимации исчезания для добавления или удаления элементов в списке. Вместо этого используйте анимации списка, созданные для этой цели.
-   Избегайте анимации исчезания для изменения всего содержимого страницы. Вместо этого используйте анимации перехода страницы, созданные для этой цели.
-   Исчезание — это тонкий способ удалить элемент.
## <a name="related-articles"></a>Связанные статьи

* [Общие сведения об анимации](https://docs.microsoft.com/windows/uwp/graphics/animations-overview)
* [Анимация затухание](https://docs.microsoft.com/previous-versions/windows/apps/jj649429(v=win.10))
* [Краткое руководство. Анимация пользовательского интерфейса с помощью библиотеки анимации](https://docs.microsoft.com/previous-versions/windows/apps/hh452703(v=win.10))
* [**Класс FadeInThemeAnimation**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.Animation.FadeInThemeAnimation)
* [**Класс FadeOutThemeAnimation**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.Animation.FadeOutThemeAnimation)

 

 




