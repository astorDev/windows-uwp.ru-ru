---
author: mijacobs
Description: Плитки используются для представления приложений в меню "Пуск". У каждого приложения имеется плитка. Когда вы создаете новый проект приложения универсальной платформы Windows (UWP) в Microsoft Visual Studio, он включает стандартную плитку, показывающую название и логотип приложения.
title: Плитки
ms.assetid: 09C7E1B1-F78D-4659-8086-2E428E797653
label: Tiles
template: detail.hbs
---

# Плитки для приложений UWP





*Плитки* используются для представления приложений в меню "Пуск". У каждого приложения имеется плитка. Когда вы создаете новый проект приложения универсальной платформы Windows (UWP) в Microsoft Visual Studio, он включает стандартную плитку, показывающую название и логотип приложения. Windows отображает эту плитку при первой установке приложения. После установки приложения вы можете изменить содержимое плиток с помощью уведомлений, например, вы можете изменить плитку для передачи новой информации пользователю, например, заголовков новостей или темы последнего непрочитанного сообщения.

## <span id="Configure_the_default_tile"></span><span id="configure_the_default_tile"></span><span id="CONFIGURE_THE_DEFAULT_TILE"></span>Настройка плитки по умолчанию


При создании нового проекта в Visual Studio создается простая стандартная плитка, которая отображает имя и логотип приложения.

```XML
  <Applications>
    <Application Id="App"
      Executable="$targetnametoken$.exe"
      EntryPoint="ExampleApp.App">
      <uap:VisualElements
        DisplayName="ExampleApp"
        Square150x150Logo="Assets\Logo.png"
        Square44x44Logo="Assets\SmallLogo.png"
        Description="ExampleApp"
        BackgroundColor="#464646">
        <uap:SplashScreen Image="Assets\SplashScreen.png" />
      </uap:VisualElements>
    </Application>
  </Applications>
```

Немногие элементы необходимо обновить.

-   DisplayName: замените это значение именем, которое вы хотите отображать на плитке.
-   ShortName: поскольку место для отображаемого имени на плитках ограничено, мы рекомендуем также указать короткое ShortName, чтобы имя приложение не было обрезано.
-   Изображения логотипов:

    Замените эти изображения собственными. Вы можете предоставить изображения для разных визуальных масштабов, но не обязательно для всех. Чтобы обеспечить надлежащий вид приложения на самом широком спектре услуг, мы рекомендуем предоставить версии с масштабом 100%, 200% и 400% каждого изображения.

    Масштабированные изображения следуют этому контексту именования:
    
    *
              &lt;имя изображения&gt;*.scale-*&lt;коэффициент масштабирования&gt;*.*&lt;расширение имени файла&gt;* 


     

    Например: SmallLogo.scale-100.png

    Когда вы ссылаетесь на изображение, вы ссылаетесь на него как *&lt;имя изображения&gt;*.*&lt;расширение имени файла&gt;* (в данном примере "SmallLogo.png"). Система автоматически выбирает соответствующее масштабированное изображение для устройства из предоставленных изображений.

-   Вы не обязаны этого делать, но мы настоятельно рекомендуем предоставить эмблемы для широких и больших плиток, чтобы пользователь мог изменить размер плитки до этих размеров. Чтобы предоставить эти дополнительные изображения, необходимо создать элемент `DefaultTile` и использовать атрибуты `Wide310x150Logo` и `Square310x310Logo` для указания дополнительных изображений:
```    XML
  <Applications>
        <Application Id="App"
          Executable="$targetnametoken$.exe"
          EntryPoint="ExampleApp.App">
          <uap:VisualElements
            DisplayName="ExampleApp"
            Square150x150Logo="Assets\Logo.png"
            Square44x44Logo="Assets\SmallLogo.png"
            Description="ExampleApp"
            BackgroundColor="#464646">
            <uap:DefaultTile
              Wide310x150Logo="Assets\WideLogo.png"
              Square310x310Logo="Assets\LargeLogo.png">
            </uap:DefaultTile>
            <uap:SplashScreen Image="Assets\SplashScreen.png" />
          </uap:VisualElements>
        </Application>
      </Applications>
```

## <span id="Use_notifications_to_customize_your_tile"></span><span id="use_notifications_to_customize_your_tile"></span><span id="USE_NOTIFICATIONS_TO_CUSTOMIZE_YOUR_TILE"></span>Использование уведомлений для настройки плитки


После установки приложения можно с помощью уведомлений настроить плитку. Вы можете сделать это при первом запуске приложения или в ответ на какое-то событие, например, push-уведомление.

1.  Создайте полезные данные XML (в форме [**Windows.Data.Xml.Dom.XmlDocument**](https://msdn.microsoft.com/library/windows/apps/br206173)) с описанием плитки.

    -   Windows 10 представляет новую адаптивную схему плиток, которую вы можете использовать. Инструкции см. в разделе [Адаптивные плитки](tiles-and-notifications-create-adaptive-tiles.md). Подробнее о схеме см. в статье [Схема адаптивных плиток](tiles-and-notifications-adaptive-tiles-schema.md). 

    -   Вы можете использовать шаблоны плиток Windows 8.1 для определения плитки. Подробнее см. в разделе [Создание плиток и индикаторов событий (Windows 8.1)](https://msdn.microsoft.com/library/windows/apps/xaml/hh868260).

2.  Создайте объект уведомления плитки и передайте его созданному [**XmlDocument**](https://msdn.microsoft.com/library/windows/apps/br206173). Существует несколько типов объектов уведомлений:
    -   Объект [**Windows.UI.NotificationsTileNotification**](https://msdn.microsoft.com/library/windows/apps/br208616) для немедленного обновления плитки.
    -   Объект [**Windows.UI.Notifications.ScheduledTileNotification**](https://msdn.microsoft.com/library/windows/apps/hh701637) для передачи плитки в определенный момент в будущем.

3.  Создайте объект [**TileUpdater**](https://msdn.microsoft.com/library/windows/apps/br208628) с помощью массива [**Windows.UI.Notifications.TileUpdateManager.CreateTileUpdaterForApplication**](https://msdn.microsoft.com/library/windows/apps/br208623).
4.  Вызовите метод [**TileUpdater.Update**](https://msdn.microsoft.com/library/windows/apps/br208632) и передайте его объекту уведомления плитки, созданному в шаге 2.

 

 






<!--HONumber=May16_HO2-->


