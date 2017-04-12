---
title: "Графический конвейер"
description: "Графический конвейер Direct3D предназначен для создания графики для игр, действие в которых разворачивается в реальном времени. Данные поступают с устройств ввода на устройства вывода, проходя через все настраиваемые или программируемые этапы."
ms.assetid: C9519AD0-5425-48BD-9FF4-AED8959CA4AD
keywords:
- "Графический конвейер"
- "Этапы конвейера"
author: PeterTurcan
ms.author: pettur
ms.date: 02/08/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
ms.openlocfilehash: af583deb51b93bffc05c4371466fa8412bb0476d
ms.sourcegitcommit: 909d859a0f11981a8d1beac0da35f779786a6889
translationtype: HT
---
# <a name="graphics-pipeline"></a>Графический конвейер


Графический конвейер Direct3D предназначен для создания графики для игр, действие в которых разворачивается в реальном времени. Данные поступают с устройств ввода на устройства вывода, проходя через все настраиваемые или программируемые этапы.

Все этапы можно настроить с помощью API Direct3D. Этапы с использованием общих ядер шейдеров (прямоугольные блоки со скругленными краями) можно программировать с помощью языка программирования [HLSL](https://msdn.microsoft.com/library/windows/desktop/bb509561). Это обеспечивает высочайшую гибкость и адаптивность конвейера.

Наиболее распространены этапы шейдера вершин (VS) и построителя текстуры (PS). Если не предоставить даже эти этапы шейдеров, используются холостая команда по умолчанию, сквозной шейдер вершин и построитель текстуры.

![схема потока данных в программируемом конвейере direct3d 11](images/d3d11-pipeline-stages.jpg)

## <a name="input-assembler-stage"></a>Этап сборщика входных данных

 

| | |
|-|-|
|Описание|Этап [сборщика входных данных (IA)](input-assembler-stage--ia-.md) поставляет данные о смежности и примитивах в контейнер, например о треугольниках, линиях и точках, включая семантические идентификаторы, чтобы повысить эффективность шейдеров путем снижения объемов обработки до примитивов, которые еще не были обработаны.|
|Входные данные|Данные примитивов (треугольники, линии или точки) из заполненных пользователем буферов в памяти. А также, возможно, данные смежности. Для каждого треугольника есть данные трех вершин и, возможно, еще трех смежных вершин.|
|Выходные данные|Примитивы с присоединенными системными значениями (такими как ИД примитива, ИД экземпляра или ИД вершины).|
| | |

<!---
<table>
<colgroup>
<col width="50%" />
<col width="50%" />
</colgroup>
<tbody>
<tr class="odd">
<td align="left">Purpose</td>
<td align="left">The [Input Assembler (IA) stage](input-assembler-stage--ia-.md) supplies primitive and adjacency data to the pipeline, such as triangles, lines and points, including semantics IDs to help make shaders more efficient by reducing processing to primitives that haven't already been processed.</td>
</tr>
<tr class="even">
<td align="left">Input</td>
<td align="left"> Primitive data (triangles, lines, and/or points), from user-filled buffers in memory. And possibly adjacency data. A triangle would be 3 vertices for each triangle and possibly 3 vertices for adjacency data per triangle.  </td>
</tr>
<tr class="odd">
<td align="left">Output</td>
<td align="left">Primitives with attached system-generated values (such as a primitive ID, an instance ID, or a vertex ID). </td>
</tr>
</tbody>
</table>
 -->



## <a name="vertex-shader-stage"></a>Этап шейдера вершин
 

| | |
|-|-|
|Описание|На [этапе шейдера вершин (VS)](vertex-shader-stage--vs-.md) происходит обработка вершин, обычно с выполнением таких операций, как преобразования, скиннинг и освещение. Шейдер вершин принимает одну входную вершину и создает одну выходную вершину. Отдельные операции по вершинам, такие как преобразования, скиннинг, морфинг и освещение по вершинам.|
|Входные данные|Одна вершина с созданными системой значениями VertexID и InstanceID. Каждая входная вершина шейдера вершин может состоять из 32-разрядных векторов (до 4 компонентов в каждом) в количестве до 16 штук.|
|Выходные данные|Одна вершина. Каждая выходная вершина может состоять из 16 32-разрядных четырехкомпонентных векторов.|
| | |

<!---
<table>
<colgroup>
<col width="50%" />
<col width="50%" />
</colgroup>
<tbody>
<tr class="odd">
<td align="left">Purpose</td>
<td align="left">The [Vertex Shader (VS) stage](vertex-shader-stage--vs-.md) processes vertices, typically performing operations such as transformations, skinning, and lighting. A vertex shader takes a single input vertex and produces a single output vertex. Individual per-vertex operations, such as:
<ul>
<li>Transformations</li>
<li>Skinning</li>
<li>Morphing</li>
<li>Per-vertex lighting</li>
</ul></td>
</tr>
<tr class="even">
<td align="left">Input</td>
<td align="left">A single vertex, with VertexID and InstanceID system-generated values. Each vertex shader input vertex can be comprised of up to 16 32-bit vectors (up to 4 components each).</td>
</tr>
<tr class="odd">
<td align="left">Output</td>
<td align="left">A single vertex. Each output vertex can be comprised of as many as 16 32-bit 4-component vectors.</td>
</tr>
</tbody>
</table>
-->
 

## <a name="hull-shader-stage"></a>Этап шейдера поверхностей
 

| | |
|-|-|
|Описание|[Этап шейдера поверхностей (HS)](hull-shader-stage--hs-.md)— один из этапов тесселяции, на котором одна поверхность модели фактически разбивается на множество треугольников.. Шейдер поверхностей вызывается единожды для каждого участка и преобразует входные контрольные точки, определяющие поверхность низкого порядка, в контрольные точки, формирующие участок. Он также выполняет некоторые вычисления для каждого участка, чтобы предоставить данные для этапа тесселяции (TS) и шейдера доменов (DS).|
|Входные данные|От 1 до 32 входных контрольных точек, которые вместе определяют поверхность низкого порядка.|
|Выходные данные|От 1 до 32 выходных контрольных точек, которые вместе образуют участок. Шейдер поверхностей объявляет состояние для этапа тесселяции (TS), включая количество контрольных точек, тип поверхности участка и нужный тип секционирования при тесселяции.|
| | |

<!---
<table>
<colgroup>
<col width="50%" />
<col width="50%" />
</colgroup>
<tbody>
<tr class="odd">
<td align="left">Purpose</td>
<td align="left">The [Hull Shader (HS) stage](hull-shader-stage--hs-.md) is one of the tessellation stages, which efficiently break up a single surface of a model into many triangles. A hull shader is invoked once per patch, and it transforms input control points that define a low-order surface into control points that make up a patch. It also does some per-patch calculations to provide data for the Tessellator (TS) stage and the Domain Shader (DS) stage.</td>
</tr>
<tr class="even">
<td align="left">Input</td>
<td align="left">Between 1 and 32 input control points, which together define a low-order surface. </td>
</tr>
<tr class="odd">
<td align="left">Output</td>
<td align="left">Between 1 and 32 output control points, which together make up a patch. The hull shader declares the state for the Tessellator (TS) stage, including the number of control points, the type of patch face, and the type of partitioning to use when tessellating. </td>
</tr>
</tbody>
</table>
-->

## <a name="tessellator-stage"></a>Этап тесселяции
 

| | |
|-|-|
|Описание|На [этапе тесселяции (TS)](tessellator-stage--ts-.md) создается шаблон выборки домена, представляющего участок геометрии, и генерируется набор небольших объектов (треугольников, точек или линий), соединяющих эти пиксели.|
|Входные данные|Тесселяция выполняется единожды для каждого участка с использованием факторов тесселяции (указывают тщательность тесселяции домена) и типа секционирования (указывает алгоритм разделения участка на части), переданных с предыдущего этапа шейдера поверхностей. |
|Выходные данные|Тесселяция выводит координаты uv (дополнительно— w) и топологию поверхности для этапа шейдера доменов.|
| | |

<!---
<table>
<colgroup>
<col width="50%" />
<col width="50%" />
</colgroup>
<tbody>
<tr class="odd">
<td align="left">Purpose</td>
<td align="left">The [Tessellator (TS) stage](tessellator-stage--ts-.md) creates a sampling pattern of the domain that represents the geometry patch and generates a set of smaller objects (triangles, points, or lines) that connect these samples.   </td>
</tr>
<tr class="even">
<td align="left">Input</td>
<td align="left"> The tessellator operates once per patch using the tessellation factors (which specify how finely the domain will be tessellated) and the type of partitioning (which specifies the algorithm used to slice up a patch) that are passed in from the hull-shader stage. </td>
</tr>
<tr class="odd">
<td align="left">Output</td>
<td align="left">The tessellator outputs uv (and optionally w) coordinates and the surface topology to the domain-shader stage.     </td>
</tr>
</tbody>
</table>
 -->

## <a name="domain-shader-stage"></a>Этап шейдера доменов
 

| | |
|-|-|
|Описание|На [этапе шейдера доменов (DS)](domain-shader-stage--ds-.md) вычисляется положение вершины составной точки в выходном участке. Вычисляется положение вершины, соответствующее каждой выборке домена. Шейдер доменов запускается единожды на каждую выходную точку этапа тесселяции и имеет доступ только для чтения к выходному участку шейдера поверхности и константам выходного участка, а также к координатам UV выходных данных этапа тесселяции.|
|Входные данные|Шейдер доменов использует выходные контрольные точки [этапа шейдера поверхности (HS)](hull-shader-stage--hs-.md). Выходные данные шейдера доменов включают контрольные точки, данные констант участков и факторы тесселяции (факторы тесселяции могут включать значения, используемые тесселятором с фиксированными функциями, а также необработанные значения— перед округлением с помощью целочисленной тесселяции,— например для упрощения геоморфизма). Шейдер доменов вызывается единожды на каждую выходную координату [этапа тесселяции (TS)](tessellator-stage--ts-.md).|
|Выходные данные|Этап шейдера доменов (DS) выводит положение вершины составной точки в выходном участке.|
| | |

<!---
<table>
<colgroup>
<col width="50%" />
<col width="50%" />
</colgroup>
<tbody>
<tr class="odd">
<td align="left">Purpose</td>
<td align="left">The [Domain Shader (DS) stage](domain-shader-stage--ds-.md) calculates the vertex position of a subdivided point in the output patch; it calculates the vertex position that corresponds to each domain sample. A domain shader is run once per tessellator stage output point and has read-only access to the hull shader output patch and output patch constants, and the tessellator stage output UV coordinates.</td>
</tr>
<tr class="even">
<td align="left">Input</td>
<td align="left"><ul>
<li>A domain shader consumes output control points from the [Hull Shader (HS) stage](hull-shader-stage--hs-.md). The hull shader outputs include:
<ul>
<li>Control points.</li>
<li>Patch constant data.</li>
<li>Tessellation factors. The tessellation factors can include the values used by the fixed-function tessellator as well as the raw values (before rounding by integer tessellation, for example), which facilitates geomorphing, for example.</li>
</ul></li>
<li>A domain shader is invoked once per output coordinate from the [Tessellator (TS) stage](tessellator-stage--ts-.md).</li>
</ul></td>
</tr>
<tr class="odd">
<td align="left">Output</td>
<td align="left">The Domain Shader (DS) stage outputs the vertex position of a subdivided point in the output patch.</td>
</tr>
</tbody>
</table>
-->
 

## <a name="geometry-shader-stage"></a>Этап шейдера геометрии
 

| | |
|-|-|
|Описание|[Этап шейдера геометрии (GS)](geometry-shader-stage--gs-.md) обрабатывает целые примитивы: треугольники, линии и точки, а также смежные с ними вершины. Он поддерживает усложнение и упрощение геометрии. Он полезен для таких алгоритмов, как расширение спрайтов точек, динамические системы частиц, создание меха/"плавников", создание объемных теней, однопроходная отрисовка с использованием кубической карты, замена материалов по примитивам и настройка материалов по примитивам, включая создание барицентрических координат в качестве данных примитива, чтобы построитель текстуры мог выполнить настраиваемую интерполяцию атрибутов. |
|Входные данные|В отличие от шейдеров вершин, работающих с одной вершиной, входными данными шейдера геометрии являются вершины полного примитива (три вершины для треугольников, две вершины для линий или одна вершина для точек).|
|Выходные данные|Этап шейдера геометрии может выводить несколько вершин, формирующих единую выбранную топологию. Доступны следующие выходные топологии шейдера геометрии: <strong>tristrip</strong>, <strong>linestrip</strong> и <strong>pointlist</strong>. Количество сгенерированных примитивов может свободно меняться при каждом вызове шейдера геометрии, но максимальное количество генерируемых вершин необходимо объявить статически. Длины полос, сгенерированных в результате вызова шейдера геометрии, могут быть произвольными, а новые полосы можно создавать с помощью функции HLSL [RestartStrip](https://msdn.microsoft.com/library/windows/desktop/bb509660).|
| | |

<!---
<table>
<colgroup>
<col width="50%" />
<col width="50%" />
</colgroup>
<tbody>
<tr class="odd">
<td align="left">Purpose</td>
<td align="left">The [Geometry Shader (GS) stage](geometry-shader-stage--gs-.md) processes entire primitives: triangles, lines, and points, along with their adjacent vertices. It is useful for algorithms including Point Sprite Expansion, Dynamic Particle Systems, and Shadow Volume Generation. It supports geometry amplification and de-amplification.
<ul>
<li>Point Sprite Expansion</li>
<li>Dynamic Particle Systems</li>
<li>Fur/Fin Generation</li>
<li>Shadow Volume Generation</li>
<li>Single Pass Render-to-Cubemap</li>
<li>Per-Primitive Material Swapping</li>
<li>Per-Primitive Material Setup, including generation of barycentric coordinates as primitive data so that a pixel shader can perform custom attribute interpolation.</li>
</ul></td>
</tr>
<tr class="even">
<td align="left">Input</td>
<td align="left">Unlike vertex shaders, which operate on a single vertex, the geometry shader's inputs are the vertices for a full primitive (three vertices for triangles, two vertices for lines, or single vertex for point).</td>
</tr>
<tr class="odd">
<td align="left">Output</td>
<td align="left">The Geometry Shader (GS) stage is capable of outputting multiple vertices forming a single selected topology. Available geometry shader output topologies are <strong>tristrip</strong>, <strong>linestrip</strong>, and <strong>pointlist</strong>. The number of primitives emitted can vary freely within any invocation of the geometry shader, though the maximum number of vertices that could be emitted must be declared statically. Strip lengths emitted from a geometry shader invocation can be arbitrary, and new strips can be created via the [RestartStrip](https://msdn.microsoft.com/library/windows/desktop/bb509660) HLSL function.</td>
</tr>
</tbody>
</table>
-->
 

## <a name="stream-output-stage"></a>Этап потокового вывода
 

| | |
|-|-|
|Описание|[Этап потокового вывода (SO)](stream-output-stage--so-.md) непрерывно выводит (или выполняет потоковую передачу) данных вершин из предыдущего активного этапа в один или несколько буферов в памяти. Поток данных, выведенный в память, можно снова вернуть в конвейер как входные данные или обратно считать из ЦП.|
|Входные данные|Данные вершин из предыдущего этапа конвейера.|
|Выходные данные|Этап потокового вывода (SO) непрерывно выводит (или выполняет потоковую передачу) данных вершин из предыдущего активного этапа, например этапа геометрического шейдера (GS), в один или несколько буферов в памяти. Если этап шейдера геометрии (GS) неактивен, а этап потокового вывода (SO) активен, оно непрерывно выводит данные вершин из этапа шейдера доменов (DS) в буферы в памяти (а если этап шейдера доменов также неактивен, то из этапа шейдера вершин (VS)).|
| | |

<!---
<table>
<colgroup>
<col width="50%" />
<col width="50%" />
</colgroup>
<tbody>
<tr class="odd">
<td align="left">Purpose</td>
<td align="left">The [Stream Output (SO) stage](stream-output-stage--so-.md) continuously outputs (or streams) vertex data from the previous active stage to one or more buffers in memory. Data streamed out to memory can be recirculated back into the pipeline as input data, or read-back from the CPU.</td>
</tr>
<tr class="even">
<td align="left">Input</td>
<td align="left">Vertex data from a previous pipeline stage.   </td>
</tr>
<tr class="odd">
<td align="left">Output</td>
<td align="left">The Stream Output (SO) stage continuously outputs (or streams) vertex data from the previous active stage, such as the Geometry Shader (GS) stage, to one or more buffers in memory. If the Geometry Shader (GS) stage is inactive, and the Stream Output (SO) stage is active, it continuously outputs vertex data from the Domain Shader (DS) stage to buffers in memory (or if the DS is also inactive, from the Vertex Shader (VS) stage).</td>
</tr>
</tbody>
</table>
-->

## <a name="rasterizer-stage"></a>Этап растеризации
 

| | |
|-|-|
|Описание|[Этап растеризации (RS)](rasterizer-stage--rs-.md) обрезает примитивы, которых нет в представлении, готовит их для этапа построителя текстуры (PS) и определяет, как вызывать эти построители. Векторные данные (состоящие из фигур или примитивов) преобразуются в растровое изображение (состоящее из пикселей) для отображения трехмерной графики в режиме реального времени.|
|Входные данные|Предполагается, что вершины (x, y, z, w), поступающие на этап растеризации, находятся однородном пространстве обрезки. В этом пространстве координат точки оси X указывают вправо, точки Y — вверх, а точки Z — по направлению от камеры.|
|Выходные данные|Фактические пиксели для отрисовки. Включает некоторые атрибуты вершин для интерполяции построителем текстуры.|
| | |

<!---
<table>
<colgroup>
<col width="50%" />
<col width="50%" />
</colgroup>
<tbody>
<tr class="odd">
<td align="left">Purpose</td>
<td align="left">The [Rasterizer (RS) stage](rasterizer-stage--rs-.md) clips primitives that aren't in view, prepares primitives for the Pixel Shader (PS) stage, and determines how to invoke pixel shaders. Converts vector information (composed of shapes or primitives) into a raster image (composed of pixels) for the purpose of displaying real-time 3D graphics.</td>
</tr>
<tr class="even">
<td align="left">Input</td>
<td align="left">Vertices (x,y,z,w) coming into the Rasterizer stage are assumed to be in homogeneous clip-space. In this coordinate space the X axis points right, Y points up and Z points away from camera.</td>
</tr>
<tr class="odd">
<td align="left">Output</td>
<td align="left">The actual pixels that need to be rendered. Includes some vertex attributes for use in interpolation by the pixel Shader.</td>
</tr>
</tbody>
</table>
 -->

## <a name="pixel-shader-stage"></a>Этап построителя текстуры
 

| | |
|-|-|
|Описание|На [этапе построителя текстуры (PS)](pixel-shader-stage--ps-.md) принимаются интерполированные данные для примитива и генерируются данные для пикселей, такие как цвет.|
|Входные данные|Если конвейер настроен без шейдера геометрии, построитель текстуры ограничивается 16 32-разрядными 4-компонентными входными данными. В противном случае шейдер пикселей может принимать до 32 32-разрядных, 4-компонентных входных данных. Входные данные шейдера пикселей включают атрибуты вершин (которые можно интерполировать с коррекцией перспективы или без нее) или могут рассматриваться как константы примитивов. Входные данные шейдера пикселей интерполируются на основе атрибутов вершин растеризуемого примитива в зависимости от объявленного режима интерполяции. Если примитив обрезается до растеризации, режим интерполяции также учитывается в процессе обрезки. |
|Выходные данные|Построитель текстуры может вывести до 8 32-разрядных 4-компонентных цветов (если пиксель отклоняется, цвет не предоставляется). Компоненты выходного регистра построителя текстур следует объявить перед использованием. У каждого регистра может быть отдельная маска вывода записи.|
| | |

<!---
<table>
<colgroup>
<col width="50%" />
<col width="50%" />
</colgroup>
<tbody>
<tr class="odd">
<td align="left">Purpose</td>
<td align="left">The [Pixel Shader (PS) stage](pixel-shader-stage--ps-.md) receives interpolated data for a primitive and generates per-pixel data such as color.</td>
</tr>
<tr class="even">
<td align="left">Input</td>
<td align="left"><p>When the pipeline is configured without a geometry shader, a pixel shader is limited to 16, 32-bit, 4-component inputs. Otherwise, a pixel shader can take up to 32, 32-bit, 4-component inputs.</p>
<p>Pixel shader input data includes vertex attributes (that can be interpolated with or without perspective correction) or can be treated as per-primitive constants. Pixel shader inputs are interpolated from the vertex attributes of the primitive being rasterized, based on the interpolation mode declared. If a primitive gets clipped before rasterization, the interpolation mode is honored during the clipping process as well.</p></td>
</tr>
<tr class="odd">
<td align="left">Output</td>
<td align="left"><p>A pixel shader can output up to 8, 32-bit, 4-component colors, or no color if the pixel is discarded. Pixel shader output register components must be declared before they can be used; each register is allowed a distinct output-write mask.</p></td>
</tr>
</tbody>
</table>
-->
 

## <a name="output-merger-stage"></a>Этап слияния вывода
 

| | |
|-|-|
|Описание|На [этапе слияния вывода](output-merger-stage--om-.md) выходные данные различных типов (значения пиксельного шейдера, информация о глубине и наборе элементов) объединяются с содержимым целевого объекта отрисовки и буферов глубины и набора элементов для создания окончательного результата конвейера.|
|Входные данные|Входные данные средства слияния вывода— это состояние конвейера, пиксельные данные, сгенерированные построителями текстур, содержимое целевых объектов отрисовки и буферов глубины и трафаретов.|
|Выходные данные|Окончательный цвет отрисованных пикселей.|
| | |


<!---
<table>
<colgroup>
<col width="50%" />
<col width="50%" />
</colgroup>
<tbody>
<tr class="odd">
<td align="left">Purpose</td>
<td align="left">The [Output Merger (OM) stage](output-merger-stage--om-.md) combines various types of output data (pixel shader values, depth and stencil information) with the contents of the render target and depth/stencil buffers to generate the final pipeline result.</td>
</tr>
<tr class="even">
<td align="left">Input</td>
<td align="left"><ul>
<li>Pipeline state</li>
<li>The pixel data generated by the pixel shaders</li>
<li>The contents of the render targets</li>
<li>The contents of the depth/stencil buffers.</li>
</ul></td>
</tr>
<tr class="odd">
<td align="left">Output</td>
<td align="left">The final rendered pixel color.</td>
</tr>
</tbody>
</table>


| | |
|-|-|
|Purpose|xxxx|
|Input|yyyy|
|Output|zzzz|
| | |
-->

## <a name="related-topics"></a>Статьи по теме


[Обучающее руководство по графике Direct3D](index.md)

[Конвейер вычислений](compute-pipeline.md)

 

 
