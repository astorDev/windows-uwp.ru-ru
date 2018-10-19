---
author: normesta
Description: Enhance your desktop application for Windows 10 users by using Universal Windows Platform (UWP) APIs.
Search.Product: eADQiWindows 10XVcnh
title: Улучшение классического приложения для Windows 10
ms.author: normesta
ms.date: 08/12/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 392f8166e16c028a57bc9e27039a9884f1d9714a
ms.sourcegitcommit: e16c9845b52d5bd43fc02bbe92296a9682d96926
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/19/2018
ms.locfileid: "4948723"
---
# <a name="enhance-your-desktop-application-for-windows-10"></a>Улучшите свое классическое приложение для Windows 10

API-интерфейсы UWP можно использовать для добавления современных видов взаимодействия, активирующихся для пользователей Windows 10.

Сначала настройте свой проект. Затем добавьте возможности Windows 10. Можно создавать отдельные версии для пользователей Windows 10 либо распространять абсолютно одинаковые двоичные файлы для всех пользователей независимо от того, какой версией Windows они пользуются.

## <a name="first-set-up-your-project"></a>Сначала настройте свой проект

Необходимо внести некоторые изменения в проект, чтобы использовать API-интерфейсы UWP.

### <a name="modify-a-net-project-to-use-uwp-apis"></a>Изменение проекта .NET для использования API-интерфейсов UWP

Откройте диалоговое окно **Диспетчера ссылок**, нажмите кнопку **Обзор**, а затем выберите **Все файлы**.

![диалоговое окно "добавление ссылки"](images/desktop-to-uwp/browse-references.png)

Затем добавьте ссылку на эти файлы.

|Файл|Расположение|
|--|--|
|System.Runtime.WindowsRuntime|C:\Program Files (x86)\Reference Assemblies\Microsoft\Framework\\.NETCore\v4.5|
|System.Runtime.WindowsRuntime.UI.Xaml|C:\Program Files (x86)\Reference Assemblies\Microsoft\Framework\\.NETCore\v4.5|
|System.Runtime.InteropServices.WindowsRuntime|C:\Program Files (x86)\Reference Assemblies\Microsoft\Framework\\.NETCore\v4.5|
|Windows.winmd|C:\Program Files (x86)\Windows Kits\10\UnionMetadata\Facade|
|Windows.Foundation.UniversalApiContract.winmd|C:\Program Files (x86)\Windows Kits\10\References\<*версия sdk*>\Windows.Foundation.UniversalApiContract\<*версия*>|
|Windows.Foundation.FoundationContract.winmd|C:\Program Files (x86)\Windows Kits\10\References\<*версия sdk*>\Windows.Foundation.FoundationContract\<*версия*>|

В окне **Свойства** установите в поле **Копировать локально** каждого файла *.winmd* значение **False**.

![поле копирования локальных файлов](images/desktop-to-uwp/copy-local-field.png)

### <a name="modify-a-c-project-to-use-uwp-apis"></a>Изменение проекта C++ для использования API-интерфейсов UWP

Откройте страницы свойств своего проекта.

В разделе **Общие** группы параметров **C/C++** установите в поле **Использовать расширение среды выполнения Windows** значение **Да (/ZW)**.

   ![Использовать расширение среды выполнения Windows](images/desktop-to-uwp/consume-runtime-extensions.png)

Откройте диалоговое окно **Дополнительные каталоги #using** и добавьте эти каталоги.

* %VSInstallDir%\Common7\IDE\VC\vcpackages
* C:\Program Files (x86)\Windows Kits\10\UnionMetadata
* C:\Program Files (x86)\Windows Kits\10\References\Windows.Foundation.UniversalApiContract\<*последняя версия*>
* C:\Program Files (x86)\Windows Kits\10\References\Windows.Foundation.FoundationContract\<*последняя версия*>

Откройте диалоговое окно **Дополнительные каталоги включаемых файлов** и добавьте этот каталог: C:\Program Files (x86) \Windows Kits\10\Include\<*последняя версия*>\um

![Дополнительные каталоги включаемых файлов](images/desktop-to-uwp/additional-include.png)

В разделе **Создание кода** группы параметров **C/C++** установите параметру **Включить минимальное перестроение** значение **Нет (/GM-)**.

![Включить минимальное перестроение](images/desktop-to-uwp/disable-min-build.png)


## <a name="add-windows-10-experiences"></a>Добавление возможностей Windows 10

Теперь все готово для добавления современных видов взаимодействия, которые активируются, когда пользователь запускает ваше приложение в Windows 10. Используйте этот процесс разработки.

:white_check_mark: **Сначала определите, какие возможности вы хотите добавить**

Выбор огромен. Например можно упростить процесс оформления покупки с помощью API-интерфейсов монетизации либо привлечь внимание к вашему приложению, когда у вас есть нечто заслуживающее внимания для совместного использования, например новую фотографию, добавленную другим пользователем.

![Всплывающее уведомление](images/desktop-to-uwp/toast.png)

Даже если пользователи не обращают внимания на ваше сообщение или закрывают его, они могут снова увидеть его в центре уведомлений, а затем щелкнуть по сообщению, чтобы открыть приложение. Это увеличивает объем взаимодействия с приложением и обеспечивает настройки приложения глубоко интеграции с операционной системой. Мы покажем код для реализации этой возможности чуть позже.

