---
title: Настройка проекта игры
description: Первым шагом сборки вашей игры является настройка проекта в Microsoft Visual Studio, позволяющая свести к минимуму работу по созданию инфраструктуры кода.
ms.assetid: 9fde90b3-bf79-bcb3-03b6-d38ab85803f2
ms.date: 10/24/2017
ms.topic: article
keywords: windows 10, uwp, игры, настройка, directx
ms.localizationpriority: medium
ms.openlocfilehash: 789b235220e5d22b85f7b3038d5d468729439501
ms.sourcegitcommit: 7a3d28472901edbe4ecdde7e1a01a505ee5bc028
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/29/2019
ms.locfileid: "58658770"
---
# <a name="set-up-the-game-project"></a>Настройка проекта игры

В этой статье рассматривается создание простой игры UWP на базе DirectX с использованием шаблонов, имеющихся в Visual Studio. Первым шагом сборки вашей игры является настройка проекта в Microsoft Visual Studio, позволяющая свести к минимуму работу по созданию инфраструктуры кода. Узнайте, как сэкономить время, выбрав правильный шаблон и настроив проект специально для разработки игры.

## <a name="objectives"></a>Цели

* Создать в Visual Studio проект игры на базе Direct3D с использованием шаблона
* Определить главную точку входа игры путем изучения исходных файлов **App**
* Изучить файл **package.appxmanifest** проекта
* Узнать, какие средства разработки игр и поддержка входят в состав проекта

## <a name="how-to-set-up-the-game-project"></a>Создание проекта игры

Если вы новичок в разработке для универсальной платформы Windows (UWP), мы рекомендуем использовать шаблоны в Visual Studio для создания базовой структуры кода.

