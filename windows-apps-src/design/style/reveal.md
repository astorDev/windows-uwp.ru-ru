---
author: mijacobs
description: Отображение— это световой эффект, который добавляет приложению глубины и помогает привлечь внимание к интерактивным элементам приложения.
title: Эффект
template: detail.hbs
ms.author: mijacobs
ms.date: 08/9/2017
ms.topic: article
keywords: windows 10, uwp
pm-contact: kisai
design-contact: conrwi
dev-contact: jevansa
doc-status: Published
ms.localizationpriority: medium
ms.openlocfilehash: db71916c9297296c4d3bb89e05032c5f413f332e
ms.sourcegitcommit: 71e8eae5c077a7740e5606298951bb78fc42b22c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/14/2018
ms.locfileid: "6669692"
---
# <a name="reveal-highlight"></a>Эффект

![главное изображение](images/header-reveal-highlight.svg)

Эффект отображения — это световой эффект, выделяющий интерактивные элементы, такие как панели команд, когда пользователь наводит на них указатель. 

> **Важные API**: [класс RevealBrush](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.revealbrush), [класс RevealBackgroundBrush](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.revealbackgroundbrush), [класс RevealBorderBrush](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.revealborderbrush), [RevealBrushHelper class](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.revealbrushhelper), [класс VisualState](https://docs.microsoft.com/en-us/uwp/api/Windows.UI.Xaml.VisualState)

## <a name="how-it-works"></a>Принцип работы
Эффект отображения привлекает внимание к интерактивным элементам, отображая контейнер элемента, когда указатель находится рядом, как показано на следующем рисунке:

![Наглядное представление эффекта отображения](images/Nav_Reveal_Animation.gif)

Показывая скрытые границы вокруг объектов, эффект отображения дает пользователям наглядное представление о пространстве, с которым они взаимодействуют, и доступных действиях. Это особенно важно при работе с элементами управления списками и групп кнопок.

## <a name="examples"></a>Примеры

<table>
<th align="left">Галерея элементов управления XAML<th>
<tr>
<td><img src="images/xaml-controls-gallery-sm.png" alt="XAML controls gallery"></img></td>
<td>
    <p>Если у вас установлено приложение <strong style="font-weight: semi-bold">галереи элементов управления XAML</strong>, щелкните здесь, чтобы <a href="xamlcontrolsgallery:/item/Reveal">открыть приложение и увидеть эффект отображения в действии</a>.</p>
    <ul>
    <li><a href="https://www.microsoft.com/store/productId/9MSVH128X2ZT">Скачать приложение галереи элементов управления XAML (Microsoft Store)</a></li>
    <li><a href="https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/XamlUIBasics">Получить исходный код (GitHub)</a></li>
    </ul>
</td>
</tr>
</table>

## <a name="video-summary"></a>Видео с кратким описанием

> [!VIDEO https://channel9.msdn.com/Events/Windows/Windows-Developer-Day-Fall-Creators-Update/WinDev013/player]

## <a name="how-to-use-it"></a>Использование

Отображение автоматически работает для некоторых элементов управления. Для других элементов управления можно включить эффект отображения, назначив специальный стиль для элемента управления, как описано в разделах этой статьи [Включение эффекта отображения для других элементов управления](#enabling-reveal-on-other-controls) и [Включение эффекта отображения для пользовательских элементов управления](#enabling-reveal-on-custom-controls) .

## <a name="controls-that-automatically-use-reveal"></a>Элементы управления, автоматически использующие эффект отображения

- [**ListView**](../controls-and-patterns/lists.md)
- [**GridView**](../controls-and-patterns/lists.md)
- [**TreeView**](../controls-and-patterns/tree-view.md)
- [**NavigationView**](../controls-and-patterns/navigationview.md)
- [**MediaTransportControl**](../controls-and-patterns/media-playback.md)
- [**CommandBar**](../controls-and-patterns/app-bars.md)

На этих иллюстрациях показан эффект отображения выделения на нескольких различных элементах управления:

![Примеры эффекта отображения](images/RevealExamples_Collage.png)


## <a name="enabling-reveal-on-other-controls"></a>Включение эффекта отображения для других элементов управления

Если вы используете подходящий для отображения сценарий (элементы управления представляют основное содержимое и/или расположены в виде списка или коллекции), вы можете воспользоваться стилями из нашего ресурса, чтобы включить отображение в таком сценарии.

Для этих элементов управления отображение не используется по умолчанию, поскольку они не относятся к основным и обычно выполняют роль вспомогательных элементов управления для центральных пунктов приложения. Однако каждое приложение уникально, и если эти элементы активно используются в вашем приложении, мы подготовили для вас несколько подходящих стилей.

| Название элемента управления   | Имя ресурса |
|----------|:-------------:|
| Button |  ButtonRevealStyle |
| ToggleButton | ToggleButtonRevealStyle |
| RepeatButton | RepeatButtonRevealStyle |
| AppBarButton | AppBarButtonRevealStyle |
| AppBarToggleButton | AppBarToggleButtonRevealStyle |
| GridViewItem (эффект отображения поверх содержимого) | GridViewItemRevealBackgroundShowsAboveContentStyle |

Чтобы применить эти стили, просто задайте свойство [Стиль](/uwp/api/Windows.UI.Xaml.Style) элемента управления:

```xaml
<Button Content="Button Content" Style="{StaticResource ButtonRevealStyle}"/>
```

### <a name="reveal-in-themes"></a>Эффект отображения в темах

Эффект отображения незначительно меняется в зависимости от запрошенной темы элемента управления, приложения или пользовательской настройки. В темной теме граница эффекта отображения и свет при наведении курсора белый, а в светлой теме границы затеняются до светло-серого.

![Эффект отображения в темной и светлой теме](images/Dark_vs_LightReveal.png)

Чтобы включить белые границы в светлой теме, просто задайте для запрошенной темы элемента управления значение "Темная".

```xaml
<Grid RequestedTheme="Dark">
    <Button Content="Button" Click="Button_Click" Style="{ThemeResource ButtonRevealStyle}"/>
</Grid>
```

Или измените параметр TargetTheme элемента RevealBorderBrush на "Темная". Помните: если TargetTheme имеет значение "Темная", эффект отображения будет белым, но если тема светлая, границы эффекта отображения будут серыми.

```xaml
 <RevealBorderBrush x:Key="MyLightBorderBrush" TargetTheme="Dark" Color="{ThemeResource SystemAccentColor}" FallbackColor="{ThemeResource SystemAccentColor}" />
```

## <a name="enabling-reveal-on-custom-controls"></a>Включение эффекта отображения для пользовательских элементов управления

Можно добавить эффект отображения в пользовательские элементы управления Перед выполнением этого полезно знать чуть больше о том, как работает эффект. Эффект отображения состоит из двух отдельных эффектов: **Эффект отображения границы** и **Эффект отображения при наведении**.

- **Эффект отображения границы** показывает границы интерактивных элементов, когда указатель рядом. Этот эффект показывает, что такие соседние объекты могут выполнять действия аналогично элементу в фокусе.
- **Эффект отображения при наведении** добавляет легкий ореол вокруг элемента в фокусе или элемента, на который наведен указатель, и воспроизводит анимацию нажатия при щелчке. 

![Слои отображения](images/RevealLayers.png)

<!-- The Reveal recipe breakdown is:

- Border reveal will be on top of all content but on the designated edges
- Text and content will be displayed directly under Border Reveal
- Hover reveal will be beneath content and text
- The backplate (that turns on and enables Hover Reveal)
- The background (background of control) -->


Эти эффекты определяются двумя кистями: 
* Отображение границ определяется **RevealBorderBrush**
* Отображение при наведении определяется **RevealBackgroundBrush**

```xaml
<RevealBorderBrush x:Key="MyRevealBorderBrush" TargetTheme="Light" Color="{ThemeResource SystemAccentColor}" FallbackColor="{ThemeResource SystemAccentColor}"/>
<RevealBackgroundBrush x:Key="MyRevealBackgroundBrush" TargetTheme="Light" Color="{StaticResource SystemAccentColor}" FallbackColor="{StaticResource SystemAccentColor}" />
```
В большинстве случаев мы пользуемся обеими этими кистями, включая автоматическое применение эффекта отображения для определенных элементов управления. Однако для включения этого эффекта на других элемента управления необходимо применить стиль или внести изменения в шаблоны этих элементов непосредственно.

### <a name="when-to-add-reveal"></a>Когда следует добавлять эффект отображения
Можно добавить эффект отображения в пользовательские элементы управления, но до этого необходимо учесть тип элемента управления и его поведение. 
* Если пользовательский элемент управления представляет собой отдельный интерактивный элемент и не делит пространство с другими схожими элементами управления (пункты меню в меню, например), велика вероятность, что настраиваемому элементу управления не требуется эффект отображения.  
* Если имеется группа связанных интерактивных элементов или содержимого, велика вероятность, что область вашего приложения действительно требует использовать эффект отображения— как правило, в этом случае он называется [командной поверхностью](https://docs.microsoft.com/windows/uwp/design/controls-and-patterns/collection-commanding).

Так, для отдельной кнопки использовать эффект отображения не следует, а вот для набора кнопок на панели команд— следует.

<!-- For example, NavigationView's items are related to page navigation. CommandBar's buttons relate to menu actions or page feature actions. MediaTransportControl's buttons beneath all relate to the media being played. -->

### <a name="using-the-control-template-to-add-reveal"></a>Использование шаблона управления для добавления эффекта отображения 
Чтобы включить эффект отображения для пользовательских элементов управления или элементов управления на основе шаблонов, необходимо внести изменения в шаблон элемента управления. В основе большинства шаблонов элементов управления лежит сетка; обновите параметр [VisualState](/uwp/api/windows.ui.xaml.visualstate) этой сетки, чтобы использовать эффект отображения:

```xaml
<VisualState x:Name="PointerOver">
    <VisualState.Setters>
        <Setter Target="RootGrid.(RevealBrush.State)" Value="PointerOver" />
        <Setter Target="RootGrid.Background" Value="{ThemeResource ButtonRevealBackgroundPointerOver}" />
        <Setter Target="ContentPresenter.BorderBrush" Value="Transparent"/>
        <Setter Target="ContentPresenter.Foreground" Value="{ThemeResource ButtonForegroundPointerOver}" />
    </VisualState.Setters>
</VisualState>
```

важно отметить, что для правильной работы эффекта отображения в визуальном состоянии (VisualState) требуются и кисть, и методы задания. Если просто задать для кисти элемента управления один из ресурсов кисти эффекта отображения, эффект отображения для этого элемента управления включен не будет. Напротив, если задать только целевые значения или параметры, не указав в качестве значений кисти эффекта отображения, эффект отображения работать не будет.

Чтобы узнать больше об изменении шаблонов элементов управления, см. статью [Шаблоны XAML элементов управления](../controls-and-patterns/control-templates.md).

Мы создали набор системных кистей эффекта отображения, которые можно использовать для настройки вашего шаблона. Например, можно использовать кисть **ButtonRevealBackground** для создания пользовательского фона кнопки или кисть **ListViewItemRevealBackground** для пользовательских списков и т. д. (Подробнее о том, как реализованы ресурсы в XAML, см. статью [Словарь ресурсов Xaml](../controls-and-patterns/resourcedictionary-and-xaml-resource-references.md)).

### <a name="full-template-example"></a>Полный пример шаблона

Далее представлен полный шаблон для кнопки с эффектом отображения:

```xaml
<Style TargetType="Button" x:Key="ButtonStyle1">
    <Setter Property="Background" Value="{ThemeResource ButtonRevealBackground}" />
    <Setter Property="Foreground" Value="{ThemeResource ButtonForeground}" />
    <Setter Property="BorderBrush" Value="{ThemeResource ButtonRevealBorderBrush}" />
    <Setter Property="BorderThickness" Value="2" />
    <Setter Property="Padding" Value="8,4,8,4" />
    <Setter Property="HorizontalAlignment" Value="Left" />
    <Setter Property="VerticalAlignment" Value="Center" />
    <Setter Property="FontFamily" Value="{ThemeResource ContentControlThemeFontFamily}" />
    <Setter Property="FontWeight" Value="Normal" />
    <Setter Property="FontSize" Value="{ThemeResource ControlContentThemeFontSize}" />
    <Setter Property="UseSystemFocusVisuals" Value="True" />
    <Setter Property="FocusVisualMargin" Value="-3" />
    <Setter Property="Template">
        <Setter.Value>
            <ControlTemplate TargetType="Button">
                <Grid x:Name="RootGrid" Background="{TemplateBinding Background}">

                    <VisualStateManager.VisualStateGroups>
                        <VisualStateGroup x:Name="CommonStates">
                            <VisualState x:Name="Normal">

                                <Storyboard>
                                    <PointerUpThemeAnimation Storyboard.TargetName="RootGrid" />
                                </Storyboard>
                            </VisualState>

                            <VisualState x:Name="PointerOver">
                                <VisualState.Setters>
                                    <Setter Target="RootGrid.(RevealBrush.State)" Value="PointerOver" />
                                    <Setter Target="RootGrid.Background" Value="{ThemeResource ButtonRevealBackgroundPointerOver}" />
                                    <Setter Target="ContentPresenter.BorderBrush" Value="Transparent"/>
                                    <Setter Target="ContentPresenter.Foreground" Value="{ThemeResource ButtonForegroundPointerOver}" />
                                </VisualState.Setters>

                                <Storyboard>
                                    <PointerUpThemeAnimation Storyboard.TargetName="RootGrid" />
                                </Storyboard>
                            </VisualState>

                            <VisualState x:Name="Pressed">
                                <VisualState.Setters>
                                    <Setter Target="RootGrid.(RevealBrush.State)" Value="Pressed" />
                                    <Setter Target="RootGrid.Background" Value="{ThemeResource ButtonRevealBackgroundPressed}" />
                                    <Setter Target="ContentPresenter.BorderBrush" Value="{ThemeResource ButtonRevealBackgroundPressed}" />
                                    <Setter Target="ContentPresenter.Foreground" Value="{ThemeResource ButtonForegroundPressed}" />
                                </VisualState.Setters>

                                <Storyboard>
                                    <PointerDownThemeAnimation Storyboard.TargetName="RootGrid" />
                                </Storyboard>
                            </VisualState>

                            <VisualState x:Name="Disabled">
                                <VisualState.Setters>
                                    <Setter Target="RootGrid.Background" Value="{ThemeResource ButtonRevealBackgroundDisabled}" />
                                    <Setter Target="ContentPresenter.BorderBrush" Value="{ThemeResource ButtonRevealBorderBrushDisabled}" />
                                    <Setter Target="ContentPresenter.Foreground" Value="{ThemeResource ButtonForegroundDisabled}" />
                                </VisualState.Setters>
                            </VisualState>
                        </VisualStateGroup>

              </VisualStateManager.VisualStateGroups>
                    <ContentPresenter x:Name="ContentPresenter"
                    BorderBrush="{TemplateBinding BorderBrush}"
                    BorderThickness="{TemplateBinding BorderThickness}"
                    Content="{TemplateBinding Content}"
                    ContentTransitions="{TemplateBinding ContentTransitions}"
                    ContentTemplate="{TemplateBinding ContentTemplate}"
                    Padding="{TemplateBinding Padding}"
                    HorizontalContentAlignment="{TemplateBinding HorizontalContentAlignment}"
                    VerticalContentAlignment="{TemplateBinding VerticalContentAlignment}"
                    AutomationProperties.AccessibilityView="Raw" />
                </Grid>
            </ControlTemplate>
        </Setter.Value>
    </Setter>
</Style>
```

### <a name="fine-tuning-the-reveal-effect-on-a-custom-control"></a>Настройка эффекта отображения для пользовательского элемента управления 

При включить эффект отображения для пользовательских или с измененным элемента управления или пользовательских командных поверхности, эти советы помогут вам оптимизировать эффекта.
 
* На смежных элементах, не схожих по высоте или ширине (особенно в списках): удалите эффект отображения при приближении границы и показывайте границы только при наведении.
* Для элементов управления, которые часто отключаются и включаются: поместите кисть отображения при приближении к границе на основы элементов, а также на их границы, чтобы привлечь внимание к состоянию элемента.
* Для смежных элементов управления, которые касаются друг друга: добавьте поле 1 пиксель между двумя элементами. 

## <a name="dos-and-donts"></a>Рекомендации
### <a name="do"></a>Выполните действия.
- Используйте эффект отображения для элементов, позволяющих пользователю выполнять много действий (панели команд, меню навигации)
- Используйте эффект отображения в группах интерактивных элементов, которые не имеют видимых разделителей по умолчанию (списки, ленты)
- Используйте эффект отображения в областях с высокой плотности интерактивных элементов (сценарии управления)
- Разделите элементы отображения полями по 1 пикселю

### <a name="dont"></a>Не рекомендуется
- Не используйте эффект отображения со статическим содержимым (фон, текст)
- Не используйте эффект отображения во всплывающих окнах, всплывающих элементах или раскрывающихся списках
- Не используйте эффект отображения для однократных, независимых сценариев
- Не используйте эффект отображения для очень больших элементов (более 500 epx)
- Не используйте отображение для элементов, связанных с вопросами безопасности. Это может отвлечь внимание от самого сообщения, которое нужно донести до пользователя.


## <a name="get-the-sample-code"></a>Получить пример кода

- [Образец галереи элементов управления XAML](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/XamlUIBasics) — ознакомьтесь со всеми элементами управления XAML в интерактивном формате.

## <a name="reveal-and-the-fluent-design-system"></a>Эффект отображения и система проектирования Fluent Design

 Система Fluent Design позволяет создавать современные и эффективные пользовательские интерфейсы, которые отличаются яркостью, глубиной, движением, материальностью и масштабированием. Эффект отображения— это компонент системы проектирования Fluent, добавляющий свет в ваше приложение. Дополнительные сведения см. в разделе [Обзор системы проектирования Fluent Design для UWP](../fluent-design-system/index.md).

## <a name="related-articles"></a>Статьи по теме

- [Класс RevealBrush](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.revealbrush)
- [Акрил](acrylic.md)
- [Эффекты композиции](https://msdn.microsoft.com/windows/uwp/graphics/composition-effects)
- [Система проектирования Fluent для UWP](../fluent-design-system/index.md)
- [Наука системы: система проектирования Fluent и глубина](https://medium.com/microsoft-design/science-in-the-system-fluent-design-and-depth-fb6d0f23a53f)
- [Наука системы: система проектирования Fluent и свет](https://medium.com/microsoft-design/the-science-in-the-system-fluent-design-and-light-94a17e0b3a4f)
