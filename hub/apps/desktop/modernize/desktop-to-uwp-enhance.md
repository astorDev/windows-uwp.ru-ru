---
Description: Расширьте возможности приложения для пользователей Windows 10, применяя API для среды выполнения Windows.
title: Вызов API для среды выполнения Windows в классических приложениях
ms.date: 08/20/2019
ms.topic: article
keywords: windows 10, uwp
ms.author: mcleans
author: mcleanbyron
ms.localizationpriority: medium
ms.custom: 19H1
ms.openlocfilehash: 444699c00a17dd90fe4c08c44ff56d540cadb3d6
ms.sourcegitcommit: ef723e3d6b1b67213c78da696838a920c66d5d30
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/02/2020
ms.locfileid: "82729859"
---
# <a name="call-windows-runtime-apis-in-desktop-apps"></a>Вызов API для среды выполнения Windows в классических приложениях

С помощью API-интерфейсов универсальной платформы Windows (UWP) в классических приложениях вы можете добавить современные возможности, которые привлекут внимание пользователей Windows 10.

Прежде всего настройте в проекте необходимые ссылки. Затем вызовите API среды выполнения Windows из кода приложения, чтобы добавить в него новые возможности Windows 10. Вы можете создавать отдельные версии для пользователей Windows 10 либо распространять одинаковые двоичные файлы для всех пользователей независимо от того, с какой версией Windows они работают.

Некоторые API среды выполнения Windows поддерживаются только в классических приложениях с [идентификатором пакета](modernize-packaged-apps.md). Подробнее см. в разделе [Доступные API среды выполнения Windows](desktop-to-uwp-supported-api.md).

## <a name="set-up-your-project"></a>Настройка проекта

Вам потребуется внести в проект некоторые изменения, чтобы использовать API среды выполнения Windows.

### <a name="modify-a-net-project-to-use-windows-runtime-apis"></a>Настройка проекта .NET для использования API среды выполнения Windows

Для проектов .NET есть два варианта.

* Если приложение ориентировано на Windows 10 версии 1803 или более поздней версии, вы можете установить пакет NuGet со всеми необходимыми ссылками.
* Также эти ссылки можно добавить вручную.

#### <a name="to-use-the-nuget-option"></a>Использование варианта с NuGet

