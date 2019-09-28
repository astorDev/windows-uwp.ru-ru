---
Description: Этот раздел поможет вам понять, как определенные ключевые функции Windows поддерживаются на разных платформах приложений и как приступить к использованию функций в коде.
title: Функции и технологии
ms.topic: article
ms.date: 05/08/2019
ms.localizationpriority: medium
ms.author: mcleans
author: mcleanbyron
ms.openlocfilehash: 4fb49b5145350360be3b86c73110762cfa30e9db
ms.sourcegitcommit: a20457776064c95a74804f519993f36b87df911e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/27/2019
ms.locfileid: "71339344"
---
# <a name="features-and-technologies-for-windows-apps"></a>Функции и технологии для приложений Windows

Вне зависимости от типа создаваемого приложения или устройства Windows поддерживает множество функций, которые являются ключевыми компонентами для важных сценариев приложений. Некоторые из этих функций доступны для универсальная платформа Windows (UWP), Win32 (Windows API) и других платформ приложений различными способами. Следующие статьи помогут понять, как некоторые функции Windows поддерживаются на различных платформах приложений, и как приступить к использованию функций в коде.

В этой статье приводится список статей с подробными сведениями о том, как получить доступ к важным функциям и технологиям Windows на платформах приложений UWP, Win32 (Windows API), WPF и Windows Forms. Полные сведения о функциях разработки каждой платформы см. в следующих ресурсах:

* [Документация по UWP](/windows/uwp/index)
* [Документация по Win32 (Windows API)](/windows/desktop/index)
* [Документация по WPF](https://docs.microsoft.com/dotnet/framework/wpf/index)
* [Документация по Windows Forms](https://docs.microsoft.com/dotnet/framework/winforms/index)

## <a name="key-windows-features-and-technologies"></a>Ключевые функции и технологии Windows

В следующих разделах приводятся некоторые важные функции и технологии Windows, позволяющие создавать современные и привлекательные возможности для клиентов.

### <a name="windows-ink"></a>Windows Ink

![Ручка Surface](images/hero-small.png)  

Платформа Windows Ink — наряду с пером — обеспечивает естественный способ создания цифровых рукописных примечаний, рисунков и заметок. Платформа поддерживает получение входных данных дигитайзера в виде рукописных данных, создание рукописных данных, управление этими данными, отображение их в виде росчерков пера на устройстве вывода и преобразование этих росчерков в текст с помощью функции распознавания рукописного ввода.

Дополнительные сведения о различных способах использования рукописного ввода Windows в приложениях Windows см. в разделе [Рукописный ввод Windows](/windows/uwp/design/input/pen-and-stylus-interactions).

### <a name="speech-interactions"></a>Взаимодействие с помощью голосовых функций

![начальный экран распознавания для ограничения на базе грамматического файла SGRS](images/speech-listening-initial.png)

![конечный экран распознавания для ограничения на базе грамматического файла SGRS](images/speech-listening-complete.png)

Windows предоставляет множество способов интеграции распознавания речи и преобразования текста в речь (также известного как TTS или синтеза речи) непосредственно в взаимодействие с пользователем приложения. Речь может быть надежной и приятной, чтобы люди могли взаимодействовать с приложением, дополнить или даже заменить, клавиатуру, мышь, сенсорный ввод и жесты.

Дополнительные сведения о различных способах использования речевого взаимодействия в приложениях Windows см. в разделе [взаимодействие речи](/windows/uwp/design/input/speech-interactions).

### <a name="windows-ai"></a>ИИ Windows

![ИИ Windows](images/windows-ai.png)

Мы предлагаем несколько различных решений искусственного интеллекта, которые можно использовать для улучшения приложений Windows. С Машинное обучение Windows вы можете интегрировать в приложения обученные модели машинного обучения и запускать их локально на устройстве. Навыки работы с концепцией Windows позволяют использовать готовые библиотеки для выполнения общих задач обработки изображений или создания собственных пользовательских решений. Директмл предоставляет интерфейсы API в стиле DirectX низкого уровня, позволяющие использовать все преимущества оборудования.

Дополнительные сведения о различных способах интеграции AI в приложениях Windows см. в разделе [Windows AI](https://docs.microsoft.com/windows/ai/).

## <a name="features-and-technologies-by-platform"></a>Функции и технологии по платформам

В следующих разделах приведены полезные ссылки на дополнительные сведения о том, как интегрироваться с основными функциями и технологиями Windows из наших основных платформ приложений: UWP, Win32 (Windows API), WPF и Windows Forms.

### <a name="user-interface-and-accessibility"></a>Пользовательский интерфейс и специальные возможности

|  UWP  |  Win32 (Windows API) |  WPF  |  Windows Forms  |
|-------|----------------------|-------|-----------------|
| [Оформление](/windows/uwp/design/basics/)<br/><br/>[Макет](/windows/uwp/design/layout/)<br/><br/>[Элементы управления](/windows/uwp/design/controls-and-patterns/)<br/><br/>[Входной](/windows/uwp/design/input/)<br/><br/>[Плитки](/windows/uwp/design/shell/tiles-and-notifications/creating-tiles)<br/><br/>[Визуальный уровень](/windows/uwp/composition/visual-layer)<br/><br/>[Платформа XAML](/windows/uwp/xaml-platform/)<br/><br/>[Запуск, возобновление и фоновые задачи](/windows/uwp/launch-resume/)<br/><br/>[Специальные возможности Windows](/windows/uwp/design/accessibility/accessibility)<br/><br/>  |  [Пользовательский интерфейс рабочего стола](/windows/desktop/windows-application-ui-development)<br/><br/>[Среда рабочего стола и оболочка](/windows/desktop/user-interface)<br/><br/>[Элементы управления Windows](/windows/desktop/controls/window-controls)<br/><br/>[Элементы управления UWP в классических приложениях (XAML Islands)](/windows/apps/desktop/modernize/xaml-islands)<br/><br/>[Визуальный уровень UWP в классических приложениях](/windows/apps/desktop/modernize/visual-layer-in-desktop-apps)<br/><br/>[Окна и сообщения](/windows/desktop/winmsg/windowing)<br/><br/>[Меню и другие ресурсы](/windows/desktop/menurc/resources)<br/><br/>[Высокое разрешение DPI](/windows/desktop/hidpi/high-dpi-desktop-application-development-on-windows)<br/><br/>[Специальные возможности](/windows/desktop/accessibility)<br/><br/>  |  [Windows в WPF](https://docs.microsoft.com/dotnet/framework/wpf/app-development/windows-in-wpf-applications)<br/><br/>[Общие сведения о навигации](https://docs.microsoft.com/dotnet/framework/wpf/app-development/navigation-overview)<br/><br/>[XAML в WPF](https://docs.microsoft.com/dotnet/framework/wpf/advanced/xaml-in-wpf)<br/><br/>[Элементы управления](https://docs.microsoft.com/dotnet/framework/wpf/controls/)<br/><br/>[Программирование визуального слоя](https://docs.microsoft.com/dotnet/framework/wpf/graphics-multimedia/visual-layer-programming)<br/><br/>[Входной](https://docs.microsoft.com/dotnet/framework/wpf/advanced/input-wpf)<br/><br/>[Специальные возможности](https://docs.microsoft.com/dotnet/framework/ui-automation/)<br/><br/>  | [Создание формы Windows Forms](https://docs.microsoft.com/dotnet/framework/winforms/creating-a-new-windows-form)<br/><br/>[Элементы управления](https://docs.microsoft.com/dotnet/framework/winforms/controls/)<br/><br/>[Диалоговые окна](https://docs.microsoft.com/dotnet/framework/winforms/dialog-boxes-in-windows-forms)<br/><br/>[Ввод данных пользователем](https://docs.microsoft.com/dotnet/framework/winforms/user-input-in-windows-forms)<br/><br/>[Специальные возможности Windows Forms](https://docs.microsoft.com/dotnet/framework/winforms/advanced/windows-forms-accessibility)<br/><br/> |

### <a name="audio-video-and-graphics"></a>Аудио, видео и графика

|  UWP  |  Win32 (Windows API) |  WPF  |  Windows Forms  |
|-------|----------------------|-------|-----------------|
| [Звук, видео и камера](/windows/uwp/audio-video-camera/)<br/><br/>[Воспроизведение мультимедиа](/windows/uwp/audio-video-camera/media-playback/)<br/><br/>[Визуальный уровень](/windows/uwp/composition/visual-layer)<br/><br/>[Платформа XAML](/windows/uwp/xaml-platform/) |  [Звук и видео](/windows/desktop/audio-and-video)<br/><br/>[Графика и игры](/windows/desktop/graphics-and-multimedia)<br/><br/>[DirectX](/windows/desktop/getting-started-with-directx-graphics)<br/><br/>[Direct2D](/windows/desktop/direct2d/direct2d-portal)<br/><br/>[Direct3D](/windows/desktop/direct3d)<br/><br/>[Windows GDI](/windows/desktop/gdi/windows-gdi)<br/><br/>[GDI+](/windows/desktop/gdiplus/-gdiplus-gdi-start)  |  [Графика](https://docs.microsoft.com/dotnet/framework/wpf/graphics-multimedia/graphics)<br/><br/>[мутимедиа](https://docs.microsoft.com/dotnet/framework/wpf/graphics-multimedia/multimedia-overview)  |  [Графика и рисование](https://docs.microsoft.com/dotnet/framework/winforms/advanced/graphics-and-drawing-in-windows-forms)<br/><br/>[SoundPlayer, класс](https://docs.microsoft.com/dotnet/framework/winforms/controls/soundplayer-class-overview)  |

### <a name="data-access-and-app-resources"></a>Доступ к данным и ресурсы приложений

|  UWP  |  Win32 (Windows API) |  WPF  |  Windows Forms  |
|-------|----------------------|-------|-----------------|
| [Доступ к данным](/windows/uwp/data-access/)<br/><br/>[Привязка данных](/windows/uwp/data-binding/)<br/><br/>[Файлы, папки и библиотеки](/windows/uwp/files/)<br/><br/>[Ресурсы приложения](/windows/uwp/app-resources/) |  [Доступ к данным и хранилище](/windows/desktop/data-access-and-storage)<br/><br/>[Локальные файловые системы](/windows/desktop/fileio/file-systems)<br/><br/>[Обзоры ресурсов](/windows/desktop/menurc/resources-overviews)</li>  |  [Данные и моделирование](https://docs.microsoft.com/dotnet/framework/data/)<br/><br/>[Привязка данных](https://docs.microsoft.com/dotnet/framework/wpf/data/data-binding-wpf)<br/><br/>[Ресурсы в приложениях .NET](https://docs.microsoft.com/dotnet/framework/resources/)<br/><br/>[Ресурсы приложения, содержимое и файлы данных](https://docs.microsoft.com/dotnet/framework/wpf/app-development/wpf-application-resource-content-and-data-files)  |  [Данные и моделирование](https://docs.microsoft.com/dotnet/framework/data/)<br/><br/>[Привязка данных](https://docs.microsoft.com/dotnet/framework/winforms/windows-forms-data-binding)<br/><br/>[Ресурсы в приложениях .NET](https://docs.microsoft.com/dotnet/framework/resources/)<br/><br/>[Параметры приложения](https://docs.microsoft.com/dotnet/framework/winforms/advanced/application-settings-for-windows-forms)  |

### <a name="devices-documents-and-printing"></a>Устройства, документы и печать

|  UWP  |  Win32 (Windows API) |  WPF  |  Windows Forms  |
|-------|----------------------|-------|-----------------|
| [Включение возможностей устройств](/windows/uwp/devices-sensors/enable-device-capabilities)<br/><br/>[Перечисление устройств](/windows/uwp/devices-sensors/enumerate-devices)<br/><br/>[Датчики](/windows/uwp/devices-sensors/sensors)<br/><br/>[Bluetooth](/windows/uwp/devices-sensors/bluetooth)<br/><br/>[Печать и сканирование](/windows/uwp/devices-sensors/printing-and-scanning)<br/><br/>[NFC](/windows/uwp/devices-sensors/nfc) | [API датчика](/windows/desktop/sensorsapi/portal)<br/><br/>[Вывод на печать](/desktop/printdocs/printdocs-printing)<br/><br/>[API-интерфейсы UPnP](/desktop/upnp/universal-plug-and-play-start-page) |  [Управление печатью и печатью системы](https://docs.microsoft.com/dotnet/framework/wpf/advanced/printing-and-print-system-management)  |  [Поддержка печати](https://docs.microsoft.com/dotnet/framework/winforms/advanced/windows-forms-print-support)  |

### <a name="system-network-and-power"></a>Система, сеть и электропитание

|  UWP  |  Win32 (Windows API) |  WPF  |  Windows Forms  |
|-------|----------------------|-------|-----------------|
| [Перечисление устройств](/windows/uwp/devices-sensors/enumerate-devices)<br/><br/>[Получение сведений об аккумуляторе](/windows/uwp/devices-sensors/get-battery-info)<br/><br/>[Потоки и асинхронное программирование](/windows/uwp/threading-async/)<br/><br/>[Сетевые и веб-службы](/windows/uwp/networking/) | [Службы системы](/windows/desktop/system-services)<br/><br/>[Управление памятью](/windows/desktop/memory/memory-management)<br/><br/>[Управление питанием](/windows/desktop/power/power-management-portal)<br/><br/>[Процессы и потоки](/windows/desktop/procthread/processes-and-threads)<br/><br/>[Сетевые подключения и Интернет](/windows/desktop/networking)<br/><br/>[Сведения о системе Windows](/windows/desktop/sysinfo/windows-system-information) |  [Потоковая модель](https://docs.microsoft.com/dotnet/framework/wpf/advanced/threading-model)<br/><br/>[Сетевое программирование в .NET Framework](https://docs.microsoft.com/dotnet/framework/network-programming/)  |  [Сведения о системе](https://docs.microsoft.com/dotnet/framework/winforms/advanced/system-information-and-windows-forms)<br/><br/>[Управление питанием](https://docs.microsoft.com/dotnet/framework/winforms/advanced/power-management-in-windows-forms)<br/><br/>[Сетевое программирование в .NET Framework](https://docs.microsoft.com/dotnet/framework/network-programming/)<br/><br/>[Сетевые подключения в Windows Forms](https://docs.microsoft.com/dotnet/framework/winforms/advanced/networking-in-windows-forms-applications)  |

### <a name="packaging-and-deployment"></a>Упаковка и развертывание

|  UWP  |  Win32 (Windows API) |  WPF  |  Windows Forms  |
|-------|----------------------|-------|-----------------|
| [Создание пакетов приложений](/windows/uwp/packaging/)<br/><br/>[MSIX](https://docs.microsoft.com/windows/msix/)<br/><br/>[Схема манифеста пакета приложения](https://docs.microsoft.com/uwp/schemas/appxpackage/uapmanifestschema/schema-root) | [Упаковка классических приложений Windows (MSIX)](/windows/msix/desktop/desktop-to-uwp-root)<br/><br/>[Установка и обслуживание приложения](/windows/desktop/application-installing-and-servicing)<br/><br/>[Установщик Windows](/windows/desktop/msi/windows-installer-portal) |  [Упаковка классических приложений Windows (MSIX)](/windows/msix/desktop/desktop-to-uwp-root)<br/><br/>[Развертывание .NET Framework и приложений](https://docs.microsoft.com/dotnet/framework/deployment/)<br/><br/>[Развертывание приложения WPF](https://docs.microsoft.com/dotnet/framework/wpf/app-development/deploying-a-wpf-application-wpf)  |  [Упаковка классических приложений Windows (MSIX)](/windows/msix/desktop/desktop-to-uwp-root)<br/><br/>[Развертывание .NET Framework и приложений](https://docs.microsoft.com/dotnet/framework/deployment/)<br/><br/>[Развертывание ClickOnce для Windows Forms](https://docs.microsoft.com/dotnet/framework/winforms/clickonce-deployment-for-windows-forms)  |
