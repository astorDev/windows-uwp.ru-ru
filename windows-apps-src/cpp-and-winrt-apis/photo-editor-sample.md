---
description: Редактор фотографий — это пример приложения UWP, демонстрирующего возможности разработки с помощью проекции языка C++/WinRT. Пример приложения позволяет извлекать фотографии из библиотеки "Изображения", а затем редактировать выбранное изображение, используя разные фотографические эффекты.
title: Пример приложения на C++/WinRT — редактор фотографий
ms.date: 06/08/2018
ms.topic: article
keywords: Windows 10, uwp, стандартный, c++, cpp, winrt, проекция, пример, приложение, фотографии, редактор
ms.localizationpriority: medium
ms.openlocfilehash: 8c6f668ef3d92f968e75659b0ba1937abadb079c
ms.sourcegitcommit: a3dc929858415b933943bba5aa7487ffa721899f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/07/2018
ms.locfileid: "8783977"
---
# <a name="photo-editor-cwinrt-sample-application"></a>Пример приложения на C++/WinRT — редактор фотографий
Пример приложения можно клонировать или скачать из репозитория [Пример приложения на C++/WinRT — редактор фотографий](https://github.com/Microsoft/Windows-appsample-photo-editor) на GitHub.

Редактор фотографий — это пример приложения универсальной платформы Windows (UWP), демонстрирующего возможности разработки с помощью проекции языка [C++/WinRT](intro-to-using-cpp-with-winrt.md). Пример приложения позволяет извлекать фотографии из библиотеки **Изображения**, а затем редактировать выбранное изображение, используя разные фотографические эффекты. В исходном коде примера вы увидите несколько стандартных процедур, таких как [привязка данных](binding-property.md) и [асинхронные действия и операции](concurrency.md), выполняемых с помощью проекции C++/WinRT. Вот некоторые конкретные функции этого примера приложения.
    
- Использование стандартного синтаксиса C++17 и библиотек с помощью API-интерфейсов среды выполнения Windows (WinRT).
- Использование сопрограмм, включая использование co_await, co_return, [**IAsyncAction**](/uwp/api/windows.foundation.iasyncaction) и [**IAsyncOperation&lt;TResult&gt;**](/uwp/api/windows.foundation.iasyncoperation_tresult_).
- Создание и использование проецируемых типов настраиваемых классов среды выполнения Windows (класс среды выполнения) и типов реализации. Дополнительные сведения об этих терминах см. в разделах [Использование API-интерфейсов с помощью C++/WinRT](consume-apis.md) и [Создание API-интерфейсов с помощью C++/WinRT](author-apis.md).
- [Обработка событий](handle-events.md), в том числе использование автоматического отзыва маркеров событий.
- Использование внешнего пакета NuGet Win2D и [Windows::UI::Composition](/uwp/api/windows.ui.composition) для эффектов изображений.
- Связывание данных XAML, включая [расширение разметки {x: Bind}](https://docs.microsoft.com/windows/uwp/xaml-platform/x-bind-markup-extension).
- Стилизация XAML и настройка пользовательского интерфейса, включая [подключенные анимации](../design/motion/connected-animation.md).
