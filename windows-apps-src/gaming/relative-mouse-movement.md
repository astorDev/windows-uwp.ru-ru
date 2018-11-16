---
author: scottmill
title: Относительное движение мыши
description: Используйте относительные элементы управления для мыши, которые не применяют системный курсор и не возвращают абсолютные экранные координаты, чтобы отслеживать изменение значений пикселей при перемещении мыши в играх.
ms.author: scotmi
ms.date: 02/08/2017
ms.topic: article
keywords: Windows 10, uwp, игры, мыши, ввод
ms.assetid: 08c35e05-2822-4a01-85b8-44edb9b6898f
ms.localizationpriority: medium
ms.openlocfilehash: adf3b629095f633521b99133ce1961e5c8408ef5
ms.sourcegitcommit: e2fca6c79f31e521ba76f7ecf343cf8f278e6a15
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/16/2018
ms.locfileid: "6992332"
---
# <a name="relative-mouse-movement-and-corewindow"></a>Относительное перемещение мыши и CoreWindow

Распространенным средством управления в играх служит мышь, привычная пользователям и неотъемлемая в играх таких жанров, как "стрелялки" от первого и от третьего лица, стратегические игры в реальном времени и другие. Здесь мы рассмотрим реализацию относительных элементов управления для мыши, которые не используют системный курсор и не возвращают абсолютные экранные координаты, а отслеживают изменение значений пикселей при перемещении мыши.

Во многих приложениях, например в играх, мышь используется в качестве основного устройства ввода. Скажем, ввод с помощью мыши может применяться в трехмерной модели, чтобы задавать направление трехмерному объекту при имитации виртуального трекбола, или в игре с помощью мыши может изменяться направление камеры (через элементы управления мышью и обзором). 

При таких сценариях приложению нужны относительные данные ввода с мыши. Относительные значения перемещения мыши означают дальность перемещения мыши относительно предыдущего кадра, а не абсолютные координаты в пределах окна или экрана. Кроме того, приложения зачастую скрывают курсор мыши, так как положение курсора относительно экранных координат несущественно при управлении трехмерным объектом или пространством. 

Если пользователь выполняет действие, которое переводит приложение в режим относительного управления трехмерным объектом или пространством, приложение должно: 
- игнорировать обработку сигналов мыши по умолчанию;
- включить относительную обработку сигналов мыши;
- скрыть курсор мыши, установив пустой указатель (nullptr). 

Если пользователь выполняет действие, которое выводит приложение из режима относительного управления трехмерным объектом или пространством, приложение должно: 
- включить обработку сигналов мыши по умолчанию;
- выключить относительную обработку сигналов мыши; 
- задать для курсора мыши значение, отличное от null (отчего курсор становится виден).

> **Примечание.**  
При такой схеме абсолютное положение курсора мыши сохраняется при переходе в относительный режим, когда курсор скрыт. Курсор вновь появится в той же координате экрана, что и до перехода в режим относительной обработки сигналов мыши.

 

## <a name="handling-relative-mouse-movement"></a>Обработка относительных значений перемещения мыши


Чтобы получить доступ к относительным значениям перемещения мыши, выполните регистрацию события [MouseDevice::MouseMoved](https://msdn.microsoft.com/library/windows/apps/xaml/windows.devices.input.mousedevice.mousemoved.aspx), как показано ниже.


```cpp


// register handler for relative mouse movement events
Windows::Devices::Input::MouseDevice::GetForCurrentView()->MouseMoved +=
        ref new TypedEventHandler<MouseDevice^, MouseEventArgs^>(this, &MoveLookController::OnMouseMoved);


```

```cpp


void MoveLookController::OnMouseMoved(
    _In_ Windows::Devices::Input::MouseDevice^ mouseDevice,
    _In_ Windows::Devices::Input::MouseEventArgs^ args
    )
{
    float2 pointerDelta;
    pointerDelta.x = static_cast<float>(args->MouseDelta.X);
    pointerDelta.y = static_cast<float>(args->MouseDelta.Y);

    float2 rotationDelta;
    rotationDelta = pointerDelta * ROTATION_GAIN;   // scale for control sensitivity

    // update our orientation based on the command
    m_pitch -= rotationDelta.y;                     // mouse y increases down, but pitch increases up
    m_yaw   -= rotationDelta.x;                     // yaw defined as CCW around y-axis

    // limit pitch to straight up or straight down
    float limit = (float)(M_PI/2) - 0.01f;
    m_pitch = (float) __max( -limit, m_pitch );
    m_pitch = (float) __min( +limit, m_pitch );

    // keep longitude in useful range by wrapping
    if ( m_yaw >  M_PI )
        m_yaw -= (float)M_PI*2;
    else if ( m_yaw < -M_PI )
        m_yaw += (float)M_PI*2;
}

```

Обработчик событий **OnMouseMoved** в данном примере кода выполняет преобразование для просмотра в зависимости от перемещений мыши. Положение указателя мыши передается в обработчик в качестве объекта [MouseEventArgs](https://msdn.microsoft.com/library/windows/apps/xaml/windows.devices.input.mouseeventargs.aspx). 

Пропускайте обработку данных об абсолютном положении мыши от события [CoreWindow::PointerMoved](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.core.corewindow.pointermoved.aspx), если ваше приложение переходит в режим обработки относительных значений перемещения мыши. При этом пропускайте эти данные, только если событие **CoreWindow::PointerMoved** наступило в результате ввода с помощью мыши (а не сенсорного ввода). Курсор скрыт, потому что для свойства [CoreWindow::PointerCursor](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.core.corewindow.pointercursor.aspx) установлено значение **nullptr**. 

## <a name="returning-to-absolute-mouse-movement"></a>Возвращение в режим обработки абсолютных значений перемещения мыши

Когда приложение выходит из режима относительного управления трехмерным объектом или пространством и более не использует относительные значения перемещения мыши (например, при возвращении в экран меню), вернитесь к обычной обработке абсолютных значений перемещения мыши. Теперь нужно прекратить чтение относительных данных о положении мыши, перезапустить обработку обычных событий мыши (и указателя) и установить для [CoreWindow::PointerCursor](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.core.corewindow.pointercursor.aspx) значение, отличное от null. 

> **Примечание.**  
Когда приложение находится в режиме относительного управления трехмерным объектом или пространством (обрабатывает относительные значения перемещения мыши при отключенном курсоре), с помощью мыши невозможно вызывать элементы пользовательского интерфейса, расположенные по краям, например чудо-кнопки, кнопку "Назад" или панель приложения. Поэтому важно предусмотреть способ выхода из данного режима, например с помощью клавиши **ESC**.

## <a name="related-topics"></a>Связанные статьи

* [Элементы управления перемещением и обзором для игр](tutorial--adding-move-look-controls-to-your-directx-game.md) 
* [Сенсорные элементы управления для игр](tutorial--adding-touch-controls-to-your-directx-game.md)