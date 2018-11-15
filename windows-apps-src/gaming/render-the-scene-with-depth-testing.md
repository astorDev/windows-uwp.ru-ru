---
author: mtoepke
title: Прорисовка сцены с проверкой глубины
description: Создание эффекта тени путем добавления проверки глубины в шейдер вершин (или геометрии) и в шейдер пикселей.
ms.assetid: bf496dfb-d7f5-af6b-d588-501164608560
ms.author: mtoepke
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp, игры, отрисовка, сцена, тестирование глубины, direct3d, тени
ms.localizationpriority: medium
ms.openlocfilehash: dc776a60e771cc8d5961e8c7b9c67eb99fabea3a
ms.sourcegitcommit: 71e8eae5c077a7740e5606298951bb78fc42b22c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/13/2018
ms.locfileid: "6667494"
---
# <a name="render-the-scene-with-depth-testing"></a>Прорисовка сцены с тестированием глубины




Создание эффекта тени путем добавления проверки глубины в шейдер вершин (или геометрии) и в построитель текстуры. [Пошаговое руководство. Реализация теневых объемов с помощью буферов глубины в Direct3D 11](implementing-depth-buffers-for-shadow-mapping.md), часть 3.

## <a name="include-transformation-for-light-frustum"></a>Включение преобразования для усеченного светового конуса


Вашему шейдеру вершин необходимо рассчитать преобразованное положение светового пространства для каждой вершины. Используя буфер констант, задайте модель светового пространства, представление и проекционные матрицы. Вы также можете использовать этот буфер констант для указания положения в световом пространстве и нормали для световых вычислений. Преобразованное положение в световом пространстве будет использоваться при проверке глубины.

```cpp
PixelShaderInput main(VertexShaderInput input)
{
    PixelShaderInput output;
    float4 pos = float4(input.pos, 1.0f);

    // Transform the vertex position into projected space.
    float4 modelPos = mul(pos, model);
    pos = mul(modelPos, view);
    pos = mul(pos, projection);
    output.pos = pos;

    // Transform the vertex position into projected space from the POV of the light.
    float4 lightSpacePos = mul(modelPos, lView);
    lightSpacePos = mul(lightSpacePos, lProjection);
    output.lightSpacePos = lightSpacePos;

    // Light ray
    float3 lRay = lPos.xyz - modelPos.xyz;
    output.lRay = lRay;
    
    // Camera ray
    output.view = eyePos.xyz - modelPos.xyz;

    // Transform the vertex normal into world space.
    float4 norm = float4(input.norm, 1.0f);
    norm = mul(norm, model);
    output.norm = norm.xyz;
    
    // Pass through the color and texture coordinates without modification.
    output.color = input.color;
    output.tex = input.tex;

    return output;
}
```

После этого по интерполированному положению в световом пространстве, предоставленному шейдером вершин, построитель текстуры будет проверять, находится ли пиксель в тени.

## <a name="test-whether-the-position-is-in-the-light-frustum"></a>Проверка нахождения в усеченном световом конусе


Сначала проверим, находится для пиксель в усеченном световом конусе, нормализовав координаты X и Y. Если обе координаты попадают в диапазон \[0, 1\], то пиксель может находиться в тени. В противном случае проверку глубины можно пропустить. Шейдер может быстро выполнить эту проверку, вызвав метод [Saturate](https://msdn.microsoft.com/library/windows/desktop/hh447231) и сравнив результат с исходным значением.

```cpp
// Compute texture coordinates for the current point's location on the shadow map.
float2 shadowTexCoords;
shadowTexCoords.x = 0.5f + (input.lightSpacePos.x / input.lightSpacePos.w * 0.5f);
shadowTexCoords.y = 0.5f - (input.lightSpacePos.y / input.lightSpacePos.w * 0.5f);
float pixelDepth = input.lightSpacePos.z / input.lightSpacePos.w;

float lighting = 1;

// Check if the pixel texture coordinate is in the view frustum of the 
// light before doing any shadow work.
if ((saturate(shadowTexCoords.x) == shadowTexCoords.x) &&
    (saturate(shadowTexCoords.y) == shadowTexCoords.y) &&
    (pixelDepth > 0))
{
```

## <a name="depth-test-against-the-shadow-map"></a>Проверка глубины по карте теней


Используйте функцию сравнения образцов ([SampleCmp](https://msdn.microsoft.com/library/windows/desktop/bb509696) или [SampleCmpLevelZero](https://msdn.microsoft.com/library/windows/desktop/bb509697)) для проверки глубины пикселя в световом пространстве по карте глубин. Рассчитайте нормализованное значение глубины светового пространства, которым является `z / w`, и передайте значение функции сравнения. Поскольку мы используем сравнительный тест LessOrEqual в образце, встроенная функция возвращает ноль при успешном тесте. Это означает, что пиксель в тени.

```cpp
// Use an offset value to mitigate shadow artifacts due to imprecise 
// floating-point values (shadow acne).
//
// This is an approximation of epsilon * tan(acos(saturate(NdotL))):
float margin = acos(saturate(NdotL));
#ifdef LINEAR
// The offset can be slightly smaller with smoother shadow edges.
float epsilon = 0.0005 / margin;
#else
float epsilon = 0.001 / margin;
#endif
// Clamp epsilon to a fixed range so it doesn't go overboard.
epsilon = clamp(epsilon, 0, 0.1);

// Use the SampleCmpLevelZero Texture2D method (or SampleCmp) to sample from 
// the shadow map, just as you would with Direct3D feature level 10_0 and
// higher.  Feature level 9_1 only supports LessOrEqual, which returns 0 if
// the pixel is in the shadow.
lighting = float(shadowMap.SampleCmpLevelZero(
    shadowSampler,
    shadowTexCoords,
    pixelDepth + epsilon
    )
    );
```

## <a name="compute-lighting-in-or-out-of-shadow"></a>Вычисление освещения в тени или за ее пределами


Если пиксель не находится в тени, построитель текстуры должен рассчитать прямое освещение и добавить его к значению пикселя.

```cpp
return float4(input.color * (ambient + DplusS(N, L, NdotL, input.view)), 1.f);
```

```cpp
float3 DplusS(float3 N, float3 L, float NdotL, float3 view)
{
    const float3 Kdiffuse = float3(.5f, .5f, .4f);
    const float3 Kspecular = float3(.2f, .2f, .3f);
    const float exponent = 3.f;

    // Compute the diffuse coefficient.
    float diffuseConst = saturate(NdotL);

    // Compute the diffuse lighting value.
    float3 diffuse = Kdiffuse * diffuseConst;

    // Compute the specular highlight.
    float3 R = reflect(-L, N);
    float3 V = normalize(view);
    float3 RdotV = dot(R, V);
    float3 specular = Kspecular * pow(saturate(RdotV), exponent);

    return (diffuse + specular);
}
```

В противном случае построитель текстуры должен вычислить значение пикселя, используя внешнее освещение.

```cpp
return float4(input.color * ambient, 1.f);
```

В следующей части этого пошагового руководства вы научитесь [реализовывать поддержку карт теней на разных типах оборудования](target-a-range-of-hardware.md).

 

 




