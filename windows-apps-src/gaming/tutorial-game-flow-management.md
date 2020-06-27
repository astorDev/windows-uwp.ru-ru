---
title: Управление потоком игры
description: Узнайте, как инициализировать состояния игры, обрабатывать события и настроить обновления игрового цикла.
ms.assetid: 6c33bf09-b46a-4bb5-8a59-ca83ce257eb3
ms.date: 06/24/2020
ms.topic: article
keywords: windows 10, uwp, игры, directx
ms.localizationpriority: medium
ms.openlocfilehash: 181eca743a9ccdc76ebfc1302e8bb04d85a32269
ms.sourcegitcommit: 20969781aca50738792631f4b68326f9171a3980
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/26/2020
ms.locfileid: "85409633"
---
# <a name="game-flow-management"></a>Управление потоком игры

> [!NOTE]
> Эта статья является частью серии руководств по [созданию простой универсальная платформа Windows (UWP) с помощью DirectX](tutorial--create-your-first-uwp-directx-game.md) . В разделе этой ссылки задается контекст для ряда.

Игра теперь содержит окно, зарегистрировало несколько обработчиков событий и загрузило ресурсы в асинхронном режиме. В этом разделе объясняется, как использовать состояния игры, как управлять конкретными основными состояниями игры, а также как создать цикл обновления для игрового ядра. Затем мы познакомимся с потоком пользовательского интерфейса и, наконец, рассмотрим дополнительные сведения о обработчиках событий, необходимых для игры UWP.

## <a name="game-states-used-to-manage-game-flow"></a>Игровые состояния, используемые для управления потоком игры

Мы используем состояния игры, чтобы управлять процессом игры.

Когда демонстрационная игра **Simple3DGameDX** выполняется в первый раз на компьютере, она находится в состоянии, где не была запущена игра. При последующих запусках игры она может находиться в любом из этих состояний.

- Игра не запущена, или между уровнями игры (высокая оценка равна нулю).
- Цикл игры выполняется и находится в середине уровня.
- Цикл игры не выполняется из-за игры, которая была завершена (высокая оценка имеет ненулевое значение).

Игра может иметь столько Штатов, сколько необходимо. Но помните, что его можно завершить в любое время. После возобновления пользователь должен возобновить работу в состоянии, в котором оно находилось в момент завершения.

## <a name="game-state-management"></a>Управление состоянием игры

Поэтому во время инициализации игры необходимо поддерживать холодный запуск игры, а также возобновлять игру после ее остановки в полете. Пример **Simple3DGameDX** всегда сохраняет свое состояние игры, чтобы дать впечатление, что она никогда не была остановлена.

В ответ на событие приостановки игрового процесса приостанавливается, но ресурсы игры остаются в памяти. Аналогичным образом, событие Resume обрабатывается, чтобы убедиться, что игра выбирается в том состоянии, в котором она находилась в момент, когда она была приостановлена или прервана. В зависимости от состояния игроку предоставляются разные возможности. 

- Если игра возобновляет середину, она появляется в приостановленном состоянии, а наложение позволяет продолжить работу.
- Если игра возобновляется в состоянии, когда игра завершается, отображаются высокие баллы и параметр для воспроизведения новой игры.
- Наконец, если игра возобновляется до начала уровня, наложение представляет собой параметр запуска для пользователя.

В примере игры не различается, является ли игра холодной, запускается в первый раз без события приостановки или возобновляется из приостановленного состояния. Такая модель подходит для любого приложения UWP.

