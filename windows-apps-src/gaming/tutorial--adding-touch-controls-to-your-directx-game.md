---
author: mtoepke
title: "Сенсорные элементы управления для игр"
description: "Узнайте, как добавлять основные сенсорные элементы управления в игровое приложение универсальной платформы Windows (UWP) на языке C++ с поддержкой DirectX."
ms.assetid: 9d40e6e4-46a9-97e9-b848-522d61e8e109
translationtype: Human Translation
ms.sourcegitcommit: 6530fa257ea3735453a97eb5d916524e750e62fc
ms.openlocfilehash: 901b83b1c4a2e572e4fe41e1df59910432982687

---

# Сенсорные элементы управления для игр


\[ Обновлено для приложений UWP в Windows10. Статьи о Windows8.x см. в [архиве](http://go.microsoft.com/fwlink/p/?linkid=619132) \]

Узнайте, как добавлять основные сенсорные элементы управления в игру универсальной платформы Windows (UWP) на языке C++ с поддержкой DirectX. Мы покажем, как добавлять сенсорные элементы управления для перемещения камеры, привязанной к фиксированной плоскости, в среде Direct3D, чтобы можно было изменять ракурс съемки перетаскиванием с помощью пальца или пера.

Вы можете добавлять эти элементы управления в игры, если хотите, чтобы игрок использовал перетаскивание для прокрутки камеры или панорамирования в трехмерной среде, такой как карта или игровое поле. Например, в стратегии или головоломке при помощи этих элементов управления можно дать игроку возможность смещать игровое поле влево или вправо, чтобы просматривать игровую среду, размеры которой выходят за пределы экрана.

> **Примечание.**  Наш код работает также с элементами управления для панорамирования при помощи мыши. API среды выполнения Windows абстрагируют события указателя, поэтому могут обрабатывать события как сенсорного ввода, так и указателя мыши.

 

## Цели


-   Создайте простой сенсорный элемент управления перетаскиванием для панорамирования камеры, привязанной к фиксированной плоскости, в игре DirectX.

## Настройка основной инфраструктуры для события сенсорного ввода


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

-   **m\_position** — положение камеры в пространстве сцены. В данном примере координата z имеет фиксированное значение 0. Мы могли бы использовать для отображения этого значения число с плавающей точкой DirectX::XMFLOAT2, но для целей данного образца и ради возможности расширения в будущем мы используем число DirectX::XMFLOAT3. Мы передаем это значение через свойство **get\_Position** непосредственно в приложение, чтобы оно могло обновлять окно просмотра соответствующим образом.
-   **m\_panInUse** — логическое значение, которое указывает, активна ли операция панорамирования, то есть касается ли игрок экрана, перемещая при этом камеру.
-   **m\_panPointerID** — уникальный идентификатор указателя. Мы не используем эту возможность в данном образце, однако рекомендуется сопоставлять класс состояния контроллера с определенным указателем.
-   **m\_panFirstDown** — точка экрана, в которой игрок в первый раз прикоснулся к нему или щелкнул мышью во время панорамирования камеры. Мы используем это значение позже для настройки зоны нечувствительности, чтобы избежать дрожания изображения при касании экрана или при небольшой нестабильности мыши.
-   **m\_panPointerPosition** — точка экрана, в которую игрок только что переместил указатель. Она нужна, чтобы путем сравнения с **m\_panFirstDown** определить, в каком направлении игрок намерен передвинуть камеру.
-   **m\_panCommand** — итоговая вычисленная команда для контроллера камеры: вверх, вниз, влево или вправо. Так как мы работаем с камерой, привязанной к плоскости x-y, можно было бы использовать также значение DirectX::XMFLOAT2.

Следующие три обработчика событий мы используем для обновления данных о состоянии контроллера камеры.

-   **OnPointerPressed** — обработчик событий, который наше приложение вызывает, когда игрок касается сенсорной поверхности и указатель перемещается к координатам области нажатия.
-   **OnPointerMoved** — обработчик событий, который наше приложение вызывает, когда игрок проводит пальцем по сенсорной поверхности. Он обновляется, когда поступают новые координаты пути перетаскивания.
-   **OnPointerReleased** — обработчик событий, который наше приложение вызывает, когда игрок отрывает палец от сенсорной поверхности.

Наконец, мы используем следующие методы и свойства для инициализации, доступа и обновления данных о состоянии контроллера камеры.

-   **Initialize** — обработчик событий, который наше приложение вызывает для инициализации элементов управления и подключения их к объекту [**CoreWindow**](https://msdn.microsoft.com/library/windows/apps/br208225), который описывает окно отображения.
-   **SetPosition** — метод, который приложение вызывает, чтобы задать координаты (x, y и z) ваших элементов управления в пространстве сцены. Обратите внимание, что координата z в данном руководстве везде равна 0.
-   **get\_Position** — свойство, которое наше приложение использует для получения данных о текущем положении камеры в пространстве сцены. Используйте это свойство для сообщения приложению текущего положения камеры.
-   **get\_FixedLookPoint** — свойство, с помощью которого приложение получает доступ к данным точки, на которую в настоящее время направлена камера. В этом примере она стандартно привязана к плоскости x-y.
-   **Update** — метод для считывания состояния контроллера и обновления положения камеры. Вы постоянно вызываете это &lt;нечто&gt; из главного цикла приложения, чтобы обновить данные контроллера камеры и положение камеры в пространстве сцены.

Теперь у вас есть все компоненты, необходимые для реализации сенсорных элементов управления. Вы можете определить, где и когда произошли события сенсорного ввода или указателя мыши, и какое действие при этом было выполнено. Вы можете настроить положение и ориентацию камеры в пространстве сцены и отслеживать изменения. Наконец, вы можете сообщать данные о новом положении камеры по запросу приложения.

Теперь соединим все вместе.

## Создание основных событий сенсорного ввода


Диспетчер событий среды выполнения Windows предоставляет три события, нужные для управления нашим приложением:

-   [**PointerPressed**](https://msdn.microsoft.com/library/windows/apps/br208278)
-   [**PointerMoved**](https://msdn.microsoft.com/library/windows/apps/br208276)
-   [**PointerReleased**](https://msdn.microsoft.com/library/windows/apps/br208279)

Эти события реализуются по типу [**CoreWindow**](https://msdn.microsoft.com/library/windows/apps/br208225). Предполагается, что у вас есть объект **CoreWindow** для работы с этим типом. Подробнее см. в статье [Настройка приложения UWP на языке C++ для отображения представления DirectX](https://msdn.microsoft.com/library/windows/apps/hh465077).

Когда наше приложение запущено, и происходят эти события, обработчики обновляют данные о состоянии контроллера камеры, определенные в закрытых полях.

Сначала заполним обработчики событий указателя касания. В первом обработчике событий, **OnPointerPressed**, мы получаем координаты x-y указателя от [**CoreWindow**](https://msdn.microsoft.com/library/windows/apps/br208225), который управляет нашим дисплеем, когда пользователь касается экрана или щелкает мышью.

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

Мы используем этот обработчик, чтобы сообщить текущему экземпляру **CameraPanController** о том, что контроллер камеры следует считать активным, установив для **m\_panInUse** значение TRUE. Таким образом, по запросу приложения **Update** он будет использовать текущие данные о положении для обновления окна просмотра.

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

Наконец, нам нужно деактивировать реакцию панорамирования камеры, когда пользователь перестает прикасаться к экрану. Мы используем **OnPointerReleased**, который вызывается при запуске [**PointerReleased**](https://msdn.microsoft.com/library/windows/apps/br208279), чтобы установить для**m\_panInUse** значение FALSE, отключить сдвиг камеры и установить для идентификатора указателя значение 0.

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

## Инициализация сенсорных элементов управления и состояния контроллера


Теперь обработаем события и инициализируем все поля основных состояний контроллера камеры.

**Initialize**

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

В качестве параметра **Initialize** принимает ссылку на экземпляр приложения [**CoreWindow**](https://msdn.microsoft.com/library/windows/apps/br208225) и регистрирует обработчики событий, созданные для соответствующих событий **CoreWindow**.

## Получение и настройка положения контроллера камеры


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

**get\_Position** — самое важное открытое свойство: способ, которым наше приложение получает текущее положение контроллера камеры в пространстве сцены, чтобы иметь возможность обновлять окно просмотра соответствующим образом.

**get\_FixedLookPoint** — открытое свойство, которое в данном примере получает данные о точке обзора, перпендикулярной к плоскости x-y. Вы можете изменить этот метод и использовать тригонометрические функции (sin и cos) при вычислении координат x, y и z, если хотите задать наклонные углы обзора для фиксированной камеры.

## Обновление информации о состоянии контроллера камеры


Теперь мы производим вычисления, которые преобразуют данные о координатах указателя, отслеженных в **m\_panPointerPosition**, в новые данные координат для нашего трехмерного пространства сцены. Приложение вызывает этот метод каждый раз при обновлении основного цикла приложения. Здесь мы вычисляем новое расположение точки обзора, которое необходимо передать в приложение и которое используется для обновления матрицы обзора перед проецированием в окно просмотра.

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

## Обновление матрицы просмотра с новым положением камеры


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

> **Примечание.**  
Эта статья адресована разработчикам приложений для Windows10 на базе универсальной платформы Windows (UWP). В случае разработки приложений для Windows 8.x или Windows Phone 8.x см. раздел [архивной документации](http://go.microsoft.com/fwlink/p/?linkid=619132).

 

 

 







<!--HONumber=Aug16_HO3-->


