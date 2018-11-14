---
author: abbycar
title: Добавление элементов управления
description: Сейчас мы рассмотрим, как реализовать элементы управления движением и обзором в 3D-игре на реальном примере и как разработать основные элементы управления касанием, мышью и игровым устройством.
ms.assetid: f9666abb-151a-74b4-ae0b-ef88f1f252f8
ms.author: abigailc
ms.date: 10/24/2017
ms.topic: article
keywords: windows 10, uwp, игры, элементы управления, ввод
ms.localizationpriority: medium
ms.openlocfilehash: bc5873486bdd9c4adf4ea74b10a240617143ad23
ms.sourcegitcommit: 71e8eae5c077a7740e5606298951bb78fc42b22c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/13/2018
ms.locfileid: "6653826"
---
# <a name="add-controls"></a>Добавление элементов управления


\[ Обновлено для приложений UWP в Windows10. Статьи о Windows 8.x см. в [архиве](http://go.microsoft.com/fwlink/p/?linkid=619132) \]

Хорошая игра для универсальной платформы Windows (UWP) должна поддерживать широкое разнообразие интерфейсов. У потенциального игрока может быть Windows10 установленной без физических кнопок, компьютер с подключенным, контроллером Xbox или последняя планшетный с высокопроизводительной мышью и игровой клавиатурой. В нашей игре элементы управления реализуются в классе [**MoveLookController**](https://github.com/Microsoft/Windows-universal-samples/blob/ef073ed8a2007d113af1d88eddace479e3bf0e07/SharedContent/cpp/GameContent/MoveLookController.cpp). Этот класс агрегирует все три типа ввода (мышь и клавиатура, сенсорный ввод и геймпад) в единый контроллер. В результате получается игра-шутер от первого лица, в которой используются стандартные элементы управления перемещением и обзором, работающие на различных устройствах.

> [!NOTE]
> Подробнее об элементах управления см. в статьях [Элементы управления перемещением и обзором для игр](tutorial--adding-move-look-controls-to-your-directx-game.md) и [Сенсорные элементы управления для игр](tutorial--adding-touch-controls-to-your-directx-game.md).


## <a name="objective"></a>Цель

На данном этапе у нас есть игра, которая отрисовывается, но мы пока не можем перемещать в ней своего игрока или стрелять по мишеням. Давайте рассмотрим, как реализовать в нашей игре UWP на базе DirectX элементы управления перемещением и обзором, характерные для шутера от первого лица, для следующих типов ввода.
- Мышь и клавиатура
- Сенсорный ввод
- Геймпад

>[!Note]
>Если вы еще не скачали последний код для игры из этого примера, перейдите в раздел [Пример игры Direct3D](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/Simple3DGameDX). Этот пример является частью большой коллекции примеров функций UWP. Инструкции по скачиванию этого примера приводятся в статье [Получение примеров UWP из GitHub](https://docs.microsoft.com/windows/uwp/get-started/get-uwp-app-samples).

## <a name="common-control-behaviors"></a>Общее в поведении элементов управления


Сенсорные элементы управления и элементы управления с помощью мыши и клавиатуры реализуются сходным образом. В приложении UWP указатель— это всего лишь точка на экране. Ею можно управлять с помощью мыши либо скольжением пальца по сенсорному экрану. Поэтому можно зарегистрировать единый набор событий, не беспокоясь о том, пользуется ли игрок мышью или сенсорным экраном, чтобы передвигать указатель и нажимать его.

После инициализации класс **MoveLookController** в примере игры регистрирует четыре типа событий для указателя и одно событие для мыши:

Событие | Описание
:------ | :-------
[**CoreWindow::PointerPressed**](https://msdn.microsoft.com/library/windows/apps/br208278) | Нажата (или удерживается) левая или правая кнопка мыши, либо произошло касание сенсорного экрана.
[**CoreWindow::PointerMoved**](https://msdn.microsoft.com/library/windows/apps/br208276) |Мышь переместилась, или провели по сенсорному экрану.
[**CoreWindow::PointerReleased**](https://msdn.microsoft.com/library/windows/apps/br208279) |Левая кнопка мыши была отпущена, или предмет, касавшийся сенсорного экрана, поднялся.
[**CoreWindow::PointerExited**](https://msdn.microsoft.com/library/windows/apps/br208275) |Указатель вышел за пределы главного окна.
[**Windows::Devices::Input::MouseMoved**](https://msdn.microsoft.com/library/windows/apps/hh758356) | Мышь переместилась на определенное расстояние. Обратите внимание, что нас интересуют приращения перемещения мыши, а не текущие координаты X-Y.


Эти обработчики событий устанавливаются и начинают прослушивание ввода пользователя, как только **MoveLookController** инициализируется в окне приложения.
```cpp
void MoveLookController::InitWindow(_In_ CoreWindow^ window)
{
    ResetState();

    window->PointerPressed +=
        ref new TypedEventHandler<CoreWindow^, PointerEventArgs^>(this, &MoveLookController::OnPointerPressed);

    window->PointerMoved +=
        ref new TypedEventHandler<CoreWindow^, PointerEventArgs^>(this, &MoveLookController::OnPointerMoved);

    window->PointerReleased +=
        ref new TypedEventHandler<CoreWindow^, PointerEventArgs^>(this, &MoveLookController::OnPointerReleased);

    window->PointerExited +=
        ref new TypedEventHandler<CoreWindow^, PointerEventArgs^>(this, &MoveLookController::OnPointerExited);

    // There is a separate handler for mouse only relative mouse movement events.
    MouseDevice::GetForCurrentView()->MouseMoved +=
        ref new TypedEventHandler<MouseDevice^, MouseEventArgs^>(this, &MoveLookController::OnMouseMoved);
    //
    // ...
    //
}
```

Полностью код [**InitWindow**](https://github.com/Microsoft/Windows-universal-samples/blob/ef073ed8a2007d113af1d88eddace479e3bf0e07/SharedContent/cpp/GameContent/MoveLookController.cpp#L68-L92) можно увидеть на GitHub.


Для определения того, когда игра должна прослушивать определенные виды ввода, **MoveLookController** имеет три особых состояния, не зависящих от вида контроллера:

Состояние | Описание
:----- | :-------
**None** | Это инициализированное состояние контроллера. Весь ввод игнорируется, поскольку игра еще не ожидает никакого ввода контроллера.
**WaitForInput** | Контроллер ожидает, что игрок подтвердит сообщение от игры нажатием левой кнопкой мыши, событием касания или нажатием кнопки меню на геймпаде.
**Active** | Контроллер находится в режиме активного игрового процесса.



### <a name="waitforinput-state-and-pausing-the-game"></a>Состояние WaitForInput и приостановка игры

В состояние **WaitForInput** игра переходит в случае приостановки. Это случается, если игрок перемещает указатель за пределы главного окна игры или нажимает кнопку "Пауза" (клавиша P или кнопка **Пуск** на геймпаде). **MoveLookController** регистрирует нажатие и сообщает об этом игровому циклу, вызывая метод [**IsPauseRequested**](https://github.com/Microsoft/Windows-universal-samples/blob/ef073ed8a2007d113af1d88eddace479e3bf0e07/SharedContent/cpp/GameContent/MoveLookController.cpp#L107-L127). Если **IsPauseRequested** возвращает значение **true**, игровой цикл вызывает метод **WaitForPress** экземпляра **MoveLookController**, чтобы перевести контроллер в состояние **WaitForInput**. 


После перехода в состояние **WaitForInput** игра прекращает обработку практических всех событий ввода игрового процесса, пока не вернется в состояние **Active**. Исключением является кнопка "Пауза", при нажатии которой игра возвращается в активное состояние. Кроме "Пауза" в порядке для игры, чтобы вернуться в **активное** состояние игрок может выбрать пункт меню. 



### <a name="the-active-state"></a>Состояние Active

В состоянии **Active** экземпляр **MoveLookController** обрабатывает события ввода от всех доступных устройств ввода и преобразовывает их в действия игрока. В результате он обновляет скорость и направление взгляда игрока и передает эти данные в игру— после вызова метода **Update** из игрового цикла.


В состоянии **Active** отслеживается весь ввод указателя, причем разным действиям указателя соответствуют разные идентификаторы указателя.
После получения события [**PointerPressed**](https://msdn.microsoft.com/library/windows/apps/br208278) **MoveLookController** получает значение идентификатора указателя, созданное окном. Идентификатор указателя представляет собой особый вид ввода. Например, при использовании устройства с мультисенсорной технологией можно одновременно совершать несколько активных действий ввода. Идентификаторы используются для отслеживания типа ввода данных игроком. Если событие произошло в прямоугольнике движения сенсорного экрана, назначается идентификатор указателя для отслеживания всех событий указателя в прямоугольнике движения. Другие события указателя в прямоугольнике огня отслеживаются отдельно, с помощью отдельного идентификатора указателя.


> [!NOTE]
> Вводу с помощью мыши и правого аналогового стика геймпада также назначаются идентификаторы, которые обрабатываются отдельно.

Когда события указателя соотнесены с конкретным игровым действием, обновляются данные, которые объект **MoveLookController** передает основному циклу игры.

Будучи вызванным, метод [**Update**](https://github.com/Microsoft/Windows-universal-samples/blob/ef073ed8a2007d113af1d88eddace479e3bf0e07/SharedContent/cpp/GameContent/MoveLookController.cpp#L1005-L1096) в процессах игры-примера обрабатывает ввод и обновляет переменные скорости и направления взгляда (**m\_velocity** и **m\_lookdirection**), которые игровой цикл затем извлекает путем вызова общедоступных методов [**Velocity**](https://github.com/Microsoft/Windows-universal-samples/blob/ef073ed8a2007d113af1d88eddace479e3bf0e07/SharedContent/cpp/GameContent/MoveLookController.cpp#L906-L909) и [**LookDirection**](https://github.com/Microsoft/Windows-universal-samples/blob/ef073ed8a2007d113af1d88eddace479e3bf0e07/SharedContent/cpp/GameContent/MoveLookController.cpp#L913-L923).

> [!NOTE]
> Подробнее о методе [**Update**](#the-update-method) речь пойдет дальше на этой странице.




Проверить, стреляет ли игрок, игровой цикл может путем вызова метода **IsFiring** экземпляра **MoveLookController**. **MoveLookController** проверяет, нажал ли игрок кнопку "Стрелять" на одном из трех возможных устройств ввода.

```cpp
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









Теперь давайте рассмотрим подробнее, как реализован каждый из трех видов элементов управления.

## <a name="adding-relative-mouse-controls"></a>Добавление относительных элементов управления мышью


Если обнаружено перемещение мыши, необходимо использовать это перемещение для определения нового угла поворота и наклона камеры. Для этого необходимо создать относительные элементы управления мышью, с помощью которых относительное расстояние перемещения мыши рассчитывается как разница между начальной и конечной точками перемещения (в отличие от регистрации абсолютных координат перемещения по осям x-y).

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
        rotationDelta.x  = mouseDelta.x * MoveLookConstants::RotationGain;   // scale for control sensitivity
        rotationDelta.y  = mouseDelta.y * MoveLookConstants::RotationGain;

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

## <a name="adding-touch-support"></a>Добавление поддержки сенсорного ввода

Сенсорные элементы управления— прекрасный способ сделать игру доступной пользователям с планшетами. Эта игра собирает сенсорный ввод путем зонирования определенных областей экрана так, чтобы каждая зона соответствовала конкретным действиям в игре.
В этой игре используется три зоны сенсорного ввода.

![компоновка зон сенсорного ввода для управления перемещением и обзором](images/simple-dx-game-controls-touchzones.png)

Следующие команды характеризуют поведение наших элементов сенсорного управления.
Пользовательский ввод | Действие
:------- | :--------
Прямоугольник движения | Сенсорный ввод преобразовывается в виртуальный джойстик, где вертикальное движение превращается в перемещение положения назад/вперед, а горизонтальное движение— в перемещение положение влево/вправо.
Прямоугольник огня | Выстрел шариком.
Касание за пределами прямоугольник движения и прямоугольника огня | Изменение поворота и наклона камеры.

**MoveLookController** проверяет идентификатор указателя, чтобы определить, где произошло событие, и предпринимает одно из следующих действий:

-   Если событие [**PointerMoved**](https://msdn.microsoft.com/library/windows/apps/br208276) произошло в прямоугольнике движения или огня, обновляет положение указателя для контроллера.
-   Если событие [**PointerMoved**](https://msdn.microsoft.com/library/windows/apps/br208276) произошло в любой другой точке оставшейся части экрана (определенной как управление камерой), вычисляет изменение углов наклона и поворота вектора направления камеры.


После того как сенсорные элементы реализованы, прямоугольники, которые мы ранее нарисовали с помощью Direct2D, будут указывать игрокам, где находятся зоны движения, огня и обзора.


![сенсорные элементы управления](images/simple-dx-game-controls-showzones.png)


Теперь давайте рассмотрим, как реализуется каждый из элементов управления.


### <a name="move-and-fire-controller"></a>Контроллер движения и огня
Прямоугольник контроллера движения в левом нижнем квадранте экрана используется в качестве планшета контроллера. Движение пальцем влево и вправо в пределах этого пространства перемещает игрока влево и вправо, а движение вверх и вниз перемещает камеру вперед и назад.
После этого касание контроллера огня в нижнем правом квадранте экрана приводит к выстрелу шариком.

Методы [**SetMoveRect**](https://github.com/Microsoft/Windows-universal-samples/blob/ef073ed8a2007d113af1d88eddace479e3bf0e07/SharedContent/cpp/GameContent/MoveLookController.cpp#L843-L853) и [**SetFireRect**](https://github.com/Microsoft/Windows-universal-samples/blob/ef073ed8a2007d113af1d88eddace479e3bf0e07/SharedContent/cpp/GameContent/MoveLookController.cpp#L857-L867) создают наши прямоугольники ввода, принимая два двухмерных вектора для задания положений верхнего левого и нижнего углов прямоугольников на экране. 


Эти параметры затем назначаются переменным **m\_fireUpperLeft** и **m\_fireLowerRight**, которые позволяют нам определить, касается ли пользователь экрана в пределах одного из прямоугольников. 
```cpp
m_fireUpperLeft  = upperLeft;
m_fireLowerRight = lowerRight;
```

Если размер экрана изменяется, эти прямоугольники перерисовываются в соответствии с новым размером.


Теперь, когда для наших элементов управления отведены отдельные зоны, нам нужно определить, когда пользователь ими пользуется.
Для этого мы создали в методе **MoveLookController::InitWindow** несколько обработчиков событий для случаев, когда пользователь нажимает, перемещает или освобождает указатель.

```cpp
window->PointerPressed +=
    ref new TypedEventHandler<CoreWindow^, PointerEventArgs^>(this, &MoveLookController::OnPointerPressed);

window->PointerMoved +=
    ref new TypedEventHandler<CoreWindow^, PointerEventArgs^>(this, &MoveLookController::OnPointerMoved);

window->PointerReleased +=
    ref new TypedEventHandler<CoreWindow^, PointerEventArgs^>(this, &MoveLookController::OnPointerReleased);
```


Сначала мы определим, что происходит, когда пользователь совершает первое нажатие в пределах прямоугольника движения или прямоугольника огня, используя метод [**OnPointerPressed**](https://github.com/Microsoft/Windows-universal-samples/blob/ef073ed8a2007d113af1d88eddace479e3bf0e07/SharedContent/cpp/GameContent/MoveLookController.cpp#L179-L313).
В данном случае мы проверяем, касается ли пользователь элемента управления, а также находится ли указатель уже в пределах этого элемента управления. Если это первое касание конкретного элемента управления, мы делаем следующее.
- Сохраняем местоположение касания в переменной **m\_moveFirstDown** или **m\_fireFirstDown** в виде двухмерного вектора.
- Назначаем идентификатор указателя переменной **m\_movePointerID** или **m\_firePointerID**.
- Устанавливаем соответствующий флаг **InUse** (**m\_moveInUse** или **m\_fireInUse**) в значение `true`, поскольку у нас теперь есть активный указатель для данного элемента управления.


```cpp
    PointerPoint^ point = args->CurrentPoint;
    uint32 pointerID = point->PointerId;
    Point pointerPosition = point->Position;
    PointerPointProperties^ pointProperties = point->Properties;
    auto pointerDevice = point->PointerDevice;
    auto pointerDeviceType = pointerDevice->PointerDeviceType;

    XMFLOAT2 position = XMFLOAT2(pointerPosition.X, pointerPosition.Y);

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
                    m_moveFirstDown = position;                 // Save the location of the initial contact
                    m_movePointerID = pointerID;                // Store the pointer using this control
                    m_moveInUse = true;                         // Set InUse flag to signal there is an active move pointer
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
                    m_fireLastPoint = position;     // Save the location of the initial contact
                    m_firePointerID = pointerID;    // Store the pointer using this control
                    m_fireInUse = true;             // Set InUse flag to signal there is an active fire pointer
                }
            }
            ...
```


После того как мы определили, касается ли пользователь элемента управления движением или огнем, нужно выяснить, совершает ли пользователь какие-либо движения нажатым пальцем.
С помощью метода [**MoveLookController::OnPointerMoved**](https://github.com/Microsoft/Windows-universal-samples/blob/ef073ed8a2007d113af1d88eddace479e3bf0e07/SharedContent/cpp/GameContent/MoveLookController.cpp#L317-L395) мы проверяем, какой указатель переместился, и сохраняем его новое положение в виде двухмерного вектора.  


```cpp
    PointerPoint^ point = args->CurrentPoint;
    uint32 pointerID = point->PointerId;
    Point pointerPosition = point->Position;
    PointerPointProperties^ pointProperties = point->Properties;
    auto pointerDevice = point->PointerDevice;

    XMFLOAT2 position = XMFLOAT2(pointerPosition.X, pointerPosition.Y);     // convert to allow math
    
    switch (m_state)
    {
    case MoveLookControllerState::Active:
        // Decide which control this pointer is operating.
        
        // Move control
        if (pointerID == m_movePointerID)
        {
            m_movePointerPosition = position;       // Save the current position.
        }
        // Look control
        else if (pointerID == m_lookPointerID)
        {
            // ...
        }
        // Fire control
        else if (pointerID == m_firePointerID)
        {
            m_fireLastPoint = position;
        }
```


Завершив жесты в пределах элементах управления, пользователь отпускает указатель. С помощью метода [**MoveLookController::OnPointerReleased**](https://github.com/Microsoft/Windows-universal-samples/blob/ef073ed8a2007d113af1d88eddace479e3bf0e07/SharedContent/cpp/GameContent/MoveLookController.cpp#L441-L500) мы определяем, какой указатель был отпущен, и проводим серию сбросов.


Если отпущен элемент управления движением, мы делаем следующее.
- Задаем скорость игрока равной `0` во всех направлениях, чтобы он больше не двигался в игре.
- Переведем флаг **m\_moveInUse** в значение `false`, поскольку пользователь больше не касается контроллера движения.
- Установим идентификатор указателя движения в `0`, поскольку в контроллере движения больше нет указателя.

```cpp
       if (pointerID == m_movePointerID)
        {
            m_velocity = XMFLOAT3(0, 0, 0);      // Stop on release.
            m_moveInUse = false;
            m_movePointerID = 0;
        }
```


Что касается элемента управления огнем, если он отпущен, все, что мы делаем,— это переводим флаг **m_fireInUse** в значение `false`, а идентификатор указателя огня— в значение `0`, поскольку в контроллере огня больше нет указателя.
```cpp
        else if (pointerID == m_firePointerID)
        {
            m_fireInUse = false;
            m_firePointerID = 0;
        }
```

### <a name="look-controller"></a>Контроллер обзора
События указателя сенсорного устройства в неиспользуемых областях экрана мы рассматриваем как контроллер обзора. При проведении пальцем в этой зоне меняются поворот и наклон камеры игрока.


Если на сенсорном устройстве в этой зоне происходит событие **MoveLookController::OnPointerPressed** и игра находится в состоянии **Active**, зоне назначается идентификатор указателя.

```cpp
    if (!m_lookInUse)   // If no pointer is in this control yet:
    {
        m_lookLastPoint = position;                   // Save the pointer for a later move.
        m_lookPointerID = pointerID;                  // Store the pointer using this control.
        m_lookLastDelta.x = m_lookLastDelta.y = 0;    // These are for smoothing.
        m_lookInUse = true;
    }
```
Полностью просмотреть код метода **MoveLookController::OnPointerPressed** можно на [GitHub](https://github.com/Microsoft/Windows-universal-samples/blob/ef073ed8a2007d113af1d88eddace479e3bf0e07/SharedContent/cpp/GameContent/MoveLookController.cpp#L252-L259).




В данном случае **MoveLookController** назначает идентификатор указателя для указателя, который вызвал событие, конкретной переменной, которая соответствует зоне управления обзором. В случае сенсорного ввода, происходящих в зоне управления обзором переменная **m\_lookPointerID** присваивается идентификатор указателя, который вызвал событие. Также задается значение логической переменной **m\_lookInUse**, чтобы указать, что элемент управления еще не отпущен.

Теперь давайте рассмотрим, как в примере игры обрабатывается событие [**PointerMoved**](https://msdn.microsoft.com/library/windows/apps/br208276) на сенсорном экране.


В методе **MoveLookController::OnPointerMoved** мы проверяем, какой идентификатор указателя назначен этому событию. Если это **m_lookPointerID**, мы вычисляем изменение положения указателя.
Затем мы используем эту разность для вычисления того, насколько должен измениться поворот. Наконец мы приходим к точке, где можем обновить переменные **m\_pitch** и **m\_yaw**, используемые в игре для изменения поворота пользователя. 

```cpp
    else if (pointerID == m_lookPointerID)     // This is the look pointer.
    {
        // Look control
        XMFLOAT2 pointerDelta;
        pointerDelta.x = position.x - m_lookLastPoint.x;        // How far did the pointer move.
        pointerDelta.y = position.y - m_lookLastPoint.y;

        XMFLOAT2 rotationDelta;
        rotationDelta.x = pointerDelta.x * MoveLookConstants::RotationGain;       // Scale for control sensitivity.
        rotationDelta.y = pointerDelta.y * MoveLookConstants::RotationGain;
        m_lookLastPoint = position;                             // Save for the next time through.

        // Update our orientation based on the command.
        m_pitch -= rotationDelta.y;
        m_yaw   += rotationDelta.x;

        // Limit pitch to straight up or straight down.
        float limit = XM_PI / 2.0f - 0.01f;
        m_pitch = __max(-limit, m_pitch);
        m_pitch = __min(+limit, m_pitch);M_PI / 2.0f, m_pitch);
        }
```





Последнее, что мы рассмотрим,— это как в примере игры обрабатывается событие [**PointerReleased**](https://msdn.microsoft.com/library/windows/apps/br208279) на сенсорном экране.
После того как пользователь завершил сенсорный жест и поднял палец с экрана, вызывается событие [**MoveLookController::OnPointerReleased**](https://github.com/Microsoft/Windows-universal-samples/blob/ef073ed8a2007d113af1d88eddace479e3bf0e07/SharedContent/cpp/GameContent/MoveLookController.cpp#L441-L500).
Если идентификатор указателя, который вызвал событие [**PointerReleased**](https://msdn.microsoft.com/library/windows/apps/br208279), является идентификатором зафиксированного ранее указателя движения, **MoveLookController** устанавливает скорость равной `0`, поскольку пользователь больше не касается зоны управления обзором.

```cpp
    else if (pointerID == m_lookPointerID)
    {
        m_lookInUse = false;
        m_lookPointerID = 0;
    }
```





## <a name="adding-mouse-and-keyboard-support"></a>Добавление поддержки мыши и клавиатуры


В этой игре следующая схема элементов управления для клавиатуры и мыши.

Пользовательский ввод | Действие
:------- | :--------
W | Переместить игрока вперед
A | Переместить игрока влево
S | Переместить игрока назад
D | Переместить игрока вправо
X | Переместить камеру вверх
Пробел | Переместить камеру вниз
P | Приостановить игру
Перемещение мыши | Изменить поворот и наклон камеры
Левая кнопка мыши | Выстрелить шариком


Для использования клавиатуры в примере игры регистрируется два новых события— [**CoreWindow::KeyUp**](https://msdn.microsoft.com/library/windows/apps/br208271) и [**CoreWindow::KeyDown**](https://msdn.microsoft.com/library/windows/apps/br208270)— в методе [**MoveLookController::InitWindow**](https://github.com/Microsoft/Windows-universal-samples/blob/ef073ed8a2007d113af1d88eddace479e3bf0e07/SharedContent/cpp/GameContent/MoveLookController.cpp#L84-L88). Эти события обрабатывают нажатие и отпускание клавиши.

```cpp
window->KeyDown +=
        ref new TypedEventHandler<CoreWindow^, KeyEventArgs^>(this, &MoveLookController::OnKeyDown);

window->KeyUp +=
        ref new TypedEventHandler<CoreWindow^, KeyEventArgs^>(this, &MoveLookController::OnKeyUp);
```

Мышь воспринимается немного иначе, чем сенсорные элементы управления, несмотря на то что она использует указатель. В соответствии с нашей схемой элементов управления **MoveLookController** поворачивает камеру всякий раз, когда игрок перемещает мышь, и стреляет, когда игрок нажимает левую кнопку мыши.


Это обрабатывается в методе [**OnPointerPressed**](https://github.com/Microsoft/Windows-universal-samples/blob/ef073ed8a2007d113af1d88eddace479e3bf0e07/SharedContent/cpp/GameContent/MoveLookController.cpp#L179-L313) экземпляра **MoveLookController**.

В этом методе мы проверяем, какой тип устройства указателя используется, с помощью перечисления [`Windows::Devices::Input::PointerDeviceType`](https://docs.microsoft.com/en-us/uwp/api/Windows.Devices.Input.PointerDeviceType). Если игра находится в состоянии **Active** и **PointerDeviceType**— не **Touch**, предполагается, что ввод производится с помощью мыши.

```cpp
    case MoveLookControllerState::Active:
        switch (pointerDeviceType)
        {
        case Windows::Devices::Input::PointerDeviceType::Touch:
            // Behavior for touch controls
        
        default:
        // Behavior for mouse controls
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
            break;
        }
```

Когда игрок прекращает нажимать одну из кнопок мыши, возникает событие мыши [CoreWindow::PointerReleased](https://docs.microsoft.com/uwp/api/Windows.UI.Core.CoreWindow.PointerReleased), что приводит к вызову метода [MoveLookController::OnPointerReleased](https://github.com/Microsoft/Windows-universal-samples/blob/ef073ed8a2007d113af1d88eddace479e3bf0e07/SharedContent/cpp/GameContent/MoveLookController.cpp#L441-L500), после чего ввод считается завершенным. На этом этапе стрельба шариками прекратится, если левая кнопка мыши была нажата, а теперь отпущена. Поскольку управление обзором включено всегда, игра продолжает использовать тот же указатель мыши для отслеживания дальнейших событий обзора.

```cpp
    case MoveLookControllerState::Active:
        // Touch points
        if (pointerID == m_movePointerID)
        {
            // Stop movement
        }
        else if (pointerID == m_lookPointerID)
        {
            // Stop look rotation
        }
        // Fire button has been released
        else if (pointerID == m_firePointerID)
        {
            // Stop firing
        }
        // Mouse point
        else if (pointerID == m_mousePointerID)
        {
            bool rightButton = pointProperties->IsRightButtonPressed;
            bool leftButton = pointProperties->IsLeftButtonPressed;

            // Mouse no longer in use so stop firing
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



Теперь давайте рассмотрим последний вид элементов управления, который мы будем поддерживать: геймпады. Геймпады обрабатываются не так, как сенсорный ввод и мышь, поскольку не они не используют объект указателя. По этой причине понадобится добавить несколько новых обработчиков событий и методов.

## <a name="adding-gamepad-support"></a>Добавление поддержки геймпада


В данной игре поддержка геймпада добавляется путем вызовов API-интерфейсов [Windows.Gaming.Input](https://docs.microsoft.com/uwp/api/windows.gaming.input). Этот набор API предоставляет доступ к вводу с таких игровых устройств управления, как гоночные рули и рукоятки управления полетом. 


Элементы управления геймпада у нас будут следующие.

Пользовательский ввод | Действие
:------- | :--------
Левый аналоговый стик | Переместить игрока
Правый аналоговый стик | Изменить поворот и наклон камеры
Правый триггер | Выстрелить шариком
Кнопка "Пуск/меню" | Приостановить или возобновить игру




В методе [**InitWindow**](https://github.com/Microsoft/Windows-universal-samples/blob/ef073ed8a2007d113af1d88eddace479e3bf0e07/SharedContent/cpp/GameContent/MoveLookController.cpp#L68-L103) мы добавляем два новых события для проверки того, был ли геймпад [добавлен](https://github.com/Microsoft/Windows-universal-samples/blob/ef073ed8a2007d113af1d88eddace479e3bf0e07/SharedContent/cpp/GameContent/MoveLookController.cpp#L1100-L1105) или [удален](https://github.com/Microsoft/Windows-universal-samples/blob/ef073ed8a2007d113af1d88eddace479e3bf0e07/SharedContent/cpp/GameContent/MoveLookController.cpp#L1109-L1114). Эти события обновляют свойство **m_gamepadsChanged**. Этот параметр используется в методе **UpdatePollingDevices** для проверки, изменился ли список известных геймпадов. 

```cpp
    // Detect gamepad connection and disconnection events.
    Gamepad::GamepadAdded +=
        ref new EventHandler<Gamepad^>(this, &MoveLookController::OnGamepadAdded);

    Gamepad::GamepadRemoved +=
        ref new EventHandler<Gamepad^>(this, &MoveLookController::OnGamepadRemoved);
```

### <a name="the-updatepollingdevices-method"></a>Метод UpdatePollingDevices

Метод [**UpdatePollingDevices**](https://github.com/Microsoft/Windows-universal-samples/blob/ef073ed8a2007d113af1d88eddace479e3bf0e07/SharedContent/cpp/GameContent/MoveLookController.cpp#L654-L782) экземпляра **MoveLookController** немедленно проверяет, подключен ли геймпад. Если да, мы начинаем считывать его состояние с помощью метода [**Gamepad.GetCurrentReading**](https://docs.microsoft.com/uwp/api/windows.gaming.input.gamepad.GetCurrentReading). Этот метод возвращает структуру [**GamepadReading**](https://docs.microsoft.com/uwp/api/Windows.Gaming.Input.GamepadReading), позволяя нам проверить, какие кнопки были нажаты или какие аналоговые стики перемещены.


Если состояние игры— **WaitForInput**, мы прослушиваем только кнопку "Пуск/меню" контроллера, чтобы можно было возобновить игру.


Если состояние игры— **Active**, мы проверяем ввод пользователя, чтобы определить, какое внутриигровое действие должно произойти.
Например, если пользователь переместил левый аналоговый стик в определенном направлении, это сообщает игре, что нужно переместить игрока в направлении перемещения стика. Перемещение стика в определенном направлении должно превысить радиус **мертвой зоны**, иначе ничего не произойдет. Радиус мертвой зоны необходим для предотвращения непроизвольного смещения, когда контроллер реагирует на незначительные движения большого пальца, лежащего на стике. Без мертвых зон элементы управления могут казаться пользователю слишком чувствительными.


Ввод аналогового стика находится в диапазоне от –1 и 1 как по оси X, так и по оси Y. Следующая константа задает радиус мертвой зоны аналогового стика.

```cpp
#define THUMBSTICK_DEADZONE 0.25f
```

С помощью этой переменной мы начинаем обрабатывать ввод с аналогового стика, который требует внутриигрового действия. Перемещение произойдет со значением от [–1, –0,26] или [0,26, 1] по любой из осей.

![мертвая зона для аналоговых стиков](images/simple-dx-game-controls-deadzone.png)

Эта часть метода **UpdatePollingDevices** обрабатывает левый и правый аналоговые стики.
Значения X и Y каждого стика проверяются на предмет того, выходят ли они за мертвую зону. Если одно или оба значения выходят за мертвую зону, мы обновляем соответствующий компонент.
Например, если левый аналоговый стик перемещается по оси X, мы добавим –1 к компоненту **x** вектора **m_moveCommand**. Этот вектор будет использоваться для агрегирования всех перемещений со всех устройств и позднее использоваться для расчета того, куда должен переместиться пользователь. 


```cpp
        // Use the left thumbstick to control the eye point position (position of the player)

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


```cpp
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
        rotationDelta.x = pointerDelta.x *  0.08f;       // Scale for control sensitivity.
        rotationDelta.y = pointerDelta.y *  0.08f;

        // Update our orientation based on the command.
        m_pitch += rotationDelta.y;
        m_yaw += rotationDelta.x;

        // Limit pitch to straight up or straight down.
        m_pitch = __max(-XM_PI / 2.0f, m_pitch);
        m_pitch = __min(+XM_PI / 2.0f, m_pitch);
```

Элементы управления игры были бы неполными без возможности стрелять шариками.


Метод **UpdatePollingDevices** метод также проверяет, нажат ли правый триггер. Если да, свойство **m_firePressed** устанавливается в значение true, что дает игре сигнал начать стрельбу.
```cpp
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
- Если нажаты какие-либо из клавиш движения на клавиатуре, значение `1.0f` или `-1.0f` добавляется к соответствующему компоненту вектора **m_moveCommand**— &mdash; `1.0f` для перемещения вперед, `-1.0f` для перемещения назад.


Как только весь ввод перемещений учтен, мы прогоняем вектор **m_moveCommand** через некоторые расчеты, чтобы сформировать новый вектор, представляющий направление игрока по отношению к игровому миру.
Затем мы берем наши перемещения по отношению к миру и применяем их к игроку в качестве скорости в этом направлении.
Наконец, мы сбрасываем вектор **m_moveCommand** в `(0.0f, 0.0f, 0.0f)`, чтобы все было готово для следующего кадра игры.


```cpp
void MoveLookController::Update()
{
    // Get any gamepad input and update state
    UpdatePollingDevices();

    // Get change in 
    if (m_moveInUse)
    {
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
    // Our velocity is based on the command. Y is up.
    m_velocity.x = -wCommand.x * MoveLookConstants::MovementGain;
    m_velocity.z =  wCommand.y * MoveLookConstants::MovementGain;
    m_velocity.y =  wCommand.z * MoveLookConstants::MovementGain;

    // Clear movement input accumulator for use during next frame.
    m_moveCommand = XMFLOAT3(0.0f, 0.0f, 0.0f);
}
```


## <a name="next-steps"></a>Следующие шаги

Итак, элементы управления добавлены. Для создания иммерсивной игры не хватает еще одной вещи— звука!
Музыка и звуковые эффекты очень важны для любой игры, поэтому следующее, что мы рассмотрим,— это [добавление звука](tutorial--adding-sound.md).
 

 

 




