---
title: Расширение примера игры
description: Узнайте, как реализовать наложение XAML в игре UWP на базе DirectX.
keywords: DirectX, XAML
ms.date: 10/24/2017
ms.topic: article
ms.localizationpriority: medium
ms.openlocfilehash: 06b52e5b6fdba1db83c941e770cd49360085accf
ms.sourcegitcommit: 20969781aca50738792631f4b68326f9171a3980
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/26/2020
ms.locfileid: "85409553"
---
# <a name="extend-the-sample-game"></a>Расширение примера игры

> [!NOTE]
> Эта статья является частью серии руководств по [созданию простой универсальная платформа Windows (UWP) с помощью DirectX](tutorial--create-your-first-uwp-directx-game.md) . В разделе этой ссылки задается контекст для ряда.

Итак, вы уже знакомы с основными компонентами трехмерной игры универсальной платформы Windows (UWP) на базе DirectX. Можно настроить платформу для игры, включая конвейер представления и подготовки к просмотру, и реализовать базовый цикл игры. Вы также можете создать базовое наложение пользовательского интерфейса, добавить звуки и реализовать элементы управления. Вы практически готовы к созданию своей собственной игры, но если вам нужна дополнительная информация и помощь, воспользуйтесь ресурсами ниже.

-   [Графика и игры DirectX](/windows/desktop/directx)
-   [Обзор Direct3D 11](/windows/desktop/direct3d11/dx-graphics-overviews)
-   [Справочник по Direct3D 11](/windows/desktop/direct3d11/d3d11-graphics-reference)

## <a name="using-xaml-for-the-overlay"></a>Использование XAML для создания наложения

Одна из альтернатив, которую мы не обсуждали в глубину, — использование XAML вместо [Direct2D](/windows/desktop/Direct2D/direct2d-portal) для наложения. С точки зрения рисования элементов пользовательского интерфейса XAML обеспечивает ряд преимуществ по сравнению с Direct2D. Главное преимущество заключается в том, что с помощью XAML проще придать вашей игре на базе DirectX внешний вид и удобство использования, характерные для Windows 10. Поскольку большое число общих элементов, стилей и функций, присущих приложениям UWP, интегрировано в модель XAML, задача разработчиков игр значительно облегчается. Если вы разрабатываете для своей игры сложный пользовательский интерфейс, советуем вам использовать XAML вместо Direct2D.

Используя XAML, можно сделать игру с интерфейсом, похожим на интерфейс на базе Direct2D, который мы создали раньше.

### <a name="xaml"></a>XAML
![Наложение XAML](./images/simple-dx-game-extend-xaml.PNG)

### <a name="direct2d"></a>Direct2D
![Наложение D2D](./images/simple-dx-game-extend-d2d.PNG)

Несмотря на схожий конечный результат, между реализацией интерфейсов на базе Direct2D и XAML существует ряд различий.

Функция | XAML| Direct2D
:----------|:----------- | :-----------
Определение наложения | Определяется в XAML-файле (`\*.xaml`). Как только вы разберетесь в XAML, создавать и конфигурировать сложные наложения будет проще, чем при использовании Direct2D.| Определяется в виде коллекции примитивов Direct2D и строк [DirectWrite](/windows/desktop/DirectWrite/direct-write-portal), вручную помещаемых и записываемых в целевой буфер Direct2D. 
Элементы пользовательского интерфейса | Элементы пользовательского интерфейса на языке XAML происходят от стандартных элементов, входящих в состав API среды выполнения Windows на языке XAML, включая [**Windows::UI::Xaml**](/uwp/api/Windows.UI.Xaml) и [**Windows::UI::Xaml::Controls**](/uwp/api/Windows.UI.Xaml.Controls). Код, обрабатывающий поведение элементов пользовательского интерфейса на языке XAML, определяется в файле кода программной части Main.xaml.cpp. | Простые фигуры можно рисовать как прямоугольники и эллипсы.
Изменение размеров окон | События изменения размеров и состояния представления обрабатываются естественным образом, и наложение трансформируется в соответствии с ними | Необходимо вручную указывать, как будут перерисовываться компоненты наложения.

Другое существенное различие связано с [цепочкой буферов](/windows/uwp/graphics-concepts/swap-chains). Цепочку буферов не нужно присоединять к объекту [**Windows::UI::Core::CoreWindow**](/uwp/api/windows.ui.core.corewindow). Вместо этого приложение DirectX, в котором используется XAML, соединяется с цепочкой буферов при создании нового объекта [**SwapChainPanel**](/uwp/api/windows.ui.xaml.controls.swapchainpanel). 

