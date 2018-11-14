---
author: eliotcowley
title: Добавление средств ввода и взаимодействия в пример Marble Maze
description: Некоторые важные рекомендации, которые следует иметь в виду при работе с устройствами ввода.
ms.assetid: b946bf62-c0ca-f9ec-1a87-8195b89a5ab4
ms.author: elcowle
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp, игры, ввод, пример
ms.localizationpriority: medium
ms.openlocfilehash: 0b7e9a3f655b8be1b93334ed8decf9fe6fa8bbf2
ms.sourcegitcommit: 71e8eae5c077a7740e5606298951bb78fc42b22c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/13/2018
ms.locfileid: "6668377"
---
# <a name="adding-input-and-interactivity-to-the-marble-maze-sample"></a>Добавление средств ввода и взаимодействия в пример Marble Maze




Игры универсальной платформы Windows (UWP) работают на самых разных устройствах, таких как настольные компьютеры, ноутбуки и планшеты. Устройство может располагать разнообразными механизмами ввода и управления. В данном документе приведены ключевые рекомендации, которые стоит учитывать при работе с устройствами ввода, и показано применение этих рекомендаций на примере приложения Marble Maze.

> [!NOTE]
> Пример кода, соответствующего этому документу, см. в [примере игры DirectX Marble Maze](http://go.microsoft.com/fwlink/?LinkId=624011).

 
Вот ключевые моменты, которые касаются работы с вводом в игре и рассматриваются в этом документе.

-   По возможности обеспечьте поддержку нескольких устройств ввода, чтобы ваша игра могла предоставить пользователям более широкий диапазон параметров и возможностей. Хотя использовать игровые устройства управления и датчики не обязательно, мы настоятельно рекомендуем это делать, чтобы улучшить взаимодействие с пользователем. Мы создали API-интерфейсы игровых устройств управления и датчиков, чтобы облегчить вам интеграцию этих устройств ввода.

-   Чтобы инициализировать сенсорный ввод, необходимо зарегистрироваться на события окна при активации, освобождении и перемещении указателя. Чтобы инициализировать акселерометр, создайте объект [Windows::Devices::Sensors::Accelerometer](https://msdn.microsoft.com/library/windows/apps/br225687) при инициализации приложения. Контроллер Xbox не требует инициализации.

-   Если игрок один, подумайте, не стоит ли объединить ввод со всех возможных контроллеров Xbox. При этом вам не придется отслеживать, какой ввод поступает от какого контроллера. Или можно просто отслеживать ввод только с последнего добавленного контроллера, как в этом примере.

-   Обрабатывайте события Windows до обработки устройств ввода.

-   Контроллер Xbox и акселерометр поддерживают опрос. Это значит, что вы можете опрашивать эти устройства на предмет данных, когда они вам нужны. При сенсорном вводе регистрируйте события касания в структурах данных, доступных вашему коду обработки ввода.

-   Подумайте о преобразовании введенных значений в общий формат. Это облегчит интерпретацию ввода другими компонентами игры, такими как моделирование физической среды, и упростит написание игр, работающих с разными разрешениями экрана.

## <a name="input-devices-supported-by-marble-maze"></a>Устройства ввода, поддерживаемые приложением Marble Maze


Marble Maze поддерживает стандартный контроллер Xbox, мышь и касание для выбора пунктов меню, а также контроллер Xbox, мышь, касание и акселерометр для управления игровым процессом. Для опроса контроллеров на предмет входных данных в Marble Maze используются API-интерфейсы [Windows::Gaming::Input](https://docs.microsoft.com/uwp/api/windows.gaming.input). Сенсорный ввод дает приложениям возможность отслеживать и реагировать на ввод с помощью пальцев. Акселерометр — это датчик, который измеряет силу, применяемую по осям X, Y и Z. С помощью среды выполнения Windows можно опрашивать текущее состояние акселерометра, а также получать события касания через механизм обработки событий среды выполнения Windows.

> [!NOTE]
> В данном документе термин «сенсорный ввод» используется применительно к касанию и мыши, а термин «указатель» — применительно к любому устройству, которое использует события указателя. Так как сенсорный ввод и мышь используют стандартные события указателя, для выбора пунктов меню и управления ходом игры можно использовать любое устройство.

 

> [!NOTE]
> Манифест пакета устанавливает **альбомную** ориентацию в качестве единственной поддерживаемой для игры во избежание смены ориентации, когда игрок поворачивает устройство для перекатывания шарика. Для просмотра манифеста пакета откройте **Package.appxmanifest** в **Обозревателе решений** Visual Studio.

 

## <a name="initializing-input-devices"></a>Инициализация устройств ввода


Контроллер Xbox не требует инициализации. Чтобы инициализировать сенсорный ввод, необходимо зарегистрироваться на события окна, такие как активация (например, когда игрок нажимает кнопку мыши или касается экрана), освобождение или перемещение указателя. Чтобы инициализировать акселерометр, необходимо создать объект [Windows::Devices::Sensors::Accelerometer](https://msdn.microsoft.com/library/windows/apps/br225687) при инициализации приложения.

В следующем примере показано, как метод **App::SetWindow** регистрируется на события указателя [Windows::UI::Core::CoreWindow::PointerPressed](https://docs.microsoft.com/uwp/api/windows.ui.core.corewindow.PointerPressed), [Windows::UI::Core::CoreWindow::PointerReleased](https://docs.microsoft.com/uwp/api/windows.ui.core.corewindow.PointerReleased) и [Windows::UI::Core::CoreWindow::PointerMoved](https://docs.microsoft.com/uwp/api/windows.ui.core.corewindow.PointerMoved). Эти события регистрируются во время инициализации приложения и до начала игрового цикла.

События обрабатываются в отдельном потоке, который вызывает обработчики событий.

Подробнее о способах инициализации приложения см. в статье [Структура приложения Marble Maze](marble-maze-application-structure.md).

```cpp
window->PointerPressed += ref new TypedEventHandler<CoreWindow^, PointerEventArgs^>(
    this, 
    &App::OnPointerPressed);

window->PointerReleased += ref new TypedEventHandler<CoreWindow^, PointerEventArgs^>(
    this, 
    &App::OnPointerReleased);

window->PointerMoved += ref new TypedEventHandler<CoreWindow^, PointerEventArgs^>(
    this, 
    &App::OnPointerMoved);
```

Класс **MarbleMazeMain** также создает объект **std::map** для хранения событий касания. Ключом для этого объекта-сопоставления является значение, уникально идентифицирующее указатель ввода. Каждый ключ сопоставляется с расстоянием между каждой точкой касания и центром экрана. Позже Marble Maze использует эти значения для расчета величины наклона лабиринта.

```cpp
typedef std::map<int, XMFLOAT2> TouchMap;
TouchMap        m_touches;
```

Класс **MarbleMazeMain** также содержит объект [Accelerometer](https://docs.microsoft.com/uwp/api/Windows.Devices.Sensors.Accelerometer).

```cpp
Windows::Devices::Sensors::Accelerometer^           m_accelerometer;
```

Объект **Accelerometer** инициируется в конструкторе **MarbleMazeMain**, как показано в следующем примере. Метод [Windows::Devices::Sensors::Accelerometer::GetDefault](https://docs.microsoft.com/uwp/api/Windows.Devices.Sensors.Accelerometer.GetDefault) возвращает экземпляр акселерометра по умолчанию. Если акселерометр по умолчанию отсутствует, **Accelerometer::GetDefault** возвращает **nullptr**.

```cpp
// Returns accelerometer ref if there is one; nullptr otherwise.
m_accelerometer = Windows::Devices::Sensors::Accelerometer::GetDefault();
```

##  <a name="navigating-the-menus"></a>Навигация по пунктам меню

Для навигации по пунктам меню можно использовать мышь, сенсорный ввод или контроллер Xbox следующим образом:

-   Используйте планшет контроллера, чтобы изменить активный пункт меню.
-   Используйте касание, клавишу A или кнопку "Меню" для выбора пунктов меню или закрытия текущего меню, например списка лучших результатов.
-   Используйте кнопку "Меню" для приостановки и возобновления игры.
-   Щелкните пункт меню мышью, чтобы выбрать это действие.

###  <a name="tracking-xbox-controller-input"></a>Отслеживание ввода контроллера Xbox

Для отслеживания геймпадов, в данный момент подключенных к устройству, в **MarbleMazeMain** определена переменная-член **m_myGamepads**, которая представляет коллекцию объектов [Windows::Gaming::Input::Gamepad](https://docs.microsoft.com/uwp/api/windows.gaming.input.gamepad). Она инициализируется в конструкторе следующим образом:

```cpp
m_myGamepads = ref new Vector<Gamepad^>();

for (auto gamepad : Gamepad::Gamepads)
{
    m_myGamepads->Append(gamepad);
}
```

Кроме того, конструктор **MarbleMazeMain** регистрирует события для добавления и удаления геймпадов:

```cpp
Gamepad::GamepadAdded += 
    ref new EventHandler<Gamepad^>([=](Platform::Object^, Gamepad^ args)
{
    m_myGamepads->Append(args);
    m_currentGamepadNeedsRefresh = true;
});

Gamepad::GamepadRemoved += 
    ref new EventHandler<Gamepad ^>([=](Platform::Object^, Gamepad^ args)
{
    unsigned int indexRemoved;

    if (m_myGamepads->IndexOf(args, &indexRemoved))
    {
        m_myGamepads->RemoveAt(indexRemoved);
        m_currentGamepadNeedsRefresh = true;
    }
});
```

При добавлении геймпада он добавляется в коллекцию **m_myGamepads**; при удалении геймпада мы проверяем, есть ли геймпад в коллекции **m_myGamepads**, и если да, удаляем его. В обоих случаях мы устанавливаем флаг **m_currentGamepadNeedsRefresh** в значение **true**, чтобы указать, что необходимо переназначить переменную **m_gamepad**.

Наконец, мы назначаем геймпад переменной **m_gamepad** и устанавливаем флаг **m_currentGamepadNeedsRefresh** в значение **false**:

```cpp
m_gamepad = GetLastGamepad();
m_currentGamepadNeedsRefresh = false;
```

В методе **Update** мы проверяем, нужно ли переназначить переменную **m_gamepad**:

```cpp
if (m_currentGamepadNeedsRefresh)
{
    auto mostRecentGamepad = GetLastGamepad();

    if (m_gamepad != mostRecentGamepad)
    {
        m_gamepad = mostRecentGamepad;
    }

    m_currentGamepadNeedsRefresh = false;
}
```

Если переменную **m_gamepad** нужно переназначить, мы назначаем ей последний добавленный геймпад с помощью метода **GetLastGamepad**, который определяется следующим образом:

```cpp
Gamepad^ MarbleMaze::MarbleMazeMain::GetLastGamepad()
{
    Gamepad^ gamepad = nullptr;

    if (m_myGamepads->Size > 0)
    {
        gamepad = m_myGamepads->GetAt(m_myGamepads->Size - 1);
    }

    return gamepad;
}
```

Этот метод просто возвращает последний гейпад в коллекции **m_myGamepads**.

К устройству с Windows 10 можно подключить до четырех контроллеров Xbox. Во избежание необходимости выяснять, какой из контроллеров является активным, мы просто отслеживаем последний добавленный контроллер. Если в вашу игру могут играть несколько игроков, отслеживать ввод необходимо отдельно для каждого игрока.

Метод **MarbleMazeMain::Update** опрашивает контроллер на предмет ввода:

```cpp
if (m_gamepad != nullptr)
{
    m_oldReading = m_newReading;
    m_newReading = m_gamepad->GetCurrentReading();
}
```

Мы отслеживаем считанный ввод, полученный в последнем кадре, с помощью переменной **m_oldReading**, и последний считанный ввод с помощью переменной **m_newReading**, значения которых получаем путем вызова метода [Gamepad::GetCurrentReading](https://docs.microsoft.com/uwp/api/windows.gaming.input.gamepad.GetCurrentReading). Этот метод возвращает объект [GamepadReading](https://docs.microsoft.com/uwp/api/windows.gaming.input.gamepadreading), содержащий информацию о текущем состоянии геймпада.

Чтобы проверить, была ли кнопка только нажата или отпущена, мы определяем методы **MarbleMazeMain::ButtonJustPressed** и **MarbleMazeMain::ButtonJustReleased**, которые сравнивают считанные состояния кнопки из этого кадра и последнего кадра. Так мы можем выполнять действие, только когда кнопка изначально нажимается или отпускается, но не когда она удерживается:

```cpp
bool MarbleMaze::MarbleMazeMain::ButtonJustPressed(GamepadButtons selection)
{
    bool newSelectionPressed = (selection == (m_newReading.Buttons & selection));
    bool oldSelectionPressed = (selection == (m_oldReading.Buttons & selection));
    return newSelectionPressed && !oldSelectionPressed;
}

bool MarbleMaze::MarbleMazeMain::ButtonJustReleased(GamepadButtons selection)
{
    bool newSelectionReleased = 
        (GamepadButtons::None == (m_newReading.Buttons & selection));

    bool oldSelectionReleased = 
        (GamepadButtons::None == (m_oldReading.Buttons & selection));

    return newSelectionReleased && !oldSelectionReleased;
}
```

Считанные значения перечисления [GamepadButtons](https://docs.microsoft.com/uwp/api/windows.gaming.input.gamepadbuttons) сравниваются с помощью побитовых операций &mdash; мы проверяем, нажата ли кнопка, используя *побитовую операцию AND* (&). Мы определяем, была ли кнопка просто нажата или отпущена, сравнивая старое считанное значение и новое считанное значение.

Используя описанные выше способы, мы проверяем, были ли нажаты определенные кнопки, и выполняем соответствующие действия. Например, при нажатии кнопки "Меню" (**GamepadButtons::Menu**) состояние игры меняется с активного на приостановленное или наоборот.

```cpp
if (ButtonJustPressed(GamepadButtons::Menu) || m_pauseKeyPressed)
{
    m_pauseKeyPressed = false;

    if (m_gameState == GameState::InGameActive)
    {
        SetGameState(GameState::InGamePaused);
    }  
    else if (m_gameState == GameState::InGamePaused)
    {
        SetGameState(GameState::InGameActive);
    }
}
```

Мы также проверяем, нажал ли игрок кнопку "Просмотр". В этом случае мы перезапускаем игру или очищаем список лучших результатов:

```cpp
if (ButtonJustPressed(GamepadButtons::View) || m_homeKeyPressed)
{
    m_homeKeyPressed = false;

    if (m_gameState == GameState::InGameActive ||
        m_gameState == GameState::InGamePaused ||
        m_gameState == GameState::PreGameCountdown)
    {
        SetGameState(GameState::MainMenu);
        m_inGameStopwatchTimer.SetVisible(false);
        m_preGameCountdownTimer.SetVisible(false);
    }
    else if (m_gameState == GameState::HighScoreDisplay)
    {
        m_highScoreTable.Reset();
    }
}
```

Если активно главное меню, активный пункт меню изменяется при нажатии планшета контроллера вверх или вниз. Если пользователь выбирает текущий раздел, соответствующий элемент пользовательского интерфейса помечается как выбранный.

```cpp
// Handle menu navigation.
bool chooseSelection = 
    (ButtonJustPressed(GamepadButtons::A) 
    || ButtonJustPressed(GamepadButtons::Menu));

bool moveUp = ButtonJustPressed(GamepadButtons::DPadUp);
bool moveDown = ButtonJustPressed(GamepadButtons::DPadDown);

switch (m_gameState)
{
case GameState::MainMenu:
    if (chooseSelection)
    {
        m_audio.PlaySoundEffect(MenuSelectedEvent);
        if (m_startGameButton.GetSelected())
        {
            m_startGameButton.SetPressed(true);
        }
        if (m_highScoreButton.GetSelected())
        {
            m_highScoreButton.SetPressed(true);
        }
    }
    if (moveUp || moveDown)
    {
        m_startGameButton.SetSelected(!m_startGameButton.GetSelected());
        m_highScoreButton.SetSelected(!m_startGameButton.GetSelected());
        m_audio.PlaySoundEffect(MenuChangeEvent);
    }
    break;

case GameState::HighScoreDisplay:
    if (chooseSelection || anyPoints)
    {
        SetGameState(GameState::MainMenu);
    }
    break;

case GameState::PostGameResults:
    if (chooseSelection || anyPoints)
    {
        SetGameState(GameState::HighScoreDisplay);
    }
    break;

case GameState::InGamePaused:
    if (m_pausedText.IsPressed())
    {
        m_pausedText.SetPressed(false);
        SetGameState(GameState::InGameActive);
    }
    break;
}
```

### <a name="tracking-touch-and-mouse-input"></a>Отслеживание сенсорного ввода и ввода с помощью мыши

При вводе с помощью сенсорного экрана или мыши выбор пункта меню происходит, когда пользователь касается или щелкает его. В следующем примере показано, как метод **MarbleMazeMain::Update** обрабатывает ввод указателя для выбора пункта меню. Переменная-член **m\_pointQueue** отслеживает расположение, в котором пользователь коснулся экрана или щелкнул по нему. Подробнее о том, как Marble Maze собирает ввод указателя, рассматривается далее в этом документе, в разделе [Обработка ввода указателя](#processing-pointer-input).

```cpp
// Check whether the user chose a button from the UI. 
bool anyPoints = !m_pointQueue.empty();
while (!m_pointQueue.empty())
{
    UserInterface::GetInstance().HitTest(m_pointQueue.front());
    m_pointQueue.pop();
}
```

Метод **UserInterface::HitTest** определяет, находится ли предоставленная точка в границах какого-либо элемента пользовательского интерфейса. Любые элементы пользовательского интерфейса, которые проходят эту проверку, помечаются как нажатые. Этот метод использует вспомогательную функцию **PointInRect**, чтобы определить, находится ли предоставленная точка в границах какого-либо элемента пользовательского интерфейса.

```cpp
void UserInterface::HitTest(D2D1_POINT_2F point)
{
    for (auto iter = m_elements.begin(); iter != m_elements.end(); ++iter)
    {
        if (!(*iter)->IsVisible())
            continue;

        TextButton* textButton = dynamic_cast<TextButton*>(*iter);
        if (textButton != nullptr)
        {
            D2D1_RECT_F bounds = (*iter)->GetBounds();
            textButton->SetPressed(PointInRect(point, bounds));
        }
    }
}
```

### <a name="updating-the-game-state"></a>Обновление состояния игры

После обработки ввода с контроллера и сенсорного ввода метод **MarbleMazeMain::Update** обновляет состояние игры, если была нажата какая-либо кнопка.

```cpp
// Update the game state if the user chose a menu option. 
if (m_startGameButton.IsPressed())
{
    SetGameState(GameState::PreGameCountdown);
    m_startGameButton.SetPressed(false);
}
if (m_highScoreButton.IsPressed())
{
    SetGameState(GameState::HighScoreDisplay);
    m_highScoreButton.SetPressed(false);
}
```

##  <a name="controlling-game-play"></a>Управление игровым процессом


Игровой цикл и метод **MarbleMazeMain::Update** совместно обновляют состояние игровых объектов. Если ваша игра поддерживает ввод с нескольких устройств, вы можете собрать данные ввода со всех устройств в один набор переменных, чтобы написать код, который проще поддерживать. Метод **MarbleMazeMain::Update** определяет один набор переменных, который накапливает перемещения со всех устройств.

```cpp
float combinedTiltX = 0.0f;
float combinedTiltY = 0.0f;
```

Механизмы ввода разных устройств ввода могут различаться. Например, ввод указателя обрабатывается моделью обработчика событий среды выполнения Windows. И наоборот, контроллер Xbox вы опрашиваете на наличие данных ввода по мере необходимости. Мы рекомендуем всегда использовать механизмы ввода, стандартные для конкретного устройства. В этом разделе описано, как Marble Maze считывает данные ввода с каждого устройства, обновляет комбинированные значения ввода и использует эти значения для обновления состояния игры.

###  <a name="processing-pointer-input"></a>Обработка ввода указателя

При работе с вводом указателя необходимо вызвать метод [Windows::UI::Core::CoreDispatcher::ProcessEvents](https://docs.microsoft.com/uwp/api/windows.ui.core.coredispatcher.processevents) для обработки событий окна. Вызывайте этот метод в игровом цикле до обновления или отрисовки сцены. Marble Maze вызывает его в методе **App::Run**: 

```cpp
while (!m_windowClosed)
{
    if (m_windowVisible)
    {
        CoreWindow::GetForCurrentThread()->
            Dispatcher->ProcessEvents(CoreProcessEventsOption::ProcessAllIfPresent);

        m_main->Update();

        if (m_main->Render())
        {
            m_deviceResources->Present();
        }
    }
    else
    {
        CoreWindow::GetForCurrentThread()->
            Dispatcher->ProcessEvents(CoreProcessEventsOption::ProcessOneAndAllPending);
    }
}
```

Если окно отображается, мы передаем **CoreProcessEventsOption::ProcessAllIfPresent** методу **ProcessEvents**, чтобы он обработал все события в очереди и сразу же вернул управление; в противном случае мы передаем **CoreProcessEventsOption::ProcessOneAndAllPending**, чтобы он обработал все события в очереди и дожидался следующего нового события. После обработки событий Marble Maze отрисовывает и представляет следующий кадр.

Среда выполнения Windows вызывает зарегистрированный обработчик для каждого произошедшего события. Метод **App::SetWindow** регистрируется на события и направляет информацию об указателе классу **MarbleMazeMain**.

```cpp
void App::OnPointerPressed(
    Windows::UI::Core::CoreWindow^ sender, 
    Windows::UI::Core::PointerEventArgs^ args)
{
    m_main->AddTouch(args->CurrentPoint->PointerId, args->CurrentPoint->Position);
}

void App::OnPointerReleased(
    Windows::UI::Core::CoreWindow^ sender, 
    Windows::UI::Core::PointerEventArgs^ args)
{
    m_main->RemoveTouch(args->CurrentPoint->PointerId);
}

void App::OnPointerMoved(
    Windows::UI::Core::CoreWindow^ sender, 
    Windows::UI::Core::PointerEventArgs^ args)
{
    m_main->UpdateTouch(args->CurrentPoint->PointerId, args->CurrentPoint->Position);
}
```

Класс **MarbleMazeMain** реагирует на события указателя, обновляя объект карты, который содержит события касания. Метод **MarbleMazeMain::AddTouch** вызывается при первом нажатии указателя, например когда пользователь впервые касается экрана сенсорного устройства. Метод **MarbleMazeMain::UpdateTouch** вызывается при изменении положения указателя. Метод **MarbleMazeMain::RemoveTouch** вызывается при отпускании указателя, например когда пользователь перестает касаться экрана.

```cpp
void MarbleMazeMain::AddTouch(int id, Windows::Foundation::Point point)
{
    m_touches[id] = PointToTouch(point, m_deviceResources->GetLogicalSize());

    m_pointQueue.push(D2D1::Point2F(point.X, point.Y));
}

void MarbleMazeMain::UpdateTouch(int id, Windows::Foundation::Point point)
{
    if (m_touches.find(id) != m_touches.end())
        m_touches[id] = PointToTouch(point, m_deviceResources->GetLogicalSize());
}

void MarbleMazeMain::RemoveTouch(int id)
{
    m_touches.erase(id);
}
```

Функция **PointToTouch** преобразовывает текущее положение указателя так, чтобы исходное положение было в центре экрана, а затем масштабирует координаты так, чтобы они находились в диапазоне примерно от –1,0 до +1,0. Это облегчает единообразный расчет наклона лабиринта при различных способах ввода.

```cpp
inline XMFLOAT2 PointToTouch(Windows::Foundation::Point point, Windows::Foundation::Size bounds)
{
    float touchRadius = min(bounds.Width, bounds.Height);
    float dx = (point.X - (bounds.Width / 2.0f)) / touchRadius;
    float dy = ((bounds.Height / 2.0f) - point.Y) / touchRadius;

    return XMFLOAT2(dx, dy);
}
```

Метод **MarbleMazeMain::Update** обновляет объединенные значения ввода путем увеличения коэффициента наклона на постоянное значение масштабирования. Значение масштабирования определяется при помощи экспериментов с несколькими различными значениями.

```cpp
// Account for touch input.
for (TouchMap::const_iterator iter = m_touches.cbegin(); 
    iter != m_touches.cend(); 
    ++iter)
{
    combinedTiltX += iter->second.x * m_touchScaleFactor;
    combinedTiltY += iter->second.y * m_touchScaleFactor;
}
```

### <a name="processing-accelerometer-input"></a>Обработка ввода акселерометра

Для обработки ввода акселерометра метод **MarbleMazeMain::Update** вызывает метод [Windows::Devices::Sensors::Accelerometer::GetCurrentReading](https://msdn.microsoft.com/library/windows/apps/br225699). Этот метод возвращает объект [Windows::Devices::Sensors::AccelerometerReading](https://msdn.microsoft.com/library/windows/apps/br225688), который представляет показания акселерометра. В свойствах **Windows::Devices::Sensors::AccelerometerReading::AccelerationX** и **Windows::Devices::Sensors::AccelerometerReading::AccelerationY** хранится ускорение силы тяжести по осям X и Y соответственно.

Следующий пример показывает, как метод **MarbleMazeMain::Update** опрашивает акселерометр и обновляет объединенные значения ввода. Когда вы наклоняете устройство, сила тяжести заставляет шарик двигаться быстрее.

```cpp
// Account for sensors.
if (m_accelerometer != nullptr)
{
    Windows::Devices::Sensors::AccelerometerReading^ reading =
        m_accelerometer->GetCurrentReading();

    if (reading != nullptr)
    {
        combinedTiltX += 
            static_cast<float>(reading->AccelerationX) * m_accelerometerScaleFactor;

        combinedTiltY += 
            static_cast<float>(reading->AccelerationY) * m_accelerometerScaleFactor;
    }
}
```

Так как вы не знаете, есть ли на компьютере пользователя акселерометр, перед опросом акселерометра всегда проверяйте наличие действительного объекта [Accelerometer](https://docs.microsoft.com/uwp/api/Windows.Devices.Sensors.Accelerometer).

### <a name="processing-xbox-controller-input"></a>Обработка ввода контроллера Xbox

В методе **MarbleMazeMain::Update** переменная **m_newReading** используется для обработки ввода с левого аналогового стика:

```cpp
float leftStickX = static_cast<float>(m_newReading.LeftThumbstickX);
float leftStickY = static_cast<float>(m_newReading.LeftThumbstickY);

auto oppositeSquared = leftStickY * leftStickY;
auto adjacentSquared = leftStickX * leftStickX;

if ((oppositeSquared + adjacentSquared) > m_deadzoneSquared)
{
    combinedTiltX += leftStickX * m_controllerScaleFactor;
    combinedTiltY += leftStickY * m_controllerScaleFactor;
}
```

Мы проверяем, находится ли ввод с левого аналогового стика за пределами мертвой зоны, и если да, мы добавляем его к переменным **combinedTiltX** и **combinedTiltY** (с умножением на коэффициент масштабирования), чтобы наклонить сцену.

> [!IMPORTANT]
> При работе с контроллером Xbox всегда принимайте во внимание мертвую зону. Мертвая зона отражает разброс чувствительности игровых планшетов к начальному движению. Для некоторых контроллеров при небольшом перемещении показания отсутствуют. В других контроллерах даже небольшое движение может стать основанием для измеряемого показателя. Чтобы учесть это, создайте зону отсутствия движения для начального перемещения аналогового стика. Подробнее о мертвой зоне см. в статье [Считывание данных с аналоговых стиков](gamepad-and-vibration.md#reading-the-thumbsticks).

 

###  <a name="applying-input-to-the-game-state"></a>Применение ввода к состоянию игры

Устройства передают значения ввода разными способами. Например, ввод указателя может быть в координатах экрана, а ввод контроллера — в абсолютно другом формате. Одна из основных сложностей при комбинировании данных ввода от нескольких устройств в один набор значений ввода— нормализация, то есть преобразование значений в общий формат. Marble Maze нормализует значения, масштабируя их в диапазоне \[–1,0, 1,0\]. Функция **PointToTouch**, описанная ранее в этом разделе, преобразовывает экранные координаты в нормализованные значения в диапазоне примерно от –1,0 до +1,0.

> [!TIP]
> Даже если ваше приложение использует только один метод ввода, мы рекомендуем всегда нормализовать значения ввода. Это облегчит интерпретацию ввода другими компонентами игры, такими как моделирование физической среды, и упростит написание игр, работающих с разными разрешениями экрана.

 

После обработки ввода метод **MarbleMazeMain::Update** создает вектор, который представляет влияние наклона лабиринта на шарик. Следующий пример показывает, как Marble Maze использует функцию [XMVector3Normalize](https://msdn.microsoft.com/library/windows/desktop/microsoft.directx_sdk.geometric.xmvector3normalize) для создания нормализованного вектора силы тяжести. Переменная **maxTilt** ограничивает величину наклона лабиринта и не дает ему упасть набок.

```cpp
const float maxTilt = 1.0f / 8.0f;

XMVECTOR gravity = XMVectorSet(
    combinedTiltX * maxTilt, 
    combinedTiltY * maxTilt, 
    1.0f, 
    0.0f);

gravity = XMVector3Normalize(gravity);
```

Чтобы завершить обновление объектов сцены, Marble Maze передает обновленный вектор силы тяжести в моделирование физической среды, обновляет моделирование физической среды на время, прошедшее с предыдущего кадра, и обновляет положение и ориентацию шарика в пространстве. Если шарик упал в отверстие лабиринта, метод **MarbleMazeMain::Update** вновь помещает шарик в последнюю контрольную точку, которой коснулся шарик, и сбрасывает состояние моделирования физической среды.

```cpp
XMFLOAT3A g;
XMStoreFloat3(&g, gravity);
m_physics.SetGravity(g);

if (m_gameState == GameState::InGameActive)
{
    // Only update physics when gameplay is active.
    m_physics.UpdatePhysicsSimulation(static_cast<float>(m_timer.GetElapsedSeconds()));

    // ...Code omitted for simplicity...

}

// ...Code omitted for simplicity...

// Check whether the marble fell off of the maze. 
const float fadeOutDepth = 0.0f;
const float resetDepth = 80.0f;
if (marblePosition.z >= fadeOutDepth)
{
    m_targetLightStrength = 0.0f;
}
if (marblePosition.z >= resetDepth)
{
    // Reset marble.
    memcpy(&marblePosition, &m_checkpoints[m_currentCheckpoint], sizeof(XMFLOAT3));
    oldMarblePosition = marblePosition;
    m_physics.SetPosition((const XMFLOAT3&)marblePosition);
    m_physics.SetVelocity(XMFLOAT3(0, 0, 0));
    m_lightStrength = 0.0f;
    m_targetLightStrength = 1.0f;

    m_resetCamera = true;
    m_resetMarbleRotation = true;
    m_audio.PlaySoundEffect(FallingEvent);
}
```

Этот раздел не описывает принципы работы моделирования физической среды. Подробнее о них можно узнать из файлов **Physics.h** и **Physics.cpp** в исходном коде Marble Maze.

## <a name="next-steps"></a>Следующие действия


Прочитайте раздел [Добавление звука в пример Marble Maze](adding-audio-to-the-marble-maze-sample.md), чтобы получить представление о рекомендациях, которым стоит следовать при работе со звуком. В документе описывается, как Marble Maze использует Microsoft Media Foundation и XAudio2 для загрузки, микширования и воспроизведения звуковых ресурсов.

## <a name="related-topics"></a>Связанные темы


* [Добавление звука в образец Marble Maze](adding-audio-to-the-marble-maze-sample.md)
* [Добавление визуального содержимого в образец Marble Maze](adding-visual-content-to-the-marble-maze-sample.md)
* [Разработка Marble Maze — игры UWP — на C++ и DirectX](developing-marble-maze-a-windows-store-game-in-cpp-and-directx.md)

 

 




