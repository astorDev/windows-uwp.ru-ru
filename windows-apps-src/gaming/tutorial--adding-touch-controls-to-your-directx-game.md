---
title: Сенсорные элементы управления для игр
description: Узнайте, как добавлять основные сенсорные элементы управления в игровое приложение универсальной платформы Windows (UWP) на языке C++ с поддержкой DirectX.
ms.assetid: 9d40e6e4-46a9-97e9-b848-522d61e8e109
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp, игры, сенсорный ввод, элементы управления, directx, ввод
ms.localizationpriority: medium
ms.openlocfilehash: b1f683f2d357057e33f3daa613e1b027a83776af
ms.sourcegitcommit: ac7f3422f8d83618f9b6b5615a37f8e5c115b3c4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/29/2019
ms.locfileid: "66367759"
---
# <a name="touch-controls-for-games"></a>Сенсорные элементы управления для игр



Узнайте, как добавлять основные сенсорные элементы управления в игровое приложение универсальной платформы Windows (UWP) на языке C++ с поддержкой DirectX. Мы покажем, как добавлять сенсорные элементы управления для перемещения камеры, привязанной к фиксированной плоскости, в среде Direct3D, чтобы можно было изменять ракурс съемки перетаскиванием с помощью пальца или пера.

Вы можете добавлять эти элементы управления в игры, если хотите, чтобы игрок использовал перетаскивание для прокрутки камеры или панорамирования в трехмерной среде, такой как карта или игровое поле. Например, в стратегии или головоломке при помощи этих элементов управления можно дать игроку возможность смещать игровое поле влево или вправо, чтобы просматривать игровую среду, размеры которой выходят за пределы экрана.

> **Примечание**  наш код также работает с панорамирования элементов управления на основе указателя мыши. API среды выполнения Windows абстрагируют события указателя, поэтому могут обрабатывать события как сенсорного ввода, так и указателя мыши.

 

## <a name="objectives"></a>Цели


-   Создайте простой сенсорный элемент управления перетаскиванием для панорамирования камеры, привязанной к фиксированной плоскости, в игре DirectX.

## <a name="set-up-the-basic-touch-event-infrastructure"></a>Настройка основной инфраструктуры для события сенсорного ввода


Сначала мы определяем основной тип контроллера, в данном случае **CameraPanController**. Здесь мы определяем контроллер как абстрактную идею, набор возможных поведений пользователя.

Класс **CameraPanController** — это регулярно обновляемый набор сведений о состоянии контроллера камеры, который предоставляет нашему приложению способ получать эти сведения в цикле обновления.

```cpp
using namespace Windows::UI::Core;
using namespace Windows::System;
using namespace Windows::Foundation;
using namespace Windows::Devices::Input;
#include <directxmath.h>

// Methods to get input from the UI pointers
ref class CameraPanController
{
}
```

Теперь создадим заголовок, который будет определять состояние контроллера камеры и основные методы и обработчики событий, реализующих взаимодействия контроллера камеры.

```cpp
ref class CameraPanController
{
private:
    // Properties of the controller object
    DirectX::XMFLOAT3 m_position;               // the position of the camera

    // Properties of the camera pan control
    bool m_panInUse;                
    uint32 m_panPointerID;          
    DirectX::XMFLOAT2 m_panFirstDown;           
    DirectX::XMFLOAT2 m_panPointerPosition;   
    DirectX::XMFLOAT3 m_panCommand;         
    
internal:
    // Accessor to set the position of the controller
    void SetPosition( _In_ DirectX::XMFLOAT3 pos );

       // Accessor to set the fixed "look point" of the controller
       DirectX::XMFLOAT3 get_FixedLookPoint();

    // Returns the position of the controller object
    DirectX::XMFLOAT3 get_Position();

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

    // Set up the Controls supported by this controller
    void Initialize( _In_ Windows::UI::Core::CoreWindow^ window );

    void Update( Windows::UI::Core::CoreWindow ^window );

};  // Class CameraPanController
```

Закрытые поля содержат данные о текущем состоянии контроллера камеры. Рассмотрим их.

