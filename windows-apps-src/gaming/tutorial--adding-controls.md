---
title: Добавление элементов управления
description: Сейчас мы рассмотрим, как реализовать элементы управления движением и обзором в 3D-игре на реальном примере и как разработать основные элементы управления касанием, мышью и игровым устройством.
ms.assetid: f9666abb-151a-74b4-ae0b-ef88f1f252f8
---

# Добавление элементов управления


\[ Обновлено для приложений UWP в Windows 10. Статьи о Windows 8.x см. в [архиве](http://go.microsoft.com/fwlink/p/?linkid=619132) \]

Сейчас мы рассмотрим, как реализовать элементы управления движением и обзором в 3D-игре на реальном примере и как разработать основные элементы управления касанием, мышью и игровым устройством.

## Цель


-   Реализация элементов управления с помощью мыши и клавиатуры, касания или контроллера Xbox для игр универсальной платформы Windows (UWP) с использованием DirectX.

## Игровое приложение UWP и элементы управления


Хорошая игра UWP должна поддерживать широкое разнообразие интерфейсов. У потенциального игрока может быть, к примеру, планшетный компьютер без физических кнопок с установленной Windows 10, мультимедийный компьютер с подключенным контроллером Xbox или последняя модель игрового компьютера с высокопроизводительной мышью и игровой клавиатурой. Игра должна поддерживать все перечисленные устройства, если только это не противоречит дизайну самой игры.

В этом примере игра поддерживает все три типа устройств. Эта простая игра-шутер от первого лица, и стандартные для этого жанра элементы управления движением и обзором легко реализуются для всех трех типов ввода.

Подробнее об элементах управления и в частности об элементах управления перемещением и обзором см. в разделах [Элементы управления перемещением и обзором для игр](tutorial--adding-move-look-controls-to-your-directx-game.md) и [Сенсорные элементы управления для игр](tutorial--adding-touch-controls-to-your-directx-game.md).

## Общее в поведении элементов управления


Сенсорные элементы управления и элементы управления с помощью мыши и клавиатуры реализуются сходным образом. В приложении UWP указатель — это всего лишь точка на экране. Ею можно управлять с помощью мыши либо скольжением пальца по сенсорному экрану. Поэтому можно зарегистрировать единый набор событий, не беспокоясь о том, пользуется ли игрок мышью или сенсорным экраном, чтобы передвигать указатель и нажимать его.

Когда класс **MoveLookController** в примере игры инициализирован, он регистрирует четыре типа событий для указателя и одно событие для мыши:

-   [**CoreWindow::PointerPressed**](https://msdn.microsoft.com/library/windows/apps/br208278). Нажата (или удерживается) левая или правая кнопка мыши, либо произошло касание сенсорного экрана.
-   [**CoreWindow::PointerMoved**](https://msdn.microsoft.com/library/windows/apps/br208276). Мышь переместилась, или провели по сенсорному экрану.
-   [**CoreWindow::PointerReleased**](https://msdn.microsoft.com/library/windows/apps/br208279). Левая кнопка мыши была отпущена, или предмет, касавшийся сенсорного экрана, поднялся.
-   [**CoreWindow::PointerExited**](https://msdn.microsoft.com/library/windows/apps/br208275). Указатель вышел за пределы главного окна.
-   [**Windows::Devices::Input::MouseMoved**](https://msdn.microsoft.com/library/windows/apps/hh758356). Мышь переместилась на определенное расстояние. Обратите внимание, что нас интересуют приращения перемещения мыши, а не текущие координаты x-y.

```cpp
void MoveLookController::Initialize(
    _In_ CoreWindow^ window
    )
{
    window->PointerPressed +=
        ref new TypedEventHandler<CoreWindow^, PointerEventArgs^>(this, &MoveLookController::OnPointerPressed);

    window->PointerMoved +=
        ref new TypedEventHandler<CoreWindow^, PointerEventArgs^>(this, &MoveLookController::OnPointerMoved);

    window->PointerReleased +=
        ref new TypedEventHandler<CoreWindow^, PointerEventArgs^>(this, &MoveLookController::OnPointerReleased);

    window->PointerExited +=
        ref new TypedEventHandler<CoreWindow^, PointerEventArgs^>(this, &MoveLookController::OnPointerExited);

    window->KeyDown +=
        ref new TypedEventHandler<CoreWindow^, KeyEventArgs^>(this, &MoveLookController::OnKeyDown);

    window->KeyUp +=
        ref new TypedEventHandler<CoreWindow^, KeyEventArgs^>(this, &MoveLookController::OnKeyUp);

    // A separate handler for mouse only relative mouse movement events.
    Windows::Devices::Input::MouseDevice::GetForCurrentView()->MouseMoved +=
        ref new TypedEventHandler<MouseDevice^, MouseEventArgs^>(this, &MoveLookController::OnMouseMoved);

    ResetState();
    m_state = MoveLookControllerState::None;

    m_pitch = 0.0f;
    m_yaw   = 0.0f;
}
```

Реализация управления для контроллера Xbox осуществляется отдельно с использованием API [XInput](https://msdn.microsoft.com/library/windows/desktop/hh405053). Рассмотрим подробнее реализацию элементов управления для игрового контроллера.

В примере игры класс **MoveLookController** имеет три особых состояния контроллера, не зависящих от вида управления:

-   **None**. Инициализированное состояние контроллера. Игра не предполагает ввода с помощью контроллера.
-   **WaitForInput**. Игра приостановлена до возвращения игрока.
-   **Active**. Игра запущена, идет обработка вводимых игроком данных.

Состояние **Active** — это состояние, когда игрок активно играет в игру. В этом состоянии экземпляр **MoveLookController** обрабатывает события ввода от доступных устройств ввода и преобразует их в действия игрока на основе собранных данных о событиях. В результате он обновляет данные о скорости и направлении взгляда игрока (нормальная плоскость просмотра) и передает эти данные в игру после того, как цикл игры запросит обновление.

Обратите внимание, что игрок может одновременно совершать несколько действий. Например, можно стрелять сферами и одновременно перемещать камеру. В состоянии **Active** отслеживаются все входные данные, причем разные идентификаторы указателя соответствуют разным действиям указателя. Это необходимо, поскольку с точки зрения игрока событие указателя в прямоугольнике огня отличается от события указателя в прямоугольнике движения или в другой области экрана.

После того как было получено событие [**PointerPressed**](https://msdn.microsoft.com/library/windows/apps/br208278), **MoveLookController** получает задаваемое окном значение идентификатора указателя. Идентификатор указателя представляет собой особый вид ввода. Например, при использовании устройства с мультисенсорной технологией можно одновременно совершать несколько активных действий ввода. Идентификаторы используются для отслеживания типа ввода данных игроком. Если событие произошло в прямоугольнике движения сенсорного экрана, идентификатор указателя будет отслеживать все события указателя, происходящие в прямоугольнике движения. Другие события указателя в прямоугольнике огня отслеживаются отдельно с помощью отдельного идентификатора указателя. (Мы еще поговорим об этом подробнее в разделе, посвященном сенсорным элементам управления.)

Вводу с помощью мыши соответствует другой идентификатор, который также обрабатывается отдельно.

Когда события указателя соотнесены с конкретным игровым действием, обновляются данные, которые объект **MoveLookController** передает основному циклу игры.

После обращения к методу **Update** в образце игры он обрабатывает введенные данные и обновляет переменные скорости и направления взгляда (**m\_velocity** и **m\_lookdirection**), которые получает цикл игры, направляя вызов общим методам **Velocity** и **LookDirection** экземпляра **MoveLookController**.

```cpp
void MoveLookController::Update()
{
    UpdateGameController();

    if (m_moveInUse)
    {
        // Move control.
        XMFLOAT2 pointerDelta;

        pointerDelta.x = m_movePointerPosition.x - m_moveFirstDown.x;
        pointerDelta.y = m_movePointerPosition.y - m_moveFirstDown.y;

        // Figure out the command from the virtual joystick.
        XMFLOAT3 commandDirection = XMFLOAT3(0.0f, 0.0f, 0.0f);
        if (fabsf(pointerDelta.x) > 16.0f)         // Leave 32 pixel-wide dead spot for being still.
            m_moveCommand.x -= pointerDelta.x/fabsf(pointerDelta.x);

        if (fabsf(pointerDelta.y) > 16.0f)
            m_moveCommand.y -= pointerDelta.y/fabsf(pointerDelta.y);
    }

    // Poll our state bits set by the keyboard input events.
    if (m_forward)
    {
        m_moveCommand.y += 1.0f;
    }
    if (m_back)
    {
        m_moveCommand.y -= 1.0f;
    }
    if (m_left)
    {
        m_moveCommand.x += 1.0f;
    }
    if (m_right)
    {
        m_moveCommand.x -= 1.0f;
    }
    if (m_up)
    {
        m_moveCommand.z += 1.0f;
    }
    if (m_down)
    {
        m_moveCommand.z -= 1.0f;
    }

    // Make sure that 45deg cases are not faster.
    if (fabsf(m_moveCommand.x) > 0.1f ||
        fabsf(m_moveCommand.y) > 0.1f ||
        fabsf(m_moveCommand.z) > 0.1f)
    {
        XMStoreFloat3(&m_moveCommand, XMVector3Normalize(XMLoadFloat3(&m_moveCommand)));
    }

    // Rotate command to align with our direction (world coordinates).
    XMFLOAT3 wCommand;
    wCommand.x =  m_moveCommand.x * cosf(m_yaw) - m_moveCommand.y * sinf(m_yaw);
    wCommand.y =  m_moveCommand.x * sinf(m_yaw) + m_moveCommand.y * cosf(m_yaw);
    wCommand.z =  m_moveCommand.z;

    // Scale for sensitivity adjustment.
    // Our velocity is based on the command, y is up.
    m_velocity.x = -wCommand.x * MOVEMENT_GAIN;
    m_velocity.z =  wCommand.y * MOVEMENT_GAIN;
    m_velocity.y =  wCommand.z * MOVEMENT_GAIN;

    // Clear movement input accumulator for use during next frame.
    m_moveCommand = XMFLOAT3(0.0f, 0.0f, 0.0f);
}
```

Цикл игры может проверить, стреляет ли игрок, направляя вызов методу **IsFiring** экземпляра **MoveLookController**. **MoveLookController** проверяет, нажал ли игрок кнопку "Стрелять" на любом из трех возможных устройств ввода.

```cpp
bool MoveLookController::IsFiring()
{
    if (m_state == MoveLookControllerState::Active)
    {
        if (m_autoFire)
        {
            return (m_fireInUse || (m_mouseInUse && m_mouseLeftInUse) || m_xinputTriggerInUse);
        }
        else
        {
            if (m_firePressed)
            {
                m_firePressed = false;
                return true;
            }
        }
    }
    return false;
}
```

Если игрок перемещает указатель за пределы главного окна игры или нажимает кнопку "Пауза" (клавиша P или кнопка Start контроллера Xbox), игра приостанавливается. **MoveLookController** регистрирует нажатие и сообщает циклу игры при отправлении им вызова методу **IsPauseRequested**. Если **IsPauseRequested** возвращает **true**, то цикл игры направляет вызов **WaitForPress** на **MoveLookController**, чтобы перевести контроллер в состояние **WaitForInput**. Затем **MoveLookController** ждет, пока игрок выберет один из пунктов меню для загрузки, продолжения или выхода из игры, и приостанавливает обработку событий ввода до тех пор, пока не вернется в состояние **Active**.

См. [полный пример кода для этого раздела](#code_sample).

Теперь давайте рассмотрим подробнее, как реализован каждый из трех типов элементов управления.

## Реализация относительных элементов управления мышью


Мы хотим использовать движение мыши для определения нового угла поворота и наклона камеры. Для этого необходимо создать относительные элементы управления мышью, с помощью которых относительное расстояние перемещения мыши рассчитывается как разница между начальной и конечной точками перемещения (в отличие от регистрации абсолютных координат перемещения по осям x-y).

Для этого мы получаем приращение по оси X (движение по горизонтали) и приращение по оси Y (движение по вертикали) с помощью полей [**MouseDelta::X**](https://msdn.microsoft.com/library/windows/apps/hh758353) и **MouseDelta::Y** объекта аргумента [**Windows::Device::Input::MouseEventArgs::MouseDelta**](https://msdn.microsoft.com/library/windows/apps/hh758358), возвращаемого событием [**MouseMoved**](https://msdn.microsoft.com/library/windows/apps/hh758356).

```cpp
void MoveLookController::OnMouseMoved(
    _In_ MouseDevice^ /* mouseDevice */,
    _In_ MouseEventArgs^ args
    )
{
    // Handle Mouse Input via dedicated relative movement handler.

    switch (m_state)
    {
    case MoveLookControllerState::Active:
        XMFLOAT2 mouseDelta;
        mouseDelta.x = static_cast<float>(args->MouseDelta.X);
        mouseDelta.y = static_cast<float>(args->MouseDelta.Y);

        XMFLOAT2 rotationDelta;
        rotationDelta.x  = mouseDelta.x * ROTATION_GAIN;   // scale for control sensitivity
        rotationDelta.y  = mouseDelta.y * ROTATION_GAIN;

        // Update our orientation based on the command.
        m_pitch -= rotationDelta.y;
        m_yaw   += rotationDelta.x;

        // Limit pitch to straight up or straight down.
        float limit = XM_PI / 2.0f - 0.01f;
        m_pitch = __max(-limit, m_pitch);
        m_pitch = __min(+limit, m_pitch);

        // Keep longitude in same range by wrapping.
        if (m_yaw >  XM_PI)
        {
            m_yaw -= XM_PI * 2.0f;
        }
        else if (m_yaw < -XM_PI)
        {
            m_yaw += XM_PI * 2.0f;
        }
        break;
    }
}
```

## Реализация сенсорных элементов управления


Труднее всего разрабатывать сенсорные элементы управления, поскольку они являются наиболее сложными и требуют высокоэффективной настройки. В примере игры прямоугольник в нижней левой четверти экрана используется как панель управления поворотом камеры. Движение большим пальцем влево и вправо на этом участке заставит камеру двигаться влево и вправо, а движением вверх и вниз камера перемещается вперед и назад. Прямоугольник в нижней правой четверти экрана можно нажимать, чтобы стрелять сферами. Прицеливание (поворот и наклон) осуществляется движением пальца по участкам экрана, которые не используются для движения и стрельбы: при движении пальца камера (с перекрестием прицела) двигается вслед за ним.

Прямоугольники движения и огня реализованы в примере кода двумя способами:

```cpp
void SetMoveRect(
        _In_ DirectX::XMFLOAT2 upperLeft,
        _In_ DirectX::XMFLOAT2 lowerRight
        );
 void SetFireRect(
        _In_ DirectX::XMFLOAT2 upperLeft,
        _In_ DirectX::XMFLOAT2 lowerRight
        );
```

На сенсорном устройстве события указателя в других областях экрана рассматриваются как команды для камеры. Если размер экрана изменяется, размер этих прямоугольников должен быть заново вычислен (и применен).

Если событие указателя на сенсорном устройстве происходит в одной из этих областей и устанавливается статус игры **Active**, ему присваивается идентификатор указателя, как обсуждалось ранее.

```cpp
void MoveLookController::OnPointerPressed(
    _In_ CoreWindow^ sender,
    _In_ PointerEventArgs^ args
    )
{
    PointerPoint^ point = args->CurrentPoint;
    UINT32 pointerID = point->PointerId;
    Point pointerPosition = point->Position;
    PointerPointProperties^ pointProperties = point->Properties;
    auto pointerDevice = point->PointerDevice;
    auto pointerDeviceType = pointerDevice->PointerDeviceType;

    XMFLOAT2 position = XMFLOAT2(pointerPosition.X, pointerPosition.Y);     // convert to allow math

    switch (m_state)
    {
    case MoveLookControllerState::WaitForInput:
        // ...
        // Game is paused, wait for click inside the game window.
        // ...
        break;

    case MoveLookControllerState::Active:
        switch (pointerDeviceType)
        {
        case Windows::Devices::Input::PointerDeviceType::Touch:
            // Check to see if this pointer is in the move control.
            if (position.x > m_moveUpperLeft.x &&
                position.x < m_moveLowerRight.x &&
                position.y > m_moveUpperLeft.y &&
                position.y < m_moveLowerRight.y)
            {
                if (!m_moveInUse)         // If no pointer is in this control yet:
                {
                    // Process a DPad touch down event.
                    m_moveFirstDown = position;                 // Save the location of the initial contact.
                    m_movePointerID = pointerID;                // Store the pointer using this control.
                    m_moveInUse = true;
                }
            }
            // Check to see if this pointer is in the fire control.
            else if (position.x > m_fireUpperLeft.x &&
                position.x < m_fireLowerRight.x &&
                position.y > m_fireUpperLeft.y &&
                position.y < m_fireLowerRight.y)
            {
                if (!m_fireInUse)
                {
                    m_fireLastPoint = position;
                    m_firePointerID = pointerID;
                    m_fireInUse = true;
                }
            }
            else
            {
                if (!m_lookInUse)   // If no pointer is in this control yet:
                {
                    m_lookLastPoint = position;                   // Save the pointer for a later move.
                    m_lookPointerID = pointerID;                  // Store the pointer using this control.
                    m_lookLastDelta.x = m_lookLastDelta.y = 0;    // These are for smoothing.
                    m_lookInUse = true;
                }
            }
            break;

        default:
            // ...
            // Handle mouse input here.
                                                // ...
            break;
        }
        break;
    }
    return;
}
```

Если событие [**PointerPressed**](https://msdn.microsoft.com/library/windows/apps/br208278) произошло в одной из трех областей управления, в прямоугольнике движения, огня или в оставшейся части экрана (управление камерой), **MoveLookController** присваивает идентификатору указателя, совершившему действие, особую переменную, соответствующую области экрана, в которой произошло действие. Например, если действие произошло в прямоугольнике движения, переменная **m\_movePointerID** присваивается идентификатору указателя, совершившему действие. Устанавливается также логическая переменная "используется" (в данном примере **m\_lookInUse**), чтобы показать, что элемент управления еще удерживается.

Теперь давайте рассмотрим, как в образце игры обрабатывается событие [**PointerMoved**](https://msdn.microsoft.com/library/windows/apps/br208276) на сенсорном экране.

```cpp
void MoveLookController::OnPointerMoved(
    _In_ CoreWindow^ sender,
    _In_ PointerEventArgs^ args
    )
{
    PointerPoint^ point = args->CurrentPoint;
    UINT32 pointerID = point->PointerId;
    Point pointerPosition = point->Position;
    PointerPointProperties^ pointProperties = point->Properties;
    auto pointerDevice = point->PointerDevice;
    auto pointerDeviceType = pointerDevice->PointerDeviceType;

    XMFLOAT2 position = XMFLOAT2(pointerPosition.X, pointerPosition.Y);     // Convert to allow math.

    switch (m_state)
    {
    case MoveLookControllerState::Active:
        // Decide which control this pointer is operating.
        if (pointerID == m_movePointerID)     // This is the move pointer.
        {
            m_movePointerPosition = position;       // Save the current position.
        }
        else if (pointerID == m_lookPointerID)     // This is the look pointer.
        {
            // Look control
            XMFLOAT2 pointerDelta;
            pointerDelta.x = position.x - m_lookLastPoint.x;        // How far did the pointer move.
            pointerDelta.y = position.y - m_lookLastPoint.y;

            XMFLOAT2 rotationDelta;
            rotationDelta.x = pointerDelta.x * ROTATION_GAIN;       // Scale for control sensitivity.
            rotationDelta.y = pointerDelta.y * ROTATION_GAIN;
            m_lookLastPoint = position;                             // Save for the next time through.

            // Update our orientation based on the command.
            m_pitch -= rotationDelta.y;
            m_yaw   += rotationDelta.x;

            // Limit pitch to straight up or straight down.
            m_pitch = __max(-XM_PI / 2.0f, m_pitch);
            m_pitch = __min(+XM_PI / 2.0f, m_pitch);
        }
        else if (pointerID == m_firePointerID)
        {
            m_fireLastPoint = position;
        }
        else if (pointerID == m_mousePointerID)
        {
            // ...
        }
        break;
    }
}
```

**MoveLookController** проверяет идентификатор указателя, чтобы определить, где произошло событие, и предпринимает одно из следующих действий:

-   Если событие [**PointerMoved**](https://msdn.microsoft.com/library/windows/apps/br208276) произошло в прямоугольнике движения или огня, обновляет положение указателя для контроллера.
-   Если событие [**PointerMoved**](https://msdn.microsoft.com/library/windows/apps/br208276) произошло в любой другой точке оставшейся части экрана (определенной как управление камерой), вычисляет изменение углов наклона и поворота вектора направления камеры.

Наконец, давайте рассмотрим, как в образце игры обрабатывается событие [**PointerReleased**](https://msdn.microsoft.com/library/windows/apps/br208279) на сенсорном экране.

```cpp
void MoveLookController::OnPointerReleased(
    _In_ CoreWindow^ sender,
    _In_ PointerEventArgs^ args
    )
{
    PointerPoint^ point = args->CurrentPoint;
    UINT32 pointerID = point->PointerId;
    Point pointerPosition = point->Position;
    PointerPointProperties^ pointProperties = point->Properties;

    XMFLOAT2 position = XMFLOAT2(pointerPosition.X, pointerPosition.Y);     // Convert to allow math.
    switch (m_state)
    {
    case MoveLookControllerState::WaitForInput:
        if (m_buttonInUse && (pointerID == m_buttonPointerID))
        {
            m_buttonInUse = false;
            m_buttonPressed = true;
        }
        break;

    case MoveLookControllerState::Active:
        if (pointerID == m_movePointerID)
        {
            m_velocity = XMFLOAT3(0, 0, 0);      // Stop on release.
            m_moveInUse = false;
            m_movePointerID = 0;
        }
        else if (pointerID == m_lookPointerID)
        {
            m_lookInUse = false;
            m_lookPointerID = 0;
        }
        else if (pointerID == m_firePointerID)
        {
            m_fireInUse = false;
            m_firePointerID = 0;
        }
        else if (pointerID == m_mousePointerID)
        {
            // ...
        }
        break;
    }
}
```

Если идентификатор указателя, который произвел событие [**PointerReleased**](https://msdn.microsoft.com/library/windows/apps/br208279), является идентификатором записанного ранее движения указателя, **MoveLookController** устанавливает нулевое значение скорости, поскольку игрок больше не дотрагивается до прямоугольника движения. Если значение скорости не будет установлено на 0, игрок будет продолжать двигаться! Если необходимо реализовать какую-либо форму инерции, целесообразно применить метод возвращения нулевого значения скорости при последующих запросах **Update** от цикла игры.

В противном случае, если событие [**PointerReleased**](https://msdn.microsoft.com/library/windows/apps/br208279) произошло в прямоугольнике огня или в области управления камерой, **MoveLookController** сбрасывает особые идентификаторы указателя.

Это основы реализации элементов управления на сенсорном экране на примере игры. Давайте перейдем к элементам управления мышью и с клавиатуры.

## Реализация элементов управления мышью и с клавиатуры


В примере игры реализованы элементы управления мышью и с клавиатуры:

-   Клавиши W, S, A и D поворачивают камеру соответственно вперед, назад, влево и вправо. Нажатие клавиш X и "Пробел" поворачивает камеру вверх и вниз соответственно.
-   Нажатие клавиши P приостанавливает игру.
-   Движение мышью позволяет контролировать угол вращения (поворота и наклона) камеры.
-   Щелчок левой кнопкой мыши позволяет стрелять сферами.

При использовании клавиатуры в образце игры регистрируется два дополнительных события: [**CoreWindow::KeyUp**](https://msdn.microsoft.com/library/windows/apps/br208271) и [**CoreWindow::KeyDown**](https://msdn.microsoft.com/library/windows/apps/br208270), которые обрабатывают нажатие и отпускание клавиши соответственно.

```cpp
window->KeyDown +=
        ref new TypedEventHandler<CoreWindow^, KeyEventArgs^>(this, &MoveLookController::OnKeyDown);

window->KeyUp +=
        ref new TypedEventHandler<CoreWindow^, KeyEventArgs^>(this, &MoveLookController::OnKeyUp);
```

Мышь воспринимается немного иначе, чем сенсорные элементы управления, несмотря на то что она использует указатель. Очевидно, что прямоугольники движения и огня не используются, потому что для игрока было бы крайне затруднительно одновременно нажимать элементы управления движением и стрельбой. Как отмечалось ранее, контроллер **MoveLookController** задействует элементы управления камерой при любом движении мыши и элементы управления стрельбой при нажатии левой кнопки мыши, как здесь показано.

```cpp
void MoveLookController::OnPointerPressed(
    _In_ CoreWindow^ /* sender */,
    _In_ PointerEventArgs^ args
    )
{
    PointerPoint^ point = args->CurrentPoint;
    uint32 pointerID = point->PointerId;
    Point pointerPosition = point->Position;
    PointerPointProperties^ pointProperties = point->Properties;
    auto pointerDevice = point->PointerDevice;
    auto pointerDeviceType = pointerDevice->PointerDeviceType;

    XMFLOAT2 position = XMFLOAT2(pointerPosition.X, pointerPosition.Y);     // convert to allow math

    switch (m_state)
    {
    case MoveLookControllerState::WaitForInput:
        if (position.x > m_buttonUpperLeft.x &&
            position.x < m_buttonLowerRight.x &&
            position.y > m_buttonUpperLeft.y &&
            position.y < m_buttonLowerRight.y)
        {
            // Wait until the button is released before setting the variable.
            m_buttonPointerID = pointerID;
            m_buttonInUse = true;

        }
        break;

    case MoveLookControllerState::Active:
        switch (pointerDeviceType)
        {
        case Windows::Devices::Input::PointerDeviceType::Touch:
            // Check to see if this pointer is in the move control.
            if (position.x > m_moveUpperLeft.x &&
                position.x < m_moveLowerRight.x &&
                position.y > m_moveUpperLeft.y &&
                position.y < m_moveLowerRight.y)
            {
                if (!m_moveInUse)         // If no pointer is in this control yet:
                {
                    // Process a DPad touch down event.
                    m_moveFirstDown = position;                 // Save the location of the initial contact.
                    m_movePointerID = pointerID;                // Store the pointer using this control.
                    m_moveInUse = true;
                }
            }
            // Check to see if this pointer is in the fire control.
            else if (position.x > m_fireUpperLeft.x &&
                position.x < m_fireLowerRight.x &&
                position.y > m_fireUpperLeft.y &&
                position.y < m_fireLowerRight.y)
            {
                if (!m_fireInUse)
                {
                    m_fireLastPoint = position;
                    m_firePointerID = pointerID;
                    m_fireInUse = true;
                    if (!m_autoFire)
                    {
                        m_firePressed = true;
                    }
                }
            }
            else
            {
                if (!m_lookInUse)   // If no pointer is in this control yet:
                {
                    m_lookLastPoint = position;                   // Save the pointer for a later move.
                    m_lookPointerID = pointerID;                  // Store the pointer using this control.
                    m_lookLastDelta.x = m_lookLastDelta.y = 0;    // These are for smoothing.
                    m_lookInUse = true;
                }
            }
            break;

        default:
            bool rightButton = pointProperties->IsRightButtonPressed;
            bool leftButton = pointProperties->IsLeftButtonPressed;

            if (!m_autoFire && (!m_mouseLeftInUse && leftButton))
            {
                m_firePressed = true;
            }

            if (!m_mouseInUse)
            {
                m_mouseInUse = true;
                m_mouseLastPoint = position;
                m_mousePointerID = pointerID;
                m_mouseLeftInUse = leftButton;
                m_mouseRightInUse = rightButton;
                m_lookLastDelta.x = m_lookLastDelta.y = 0;          // These are for smoothing.
            }
            else
            {

            }
            break;
        }

        break;
    }

    return;
}
```

Теперь давайте рассмотрим, как в образце игры обрабатывается событие мыши [**PointerReleased**](https://msdn.microsoft.com/library/windows/apps/br208279).

```cpp
void MoveLookController::OnPointerReleased(
    _In_ CoreWindow^ /* sender */,
    _In_ PointerEventArgs^ args
    )
{
    PointerPoint^ point = args->CurrentPoint;
    uint32 pointerID = point->PointerId;
    Point pointerPosition = point->Position;
    PointerPointProperties^ pointProperties = point->Properties;

    XMFLOAT2 position = XMFLOAT2(pointerPosition.X, pointerPosition.Y);     // Convert to allow math.
    switch (m_state)
    {
    case MoveLookControllerState::WaitForInput:
        if (m_buttonInUse && (pointerID == m_buttonPointerID))
        {
            m_buttonInUse = false;
            m_buttonPressed = true;
        }
        break;

    case MoveLookControllerState::Active:
        if (pointerID == m_movePointerID)
        {
            m_velocity = XMFLOAT3(0, 0, 0);      // Stop on release.
            m_moveInUse = false;
            m_movePointerID = 0;
        }
        else if (pointerID == m_lookPointerID)
        {
            m_lookInUse = false;
            m_lookPointerID = 0;
        }
        else if (pointerID == m_firePointerID)
        {
            m_fireInUse = false;
            m_firePointerID = 0;
        }
        else if (pointerID == m_mousePointerID)
        {
            bool rightButton = pointProperties->IsRightButtonPressed;
            bool leftButton = pointProperties->IsLeftButtonPressed;

            m_mouseInUse = false;

            // Don't clear the mouse pointer ID so that Move events still result in Look changes.
            // m_mousePointerID = 0;
            m_mouseLeftInUse = leftButton;
            m_mouseRightInUse = rightButton;
        }
        break;
    }
}
```

Когда игрок перестает нажимать одну из кнопок мыши, ввод заканчивается: стрельба сферами прекращается. Поскольку камера всегда включена, игра продолжает использовать тот же указатель мыши для отслеживания текущих событий камеры.

Теперь давайте рассмотрим последний вид управления — контроллер Xbox. Он обрабатывается не так, как сенсорные элементы управления и элементы управления мышью, поскольку не использует объект указателя.

## Реализация элементов управления контроллера Xbox.


В примере игры поддержка контроллера Xbox реализуется через вызовы API [XInput](https://msdn.microsoft.com/library/windows/desktop/hh405053), которые представляют собой набор API, предназначенных для упрощения программирования под игровые устройства управления. В примере игры левый аналоговый джойстик игрового устройства Xbox используется для перемещения, правый аналоговый джойстик для управления обзором, а правый триггер — для стрельбы. Кнопка Start служит для приостановки и возобновления игры.

Метод **Update** экземпляра **MoveLookController** сразу проверяет, подключено ли игровое устройство управления, а затем проверяет его состояние.

```cpp
void MoveLookController::UpdateGameController()
{
    if (!m_isControllerConnected)
    {
        // Check for controller connection by trying to get the capabilties.
        DWORD capsResult = XInputGetCapabilities(0, XINPUT_FLAG_GAMEPAD, &m_xinputCaps);
        if (capsResult != ERROR_SUCCESS)
        {
            return;
        }
        // Device is connected.
        m_isControllerConnected = true;
        m_xinputStartButtonInUse = false;
        m_xinputTriggerInUse = false;
    }

    DWORD stateResult = XInputGetState(0, &m_xinputState);
    if (stateResult != ERROR_SUCCESS)
    {
        // Device is no longer connected.
        m_isControllerConnected = false;
    }

    switch (m_state)
    {
    case MoveLookControllerState::WaitForInput:
        if (m_xinputState.Gamepad.wButtons & XINPUT_GAMEPAD_START)
        {
            m_xinputStartButtonInUse = true;
        }
        else if (m_xinputStartButtonInUse)
        {
            // Trigger one time only on button release.
            m_xinputStartButtonInUse = false;
            m_buttonPressed = true;
        }
        break;

    case MoveLookControllerState::Active:
        if (m_xinputState.Gamepad.wButtons & XINPUT_GAMEPAD_START)
        {
            m_xinputStartButtonInUse = true;
        }
        else if (m_xinputStartButtonInUse)
        {
            // Trigger one time only on button release.
            m_xinputStartButtonInUse = false;
            m_pausePressed = true;
        }
        // Use the Right Thumb joystick on the XBox controller to control
        // the eye point position control.
        // The controller input goes from -32767 to 32767.   We will normalize
        // this from -1 to 1 and keep a dead spot in the middle to avoid drift.

        if (m_xinputState.Gamepad.sThumbLX > XINPUT_GAMEPAD_LEFT_THUMB_DEADZONE ||
            m_xinputState.Gamepad.sThumbLX < -XINPUT_GAMEPAD_LEFT_THUMB_DEADZONE)
        {
            float x = (float)m_xinputState.Gamepad.sThumbLX/32767.0f;
            m_moveCommand.x -= x / fabsf(x);
        }

        if (m_xinputState.Gamepad.sThumbLY > XINPUT_GAMEPAD_LEFT_THUMB_DEADZONE ||
            m_xinputState.Gamepad.sThumbLY < -XINPUT_GAMEPAD_LEFT_THUMB_DEADZONE)
        {
            float y = (float)m_xinputState.Gamepad.sThumbLY/32767.0f;
            m_moveCommand.y += y / fabsf(y);
        }

        // Use the Left Thumb Joystick on the XBox controller to control
        // the look at control.
        // The controller input goes from -32767 to 32767.   We will normalize
        // this from -1 to 1 and keep a dead spot in the middle to avoid drift.
        XMFLOAT2 pointerDelta;
        if (m_xinputState.Gamepad.sThumbRX > XINPUT_GAMEPAD_RIGHT_THUMB_DEADZONE ||
            m_xinputState.Gamepad.sThumbRX < -XINPUT_GAMEPAD_RIGHT_THUMB_DEADZONE)
        {
            pointerDelta.x = (float)m_xinputState.Gamepad.sThumbRX/32767.0f;
            pointerDelta.x = pointerDelta.x * pointerDelta.x * pointerDelta.x;
        }
        else
        {
            pointerDelta.x = 0.0f;
        }
        if (m_xinputState.Gamepad.sThumbRY > XINPUT_GAMEPAD_RIGHT_THUMB_DEADZONE ||
            m_xinputState.Gamepad.sThumbRY < -XINPUT_GAMEPAD_RIGHT_THUMB_DEADZONE)
        {
            pointerDelta.y = (float)m_xinputState.Gamepad.sThumbRY/32767.0f;
            pointerDelta.y = pointerDelta.y * pointerDelta.y * pointerDelta.y;
        }
        else
        {
            pointerDelta.y = 0.0f;
        }

        XMFLOAT2 rotationDelta;
        rotationDelta.x = pointerDelta.x *  0.08f;       // Scale for control sensitivity.
        rotationDelta.y = pointerDelta.y *  0.08f;

        // Update our orientation based on the command.
        m_pitch += rotationDelta.y;
        m_yaw   += rotationDelta.x;

        // Limit pitch to straight up or straight down.
        m_pitch = __max(-XM_PI / 2.0f, m_pitch);
        m_pitch = __min(+XM_PI / 2.0f, m_pitch);

        // Check the state of the A button.  This is used to indicate fire control.

        if (m_xinputState.Gamepad.bRightTrigger > XINPUT_GAMEPAD_TRIGGER_THRESHOLD)
        {
            if (!m_autoFire && !m_xinputTriggerInUse)
            {
                m_firePressed = true;
            }
            m_xinputTriggerInUse = true;
        }
        else
        {
            m_xinputTriggerInUse = false;
        }
        break;
    }
}
```

Если игровое устройство управления находится в состоянии **Active**, данный метод проверяет, перемещен ли левый аналоговый джойстик в определенном направлении. Однако движение джойстика в определенном направлении должно регистрироваться как превышающее радиус мертвой зоны, иначе ничего не произойдет. Радиус мертвой зоны служит для предотвращения "непроизвольного смещения", когда контроллер реагирует на случайные движения большого пальца, лежащего на джойстике. Если бы мертвой зоны не было, игрока раздражала бы повышенная чувствительность элементов управления.

В данном случае метод **Update** осуществляет такую же проверку правого джойстика, чтобы определить, изменил ли игрок положение камеры, поскольку движение джойстика вышло за пределы радиуса мертвой зоны.

**Update** вычисляет новый угол поворота и наклона, а затем проверяет, нажал ли пользователь правый аналоговый триггер, который в нашем случае является кнопкой стрельбы.

Так в данном образце реализован полный набор средств управления. Помните, что хорошее приложение UWP поддерживает различные варианты средств управления, чтобы игроки с разными форм-факторами и устройствами могли играть так, как им нравится.

## Следующие действия


Мы рассмотрели все основные компоненты игры UWP на базе DirectX, кроме одного: звук! Музыка и звуковые эффекты очень важны для любой игры, поэтому давайте поговорим о [добавлении звука](tutorial--adding-sound.md).

## Полный образец кода для этого раздела


MoveLookController.h

```cpp
//// THIS CODE AND INFORMATION IS PROVIDED "AS IS" WITHOUT WARRANTY OF
//// ANY KIND, EITHER EXPRESSED OR IMPLIED, INCLUDING BUT NOT LIMITED TO
//// THE IMPLIED WARRANTIES OF MERCHANTABILITY AND/OR FITNESS FOR A
//// PARTICULAR PURPOSE.
////
//// Copyright (c) Microsoft Corporation. All rights reserved

#pragma once

// Uncomment to print debug tracing.
// #define MOVELOOKCONTROLLER_TRACE 1

enum class MoveLookControllerState
{
    None,
    WaitForInput,
    Active,
};

ref class MoveLookController
{
internal:
    MoveLookController();

    void Initialize(
        _In_ Windows::UI::Core::CoreWindow^ window
        );
    void SetMoveRect(
        _In_ DirectX::XMFLOAT2 upperLeft,
        _In_ DirectX::XMFLOAT2 lowerRight
        );
    void SetFireRect(
        _In_ DirectX::XMFLOAT2 upperLeft,
        _In_ DirectX::XMFLOAT2 lowerRight
        );
    void WaitForPress(
        _In_ DirectX::XMFLOAT2 UpperLeft,
        _In_ DirectX::XMFLOAT2 LowerRight
        );
    void WaitForPress();

    void Update();
    bool IsFiring();
    bool IsPressComplete();
    bool IsPauseRequested();

    DirectX::XMFLOAT3 Velocity();
    DirectX::XMFLOAT3 LookDirection();
    float Pitch();
    void  Pitch(_In_ float pitch);
    float Yaw();
    void  Yaw(_In_ float yaw);
    bool  Active();
    void  Active(_In_ bool active);

    bool AutoFire();
    void AutoFire(_In_ bool AutoFire);

protected:
    void OnPointerPressed(
        _In_ Windows::UI::Core::CoreWindow^ sender,
        _In_ Windows::UI::Core::PointerEventArgs^ args
        );
    void OnPointerMoved(
        _In_ Windows::UI::Core::CoreWindow^ sender,
        _In_ Windows::UI::Core::PointerEventArgs^ args
        );
    void OnPointerReleased(
        _In_ Windows::UI::Core::CoreWindow^ sender,
        _In_ Windows::UI::Core::PointerEventArgs^ args
        );
    void OnPointerExited(
        _In_ Windows::UI::Core::CoreWindow^ sender,
        _In_ Windows::UI::Core::PointerEventArgs^ args
        );
    void OnKeyDown(
        _In_ Windows::UI::Core::CoreWindow^ sender,
        _In_ Windows::UI::Core::KeyEventArgs^ args
        );
    void OnKeyUp(
        _In_ Windows::UI::Core::CoreWindow^ sender,
        _In_ Windows::UI::Core::KeyEventArgs^ args
        );

    void OnMouseMoved(
        _In_ Windows::Devices::Input::MouseDevice^ mouseDevice,
        _In_ Windows::Devices::Input::MouseEventArgs^ args
        );

#ifdef MOVELOOKCONTROLLER_TRACE
    void DebugTrace(const wchar_t *format, ...);
#endif

private:
    // Properties of the controller object
    MoveLookControllerState     m_state;
    DirectX::XMFLOAT3           m_velocity;             // How far we move it this frame
    float                       m_pitch;
    float                       m_yaw;                  // Orientation euler angles in radians

    // Properties of the Move control
    DirectX::XMFLOAT2           m_moveUpperLeft;        // Bounding box where this control will activate
    DirectX::XMFLOAT2           m_moveLowerRight;
    bool                        m_moveInUse;            // The move control is in use.
    uint32                      m_movePointerID;        // The id of the pointer in this control.
    DirectX::XMFLOAT2           m_moveFirstDown;        // The point where the initial contact occurred.
    DirectX::XMFLOAT2           m_movePointerPosition;  // The point where the move pointer is currently located.
    DirectX::XMFLOAT3           m_moveCommand;          // The net command from the move control.

    // Properties of the Look control
    bool                        m_lookInUse;            // The look control is in use.
    uint32                      m_lookPointerID;        // The id of the pointer in this control.
    DirectX::XMFLOAT2           m_lookLastPoint;        // The last point (from last frame)
    DirectX::XMFLOAT2           m_lookLastDelta;        // for smoothing.

    // Properties of the Fire control
    bool                        m_autoFire;
    bool                        m_firePressed;
    DirectX::XMFLOAT2           m_fireUpperLeft;        // The bounding box where this control will activate.
    DirectX::XMFLOAT2           m_fireLowerRight;
    bool                        m_fireInUse;            // The fire control is in use.
    UINT32                      m_firePointerID;        // The id of the pointer in this control.
    DirectX::XMFLOAT2           m_fireLastPoint;        // The last fire position.

    // Properties of the Mouse control. This is a combination of Look and Fire.
    bool                        m_mouseInUse;
    uint32                      m_mousePointerID;
    DirectX::XMFLOAT2           m_mouseLastPoint;
    bool                        m_mouseLeftInUse;
    bool                        m_mouseRightInUse;

    bool                        m_buttonInUse;
    uint32                      m_buttonPointerID;
    DirectX::XMFLOAT2           m_buttonUpperLeft;
    DirectX::XMFLOAT2           m_buttonLowerRight;
    bool                        m_buttonPressed;
    bool                        m_pausePressed;

    // XBox Input related members
    bool                        m_isControllerConnected;  // Do we have a controller connected?
    XINPUT_CAPABILITIES         m_xinputCaps;             // The capabilites of the controller.
    XINPUT_STATE                m_xinputState;            // The current state of the controller.
    bool                        m_xinputStartButtonInUse;
    bool                        m_xinputTriggerInUse;

    // Input states for Keyboard
    bool                        m_forward;
    bool                        m_back;                    // States for movement
    bool                        m_left;
    bool                        m_right;
    bool                        m_up;
    bool                        m_down;
    bool                        m_pause;

private:
    void     ResetState();
    void     UpdateGameController();
};
```

MoveLookController.cpp

```cpp
//// THIS CODE AND INFORMATION IS PROVIDED "AS IS" WITHOUT WARRANTY OF
//// ANY KIND, EITHER EXPRESSED OR IMPLIED, INCLUDING BUT NOT LIMITED TO
//// THE IMPLIED WARRANTIES OF MERCHANTABILITY AND/OR FITNESS FOR A
//// PARTICULAR PURPOSE.
////
//// Copyright (c) Microsoft Corporation. All rights reserved

#include "pch.h"
#include "MoveLookController.h"
#include "DirectXSample.h"

using namespace Windows::UI::Core;
using namespace Windows::UI::Input;
using namespace Windows::UI;
using namespace Windows::Foundation;
using namespace Microsoft::WRL;
using namespace DirectX;
using namespace Windows::Devices::Input;
using namespace Windows::System;

#define ROTATION_GAIN 0.008f        // The sensitivity adjustment for the look controller.
#define MOVEMENT_GAIN 2.f           // The sensitivity adjustment for the move controller.

// A basic Move/Look Controller class such as in an FPS
// horizontal (x-z-plane) movement on left virtual joystick
// also supports WASD keyboard input
// steering and orientation via left mouse down or touch drag.

//----------------------------------------------------------------------

MoveLookController::MoveLookController():
    m_autoFire(true),
    m_isControllerConnected(false)
{
}

//----------------------------------------------------------------------
// Set up the controls supported by this controller.

void MoveLookController::Initialize(
    _In_ CoreWindow^ window
    )
{
    window->PointerPressed +=
        ref new TypedEventHandler<CoreWindow^, PointerEventArgs^>(this, &MoveLookController::OnPointerPressed);

    window->PointerMoved +=
        ref new TypedEventHandler<CoreWindow^, PointerEventArgs^>(this, &MoveLookController::OnPointerMoved);

    window->PointerReleased +=
        ref new TypedEventHandler<CoreWindow^, PointerEventArgs^>(this, &MoveLookController::OnPointerReleased);

    window->PointerExited +=
        ref new TypedEventHandler<CoreWindow^, PointerEventArgs^>(this, &MoveLookController::OnPointerExited);

    window->KeyDown +=
        ref new TypedEventHandler<CoreWindow^, KeyEventArgs^>(this, &MoveLookController::OnKeyDown);

    window->KeyUp +=
        ref new TypedEventHandler<CoreWindow^, KeyEventArgs^>(this, &MoveLookController::OnKeyUp);

    // A separate handler for mouse only relative mouse movement events.
    Windows::Devices::Input::MouseDevice::GetForCurrentView()->MouseMoved +=
        ref new TypedEventHandler<MouseDevice^, MouseEventArgs^>(this, &MoveLookController::OnMouseMoved);

    ResetState();
    m_state = MoveLookControllerState::None;

    m_pitch = 0.0f;
    m_yaw   = 0.0f;
}

//----------------------------------------------------------------------

bool MoveLookController::IsPauseRequested()
{
    switch (m_state)
    {
    case MoveLookControllerState::Active:
        UpdateGameController();
        if (m_pausePressed)
        {

            m_pausePressed = false;
            return true;
        }
        else
        {
            return false;
        }
    }
    return false;
}

//----------------------------------------------------------------------

bool MoveLookController::IsFiring()
{
    if (m_state == MoveLookControllerState::Active)
    {
        if (m_autoFire)
        {
            return (m_fireInUse || (m_mouseInUse && m_mouseLeftInUse) || m_xinputTriggerInUse);
        }
        else
        {
            if (m_firePressed)
            {
                m_firePressed = false;
                return true;
            }
        }
    }
    return false;
}

//----------------------------------------------------------------------

bool MoveLookController::IsPressComplete()
{
    switch (m_state)
    {
    case MoveLookControllerState::WaitForInput:
        UpdateGameController();
        if (m_buttonPressed)
        {

            m_buttonPressed = false;
            return true;
        }
        else
        {
            return false;
        }
        break;
    }

    return false;
}

//----------------------------------------------------------------------

void MoveLookController::OnPointerPressed(
    _In_ CoreWindow^ /* sender */,
    _In_ PointerEventArgs^ args
    )
{
    PointerPoint^ point = args->CurrentPoint;
    uint32 pointerID = point->PointerId;
    Point pointerPosition = point->Position;
    PointerPointProperties^ pointProperties = point->Properties;
    auto pointerDevice = point->PointerDevice;
    auto pointerDeviceType = pointerDevice->PointerDeviceType;

    XMFLOAT2 position = XMFLOAT2(pointerPosition.X, pointerPosition.Y);     // Convert to allow math.

    switch (m_state)
    {
    case MoveLookControllerState::WaitForInput:
        if (position.x > m_buttonUpperLeft.x &&
            position.x < m_buttonLowerRight.x &&
            position.y > m_buttonUpperLeft.y &&
            position.y < m_buttonLowerRight.y)
        {
            // Wait until button released before setting variable.
            m_buttonPointerID = pointerID;
            m_buttonInUse = true;

        }
        break;

    case MoveLookControllerState::Active:
        switch (pointerDeviceType)
        {
        case Windows::Devices::Input::PointerDeviceType::Touch:
            // Check to see if this pointer is in the move control.
            if (position.x > m_moveUpperLeft.x &&
                position.x < m_moveLowerRight.x &&
                position.y > m_moveUpperLeft.y &&
                position.y < m_moveLowerRight.y)
            {
                if (!m_moveInUse)         // If no pointer is in this control yet:
                {
                    // Process a DPad touch down event.
                    m_moveFirstDown = position;                 // Save the location of the initial contact.
                    m_movePointerID = pointerID;                // Store the pointer using this control.
                    m_moveInUse = true;
                }
            }
            // Check to see if this pointer is in the fire control.
            else if (position.x > m_fireUpperLeft.x &&
                position.x < m_fireLowerRight.x &&
                position.y > m_fireUpperLeft.y &&
                position.y < m_fireLowerRight.y)
            {
                if (!m_fireInUse)
                {
                    m_fireLastPoint = position;
                    m_firePointerID = pointerID;
                    m_fireInUse = true;
                    if (!m_autoFire)
                    {
                        m_firePressed = true;
                    }
                }
            }
            else
            {
                if (!m_lookInUse)   // If no pointer is in this control yet:
                {
                    m_lookLastPoint = position;                   // Save the point for a later move.
                    m_lookPointerID = pointerID;                  // Store the pointer using this control.
                    m_lookLastDelta.x = m_lookLastDelta.y = 0;    // These are for smoothing.
                    m_lookInUse = true;
                }
            }
            break;

        default:
            bool rightButton = pointProperties->IsRightButtonPressed;
            bool leftButton = pointProperties->IsLeftButtonPressed;

            if (!m_autoFire && (!m_mouseLeftInUse && leftButton))
            {
                m_firePressed = true;
            }

            if (!m_mouseInUse)
            {
                m_mouseInUse = true;
                m_mouseLastPoint = position;
                m_mousePointerID = pointerID;
                m_mouseLeftInUse = leftButton;
                m_mouseRightInUse = rightButton;
                m_lookLastDelta.x = m_lookLastDelta.y = 0;          // These are for smoothing.
            }
            else
            {

            }
            break;
        }

        break;
    }

    return;
}

//----------------------------------------------------------------------

void MoveLookController::OnPointerMoved(
    _In_ CoreWindow^ /* sender */,
    _In_ PointerEventArgs^ args
    )
{
    PointerPoint^ point = args->CurrentPoint;
    uint32 pointerID = point->PointerId;
    Point pointerPosition = point->Position;
    PointerPointProperties^ pointProperties = point->Properties;
    auto pointerDevice = point->PointerDevice;

    XMFLOAT2 position = XMFLOAT2(pointerPosition.X, pointerPosition.Y);     // Convert to allow math.

    switch (m_state)
    {
    case MoveLookControllerState::Active:
        // Decide which control this pointer is operating.
        if (pointerID == m_movePointerID)     // This is the move pointer.
        {
            m_movePointerPosition = position;       // Save the current position.
        }
        else if (pointerID == m_lookPointerID)     // This is the look pointer.
        {
            // Look control
            XMFLOAT2 pointerDelta;
            pointerDelta.x = position.x - m_lookLastPoint.x;        // How far did the pointer move?
            pointerDelta.y = position.y - m_lookLastPoint.y;

            XMFLOAT2 rotationDelta;
            rotationDelta.x = pointerDelta.x * ROTATION_GAIN;       // Scale for control sensitivity.
            rotationDelta.y = pointerDelta.y * ROTATION_GAIN;
            m_lookLastPoint = position;                             // Save for the next time through.

            // Update our orientation based on the command.
            m_pitch -= rotationDelta.y;
            m_yaw   += rotationDelta.x;

            // Limit pitch to straight up or straight down.
            float limit = XM_PI / 2.0f - 0.01f;
            m_pitch = __max(-limit, m_pitch);
            m_pitch = __min(+limit, m_pitch);

            // Keep longitude in the same range by wrapping.
            if (m_yaw >  XM_PI)
            {
                m_yaw -= XM_PI * 2.0f;
            }
            else if (m_yaw < -XM_PI)
            {
                m_yaw += XM_PI * 2.0f;
            }
        }
        else if (pointerID == m_firePointerID)
        {
            m_fireLastPoint = position;
        }
        else if (pointerID == m_mousePointerID)
        {
            m_mouseLeftInUse  = pointProperties->IsLeftButtonPressed;
            m_mouseRightInUse = pointProperties->IsRightButtonPressed;;
            m_mouseLastPoint = position;                            // save for next time through

            // Handle mouse movement via a separate relative mouse movement handler (OnMouseMoved).
        }
        break;
    }
}

//----------------------------------------------------------------------

void MoveLookController::OnMouseMoved(
    _In_ MouseDevice^ /* mouseDevice */,
    _In_ MouseEventArgs^ args
    )
{
    // Handle Mouse Input via dedicated relative movement handler.

    switch (m_state)
    {
    case MoveLookControllerState::Active:
        XMFLOAT2 mouseDelta;
        mouseDelta.x = static_cast<float>(args->MouseDelta.X);
        mouseDelta.y = static_cast<float>(args->MouseDelta.Y);

        XMFLOAT2 rotationDelta;
        rotationDelta.x  = mouseDelta.x * ROTATION_GAIN;   // Scale for control sensitivity.
        rotationDelta.y  = mouseDelta.y * ROTATION_GAIN;

        // Update our orientation based on the command.
        m_pitch -= rotationDelta.y;
        m_yaw   += rotationDelta.x;

        // Limit pitch to straight up or straight down.
        float limit = XM_PI / 2.0f - 0.01f;
        m_pitch = __max(-limit, m_pitch);
        m_pitch = __min(+limit, m_pitch);

        // Keep longitude in same range by wrapping.
        if (m_yaw >  XM_PI)
        {
            m_yaw -= XM_PI * 2.0f;
        }
        else if (m_yaw < -XM_PI)
        {
            m_yaw += XM_PI * 2.0f;
        }
        break;
    }
}

//----------------------------------------------------------------------

void MoveLookController::OnPointerReleased(
    _In_ CoreWindow^ /* sender */,
    _In_ PointerEventArgs^ args
    )
{
    PointerPoint^ point = args->CurrentPoint;
    uint32 pointerID = point->PointerId;
    Point pointerPosition = point->Position;
    PointerPointProperties^ pointProperties = point->Properties;

    XMFLOAT2 position = XMFLOAT2(pointerPosition.X, pointerPosition.Y);     // Convert to allow math.
    switch (m_state)
    {
    case MoveLookControllerState::WaitForInput:
        if (m_buttonInUse && (pointerID == m_buttonPointerID))
        {
            m_buttonInUse = false;
            m_buttonPressed = true;
        }
        break;

    case MoveLookControllerState::Active:
        if (pointerID == m_movePointerID)
        {
            m_velocity = XMFLOAT3(0, 0, 0);      // Stop on release.
            m_moveInUse = false;
            m_movePointerID = 0;
        }
        else if (pointerID == m_lookPointerID)
        {
            m_lookInUse = false;
            m_lookPointerID = 0;
        }
        else if (pointerID == m_firePointerID)
        {
            m_fireInUse = false;
            m_firePointerID = 0;
        }
        else if (pointerID == m_mousePointerID)
        {
            bool rightButton = pointProperties->IsRightButtonPressed;
            bool leftButton = pointProperties->IsLeftButtonPressed;

            m_mouseInUse = false;

            // Don't clear the mouse pointer ID so that Move events still result in Look changes.
            // m_mousePointerID = 0;
            m_mouseLeftInUse = leftButton;
            m_mouseRightInUse = rightButton;
        }
        break;
    }
}

//----------------------------------------------------------------------

void MoveLookController::OnPointerExited(
    _In_ CoreWindow^ /* sender */,
    _In_ PointerEventArgs^ args
    )
{
    PointerPoint^ point = args->CurrentPoint;
    uint32 pointerID = point->PointerId;
    Point pointerPosition = point->Position;
    PointerPointProperties^ pointProperties = point->Properties;

    XMFLOAT2 position  = XMFLOAT2(pointerPosition.X, pointerPosition.Y);        // Convert to allow math.

    switch (m_state)
    {
    case MoveLookControllerState::WaitForInput:
        if (m_buttonInUse && (pointerID == m_buttonPointerID))
        {
            m_buttonInUse = false;
            m_buttonPressed = false;
        }
        break;

    case MoveLookControllerState::Active:
        if (pointerID == m_movePointerID)
        {
            m_velocity = XMFLOAT3(0, 0, 0);      // Stop on release.
            m_moveInUse = false;
            m_movePointerID = 0;
        }
        else if (pointerID == m_lookPointerID)
        {
            m_lookInUse = false;
            m_lookPointerID = 0;
        }
        else if (pointerID == m_firePointerID)
        {
            m_fireInUse = false;
            m_firePointerID = 0;
        }
        else if (pointerID == m_mousePointerID)
        {
            m_mouseInUse = false;
            m_mousePointerID = 0;
            m_mouseLeftInUse = false;
            m_mouseRightInUse = false;
        }
        break;
    }
}

//----------------------------------------------------------------------

void MoveLookController::OnKeyDown(
    _In_ CoreWindow^ /* sender */,
    _In_ KeyEventArgs^ args
    )
{
    Windows::System::VirtualKey Key;
    Key = args->VirtualKey;

    // Figure out the command from the keyboard.
    if (Key == VirtualKey::W)       // forward
        m_forward = true;
    if (Key == VirtualKey::S)       // back
        m_back = true;
    if (Key == VirtualKey::A)       // left
        m_left = true;
    if (Key == VirtualKey::D)       // right
        m_right = true;
    if (Key == VirtualKey::Space)   // up
        m_up = true;
    if (Key == VirtualKey::X)       // down
        m_down = true;
    if (Key == VirtualKey::P)       // Pause
        m_pause = true;
}

//----------------------------------------------------------------------

void MoveLookController::OnKeyUp(
    _In_ CoreWindow^ /* sender */,
    _In_ KeyEventArgs^ args
    )
{
    Windows::System::VirtualKey Key;
    Key = args->VirtualKey;

    // Figure out the command from the keyboard.
    if (Key == VirtualKey::W)       // Forward
        m_forward = false;
    if (Key == VirtualKey::S)       // Back
        m_back = false;
    if (Key == VirtualKey::A)       // Left
        m_left = false;
    if (Key == VirtualKey::D)       // Right
        m_right = false;
    if (Key == VirtualKey::Space)   // Up
        m_up = false;
    if (Key == VirtualKey::X)       // Down
        m_down = false;
    if (Key == VirtualKey::P)
    {
        if (m_pause)
        {
            // Trigger pause only one time on button release.
            m_pausePressed = true;
            m_pause = false;
        }
    }
}

//----------------------------------------------------------------------

void MoveLookController::ResetState()
{
    // Reset the state of the controller.
    // Disable any active pointer IDs to stop all interaction.
    m_buttonPressed = false;
    m_pausePressed = false;
    m_buttonInUse = false;
    m_moveInUse = false;
    m_lookInUse = false;
    m_fireInUse = false;
    m_mouseInUse = false;
    m_mouseLeftInUse = false;
    m_mouseRightInUse = false;
    m_movePointerID = 0;
    m_lookPointerID = 0;
    m_firePointerID = 0;
    m_mousePointerID = 0;
    m_velocity = XMFLOAT3(0.0f, 0.0f, 0.0f);

    m_xinputStartButtonInUse = false;
    m_xinputTriggerInUse = false;

    m_moveCommand = XMFLOAT3(0.0f, 0.0f, 0.0f);
    m_forward = false;
    m_back = false;
    m_left = false;
    m_right = false;
    m_up = false;
    m_down = false;
    m_pause = false;
}

//----------------------------------------------------------------------

void MoveLookController::SetMoveRect (
    _In_ XMFLOAT2 upperLeft,
    _In_ XMFLOAT2 lowerRight
    )
{
    m_moveUpperLeft  = upperLeft;
    m_moveLowerRight = lowerRight;
}

//----------------------------------------------------------------------

void MoveLookController::SetFireRect (
    _In_ XMFLOAT2 upperLeft,
    _In_ XMFLOAT2 lowerRight
    )
{
    m_fireUpperLeft  = upperLeft;
    m_fireLowerRight = lowerRight;
}

//----------------------------------------------------------------------

void MoveLookController::WaitForPress(
    _In_ XMFLOAT2 upperLeft,
    _In_ XMFLOAT2 lowerRight
    )
{

    ResetState();
    m_state = MoveLookControllerState::WaitForInput;
    m_buttonUpperLeft  = upperLeft;
    m_buttonLowerRight = lowerRight;

    // Turn on the mouse cursor.
    CoreWindow::GetForCurrentThread()->PointerCursor = ref new CoreCursor(CoreCursorType::Arrow, 0);
}

//----------------------------------------------------------------------

void MoveLookController::WaitForPress()
{
    ResetState();
    m_state = MoveLookControllerState::WaitForInput;
    m_buttonUpperLeft.x = 0.0f;
    m_buttonUpperLeft.y = 0.0f;
    m_buttonLowerRight.x = 0.0f;
    m_buttonLowerRight.y = 0.0f;

    // Turn on the mouse cursor.
    CoreWindow::GetForCurrentThread()->PointerCursor = ref new CoreCursor(CoreCursorType::Arrow, 0);
}

//----------------------------------------------------------------------

XMFLOAT3 MoveLookController::Velocity()
{
    return m_velocity;
}

//----------------------------------------------------------------------

XMFLOAT3 MoveLookController::LookDirection()
{
    XMFLOAT3 lookDirection;

    float r = cosf(m_pitch);              // In the plane
    lookDirection.y = sinf(m_pitch);      // Vertical
    lookDirection.z = r * cosf(m_yaw);    // Fwd-back
    lookDirection.x = r * sinf(m_yaw);    // Left-right

    return lookDirection;
}

//----------------------------------------------------------------------

float MoveLookController::Pitch()
{
    return m_pitch;
}

//----------------------------------------------------------------------

void MoveLookController::Pitch(_In_ float pitch)
{
    m_pitch = pitch;
}

//----------------------------------------------------------------------

float MoveLookController::Yaw()
{
    return m_yaw;
}

//----------------------------------------------------------------------

void MoveLookController::Yaw(_In_ float yaw)
{
    m_yaw = yaw;
}

//----------------------------------------------------------------------

void MoveLookController::Active(_In_ bool active)
{
    ResetState();

    if (active)
    {
        m_state = MoveLookControllerState::Active;
        // Turn the mouse cursor off (hidden).
        CoreWindow::GetForCurrentThread()->PointerCursor = nullptr;
    }
    else
    {
        m_state = MoveLookControllerState::None;
        // Turn the mouse cursor on.
        auto window = CoreWindow::GetForCurrentThread();
        if (window)
        {
            // Protect case where there isn't a window associated with the current thread.
            // This happens on initialization.
            window->PointerCursor = ref new CoreCursor(CoreCursorType::Arrow, 0);
        }
    }
}

//----------------------------------------------------------------------

bool MoveLookController::Active()
{
    if (m_state == MoveLookControllerState::Active)
    {
        return true;
    }
    else
    {
        return false;
    }
}

//----------------------------------------------------------------------

void MoveLookController::AutoFire(_In_ bool autoFire)
{
    m_autoFire = autoFire;
}

//----------------------------------------------------------------------

bool MoveLookController::AutoFire()
{
    return m_autoFire;
}

//----------------------------------------------------------------------

void MoveLookController::Update()
{
    UpdateGameController();

    if (m_moveInUse)
    {
        // Move control.
        XMFLOAT2 pointerDelta;

        pointerDelta.x = m_movePointerPosition.x - m_moveFirstDown.x;
        pointerDelta.y = m_movePointerPosition.y - m_moveFirstDown.y;

        // Figure out the command from the virtual joystick.
        XMFLOAT3 commandDirection = XMFLOAT3(0.0f, 0.0f, 0.0f);
        if (fabsf(pointerDelta.x) > 16.0f)         // leave 32 pixel-wide dead spot for being still
            m_moveCommand.x -= pointerDelta.x/fabsf(pointerDelta.x);

        if (fabsf(pointerDelta.y) > 16.0f)
            m_moveCommand.y -= pointerDelta.y/fabsf(pointerDelta.y);
    }

    // Poll our state bits set by the keyboard input events.
    if (m_forward)
    {
        m_moveCommand.y += 1.0f;
    }
    if (m_back)
    {
        m_moveCommand.y -= 1.0f;
    }
    if (m_left)
    {
        m_moveCommand.x += 1.0f;
    }
    if (m_right)
    {
        m_moveCommand.x -= 1.0f;
    }
    if (m_up)
    {
        m_moveCommand.z += 1.0f;
    }
    if (m_down)
    {
        m_moveCommand.z -= 1.0f;
    }

    // Make sure that 45 deg cases are not faster.
    if (fabsf(m_moveCommand.x) > 0.1f ||
        fabsf(m_moveCommand.y) > 0.1f ||
        fabsf(m_moveCommand.z) > 0.1f)
    {
        XMStoreFloat3(&m_moveCommand, XMVector3Normalize(XMLoadFloat3(&m_moveCommand)));
    }

    // Rotate command to align with our direction (world coordinates).
    XMFLOAT3 wCommand;
    wCommand.x =  m_moveCommand.x * cosf(m_yaw) - m_moveCommand.y * sinf(m_yaw);
    wCommand.y =  m_moveCommand.x * sinf(m_yaw) + m_moveCommand.y * cosf(m_yaw);
    wCommand.z =  m_moveCommand.z;

    // Scale for sensitivity adjustment.
    // Our velocity is based on the command, y is up.
    m_velocity.x = -wCommand.x * MOVEMENT_GAIN;
    m_velocity.z =  wCommand.y * MOVEMENT_GAIN;
    m_velocity.y =  wCommand.z * MOVEMENT_GAIN;

    // Clear the movement input accumulator for use during next frame.
    m_moveCommand = XMFLOAT3(0.0f, 0.0f, 0.0f);
}

//----------------------------------------------------------------------

void MoveLookController::UpdateGameController()
{
    if (!m_isControllerConnected)
    {
        // Check for controller connection by trying to get the capabilties.
        DWORD capsResult = XInputGetCapabilities(0, XINPUT_FLAG_GAMEPAD, &m_xinputCaps);
        if (capsResult != ERROR_SUCCESS)
        {
            return;
        }
        // The device is connected.
        m_isControllerConnected = true;
        m_xinputStartButtonInUse = false;
        m_xinputTriggerInUse = false;
    }

    DWORD stateResult = XInputGetState(0, &m_xinputState);
    if (stateResult != ERROR_SUCCESS)
    {
        // The device is no longer connected.
        m_isControllerConnected = false;
    }

    switch (m_state)
    {
    case MoveLookControllerState::WaitForInput:
        if (m_xinputState.Gamepad.wButtons & XINPUT_GAMEPAD_START)
        {
            m_xinputStartButtonInUse = true;
        }
        else if (m_xinputStartButtonInUse)
        {
            // Trigger one time only on button release.
            m_xinputStartButtonInUse = false;
            m_buttonPressed = true;
        }
        break;

    case MoveLookControllerState::Active:
        if (m_xinputState.Gamepad.wButtons & XINPUT_GAMEPAD_START)
        {
            m_xinputStartButtonInUse = true;
        }
        else if (m_xinputStartButtonInUse)
        {
            // Trigger one time only on button release.
            m_xinputStartButtonInUse = false;
            m_pausePressed = true;
        }
        // Use the Right Thumb joystick on the XBox controller to control
        // the eye point position control.
        // The controller input goes from -32767 to 32767.   We will normalize
        // this from -1 to 1 and keep a dead spot in the middle to avoid drift.

        if (m_xinputState.Gamepad.sThumbLX > XINPUT_GAMEPAD_LEFT_THUMB_DEADZONE ||
            m_xinputState.Gamepad.sThumbLX < -XINPUT_GAMEPAD_LEFT_THUMB_DEADZONE)
        {
            float x = (float)m_xinputState.Gamepad.sThumbLX/32767.0f;
            m_moveCommand.x -= x / fabsf(x);
        }

        if (m_xinputState.Gamepad.sThumbLY > XINPUT_GAMEPAD_LEFT_THUMB_DEADZONE ||
            m_xinputState.Gamepad.sThumbLY < -XINPUT_GAMEPAD_LEFT_THUMB_DEADZONE)
        {
            float y = (float)m_xinputState.Gamepad.sThumbLY/32767.0f;
            m_moveCommand.y += y / fabsf(y);
        }

        // Use the Left Thumb Joystick on the XBox controller to control
        // the look at control.
        // The controller input goes from -32767 to 32767.   We will normalize
        // this from -1 to 1 and keep a dead spot in the middle to avoid drift.
        XMFLOAT2 pointerDelta;
        if (m_xinputState.Gamepad.sThumbRX > XINPUT_GAMEPAD_RIGHT_THUMB_DEADZONE ||
            m_xinputState.Gamepad.sThumbRX < -XINPUT_GAMEPAD_RIGHT_THUMB_DEADZONE)
        {
            pointerDelta.x = (float)m_xinputState.Gamepad.sThumbRX/32767.0f;
            pointerDelta.x = pointerDelta.x * pointerDelta.x * pointerDelta.x;
        }
        else
        {
            pointerDelta.x = 0.0f;
        }
        if (m_xinputState.Gamepad.sThumbRY > XINPUT_GAMEPAD_RIGHT_THUMB_DEADZONE ||
            m_xinputState.Gamepad.sThumbRY < -XINPUT_GAMEPAD_RIGHT_THUMB_DEADZONE)
        {
            pointerDelta.y = (float)m_xinputState.Gamepad.sThumbRY/32767.0f;
            pointerDelta.y = pointerDelta.y * pointerDelta.y * pointerDelta.y;
        }
        else
        {
            pointerDelta.y = 0.0f;
        }

        XMFLOAT2 rotationDelta;
        rotationDelta.x = pointerDelta.x *  0.08f;       // scale for control sensitivity
        rotationDelta.y = pointerDelta.y *  0.08f;

        // Update our orientation based on the command.
        m_pitch += rotationDelta.y;
        m_yaw   += rotationDelta.x;

        // Limit pitch to straight up or straight down.
        m_pitch = __max(-XM_PI / 2.0f, m_pitch);
        m_pitch = __min(+XM_PI / 2.0f, m_pitch);

        // Check the state of the A button.  This is used to indicate fire control.

        if (m_xinputState.Gamepad.bRightTrigger > XINPUT_GAMEPAD_TRIGGER_THRESHOLD)
        {
            if (!m_autoFire && !m_xinputTriggerInUse)
            {
                m_firePressed = true;
            }
            m_xinputTriggerInUse = true;
        }
        else
        {
            m_xinputTriggerInUse = false;
        }
        break;
    }
}
```

> **Примечание.**  
Эта статья адресована разработчикам приложений для Windows 10 на базе универсальной платформы Windows (UWP). При разработке приложений для Windows 8.x или Windows Phone 8.x см. раздел [архивной документации](http://go.microsoft.com/fwlink/p/?linkid=619132).

 

## Связанные разделы


[Создание простой игры UWP c использованием DirectX](tutorial--create-your-first-metro-style-directx-game.md)

 

 






<!--HONumber=Mar16_HO1-->


