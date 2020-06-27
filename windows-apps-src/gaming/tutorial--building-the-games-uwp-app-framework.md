---
title: Определение структуры приложения для игры UWP
description: Первым шагом в написании кода для игры универсальная платформа Windows (UWP) является создание платформы, которая позволяет объекту приложения взаимодействовать с Windows.
ms.assetid: 7beac1eb-ba3d-e15c-44a1-da2f5a79bb3b
ms.date: 06/24/2020
ms.topic: article
keywords: windows 10, uwp, игры, directx
ms.localizationpriority: medium
ms.openlocfilehash: 5052b4e71196950b6a8a91aaa271b5550fb448b5
ms.sourcegitcommit: 20969781aca50738792631f4b68326f9171a3980
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/26/2020
ms.locfileid: "85409623"
---
#  <a name="define-the-games-uwp-app-framework"></a>Определение структуры приложения для игры UWP

> [!NOTE]
> Эта статья является частью серии руководств по [созданию простой универсальная платформа Windows (UWP) с помощью DirectX](tutorial--create-your-first-uwp-directx-game.md) . В разделе этой ссылки задается контекст для ряда.

Первым шагом в программировании игры универсальная платформа Windows (UWP) является создание платформы, которая позволяет объекту приложения взаимодействовать с окнами, включая среда выполнения Windows такие функции, как приостановка и возобновление обработки событий, изменения фокуса окна и привязка.

## <a name="objectives"></a>Задачи

* Настройте платформу для игры DirectX универсальная платформа Windows (UWP) и реализуйте конечный автомат, который определяет общий поток игр.

> [!NOTE]
> Для работы с этим разделом найдите исходный код для скачанного примера игры [Simple3DGameDX](/samples/microsoft/windows-universal-samples/simple3dgamedx/) .

## <a name="introduction"></a>Введение

В разделе [Настройка игрового проекта](tutorial--setting-up-the-games-infrastructure.md) мы предоставили функцию **wWinMain** , а также интерфейсы [**IFrameworkViewSource**](/uwp/api/windows.applicationmodel.core.iframeworkviewsource) и [**IFrameworkView**](/uwp/api/windows.applicationmodel.core.iframeworkviewsource) . Мы узнали, что класс **приложения** (который можно увидеть в `App.cpp` файле исходного кода в проекте **Simple3DGameDX** ) выступает в качестве фабрики и *представления* *поставщика представления* .

В этом разделе мы расскажу об этом, и подробно узнаете, как класс **приложения** в игре должен реализовывать методы **IFrameworkView**.

## <a name="the-appinitialize-method"></a>Метод App:: Initialize

При запуске приложения первым методом, который вызывает Windows, является наша реализация [**IFrameworkView:: Initialize**](/uwp/api/windows.applicationmodel.core.iframeworkview.initialize).

Ваша реализация должна справляться с наиболее фундаментальным поведением игры UWP, например убедиться, что игра может справиться с паузой (и возможной позже возобновлением) с помощью подписки на эти события. У нас также есть доступ к устройству видеоадаптера, поэтому мы можем создавать графические ресурсы, зависящие от устройства.

```cppwinrt
void Initialize(CoreApplicationView const& applicationView)
{
    applicationView.Activated({ this, &App::OnActivated });

    CoreApplication::Suspending({ this, &App::OnSuspending });

    CoreApplication::Resuming({ this, &App::OnResuming });

    // At this point we have access to the device. 
    // We can create the device-dependent resources.
    m_deviceResources = std::make_shared<DX::DeviceResources>();
}
```

Старайтесь избегать необработанных указателей, когда это возможно (и почти всегда возможно).

