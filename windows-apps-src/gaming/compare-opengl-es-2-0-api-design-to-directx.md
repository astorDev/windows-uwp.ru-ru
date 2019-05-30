---
title: Планирование переноса из OpenGL ES 2.0 в Direct3D
description: Если вы переносите игру с платформы iOS или Android, вам, вероятно, потребуется существенно углубиться в OpenGL ES 2.0.
ms.assetid: a31b8c5a-5577-4142-fc60-53217302ec3a
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp, игры, opengl, direct3d
ms.localizationpriority: medium
ms.openlocfilehash: 44b851ef96b93974724ff4cf0b309d119dfd72d7
ms.sourcegitcommit: ac7f3422f8d83618f9b6b5615a37f8e5c115b3c4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/29/2019
ms.locfileid: "66368967"
---
# <a name="plan-your-port-from-opengl-es-20-to-direct3d"></a>Планирование переноса из OpenGL ES 2.0 в Direct3D




**Важные API**

-   [Direct3D 11](https://docs.microsoft.com/windows/desktop/direct3d11/atoc-dx-graphics-direct3d-11)
-   [Visual C++](https://docs.microsoft.com/previous-versions/60k1461a(v=vs.140))

Если вы переносите игру с платформы iOS или Android, вам, вероятно, потребуется существенно углубиться в OpenGL ES 2.0. При подготовке к переносу базы кода вашего конвейера графики в Direct3D 11 и среду выполнения Windows необходимо перед началом работы решить несколько вопросов.

Основные усилия при переносе обычно связаны с пересмотром базы кода и сопоставлением API и шаблонов для двух моделей. Вам будет намного легче, если вы уделите время изучению этого раздела.

Вот некоторые вещи, о которых надо знать при переносе графики из OpenGL ES 2.0 в Direct3D 11.

## <a name="notes-on-specific-opengl-es-20-providers"></a>Примечания по конкретным поставщикам OpenGL ES 2.0


В разделах о переносе в этой части содержатся ссылки на реализацию Windows спецификации OpenGL ES 2.0, созданной группой Khronos. Все примеры кода OpenGL ES 2.0 были разработаны с помощью Visual Studio 2012 и основного синтаксиса Windows C. Если вы переходите от базы кода Objective C (iOS) или Java (Android), необходимо помнить, что в предоставленных образцах кода OpenGL ES 2.0 могут использоваться другие параметры или синтаксис вызова API. В этом руководстве мы пытались по мере возможности обойтись без привязки к конкретной платформе.

В этой документации используются только API спецификации 2.0 для кода OpenGL ES и справочник. Если вы выполняете перенос из OpenGL ES 1.1 или 3.0, это содержимое тоже окажется полезным, хотя некоторые из примеров кода OpenGL ES 2.0 могут казаться непривычными.

Образцы Direct3D 11 в этих разделах используют Microsoft Windows C++ с расширениями компонентов (CX). Дополнительные сведения об этой версии синтаксис C++. в статье [Visual C++](https://docs.microsoft.com/previous-versions/60k1461a(v=vs.140)), [расширения компонентов для платформ среды выполнения](https://docs.microsoft.com/cpp/windows/component-extensions-for-runtime-platforms), и [краткий справочник (C++\\CX)](https://docs.microsoft.com/cpp/cppcx/quick-reference-c-cx).

## <a name="understand-your-hardware-requirements-and-resources"></a>Требования к оборудованию и ресурсам


Набор компонентов обработки графики, поддерживаемый OpenGL ES 2.0, примерно сопоставим с компонентами, предоставляемыми в Direct3D 9.1. Если вы хотите использовать преимущества более мощных средств, предоставляемых Direct3D 11, ознакомьтесь с документацией [Direct3D 11](https://docs.microsoft.com/windows/desktop/direct3d11/atoc-dx-graphics-direct3d-11) при планировании переноса или просмотрите разделы [Перенос из DirectX 9 на универсальную платформу Windows (UWP)](porting-your-directx-9-game-to-windows-store.md), когда закончите предварительную работу.

Чтобы упростить первоначальные действия по переносу, начните с шаблона Visual Studio Direct3D. Он предоставляет уже настроенное базовое средство отрисовки и поддерживает такие средства приложения UWP, как повторное создание ресурсов при изменении окна и уровни компонентов Direct3D.

## <a name="understand-direct3d-feature-levels"></a>Знакомство с уровнями компонентов Direct3D


Direct3D 11 предоставляет поддержку для оборудования «функциональных уровней» из 9\_1 (Direct3D 9.1) 11\_1. Эти уровни компонентов указывают доступность определенных графических средств и ресурсов. Как правило, большинство платформ OpenGL ES 2.0 поддерживает Direct3D 9.1 (уровень 9\_1) набор функций.

## <a name="review-directx-graphics-features-and-apis"></a>Обзор графических средств DirectX и API


| Семейство API                                                | Описание                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
|-----------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [DXGI](https://docs.microsoft.com/windows/desktop/direct3ddxgi/dx-graphics-dxgi)                     | DirectX Graphics Infrastructure (DXGI) предоставляет интерфейс между графическим оборудованием и Direct3D. Эта инфраструктура устанавливает адаптер устройства и аппаратную конфигурацию с помощью [**IDXGIAdapter**](https://docs.microsoft.com/windows/desktop/api/dxgi/nn-dxgi-idxgiadapter) и интерфейсов модели COM [**IDXGIDevice1**](https://docs.microsoft.com/windows/desktop/api/dxgi1_2/nn-dxgi1_2-idxgidevice2). Используйте ее для создания и настройки ваших буферов и других ресурсов окон. В частности, шаблон фабрики [**IDXGIFactory2**](https://docs.microsoft.com/windows/desktop/api/dxgi1_2/nn-dxgi1_2-idxgifactory2) используется для получения графических ресурсов, в том числе цепочки буферов (набор буферов кадров). Поскольку в DXGI имеется цепочка буферов, интерфейс [**IDXGISwapChain1**](https://docs.microsoft.com/windows/desktop/api/dxgi1_2/nn-dxgi1_2-idxgiswapchain1) используется для представления кадров на экране. |
| [Direct3D](https://docs.microsoft.com/windows/desktop/direct3d11/atoc-dx-graphics-direct3d-11)       | Direct3D является набором API, которые предоставляют виртуальное представление графического интерфейса и позволяют рисовать графику. На уровне компонентов версия 11 примерно сравнима с OpenGL 4.3. (OpenGL ES 2.0, с другой стороны, аналогичную DirectX9, совместимы и OpenGL 2.0, но с OpenGL 3.0 единой конвейер шейдера.) Большая часть тяжелой работы выполняется с помощью ID3D11Device1 и ID3D11DeviceContext1 интерфейсы, которые предоставляют доступ к отдельных ресурсах и вспомогательных ресурсах и контексте отрисовки, соответственно.                                                                                                                                          |
| [Direct2D](https://docs.microsoft.com/windows/desktop/Direct2D/direct2d-portal)                      | Direct2D предоставляет набор API для двумерной отрисовки с аппаратным ускорением. По предназначению это можно сравнить с OpenVG.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
| [DirectWrite](https://docs.microsoft.com/windows/desktop/DirectWrite/direct-write-portal)            | DirectWrite предоставляет набор API для отрисовки шрифтов с аппаратным ускорением.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
| [DirectXMath](https://docs.microsoft.com/windows/desktop/dxmath/directxmath-portal)                  | DirectXMath предоставляет набор API и макросы для обработки общих линейноалгебраических и тригонометрических типов, значений и функций. Эти типы и функции хорошо подходят для Direct3D и операций шейдеров в нем.                                                                                                                                                                                                                                                                                                                                                                                                                                                               |
| [DirectX HLSL](https://docs.microsoft.com/windows/desktop/direct3dhlsl/dx-graphics-hlsl-common-core) | Шейдеры Direct3D используют текущий синтаксис HLSL. Он реализует модель шейдера Direct3D 5.0.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |

 

## <a name="review-the-windows-runtime-apis-and-template-library"></a>Обзор API среды выполнения Windows и библиотеки шаблонов


API среды выполнения Windows предоставляют общую инфраструктуру для приложений UWP. Просмотрите их обзор [здесь](https://docs.microsoft.com/uwp/api/).

Ключевые API среды выполнения Windows, используемые при переносе конвейера графики, включают:

-   [**Windows::UI::Core::CoreWindow**](https://docs.microsoft.com/uwp/api/Windows.UI.Core.CoreWindow)
-   [**Windows::UI::Core::CoreDispatcher**](https://docs.microsoft.com/uwp/api/Windows.UI.Core.CoreDispatcher)
-   [**Windows::ApplicationModel::Core::IFrameworkView**](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Core.IFrameworkView)
-   [**Windows::ApplicationModel::Core::CoreApplicationView**](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Core.CoreApplicationView)

Кроме того, библиотека шаблонов C++ для среды выполнения Windows (WRL) позволяет выполнять разработку на низком уровне и использовать компоненты среды выполнения Windows. API Direct3D 11 для приложений UWP лучше всего использовать с интерфейсами и типами из этой библиотеки, такими как интеллектуальный указатель ([ComPtr](https://docs.microsoft.com/cpp/windows/comptr-class)). Дополнительно о WRL см. в документе [Библиотека шаблонов C++ для среды выполнения Windows (WRL)](https://docs.microsoft.com/cpp/windows/windows-runtime-cpp-template-library-wrl).

## <a name="change-your-coordinate-system"></a>Изменение системы координат


Одним из отличий, затрудняющих начало работы по переносу, является переход от традиционной правовинтовой системы координат OpenGL к используемой по умолчанию в Direct3D левовинтовой системе координат. Такое изменение в моделировании координат повлияет на многие части вашей игры — от установки и настройки буферов вершин до многих математических матричных функций. Необходимо внести следующие важные изменения.

-   Измените порядок вершин треугольников так, чтобы в Direct3D они обходились по часовой стрелке от передней. Например, если в конвейере OpenGL вершины индексируются как 0, 1 и 2, передавайте их в Direct3D как 0, 2, 1.
-   Используйте матрицу представления для масштабирования пространства с коэффициентом –1.0f в направлении Z, что в результате приведет к обращению направления координатной оси Z. Для этого измените знак значений в позициях M31, M32 и M33 в вашей матрице представления (при ее переносе к типу [**Matrix**](https://docs.microsoft.com/windows/desktop/direct3d9/matrix4x4)). Если M34 не равняется 0, также измените его знак.

Однако Direct3D может поддерживать и правовинтовую систему координат. DirectXMath предоставляет ряд функций, которые могут работать как в обеих системах координат, так и между ними. Эти функции можно использовать для сохранения некоторых из ваших исходных данных сетки и для обработки матриц. в том числе:

| Матричная функция DirectXMath                                                   | Описание                                                                                                                 |
|-------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------|
| [**XMMatrixLookAtLH**](https://docs.microsoft.com/windows/desktop/api/directxmath/nf-directxmath-xmmatrixlookatlh)                               | Строит матрицу представления для левовинтовой системы координат, используя позицию камеры, направление вверх и фокальную точку.       |
| [**XMMatrixLookAtRH**](https://docs.microsoft.com/windows/desktop/api/directxmath/nf-directxmath-xmmatrixlookatrh)                               | Строит матрицу представления для правовинтовой системы координат, используя позицию камеры, направление вверх и фокальную точку.      |
| [**XMMatrixLookToLH**](https://docs.microsoft.com/windows/desktop/api/directxmath/nf-directxmath-xmmatrixlooktolh)                               | Строит матрицу представления для левовинтовой системы координат, используя позицию камеры, направление вверх и направление камеры.  |
| [**XMMatrixLookToRH**](https://docs.microsoft.com/windows/desktop/api/directxmath/nf-directxmath-xmmatrixlooktorh)                               | Строит матрицу представления для правовинтовой системы координат, используя позицию камеры, направление вверх и направление камеры. |
| [**XMMatrixOrthographicLH**](https://docs.microsoft.com/windows/desktop/api/directxmath/nf-directxmath-xmmatrixorthographiclh)                   | Строит матрицу ортогональной проекции для левовинтовой системы координат.                                                 |
| [**XMMatrixOrthographicOffCenterLH**](https://docs.microsoft.com/windows/desktop/api/directxmath/nf-directxmath-xmmatrixorthographicoffcenterlh) | Строит пользовательскую матрицу ортогональной проекции для левовинтовой системы координат.                                           |
| [**XMMatrixOrthographicOffCenterRH**](https://docs.microsoft.com/windows/desktop/api/directxmath/nf-directxmath-xmmatrixorthographicoffcenterrh) | Строит пользовательскую матрицу ортогональной проекции для правовинтовой системы координат.                                          |
| [**XMMatrixOrthographicRH**](https://docs.microsoft.com/windows/desktop/api/directxmath/nf-directxmath-xmmatrixorthographicrh)                   | Строит матрицу ортогональной проекции для правовинтовой системы координат.                                                |
| [**XMMatrixPerspectiveFovLH**](https://docs.microsoft.com/windows/desktop/api/directxmath/nf-directxmath-xmmatrixperspectivefovlh)               | Строит левовинтовую матрицу перспективной проекции на основании поля зрения.                                                |
| [**XMMatrixPerspectiveFovRH**](https://docs.microsoft.com/windows/desktop/api/directxmath/nf-directxmath-xmmatrixperspectivefovrh)               | Строит правовинтовую матрицу перспективной проекции на основании поля зрения.                                               |
| [**XMMatrixPerspectiveLH**](https://docs.microsoft.com/windows/desktop/api/directxmath/nf-directxmath-xmmatrixperspectivelh)                     | Строит левовинтовую матрицу перспективной проекции.                                                                         |
| [**XMMatrixPerspectiveOffCenterLH**](https://docs.microsoft.com/windows/desktop/api/directxmath/nf-directxmath-xmmatrixperspectiveoffcenterlh)   | Строит пользовательскую версию левовинтовой матрицы перспективной проекции.                                                     |
| [**XMMatrixPerspectiveOffCenterRH**](https://docs.microsoft.com/windows/desktop/api/directxmath/nf-directxmath-xmmatrixperspectiveoffcenterrh)   | Строит пользовательскую версию правовинтовой матрицы перспективной проекции.                                                    |
| [**XMMatrixPerspectiveRH**](https://docs.microsoft.com/windows/desktop/api/directxmath/nf-directxmath-xmmatrixperspectiverh)                     | Строит правовинтовую матрицу перспективной проекции.                                                                        |

 

## <a name="opengl-es20-to-direct3d-11-porting-frequently-asked-questions"></a>Перенос OpenGL ES2.0 в Direct3D 11. Вопросы и ответы


-   Вопрос. «Как правило, можно ли поиск определенных строк или шаблоны в моем коде OpenGL и замените их эквиваленты Direct3D?»
-   Ответ: Нет. OpenGL ES 2.0 и Direct3D 11 представляют разные поколения моделирования конвейера графики. Хотя имеется поверхностное сходство в их концепциях и API, например контекст отрисовки и создание экземпляров шейдеров, вы должны изучить это руководство и справочник Direct3D 11, чтобы выбирать наилучшие решения при восстановлении вашего конвейера, а не пытаться установить соответствие один-к-одному. Однако при переносе от GLSL к HLSL создание набора общих псевдонимов для переменных GLSL, встроенных элементов и функций не только облегчит перенос, но и позволит вам поддерживать только один набор кода файлов шейдера.

 

 




