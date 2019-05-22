---
Description: Этот раздел поможет вам понять, как некоторые ключевые возможности Windows поддерживаются в платформах для разных приложений и как приступить к работе с помощью функций в коде.
title: Функции и технологии
ms.topic: article
ms.date: 05/08/2019
ms.openlocfilehash: ff91d8c01e6832e645cc857b638851e1833fc3f9
ms.sourcegitcommit: f0f933d5cf0be734373a7b03e338e65000cc3d80
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/21/2019
ms.locfileid: "65984984"
---
# <a name="features-and-technologies-for-windows-apps"></a>Функции и технологии для приложений Windows

Независимо от того, какой тип приложения пользователям, здания или устройства, который вы используете Windows поддерживает множество функций, которые являются ключевые структурные элементы для сценариев важных приложений. Некоторые из этих функций доступны в Windows платформа Универсальной, Win32 (Windows API) и других платформ приложений по-разному. Следующие статьи помогут вам понять, как некоторые функции Windows поддерживаются в платформах для разных приложений и как приступить к работе с помощью функций в коде.

В этой статье предоставляет специализированные список статей, чтобы узнать больше о том, как вы доступа к важных функций Windows и технологии в универсальной платформы Windows, Win32 (Windows API), WPF и платформы приложений Windows Forms. Полные сведения о функции разработки для каждой платформы см. следующие ресурсы:

* [Документация по UWP](/windows/uwp/index)
* [Документация (Windows API) Win32](/windows/desktop/index)
* [Документация по WPF](https://docs.microsoft.com/dotnet/framework/wpf/index)
* [Документация по Windows Forms](https://docs.microsoft.com/dotnet/framework/winforms/index)

## <a name="key-windows-features-and-technologies"></a>Ключ Windows возможности и технологии

В следующих разделах рассматриваются несколько важных функций Windows и технологии, которые позволяют доставлять современных и предоставлять своим клиентам привлечь пользователей.

### <a name="windows-ink"></a>Windows Ink

![Ручка Surface](images/hero-small.png)  

Платформа Windows Ink — наряду с пером — обеспечивает естественный способ создания цифровых рукописных примечаний, рисунков и заметок. Платформа поддерживает получение входных данных дигитайзера в виде рукописных данных, создание рукописных данных, управление этими данными, отображение их в виде росчерков пера на устройстве вывода и преобразование этих росчерков в текст с помощью функции распознавания рукописного ввода.

Дополнительные сведения о различных способах использования рукописного ввода Windows в приложениях Windows, см. в разделе [рукописного ввода Windows](/windows/uwp/design/input/pen-and-stylus-interactions).

### <a name="speech-interactions"></a>Взаимодействие с помощью голосовых функций

![начальный экран распознавания для ограничения на базе грамматического файла SGRS](images/speech-listening-initial.png)

![конечный экран распознавания для ограничения на базе грамматического файла SGRS](images/speech-listening-complete.png)

Windows предоставляет множество способов интеграции распознавания речи и преобразования текста в речь (также известное как TTS или синтеза речи) непосредственно в интерфейс пользователя приложения. Речи может быть надежным и приятной способом пользователям взаимодействовать с приложения, которая дополняет или даже замены клавиатуры, мыши, касания и жесты.

Дополнительные сведения о различных способах использования взаимодействий речи в приложениях Windows, см. в разделе [речи взаимодействия](/windows/uwp/design/input/speech-interactions).

### <a name="windows-ai"></a>ИИ Windows

![ИИ Windows](images/windows-ai.png)

Мы предлагаем несколько различных решений искусственного Интеллекта, которые можно использовать для улучшения приложений Windows. С помощью Windows машинного обучения можно интегрировать обученных моделей машинного обучения в приложения и запустить их локально на устройстве. Навыки концепции Windows позволяет использовать готовые библиотеки для выполнения распространенных задач обработки изображений или создания собственных настраиваемых решениях. DirectML предоставляет низкого уровня, стиле DirectX API-интерфейсы, которые позволяют использовать все преимущества оборудования.

Дополнительные сведения о различных способах интеграция искусственного Интеллекта в приложения Windows, см. в разделе [Windows AI](https://docs.microsoft.com/windows/ai/).

## <a name="features-and-technologies-by-platform"></a>Функции и технологии платформы

Ниже приводятся полезные ссылки на дополнительные сведения о том, как интегрировать с основные функции Windows и технологии наших платформах основного приложения: UWP, Win32 (Windows API), WPF и Windows Forms.

### <a name="user-interface-and-accessibility"></a>Пользовательский интерфейс и специальные возможности

|  UWP  |  Win32 (Windows API) |  WPF  |  Windows Forms  |
|-------|----------------------|-------|-----------------|
| [Оформление](/windows/uwp/design/basics/)<br/><br/>[Макет](/windows/uwp/design/layout/)<br/><br/>[Элементы управления](/windows/uwp/design/controls-and-patterns/)<br/><br/>[Входные данные](/windows/uwp/design/input/)<br/><br/>[Плитки](/windows/uwp/design/shell/tiles-and-notifications/creating-tiles)<br/><br/>[Визуальный уровень](/windows/uwp/composition/visual-layer)<br/><br/>[Платформа XAML](/windows/uwp/xaml-platform/)<br/><br/>[Запуск, возобновление и фоновые задачи](/windows/uwp/launch-resume/)<br/><br/>[Специальные возможности Windows](/windows/uwp/design/accessibility/accessibility)<br/><br/>  |  [Пользовательский интерфейс рабочего стола](/windows/desktop/windows-application-ui-development)<br/><br/>[Среду рабочего стола и оболочка](/windows/desktop/user-interface)<br/><br/>[элементы управления Windows](/windows/desktop/controls/window-controls)<br/><br/>[Элементы управления универсальной платформы Windows в классических приложениях (XAML о-ва)](/windows/apps/desktop/modernize/xaml-islands)<br/><br/>[UWP визуальном уровне в приложениях для настольных систем](/windows/apps/desktop/modernize/visual-layer-in-desktop-apps)<br/><br/>[Windows и сообщения](/windows/desktop/winmsg/windowing)<br/><br/>[Меню и другие ресурсы](/windows/desktop/menurc/resources)<br/><br/>[Высокое разрешение](/windows/desktop/hidpi/high-dpi-desktop-application-development-on-windows)<br/><br/>[Специальные возможности](/windows/desktop/accessibility)<br/><br/>  |  [Windows в WPF](https://docs.microsoft.com/dotnet/framework/wpf/app-development/windows-in-wpf-applications)<br/><br/>[Общие сведения о переходах](https://docs.microsoft.com/dotnet/framework/wpf/app-development/navigation-overview)<br/><br/>[XAML в WPF](https://docs.microsoft.com/dotnet/framework/wpf/advanced/xaml-in-wpf)<br/><br/>[Элементы управления](https://docs.microsoft.com/dotnet/framework/wpf/controls/)<br/><br/>[Программирование визуального слоя](https://docs.microsoft.com/dotnet/framework/wpf/graphics-multimedia/visual-layer-programming)<br/><br/>[Входные данные](https://docs.microsoft.com/dotnet/framework/wpf/advanced/input-wpf)<br/><br/>[Специальные возможности](https://docs.microsoft.com/dotnet/framework/ui-automation/)<br/><br/>  | [Создайте форму Windows](https://docs.microsoft.com/dotnet/framework/winforms/creating-a-new-windows-form)<br/><br/>[Элементы управления](https://docs.microsoft.com/dotnet/framework/winforms/controls/)<br/><br/>[Диалоговые окна](https://docs.microsoft.com/dotnet/framework/winforms/dialog-boxes-in-windows-forms)<br/><br/>[Ввод данных пользователем](https://docs.microsoft.com/dotnet/framework/winforms/user-input-in-windows-forms)<br/><br/>[Поддержка специальных возможностей Windows Forms](https://docs.microsoft.com/dotnet/framework/winforms/advanced/windows-forms-accessibility)<br/><br/> |

### <a name="audio-video-and-graphics"></a>Аудио-, видео и графики

|  UWP  |  Win32 (Windows API) |  WPF  |  Windows Forms  |
|-------|----------------------|-------|-----------------|
| [Звук, видео и камера](/windows/uwp/audio-video-camera/)<br/><br/>[Воспроизведение мультимедиа](/windows/uwp/audio-video-camera/media-playback/)<br/><br/>[Визуальный уровень](/windows/uwp/composition/visual-layer)<br/><br/>[Платформа XAML](/windows/uwp/xaml-platform/) |  [Звук и видео](/windows/desktop/audio-and-video)<br/><br/>[Графика и игры](/windows/desktop/graphics-and-multimedia)<br/><br/>[DirectX](/windows/desktop/getting-started-with-directx-graphics)<br/><br/>[Direct2D](/windows/desktop/direct2d/direct2d-portal)<br/><br/>[Direct3D](/windows/desktop/direct3d)<br/><br/>[Windows GDI](/windows/desktop/gdi/windows-gdi)<br/><br/>[GDI +](/windows/desktop/gdiplus/-gdiplus-gdi-start)  |  [Графика](https://docs.microsoft.com/dotnet/framework/wpf/graphics-multimedia/graphics)<br/><br/>[Mutimedia](https://docs.microsoft.com/dotnet/framework/wpf/graphics-multimedia/multimedia-overview)  |  [Объекты Graphics и drawing](https://docs.microsoft.com/dotnet/framework/winforms/advanced/graphics-and-drawing-in-windows-forms?view=netframework-4.8)<br/><br/>[Класс SoundPlayer](https://docs.microsoft.com/dotnet/framework/winforms/controls/soundplayer-class-overview)  |

### <a name="data-access-and-app-resources"></a>Ресурсы данных access и приложения

|  UWP  |  Win32 (Windows API) |  WPF  |  Windows Forms  |
|-------|----------------------|-------|-----------------|
| [Доступ к данным](/windows/uwp/data-access/)<br/><br/>[Привязка данных](/windows/uwp/data-binding/)<br/><br/>[Файлы, папки и библиотеки](/windows/uwp/files/)<br/><br/>[Ресурсы приложений](/windows/uwp/app-resources/) |  [Доступ и хранение данных](/windows/desktop/data-access-and-storage)<br/><br/>[Локальные файловые системы](/windows/desktop/fileio/file-systems)<br/><br/>[Обзор ресурсов](/windows/desktop/menurc/resources-overviews)</li>  |  [Данные и моделирование](https://docs.microsoft.com/dotnet/framework/data/)<br/><br/>[Привязка данных](https://docs.microsoft.com/dotnet/framework/wpf/data/data-binding-wpf)<br/><br/>[Ресурсы в приложениях .NET](https://docs.microsoft.com/dotnet/framework/resources/?view=netframework-4.8)<br/><br/>[Файлы ресурсов, содержимого и данных приложения](https://docs.microsoft.com/dotnet/framework/wpf/app-development/wpf-application-resource-content-and-data-files)  |  [Данные и моделирование](https://docs.microsoft.com/dotnet/framework/data/)<br/><br/>[Привязка данных](https://docs.microsoft.com/dotnet/framework/winforms/windows-forms-data-binding)<br/><br/>[Ресурсы в приложениях .NET](https://docs.microsoft.com/dotnet/framework/resources/?view=netframework-4.8)<br/><br/>[Параметры приложения](https://docs.microsoft.com/dotnet/framework/winforms/advanced/application-settings-for-windows-forms)  |

### <a name="devices-documents-and-printing"></a>Устройства, документы и печать

|  UWP  |  Win32 (Windows API) |  WPF  |  Windows Forms  |
|-------|----------------------|-------|-----------------|
| [Включение возможностей устройств](/windows/uwp/devices-sensors/enable-device-capabilities)<br/><br/>[Перечисление устройств](/windows/uwp/devices-sensors/enumerate-devices)<br/><br/>[Датчики](/windows/uwp/devices-sensors/sensors)<br/><br/>[Bluetooth](/windows/uwp/devices-sensors/bluetooth)<br/><br/>[Печать и сканирование](/windows/uwp/devices-sensors/printing-and-scanning)<br/><br/>[NFC](/windows/uwp/devices-sensors/nfc) | [Датчик API](/windows/desktop/sensorsapi/portal)<br/><br/>[Вывод на печать](/desktop/printdocs/printdocs-printing)<br/><br/>[API-интерфейсы UPnP](/desktop/upnp/universal-plug-and-play-start-page) |  [Печать и управления печатью](https://docs.microsoft.com/dotnet/framework/wpf/advanced/printing-and-print-system-management)  |  [поддержка печати](https://docs.microsoft.com/dotnet/framework/winforms/advanced/windows-forms-print-support)  |

### <a name="system-network-and-power"></a>Системы, сети и электропитания

|  UWP  |  Win32 (Windows API) |  WPF  |  Windows Forms  |
|-------|----------------------|-------|-----------------|
| [Перечисление устройств](/windows/uwp/devices-sensors/enumerate-devices)<br/><br/>[Получение сведений об аккумуляторе](/windows/uwp/devices-sensors/get-battery-info)<br/><br/>[Потоки и асинхронное программирование](/windows/uwp/threading-async/)<br/><br/>[Сетевые и веб-службы](/windows/uwp/networking/) | [Службы системы](/windows/desktop/system-services)<br/><br/>[Управление памятью](/windows/desktop/memory/memory-management)<br/><br/>[Управление питанием](/windows/desktop/power/power-management-portal)<br/><br/>[Процессы и потоки](/windows/desktop/procthread/processes-and-threads)<br/><br/>[Сеть и Интернет](/windows/desktop/networking)<br/><br/>[Сведения о системе Windows](/windows/desktop/sysinfo/windows-system-information) |  [Потоковая модель](https://docs.microsoft.com/dotnet/framework/wpf/advanced/threading-model)<br/><br/>[Сетевое программирование в .NET Framework](https://docs.microsoft.com/dotnet/framework/network-programming/)  |  [Сведения о системе](https://docs.microsoft.com/dotnet/framework/winforms/advanced/system-information-and-windows-forms)<br/><br/>[Управление питанием](https://docs.microsoft.com/dotnet/framework/winforms/advanced/power-management-in-windows-forms)<br/><br/>[Сетевое программирование в .NET Framework](https://docs.microsoft.com/dotnet/framework/network-programming/)<br/><br/>[Использование сети в Windows Forms](https://docs.microsoft.com/dotnet/framework/winforms/advanced/networking-in-windows-forms-applications)  |

### <a name="packaging-and-deployment"></a>Упаковка и развертывание

|  UWP  |  Win32 (Windows API) |  WPF  |  Windows Forms  |
|-------|----------------------|-------|-----------------|
| [Создание пакетов приложений](/windows/uwp/packaging/)<br/><br/>[MSIX](https://docs.microsoft.com/windows/msix/)<br/><br/>[Схема манифеста пакета приложения](https://docs.microsoft.com/uwp/schemas/appxpackage/uapmanifestschema/schema-root) | [Классические приложения Windows пакета (MSIX)](/windows/msix/desktop/desktop-to-uwp-root)<br/><br/>[Установка приложений и обслуживание](/windows/desktop/application-installing-and-servicing)<br/><br/>[Установщик Windows](/windows/desktop/msi/windows-installer-portal) |  [Классические приложения Windows пакета (MSIX)](/windows/msix/desktop/desktop-to-uwp-root)<br/><br/>[Развертывание .NET Framework и приложений](https://docs.microsoft.com/dotnet/framework/deployment/)<br/><br/>[Развертывание приложений WPF](https://docs.microsoft.com/dotnet/framework/wpf/app-development/deploying-a-wpf-application-wpf)  |  [Классические приложения Windows пакета (MSIX)](/windows/msix/desktop/desktop-to-uwp-root)<br/><br/>[Развертывание .NET Framework и приложений](https://docs.microsoft.com/dotnet/framework/deployment/)<br/><br/>[Развертывание ClickOnce для форм Windows Forms](https://docs.microsoft.com/dotnet/framework/winforms/clickonce-deployment-for-windows-forms)  |
