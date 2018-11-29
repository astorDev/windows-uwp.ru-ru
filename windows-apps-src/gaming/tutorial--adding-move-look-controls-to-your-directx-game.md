---
title: Элементы управления перемещением и обзором для игр
description: Узнайте, как добавить в игру DirectX традиционные элементы управления перемещением и обзором с помощью мыши и клавиатуры.
ms.assetid: 4b4d967c-3de9-8a97-ae68-0327f00cc933
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp, игры, перемещение и обзор, элементы управления
ms.localizationpriority: medium
ms.openlocfilehash: 222f46bbda165442003aecea0bbd138bcb844a3b
ms.sourcegitcommit: 89ff8ff88ef58f4fe6d3b1368fe94f62e59118ad
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/29/2018
ms.locfileid: "8192894"
---
# <a name="span-iddevgamingtutorialaddingmove-lookcontrolstoyourdirectxgamespanmove-look-controls-for-games"></a><span id="dev_gaming.tutorial__adding_move-look_controls_to_your_directx_game"></span>Элементы управления перемещением и обзором для игр



В этом учебном руководстве описывается добавление традиционных элементов управления перемещением и обзором, т. е. мыши и клавиатуры, в игру DirectX.

Также описывается поддержка перемещения и обзора для сенсорных устройств. Устройство управления перемещением определяется в виде раздела экрана в нижнем левом углу, который функционирует как поле направленного ввода; устройство управления обзором определяется для остальной части экрана; камера располагается по центру на последнем месте, которого касается игрок в этой области.

Если вы не знакомы с этой концепцией элементов управления, ее можно представить следующим образом: клавиатура (или поле направленного ввода для касаний) управляет вашими ногами в трехмерном пространстве и ведет себя так, как будто ваши ноги могут перемещаться только вперед-назад или влево-вправо. Мышь или указатель касания управляет вашей головой. Ваша голова используется для обзора влево и вправо, вниз и вверх или в другую сторону в этой плоскости. Если в поле зрения существует цель, вы центрируете поле зрения камеры на этой цели с помощью мыши и затем нажимаете клавишу "Вперед" для перемещения по направлению к ней или клавишу "Назад" для перемещения от нее. Чтобы обойти цель по кругу, вам необходимо держать поле зрения камеры на цели и одновременно перемещаться влево или вправо. Вы увидите, насколько эффективен этот метод управления для навигации в трехмерных средах!

В играх эти элементы управления называются элементами управления WASD, где кнопки W, A, S и D используются для движения камеры в плоскости X-Z, а мышь используется для управления вращением камеры вокруг осей X и Y.

## <a name="objectives"></a>Цели


-   Добавить простейшие элементы управления перемещением и обзором в игру DirectX для мыши и клавиатуры, а также сенсорных экранов.
-   Реализовать камеру с видом от первого лица, используемую для навигации в трехмерной среде.

## <a name="a-note-on-touch-control-implementations"></a>Замечание по реализации элементов управления касанием


Для элементов управления касанием мы реализуем два устройства управления: устройство управления перемещением, которое обрабатывает перемещения в плоскости X-Z относительно точки зрения камеры, и устройство управления обзором, которое направляет камеру. Устройство управления перемещением соответствует кнопкам WASD на клавиатуре, а устройство управления обзором соответствует мыши. Однако для элементов управления касанием необходимо определить область экрана, используемую в качестве направленного ввода, или виртуальные кнопки WASD, а остальную часть экрана необходимо использовать в качестве пространства ввода для элементов управления обзором.

Экран выглядит следующим образом.

![структура устройств управления перемещением и обзором](images/movelook-touch.png)

Когда вы перемещаете указатель касания (не мыши) в левую нижнюю часть экрана, то любое перемещение наверх заставляет камеру перемещаться вперед. Любое перемещение вниз заставляет камеру перемещаться назад. То же самое касается перемещений вправо и влево внутри области указателя устройства управления перемещением. Вне этой области расположена область устройства управления обзором. Вы просто касаетесь или перетаскиваете камеру в требуемое место.

