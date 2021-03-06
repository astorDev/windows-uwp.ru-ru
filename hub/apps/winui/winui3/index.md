---
title: WinUI 3.0, предварительная версия 1 (май 2020 г.)
description: Обзор платформы WinUI 3.0, предварительная версия
ms.date: 05/14/2020
ms.topic: article
ms.openlocfilehash: 3aac14807f8455eb9a9294c40ddc76ddfa224659
ms.sourcegitcommit: 7e8c7f89212c88dcc0274c69d2c3365194c0954a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2020
ms.locfileid: "83688486"
---
# <a name="windows-ui-library-30-preview-1-may-2020"></a>Библиотека пользовательского интерфейса Windows 3.0, предварительная версия 1 (май 2020 г.)

Библиотека пользовательского интерфейса Windows (WinUI) 3.0 — это основное обновление, которое преобразует WinUI в полнофункциональную платформу пользовательского интерфейса, поддерживаемую всеми типами приложений для Windows — от Win32 до UWP.

> [!Important]
> Предварительная версия выпуска WinUI 3.0 предназначена для раннего ознакомления и сбора отзывов от сообщества разработчиков. Ее **НЕ СЛЕДУЕТ** использовать для приложений в рабочей среде.
>
> **См. сведения в разделе [Ограничения и известные проблемы в предварительной версии 1](#preview-1-limitations-and-known-issues)** .
## <a name="new-features-in-winui-30-preview-1"></a>Новые возможности в WinUI 3.0, предварительная версия 1

- Возможность создания классических приложений с помощью WinUI, включая [.NET 5](https://github.com/dotnet/core/tree/master/release-notes/5.0) для приложений Win32.
- [RadialGradientBrush.](/windows/uwp/design/style/brushes#radial-gradient-brushes)
- [Обновления TabView.](/windows/uwp/design/controls-and-patterns/tab-view)
- Обновления темной темы.
- Улучшения и обновления [WebView2](https://docs.microsoft.com/microsoft-edge/hosting/webview2):
  - поддержка высокого DPI;
  - поддержка перемещения окна и изменения его размера;
  - обновление для включения поддержки последней версии Edge;
  - отсутствие необходимости ссылаться на пакет NuGet, зависящий от WebView2.
- SwapChainPanel
- Улучшения, требуемые для миграции с открытым кодом.

Сведения о стратегии развития и преимуществах WinUI 3.0 см. в описании [библиотеки пользовательского интерфейса Windows](https://github.com/microsoft/microsoft-ui-xaml/blob/master/docs/roadmap.md) на сайте GitHub.

### <a name="provide-feedback-and-suggestions"></a>Отправка отзывов и предложений

Мы будем рады, если вы оставите свой отзыв в [репозитории GitHub WinUI](https://github.com/microsoft/microsoft-ui-xaml/issues/new/choose).

## <a name="try-winui-30-preview-1"></a>Ознакомление с WinUI 3.0, предварительная версия 1

Настройте среду разработки (подробные инструкции см. в разделе [Настройка среды разработки](#configure-your-dev-environment)), установите VSIX WinUI 3.0, предварительная версия 1 по следующей ссылке и ознакомьтесь с шаблонами проектов WinUI 3.0.

<table>
<tr>
<td align="center">
<a href="https://aka.ms/winui3/previewdownload"><img src="images/downloadbuttontx.png" alt="Download the WinUI 3.0 Preview 1 VSIX"/></a>
<!--
<br/>
<a href="https://aka.ms/winui3/previewdownload">Download the WinUI 3.0 Preview 1 VSIX</a>
-->
</td>
</tr>
</table>

Вы также можете клонировать и выполнить сборку WinUI 3.0, предварительная версия 1 из [коллекции элементов управления XAML](#xaml-controls-gallery-winui-30-preview-1-branch).

### <a name="configure-your-dev-environment"></a>Настройка среды разработки

Убедитесь, что на компьютере разработчика установлено обновление Windows 10 за апрель 2018 г. (версия 1803, сборка 17134) или более новое.

Установите Visual Studio 2019, версия 16.7, предварительная версия 1 (скачать можно на странице [Visual Studio Preview](https://visualstudio.microsoft.com/vs/preview)).

При установке Visual Studio Preview необходимо включить следующие рабочие нагрузки:

- Разработка классических приложений .NET.
- "Разработка приложений для универсальной платформы Windows".

Для сборки приложений на C++ необходимо также включить следующие рабочие нагрузки:

- "Разработка классических приложений на C++";
- дополнительный компонент *Средства универсальной платформы Windows на C++ (версия 142)* для рабочей нагрузки универсальной платформы Windows.

### <a name="visual-studio-project-templates"></a>Шаблоны проектов Visual Studio

Чтобы получить доступ к WinUI 3.0, предварительная версия 1 и шаблонам проектов, перейдите по адресу **https://aka.ms/winui3/previewdownload** .

Скачайте расширение Visual Studio (`.vsix`), чтобы добавить шаблоны проектов WinUI и пакет NuGget в Visual Studio 2019.

Инструкции по добавлению `.vsix` в Visual Studio см. в статье [Поиск и использование расширений Visual Studio](https://docs.microsoft.com/visualstudio/ide/finding-and-using-visual-studio-extensions?view=vs-2019#install-without-using-the-manage-extensions-dialog-box).

После установки расширения `.vsix` вы сможете создать проект WinUI 3.0, выполнив поиск по слову "WinUI" и выбрав один из доступных шаблонов для C# или C++.

![Шаблоны Visual Studio для WinUI 3.0](images/WinUI3Templates.png)<br/>
*Примеры шаблонов WinUI 3.0 для Visual Studio*

### <a name="visual-studio-project-configuration"></a>Конфигурация проекта Visual Studio

При создании проекта с помощью одного из шаблонов WinUI 3.0, предварительная версия 1 укажите Windows 10, версия 1903 (сборка 18362) в качестве **целевой версии** и Windows 10, версия 1803 (сборка 17134) в качестве **минимальной версии**.

Чтобы изменить эти значения после создания проекта, щелкните правой кнопкой мыши проект в **Обозревателе решений** и выберите **Свойства**.

### <a name="creating-a-desktop-win32-app-with-winui-30-preview-1"></a>Создание классического приложения Win32 с помощью WinUI 3.0, предварительная версия 1

Сведения см. в статье [Начало работы с WinUI 3.0 для классических приложений](get-started-winui3-for-desktop.md).

За исключением ограничений и известных проблем, которые описаны ниже, сборка приложения на основе WinUI 3.0, предварительная версия 1 очень похожа на сборку приложения UWP на основе XAML и WinUI 2.x. Следовательно, вы можете использовать большую часть руководств и документации по приложениям UWP и интерфейсам API `Windows.UI`.

## <a name="preview-1-limitations-and-known-issues"></a>Ограничения и известные проблемы в предварительной версии 1

Этот выпуск представляет собой предварительную версию 1. Кроме того, относительно новыми являются сценарии с использованием классических приложений Win32. Учитывайте возможные ошибки, ограничения и проблемы.

Ниже перечислены некоторые известные проблемы с выпуском WinUI 3.0, предварительная версия 1. Если возникнет ошибка, которая не указана ниже, сообщите нам об этом. Для этого добавьте комментарии в ветвь о существующей проблеме или создайте новый вопрос в [репозитории WinUI на сайте GitHub](https://github.com/microsoft/microsoft-ui-xaml/issues/new/choose).

### <a name="platform-and-os-support"></a>Поддержка платформ и операционных систем

Выпуск WinUI 3.0, предварительная версия 1 совместим с компьютерами, на которых установлено обновление Windows 10 за апрель 2018 г. (версия 1803, сборка 17134) и более поздних версий.

### <a name="developer-tools"></a>Средства разработчика

- Классические приложения поддерживают .NET 5 и C# 8 и должны упаковываться.
- Приложения UWP поддерживают .NET Native и C# 7.3.
- Технология IntelliSense не поддерживается в полной мере.
- Отсутствует визуальный конструктор.
- Отсутствует горячая перезагрузка.
- Отсутствует динамическое визуальное дерево.
- Разработка с помощью VS Code еще не поддерживается.
- Новые приложения C++/CX не поддерживаются, но существующие приложения будут работать (перейдите на использование C++/WinRT как можно скорее).
- Содержимое WinUI 3.0 может находиться в одном окне для каждого процесса или в одном представлении ApplicationView для каждого приложения.
- Развертывание неупакованных классических приложений не поддерживается.
- Отсутствует поддержка ARM64.

### <a name="missing-platform-features"></a>Отсутствуют функции платформы.

- Отсутствует поддержка Xbox.
- Отсутствует поддержка HoloLens.
- Всплывающие окна не поддерживаются.
- Рукописный ввод не поддерживается.
- Акриловая кисть для фона.
- MediaElement и MediaPlayerElement.
- RenderTargetBitmap
- MapControl
- Иерархическая навигация с помощью представления NavigationView.
- SwapChainPanel не поддерживает прозрачность.
- При глобальной подсветке используется поведение отката и сплошная кисть.
- В этом выпуске отсутствует поддержка XAML Islands.
- Сторонние библиотеки экосистемы поддерживаются не полностью.
- IME не поддерживаются.
- Методы в пространстве имен Windows.UI.Text не вызываются.

### <a name="known-issues"></a>Известные проблемы

- В классических приложениях C#:
   - Для слабых ссылок на объекты Windows (включая объекты XAML) нужно использовать `WinRT.WeakReference<T>`, а не `System.WeakReference<T>`.
   - В структурах [Point](https://docs.microsoft.com/uwp/api/Windows.Foundation.Point), [Rect](https://docs.microsoft.com/uwp/api/Windows.Foundation.Rect) и [Size](https://docs.microsoft.com/uwp/api/Windows.Foundation.Size) чаще используются элементы типа Float, чем элементы типа Double.


## <a name="xaml-controls-gallery-winui-30-preview-1-branch"></a>Коллекция элементов управления XAML (ветвь для WinUI 3.0, предварительная версия 1)

См. пример приложения, которое использует все элементы управления и функции WinUI 3.0, предварительная версия 1, в [ветви для WinUI 3.0, предварительная версия 1 коллекции элементов управления XAML](https://github.com/microsoft/Xaml-Controls-Gallery/tree/winui3preview).

![Приложение для WinUI 3.0, предварительная версия 1 из коллекции элементов управления XAML](images/WinUI3XamlControlsGallery.png)<br/>
*Пример приложения для WinUI 3.0, предварительная версия 1 из коллекции элементов управления XAML*

Чтобы скачать этот пример, клонируйте ветвь **winui3preview** с помощью следующей команды:

> `git clone --single-branch --branch winui3preview https://github.com/microsoft/Xaml-Controls-Gallery.git`

После клонирования обязательно переключитесь на ветвь **winui3preview** в локальной среде Git:

> `git checkout winui3preview`
