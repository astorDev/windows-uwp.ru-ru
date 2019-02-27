---
Description: Use pointer animations to provide users with visual feedback when the user taps on an item.
title: Анимации щелчков указателя в приложениях UWP
ms.assetid: EEB10A2C-629A-4705-8468-4D019D74DDFF
ms.date: 08/09/2017
ms.topic: article
keywords: Windows10, UWP
ms.localizationpriority: medium
ms.openlocfilehash: f1abd71cda8358e3f7e2fe36091f9c42f05bcb00
ms.sourcegitcommit: 079801609165bc7eb69670d771a05bffe236d483
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/27/2019
ms.locfileid: "9116020"
---
# <a name="pointer-click-animations"></a>Анимации щелчков указателя



Используйте анимацию указателя, чтобы предоставить пользователям визуальный отклик на касание или щелчок элемента. Анимация нажатия указателя слегка уменьшает и наклоняет нажатый элемент и воспроизводится при первом касании элемента. Анимация поднятия указателя, которая возвращает элемент в исходное положение, воспроизводится, когда пользователь отпускает указатель.


> **Важные API-интерфейсы**: [**класс PointerUpThemeAnimation**](https://msdn.microsoft.com/library/windows/apps/hh969168), [**класс PointerDownThemeAnimation**](https://msdn.microsoft.com/library/windows/apps/hh969164)


## <a name="dos-and-donts"></a>Рекомендации

-   Если вы используете анимацию поднятия указателя, немедленно запустите анимацию после того, как пользователь отпустит указатель. Благодаря этому пользователь видит незамедлительную реакцию на свои действия, даже если ответ на действие, запускаемое касанием (например, переход на новую страницу), появляется медленнее.

## <a name="related-articles"></a>Еще по теме

* [Обзор анимаций](https://msdn.microsoft.com/library/windows/apps/mt187350)
* [Анимация щелчков указателя](https://msdn.microsoft.com/library/windows/apps/xaml/jj649432)
* [Краткое руководство: анимация пользовательского интерфейса с помощью анимаций библиотеки](https://msdn.microsoft.com/library/windows/apps/xaml/hh452703)
* [**Класс PointerUpThemeAnimation**](https://msdn.microsoft.com/library/windows/apps/hh969168)
* [**Класс PointerDownThemeAnimation**](https://msdn.microsoft.com/library/windows/apps/hh969164)

 

 




