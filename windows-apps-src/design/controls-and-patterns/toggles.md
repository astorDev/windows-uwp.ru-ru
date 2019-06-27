---
Description: Элемент управления Toggle Switch имитирует физический переключатель, позволяющий пользователям включать и выключать что-либо.
title: Руководство по элементам управления Toggle Switch
ms.assetid: 753CFEA4-80D3-474C-B4A9-555F872A3DEF
label: Toggle switches
template: detail.hbs
ms.date: 05/19/2017
ms.topic: article
keywords: windows 10, uwp
pm-contact: kisai
design-contact: kimsea
dev-contact: mitra
doc-status: Published
ms.localizationpriority: medium
ms.openlocfilehash: a23a7a7da9605c0ffb5a537c75d63e3f06705881
ms.sourcegitcommit: aaa4b898da5869c064097739cf3dc74c29474691
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "63775150"
---
# <a name="toggle-switches"></a>Тумблеры

Выключатель представляет собой коммутационный аппарат, позволяющий пользователям включать и выключать что-либо, аналогично бытовому выключателю освещения. Элемент управления "Выключатель" предлагает пользователям альтернативу из двух вариантов (включить или выключить) с немедленным результатом выбора.

Для создания элемента управления "Выключатель" используется [класс ToggleSwitch](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.toggleswitch).

> **Важные API**: [класс ToggleSwitch](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.toggleswitch), [свойство IsOn](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.toggleswitch.ison), [событие Toggled](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.toggleswitch.toggled)

## <a name="is-this-the-right-control"></a>Выбор правильного элемента управления

Используйте переключатель для бинарных операций, выполняющихся сразу после изменения состояния переключателя.

![Переключатель Wi-Fi, включение и отключение](images/toggleswitches01.png)

Представляйте себе выключатель как обыкновенный выключатель на устройстве, которым вы щелкаете, чтобы привести устройство в действие или остановить его.

Для наглядности указывайте в названии выключателя (одним-двумя существительными) выполняемые им функции, например Wi-Fi, освещение на кухне. 

## <a name="examples"></a>Примеры

<table>
<th align="left">XAML Controls Gallery<th>
<tr>
<td><img src="images/xaml-controls-gallery-sm.png" alt="XAML controls gallery"></img></td>
<td>
    <p>Если у вас установлено приложение <strong style="font-weight: semi-bold">галереи элементов управления XAML</strong>, щелкните здесь, чтобы открыть приложение и увидеть <a href="xamlcontrolsgallery:/item/ToggleSwitch">ToggleSwitch</a> или <a href="xamlcontrolsgallery:/item/ToggleButton">ToggleButton</a> в действии.</p>
    <ul>
    <li><a href="https://www.microsoft.com/store/productId/9MSVH128X2ZT">Получить приложение XAML Controls Gallery (Microsoft Store)</a></li>
    <li><a href="https://github.com/Microsoft/Xaml-Controls-Gallery">Получить исходный код (GitHub)</a></li>
    </ul>
</td>
</tr>
</table>

## <a name="choosing-between-toggle-switch-and-check-box"></a>Выбор между переключателем и флажком

Для некоторых действий можно использовать и переключатель, и флажок. Чтобы выбрать наиболее подходящий элемент управления, следуйте указанным ниже советам.

- Используйте переключатель для параметров с двумя значениями, когда изменения сразу вступают в силу.

    ![Сравнение переключателя и флажка](images/toggleswitches02.png)

    В этом примере с выключателем сразу понятно, что свет на кухне включен. Но при использовании флажка пользователю придется задуматься, включен ли сейчас свет или необходимо установить флажок, чтобы включить его.

- Используйте флажки для выбора дополнительных элементов.
- Используйте флажок, когда пользователю необходимо выполнить дополнительные действия, чтобы изменения вступили в силу. Например, если пользователю необходимо нажать кнопку "Отправить" или "Далее", чтобы применить изменения, используйте флажок.
- Используйте флажки, когда пользователь может выбрать несколько элементов, которые связаны с одним и тем же параметром или функцией.

## <a name="toggle-switches-in-the-windows-ui"></a>Выключатели в пользовательском интерфейсе Windows

На этих изображениях показано, как используются выключатели в пользовательском интерфейсе Windows. Вот как выключатели используются в диалоговом окне параметров смарт-хранилища:

![Выключатели в параметрах смарт-хранилища](images/SmartStorageToggle.png)

Вот пример со страницы параметров ночного света:

![Тумблеры в параметрах меню "Пуск" в Windows.](images/NightLightToggle.png)

## <a name="create-a-toggle-switch"></a>Создание тумблера

Ниже описано, как создать простой тумблер. Этот код XAML создает выключатель, показанный выше.

```xaml
<ToggleSwitch x:Name="lightToggle" Header="Kitchen Lights"/>
```

Ниже описано, как создать такой же тумблер в коде.

```csharp
ToggleSwitch lightToggle = new ToggleSwitch();
lightToggle.Header = "Kitchen Lights";

// Add the toggle switch to a parent container in the visual tree.
stackPanel1.Children.Add(lightToggle);
```

### <a name="ison"></a>IsOn

Переключатель может быть в состоянии "включено" или "выключено". Используйте свойство [IsOn](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.toggleswitch.ison) для определения состояния выключателя. Если переключатель используется для управления другим двоичным свойством, можно использовать привязку, как показано ниже.

```xaml
<StackPanel Orientation="Horizontal">
    <ToggleSwitch x:Name="ToggleSwitch1" IsOn="True"/>
    <ProgressRing IsActive="{x:Bind ToggleSwitch1.IsOn, Mode=OneWay}" Width="130"/>
</StackPanel>
```

### <a name="toggled"></a>Toggled

В других случаях можно обработать событие [Toggled](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.toggleswitch.toggled), чтобы оно реагировало на изменения в состоянии.

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

По умолчанию тумблер включает буквальные метки "Вкл."/"Выкл.", которые локализуются автоматически. Вы можете изменить эти метки, установив свойства [OnContent](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.toggleswitch.oncontent) и [OffContent](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.toggleswitch.offcontent).

В этом примере показано, как заменить метки "Вкл."/"Выкл." на метки "Показать"/"Скрыть".

```xaml
<ToggleSwitch x:Name="imageToggle" Header="Show images"
              OffContent="Show" OnContent="Hide"
              Toggled="ToggleSwitch_Toggled"/>
```

Вы также можете использовать более сложное содержимое, установив свойства [OnContentTemplate](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.toggleswitch.oncontenttemplate) и [OffContentTemplate](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.toggleswitch.offcontenttemplate).

## <a name="recommendations"></a>Рекомендации

- Используйте метки включения и выключения по умолчанию, когда это возможно, но заменяйте их на выключатель, когда это целесообразно. Если вы заменяете их, используйте одно слово, более точно описывающее выключатель. Как правило, если слова "Вкл." и "Выкл." не описывают действие, выполняемое выключателем, возможно, будет лучше использовать другой элемент управления.
- Не заменяйте метки "Вкл." и "Выкл." без необходимости; оставьте метки по умолчанию, если ситуация не требует пользовательских меток.

## <a name="get-the-sample-code"></a>Получить пример кода

- [Пример из коллекции элементов управления XAML](https://github.com/Microsoft/Xaml-Controls-Gallery) — ознакомьтесь со всеми элементами управления XAML в интерактивном режиме.

## <a name="related-articles"></a>Связанные статьи

- [Класс ToggleSwitch](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.toggleswitch)
- [Переключатели](radio-button.md)
- [Выключатели](toggles.md)
- [Флажки](checkbox.md)
