---
title: Определение структуры игрового приложения универсальной платформы для Windows (UWP)
description: Первый этап программирования игры универсальной платформы Windows (UWP) на базе DirectX заключается в создании структуры, которая позволит объекту игры взаимодействовать с Windows.
ms.assetid: 7beac1eb-ba3d-e15c-44a1-da2f5a79bb3b
---

#  Определение структуры игрового приложения универсальной платформы для Windows (UWP)


\[ Обновлено для приложений UWP в Windows 10. Статьи о Windows 8.x см. в [архиве](http://go.microsoft.com/fwlink/p/?linkid=619132) \]

Первый этап программирования игры универсальной платформы Windows (UWP) на базе DirectX заключается в создании структуры, которая позволит объекту игры взаимодействовать с Windows. Сюда входят такие свойства среды выполнения Windows, как приостановка и возобновление обработки событий, фокус и прикрепление окна, а также события, взаимодействия и переходы для пользовательского интерфейса. Мы рассмотрим на образце, как структурирована игра и как она определяет конечный автомат высокого уровня для взаимодействия игрока и системы.

## Цель


-   Настроить структуру для игры UWP на базе DirectX и реализовать конечный автомат, определяющий общий процесс игры.

## Инициализация и запуск поставщика представлений


В игре UWP на базе DirectX необходимо получить поставщик представлений, который singleton-объект приложения — объект среды выполнения Windows, определяющий экземпляр работающего приложения, — может использовать для доступа к необходимым графическим ресурсам. Благодаря среде выполнения Windows приложение напрямую подключается к графическому интерфейсу, однако следует указать необходимые ресурсы и порядок их обработки.

В разделе [Настройка проекта игры](tutorial--setting-up-the-games-infrastructure.md) уже говорилось о том, что Microsoft Visual Studio 2015 содержит реализацию базового обработчика для DirectX в файле **Sample3DSceneRenderer.cpp**, который становится доступным после выбора шаблона **Приложение DirectX 11 (универсальные приложения для Windows)**.

Подробнее о понятии и создании поставщика представлений и обработчика см. в разделе [Настройка приложения UWP на C++ и DirectX для отображения представления DirectX](https://msdn.microsoft.com/library/windows/apps/hh465077).

В целом нам потребуется реализовать пять методов, которые вызываются singleton-объектом приложения:

-   [**Initialize**](https://msdn.microsoft.com/library/windows/apps/hh700495)
-   [**SetWindow**](https://msdn.microsoft.com/library/windows/apps/hh700509)
-   [**Load**](https://msdn.microsoft.com/library/windows/apps/hh700501)
-   [**Run**](https://msdn.microsoft.com/library/windows/apps/hh700505)
-   [**Uninitialize**](https://msdn.microsoft.com/library/windows/apps/hh700523)

В шаблоне приложения DirectX 11 (универсальные приложения для Windows) эти 5 методов определены на объекте **App** в [App.h](#code_sample). Рассмотрим способ их реализации в этой игре.

Метод Initialize поставщика представлений

```cpp
void App::Initialize(
    _In_ CoreApplicationView^ applicationView
    )
{
    applicationView->Activated +=
        ref new TypedEventHandler<CoreApplicationView^, IActivatedEventArgs^>(this, &App::OnActivated);

    CoreApplication::Suspending +=
        ref new EventHandler<SuspendingEventArgs^>(this, &App::OnSuspending);

    CoreApplication::Resuming +=
        ref new EventHandler<Platform::Object^>(this, &App::OnResuming);

    m_controller = ref new MoveLookController();
    m_renderer = ref new GameRenderer();
    m_game = ref new Simple3DGame();
}
```

Сначала singleton-объект приложения вызывает метод **Initialize**. Поэтому очень важно, чтобы этот метод обрабатывал большинство основных вариантов поведения игры UWP, таких как активация главного окна и внезапная приостановка (с последующим возобновлением).

При инициализации игровое приложение выделяет определенный объем памяти для контроллера, что позволяет играющему начать ввод. Кроме того, оно создает новые, неинициализированные экземпляры обработчика игры и конечного автомата. Подробнее об этом см. в разделе [Определение главного объекта игры](tutorial--defining-the-main-game-loop.md).

К этому моменту игровое приложение может обрабатывать сообщение о приостановке (или возобновлении), а для контроллера, обработчика и самой игры выделена память, но отсутствует окно, с которым можно работать, и игра не инициализирована. Необходимо выполнить еще ряд операций!

Метод SetWindow поставщика представлений

```cpp
void App::SetWindow(
    _In_ CoreWindow^ window
    )
{
    window->PointerCursor = ref new CoreCursor(CoreCursorType::Arrow, 0);

    window->SizeChanged +=
        ref new TypedEventHandler<CoreWindow^, WindowSizeChangedEventArgs^>(this, &App::OnWindowSizeChanged);

    window->Closed +=
        ref new TypedEventHandler<CoreWindow^, CoreWindowEventArgs^>(this, &App::OnWindowClosed);

    window->VisibilityChanged +=
        ref new TypedEventHandler<CoreWindow^, VisibilityChangedEventArgs^>(this, &App::OnVisibilityChanged);

    DisplayProperties::LogicalDpiChanged +=
        ref new DisplayPropertiesEventHandler(this, &App::OnLogicalDpiChanged);

    m_controller->Initialize(window);

    m_controller->SetMoveRect(
        XMFLOAT2(0.0f, window->Bounds.Height - GameConstants::TouchRectangleSize),
        XMFLOAT2(GameConstants::TouchRectangleSize, window->Bounds.Height)
        );
    m_controller->SetFireRect(
        XMFLOAT2(window->Bounds.Width - GameConstants::TouchRectangleSize, window->Bounds.Height - GameConstants::TouchRectangleSize),
        XMFLOAT2(window->Bounds.Width, window->Bounds.Height)
        );

    m_renderer->Initialize(window, DisplayProperties::LogicalDpi);
    SetGameInfoOverlay(GameInfoOverlayState::Loading);
    ShowGameInfoOverlay();
}
```

При вызове реализации [**SetWindow**](https://msdn.microsoft.com/library/windows/apps/hh700509) singleton-объект приложения предоставляет объект [**CoreWindow**](https://msdn.microsoft.com/library/windows/apps/br208225), который отображает главное окно игры и открывает доступ к ее ресурсам и событиям. Теперь, благодаря тому, что появилось рабочее окно, игра может начать добавлять основные компоненты и события интерфейса пользователя: указатель (используется как для мыши, так и для сенсорных элементов управления) и основные события для изменения размера и закрытия окна, а также для изменений DPI (при изменениях устройства отображения).

Игровое приложение также инициализирует контроллер и сам объект игры, так как уже есть окно для взаимодействия с ним. Приложение может считывать входные сигналы с контроллера (сенсорных элементов управления, мыши или контроллера XBox 360).

После инициализации контроллера приложение определяет две прямоугольных области в левом нижнем и правом нижнем углах экрана для сенсорных элементов управления перемещением и камерой соответственно. Игрок использует определенный вызовом **SetMoveRect** прямоугольник в левом нижнем углу как виртуальную панель управления для перемещения камеры вперед и назад, а также в стороны. Определенный в результате вызова метода **SetFireRect** прямоугольник в правом нижнем углу используется в качестве виртуальной кнопки для применения боеприпасов.

Все компоненты начинают работать вместе.

Метод Load поставщика представлений

```cpp
void App::Load(
    Platform::String^ entryPoint
    )
{
    task<void>([this]()
    {
        m_game->Initialize(m_controller, m_renderer);

        return m_renderer->CreateGameDeviceResourcesAsync(m_game);

    }).then([this]()
    {
        // The finalize code needs to run in the same thread context
        // in which the m_renderer object was created because the D3D device context
        // can be accessed only on a single thread.
        m_renderer->FinalizeCreateGameDeviceResources();

        InitializeGameState();

        if (m_updateState == UpdateEngineState::WaitingForResources)
        {
            // In the middle of a game, so spin up the async task to load the level.
            create_task([this]()
            {
                return m_game->LoadLevelAsync();

            }).then([this]()
            {
                // The m_game object may need to deal with D3D device context work, so
                // the finalizer code needs to run in the same thread
                // context as the m_renderer object was created because the D3D 
                // device context can  be accessed only on a single thread.
                m_game->FinalizeLoadLevel();
                m_updateState = UpdateEngineState::ResourcesLoaded;

            }, task_continuation_context::use_current());
        }
    }, task_continuation_context::use_current());
}
```

После настройки главного окна singleton-объект приложения вызывает **Load**. В примере этот метод использует набор асинхронных задач (синтаксис которых определен в [Библиотеке параллельных шаблонов](https://msdn.microsoft.com/library/windows/apps/dd492418.aspx)) для создания объектов игры, загрузки графических ресурсов и инициализации конечного автомата игры. При помощи шаблона асинхронной задачи метод Load быстро завершает работу и позволяет приложению начать обработку ввода. Кроме того, в этом методе приложение выводит индикатор выполнения, отображающий ход загрузки файлов ресурсов.

Мы разбиваем загрузку ресурсов на две отдельные стадии, потому что доступ к контексту устройства Direct3D 11 ограничен потоком, в котором был создан контекст этого устройства, тогда как доступ к устройству Direct3D 11 для создания объектов не ограничен каким-либо определенным потоком. Задача **CreateGameDeviceResourcesAsync** выполняется в другом потоке, отдельно от задачи завершения (*FinalizeCreateGameDeviceResources*), которая выполняется в исходном потоке. Для загрузки ресурсов уровней используются одинаковые шаблоны с **LoadLevelAsync** и **FinalizeLoadLevel**.

После создания объектов игры и загрузки графических ресурсов мы инициализируем конечный автомат игры для исходных условий (пример: установка исходного количества боеприпасов, номера уровня и положения объекта). Если состояние игры показывает, что играющий возобновляет игру, мы загружаем текущий уровень (уровень, на котором находился игрок в момент приостановки игры).

В методе **Load** до начала игры выполняются все необходимые подготовительные операции, такие как настройка любых начальных состояний или глобальных значений. Если необходимо предварительно получить данные или ресурсы игры, лучше сделать это на данном этапе, а не на этапе **SetWindow** или **Initialize**. Используйте в вашей игре асинхронные задачи для любых загрузок, так как Windows выделяет игре ограниченное время на запуск обработки ввода. Если загрузка игры продолжается довольно долго из-за большого количества ресурсов, добавьте в интерфейс индикатор хода выполнения.

При разработке собственных игр проектируйте код запуска с применением этих методов. Ниже приведен список простых рекомендаций для каждого метода.

-   Метод **Initialize** выделяет память для основных классов и основных обработчиков событий.
-   Метод **SetWindow** применяется для создания главного окна и подключения любых связанных с ним событий.
-   Метод **Load** служит для обработки всех остальных настроек, а также инициализации асинхронного создания объектов и загрузки ресурсов. Если необходимо создавать временные файлы или данные, такие как процедурно-сгенерированные ресурсы, то это также лучше делать на данном этапе.

Таким образом, образец игры создает экземпляр конечного автомата игры, настраивает его конфигурацию запуска и обрабатывает все системные события и события ввода. Игра открывает окно для отображения содержимого. Теперь код сценария игры готов к запуску.

Метод Run поставщика представлений

```cpp
void App::Run()
{
    while (!m_windowClosed)
    {
        if (m_visible)
        {
            switch (m_updateState)
            {
            case UpdateEngineState::Deactivated:
            case UpdateEngineState::Snapped:
                if (!m_renderNeeded)
                {
                    // The app is not currently the active window, so just wait for events.
                    CoreWindow::GetForCurrentThread()->Dispatcher->ProcessEvents(CoreProcessEventsOption::ProcessOneAndAllPending);
                    break;
                }
                // Otherwise, fall through and do normal processing to get the rendering handled.
            default:
                CoreWindow::GetForCurrentThread()->Dispatcher->ProcessEvents(CoreProcessEventsOption::ProcessAllIfPresent);
                Update();
                m_renderer->Render();
                m_renderNeeded = false;
            }
        }
        else
        {
            CoreWindow::GetForCurrentThread()->Dispatcher->ProcessEvents(CoreProcessEventsOption::ProcessOneAndAllPending);
        }
    }
    m_game->OnSuspending();  // Exiting due to window close.  Make sure to save the state.
}
```

Этот метод относится к игровой части приложения. Выполнив эти три метода и подготовив все для игры, игровое приложение запускает метод **Run**, чтобы начать игру.

В образце игры мы запускаем цикл while, который заканчивается, когда пользователь закрывает окно игры. Образец кода переходит в одно из двух состояний автомата состояний модуля игры.

-   Окно игры деактивировано (потеряло фокус) или прикреплено. Когда этот происходит, игра приостанавливает обработку событий и ожидает, когда окно получит фокус или будет откреплено.
-   В противном случае игра обновляет собственное состояние и обрабатывает графику для отображения.

Когда игра удерживает фокус, необходимо обрабатывать каждое событие в очереди сообщений по мере их поступления, поэтому требуется вызвать [**CoreWindowDispatch.ProcessEvents**](https://msdn.microsoft.com/library/windows/apps/br208215) с помощью параметра **ProcessAllIfPresent**. Другие параметры могут вызвать задержки в обработке событий сообщений, что приведет к потере отклика от игры или медленному отклику на сенсорный ввод без адекватной реакции.

Конечно, когда приложение не отображается, приостановлено или прикреплено, оно не должно потреблять ресурсы на обработку сообщений, которые никогда не будут доставлены. Поэтому ваша игра должна использовать метод **ProcessOneAndAllPending**, который находится в состоянии блокировки, пока не получит какое-либо событие, а затем обрабатывает это событие и любые другие события, которые поступают в очередь при обработке первого события. [
							После обработки очереди сразу же возвращается **ProcessEvents**](https://msdn.microsoft.com/library/windows/apps/br208215).

Игра работает! События, используемые приложением для перехода между игровыми состояниями, отсылаются и обрабатываются. Графические изображения обновляются по мере повтора циклов игры. Надеемся, игрок хорошо проводит время. Но все хорошее рано или поздно заканчивается...

...и тогда нам потребуется выполнить окончательную уборку. Для этого существует свойство **Uninitialize**.

Метод Uninitialize поставщика представлений

```cpp
void App::Uninitialize()
{
}
```

В образце игры singleton-объект приложения очистил все после завершения игры. В Windows 10 закрытие окна приложения не приводит к полному прекращению процесса приложения. Вместо этого состояние singleton-объекта приложения записывается в память. Если необходимо, чтобы при обращении системы к этой области памяти выполнялась какая-либо специальная операция, например специальная очистка ресурсов, то вставьте в этот метод код очистки.

В ходе урока мы будет возвращаться к этим пяти методам, поэтому не забывайте о них. Теперь давайте рассмотрим общую структуру игрового модуля и определяющий ее конечный автомат.

## Инициализация состояния игрового модуля


Поскольку пользователь может в любое время возобновить выполнение игрового приложения UWP из состояния приостановки, приложение может иметь любое число возможных состояний.

Образец игры при запуске может находиться в одном из трех состояний.

-   Цикл игры выполнялся и находится на середине уровня.
-   Цикл игры не выполнялся, поскольку игра недавно была завершена. (Получен высокий результат.)
-   Игра не была начата или игра находится между уровнями. (Максимальный результат: 0).

Очевидно, что количество состояний в вашей игре может быть другим. Как всегда, не следует забывать, что ваша игра UWP должна поддерживать приостановку и последующее возобновление с того же места.

В образце игры поток выполнения выглядит, как показано ниже.

```cpp
void App::InitializeGameState()
{
    //
    // Set up the initial state machine for handling game playing state.
    //
    if (m_game->GameActive() && m_game->LevelActive())
    {
        m_updateState = UpdateEngineState::WaitingForResources;
        // ...

    }
    else if (!m_game->GameActive() && (m_game->HighScore().totalHits > 0))
    {
        m_updateState = UpdateEngineState::WaitingForPress;
        // ...
    }
    else
    {
        m_updateState = UpdateEngineState::WaitingForResources;
        // ...
    }
    SetAction(GameInfoOverlayCommand::PleaseWait);
    ShowGameInfoOverlay();
}
```

Инициализация в меньшей степени относится к "холодному старту" приложения и в большей степени — к перезапуску приложения после прерывания. Образец игры всегда сохраняет состояние; это создает впечатление, что приложение постоянно выполняется. В состоянии приостановки приостанавливается только ход игры, а ресурсы игры по-прежнему остаются в памяти. Соответственно, событие возобновления указывает, что образец игры возобновляет работу с того места, на котором она была приостановлена или прервана. Когда образец игры перезапускается после остановки, он запускается в обычном режиме и ищет последнее известное состояние, чтобы игрок мог сразу же продолжить игру.

На блок-схеме показаны начальные состояния и переходы для процесса инициализации образца игры.

![Процесс инициализации и подготовки игры до запуска главного цикла](images/simple3dgame-appstartup.png)

В зависимости от состояния игроку предоставляются разные возможности. Если игра возобновляется на середине уровня, она отображается на паузе и в диалоговом окне предоставляется возможность продолжения. Если игра возобновляется в состоянии, в котором игра завершена, отображаются лучшие результаты и на экране предоставляется возможность начать новую игру. Наконец, если игра возобновляется перед началом прохождения уровня, пользователю в наложении предоставляется возможность начать.

Образец игры не различает состояния "холодного старта", т. е. первого запуска игры без события приостановки, и возобновления игры из приостановленного состояния. Такая модель подходит для любого приложения UWP.

## Обработка событий


Наш пример кода зарегистрировал в **Initialize**, **SetWindow** и **Load** несколько обработчиков специальных событий. Как вы уже, вероятно, догадались, эти события были важными, так как пример кода выполнил эти задачи задолго до того, как запустил какие-либо механизмы игры или начал развертывание графического изображения. Вы правы! Эти события являются обязательными для правильной работы приложения UWP, так как приложение может быть активировано, деактивировано, может быть изменен его размер, приложение может быть прикреплено и откреплено, приостановлено или возобновлено в любое время. Следовательно, игра должна регистрировать такие события максимально быстро и обрабатывать их так, чтобы игровой процесс протекал для игрока плавно и предсказуемо.

Ниже приведены обработчики, используемые в образце игры, и события, которые они обрабатывают. Полный код этих обработчиков событий приведен в разделе [Полный образец кода для этого раздела](#code_sample)

<table>
<colgroup>
<col width="50%" />
<col width="50%" />
</colgroup>
<thead>
<tr class="header">
<th align="left">Обработчик событий</th>
<th align="left">Описание</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left">OnActivated</td>
<td align="left">Обрабатывает [<strong>CoreApplicationView::Activated</strong>](https://msdn.microsoft.com/library/windows/apps/br225018). Игровое приложение было выведено на передний план, поэтому активировано главное окно.</td>
</tr>
<tr class="even">
<td align="left">OnLogicalDpiChanged</td>
<td align="left">Обрабатывает [<strong>DisplayProperties::LogicalDpiChanged</strong>](https://msdn.microsoft.com/library/windows/apps/br226150). Разрешение для главного окна игры было изменено, и игровое приложение соответствующим образом перераспределяет ресурсы.
<div class="alert">
<strong>Примечание.</strong>  Координаты [<strong>CoreWindow</strong>](https://msdn.microsoft.com/library/windows/desktop/hh404559) задаются в формате DIP (аппаратно-независимые пиксели), как в [Direct2D](https://msdn.microsoft.com/library/windows/desktop/dd370987). В результате необходимо передать в Direct2D уведомление об изменениях разрешения для правильного отображения любых двумерных ресурсов или примитивов.
</div>
<div>
 
</div></td>
</tr>
<tr class="odd">
<td align="left">OnResuming</td>
<td align="left">Обрабатывает [<strong>CoreApplication::Resuming</strong>](https://msdn.microsoft.com/library/windows/apps/br205859). Игровое приложение восстанавливает игру из состояния приостановки.</td>
</tr>
<tr class="even">
<td align="left">OnSuspending</td>
<td align="left">Обрабатывает [<strong>CoreApplication::Suspending</strong>](https://msdn.microsoft.com/library/windows/apps/br205860). Игровое приложение сохраняет свое состояние на диск. У него есть 5 секунд, чтобы сохранить состояние в хранилище.</td>
</tr>
<tr class="odd">
<td align="left">OnVisibilityChanged</td>
<td align="left">Обрабатывает [<strong>CoreWindow::VisibilityChanged</strong>](https://msdn.microsoft.com/library/windows/apps/hh701591). Игровое приложение изменило видимость и стало видимым или невидимым из-за того, что отобразилось другое приложение.</td>
</tr>
<tr class="even">
<td align="left">OnWindowActivationChanged</td>
<td align="left">Обрабатывает [<strong>CoreWindow::Activated</strong>](https://msdn.microsoft.com/library/windows/apps/br208255). Главное окно игрового приложения было деактивировано или активировано, поэтому оно должно удалить фокус и приостановить игру или восстановить фокус. В обоих случаях наложение показывает, что игра приостановлена.</td>
</tr>
<tr class="odd">
<td align="left">OnWindowClosed</td>
<td align="left">Обрабатывает [<strong>CoreWindow::Closed</strong>](https://msdn.microsoft.com/library/windows/apps/br208261). Игровое приложение закрывает главное окно и приостанавливает игру.</td>
</tr>
<tr class="even">
<td align="left">OnWindowSizeChanged</td>
<td align="left">Обрабатывает [<strong>CoreWindow::SizeChanged</strong>](https://msdn.microsoft.com/library/windows/apps/br208283). Игровое приложение перераспределяет графические ресурсы и наложение, чтобы подстроиться под изменение размера, а затем обновляет целевой объект прорисовки.</td>
</tr>
</tbody>
</table>

 

Ваша игра должна обрабатывать эти события, так как они являются частью схемы приложения UWP.

## Обновление игрового модуля


В этом образце в методе **Run** цикла игры реализован базовый конечный автомат, обрабатывающий все основные действия, которые может выполнять играющий. Самый высокий уровень этого конечного автомата обрабатывает загрузку игры, игру на конкретном уровне или продолжение игры на том или ином уровне после приостановки игры (системой или играющим).

В образце игры имеются три основных состояния (UpdateEngineState), в которых может находиться игра:

-   **Waiting for resources**. Игровой цикл повторяется из-за невозможности перехода до тех пор, пока не появится доступ к ресурсам (в данном случае — к графическим). Когда выполнение асинхронных задач загрузки ресурсов завершается, он изменяет состояние на **ResourcesLoaded**. Обычно это происходит при переходах между уровнями, когда требуется загрузить с диска ресурсы для нового уровня. В примере игры такое поведение имитируется, поскольку здесь не требуется загружать какие-либо ресурсы.
-   **Waiting for press**. Игровой цикл повторяется в ожидании конкретного ввода от пользователя. Ввод представляет действие пользователя для загрузки игры, начала прохождения уровня или продолжения уровня. Образец игры ссылается на эти промежуточные состояния как на перечисляемые значения PressResultState.
-   **Dynamics**. Игровой цикл выполняется с участием пользователя. Когда пользователь играет, игра проверяет три условия, в которые она может перейти: истечение установленного времени для уровня, завершение уровня пользователем или завершение всех уровней пользователем.

Ниже приведена структура кода. Полный код приведен в разделе [Полный пример кода для этого раздела](#code_sample).

Структура конечного автомата, использующегося для обновления игрового модуля

```cpp
void App::Update()
{
    m_controller->Update();

    switch (m_updateState)
    {
    case UpdateEngineState::WaitingForResources:
        // Waiting for initial load.  Display an update once per 60 updates.
        loadCount++;
        if ((loadCount % 60) == 0)
        {
            m_loadingCount++;
            SetGameInfoOverlay(m_gameInfoOverlayState);
        }
        break;

    case UpdateEngineState::ResourcesLoaded:
        switch (m_pressResult)
        {
        case PressResultState::LoadGame:
            // ...
            break;

        case PressResultState::PlayLevel:
            // ...
            break;

        case PressResultState::ContinueLevel:
            // ...
            break;
        }
        // ...
        break;

    case UpdateEngineState::WaitingForPress:
        if (m_controller->IsPressComplete() || m_pressComplete)
        {
            m_pressComplete = false;

            switch (m_pressResult)
            {
            case PressResultState::LoadGame:
                // ...
                break;

            case PressResultState::PlayLevel:
                // ...
                break;

            case PressResultState::ContinueLevel:
                // ...
                break;
            }
        }
        break;

    case UpdateEngineState::Dynamics:
        if (m_controller->IsPauseRequested() || m_pauseRequested)
        {
            // ...
        }
        else 
        {
            GameState runState = m_game->RunGame();
            switch (runState)
            {
            case GameState::TimeExpired:
                // ...
                break;

            case GameState::LevelComplete:
                // ...
                break;

            case GameState::GameComplete:
                // ...
                break;
            }
        }

        if (m_updateState == UpdateEngineState::WaitingForPress)
        {
            // transitioning state, so enable waiting for the press event
            m_controller->WaitForPress(m_game->GameInfoOverlayUpperLeft(), m_game->GameInfoOverlayLowerRight());
        }
        if (m_updateState == UpdateEngineState::WaitingForResources)
        {
            // Transitioning state, so shut down the input controller until resources are loaded
            m_controller->Active(false);
        }

        break;
    }
}
```

Наглядное представление главного конечного автомата игры приведено ниже:

![Главный конечный автомат для игры](images/simple3dgame-mainstatemachine.png)

Подробнее сама логика игры описана в разделе [Определение главного объекта игры](tutorial--defining-the-main-game-loop.md). На данный момент важно воспринимать игру как конечный автомат. Каждое отдельное состояние имеет специальные критерии, которые определяют его, и переходы из одного состояния в другое должны основываться на дискретном вводе пользователя или действиях системы (таких как загрузка графических ресурсов). При планировании игры составьте схему, аналогичную используемой нами, обязательно указав в ней все возможные действия, которые пользователь или система могут выполнить на высоком уровне. Игры могут быть очень сложными, и конечный автомат является мощным инструментом для визуализации составных процессов и облегчения управления ими.

Как было показано, конечные автоматы существуют в самих конечных автоматах. Существует конечный автомат для контроллера, обрабатывающий все допустимые вводы, которые могут быть сгенерированы пользователем. Как видно из схемы, нажатие — это определенная форма ввода пользователя. Конечному автомату все равно, что это такое, так как он работает на высоком уровне и считает, что конечный автомат контроллера обработает любые переходы, влияющие на режимы перемещения и стрельбы, а также связанные с ними обновления визуализации. Управление состояниями ввода обсуждается в разделе [Добавление элементов управления](tutorial--adding-controls.md).

## Обновление пользовательского интерфейса


Чтобы пользователь мог изменять состояние высокого уровня в соответствии с правилами игры, его необходимо информировать о состоянии системы. В большинстве игр, включая описываемый образец игры, это осуществляется при помощи индикаторной панели, которая содержит сведения о состоянии игры и другие относящиеся к игре данные, такие как количество очков или боеприпасов, количество оставшихся попыток. Мы называем это наложением, так как визуализация панели выполняется отдельно от основного графического конвейера и она размещается поверх трехмерной проекции. В образце игры мы создали такое наложение при помощи интерфейсов API Direct2D. Это наложение также можно создать с использованием XAML, и такой процесс разъясняется в разделе [Расширение образца игры](tutorial-resources.md).

Пользовательский интерфейс имеет два компонента:

-   индикаторную панель, показывающую количество очков и содержащую сведения о текущем состоянии игрового процесса;
-   точечный рисунок паузы, представляющий собой черный прямоугольник с текстом, накладываемый в состоянии паузы/приостановки игры. Это наложение в игре описано ниже в разделе [Добавление пользовательского интерфейса](tutorial--adding-a-user-interface.md).

Неудивительно, что наложение также снабжено конечным автоматом и может отображать сообщение о начале уровня или завершении игры. В сущности оно представляет собой полотно для вывода различной информации о состоянии игры, отображаемой для играющего в случае паузы в игре или приостановки.

Порядок структурирования образцом игры конечного автомата наложения приведен ниже.

```cpp
void App::SetGameInfoOverlay(GameInfoOverlayState state)
{
    m_gameInfoOverlayState = state;
    switch (state)
    {

    case GameInfoOverlayState::Loading:
        m_renderer->InfoOverlay()->SetGameLoading(m_loadingCount);
        break;

    case GameInfoOverlayState::GameStats:
        // ...
        break;

    case GameInfoOverlayState::LevelStart:
        // ...
        break;

    case GameInfoOverlayState::GameOverCompleted:
        // ...
        break;

    case GameInfoOverlayState::GameOverExpired:
        // ...
        break;

    case GameInfoOverlayState::Pause:
        // ...
        break;
    }
}
```

В зависимости от состояния самой игры наложение отображает пять экранов: экран загрузки ресурсов в начале игры, экран хода игры, экран сообщения о начале уровня, экран завершения игры при прохождении всех уровней до истечения времени, экран завершения игры при истечении времени и экран меню паузы.

Отделение пользовательского интерфейса от графического конвейера игры позволяет работать над ним независимо от модуля графической визуализации игры и значительно снижает сложность кода игры.

## Следующие шаги


Мы рассмотрели базовую структуру образца игры и соответствующую модель для разработки игрового приложения UWP с использованием DirectX. Конечно, об этом можно написать гораздо больше. Мы создали только общую структуру игры. Теперь необходимо подробно рассмотреть игру и ее механизмы, а также те механизмы, что реализованы в качестве основного объекта игры. Эта часть рассматривается в разделе [Определение главного объекта игры](tutorial--defining-the-main-game-loop.md).

Также необходимо подробно рассмотреть графический модуль образца игры. Эта часть рассматривается в разделе [Сборка конвейера визуализации](tutorial--assembling-the-rendering-pipeline.md).

## Полный пример кода для этого раздела


App.h

```cpp
///// THIS CODE AND INFORMATION IS PROVIDED "AS IS" WITHOUT WARRANTY OF
//// ANY KIND, EITHER EXPRESSED OR IMPLIED, INCLUDING BUT NOT LIMITED TO
//// THE IMPLIED WARRANTIES OF MERCHANTABILITY AND/OR FITNESS FOR A
//// PARTICULAR PURPOSE.
////
//// Copyright (c) Microsoft Corporation. All rights reserved

#pragma once

#include "Simple3DGame.h"

enum class UpdateEngineState
{
    WaitingForResources,
    ResourcesLoaded,
    WaitingForPress,
    Dynamics,
    Snapped,
    Suspended,
    Deactivated,
};

enum class PressResultState
{
    LoadGame,
    PlayLevel,
    ContinueLevel,
};

enum class GameInfoOverlayState
{
    Loading,
    GameStats,
    GameOverExpired,
    GameOverCompleted,
    LevelStart,
    Pause,
};

ref class App : public Windows::ApplicationModel::Core::IFrameworkView
{
internal:
    App();

public:
    // IFrameworkView Methods
    virtual void Initialize(_In_ Windows::ApplicationModel::Core::CoreApplicationView^ applicationView);
    virtual void SetWindow(_In_ Windows::UI::Core::CoreWindow^ window);
    virtual void Load(_In_ Platform::String^ entryPoint);
    virtual void Run();
    virtual void Uninitialize();

private:
    void InitializeGameState();

    // Event Handlers
    void OnSuspending(
        _In_ Platform::Object^ sender,
        _In_ Windows::ApplicationModel::SuspendingEventArgs^ args
        );

    void OnResuming(
        _In_ Platform::Object^ sender,
        _In_ Platform::Object^ args
        );

    void UpdateViewState();

    void OnWindowActivationChanged(
        _In_ Windows::UI::Core::CoreWindow^ sender,
        _In_ Windows::UI::Core::WindowActivatedEventArgs^ args
        );

    void OnWindowSizeChanged(
        _In_ Windows::UI::Core::CoreWindow^ sender,
        _In_ Windows::UI::Core::WindowSizeChangedEventArgs^ args
        );

    void OnWindowClosed(
        _In_ Windows::UI::Core::CoreWindow^ sender,
        _In_ Windows::UI::Core::CoreWindowEventArgs^ args
        );

    void OnLogicalDpiChanged(
        _In_ Platform::Object^ sender
        );

    void OnActivated(
        _In_ Windows::ApplicationModel::Core::CoreApplicationView^ applicationView,
        _In_ Windows::ApplicationModel::Activation::IActivatedEventArgs^ args
        );

    void OnVisibilityChanged(
        _In_ Windows::UI::Core::CoreWindow^ sender,
        _In_ Windows::UI::Core::VisibilityChangedEventArgs^ args
        );

    void Update();
    void SetGameInfoOverlay(GameInfoOverlayState state);
    void SetAction (GameInfoOverlayCommand command);
    void ShowGameInfoOverlay();
    void HideGameInfoOverlay();
    void SetSnapped();
    void HideSnapped();

    bool                                                m_windowClosed;
    bool                                                m_renderNeeded;
    bool                                                m_haveFocus;
    bool                                                m_visible;

    MoveLookController^                                 m_controller;
    GameRenderer^                                       m_renderer;
    Simple3DGame^                                       m_game;

    UpdateEngineState                                   m_updateState;
    UpdateEngineState                                   m_updateStateNext;
    PressResultState                                    m_pressResult;
    GameInfoOverlayState                                m_gameInfoOverlayState;
    GameInfoOverlayCommand                              m_gameInfoOverlayCommand;
    uint32                                              m_loadingCount;
};

ref class Direct3DApplicationSource : Windows::ApplicationModel::Core::IFrameworkViewSource
{
public:
    virtual Windows::ApplicationModel::Core::IFrameworkView^ CreateView();
};
```

App.cpp

```cpp
//--------------------------------------------------------------------------------------
//// THIS CODE AND INFORMATION IS PROVIDED "AS IS" WITHOUT WARRANTY OF
//// ANY KIND, EITHER EXPRESSED OR IMPLIED, INCLUDING BUT NOT LIMITED TO
//// THE IMPLIED WARRANTIES OF MERCHANTABILITY AND/OR FITNESS FOR A
//// PARTICULAR PURPOSE.
////
//// Copyright (c) Microsoft Corporation. All rights reserved

#include "pch.h"
#include "App.h"

using namespace concurrency;
using namespace DirectX;
using namespace Windows::ApplicationModel;
using namespace Windows::ApplicationModel::Activation;
using namespace Windows::ApplicationModel::Core;
using namespace Windows::Foundation;
using namespace Windows::Graphics::Display;
using namespace Windows::UI::Core;
using namespace Windows::UI::Input;
using namespace Windows::UI::ViewManagement;


App::App() :
    m_windowClosed(false),
    m_haveFocus(false),
    m_gameInfoOverlayCommand(GameInfoOverlayCommand::None),
    m_visible(true),
    m_loadingCount(0),
    m_updateState(UpdateEngineState::WaitingForResources)
{
}

//--------------------------------------------------------------------------------------

void App::Initialize(
    _In_ CoreApplicationView^ applicationView
    )
{
    applicationView->Activated +=
        ref new TypedEventHandler<CoreApplicationView^, IActivatedEventArgs^>(this, &App::OnActivated);

    CoreApplication::Suspending +=
        ref new EventHandler<SuspendingEventArgs^>(this, &App::OnSuspending);

    CoreApplication::Resuming +=
        ref new EventHandler<Platform::Object^>(this, &App::OnResuming);

    m_controller = ref new MoveLookController();
    m_renderer = ref new GameRenderer();
    m_game = ref new Simple3DGame();
}

//--------------------------------------------------------------------------------------

void App::SetWindow(
    _In_ CoreWindow^ window
    )
{
    window->PointerCursor = ref new CoreCursor(CoreCursorType::Arrow, 0);

    PointerVisualizationSettings^ visualizationSettings = PointerVisualizationSettings::GetForCurrentView();
    visualizationSettings->IsContactFeedbackEnabled = false;
    visualizationSettings->IsBarrelButtonFeedbackEnabled = false;

    window->SizeChanged +=
        ref new TypedEventHandler<CoreWindow^, WindowSizeChangedEventArgs^>(this, &App::OnWindowSizeChanged);

    window->Closed +=
        ref new TypedEventHandler<CoreWindow^, CoreWindowEventArgs^>(this, &App::OnWindowClosed);

    window->VisibilityChanged +=
        ref new TypedEventHandler<CoreWindow^, VisibilityChangedEventArgs^>(this, &App::OnVisibilityChanged);

    DisplayProperties::LogicalDpiChanged +=
        ref new DisplayPropertiesEventHandler(this, &App::OnLogicalDpiChanged);

    m_controller->Initialize(window);

    m_controller->SetMoveRect(
        XMFLOAT2(0.0f, window->Bounds.Height - GameConstants::TouchRectangleSize),
        XMFLOAT2(GameConstants::TouchRectangleSize, window->Bounds.Height)
        );
    m_controller->SetFireRect(
        XMFLOAT2(window->Bounds.Width - GameConstants::TouchRectangleSize, window->Bounds.Height - GameConstants::TouchRectangleSize),
        XMFLOAT2(window->Bounds.Width, window->Bounds.Height)
        );

    m_renderer->Initialize(window, DisplayProperties::LogicalDpi);
    SetGameInfoOverlay(GameInfoOverlayState::Loading);
    ShowGameInfoOverlay();
}

//--------------------------------------------------------------------------------------

void App::Load(
    _In_ Platform::String^ /* entryPoint */
    )
{
    create_task([this]()
    {
        // Asynchronously initialize the game class and load the renderer device resources.
        // By doing all this asynchronously, the game gets to its main loop more quickly
        // and loads all the necessary resources in parallel on other threads.
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
            // In the middle of a game, so spin up the async task to load the level.
            create_task([this]()
            {
                return m_game->LoadLevelAsync();

            }).then([this]()
            {
                // The m_game object may need to deal with D3D device context work, so
                // again the finalize code needs to run in the same thread
                // context as the m_renderer object was created because the D3D 
                // device context can ONLY be accessed on a single thread.
                m_game->FinalizeLoadLevel();
                m_updateState = UpdateEngineState::ResourcesLoaded;

            }, task_continuation_context::use_current());
        }
    }, task_continuation_context::use_current());
}

//--------------------------------------------------------------------------------------

void App::Run()
{
    while (!m_windowClosed)
    {
        if (m_visible)
        {
            switch (m_updateState)
            {
            case UpdateEngineState::Deactivated:
            case UpdateEngineState::Snapped:
                if (!m_renderNeeded)
                {
                    // The App is not currently the active window, so just wait for events.
                    CoreWindow::GetForCurrentThread()->Dispatcher->ProcessEvents(CoreProcessEventsOption::ProcessOneAndAllPending);
                    break;
                }
                // Otherwise, fall through and do normal processing to get the rendering handled.
            default:
                CoreWindow::GetForCurrentThread()->Dispatcher->ProcessEvents(CoreProcessEventsOption::ProcessAllIfPresent);
                Update();
                m_renderer->Render();
                m_renderNeeded = false;
            }
        }
        else
        {
            CoreWindow::GetForCurrentThread()->Dispatcher->ProcessEvents(CoreProcessEventsOption::ProcessOneAndAllPending);
        }
    }
    m_game->OnSuspending();  // Exiting due to window close.  Make sure to save state.
}

//--------------------------------------------------------------------------------------

void App::Uninitialize()
{
}

//--------------------------------------------------------------------------------------

void App::OnWindowSizeChanged(
    _In_ CoreWindow^ window,
    _In_ WindowSizeChangedEventArgs^ /* args */
    )
{
    UpdateViewState();
    m_renderer->UpdateForWindowSizeChange();

    // The location of the GameInfoOverlay may have changed with the size change, so update the controller.
    m_controller->SetMoveRect(
        XMFLOAT2(0.0f, window->Bounds.Height - GameConstants::TouchRectangleSize),
        XMFLOAT2(GameConstants::TouchRectangleSize, window->Bounds.Height)
        );
    m_controller->SetFireRect(
        XMFLOAT2(window->Bounds.Width - GameConstants::TouchRectangleSize, window->Bounds.Height - GameConstants::TouchRectangleSize),
        XMFLOAT2(window->Bounds.Width, window->Bounds.Height)
        );

    if (m_updateState == UpdateEngineState::WaitingForPress)
    {
        m_controller->WaitForPress(m_renderer->GameInfoOverlayUpperLeft(), m_renderer->GameInfoOverlayLowerRight());
    }
}

//--------------------------------------------------------------------------------------

void App::OnWindowClosed(
    _In_ CoreWindow^ /* sender */,
    _In_ CoreWindowEventArgs^ /* args */
    )
{
    m_windowClosed = true;
}

//--------------------------------------------------------------------------------------

void App::OnLogicalDpiChanged(
    _In_ Platform::Object^ /* sender */
    )
{
    m_renderer->SetDpi(DisplayProperties::LogicalDpi);

    // The GameInfoOverlay may have been recreated as a result of DPI changes, so
    // regenerate the data.
    SetGameInfoOverlay(m_gameInfoOverlayState);
    SetAction(m_gameInfoOverlayCommand);
}

//--------------------------------------------------------------------------------------

void App::OnActivated(
    _In_ CoreApplicationView^ /* applicationView */,
    _In_ IActivatedEventArgs^ /* args */
    )
{
    CoreWindow::GetForCurrentThread()->Activated +=
        ref new TypedEventHandler<CoreWindow^, WindowActivatedEventArgs^>(this, &App::OnWindowActivationChanged);
    CoreWindow::GetForCurrentThread()->Activate();
}

//--------------------------------------------------------------------------------------

void App::OnVisibilityChanged(
    _In_ CoreWindow^ /* sender */,
    _In_ VisibilityChangedEventArgs^ args
    )
{
    m_visible = args->Visible;
}

//--------------------------------------------------------------------------------------

void App::InitializeGameState()
{
    // Set up the initial state machine for handling the Game playing state.
    if (m_game->GameActive() && m_game->LevelActive())
    {
        // The last time the game terminated it was in the middle
        // of a level.
        // We are waiting for the user to continue the game.
        m_updateState = UpdateEngineState::WaitingForResources;
        m_pressResult = PressResultState::ContinueLevel;
        SetGameInfoOverlay(GameInfoOverlayState::Pause);
        SetAction(GameInfoOverlayCommand::PleaseWait);
    }
    else if (!m_game->GameActive() && (m_game->HighScore().totalHits > 0))
    {
        // The last time the game terminated the game had been completed.
        // Show the high score.
        // We are waiting for the user to acknowledge the high score and start a new game.
        // The level resources for the first level will be loaded later.
        m_updateState = UpdateEngineState::WaitingForPress;
        m_pressResult = PressResultState::LoadGame;
        SetGameInfoOverlay(GameInfoOverlayState::GameStats);
        m_controller->WaitForPress(m_renderer->GameInfoOverlayUpperLeft(), m_renderer->GameInfoOverlayLowerRight());
        SetAction(GameInfoOverlayCommand::TapToContinue);
    }
    else
    {
        // This is either the first time the game has run or
        // the last time the game terminated the level was completed.
        // We are waiting for the user to begin the next level.
        m_updateState = UpdateEngineState::WaitingForResources;
        m_pressResult = PressResultState::PlayLevel;
        SetGameInfoOverlay(GameInfoOverlayState::LevelStart);
        SetAction(GameInfoOverlayCommand::PleaseWait);
    }
    ShowGameInfoOverlay();
}

//--------------------------------------------------------------------------------------

void App::Update()
{
    static uint32 loadCount = 0;

    m_controller->Update();

    switch (m_updateState)
    {
    case UpdateEngineState::WaitingForResources:
        // Waiting for the initial load.  Display an update once per 60 updates.
        loadCount++;
        if ((loadCount % 60) == 0)
        {
            m_loadingCount++;
            SetGameInfoOverlay(m_gameInfoOverlayState);
        }
        break;

    case UpdateEngineState::ResourcesLoaded:
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
        m_updateState = UpdateEngineState::WaitingForPress;
        SetAction(GameInfoOverlayCommand::TapToContinue);
        m_controller->WaitForPress(m_renderer->GameInfoOverlayUpperLeft(), m_renderer->GameInfoOverlayLowerRight());
        ShowGameInfoOverlay();
        m_renderNeeded = true;
        break;

    case UpdateEngineState::WaitingForPress:
        if (m_controller->IsPressComplete())
        {
            switch (m_pressResult)
            {
            case PressResultState::LoadGame:
                m_updateState = UpdateEngineState::WaitingForResources;
                m_pressResult = PressResultState::PlayLevel;
                m_controller->Active(false);
                m_game->LoadGame();
                SetAction(GameInfoOverlayCommand::PleaseWait);
                SetGameInfoOverlay(GameInfoOverlayState::LevelStart);
                ShowGameInfoOverlay();

                m_game->LoadLevelAsync().then([this]()
                {
                    m_game->FinalizeLoadLevel();
                    m_updateState = UpdateEngineState::ResourcesLoaded;

                }, task_continuation_context::use_current());
                break;

            case PressResultState::PlayLevel:
                m_updateState = UpdateEngineState::Dynamics;
                HideGameInfoOverlay();
                m_controller->Active(true);
                m_game->StartLevel();
                break;

            case PressResultState::ContinueLevel:
                m_updateState = UpdateEngineState::Dynamics;
                HideGameInfoOverlay();
                m_controller->Active(true);
                m_game->ContinueGame();
                break;
            }
        }
        break;

    case UpdateEngineState::Dynamics:
        if (m_controller->IsPauseRequested())
        {
            m_game->PauseGame();
            SetGameInfoOverlay(GameInfoOverlayState::Pause);
            SetAction(GameInfoOverlayCommand::TapToContinue);
            m_updateState = UpdateEngineState::WaitingForPress;
            m_pressResult = PressResultState::ContinueLevel;
            ShowGameInfoOverlay();
        }
        else
        {
            GameState runState = m_game->RunGame();
            switch (runState)
            {
            case GameState::TimeExpired:
                SetAction(GameInfoOverlayCommand::TapToContinue);
                SetGameInfoOverlay(GameInfoOverlayState::GameOverExpired);
                ShowGameInfoOverlay();
                m_updateState = UpdateEngineState::WaitingForPress;
                m_pressResult = PressResultState::LoadGame;
                break;

            case GameState::LevelComplete:
                SetAction(GameInfoOverlayCommand::PleaseWait);
                SetGameInfoOverlay(GameInfoOverlayState::LevelStart);
                ShowGameInfoOverlay();
                m_updateState = UpdateEngineState::WaitingForResources;
                m_pressResult = PressResultState::PlayLevel;

                m_game->LoadLevelAsync().then([this]()
                {
                    m_game->FinalizeLoadLevel();
                    m_updateState = UpdateEngineState::ResourcesLoaded;

                }, task_continuation_context::use_current());
                break;

            case GameState::GameComplete:
                SetAction(GameInfoOverlayCommand::TapToContinue);
                SetGameInfoOverlay(GameInfoOverlayState::GameOverCompleted);
                ShowGameInfoOverlay();
                m_updateState  = UpdateEngineState::WaitingForPress;
                m_pressResult = PressResultState::LoadGame;
                break;
            }
        }

        if (m_updateState == UpdateEngineState::WaitingForPress)
        {
            // Transitioning state, so enable waiting for the press event.
            m_controller->WaitForPress(m_renderer->GameInfoOverlayUpperLeft(), m_renderer->GameInfoOverlayLowerRight());
        }
        if (m_updateState == UpdateEngineState::WaitingForResources)
        {
            // Transitioning state, so shut down the input controller until resources are loaded.
            m_controller->Active(false);
        }
        break;
    }
}

//--------------------------------------------------------------------------------------

void App::OnWindowActivationChanged(
    _In_ Windows::UI::Core::CoreWindow^ /* sender */,
    _In_ Windows::UI::Core::WindowActivatedEventArgs^ args
    )
{
    if (args->WindowActivationState == CoreWindowActivationState::Deactivated)
    {
        m_haveFocus = false;

        switch (m_updateState)
        {
        case UpdateEngineState::Dynamics:
            // From Dynamic mode, when coming out of Deactivated rather than going directly back into game play
            // go to the paused state waiting for user input to continue.
            m_updateStateNext = UpdateEngineState::WaitingForPress;
            m_pressResult = PressResultState::ContinueLevel;
            SetGameInfoOverlay(GameInfoOverlayState::Pause);
            ShowGameInfoOverlay();
            m_game->PauseGame();
            m_updateState = UpdateEngineState::Deactivated;
            SetAction(GameInfoOverlayCommand::None);
            m_renderNeeded = true;
            break;

        case UpdateEngineState::WaitingForResources:
        case UpdateEngineState::WaitingForPress:
            m_updateStateNext = m_updateState;
            m_updateState = UpdateEngineState::Deactivated;
            SetAction(GameInfoOverlayCommand::None);
            ShowGameInfoOverlay();
            m_renderNeeded = true;
            break;
        }
        // Don't have focus, so shutdown input processing.
        m_controller->Active(false);
    }
    else if (args->WindowActivationState == CoreWindowActivationState::CodeActivated
        || args->WindowActivationState == CoreWindowActivationState::PointerActivated)
    {
        m_haveFocus = true;

        if (m_updateState == UpdateEngineState::Deactivated)
        {
            m_updateState = m_updateStateNext;

            if (m_updateState == UpdateEngineState::WaitingForPress)
            {
                SetAction(GameInfoOverlayCommand::TapToContinue);
                m_controller->WaitForPress(m_renderer->GameInfoOverlayUpperLeft(), m_renderer->GameInfoOverlayLowerRight());
            }
            else if (m_updateStateNext == UpdateEngineState::WaitingForResources)
            {
                SetAction(GameInfoOverlayCommand::PleaseWait);
            }
        }
    }
}

//--------------------------------------------------------------------------------------

void App::OnSuspending(
    _In_ Platform::Object^ /* sender */,
    _In_ SuspendingEventArgs^ args
    )
{
    // Save application state.
    // If your application needs time to complete a lengthy operation, it can request a deferral.
    // The SuspendingOperation has a deadline time. Make sure all your operations are complete by that time!
    // If the app doesn't return from this handler within five seconds, it will be terminated.
    SuspendingOperation^ op = args->SuspendingOperation;
    SuspendingDeferral^ deferral = op->GetDeferral();

    create_task([=]()
    {
        switch (m_updateState)
        {
        case UpdateEngineState::Dynamics:
            // Game is in the active game play state, Stop Game Timer and Pause play and save the state.
            SetAction(GameInfoOverlayCommand::None);
            SetGameInfoOverlay(GameInfoOverlayState::Pause);
            m_updateStateNext = UpdateEngineState::WaitingForPress;
            m_pressResult = PressResultState::ContinueLevel;
            m_game->PauseGame();
            break;

        case UpdateEngineState::WaitingForResources:
        case UpdateEngineState::WaitingForPress:
            m_updateStateNext = m_updateState;
            break;

        default:
            // If it is any other state, don't save as the next state as they are transient states and have already set m_updateStateNext
            break;
        }
        m_updateState = UpdateEngineState::Suspended;

        m_controller->Active(false);
        m_game->OnSuspending();

        deferral->Complete();
    });
}

//--------------------------------------------------------------------------------------

void App::OnResuming(
    _In_ Platform::Object^ /* sender */,
    _In_ Platform::Object^ /* args */
    )
{
    if (m_haveFocus)
    {
        m_updateState = m_updateStateNext;
    }
    else
    {
        m_updateState = UpdateEngineState::Deactivated;
    }

    if (m_updateState == UpdateEngineState::WaitingForPress)
    {
        SetAction(GameInfoOverlayCommand::TapToContinue);
        m_controller->WaitForPress(m_renderer->GameInfoOverlayUpperLeft(), m_renderer->GameInfoOverlayLowerRight());
    }
    m_game->OnResuming();
    ShowGameInfoOverlay();
    m_renderNeeded = true;
}

//--------------------------------------------------------------------------------------

void App::UpdateViewState()
{
    m_renderNeeded = true;

    if (ApplicationView::Value == ApplicationViewState::Snapped)
    {
        switch (m_updateState)
        {
        case UpdateEngineState::Dynamics:
            // From Dynamic mode, when coming out of SNAPPED layout rather than going directly back into game play,
            // go to the paused state and wait for user input to continue.
            m_updateStateNext = UpdateEngineState::WaitingForPress;
            m_pressResult = PressResultState::ContinueLevel;
            SetGameInfoOverlay(GameInfoOverlayState::Pause);
            SetAction(GameInfoOverlayCommand::TapToContinue);
            m_game->PauseGame();
            break;

        case UpdateEngineState::WaitingForResources:
        case UpdateEngineState::WaitingForPress:
            // Avoid corrupting the m_updateStateNext on a transition from Snapped -> Snapped.
            // Otherwise, just cache the current state and return to it when leaving SNAPPED layout.

            m_updateStateNext = m_updateState;
            break;

        default:
            break;
        }

        m_updateState = UpdateEngineState::Snapped;
        m_controller->Active(false);
        HideGameInfoOverlay();
        SetSnapped();
    }
    else if (ApplicationView::Value == ApplicationViewState::Filled ||
        ApplicationView::Value == ApplicationViewState::FullScreenLandscape ||
        ApplicationView::Value == ApplicationViewState::FullScreenPortrait)
    {
        if (m_updateState == UpdateEngineState::Snapped)
        {

            HideSnapped();
            ShowGameInfoOverlay();
            m_renderNeeded = true;

            if (m_haveFocus)
            {
                if (m_updateStateNext == UpdateEngineState::WaitingForPress)
                {
                    SetAction(GameInfoOverlayCommand::TapToContinue);
                    m_controller->WaitForPress(m_renderer->GameInfoOverlayUpperLeft(), m_renderer->GameInfoOverlayLowerRight());
                }
                else if (m_updateStateNext == UpdateEngineState::WaitingForResources)
                {
                    SetAction(GameInfoOverlayCommand::PleaseWait);
                }

                m_updateState = m_updateStateNext;
            }
            else
            {
                m_updateState = UpdateEngineState::Deactivated;
                SetAction(GameInfoOverlayCommand::None);
            }
        }
    }
}

//--------------------------------------------------------------------------------------

void App::SetGameInfoOverlay(GameInfoOverlayState state)
{
    m_gameInfoOverlayState = state;
    switch (state)
    {
    case GameInfoOverlayState::Loading:
        m_renderer->InfoOverlay()->SetGameLoading(m_loadingCount);
        break;

    case GameInfoOverlayState::GameStats:
        m_renderer->InfoOverlay()->SetGameStats(
            m_game->HighScore().levelCompleted + 1,
            m_game->HighScore().totalHits,
            m_game->HighScore().totalShots
            );
        break;

    case GameInfoOverlayState::LevelStart:
        m_renderer->InfoOverlay()->SetLevelStart(
            m_game->LevelCompleted() + 1,
            m_game->CurrentLevel()->Objective(),
            m_game->CurrentLevel()->TimeLimit(),
            m_game->BonusTime()
            );
        break;

    case GameInfoOverlayState::GameOverCompleted:
        m_renderer->InfoOverlay()->SetGameOver(
            true,
            m_game->LevelCompleted() + 1,
            m_game->TotalHits(),
            m_game->TotalShots(),
            m_game->HighScore().totalHits
            );
        break;

    case GameInfoOverlayState::GameOverExpired:
        m_renderer->InfoOverlay()->SetGameOver(
            false,
            m_game->LevelCompleted(),
            m_game->TotalHits(),
            m_game->TotalShots(),
            m_game->HighScore().totalHits
            );
        break;

    case GameInfoOverlayState::Pause:
        m_renderer->InfoOverlay()->SetPause();
        break;
    }
}

//--------------------------------------------------------------------------------------

void App::SetAction (GameInfoOverlayCommand command)
{
    m_gameInfoOverlayCommand = command;
    m_renderer->InfoOverlay()->SetAction(command);
}

//--------------------------------------------------------------------------------------

void App::ShowGameInfoOverlay()
{
    m_renderer->InfoOverlay()->ShowGameInfoOverlay();
}

//--------------------------------------------------------------------------------------

void App::HideGameInfoOverlay()
{
    m_renderer->InfoOverlay()->HideGameInfoOverlay();
}

//--------------------------------------------------------------------------------------

void App::SetSnapped()
{
    m_renderer->InfoOverlay()->SetPause();
    m_renderer->InfoOverlay()->ShowGameInfoOverlay();
}

//--------------------------------------------------------------------------------------

void App::HideSnapped()
{
    m_renderer->InfoOverlay()->HideGameInfoOverlay();
    SetGameInfoOverlay(m_gameInfoOverlayState);
}

//--------------------------------------------------------------------------------------

IFrameworkView^ DirectXAppSource::CreateView()
{
    return ref new App();
}

//--------------------------------------------------------------------------------------

[Platform::MTAThread]
int main(Platform::Array<Platform::String^>^)
{
    auto direct3DApplicationSource = ref new Direct3DApplicationSource();
    CoreApplication::Run(direct3DApplicationSource);
    return 0;
}

//--------------------------------------------------------------------------------------
```

 

 






<!--HONumber=Mar16_HO1-->