-   **m\_позиции** — это положение камеры в пространстве сцены. В данном примере координата z имеет фиксированное значение 0. Мы могли бы использовать для отображения этого значения число с плавающей точкой DirectX::XMFLOAT2, но для целей данного образца и ради возможности расширения в будущем мы используем число DirectX::XMFLOAT3. Мы передаем значение через **получить\_позиции** свойства самого, соответствующим образом обновить окно просмотра приложения.
-   **m\_panInUse** является логическое значение, указывающее, что операцию pan активно; или, точнее, проигрыватель прикосновения к экрану и перемещение камеры.
-   **m\_panPointerID** представляет собой уникальный идентификатор для указателя. Мы не используем эту возможность в данном образце, однако рекомендуется сопоставлять класс состояния контроллера с определенным указателем.
-   **m\_panFirstDown** представляет собой точку на экране, где игрок сначала затронутых экрана и щелчке мыши во время выполнения действия сдвиг камеры. Мы используем это значение позже для настройки зоны нечувствительности, чтобы избежать дрожания изображения при касании экрана или при небольшой нестабильности мыши.
-   **m\_panPointerPosition** представляет собой точку на экране, где игрок в настоящее время был перемещен указатель. Мы используем чтобы определить направление проигрыватель нужно было перенести путем проверки его относительно **m\_panFirstDown**.
-   **m\_panCommand** — это последняя команда вычисляемых для контроллера камеры: вверх, вниз, влево или вправо. Так как мы работаем с камерой, привязанной к плоскости x-y, можно было бы использовать также значение DirectX::XMFLOAT2.

Следующие три обработчика событий мы используем для обновления данных о состоянии контроллера камеры.

-   **OnPointerPressed** — обработчик событий, который наше приложение вызывает, когда игрок касается сенсорной поверхности и указатель перемещается к координатам области нажатия.
-   **OnPointerMoved** — обработчик событий, который наше приложение вызывает, когда игрок проводит пальцем по сенсорной поверхности. Он обновляется, когда поступают новые координаты пути перетаскивания.
-   **OnPointerReleased** — обработчик событий, который наше приложение вызывает, когда игрок отрывает палец от сенсорной поверхности.

Наконец, мы используем следующие методы и свойства для инициализации, доступа и обновления данных о состоянии контроллера камеры.

