---
description: Подсвечивание — это световой эффект, который добавляет приложению глубины и помогает привлечь внимание к интерактивным элементам приложения.
title: Подсвечивание
template: detail.hbs
ms.date: 08/09/2017
ms.topic: article
keywords: windows 10, uwp
pm-contact: kisai
design-contact: conrwi
dev-contact: jevansa
doc-status: Published
ms.localizationpriority: medium
ms.openlocfilehash: 26e756b52d4faf18eff2fc684c7db94bca058642
ms.sourcegitcommit: 0dee502484df798a0595ac1fe7fb7d0f5a982821
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/08/2020
ms.locfileid: "82971079"
---
# <a name="reveal-highlight"></a>Подсвечивание

![Главное изображение](images/header-reveal-highlight.svg)

Подсвечивание — это световой эффект, выделяющий интерактивные элементы, такие как панели команд, когда пользователь наводит на них указатель. 

> **Важные API**: [класс RevealBrush](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.revealbrush), [класс RevealBackgroundBrush](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.revealbackgroundbrush), [класс RevealBorderBrush](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.revealborderbrush), [RevealBrushHelper class](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.revealbrushhelper), [класс VisualState](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.VisualState).

## <a name="how-it-works"></a>Принцип работы
Подсвечивание привлекает внимание к интерактивным элементам, подсвечивая контейнер элемента, когда указатель находится рядом, как показано на следующем рисунке:

![Наглядное представление подсвечивания](images/Nav_Reveal_Animation.gif)

Показывая скрытые границы вокруг объектов, эффект подсвечивания дает пользователям наглядное представление о пространстве, с которым они взаимодействуют, и доступных действиях. Это особенно важно при работе с элементами управления списками и групп кнопок.

## <a name="examples"></a>Примеры

<table>
<th align="left">XAML Controls Gallery<th>
<tr>
<td><img src="images/xaml-controls-gallery-sm.png" alt="XAML controls gallery"></img></td>
<td>
    <p>Если у вас установлено приложение <strong style="font-weight: semi-bold">галереи элементов управления XAML</strong>, щелкните здесь, чтобы <a href="xamlcontrolsgallery:/item/Reveal">открыть приложение и увидеть эффект подсвечивания в действии</a>.</p>
    <ul>
    <li><a href="https://www.microsoft.com/store/productId/9MSVH128X2ZT">Получить приложение XAML Controls Gallery (Microsoft Store)</a></li>
    <li><a href="https://github.com/Microsoft/Xaml-Controls-Gallery">Получить исходный код (GitHub)</a></li>
    </ul>
</td>
</tr>
</table>

## <a name="video-summary"></a>Краткий видеообзор

