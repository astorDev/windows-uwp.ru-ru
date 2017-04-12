---
author: scottmill
ms.assetid: 03dd256f-78c0-e1b1-3d9f-7b3afab29b2f
title: "Кисти композиции"
description: "Кисть заполняет пространство объекта класса Visual своими выводимыми данными. Разные кисти имеют различные типы выводимых данных."
ms.author: scotmi
ms.date: 02/08/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, uwp
ms.openlocfilehash: 71ffd720dac0be29f003da965054db16ae8e39c0
ms.sourcegitcommit: 909d859a0f11981a8d1beac0da35f779786a6889
translationtype: HT
---
# <a name="composition-brushes"></a>Кисти композиции

\[ Обновлено для приложений UWP в Windows10. Статьи, касающиеся Windows8.x, см. в разделе [Архив](http://go.microsoft.com/fwlink/p/?linkid=619132) \]

Кисть заполняет пространство объекта класса [**Visual**](https://msdn.microsoft.com/library/windows/apps/Dn706858) своими выводимыми данными. Разные кисти имеют различные типы выводимых данных. В API композиции представлены три типа кистей.

-   [**CompositionColorBrush**](https://msdn.microsoft.com/library/windows/apps/Mt589399) закрашивает визуальный объект сплошным цветом
-   [**CompositionSurfaceBrush**](https://msdn.microsoft.com/library/windows/apps/Mt589415) заполняет визуальный объект содержимым поверхности композиции
-   [**CompositionEffectBrush**](https://msdn.microsoft.com/library/windows/apps/Mt589406) заполняет визуальный объект содержимым эффекта композиции

Все кисти наследуются от [**CompositionBrush**](https://msdn.microsoft.com/library/windows/apps/Mt589398). Они создаются прямо или косвенно с помощью объекта [**Compositor**](https://msdn.microsoft.com/library/windows/apps/Dn706789) и являются ресурсами, не зависящими от устройства. Несмотря на то, что кисти являются независимыми от устройства ресурсами, [**CompositionSurfaceBrush**](https://msdn.microsoft.com/library/windows/apps/Mt589415) и [**CompositionEffectBrush**](https://msdn.microsoft.com/library/windows/apps/Mt589406) заполняют объект класса [**Visual**](https://msdn.microsoft.com/library/windows/apps/Dn706858) содержимым поверхности композиции, которая является ресурсом, зависимым от устройства.

-   [Предварительные условия и необходимые компоненты](./composition-brushes.md#prerequisites)
-   [Основные сведения о цвете](./composition-brushes.md#color-basics)
    -   [Режимы альфа-канала](./composition-brushes.md#alpha-modes)
-   [Использование цветной кисти](./composition-brushes.md#using-color-brush)
-   [Использование кисти поверхности](./composition-brushes.md#using-surface-brush)
-   [Настройка растяжения и выравнивания](./composition-brushes.md#configuring-stretch-and-alignment)

## <a name="prerequisites"></a>Предварительные условия и необходимые компоненты

Материал данного раздела предполагает, что вы знакомы со структурой базового приложения композиции, которая описана в разделе [Пользовательский интерфейс композиции](visual-layer.md).

## <a name="color-basics"></a>Основные сведения о цвете

Перед тем, как начать рисовать с помощью [**CompositionColorBrush**](https://msdn.microsoft.com/library/windows/apps/Mt589399), необходимо выбрать цвета. Для представления цвета в композиции API используется структура Color среды выполнения Windows. В структуре Color используется кодирование sRGB. Кодирование sRGB делит цвета на четыре канала: альфа, красный, зеленый и синий. Каждый компонент представлен значением с плавающей запятой, которое имеет нормальный диапазон от 0,0 до 1,0. Значение 0,0 указывает на полное отсутствие данного цвета, а значение 1,0 указывает на его максимальное присутствие. Для альфа-компонента значение 0,0 означает полностью прозрачный цвет, а 1,0 — полностью непрозрачный цвет.

### <a name="alpha-modes"></a>Режимы альфа-канала

Значения цвета в [**CompositionColorBrush**](https://msdn.microsoft.com/library/windows/apps/Mt589399) всегда интерпретируются как прямые альфа-значения.

## <a name="using-color-brush"></a>Использование цветной кисти

Чтобы создать цветную кисть, вызовите метод Compositor.[**CreateColorBrush**](https://msdn.microsoft.com/library/windows/apps/windows.ui.composition.compositor.createcolorbrush.aspx), который возвращает объект [**CompositionColorBrush**](https://msdn.microsoft.com/library/windows/apps/Mt589399). В **CompositionColorBrush** используется цвет по умолчанию с кодом \#00000000. Следующий рисунок и фрагмент кода демонстрируют небольшое визуальное дерево для создания прямоугольника, заштрихованного с помощью кисти черного цвета и заполненного с помощью кисти сплошного цвета с кодом 0x9ACD32.

![CompositionColorBrush](images/composition-compositioncolorbrush.png)
```cs
Compositor _compositor;
ContainerVisual _container;
SpriteVisual visual1, visual2;
CompositionColorBrush _blackBrush, _greenBrush; 

_compositor = new Compositor();
_container = _compositor.CreateContainerVisual();

_blackBrush = _compositor.CreateColorBrush(Colors.Black);
visual1 = _compositor.CreateSpriteVisual();
visual1.Brush = _blackBrush;
visual1.Size = new Vector2(156, 156);
visual1.Offset = new Vector3(0, 0, 0);

_ greenBrush = _compositor.CreateColorBrush(Color.FromArgb(0xff, 0x9A, 0xCD, 0x32));
Visual2 = _compositor.CreateSpriteVisual();
Visual2.Brush = _greenBrush;
Visual2.Size = new Vector2(150, 150);
Visual2.Offset = new Vector3(3, 3, 0);
```

В отличие от других кистей, создание кисти [**CompositionColorBrush**](https://msdn.microsoft.com/library/windows/apps/Mt589399) является относительно несложной операцией. Вы можете создавать объекты **CompositionColorBrush** каждый раз при выполнении отрисовки, практически не влияя на производительность.

## <a name="using-surface-brush"></a>Использование кисти поверхности

Кисть [**CompositionSurfaceBrush**](https://msdn.microsoft.com/library/windows/apps/Mt589415) заполняет визуальный объект поверхностью композиции (представляемой объектом [**ICompositionSurface**](https://msdn.microsoft.com/library/windows/apps/Dn706819)). На следующем рисунке показан квадратный визуальный объект, заполненный растровым изображением лакрицы, отрисованным на поверхности **ICompositionSurface** с помощью D2D.

![CompositionSurfaceBrush](images/composition-compositionsurfacebrush.png) В первом примере поверхность композиции инициализируется для применения кисти. Поверхность композиции создается с помощью вспомогательного метода LoadImage, в который передаются объект [**CompositionSurfaceBrush**](https://msdn.microsoft.com/library/windows/apps/Mt589415) и URL-адрес в виде строки. Этот метод загружает изображение из URL-адреса, отрисовывает изображение на поверхности [**ICompositionSurface**](https://msdn.microsoft.com/library/windows/apps/Dn706819) и задает поверхность в качестве содержимого кисти **CompositionSurfaceBrush**. Обратите внимание, что **ICompositionSurface** предоставляется только в собственном машинном коде, поэтому метод LoadImage реализуется в нем же.

```cs
LoadImage(Brush,
          "ms-appx:///Assets/liqorice.png");
```

Для создания кисти поверхности вызовите метод Compositor.[**CreateSurfaceBrush**](https://msdn.microsoft.com/library/windows/apps/windows.ui.composition.compositor.createsurfacebrush.aspx). Этот метод возвращает объект [**CompositionSurfaceBrush**](https://msdn.microsoft.com/library/windows/apps/Mt589415). Ниже показан код, который можно использовать для заполнения визуального объекта содержимым кисти **CompositionSurfaceBrush**.

```cs
Compositor _compositor;
ContainerVisual _container;
SpriteVisual visual;
CompositionSurfaceBrush _surfaceBrush;

_surfaceBrush = _compositor.CreateSurfaceBrush();
LoadImage(_surfaceBrush, "ms-appx:///Assets/liqorice.png");
visual.Brush = _surfaceBrush;
```

## <a name="configuring-stretch-and-alignment"></a>Настройка растяжения и выравнивания

Иногда содержимое поверхности [**ICompositionSurface**](https://msdn.microsoft.com/library/windows/apps/Dn706819) для кисти [**CompositionSurfaceBrush**](https://msdn.microsoft.com/library/windows/apps/Mt589415) не полностью заполняет площадь обрабатываемого визуального объекта. В этом случае API композиции использует параметры режима [**HorizontalAlignmentRatio**](https://msdn.microsoft.com/library/windows/apps/windows.ui.composition.compositionsurfacebrush.horizontalalignmentratio.aspx), [**VerticalAlignmentRatio**](https://msdn.microsoft.com/library/windows/apps/windows.ui.composition.compositionsurfacebrush.verticalalignmentratio) и [**Stretch**](https://msdn.microsoft.com/library/windows/apps/windows.ui.composition.compositionsurfacebrush.stretch), чтобы определить, как заполнить оставшуюся область.

-   [**HorizontalAlignmentRatio**](https://msdn.microsoft.com/library/windows/apps/windows.ui.composition.compositionsurfacebrush.horizontalalignmentratio.aspx) и [**VerticalAlignmentRatio**](https://msdn.microsoft.com/library/windows/apps/windows.ui.composition.compositionsurfacebrush.verticalalignmentratio) имеют тип float и используются для позиционирования кисти в пределах визуального объекта.
    -   Значение 0,0 переводит левый верхний угол кисти в левый верхний угол визуального объекта.
    -   Значение 0,5 переводит центр кисти в центр визуального объекта.
    -   Значение 1,0 переводит правый нижний угол кисти в правый нижний угол визуального объекта.
-   Для свойства [**Stretch**](https://msdn.microsoft.com/library/windows/apps/windows.ui.composition.compositionsurfacebrush.stretch) используются следующие значения, определенные в перечислении [**CompositionStretch**](https://msdn.microsoft.com/library/windows/apps/Dn706786).
    -   None: кисть не растягивается для заполнения границ визуального объекта. Будьте осторожны с этим значением параметра Stretch: если размер кисти превышает границы визуального объекта, содержимое кисти будет обрезано. Частью кисти, которая используется для заполнения границ визуального объекта, можно управлять с помощью свойств [**HorizontalAlignmentRatio**](https://msdn.microsoft.com/library/windows/apps/windows.ui.composition.compositionsurfacebrush.horizontalalignmentratio.aspx) и [**VerticalAlignmentRatio**](https://msdn.microsoft.com/library/windows/apps/windows.ui.composition.compositionsurfacebrush.verticalalignmentratio).
    -   Uniform: кисть масштабируется таким образом, чтобы она помещалась в границах визуального объекта; пропорции кисти сохраняются. Это значение используется по умолчанию.
    -   UniformToFill: кисть масштабируется таким образом, чтобы она полностью заполняла границы визуального объекта; пропорции кисти сохраняются.
    -   Fill: кисть масштабируется таким образом, чтобы она помещалась в границах визуального объекта. Так как высота и ширина кисти изменяются независимо друг от друга, ее исходные пропорции могут быть нарушены. Таким образом, кисть может быть искажена для максимального заполнения границ визуального объекта.

 

## <a name="related-topics"></a>Статьи по теме
[Собственное взаимодействие DirectX и Direct2D композиции с BeginDraw и EndDraw](composition-native-interop.md)




