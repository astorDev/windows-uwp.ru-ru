---
Description: Используйте анимацию указателя, чтобы предоставить пользователям визуальный отклик на касание или щелчок элемента.
title: Анимации щелчков указателя в приложениях UWP
ms.assetid: EEB10A2C-629A-4705-8468-4D019D74DDFF
ms.date: 08/09/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: f1abd71cda8358e3f7e2fe36091f9c42f05bcb00
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57663799"
---
# <a name="pointer-click-animations"></a>Анимации щелчков указателя



Используйте анимацию указателя, чтобы предоставить пользователям визуальный отклик на касание или щелчок элемента. Анимация нажатия указателя слегка уменьшает и наклоняет нажатый элемент и воспроизводится при первом касании элемента. Анимация поднятия указателя, которая возвращает элемент в исходное положение, воспроизводится, когда пользователь отпускает указатель.


> **Важные API-интерфейсы**: [**Класс PointerUpThemeAnimation**](https://msdn.microsoft.com/library/windows/apps/hh969168), [ **PointerDownThemeAnimation-класс**](https://msdn.microsoft.com/library/windows/apps/hh969164)


## <a name="dos-and-donts"></a>Возможности и ограничения

-   Если вы используете анимацию поднятия указателя, немедленно запустите анимацию после того, как пользователь отпустит указатель. Благодаря этому пользователь видит незамедлительную реакцию на свои действия, даже если ответ на действие, запускаемое касанием (например, переход на новую страницу), появляется медленнее.

## <a name="related-articles"></a>Связанные статьи

* [Общие сведения об анимации](https://msdn.microsoft.com/library/windows/apps/mt187350)
* [Анимации указатель щелчков](https://msdn.microsoft.com/library/windows/apps/xaml/jj649432)
* [Краткое руководство. Анимация пользовательского интерфейса с помощью библиотеки анимации](https://msdn.microsoft.com/library/windows/apps/xaml/hh452703)
* [**Класс PointerUpThemeAnimation**](https://msdn.microsoft.com/library/windows/apps/hh969168)
* [**Класс PointerDownThemeAnimation**](https://msdn.microsoft.com/library/windows/apps/hh969164)

 

 




