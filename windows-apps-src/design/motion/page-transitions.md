---
author: serenaz
Description: Learn how to use page transitions in your UWP apps.
title: Переходы между страницами в приложениях UWP
template: detail.hbs
ms.author: sezhen
ms.date: 04/08/2018
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: Windows10, UWP
pm-contact: stmoy
ms.localizationpriority: medium
ms.openlocfilehash: cba05cd9106d64f443e87b1e8373b2501d0ce451
ms.sourcegitcommit: 517c83baffd344d4c705bc644d7c6d2b1a4c7e1a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/07/2018
ms.locfileid: "1842241"
---
# <a name="page-transitions"></a>Смена страниц

Смена страниц — это перемещение пользователей между страницами в приложении с демонстрацией связи между страницами. Переходы страниц помогают пользователям понять, находятся ли они на верхнем уровне иерархии, перемещаются между страницами одного уровня или спускаются на более низкий уровень иерархии страниц.

Для навигации между страницами в приложении предоставляются две разные анимации: *Обновление страницы* и *Винт*. Они представлены подклассами [**NavigationTransitionInfo**](/api/windows.ui.xaml.media.animation.navigationtransitioninfo).

## <a name="page-refresh"></a>Обновление страницы

Обновление страницы представляет собой сочетание анимации скольжения вверх и анимации появления входящего содержимого. Используйте обновление страницы, когда пользователь переходит в верхнюю часть стека навигации, например при переходе между вкладками или элементами левой навигационной панели.

Она призвана создать ощущение, что пользователь начинает заново.

![анимация обновления страницы](images/page-refresh.gif)

Анимация обновления страницы представлена классом [**EntranceNavigationTransitionInfoClass**](/api/windows.ui.xaml.media.animation.entrancenavigationtransitioninfo).

```csharp
// Explicitly play the page refresh animation
myFrame.Navigate(typeof(Page2), null, new EntranceNavigationTransitionInfo());

```

**Примечание**. [**Кадр**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.frame) автоматически использует [**NavigationThemeTransition**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.animation.navigationthemetransition) для анимации навигации между двумя страницами. По умолчанию анимацией является обновление страницы.

## <a name="drill"></a>Винт

Используйте "винт", когда пользователи переходят на уровень ниже в приложении, например при отображении дополнительной информации после выбора элемента.

Эта анимация создает ощущение, что пользователь перешел на более глубокий уровень приложения.

![анимация "винт"](images/drill.gif)

Анимация "винт" представлена классом [**DrillInNavigationTransitionInfo**](/api/windows.ui.xaml.media.animation.drillinnavigationtransitioninfo).

```csharp
// Play the drill in animation
myFrame.Navigate(typeof(Page2), null, new DrillInNavigationTransitionInfo());
```

## <a name="suppress"></a>Подавление

Чтобы избежать воспроизведения анимации во время перехода, используйте [**SuppressNavigationTransitionInfo**](/api/windows.ui.xaml.media.animation.suppressnavigationtransitioninfo) вместо других подтипов **NavigationTransitionInfo**.

```csharp
// Suppress the default animation
myFrame.Navigate(typeof(Page2), null, new SuppressNavigationTransitionInfo());
```

Подавление анимации полезно, если вы создаете собственный переход с помощью [подключенных анимаций](connected-animation.md) или неявных анимаций показа/скрытия.

## <a name="backwards-navigation"></a>Навигация в обратном направлении

Для воспроизведения определенной анимации при переходе в обратном направлении, используйте `Frame.GoBack(NavigationTransitionInfo)`.

Это может быть полезно при динамическом изменении поведения навигации в зависимости от размера экрана, например при использовании адаптивного шаблона основных и подробных данных.

## <a name="related-topics"></a>Смежные разделы

- [Навигация между двумя страницами](../basics/navigate-between-two-pages.md)
- [Движение в приложениях UWP](index.md)