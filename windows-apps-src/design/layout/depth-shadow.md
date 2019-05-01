---
author: knicholasa
description: Z-depth, или путь относительно глубину и тени можно двумя способами встраивать в приложения, чтобы помочь пользователям сосредоточиться естественным образом и эффективно глубины.
title: Z-depth и тени для приложений универсальной платформы Windows
template: detail.hbs
ms.author: nichola
ms.date: 04/19/2019
ms.topic: article
ms.custom: 19H1
keywords: windows 10, uwp
pm-contact: chigy
ms.localizationpriority: medium
ms.openlocfilehash: ab49f13d3938e55750ce523f9e0d4ae241304763
ms.sourcegitcommit: fca0132794ec187e90b2ebdad862f22d9f6c0db8
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/24/2019
ms.locfileid: "63817723"
---
# <a name="z-depth-and-shadow"></a>Глубина трехмерной сцены и тени

![Gif, показывающий четыре серых прямоугольников, которые являются гистограммы с накоплением по диагонали, один над другим. Gif анимируется, чтобы shadows появляются и исчезают.](images/elevation-shadow/shadow.gif)

Создание визуальной иерархии элементов во всем пользовательском Интерфейсе делает пользовательский Интерфейс, удобной для быстрого просмотра и указывает, что нужно обратить внимание на. Повышение прав, act, приведению выберите элементы пользовательского интерфейса вперед, часто используется для достижения такой иерархии в программном обеспечении. В этой статье рассматривается создание повышение прав в приложении UWP с помощью z-depth и тени.

Z-depth — это термин, который используется для создателей приложений 3D для обозначения расстояние между двух областей вдоль оси z. Здесь показано, как закрыть объект находится в средство просмотра. Представляйте его применения аналогичной концепции для x / y-координаты, но в направлении оси z.

## <a name="why-use-z-depth"></a>Зачем использовать z-depth?

В реальном мире мы склонны сосредотачиваться на объекты, которые ближе к нам. К цифровой пользовательский интерфейс, можно применить этот пространственный инстинкт. Например если вы переносите элемент ближе к пользователю, затем пользователь инстинктивно сосредоточусь на элемент. Перемещение пользовательского интерфейса элементами ближе оси z вы можете установить визуальной иерархии между объектами, как помочь пользователям выполнить задачи естественным образом и эффективно в вашем приложении.

## <a name="what-is-shadow"></a>Что такое тени

Тени — один из способов, которые пользователь воспринимает повышение прав. Индикатор над с повышенными правами объекта создает тень на область, расположенную ниже. Чем выше объекта, чем больше и мягкий становится тени. Объекты с повышенными правами в пользовательский Интерфейс не требуется имеют тени, но они помогают создать внешний вид повышение прав.

В приложениях UWP shadows следует использовать в определенном целенаправленным, а не внешнего вида. С помощью слишком много shadows уменьшить или исключить возможность использования тени сосредоточиться пользователя.

Если вы используете стандартные элементы управления, ThemeShadow shadows будет включен автоматически в пользовательский Интерфейс. Тем не менее можно вручную включить тени в пользовательский Интерфейс с помощью ThemeShadow или DropShadow API-интерфейсы. 

## <a name="themeshadow"></a>ThemeShadow

ThemeShadow, типа могут быть применены к любому элементу XAML для рисования затемняет соответствующим образом на основе x, y, z координаты. ThemeShadow также автоматически корректируется для других эксплуатации:

- Адаптация к изменениям в освещения, тема, среда приложений и оболочки.
- Применяется тени для элементов, автоматически на основании их z глубины. 
- Элементы обеспечивает синхронизацию, так как они перемещаются и изменяются повышение прав.
- Совпадали shadows везде и в приложениях.

Вот, как ThemeShadow реализовано MenuFlyout. MenuFlyout имеет встроенный интерфейс, где основной рабочей области, повышены до 32 пикс и всех дополнительных каскадные меню открывается + 8px выше меню, которое открывается из.

