---
title: Добавление поддержки приложений для веб-сайтов, использующих обработчики URI приложений
description: Повысьте удобство своего приложения для пользователей, добавив поддержку функции "Приложения для веб-сайтов".
keywords: Глубокие связи Windows
ms.date: 08/25/2017
ms.topic: article
ms.assetid: 260cf387-88be-4a3d-93bc-7e4560f90abc
ms.localizationpriority: medium
ms.openlocfilehash: 5807cdc19e4b38c8cc8fa4ca45c4ef47e79b7742
ms.sourcegitcommit: 445320ff0ee7323d823194d4ec9cfa6e710ed85d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2019
ms.locfileid: "72282248"
---
# <a name="enable-apps-for-websites-using-app-uri-handlers"></a>Добавление поддержки приложений для веб-сайтов, использующих обработчики URI приложений

Приложения для веб-сайтов связывают ваше приложение с веб-сайтом, чтобы при переходе по ссылке на ваш веб-сайт вместо открытия браузера запускалось ваше приложение. Если ваше приложение не установлено, ваш сайт откроется в браузере, как обычно. Пользователи могут доверять такому подходу, поскольку только проверенные владельцы содержимого могут зарегистрироваться для предоставления ссылки. Пользователи будут иметь возможность проверить все свои зарегистрированные ссылки в Интернете на приложение, перейдя к элементам "Параметры" > "Приложения" > "Приложения для веб-сайтов".

Для включения привязки приложения к веб-страницам необходимо выполнить следующие требования.
- Определите URI, которые ваше приложение будет обрабатывать, в файле манифеста
- JSON-файл, определяющий связь между вашим приложением и веб-сайтом, с именем семейства пакетов для приложения должен находиться в той же корневой папке, что и объявление манифеста приложения.
- Обработка активации в приложении.

> [!Note]
> Начиная с обновления Windows 10 Creators Update, поддерживаемые ссылки, нажатые в Microsoft ребр, будут запускать соответствующее приложение. Поддерживаемые ссылки, которые были выбраны в других браузерах (например, Internet Explorer и т. д.), будут работать в режиме обзора.

## <a name="register-to-handle-http-and-https-links-in-the-app-manifest"></a>Регистрация для обработки ссылок вида http и https в манифесте приложения

Ваше приложение должно распознавать URI для веб-сайтов, которые оно будет обрабатывать. Для этого добавьте регистрацию расширения **Windows.appUriHandler** в файл манифеста вашего приложения **Package.appxmanifest**.

Например, если адрес вашего сайта — msn.com, следует внести следующую запись в манифест приложения:

```xml
<Applications>
  <Application ... >
      ...
      <Extensions>
         <uap3:Extension Category="windows.appUriHandler">
          <uap3:AppUriHandler>
            <uap3:Host Name="msn.com" />
          </uap3:AppUriHandler>
        </uap3:Extension>
      </Extensions>
  </Application>
</Applications>
```

Приведенное выше объявления регистрирует ваше приложение для обработки ссылок, относящихся к указанному узлу. Если веб-сайт имеет несколько адресов (например, m.example.com, www\.example.com и example.com), добавьте отдельную запись `<uap3:Host Name=... />` в `<uap3:AppUriHandler>` для каждого адреса.

## <a name="associate-your-app-and-website-with-a-json-file"></a>Связывание приложения и веб-сайта в JSON-файле

Для обеспечения возможности открытия расположенного на сайте содержимого вашим приложением добавьте имя семейства пакетов вашего приложения в JSON-файл, расположенный в корневом каталоге вашего веб-сервера или в известном каталоге в домене. Это указывает, что ваш сайт дает согласие на открытие содержимого в перечисленных приложениях. Имя семейства пакетов можно найти в разделе Packages в конструкторе манифеста приложения.

>[!Important]
> JSON-файл не должен иметь расширение .json.

Создайте JSON-файл (без расширения .json) с именем **windows-app-web-link** и укажите имя семейства пакетов вашего приложения. Например:

``` JSON
[{
  "packageFamilyName": "Your app's package family name, e.g MyApp_9jmtgj1pbbz6e",
  "paths": [ "*" ],
  "excludePaths" : [ "/news/*", "/blog/*" ]
 }]
```

