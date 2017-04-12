---
author: mtoepke
title: "Настройка проекта игры"
description: "Первым шагом сборки вашей игры является настройка проекта в Microsoft Visual Studio, позволяющая свести к минимуму работу по созданию инфраструктуры кода."
ms.assetid: 9fde90b3-bf79-bcb3-03b6-d38ab85803f2
ms.author: mtoepke
ms.date: 02/08/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: "windows 10, uwp, игры, настройка, directx"
ms.openlocfilehash: 3bf9e6f70f71dc0b70a2f8af148c44acc1a329fb
ms.sourcegitcommit: 909d859a0f11981a8d1beac0da35f779786a6889
translationtype: HT
---
# <a name="set-up-the-game-project"></a>Настройка проекта игры


\[ Обновлено для приложений UWP в Windows10. Статьи о Windows8.x см. в [архиве](http://go.microsoft.com/fwlink/p/?linkid=619132) \]

Первым шагом сборки вашей игры является настройка проекта в Microsoft Visual Studio, позволяющая свести к минимуму работу по созданию инфраструктуры кода. Вы можете сэкономить много времени и усилий, выбрав правильный шаблон и настроив проект специально для разработки игры. Ниже описан процесс настройки и выбора конфигурации для проекта по разработке простой игры.

## <a name="objective"></a>Цель


-   Понять, как настроить проект игры Direct3D в Visual Studio.

## <a name="setting-up-the-game-project"></a>Настройка проекта игры


Вы можете написать игру с нуля просто с помощью удобного текстового редактора, нескольких примеров и головы, в которой бурлят идеи. Однако такое использование вашего времени не будет оптимальным. Если вы только начинаете разработку для универсальной платформы Windows (UWP), почему бы не переложить часть работы на Visual Studio? Ниже приведены шаги, выполнив которые, можно быстро начать проект.

## <a name="1-pick-the-right-template"></a>1. Выберите подходящий шаблон.


Шаблон Visual Studio— это набор параметров и файлов кода, которые рассчитаны на определенный тип приложений и основаны на выбранном языке программирования и технологии. В Microsoft Visual Studio 2015 вы найдете большое количество шаблонов, которые могут значительно облегчить разработку игровых и графических приложений. Без шаблона вам придется самостоятельно разработать большую часть кода для отрисовки основной графики и структуры отображения, что может оказаться слишком сложной задачей для начинающего разработчика игр.

Для этого учебника мы будем использовать шаблон "Приложение DirectX 11 (универсальные приложения для Windows)". В Visual Studio 2015 выберите **Файл...** &gt; **Создать проект**. Затем:

1.  В списке **Шаблоны** выберите **Visual C++**, **Windows**, **Универсальные**.
2.  На центральной панели выберите **Приложение DirectX 11 (универсальные приложения для Windows)**.
3.  Выберите имя для проекта игры и нажмите **ОК**.

![Выбор шаблона "Приложение Direct3D"](images/simple-dx-game-vs-new-proj.png)

Этот шаблон предоставляет базовую структуру для приложения UWP на языке C++ с использованием DirectX. Дерзайте! Выполните сборку и запустите приложение с помощью клавиши F5! Посмотрите на этот туманно-синий экран. Не пожалейте времени на просмотр кода, предоставляемого шаблоном. Шаблон создает несколько файлов кода, содержащих базовые функциональные возможности для приложения UWP на языке C++ c использованием DirectX Остальные файлы кода мы обсудим подробнее на [шаге 3](#3-review-the-included-libraries-and-headers). Сейчас кратко рассмотрим **App.h**.

```cpp
    ref class App sealed : public Windows::ApplicationModel::Core::IFrameworkView
    {
    public:
        App();

        // IFrameworkView Methods.
        virtual void Initialize(Windows::ApplicationModel::Core::CoreApplicationView^ applicationView);
        virtual void SetWindow(Windows::UI::Core::CoreWindow^ window);
        virtual void Load(Platform::String^ entryPoint);
        virtual void Run();
        virtual void Uninitialize();

    protected:
        // Application lifecycle event handlers.
        void OnActivated(Windows::ApplicationModel::Core::CoreApplicationView^ applicationView, Windows::ApplicationModel::Activation::IActivatedEventArgs^ args);
        void OnSuspending(Platform::Object^ sender, Windows::ApplicationModel::SuspendingEventArgs^ args);
        void OnResuming(Platform::Object^ sender, Platform::Object^ args);

        // Window event handlers.
        void OnWindowSizeChanged(Windows::UI::Core::CoreWindow^ sender, Windows::UI::Core::WindowSizeChangedEventArgs^ args);
        void OnVisibilityChanged(Windows::UI::Core::CoreWindow^ sender, Windows::UI::Core::VisibilityChangedEventArgs^ args);
        void OnWindowClosed(Windows::UI::Core::CoreWindow^ sender, Windows::UI::Core::CoreWindowEventArgs^ args);

        // DisplayInformation event handlers.
        void OnDpiChanged(Windows::Graphics::Display::DisplayInformation^ sender, Platform::Object^ args);
        void OnOrientationChanged(Windows::Graphics::Display::DisplayInformation^ sender, Platform::Object^ args);
        void OnDisplayContentsInvalidated(Windows::Graphics::Display::DisplayInformation^ sender, Platform::Object^ args);

    private:
        std::shared_ptr<DX::DeviceResources> m_deviceResources;
        std::unique_ptr<MyAwesomeGameMain> m_main;
        bool m_windowClosed;
        bool m_windowVisible;
    };
```

Вы создаете эти 5методов ([**Initialize**](https://msdn.microsoft.com/library/windows/apps/hh700495), [**SetWindow**](https://msdn.microsoft.com/library/windows/apps/hh700509), [**Load**](https://msdn.microsoft.com/library/windows/apps/hh700501), [**Run**](https://msdn.microsoft.com/library/windows/apps/hh700505) и  [**Uninitialize**](https://msdn.microsoft.com/library/windows/apps/hh700523)) при реализации интерфейса [**IFrameworkView**](https://msdn.microsoft.com/library/windows/apps/hh700469), который определяет поставщик представления. Эти методы запускаются singleton-объектом приложения, который создается при запуске игры, загружает ресурсы приложения, а также подключает соответствующие обработчики событий.

Ваш метод **main** находится в исходном файле **App.cpp**. Он выглядит следующим образом:

```cpp
[Platform::MTAThread]
int main(Platform::Array<Platform::String^>^)
{
    auto direct3DApplicationSource = ref new Direct3DApplicationSource();
    CoreApplication::Run(direct3DApplicationSource);
    return 0;
}
```

Сейчас он создает экземпляр поставщика представления Direct3D из фабрики поставщиков представлений (**Direct3DApplicationSource**, определенный в **App.h**), а затем передает его singleton-объекту приложения для запуска ([**CoreApplication::Run**](https://msdn.microsoft.com/library/windows/apps/hh700469)). Это означает, что начальная точка вашей игры будет находиться в реализации метода [**IFrameworkView::Run**](https://msdn.microsoft.com/library/windows/apps/hh700505), в данном случае **App::Run**. Вот соответствующий код.

```cpp
void App::Run()
{
    while (!m_windowClosed)
    {
        if (m_windowVisible)
        {
            CoreWindow::GetForCurrentThread()->Dispatcher->ProcessEvents(CoreProcessEventsOption::ProcessAllIfPresent);

            m_main->Update();

            if (m_main->Render())
            {
                m_deviceResources->Present();
            }
        }
        else
        {
            CoreWindow::GetForCurrentThread()->Dispatcher->ProcessEvents(CoreProcessEventsOption::ProcessOneAndAllPending);
        }
    }
}
```

Если окно вашей игры не закрыто, оно будет обрабатывать все события, обновлять таймер, а также обрабатывать и предоставлять результаты конвейера визуализации. Мы поговорим об этом подробнее в разделах [Определение структуры игры UWP](tutorial--building-the-games-metro-style-app-framework.md) и [Сборка конвейера визуализации](tutorial--assembling-the-rendering-pipeline.md). На этом этапе вы должны понимать основную структуру кода игры UWP на базе DirectX.

## <a name="2-review-and-update-the-packageappxmanifest-file"></a>2. Обзор и обновление файла package.appxmanifest.


В шаблоне есть не только файлы кода. Файл **package.appxmanifest** содержит метаданные о вашем проекте, которые используются для упаковки и запуска игры, а также для ее передачи в Магазин Windows. Также в нем находится важная информация, используемая системой игрока для предоставления доступа к системным ресурсам, необходимым для выполнения игры.

Запустите **Конструктор манифеста**, дважды щелкнув файл **package.appxmanifest** в **обозревателе решений**. Вы увидите следующее:

![Редактор манифеста package.appx.](images/simple-dx-game-vs-app-manifest.png)

Подробнее о файле **package.appxmanifest** и упаковке см. в разделе [Конструктор манифестов](https://msdn.microsoft.com/library/windows/apps/br230259.aspx). А теперь откройте вкладку **Возможности** и посмотрите на имеющиеся параметры.

![Здесь представлены возможности по умолчанию для приложения Direct3D.](images/simple-dx-game-vs-capabilities.png)

Если вы не выберете необходимые возможности для игры, например доступ к **Интернету** для использования глобальной таблицы рекордов, вы не сможете получить доступ к соответствующим ресурсам и функциям. При создании новой игры не забудьте выбрать возможности, которые необходимы для ее работы.

Теперь посмотрим на остальные файлы в составе шаблона **Приложение DirectX11 (универсальные приложения для Windows)**.

## <a name="3-review-the-included-libraries-and-headers"></a>3. Обзор включенных библиотек и заголовков.


Есть еще несколько файлов, которые мы до сих пор не рассматривали. Эти файлы предоставляют дополнительные общие инструменты и поддержку для сценариев разработки игр для Direct3D.

| Исходный файл шаблона         | Описание                                                                                                                                                                                                            |
|------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| StepTimer.h                  | Определяет высокоточный таймер для игр или интерактивных приложений с прорисовкой.                                                                                                                                       |
| Sample3DSceneRenderer.h/.cpp | Определяет базовую реализацию обработчика, объединяющего цепочку буферов Direct3D и графический адаптер для игры UWP на DirectX.                                                                                            |
| DirectXHelper.h              | Реализует единый метод **DX::ThrowIfFailed**, который преобразует значения ошибок HRESULT, возвращаемых API DirectX, в исключения среды выполнения Windows. Используйте этот метод, чтобы устанавливать точки прерывания для отладки ошибок DirectX. |
| pch.h/.cpp                   | Содержит все системные включения Windows для интерфейсов API, используемых приложением Direct3D, в том числе API для DirectX 11.                                                                                                           |
| SamplePixelShader.hlsl       | Содержит код высокоуровнего языка шейдера (HLSL) для базового построителя текстуры.                                                                                                                                     |
| SampleVertexShader.hlsl      | Содержит код высокоуровнего языка шейдера (HLSL) для базового вершинного шейдера.                                                                                                                                    |

 

### <a name="next-steps"></a>Следующие действия

На этом этапе вы можете создать проект игры UWP на базе DirectX и определить компоненты и файлы, предоставленные в шаблоне "Приложение DirectX11 (универсальные приложения для Windows)".

В следующем учебнике, [Определение структуры игры UWP](tutorial--building-the-games-metro-style-app-framework.md), мы изучим завершенную игру и рассмотрим, как в ней использованы и расширены многие концепции и компоненты шаблона.

 

 




