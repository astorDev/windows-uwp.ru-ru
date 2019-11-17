---
Description: Узнайте, как приступить к созданию классических приложений для компьютеров с Windows, а также как выбрать подходящую платформу для новых приложений и модернизировать существующие приложения для Windows 10.
title: Создание классических приложений для компьютеров с Windows
ms.topic: article
ms.date: 11/04/2019
keywords: windows win32, desktop development
ms.author: mcleans
author: mcleanbyron
ms.localizationpriority: medium
ms.openlocfilehash: c27826980127674b4a4356af8a3c36056e86bd83
ms.sourcegitcommit: cf88f5e8e1de476ed2635e791a5e5e82ae4bd8cf
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/13/2019
ms.locfileid: "74056861"
---
# <a name="build-desktop-apps-for-windows-pcs"></a>Создание классических приложений для компьютеров с Windows

Эта статья содержит сведения, необходимые, чтобы приступить к созданию классических приложений для Windows или обновления существующих классических приложений для внедрения новейших возможностей Windows 10.

## <a name="platforms-for-desktop-apps"></a>Платформы для классических приложений

Существуют четыре основные платформы для создания классических приложений для компьютеров с Windows. Каждая платформа предоставляет модель приложения, определяющую жизненный цикл приложения, полный набор элементов управления пользовательского интерфейса и доступ к исчерпывающему набору управляемых или собственных интерфейсов API для использования функций Windows.

Эти платформы представлены в следующей таблице. Подробное сравнение этих платформ и дополнительные материалы по каждой платформе доступны в разделе [Выбор платформы для приложения](choose-your-platform.md).

<br/>

<table>
<colgroup>
<col width="20%" />
<col width="60%" />
<col width="20%" />
</colgroup>
<thead>
<tr class="header">
<th>Платформа</th>
<th>Описание</th>
<th>Документация и ресурсы</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td><a href="https://docs.microsoft.com/windows/uwp/">Универсальная платформа Windows (UWP)</a></td>
<td><p>Передовая платформа для приложений и игр для, предназначенных для Windows 10. Вы можете создавать приложения UWP, которые используют только элементы управления и интерфейсы API универсальной платформы Windows (UWP), или использовать эти элементы управления и API в классических приложениях, созданных с помощью одной из других платформ.</p></td>
<td><a href="/windows/uwp/get-started/">Начало работы</a><br/><a href="/uwp/">Справочник по API</a><br/><a href="https://github.com/Microsoft/Windows-universal-samples">Примеры</a></td>
</tr>
<tr class="even">
<td><a href="https://docs.microsoft.com/windows/win32/">Win32</a>.</td>
<td><p>Предпочтительная платформа для собственных приложений C/C++ для Windows, которым требуется прямой доступ к Windows и оборудованию.</p></td>
<td><a href="/windows/win32/desktop-programming/">Начало работы</a><br/><a href="/windows/win32/apiindex/windows-api-list/">Справочник по API</a><br/><a href="https://github.com/Microsoft/Windows-classic-samples">Примеры</a></td>
</tr>
<tr class="odd">
<td><a href="https://docs.microsoft.com/dotnet/framework/wpf/">WPF</a></td>
<td><p>Общепризнанная платформа на основе .NET для преимущественно графических приложений для Windows, использующих модель пользовательского интерфейса XAML. Эти приложения могут быть нацелены на <a href="https://docs.microsoft.com/dotnet/core/whats-new/dotnet-core-3-0">.NET Core 3</a> или полную платформу .NET Framework.</p></td>
<td><a href="/dotnet/framework/wpf/getting-started/">Начало работы</a><br/><a href="https://docs.microsoft.com/dotnet/api/index">Справочные материалы по API (.NET)</a><br/><a href="https://github.com/Microsoft/WPF-Samples">Примеры</a></td>
</tr>
<tr class="even">
<td><a href="https://docs.microsoft.com/dotnet/framework/winforms/">Windows Forms</a></td>
<td><p>Платформа на основе .NET, предназначенная для управляемых бизнес-приложений, использующих упрощенную модель пользовательского интерфейса. Эти приложения могут быть нацелены на <a href="https://docs.microsoft.com/dotnet/core/whats-new/dotnet-core-3-0">.NET Core 3</a> или полную платформу .NET Framework.</p></td>
<td><a href="/dotnet/framework/winforms/getting-started-with-windows-forms">Начало работы</a><br/><a href="https://docs.microsoft.com/dotnet/api/index">Справочные материалы по API (.NET)</a><br/><a href="https://code.msdn.microsoft.com/windowsdesktop/site/search?f%5B0%5D.Type=Technology&f%5B0%5D.Value=Windows%20Forms">Примеры</a></td>
</tr>
</tbody>
</table>

## <a name="update-existing-desktop-apps-for-windows-10"></a>Обновление существующих классических приложений для Windows 10

