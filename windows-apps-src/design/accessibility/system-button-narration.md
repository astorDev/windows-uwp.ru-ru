---
Description: ''
title: Средства чтения с экрана и события кнопки оборудования
label: Screen readers and hardware button events
template: detail.hbs
ms.date: 02/20/2020
ms.topic: article
keywords: Windows 10, UWP, Специальные возможности, Экранный диктор, средство чтения с экрана
ms.localizationpriority: medium
ms.openlocfilehash: 41c6ed531f21a922b407ff3ba5aae93afb8d917e
ms.sourcegitcommit: 87fd0ec1e706a460832b67f936a3014f0877a88c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/12/2020
ms.locfileid: "83234937"
---
# <a name="screen-readers-and-hardware-system-buttons"></a>Средства чтения с экрана и аппаратные системные кнопки

Читатели экрана, такие как [экранный диктор](https://support.microsoft.com/help/22798/windows-10-complete-guide-to-narrator), должны иметь возможность распознать и обрабатывать события кнопки аппаратной системы и передавать их состояние пользователям. В некоторых случаях средству чтения с экрана может потребоваться обрабатывать эти события аппаратной кнопки в монопольном режиме и не добавим их к другим обработчикам.

Начиная с Windows 10 версии 2004, приложения UWP могут ожидать событий кнопки аппаратной системы **fn** и работать с ними так же, как и с другими аппаратными кнопками. Ранее эта системная кнопка действовала только как модификатор, определяющий, как другие аппаратные кнопки сообщали о событиях и состоянии.

> [!NOTE]
> Поддержка кнопок FN зависит от производителя и может включать в себя такие функции, как возможность переключения/блокировки (и сочетания клавиш для нажатия и удерживания), а также соответствующий индикатор блокировки (который может не быть полезен для пользователей с ослабленным зрением или с нарушениями зрения).

События кнопок FN доступны через новый [класс систембуттоневентконтроллер](/uwp/api/windows.ui.input.systembuttoneventcontroller) в пространстве имен [Windows. UI. Input](/uwp/api/windows.ui.input) . Объект Систембуттоневентконтроллер поддерживает следующие события:

- [системфунктионбуттонпрессед](/uwp/api/windows.ui.input.systembuttoneventcontroller.systemfunctionbuttonpressed)
- [системфунктионбуттонрелеасед](/uwp/api/windows.ui.input.systembuttoneventcontroller.systemfunctionbuttonreleased)
- [системфунктионлоккчанжед](/uwp/api/windows.ui.input.systembuttoneventcontroller.systemfunctionlockchanged)
- [системфунктионлоккиндикаторчанжед](/uwp/api/windows.ui.input.systembuttoneventcontroller.systemfunctionlockindicatorchanged)

> [!Important]
> Систембуттоневентконтроллер не может получить эти события, если они уже обработаны обработчиком более высокого приоритета.

## <a name="examples"></a>Примеры

В следующих примерах мы покажем, как создать [систембуттоневентконтроллер](/uwp/api/windows.ui.input.systembuttoneventcontroller) на основе объекта DispatcherQueue и справиться с четырьмя событиями, поддерживаемыми этим объектом.

Обычно при нажатии кнопки Fn часто срабатывает более одного из поддерживаемых событий. Например, нажатие кнопки Fn на клавиатуре Surface активирует Системфунктионбуттонпрессед, Системфунктионлоккчанжед и Системфунктионлоккиндикаторчанжед одновременно.

1. В этом первом фрагменте кода мы просто включаем необходимые пространства имен и указываем некоторые глобальные объекты, включая объекты [DispatcherQueue](/uwp/api/windows.system.dispatcherqueue) и [диспатчеркуеуеконтроллер](/uwp/api/windows.system.dispatcherqueuecontroller) для управления потоком [систембуттоневентконтроллер](/uwp/api/windows.ui.input.systembuttoneventcontroller) .

   Затем мы указываем [токены событий](/uwp/cpp-ref-for-winrt/event-token) , возвращаемые при регистрации делегатов обработки событий [систембуттоневентконтроллер](/uwp/api/windows.ui.input.systembuttoneventcontroller) .

    ```cppwinrt
    namespace winrt
    {
        using namespace Windows::System;
        using namespace Windows::UI::Input;
    }

    ...

    // Declare related members
    winrt::DispatcherQueueController _queueController;
    winrt::DispatcherQueue _queue;
    winrt::SystemButtonEventController _controller;
    winrt::event_token _fnKeyDownToken;
    winrt::event_token _fnKeyUpToken;
    winrt::event_token _fnLockToken;
    ```

2. Кроме того, для события [системфунктионлоккиндикаторчанжед](/uwp/api/windows.ui.input.systembuttoneventcontroller.systemfunctionlockindicatorchanged) указывается маркер события, а также логическое значение, указывающее, находится ли приложение в режиме обучения (где пользователь просто пытается исследовать клавиатуру без выполнения каких-либо функций).

    ```cppwinrt
    winrt::event_token _fnLockIndicatorToken;
    bool _isLearningMode = false;
    ```

3. Этот третий фрагмент включает соответствующие делегаты обработчиков событий для каждого события, поддерживаемого объектом [систембуттоневентконтроллер](/uwp/api/windows.ui.input.systembuttoneventcontroller) .

   Каждый обработчик событий объявляет о произошедшем событии. Кроме того, обработчик Функтионлоккиндикаторчанжед также определяет, находится ли приложение в режиме обучения ( `_isLearningMode` = true), которое предотвращает восходящую передачу события в другие обработчики и позволяет пользователю исследовать возможности клавиатуры без фактического выполнения этого действия.

    ```cppwinrt
    void SetupSystemButtonEventController()
    {
        // Create dispatcher queue controller and dispatcher queue
        _queueController = winrt::DispatcherQueueController::CreateOnDedicatedThread();
        _queue = _queueController.DispatcherQueue();

        // Create controller based on new created dispatcher queue
        _controller = winrt::SystemButtonEventController::CreateForDispatcherQueue(_queue);

        // Add Event Handler for each different event
        _fnKeyDownToken = _controller->FunctionButtonPressed(
            [](const winrt::SystemButtonEventController& /*sender*/, const winrt:: FunctionButtonEventArgs& args)
            {
                // Mock function to read the sentence "Fn button is pressed"
                PronounceFunctionButtonPressedMock();
                // Set Handled as true means this event is consumed by this controller
                // no more targets will receive this event
                args.Handled(true);
            });

            _fnKeyUpToken = _controller->FunctionButtonReleased(
                [](const winrt::SystemButtonEventController& /*sender*/, const winrt:: FunctionButtonEventArgs& args)
                {
                    // Mock function to read the sentence "Fn button is up"
                    PronounceFunctionButtonReleasedMock();
                    // Set Handled as true means this event is consumed by this controller
                    // no more targets will receive this event
                    args.Handled(true);
                });

        _fnLockToken = _controller->FunctionLockChanged(
            [](const winrt::SystemButtonEventController& /*sender*/, const winrt:: FunctionLockChangedEventArgs& args)
            {
                // Mock function to read the sentence "Fn shift is locked/unlocked"
                PronounceFunctionLockMock(args.IsLocked());
                // Set Handled as true means this event is consumed by this controller
                // no more targets will receive this event
                args.Handled(true);
            });

        _fnLockIndicatorToken = _controller->FunctionLockIndicatorChanged(
            [](const winrt::SystemButtonEventController& /*sender*/, const winrt:: FunctionLockIndicatorChangedEventArgs& args)
            {
                // Mock function to read the sentence "Fn lock indicator is on/off"
                PronounceFunctionLockIndicatorMock(args.IsIndicatorOn());
                // In learning mode, the user is exploring the keyboard. They expect the program
                // to announce what the key they just pressed WOULD HAVE DONE, without actually
                // doing it. Therefore, handle the event when in learning mode so the key is ignored
                // by the system.
                args.Handled(_isLearningMode);
            });
    }
    ```

## <a name="see-also"></a>См. также раздел

[Класс Систембуттоневентконтроллер](/uwp/api/windows.ui.input.systembuttoneventcontroller)
