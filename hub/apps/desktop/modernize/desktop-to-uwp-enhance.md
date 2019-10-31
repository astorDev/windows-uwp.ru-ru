---
Description: Улучшите классическое приложение для пользователей Windows 10 с помощью интерфейсов API универсальная платформа Windows (UWP).
title: Использование интерфейсов API UWP в классических приложениях
ms.date: 08/20/2019
ms.topic: article
keywords: windows 10, uwp
ms.author: mcleans
author: mcleanbyron
ms.localizationpriority: medium
ms.custom: 19H1
ms.openlocfilehash: bcdeafc3f30f5b385c6feeddee78cf31635177a0
ms.sourcegitcommit: d7eccdb27c22bccac65bd014e62b6572a6b44602
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2019
ms.locfileid: "73142537"
---
# <a name="call-uwp-apis-in-desktop-apps"></a>Вызов интерфейсов API UWP в классических приложениях

Вы можете использовать интерфейсы API универсальная платформа Windows (UWP) для добавления современных возможностей в классические приложения, предназначенные для пользователей Windows 10.

Сначала настройте проект с помощью необходимых ссылок. Затем вызывайте интерфейсы API UWP из кода, чтобы добавить в классическое приложение возможности Windows 10. Можно создать отдельно для пользователей Windows 10 или распространить одни и те же двоичные файлы всем пользователям независимо от того, какая версия Windows запущена.

Некоторые API-интерфейсы UWP поддерживаются только в классических приложениях с [удостоверением пакета](modernize-packaged-apps.md). Дополнительные сведения см. в разделе [доступные интерфейсы API UWP](desktop-to-uwp-supported-api.md).

## <a name="set-up-your-project"></a>Настройка проекта

Необходимо внести некоторые изменения в проект, чтобы использовать API-интерфейсы UWP.

### <a name="modify-a-net-project-to-use-windows-runtime-apis"></a>Изменение проекта .NET для использования среда выполнения Windows API

Существуют два варианта проектов .NET.

* Если приложение предназначено для Windows 10 версии 1803 или более поздней, можно установить пакет NuGet, который предоставляет все необходимые ссылки.
* Также можно добавить ссылки вручную.

#### <a name="to-use-the-nuget-option"></a>Использование параметра NuGet

