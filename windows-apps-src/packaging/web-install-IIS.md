---
author: laurenhughes
title: Установка приложения UWP с сервера IIS
description: В этом руководстве показано, как настроить сервер IIS, убедитесь, что веб-приложения можно размещать пакеты приложений, вызов и эффективное использование установщика приложений.
ms.author: cdon
ms.date: 05/30/2018
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: Windows 10, uwp, установщик приложений, AppInstaller, загрузка неопубликованных приложений, связанных с установлен, дополнительных пакетов, сервера IIS
ms.localizationpriority: medium
ms.openlocfilehash: 214ddd2b55bca1acecbab0a841cf2048335e7b3a
ms.sourcegitcommit: d10fb9eb5f75f2d10e1c543a177402b50fe4019e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/12/2018
ms.locfileid: "4569400"
---
# <a name="install-a-uwp-app-from-an-iis-server"></a>Установка приложения UWP с сервера IIS

В этом руководстве показано, как настроить сервер IIS, убедитесь, что веб-приложения можно размещать пакеты приложений, вызов и эффективное использование установщика приложений.

Приложение "Установщик приложений" позволяет разработчикам и ИТ-специалистам распространять приложения Windows 10 путем их размещения в собственной сети доставки содержимого (CDN). Это полезно для предприятий, которым не требуется публиковать свои приложения в Microsoft Store, но они все же хотят воспользоваться преимуществами платформы упаковки и развертывания Windows 10. 

## <a name="setup"></a>Настройка

Чтобы успешно выполнить в этом руководстве, потребуется следующее:

1. Visual Studio 2017  
2. Веб-разработки инструменты и службы IIS 
3. Пакет приложения UWP — пакет приложения, который вы будете распространять

