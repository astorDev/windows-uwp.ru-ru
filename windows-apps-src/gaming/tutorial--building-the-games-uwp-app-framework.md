---
title: Определение структуры приложения UWP
description: Первый этап программирования игры универсальной платформы Windows (UWP) на базе DirectX заключается в создании структуры, которая позволит объекту игры взаимодействовать с Windows.
ms.assetid: 7beac1eb-ba3d-e15c-44a1-da2f5a79bb3b
ms.date: 10/24/2017
ms.topic: article
keywords: windows 10, uwp, игры, directx
ms.localizationpriority: medium
ms.openlocfilehash: 940de8c00dc2639785ae82e87d63f4994b1b6b2e
ms.sourcegitcommit: ac7f3422f8d83618f9b6b5615a37f8e5c115b3c4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/29/2019
ms.locfileid: "66367739"
---
#  <a name="define-the-uwp-app-framework"></a>Определение структуры приложения UWP

Узнайте, как создать структуру, которая позволит вашему игровому объекту взаимодействовать с Windows, в том числе со свойствами среды выполнения Windows, такими приостановка и возобновление обработки событий, изменения фокуса окна и прикрепление.

Для создания такой структуры необходимо сначала получить поставщик представлений, который singleton-объект приложения — объект среды выполнения Windows, определяющий экземпляр работающего приложения, — может использовать для доступа к необходимым графическим ресурсам. Посредством среды выполнения Windows приложение также напрямую подключается к графическому интерфейсу, что позволяет вам указать необходимые ресурсы и порядок их обработки.

Объект поставщика представлений реализует интерфейс __IFrameworkView__, состоящий из серии методов, которые необходимо настроить для создания этого примера игры.

