---
author: Jwmsft
title: Анимация свойств XAML
description: Анимация элементов XAML с анимациями композиции.
ms.author: jimwalk
ms.date: 09/13/2018
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, uwp
pm-contact: stmoy
design-contact: jeffarn
ms.localizationpriority: medium
ms.openlocfilehash: a03ffc8d5ea78ee6cbdf78feaae7ba1cd1448f37
ms.sourcegitcommit: d10fb9eb5f75f2d10e1c543a177402b50fe4019e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/12/2018
ms.locfileid: "4568904"
---
# <a name="animating-xaml-elements-with-composition-animations"></a>Анимация элементов XAML с анимациями композиции

В этой статье представлены новые свойства, которые позволяют анимировать UIElement для XAML с производительность анимации композиции и простоту свойства XAML.

До выхода Windows 10, версия 1809, было 2 вариантов для создания анимации в приложениях UWP.

- Использование конструкции XAML как [Раскадрованные анимации](storyboarded-animations.md)или _* текст ThemeTransition_ и _* текст ThemeAnimation_ классы в пространстве имен [Windows.UI.Xaml.Media.Animation](/uwp/api/windows.ui.xaml.media.animation) .
- Используйте анимации композиции, как описано в [с использованием визуального уровня с помощью XAML](../../composition/using-the-visual-layer-with-xaml.md).

Использование визуальный уровень обеспечивает более высокую производительность, чем с помощью XAML выполняет построение. Однако с помощью [ElementCompositionPreview](/uwp/api/Windows.UI.Xaml.Hosting.ElementCompositionPreview) , чтобы получить базовый объект [визуальных](/uwp/api/windows.ui.composition.visual) элементов композиции, а затем анимация Visual с анимациями композиции, является более сложным для использования.

Начиная с Windows 10, версия 1809, вы можете анимировать свойства в элементе UIElement, непосредственно с помощью анимации композиции без необходимости для получения базовых композиции Visual.

> [!NOTE]
> Чтобы использовать эти свойства на элемент UIElement, своей целевой версии проекта UWP необходимо 1809 или более поздней версии. Дополнительные сведения о настройке версия вашего проекта см. в разделе [адаптивные к версии приложения](../../debug-test-perf/version-adaptive-apps.md).

## <a name="new-rendering-properties-replace-old-rendering-properties"></a>Новые свойства отрисовки Замените старый свойства отрисовки

В этой таблице показаны свойства, которые можно использовать для изменения отрисовки UIElement, который также можно анимировать с помощью [CompositionAnimation](/uwp/api/windows.ui.composition.compositionanimation).

| Свойство | Тип | Описание |
| -- | -- | -- |
| [Opacity (Прозрачность)](/uwp/api/windows.ui.xaml.uielement.opacity) | Double | Степень непрозрачности объекта |
| [Translation (Преобразование)](/uwp/api/windows.ui.xaml.uielement.translation) | Vector3 (Вектор 3) | Положение X и Y и Z элемента |
| [TransformMatrix](/uwp/api/windows.ui.xaml.uielement.transformmatrix) | Matrix4x4 | Матрица преобразования, чтобы применить к элементу |
| [Scale (Масштаб)](/uwp/api/windows.ui.xaml.uielement.scale) | Vector3 (Вектор 3) | Масштаб элемента, по центру относительно центральной точки |
| [Поворот](/uwp/api/windows.ui.xaml.uielement.rotation) | Плавающий | Поворот элемента вокруг RotationAxis и CenterPoint |
| [RotationAxis (Ось поворота)](/uwp/api/windows.ui.xaml.uielement.rotationaxis) | Vector3 (Вектор 3) | А именно оси вращения |
| [CenterPoint (Центральная точка)](/uwp/api/windows.ui.xaml.uielement.centerpoint) | Vector3 (Вектор 3) | Центральная точка масштабирования и поворота |

Значение свойства TransformMatrix используется в сочетании со свойствами масштаб, поворот и перевода в следующем порядке: TransformMatrix, масштабирования, поворота, перевода.

Эти свойства не влияют на макет элемента, поэтому изменение этих свойств не приведет к новой [меры](/uwp/api/windows.ui.xaml.uielement.measure)/[упорядочения](/uwp/api/windows.ui.xaml.uielement.arrange) .

Эти свойства имеют те же цель и поведение как свойства с именем как в композиции класса [Visual](/uwp/api/windows.ui.composition.visual) (за исключением трансляции, который не на Visual).