Windows установит https-соединение с вашим сайтом и будет искать соответствующий JSON-файл на вашем сервере.

### <a name="wildcards"></a>Подстановочные символы

В приведенном выше примере JSON-файла демонстрируется использование подстановочных символов. Подстановочные символы позволяют поддерживать разнообразные ссылки, используя меньше строк кода. Привязка приложений к Интернету поддерживает два типа подстановочных символов в файле JSON:

| **Использования** | **Описание**               |
|--------------|-------------------------------|
| **\***       | Представляет любую подстроку      |
| **?**        | Представляет единичный символ |

Например, в приведенном выше примере `"excludePaths" : [ "/news/*", "/blog/*" ]` приложение будет поддерживать все пути, которые начинаются с адреса вашего веб-сайта (например, msn.com), **за исключением** тех, которые находятся в разделе `/news/` и `/blog/`. **MSN.com/weather.HTML** будет поддерживаться, но не **MSN.com/News/Topnews.HTML**.

### <a name="multiple-apps"></a>Несколько приложений

Если вы хотите привязать к своему сайту два приложения, укажите оба имени семейства пакетов приложения в вашем JSON-файле **windows-app-web-link**. Возможна поддержка обоих приложений. Пользователю будет предлагаться выбрать способ открытия ссылки по умолчанию, если установлены оба приложения. Если в дальнейшем пользователь захочет изменить способ открытия ссылок по умолчанию, это можно сделать в разделе **Параметры > Приложения для веб-сайтов**. Разработчики также могут изменить файл JSON в любое время, и изменения могут вступить в силу в тот же день, но не позднее чем через 8 дней после обновления.

``` JSON
[{
  "packageFamilyName": "Your apps's package family name, e.g MyApp_9jmtgj1pbbz6e",
  "paths": [ "*" ],
  "excludePaths" : [ "/news/*", "/blog/*" ]
 },
 {
  "packageFamilyName": "Your second app's package family name, for example, MyApp2_8jmtgj2pbbz6e",
  "paths": [ "/example/*", "/links/*" ]
 }]
```

Для обеспечения наилучшего взаимодействия пользователя с приложением используйте пути исключений, чтобы предотвратить обращение к доступному только через Интернет содержимому в поддерживаемых путях в файле JSON.

Пути исключений проверяются в первую очередь, и, если обнаруживается соответствие, эта страница будет открыта в браузере, а не в заданном приложении. В приведенном выше примере "/Невс/\*" включает все страницы по этому пути, а "/Невс\*" (без прямой косой черты "Новости") включает в себя любые пути в разделе "Новости\*", такие как "невслокал/", "невсинтернатионал/" и т. д.

## <a name="handle-links-on-activation-to-link-to-content"></a>Обработка ссылок на активацию для привязки ссылки к содержимому

Перейдите к файлу **App.xaml.cs** в вашем проекте Visual Studio и добавьте обработку связанного содержимого в разделе **OnActivated()** . В следующем примере страница, открываемая в приложении, зависит от URI:

``` CS
protected override void OnActivated(IActivatedEventArgs e)
{
    Frame rootFrame = Window.Current.Content as Frame;
    if (rootFrame == null)
    {
        ...
    }

    // Check ActivationKind, Parse URI, and Navigate user to content
    Type deepLinkPageType = typeof(MainPage);
    if (e.Kind == ActivationKind.Protocol)
    {
        var protocolArgs = (ProtocolActivatedEventArgs)e;        
        switch (protocolArgs.Uri.AbsolutePath)
        {
            case "/":
                break;
            case "/index.html":
                break;
            case "/sports.html":
                deepLinkPageType = typeof(SportsPage);
                break;
            case "/technology.html":
                deepLinkPageType = typeof(TechnologyPage);
                break;
            case "/business.html":
                deepLinkPageType = typeof(BusinessPage);
                break;
            case "/science.html":
                deepLinkPageType = typeof(SciencePage);
                break;
        }
    }

    if (rootFrame.Content == null)
    {
        // Default navigation
        rootFrame.Navigate(deepLinkPageType, e);
    }

    // Ensure the current window is active
    Window.Current.Activate();
}
```

