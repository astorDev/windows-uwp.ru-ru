---
title: Запуск приложения "Параметры" для Windows
description: Узнайте, как запустить приложение "Параметры" для Windows из вашего приложения. В этом разделе приводится описание схемы URI ms-settings. Используйте эту схему URI для запуска приложения "Параметры" для Windows на определенных страницах параметров.
ms.assetid: C84D4BEE-1FEE-4648-AD7D-8321EAC70290
ms.date: 04/19/2019
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.custom: 19H1
dev_langs:
- csharp
- cppwinrt
ms.openlocfilehash: 1ef32816d04516bf4c8ce8677d7f682d2d59f657
ms.sourcegitcommit: db48036af630f33f0a2f7a908bfdfec945f3c241
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/05/2020
ms.locfileid: "84437175"
---
# <a name="launch-the-windows-settings-app"></a>Запуск приложения "Параметры" для Windows

**Важные API**

-   [**LaunchUriAsync**](https://docs.microsoft.com/uwp/api/windows.system.launcher.launchuriasync)
-   [**PreferredApplicationPackageFamilyName**](https://docs.microsoft.com/uwp/api/windows.system.launcheroptions.preferredapplicationpackagefamilyname)
-   [**DesiredRemainingView**](https://docs.microsoft.com/uwp/api/windows.system.launcheroptions.desiredremainingview)

Узнайте, как запустить приложение «Параметры» для Windows. В этом разделе описывается схема **MS-Settings:** URI. Используйте эту схему URI для запуска приложения "Параметры" для Windows на определенных страницах параметров.

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
```cppwinrt
bool result = co_await Windows::System::Launcher::LaunchUriAsync(Windows::Foundation::Uri(L"ms-settings:privacy-webcam"));
```

Приведенный выше код запускает страницу параметров конфиденциальности для камеры:

![Параметры конфиденциальности камеры.](images/privacyawarenesssettingsapp.png)

Дополнительные сведения о запуске URI см. в разделе [Запуск приложения по умолчанию для URI](launch-default-app.md).

## <a name="ms-settings-uri-scheme-reference"></a>Справка по схеме URI ms-settings:

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

|Страница «Параметры»| URI |
|-------------|-----|
| Доступ к учетной записи места работы или учебного заведения | ms-settings:workplace |
| Адрес электронной почты; учетные записи приложений  | ms-settings:emailandaccounts |
| Семья и другие пользователи | ms-settings:otherusers |
| Настройка киоска | MS-Settings: ассигнедакцесс |
| Параметры входа | ms-settings:signinoptions<br>ms-settings:signinoptions-dynamiclock |
| Синхронизация параметров | ms-settings:sync |
| Настройка функции Windows Hello | ms-settings:signinoptions-launchfaceenrollment<br>ms-settings:signinoptions-launchfingerprintenrollment |
| Ваши данные | ms-settings:yourinfo |

## <a name="apps"></a>Приложения

|Страница «Параметры»| URI |
|-------------|-----|
| Приложения и возможности | ms-settings:appsfeatures |
| Возможности приложения | ms-settings:appsfeatures-app (сброс, управление надстройками и скачиваемым контентом и т. п. для приложения)|
| Приложения для веб-сайтов | ms-settings:appsforwebsites |
| Приложения по умолчанию | ms-settings:defaultapps |
| Управление дополнительными возможностями | ms-settings:optionalfeatures |
| Карты для автономного использования | ms-settings:maps<br/>MS-Settings: Maps-довнлоадмапс (карты загрузки) |
| Автозагружаемые приложения | ms-settings:startupapps |
| Воспроизведение видео | ms-settings:videoplayback |

## <a name="cortana"></a>Кортана

|Страница «Параметры»| URI |
|-------------|-----|
| Кортана на моих устройствах | ms-settings:cortana-notifications |
| Дополнительные сведения | ms-settings:cortana-moredetails |
| Журнал & разрешений | ms-settings:cortana-permissions |
| Поиск в окнах | MS-Settings: Кортана — виндовссеарч |
| Общение с Кортаной | ms-settings:cortana-language<br/>MS-Settings: Кортана<br/>MS-Settings: Кортана — талктокортана |

> [!NOTE] 
> Этот раздел параметров на рабочем столе будет называться Поиск, если на компьютере настроены регионы, в которых Кортана в настоящее время недоступна или Кортана отключена. В этом случае не будут перечислены страницы, относящиеся к Кортане (Кортана на моих устройствах и обращение к Кортане). 

## <a name="devices"></a>Устройства

|Страница «Параметры»| URI |
|-------------|-----|
| Автозапуск | ms-settings:autoplay |
| Bluetooth | ms-settings:bluetooth |
| Подключенные устройства | ms-settings:connecteddevices |
| Камера по умолчанию | MS-Settings: Camera (**не рекомендуется в Windows 10, версия 1809 и более поздние**) |
| Мышь и сенсорная панель | ms-settings:mousetouchpad (параметры сенсорной панели доступны только на устройствах с сенсорной панелью) |
| Перо и Windows Ink | ms-settings:pen |
| Принтеры и сканеры | ms-settings:printers |
| Touchpad | ms-settings:devices-touchpad (доступно только на устройствах с сенсорной панелью) |
| Ввод с клавиатуры | ms-settings:typing |
| USB | ms-settings:usb |
| Wheel | ms-settings:wheel (доступно, только если устройство Dial связано) |
| Ваш телефон | ms-settings:mobile-devices  |

## <a name="ease-of-access"></a>Простота доступа

|Страница «Параметры»| URI |
|-------------|-----|
| звук; | ms-settings:easeofaccess-audio |
| Скрытые субтитры | ms-settings:easeofaccess-closedcaptioning |
| Цветовые фильтры | MS-Settings: еасеофакцесс-колорфилтер |
| Размеры курсора и указателя | MS-Settings: еасеофакцесс-курсорандпоинтерсизе |
| Отображение | ms-settings:easeofaccess-display |
| Управление глазами | ms-settings:easeofaccess-eyecontrol |
| Fonts | ms-settings:fonts |
| Высокая контрастность | ms-settings:easeofaccess-highcontrast |
| Keyboard (Клавиатура) | ms-settings:easeofaccess-keyboard |
| Лупа | ms-settings:easeofaccess-magnifier |
| Мышь | ms-settings:easeofaccess-mouse |
| Диктор | ms-settings:easeofaccess-narrator |
| Другие варианты | MS-Settings: еасеофакцесс-осероптионс (**не рекомендуется в Windows 10, версия 1809 и более поздние**) |
| Речь | ms-settings:easeofaccess-speechrecognition |

## <a name="extras"></a>Дополнительно

|Страница «Параметры»| URI |
|-------------|-----|
| Дополнительно | MS-Settings: дополнительные компоненты (доступны только в том случае, если установлены приложения для настройки, например сторонними производителями). |

## <a name="gaming"></a>Игры

|Страница «Параметры»| URI |
|-------------|-----|
| Трансляции | ms-settings:gaming-broadcasting |
| Меню игры | ms-settings:gaming-gamebar |
| DVR для игр | ms-settings:gaming-gamedvr |
| Режим игры | ms-settings:gaming-gamemode |
| Отображение игры в полноэкранном режиме | ms-settings:quietmomentsgame |
| TruePlay | MS-Settings: Gaming-труеплай (**не рекомендуется в Windows 10, версия 1809 и более поздние**) |
| Сеть на Xbox | ms-settings:gaming-xboxnetworking |

## <a name="home-page"></a>Главная страница

|Страница «Параметры»| URI |
|-------------|-----|
| Начальная страница параметров | ms-settings: |

## <a name="mixed-reality"></a>Смешанная реальность

> [!NOTE]
> Эти параметры доступны только в том случае, если установлено приложение портала Mixed Reality.

| Страница «Параметры» | URI |
|---------------|-----|
| Звук и голосовые функции | ms-settings:holographic-audio |
| Среда | MS-Settings: privacy-holographic-среда |
| Отображение гарнитуры | MS-Settings: holographic-головной телефон |
| Удаление | MS-Settings: holographic-управление |

## <a name="network--internet"></a>Сеть и Интернет

|Страница «Параметры»| URI |
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
| Прокси | ms-settings:network-proxy |
| Состояние | ms-settings:network-status<br/>MS-Settings: сеть |
| Виртуальная частная сеть | ms-settings:network-vpn |
| Wi-Fi | ms-settings:network-wifi (доступно, только если на устройстве есть адаптер Wi-Fi) |
| Вызов через Wi-Fi | ms-settings:network-wificalling (доступно, только если возможность вызова через Wi-Fi включена) |

## <a name="personalization"></a>Personalization

|Страница «Параметры»| URI |
|-------------|-----|
| Историческая справка | ms-settings:personalization-background |
| Выберите, какие папки будут отображаться на начальном экране | ms-settings:personalization-start-places |
| Цвета | ms-settings:personalization-colors<br/>MS-Settings: цвета |
| Заставка | MS-Settings: Персонализация — краткий обзор (**не рекомендуется в Windows 10, версия 1809 и более поздние**) |
| Экран блокировки | ms-settings:lockscreen |
| Панель переходов | MS-Settings: Персонализация — панель навигации (**не рекомендуется в Windows 10, версия 1809 и более поздние**) |
| Персонализация (категория) | ms-settings:personalization |
| Начать | ms-settings:personalization-start |
| Панель задач | ms-settings:taskbar |
| Темы | ms-settings:themes |

## <a name="phone"></a>Телефон

|Страница «Параметры»| URI |
|-------------|-----|
| Ваш телефон | ms-settings:mobile-devices<br/>MS-Settings: Mobile-Devices-аддфоне<br/>MS-Settings: Mobile-Devices-аддфоне-Direct (открывает приложение для **телефона** ) |

## <a name="privacy"></a>Конфиденциальность

|Страница «Параметры»| URI |
|-------------|-----|
| Приложения-помощники | MS-Settings: privacy-акцессоряппс (**не рекомендуется в Windows 10, версия 1809 и более поздние**) |
| Сведения об учетной записи | ms-settings:privacy-accountinfo |
| Журнал действий | ms-settings:privacy-activityhistory |
| Идентификатор получателя рекламы | MS-Settings: privacy-адвертисингид (**не рекомендуется в Windows 10, версия 1809 и более поздние**) |
| Диагностика приложений | ms-settings:privacy-appdiagnostics |
| Автоматически загружаемые файлы | ms-settings:privacy-automaticfiledownloads |
| Фоновые приложения | ms-settings:privacy-backgroundapps |
| Календарь | ms-settings:privacy-calendar |
| Журнал вызовов | ms-settings:privacy-callhistory |
| Камера | ms-settings:privacy-webcam |
| Контакты | ms-settings:privacy-contacts |
| Документы | ms-settings:privacy-documents |
| Адрес электронной почты | ms-settings:privacy-email |
| Устройство отслеживания взгляда | ms-settings:privacy-eyetracker (требуется оборудование для отслеживания взгляда) |
| Отзывы и диагностика | ms-settings:privacy-feedback |
| Файловая система | ms-settings:privacy-broadfilesystemaccess |
| Общие сведения | MS-Settings: privacy или MS-Settings: privacy-General |
| Ввод рукописного ввода & |ms-settings:privacy-speechtyping |
| Расположение | ms-settings:privacy-location |
| Обмен сообщениями | ms-settings:privacy-messaging |
| Микрофон | ms-settings:privacy-microphone |
| Движение | ms-settings:privacy-motion |
| Уведомления | ms-settings:privacy-notifications |
| Другие устройства | ms-settings:privacy-customdevices |
| Телефонные звонки | MS-Settings: privacy-фонекаллс |
| Изображения | ms-settings:privacy-pictures |
| Радиомодули | ms-settings:privacy-radios |
| Речь | MS-Settings: конфиденциальность — речь |
| Задания | ms-settings:privacy-tasks |
| Видеоролики | ms-settings:privacy-videos |
| Активация голоса | MS-Settings: privacy-воицеактиватион |

## <a name="surface-hub"></a>Surface Hub

|Страница «Параметры»| URI |
|-------------|-----|
| Учетные записи | ms-settings:surfacehub-accounts |
| Очистка сеанса | ms-settings:surfacehub-sessioncleanup |
| Групповые конференции | ms-settings:surfacehub-calling |
| Управление устройствами группы | ms-settings:surfacehub-devicemanagenent |
| Экран приветствия | ms-settings:surfacehub-welcome |

## <a name="system"></a>Система

|Страница «Параметры»| URI |
|-------------|-----|
| Сведения | ms-settings:about |
| Дополнительные параметры экрана | ms-settings:display-advanced (доступно только на устройствах, поддерживающих расширенные параметры отображения) |
| Параметры тома и устройства приложения | MS-Settings: Apps — том (**добавлен в Windows 10, версия 1903**)|
| Экономия заряда аккумулятора | ms-settings:batterysaver (доступно только на устройствах с аккумулятором, таких как планшет) |
| Параметры экономии заряда | ms-settings:batterysaver-settings (доступно только на устройствах с аккумулятором, таких как планшет) |
| Использование аккумулятора | ms-settings:batterysaver-usagedetails (доступно только на устройствах с аккумулятором, таких как планшет) |
| буфер обмена | MS-Settings: буфер обмена |
| Отображение | ms-settings:display |
| Места сохранения по умолчанию | ms-settings:savelocations |
| Отображение | ms-settings:screenrotation |
| Дублирование дисплея | ms-settings:quietmomentspresentation |
| В эти часы | ms-settings:quietmomentsscheduled |
| Шифрование | ms-settings:deviceencryption |
| Фокусировка внимания | ms-settings:quiethours <br> ms-settings:quietmomentshome |
| Параметры графики | ms-settings:display-advancedgraphics (доступно только на устройствах, поддерживающих расширенные параметры графики) |
| Обмен сообщениями | ms-settings:messaging |
| Многозадачность | ms-settings:multitasking |
| Параметры ночного света | ms-settings:nightlight |
| Телефон | ms-settings:phone-defaultapps |
| Проецирование на этот компьютер | ms-settings:project |
| Общие возможности | ms-settings:crossdevice |
| Режим планшета | ms-settings:tabletmode |
| Панель задач | ms-settings:taskbar |
| Уведомления и действия | ms-settings:notifications |
| Удаленный рабочий стол | ms-settings:remotedesktop |
| Телефон | MS-Settings: Phone (**не рекомендуется в Windows 10, версия 1809 и более поздние**) |
| Питание и спящий режим | ms-settings:powersleep |
| Звук | MS-Settings: звук |
| Память | ms-settings:storagesense |
| Контроль памяти | ms-settings:storagepolicies |

## <a name="time-and-language"></a>Время и язык

|Страница «Параметры»| URI |
|-------------|-----|
| Дата и время | ms-settings:dateandtime |
| Параметры IME для Японии | ms-settings:regionlanguage-jpnime (доступно, если установлен редактор метода ввода Microsoft для Японии) |
| Регион | MS-Settings: регионформаттинг |
| Язык | MS-Settings: клавиатура<br/>ms-settings:regionlanguage<br/>MS-Settings: регионлангуаже-бпмфиме<br/>MS-Settings: регионлангуаже-кангжиеиме<br/>MS-Settings: регионлангуаже-чсиме-пиньинь-домаинлексикон<br/>MS-Settings: регионлангуаже-чсиме-пиньинь-кэйконфиг<br/>MS-Settings: регионлангуаже-чсиме-пиньинь-UDP<br/>MS-Settings: регионлангуаже-чсиме-Wubi-UDP<br/>MS-Settings: регионлангуаже-куиккиме |
| Параметры IME пиньинь | ms-settings:regionlanguage-chsime-pinyin (доступно, если установлен редактор метода ввода Microsoft Pinyin) |
| Речь | ms-settings:speech |
| Параметры IME Wubi  | ms-settings:regionlanguage-chsime-wubi (доступно, если установлен редактор метода ввода Microsoft Wubi) |

## <a name="update--security"></a>Обновление и безопасность

|Страница «Параметры»| URI |
|-------------|-----|
| Активация | ms-settings:activation |
| Backup | ms-settings:backup |
| Оптимизация доставки | ms-settings:delivery-optimization |
| Поиск устройства | ms-settings:findmydevice |
| Для разработчиков | ms-settings:developers |
| Модель | ms-settings:recovery |
| Диагностика | ms-settings:troubleshoot |
| Безопасность Windows | ms-settings:windowsdefender |
| Программа предварительной оценки Windows | ms-settings:windowsinsider (доступно, только если пользователь зарегистрирован в WIP)<br/>MS-Settings: виндовсинсидер-OptIn |
| Центра обновления Windows; | ms-settings:windowsupdate<br>ms-settings:windowsupdate-action |
| Параметры Windows Update-Advanced | ms-settings:windowsupdate-options |
| Параметры Windows Update-Restart | ms-settings:windowsupdate-restartoptions |
| Журнал обновлений Windows Update-View | ms-settings:windowsupdate-history |

## <a name="user-accounts"></a>Учетные записи пользователей

|Страница «Параметры»| URI |
|-------------|-----|
| Подготовка | ms-settings:workplace-provisioning (доступно, только если на предприятии развернут пакет подготовки) |
| Подготовка | ms-settings:provisioning (доступно только на мобильных устройствах и только если на предприятии развернут пакет подготовки) |
| Windows Anywhere | ms-settings:windowsanywhere (устройство должно поддерживать Windows Anywhere) |