1. Убедитесь, что [ссылки на пакеты](https://docs.microsoft.com/nuget/consume-packages/package-references-in-project-files) активны:

    1. В Visual Studio выберите элементы **Сервис -> Диспетчер пакетов NuGet -> Параметры диспетчера пакетов**.
    2. Убедитесь, что для **формата управления пакетами по умолчанию** установлено значение **PackageReference**.

2. В Visual Studio щелкните правой кнопкой мыши проект в **обозревателе решений** и выберите элемент **Управление пакетами NuGet**.

3. В окне **диспетчера пакетов NuGet** выберите вкладку **Обзор** и найдите `Microsoft.Windows.SDK.Contracts`.

4. Когда пакет `Microsoft.Windows.SDK.Contracts` будет найден, выберите на правой панели **диспетчера пакетов NuGet** нужную **версию** пакета в зависимости от версии Windows 10, на которую нужно ориентировать приложение:

    * **10.0.18362.xxxx** — вариант для Windows 10 версии 1903;
    * **10.0.17763.xxxx** — вариант для Windows 10 версии 1809;
    * **10.0.17134.xxxx** — вариант для Windows 10 версии 1803.

5. Нажмите кнопку **Установить**.

#### <a name="to-add-the-required-references-manually"></a>Добавление требуемых ссылок вручную

1. Откройте диалоговое окно **Диспетчера ссылок**, нажмите кнопку **Обзор**, а затем выберите элемент **Все файлы**.

    ![диалоговое окно "добавление ссылки"](images/desktop-to-uwp/browse-references.png)

2. Добавьте ссылку на эти файлы.

    |Файл|Расположение|
    |--|--|
    |System.Runtime.WindowsRuntime|C:\Windows\Microsoft.NET\Framework\v4.0.30319|
    |System.Runtime.WindowsRuntime.UI.Xaml|C:\Windows\Microsoft.NET\Framework\v4.0.30319|
    |System.Runtime.InteropServices.WindowsRuntime|C:\Windows\Microsoft.NET\Framework\v4.0.30319|
    |windows.winmd|C:\Program Files (x86)\Windows Kits\10\UnionMetadata\\<*версия sdk*>\Facade|
    |Windows.Foundation.UniversalApiContract.winmd|C:\Program Files (x86)\Windows Kits\10\References\\<*версия пакета SDK*>\Windows.Foundation.UniversalApiContract\<*версия*>|
    |Windows.Foundation.FoundationContract.winmd|C:\Program Files (x86)\Windows Kits\10\References\\<*версия пакета SDK*>\Windows.Foundation.FoundationContract\<*версия*>|

3. В окне **Свойства** установите в поле **Копировать локально** значение **False** для каждого файла *.winmd*.

    ![copy-local-field](images/desktop-to-uwp/copy-local-field.png)

### <a name="modify-a-c-win32-project-to-use-windows-runtime-apis"></a>Настройка использования API среды выполнения Windows в проекте Win32 на C++

Примените [C++/WinRT](https://docs.microsoft.com/windows/uwp/cpp-and-winrt-apis/) для использования API среды выполнения Windows. C++/WinRT — это полностью стандартная проекция языка C++17 для API среды выполнения Windows (WinRT), реализованная как библиотека на основе файлов заголовков и предназначенная для предоставления вам первоклассного доступа к современным интерфейсам API Windows.

Чтобы настроить C++/WinRT для проекта, сделайте следующее:

* Для новых проектов можно установить расширение [C++/WinRT Visual Studio (VSIX)](https://marketplace.visualstudio.com/items?itemName=CppWinRTTeam.cppwinrt101804264) и использовать один из шаблонов проектов C++/WinRT, входящих в это расширение.
* Для существующих проектов можно установить в проект пакет NuGet [Microsoft.Windows.CppWinRT](https://www.nuget.org/packages/Microsoft.Windows.CppWinRT/).

Дополнительные сведения об этом варианте см. в [этой статье](/windows/uwp/cpp-and-winrt-apis/intro-to-using-cpp-with-winrt#visual-studio-support-for-cwinrt-xaml-the-vsix-extension-and-the-nuget-package).

## <a name="add-windows-10-experiences"></a>Добавление возможностей Windows 10

Теперь все готово для добавления современных видов взаимодействия, которые активируются, когда пользователь запускает ваше приложение в Windows 10. Используйте этот процесс разработки.

:white_check_mark: **Сначала определите, какие возможности нужно добавить**

Выбор огромен. Например, можно упростить оформление покупки с помощью [API монетизации](/windows/uwp/monetize) либо [привлечь внимание к приложению](/windows/uwp/design/shell/tiles-and-notifications/adaptive-interactive-toasts), когда вы можете предложить что-либо интересное, например новую фотографию, добавленную другим пользователем.

![Всплывающее](images/desktop-to-uwp/toast.png)

Даже если пользователи не обращают внимания на ваше сообщение или закрывают его, они могут снова увидеть его в центре уведомлений, а затем щелкнуть сообщение, чтобы открыть приложение. Это позволяет увеличить объем взаимодействия с приложением и обеспечивает дополнительное преимущество в виде тесной интеграции вашего приложения с операционной системой. Мы предложим код для реализации этой возможности чуть позже в этой статье.

Дополнительные сведения см. в [документации по UWP](/windows/uwp/get-started/).

:white_check_mark: **Выберите путь улучшения: дополнить или расширить**

Вам часто будут встречаться термины *дополнить* и *расширить*, поэтому сейчас мы поясним, какой именно смысл вкладываем в них.

Термин *дополнить* относится к API среды выполнения Windows, которые вы можете вызывать напрямую из классического приложения (независимо от того, применяете ли вы для приложения упаковку в пакет MSIX). При выборе новой возможности Windows 10 определите API-интерфейсы, которые вам необходимы для ее реализации, а затем проверьте, входит ли нужный API в [этот список](desktop-to-uwp-supported-api.md). Это список API-интерфейсов, которые можно вызывать непосредственно из классического приложения. Если выбранный API не входит в этот список, значит, связанные с этим API функции могут работать только в рамках процесса UWP. Сюда часто входят API-интерфейсы, которые отображают элементы XAML UWP, например элемент управления картой UWP или запрос безопасности Windows Hello.

> [!NOTE]
> API, которые отображают XAML UWP, обычно нельзя напрямую вызывать из классического приложений. Но иногда для них доступны альтернативные подходы. Если вы хотите разместить элементы управления XAML UWP или другие пользовательские визуальные объекты, попробуйте применить [XAML Islands](xaml-islands.md) (доступны в Windows 10 с версии 1903) и (или) [визуальный уровень](visual-layer-in-desktop-apps.md) (доступен в Windows 10 с версии 1803). Эти возможности можно использовать в упакованных и неупакованных классических приложениях.

Если вы решили упаковать классическое приложение в пакет MSIX, у вас есть возможность *расширить* это приложение, добавив к нему проект UWP. Проект классического приложения по-прежнему остается точкой входа для вашего приложения. Но проект UWP предоставляет доступ ко всем API-интерфейсам, которые не входят в [этот список](desktop-to-uwp-supported-api.md). Классические приложения могут взаимодействовать с процессами UWP с помощью службы приложений. У нас есть множество рекомендаций по настройке этой функции. Если вы хотите добавить возможность, для которой требуется проект UWP, см. раздел [Расширение возможностей с помощью компонентов UWP](desktop-to-uwp-extend.md).

:white_check_mark: **Добавьте ссылки на контракты API**

Если вы можете вызывать API непосредственно из классического приложения, откройте браузер и найдите справочный раздел для этого API-интерфейса.
Под общей информацией об API там представлена таблица с описанием контракта для этого API. Пример такой таблицы приведен ниже.

![Таблица контракта API](images/desktop-to-uwp/contract-table.png)

Если у вас есть классическое приложение на основе .NET, добавьте ссылку на этот контракт API и присвойте свойству **Копировать локально** этого файла значение **False**. Если у вас есть проект на основе C++, добавьте в раздел **Дополнительные каталоги включаемых файлов** путь к папке, содержащей этот контракт.

:white_check_mark: **Вызовите API-интерфейсы для добавления возможности**

Ниже приведен код для отображения окна уведомлений, которое мы уже видели ранее. Эти API-интерфейсы внесены в [этот список](desktop-to-uwp-supported-api.md), а значит, вы можете добавить этот код в классическое приложение и запустить его прямо сейчас.

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

Дополнительные сведения см. в статье об [адаптивных и интерактивных всплывающих уведомлениях](https://docs.microsoft.com/windows/uwp/design/shell/tiles-and-notifications/adaptive-interactive-toasts).

## <a name="support-windows-xp-windows-vista-and-windows-78-install-bases"></a>Поддержка установочных баз Windows XP, Windows Vista, Windows 7 и Windows 8

Вы можете модернизировать приложение для Windows 10, не создавая новую ветвь и не поддерживая раздельные базы кода.

Если вы хотите создать отдельные двоичные файлы для пользователей Windows 10, используйте условную компиляцию. Если вы предпочитаете создать один набор двоичных файлов, которые развертываются для всех пользователей Windows, используйте проверки во время выполнения.

Давайте вкратце рассмотрим эти варианты.

### <a name="conditional-compilation"></a>Условная компиляция

Можно вести одну базу кода и компилировать набор двоичных файлов только для пользователей Windows 10.

Для этого добавьте новую конфигурацию сборки в проект.

![Конфигурация сборки](images/desktop-to-uwp/build-config.png)

Создайте константу для этой конфигурации сборки, чтобы определить код, который будет вызывать API среды выполнения Windows.  

Для проектов на основе .NET эта константа называется **константой условной компиляции**.

![препроцессор](images/desktop-to-uwp/compilation-constants.png)

Для проектов на основе C++ эта константа называется **описанием препроцессора**.

![препроцессор](images/desktop-to-uwp/pre-processor.png)

Добавьте эту константу до всех блоков кода UWP.

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

Компилятор выполнит сборку этого кода, только если соответствующая константа определена в активной конфигурации сборки.

### <a name="runtime-checks"></a>Проверки во время выполнения

Можно скомпилировать один набор двоичных файлов для всех пользователей Windows независимо от того, какую версию Windows они используют. В этом случае приложение будет обращаться к API среды выполнения Windows, только если пользователь выполняет его как упакованное приложение в среде Windows 10.

Чтобы добавить в код проверки во время выполнения, проще всего установить пакет Nuget: использовать [вспомогательные средства моста для классических приложений](https://www.nuget.org/packages/DesktopBridge.Helpers/) и применить метод ``IsRunningAsUWP()`` для отсечения всего кода, в котором вызываются API среды выполнения Windows. Дополнительные сведения см. в записи блога [Мост для классических приложений — определение контекста приложения](https://blogs.msdn.microsoft.com/appconsult/2016/11/03/desktop-bridge-identify-the-applications-context/).

## <a name="related-samples"></a>Связанные примеры

* [Пример кода Hello World](https://github.com/Microsoft/DesktopBridgeToUWP-Samples/tree/master/Samples/HelloWorldSample)
* [Вспомогательная плитка](https://github.com/Microsoft/DesktopBridgeToUWP-Samples/tree/master/Samples/SecondaryTileSample)
* [Пример для API Store](https://github.com/Microsoft/DesktopBridgeToUWP-Samples/tree/master/Samples/StoreSample)
* [Приложение WinForms, реализующее UWP UpdateTask](https://github.com/Microsoft/DesktopBridgeToUWP-Samples/tree/master/Samples/WinFormsUpdateTaskSample)
* [Примеры моста UWP для классических приложений](https://github.com/Microsoft/DesktopBridgeToUWP-Samples)

## <a name="find-answers-to-your-questions"></a>Получение ответов на вопросы

Есть вопросы? Задайте их на Stack Overflow. Наша команда следит за этими [тегами](https://stackoverflow.com/questions/tagged/project-centennial+or+desktop-bridge). Вы также можете задать нам вопросы [здесь](https://social.msdn.microsoft.com/Forums/en-US/home?filter=alltypes&sort=relevancedesc&searchTerm=%5BDesktop%20Converter%5D).
