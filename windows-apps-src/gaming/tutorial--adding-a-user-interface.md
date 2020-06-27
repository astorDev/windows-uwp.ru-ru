---
title: Добавление пользовательского интерфейса
description: Узнайте, как добавить наложение 2D-пользовательского интерфейса в игровое устройство DirectX UWP.
ms.assetid: fa40173e-6cde-b71b-e307-db90f0388485
ms.date: 10/24/2017
ms.topic: article
keywords: windows 10, uwp, игры, пользовательский интерфейс, directx
ms.localizationpriority: medium
ms.openlocfilehash: b6b59bd4f42d31e1f29cc1af298199b42cce6781
ms.sourcegitcommit: 20969781aca50738792631f4b68326f9171a3980
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/26/2020
ms.locfileid: "85409543"
---
# <a name="add-a-user-interface"></a>Добавление пользовательского интерфейса

> [!NOTE]
> Эта статья является частью серии руководств по [созданию простой универсальная платформа Windows (UWP) с помощью DirectX](tutorial--create-your-first-uwp-directx-game.md) . В разделе этой ссылки задается контекст для ряда.

Теперь, когда у нашей игры есть трехмерные визуальные элементы, пришло время сосредоточиться на добавлении некоторых 2D-элементов, чтобы игра могла оставить отзыв о состоянии игры проигрывателю. Это можно сделать, добавив простые параметры меню и компоненты экрана в верхней части трехмерного конвейера графики.

>[!Note]
>Если вы еще не скачали последний код игры для этого примера, перейдите к разделу [Пример игры Direct3D](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/Simple3DGameDX). Этот пример является частью большой коллекции примеров функций UWP. Указания по скачиванию этого примера приводятся в разделе [Получение примеров UWP из GitHub](/windows/uwp/get-started/get-uwp-app-samples).

## <a name="objective"></a>Назначение

С помощью Direct2D добавьте в нашу игру DirectX UWP ряд графических элементов пользовательского интерфейса и поведения, в том числе:
- Отображение заголовков, включая прямоугольники, ограничивающие [контроллеры перемещения](tutorial--adding-controls.md)
- Меню состояния игры


## <a name="the-user-interface-overlay"></a>Перекрытие пользовательского интерфейса


Хотя существует много способов отобразить текст и элементы пользовательского интерфейса в игре DirectX, мы будем сосредоточиться на использовании [Direct2D](/windows/desktop/Direct2D/direct2d-portal). Мы также будем использовать [DirectWrite](/windows/desktop/DirectWrite/direct-write-portal) для текстовых элементов.


Direct2D — это набор API-интерфейсов двухмерной прорисовки, используемый для рисования примитивов и эффектов на основе точек. При начале работы с Direct2D лучше не усложнять. Сложные макеты и функции интерфейса требуют времени на разработку и заблаговременного планирования. Если для игры требуется сложный пользовательский интерфейс, например в играх моделирования и стратегии, рассмотрите возможность использования XAML.

> [!NOTE]
> Сведения о разработке пользовательского интерфейса с XAML в игре с помощью DirectX в UWP см. в разделе [расширение игры в образце](tutorial-resources.md).

Direct2D не предназначен специально для пользовательских интерфейсов или макетов, таких как HTML и XAML. Он не предоставляет компоненты пользовательского интерфейса, такие как списки, поля или кнопки. Он также не предоставляет компоненты макета, такие как div, таблицы или сетки.


Для этого примера игры у нас есть два основных компонента пользовательского интерфейса.
1. Заголовков для оценки и управления в игре.
2. Наложение, используемое для отображения текста состояния игры и параметров, таких как пауза и уровень запуска.

### <a name="using-direct2d-for-a-heads-up-display"></a>Использование Direct2D для отображения экранных элементов

На следующем рисунке показаны заголовки, отображаемые в игре, для примера. Это просто и не перегружено, позволяя игроку сосредоточиться на перемещении трехмерного мира и целевых целей. Хороший интерфейс или отображение заголовков не должны усложнить способность проигрывателя обрабатывать события в игре и реагировать на них.