- Для среда выполнения Windows типов можно очень часто избегать указателей и просто создать значение в стеке. Если вам нужен указатель, используйте [**WinRT:: com_ptr**](/uwp/cpp-ref-for-winrt/com-ptr) (мы увидим пример в ближайшее время).
- Для уникальных указателей используйте [**std:: unique_ptr**](/cpp/cpp/how-to-create-and-use-unique-ptr-instances) и **std:: make_unique**.
- Для общих указателей используйте [**std:: shared_ptr**](/cpp/cpp/how-to-create-and-use-shared-ptr-instances) и **std:: make_shared**.

## <a name="the-appsetwindow-method"></a>Метод App:: Сетвиндов

После **инициализации**Windows вызывает нашу реализацию [**IFrameworkView:: сетвиндов**](/uwp/api/windows.applicationmodel.core.iframeworkview.setwindow), передавая объект [**CoreWindow**](/uwp/api/windows.ui.core.corewindow) , представляющий главное окно игры.

В **app:: сетвиндов**мы подписались на события, связанные с окнами, и настроим некоторые окна и поведение при отображении. Например, мы создаем указатель мыши (через класс [**корекурсор**](/uwp/api/windows.ui.core.corecursor) ), который может использоваться как элементами управления мыши, так и сенсорным касанием. Также мы передаем объект Window в объект ресурсов, зависимый от устройства.

