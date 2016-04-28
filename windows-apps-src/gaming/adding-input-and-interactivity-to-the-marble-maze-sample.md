---
title: Ввод и интерактивные функции в образце Marble Maze
description: Игровые приложения универсальной платформы Windows (UWP) работают на самых разных устройствах, таких как настольные компьютеры, ноутбуки и планшеты.
ms.assetid: b946bf62-c0ca-f9ec-1a87-8195b89a5ab4
---

# Добавление ввода и интерактивной функциональности в образец Marble Maze


\[ Обновлено для приложений UWP в Windows 10. Статьи о Windows 8.x см. в [архиве](http://go.microsoft.com/fwlink/p/?linkid=619132) \]


Игровые приложения универсальной платформы Windows (UWP) работают на самых разных устройствах, таких как настольные компьютеры, ноутбуки и планшеты. Устройство может располагать разнообразными механизмами ввода и управления. Обеспечьте поддержку нескольких устройств ввода, чтобы ваша игра могла предоставить пользователям более широкий диапазон параметров и возможностей. В данном документе приведены ключевые рекомендации, которые стоит учитывать при работе с устройствами ввода, и показано применение этих рекомендаций на примере приложения Marble Maze.

> **Примечание** Пример кода, соответствующий этому документу, можно найти в [примере игры DirectX Marble Maze](http://go.microsoft.com/fwlink/?LinkId=624011).

 
Вот ключевые моменты, которые касаются работы с вводом в игре и рассматриваются в этом документе.

-   По возможности обеспечьте поддержку нескольких устройств ввода, чтобы ваша игра могла предоставить пользователям более широкий диапазон параметров и возможностей. Хотя использование игрового устройства управления и датчика не является обязательным, мы настоятельно рекомендуем их, чтобы улучшить взаимодействие с пользователем. Мы создали API  игрового устройства управления и датчика, чтобы облегчить вам интеграцию этих устройств ввода.
-   Чтобы инициализировать сенсорный ввод, необходимо зарегистрироваться на события окна при активации, освобождении и перемещении указателя. Чтобы инициализировать акселерометр, создайте объект [**Windows::Devices::Sensors::Accelerometer**](https://msdn.microsoft.com/library/windows/apps/br225687) при инициализации приложения. Контроллер Xbox 360 не требует инициализации.
-   Если игрок один, подумайте о комбинировании ввода от всех возможных контроллеров Xbox 360. При этом вам не придется отслеживать, какой ввод поступает от какого контроллера.
-   Обрабатывайте события Windows до обработки устройств ввода.
-   Контроллер Xbox 360 и акселерометр поддерживают опрос. То есть вы можете запросить данные, когда они нужны. При сенсорном вводе регистрируйте события касания в структурах данных, доступных вашему коду обработки ввода.
-   Подумайте о преобразовании введенных значений в общий формат. Это облегчит интерпретацию ввода другими компонентами игры, такими как моделирование физической среды, и упростит написание игр, работающих с разными разрешениями экрана.

## Устройства ввода, поддерживаемые приложением Marble Maze


Marble Maze поддерживает стандартные контроллеры Xbox 360, мышь и касание для выбора пунктов меню, а также контроллер Xbox 360, мышь, касание и акселерометр для управления игрой. Marble Maze использует API XInput для опроса контроллера для ввода. Сенсорный ввод дает приложениям возможность отслеживать и реагировать на ввод с помощью пальцев. Акселерометр — это датчик, который измеряет силу, применяемую по осям x, y и z. С помощью среды выполнения Windows можно опрашивать текущее состояние акселерометра, а также получать события касания через механизм обработки событий среды выполнения Windows.

> **Примечание** В данном документе термин "сенсорный ввод" используется применительно к касанию и мыши, а термин "указатель" — применительно к любому устройству, которое использует события указателя. Так как касание и мышь используют стандартные события указателя, для выбора пунктов меню и управления ходом игры можно использовать любое устройство.

 

> **Примечание** Манифест пакета устанавливает альбомную ориентацию как поддерживаемую для игры, чтобы предотвратить смену ориентации при повороте устройства для перемещения шарика.

 

## Инициализация устройств ввода


Контроллер Xbox 360 не требует инициализации. Чтобы инициализировать сенсорный ввод, необходимо зарегистрироваться на события окна, такие как активация (например, когда пользователь нажимает кнопку мыши или касается экрана), освобождение или перемещение указателя. Чтобы инициализировать акселерометр, необходимо создать объект [**Windows::Devices::Sensors::Accelerometer**](https://msdn.microsoft.com/library/windows/apps/br225687) при инициализации приложения.

В следующем примере показано, как конструктор **DirectXPage** регистрируется для событий указателя [**Windows::UI::Core::CoreIndependentInputSource::PointerPressed**](https://msdn.microsoft.com/library/windows/apps/dn298471), [**Windows::UI::Core::CoreIndependentInputSource::PointerReleased**](https://msdn.microsoft.com/library/windows/apps/dn298472) и [**Windows::UI::Core::CoreIndependentInputSource::PointerMoved**](https://msdn.microsoft.com/library/windows/apps/dn298469) на панели [**SwapChainPanel**](https://msdn.microsoft.com/library/windows/apps/dn252834). Эти события регистрируются во время инициализации приложения и до начала цикла игры.

События обрабатываются в отдельном потоке, который вызывает обработчики событий.

Подробнее о способах инициализации приложения см. в разделе [Структура приложения Marble Maze](marble-maze-application-structure.md).

```cpp
coreInput->PointerPressed += ref new TypedEventHandler<Object^, PointerEventArgs^>(this, &DirectXPage::OnPointerPressed);
coreInput->PointerMoved += ref new TypedEventHandler<Object^, PointerEventArgs^>(this, &DirectXPage::OnPointerMoved);
coreInput->PointerReleased += ref new TypedEventHandler<Object^, PointerEventArgs^>(this, &DirectXPage::OnPointerReleased);
```

Класс MarbleMaze также создает объект std::map для удерживания событий касания. Ключом для этого назначенного объекта является значение, уникально идентифицирующее указатель ввода. Каждый ключ сопоставляется с расстоянием между каждой точкой касания и центром экрана. Позже Marble Maze использует эти значения для расчета степени наклона лабиринта.

```cpp
typedef std::map<int, XMFLOAT2> TouchMap;
TouchMap        m_touches;
```

Класс MarbleMaze содержит объект Accelerometer.

```cpp
Windows::Devices::Sensors::Accelerometer^           m_accelerometer;
```

Объект Accelerometer инициируется в методе MarbleMaze::Initialize, как показано в следующем примере. Метод Windows::Devices::Sensors::Accelerometer::GetDefault возвращает экземпляр акселерометра по умолчанию. Если акселерометр по умолчанию (Accelerometer::GetDefault) отсутствует, значение m\_accelerometer остается равным nullptr.

```cpp
// Returns accelerometer ref if there is one; nullptr otherwise.
m_accelerometer = Windows::Devices::Sensors::Accelerometer::GetDefault();
```

##  Навигация по пунктам меню


###  Отслеживание ввода контроллера Xbox 360

Для навигации по пунктам меню можно использовать мышь, сенсорный ввод или контроллер Xbox 360 следующим образом.

-   Используйте планшет контроллера, чтобы изменить активный пункт меню.
-   Используйте кнопку A или "Пуск" для выбора пунктов меню или закрытия текущего меню, например списка лучших результатов.
-   Используйте кнопку "Пуск" для приостановки и возобновления игры.
-   Щелкните пункт меню мышью, чтобы выбрать это действие.

###  Отслеживание ввода с использованием касаний и мыши

Чтобы отслеживать ввод контроллера Xbox 360, метод **MarbleMaze::Update** устанавливает массив кнопок, определяющих поведение ввода. XInput предоставляет только текущее состояние контроллера. Поэтому **MarbleMaze::Update** также определяет два массива, которые отслеживают для каждого возможного контроллера Xbox 360, была ли нажата каждая кнопка в предыдущем кадре и нажата ли каждая кнопка в данный момент.

```cpp
// This array contains the constants from XINPUT that map to the 
// particular buttons that are used by the game. 
// The index of the array is used to associate the state of that button in 
// the wasButtonDown, isButtonDown, and combinedButtonPressed variables. 

static const WORD buttons[] = {
    XINPUT_GAMEPAD_A,
    XINPUT_GAMEPAD_START,
    XINPUT_GAMEPAD_DPAD_UP,
    XINPUT_GAMEPAD_DPAD_DOWN,
    XINPUT_GAMEPAD_DPAD_LEFT,
    XINPUT_GAMEPAD_DPAD_RIGHT,
    XINPUT_GAMEPAD_BACK,
};

static const int buttonCount = ARRAYSIZE(buttons);
static bool wasButtonDown[XUSER_MAX_COUNT][buttonCount] = { false, };
bool isButtonDown[XUSER_MAX_COUNT][buttonCount] = { false, };
```

Вы можете подключить до четырех контроллеров Xbox 360 к устройству с Windows. Чтобы не нужно было выяснять, какой именно контроллер активен, метод **MarbleMaze::Update** комбинирует ввод со всех контроллеров.

```cpp
bool combinedButtonPressed[buttonCount] = { false, };
```

Если в вашу игру могут играть несколько игроков, отслеживать ввод необходимо отдельно для каждого игрока.

В ходе цикла метод **MarbleMaze::Update** опрашивает каждый контроллер на наличие ввода и считывает состояние каждой кнопки.

```cpp
// Account for input on any connected controller.
XINPUT_STATE inputState = {0};
for (DWORD userIndex = 0; userIndex < XUSER_MAX_COUNT; ++userIndex)
{
    DWORD result = XInputGetState(userIndex, &inputState);
    if(result != ERROR_SUCCESS) 
        continue;

    SHORT thumbLeftX = inputState.Gamepad.sThumbLX;
    if (abs(thumbLeftX) < XINPUT_GAMEPAD_LEFT_THUMB_DEADZONE) 
        thumbLeftX = 0;

    SHORT thumbLeftY = inputState.Gamepad.sThumbLY;
    if (abs(thumbLeftY) < XINPUT_GAMEPAD_LEFT_THUMB_DEADZONE) 
        thumbLeftY = 0;

    combinedTiltX += static_cast<float>(thumbLeftX) / 32768.0f;
    combinedTiltY += static_cast<float>(thumbLeftY) / 32768.0f;

    for (int i = 0; i < buttonCount; ++i)
        isButtonDown[userIndex][i] = (inputState.Gamepad.wButtons & buttons[i]) == buttons[i];
}
```

После опроса на наличие ввода метод **MarbleMaze::Update** обновляет комбинированный массив ввода. Комбинированный массив ввода отслеживает только нажатие кнопок, которые не были нажаты ранее. Это позволяет игре выполнять действие, только когда кнопка изначально нажимается, но не когда она удерживается.

```cpp
bool combinedButtonPressed[buttonCount] = { false, };
for (int i = 0; i < buttonCount; ++i)
{
    for (DWORD userIndex = 0; userIndex < XUSER_MAX_COUNT; ++userIndex)
    {
        bool pressed = !wasButtonDown[userIndex][i] && isButtonDown[userIndex][i];
        combinedButtonPressed[i] = combinedButtonPressed[i] || pressed;
    }
}
```

После сбора данных ввода с кнопок метод **MarbleMaze::Update** выполняет требуемые действия. Например, при нажатии кнопки на начальном экране (XINPUT\_GAMEPAD\_START) состояние игры изменяется с активного на приостановленное или наоборот.

```cpp
// Check whether the user paused or resumed the game. 
// XINPUT_GAMEPAD_START  
if (combinedButtonPressed[1] || m_pauseKeyPressed)
{
    m_pauseKeyPressed = false;
    if (m_gameState == GameState::InGameActive)
        SetGameState(GameState::InGamePaused);
    else if (m_gameState == GameState::InGamePaused)
        SetGameState(GameState::InGameActive);
}
```

Если активно главное меню, активный пункт меню изменяется при нажатии планшета контроллера вверх или вниз. Если пользователь выбирает текущий раздел, соответствующий элемент пользовательского интерфейса помечается как выбранный.

```cpp
// Handle menu navigation. 

// XINPUT_GAMEPAD_A or XINPUT_GAMEPAD_START 
bool chooseSelection = (combinedButtonPressed[0] || combinedButtonPressed[1]);

// XINPUT_GAMEPAD_DPAD_UP 
bool moveUp = combinedButtonPressed[2];

// XINPUT_GAMEPAD_DPAD_DOWN 
bool moveDown = combinedButtonPressed[3];                                           

switch (m_gameState)
{
case GameState::MainMenu:
    if (chooseSelection)
    {
        m_audio.PlaySoundEffect(MenuSelectedEvent);

        if (m_startGameButton.GetSelected())
            m_startGameButton.SetPressed(true);

        if (m_highScoreButton.GetSelected())
            m_highScoreButton.SetPressed(true);
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
        SetGameState(GameState::MainMenu);
    break;

case GameState::PostGameResults:
    if (chooseSelection || anyPoints)
        SetGameState(GameState::HighScoreDisplay);
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

После обработки ввода контроллера метод **MarbleMaze::Update** сохраняет текущее состояние ввода для следующего кадра.

```cpp
// Update the button state for the next frame.
memcpy(wasButtonDown, isButtonDown, sizeof(wasButtonDown));
```

### Отслеживание ввода с использованием касаний и мыши

При вводе с помощью касаний или мыши выбор пункта меню происходит, когда пользователь касается или щелкает его. В следующем примере показано, как метод **MarbleMaze::Update** обрабатывает ввод указателя для выбора пункта меню. Переменная-член **m\_pointQueue** отслеживает расположение, в котором пользователь коснулся экрана или щелкнул по нему. Дополнительные сведения о способе сбора ввода указателя в приложении Marble Maze см. далее в этом документе в разделе "Обработка ввода указателя".

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

### Обновление состояния игры

После обработки ввода контроллера и указателя метод **MarbleMaze::Update** обновляет состояние игры, если была нажата какая-либо кнопка.

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

##  Управление ходом игры


Цикл игры и метод **MarbleMaze::Update** совместно обновляют состояние игровых объектов. Если ваша игра поддерживает ввод с нескольких устройств, вы можете собрать данные ввода со всех устройств в один набор переменных, чтобы написать код, который проще поддерживать. Метод **MarbleMaze::Update** определяет один набор переменных, который включает перемещения со всех устройств.

```cpp
float combinedTiltX = 0.0f;
float combinedTiltY = 0.0f;
```

Механизмы ввода разных устройств ввода могут различаться. Например, ввод указателя обрабатывается моделью обработчика событий среды выполнения Windows. И наоборот, вы опрашиваете контроллер Xbox 360 на наличие данных ввода по мере необходимости. Мы рекомендуем всегда использовать механизмы ввода, стандартные для конкретного устройства. В этом разделе описано, как Marble Maze считывает данные ввода с каждого устройства, обновляет комбинированные значения ввода и использует эти значения для обновления состояния игры.

###  Обработка ввода указателя

При работе с вводом указателя необходимо вызвать метод [**Windows::UI::Core::CoreDispatcher::ProcessEvents**](https://msdn.microsoft.com/library/windows/apps/br208217) для обработки событий окна. Вызывайте этот метод в цикле игры до обновления или отрисовки сцены. Приложение Marble Maze передает метод **CoreProcessEventsOption::ProcessAllIfPresent** в этот метод для обработки всех событий в очереди и немедленного возврата. После обработки событий Marble Maze отрисовывает и представляет следующий кадр.

```cpp
// Process windowing events.
CoreWindow::GetForCurrentThread()->Dispatcher->ProcessEvents(CoreProcessEventsOption::ProcessAllIfPresent);
```

Среда выполнения Windows вызывает зарегистрированный обработчик для каждого произошедшего события. Класс **DirectXApp** регистрируется на события и направляет сведения указателя классу **MarbleMaze**.

```cpp
void DirectXApp::OnPointerPressed(
    _In_ Windows::UI::Core::CoreWindow^ sender,
    _In_ Windows::UI::Core::PointerEventArgs^ args
    )
{
    m_renderer->AddTouch(args->CurrentPoint->PointerId, args->CurrentPoint->Position);
}

void DirectXApp::OnPointerReleased(
    _In_ Windows::UI::Core::CoreWindow^ sender,
    _In_ Windows::UI::Core::PointerEventArgs^ args
    )
{
    m_renderer->RemoveTouch(args->CurrentPoint->PointerId);
}

void DirectXApp::OnPointerMoved(
    _In_ Windows::UI::Core::CoreWindow^ sender,
    _In_ Windows::UI::Core::PointerEventArgs^ args
    )
{
    m_renderer->UpdateTouch(args->CurrentPoint->PointerId, args->CurrentPoint->Position);
}
```

Класс **MarbleMaze** реагирует на события указателя, обновляя объект карты, который удерживает события касания. Метод **MarbleMaze::AddTouch** вызывается при первом нажатии указателя, например когда пользователь впервые касается экрана сенсорного устройства. Метод **MarbleMaze::UpdateTouch** вызывается при изменении положения указателя. Метод **MarbleMaze::RemoveTouch** вызывается при освобождении указателя, например когда пользователь перестает касаться экрана.

```cpp
void MarbleMaze::AddTouch(int id, Windows::Foundation::Point point)
{
    m_touches[id] = PointToTouch(point, m_windowBounds);

    m_pointQueue.push(D2D1::Point2F(point.X, point.Y));
}

void MarbleMaze::UpdateTouch(int id, Windows::Foundation::Point point)
{
    if (m_touches.find(id) != m_touches.end())
        m_touches[id] = PointToTouch(point, m_windowBounds);
}

void MarbleMaze::RemoveTouch(int id)
{
    m_touches.erase(id);
}
```

Функция PointToTouch переносит текущее положение указателя так, чтобы исходное положение было в центре экрана, а затем масштабирует систему координат, чтобы диапазон составлял примерно от –1,0 до +1,0. Это облегчает согласованный расчет наклона лабиринта при различных методах ввода.

```cpp
inline XMFLOAT2 PointToTouch(Windows::Foundation::Point point, Windows::Foundation::Rect bounds)
{
    float touchRadius = min(bounds.Width, bounds.Height);
    float dx = (point.X - (bounds.Width / 2.0f)) / touchRadius;
    float dy = ((bounds.Height / 2.0f) - point.Y) / touchRadius;

    return XMFLOAT2(dx, dy);
}
```

Метод **MarbleMaze::Update** обновляет комбинированные значения ввода увеличением коэффициента наклона на постоянное значение масштабирования. Значение масштабирования определяется при помощи экспериментов с несколькими различными значениями.

```cpp
// Account for touch input. 
const float touchScalingFactor = 2.0f;
for (TouchMap::const_iterator iter = m_touches.cbegin(); iter != m_touches.cend(); ++iter)
{
    combinedTiltX += iter->second.x * touchScalingFactor;
    combinedTiltY += iter->second.y * touchScalingFactor;
}
```

### Обработка ввода акселерометра

Для обработки ввода акселерометра метод **MarbleMaze::Update** вызывает метод [**Windows::Devices::Sensors::Accelerometer::GetCurrentReading**](https://msdn.microsoft.com/library/windows/apps/br225699). Этот метод возвращает объект [**Windows::Devices::Sensors::AccelerometerReading**](https://msdn.microsoft.com/library/windows/apps/br225688), который представляет показания акселерометра. Свойства **Windows::Devices::Sensors::AccelerometerReading::AccelerationX** и **Windows::Devices::Sensors::AccelerometerReading::AccelerationY** удерживают ускорение силы тяжести по осям x и y соответственно.

Следующий пример показывает, как метод **MarbleMaze::Update** опрашивает акселерометр и обновляет комбинированные значения ввода. Когда вы наклоняете устройство, сила тяжести заставляет шарик двигаться быстрее.

```cpp
// Account for sensors. 
const float acceleromterScalingFactor = 3.5f;
if (m_accelerometer != nullptr)
{
    Windows::Devices::Sensors::AccelerometerReading^ reading =
        m_accelerometer->GetCurrentReading();

    if (reading != nullptr)
    {
        combinedTiltX += static_cast<float>(reading->AccelerationX) * acceleromterScalingFactor;
        combinedTiltY += static_cast<float>(reading->AccelerationY) * acceleromterScalingFactor;
    }
}
```

Так как вы не знаете, есть ли на компьютере пользователя акселерометр, перед опросом акселерометра всегда проверяйте наличие действительного объекта Accelerometer.

### Обработка ввода контроллера Xbox 360

Следующий пример показывает, как метод **MarbleMaze::Update** считывает данные контроллера Xbox 360 и обновляет комбинированные значения ввода. Метод **MarbleMaze::Update** использует цикл, чтобы обеспечить получение ввода от любого подключенного контроллера. Метод **XInputGetState** заполняет объект XINPUT\_STATE текущим состоянием контроллера. Значения **combinedTiltX** и **combinedTiltY** обновляются согласно значениям x и y левого джойстика.

```cpp
// Account for input on any connected controller.
XINPUT_STATE inputState = {0};
for (DWORD userIndex = 0; userIndex < XUSER_MAX_COUNT; ++userIndex)
{
    DWORD result = XInputGetState(userIndex, &inputState);
    if(result != ERROR_SUCCESS) 
        continue;

    SHORT thumbLeftX = inputState.Gamepad.sThumbLX;
    if (abs(thumbLeftX) < XINPUT_GAMEPAD_LEFT_THUMB_DEADZONE) 
        thumbLeftX = 0;

    SHORT thumbLeftY = inputState.Gamepad.sThumbLY;
    if (abs(thumbLeftY) < XINPUT_GAMEPAD_LEFT_THUMB_DEADZONE) 
        thumbLeftY = 0;

    combinedTiltX += static_cast<float>(thumbLeftX) / 32768.0f;
    combinedTiltY += static_cast<float>(thumbLeftY) / 32768.0f;

    for (int i = 0; i < buttonCount; ++i)
        isButtonDown[userIndex][i] = (inputState.Gamepad.wButtons & buttons[i]) == buttons[i];
}
```

XInput определяет константу **XINPUT\_GAMEPAD\_LEFT\_THUMB\_DEADZONE** для левого джойстика. Это соответствующий порог мертвой зоны для большинства игр.

> **Важно!** При работе с контроллером Xbox 360 всегда помните о мертвой зоне. Мертвая зона появляется из-за разброса чувствительности игровых планшетов к начальному движению. Для некоторых контроллеров при небольшом перемещении показания отсутствуют. В других контроллерах даже небольшое движение может стать основанием для измеряемого показателя. Чтобы учесть это, создайте зону отсутствия движения для начального перемещения джойстика. Дополнительные сведения о мертвой зоне см. в разделе [Начало работы с XInput.](https://msdn.microsoft.com/library/windows/desktop/ee417001)

 

###  Применение ввода к состоянию игры

Устройства передают значения ввода разными способами. Например, ввод указателя может быть в координатах экрана, а ввод контроллера — в абсолютно другом формате. Одна из основных сложностей при комбинировании данных ввода от нескольких устройств в один набор значений ввода — нормализация, то есть преобразование значений в общий формат. Marble Maze нормализует значения, масштабируя их в диапазоне \[-1.0, 1.0\]. Для нормализации ввода контроллера Xbox 360 Marble Maze делит значения ввода на 32768, так как значения ввода джойстика всегда находятся в диапазоне от –32768 до 32767. Функция **PointToTouch**, описанная ранее в этом разделе, достигает похожих результатов преобразованием координат экрана в нормализованные значения в диапазоне примерно от –1,0 до +1,0.

> **Подсказка** Даже если ваше приложение использует только один метод ввода, мы рекомендуем всегда нормализовать значения ввода. Это облегчит интерпретацию ввода другими компонентами игры, такими как моделирование физической среды, и упростит написание игр, работающих с разными разрешениями экрана.

 

После обработки ввода метод **MarbleMaze::Update** создает вектор, который представляет влияние наклона лабиринта на шарик. Следующий пример показывает, как Marble Maze использует функцию **XMVector3Normalize** для создания нормализованного вектора силы тяжести. Переменная *MaxTilt* ограничивает степень наклона лабиринта и не дает ему упасть набок.

```cpp
const float maxTilt = 1.0f / 8.0f;
XMVECTOR gravity = XMVectorSet(combinedTiltX * maxTilt, combinedTiltY * maxTilt, 1.0f, 0.0f);
gravity = XMVector3Normalize(gravity);
```

Чтобы завершить обновление объектов сцены, Marble Maze передает обновленный вектор силы тяжести в моделирование физической среды, обновляет моделирование физической среды на время, прошедшее с предыдущего кадра, и обновляет положение и ориентацию шарика в пространстве. Если шарик упал в отверстие лабиринта, метод **MarbleMaze::Update** вновь помещает шарик в последнюю контрольную точку, задействованную шариком, и сбрасывает состояние моделирования физической среды.

```cpp
XMFLOAT3 g;
XMStoreFloat3(&g, gravity);
m_physics.SetGravity(g);
```

```cpp
// Only update physics when gameplay is active.
m_physics.UpdatePhysicsSimulation(timeDelta);
```

```cpp
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

Этот раздел не описывает принципы работы моделирования физической среды. Дополнительные сведения см. в файлах Physics.h и Physics.cpp в исходных файлах Marble Maze.

## Следующие действия


Прочитайте раздел [Добавление звука в пример Marble Maze](adding-audio-to-the-marble-maze-sample.md), чтобы получить представление о рекомендациях, которым стоит следовать при работе со звуком. В документе описывается, как Marble Maze использует Microsoft Media Foundation и XAudio2 для загрузки, микширования и воспроизведения звуковых ресурсов.

## Связанные темы


* [Добавление звука в образец Marble Maze](adding-audio-to-the-marble-maze-sample.md)
* [Добавление визуального содержимого в образец Marble Maze](adding-visual-content-to-the-marble-maze-sample.md)
* [Разработка Marble Maze — игры UWP — на C++ и DirectX](developing-marble-maze-a-windows-store-game-in-cpp-and-directx.md)

 

 






<!--HONumber=Mar16_HO1-->