> [!VIDEO https://channel9.msdn.com/Events/Windows/Windows-Developer-Day-Fall-Creators-Update/WinDev013/player]

## <a name="how-to-use-it"></a>Использование

Подсвечивание автоматически применяется для некоторых элементов управления. Чтобы включить подсвечивание для других элементов управления, можно назначить им особый стиль, как описано в разделах [Включение подсвечивания для других элементов управления](#enabling-reveal-on-other-controls) и [Включение подсвечивания для пользовательских элементов управления](#enabling-reveal-on-custom-controls) этой статьи.

## <a name="controls-that-automatically-use-reveal"></a>Элементы управления, к которым подсвечивание применяется автоматически

- [**ListView**](../controls-and-patterns/lists.md)
- [**GridView**](../controls-and-patterns/lists.md)
- [**TreeView**](../controls-and-patterns/tree-view.md)
- [**NavigationView**](../controls-and-patterns/navigationview.md)
- [**MediaTransportControl**](../controls-and-patterns/media-playback.md)
- [**CommandBar**](../controls-and-patterns/app-bars.md)

На этих иллюстрациях показан эффект подсвечивания на нескольких различных элементах управления.

![Примеры эффекта подсвечивания](images/RevealExamples_Collage.png)


## <a name="enabling-reveal-on-other-controls"></a>Включение подсвечивания для других элементов управления

Если вы используете подходящий для подсвечивания сценарий (элементы управления представляют основное содержимое и (или) расположены в виде списка или коллекции), вы можете воспользоваться стилями из нашего ресурса, чтобы включить подсвечивание в таком сценарии.

Для этих элементов управления подсвечивание не используется по умолчанию, так как они не относятся к основным и обычно выполняют роль вспомогательных элементов управления для центральных пунктов приложения. Однако каждое приложение уникально, и если эти элементы активно используются в вашем приложении, мы подготовили для вас несколько подходящих стилей.

| Название элемента управления   | Имя ресурса |
|----------|:-------------:|
| Кнопка |  ButtonRevealStyle |
| ToggleButton | ToggleButtonRevealStyle |
| RepeatButton | RepeatButtonRevealStyle |
| AppBarButton | AppBarButtonRevealStyle |
| AppBarToggleButton | AppBarToggleButtonRevealStyle |
| GridViewItem (эффект подсвечивания поверх содержимого) | GridViewItemRevealBackgroundShowsAboveContentStyle |

Чтобы применить эти стили, просто задайте свойство [Style](/uwp/api/Windows.UI.Xaml.Style) элемента управления:

```xaml
<Button Content="Button Content" Style="{ThemeResource ButtonRevealStyle}"/>
```

### <a name="reveal-in-themes"></a>Эффект подсвечивания в темах

Эффект подсвечивания незначительно меняется в зависимости от запрошенной темы элемента управления, приложения или пользовательской настройки. В темной теме граница эффекта подсвечивания и свет при наведении курсора белые, а в светлой теме границы затеняются до светло-серого.

![Эффект подсвечивания в темной и светлой теме](images/Dark_vs_LightReveal.png)

Чтобы включить белые границы в светлой теме, просто задайте для запрошенной темы элемента управления значение Dark.

```xaml
<Grid RequestedTheme="Dark">
    <Button Content="Button" Click="Button_Click" Style="{ThemeResource ButtonRevealStyle}"/>
</Grid>
```

Или измените параметр TargetTheme элемента RevealBorderBrush на Dark. Помните: если TargetTheme имеет значение Dark (Темная), подсвечивание будет белым, но если тема светлая (Light), границы подсвечивания будут серыми.

```xaml
 <RevealBorderBrush x:Key="MyLightBorderBrush" TargetTheme="Dark" Color="{ThemeResource SystemAccentColor}" FallbackColor="{ThemeResource SystemAccentColor}" />
```

## <a name="enabling-reveal-on-custom-controls"></a>Включение подсвечивания для пользовательских элементов управления

Эффект подсвечивания можно добавить в пользовательские элементы управления. До этого полезно знать чуть больше о том, как работает эффект подсвечивания. Подсвечивание состоит из двух отдельных эффектов: **подсвечивание границы** и **подсвечивание при наведении**.

- **Эффект подсвечивания границы** показывает границы интерактивных элементов, когда указатель рядом. Этот эффект показывает, что такие соседние объекты могут выполнять действия аналогично элементу в фокусе.
- **Эффект подсвечивания при наведении** добавляет легкий ореол вокруг элемента в фокусе или элемента, на который наведен указатель, и воспроизводит анимацию нажатия при щелчке. 

![Слои подсвечивания](images/RevealLayers.png)

<!-- The Reveal recipe breakdown is:

- Border reveal will be on top of all content but on the designated edges
- Text and content will be displayed directly under Border Reveal
- Hover reveal will be beneath content and text
- The backplate (that turns on and enables Hover Reveal)
- The background (background of control) -->


Эти эффекты определяются двумя кистями: 
* Эффект подсвечивания границы определяется кистью **RevealBorderBrush**.
* Эффект подсвечивания при наведении определяется кистью **RevealBackgroundBrush**.

```xaml
<RevealBorderBrush x:Key="MyRevealBorderBrush" TargetTheme="Light" Color="{ThemeResource SystemAccentColor}" FallbackColor="{ThemeResource SystemAccentColor}"/>
<RevealBackgroundBrush x:Key="MyRevealBackgroundBrush" TargetTheme="Light" Color="{StaticResource SystemAccentColor}" FallbackColor="{StaticResource SystemAccentColor}" />
```
В большинстве случаев мы пользуемся обеими этими кистями, включая автоматическое применение эффекта подсвечивания для определенных элементов управления. Однако для включения этого эффекта на других элемента управления необходимо применить стиль или внести изменения непосредственно в шаблоны этих элементов.

### <a name="when-to-add-reveal"></a>Когда следует добавлять эффект подсвечивания
Эффект подсвечивания можно добавить в пользовательские элементы управления, но до этого необходимо учесть тип элемента управления и его поведение. 
* Если пользовательский элемент управления представляет собой отдельный интерактивный элемент и не делит пространство с другими схожими элементами управления (пункты меню в меню, например), велика вероятность, что настраиваемому элементу управления не требуется эффект подсвечивания.  
* Если имеется группа связанных интерактивных элементов или содержимого, велика вероятность, что для области вашего приложения требуется использовать эффект подсвечивания — как правило, в этом случае такая область называется [командной поверхностью](https://docs.microsoft.com/windows/uwp/design/controls-and-patterns/collection-commanding).

Так, для отдельной кнопки использовать эффект подсвечивания не следует, а вот для набора кнопок на панели команд — следует.

<!-- For example, NavigationView's items are related to page navigation. CommandBar's buttons relate to menu actions or page feature actions. MediaTransportControl's buttons beneath all relate to the media being played. -->

### <a name="using-the-control-template-to-add-reveal"></a>Использование шаблона управления для добавления эффекта подсвечивания 
Чтобы включить эффект подсвечивания для пользовательских элементов управления или элементов управления на основе шаблонов, необходимо внести изменения в шаблон элемента управления. В основе большинства шаблонов элементов управления лежит сетка. Обновите параметр [VisualState](/uwp/api/windows.ui.xaml.visualstate) этой сетки, чтобы использовать эффект подсвечивания:

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

Важно отметить, что для правильной работы эффекта подсвечивания в визуальном состоянии (VisualState) требуются и кисть, и методы задания. Если просто задать для кисти элемента управления один из ресурсов кисти эффекта подсвечивания, эффект подсвечивания для этого элемента управления включен не будет. Напротив, если задать только целевые значения или параметры, не указав в качестве значений кисти эффекта подсвечивания, эффект подсвечивания работать не будет.

Чтобы узнать больше об изменении шаблонов элементов управления, см. статью о [шаблонах XAML элементов управления](../controls-and-patterns/control-templates.md).

Мы создали набор системных кистей эффекта подсвечивания, которые можно использовать для настройки вашего шаблона. Например, можно использовать кисть **ButtonRevealBackground** для создания пользовательского фона кнопки, кисть **ListViewItemRevealBackground** — для пользовательских списков и т. д. (Подробные сведения о том, как реализованы ресурсы в XAML, см. в статье о [словаре ресурсов XAML](../controls-and-patterns/resourcedictionary-and-xaml-resource-references.md)).

### <a name="full-template-example"></a>Полный пример шаблона

Далее представлен полный шаблон для кнопки с эффектом подсвечивания:

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

### <a name="fine-tuning-the-reveal-effect-on-a-custom-control"></a>Настройка эффекта подсвечивания для пользовательского элемента управления 

При включении эффекта подсвечивания для пользовательского или созданного по шаблону элемента управления или пользовательской командной поверхности оптимизировать эффект помогут следующие советы:
 
* На смежных элементах, не схожих по высоте или ширине (особенно в списках): удалите эффект подсвечивания при приближении к границе и показывайте границы только при наведении.
* Для элементов управления, которые часто отключаются и включаются: поместите кисть подсвечивания при приближении к границе на основы элементов, а также на их границы, чтобы привлечь внимание к состоянию элемента.
* Для смежных элементов управления, которые касаются друг друга: добавьте границу в 1 пиксель между двумя элементами. 

## <a name="dos-and-donts"></a>Что рекомендуется и что не рекомендуется делать
### <a name="do"></a>Рекомендуется
- Используйте эффект подсвечивания для элементов, позволяющих пользователю выполнять много действий (панели команд, меню навигации).
- Используйте эффект подсвечивания в группах интерактивных элементов, которые не имеют видимых разделителей по умолчанию (списки, ленты).
- Используйте эффект подсвечивания в областях с высокой плотностью интерактивных элементов (сценарии управления).
- Разделите элементы подсвечивания границами в 1 пиксель.

### <a name="dont"></a>Не рекомендуется
- Не используйте эффект подсвечивания со статическим содержимым (фон, текст).
- Не используйте эффект подсвечивания во всплывающих окнах, всплывающих элементах или раскрывающихся списках.
- Не используйте эффект подсвечивания для однократных независимых сценариев.
- Не используйте эффект подсвечивания для очень больших элементов (более 500 пикселей).
- Не используйте подсвечивание для элементов, связанных с вопросами безопасности. Это может отвлечь внимание от самого сообщения, которое нужно донести до пользователя.


## <a name="get-the-sample-code"></a>Получение примера кода

- [Пример из коллекции элементов управления XAML](https://github.com/Microsoft/Xaml-Controls-Gallery) — ознакомьтесь со всеми элементами управления XAML в интерактивном режиме.

## <a name="reveal-and-the-fluent-design-system"></a>Эффект подсвечивания и система Fluent Design

 Система Fluent Design позволяет создавать современные и эффективные пользовательские интерфейсы, которые отличаются яркостью, глубиной, движением, материальностью и масштабированием. Эффект подсвечивания — это компонент системы проектирования Fluent, добавляющий свет в ваше приложение. См. сведения о [системе проектирования Fluent Design](/windows/apps/fluent-design-system).

## <a name="related-articles"></a>Похожие статьи

- [Класс RevealBrush](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.revealbrush)
- [Акрил](acrylic.md)
- [Эффекты композиции](https://docs.microsoft.com/windows/uwp/graphics/composition-effects)
- [Fluent Design для UWP](/windows/apps/fluent-design-system)
- [Science in the System: Fluent Design and Depth](https://medium.com/microsoft-design/science-in-the-system-fluent-design-and-depth-fb6d0f23a53f) (Наука системы: глубина в Fluent Design)
- [Science in the System: Fluent Design and Light](https://medium.com/microsoft-design/the-science-in-the-system-fluent-design-and-light-94a17e0b3a4f) (Свет в Fluent Design)
