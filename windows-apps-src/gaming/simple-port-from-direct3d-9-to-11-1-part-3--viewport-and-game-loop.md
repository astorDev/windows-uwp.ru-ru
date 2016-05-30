---
author: mtoepke
title: Портирование игрового цикла
description: Здесь показано, как реализовать окно для игрового приложения UWP и перенести игровой цикл, включая создание интерфейса IFrameworkView для управления полноэкранным CoreWindow.
ms.assetid: 070dd802-cb27-4672-12ba-a7f036ff495c
---

# Портирование игрового цикла


\[ Обновлено для приложений UWP в Windows 10. Статьи о Windows 8.x см. в [архиве](http://go.microsoft.com/fwlink/p/?linkid=619132) \]

**Резюме**

-   [Часть 1. Инициализация Direct3D 11](simple-port-from-direct3d-9-to-11-1-part-1--initializing-direct3d.md)
-   [Часть 2. Преобразование инфраструктуры отрисовки](simple-port-from-direct3d-9-to-11-1-part-2--rendering.md)
-   Часть 3. Перенос игрового цикла


Здесь показано, как реализовать окно для игрового приложения UWP и перенести игровой цикл, включая создание интерфейса [**IFrameworkView**](https://msdn.microsoft.com/library/windows/apps/hh700478) для управления полноэкранным [**CoreWindow**](https://msdn.microsoft.com/library/windows/apps/br208225). [Пошаговое руководство: портирование простого приложения Direct3D 9 на DirectX 11 и UWP](walkthrough--simple-port-from-direct3d-9-to-11-1.md), часть 3.

## Создание окна


Чтобы создать окно рабочего стола с окном просмотра Direct3D 9, нам приходилось реализовывать традиционную оконную инфраструктуру классических приложений. В частности, нужно было создавать HWND, устанавливать размер окна, предоставлять обратный вызов обработки окон, делать окно видимым и т. д.

В приложениях UWP все гораздо проще. Вместо того чтобы создавать традиционное окно, в игровом приложении Магазина Windows на базе DirectX реализуется интерфейс [**IFrameworkView**](https://msdn.microsoft.com/library/windows/apps/hh700478). Этот интерфейс предназначен для того, чтобы выполнять игры и приложения на базе DirectX непосредственно в объекте [**CoreWindow**](https://msdn.microsoft.com/library/windows/apps/br208225) внутри контейнера приложения.

> **Примечание.**   Windows предоставляет управляемые указатели на ресурсы, такие как объект приложения-источника и [**CoreWindow**](https://msdn.microsoft.com/library/windows/apps/br208225). См. [**Оператор дескриптора объекта (^)**]https://msdn.microsoft.com/library/windows/apps/yk97tc08.aspx.

 

Ваш класс main должен наследовать от [**IFrameworkView**](https://msdn.microsoft.com/library/windows/apps/hh700478) и реализовывать пять методов интерфейса **IFrameworkView**: [**Initialize**](https://msdn.microsoft.com/library/windows/apps/hh700495), [**SetWindow**](https://msdn.microsoft.com/library/windows/apps/hh700509), [**Load**](https://msdn.microsoft.com/library/windows/apps/hh700501), [**Run**](https://msdn.microsoft.com/library/windows/apps/hh700505) и [**Uninitialize**](https://msdn.microsoft.com/library/windows/apps/hh700523). Помимо создания интерфейса **IFrameworkView**, внутри которого будет существовать ваша игра, вам необходимо реализовать фабрику классов, которая создает экземпляр вашего **IFrameworkView**. У вашей игры по-прежнему будет исполняемый файл с методом под названием **main()**, но все, что этот метод будет делать, – создавать экземпляр **IFrameworkView** с помощью фабрики.

Функция main

```cpp
//-----------------------------------------------------------------------------
// Required method for a DirectX-only app.
// The main function is only used to initialize the app's IFrameworkView class.
//-----------------------------------------------------------------------------
[Platform::MTAThread]
int main(Platform::Array<Platform::String^>^)
{
    auto direct3DApplicationSource = ref new Direct3DApplicationSource();
    CoreApplication::Run(direct3DApplicationSource);
    return 0;
}
```

Фабрика IFrameworkView

```cpp
//-----------------------------------------------------------------------------
// This class creates our IFrameworkView.
//-----------------------------------------------------------------------------
ref class Direct3DApplicationSource sealed : 
    Windows::ApplicationModel::Core::IFrameworkViewSource
{
public:
    virtual Windows::ApplicationModel::Core::IFrameworkView^ CreateView()
    {
        return ref new Cube11();
    };
};
```

## Портирование игрового цикла


Посмотрим на игровой цикл из нашей игры, реализованной на базе Direct3D 9. Этот код существует внутри функции main приложения. Каждая итерация игрового цикла обрабатывает сообщение от окна или преобразует кадр.

Игровой цикл в классической игре на базе Direct3D 9

```cpp
while(WM_QUIT != msg.message)
{
    // Process window events.
    // Use PeekMessage() so we can use idle time to render the scene. 
    bGotMsg = (PeekMessage(&msg, NULL, 0U, 0U, PM_REMOVE) != 0);

    if(bGotMsg)
    {
        // Translate and dispatch the message
        TranslateMessage(&msg);
        DispatchMessage(&msg);
    }
    else
    {
        // Render a new frame.
        // Render frames during idle time (when no messages are waiting).
        RenderFrame();
    }
}
```

Игровой цикл в версии нашей игры UWP устроен сходным образом, но несколько проще:

Игровой цикл выполняется внутри метода [**IFrameworkView::Run**](https://msdn.microsoft.com/library/windows/apps/hh700505), а не **main()**, потому что наша игра работает внутри класса [**IFrameworkView**](https://msdn.microsoft.com/library/windows/apps/hh700478).

Вместо того чтобы реализовать инфраструктуру обработки сообщений и вызывать [**PeekMessage**](https://msdn.microsoft.com/library/windows/desktop/ms644943), мы можем вызвать встроенный метод [**ProcessEvents**](https://msdn.microsoft.com/library/windows/apps/br208215) класса [**CoreDispatcher**](https://msdn.microsoft.com/library/windows/apps/br208211) окна нашего приложения. В игровом цикле не требуется ветвление для обработки сообщений – достаточно вызвать метод **ProcessEvents**.

Игровой цикл в игровом приложении Магазина Windows на базе Direct3D 11

```cpp
// Windows Store apps should not exit. Use app lifecycle events instead.
while (true)
{
    // Process window events.
    auto dispatcher = CoreWindow::GetForCurrentThread()->Dispatcher;
    dispatcher->ProcessEvents(CoreProcessEventsOption::ProcessAllIfPresent);

    // Render a new frame.
    RenderFrame();
}
```

Теперь у нас есть приложение UWP, которое создает ту же самую базовую графическую инфраструктуру и обрабатывает тот же цветной куб, что и пример с использованием DirectX 9.

## Дополнительные ресурсы


Добавьте себе в закладки раздел с [вопросами и ответами по переносу на DirectX 11](directx-porting-faq.md).

Шаблоны UWP на базе DirectX включают надежную инфраструктуру устройств Direct3D, готовую к использованию в игровых приложениях. Инструкции по выбору шаблона см. в разделе [Создание проекта игры DirectX на основе шаблона](user-interface.md).

Ознакомьтесь со следующими подробными статьями о разработке игровых универсальных приложений Windows:

-   [Пошаговое руководство: создание простой игры UWP с использованием DirectX](tutorial--create-your-first-metro-style-directx-game.md)
-   [Звук для игр](working-with-audio-in-your-directx-game.md)
-   [Элементы управления перемещением и обзором для игр](tutorial--adding-move-look-controls-to-your-directx-game.md)

 

 






<!--HONumber=May16_HO2-->


