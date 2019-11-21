---
Description: Определение устройств ввода, подключенных к устройству с универсальной платформой Windows (UWP), а также их возможностей и атрибутов.
title: Определение устройств ввода
ms.assetid: B2E93FBF-C508-44D9-BA46-ECFDAA8746F4
label: Identify input devices
template: detail.hbs
keywords: устройство, дигитайзер, ввод, взаимодействие
ms.date: 02/08/2017
ms.topic: article
ms.localizationpriority: medium
ms.openlocfilehash: b2a17d1f4664326cb54d9c53d828eb372ef93fe4
ms.sourcegitcommit: b52ddecccb9e68dbb71695af3078005a2eb78af1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74257886"
---
# <a name="identify-input-devices"></a>Определение устройств ввода


Определение устройств ввода, подключенных к устройству с универсальной платформой Windows (UWP), а также их возможностей и атрибутов.

> **Важные API-интерфейсы**: [**Windows.Devices.Input**](https://docs.microsoft.com/uwp/api/Windows.Devices.Input), [**Windows.UI.Input**](https://docs.microsoft.com/uwp/api/Windows.UI.Core), [**Windows.UI.Xaml.Input**](https://docs.microsoft.com/uwp/api/Windows.UI.Input)

## <a name="retrieve-mouse-properties"></a>Получение свойств мыши


В пространстве имен [**Windows.Devices.Input**](https://docs.microsoft.com/uwp/api/Windows.Devices.Input) содержится класс [**MouseCapabilities**](https://docs.microsoft.com/uwp/api/Windows.Devices.Input.MouseCapabilities), который используется для получения свойств одной подключенной мыши или нескольких. Просто создайте новый объект **MouseCapabilities** и получите необходимые вам свойства.

**Обратите внимание** ,  значения, возвращаемые описанными здесь свойствами, основаны на всех обнаруженных курсорах: логические свойства возвращают ненулевое значение, если хотя бы одна мышь поддерживает определенную возможность, а числовые свойства возвращают максимальное число, предоставляемое одним щелчком мыши.

 

В следующем коде для отображения свойств и значений отдельной мыши используются последовательности элементов [**TextBlock**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.TextBlock).

```CSharp
private void GetMouseProperties()
{
    MouseCapabilities mouseCapabilities = new Windows.Devices.Input.MouseCapabilities();
    MousePresent.Text = mouseCapabilities.MousePresent != 0 ? "Yes" : "No";
    VertWheel.Text = mouseCapabilities.VerticalWheelPresent != 0 ? "Yes" : "No";
    HorzWheel.Text = mouseCapabilities.HorizontalWheelPresent != 0 ? "Yes" : "No";
    SwappedButtons.Text = mouseCapabilities.SwapButtons != 0 ? "Yes" : "No";
    NumButtons.Text = mouseCapabilities.NumberOfButtons.ToString();
}
```

## <a name="retrieve-keyboard-properties"></a>Получение свойств клавиатуры


В пространстве имен [**Windows.Devices.Input**](https://docs.microsoft.com/uwp/api/Windows.Devices.Input) содержится класс [**KeyboardCapabilities**](https://docs.microsoft.com/uwp/api/Windows.Devices.Input.KeyboardCapabilities), используемый для загрузки данных о том, подключена ли клавиатура. Просто создайте новый объект **KeyboardCapabilities** и получите свойство [**KeyboardPresent**](https://docs.microsoft.com/uwp/api/windows.devices.input.keyboardcapabilities.keyboardpresent).

В следующем коде для отображения свойства и значения клавиатуры используется элемент [**TextBlock**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.TextBlock).

```CSharp
private void GetKeyboardProperties()
{
    KeyboardCapabilities keyboardCapabilities = new Windows.Devices.Input.KeyboardCapabilities();
    KeyboardPresent.Text = keyboardCapabilities.KeyboardPresent != 0 ? "Yes" : "No";
}
```

## <a name="retrieve-touch-properties"></a>Получение свойств устройства сенсорного ввода


В пространстве имен [**Windows.Devices.Input**](https://docs.microsoft.com/uwp/api/Windows.Devices.Input) содержится класс [**TouchCapabilities**](https://docs.microsoft.com/uwp/api/Windows.Devices.Input.TouchCapabilities), используемый для получения данных о том, подключены ли сенсорные дигитайзеры. Просто создайте новый объект **TouchCapabilities** и получите необходимые вам свойства.

**Обратите внимание** ,  значения, возвращаемые описываемыми здесь свойствами, основаны на всех обнаруженных сенсорных дигитайзерах: логические свойства возвращают ненулевое значение, если хотя бы один дигитайзер поддерживает определенную возможность, а числовые свойства возвращают максимальное число, предоставляемое одним дигитайзером.

 

В следующем коде для отображения свойств и значений отдельного устройства сенсорного ввода используются последовательности элементов [**TextBlock**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.TextBlock).

```CSharp
private void GetTouchProperties()
{
    TouchCapabilities touchCapabilities = new Windows.Devices.Input.TouchCapabilities();
    TouchPresent.Text = touchCapabilities.TouchPresent != 0 ? "Yes" : "No";
    Contacts.Text = touchCapabilities.Contacts.ToString();
}
```

## <a name="retrieve-pointer-properties"></a>Получение свойств указателя


В пространстве имен [**Windows.Devices.Input**](https://docs.microsoft.com/uwp/api/Windows.Devices.Input) содержится класс [**PointerDevice**](https://docs.microsoft.com/uwp/api/Windows.Devices.Input.PointerDevice), используемый для получения данных о том, поддерживают ли какие-либо из обнаруженных устройств ввод указателем (устройство сенсорного ввода, сенсорная панель, мышь или перо). Просто создайте новый объект **PointerDevice** и получите необходимые вам свойства.

**Обратите внимание** ,  значения, возвращаемые описываемыми здесь свойствами, основаны на всех обнаруженных устройствах-указателях: логические свойства возвращают ненулевое значение, если хотя бы одно устройство поддерживает определенную возможность, а числовые свойства возвращают максимальный размер, предоставляемый любым устройством с одним указателем.

В следующем коде для отображения свойств и значений для каждого указателя используется таблица.

```CSharp
private void GetPointerDevices()
{
    IReadOnlyList<PointerDevice> pointerDevices = Windows.Devices.Input.PointerDevice.GetPointerDevices();
    int gridRow = 0;
    int gridColumn = 0;

    for (int i = 0; i < pointerDevices.Count; i++)
    {
        // Pointer device type.
        TextBlock textBlock1 = new TextBlock();
        Grid_PointerProps.Children.Add(textBlock1);
        textBlock1.Text = (i + 1).ToString() + " Pointer Device Type:";
        Grid.SetRow(textBlock1, gridRow);
        Grid.SetColumn(textBlock1, gridColumn);

        TextBlock textBlock2 = new TextBlock();
        textBlock2.Text = pointerDevices[i].PointerDeviceType.ToString();
        Grid_PointerProps.Children.Add(textBlock2);
        Grid.SetRow(textBlock2, gridRow++);
        Grid.SetColumn(textBlock2, gridColumn + 1);

        // Is external?
        TextBlock textBlock3 = new TextBlock();
        Grid_PointerProps.Children.Add(textBlock3);
        textBlock3.Text = (i + 1).ToString() + " Is External?";
        Grid.SetRow(textBlock3, gridRow);
        Grid.SetColumn(textBlock3, gridColumn);

        TextBlock textBlock4 = new TextBlock();
        Grid_PointerProps.Children.Add(textBlock4);
        textBlock4.Text = pointerDevices[i].IsIntegrated.ToString();
        Grid.SetRow(textBlock4, gridRow++);
        Grid.SetColumn(textBlock4, gridColumn + 1);

        // Maximum contacts.
        TextBlock textBlock5 = new TextBlock();
        Grid_PointerProps.Children.Add(textBlock5);
        textBlock5.Text = (i + 1).ToString() + " Max Contacts:";
        Grid.SetRow(textBlock5, gridRow);
        Grid.SetColumn(textBlock5, gridColumn);

        TextBlock textBlock6 = new TextBlock();
        Grid_PointerProps.Children.Add(textBlock6);
        textBlock6.Text = pointerDevices[i].MaxContacts.ToString();
        Grid.SetRow(textBlock6, gridRow++);
        Grid.SetColumn(textBlock6, gridColumn + 1);

        // Physical device rectangle.
        TextBlock textBlock7 = new TextBlock();
        Grid_PointerProps.Children.Add(textBlock7);
        textBlock7.Text = (i + 1).ToString() + " Physical Device Rect:";
        Grid.SetRow(textBlock7, gridRow);
        Grid.SetColumn(textBlock7, gridColumn);

        TextBlock textBlock8 = new TextBlock();
        Grid_PointerProps.Children.Add(textBlock8);
        textBlock8.Text = pointerDevices[i].PhysicalDeviceRect.X.ToString() + "," +
            pointerDevices[i].PhysicalDeviceRect.Y.ToString() + "," +
            pointerDevices[i].PhysicalDeviceRect.Width.ToString() + "," +
            pointerDevices[i].PhysicalDeviceRect.Height.ToString();
        Grid.SetRow(textBlock8, gridRow++);
        Grid.SetColumn(textBlock8, gridColumn + 1);

        // Screen rectangle.
        TextBlock textBlock9 = new TextBlock();
        Grid_PointerProps.Children.Add(textBlock9);
        textBlock9.Text = (i + 1).ToString() + " Screen Rect:";
        Grid.SetRow(textBlock9, gridRow);
        Grid.SetColumn(textBlock9, gridColumn);

        TextBlock textBlock10 = new TextBlock();
        Grid_PointerProps.Children.Add(textBlock10);
        textBlock10.Text = pointerDevices[i].ScreenRect.X.ToString() + "," +
            pointerDevices[i].ScreenRect.Y.ToString() + "," +
            pointerDevices[i].ScreenRect.Width.ToString() + "," +
            pointerDevices[i].ScreenRect.Height.ToString();
        Grid.SetRow(textBlock10, gridRow++);
        Grid.SetColumn(textBlock10, gridColumn + 1);

        gridColumn += 2;
        gridRow = 0;
    }
```

## <a name="related-articles"></a>Связанные статьи


**Примеры**
* [Пример базового ввода](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/BasicInput)
* [Пример ввода с небольшой задержкой](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/LowLatencyInput)
* [Пример режима взаимодействия с пользователем](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/UserInteractionMode)

**Примеры из архива**
* [Входные данные: пример возможностей устройства](https://code.msdn.microsoft.com/windowsapps/Input-device-capabilities-31b67745)
 

 




