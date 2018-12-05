---
title: Минимизация задержки ввода в играх UWP, созданным в DirectX
description: Задержка ввода может серьезно повлиять на впечатление от игры, поэтому чем меньше задержка— тем лучше выглядит продукт.
ms.assetid: e18cd1a8-860f-95fb-098d-29bf424de0c0
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp, игры, directx, задержка ввода
ms.localizationpriority: medium
ms.openlocfilehash: 537dd6e9d3f300666a0692b66f422ce00dd68460
ms.sourcegitcommit: c01c29cd97f1cbf050950526e18e15823b6a12a0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/05/2018
ms.locfileid: "8694550"
---
#  <a name="optimize-input-latency-for-universal-windows-platform-uwp-directx-games"></a>Минимизация задержки ввода в играх DirectX универсальной платформы Windows (UWP)



Задержка ввода может серьезно повлиять на впечатление от игры, поэтому чем меньше задержка — тем лучше выглядит продукт. Кроме того, оптимизация событий ввода продлевает время работы батареи. Узнайте, как правильно настроить обработку событий ввода при помощи CoreDispatcher, чтобы игра реагировала на ввод наилучшим образом.

## <a name="input-latency"></a>Задержка ввода


Задержка ввода— это время реакции системы на действия пользователя. Реакция зачастую выражается в изменениях изображения на экране или в разного рода звуковых эффектах.

При каждом событии ввода, было ли оно событием указателя касания, указателя мыши или клавиатуры, создается сообщение для обработчика события. Современные дигитайзеры касаний и периферийные игровые устройства сообщают о событиях ввода с минимальной частотой 100Гц на каждый указатель. Это означает, что приложение может получать сто и более событий в секунду для каждого указателя (или нажатия клавиши). Частота обновлений еще выше, если используются одновременно несколько указателей или устройство ввода повышенной точности (например, игровая мышь). В таких условиях может выстраиваться целая очередь сообщений о событиях.

Важно понимать, на какую задержку ввода должна быть рассчитана игра, чтобы события обрабатывались оптимально для сценария. Универсального решения для всех игр в этом отношении не существует.

## <a name="power-efficiency"></a>Эффективность энергопотребления


В контексте задержки ввода под эффективностью энергопотребления понимается то, насколько интенсивно игра использует GPU. Эффективнее та игра, которая использует меньше ресурсов GPU, продлевая тем самым время работы батареи. То же относится и к центральному процессору.

Если игра обеспечивает отрисовку экрана менее чем за 60кадров в секунду (в настоящее время это максимальная скорость отрисовки для большинства дисплеев) без ущерба для восприятия, целесообразнее снизить частоту отрисовки для экономии энергии. В некоторых играх изображение обновляется только в ответ на ввод данных пользователем, чтобы не отрисовывать одно и то же содержимое снова и снова с частотой 60кадров в секунду.

## <a name="choosing-what-to-optimize-for"></a>Выбор приоритетов


Разрабатывая приложение DirectX, следует ответить на ряд вопросов. Нужно ли отрисовывать изображение с частотой 60кадров в секунду, чтобы обеспечить плавную анимацию, или достаточно реагировать на ввод данных пользователем? Важно ли предельно снизить задержку ввода, или небольшое запаздывание некритично? Рассчитывают ли пользователи, что приложение будет целесообразно расходовать заряд батареи?

Ответы на эти вопросы помогут отнести ваше приложение к одному из следующих сценариев.

1.  Отрисовка по запросу. Игры из этой категории должны обновлять экран только в ответ на определенные типы ввода. Здесь очень высока эффективность энергопотребления, поскольку приложение не выполняет постоянную отрисовку одинаковых кадров, а задержка ввода мала, потому что приложение почти все время проводит в ожидании ввода. Примерами приложений такой категории служат настольные игры и программы для чтения новостей.
2.  Отрисовка по запросу с кратковременной анимацией. Этот сценарий аналогичен первому, но здесь некоторые типы ввода запускают анимацию, которая не зависит от последующего ввода пользователя. Эффективность энергопотребления высока, поскольку игра не выполняет постоянную отрисовку одинаковых кадров, а задержка ввода, пока игра не выполняет анимацию, мала. К этой категории относятся интерактивные детские игры и настольные игры с анимацией каждого хода.
3.  Отрисовка с частотой 60кадров в секунду. В этом сценарии игра постоянно обновляет экран. Эффективность энергопотребления низка, поскольку игра отрисовывает кадры с максимальной скоростью, поддерживаемой экраном. Задержка ввода велика, потому что DirectX блокирует поток, пока идет представление содержимого. Это делается, чтобы поток не отправлял на экран больше кадров, чем тот может показать пользователю. Примером игр этой категории служат шутеры от первого лица, стратегии реального времени и игры с реалистичной физикой.
4.  Отрисовка с частотой 60кадров в секунду с минимальной задержкой ввода. Здесь, как и в сценарии 3, приложение постоянно обновляет экран, и поэтому эффективность энергопотребления будет низкой. Отличие состоит в том, что игра отвечает на ввод в отдельном потоке, и поэтому обработка ввода не блокируется представлением графики на экране. К этой категории относятся многопользовательские сетевые игры, игры с боевыми действиями и игры, где нужно попадать в ритм и вовремя выполнять различные действия, поскольку они поддерживают обработку входных движений в очень ограниченные отрезки времени.

