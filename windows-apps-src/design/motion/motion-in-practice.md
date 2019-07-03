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
ms.openlocfilehash: 8cf010533d2d62559bb8dc0d214e04ab917e62bd
ms.sourcegitcommit: d534f81590d881a18d677a648c59913029837a84
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/03/2019
ms.locfileid: "67535435"
---
# <a name="bringing-it-together"></a>Реализация

Синхронность, реалистичность анимации, направление и сила притяжения лежат в основе движения Fluent (плавность). Каждую характеристику следует учитывать в контексте других и применять соответствующим образом в контексте вашего приложения.

Ниже приведены три способа применения основных принципов движения Fluent в вашем приложении.

:::row:::
    :::column:::
**Неявные анимации** автоматического анимации и времени между значениями в изменение параметра для достижения очень простой Fluent движения с помощью стандартизированных значений.
    :::column-end:::
    :::column:::
**Встроенные анимации** компоненты системы, такие как стандартные элементы управления и общие движения, являются «Fluent по умолчанию». Основные принципы были применены в соответствии с их использованием подразумеваемых.
    :::column-end:::
    :::column:::
**Пользовательскую анимацию, выполнив инструкции, рекомендации** могут возникнуть ситуации, когда система пока не предоставляет решение точное движения для вашего сценария. В таких случаях следует используйте основные Базовые рекомендации в качестве отправной точки для своим опытом.
    :::column-end:::
:::row-end:::

**Пример перехода**

![функциональная анимация](images/pageRefresh.gif)

:::row:::
    :::column:::
<b>Направление вперед ожидания:</b><br>
Исчезание: 150 МБ; Замедление: По умолчанию ускорить <b>шагом вперед в:</b><br>
Сдвинуть вверх 150px: учетом 300 мс; Замедление: По умолчанию замедление
    :::column-end:::
    :::column:::
<b>Направление обратной Out:</b><br>
Проведите пальцем вниз 150px: 150ms; Замедление: По умолчанию ускорить <b>обратно в направлении:</b><br>
Появление: учетом 300 мс; Замедление: По умолчанию замедление
    :::column-end:::
:::row-end:::

**Пример объекта**

 ![Движение 300 мс](images/control.gif)

:::row:::
    :::column:::
<b>Разверните направление:</b><br>
Приращения: учетом 300 мс; Замедление: Standard
    :::column-end:::
    :::column:::
<b>Направление контракт:</b><br>
Приращения: 150ms; Замедление: Ускорьте по умолчанию
    :::column-end:::
:::row-end:::

## <a name="examples"></a>Примеры

<table>
<tr>
<td><img src="images/xaml-controls-gallery-app-icon.png" alt="XAML controls gallery" width="168"></img></td>
<td>
    <p>Если у вас есть <strong style="font-weight: semi-bold">коллекции элементов управления XAML</strong> приложения. Щелкните здесь, чтобы <a href="xamlcontrolsgallery:/item/ImplicitTransition">откройте приложение и увидеть неявные переходы в действии</a>.</p>
    <ul>
    <li><a href="https://www.microsoft.com/p/xaml-controls-gallery/9msvh128x2zt">Получить приложение XAML Controls Gallery (Microsoft Store)</a></li>
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
