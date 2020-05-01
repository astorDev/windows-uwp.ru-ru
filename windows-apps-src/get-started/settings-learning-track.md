---
title: Сохранение и загрузка параметров в приложении UWP
description: Узнайте, как сохранять и загружать параметры приложения в приложениях универсальной платформы Windows.
ms.date: 05/07/2018
ms.topic: article
keywords: приступая к работе, uwp, windows 10, обучающий курс, параметры, сохранение параметров, загрузка параметров
ms.localizationpriority: medium
ms.custom: RS5
ms.openlocfilehash: 490dd8f0f3841fae089626ec9c283d54cc0d8cd9
ms.sourcegitcommit: 76e8b4fb3f76cc162aab80982a441bfc18507fb4
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/29/2020
ms.locfileid: "66370489"
---
# <a name="save-and-load-settings-in-a-uwp-app"></a>Сохранение и загрузка параметров в приложении UWP

В этом разделе приведены инструкции по загрузке и сохранению параметров в приложении UWP. В этом разделе представлены основные интерфейсы API, а также необходимые ссылки на дополнительные ресурсы.

Используйте параметры, чтобы запомнить настраиваемые пользователем аспекты вашего приложения. Например, средство чтения новостей может использовать параметры приложения для сохранения того, какие источники новостей следует отображать и какой шрифт следует использовать для чтения статей.

Мы рассмотрим код для сохранения и загрузки параметров приложения, включая локальные и перемещаемые параметры.

## <a name="what-do-you-need-to-know"></a>Что необходимо знать?

Параметры приложения используются для сохранения данных конфигурации, таких как предпочтения пользователя и состояние приложения.  Параметры, относящиеся к устройству, хранятся локально. Параметры, которые применяются независимо от того, на каком устройстве установлено ваше приложение, хранятся в хранилище перемещаемых данных. Параметры перемещаются между устройствами, на которых пользователь осуществляет вход в систему с одной и той же учетной записью Майкрософт и на которых совпадает версия установленного приложения.

