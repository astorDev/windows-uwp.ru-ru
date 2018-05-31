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
ms.openlocfilehash: dc42199eba00071f5dbabd83a4ae524298a619ee
ms.sourcegitcommit: 91511d2d1dc8ab74b566aaeab3ef2139e7ed4945
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/30/2018
ms.locfileid: "1818514"
---
# <a name="page-transitions"></a>Переходы страницы

Переходы страниц — это анимации, которые воспроизводятся, когда пользователи перемещаются между страницами в приложении, демонстрируя связь между страницами. Переходы страниц помогают пользователям понять, находятся ли они на верхнем уровне иерархии, перемещаются между страницами одного уровня или спускаются на более низкий уровень иерархии страниц.

Для навигации между страницами в приложении предоставляются две разные анимации: *обновление страницы* и *винт*. Они представлены подклассами [NavigationTransitionInfo](/uwp/api/windows.ui.xaml.media.animation.navigationtransitioninfo).

## <a name="page-refresh"></a>Обновление страницы

Обновление страницы представляет собой сочетание анимации скольжения вверх и анимации появления входящего содержимого. Она призвана создать ощущение, что пользователь начинает заново.

Используйте обновление страницы, когда пользователь переходит в верхнюю часть стека навигации, например при переходе между [вкладками](../controls-and-patterns/tabs-pivot.md) или элементами [левой навигационной панели](../controls-and-patterns/navigationview.md). По умолчанию метод [Frame.Navigate()](/uwp/api/windows.ui.xaml.controls.frame.navigate) использует обновление страницы.

![анимация обновления страницы](images/page-refresh.gif)

Анимация обновления страницы представлена классом [EntranceNavigationTransitionInfoClass](/uwp/api/windows.ui.xaml.media.animation.entrancenavigationtransitioninfo).

```csharp
// Explicitly play the page refresh animation
myFrame.Navigate(typeof(Page2), null, new EntranceNavigationTransitionInfo());
```

## <a name="drill"></a>Винт

Используйте "винт", когда пользователи переходят на уровень ниже в приложении, например при отображении дополнительной информации после выбора элемента.

Эта анимация создает ощущение, что пользователь перешел на более глубокий уровень приложения.

![анимация "винт"](images/drill.gif)

Анимация "винт" представлена классом [DrillInNavigationTransitionInfo](/uwp/api/windows.ui.xaml.media.animation.drillinnavigationtransitioninfo).

```csharp
// Play the drill in animation
myFrame.Navigate(typeof(Page2), null, new DrillInNavigationTransitionInfo());
```

## <a name="suppress"></a>Подавление

Подавление анимации полезно, если вы создаете собственный переход с помощью [подключенных анимаций](connected-animation.md) или неявных анимаций показа/скрытия.

Чтобы избежать воспроизведение анимации во время перехода, используйте [SuppressNavigationTransitionInfo](/uwp/api/windows.ui.xaml.media.animation.suppressnavigationtransitioninfo) вместо других подтипов [NavigationTransitionInfo](/uwp/api/windows.ui.xaml.media.animation.navigationtransitioninfo).

```csharp
// Suppress the default animation
myFrame.Navigate(typeof(Page2), null, new SuppressNavigationTransitionInfo());
```

## <a name="backwards-navigation"></a>Навигация в обратном направлении

По умолчанию [Frame.GoBack()](/uwp/api/windows.ui.xaml.controls.frame.goback) воспроизводит соответствующую анимацию перехода в обратном направлении на основе анимации, которая воспроизводилась для перехода на страницу. Например, если приложение использует анимацию "вкручивания винта" для перехода на страницу, при переходе в обратном направлении отобразится анимация "выкручивания винта".

Для воспроизведения определенной анимации при переходе в обратном направлении, используйте `Frame.GoBack(NavigationTransitionInfo)`. Это может быть полезно при динамическом изменении поведения навигации в зависимости от размера экрана, например при использовании адаптивного шаблона основных и подробных данных.

## <a name="related-topics"></a>Статьи по теме

- [Навигация между двумя страницами](../basics/navigate-between-two-pages.md)
- [Движение в приложениях UWP](index.md)