![Снимок экрана ThemeShadow применяется к MenuFlyout с три открытых, вложенные меню. Первое меню является с повышенными правами 32 пикс и всех последующих меню, открывшемся в предыдущем меню — с повышенными правами 8px дополнительные таким образом, что оставляет distinct тень фона.](images/elevation-shadow/themeshadow-menuflyout.png)

### <a name="themeshadow-in-common-controls"></a>ThemeShadow Общие элементы управления

Следующие общие элементы управления будут автоматически использовать ThemeShadow для приведения тени от глубины 32 пикс, если не указано иначе:

- [Контекстное меню](../controls-and-patterns/menus.md), [панели команд](../controls-and-patterns/app-bars.md), [команды панели всплывающего меню](../controls-and-patterns/command-bar-flyout.md), [MenuBar](../controls-and-patterns/menus.md#create-a-menu-bar)
- [Диалоговые окна и всплывающие окна](../controls-and-patterns/dialogs.md) (диалоговое окно в 64px)
- [NavigationView](../controls-and-patterns/navigationview.md)
- [Поле со списком](../controls-and-patterns/combo-box.md), [ToggleSplitButton DropDownButton SplitButton,](../controls-and-patterns/buttons.md)
- [TeachingTip](../controls-and-patterns/dialogs-and-flyouts/teaching-tip.md)
- [AutoSuggestBox](../controls-and-patterns/auto-suggest-box.md) 
- [Выбор календаря, дате и времени](../controls-and-patterns/date-and-time.md)
- [Подсказка](../controls-and-patterns/tooltips.md) (16px)
- [Передача носителя управления](../controls-and-patterns/media-playback.md#media-transport-controls), [InkToolbar](../controls-and-patterns/inking-controls.md)
- [Подключенные анимации](../motion/connected-animation.md)

Примечание. Всплывающие окна будет применяться только ThemeShadow при компиляции с Windows 10 версии 1903 года или более поздней SDK.

### <a name="themeshadow-in-popups"></a>ThemeShadow в контекстных меню

Часто бывает так, что пользовательский Интерфейс приложения использует всплывающее окно для сценариев требующие внимания и быстрое действие пользователя. Это отличных примеров тени следует использовать для создания иерархии в пользовательском Интерфейсе приложения.

ThemeShadow автоматически приводит shadows при применении к любому элементу XAML в [всплывающее окно](/uwp/api/windows.ui.xaml.controls.primitives.popup). Он будет приведен тенями фона содержимого приложения за его и любых других открытых всплывающих окон под ним.

Чтобы использовать ThemeShadow с всплывающие окна, используйте `Shadow` свойство для применения ThemeShadow элементу XAML. Затем elevate элемента с помощью других элементов, например с помощью z-компонент `Translation` свойство.
Для большинства всплывающего окна пользовательского интерфейса рекомендуемое значение по умолчанию высота относительно содержимого фона приложения — 32 пикселя эффективным.

Этот пример показывает прямоугольник во всплывающем окне, отбрасывающего тень на фон содержимого приложения и другие всплывающие окна за ней.

```xaml
<Popup>
    <Rectangle x:Name="PopupRectangle" Fill="Lavender" Height="48" Width="96">
        <Rectangle.Shadow>
            <ThemeShadow />
        </Rectangle.Shadow>
    </Rectangle>
</Popup>
```

```csharp
// Elevate the rectangle by 32px
PopupRectangle.Translation += new Vector3(0, 0, 32);
```

![Единый прямоугольное всплывающее окно с тенью.](images/elevation-shadow/PopupRectangle.png)

### <a name="disabling-default-themeshadow-on-custom-flyout-controls"></a>Отключение по умолчанию управляет ThemeShadow на пользовательского всплывающего меню

Элементы управления на основе [всплывающего меню](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.flyout), [DatePickerFlyout](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.datepickerflyout), [MenuFlyout](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.menuflyout) или [TimePickerFlyout](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.timepickerflyout) автоматически использовать ThemeShadow для приведения тень.

Если по умолчанию тени работает неправильно на вашей содержимое элемента управления, а затем его можно отключить, установив [IsDefaultShadowEnabled](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.flyoutpresenter.isdefaultshadowenabled) свойства `false` на связанный FlyoutPresenter:

```xaml
<Flyout>
    <Flyout.FlyoutPresenterStyle>
        <Style TargetType="FlyoutPresenter">
            <Setter Property="IsDefaultShadowEnabled" Value="False" />
        </Style>
    </Flyout.FlyoutPresenterStyle>
</Flyout>
```

### <a name="themeshadow-in-other-elements"></a>ThemeShadow в других элементах

В целом мы рекомендуем тщательно продумывать использования тени и ограничить его использование для случаев, где он представляет значимые визуальную иерархию. Тем не менее мы предоставляем способ уводят в тень, из любой элемент пользовательского интерфейса, в случае, если дополнительные сценарии, требующий его.

Чтобы привести тень от элемента XAML, не во всплывающем окне, необходимо явно указать другие элементы, которые могут получать тени в `ThemeShadow.Receivers` коллекции. Получатели не может быть предком caster в визуальном дереве.

В этом примере показано два прямоугольника, приведение тени в сетку за ними:

```xaml
<Grid>
    <Grid.Resources>
        <ThemeShadow x:Name="SharedShadow" />
    </Grid.Resources>

    <Grid x:Name="BackgroundGrid" Background="{ThemeResource ApplicationPageBackgroundThemeBrush}" />

    <Rectangle x:Name="Rectangle1" Height="100" Width="100" Fill="Turquoise" Shadow="{StaticResource SharedShadow}" />

    <Rectangle x:Name="Rectangle2" Height="100" Width="100" Fill="Turquoise" Shadow="{StaticResource SharedShadow}" />
</Grid>
```

```csharp
/// Add BackgroundGrid as a shadow receiver and elevate the casting buttons above it
SharedShadow.Receivers.Add(BackgroundGrid);

Rectangle1.Translation += new Vector3(0, 0, 16);
Rectangle2.Translation += new Vector3(120, 0, 32);
```

![Двух прямоугольников бирюзовый рядом друг с другом, как с тенями.](images/elevation-shadow/SharedShadow.png)

### <a name="performance-best-practices-for-themeshadow"></a>Рекомендации по производительности для ThemeShadow

1. Система задает ограничение на число 5 пар caster получатель и отключается тени, при превышении. Придерживайтесь ограничение принудительно системы 5 пар caster получателя.

2. Ограничьте число элементов приемника минимально необходимой.

3. Несколько элементов получатель находятся на том же повышение прав, попробуйте объединить их, вместо предназначен для одного родительского элемента.

4. Если несколько элементов будет приведен тот же тип тень на те же элементы получателя добавить тени в качестве общего ресурса и повторно использовать ее.

## <a name="drop-shadow"></a>Тень

DropShadow не автоматически отвечает на окружающую среду и не использует источники света. Пример реализации, см. в разделе [DropShadow класс](https://docs.microsoft.com/uwp/api/windows.ui.composition.dropshadow).

## <a name="which-shadow-should-i-use"></a>Какие тени следует использовать?

| Свойство | ThemeShadow | DropShadow |
| - | - | - | - |
| **Min SDK** | Windows 10 версии 1903 | 14393 |
| **Адаптируемости** | Да | Нет |
| **Настройки** | Нет | Да |
| **Источник света** | Автоматическое (глобальный по умолчанию, но можно переопределить для каждого приложения) | Нет |
| **Поддерживается в средах 3D** | Да | Нет |

- Имейте в виду, что тень служит для предоставления значимых иерархии, а не как простой визуальное представление.
- Как правило рекомендуется использовать ThemeShadow, который автоматически адаптирует на окружающую среду.
- Для проблемы производительности ограничить число теней, используйте другие визуальное представление или DropShadow.
- Если более сложных сценариях для достижения визуальной иерархии, рассмотрите возможность использования других визуальное представление (например, цвет). Если требуется тени, используйте DropShadow.
