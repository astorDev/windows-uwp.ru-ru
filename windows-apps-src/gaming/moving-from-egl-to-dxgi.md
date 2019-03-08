---
title: Сравнение кода EGL с DXGI и Direct3D
description: Программный интерфейс DirectX для работы с графикой (DXGI) и несколько API Direct3D выполняют ту же роль, что и EGL. Этот раздел помогает разобраться в DXGI и Direct3D 11 с точки зрения EGL.
ms.assetid: 90f5ecf1-dd5d-fea3-bed8-57a228898d2a
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp, egl, dxgi, direct3d
ms.localizationpriority: medium
ms.openlocfilehash: 19c857ae5274be70d19a14d5bbf47adb595b5676
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57606369"
---
# <a name="compare-egl-code-to-dxgi-and-direct3d"></a>Сравнение кода EGL с DXGI и Direct3D




**Важные API**

-   [**ID3D11Device1**](https://msdn.microsoft.com/library/windows/desktop/hh404575)
-   [**ID3D11DeviceContext1**](https://msdn.microsoft.com/library/windows/desktop/hh404598)
-   [**CoreWindow**](https://msdn.microsoft.com/library/windows/apps/br208225)

Программный интерфейс DirectX для работы с графикой (DXGI) и несколько API Direct3D выполняют ту же роль, что и EGL. Этот раздел помогает разобраться в DXGI и Direct3D 11 с точки зрения EGL.

DXGI и Direct3D, подобно EGL, предоставляют методы для настройки графических ресурсов, получения контекста отрисовки, в котором будут рисовать шейдеры, и отображения результатов в окне. Но у DXGI и Direct3D есть целый ряд дополнительных возможностей, и они требуют дополнительных усилий для правильной настройки при переносе с EGL.

> **Примечание**    это руководство основывается на группе Khronos открытых спецификаций для EGL 1.4, см. здесь: [Интерфейс графических платформы машинный код Khronos (EGL версии 1.4 - 6 апреля 2011 г.) \[PDF\]](https://www.khronos.org/registry/egl/specs/eglspec.1.4.20110406.pdf). Различия в синтаксисе, касающиеся других платформ и языков разработки, не охвачены в этом руководстве.

 

## <a name="how-does-dxgi-and-direct3d-compare"></a>Как DXGI и Direct3D выглядят в сравнении?


Большим преимуществом EGL по сравнению с DXGI и Direct3D является относительная простота начала рисования на поверхности окна. Это вызвано тем, что OpenGL ES 2.0, а следовательно и EGL, является спецификацией, реализованной несколькими поставщиками платформ, тогда как DXGI и Direct3D являются единым эталоном, которому поставщики оборудования должны соответствовать. Это означает, что Майкрософт необходимо реализовать набор API, допускающий наиболее широкий спектр возможностей поставщиков, вместо того чтобы концентрироваться на подмножестве функций, предлагаемых определенным поставщиком или комбинировать команды настройки от определенных поставщиков в более простые API. С другой стороны, Direct3D предоставляет единый набор API, охватывающий очень широкий диапазон аппаратных графических платформ и функциональных уровней, а также предлагающий большую гибкость для разработчиков, имеющих опыт работы с платформой.

Подобно EGL, DXGI и Direct3D предоставляют API для следующих поведений:

-   Получение буфера кадров, чтение и запись в него (называется "цепочкой буферов" в DXGI).
-   Связь буфера кадров с окном пользовательского интерфейса.
-   Получение и настройка контекстов отрисовки, в которых следует рисовать. 
-   Выдача команд графическому конвейеру для конкретного контекста отрисовки.
-   Создание ресурсов шейдеров и управление ими, а также связывание их с контекстом отрисовки.
-   Отрисовка в конкретную однобуферную прорисовку (например, в случае текстур).
-   Обновление области отображения окна результатами отрисовки с помощью графических ресурсов.

Чтобы увидеть базовый процесс Direct3D для настройки конвейера графики, ознакомьтесь с шаблоном приложение DirectX 11 (универсальные Windows) в Microsoft Visual Studio 2015. Базовый класс отрисовки в нем предоставляет хорошую основу для создания графической инфраструктуры Direct3D 11 и настройки на ней базовых ресурсов, а также поддержки функций приложения универсальной платформы Windows (UWP), таких как поворот экрана.

У EGL имеется очень немного API по сравнению с Direct3D 11, и навигация в последнем может быть проблематична, если вы не знакомы со схемой именования и жаргоном конкретной платформы. Вот простой обзор, чтобы помочь вам сориентироваться.

Сперва рассмотрите сопоставление базового объекта EGL с интерфейсом Direct3D:

| Абстракция EGL | Аналогичное представление Direct3D                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
|-----------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **EGLDisplay**  | В Direct3D (для приложений UWP) дескриптор экрана получается через API [**Windows::UI::CoreWindow**](https://msdn.microsoft.com/library/windows/apps/br208225) (или интерфейс **ICoreWindowInterop**, предоставляющий HWND). Конфигурации адаптера и оборудования задаются с помощью интерфейсов СОМ [**IDXGIAdapter**](https://msdn.microsoft.com/library/windows/desktop/bb174523) и [**IDXGIDevice1**](https://msdn.microsoft.com/library/windows/desktop/hh404543) соответственно.                                                                                                                                                                                                                                                           |
| **EGLSurface**  | В Direct3D буферы и прочие ресурсы окон (видимые или находящиеся за экраном) создаются и настраиваются с помощью конкретных интерфейсов DXGI, включая [**IDXGIFactory2**](https://msdn.microsoft.com/library/windows/desktop/hh404556) (фабричная реализация шаблона, используемая для получения ресурсов DXGI, таких как [**IDXGISwapChain1**](https://msdn.microsoft.com/library/windows/desktop/hh404631) (буферы экрана)). [  **ID3D11Device1**](https://msdn.microsoft.com/library/windows/desktop/hh404575), представляющий графическое устройство и его ресурсы, получается с помощью [**D3D11Device::CreateDevice**](https://msdn.microsoft.com/library/windows/desktop/ff476082). Для однобуферных прорисовок используйте интерфейс [**ID3D11RenderTargetView**](https://msdn.microsoft.com/library/windows/desktop/ff476582). |
| **EGLContext**  | В Direct3D команды настраиваются и выдаются графическому конвейеру с помощью интерфейса [**ID3D11DeviceContext1**](https://msdn.microsoft.com/library/windows/desktop/hh404598).                                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
| **EGLConfig**   | В Direct3D 11 ресурсы графики, такие как буферы, текстуры, наборы элементов и шейдеры, создаются и настраиваются с помощью методов интерфейса [**ID3D11Device1**](https://msdn.microsoft.com/library/windows/desktop/hh404575).                                                                                                                                                                                                                                                                                                                                                                                                                                                      |

 

Теперь покажем простейший способ настройки простого отображения графики, настройки ресурсов и контекста в DXGI и Direct3D для приложения UWP.

1.  Получите дескриптор объекта [**CoreWindow**](https://msdn.microsoft.com/library/windows/apps/br208225) для основного потока пользовательского интерфейса приложения, вызвав метод [**CoreWindow::GetForCurrentThread**](https://msdn.microsoft.com/library/windows/apps/hh701589).
2.  Для приложений UWP получите цепочку буферов из [**IDXGIAdapter2**](https://msdn.microsoft.com/library/windows/desktop/hh404537) с помощью [**IDXGIFactory2::CreateSwapChainForCoreWindow**](https://msdn.microsoft.com/library/windows/desktop/hh404559) и передайте ей ссылку [**CoreWindow**](https://msdn.microsoft.com/library/windows/apps/br208225), полученную на шаге 1. Будет возвращен экземпляр [**IDXGISwapChain1**](https://msdn.microsoft.com/library/windows/desktop/hh404631). Задайте для него область вашего объекта обработчика и его потока обработки.
3.  Получите экземпляры [**ID3D11Device1**](https://msdn.microsoft.com/library/windows/desktop/hh404575) и [**ID3D11DeviceContext1**](https://msdn.microsoft.com/library/windows/desktop/hh404598), вызвав метод [**D3D11Device::CreateDevice**](https://msdn.microsoft.com/library/windows/desktop/ff476082). Также задайте для них область вашего объекта обработчика.
4.  Создайте шейдеры, текстуры и другие ресурсы с помощью методов в объекте [**ID3D11Device1**](https://msdn.microsoft.com/library/windows/desktop/hh404575) вашего обработчика.
5.  Определите буферы, запустите шейдеры и управляйте этапами конвейера с помощью методов в объекте [**ID3D11DeviceContext1**](https://msdn.microsoft.com/library/windows/desktop/hh404598) вашего обработчика.
6.  После выполнения конвейера и перехода кадра в задний буфер представьте его на экране с помощью метода [**IDXGISwapChain1::Present1**](https://msdn.microsoft.com/library/windows/desktop/hh446797).

Этот процесс подробно описан в разделе [Начало работы с графикой DirectX](https://msdn.microsoft.com/library/windows/desktop/hh309467). Далее в статье описываются многие распространенные действия по базовой настройке графического конвейера и по управлению конвейером.
> **Примечание**    приложениях для настольных систем Windows имеют разные API для получения цепочку обмена Direct3D, такие как [ **D3D11Device::CreateDeviceAndSwapChain**](https://msdn.microsoft.com/library/windows/desktop/ff476083)и не используйте [ **CoreWindow** ](https://msdn.microsoft.com/library/windows/apps/br208225) объекта.

 

## <a name="obtaining-a-window-for-display"></a>Получение окна для отображения.


В данном примере eglGetDisplay передается HWND для ресурса окна, уникального для платформы Microsoft Windows. Другие платформы, такие как Apple iOS (Cocoa) и Google Android, используют другие дескрипторы и ссылки на ресурсы окон, а также могут иметь совершенно иной синтаксис вызова. После получения отображения мы инициализируем его, задаем предпочтительную конфигурацию и создаем поверхность с задним буфером, на которой можно рисовать.

Получение отображения и его настройка с помощью EGL.

``` syntax
// Obtain an EGL display object.
EGLDisplay display = eglGetDisplay(GetDC(hWnd));
if (display == EGL_NO_DISPLAY)
{
  return EGL_FALSE;
}

// Initialize the display
if (!eglInitialize(display, &majorVersion, &minorVersion))
{
  return EGL_FALSE;
}

// Obtain the display configs
if (!eglGetConfigs(display, NULL, 0, &numConfigs))
{
  return EGL_FALSE;
}

// Choose the display config
if (!eglChooseConfig(display, attribList, &config, 1, &numConfigs))
{
  return EGL_FALSE;
}

// Create a surface
surface = eglCreateWindowSurface(display, config, (EGLNativeWindowType)hWnd, NULL);
if (surface == EGL_NO_SURFACE)
{
  return EGL_FALSE;
}
```

В Direct3D главное окно приложения UWP представлено объектом [**CoreWindow**](https://msdn.microsoft.com/library/windows/apps/br208225), который можно получить из объекта приложения, вызвав [**CoreWindow::GetForCurrentThread**](https://msdn.microsoft.com/library/windows/apps/hh701589) в рамках процесса инициализации поставщика представлений, создаваемого для Direct3D. (При использовании взаимодействия Direct3D XAML, использовать поставщик платформы XAML представления.) Подробно описан процесс создания поставщиков представления Direct3D [как настроить приложение, позволяющее просмотреть](https://msdn.microsoft.com/library/windows/apps/hh465077).

Получение CoreWindow для Direct3D.

``` syntax
CoreWindow::GetForCurrentThread();
```

После получения ссылки [**CoreWindow**](https://msdn.microsoft.com/library/windows/apps/br208225) окно необходимо активировать, что выполняет метод **Run** основного объекта и начинает обработку события окна. После этого создайте [ **ID3D11Device1** ](https://msdn.microsoft.com/library/windows/desktop/hh404575) и [ **ID3D11DeviceContext1**](https://msdn.microsoft.com/library/windows/desktop/hh404598)и используют их для получения базового [ **IDXGIDevice1** ](https://msdn.microsoft.com/library/windows/desktop/ff471331) и [ **IDXGIAdapter** ](https://msdn.microsoft.com/library/windows/desktop/bb174523) , можно получить [ **IDXGIFactory2** ](https://msdn.microsoft.com/library/windows/desktop/hh404556) для создания ресурса цепочке замены на основе вашего [ **DXGI\_ЗАМЕНЫ\_ЦЕПОЧКИ\_DESC1** ](https://msdn.microsoft.com/library/windows/desktop/hh404528) конфигурации.

Создание и настройка цепочки буферов DXGI на CoreWindow для Direct3D.

``` syntax
// Called when the CoreWindow object is created (or re-created).
void SimpleDirect3DApp::SetWindow(CoreWindow^ window)
{
  // Register event handlers with the CoreWindow object.
  // ...

  // Obtain your ID3D11Device1 and ID3D11DeviceContext1 objects
  // In this example, m_d3dDevice contains the scoped ID3D11Device1 object
  // ...

  ComPtr<IDXGIDevice1>  dxgiDevice;
  // Get the underlying DXGI device of the Direct3D device.
  m_d3dDevice.As(&dxgiDevice);

  ComPtr<IDXGIAdapter> dxgiAdapter;
  dxgiDevice->GetAdapter(&dxgiAdapter);

  ComPtr<IDXGIFactory2> dxgiFactory;
  dxgiAdapter->GetParent(
    __uuidof(IDXGIFactory2), 
    &dxgiFactory);

  DXGI_SWAP_CHAIN_DESC1 swapChainDesc = {0};
  swapChainDesc.Width = static_cast<UINT>(m_d3dRenderTargetSize.Width); // Match the size of the window.
  swapChainDesc.Height = static_cast<UINT>(m_d3dRenderTargetSize.Height);
  swapChainDesc.Format = DXGI_FORMAT_B8G8R8A8_UNORM; // This is the most common swap chain format.
  swapChainDesc.Stereo = false;
  swapChainDesc.SampleDesc.Count = 1; // Don't use multi-sampling.
  swapChainDesc.SampleDesc.Quality = 0;
  swapChainDesc.BufferUsage = DXGI_USAGE_RENDER_TARGET_OUTPUT;
  swapChainDesc.BufferCount = 2; // Use double-buffering to minimize latency.
  swapChainDesc.SwapEffect = DXGI_SWAP_EFFECT_FLIP_SEQUENTIAL; // All UWP apps must use this SwapEffect.
  swapChainDesc.Flags = 0;

  // ...

  Windows::UI::Core::CoreWindow^ window = m_window.Get();
  dxgiFactory->CreateSwapChainForCoreWindow(
    m_d3dDevice.Get(),
    reinterpret_cast<IUnknown*>(window),
    &swapChainDesc,
    nullptr, // Allow on all displays.
    &m_swapChainCoreWindow);
}
```

Вызовите метод [**IDXGISwapChain1::Present1**](https://msdn.microsoft.com/library/windows/desktop/hh446797) после подготовки кадра, чтобы отобразить его.

Обратите внимание, что в Direct3D 11 нет абстракций, идентичных EGLSurface. (Существует [ **IDXGISurface1**](https://msdn.microsoft.com/library/windows/desktop/ff471343), но он используется по-разному.) Концептуальная приближением является [ **ID3D11RenderTargetView** ](https://msdn.microsoft.com/library/windows/desktop/ff476582) объекта, что мы используем для назначения текстуры ([**ID3D11Texture2D** ](https://msdn.microsoft.com/library/windows/desktop/ff476635)), наш конвейер шейдера рисующий в задним буфером.

Настройка заднего буфера для цепочки буферов в Direct3D 11

``` syntax
ComPtr<ID3D11RenderTargetView>    m_d3dRenderTargetViewWin; // scoped to renderer object

// ...

ComPtr<ID3D11Texture2D> backBuffer2;
    
m_swapChainCoreWindow->GetBuffer(0, IID_PPV_ARGS(&backBuffer2));

m_d3dDevice->CreateRenderTargetView(
  backBuffer2.Get(),
  nullptr,
    &m_d3dRenderTargetViewWin);
```

Этот код рекомендуется вызывать каждый раз, когда окно создается или меняет размер. В ходе отрисовки установите представление однобуферной прорисовки с помощью [**ID3D11DeviceContext1::OMSetRenderTargets**](https://msdn.microsoft.com/library/windows/desktop/ff476464), прежде чем создавать все прочие подресурсы, такие как буферы вершин или шейдеры.

``` syntax
// Set the render target for the draw operation.
m_d3dContext->OMSetRenderTargets(
        1,
        d3dRenderTargetView.GetAddressOf(),
        nullptr);
```

## <a name="creating-a-rendering-context"></a>Создание контекста отрисовки


В EGL 1.4 "экран" представляет набор ресурсов окна. Как правило, мы настраиваем "поверхность" для отображения, предоставляя набор атрибутов объекту отображения и получая в ответ поверхность. Контекст для отображения содержимого поверхности строится путем создания контекста и привязки его к поверхности и отображению.

Поток вызовов обычно выглядит примерно так:

-   Вызовите eglGetDisplay с дескриптором для отображения или ресурса окна и получите объект отображения. 
-   Инициализируйте отображение с помощью eglInitialize.
-   Получите доступные конфигурации отображения и выберите одну из них с помощью eglGetConfigs и eglChooseConfig.
-   Создайте поверхность окна с помощью eglCreateWindowSurface.
-   Создайте контекст отображения для рисования с помощью eglCreateContext.
-   Привяжите контекст отображения к отображению и поверхности с помощью eglMakeCurrent.

В предыдущем разделе мы создали EGLDisplay и EGLSurface, теперь мы используем EGLDisplay для создания контекста и связи этого контекста с отображением, а настроенный EGLSurface — для параметризации выходных данных.

Получение контекста отрисовки с помощью EGL 1.4

```cpp
// Configure your EGLDisplay and obtain an EGLSurface here ...
// ...

// Create a drawing context from the EGLDisplay
context = eglCreateContext(display, config, EGL_NO_CONTEXT, contextAttribs);
if (context == EGL_NO_CONTEXT)
{
  return EGL_FALSE;
}   
   
// Make the context current
if (!eglMakeCurrent(display, surface, surface, context))
{
  return EGL_FALSE;
}
```

Контекст отрисовки в Direct3D 11 представлен объектом [**ID3D11Device1**](https://msdn.microsoft.com/library/windows/desktop/hh404575), который представляет адаптер и позволяет создавать ресурсы Direct3D, такие как буферы и шейдеры, а также объектом [**ID3D11DeviceContext1**](https://msdn.microsoft.com/library/windows/desktop/hh404598), который позволяет управлять графическим конвейером и исполнять шейдеры.

Не забывайте про функциональные уровни Direct3D. Они используются для поддержки старых аппаратных платформ Direct3D, от DirectX 9.1 до DirectX 11. Многие платформы, использующие маломощное графическое оборудование, такие как планшеты, имеют доступ только к возможностям DirectX 9.1, а старое поддерживаемое графическое оборудование может требовать версий от 9.1 до 11.

Создание контекста отрисовки с помощью DXGI и Direct3D

```cpp

// ... 

UINT creationFlags = D3D11_CREATE_DEVICE_BGRA_SUPPORT;
ComPtr<IDXGIDevice> dxgiDevice;

D3D_FEATURE_LEVEL featureLevels[] = 
{
        D3D_FEATURE_LEVEL_11_1,
        D3D_FEATURE_LEVEL_11_0,
        D3D_FEATURE_LEVEL_10_1,
        D3D_FEATURE_LEVEL_10_0,
        D3D_FEATURE_LEVEL_9_3,
        D3D_FEATURE_LEVEL_9_2,
        D3D_FEATURE_LEVEL_9_1
};

// Create the Direct3D 11 API device object and a corresponding context.
ComPtr<ID3D11Device> device;
ComPtr<ID3D11DeviceContext> d3dContext;

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
  &d3dContext // Returns the device immediate context.
);
```

## <a name="drawing-into-a-texture-or-pixmap-resource"></a>Рисование в текстуре или рисунке pixmap


Чтобы рисовать в текстуре с помощью OpenGL ES 2.0, настройте буфер пикселей, или PBuffer. После того как вы успешно настроили и создали EGLSurface для него, ему можно предоставить контекст отрисовки и исполнить конвейер шейдера для прорисовки текстуры.

Рисование в буфере пикселей с помощью OpenGL ES 2.0

``` syntax
// Create a pixel buffer surface to draw into
EGLConfig pBufConfig;
EGLint totalpBufAttrs;

const EGLint pBufConfigAttrs[] =
{
    // Configure the pBuffer here...
};
 
eglChooseConfig(eglDsplay, pBufConfigAttrs, &pBufConfig, 1, &totalpBufAttrs);
EGLSurface pBuffer = eglCreatePbufferSurface(eglDisplay, pBufConfig, EGL_TEXTURE_RGBA); 
```

В Direct3D 11 мы создаем ресурс [**ID3D11Texture2D**](https://msdn.microsoft.com/library/windows/desktop/ff476635) и делаем его однобуферной прорисовкой. Настройте целевой объект отрисовки с помощью [ **D3D11\_ВИЗУАЛИЗАЦИИ\_ЦЕЛЕВОЙ\_ПРЕДСТАВЛЕНИЕ\_DESC**](https://msdn.microsoft.com/library/windows/desktop/ff476201). При вызове [ **ID3D11DeviceContext::Draw** ](https://msdn.microsoft.com/library/windows/desktop/ff476407) метод (или аналогичные Draw\* операции на контекст устройства) с помощью данного целевого объекта отрисовки, результаты отображаются в текстуре.

Рисование в текстуре с помощью Direct3D 11

``` syntax
ComPtr<ID3D11Texture2D> renderTarget1;

D3D11_RENDER_TARGET_VIEW_DESC renderTargetDesc = {0};
// Configure renderTargetDesc here ...

m_d3dDevice->CreateRenderTargetView(
  renderTarget1.Get(),
  nullptr,
  &m_d3dRenderTargetViewWin);

// Later, in your render loop...

// Set the render target for the draw operation.
m_d3dContext->OMSetRenderTargets(
        1,
        d3dRenderTargetView.GetAddressOf(),
        nullptr);
```

Эту текстуру можно передать шейдеру, если он связан с [**ID3D11ShaderResourceView**](https://msdn.microsoft.com/library/windows/desktop/ff476628).

## <a name="drawing-to-the-screen"></a>Рисование на экране


После использования EGLContext для настройки буферов и обновления данных можно выполнить привязанные к нему шейдеры и прорисовать результаты в задний буфер с помощью glDrawElements. Задний буфер отображается посредством вызова eglSwapBuffers.

Open GL ES 2.0: Рисование на экране.

``` syntax
glDrawElements(GL_TRIANGLES, renderer->numIndices, GL_UNSIGNED_INT, 0);

eglSwapBuffers(drawContext->eglDisplay, drawContext->eglSurface);
```

В Direct3D 11 мы настраиваем буферы и привязываем шейдеры с помощью [**IDXGISwapChain::Present1**](https://msdn.microsoft.com/library/windows/desktop/hh446797). Затем при вызове одного из [ **ID3D11DeviceContext1::Draw** ](https://msdn.microsoft.com/library/windows/desktop/ff476407) \* методы для запуска преобразователи текстур и отобразите результаты целевой объект отрисовки, настроить задним буфером цепочки буферов. После этого мы просто представляем задний буфер для отображения, вызывая **IDXGISwapChain::Present1**.

Direct3D 11: Рисование на экране.

``` syntax

m_d3dContext->DrawIndexed(
        m_indexCount,
        0,
        0);

// ...

m_swapChainCoreWindow->Present1(1, 0, &parameters);
```

## <a name="releasing-graphics-resources"></a>Высвобождение графических ресурсов


В EGL мы высвобождаем графические ресурсы, передавая EGLDisplay к eglTerminate.

Прерывание отображения с помощью EGL 1.4

```cpp
EGLBoolean eglTerminate(eglDisplay);
```

В приложении UWP CoreWindow можно закрыть с помощью [**CoreWindow::Close**](https://msdn.microsoft.com/library/windows/apps/br208260), хотя это можно использовать только для вспомогательных окон пользовательского интерфейса. Основной поток пользовательского интерфейса и связанное с ним CoreWindow закрыть нельзя — срок их действия определяется операционной системой. Но при закрытии вспомогательного CoreWindow создается событие [**CoreWindow::Closed**](https://msdn.microsoft.com/library/windows/apps/br208261).

## <a name="api-reference-mapping-for-egl-to-direct3d-11"></a>Справка по сопоставлению API EGL и Direct3D 11


| API EGL                          | Аналогичный API или поведение Direct3D 11                                                                                                                                                                                                                                                                                                                                                                                                                                                             |
|----------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| eglBindAPI                       | Н/д.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
| eglBindTexImage                  | Вызовите [**ID3D11Device::CreateTexture2D**](https://msdn.microsoft.com/library/windows/desktop/ff476521) для настройки двухмерной текстуры.                                                                                                                                                                                                                                                                                                                                                                                          |
| eglChooseConfig                  | Direct3D не предоставляет набор конфигураций буфера кадров по умолчанию. Конфигурация цепочки буферов                                                                                                                                                                                                                                                                                                                                                                                           |
| eglCopyBuffers                   | Для копирования данных буфера вызовите [**ID3D11DeviceContext::CopyStructureCount**](https://msdn.microsoft.com/library/windows/desktop/ff476393). Для копирования ресурса вызовите [**ID3DDeviceCOntext::CopyResource**](https://msdn.microsoft.com/library/windows/desktop/ff476392).                                                                                                                                                                                                                                                      |
| eglCreateContext                 | Создайте контекст устройства Direct3D, вызвав [**D3D11CreateDevice**](https://msdn.microsoft.com/library/windows/desktop/ff476082), который возвращает как дескриптор для устройства Direct3D, так и немедленный контекст Direct3D по умолчанию (объект [**ID3D11DeviceContext1**](https://msdn.microsoft.com/library/windows/desktop/hh404598)). Также можно создать отложенный контекст Direct3D, вызвав [**ID3D11Device2::CreateDeferredContext**](https://msdn.microsoft.com/library/windows/desktop/dn280495) на возвращенном объекте [**ID3D11Device1**](https://msdn.microsoft.com/library/windows/desktop/hh404575). |
| eglCreatePbufferFromClientBuffer | Все буферы читаются и записываются как подресурс Direct3D, такой как [**ID3D11Texture2D**](https://msdn.microsoft.com/library/windows/desktop/ff476635). Выполняйте копирование между совместимыми типами подресурсов с помощью таких методов, как [**ID3D11DeviceContext1:CopyResource**](https://msdn.microsoft.com/library/windows/desktop/ff476392).                                                                                                                                                                                                     |
| eglCreatePbufferSurface          | Чтобы создать устройство Direct3D без цепочки буферов, вызовите статический метод [**D3D11CreateDevice**](https://msdn.microsoft.com/library/windows/desktop/ff476082). Для представления однобуферной прорисовки Direct3D вызовите [**ID3D11Device::CreateRenderTargetView**](https://msdn.microsoft.com/library/windows/desktop/ff476517).                                                                                                                                                                                                                               |
| eglCreatePixmapSurface           | Чтобы создать устройство Direct3D без цепочки буферов, вызовите статический метод [**D3D11CreateDevice**](https://msdn.microsoft.com/library/windows/desktop/ff476082). Для представления однобуферной прорисовки Direct3D вызовите [**ID3D11Device::CreateRenderTargetView**](https://msdn.microsoft.com/library/windows/desktop/ff476517).                                                                                                                                                                                                                               |
| eglCreateWindowSurface           | Получите [**IDXGISwapChain1**](https://msdn.microsoft.com/library/windows/desktop/hh404631) (для буферов отображения) и [**ID3D11Device1**](https://msdn.microsoft.com/library/windows/desktop/hh404575) (виртуальный интерфейс для графического устройства и его ресурсов). Используйте **ID3D11Device1** для определения [**ID3D11RenderTargetView**](https://msdn.microsoft.com/library/windows/desktop/ff476582), который можно использовать для создания буфера кадров, предоставляемого **IDXGISwapChain1**.                                                                                         |
| eglDestroyContext                | Н/д. Используйте [**ID3D11DeviceContext::DiscardView1**](https://msdn.microsoft.com/library/windows/desktop/jj247573) для избавления от представления однобуферной прорисовки. Чтобы закрыть родительский [**ID3D11DeviceContext1**](https://msdn.microsoft.com/library/windows/desktop/hh404598), установите для экземпляра значение «null» и подождите, пока платформа не освободит ресурсы. Ликвидировать контекст устройства напрямую нельзя.                                                                                                                                                |
| eglDestroySurface                | Н/д. Графические ресурсы очищаются, когда CoreWindow приложения UWP закрывается платформой.                                                                                                                                                                                                                                                                                                                                                                                                 |
| eglGetCurrentDisplay             | Вызовите [**CoreWindow::GetForCurrentThread**](https://msdn.microsoft.com/library/windows/apps/hh701589), чтобы получить ссылку на текущее основное окно приложения.                                                                                                                                                                                                                                                                                                                                                         |
| eglGetCurrentSurface             | Это текущий [**ID3D11RenderTargetView**](https://msdn.microsoft.com/library/windows/desktop/ff476582). Обычно его область устанавливается на ваш объект обработчика.                                                                                                                                                                                                                                                                                                                                                         |
| eglGetError                      | Ошибки получаются как результаты HRESULT, возвращаемые большинством методов интерфейсов DirectX. Если метод не возвращает HRESULT, вызовите [**GetLastError**](https://msdn.microsoft.com/library/windows/desktop/ms679360). Чтобы преобразовать Системная ошибка в значение HRESULT, используйте [**HRESULT\_FROM\_WIN32**](https://msdn.microsoft.com/library/windows/desktop/ms680746) макрос.                                                                                                                                                                                                  |
| eglInitialize                    | Вызовите [**CoreWindow::GetForCurrentThread**](https://msdn.microsoft.com/library/windows/apps/hh701589), чтобы получить ссылку на текущее основное окно приложения.                                                                                                                                                                                                                                                                                                                                                         |
| eglMakeCurrent                   | Установите однобуферную прорисовку для рисования в текущем контексте с помощью [**ID3D11DeviceContext1::OMSetRenderTargets**](https://msdn.microsoft.com/library/windows/desktop/ff476464).                                                                                                                                                                                                                                                                                                                                  |
| eglQueryContext                  | Н/д. Однако однобуферные прорисовки, а также некоторые данные конфигурации можно получать из экземпляра [**ID3D11Device1**](https://msdn.microsoft.com/library/windows/desktop/hh404575). (Список доступных методов см. по ссылке.)                                                                                                                                                                                                                                                                                           |
| eglQuerySurface                  | Н/д. Однако данные об окнах просмотра и текущем графическом оборудовании можно получать от методов в экземпляре [**ID3D11Device1**](https://msdn.microsoft.com/library/windows/desktop/hh404575). (Список доступных методов см. по ссылке.)                                                                                                                                                                                                                                                                               |
| eglReleaseTexImage               | Н/д.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
| eglReleaseThread                 | Общие сведения о многопоточности GPU см. в разделе [Многопоточность](https://msdn.microsoft.com/library/windows/desktop/ff476891).                                                                                                                                                                                                                                                                                                                                                                              |
| eglSurfaceAttrib                 | Используйте [ **D3D11\_ВИЗУАЛИЗАЦИИ\_ЦЕЛЕВОЙ\_ПРЕДСТАВЛЕНИЕ\_DESC** ](https://msdn.microsoft.com/library/windows/desktop/ff476201) для настройки представления целевого объекта прорисовки Direct3D,                                                                                                                                                                                                                                                                                                                                                               |
| eglSwapBuffers                   | Используйте [**IDXGISwapChain1::Present1**](https://msdn.microsoft.com/library/windows/desktop/hh446797).                                                                                                                                                                                                                                                                                                                                                                                                                     |
| eglSwapInterval                  | См. [**IDXGISwapChain1**](https://msdn.microsoft.com/library/windows/desktop/hh404631).                                                                                                                                                                                                                                                                                                                                                                                                                                        |
| eglTerminate                     | CoreWindow, используемое для отображения выходных данных графического конвейера, управляется операционной системой.                                                                                                                                                                                                                                                                                                                                                                                          |
| eglWaitClient                    | Для общих поверхностей используйте IDXGIKeyedMutex. Общие сведения о многопоточности GPU см. в разделе [Многопоточность](https://msdn.microsoft.com/library/windows/desktop/ff476891).                                                                                                                                                                                                                                                                                                                                    |
| eglWaitGL                        | Для общих поверхностей используйте IDXGIKeyedMutex. Общие сведения о многопоточности GPU см. в разделе [Многопоточность](https://msdn.microsoft.com/library/windows/desktop/ff476891).                                                                                                                                                                                                                                                                                                                                    |
| eglWaitNative                    | Для общих поверхностей используйте IDXGIKeyedMutex. Общие сведения о многопоточности GPU см. в разделе [Многопоточность](https://msdn.microsoft.com/library/windows/desktop/ff476891).                                                                                                                                                                                                                                                                                                                                    |

 

 

 