Вам понадобится реализовать следующие пять методов, вызываемые singleton-объектом приложения:
* [__Инициализация__](#initialize-the-view-provider)
* [__SetWindow__](#configure-the-window-and-display-behaviors)
* [__загрузить__](#load-method-of-the-view-provider)
* [__запустить__](#run-method-of-the-view-provider)
* [__Отменить инициализацию__](#uninitialize-method-of-the-view-provider)

Метод __Initialize__ вызывается при запуске приложения. Метод __SetWindow__ вызывается после метода __Initialize__. После этого вызывается метод __Load__. Метод __Run__ вызывается, когда игра запущена. По окончании игры вызывается метод __Uninitialize__. Подробнее см. в [справочных материалах по API __IFrameworkView__](https://docs.microsoft.com/uwp/api/windows.applicationmodel.core.iframeworkview). 

>[!Note]
>Если вы еще не скачали последний код для игры из этого примера, перейдите в раздел [Пример игры Direct3D](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/Simple3DGameDX). Этот пример является частью большой коллекции примеров функций UWP. Указания по скачиванию этого примера приводятся в разделе [Получение примеров UWP из GitHub](https://docs.microsoft.com/windows/uwp/get-started/get-uwp-app-samples).

## <a name="objective"></a>Цель

Создать структуру для игры универсальной платформы Windows (UWP) на базе DirectX и реализовать конечный автомат, определяющий общий поток игры.

## <a name="define-the-view-provider-factory-and-view-provider-object"></a>Определить фабрику поставщиков представлений и объект поставщика представлений

Давайте рассмотрим цикл __main__ в файле __App.cpp__. 

На этом этапе мы создадим для представления фабрику (реализует __IFrameworkViewSource__), которая в свою очередь создает экземпляры объекта поставщика представлений (реализует __IFrameworkView__), определяющего представление.

### <a name="main-method"></a>Метод Main

Создайте новый __DirectXApplicationSource__, если у вас загружен код примера с GitHub. (Используйте __Direct3DApplicationSource__, если вы используете исходный шаблон DirectX) Это фабрика поставщиков представлений, которая реализует __IFrameworkViewSource__. В интерфейсе __IFrameworkViewSource__ фабрики поставщика представлений определен один-единственный метод __CreateView__.

В __CoreApplication::Run__ метод __CreateView__ вызывается singleton-объектом приложения при передаче __Direct3DApplicationSource__ или __DirectXApplicationSource__.

__CreateView__ возвращает ссылку на новый экземпляр объекта вашего приложения, который реализует __IFrameworkView__, т. е. объект класса __App__ в данном примере. Объект класса __App__ — это объект поставщика представлений.

```cpp
// The main function is only used to initialize our IFrameworkView class.
[Platform::MTAThread]
int main(Platform::Array<Platform::String^>^)
{
    auto directXApplicationSource = ref new DirectXApplicationSource();
    CoreApplication::Run(directXApplicationSource);
    return 0;
}

//--------------------------------------------------------------------------------------

IFrameworkView^ DirectXApplicationSource::CreateView()
{
    return ref new App();
}
```

## <a name="initialize-the-view-provider"></a>Инициализация поставщика представлений

После создания объекта поставщика представлений singleton-объект приложения вызывает метод [**Initialize**](https://docs.microsoft.com/uwp/api/windows.applicationmodel.core.iframeworkview.initialize) (при запуске приложения). Поэтому очень важно, чтобы этот метод обрабатывал большинство основных вариантов поведения игры UWP, таких как активация главного окна и внезапная приостановка (с последующим возобновлением).

На этом этапе игровое приложение может обрабатывать сообщение о приостановке (или возобновлении). Однако отсутствует окно, с которым можно было бы работать, и игра не инициализирована. Необходимо выполнить еще ряд операций!

### <a name="appinitialize-method"></a>Метод App::Initialize

В этом методе создаются различные обработчики событий для активации, приостановки и возобновления игры.

Получите доступ к ресурсам устройства. Функция __make_shared__ используется для создания __shared_ptr__ при первом создании ресурса памяти. Преимущество использования __make_shared__ состоит в том, что это обеспечивает обработку всех исключений. Один и тот же вызов используется для выделения памяти для блока управления и ресурса, т. е. сокращаются временные затраты на построение.

```cpp
void App::Initialize(
    CoreApplicationView^ applicationView
    )
{
    // Register event handlers for app lifecycle. This example includes Activated, so that we
    // can make the CoreWindow active and start rendering on the window.
    applicationView->Activated +=
        ref new TypedEventHandler<CoreApplicationView^, IActivatedEventArgs^>(this, &App::OnActivated);

    CoreApplication::Suspending +=
        ref new EventHandler<SuspendingEventArgs^>(this, &App::OnSuspending);

    CoreApplication::Resuming +=
        ref new EventHandler<Platform::Object^>(this, &App::OnResuming);

    // At this point we have access to the device. 
    // We can create the device-dependent resources.
    m_deviceResources = std::make_shared<DX::DeviceResources>();
}
```

## <a name="configure-the-window-and-display-behaviors"></a>Настройка поведения окна и экрана

Теперь давайте рассмотрим реализацию [__SetWindow__](https://docs.microsoft.com/uwp/api/windows.applicationmodel.core.iframeworkview.setwindow). В методе __SetWindow__ настраиваются операции в окне и на экране.

### <a name="appsetwindow-method"></a>Метод App::SetWindow

Singleton-объект приложения предоставляет объект [__CoreWindow__](https://docs.microsoft.com/uwp/api/Windows.UI.Core.CoreWindow), который представляет главное окно игры и открывает игре доступ к его ресурсам и событиям. Теперь, когда у игры есть окно, можно начинать добавлять базовые компоненты и события пользовательского интерфейса.

После этого необходимо создать с помощью метода __CoreCursor__ указатель, который может использоваться и мышью, и сенсорными элементами управления.

Наконец, создайте основные события для изменения размеров, закрытия и изменения DPI окна (при изменении устройства отображения). Подробнее о событиях см. в статье [Обработка событий](tutorial-game-flow-management.md#events-handling).

```cpp
void App::SetWindow(
    CoreWindow^ window
    )
{
    // Codes added to modify the original DirectX template project
    window->PointerCursor = ref new CoreCursor(CoreCursorType::Arrow, 0);

    PointerVisualizationSettings^ visualizationSettings = PointerVisualizationSettings::GetForCurrentView();
    visualizationSettings->IsContactFeedbackEnabled = false;
    visualizationSettings->IsBarrelButtonFeedbackEnabled = false;
    // --end of codes added
    
    m_deviceResources->SetWindow(window);

    window->Activated +=
        ref new TypedEventHandler<CoreWindow^, WindowActivatedEventArgs^>(this, &App::OnWindowActivationChanged);

    window->SizeChanged +=
        ref new TypedEventHandler<CoreWindow^, WindowSizeChangedEventArgs^>(this, &App::OnWindowSizeChanged);

    window->VisibilityChanged +=
        ref new TypedEventHandler<CoreWindow^, VisibilityChangedEventArgs^>(this, &App::OnVisibilityChanged);
        
    window->Closed +=
        ref new TypedEventHandler<CoreWindow^, CoreWindowEventArgs^>(this, &App::OnWindowClosed);

    DisplayInformation^ currentDisplayInformation = DisplayInformation::GetForCurrentView();

    currentDisplayInformation->DpiChanged +=
        ref new TypedEventHandler<DisplayInformation^, Platform::Object^>(this, &App::OnDpiChanged);

    currentDisplayInformation->OrientationChanged +=
        ref new TypedEventHandler<DisplayInformation^, Object^>(this, &App::OnOrientationChanged);
    
    // Codes added to modify the original DirectX template project
    currentDisplayInformation->StereoEnabledChanged +=
        ref new TypedEventHandler<DisplayInformation^, Platform::Object^>(this, &App::OnStereoEnabledChanged);
    // --end of codes added
    
    DisplayInformation::DisplayContentsInvalidated +=
        ref new TypedEventHandler<DisplayInformation^, Platform::Object^>(this, &App::OnDisplayContentsInvalidated);
}
```

## <a name="load-method-of-the-view-provider"></a>Метод Load поставщика представлений

После задания главного окна singleton-объект приложения вызывает метод [__Load__](https://docs.microsoft.com/uwp/api/windows.applicationmodel.core.iframeworkview.load). В этом методе используется набор асинхронных задач для создания игровых объектов, загрузки графических ресурсов и инициализации конечного автомата игры. Если необходимо предварительно получить данные или ресурсы игры, лучше сделать это на данном этапе, а не на этапе **SetWindow** или **Initialize**. 

Поскольку Windows накладывает ограничения на время, которое может пройти до того, как игра начнет обрабатывать ввод, с помощью шаблона асинхронной задачи необходимо спроектировать метод __Load__ так, чтобы он завершился быстро и можно было начать обработку ввода. Если загрузка игры занимает длительное время или в игре большое количество ресурсов, добавьте в интерфейс индикатор хода выполнения. Этот метод также используется для выполнения всех необходимых подготовительных операций, таких как настройка начальных состояний или глобальных значений.

Если вы не знакомы с асинхронным программированием и концепцией параллельного выполнения задач, изучите статью [Асинхронное программирование на языке C++](https://docs.microsoft.com/windows/uwp/threading-async/asynchronous-programming-in-cpp-universal-windows-platform-apps).

### <a name="appload-method"></a>Метод App::Load

Создайте класс __GameMain__, который содержит задачи загрузки.

```cpp
void App::Load(
    Platform::String^ entryPoint
    )
{
        if (!m_main)
    {
        m_main = std::unique_ptr<GameMain>(new GameMain(m_deviceResources));
    }
}
````

### <a name="gamemain-constructor"></a>Конструктор GameMain

* Создайте и инициализируйте отрисовщик игры. Дополнительные сведения см. в разделе [платформу для отображения инструкции: Общие сведения о подготовке к просмотру](tutorial--assembling-the-rendering-pipeline.md).
* Создайте и инициализируйте объект Simple3Dgame. Подробнее: [Определение основного игрового объекта](tutorial--defining-the-main-game-loop.md).    
* Создайте объект управления пользовательским интерфейсом игры и отобразите наложение с информацией об игре — индикатором хода загрузки файлов ресурсов. Подробнее: [Добавление пользовательского интерфейса](tutorial--adding-a-user-interface.md).
* Создайте контроллер, чтобы он мог считывать входные сигналы с контроллера (сенсорного экрана, мыши или беспроводного контроллера XBox). Подробнее: [Добавление элементов управления](tutorial--adding-controls.md).
* После инициализации контроллера мы определили две прямоугольные области в левом нижнем и правом нижнем углах экрана для сенсорных элементов управления перемещением и камерой соответственно. Игрок использует определенный вызовом **SetMoveRect** прямоугольник в левом нижнем углу как виртуальную панель управления для перемещения камеры вперед и назад, а также в стороны. Определенный в результате вызова метода **SetFireRect** прямоугольник в правом нижнем углу используется в качестве виртуальной кнопки для применения боеприпасов.
* Используйте __create_task__ и __create_task::then__ для разбиения загрузки ресурсов на два отдельных этапа. Поскольку доступ к контексту устройства Direct3D 11 ограничен потоком, в котором был создан контекст устройства, тогда как доступ к устройству Direct3D 11 для создания объектов не привязан к потокам, это означает, что задача **CreateGameDeviceResourcesAsync** может выполняться в потоке, отдельном от потока задачи завершения (*FinalizeCreateGameDeviceResources*), которая выполняется в первоначальном потоке. Для загрузки ресурсов уровней используются одинаковые шаблоны с **LoadLevelAsync** и **FinalizeLoadLevel**.

```cpp
GameMain::GameMain(const std::shared_ptr<DX::DeviceResources>& deviceResources) :
    m_deviceResources(deviceResources),
    m_windowClosed(false),
    m_haveFocus(false),
    m_gameInfoOverlayCommand(GameInfoOverlayCommand::None),
    m_visible(true),
    m_loadingCount(0),
    m_updateState(UpdateEngineState::WaitingForResources)
{
    m_deviceResources->RegisterDeviceNotify(this);

    m_renderer = ref new GameRenderer(m_deviceResources);
    m_game = ref new Simple3DGame();

    m_uiControl = m_renderer->GameUIControl();

    m_controller = ref new MoveLookController(CoreWindow::GetForCurrentThread());

    auto bounds = m_deviceResources->GetLogicalSize();

    m_controller->SetMoveRect(
        XMFLOAT2(0.0f, bounds.Height - GameConstants::TouchRectangleSize),
        XMFLOAT2(GameConstants::TouchRectangleSize, bounds.Height)
        );
    m_controller->SetFireRect(
        XMFLOAT2(bounds.Width - GameConstants::TouchRectangleSize, bounds.Height - GameConstants::TouchRectangleSize),
        XMFLOAT2(bounds.Width, bounds.Height)
        );

    SetGameInfoOverlay(GameInfoOverlayState::Loading);
    m_uiControl->SetAction(GameInfoOverlayCommand::None);
    m_uiControl->ShowGameInfoOverlay();

    create_task([this]()
    {
        // Asynchronously initialize the game class and load the renderer device resources.
        // By doing all this asynchronously, the game gets to its main loop more quickly
        // and in parallel all the necessary resources are loaded on other threads.
        m_game->Initialize(m_controller, m_renderer);

        return m_renderer->CreateGameDeviceResourcesAsync(m_game);

    }).then([this]()
    {
        // The finalize code needs to run in the same thread context
        // as the m_renderer object was created because the D3D device context
        // can ONLY be accessed on a single thread.
        m_renderer->FinalizeCreateGameDeviceResources();

        InitializeGameState();

        if (m_updateState == UpdateEngineState::WaitingForResources)
        {
            // In the middle of a game so spin up the async task to load the level.
            return m_game->LoadLevelAsync().then([this]()
            {
                // The m_game object may need to deal with D3D device context work so
                // again the finalize code needs to run in the same thread
                // context as the m_renderer object was created because the D3D
                // device context can ONLY be accessed on a single thread.
                m_game->FinalizeLoadLevel();
                m_game->SetCurrentLevelToSavedState();
                m_updateState = UpdateEngineState::ResourcesLoaded;

            }, task_continuation_context::use_current());
        }
        else
        {
            // The game is not in the middle of a level so there aren't any level
            // resources to load.  Creating a no-op task so that in both cases
            // the same continuation logic is used.
            return create_task([]()
            {
            });
        }
    }, task_continuation_context::use_current()).then([this]()
    {
        // Since Game loading is an async task, the app visual state
        // may be too small or not have focus.  Put the state machine
        // into the correct state to reflect these cases.

        if (m_deviceResources->GetLogicalSize().Width < GameConstants::MinPlayableWidth)
        {
            m_updateStateNext = m_updateState;
            m_updateState = UpdateEngineState::TooSmall;
            m_controller->Active(false);
            m_uiControl->HideGameInfoOverlay();
            m_uiControl->ShowTooSmall();
            m_renderNeeded = true;
        }
        else if (!m_haveFocus)
        {
            m_updateStateNext = m_updateState;
            m_updateState = UpdateEngineState::Deactivated;
            m_controller->Active(false);
            m_uiControl->SetAction(GameInfoOverlayCommand::None);
            m_renderNeeded = true;
        }
    }, task_continuation_context::use_current());
}
```

## <a name="run-method-of-the-view-provider"></a>Метод Run поставщика представлений

Более ранние три метода: __Инициализировать__, __SetWindow__, и __нагрузки__ задали рабочей области. Теперь можно переходить к методу **Run**, т. е. начинать играть! События, используемые этим методом для перехода между игровыми состояниями, отсылаются и обрабатываются. Графика обновляется по мере повтора игрового цикла.

### <a name="apprun"></a>App::Run

Запустите цикл __while__, который заканчивается, когда пользователь закрывает окно игры.

Образец кода переходит в одно из двух состояний автомата состояний модуля игры.
    * __Деактивировать__: Окно игры деактивировано (потеряло фокус) или прикреплено. Когда этот происходит, игра приостанавливает обработку событий и ожидает, когда окно получит фокус или будет откреплено.
    * __TooSmall__: Игра обновляет собственное состояние и отображает графики для отображения.

Когда игра удерживает фокус, необходимо обрабатывать каждое событие в очереди сообщений по мере их поступления, поэтому требуется вызвать [**CoreWindowDispatch.ProcessEvents**](https://docs.microsoft.com/uwp/api/windows.ui.core.coredispatcher.processevents) с помощью параметра **ProcessAllIfPresent**. Другие варианты могут вызвать задержки в обработке событий сообщений, что приведет к потере отклика от игры или медленному отклику на сенсорный ввод, не позволяющему игроку ощутить адекватную реакцию.

Когда игра не отображается, приостановлена или прикреплено, она не должна потреблять ресурсы на отправку сообщений, которые никогда не будут доставлены. В этом случае игра должна использовать метод **ProcessOneAndAllPending**, который находится в состоянии блокировки, пока не получит какое-либо событие, а затем обрабатывает это событие и любые другие события, поступившие в очередь во время обработки первого события. [**ProcessEvents** ](https://docs.microsoft.com/uwp/api/windows.ui.core.coredispatcher.processevents) возвращается сразу после обработки очереди.

```cpp
void App::Run()
{
    m_main->Run();
}

void GameMain::Run()
{
    while (!m_windowClosed)
    {
        if (m_visible)
        {
            switch (m_updateState)
            {
            case UpdateEngineState::Deactivated:
            case UpdateEngineState::TooSmall:
                if (m_updateStateNext == UpdateEngineState::WaitingForResources)
                {
                    WaitingForResourceLoading();
                    m_renderNeeded = true;
                }
                else if (m_updateStateNext == UpdateEngineState::ResourcesLoaded)
                {
                    // In the device lost case, we transition to the final waiting state
                    // and make sure the display is updated.
                    switch (m_pressResult)
                    {
                    case PressResultState::LoadGame:
                        SetGameInfoOverlay(GameInfoOverlayState::GameStats);
                        break;

                    case PressResultState::PlayLevel:
                        SetGameInfoOverlay(GameInfoOverlayState::LevelStart);
                        break;

                    case PressResultState::ContinueLevel:
                        SetGameInfoOverlay(GameInfoOverlayState::Pause);
                        break;
                    }
                    m_updateStateNext = UpdateEngineState::WaitingForPress;
                    m_uiControl->ShowGameInfoOverlay();
                    m_renderNeeded = true;
                }

                if (!m_renderNeeded)
                {
                    // The App is not currently the active window and not in a transient state so just wait for events.
                    CoreWindow::GetForCurrentThread()->Dispatcher->ProcessEvents(CoreProcessEventsOption::ProcessOneAndAllPending);
                    break;
                }
                // otherwise fall through and do normal processing to get the rendering handled.
            default:
                CoreWindow::GetForCurrentThread()->Dispatcher->ProcessEvents(CoreProcessEventsOption::ProcessAllIfPresent);
                Update();
                m_renderer->Render();
                m_deviceResources->Present();
                m_renderNeeded = false;
            }
        }
        else
        {
            CoreWindow::GetForCurrentThread()->Dispatcher->ProcessEvents(CoreProcessEventsOption::ProcessOneAndAllPending);
        }
    }
    m_game->OnSuspending();  // exiting due to window close.  Make sure to save state.
}
```

## <a name="uninitialize-method-of-the-view-provider"></a>Метод Uninitialize поставщика представлений

Когда пользователь в конечном итоге закончит сеанс игры, нужно будет выполнить некоторую очистку. Для этого существует свойство **Uninitialize**.

В Windows 10 при закрытии окна приложения не завершить процесс приложения, но вместо этого он записывает состояние одноэлементный экземпляр приложения в память. Если необходимо, чтобы при освобождении системой этой области памяти выполнялась какая-либо особая операция, включая особое удаление ресурсов, вставьте в этот метод код для такого удаления.

### <a name="app-uninitialize"></a>Приложение:: Uninitialize

```cpp
void App::Uninitialize()
{
}
```

## <a name="tips"></a>Советы

При разработке собственных игр проектируйте код запуска с применением этих методов. Ниже приведен список простых рекомендаций для каждого метода.

-   Метод **Initialize** выделяет память для основных классов и основных обработчиков событий.
-   Метод **SetWindow** применяется для создания главного окна и подключения любых связанных с ним событий.
-   Метод **Load** служит для обработки всех остальных настроек, а также инициализации асинхронного создания объектов и загрузки ресурсов. Если необходимо создавать временные файлы или данные, такие как процедурно-сгенерированные ресурсы, то это также лучше делать на данном этапе.


## <a name="next-steps"></a>Следующие шаги

В этой статье рассматривается базовая структура игры UWP с использованием DirectX. Помните об этих пяти методах, потому что мы будем ссылаться на них в остальных частях этого пошагового руководства. Далее мы подробно рассмотрим управление игровыми состояниями и обработку событий для продолжения игры в статье [Управление потоком игры](tutorial-game-flow-management.md).