Необязательно: [Стартовый проект](https://github.com/AppInstaller/MySampleWebApp) на GitHub. Это полезно в том случае, если вы не обязаны пакетов приложений для работы с, но все равно хотите узнать, как использовать эту функцию.

## <a name="step-1---install-iis-and-aspnet"></a>Шаг 1 - Установка IIS и ASP.NET 

[Службы IIS](https://www.iis.net/) — это компонент Windows, который можно установить через меню "Пуск". В **меню "Пуск"** поиск **Включение или отключение компонентов**.

Найдите и выберите **Службы IIS** для установки служб IIS.

> [!NOTE]
> Установите все флажки в службы IIS не нужно. Только выбранные при проверке **Службы IIS** достаточны.

Также необходимо установить ASP.NET 4.5 или более поздней версии. Чтобы установить ее, найдите **службы IIS "->" Интернета служб -> средства разработки приложений**. Выберите версию ASP.NET, которое больше или равно ASP.NET 4.5.

![Установка ASP.NET](images/install-asp.png)

## <a name="step-2---install-visual-studio-2017-and-web-development-tools"></a>Шаг 2 - установка Visual Studio 2017 г. и средства разработки веб-приложений 

[Установка Visual Studio 2017](https://docs.microsoft.com/visualstudio/install/install-visual-studio) , если вы еще не установлен его. Если у вас уже есть Visual Studio 2017, убедитесь, что установлены нагрузками. Если рабочих нагрузок, не представлены в вашей установке, следуйте инструкциям с помощью установщика Visual Studio (найден из меню "Пуск").  

Во время установки выберите **ASP.NET и веб-разработки** и рабочих нагрузок, которые вас интересуют. 

После завершения установки запустите Visual Studio и создайте новый проект (**файл** -> **Нового проекта**).

## <a name="step-3---build-a-web-app"></a>Шаг 3 - создания веб-приложения

Запустите Visual Studio 2017 от имени **администратора** и создать новый проект **Visual C# веб-приложения** с **пустого** шаблона проекта. 

![Новый проект](images/sample-web-app.png)

## <a name="step-4---configure-iis-with-our-web-app"></a>Шаг 4 - Настройка IIS с помощью наших веб-приложения 

В обозревателе решений щелкните правой кнопкой мыши корневой проект и выберите **Свойства**.

В свойства приложения web выберите вкладку **Web** . В разделе **серверов** выберите **Локальный IIS** из раскрывающегося меню и нажмите кнопку **Создать виртуальный каталог**. 

![Вкладка веб-сайта](images/web-tab.png)

## <a name="step-5---add-an-app-package-to-a-web-application"></a>Шаг 5 – добавить пакет приложения для веб-приложения 

Добавьте пакет приложения, который вы собираетесь распространять в веб-приложения. Можно использовать пакет приложения, который является частью предоставленного [начального проекта пакетов](https://github.com/AppInstaller/MySampleWebApp/tree/master/MySampleWebApp/packages) на GitHub, если у вас нет доступных пакета приложения. Сертификат (MySampleApp.cer), с помощью которого был подписан пакет, также входит в состав примера на GitHub. Должен иметь сертификат, установленных на устройстве перед установкой приложения (шаг 9).

Стартовый проект веб-приложения, создается новая папка был добавлен в веб-приложение под названием `packages` , содержащий пакеты, приложение будет распространяться. Чтобы создать папку в Visual Studio, корневого элемента в обозревателе решений щелкните правой кнопкой мыши, выберите **Добавить** -> **Новую папку** и назовите его `packages`. Добавление пакетов приложений в папку, щелкните правой кнопкой мыши `packages` папки и выберите **Добавить** -> **Существующий элемент …** и перейдите к приложению пакета расположение. 

![Добавление пакета](images/add-package.png)

## <a name="step-6---create-a-web-page"></a>Шаг 6 - создание веб-страницы

Этот образец веб-приложение использует простой HTML-код. Вы, кстати для создания веб-приложения при необходимости для ваших потребностей. 

Корневой проект в обозревателе решений щелкните правой кнопкой мыши, выберите **Добавить** -> **Новый элемент**, и добавьте новую **HTML-странице** в разделе **Web** .

После создания HTML-странице HTML-странице в обозревателе решений щелкните правой кнопкой мыши и выберите **Значение в качестве начальной страницы**.  

Дважды щелкните файл HTML, чтобы открыть его в окне редактора кода. В этом руководстве будут использоваться только для элементов в требуемом веб-страницы для вызова установщик приложений успешно для установки приложений Windows 10. 

Включите следующий код HTML в веб-страницы. Ключ для успешного вызова установщика приложений — использовать настраиваемую схему, установщик приложений регистрируется в ОС: `ms-appinstaller:?source=`. См. в примере кода ниже, для получения дополнительных сведений.

> [!NOTE]
> Убедитесь, что путь URL-адрес, указанный после соответствует настраиваемую схему URL-адрес проекта на вкладке веб-решение VS.
 
```HTML
<html>
<head>
    <meta charset="utf-8" />
    <title> Install Page </title>
</head>
<body>
    <a href="ms-appinstaller:?source=http://localhost/SampleWebApp/packages/MySampleApp.appxbundle"> Install My Sample App</a>
</body>
</html>
```

## <a name="step-7---configure-the-web-app-for-app-package-mime-types"></a>Шаг 7 - Настройка веб-приложения для типов MIME пакета приложения

Откройте файл **Web.config** из обозревателя решений и добавьте следующие строки в `<configuration>` элемента. 

```xml
<system.webServer>
    <!--This is to allow the web server to serve resources with the appropriate file extension-->
    <staticContent>
      <mimeMap fileExtension=".appx" mimeType="application/appx" />
      <mimeMap fileExtension=".msix" mimeType="application/msix" />
      <mimeMap fileExtension=".appxbundle" mimeType="application/appxbundle" />
      <mimeMap fileExtension=".msixbundle" mimeType="application/msixbundle" />
      <mimeMap fileExtension=".appinstaller" mimeType="application/appinstaller" />
    </staticContent>
</system.webServer>
```

## <a name="step-8---add-loopback-exemption-for-app-installer"></a>Шаг 8. Добавление замыкания для установщика приложений

Из-за сетевой изоляции приложений UWP, например установщик ограничены использование IP-адреса замыкания на себя как http://localhost/. При использовании локального сервера IIS, установщик приложений необходимо добавить в список исключение замыкания на себя. 

Для этого откройте **командной строки** с правами **администратора** и введите следующее: '' "CheckNetIsolation.exe LoopbackExempt командной строки --n=microsoft.desktopappinstaller_8wekyb3d8bbwe
```

To verify that the app is added to the exempt list, use the following command to display the apps in the loopback exempt list: 
```Command Line
CheckNetIsolation.exe LoopbackExempt -s
```

Необходимо найти `microsoft.desktopappinstaller_8wekyb3d8bbwe` в списке.

После завершения локальной проверки установки приложения через установщик приложений, вы можете удалить замыкания, который был добавлен на этом этапе по:

''' Командной строки CheckNetIsolation.exe LoopbackExempt -d-n=microsoft.desktopappinstaller_8wekyb3d8bbwe
```

## Step 9 - Run the Web App 

Build and run the web application by clicking on the run button on the VS Ribbon as shown in the image below:

![run](images/run.png)

A web page will open in your browser:

![web page](images/web-page.png)

Click on the link in the web page to launch the App Installer app and install your Windows 10 app package.


## Troubleshooting issues

### Not sufficient privilege 

If running the web app in Visual Studio displays an error such as "You do not have sufficient privilege to access IIS web sites on your machine", you will need to run Visual Studio as an administrator. Close the current instance of Visual Studio and reopen it as an admin.

### Set start page 

If running the web app causes the browser to load with an HTTP 403.14 - Forbidden error, it's because the web app doesn't have a defined start page. Refer to Step 6 in this tutorial to learn how to define a start page.