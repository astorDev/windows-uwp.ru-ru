---
title: Разработка Creators заголовок с Visual Studio
description: Начало разработки Xbox Live Creators Program заголовок с помощью Visual Studio
ms.assetid: 6952dac0-66ff-4717-b3c7-8b3792e834e3
ms.date: 11/28/2017
ms.topic: article
keywords: Xbox live, xbox, игры, универсальной платформы Windows, windows 10, один xbox, xbox live creators, visual studio
ms.localizationpriority: medium
ms.openlocfilehash: 854d6cf138d1c2d5ef8d68cdf7033e40ba900120
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57614129"
---
# <a name="get-started-developing-an-xbox-live-creators-program-title-with-visual-studio"></a>Приступите к разработке title Xbox Live Creators Program, с помощью Visual Studio

> [!NOTE]
> Есть подключаемый модуль для игр, которые разрабатываются с помощью Unity. См. в разделе [разрабатывать Creators заголовок с Unity](develop-creators-title-with-unity.md) для получения дополнительной информации.

## <a name="requirements"></a>Требования

1. Регистрация в  **[программе для разработчиков центра партнеров](https://developer.microsoft.com/store/register)**.
2. **[Windows 10](https://microsoft.com/windows)**.
3. **[Visual Studio 2015](https://www.visualstudio.com/)**  (или более поздней версии) с **средства разработки универсальных приложений Windows**.
4. **[Пакет SDK для Windows 10](https://developer.microsoft.com/windows/downloads/windows-10-sdk) v10.0.10586.0** или более поздней версии.

> [!IMPORTANT]
> Visual Studio 2017 является обязательным, если с помощью пакета SDK для Windows 10 версией 10.0.15063.0 (также называется Creators Update) или более поздней версии.

## <a name="create-a-new-product-in-partner-center"></a>Создание нового продукта в центре партнеров

Каждый заголовок, Xbox Live должен иметь продукт, созданный в [центра партнеров](https://partner.microsoft.com/dashboard) прежде чем можно будет возможность входа в систему и вызывать Xbox Live службы. См. в разделе [Создание новый заголовок Creators](create-and-test-a-new-creators-title.md) Дополнительные сведения.

## <a name="configuring-your-development-device"></a>Чтобы настроить устройство разработки

Предварительная настройка требуется выполнение следующих действий на устройстве, чтобы пользователь мог успешно входа с помощью Xbox Live и вызвать различные службы Xbox Live.

### <a name="set-your-sandbox"></a>Задать изолированную среду

"Песочницы" предоставляют способ сохранить ваши [конфигурации-службы Xbox Live](xbox-live-service-configuration-creators.md) изолированы от розничной торговли, пока вы не будете готовы к выпуску название. Некоторые данные, собираемые относится только к "песочнице". Например предположим, что название определяет stat вызывается *Headshots*, данных и накопления некоторое количество Headshots в учетной записи пользователя при тестировании название. Это значение будет относящиеся к заголовок вашей изолированной среды для разработки, и если вы перешли воспроизведение розничную версию заголовка, headshots бы не переносятся.

См. в разделе [Xbox Live "песочницы"](xbox-live-sandboxes-creators.md) статью, чтобы узнать больше и узнать, как задать изолированную среду.

### <a name="sign-in-with-an-xbox-live-account-that-has-been-authorized-for-testing"></a>Войдите, используя учетную запись Xbox Live, разрешенный для тестирования

Для входа в изолированную среду разработки, необходимо подготовить регулярных учетной записи Майкрософт (MSA) для доступа к вашей "песочницы". Это обеспечивает повышенную безопасность для вашей наименований разработки, а также некоторые другие преимущества.

Дополнительные сведения о тестовых учетных записей и как ее создать, см. в разделе [авторизовать Xbox Live учетных записей для тестирования в вашей среде](authorize-xbox-live-accounts.md).

## <a name="visual-studio-project-setup"></a>Настройка проекта Visual Studio

### <a name="1-open-a-uwp-project"></a>1. Откройте проект универсальной платформы Windows
Если у вас еще нет существующего проекта UWP, можно создать его, сделав следующее:

1. В Visual Studio **файл** > **новый** > **проекта**.
2. В **новый проект** выберите **Visual C#**   >  **Windows** > **универсальной** узла в левой области, а затем выберите **пустое приложение (универсальные Windows)** на правой панели.
3. В нижней части диалогового окна присвойте проекту имя и расположение проекта.
4. Укажите целевую версию и Минимальная версия Windows 10 SDK. См. в разделе [Выбор версии UWP](https://docs.microsoft.com/windows/uwp/updates-and-versions/choose-a-uwp-version) Дополнительные сведения.

![Создание проекта в Visual STUDIO](../images/getting_started/vs-create-project.gif)

> [!NOTE]
> > Xbox Live API (XSAPI) требует использования версии 10.0.10586.0 или более поздней версии.

### <a name="2-add-references-to-the-xbox-live-api-xsapi-in-your-project"></a>2. Добавьте ссылки на Xbox Live API (XSAPI) в проекте
API службы Xbox имеет вариации для C++ и WinRT и их названиям структурированные как **Microsoft.Xbox.Live.SDK.*. UWP**. Дополнительные сведения об универсальной платформы Windows на Xbox One в [ https://docs.microsoft.com/windows/uwp/xbox-apps/getting-started ](https://docs.microsoft.com/windows/uwp/xbox-apps/getting-started). Пакет SDK C++ можно использовать для C++ игровых движков, а пакет SDK WinRT для игровых движков, написанный на языке C++, C#, или JavaScript. При использовании WinRT с обработчиком C++, используется C + +/ CX, использующего шляпы (^). C++ является рекомендованным интерфейсом API для игровых движков C++.  

Чтобы использовать API Xbox Live из проекта, можно либо добавить ссылки на двоичные файлы, с помощью пакетов NuGet или добавлении источника API. Добавление пакетов NuGet ускоряет компиляцию, а добавление источника упрощает отладку. В этой статье поможет выполнить с помощью пакетов NuGet. Если вы хотите использовать источник, обратитесь к [компиляции Xbox Live API-интерфейсы источника в ваш проект UWP](../get-started-with-partner/add-xbox-live-apis-source-to-a-uwp-project.md). Можно добавить пакет Xbox Live SDK NuGet для по:

1. В Visual Studio перейдите в раздел **средства** > **диспетчер пакетов NuGet** > **управление пакетами NuGet для решения...** .
2. В диспетчере пакетов NuGet, щелкните **Обзор** и введите **Xbox.Live.SDK** в поле поиска.
3. Выберите версию пакета SDK Xbox Live, вы хотите использовать в списке слева. В этом случае мы будем использовать пакет Microsoft.Xbox.Live.SDK.WinRT.UWP.
3. В правой части окна установите флажок рядом с проектом и нажмите кнопку **установить**.

![Добавление XBL с помощью NuGet](../images/getting_started/vs-add-nuget-xbl.gif)

#### <a name="optionally-include-xsapi-header-in-your-project"></a>При необходимости включить XSAPI заголовка в проект

Для проектов на основе Microsoft.Xbox.Live.SDK.Cpp.*, необходимо включить `xsapi\\services.h` в проекте C++ для перевода в заголовке для Xbox Live службы API (XSAPI) NuGet упаковки. Перед включением XSAPI заголовка, необходимо определить `XBOX_LIVE_CREATORS_SDK`. Это ограничивает контактную зону API, чтобы только тех интерфейсов API, которые могут использоваться разработчиками в Xbox Live Creators Program. Например:

```c++
#define XBOX_LIVE_CREATORS_SDK
#include "xsapi\services.h"
```
### <a name="3-optional-using-connected-storage"></a>3. (Необязательно) С помощью подключенного хранилища
Если вы хотите использовать [подключенные хранилища](../storage-platform/connected-storage/connected-storage-technical-overview.md) службы, вам потребуется доступ к `Windows.Gaming.XboxLive.Storage` пространства имен. В зависимости от версии пакета SDK Windows, который вы используете может потребоваться установка дополнительного содержимого или вручную добавить ссылки на проект, чтобы использовать его. Если вы предназначенные пакета SDK для Windows 10 10.0.16299 или более поздней версии, затем можно будет получить доступ к подключенных хранения пространство имен без выполнения дополнительных действий.

#### <a name="windows-10-sdk-version-10015063-or-lower"></a>Пакет SDK для Windows 10 версии 10.0.15063 или более ранней версии
Если вы хотите использовать подключения хранилища, необходимо установить пакет SDK расширения платформы Live Xbox, перед добавлением ссылки на проект. Это можно сделать:

1. Загрузите и извлеките [Live SDK расширения платформы Xbox](https://aka.ms/xblextsdk).
2. После извлечения, запустите включенного файла MSI, которая совпадает с версией пакета SDK для Windows 10, который используется.

После установки пакета SDK расширения платформы Live Xbox, необходимо добавить ссылку на него в Visual Studio. Это можно сделать:

1. В **обозревателе решений**, щелкните правой кнопкой мыши **ссылки** узла и выбора **добавить ссылку...**
2. В левой части **диспетчер ссылок** диалоговом окне выберите **универсальной Windows** > **расширения**.
3. В появившемся списке найдите **расширения рабочего стола Windows для UWP** и установите флажок напротив версию, которая соответствует вашей пакета SDK для Windows 10.
4. Нажмите кнопку **ОК**.

![Добавить новую ссылку в Visual STUDIO](../images/getting_started/get-started-vs-add-ref.png)

### <a name="4-associate-your-visual-studio-project-with-your-uwp-app"></a>4. Связать проект Visual Studio с помощью приложения универсальной платформы Windows

Вашей игры иметь возможность входа в систему, должен быть связан с продуктом, вы создали в центре партнеров. Перенос игры в Visual Studio можно связать с помощью мастера взаимосвязей Store. В Visual Studio сделайте следующее:

1.  Щелкните правой кнопкой мыши основной проект (запускаемым проектом), нажмите кнопку **Store** > **связать приложение с Store...**
2.  Вход в систему с **учетную запись разработчика Windows** используется для создания приложения, если запрос и следуйте инструкциям на экране.

> [!TIP]
> См. в разделе [упаковка приложений](https://docs.microsoft.com/windows/uwp/packaging/) Дополнительные сведения о подготовке игры для Windows Store.

### <a name="5-add-internet-capabilities-to-your-visual-studio-project"></a>5. Добавьте Интернет-возможностей в проект Visual Studio
Проект UWP, потребуется указать Интернет-возможностей для взаимодействия с Xbox Live. Эти свойства можно задать с:

1. Дважды щелкните **package.appxmanifest** файл в Visual Studio, чтобы открыть **конструктор манифеста**.
2. Щелкните **возможности** вкладку и проверьте **Интернет (клиент)**.

![Добавить новую ссылку в Visual STUDIO](../images/getting_started/get-started-vs-add-capability.png)

### <a name="6-associate-your-visual-studio-project-with-your-xbox-live-enabled-title"></a>6. Связать проект Visual Studio вместе с названием Xbox Live включена

Чтобы взаимодействовать со службой Xbox Live, необходимо добавить в проект файл конфигурации службы. Это может просто осуществляться с помощью:

1. Запускаемый проект, щелкните правой кнопкой мыши и выберите **добавить** > **новый элемент**.
2. Выберите **текстовый файл** введите и назовите его **xboxservices.config**.
3. Щелкнуть правой кнопкой мыши файл, выберите **свойства** и убедитесь, что:
    1. **Действие при сборке** присваивается **содержимого**, и  
    2. **Копировать в выходной каталог** присваивается **всегда Копировать**.
5.  Изменить файл конфигурации, содержащий следующий шаблон, заменив **TitleId** и **PrimaryServiceConfigId** с помощью значения, применяемые к название. Правильные значения можно получить на корневую страницу Xbox Live в центре партнеров. **PrimaryServiceConfigId** отображается в центре партнеров, как **SCID**.

```json
    {
       "TitleId" : "your title ID (JSON number in decimal)",
       "PrimaryServiceConfigId" : "your primary service config ID",
       "XboxLiveCreatorsTitle" : true
    }
```

Например:

```json
    {
        "TitleId" : 1563044810,
        "PrimaryServiceConfigId" : "12200100-88da-4d8b-af88-e38f5d2a2bca",
        "XboxLiveCreatorsTitle" : true
    }
```

> [!TIP]
> Все значения внутри xboxservices.config чувствительны к регистру. См. в разделе [конфигурации службы](../xbox-live-service-configuration.md) Дополнительные сведения о получении TitleID и PrimaryServiceConfigId.

## <a name="learn-more"></a>Подробнее

[Примеры Xbox Live SDK](https://github.com/Microsoft/xbox-live-samples/tree/master/Samples/CreatorsSDK) в разделе продемонстрировать интерфейсы API, доступные для разработчиков в Xbox Live Creators program. Чтобы использовать образцы, необходимо будет изменить изолированную среду для XDKS.1.
