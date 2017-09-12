---
author: QuinnRadich
title: "Что нового в документации Windows в августе 2017г.— разработка приложений UWP"
description: "Новые возможности, видео и руководства для разработчиков добавлены в документацию для разработчиков Windows 10 в августе 2017г"
keywords: "новые возможности, обновления, компоненты, руководство разработчика, Windows 10, 1708"
ms.author: quradic
ms.date: 08/03/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
redirect_url: windows-docs-latest
ms.openlocfilehash: 43eebdc73f626034d9aaea12d18bdb6ce8657d90
ms.sourcegitcommit: 6c6f3c265498d7651fcc4081c04c41fafcbaa5e7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/09/2017
---
# <a name="whats-new-in-the-windows-developer-docs-in-august-2017"></a>Что нового в документации для разработчиков Windows в августе 2017г.

Документация для разработчиков Windows непрерывно обновляется и дополняется информацией о новых функциях, доступных разработчикам на платформе Windows. Недавно стали доступны следующие обзоры функций, руководства для разработчиков и видео, содержащие новые и обновленные сведения для разработчиков Windows.

[Установив средства и пакет SDK](http://go.microsoft.com/fwlink/?LinkId=821431) в Windows 10, вы можете сразу приступить либо к [созданию нового проекта универсального приложения для Windows](../get-started/your-first-app.md), либо к использованию [существующего кода приложения в ОС Windows](../porting/index.md).

## <a name="features"></a>Возможности

### <a name="windows-template-studio"></a>Windows Template Studio

Используйте новое расширение [Windows Template Studio](https://aka.ms/wtsinstall) для Visual Studio 2017 для быстрого создания приложения UWP с нужными страницами, платформой и функциями. Расширение, выполняющее функции мастера, воплотило в себе проверенные шаблоны и передовые практики, существенно упрощающие и ускоряющие добавление функциональных возможностей в ваше приложение.

![Windows Template Studio](images/template-studio.png)

### <a name="conditional-xaml"></a>Условный XAML

Теперь можно выполнять предварительный просмотр [условного XAML](../debug-test-perf/conditional-xaml.md)для создания [адаптивных к версии приложений](../debug-test-perf/version-adaptive-apps.md). Условный XAML позволяет использовать метод ApiInformation.IsApiContractPresent в разметке XAML, чтобы настраивать свойства и создавать экземпляры объектов в разметке в зависимости от наличия API, без использования кода программной части.

### <a name="game-mode"></a>Режим игры

API-интерфейсы [режима игры](https://msdn.microsoft.com/library/windows/desktop/mt808808) для универсальной платформы Windows (UWP) позволяют создавать оптимизированные игровые среды, используя режим игры в Windows 10. Эти API находятся в заголовке <expandedresources.h>.

![Режим игры](images/game-mode.png)

### <a name="submission-api-supports-video-trailers-and-gaming-options"></a>API отправки поддерживают видеоанонсы и параметры игры

[API отправки в Магазин Windows](../monetize/create-and-manage-submissions-using-windows-store-services.md) теперь позволяет включить [видеоанонсы](../monetize/manage-app-submissions.md#trailer-object) и [параметры игры](../monetize/manage-app-submissions.md#gaming-options-object) в отправляемые приложения.


## <a name="developer-guidance"></a>Руководство для разработчиков

### <a name="data-schemas-for-store-products"></a>Схемы данных для продуктов из Магазина

Мы добавили статью [Схемы данных для продуктов из Магазина](../monetize/data-schemas-for-store-products.md). В этой статье вы найдете схемы данных, связанных с Магазином, доступные для нескольких объектов в пространстве имен [Windows.Services.Store](https://msdn.microsoft.com/library/windows/apps/windows.services.store.aspx), включая [StoreProduct](https://docs.microsoft.com/uwp/api/windows.services.store.storeproduct) и [StoreAppLicense](https://docs.microsoft.com/uwp/api/windows.services.store.storeapplicense).

### <a name="desktop-bridge"></a>Мост для классических приложений

Мы добавили два руководства, которые помогут вам добавлять современные взаимодействия для пользователей Windows 10.

В разделе [Улучшение классических приложений для Windows 10](https://docs.microsoft.com/windows/uwp/porting/desktop-to-uwp-enhance) вы найдете нужные файлы, сможете воспользоваться ими и написать код для улучшения приложений UWP для пользователей Windows 10.  

В разделе [Дополнение классических приложений современными компонентами UWP](https://docs.microsoft.com/windows/uwp/porting/desktop-to-uwp-extend) вы найдете информацию по вставке современных элементов пользовательского интерфейса XAML и прочих элементов взаимодействия UWP, которые должны выполняться в контейнере приложений UWP.

### <a name="getting-started-with-point-of-service"></a>Начало работы с точкой обслуживания

Мы добавили новое руководство, которое поможет вам [приступить к работе с устройствами точки обслуживания](https://docs.microsoft.com/en-us/windows/uwp/devices-sensors/pos-get-started). В руководстве рассматриваются такие вопросы, как перечисление устройств, проверка возможностей устройства, объявление устройств и предоставление общего доступа к устройствам. 


## <a name="videos"></a>Видео

### <a name="mixed-reality"></a>Смешанная реальность

Выпущен ряд новых обучающих видео для [250 курса по Microsoft HoloLens](https://developer.microsoft.com/en-us/windows/mixed-reality/mixed_reality_250). Если вы уже установили инструменты и знакомы с основами разработки для смешанной реальности, изучите эти видеокурсы, в которых вы найдете информацию о создании общих взаимодействий на разных устройствах вмешанной реальности.

### <a name="narrator-and-dev-mode"></a>Экранный диктор и режима разработчика

Возможно, вы уже знаете, что [Экранный диктор](https://support.microsoft.com/help/22798/windows-10-narrator-get-started) можно использовать для проверки качества чтения с экрана в вашем приложении. Однако экранный диктор также поддерживает режим разработчика, обеспечивающий хорошее визуальное представление о сведениях, которые предоставляются экранному диктору. [Посмотрите видео](https://channel9.msdn.com/Blogs/One-Dev-Minute/Using-Narrator-and-Dev-Mode) и узнайте больше о [режиме разработчика Экранного диктора](https://channel9.msdn.com/Blogs/One-Dev-Minute/Using-Narrator-and-Dev-Mode).

### <a name="windows-template-studio"></a>Windows Template Studio

Более подробный обзор Windows Template Studio приводится в [этом видео](https://channel9.msdn.com/Blogs/One-Dev-Minute/Getting-Started-with-Windows-Template-Studio). Когда вы будете готовы, [установите расширение](https://aka.ms/wtsinstall) или [извлеките исходный код и документацию](https://aka.ms/wtsinstall).