-   **Initialize** — обработчик событий, который наше приложение вызывает для инициализации элементов управления и подключения их к объекту [**CoreWindow**](https://docs.microsoft.com/uwp/api/Windows.UI.Core.CoreWindow), который описывает окно отображения.
-   **SetPosition** — метод, который приложение вызывает, чтобы задать координаты (x, y и z) ваших элементов управления в пространстве сцены. Обратите внимание, что координата z в данном руководстве везде равна 0.
-   **получить\_позиции** является свойством, которое обращается к наше приложение, чтобы получить текущее положение камеры в пространстве сцены. Используйте это свойство для сообщения приложению текущего положения камеры.
-   **получить\_FixedLookPoint** — это свойство, которое обращается к нашему приложению для получения текущей точки, к которому направлена камера контроллера. В этом примере она стандартно привязана к плоскости x-y.
-   **Update** — метод для считывания состояния контроллера и обновления положения камеры. Вы постоянно вызываете это &lt;нечто&gt; из главного цикла приложения, чтобы обновить данные контроллера камеры и положение камеры в пространстве сцены.

Теперь у вас есть все компоненты, необходимые для реализации сенсорных элементов управления. Вы можете определить, где и когда произошли события сенсорного ввода или указателя мыши, и какое действие при этом было выполнено. Вы можете настроить положение и ориентацию камеры в пространстве сцены и отслеживать изменения. Наконец, вы можете сообщать данные о новом положении камеры по запросу приложения.

Теперь соединим все вместе.

## <a name="create-the-basic-touch-events"></a>Создание основных событий сенсорного ввода


Диспетчер событий среды выполнения Windows предоставляет три события, нужные для управления нашим приложением:

-   [**PointerPressed**](https://docs.microsoft.com/uwp/api/windows.ui.core.corewindow.pointerpressed)
-   [**PointerMoved**](https://docs.microsoft.com/uwp/api/windows.ui.core.corewindow.pointermoved)
-   [**PointerReleased**](https://docs.microsoft.com/uwp/api/windows.ui.core.corewindow.pointerreleased)

Эти события реализуются по типу [**CoreWindow**](https://docs.microsoft.com/uwp/api/Windows.UI.Core.CoreWindow). Предполагается, что у вас есть объект **CoreWindow** для работы с этим типом. Подробнее см. в статье [Настройка приложения UWP на языке C++ для отображения представления DirectX](https://docs.microsoft.com/previous-versions/windows/apps/hh465077(v=win.10)).

Когда наше приложение запущено, и происходят эти события, обработчики обновляют данные о состоянии контроллера камеры, определенные в закрытых полях.

Сначала заполним обработчики событий указателя касания. В первом обработчике событий, **OnPointerPressed**, мы получаем координаты x-y указателя от [**CoreWindow**](https://docs.microsoft.com/uwp/api/Windows.UI.Core.CoreWindow), который управляет нашим дисплеем, когда пользователь касается экрана или щелкает мышью.

**OnPointerPressed**

```cpp
void CameraPanController::OnPointerPressed(
                                           _In_ CoreWindow^ sender,
                                           _In_ PointerEventArgs^ args)
{
    // Get the current pointer position.
    uint32 pointerID = args->CurrentPoint->PointerId;
    DirectX::XMFLOAT2 position = DirectX::XMFLOAT2( args->CurrentPoint->Position.X, args->CurrentPoint->Position.Y );

    auto device = args->CurrentPoint->PointerDevice;
    auto deviceType = device->PointerDeviceType;
    

       if ( !m_panInUse )   // If no pointer is in this control yet.
    {
       m_panFirstDown = position;                   // Save the location of the initial contact.
       m_panPointerPosition = position;
       m_panPointerID = pointerID;              // Store the id of the pointer using this control.
       m_panInUse = TRUE;
    }
    
}
```

Мы используем этот обработчик для текущего **CameraPanController** экземпляр знать контролере камеры должны рассматриваться как активные, задав **m\_panInUse** значение true. Таким образом, по запросу приложения **Update** он будет использовать текущие данные о положении для обновления окна просмотра.

Теперь, установив основные значения для перемещения камеры при прикосновении пользователя к экрану или нажатии кнопок в окне отображения, мы должны определить, какие действия выполнять, когда пользователь проводит пальцем по экрану или двигает мышью при нажатой кнопке.

Обработчик событий **OnPointerMoved** запускается по мере перемещения указателя на каждом делении, через которое игрок перетаскивает его по экрану. Нам нужно, чтобы приложение знало текущее положение указателя, и мы добиваемся этого следующим образом.

**OnPointerMoved**

```cpp
void CameraPanController::OnPointerMoved(
                                        _In_ CoreWindow ^sender,
                                        _In_ PointerEventArgs ^args)
{
    uint32 pointerID = args->CurrentPoint->PointerId;
    DirectX::XMFLOAT2 position = DirectX::XMFLOAT2( args->CurrentPoint->Position.X, args->CurrentPoint->Position.Y );

    m_panPointerPosition = position;
}
```

Наконец, нам нужно деактивировать реакцию панорамирования камеры, когда пользователь перестает прикасаться к экрану. Мы используем **OnPointerReleased**, который вызывается, когда [ **PointerReleased** ](https://docs.microsoft.com/uwp/api/windows.ui.core.corewindow.pointerreleased) инициируется, чтобы задать **m\_panInUse** значение false и отключить перемещение сдвиг камеры и указатель на идентификатор присвоено значение 0.

**OnPointerReleased**

```cpp
void CameraPanController::OnPointerReleased(
                                             _In_ CoreWindow ^sender,
                                             _In_ PointerEventArgs ^args)
{
    uint32 pointerID = args->CurrentPoint->PointerId;
    DirectX::XMFLOAT2 position = DirectX::XMFLOAT2( args->CurrentPoint->Position.X, args->CurrentPoint->Position.Y );

    m_panInUse = FALSE;
    m_panPointerID = 0;
}
```

## <a name="initialize-the-touch-controls-and-the-controller-state"></a>Инициализация сенсорных элементов управления и состояния контроллера


Теперь обработаем события и инициализируем все поля основных состояний контроллера камеры.

**Инициализация**

```cpp
void CameraPanController::Initialize( _In_ CoreWindow^ window )
{

    // Start recieving touch/mouse events.
    window->PointerPressed += 
    ref new TypedEventHandler<CoreWindow^, PointerEventArgs^>(this, &CameraPanController::OnPointerPressed);

    window->PointerMoved += 
    ref new TypedEventHandler<CoreWindow^, PointerEventArgs^>(this, &CameraPanController::OnPointerMoved);

    window->PointerReleased += 
    ref new TypedEventHandler<CoreWindow^, PointerEventArgs^>(this, &CameraPanController::OnPointerReleased);


    // Initialize the state of the controller.
    m_panInUse = FALSE;             
    m_panPointerID = 0;

    //  Initialize this as it is reset on every frame.
    m_panCommand = DirectX::XMFLOAT3( 0.0f, 0.0f, 0.0f );

}
```

В качестве параметра **Initialize** принимает ссылку на экземпляр приложения [**CoreWindow**](https://docs.microsoft.com/uwp/api/Windows.UI.Core.CoreWindow) и регистрирует обработчики событий, созданные для соответствующих событий **CoreWindow**.

## <a name="getting-and-setting-the-position-of-the-camera-controller"></a>Получение и настройка положения контроллера камеры


Определим некоторые методы получения и настройки положения контроллера камеры в пространстве сцены.

```cpp
void CameraPanController::SetPosition( _In_ DirectX::XMFLOAT3 pos )
{
    m_position = pos;
}

// Returns the position of the controller object
DirectX::XMFLOAT3 CameraPanController::get_Position()
{
    return m_position;
}

DirectX::XMFLOAT3 CameraPanController::get_FixedLookPoint()
{
    // For this sample, we don't need to use the trig functions because our
    // look point is fixed. 
    DirectX::XMFLOAT3 result= m_position;
    result.z += 1.0f;
    return result;    

}
```

**SetPosition** — открытый метод, который мы можем вызвать из приложения, если требуется установить контроллер камеры в определенную точку.

**получить\_позиции** является наиболее важным общее свойство: это способ, наше приложение получает текущую позицию контроллера камеры в пространстве сцены, чтобы соответствующим образом обновить окно просмотра.

**получить\_FixedLookPoint** — это открытое свойство, которое в этом примере Получает точку взгляд, является нормальным плоскости x-y. Вы можете изменить этот метод и использовать тригонометрические функции (sin и cos) при вычислении координат x, y и z, если хотите задать наклонные углы обзора для фиксированной камеры.

## <a name="updating-the-camera-controller-state-information"></a>Обновление информации о состоянии контроллера камеры


Теперь мы выполнять вычисления, которые преобразуют данные координат указателя, отслеживаются в **m\_panPointerPosition** в новых координат сведений о соответствующих нашей пространства трехмерной сцены. Приложение вызывает этот метод каждый раз при обновлении основного цикла приложения. Здесь мы вычисляем новое расположение точки обзора, которое необходимо передать в приложение и которое используется для обновления матрицы обзора перед проецированием в окно просмотра.

```cpp

void CameraPanController::Update( CoreWindow ^window )
{
    if ( m_panInUse )
    {
        pointerDelta.x = m_panPointerPosition.x - m_panFirstDown.x;
        pointerDelta.y = m_panPointerPosition.y - m_panFirstDown.y;

        if ( pointerDelta.x > 16.0f )        // Leave 32 pixel-wide dead spot for being still.
            m_panCommand.x += 1.0f;
        else
            if ( pointerDelta.x < -16.0f )
                m_panCommand.x += -1.0f;

        if ( pointerDelta.y > 16.0f )        
            m_panCommand.y += 1.0f;
        else
            if (pointerDelta.y < -16.0f )
                m_panCommand.y += -1.0f;
    }

       DirectX::XMFLOAT3 command = m_panCommand;
   
    // Our velocity is based on the command.
    DirectX::XMFLOAT3 Velocity;
    Velocity.x =  command.x;
    Velocity.y =  command.y;
    Velocity.z =  0.0f;

    // Integrate
    m_position.x = m_position.x + Velocity.x;
    m_position.y = m_position.y + Velocity.y;
    m_position.z = m_position.z + Velocity.z;

    // Clear the movement input accumulator for use during the next frame.
    m_panCommand = DirectX::XMFLOAT3( 0.0f, 0.0f, 0.0f );

}
```

Поскольку мы не хотим, чтобы дрожание указателя сенсорного ввода или мыши привело к неравномерному перемещению камеры, зададим зону нечувствительности вокруг указателя диаметром 32 пикселя. Также у нас есть значение скорости, которая в данном случае относится к скорости прохода указателем зоны нечувствительности в пикселях как 1:1. Вы можете настроить эту реакцию на замедление или ускорение скорости перемещения.

## <a name="updating-the-view-matrix-with-the-new-camera-position"></a>Обновление матрицы просмотра с новым положением камеры


Теперь можно получать координаты пространства сцены, на которое направлена камера, и которые обновляются так, как вы укажете приложению (например, каждые 60 секунд в основном цикле приложения). Следующий псевдокод предлагает реакции вызова, которые вы можете реализовать.

```cpp
 myCameraPanController->Update( m_window ); 

 // Update the view matrix based on the camera position.
 myCamera->MyMethodToComputeViewMatrix(
        myController->get_Position(),        // The position in the 3D scene space.
        myController->get_FixedLookPoint(),      // The point in the space we are looking at.
        DirectX::XMFLOAT3( 0, 1, 0 )                    // The axis that is "up" in our space.
        );  
```

Поздравляем! Вы реализовали простой набор сенсорных элементов управления для панорамирования камеры в вашей игре.


 

 

 




