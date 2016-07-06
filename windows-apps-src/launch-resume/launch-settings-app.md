---
author: TylerMSFT
title: "Запуск приложения \"Параметры\" для Windows"
description: "Узнайте, как запустить приложение \"Параметры\" для Windows из вашего приложения. В этом разделе приводится описание схемы URI ms-settings. Используйте эту схему URI для запуска приложения \"Параметры\" для Windows на определенных страницах параметров."
ms.assetid: C84D4BEE-1FEE-4648-AD7D-8321EAC70290
ms.sourcegitcommit: 3cf9dd4ab83139a2b4b0f44a36c2e57a92900903
ms.openlocfilehash: e52a4245e8697a68bfc5c5605dc54e5ea510c662

---

# Запуск приложения "Параметры" для Windows


\[ Обновлено для приложений UWP в Windows 10. Статьи о Windows 8.x см. в [архиве](http://go.microsoft.com/fwlink/p/?linkid=619132) \]


**Важные API**

-   [**LaunchUriAsync**](https://msdn.microsoft.com/library/windows/apps/hh701476)
-   [**PreferredApplicationPackageFamilyName**](https://msdn.microsoft.com/library/windows/apps/hh965482)
-   [**DesiredRemainingView**](https://msdn.microsoft.com/library/windows/apps/dn298314)

Узнайте, как запустить приложение «Параметры» для Windows из вашего приложения. В этом разделе приводится описание схемы URI **ms-settings:**. Используйте эту схему URI для запуска приложения «Параметры» для Windows на определенных страницах параметров.

Запуск приложения «Параметры»— важная составляющая создания приложения, учитывающего требования конфиденциальности. Если ваше приложение не может получить доступ к конфиденциальному ресурсу, рекомендуется предоставить пользователю удобную ссылку на параметры конфиденциальности для этого ресурса. Дополнительные сведения см. в статье [Руководство по приложениям, учитывающим конфиденциальность](https://msdn.microsoft.com/library/windows/apps/hh768223).

## Запуск приложения «Параметры»


Если параметры конфиденциальности не позволяют вашему приложению получить доступ к конфиденциальному ресурсу, рекомендуется предоставить пользователю удобную ссылку на параметры конфиденциальности в приложении **Параметры**. Это поможет пользователям изменить параметры.

Для запуска непосредственно в приложении **Параметры** используйте схему URI `ms-settings:`, как показано в следующих примерах.

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

Либо ваше приложение может вызвать метод [**LaunchUriAsync**](https://msdn.microsoft.com/library/windows/apps/hh701476), чтобы запустить приложение **Параметры** из кода.

```cs
using Windows.System;
...
bool result = await Launcher.LaunchUriAsync(new Uri("ms-settings:privacy-webcam"));
```

В этом примере показано, как запустить страницу параметров конфиденциальности для камеры с помощью URI `ms-settings:privacy-webcam`.

![Параметры конфиденциальности камеры.](images/privacyawarenesssettingsapp.png)

Дополнительные сведения о запуске URI см. в разделе [Запуск приложения по умолчанию для URI](launch-default-app.md).

## Справка по схеме URI ms-settings:


Используйте следующие URI для открытия разных страниц приложения «Параметры». Обратите внимание, что в столбце «Поддерживаемые SKU» указано, существует ли данная страница параметров в классических выпусках Windows 10 (Домашняя, Pro, Корпоративная и для образовательных учреждений), в Windows 10 Mobile или в обоих вариантах.

| Категория           | Страница параметров                          | Поддерживаемые SKU | Универсальный код ресурса (URI)                                       |
|--------------------|----------------------------------------|----------------|-------------------------------------------|
| Домашняя страница          | Целевая страница для параметров              | То и другое           | ms-settings:                              |
| Система             | Дисплей                                | То и другое           | ms-settings:screenrotation                |
|                    | Уведомления и действия                | То и другое           | ms-settings:notifications                 |
|                    | Телефон                                  | Только на мобильных    | ms-settings:phone                         |
|                    | Сообщения                              | Только на мобильных    | ms-settings:messaging                     |
|                    | Экономия заряда аккумулятора                          | В мобильной и настольной версиях на устройствах с батареей, таких как планшет    | ms-settings:batterysaver                  |
|                    | Экономия заряда аккумулятора и параметры экономии | В мобильной и настольной версиях на устройствах с батареей, таких как планшет | ms-settings:batterysaver-settings         |
|                    | Экономия и использование заряда аккумулятора            | В мобильной и настольной версиях на устройствах с батареей, таких как планшет    | ms-settings:batterysaver-usagedetails     |
|                    | Питание и спящий режим                          | Только на ПК   | ms-settings:powersleep                    |
|                    | ПК: описание                         | То и другое           | ms-settings:deviceencryption              |
|                    |                                        |                |                                           |
|                    | Мобильное устройство: описание устройства              |                |                                           |
|                    | Карты для автономного использования                           | То и другое           | ms-settings:maps                          |
|                    | Описание                                  | То и другое           | ms-settings:about                         |
| Устройства            | Камера по умолчанию                         | Только на мобильных    | ms-settings:camera                        |
|                    | Bluetooth                              | Только на ПК   | ms-settings:bluetooth                     |
|                    | Мышь и сенсорная панель                       | То и другое           | ms-settings:mousetouchpad                 |
|                    | NFC                                    | То и другое           | ms-settings:nfctransactions               |
| Сеть и беспроводная сеть | Wi-Fi                                  | То и другое           | ms-settings:network-wifi                  |
|                    | Режим «В самолете»                          | То и другое           | ms-settings:network-airplanemode          |
| Сеть и Интернет | Использование данных                             | То и другое           | ms-settings:datausage                     |
|                    | Передача данных и SIM                         | То и другое           | ms-settings:network-cellular              |
|                    | Мобильный хот-спот                         | То и другое           | ms-settings:network-mobilehotspot         |
|                    | Прокси-сервер                                  | То и другое           | ms-settings:network-proxy                 |
| Персонализация    | Персонализация (категория)             | То и другое           | ms-settings:personalization               |
|                    | Фон                             | Только на ПК   | ms-settings:personalization-background    |
|                    | Цвета                                 | То и другое           | ms-settings:personalization-colors        |
|                    | Звуки                                 | Только на мобильных    | ms-settings:sounds                        |
|                    | Экран блокировки                            | То и другое           | ms-settings:lockscreen                    |
| Учетные записи           | Электронная почта и учетные записи                | То и другое           | ms-settings:emailandaccounts              |
|                    | Рабочий доступ                            | То и другое           | ms-settings:workplace                     |
|                    | Синхронизация параметров                     | То и другое           | ms-settings:sync                          |
| Время и язык  | Дата и время                            | То и другое           | ms-settings:dateandtime                   |
|                    | Язык и региональные стандарты                      | Только на ПК   | ms-settings:regionlanguage                |
| Специальные возможности     | Экранный диктор                               | То и другое           | ms-settings:easeofaccess-narrator         |
|                    | Экранная лупа                              | То и другое           | ms-settings:easeofaccess-magnifier        |
|                    | Высокая контрастность                          | То и другое           | ms-settings:easeofaccess-highcontrast     |
|                    | Скрытые субтитры                        | То и другое           | ms-settings:easeofaccess-closedcaptioning |
|                    | Клавиатура                               | То и другое           | ms-settings:easeofaccess-keyboard         |
|                    | Мышь                                  | То и другое           | ms-settings:easeofaccess-mouse            |
|                    | Другие параметры                          | То и другое           | ms-settings:easeofaccess-otheroptions     |
| Конфиденциальность            | Местоположение                               | То и другое           | ms-settings:privacy-location              |
|                    | Камера                                 | То и другое           | ms-settings:privacy-webcam                |
|                    | Микрофон                             | То и другое           | ms-settings:privacy-microphone            |
|                    | Движение                                 | То и другое           | ms-settings:privacy-motion                |
|                    | Голосовые функции, рукописный ввод и ввод с клавиатуры                | То и другое           | ms-settings:privacy-speechtyping          |
|                    | Сведения об учетной записи                           | То и другое           | ms-settings:privacy-accountinfo           |
|                    | Контакты                               | То и другое           | ms-settings:privacy-contacts              |
|                    | Календарь                               | То и другое           | ms-settings:privacy-calendar              |
|                    | Журнал вызовов                           | То и другое           | ms-settings:privacy-callhistory           |
|                    | Электронная почта                                  | То и другое           | ms-settings:privacy-email                 |
|                    | Обмен сообщениями                              | То и другое           | ms-settings:privacy-messaging             |
|                    | Радиомодули                                 | То и другое           | ms-settings:privacy-radios                |
|                    | Фоновые приложения                        | То и другое           | ms-settings:privacy-backgroundapps        |
|                    | Другие устройства                          | То и другое           | ms-settings:privacy-customdevices         |
|                    | Отзывы и диагностика                 | То и другое           | ms-settings:privacy-feedback              |
| Обновление и безопасность  | Для разработчиков                         | То и другое           | ms-settings:developers                    |
 



<!--HONumber=Jun16_HO5-->