### <a name="example-setting-the-scale-property"></a>Пример: Задание свойства масштабирования

В этом примере показано, как задать свойство масштабирования элемента "Button".

```xaml
<Button Scale="2,2,1" Content="I am a large button" />
```

```csharp
var button = new Button();
button.Content = "I am a large button";
button.Scale = new Vector3(2.0f,2.0f,1.0f);
```

### <a name="mutual-exclusivity-between-new-and-old-properties"></a>Взаимной монопольности между старыми и свойства

> [!NOTE]
> Свойства **Opacity** применение взаимной монопольности, описанные в этом разделе. Можно использовать одно и то же свойство Opacity использования XAML или композиции анимаций.

Свойства, которые можно анимировать с CompositionAnimation приведены замены для нескольких существующих свойств UIElement.

- [RenderTransform](/uwp/api/windows.ui.xaml.uielement.rendertransform)
- [RenderTransformOrigin](/uwp/api/windows.ui.xaml.uielement.rendertransformorigin)
- [Projection](/uwp/api/windows.ui.xaml.uielement.projection)
- [Transform3D](/uwp/api/windows.ui.xaml.uielement.transform3d)

Задайте (или анимации) какие-либо новые свойства, нельзя использовать старые свойства. И наоборот Если необходимо установить (или анимации) старого свойства, невозможно использовать новые свойства.

Новые свойства также нельзя использовать, если вы используете ElementCompositionPreview и визуального самостоятельно с помощью следующих методов:

- [ElementCompositionPreview.GetElementVisual](/uwp/api/windows.ui.xaml.hosting.elementcompositionpreview.getelementvisual)
- [ElementCompositionPreview.SetIsTranslationEnabled](/uwp/api/windows.ui.xaml.hosting.elementcompositionpreview.setistranslationenabled)

> [!IMPORTANT]
> Попытка смешивать использование двух наборов свойств вызовет вызова API для сбоем и сообщения об ошибке.

Существует возможность переход с один набор свойств, сняв их, но для простоты не рекомендуется. Если свойство соответствует DependencyProperty (например, UIElement.Projection соответствует UIElement.ProjectionProperty), затем вызовите метод ClearValue позволяет восстановить его в состоянии «неиспользуемые». В противном случае (например, свойство масштаб), задайте свойство значение по умолчанию.

## <a name="animating-uielement-properties-with-compositionanimation"></a>Анимирование свойства UIElement с CompositionAnimation

Вы можете анимировать свойства отрисовки, указанные в таблице с CompositionAnimation. Эти свойства также будут ссылаться анимацию [ExpressionAnimation](/uwp/api/windows.ui.composition.expressionanimation).

Используйте методы [StartAnimation](/uwp/api/windows.ui.xaml.uielement.startanimation) и [StopAnimation](/uwp/api/windows.ui.xaml.uielement.stopanimation) UIElement анимировать свойства UIElement.

### <a name="example-animating-the-scale-property-with-a-vector3keyframeanimation"></a>Пример: Анимация свойство масштаба с помощью Vector3KeyFrameAnimation

В этом примере показано, как анимировать масштаб кнопки.

```csharp
var compositor = Window.Current.Compositor;
var animation = compositor.CreateVector3KeyFrameAnimation();

animation.InsertKeyFrame(1.0f, new Vector3(2.0f,2.0f,1.0f));
animation.Duration = TimeSpan.FromSeconds(1);
animation.Target = "Scale";

button.StartAnimation(animation);
```

### <a name="example-animating-the-scale-property-with-an-expressionanimation"></a>Пример: Анимация свойств масштабирования в анимации ExpressionAnimation

Страница содержит две кнопки. Второй кнопке анимирует дважды быть как большим (через scale) как первый вариант.

```xaml
<Button x:Name="sourceButton" Content="Source"/>
<Button x:Name="destinationButton" Content="Destination"/>
```

```csharp
var compositor = Window.Current.Compositor;
var animation = compositor.CreateExpressionAnimation("sourceButton.Scale*2");
animation.SetExpressionReferenceParameter("sourceButton", sourceButton);
animation.Target = "Scale";
destinationButton.StartAnimation(animation);
```

## <a name="related-topics"></a>Еще по теме

- [Раскадрованные анимации](storyboarded-animations.md)
- [Использование визуального уровня с помощью XAML](../../composition/using-the-visual-layer-with-xaml.md)
- [Обзор преобразований](../layout/transforms.md)