---
Description: Добавляйте современные пользовательские интерфейсы XAML, создавайте MSIX-пакеты и включайте другие актуальные компоненты в свои классические приложения.
title: Модернизация классических приложений для Windows
ms.topic: article
ms.date: 04/17/2019
ms.author: mcleans
author: mcleanbyron
ms.localizationpriority: medium
ms.openlocfilehash: 47cb292b1f5aeed9473cac0f27074f449dc67032
ms.sourcegitcommit: b8087f8b6cf8367f8adb7d6db4581d9aa47b4861
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/27/2019
ms.locfileid: "67414109"
---
# <a name="modernize-your-desktop-apps"></a>Модернизация классических приложений

В Windows 10 и универсальной платформе Windows (UWP) предлагается много функций для реализации современных функций в классических приложениях. Большинство этих функций доступны в виде модульных компонентов, которые вы можете использовать в классических приложениях по своему усмотрению, не переписывая код приложения для другой платформы. Вы можете выбрать, какие компоненты Windows 10 и UWP нужно внедрить для улучшения существующих классических приложений.

В этой статье описаны функции Windows 10 и UWP, которые можно использовать в классических приложениях уже сегодня. Инструкции по модернизации существующего приложения для использования многих из функций, описанных в этой статье, приведены в руководстве по [модернизации приложения WPF](modernize-wpf-tutorial.md).

