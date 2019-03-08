---
title: Перенос объектов шейдеров
description: При переносе простого обработчика из OpenGL ES 2.0 прежде всего следует задать эквивалентные объекты вершинного шейдера и шейдера фрагментов в Direct3D 11 и убедиться, что основная программа может связываться с объектами шейдера после их компиляции.
ms.assetid: 0383b774-bc1b-910e-8eb6-cc969b3dcc08
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp, игры, порт, шейдер, direct3d, opengl
ms.localizationpriority: medium
ms.openlocfilehash: f061d31ca779cb4c6cbe76f163e190996a6985cb
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57618749"
---
# <a name="port-the-shader-objects"></a>Перенос объектов шейдеров




**Важные API**

-   [**ID3D11Device**](https://msdn.microsoft.com/library/windows/desktop/ff476379)
-   [**ID3D11DeviceContext**](https://msdn.microsoft.com/library/windows/desktop/ff476385)

При переносе простого обработчика из OpenGL ES 2.0 прежде всего следует задать эквивалентные объекты вершинного шейдера и шейдера фрагментов в Direct3D 11 и убедиться, что основная программа может связываться с объектами шейдера после их компиляции.

> **Примечание**    создали новый проект Direct3D? Если нет, выполните инструкции из раздела [Создание нового проекта DirectX 11 для универсальной платформы Windows (UWP)](user-interface.md). В этом пошаговом руководстве предполагается, что вы уже создали ресурсы DXGI и Direct3D для рисования на экране, которые предоставлены в шаблоне.

 

Как и в OpenGL ES 2.0, компилированные шейдеры в Direct3D должны быть связаны с контекстом рисования. Однако в Direct3D нет понятия объекта программы-шейдера как таковой. Вместо этого вы должны напрямую назначить шейдеры в [**ID3D11DeviceContext**](https://msdn.microsoft.com/library/windows/desktop/ff476385). Этот шаг следует за процессом создания и привязки объектов шейдера в OpenGL ES 2.0 и предоставляет вам возможности соответствующих API в Direct3D.

<a name="instructions"></a>Инструкция
------------

### <a name="step-1-compile-the-shaders"></a>Шаг 1. Скомпилируйте построители текстуры

В этом простом примере OpenGL ES 2.0 шейдеры сохраняются как текстовые файлы и загружаются как строковые данные для компиляции во время выполнения.

OpenGL ES 2.0: Компиляция шейдера

``` syntax
GLuint __cdecl CompileShader (GLenum shaderType, const char *shaderSrcStr)
// shaderType can be GL_VERTEX_SHADER or GL_FRAGMENT_SHADER. Returns 0 if compilation fails.
{
  GLuint shaderHandle;
  GLint compiledShaderHandle;
   
  // Create an empty shader object.
  shaderHandle = glCreateShader(shaderType);

  if (shaderHandle == 0)
  return 0;

  // Load the GLSL shader source as a string value. You could obtain it from
  // from reading a text file or hardcoded.
  glShaderSource(shaderHandle, 1, &shaderSrcStr, NULL);
   
  // Compile the shader.
  glCompileShader(shaderHandle);

  // Check the compile status
  glGetShaderiv(shaderHandle, GL_COMPILE_STATUS, &compiledShaderHandle);

  if (!compiledShaderHandle) // error in compilation occurred
  {
    // Handle any errors here.
              
    glDeleteShader(shaderHandle);
    return 0;
  }

  return shaderHandle;

}
```

В Direct3D шейдеры не компилируются во время выполнения; они всегда откомпилированы в CSO-файлы, когда компилируется остальная часть программы. Когда вы компилируете свое приложение в Microsoft Visual Studio, HLSL-файлы компилируются в CSO-файлы, которые должно загрузить ваше приложение. Не забудьте включить эти CSO-файлы в свое приложение при его упаковке!

> **Примечание**    в следующем примере выполняется загрузка шейдеров и компиляции, в асинхронном режиме с помощью **автоматически** синтаксис ключевого слова и лямбда-выражения. Метод ReadDataAsync() реализован для шаблона, который читает CSO-файл как массив байтовых данных (fileData).

 

Direct3D 11: Компиляция шейдера

``` syntax
auto loadVSTask = DX::ReadDataAsync(m_projectDir + "SimpleVertexShader.cso");
auto loadPSTask = DX::ReadDataAsync(m_projectDir + "SimplePixelShader.cso");

auto createVSTask = loadVSTask.then([this](Platform::Array<byte>^ fileData) {

m_d3dDevice->CreateVertexShader(
  fileData->Data,
  fileData->Length,
  nullptr,
  &m_vertexShader);

auto createPSTask = loadPSTask.then([this](Platform::Array<byte>^ fileData) {
  m_d3dDevice->CreatePixelShader(
    fileData->Data,
    fileData->Length,
    nullptr,
    &m_pixelShader;
};
```

### <a name="step-2-create-and-load-the-vertex-and-fragment-pixel-shaders"></a>Шаг 2. Создавать и загружать вершины и fragment (шейдеры)

В OpenGL ES 2.0 имеется понятие «программы» шейдера, которая служит интерфейсом между основной программой, выполняющейся на ЦП, и шейдерами, которые выполняются на GPU. Шейдеры компилируются (или загружаются из компилированных источников) и связываются с программой, которая включает выполнение на GPU.

OpenGL ES 2.0: Загрузка шейдеров вершин и фрагмент в программу заливки

``` syntax
GLuint __cdecl LoadShaderProgram (const char *vertShaderSrcStr, const char *fragShaderSrcStr)
{
  GLuint programObject, vertexShaderHandle, fragmentShaderHandle;
  GLint linkStatusCode;

  // Load the vertex shader and compile it to an internal executable format.
  vertexShaderHandle = CompileShader(GL_VERTEX_SHADER, vertShaderSrcStr);
  if (vertexShaderHandle == 0)
  {
    glDeleteShader(vertexShaderHandle);
    return 0;
  }

   // Load the fragment/pixel shader and compile it to an internal executable format.
  fragmentShaderHandle = CompileShader(GL_FRAGMENT_SHADER, fragShaderSrcStr);
  if (fragmentShaderHandle == 0)
  {
    glDeleteShader(fragmentShaderHandle);
    return 0;
  }

  // Create the program object proper.
  programObject = glCreateProgram();
   
  if (programObject == 0)    return 0;

  // Attach the compiled shaders
  glAttachShader(programObject, vertexShaderHandle);
  glAttachShader(programObject, fragmentShaderHandle);

  // Compile the shaders into binary executables in memory and link them to the program object..
  glLinkProgram(programObject);

  // Check the project object link status and determine if the program is available.
  glGetProgramiv(programObject, GL_LINK_STATUS, &linkStatusCode);

  if (!linkStatusCode) // if link status <> 0
  {
    // Linking failed; delete the program object and return a failure code (0).

    glDeleteProgram (programObject);
    return 0;
  }

  // Deallocate the unused shader resources. The actual executables are part of the program object.
  glDeleteShader(vertexShaderHandle);
  glDeleteShader(fragmentShaderHandle);

  return programObject;
}

// ...

glUseProgram(renderer->programObject);
```

В Direct3D нет понятия объекта программы-шейдера. Вместо этого шейдеры создаются, когда вызывается один из методов создания шейдеров в интерфейсе [**ID3D11Device**](https://msdn.microsoft.com/library/windows/desktop/ff476379) (такой как [**ID3D11Device::CreateVertexShader**](https://msdn.microsoft.com/library/windows/desktop/ff476524) или [**ID3D11Device::CreatePixelShader**](https://msdn.microsoft.com/library/windows/desktop/ff476513)). Чтобы задать шейдеры для текущего контекста рисования, мы предоставляем их для текущего [**ID3D11DeviceContext**](https://msdn.microsoft.com/library/windows/desktop/ff476385) с помощью метода установки шейдера, такого как [**ID3D11DeviceContext::VSSetShader**](https://msdn.microsoft.com/library/windows/desktop/ff476493) для вершинного шейдера или [**ID3D11DeviceContext::PSSetShader**](https://msdn.microsoft.com/library/windows/desktop/ff476472) для шейдера фрагментов.

Direct3D 11: Задайте шейдеров для рисования контекст графического устройства.

``` syntax
m_d3dContext->VSSetShader(
  m_vertexShader.Get(),
  nullptr,
  0);

m_d3dContext->PSSetShader(
  m_pixelShader.Get(),
  nullptr,
  0);
```

### <a name="step-3-define-the-data-to-supply-to-the-shaders"></a>Шаг 3. Определение данных для предоставления построители текстуры

В нашем примере OpenGL ES 2.0 имеется один **однородный элемент**, объявляемый для конвейера шейдера:

-   **u\_mvpMatrix**: массив 4 x 4 из числа с плавающей запятой, представляющий матрицу окончательного преобразования проекции model-view, принимающий модели координаты для куба и преобразовывает их в двухмерных проекции координаты для преобразования сканирования.

И два значения **атрибутов** для данных вершины:

-   **\_позиции**: вектор 4 с плавающей запятой для модели координаты вершины.
-   **\_цвет**: Вектор 4 с плавающей запятой для значение RGBA цвета, связанных с вершиной.

Open GL ES 2.0: Определения GLSL форменная одежда и атрибуты

``` syntax
uniform mat4 u_mvpMatrix;
attribute vec4 a_position;
attribute vec4 a_color;
```

Соответствующие элементы основной программы в данном случае определяются как поля объекта обработчика. (См. в заголовке [как: порт простой модуль подготовки отчетов OpenGL ES 2.0 на Direct3D 11](port-a-simple-opengl-es-2-0-renderer-to-directx-11-1.md).) После сделано, необходимо указать местоположение в памяти, где основной программы будут получены эти значения для конвейера шейдера, что мы обычно делаем прямо перед вызовом draw:

OpenGL ES 2.0: Расположения данные неравномерно и атрибутов

``` syntax

// Inform the shader of the attribute locations
loc = glGetAttribLocation(renderer->programObject, "a_position");
glVertexAttribPointer(loc, 3, GL_FLOAT, GL_FALSE, 
    sizeof(Vertex), 0);
glEnableVertexAttribArray(loc);

loc = glGetAttribLocation(renderer->programObject, "a_color");
glVertexAttribPointer(loc, 4, GL_FLOAT, GL_FALSE, 
    sizeof(Vertex), (GLvoid*) (sizeof(float) * 3));
glEnableVertexAttribArray(loc);


// Inform the shader program of the uniform location
renderer->mvpLoc = glGetUniformLocation(renderer->programObject, "u_mvpMatrix");
```

В Direct3D отсутствуют понятия "атрибута" или "однородного элемента" в том же смысле (по крайней мере, отсутствует такой синтаксис). Вместо этого имеются буферы констант, представленные как подчиненные ресурсы Direct3D, то есть ресурсы, которые совместно используются основной программой и программами-шейдерами. Некоторые из этих подчиненных ресурсов, такие как позиции вершин и цвета, описываются семантикой HLSL. Дополнительные сведения о связи буферов констант и семантики HLSL с понятиями OpenGL ES 2.0 см. в разделе [Перенос объектов буферов кадра, однородных элементов и атрибутов](porting-uniforms-and-attributes.md).

При переносе этого процесса в Direct3D мы преобразуем однородный элемент в буфер констант Direct3D (cbuffer) и назначаем его регистру для подстановки с помощью семантики HLSL **register**. Два атрибута вершины обрабатываются как входные элементы на стадиях конвейера шейдера. Им также назначается [семантика HLSL](https://msdn.microsoft.com/library/windows/desktop/bb205574) (POSITION и COLOR0), поставляющая информацию для шейдеров. Построитель текстуры принимает SV\_ПОЗИЦИИ с SV\_ префикс, что это значение системы, сформированное GPU. (В данном случае это положение пикселя, созданные в процессе преобразования сканирования.) VertexShaderInput и PixelShaderInput не объявляются как константа помещает в буфер, поскольку первое из них будет использоваться для определения буфера вершин (см. в разделе [порта буферы вершин и данных](port-the-vertex-buffers-and-data-config.md)), и данные для второго формируется в результате Предыдущий этап конвейера, что в данном случае является вершинный построитель текстуры.

Direct3D: Определения HLSL для постоянные буферы и данные вершин

``` syntax
cbuffer ModelViewProjectionConstantBuffer : register(b0)
{
  matrix mvp;
};

// Per-vertex data used as input to the vertex shader.
struct VertexShaderInput
{
  float4 pos : POSITION;
  float4 color : COLOR0;
};

// Per-vertex color data passed through the pixel shader.
struct PixelShaderInput
{
  float4 pos : SV_POSITION;
  float3 color : COLOR0;
};
```

Дополнительные сведения о переносе семантики HLSL в буферы констант и приложения см. в разделе [Портирование объектов буферов кадра, однородных элементов и атрибутов](porting-uniforms-and-attributes.md).

Перечислим структуры для макетов данных, переданных в конвейер шейдера с помощью буфера констант или буфера вершин.

Direct3D 11: Объявление его компоновки буферы констант и вершин

``` syntax
// Constant buffer used to send MVP matrices to the vertex shader.
struct ModelViewProjectionConstantBuffer
{
  DirectX::XMFLOAT4X4 modelViewProjection;
};

// Used to send per-vertex data to the vertex shader.
struct VertexPositionColor
{
  DirectX::XMFLOAT4 pos;
  DirectX::XMFLOAT4 color;
};
```

Использовать DirectXMath XM\* типы для константу буфер элементов, так как они обеспечивают правильное упаковки и выравнивания содержимого, если они отправляются в конвейер построителя текстуры. Если вы используете стандартные типы с плавающей точкой и массивы платформы Windows, вы должны выполнить упаковку и выравнивание самостоятельно.

Чтобы привязать буфер констант, создать описание макета как [ **CD3D11\_БУФЕРА\_DESC** ](https://msdn.microsoft.com/library/windows/desktop/jj151620) структуры и передать его в [ **ID3DDevice:: CreateBuffer**](https://msdn.microsoft.com/library/windows/desktop/ff476501). Затем в своем методе обработки передайте буфер констант в [**ID3D11DeviceContext::UpdateSubresource**](https://msdn.microsoft.com/library/windows/desktop/ff476486) перед рисованием.

Direct3D 11: Привязать буфер констант

``` syntax
CD3D11_BUFFER_DESC constantBufferDesc(sizeof(ModelViewProjectionConstantBuffer), D3D11_BIND_CONSTANT_BUFFER);

m_d3dDevice->CreateBuffer(
  &constantBufferDesc,
  nullptr,
  &m_constantBuffer);

// ...

// Only update shader resources that have changed since the last frame.
m_d3dContext->UpdateSubresource(
  m_constantBuffer.Get(),
  0,
  NULL,
  &m_constantBufferData,
  0,
  0);
```

Буфер вершин создается и обновляется аналогично, как обсуждается на следующем шаге, в разделе [Перенос буферов вершин и данных](port-the-vertex-buffers-and-data-config.md).

<a name="next-step"></a>Дальнейшие действия
---------

[Перенос данных и буферов вершин](port-the-vertex-buffers-and-data-config.md)
## <a name="related-topics"></a>Статьи по теме


[Практическое: порт простой модуль подготовки отчетов OpenGL ES 2.0 на Direct3D 11](port-a-simple-opengl-es-2-0-renderer-to-directx-11-1.md)

[Перенос данных и буферов вершин](port-the-vertex-buffers-and-data-config.md)

[Перенос GLSL](port-the-glsl.md)

[Рисование объектов на экране](draw-to-the-screen.md)

 

 




