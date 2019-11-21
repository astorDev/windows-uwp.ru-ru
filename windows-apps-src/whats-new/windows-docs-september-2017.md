---
title: Что нового в документации Windows в сентябре 2017 г. — разработка приложений UWP
description: В сентябре 2017 г документация для разработчиков Windows 10 пополнилась описанием новых возможностей, видеоматериалами и руководствами для разработчиков.
keywords: what's new, update, features, developer guidance, Windows 10, 1709
ms.date: 09/06/2017
ms.topic: article
ms.localizationpriority: medium
ms.openlocfilehash: bf88814e00e81608f698231f31c2f4ab52cd4ea6
ms.sourcegitcommit: b52ddecccb9e68dbb71695af3078005a2eb78af1
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74258781"
---
# <a name="whats-new-in-the-windows-developer-docs-in-september-2017"></a>Что нового в документации для разработчиков Windows в сентябре 2017 г.

Документация для разработчиков Windows постоянно обновляется и дополняется информацией о новых функциях, доступных разработчикам на платформе Windows. Недавно стали доступны следующие обзоры функций, руководства для разработчиков и примеры, содержащие новые и обновленные сведения для разработчиков Windows.

Конечно, приближается обновление Fall Creators Update, поэтому следите за новостями, чтобы поскорее ознакомиться с дополнительной документацией, которая появится в ближайший месяц!

[Установив средства и пакет SDK](https://developer.microsoft.com/windows/downloads#_blank) в Windows 10, вы можете сразу приступить либо к [созданию нового проекта универсального приложения для Windows](../get-started/your-first-app.md) либо к использованию [существующего кода приложения в ОС Windows](../porting/index.md).

## <a name="features"></a>Возможности

### <a name="xbox-live-creators-program"></a>Программа Xbox Live Creators Program

Программа Xbox Live Creators Program уже запущена и позволяет легко создавать и публиковать UWP-игры, которые можно запускать как на компьютерах с Windows 10, так и на консолях Xbox One. Дополнительные сведения см. в разделе [Начало работы с Xbox Live Creators Program](https://docs.microsoft.com/gaming/xbox-live/get-started-with-creators/get-started-with-xbox-live-creators.md).

## <a name="developer-guidance"></a>Руководство для разработчиков

### <a name="xaml-basics-tutorials"></a>Учебники по основам XAML

Мы написали четыре [учебника по основам XAML](https://docs.microsoft.com/windows/uwp/design/basics/xaml-basics-ui), в которых описан новый [пример PhotoLab](https://github.com/Microsoft/Windows-appsample-photo-lab). В них объясняются четыре основных аспекта программирования на языке XAML: пользовательские интерфейсы, привязка данных, настраиваемые стили и адаптивные макеты. Курс в каждом учебнике начинается с частично готовой версии примера PhotoLab, а затем поэтапно описывается разработка одного недостающего компонента окончательной версии приложения. 

![Снимок экрана с примером PhotoLab, где показана страница коллекции фотографий](images/PhotoLab-gallery-page.png)  

Вот краткий обзор новых статей.

+ [**Создание пользовательских интерфейсов**](https://docs.microsoft.com/windows/uwp/design/basics/xaml-basics-ui) — здесь демонстрируется создание простого интерфейса коллекции фотографий.
+ [**Создание привязки данных**](https://docs.microsoft.com/windows/uwp/data-binding/xaml-basics-data-binding) — здесь показано, как добавить привязки данных к коллекции фотографий, заполнив ее настоящими изображениями.
+ [**Создание настраиваемых стилей**](https://docs.microsoft.com/windows/uwp/design/controls-and-patterns/xaml-basics-style) — здесь рассказывается, как добавить необычные настраиваемые стили в меню редактирования фотографий.
+ [**Создание адаптивных макетов**](https://docs.microsoft.com/windows/uwp/design/basics/xaml-basics-adaptive-layout) — здесь поясняется, как сделать макет коллекции адаптивным, чтобы она хорошо выглядела на всех устройствах и с экранами разного размера.

### <a name="get-started-tutorials"></a>Учебники по началу работы

Раздел "Начало работы" в документации по UWP дополнен [новой начальной страницей для раздела учебников](https://docs.microsoft.com/windows/uwp/get-started/create-uwp-apps). Этот раздел отличается новой улучшенной структурой содержимого "Начало работы", что помогает пользователям без труда находить и использовать подходящие им учебники, включая упомянутые выше учебники по основам XAML.

### <a name="voice-and-tone"></a>Стиль и тон

Мы добавили новые [рекомендации по стилю и тону в приложениях UWP](https://docs.microsoft.com/windows/uwp/in-app-help/voice-and-tone), где даем советы по написанию текста для вашего приложения. Независимо от того, что вы разрабатываете, важно использовать доступный, понятный и информативный язык.

## <a name="samples"></a>Примеры

### <a name="photolab-sample"></a>Пример PhotoLab

В [примере PhotoLab](https://github.com/Microsoft/windows-appsample-photo-lab) представлены простая коллекция фотографий и возможности редактирования фотографий.

![Снимок экрана с примером PhotoLab, где показана страница редактирования фотографий](images/PhotoLab-editing-page.png)  

### <a name="customer-orders"></a>Заказы клиента

Пример [базы данных заказов клиентов](https://github.com/Microsoft/Windows-appsample-customers-orders-database) обновлен с использованием новых инструментов .NET Core 2.0 и Entity Framework.