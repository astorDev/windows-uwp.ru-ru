---
description: С помощью стилей вы можете настраивать свойства элементов управления и многократно использовать эти параметры, чтобы придать единообразный внешний вид нескольким элементам управления.
MS-HAID: dev\_ctrl\_layout\_txt.styling\_controls
MSHAttr: PreferredLib:/library/windows/apps
Search.Product: eADQiWindows 10XVcnh
ms.date: 01/03/2019
title: Стили XAML
ms.assetid: AB469A46-FAF5-42D0-9340-948D0EDF4150
label: XAML styles
template: detail.hbs
ms.topic: article
ms.localizationpriority: medium
ms.openlocfilehash: 5a2f73fe6280d3a86510ce48f47017dd4897139a
ms.sourcegitcommit: 3ec8c1d0ea3798cdb2745e7a941a025cf3cf21c8
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/08/2019
ms.locfileid: "67650779"
---
# <a name="xaml-styles"></a>Стили XAML





Платформа XAML предоставляет множество способов настройки внешнего вида приложений. С помощью стилей вы можете настраивать свойства элементов управления и многократно использовать эти параметры, чтобы придать единообразный внешний вид нескольким элементам управления.

## <a name="style-basics"></a>Основные сведения о стилях

С помощью стилей можно превратить параметры визуальных элементов во многократно используемые ресурсы. В следующем примере показаны три кнопки со стилем, задающим свойства [BorderBrush](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.control.borderbrush), [BorderThickness](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.control.borderthickness) и [Foreground](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.control.foreground). Применение стиля позволяет обеспечить единообразный вид элементов управления, не настраивая свойства каждого из них в отдельности

![Кнопки со стилем](images/styles-rainbow-buttons.png)

Стиль можно определить в качестве встроенного для элемента управления на XAML или в качестве многократно используемого ресурса. Ресурсы определяются в XAML-файле отдельной страницы, файле App.xaml или отдельном XAML-файле словаря ресурсов. Несколько приложений могут совместно использовать XAML-файл словаря ресурсов. Одна программа может использовать несколько словарей ресурсов. Область определения ресурсов определяет область их использования. Ресурсы, определенные на уровне страницы, доступны только для этой страницы. Если ресурсы с одинаковым ключом определены одновременно в файле App.xaml и в странице, ресурс страницы переопределяет ресурс в App.xaml. Если ресурс определен в отдельном файле словаря ресурсов, то область его использования зависит от того, на что ссылается словарь ресурсов.

