---
author: jwmsft
title: "Обновление путем оттягивания с модификаторами источника"
description: "Создание пользовательских элементов управления обновлением путем оттягивания с использованием модификаторов SourceModifiers"
ms.author: jimwalk
ms.date: 10/10/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: "windows 10, uwp, анимация"
ms.localizationpriority: medium
ms.openlocfilehash: 1d85dbe89fe90723c3475b77c6057def684bd60d
ms.sourcegitcommit: f9a4854b6aecfda472fb3f8b4a2d3b271b327800
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/12/2017
---
# <a name="pull-to-refresh-with-source-modifiers"></a>Обновление путем оттягивания с модификаторами источника

В этой статье более подробно рассматривается использование функции SourceModifier в составе InteractionTracker и демонстрируется ее использование путем создания пользовательского элемента управления обновлением путем оттягивания.

## <a name="prerequisites"></a>Предварительные требования

Подразумевается, что вы знакомы с понятиями, которые рассматриваются в этих статьях:

- [Анимации на основе ввода](input-driven-animations.md)
- [Пользовательские взаимодействия с манипуляциями, выполняемые с помощью InteractionTracker](interaction-tracker-manipulations.md)
- [Анимации на основе связей](relation-animations.md)

## <a name="what-is-a-sourcemodifier-and-why-are-they-useful"></a>Что такое SourceModifier и в чем польза этих элементов?

Как и модификаторы [InertiaModifiers](inertia-modifiers.md), SourceModifiers обеспечивают более точный контроль над перемещением InteractionTracker. Однако в отличие от InertiaModifiers, определяющих движение после перехода InteractionTracker в состояние инерции, SourceModifiers определяют движение InteractionTracker в состоянии взаимодействия. В этих случаях требуется взаимодействие, отличное от традиционного "прилипания к пальцу",

Классический пример этого— обновление путем оттягивания, когда пользователь оттягивает список, чтобы обновить его содержимое, и список сдвигается с той же скоростью, что и палец, и останавливается, пройдя определенное расстояние. Такое движение выглядит резким и механическим. Гораздо более естественное взаимодействие— это ощущение сопротивления, когда список активно взаимодействует со списком. Этот небольшой нюанс позволяет сделать взаимодействие конечного пользователя со списком более динамическим и привлекательным. В разделе "Пример" более подробно рассматривается построение этого взаимодействия.

Существует два типа модификаторов источника:

- DeltaPosition— это разница между текущим и предыдущим положением кадра для пальца во время взаимодействия "сдвиг касанием". Этот модификатор источника позволяет модифицировать дельта-положение взаимодействия перед отправкой взаимодействия для дальнейшей обработки. Это параметр типа Vector3, и разработчик может изменить любые атрибуты (X, Y или Z) положения, прежде чем передавать его InteractionTracker.
- DeltaScale— это разница между текущим и предыдущим масштабом кадра, которая применялась во время взаимодействия "масштабирование касанием". Этот модификатор источника позволяет изменять уровень масштаба взаимодействия. Это атрибут плавающего типа, который разработчик может изменить, прежде чем передавать в InteractionTracker.

Если InteractionTracker находится в состоянии взаимодействия, он оценивает все назначенные ему модификаторы источника и определяет, применяются ли какие-либо из них. Это означает, что модулю InteractionTracker можно назначить несколько созданных модификаторов источника. Однако при определении каждого из них необходимо сделать следующее:

1. Определите состояние — выражение, которое определяет условный оператор, при котором должен применяться конкретный модификатор источника.
1. Определите параметр DeltaPosition/DeltaScale— выражение модификатора источника, которое меняет параметр DeltaPosition или DeltaScale, если определенное выше условие выполнено.

## <a name="example"></a>Пример.

Посмотрим, как можно использовать модификаторы источника, чтобы создать пользовательское взаимодействие "обновление путем оттягивания" с существующим элементом управления XAML ListView. Для создания этого взаимодействия мы будем использовать холст в качестве панели обновления, которая будет располагаться над элементом XAML ListView.

Для конечного пользователя мы хотим создать взаимодействие с эффектом "сопротивления": пользователь активно сдвигает список касанием и перестает сдвигать его после того, как положение списка пересечет определенную точку.

![Список с обновлением путем оттягивания](images/animation/city-list.gif)

