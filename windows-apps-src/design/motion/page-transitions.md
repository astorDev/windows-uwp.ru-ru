---
Description: Узнайте, как использовать переходы в приложениях универсальной платформы Windows.
title: Переходы между страницами в приложениях UWP
template: detail.hbs
ms.date: 04/08/2018
ms.topic: article
keywords: windows 10, uwp
pm-contact: stmoy
ms.localizationpriority: medium
ms.custom: RS5
ms.openlocfilehash: 9b3244c24ff4fa8e3c85ee9970536b1b35d8efd5
ms.sourcegitcommit: cc0ef75f314658b14376eb60ef8e5bb4d7726e04
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/08/2019
ms.locfileid: "65444195"
---
# <a name="page-transitions"></a>Смена страниц

Смена страниц — это перемещение пользователей между страницами в приложении с демонстрацией связи между страницами. Переходы страниц помогают пользователям понять, находятся ли они на верхнем уровне иерархии, перемещаются между страницами одного уровня или спускаются на более низкий уровень иерархии страниц.

Для навигации между страницами в приложении предоставляются две разные анимации: *Обновление страницы* и *Винт*. Они представлены подклассами [**NavigationTransitionInfo**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.animation.navigationtransitioninfo).

## <a name="examples"></a>Примеры

<table>
<th align="left">Галерея элементов управления XAML<th>
<tr>
<td><img src="images/xaml-controls-gallery-app-icon.png" alt="XAML controls gallery" width="168"></img></td>
<td>
    <p>Если у вас есть <strong style="font-weight: semi-bold">коллекции элементов управления XAML</strong> приложения. Щелкните здесь, чтобы <a href="xamlcontrolsgallery:/item/PageTransition">откройте приложение и увидеть переходов страниц в действии</a>.</p>
    <ul>
    <li><a href="https://www.microsoft.com/store/productId/9MSVH128X2ZT">Получить приложение XAML Controls Gallery (Microsoft Store)</a></li>
    <li><a href="https://github.com/Microsoft/Xaml-Controls-Gallery">Получить исходный код (GitHub)</a></li>
    </ul>
</td>
</tr>
</table>

## <a name="page-refresh"></a>Обновление страницы

Обновление страницы представляет собой сочетание анимации скольжения вверх и анимации появления входящего содержимого. Используйте обновление страницы, когда пользователь переходит в верхнюю часть стека навигации, например при переходе между вкладками или элементами левой навигационной панели.

Она призвана создать ощущение, что пользователь начинает заново.

![анимация обновления страницы](images/page-refresh.gif)

Анимация обновления страницы представлена классом [**EntranceNavigationTransitionInfoClass**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.animation.entrancenavigationtransitioninfo).

```csharp
// Explicitly play the page refresh animation
myFrame.Navigate(typeof(Page2), null, new EntranceNavigationTransitionInfo());

```

**Примечание**. Объект [ **кадра** ](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.frame) автоматически использует [ **NavigationThemeTransition** ](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.animation.navigationthemetransition) для анимации перехода между двумя страницами. По умолчанию анимацией является обновление страницы.

## <a name="drill"></a>Винт

Используйте "винт", когда пользователи переходят на уровень ниже в приложении, например при отображении дополнительной информации после выбора элемента.

Эта анимация создает ощущение, что пользователь перешел на более глубокий уровень приложения.

![анимация "винт"](images/drill.gif)

Анимация "винт" представлена классом [**DrillInNavigationTransitionInfo**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.animation.drillinnavigationtransitioninfo).

```csharp
// Play the drill in animation
myFrame.Navigate(typeof(Page2), null, new DrillInNavigationTransitionInfo());
```

## <a name="horizontal-slide"></a>Горизонтальная слайд

Используйте горизонтальное слайд, чтобы показать, что родственным ей страницам отображаются рядом друг с другом. [NavigationView](../controls-and-patterns/navigationview.md) элемент управления автоматически использует эту анимацию для верхней панели навигации, но если вы создаете собственный горизонтальное навигацию, затем можно реализовать горизонтальное слайд с SlideNavigationTransitionInfo.

Требуемой чувство является то, что пользователь перемещается между страницами, которые находятся рядом друг с другом. 

```csharp
// Navigate to the right, ie. from LeftPage to RightPage
myFrame.Navigate(typeof(RightPage), null, new SlideNavigationTransitionInfo() { Effect = SlideNavigationTransitionEffect.FromRight } );

// Navigate to the left, ie. from RightPage to LeftPage
myFrame.Navigate(typeof(LeftPage), null, new SlideNavigationTransitionInfo() { Effect = SlideNavigationTransitionEffect.FromLeft } );
```

## <a name="suppress"></a>Подавление

Чтобы избежать воспроизведения анимации во время перехода, используйте [**SuppressNavigationTransitionInfo**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.animation.suppressnavigationtransitioninfo) вместо других подтипов **NavigationTransitionInfo**.

```csharp
// Suppress the default animation
myFrame.Navigate(typeof(Page2), null, new SuppressNavigationTransitionInfo());
```

Подавление анимации полезно, если вы создаете собственный переход с помощью [подключенных анимаций](connected-animation.md) или неявных анимаций показа/скрытия.

## <a name="backwards-navigation"></a>Навигация в обратном направлении

Для воспроизведения определенной анимации при переходе в обратном направлении, используйте `Frame.GoBack(NavigationTransitionInfo)`.

Это может быть полезно при динамическом изменении поведения навигации в зависимости от размера экрана, например при использовании адаптивного шаблона основных и подробных данных.

## <a name="related-topics"></a>См. также

- [Переход между двумя страницами](../basics/navigate-between-two-pages.md)
- [Движения в приложениях универсальной платформы Windows](index.md)