Посетите наш [Центр разработчиков](https://developer.microsoft.com/windows), чтобы найти и другие идеи.

:white_check_mark: **Решите, заняться ли улучшением или расширением возможностей**

Вам часто будут встречаться термины "улучшить" и "расширить", поэтому сейчас мы поясним, какой именно смысл мы вкладываем в эти слова.

Мы используем термин "улучшить" для описания API-интерфейсов UWP, которые можно вызывать непосредственно из классического приложения. При выборе возможности, предлагаемой в Windows 10, определите API-интерфейсы, которые вам необходимы для ее реализации, а затем проверьте, входит ли такой API в этот [список](desktop-to-uwp-supported-api.md). Это список API-интерфейсов, которые можно вызывать непосредственно из классического приложения. Если выбранный вами API не входит в этот список, это обосновано тем, что функции, связанные с этим API, могут работать только в рамках процесса UWP. Зачастую в их число входят API-интерфейсы, которые отображают современные элементы пользовательского интерфейса, например элемент управления картой UWP или запрос безопасности Windows Hello.

Необходимо отметить, что если вы хотите включить эти возможности в приложение, просто "расширьте" свое приложение, добавив в решение проект UWP. Проект классического приложения по-прежнему является точкой входа для вашего приложения, но проект UWP предоставляет доступ ко всем API-интерфейсам, которые не входят в этот [список](desktop-to-uwp-supported-api.md). Классические приложения могут взаимодействовать с процессами UWP с помощью службы приложения, и мы приведем множество рекомендаций по способам настройки этой функции. Если вы хотите добавить возможность, для которой требуется проект UWP, см. раздел [Расширение с помощью UWP](desktop-to-uwp-extend.md).

:white_check_mark: **Примеры контрактов API**

Если вы можете вызывать API непосредственно из классического приложения, откройте браузер и найдите справочный раздел для этого API-интерфейса.
В сводных сведениях по этому API-интерфейсу вы найдете таблицу, в которой приводится описание контракта API для этого API. Пример такой таблицы приведен ниже.

![Таблица контракта API](images/desktop-to-uwp/contract-table.png)

Если у вас есть классическое приложение на основе .NET, добавьте ссылку на этот контракт API и присвойте свойству **Копировать локально** этого файла значение **False**. Если у вас есть проект на основе C++, добавьте в свои **Дополнительные каталоги включаемых файлов** путь к папке, содержащей этот контракт.

:white_check_mark: **Вызов API-интерфейсов для добавления возможности**

Ниже приведен код, используемый для отображения окна уведомлений, которое мы рассмотрели ранее. Эти API-интерфейсы внесены в [список](desktop-to-uwp-supported-api.md), так что вы можете добавить этот код в свое классическое приложение и запустить его прямо сейчас.

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

Можно модернизировать приложение для Windows 10, не создавая новую ветвь и не поддерживая раздельные базы кода.

Если вы хотите создать отдельные двоичные файлы для пользователей Windows 10, используйте условную компиляцию. Если вы предпочитаете создать один набор двоичных файлов, которые развертываются для всех пользователей Windows, используйте проверки во время выполнения.

Вкратце рассмотрим все варианты.

### <a name="conditional-compilation"></a>Условная компиляция

Можно вести одну базу кода и компилировать набор двоичных файлов только для пользователей Windows 10.

Сначала добавьте конфигурацию новой сборки в свой проект.

![Конфигурация сборки](images/desktop-to-uwp/build-config.png)

Создайте константу для этой конфигурации сборки, чтобы определить код, который будет вызывать API-интерфейсы UWP.  

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

Можно скомпилировать один набор двоичных файлов для всех пользователей Windows независимо от того, какой версией Windows они пользуются. Приложение вызывает API-интерфейсы UWP только в том случае, если пользователь работает приложение как упакованное приложение в Windows 10.

Для добавления проверок во время выполнения в код проще всего установить этот пакет Nuget: [Вспомогательные элементы моста для классических приложений](https://www.nuget.org/packages/DesktopBridge.Helpers/), а затем использовать метод ``IsRunningAsUWP()`` для отсеивания всего кода UWP. Дополнительные сведения см. в этой записи блога: [Мост для классических приложений — определение контекста приложения](https://blogs.msdn.microsoft.com/appconsult/2016/11/03/desktop-bridge-identify-the-applications-context/).

## <a name="related-video"></a>Видео по теме

<iframe src="https://mva.microsoft.com/en-US/training-courses-embed/developers-guide-to-the-desktop-bridge-17373/Demo-Use-UWP-APIs-in-Your-Code-3d78c6WhD_9506218965" width="636" height="480" allowFullScreen frameBorder="0"></iframe>

## <a name="related-samples"></a>Примеры по теме

* [Пример кода "Hello World"](https://github.com/Microsoft/DesktopBridgeToUWP-Samples/tree/master/Samples/HelloWorldSample)
* [Вспомогательная плитка](https://github.com/Microsoft/DesktopBridgeToUWP-Samples/tree/master/Samples/SecondaryTileSample)
* [Пример API Store](https://github.com/Microsoft/DesktopBridgeToUWP-Samples/tree/master/Samples/StoreSample)
* [Приложение WinForms, реализующее UWP UpdateTask](https://github.com/Microsoft/DesktopBridgeToUWP-Samples/tree/master/Samples/WinFormsUpdateTaskSample)
* [Примеры для преобразования классических приложений в приложения UWP](https://github.com/Microsoft/DesktopBridgeToUWP-Samples)


## <a name="support-and-feedback"></a>Поддержка и отзывы

**Поиск ответов на вопросы**

Есть вопросы? Задайте их на Stack Overflow. Наша команда следит за этими [тегами](http://stackoverflow.com/questions/tagged/project-centennial+or+desktop-bridge). Вы также можете задать нам вопросы [здесь](https://social.msdn.microsoft.com/Forums/en-US/home?filter=alltypes&sort=relevancedesc&searchTerm=%5BDesktop%20Converter%5D).

**Оставьте отзыв или предложите новые возможности для реализации**

См. раздел [UserVoice](https://wpdev.uservoice.com/forums/110705-universal-windows-platform/category/161895-desktop-bridge-centennial)
