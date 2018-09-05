---
author: QuinnRadich
title: Что нового в документации Windows в сентябре 2017г.— разработка приложений UWP
description: Новые возможности, видео и руководства для разработчиков добавлены в документацию для разработчиков Windows 10 в сентябре 2017г
keywords: новые возможности, обновления, компоненты, руководство разработчика, Windows 10, 1709
ms.author: quradic
ms.date: 09/06/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
ms.localizationpriority: medium
ms.openlocfilehash: 33091903ebf1a7ff1150dcaa9bd83a3e76417926
ms.sourcegitcommit: 7aa1933e6970f878faf50d59e1f799b90afd7cc7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/05/2018
ms.locfileid: "3376578"
---
# <a name="whats-new-in-the-windows-developer-docs-in-september-2017"></a>Что нового в документации для разработчиков Windows в сентябре 2017г.

Документация для разработчиков Windows непрерывно обновляется и дополняется информацией о новых функциях, доступных разработчикам на платформе Windows. Недавно стали доступны следующие обзоры функций, руководства для разработчиков и примеры, содержащие новые и обновленные сведения для разработчиков Windows.

Конечно, приближается осеннее обновление Creators Update, поэтому следите за новостями, чтобы получить доступ к большому объему дополнительной документации, которая поступит в ближайший месяц!

[Установив средства и пакет SDK](http://go.microsoft.com/fwlink/?LinkId=821431) в Windows 10, вы можете сразу приступить либо к [созданию нового проекта универсального приложения для Windows](../get-started/your-first-app.md), либо к использованию [существующего кода приложения в ОС Windows](../porting/index.md).

## <a name="features"></a>Возможности

### <a name="xbox-live-creators-program"></a>Программа Xbox Live Creators Program

Программа Xbox Live Creators Program уже работает и позволяет вам легко создавать и публиковать UWP-игры, которые можно запускать как на компьютерах с Windows 10, так и на консолях Xbox One. Дополнительные сведения см. в разделе [Начало работы с программой Xbox Live Creators](../xbox-live/get-started-with-creators/get-started-with-xbox-live-creators.md).

## <a name="developer-guidance"></a>Руководство для разработчиков

### <a name="xaml-basics-tutorials"></a>Учебники по основам XAML

Мы написали четыре [учебника по основам XAML](https://docs.microsoft.com/en-us/windows/uwp/get-started/xaml-basics-intro) для описания нового [примера PhotoLab](https://github.com/Microsoft/Windows-appsample-photo-lab), которые описывают четыре основных аспекта программирования на языке XAML: пользовательские интерфейсы, привязка данных, настраиваемые стили и адаптивные макеты. Курс в каждом учебнике начинается с частично готовой версии примера PhotoLab, и затем поэтапно производится разработка одного недостающего компонента окончательной версии приложения. 

![Снимок экрана с примером PhotoLab, где показана страница коллекции фотографий](images/PhotoLab-gallery-page.png)  

Вот краткий обзор новых статей.

+ [**Создание пользовательских интерфейсов**](https://docs.microsoft.com/en-us/windows/uwp/get-started/xaml-basics-ui) — здесь демонстрируется создание простого интерфейса коллекции фотографий.
+ [**Создание привязки данных**](https://docs.microsoft.com/en-us/windows/uwp/get-started/xaml-basics-data-binding) — здесь показано, как добавить привязки данных к коллекции фотографий, заполнив ее настоящими изображениями.
+ [**Создание настраиваемых стилей**](https://docs.microsoft.com/en-us/windows/uwp/get-started/xaml-basics-style) — здесь рассказывается, как добавить необычные настраиваемые стили в меню редактирования фотографий.
+ [**Создание адаптивных макетов**](https://docs.microsoft.com/en-us/windows/uwp/get-started/xaml-basics-adaptive-layout) — здесь поясняется, как сделать макет коллекции адаптивным, чтобы она хорошо выглядела на всех устройствах и с экранами разного размера.

### <a name="get-started-tutorials"></a>Учебники по началу работы

Раздел "Начало работы" в документации по UWP дополнен [новой начальной страницей для раздела учебников](https://docs.microsoft.com/windows/uwp/get-started/create-uwp-apps). Этот раздел отличается новой улучшенной структурой содержимого "Начало работы", что помогает пользователям без труда находить и использовать подходящие им учебники, включая упомянутые выше учебники по основам XAML.

### <a name="voice-and-tone"></a>Стиль и тон

Мы добавили новые [рекомендации по стилю и тону в приложениях UWP](https://docs.microsoft.com/windows/uwp/in-app-help/voice-and-tone), чтобы дать вам советы по написанию текста для вашего приложения. Независимо от того, что вы разрабатываете, важно использовать доступный, понятный и информативный язык.

## <a name="samples"></a>Примеры

### <a name="photolab-sample"></a>Пример PhotoLab

В [примере PhotoLab](https://github.com/Microsoft/windows-appsample-photo-lab) представлены простая коллекция фотографий и возможности редактирования фотографий.

![Снимок экрана с примером PhotoLab, где показана страница редактирования фотографий](images/PhotoLab-editing-page.png)  

### <a name="customer-orders"></a>Заказы клиента

Пример [базы данных заказов клиентов](https://github.com/Microsoft/Windows-appsample-customers-orders-database) обновлен с использованием новых инструментов .NET Core 2.0 и Entity Framework.