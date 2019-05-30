---
title: Добавление пользовательского интерфейса
description: Сведения о добавлении наложенными интерфейс 2D пользователя для игры DirectX универсальной платформы Windows.
ms.assetid: fa40173e-6cde-b71b-e307-db90f0388485
ms.date: 10/24/2017
ms.topic: article
keywords: windows 10, uwp, игры, пользовательский интерфейс, directx
ms.localizationpriority: medium
ms.openlocfilehash: ef966901534302c505ddad37bd277d9141b512a1
ms.sourcegitcommit: ac7f3422f8d83618f9b6b5615a37f8e5c115b3c4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/29/2019
ms.locfileid: "66367864"
---
# <a name="add-a-user-interface"></a>Добавление пользовательского интерфейса


Теперь, когда нашей игре имеет его трехмерные визуальные элементы на месте, пришло время уделяем внимание добавлению некоторые двухмерные элементы, так что игру можно оставить отзыв о состояния игры с проигрывателем. Это можно сделать, добавив параметры простого меню и окна отображения компонентов на основе трехмерной графики конвейера выходные данные.

>[!Note]
>Если вы еще не скачали последний код для игры из этого примера, перейдите в раздел [Пример игры Direct3D](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/Simple3DGameDX). Этот пример является частью большой коллекции примеров функций UWP. Указания по скачиванию этого примера приводятся в разделе [Получение примеров UWP из GitHub](https://docs.microsoft.com/windows/uwp/get-started/get-uwp-app-samples).

## <a name="objective"></a>Цель

С помощью Direct2D, добавьте несколько графического интерфейса пользователя и поведения наших DirectX для универсальной платформы Windows игры включая:
- Отображение информационных индикаторов, включая [внешний вид, Перемещение контроллера](tutorial--adding-controls.md) boundry прямоугольников
- Меню состояния игры


## <a name="the-user-interface-overlay"></a>Перекрытие пользовательского интерфейса


Хотя существует много способов для отображения текста и пользовательских элементов интерфейса в игр DirectX, мы собираемся фокус по использованию [Direct2D](https://docs.microsoft.com/windows/desktop/Direct2D/direct2d-portal). Мы также используем [DirectWrite](https://docs.microsoft.com/windows/desktop/DirectWrite/direct-write-portal) для текстовых элементов.


Direct2D — набор двухмерных рисования API используется для рисования точечных примитивы и эффекты. При начале работы с помощью Direct2D, лучше всего усложнять вещи. Сложные макеты и функции интерфейса требуют времени на разработку и заблаговременного планирования. Если вашей игре требуются сложного пользовательского интерфейса, аналогичные операциям в моделирование и стратегические игры, попробуйте использовать XAML.

> [!NOTE]
> Сведения о разработке пользовательского интерфейса с помощью XAML в игру DirectX для универсальной платформы Windows, см. в разделе [расширение пример игры](tutorial-resources.md).

Direct2D не предназначена специально для пользовательских интерфейсов или макеты, например, HTML и XAML. Она не предоставляет компоненты пользовательского интерфейса, такие как списки, поля или кнопки. Он также не предоставляет макет компонентов, таких как элементы DIV, таблицы или сетки.


Для этого образца игры у нас есть два основных компонента пользовательского интерфейса.
1. Индикации оценка и элементы управления, в игре.
2. Наложение, используемый для отображения текста состояния игры и параметры, такие как Приостановка сведения и параметры запуска уровень.

### <a name="using-direct2d-for-a-heads-up-display"></a>Использование Direct2D для отображения экранных элементов

На следующем рисунке индикации в игре для примера. Это деталей, позволяя видеопроигрывателю сосредоточиться на перемещение трехмерный мир и устранении неполадок целевых объектов. Хороший интерфейс или hud-дисплея никогда не должен усложнить возможности проигрывателя для обработки и реагировать на них в игре.

![Снимок экрана с перекрытием в игре](images/simple-dx-game-ui-overlay.png)

Наложение состоит из следующих основных примитивы.
- [**DirectWrite** ](https://docs.microsoft.com/windows/desktop/DirectWrite/direct-write-portal) текста в правом верхнем углу, о том, исполнитель 
    - Успешных попаданий
    - Число снимков, сделанные проигрывателя
    - Время, оставшееся на уровне
    - Номер текущего уровня 
- Два пересекающиеся сегменты линии, используемый для формирования перекрестие
- Двух прямоугольников в углах нижней для [внешний вид, Перемещение контроллера](tutorial--adding-controls.md) границы. 


Состояние внутриигровой hud-дисплея наложения рисуется [ **GameHud::Render** ](https://github.com/Microsoft/Windows-universal-samples/blob/5f0d0912214afc1c2a7c7470203933ddb46f7c89/Samples/Simple3DGameDX/cpp/GameHud.cpp#L234-L358) метод [ **GameHud** ](https://github.com/Microsoft/Windows-universal-samples/blob/5f0d0912214afc1c2a7c7470203933ddb46f7c89/Samples/Simple3DGameDX/cpp/GameHud.h) класса. В этом методе Direct2D оверлее, который представляет наш пользовательский Интерфейс обновляется в соответствии с изменениями в число попаданий, оставшиеся и уровней количество раз.

При инициализации игры, мы добавим `TotalHits()`, `TotalShots()`, и `TimeRemaining()` для [ **swprintf_s** ](https://docs.microsoft.com/cpp/c-runtime-library/reference/sprintf-s-sprintf-s-l-swprintf-s-swprintf-s-l) буфера и указать формат печати. Затем можно провести его с помощью [ **DrawText** ](https://docs.microsoft.com/windows/desktop/Direct2D/id2d1rendertarget-drawtext) метод. Мы не только для текущего уровня индикатора, рисования пустой числа для отображения незавершенных уровней, таких как ➀ и заполненный числа, например ➊, чтобы показать, что определенный уровень была выполнена.


В следующем фрагменте кода описывается **GameHud::Render** процесс метода 
- Создание растрового изображения с помощью [** ID2D1RenderTarget::DrawBitmap **](https://docs.microsoft.com/windows/desktop/api/d2d1/nf-d2d1-id2d1rendertarget-drawbitmap(id2d1bitmap_constd2d1_rect_f__float_d2d1_bitmap_interpolation_mode_constd2d1_rect_f_))
- Разделив областей пользовательского интерфейса в прямоугольников с помощью [ **D2D1::RectF**](https://docs.microsoft.com/windows/desktop/api/dcommon/ns-dcommon-d2d_rect_f)
- С помощью **DrawText** чтобы сделать текстовые элементы

```cpp
void GameHud::Render(_In_ Simple3DGame^ game)
{
    auto d2dContext = m_deviceResources->GetD2DDeviceContext();
    auto windowBounds = m_deviceResources->GetLogicalSize();

    if (m_showTitle)
    {
        d2dContext->DrawBitmap(
            m_logoBitmap.Get(),
            D2D1::RectF(
                GameConstants::Margin,
                GameConstants::Margin,
                m_logoSize.width + GameConstants::Margin,
                m_logoSize.height + GameConstants::Margin
                )
            );
        d2dContext->DrawTextLayout(
            Point2F(m_logoSize.width + 2.0f * GameConstants::Margin, GameConstants::Margin),
            m_titleHeaderLayout.Get(),
            m_textBrush.Get()
            );
        d2dContext->DrawTextLayout(
            Point2F(GameConstants::Margin, m_titleBodyVerticalOffset),
            m_titleBodyLayout.Get(),
            m_textBrush.Get()
            );
    }

    // Draw text for number of hits, total shots, and time remaining
    if (game != nullptr)
    {
        // This section is only used after the game state has been initialized.
        static const int bufferLength = 256;
        static char16 wsbuffer[bufferLength];
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
            m_textFormatBody.Get(),
            D2D1::RectF(
                windowBounds.Width - GameConstants::HudRightOffset,
                GameConstants::HudTopOffset,
                windowBounds.Width,
                GameConstants::HudTopOffset + (GameConstants::HudBodyPointSize + GameConstants::Margin) * 3
                ),
            m_textBrush.Get()
            );

        // Using the unicode characters starting at 0x2780 ( ➀ ) for the consecutive levels of the game.
        // For completed levels start with 0x278A ( ➊ ) (This is 0x2780 + 10).
        uint32 levelCharacter[6];
        for (uint32 i = 0; i < 6; i++)
        {
            levelCharacter[i] = 0x2780 + i + ((static_cast<uint32>(game->LevelCompleted()) == i) ? 10 : 0);
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
            m_textFormatBodySymbol.Get(),
            D2D1::RectF(
                windowBounds.Width - GameConstants::HudRightOffset,
                GameConstants::HudTopOffset + (GameConstants::HudBodyPointSize + GameConstants::Margin) * 3 + GameConstants::Margin,
                windowBounds.Width,
                GameConstants::HudTopOffset + (GameConstants::HudBodyPointSize+ GameConstants::Margin) * 4
                ),
            m_textBrush.Get()
            );

        if (game->IsActivePlay())
        {
            // Draw the move and fire rectangles
            // Draw the crosshairs
        }
    }
}
```

Критические метод вниз Кроме того, эту часть [ **GameHud::Render** ](https://github.com/Microsoft/Windows-universal-samples/blob/5f0d0912214afc1c2a7c7470203933ddb46f7c89/Samples/Simple3DGameDX/cpp/GameHud.cpp#L320-L358) метод рисует наш переход и инициировать прямоугольники с [ **ID2D1RenderTarget::DrawRectangle** ](https://docs.microsoft.com/windows/desktop/api/d2d1/nf-d2d1-id2d1rendertarget-drawrectangle(constd2d1_rect_f__id2d1brush_float_id2d1strokestyle))и с помощью двух вызовов в виде перекрестия [ **ID2D1RenderTarget::DrawLine**](https://docs.microsoft.com/windows/desktop/api/d2d1/nf-d2d1-id2d1rendertarget-drawline).

```cpp
        // Check if game is playing
        if (game->IsActivePlay())
        {
            // Draw a rectangle for the touch input for the move control.
            d2dContext->DrawRectangle(
                D2D1::RectF(
                    0.0f,
                    windowBounds.Height - GameConstants::TouchRectangleSize,
                    GameConstants::TouchRectangleSize,
                    windowBounds.Height
                    ),
                m_textBrush.Get()
                );
            // Draw a rectangle for the touch input of the fire control.
            d2dContext->DrawRectangle(
                D2D1::RectF(
                    windowBounds.Width - GameConstants::TouchRectangleSize,
                    windowBounds.Height - GameConstants::TouchRectangleSize,
                    windowBounds.Width,
                    windowBounds.Height
                    ),
                m_textBrush.Get()
                );

            // Draw two lines to form crosshairs
            d2dContext->DrawLine(
                D2D1::Point2F(windowBounds.Width / 2.0f - GameConstants::CrossHairHalfSize, windowBounds.Height / 2.0f),
                D2D1::Point2F(windowBounds.Width / 2.0f + GameConstants::CrossHairHalfSize, windowBounds.Height / 2.0f),
                m_textBrush.Get(),
                3.0f
                );
            d2dContext->DrawLine(
                D2D1::Point2F(windowBounds.Width / 2.0f, windowBounds.Height / 2.0f - GameConstants::CrossHairHalfSize),
                D2D1::Point2F(windowBounds.Width / 2.0f, windowBounds.Height / 2.0f + GameConstants::CrossHairHalfSize),
                m_textBrush.Get(),
                3.0f
                );
        }
```

В **GameHud::Render** метод мы сохраняем логический размер окна игры в `windowBounds` переменной. При этом используется [ `GetLogicalSize` ](https://github.com/Microsoft/Windows-universal-samples/blob/5f0d0912214afc1c2a7c7470203933ddb46f7c89/Samples/Simple3DGameDX/cpp/Common/DeviceResources.h#L41) метод **DeviceResources** класса. 
```cpp
auto windowBounds = m_deviceResources->GetLogicalSize();
```

 Получение размера окна игры очень важен для программирование пользовательского интерфейса. Указывает размер окна в измерения, именем частные интерфейсы (аппаратно-независимых пикселях), где DIP определяется как 1/96 дюйма. Direct2D масштабирует единицы измерения, рисования собственно пикселы при возникновении рисунка, таким образом с помощью Windows точек на дюйм (DPI). Аналогично, при рисовании текста с помощью [ **DirectWrite**](https://docs.microsoft.com/windows/desktop/DirectWrite/direct-write-portal), укажите частные интерфейсы, а не точки для размера шрифта. Значения DIP выражаются числами с плавающей запятой.

 

### <a name="displaying-game-state-info"></a>Отображение сведений о состояния игры

Помимо отображения информационных индикаторов пример игры имеет наложение, представляющий шесть состояния игры. Все состояния компонентов примитив большой черный прямоугольник с текстом для проигрывателя для чтения. Прямоугольники контроллера внешний вид, перемещения и перекрестье так, как они могут не действовать в эти состояния не отображаются.

Наложение создается с помощью [ **GameInfoOverlay** ](https://github.com/Microsoft/Windows-universal-samples/blob/5f0d0912214afc1c2a7c7470203933ddb46f7c89/Samples/Simple3DGameDX/cpp/GameInfoOverlay.h) класса, что позволило нам для какой текст отображается в соответствии с состоянием игры.

![состояния и операций наложения](images/simple-dx-game-ui-finaloverlay.png)

Наложение разбивается на два раздела: **Состояние** и **действие**. **Состояние** ассемблерного дополнительно разделить на **Title** и **текст** прямоугольники. **Действие** раздел имеет только один прямоугольник. Каждый прямоугольник у нее другая роль.

-   `titleRectangle` содержит текст заголовка.
-   `bodyRectangle` содержит текст.
-   `actionRectangle` содержит текст, который информирует проигрывателя, чтобы выполнить определенное действие.

Игра имеет шесть состояний, которые могут быть заданы. Состояние игры, передать **состояние** часть наложения. **Состояние** прямоугольники обновляются с помощью нескольких методов, соответствующих со следующими состояниями.

- Загрузка
- Статистика оценки начального запуска и высокий уровень
- Уровень запуска
- Игра приостановлена
- Игра закончена
- Победа


**Действие** часть наложения обновляется с помощью [ **GameInfoOverlay::SetAction** ](https://github.com/Microsoft/Windows-universal-samples/blob/5f0d0912214afc1c2a7c7470203933ddb46f7c89/Samples/Simple3DGameDX/cpp/GameInfoOverlay.cpp#L522-L564) метод, что текст будет присвоено одно из следующих действий.
- «Коснитесь, чтобы повторно воспроизвести...»
- «Уровень загрузки, пожалуйста, подождите...»
- «Коснитесь, чтобы продолжить...»
- Нет

> [!NOTE]
> Обоих методов будет рассказано далее в [представляющий состояния игры](#representing-game-state) раздел.

В зависимости от того, что происходит в игре **состояние** и **действие** корректируются разделе текстовые поля.
Давайте рассмотрим, как инициализировать и нарисовать наложение для этих шести состояний.

### <a name="initializing-and-drawing-the-overlay"></a>Инициализация и рисование перекрытия

Шесть **состояние** состояния есть несколько общих, ресурсы и методы необходимым очень похожи.
    - Они все используют черный прямоугольник в центре экрана с фоном.
    - Отображаемый текст является либо **Title** или **текст** текста.
    - Текст используется шрифт Segoe UI и рисуется поверх назад прямоугольника. 


Пример игры имеет четыре метода, которые следует учитывать при создании наложения.
 

#### <a name="gameinfooverlaygameinfooverlay"></a>GameInfoOverlay::GameInfoOverlay
[ **GameInfoOverlay::GameInfoOverlay** ](https://github.com/Microsoft/Windows-universal-samples/blob/5f0d0912214afc1c2a7c7470203933ddb46f7c89/Samples/Simple3DGameDX/cpp/GameInfoOverlay.cpp#L30-L78) конструктор инициализирует наложения, рабочей области точечного рисунка, который будет использоваться для отображения информации в проигрыватель на обслуживание. Конструктор получает фабрику из [ **ID2D1Device** ](https://docs.microsoft.com/windows/desktop/api/d2d1_1/nn-d2d1_1-id2d1device) , переданное в него, которая используется для создания [ **ID2D1DeviceContext** ](https://docs.microsoft.com/windows/desktop/api/d2d1_1/nn-d2d1_1-id2d1devicecontext) что сам объект наложения можно нарисовать. [IDWriteFactory::CreateTextFormat](https://docs.microsoft.com/windows/desktop/api/dwrite/nf-dwrite-idwritefactory-createtextformat) 


#### <a name="gameinfooverlaycreatedevicedependentresources"></a>GameInfoOverlay::CreateDeviceDependentResources
[**GameInfoOverlay::CreateDeviceDependentResources** ](https://github.com/Microsoft/Windows-universal-samples/blob/5f0d0912214afc1c2a7c7470203933ddb46f7c89/Samples/Simple3DGameDX/cpp/GameInfoOverlay.cpp#L82-L104) — это наш метод для создания кисти, которая будет использоваться для рисования наш текст. Чтобы сделать это, мы получаем [ **ID2D1DeviceContext2** ](https://docs.microsoft.com/windows/desktop/api/d2d1_3/nn-d2d1_3-id2d1devicecontext2) объект, который позволяет создавать и отрисовка для класса geometry, а также функции, такие как рукописный ввод и градиент mesh подготовки к просмотру. Затем мы создаем ряд цветные кисти с использованием [ **ID2D1SolidColorBrush** ](https://docs.microsoft.com/windows/desktop/api/d2d1/nn-d2d1-id2d1solidcolorbrush) для рисования folling элементы пользовательского интерфейса.
- Черная кисть для фона прямоугольника
- Белый кисти для текста состояния
- Оранжевый кисти для текста действие

#### <a name="deviceresourcessetdpi"></a>DeviceResources::SetDpi
[ **DeviceResources::SetDpi** ](https://github.com/Microsoft/Windows-universal-samples/blob/5f0d0912214afc1c2a7c7470203933ddb46f7c89/Samples/Simple3DGameDX/cpp/Common/DeviceResources.cpp#L514-L527) метод задает размер в точках на дюйм окна. Этот метод вызывается, когда меняется DPI и должен быть перенастроены, что возникает при изменении размера окна игры. После обновления DPI, этот метод также вызывает[**DeviceResources::CreateWindowSizeDependentResources** ](https://github.com/Microsoft/Windows-universal-samples/blob/5f0d0912214afc1c2a7c7470203933ddb46f7c89/Samples/Simple3DGameDX/cpp/Common/DeviceResources.cpp#L214-L487) для создания действительно необходимых ресурсов создаются повторно каждый раз при изменении размера окна.


#### <a name="gameinfooverlaycreatewindowssizedependentresources"></a>GameInfoOverlay::CreateWindowsSizeDependentResources
[ **GameInfoOverlay::CreateWindowsSizeDependentResources** ](https://github.com/Microsoft/Windows-universal-samples/blob/5f0d0912214afc1c2a7c7470203933ddb46f7c89/Samples/Simple3DGameDX/cpp/GameInfoOverlay.cpp#L108-L225) метод является, где наши рисование выполняется. Ниже приводится объяснение того метода действия.
- Создаются три прямоугольника, раздел текста пользовательского интерфейса для **Title**, **текст**, и **действие** текста.
    ```cpp 
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

- Битовая карта создается именованный `m_levelBitmap`, учитывая учетной записи с помощью текущего DPI **CreateBitmap**.
- `m_levelBitmap` задается как нашей двухмерной отрисовки целевого с помощью [ **ID2D1DeviceContext::SetTarget**](https://docs.microsoft.com/windows/desktop/api/d2d1_1/nf-d2d1_1-id2d1devicecontext-settarget).
- Точечный рисунок снят в каждом пикселе, внесенные с помощью черной [ **ID2D1RenderTarget::Clear**](https://docs.microsoft.com/windows/desktop/api/d2d1/nf-d2d1-id2d1rendertarget-clear).
- [**ID2D1RenderTarget::BeginDraw** ](https://docs.microsoft.com/windows/desktop/api/d2d1/nf-d2d1-id2d1rendertarget-begindraw) вызывается в начале документа. 
- **DrawText** вызывается для рисования текста, хранящегося в `m_titleString`, `m_bodyString`, и `m_actionString` в прямоугольнике approperiate, с помощью соответствующего **ID2D1SolidColorBrush**.
- [**ID2D1RenderTarget::EndDraw** ](ID2D1RenderTarget::EndDraw) вызывается, чтобы остановить все операции рисования на `m_levelBitmap`.
- Создан с помощью другого точечного рисунка **CreateBitmap** с именем `m_tooSmallBitmap` для использования в качестве запасного варианта, отображаются только, если конфигурация отображения слишком мал для игры.
- Повторите процедуру для рисования на `m_levelBitmap` для `m_tooSmallBitmap`, на этот раз только графические строка `Paused` в тексте.




Теперь необходимо шесть методов для заполнения текст из наших состояний шесть наложения!

### <a name="representing-game-state"></a>Представляет состояния игры


Каждая из шести наложения состояний в игре имеет соответствующий метод **GameInfoOverlay** объекта. Эти методы рисуют вариант перекрытия для явной передачи игроку информации об игре. Это взаимодействие представляется с помощью **Title** и **текст** строка. Так как образец уже настроенного ресурсы и макет для этой информации при инициализации с [ **GameInfoOverlay::CreateDeviceDependentResources** ](https://github.com/Microsoft/Windows-universal-samples/blob/5f0d0912214afc1c2a7c7470203933ddb46f7c89/Samples/Simple3DGameDX/cpp/GameInfoOverlay.cpp#L82-L104) метод, вполне достаточно для предоставления строки состояния наложения.

**Состояние** задана часть наложения с вызовом одного из следующих методов.

Состояния игры | Метод set состояния | Поля состояния
:----- | :------- | :---------
Загрузка | [GameInfoOverlay::SetGameLoading](https://github.com/Microsoft/Windows-universal-samples/blob/5f0d0912214afc1c2a7c7470203933ddb46f7c89/Samples/Simple3DGameDX/cpp/GameInfoOverlay.cpp#L254-L306) |**Заголовок**</br>Загрузка ресурсов </br>**Текст**</br> Последовательно выводит «.» подразумевается действие загрузки.
Статистика оценки начального запуска и высокий уровень | [GameInfoOverlay::SetGameStats](https://github.com/Microsoft/Windows-universal-samples/blob/5f0d0912214afc1c2a7c7470203933ddb46f7c89/Samples/Simple3DGameDX/cpp/GameInfoOverlay.cpp#L310-L354) |**Заголовок**</br>Высокая оценка</br> **Текст**</br> Уровни завершения # </br>Общее число точек #</br>Всего кадрами #
Уровень запуска | [GameInfoOverlay::SetLevelStart](https://github.com/Microsoft/Windows-universal-samples/blob/5f0d0912214afc1c2a7c7470203933ddb46f7c89/Samples/Simple3DGameDX/cpp/GameInfoOverlay.cpp#L413-L471) |**Заголовок**</br># Уровня</br>**Текст**</br>Описание цели уровня.
Игра приостановлена | [GameInfoOverlay::SetPause](https://github.com/Microsoft/Windows-universal-samples/blob/5f0d0912214afc1c2a7c7470203933ddb46f7c89/Samples/Simple3DGameDX/cpp/GameInfoOverlay.cpp#L475-L502) |**Заголовок**</br>Игра приостановлена</br>**Текст**</br>Нет
Игра закончена | [GameInfoOverlay::SetGameOver](https://github.com/Microsoft/Windows-universal-samples/blob/5f0d0912214afc1c2a7c7470203933ddb46f7c89/Samples/Simple3DGameDX/cpp/GameInfoOverlay.cpp#L358-L409) |**Заголовок**</br>Игра закончена</br> **Текст**</br> Уровни завершения # </br>Общее число точек #</br>Всего кадрами #</br>Уровни завершения #</br>Высокая оценка #
Победа | [GameInfoOverlay::SetGameOver](https://github.com/Microsoft/Windows-universal-samples/blob/5f0d0912214afc1c2a7c7470203933ddb46f7c89/Samples/Simple3DGameDX/cpp/GameInfoOverlay.cpp#L358-L409) |**Заголовок**</br>Вы ВЫИГРАЛИ!</br> **Текст**</br> Уровни завершения # </br>Общее число точек #</br>Всего кадрами #</br>Уровни завершения #</br>Высокая оценка #




С помощью [ **GameInfoOverlay::CreateWindowSizeDependentResources** ](https://github.com/Microsoft/Windows-universal-samples/blob/5f0d0912214afc1c2a7c7470203933ddb46f7c89/Samples/Simple3DGameDX/cpp/GameInfoOverlay.cpp#L117-L134) метод, образец объявлены три прямоугольной области, которые соответствуют определенные регионы наложения.



Рассмотрим один из методов состояния — **GameInfoOverlay::SetGameStats** — с учетом расположения этих областей и узнаем, как рисуется перекрытие.

```cpp
void GameInfoOverlay::SetGameStats(int maxLevel, int hitCount, int shotCount)
{
    int length;

    auto d2dContext = m_deviceResources->GetD2DDeviceContext();

    d2dContext->SetTarget(m_levelBitmap.Get());
    d2dContext->BeginDraw();
    d2dContext->SetTransform(D2D1::Matrix3x2F::Identity());
    d2dContext->FillRectangle(&m_titleRectangle, m_backgroundBrush.Get());
    d2dContext->FillRectangle(&m_bodyRectangle, m_backgroundBrush.Get());
    m_titleString = "High Score";

    d2dContext->DrawText(
        m_titleString->Data(),
        m_titleString->Length(),
        m_textFormatTitle.Get(),
        m_titleRectangle,
        m_textBrush.Get()
        );
    length = swprintf_s(
        wsbuffer,
        bufferLength,
        L"Levels Completed %d\nTotal Points %d\nTotal Shots %d",
        maxLevel,
        hitCount,
        shotCount
        );
    m_bodyString = ref new Platform::String(wsbuffer, length);
    d2dContext->DrawText(
        m_bodyString->Data(),
        m_bodyString->Length(),
        m_textFormatBody.Get(),
        m_bodyRectangle,
        m_textBrush.Get()
        );

    // We ignore D2DERR_RECREATE_TARGET here. This error indicates that the device
    // is lost. It will be handled during the next call to Present.
    HRESULT hr = d2dContext->EndDraw();
    if (hr != D2DERR_RECREATE_TARGET)
    {
        // The D2DERR_RECREATE_TARGET indicates there has been a problem with the underlying
        // D3D device.  All subsequent rendering will be ignored until the device is recreated.
        // This error will be propagated and the appropriate D3D error will be returned from the
        // swapchain->Present(...) call.   At that point, the sample will recreate the device
        // and all associated resources.  As a result, the D2DERR_RECREATE_TARGET doesn't
        // need to be handled here.
        DX::ThrowIfFailed(hr);
    }
}
```

Использование контекста устройства Direct2D, **GameInfoOverlay** объект инициализирован, этот метод заполняет прямоугольники заголовок и текст с черным, используя кисть фона. Белой кистью в прямоугольнике заголовка рисуется текст "High Score" (Лучший результат), а в прямоугольнике основного текста — строка с обновленным состоянием игры.


Действие прямоугольник обновляется с последующим вызовом [ **GameInfoOverlay::SetAction** ](https://github.com/Microsoft/Windows-universal-samples/blob/5f0d0912214afc1c2a7c7470203933ddb46f7c89/Samples/Simple3DGameDX/cpp/GameInfoOverlay.cpp#L522-L564) из метода на **GameMain** объектом, который предоставляет сведения состояния игры, необходимые по **GameInfoOverlay::SetAction** для определения соответствующего сообщения с проигрывателем, например «Коснитесь, чтобы продолжить».

Наложение для любого заданного состояния выбирается в [ **GameMain::SetGameInfoOverlay** ](https://github.com/Microsoft/Windows-universal-samples/blob/6370138b150ca8a34ff86de376ab6408c5587f5d/Samples/Simple3DGameXaml/cpp/GameMain.cpp#L606-L661) метод следующим образом:

```cpp
void GameMain::SetGameInfoOverlay(GameInfoOverlayState state)
{
    m_gameInfoOverlayState = state;
    switch (state)
    {
    case GameInfoOverlayState::Loading:
        m_uiControl->SetGameLoading();
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

Теперь игра имеет способ сообщить текста сведения проигрывателя, в зависимости от состояния игры и мы предлагаем возможность переключения, отображаемые в них во всей игры.

### <a name="next-steps"></a>Следующие шаги

Следующий раздел называется [Добавление элементов управления](tutorial--adding-controls.md). В нем мы рассмотрим, как игрок взаимодействует с игрой и как вводимые команды меняют состояние игры.



 