Дополнительные сведения об обработке событий см. в разделе [Управление потоком игр](tutorial-game-flow-management.md#event-handling) .

```cppwinrt
void SetWindow(CoreWindow const& window)
{
    //CoreWindow window = CoreWindow::GetForCurrentThread();
    window.Activate();

    window.PointerCursor(CoreCursor(CoreCursorType::Arrow, 0));

    PointerVisualizationSettings visualizationSettings{ PointerVisualizationSettings::GetForCurrentView() };
    visualizationSettings.IsContactFeedbackEnabled(false);
    visualizationSettings.IsBarrelButtonFeedbackEnabled(false);

    m_deviceResources->SetWindow(window);

    window.Activated({ this, &App::OnWindowActivationChanged });

    window.SizeChanged({ this, &App::OnWindowSizeChanged });

    window.Closed({ this, &App::OnWindowClosed });

    window.VisibilityChanged({ this, &App::OnVisibilityChanged });

    DisplayInformation currentDisplayInformation{ DisplayInformation::GetForCurrentView() };

    currentDisplayInformation.DpiChanged({ this, &App::OnDpiChanged });

    currentDisplayInformation.OrientationChanged({ this, &App::OnOrientationChanged });

    currentDisplayInformation.StereoEnabledChanged({ this, &App::OnStereoEnabledChanged });

    DisplayInformation::DisplayContentsInvalidated({ this, &App::OnDisplayContentsInvalidated });
}
```

## <a name="the-appload-method"></a>Метод App:: Load

Теперь, когда главное окно задано, вызывается наша реализация [**IFrameworkView:: Load**](/uwp/api/windows.applicationmodel.core.iframeworkview.load) . **Загрузка** — это лучшее место для предварительной выборки данных или ресурсов игр, чем **Инициализация** и **сетвиндов**.

```cppwinrt
void Load(winrt::hstring const& /* entryPoint */)
{
    if (!m_main)
    {
        m_main = winrt::make_self<GameMain>(m_deviceResources);
    }
}
```

Как видите, фактическая работа делегируется конструктору объекта **гамемаин** , который мы сделаем здесь. Класс **гамемаин** определен в `GameMain.h` и `GameMain.cpp` .

### <a name="the-gamemaingamemain-constructor"></a>Конструктор Гамемаин:: Гамемаин

Конструктор **гамемаин** (и другие функции-члены, которые он вызывает) начинает набор асинхронных операций загрузки для создания игровых объектов, загрузки графических ресурсов и инициализации конечного автомата игры. Мы также выполним все необходимые подготовительные действия перед началом игры, например задание любых начальных состояний или глобальных значений.

Windows накладывает ограничение на время, в течение которого ваша игра сможет начать обработку входных данных. Итак, использование АСИК, как мы делаем здесь, означает, что **нагрузка** может быстро возвращаться в фоновом режиме. Если загрузка занимает много времени или при наличии большого количества ресурсов, рекомендуется предоставить пользователям часто обновляемый индикатор выполнения. 

Если вы не знакомы с асинхронным программированием, см. статью [Параллельные и асинхронные операции с C++/WinRT](/windows/uwp/cpp-and-winrt-apis/concurrency).

```cppwinrt
GameMain::GameMain(std::shared_ptr<DX::DeviceResources> const& deviceResources) :
    m_deviceResources(deviceResources),
    m_windowClosed(false),
    m_haveFocus(false),
    m_gameInfoOverlayCommand(GameInfoOverlayCommand::None),
    m_visible(true),
    m_loadingCount(0),
    m_updateState(UpdateEngineState::WaitingForResources)
{
    m_deviceResources->RegisterDeviceNotify(this);

    m_renderer = std::make_shared<GameRenderer>(m_deviceResources);
    m_game = std::make_shared<Simple3DGame>();

    m_uiControl = m_renderer->GameUIControl();

    m_controller = std::make_shared<MoveLookController>(CoreWindow::GetForCurrentThread());

    auto bounds = m_deviceResources->GetLogicalSize();

    m_controller->SetMoveRect(
        XMFLOAT2(0.0f, bounds.Height - GameUIConstants::TouchRectangleSize),
        XMFLOAT2(GameUIConstants::TouchRectangleSize, bounds.Height)
        );
    m_controller->SetFireRect(
        XMFLOAT2(bounds.Width - GameUIConstants::TouchRectangleSize, bounds.Height - GameUIConstants::TouchRectangleSize),
        XMFLOAT2(bounds.Width, bounds.Height)
        );

    SetGameInfoOverlay(GameInfoOverlayState::Loading);
    m_uiControl->SetAction(GameInfoOverlayCommand::None);
    m_uiControl->ShowGameInfoOverlay();

    // Asynchronously initialize the game class and load the renderer device resources.
    // By doing all this asynchronously, the game gets to its main loop more quickly
    // and in parallel all the necessary resources are loaded on other threads.
    ConstructInBackground();
}

winrt::fire_and_forget GameMain::ConstructInBackground()
{
    auto lifetime = get_strong();

    m_game->Initialize(m_controller, m_renderer);

    co_await m_renderer->CreateGameDeviceResourcesAsync(m_game);

    // The finalize code needs to run in the same thread context
    // as the m_renderer object was created because the D3D device context
    // can ONLY be accessed on a single thread.
    // co_await of an IAsyncAction resumes in the same thread context.
    m_renderer->FinalizeCreateGameDeviceResources();

    InitializeGameState();

    if (m_updateState == UpdateEngineState::WaitingForResources)
    {
        // In the middle of a game so spin up the async task to load the level.
        co_await m_game->LoadLevelAsync();

        // The m_game object may need to deal with D3D device context work so
        // again the finalize code needs to run in the same thread
        // context as the m_renderer object was created because the D3D
        // device context can ONLY be accessed on a single thread.
        m_game->FinalizeLoadLevel();
        m_game->SetCurrentLevelToSavedState();
        m_updateState = UpdateEngineState::ResourcesLoaded;
    }
    else
    {
        // The game is not in the middle of a level so there aren't any level
        // resources to load.
    }

    // Since Game loading is an async task, the app visual state
    // may be too small or not have focus. Put the state machine
    // into the correct state to reflect these cases.

    if (m_deviceResources->GetLogicalSize().Width < GameUIConstants::MinPlayableWidth)
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
}

void GameMain::InitializeGameState()
{
    // Set up the initial state machine for handling Game playing state.
    ...
}
```

Ниже приведена структура последовательности действий, которые запускает конструктор.

- Создайте и инициализируйте объект типа **гамерендерер**. Подробнее: [Платформа отрисовки I: введение в отрисовку](tutorial--assembling-the-rendering-pipeline.md).
- Создайте и инициализируйте объект типа **Simple3DGame**. Подробнее: [Определение основного игрового объекта](tutorial--defining-the-main-game-loop.md).
- Создайте объект элемента управления пользовательского интерфейса игры и отобразите наложение сведений о игре, чтобы показать индикатор выполнения по мере загрузки файлов ресурсов. Подробнее: [Добавление пользовательского интерфейса](tutorial--adding-a-user-interface.md).
- Создайте объект контроллера для считывания входных данных из контроллера (сенсорный экран, мышь или беспроводной контроллер Xbox). Подробнее: [Добавление элементов управления](tutorial--adding-controls.md).
- Определите две прямоугольные области в нижнем левом и нижнем правом углу экрана для сенсорных элементов управления перемещения и камеры соответственно. Проигрыватель использует левый нижний прямоугольник (определенный в вызове **сетмоверект**) в качестве виртуальной панели управления для перемещения камеры вперед и назад, а также параллельно с стороной. Правый нижний прямоугольник (определенный методом **сетфирерект** ) используется в качестве виртуальной кнопки для запуска АММО.
- Используйте соподпрограммы для разбиения загрузки ресурсов на отдельные этапы. Доступ к контексту устройства Direct3D ограничивается потоком, в котором был создан контекст устройства; при доступе к устройству Direct3D для создания объектов используется беспотоковая поддержка. Следовательно, соподпрограмма **гамерендерер:: креатегамедевицересаурцесасинк** может выполняться в отдельном потоке от задачи завершения (**Гамерендерер:: финализекреатегамедевицересаурцес**), которая выполняется в исходном потоке.
- Мы используем аналогичный шаблон для загрузки ресурсов уровня с помощью **Simple3DGame:: лоадлевеласинк** и **Simple3DGame:: финализелоадлевел**.

Дополнительные сведения о **гамемаин:: инитиализегаместате** см. в следующем разделе ([Управление игровыми потоком](tutorial-game-flow-management.md)).

## <a name="the-apponactivated-method"></a>Метод App:: OnActivated

Далее возникает событие [**кореаппликатионвиев:: Activated**](/uwp/api/windows.applicationmodel.core.coreapplicationview.activated) . Поэтому вызывается любой обработчик событий **OnActivated** (например, наш метод **app:: OnActivated** ).

```cppwinrt
void OnActivated(CoreApplicationView const& /* applicationView */, IActivatedEventArgs const& /* args */)
{
    CoreWindow window = CoreWindow::GetForCurrentThread();
    window.Activate();
}
```

Единственная работа, которую мы делаем здесь, — активировать основной [**CoreWindow**](/uwp/api/windows.ui.core.corewindow). Кроме того, это можно сделать в **app:: сетвиндов**.

## <a name="the-apprun-method"></a>Метод App:: Run

**Инициализация**, **сетвиндов**и **Загрузка** задают этап. Теперь, когда игра запущена и работает, вызывается наша реализация [**IFrameworkView:: Run**](/uwp/api/windows.applicationmodel.core.iframeworkview.run) .

```cppwinrt
void Run()
{
    m_main->Run();
}
```

Опять же, работа делегируется **гамемаин**.

### <a name="the-gamemainrun-method"></a>Метод Гамемаин:: Run

**Гамемаин:: Run** — это основной цикл игры; его можно найти в `GameMain.cpp` . Основная логика заключается в том, что пока окно игры остается открытым, отправляются все события, обновляете таймер, а затем готовится к просмотру и представляют результаты графического конвейера. Кроме того, события, используемые для перехода между состояниями игры, отправляются и обрабатываются.

Этот код также касается двух состояний в автомате для игровых механизмов.

- **Упдатингинестате::D еактиватед**. Это означает, что окно игры деактивировано (потеряно фокус) или привязано. 
- **Упдатингинестате:: тусмалл**. Это означает, что клиентская область слишком мала для визуализации игры в.

В любом из этих состояний игра приостанавливает обработку событий и ожидает, пока окно не потеряет фокус, не привязывается или не изменит его размер.

Когда игра удерживает фокус, необходимо обрабатывать каждое событие в очереди сообщений по мере их поступления, поэтому требуется вызвать [**CoreWindowDispatch.ProcessEvents**](/uwp/api/windows.ui.core.coredispatcher.processevents) с помощью параметра **ProcessAllIfPresent**. Другие параметры могут вызвать задержки при обработке событий сообщений, что может сделать игру неотвечающей или привести к низкой реакции на поведение сенсорного экрана.

Если игра не отображается, приостановлена или прикреплена, вы не хотите, чтобы она использовала все ресурсы для отправки сообщений, которые никогда не поступают. В этом случае для игры необходимо использовать параметр **процессонеандаллпендинг** . Этот параметр блокируется до тех пор, пока не будет получено событие, а затем обработает это событие (а также все остальные, поступающие в очередь процессов во время обработки первой). **Коревиндовдиспатч. ProcessEvents** затем немедленно возвращается после обработки очереди.

```cppwinrt
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
                    CoreWindow::GetForCurrentThread().Dispatcher().ProcessEvents(CoreProcessEventsOption::ProcessOneAndAllPending);
                    break;
                }
                // otherwise fall through and do normal processing to get the rendering handled.
            default:
                CoreWindow::GetForCurrentThread().Dispatcher().ProcessEvents(CoreProcessEventsOption::ProcessAllIfPresent);
                Update();
                m_renderer->Render();
                m_deviceResources->Present();
                m_renderNeeded = false;
            }
        }
        else
        {
            CoreWindow::GetForCurrentThread().Dispatcher().ProcessEvents(CoreProcessEventsOption::ProcessOneAndAllPending);
        }
    }
    m_game->OnSuspending();  // Exiting due to window close, so save state.
}
```

## <a name="the-appuninitialize-method"></a>Метод App:: Uninitialize

Когда игра завершается, вызывается наша реализация [**IFrameworkView:: Uninitialize**](/uwp/api/windows.applicationmodel.core.iframeworkview.uninitialize) . Это наша возможность выполнить очистку. Закрытие окна приложения не приводит к завершению процесса приложения. Вместо этого он записывает состояние одноэлементного приложения в память. Если при освобождении памяти системой потребуется нечто специальное, в том числе все специальные ресурсы, а затем поставьте код для этой очистки при **неинициализации**.

В нашем случае **Приложение:: Uninitialize** — это No-Op.

```cpp
void Uninitialize()
{
}
```

## <a name="tips"></a>"Советы"

При разработке собственной игры Разрабатывайте код запуска вокруг методов, описанных в этом разделе. Ниже приведен простой список основных рекомендаций для каждого метода.

- Используйте **инициализацию** для выделения основных классов и подключения основных обработчиков событий.
- Используйте **сетвиндов** , чтобы подписываться на любые события, относящиеся к окну, а также для передачи главного окна в объект ресурсов, зависящих от устройства, чтобы он мог использовать это окно при создании цепочки буферов.
- Используйте **загрузку** для выполнения оставшихся настроек, а также для инициации асинхронного создания объектов и загрузки ресурсов. Если необходимо создать временные файлы или данные, такие как процедурно сформированные ресурсы, это также можно сделать здесь.

## <a name="next-steps"></a>Следующие шаги

В этом разделе рассматривается базовая структура игры UWP, в которой используется DirectX. Рекомендуется помнить об этих методах, так как мы будем ссылаться на некоторые из них в следующих разделах.

В следующем разделе &mdash; [Управление потоком игр](tutorial-game-flow-management.md)мы подробно рассмотрим, &mdash; как управлять состояниями игры и обработкой событий, чтобы избежать поступающих игр.
