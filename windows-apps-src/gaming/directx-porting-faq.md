---
title: Вопросы и ответы по переносу на DirectX 11
description: Ответы на часто встречающиеся вопросы о переносе игр на универсальную платформу Windows (UWP).
ms.assetid: 79c3b4c0-86eb-5019-97bb-5feee5667a2d
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp, игры, directx 11
ms.localizationpriority: medium
ms.openlocfilehash: e99aec682ee02463fc282a70a183776d13dd58a8
ms.sourcegitcommit: b52ddecccb9e68dbb71695af3078005a2eb78af1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74258525"
---
# <a name="directx-11-porting-faq"></a>Вопросы и ответы по переносу на DirectX 11




Ответы на часто встречающиеся вопросы о переносе игр на универсальную платформу Windows (UWP).

## <a name="is-porting-my-game-going-to-be-a-set-of-search-and-replace-operations-on-api-methods-or-do-i-need-to-plan-for-a-more-thoughtful-porting-process"></a>Будет ли процесс переноса игры заключаться в поиске и замене методов API, или следует рассчитывать на более сложный процесс переноса?


Direct3D 11 значительно обновлен по сравнению с Direct3D 9. Есть несколько сдвигов парадигм, включая отдельные API для виртуализированного графического адаптера и его контекста, а также новый уровень полиморфизма для ресурсов устройств. В основном ваша игра может использовать графическое оборудование прежним способом, но вам понадобится изучить новую архитектуру API Direct3D 11 и обновить каждую часть графического кода, чтобы использовать правильные компоненты API. См. раздел [Принципы и вопросы переноса](porting-considerations.md).

## <a name="what-is-the-new-device-context-for-am-i-supposed-to-replace-my-direct3d-9-device-with-the-direct3d-11-device-the-device-context-or-both"></a>Для чего нужен новый контекст устройства? Нужно ли заменить устройство Direct3D 9 устройством Direct3D 11, заменить контекст устройства или и то, и другое?


Устройство Direct3D теперь используется для создания ресурсов в видеопамяти, а контекст устройства применяется для установки состояния конвейера и генерирования команд прорисовки. Подробнее: [Каковы наиболее важные изменения по сравнению с Direct3D 9?](understand-direct3d-11-1-concepts.md)

##  <a name="do-i-have-to-update-my-game-timer-for-uwp"></a>Нужно ли обновлять таймер игры для UWP?


