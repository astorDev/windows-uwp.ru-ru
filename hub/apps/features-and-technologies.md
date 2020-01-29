---
Description: Этот раздел поможет вам понять, как определенные основные функции Windows поддерживаются на различных платформах для приложений и как приступить к использованию этих функций в коде.
title: Функции и технологии
ms.topic: article
ms.date: 05/08/2019
ms.localizationpriority: medium
ms.author: mcleans
author: mcleanbyron
ms.openlocfilehash: ac779bf57e51b13051fa25293606daab05540fd1
ms.sourcegitcommit: 8a88a05ad89aa180d41a93152632413694f14ef8
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/24/2020
ms.locfileid: "76726027"
---
# <a name="features-and-technologies-for-windows-apps"></a>Функции и технологии для приложений для Windows

Вне зависимости от типа создаваемого приложения или применяемого устройства Windows поддерживает множество функций, которые являются основными компонентами для важных сценариев приложений. Некоторые из этих функций по-разному доступны для универсальной платформы Windows (UWP), Win32 (API Windows) и других платформ приложений. Приведенные ниже статьи помогут понять, как определенные функции Windows поддерживаются на различных платформах приложений и как приступить к использованию этих функций в коде.

Эта статья содержит адаптированный список статей с подробными сведениями о том, как получить доступ к важным функциям и технологиям Windows на платформах приложений UWP, Win32 (API Windows), WPF и Windows Forms. Полные сведения о функциях разработки каждой платформы см. в следующих ресурсах:

* [Документация по универсальной платформе Windows](/windows/uwp/index)
* [Build desktop Windows apps using the Win32 API](/windows/desktop/index) (Создание классических приложений Windows с помощью API Win32)
* [Windows Presentation Foundation (WPF)](https://docs.microsoft.com/dotnet/framework/wpf/index)
* [Windows Forms](https://docs.microsoft.com/dotnet/framework/winforms/index)

## <a name="key-windows-features-and-technologies"></a>Основные функции и технологии Windows

В следующих разделах приводятся некоторые важные функции и технологии Windows, которые позволяют создавать современные и привлекательные возможности для клиентов.

### <a name="windows-ink"></a>Windows Ink

![Ручка Surface](images/hero-small.png)  

Платформа Windows Ink — наряду с пером — обеспечивает естественный способ создания цифровых рукописных примечаний, рисунков и заметок. Платформа поддерживает получение входных данных дигитайзера в виде рукописных данных, создание рукописных данных, управление этими данными, отображение их в виде росчерков пера на устройстве вывода и преобразование этих росчерков в текст с помощью функции распознавания рукописного ввода.

Дополнительные сведения о различных способах использования платформы Windows Ink в приложениях для Windows см. в статье [Взаимодействие с пером и Windows Ink в приложениях UWP](/windows/uwp/design/input/pen-and-stylus-interactions).

### <a name="speech-interactions"></a>Взаимодействие с помощью голосовых функций

![начальный экран распознавания для ограничения на базе грамматического файла SGRS](images/speech-listening-initial.png)

![конечный экран распознавания для ограничения на базе грамматического файла SGRS](images/speech-listening-complete.png)

Windows предоставляет множество способов для интеграции функций распознавания речи и преобразования текста в речь (также известную как TTS или синтез речи) непосредственно в приложение. Речь может быть надежным и приятным способом взаимодействия пользователя с вашим приложением, который сможет дополнить или даже заменить взаимодействие с помощью клавиатуры, мыши, сенсорного ввода и жестов.

Дополнительные сведения о различных способах применения взаимодействия с помощью речи в приложениях для Windows 10 см. в [этой статье](speech.md).

### <a name="windows-ai"></a>ИИ Windows

![ИИ Windows](images/windows-ai.png)

Мы предлагаем несколько разных решений искусственного интеллекта, которые можно использовать для улучшения приложений Windows. С помощью машинного обучения Windows вы можете интегрировать обученные модели машинного обучения в приложения и локально запускать их на устройстве. Навыки компьютерного зрения Windows позволяют использовать готовые библиотеки для выполнения общих задач обработки изображений или создания собственных решений. DirectML предоставляет низкоуровневые интерфейсы API типа DirectX, которые позволяют использовать все возможности оборудования.

Дополнительные сведения о различных способах интеграции искусственного интеллекта в приложения для Windows см. в статье [ИИ Windows](https://docs.microsoft.com/windows/ai/).

## <a name="features-and-technologies-by-platform"></a>Функции и технологии по платформам

В следующих разделах приведены полезные ссылки для получения дополнительных сведений о том, как выполнять интеграцию с основными функциями и технологиями Windows из наших основных платформ приложений: UWP, Win32 (API Windows), WPF и Windows Forms.

### <a name="user-interface-and-accessibility"></a>Пользовательский интерфейс и специальные возможности

|  UWP  |  Win32 (API Windows) |  WPF  |  Windows Forms  |
|-------|----------------------|-------|-----------------|
| [Оформление](/windows/uwp/design/basics/)<br/><br/>[Макет](/windows/uwp/design/layout/)<br/><br/>[Элементы управления](/windows/uwp/design/controls-and-patterns/)<br/><br/>[Ввод](/windows/uwp/design/input/)<br/><br/>[Плитки](/windows/uwp/design/shell/tiles-and-notifications/creating-tiles)<br/><br/>[Визуальный уровень](/windows/uwp/composition/visual-layer)<br/><br/>[Платформа XAML](/windows/uwp/xaml-platform/)<br/><br/>[Запуск, возобновление и фоновые задачи](/windows/uwp/launch-resume/)<br/><br/>[Специальные возможности в Windows](/windows/uwp/design/accessibility/accessibility)<br/><br/>[Взаимодействия с помощью речи](https://docs.microsoft.com/windows/uwp/design/input/speech-interactions)<br/><br/> |  [Пользовательский интерфейс классического приложения](/windows/desktop/windows-application-ui-development)<br/><br/>[Среда рабочего стола и оболочка](/windows/desktop/user-interface)<br/><br/>[Элементы управления Windows](/windows/desktop/controls/window-controls)<br/><br/>[Элементы управления UWP в классических приложениях (XAML Islands)](/windows/apps/desktop/modernize/xaml-islands)<br/><br/>[Визуальный уровень UWP в классических приложениях](/windows/apps/desktop/modernize/visual-layer-in-desktop-apps)<br/><br/>[Windows и сообщения](/windows/desktop/winmsg/windowing)<br/><br/>[Меню и прочие ресурсы](/windows/desktop/menurc/resources)<br/><br/>[Приложения с высоким DPI](/windows/desktop/hidpi/high-dpi-desktop-application-development-on-windows)<br/><br/>[Специальные возможности](/windows/desktop/accessibility)<br/><br/>[Платформа Microsoft Speech Platform — пакет средств разработки программного обеспечения (SDK) (версия 11)](https://www.microsoft.com/download/details.aspx?id=27226)<br/><br/>[Пакет SDK для Microsoft Speech, версия 5.1](https://www.microsoft.com/download/details.aspx?id=10121)<br/><br/>  |  [Windows в приложениях WPF](https://docs.microsoft.com/dotnet/framework/wpf/app-development/windows-in-wpf-applications)<br/><br/>[Общие сведения о переходах](https://docs.microsoft.com/dotnet/framework/wpf/app-development/navigation-overview)<br/><br/>[XAML в WPF](https://docs.microsoft.com/dotnet/framework/wpf/advanced/xaml-in-wpf)<br/><br/>[Элементы управления](https://docs.microsoft.com/dotnet/framework/wpf/controls/)<br/><br/>[Программирование визуального слоя](https://docs.microsoft.com/dotnet/framework/wpf/graphics-multimedia/visual-layer-programming)<br/><br/>[Ввод](https://docs.microsoft.com/dotnet/framework/wpf/advanced/input-wpf)<br/><br/>[Специальные возможности](https://docs.microsoft.com/dotnet/framework/ui-automation/)<br/><br/>[Руководство по программированию System.Speech для .NET Framework](https://docs.microsoft.com/previous-versions/office/developer/speech-technologies/hh361625(v=office.14))<br/><br/>  | [Создание приложения Windows Forms](https://docs.microsoft.com/dotnet/framework/winforms/creating-a-new-windows-form)<br/><br/>[Элементы управления](https://docs.microsoft.com/dotnet/framework/winforms/controls/)<br/><br/>[Диалоговые окна](https://docs.microsoft.com/dotnet/framework/winforms/dialog-boxes-in-windows-forms)<br/><br/>[Ввод данных пользователем](https://docs.microsoft.com/dotnet/framework/winforms/user-input-in-windows-forms)<br/><br/>[Поддержка специальных возможностей в Windows Forms](https://docs.microsoft.com/dotnet/framework/winforms/advanced/windows-forms-accessibility)<br/><br/>[Руководство по программированию System.Speech для .NET Framework](https://docs.microsoft.com/previous-versions/office/developer/speech-technologies/hh361625(v=office.14))<br/><br/> |

### <a name="audio-video-and-graphics"></a>Звук, видео и графика

|  UWP  |  Win32 (API Windows) |  WPF  |  Windows Forms  |
|-------|----------------------|-------|-----------------|
| [Звук, видео и камера](/windows/uwp/audio-video-camera/)<br/><br/>[Воспроизведение мультимедиа](/windows/uwp/audio-video-camera/media-playback/)<br/><br/>[Визуальный уровень](/windows/uwp/composition/visual-layer)<br/><br/>[Платформа XAML](/windows/uwp/xaml-platform/) |  [Звук и видео](/windows/desktop/audio-and-video)<br/><br/>[Графика и игры](/windows/desktop/graphics-and-multimedia)<br/><br/>[DirectX](/windows/desktop/getting-started-with-directx-graphics)<br/><br/>[Direct2D](/windows/desktop/direct2d/direct2d-portal)<br/><br/>[Direct3D](/windows/desktop/direct3d)<br/><br/>[Windows GDI](/windows/desktop/gdi/windows-gdi)<br/><br/>[GDI+](/windows/desktop/gdiplus/-gdiplus-gdi-start)  |  [Графика](https://docs.microsoft.com/dotnet/framework/wpf/graphics-multimedia/graphics)<br/><br/>[Мультимедиа](https://docs.microsoft.com/dotnet/framework/wpf/graphics-multimedia/multimedia-overview)  |  [Графика и рисунки](https://docs.microsoft.com/dotnet/framework/winforms/advanced/graphics-and-drawing-in-windows-forms)<br/><br/>[Класс SoundPlayer](https://docs.microsoft.com/dotnet/framework/winforms/controls/soundplayer-class-overview)  |

### <a name="data-access-and-app-resources"></a>Доступ к данным и ресурсы приложения

|  UWP  |  Win32 (API Windows) |  WPF  |  Windows Forms  |
|-------|----------------------|-------|-----------------|
| [Доступ к данным](/windows/uwp/data-access/)<br/><br/>[Привязка данных](/windows/uwp/data-binding/)<br/><br/>[Файлы, папки и библиотеки](/windows/uwp/files/)<br/><br/>[Ресурсы приложения](/windows/uwp/app-resources/) |  [Доступ к данным и хранилище](/windows/desktop/data-access-and-storage)<br/><br/>[Локальные файловые системы](/windows/desktop/fileio/file-systems)<br/><br/>[Разделы общих сведений о ресурсах](/windows/desktop/menurc/resources-overviews)</li>  |  [Данные и моделирование](https://docs.microsoft.com/dotnet/framework/data/)<br/><br/>[Привязка данных](https://docs.microsoft.com/dotnet/framework/wpf/data/data-binding-wpf)<br/><br/>[Ресурсы в приложениях .NET](https://docs.microsoft.com/dotnet/framework/resources/)<br/><br/>[Ресурсы, содержимое и файлы данных приложения](https://docs.microsoft.com/dotnet/framework/wpf/app-development/wpf-application-resource-content-and-data-files)  |  [Данные и моделирование](https://docs.microsoft.com/dotnet/framework/data/)<br/><br/>[Привязка данных](https://docs.microsoft.com/dotnet/framework/winforms/windows-forms-data-binding)<br/><br/>[Ресурсы в приложениях .NET](https://docs.microsoft.com/dotnet/framework/resources/)<br/><br/>[Параметры приложения](https://docs.microsoft.com/dotnet/framework/winforms/advanced/application-settings-for-windows-forms)  |

### <a name="devices-documents-and-printing"></a>Устройства, документы и печать

|  UWP  |  Win32 (API Windows) |  WPF  |  Windows Forms  |
|-------|----------------------|-------|-----------------|
| [Включение возможностей устройств](/windows/uwp/devices-sensors/enable-device-capabilities)<br/><br/>[Перечисление устройств](/windows/uwp/devices-sensors/enumerate-devices)<br/><br/>[Датчики](/windows/uwp/devices-sensors/sensors)<br/><br/>[Bluetooth](/windows/uwp/devices-sensors/bluetooth)<br/><br/>[Печать и сканирование](/windows/uwp/devices-sensors/printing-and-scanning)<br/><br/>[NFC](/windows/uwp/devices-sensors/nfc) | [API датчика](/windows/desktop/sensorsapi/portal)<br/><br/>[Вывод на печать](/desktop/printdocs/printdocs-printing)<br/><br/>[Интерфейсы API UPnP](/desktop/upnp/universal-plug-and-play-start-page) |  [Печать и управление системой печати](https://docs.microsoft.com/dotnet/framework/wpf/advanced/printing-and-print-system-management)  |  [Поддержка печати](https://docs.microsoft.com/dotnet/framework/winforms/advanced/windows-forms-print-support)  |

### <a name="system-network-and-power"></a>Система, сеть и питание

|  UWP  |  Win32 (API Windows) |  WPF  |  Windows Forms  |
|-------|----------------------|-------|-----------------|
| [Перечисление устройств](/windows/uwp/devices-sensors/enumerate-devices)<br/><br/>[Получение сведений об аккумуляторе](/windows/uwp/devices-sensors/get-battery-info)<br/><br/>[Потоки и асинхронное программирование](/windows/uwp/threading-async/)<br/><br/>[Сетевые и веб-службы](/windows/uwp/networking/) | [Системные службы](/windows/desktop/system-services)<br/><br/>[Управление памятью](/windows/desktop/memory/memory-management)<br/><br/>[Управление питанием](/windows/desktop/power/power-management-portal)<br/><br/>[Процессы и потоки](/windows/desktop/procthread/processes-and-threads)<br/><br/>[Сеть и Интернет](/windows/desktop/networking)<br/><br/>[Сведения о системе Windows](/windows/desktop/sysinfo/windows-system-information) |  [Модель потоков](https://docs.microsoft.com/dotnet/framework/wpf/advanced/threading-model)<br/><br/>[Сетевое программирование в .NET Framework](https://docs.microsoft.com/dotnet/framework/network-programming/)  |  [Информация о системе](https://docs.microsoft.com/dotnet/framework/winforms/advanced/system-information-and-windows-forms)<br/><br/>[Управление питанием](https://docs.microsoft.com/dotnet/framework/winforms/advanced/power-management-in-windows-forms)<br/><br/>[Сетевое программирование в .NET Framework](https://docs.microsoft.com/dotnet/framework/network-programming/)<br/><br/>[Использование сети в приложениях Windows Forms](https://docs.microsoft.com/dotnet/framework/winforms/advanced/networking-in-windows-forms-applications)  |

### <a name="packaging-and-deployment"></a>Упаковка и развертывание

|  UWP  |  Win32 (API Windows) |  WPF  |  Windows Forms  |
|-------|----------------------|-------|-----------------|
| [Создание пакетов приложений](/windows/uwp/packaging/)<br/><br/>[MSIX](https://docs.microsoft.com/windows/msix/)<br/><br/>[Схема манифеста пакета приложений](https://docs.microsoft.com/uwp/schemas/appxpackage/uapmanifestschema/schema-root) | [Упаковка классических приложений Windows (MSIX)](/windows/msix/desktop/desktop-to-uwp-root)<br/><br/>[Установка и обслуживание приложения](/windows/desktop/application-installing-and-servicing)<br/><br/>[Установщик Windows](/windows/desktop/msi/windows-installer-portal) |  [Упаковка классических приложений Windows (MSIX)](/windows/msix/desktop/desktop-to-uwp-root)<br/><br/>[Развертывание .NET Framework и приложений](https://docs.microsoft.com/dotnet/framework/deployment/)<br/><br/>[Развертывание приложения WPF](https://docs.microsoft.com/dotnet/framework/wpf/app-development/deploying-a-wpf-application-wpf)  |  [Упаковка классических приложений Windows (MSIX)](/windows/msix/desktop/desktop-to-uwp-root)<br/><br/>[Развертывание .NET Framework и приложений](https://docs.microsoft.com/dotnet/framework/deployment/)<br/><br/>[Развертывание ClickOnce для форм Windows Forms](https://docs.microsoft.com/dotnet/framework/winforms/clickonce-deployment-for-windows-forms)  |
