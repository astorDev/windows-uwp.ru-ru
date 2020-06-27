---
title: Настройка проекта игры
description: Первым шагом в разработке игры является настройка проекта в Microsoft Visual Studio. После настройки проекта, предназначенного специально для разработки игр, его можно использовать в качестве типа шаблона.
ms.assetid: 9fde90b3-bf79-bcb3-03b6-d38ab85803f2
ms.date: 06/24/2020
ms.topic: article
keywords: windows 10, uwp, игры, настройка, directx
ms.localizationpriority: medium
ms.openlocfilehash: c0c8d82752d9b73a3e3e7ffcec3ced1515564072
ms.sourcegitcommit: 20969781aca50738792631f4b68326f9171a3980
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/26/2020
ms.locfileid: "85409593"
---
# <a name="set-up-the-game-project"></a>Настройка проекта игры

> [!NOTE]
> Эта статья является частью серии руководств по [созданию простой универсальная платформа Windows (UWP) с помощью DirectX](tutorial--create-your-first-uwp-directx-game.md) . В разделе этой ссылки задается контекст для ряда.

Первым шагом в разработке игры является создание проекта в Microsoft Visual Studio. После настройки проекта, предназначенного специально для разработки игр, его можно использовать в качестве типа шаблона.

## <a name="objectives"></a>Задачи

* Создание нового проекта в Visual Studio с помощью шаблона проекта.
* Изучите точку входа и инициализацию игры, изучив исходный файл для класса **app** .
* Взгляните на цикл игры.
* Проверьте файл **Package. appxmanifest** проекта.

## <a name="create-a-new-project-in-visual-studio"></a>Создание проекта в Visual Studio