## <a name="set-up-the-basic-input-event-infrastructure"></a>Настройка инфраструктуры основных событий ввода


Сначала необходимо создать класс элемента управления, который будет использоваться для обработки событий ввода мыши и клавиатуры, и обновить положение камеры с учетом данных с этих устройств ввода. Поскольку мы реализуем элементы управления перемещением и обзором, необходимо вызвать **MoveLookController**.

```cpp
using namespace Windows::UI::Core;
using namespace Windows::System;
using namespace Windows::Foundation;
using namespace Windows::Devices::Input;
#include <DirectXMath.h>

// Methods to get input from the UI pointers
ref class MoveLookController
{
};  // class MoveLookController
```

Теперь создадим заголовок, который определяет состояние устройства управления перемещением и обзором и его камеры с видом от первого лица, а также базовые методы и обработчики событий, которые реализуют элементы управления и обновляют состояние камеры.

```cpp
#define ROTATION_GAIN 0.004f    // Sensitivity adjustment for the look controller
#define MOVEMENT_GAIN 0.1f      // Sensitivity adjustment for the move controller

ref class MoveLookController
{
private:
    // Properties of the controller object
    DirectX::XMFLOAT3 m_position;               // The position of the controller
    float m_pitch, m_yaw;           // Orientation euler angles in radians

    // Properties of the Move control
    bool m_moveInUse;               // Specifies whether the move control is in use
    uint32 m_movePointerID;         // Id of the pointer in this control
    DirectX::XMFLOAT2 m_moveFirstDown;          // Point where initial contact occurred
    DirectX::XMFLOAT2 m_movePointerPosition;   // Point where the move pointer is currently located
    DirectX::XMFLOAT3 m_moveCommand;            // The net command from the move control

    // Properties of the Look control
    bool m_lookInUse;               // Specifies whether the look control is in use
    uint32 m_lookPointerID;         // Id of the pointer in this control
    DirectX::XMFLOAT2 m_lookLastPoint;          // Last point (from last frame)
    DirectX::XMFLOAT2 m_lookLastDelta;          // For smoothing

    bool m_forward, m_back;         // States for movement
    bool m_left, m_right;
    bool m_up, m_down;


public:

    // Methods to get input from the UI pointers
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

    void OnKeyDown(
        _In_ Windows::UI::Core::CoreWindow^ sender,
        _In_ Windows::UI::Core::KeyEventArgs^ args
        );

    void OnKeyUp(
        _In_ Windows::UI::Core::CoreWindow^ sender,
        _In_ Windows::UI::Core::KeyEventArgs^ args
        );

    // Set up the Controls that this controller supports
    void Initialize( _In_ Windows::UI::Core::CoreWindow^ window );

    void Update( Windows::UI::Core::CoreWindow ^window );
    
internal:
    // Accessor to set position of controller
    void SetPosition( _In_ DirectX::XMFLOAT3 pos );

    // Accessor to set position of controller
    void SetOrientation( _In_ float pitch, _In_ float yaw );

    // Returns the position of the controller object
    DirectX::XMFLOAT3 get_Position();

    // Returns the point  which the controller is facing
    DirectX::XMFLOAT3 get_LookPoint();


};  // class MoveLookController
```

Код содержит 4 группы частных полей. Рассмотрим предназначение каждой группы.

Сначала мы определяем поля, которые предназначены для хранения обновленных сведений о поле зрения камеры.

-   **m\_position** — положение камеры и, следовательно, плоскости отображения в трехмерной сцене, которое определяется с помощью координат сцены.
-   **m\_pitch** — угол наклона камеры или поворот вниз-вверх вокруг оси X плоскости отображения в радианах.
-   **m\_yaw** — угол поворота камеры относительно вертикальной оси или поворот влево-вправо вокруг оси Y плоскости отображения в радианах.

