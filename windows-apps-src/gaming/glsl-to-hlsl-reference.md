---
author: mtoepke
title: Справочные материалы по переносу с GLSL на HLSL
description: Вы переносите код с GLSL на HLSL, когда портируете графическую архитектуру с OpenGL ES2.0 на Direct3D11, чтобы создавать игры для универсальной платформы Windows (UWP).
ms.assetid: 979d19f6-ef0c-64e4-89c2-a31e1c7b7692
ms.author: mtoepke
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp, glsl, hlsl, opengl, directx, шейдеры
ms.localizationpriority: medium
ms.openlocfilehash: 30c925f9ebb07d578147dfba373fdeb3baa364fe
ms.sourcegitcommit: b7e3d222e229cdbf04e837fcb94fb7d84a93de09
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/26/2018
ms.locfileid: "5599632"
---
# <a name="glsl-to-hlsl-reference"></a>Справочные материалы по переносу с GLSL на HLSL



Вы переносите код с GLSL на HLSL, когда [портируете графическую архитектуру с OpenGL ES2.0 на Direct3D11](port-from-opengl-es-2-0-to-directx-11-1.md), чтобы создавать игры для универсальной платформы Windows (UWP). Язык GLSL, о котором пойдет речь в этом разделе, совместим с OpenGL ES2.0. Язык HLSL совместим с Direct3D11. Подробнее об отличиях Direct3D11 от предыдущих версий Direct3D см. в разделе [Сопоставление компонентов](feature-mapping.md).

