---
Description: Используйте анимацию указателя, чтобы предоставить пользователям визуальный отклик на касание или щелчок элемента.
title: Анимации щелчков указателя
ms.assetid: EEB10A2C-629A-4705-8468-4D019D74DDFF
ms.date: 08/09/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 32c3460b1af226adfa969d02a695f1d6436390be
ms.sourcegitcommit: 0dee502484df798a0595ac1fe7fb7d0f5a982821
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/08/2020
ms.locfileid: "82970549"
---
# <a name="pointer-click-animations"></a>Анимации щелчков указателя



Используйте анимацию указателя, чтобы предоставить пользователям визуальный отклик на касание или щелчок элемента. Анимация нажатия указателя слегка уменьшает и наклоняет нажатый элемент и воспроизводится при первом касании элемента. Анимация поднятия указателя, которая возвращает элемент в исходное положение, воспроизводится, когда пользователь отпускает указатель.


> **Важные API-интерфейсы**: [**класс PointerUpThemeAnimation**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.Animation.PointerUpThemeAnimation), [**класс PointerDownThemeAnimation**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.Animation.PointerDownThemeAnimation)


## <a name="dos-and-donts"></a>Что рекомендуется и что не рекомендуется делать

-   Если вы используете анимацию поднятия указателя, немедленно запустите анимацию после того, как пользователь отпустит указатель. Благодаря этому пользователь видит незамедлительную реакцию на свои действия, даже если ответ на действие, запускаемое касанием (например, переход на новую страницу), появляется медленнее.

## <a name="related-articles"></a>Похожие статьи

* [Обзор анимаций](https://docs.microsoft.com/windows/uwp/graphics/animations-overview)
* [Анимация щелчков указателя](https://docs.microsoft.com/previous-versions/windows/apps/jj649432(v=win.10))
* [Краткое руководство: анимация пользовательского интерфейса с помощью анимаций библиотеки](https://docs.microsoft.com/previous-versions/windows/apps/hh452703(v=win.10))
* [**Класс PointerUpThemeAnimation**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.Animation.PointerUpThemeAnimation)
* [**Класс PointerDownThemeAnimation**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.Animation.PointerDownThemeAnimation)

 

 




