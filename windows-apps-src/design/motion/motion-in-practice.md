---
Description: Узнайте, как в приложении могут объединяться основные принципы перемещения Fluent.
title: Движение на практике — анимация в приложениях Windows
label: Motion in practice
template: detail.hbs
ms.date: 10/02/2018
ms.topic: article
keywords: windows 10, uwp
pm-contact: stmoy
design-contact: jeffarn
doc-status: Draft
ms.localizationpriority: medium
ms.custom: RS5
ms.openlocfilehash: 45ab6c593b9e20f778e4b352a8b284cefe57c9a8
ms.sourcegitcommit: 0dee502484df798a0595ac1fe7fb7d0f5a982821
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/08/2020
ms.locfileid: "82970329"
---
# <a name="bringing-it-together"></a>Реализация

Синхронность, реалистичность анимации, направление и сила притяжения лежат в основе движения Fluent (плавность). Каждую характеристику следует учитывать в контексте других и применять соответствующим образом в контексте вашего приложения.

Ниже приведены три способа применения основных принципов движения Fluent в вашем приложении.

:::row:::
    :::column:::
**Неявная анимация** Автоматическая анимация и время между значениями в параметре меняются для достижения очень простого движения Fluent с использованием стандартизованных значений.
    :::column-end:::
    :::column:::
**Встроенная анимация** Системные компоненты, такие как общие элементы управления и движение, по умолчанию имеют свободный доступ. Основные принципы применены способом, согласованным с их подразумеваемым использованием.
    :::column-end:::
    :::column:::
**Пользовательская анимация после рекомендаций по** рекомендациям Может возникнуть ситуация, когда система еще не предоставляет точного решения Motion для вашего сценария. В таких случаях используйте базовые фундаментальные рекомендации в качестве отправной точки для вашего опыта.
    :::column-end:::
:::row-end:::

**Пример перехода**

![функциональная анимация](images/pageRefresh.gif)

:::row:::
    :::column:::
<b>Направление переадресации:</b><br>
Исчезание: 150m; Замедление: <b>направление</b> ускорения по умолчанию:<br>
На слайде up 150px: 300 мс; Замедление: замедление по умолчанию
    :::column-end:::
    :::column:::
<b>Направление обратного отнаправления:</b><br>
Сдвинуть вниз 150px: 150ms; Замедление: направление ускорения по умолчанию <b>назад в:</b><br>
Появление: 300 мс; Замедление: замедление по умолчанию
    :::column-end:::
:::row-end:::

**Пример объекта**

 ![Движение 300 мс](images/control.gif)

:::row:::
    :::column:::
<b>Направление развертывания:</b><br>
Рост: 300 мс; Замедление: Стандартный
    :::column-end:::
    :::column:::
<b>Контракт направления:</b><br>
Рост: 150ms; Замедление: ускорение по умолчанию
    :::column-end:::
:::row-end:::

## <a name="examples"></a>Примеры

<table>
<tr>
<td><img src="images/xaml-controls-gallery-app-icon.png" alt="XAML controls gallery" width="168"></img></td>
<td>
    <p>Если приложение <strong style="font-weight: semi-bold">коллекции элементов управления XAML</strong> установлено, щелкните здесь, чтобы <a href="xamlcontrolsgallery:/item/ImplicitTransition">открыть приложение и просмотреть неявные переходы в действии</a>.</p>
    <ul>
    <li><a href="https://www.microsoft.com/p/xaml-controls-gallery/9msvh128x2zt">Получить приложение XAML Controls Gallery (Microsoft Store)</a></li>
    <li><a href="https://github.com/Microsoft/Xaml-Controls-Gallery">Получить исходный код (GitHub)</a></li>
    </ul>
</td>
</tr>
</table>

## <a name="implicit-animations"></a>Неявные анимации

> Для неявных анимаций требуется Windows 10, версия 1809 ([SDK 17763](https://developer.microsoft.com/windows/downloads/windows-10-sdk)) или более поздняя.

Неявные анимации — это простой способ достижения свободного движения путем автоматической интерполяции между старыми и новыми значениями во время изменения параметра.

Можно неявно анимировать изменения в следующих свойствах:

- [UIElement](/uwp/api/windows.ui.xaml.uielement)
  - **Непрозрачность**
  - **Поворот**
  - **Масштабирование**
  - **Перевод**

- [Граница](/uwp/api/windows.ui.xaml.controls.border), [ContentPresenter](/uwp/api/windows.ui.xaml.controls.contentpresenter)или [панель](/uwp/api/windows.ui.xaml.controls.panel)
  - **Вводная информация**

Каждое свойство, которое может иметь неявно анимированные изменения, имеет соответствующее свойство _перехода_ . Чтобы анимировать свойство, необходимо назначить тип перехода соответствующему свойству _перехода_ . В этой таблице показаны свойства _перехода_ и тип перехода, используемый для каждого из них.

| Анимированное свойство | Свойство перехода | Тип неявного перехода |
| -- | -- | -- |
| [UIElement. Opacity](/uwp/api/windows.ui.xaml.uielement.opacity) | [опаЦититранситион](/uwp/api/windows.ui.xaml.uielement.opacitytransition) | [ScalarTransition](/uwp/api/windows.ui.xaml.scalartransition) |
| [UIElement.Rotation](/uwp/api/windows.ui.xaml.uielement.rotation) | [ротатионтранситион](/uwp/api/windows.ui.xaml.uielement.rotationtransition) | [ScalarTransition](/uwp/api/windows.ui.xaml.scalartransition) |
| [UIElement.Scale](/uwp/api/windows.ui.xaml.uielement.scale) | [скалетранситион](/uwp/api/windows.ui.xaml.uielement.scaletransition) | [Vector3Transition](/uwp/api/windows.ui.xaml.vector3transition) |
| [UIElement.Translation](/uwp/api/windows.ui.xaml.uielement.translation) | [транслатионтранситион](/uwp/api/windows.ui.xaml.uielement.translationtransition) | [Vector3Transition](/uwp/api/windows.ui.xaml.vector3transition) |
| [Border. Background](/uwp/api/windows.ui.xaml.controls.border.background) | [баккграундтранситион](/uwp/api/windows.ui.xaml.controls.border.backgroundtransition) | [BrushTransition](//uwp/api/windows.ui.xaml.uielement.brushtransition) |
| [ContentPresenter. Background](/uwp/api/windows.ui.xaml.controls.contentpresenter.background) | [баккграундтранситион](/uwp/api/windows.ui.xaml.controls.contentpresenter.backgroundtransition) | [BrushTransition](//uwp/api/windows.ui.xaml.uielement.brushtransition) |
| [Панель. фон](/uwp/api/windows.ui.xaml.controls.panel.background) | [баккграундтранситион](/uwp/api/windows.ui.xaml.controls.panel.backgroundtransition)  | [BrushTransition](//uwp/api/windows.ui.xaml.uielement.brushtransition) |

В этом примере показано, как использовать свойство Opacity и переход, чтобы сделать кнопку плавной при включении и исчезновении элемента управления, если он отключен.

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

## <a name="related-articles"></a>Похожие статьи

- [Обзор движения](index.md)
- [Согласование по времени и реалистичная анимация](timing-and-easing.md)
- [Направление и сила притяжения](directionality-and-gravity.md)