**Важно!** Не забудьте заменить заключительный фрагмент `if (rootFrame.Content == null)` кодом `rootFrame.Navigate(deepLinkPageType, e);`, как показано в примере выше.

## <a name="test-it-out-local-validation-tool"></a>Тестирование: локальное средство проверки

Вы можете тестировать конфигурацию своего приложения и веб-сайта путем запуска средства проверки регистрации хоста приложения, расположенного в папке:

% WINDIR%\\system32\\**апфострегистратионверифиер. exe**

Проверяйте конфигурацию своего приложения и веб-сайта путем запуска этого средства со следующими параметрами:

**Апфострегистратионверифиер. exe** *имя узла паккажефамилинаме FilePath*

-   Имя узла: ваш веб-сайт (например, microsoft.com).
-   Package Family Name: имя семейства пакетов вашего приложения
-   Путь к файлу: JSON-файл для локальной проверки (например, C:\\Сомефолдер\\Windows-App-Web-Link).

Если средство ничего не возвращает, проверки будут работать для этого файла при отправке. Если возникает код ошибки, функция не будет работать.

Можно использовать следующий раздел реестра для принудительной проверки соответствия путей для неопубликованных приложений в рамках локальной проверки.

`HKCU\Software\Classes\LocalSettings\Software\Microsoft\Windows\CurrentVersion\
AppModel\SystemAppData\YourApp\AppUriHandlers`

KeyName: `ForceValidation` значение: `1`

## <a name="test-it-web-validation"></a>Тестирование: проверка в Интернете

Закройте свое приложение, чтобы убедиться, что приложение запускается, когда вы нажимаете на ссылку. Затем скопируйте адрес одного из поддерживаемых путей на вашем веб-сайте. Например, если адрес веб-сайта — "msn.com", а один из путей поддержки — "path1", используйте `http://msn.com/path1`

Убедитесь, что ваше приложение закрыто. Нажмите **клавишу Windows + R**, чтобы открыть диалоговое окно **Выполнить**, и вставьте ссылку в этом окне. Вместо браузера должно запуститься ваше приложение.

Кроме того, можно протестировать приложение, запустив его из другого приложения с помощью API [LaunchUriAsync](https://docs.microsoft.com/uwp/api/windows.system.launcher.launchuriasync). Можно также использовать этот API для тестирования на телефонах.

Если вы хотите отслеживать логику активации протокола, установите точку останова в обработчике событий **OnActivated**.

## <a name="appurihandlers-tips"></a>Советы по AppUriHandlers.

- Следите за тем, чтобы были указаны только те ссылки, которые ваше приложение может обработать.
- Перечисляйте все узлы, которые вы будете поддерживать.  Обратите внимание, что www\.example.com и example.com — разные узлы.
- Пользователи могут выбрать, какое приложение они предпочитают использовать для открытия сайтов, в Параметрах.
- Файл JSON должен быть размещен на https-сервере.
- Если вам нужно изменить пути, которые вы хотите поддерживать, можно повторно опубликовать JSON-файл, не переиздавая приложение. Пользователи увидят изменения через 1-8 дней.
- Все неопубликованные приложения с AppUriHandlers будут иметь проверенные ссылки для данного узла при установке. Для проверки функциональности не требуется передавать JSON-файл на сайт.
- Эта функция работает во всех случаях, когда ваше приложение является приложением UWP и запускается с помощью [LaunchUriAsync](https://docs.microsoft.com/uwp/api/windows.system.launcher.launchuriasync) или является приложением для настольной версии Windows и запускается с помощью [ShellExecuteEx](https://docs.microsoft.com/windows/desktop/api/shellapi/nf-shellapi-shellexecuteexa). Если URL-адрес совпадает с зарегистрированным обработчиком URI приложения, вместо браузера будет запущено приложение.

## <a name="see-also"></a>См. также

[Пример проекта веб-приложения](https://github.com/project-rome/AppUriHandlers/tree/master/NarwhalFacts)
[Регистрация windows.protocol](https://docs.microsoft.com/uwp/schemas/appxpackage/appxmanifestschema/element-protocol)
[Активация обработки URI](https://docs.microsoft.com/windows/uwp/launch-resume/handle-uri-activation)
[Пример привязки запуска](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/AssociationLaunching) показывает, как использовать API LaunchUriAsync().
