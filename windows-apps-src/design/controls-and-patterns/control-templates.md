---
Description: Вы можете настроить визуальную структуру и визуальное поведение элемента управления, создав шаблон элемента управления в среде XAML.
MS-HAID: dev\_ctrl\_layout\_txt.control\_templates
MSHAttr: PreferredLib:/library/windows/apps
Search.Product: eADQiWindows 10XVcnh
title: Шаблоны элементов управления
ms.assetid: 6E642626-A1D6-482F-9F7E-DBBA7A071DAD
label: Control templates
template: detail.hbs
ms.date: 05/19/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 8ed4515a90df50a1ff8c0d6b82eb8cda5a4a78c9
ms.sourcegitcommit: 0dee502484df798a0595ac1fe7fb7d0f5a982821
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/08/2020
ms.locfileid: "82968749"
---
# <a name="control-templates"></a>Шаблоны элементов управления

Вы можете настроить визуальную структуру и визуальное поведение элемента управления, создав шаблон элемента управления в среде XAML. У элементов управления много свойств, таких как [**Background**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.control.background), [**Foreground**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.control.foreground) и [**FontFamily**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.control.fontfamily), которые можно задать, чтобы указать различные аспекты вида элемента управления. Однако настройка этих свойств позволяет вносить ограниченные изменения. Вы можете указать дополнительные настройки, создав шаблон с помощью класса [**ControlTemplate**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.ControlTemplate). Здесь мы покажем, как создать **ControlTemplate** для настройки вида элемента управления [**CheckBox**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.CheckBox).

> **Важные API**: [**класс ControlTemplate**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.ControlTemplate) и [**свойство Control.Template**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.control.template)

## <a name="custom-control-template-example"></a>Пример пользовательского шаблона элемента управления

По умолчанию элемент управления [**CheckBox**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.CheckBox) помещает свое содержимое (строку или объект рядом с **CheckBox**) справа от поля выбора, а флажок указывает, что пользователь выбрал **CheckBox**. Эти характеристики представляют визуальную структуру и визуальное поведение **CheckBox**.

Здесь [**CheckBox**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.CheckBox) использует шаблон по умолчанию [**ControlTemplate**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.ControlTemplate), показанный в состояниях `Unchecked`, `Checked` и `Indeterminate`.

![Шаблон по умолчанию CheckBox](images/templates-checkbox-states-default.png)

Эти характеристики можно изменить, создав [**ControlTemplate**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.ControlTemplate) для [**CheckBox**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.CheckBox). Например, если содержимое поля флажка должно быть ниже поля выбора, а для указания того, что пользователь установил флажок, следует использовать **X**. Эти характеристики указываются в **ControlTemplate** элемента управления **CheckBox**.

Чтобы использовать пользовательский шаблон с элементом управления, [**ControlTemplate**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.ControlTemplate) назначается свойству [**Template**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.control.template) элемента управления. Вот [**CheckBox**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.CheckBox), использующий **ControlTemplate**под названием `CheckBoxTemplate1`. Мы покажем код XAML (Extensible Application Markup Language) для **ControlTemplate** в следующем разделе.

```XAML
<CheckBox Content="CheckBox" Template="{StaticResource CheckBoxTemplate1}" IsThreeState="True" Margin="20"/>
```

Вот как этот класс [**CheckBox**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.CheckBox) выглядит в состояниях `Unchecked`, `Checked` и `Indeterminate` после применения нашего шаблона.

![Пользовательский шаблон CheckBox](images/templates-checkbox-states.png)

## <a name="specify-the-visual-structure-of-a-control"></a>Указание визуальной структуры элемента управления

При создании [**ControlTemplate**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.ControlTemplate) мы объединяем объекты [**FrameworkElement**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.FrameworkElement), чтобы создать единый элемент управления. У **ControlTemplate** должен быть только один **FrameworkElement** в качестве корневого элемента. Корневой элемент обычно содержит другие объекты **FrameworkElement**. Такое сочетание объектов формирует визуальную структуру элемента управления.

