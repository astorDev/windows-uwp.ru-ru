---
title: Добавление элементов управления
description: Теперь рассмотрим, как пример игры реализует элементы управления перемещения и просмотра в трехмерной игре, а также как разрабатывать основные элементы управления сенсорным вводом, мыши и игровым контроллером.
ms.assetid: f9666abb-151a-74b4-ae0b-ef88f1f252f8
ms.date: 10/24/2017
ms.topic: article
keywords: windows 10, uwp, игры, элементы управления, ввод
ms.localizationpriority: medium
ms.openlocfilehash: dfe864f0b8c16cce9cc8d413c41a4e3324cf2e9b
ms.sourcegitcommit: 20969781aca50738792631f4b68326f9171a3980
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/26/2020
ms.locfileid: "85409663"
---
# <a name="add-controls"></a>Добавление элементов управления

> [!NOTE]
> Эта статья является частью серии руководств по [созданию простой универсальная платформа Windows (UWP) с помощью DirectX](tutorial--create-your-first-uwp-directx-game.md) . В разделе этой ссылки задается контекст для ряда.

\[Обновлено для приложений UWP в Windows 10. Статьи по Windows 8. x см. в [архиве](/previous-versions/windows/apps/mt244353(v=win.10)?redirectedfrom=MSDN)\]

