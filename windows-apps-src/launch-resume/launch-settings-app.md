---
author: TylerMSFT
title: "Запуск приложения &quot;Параметры&quot; для Windows"
description: "Узнайте, как запустить приложение &quot;Параметры&quot; для Windows из вашего приложения. В этом разделе приводится описание схемы URI ms-settings. Используйте эту схему URI для запуска приложения &quot;Параметры&quot; для Windows на определенных страницах параметров."
ms.assetid: C84D4BEE-1FEE-4648-AD7D-8321EAC70290
translationtype: Human Translation
ms.sourcegitcommit: 1135feec72510e6cbe955161ac169158a71097b9
ms.openlocfilehash: f762d7eb70a0e9119f32350a815691109f994c75

---

# <a name="launch-the-windows-settings-app"></a>Запуск приложения "Параметры" для Windows

\[ Обновлено для приложений UWP в Windows 10. Статьи о Windows 8.x см. в [архиве](http://go.microsoft.com/fwlink/p/?linkid=619132) \]

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

Используйте следующие URI для открытия разных страниц приложения «Параметры». Обратите внимание, что в столбце "Поддерживаемые SKU" указано, существует ли данная страница параметров в классических выпусках Windows 10 (Домашняя, Pro и Корпоративная и для образовательных учреждений), в Windows 10 Mobile или в обоих вариантах.

<table border="1">
    <tr>
        <th>Категория</th>
        <th>Страница параметров</th>
        <th>Поддерживаемые SKU</th>
        <th>URI</th>
    </tr>
    <tr>
        <td>Домашняя страница</td>
        <td>Целевая страница для параметров</td>
        <td>Устройства обоих типов</td>
        <td>ms-settings:</td>
    </tr>
    <tr>
        <td rowspan="10">Система</td>
        <td>Экран</td>
        <td>То и другое</td>
        <td>ms-settings:screenrotation</td>
    </tr>
    <tr>
        <td>Уведомления и действия</td>
        <td>То и другое</td>
        <td>ms-settings:notifications</td>
    </tr>
    <tr>
        <td>Телефон</td>
        <td>Только на мобильных</td>
        <td>ms-settings:phone</td>
    </tr>
    <tr>
        <td>Сообщения</td>
        <td>Только на мобильных</td>
        <td>ms-settings:messaging</td>
    </tr>
    <tr>
        <td>Экономия заряда аккумулятора</td>
        <td>Устройства обоих типов<br>Доступно только на устройствах с аккумулятором, таких как планшет</td>
        <td>ms-settings:batterysaver</td>
    </tr>
    <tr>
        <td>Использование аккумулятора</td>
        <td>Устройства обоих типов<br>Доступно только на устройствах с аккумулятором, таких как планшет</td>
        <td>ms-settings:batterysaver-usagedetails</td>
    </tr>
    <tr>
        <td>Питание и спящий режим</td>
        <td>Только на ПК</td>
        <td>ms-settings:powersleep</td>
    </tr>
    <tr>
        <td>Описание</td>
        <td>Устройства обоих типов</td>
        <td>ms-settings:about</td>
    </tr>
    <tr>
        <td>Шифрование</td>
        <td>Устройства обоих типов</td>
        <td>ms-settings:deviceencryption</td>
    </tr>
    <tr>
        <td>Карты для автономного использования</td>
        <td>Устройства обоих типов</td>
        <td>ms-settings:maps</td>
    </tr>
    <tr>
        <td rowspan="4">Устройства</td>
        <td>Камера по умолчанию</td>
        <td>Только на мобильных</td>
        <td>ms-settings:camera</td>
    </tr>
    <tr>
        <td>Bluetooth</td>
        <td>Только на ПК</td>
        <td>ms-settings:bluetooth</td>
    </tr>
    <tr>
        <td>Подключенные устройства</td>
        <td>Только на ПК</td>
        <td>ms-settings:connecteddevices</td>
    </tr>
    <tr>
        <td>Мышь и сенсорная панель</td>
        <td>Устройства обоих типов<br>Параметры сенсорной панели доступны только на устройствах с сенсорной панелью</td>
        <td>ms-settings:mousetouchpad</td>
    </tr>
    <tr>
        <td rowspan="3">Сеть и беспроводная сеть</td>
        <td>NFC</td>
        <td>Устройства обоих типов</td>
        <td>ms-settings:nfctransactions</td>
    </tr>
    <tr>
        <td>Wi-Fi</td>
        <td>Устройства обоих типов</td>
        <td>ms-settings:network-wifi</td>
    </tr>
    <tr>
        <td>Режим "в самолете"</td>
        <td>Устройства обоих типов</td>
        <td>ms-settings:network-airplanemode</td>
    </tr>
    <tr>
        <td rowspan="5">Сеть и Интернет</td>
        <td>Использование данных</td>
        <td>То и другое</td>
        <td>ms-settings:datausage</td>
    </tr>
    <tr>
        <td>Передача данных и SIM</td>
        <td>То и другое</td>
        <td>ms-settings:network-cellular</td>
    </tr>
    <tr>
        <td>Мобильный хот-спот</td>
        <td>Устройства обоих типов</td>
        <td>ms-settings:network-mobilehotspot</td>
    </tr>
    <tr>
        <td>Прокси-сервер</td>
        <td>Только на ПК</td>
        <td>ms-settings:network-proxy</td>
    </tr>
    <tr>
        <td>Состояние</td>
        <td>Только на ПК</td>
        <td>ms-settings:network-status</td>
    </tr>
    <tr>
        <td rowspan="5">Персонализация</td>
        <td>Персонализация (категория)</td>
        <td>То и другое</td>
        <td>ms-settings:personalization</td>
    </tr>
    <tr>
        <td>Фон</td>
        <td>Только на ПК</td>
        <td>ms-settings:personalization-background</td>
    </tr>
    <tr>
        <td>Цвета</td>
        <td>То и другое</td>
        <td>ms-settings:personalization-colors</td>
    </tr>
    <tr>
        <td>Звуки</td>
        <td>Только на мобильных </td>
        <td>ms-settings:sounds</td>
    </tr>
    <tr>
        <td>Экран блокировки</td>
        <td>То и другое</td>
        <td>ms-settings:lockscreen</td>
    </tr>
    <tr>
        <td rowspan="7">Учетные записи</td>
        <td>Доступ на рабочем месте или в учебном учреждении</td>
        <td>То и другое</td>
        <td>ms-settings:workplace</td>
    </tr>
    <tr>
        <td>Учетные записи электронной почты и приложений</td>
        <td>То и другое</td>
        <td>ms-settings:emailandaccounts</td>
    </tr>
    <tr>
        <td>Семья и другие пользователи</td>
        <td>То и другое</td>
        <td>ms-settings:otherusers</td>
    </tr>
    <tr>
        <td>Параметры входа</td>
        <td>То и другое</td>
        <td>ms-settings:signinoptions</td>
    </tr>
    <tr>
        <td>Синхронизация параметров</td>
        <td>То и другое</td>
        <td>ms-settings:sync</td>
    </tr>
    <tr>
        <td>Другие пользователи</td>
        <td>То и другое</td>
        <td>ms-settings:otherusers</td>
    </tr>
    <tr>
        <td>Сведения</td>
        <td>То и другое</td>
        <td>ms-settings:yourinfo</td>
    </tr>
    <tr>
        <td rowspan="2">Время и язык</td>
        <td>Дата и время</td>
        <td>То и другое</td>
        <td>ms-settings:dateandtime</td>
    </tr>
    <tr>
        <td>Язык и региональные стандарты</td>
        <td>Только на ПК</td>
        <td>ms-settings:regionlanguage</td>
    </tr>
    <tr>
        <td rowspan="7">Специальные возможности</td>
        <td>Экранный диктор</td>
        <td>То и другое</td>
        <td>ms-settings:easeofaccess-narrator</td>
    </tr>
    <tr>
        <td>Экранная лупа</td>
        <td>То и другое</td>
        <td>ms-settings:easeofaccess-magnifier</td>
    </tr>
    <tr>
        <td>Высокая контрастность </td>
        <td>То и другое</td>
        <td>ms-settings:easeofaccess-highcontrast</td>
    </tr>
    <tr>
        <td>Скрытые субтитры</td>
        <td>То и другое</td>
        <td>ms-settings:easeofaccess-closedcaptioning</td>
    </tr>
    <tr>
        <td>Клавиатура</td>
        <td>То и другое</td>
        <td>ms-settings:easeofaccess-keyboard</td>
    </tr>
    <tr>
        <td>Мышь</td>
        <td>То и другое</td>
        <td>ms-settings:easeofaccess-mouse</td>
    </tr>
    <tr>
        <td>Другие параметры</td>
        <td>То и другое</td>
        <td>ms-settings:easeofaccess-otheroptions</td>
    </tr>
    <tr>
        <td rowspan="15">Конфиденциальность</td>
        <td>Местоположение</td>
        <td>То и другое</td>
        <td>ms-settings:privacy-location</td>
    </tr>
    <tr>
        <td>Камера</td>
        <td>То и другое</td>
        <td>ms-settings:privacy-webcam</td>
    </tr>
    <tr>
        <td>Микрофон</td>
        <td>То и другое</td>
        <td>ms-settings:privacy-microphone</td>
    </tr>
    <tr>
        <td>Движение</td>
        <td>То и другое</td>
        <td>ms-settings:privacy-motion</td>
    </tr>
    <tr>
        <td>Голосовые функции, рукописный ввод и ввод с клавиатуры</td>
        <td>То и другое</td>
        <td>ms-settings:privacy-speechtyping</td>
    </tr>
    <tr>
        <td>Сведения об учетной записи</td>
        <td>То и другое</td>
        <td>ms-settings:privacy-accountinfo</td>
    </tr>
    <tr>
        <td>Контакты</td>
        <td>То и другое</td>
        <td>ms-settings:privacy-contacts</td>
    </tr>
    <tr>
        <td>Календарь</td>
        <td>То и другое</td>
        <td>ms-settings:privacy-calendar</td>
    </tr>
    <tr>
        <td>Журнал вызовов</td>
        <td>То и другое</td>
        <td>ms-settings:privacy-callhistory</td>
    </tr>
    <tr>
        <td>Электронная почта</td>
        <td>То и другое</td>
        <td>ms-settings:privacy-email</td>
    </tr>
    <tr>
        <td>Обмен сообщениями</td>
        <td>То и другое</td>
        <td>ms-settings:privacy-messaging</td>
    </tr>
    <tr>
        <td>Радиомодули</td>
        <td>То и другое</td>
        <td>ms-settings:privacy-radios</td>
    </tr>
    <tr>
        <td>Фоновые приложения</td>
        <td>То и другое</td>
        <td>ms-settings:privacy-backgroundapps</td>
    </tr>
    <tr>
        <td>Другие устройства</td>
        <td>То и другое</td>
        <td>ms-settings:privacy-customdevices</td>
    </tr>
    <tr>
        <td>Отзывы и диагностика</td>
        <td>То и другое</td>
        <td>ms-settings:privacy-feedback</td>
    </tr>
    <tr>
        <td>Обновление и безопасность</td>
        <td>Для разработчиков</td>
        <td>То и другое</td>
        <td>ms-settings:developers</td>
    </tr>
</table><br/>



<!--HONumber=Dec16_HO1-->


