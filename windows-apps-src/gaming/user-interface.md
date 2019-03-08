---
title: Шаблоны проектов игр DirectX
description: Узнайте о шаблонах для создания игры для универсальной платформы Windows (UWP) с использованием DirectX.
ms.assetid: 41b6cd76-5c9a-e2b7-ef6f-bfbf6ef7331d
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp, игры, directx, шаблоны
ms.localizationpriority: medium
ms.openlocfilehash: 9a4491fe9a3bb97a73652c40a2968f2f53c377b5
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57634249"
---
# <a name="directx-game-project-templates"></a>Шаблоны проектов игр DirectX



Шаблоны DirectX и универсальной платформы Windows (UWP) позволяют быстро создать проект в виде начальной точки вашей игры.

## <a name="prerequisites"></a>Предварительные условия


Для создания проекта вам потребуется:

-   [Скачайте Microsoft Visual Studio 2015](https://www.visualstudio.com/vs-2015-product-editions). Visual Studio 2015 включает средства для программирования, таких как средства отладки графики. Обзор графических и игровых компонентов и инструментов DirectX можно просмотреть в разделе об [инструментах Visual Studio для разработки игр DirectX](set-up-visual-studio-for-game-development.md).

## <a name="choosing-a-template"></a>Выбор шаблона


Visual Studio 2015 включает в себя три шаблоны DirectX и универсальной платформы Windows:

-   Приложение DirectX 11 (универсальные приложения для Windows) — шаблон приложения DirectX 11 (универсальные приложения для Windows) создает проект UWP, который выполняет обработку с помощью DirectX 11 непосредственно в окне приложения.
-   Приложение DirectX 12 (универсальные приложения для Windows) — шаблон приложения DirectX 12 (универсальные приложения для Windows) создает проект UWP, который выполняет обработку с помощью DirectX 12 непосредственно в окне приложения.
-   Приложение DirectX 11 и XAML (универсальные приложения для Windows) — шаблон приложения DirectX 11 и XAML (универсальные приложения для Windows) создает проект UWP, который выполняет обработку с помощью DirectX 11 внутри элемента управления XAML. Этот шаблон использует [**SwapChainPanel**](https://msdn.microsoft.com/library/windows/apps/dn252834), поэтому вы можете использовать элементы управления пользовательским интерфейсом XAML. Это облегчает задачу добавления элементов пользовательского интерфейса, но использование шаблона XAML может привести к снижению производительности.

Выбор шаблона зависит от производительности и технологий, которые вы хотите использовать.

## <a name="template-structure"></a>Структура шаблона


Универсальные шаблоны DirectX для Windows содержат следующие файлы:

-   pch.h и pch.cpp — поддержка предварительно скомпилированных заголовков.
-   Package.appxmanifest — свойства пакета развертывания для приложения.
-   \*.pfx - сертификаты для приложения.
-   Внешние зависимости — ссылки на внешние файлы, которые используются в проекте.
-   \*Main.h и \*Main.cpp - методы для управления ресурсами приложения, обновление состояния приложения и отрисовки кадра.
-   App.h и App.cpp — главная точка входа для приложения. Соединяет приложение с оболочкой Windows и обрабатывает события жизненного цикла приложения. Эти файлы появляются только в шаблонах приложения DirectX 11 (универсальные приложения для Windows) и приложения DirectX 12 (универсальные приложения для Windows).
-   App.xaml, App.xaml.cpp и App.xaml.h — главная точка входа для приложения. Соединяет приложение с оболочкой Windows и обрабатывает события жизненного цикла приложения. Эти файлы появляются только в шаблоне приложения DirectX 11 и XAML (универсальные приложения для Windows).
-   DirectXPage.xaml, DirectXPage.xaml.cpp и DirectXPage.xaml.h — страница, на которой размещается DirectX SwapChainPanel. Эти файлы появляются только в шаблоне приложения DirectX 11 и XAML (универсальные приложения для Windows).
-   Содержимое
    -   Sample3DSceneRenderer.h и Sample3DSceneRenderer.cpp — простой обработчик, который создает экземпляр базового конвейера отрисовки.
    -   SampleFpsTextRenderer.h и SampleFpsTextRenderer.cpp — отрисовывает текущее значение кадров в секунду в нижнем правом углу экрана с помощью Direct2D и DirectWrite. Эти файлы появляются только в шаблонах приложения DirectX 11 (Universal Windows) и приложения DirectX 11 и XAML (Universal Windows).
    -   SamplePixelShader.hlsl — простой пример построителя текстуры.
    -   SampleVertexShader.hlsl — простой пример вершинного построителя текстуры.
    -   ShaderStructures.h — структуры, используемые для отправки данных в пример вершинного построителя текстуры.
-   Общие
    -   StepTimer.h — вспомогательный класс для расчета времени анимации и симуляции.
    -   DirectXHelper.h — различные вспомогательные функции.
    -   DeviceResources.h и Device Resources.cpp — предоставляет интерфейс для уведомления приложения, имеющего DeviceResources, о потере или создании устройства.
    -   d3dx12.h — содержит библиотеку средств D3DX12. Этот файл появляется только в приложении DirectX 12 (Universal Windows).
-   Ресурсы — эмблема и изображение элемента splashscreen, используемых приложением.

## <a name="next-steps"></a>Дальнейшие действия


Теперь у вас есть отправная точка; остается добавить к этому знания и навыки разработки игр для Microsoft Store.

Сведения о портировании существующей игры см. в следующих разделах.

-   [Перенос с OpenGL ES 2.0 на Direct3D 11.1](port-from-opengl-es-2-0-to-directx-11-1.md)
-   [Перенос с DirectX 9 на платформе универсальных Windows](porting-your-directx-9-game-to-windows-store.md)

Сведения о создании новой игры DirectX см. в следующих разделах.

-   [Создание простой игры UWP c использованием DirectX](tutorial--create-your-first-uwp-directx-game.md)
-   [Разработка Marble Maze — игры для универсальной платформы Windows в C++ и DirectX](developing-marble-maze-a-windows-store-game-in-cpp-and-directx.md)