В следующем фрагменте кода показано, как объявить разметку XAML для **SwapChainPanel** в файле [**DirectXPage.xaml**](https://github.com/Microsoft/Windows-universal-samples/blob/6370138b150ca8a34ff86de376ab6408c5587f5d/Samples/Simple3DGameXaml/cpp/DirectXPage.xaml).
```xml
<Page
    x:Class="Simple3DGameXaml.DirectXPage"
    xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
    xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
    xmlns:local="using:Simple3DGameXaml"
    xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
    xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
    mc:Ignorable="d">
    <SwapChainPanel x:Name="DXSwapChainPanel">

    <!-- ... XAML user controls and elements -->

    </SwapChainPanel>
</Page>
```

Объект **SwapChainPanel** задается как свойство [**Content**](/uwp/api/Windows.UI.Xaml.Window.Content) объекта текущего окна, создаваемого [при запуске](https://github.com/Microsoft/Windows-universal-samples/blob/6370138b150ca8a34ff86de376ab6408c5587f5d/Samples/Simple3DGameXaml/cpp/App.xaml.cpp#L45-L51) singleton-объектом приложения.

```cpp
void App::OnLaunched(_In_ LaunchActivatedEventArgs^ /* args */)
{
    m_mainPage = ref new DirectXPage();

    Window::Current->Content = m_mainPage;
    // Bring the application to the foreground so that it's visible
    Window::Current->Activate();
}
```

Чтобы привязать настроенную цепочку буферов к экземпляру [**SwapChainPanel**](/uwp/api/Windows.UI.Xaml.Controls.SwapChainPanel), определенному разметкой XAML, необходимо получить указатель на базовую реализацию интерфейса [**ISwapChainPanelNative**](/windows/desktop/api/windows.ui.xaml.media.dxinterop/nn-windows-ui-xaml-media-dxinterop-iswapchainpanelnative) и вызвать для него метод [**ISwapChainPanelNative::SetSwapChain**](/windows/desktop/api/windows.ui.xaml.media.dxinterop/nf-windows-ui-xaml-media-dxinterop-iswapchainpanelnative-setswapchain), передав ему настроенную цепочку буферов. 

Следующий фрагмент кода из примера [**DX::DeviceResources::CreateWindowSizeDependentResources**](https://github.com/Microsoft/Windows-universal-samples/blob/6370138b150ca8a34ff86de376ab6408c5587f5d/Samples/Simple3DGameXaml/cpp/Common/DeviceResources.cpp#L218-L521) демонстрирует, как это делается для взаимодействия DirectX/XAML:

```cpp
        ComPtr<IDXGIDevice3> dxgiDevice;
        DX::ThrowIfFailed(
            m_d3dDevice.As(&dxgiDevice)
            );

        ComPtr<IDXGIAdapter> dxgiAdapter;
        DX::ThrowIfFailed(
            dxgiDevice->GetAdapter(&dxgiAdapter)
            );

        ComPtr<IDXGIFactory2> dxgiFactory;
        DX::ThrowIfFailed(
            dxgiAdapter->GetParent(IID_PPV_ARGS(&dxgiFactory))
            );

        // When using XAML interop, the swap chain must be created for composition.
        DX::ThrowIfFailed(
            dxgiFactory->CreateSwapChainForComposition(
                m_d3dDevice.Get(),
                &swapChainDesc,
                nullptr,
                &m_swapChain
                )
            );

        // Associate swap chain with SwapChainPanel
        // UI changes will need to be dispatched back to the UI thread
        m_swapChainPanel->Dispatcher->RunAsync(CoreDispatcherPriority::High, ref new DispatchedHandler([=]()
        {
            // Get backing native interface for SwapChainPanel
            ComPtr<ISwapChainPanelNative> panelNative;
            DX::ThrowIfFailed(
                reinterpret_cast<IUnknown*>(m_swapChainPanel)->QueryInterface(IID_PPV_ARGS(&panelNative))
                );
            DX::ThrowIfFailed(
                panelNative->SetSwapChain(m_swapChain.Get())
                );
        }, CallbackContext::Any));

        // Ensure that DXGI does not queue more than one frame at a time. This both reduces latency and
        // ensures that the application will only render after each VSync, minimizing power consumption.
        DX::ThrowIfFailed(
            dxgiDevice->SetMaximumFrameLatency(1)
            );
    }
```

Подробнее об этой процедуре см. в статье [Межпрограммное взаимодействие DirectX и XAML](directx-and-xaml-interop.md).

## <a name="sample"></a>Пример

Чтобы загрузить версию этой игры, которая использует XAML для оверлея, перейдите к [примеру игры Direct3D с образцом (XAML)](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/Simple3DGameXaml).

В отличие от версии примера игры, обсуждаемой в остальных разделах, версия XAML определяет свою платформу в файлах [app. XAML. cpp](https://github.com/Microsoft/Windows-universal-samples/blob/6370138b150ca8a34ff86de376ab6408c5587f5d/Samples/Simple3DGameXaml/cpp/App.xaml.cpp) и [DirectXPage. XAML. cpp](https://github.com/Microsoft/Windows-universal-samples/blob/6370138b150ca8a34ff86de376ab6408c5587f5d/Samples/Simple3DGameXaml/cpp/DirectXPage.xaml.cpp) вместо [app. cpp](https://github.com/Microsoft/Windows-universal-samples/blob/6370138b150ca8a34ff86de376ab6408c5587f5d/Samples/Simple3DGameDX/cpp/App.cpp) и [гамеинфуверлай. cpp](https://github.com/Microsoft/Windows-universal-samples/blob/6370138b150ca8a34ff86de376ab6408c5587f5d/Samples/Simple3DGameDX/cpp/GameInfoOverlay.cpp)соответственно.