Если у вас есть классические приложения WPF, Windows Forms или собственные классические приложения Win32, в Windows 10 и универсальной платформе Windows (UWP) предлагается много возможностей для реализации современных функций в имеющихся приложениях. Большинство этих функций доступны в виде модульных компонентов, которые можно внедрить в приложение в своем собственном темпе, не переписывая код приложения для другой платформы.

Ниже приведено лишь несколько функций, позволяющих улучшить существующие классические приложения.

* Используйте [MSIX](/windows/msix/) для упаковки и развертывания классических приложений. MSIX — это современный формат пакета приложения для Windows, позволяющий упаковывать любые приложения для Windows. Этот безопасный и надежный формат упаковки сочетает в себе преимущества технологий установки с использованием MSI-файлов, APPX-файлов, App-V и ClickOnce.
* Интегрируйте возможности Windows 10 в свое классическое приложение с помощью [расширений пакетов](/windows/apps/desktop/modernize/desktop-to-uwp-extensions). Например, нацельте плитки "Пуск" на свое приложение, предоставьте к нему общий доступ или отправляйте всплывающие уведомления из приложения.
* Используйте [острова XAML](/windows/apps/desktop/modernize/xaml-islands) для размещения элементов управления XAML UWP в классическом приложении. Многие последние функции пользовательского интерфейса Windows 10 доступны только для элементов управления XAML UWP.

Дополнительные сведения доступны в приведенных ниже статьях.

<br/>

| Статья | Описание |
|---------|-------------|
| [Модернизация классических приложений](/windows/apps/desktop/modernize) | Описываются последние возможности разработки для Windows 10 и UWP, которые можно использовать в любом классическом приложении, включая приложения WPF, Windows Forms и приложения Win32 на C++. |
| [Руководство. Модернизация приложения WPF](/windows/apps/desktop/modernize/modernize-wpf-tutorial) | Следуйте пошаговым инструкциям, чтобы модернизировать существующий пример бизнес-приложения WPF, добавив элементы управления рукописным вводом и календарем UWP в приложение и упаковав его в пакет MSIX.  |

## <a name="create-new-desktop-apps"></a>Создание классических приложений

Ниже приведены некоторые ресурсы, которые помогут вам приступить к созданию классического приложения для Windows.

<br/>

| Статья | Описание |
|---------|-------------|
| [Выбор платформы для приложения](choose-your-platform.md) | Приводится подробное сравнение основных платформ классических приложений, а также рекомендации по выбору правильной платформы в зависимости от ваших потребностей. В этой статье также доступны полезные ссылки на документацию по каждой из платформ. |
| [Модернизация классических приложений](/windows/apps/desktop/modernize) | Описываются последние возможности разработки для Windows 10 и UWP, которые можно использовать в любом классическом приложении, включая приложения WPF, Windows Forms и приложения Win32 на C++. |
| [Функции и технологии](/windows/apps/features-and-technologies) | Содержит общие сведения о функциях Windows, предоставляемых каждой из основных платформ классических приложений, а также ссылки на сопутствующую документацию. |

## <a name="related-documentation-and-technologies"></a>Сопутствующая документация и технологии

| Ресурс | Описание |
|---------|-------------|
| [.NET Core 3.0](https://docs.microsoft.com/dotnet/core/whats-new/dotnet-core-3-0) | Узнайте о последних возможностях .NET Core 3.0, включая улучшения для приложений WPF и Windows Forms. |
| [Руководство по разработке классических приложений WPF для .NET Core 3.0](https://docs.microsoft.com/dotnet/desktop-wpf/overview/index) | Разрабатывайте приложения WPF, нацеленные на .NET Core 3.0, а не на полную платформу .NET Framework.  |
| [Azure](https://docs.microsoft.com/azure/) | Расширьте возможности приложений с помощью облачных служб Azure. |
| [Visual Studio](https://docs.microsoft.com/visualstudio/) | Узнайте, как использовать Visual Studio для разработки приложений и служб. |
| [MSIX](https://docs.microsoft.com/windows/msix/) | Упакуйте и разверните любое приложение для Windows, воспользовавшись современным универсальным форматом пакета. |
| [Средства ИИ Windows](https://docs.microsoft.com/windows/ai/) | Используйте ИИ Windows, чтобы создавать интеллектуальные решения для выполнения сложных задач в приложениях. |
| [Контейнеры Windows](https://docs.microsoft.com/virtualization/windowscontainers/) | Упаковывайте приложения с зависимостями в быстрые, полностью изолированные среды Windows. |
| [Прогрессивные веб-приложения](https://docs.microsoft.com/microsoft-edge/progressive-web-apps) | Преобразовывайте веб-приложения в прогрессивные веб-приложения, которые можно распространять и запускать как приложения UWP в Windows 10. |
| [Xamarin](https://docs.microsoft.com/xamarin/) | Создавайте кросс-платформенные приложения для Windows, Android, iOS и macOS с помощью кода .NET и пользовательских интерфейсов, зависящих от платформы. |
| [Архив с документацией по Windows 8.x и более ранних версий](https://docs.microsoft.com/previous-versions/windows/) | Обращайтесь к архивной документации по созданию приложений для Windows 8. x и более ранних версий. |