Хорошая игра для универсальной платформы Windows (UWP) должна поддерживать широкое разнообразие интерфейсов. У потенциального игрока может быть, к примеру, планшетный компьютер без физических кнопок с установленной Windows 10, компьютер с подключенным контроллером Xbox или последняя модель игрового компьютера с высокопроизводительной мышью и игровой клавиатурой. В нашей игре элементы управления реализуются в классе [**MoveLookController**](https://github.com/Microsoft/Windows-universal-samples/blob/ef073ed8a2007d113af1d88eddace479e3bf0e07/SharedContent/cpp/GameContent/MoveLookController.cpp). Этот класс агрегирует все три типа ввода (мышь и клавиатура, сенсорный ввод и геймпад) в единый контроллер. В результате получается игра-шутер от первого лица, в которой используются стандартные элементы управления перемещением и обзором, работающие на различных устройствах.

> [!NOTE]
> Подробнее об элементах управления см. в статьях [Элементы управления перемещением и обзором для игр](tutorial--adding-move-look-controls-to-your-directx-game.md) и [Сенсорные элементы управления для игр](tutorial--adding-touch-controls-to-your-directx-game.md).


## <a name="objective"></a>Назначение

На данном этапе у нас есть игра, которая отрисовывается, но мы пока не можем перемещать в ней своего игрока или стрелять по мишеням. Давайте рассмотрим, как реализовать в нашей игре UWP на базе DirectX элементы управления перемещением и обзором, характерные для шутера от первого лица, для следующих типов ввода.
- Мышь и клавиатура
- Сенсорные технологии
- Геймпад

>[!Note]
>Если вы еще не скачали последний код игры для этого примера, перейдите к разделу [Пример игры Direct3D](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/Simple3DGameDX). Этот пример является частью большой коллекции примеров функций UWP. Указания по скачиванию этого примера приводятся в разделе [Получение примеров UWP из GitHub](/windows/uwp/get-started/get-uwp-app-samples).

## <a name="common-control-behaviors"></a>Общее в поведении элементов управления


Сенсорные элементы управления и элементы управления с помощью мыши и клавиатуры реализуются сходным образом. В приложении UWP указатель — это всего лишь точка на экране. Ею можно управлять с помощью мыши либо скольжением пальца по сенсорному экрану. Поэтому можно зарегистрировать единый набор событий, не беспокоясь о том, пользуется ли игрок мышью или сенсорным экраном, чтобы передвигать указатель и нажимать его.

Когда класс **мовелукконтроллер** в примере игры инициализирован, он регистрирует четыре события, относящиеся к указателю, и одно событие, связанное с мышью:

Событие | Описание:
:------ | :-------
[**CoreWindow::PointerPressed**](/uwp/api/windows.ui.core.corewindow.pointerpressed) | Нажата (или удерживается) левая или правая кнопка мыши, либо произошло касание сенсорного экрана.
[**CoreWindow::P Оинтермовед**](/uwp/api/windows.ui.core.corewindow.pointermoved) |Мышь переместилась, или провели по сенсорному экрану.
[**CoreWindow::P Оинтеррелеасед**](/uwp/api/windows.ui.core.corewindow.pointerreleased) |Левая кнопка мыши была отпущена, или предмет, касавшийся сенсорного экрана, поднялся.
[**CoreWindow::PointerExited**](/uwp/api/windows.ui.core.corewindow.pointerexited) |Указатель вышел за пределы главного окна.
[**Windows::Devices::Input::MouseMoved**](/uwp/api/windows.devices.input.mousedevice.mousemoved) | Мышь переместилась на определенное расстояние. Обратите внимание, что нас интересуют приращения перемещения мыши, а не текущие координаты X-Y.


Эти обработчики событий устанавливаются и начинают прослушивание ввода пользователя, как только **MoveLookController** инициализируется в окне приложения.
```cppwinrt
void MoveLookController::InitWindow(_In_ CoreWindow const& window)
{
    ResetState();

    window.PointerPressed({ this, &MoveLookController::OnPointerPressed });

    window.PointerMoved({ this, &MoveLookController::OnPointerMoved });

    window.PointerReleased({ this, &MoveLookController::OnPointerReleased });

    window.PointerExited({ this, &MoveLookController::OnPointerExited });

    ...

    // There is a separate handler for mouse-only relative mouse movement events.
    MouseDevice::GetForCurrentView().MouseMoved({ this, &MoveLookController::OnMouseMoved });

    ...
}
```

Полностью код [**InitWindow**](https://github.com/Microsoft/Windows-universal-samples/blob/ef073ed8a2007d113af1d88eddace479e3bf0e07/SharedContent/cpp/GameContent/MoveLookController.cpp#L68-L92) можно увидеть на GitHub.


Для определения того, когда игра должна прослушивать определенные виды ввода, **MoveLookController** имеет три особых состояния, не зависящих от вида контроллера:

Состояние | Описание
:----- | :-------
**None** | Это инициализированное состояние контроллера. Весь ввод игнорируется, поскольку игра еще не ожидает никакого ввода контроллера.
**ваитфоринпут** | Контроллер ожидает, что игрок подтвердит сообщение от игры нажатием левой кнопкой мыши, событием касания или нажатием кнопки меню на геймпаде.
**Активен** | Контроллер находится в режиме активного игрового процесса.



### <a name="waitforinput-state-and-pausing-the-game"></a>Состояние WaitForInput и приостановка игры

В состояние **WaitForInput** игра переходит в случае приостановки. Это случается, если игрок перемещает указатель за пределы главного окна игры или нажимает кнопку "Пауза" (клавиша P или кнопка **Пуск** на геймпаде). **MoveLookController** регистрирует нажатие и сообщает об этом игровому циклу, вызывая метод [**IsPauseRequested**](https://github.com/Microsoft/Windows-universal-samples/blob/ef073ed8a2007d113af1d88eddace479e3bf0e07/SharedContent/cpp/GameContent/MoveLookController.cpp#L107-L127). Если **IsPauseRequested** возвращает значение **true**, игровой цикл вызывает метод **WaitForPress** экземпляра **MoveLookController**, чтобы перевести контроллер в состояние **WaitForInput**. 


После перехода в состояние **WaitForInput** игра прекращает обработку практических всех событий ввода игрового процесса, пока не вернется в состояние **Active**. Исключением является кнопка "Пауза", при нажатии которой игра возвращается в активное состояние. Кроме кнопки приостановить, чтобы игра вернуться к **активному** состоянию, игроку нужно выбрать пункт меню. 



### <a name="the-active-state"></a>Состояние Active

В состоянии **Active** экземпляр **MoveLookController** обрабатывает события ввода от всех доступных устройств ввода и преобразовывает их в действия игрока. В результате он обновляет скорость и направление взгляда игрока и передает эти данные в игру — после вызова метода **Update** из игрового цикла.


В состоянии **Active** отслеживается весь ввод указателя, причем разным действиям указателя соответствуют разные идентификаторы указателя.
После получения события [**PointerPressed**](/uwp/api/windows.ui.core.corewindow.pointerpressed)**MoveLookController** получает значение идентификатора указателя, созданное окном. Идентификатор указателя представляет собой особый вид ввода. Например, при использовании устройства с мультисенсорной технологией можно одновременно совершать несколько активных действий ввода. Идентификаторы используются для отслеживания типа ввода данных игроком. Если событие произошло в прямоугольнике движения сенсорного экрана, назначается идентификатор указателя для отслеживания всех событий указателя в прямоугольнике движения. Другие события указателя в прямоугольнике огня отслеживаются отдельно, с помощью отдельного идентификатора указателя.


> [!NOTE]
> Вводу с помощью мыши и правого аналогового стика геймпада также назначаются идентификаторы, которые обрабатываются отдельно.

Когда события указателя соотнесены с конкретным игровым действием, обновляются данные, которые объект **MoveLookController** передает основному циклу игры.

При вызове метод [**Update**](https://github.com/Microsoft/Windows-universal-samples/blob/ef073ed8a2007d113af1d88eddace479e3bf0e07/SharedContent/cpp/GameContent/MoveLookController.cpp#L1005-L1096) в демонстрационной игре обрабатывает входные данные и обновляет переменные направления скорости и вида (** \_ скорость m** и ** \_ лукдиректион**), которые затем извлекаются из цикла, вызывая методы public [**скорость**](https://github.com/Microsoft/Windows-universal-samples/blob/ef073ed8a2007d113af1d88eddace479e3bf0e07/SharedContent/cpp/GameContent/MoveLookController.cpp#L906-L909) и [**лукдиректион**](https://github.com/Microsoft/Windows-universal-samples/blob/ef073ed8a2007d113af1d88eddace479e3bf0e07/SharedContent/cpp/GameContent/MoveLookController.cpp#L913-L923) .

> [!NOTE]
> Подробнее о методе [**Update**](#the-update-method) речь пойдет дальше на этой странице.




Проверить, стреляет ли игрок, игровой цикл может путем вызова метода **IsFiring** экземпляра **MoveLookController**. **MoveLookController** проверяет, нажал ли игрок кнопку "Стрелять" на одном из трех возможных устройств ввода.

```cppwinrt
bool MoveLookController::IsFiring()
{
    if (m_state == MoveLookControllerState::Active)
    {
        if (m_autoFire)
        {
            return (m_fireInUse || (m_mouseInUse && m_mouseLeftInUse) || PollingFireInUse());
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

Теперь рассмотрим реализацию каждого из трех типов элементов управления чуть подробнее.

## <a name="adding-relative-mouse-controls"></a>Добавление относительных элементов управления мышью


Если обнаружено перемещение мыши, необходимо использовать это перемещение для определения нового угла поворота и наклона камеры. Для этого необходимо создать относительные элементы управления мышью, с помощью которых относительное расстояние перемещения мыши рассчитывается как разница между начальной и конечной точками перемещения (в отличие от регистрации абсолютных координат перемещения по осям x-y).

Для этого мы получаем приращение по оси X (движение по горизонтали) и приращение по оси Y (движение по вертикали) с помощью полей [**MouseDelta::X**](/uwp/api/Windows.Devices.Input.MouseDelta) и **MouseDelta::Y** объекта аргумента [**Windows::Device::Input::MouseEventArgs::MouseDelta**](/uwp/api/windows.devices.input.mouseeventargs.mousedelta), возвращаемого событием [**MouseMoved**](/uwp/api/windows.devices.input.mousedevice.mousemoved).

```cppwinrt
void MoveLookController::OnMouseMoved(
    _In_ MouseDevice const& /* mouseDevice */,
    _In_ MouseEventArgs const& args
    )
{
    // Handle Mouse Input via dedicated relative movement handler.

    switch (m_state)
    {
    case MoveLookControllerState::Active:
        XMFLOAT2 mouseDelta;
        mouseDelta.x = static_cast<float>(args.MouseDelta().X);
        mouseDelta.y = static_cast<float>(args.MouseDelta().Y);

        XMFLOAT2 rotationDelta;
        // Scale for control sensitivity.
        rotationDelta.x = mouseDelta.x * MoveLookConstants::RotationGain;
        rotationDelta.y = mouseDelta.y * MoveLookConstants::RotationGain;

        // Update our orientation based on the command.
        m_pitch -= rotationDelta.y;
        m_yaw += rotationDelta.x;

        // Limit pitch to straight up or straight down.
        float limit = XM_PI / 2.0f - 0.01f;
        m_pitch = __max(-limit, m_pitch);
        m_pitch = __min(+limit, m_pitch);

        // Keep longitude in sane range by wrapping.
        if (m_yaw > XM_PI)
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

## <a name="adding-touch-support"></a>Добавление поддержки сенсорного ввода

Сенсорные элементы управления — прекрасный способ сделать игру доступной пользователям с планшетами. Эта игра собирает сенсорный ввод путем зонирования определенных областей экрана так, чтобы каждая зона соответствовала конкретным действиям в игре.
В этой игре используется три зоны сенсорного ввода.

![компоновка зон сенсорного ввода для управления перемещением и обзором](images/simple-dx-game-controls-touchzones.png)

Следующие команды характеризуют поведение наших элементов сенсорного управления.
Вводимые пользователем данные | Действие
:------- | :--------
Прямоугольник движения | Сенсорный ввод преобразовывается в виртуальный джойстик, где вертикальное движение превращается в перемещение положения назад/вперед, а горизонтальное движение — в перемещение положение влево/вправо.
Прямоугольник огня | Выстрел шариком.
Касание за пределами прямоугольник движения и прямоугольника огня | Изменение поворота и наклона камеры.

**MoveLookController** проверяет идентификатор указателя, чтобы определить, где произошло событие, и предпринимает одно из следующих действий:

-   Если событие [**PointerMoved**](/uwp/api/windows.ui.core.corewindow.pointermoved) произошло в прямоугольнике движения или огня, обновляет положение указателя для контроллера.
-   Если событие [**PointerMoved**](/uwp/api/windows.ui.core.corewindow.pointermoved) произошло в любой другой точке оставшейся части экрана (определенной как управление камерой), вычисляет изменение углов наклона и поворота вектора направления камеры.


После того как сенсорные элементы реализованы, прямоугольники, которые мы ранее нарисовали с помощью Direct2D, будут указывать игрокам, где находятся зоны движения, огня и обзора.


![сенсорные элементы управления](images/simple-dx-game-controls-showzones.png)


Теперь давайте рассмотрим, как реализуется каждый из элементов управления.


### <a name="move-and-fire-controller"></a>Контроллер движения и огня
Прямоугольник контроллера движения в левом нижнем квадранте экрана используется в качестве планшета контроллера. Движение пальцем влево и вправо в пределах этого пространства перемещает игрока влево и вправо, а движение вверх и вниз перемещает камеру вперед и назад.
После этого касание контроллера огня в нижнем правом квадранте экрана приводит к выстрелу шариком.

Методы [**SetMoveRect**](https://github.com/Microsoft/Windows-universal-samples/blob/ef073ed8a2007d113af1d88eddace479e3bf0e07/SharedContent/cpp/GameContent/MoveLookController.cpp#L843-L853) и [**SetFireRect**](https://github.com/Microsoft/Windows-universal-samples/blob/ef073ed8a2007d113af1d88eddace479e3bf0e07/SharedContent/cpp/GameContent/MoveLookController.cpp#L857-L867) создают наши прямоугольники ввода, принимая два двухмерных вектора для задания положений верхнего левого и нижнего углов прямоугольников на экране. 


Затем параметры назначаются **m \_ фиреупперлефт** и **m \_ фиреловерригхт** , которые помогут определить, касается ли пользователь прямоугольников. 
```cppwinrt
m_fireUpperLeft = upperLeft;
m_fireLowerRight = lowerRight;
```

Если размер экрана изменяется, эти прямоугольники перерисовываются в соответствии с новым размером.

Теперь, когда для наших элементов управления отведены отдельные зоны, нам нужно определить, когда пользователь ими пользуется.
Для этого мы создали в методе **MoveLookController::InitWindow** несколько обработчиков событий для случаев, когда пользователь нажимает, перемещает или освобождает указатель.

```cppwinrt
window.PointerPressed({ this, &MoveLookController::OnPointerPressed });

window.PointerMoved({ this, &MoveLookController::OnPointerMoved });

window.PointerReleased({ this, &MoveLookController::OnPointerReleased });
```

Сначала мы определим, что происходит, когда пользователь совершает первое нажатие в пределах прямоугольника движения или прямоугольника огня, используя метод [**OnPointerPressed**](https://github.com/Microsoft/Windows-universal-samples/blob/ef073ed8a2007d113af1d88eddace479e3bf0e07/SharedContent/cpp/GameContent/MoveLookController.cpp#L179-L313).
В данном случае мы проверяем, касается ли пользователь элемента управления, а также находится ли указатель уже в пределах этого элемента управления. Если это первое касание конкретного элемента управления, мы делаем следующее.
- Сохраните расположение TouchDown в **m \_ мовефирстдовн** или **m \_ фирефирстдовн** в виде 2D-вектора.
- Назначьте идентификатор указателя **m \_ мовепоинтерид** или **m \_ фирепоинтерид**.
- Задайте правильный флаг **inuse** (**m \_ мовеинусе** или **m \_ фиреинусе**), `true` так как теперь у нас есть активный указатель для этого элемента управления.

```cppwinrt
PointerPoint point = args.CurrentPoint();
uint32_t pointerID = point.PointerId();
Point pointerPosition = point.Position();
PointerPointProperties pointProperties = point.Properties();
auto pointerDevice = point.PointerDevice();
auto pointerDeviceType = pointerDevice.PointerDeviceType();

XMFLOAT2 position = XMFLOAT2(pointerPosition.X, pointerPosition.Y);

...
case MoveLookControllerState::Active:
    switch (pointerDeviceType)
    {
    case winrt::Windows::Devices::Input::PointerDeviceType::Touch:
        // Check to see if this pointer is in the move control.
        if (position.x > m_moveUpperLeft.x &&
            position.x < m_moveLowerRight.x &&
            position.y > m_moveUpperLeft.y &&
            position.y < m_moveLowerRight.y)
        {
            // If no pointer is in this control yet.
            if (!m_moveInUse)
            {
                // Process a DPad touch down event.
                // Save the location of the initial contact
                m_moveFirstDown = position;
                // Store the pointer using this control
                m_movePointerID = pointerID;
                // Set InUse flag to signal there is an active move pointer
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
                // Save the location of the initial contact
                m_fireLastPoint = position;
                // Store the pointer using this control
                m_firePointerID = pointerID;
                // Set InUse flag to signal there is an active fire pointer
                m_fireInUse = true;
                ...
            }
        }
        ...
```

После того как мы определили, касается ли пользователь элемента управления движением или огнем, нужно выяснить, совершает ли пользователь какие-либо движения нажатым пальцем.
С помощью метода [**MoveLookController::OnPointerMoved**](https://github.com/Microsoft/Windows-universal-samples/blob/ef073ed8a2007d113af1d88eddace479e3bf0e07/SharedContent/cpp/GameContent/MoveLookController.cpp#L317-L395) мы проверяем, какой указатель переместился, и сохраняем его новое положение в виде двухмерного вектора.  

```cppwinrt
PointerPoint point = args.CurrentPoint();
uint32_t pointerID = point.PointerId();
Point pointerPosition = point.Position();
PointerPointProperties pointProperties = point.Properties();
auto pointerDevice = point.PointerDevice();

// convert to allow math
XMFLOAT2 position = XMFLOAT2(pointerPosition.X, pointerPosition.Y);

switch (m_state)
{
case MoveLookControllerState::Active:
    // Decide which control this pointer is operating.

    // Move control
    if (pointerID == m_movePointerID)
    {
        // Save the current position.
        m_movePointerPosition = position;
    }
    // Look control
    else if (pointerID == m_lookPointerID)
    {
        ...
    }
    // Fire control
    else if (pointerID == m_firePointerID)
    {
        m_fireLastPoint = position;
    }
    ...
```

Завершив жесты в пределах элементах управления, пользователь отпускает указатель. С помощью метода [**MoveLookController::OnPointerReleased**](https://github.com/Microsoft/Windows-universal-samples/blob/ef073ed8a2007d113af1d88eddace479e3bf0e07/SharedContent/cpp/GameContent/MoveLookController.cpp#L441-L500) мы определяем, какой указатель был отпущен, и проводим серию сбросов.


Если отпущен элемент управления движением, мы делаем следующее.
- Задаем скорость игрока равной `0` во всех направлениях, чтобы он больше не двигался в игре.
- Переключить **m \_ мовеинусе** на, `false` так как пользователь больше не касается контроллера перемещения.
- Установим идентификатор указателя движения в `0`, поскольку в контроллере движения больше нет указателя.

```cppwinrt
if (pointerID == m_movePointerID)
{
    // Stop on release.
    m_velocity = XMFLOAT3(0, 0, 0);
    m_moveInUse = false;
    m_movePointerID = 0;
}
```

Что касается элемента управления огнем, если он отпущен, все, что мы делаем, — это переводим флаг **m_fireInUse** в значение `false`, а идентификатор указателя огня — в значение `0`, поскольку в контроллере огня больше нет указателя.
```cppwinrt
else if (pointerID == m_firePointerID)
{
    m_fireInUse = false;
    m_firePointerID = 0;
}
```

### <a name="look-controller"></a>Контроллер обзора
События указателя сенсорного устройства в неиспользуемых областях экрана мы рассматриваем как контроллер обзора. При проведении пальцем в этой зоне меняются поворот и наклон камеры игрока.

Если на сенсорном устройстве в этой зоне происходит событие **MoveLookController::OnPointerPressed** и игра находится в состоянии **Active**, зоне назначается идентификатор указателя.

```cppwinrt
// If no pointer is in this control yet.
if (!m_lookInUse)
{
    // Save point for later move.
    m_lookLastPoint = position;
    // Store the pointer using this control.
    m_lookPointerID = pointerID;
    // These are for smoothing.
    m_lookLastDelta.x = m_lookLastDelta.y = 0;
    m_lookInUse = true;
}
```

В данном случае **MoveLookController** назначает идентификатор указателя для указателя, который вызвал событие, конкретной переменной, которая соответствует зоне управления обзором. В случае сенсорного ввода в области вида переменной **m \_ лукпоинтерид** присваивается идентификатор указателя, который активировал событие. Логическая переменная, **m \_ лукинусе**, также устанавливается для указания того, что элемент управления еще не был освобожден.

Теперь рассмотрим, как в примере игры обрабатывается событие сенсорного экрана [**поинтермовед**](/uwp/api/windows.ui.core.corewindow.pointermoved) .

В методе **MoveLookController::OnPointerMoved** мы проверяем, какой идентификатор указателя назначен этому событию. Если это **m_lookPointerID**, мы вычисляем изменение положения указателя.
Затем мы используем эту разность для вычисления того, насколько должен измениться поворот. Наконец, мы можем обновить ** \_ шаг m** и **m \_ значения нутации** , которые будут использоваться в игре, чтобы изменить поворот проигрывателя. 

```cppwinrt
// This is the look pointer.
else if (pointerID == m_lookPointerID)
{
    // Look control.
    XMFLOAT2 pointerDelta;
    // How far did the pointer move?
    pointerDelta.x = position.x - m_lookLastPoint.x;
    pointerDelta.y = position.y - m_lookLastPoint.y;

    XMFLOAT2 rotationDelta;
    // Scale for control sensitivity.
    rotationDelta.x = pointerDelta.x * MoveLookConstants::RotationGain;
    rotationDelta.y = pointerDelta.y * MoveLookConstants::RotationGain;
    // Save for next time through.
    m_lookLastPoint = position;

    // Update our orientation based on the command.
    m_pitch -= rotationDelta.y;
    m_yaw += rotationDelta.x;

    // Limit pitch to straight up or straight down.
    float limit = XM_PI / 2.0f - 0.01f;
    m_pitch = __max(-limit, m_pitch);
    m_pitch = __min(+limit, m_pitch);
    ...
}
```

Последний фрагмент, который мы рассмотрим, — это то, как пример игры обрабатывает событие сенсорного экрана [**поинтеррелеасед**](/uwp/api/windows.ui.core.corewindow.pointerreleased) .
После того как пользователь завершил сенсорный жест и поднял палец с экрана, вызывается событие [**MoveLookController::OnPointerReleased**](https://github.com/Microsoft/Windows-universal-samples/blob/ef073ed8a2007d113af1d88eddace479e3bf0e07/SharedContent/cpp/GameContent/MoveLookController.cpp#L441-L500).
Если идентификатор указателя, который вызвал событие [**PointerReleased**](/uwp/api/windows.ui.core.corewindow.pointerreleased), является идентификатором зафиксированного ранее указателя движения, **MoveLookController** устанавливает скорость равной `0`, поскольку пользователь больше не касается зоны управления обзором.

```cppwinrt
else if (pointerID == m_lookPointerID)
{
    m_lookInUse = false;
    m_lookPointerID = 0;
}
```

## <a name="adding-mouse-and-keyboard-support"></a>Добавление поддержки мыши и клавиатуры

В этой игре следующая схема элементов управления для клавиатуры и мыши.

Вводимые пользователем данные | Действие
:------- | :--------
W | Переместить игрока вперед
Объект | Переместить игрока влево
S | Переместить игрока назад
D | Переместить игрока вправо
X | Переместить камеру вверх
Пробел | Переместить камеру вниз
P | Приостановить игру
Перемещение мыши | Изменить поворот и наклон камеры
Левая кнопка мыши | Выстрелить шариком


Для использования клавиатуры образец игры регистрирует два новых события — [**CoreWindow:: KeyUp**](/uwp/api/windows.ui.core.corewindow.keyup) и [**CoreWindow:: KeyDown**](/uwp/api/windows.ui.core.corewindow.keydown)в методе [**мовелукконтроллер:: инитвиндов**](https://github.com/Microsoft/Windows-universal-samples/blob/ef073ed8a2007d113af1d88eddace479e3bf0e07/SharedContent/cpp/GameContent/MoveLookController.cpp#L84-L88) . Эти события обрабатывают нажатие и отпускание клавиши.

```cppwinrt
window.KeyDown({ this, &MoveLookController::OnKeyDown });

window.KeyUp({ this, &MoveLookController::OnKeyUp });
```

Мышь воспринимается немного иначе, чем сенсорные элементы управления, несмотря на то что она использует указатель. В соответствии с нашей схемой элементов управления **MoveLookController** поворачивает камеру всякий раз, когда игрок перемещает мышь, и стреляет, когда игрок нажимает левую кнопку мыши.

Это обрабатывается в методе [**OnPointerPressed**](https://github.com/Microsoft/Windows-universal-samples/blob/ef073ed8a2007d113af1d88eddace479e3bf0e07/SharedContent/cpp/GameContent/MoveLookController.cpp#L179-L313) экземпляра **MoveLookController**.

В этом методе мы проверяем, какой тип устройства указателя используется с [`Windows::Devices::Input::PointerDeviceType`](/uwp/api/Windows.Devices.Input.PointerDeviceType) перечислением. Если игра находится в состоянии **Active** и **PointerDeviceType** — не **Touch**, предполагается, что ввод производится с помощью мыши.

```cppwinrt
case MoveLookControllerState::Active:
    switch (pointerDeviceType)
    {
    case winrt::Windows::Devices::Input::PointerDeviceType::Touch:
        // Behavior for touch controls
        ...

    default:
        // Behavior for mouse controls
        bool rightButton = pointProperties.IsRightButtonPressed();
        bool leftButton = pointProperties.IsLeftButtonPressed();

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
            // These are for smoothing.
            m_lookLastDelta.x = m_lookLastDelta.y = 0;
        }
        break;
    }
    break;
```

Когда игрок прекращает нажимать одну из кнопок мыши, возникает событие мыши [CoreWindow::PointerReleased](/uwp/api/Windows.UI.Core.CoreWindow.PointerReleased), что приводит к вызову метода [MoveLookController::OnPointerReleased](https://github.com/Microsoft/Windows-universal-samples/blob/ef073ed8a2007d113af1d88eddace479e3bf0e07/SharedContent/cpp/GameContent/MoveLookController.cpp#L441-L500), после чего ввод считается завершенным. На этом этапе стрельба шариками прекратится, если левая кнопка мыши была нажата, а теперь отпущена. Поскольку управление обзором включено всегда, игра продолжает использовать тот же указатель мыши для отслеживания дальнейших событий обзора.

```cppwinrt
case MoveLookControllerState::Active:
    // Touch points
    if (pointerID == m_movePointerID)
    {
        // Stop movement
        ...
    }
    else if (pointerID == m_lookPointerID)
    {
        // Stop look rotation
        ...
    }
    // Fire button has been released
    else if (pointerID == m_firePointerID)
    {
        // Stop firing
        ...
    }
    // Mouse point
    else if (pointerID == m_mousePointerID)
    {
        bool rightButton = pointProperties.IsRightButtonPressed();
        bool leftButton = pointProperties.IsLeftButtonPressed();

        // Mouse no longer in use so stop firing
        m_mouseInUse = false;

        // Don't clear the mouse pointer ID so that Move events still result in Look changes.
        // m_mousePointerID = 0;
        m_mouseLeftInUse = leftButton;
        m_mouseRightInUse = rightButton;
    }
    break;
```

Теперь давайте рассмотрим последний вид элементов управления, который мы будем поддерживать: геймпады. Геймпады обрабатываются не так, как сенсорный ввод и мышь, поскольку не они не используют объект указателя. По этой причине понадобится добавить несколько новых обработчиков событий и методов.

## <a name="adding-gamepad-support"></a>Добавление поддержки геймпада

В данной игре поддержка геймпада добавляется путем вызовов API-интерфейсов [Windows.Gaming.Input](/uwp/api/windows.gaming.input). Этот набор API предоставляет доступ к вводу с таких игровых устройств управления, как гоночные рули и рукоятки управления полетом. 

Элементы управления геймпада у нас будут следующие.

Вводимые пользователем данные | Действие
:------- | :--------
Левый аналоговый стик | Переместить игрока
Правый аналоговый стик | Изменить поворот и наклон камеры
Правый триггер | Выстрелить шариком
Кнопка "Пуск/меню" | Приостановить или возобновить игру

В методе [**InitWindow**](https://github.com/Microsoft/Windows-universal-samples/blob/ef073ed8a2007d113af1d88eddace479e3bf0e07/SharedContent/cpp/GameContent/MoveLookController.cpp#L68-L103) мы добавляем два новых события для проверки того, был ли геймпад [добавлен](https://github.com/Microsoft/Windows-universal-samples/blob/ef073ed8a2007d113af1d88eddace479e3bf0e07/SharedContent/cpp/GameContent/MoveLookController.cpp#L1100-L1105) или [удален](https://github.com/Microsoft/Windows-universal-samples/blob/ef073ed8a2007d113af1d88eddace479e3bf0e07/SharedContent/cpp/GameContent/MoveLookController.cpp#L1109-L1114). Эти события обновляют свойство **m_gamepadsChanged**. Используется в методе **упдатеполлингдевицес** для проверки изменения списка известных игровых планшетов. 

```cppwinrt
// Detect gamepad connection and disconnection events.
Gamepad::GamepadAdded({ this, &MoveLookController::OnGamepadAdded });

Gamepad::GamepadRemoved({ this, &MoveLookController::OnGamepadRemoved });
```

> [!NOTE]
> Приложения UWP не могут получать входные данные с контроллера Xbox One, пока приложение не находится в фокусе.

### <a name="the-updatepollingdevices-method"></a>Метод UpdatePollingDevices

Метод [**UpdatePollingDevices**](https://github.com/Microsoft/Windows-universal-samples/blob/ef073ed8a2007d113af1d88eddace479e3bf0e07/SharedContent/cpp/GameContent/MoveLookController.cpp#L654-L782) экземпляра **MoveLookController** немедленно проверяет, подключен ли геймпад. Если да, мы начинаем считывать его состояние с помощью метода [**Gamepad.GetCurrentReading**](/uwp/api/windows.gaming.input.gamepad.GetCurrentReading). Этот метод возвращает структуру [**GamepadReading**](/uwp/api/Windows.Gaming.Input.GamepadReading), позволяя нам проверить, какие кнопки были нажаты или какие аналоговые стики перемещены.

Если состояние игры — **WaitForInput**, мы прослушиваем только кнопку "Пуск/меню" контроллера, чтобы можно было возобновить игру.

Если состояние игры — **Active**, мы проверяем ввод пользователя, чтобы определить, какое внутриигровое действие должно произойти.
Например, если пользователь переместил левый аналоговый стик в определенном направлении, это сообщает игре, что нужно переместить игрока в направлении перемещения стика. Перемещение стика в определенном направлении должно превысить радиус **мертвой зоны**, иначе ничего не произойдет. Радиус мертвой зоны необходим для предотвращения непроизвольного смещения, когда контроллер реагирует на незначительные движения большого пальца, лежащего на стике. Без мертвых зон элементы управления могут казаться пользователю слишком чувствительными.

Ввод аналогового стика находится в диапазоне от –1 и 1 как по оси X, так и по оси Y. Следующая константа задает радиус мертвой зоны аналогового стика.

```cppwinrt
#define THUMBSTICK_DEADZONE 0.25f
```

С помощью этой переменной мы начинаем обрабатывать ввод с аналогового стика, который требует внутриигрового действия. Перемещение произойдет со значением от [–1, –0,26] или [0,26, 1] по любой из осей.

![мертвая зона для аналоговых стиков](images/simple-dx-game-controls-deadzone.png)

Эта часть метода **UpdatePollingDevices** обрабатывает левый и правый аналоговые стики.
Значения X и Y каждого стика проверяются на предмет того, выходят ли они за мертвую зону. Если одно или оба значения выходят за мертвую зону, мы обновляем соответствующий компонент.
Например, если левый аналоговый стик перемещается по оси X, мы добавим –1 к компоненту **x** вектора **m_moveCommand**. Этот вектор будет использоваться для агрегирования всех перемещений со всех устройств и позднее использоваться для расчета того, куда должен переместиться пользователь. 

```cppwinrt
// Use the left thumbstick to control the eye point position
// (position of the player).

// Check if left thumbstick is outside of dead zone on x axis
if (reading.LeftThumbstickX > THUMBSTICK_DEADZONE ||
    reading.LeftThumbstickX < -THUMBSTICK_DEADZONE)
{
    // Get value of left thumbstick's position on x axis
    float x = static_cast<float>(reading.LeftThumbstickX);
    // Set the x of the move vector to 1 if the stick is being moved right.
    // Set to -1 if moved left. 
    m_moveCommand.x -= (x > 0) ? 1 : -1;
}

// Check if left thumbstick is outside of dead zone on y axis
if (reading.LeftThumbstickY > THUMBSTICK_DEADZONE ||
    reading.LeftThumbstickY < -THUMBSTICK_DEADZONE)
{
    // Get value of left thumbstick's position on y axis
    float y = static_cast<float>(reading.LeftThumbstickY);
    // Set the y of the move vector to 1 if the stick is being moved forward.
    // Set to -1 if moved backwards.
    m_moveCommand.y += (y > 0) ? 1 : -1;
}
```

Аналогично тому, как левый стик управляет движением, правый стик управляем поворотом камеры.

Поведение правого аналогового стика соответствует поведению перемещения мыши в схеме управления с помощью мыши и клавиатуры.
Если стик находится за пределами мертвой зоны, мы вычисляем разность между текущим положением указателя и местом, куда пытается посмотреть пользователь.
Это изменение в положение указателя (**pointerDelta**) используется для обновления поворота и наклона камеры, которое позднее применяется в методе **Update**.
Вектор **pointerDelta** может показаться вам знакомым, поскольку он также используется в методе [MoveLookController::OnPointerMoved](https://github.com/Microsoft/Windows-universal-samples/blob/ef073ed8a2007d113af1d88eddace479e3bf0e07/SharedContent/cpp/GameContent/MoveLookController.cpp#L318-L395) для отслеживания изменений положения указателя при вводе с помощью мыши и сенсорного экрана.

```cppwinrt
// Use the right thumbstick to control the look at position

XMFLOAT2 pointerDelta;

// Check if right thumbstick is outside of deadzone on x axis
if (reading.RightThumbstickX > THUMBSTICK_DEADZONE ||
    reading.RightThumbstickX < -THUMBSTICK_DEADZONE)
{
    float x = static_cast<float>(reading.RightThumbstickX);
    // Register the change in the pointer along the x axis
    pointerDelta.x = x * x * x;
}
// No actionable thumbstick movement. Register no change in pointer.
else
{
    pointerDelta.x = 0.0f;
}
// Check if right thumbstick is outside of deadzone on y axis
if (reading.RightThumbstickY > THUMBSTICK_DEADZONE ||
    reading.RightThumbstickY < -THUMBSTICK_DEADZONE)
{
    float y = static_cast<float>(reading.RightThumbstickY);
    // Register the change in the pointer along the y axis
    pointerDelta.y = y * y * y;
}
else
{
    pointerDelta.y = 0.0f;
}

XMFLOAT2 rotationDelta;
// Scale for control sensitivity.
rotationDelta.x = pointerDelta.x * 0.08f;
rotationDelta.y = pointerDelta.y * 0.08f;

// Update our orientation based on the command.
m_pitch += rotationDelta.y;
m_yaw += rotationDelta.x;

// Limit pitch to straight up or straight down.
m_pitch = __max(-XM_PI / 2.0f, m_pitch);
m_pitch = __min(+XM_PI / 2.0f, m_pitch);
```

Элементы управления игры были бы неполными без возможности стрелять шариками.

Метод **UpdatePollingDevices** метод также проверяет, нажат ли правый триггер. Если да, свойство **m_firePressed** устанавливается в значение true, что дает игре сигнал начать стрельбу.
```cppwinrt
if (reading.RightTrigger > TRIGGER_DEADZONE)
{
    if (!m_autoFire && !m_gamepadTriggerInUse)
    {
        m_firePressed = true;
    }

    m_gamepadTriggerInUse = true;
}
else
{
    m_gamepadTriggerInUse = false;
}
```

## <a name="the-update-method"></a>Метод Update

В заключение рассмотрим подробнее метод [**Update**](https://github.com/Microsoft/Windows-universal-samples/blob/ef073ed8a2007d113af1d88eddace479e3bf0e07/SharedContent/cpp/GameContent/MoveLookController.cpp#L1005-L1096).
Этот метод объединяет все перемещения или повороты, сделанные игроком с помощью любых поддерживаемых устройств ввода, для формирования вектора скорости и обновления значений наклона и поворота, к которым обращается игровой цикл.

Метод **Update** начинает работу с вызова метода [**UpdatePollingDevices**](#the-updatepollingdevices-method) для обновления состояния контроллера. Этот метод также собирает входные данные с геймпада и добавляет перемещения на нем в вектор **m_moveCommand**. 

Затем в методе **Update** мы обновляем следующие проверки ввода.
- Если игрок использует прямоугольник контроллера движения, мы определяем изменение в положении указателя и используем его для вычисления того, переместил ли пользователь указатель за пределы мертвой зоны контроллера. Если выход за пределы мертвой зоны имеет место, векторное свойство **m_moveCommand** обновляется в соответствии со значением виртуального джойстика.
- Если нажаты какие-либо входные клавиши перемещения, значение `1.0f` или `-1.0f` добавляется в соответствующий компонент вектора **m_moveCommand** &mdash; `1.0f` для прямого и `-1.0f` для обратного.

Как только весь ввод перемещений учтен, мы прогоняем вектор **m_moveCommand** через некоторые расчеты, чтобы сформировать новый вектор, представляющий направление игрока по отношению к игровому миру.
Затем мы берем наши перемещения по отношению к миру и применяем их к игроку в качестве скорости в этом направлении.
Наконец, мы сбрасываем вектор **m_moveCommand** в `(0.0f, 0.0f, 0.0f)`, чтобы все было готово для следующего кадра игры.

```cppwinrt
void MoveLookController::Update()
{
    // Get any gamepad input and update state
    UpdatePollingDevices();

    if (m_moveInUse)
    {
        // Move control.
        XMFLOAT2 pointerDelta;

        pointerDelta.x = m_movePointerPosition.x - m_moveFirstDown.x;
        pointerDelta.y = m_movePointerPosition.y - m_moveFirstDown.y;

        // Figure out the command from the virtual joystick.
        XMFLOAT3 commandDirection = XMFLOAT3(0.0f, 0.0f, 0.0f);
        // Leave 32 pixel-wide dead spot for being still.
        if (fabsf(pointerDelta.x) > 16.0f)
            m_moveCommand.x -= pointerDelta.x / fabsf(pointerDelta.x);

        if (fabsf(pointerDelta.y) > 16.0f)
            m_moveCommand.y -= pointerDelta.y / fabsf(pointerDelta.y);
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
    wCommand.x = m_moveCommand.x * cosf(m_yaw) - m_moveCommand.y * sinf(m_yaw);
    wCommand.y = m_moveCommand.x * sinf(m_yaw) + m_moveCommand.y * cosf(m_yaw);
    wCommand.z = m_moveCommand.z;

    // Scale for sensitivity adjustment.
    // Our velocity is based on the command. Y is up.
    m_velocity.x = -wCommand.x * MoveLookConstants::MovementGain;
    m_velocity.z = wCommand.y * MoveLookConstants::MovementGain;
    m_velocity.y = wCommand.z * MoveLookConstants::MovementGain;

    // Clear movement input accumulator for use during next frame.
    m_moveCommand = XMFLOAT3(0.0f, 0.0f, 0.0f);
}
```

## <a name="next-steps"></a>Следующие шаги

Итак, элементы управления добавлены. Для создания иммерсивной игры не хватает еще одной вещи — звука!
Музыка и звуковые эффекты очень важны для любой игры, поэтому следующее, что мы рассмотрим, — это [добавление звука](tutorial--adding-sound.md).