Рабочий код для этого взаимодействия можно найти в [репозитории лабораторных работ по разработке пользовательского интерфейса Window в GitHub](https://github.com/Microsoft/WindowsUIDevLabs). Это пошаговое руководство по созданию такого взаимодействия.
В коде разметки XAML присутствуют следующие элементы:

```xaml
<StackPanel Height="500" MaxHeight="500" x:Name="ContentPanel" HorizontalAlignment="Left" VerticalAlignment="Top" >
 <Canvas Width="400" Height="100" x:Name="RefreshPanel" >
<Image x:Name="FirstGear" Source="ms-appx:///Assets/Loading.png" Width="20" Height="20" Canvas.Left="200" Canvas.Top="70"/>
 </Canvas>
 <ListView x:Name="ThumbnailList"
 MaxWidth="400"
 Height="500"
ScrollViewer.VerticalScrollMode="Enabled" ScrollViewer.IsScrollInertiaEnabled="False" ScrollViewer.IsVerticalScrollChainingEnabled="True" >
 <ListView.ItemTemplate>
 ……
 </ListView.ItemTemplate>
 </ListView>
</StackPanel>
```

Поскольку ListView (`ThumbnailList`)— это элемент управления XAML, который уже прокручивается, прокрутка должна быть связана с родительским элементом (`ContentPanel`), когда достигается элемент самого верхнего уровня и дальнейшая прокрутка невозможна. (Модификаторы источника применяются на панели ContentPanel.) Для этого необходимо задать в разметке ListView значение **true** для свойства ScrollViewer.IsVerticalScrollChainingEnabled. Кроме того, потребуется задать значение **Всегда** для режима сцепления в свойстве VisualInteractionSource.

Необходимо задать значение **true** для обработчика PointerPressedEvent с параметром _handledEventsToo_. Без этого обработчик PointerPressedEvent не будет привязан к панели ContentPanel, поскольку элемент управления ListView пометит эти события как обработанные и они не будут отправлены по цепочке визуальных элементов.

```csharp
//The PointerPressed handler needs to be added using AddHandler method with the //handledEventsToo boolean set to "true"
//instead of the XAML element's "PointerPressed=Window_PointerPressed",
//because the list view needs to chain PointerPressed handled events as well.
ContentPanel.AddHandler(PointerPressedEvent, new PointerEventHandler( Window_PointerPressed), true);
```

Теперь вы готовы связать все это с InteractionTracker. Для начала настройте классы InteractionTracker, VisualInteractionSource и Expression, в которых будет использоваться положение InteractionTracker.

```csharp
// InteractionTracker and VisualInteractionSource setup.
_root = ElementCompositionPreview.GetElementVisual(Root);
_compositor = _root.Compositor;
_tracker = InteractionTracker.Create(_compositor);
_interactionSource = VisualInteractionSource.Create(_root);
_interactionSource.PositionYSourceMode = InteractionSourceMode.EnabledWithInertia;
_interactionSource.PositionYChainingMode = InteractionChainingMode.Always;
_tracker.InteractionSources.Add(_interactionSource);
float refreshPanelHeight = (float)RefreshPanel.ActualHeight;
_tracker.MaxPosition = new Vector3((float)Root.ActualWidth, 0, 0);
_tracker.MinPosition = new Vector3(-(float)Root.ActualWidth, -refreshPanelHeight, 0);

// Use the Tacker's Position (negated) to apply to the Offset of the Image.
// The -{refreshPanelHeight} is to hide the refresh panel
m_positionExpression = _compositor.CreateExpressionAnimation($"-tracker.Position.Y - {refreshPanelHeight} ");
m_positionExpression.SetReferenceParameter("tracker", _tracker);
_contentPanelVisual.StartAnimation("Offset.Y", m_positionExpression);
```

После настройки этих параметров панель обновления будет находиться за пределами окна просмотра в исходном положении, и пользователь увидит только представление списка listView. Когда сдвиг достигнет панели ContentPanel, будет запущено событие PointerPressed, в котором вы укажете системе на необходимость использовать InteractionTracker для реализации указанного взаимодействия с манипуляциями.

```csharp
private void Window_PointerPressed(object sender, PointerRoutedEventArgs e)
{
if (e.Pointer.PointerDeviceType == Windows.Devices.Input.PointerDeviceType.Touch) {
 // Tell the system to use the gestures from this pointer point (if it can).
 _interactionSource.TryRedirectForManipulation(e.GetCurrentPoint(null));
 }
}
```

> [!NOTE]
> Если связывать обработанные события не требуется, добавить обработчик PointerPressedEvent можно непосредственно с помощью разметки XAML, используя атрибут (`PointerPressed="Window_PointerPressed"`).

Следующий шаг— настройка модификаторов источника. Для получения этого поведения вы используете 2 модификатора источника: _сопротивление_ и _остановка_.

- Сопротивление: перемещайте DeltaPosition.Y с половинной скоростью, пока объект не достигнет высоты RefreshPanel.

```csharp
CompositionConditionalValue resistanceModifier = CompositionConditionalValue.Create (_compositor);
ExpressionAnimation resistanceCondition = _compositor.CreateExpressionAnimation(
 $"-tracker.Position.Y < {pullToRefreshDistance}");
resistanceCondition.SetReferenceParameter("tracker", _tracker);
ExpressionAnimation resistanceAlternateValue = _compositor.CreateExpressionAnimation(
 "source.DeltaPosition.Y / 3");
resistanceAlternateValue.SetReferenceParameter("source", _interactionSource);
resistanceModifier.Condition = resistanceCondition;
resistanceModifier.Value = resistanceAlternateValue;
```

- Остановка: прекратите перемещение, после того как вся RefreshPanel окажется на экране.

```csharp
CompositionConditionalValue stoppingModifier = CompositionConditionalValue.Create (_compositor);
ExpressionAnimation stoppingCondition = _compositor.CreateExpressionAnimation(
 $"-tracker.Position.Y >= {pullToRefreshDistance}");
stoppingCondition.SetReferenceParameter("tracker", _tracker);
ExpressionAnimation stoppingAlternateValue = _compositor.CreateExpressionAnimation("0");
stoppingModifier.Condition = stoppingCondition;
stoppingModifier.Value = stoppingAlternateValue;
Now add the 2 source modifiers to the InteractionTracker.
List<CompositionConditionalValue> modifierList = new List<CompositionConditionalValue>()
{ resistanceModifier, stoppingModifier };
_interactionSource.ConfigureDeltaPositionYModifiers(modifierList);
```

Эта схема обеспечивает визуализацию настройки SourceModifiers.

![Схема сдвига](images/animation/source-modifiers-diagram.png)

При использовании SourceModifiers вы заметите, что при сдвиге ListView вниз и достижении самого верхнего элемента панель обновления оттягивается вниз с половинным темпом сдвига, пока не достигнет высоты RefreshPanel, после чего панель перестает двигаться.

В полном примере анимация ключевого кадра используется для вращения значка во время взаимодействия на холсте RefreshPanel. Вместо этого можно использовать любое содержимое; кроме того, можно использовать положение InteractionTracker для отдельного выполнения этой анимации.
