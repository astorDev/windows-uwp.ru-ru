---
author: mcleanbyron
description: "Узнайте, как обновить ваше приложение для использования последних поддерживаемых версий библиотек Microsoft Advertising, чтобы приложение продолжало получать рекламные баннеры."
title: "Обновление приложения для использования последних библиотек Microsoft Advertising"
ms.author: mcleans
ms.date: 02/08/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: "Windows 10, UWP, рекламные объявления, реклама, AdControl, AdMediatorControl, переход"
ms.assetid: f8d5b2ad-fcdb-4891-bd68-39eeabdf799c
ms.openlocfilehash: 25435ebf314327db7288ac853819c90ebba35669
ms.sourcegitcommit: 909d859a0f11981a8d1beac0da35f779786a6889
translationtype: HT
---
# <a name="update-your-app-to-the-latest-advertising-libraries"></a>Обновление приложения для использования последних библиотек Microsoft Advertising

Приложения, отображающие рекламные баннеры Microsoft advertising, должны использовать объекты **AdControl** или **AdMediatorControl** из одного из следующих пакетов SDK, чтобы получать рекламные баннеры после 1 апреля 2017 г.:

  * [Microsoft Store Services SDK](http://aka.ms/store-services-sdk) (для приложений UWP),
  * [Microsoft Advertising SDK для Windows и WindowsPhone8.x](http://aka.ms/store-8-sdk) (для приложений Windows8.1 и WindowsPhone8.x).

До этих SDK данные элементы управления были представлены в нескольких более старых выпусках пакетов SDK для показа рекламы в приложениях для Windows и Windows Phone. Эти устаревшие выпуски больше не поддерживаются. После 1 апреля 2017 г. корпорация Майкрософт может остановить обслуживание рекламных баннеров для приложений, использующих устаревшие выпуски пакетов SDK для показа рекламы, в любое время без дополнительного предупреждения.

Если у вас есть приложение (уже находящееся в Магазине или на стадии разработки), отображающее рекламные баннеры с помощью **AdControl** или **AdMediatorControl**, следуйте инструкциям в этой статье, чтобы определить, повлияет ли на него это изменение, и узнать, как его обновить, если возникнет такая необходимость.

>**Примечание**&nbsp;&nbsp;Кроме того, после 1 апреля 2017 г. мы также остановим обслуживание рекламных баннеров для любых групп объявлений, которые используются в более чем одном приложении. Чтобы подготовиться к этому изменению, убедитесь, что каждая группа объявлений используется только в одном приложении.

## <a name="more-details-about-this-change"></a>Дополнительные сведения об этом изменении

Некоторый дополнительный контекст об этом изменении: мы прекращаем поддержку старых выпусков рекламного SDK, которые не поддерживают минимальный набор возможностей, включая возможность показывать мультимедиа HTML5 по [спецификации Mobile Rich-media Ad Interface Definitions (MRAID) 1.0](http://www.iab.com/wp-content/uploads/2015/08/IAB_MRAID_VersionOne.pdf) Бюро интерактивной рекламы (IAB). Эти возможности интересуют многих из наших рекламодателей, поэтому это изменение поможет сделать нашу экосистему приложений более привлекательной для рекламодателей и в конечном итоге повысить ваши доходы.

Если это изменение затрагивает ваше приложение и вы не обновите его для использования последнего рекламного SDK для вашей целевой платформы, вы будете наблюдать следующее поведение, когда мы прекратим обслуживать рекламные баннеры в приложениях с неподдерживаемыми выпусками рекламного SDK:

* В элементах управления **AdControl** или **AdMediatorControl** в вашем приложении больше не будут отображаться рекламные баннеры, и вы не будете получать доход от рекламы от этих элементов управления.

* Когда элемент управления **AdControl** или **AdMediatorControl** в вашем приложении запрашивает новую рекламу, создается событие **ErrorOccurred** элемента управление, и свойство **ErrorCode** аргументов события будет иметь значение **NoAdAvailable**.

Если у вас возникли проблемы или вам нужна помощь, [обратитесь в службу поддержки](http://go.microsoft.com/fwlink/?LinkId=393643).

>**Примечание**&nbsp;&nbsp;Если в вашем приложении уже используется [Microsoft Store Services SDK](http://aka.ms/store-services-sdk) (для приложений UWP) или [Microsoft Advertising SDK для Windows и Windows Phone 8.x](http://aka.ms/store-8-sdk) (для приложений Windows 8.1 и Windows Phone 8.x) или вы ранее обновили свое приложение для использования одного из этих SDK, в вашем приложении уже используется последний доступный рекламный SDK, и никакие дополнительные изменения не требуются.

## <a name="prerequisites"></a>Необходимые условия

* Полный исходный код и файлы проекта Visual Studio для приложения, в котором используются элементы управления **AdControl** или **AdMediatorControl**.

* Пакет AppX или XAP для приложения.

  >**Примечание**&nbsp;&nbsp;Если у вас больше нет пакета AppX или XAP для вашего приложения, однако есть компьютер разработки с версией Visual Studio и рекламным SDK, который использовался для создания приложения, вы можете заново создать пакет AppX или XAP в Visual Studio.

<span id="part-1" />
## <a name="part-1-determine-whether-you-need-to-update-your-app"></a>Часть 1. Определите, нужно ли вам обновлять приложение

Следуйте инструкциям в следующих разделах, чтобы определить, нужно ли вам обновлять ваше приложение.

### <a name="your-app-uses-adcontrol"></a>В вашем приложении используется AdControl

Если в вашем приложении для показа рекламных баннеров используется элемент управления **AdControl**, выполните следующие действия.

**Приложения UWP для Windows 10**

1. Создайте копию пакета AppX для своего приложения, чтобы не повредить исходный пакет, переименуйте копию так, чтобы у нее было расширение ZIP, и извлеките содержимое файла.

2. Проверьте извлеченное содержимое пакета приложения:

  * Если вы видите файл Microsoft.Advertising.dll, в вашем приложении используется старый SDK, и вам необходимо обновить проект, следуя инструкциям в разделах ниже. Переходите к [части 2](update-your-app-to-the-latest-advertising-libraries.md#part-2).

  * Если вы не видите файла Microsoft.Advertising.dll, в вашем приложении UWP уже используется последний доступный рекламный SDK, и вносить изменения в проект не нужно.

<span/>

**Приложения для Windows 8.1 и Windows Phone 8.x**

1. Создайте копию пакета AppX или XAP для своего приложения, чтобы не повредить исходный пакет, переименуйте копию так, чтобы у нее было расширение ZIP, и извлеките содержимое файла.

2. В случае приложений XAML или JavaScript/HTML проверьте извлеченное содержимое пакета приложения:

  * Если вы видите файл Microsoft.Advertising.winmd, но не видите файла UniversalXamlAdControl.\*.dll (для приложений XAML) или файла UniversalSharedLibrary.Windows.dll (для приложений JavaScript/HTML), в вашем приложении используется старый SDK, и вам необходимо обновить проект, следуя инструкциям в разделах ниже. Переходите к [части 2](update-your-app-to-the-latest-advertising-libraries.md#part-2).

  * В противном случае выполните следующий шаг.

2. Откройте Windows PowerShell, введите следующую команду, указав в качестве аргумента ```-Path``` полный путь к извлеченному содержимому пакета приложения. Эта команда выводит все рекламные библиотеки, на которые ссылается ваш проект, и версию каждой библиотеки.

  > [!div class="tabbedCodeSnippets"]
  ```syntax
  get-childitem -Path "<path to your extracted package>" * -Recurse -include *advert*.dll,*admediator*.dll,*xamladcontrol*.dll,*universalsharedlibrary*.dll | where-object {$_.Name -notlike "*resources*" -and $_.Name -notlike "*design*" } | foreach-object { "{0}`t{1}" -f $_.FullName, [System.Diagnostics.FileVersionInfo]::GetVersionInfo($_).FileVersion }
  ```

2. Найдите файл, указанный в следующей таблице для целевой платформы вашего приложения, и сравните версии этого файла с версией, указанной в таблице.

  <table>
    <colgroup>
      <col width="33%" />
      <col width="33%" />
      <col width="33%" />
    </colgroup>
    <thead>
      <tr class="header">
        <th align="left">Целевая платформа</th>
        <th align="left">Файлы</th>
        <th align="left">Версия</th>
      </tr>
    </thead>
    <tbody>
      <tr class="odd">
        <td align="left"><p>Windows 8.1 XAML</p></td>
        <td align="left"><p>UniversalXamlAdControl.Windows.dll</p></td>
        <td align="left"><p>8.5.1601.07018</p></td>
      </tr>
      <tr class="odd">
        <td align="left"><p>Windows Phone 8.1 XAML</p></td>
        <td align="left"><p>UniversalXamlAdControl.WindowsPhone.dll</p></td>
        <td align="left"><p>8.5.1601.07018</p></td>
      </tr>
      <tr class="odd">
        <td align="left"><p>Windows 8.1 JavaScript/HTML<br/>Windows Phone 8.1 JavaScript/HTML</p></td>
        <td align="left"><p>UniversalSharedLibrary.Windows.dll</p></td>
        <td align="left"><p>8.5.1601.07018</p></td>
      </tr>
      <tr class="odd">
        <td align="left"><p>Windows Phone 8.1 Silverlight</p></td>
        <td align="left"><p>Microsoft.Advertising.\*.dll</p></td>
        <td align="left"><p>8.1.50112.0</p></td>
      </tr>
      <tr class="odd">
        <td align="left"><p>Windows Phone 8.0 Silverlight</p></td>
        <td align="left"><p>Microsoft.Advertising.\*.dll</p></td>
        <td align="left"><p>6.2.40501.0</p></td>
      </tr>
    </tbody>
  </table>

3. Если файл имеет версию, указанную в таблице выше или более позднюю, вносить изменения в проект не нужно.

  Если файл более старой версии, вам необходимо обновить проект, следуя инструкциям в разделах ниже. Переходите к [части 2](update-your-app-to-the-latest-advertising-libraries.md#part-2).

<span/>

**Приложения для Windows 8.0**

* В приложениях, предназначенных для Windows 8.0, показ рекламных баннеров в будущем осуществляться не будет. Во избежание потери показов рекомендуем преобразовать проект в приложение UWP, предназначенное для Windows 10. Большая часть трафика приложений для Windows 8.0 теперь происходит на устройствах с Windows 10.

<span/>

**Приложения для Windows Phone 7.x**

* В приложениях, предназначенных для Windows Phone 7.x, показ рекламных баннеров в будущем осуществляться не будет. Во избежание потери показов рекомендуем преобразовать проект в приложение, предназначенное для Windows Phone 8.1, или в приложение UWP, предназначенное для Windows 10. Большая часть трафика приложений для Windows 7.x теперь происходит на устройствах с Windows Phone 8.1 или Windows 10.

<span/>

### <a name="your-app-uses-admediatorcontrol"></a>В вашем приложении используется AdMediatorControl

Если в вашем приложении для показа рекламных баннеров используется элемент управления **AdMediatorControl**, выполните следующие действия, чтобы определить, нужно ли его обновлять.

**Приложения UWP для Windows 10**

* Элемент управления **AdMediatorControl** для приложений UWP больше не поддерживается. Необходимо перейти на использование элемента управления **AdControl**, следуя инструкциям в разделах ниже. Переходите к [части 2](update-your-app-to-the-latest-advertising-libraries.md#part-2).

<span/>

**Приложения для Windows 8.1 или Windows Phone 8.1**

1. Создайте копию пакета AppX или XAP для своего приложения, чтобы не повредить исходный пакет, переименуйте копию так, чтобы у нее было расширение ZIP, и извлеките содержимое файла.

2. Откройте Windows PowerShell, введите следующую команду, указав в качестве аргумента ```-Path``` полный путь к извлеченному содержимому пакета приложения. Эта команда выводит все рекламные библиотеки, на которые ссылается ваш проект, и версию каждой библиотеки.

  > [!div class="tabbedCodeSnippets"]
  ```syntax
  get-childitem -Path "<path to your extracted package>" * -Recurse -include *advert*.dll,*admediator*.dll,*xamladcontrol*.dll,*universalsharedlibrary*.dll | where-object {$_.Name -notlike "*resources*" -and $_.Name -notlike "*design*" } | foreach-object { "{0}`t{1}" -f $_.FullName, [System.Diagnostics.FileVersionInfo]::GetVersionInfo($_).FileVersion }
  ```

2. Если файлы Microsoft.AdMediator.\*.dll в выходных данных команды имеют версию 2.0.1603.18005 или позднее, вносить изменения в проект не нужно.

  Если эти файлы более старой версии, вам необходимо обновить проект, следуя инструкциям в разделах ниже. Переходите к [части 2](update-your-app-to-the-latest-advertising-libraries.md#part-2).

<span id="part-2" />
## <a name="part-2-install-the-latest-sdk"></a>Часть 2. Установите последнюю версию SDK

Если в вашем приложении используется старый выпуск SDK, выполните следующие действия, чтобы установить на свой компьютер разработки последнюю версию SDK.

1. Убедитесь, что на вашем компьютере разработки есть Visual Studio 2015 (для проектов UWP, Windows 8.1 или Windows Phone 8.x) или Visual Studio 2013 (для проектов Windows 8.1 или Windows Phone 8.x).

  >**Примечание**&nbsp;&nbsp;Если система Visual Studio на компьютере разработки открыта, закройте ее, прежде чем выполнять следующие действия.

1.    Удалите все предыдущие версии Microsoft Advertising SDK и Ad Mediator SDK с компьютера разработки.

2.    Откройте окно **Командная строка** и выполните следующие команды, чтобы удалить все версии рекламных SDK, которые могли быть установлены вместе с Visual Studio, но, возможно, не отображаются в списке установленных программ на компьютере:

  > [!div class="tabbedCodeSnippets"]
  ```syntax
  MsiExec.exe /x{5C87A4DB-31C7-465E-9356-71B485B69EC8}
  MsiExec.exe /x{6AB13C21-C3EC-46E1-8009-6FD5EBEE515B}
  MsiExec.exe /x{6AC81125-8485-463D-9352-3F35A2508C11}
  ```

3.    Установите последнюю версию SDK для вашего приложения:
  * Для приложений UWP в Windows 10 установите [Microsoft Store Services SDK](http://aka.ms/store-services-sdk).
  * Для приложений, предназначенных для более ранней версии операционной системы, установите [Microsoft Advertising SDK для Windows и Windows Phone 8.x](http://aka.ms/store-8-sdk).

## <a name="part-3-update-your-project"></a>Часть 3. Обновите проект

Выполните следующие действия, чтобы обновить свой проект.

### <a name="uwp-projects-for-windows-10"></a>Проекты UWP для Windows 10

<span/>

Если в вашем приложении используется элемент управления **AdMediatorControl**, выполните его [рефакторинг для перехода на элемент управления AdControl](migrate-from-admediatorcontrol-to-adcontrol.md). Элемент управления **AdMediatorControl** для приложений UWP больше не поддерживается.

Если в вашем приложении используется элемент управления **AdControl**, удалите из проекта все существующие ссылки на библиотеки Microsoft Advertising и следуйте инструкциям в статье [AdControl в XAML](adcontrol-in-xaml-and--net.md) или [AdControl в HTML](adcontrol-in-html-5-and-javascript.md), чтобы добавить необходимые ссылки. Это позволит гарантировать, что в вашем проекте используются правильные библиотеки. Вы можете сохранить существующую разметку XAML и код.

<span/>

### <a name="windows-81-or-windows-phone-81-xaml-or-javascripthtml-projects"></a>Проекты для Windows 8.1 или Windows Phone 8.1 (XAML или JavaScript/HTML)

<span/>

1. Удалите из своего проекта все ссылки на Microsoft.Advertising.\* и Microsoft.AdMediator.\*. У вас может быть две ссылки (одна для Windows и одна для Windows Phone), если вы использовали универсальный шаблон проекта.

2. Если в вашем приложении используется элемент управления **AdMediatorControl**, добавьте обратно ссылки на библиотеки, следуя инструкциям в статье [Добавление и использование элемента управления рекламного посредника](https://msdn.microsoft.com/library/windows/apps/xaml/dn864355.aspx). Если в вашем приложении используется элемент управления **AdControl**, добавьте обратно ссылки на библиотеки, следуя инструкциям в статье [AdControl в XAML](adcontrol-in-xaml-and--net.md) или [AdControl в HTML](adcontrol-in-html-5-and-javascript.md).

<span/>

Примите к сведению следующее.

* Если ваше приложение раньше было скомпилировано для платформы **Любой ЦП**, необходимо перекомпилировать проект для платформы определенной архитектуры (x86, x64 или ARM).

* Если у вас приложение XAML для Windows Phone 8.x, в котором раньше использовалась версия SDK с классом **AdControl**, определенным в пространстве имен **Microsoft.Advertising.Mobile.UI**, необходимо обновить код так, чтобы он ссылался на класс **AdControl** в пространстве имен **Microsoft.Advertising.WinRT.UI** (в более новых выпусках SDK этот класс перенесен в другое пространство имен).

* В остальном вы можете сохранить существующую разметку XAML и код.

<span/>

### <a name="windows-phone-8x-silverlight-projects"></a>Проекты Silverlight для Windows Phone 8.x

<span/>

1. Удалите из своего проекта все ссылки на Microsoft.Advertising.\* и Microsoft.AdMediator.\*.

2. Если в вашем приложении используется элемент управления **AdMediatorControl**, добавьте обратно ссылки на библиотеки, следуя инструкциям в статье [Добавление и использование элемента управления рекламного посредника](https://msdn.microsoft.com/library/windows/apps/xaml/dn864355.aspx). Если в вашем приложении используется элемент управления **AdControl**, добавьте обратно ссылки на библиотеки, следуя инструкциям в статье [AdControl в Windows Phone Silverlight](adcontrol-in-windows-phone-silverlight.md).

<span/>

Примите к сведению следующее.

* Вы можете сохранить существующую разметку XAML и код.

* В **Обозревателе решений** проверьте свойства ссылки на **Microsoft.Advertising.Mobile.UI** в своем проекте. Она должна быть версии 6.2.40501.0, если ваше приложение предназначено для Windows Phone 8.0, или 8.1.50112.0, если ваше приложение предназначено для Windows Phone 8.1.

* Для приложений Silverlight для Windows Phone 8.x не поддерживается тестирование рабочих единиц на эмуляторе. Рекомендуем тестировать приложение на устройстве.

## <a name="part-4-test-and-republish-your-app"></a>Часть 4. Протестируйте свое приложение и заново опубликуйте его

Протестируйте свое приложение, чтобы убедиться, что рекламные баннеры отображаются должным образом.

Если предыдущая версия вашего приложения уже доступна в Магазине, [создайте новую отправку](../publish/app-submissions.md) для обновленного приложения в информационной панели Центра разработки для Windows, чтобы опубликовать приложение заново.





 