1. Убедитесь, что [ссылки на пакет](https://docs.microsoft.com/nuget/consume-packages/package-references-in-project-files) включены:

    1. В Visual Studio щелкните **Сервис-> диспетчер пакетов NuGet — параметры диспетчера пакетов >** .
    2. Убедитесь, что **PackageReference** выбран для **формата управления пакетами по умолчанию**.

2. Откройте проект в Visual Studio, щелкните проект правой кнопкой мыши в **Обозреватель решений** и выберите **Управление пакетами NuGet**.

3. В окне **Диспетчер пакетов NuGet** установите флажок **включить предварительные выпуски** . Затем выберите вкладку **Обзор** и выполните поиск по запросу `Microsoft.Windows.SDK.Contracts`.

4. После того как пакет `Microsoft.Windows.SDK.Contracts` будет найден, в правой области окна **Диспетчер пакетов NuGet** выберите **версию** пакета, которую необходимо установить, на основе версии Windows 10, которую вы хотите использовать.

    * **10.0.18362. xxxx-Preview**: выберите это для Windows 10, версия 1903.
    * **10.0.17763. xxxx-Preview**: выберите это для Windows 10, версия 1809.
    * **10.0.17134. xxxx-Preview**: выберите это для Windows 10, версия 1803.

5. Нажмите кнопку **Установить**.

#### <a name="to-add-the-required-references-manually"></a>Добавление необходимых ссылок вручную

1. Откройте диалоговое окно **Диспетчера ссылок**, нажмите кнопку **Обзор**, а затем выберите **Все файлы**.

    ![диалоговое окно "добавление ссылки"](images/desktop-to-uwp/browse-references.png)

2. Добавьте ссылку на эти файлы.

    |Файл|Местоположение|
    |--|--|
    |System.Runtime.WindowsRuntime|C:\Windows\Microsoft.NET\Framework\v4.0.30319|
    |System.Runtime.WindowsRuntime.UI.Xaml|C:\Windows\Microsoft.NET\Framework\v4.0.30319|
    |System.Runtime.InteropServices.WindowsRuntime|C:\Windows\Microsoft.NET\Framework\v4.0.30319|
    |Windows. winmd|C:\Program Files (x86) \Windows Kits\10\UnionMetadata\\<*SDK версии*> \факаде|
    |Windows.Foundation.UniversalApiContract.winmd|C:\Program Files (x86) \Windows Kits\10\References\\<*SDK версия*> \виндовс.фаундатион.универсалапиконтракт\<*версия*>|
    |Windows.Foundation.FoundationContract.winmd|C:\Program Files (x86) \Windows Kits\10\References\\<*SDK версия*> \виндовс.фаундатион.фаундатионконтракт\<*версия*>|

3. В окне **Свойства** установите в поле **Копировать локально** каждого файла *.winmd* значение **False**.

    ![поле копирования локальных файлов](images/desktop-to-uwp/copy-local-field.png)

### <a name="modify-a-c-win32-project-to-use-windows-runtime-apis"></a>Изменение проекта C++ Win32 для использования среда выполнения Windows API

Используйте [ C++/WinRT](https://docs.microsoft.com/windows/uwp/cpp-and-winrt-apis/) для использования API Среда выполнения Windows. C++/WinRT — это полностью стандартная проекция языка C++17 для API среды выполнения Windows (WinRT), реализованная как библиотека на основе файлов заголовков и предназначенная для предоставления вам первоклассного доступа к современным интерфейсам API Windows.

Чтобы настроить проект для C++/WinRT, выполните следующие действия.

* Для новых проектов можно установить [ C++расширение Visual Studio/WinRT (VSIX)](https://marketplace.visualstudio.com/items?itemName=CppWinRTTeam.cppwinrt101804264) и использовать один из шаблонов проектов C++/WinRT, входящих в это расширение.
* Для существующих проектов пакет NuGet [Microsoft. Windows. кппвинрт](https://www.nuget.org/packages/Microsoft.Windows.CppWinRT/) можно установить в проекте.

Дополнительные сведения об этих параметрах см. в [этой статье](/windows/uwp/cpp-and-winrt-apis/intro-to-using-cpp-with-winrt#visual-studio-support-for-cwinrt-xaml-the-vsix-extension-and-the-nuget-package).

## <a name="add-windows-10-experiences"></a>Добавление возможностей Windows 10

Теперь все готово для добавления современных видов взаимодействия, которые активируются, когда пользователь запускает ваше приложение в Windows 10. Используйте этот процесс разработки.

:white_check_mark: **Сначала определите, какие возможности вы хотите добавить**

Выбор огромен. Например, можно упростить поток заказа на покупку с помощью API- [интерфейсов монетизацию](/windows/uwp/monetize)или [обратить внимание на ваше приложение](/windows/uwp/design/shell/tiles-and-notifications/adaptive-interactive-toasts) , когда у вас есть нечто интересное, например новая картинка, опубликованная другим пользователем.

![Всплывающее уведомление](images/desktop-to-uwp/toast.png)

Даже если пользователи не обращают внимания на ваше сообщение или закрывают его, они могут снова увидеть его в центре уведомлений, а затем щелкнуть по сообщению, чтобы открыть приложение. Это повышает уровень участия в работе приложения и обладает дополнительной надбавкой к обеспечению тесной интеграции приложения с операционной системой. Мы покажем вам код для этого интерфейса чуть позже в этой статье.

Дополнительные идеи см. в [документации по UWP](/windows/uwp/get-started/) .

:white_check_mark: **Решите, заняться ли улучшением или расширением возможностей**

Вы часто слышите, что мы будем использовать термины *улучшения* и *расширения*, поэтому мы поговорим о том, что именно означает каждое из этих терминов.

Мы используем термин « *улучшение* » для описания среда выполнения Windows интерфейсов API, которые можно вызывать непосредственно из классического приложения (независимо от того, выбрано ли упаковка приложения в пакет MSIX). Когда вы выбрали интерфейс Windows 10, укажите API-интерфейсы, которые необходимо создать, а затем проверьте, появился ли этот API в [этом списке](desktop-to-uwp-supported-api.md). Это список интерфейсов API, которые можно вызывать непосредственно из классического приложения. Если выбранный вами API не входит в этот список, это обосновано тем, что функции, связанные с этим API, могут работать только в рамках процесса UWP. Часто они включают интерфейсы API, которые визуализируют XAML UWP, например элемент управления картой UWP или запрос безопасности Windows Hello.

> [!NOTE]
> Хотя интерфейсы API, которые визуализируют XAML UWP, обычно не могут вызываться напрямую с рабочего стола, вы можете использовать альтернативные подходы. Если вы хотите разместить элементы управления XAML UWP или другие пользовательские визуальные возможности, можно использовать [острова XAML](xaml-islands.md) (начиная с Windows 10, версия 1903) и [визуальный уровень](visual-layer-in-desktop-apps.md) (начиная с windows 10, версия 1803). Эти функции можно использовать в упакованных или неупакованных настольных приложениях.

Если вы решили упаковать классическое приложение в пакет MSIX, можно *расширить* приложение, добавив в решение проект UWP. Проект для настольных систем по-прежнему является точкой входа вашего приложения, но проект UWP предоставляет доступ ко всем API, которые не отображаются в [этом списке](desktop-to-uwp-supported-api.md). Классическое приложение может взаимодействовать с процессом UWP с помощью службы приложений, и у нас есть множество рекомендаций по ее настройке. Если вы хотите добавить опыт, требующий проекта UWP, см. статью [расширение с помощью компонентов UWP](desktop-to-uwp-extend.md).

:white_check_mark: **Примеры контрактов API**

Если вы можете вызвать API непосредственно из классического приложения, откройте браузер и выполните поиск справочного раздела для этого API.
В сводных сведениях по этому API-интерфейсу вы найдете таблицу, в которой приводится описание контракта API для этого API. Пример такой таблицы приведен ниже.

![Таблица контракта API](images/desktop-to-uwp/contract-table.png)

Если у вас есть классическое приложение на основе .NET, добавьте ссылку на этот контракт API и присвойте свойству **Копировать локально** этого файла значение **False**. Если у вас есть проект на основе C++, добавьте в свои **Дополнительные каталоги включаемых файлов** путь к папке, содержащей этот контракт.

:white_check_mark: **Вызов API-интерфейсов для добавления возможности**

Ниже приведен код, используемый для отображения окна уведомлений, которое мы рассмотрели ранее. Эти API-интерфейсы отображаются в этом [списке](desktop-to-uwp-supported-api.md) , поэтому вы можете добавить этот код в классическое приложение и запустить его прямо сейчас.

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

Вы можете модернизировать приложение для Windows 10 без необходимости создавать новую ветвь и поддерживать отдельные базы кода.

Если вы хотите создать отдельные двоичные файлы для пользователей Windows 10, используйте условную компиляцию. Если вы предпочитаете создать один набор двоичных файлов, которые развертываются для всех пользователей Windows, используйте проверки во время выполнения.

Вкратце рассмотрим все варианты.

### <a name="conditional-compilation"></a>Условная компиляция

Можно вести одну базу кода и компилировать набор двоичных файлов только для пользователей Windows 10.

Сначала добавьте конфигурацию новой сборки в свой проект.

![Конфигурация сборки](images/desktop-to-uwp/build-config.png)

Для этой конфигурации сборки создайте константу, которая будет обозначать код, вызывающий среда выполнения Windows API.  

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

Можно скомпилировать один набор двоичных файлов для всех пользователей Windows независимо от того, какой версией Windows они пользуются. Приложение вызывает среда выполнения Windows API, только если пользователь запускает приложение в виде упакованного приложения в Windows 10.

Самый простой способ добавить проверки среды выполнения в код — это установить этот пакет NuGet: [вспомогательные методы моста](https://www.nuget.org/packages/DesktopBridge.Helpers/) , а затем использовать метод ``IsRunningAsUWP()`` для разворота всего кода, который вызывает среда выполнения Windows API. Дополнительные сведения см. в этой записи блога: [Мост для классических приложений — определение контекста приложения](https://blogs.msdn.microsoft.com/appconsult/2016/11/03/desktop-bridge-identify-the-applications-context/).

## <a name="related-samples"></a>Примеры по теме

* [Пример Hello World](https://github.com/Microsoft/DesktopBridgeToUWP-Samples/tree/master/Samples/HelloWorldSample)
* [Вторичная плитка](https://github.com/Microsoft/DesktopBridgeToUWP-Samples/tree/master/Samples/SecondaryTileSample)
* [Пример API хранилища](https://github.com/Microsoft/DesktopBridgeToUWP-Samples/tree/master/Samples/StoreSample)
* [Приложение WinForms, реализующее Упдатетаск UWP](https://github.com/Microsoft/DesktopBridgeToUWP-Samples/tree/master/Samples/WinFormsUpdateTaskSample)
* [Примеры моста для классических приложений в UWP](https://github.com/Microsoft/DesktopBridgeToUWP-Samples)

## <a name="support-and-feedback"></a>Поддержка и отзывы

**Поиск ответов на вопросы**

Есть вопросы? Задайте их на Stack Overflow. Наша команда следит за этими [тегами](https://stackoverflow.com/questions/tagged/project-centennial+or+desktop-bridge). Вы также можете задать нам вопросы [здесь](https://social.msdn.microsoft.com/Forums/en-US/home?filter=alltypes&sort=relevancedesc&searchTerm=%5BDesktop%20Converter%5D).

**Отправьте отзыв или получите предложения по функциям**

См. раздел [UserVoice](https://wpdev.uservoice.com/forums/110705-universal-windows-platform/category/161895-desktop-bridge-centennial)