В определении [Style](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Style) требуется атрибут [TargetType](https://docs.microsoft.com/uwp/api/windows.ui.xaml.style.targettype) и коллекция одного или нескольких элементов [Setter](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Setter). Атрибут **TargetType** является строкой, задающей тип [FrameworkElement](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.FrameworkElement), к которому применяется стиль. Значение **TargetType** должно указывать производный от **FrameworkElement** тип, определенный средой выполнения Windows, или пользовательский тип, доступный в ссылаемой сборке. Если вы попытаетесь применить стиль к элементу управления, тип которого не соответствует атрибуту **TargetType** этого стиля, будет выдано исключение.

Для каждого элемента [Setter](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Setter) необходимы параметры [Property](https://docs.microsoft.com/uwp/api/windows.ui.xaml.setter.property) и [Value](https://docs.microsoft.com/uwp/api/windows.ui.xaml.setter.value). Эти параметры свойства показывают, к какому свойству элемента управления применяется параметр, а также значение, задаваемое для этого свойства. Параметр **Setter.Value** можно настроить с помощью синтаксиса атрибута или элемента свойства. В этом примере кода XAML показано, как применить стиль к показанным ранее кнопкам. В этом коде XAML для первых двух элементов **Setter** используется синтаксис атрибута, а в последнем элементе **Setter** (для свойства [BorderBrush](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.control.borderbrush)) — синтаксис элемента свойства. В примере не используется атрибут [x:Key](../../xaml-platform/x-key-attribute.md), и поэтому стиль неявно применяется к кнопкам. Явное и неявное применение стилей поясняется в следующем разделе.

```XAML
<Page.Resources>
    <Style TargetType="Button">
        <Setter Property="BorderThickness" Value="5" />
        <Setter Property="Foreground" Value="Black" />
        <Setter Property="BorderBrush" >
            <Setter.Value>
                <LinearGradientBrush StartPoint="0.5,0" EndPoint="0.5,1">
                    <GradientStop Color="Yellow" Offset="0.0" />
                    <GradientStop Color="Red" Offset="0.25" />
                    <GradientStop Color="Blue" Offset="0.75" />
                    <GradientStop Color="LimeGreen" Offset="1.0" />
                </LinearGradientBrush>
            </Setter.Value>
        </Setter>
    </Style>
</Page.Resources>

<StackPanel Orientation="Horizontal">
    <Button Content="Button"/>
    <Button Content="Button"/>
    <Button Content="Button"/>
</StackPanel>
```

## <a name="apply-an-implicit-or-explicit-style"></a>Применение явного или неявного стиля

Стиль, определенный как ресурс, можно применять к элементам управления двумя способами:

-   неявно, когда указывается только атрибут [TargetType](https://docs.microsoft.com/uwp/api/windows.ui.xaml.style.targettype) для элемента [Style](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Style);
-   явно, когда указываются атрибуты [TargetType](https://docs.microsoft.com/uwp/api/windows.ui.xaml.style.targettype) и [x:Key](../../xaml-platform/x-key-attribute.md) для элемента [Style](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Style), а затем в свойстве [Style](https://docs.microsoft.com/uwp/api/windows.ui.xaml.frameworkelement.style) нужного элемента управления задается ссылка на [расширение разметки {StaticResource}](https://docs.microsoft.com/windows/uwp/xaml-platform/staticresource-markup-extension), которая использует явный ключ.

Если стиль содержит атрибут [x:Key](../../xaml-platform/x-key-attribute.md), то его можно применить к элементу управления только путем задания стиля с ключом в свойстве [Style](https://docs.microsoft.com/uwp/api/windows.ui.xaml.frameworkelement.style) элемента управления. Стиль, не имеющий атрибута x:Key, автоматически применяется к каждому элементу управления целевого типа, если отсутствует явно заданный стиль.

Далее показаны две кнопки с явным и неявным стилем.

![Кнопки с явным и неявным заданием стиля.](images/styles-buttons-implicit-explicit.png)

В этом примере первый стиль содержит атрибут [x:Key](../../xaml-platform/x-key-attribute.md) и имеет тип целевого объекта [Button](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Button). Данный ключ задается в свойстве [Style](https://docs.microsoft.com/uwp/api/windows.ui.xaml.frameworkelement.style) и поэтому стиль применяется явным образом. Второй стиль применяется ко второй кнопке неявно, поскольку он имеет тип целевого объекта **Button**, а в стиле отсутствует атрибут x:Key.

```XAML
<Page.Resources>
    <Style x:Key="PurpleStyle" TargetType="Button">
        <Setter Property="FontFamily" Value="Segoe UI"/>
        <Setter Property="FontSize" Value="14"/>
        <Setter Property="Foreground" Value="Purple"/>
    </Style>

    <Style TargetType="Button">
        <Setter Property="FontFamily" Value="Segoe UI"/>
        <Setter Property="FontSize" Value="14"/>
        <Setter Property="RenderTransform">
            <Setter.Value>
                <RotateTransform Angle="25"/>
            </Setter.Value>
        </Setter>
        <Setter Property="BorderBrush" Value="Green"/>
        <Setter Property="BorderThickness" Value="2"/>
        <Setter Property="Foreground" Value="Green"/>
    </Style>
</Page.Resources>

<Grid x:Name="LayoutRoot">
    <Button Content="Button" Style="{StaticResource PurpleStyle}"/>
    <Button Content="Button"/>
</Grid>
```

## <a name="use-based-on-styles"></a>Использование производных стилей

Для упрощения работы со стилями и оптимизации их многократного использования можно создавать стили, производные от других стилей. Для создания производных стилей служит свойство [BasedOn](https://docs.microsoft.com/uwp/api/windows.ui.xaml.style.basedon). Производные стили должны применяться к элементу управления того же типа, к которому применяется базовый стиль, или к производному элементу управления. Например, если базовый стиль применяется к элементу [ContentControl](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.ContentControl), то основанные на нем стили могут применяться к элементу **ContentControl** или к типам, производным от **ContentControl**, например [Button](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Button) и [ScrollViewer](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.ScrollViewer). Если в производном стиле не задано значение, оно наследуется от базового стиля. Чтобы изменить значение базового стиля, его следует переопределить в производном стиле. В следующем примере показаны классы **Button** и [CheckBox](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.CheckBox) со стилями, производными от одного базового стиля.

![Кнопки с применением производных стилей.](images/styles-buttons-based-on.png)

В базовом стиле целевым типом является [ContentControl](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.ContentControl) и заданы свойства [Height](/uwp/api/Windows.UI.Xaml.FrameworkElement.Height) и [Width](/uwp/api/Windows.UI.Xaml.FrameworkElement.Width). В стилях, основанных на этом стиле, целевыми типами будут [CheckBox](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.CheckBox) и [Button](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Button), производные от **ContentControl**. Производные стили задают новые цвета для свойств [BorderBrush](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.control.borderbrush) и [Foreground](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.control.foreground). (Обычно вы не отображаете рамку вокруг **CheckBox**. Мы делаем это, чтобы продемонстрировать влияние на стиль.)

```XAML
<Page.Resources>
    <Style x:Key="BasicStyle" TargetType="ContentControl">
        <Setter Property="Width" Value="130" />
        <Setter Property="Height" Value="30" />
    </Style>

    <Style x:Key="ButtonStyle" TargetType="Button"
           BasedOn="{StaticResource BasicStyle}">
        <Setter Property="BorderBrush" Value="Orange" />
        <Setter Property="BorderThickness" Value="2" />
        <Setter Property="Foreground" Value="Red" />
    </Style>

    <Style x:Key="CheckBoxStyle" TargetType="CheckBox"
           BasedOn="{StaticResource BasicStyle}">
        <Setter Property="BorderBrush" Value="Blue" />
        <Setter Property="BorderThickness" Value="2" />
        <Setter Property="Foreground" Value="Green" />
    </Style>
</Page.Resources>

<StackPanel>
    <Button Content="Button" Style="{StaticResource ButtonStyle}" Margin="0,10"/>
    <CheckBox Content="CheckBox" Style="{StaticResource CheckBoxStyle}"/>
</StackPanel>
```

## <a name="use-tools-to-work-with-styles-easily"></a>Инструменты для удобной работы со стилями

Чтобы быстро применить стили к элементу управления, щелкните его правой кнопкой мыши в рабочей области конструирования XAML в Microsoft Visual Studio и выберите команду **Изменить стиль** или **Изменить шаблон** (в зависимости от элемента управления). Затем можно применить существующий стиль, выбрав команду **Применить ресурс**, или определить новый стиль командой **Создать пустой**. При создании пустого стиля можно определить его на странице, в файле App.xaml или в отдельном словаре ресурсов.

## <a name="lightweight-styling"></a>Облегченное определение стиля

Переопределение системных кистей обычно выполняется на уровне страницы или приложения, и в обоих случаях переопределение цвета влияет на все элементы управления, ссылающиеся на эту кисть, а в XAML многие элементы управления могут ссылаться на одну и ту же системную кисть.

![Кнопки со стилем](images/LightweightStyling_ButtonStatesExample.png)

```XAML
<Page.Resources>
    <ResourceDictionary>
        <ResourceDictionary.ThemeDictionaries>
            <ResourceDictionary x:Key="Light">
                 <SolidColorBrush x:Key="ButtonBackground" Color="Transparent"/>
                 <SolidColorBrush x:Key="ButtonForeground" Color="MediumSlateBlue"/>
                 <SolidColorBrush x:Key="ButtonBorderBrush" Color="MediumSlateBlue"/>
            </ResourceDictionary>
        </ResourceDictionary.ThemeDictionaries>
    </ResourceDictionary>
</Page.Resources>
```

Для состояний наподобие PointerOver (наведение мыши на кнопку), **PointerPressed** (кнопка вызвана), или Disabled (кнопка не активна). Эти окончания добавляются к исходным именам облегченного оформления: **ButtonBackgroundPointerOver**, **ButtonForegroundPointerPressed**, **ButtonBorderBrushDisabled** и т. д. Изменение и этих кистей гарантирует, что ваши элементы управления раскрашены в соответствии с темой приложения.

Размещение переопределений этих кистей на уровне **App.Resources** изменяет все кнопки в пределах всего приложения, а не на одной странице.

### <a name="per-control-styling"></a>Настройка стиля для элементов управления по отдельности

В других случаях необходимо изменение одного элемента управления на одной странице определенным образом без изменения других версий этого элемента управления:

![Кнопки со стилем](images/LightweightStyling_CheckboxExample.png)

```XAML
<CheckBox Content="Normal CheckBox" Margin="5"/>
<CheckBox Content="Special CheckBox" Margin="5">
    <CheckBox.Resources>
        <ResourceDictionary>
            <ResourceDictionary.ThemeDictionaries>
                <ResourceDictionary x:Key="Light">
                    <SolidColorBrush x:Key="CheckBoxForegroundUnchecked"
                        Color="Purple"/>
                    <SolidColorBrush x:Key="CheckBoxForegroundChecked"
                        Color="Purple"/>
                    <SolidColorBrush x:Key="CheckBoxCheckGlyphForegroundChecked"
                        Color="White"/>
                    <SolidColorBrush x:Key="CheckBoxCheckBackgroundStrokeChecked"  
                        Color="Purple"/>
                    <SolidColorBrush x:Key="CheckBoxCheckBackgroundFillChecked"
                        Color="Purple"/>
                </ResourceDictionary>
            </ResourceDictionary.ThemeDictionaries>
        </ResourceDictionary>
    </CheckBox.Resources>
</CheckBox>
<CheckBox Content="Normal CheckBox" Margin="5"/>
```

Это повлияет только на тот единственный Special CheckBox на странице, где этот элемент управления расположен.

## <a name="modify-the-default-system-styles"></a>Изменение системных стилей, используемых по умолчанию

По возможности следует использовать стили из ресурсов XAML среды выполнения Windows, используемых по умолчанию. Если вам нужно определить собственные стили, старайтесь создавать их на базе стилей по умолчанию (используйте базовые стили, как описано выше, или измените копию исходного стиля по умолчанию).

## <a name="the-template-property"></a>Свойство Template

Вы можете использовать метод задания стиля для свойства [Template](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.control.template) класса [Control](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Control). По сути, сюда входит большинство типовых стилей XAML и ресурсов XAML приложения. Подробные сведения см. в статье [Шаблоны элементов управления](control-templates.md).
