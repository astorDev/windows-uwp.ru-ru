---
author: Jwmsft
Description: A color picker lets a user browse through and select colors.
title: Палитра
label: Color Picker
template: detail.hbs
ms.author: jimwalk
ms.date: 05/19/2017
ms.topic: article
keywords: windows 10, uwp
pm-contact: kisai
design-contact: ksulliv
dev-contact: llongley
doc-status: Published
ms.localizationpriority: medium
ms.openlocfilehash: f2b6270fcd7bc3dcf45d6e80dd547ee783d8e9ae
ms.sourcegitcommit: 38f06f1714334273d865935d9afb80efffe97a17
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/09/2018
ms.locfileid: "6195613"
---
# <a name="color-picker"></a>Палитра

Палитра используется для просмотра и выбрать цвета. По умолчанию он позволяет пользователю перемещаться по цветов на спектр цветов или задать цвет в текстовые поля, красный, зеленый, синий (RGB), значение насыщенности оттенок (HSV) или шестнадцатеричном формате.

> **Важные API-интерфейсы**: [класс ColorPicker](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.colorpicker), [свойство Color](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.colorpicker.Color), [событие ColorChanged](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.colorpicker.ColorChanged)

![Палитра по умолчанию](images/color-picker-default.png)


## <a name="is-this-the-right-control"></a>Выбор правильного элемента управления

Палитра цветов, чтобы предоставить пользователю возможность выбора цвета в вашем приложении. Например используете его для изменения цвета параметры, такие как цвет шрифта, фона или цвета темы приложения.

Если ваше приложение для рисования или других задач, с помощью пера, рассмотрите возможность использования [рукописный ввод элементов управления](inking-controls.md) вместе с палитра.

## <a name="examples"></a>Примеры.

<table>
<th align="left">Галерея элементов управления XAML<th>
<tr>
<td><img src="images/xaml-controls-gallery-sm.png" alt="XAML controls gallery"></img></td>
<td>
    <p>Если у вас установлено приложение <strong style="font-weight: semi-bold">галереи элементов управления XAML</strong>, щелкните здесь, чтобы <a href="xamlcontrolsgallery:/item/ColorPicker">открыть приложение и увидеть ColorPicker в действии</a>.</p>
    <ul>
    <li><a href="https://www.microsoft.com/store/productId/9MSVH128X2ZT">Скачать приложение галереи элементов управления XAML (Microsoft Store)</a></li>
    <li><a href="https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/XamlUIBasics">Получить исходный код (GitHub)</a></li>
    </ul>
</td>
</tr>
</table>

## <a name="create-a-color-picker"></a>Создание палитры

В этом примере показано, как создать элемент выбора цвета по умолчанию в XAML.

```xaml
<ColorPicker x:Name="myColorPicker"/>
```

По умолчанию палитра отображается предварительный просмотр выбранный цвет на прямоугольную панели рядом с спектра цветов. Вы можете использовать [ColorChanged](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.colorpicker.ColorChanged) событий или [цвет](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.colorpicker.Color) свойство для доступа к выбранный цвет и использовать его в приложении. См. следующие примеры для подробного кода.

### <a name="bind-to-the-chosen-color"></a>Привязка к выбранный цвет

После выбора цвета должны вступают в силу немедленно, можно использовать привязку данных для привязки к свойству, цвет или обработать событие ColorChanged для доступа к в выбранном цвете в коде.

В этом примере привязать свойство цвета SolidColorBrush, используемого в качестве заливки прямоугольника непосредственно в выбранной цветовой палитры цветов. Все изменения, палитра результатов в режиме реального времени измените связанных свойств.

```xaml
<ColorPicker x:Name="myColorPicker"
             ColorSpectrumShape=”Ring”
             IsColorPreviewVisible="False"
             IsColorChannelTextInputVisible="False"
             IsHexInputVisible="False"/>

<Rectangle Height="50" Width="50">
    <Rectangle.Fill>
        <SolidColorBrush Color="{x:Bind myColorPicker.Color, Mode=OneWay}"/>
    </Rectangle.Fill>
</Rectangle>
```

В этом примере использует упрощенный палитра с просто круг, а также ползунок, Общее взаимодействие комплектации «несложных» цвет, используемый. Когда можно увидеть изменение цвета и происходит в в режиме реального времени на затронутых объектов, вам не нужно отображать панель просмотра цвет. В разделе *Настройка палитра* дополнительную информацию.

### <a name="save-the-chosen-color"></a>Сохранить выбранный цвет

В некоторых случаях вы не хотите сразу применить цвет. Например при размещении палитра в всплывающий элемент, мы рекомендуем использовать выбранный цвет только после пользователь подтверждает Выбор или закрывает всплывающий элемент. Вы также можете сохранить значение цвета для последующего использования.

В этом примере узел палитра во всплывающем с кнопками подтверждение и "Отмена". Когда пользователь подтверждает Выбор цвета, можно сохранить выбранный цвет для использования в приложении.

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

