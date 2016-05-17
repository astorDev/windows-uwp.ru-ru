---
author: mtoepke
title: Межпрограммное взаимодействие DirectX и XAML
description: Вы можете использовать одновременно и XAML, и Microsoft DirectX в своей игре универсальной платформы Windows (UWP).
ms.assetid: 0fb2819a-61ed-129d-6564-0b67debf5c6b
---

# Межпрограммное взаимодействие DirectX и XAML


\[ Обновлено для приложений UWP в Windows 10. Статьи о Windows 8.x см. в [архиве](http://go.microsoft.com/fwlink/p/?linkid=619132) \]

Вы можете использовать одновременно и XAML, и Microsoft DirectX в своей игре универсальной платформы Windows (UWP). Сочетание XAML и DirectX позволяет создавать гибкие структуры пользовательского интерфейса, взаимодействующие с содержимым, обрабатываемым DirectX, что особенно удобно для приложений с интенсивным использованием графики. В этом разделе описывается структура приложения UWP, использующего DirectX, и вводятся важные типы, которые применяются при сборке приложения UWP для обеспечения работы с DirectX.

> **Примечание.** API DirectX не определены как типы среды выполнения Windows, поэтому для разработки компонентов приложений UWP на XAML, взаимодействующих с DirectX, обычно используются расширения компонентов Visual C++ (C++/CX). Кроме того, вы можете создавать приложения UWP, использующие DirectX, на C# и XAML, если инкорпорируете вызовы DirectX в отдельный файл метаданных среды выполнения Windows.

 

## XAML и DirectX


DirectX предоставляет две мощные библиотеки для двух- и трехмерной графики: Direct2D и Microsoft Direct3D. Хотя язык XAML поддерживает базовые двухмерные примитивы и эффекты, многим приложениям (например, приложениям для моделирования или играм) необходима поддержка более сложной графики. В этих случаях библиотеки Direct2D и Direct3D можно использовать для обработки части или всей графики, а язык XAML — для всего остального.

В сценарии взаимодействия XAML и DirectX вам нужно знать две следующие концепции.

-   Общие поверхности — это области отображения заданного размера, определенные средствами языка XAML, для рисования на которых можно непрямо использовать DirectX с помощью типов [**Windows::UI::Xaml::Media::Brush**](https://msdn.microsoft.com/library/windows/apps/br228076). В случае общих поверхностей вы не управляете вызовами для представления цепочек буферов. Обновления общих поверхностей синхронизируются с обновлениями платформы XAML.
-   Собственно цепочка буферов. Цепочка переключения предоставляет фоновый буфер конвейера визуализации DirectX — область памяти, предназначенную для отображения после того, как однобуферная прорисовка выполнена.

Проанализируйте, для чего вы используете DirectX. Будет ли DirectX использоваться для формирования или анимации отдельного элемента управления, соответствующего размерам отображаемого окна? Может ли сформированная поверхность быть закрыта другими поверхностями или краями экрана? Будет ли она содержать выходные данные, требующие обработки и управления в режиме реального времени, как, например, в игре?

Определив способ использования DirectX, вы будете использовать один из типов среды выполнения Windows для включения обработки DirectX в приложение Магазина Windows:

-   Чтобы получить статическое изображение или нарисовать сложное изображение через интервалы, управляемые событиями, нарисуйте общую поверхность с помощью типа [**Windows::UI::Xaml::Media::Imaging::SurfaceImageSource**](https://msdn.microsoft.com/library/windows/apps/hh702041). Этот тип обрабатывает поверхности определенного размера, рисуемые средствами DirectX. Обычно данный тип используется при формировании изображения или текстуры, например точечного рисунка, для отображения в документе или элементе пользовательского интерфейса. Он плохо подходит для обеспечения взаимодействия в реальном времени, например в высокопроизводительных играх. Это связано с тем, что обновления объекта **SurfaceImageSource** синхронизированы с обновлениями пользовательского интерфейса XAML, из-за чего возможна задержка предоставляемой пользователю визуальной обратной связи, например флуктуация частоты кадров или заметное ухудшение реакции на ввод данных в реальном времени. Хотя для динамических элементов управления или моделирования данных обновления выполняются достаточно быстро.

    Графические объекты класса [
              **SurfaceImageSource**
            ](https://msdn.microsoft.com/library/windows/apps/hh702041) можно комбинировать с другими элементами пользовательского интерфейса на XAML. Их можно преобразовывать или проецировать, и платформа XAML будет учитывать любые значения прозрачности или z-индекса.

-   Если изображение больше предоставленного пространства на экране, пользователь может масштабировать его с помощью типа [**Windows::UI::Xaml::Media::Imaging::VirtualSurfaceImageSource**](https://msdn.microsoft.com/library/windows/apps/hh702050). Данный тип обрабатывает нарисованную средствами DirectX поверхность определенного размера, которая больше экрана. Как и класс [**SurfaceImageSource**](https://msdn.microsoft.com/library/windows/apps/hh702041), он используется при динамическом составлении сложного изображения или элемента управления. Кроме того, как и класс **SurfaceImageSource**, он плохо подходит для высокопроизводительных игр. К примерам элементов XAML, в которых может использоваться класс **VirtualSurfaceImageSource**, относятся элементы управления картой или средство просмотра документов с большим количеством изображений.

-   При использовании DirectX для представления графики, обновляемой в реальном времени, или в ситуации, когда обновления должны выполняться регулярно с низкой задержкой, используйте класс [**SwapChainPanel**](https://msdn.microsoft.com/library/windows/apps/dn252834) для обновления графики без синхронизации с таймером обновления платформы XAML. Этот тип предоставляет прямой доступ к цепочке буферов графического устройства ([**IDXGISwapChain1**](https://msdn.microsoft.com/library/windows/desktop/hh404631)) и слою XAML, наложенному на однобуферную прорисовку. Этот тип отлично подходит для игр и других полноэкранных приложений DirectX, которым требуется пользовательский интерфейс на основе языка XAML. Чтобы использовать описанный подход, вы должны хорошо разбираться в DirectX, в том числе в технологиях Microsoft DirectX Graphics Infrastructure (DXGI), Direct2D и Direct3D. Дополнительные сведения см. в разделе [Руководство по программированию для Direct3D 11](https://msdn.microsoft.com/library/windows/desktop/ff476345).

## SurfaceImageSource


[
              **SurfaceImageSource**
            ](https://msdn.microsoft.com/library/windows/apps/hh702041) предоставляет общие поверхности DirectX для рисования, а затем объединяет все биты в содержимое приложения.

Здесь описывается простой процесс создания и обновления объекта [**SurfaceImageSource**](https://msdn.microsoft.com/library/windows/apps/hh702041) в коде программной части.

1.  Определите размер общей поверхности, передав высоту и ширину конструктору [**SurfaceImageSource**](https://msdn.microsoft.com/library/windows/apps/hh702041). Вы можете также указать, требуется ли поверхности поддержка альфа-режима (непрозрачности).

    Пример:

    `SurfaceImageSource^ surfaceImageSource = ref new SurfaceImageSource(400, 300);`

2.  Получите указатель на интерфейс [**ISurfaceImageSourceNative**](https://msdn.microsoft.com/library/windows/desktop/hh848322). Приведите объект [**SurfaceImageSource**](https://msdn.microsoft.com/library/windows/apps/hh702041) к типу [**IInspectable**](https://msdn.microsoft.com/library/windows/desktop/br205821) (или **IUnknown**) и примените к нему метод **QueryInterface**, чтобы получить лежащую в основе реализацию интерфейса **ISurfaceImageSourceNative**. Методы, определенные в этой реализации, используются для настройки устройства и выполнения операций рисования.

    ```cpp
    Microsoft::WRL::ComPtr<ISurfaceImageSourceNative> m_sisNative;
    // ...
    IInspectable* sisInspectable = (IInspectable*) reinterpret_cast<IInspectable*>(surfaceImageSource);
    sisInspectable->QueryInterface(__uuidof(ISurfaceImageSourceNative), (void **)&m_sisNative);
    ```

3.  Настройте устройство DXGI, сначала вызвав метод [**D3D11CreateDevice**](https://msdn.microsoft.com/library/windows/desktop/ff476082), а затем передав устройство и контекст методу [**ISurfaceImageSourceNative::SetDevice**](https://msdn.microsoft.com/library/windows/desktop/hh848325). Пример:

    ```cpp
    Microsoft::WRL::ComPtr<ID3D11Device>              m_d3dDevice;
    Microsoft::WRL::ComPtr<ID3D11DeviceContext>           m_d3dContext;
    // ...
    D3D11CreateDevice(
            NULL,
            D3D_DRIVER_TYPE_HARDWARE,
            NULL,
            flags,
            featureLevels,
            ARRAYSIZE(featureLevels),
            D3D11_SDK_VERSION,
            &m_d3dDevice,
            NULL,
            &m_d3dContext
            )
        );  
    Microsoft::WRL::ComPtr<IDXGIDevice> dxgiDevice;
    m_d3dDevice.As(&dxgiDevice);
    // ...
    m_sisNative->SetDevice(dxgiDevice.Get());
    ```

4.  Предоставьте указатель объекту [**IDXGISurface**](https://msdn.microsoft.com/library/windows/desktop/bb174565) для метода [**ISurfaceImageSourceNative::BeginDraw**](https://msdn.microsoft.com/library/windows/desktop/hh848323) и рисуйте на этой поверхности с помощью DirectX. Прорисовка будет производиться только в области, указанной для обновления в параметре *updateRect*.

    > **Примечание.** В каждый момент времени у вас может быть только одна активная невыполненная операция [**BeginDraw**](https://msdn.microsoft.com/library/windows/desktop/hh848323) на один интерфейс [**IDXGIDevice**](https://msdn.microsoft.com/library/windows/desktop/bb174527).

     

    Этот метод возвращает смещение точки (x, y) обновленного целевого прямоугольника в параметре *offset*. С помощью этого смещения вы можете определить место, где будет выполняться рисование, внутри поверхности [**IDXGISurface**](https://msdn.microsoft.com/library/windows/desktop/bb174565).

    ```cpp
    ComPtr<IDXGISurface> surface;

    HRESULT beginDrawHR = m_sisNative->BeginDraw(updateRect, &surface, &offset);
    if (beginDrawHR == DXGI_ERROR_DEVICE_REMOVED || beginDrawHR == DXGI_ERROR_DEVICE_RESET)
    {
              // Device changed
    }
    else
    {
        // Draw to IDXGISurface (the surface paramater)
    }
    ```

5.  Вызовите метод [**ISurfaceImageSourceNative::EndDraw**](https://msdn.microsoft.com/library/windows/desktop/hh848324) для завершения формирования растрового изображения. Передайте это растровое изображение методу [**ImageBrush**](https://msdn.microsoft.com/library/windows/apps/br210101).

    ```cpp
    m_sisNative->EndDraw();
    // ...
    // The SurfaceImageSource object's underlying ISurfaceImageSourceNative object contains the completed bitmap.
    ImageBrush^ brush = ref new ImageBrush();
    brush->ImageSource = surfaceImageSource;
    ```

6.  Используйте метод [**ImageBrush**](https://msdn.microsoft.com/library/windows/apps/br210101), чтобы отобразить растровое изображение.

> **Примечание.** Вызов метода [**SurfaceImageSource::SetSource**](https://msdn.microsoft.com/library/windows/apps/br243255) (наследуемого от **IBitmapSource::SetSource**) в настоящее время инициирует исключение. Не вызывайте его из вашего объекта [**SurfaceImageSource**](https://msdn.microsoft.com/library/windows/apps/hh702041).

 

## VirtualSurfaceImageSource


Класс [
              **VirtualSurfaceImageSource**
            ](https://msdn.microsoft.com/library/windows/apps/hh702050) расширяет класс [**SurfaceImageSource**](https://msdn.microsoft.com/library/windows/apps/hh702041), когда содержимое может не поместиться на экране и поэтому требуется виртуализация содержимого для оптимального формирования изображения.

Класс [
              **VirtualSurfaceImageSource**
            ](https://msdn.microsoft.com/library/windows/apps/hh702050) отличается от класса [**SurfaceImageSource**](https://msdn.microsoft.com/library/windows/apps/hh702041) тем, что использует обратный вызов [**IVirtualSurfaceImageSourceCallbacksNative::UpdatesNeeded**](https://msdn.microsoft.com/library/windows/desktop/hh848337), реализуемый для обновления областей поверхности по мере их появления на экране. Скрытые области очищать не нужно, так как за это отвечает платформа XAML.

Здесь описывается простой процесс создания и обновления объекта [**VirtualSurfaceImageSource**](https://msdn.microsoft.com/library/windows/apps/hh702050) в коде программной части.

1.  Создайте экземпляр класса [**VirtualSurfaceImageSource**](https://msdn.microsoft.com/library/windows/apps/hh702050) нужного размера. Пример:

    `VirtualSurfaceImageSource^ virtualSIS = ref new VirtualSurfaceImageSource(2000, 2000);`

2.  Получите указатель на интерфейс [**IVirtualSurfaceImageSourceNative**](https://msdn.microsoft.com/library/windows/desktop/hh848328). Приведите объект [**VirtualSurfaceImageSource**](https://msdn.microsoft.com/library/windows/apps/hh702050) к типу [**IInspectable**](https://msdn.microsoft.com/library/windows/desktop/br205821) или [**IUnknown**](https://msdn.microsoft.com/library/windows/desktop/ms680509) и примените к нему метод [**QueryInterface**](https://msdn.microsoft.com/library/windows/desktop/ms682521), чтобы получить лежащую в основе реализацию интерфейса **IVirtualSurfaceImageSourceNative**. Методы, определенные в этой реализации, используются для настройки устройства и выполнения операций рисования.

    ```cpp
    Microsoft::WRL::ComPtr<IVirtualSurfaceImageSourceNative>  m_vsisNative;
    // ...
    IInspectable* vsisInspectable = (IInspectable*) reinterpret_cast<IInspectable*>(virtualSIS);
    vsisInspectable->QueryInterface(__uuidof(IVirtualSurfaceImageSourceNative), (void **)&m_vsisNative);
    ```

3.  Настройте устройство DXGI, вызвав метод [**IVirtualSurfaceImageSourceNative::SetDevice**](https://msdn.microsoft.com/library/windows/desktop/hh848325). Пример:

    ```cpp
    Microsoft::WRL::ComPtr<ID3D11Device>              m_d3dDevice;
    Microsoft::WRL::ComPtr<ID3D11DeviceContext>           m_d3dContext;
    // ...
    D3D11CreateDevice(
            NULL,
            D3D_DRIVER_TYPE_HARDWARE,
            NULL,
            flags,
            featureLevels,
            ARRAYSIZE(featureLevels),
            D3D11_SDK_VERSION,
            &m_d3dDevice,
            NULL,
            &m_d3dContext
            )
        );  
    Microsoft::WRL::ComPtr<IDXGIDevice> dxgiDevice;
    m_d3dDevice.As(&dxgiDevice);
    // ...
    m_vsisNative->SetDevice(dxgiDevice.Get());
    ```

4.  Вызовите метод [**IVirtualSurfaceImageSourceNative::RegisterForUpdatesNeeded**](https://msdn.microsoft.com/library/windows/desktop/hh848334), передав ссылку на свою реализацию интерфейса [**IVirtualSurfaceUpdatesCallbackNative**](https://msdn.microsoft.com/library/windows/desktop/hh848336).

    ```cpp
    class MyContentImageSource : public IVirtualSurfaceUpdatesCallbackNative
    {
    // ...
      private:
         virtual HRESULT STDMETHODCALLTYPE UpdatesNeeded() override;
    }

    // ...

    HRESULT STDMETHODCALLTYPE MyContentImageSource::UpdatesNeeded()
    {
      // .. Perform drawing here ...
    }
    void MyContentImageSource::Initialize()
    {
      // ...
      m_vsisNative->RegisterForUpdatesNeeded(this);
      // ...
    }
    ```

    Платформа вызывает вашу реализацию [**IVirtualSurfaceUpdatesCallbackNative::UpdatesNeeded**](https://msdn.microsoft.com/library/windows/desktop/hh848334), когда необходимо обновить область [**VirtualSurfaceImageSource**](https://msdn.microsoft.com/library/windows/apps/hh702050).

    Такое может произойти, если платформа определяет область, которую необходимо нарисовать (например, если пользователь масштабирует вид поверхности), или после применения к такой области метода [**IVirtualSurfaceImageSourceNative::Invalidate**](https://msdn.microsoft.com/library/windows/desktop/hh848332), вызванного приложением.

5.  В методе [**IVirtualSurfaceImageSourceNative::UpdatesNeeded**](https://msdn.microsoft.com/library/windows/desktop/hh848337) с помощью методов [**IVirtualSurfaceImageSourceNative::GetUpdateRectCount**](https://msdn.microsoft.com/library/windows/desktop/hh848329) и [**IVirtualSurfaceImageSourceNative::GetUpdateRects**](https://msdn.microsoft.com/library/windows/desktop/hh848330) определите, какие области поверхности необходимо нарисовать.

    ```cpp
    HRESULT STDMETHODCALLTYPE MyContentImageSource::UpdatesNeeded()
    {
        HRESULT hr = S_OK;

        try
        {
            ULONG drawingBoundsCount = 0;  

                  m_vsisNative->GetUpdateRectCount(&drawingBoundsCount);
            std::unique_ptr<RECT[]> drawingBounds(new RECT[drawingBoundsCount]);
            m_vsisNative->GetUpdateRects(drawingBounds.get(), drawingBoundsCount);
            
            for (ULONG i = 0; i < drawingBoundsCount; ++i)
            {
                // Drawing code here ...
            }
        }
        catch (Platform::Exception^ exception)
        {
            hr = exception->HResult;
        }

        return hr;
    }
    ```

6.  И наконец, для каждой области, которую нужно обновить, выполните следующее.

    1.  Предоставьте указатель объекту [**IDXGISurface**](https://msdn.microsoft.com/library/windows/desktop/bb174565) для метода [**IVirtualSurfaceImageSourceNative::BeginDraw**](https://msdn.microsoft.com/library/windows/desktop/hh848323) и рисуйте на этой поверхности с помощью DirectX. Прорисовка будет выполняться только в области, указанной для обновления в параметре *updateRect*.

        Так же как и [**IlSurfaceImageSourceNative::BeginDraw**](https://msdn.microsoft.com/library/windows/desktop/hh848323), этот метод возвращает смещение точки (x,y) обновленного целевого прямоугольника в параметре *offset*. С помощью этого смещения вы можете определить место, где будет выполняться рисование, внутри поверхности [**IDXGISurface**](https://msdn.microsoft.com/library/windows/desktop/bb174565).

        > **Примечание.** В каждый момент времени у вас может быть только одна активная невыполненная операция [**BeginDraw**](https://msdn.microsoft.com/library/windows/desktop/hh848323) на один интерфейс [**IDXGIDevice**](https://msdn.microsoft.com/library/windows/desktop/bb174527).

         

        ```cpp
        ComPtr<IDXGISurface> bigSurface;

        HRESULT beginDrawHR = m_vsisNative->BeginDraw(updateRect, &bigSurface, &offset);
        if (beginDrawHR == DXGI_ERROR_DEVICE_REMOVED || beginDrawHR == DXGI_ERROR_DEVICE_RESET)
        {
                  // Device changed
        }
        else
        {
            // Draw to IDXGISurface
        }
        ```

    2.  Рисуйте конкретное содержимое в этой области, но не выходите за пределы областей, чтобы не снизить производительность.

    3.  Вызовите [**IVirtualSurfaceImageSourceNative::EndDraw**](https://msdn.microsoft.com/library/windows/desktop/hh848324). Результат — растровое изображение

## SwapChainPanel и игры


[
              **SwapChainPanel**
            ](https://msdn.microsoft.com/library/windows/apps/dn252834) — это тип среды выполнения Windows для поддержки высокопроизводительных игр и графики, в котором вы управляете цепочкой буферов напрямую. В данном случае вы создаете собственную цепочку буферов DirectX и управляете представлением своего отображаемого содержимого. Затем в объект **SwapChainPanel** можно добавить такие элементы XAML, как меню, верхние панели и другие наложения пользовательского интерфейса.

Для обеспечения хорошей производительности у типа [**SwapChainPanel**](https://msdn.microsoft.com/library/windows/apps/dn252834) есть определенные ограничения.

-   Допускается только 4 экземпляра [**SwapChainPanel**](https://msdn.microsoft.com/library/windows/apps/dn252834) на приложение.
-   Свойства **Opacity**, **RenderTransform**, **Projection** и **Clip**, наследуемые классом [**SwapChainPanel**](https://msdn.microsoft.com/library/windows/apps/dn252834), не поддерживаются.
-   Следует задать высоту и ширину цепочки буферов DirectX (в структуре [**DXGI\_SWAP\_CHAIN\_DESC1**](https://msdn.microsoft.com/library/windows/desktop/hh404528)) равными текущим размерам окна приложения. Если этого не сделать, содержимое экрана подгоняется под его размер (с помощью **DXGI\_SCALING\_STRETCH**).
-   Для режима масштабирования цепочки буферов DirectX (в структуре [**DXGI\_SWAP\_CHAIN\_DESC1**](https://msdn.microsoft.com/library/windows/desktop/hh404528)) необходимо задать состояние **DXGI\_SCALING\_STRETCH**.
-   Для режима альфа-канала цепочки буферов DirectX (в структуре [**DXGI\_SWAP\_CHAIN\_DESC1**](https://msdn.microsoft.com/library/windows/desktop/hh404528)) невозможно задать состояние **DXGI\_ALPHA\_MODE\_PREMULTIPLIED**.
-   Необходимо создать цепочку буферов DirectX путем вызова [**IDXGIFactory2::CreateSwapChainForComposition**](https://msdn.microsoft.com/library/windows/desktop/hh404558).

[
            **SwapChainPanel**](https://msdn.microsoft.com/library/windows/apps/dn252834) обновляется в зависимости от потребностей приложения, а не обновлений платформы XAML. Если обновления **SwapChainPanel** необходимо синхронизировать с обновлениями платформы XAML, зарегистрируйте событие [**Windows::UI::Xaml::Media::CompositionTarget::Rendering**](https://msdn.microsoft.com/library/windows/apps/br228127). В противном случае необходимо учитывать все возможные проблемы с разными потоками при попытке обновления элементов XAML не из того потока, который обновляет **SwapChainPanel**.

Также есть несколько общих рекомендаций по проектированию приложений с использованием [**SwapChainPanel**](https://msdn.microsoft.com/library/windows/apps/dn252834).

-   [
              **SwapChainPanel**
            ](https://msdn.microsoft.com/library/windows/apps/dn252834) наследуется от класса [**Windows::UI::Xaml::Controls::Grid**](https://msdn.microsoft.com/library/windows/apps/br242704) и поддерживает схожее поведение макета. Ознакомьтесь с типом **Grid** и его свойствами.

-   После задания цепочки буферов DirectX все события ввода, возникающие для класса [**SwapChainPanel**](https://msdn.microsoft.com/library/windows/apps/dn252834), действуют так же, как и для любого другого элемента XAML. Для класса **SwapChainPanel** не задается кисть фона, и события ввода объекта [**CoreWindow**](https://msdn.microsoft.com/library/windows/apps/br208225) приложения не требуется обрабатывать напрямую, как это делается в приложениях DirectX, которые не используют класс **SwapChainPanel**.

-   • Все содержимое дерева визуальных элементов XAML в прямом дочернем элементе объекта [**SwapChainPanel**](https://msdn.microsoft.com/library/windows/apps/dn252834) обрезается до размера макета непосредственного дочернего элемента объекта **SwapChainPanel**. Любое содержимое, которое в результате преобразования выходит за рамки макета, не визуализируется. Поэтому любое содержимое XAML, которое анимируется с помощью класса [**Storyboard**](https://msdn.microsoft.com/library/windows/apps/br210490) XAML, размещайте в визуальном дереве элемента, рамки макета которого достаточно велики для включения всей области анимации.

-   Ограничьте число непосредственных визуальных элементов XAML в [**SwapChainPanel**](https://msdn.microsoft.com/library/windows/apps/dn252834). По возможности группируйте под общим родительским объектом элементы, которые находятся в непосредственной близости. Однако между производительностью и числом непосредственных визуальных дочерних объектов и их размерами существует связь: слишком большое количество элементов XAML или неоправданно большой их размер могут повлиять на общую производительность. Аналогичным образом не создавайте отдельных полноэкранных дочерних элементов XAML для объекта **SwapChainPanel** своего приложения, так как это повышает избыточное использование ресурсов в приложении и снижает производительность. Как правило, не следует создавать больше 8 непосредственных визуальных дочерних элементов XAML для объекта **SwapChainPanel** приложения. Размер макета каждого элемента не должен превышать достаточный для включения визуального содержимого элемента. Однако в дочернем элементе объекта **SwapChainPanel** можно создать достаточно сложное визуальное дерево элементов без чрезмерного ухудшения производительности.

> **Примечание.** В целом ваши приложения DirectX должны создавать цепочки буферов в альбомной ориентации и быть равными по размеру окну отображения (как правило, оно определяется собственным разрешением экрана в большинстве игр Магазина Windows). Таким образом гарантируется, что ваше приложение использует оптимальную реализацию цепочки буферов при отсутствии видимого наложения XAML. Если ориентация приложения меняется на книжную, ваше приложение должно вызывать [**IDXGISwapChain1::SetRotation**](https://msdn.microsoft.com/library/windows/desktop/hh446801) в существующей цепочке буферов, при необходимости применить к содержимому преобразование, а затем вызывать метод [**SetSwapChain**](https://msdn.microsoft.com/library/windows/desktop/dn302144) еще раз в той же цепочке буферов. Схожим образом ваше приложение должно снова вызывать **SetSwapChain** в той же цепочке буферов, когда размер этой цепочки изменяется посредством вызова [**IDXGISwapChain::ResizeBuffers**](https://msdn.microsoft.com/library/windows/desktop/bb174577).

 

Здесь описывается простой процесс создания и обновления объекта [**SwapChainPanel**](https://msdn.microsoft.com/library/windows/apps/dn252834) в коде программной части.

1.  Получите экземпляр панели цепочки буферов для своего приложения. Экземпляры указываются в XAML-коде с помощью тега `<SwapChainPanel>`.

    `Windows::UI::Xaml::Controls::SwapChainPanel^ swapChainPanel;`

    Пример использования тега `<SwapChainPanel>`.

    ```xml
    <SwapChainPanel x:Name="swapChainPanel">
        <SwapChainPanel.ColumnDefinitions>
            <ColumnDefinition Width="300*"/>
            <ColumnDefinition Width="1069*"/>
        </SwapChainPanel.ColumnDefinitions>
    …
    ```

2.  Получите указатель на интерфейс [**ISwapChainPanelNative**](https://msdn.microsoft.com/library/windows/desktop/dn302143). Приведите объект [**SwapChainPanel**](https://msdn.microsoft.com/library/windows/apps/dn252834) к типу [**IInspectable**](https://msdn.microsoft.com/library/windows/desktop/br205821) (или **IUnknown**) и примените к нему метод **QueryInterface**, чтобы получить лежащую в основе реализацию интерфейса **ISwapChainPanelNative**.

    ```cpp
    Microsoft::WRL::ComPtr<ISwapChainPanelNative> m_swapChainNative;
    // ...
    IInspectable* panelInspectable = (IInspectable*) reinterpret_cast<IInspectable*>(swapChainPanel);
    panelInspectable->QueryInterface(__uuidof(ISwapChainPanelNative), (void **)&m_swapChainNative);
    ```

3.  Создайте устройство DXGI и цепочку буферов. Цепочку буферов настройте в [**ISwapChainPanelNative**](https://msdn.microsoft.com/library/windows/desktop/dn302143), передав ее методу [**SetSwapChain**](https://msdn.microsoft.com/library/windows/desktop/dn302144).

    ```cpp
    Microsoft::WRL::ComPtr<IDXGISwapChain1>               m_swapChain;    
    // ...
    DXGI_SWAP_CHAIN_DESC1 swapChainDesc = {0};
            swapChainDesc.Width = m_bounds.Width;
            swapChainDesc.Height = m_bounds.Height;
            swapChainDesc.Format = DXGI_FORMAT_B8G8R8A8_UNORM;           // This is the most common swapchain format.
            swapChainDesc.Stereo = false; 
            swapChainDesc.SampleDesc.Count = 1;                          // Don't use multi-sampling.
            swapChainDesc.SampleDesc.Quality = 0;
            swapChainDesc.BufferUsage = DXGI_USAGE_RENDER_TARGET_OUTPUT;
            swapChainDesc.BufferCount = 2;
            swapChainDesc.Scaling = DXGI_SCALING_STRETCH;
            swapChainDesc.SwapEffect = DXGI_SWAP_EFFECT_FLIP_SEQUENTIAL; // We recommend using this swap effect for all. applications
            swapChainDesc.Flags = 0;
                    
    // QI for DXGI device
    Microsoft::WRL::ComPtr<IDXGIDevice> dxgiDevice;
    m_d3dDevice.As(&dxgiDevice);

    // Get the DXGI adapter.
    Microsoft::WRL::ComPtr<IDXGIAdapter> dxgiAdapter;
    dxgiDevice->GetAdapter(&dxgiAdapter);

    // Get the DXGI factory.
    Microsoft::WRL::ComPtr<IDXGIFactory2> dxgiFactory;
    dxgiAdapter->GetParent(__uuidof(IDXGIFactory2), &dxgiFactory);
    // Create a swap chain by calling CreateSwapChainForComposition.
    dxgiFactory->CreateSwapChainForComposition(
                m_d3dDevice.Get(),
                &swapChainDesc,
                nullptr,        // Allow on any display. 
                &m_swapChain
                );
            
    m_swapChainNative->SetSwapChain(m_swapChain.Get());
    ```

4.  Нарисуйте цепочку буферов DirectX и выполните ее представление, чтобы отобразить содержимое.

    ```cpp
    HRESULT hr = m_swapChain->Present(1, 0);
    ```

    Элементы XAML обновляются, когда логика макета или отрисовки среды выполнения Windows сигнализирует об обновлении.

## Связанные разделы


* [**SurfaceImageSource**](https://msdn.microsoft.com/library/windows/apps/hh702041)
* [**VirtualSurfaceImageSource**](https://msdn.microsoft.com/library/windows/apps/hh702050)
* [**SwapChainPanel**](https://msdn.microsoft.com/library/windows/apps/dn252834)
* [**ISwapChainPanelNative**](https://msdn.microsoft.com/library/windows/desktop/dn302143)
* [Руководство по программированию для Direct3D 11](https://msdn.microsoft.com/library/windows/desktop/ff476345)

 

 






<!--HONumber=May16_HO2-->


