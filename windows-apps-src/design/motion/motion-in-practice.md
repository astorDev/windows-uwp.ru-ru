---
Description: Узнайте, как Fluent движения, основы, объединяются в вашем приложении.
title: Движение на практике — анимация в приложениях UWP
label: Motion in practice
template: detail.hbs
ms.date: 10/02/2018
ms.topic: article
keywords: windows 10, uwp
pm-contact: stmoy
design-contact: jeffarn
doc-status: Draft
ms.localizationpriority: medium
ms.custom: RS5
ms.openlocfilehash: b8cbbd6f82555576b89328d28d94e43aae7574a2
ms.sourcegitcommit: 81021b7930905beb67383a08b09520cf95c68fd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2019
ms.locfileid: "65940317"
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

## <a name="examples"></a>Примеры

<table>
<tr>
<td><img src="images/xaml-controls-gallery-app-icon.png" alt="XAML controls gallery" width="168"></img></td>
<td>
    <p>Если у вас есть <strong style="font-weight: semi-bold">коллекции элементов управления XAML</strong> приложения. Щелкните здесь, чтобы <a href="xamlcontrolsgallery:/item/ImplicitTransition">откройте приложение и увидеть неявные переходы в действии</a>.</p>
    <ul>
    <li><a href="https://www.microsoft.com/store/productId/9MSVH128X2ZT">Получить приложение XAML Controls Gallery (Microsoft Store)</a></li>
    <li><a href="https://github.com/Microsoft/Xaml-Controls-Gallery">Получить исходный код (GitHub)</a></li>
    </ul>
</td>
</tr>
</table>

## <a name="implicit-animations"></a>Неявные анимации

> Неявные анимации требуется Windows 10, версия 1809 ([SDK 17763](https://developer.microsoft.com/windows/downloads/windows-10-sdk)) или более поздней версии.

Неявные анимации представляет собой простой способ добиться Fluent движения, автоматически интерполяции между значениями старого и нового во время изменения параметра.

Неявно можно анимировать изменения в следующие свойства:

- [UIElement](/uwp/api/windows.ui.xaml.uielement)
  - **Непрозрачности**
  - **Поворот**
  - **Масштаб**
  - **Перевод**

- [Границы](/uwp/api/windows.ui.xaml.controls.border), [ContentPresenter](/uwp/api/windows.ui.xaml.controls.contentpresenter), или [панели](/uwp/api/windows.ui.xaml.controls.panel)
  - **Фон**

Каждое свойство, которое может быть неявно анимировать изменения имеет соответствующий _перехода_ свойство. Для анимации свойства, назначаемые к типу перехода к соответствующему _перехода_ свойство. В этой таблице показаны _перехода_ свойства и тип перехода для использования для каждого из них.

| Анимируемого свойства | Свойство перехода | Тип неявное перехода |
| -- | -- | -- |
| [UIElement.Opacity](/uwp/api/windows.ui.xaml.uielement.opacity) | [OpacityTransition](/uwp/api/windows.ui.xaml.uielement.opacitytransition) | [ScalarTransition](/uwp/api/windows.ui.xaml.scalartransition) |
| [UIElement.Rotation](/uwp/api/windows.ui.xaml.uielement.rotation) | [RotationTransition](/uwp/api/windows.ui.xaml.uielement.rotationtransition) | [ScalarTransition](/uwp/api/windows.ui.xaml.scalartransition) |
| [UIElement.Scale](/uwp/api/windows.ui.xaml.uielement.scale) | [ScaleTransition](/uwp/api/windows.ui.xaml.uielement.scaletransition) | [Vector3Transition](/uwp/api/windows.ui.xaml.vector3transition) |
| [UIElement.Translation](/uwp/api/windows.ui.xaml.uielement.translation) | [TranslationTransition](/uwp/api/windows.ui.xaml.uielement.translationtransition) | [Vector3Transition](/uwp/api/windows.ui.xaml.vector3transition) |
| [Border.Background](/uwp/api/windows.ui.xaml.controls.border.background) | [BackgroundTransition](/uwp/api/windows.ui.xaml.controls.border.backgroundtransition) | [BrushTransition](//uwp/api/windows.ui.xaml.uielement.brushtransition) |
| [ContentPresenter.Background](/uwp/api/windows.ui.xaml.controls.contentpresenter.background) | [BackgroundTransition](/uwp/api/windows.ui.xaml.controls.contentpresenter.backgroundtransition) | [BrushTransition](//uwp/api/windows.ui.xaml.uielement.brushtransition) |
| [Panel.Background](/uwp/api/windows.ui.xaml.controls.panel.background) | [BackgroundTransition](/uwp/api/windows.ui.xaml.controls.panel.backgroundtransition)  | [BrushTransition](//uwp/api/windows.ui.xaml.uielement.brushtransition) |

В этом примере показано, как использовать свойства Opacity и перехода для создания кнопки проявляться при включении элемента управления и Исчезание, когда она неактивна.

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

## <a name="related-articles"></a>Связанные статьи

- [Обзор методологии Motion](index.md)
- [О времени и реалистичной анимации](timing-and-easing.md)
- [Направленность и тяжести](directionality-and-gravity.md)
