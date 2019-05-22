---
Description: Улучшить своего настольного приложения для пользователей Windows 10 с помощью универсальной Windows API платформы (UWP).
title: Использовать API универсальной платформы Windows в приложениях для настольных систем
ms.date: 04/19/2019
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.custom: 19H1
ms.openlocfilehash: eb0cee26ee65c029af3f0385f44630afa057b29f
ms.sourcegitcommit: f0f933d5cf0be734373a7b03e338e65000cc3d80
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/21/2019
ms.locfileid: "65984664"
---
# <a name="call-uwp-apis-in-desktop-apps"></a>Вызов API универсальной платформы Windows в приложениях для настольных систем

Добавлять современные возможности в классических приложениях, которые немного лучше для пользователей Windows 10 можно использовать универсальный Windows API платформы (UWP).

Во-первых Настройте проект с необходимые ссылки. Затем вызовите API-интерфейсов универсальной платформы Windows из кода для добавления работы в Windows 10 для настольных приложений. Можно создать отдельно для пользователей Windows 10 или распространять те же двоичные файлы для всех пользователей, независимо от того, какая версия Windows они выполняются.

Некоторые интерфейсы API универсальной платформы Windows поддерживаются только в классических приложениях, которые содержатся в [MSIX пакета](https://docs.microsoft.com/windows/msix/desktop/desktop-to-uwp-root). Дополнительные сведения см. в разделе [доступные интерфейсы API UWP](desktop-to-uwp-supported-api.md).

## <a name="set-up-your-project"></a>Настройка проекта

Необходимо внести некоторые изменения в проект, чтобы использовать API-интерфейсы UWP.

### <a name="modify-a-net-project-to-use-windows-runtime-apis"></a>Измените проект .NET, чтобы использовать API среды выполнения Windows

1. Откройте диалоговое окно **Диспетчера ссылок**, нажмите кнопку **Обзор**, а затем выберите **Все файлы**.

    ![диалоговое окно "добавление ссылки"](images/desktop-to-uwp/browse-references.png)

2. Добавьте ссылку на эти файлы.

  |Файл|Location|
  |--|--|
  |System.Runtime.WindowsRuntime|C:\Windows\Microsoft.NET\Framework\v4.0.30319|
  |System.Runtime.WindowsRuntime.UI.Xaml|C:\Windows\Microsoft.NET\Framework\v4.0.30319|
  |System.Runtime.InteropServices.WindowsRuntime|C:\Windows\Microsoft.NET\Framework\v4.0.30319|
  |Windows.winmd|C:\Program файлы (x86) \Windows Kits\10\UnionMetadata\\<*версия пакета sdk*> \Facade|
  |Windows.Foundation.UniversalApiContract.winmd|C:\Program Files (x86)\Windows Kits\10\References\\<*sdk version*>\Windows.Foundation.UniversalApiContract\<*version*>|
  |Windows.Foundation.FoundationContract.winmd|C:\Program Files (x86)\Windows Kits\10\References\\<*sdk version*>\Windows.Foundation.FoundationContract\<*version*>|

3. В окне **Свойства** установите в поле **Копировать локально** каждого файла *.winmd* значение **False**.

    ![поле копирования локальных файлов](images/desktop-to-uwp/copy-local-field.png)

### <a name="modify-a-c-project-to-use-windows-runtime-apis"></a>Измените проект C++, чтобы использовать API среды выполнения Windows

Используйте [ C++/WinRT](https://docs.microsoft.com/windows/uwp/cpp-and-winrt-apis/) для работы с API среды выполнения Windows. C++/WinRT — это полностью стандартная проекция языка C++17 для API среды выполнения Windows (WinRT), реализованная как библиотека на основе файлов заголовков и предназначенная для предоставления вам первоклассного доступа к современным API-интерфейсам Windows.

Чтобы настроить проект для C++/WinRT, см. в разделе [изменить проект приложения Windows Desktop, чтобы добавить C++поддержки /WinRT](https://docs.microsoft.com/windows/uwp/cpp-and-winrt-apis/get-started#modify-a-windows-desktop-application-project-to-add-cwinrt-support).

## <a name="add-windows-10-experiences"></a>Добавление возможностей Windows 10

Теперь все готово для добавления современных видов взаимодействия, которые активируются, когда пользователь запускает ваше приложение в Windows 10. Используйте этот процесс разработки.

:white_check_mark: **Во-первых решите, какие возможности вы хотите добавить**

Выбор огромен. Например, можно упростить порядок последовательности покупки с помощью [монетизацию API-интерфейсы](/windows/uwp/monetize), или [внимание приложению](/windows/uwp/design/shell/tiles-and-notifications/adaptive-interactive-toasts) при наличии нечто интересное для совместного использования, такие как новое изображение другой пользователь опубликовал.

![Всплывающее](images/desktop-to-uwp/toast.png)

Даже если пользователи не обращают внимания на ваше сообщение или закрывают его, они могут снова увидеть его в центре уведомлений, а затем щелкнуть по сообщению, чтобы открыть приложение. Это увеличивает взаимодействия с вашим приложением и полевых условиях настройки приложения отображаются интегрирована в операционную систему. Мы покажем код этом немного позже в этой статье.

Посетите [документация по UWP](/windows/uwp/get-started/) Дополнительные соображения.

:white_check_mark: **Решить, следует ли для улучшения или расширения**

Вы услышите часто нам использовать термины *улучшения* и *расширить*, поэтому мы рассмотрим немного объяснить точно содержимого каждого из этих условий среднее значение.

Мы используем термин *улучшения* для описания API среды выполнения Windows, которые можно вызывать непосредственно из вашего приложения (ли вы выбрали для упаковки приложения в пакете MSIX). Если вы выбрали в интерфейсе Windows 10, идентифицировать API, которые необходимо создать его и затем отображаются ли этот API [этот список](desktop-to-uwp-supported-api.md). Это список интерфейсов API, которые можно вызывать непосредственно из вашего приложения. Если выбранный вами API не входит в этот список, это обосновано тем, что функции, связанные с этим API, могут работать только в рамках процесса UWP. Зачастую, к ним относятся интерфейсы API, которые отрисовки XAML UWP, например элемент управления map универсальной платформы Windows или запрос безопасности Windows Hello.

> [!NOTE]
> Несмотря на то, что интерфейсы API, которые обычно визуализации XAML UWP не может вызываться непосредственно с рабочего стола, можно использовать альтернативные подходы. Если вы хотите разместить элементы управления XAML UWP или других пользовательских визуальных возможностей, можно использовать [XAML о-ва](xaml-islands.md) (начиная с Windows 10, версия 1903) и [визуальный слой](visual-layer-in-desktop-apps.md) (начиная с Windows 10 версии 1803). Эти функции можно использовать в пакете или распакован классических приложениях.

Если вы выбрали для упаковки приложения рабочего стола в пакете MSIX, другой вариант — *расширить* приложения путем добавления в решение проекта универсальной платформы Windows. Проект для настольной системы по-прежнему является точкой входа приложения, но проект универсальной платформы Windows предоставляет доступ ко всем API, которые не отображаются в [этот список](desktop-to-uwp-supported-api.md). Классическое приложение может взаимодействовать с процессом универсальной платформы Windows с помощью службы приложений и мы большое число рекомендации о том, как настроить эту функцию. Если вы хотите добавить интерфейс, требуется проект универсальной платформы Windows, см. в разделе [расширить с помощью универсальной платформы Windows компоненты](desktop-to-uwp-extend.md).

:white_check_mark: **Справочник по API контрактов**

Если API можно вызвать непосредственно из вашего приложения, откройте браузер и найдите в разделе справки для этого API.
В сводных сведениях по этому API-интерфейсу вы найдете таблицу, в которой приводится описание контракта API для этого API. Пример такой таблицы приведен ниже.

![Таблица контракта API](images/desktop-to-uwp/contract-table.png)

Если у вас есть классическое приложение на основе .NET, добавьте ссылку на этот контракт API и присвойте свойству **Копировать локально** этого файла значение **False**. Если у вас есть проект на основе C++, добавьте в свои **Дополнительные каталоги включаемых файлов** путь к папке, содержащей этот контракт.

:white_check_mark: **Вызов API-интерфейсы для добавления работы**

Ниже приведен код, используемый для отображения окна уведомлений, которое мы рассмотрели ранее. Эти API-интерфейсы появляться в этом [списка](desktop-to-uwp-supported-api.md) , можно добавить этот код вашего приложения и запустить его прямо сейчас.

```csharp
using Windows.Foundation;
using Windows.System;
using Windows.UI.Notifications;
using Windows.Data.Xml.Dom;
...

private void ShowToast()
{
    string title = "featured picture of the day";
    string content = "beautiful scenery";
    string image = "https://picsum.photos/360/180?image=104";
    string logo = "https://picsum.photos/64?image=883";

    string xmlString =
    $@"<toast><visual>
       <binding template='ToastGeneric'>
       <text>{title}</text>
       <text>{content}</text>
       <image src='{image}'/>
       <image src='{logo}' placement='appLogoOverride' hint-crop='circle'/>
       </binding>
      </visual></toast>";

    XmlDocument toastXml = new XmlDocument();
    toastXml.LoadXml(xmlString);

    ToastNotification toast = new ToastNotification(toastXml);

    ToastNotificationManager.CreateToastNotifier().Show(toast);
}
```

```C++
using namespace Windows::Foundation;
using namespace Windows::System;
using namespace Windows::UI::Notifications;
using namespace Windows::Data::Xml::Dom;

void UWP::ShowToast()
{
    Platform::String ^title = "featured picture of the day";
    Platform::String ^content = "beautiful scenery";
    Platform::String ^image = "https://picsum.photos/360/180?image=104";
    Platform::String ^logo = "https://picsum.photos/64?image=883";

    Platform::String ^xmlString =
        L"<toast><visual><binding template='ToastGeneric'>" +
        L"<text>" + title + "</text>" +
        L"<text>"+ content + "</text>" +
        L"<image src='" + image + "'/>" +
        L"<image src='" + logo + "'" +
        L" placement='appLogoOverride' hint-crop='circle'/>" +
        L"</binding></visual></toast>";

    XmlDocument ^toastXml = ref new XmlDocument();

    toastXml->LoadXml(xmlString);

    ToastNotificationManager::CreateToastNotifier()->Show(ref new ToastNotification(toastXml));
}
```

Дополнительные сведения об уведомлениях см. в разделе [Адаптивные и интерактивные всплывающие уведомления](https://docs.microsoft.com/windows/uwp/design/shell/tiles-and-notifications/adaptive-interactive-toasts).

## <a name="support-windows-xp-windows-vista-and-windows-78-install-bases"></a>Поддержка установочных баз Windows XP, Windows Vista и Windows 7 и Windows 8

Вы можете модернизировать приложения для Windows 10 без необходимости создавать новую ветвь и поддерживать отдельные базы кода.

Если вы хотите создать отдельные двоичные файлы для пользователей Windows 10, используйте условную компиляцию. Если вы предпочитаете создать один набор двоичных файлов, которые развертываются для всех пользователей Windows, используйте проверки во время выполнения.

Вкратце рассмотрим все варианты.

### <a name="conditional-compilation"></a>Условная компиляция

Можно вести одну базу кода и компилировать набор двоичных файлов только для пользователей Windows 10.

Сначала добавьте конфигурацию новой сборки в свой проект.

![Конфигурация сборки](images/desktop-to-uwp/build-config.png)

Для этой конфигурации создайте константу, чтобы определить код, который вызывает API среды выполнения Windows.  

Для проектов на основе .NET эта константа называется **Константой условной компиляции**.

![препроцессор](images/desktop-to-uwp/compilation-constants.png)

Для проектов на основе C++ эта константа называется **Описанием препроцессора**.

![препроцессор](images/desktop-to-uwp/pre-processor.png)

Добавьте эту константу перед любым блоком кода UWP.

```csharp

[System.Diagnostics.Conditional("_UWP")]
private void ShowToast()
{
 ...
}

```

```C++

#if _UWP
void UWP::ShowToast()
{
 ...
}
#endif

```

Компилятор собирает этот код только в том случае, если эта константа определена в активной конфигурации сборки.

### <a name="runtime-checks"></a>Проверки во время выполнения

Можно скомпилировать один набор двоичных файлов для всех пользователей Windows независимо от того, какой версией Windows они пользуются. Приложение вызывает API среды выполнения Windows только в том случае, если пользователь работает ваше приложение как упакованное приложение в Windows 10.

Для добавления проверки времени выполнения в код проще установить этот пакет Nuget: [Вспомогательные функции моста для классических](https://www.nuget.org/packages/DesktopBridge.Helpers/) , а затем использовать ``IsRunningAsUWP()`` метод к шлюзу off весь код, который вызывает API среды выполнения Windows. см. в записи блога для получения дополнительных сведений: [Мост для классических приложений — определить контекст приложения](https://blogs.msdn.microsoft.com/appconsult/2016/11/03/desktop-bridge-identify-the-applications-context/).

## <a name="related-video"></a>Видео по теме

<iframe src="https://mva.microsoft.com/en-US/training-courses-embed/developers-guide-to-the-desktop-bridge-17373/Demo-Use-UWP-APIs-in-Your-Code-3d78c6WhD_9506218965" width="636" height="480" allowFullScreen frameBorder="0"></iframe>

## <a name="related-samples"></a>Примеры по теме

* [Пример Hello World](https://github.com/Microsoft/DesktopBridgeToUWP-Samples/tree/master/Samples/HelloWorldSample)
* [Вторичная плитка](https://github.com/Microsoft/DesktopBridgeToUWP-Samples/tree/master/Samples/SecondaryTileSample)
* [Пример API Store](https://github.com/Microsoft/DesktopBridgeToUWP-Samples/tree/master/Samples/StoreSample)
* [Приложение WinForms, реализующее UpdateTask универсальной платформы Windows](https://github.com/Microsoft/DesktopBridgeToUWP-Samples/tree/master/Samples/WinFormsUpdateTaskSample)
* [Мост классическое приложение для примеров на UWP](https://github.com/Microsoft/DesktopBridgeToUWP-Samples)

## <a name="support-and-feedback"></a>Поддержка и отзывы

**Найдите ответы на ваши вопросы**

Есть вопросы? Задайте их на Stack Overflow. Наша команда следит за этими [тегами](https://stackoverflow.com/questions/tagged/project-centennial+or+desktop-bridge). Вы также можете задать нам вопросы [здесь](https://social.msdn.microsoft.com/Forums/en-US/home?filter=alltypes&sort=relevancedesc&searchTerm=%5BDesktop%20Converter%5D).

**Отправить отзыв или предложения по функциям**

См. раздел [UserVoice](https://wpdev.uservoice.com/forums/110705-universal-windows-platform/category/161895-desktop-bridge-centennial)
