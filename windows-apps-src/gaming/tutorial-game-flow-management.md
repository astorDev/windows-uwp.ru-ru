---
title: Управление потоком игры
description: Узнайте, как инициализировать состояния игры, обрабатывать события и настроить обновления игрового цикла.
ms.assetid: 6c33bf09-b46a-4bb5-8a59-ca83ce257eb3
ms.date: 10/24/2017
ms.topic: article
keywords: windows 10, uwp, игры, directx
ms.localizationpriority: medium
ms.openlocfilehash: c6d13b848a9e5d2dfc145431f732187c35c46ab6
ms.sourcegitcommit: b5c9c18e70625ab770946b8243f3465ee1013184
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/28/2018
ms.locfileid: "7971784"
---
# <a name="game-flow-management"></a>Управление потоком игры

Теперь у игры есть окно, зарегистрировано несколько обработчиков событий, и выполняется асинхронная загрузка ресурсов. В этом разделе рассматривается использование состояний игры, методы управления определенными ключевыми состояниями игры и создания цикла обновления для игрового движка. Затем мы рассмотрим поток интерфейса пользователя и, наконец, получим более полное представление об обработчиках событий и событиях, необходимых для игры UWP.

>[!Note]
>Если вы еще не скачали последний код для игры из этого примера, перейдите в раздел [Пример игры Direct3D](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/Simple3DGameDX). Этот пример является частью большой коллекции примеров функций UWP. Указания по скачиванию этого примера приводятся в разделе [Получение примеров UWP из GitHub](https://docs.microsoft.com/windows/uwp/get-started/get-uwp-app-samples).

## <a name="game-states-used-to-manage-game-flow"></a>Игровые состояния, используемые для управления потоком игры

Мы используем состояния игры, чтобы управлять процессом игры. Поскольку пользователь может в любое время возобновить выполнение игрового приложения UWP из состояния приостановки, игра может иметь любое число возможных состояний.

В этом примере игра при запуске может находиться в одном из трех состояний.
* Цикл игры выполняется и находится на середине уровня.
* Цикл игры не выполняется, поскольку игра недавно была завершена. (Получен высокий результат)
* Игра не была начата или находится между уровнями. (Максимальный результат: 0)

Можно определить количество необходимых состояний в зависимости от потребностей вашей игры. Опять же, не следует забывать, что ваша игра UWP должна поддерживать приостановку и последующее возобновление с того же места.

## <a name="game-states-initialization"></a>Инициализация состояний игры

При инициализации игры вы не просто сосредоточены на холодном запуске игры, но также на перезапуске после ее приостановки или завершения. В нашем примере игры всегда сохраняется состояние игры; это создает впечатление, что приложение постоянно выполняется. 

В состоянии приостановки приостанавливается только ход игры, но ресурсы игры по-прежнему остаются в памяти. 

Соответственно, событие возобновления обеспечивает, что пример игры возобновляет работу с того места, на котором она была приостановлена или прервана. Когда образец игры перезапускается после остановки, он запускается в обычном режиме и ищет последнее известное состояние, чтобы игрок мог сразу же продолжить игру.

В зависимости от состояния игроку предоставляются разные возможности. 

* Если игра возобновляется на середине уровня, она отображается на паузе и в диалоговом окне предоставляется возможность продолжения.
* Если игра возобновляется в состоянии, в котором игра завершена, отображаются лучшие результаты, и на экране предоставляется возможность начать новую игру.
* Наконец, если игра возобновляется перед началом прохождения уровня, пользователю в наложении предоставляется возможность начать.

Образец игры не различает состояния "холодного старта", т. е. первого запуска игры без события приостановки, и возобновления игры из приостановленного состояния. Такая модель подходит для любого приложения UWP.

В этом примере инициализация состояний игры происходит в [__GameMain::InitializeGameState__](#gamemaininitializegamestate-method).

Это блок-схема для визуализации процесса, в ней рассматриваются и инициализация, и цикл обновления.

* Инициализация начинается с узла __Start__ при проверке текущего состояния игры. Код игры приводится в разделе [__GameMain::InitializeGameState__](#gamemaininitializegamestate-method).
* Дополнительные сведения о цикле обновления приведены в разделе [Обновление игрового движка](#update-game-engine). Код игры приведен в разделе [__App::Update__](#appupdate-method).

![Главный конечный автомат для игры](images/simple-dx-game-flow-statemachine.png)

### <a name="gamemaininitializegamestate-method"></a>Метод GameMain::InitializeGameState

Метод __InitializeGameState__ вызывается из класса конструктора [__GameMain__](https://github.com/Microsoft/Windows-universal-samples/blob/5f0d0912214afc1c2a7c7470203933ddb46f7c89/Samples/Simple3DGameDX/cpp/GameMain.cpp#L32-L131), который вызывается при создании объекта класса __GameMain__ в методе [__App::Load__](https://github.com/Microsoft/Windows-universal-samples/blob/5f0d0912214afc1c2a7c7470203933ddb46f7c89/Samples/Simple3DGameDX/cpp/App.cpp#L115-L123).

```cpp

GameMain::GameMain(...)
{
    m_deviceResources->RegisterDeviceNotify(this);
    ...

    create_task([this]()
    {
        ...

    }).then([this]()
    {
        // The finalize code needs to run in the same thread context
        // as the m_renderer object was created because the D3D device context
        // can ONLY be accessed on a single thread.
        m_renderer->FinalizeCreateGameDeviceResources();

        InitializeGameState(); //Initialization of game states occurs here.
        
        ...
    
    }, task_continuation_context::use_current()).then([this]()
    {
        ...
        
    }, task_continuation_context::use_current());
}

```

```cpp

void GameMain::InitializeGameState()
{
    // Set up the initial state machine for handling Game playing state.
    if (m_game->GameActive() && m_game->LevelActive())
    {
        // The last time the game terminated it was in the middle of a level.
        // We are waiting for the user to continue the game.
        //...
    }
    else if (!m_game->GameActive() && (m_game->HighScore().totalHits > 0))
    {
        // The last time the game terminated the game had been completed.
        // Show the high score.
        // We are waiting for the user to acknowledge the high score and start a new game.
        // The level resources for the first level will be loaded later.
        //...
    }
    else
    {
        // This is either the first time the game has run or
        // the last time the game terminated the level was completed.
        // We are waiting for the user to begin the next level.
        m_updateState = UpdateEngineState::WaitingForResources;
        m_pressResult = PressResultState::PlayLevel;
        SetGameInfoOverlay(GameInfoOverlayState::LevelStart);
        m_uiControl->SetAction(GameInfoOverlayCommand::PleaseWait);
    }
    m_uiControl->ShowGameInfoOverlay();
}

```

## <a name="update-game-engine"></a>Обновление игрового движка

В методе [__App::Run__](https://github.com/Microsoft/Windows-universal-samples/blob/5f0d0912214afc1c2a7c7470203933ddb46f7c89/Samples/Simple3DGameDX/cpp/App.cpp#L127-L130) вызывается [__GameMain::Run__](https://github.com/Microsoft/Windows-universal-samples/blob/5f0d0912214afc1c2a7c7470203933ddb46f7c89/Samples/Simple3DGameDX/cpp/GameMain.cpp#L143-L202). В этом примере в методе реализован базовый конечный автомат, обрабатывающий все основные действия, которые может выполнять играющий. Самый высокий уровень этого конечного автомата обрабатывает загрузку игры, игру на конкретном уровне или продолжение игры на том или ином уровне после приостановки игры (системой или играющим).

В примере игры имеются три основных состояния (__UpdateEngineState__), в которых может находиться игра:

1. __Ожидание ресурсов__: игровой цикл повторяется из-за невозможности перехода до тех пор, пока не появится доступ к ресурсам (в данном случае — к графическим). Когда выполнение асинхронных задач загрузки ресурсов завершается, он изменяет состояние на __ResourcesLoaded__. Обычно это происходит при переходах между уровнями, когда выполняется загрузка новых ресурсов с диска, сервера игры или из облака. В примере игры такое поведение имитируется, поскольку здесь не требуется загружать какие-либо ресурсы.
2. __Ожидание нажатия__: игровой цикл повторяется в ожидании конкретного ввода от пользователя. Ввод представляет действие пользователя для загрузки игры, начала прохождения уровня или продолжения уровня. Образец игры ссылается на эти промежуточные состояния как на перечисляемые значения __PressResultState__.
3. В __динамике__: игровой цикл выполняется с участием пользователя. При нахождении пользователя в игровом процессе игра проверяет 3 состояния, в которые она может перейти: 
    * __TimeExpired__: истечение установленного времени для уровня
    * __LevelComplete__: завершение уровня игроком 
    * __GameComplete__: завершение всех уровней игроком

Ваша игра — просто конечный автомат, содержащий несколько меньших конечных автоматов. Каждое конкретное состояние должно быть определено очень определенными критериями. Переходы из одного состояния в другое должны основываться на дискретном вводе пользователя или действиях системы (таких как загрузка графических ресурсов). При планировании игры рассмотрите возможность нарисовать набросок всего потока игры, чтобы убедиться, что вы предусмотрели все возможные действия, которые может выполнить пользователь или система. Игры могут быть очень сложными, поэтому конечный автомат является мощным инструментом для визуализации составных процессов и облегчения управления ими.

Рассмотрим коды для цикла обновления ниже.

### <a name="appupdate-method"></a>Метод App::Update

Структура конечного автомата, использующегося для обновления игрового модуля

```cpp
void GameMain::Update()
{
    m_controller->Update(); //the controller instance has its own update loop.

    switch (m_updateState)
    {
    case UpdateEngineState::WaitingForResources:
        //...
        break;

    case UpdateEngineState::ResourcesLoaded:
        //...
        break;

    case UpdateEngineState::WaitingForPress:
        if (m_controller->IsPressComplete())
        {
            //...
        }
        break;

    case UpdateEngineState::Dynamics:
        if (m_controller->IsPauseRequested())
        {
            //...
        }
        else
        {
            GameState runState = m_game->RunGame(); //when the player is playing, the work is handled by this Simple3DGame::RunGame method.
            switch (runState)
            {
            case GameState::TimeExpired:
                //...
                break;

            case GameState::LevelComplete:
                //...
                break;

            case GameState::GameComplete:
                //...
                break;
            }
        }

        if (m_updateState == UpdateEngineState::WaitingForPress)
        {
            // Transitioning state, so enable waiting for the press event
            m_controller->WaitForPress(m_renderer->GameInfoOverlayUpperLeft(), m_renderer->GameInfoOverlayLowerRight());
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

## <a name="update-user-interface"></a>Обновление пользовательского интерфейса

Чтобы состояние могло меняться в зависимости от действий игрока и в соответствии с правилами игры, его необходимо информировать о состоянии системы. Многие игры, включая этот пример игры, обычно используют элементы пользовательского интерфейса (UI) для отображения этой информации игроку. Пользовательский интерфейс содержит представления состояния игры и другие касающиеся игры сведения, такие как количество очков или боеприпасов либо количество оставшихся попыток. Пользовательский интерфейс также называют наложением, поскольку его визуализация выполняется отдельно от основного графического конвейера и он размещается поверх трехмерной проекции. Некоторые сведения в пользовательском интерфейсе также представлены в виде экранных элементов (HUD), которые позволяют пользователям получить эти сведения, не отрывая взгляда от области основного игрового процесса. В образце игры мы создали такое наложение при помощи интерфейсов API Direct2D. Это наложение также можно создать с использованием XAML, и такой процесс разъясняется в разделе [Расширение образца игры](tutorial-resources.md).

Пользовательский интерфейс имеет два компонента:

-   набор индикаторов, показывающий количество очков и содержащий сведения о текущем состоянии игрового процесса;
-   точечный рисунок паузы, представляющий собой черный прямоугольник с текстом, накладываемый в состоянии паузы/приостановки игры. Это наложение в игре описано ниже в разделе [Добавление пользовательского интерфейса](tutorial--adding-a-user-interface.md).

Неудивительно, что наложение также снабжено конечным автоматом и может отображать сообщение о начале уровня или завершении игры. В сущности оно представляет собой полотно для вывода различной информации о состоянии игры, отображаемой для играющего в случае паузы в игре или приостановки.

Отображаемое наложение может соответствовать одному из шести экранов в зависимости от состояния игры: 
1. Экран загрузки ресурсов в начале игры
2. Экран статистики игры
3. Экран с начальным сообщением уровня
4. Экран завершения игры, когда все пройдены без превышения максимального времени игры
5. Экран завершения игры, если время истекло
6. Экран меню паузы

Отделение пользовательского интерфейса от графического конвейера игры позволяет работать над ним независимо от модуля графической визуализации игры и значительно снижает сложность кода игры.

Порядок структурирования образцом игры конечного автомата наложения приведен ниже.

```cpp
void GameMain::SetGameInfoOverlay(GameInfoOverlayState state)
{
    m_gameInfoOverlayState = state;
    switch (state)
    {
    case GameInfoOverlayState::Loading:
        m_uiControl->SetGameLoading(m_loadingCount);
        break;

    case GameInfoOverlayState::GameStats:
        //...
        break;

    case GameInfoOverlayState::LevelStart:
        //...
        break;

    case GameInfoOverlayState::GameOverCompleted:
        //...
        break;

    case GameInfoOverlayState::GameOverExpired:
        //...
        break;

    case GameInfoOverlayState::Pause:
        //...
        break;
    }
}
```

## <a name="events-handling"></a>Обработка событий

Наш пример кода зарегистрировал в **Initialize**, **SetWindow** и **Load** в App.cpp несколько обработчиков специальных событий. Это важные события, которые должны быть функциональны, прежде чем можно будет добавить игровую механику или начать разработку графики. Эти события — основа правильной работы приложения UWP. Приложение UWP может быть активировано, деактивировано, может быть изменен его размер, приложение может быть прикреплено и откреплено, приостановлено или возобновлено в любое время. Следовательно, игра должна регистрировать такие события максимально быстро и обрабатывать их так, чтобы игровой процесс протекал для игрока плавно и предсказуемо.

Ниже приведены обработчики событий, используемые в этом примере игры, и события, которые они обрабатывают.

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
<td align="left">Обрабатывает <a href="https://msdn.microsoft.com/library/windows/apps/br225018"><strong>CoreApplicationView::Activated</strong></a>. Игровое приложение было выведено на передний план, поэтому активировано главное окно.</td>
</tr>
<tr class="even">
<td align="left">OnDpiChanged</td>
<td align="left">Обрабатывает <a href="https://docs.microsoft.com/uwp/api/windows.graphics.display.displayinformation#Windows_Graphics_Display_DisplayInformation_DpiChanged"><strong>Graphics::Display::DisplayInformation::DpiChanged</strong></a>. Разрешение экрана было изменено, и игровое приложение соответствующим образом перераспределяет ресурсы.
<div class="alert">
<strong>Примечание</strong>[<strong>CoreWindow</strong>] (https://msdn.microsoft.com/library/windows/desktop/hh404559) координаты находятся в (аппаратно-независимые пиксели) для [Direct2D](https://msdn.microsoft.com/library/windows/desktop/dd370987). В результате необходимо передать в Direct2D уведомление об изменениях разрешения для правильного отображения любых двумерных ресурсов или примитивов.
</div>
<div>
</div></td>
</tr>
<tr class="odd">
<td align="left">OnOrientationChanged</td>
<td align="left">Обрабатывает <a href="https://docs.microsoft.com/uwp/api/windows.graphics.display.displayinformation#Windows_Graphics_Display_DisplayInformation_OrientationChanged"><strong>Graphics::Display::DisplayInformation::OrientationChanged</strong></a>. Ориентация дисплея изменилась, и графические элементы необходимо обновить.</td>
</tr>
<tr class="even">
<td align="left">OnDisplayContentsInvalidated</td>
<td align="left">Обрабатывает <a href="https://docs.microsoft.com/uwp/api/windows.graphics.display.displayinformation#Windows_Graphics_Display_DisplayInformation_DisplayContentsInvalidated"><strong>Graphics::Display::DisplayInformation::DisplayContentsInvalidated</strong></a>. Изображение на экране следует обновить, и ваша игра должна быть повторно отрисована.</td>
</tr>
<tr class="odd">
<td align="left">OnResuming</td>
<td align="left">Обрабатывает <a href="https://msdn.microsoft.com/library/windows/apps/br205859"><strong>CoreApplication::Resuming</strong></a>. Игровое приложение восстанавливает игру из состояния приостановки.</td>
</tr>
<tr class="even">
<td align="left">OnSuspending</td>
<td align="left">Обрабатывает <a href="https://msdn.microsoft.com/library/windows/apps/br205860"><strong>CoreApplication::Suspending</strong></a>. Игровое приложение сохраняет свое состояние на диск. У него есть 5 секунд, чтобы сохранить состояние в хранилище.</td>
</tr>
<tr class="odd">
<td align="left">OnVisibilityChanged</td>
<td align="left">Обрабатывает <a href="https://msdn.microsoft.com/library/windows/apps/hh701591"><strong>CoreWindow::VisibilityChanged</strong></a>. Игровое приложение изменило видимость и стало видимым или невидимым из-за того, что отобразилось другое приложение.</td>
</tr>
<tr class="even">
<td align="left">OnWindowActivationChanged</td>
<td align="left">Обрабатывает <a href="https://msdn.microsoft.com/library/windows/apps/br208255"><strong>CoreWindow::Activated</strong></a>. Главное окно игрового приложения было деактивировано или активировано, поэтому оно должно удалить фокус и приостановить игру или восстановить фокус. В обоих случаях наложение показывает, что игра приостановлена.</td>
</tr>
<tr class="odd">
<td align="left">OnWindowClosed</td>
<td align="left">Обрабатывает <a href="https://msdn.microsoft.com/library/windows/apps/br208261"><strong>CoreWindow::Closed</strong></a>. Игровое приложение закрывает главное окно и приостанавливает игру.</td>
</tr>
<tr class="even">
<td align="left">OnWindowSizeChanged</td>
<td align="left">Обрабатывает <a href="https://msdn.microsoft.com/library/windows/apps/br208283"><strong>CoreWindow::SizeChanged</strong></a>. Игровое приложение перераспределяет графические ресурсы и наложение, чтобы подстроиться под изменение размера, а затем обновляет целевой объект прорисовки.</td>
</tr>
</tbody>
</table>

## <a name="next-steps"></a>Дальнейшие действия

В этом разделе мы рассмотрели, как выполняется управление общим потоком игры с помощью состояний игры, и отметили, что игра состоит из нескольких различных конечных автоматов. Также мы узнали, как обновлять пользовательский интерфейс и управлять обработчиками основных событий приложений. Теперь мы готовы подробно рассмотреть цикл отрисовки, игру и ее механику.
 
Другие компоненты, которые в совокупности составляют эту игру, можно посмотреть в любом порядке:
* [Определение основного игрового объекта](tutorial--defining-the-main-game-loop.md)
* [Платформа отрисовки I: введение в отрисовку](tutorial--assembling-the-rendering-pipeline.md)
* [Платформа отрисовки II: отрисовка игры](tutorial-game-rendering.md)
* [Добавление пользовательского интерфейса](tutorial--adding-a-user-interface.md)
* [Добавление элементов управления](tutorial--adding-controls.md)
* [Добавление звука](tutorial--adding-sound.md)