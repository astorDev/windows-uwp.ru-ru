---
author: Jwmsft
Description: "Элемент управления Toggle Switch имитирует физический переключатель, позволяющий пользователям включать и выключать что-либо."
title: "Руководство по элементам управления Toggle Switch"
ms.assetid: 753CFEA4-80D3-474C-B4A9-555F872A3DEF
label: Toggle switches
template: detail.hbs
ms.author: jimwalk
ms.date: 05/19/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, uwp
pm-contact: kisai
design-contact: kimsea
dev-contact: mitra
doc-status: Published
ms.openlocfilehash: 7cc1d11035d072fdd52bdfa4a1d0c6e66926ba9f
ms.sourcegitcommit: 10d6736a0827fe813c3c6e8d26d67b20ff110f6c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/22/2017
---
# <a name="toggle-switches"></a>Тумблеры
<link rel="stylesheet" href="https://az835927.vo.msecnd.net/sites/uwp/Resources/css/custom.css">

Элемент управления [toggle switch](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.toggleswitch.aspx) имитирует физический переключатель, позволяющий пользователям включать и выключать что-либо подобно переключателю. С помощью элементов управления переключателя пользователи могут выбрать один из взаимоисключающих вариантов выбора (например, "включено/выключено"). Результат выбора применяется сразу же. 

Чтобы создать элемент управления переключателем, используйте [класс ToggleSwitch](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.toggleswitch.aspx).

> **Важные API-интерфейсы**: [класс ToggleSwitch](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.toggleswitch.aspx), [свойство IsOn](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.toggleswitch.ison.aspx), [событие Toggled](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.toggleswitch.toggled.aspx)


## <a name="is-this-the-right-control"></a>Выбор правильного элемента управления

Используйте переключатель для бинарных операций, выполняющихся сразу после изменения состояния переключателя.

![Переключатель Wi-Fi, включение и отключение](images/toggleswitches01.png)

Воспринимайте переключатель как физический переключатель питания на устройстве: вы включаете или выключаете его, если требуется включить или отключить действие, выполняемое устройством.

Чтобы облегчить понимание переключателя, пометьте его одним или двумя словами, предпочтительно существительными, описывающими функции, которыми он управляет. Например, "WiFi" или "Kitchen lights."  


### <a name="choosing-between-toggle-switch-and-check-box"></a>Выбор между переключателем и флажком

Для некоторых действий можно использовать и переключатель, и флажок. Чтобы выбрать наиболее подходящий элемент управления, следуйте указанным ниже советам.

-   Используйте переключатель для параметров с двумя значениями, когда изменения сразу вступают в силу.

    ![Сравнение переключателя и флажка](images/toggleswitches02.png)

    В этом примере (в котором использован тумблер) понятно, что беспроводная связь включена. При использовании флажка пользователю придется задуматься, включена ли беспроводная связь или необходимо установить флажок, чтобы включить ее.

-   Флажки используются для выбора дополнительных элементов ("желательных"). 
-   Используйте флажок, когда пользователю необходимо выполнить дополнительные действия, чтобы изменения вступили в силу. Например, если пользователю необходимо нажать кнопку "Отправить" или "Далее", чтобы применить изменения, используйте флажок.
-   Используйте флажки, когда пользователь может выбрать несколько элементов, которые связаны с одним и тем же параметром или функцией. 

## <a name="toggle-switches-in-the-the-windows-ui"></a>Переключение тумблеров в пользовательском интерфейсе Windows

На этих изображениях показано, как пользовательский интерфейс Windows использует переключатели. Ниже показано, как переключатели используются на экране параметров смарт-хранилища:

![Тумблеры в смарт-хранилище](images/SmartStorageToggle.png)

Этот пример взят со страницы параметров ночного света:

![Тумблеры в параметрах меню "Пуск" в Windows.](images/NightLightToggle.png)

## <a name="create-a-toggle-switch"></a>Создание тумблера

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

### <a name="ison"></a>IsOn

Переключатель может быть в состоянии "включено" или "выключено". Используйте свойство [IsOn](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.toggleswitch.ison.aspx) для определения состояния переключателя. Если переключатель используется для управления другим двоичным свойством, можно использовать привязку, как показано ниже.

```
<StackPanel Orientation="Horizontal">
    <ToggleSwitch x:Name="ToggleSwitch1" IsOn="True"/>
    <ProgressRing IsActive="{x:Bind ToggleSwitch1.IsOn, Mode=OneWay}" Width="130"/>
</StackPanel>
```

### <a name="toggled"></a>Toggled

В других случаях можно обработать событие [Toggled](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.toggleswitch.toggled.aspx), чтобы оно реагировало на изменения в состоянии.

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

### <a name="onoff-labels"></a>Метки "Вкл."/"Выкл."

По умолчанию тумблер включает буквальные метки "Вкл."/"Выкл.", которые локализуются автоматически. Вы можете изменить эти метки, установив свойства [OnContent](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.toggleswitch.oncontent.aspx) и [OffContent](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.toggleswitch.offcontent.aspx).

В этом примере показано, как заменить метки "Вкл."/"Выкл." на метки "Показать"/"Скрыть".  

```xaml
<ToggleSwitch x:Name="imageToggle" Header="Show images"
              OffContent="Show" OnContent="Hide"
              Toggled="ToggleSwitch_Toggled"/>
```

Вы также можете использовать более сложное содержимое, установив свойства [OnContentTemplate](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.toggleswitch.oncontenttemplate.aspx) и [OffContentTemplate](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.toggleswitch.offcontenttemplate.aspx).

## <a name="recommendations"></a>Рекомендации

-    Используйте метки включения и выключения по умолчанию, когда можно. Только заменяйте их при необходимости для нормальной работы переключателя. Если вы заменяете их, используйте одно слово, более точно описывающее переключатель. Как правило, если слова "Вкл." и "Выкл." не описывают действие, привязанное к переключателю, вам может потребоваться другой элемент управления.
-    Не заменяйте метки "Вкл." и "Выкл." без необходимости; оставьте метки по умолчанию, если ситуация не требует пользовательских меток.


## <a name="related-articles"></a>Еще по теме

- [Класс ToggleSwitch](https://msdn.microsoft.com/library/windows/apps/hh701411)
- [Переключатели](radio-button.md)
- [Тумблеры](toggles.md)
- [Флажки](checkbox.md)