Теперь определим поля, используемые для хранения сведений о состоянии и расположении устройств управления. Сначала определим поля, используемые для устройства управления перемещением на основе касания. (Реализация устройства управления перемещением с помощью клавиатуры не накладывает каких-либо требований. Для этого необходимо считывать события клавиатуры со специальными обработчиками.)

-   **m\_moveInUse** — указывает, используется ли устройство управления перемещением.
-   **m\_movePointerID** — уникальный идентификатор для текущего указателя перемещения. Этот идентификатор используется, чтобы различать указатель обзора и указатель перемещения при проверке значения идентификатора указателя.
-   **m\_moveFirstDown** — точка на экране, в которой игрок сначала касается области указателя устройства управления перемещением. Позднее с помощью этого значения зададим "мертвую" зону для предотвращения дрожания плоскости отображения из-за небольших перемещений.
-   **m\_movePointerPosition** — точка экрана, в которую игрок только что переместил указатель. Она нужна, чтобы путем сравнения с **m\_moveFirstDown** определить, в каком направлении игрок намерен передвинуть камеру.
-   **m\_moveCommand** — последняя вычисляемая команда для устройства управления перемещением: вверх (вперед), вниз (назад), влево или вправо.

Теперь определим поля, предназначенные для устройства управления обзором, — с возможностью управления мышью и касанием.

-   **m\_lookInUse** — указывает, используется ли элемент управления обзором.
-   **m\_lookPointerID** — уникальный идентификатор текущего указателя обзора. Этот идентификатор используется, чтобы различать указатель обзора и указатель перемещения при проверке значения идентификатора указателя.
-   **m\_lookLastPoint** — последняя точка в координатах сцены, полученная в предыдущем кадре.
-   **m\_lookLastDelta** — вычисленная разница между текущими значениями **m\_position** и **m\_lookLastPoint**.

В завершение определим 6 логических значений для 6 степеней свободы, которые используются для указания текущего состояния каждого действия направленного движения (on или off):

-   **m\_forward**, **m\_back**, **m\_left**, **m\_right**, **m\_up** и **m\_down**.

Для получения входных данных, которые используются для обновления состояния устройств управления, используются 6 обработчиков событий:

-   **OnPointerPressed**. Игрок нажал левую кнопку мыши, когда указатель расположен на экране игры, или коснулся экрана.
-   **OnPointerMoved**. Игрок переместил мышь с указателем на экране игры или перетащил указатель касания на экран.
-   **OnPointerReleased**. Игрок отпустил левую кнопку мыши с указателем на экране игры или отнял палец от экрана.
-   **OnKeyDown**. Игрок нажал клавишу.
-   **OnKeyUp**. Игрок отпустил клавишу.

Для инициализации и обновления сведений о состоянии устройств управления, а также доступа к ним используются следующие методы и свойства.