> [!NOTE]
> Нужна помощь в миграции классических приложений в Windows 10? [Служба Assure для классических приложений](https://docs.microsoft.com/FastTrack/win-10-desktop-app-assure) предоставляет прямую бесплатную поддержку для разработчиков при миграции приложений в Windows 10. Эта программа доступна для всех независимых поставщиков программного обеспечения и соответствующих предприятий. Дополнительные сведения о соответствии критериям, а также о самой программе см. здесь: [https://aka.ms/DesktopAppAssure](https://aka.ms/DesktopAppAssure). Чтобы начать работу прямо сейчас, [отправьте запрос](https://aka.ms/DesktopAppAssureRequest).

## <a name="msix-packages"></a>Пакеты MSIX

MSIX — это современный универсальный формат пакетов для упаковки любых приложений для Windows, включая приложения UWP, WPF, Windows Forms и Win32. Этот безопасный и надежный формат упаковки сочетает в себе преимущества технологий установки с использованием MSI-файлов, APPX-файлов, App-V и ClickOnce.

Упаковав классические приложения для Windows в MSIX-пакеты, вы получаете доступ к надежным возможностям установки и обновления, управляемой модели безопасности с гибкой системой возможностей, поддержке Microsoft Store, возможностям управления предприятием и многим пользовательским моделям распространения.

Дополнительные сведения см. в статье об [упаковке классических приложений](/windows/msix/desktop/desktop-to-uwp-root) в документации по MSIX.

## <a name="net-core-3"></a>.NET Core 3

.NET Core 3 — это следующий основной выпуск .NET Core. Основным преимуществом этого предстоящего выпуска является поддержка классических приложений для Windows, включая приложения Windows Forms и WPF. Используя .NET Core 3, вы сможете запускать новые и существующие классические приложения для Windows и пользоваться всеми преимуществами этой версии. В приложениях для Windows Forms и WPF, предназначенных для .NET Core 3, вы сможете использовать элементы управления UWP, доступные в [XAML Islands](xaml-islands.md).

Дополнительные сведения доступны в следующих статьях.

* [Объявление о выпуске .NET Core 3.0 (предварительная версия 1)](https://devblogs.microsoft.com/dotnet/announcing-net-core-3-preview-1-and-open-sourcing-windows-desktop-frameworks/)
* [Объявление о выпуске .NET Core 3.0 (предварительная версия 2)](https://devblogs.microsoft.com/dotnet/announcing-net-core-3-preview-2/)
* [Объявление о выпуске .NET Core 3.0 (предварительная версия 2)](https://devblogs.microsoft.com/dotnet/announcing-net-core-3-preview-3/)
* [Объявление о выпуске .NET Core 3.0 (предварительная версия 4)](https://devblogs.microsoft.com/dotnet/announcing-net-core-3-preview-4/)
* [Новые возможности в .NET Core 3.0](https://docs.microsoft.com/dotnet/core/whats-new/dotnet-core-3-0)

## <a name="uwp-apis"></a>API UWP

Для интеграции современных возможностей, которые могут заинтересовать пользователей Windows 10, большинство API UWP можно вызывать напрямую из классических приложений WPF, Windows Forms или C++ Win32. Например, вы можете вызывать API UWP, чтобы добавлять всплывающие уведомления в приложение.

Дополнительные сведения см. в статье [Call UWP APIs in desktop apps](desktop-to-uwp-enhance.md) (Вызов API UWP из классических приложений).

## <a name="host-uwp-controls-xaml-islands"></a>Добавление элементов управления UWP (XAML Islands)

Начиная с Windows 10 версии 1903, можно добавить [элемент управления XAML платформы UWP](/windows/uwp/design/controls-and-patterns/controls-by-function) в любой элемент пользовательского интерфейса приложения WPF, Windows Forms или C++ Win32, который связан с дескриптором окна (HWND). Это означает, что новые функции UWP, например [рукописный ввод Windows](/windows/uwp/design/input/pen-and-stylus-interactions), можно объединить с элементами управления, поддерживающими [систему Fluent Design](/windows/uwp/design/fluent-design-system/index), в окнах и других средствах для отображения в классических приложениях. Такой сценарий разработки иногда называют *XAML Islands*.

Дополнительные сведения см. в статье [Host UWP XAML controls in desktop apps (XAML Islands)](xaml-islands.md) (Добавление элементов управления XAML платформы UWP в классические приложения (XAML Islands)).

## <a name="use-the-visual-layer-in-desktop-apps"></a>Использование визуального уровня в классических приложениях

Теперь API можно использовать и в классических приложениях, созданных не на платформе UWP, чтобы улучшить внешний вид и функции этих приложений (WPF, Windows Forms и C++ Win32) и воспользоваться последними функциями пользовательского интерфейса Windows 10, которые доступны только через UWP. Это удобно, когда нужно создать пользовательские функции, которые невозможно реализовать с помощью встроенных элементов управления UWP, добавляемых с помощью XAML Islands.

Дополнительные сведения см. в статье [Using the Visual layer in desktop apps](visual-layer-in-desktop-apps.md) (Использование визуального уровня в классических приложениях).

## <a name="additional-features-available-to-packaged-apps"></a>Дополнительные функции, доступные для упакованных приложений

Некоторые современные возможности Windows 10 доступны только в классических приложениях, упакованных в [MSIX-пакет](/windows/msix/desktop/desktop-to-uwp-root). В таких приложениях можно использовать расширения пакета, компоненты UWP и API UWP, требующие идентификатор пакета.

Дополнительные сведения см. в статье [Features that require package identity](modernize-packaged-apps.md) (Функции, которым требуется идентификатор пакета).

<a id="desktop-uwp-controls"/>

## <a name="uwp-controls-optimized-for-desktop-apps"></a>Элементы управления UWP, оптимизированные для классических приложений

Приведенные ниже новые и обновленные элементы управления UWP предоставляют средства рабочего стола как часть [системы Fluent Design](/windows/uwp/design/fluent-design-system/index). Эти возможности можно применять, независимо от того, создаете ли вы приложение UWP, предназначенное только для компьютеров, или хотите использовать элементы управления UWP в классическом приложении WPF, Windows Forms или C++ Win32. Эти элементы управления появились в Windows 10 версии 1809, (обновление за октябрь 2018 г. или версия 10.0.17763).

| Элемент управления |  Описание |
|------ |--------------|
| [MenuBar](https://docs.microsoft.com/windows/uwp/design/controls-and-patterns/menus#create-a-menu-bar) | Обеспечивает простой и быстрый способ выводить набор команд в приложениях, которым могут понадобиться дополнительные функции упорядочения или группирования, недоступные в **CommandBar**. |
| [DropDownButton](https://docs.microsoft.com/windows/uwp/design/controls-and-patterns/buttons#create-a-drop-down-button) | Отображение шеврона в качестве визуального индикатора, который свидетельствует о наличии вложенного всплывающего меню с дополнительными параметрами.  |
| [SplitButton](https://docs.microsoft.com/windows/uwp/design/controls-and-patterns/buttons#create-a-split-button) | Кнопка, состоящая из двух частей, которые можно использовать по-отдельности. Одна часть представляет собой стандартную кнопку, которая вызывает немедленное действие. Другая часть позволяет вызывать всплывающий элемент с дополнительными параметрами, которые пользователь может выбрать.|
| [ToggleSplitButton](https://docs.microsoft.com/windows/uwp/design/controls-and-patterns/buttons#create-a-toggle-split-button) | Кнопка, состоящая из двух частей, которые можно использовать по-отдельности. Одна часть выполняет функцию переключателя. Другая часть позволяет вызывать всплывающий элемент с дополнительными параметрами, которые пользователь может выбрать. |
| [CommandBarFlyout](https://docs.microsoft.com/windows/uwp/design/controls-and-patterns/command-bar-flyout) |  Позволяет отобразить стандартные задачи пользователя в контексте элемента на холсте пользовательского интерфейса. |
| [ComboBox](https://docs.microsoft.com/windows/uwp/design/controls-and-patterns/combo-box#make-a-combo-box-editable) | Теперь поле со списком можно сделать редактируемым, чтобы пользователь мог ввести значения, которые не указаны в элементе управления.  |
| [TreeView](https://docs.microsoft.com/windows/uwp/design/controls-and-patterns/tree-view) | Теперь можно настроить представление в виде дерева для реализации привязки данных, шаблонов элементов и перетаскивания элементов.  |
| [DataGridView](https://docs.microsoft.com/windows/communitytoolkit/controls/datagrid) |   Позволяет гибко отображать коллекцию данных в строках и столбцах. Этот элемент управления доступен в [наборе средств сообщества Windows](https://docs.microsoft.com/windows/uwpcommunitytoolkit/).  |

## <a name="windows-ui-library"></a>Библиотека пользовательского интерфейса Windows

Библиотека пользовательского интерфейса Windows — это набор пакетов NuGet, предоставляющих новые элементы управления и прочие элементы пользовательского интерфейса для приложений UWP. API библиотеки пользовательского интерфейса Windows работают и в более ранних версиях Windows 10, поэтому приложение будет выполняться, даже если последняя версия Windows 10 не используется. Это позволяет внедрять новые элементы управления по мере их выпуска в библиотеке пользовательского интерфейса Windows, не беспокоясь о включении проверки версии или условного кода XAML.

Дополнительные сведения см. в статье о [библиотеке пользовательского интерфейса Windows](https://docs.microsoft.com/uwp/toolkits/winui/).

## <a name="other-technologies-for-modern-desktop-apps"></a>Другие технологии для современных классических приложений

### <a name="microsoft-graph"></a>Microsoft Graph

Microsoft Graph — это коллекция API, которые можно использовать для создания приложений для организаций и клиентов, взаимодействующих с данными миллионов пользователей. Microsoft Graph предоставляет REST API и клиентские библиотеки для доступа к данным в следующих службах:
* Azure Active Directory
* службы Office 365: SharePoint, OneDrive, Outlook или Exchange, Microsoft Teams, OneNote, Планировщик и Excel;
* службы Enterprise Mobility + Security: Identity Manager, Intune, Advanced Threat Analytics и Advanced Threat Protection;
* службы Windows 10: действия и устройства.

Дополнительные сведения см. в документации по [Microsoft Graph](https://developer.microsoft.com/graph/docs/concepts/overview).

### <a name="adaptive-cards"></a>Адаптивные карточки

Адаптивные карточки — это открытая платформа для кроссплатформенных приложений, которая позволяет привычным и согласованным способом обмениваться между устройствами и платформами содержимым пользовательского интерфейса на основе карточек.

Дополнительные сведения см. в документации по [адаптивным карточкам](https://docs.microsoft.com/adaptive-cards/).