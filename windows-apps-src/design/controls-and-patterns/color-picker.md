---
Description: Палитра позволяет пользователю просматривать и выбирать цвета.
title: Палитра
label: Color Picker
template: detail.hbs
ms.date: 05/19/2017
ms.topic: article
keywords: windows 10, uwp
pm-contact: kisai
design-contact: ksulliv
dev-contact: llongley
doc-status: Published
ms.localizationpriority: medium
ms.openlocfilehash: f287f738c39e21ea76ff2595cc34ac715a1b52ca
ms.sourcegitcommit: af4050f69168c15b0afaaa8eea66a5ee38b88fed
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/21/2020
ms.locfileid: "80081207"
---
# <a name="color-picker"></a>Палитра

Палитра используется для просмотра и выбора цвета. По умолчанию она позволяет пользователю выбирать цвета из спектра или задать цвет в текстовых полях значений красного, зеленого и синего цветов формате RGB, значения насыщенности и оттенка (HSV) или значения цвета в шестнадцатеричном формате.

![Палитра по умолчанию](images/color-picker-default.png)

**Получение библиотеки пользовательского интерфейса Windows**

|  |  |
| - | - |
| ![Логотип WinUI](images/winui-logo-64x64.png) | Элемент управления **ColorPicker** является частью библиотеки пользовательского интерфейса Windows, пакета NuGet, который содержит новые элементы управления и компоненты пользовательского интерфейса для приложений UWP. Дополнительные сведения, включая инструкции по установке, см. в описании [библиотеки пользовательского интерфейса Windows](https://docs.microsoft.com/uwp/toolkits/winui/). |

> **API-интерфейсы библиотеки пользовательского интерфейса Windows:** [класс ColorPicker](/uwp/api/microsoft.ui.xaml.controls.colorpicker), [свойство Color](/uwp/api/microsoft.ui.xaml.controls.colorpicker.Color), [событие ColorChanged](/uwp/api/microsoft.ui.xaml.controls.colorpicker.ColorChanged)
>
> **API платформы:** [класс ColorPicker](/uwp/api/windows.ui.xaml.controls.colorpicker), [свойство Color](/uwp/api/windows.ui.xaml.controls.colorpicker.Color), [событие ColorChanged](/uwp/api/windows.ui.xaml.controls.colorpicker.ColorChanged)

## <a name="is-this-the-right-control"></a>Выбор правильного элемента управления

Палитра цветов используется, чтобы предоставить пользователю возможность выбора цвета в вашем приложении. Например, используйте ее для изменения параметров цвета, таких как цвет шрифта, фона или цветов темы приложения.

Если ваше приложение для рисования или схожих задач использует перо, рассмотрите возможность использования [элементов управления рукописным вводом](inking-controls.md) вместе с палитрой.

## <a name="examples"></a>Примеры

<table>
<th align="left">XAML Controls Gallery<th>
<tr>
<td><img src="images/xaml-controls-gallery-app-icon-sm.png" alt="XAML controls gallery"></img></td>
<td>
    <p>Если у вас установлено приложение <strong style="font-weight: semi-bold">галереи элементов управления XAML</strong>, щелкните здесь, чтобы <a href="xamlcontrolsgallery:/item/ColorPicker">открыть приложение и увидеть ColorPicker в действии</a>.</p>
    <ul>
    <li><a href="https://www.microsoft.com/store/productId/9MSVH128X2ZT">Получить приложение XAML Controls Gallery (Microsoft Store)</a></li>
    <li><a href="https://github.com/Microsoft/Xaml-Controls-Gallery">Получить исходный код (GitHub)</a></li>
    </ul>
</td>
</tr>
</table>

## <a name="create-a-color-picker"></a>Создание палитры

В этом примере показано, как создать палитру по умолчанию на XAML.

```xaml
<ColorPicker x:Name="myColorPicker"/>
```

По умолчанию палитра отображает предварительное изображение выбранного цвета на прямоугольной панели рядом со спектром цветов. Вы можете использовать событие [ColorChanged](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.colorpicker.ColorChanged) или свойство [Color](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.colorpicker.Color) для доступа к выбранному цвету и применения его в приложении. Ознакомьтесь со следующими примерами, чтобы изучить подробно описанный код.

### <a name="bind-to-the-chosen-color"></a>Привязка к выбранному цвету

Если выбранный цвет должен мгновенно вступить в действие, можно использовать привязку данных для привязки к свойству Color или обработать событие ColorChanged для обращения к выбранному цвету в коде.

В этом примере привязывается свойство Color элемента SolidColorBrush, используемое для заливки прямоугольной области напрямую выбранным на палитре цветом. Любые изменения на палитре в реальном времени изменяют связанное свойство.

```xaml
<ColorPicker x:Name="myColorPicker"
             ColorSpectrumShape="Ring"
             IsColorPreviewVisible="False"
             IsColorChannelTextInputVisible="False"
             IsHexInputVisible="False"/>

<Rectangle Height="50" Width="50">
    <Rectangle.Fill>
        <SolidColorBrush Color="{x:Bind myColorPicker.Color, Mode=OneWay}"/>
    </Rectangle.Fill>
</Rectangle>
```

В этом примере используется упрощенная палитра, содержащая только круг и ползунок. Это упрощенная палитра, используемая во многих приложениях для выбора цвета. Когда изменение цвета можно увидеть и оно применяется к объекту в реальном времени, вам не нужно отображать панель просмотра цвета. В разделе *Настройка палитры* можно получить дополнительную информацию.

### <a name="save-the-chosen-color"></a>Сохранение выбранного цвета

В некоторых случаях не требуется сразу применять изменение цвета. Например, если палитра размещена во всплывающем элементе, мы рекомендуем применять выбранный цвет только после того, как пользователь подтвердит выбор или закроет этот всплывающий элемент. Вы также можете сохранить значение цвета для последующего использования.

В этом примере палитра размещена во всплывающем элементе с кнопками "Confirm" (Подтвердить) и "Cancel" (Отменить). Когда пользователь подтверждает выбор цвета, можно сохранить выбранный цвет для использования в приложении.

```xaml
<Page.Resources>
    <Flyout x:Key="myColorPickerFlyout">
        <RelativePanel>
            <ColorPicker x:Name="myColorPicker"
                         IsColorChannelTextInputVisible="False"
                         IsHexInputVisible="False"/>

            <Grid RelativePanel.Below="myColorPicker"
                  RelativePanel.AlignLeftWithPanel="True"
                  RelativePanel.AlignRightWithPanel="True">
                <Grid.ColumnDefinitions>
                    <ColumnDefinition/>
                    <ColumnDefinition/>
                </Grid.ColumnDefinitions>
                <Button Content="OK" Click="confirmColor_Click"
                        Margin="0,12,2,0" HorizontalAlignment="Stretch"/>
                <Button Content="Cancel" Click="cancelColor_Click"
                        Margin="2,12,0,0" HorizontalAlignment="Stretch"
                        Grid.Column="1"/>
            </Grid>
        </RelativePanel>
    </Flyout>
</Page.Resources>

<Grid Background="{ThemeResource ApplicationPageBackgroundThemeBrush}">
    <Button x:Name="colorPickerButton"
            Content="Pick a color"
            Flyout="{StaticResource myColorPickerFlyout}"/>
</Grid>
```

```csharp
private Color mycolor;

private void confirmColor_Click(object sender, RoutedEventArgs e)
{
    // Assign the selected color to a variable to use outside the popup.
    myColor = myColorPicker.Color;

    // Close the Flyout.
    colorPickerButton.Flyout.Hide();
}

private void cancelColor_Click(object sender, RoutedEventArgs e)
{
    // Close the Flyout.
    colorPickerButton.Flyout.Hide();
}
```

### <a name="configure-the-color-picker"></a>Настройка палитры

Не все поля нужны пользователю для выбора цвета, поэтому возможности палитры гибкие. Она предоставляет множество параметров, которые позволяют настроить этот элемент управления в соответствии со своими потребностями.

Если пользователю не нужна точность в выборе, например, чтобы выбрать цвет маркера в приложении для заметок, можно использовать упрощенный пользовательский интерфейс. Можно скрыть текстовые поля ввода и заменить спектр цветов кругом.

Когда пользователю нужна точность в выборе, например, в приложении для графического дизайна, можно отобразить и ползунки, и текстовые поля для каждого аспекта цвета.

#### <a name="show-the-circle-spectrum"></a>Отображение кругового спектра

В этом примере показано, как использовать свойство [ColorSpectrumShape](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.colorpicker.ColorSpectrumShape), чтобы настроить палитру для отображения кругового спектра вместо квадратного спектра цветов по умолчанию.

```xaml
<ColorPicker x:Name="myColorPicker"
             ColorSpectrumShape="Ring"/>
```

![Палитра с круговым спектром](images/color-picker-ring.png)

Когда необходимо выбрать квадратный или круговой спектр цветов, основную роль в принятии решения играет точность. Пользователь может точнее выбрать цвет на квадратном спектре, так как он отображает более широкую цветовую гамму. Круговой спектр можно считать более простым средством выбора цвета.

#### <a name="show-the-alpha-channel"></a>Отображение альфа-канала

В этом примере вы включаете ползунок непрозрачности и текстовое поле на палитре.

```xaml
<ColorPicker x:Name="myColorPicker"
             IsAlphaEnabled="True"/>
```

![Палитра с IsAlphaEnabled, для которого задано значение true](images/color-picker-alpha.png)

#### <a name="show-a-simple-picker"></a>Отображение простой палитры

В этом примере показано, как настроить палитру с простым пользовательским интерфейсом для простых задач. Вы отображаете круговой спектр и скрываете поля для ввода текста по умолчанию. Когда изменение цвета можно увидеть и оно применяется к объекту в реальном времени, вам не нужно отображать панель просмотра цвета. В противном случае не следует скрывать панель просмотра цвета.

```xaml
<ColorPicker x:Name="myColorPicker"
             ColorSpectrumShape="Ring"
             IsColorPreviewVisible="False"
             IsColorChannelTextInputVisible="False"
             IsHexInputVisible="False"/>
```

![Простая палитра](images/color-picker-casual.png)

#### <a name="show-or-hide-additional-features"></a>Отображение или скрытие дополнительных компонентов

В этой таблице показаны все параметры, которые можно использовать для настройки элемента управления ColorPicker.

Функция | Свойства
--------|-----------
Спектр цветов | IsColorSpectrumVisible, ColorSpectrumShape, ColorSpectrumComponents
Просмотр цвета | IsColorPreviewVisible
Значения цвета| IsColorSliderVisible, IsColorChannelTextInputVisible
Значения непрозрачности | IsAlphaEnabled, IsAlphaSliderVisible, IsAlphaTextInputVisible
Шестнадцатеричные значения | IsHexInputVisible

> [!NOTE]
> Параметр IsAlphaEnabled должн иметь значение **true** для отображения текстового поля непрозрачности и ползунка. Видимость элементов управления для ввода можно изменять с помощью свойств IsAlphaTextInputVisible и IsAlphaSliderVisible. Дополнительные сведения см. в документации по API.

## <a name="dos-and-donts"></a>Что следует и чего не следует делать

- Подумайте о том, какие возможности выбора цвета подойдут для вашего приложения. Для некоторых сценариев может не требоваться точный выбор цвета и будет достаточно использовать упрощенную палитру.
- Чтобы обеспечить наиболее точный выбор цвета, используйте квадратный спектр размером не менее 256x256 пикселей или включите текстовые поля ввода, позволив пользователям уточнить выбранный цвет.
- Если для выбора цвета используется всплывающий элемент, касания спектра или перемещения ползунка должно быть недостаточно, чтобы зафиксировать выбор цвета. Реализуйте следующие возможности фиксации выбора.
  - Предоставьте кнопки фиксации и отмены для применения или отмены выбора. Нажатие кнопки "Назад" или касание вне всплывающего элемента должно закрывать его без сохранения выбора пользователя.
  - Можно также фиксировать выбор после закрытия всплывающего элемента, когда пользователь коснется области за его пределами или нажмет кнопку "Назад".

## <a name="get-the-sample-code"></a>Получение примера кода

- [Пример из коллекции элементов управления XAML](https://github.com/Microsoft/Xaml-Controls-Gallery) — ознакомьтесь со всеми элементами управления XAML в интерактивном режиме.

## <a name="related-articles"></a>Похожие статьи

- [Взаимодействие с помощью пера в приложениях UWP](../input/pen-and-stylus-interactions.md)
- [Рукописный ввод](inking-controls.md)

<!--
<div class="microsoft-internal-note">
<p>
<p>
Note: For more info, see the [color picker redlines](https://uni/DesignDepot.FrontEnd/#/ProductNav/3666/15/dv/?t=Windows%7CControls&f=RS2) on UNI.
</div>
-->