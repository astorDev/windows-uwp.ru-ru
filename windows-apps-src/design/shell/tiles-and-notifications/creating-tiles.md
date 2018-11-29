---
Description: A tile is an app's representation on the Start menu. Every app has a tile. When you create a new Universal Windows Platform (UWP) app project in Microsoft Visual Studio, it includes a default tile that displays your app's name and logo.
title: Плитки
ms.assetid: 09C7E1B1-F78D-4659-8086-2E428E797653
label: Tiles
template: detail.hbs
ms.date: 05/19/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: e46d73c91f54b1bb74a70990a238f13ccd47645d
ms.sourcegitcommit: 89ff8ff88ef58f4fe6d3b1368fe94f62e59118ad
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/29/2018
ms.locfileid: "8218227"
---
# <a name="tiles-for-uwp-apps"></a>Плитки для приложений UWP

 

*Плитки* используются для представления приложений в меню "Пуск". У каждого приложения имеется плитка. Когда вы создаете новый проект приложения универсальной платформы Windows (UWP) в Microsoft Visual Studio, он включает стандартную плитку, показывающую название и логотип приложения.Windows отображает эту плитку при первой установке приложения. После установки приложения вы можете изменить содержимое плиток с помощью уведомлений, например, вы можете изменить плитку для передачи новой информации пользователю, например, заголовков новостей или темы последнего непрочитанного сообщения.

## <a name="configure-the-default-tile"></a>Настройка плитки по умолчанию


При создании нового проекта в Visual Studio создается простая стандартная плитка, которая отображает имя и логотип приложения.

Чтобы изменить плитку, дважды щелкните файл **Package.appxmanifest** в основном проекте UWP, чтобы открыть конструктор (или щелкните файл правой кнопкой мыши и выберите команду "Просмотреть код").

```XML
  <Applications>
    <Application Id="App"
      Executable="$targetnametoken$.exe"
      EntryPoint="ExampleApp.App">
      <uap:VisualElements
        DisplayName="ExampleApp"
        Square150x150Logo="Assets\Square150x150Logo.png"
        Square44x44Logo="Assets\Square44x44Logo.png"
        Description="ExampleApp"
        BackgroundColor="#464646">
        <uap:SplashScreen Image="Assets\SplashScreen.png" />
      </uap:VisualElements>
    </Application>
  </Applications>
```

Необходимо изменить несколько элементов:

-   DisplayName: замените это значение именем, которое вы хотите отображать на плитке.
-   ShortName: поскольку место для отображаемого имени на плитках ограничено, мы рекомендуем также указать короткое ShortName, чтобы имя приложение не было обрезано.
-   Изображения логотипов:

    Замените эти изображения собственными. Вы можете предоставить изображения для разных визуальных масштабов, но не обязательно для всех. Чтобы обеспечить надлежащий вид приложения на самом широком спектре услуг, мы рекомендуем предоставить версии с масштабом 100%, 200% и 400% каждого изображения. Дополнительные сведения о создании этих ресурсов см. в разделе [ресурсы плиток и значков](app-assets.md).

    Масштабированные изображения следуют этому контексту именования:
    
    *&lt;имя изображения&gt;*.scale-*&lt;коэффициент масштабирования&gt;*.*&lt;расширение имени файла&gt;* 

    Например: SplashScreen.scale-100.png

    Когда вы ссылаетесь на изображение, вы ссылаетесь на него как *&lt;имя изображения&gt;*.*&lt;расширение имени файла&gt;* (в данном примере "SplashScreen.png"). Система автоматически выбирает соответствующее масштабированное изображение для устройства из предоставленных изображений.

-   Вы не обязаны этого делать, но мы настоятельно рекомендуем предоставить эмблемы для широких и больших плиток, чтобы пользователь мог изменить размер плитки до этих размеров. Чтобы предоставить эти дополнительные изображения, необходимо создать элемент **DefaultTile** и использовать атрибуты **Wide310x150Logo** и **Square310x310Logo** для указания дополнительных изображений:
```    XML
  <Applications>
        <Application Id="App"
          Executable="$targetnametoken$.exe"
          EntryPoint="ExampleApp.App">
          <uap:VisualElements
            DisplayName="ExampleApp"
            Square150x150Logo="Assets\Square150x150Logo.png"
            Square44x44Logo="Assets\Square44x44Logo.png"
            Description="ExampleApp"
            BackgroundColor="#464646">
            <uap:DefaultTile
              Wide310x150Logo="Assets\Wide310x150Logo.png"
              Square310x310Logo="Assets\Square310x310Logo.png">
            </uap:DefaultTile>
            <uap:SplashScreen Image="Assets\SplashScreen.png" />
          </uap:VisualElements>
        </Application>
      </Applications>
```

## <a name="use-notifications-to-customize-your-tile"></a>Использование уведомлений для настройки плитки


После установки приложения можно с помощью уведомлений настроить плитку. Вы можете сделать это при первом запуске приложения или в ответ на какое-то событие, например, push-уведомление.

Сведения об отправке уведомлений плиток см. в разделе [Отправка локального уведомления на плитке](sending-a-local-tile-notification.md).
