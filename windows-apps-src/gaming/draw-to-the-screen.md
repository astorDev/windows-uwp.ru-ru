---
title: Рисование объектов на экране
description: Наконец, мы переносим код, который отрисовывает вращающийся куб на экране.
ms.assetid: cc681548-f694-f613-a19d-1525a184d4ab
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp, игры, directx, графика
ms.localizationpriority: medium
ms.openlocfilehash: fc93111d48f71a6ca8acad8191a2afb535fad2f0
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57660939"
---
# <a name="draw-to-the-screen"></a>Рисование объектов на экране




**Важные API**

-   [**ID3D11Texture2D**](https://msdn.microsoft.com/library/windows/desktop/ff476635)
-   [**ID3D11RenderTargetView**](https://msdn.microsoft.com/library/windows/desktop/ff476582)
-   [**IDXGISwapChain1**](https://msdn.microsoft.com/library/windows/desktop/hh404631)

Наконец, мы переносим код, который отрисовывает вращающийся куб на экране.

В OpenGL ES 2.0 тип контекста отрисовки определяется как EGLContext. Он содержит параметры окна и поверхности, а также ресурсы, необходимые для отрисовки в однобуферной прорисовке, которая будет использована для создания конечного изображения, отображаемого в окне. Вы используете этот контекст, чтобы настроить графические ресурсы на правильное отображение результатов конвейера шейдеров на дисплее. Одним из основных ресурсов является задний буфер (или объект буфера кадра), который содержит окончательную составленную однобуферную прорисовку, готовую к представлению на дисплее.

При использовании Direct3D процесс настройки графических ресурсов для отрисовки на дисплее носит более дидактический характер и требует большего количества API. (Шаблон Microsoft Visual Studio Direct3D могут значительно упростить этот процесс, однако!) Чтобы получить контекст (называемые контекста устройства Direct3D), необходимо сначала получить [ **ID3D11Device1** ](https://msdn.microsoft.com/library/windows/desktop/hh404575) и использовать его для создания и настройки [ **ID3D11DeviceContext1**  ](https://msdn.microsoft.com/library/windows/desktop/hh404598) объекта. Эти два объекта используются совместно для настройки определенных ресурсов, необходимых для отрисовки на дисплее.

Если коротко, API DXGI содержат в основном API для управления ресурсами, которые имеют прямое отношение к графическому адаптеру, а Direct3D содержит API, которые обеспечивают интерфейс между GPU и вашей главной программой, работающей на ЦП.

Для сравнения в этом примере представлены соответствующие типы тех и других API.

-   [**ID3D11Device1**](https://msdn.microsoft.com/library/windows/desktop/hh404575): обеспечивает представление виртуального графического устройства и его ресурсы.
-   [**ID3D11DeviceContext1**](https://msdn.microsoft.com/library/windows/desktop/hh404598): предоставляет интерфейс для настройки буферов и выдачи команд отрисовки.
-   [**IDXGISwapChain1**](https://msdn.microsoft.com/library/windows/desktop/hh404631): цепочки буферов является аналогом задний буфер в OpenGL ES 2.0. Это область памяти на графическом адаптере, которая содержит окончательные обработанные изображения для дисплея. Она называется цепочкой буферов, потому что содержит несколько буферов, в которые можно записать последнюю обработку и которые можно переключить для ее представления на экране.
-   [**ID3D11RenderTargetView**](https://msdn.microsoft.com/library/windows/desktop/ff476582): этот файл содержит буфера 2D растрового изображения, что контекст устройства Direct3D рисует в, и которая представил цепочки буферов. Как и в OpenGL ES 2.0, вы можете использовать многобуферную прорисовку, часть буферов которой не привязана к цепочке буферов, но используется для методов многоэтапной заливки.

В шаблоне объект обработчика содержит следующие поля.

Direct3D 11: Устройства и устройства контекстных объявлениях

``` syntax
Platform::Agile<Windows::UI::Core::CoreWindow>       m_window;

Microsoft::WRL::ComPtr<ID3D11Device1>                m_d3dDevice;
Microsoft::WRL::ComPtr<ID3D11DeviceContext1>          m_d3dContext;
Microsoft::WRL::ComPtr<IDXGISwapChain1>                      m_swapChainCoreWindow;
Microsoft::WRL::ComPtr<ID3D11RenderTargetView>          m_d3dRenderTargetViewWin;
```

Вот как можно настроить задний буфер в качестве однобуферной прорисовки и предоставить его цепочке буферов.

``` syntax
ComPtr<ID3D11Texture2D> backBuffer;
m_swapChainCoreWindow->GetBuffer(0, IID_PPV_ARGS(backBuffer));
m_d3dDevice->CreateRenderTargetView(
  backBuffer.Get(),
  nullptr,
  &m_d3dRenderTargetViewWin);
```

Среда выполнения Direct3D неявно создает [**IDXGISurface1**](https://msdn.microsoft.com/library/windows/desktop/ff471343) для [**ID3D11Texture2D**](https://msdn.microsoft.com/library/windows/desktop/ff476635), представляющий текстуру в качестве заднего буфера, который в свою очередь может использовать цепочка буферов для отображения.

Инициализацию и настройку устройства и контекста устройства Direct3D, а также однобуферную прорисовку можно найти в пользовательских методах **CreateDeviceResources** и **CreateWindowSizeDependentResources** в шаблоне Direct3D.

Подробнее о контексте устройства Direct3D и его отношении к типам контекста EGL и EGLContext читайте в разделе [Перенос кода EGL в DXGI и Direct3D](moving-from-egl-to-dxgi.md).

## <a name="instructions"></a>Инструкция

### <a name="step-1-rendering-the-scene-and-displaying-it"></a>Шаг 1. Отрисовку сцены и его отображения

После обновления данных куба (в нашем случае путем небольшого вращения вокруг оси Y) метод обработки настраивает окно просмотра в соответствии с размерами контекста обработки (EGLContext). Этот контекст содержит буфер цвета, который будет отображен на поверхности окна (EGLSurface) с помощью настроенного дисплея (EGLDisplay). В это время пример обновляет атрибуты данных вершины, повторно привязывает буфер индексов, отрисовывает куб и подключает буфер цвета, отрисованный конвейером заливки на поверхности отображения.

OpenGL ES 2.0: Отрисовке определенного кадра для отображения

``` syntax
void Render(GraphicsContext *drawContext)
{
  Renderer *renderer = drawContext->renderer;

  int loc;
   
  // Set the viewport
  glViewport ( 0, 0, drawContext->width, drawContext->height );
   
   
  // Clear the color buffer
  glClear(GL_COLOR_BUFFER_BIT | GL_DEPTH_BUFFER_BIT);
  glEnable(GL_DEPTH_TEST);


  // Use the program object
  glUseProgram (renderer->programObject);

  // Load the a_position attribute with the vertex position portion of a vertex buffer element
  loc = glGetAttribLocation(renderer->programObject, "a_position");
  glVertexAttribPointer(loc, 3, GL_FLOAT, GL_FALSE, 
      sizeof(Vertex), 0);
  glEnableVertexAttribArray(loc);

  // Load the a_color attribute with the color position portion of a vertex buffer element
  loc = glGetAttribLocation(renderer->programObject, "a_color");
  glVertexAttribPointer(loc, 4, GL_FLOAT, GL_FALSE, 
      sizeof(Vertex), (GLvoid*) (sizeof(float) * 3));
  glEnableVertexAttribArray(loc);

  // Bind the index buffer
  glBindBuffer(GL_ELEMENT_ARRAY_BUFFER, renderer->indexBuffer);

  // Load the MVP matrix
  glUniformMatrix4fv(renderer->mvpLoc, 1, GL_FALSE, (GLfloat*) &renderer->mvpMatrix.m[0][0]);

  // Draw the cube
  glDrawElements(GL_TRIANGLES, renderer->numIndices, GL_UNSIGNED_INT, 0);

  eglSwapBuffers(drawContext->eglDisplay, drawContext->eglSurface);
}
```

В Direct3D 11 процесс очень прост. (Мы предполагаем, что вы используете окно просмотра и конфигурацию однобуферной прорисовки шаблона Direct3D.)

-   Обновите буферы констант (в данном случае — матрицу «модель-представление-проекция») с помощью вызовов [**ID3D11DeviceContext1::UpdateSubresource**](https://msdn.microsoft.com/library/windows/desktop/hh446790).
-   Установите буфер вершин с помощью [**ID3D11DeviceContext1::IASetVertexBuffers**](https://msdn.microsoft.com/library/windows/desktop/ff476456).
-   Установите буфер индексов с помощью [**ID3D11DeviceContext1::IASetIndexBuffer**](https://msdn.microsoft.com/library/windows/desktop/ff476453).
-   Установите специфическую треугольную топологию (список треугольников) с помощью [**ID3D11DeviceContext1::IASetPrimitiveTopology**](https://msdn.microsoft.com/library/windows/desktop/ff476455).
-   Установите входной макет буфера вершин с помощью [**ID3D11DeviceContext1::IASetInputLayout**](https://msdn.microsoft.com/library/windows/desktop/ff476454).
-   Привяжите вершинный шейдер с помощью [**ID3D11DeviceContext1::VSSetShader**](https://msdn.microsoft.com/library/windows/desktop/ff476493).
-   Привяжите шейдер фрагмента с помощью [**ID3D11DeviceContext1::PSSetShader**](https://msdn.microsoft.com/library/windows/desktop/ff476472).
-   Отправьте индексированные вершины через шейдеры и выведите результаты цвета в буфер однобуферной прорисовки с помощью [**ID3D11DeviceContext1::DrawIndexed**](https://msdn.microsoft.com/library/windows/desktop/ff476409).
-   Отобразите буфер однобуферной прорисовки с помощью [**IDXGISwapChain1::Present1**](https://msdn.microsoft.com/library/windows/desktop/hh446797).

Direct3D 11: Отрисовке определенного кадра для отображения

``` syntax
void RenderObject::Render()
{
  // ...

  // Only update shader resources that have changed since the last frame.
  m_d3dContext->UpdateSubresource(
    m_constantBuffer.Get(),
    0,
    NULL,
    &m_constantBufferData,
    0,
    0);

  // Set up the IA stage corresponding to the current draw operation.
  UINT stride = sizeof(VertexPositionColor);
  UINT offset = 0;
  m_d3dContext->IASetVertexBuffers(
    0,
    1,
    m_vertexBuffer.GetAddressOf(),
    &stride,
    &offset);

  m_d3dContext->IASetIndexBuffer(
    m_indexBuffer.Get(),
    DXGI_FORMAT_R16_UINT,
    0);

  m_d3dContext->IASetPrimitiveTopology(D3D11_PRIMITIVE_TOPOLOGY_TRIANGLELIST);
  m_d3dContext->IASetInputLayout(m_inputLayout.Get());

  // Set up the vertex shader corresponding to the current draw operation.
  m_d3dContext->VSSetShader(
    m_vertexShader.Get(),
    nullptr,
    0);

  m_d3dContext->VSSetConstantBuffers(
    0,
    1,
    m_constantBuffer.GetAddressOf());

  // Set up the pixel shader corresponding to the current draw operation.
  m_d3dContext->PSSetShader(
    m_pixelShader.Get(),
    nullptr,
    0);

  m_d3dContext->DrawIndexed(
    m_indexCount,
    0,
    0);

    // ...

  m_swapChainCoreWindow->Present1(1, 0, &parameters);
}

```

После вызова [**IDXGISwapChain1::Present1**](https://msdn.microsoft.com/library/windows/desktop/hh446797) кадр выводится на настроенный дисплей.

## <a name="previous-step"></a>Предыдущий шаг


[Перенос GLSL](port-the-glsl.md)

## <a name="remarks"></a>Замечания

Этот пример значительно снижает сложность настройки ресурсов устройства, особенно для приложений универсальной платформы Windows (UWP) с DirectX. Мы предлагаем вам просмотреть полный код шаблона, особенно те части, которые касаются настройки и управления окном и ресурсами устройства. Приложения UWP должны поддерживать события поворота, а также события приостановки и возобновления. Шаблон демонстрирует рекомендованный способ справиться с потерей интерфейса или изменением параметров дисплея.

## <a name="related-topics"></a>Статьи по теме


* [Практическое: порт простой модуль подготовки отчетов OpenGL ES 2.0 на Direct3D 11](port-a-simple-opengl-es-2-0-renderer-to-directx-11-1.md)
* [Перенос объектов шейдеров](port-the-shader-config.md)
* [Перенос GLSL](port-the-glsl.md)
* [Рисование объектов на экране](draw-to-the-screen.md)

 

 




