---
author: mijacobs
description: "Отображение— это новая модель взаимодействия, которая помогает сфокусировать внимание и сделать приложение привлекательнее."
title: "Отображение"
template: detail.hbs
ms.author: mijacobs
ms.date: 08/9/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows10, uwp
pm-contact: kisai
design-contact: conrwi
dev-contact: jevansa
doc-status: Published
ms.openlocfilehash: d50e3f47faad5fff0ef461a4b5312127a0b9ec9c
ms.sourcegitcommit: 0d5b3daddb3ae74f91178c58e35cbab33854cb7f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/09/2017
---
# <a name="reveal"></a>Отображение

> [!IMPORTANT]
> В этой статье описана еще не выпущенная функция, которая может быть существенно изменена до коммерческого выпуска. Майкрософт не дает никаких гарантий, явных или подразумеваемых, в отношении предоставленной здесь информации.

Отображение— это световой эффект, который добавляет приложению глубины и помогает привлечь внимание к интерактивным элементам приложения.

> **Важные API**: [класс RevealBrush](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.revealbrush), [класс RevealBackgroundBrush](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.revealbackgroundbrush), [класс RevealBorderBrush](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.revealborderbrush), [RevealBrushHelper class](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.revealbrushhelper), [класс VisualState](https://docs.microsoft.com/en-us/uwp/api/Windows.UI.Xaml.VisualState)

Это происходит путем отображения части элементов вокруг главного (или находящегося в фокусе) содержимого при наведении мыши или прямоугольника фокуса на нужную область.

![Наглядное представление эффекта отображения](images/Nav_Reveal_Animation.gif)

Показывая скрытые границы вокруг объектов, эффект отображения дает пользователям наглядное представление о пространстве, с которым они взаимодействуют, и доступных действиях. Это особенно важно при работе с элементами управления списками и элементами управления с основой.

## <a name="reveal-and-the-fluent-design-system"></a>Отображение и система проектирования Fluent Design

 Система Fluent Design позволяет создавать современные и эффективные пользовательские интерфейсы, которые отличаются яркостью, глубиной, движением, материальностью и масштабированием. Отображение— это компонент системы проектирования Fluent Design, добавляющий свет в ваше приложение. 

## <a name="what-is-reveal"></a>Что такое отображение?

Отображение содержит два визуальных компонента: **отображение при наведении указателя** и **отображение границ**.

![Слои отображения](images/RevealLayers.png)

Отображение при наведении указателя связано непосредственно с содержимым, на которое наводится указатель (или содержимым в фокусе ввода). Вокруг такого элемента появляется легкий ореол, показывающий, что с этим элементом можно взаимодействовать.

Отображение границ применяется к элементу в фокусе и соседним с ним элементам. Это показывает, что такие соседние объекты могут выполнять действия аналогично элементу в фокусе.

Разложив эффект отображения на составляющие, получим:

- отображение границ поверх всего содержимого, но в соответствии с предусмотренными краями;
- текст и содержимое непосредственно под отображаемыми границами;
- слой, обеспечивающий отображение при наведении, под содержимым и текстом;
- основа (активирующая отображение при наведении);
- фон (фон элемента управления).

<!--
<div class=”microsoft-internal-note”>
To create your own Reveal lighting effect for static comps or prototype purposes, see the full [uni design guidance](http://uni/DesignDepot.FrontEnd/#/ProductNav/3020/1/dv/?t=Resources%7CToolkit%7CReveal&f=Neon) for this effect in illustrator.
</div>
-->

## <a name="how-to-use-it"></a>Использование

Эффект отображения проявляет области вокруг курсора, когда нужно, а затем они плавно исчезают, когда курсор движется дальше.

Эффект отображения лучше всего применять к основному содержимому приложения, имеющему скрытые границы и соответствующие им основы. Кроме того, эффект отображения можно использовать с элементами управления коллекции или списка.

## <a name="controls-that-automatically-use-reveal"></a>Элементы управления, автоматически использующие эффект отображения

- [**ListView**](../controls-and-patterns/lists.md)
- [**TreeView**](../controls-and-patterns/tree-view.md)
- [**NavigationView**](../controls-and-patterns/navigationview.md)
- [**AutosuggestBox**](../controls-and-patterns/auto-suggest-box.md)

## <a name="enabling-reveal-on-other-common-controls"></a>Включение отображения для других распространенных элементов управления

Если вы используете подходящий для отображения сценарий (элементы управления представляют основное содержимое и/или расположены в виде списка или коллекции), вы можете воспользоваться стилями из нашего ресурса, чтобы включить отображение в таком сценарии.

Для этих элементов управления отображение не используется по умолчанию, поскольку они не относятся к основным и обычно выполняют роль вспомогательных элементов управления для центральных пунктов приложения. Однако каждое приложение уникально, и если эти элементы активно используются в вашем приложении, мы подготовили для вас несколько подходящих стилей.

| Название элемента управления   | Имя ресурса |
|----------|:-------------:|
| Button |  ButtonRevealStyle |
| ToggleButton | ToggleButtonRevealStyle |
| RepeatButton | RepeatButtonRevealStyle |
| AppBarButton | AppBarButtonRevealStyle |
| SemanticZoom | SemanticZoomRevealStyle |
| ComboBoxItem | ComboxBoxItemRevealStyle |

Чтобы применить эти стили, просто обновите свойство Style таким образом:

```XAML
<Button Content="Button Content" Style="{StaticResource ButtonRevealStyle}"/>
```

> [!NOTE]
> В версии 16190 пакета SDK эти стили недоступны автоматически. Чтобы получить их, необходимо вручную копировать их из файла generic.xaml в приложение. Generic.xaml обычно находится в папке C:\Program Files (x86)\Windows Kits\10\DesignTime\CommonConfiguration\Neutral\UAP\10.0.16190.0\Generic. Эта неполадка будет устранена в более поздней сборке. 

## <a name="enabling-reveal-on-custom-controls"></a>Включение отображения для настраиваемых элементов управления

Чтобы включить отображение для настраиваемых элементов управления или элементов с измененным шаблоном, перейдите к настройкам стиля этого элемента в разделе визуальных состояний его шаблона и укажите отображение в корневой сетке:

```xaml
<VisualState x:Name="PointerOver">
  <VisualState.Setters>
    <Setter Target="RootGrid.(RevealBrushHelper.State)" Value="PointerOver" />
    <Setter Target="RootGrid.Background" Value="{ThemeResource ButtonRevealBackgroundPointerOver}"/>
    <Setter Target="ContentPresenter.BorderBrush" Value="{ThemeResource ButtonRevealBorderBrushPointerOver}"/>
    <Setter Target="ContentPresenter.Foreground" Value="{ThemeResource ButtonForegroundPointerOver}"/>
  </VisualState.Setters>
</VisualState>
```

Чтобы получить эффект отображения для нажатия кнопки, нужно добавить RevealBrushHelper в атрибут PressedState:

```xaml
<VisualState x:Name="Pressed">
  <VisualState.Setters>
    <Setter Target="RootGrid.(RevealBrushHelper.State)" Value="Pressed" />
    <Setter Target="RootGrid.Background" Value="{ThemeResource ButtonRevealBackgroundPressed}"/>
    <Setter Target="ContentPresenter.BorderBrush" Value="{ThemeResource ButtonRevealBorderBrushPressed}"/>
    <Setter Target="ContentPresenter.Foreground" Value="{ThemeResource ButtonForegroundPressed}"/>
  </VisualState.Setters>
</VisualState>
```


Использование эффекта отображения из нашего системного ресурса означает, что мы будем обрабатывать для вас все изменения в этой теме.

## <a name="dos-and-donts"></a>Рекомендации
- Используйте эффект отображения для элементов, с которыми пользователь может выполнять действия. Не применяйте его к статичному содержимому.
- Используйте эффект отображения в списках или коллекциях данных.
- Применяйте отображение к содержимому с четко очерченными границами и основой.
- Не показывайте отображение при использовании статичных фонов, текстов или изображений, с которыми невозможно взаимодействовать.
- Не применяйте отображение к несвязанному плавающему содержимому.
- Не используйте отображение для однократных, независимых сценариев, например для диалоговых окон или уведомлений.
- Не используйте отображение для элементов, связанных с вопросами безопасности. Это может отвлечь внимание от самого сообщения, которое нужно донести до пользователя.

## <a name="related-articles"></a>Статьи по теме

- [Класс RevealBrush](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.revealbrush)
- [**Акрил**](acrylic.md)
- [**Эффекты композиции**](https://msdn.microsoft.com/windows/uwp/graphics/composition-effects)
