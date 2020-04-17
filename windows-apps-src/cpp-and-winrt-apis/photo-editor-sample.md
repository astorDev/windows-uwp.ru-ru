---
description: Редактор фотографий — это пример приложения UWP, демонстрирующего возможности разработки с помощью проекции языка C++/WinRT. Пример приложения позволяет извлекать фотографии из библиотеки изображений, а затем редактировать выбранное изображение, используя разные фотоэффекты.
title: Пример приложения C++/WinRT — редактор фотографий
ms.date: 04/23/2019
ms.topic: article
keywords: windows 10, uwp, standard, c++, cpp, winrt, projection, sample, application, photo, editor
ms.localizationpriority: medium
ms.openlocfilehash: dcefe2ad8321ae85fcb814bbaead0bb0e5373300
ms.sourcegitcommit: 8b7b677c7da24d4f39e14465beec9c4a3779927d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/13/2020
ms.locfileid: "81266912"
---
# <a name="photo-editor-cwinrt-sample-application"></a>Пример приложения C++/WinRT — редактор фотографий

> [!NOTE]
> Этот пример предназначен и протестирован для использования в Windows 10, версия 1903 (10.0; сборка 18362) и Visual Studio 2019. При желании можно использовать свойства проекта, чтобы настроить проекты для использования в Windows 10, версия 1809 (10.0; сборка 17763), а также открывать примеры в Visual Studio 2017.

См. также о клонировании или скачивании примера приложения [Photo Editor C++/WinRT](/samples/microsoft/windows-appsample-photo-editor/photo-editor-cwinrt-sample-application/) в коллекции примеров кода.

Редактор фотографий — это пример приложения универсальной платформы Windows (UWP) для демонстрации возможностей разработки с помощью проекции языка [C++/WinRT](intro-to-using-cpp-with-winrt.md). Пример приложения позволяет извлекать фотографии из библиотеки **Pictures**, а затем редактировать выбранное изображение, используя разные фотографические эффекты. В исходном коде примера вы увидите несколько стандартных процедур, таких как [привязка данных](binding-property.md) и [асинхронные действия и операции](concurrency.md), выполняемых с помощью проекции C++/WinRT. Вот некоторые конкретные функции этого примера приложения.

- Использование стандартного синтаксиса C++17 и библиотек с помощью API среды выполнения Windows (WinRT).
- Использование сопрограмм, включая co_await, co_return, [**IAsyncAction**](/uwp/api/windows.foundation.iasyncaction) и [**IAsyncOperation&lt;TResult&gt;** ](/uwp/api/windows.foundation.iasyncoperation-1).
- Создание и использование проецируемых типов настраиваемых классов среды выполнения Windows (класс среды выполнения) и типов реализации. См. подробнее об этих терминах в разделах [Использование API с помощью C++/WinRT](consume-apis.md) и [Создание API с помощью C++/WinRT](author-apis.md).
- [Обработка событий](handle-events.md), в том числе использование автоматического отзыва маркеров событий.
- Использование внешнего пакета NuGet Win2D и [Windows::UI::Composition](/uwp/api/windows.ui.composition) для эффектов изображений.
- Связывание данных XAML, включая [расширение разметки {x: Bind}](https://docs.microsoft.com/windows/uwp/xaml-platform/x-bind-markup-extension).
- Стилизация XAML и настройка пользовательского интерфейса, включая [подключенные анимации](../design/motion/connected-animation.md).