-   [Сравнение OpenGL ES2.0 и Direct3D11](#comparing-opengl-es-20-with-direct3d-11)
-   [Перенос переменных GLSL в HLSL](#porting-glsl-variables-to-hlsl)
-   [Перенос типов GLSL в HLSL](#porting-glsl-types-to-hlsl)
-   [Перенос предварительно определенных глобальных переменных GLSL в HLSL](#porting-glsl-pre-defined-global-variables-to-hlsl)
-   [Пример переноса переменных GLSL в HLSL](#examples-of-porting-glsl-variables-to-hlsl)
    -   [Неизменная переменная, атрибут и меняющаяся переменная в GLSL](#uniform-attribute-and-varying-in-glsl)
    -   [Буферы констант и передача данных в HLSL](#constant-buffers-and-data-transfers-in-hlsl)
-   [Примеры переноса кода отрисовки OpenGL в Direct3D](#examples-of-porting-opengl-rendering-code-to-direct3d)
-   [Связанные разделы](#related-topics)

## <a name="comparing-opengl-es-20-with-direct3d-11"></a>Сравнение OpenGL ES2.0 и Direct3D11


Между OpenGL ES2.0 и Direct3D11 много общего. У них похожие конвейеры отрисовки и графические компоненты. Но Direct3D11 представляет собой реализацию отрисовки и API, а не спецификацию. OpenGL ES2.0 является спецификацией отрисовки и API, а не реализацией. В целом Direct3D11 и OpenGL ES2.0 отличаются в следующем.

| OpenGL ES2.0                                                                                         | Direct3D11                                                                                                            |
|-------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------|
| Не зависящая от оборудования и операционной системы спецификация с реализациями, предоставленными поставщиком             | Реализация аппаратной абстракции Майкрософт и сертификация на платформах Windows                                |
| Отделенная для поддержки многообразия оборудования среда выполнения управляет большей частью ресурсов                                     | Прямой доступ к аппаратной структуре; приложение может управлять ресурсами и обработкой                                              |
| Предоставляет модули более высокого уровня через сторонние библиотеки (например, SDL) | Модули более высокого уровня, такие как Direct2D, основаны на модулях более низкого уровня, чтобы упростить разработку приложений для Windows             |
| Поставщики оборудования различаются с помощью расширений                                                         | Майкрософт добавляет к API универсальные дополнительные компоненты, чтобы они не были специфическими ни для одного конкретного поставщика |

 

GLSL и HLSL отличаются в следующем.

<table>
<colgroup>
<col width="50%" />
<col width="50%" />
</colgroup>
<thead>
<tr class="header">
<th align="left">GLSL</th>
<th align="left">HLSL</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left">Процессуальный, сконцентрированный на этапах (как C)</td>
<td align="left">Объектно-ориентированный, сконцентрированный на данных (как C++)</td>
</tr>
<tr class="even">
<td align="left">Компиляция шейдера интегрирована в графический API</td>
<td align="left">Компилятор HLSL <a href="https://msdn.microsoft.com/library/windows/desktop/bb509633">компилирует шейдер</a> в промежуточное двоичное представление, прежде чем Direct3D передает его драйверу.
<div class="alert">
<strong>Примечание</strong>это двоичное представление не зависит от оборудования. Оно обычно компилируется во время сборки приложения, а не во время его выполнения.
</div>
<div>
 
</div></td>
</tr>
<tr class="odd">
<td align="left">Модификаторы хранилищ <a href="#porting-glsl-variables-to-hlsl">переменных</a></td>
<td align="left">Буферы констант и передачи данных через объявления входных макетов</td>
</tr>
<tr class="even">
<td align="left"><p><a href="#porting-glsl-types-to-hlsl">Типы</a></p>
<p>Стандартный тип вектора: vec2/3/4</p>
<p>lowp, mediump, highp</p></td>
<td align="left"><p>Стандартный тип вектора: float2/3/4</p>
<p>min10float, min16float</p></td>
</tr>
<tr class="odd">
<td align="left">texture2D [функция]</td>
<td align="left"><a href="https://msdn.microsoft.com/library/windows/desktop/bb509695">texture.Sample</a> [тип_данных.функция]</td>
</tr>
<tr class="even">
<td align="left">sampler2D [тип_данных]</td>
<td align="left"><a href="https://msdn.microsoft.com/library/windows/desktop/ff471525">Texture2D</a> [тип_данных]</td>
</tr>
<tr class="odd">
<td align="left">Развернутые по строкам матрицы (по умолчанию)</td>
<td align="left">Развернутые по столбцам матрицы (по умолчанию)
<div class="alert">
<strong>Примечание</strong>  используйте модификатор типа <strong>row_major</strong> изменить макет для одной переменной. Подробнее см. <a href="https://msdn.microsoft.com/library/windows/desktop/bb509706">Синтаксис переменной</a>. Вы также можете указать флажок компилятора или псевдокомментарий, чтобы изменить глобальное значение по умолчанию.
</div>
<div>
 
</div></td>
</tr>
<tr class="even">
<td align="left">Шейдер фрагмента</td>
<td align="left">Построитель текстуры</td>
</tr>
</tbody>
</table>

 

> **Примечание**имеет HLSL текстуры и дискретизаторы являются двумя отдельными объектами. В GLSL, как в Direct3D9, привязка текстуры является частью состояния дискретизатора.

 

В GLSL вы представляете значительную часть состояния OpenGL в виде предопределенных глобальных переменных. Например, в GLSL вы используете переменную **gl\_Position**, чтобы указать расположение вершины, и переменную **gl\_FragColor**, чтобы указать цвет фрагмента. В HLSL вы явно передаете состояние Direct3D из кода приложения в шейдер. Например, при использовании Direct3D и HLSL данные, введенные в вершинный шейдер, должны соответствовать формату данных в буфере вершины, а структура буфера констант в коде приложения должна соответствовать структуре буфера констант ([cbuffer](https://msdn.microsoft.com/library/windows/desktop/bb509581)) в коде шейдера.

## <a name="porting-glsl-variables-to-hlsl"></a>Перенос переменных GLSL в HLSL


В GLSL вы применяете модификаторы (квалификаторы) к объявлению глобальной переменной шейдера, чтобы обеспечить определенные реакции этой переменной на события в вашем шейдере. В HLSL вам не нужны эти модификаторы, потому что вы определяете поток шейдера с помощью аргументов, которые передаете шейдеру и возвращаете от него.

<table>
<colgroup>
<col width="50%" />
<col width="50%" />
</colgroup>
<thead>
<tr class="header">
<th align="left">Поведение переменной GLSL</th>
<th align="left">Эквивалент HLSL</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left"><p><strong>uniform</strong></p>
<p>Вы передаете неизменную переменную из кода приложения в вершинный шейдер и(или) шейдер фрагмента. Вы должны передать значения всех неизменных переменных перед отрисовкой какого-либо треугольника с помощью этих шейдеров, чтобы их значения остались теми же во время отрисовки треугольной сетки. Эти значения не меняются. Некоторые неизменные значения устанавливаются для всего кадра, другие— для одной конкретной пары вершина-пиксель.</p>
<p>Неизменные переменные индивидуальны для полигона.</p></td>
<td align="left"><p>Используйте буфер констант.</p>
<p>См. разделы <a href="https://msdn.microsoft.com/library/windows/desktop/ff476896">Краткое руководство: создание буфера констант</a> и <a href="https://msdn.microsoft.com/library/windows/desktop/bb509581">Постоянные шейдера</a>.</p></td>
</tr>
<tr class="even">
<td align="left"><p><strong>varying</strong></p>
<p>Вы инициализируете меняющуюся переменную в вершинном шейдере и передаете ее меняющейся переменной с таким же названием в шейдере фрагмента. Так как вершинный шейдер устанавливает только значение меняющейся переменной для каждой вершины, средство прорисовки интерполирует эти значения (надлежащим с точки зрения перспективы образом), чтобы генерировать значения для каждого фрагмента и передать их в шейдер фрагмента. Эти переменные меняются от треугольника к треугольнику.</p></td>
<td align="left">Используйте структуру, которую возвращаете от вершинного шейдера, в качестве входных данных для построителя текстуры. Убедитесь, что семантические значения совпадают.</td>
</tr>
<tr class="odd">
<td align="left"><p><strong>attribute</strong></p>
<p>Атрибут является частью описания вершины, которое вы передаете из кода приложения только в вершинный шейдер. В отличие от неизменного значения вы устанавливаете значение каждого атрибута для каждой вершины, что, в свою очередь, позволяет всем вершинам иметь разные значения. Переменные атрибутов индивидуальны для вершин.</p></td>
<td align="left"><p>Определите буфер вершины в коде приложения Direct3D и обеспечьте его соответствие входным данным вершины, определенным в вершинном шейдере. При желании определите буфер индексов. См. разделы <a href="https://msdn.microsoft.com/library/windows/desktop/ff476899">Краткое руководство: создание буфера вершины</a> и <a href="https://msdn.microsoft.com/library/windows/desktop/ff476897">Краткое руководство: создание буфера индексов</a>.</p>
<p>Создайте макет входных данных в коде приложения Direct3D и обеспечьте соответствие семантических значений семантическим значениям входных данных вершины. См. раздел <a href="https://msdn.microsoft.com/library/windows/desktop/bb205117#Create_the_Input_Layout">Создание входного макета</a>.</p></td>
</tr>
<tr class="even">
<td align="left"><p><strong>const</strong></p>
<p>Постоянные, которые компилируются в шейдер и никогда не меняются.</p></td>
<td align="left">Используйте <strong>static const</strong> <strong>Static</strong> означает, что данное значение не предоставляется буферам констант, а <strong>const</strong> означает, что шейдер не может изменить данное значение. Таким образом, значение известно во время компиляции на основании инициализатора.</td>
</tr>
</tbody>
</table>

 

В GLSL переменные без модификаторов являются лишь простыми глобальными переменными, частными для каждого шейдера.

Когда вы передаете данные текстурам ([Texture2D](https://msdn.microsoft.com/library/windows/desktop/ff471525) в HLSL) и их соответствующим дискретизаторам ([SamplerState](https://msdn.microsoft.com/library/windows/desktop/bb509644) в HLSL), вы обычно объявляете их как глобальные переменные в построителе текстуры.

## <a name="porting-glsl-types-to-hlsl"></a>Перенос типов GLSL в HLSL


Используйте эту таблицу для переноса типов GLSL в HLSL.

<table>
<colgroup>
<col width="50%" />
<col width="50%" />
</colgroup>
<thead>
<tr class="header">
<th align="left">Тип GLSL</th>
<th align="left">Тип HLSL</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left">скалярные типы: float, int, bool</td>
<td align="left"><p>скалярные типы: float, int, bool</p>
<p>также uint, double</p>
<p>Подробнее: <a href="https://msdn.microsoft.com/library/windows/desktop/bb509646">Скалярные типы</a>.</p></td>
</tr>
<tr class="even">
<td align="left"><p>векторные типы</p>
<ul>
<li>вектор из чисел с плавающей точкой: vec2, vec3, vec4</li>
<li>логический вектор: bvec2, bvec3, bvec4</li>
<li>вектор из знаковых целых чисел: ivec2, ivec3, ivec4</li>
</ul></td>
<td align="left"><p>векторные типы</p>
<ul>
<li>float2, float3, float4 и float1</li>
<li>bool2, bool3, bool4 и bool1</li>
<li>int2, int3, int4 и int1</li>
<li><p>Эти типы также располагают векторными расширениями, схожими с float, bool и int:</p>
<ul>
<li>uint</li>
<li>min10float, min16float</li>
<li>min12int, min16int</li>
<li>min16uint</li>
</ul></li>
</ul>
<p>Подробнее см. в разделах <a href="https://msdn.microsoft.com/library/windows/desktop/bb509707">Тип вектора</a> и <a href="https://msdn.microsoft.com/library/windows/desktop/bb509568">Ключевые слова</a>.</p>
<p>тип вектора также можно определить, как float4 (typedef vector &lt;float, 4&gt; vector;). Дополнительные сведения см. в разделе <a href="https://msdn.microsoft.com/library/windows/desktop/bb509702">Определяемый пользователем тип</a>.</p></td>
</tr>
<tr class="odd">
<td align="left"><p>тип матрицы</p>
<ul>
<li>mat2: плавающая матрица 2x2</li>
<li>mat3: плавающая матрица 3x3</li>
<li>mat4: плавающая матрица 4x4</li>
</ul></td>
<td align="left"><p>тип матрицы</p>
<ul>
<li>float2x2</li>
<li>float3x3</li>
<li>float4x4</li>
<li>также float1x1, float1x2, float1x3, float1x4, float2x1, float2x3, float2x4, float3x1, float3x2, float3x4, float4x1, float4x2, float4x3</li>
<li><p>Эти типы также располагают матричными расширениями, схожими с float:</p>
<ul>
<li>int, uint, bool</li>
<li>min10float, min16float</li>
<li>min12int, min16int</li>
<li>min16uint</li>
</ul></li>
</ul>
<p>Вы также можете воспользоваться <a href="https://msdn.microsoft.com/library/windows/desktop/bb509623">типом матрицы</a>, чтобы определить матрицу.</p>
<p>Например: matrix &lt;float, 2, 2&gt; fMatrix = {0.0f, 0.1, 2.1f, 2.2f};</p>
<p>тип матрицы также можно определить как float4x4 (typedef matrix &lt;float, 4, 4&gt; matrix;). Дополнительные сведения см. в разделе <a href="https://msdn.microsoft.com/library/windows/desktop/bb509702">Определяемый пользователем тип</a>.</p></td>
</tr>
<tr class="even">
<td align="left"><p>Квалификатор точности для числа с плавающей запятой, целого числа, дискретизатора</p>
<ul>
<li><p>highp</p>
<p>Этот квалификатор обеспечивает минимальные требования к точности, которые больше предоставляемых типом min16float и меньше полного 32-разрядного числа с плавающей запятой. Эквивалент в HLSL:</p>
<p>highp float -&gt; число с плавающей точкой</p>
<p>highp int -&gt; целое число</p></li>
<li><p>mediump</p>
<p>Этот квалификатор в применении к числу с плавающей запятой и целому числу эквивалентен типам min16float и min12int в HLSL. Минимум 10бит мантиссы, не как min10float.</p></li>
<li><p>lowp</p>
<p>Этот квалификатор в применении к числу с плавающей точкой предоставляет диапазон плавающей запятой от –2 до 2. Эквивалентен типу min10float в HLSL.</p></li>
</ul></td>
<td align="left"><p>типы точности</p>
<ul>
<li>min16float: минимум 16-разрядное значение плавающей запятой</li>
<li><p>min10float</p>
<p>Минимальное значение с фиксированной запятой и знаком на 2,8бита (2бита целого числа и 8бит дробного компонента). 8-разрядный дробный компонент может включать1, чтобы дать полный суммарный диапазон от –2 до 2.</p></li>
<li>min16int: минимум 16-разрядное знаковое целое число</li>
<li><p>min12int: минимум 12-разрядное знаковое целое число</p>
<p>Этот тип для 10Level9 (<a href="https://msdn.microsoft.com/library/windows/desktop/ff476876">уровни функций 9_x</a>), где целые числа представлены числами с плавающей точкой. Это точность, которую вы можете получить, когда эмулируете целое число с помощью 16-разрядного числа с плавающей точкой.</p></li>
<li>min16uint: минимум 16-разрядное целое число без знака</li>
</ul>
<p>Подробнее см. в разделах <a href="https://msdn.microsoft.com/library/windows/desktop/bb509646">Скалярные типы</a> и <a href="https://msdn.microsoft.com/library/windows/desktop/hh968108">Использование минимальной точности HLSL</a>.</p></td>
</tr>
<tr class="odd">
<td align="left">sampler2D</td>
<td align="left"><a href="https://msdn.microsoft.com/library/windows/desktop/ff471525">Texture2D</a></td>
</tr>
<tr class="even">
<td align="left">samplerCube</td>
<td align="left"><a href="https://msdn.microsoft.com/library/windows/desktop/bb509700">TextureCube</a></td>
</tr>
</tbody>
</table>

 

## <a name="porting-glsl-pre-defined-global-variables-to-hlsl"></a>Перенос предварительно определенных глобальных переменных GLSL в HLSL


Используйте эту таблицу для переноса предварительно определенных глобальных переменных GLSL в HLSL.

<table>
<colgroup>
<col width="50%" />
<col width="50%" />
</colgroup>
<thead>
<tr class="header">
<th align="left">Предварительно определенные глобальные переменные GLSL</th>
<th align="left">Семантика HLSL</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left"><p><strong>gl_Position</strong></p>
<p>Эта переменная относится к типу <strong>vec4</strong>.</p>
<p>Положение вершины</p>
<p>например, - gl_Position = position;</p></td>
<td align="left"><p>SV_Position</p>
<p>POSITION в Direct3D 9</p>
<p>Эта семантика относится к типу <strong>float4</strong>.</p>
<p>Выходные данные вершинного шейдера</p>
<p>Положение вершины</p>
<p>например, - float4 vPosition : SV_Position;</p></td>
</tr>
<tr class="even">
<td align="left"><p><strong>gl_PointSize</strong></p>
<p>Эта переменная относится к типу <strong>float</strong>.</p>
<p>Размер точки</p></td>
<td align="left"><p>PSIZE</p>
<p>Значение отсутствует, если вы не используете Direct3D9</p>
<p>Эта семантика относится к типу <strong>float</strong>.</p>
<p>Выходные данные вершинного шейдера</p>
<p>Размер точки</p></td>
</tr>
<tr class="odd">
<td align="left"><p><strong>gl_FragColor</strong></p>
<p>Эта переменная относится к типу <strong>vec4</strong>.</p>
<p>Цвет фрагмента</p>
<p>например, - gl_FragColor = vec4(colorVarying, 1.0);</p></td>
<td align="left"><p>SV_Target</p>
<p>COLOR в Direct3D9</p>
<p>Эта семантика относится к типу <strong>float4</strong>.</p>
<p>Выходные данные построителя текстуры</p>
<p>Цвет пикселя</p>
<p>например, - float4 Color[4] : SV_Target;</p></td>
</tr>
<tr class="even">
<td align="left"><p><strong>gl_FragData[n]</strong></p>
<p>Эта переменная относится к типу <strong>vec4</strong>.</p>
<p>Цвет фрагмента для прикрепления цвета n</p></td>
<td align="left"><p>SV_Target[n]</p>
<p>Эта семантика относится к типу <strong>float4</strong>.</p>
<p>Выходное значение построителя текстуры, которое хранится в однобуферной прорисовке n, где 0 &lt;= n &lt;= 7.</p></td>
</tr>
<tr class="odd">
<td align="left"><p><strong>gl_FragCoord</strong></p>
<p>Эта переменная относится к типу <strong>vec4</strong>.</p>
<p>Положение фрагмента в буфере кадров</p></td>
<td align="left"><p>SV_Position</p>
<p>Отсутствует в Direct3D9</p>
<p>Эта семантика относится к типу <strong>float4</strong>.</p>
<p>Входные данные построителя текстуры</p>
<p>Координаты пространства экрана</p>
<p>например, - float4 screenSpace : SV_Position</p></td>
</tr>
<tr class="even">
<td align="left"><p><strong>gl_FrontFacing</strong></p>
<p>Эта переменная относится к типу <strong>bool</strong>.</p>
<p>Определяет, принадлежит ли фрагмент лицевому примитиву.</p></td>
<td align="left"><p>SV_IsFrontFace</p>
<p>VFACE в Direct3D 9</p>
<p>SV_IsFrontFace относится к типу <strong>bool</strong>.</p>
<p>VFACE относится к типу <strong>float</strong>.</p>
<p>Входные данные построителя текстуры</p>
<p>Лицевая поверхность примитива</p></td>
</tr>
<tr class="odd">
<td align="left"><p><strong>gl_PointCoord</strong></p>
<p>Эта переменная относится к типу <strong>vec2</strong>.</p>
<p>Положение фрагмента в пределах точки (только растеризация точки)</p></td>
<td align="left"><p>SV_Position</p>
<p>VPOS в Direct3D 9</p>
<p>SV_Position относится к типу <strong>float4</strong>.</p>
<p>VPOS относится к типу <strong>float2</strong>.</p>
<p>Входные данные построителя текстуры</p>
<p>Положение пикселя или образца на пространстве экрана</p>
<p>например, - float4 pos : SV_Position</p></td>
</tr>
<tr class="even">
<td align="left"><p><strong>gl_FragDepth</strong></p>
<p>Эта переменная относится к типу <strong>float</strong>.</p>
<p>Данные буфера глубины</p></td>
<td align="left"><p>SV_Depth</p>
<p>DEPTH в Direct3D 9</p>
<p>SV_Depth относится к типу <strong>float</strong>.</p>
<p>Выходные данные построителя текстуры</p>
<p>Данные буфера глубины</p></td>
</tr>
</tbody>
</table>

 

Вы используете семантику, чтобы указать положение, цвет и прочие сведения для входных данных вершинного шейдера и построителя текстуры. Значения семантики в макете входных данных должны соответствовать входным данным вершинного шейдера. Например, см. раздел [Примеры переноса переменных GLSL в HLSL](#examples-of-porting-glsl-variables-to-hlsl). Подробнее о семантике HLSL: [Семантика](https://msdn.microsoft.com/library/windows/desktop/bb509647).

## <a name="examples-of-porting-glsl-variables-to-hlsl"></a>Пример переноса переменных GLSL в HLSL


Здесь мы приведем примеры использования переменных GLSL в коде OpenGL/GLSL и эквивалентный пример в коде Direct3D/HLSL.

### <a name="uniform-attribute-and-varying-in-glsl"></a>Неизменная переменная, атрибут и меняющаяся переменная в GLSL

Код приложения OpenGL

``` syntax
// Uniform values can be set in app code and then processed in the shader code.
uniform mat4 model;
uniform mat4 view;
uniform mat4 projection;

// Incoming position of vertex
attribute vec4 position;
 
// Incoming color for the vertex
attribute vec3 color;
 
// The varying variable tells the shader pipeline to pass it  
// on to the fragment shader.
varying vec3 colorVarying;
```

Код вершинного шейдера GLSL

``` syntax
//The shader entry point is the main method.
void main()
{
colorVarying = color; //Use the varying variable to pass the color to the fragment shader
gl_Position = position; //Copy the position to the gl_Position pre-defined global variable
}
```

Код шейдера фрагмента GLSL

``` syntax
void main()
{
//Pad the colorVarying vec3 with a 1.0 for alpha to create a vec4 color
//and assign that color to the gl_FragColor pre-defined global variable
//This color then becomes the fragment's color.
gl_FragColor = vec4(colorVarying, 1.0);
}
```

### <a name="constant-buffers-and-data-transfers-in-hlsl"></a>Буферы констант и передача данных в HLSL

Вот пример передачи данных вершинному шейдеру HLSL, которые затем направляются построителю текстуры. В коде приложения определите вершину и буфер констант. Затем в коде вершинного шейдера определите буфер констант как [cbuffer](https://msdn.microsoft.com/library/windows/desktop/bb509581) и сохраните индивидуальные данные вершины и входные данные построителя текстуры. Здесь мы используем структуры, называемые **VertexShaderInput** и **PixelShaderInput**.

Код приложения Direct3D

```cpp
struct ConstantBuffer
{
    XMFLOAT4X4 model;
    XMFLOAT4X4 view;
    XMFLOAT4X4 projection;
};
struct SimpleCubeVertex
{
    XMFLOAT3 pos;   // position
    XMFLOAT3 color; // color
};

 // Create an input layout that matches the layout defined in the vertex shader code.
 const D3D11_INPUT_ELEMENT_DESC basicVertexLayoutDesc[] =
 {
     { "POSITION", 0, DXGI_FORMAT_R32G32B32_FLOAT, 0,  0, D3D11_INPUT_PER_VERTEX_DATA, 0 },
     { "COLOR",    0, DXGI_FORMAT_R32G32B32_FLOAT, 0, 12, D3D11_INPUT_PER_VERTEX_DATA, 0 },
 };

// Create vertex and index buffers that define a geometry.
```

Код вершинного шейдера HLSL

``` syntax
cbuffer ModelViewProjectionCB : register( b0 )
{
    matrix model; 
    matrix view;
    matrix projection;
};
// The POSITION and COLOR semantics must match the semantics in the input layout Direct3D app code.
struct VertexShaderInput
{
    float3 pos : POSITION; // Incoming position of vertex 
    float3 color : COLOR; // Incoming color for the vertex
};

struct PixelShaderInput
{
    float4 pos : SV_Position; // Copy the vertex position.
    float4 color : COLOR; // Pass the color to the pixel shader.
};

PixelShaderInput main(VertexShaderInput input)
{
    PixelShaderInput vertexShaderOutput;

    // shader source code

    return vertexShaderOutput;
}
```

Код построителя текстуры HLSL

``` syntax
// Collect input from the vertex shader. 
// The COLOR semantic must match the semantic in the vertex shader code.
struct PixelShaderInput
{
    float4 pos : SV_Position;
    float4 color : COLOR; // Color for the pixel
};

// Set the pixel color value for the renter target. 
float4 main(PixelShaderInput input) : SV_Target
{
    return input.color;
}
```

## <a name="examples-of-porting-opengl-rendering-code-to-direct3d"></a>Примеры переноса кода отрисовки OpenGL в Direct3D


Здесь мы продемонстрируем пример отрисовки в коде OpenGL ES2.0 и эквивалентный пример в коде Direct3D11.

Код отрисовки OpenGL

``` syntax
// Bind shaders to the pipeline. 
// Both vertex shader and fragment shader are in a program.
glUseProgram(m_shader->getProgram());
 
// Input asssembly 
// Get the position and color attributes of the vertex.

m_positionLocation = glGetAttribLocation(m_shader->getProgram(), "position");
glEnableVertexAttribArray(m_positionLocation);

m_colorLocation = glGetAttribColor(m_shader->getProgram(), "color");
glEnableVertexAttribArray(m_colorLocation);
 
// Bind the vertex buffer object to the input assembler.
glBindBuffer(GL_ARRAY_BUFFER, m_geometryBuffer);
glVertexAttribPointer(m_positionLocation, 4, GL_FLOAT, GL_FALSE, 0, NULL);
glBindBuffer(GL_ARRAY_BUFFER, m_colorBuffer);
glVertexAttribPointer(m_colorLocation, 3, GL_FLOAT, GL_FALSE, 0, NULL);
 
// Draw a triangle with 3 vertices.
glDrawArray(GL_TRIANGLES, 0, 3);
```

Код отрисовки Direct3D

```cpp
// Bind the vertex shader and pixel shader to the pipeline.
m_d3dDeviceContext->VSSetShader(vertexShader.Get(),nullptr,0);
m_d3dDeviceContext->PSSetShader(pixelShader.Get(),nullptr,0);
 
// Declare the inputs that the shaders expect.
m_d3dDeviceContext->IASetInputLayout(inputLayout.Get());
m_d3dDeviceContext->IASetVertexBuffers(0, 1, vertexBuffer.GetAddressOf(), &stride, &offset);

// Set the primitive's topology.
m_d3dDeviceContext->IASetPrimitiveTopology(D3D11_PRIMITIVE_TOPOLOGY_TRIANGLELIST);

// Draw a triangle with 3 vertices. triangleVertices is an array of 3 vertices.
m_d3dDeviceContext->Draw(ARRAYSIZE(triangleVertices),0);
```

## <a name="related-topics"></a>Связанные разделы


* [Перенос из OpenGL ES2.0 в Direct3D11](port-from-opengl-es-2-0-to-directx-11-1.md)

 

 