Данный код XAML создает [**ControlTemplate**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.ControlTemplate) для [**CheckBox**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.CheckBox), указывающий, что содержимое элемента управления находится под полем выбора. Корневой элемент — [**Border**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Border). Этот пример предлагает [**Path**](/uwp/api/Windows.UI.Xaml.Shapes.Path) создать **X**, указывающий на то, что пользователь выбрал **CheckBox**, и [**Ellipse**](/uwp/api/Windows.UI.Xaml.Shapes.Ellipse), указывающий на неопределенное состояние. Обратите внимание, что свойству [**Opacity**](/uwp/api/Windows.UI.Xaml.UIElement.Opacity) задано значение 0 в **Path** и **Ellipse**, так что по умолчанию последние не выводятся.

[TemplateBinding](../../xaml-platform/templatebinding-markup-extension.md) связывает значение свойства в шаблоне элемента управления и значение какого-либо другого предоставленного свойства элемента управления-шаблона. TemplateBinding может использоваться только в пределах определения ControlTemplate в XAML. См. подробнее в разделе [Расширение разметки TemplateBinding](../../xaml-platform/templatebinding-markup-extension.md).

> [!NOTE]
> Начиная с Windows 10, версия 1809 ([SDK 17763](https://developer.microsoft.com/windows/downloads/windows-10-sdk)), вы можете использовать расширения разметки [**x:Bind**](https://docs.microsoft.com/windows/uwp/xaml-platform/x-bind-markup-extension) вместе с [TemplateBinding](../../xaml-platform/templatebinding-markup-extension.md). См. подробнее в разделе [Расширение разметки TemplateBinding](../../xaml-platform/templatebinding-markup-extension.md).

```XAML
<ControlTemplate x:Key="CheckBoxTemplate1" TargetType="CheckBox">
    <Border BorderBrush="{TemplateBinding BorderBrush}"
            BorderThickness="{TemplateBinding BorderThickness}"
            Background="{TemplateBinding Background}">
        <Grid>
            <Grid.RowDefinitions>
                <RowDefinition Height="*"/>
                <RowDefinition Height="25"/>
            </Grid.RowDefinitions>
            <Rectangle x:Name="NormalRectangle" Fill="Transparent" Height="20" Width="20"
                       Stroke="{ThemeResource SystemControlForegroundBaseMediumHighBrush}"
                       StrokeThickness="{ThemeResource CheckBoxBorderThemeThickness}"
                       UseLayoutRounding="False"/>
            <!-- Create an X to indicate that the CheckBox is selected. -->
            <Path x:Name="CheckGlyph"
                  Data="M103,240 L111,240 119,248 127,240 135,240 123,252 135,264 127,264 119,257 111,264 103,264 114,252 z"
                  Fill="{ThemeResource CheckBoxForegroundThemeBrush}"
                  FlowDirection="LeftToRight"
                  Height="14" Width="16" Opacity="0" Stretch="Fill"/>
            <Ellipse x:Name="IndeterminateGlyph"
                     Fill="{ThemeResource CheckBoxForegroundThemeBrush}"
                     Height="8" Width="8" Opacity="0" UseLayoutRounding="False" />
            <ContentPresenter x:Name="ContentPresenter"
                              ContentTemplate="{TemplateBinding ContentTemplate}"
                              Content="{TemplateBinding Content}"
                              Margin="{TemplateBinding Padding}" Grid.Row="1"
                              HorizontalAlignment="Center"
                              VerticalAlignment="{TemplateBinding VerticalContentAlignment}"/>
        </Grid>
    </Border>
</ControlTemplate>
```

## <a name="specify-the-visual-behavior-of-a-control"></a>Указание визуального поведения элемента управления

Визуальное поведение описывает внешний вид элемента управления в определенных состояниях. У элемента управления [**CheckBox**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.CheckBox) есть три состояния выставления: `Checked`, `Unchecked` и `Indeterminate`. Значение свойства [**IsChecked**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.primitives.togglebutton.ischecked) определяет состояние **CheckBox**, а состояние последнего определяет, что появляется в поле.

В приведенной ниже таблице перечислены возможные значения [**IsChecked**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.primitives.togglebutton.ischecked), соответствующие состояния [**CheckBox**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.CheckBox) и представление **CheckBox**.

|                     |                    |                         |
|---------------------|--------------------|-------------------------|
| Значение **IsChecked** | Состояние **CheckBox** | Вид **CheckBox** |
| **true**            | `Checked`          | Содержит X.        |
| **false**           | `Unchecked`        | Пусто.                  |
| **null**            | `Indeterminate`    | Содержит кружок.      |


Для указания представления элемента управления, когда он находится в определенном состоянии, используйте объекты [**VisualState**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.VisualState). Объект **VisualState** содержит класс [**Setter**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Setter) или [**Storyboard**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.Animation.BeginStoryboard), меняющий представление элементов в классе [**ControlTemplate**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.ControlTemplate). Когда элемент управления переходит в состояние, указанное в свойстве [**VisualState.Name**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.visualstate.name), применяются изменения свойств в **Setter** или [**Storyboard**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.Animation.Storyboard). Когда элемент управления выходит из этого состояния, изменения удаляются. Мы добавляем объекты **VisualState** к объектам [**VisualStateGroup**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.VisualStateGroup). Мы добавляем объекты **VisualStateGroup** к подключенному свойству [**VisualStateManager.VisualStateGroups**](https://docs.microsoft.com/dotnet/api/system.windows.visualstatemanager), установленному на корневом элементе [**FrameworkElement**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.FrameworkElement) шаблона **ControlTemplate**.

Приведенный XAML-код показывает объекты [**VisualState**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.VisualState) для состояний `Checked`, `Unchecked` и `Indeterminate`. Данный пример задает присоединенное свойство [**VisualStateManager.VisualStateGroups**](https://docs.microsoft.com/dotnet/api/system.windows.visualstatemanager) для [**Border**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Border), который является корневым элементом [**ControlTemplate**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.ControlTemplate). **VisualState** `Checked` указывает, что значение [**Opacity**](/uwp/api/Windows.UI.Xaml.UIElement.Opacity) для [**Path**](/uwp/api/Windows.UI.Xaml.Shapes.Path) с именем `CheckGlyph` (см. предыдущий пример) равно 1. **VisualState** `Indeterminate` указывает, что значение **Opacity** для [**Ellipse**](/uwp/api/Windows.UI.Xaml.Shapes.Ellipse) с именем `IndeterminateGlyph` равно 1. **VisualState** `Unchecked` нет имеет [**Setter**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Setter) или [**Storyboard**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.Animation.Storyboard), поэтому [**CheckBox**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.CheckBox) возвращается к своему внешнему виду по умолчанию.

```XAML
<ControlTemplate x:Key="CheckBoxTemplate1" TargetType="CheckBox">
    <Border BorderBrush="{TemplateBinding BorderBrush}"
            BorderThickness="{TemplateBinding BorderThickness}"
            Background="{TemplateBinding Background}">

        <VisualStateManager.VisualStateGroups>
            <VisualStateGroup x:Name="CheckStates">
                <VisualState x:Name="Checked">
                    <VisualState.Setters>
                        <Setter Target="CheckGlyph.Opacity" Value="1"/>
                    </VisualState.Setters>
                    <!-- This Storyboard is equivalent to the Setter. -->
                    <!--<Storyboard>
                        <DoubleAnimation Duration="0" To="1"
                         Storyboard.TargetName="CheckGlyph" Storyboard.TargetProperty="Opacity"/>
                    </Storyboard>-->
                </VisualState>
                <VisualState x:Name="Unchecked"/>
                <VisualState x:Name="Indeterminate">
                    <VisualState.Setters>
                        <Setter Target="IndeterminateGlyph.Opacity" Value="1"/>
                    </VisualState.Setters>
                    <!-- This Storyboard is equivalent to the Setter. -->
                    <!--<Storyboard>
                        <DoubleAnimation Duration="0" To="1"
                         Storyboard.TargetName="IndeterminateGlyph" Storyboard.TargetProperty="Opacity"/>
                    </Storyboard>-->
                </VisualState>
            </VisualStateGroup>
        </VisualStateManager.VisualStateGroups>

        <Grid>
            <Grid.RowDefinitions>
                <RowDefinition Height="*"/>
                <RowDefinition Height="25"/>
            </Grid.RowDefinitions>
            <Rectangle x:Name="NormalRectangle" Fill="Transparent" Height="20" Width="20"
                       Stroke="{ThemeResource SystemControlForegroundBaseMediumHighBrush}"
                       StrokeThickness="{ThemeResource CheckBoxBorderThemeThickness}"
                       UseLayoutRounding="False"/>
            <!-- Create an X to indicate that the CheckBox is selected. -->
            <Path x:Name="CheckGlyph"
                  Data="M103,240 L111,240 119,248 127,240 135,240 123,252 135,264 127,264 119,257 111,264 103,264 114,252 z"
                  Fill="{ThemeResource CheckBoxForegroundThemeBrush}"
                  FlowDirection="LeftToRight"
                  Height="14" Width="16" Opacity="0" Stretch="Fill"/>
            <Ellipse x:Name="IndeterminateGlyph"
                     Fill="{ThemeResource CheckBoxForegroundThemeBrush}"
                     Height="8" Width="8" Opacity="0" UseLayoutRounding="False" />
            <ContentPresenter x:Name="ContentPresenter"
                              ContentTemplate="{TemplateBinding ContentTemplate}"
                              Content="{TemplateBinding Content}"
                              Margin="{TemplateBinding Padding}" Grid.Row="1"
                              HorizontalAlignment="Center"
                              VerticalAlignment="{TemplateBinding VerticalContentAlignment}"/>
        </Grid>
    </Border>
</ControlTemplate>
```

Чтобы лучше понять, как работают объекты [**VisualState**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.VisualState), давайте рассмотрим, что происходит, когда [**CheckBox**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.CheckBox) переходит из состояния `Unchecked` в состояние `Checked`, затем в состояние `Indeterminate` и обратно в состояние `Unchecked`. Вот эти переходы.

|                                      |                                                                                                                                                                                                                                                                                                                                                |                                                   |
|--------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------|
| Смена состояния                     | Что происходит                                                                                                                                                                                                                                                                                                                                   | Представление CheckBox по завершении смены состояния |
| С `Unchecked` на `Checked`.       | Применяется значение [**Setter**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Setter) [**VisualState**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.VisualState) `Checked`, поэтому значение [**Opacity**](/uwp/api/Windows.UI.Xaml.UIElement.Opacity) для `CheckGlyph` равно 1.                                                                                                                                                         | Отображается X.                                |
| С `Checked` на `Indeterminate`.   | Применяется значение [**Setter**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Setter) [**VisualState**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.VisualState) `Indeterminate`, поэтому значение [**Opacity**](/uwp/api/Windows.UI.Xaml.UIElement.Opacity) для `IndeterminateGlyph` равно 1. Значение **Setter** **VisualState** `Checked` удаляется, поэтому значение [**Opacity**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.brush.opacity) для `CheckGlyph` равно 0. | Отображается круг.                            |
| С `Indeterminate` на `Unchecked`. | Значение [**Setter**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Setter) [**VisualState**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.VisualState) `Indeterminate` удаляется, поэтому значение [**Opacity**](/uwp/api/Windows.UI.Xaml.UIElement.Opacity) для `IndeterminateGlyph` равно 0.                                                                                                                                           | Не отображается ничего.                             |

 
Подробнее о том, как создавать визуальные состояния элементов управления, в частности о том, как использовать класс [**Storyboard**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.Animation.Storyboard) и типы анимации см. в разделе [Раскадрованные анимации для визуальных состояний](https://docs.microsoft.com/previous-versions/windows/apps/jj819808(v=win.10)).

## <a name="use-tools-to-work-with-themes-easily"></a>Используйте средства для упрощения работы с темами

Чтобы быстро применить темы к своим элементам управления, щелкните элемент управления правой кнопкой мыши в **структуре документа** Microsoft Visual Studio XAML и выберите команду **Изменить тему** или **Изменить стиль** (в зависимости от элемента управления). Затем можно применить существующую тему, выбрав команду **Применить ресурс**, или определить новую командой **Создать пустой**.

## <a name="controls-and-accessibility"></a>Элементы управления и специальные возможности

При создании нового шаблона для элемента управления помимо возможного изменения его поведения и внешнего вида вы, вероятно, также измените то, как элемент управления заявляет о себе платформам специальных возможностей. Приложения для Windows поддерживают инфраструктуру автоматизации пользовательского интерфейса Майкрософт для включения специальных возможностей. Все элементы управления по умолчанию и их шаблоны поддерживают общие типы и схемы элементов управления автоматизацией пользовательского интерфейса, соответствующие назначению и функции элемента управления. Эти типы и схемы элементов управления интерпретируются такими клиентами автоматизации пользовательского интерфейса, как клиенты специальных возможностей. И это обеспечивает доступ к элементу управления в составе более масштабного доступного пользовательского интерфейса приложения.

Чтобы выделить базовую логику элементов управления, а также соответствовать ряду архитектурных требований автоматизации пользовательского интерфейса, классы элементов управления включают поддержку специальных возможностей в отдельном классе, называемом "одноранговый элемент автоматизации". Одноранговые элементы автоматизации иногда взаимодействуют с шаблонами элементов управления, так как эти элементы автоматизации ожидают наличия в шаблонах определенных именованных частей. Благодаря этому доступны такие функции, как включение специальных возможностей при вызове действий кнопок.

Когда вы создаете совершенно новый пользовательский элемент управления, порой бывает необходимо создать помимо этого новый одноранговый элемент автоматизации. Дополнительную информацию см. в разделе [Настраиваемые одноранговые элементы автоматизации](../accessibility/custom-automation-peers.md).

## <a name="learn-more-about-a-controls-default-template"></a>Дополнительные сведения о стандартном шаблоне элемента управления

Разделы, описывающие стили и шаблоны для элементов управления XAML, показывают выдержки из аналогичных разделов краткого руководства по XAML, которые вы бы увидели, если бы использовали команды **Изменить тему** или **Изменить стиль**, описанные ранее. Каждый раздел перечисляет имена визуальных состояний, использованные ресурсы тем, а также полный код XAML для стиля, содержащего шаблон. Разделы могут стать полезным руководством, если вы уже начали изменение шаблона и хотите посмотреть, как выглядит первоначальный шаблон, либо хотите проверить, имеет ли новый шаблон все необходимые именованные визуальные состояния.

## <a name="theme-resources-in-control-templates"></a>Ресурсы тем в шаблонах элементов управления

В некоторых атрибутах в примерах кода XAML вы могли заметить ссылки на ресурсы, которые используют [расширение разметки {ThemeResource}](../../xaml-platform/themeresource-markup-extension.md). Данная технология позволяет одному шаблону элемента управления использовать разнообразные ресурсы в зависимости от того, какая из тем активна на текущий момент. Это особенно важно для кистей и цветов, поскольку основная цель тем — предоставить пользователям возможность выбора темной или светлой темы либо темы "Высокая контрастность" в применении к системе в целом. Приложения, которые используют систему на базе ресурсов XAML, могут использовать набор ресурсов, соответствующий определенной теме. Выбор темы в пользовательском интерфейсе приложения будет отражать выбор пользователем темы для всей системы в целом.

## <a name="get-the-sample-code"></a>Получение примера кода

* [Пример коллекции элементов управления XAML](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/XamlUIBasics)
* [Пример пользовательского элемента управления для редактирования текста](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/CustomEditControl)
