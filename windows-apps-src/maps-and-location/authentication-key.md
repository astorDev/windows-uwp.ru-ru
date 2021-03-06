---
title: Запрос ключа проверки подлинности карт
description: Ваше универсальное приложение для Windows должно пройти проверку подлинности, прежде чем оно сможет использовать MapControl и службы карт в пространстве имен Windows.Services.Maps.
ms.assetid: 13B400D7-E13F-4F07-ACC3-9C34087F0F73
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp, ключ проверки подлинности карт, элемент управления картой
ms.localizationpriority: medium
ms.openlocfilehash: 2f4a76edfe5772665564cb8890ffcdf56205a2f7
ms.sourcegitcommit: d1eba7cf79cd2885b5bf8f5501bc44a569ab9864
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/29/2020
ms.locfileid: "84172596"
---
# <a name="request-a-maps-authentication-key"></a>Запрос ключа проверки подлинности карт

> [!WARNING]
> Службы онлайн Maps могут быть недоступны в более старых версиях Windows 10. В следующих версиях MapControl может больше не отображать карты и интерфейсы API в пространстве имен Windows. Services. Maps может не возвращать результаты:
> - Windows 10, версия 1607 и более ранние версии: службы карт будут недоступны по всему миру начиная с 2020 октября
> - Windows 10, версия 1703 и более ранние версии: службы Map недоступны на [некоторых устройствах, продаваемых в Китае](https://docs.microsoft.com/windows-hardware/customize/desktop/unattend/microsoft-windows-mapcontrol-desktop-chinavariantwin10)

Ваше [приложение Universal Windows](https://docs.microsoft.com/windows/uwp/get-started/universal-application-platform-guide) должно пройти проверку подлинности, прежде чем оно сможет использовать [**MapControl**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Maps.MapControl) и службы карт в пространстве имен [**Windows.Services.Maps**](https://docs.microsoft.com/uwp/api/Windows.Services.Maps). Для проверки подлинности приложения необходимо указать ключ проверки подлинности карт. В этом разделе описано, как запросить ключ проверки подлинности карт из [центра разработки Карт Bing](https://www.bingmapsportal.com/) и добавить его в приложение.

**Подсказка.** Чтобы получить дополнительные сведения об использовании карт в приложении, скачайте следующий пример из [репозитория Windows-universal-samples](https://github.com/Microsoft/Windows-universal-samples) на веб-сайте GitHub:

-   [Пример карты универсальной платформы Windows (UWP)](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/MapControl)

## <a name="get-a-key"></a>Получение ключа


Создавайте ключи проверки подлинности карт для своих универсальных приложений для Windows и управляйте ими с помощью [центра разработки Карт Bing](https://www.bingmapsportal.com/).

Вот как создать новый ключ

1.  В браузере перейдите в центр разработчиков карт Bing ( [https://www.bingmapsportal.com](https://www.bingmapsportal.com/) ).

2.  Если появится запрос на вход, введите свою учетную запись Майкрософт и щелкните **Войти**.

3.  Выберите учетную запись, которую нужно связать с учетной записью Карт Bing. Если вы хотите использовать учетную запись Майкрософт, щелкните **Да**. В противном случае выберите **Войти под другой учетной записью**.

4.  Если у вас еще нет учетной записи Карт Bing, создайте новую учетную запись. Введите **Имя учетной записи**, **Имя контакта**, **Название организации**, **Адрес электронной почты** и **Номер телефона**. Приняв условия использования, щелкните **Создать**.

5.  В меню **Моя учетная запись** щелкните **Мои ключи**.

6.  Если у вас есть ранее созданный ключ, щелкните ссылку, чтобы создать новый ключ. В противном случае перейдите к форме "Создание ключа".

7.  Заполните форму **Создание ключа**, а затем щелкните **Создать**.

    -   **Имя приложения:** имя вашего приложения.
    -   **URL-адрес приложения (необязательно):** URL-адрес вашего приложения.
    -   **Тип ключа:** выберите **Базовый** или **Корпоративный**.
    -   **Тип приложения:** Выберите **приложение Windows** для использования в универсальном приложении Windows.

    Ниже приведен пример формы.

    ![пример формы создания ключей.](images/createkeydialog.png)

8.  После нажатия кнопки **Создать** под формой **Создание ключа** появится новый ключ. Скопируйте его в безопасное место или сразу добавьте в свое приложение, как описано в следующем шаге.

## <a name="add-the-key-to-your-app"></a>Добавление ключа в приложение


Ключ проверки подлинности карт требуется для использования [**MapControl**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Maps.MapControl) и служб карт ([**Windows.Services.Maps**](https://docs.microsoft.com/uwp/api/Windows.Services.Maps)) в универсальном приложении для Windows. Добавляйте его в объекты элемента управления картой и служб карт по обстоятельствам.

### <a name="to-add-the-key-to-a-map-control"></a>Вот как добавить ключ в элемент управления картой

Для проверки подлинности [**MapControl**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Maps.MapControl) установите свойство [**MapServiceToken**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.maps.mapcontrol.mapservicetoken) для значения ключа проверки подлинности. Это свойство можно задать в коде или в разметке XAML, в зависимости от ваших предпочтений. Дополнительные сведения об использовании **MapControl** см. в статье [Отображение карт с помощью двумерных и трехмерных представлений, а также с помощью представлений Streetside](display-maps.md).

-   В этом примере для **MapServiceToken** устанавливается значение ключа проверки подлинности в коде.

    ```cs
    MapControl1.MapServiceToken = "abcdef-abcdefghijklmno";
    ```

-   В этом примере для **MapServiceToken** устанавливается значение ключа проверки подлинности в разметке XAML.

    ```xml
    <Maps:MapControl x:Name="MapControl1" MapServiceToken="abcdef-abcdefghijklmno"/>
    ```

### <a name="to-add-the-key-to-map-services"></a>Вот как добавить ключ в службы карт

Для использования служб в пространстве имен [**Windows.Services.Maps**](https://docs.microsoft.com/uwp/api/Windows.Services.Maps) установите для свойства [**ServiceToken**](https://docs.microsoft.com/uwp/api/windows.services.maps.mapservice.servicetoken) значение ключа проверки подлинности. Дополнительные сведения об использовании служб карт см.: в разделах [Отображение дорог и направлений](routes-and-directions.md) и [Выполнение геокодирования и обратного геокодирования](geocoding.md).

-   В этом примере для **ServiceToken** устанавливается значение ключа проверки подлинности в коде.

    ```cs
    MapService.ServiceToken = "abcdef-abcdefghijklmno";
    ```

## <a name="related-topics"></a>Связанные темы

* [Bing Maps Developer Center](https://www.bingmapsportal.com/)
* [Пример карты UWP](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/MapControl)
* [Руководство по разработке карт](https://docs.microsoft.com/windows/uwp/maps-and-location/controls-map)
* [Видео c конференции Build 2015: использование карт и местоположений в приложениях для Windows на телефонах, планшетах и ПК](https://channel9.msdn.com/Events/Build/2015/2-757)
* [Пример приложения UWP для работы с трафиком](https://github.com/Microsoft/Windows-appsample-trafficapp)
