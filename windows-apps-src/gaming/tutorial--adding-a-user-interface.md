---
author: abbycar
title: Добавление пользовательского интерфейса
description: Инструкции по добавлению наложение 2D пользовательского интерфейса в игру UWP на DirectX.
ms.assetid: fa40173e-6cde-b71b-e307-db90f0388485
ms.author: abigailc
ms.date: 10/24/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, uwp, игры, пользовательский интерфейс, directx
ms.localizationpriority: medium
ms.openlocfilehash: 3a82958f01530b84276823ea8d025d292bd664ac
ms.sourcegitcommit: c4d3115348c8b54fcc92aae8e18fdabc3deb301d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/22/2018
ms.locfileid: "5404582"
---
# <a name="add-a-user-interface"></a>Добавление пользовательского интерфейса


Теперь, когда наш игра снабжена его визуальных эффектов трехмерной, пора сосредоточиться на добавление некоторые двухмерные элементы, таким образом, чтобы игру можно отправить отзыв о состоянии игры игроку. Это можно сделать путем добавления простые команды меню и экранные элементы поверх трехмерной графики конвейера выходных данных.

>[!Note]
>Если вы еще не скачали последний код для игры из этого примера, перейдите в раздел [Пример игры Direct3D](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/Simple3DGameDX). Этот пример является частью большой коллекции примеров функций UWP. Указания по скачиванию этого примера приводятся в разделе [Получение примеров UWP из GitHub](https://docs.microsoft.com/windows/uwp/get-started/get-uwp-app-samples).

## <a name="objective"></a>Цель

С помощью Direct2D, добавьте количество графического интерфейса пользователя и поведение наших UWP на базе DirectX игры в том числе:
- Отображения экранных элементов, включая [контроллера движения наблюдения](tutorial--adding-controls.md) boundry прямоугольников
- Меню состояния игры


## <a name="the-user-interface-overlay"></a>Перекрытие пользовательского интерфейса


Существует множество способов для отображения текста и элементов пользовательского интерфейса в игре DirectX, мы добавим фокус на с помощью [Direct2D](https://msdn.microsoft.com/library/windows/apps/dd370990.aspx). Мы будем также использовать [DirectWrite](https://msdn.microsoft.com/library/windows/desktop/dd368038) для текстовых элементов.


Direct2D — это набор двухмерных рисования API используется для рисования двухмерных пиксельных примитивов и эффектов. Когда начиная работу с Direct2D, лучше не усложнять. Сложные макеты и функции интерфейса требуют времени на разработку и заблаговременного планирования. Если в игре нужен сложный пользовательский интерфейс, как в стратегиях и симуляторах, попробуйте использовать XAML.

> [!NOTE]
> Сведения о разработке пользовательского интерфейса в XAML в игре UWP DirectX см. [образец расширения игры](tutorial-resources.md).

Direct2D не предназначен специально для разработки пользовательского интерфейса и макетов, таких как HTML и XAML. Он не предоставляет компоненты пользовательского интерфейса, такие как списки, поля или кнопки. Он также не предоставляет компонентов макета элементов div, таблицы или сетки.


В этом примере игры у нас есть два основных компонента пользовательского интерфейса.
1. Экранных элементов для оценки и элементов управления в игру.
2. Наложение используется для отображения текста состояния игры и параметры, такие как сведений о паузе и параметры запуска уровня.

### <a name="using-direct2d-for-a-heads-up-display"></a>Использование Direct2D для отображения экранных элементов

На следующем рисунке показано отображение экранных элементов в игре для примера. Оно не загромождает экран, позволяя игроку сосредоточиться на перемещении трехмерному миру и стрельбе конечных объектов. Хороший интерфейс и экранные никогда не должны усложнить возможность игроку своевременно реагировать на события в игре.

![Снимок экрана с перекрытием в игре](images/simple-dx-game-ui-overlay.png)

Перекрытие состоит из следующих простых примитивов.
- Текст [**DirectWrite**](https://msdn.microsoft.com/en-us/library/windows/desktop/dd368038) в правом верхнем углу, который сообщает игроку 
    - Успешно попаданий
    - Число снимков, сделанные игроком
    - Время, оставшееся на уровне
    - Номер текущего уровня 
- Два пересечение отрезков, используемый для формирования курсором-перекрестием
- Двух прямоугольников в нижнем углах для [контроллера движения наблюдения](tutorial--adding-controls.md) имеющему скрытые границы. 


Состояние экранных элементов в игре наложения рисуется в методе [**GameHud::Render**](https://github.com/Microsoft/Windows-universal-samples/blob/5f0d0912214afc1c2a7c7470203933ddb46f7c89/Samples/Simple3DGameDX/cpp/GameHud.cpp#L234-L358) [**GameHud**](https://github.com/Microsoft/Windows-universal-samples/blob/5f0d0912214afc1c2a7c7470203933ddb46f7c89/Samples/Simple3DGameDX/cpp/GameHud.h) класса. В этом методе изображение Direct2D, представляющий наш пользовательский Интерфейс обновляется в соответствии с меняющимся числом попаданий, время оставшиеся и уровня номер.

При инициализации игры, мы добавляем `TotalHits()`, `TotalShots()`, и `TimeRemaining()` для [**swprintf_s**](https://docs.microsoft.com/cpp/c-runtime-library/reference/sprintf-s-sprintf-s-l-swprintf-s-swprintf-s-l) буфера и указать формат печати. Затем мы можно рисовать с помощью метода [**DrawText**](https://msdn.microsoft.com/en-us/library/windows/desktop/dd742848) . Мы делаем так же для текущего индикатор уровня рисования пустой для отображения незавершенные уровни как ➀ и заполненного номеров как ➊ для отображения, что определенный уровень была выполнена.


В следующем фрагменте кода проходит через метод **GameHud::Render** процесс 
- Создание растрового изображения с помощью [** ID2D1RenderTarget::DrawBitmap **](https://msdn.microsoft.com/en-us/library/windows/desktop/dd371880)
- Вводится Выкл областях пользовательского интерфейса в прямоугольников с помощью [ **D2D1::RectF**](https://msdn.microsoft.com/en-us/library/windows/desktop/dd368184)
- С помощью **DrawText** создать текстовые элементы

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

Неисправность метод вниз Кроме того, эта часть рисует метод [**GameHud::Render**](https://github.com/Microsoft/Windows-universal-samples/blob/5f0d0912214afc1c2a7c7470203933ddb46f7c89/Samples/Simple3DGameDX/cpp/GameHud.cpp#L320-L358) наши перемещения прямоугольники и огня с [**ID2D1RenderTarget::DrawRectangle**](https://msdn.microsoft.com/library/windows/desktop/dd371902)и перекрестья с помощью двух вызовов к [**ID2D1RenderTarget::DrawLine**](https://msdn.microsoft.com/library/windows/desktop/dd371895).

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

В методе **GameHud::Render** сохраняем логический размер окна игры в `windowBounds` переменной. При этом используется [`GetLogicalSize`](https://github.com/Microsoft/Windows-universal-samples/blob/5f0d0912214afc1c2a7c7470203933ddb46f7c89/Samples/Simple3DGameDX/cpp/Common/DeviceResources.h#L41) метод класса **DeviceResources** . 
```cpp
auto windowBounds = m_deviceResources->GetLogicalSize();
```

 Получение размера окна игры является надлежащая Подготовка программирования пользовательского интерфейса. Размер окна устанавливается в единицах под названием (аппаратно-независимые пиксели), где DIP определяется как 1/96 дюйма. Direct2D масштабирует единицы в реальные пиксели при возникновении отрисовки, при этом с помощью Windows параметр точек на дюйм (точек на ДЮЙМ). Аналогичным образом при рисовании текста в [**DirectWrite**](https://msdn.microsoft.com/en-us/library/windows/desktop/dd368038), укажите DIPs, а не точек для размера шрифта. Значения DIP выражаются числами с плавающей запятой.

 

### <a name="displaying-game-state-info"></a>Отображение сведений о состоянии игры

Помимо отображения экранных элементов образец игры есть перекрытие, представляющее шесть состояний игры. Все состояния компонентов примитив большой черный прямоугольник с текстом для игрока. Прямоугольники для управления перемещением и обзором и перекрестья не рисуются, так как они не активны в этих состояниях.

Наложение создается с использованием класса [**GameInfoOverlay**](https://github.com/Microsoft/Windows-universal-samples/blob/5f0d0912214afc1c2a7c7470203933ddb46f7c89/Samples/Simple3DGameDX/cpp/GameInfoOverlay.h) , позволяя нам для переключения, какой текст отображается в соответствии с состояния игры.

![состояние и действие наложения](images/simple-dx-game-ui-finaloverlay.png)

Наложение разбивается на две части: **состояние** и **действия**. **Состояние** ассемблерного разбивается на прямоугольники **заголовка** и **основного** . В разделе **действий** есть только один прямоугольник. Каждый прямоугольник имеет другой цели.

-   `titleRectangle` содержит текст заголовка.
-   `bodyRectangle` содержит основного текста.
-   `actionRectangle` содержит текст, который сообщает игроку определенное действие.

Игра снабжена шесть состояний, которые могут быть установлены. Состояние игры, передаваемых с помощью **состояния** часть накладываемого слоя. Прямоугольники **состояние** обновляются с помощью следующих состояний, соответствующие методам.

- Загрузка
- Начальное меню "Пуск" и высокой оценкой статистики
- Уровень меню "Пуск"
- Игра приостановлена
- Игра закончена
- Выиграл игру


**Действие** часть накладываемого слоя обновляется с помощью метода [**GameInfoOverlay::SetAction**](https://github.com/Microsoft/Windows-universal-samples/blob/5f0d0912214afc1c2a7c7470203933ddb46f7c89/Samples/Simple3DGameDX/cpp/GameInfoOverlay.cpp#L522-L564) , позволяя задать одно из следующих действий текст.
- «Коснитесь начать новую игру …»
- «Уровень загрузки, подождите …»
- «Коснитесь для продолжения …»
- Нет

> [!NOTE]
> Оба этих метода будет описано далее в разделе [представление состояния игры](#representing-game-state) .

В зависимости от того, что происходит в игре, **состояние** и раздел **действий** корректируются текстовых полей.
Давайте рассмотрим, как инициализируется и рисуется перекрытие в этих шести состояний.

### <a name="initializing-and-drawing-the-overlay"></a>Инициализация и рисование перекрытия

**Шесть состояния** есть несколько общих ресурсов и методы они необходимость, очень похожи.
    - Все они используют черный прямоугольник в центре экрана с фоном.
    - Отображается текст является **заголовком** или **основного** текста.
    - Стиль текста используется Интерфейсный шрифт и рисуется поверх фонового прямоугольника. 


Примере игры используются четыре метода, которые следует учитывать при создании наложения.
 

#### <a name="gameinfooverlaygameinfooverlay"></a>GameInfoOverlay::GameInfoOverlay
[**GameInfoOverlay::GameInfoOverlay**](https://github.com/Microsoft/Windows-universal-samples/blob/5f0d0912214afc1c2a7c7470203933ddb46f7c89/Samples/Simple3DGameDX/cpp/GameInfoOverlay.cpp#L30-L78) конструктор инициализирует перекрытия точечный рисунок поверхность, которая будет использоваться для отображения информации для игрока на обслуживание. Конструктор получает фабрику из объекта [**ID2D1Device**](https://msdn.microsoft.com/library/windows/desktop/hh404478) передаются приложению, который используется для создания [**ID2D1DeviceContext**](https://msdn.microsoft.com/library/windows/desktop/hh404479) , которую можно выводить сам объект перекрытия. [IDWriteFactory::CreateTextFormat](https://msdn.microsoft.com/en-us/library/windows/desktop/dd368203) 


#### <a name="gameinfooverlaycreatedevicedependentresources"></a>GameInfoOverlay::CreateDeviceDependentResources
[**GameInfoOverlay::CreateDeviceDependentResources**](https://github.com/Microsoft/Windows-universal-samples/blob/5f0d0912214afc1c2a7c7470203933ddb46f7c89/Samples/Simple3DGameDX/cpp/GameInfoOverlay.cpp#L82-L104) — это наш метод для создания кисти, которые будут использоваться для рисования наш текст. Чтобы сделать это, мы получите объект [**ID2D1DeviceContext2**](https://msdn.microsoft.com/en-us/library/windows/desktop/dn890789) , который позволяет создавать и рисования геометрии, а также функции, такие как рукописный ввод и градиент сетки отрисовки. Затем мы создадим ряд цветной кисти, с помощью [**ID2D1SolidColorBrush**](https://msdn.microsoft.com/en-us/library/windows/desktop/dd372207) для рисования элементов пользовательского интерфейса folling.
- Черную кисть для фона прямоугольник
- Белую кисть для текст состояния
- Оранжевую кисть для текста уведомлений

#### <a name="deviceresourcessetdpi"></a>DeviceResources::SetDpi
Метод [**DeviceResources::SetDpi**](https://github.com/Microsoft/Windows-universal-samples/blob/5f0d0912214afc1c2a7c7470203933ddb46f7c89/Samples/Simple3DGameDX/cpp/Common/DeviceResources.cpp#L514-L527) устанавливает точек на дюйм окна. Этот метод вызывается, когда меняется DPI и должен быть переопределяется, что происходит при изменении размера окна игры. После обновления DPI, этот метод также вызывает[**DeviceResources::CreateWindowSizeDependentResources**](https://github.com/Microsoft/Windows-universal-samples/blob/5f0d0912214afc1c2a7c7470203933ddb46f7c89/Samples/Simple3DGameDX/cpp/Common/DeviceResources.cpp#L214-L487) , чтобы убедиться в том, что необходимые ресурсы создаются заново при каждом изменении размера окна.


#### <a name="gameinfooverlaycreatewindowssizedependentresources"></a>GameInfoOverlay::CreateWindowsSizeDependentResources
Метод [**GameInfoOverlay::CreateWindowsSizeDependentResources**](https://github.com/Microsoft/Windows-universal-samples/blob/5f0d0912214afc1c2a7c7470203933ddb46f7c89/Samples/Simple3DGameDX/cpp/GameInfoOverlay.cpp#L108-L225) является, где все рисование выполняется. Ниже приведен краткие сведения о методе действия.
- Три прямоугольника создаются раздел выключение текста пользовательского интерфейса для текста **заголовка**, **текст**и **Действие** .
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

- Создается точечный рисунок `m_levelBitmap`, учитывая текущее разрешение с помощью **CreateBitmap**.
- `m_levelBitmap` устанавливается как наш 2D отрисовки с помощью [**ID2D1DeviceContext::SetTarget**](https://msdn.microsoft.com/en-us/library/windows/desktop/hh404533)целевого объекта.
- Растровое изображение удаляется с каждого пикселя внесенные черным цветом с помощью [**ID2D1RenderTarget::Clear**](https://msdn.microsoft.com/en-us/library/windows/desktop/dd371772).
- [**ID2D1RenderTarget::BeginDraw**](https://msdn.microsoft.com/en-us/library/windows/desktop/dd371768) вызывается для запуска рисования. 
- **DrawText** вызывается для рисования текста, хранящиеся в `m_titleString`, `m_bodyString`, и `m_actionString` в соответствии с новым прямоугольнике, используя соответствующие **ID2D1SolidColorBrush**.
- [**ID2D1RenderTarget::EndDraw**](ID2D1RenderTarget::EndDraw) вызывается, чтобы остановить все операции рисования на `m_levelBitmap`.
- Другой точечный рисунок создается с использованием **CreateBitmap** с именем `m_tooSmallBitmap` для использования в качестве резервного, отображение только если конфигурация экрана слишком мал для игры.
- Повторите процесс для рисования на `m_levelBitmap` для `m_tooSmallBitmap`, на этот раз только графические строка `Paused` в тексте.




Теперь все, что нам нужно, шесть методов для заполнения текст из наших состояний перекрытия шесть!

### <a name="representing-game-state"></a>Представление состояния игры


Каждый из состояний шесть перекрытия в игре имеет соответствующий метод в объекте **GameInfoOverlay** . Эти методы рисуют вариант перекрытия для явной передачи игроку информации об игре. Это взаимодействие представляется со строкой **заголовка** и **основного** . Пример уже настроены ресурсы и макет для такой информации, когда он был инициализирован и с помощью метода [**GameInfoOverlay::CreateDeviceDependentResources**](https://github.com/Microsoft/Windows-universal-samples/blob/5f0d0912214afc1c2a7c7470203933ddb46f7c89/Samples/Simple3DGameDX/cpp/GameInfoOverlay.cpp#L82-L104) , его нужно только задать строки состояния.

**Состояние** часть накладываемого слоя задается с помощью вызова к одному из следующих методов.

Состояния игры | Метод set состояния | Поля состояния
:----- | :------- | :---------
Загрузка | [GameInfoOverlay::SetGameLoading](https://github.com/Microsoft/Windows-universal-samples/blob/5f0d0912214afc1c2a7c7470203933ddb46f7c89/Samples/Simple3DGameDX/cpp/GameInfoOverlay.cpp#L254-L306) |**Title**</br>Загрузка ресурсов </br>**Body**</br> Постепенно выводит «.» подразумевает загрузки действий.
Начальное меню "Пуск" и высокой оценкой статистики | [GameInfoOverlay::SetGameStats](https://github.com/Microsoft/Windows-universal-samples/blob/5f0d0912214afc1c2a7c7470203933ddb46f7c89/Samples/Simple3DGameDX/cpp/GameInfoOverlay.cpp#L310-L354) |**Title**</br>Максимальный результат</br> **Body**</br> Уровни завершения # </br>Общее количество баллов #</br>Общее количество снимков #
Уровень меню "Пуск" | [GameInfoOverlay::SetLevelStart](https://github.com/Microsoft/Windows-universal-samples/blob/5f0d0912214afc1c2a7c7470203933ddb46f7c89/Samples/Simple3DGameDX/cpp/GameInfoOverlay.cpp#L413-L471) |**Title**</br>Level #</br>**Body**</br>Описание уровня цели.
Игра приостановлена | [GameInfoOverlay::SetPause](https://github.com/Microsoft/Windows-universal-samples/blob/5f0d0912214afc1c2a7c7470203933ddb46f7c89/Samples/Simple3DGameDX/cpp/GameInfoOverlay.cpp#L475-L502) |**Title**</br>Игра приостановлена</br>**Body**</br>Нет
Игра закончена | [GameInfoOverlay::SetGameOver](https://github.com/Microsoft/Windows-universal-samples/blob/5f0d0912214afc1c2a7c7470203933ddb46f7c89/Samples/Simple3DGameDX/cpp/GameInfoOverlay.cpp#L358-L409) |**Title**</br>Игра закончена</br> **Body**</br> Уровни завершения # </br>Общее количество баллов #</br>Общее количество снимков #</br>Уровни завершения #</br>Высокая оценка #
Выиграл игру | [GameInfoOverlay::SetGameOver](https://github.com/Microsoft/Windows-universal-samples/blob/5f0d0912214afc1c2a7c7470203933ddb46f7c89/Samples/Simple3DGameDX/cpp/GameInfoOverlay.cpp#L358-L409) |**Title**</br>Вы ВЫИГРАЛ!</br> **Body**</br> Уровни завершения # </br>Общее количество баллов #</br>Общее количество снимков #</br>Уровни завершения #</br>Высокая оценка #




С помощью метода [**GameInfoOverlay::CreateWindowSizeDependentResources**](https://github.com/Microsoft/Windows-universal-samples/blob/5f0d0912214afc1c2a7c7470203933ddb46f7c89/Samples/Simple3DGameDX/cpp/GameInfoOverlay.cpp#L117-L134) объявляются три прямоугольные области, которые соответствуют определенным регионам наложения.



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

Используя контекст устройства Direct2D, инициализированного объекта **GameInfoOverlay** , этот метод заполняет прямоугольники заголовка и основного с черным с помощью кисти фона. Белой кистью в прямоугольнике заголовка рисуется текст "High Score" (Лучший результат), а в прямоугольнике основного текста — строка с обновленным состоянием игры.


Прямоугольник действий обновляется последующим вызовом [**GameInfoOverlay::SetAction**](https://github.com/Microsoft/Windows-universal-samples/blob/5f0d0912214afc1c2a7c7470203933ddb46f7c89/Samples/Simple3DGameDX/cpp/GameInfoOverlay.cpp#L522-L564) из метода в объекте **GameMain** , который предоставляет данные о состоянии игры, необходимые **GameInfoOverlay::SetAction** для определения сообщение, справа игрок, например, «продолжить».

Перекрытие для состояния выбирается в методе [**GameMain::SetGameInfoOverlay**](https://github.com/Microsoft/Windows-universal-samples/blob/6370138b150ca8a34ff86de376ab6408c5587f5d/Samples/Simple3DGameXaml/cpp/GameMain.cpp#L606-L661) следующим образом:

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

Теперь у приложения есть возможность игроку текст в зависимости от состояния игры, и у нас есть способ переключения, отображаемых на них во время игры.

### <a name="next-steps"></a>Следующие шаги

Следующий раздел называется [Добавление элементов управления](tutorial--adding-controls.md). В нем мы рассмотрим, как игрок взаимодействует с игрой и как вводимые команды меняют состояние игры.



 




