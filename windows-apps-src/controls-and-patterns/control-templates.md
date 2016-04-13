---
Description: Вы можете настроить визуальную структуру и визуальное поведение элемента управления, создав шаблон элемента управления в среде XAML.
MS-HAID: 'dev\_ctrl\_layout\_txt.control\_templates'
MSHAttr: 'PreferredLib:/library/windows/apps'
Search.Product: eADQiWindows 10XVcnh
title: Шаблоны элементов управления
ms.assetid: 6E642626-A1D6-482F-9F7E-DBBA7A071DAD
label: Шаблоны элементов управления
template: detail.hbs
---

# Шаблоны элементов управления

\[ Обновлено для приложений UWP в Windows 10. Статьи, касающиеся Windows 8.x, см. в разделе [Архив](http://go.microsoft.com/fwlink/p/?linkid=619132) \]

**Важные API**

-   [**ControlTemplate class**](https://msdn.microsoft.com/library/windows/apps/br209391)
-   [**Control.Template property**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.control.template.aspx)

Вы можете настроить визуальную структуру и визуальное поведение элемента управления, создав шаблон элемента управления в среде XAML. У элементов управления много свойств, таких как [**Background**](https://msdn.microsoft.com/library/windows/apps/br209395), [**Foreground**](https://msdn.microsoft.com/library/windows/apps/br209414) и [**FontFamily**](https://msdn.microsoft.com/library/windows/apps/br209404), которые можно задать, чтобы указать различные аспекты вида элемента управления. Однако настройка этих свойств позволяет вносить ограниченные изменения. Вы можете указать дополнительные настройки, создав шаблон с помощью класса [**ControlTemplate**](https://msdn.microsoft.com/library/windows/apps/br209391). Здесь мы покажем, как создать **ControlTemplate** для настройки вида элемента управления [**CheckBox**](https://msdn.microsoft.com/library/windows/apps/br209316).

## Пример пользовательского шаблона элемента управления


По умолчанию элемент управления [**CheckBox**](https://msdn.microsoft.com/library/windows/apps/br209316) помещает свое содержимое (строку или объект рядом с **CheckBox**) справа от поля выбора, а флажок указывает, что пользователь выбрал **CheckBox**. Эти характеристики представляют визуальную структуру и визуальное поведение **CheckBox**.

Здесь [**CheckBox**](https://msdn.microsoft.com/library/windows/apps/br209316) использует шаблон по умолчанию [**ControlTemplate**](https://msdn.microsoft.com/library/windows/apps/br209391), показанный в состояниях `Unchecked`, `Checked` и `Indeterminate`.

![Шаблон по умолчанию CheckBox](images/templates-checkbox-states-default.png)

Эти характеристики можно изменить, создав [**ControlTemplate**](https://msdn.microsoft.com/library/windows/apps/br209391) для [**CheckBox**](https://msdn.microsoft.com/library/windows/apps/br209316). Например, если содержимое поля флажка должно быть ниже поля выбора, а для указания того, что пользователь установил флажок, следует использовать **X**. Эти характеристики указываются в **ControlTemplate** элемента управления **CheckBox**.

Чтобы использовать пользовательский шаблон с элементом управления, [**ControlTemplate**](https://msdn.microsoft.com/library/windows/apps/br209391) назначается свойству [**Template**](https://msdn.microsoft.com/library/windows/apps/br209465) элемента управления. Вот [**CheckBox**](https://msdn.microsoft.com/library/windows/apps/br209316), использующий **ControlTemplate**под названием `CheckBoxTemplate1`. Мы покажем код XAML (Extensible Application Markup Language) для **ControlTemplate** в следующем разделе.

```XAML
<CheckBox Content="CheckBox" Template="{StaticResource CheckBoxTemplate1}" IsThreeState="True" Margin="20"/>
```

Вот как этот класс [**CheckBox**](https://msdn.microsoft.com/library/windows/apps/br209316) выглядит в состояниях `Unchecked`, `Checked` и `Indeterminate` после применения нашего шаблона.

![Пользовательский шаблон CheckBox](images/templates-checkbox-states.png)

## Указание визуальной структуры элемента управления


При создании [**ControlTemplate**](https://msdn.microsoft.com/library/windows/apps/br209391) мы объединяем объекты [**FrameworkElement**](https://msdn.microsoft.com/library/windows/apps/br208706), чтобы создать единый элемент управления. У **ControlTemplate** должен быть только один **FrameworkElement** в качестве корневого элемента. Корневой элемент обычно содержит другие объекты **FrameworkElement**. Такое сочетание объектов формирует визуальную структуру элемента управления.

Данный код XAML создает [**ControlTemplate**](https://msdn.microsoft.com/library/windows/apps/br209391) для [**CheckBox**](https://msdn.microsoft.com/library/windows/apps/br209316), указывающий, что содержимое элемента управления находится под полем выбора. Корневой элемент — [**Border**](https://msdn.microsoft.com/library/windows/apps/br209250). Этот пример предлагает [**Path**](https://msdn.microsoft.com/library/windows/apps/br243355) создать **X**, указывающий на то, что пользователь выбрал **CheckBox**, и [**Ellipse**](https://msdn.microsoft.com/library/windows/apps/br243343), указывающий на неопределенное состояние. Обратите внимание, что свойству [**Opacity**](https://msdn.microsoft.com/library/windows/apps/br208962) задано значение 0 в **Path** и **Ellipse**, так что по умолчанию последние не выводятся.

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

## Указание визуального поведения элемента управления


Визуальное поведение описывает внешний вид элемента управления в определенных состояниях. У элемента управления [**CheckBox**](https://msdn.microsoft.com/library/windows/apps/br209316) есть три состояния выставления: `Checked`, `Unchecked` и `Indeterminate`. Значение свойства [**IsChecked**](https://msdn.microsoft.com/library/windows/apps/br209798) определяет состояние **CheckBox**, а состояние последнего определяет, что появляется в поле.

В приведенной ниже таблице перечислены возможные значения [**IsChecked**](https://msdn.microsoft.com/library/windows/apps/br209798), соответствующие состояния [**CheckBox**](https://msdn.microsoft.com/library/windows/apps/br209316) и представление **CheckBox**.

|                     |                    |                         |
|---------------------|--------------------|-------------------------|
| Значение **IsChecked** | Состояние **CheckBox** | Вид **CheckBox** |
| **true**            | `Checked`          | Содержит X.        |
| **false**           | `Unchecked`        | Пусто.                  |
| **null**            | `Indeterminate`    | Содержит кружок.      |

 

Для указания представления элемента управления, когда он находится в определенном состоянии, используйте объекты [**VisualState**](https://msdn.microsoft.com/library/windows/apps/br209007). Объект **VisualState** содержит класс [**Setter**](https://msdn.microsoft.com/library/windows/apps/br208817) или [**Storyboard**](https://msdn.microsoft.com/library/windows/apps/br243053), меняющий представление элементов в классе [**ControlTemplate**](https://msdn.microsoft.com/library/windows/apps/br209391). Когда элемент управления переходит в состояние, указанное в свойстве [**VisualState.Name**](https://msdn.microsoft.com/library/windows/apps/br209031), применяются изменения свойств в **Setter** или [**Storyboard**](https://msdn.microsoft.com/library/windows/apps/br210490). Когда элемент управления выходит из этого состояния, изменения удаляются. Мы добавляем объекты **VisualState** к объектам [**VisualStateGroup**](https://msdn.microsoft.com/library/windows/apps/br209014). Мы добавляем объекты **VisualStateGroup** к подключенному свойству [**VisualStateManager.VisualStateGroups**](https://msdn.microsoft.com/library/windows/apps/hh738505), установленному на корневом элементе [**FrameworkElement**](https://msdn.microsoft.com/library/windows/apps/br208706) шаблона **ControlTemplate**.

Приведенный XAML-код показывает объекты [**VisualState**](https://msdn.microsoft.com/library/windows/apps/br209007) для состояний `Checked`, `Unchecked` и `Indeterminate`. Данный пример задает присоединенное свойство [**VisualStateManager.VisualStateGroups**](https://msdn.microsoft.com/library/windows/apps/hh738505) для [**Border**](https://msdn.microsoft.com/library/windows/apps/br209250), который является корневым элементом [**ControlTemplate**](https://msdn.microsoft.com/library/windows/apps/br209391). `Checked` **VisualState** указывает, что значение [**Opacity**](https://msdn.microsoft.com/library/windows/apps/br208962) у [**Path**](https://msdn.microsoft.com/library/windows/apps/br243355)  с именем `CheckGlyph` (показанного в предыдущем примере) равно 1. `Indeterminate` **VisualState** указывает, что значение **Opacity** у [**Ellipse**](https://msdn.microsoft.com/library/windows/apps/br243343) с именем `IndeterminateGlyph` равно 1. У `Unchecked` **VisualState** нет [**Setter**](https://msdn.microsoft.com/library/windows/apps/br208817) или [**Storyboard**](https://msdn.microsoft.com/library/windows/apps/br210490), поэтому класс [**CheckBox**](https://msdn.microsoft.com/library/windows/apps/br209316) возвращается к своему внешнему виду по умолчанию.

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

Чтобы лучше понять, как работают объекты [**VisualState**](https://msdn.microsoft.com/library/windows/apps/br209007), давайте рассмотрим, что происходит, когда [**CheckBox**](https://msdn.microsoft.com/library/windows/apps/br209316) переходит из состояния `Unchecked` в состояние `Checked`, затем в состояние `Indeterminate` и обратно в состояние `Unchecked`. Вот эти переходы.

|                                      |                                                                                                                                                                                                                                                                                                                                                |                                                   |
|--------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------|
| Смена состояния                     | Что происходит                                                                                                                                                                                                                                                                                                                                   | Представление CheckBox по завершении смены состояния |
| С `Unchecked` на `Checked`.       | Применяется значение [**Setter**](https://msdn.microsoft.com/library/windows/apps/br208817) `Checked` [**VisualState**](https://msdn.microsoft.com/library/windows/apps/br209007), поэтому [**Opacity**](https://msdn.microsoft.com/library/windows/apps/br208962) `CheckGlyph` равно 1.                                                                                                                                                         | Отображается X.                                |
| С `Checked` на `Indeterminate`.   | Применяется значение [**Setter**](https://msdn.microsoft.com/library/windows/apps/br208817) `Indeterminate` [**VisualState**](https://msdn.microsoft.com/library/windows/apps/br209007), поэтому [**Opacity**](https://msdn.microsoft.com/library/windows/apps/br208962) `IndeterminateGlyph` равно 1. Значение **Setter** `Checked` **VisualState** удаляется, поэтому [**Opacity**](https://msdn.microsoft.com/library/windows/apps/br228078) `CheckGlyph`равно 0. | Отображается круг.                            |
| С `Indeterminate` на `Unchecked`. | Значение [**Setter**](https://msdn.microsoft.com/library/windows/apps/br208817) `Indeterminate` [**VisualState**](https://msdn.microsoft.com/library/windows/apps/br209007) удаляется, поэтому [**Opacity**](https://msdn.microsoft.com/library/windows/apps/br208962) `IndeterminateGlyph` равно 0.                                                                                                                                           | Не отображается ничего.                             |

 
Подробнее о том, как создавать визуальные состояния элементов управления, в частности о том, как использовать класс [**Storyboard**](https://msdn.microsoft.com/library/windows/apps/br210490) и типы анимации см. в разделе [Раскадрованные анимации для визуальных состояний](https://msdn.microsoft.com/library/windows/apps/xaml/jj819808).

## Используйте средства для упрощения работы с темами

Чтобы быстро применить темы к своим элементам управления, щелкните элемент управления правой кнопкой мыши в **структуре документа** Microsoft Visual Studio XAML и выберите команду **Изменить тему** или **Изменить стиль** (в зависимости от элемента управления). Затем можно применить существующую тему, выбрав команду **Применить ресурс**, или определить новую командой **Создать пустой**.

## Элементы управления и специальные возможности

При создании нового шаблона для элемента управления помимо возможного изменения его поведения и внешнего вида вы, вероятно, также измените то, как элемент управления заявляет о себе платформам специальных возможностей. Универсальная платформа Windows (UWP) поддерживает специальные возможности для инфраструктуры автоматизации пользовательского интерфейса Майкрософт. Все элементы управления по умолчанию и их шаблоны поддерживают общие типы и схемы элементов управления автоматизацией пользовательского интерфейса, соответствующие назначению и функции элемента управления. Эти типы и схемы элементов управления интерпретируются такими клиентами автоматизации пользовательского интерфейса, как клиенты специальных возможностей. И это обеспечивает доступ к элементу управления в составе более масштабного доступного пользовательского интерфейса приложения.

Чтобы выделить базовую логику элементов управления, а также соответствовать ряду архитектурных требований автоматизации пользовательского интерфейса, классы элементов управления включают поддержку специальных возможностей в отдельном классе, называемом "одноранговый элемент автоматизации". Одноранговые элементы автоматизации иногда взаимодействуют с шаблонами элементов управления, так как эти элементы автоматизации ожидают наличия в шаблонах определенных именованных частей. Благодаря этому доступны такие функции, как включение специальных возможностей при вызове действий кнопок.

Когда вы создаете совершенно новый пользовательский элемент управления, порой бывает необходимо создать помимо этого новый одноранговый элемент автоматизации. Дополнительную информацию см. в разделе [Настраиваемые одноранговые элементы автоматизации](../accessibility/custom-automation-peers.md).

## Дополнительные сведения о стандартном шаблоне элемента управления

Разделы, описывающие стили и шаблоны для элементов управления XAML, показывают выдержки из аналогичных разделов краткого руководства по XAML, которые вы бы увидели, если бы использовали команды **Изменить тему** или **Изменить стиль**, описанные ранее. Каждый раздел перечисляет имена визуальных состояний, использованные ресурсы тем, а также полный код XAML для стиля, содержащего шаблон. Разделы могут стать полезным руководством, если вы уже начали изменение шаблона и хотите посмотреть, как выглядит первоначальный шаблон, либо хотите проверить, имеет ли новый шаблон все необходимые именованные визуальные состояния.

## Ресурсы тем в шаблонах элементов управления

В некоторых атрибутах в примерах кода XAML вы могли заметить ссылки на ресурсы, которые используют [расширение разметки {ThemeResource}](../xaml-platform/themeresource-markup-extension.md). Данная технология позволяет одному шаблону элемента управления использовать разнообразные ресурсы в зависимости от того, какая из тем активна на текущий момент. Это особенно важно для кистей и цветов, поскольку основная цель тем — предоставить пользователям возможность выбора темной или светлой темы либо темы "Высокая контрастность" в применении к системе в целом. Приложения, которые используют систему на базе ресурсов XAML, могут использовать набор ресурсов, соответствующий определенной теме. Выбор темы в пользовательском интерфейсе приложения будет отражать выбор пользователем темы для всей системы в целом.

**Примечание.**  
Эта статья адресована разработчикам приложений UWP для Windows 10. В случае разработки приложений для Windows 8.x или Windows Phone 8.x см. раздел [архивной документации](http://go.microsoft.com/fwlink/p/?linkid=619132).

 

 

 





<!--HONumber=Mar16_HO1-->


