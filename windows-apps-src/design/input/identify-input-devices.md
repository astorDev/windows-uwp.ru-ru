---
author: Karl-Bridge-Microsoft
Description: Identify the input devices connected to a Universal Windows Platform (UWP) device and identify their capabilities and attributes.
title: Определение устройств ввода
ms.assetid: B2E93FBF-C508-44D9-BA46-ECFDAA8746F4
label: Identify input devices
template: detail.hbs
keywords: устройство, дигитайзер, ввод, взаимодействие
ms.author: kbridge
ms.date: 02/08/2017
ms.topic: article
ms.localizationpriority: medium
ms.openlocfilehash: 77d2a914147fb2cd20f8043e0b52a824ddf3710d
ms.sourcegitcommit: 70ab58b88d248de2332096b20dbd6a4643d137a4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/02/2018
ms.locfileid: "5942911"
---
# <a name="identify-input-devices"></a>Определение устройств ввода


Определение устройств ввода, подключенных к устройству с универсальной платформой Windows (UWP), а также их возможностей и атрибутов.

> **Важные API-интерфейсы**: [**Windows.Devices.Input**](https://msdn.microsoft.com/library/windows/apps/br225648), [**Windows.UI.Input**](https://msdn.microsoft.com/library/windows/apps/br208383), [**Windows.UI.Xaml.Input**](https://msdn.microsoft.com/library/windows/apps/br242084)

## <a name="retrieve-mouse-properties"></a>Получение свойств мыши


В пространстве имен [**Windows.Devices.Input**](https://msdn.microsoft.com/library/windows/apps/br225648) содержится класс [**MouseCapabilities**](https://msdn.microsoft.com/library/windows/apps/br225626), который используется для получения свойств одной подключенной мыши или нескольких. Просто создайте новый объект **MouseCapabilities** и получите необходимые вам свойства.

**Примечание**значения, возвращаемые данными свойствами, основаны на всех обнаруженных мышах: логические свойства возвращают не равны нулю, если по крайней мере одна мышь поддерживает специальную возможность, а числовые свойства возвращают максимальное значение, выраженное одним мышь.

 

В следующем коде для отображения свойств и значений отдельной мыши используются последовательности элементов [**TextBlock**](https://msdn.microsoft.com/library/windows/apps/br209652).

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


В пространстве имен [**Windows.Devices.Input**](https://msdn.microsoft.com/library/windows/apps/br225648) содержится класс [**KeyboardCapabilities**](https://msdn.microsoft.com/library/windows/apps/br225623), используемый для загрузки данных о том, подключена ли клавиатура. Просто создайте новый объект **KeyboardCapabilities** и получите свойство [**KeyboardPresent**](https://msdn.microsoft.com/library/windows/apps/br225625).

В следующем коде для отображения свойства и значения клавиатуры используется элемент [**TextBlock**](https://msdn.microsoft.com/library/windows/apps/br209652).

```CSharp
private void GetKeyboardProperties()
{
    KeyboardCapabilities keyboardCapabilities = new Windows.Devices.Input.KeyboardCapabilities();
    KeyboardPresent.Text = keyboardCapabilities.KeyboardPresent != 0 ? "Yes" : "No";
}
```

## <a name="retrieve-touch-properties"></a>Получение свойств устройства сенсорного ввода


В пространстве имен [**Windows.Devices.Input**](https://msdn.microsoft.com/library/windows/apps/br225648) содержится класс [**TouchCapabilities**](https://msdn.microsoft.com/library/windows/apps/br225644), используемый для получения данных о том, подключены ли сенсорные дигитайзеры. Просто создайте новый объект **TouchCapabilities** и получите необходимые вам свойства.

**Примечание**значения, возвращаемые данными свойствами, основаны на всех подключенных сенсорных дигитайзерах: логические свойства возвращают не равны нулю, если по крайней мере один дигитайзер поддерживает специальную возможность, а числовые свойства возвращают максимальное значение выраженное одним дигитайзером.

 

В следующем коде для отображения свойств и значений отдельного устройства сенсорного ввода используются последовательности элементов [**TextBlock**](https://msdn.microsoft.com/library/windows/apps/br209652).

```CSharp
private void GetTouchProperties()
{
    TouchCapabilities touchCapabilities = new Windows.Devices.Input.TouchCapabilities();
    TouchPresent.Text = touchCapabilities.TouchPresent != 0 ? "Yes" : "No";
    Contacts.Text = touchCapabilities.Contacts.ToString();
}
```

## <a name="retrieve-pointer-properties"></a>Получение свойств указателя


В пространстве имен [**Windows.Devices.Input**](https://msdn.microsoft.com/library/windows/apps/br225648) содержится класс [**PointerDevice**](https://msdn.microsoft.com/library/windows/apps/br225633), используемый для получения данных о том, поддерживают ли какие-либо из обнаруженных устройств ввод указателем (устройство сенсорного ввода, сенсорная панель, мышь или перо). Просто создайте новый объект **PointerDevice** и получите необходимые вам свойства.

**Примечание**значения, возвращаемые данными свойствами, основаны на всех обнаруженных указателях: логические свойства возвращают не равны нулю, если по крайней мере один указатель поддерживает специальную возможность, а числовые свойства возвращают максимальное значение, предоставляемые с одним указателем.

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

## <a name="related-articles"></a>Связанные разделы


**Примеры**
* [Пример базового ввода](http://go.microsoft.com/fwlink/p/?LinkID=620302)
* [Пример ввода с малой задержкой](http://go.microsoft.com/fwlink/p/?LinkID=620304)
* [Пример режима взаимодействия с пользователем](http://go.microsoft.com/fwlink/p/?LinkID=619894)

**Примеры архива**
* [Ввод. Пример возможностей устройства](http://go.microsoft.com/fwlink/p/?linkid=231530)
 

 