В этом примере инициализация состояний игры происходит в [**гамемаин:: инитиализегаместате**](#the-gamemaininitializegamestate-method) (структура этого метода показана в следующем разделе).

Ниже приведена блок-схема, помогающая визуализировать поток. Он охватывает как инициализацию, так и цикл обновления.

- Инициализация начинается с узла **Start** при проверке текущего состояния игры. Сведения о коде игры см. в разделе [**гамемаин:: инитиализегаместате**](#the-gamemaininitializegamestate-method) .
* Дополнительные сведения о цикле обновления приведены в разделе [Обновление игрового движка](#update-game-engine). Для кода игры перейдите к [**гамемаин:: Update**](#the-gamemainupdate-method).

![Главный конечный автомат для игры](images/simple-dx-game-flow-statemachine.png)

### <a name="the-gamemaininitializegamestate-method"></a>Метод Гамемаин:: Инитиализегаместате

Метод **гамемаин:: инитиализегаместате** вызывается косвенно через конструктор класса **гамемаин** , который является результатом создания экземпляра **Гамемаин** в **приложении:: Load**.

```cppwinrt
GameMain::GameMain(std::shared_ptr<DX::DeviceResources> const& deviceResources) : ...
{
    m_deviceResources->RegisterDeviceNotify(this);
    ...
    ConstructInBackground();
}

winrt::fire_and_forget GameMain::ConstructInBackground()
{
    ...
    m_renderer->FinalizeCreateGameDeviceResources();

    InitializeGameState();
    ...
}

void GameMain::InitializeGameState()
{
    // Set up the initial state machine for handling Game playing state.
    if (m_game->GameActive() && m_game->LevelActive())
    {
        // The last time the game terminated it was in the middle
        // of a level.
        // We are waiting for the user to continue the game.
        ...
    }
    else if (!m_game->GameActive() && (m_game->HighScore().totalHits > 0))
    {
        // The last time the game terminated the game had been completed.
        // Show the high score.
        // We are waiting for the user to acknowledge the high score and start a new game.
        // The level resources for the first level will be loaded later.
        ...
    }
    else
    {
        // This is either the first time the game has run or
        // the last time the game terminated the level was completed.
        // We are waiting for the user to begin the next level.
        ...
    }
    m_uiControl->ShowGameInfoOverlay();
}
```

## <a name="update-game-engine"></a>Обновление игрового движка

Метод **app:: Run** вызывает **Гамемаин:: Run**. В **гамемаин:: Run** — это базовый конечный автомат для обработки всех основных действий, которые может выполнять пользователь. Самый высокий уровень этого конечного автомата связан с загрузкой игры, воспроизведением определенного уровня или продолжением уровня после приостановки игры (системой или пользователем).

В игре-образце имеется 3 основных состояния (представленные перечислением **упдатингинестате** ), в которых может находиться игра.

1. **Упдатингинестате:: ваитингфорресаурцес**. Игровой цикл повторяется из-за невозможности перехода до тех пор, пока не появится доступ к ресурсам (в данном случае — к графическим). По завершении асинхронных задач загрузки ресурсов мы обновляем состояние на **упдатингинестате:: ресаурцеслоадед**. Обычно это происходит между уровнями, когда уровень загружает новые ресурсы с диска, с игрового сервера или с облачной серверной части. В примере игры мы моделируем такое поведение, так как в этом примере не требуются дополнительные ресурсы на уровне.
2. **Упдатингинестате:: ваитингфорпресс**. Игровой цикл повторяется в ожидании конкретного ввода от пользователя. Эти входные данные являются действием проигрывателя для загрузки игры, для запуска уровня или для продолжения уровня. Пример кода ссылается на эти подсостояния через перечисление **прессресултстате** .
3. **Упдатингинестате::D инамикс**. Игровой цикл выполняется с участием пользователя. При нахождении пользователя в игровом процессе игра проверяет 3 состояния, в которые она может перейти: 
 - **Гаместате:: тимикспиред**. Срок действия ограничения по времени для уровня.
 - **Гаместате:: левелкомплете**. Завершение уровня проигрывателем.
 - **Гаместате:: гамекомплете**. Завершение всех уровней проигрывателем.

Игра — это просто конечный автомат, содержащий несколько меньших состояний автоматов. Каждое определенное состояние должно быть определено особым критерием. Переходы из одного состояния в другое должны основываться на отдельных входных данных пользователя или системных действиях (таких как загрузка графических ресурсов).

При планировании игры рассмотрите возможность вырисовки всего игрового потока, чтобы убедиться в том, что вы устранили все возможные действия, которые может предпринять пользователь или система. Игра может быть очень сложной, поэтому конечный автомат является мощным средством, помогающим визуализировать эту сложность и сделать ее более управляемой.

Давайте рассмотрим код для цикла обновления.

### <a name="the-gamemainupdate-method"></a>Метод Гамемаин:: Update

Это структура конечного автомата, используемая для обновления модуля игры.

```cppwinrt
void GameMain::Update()
{
    // The controller object has its own update loop.
    m_controller->Update(); 

    switch (m_updateState)
    {
    case UpdateEngineState::WaitingForResources:
        ...
        break;

    case UpdateEngineState::ResourcesLoaded:
        ...
        break;

    case UpdateEngineState::WaitingForPress:
        if (m_controller->IsPressComplete())
        {
            ...
        }
        break;

    case UpdateEngineState::Dynamics:
        if (m_controller->IsPauseRequested())
        {
            ...
        }
        else
        {
            // When the player is playing, work is done by Simple3DGame::RunGame.
            GameState runState = m_game->RunGame();
            switch (runState)
            {
            case GameState::TimeExpired:
                ...
                break;

            case GameState::LevelComplete:
                ...
                break;

            case GameState::GameComplete:
                ...
                break;
            }
        }

        if (m_updateState == UpdateEngineState::WaitingForPress)
        {
            // Transitioning state, so enable waiting for the press event.
            m_controller->WaitForPress(
                m_renderer->GameInfoOverlayUpperLeft(),
                m_renderer->GameInfoOverlayLowerRight());
        }
        if (m_updateState == UpdateEngineState::WaitingForResources)
        {
            // Transitioning state, so shut down the input controller
            // until resources are loaded.
            m_controller->Active(false);
        }
        break;
    }
}
```

## <a name="update-the-user-interface"></a>Обновление пользовательского интерфейса

Чтобы состояние могло меняться в зависимости от действий игрока и в соответствии с правилами игры, его необходимо информировать о состоянии системы. Многие игры, включая этот пример игры, обычно используют элементы пользовательского интерфейса для предоставления этой информации проигрывателю. Пользовательский интерфейс содержит представления состояния игры и другие сведения, относящиеся к игре, такие как оценка, АММО или количество оставшихся шансов. Пользовательский интерфейс также называется наложением, так как он отрисовывается отдельно от основного графического конвейера и помещается поверх трехмерной проекции.

Некоторые сведения о пользовательском интерфейсе также представляются в виде заголовков (HUD), что позволяет пользователю видеть эти сведения, не отвлекая их глаза в основную область игрового процесса. В примере игры мы создадим этот оверлей с помощью API-интерфейсов Direct2D. Кроме того, мы можем создать этот оверлей с помощью XAML, который обсуждается в разделе [расширение игры в образце](tutorial-resources.md).

Пользовательский интерфейс состоит из двух компонентов.

- HUD, содержащий оценку и сведения о текущем состоянии игрового процесса.
- точечный рисунок паузы, представляющий собой черный прямоугольник с текстом, накладываемый в состоянии паузы/приостановки игры. Это наложение в игре описано ниже в разделе [Добавление пользовательского интерфейса](tutorial--adding-a-user-interface.md).

Неудивительно, что наложение также снабжено конечным автоматом Наложение может отображать начало или сообщение на уровне игры. По сути, это холст, на котором мы можем выпустить любые сведения о состоянии игры, которые нужно отобразить проигрывателю во время приостановки или приостановки игры.

Отображаемый оверлей может быть одним из этих шести экранов в зависимости от состояния игры.

1. Экран хода выполнения загрузки ресурсов в начале игры.
2. Экран статистики игрового процесса.
3. Экран сообщения о запуске уровня.
4. Экран на экране, если все уровни завершены без времени выполнения.
5. Экран при истечении времени выполнения.
6. Приостановить экран меню.

Отделение пользовательского интерфейса от графического конвейера игры позволяет работать с ним независимо от механизма рендеринга графики игры и значительно снизить сложность кода игры.

Вот как пример игры применяет конечный автомат наложения.

```cppwinrt
void GameMain::SetGameInfoOverlay(GameInfoOverlayState state)
{
    m_gameInfoOverlayState = state;
    switch (state)
    {
    case GameInfoOverlayState::Loading:
        m_uiControl->SetGameLoading(m_loadingCount);
        break;

    case GameInfoOverlayState::GameStats:
        ...
        break;

    case GameInfoOverlayState::LevelStart:
        ...
        break;

    case GameInfoOverlayState::GameOverCompleted:
        ...
        break;

    case GameInfoOverlayState::GameOverExpired:
        ...
        break;

    case GameInfoOverlayState::Pause:
        ...
        break;
    }
}
```

## <a name="event-handling"></a>Обработка событий

Как мы видели в разделе [Определение платформы приложений UWP в игре](tutorial--building-the-games-uwp-app-framework.md) , многие методы представления-поставщика класса **приложения** регистрируют обработчики событий. Эти методы должны правильно обрабатывали эти важные события перед добавлением механики игры или запуском разработки графики.

Правильная обработка рассматриваемых событий является фундаментальным элементом для работы с приложениями UWP. Так как приложение UWP может быть активировано в любое время, деактивировано, изменено, привязано, не привязано, отключено или возобновлено, игра должна зарегистрироваться для этих событий, как только это будет возможно, и обрабатывать их способом, который обеспечивает гладкую и предсказуемую работу проигрывателя.

Это обработчики событий, используемые в этом примере, и обрабатываемые ими события.

<table>
<colgroup>
<col width="50%" />
<col width="50%" />
</colgroup>
<thead>
<tr class="header">
<th align="left">Обработчик событий</th>
<th align="left">Описание:</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left">OnActivated</td>
<td align="left">Обрабатывает <a href="/uwp/api/windows.applicationmodel.core.coreapplicationview.activated"><strong>CoreApplicationView::Activated</strong></a>. Игровое приложение было выведено на передний план, поэтому активировано главное окно.</td>
</tr>
<tr class="even">
<td align="left">OnDpiChanged</td>
<td align="left">Обрабатывает <a href="/uwp/api/windows.graphics.display.displayinformation#Windows_Graphics_Display_DisplayInformation_DpiChanged"><strong>Graphics::Display::DisplayInformation::DpiChanged</strong></a>. Разрешение экрана было изменено, и игровое приложение соответствующим образом перераспределяет ресурсы.
<div class="alert">
<strong>Примечание</strong>. координаты <a href="/windows/desktop/api/dxgi1_2/nf-dxgi1_2-idxgifactory2-createswapchainforcorewindow"><strong>CoreWindow</strong></a> находятся в аппаратно-независимых пикселях (DIP) для <a href="/windows/desktop/Direct2D/direct2d-overview">Direct2D</a>. В результате необходимо передать в Direct2D уведомление об изменениях разрешения для правильного отображения любых двумерных ресурсов или примитивов.
</div>
<div>
</div></td>
</tr>
<tr class="odd">
<td align="left">OnOrientationChanged</td>
<td align="left">Обрабатывает <a href="/uwp/api/windows.graphics.display.displayinformation#Windows_Graphics_Display_DisplayInformation_OrientationChanged"><strong>Graphics::Display::DisplayInformation::OrientationChanged</strong></a>. Ориентация дисплея изменилась, и графические элементы необходимо обновить.</td>
</tr>
<tr class="even">
<td align="left">OnDisplayContentsInvalidated</td>
<td align="left">Обрабатывает <a href="/uwp/api/windows.graphics.display.displayinformation#Windows_Graphics_Display_DisplayInformation_DisplayContentsInvalidated"><strong>Graphics::Display::DisplayInformation::DisplayContentsInvalidated</strong></a>. Изображение на экране следует обновить, и ваша игра должна быть повторно отрисована.</td>
</tr>
<tr class="odd">
<td align="left">OnResuming</td>
<td align="left">Обрабатывает <a href="/uwp/api/windows.applicationmodel.core.coreapplication.resuming"><strong>CoreApplication::Resuming</strong></a>. Игровое приложение восстанавливает игру из состояния приостановки.</td>
</tr>
<tr class="even">
<td align="left">OnSuspending</td>
<td align="left">Обрабатывает <a href="/uwp/api/windows.applicationmodel.core.coreapplication.suspending"><strong>CoreApplication::Suspending</strong></a>. Игровое приложение сохраняет свое состояние на диск. У него есть 5 секунд, чтобы сохранить состояние в хранилище.</td>
</tr>
<tr class="odd">
<td align="left">OnVisibilityChanged</td>
<td align="left">Обрабатывает <a href="/uwp/api/windows.ui.core.corewindow.visibilitychanged"><strong>CoreWindow::VisibilityChanged</strong></a>. Игровое приложение изменило видимость и стало видимым или невидимым из-за того, что отобразилось другое приложение.</td>
</tr>
<tr class="even">
<td align="left">OnWindowActivationChanged</td>
<td align="left">Обрабатывает <a href="/uwp/api/windows.ui.core.corewindow.activated"><strong>CoreWindow::Activated</strong></a>. Главное окно игрового приложения было деактивировано или активировано, поэтому оно должно удалить фокус и приостановить игру или восстановить фокус. В обоих случаях наложение показывает, что игра приостановлена.</td>
</tr>
<tr class="odd">
<td align="left">OnWindowClosed</td>
<td align="left">Обрабатывает <a href="/uwp/api/windows.ui.core.corewindow.closed"><strong>CoreWindow::Closed</strong></a>. Игровое приложение закрывает главное окно и приостанавливает игру.</td>
</tr>
<tr class="even">
<td align="left">OnWindowSizeChanged</td>
<td align="left">Обрабатывает <a href="/uwp/api/windows.ui.core.corewindow.sizechanged"><strong>CoreWindow::SizeChanged</strong></a>. Игровое приложение перераспределяет графические ресурсы и наложение, чтобы подстроиться под изменение размера, а затем обновляет целевой объект прорисовки.</td>
</tr>
</tbody>
</table>

## <a name="next-steps"></a>Следующие шаги

В этом разделе мы рассмотрели управление общим потоком игр с помощью состояний игры и то, что игра состоит из нескольких компьютеров с разными состояниями. Мы также рассмотрели, как обновлять пользовательский интерфейс и управлять основными обработчиками событий приложения. Теперь мы готовы перейти к циклу отрисовки, игре и ее механикам.
 
Вы можете пройти по оставшимся темам, которые задокументированы эту игру в любом порядке.

- [Определение основного игрового объекта](tutorial--defining-the-main-game-loop.md)
- [Платформа отрисовки I: введение в отрисовку](tutorial--assembling-the-rendering-pipeline.md)
- [Платформа отрисовки II: отрисовка игры](tutorial-game-rendering.md)
- [Добавление пользовательского интерфейса](tutorial--adding-a-user-interface.md)
- [Добавить элементы управления](tutorial--adding-controls.md)
- [Добавление звука](tutorial--adding-sound.md)