-   **Initialize**. Приложение вызывает этот обработчик событий для инициализации элементов управления и присоединения их к объекту [**CoreWindow**](https://msdn.microsoft.com/library/windows/apps/br208225), который описывает окно отображения.
-   **SetPosition**. Приложение вызывает этот метод, чтобы задать координаты элементов управления в пространстве сцены (X, Y и Z).
-   **SetOrientation**. Приложение вызывает этот метод, чтобы задать уклон и поворот камеры относительно вертикальной оси.
-   **get\_Position**. С помощью этого свойства приложение получает доступ к текущему расположению камеры в пространстве сцены. Это свойство используется для связи текущего расположения камеры с приложением.
-   **get\_LookPoint**. С помощью этого свойства приложение получает текущую точку, к которой обращено устройство управления камерой.
-   **Update**. Считывает состояние устройств управления перемещением и обзором и обновляет расположение камеры. Этот метод постоянно вызывается из главного цикла приложения для обновления данных устройства управления камерой и расположения камеры в пространстве сцены.

Теперь вы знакомы со всеми компонентами, необходимыми для реализации элементов управления перемещением и обзором. Свяжем эти компоненты воедино.

## <a name="create-the-basic-input-events"></a>Создание простейших событий ввода


Диспетчер событий среды выполнения Windows предоставляет 5 событий, для обработки которых мы хотим создать экземпляры класса **MoveLookController**:

-   [**PointerPressed**](https://msdn.microsoft.com/library/windows/apps/br208278)
-   [**PointerMoved**](https://msdn.microsoft.com/library/windows/apps/br208276)
-   [**PointerReleased**](https://msdn.microsoft.com/library/windows/apps/br208279)
-   [**KeyUp**](https://msdn.microsoft.com/library/windows/apps/br208271)
-   [**KeyDown**](https://msdn.microsoft.com/library/windows/apps/br208270)

Эти события реализуются по типу [**CoreWindow**](https://msdn.microsoft.com/library/windows/apps/br208225). Предполагается, что у вас есть объект **CoreWindow** для работы с этим типом. Дополнительные сведения о создании этого объекта см. в разделе [Настройка приложения UWP с использованием C++ для отображения представления DirectX](https://msdn.microsoft.com/library/windows/apps/hh465077).

Поскольку эти события возникают, когда приложение запущено, обработчики обновляют сведения о состоянии устройства управления, определенные в частных полях.

Сначала добавим обработчики событий мыши и указателя касания. В первом обработчике событий **OnPointerPressed()** для получения координат X-Y указателя используется тип [**CoreWindow**](https://msdn.microsoft.com/library/windows/apps/br208225), который управляет экраном, когда пользователь нажимает кнопку мыши или касается экрана в области устройства управления обзором.

**OnPointerPressed**

```cpp
void MoveLookController::OnPointerPressed(
_In_ CoreWindow^ sender,
_In_ PointerEventArgs^ args)
{
    // Get the current pointer position.
    uint32 pointerID = args->CurrentPoint->PointerId;
    DirectX::XMFLOAT2 position = DirectX::XMFLOAT2( args->CurrentPoint->Position.X, args->CurrentPoint->Position.Y );

    auto device = args->CurrentPoint->PointerDevice;
    auto deviceType = device->PointerDeviceType;
    if ( deviceType == PointerDeviceType::Mouse )
    {
        // Action, Jump, or Fire
    }

    // Check  if this pointer is in the move control.
    // Change the values  to percentages of the preferred screen resolution.
    // You can set the x value to <preferred resolution> * <percentage of width>
    // for example, ( position.x < (screenResolution.x * 0.15) ).

    if (( position.x < 300 && position.y > 380 ) && ( deviceType != PointerDeviceType::Mouse ))
    {
        if ( !m_moveInUse ) // if no pointer is in this control yet
        {
            // Process a DPad touch down event.
            m_moveFirstDown = position;                 // Save the location of the initial contact.
            m_movePointerPosition = position;
            m_movePointerID = pointerID;                // Store the id of the pointer using this control.
            m_moveInUse = TRUE;
        }
    }
    else // This pointer must be in the look control.
    {
        if ( !m_lookInUse ) // If no pointer is in this control yet...
        {
            m_lookLastPoint = position;                         // save the point for later move
            m_lookPointerID = args->CurrentPoint->PointerId;  // store the id of pointer using this control
            m_lookLastDelta.x = m_lookLastDelta.y = 0;          // these are for smoothing
            m_lookInUse = TRUE;
        }
    }
}
```

Этот обработчик событий проверяет, не является ли указатель указателем мыши (для этого образца, который поддерживает мышь и касания) и не находится ли он в области устройства управления перемещением. Если оба условия выполняются, то он проверяет, был ли нажат указатель, в частности не относится ли это нажатие или щелчок к предыдущему вводу перемещения или обзора, путем выявления значения false для **m\_moveInUse**. В таком случае обработчик получает точку в области устройства управления перемещением, в которой произошло нажатие, и присваивает **m\_moveInUse** значение true, чтобы при повторном вызове обработчика он не перезаписывал начальную точку взаимодействия через ввод для устройства управления перемещением. Обработчик также присваивает идентификатору указателя устройства управления перемещением значение идентификатора текущего указателя.

Если используется указатель мыши или если указатель касания не находится в области устройства управления перемещением, он должен находиться в области устройства управления обзором. Он присваивает **m\_lookLastPoint** текущее расположение, в котором пользователь нажимает кнопку мыши или касается экрана, сбрасывает разницу и присваивает идентификатору указателя устройства управления обзором значение идентификатора текущего указателя. Он также задает активное состояние устройства управления обзором.

**OnPointerMoved**

```cpp
void MoveLookController::OnPointerMoved(
    _In_ CoreWindow ^sender,
    _In_ PointerEventArgs ^args)
{
    uint32 pointerID = args->CurrentPoint->PointerId;
    DirectX::XMFLOAT2 position = DirectX::XMFLOAT2(args->CurrentPoint->Position.X, args->CurrentPoint->Position.Y);

    // Decide which control this pointer is operating.
    if (pointerID == m_movePointerID)           // This is the move pointer.
    {
        // Move control
        m_movePointerPosition = position;       // Save the current position.

    }
    else if (pointerID == m_lookPointerID)      // This is the look pointer.
    {
        // Look control

        DirectX::XMFLOAT2 pointerDelta;
        pointerDelta.x = position.x - m_lookLastPoint.x;        // How far did pointer move
        pointerDelta.y = position.y - m_lookLastPoint.y;

        DirectX::XMFLOAT2 rotationDelta;
        rotationDelta.x = pointerDelta.x * ROTATION_GAIN;   // Scale for control sensitivity.
        rotationDelta.y = pointerDelta.y * ROTATION_GAIN;

        m_lookLastPoint = position;                     // Save for the next time through.

                                                        // Update our orientation based on the command.
        m_pitch -= rotationDelta.y;                     // Mouse y increases down, but pitch increases up.
        m_yaw -= rotationDelta.x;                       // Yaw is defined as CCW around the y-axis.

                                                        // Limit the pitch to straight up or straight down.
        m_pitch = (float)__max(-DirectX::XM_PI / 2.0f, m_pitch);
        m_pitch = (float)__min(+DirectX::XM_PI / 2.0f, m_pitch);
    }
}
```

Обработчик событий **OnPointerMoved** вызывается при любом перемещении указателя (в данном случае — при перетаскивании указателя сенсорного экрана или перемещении указателя мыши с нажатой левой кнопкой мыши). Если идентификатор указателя совпадает с идентификатором указателя устройства управления перемещением, он является указателем перемещения. В противном случае выполняется проверка, является ли устройство управления обзором активным указателем.

Если устройство является устройством управления перемещением, расположение указателя обновляется. Расположение обновляется до тех пор, пока вызывается событие [**PointerMoved**](https://msdn.microsoft.com/library/windows/apps/br208276), поскольку необходимо сравнить конечное расположение с расположением, полученным с помощью обработчика событий **OnPointerPressed**.

Если устройство является устройством управления обзором, все немного усложняется. Нам необходимо вычислить новую точку обзора и центрировать камеру на ней. Для этого необходимо найти разницу между последней точкой обзора и текущим расположением экрана, а затем умножить полученное значение на коэффициент масштабирования, с помощью которого можно увеличить или уменьшить перемещения относительно расстояния перемещения по экрану. С помощью этого значения вычисляется угол тангажа и угол рыскания.

В завершение необходимо отключить поведение устройства управления перемещением или обзором, когда игрок перестает двигать мышь или касаться экрана. При возникновении события **OnPointerReleased** мы вызываем [**PointerReleased**](https://msdn.microsoft.com/library/windows/apps/br208279), чтобы присвоить **m\_moveInUse** или **m\_lookInUse** значение FALSE и отключить сдвиг камеры, а также обнулить идентификатор указателя.

**OnPointerReleased**

```cpp
void MoveLookController::OnPointerReleased(
_In_ CoreWindow ^sender,
_In_ PointerEventArgs ^args)
{
    uint32 pointerID = args->CurrentPoint->PointerId;
    DirectX::XMFLOAT2 position = DirectX::XMFLOAT2( args->CurrentPoint->Position.X, args->CurrentPoint->Position.Y );


    if ( pointerID == m_movePointerID )    // This was the move pointer.
    {
        m_moveInUse = FALSE;
        m_movePointerID = 0;
    }
    else if (pointerID == m_lookPointerID ) // This was the look pointer.
    {
        m_lookInUse = FALSE;
        m_lookPointerID = 0;
    }
}
```

Таким образом, мы обработали все события сенсорного экрана. Теперь обработаем основные события ввода для устройства управления перемещением с применением клавиатуры.

**OnKeyDown**

```cpp
void MoveLookController::OnKeyDown(
                                   __in CoreWindow^ sender,
                                   __in KeyEventArgs^ args )
{
    Windows::System::VirtualKey Key;
    Key = args->VirtualKey;

    // Figure out the command from the keyboard.
    if ( Key == VirtualKey::W )     // Forward
        m_forward = true;
    if ( Key == VirtualKey::S )     // Back
        m_back = true;
    if ( Key == VirtualKey::A )     // Left
        m_left = true;
    if ( Key == VirtualKey::D )     // Right
        m_right = true;
}
```

Поскольку одним из этих событий является нажатие, этот обработчик событий присваивает соответствующему состоянию направленного перемещения значение true.

**OnKeyUp**

```cpp
void MoveLookController::OnKeyUp(
                                 __in CoreWindow^ sender,
                                 __in KeyEventArgs^ args)
{
    Windows::System::VirtualKey Key;
    Key = args->VirtualKey;

    // Figure out the command from the keyboard.
    if ( Key == VirtualKey::W )     // forward
        m_forward = false;
    if ( Key == VirtualKey::S )     // back
        m_back = false;
    if ( Key == VirtualKey::A )     // left
        m_left = false;
    if ( Key == VirtualKey::D )     // right
        m_right = false;
}
```

После отпускания клавиши обработчик событий присваивает значение false. При вызове **Update** выполняется проверка этих состояний перемещения, и камера перемещается соответствующим образом. Это немного проще, чем реализация касания!

## <a name="initialize-the-touch-controls-and-the-controller-state"></a>Инициализация элементов управления касанием и состояния устройства управления


Обработаем события и выполним инициализацию всех полей состояния устройства управления.

**Initialize**

```cpp
void MoveLookController::Initialize( _In_ CoreWindow^ window )
{

    // Opt in to receive touch/mouse events.
    window->PointerPressed += 
    ref new TypedEventHandler<CoreWindow^, PointerEventArgs^>(this, &MoveLookController::OnPointerPressed);

    window->PointerMoved += 
    ref new TypedEventHandler<CoreWindow^, PointerEventArgs^>(this, &MoveLookController::OnPointerMoved);

    window->PointerReleased += 
    ref new TypedEventHandler<CoreWindow^, PointerEventArgs^>(this, &MoveLookController::OnPointerReleased);

    window->CharacterReceived +=
    ref new TypedEventHandler<CoreWindow^, CharacterReceivedEventArgs^>(this, &MoveLookController::OnCharacterReceived);

    window->KeyDown += 
    ref new TypedEventHandler<CoreWindow^, KeyEventArgs^>(this, &MoveLookController::OnKeyDown);

    window->KeyUp += 
    ref new TypedEventHandler<CoreWindow^, KeyEventArgs^>(this, &MoveLookController::OnKeyUp);

    // Initialize the state of the controller.
    m_moveInUse = FALSE;                // No pointer is in the Move control.
    m_movePointerID = 0;

    m_lookInUse = FALSE;                // No pointer is in the Look control.
    m_lookPointerID = 0;

    //  Need to init this as it is reset every frame.
    m_moveCommand = DirectX::XMFLOAT3( 0.0f, 0.0f, 0.0f );

    SetOrientation( 0, 0 );             // Look straight ahead when the app starts.

}
```

В качестве параметра **Initialize** принимает ссылку на экземпляр приложения [**CoreWindow**](https://msdn.microsoft.com/library/windows/apps/br208225) и регистрирует обработчики событий, созданные для соответствующих событий **CoreWindow**. Метод инициализирует идентификаторы указателей обзора и перемещения, присваивает значение 0 вектору команды реализации устройства управления перемещением сенсорного экрана и задает правильное расположение камеры (прямо) при запуске приложения.

## <a name="getting-and-setting-the-position-and-orientation-of-the-camera"></a>Получение и задание расположения и ориентации камеры


Определим некоторые методы получения или задания расположения камеры относительно окна просмотра.

```cpp
void MoveLookController::SetPosition( _In_ DirectX::XMFLOAT3 pos )
{
    m_position = pos;
}

// Accessor to set the position of the controller.
void MoveLookController::SetOrientation( _In_ float pitch, _In_ float yaw )
{
    m_pitch = pitch;
    m_yaw = yaw;
}

// Returns the position of the controller object.
DirectX::XMFLOAT3 MoveLookController::get_Position()
{
    return m_position;
}

// Returns the point at which the camera controller is facing.
DirectX::XMFLOAT3 MoveLookController::get_LookPoint()
{
    float y = sinf(m_pitch);        // Vertical
    float r = cosf(m_pitch);        // In the plane
    float z = r*cosf(m_yaw);        // Fwd-back
    float x = r*sinf(m_yaw);        // Left-right
    DirectX::XMFLOAT3 result(x,y,z);
    result.x += m_position.x;
    result.y += m_position.y;
    result.z += m_position.z;

    // Return m_position + DirectX::XMFLOAT3(x, y, z);
    return result;
}
```

## <a name="updating-the-controller-state-info"></a>Обновление сведений о состоянии устройства управления


Преобразуем отслеживаемые в **m\_movePointerPosition** координаты указателя в новые координаты, соответствующие системе мировых координат. Приложение вызывает этот метод каждый раз при обновлении основного цикла приложения. Здесь мы вычисляем новое расположение точки обзора, которое необходимо передать в приложение для обновления матрицы обзора перед проецированием в окно просмотра.

```cpp
void MoveLookController::Update(CoreWindow ^window)
{
    // Check for input from the Move control.
    if (m_moveInUse)
    {
        DirectX::XMFLOAT2 pointerDelta(m_movePointerPosition);
        pointerDelta.x -= m_moveFirstDown.x;
        pointerDelta.y -= m_moveFirstDown.y;

        // Figure out the command from the touch-based virtual joystick.
        if (pointerDelta.x > 16.0f)      // Leave 32 pixel-wide dead spot for being still.
            m_moveCommand.x = 1.0f;
        else
            if (pointerDelta.x < -16.0f)
            m_moveCommand.x = -1.0f;

        if (pointerDelta.y > 16.0f)      // Joystick y is up, so change sign.
            m_moveCommand.y = -1.0f;
        else
            if (pointerDelta.y < -16.0f)
            m_moveCommand.y = 1.0f;
    }

    // Poll our state bits that are set by the keyboard input events.
    if (m_forward)
        m_moveCommand.y += 1.0f;
    if (m_back)
        m_moveCommand.y -= 1.0f;

    if (m_left)
        m_moveCommand.x -= 1.0f;
    if (m_right)
        m_moveCommand.x += 1.0f;

    if (m_up)
        m_moveCommand.z += 1.0f;
    if (m_down)
        m_moveCommand.z -= 1.0f;

    // Make sure that 45 degree cases are not faster.
    DirectX::XMFLOAT3 command = m_moveCommand;
    DirectX::XMVECTOR vector;
    vector = DirectX::XMLoadFloat3(&command);

    if (fabsf(command.x) > 0.1f || fabsf(command.y) > 0.1f || fabsf(command.z) > 0.1f)
    {
        vector = DirectX::XMVector3Normalize(vector);
        DirectX::XMStoreFloat3(&command, vector);
    }
    

    // Rotate command to align with our direction (world coordinates).
    DirectX::XMFLOAT3 wCommand;
    wCommand.x = command.x*cosf(m_yaw) - command.y*sinf(m_yaw);
    wCommand.y = command.x*sinf(m_yaw) + command.y*cosf(m_yaw);
    wCommand.z = command.z;

    // Scale for sensitivity adjustment.
    wCommand.x = wCommand.x * MOVEMENT_GAIN;
    wCommand.y = wCommand.y * MOVEMENT_GAIN;
    wCommand.z = wCommand.z * MOVEMENT_GAIN;

    // Our velocity is based on the command.
    // Also note that y is the up-down axis. 
    DirectX::XMFLOAT3 Velocity;
    Velocity.x = -wCommand.x;
    Velocity.z = wCommand.y;
    Velocity.y = wCommand.z;

    // Integrate
    m_position.x += Velocity.x;
    m_position.y += Velocity.y;
    m_position.z += Velocity.z;

    // Clear movement input accumulator for use during the next frame.
    m_moveCommand = DirectX::XMFLOAT3(0.0f, 0.0f, 0.0f);

}
```

Поскольку вибрации движения, возникающие при использовании устройства управления перемещением касанием, нам не нужны, зададим виртуальную «мертвую» зону вокруг указателя диаметром 32 пикселя. Также добавим скорость, которая представляет собой сумму значений команды и скорости перемещения. (Вы можете скорректировать это поведение по своему усмотрению, чтобы ускорить или замедлить движение на основании расстояния, на которое перемещается указатель в области устройства управления перемещением.)

При вычислении скорости также необходимо преобразовать координаты, полученные от устройств управления перемещением и обзором, в перемещение фактической точки обзора, отправляемой в метод, который рассчитывает матрицу обзора для сцены. Сначала необходимо преобразовать координату по оси X, поскольку при щелчке и перемещении или перетаскивании вправо или влево устройства управления обзором точка обзора перевернется в противоположное направление на сцене, поскольку камера может колебаться вокруг центральной оси. Затем необходимо поменять оси Y и Z, поскольку нажатие клавиши "Вниз" или "Вверх" или движение перетаскивания (значение по оси Y) на устройстве управления перемещением должно преобразовываться в действие камеры, которая приближает или удаляет точку обзора (ось Z).

Последнее расположение точки обзора для игрока — это сумма последнего расположения и вычисленной скорости. Это значение считывается при вызове метода **get\_Position** (вероятнее всего, в течение настройки для каждого кадра). После этого команде перемещения присваивается значение 0.

## <a name="updating-the-view-matrix-with-the-new-camera-position"></a>Обновление матрицы обзора с учетом нового расположения камеры


Мы можем получить координаты пространства сцены, на которых сфокусирована наша камера. Эти координаты обновляются с интервалом, заданным в приложении (например, каждые 60 секунд в основном цикле приложения). В следующем псевдокоде предлагается поведение вызова, которое вы можете реализовать.

```cpp
myMoveLookController->Update( m_window );   

// Update the view matrix based on the camera position.
myFirstPersonCamera->SetViewParameters(
                 myMoveLookController->get_Position(),       // Point we are at
                 myMoveLookController->get_LookPoint(),      // Point to look towards
                 DirectX::XMFLOAT3( 0, 1, 0 )                   // Up-vector
                 ); 
```

Поздравляем! Вы реализовали в игре основные элементы управления перемещением и обзором для сенсорных экранов и элементов управления касанием для ввода с помощью клавиатуры или мыши!



 

 