> [!NOTE]
> Сведения об установке Visual Studio для разработки с использованием C++/WinRT, включая установку и использование расширения C++/WinRT для Visual Studio (VSIX) и пакета NuGet (которые вместе обеспечивают поддержку шаблона проекта и сборки), приведены в разделе [Поддержка Visual Studio для C++/WinRT, XAML, расширения VSIX и пакета NuGet](/windows/uwp/intro-to-using-cpp-with-winrt.md#visual-studio-support-for-cwinrt-xaml-the-vsix-extension-and-the-nuget-package).

Сначала установите (или обновите до) последнюю версию расширения C++/WinRT Visual Studio (VSIX). см. примечание выше. Затем в Visual Studio создайте новый проект на основе шаблона проекта **Core App (C++/WinRT)** . В качестве цели выберите последнюю общедоступную (то есть не предварительную) версию Windows SDK.

## <a name="review-the-app-class-to-understand-iframeworkviewsource-and-iframeworkview"></a>Изучите класс **app** , чтобы понять **IFrameworkViewSource** и **IFrameworkView**

В проекте основного приложения откройте файл исходного кода `App.cpp` . В существует реализация класса **приложения** , который представляет приложение и его жизненный цикл. В данном случае, конечно, мы понимаем, что приложение является игрой. Но мы будем называть его как *приложение* для более подробного разговора о том, как инициализируется приложение универсальная платформа Windows (UWP).

### <a name="the-wwinmain-function"></a>Функция wWinMain

Функция **wWinMain** — это точка входа для приложения. Вот как выглядит **wWinMain** (от `App.cpp` ).

```cppwinrt
int __stdcall wWinMain(HINSTANCE, HINSTANCE, PWSTR, int)
{
    CoreApplication::Run(winrt::make<App>());
}
```

Мы создаем экземпляр класса **app** (только один экземпляр **приложения** , который создан) и передаем его в статический метод [**CoreApplication. Run**](/uwp/api/windows.applicationmodel.core.coreapplication.run) . Обратите внимание, что **CoreApplication. Run** принимает интерфейс [**IFrameworkViewSource**](/uwp/api/windows.applicationmodel.core.iframeworkviewsource) . Поэтому классу **приложения** необходимо реализовать этот интерфейс.

В следующих двух подразделах этого раздела описываются интерфейсы [**IFrameworkViewSource**](/uwp/api/windows.applicationmodel.core.iframeworkviewsource) и [**IFrameworkView**](/uwp/api/windows.applicationmodel.core.iframeworkviewsource) . Эти интерфейсы (а также **CoreApplication. Run**) представляют собой способ предоставления приложения Windows с помощью *поставщика представления*. Windows использует этого поставщика представления для подключения приложения к оболочке Windows, чтобы можно было управлять событиями жизненного цикла приложения.

### <a name="the-iframeworkviewsource-interface"></a>Интерфейс IFrameworkViewSource

Класс **app** действительно реализует [**IFrameworkViewSource**](/uwp/api/windows.applicationmodel.core.iframeworkviewsource), как можно увидеть в приведенном ниже списке.

```cppwinrt
struct App : winrt::implements<App, IFrameworkViewSource, IFrameworkView>
{
    ...
    IFrameworkView CreateView()
    {
        return *this;
    }
    ...
}
```

Объект, реализующий **IFrameworkViewSource** , является объектом *фабрики для поставщика представления* . Задача этого объекта — изготовление и возврат объекта *поставщика представления* .

**IFrameworkViewSource** имеет единственный метод [**IFrameworkViewSource:: CreateView**](/uwp/api/windows.applicationmodel.core.iframeworkviewsource.createview). Windows вызывает эту функцию для объекта, переданного в **CoreApplication. Run**. Как можно увидеть выше, реализация метода **app:: CreateView** возвращает `*this` . Иными словами, объект **app** возвращает сам себя. Так как **IFrameworkViewSource:: CreateView** имеет тип возвращаемого значения [**IFrameworkView**](/uwp/api/windows.applicationmodel.core.iframeworkviewsource), следует также убедиться, что класс **приложения** тоже должен реализовать *этот* интерфейс. Как видно из приведенного выше списка.

### <a name="the-iframeworkview-interface"></a>Интерфейс IFrameworkView

Объект, реализующий [**IFrameworkView**](/uwp/api/windows.applicationmodel.core.iframeworkviewsource) , является объектом *поставщика представления* . Теперь мы предоставили Windows с этим поставщиком представления. Это тот же объект **приложения** , который мы создали в **wWinMain**. Таким образом, класс **app** выступает в качестве *фабрики* и *поставщика*представления.

Теперь Windows может вызвать реализации методов **IFrameworkView**класса **app** . В реализациях этих методов приложение может выполнять такие задачи, как инициализация, чтобы начать загрузку необходимых ресурсов, подключить соответствующие обработчики событий и получить [**CoreWindow**](/uwp/api/windows.ui.core.corewindow) , который приложение будет использовать для вывода выходных данных.

Реализации методов **IFrameworkView** вызываются в порядке, показанном ниже.

- [**Initialize**](/uwp/api/windows.applicationmodel.core.iframeworkview.initialize)
- [**SetWindow**](/uwp/api/windows.applicationmodel.core.iframeworkview.setwindow)
- [**Загрузить**](/uwp/api/windows.applicationmodel.core.iframeworkview.load)
- Вызвано событие [**кореаппликатионвиев:: Activated**](/uwp/api/windows.applicationmodel.core.coreapplicationview.activated) . Поэтому, если вы (необязательно) зарегистрировались для обработки этого события, в настоящее время вызывается обработчик **OnActivated** .
- [**Выполнить**](/uwp/api/windows.applicationmodel.core.iframeworkview.run)
- [**Отменить инициализацию**](/uwp/api/windows.applicationmodel.core.iframeworkview.uninitialize)

А вот скелет класса **app** (in `App.cpp` ), показывающий сигнатуры этих методов.

```cppwinrt
struct App : winrt::implements<App, IFrameworkViewSource, IFrameworkView>
{
    ...
    void Initialize(Windows::ApplicationModel::CoreCoreApplicationView const& applicationView) { ... }
    void SetWindow(Windows::UI::Core::CoreWindow const& window) { ... }
    void Load(winrt::hstring const& entryPoint) { ... }
    void OnActivated(
        Windows::ApplicationModel::CoreCoreApplicationView const& applicationView,
        Windows::ApplicationModel::Activation::IActivatedEventArgs const& args) { ... }
    void Run() { ... }
    void Uninitialize() { ... }
    ...
}
```

Это было лишь введением в **IFrameworkView**. Мы подробно рассмотрим эти методы и как их реализовать, в разделе [Определение платформы приложений UWP в игре](tutorial--building-the-games-uwp-app-framework.md).

### <a name="tidy-up-the-project"></a>Очистка проекта

Проект основного приложения, созданный на основе шаблона проекта, содержит функциональные возможности, которые должны быть включены в этот момент. После этого можно использовать проект для повторного создания игры коллекции вариантов (**Simple3DGameDX**). Внесите следующие изменения в класс **app** в `App.cpp` .

- Удалите элементы данных.
- Удалить **онпоинтерпрессед**, **онпоинтермовед**и **аддвисуал**
- Удалите код из **сетвиндов**.

Проект будет построен и запущен, но в клиентской области будет отображаться только сплошной цвет.

## <a name="the-game-loop"></a>Игровой цикл

Чтобы понять, как выглядит игровой цикл, просмотрите исходный код для скачанного примера игры [Simple3DGameDX](/samples/microsoft/windows-universal-samples/simple3dgamedx/) .

Класс **app** имеет элемент данных с именем *m_main*типа **гамемаин**. И этот элемент используется в **app:: Run** следующим образом.

```cppwinrt
void Run()
{
    m_main->Run();
}
```

В можно найти **гамемаин:: Run** `GameMain.cpp` . Это главный цикл игры, и вот очень грубая структура, в которой показаны наиболее важные функции.

```cppwinrt
void GameMain::Run()
{
    while (!m_windowClosed)
    {
        if (m_visible)
        {
            CoreWindow::GetForCurrentThread().Dispatcher().ProcessEvents(CoreProcessEventsOption::ProcessAllIfPresent);
            Update();
            m_renderer->Render();
            m_deviceResources->Present();
        }
        else
        {
            CoreWindow::GetForCurrentThread().Dispatcher().ProcessEvents(CoreProcessEventsOption::ProcessOneAndAllPending);
        }
    }
}
```

А вот краткое описание того, что делает этот главный цикл игры.

Если окно для игры не закрыто, отправьте все события, обновите таймер, а затем выполните визуализацию и представление результатов графического конвейера. Есть гораздо больше рассказать об этих проблемах, и мы будем делать это в разделах, которые [определяют платформу приложений UWP в игре](tutorial--building-the-games-uwp-app-framework.md), [платформу визуализации I: введение в подготовку к просмотру](tutorial--assembling-the-rendering-pipeline.md)и [отрисовку инфраструктуры II: Визуализация игр](tutorial-game-rendering.md). Но это базовая структура кода для игры DirectX UWP.

## <a name="review-and-update-the-packageappxmanifest-file"></a>Изучение и обновление файла package.appxmanifest

Файл **Package. appxmanifest** содержит метаданные о проекте UWP. Эти метаданные используются для упаковки и запуска игры, а также для отправки в Microsoft Store. Файл также содержит важную информацию, которую система проигрывателя использует для предоставления доступа к системным ресурсам, которые должны быть запущены игрой.

Запустите **конструктор манифеста** , дважды щелкнув файл **Package. appxmanifest** в **Обозреватель решений**.

![Снимок экрана редактора манифеста package.appx.](images/simple-dx-game-setup-app-manifest.png)

Подробнее о файле **package.appxmanifest** и упаковке см. в статье [Конструктор манифестов](/previous-versions/br230259(v=vs.140)). Сейчас Взгляните на вкладку **возможности** и просмотрите доступные параметры.

![Снимок экрана с возможностями по умолчанию для приложения Direct3D.](images/simple-dx-game-setup-capabilities.png)

Если вы не выбрали возможности, используемые в игре, например доступ к **Интернету** для глобальной платы высокой оценок, вы не сможете получить доступ к соответствующим ресурсам и функциям. При создании игры убедитесь, что выбраны все возможности, необходимые API-интерфейсам, которые вызывает данная игра.

Теперь давайте взглянем на остальные файлы, которые поставляются вместе с шаблоном **приложения DirectX 11 (Universal Windows)** .

## <a name="review-other-important-libraries-and-source-code-files"></a>Ознакомьтесь с другими важными библиотеками и файлами исходного кода

Если вы планируете создать тип шаблона игрового проекта для себя, чтобы вы могли использовать его повторно в качестве начальной точки для будущих проектов, вам потребуется скопировать `GameMain.h` и `GameMain.cpp` извлечь загруженный проект [Simple3DGameDX](/samples/microsoft/windows-universal-samples/simple3dgamedx/) и добавить его в новый проект основного приложения. Изучите эти файлы, Узнайте, что они делают, и удалите все, что характерно для **Simple3DGameDX**. Кроме того, закомментируйте все, что зависит от кода, который еще не скопирован. Как и в примере, `GameMain.h` зависит от `GameRenderer.h` . Вы сможете раскомментировать, когда вы скопируйте больше файлов из **Simple3DGameDX**.

Ниже приведен краткий обзор некоторых файлов в **Simple3DGameDX** , которые можно использовать в шаблоне, если вы делаете это. В любом случае они важны для понимания того, как работает сам **Simple3DGameDX** .

|Исходный файл|Папка с файлами|Описание:|
|------------------------------|------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|DeviceResources.h/.cpp|Техническое оборудование|Определяет класс **DeviceResources** , который управляет всеми [ресурсами устройств](tutorial--assembling-the-rendering-pipeline.md#resource)DirectX. Также определяет интерфейс **объект idevicenotify** , используемый для уведомления приложения о том, что устройство графического адаптера было потеряно или повторно создано.|
|DirectXSample.h|Техническое оборудование|Реализует вспомогательные функции, такие как **конвертдипстопикселс**. **ConvertDipsToPixels** преобразовывает длину в аппаратно независимых пикселях (DIP) в длину в физических пикселях.|
|Гаметимер. h/. cpp|Техническое оборудование|Определяет высокоточный таймер для игр или интерактивных приложений с отрисовкой.|
|GameRenderer.h/.cpp|Отрисовка|Определяет класс **гамерендерер** , реализующий базовый конвейер отрисовки.|
|Гамехуд. h/. cpp|Отрисовка|Определяет класс для отображения заголовков (HUD) для игры с использованием Direct2D и DirectWrite.|
|Вертексшадер. HLSL и Вертексшадерфлат. HLSL|Шейдеры|Содержит код на высоком уровне шейдера (HLSL) для основных шейдеров вершин.|
|PixelShader. HLSL и Пикселшадерфлат. HLSL|Шейдеры|Содержит код на высоком уровне шейдера (HLSL) для базовых шейдеров пикселей.|
|Константбуфферс. hlsli|Шейдеры|Содержит определения структуры данных для буферов констант и структур шейдеров, которые используются для передачи матриц-представлений (MVP) и данных по вершинам в шейдер вершин.|
|pch.h/.cpp|Недоступно|Содержит общие включаемые в C++/WinRT, Windows и DirectX.| 

### <a name="next-steps"></a>Следующие шаги

На этом этапе мы увидели, как создать новый проект UWP для игры DirectX, Просмотрели некоторые из его частей и начали думать о том, как превратить этот проект в тип повторно используемого шаблона для игр. Мы также рассмотрели некоторые важные части примера игры **Simple3DGameDX** .

В следующем разделе определяется [Платформа приложений UWP в игре](tutorial--building-the-games-uwp-app-framework.md). Мы подробнее рассмотрим, как работает **Simple3DGameDX** .