С параметрами можно использовать следующие типы данных: целые числа, числа двойной точности с плавающей запятой, значения с плавающей запятой, знаки, строки, точки, дату и время и другое. Можно также хранить экземпляры класса [ApplicationDataCompositeValue](https://docs.microsoft.com/uwp/api/Windows.Storage.ApplicationDataCompositeValue), которые бывают полезны, когда несколько параметров необходимо рассматривать как единое целое. Например, имя шрифта и размер точки для отображения текста в области чтения приложения необходимо сохранять или восстанавливать как единое целое. Это предотвращает рассинхронизацию параметров из-за задержек в перемещении одного параметра перед другим.

Ниже приведены основные интерфейсы API для сохранения и загрузки параметров приложения.

- [Windows.Storage.ApplicationData.Current.LocalSettings](https://docs.microsoft.com/uwp/api/Windows.Storage.ApplicationData#Windows_Storage_ApplicationData_LocalSettings) получает контейнер параметров приложения из локального хранилища данных приложения. Здесь следует хранить те параметры, которые не подходят для перемещения между устройствами из-за того, что они представляют конкретное состояние устройства или являются слишком большими.
- [Windows.Storage.ApplicationData.Current.RoamingSettings](https://docs.microsoft.com/uwp/api/windows.storage.applicationdata.roamingsettings#Windows_Storage_ApplicationData_RoamingSettings) получает контейнер параметров приложения из перемещаемого хранилища данных приложения. Эти данные перемещаются между устройствами.
- [Windows.Storage.ApplicationDataContainer](https://docs.microsoft.com/uwp/api/windows.storage.applicationdatacontainer) — это контейнер, представляющий параметры приложения в виде пар "ключ-значение". Этот класс используется для создания и получения значений параметров.
- [Windows.Storage.ApplicationDataCompositeValue](https://docs.microsoft.com/uwp/api/Windows.Storage.ApplicationDataCompositeValue) представляет несколько параметров приложения, которые должны следует упорядочить как единое целое. Это удобно в тех случаях, когда один параметр не должен обновляться независимо от другого.

## <a name="save-app-settings"></a>Сохранение параметров приложения

Здесь мы расскажем о двух простых сценариях: локальное сохранение и загрузка параметра приложения и перемещение составного параметра шрифта и размера шрифта между устройствами.

 ```csharp
// Save a setting locally on the device
ApplicationDataContainer localSettings = Windows.Storage.ApplicationData.Current.LocalSettings;
localSettings.Values["test setting"] = "a device specific setting";

// Save a composite setting that will be roamed between devices
ApplicationDataContainer roamingSettings = Windows.Storage.ApplicationData.Current.RoamingSettings;
Windows.Storage.ApplicationDataCompositeValue composite = new Windows.Storage.ApplicationDataCompositeValue();
composite["Font"] = "Calibri";
composite["FontSize"] = 11;
roamingSettings.Values["RoamingFontInfo"] = composite;
 ```

Сохраните параметр на локальном устройстве путем получения **ApplicationDataContainer** для локального хранилища данных параметров с `Windows.Storage.ApplicationData.Current.LocalSettings`. Словарные пары "ключ-значение", присваиваемые этому экземпляру, сохраняются в хранилище данных параметров на локальном устройстве.

Сохраните перемещаемый параметр, используя аналогичную схему. Сначала получите **ApplicationDataContainer** для перемещаемого хранилища данных параметров с `Windows.Storage.ApplicationData.Current.RoamingSettings`. Затем назначьте пары "ключ-значение" этому экземпляру.  Эти пары "ключ-значение" будут автоматически перемещаться между устройствами.

В примере кода, приведенном выше, **ApplicationDataCompositeValue** сохраняет несколько пар "ключ-значение". Составные значения полезны в тех случаях, когда у вас есть несколько параметров, которые не должны быть рассинхронизированы. При сохранении **ApplicationDataCompositeValue** значения сохраняются и загружаются как единое целое или атомарным образом. Таким образом, связанные параметры не будут рассинхронизироваться, так как они перемещаются как единое целое, а не по отдельности.

## <a name="load-app-settings"></a>Загрузка параметров приложения

```csharp
// load a setting that is local to the device
ApplicationDataContainer localSettings = Windows.Storage.ApplicationData.Current.LocalSettings;
String localValue = localSettings.Values["test setting"] as string;

// load a composite setting that roams between devices
ApplicationDataContainer roamingSettings = Windows.Storage.ApplicationData.Current.RoamingSettings;
Windows.Storage.ApplicationDataCompositeValue composite = (ApplicationDataCompositeValue)roamingSettings.Values["RoamingFontInfo"];
if (composite != null)
{
    String fontName = composite["Font"] as string;
    int fontSize = (int)composite["FontSize"];
}
```

Загрузите параметр из локального устройства путем получения экземпляра **ApplicationDataContainer** для локального хранилища данных параметров с `Windows.Storage.ApplicationData.Current.LocalSettings`. Затем используйте его для получения пар "ключ-значение".

Загрузите перемещаемый параметр, используя аналогичную схему. Сначала получите экземпляр **ApplicationDataContainer** из перемещаемого хранилища данных параметров с `Windows.Storage.ApplicationData.Current.RoamingSettings`. Обратитесь к парам "ключ-значение" из этого экземпляра. Если данные пока не переместились на устройство, с которого вы осуществляете доступ к параметрам, значением экземпляра **ApplicationDataContainer** будет NULL. Поэтому в примере кода выше присутствует проверка `if (composite != null)`.

## <a name="useful-apis-and-docs"></a>Полезные интерфейсы API и документы

Вот краткое описание интерфейсов API и другая полезная документация, которая поможет вам сохранять и загружать параметры приложения.

### <a name="useful-apis"></a>Полезные интерфейсы API

| API | Описание |
|------|---------------|
| [ApplicationData.LocalSettings](https://docs.microsoft.com/uwp/api/windows.storage.applicationdata.temporaryfolder) | Получает контейнер параметров приложения из локального хранилища данных приложения. |
| [ApplicationData.RoamingSettings](https://docs.microsoft.com/uwp/api/windows.storage.applicationdata.roamingsettings) | Получает контейнер параметров приложения из перемещаемого хранилища данных приложения. |
| [ApplicationDataContainer](https://docs.microsoft.com/uwp/api/windows.storage.applicationdatacontainer) | Контейнер для параметров приложения, который поддерживает создание, удаление, перечисление и обход иерархии контейнеров. |
| [Windows.UI.ApplicationSettings Namespace](https://docs.microsoft.com/uwp/api/windows.ui.applicationsettings) | Содержит классы, которые вы будете использовать для определения параметров приложения, отображающихся на панели параметров оболочки Windows. |

### <a name="useful-docs"></a>Полезные документы

| Раздел | Описание |
|-------|----------------|
| [Руководство по параметрам приложений](https://docs.microsoft.com/windows/uwp/design/app-settings/guidelines-for-app-settings) | Рекомендации по созданию и отображению параметров приложения. |
| [Хранение и извлечение параметров и прочих данных приложения](https://docs.microsoft.com/windows/uwp/design/app-settings/store-and-retrieve-app-data#create-and-read-a-local-file) | Руководство по сохранению и извлечению параметров, включая перемещаемые параметры. |

## <a name="useful-code-samples"></a>Полезные примеры кода

| Пример кода | Описание |
|-----------------|---------------|
| [Пример данных приложения](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/ApplicationData) | В сценариях 2–4 рассказывается о работе с параметрами. |
