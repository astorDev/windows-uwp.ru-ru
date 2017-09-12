---
author: TylerMSFT
title: "Запуск приложения &quot;Параметры&quot; для Windows"
description: "Узнайте, как запустить приложение &quot;Параметры&quot; для Windows из вашего приложения. В этом разделе приводится описание схемы URI ms-settings. Используйте эту схему URI для запуска приложения &quot;Параметры&quot; для Windows на определенных страницах параметров."
ms.assetid: C84D4BEE-1FEE-4648-AD7D-8321EAC70290
ms.author: twhitney
ms.date: 06/12/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, uwp
ms.openlocfilehash: 2a30e14f7c275c48f52253157fc9c67bf05d259e
ms.sourcegitcommit: 00c3f5a1208bd0125f5b275f972cf2a82d8eb9b6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2017
---
# <a name="launch-the-windows-settings-app"></a>Запуск приложения «Параметры» для Windows

\[ Обновлено для приложений UWP в Windows10. Статьи о Windows 8.x см. в [архиве](http://go.microsoft.com/fwlink/p/?linkid=619132) \]

**Важные API**

-   [**LaunchUriAsync**](https://msdn.microsoft.com/library/windows/apps/hh701476)
-   [**PreferredApplicationPackageFamilyName**](https://msdn.microsoft.com/library/windows/apps/hh965482)
-   [**DesiredRemainingView**](https://msdn.microsoft.com/library/windows/apps/dn298314)

Узнайте, как запустить приложение «Параметры» для Windows из вашего приложения. В этом разделе приводится описание схемы URI **ms-settings:**. Используйте эту схему URI для запуска приложения «Параметры» для Windows на определенных страницах параметров.

Запуск приложения «Параметры» — важная составляющая создания приложения, учитывающего требования конфиденциальности. Если ваше приложение не может получить доступ к конфиденциальному ресурсу, рекомендуется предоставить пользователю удобную ссылку на параметры конфиденциальности для этого ресурса. Дополнительные сведения см. в статье [Руководство по приложениям, учитывающим конфиденциальность](https://msdn.microsoft.com/library/windows/apps/hh768223).

## <a name="how-to-launch-the-settings-app"></a>Запуск приложения «Параметры»

Для запуска приложения **Параметры** используйте схему URI `ms-settings:`, как показано в следующих примерах.

В этом примере элемент управления XAML с гиперссылкой используется для запуска страницы параметров конфиденциальности для микрофона с помощью URI `ms-settings:privacy-microphone`.

```xml
<!--Set Visibility to Visible when access to the microphone is denied -->  
<TextBlock x:Name="LocationDisabledMessage" FontStyle="Italic"
                 Visibility="Collapsed" Margin="0,15,0,0" TextWrapping="Wrap" >
          <Run Text="This app is not able to access the microphone. Go to " />
              <Hyperlink NavigateUri="ms-settings:privacy-microphone">
                  <Run Text="Settings" />
              </Hyperlink>
          <Run Text=" to check the microphone privacy settings."/>
</TextBlock>
```

Либо ваше приложение может вызвать метод [**LaunchUriAsync**](https://msdn.microsoft.com/library/windows/apps/hh701476), чтобы запустить приложение **Параметры** из кода. В этом примере показано, как запустить страницу параметров конфиденциальности для камеры с помощью URI `ms-settings:privacy-webcam`.

```cs
bool result = await Windows.System.Launcher.LaunchUriAsync(new Uri("ms-settings:privacy-webcam"));
```

Приведенный выше код запускает страницу параметров конфиденциальности для камеры:

![Параметры конфиденциальности камеры.](images/privacyawarenesssettingsapp.png)

Дополнительные сведения о запуске URI см. в разделе [Запуск приложения по умолчанию для URI](launch-default-app.md).

## <a name="ms-settings-uri-scheme-reference"></a>Справка по схеме URI ms-settings:

Используйте следующие URI для открытия разных страниц приложения «Параметры».

> Обратите внимание, что доступность страницы параметров зависит от Windows SKU. Не все страницы параметров, доступные в Windows 10 для настольных систем, доступны в Windows 10 Mobile и наоборот. В столбце "Примечания" также представлены дополнительные требования, которые должны быть выполнены, чтобы страница была доступна.

<table border="1">
 <tr>
  <th>Категория</th>
  <th>Страница параметров</th>
  <th>URI</th>
  <th>Примечания</th>
 </tr>
 <tr>
  <td rowspan="6">Учетные записи</td>
  <td>Доступ на рабочем месте или в учебном учреждении</td>
  <td>ms-settings:workplace</td>
  <td></td>
 </tr>
 <tr>
  <td>Учетные записи электронной почты и приложений</td>
  <td>ms-settings:emailandaccounts</td>
  <td></td>
 </tr>
 <tr>
  <td>Семья и другие пользователи</td>
  <td>ms-settings:otherusers</td>
  <td></td>
 </tr>
 <tr>
  <td>Параметры входа</td>
  <td>ms-settings:signinoptions</td>
  <td></td>
 </tr>
 <tr>
  <td>Синхронизация параметров</td>
  <td>ms-settings:sync</td>
  <td></td>
 </tr>
 <tr>
  <td>Сведения</td>
  <td>ms-settings:yourinfo</td>
  <td></td>
 </tr>
 <tr>
  <td rowspan="4">Приложения</td>
  <td>Приложения и компоненты</td>
  <td>ms-settings:appsfeatures</td>
  <td></td>
 </tr>
 <tr>
  <td>Приложения для веб-сайтов</td>
  <td>ms-settings:appsforwebsites</td>
  <td></td>
 </tr>
 <tr>
  <td>Приложения по умолчанию</td>
  <td>ms-settings:defaultapps</td>
  <td></td>
 </tr>
 <tr>
  <td>Приложения и компоненты</td>
  <td>ms-settings:optionalfeatures</td>
  <td></td>
 </tr>
 <tr>
  <td rowspan="12">Устройства</td>
  <td>USB</td>
  <td>ms-settings:usb</td>
  <td></td>
 </tr>
 <tr>
  <td>Звук и голосовые функции</td>
  <td>ms-settings:holographic-audio</td>
  <td>Доступно, только если установлено приложение портала смешанной реальности (доступно в Магазине Windows)</td>
 </tr>
 <tr>
  <td>Автозапуск</td>
  <td>ms-settings:autoplay</td>
  <td></td>
 </tr>
 <tr>
  <td>Сенсорная панель</td>
  <td>ms-settings:devices-touchpad</td>
  <td>Доступно только при наличии оборудования сенсорной панели</td>
 </tr>
 <tr>
  <td>Перо и Windows Ink</td>
  <td>ms-settings:pen</td>
  <td></td>
 </tr>
 <tr>
  <td>Принтеры и сканеры</td>
  <td>ms-settings:printers</td>
  <td></td>
 </tr>
 <tr>
  <td>Ввод с клавиатуры</td>
  <td>ms-settings:typing</td>
  <td></td>
 </tr>
 <tr>
  <td>Колесо</td>
  <td>ms-settings:wheel</td>
  <td>Доступны, только если устройство Dial связано</td>
 </tr>
 <tr>
  <td>Камера по умолчанию</td>
  <td>ms-settings:camera</td>
  <td></td>
 </tr>
 <tr>
  <td>Bluetooth</td>
  <td>ms-settings:bluetooth</td>
  <td></td>
 </tr>
 <tr>
  <td>Подключенные устройства</td>
  <td>ms-settings:connecteddevices</td>
  <td></td>
 </tr>
 <tr>
  <td>Мышь и сенсорная панель</td>
  <td>ms-settings:mousetouchpad</td>
  <td>Параметры сенсорной панели доступны только на устройствах с сенсорной панелью</td>
 </tr>
 <tr>
  <td rowspan="7">Специальные возможности</td>
  <td>Экранный диктор</td>
  <td>ms-settings:easeofaccess-narrator</td>
  <td></td>
 </tr>
 <tr>
  <td>Экранная лупа</td>
  <td>ms-settings:easeofaccess-magnifier</td>
  <td></td>
 </tr>
 <tr>
  <td>Высокая контрастность</td>
  <td>ms-settings:easeofaccess-highcontrast</td>
  <td></td>
 </tr>
 <tr>
  <td>Скрытые субтитры</td>
  <td>ms-settings:easeofaccess-closedcaptioning</td>
  <td></td>
 </tr>
 <tr>
  <td>Клавиатура</td>
  <td>ms-settings:easeofaccess-keyboard</td>
  <td></td>
 </tr>
 <tr>
  <td>Мышь</td>
  <td>ms-settings:easeofaccess-mouse</td>
  <td></td>
 </tr>
 <tr>
  <td>Другие параметры</td>
  <td>ms-settings:easeofaccess-otheroptions</td>
 </tr>
 <tr>
  <td>Дополнительно</td>
  <td>Дополнительно</td>
  <td>ms-settings:extras</td>
  <td>Доступно только, если установлены "приложения параметров" (например, сторонние)</td>
 </tr>
 <tr>
  <td rowspan="4">Игры</td>
  <td>Трансляции</td>
  <td>ms-settings:gaming-broadcasting</td>
  <td></td>
 </tr>
 <tr>
  <td>Меню игры</td>
  <td>ms-settings:gaming-gamebar</td>
  <td></td>
 </tr>
 <tr>
  <td>DVR для игр</td>
  <td>ms-settings:gaming-gamedvr</td>
  <td></td>
 </tr>
 <tr>
  <td>Режим игры</td>
  <td>ms-settings:gaming-gamemode</td>
  <td></td>
 </tr>
 <tr>
  <td>Домашняя страница</td>
  <td>Целевая страница для параметров</td>
  <td>ms-settings:</td>
  <td></td>
 </tr>
 <tr>
  <td rowspan="10">Сеть и Интернет</td>
  <td>Ethernet</td>
  <td>ms-settings:network-ethernet</td>
  <td></td>
 </tr>
 <tr>
  <td>VPN</td>
  <td>ms-settings:network-vpn</td>
  <td></td>
 </tr>
 <tr>
  <td>Коммутируемое соединение</td>
  <td>ms-settings:network-dialup</td>
  <td></td>
 </tr>
 <tr>
  <td>DirectAccess</td>
  <td>ms-settings:network-directaccess</td>
  <td>Доступно, только если DirectAccess включен</td>
 </tr>
 <tr>
  <td>Вызов через Wi-Fi</td>
  <td>ms-settings:network-wificalling</td>
  <td>Доступно, только если вызов через Wi-Fi включен</td>
 </tr>
 <tr>
  <td>Использование данных</td>
  <td>ms-settings:datausage</td>
  <td></td>
 </tr>
 <tr>
  <td>Передача данных и SIM</td>
  <td>ms-settings:network-cellular</td>
  <td></td>
 </tr>
 <tr>
  <td>Мобильный хот-спот</td>
  <td>ms-settings:network-mobilehotspot</td>
  <td></td>
 </tr>
 <tr>
  <td>Прокси-сервер</td>
  <td>ms-settings:network-proxy</td>
  <td></td>
 </tr>
 <tr>
  <td>Статус</td>
  <td>ms-settings:network-status</td>
  <td></td>
 </tr>
 <tr>
  <td>Управление известными сетями</td>
  <td>ms-settings:network-wifisettings</td>
  <td></td>
 </tr>
 <tr>
  <td rowspan="3">Сеть и беспроводная сеть</td>
  <td>NFC</td>
  <td>ms-settings:nfctransactions</td>
  <td></td>
 </tr>
 <tr>
  <td>Wi-Fi</td>
  <td>ms-settings:network-wifi</td>
  <td>Доступны, только если устройство оснащено адаптером Wi-Fi</td>
 </tr>
 <tr>
  <td>Режим "в самолете"</td>
  <td>ms-settings:network-airplanemode</td>
  <td>Используйте ms-settings:proximity в Windows 8.x</td>
 </tr>
 <tr>
  <td rowspan="10">Персонализация</td>
  <td>Пуск</td>
  <td>ms-settings:personalization-start</td>
  <td></td>
 </tr>
 <tr>
  <td>Темы</td>
  <td>ms-settings:themes</td>
  <td></td>
 </tr>
 <tr>
  <td>Заставка</td>
  <td>ms-settings:personalization-glance</td>
  <td></td>
 </tr>
 <tr>
  <td>Панель навигации</td>
  <td>ms-settings:personalization-navbar</td>
  <td></td>
 </tr>
 <tr>
  <td>Персонализация (категория)</td>
   <td>ms-settings:personalization</td>
   <td></td>
 </tr>
 <tr>
  <td>Общая информация</td>
   <td>ms-settings:personalization-background</td>
   <td></td>
 </tr>
 <tr>
  <td>Цвета</td>
   <td>ms-settings:personalization-colors</td>
   <td></td>
 </tr>
 <tr>
  <td>Звуки</td>
   <td>ms-settings:sounds</td>
   <td></td>
 </tr>
 <tr>
  <td>Экран блокировки</td>
   <td>ms-settings:lockscreen</td>
   <td></td>
 </tr>
 <tr>
  <td>Панель задач</td>
   <td>ms-settings:taskbar</td>
   <td></td>
 </tr>
 <tr>
  <td rowspan="22">Конфиденциальность</td>
  <td>Диагностика приложений</td>
   <td>ms-settings:privacy-appdiagnostics</td>
   <td></td>
 </tr>
 <tr>
  <td>Уведомления</td>
   <td>ms-settings:privacy-notifications</td>
   <td></td>
 </tr>
 <tr>
  <td>Задачи</td>
   <td>ms-settings:privacy-tasks</td>
   <td></td>
 </tr>
 <tr>
  <td>Общие</td>
   <td>ms-settings:privacy-general</td>
   <td></td>
 </tr>
 <tr>
  <td>Приложения-помощники</td>
   <td>ms-settings:privacy-accessoryapps</td>
   <td></td>
 </tr>
 <tr>
  <td>Идентификатор получателя рекламы</td>
   <td>ms-settings:privacy-advertisingid</td>
   <td></td>
 </tr>
 <tr>
  <td>Телефонные звонки</td>
   <td>ms-settings:privacy-phonecall</td>
   <td></td>
 </tr>
 <tr>
  <td>Расположение</td>
   <td>ms-settings:privacy-location</td>
   <td></td>
 </tr>
 <tr>
  <td>Камера</td>
   <td>ms-settings:privacy-webcam</td>
   <td></td>
 </tr>
 <tr>
  <td>Микрофон</td>
   <td>ms-settings:privacy-microphone</td>
   <td></td>
 </tr>
 <tr>
  <td>Движение</td>
   <td>ms-settings:privacy-motion</td>
   <td></td>
 </tr>
 <tr>
  <td>Голосовые функции, рукописный ввод и ввод с клавиатуры</td>
   <td>ms-settings:privacy-speechtyping</td>
   <td></td>
 </tr>
 <tr>
  <td>Сведения об учетной записи</td>
   <td>ms-settings:privacy-accountinfo</td>
   <td></td>
 </tr>
 <tr>
  <td>Контакты</td>
   <td>ms-settings:privacy-contacts</td>
   <td></td>
 </tr>
 <tr>
  <td>Календарь</td>
   <td>ms-settings:privacy-calendar</td>
   <td></td>
 </tr>
 <tr>
  <td>Журнал вызовов</td>
   <td>ms-settings:privacy-callhistory</td>
   <td></td>
 </tr>
 <tr>
  <td>Электронная почта</td>
  <td>ms-settings:privacy-email</td>
  <td></td>
 </tr>
 <tr>
  <td>Сообщения</td>
    <td>ms-settings:privacy-messaging</td>
  <td></td>
 </tr>
 <tr>
  <td>Радиомодули</td>
    <td>ms-settings:privacy-radios</td>
  <td></td>
 </tr>
 <tr>
  <td>Фоновые приложения</td>
    <td>ms-settings:privacy-backgroundapps</td>
  <td></td>
 </tr>
 <tr>
  <td>Другие устройства</td>
    <td>ms-settings:privacy-customdevices</td>
  <td></td>
 </tr>
 <tr>
  <td>Отзывы и диагностика</td>
    <td>ms-settings:privacy-feedback</td>
  <td></td>
 </tr>
 <tr>
  <td rowspan="5">Surface Hub</td>
  <td>Учетные записи</td>
    <td>ms-settings:surfacehub-accounts</td>
      <td></td>
  </tr>
  <tr>
    <td>Групповые конференции</td>
      <td>ms-settings:surfacehub-calling</td>
      <td></td>
  </tr>
  <tr>
    <td>Управление устройствами группы</td>
      <td>ms-settings:surfacehub-devicemanagenent</td>
      <td></td>
  </tr>
  <tr>
    <td>Очистка сеанса</td>
      <td>ms-settings:surfacehub-sessioncleanup</td>
      <td></td>
  </tr>
  <tr>
    <td>Экран приветствия</td>
      <td>ms-settings:surfacehub-welcome</td>
      <td></td>
  </tr>
    <td rowspan="19">Система</td>
    <td>Общие возможности</td>
      <td>ms-settings:crossdevice</td>
    <td></td>
 </tr>
 <tr>
  <td>Экран</td>
    <td>ms-settings:display</td>
  <td></td>
 </tr>
 <tr>
  <td>Многозадачность</td>
    <td>ms-settings:multitasking</td>
  <td></td>
 </tr>
 <tr>
  <td>Проецирование на этот компьютер</td>
    <td>ms-settings:project</td>
  <td></td>
 </tr>
 <tr>
  <td>Режим планшета</td>
    <td>ms-settings:tabletmode</td>
  <td></td>
 </tr>
 <tr>
  <td>Панель задач</td>
    <td>ms-settings:taskbar</td>
  <td></td>
 </tr>
 <tr>
  <td>Телефон</td>
    <td>ms-settings:phone-defaultapps</td>
  <td></td>
 </tr>
 <tr>
  <td>Экран</td>
    <td>ms-settings:screenrotation</td>
  <td></td>
 </tr>
 <tr>
  <td>Уведомления и действия</td>
    <td>ms-settings:notifications</td>
  <td></td>
 </tr>
 <tr>
  <td>Телефон</td>
    <td>ms-settings:phone</td>
  <td></td>
 </tr>
 <tr>
  <td>Сообщения</td>
    <td>ms-settings:messaging</td>
  <td></td>
 </tr>
 <tr>
  <td>Экономия заряда аккумулятора</td>
  <td>ms-settings:batterysaver</td>
  <td>Доступно только на устройствах с аккумулятором, таких как планшет</td>
 </tr>
 <tr>
  <td>Использование аккумулятора</td>
  <td>ms-settings:batterysaver-usagedetails</td>
  <td>Доступно только на устройствах с аккумулятором, таких как планшет</td>
 </tr>
 <tr>
  <td>Питание и спящий режим</td>
  <td>ms-settings:powersleep</td>
  <td></td>
 </tr>
 <tr>
  <td>Описание</td>
    <td>ms-settings:about</td>
  <td></td>
 </tr>
 <tr>
  <td>Память</td>
    <td>ms-settings:storagesense</td>
  <td></td>
 </tr>
 <tr>
  <td>Контроль памяти</td>
    <td>ms-settings:storagepolicies</td>
  <td></td>
 </tr>
 <tr>
  <td>Шифрование</td>
    <td>ms-settings:deviceencryption</td>
  <td></td>
 </tr>
 <tr>
  <td>Карты для автономного использования</td>
    <td>ms-settings:maps</td>
  <td></td>
 </tr>
 <tr>
  <td rowspan="5">Время и язык</td>
  <td>Дата и время</td>
    <td>ms-settings:dateandtime</td>
  <td></td>
 </tr>
 <tr>
  <td>Язык и региональные стандарты</td>
    <td>ms-settings:regionlanguage</td>
  <td></td>
 </tr>
 <tr>
     <td>Язык голосовых функций</td>
     <td>ms-settings:speech</td>
     <td></td>
 </tr>
 <tr>
     <td>Клавиатура пиньинь</td>
     <td>ms-settings:regionlanguage-chsime-pinyin</td>
     <td>Доступно, если установлен редактор метода ввода Microsoft Pinyin</td>
 </tr>
 <tr>
     <td>Режим ввода Wubi</td>
     <td>ms-settings:regionlanguage-chsime-wubi</td>
     <td>Доступно, если установлен редактор метода ввода Microsoft Wubi</td>
 </tr>
 <tr>
  <td rowspan="13">Обновление и безопасность</td>
  <td>Настройка функции Windows Hello</td>
    <td>ms-settings:signinoptions-launchfaceenrollment<br>ms-settings:signinoptions-launchfingerprintenrollment</td>
  </tr>
  <tr>
    <td>Архивация</td>
      <td>ms-settings:backup</td>
    <td></td>
 </tr>
 <tr>
  <td>Поиск устройства</td>
    <td>ms-settings:findmydevice</td>
  <td></td>
 </tr>
 <tr>
  <td>Программа предварительной оценки Windows</td>
  <td>ms-settings:windowsinsider</td>
  <td>Доступно, только если пользователь зарегистрирован в WIP</td>
 </tr>
 <tr>
  <td>Центр обновления Windows</td>
  <td>ms-settings:windowsupdate</td>
  <td></td>
 </tr>
 <tr>
  <td>Центр обновления Windows</td>
    <td>ms-settings:windowsupdate-history</td>
  <td></td>
 </tr>
 <tr>
  <td>Центр обновления Windows</td>
    <td>ms-settings:windowsupdate-options</td>
  <td></td>
 </tr>
 <tr>
  <td>Центр обновления Windows</td>
    <td>ms-settings:windowsupdate-restartoptions</td>
  <td></td>
 </tr>
 <tr>
  <td>Центр обновления Windows</td>
    <td>ms-settings:windowsupdate-action</td>
  <td></td>
 </tr>
 <tr>
  <td>Активация</td>
    <td>ms-settings:activation</td>
  <td></td>
 </tr>
 <tr>
  <td>Восстановление</td>
    <td>ms-settings:recovery</td>
  <td></td>
 </tr>
 <tr>
  <td>Устранение неполадок</td>
    <td>ms-settings:troubleshoot</td>
  <td></td>
 </tr>
 <tr>
  <td>Защитник Windows</td>
    <td>ms-settings:windowsdefender</td>
  <td></td>
 </tr>
 <tr>
  <td>Для разработчиков</td>
    <td>ms-settings:developers</td>
  <td></td>
 </tr>
 <tr>
  <td rowspan="2">Ученые записи пользователей</td>
  <td>Windows Anywhere</td>
  <td>ms-settings:windowsanywhere</td>
  <td>Устройство должно поддерживать Windows Anywhere</td>
 </tr>
 <tr>
  <td>Подготовка</td>
  <td>ms-settings:workplace-provisioning</td>
  <td>Доступно, только если на предприятии развернут пакет подготовки</td>
 </tr>
</table><br/>  
