---
title: Пополнение документации Windows в августе 2017 г. — разработка приложений UWP
description: В августе 2017 г документация для разработчиков Windows 10 пополнилась описанием новых возможностей, видеоматериалами и руководствами для разработчиков.
keywords: новые возможности, обновления, компоненты, руководство разработчика, Windows 10, 1708
ms.date: 08/03/2017
ms.topic: article
ms.localizationpriority: medium
ms.openlocfilehash: 824cdb74a809ae3f9ae2ed202a9889535021f711
ms.sourcegitcommit: 26bb75084b9d2d2b4a76d4aa131066e8da716679
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/06/2020
ms.locfileid: "75684724"
---
# <a name="whats-new-in-the-windows-developer-docs-in-august-2017"></a>Пополнение документации Windows в августе 2017 г.

Документация для разработчиков Windows постоянно обновляется и дополняется информацией о новых функциях, доступных разработчикам на платформе Windows. Недавно были опубликованы следующие обзоры функций, руководства для разработчиков и видеоматериалы с новыми и обновленными сведениями для разработчиков Windows.

[Установив средства и пакет SDK](https://developer.microsoft.com/windows/downloads#_blank) в Windows 10, вы можете сразу приступить к [созданию нового проекта универсального приложения для Windows](../get-started/your-first-app.md) или использованию [существующего кода приложения в Windows](../porting/index.md).

## <a name="features"></a>Возможности

### <a name="windows-template-studio"></a>Windows Template Studio

Используйте новое расширение [Windows Template Studio](https://marketplace.visualstudio.com/items?itemName=WASTeamAccount.WindowsTemplateStudio) для Visual Studio 2019, чтобы быстро создать приложение UWP с нужными страницами, платформой и функциями. Расширение, выполняющее функции мастера, с помощью проверенных способов существенно упрощает и ускоряет добавление функциональных возможностей в приложение.

![Windows Template Studio](images/template-studio.png)

### <a name="conditional-xaml"></a>Условный код XAML

Теперь можно выполнять предварительный просмотр [условного XAML](../debug-test-perf/conditional-xaml.md) для создания [адаптивных к версии приложений](../debug-test-perf/version-adaptive-apps.md). Условный XAML позволяет использовать метод ApiInformation.IsApiContractPresent в разметке XAML, чтобы настраивать свойства и создавать экземпляры объектов в разметке в зависимости от наличия API, без использования кода программной части.

### <a name="game-mode"></a>Режим игры

API-интерфейсы [режима игры](https://docs.microsoft.com/previous-versions/windows/desktop/gamemode/game-mode-portal) для универсальной платформы Windows (UWP) позволяют создавать оптимизированные игровые среды, используя режим игры в Windows 10. Эти API находятся в заголовке **&lt;expandedresources.h&gt;** .

![Режим игры](images/game-mode.png)

### <a name="submission-api-supports-video-trailers-and-gaming-options"></a>API отправки поддерживают видеоанонсы и параметры игры

[API отправки в Microsoft Store](../monetize/create-and-manage-submissions-using-windows-store-services.md) теперь позволяет включить [видеоанонсы](../monetize/manage-app-submissions.md#trailer-object) и [параметры игры](../monetize/manage-app-submissions.md#gaming-options-object) в отправки приложений.


## <a name="developer-guidance"></a>Руководство для разработчиков

### <a name="data-schemas-for-store-products"></a>Схемы данных для продуктов Store

Мы добавили статью о [схемах данных для продуктов Microsoft Store](../monetize/data-schemas-for-store-products.md). В этой статье вы найдете схемы данных, связанных с Microsoft Store, которые доступны для нескольких объектов в пространстве имен [Windows.Services.Store](https://docs.microsoft.com/uwp/api/windows.services.store), включая [StoreProduct](https://docs.microsoft.com/uwp/api/windows.services.store.storeproduct) и [StoreAppLicense](https://docs.microsoft.com/uwp/api/windows.services.store.storeapplicense).

### <a name="desktop-bridge"></a>Мост для классических приложений

Появилось два руководства, которые помогут вам расширить возможности пользователей Windows 10.

В разделе об [улучшении классических приложений для Windows 10](https://docs.microsoft.com/windows/uwp/porting/desktop-to-uwp-enhance) вы найдете нужные файлы, сможете воспользоваться ими и написать код, оптимизирующий приложения UWP для пользователей Windows 10.  

В разделе о [добавлении современных компонентов UWP в классические приложения](https://docs.microsoft.com/windows/uwp/porting/desktop-to-uwp-extend) вы найдете информацию по добавлению современных элементов пользовательского интерфейса XAML и прочих элементов UWP, которые должны выполняться в контейнере приложений UWP.

### <a name="getting-started-with-point-of-service"></a>Начало работы с точкой обслуживания

Добавлено новое руководство, которое поможет вам [приступить к работе с устройствами точки обслуживания](https://docs.microsoft.com/windows/uwp/devices-sensors/pos-get-started). В руководстве рассматриваются такие вопросы, как перечисление устройств, проверка возможностей устройств, объявление устройств и предоставление общего доступа к устройствам. 

### <a name="xbox-live"></a>Xbox Live

Мы добавили документацию для разработчиков Xbox Live для игр UWP и XDK (комплект средств для разработчика Xbox).

Из [руководства для разработчиков Xbox Live](https://docs.microsoft.com//gaming/xbox-live/index) вы узнаете, как использовать API Xbox Live для подключения игры к социальной игровой сети Xbox Live.

С помощью [программы Xbox Live Creators Program](https://docs.microsoft.com//gaming/xbox-live/get-started-with-creators/get-started-with-xbox-live-creators) любой разработчик игр UWP сможет разработать и опубликовать игру с поддержкой Xbox Live как для ПК, так и для Xbox One.

См. сведения о программах и функциях, доступных разработчикам Xbox Live, в разделе с [обзором программ для разработчиков Xbox Live](https://docs.microsoft.com//gaming/xbox-live/developer-program-overview).

## <a name="videos"></a>Видео

### <a name="mixed-reality"></a>Смешанная реальность

Выпущен ряд новых обучающих видео для [курса № 250 по Microsoft HoloLens](https://developer.microsoft.com/windows/mixed-reality/mixed_reality_250). Если вы уже установили инструменты и знакомы с основами разработки для смешанной реальности, изучите эти видеокурсы, в которых вы найдете информацию о создании общих сценариев для разных устройств смешанной реальности.

### <a name="narrator-and-dev-mode"></a>Экранный диктор и режима разработчика

Должно быть вы знаете, что [Экранный диктор](https://support.microsoft.com/help/22798/windows-10-complete-guide-to-narrator) можно использовать для тестирования функции чтения с экрана в разработанном вами приложении. Экранный диктор также поддерживает режим разработчика, дающий хорошее визуальное представление о сведениях, переданных ему. [Посмотрите видеоролик](https://channel9.msdn.com/Blogs/One-Dev-Minute/Using-Narrator-and-Dev-Mode), чтобы больше узнать о [режиме разработчика Экранного диктора](https://channel9.msdn.com/Blogs/One-Dev-Minute/Using-Narrator-and-Dev-Mode).

### <a name="windows-template-studio"></a>Windows Template Studio

В [этом видео](https://channel9.msdn.com/Blogs/One-Dev-Minute/Getting-Started-with-Windows-Template-Studio) дан более обстоятельный обзор Windows Template Studio. Когда решите приступить к работе, [установите расширение](https://marketplace.visualstudio.com/items?itemName=WASTeamAccount.WindowsTemplateStudio) или [просмотрите исходный код и документацию](https://marketplace.visualstudio.com/items?itemName=WASTeamAccount.WindowsTemplateStudio).