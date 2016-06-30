---
author: Jwmsft
Description: "Элемент управления Toggle Switch имитирует физический переключатель, позволяющий пользователям включать и выключать что-либо."
title: "Руководство по элементам управления Toggle Switch"
ms.assetid: 753CFEA4-80D3-474C-B4A9-555F872A3DEF
label: Toggle switches
template: detail.hbs
translationtype: Human Translation
ms.sourcegitcommit: c183f7390c5b4f99cf0f31426c1431066e1bc96d
ms.openlocfilehash: e90760c6894ee5d32ba1063993a703023d23c152

---
# Тумблеры

Элемент управления Toggle Switch имитирует физический переключатель, позволяющий пользователям включать и выключать что-либо. С помощью элементов управления **ToggleSwitch** пользователи могут выбрать один из взаимоисключающих вариантов выбора (например, "включено/выключено"). Результат выбора применяется сразу же.

<span class="sidebar_heading" style="font-weight: bold;">Важные API</span>

-   [**ToggleSwitch class**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.toggleswitch.aspx)
-   [**Свойство IsOn**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.toggleswitch.ison.aspx)
-   [**Событие Toggled**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.toggleswitch.toggled.aspx)

## Выбор правильного элемента управления

Используйте переключатель для бинарных операций, выполняющихся сразу после изменения состояния переключателя. Например, используйте переключатель, чтобы включать или отключать службы или компоненты оборудования, например Wi-Fi.

![Переключатель Wi-Fi, включение и отключение](images/toggleswitches01.png)

Если для действия подошел бы физический переключатель, то лучше использовать элемент управления "Тумблер".

Когда пользователь изменяет положение переключателя, рекомендуется немедленно выполнить соответствующее действие.

### Выбор между переключателем и флажком

Для некоторых действий можно использовать и переключатель, и флажок. Чтобы выбрать наиболее подходящий элемент управления, следуйте указанным ниже советам.

-   Используйте переключатель для параметров с двумя значениями, когда изменения сразу вступают в силу.

    ![Сравнение переключателя и флажка](images/toggleswitches02.png)

    В примере выше (в котором использован тумблер) понятно, что беспроводная связь включена. При использовании флажка пользователю придется задуматься, включена ли беспроводная связь или необходимо установить флажок, чтобы включить ее.

-   Используйте флажок, когда пользователю необходимо выполнить дополнительные действия, чтобы изменения вступили в силу. Например, если пользователю необходимо нажать кнопку "Отправить" или "Далее", чтобы применить изменения, используйте флажок.

    ![Флажок и кнопка “Отправить”](images/submitcheckbox.png)

-   Если у пользователя имеется возможность выбрать несколько элементов, используйте флажки или [список](lists.md).

    ![Флажок для выбора нескольких элементов](images/guidelines_and_checklist_for_toggle_switches_checkbox_multi_select.png)

## Примеры.

Тумблеры в общих параметрах новостного приложения.

![Тумблеры в общих параметрах новостного приложения.](images/control-examples/toggle-switch-news.png)

Тумблеры в параметрах меню "Пуск" в Windows.

![Тумблеры в параметрах меню "Пуск" в Windows.](images/control-examples/toggle-switch-start-settings.png)

## Создание тумблера

Ниже описано, как создать простой тумблер. Этот код XAML создает тумблер для функции Wi-Fi, показанный выше.

```xaml
<ToggleSwitch x:Name="wiFiToggle" Header="Wifi"/>
```
Ниже описано, как создать такой же тумблер в коде.

```csharp
ToggleSwitch wiFiToggle = new ToggleSwitch();
wiFiToggle.Header = "WiFi";

// Add the toggle switch to a parent container in the visual tree.
stackPanel1.Children.Add(wiFiToggle);
```

### IsOn

Переключатель может быть в состоянии "включено" или "выключено". Используйте свойство [**IsOn**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.toggleswitch.ison.aspx) для определения состояния переключателя. Если переключатель используется для управления другим двоичным свойством, можно использовать привязку, как показано ниже.

```
<StackPanel Orientation="Horizontal">
    <ToggleSwitch x:Name="ToggleSwitch1" IsOn="True"/>
    <ProgressRing IsActive="{x:Bind ToggleSwitch1.IsOn, Mode=OneWay}" Width="130"/>
</StackPanel>
```

### Toggled

В других случаях можно обработать событие [**Toggled**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.toggleswitch.toggled.aspx), чтобы оно реагировало на изменения в состоянии.

В данном примере показано, как добавить обработчик события Toggled в XAML и в коде. Событие Toggled обрабатывается, чтобы включить или выключить кольцевой индикатор выполнения и изменить его видимость.

```xaml
<ToggleSwitch x:Name="toggleSwitch1" IsOn="True" 
              Toggled="ToggleSwitch_Toggled"/>
```

Ниже описано, как создать такой же тумблер в коде.

```csharp
// Create a new toggle switch and add a Toggled event handler.
ToggleSwitch toggleSwitch1 = new ToggleSwitch();
toggleSwitch1.Toggled += ToggleSwitch_Toggled;

// Add the toggle switch to a parent container in the visual tree.
stackPanel1.Children.Add(toggleSwitch1);
```

Обработчик для события Toggled.

```csharp
private void ToggleSwitch_Toggled(object sender, RoutedEventArgs e)
{
    ToggleSwitch toggleSwitch = sender as ToggleSwitch;
    if (toggleSwitch != null)
    {
        if (toggleSwitch.IsOn == true)
        {
            progress1.IsActive = true;
            progress1.Visibility = Visibility.Visible;
        }
        else
        {
            progress1.IsActive = false;
            progress1.Visibility = Visibility.Collapsed;
        }
    }
}
```

### Метки "Вкл."/"Выкл."

По умолчанию тумблер включает буквальные метки "Вкл."/"Выкл.", которые локализуются автоматически. Вы можете изменить эти метки, установив свойства [**OnContent**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.toggleswitch.oncontent.aspx) и [**OffContent**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.toggleswitch.offcontent.aspx).

В этом примере показано, как заменить метки "Вкл."/"Выкл." на метки "Показать"/"Скрыть".  

```xaml
<ToggleSwitch x:Name="imageToggle" Header="Show images"
              OffContent="Show" OnContent="Hide" 
              Toggled="ToggleSwitch_Toggled"/>
```

Вы также можете использовать более сложное содержимое, установив свойства [**OnContentTemplate**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.toggleswitch.oncontenttemplate.aspx) и [**OffContentTemplate**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.toggleswitch.offcontenttemplate.aspx).

## Рекомендации

-   Заменяйте метки "Вкл." и "Выкл.", если есть более подходящие метки для параметра. Если есть короткие (3-4 символа) метки, представляющие противоположные состояния, которые больше подходят для конкретного параметра, используйте их. Например, можно использовать метки "Показать"/"Скрыть", если создается тумблер для параметра "Показать изображения". Используйте более конкретные метки. Это помогает при локализации пользовательского интерфейса.
-   Не заменяйте метки "Вкл." и "Выкл." без необходимости; оставьте метки по умолчанию, если ситуация не требует пользовательских меток.
-   Метки должны быть не длиннее 4 символов.

## Связанные статьи

[**ToggleSwitch**](https://msdn.microsoft.com/library/windows/apps/hh701411)
- [Переключатели](radio-button.md)
- [Тумблеры](toggles.md)
- [Флажки](checkbox.md)

**Для разработчиков (XAML)**
- [**ToggleSwitch class**](https://msdn.microsoft.com/library/windows/apps/br209712)



<!--HONumber=Jun16_HO4-->


