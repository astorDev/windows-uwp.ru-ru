---
author: mijacobs
Description: "Используйте анимацию указателя, чтобы предоставить пользователям визуальный отклик на касание или щелчок элемента."
title: "Анимации щелчков указателя в приложениях UWP"
ms.assetid: EEB10A2C-629A-4705-8468-4D019D74DDFF
label: Motion--Pointer animations
template: detail.hbs
translationtype: Human Translation
ms.sourcegitcommit: a3924fef520d7ba70873d6838f8e194e5fc96c62
ms.openlocfilehash: c208b67829e2053302ec0cc7c6da013b89cee8b3

---

# <a name="pointer-click-animations"></a>Анимации щелчков указателя

<link rel="stylesheet" href="https://az835927.vo.msecnd.net/sites/uwp/Resources/css/custom.css">

Используйте анимацию указателя, чтобы предоставить пользователям визуальный отклик на касание или щелчок элемента. Анимация нажатия указателя слегка уменьшает и наклоняет нажатый элемент и воспроизводится при первом касании элемента. Анимация поднятия указателя, которая возвращает элемент в исходное положение, воспроизводится, когда пользователь отпускает указатель.


<div class="important-apis" >
<b>Важные API</b><br/>
<ul>
<li>[**Класс PointerUpThemeAnimation**](https://msdn.microsoft.com/library/windows/apps/hh969168)</li>
<li>[**Класс PointerDownThemeAnimation**](https://msdn.microsoft.com/library/windows/apps/hh969164)</li>
</ul>
</div>


## <a name="dos-and-donts"></a>Возможности и ограничения

-   Если вы используете анимацию поднятия указателя, немедленно запустите анимацию после того, как пользователь отпустит указатель. Благодаря этому пользователь видит незамедлительную реакцию на свои действия, даже если ответ на действие, запускаемое касанием (например, переход на новую страницу), появляется медленнее.

## <a name="related-articles"></a>Связанные разделы

* [Обзор анимаций](https://msdn.microsoft.com/library/windows/apps/mt187350)
* [Анимация щелчков указателя](https://msdn.microsoft.com/library/windows/apps/xaml/jj649432)
* [Краткое руководство: анимация пользовательского интерфейса с помощью анимаций библиотеки](https://msdn.microsoft.com/library/windows/apps/xaml/hh452703)
* [**Класс PointerUpThemeAnimation**](https://msdn.microsoft.com/library/windows/apps/hh969168)
* [**Класс PointerDownThemeAnimation**](https://msdn.microsoft.com/library/windows/apps/hh969164)

 

 







<!--HONumber=Dec16_HO2-->


