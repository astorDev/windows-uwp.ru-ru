---
title: Сравнение конвейера шейдеров OpenGL ES 2.0 с Direct3D
description: Концептуально конвейер шейдеров Direct3D 11 очень похож на таковой в OpenGL ES 2.0.
ms.assetid: 3678a264-e3f9-72d2-be91-f79cd6f7c4ca
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp, игры, opengl, direct3d, конвейер шейдеров
ms.localizationpriority: medium
ms.openlocfilehash: f02b365175909b5038e5eb117f12851be9f14e3a
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57653699"
---
# <a name="compare-the-opengl-es-20-shader-pipeline-to-direct3d"></a>Сравнение конвейера шейдеров OpenGL ES 2.0 с Direct3D




**Важные API**

-   [Сборщик входных данных рабочей области](https://msdn.microsoft.com/library/windows/desktop/bb205116)
-   [Уровень построителя текстур вершин](https://msdn.microsoft.com/library/windows/desktop/bb205146#Vertex_Shader_Stage)
-   [Уровень построителя текстур пикселей](https://msdn.microsoft.com/library/windows/desktop/bb205146#Pixel_Shader_Stage)

Концептуально конвейер шейдеров Direct3D 11 очень похож на таковой в OpenGL ES 2.0. Однако в терминах проектного решения API главными компонентами для создания и управления стадиями шейдеров являются части двух основных интерфейсов, [**ID3D11Device1**](https://msdn.microsoft.com/library/windows/desktop/hh404575) и [**ID3D11DeviceContext1**](https://msdn.microsoft.com/library/windows/desktop/hh404598). В этом разделе делается попытка сопоставить общие шаблоны API конвейера шейдеров OpenGL ES 2.0 с их эквивалентами Direct3D 11 в этих интерфейсах.

## <a name="reviewing-the-direct3d-11-shader-pipeline"></a>Обзор конвейера шейдеров Direct3D 11


Объекты шейдера создаются с помощью методов в интерфейсе шейдера [**ID3D11Device1**](https://msdn.microsoft.com/library/windows/desktop/hh404575), таких как [**ID3D11Device1::CreateVertexShader**](https://msdn.microsoft.com/library/windows/desktop/ff476524) и [**ID3D11Device1::CreatePixelShader**](https://msdn.microsoft.com/library/windows/desktop/ff476513).

Конвейер графики Direct3D 11, который управляется экземплярами интерфейса [**ID3D11DeviceContext1**](https://msdn.microsoft.com/library/windows/desktop/hh404598), имеет следующие стадии.

-   [Стадия сборщика входных данных](https://msdn.microsoft.com/library/windows/desktop/bb205116). На стадии сборщика входных данных конвейеру поставляются данные (треугольники, линии и точки). [**ID3D11DeviceContext1** ](https://msdn.microsoft.com/library/windows/desktop/hh404598) методы, поддерживающие этот этап должны иметь префикс «IA».
-   [Стадия вершинного шейдера](https://msdn.microsoft.com/library/windows/desktop/bb205146#Vertex_Shader_Stage). На стадии вершинного шейдера обрабатываются вершины, обычно с выполнением таких операций, как преобразования, скиннинг и освещение. Вершинный шейдер всегда принимает одну входную вершину и создает одну выходную вершину. [**ID3D11DeviceContext1** ](https://msdn.microsoft.com/library/windows/desktop/hh404598) методы, поддерживающие этот этап должны иметь префикс «VS».
-   [Стадия потокового вывода](https://msdn.microsoft.com/library/windows/desktop/bb205121). На стадии потокового вывода поток данных примитивов из конвейера направляется в память на своем пути к средству прорисовки. Поток данных может быть выходным или передаваться в средство программной прорисовки. Поток данных, выведенный в память, можно снова вернуть в конвейер как входные данные или обратно считать из ЦП. [**ID3D11DeviceContext1** ](https://msdn.microsoft.com/library/windows/desktop/hh404598) методы, поддерживающие этот этап должны иметь префикс «Т».
-   [Стадия средства программной прорисовки](https://msdn.microsoft.com/library/windows/desktop/bb205125). Средство программной прорисовки обрезает примитивы, готовит их для построителя текстуры и определяет, как вызывать построители текстуры. Вы можете отключить растеризации, указывая конвейера имеется не шейдер пикселей (значение NULL с этап шейдера пикселей [ **ID3D11DeviceContext::PSSetShader**](https://msdn.microsoft.com/library/windows/desktop/ff476472)) и отключение глубины и тестирование (набор элементов Задайте значение FALSE в DepthEnable и StencilEnable [ **D3D11\_ГЛУБИНА\_ТРАФАРЕТА\_DESC**](https://msdn.microsoft.com/library/windows/desktop/ff476110)). После отключения не будут обновляться связанные с растеризацией счетчики конвейера.
-   [Стадия построителя текстуры](https://msdn.microsoft.com/library/windows/desktop/bb205146#Pixel_Shader_Stage). На стадии построителя текстуры принимаются интерполированные данные для примитива и генерируются данные для пикселей, такие как цвет. [**ID3D11DeviceContext1** ](https://msdn.microsoft.com/library/windows/desktop/hh404598) методы, поддерживающие этот этап должны иметь префикс «PS».
-   [Стадия слияния вывода](https://msdn.microsoft.com/library/windows/desktop/bb205120). На стадии слияния вывода выходные данные различных типов (значения построителя текстуры, информация о глубине и наборе элементов) объединяются с содержимым целевого объекта отрисовки и буферов глубины и набора элементов для создания окончательного результата конвейера. [**ID3D11DeviceContext1** ](https://msdn.microsoft.com/library/windows/desktop/hh404598) методы, поддерживающие этот этап должны иметь префикс «OM».

(Существуют также стадии для шейдеров геометрии, шейдеров поверхности, тесселяторов и шейдеров областей, но у них нет аналогов в OpenGL ES 2.0, поэтому здесь мы их обсуждать не будем.) Полный перечень методов для этих стадий см. на справочных страницах [**ID3D11DeviceContext**](https://msdn.microsoft.com/library/windows/desktop/ff476385) и [**ID3D11DeviceContext1**](https://msdn.microsoft.com/library/windows/desktop/hh404598). **ID3D11DeviceContext1** является расширением **ID3D11DeviceContext** для Direct3D 11.

## <a name="creating-a-shader"></a>Создание шейдера


В Direct3D ресурсы шейдера не создаются до компиляции и их загрузки. Ресурсы создаются, когда загружается HLSL. Таким образом, отсутствии непосредственно аналогично функции для glCreateShader, который создает ресурс шейдера инициализированный определенного типа (например GL\_ВЕРШИН\_ШЕЙДЕРА или GL\_ФРАГМЕНТ\_ШЕЙДЕРА). Вместо этого шейдеры создаются после загрузки HLSL с конкретными функциями, такими как [**ID3D11Device1::CreateVertexShader**](https://msdn.microsoft.com/library/windows/desktop/ff476524) и [**ID3D11Device1::CreatePixelShader**](https://msdn.microsoft.com/library/windows/desktop/ff476513), которые принимают тип и компилированный HLSL в качестве параметров.

| OpenGL ES 2.0  | Direct3D 11                                                                                                                                                                                                                                                             |
|----------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| glCreateShader | Вызовите [**ID3D11Device1::CreateVertexShader**](https://msdn.microsoft.com/library/windows/desktop/ff476524) и [**ID3D11Device1::CreatePixelShader**](https://msdn.microsoft.com/library/windows/desktop/ff476513) после успешной загрузки компилированного объекта шейдера (CSO), передавая их в этот объект CSO как буфер. |

 

## <a name="compiling-a-shader"></a>Компиляция шейдера


В приложениях для универсальной платформы Windows (UWP) шейдеры Direct3D должны предварительно компилироваться как файлы компилированного объекта шейдера (CSO-файлы) и загружаться с помощью одного из API файла среды выполнения Windows. (Классические приложения можно компилировать построители текстуры из текстовых файлов или строку во время выполнения). Файлы CSO создаются из любой .hlsl файлы, которые являются частью проекта Microsoft Visual Studio и сохранять те же имена, только с расширением файла .cso. Обеспечивайте их включение в ваш пакет при отправке!

| OpenGL ES 2.0                          | Direct3D 11                                                                                                                                                                   |
|----------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| glCompileShader                        | Н/д. Компилируйте шейдеры в CSO-файлы в Visual Studio и включайте их в пакет.                                                                                     |
| Использование glGetShaderiv для получения состояния компиляции | Н/д. Проверяйте по выходным результатам компилятора Visual Studio FX (FXC), имели ли место ошибки при компиляции. Если компиляция прошла успешна, создается соответствующий CSO-файл. |

 

## <a name="loading-a-shader"></a>Загрузка шейдера


Как показано в разделе о создании шейдера, Direct3D 11 создает шейдер, когда соответствующий CSO-файл загружается в буфер и передается в один из методов из следующей таблицы.

| OpenGL ES 2.0 | Direct3D 11                                                                                                                                                                                                                           |
|---------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ShaderSource  | Вызовите [**ID3D11Device1::CreateVertexShader**](https://msdn.microsoft.com/library/windows/desktop/ff476524) и [**ID3D11Device1::CreatePixelShader**](https://msdn.microsoft.com/library/windows/desktop/ff476513) после успешной загрузки компилированного объекта шейдера. |

 

## <a name="setting-up-the-pipeline"></a>Настройка конвейера


В OpenGL ES 2.0 есть объект "программа-шейдер", который содержит несколько шейдеров для выполнения. Отдельные шейдеры подключены к объекту программы-шейдера. Однако в Direct3D 11 вы работаете непосредственно с контекстом отрисовки ([**ID3D11DeviceContext1**](https://msdn.microsoft.com/library/windows/desktop/hh404598)) и создаете шейдеры в нем.

| OpenGL ES 2.0   | Direct3D 11                                                                                   |
|-----------------|-----------------------------------------------------------------------------------------------|
| glCreateProgram | Н/д. Direct3D 11 не использует абстракцию объекта программы-шейдера.                          |
| glLinkProgram   | Н/д. Direct3D 11 не использует абстракцию объекта программы-шейдера.                          |
| glUseProgram    | Н/д. Direct3D 11 не использует абстракцию объекта программы-шейдера.                          |
| glGetProgramiv  | Используйте созданную вами ссылку на [**ID3D11DeviceContext1**](https://msdn.microsoft.com/library/windows/desktop/hh404598). |

 

Создайте экземпляр [**ID3D11DeviceContext1**](https://msdn.microsoft.com/library/windows/desktop/hh404598) и [**ID3D11Device1**](https://msdn.microsoft.com/library/windows/desktop/dn280493) с помощью статического метода [**D3D11CreateDevice**](https://msdn.microsoft.com/library/windows/desktop/ff476082).

``` syntax
Microsoft::WRL::ComPtr<ID3D11Device1>          m_d3dDevice;
Microsoft::WRL::ComPtr<ID3D11DeviceContext1>  m_d3dContext;

// ...

D3D11CreateDevice(
  nullptr, // Specify nullptr to use the default adapter.
  D3D_DRIVER_TYPE_HARDWARE,
  nullptr,
  creationFlags, // Set set debug and Direct2D compatibility flags.
  featureLevels, // List of feature levels this app can support.
  ARRAYSIZE(featureLevels),
  D3D11_SDK_VERSION, // Always set this to D3D11_SDK_VERSION for UWP apps.
  &device, // Returns the Direct3D device created.
  &m_featureLevel, // Returns feature level of device created.
  &m_d3dContext // Returns the device's immediate context.
);
```

## <a name="setting-the-viewports"></a>Установка окон просмотра


Установка окна просмотра Direct3D 11 очень похожа на установку окна просмотра в OpenGL ES 2.0. В Direct3D 11, вызовите [ **ID3D11DeviceContext::RSSetViewports** ](https://msdn.microsoft.com/library/windows/desktop/ff476480) с настроенного [ **CD3D11\_ПРОСМОТРА**](https://msdn.microsoft.com/library/windows/desktop/jj151722).

Direct3D 11: Настройка окна просмотра.

``` syntax
CD3D11_VIEWPORT viewport(
        0.0f,
        0.0f,
        m_d3dRenderTargetSize.Width,
        m_d3dRenderTargetSize.Height
        );
m_d3dContext->RSSetViewports(1, &viewport);
```

| OpenGL ES 2.0 | Direct3D 11                                                                                                                                  |
|---------------|----------------------------------------------------------------------------------------------------------------------------------------------|
| glViewport    | [**CD3D11\_VIEWPORT**](https://msdn.microsoft.com/library/windows/desktop/jj151722), [**ID3D11DeviceContext::RSSetViewports**](https://msdn.microsoft.com/library/windows/desktop/ff476480) |

 

## <a name="configuring-the-vertex-shaders"></a>Настройка вершинных шейдеров


Настройка вершинного шейдера в Direct3D 11 выполняется, когда шейдер загружен. Универсальные объекты передаются как буферы констант с помощью [**ID3D11DeviceContext1::VSSetConstantBuffers1**](https://msdn.microsoft.com/library/windows/desktop/hh446795).

| OpenGL ES 2.0                    | Direct3D 11                                                                                               |
|----------------------------------|-----------------------------------------------------------------------------------------------------------|
| glAttachShader                   | [**ID3D11Device1::CreateVertexShader**](https://msdn.microsoft.com/library/windows/desktop/ff476524)                       |
| glGetShaderiv, glGetShaderSource | [**ID3D11DeviceContext1::VSGetShader**](https://msdn.microsoft.com/library/windows/desktop/ff476489)                       |
| glGetUniformfv, glGetUniformiv   | [**ID3D11DeviceContext1::VSGetConstantBuffers1**](https://msdn.microsoft.com/library/windows/desktop/hh446793). |

 

## <a name="configuring-the-pixel-shaders"></a>Настройка построителей текстуры


Настройка построителя текстуры в Direct3D 11 выполняется, когда шейдер загружен. Универсальные объекты передаются как буферы констант с помощью [**ID3D11DeviceContext1::PSSetConstantBuffers1**](https://msdn.microsoft.com/library/windows/desktop/hh404649).

| OpenGL ES 2.0                    | Direct3D 11                                                                                               |
|----------------------------------|-----------------------------------------------------------------------------------------------------------|
| glAttachShader                   | [**ID3D11Device1::CreatePixelShader**](https://msdn.microsoft.com/library/windows/desktop/ff476513)                         |
| glGetShaderiv, glGetShaderSource | [**ID3D11DeviceContext1::PSGetShader**](https://msdn.microsoft.com/library/windows/desktop/ff476468)                       |
| glGetUniformfv, glGetUniformiv   | [**ID3D11DeviceContext1::PSGetConstantBuffers1**](https://msdn.microsoft.com/library/windows/desktop/hh404645). |

 

## <a name="generating-the-final-results"></a>Генерация окончательных результатов


Когда конвейер завершает работу, результаты стадий шейдеров извлекаются в задний буфер. В Direct3D 11, так же как и в Open GL ES 2.0, это включает вызов команды рисования для вывода результатов в виде цветовой карты в задний буфер с последующей отправкой содержимого заднего буфера на экран.

| OpenGL ES 2.0  | Direct3D 11                                                                                                                                                                                                                                         |
|----------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| glDrawElements | [**ID3D11DeviceContext1::Draw**](https://msdn.microsoft.com/library/windows/desktop/ff476407), [ **ID3D11DeviceContext1::DrawIndexed** ](https://msdn.microsoft.com/library/windows/desktop/ff476409) (или других Draw\* методы [  **ID3D11DeviceContext1**](https://msdn.microsoft.com/library/windows/desktop/ff476385)). |
| eglSwapBuffers | [**IDXGISwapChain1::Present1**](https://msdn.microsoft.com/library/windows/desktop/hh446797)                                                                                                                                                                              |

 

## <a name="porting-glsl-to-hlsl"></a>Перенос GLSL в HLSL


Языки GLSL и HLSL не имеют существенных отличий, за исключением поддержки сложных типов и некоторых отличий в общем синтаксисе. Многие разработчики считают простейшим способом переноса альтернативное именование общих инструкций и определений OpenGL ES 2.0 для совмещения имен с их эквивалентами в HLSL. Отметим, что Direct3D использует версию модели шейдера для выражения набора функций HLSL, поддерживаемых графическим интерфейсом, тогда как OpenGL имеет другую спецификацию версии для HLSL. Следующая таблица должна дать вам примерное представление о наборе функций языка шейдера, определенных для Direct3D 11 и OpenGL ES 2.0, в терминах версии другого языка.

| Язык шейдера           | Версия функций GLSL                                                                                                                                                                                                      | Модель шейдера Direct3D |
|---------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------|
| Direct3D 11 HLSL          | ~4.30.                                                                                                                                                                                                                    | SM 5.0                |
| GLSL ES для OpenGL ES 2.0 | 1.40. Другие (более старые) реализации GLSL ES для OpenGL ES 2.0 могут использовать версии от 1.10 до 1.30. Проверьте исходный код с glGetString (GL\_ЗАЛИВКИ\_языка\_версии) или glGetString (ЗАЛИВКА\_языка\_версии) для определения его. | ~SM 2.0               |

 

Подробно о различиях двух языков шейдера, а также об общем сопоставлении синтаксиса см. в [справочнике по сопоставлению GLSL с HLSL](glsl-to-hlsl-reference.md).

## <a name="porting-the-opengl-intrinsics-to-hlsl-semantics"></a>Перенос встроенных элементов OpenGL в семантику HLSL


Семантика Direct3D 11 HLSL представляет строки, которые аналогично универсальным именам и именам атрибутов используются для идентификации значения, передаваемого между приложением и программой-шейдером. Хотя можно использовать любые из возможных строк, рекомендуется использовать строки, указывающие на использование, такие как POSITION или COLOR. Вы назначаете такую семантику при построении буфера констант или макета входного буфера. Можно также добавлять к семантике цифру от 0 до 7, чтобы использовать отдельные регистры для сходных значений. Например: ЦВЕТ2 COLOR0 COLOR1...

Семантики, начинаются с префикса «SV\_"являются семантику системных значений, созданных программой шейдера; их нельзя изменять само (запущенной на ЦП) приложение. Обычно они содержат значения, которые являются входными или выходными для другой стадии шейдера в конвейере графики или полностью генерируются ЦП.

Кроме того, SV\_ семантику иметь разные характеристики, когда они используются для указания входных данных или выходные данные из этапа шейдера. Например, SV\_ПОЗИЦИЯ (на выходе) содержит данные вершин, преобразуются во время этапа шейдеров вершин и SV\_ПОЗИЦИИ (input) содержит значения положение пикселя, интерполированные во время растеризации.

Перечислим некоторые сопоставления для общих встроенных элементов шейдера OpenGL ES 2.0:

| Системное значение OpenGL | Используйте семантику HLSL                                                                                                                                                   |
|---------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| GL\_позиции        | POSITION(n) для данных буфера вершин. SV\_положение предоставляет положение пикселя в шейдер пикселей и не может быть записан для приложения.                                        |
| GL\_норм.          | NORMAL(n) для данных нормали, предоставленных буфером вершин.                                                                                                                 |
| GL\_TexCoord\[n\]   | TEXCOORD(n) для данных UV-координат (ST в некоторой документации OpenGL) текстуры, которые предоставляются шейдером.                                                                       |
| GL\_FragColor       | COLOR(n) для данных цветов RGBA, которые предоставляются шейдеру. Отметим, что они обрабатываются аналогично данным координат. Семантика просто помогает понять, что они являются данными цветов. |
| GL\_FragData\[n\]   | SV\_целевой\[n\] для записи из построителя текстур пикселей текстуры цели или других буфер пикселя.                                                                               |

 

Метод, с помощью которого вы кодируете семантику, не совпадает с методом, используемым компонентами OpenGL ES 2.0. В OpenGL вы можете получить доступ ко многим внутренним элементам без настройки или объявления. В Direct3D для использования конкретной семантики необходимо объявить поле в определенном буфере констант. Можно также объявить возвращаемое значение метода **main()** шейдера.

Приведем пример семантики, используемой в определении буфера констант:

```cpp
struct VertexShaderInput
{
  float3 pos : POSITION;
  float3 color : COLOR0;
};

// The position is interpolated to the pixel value by the system. The per-vertex color data is also interpolated and passed through the pixel shader. 
struct PixelShaderInput
{
  float4 pos : SV_POSITION;
  float3 color : COLOR0;
};
```

Этот код определяет пару простых буферов констант

Вот пример семантики, используемой для определения значения, возвращаемого шейдером фрагментов:

```cpp
// A pass-through for the (interpolated) color data.
float4 main(PixelShaderInput input) : SV_TARGET
{
  return float4(input.color,1.0f);
}
```

В данном случае SV\_целевым ОБЪЕКТОМ является расположение целевого объекта отрисовки, записываемый цвет пикселя (определяется как вектор с четырьмя значениями с плавающей запятой), когда шейдер завершает выполнение.

Подробнее об использовании семантики с Direct3D: [Семантики HLSL](https://msdn.microsoft.com/library/windows/desktop/bb509647).

 

 




