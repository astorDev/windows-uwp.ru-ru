---
title: Сравнение конвейера шейдеров OpenGL ES 2.0 с Direct3D
description: Концептуально конвейер шейдеров Direct3D 11 очень похож на таковой в OpenGL ES 2.0.
ms.assetid: 3678a264-e3f9-72d2-be91-f79cd6f7c4ca
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp, игры, opengl, direct3d, конвейер шейдеров
ms.localizationpriority: medium
ms.openlocfilehash: 7a35102fed9993ca37afa1d1f47850427235ed49
ms.sourcegitcommit: cbd900f350569a3901086a44b2d5007bb6fb7bed
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2019
ms.locfileid: "72276293"
---
# <a name="compare-the-opengl-es-20-shader-pipeline-to-direct3d"></a>Сравнение конвейера шейдеров OpenGL ES 2.0 с Direct3D




**Важные API**

-   [Этап ввода-ассемблера](https://docs.microsoft.com/windows/desktop/direct3d11/d3d10-graphics-programming-guide-input-assembler-stage)
-   [Стадия вершинного шейдера](https://docs.microsoft.com/previous-versions/bb205146(v=vs.85))
-   [Этап построителя текстуры](https://docs.microsoft.com/previous-versions/bb205146(v=vs.85))

Концептуально конвейер шейдеров Direct3D 11 очень похож на таковой в OpenGL ES 2.0. Однако в терминах проектного решения API главными компонентами для создания и управления стадиями шейдеров являются части двух основных интерфейсов, [**ID3D11Device1**](https://docs.microsoft.com/windows/desktop/api/d3d11_1/nn-d3d11_1-id3d11device1) и [**ID3D11DeviceContext1**](https://docs.microsoft.com/windows/desktop/api/d3d11_1/nn-d3d11_1-id3d11devicecontext1). В этом разделе делается попытка сопоставить общие шаблоны API конвейера шейдеров OpenGL ES 2.0 с их эквивалентами Direct3D 11 в этих интерфейсах.

## <a name="reviewing-the-direct3d-11-shader-pipeline"></a>Обзор конвейера шейдеров Direct3D 11


Объекты шейдера создаются с помощью методов в интерфейсе шейдера [**ID3D11Device1**](https://docs.microsoft.com/windows/desktop/api/d3d11_1/nn-d3d11_1-id3d11device1), таких как [**ID3D11Device1::CreateVertexShader**](https://docs.microsoft.com/windows/desktop/api/d3d11/nf-d3d11-id3d11device-createvertexshader) и [**ID3D11Device1::CreatePixelShader**](https://docs.microsoft.com/windows/desktop/api/d3d11/nf-d3d11-id3d11device-createpixelshader).

Конвейер графики Direct3D 11, который управляется экземплярами интерфейса [**ID3D11DeviceContext1**](https://docs.microsoft.com/windows/desktop/api/d3d11_1/nn-d3d11_1-id3d11devicecontext1), имеет следующие стадии.

-   [Стадия сборщика входных данных](https://docs.microsoft.com/windows/desktop/direct3d11/d3d10-graphics-programming-guide-input-assembler-stage). На стадии сборщика входных данных конвейеру поставляются данные (треугольники, линии и точки). Методы [**ID3D11DeviceContext1**](https://docs.microsoft.com/windows/desktop/api/d3d11_1/nn-d3d11_1-id3d11devicecontext1) , поддерживающие этот этап, начинаются с префикса "IA".
-   [Стадия вершинного шейдера](https://docs.microsoft.com/previous-versions/bb205146(v=vs.85)). На стадии вершинного шейдера обрабатываются вершины, обычно с выполнением таких операций, как преобразования, скиннинг и освещение. Вершинный шейдер всегда принимает одну входную вершину и создает одну выходную вершину. Методы [**ID3D11DeviceContext1**](https://docs.microsoft.com/windows/desktop/api/d3d11_1/nn-d3d11_1-id3d11devicecontext1) , которые поддерживают этот этап, имеют префикс "VS".
-   [Стадия потокового вывода](https://docs.microsoft.com/windows/desktop/direct3d11/d3d10-graphics-programming-guide-output-stream-stage). На стадии потокового вывода поток данных примитивов из конвейера направляется в память на своем пути к средству прорисовки. Поток данных может быть выходным или передаваться в средство программной прорисовки. Поток данных, выведенный в память, можно снова вернуть в конвейер как входные данные или обратно считать из ЦП. Методы [**ID3D11DeviceContext1**](https://docs.microsoft.com/windows/desktop/api/d3d11_1/nn-d3d11_1-id3d11devicecontext1) , поддерживающие этот этап, начинаются с префикса "So".
-   [Стадия средства программной прорисовки](https://docs.microsoft.com/windows/desktop/direct3d11/d3d10-graphics-programming-guide-rasterizer-stage). Средство программной прорисовки обрезает примитивы, готовит их для построителя текстуры и определяет, как вызывать построители текстуры. Можно отключить растрирование, указав конвейеру, что в нем нет построителя текстуры (задайте для этапа шейдера пикселей значение NULL с помощью [**ссылку ID3D11DeviceContext::P ссетшадер**](https://docs.microsoft.com/windows/desktop/api/d3d11/nf-d3d11-id3d11devicecontext-pssetshader)) и отключите тестирование глубины и трафарета (установите Депсенабле и стенЦиленабле в значение false в [ **D3D11 @ no__t-4DEPTH @ no__t-5STENCIL @ no__t-6DESC**](https://docs.microsoft.com/windows/desktop/api/d3d11/ns-d3d11-d3d11_depth_stencil_desc)). После отключения не будут обновляться связанные с растеризацией счетчики конвейера.
-   [Стадия построителя текстуры](https://docs.microsoft.com/previous-versions/bb205146(v=vs.85)). На стадии построителя текстуры принимаются интерполированные данные для примитива и генерируются данные для пикселей, такие как цвет. Методы [**ID3D11DeviceContext1**](https://docs.microsoft.com/windows/desktop/api/d3d11_1/nn-d3d11_1-id3d11devicecontext1) , поддерживающие этот этап, начинаются с префикса "PS".
-   [Стадия слияния вывода](https://docs.microsoft.com/windows/desktop/direct3d11/d3d10-graphics-programming-guide-output-merger-stage). На стадии слияния вывода выходные данные различных типов (значения построителя текстуры, информация о глубине и наборе элементов) объединяются с содержимым целевого объекта отрисовки и буферов глубины и набора элементов для создания окончательного результата конвейера. Методы [**ID3D11DeviceContext1**](https://docs.microsoft.com/windows/desktop/api/d3d11_1/nn-d3d11_1-id3d11devicecontext1) , поддерживающие этот этап, начинаются с префикса "om".

(Существуют также стадии для шейдеров геометрии, шейдеров поверхности, тесселяторов и шейдеров областей, но у них нет аналогов в OpenGL ES 2.0, поэтому здесь мы их обсуждать не будем.) Полный перечень методов для этих стадий см. на справочных страницах [**ID3D11DeviceContext**](https://docs.microsoft.com/windows/desktop/api/d3d11/nn-d3d11-id3d11devicecontext) и [**ID3D11DeviceContext1**](https://docs.microsoft.com/windows/desktop/api/d3d11_1/nn-d3d11_1-id3d11devicecontext1). **ID3D11DeviceContext1** является расширением **ID3D11DeviceContext** для Direct3D 11.

## <a name="creating-a-shader"></a>Создание шейдера


В Direct3D ресурсы шейдера не создаются до компиляции и их загрузки. Ресурсы создаются, когда загружается HLSL. Таким образом, не существует прямой аналогичной функции для Глкреатешадер, которая создает инициализированный ресурс шейдера определенного типа (например, GL @ no__t-0VERTEX @ no__t-1SHADER или GL @ no__t-2FRAGMENT @ no__t-3SHADER). Вместо этого шейдеры создаются после загрузки HLSL с конкретными функциями, такими как [**ID3D11Device1::CreateVertexShader**](https://docs.microsoft.com/windows/desktop/api/d3d11/nf-d3d11-id3d11device-createvertexshader) и [**ID3D11Device1::CreatePixelShader**](https://docs.microsoft.com/windows/desktop/api/d3d11/nf-d3d11-id3d11device-createpixelshader), которые принимают тип и компилированный HLSL в качестве параметров.

| OpenGL ES 2.0  | Direct3D 11                                                                                                                                                                                                                                                             |
|----------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| glCreateShader | Вызовите [**ID3D11Device1::CreateVertexShader**](https://docs.microsoft.com/windows/desktop/api/d3d11/nf-d3d11-id3d11device-createvertexshader) и [**ID3D11Device1::CreatePixelShader**](https://docs.microsoft.com/windows/desktop/api/d3d11/nf-d3d11-id3d11device-createpixelshader) после успешной загрузки компилированного объекта шейдера (CSO), передавая их в этот объект CSO как буфер. |

 

## <a name="compiling-a-shader"></a>Компиляция шейдера


Шейдеры Direct3D должны быть предварительно скомпилированы как скомпилированные файлы объектов Shader (CSO) в универсальная платформа Windows (UWP) и загружены с помощью одного из среда выполнения Windows файловых API. (Классические приложения могут компилировать шейдеры из текстовых файлов или строки во время выполнения.) Файлы CSO построены на основе всех HLSL-файлов, которые являются частью проекта Microsoft Visual Studio, и сохраняются с одинаковыми именами, только с расширением файла cso. Обеспечивайте их включение в ваш пакет при отправке!

| OpenGL ES 2.0                          | Direct3D 11                                                                                                                                                                   |
|----------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| glCompileShader                        | Н/д. Компилируйте шейдеры в CSO-файлы в Visual Studio и включайте их в пакет.                                                                                     |
| Использование glGetShaderiv для получения состояния компиляции | Н/д. Проверяйте по выходным результатам компилятора Visual Studio FX (FXC), имели ли место ошибки при компиляции. Если компиляция прошла успешна, создается соответствующий CSO-файл. |

 

## <a name="loading-a-shader"></a>Загрузка шейдера


Как показано в разделе о создании шейдера, Direct3D 11 создает шейдер, когда соответствующий CSO-файл загружается в буфер и передается в один из методов из следующей таблицы.

| OpenGL ES 2.0 | Direct3D 11                                                                                                                                                                                                                           |
|---------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ShaderSource  | Вызовите [**ID3D11Device1::CreateVertexShader**](https://docs.microsoft.com/windows/desktop/api/d3d11/nf-d3d11-id3d11device-createvertexshader) и [**ID3D11Device1::CreatePixelShader**](https://docs.microsoft.com/windows/desktop/api/d3d11/nf-d3d11-id3d11device-createpixelshader) после успешной загрузки компилированного объекта шейдера. |

 

## <a name="setting-up-the-pipeline"></a>Настройка конвейера


В OpenGL ES 2.0 есть объект "программа-шейдер", который содержит несколько шейдеров для выполнения. Отдельные шейдеры подключены к объекту программы-шейдера. Однако в Direct3D 11 вы работаете непосредственно с контекстом отрисовки ([**ID3D11DeviceContext1**](https://docs.microsoft.com/windows/desktop/api/d3d11_1/nn-d3d11_1-id3d11devicecontext1)) и создаете шейдеры в нем.

| OpenGL ES 2.0   | Direct3D 11                                                                                   |
|-----------------|-----------------------------------------------------------------------------------------------|
| glCreateProgram | Н/д. Direct3D 11 не использует абстракцию объекта программы-шейдера.                          |
| glLinkProgram   | Н/д. Direct3D 11 не использует абстракцию объекта программы-шейдера.                          |
| glUseProgram    | Н/д. Direct3D 11 не использует абстракцию объекта программы-шейдера.                          |
| glGetProgramiv  | Используйте созданную вами ссылку на [**ID3D11DeviceContext1**](https://docs.microsoft.com/windows/desktop/api/d3d11_1/nn-d3d11_1-id3d11devicecontext1). |

 

Создайте экземпляр [**ID3D11DeviceContext1**](https://docs.microsoft.com/windows/desktop/api/d3d11_1/nn-d3d11_1-id3d11devicecontext1) и [**ID3D11Device1**](https://docs.microsoft.com/windows/desktop/api/d3d11_2/nn-d3d11_2-id3d11device2) с помощью статического метода [**D3D11CreateDevice**](https://docs.microsoft.com/windows/desktop/api/d3d11/nf-d3d11-d3d11createdevice).

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


Установка окна просмотра Direct3D 11 очень похожа на установку окна просмотра в OpenGL ES 2.0. В Direct3D 11 вызовите [**ссылку ID3D11DeviceContext:: рссетвиевпортс**](https://docs.microsoft.com/windows/desktop/api/d3d11/nf-d3d11-id3d11devicecontext-rssetviewports) с настроенным [**CD3D11 @ no__t-4VIEWPORT**](https://docs.microsoft.com/previous-versions/windows/desktop/legacy/jj151722(v=vs.85)).

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
| glViewport    | [**CD3D11 @ no__t-2VIEWPORT**](https://docs.microsoft.com/previous-versions/windows/desktop/legacy/jj151722(v=vs.85)), [ **ссылку ID3D11DeviceContext:: рссетвиевпортс**](https://docs.microsoft.com/windows/desktop/api/d3d11/nf-d3d11-id3d11devicecontext-rssetviewports) |

 

## <a name="configuring-the-vertex-shaders"></a>Настройка вершинных шейдеров


Настройка вершинного шейдера в Direct3D 11 выполняется, когда шейдер загружен. Универсальные объекты передаются как буферы констант с помощью [**ID3D11DeviceContext1::VSSetConstantBuffers1**](https://docs.microsoft.com/windows/desktop/api/d3d11_1/nf-d3d11_1-id3d11devicecontext1-vssetconstantbuffers1).

| OpenGL ES 2.0                    | Direct3D 11                                                                                               |
|----------------------------------|-----------------------------------------------------------------------------------------------------------|
| glAttachShader                   | [**ID3D11Device1:: Креатевертексшадер**](https://docs.microsoft.com/windows/desktop/api/d3d11/nf-d3d11-id3d11device-createvertexshader)                       |
| glGetShaderiv, glGetShaderSource | [**ID3D11DeviceContext1:: Всжетшадер**](https://docs.microsoft.com/windows/desktop/api/d3d11/nf-d3d11-id3d11devicecontext-vsgetshader)                       |
| glGetUniformfv, glGetUniformiv   | [**ID3D11DeviceContext1:: VSGetConstantBuffers1**](https://docs.microsoft.com/windows/desktop/api/d3d11_1/nf-d3d11_1-id3d11devicecontext1-vsgetconstantbuffers1). |

 

## <a name="configuring-the-pixel-shaders"></a>Настройка построителей текстуры


Настройка построителя текстуры в Direct3D 11 выполняется, когда шейдер загружен. Универсальные объекты передаются как буферы констант с помощью [**ID3D11DeviceContext1::PSSetConstantBuffers1.** ](https://docs.microsoft.com/windows/desktop/api/d3d11_1/nf-d3d11_1-id3d11devicecontext1-pssetconstantbuffers1).

| OpenGL ES 2.0                    | Direct3D 11                                                                                               |
|----------------------------------|-----------------------------------------------------------------------------------------------------------|
| glAttachShader                   | [**ID3D11Device1:: Креатепикселшадер**](https://docs.microsoft.com/windows/desktop/api/d3d11/nf-d3d11-id3d11device-createpixelshader)                         |
| glGetShaderiv, glGetShaderSource | [**ID3D11DeviceContext1::P Сжетшадер**](https://docs.microsoft.com/windows/desktop/api/d3d11/nf-d3d11-id3d11devicecontext-psgetshader)                       |
| glGetUniformfv, glGetUniformiv   | [**ID3D11DeviceContext1::P sgetconstantbuffers1**](https://docs.microsoft.com/windows/desktop/api/d3d11_1/nf-d3d11_1-id3d11devicecontext1-psgetconstantbuffers1). |

 

## <a name="generating-the-final-results"></a>Генерация окончательных результатов


Когда конвейер завершает работу, результаты стадий шейдеров извлекаются в задний буфер. В Direct3D 11, так же как и в Open GL ES 2.0, это включает вызов команды рисования для вывода результатов в виде цветовой карты в задний буфер с последующей отправкой содержимого заднего буфера на экран.

| OpenGL ES 2.0  | Direct3D 11                                                                                                                                                                                                                                         |
|----------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| glDrawElements | [**ID3D11DeviceContext1::D RAW**](https://docs.microsoft.com/windows/desktop/api/d3d11/nf-d3d11-id3d11devicecontext-draw), [**ID3D11DeviceContext1::D равиндексед**](https://docs.microsoft.com/windows/desktop/api/d3d11/nf-d3d11-id3d11devicecontext-drawindexed) (или другие методы Draw @ No__t-4 в [**ID3D11DeviceContext1**](https://docs.microsoft.com/windows/desktop/api/d3d11/nn-d3d11-id3d11devicecontext)). |
| eglSwapBuffers | [**IDXGISwapChain1::P resent1**](https://docs.microsoft.com/windows/desktop/api/dxgi1_2/nf-dxgi1_2-idxgiswapchain1-present1)                                                                                                                                                                              |

 

## <a name="porting-glsl-to-hlsl"></a>Перенос GLSL в HLSL


Языки GLSL и HLSL не имеют существенных отличий, за исключением поддержки сложных типов и некоторых отличий в общем синтаксисе. Многие разработчики считают простейшим способом переноса альтернативное именование общих инструкций и определений OpenGL ES 2.0 для совмещения имен с их эквивалентами в HLSL. Отметим, что Direct3D использует версию модели шейдера для выражения набора функций HLSL, поддерживаемых графическим интерфейсом, тогда как OpenGL имеет другую спецификацию версии для HLSL. Следующая таблица должна дать вам примерное представление о наборе функций языка шейдера, определенных для Direct3D 11 и OpenGL ES 2.0, в терминах версии другого языка.

| Язык шейдера           | Версия функций GLSL                                                                                                                                                                                                      | Модель шейдера Direct3D |
|---------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------|
| Direct3D 11 HLSL          | ~4.30.                                                                                                                                                                                                                    | SM 5.0                |
| GLSL ES для OpenGL ES 2.0 | 1.40. Другие (более старые) реализации GLSL ES для OpenGL ES 2.0 могут использовать версии от 1.10 до 1.30. Проверьте исходный код с помощью Глжетстринг (GL @ no__t-0SHADING @ no__t-1LANGUAGE @ no__t-2VERSION) или Глжетстринг (ЗАЛИВка @ no__t-3LANGUAGE @ no__t-4VERSION), чтобы определить его. | ~SM 2.0               |

 

Подробно о различиях двух языков шейдера, а также об общем сопоставлении синтаксиса см. в [справочнике по сопоставлению GLSL с HLSL](glsl-to-hlsl-reference.md).

## <a name="porting-the-opengl-intrinsics-to-hlsl-semantics"></a>Перенос встроенных элементов OpenGL в семантику HLSL


Семантика Direct3D 11 HLSL представляет строки, которые аналогично универсальным именам и именам атрибутов используются для идентификации значения, передаваемого между приложением и программой-шейдером. Хотя можно использовать любые из возможных строк, рекомендуется использовать строки, указывающие на использование, такие как POSITION или COLOR. Вы назначаете такую семантику при построении буфера констант или макета входного буфера. Можно также добавлять к семантике цифру от 0 до 7, чтобы использовать отдельные регистры для сходных значений. Пример: COLOR0, COLOR1, COLOR2...

Семантика с префиксом "SV @ no__t-0" — это семантика системных значений, записываемая программой шейдера; само приложение (работающее на ЦП) не может изменить их. Обычно они содержат значения, которые являются входными или выходными для другой стадии шейдера в конвейере графики или полностью генерируются ЦП.

Кроме того, семантика ОКП @ no__t-0 отличается поведением, когда они используются для указания входных данных или выходных данных на этапе шейдера. Например, ОКП @ no__t-0POSITION (Output) содержит данные вершин, преобразованные на этапе шейдера вершин, а ОКП @ no__t-1POSITION (входные данные) содержит значения позиций в пикселах, которые интерполируются во время растрирования.

Перечислим некоторые сопоставления для общих встроенных элементов шейдера OpenGL ES 2.0:

| Системное значение OpenGL | Используйте семантику HLSL                                                                                                                                                   |
|---------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| GL @ no__t-0Position        | POSITION(n) для данных буфера вершин. ОКП @ no__t-0POSITION предоставляет пиксельное построитель текстуры и не может быть записано приложением.                                        |
| GL @ no__t-0Normal          | NORMAL(n) для данных нормали, предоставленных буфером вершин.                                                                                                                 |
| GL @ no__t-0TexCoord @ no__t-1N @ no__t-2   | TEXCOORD(n) для данных UV-координат (ST в некоторой документации OpenGL) текстуры, которые предоставляются шейдером.                                                                       |
| GL @ no__t-0FragColor       | COLOR(n) для данных цветов RGBA, которые предоставляются шейдеру. Отметим, что они обрабатываются аналогично данным координат. Семантика просто помогает понять, что они являются данными цветов. |
| GL @ no__t-0FragData @ no__t-1N @ no__t-2   | ОКП @ no__t-0Target @ no__t-1N @ no__t-2 для записи из шейдера пикселей в целевую текстуру или другой буфер пикселей.                                                                               |

 

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

В этом случае ОКП @ no__t-0TARGET — это расположение целевого объекта отрисовки, на который записывается цвет пикселя (определяется как вектор с четырьмя значениями типа float), когда шейдер завершает выполнение.

Подробнее об использовании семантики с Direct3D: [Семантики HLSL](https://docs.microsoft.com/windows/desktop/direct3dhlsl/dx-graphics-hlsl-semantics).

 

 




