---
author: jwmsft
title: Улучшения существующих механизмов взаимодействия ScrollViewer
description: Узнайте, как использовать XAML ScrollViewer и анимации ExpressionAnimation для создания динамичных моделей движения на основе ввода.
ms.author: jimwalk
ms.date: 10/10/2017
ms.topic: article
keywords: windows 10, uwp, анимация
ms.localizationpriority: medium
ms.openlocfilehash: a078d096a9cffe26e9b342250726dd75cdf48817
ms.sourcegitcommit: 3257416aebb5a7b1515e107866806f8bd57845a8
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/17/2018
ms.locfileid: "7151424"
---
# <a name="enhance-existing-scrollviewer-experiences"></a>Улучшения существующих механизмов взаимодействия ScrollViewer

В этой статье рассказывается, как использовать XAML ScrollViewer и анимации ExpressionAnimation для создания динамичных моделей движения на основе ввода.

## <a name="prerequisites"></a>Предварительные требования

Подразумевается, что вы знакомы с понятиями, которые рассматриваются в этих статьях:

- [Анимации на основе ввода](input-driven-animations.md)
- [Анимации на основе отношений](relation-animations.md)

## <a name="why-build-on-top-of-scrollviewer"></a>Причины для создания объектов поверх ScrollViewer

Как правило, существующий инструмент XAML ScrollViewer используется для создания поверхности с возможностью прокрутки и масштабирования для размещения содержимого вашего приложения. С появлением системы проектирования Fluent необходимо обращать внимание и на то, как использовать возможность прокрутки и масштабирования поверхности для воссоздания других элементов движения. Например, с помощью прокрутки можно создать размытую анимацию фона или управлять положением закрепленных заголовков.

В этих сценариях такие типы поведения и взаимодействия с манипуляциями, как прокрутка и масштабирование, используются, чтобы сделать другие части приложения более динамичными. Это, в свою очередь, делает приложение более связным и запоминающимся в глазах конечных пользователей. Делая пользовательский интерфейс приложения более запоминающимся, вы поощряете более активное и продолжительное взаимодействие конечных пользователей с приложением.

## <a name="what-can-you-build-on-top-of-scrollviewer"></a>Что можно создать поверх ScrollViewer

Положение ScrollViewer можно использовать для создания ряда динамических компонентов:

- Параллакс — используйте положение ScrollViewer для перемещения содержимого фона или переднего плана с относительной скоростью в положение прокрутки.
- Закрепленные заголовки— используйте положение ScrollViewer, чтобы сделать заголовок анимированным и "зафиксировать" его в определенном положении
- Эффекты на основе ввода— используйте положение Scrollviewer для анимирования эффекта композиции, например размытия.

Как правило, ссылаясь на положение ScrollViewer в анимации ExpressionAnimation, можно создать динамически меняющуюся относительно величины прокрутки анимацию.

![Представление списка с параллаксом](images/animation/parallax.gif)

![Скрытый заголовок](images/animation/shy-header.gif)

## <a name="using-scrollmanipulationpropertyset"></a>Использование набора свойств ScrollManipulationPropertySet

Для создания указанных динамических взаимодействий с использованием XAML ScrollViewer необходимо иметь возможность сослаться на положение прокрутки в анимации. Для этого нужно воспользоваться набором свойств CompositionPropertySet за пределами XAML ScrollViewer, который называется ScrollManipulationPropertySet.
Набор свойств ScrollManipulationPropertySet содержит одно свойство Vector3 под названием "трансляция", обеспечивающее доступ к расположению прокрутки ScrollViewer. Затем на него можно ссылаться так же, как на любой другой набор свойств CompositionPropertySet в анимации ExpressionAnimation.

Общие инструкции по началу работы:

1. Откройте набор свойств ScrollManipulationPropertySet в ElementCompositionPreview.
    - `ElementCompositionPreview.GetScrollManipulationPropertySet(ScrollViewer scroller)`
1. Создайте анимацию ExpressionAnimation, которая ссылается на свойство Translation в PropertySet.
    - Не забудьте настроить параметр ссылки!
1. Укажите в качестве целевого свойство объекта CompositionObject с ExpressionAnimation.

> [!NOTE]
> Рекомендуется назначить набор PropertySet, возвращаемый из метода GetScrollManipulationPropertySet в переменную класса. Благодаря этому набор свойств не будет удален при сборке мусора и никак не повлияет на анимацию ExpressionAnimation, содержащую ссылку на него. Анимации ExpressionAnimations не поддерживают строгую ссылку ни на какой из объектов, используемых в уравнении.

## <a name="example"></a>Пример

Посмотрим, как создавался параллакс, упомянутый в примере выше. Весь исходный код для этого приложения можно найти в разделе [Репозиторий лабораторных работ для разработчиков пользовательского интерфейса Window в GitHub](https://github.com/Microsoft/WindowsUIDevLabs).

Сначала нужно получить ссылку на ScrollManipulationPropertySet.

```csharp
_scrollProperties =
    ElementCompositionPreview.GetScrollViewerManipulationPropertySet(myScrollViewer);
```

Затем необходимо создать анимацию ExpressionAnimation, которая определяет выражение, где используется положение прокрутки ScrollViewer.

```csharp
_parallaxExpression = compositor.CreateExpressionAnimation();
_parallaxExpression.SetScalarParameter("StartOffset", 0.0f);
_parallaxExpression.SetScalarParameter("ParallaxValue", 0.5f);
_parallaxExpression.SetScalarParameter("ItemHeight", 0.0f);
_parallaxExpression.SetReferenceParameter("ScrollManipulation", _scrollProperties);
_parallaxExpression.Expression = "(ScrollManipulation.Translation.Y + StartOffset - (0.5 * ItemHeight)) * ParallaxValue - (ScrollManipulation.Translation.Y + StartOffset - (0.5 * ItemHeight))";
```

> [!NOTE]
> Можно также использовать вспомогательные классы ExpressionBuilder для создания того же выражения без необходимости использовать строки:

> ```csharp
> var scrollPropSet = _scrollProperties.GetSpecializedReference<ManipulationPropertySetReferenceNode>();
> var parallaxValue = 0.5f;
> var parallax = (scrollPropSet.Translation.Y + startOffset);
> _parallaxExpression = parallax * parallaxValue - parallax;
> ```

Наконец, в анимации ExpressionAnimation можно в качестве целевого объекта указать визуальный элемент, который будет использоваться в параллаксе. В данном случае это изображение всех элементов в списке.

```csharp
Visual visual = ElementCompositionPreview.GetElementVisual(image);
visual.StartAnimation("Offset.Y", _parallaxExpression);
```