>[!Note]
>Эта статья входит в серию учебников, основанную на примере игры. Получить последнюю версию кода можно на [странице примера игры Direct3D](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/Simple3DGameDX). Этот пример является частью большой коллекции примеров функций UWP. Указания по скачиванию этого примера приводятся в разделе [Получение примеров UWP из GitHub](https://docs.microsoft.com/windows/uwp/get-started/get-uwp-app-samples).

### <a name="use-directx-template-to-create-a-project"></a>Использование шаблона DirectX для создания проекта

Шаблон Visual Studio — это набор параметров и файлов кода, которые рассчитаны на определенный тип приложений и основаны на выбранном языке программирования и технологии. В Microsoft Visual Studio 2017 вы найдете несколько шаблонов, которые могут значительно упростить разработки приложений для игр и графики. Без шаблона вам придется самостоятельно разработать большую часть кода для отрисовки основной графики и структуры отображения, что может оказаться слишком сложной задачей для начинающего разработчика игр.

В этом учебнике используется шаблон под названием **Приложение DirectX 11 (универсальное приложение Windows)**. 

Порядок создания игрового проекта DirectX 11 в Visual Studio:
1.  Выберите **файл...** &gt; **Новый** &gt; **проекта...**
2.  В левой области выберите **установленные** &gt; **шаблоны** &gt; **Visual C++** &gt; **универсальной Windows**
3.  На центральной панели выберите **Приложение DirectX 11 (универсальное приложение Windows)**.
4.  Выберите имя для проекта игры и нажмите **ОК**.

![Снимок экрана, показывающий, как выбрать шаблон DirectX 11 для создания нового проекта игры](images/simple-dx-game-setup-new-project.png)

Этот шаблон предоставляет базовую структуру для приложения UWP на языке C++ с использованием DirectX. Нажмите F5, чтобы выполнить сборку и запустить приложение. Посмотрите на этот туманно-синий экран. Шаблон создает несколько файлов кода, содержащих базовые функциональные возможности для приложения UWP на языке C++ c использованием DirectX

## <a name="review-the-apps-main-entry-point-by-understanding-iframeworkview"></a>Определение главной точки входа приложения путем изучения IFrameworkView

Класс **App** наследуется от класса **IFrameworkView**.

### <a name="inspect-apph"></a>Изучите файл **App.h**.

Давайте быстро взглянуть на 5 методы **файле App.h** &mdash; [ **инициализировать**](https://msdn.microsoft.com/library/windows/apps/hh700495), [ **SetWindow** ](https://msdn.microsoft.com/library/windows/apps/hh700509), [ **Нагрузки**](https://msdn.microsoft.com/library/windows/apps/hh700501), [ **запуска**](https://msdn.microsoft.com/library/windows/apps/hh700505), и [ **отменить инициализацию** ](https://msdn.microsoft.com/library/windows/apps/hh700523) при реализации [ **IFrameworkView** ](https://msdn.microsoft.com/library/windows/apps/hh700469) интерфейс, определяющий поставщик представления. Эти методы запускаются singleton-объектом приложения, который создается при запуске игры, загружает ресурсы приложения, а также подключает соответствующие обработчики событий.

```cpp
    // Main entry point for our app. Connects the app with the Windows shell and handle application lifecycle events.
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
        ...
    };
```

### <a name="inspect-appcpp"></a>Изучите файл **App.cpp**

Вот метод **main** в исходном файле **App.cpp**:

```cpp
//The main function is only used to initialize our IFrameworkView class.
[Platform::MTAThread]
int main(Platform::Array<Platform::String^>^)
{
    auto direct3DApplicationSource = ref new Direct3DApplicationSource();
    CoreApplication::Run(direct3DApplicationSource);
    return 0;
}
```

Этот метод создает экземпляр поставщика представление Direct3D из фабрики поставщика представление (**Direct3DApplicationSource**, определенный в `App.h`) и передает его единственного экземпляра приложения путем вызова [  **CoreApplication::Run**](/uwp/api/windows.applicationmodel.core.coreapplication.run). Методы класса представления framework (который является **приложения** в этом примере класс) вызываются в порядке **инициализировать**-**SetWindow** - **Нагрузки**-**OnActivated**-**запуска**-**отменить инициализацию**. Вызов **CoreApplication::Run** запускает этот lifycycle. Основной цикл игры находится в теле реализации [ **IFrameworkView::Run** метод](/uwp/api/windows.applicationmodel.core.iframeworkview.run), и в этом случае он имеет **App::Run**.

Прокрутите вниз, чтобы найти метод **App::Run** в **App.cpp**. Ниже приведен код.

```cpp
//This method is called after the window becomes active.
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

Что делает этот метод: Если окно вашей игры не закрыто, он отправляет все события, обновляет таймер, затем отображает и представлены результаты конвейера графики. Мы рассмотрим это более подробно в [определить платформа приложения UWP](tutorial--building-the-games-uwp-app-framework.md), [платформу для отображения инструкции: Общие сведения о подготовке к просмотру](tutorial--assembling-the-rendering-pipeline.md), и [платформу для отображения II: Отрисовки игр](tutorial-game-rendering.md). На этом этапе вы должны понимать основную структуру кода игры UWP на базе DirectX.

## <a name="review-and-update-the-packageappxmanifest-file"></a>Изучение и обновление файла package.appxmanifest


В шаблоне есть не только файлы кода. Файл **package.appxmanifest** содержит метаданные о вашем проекте, которые используются для упаковки и запуска игры, а также для ее отправки в Microsoft Store. Также в нем находится важная информация, используемая системой игрока для предоставления доступа к системным ресурсам, необходимым для выполнения игры.

Запустите **Конструктор манифеста**, дважды щелкнув файл **package.appxmanifest** в **Обозревателе решений**.

![Снимок экрана редактора манифеста package.appx.](images/simple-dx-game-setup-app-manifest.png)

Подробнее о файле **package.appxmanifest** и упаковке см. в разделе [Конструктор манифестов](https://msdn.microsoft.com/library/windows/apps/br230259.aspx). А теперь откройте вкладку **Возможности** и посмотрите на имеющиеся параметры.

![Снимок экрана с возможностями по умолчанию для приложения Direct3D.](images/simple-dx-game-setup-capabilities.png)

Если вы не выберете необходимые возможности для игры, например доступ к **Интернету** для использования глобальной таблицы рекордов, вы не сможете получить доступ к соответствующим ресурсам и функциям. При создании новой игры не забудьте выбрать возможности, которые необходимы для ее работы.

Теперь посмотрим на остальные файлы в составе шаблона **Приложение DirectX 11 (универсальные приложения для Windows)**.

## <a name="review-the-included-libraries-and-headers"></a>Изучение включенных библиотек и заголовков

Есть еще несколько файлов, которые мы до сих пор не рассматривали. Эти файлы предоставляют дополнительные общие инструменты и поддержку для сценариев разработки игр для Direct3D. Полный список файлов, входящих в состав базового игрового проекта с использованием DirectX, см. в статье [Шаблоны проектов игр DirectX](user-interface.md#template-structure).

| Исходный файл шаблона         | Папка файла            | Описание |
|------------------------------|------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| DeviceResources.h/.cpp       | Общие                 | Определяет объект класса, который управляет всеми [ресурсами устройства](tutorial--assembling-the-rendering-pipeline.md#resource) DirectX. Также включает в себя интерфейс для приложения, которому принадлежат DeviceResources для уведомления при потере или создании устройства.                                                |
| DirectXHelper.h              | Общие                 | Реализует методы, в том числе **DX::ThrowIfFailed**, **ReadDataAsync** и ** ConvertDipsToPixels. **DX::ThrowIfFailed** преобразовывает значения ошибок HRESULT, возвращаемые API Win32 DirectX, в исключения среды выполнения Windows. Используйте этот метод, чтобы устанавливать точки прерывания для отладки ошибок DirectX. Подробнее: [ThrowIfFailed](https://github.com/Microsoft/DirectXTK/wiki/ThrowIfFailed). **ReadDataAsync** асинхронно считывает данные из двоичного файла. **ConvertDipsToPixels** преобразовывает длину в аппаратно независимых пикселях (DIP) в длину в физических пикселях. |
| StepTimer.h                  | Общие                 | Определяет высокоточный таймер для игр или интерактивных приложений с прорисовкой.   |
| Sample3DSceneRenderer.h/.cpp | Content                | Определяет объект класса для создания экземпляра базового контейнера отрисовки. Создает базовую реализацию отрисовщика, которая соединяет цепочку буферов Direct3D и графический адаптер с вашей игрой UWP с использование DirectX.   |
| SampleFPSTextRenderer.h/.cpp | Content                | Определяет объект класса для отрисовки текущего значения кадров в нижнем правому углу экрана с помощью Direct2D и DirectWrite.  |
| SamplePixelShader.hlsl       | Content                | Содержит код высокоуровнего языка шейдера (HLSL) для базового построителя текстуры.                                            |
| SampleVertexShader.hlsl      | Content                | Содержит код высокоуровнего языка шейдера (HLSL) для базового вершинного шейдера.                                           |
| ShaderStructures.h           | Content                | Содержит структуры шейдера, которые можно использовать для отправки матриц MVP и повершинных данных в вершинный шейдер.  |
| pch.h/.cpp                   | Основной блок                   | Содержит все системные включения Windows для интерфейсов API, используемых приложением Direct3D, в том числе API для DirectX 11.| 

### <a name="next-steps"></a>Следующие шаги

На этом этапе вы знаете, как создать проект игры UWP на базе DirectX с использованием шаблона **Приложение DirectX 11 (универсальное приложение Windows)**, а также ознакомились с некоторыми компонентами и файлами, содержащимися в этом проекте.

Следующий раздел — это [Определение структуры игры UWP](tutorial--building-the-games-uwp-app-framework.md). Мы рассмотрим, как в этой игре используются и расширяются многие из концепций и компонентов, предусмотренных в шаблоне.

 

 




