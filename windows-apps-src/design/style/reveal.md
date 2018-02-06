---
author: mijacobs
description: "Отображение— это световой эффект, который добавляет приложению глубины и помогает привлечь внимание к интерактивным элементам приложения."
title: "Эффект отображения"
template: detail.hbs
ms.author: mijacobs
ms.date: 08/9/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows10, uwp
pm-contact: kisai
design-contact: conrwi
dev-contact: jevansa
doc-status: Published
ms.localizationpriority: high
ms.openlocfilehash: 8ba0d9939d7ab1d9826ed2848e476499f09c628f
ms.sourcegitcommit: 4b522af988273946414a04fbbd1d7fde40f8ba5e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/08/2018
---
# <a name="reveal-highlight"></a>Эффект отображения

Отображение— это световой эффект, который добавляет приложению глубины и помогает привлечь внимание к интерактивным элементам приложения.

> **Важные API**: [класс RevealBrush](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.revealbrush), [класс RevealBackgroundBrush](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.revealbackgroundbrush), [класс RevealBorderBrush](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.revealborderbrush), [RevealBrushHelper class](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.revealbrushhelper), [класс VisualState](https://docs.microsoft.com/en-us/uwp/api/Windows.UI.Xaml.VisualState)

Поведение отображения достигается за счет выделения реагирующего на щелчок содержимого контейнера, когда указатель находится поблизости от него.

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

## <a name="reveal-and-the-fluent-design-system"></a>Отображение и система проектирования Fluent

 Система Fluent Design позволяет создавать современные и эффективные пользовательские интерфейсы, которые отличаются яркостью, глубиной, движением, материальностью и масштабированием. Эффект отображения— это компонент системы проектирования Fluent, добавляющий свет в ваше приложение. Дополнительные сведения см. в разделе [Обзор системы проектирования Fluent для UWP](../fluent-design-system/index.md).

## <a name="how-to-use-it"></a>Использование

Отображение автоматически работает для некоторых элементов управления. Для других элементов управления можно включить эффект отображения, назначив специальный стиль для элемента управления.

## <a name="controls-that-automatically-use-reveal"></a>Элементы управления, автоматически использующие эффект отображения

- [**ListView**](../controls-and-patterns/lists.md)
- [**GridView**](../controls-and-patterns/lists.md)
- [**TreeView**](../controls-and-patterns/tree-view.md)
- [**NavigationView**](../controls-and-patterns/navigationview.md)
- [**AutosuggestBox**](../controls-and-patterns/auto-suggest-box.md)
- [**MediaTransportControl**](../controls-and-patterns/media-playback.md)
- [**CommandBar**](../controls-and-patterns/app-bars.md)
- [**ComboBox**](../controls-and-patterns/lists.md)

На этих иллюстрациях показан эффект отображения на нескольких различных элементах управления.

![Примеры эффекта отображения](images/RevealExamples_Collage.png)

## <a name="enabling-reveal-on-other-common-controls"></a>Включение эффекта отображения для других распространенных элементов управления

Если вы используете подходящий для отображения сценарий (элементы управления представляют основное содержимое и/или расположены в виде списка или коллекции), вы можете воспользоваться стилями из нашего ресурса, чтобы включить отображение в таком сценарии.

Для этих элементов управления отображение не используется по умолчанию, поскольку они не относятся к основным и обычно выполняют роль вспомогательных элементов управления для центральных пунктов приложения. Однако каждое приложение уникально, и если эти элементы активно используются в вашем приложении, мы подготовили для вас несколько подходящих стилей.

| Название элемента управления   | Имя ресурса |
|----------|:-------------:|
| Button |  ButtonRevealStyle |
| ToggleButton | ToggleButtonRevealStyle |
| RepeatButton | RepeatButtonRevealStyle |
| AppBarButton | AppBarButtonRevealStyle |
| SemanticZoom | SemanticZoomRevealStyle |

Чтобы применить эти стили, просто обновите свойство Style следующим образом:

```XAML
<Button Content="Button Content" Style="{StaticResource ButtonRevealStyle}"/>
```

## <a name="enabling-reveal-on-custom-controls"></a>Включение отображения для настраиваемых элементов управления

При принятии решения об использовании эффекта отображения для того или иного пользовательского элемента управления следует учитывать следующее правило: должна существовать совокупность интерактивных элементов, которые все относятся к общей функции или действию, которое вы хотите выполнить в вашем приложении.

Например элементы NavigationView относятся к навигации по страницам. Кнопки CommandBar связаны с действиями меню или действиями на странице компонента. Кнопки MediaTransportControl связаны с воспроизводимым элементом мультимедиа.

Элементы управления, получающие эффект отображения, могут быть не связаны друг с другом, они лишь должны находиться в зоне высокой плотности и служить для выполнения одной более важной функции.

Чтобы включить эффект отображения для пользовательских элементов управления или элементов с измененным шаблоном, перейдите к настройкам стиля нужного элемента в разделе визуальных состояний его шаблона и укажите эффект отображения в корневой сетке:

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

Важно отметить, что для работы эффекта отображения в визуальном состоянии должны присутствовать как кисть, так и метод задания свойств. Для включения эффекта отображения для элемента управления недостаточно установить один из ресурсов кисти отображения для кисти элемента управления. И наоборот, наличие только целевых объектов или параметров без значений, соответствующих кистям отображения, также не включит эффект отображения.

Мы создали набор системных кистей отображения, которые можно использовать для настройки пользовательского интерфейса. Например, можно использовать кисть **ButtonRevealBackground** для создания пользовательского фона кнопки или кисть **ListViewItemRevealBackground** для пользовательских списков и т. д.

(Дополнительные сведения о работе ресурсов в XAML см. в статье [Словарь ресурсов XAML](../controls-and-patterns/resourcedictionary-and-xaml-resource-references.md).)

### <a name="reveal-on-listview-controls-with-nested-buttons"></a>Эффект отображения для элементов управления ListView с вложенными кнопками

Если у вас есть элемент [ListView](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.ListView) и кнопки или вызываемое содержимое, вложенное в его элементы [ListViewItem](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.listviewitem), необходимо включить эффект отображения для вложенных элементов.

При наличии кнопок или аналогичных кнопкам элементов управления в ListViewItem просто установите для свойства [Style](https://docs.microsoft.com/uwp/api/windows.ui.xaml.frameworkelement#Windows_UI_Xaml_FrameworkElement_Style) элемента управления статический ресурс **ButtonRevealStyle**. 

![Эффект отображения для вложенных элементов](images/NestedListContent.png)

В этом примере включается эффект отображения для нескольких кнопок внутри ListViewItem. 

```XAML
<ListViewItem>
    <StackPanel Orientation="Horizontal">
        <TextBlock Margin="5">Test Text: lorem ipsum.</TextBlock>
        <StackPanel Orientation="Horizontal">
            <Button Content="&#xE71B;" FontFamily="Segoe MDL2 Assets" Width="45" Height="45" Margin="5" Style="{StaticResource ButtonRevealStyle}"/>
            <Button Content="&#xE728;" FontFamily="Segoe MDL2 Assets" Width="45" Height="45" Margin="5" Style="{StaticResource ButtonRevealStyle}"/>
            <Button Content="&#xE74D;" FontFamily="Segoe MDL2 Assets" Width="45" Height="45" Margin="5" Style="{StaticResource ButtonRevealStyle}"/>
         </StackPanel>
    </StackPanel>
</ListViewItem>
```

### <a name="listviewitempresenter-with-reveal"></a>ListViewItemPresenter с эффектом отображения

Списки в XAML немного отличаются, и для включения эффекта отображения потребуется определить диспетчер визуальных состояний для эффекта отображения только внутри ListViewItemPresenter:

```XAML
<ListViewItemPresenter>
<!-- ContentTransitions, SelectedForeground, etc. properties -->
RevealBackground="{ThemeResource ListViewItemRevealBackground}"
RevealBorderThickness="{ThemeResource ListViewItemRevealBorderThemeThickness}"
RevealBorderBrush="{ThemeResource ListViewItemRevealBorderBrush}">
    <VisualStateManager.VisualStateGroups>
        <VisualStateGroup x:Name="CommonStates">
        <VisualState x:Name="Normal" />
        <VisualState x:Name="Selected" />
        <VisualState x:Name="PointerOver">
            <VisualState.Setters>
                <Setter Target="Root.(RevealBrush.State)" Value="PointerOver" />
            </VisualState.Setters>
            </VisualState>
            <VisualState x:Name="PointerOverSelected">
                <VisualState.Setters>
                    <Setter Target="Root.(RevealBrush.State)" Value="PointerOver" />
                </VisualState.Setters>
            </VisualState>
            <VisualState x:Name="PointerOverPressed">
                <VisualState.Setters>
                    <Setter Target="Root.(RevealBrush.State)" Value="Pressed" />
                </VisualState.Setters>
            </VisualState>
            <VisualState x:Name="Pressed">
                <VisualState.Setters>
                    <Setter Target="Root.(RevealBrush.State)" Value="Pressed" />
                </VisualState.Setters>
            </VisualState>
            <VisualState x:Name="PressedSelected">
                <VisualState.Setters>
                    <Setter Target="Root.(RevealBrush.State)" Value="Pressed" />
                </VisualState.Setters>
            </VisualState>
            </VisualStateGroup>
                <VisualStateGroup x:Name="EnabledGroup">
                    <VisualState x:Name="Enabled" />
                    <VisualState x:Name="Disabled">
                        <VisualState.Setters>
                             <Setter Target="Root.RevealBorderThickness" Value="0"/>
                        </VisualState.Setters>
                    </VisualState>
                </VisualStateGroup>
    </VisualStateManager.VisualStateGroups>
</ListViewItemPresenter>
```

Это означает, что необходимо добавить в конец набора свойств в ListViewItemPresenter методы установки значений визуального состояния, связанные с эффектом Reveal.

### <a name="full-template-example"></a>Полный пример шаблона

Далее представлен полный шаблон для кнопки с эффектом отображения:

```XAML
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

## <a name="dos-and-donts"></a>Рекомендации
- Используйте эффект отображения для элементов, позволяющих пользователю выполнить действие (кнопки, элементы выбора).
- Используйте эффект отображения в группах интерактивных элементов, которые не имеют видимых разделителей по умолчанию (списки, панели команд)
- Используйте эффект отображения в областях с высокой плотности интерактивных элементов
- Не используйте эффект отображения со статическим содержимым (фон, текст)
- Не используйте эффект отображения для однократных, независимых сценариев
- Не используйте эффект отображения для очень больших элементов (более 500 epx)
- Не используйте отображение для элементов, связанных с вопросами безопасности. Это может отвлечь внимание от самого сообщения, которое нужно донести до пользователя.

## <a name="how-we-designed-reveal"></a>История создания эффекта отображения

Отображение содержит два визуальных компонента: **отображение при наведении указателя** и **отображение границ**.

![Слои отображения](images/RevealLayers.png)

Отображение при наведении указателя связано непосредственно с содержимым, на которое наводится указатель (или содержимым в фокусе ввода). Вокруг такого элемента появляется легкий ореол, показывающий, что с этим элементом можно взаимодействовать.

Отображение границ применяется к элементу в фокусе и соседним с ним элементам. Это показывает, что такие соседние объекты могут выполнять действия аналогично элементу в фокусе.

Разложив эффект отображения на составляющие, получим:

- отображение границ поверх всего содержимого, но в соответствии с предусмотренными краями;
- текст и содержимое непосредственно под отображаемыми границами;
- слой, обеспечивающий отображение при наведении, под содержимым и текстом;
- основа (активирующая отображение при наведении);
- фон (фон элемента управления).

<!--
<div class=”microsoft-internal-note”>
To create your own Reveal lighting effect for static comps or prototype purposes, see the full [uni design guidance](http://uni/DesignDepot.FrontEnd/#/ProductNav/3020/1/dv/?t=Resources%7CToolkit%7CReveal&f=Neon) for this effect in illustrator.
</div>
-->  

## <a name="get-the-sample-code"></a>Получить пример кода

- [Образец галереи элементов управления XAML](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/XamlUIBasics) — ознакомьтесь со всеми элементами управления XAML в интерактивном формате.

## <a name="related-articles"></a>Еще по теме

- [Класс RevealBrush](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.revealbrush)
- [Акрил](acrylic.md)
- [Эффекты композиции](https://msdn.microsoft.com/windows/uwp/graphics/composition-effects)
- [Система проектирования Fluent для UWP](../fluent-design-system/index.md)
- [Наука системы: система проектирования Fluent и глубина](https://medium.com/microsoft-design/science-in-the-system-fluent-design-and-depth-fb6d0f23a53f)
- [Наука системы: система проектирования Fluent и свет](https://medium.com/microsoft-design/the-science-in-the-system-fluent-design-and-light-94a17e0b3a4f)
