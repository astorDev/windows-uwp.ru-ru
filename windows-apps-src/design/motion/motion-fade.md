---
Description: Анимация угасания используется для ввода элементов в поле зрения и вывода из него. Две распространенные анимации такого типа — появление и исчезание.
title: Анимации исчезания
ms.assetid: 975E5EE3-EFBE-4159-8D10-3C94143DD07F
label: Motion--fades
template: detail.hbs
ms.date: 05/19/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 6b937ba4174d01096a09a98f7efd4e7e3dce9632
ms.sourcegitcommit: 0dee502484df798a0595ac1fe7fb7d0f5a982821
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/08/2020
ms.locfileid: "82970309"
---
# <a name="fade-animations"></a>Анимации исчезания



Анимация угасания используется для ввода элементов в поле зрения и вывода из него. Две распространенные анимации такого типа — появление и исчезание.

> **Важные API-интерфейсы**: [**класс FadeInThemeAnimation **](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.Animation.FadeInThemeAnimation), [**класс FadeOutThemeAnimation**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.Animation.FadeOutThemeAnimation)


## <a name="dos-and-donts"></a>Что рекомендуется и что не рекомендуется делать


-   При переходе приложения между несвязанными элементами или элементами, содержащими большой объем текста, используйте исчезание с последующим появлением. Это позволит исходящему объекту полностью исчезнуть, прежде чем входящий объект станет видимым.
-   Отобразите входящий элемент или элементы поверх исходящих элементов, если размер элементов остается постоянным и требуется сделать так, чтобы пользователь думал, что смотрит на один и тот же элемент. После завершения прорисовки входящего элемента исходящий элемент можно удалить. Этот вариант работает, только если исходящий элемент полностью закрывается входящим элементом.
-   Избегайте анимации исчезания для добавления или удаления элементов в списке. Вместо этого используйте анимации списка, созданные для этой цели.
-   Избегайте анимации исчезания для изменения всего содержимого страницы. Вместо этого используйте анимации перехода страницы, созданные для этой цели.
-   Исчезание — это тонкий способ удалить элемент.
## <a name="related-articles"></a>Похожие статьи

* [Обзор анимаций](https://docs.microsoft.com/windows/uwp/graphics/animations-overview)
* [Анимация появления или исчезания](https://docs.microsoft.com/previous-versions/windows/apps/jj649429(v=win.10))
* [Краткое руководство: анимация пользовательского интерфейса с помощью анимаций библиотеки](https://docs.microsoft.com/previous-versions/windows/apps/hh452703(v=win.10))
* [**Класс FadeInThemeAnimation**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.Animation.FadeInThemeAnimation)
* [**Класс FadeOutThemeAnimation**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.Animation.FadeOutThemeAnimation)

 

 




