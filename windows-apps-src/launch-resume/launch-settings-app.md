---
title: Запуск приложения "Параметры" для Windows
description: Узнайте, как запустить приложение "Параметры" для Windows из вашего приложения. В этом разделе приводится описание схемы URI ms-settings. Используйте эту схему URI для запуска приложения "Параметры" для Windows на определенных страницах параметров.
ms.assetid: C84D4BEE-1FEE-4648-AD7D-8321EAC70290
ms.date: 04/19/2019
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.custom: 19H1
ms.openlocfilehash: 1a0c632089c8c4cf84ecf6df99935671423b51a6
ms.sourcegitcommit: ac7f3422f8d83618f9b6b5615a37f8e5c115b3c4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/29/2019
ms.locfileid: "66370808"
---
# <a name="launch-the-windows-settings-app"></a>Запуск приложения "Параметры" для Windows

**Важные API**

-   [**LaunchUriAsync**](https://docs.microsoft.com/uwp/api/windows.system.launcher.launchuriasync)
-   [**PreferredApplicationPackageFamilyName**](https://docs.microsoft.com/uwp/api/windows.system.launcheroptions.preferredapplicationpackagefamilyname)
-   [**DesiredRemainingView**](https://docs.microsoft.com/uwp/api/windows.system.launcheroptions.desiredremainingview)

Узнайте, как запустить приложение «Параметры» для Windows. В этом разделе описывается **ms параметры:** Схема URI. Используйте эту схему URI для запуска приложения "Параметры" для Windows на определенных страницах параметров.

Запуск приложения «Параметры» — важная составляющая создания приложения, учитывающего требования конфиденциальности. Если ваше приложение не может получить доступ к конфиденциальному ресурсу, рекомендуется предоставить пользователю удобную ссылку на параметры конфиденциальности для этого ресурса. Дополнительные сведения см. в статье [Руководство по приложениям, учитывающим конфиденциальность](https://docs.microsoft.com/windows/uwp/security/index).

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

Либо ваше приложение может вызвать метод [**LaunchUriAsync**](https://docs.microsoft.com/uwp/api/windows.system.launcher.launchuriasync), чтобы запустить приложение **Параметры**. В этом примере показано, как запустить страницу параметров конфиденциальности для камеры с помощью URI `ms-settings:privacy-webcam`.

```cs
bool result = await Windows.System.Launcher.LaunchUriAsync(new Uri("ms-settings:privacy-webcam"));
```

Приведенный выше код запускает страницу параметров конфиденциальности для камеры:

![Параметры конфиденциальности камеры.](images/privacyawarenesssettingsapp.png)

Дополнительные сведения о запуске URI см. в разделе [Запуск приложения по умолчанию для URI](launch-default-app.md).

## <a name="ms-settings-uri-scheme-reference"></a>ms-settings: Схема URI-ссылка

Используйте следующие URI для открытия разных страниц приложения «Параметры».

> Обратите внимание, что доступность страницы параметров зависит от Windows SKU. Не все страницы параметров, доступные в Windows 10 для настольных систем, доступны в Windows 10 Mobile и наоборот. В столбце "Примечания" также представлены дополнительные требования, которые должны быть выполнены, чтобы страница была доступна.

<!-- TODO: 
* ms-settings:controlcenter
* ms-settings:holographic
* ms-settings:keyboard-advanced
* ms-settings:regionlanguage-adddisplaylanguage (crashed)
* ms-settings:regionlanguage-setdisplaylanguage (crashed)
* ms-settings:signinoptions-launchpinenrollment
* ms-settings:storagecleanup
* ms-settings:update-security -->

## <a name="accounts"></a>Учетные записи

|Страница параметров| URI |
|-------------|-----|
| Доступ на рабочем месте или в учебном учреждении | ms-settings:workplace |
| Учетные записи электронной почты и приложений  | ms-settings:emailandaccounts |
| Семья и другие пользователи | ms-settings:otherusers |
| Настройка киоска | ms-settings:assignedaccess |
| Параметры входа | ms-settings:signinoptions<br>ms-settings:signinoptions-dynamiclock |
| Синхронизация параметров | ms-settings:sync |
| Настройка функции Windows Hello | ms-settings:signinoptions-launchfaceenrollment<br>ms-settings:signinoptions-launchfingerprintenrollment |
| Сведения | ms-settings:yourinfo |

## <a name="apps"></a>Приложения

|Страница параметров| URI |
|-------------|-----|
| Приложения и компоненты | ms-settings:appsfeatures |
| Возможности приложения | ms-settings:appsfeatures-app (сброс, управление надстройками и скачиваемым контентом и т. п. для приложения)|
| Приложения для веб-сайтов | ms-settings:appsforwebsites |
| Приложения по умолчанию | ms-settings:defaultapps |
| Управление дополнительными возможностями | ms-settings:optionalfeatures |
| Карты для автономного использования | ms-settings:maps<br/>MS-параметры: maps-downloadmaps (maps загрузки) |
| Автозагружаемые приложения | ms-settings:startupapps |
| Воспроизведение видео | ms-settings:videoplayback |

## <a name="cortana"></a>Кортана

|Страница параметров| URI |
|-------------|-----|
| "Кортана на моих устройствах" | ms-settings:cortana-notifications |
| Подробности | ms-settings:cortana-moredetails |
| Разрешения & журнал | ms-settings:cortana-permissions |
| Поиск Windows | ms-settings:cortana-windowssearch |
| Общение с Кортаной | ms-settings:cortana-language<br/>ms-settings:cortana<br/>MS-параметры: cortana-talktocortana |

> [!NOTE] 
> В этом разделе "Параметры" на рабочем столе будет вызываться поиска ПК, настроенное в регионах, где Cortana в настоящее время недоступна или отключена Cortana. Страниц, относящихся к Cortana (Cortana в Мои устройства), а также общения с Cortana будет отсутствовать в этом случае. 

## <a name="devices"></a>Устройства

|Страница параметров| URI |
|-------------|-----|
| Автозапуск | ms-settings:autoplay |
| Bluetooth | ms-settings:bluetooth |
| Подключенные устройства | ms-settings:connecteddevices |
| Камера по умолчанию | MS-параметры: камеры (**устаревшими в Windows 10, версия 1809 и более поздние версии**) |
| Мышь и сенсорная панель | ms-settings:mousetouchpad (параметры сенсорной панели доступны только на устройствах с сенсорной панелью) |
| Перо и Windows Ink | ms-settings:pen |
| Принтеры и сканеры | ms-settings:printers |
| Сенсорная панель | ms-settings:devices-touchpad (доступно только на устройствах с сенсорной панелью) |
| Ввод с клавиатуры | ms-settings:typing |
| USB | ms-settings:usb |
| Колесо | ms-settings:wheel (доступно, только если устройство Dial связано) |
| Ваш телефон | ms-settings:mobile-devices  |

## <a name="ease-of-access"></a>Специальные возможности

|Страница параметров| URI |
|-------------|-----|
| Звук | ms-settings:easeofaccess-audio |
| Скрытые субтитры | ms-settings:easeofaccess-closedcaptioning |
| Цвет фильтры | ms-settings:easeofaccess-colorfilter |
| Размер курсора & указатель | ms-settings:easeofaccess-cursorandpointersize |
| Монитор | ms-settings:easeofaccess-display |
| Управление глазами | ms-settings:easeofaccess-eyecontrol |
| Шрифты | ms-settings:fonts |
| Высокая контрастность | ms-settings:easeofaccess-highcontrast |
| Клавиатура | ms-settings:easeofaccess-keyboard |
| Экранная лупа | ms-settings:easeofaccess-magnifier |
| Мышь | ms-settings:easeofaccess-mouse |
| Экранный диктор | ms-settings:easeofaccess-narrator |
| Другие параметры | MS-параметры: easeofaccess-otheroptions (**устаревшими в Windows 10, версия 1809 и более поздние версии**) |
| Голосовые функции | ms-settings:easeofaccess-speechrecognition |

## <a name="extras"></a>Дополнительно

|Страница параметров| URI |
|-------------|-----|
| Дополнительно | ms-settings:extras (доступно только, если установлены "приложения параметров" (например, сторонние)) |

## <a name="gaming"></a>Игры

|Страница параметров| URI |
|-------------|-----|
| Трансляции | ms-settings:gaming-broadcasting |
| Меню игры | ms-settings:gaming-gamebar |
| DVR для игр | ms-settings:gaming-gamedvr |
| Режим игры | ms-settings:gaming-gamemode |
| Отображение игры в полноэкранном режиме | ms-settings:quietmomentsgame |
| TruePlay | MS-параметры: игры-trueplay (**устаревшими в Windows 10, версия 1809 и более поздние версии**) |
| Сеть на Xbox | ms-settings:gaming-xboxnetworking |

## <a name="home-page"></a>Домашняя страница

|Страница параметров| URI |
|-------------|-----|
| Начальная страница параметров | ms-settings: |

## <a name="mixed-reality"></a>Смешанная реальность

> [!NOTE]
> Эти параметры доступны только в том случае, если установлено приложение портала смешанной реальности.

| Страница параметров | URI |
|---------------|-----|
| Звук и голосовые функции | ms-settings:holographic-audio |
| Среда | MS-параметры: конфиденциальность-holographic-среды |
| Гарнитура отображения | ms-settings:holographic-headset |
| Удалить | ms-settings:holographic-management |

## <a name="network--internet"></a>Сеть и Интернет

|Страница параметров| URI |
|-------------|-----|
| Режим «в самолете» | ms-settings:network-airplanemode<br/>ms-settings:proximity |
| Передача данных и SIM | ms-settings:network-cellular |
| Использование данных | ms-settings:datausage |
| Коммутируемое соединение | ms-settings:network-dialup |
| DirectAccess | ms-settings:network-directaccess (доступно, только если включен DirectAccess) |
| Ethernet | ms-settings:network-ethernet |
| Управление известными сетями | ms-settings:network-wifisettings |
| Мобильный хот-спот | ms-settings:network-mobilehotspot |
| NFC | ms-settings:nfctransactions |
| Proxy (Прокси) | ms-settings:network-proxy |
| Состояние | ms-settings:network-status<br/>ms-settings:network |
| VPN | ms-settings:network-vpn |
| Wi-Fi | ms-settings:network-wifi (доступно, только если на устройстве есть адаптер Wi-Fi) |
| Вызов через Wi-Fi | ms-settings:network-wificalling (доступно, только если возможность вызова через Wi-Fi включена) |

## <a name="personalization"></a>Personalization

|Страница параметров| URI |
|-------------|-----|
| Фон | ms-settings:personalization-background |
| Выберите, какие папки будут отображаться на начальном экране | ms-settings:personalization-start-places |
| Цвета | ms-settings:personalization-colors<br/>ms-settings:colors |
| Заставка | MS-параметры: персонализации-glance (**устаревшими в Windows 10, версия 1809 и более поздние версии**) |
| Экран блокировки | ms-settings:lockscreen |
| Панель навигации | MS-параметры: персонализации-переходов (**устаревшими в Windows 10, версия 1809 и более поздние версии**) |
| Персонализация (категория) | ms-settings:personalization |
| Начало | ms-settings:personalization-start |
| Панель задач | ms-settings:taskbar |
| Темы | ms-settings:themes |

## <a name="phone"></a>Phone

|Страница параметров| URI |
|-------------|-----|
| Ваш телефон | ms-settings:mobile-devices<br/>ms-settings:mobile-devices-addphone<br/>MS-параметры: мобильный-устройств-addphone-direct (открывает **Your Phone** приложения) |

## <a name="privacy"></a>Конфиденциальность

|Страница параметров| URI |
|-------------|-----|
| Приложения-помощники | MS-параметры: конфиденциальность-accessoryapps (**устаревшими в Windows 10, версия 1809 и более поздние версии**) |
| Сведения об учетной записи | ms-settings:privacy-accountinfo |
| Журнал действий | ms-settings:privacy-activityhistory |
| Идентификатор получателя рекламы | MS-параметры: конфиденциальность-advertisingid (**устаревшими в Windows 10, версия 1809 и более поздние версии**) |
| "Диагностика приложений" | ms-settings:privacy-appdiagnostics |
| Автоматически загружаемые файлы | ms-settings:privacy-automaticfiledownloads |
| Фоновые приложения | ms-settings:privacy-backgroundapps |
| Календарь | ms-settings:privacy-calendar |
| Журнал вызовов | ms-settings:privacy-callhistory |
| Камера | ms-settings:privacy-webcam |
| Контакты | ms-settings:privacy-contacts |
| Документы | ms-settings:privacy-documents |
| Электронная почта | ms-settings:privacy-email |
| "Устройство отслеживания взгляда" | ms-settings:privacy-eyetracker (требуется оборудование для отслеживания взгляда) |
| Отзывы и диагностика | ms-settings:privacy-feedback |
| Файловая система | ms-settings:privacy-broadfilesystemaccess |
| Общие | ms-settings:privacy-general |
| Location | ms-settings:privacy-location |
| Messaging | ms-settings:privacy-messaging |
| Микрофон | ms-settings:privacy-microphone |
| Движение | ms-settings:privacy-motion |
| Уведомления | ms-settings:privacy-notifications |
| Другие устройства | ms-settings:privacy-customdevices |
| Изображения | ms-settings:privacy-pictures |
| Телефонные звонки | MS-параметры: конфиденциальность-звонок (**устаревшими в Windows 10, версия 1809 и более поздние версии**) |
| Радиомодули | ms-settings:privacy-radios |
| Голосовые функции, рукописный ввод и ввод с клавиатуры |ms-settings:privacy-speechtyping |
| Задачи | ms-settings:privacy-tasks |
| Видео | ms-settings:privacy-videos |
| Активация голоса | ms-settings:privacy-voiceactivation |

## <a name="surface-hub"></a>Surface Hub

|Страница параметров| URI |
|-------------|-----|
| Учетные записи | ms-settings:surfacehub-accounts |
| Очистка сеанса | ms-settings:surfacehub-sessioncleanup |
| Групповые конференции | ms-settings:surfacehub-calling |
| Управление устройствами группы | ms-settings:surfacehub-devicemanagenent |
| Экран приветствия | ms-settings:surfacehub-welcome |

## <a name="system"></a>Система

|Страница параметров| URI |
|-------------|-----|
| Описание | ms-settings:about |
| Дополнительные параметры экрана | ms-settings:display-advanced (доступно только на устройствах, поддерживающих расширенные параметры отображения) |
| Приложения установки для тома и устройства | MS-параметры: приложения-том |
| Экономия заряда аккумулятора | ms-settings:batterysaver (доступно только на устройствах с аккумулятором, таких как планшет) |
| Параметры экономии заряда | ms-settings:batterysaver-settings (доступно только на устройствах с аккумулятором, таких как планшет) |
| Использование аккумулятора | ms-settings:batterysaver-usagedetails (доступно только на устройствах с аккумулятором, таких как планшет) |
| Буфер обмена | ms-settings:clipboard |
| Монитор | ms-settings:display |
| Места сохранения по умолчанию | ms-settings:savelocations |
| Монитор | ms-settings:screenrotation |
| Дублирование дисплея | ms-settings:quietmomentspresentation |
| В эти часы | ms-settings:quietmomentsscheduled |
| Шифрование | ms-settings:deviceencryption |
| Фокусировка внимания | ms-settings:quiethours <br> ms-settings:quietmomentshome |
| Параметры графики | ms-settings:display-advancedgraphics (доступно только на устройствах, поддерживающих расширенные параметры графики) |
| Messaging | ms-settings:messaging |
| Многозадачность | ms-settings:multitasking |
| Параметры ночного света | ms-settings:nightlight |
| Phone | ms-settings:phone-defaultapps |
| Проецирование на этот компьютер | ms-settings:project |
| Общие возможности | ms-settings:crossdevice |
| Режим планшета | ms-settings:tabletmode |
| Панель задач | ms-settings:taskbar |
| Уведомления и действия | ms-settings:notifications |
| Удаленный рабочий стол | ms-settings:remotedesktop |
| Phone | MS-параметры: phone (**устаревшими в Windows 10, версия 1809 и более поздние версии**) |
| Питание и спящий режим | ms-settings:powersleep |
| Звук | ms-settings:sound |
| Хранилище | ms-settings:storagesense |
| Контроль памяти | ms-settings:storagepolicies |

## <a name="time-and-language"></a>Время и язык

|Страница параметров| URI |
|-------------|-----|
| Дата и время | ms-settings:dateandtime |
| Параметры IME для Японии | ms-settings:regionlanguage-jpnime (доступно, если установлен редактор метода ввода Microsoft для Японии) |
| Язык | MS-параметры: клавиатуры<br/>ms-settings:regionlanguage<br/>ms-settings:regionlanguage-bpmfime<br/>ms-settings:regionlanguage-cangjieime<br/>MS-параметры: regionlanguage-chsime-pinyin-domainlexicon<br/>ms-settings:regionlanguage-chsime-pinyin-keyconfig<br/>ms-settings:regionlanguage-chsime-pinyin-udp<br/>ms-settings:regionlanguage-chsime-wubi-udp<br/>ms-settings:regionlanguage-quickime |
| Параметры IME пиньинь | ms-settings:regionlanguage-chsime-pinyin (доступно, если установлен редактор метода ввода Microsoft Pinyin) |
| Голосовые функции | ms-settings:speech |
| Параметры IME Wubi  | ms-settings:regionlanguage-chsime-wubi (доступно, если установлен редактор метода ввода Microsoft Wubi) |

## <a name="update--security"></a>Обновление и безопасность

|Страница параметров| URI |
|-------------|-----|
| Активация | ms-settings:activation |
| Резервное копирование | ms-settings:backup |
| Оптимизация доставки | ms-settings:delivery-optimization |
| Поиск устройства | ms-settings:findmydevice |
| Для разработчиков | ms-settings:developers |
| Восстановление | ms-settings:recovery |
| Устранение неполадок | ms-settings:troubleshoot |
| "Безопасность Windows" | ms-settings:windowsdefender |
| Программа предварительной оценки Windows | ms-settings:windowsinsider (доступно, только если пользователь зарегистрирован в WIP)<br/>ms-settings:windowsinsider-optin |
| Центр обновления Windows | ms-settings:windowsupdate<br>ms-settings:windowsupdate-action |
| Параметры Windows Update-Advanced | ms-settings:windowsupdate-options |
| Параметры Windows Update-Restart | ms-settings:windowsupdate-restartoptions |
| Журнал обновлений Windows Update-View | ms-settings:windowsupdate-history |

## <a name="user--accounts"></a>Ученые записи пользователей

|Страница параметров| URI |
|-------------|-----|
| Подготовка | ms-settings:workplace-provisioning (доступно, только если на предприятии развернут пакет подготовки) |
| Подготовка | ms-settings:provisioning (доступно только на мобильных устройствах и только если на предприятии развернут пакет подготовки) |
| Windows Anywhere | ms-settings:windowsanywhere (устройство должно поддерживать Windows Anywhere) |
