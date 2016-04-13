---
ms.assetid: 386faf59-8f22-2e7c-abc9-d04216e78894
title: Анимации композиции
description: Многие свойства объектов композиции и эффектов можно анимировать, используя анимацию по ключевым кадрам или анимацию с помощью выражений. Это позволяет изменять свойства элементов пользовательского интерфейса во времени или на основе расчетов.
---
# Анимации композиции

\[ Обновлено для приложений UWP в Windows 10. Статьи о Windows 8.x см. в разделе [Архив](http://go.microsoft.com/fwlink/p/?linkid=619132) \]

Многие свойства объектов композиции и эффектов можно анимировать, используя анимацию по ключевым кадрам или анимацию с помощью выражений. Это позволяет изменять свойства элементов пользовательского интерфейса во времени или на основе расчетов. Существует два типа анимации: анимация по ключевым кадрам, представляемая классом [**KeyFrameAnimation**](https://msdn.microsoft.com/library/windows/apps/Dn706830), и анимация с помощью выражений, представляемая классом [**ExpressionAnimation**](https://msdn.microsoft.com/library/windows/apps/Dn706817).

-   [Свойства, поддерживающие анимацию](./composition-animation.md#animatable_properties)
-   [Анимация по ключевым кадрам](./composition-animation.md#keyframe-animations)
    -   [Создание собственной анимации и ключевых кадров](./composition-animation.md#creating-your-animation-and-Keyframes)
    -   [Свойства ключевых кадров](./composition-animation.md#keyframe-properties)
    -   [Функции для реалистичной анимации](./composition-animation.md#easing-functions)
    -   [Запуск и остановка анимации по ключевым кадрам](./composition-animation.md#starting-and-stopping-keyframe-animations)
    -   [События завершения анимации](./composition-animation.md#animation-completion-events)
-   [Анимация с помощью выражений](./composition-animation.md#expression-animations)
    -   [Создание выражений](./composition-animation.md#creating-your-expression)
    -   [Наборы свойств](./composition-animation.md#property-sets)
    -   [Ключевые кадры выражения](./composition-animation.md#expression_keyframes)

## Свойства, поддерживающие анимацию

Следующие свойства поддерживают анимацию путем их связывания с анимацией по ключевым кадрам или анимацией на основе выражений.

-   CompositionColorBrush.Color
-   InsetClip.BottomInset
-   InsetClip.LeftInset
-   InsetClip.RightInset
-   InsetClip.TopInset
-   Visual.AnchorPoint
-   Visual.CenterPoint
-   Visual.Offset
-   Visual.Opacity
-   Visual.Orientation
-   Visual.RotationAngle
-   Visual.RotationAxis
-   Visual.Size
-   Visual.TransformMatrix
-   CompositionPropertySet

## Анимация по ключевым кадрам

Анимация по ключевым кадрам — это анимация с привязкой ко времени, в которой используются один или несколько ключевых кадров, указывающих изменение значения во времени. Кадры представляют собой метки, позволяющие определять, каким должно быть анимированное значение в определенный момент времени.

### Создание собственной анимации и ключевых кадров

Для создания анимации по ключевым кадрам используйте один из методов-конструкторов класса Compositor, который соответствует типу структуры того свойства, которое вы хотите анимировать.

-   [**CreateColorKeyFrameAnimation**](https://msdn.microsoft.com/library/windows/apps/Mt589424)
-   [**CreateQuaternionKeyFrameAnimation**](https://msdn.microsoft.com/library/windows/apps/Mt517858)
-   [**CreateScalarKeyFrameAnimation**](https://msdn.microsoft.com/library/windows/apps/Dn706803)
-   [**CreateVector2KeyFrameAnimation**](https://msdn.microsoft.com/library/windows/apps/Dn706806)
-   [**CreateVector3KeyFrameAnimation**](https://msdn.microsoft.com/library/windows/apps/Dn706807)
-   [**CreateVector4KeyFrameAnimation**](https://msdn.microsoft.com/library/windows/apps/Dn706808)

Например, в следующем фрагменте кода создается анимация по ключевым кадрам Vector3.

```cs
var animation = _compositor.CreateVector3KeyFrameAnimation();
```

Каждый ключевой кадр создается путем вызова метода InsertKeyFrame класса KeyFrameAnimation и указания двух компонентов, а также (при необходимости) третьего компонента.

-   Time (Время): нормализованное состояние изменения ключевого кадра (значение — от 0,0 до 1,0)
-   Value (Значение): анимируемое значение в конкретный момент времени
-   Easing Function (Функция для реалистичной анимации) (необязательный компонент): функция для описания интерполяции между предыдущим и текущим ключевым кадром (будет рассмотрена позже)

В следующем фрагменте кода ключевой кадр вставляется в анимацию Vector3KeyFrameAnimation, которая будет активирована в процессе анимации.

```cs
animation.InsertKeyFrame(0.5f, new Vector3(50.0f, 80.0f, 0.0f));
```

### Свойства ключевых кадров

Определив анимацию по ключевым кадрам и отдельные ключевые кадры, можно указать различные свойства анимации.

-   DelayTime (Время задержки) — время перед началом анимации после вызова функции StartAnimation()
-   Duration (Продолжительность) — продолжительность анимации
-   IterationBehavior (Поведение повторения) — количество повторений (либо бесконечное повторение) анимации
-   IterationCount (Число повторений) — число конечных временных периодов, в течение которых повторяется анимация по ключевым кадрам
-   KeyFrameCount (Число ключевых кадров) — число ключевых кадров в определенной анимации по ключевым кадрам
-   StopBehavior (Поведение остановки) — определяет поведение анимируемого значения при вызове функции StopAnimation.

В следующем фрагменте кода для анимации устанавливается значение продолжительности, равное 5 секундам.

```cs
animation.Duration = TimeSpan.FromSeconds(5);
```

### Функции для реалистичной анимации

CompositionEasingFunction, функция для реалистичной анимации по ключевым кадрам указывает, как изменяются промежуточные значения между предыдущим и текущим значениями ключевого кадра. Если для ключевого кадра не задана функция для реалистичной анимации, используется кривая по умолчанию.

Поддерживаются два типа функций для реалистичной анимации.

-   Линейная ([**LinearEasingFunction**](https://msdn.microsoft.com/library/windows/apps/Dn706839))
-   Кривая Безье третьего порядка ([**CubicBezierEasingFunction**](https://msdn.microsoft.com/library/windows/apps/Dn706812))

Чтобы создать функцию для реалистичной анимации, используйте методы [**CreateLinearEasingFunction**](https://msdn.microsoft.com/library/windows/apps/Dn706801) или [**CreateCubicBezierEasingFunction**](https://msdn.microsoft.com/library/windows/apps/Dn706791) класса [**Compositor**](https://msdn.microsoft.com/library/windows/apps/Dn706761).

```cs
var linear = _compositor.CreateLinearEasingFunction();
var easeIn = _compositor.CreateCubicBezierEasingFunction(new Vector2(0.5f, 0.0f), new Vector2(1.0f, 1.0f));
```

Примечание. Полезное средство для генерации точек кривой Безье третьего порядка можно найти здесь.

Чтобы добавить функцию для реалистичной анимации к ключевому кадру, просто добавьте третий параметр к ключевому кадру при его добавлении в анимацию.

```cs
animation.InsertKeyFrame(0.5f, new Vector3(50.0f, 80.0f, 0.0f), easeIn);
```

### Запуск и остановка анимации по ключевым кадрам

После того как вы определили анимацию, ключевые кадры и свойства, вы можете подключить анимацию к свойству, которое требуется анимировать. Для подключения анимации к свойству, которое нужно анимировать, вызовите метод [**StartAnimation**](https://msdn.microsoft.com/library/windows/apps/Mt590840) объекта [**Visual**](https://msdn.microsoft.com/library/windows/apps/Dn706858), которому принадлежит свойство.

Ниже приведен общий пример синтаксиса.

```cs
targetVisual.StartAnimation(“Offset”, animation);
```

После запуска анимации ее также можно остановить и отключить. Для этого вызовите метод [**StopAnimation**](https://msdn.microsoft.com/library/windows/apps/Mt590841) и укажите свойство, анимацию которого нужно остановить.

Например:

```cs
targetVisual.StopAnimation(“Offset”);
```

### События завершения анимации

Анимация по ключевым кадрам имеет определенную конечную точку, в отличие от анимации с помощью выражений, которая является условной. С помощью пакетов разработчики могут указать, когда должны завершиться группы анимаций (либо отдельная анимация) по ключевым кадрам. Пакеты можно создавать или извлекать в зависимости от типа их объекта. Они используются для объединения конечного состояния анимаций. Пакеты с заданной областью создаются, а пакеты фиксации — извлекаются. Использование этих типов пакетов рассматривается ниже.

Событие завершения пакета возникает после завершения всех анимаций внутри пакета. Время, необходимое для генерации события завершения пакета, определяется самой продолжительной анимацией или анимацией с самой большой задержкой в пакете. Объединение конечных состояний полезно использовать, когда нужно знать, когда завершаются группы выбранных анимаций, чтобы запланировать другую работу.

### Пакеты с заданной областью

Для объединения определенной группы анимаций или отдельной анимации сначала нужно создать пакет с заданной областью, вызвав метод [**CreateScopedBatch**](https://msdn.microsoft.com/library/windows/apps/Mt589425).

```cs
myScopedBatch = _compositor.CreateScopedBatch(CompositionBatchTypes.Animation);
```

После создания пакета с заданной областью все запущенные анимации объединяются, пока пакет явным образом не будет приостановлен либо остановлен с помощью методов [**Suspend**](https://msdn.microsoft.com/library/windows/apps/Mt590848) или [**End**](https://msdn.microsoft.com/library/windows/apps/Mt590844).

Вызов метода [**Suspend**](https://msdn.microsoft.com/library/windows/apps/Mt590848) останавливает агрегацию конечных состояний анимаций, до тех пор пока не будет вызван метод [**Resume**](https://msdn.microsoft.com/library/windows/apps/Mt590847). Это позволяет явным образом исключить содержимое из данного пакета.

```cs
myScopedBatch.Suspend();
```

Чтобы завершить выполнение пакета, необходимо вызвать метод [**End**](https://msdn.microsoft.com/library/windows/apps/Mt590844). Если не вызвать метод End, пакет будет оставаться открытым, постоянно собирая объекты.

```cs
myScopedBatch.End();
```

Если вы хотите узнать, когда заканчивается отдельная анимация, вам необходимо создать пакет с заданной областью, запустить анимацию и завершить пакет.

Например:

```cs
myScopedBatch = _compositor.CreateScopedBatch(CompositionBatchTypes.Animation);
Visual.StartAnimation(“Opacity”, myAnimation);
myScopedBatch.End();
```

### Пакеты фиксации

Пакет фиксации — это пакет, который создается неявно во время цикла фиксации. Текущий пакет фиксации можно получить, вызвав метод [**GetCommitBatch**](https://msdn.microsoft.com/library/windows/apps/Mt589430) в любой момент во время цикла фиксации. Цикл фиксации определяется как временной промежуток между обновлениями, получаемыми от компоновщика. Обновления помещаются в очередь, пока система не будет готова обработать изменения и отрисовать данные на экране. Заголовки не управляют временем выполнения фиксации. Пакет фиксации объединяет все объекты в пределах цикла фиксации (то есть, до и после вызова функции GetCommitBatch). Для цикла фиксации имеется только один пакет фиксации, и вам не нужно явным образом вызывать метод [**End**](https://msdn.microsoft.com/library/windows/apps/Mt590844) для пакета.

```cs
myCommitBatch = _compositor.GetCommitBatch(CompositionBatchTypes.Animation);
```

### Состояния пакетов

Чтобы определить, открыт ли пакет для объединения анимаций, используйте свойство **IsActive**. Если свойство **IsEnded** возвращает значение true, то к этому конкретному пакету нельзя добавить анимацию. Пакеты с заданной областью находятся в состоянии "Завершен", если они были явно завершены с помощью вызова метода [**End**](https://msdn.microsoft.com/library/windows/apps/Mt590844). Пакеты фиксации завершаются после завершения текущего цикла фиксации.

## Анимация с помощью выражений

Анимация с помощью выражений — это анимация, использующая какое-либо математическое выражение для вычисления анимированного значения для каждого кадра. В самом языке выражений можно использовать ссылки на свойства других объектов композиции. В отличие от анимации по ключевым кадрам, анимация с помощью выражений не привязана ко времени и обрабатывается покадрово (если это необходимо). Выражения могут использоваться для описания более сложных видов взаимодействия с пользователем, которые обрабатываются механизмом композиции (например, закрепленных заголовков и параллакса).

### Создание выражений

Для создания выражения вызовите метод [**CreateExpressionAnimation**](https://msdn.microsoft.com/library/windows/apps/Mt187002) в объекте Compositor и укажите нужное выражение.

```cs
var expression = _compositor.CreateExpressionAnimation(“INSERT_EXPRESSION_STRING”)
```

### Операторы, приоритет и ассоциативность

Язык выражений поддерживает следующие операторы и соблюдает их приоритет и ассоциативность, которые определены в спецификации языка C#.

-   Унарные ("-")
-   Мультипликативные ("*", "/")
-   Аддитивные ("-", "+")

Язык выражений также поддерживает концепцию покомпонентных математических операций. Операторы доступа к элементу (x.y) поддерживаются только для простых типов (вектор и матрицы). Например:

```cs
Offset.x + 5.0
```

### Параметры свойств

Одной из сильных сторон языка выражений является возможность использования параметров в качестве переменных выражения. Строка выражения может содержать параметры, которые при вычислении выражения заменяются либо постоянным значением, либо ссылками на значение свойства другого объекта. Например:

```cs
ChildVisual.Offset.X / ParentVisual.Offset.Y
```

В этом примере ChildOffset и ParentOffset являются параметрами, которые представляют свойство смещения двух визуальных объектов. Для указания параметров используйте методы **Set\*Parameter** класса [**CompositionAnimation**](https://msdn.microsoft.com/library/windows/apps/Dn706708).

-   Для создания векторного параметра используйте методы [**SetVector2Parameter**](https://msdn.microsoft.com/en-us/library/windows/apps/windows.ui.composition.compositionanimation.setvector2parameter.aspx), [**SetVector3Parameter**](https://msdn.microsoft.com/en-us/library/windows/apps/windows.ui.composition.compositionanimation.setvector3parameter.aspx) или [**SetVector4Parameter**](https://msdn.microsoft.com/library/windows/apps/windows.ui.composition.compositionanimation.setvector4parameter).
-   Для создания матричного параметра используйте методы [**SetMatrix3x2Parameter**](https://msdn.microsoft.com/en-us/library/windows/apps/windows.ui.composition.compositionanimation.setmatrix3x2parameter.aspx) или [**SetMatrix4x4Parameter**](https://msdn.microsoft.com/library/windows/apps/windows.ui.composition.compositionanimation.setmatrix4x4parameter).
-   Для создания скалярного параметра используйте метод [**SetScalarParameter**](https://msdn.microsoft.com/library/windows/apps/windows.ui.composition.compositionanimation.setscalarparameter).
-   Для создания параметра цвета используйте метод [**SetColorParameter**](https://msdn.microsoft.com/library/windows/apps/windows.ui.composition.compositionanimation.setcolorparameter).
-   Для создания параметра кватерниона используйте метод [**SetQuaternionParameter**](https://msdn.microsoft.com/library/windows/apps/windows.ui.composition.compositionanimation.setquaternionparameter).
-   Для создания параметра ссылки используйте метод [**SetReferenceParameter**](https://msdn.microsoft.com/library/windows/apps/windows.ui.composition.compositionanimation.setreferenceparameter).

В вышеприведенной строке выражения нам нужно создать два параметра для определения двух визуальных объектов.

```cs
Expression.SetReferenceParameter(“ChildVisual”, childVisual);
Expression.SetReferenceParameter(“ParentVisual”, parentVisual);
```

### Вспомогательные функции выражений

Помимо доступа к операторам и параметрам свойств, также имеется доступ к полной библиотеке вспомогательных функций языка выражений. Полный список вспомогательных функций можно найти в разделе примечаний класса [**ExpressionAnimation**](https://msdn.microsoft.com/library/windows/apps/Dn706817). Вот некоторые из них.

-   Max (скалярное значение 1, скалярное значение 2)
-   Scale (значение Vector3, скалярный множитель)
-   Transform(значение Vector2, матрица Matrix 3 x 2)

Вот более сложный пример строки выражения, в котором используется вспомогательная функция Clamp.

```cs
Clamp((scroller.Offset.y * -1.0) – container.Offset.y, 0, container.Size.y – header.Size.y)
```

### Запуск и остановка анимации на основе выражений

Для запуска и остановки анимации на основе выражений используйте те же функции ([**StartAnimation**](https://msdn.microsoft.com/library/windows/apps/Mt590840), [**StopAnimation**](https://msdn.microsoft.com/library/windows/apps/Mt590841)), что и для анимации по ключевым кадрам. Примечание. Анимации на основе выражений работают до тех пор, пока не будут остановлены с помощью **StopAnimation**. Они, в отличие от анимаций по ключевым кадрам, не имеют конечной точки.

### Наборы свойств

Помимо возможности ссылаться на свойства других объектов композиции, также имеется возможность создавать и сохранять собственные значения свойств, которые можно использовать в нескольких анимациях. Наборы свойств представлены классом [**CompositionPropertySet**](https://msdn.microsoft.com/library/windows/apps/Dn706772). Наборы свойств позволяют создать свойство со значением и впоследствии ссылаться на него в выражении или даже устанавливать его в качестве цели для анимации по ключевым кадрам.

Для создания набора свойств используйте метод [**CreatePropertySet**](https://msdn.microsoft.com/library/windows/apps/Dn706802) класса Compositor. Например, так:

```cs
_sharedProperties = _compositor.CreatePropertySet();
```

Как только вы создали свой набор свойств, вы можете добавить в него свойство и значение, используя один из методов **Insert\*** класса [**CompositionPropertySet**](https://msdn.microsoft.com/library/windows/apps/Dn706772). Например:

```cs
_sharedProperties.InsertVector3(“NewOffset”, offset);
```

После создания анимации на основе выражений ссылаться на свойства из набора в строке выражения можно с помощью параметра ссылки. Например:

```cs
var expression = _compositor.CreateExpressionAnimation(“sharedProperties.NewOffset”);
expression.SetReferenceParameter(“sharedProperties”, _sharedProperties);
```

### Ключевые кадры выражения

Выше в этом документе мы рассмотрели создание анимации по ключевым кадрам и соответствующих ключевых кадров. Помимо создания обычного ключевого кадра с нормализованным компонентом времени и значения, также имеется возможность определять ключевые кадры выражения.

Вместо явного определения значения для определенного момента времени в ключевом кадре можно использовать синтаксис выражения, чтобы описать, как необходимо вычислять значение в указанный момент на временной шкале ключевого кадра. В анимации по ключевым кадрам можно смешивать и сопоставлять ключевые кадры выражения и обычные ключевые кадры.

### Создание ключевых кадров выражения

Подобно обычным ключевым кадрам, ключевые кадры выражения создаются с помощью определения двух компонентов.

-   Time (Время) — нормализованное состояние времени ключевого кадра (значение от 0,0 до 1,0)
-   Value (Значение) — строка выражения, которая описывает, как необходимо вычислять значение

Например, в следующем фрагменте кода используется сочетание обычных ключевых кадров и ключевых кадров выражения.

```cs
var animation = _compositor.CreateScalarKeyFrameAnimation();
animation.InsertExpressionKeyFrame(0.25, “VisualBOffset.X / VisualAOffset.Y”);
animation.InsertKeyFrame(1.00f, 0.8f);
```

### Использование текущих и начальных значений

В языке выражений можно ссылаться на текущее и начальное значения анимации. В языке выражений к этим значениям добавляется префикс this.

-   this.CurrentValue
-   this.StartingValue

Пример использования в ключевом кадре выражения.

```cs
animation.InsertExpressionKeyFrame(0.0f, “this.CurrentValue + delta”);
```

### Условные выражения

Помимо поддержки основных математических выражений, с помощью тернарного оператора можно также определить условное выражение.

```cs
(condition ? first_expression : second_expression)
```

Внутри выражений в инструкциях условия поддерживаются следующие операторы сравнения.

-   Оператор «Равно» (==)
-   Оператор «Не равно» (!=)
-   Оператор «Меньше, чем» (<)
-   Оператор «Меньше или равно» (<=)
-   Оператор «Больше» (>)
-   Оператор «Больше или равно» (>=)

Наконец, в инструкциях условия поддерживаются следующие логические операции в виде операторов и функций.

-   Логическое «НЕ»: ! / Not(bool1)
-   Логическое «И»: && / And(bool1, bool2)
-   Логическое «ИЛИ»: || / Or(bool1, bool2)

Следующий фрагмент кода демонстрирует пример использования условных операторов в выражении.

```cs
var expression = _compositor.CreateExpressionAnimation(“target.Offset.x > 50 ? 0.0f +   (target.Offset.x / parent.Offset.x) : 1.0f”);
```

 

 






<!--HONumber=Mar16_HO1-->


