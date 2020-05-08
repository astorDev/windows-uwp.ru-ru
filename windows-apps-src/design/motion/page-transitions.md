---
Description: Узнайте, как использовать переходы страниц в приложениях Windows.
title: Смена страниц
template: detail.hbs
ms.date: 04/08/2018
ms.topic: article
keywords: windows 10, uwp
pm-contact: stmoy
ms.localizationpriority: medium
ms.custom: RS5
ms.openlocfilehash: 6239d8409767cab06d4d2c8c9c3abb9d743ca1c9
ms.sourcegitcommit: 0dee502484df798a0595ac1fe7fb7d0f5a982821
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/08/2020
ms.locfileid: "82970519"
---
# <a name="page-transitions"></a>Смена страниц

Смена страниц — это перемещение пользователей между страницами в приложении с демонстрацией связи между страницами. Переходы страниц помогают пользователям понять, находятся ли они на верхнем уровне иерархии, перемещаются между страницами одного уровня или спускаются на более низкий уровень иерархии страниц.

Для навигации между страницами в приложении, *обновлением страницы* и *детализацией*предоставляются две различные анимации, которые представлены подклассами [**навигатионтранситионинфо**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.animation.navigationtransitioninfo).

## <a name="examples"></a>Примеры

<table>
<th align="left">XAML Controls Gallery<th>
<tr>
<td><img src="images/xaml-controls-gallery-app-icon.png" alt="XAML controls gallery" width="168"></img></td>
<td>
    <p>Если приложение <strong style="font-weight: semi-bold">коллекции элементов управления XAML</strong> установлено, щелкните здесь, чтобы <a href="xamlcontrolsgallery:/item/PageTransition">открыть приложение и просмотреть переходы страниц в действии</a>.</p>
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

**Примечание**. [**Кадр**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.frame) автоматически использует [**NavigationThemeTransition**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.animation.navigationthemetransition) для анимации навигации между двумя страницами. По умолчанию анимацией является обновление страницы.

## <a name="drill"></a>Drill

Используйте "винт", когда пользователи переходят на уровень ниже в приложении, например при отображении дополнительной информации после выбора элемента.

Эта анимация создает ощущение, что пользователь перешел на более глубокий уровень приложения.

![анимация "винт"](images/drill.gif)

Анимация детализации представлена классом [**дриллиннавигатионтранситионинфо**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.animation.drillinnavigationtransitioninfo) .

```csharp
// Play the drill in animation
myFrame.Navigate(typeof(Page2), null, new DrillInNavigationTransitionInfo());
```

## <a name="horizontal-slide"></a>Горизонтальный слайд

Используйте горизонтальный слайд, чтобы увидеть, что родственные страницы расположены рядом друг с другом. Элемент управления [представления navigationview](../controls-and-patterns/navigationview.md) автоматически использует эту анимацию для работы с верхней панелью навигации, но при создании собственного интерфейса навигации по горизонтали можно реализовать горизонтальный слайд с помощью слиденавигатионтранситионинфо.

Желаемое чувство заключается в том, что пользователь перемещается между страницами, расположенными рядом друг с другом. 

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

## <a name="backwards-navigation"></a>Обратная навигация

Для воспроизведения определенной анимации при переходе в обратном направлении, используйте `Frame.GoBack(NavigationTransitionInfo)`.

Это может быть полезно при динамическом изменении поведения навигации в зависимости от размера экрана, например при использовании адаптивного шаблона основных и подробных данных.

## <a name="related-topics"></a>Связанные темы

- [Навигация между двумя страницами](../basics/navigate-between-two-pages.md)
- [Перемещение в Увиндовсвп Apps](index.md)