![Снимок экрана с перекрытием в игре](images/simple-dx-game-ui-overlay.png)

Наложение состоит из следующих основных примитивов.
- [**DirectWrite**](/windows/desktop/DirectWrite/direct-write-portal) текст в правом верхнем углу, который информирует игрока 
    - Успешных попаданий
    - Число снимков, сделанных проигрывателем
    - Оставшееся время на уровне
    - Текущий номер уровня 
- Два пересекающихся сегмента линии, используемые для формирования перекрестных крестиков
- Два прямоугольника в нижних углах для границы [контроллера перемещения и просмотра](tutorial--adding-controls.md) . 


Состояние отображения наложения в игре отображается в методе [**гамехуд:: Render**](https://github.com/Microsoft/Windows-universal-samples/blob/5f0d0912214afc1c2a7c7470203933ddb46f7c89/Samples/Simple3DGameDX/cpp/GameHud.cpp#L234-L358) класса [**гамехуд**](https://github.com/Microsoft/Windows-universal-samples/blob/5f0d0912214afc1c2a7c7470203933ddb46f7c89/Samples/Simple3DGameDX/cpp/GameHud.h) . В этом методе наложение Direct2D, представляющее пользовательский интерфейс, обновляется для отражения изменений числа попаданий, оставшегося времени и номера уровня.

Если игра была инициализирована, добавьте `TotalHits()` , `TotalShots()` и `TimeRemaining()` в [**swprintf_s**](/cpp/c-runtime-library/reference/sprintf-s-sprintf-s-l-swprintf-s-swprintf-s-l) буфер и укажите формат печати. Затем можно нарисовать его с помощью метода [**DrawText**](/windows/desktop/Direct2D/id2d1rendertarget-drawtext) . Мы делаем то же самое для индикатора текущего уровня, рисуя пустые числа, чтобы отобразить незавершенные уровни, такие как ➀, и заполненные числа, например ➊, чтобы продемонстрировать, что конкретный уровень был завершен.


В следующем фрагменте кода рассматривается процесс метода **гамехуд:: Render** для 
- Создание точечного рисунка с помощью [* * ID2D1RenderTarget::D равбитмап * *](/windows/desktop/api/d2d1/nf-d2d1-id2d1rendertarget-drawbitmap(id2d1bitmap_constd2d1_rect_f__float_d2d1_bitmap_interpolation_mode_constd2d1_rect_f_))
- Разбивает области пользовательского интерфейса на прямоугольники с помощью [ **D2D1:: ректф**](/windows/desktop/api/dcommon/ns-dcommon-d2d_rect_f)
- Использование **DrawText** для создания элементов текста

```cppwinrt
void GameHud::Render(_In_ std::shared_ptr<Simple3DGame> const& game)
{
    auto d2dContext = m_deviceResources->GetD2DDeviceContext();
    auto windowBounds = m_deviceResources->GetLogicalSize();

    if (m_showTitle)
    {
        d2dContext->DrawBitmap(
            m_logoBitmap.get(),
            D2D1::RectF(
                GameUIConstants::Margin,
                GameUIConstants::Margin,
                m_logoSize.width + GameUIConstants::Margin,
                m_logoSize.height + GameUIConstants::Margin
                )
            );
        d2dContext->DrawTextLayout(
            Point2F(m_logoSize.width + 2.0f * GameUIConstants::Margin, GameUIConstants::Margin),
            m_titleHeaderLayout.get(),
            m_textBrush.get()
            );
        d2dContext->DrawTextLayout(
            Point2F(GameUIConstants::Margin, m_titleBodyVerticalOffset),
            m_titleBodyLayout.get(),
            m_textBrush.get()
            );
    }

    // Draw text for number of hits, total shots, and time remaining
    if (game != nullptr)
    {
        // This section is only used after the game state has been initialized.
        static const int bufferLength = 256;
        static wchar_t wsbuffer[bufferLength];
        int length = swprintf_s(
            wsbuffer,
            bufferLength,
            L"Hits:\t%10d\nShots:\t%10d\nTime:\t%8.1f",
            game->TotalHits(),
            game->TotalShots(),
            game->TimeRemaining()
            );

        // Draw the upper right portion of the HUD displaying total hits, shots, and time remaining
        d2dContext->DrawText(
            wsbuffer,
            length,
            m_textFormatBody.get(),
            D2D1::RectF(
                windowBounds.Width - GameUIConstants::HudRightOffset,
                GameUIConstants::HudTopOffset,
                windowBounds.Width,
                GameUIConstants::HudTopOffset + (GameUIConstants::HudBodyPointSize + GameUIConstants::Margin) * 3
                ),
            m_textBrush.get()
            );

        // Using the unicode characters starting at 0x2780 ( ➀ ) for the consecutive levels of the game.
        // For completed levels start with 0x278A ( ➊ ) (This is 0x2780 + 10).
        uint32_t levelCharacter[6];
        for (uint32_t i = 0; i < 6; i++)
        {
            levelCharacter[i] = 0x2780 + i + ((static_cast<uint32_t>(game->LevelCompleted()) == i) ? 10 : 0);
        }
        length = swprintf_s(
            wsbuffer,
            bufferLength,
            L"%lc %lc %lc %lc %lc %lc",
            levelCharacter[0],
            levelCharacter[1],
            levelCharacter[2],
            levelCharacter[3],
            levelCharacter[4],
            levelCharacter[5]
            );
        // Create a new rectangle and draw the current level info text inside
        d2dContext->DrawText(
            wsbuffer,
            length,
            m_textFormatBodySymbol.get(),
            D2D1::RectF(
                windowBounds.Width - GameUIConstants::HudRightOffset,
                GameUIConstants::HudTopOffset + (GameUIConstants::HudBodyPointSize + GameUIConstants::Margin) * 3 + GameUIConstants::Margin,
                windowBounds.Width,
                GameUIConstants::HudTopOffset + (GameUIConstants::HudBodyPointSize + GameUIConstants::Margin) * 4
                ),
            m_textBrush.get()
            );

        if (game->IsActivePlay())
        {
            // Draw the move and fire rectangles
            ...
            // Draw the crosshairs
            ...
        }
    }
}
```

Разбивая этот метод дальше, эта часть метода [**гамехуд:: Render**](https://github.com/Microsoft/Windows-universal-samples/blob/5f0d0912214afc1c2a7c7470203933ddb46f7c89/Samples/Simple3DGameDX/cpp/GameHud.cpp#L320-L358) Рисует прямоугольники перемещения и пожара с помощью [**ID2D1RenderTarget::D равректангле**](/windows/desktop/api/d2d1/nf-d2d1-id2d1rendertarget-drawrectangle(constd2d1_rect_f__id2d1brush_float_id2d1strokestyle))и перекрестий с использованием двух вызовов [**ID2D1RenderTarget::D равлине**](/windows/desktop/api/d2d1/nf-d2d1-id2d1rendertarget-drawline).

```cppwinrt
// Check if game is playing
if (game->IsActivePlay())
{
    // Draw a rectangle for the touch input for the move control.
    d2dContext->DrawRectangle(
        D2D1::RectF(
            0.0f,
            windowBounds.Height - GameUIConstants::TouchRectangleSize,
            GameUIConstants::TouchRectangleSize,
            windowBounds.Height
            ),
        m_textBrush.get()
        );
    // Draw a rectangle for the touch input for the fire control.
    d2dContext->DrawRectangle(
        D2D1::RectF(
            windowBounds.Width - GameUIConstants::TouchRectangleSize,
            windowBounds.Height - GameUIConstants::TouchRectangleSize,
            windowBounds.Width,
            windowBounds.Height
            ),
        m_textBrush.get()
        );

    // Draw the cross hairs
    d2dContext->DrawLine(
        D2D1::Point2F(windowBounds.Width / 2.0f - GameUIConstants::CrossHairHalfSize,
            windowBounds.Height / 2.0f),
        D2D1::Point2F(windowBounds.Width / 2.0f + GameUIConstants::CrossHairHalfSize,
            windowBounds.Height / 2.0f),
        m_textBrush.get(),
        3.0f
        );
    d2dContext->DrawLine(
        D2D1::Point2F(windowBounds.Width / 2.0f, windowBounds.Height / 2.0f -
            GameUIConstants::CrossHairHalfSize),
        D2D1::Point2F(windowBounds.Width / 2.0f, windowBounds.Height / 2.0f +
            GameUIConstants::CrossHairHalfSize),
        m_textBrush.get(),
        3.0f
        );
}
```

В методе **гамехуд:: Render** мы сохраняем логический размер игрового окна в `windowBounds` переменной. В этом случае используется [`GetLogicalSize`](https://github.com/Microsoft/Windows-universal-samples/blob/5f0d0912214afc1c2a7c7470203933ddb46f7c89/Samples/Simple3DGameDX/cpp/Common/DeviceResources.h#L41) метод класса **DeviceResources** . 

```cppwinrt
auto windowBounds = m_deviceResources->GetLogicalSize();
```

Получение размера окна игры очень важно для программирования пользовательского интерфейса. Размер окна указывается в измерении с именем DIP (аппаратно-независимые пиксели), где DIP определяется как 1/96 дюйма. Direct2D масштабирует единицы отрисовки на фактические Пиксели при прорисовке, выполняя это с помощью параметра точки на дюйм в Windows. Аналогичным образом при рисовании текста с помощью [**DirectWrite**](/windows/desktop/DirectWrite/direct-write-portal)вы указываете DIP, а не точки для размера шрифта. Значения DIP выражаются числами с плавающей запятой. 

### <a name="displaying-game-state-info"></a>Отображение сведений о состоянии игры

Помимо внешнего экрана, в примере игры есть наложение, представляющее шесть состояний игры. Все состояния имеют большой черный прямоугольник с текстом, который проигрыватель считывает. Прямоугольники и перекрестия контроллера перемещения не рисуются, так как они не активны в этих состояниях.

Наложение создается с помощью класса [**гамеинфуверлай**](https://github.com/Microsoft/Windows-universal-samples/blob/5f0d0912214afc1c2a7c7470203933ddb46f7c89/Samples/Simple3DGameDX/cpp/GameInfoOverlay.h) , что позволяет нам выровнять отображаемый текст, чтобы согласовать с состоянием игры.

![состояние и действие оверлея](images/simple-dx-game-ui-finaloverlay.png)

Наложение разбивается на два раздела: **состояние** и **действие**. **Состояние** сектон далее разбивается на прямоугольники **заголовка** и **текста** . Раздел **Action** содержит только один прямоугольник. У каждого прямоугольника есть другая цель.

-   `titleRectangle`содержит текст заголовка.
-   `bodyRectangle`содержит основной текст.
-   `actionRectangle`содержит текст, который информирует игрока о необходимости выполнения определенного действия.

Игра имеет шесть состояний, которые можно установить. Состояние игры, переданного с помощью части **состояния** оверлея. Прямоугольники **состояния** обновляются с помощью ряда методов, соответствующих следующим состояниям.

- Загрузка
- Начальная статистика начального или высокого показателя
- Начало уровня
- Игра приостановлена
- Игра закончена
- Игра заключена


Часть **действия** оверлея обновляется с помощью метода [**Гамеинфуверлай:: сетактион**](https://github.com/Microsoft/Windows-universal-samples/blob/5f0d0912214afc1c2a7c7470203933ddb46f7c89/Samples/Simple3DGameDX/cpp/GameInfoOverlay.cpp#L522-L564) , что позволяет задать для текста действия один из следующих элементов.
- "Коснитесь, чтобы воспроизвести еще раз..."
- "Загрузка на уровне, подождите..."
- "Коснитесь, чтобы продолжить..."
- Отсутствуют

> [!NOTE]
> Оба эти метода будут рассмотрены далее в разделе [представление состояния игры](#representing-game-state) .

В зависимости от того, что происходит в игре, текстовые поля **состояние** и **действие** корректируются.
Давайте посмотрим, как мы инициализируем и нарисуем наложение для этих шести состояний.

### <a name="initializing-and-drawing-the-overlay"></a>Инициализация и рисование перекрытия

Шесть состояний **состояния** имеют несколько общих моментов, что делает необходимые ресурсы и методы очень похожими.
    - Все они используют черный прямоугольник в центре экрана в качестве фона.
    - Отображаемый текст — **заголовок** или **основной** текст.
    - Текст использует шрифт Segoe UI и рисуется поверх заднего прямоугольника. 


В примере игры есть четыре метода, которые возникают при создании наложения.
 

#### <a name="gameinfooverlaygameinfooverlay"></a>Гамеинфуверлай:: Гамеинфуверлай
Конструктор [**гамеинфуверлай:: гамеинфуверлай**](https://github.com/Microsoft/Windows-universal-samples/blob/5f0d0912214afc1c2a7c7470203933ddb46f7c89/Samples/Simple3DGameDX/cpp/GameInfoOverlay.cpp#L30-L78) инициализирует наложение, сохраняя поверхность точечного рисунка, которая будет использоваться для отображения сведений для проигрывателя. Конструктор получает фабрику от переданного в нее объекта [**ID2D1Device**](/windows/desktop/api/d2d1_1/nn-d2d1_1-id2d1device) , который используется для создания [**ID2D1DeviceContext**](/windows/desktop/api/d2d1_1/nn-d2d1_1-id2d1devicecontext) , в который может нарисоваться сам объект оверлея. [Идвритефактори:: Креатетекстформат](/windows/desktop/api/dwrite/nf-dwrite-idwritefactory-createtextformat) 


#### <a name="gameinfooverlaycreatedevicedependentresources"></a>Гамеинфуверлай:: Креатедевицедепендентресаурцес
[**Гамеинфуверлай:: креатедевицедепендентресаурцес**](https://github.com/Microsoft/Windows-universal-samples/blob/5f0d0912214afc1c2a7c7470203933ddb46f7c89/Samples/Simple3DGameDX/cpp/GameInfoOverlay.cpp#L82-L104) — наш метод создания кистей, которые будут использоваться для рисования текста. Для этого мы получаем объект [**ID2D1DeviceContext2**](/windows/desktop/api/d2d1_3/nn-d2d1_3-id2d1devicecontext2) , который обеспечивает создание и рисование геометрии, а также такие функции, как рукописный ввод и рендеринг сетки градиента. Затем мы создадим ряд цветных кистей с помощью [**ID2D1SolidColorBrush**](/windows/desktop/api/d2d1/nn-d2d1-id2d1solidcolorbrush) для рисования элементов пользовательского интерфейса фоллинг.
- Черная кисть для фона прямоугольника
- Белая кисть для текста состояния
- Оранжевая кисть для текста действия

#### <a name="deviceresourcessetdpi"></a>DeviceResources:: Сетдпи

Метод [**DeviceResources:: сетдпи**](https://github.com/Microsoft/Windows-universal-samples/blob/5f0d0912214afc1c2a7c7470203933ddb46f7c89/Samples/Simple3DGameDX/cpp/Common/DeviceResources.cpp#L514-L527) задает точки на дюйм окна. Этот метод вызывается при изменении DPI и требует его перенастройки, что происходит при изменении размера игрового окна. После обновления DPI этот метод также вызывает[**DeviceResources:: креатевиндовсизедепендентресаурцес**](https://github.com/Microsoft/Windows-universal-samples/blob/5f0d0912214afc1c2a7c7470203933ddb46f7c89/Samples/Simple3DGameDX/cpp/Common/DeviceResources.cpp#L214-L487) , чтобы обеспечить повторное создание необходимых ресурсов при каждом изменении размера окна.

#### <a name="gameinfooverlaycreatewindowssizedependentresources"></a>Гамеинфуверлай:: Креатевиндовссизедепендентресаурцес

Метод [**гамеинфуверлай:: креатевиндовссизедепендентресаурцес**](https://github.com/Microsoft/Windows-universal-samples/blob/5f0d0912214afc1c2a7c7470203933ddb46f7c89/Samples/Simple3DGameDX/cpp/GameInfoOverlay.cpp#L108-L225) — это место, где происходит вся прорисовка. Ниже приведена структура шагов метода.
- Три прямоугольника создаются для разсечения текста пользовательского интерфейса для **заголовка**, **текста**и **действия** .
    ```cppwinrt 
    m_titleRectangle = D2D1::RectF(
        GameInfoOverlayConstant::SideMargin,
        GameInfoOverlayConstant::TopMargin,
        overlaySize.width - GameInfoOverlayConstant::SideMargin,
        GameInfoOverlayConstant::TopMargin + GameInfoOverlayConstant::TitleHeight
        );
    m_actionRectangle = D2D1::RectF(
        GameInfoOverlayConstant::SideMargin,
        overlaySize.height - (GameInfoOverlayConstant::ActionHeight + GameInfoOverlayConstant::BottomMargin),
        overlaySize.width - GameInfoOverlayConstant::SideMargin,
        overlaySize.height - GameInfoOverlayConstant::BottomMargin
        );
    m_bodyRectangle = D2D1::RectF(
        GameInfoOverlayConstant::SideMargin,
        m_titleRectangle.bottom + GameInfoOverlayConstant::Separator,
        overlaySize.width - GameInfoOverlayConstant::SideMargin,
        m_actionRectangle.top - GameInfoOverlayConstant::Separator
        );
    ```

- Создается растровое изображение `m_levelBitmap` , которое учитывает текущее значение dpi с помощью **креатебитмап**.
- `m_levelBitmap`задан как наш 2D-объект отрисовки с помощью [**ID2D1DeviceContext:: сеттаржет**](/windows/desktop/api/d2d1_1/nf-d2d1_1-id2d1devicecontext-settarget).
- Растровое изображение очищается, когда каждый пиксель становится черным с помощью [**ID2D1RenderTarget:: Clear**](/windows/desktop/api/d2d1/nf-d2d1-id2d1rendertarget-clear).
- Для инициации рисования вызывается [**ID2D1RenderTarget:: бегиндрав**](/windows/desktop/api/d2d1/nf-d2d1-id2d1rendertarget-begindraw) . 
- **DrawText** вызывается для рисования текста, хранящегося в `m_titleString` , `m_bodyString` и `m_actionString` в прямоугольнике аппропериате с помощью соответствующего **ID2D1SolidColorBrush**.
- [**ID2D1RenderTarget:: EndDraw**](ID2D1RenderTarget::EndDraw) вызывается для завершения всех операций рисования `m_levelBitmap` .
- Другой точечный рисунок создается с помощью **креатебитмап** с именем `m_tooSmallBitmap` для использования в качестве резерва, который отображается только в том случае, если конфигурация экрана слишком мала для игры.
- Повторять процесс для рисования на `m_levelBitmap` `m_tooSmallBitmap` , на этот раз только `Paused` выполнит рисование строки в тексте.




Теперь нам нужно шесть методов для заполнения текста шести состояний наложения!

### <a name="representing-game-state"></a>Представление состояния игры


Каждое из шести состояний наложения в игре имеет соответствующий метод в объекте **гамеинфуверлай** . Эти методы рисуют вариант перекрытия для явной передачи игроку информации об игре. Это взаимодействие представлено с **заголовком** и строкой **текста** . Так как в примере уже настроены ресурсы и макет для этих сведений при инициализации, а также метод [**гамеинфуверлай:: креатедевицедепендентресаурцес**](https://github.com/Microsoft/Windows-universal-samples/blob/5f0d0912214afc1c2a7c7470203933ddb46f7c89/Samples/Simple3DGameDX/cpp/GameInfoOverlay.cpp#L82-L104) , ему необходимо предоставить только строки с состоянием оверлея.

Часть **состояния** оверлея задается вызовом одного из следующих методов.

Состояние игры | Метод задания состояния | Поля состояния
:----- | :------- | :---------
Загрузка | [Гамеинфуверлай:: Сетгамелоадинг](https://github.com/Microsoft/Windows-universal-samples/blob/5f0d0912214afc1c2a7c7470203933ddb46f7c89/Samples/Simple3DGameDX/cpp/GameInfoOverlay.cpp#L254-L306) |**Title**</br>Загрузка ресурсов </br>**Текст**</br> Пошаговая печать "." для подразумевая загрузки действия.
Начальная статистика начального или высокого показателя | [Гамеинфуверлай:: Сетгаместатс](https://github.com/Microsoft/Windows-universal-samples/blob/5f0d0912214afc1c2a7c7470203933ddb46f7c89/Samples/Simple3DGameDX/cpp/GameInfoOverlay.cpp#L310-L354) |**Title**</br>Высокая оценка</br> **Текст**</br> Уровни завершены # </br>Всего очков #</br>Всего снимков #
Начало уровня | [Гамеинфуверлай:: Сетлевелстарт](https://github.com/Microsoft/Windows-universal-samples/blob/5f0d0912214afc1c2a7c7470203933ddb46f7c89/Samples/Simple3DGameDX/cpp/GameInfoOverlay.cpp#L413-L471) |**Title**</br>Уровню #</br>**Текст**</br>Описание цели уровня.
Игра приостановлена | [Гамеинфуверлай:: Сетпаусе](https://github.com/Microsoft/Windows-universal-samples/blob/5f0d0912214afc1c2a7c7470203933ddb46f7c89/Samples/Simple3DGameDX/cpp/GameInfoOverlay.cpp#L475-L502) |**Title**</br>Игра приостановлена</br>**Текст**</br>Отсутствуют
Игра закончена | [Гамеинфуверлай:: Сетгамеовер](https://github.com/Microsoft/Windows-universal-samples/blob/5f0d0912214afc1c2a7c7470203933ddb46f7c89/Samples/Simple3DGameDX/cpp/GameInfoOverlay.cpp#L358-L409) |**Title**</br>Игра закончена</br> **Текст**</br> Уровни завершены # </br>Всего очков #</br>Всего снимков #</br>Уровни завершены #</br>Высокая оценка #
Игра заключена | [Гамеинфуверлай:: Сетгамеовер](https://github.com/Microsoft/Windows-universal-samples/blob/5f0d0912214afc1c2a7c7470203933ddb46f7c89/Samples/Simple3DGameDX/cpp/GameInfoOverlay.cpp#L358-L409) |**Title**</br>Вы выиграли!</br> **Текст**</br> Уровни завершены # </br>Всего очков #</br>Всего снимков #</br>Уровни завершены #</br>Высокая оценка #

С помощью метода [**гамеинфуверлай:: креатевиндовсизедепендентресаурцес**](https://github.com/Microsoft/Windows-universal-samples/blob/5f0d0912214afc1c2a7c7470203933ddb46f7c89/Samples/Simple3DGameDX/cpp/GameInfoOverlay.cpp#L117-L134) пример объявил три прямоугольные области, соответствующие определенным регионам наложения.

Рассмотрим один из методов состояния — **GameInfoOverlay::SetGameStats** — с учетом расположения этих областей и узнаем, как рисуется перекрытие.

```cppwinrt
void GameInfoOverlay::SetGameStats(int maxLevel, int hitCount, int shotCount)
{
    int length;

    auto d2dContext = m_deviceResources->GetD2DDeviceContext();

    d2dContext->SetTarget(m_levelBitmap.get());
    d2dContext->BeginDraw();
    d2dContext->SetTransform(D2D1::Matrix3x2F::Identity());
    d2dContext->FillRectangle(&m_titleRectangle, m_backgroundBrush.get());
    d2dContext->FillRectangle(&m_bodyRectangle, m_backgroundBrush.get());
    m_titleString = L"High Score";

    d2dContext->DrawText(
        m_titleString.c_str(),
        m_titleString.size(),
        m_textFormatTitle.get(),
        m_titleRectangle,
        m_textBrush.get()
        );
    length = swprintf_s(
        wsbuffer,
        bufferLength,
        L"Levels Completed %d\nTotal Points %d\nTotal Shots %d",
        maxLevel,
        hitCount,
        shotCount
        );
    m_bodyString = std::wstring(wsbuffer, length);
    d2dContext->DrawText(
        m_bodyString.c_str(),
        m_bodyString.size(),
        m_textFormatBody.get(),
        m_bodyRectangle,
        m_textBrush.get()
        );

    // We ignore D2DERR_RECREATE_TARGET here. This error indicates that the device
    // is lost. It will be handled during the next call to Present.
    HRESULT hr = d2dContext->EndDraw();
    if (hr != D2DERR_RECREATE_TARGET)
    {
        // The D2DERR_RECREATE_TARGET indicates there has been a problem with the underlying
        // D3D device. All subsequent rendering will be ignored until the device is recreated.
        // This error will be propagated and the appropriate D3D error will be returned from the
        // swapchain->Present(...) call. At that point, the sample will recreate the device
        // and all associated resources. As a result, the D2DERR_RECREATE_TARGET doesn't
        // need to be handled here.
        winrt::check_hresult(hr);
    }
}
```

При использовании контекста устройства Direct2D, инициализированного объектом **гамеинфуверлай** , этот метод заполняет прямоугольники заголовка и тела черным цветом с помощью кисти фона. Белой кистью в прямоугольнике заголовка рисуется текст "High Score" (Лучший результат), а в прямоугольнике основного текста — строка с обновленным состоянием игры.


Прямоугольник действия обновляется путем последующего вызова [**гамеинфуверлай:: сетактион**](https://github.com/Microsoft/Windows-universal-samples/blob/5f0d0912214afc1c2a7c7470203933ddb46f7c89/Samples/Simple3DGameDX/cpp/GameInfoOverlay.cpp#L522-L564) из метода объекта **гамемаин** , который предоставляет сведения о состоянии игры, необходимые **гамеинфуверлай:: сетактион** , чтобы определить нужное сообщение для проигрывателя, например "коснуться, чтобы продолжить".

Наложение для любого заданного состояния выбирается в методе [**гамемаин:: сетгамеинфуверлай**](https://github.com/Microsoft/Windows-universal-samples/blob/6370138b150ca8a34ff86de376ab6408c5587f5d/Samples/Simple3DGameXaml/cpp/GameMain.cpp#L606-L661) следующим образом:

```cppwinrt
void GameMain::SetGameInfoOverlay(GameInfoOverlayState state)
{
    m_gameInfoOverlayState = state;
    switch (state)
    {
    case GameInfoOverlayState::Loading:
        m_uiControl->SetGameLoading(m_loadingCount);
        break;

    case GameInfoOverlayState::GameStats:
        m_uiControl->SetGameStats(
            m_game->HighScore().levelCompleted + 1,
            m_game->HighScore().totalHits,
            m_game->HighScore().totalShots
            );
        break;

    case GameInfoOverlayState::LevelStart:
        m_uiControl->SetLevelStart(
            m_game->LevelCompleted() + 1,
            m_game->CurrentLevel()->Objective(),
            m_game->CurrentLevel()->TimeLimit(),
            m_game->BonusTime()
            );
        break;

    case GameInfoOverlayState::GameOverCompleted:
        m_uiControl->SetGameOver(
            true,
            m_game->LevelCompleted() + 1,
            m_game->TotalHits(),
            m_game->TotalShots(),
            m_game->HighScore().totalHits
            );
        break;

    case GameInfoOverlayState::GameOverExpired:
        m_uiControl->SetGameOver(
            false,
            m_game->LevelCompleted(),
            m_game->TotalHits(),
            m_game->TotalShots(),
            m_game->HighScore().totalHits
            );
        break;

    case GameInfoOverlayState::Pause:
        m_uiControl->SetPause(
            m_game->LevelCompleted() + 1,
            m_game->TotalHits(),
            m_game->TotalShots(),
            m_game->TimeRemaining()
            );
        break;
    }
}
```

Теперь у игры есть способ передачи текстовых данных проигрывателю на основе состояния игры, и у нас есть способ переключения того, что отображается для них во всей игре.

### <a name="next-steps"></a>Следующие шаги

В следующем разделе, посвященном [добавлению элементов управления](tutorial--adding-controls.md), мы рассмотрим, как проигрыватель взаимодействует с образцом игры и как вход изменяет состояние игры.
