---
author: jwmsft
Description: Learn how Fluent motion fundamentals come together in your app.
title: Движение на практике — анимация в приложениях UWP
label: Motion in practice
template: detail.hbs
ms.author: jimwalk
ms.date: 10/02/2018
ms.topic: article
keywords: Windows10, UWP
pm-contact: stmoy
design-contact: jeffarn
doc-status: Draft
ms.localizationpriority: medium
ms.openlocfilehash: 36081c14cfb75a1cedb103ba17eff4a05f5e4e83
ms.sourcegitcommit: f2c9a050a9137a473f28b613968d5782866142c6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/10/2018
ms.locfileid: "6268092"
---
# <a name="bringing-it-together"></a>Реализация

Синхронность, реалистичность анимации, направление и сила притяжения лежат в основе движения Fluent (плавность). Каждую характеристику следует учитывать в контексте других и применять соответствующим образом в контексте вашего приложения.

Ниже приведены три способа применения основных принципов движения Fluent в вашем приложении.

:::row:::
    :::column:::
        **Implicit animation**

        Automatic tween and timing between values in a parameter change to achieve very simple Fluent motion using the standardized values.
    :::column-end:::
    :::column:::
        **Built-in animation**

        System components, such as common controls and shared motion, are "Fluent by default". Fundamentals have been applied in a manner consistent with their implied usage.
    :::column-end:::
    :::column:::
        **Custom animation following guidance recommendations**

        There may be times when the system does not yet provide an exact motion solution for your scenario. In those cases, use the baseline fundamental recommendations as a starting point for your experiences.
    :::column-end:::
:::row-end:::

**Пример перехода**

![функциональная анимация](images/pageRefresh.gif)

:::row:::
    :::column:::
        <b>Direction Forward Out:</b><br>
        Fade out: 150m; Easing: Default Accelerate

        <b>Direction Forward In:</b><br>
        Slide up 150px: 300ms; Easing: Default Decelerate
    :::column-end:::
    :::column:::
         <b>Direction Backward Out:</b><br>
        Slide down 150px: 150ms; Easing: Default Accelerate

        <b>Direction Backward In:</b><br>
        Fade in: 300ms; Easing: Default Decelerate
    :::column-end:::
:::row-end:::

**Пример объекта**

 ![Движение 300 мс](images/control.gif)

:::row:::
    :::column:::
        <b>Direction Expand:</b><br>
        Grow: 300ms; Easing: Standard
    :::column-end:::
    :::column:::
        <b>Direction Contract:</b><br>
        Grow: 150ms; Easing: Default Accelerate
    :::column-end:::
:::row-end:::

## <a name="implicit-animations"></a>Неявные анимации

> Неявные анимации требуется Windows 10, версия 1809 ([SDK 17763](https://developer.microsoft.com/windows/downloads/windows-10-sdk)) или более поздней версии.


Неявные анимации — это простой способ для достижения движения Fluent, автоматически выполняя интерполяцию между старых и новых значений во время смены параметра.

Неявно, вы можете анимировать изменения в следующие свойства:

- [UIElement](/uwp/api/windows.ui.xaml.uielement)
  - **Opacity (Прозрачность)**
  - **Поворот**
  - **Масштаб**
  - **Translation (Преобразование)**

- [Границы](/uwp/api/windows.ui.xaml.controls.border), [ContentPresenter](/uwp/api/windows.ui.xaml.controls.contentpresenter)или [Панель](/uwp/api/windows.ui.xaml.controls.panel)
  - **Фон**

Каждое свойство, которое может быть неявно анимировать изменения имеет соответствующее свойство _перехода_ . Чтобы анимировать свойство, тип перехода назначить соответствующее свойство _перехода_ . В этой таблице показаны свойства _перехода_ и тип перехода для каждого из них.

| Анимируемого свойства | Свойство перехода | Тип неявное перехода |
| -- | -- | -- |
| [UIElement.Opacity](/uwp/api/windows.ui.xaml.uielement.opacity) | [OpacityTransition](/uwp/api/windows.ui.xaml.uielement.opacitytransition) | [ScalarTransition](/uwp/api/windows.ui.xaml.scalartransition) |
| [UIElement.Rotation](/uwp/api/windows.ui.xaml.uielement.rotation) | [RotationTransition](/uwp/api/windows.ui.xaml.uielement.rotationtransition) | [ScalarTransition](/uwp/api/windows.ui.xaml.scalartransition) |
| [UIElement.Scale](/uwp/api/windows.ui.xaml.uielement.scale) | [ScaleTransition](/uwp/api/windows.ui.xaml.uielement.scaletransition) | [Vector3Transition](/uwp/api/windows.ui.xaml.uielement.vector3transition) |
| [UIElement.Translation](/uwp/api/windows.ui.xaml.uielement.scale) | [TranslationTransition](/uwp/api/windows.ui.xaml.uielement.translationtransition) | [Vector3Transition](/uwp/api/windows.ui.xaml.uielement.vector3transition) |
| [Border.Background](/uwp/api/windows.ui.xaml.controls.border.background) | [BackgroundTransition](/uwp/api/windows.ui.xaml.controls.border.backgroundtransition) | [BrushTransition](//uwp/api/windows.ui.xaml.uielement.brushtransition) |
| [ContentPresenter.Background](/uwp/api/windows.ui.xaml.controls.contentpresenter.background) | [BackgroundTransition](/uwp/api/windows.ui.xaml.controls.contentpresenter.backgroundtransition) | [BrushTransition](//uwp/api/windows.ui.xaml.uielement.brushtransition) |
| [Panel.Background](/uwp/api/windows.ui.xaml.controls.panel.background) | [BackgroundTransition](/uwp/api/windows.ui.xaml.controls.panel.backgroundtransition)  | [BrushTransition](//uwp/api/windows.ui.xaml.uielement.brushtransition) |

В этом примере показано, как использовать свойства Opacity и перехода, чтобы сделать кнопку появления при включении элемента управления или Исчезание, когда она отключена.

```xaml
<Button x:Name="SubmitButton"
        Content="Submit"
        Opacity="{x:Bind OpaqueIfEnabled(SubmitButton.IsEnabled), Mode=OneWay}">
    <Button.OpacityTransition>
        <ScalarTransition />
    </Button.OpacityTransition>
</Button>
```

```csharp
public double OpaqueIfEnabled(bool IsEnabled)
{
    return IsEnabled ? 1.0 : 0.2;
}
```

## <a name="related-articles"></a>Смежные разделы

- [Обзор движения](index.md)
- [Синхронность и реалистичная анимация](timing-and-easing.md)
- [Направление и сила притяжения](directionality-and-gravity.md)