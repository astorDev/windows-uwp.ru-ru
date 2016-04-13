---
Description: Используйте анимацию указателя, чтобы предоставить пользователям визуальный отклик на касание или щелчок элемента.
title: Анимации щелчков указателя в приложениях UWP
ms.assetid: EEB10A2C-629A-4705-8468-4D019D74DDFF
label: Motion--Pointer animations
template: detail.hbs
---

# Анимации щелчков указателя

Используйте анимацию указателя, чтобы предоставить пользователям визуальный отклик на касание или щелчок элемента. Анимация нажатия указателя слегка уменьшает и наклоняет нажатый элемент и воспроизводится при первом касании элемента. Анимация поднятия указателя, которая возвращает элемент в исходное положение, воспроизводится, когда пользователь отпускает указатель.

\[ Обновлено для приложений UWP в Windows 10. Статьи о Windows 8.x см. в [архиве](http://go.microsoft.com/fwlink/p/?linkid=619132) \]


**Важные API**

-   [**Класс PointerUpThemeAnimation**](https://msdn.microsoft.com/library/windows/apps/hh969168)
-   [**Класс PointerDownThemeAnimation**](https://msdn.microsoft.com/library/windows/apps/hh969164)



## Возможности и ограничения


-   Если вы используете анимацию поднятия указателя, немедленно запустите анимацию после того, как пользователь отпустит указатель. Благодаря этому пользователь видит незамедлительную реакцию на свои действия, даже если ответ на действие, запускаемое касанием (например, переход на новую страницу), появляется медленнее.

## Статьи по теме

**Разработчикам (XAML)**
* [Обзор анимаций](https://msdn.microsoft.com/library/windows/apps/mt187350)
* [Анимация щелчков указателя](https://msdn.microsoft.com/library/windows/apps/xaml/jj649432)
* [Краткое руководство: анимация пользовательского интерфейса](https://msdn.microsoft.com/library/windows/apps/xaml/hh452703)
* [**Класс PointerUpThemeAnimation**](https://msdn.microsoft.com/library/windows/apps/hh969168)
* [**Класс PointerDownThemeAnimation**](https://msdn.microsoft.com/library/windows/apps/hh969164)

 

 






<!--HONumber=Mar16_HO3-->