Не все поля, необходимо дать пользователю возможность выбрать цвет, то гибкий палитра. Он предоставляет множество параметров, которые позволяют настроить элемент управления в соответствии с потребностями.

Например если пользователь не нужны точного управления, например выбора цвет выделения в заметки и приложения, можно использовать упрощенный пользовательский Интерфейс. Можно скрыть поля ввода текста и изменить спектр цветов для круг.

Когда пользователю требуется точного управления, например в приложении Графический дизайн можно показать ползунки и текстовых полей для каждым аспектом цвет.

#### <a name="show-the-circle-spectrum"></a>Показать спектра круг

В этом примере показано, как использовать [ColorSpectrumShape](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.colorpicker.ColorSpectrumShape) для настройки палитры цветов для использования циклических спектра вместо квадрат по умолчанию.

```xaml
<ColorPicker x:Name="myColorPicker"
             ColorSpectrumShape="Ring"/>
```

![Палитра с спектра круг](images/color-picker-ring.png)

Необходимо выбрать квадрат и круг спектра цветов, основное внимание при точности. Пользователь имеет больший контроль при выборе определенных цвет, с помощью квадрат со стороной, поскольку больше цветовую гамму отображается. Следует учитывать спектра круг больше «несложных» Выбор взаимодействие цвета.

#### <a name="show-the-alpha-channel"></a>Показать альфа-канала

В этом примере включить ползунок непрозрачности и textbox на палитра.

```xaml
<ColorPicker x:Name="myColorPicker"
             IsAlphaEnabled="True"/>
```

![Палитра с IsAlphaEnabled задано значение true](images/color-picker-alpha.png)

#### <a name="show-a-simple-picker"></a>Показать простые средства выбора

В этом примере показано, как настроить окно выбора цвета с помощью простого пользовательского интерфейса для использования в «обычное». Циклические спектра показа и скрытия поля для ввода текста по умолчанию. Когда можно увидеть изменение цвета и происходит в в режиме реального времени на затронутых объектов, вам не нужно отображать панель просмотра цвет. В противном случае следует оставить preview цвет видимой.

```xaml
<ColorPicker x:Name="myColorPicker"
             ColorSpectrumShape="Ring"
             IsColorPreviewVisible="False"
             IsColorChannelTextInputVisible="False"
             IsHexInputVisible="False"/>
```

![Простой палитра](images/color-picker-casual.png)

#### <a name="show-or-hide-additional-features"></a>Показать или скрыть дополнительные функции

В этой таблице показаны все параметры, которые можно использовать для настройки управления ColorPicker.

Компонент | Свойства
--------|-----------
Спектр цветов | IsColorSpectrumVisible, ColorSpectrumShape, ColorSpectrumComponents
Цвет предварительного просмотра | IsColorPreviewVisible
Значения цвета| IsColorSliderVisible, IsColorChannelTextInputVisible
Значения непрозрачности | IsAlphaEnabled, IsAlphaSliderVisible, IsAlphaTextInputVisible
Шестнадцатеричные значения | IsHexInputVisible

> [!NOTE]
> Должно быть IsAlphaEnabled **true** для отображения textbox непрозрачности и ползунка. Затем видимость элементов управления для ввода можно изменять с помощью свойств IsAlphaTextInputVisible и IsAlphaSliderVisible. Дополнительные сведения см. в документации по API.

## <a name="dos-and-donts"></a>Что следует и чего не следует делать

- Подумайте о том, какие цвета момент подходящий для вашего приложения. Некоторые сценарии не может потребоваться комплектации детального цвета и получают преимущества от упрощенного выбора
- Для выбора возможности самые точные цвета, используйте квадратные спектра и проследить за его по крайней мере 256x256px, или включить поля ввода текста пользователям уточнить их выбранного цвета.
- При использовании в всплывающий элемент, коснувшись в спектра или переместить ползунок отдельно не следует принять Выбор цвета. Чтобы сохранить выбора:
  - Предоставьте фиксации и отменить кнопки для применения или отменить выбор. Нажав кнопку «Назад» или коснуться за пределами всплывающий элемент будет закройте его и сохраняет выбор пользователя.
  - Или сделайте выбор, закрыв всплывающий элемент, коснувшись вне его области или нажав кнопку "Назад".

## <a name="get-the-sample-code"></a>Получить пример кода

- [Образец галереи элементов управления XAML](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/XamlUIBasics) — ознакомьтесь со всеми элементами управления XAML в интерактивном формате.

## <a name="related-articles"></a>Еще по теме

- [Взаимодействие с помощью пера в приложениях UWP](../input/pen-and-stylus-interactions.md)
- [Рукописный ввод](inking-controls.md)

<!--
<div class=”microsoft-internal-note”>
<p>
<p>
Note: For more info, see the [color picker redlines](http://uni/DesignDepot.FrontEnd/#/ProductNav/3666/15/dv/?t=Windows%7CControls&f=RS2) on UNI.
</div>
-->