[**QueryPerformanceCounter**](https://docs.microsoft.com/windows/desktop/api/profileapi/nf-profileapi-queryperformancecounter), вместе с [**куериперформанцефрекуенци**](https://docs.microsoft.com/windows/desktop/api/profileapi/nf-profileapi-queryperformancefrequency), по-прежнему является лучшим способом реализации таймера игры для приложений UWP.

Помните о нюансе относительно таймеров и жизненного цикла приложения UWP. Приостановка и возобновление отличаются от перезапуска классического приложения, так как игра возобновляется с того момента, на котором была приостановлена. Если прошло длительное время, например несколько недель, некоторые реализации таймеров могут работать некорректно. Вы можете использовать события жизненного цикла приложения, чтобы сбросить таймер при возобновлении игры.

Игры, которые по-прежнему используют инструкцию RDTSC, необходимо обновить. См. раздел [Расписание игры и многоядерный процессор](https://docs.microsoft.com/windows/desktop/DxTechArts/game-timing-and-multicore-processors).

## <a name="my-game-code-is-based-on-d3dx-and-dxut-is-there-anything-available-that-can-help-me-migrate-my-code"></a>Код моей игры основан на D3DX и DXUT. Есть ли какие-либо способы переноса моего кода?


Сообщество [DirectX Tool Kit (DirectXTK)](https://github.com/Microsoft/DirectXTK) предлагает вспомогательные классы для использования с Direct3D 11.

##  <a name="how-do-i-maintain-code-paths-for-the-desktop-and-the-microsoft-store"></a>Разделы справки поддерживать пути кода для настольных систем и Microsoft Store?


Серия статей Чак Валбаурн, озаглавленная [двойные приемы кодирования для игр](https://blogs.msdn.com/b/chuckw/archive/2012/09/17/dual-use-coding-techniques-for-games.aspx) , предлагает руководство по совместному использованию кода между настольным компьютером и Microsoft Store путями кода.

##  <a name="how-do-i-load-image-resources-in-my-directx-uwp-app"></a>Как загружать ресурсы изображений в приложение UWP с DirectX?


Существует два пути API для загрузки изображений.

-   Конвейер содержимого преобразует изображения в DDS-файлы, используемые в качестве ресурсов текстур Direct3D. См. раздел [Использование трехмерных ресурсов в игре или приложении](https://docs.microsoft.com/visualstudio/designers/using-3-d-assets-in-your-game-or-app?view=vs-2015).
-   Для загрузки изображений в различных форматах можно использовать [Компонент обработки изображений Windows](https://docs.microsoft.com/windows/desktop/wic/-wic-lh). Его также можно применять для точечных рисунков Direct2D и ресурсов текстур Direct3D.

Также можно воспользоваться средствами загрузки DDSTextureLoader и WICTextureLoader из [DirectXTK](https://github.com/Microsoft/DirectXTK) или [DirectXTex](https://github.com/Microsoft/DirectXTex).

## <a name="where-is-the-directx-sdk"></a>Где находится пакет SDK для DirectX?


Пакет SDK DirectX включен в пакет Windows SDK. Самый недавний пакет SDK DirectX, отдельный от пакета Windows SDK, был выпущен в июне 2010 года. Примеры Direct3D можно найти в коллекции кода вместе с другими примерами приложений для Windows.

## <a name="what-about-directx-redistributables"></a>Что нового в распространяемых компонентах DirectX?


Подавляющее большинство компонентов пакета Windows SDK уже включены в поддерживаемые версии ОС или не имеют компонента DLL (например, DirectXMath). Все компоненты API Direct3D, которые могут использовать приложения UWP, будут доступны для вашей игры, и вам не понадобится распространять их.

Классические приложения Win32 по-прежнему используют DirectSetup, поэтому если вы также обновляете классическую версию вашей игры, см. раздел [Развертывание Direct3D 11 для разработчиков игр](https://docs.microsoft.com/windows/desktop/direct3darticles/direct3d11-deployment).

## <a name="is-there-any-way-i-can-update-my-desktop-code-to-directx-11-before-moving-away-from-effects"></a>Есть ли способ обновить код для рабочего стола до DirectX 11 перед прекращением использования эффектов?


См. раздел [Эффекты для обновления Direct3D 11](https://github.com/Microsoft/FX11). Эффекты 11 помогают удалить зависимости от устаревших заголовков DirectX SDK. Их можно использовать в качестве помощи при переносе и только с классическими приложениями.

##  <a name="is-there-a-path-for-porting-my-directx-8-game-to-uwp"></a>Существует ли путь переноса игры с DirectX 8 в UWP?


Да.

-   См. раздел [Преобразование в Direct3D 9](https://docs.microsoft.com/windows/desktop/direct3d9/converting-to-directx-9).
-   Убедитесь, что в вашей игре нет остатков фиксированного конвейера. См. раздел [Устаревшие компоненты](https://docs.microsoft.com/windows/desktop/direct3d10/d3d10-graphics-programming-guide-api-features-deprecated).
-   Затем воспользуйтесь путем переноса DirectX 9: [Портирование из D3D 9 в UWP](walkthrough--simple-port-from-direct3d-9-to-11-1.md).

##  <a name="can-i-port-my-directx-10-or-11-game-to-uwp"></a>Можно ли портировать игру с DirectX 10 или 11 на UWP?


Классические игры с DirectX 10.x и 11 легко портируются на UWP. См. раздел [Миграция в Direct3D 11](https://docs.microsoft.com/windows/desktop/direct3d11/d3d11-programming-guide-migrating).

## <a name="how-do-i-choose-the-right-display-device-in-a-multi-monitor-system"></a>Как выбрать правильный монитор в системе с несколькими мониторами?


Пользователь определяет, на каком экране будет отображаться ваше приложение. Позвольте Windows предоставить правильный адаптер, вызвав [**D3D11CreateDevice**](https://docs.microsoft.com/windows/desktop/api/d3d11/nf-d3d11-d3d11createdevice) со значением первого параметра **nullptr**. Затем получите [**интерфейс IDXGIDevice**](https://docs.microsoft.com/windows/desktop/api/dxgi/nn-dxgi-idxgidevice) устройства, вызовите [**GetAdapter**](https://docs.microsoft.com/windows/desktop/api/dxgi/nf-dxgi-idxgidevice-getadapter) и используйте адаптер DXGI, чтобы создать цепочку буферов.

## <a name="how-do-i-turn-on-antialiasing"></a>Как включить сглаживание?


Сглаживание (множественная дискретизация) включено, когда вы создаете устройство Direct3D. Перечислите поддержку многообъектной выборки, вызвав [**чеккмултисамплекуалитилевелс**](https://docs.microsoft.com/windows/desktop/api/d3d11/nf-d3d11-id3d11device-checkmultisamplequalitylevels), а затем задайте параметры мультисэмплинга в [ **\_образца\_"DESC**](https://docs.microsoft.com/windows/desktop/api/dxgicommon/ns-dxgicommon-dxgi_sample_desc) " при вызове [**креатесурфаце**](https://docs.microsoft.com/windows/desktop/api/dxgi/nf-dxgi-idxgidevice-createsurface).

## <a name="my-game-renders-using-multithreading-andor-deferred-rendering-what-do-i-need-to-know-for-direct3d-11"></a>При прорисовке моей игры используется многопоточность и (или) отложенная прорисовка. Что мне нужно знать, чтобы использовать Direct3D 11?


Для начала посетите страницу [Введение в многопоточность в Direct3D 11](https://docs.microsoft.com/windows/desktop/direct3d11/overviews-direct3d-11-render-multi-thread-intro). Список ключевых отличий см. в разделе [Разница между потоками в различных версиях Direct3D](https://docs.microsoft.com/windows/desktop/direct3d11/overviews-direct3d-11-render-multi-thread-differences). Обратите внимание, что отложенная прорисовка использует *отложенный контекст* устройства вместо *немедленного контекста*.

## <a name="where-can-i-read-more-about-the-programmable-pipeline-since-direct3d-9"></a>Где можно найти дополнительную информацию о программируемом конвейере начиная с Direct3D 9?


Просмотрите следующие разделы.

-   [Инструкции по программированию для HLSL](https://docs.microsoft.com/windows/desktop/direct3dhlsl/dx-graphics-hlsl-pguide)
-   [Часто задаваемые вопросы о Direct3D 10](https://docs.microsoft.com/windows/desktop/DxTechArts/direct3d10-frequently-asked-questions)

## <a name="what-should-i-use-instead-of-the-x-file-format-for-my-models"></a>Что использовать для моделей вместо формата файлов .x?


Хотя у нас нет официальной замены формата файлов .x, во многих примерах использован формат SDKMesh. Visual Studio также включает [конвейер содержимого](https://docs.microsoft.com/visualstudio/designers/using-3-d-assets-in-your-game-or-app?view=vs-2015), который компилирует несколько популярных форматов в CMO-файлы, которые можно загрузить с кодом из начального набора Visual Studio 3D или с помощью [DirectXTK](https://github.com/Microsoft/DirectXTK).

## <a name="how-do-i-debug-my-shaders"></a>Как реализовать отладку шейдеров?


Microsoft Visual Studio 2015 включает средства диагностики для графики DirectX. См. раздел [Отладка графики DirectX](https://docs.microsoft.com/visualstudio/debugger/visual-studio-graphics-diagnostics?view=vs-2015).

##  <a name="what-is-the-direct3d-11-equivalent-for-x-function"></a>Какая функция в Direct3D 11 эквивалентна функции *x*?


См. [сопоставление функций](feature-mapping.md#function-mapping) в разделе «Сопоставление компонентов DirectX 9 и API DirectX 11».

##  <a name="what-is-the-dxgi_format-equivalent-of-y-surface-format"></a>Что такое формат\_а DXGI, эквивалентный формату поверхности *y* ?


См. [сопоставление форматов поверхности](feature-mapping.md#surface-format-mapping) в разделе «Сопоставление компонентов DirectX 9 и API DirectX 11».

 

 




