---
title: Что нового в Windows 10 (сборка 19041)
description: Windows 10 (сборка 18362) и новые средства разработчика предоставляют доступ к инструментам, компонентам и возможностям платформы Windows 10.
keywords: новое, новые возможности, Windows, Windows 10, обновление, обновления, функции, новинки, самое новое, разработчики, 19041, май
ms.date: 05/12/2020
ms.topic: article
ms.localizationpriority: medium
ms.openlocfilehash: 6e5f07e83d7e2e1b96c4bade5a2a6998c11e0559
ms.sourcegitcommit: dbb368861c85c45f34ea0d5b77eb3af2416be1b2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/13/2020
ms.locfileid: "83382806"
---
# <a name="whats-new-for-developers-in-windows-10-build-19041"></a>Новые возможности для разработчиков в Windows 10 (сборка 19041)

Сборка 19041 Windows 10 (также известная как пакет SDK версии 2004) в сочетании с Visual Studio 2019 и связанными с ним инструментами и функциями предоставляет все необходимое для создания уникальных приложений Windows. [Установив средства и пакет SDK](https://developer.microsoft.com/windows/downloads#_blank) в Windows 10, вы можете сразу приступить к [созданию нового проекта универсального приложения для Windows](../get-started/create-uwp-apps.md) или использованию [существующего кода приложения в Windows](../porting/index.md).

В этом разделе приводится подборка новых и улучшенных функций этого выпуска, а также руководств, представляющих интерес для разработчиков. Полный список новых пространств имен, добавленных в пакет SDK для Windows, см. в статье об [изменениях API в Windows 10 (сборка 19041)](windows-10-build-19041-api-diff.md). Дополнительные сведения об основных функциях Windows 10 см. в разделе [Потрясающие возможности Windows 10](https://developer.microsoft.com/windows/windows-10-for-developers).

## <a name="windows-10-apps"></a>Приложения Windows 10

Функция | Описание
:------ | :------
Воспроизведение звука через Bluetooth | В статье [Включение воспроизведения звука с устройств, подключенных удаленно по Bluetooth](../audio-video-camera/enable-remote-audio-playback.md) описано, как использовать [AudioPlaybackConnection](/uwp/api/windows.media.audio.audioplaybackconnection), чтобы удаленные устройства, подключенные по Bluetooth, могли воспроизводить звук на локальном компьютере, позволяя выполнять такие сценарии, как настройка ПК на работу в качестве динамика Bluetooth и предоставление пользователям возможности слушать аудио с телефона.
Перенос приложения C# | Мы описали процесс переноса приложения C# в C++/WinRT. Статья [Перенос примера буфера обмена в C++/WinRT из C# — пример использования](/windows/uwp/cpp-and-winrt-apis/clipboard-to-winrt-from-csharp) является контекстной и основывается на конкретном реальном опыте по переносу. Связанная с этим статья [Переход на C++/WinRT с C#](/windows/uwp/cpp-and-winrt-apis/move-to-winrt-from-csharp) предоставляет более подробный обзор технических деталей и действий, связанных с переносом. 
C++/WinRT | Узнайте об обновлениях C++/WinRT для улучшения производительности во время сборки и выполнения (результат совместной работы с группой разработчиков Visual C++) в разделе [Сводка по последним улучшениям и дополнениям за март 2020 г.](/windows/uwp/cpp-and-winrt-apis/news#rollup-of-recent-improvementsadditions-as-of-march-2020) </br> Для C++/WinRT мы добавили дополнительные сведения в следующие темы: [перенос из C++/CX](/windows/uwp/cpp-and-winrt-apis/move-to-winrt-from-cx#boxing-and-unboxing), [перенос из C#](/windows/uwp/cpp-and-winrt-apis/move-to-winrt-from-csharp#boxing-and-unboxing), [простой пример библиотеки пользовательского интерфейса Windows для C++/WinRT](/windows/uwp/cpp-and-winrt-apis/simple-winui-example), [параллелизм](/windows/uwp/cpp-and-winrt-apis/concurrency), [get_unknown()](/uwp/cpp-ref-for-winrt/get-unknown) и [создание пользовательских (на основе шаблона) элементов управления XAML с помощью C++/WinRT](/windows/uwp/cpp-and-winrt-apis/xaml-cust-ctrl).
DirectX | Мы обновили несколько связанных с DirectX статей о новых возможностях для нескольких прошлых выпусков Windows, начиная с Creators Update до Windows 10 версии 1903. [Новые возможности в DirectWrite](/windows/win32/directwrite/what-s-new-in-directwrite-for-windows-8-consumer-preview), [Усовершенствования DXGI 1.6](/windows/win32/direct3ddxgi/dxgi-1-6-improvements) и [Новые возможности в Direct3D 12](/windows/win32/direct3d12/new-releases).
DirectXMath | Мы опубликовали 21 новую статью о DirectXMath, где описываются две структуры матрицы, а также их функции-члены и свободные функции. [Структура XMFLOAT3X4](/windows/win32/api/directxmath/ns-directxmath-xmfloat3x4) является примером.
Direct3D | В статье об [использовании DirectX с дисплеями высокого динамического диапазона и расширенным цветом](/windows/win32/direct3darticles/high-dynamic-range) предоставляется список рекомендаций для приложений Windows с высоким динамическим диапазоном. </br> Новый интерфейс [ID3D11On12Device2](/windows/win32/api/d3d11on12/nn-d3d11on12-id3d11on12device2) и его методы позволяют получать ресурсы, созданные с помощью API Direct3D 11, и использовать их в Direct3D 12.
Direct3D 12 | Добавлен [уровень компонентов Direct3D 12 Core 1.0](/windows/win32/direct3d12/core-feature-levels), который устройства могут использовать *только для вычислений*. </br> Добавлены новые разделы для [интерфейса ID3D12Debug3](/windows/win32/api/d3d12sdklayers/nn-d3d12sdklayers-id3d12debug3).
Direct ML | В DirectML добавлены 18 операторов и низкоуровневый интерфейс API с аппаратным ускорением, на котором создается WinML. Пример: [структура DML_ACTIVATION_SHRINK_OPERATOR_DESC](/windows/win32/api/directml/ns-directml-dml_activation_shrink_operator_desc).
Отчеты об ошибках | Функция RoFailFastWithErrorContextInternal2 была добавлена в Win32, что вызывает исключение, которое может содержать дополнительный контекст ошибки.
Машинное обучение | Машинное обучение Windows [теперь поддерживает ONNX версии 1.4 и набор операций 9](/windows/ai/windows-ml/release-notes). </br>  API [CloseModelOnSessionCreation](https://docs.microsoft.com/uwp/api/windows.ai.machinelearning.learningmodelsessionoptions.closemodelonsessioncreation?view=winrt-19041) позволяет экономить память, автоматически закрывая модель обучения, когда она больше не нужна.
Wi-Fi | Добавлено несколько новых собственных функций и структур Wi-Fi, например [функция WlanDeviceServiceCommand](/windows/win32/api/wlanapi/nf-wlanapi-wlandeviceservicecommand).
Хот-спот Wi-Fi 2 | В статье [Подготовка профиля Wi-Fi с помощью веб-сайта](/windows/win32/nativewifi/prov-wifi-profile-via-website) описаны новые функции для Хот-спота Wi-Fi 2.
Взаимодействие Windows Holographic | Добавлен заголовок [`windows.graphics.holographic.interop.h`](/windows/win32/api/windows.graphics.holographic.interop) с 17 API-интерфейсами Win32. API-интерфейсы предназначены для взаимодействия между средой выполнения Win32 и Windows. Хотя API-интерфейсы были добавлены в Windows 10 (сборка 18362), заголовок является новым для сборки 19041.
Сокеты Windows | В содержимое SPI Windows Sockets 2 были добавлены несколько улучшений. Примером одного из многих улучшенных и дополненных разделов является раздел [функции обратного вызова LPWSPEVENTSELECT](/windows/win32/api/ws2spi/nc-ws2spi-lpwspeventselect).
Основные сведения о XAML Islands | Разместите элементы управления XAMl UWP в классических приложениях Windows с помощью XAML Islands. Узнайте, как [разместить стандартный элемент управления UWP в приложении WPF](/windows/apps/desktop/modernize/host-standard-control-with-xaml-island), а также [разместить стандартный элемент управления UWP в приложении Win32 на C++](/windows/apps/desktop/modernize/host-standard-control-with-xaml-islands-cpp).
Пользовательские элементы управления XAML Islands | Пакеты NuGet [Microsoft.Toolkit.Win32.UI.XamlApplication](https://www.nuget.org/packages/Microsoft.Toolkit.Win32.UI.XamlApplication) и [Microsoft.Toolkit.Win32.UI.SDK](https://www.nuget.org/packages/Microsoft.Toolkit.Win32.UI.SDK) упрощают размещение пользовательских элементов управления XAML UWP в приложениях .NET и Win32 на C++. </br> Пошаговые руководства см. в статьях [Размещение настраиваемого элемента управления UWP в приложении WPF, использующем XAML Islands](/windows/apps/desktop/modernize/host-custom-control-with-xaml-islands) и [Размещение настраиваемого элемента управления UWP в приложении Win32 на C++](/windows/apps/desktop/modernize/host-custom-control-with-xaml-islands-cpp). </br> Наконец, рекомендации по более сложным сценариям Win32 на C++ см. в статье [Расширенные сценарии для XAML Islands в приложениях Win32 на C++](/windows/apps/desktop/modernize/advanced-scenarios-xaml-islands-cpp).

## <a name="build-with-windows"></a>Создание решений с помощью Windows

Функция | Описание
:------ | :------
Среда разработки Windows | В документации по [среде разработки Windows](/windows/dev-environment/) предоставлены ресурсы по использованию Windows для разработки на различных платформах для достижения любых целей разработки.
Python в Windows | В разделе [Python в Windows](/windows/python/) содержатся сведения для разработчиков, которые имеют небольшой опыт работы с языком Python, а также для разработчиков, которым необходимо оптимизировать разработку на Python с помощью других инструментов, доступных в Windows. Узнайте, как настроить среду Python для [веб-разработки](/windows/python/web-frameworks) и [взаимодействия с базами данных](/windows/python/databases).
NodeJS в Windows | В статье [Настройка среды разработки Node.js в подсистеме Windows для Linux версии 2](/windows/nodejs/setup-on-wsl2) содержатся подробные рекомендации для опытных разработчиков, выполняющих развертывание на серверах Linux. Также доступны инструкции по настройке для [популярных веб-платформ Node.js](/windows/nodejs/web-frameworks), [взаимодействия с базами данных](/windows/nodejs/databases) и [контейнеров Docker](/windows/nodejs/containers).
Использование Mac в Windows | Наше [руководство по изменению среды разработки](/windows/dev-environment/mac-to-windows) предназначено для пользователей, переводящих свою платформу разработки с Mac на Windows, и предоставляет сопоставления для сравнимых ярлыков и служебных программ разработки.
Терминал Windows | [Современное приложение терминала](https://www.microsoft.com/p/windows-terminal/9n0dx20hk701?activetab=pivot:overviewtab) для пользователей средств и оболочек командной строки, таких как Командная строка, PowerShell и Подсистема Windows для Linux (WSL). К его основным функциям относится поддержка нескольких вкладок, панелей, символов Юникода и UTF-8, а также модуль отрисовки текста с ускоренным GPU, возможность создания собственных тем и настройки текста, цвета, фона и сочетаний клавиш.
WSL 2 | [Теперь доступна новая версия подсистемы Windows для Linux (WSL)](/windows/wsl/wsl2-about). В WSL 2 реализована перенастроенная архитектура для запуска фактического ядра Linux в Windows, что повышает производительность файловой системы и добавляет полную совместимость системных вызовов. Эта новая архитектура изменяет способ взаимодействия двоичных файлов Linux с Windows и оборудованием компьютера, но по-прежнему предоставляет то же взаимодействие с пользователем, что и предыдущая версия WSL. Каждое отдельное распределение Linux может работать как дистрибутив WSL1 или WSL2, может быть запущено параллельно и изменено в любое время. </br> Чтобы приступить к работе, [установите WSL 2](https://docs.microsoft.com/windows/wsl/wsl2-install). </br> Дополнительные сведения см. в статье о [сравнении взаимодействия с пользователем в WSL 1 и WSL 2](https://docs.microsoft.com/windows/wsl/wsl2-ux-changes). </br> Просмотрите статью [Вопросы и ответы по WSL](https://docs.microsoft.com/windows/wsl/wsl2-faq).

## <a name="msix-packaging-and-deployment"></a>Упаковка и развертывание с использованием MSIX

Функция | Описание
:------ | :------
MSIX | После последнего выпуска пакета SDK для Windows 10 были внесены значительные изменения в [формат пакета MSIX](/windows/msix/overview). 
Упаковка с помощью служб | Формат MSIX и средство упаковки MSIX [теперь поддерживают пакеты приложений, содержащие службы](/windows/msix/packaging-tool/convert-an-installer-with-services).
Скрипты в пакетах MSIX | Вы можете [использовать платформу поддержки пакетов (PSF) для запуска скриптов в пакете приложения MSIX](/windows/msix/psf/run-scripts-with-package-support-framework), что позволит ИТ-специалистам динамически настраивать приложение в среде пользователя после упаковки с помощью MSIX.
Принудительная проверка целостности пакетов | Теперь можно принудительно применить проверку целостности содержимого пакетов MSIX с помощью [элемента uap10:PackageIntegrity](/uwp/schemas/appxpackage/uapmanifestschema/element-uap10-packageintegrity) в манифесте пакета. Принудительно применить проверку целостности пакетов также можно при создании пакетов MSIX с помощью средства упаковки MSIX.
Разреженные пакеты | Вы можете [предоставлять идентификаторы в пакетах для классических приложений, которые не упакованы в пакет MSIX](/windows/apps/desktop/modernize/grant-identity-to-nonpackaged-apps), создав и зарегистрировав *разреженный пакет* с помощью приложения. Эта возможность позволяет классическим приложениям, для которых еще нельзя внедрить упаковку MSIX для развертывания, использовать возможности расширяемости в Windows 10, для которых требуется идентификатор пакета.
Размещенные приложения | Теперь можно [создать размещенные приложения](/windows/uwp/launch-resume/hosted-apps). Размещенные приложения совместно используют те же исполняемый файл и определение, что и родительское размещенное приложение, но они выглядят и ведут себя как отдельное приложение в системе. Размещенные приложения полезны в сценариях, где требуется, чтобы компонент (например, исполняемый файл или файл сценария) работал как автономное приложение Windows 10, но для работы компонента требуется процесс узла. Размещенное приложение может иметь собственные плитку начального экрана, удостоверение и глубокую интеграцию с такими компонентами Windows 10, как фоновые задачи, уведомления, плитки и получатели данных.

## <a name="windows-ui-library-winui"></a>Библиотека пользовательского интерфейса Windows (WinUI)

Функция | Описание
:------ | :------
WinUI 2.4 | [WinUI 2.4](/uwp/toolkits/winui/release-notes/winui-2.4) — это последний общедоступный выпуск Библиотеки пользовательского интерфейса Windows. Все версии WinUI предоставляют широкий набор официальных элементов управления пользовательского интерфейса для приложений Windows и предоставляются как пакет NuGet, независимый от Windows SDK. Поэтому они работают в более ранних версиях Windows 10. Чтобы установить WinUI, [выполните эти инструкции](/uwp/toolkits/winui).
RadialGradientBrush | Новый объект [RadialGradientBrush](../design/style/brushes.md#radial-gradient-brushes) в WinUI 2.4 заполняет область внутри эллипса, который определяется свойствами Center, RadiusX и RadiusY. Начальный цвет градиента определяется в центре эллипса, а конечный — в конце радиуса.
ProgressRing | Новый [элемент управления ProgressRing](../design/controls-and-patterns/progress-controls.md) в WinUI 2.4 используется для модальных взаимодействий, при которых действия пользователя блокируются до исчезновения ProgressRing. Используйте этот элемент управления, если для операции требуется приостановить значительную часть взаимодействий с приложением до ее завершения.
TabView | Обновления [элемента управления TabView](../design/controls-and-patterns/tab-view.md) обеспечивают дополнительный контроль над отображением вкладок. Вы можете задать ширину невыбранных вкладок и показать только значок для сохранения экранного пространства. Кроме того, можно скрыть кнопку закрытия на невыбранных вкладках, пока пользователь не наведет указатель мыши на вкладку.
Элементы управления TextBox | Теперь, когда включена темная тема, при вставке текста цвет фона элементов управления семейства TextBox по умолчанию остается темным. Затронутые элементы управления: [TextBox](/uwp/api/windows.ui.xaml.controls.textbox), [RichEditBox](/uwp/api/windows.ui.xaml.controls.richtextblock), [PasswordBox](/uwp/api/windows.ui.xaml.controls.passwordbox), [Редактируемый ComboBox](/uwp/api/windows.ui.xaml.controls.combobox) и [AutoSuggestBox](/uwp/api/windows.ui.xaml.controls.autosuggestbox).
NavigationView | [Элемент управления NavigationView](/uwp/api/microsoft.ui.xaml.controls.navigationview) теперь поддерживает иерархическую навигацию и режимы отображения Left, Top и LeftCompact. Иерархический элемент управления NavigationView полезен для отображения категорий страниц и определения страниц со связанными дочерними страницами, а также для использования приложений со звездообразной топологией страниц, когда одна страница может быть связана со многими другими страницами.
Коллекция пользовательского интерфейса Windows | Примеры каждой функции WinUI доступны в коллекции элементов управления XAML. Скачайте ее с [Microsoft Store](https://www.microsoft.com/p/xaml-controls-gallery/9msvh128x2zt) или [просмотрите исходный код на сайте GitHub](https://github.com/Microsoft/Xaml-Controls-Gallery).
Предыдущие версии | После предыдущего основного выпуска пакета SDK для Windows 10 были также выпущены [WinUI 2.3](/uwp/toolkits/winui/release-notes/winui-2.3) и [WinUI 2.2](/uwp/toolkits/winui/release-notes/winui-2.2), которые предоставляют новые функции пользовательского интерфейса для разработчиков Windows.

## <a name="samples"></a>примеры

Следующие примеры приложений были обновлены для Windows 10 (сборка 19041).

* [Удаленные сеансы (игра-викторина)](https://github.com/microsoft/Windows-appsample-remote-system-sessions)
* [База данных заказов клиентов](https://github.com/Microsoft/Windows-appsample-customers-orders-database)
* [Средство чтения RSS](https://github.com/Microsoft/Windows-appsample-rssreader)
* [Игра Marble Maze](https://github.com/Microsoft/Windows-appsample-marble-maze)
* [Редактор фотографий](https://github.com/Microsoft/Windows-appsample-photo-editor)
* [Планировщик обедов](https://github.com/Microsoft/Windows-appsample-lunch-scheduler)
* [Раскраска](https://github.com/Microsoft/Windows-appsample-coloringbook)
* [Средство управления оттенком света](https://github.com/Microsoft/Windows-appsample-huelightcontroller)
* [Фотолаборатория](https://github.com/Microsoft/Windows-appsample-photo-lab)
* [Семейные заметки](https://github.com/Microsoft/Windows-appsample-familynotes)

## <a name="videos"></a>Видео

### <a name="windows-terminal-the-secret-to-command-line-happiness"></a>Терминал Windows: секрет счастливой работы с командной строкой!

Узнайте, как настроить Терминал Windows для рабочего процесса, и просмотрите демоверсии его функций в действии. [Посмотрите это видео](https://www.youtube.com/watch?v=2dsnwlnNBzs), а затем [изучите документацию](https://github.com/microsoft/terminal#terminal--console-overview) для получения дополнительной информации.

### <a name="wsl2-code-faster-on-the-windows-subsystem-for-linux"></a>WSL2: быстрая работа с кодом в подсистеме Windows для Linux

Узнайте все о WSL2, новой версии подсистемы Windows для Linux, и изменениях для повышения производительности. [Посмотрите это видео](https://www.youtube.com/watch?v=MrZolfGm8Zk), а затем [изучите документацию](/windows/wsl/wsl2-about) для получения дополнительной информации.

### <a name="msix-package-desktop-apps-for-windows-10-replace-outdated-installers"></a>MSIX. Упаковка классических приложений для Windows 10. Замена устаревших установщиков.

Узнайте о MSIX, формате пакета для установки приложений Windows, в том числе об упаковке существующего кода в Visual Studio, а также о развертывании и распространении приложения. [Посмотрите это видео](https://www.youtube.com/watch?v=yhOnClQrvBk), а затем [изучите документацию](/windows/msix/) для получения дополнительной информации.