## <a name="implementation"></a>Реализация


Большинство игр DirectX используют так называемые "циклы". Базовый алгоритм строится на выполнении этих шагов до тех пор, пока пользователь не закончит игру или не завершит работу с приложением.

1.  Обработка ввода
2.  Обновление состояния игры
3.  Отрисовка содержимого

Когда содержимое игры DirectX отрисовано и готово к отображению на экране, цикл игры ждет, пока графический процессор не будет готов к приему нового кадра, прежде чем снова переходить к обработке ввода.

Рассмотрим работу циклов для каждого из сценариев, перечисленных выше, на примере игры-пазла. Рассматриваемые в каждом случае критерии принятия решений, преимущества и компромиссные варианты подскажут вам, как оптимизировать приложение, чтобы снизить задержку ввода и повысить эффективность энергопотребления.

## <a name="scenario-1-render-on-demand"></a>Сценарий 1. Отрисовка по запросу


В первой итерации игры изображение на экране обновляется только тогда, когда пользователь перемещает кусочки пазла. Пользователь может перетаскивать кусочки, а может выбирать их, а затем касаться того места, куда их следует положить. Во втором случае кусочки перемещаются в нужное место мгновенно, без какой-либо анимации или эффектов.

В коде заложен однопотоковый цикл внутри метода [**IFrameworkView::Run**](https://msdn.microsoft.com/library/windows/apps/hh700505), использующего **CoreProcessEventsOption::ProcessOneAndAllPending**. При этом все доступные в настоящее время события отправляются в очередь. Если событий нет, цикл ждет, пока они не появятся.

``` syntax
void App::Run()
{
    
    while (!m_windowClosed)
    {
        // Wait for system events or input from the user.
        // ProcessOneAndAllPending will block the thread until events appear and are processed.
        CoreWindow::GetForCurrentThread()->Dispatcher->ProcessEvents(CoreProcessEventsOption::ProcessOneAndAllPending);

        // If any of the events processed resulted in a need to redraw the window contents, then we will re-render the
        // scene and present it to the display.
        if (m_updateWindow || m_state->StateChanged())
        {
            m_main->Render();
            m_deviceResources->Present();

            m_updateWindow = false;
            m_state->Validate();
        }
    }
}
```

## <a name="scenario-2-render-on-demand-with-transient-animations"></a>Сценарий 2. Отрисовка по запросу с периодической анимацией


Во второй итерации игра выглядит несколько иначе: после того как пользователь выбирает кусочек пазла, а затем касается места, перемещение кусочка сопровождается анимацией от начальной точки до конечной.

В коде также заложен однопотоковый цикл, использующий **ProcessOneAndAllPending** для отправки событий ввода в очередь. Разница в том, что в процессе анимации цикл меняется и использует **CoreProcessEventsOption::ProcessAllIfPresent**, чтобы не ждать новых событий ввода. Если событий нет, [**ProcessEvents**](https://msdn.microsoft.com/library/windows/apps/br208215) немедленно возвращается, за счет чего приложение может отображать следующий кадр анимации. По завершении анимации цикл вновь переключается на **ProcessOneAndAllPending**, чтобы ограничить обновление экрана.

``` syntax
void App::Run()
{

    while (!m_windowClosed)
    {
        // 2. Switch to a continuous rendering loop during the animation.
        if (m_state->Animating())
        {
            // Process any system events or input from the user that is currently queued.
            // ProcessAllIfPresent will not block the thread to wait for events. This is the desired behavior when
            // you are trying to present a smooth animation to the user.
            CoreWindow::GetForCurrentThread()->Dispatcher->ProcessEvents(CoreProcessEventsOption::ProcessAllIfPresent);

            m_state->Update();
            m_main->Render();
            m_deviceResources->Present();
        }
        else
        {
            // Wait for system events or input from the user.
            // ProcessOneAndAllPending will block the thread until events appear and are processed.
            CoreWindow::GetForCurrentThread()->Dispatcher->ProcessEvents(CoreProcessEventsOption::ProcessOneAndAllPending);

            // If any of the events processed resulted in a need to redraw the window contents, then we will re-render the
            // scene and present it to the display.
            if (m_updateWindow || m_state->StateChanged())
            {
                m_main->Render();
                m_deviceResources->Present();

                m_updateWindow = false;
                m_state->Validate();
            }
        }
    }
}
```

Для перехода между **ProcessOneAndAllPending** и **ProcessAllIfPresent** приложение должно отслеживать собственное состояние, чтобы знать, воспроизводится ли анимация. В игре-пазле для этого добавляется новый метод, который можно вызывать во время цикла игры в классе GameState. Ветвь анимации внутри цикла обновляет состояние анимации, вызывая новый метод Update GameState.

## <a name="scenario-3-render-60-frames-per-second"></a>Сценарий 3. Отрисовка с частотой 60кадров в секунду


В третьей итерации игра отображает таймер, на котором пользователь видит, как долго он собирает пазл. Поскольку таймер отображает время с точностью до миллисекунды, отрисовка должна выполняться с частотой 60кадров в секунду, чтобы изображение на экране не устаревало.

Как и в сценариях 1 и 2, приложение строится на однопотоковом цикле. Однако поскольку отрисовка происходит постоянно, нет необходимости отслеживать состояние игры (в отличие от первых двух сценариев). Поэтому для обработки событий можно по умолчанию использовать **ProcessAllIfPresent**. Если событий нет, **ProcessEvents** немедленно возвращается и переходит к отрисовке следующего кадра.

``` syntax
void App::Run()
{

    while (!m_windowClosed)
    {
        if (m_windowVisible)
        {
            // 3. Continuously render frames and process system events and input as they appear in the queue.
            // ProcessAllIfPresent will not block the thread to wait for events. This is the desired behavior when
            // trying to present smooth animations to the user.
            CoreWindow::GetForCurrentThread()->Dispatcher->ProcessEvents(CoreProcessEventsOption::ProcessAllIfPresent);

            m_state->Update();
            m_main->Render();
            m_deviceResources->Present();
        }
        else
        {
            // 3. If the window isn't visible, there is no need to continuously render.
            // Process events as they appear until the window becomes visible again.
            CoreWindow::GetForCurrentThread()->Dispatcher->ProcessEvents(CoreProcessEventsOption::ProcessOneAndAllPending);
        }
    }
}
```

Это наиболее простой способ, так как не нужно дополнительно отслеживать состояние игры, чтобы переходить к отрисовке. При этом обеспечивается наиболее быстрая отрисовка и приемлемое время реагирования на ввод в рамках интервалов таймера.

Но у данного способа есть и обратная сторона. Отрисовка с частотой 60 кадров в секунду использует больше электроэнергии, чем отрисовка по запросу. Лучше всего использовать **ProcessAllIfPresent**, пока игра меняет содержимое, отображаемое в каждом следующем кадре. Задержка ввода при этом увеличивается на 16,7мс, поскольку теперь приложение блокирует цикл на время интервала синхронизации дисплея вместо **ProcessEvents**. При этом некоторые события ввода могут игнорироваться, поскольку очередь обрабатывается со скоростью, эквивалентной частоте кадров (60 Гц).

## <a name="scenario-4-render-60-frames-per-second-and-achieve-the-lowest-possible-input-latency"></a>Сценарий 4. Отрисовка с частотой 60кадров в секунду с минимальной задержкой ввода


В некоторых играх есть возможность игнорировать задержку ввода, существующую в сценарии3, или компенсировать ее. Однако если задержка ввода критична для получения ожидаемого впечатления от игры и адекватной обратной связи, при отрисовке с частотой 60кадров в секунду игры должны обрабатывать ввод в отдельном потоке.

В четвертой итерации игра строится на сценарии3, разделяя обработку ввода и отрисовку графики из цикла на отдельные потоки. Благодаря разделению потоков обработка ввода не задерживается выводом графики, хотя код при этом усложняется. В сценарии4 поток ввода вызывает [**ProcessEvents**](https://msdn.microsoft.com/library/windows/apps/br208215) с [**CoreProcessEventsOption::ProcessUntilQuit**](https://msdn.microsoft.com/library/windows/apps/br208217), который ждет новые события и распределяет все доступные. Это длится до тех пор, пока окно не будет закрыто или пока игра не вызовет [**CoreWindow::Close**](https://msdn.microsoft.com/library/windows/apps/br208260).

``` syntax
void App::Run()
{
    // 4. Start a thread dedicated to rendering and dedicate the UI thread to input processing.
    m_main->StartRenderThread();

    // ProcessUntilQuit will block the thread and process events as they appear until the App terminates.
    CoreWindow::GetForCurrentThread()->Dispatcher->ProcessEvents(CoreProcessEventsOption::ProcessUntilQuit);
}

void JigsawPuzzleMain::StartRenderThread()
{
    // If the render thread is already running, then do not start another one.
    if (IsRendering())
    {
        return;
    }

    // Create a task that will be run on a background thread.
    auto workItemHandler = ref new WorkItemHandler([this](IAsyncAction^ action)
    {
        // Notify the swap chain that this app intends to render each frame faster
        // than the display's vertical refresh rate (typically 60 Hz). Apps that cannot
        // deliver frames this quickly should set this to 2.
        m_deviceResources->SetMaximumFrameLatency(1);

        // Calculate the updated frame and render once per vertical blanking interval.
        while (action->Status == AsyncStatus::Started)
        {
            // Execute any work items that have been queued by the input thread.
            ProcessPendingWork();

            // Take a snapshot of the current game state. This allows the renderers to work with a
            // set of values that won't be changed while the input thread continues to process events.
            m_state->SnapState();

            m_sceneRenderer->Render();
            m_deviceResources->Present();
        }

        // Ensure that all pending work items have been processed before terminating the thread.
        ProcessPendingWork();
    });

    // Run the task on a dedicated high priority background thread.
    m_renderLoopWorker = ThreadPool::RunAsync(workItemHandler, WorkItemPriority::High, WorkItemOptions::TimeSliced);
}
```

Шаблон **DirectX 11 и XAML (универсальное приложение Windows)** в Microsoft Visual Studio2015 разделяет цикл игры на несколько потоков по аналогичному. С помощью объекта [**Windows::UI::Core::CoreIndependentInputSource**](https://msdn.microsoft.com/library/windows/apps/dn298460) он запускает поток обработки ввода, а также создает поток отрисовки независимо от потока пользовательского интерфейса XAML. Подробности об этих шаблонах см. в статье [Создание проекта игры универсальной платформы Windows и DirectX на основе шаблона](user-interface.md).

## <a name="additional-ways-to-reduce-input-latency"></a>Дополнительные способы сокращения задержки ввода


### <a name="use-waitable-swap-chains"></a>Цепочки буферов с поддержкой ожидания

Игры DirectX реагируют на действия пользователя, обновляя изображение на экране. На дисплеях с частотой 60Гц изображение обновляется каждые 16,7мс (1секунда/60кадров). На рисунке1 представлены примерный жизненный цикл и реакция на событие ввода относительно сигнала обновления 16,7мс (VBlank) для приложений с отрисовкой 60кадров в секунду.

Рисунок 1

![Рисунок 1. Задержка ввода в DirectX ](images/input-latency1.png)

В Windows8.1 DXGI представили флаг **DXGI\_SWAP\_CHAIN\_FLAG\_FRAME\_LATENCY\_WAITABLE\_OBJECT** для цепочки буферов, что позволяет приложениям легко сократить задержку без необходимости использовать эвристику для очереди Present пустым. Цепочки буферов с таким флагом называются цепочками буферов с поддержкой ожидания. На рисунке2 представлены примерный жизненный цикл и реакция на событие ввода для приложений, использующих цепочки буферов с поддержкой ожидания.

Рисунок 2

![Рисунок 2. Задержка ввода в DirectX: цепочки буферов с поддержкой ожидания](images/input-latency2.png)

Из этих схем следует, что игры потенциально могут снижать задержку ввода на два полных кадра, если они способны отрисовывать и выводить каждый кадр за те 16,7мс, за которые обновляется изображение на дисплее. Пример игры-пазла использует цепочки буферов с поддержкой ожидания и контролирует заполнение очереди Present с помощью вызова ` m_deviceResources->SetMaximumFrameLatency(1);`

 

 




