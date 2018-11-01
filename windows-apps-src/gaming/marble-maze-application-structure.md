---
author: eliotcowley
title: Структура приложения Marble Maze
description: Структура приложения универсальной платформы Windows (UWP) на DirectX отличается от обычного классического приложения.
ms.assetid: 6080f0d3-478a-8bbe-d064-73fd3d432074
ms.author: elcowle
ms.date: 09/08/2017
ms.topic: article
keywords: windows 10, uwp, игры, пример, directx, структура
ms.localizationpriority: medium
ms.openlocfilehash: 1272200bf128443c82807aec9df5559f207819e1
ms.sourcegitcommit: cd00bb829306871e5103db481cf224ea7fb613f0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/31/2018
ms.locfileid: "5872526"
---
# <a name="marble-maze-application-structure"></a>Структура приложения Marble Maze




Структура приложения универсальной платформы Windows (UWP) на базе DirectX отличается от обычного классического приложения. Вместо работы с такими типами дескрипторов, как [HWND](https://msdn.microsoft.com/library/windows/desktop/aa383751), и функциями, подобными [CreateWindow](https://msdn.microsoft.com/library/windows/desktop/ms632679), среда выполнения Windows предоставляет такие интерфейсы, как [Windows::UI::Core::ICoreWindow](https://msdn.microsoft.com/library/windows/apps/br208296), благодаря чему вы можете разрабатывать приложения UWP более современным объектно-ориентированным образом. В этом разделе документации показана структура программного кода приложения Marble Maze.

> [!NOTE]
> Пример кода, соответствующего этому документу, см. в [примере игры DirectX Marble Maze](http://go.microsoft.com/fwlink/?LinkId=624011).

 
## 
В этом документе рассматриваются следующие основные моменты создания структуры программного кода игры.

-   На этапе инициализации задайте компоненты среды выполнения и библиотеки, которые используются игрой, и загрузите ресурсы для игры.
-   Приложения UWP должны начинать обработку событий в течение 5 секунд после запуска. Поэтому во время загрузки приложения загружайте только необходимые ресурсы. Ресурсы большого объема в играх должны загружаться в фоновом режиме с отображением хода выполнения.
-   В игровом цикле отвечайте на события Windows, считывайте пользовательский ввод, обновляйте объекты сцены и выполняйте ее отрисовку.
-   Для ответа на события окон используйте обработчики событий. (Они заменяют окна сообщений, открываемые классическими приложениями для Windows.)
-   Для управления потоком и порядком игровой логики используйте конечный автомат.

##  <a name="file-organization"></a>Организация файлов


Некоторые компоненты Marble Maze можно использовать в других играх с минимальной доработкой. Вы можете адаптировать организацию и общее назначение этих файлов для вашей игры. В следующей таблице кратко описаны важные файлы исходного кода.

| Файлы                                      | Описание                                                                                                                                                                          |
|--------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| App.h, App.cpp               | Определяют классы **App** и **DirectXApplicationSource**, в которых инкапсулируется представление приложения (окно, поток и события).                                                     |
| Audio.h, Audio.cpp                         | Определяют класс **Audio**, который управляет звуковыми ресурсами                                                                                                                          |
| BasicLoader.h, BasicLoader.cpp             | Определяют класс **BasicLoader**, который предоставляет служебные методы, упрощающие загрузку текстур, сеток и шейдеров                                                                  |
| BasicMath.h                                | Определяет структуры и функции для работы с векторными и матричными данными и вычислениями. Многие из этих функций совместимы с типами шейдеров HLSL.                     |
| BasicReaderWriter.h, BasicReaderWriter.cpp | Определяют класс **BasicReaderWriter**, который использует среду выполнения Windows для чтения и записи файловых данных в приложение UWP                                                                    |
| BasicShapes.h, BasicShapes.cpp             | Определяют класс **BasicShapes**, который предоставляет служебные методы для создания основных фигур (кубов, сфер и т.п.). (Эти файлы не используются в реализации Marble Maze). |                                                                                  |
| Camera.h, Camera.cpp                       | Определяют класс **Camera**, который предоставляет положение и ориентацию камеры                                                                                               |
| Collision.h, Collision.cpp                 | Обрабатывают столкновения шарика с другими объектами (в том числе со стенами лабиринта)                                                                                                       |
| DDSTextureLoader.h, DDSTextureLoader.cpp   | Определяют функцию **CreateDDSTextureFromMemory**, которая загружает текстуры в формате DDS из буфера памяти.                                                              |
| DirectXHelper.h             | Определяет вспомогательные функции DirectX, используемые во многих приложениях UWP DirectX.                                                                            |
| LoadScreen.h, LoadScreen.cpp               | Определяют класс **LoadScreen**, который отображает экран загрузки во время инициализации приложения.                                                                                         |
| MarbleMazeMain.h, MarbleMazeMain.cpp               | Определяют класс **MarbleMazeMain**, который управляет игровыми ресурсами и определяет большую часть игровой логики.                                                                          |
| MediaStreamer.h, MediaStreamer.cpp         | Определяют класс **MediaStreamer**, который с помощью Media Foundation управляет звуковыми ресурсами в игре                                                                            |
| PersistentState.h, PersistentState.cpp     | Определяют класс **PersistentState**, который считывает примитивные типы данных из резервного хранилища и записывает их в резервное хранилище                                                                      |
| Physics.h, Physics.cpp                     | Определяют класс **Physics**, в котором реализуется физика взаимодействия шарика и лабиринта                                                                              |
| Primitives.h                               | Определяет геометрические типы, используемые в игре                                                                                                                                   |
| SampleOverlay.h, SampleOverlay.cpp         | Определяют класс **SampleOverlay**, предоставляющий общие данные и операции для двухмерной графики и пользовательского интерфейса.                                                                               |
| SDKMesh.h, SDKMesh.cpp                     | Определяют класс **SDKMesh**, который загружает и отрисовывает сетки в формате SDK Mesh (.sdkmesh).                                                                                |
| StepTimer.h               | Определяют класс **StepTimer**, который позволяет легко получать значения общего и затраченного времени.
| UserInterface.h, UserInterface.cpp         | Определяют функции пользовательского интерфейса (системы меню, таблицы результатов и т.п.).                                                                        |

 

##  <a name="design-time-versus-run-time-resource-formats"></a>Форматы ресурсов времени разработки и времени выполнения


Для более эффективной загрузки игровых ресурсов по возможности используйте форматы времени выполнения вместо форматов времени разработки.

Формат *времени разработки* используется при разработке ресурса. Обычно при создании трехмерной графики дизайнеры работают с форматами времени разработки. Некоторые форматы времени разработки имеют текстовый вид, и их можно изменять в любом текстовом редакторе. Форматы времени разработки могут быть подробными и содержать больше информации, чем необходимо для игры. Формат *времени выполнения* — это двоичный формат, который считывается игрой. Форматы времени выполнения обычно более компактны и более эффективны в загрузке, чем соответствующие форматы времени разработки. Поэтому в большинстве игр во время выполнения используются ресурсы времени выполнения.

Ваша игра может непосредственно считывать формат времени разработки, но использование отдельного формата времени выполнения дает некоторые преимущества. Поскольку форматы времени выполнения часто более компактны, им требуется меньше места на диске и меньше времени на передачу по сети. Кроме того, форматы времени выполнения часто представляются структурами данных, сопоставленными с памятью. Такие структуры загружаются в память намного быстрее, чем, например, текстовый XML-файл. Наконец, отдельные форматы времени выполнения обычно имеют двоичную кодировку, и пользователю сложнее их изменить.

Примером ресурсов, для которых используются различные форматы времени разработки и времени выполнения, являются шейдеры HLSL. В Marble Maze используется формат HLSL во время разработки и формат CSO во время выполнения. HLSL-файл содержит исходный код шейдера, а CSO-файл содержит соответствующий байт-код шейдера. Если преобразовать HLSL-файлы в автономном режиме и включить в игру CSO-файлы, то устраняется необходимость преобразования HLSL-файлов исходного кода в байт-код при загрузке игры.

Для наглядности в проект Marble Maze многие ресурсы включены и в формате времени разработки, и в формате времени выполнения, но в исходном проекте вашей игры вам нужно поддерживать только форматы времени разработки, поскольку их можно в нужный момент преобразовать в форматы времени выполнения. В этой документации показано, как преобразовать форматы времени разработки в форматы времени выполнения.

##  <a name="application-life-cycle"></a>Жизненный цикл приложения


В Marble Maze применяется типичный жизненный цикл приложения UWP. Дополнительные сведения о жизненном цикле приложения UWP см. в разделе [Жизненный цикл приложения](https://msdn.microsoft.com/library/windows/apps/mt243287).

Во время инициализации игры UWP обычно инициализируются компоненты времени выполнения, такие как Direct3D, Direct2D и используемые библиотеки ввода, звука и физики. Также загружаются игровые ресурсы, которые необходимы перед началом игры. Такая инициализация выполняется один раз за игровой сеанс.

После инициализации игры обычно запускают *игровой цикл*. Игры в этом цикле обычно выполняют четыре действия: обработку событий Windows, сбор ввода, обновление объектов сцены и отрисовку сцены. Во время обновления сцены игра может применять текущее состояние ввода к объектам сцены и имитировать физические события, такие как столкновения объектов. Игра также может выполнять другие операции, например воспроизводить звуковые эффекты или отправлять данные по сети. Во время отрисовки сцены игра берет ее текущее состояние и выводит его на дисплей. В следующих разделах эти операции рассматриваются более подробно.

##  <a name="adding-to-the-template"></a>Добавление в шаблон


Шаблон **Приложение DirectX11 (универсальное приложение Windows)** создает основное окно, в котором можно выполнять отрисовку с помощью Direct3D. Этот шаблон включает также класс **DeviceResources**, создающий все ресурсы устройства Direct3D, которые необходимы для отрисовки трехмерного содержимого в приложении UWP.

Класс **App** создает объект класса **MarbleMazeMain**, запускает загрузку ресурсов, выполняет цикл для обновления таймера и вызывает метод **MarbleMazeMain::Render** в каждом кадре. Каждый из методов **App::OnWindowSizeChanged**, **App::OnDpiChanged** и **App::OnOrientationChanged** вызывает метод **MarbleMazeMain::CreateWindowSizeDependentResources**, а метод **App::Run** вызывает методы **MarbleMazeMain::Update** и **MarbleMazeMain::Render**.

В следующем примере показано, где метод **App::SetWindow** создает объект класса **MarbleMazeMain**. Класс **DeviceResources** передается этому методу, и он может использовать объекты Direct3D для отрисовки.

```cpp
    m_main = std::unique_ptr<MarbleMazeMain>(new MarbleMazeMain(m_deviceResources));
```

Класс **App** также запускает загрузку отложенных ресурсов для игры. Более подробные сведения см. в следующем разделе.

Кроме того, класс **App** настраивает обработчики для событий [CoreWindow](https://docs.microsoft.com/uwp/api/windows.ui.core.corewindow). Когда вызываются обработчики для этих событий, они передают входные данные классу **MarbleMazeMain**.

## <a name="loading-game-assets-in-the-background"></a>Загрузка ресурсов игры в фоновом режиме


Чтобы обеспечить игре возможность отвечать на события окон в течение 5 секунд после запуска, рекомендуется загружать ресурсы игры асинхронно, или в фоновом режиме. Пока ресурсы загружаются в фоновом режиме, игра может отвечать на события окон.

> [!NOTE]
> Вы также можете показать главное меню, когда оно будет готово, а загрузку остальных ресурсов продолжать в фоновом режиме. Если пользователь выбирает из меню какой-либо пункт, когда загружены еще не все ресурсы, вы можете показать, что ресурсы сцены еще загружаются (например, отобразить индикатор выполнения).

 

Даже если ваша игра содержит относительно мало ресурсов, рекомендуется загружать их асинхронно по двум причинам. Во-первых, сложно гарантировать, что все ресурсы будут быстро загружаться на всех устройствах во всех конфигурациях. Во-вторых, реализация асинхронной загрузки на ранних этапах разработки обеспечит возможность масштабирования кода по мере добавления функций.

Асинхронная загрузка ресурсов начинается с метода **App::Load**. Этот метод использует класс [task](https://docs.microsoft.com/cpp/parallel/concrt/reference/task-class) для загрузки ресурсов игры в фоновом режиме.

```cpp
    task<void>([=]()
    {
        m_main->LoadDeferredResources(true, false);
    });
```

В классе **MarbleMazeMain** определяется флаг *m\_deferredResourcesReady*, указывающий на завершение асинхронной загрузки. Метод **MarbleMazeMain::LoadDeferredResources** загружает ресурсы игры, а затем устанавливает этот флаг. На этапах обновления (**MarbleMazeMain::Update**) и отрисовки (**MarbleMazeMain::Render**) приложение проверяет состояние этого флага. Если флаг установлен, то игра продолжает работу обычным образом. Если флаг еще не установлен, то игра отображает экран загрузки.

Дополнительные сведения об асинхронном программировании для приложений UWP см. в разделе [Асинхронное программирование на языке C++](https://docs.microsoft.com/windows/uwp/threading-async/asynchronous-programming-in-cpp-universal-windows-platform-apps).

> [!TIP]
> Если вы создаете игровой код, входящий в библиотеку среды выполнения Windows C++ (то есть библиотеку DLL), ознакомьтесь со статьей [Создание асинхронных операций на C++ для приложений UWP](https://docs.microsoft.com/cpp/parallel/concrt/creating-asynchronous-operations-in-cpp-for-windows-store-apps), чтобы узнать, как создавать асинхронные операции, которые могут обрабатываться приложениями и другими библиотеками.

 

## <a name="the-game-loop"></a>Игровой цикл


Метод **App::Run** запускает главный игровой цикл (**MarbleMazeMain::Update**). Этот метод вызывается в каждом кадре.

Чтобы отделить код представлений и окон от игрового кода, мы реализовали метод **App::Run** для перенаправления вызовов обновления и отрисовки в объект **MarbleMazeMain**.

В следующем примере показан метод **App::Run**, в который входит главный игровой цикл. Игровой цикл обновляет переменные общего времени и времени кадра, а затем обновляет и отрисовывает сцену. Тем самым можно также гарантировать отрисовку содержимого только в случае видимости окна.

```cpp
void App::Run()
{
    while (!m_windowClosed)
    {
        if (m_windowVisible)
        {
            CoreWindow::GetForCurrentThread()->Dispatcher->
                ProcessEvents(CoreProcessEventsOption::ProcessAllIfPresent);

            m_main->Update();

            if (m_main->Render())
            {
                m_deviceResources->Present();
            }
        }
        else
        {
            CoreWindow::GetForCurrentThread()->Dispatcher->
                ProcessEvents(CoreProcessEventsOption::ProcessOneAndAllPending);
        }
    }

    // The app is exiting so do the same thing as if the app were being suspended.
    m_main->OnSuspending();

#ifdef _DEBUG
    // Dump debug info when exiting.
    DumpD3DDebug();
#endif //_DEGBUG
}
```

## <a name="the-state-machine"></a>Конечный автомат


В играх обычно используется *конечный автомат* (*КА*) для управления потоком и порядком игровой логики. Конечный автомат содержит заданное число состояний и позволяет переходить между ними. Обычно конечный автомат начинается с *исходного* состояния, переходит в одно или несколько *промежуточных* состояний и может завершаться в *конечном* состоянии.

Конечный автомат часто используется в игровом цикле, чтобы выполнять логику, соответствующую текущему игровому состоянию. В Marble Maze определяется перечисление **GameState**, где определены все возможные состояния игры.

```cpp
enum class GameState
{
    Initial,
    MainMenu,
    HighScoreDisplay,
    PreGameCountdown,
    InGameActive,
    InGamePaused,
    PostGameResults,
};
```

Например, состояние **MainMenu** определяет, что отображается главное меню, а игра неактивна. Состояние **InGameActive**, напротив, определяет, что игра активна, а меню не отображается. В классе **MarbleMazeMain** определяется переменная-член **m\_gameState** для хранения активного состояния игры.

В методах **MarbleMazeMain::Update** и **MarbleMazeMain::Render** с помощью операторов switch выполняется логика для текущего состояния. В следующем примере показано, как выглядит оператор switch для метода **MarbleMazeMain::Update** (подробности опущены для наглядности представления структуры).

```cpp
switch (m_gameState)
{
case GameState::MainMenu:
    // Do something with the main menu. 
    break;

case GameState::HighScoreDisplay:
    // Do something with the high-score table. 
    break;

case GameState::PostGameResults:
    // Do something with the game results. 
    break;

case GameState::InGamePaused:
    // Handle the paused state. 
    break;
}
```

Если игровая логика или отрисовка зависят от определенного состояния игры, мы подчеркиваем это в документации.

## <a name="handling-app-and-window-events"></a>Обработка событий приложения и окон


Среда выполнения Windows предоставляет объектно-ориентированную систему обработки событий, упрощающую управление сообщениями Windows. Для обработки события в приложении вы должны предоставить обработчик событий (метод обработки событий), который отвечает на событие. Вы также должны зарегистрировать этот обработчик событий в источнике событий. Этот процесс часто называется привязкой событий.

### <a name="supporting-suspend-resume-and-restart"></a>Поддержка приостановки, возобновления и перезапуска

Игра Marble Maze приостанавливается, когда пользователь переходит в другое приложение или когда Windows переводит устройство в режим пониженного энергопотребления. Игра возобновляется, когда пользователь переводит ее на передний план или Windows выводит устройство из режима пониженного энергопотребления. Обычно приложения не закрываются. Windows может завершить приложение, когда оно приостановлено, а системе требуются ресурсы (например, память), занятые приложением. Windows уведомляет приложение о готовящейся приостановке и возобновлении, но не уведомляет приложение перед завершением. Поэтому приложение в момент, когда Windows уведомляет его перед приостановкой, должно иметь возможность сохранить все данные, которые могут потребоваться для восстановления текущего состояния пользователя после перезапуска приложения. Если для сохранения важного состояния в приложении необходимо много ресурсов, может понадобиться регулярно сохранять состояние даже до получения уведомления о приостановке. Marble Maze реагирует на уведомления о приостановке и возобновлении по двум причинам.

1.  Когда приложение приостанавливается, игра сохраняет текущее состояние и приостанавливает воспроизведение звука. Когда приложение возобновляется, игра возобновляет воспроизведение звука.
2.  Когда приложение закрывается и затем перезапускается, игра возобновляется из предыдущего состояния.

Для поддержки приостановки и возобновления приложение Marble Maze выполняет следующие задачи.

-   Оно сохраняет свое состояние в постоянном хранилище в ключевые моменты игры, например когда пользователь достигает контрольной точки.
-   Оно реагирует на уведомления о приостановке, сохраняя свое состояние в постоянном хранилище.
-   Оно реагирует на уведомления о возобновлении, загружая свое состояние из постоянного хранилища. Оно также загружает предыдущее состояние во время запуска.

Для поддержки приостановки и возобновления в Marble Maze определяется класс **PersistentState**. (См. файлы **PersistentState.h** и **PersistentState.cpp**). Этот класс использует интерфейс [Windows::Foundation::Collections::IPropertySet](https://docs.microsoft.com/uwp/api/Windows.Foundation.Collections.IPropertySet) для чтения и записи свойств. Класс **PersistentState** предоставляет методы, которые считывают примитивные типы данных (например, **bool**, **int**, **float**, [XMFLOAT3](https://msdn.microsoft.com/library/windows/desktop/ee419475), и [Platform::String](https://docs.microsoft.com/cpp/cppcx/platform-string-class)) из резервного хранилища и записывают их в резервное хранилище.

```cpp
ref class PersistentState
{
internal:
    void Initialize(
        _In_ Windows::Foundation::Collections::IPropertySet^ settingsValues,
        _In_ Platform::String^ key
        );

    void SaveBool(Platform::String^ key, bool value);
    void SaveInt32(Platform::String^ key, int value);
    void SaveSingle(Platform::String^ key, float value);
    void SaveXMFLOAT3(Platform::String^ key, DirectX::XMFLOAT3 value);
    void SaveString(Platform::String^ key, Platform::String^ string);

    bool LoadBool(Platform::String^ key, bool defaultValue);
    int  LoadInt32(Platform::String^ key, int defaultValue);
    float LoadSingle(Platform::String^ key, float defaultValue);

    DirectX::XMFLOAT3 LoadXMFLOAT3(
        Platform::String^ key, 
        DirectX::XMFLOAT3 defaultValue);

    Platform::String^ LoadString(
        Platform::String^ key, 
        Platform::String^ defaultValue);

private:
    Platform::String^ m_keyName;
    Windows::Foundation::Collections::IPropertySet^ m_settingsValues;
};
```

Класс **MarbleMazeMain** содержит объект **PersistentState**. Конструктор **MarbleMazeMain** инициализирует этот объект и предоставляет локальное хранилище данных приложения в качестве резервного хранилища.

```cpp
m_persistentState = ref new PersistentState();

m_persistentState->Initialize(
    Windows::Storage::ApplicationData::Current->LocalSettings->Values,
    "MarbleMaze");
```

Приложение Marble Maze сохраняет свое состояние, когда шарик проходит контрольную точку или достигает цели (в методе **MarbleMazeMain::Update**), а также когда окно теряет фокус (в методе **MarbleMazeMain::OnFocusChange**). Если данные о состоянии игры занимают большой объем, рекомендуется время от времени сохранять состояние в постоянном хранилище аналогичным образом, так как у игры на действия после уведомления о приостановке будет лишь несколько секунд. В этом случае после получения уведомления о приостановке приложение должно будет сохранить только изменившиеся данные о состоянии.

В ответ на уведомления о приостановке и возобновлении класс **MarbleMazeMain** определяет методы **SaveState** и **LoadState**, вызываемые при приостановке и возобновлении. Метод **MarbleMazeMain::OnSuspending** обрабатывает событие приостановки, а метод **MarbleMazeMain::OnResuming** — событие возобновления.

Метод **MarbleMazeMain::OnSuspending** сохраняет состояние игры и приостанавливает воспроизведение звука.

```cpp
void MarbleMazeMain::OnSuspending()
{
    SaveState();
    m_audio.SuspendAudio();
}
```

Метод **MarbleMazeMain::SaveState** сохраняет значения состояния игры (текущее положение и скорость шарика, последнюю контрольную точку и таблицу результатов).

```cpp
void MarbleMazeMain::SaveState()
{
    m_persistentState->SaveXMFLOAT3(":Position", m_physics.GetPosition());
    m_persistentState->SaveXMFLOAT3(":Velocity", m_physics.GetVelocity());

    m_persistentState->SaveSingle(
        ":ElapsedTime", 
        m_inGameStopwatchTimer.GetElapsedTime());

    m_persistentState->SaveInt32(":GameState", static_cast<int>(m_gameState));
    m_persistentState->SaveInt32(":Checkpoint", static_cast<int>(m_currentCheckpoint));

    int i = 0;
    HighScoreEntries entries = m_highScoreTable.GetEntries();
    const int bufferLength = 16;
    char16 str[bufferLength];

    m_persistentState->SaveInt32(":ScoreCount", static_cast<int>(entries.size()));

    for (auto iter = entries.begin(); iter != entries.end(); ++iter)
    {
        int len = swprintf_s(str, bufferLength, L"%d", i++);
        Platform::String^ string = ref new Platform::String(str, len);

        m_persistentState->SaveSingle(
            Platform::String::Concat(":ScoreTime", string), 
            iter->elapsedTime);

        m_persistentState->SaveString(
            Platform::String::Concat(":ScoreTag", string), 
            iter->tag);
    }
}
```

При возобновлении игры нужно только возобновить воспроизведение звука. Состояние не нужно загружать из постоянного хранилища, поскольку оно уже загружено в память.

Приостановка и возобновление воспроизведения звука в игре описываются в документе [Добавление звука в пример Marble Maze](adding-audio-to-the-marble-maze-sample.md).

Для поддержки перезапуска конструктор **MarbleMazeMain::Initialize**, который вызывается во время запуска, вызывает метод **MarbleMazeMain::LoadState**. Метод **MarbleMazeMain::LoadState** считывает и применяет состояние к игровым объектам. Этот метод также задает для игры состояние паузы независимо от состояния на момент приостановки (пауза или активная игра). Пауза включается, чтобы пользователя не застала врасплох игровая ситуация. Если на момент приостановки не был активен игровой процесс, то игра переходит в главное меню.

```cpp
void MarbleMazeMain::LoadState()
{
    XMFLOAT3 position = m_persistentState->LoadXMFLOAT3(
        ":Position", 
        m_physics.GetPosition());

    XMFLOAT3 velocity = m_persistentState->LoadXMFLOAT3(
        ":Velocity", 
        m_physics.GetVelocity());

    float elapsedTime = m_persistentState->LoadSingle(":ElapsedTime", 0.0f);

    int gameState = m_persistentState->LoadInt32(
        ":GameState", 
        static_cast<int>(m_gameState));

    int currentCheckpoint = m_persistentState->LoadInt32(
        ":Checkpoint", 
        static_cast<int>(m_currentCheckpoint));

    switch (static_cast<GameState>(gameState))
    {
    case GameState::Initial:
        break;

    case GameState::MainMenu:
    case GameState::HighScoreDisplay:
    case GameState::PreGameCountdown:
    case GameState::PostGameResults:
        SetGameState(GameState::MainMenu);
        break;

    case GameState::InGameActive:
    case GameState::InGamePaused:
        m_inGameStopwatchTimer.SetVisible(true);
        m_inGameStopwatchTimer.SetElapsedTime(elapsedTime);
        m_physics.SetPosition(position);
        m_physics.SetVelocity(velocity);
        m_currentCheckpoint = currentCheckpoint;
        SetGameState(GameState::InGamePaused);
        break;
    }

    int count = m_persistentState->LoadInt32(":ScoreCount", 0);

    const int bufferLength = 16;
    char16 str[bufferLength];

    for (int i = 0; i < count; i++)
    {
        HighScoreEntry entry;
        int len = swprintf_s(str, bufferLength, L"%d", i);
        Platform::String^ string = ref new Platform::String(str, len);

        entry.elapsedTime = m_persistentState->LoadSingle(
            Platform::String::Concat(":ScoreTime", string), 
            0.0f);

        entry.tag = m_persistentState->LoadString(
            Platform::String::Concat(":ScoreTag", string), 
            L"");

        m_highScoreTable.AddScoreToTable(entry);
    }
}
```

> [!IMPORTANT]
> В Marble Maze нет различия между начальным запуском (без предшествующего события приостановки) и возобновлением после приостановки. Так рекомендуется делать во всех приложениях UWP.

Подробнее о данных приложения: [Хранение и извлечение параметров и прочих данных приложения](https://msdn.microsoft.com/library/windows/apps/mt299098).

##  <a name="next-steps"></a>Дальнейшие действия


Ознакомьтесь с разделом [Добавление визуального содержимого в пример Marble Maze](adding-visual-content-to-the-marble-maze-sample.md), чтобы изучить основные моменты, о которых нужно помнить при работе с визуальными ресурсами.

## <a name="related-topics"></a>Ссылки по теме

* [Добавление визуального содержимого в пример Marble Maze](adding-visual-content-to-the-marble-maze-sample.md)
* [Основные характеристики примера Marble Maze](marble-maze-sample-fundamentals.md)
* [Разработка Marble Maze — игры UWP — на C++ и DirectX](developing-marble-maze-a-windows-store-game-in-cpp-and-directx.md)

 

 




