---
Description: Выберите базовую цену для приложения и запланируйте изменения цены. Вы также можете настроить эти параметры для отдельных рынков.
title: Настройка и планирование цены приложения
ms.date: 10/31/2018
ms.topic: article
keywords: windows 10, uwp, цена, цены на приложения, цена приложения, продажа приложений, изменение цены, рассчитываемая цена, цена, цены, стоимость, переопределить базовую цену, цена в произвольной форме, произвольная форма
ms.localizationpriority: medium
ms.openlocfilehash: 451a22ffef2d8062de7bf7d29d921db7197987b5
ms.sourcegitcommit: ca1b5c3ab905ebc6a5b597145a762e2c170a0d1c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79210500"
---
# <a name="set-and-schedule-app-pricing"></a>Настройка и планирование цены приложения

Раздел **Цена** на странице [Цены и доступность](set-app-pricing-and-availability.md) позволяет выбрать базовую цену для приложения. Вы также можете [запланировать изменение цены](#schedule-price-changes), указав дату и время, когда цена вашего приложения должна измениться. Кроме того, у вас есть возможность [переопределить базовую цену для конкретных рынков](#override-base-price-for-specific-markets), выбрав новый ценовой уровень или введя цену в свободной форме в местной валюте страны или региона.

> [!NOTE]
> Несмотря на то что этот раздел относится к приложениям, выбор цены для отправок надстроек выполняется аналогичным образом. Обратите внимание, что для [надстроек подписки](../monetize/enable-subscription-add-ons-for-your-app.md)выбранная базовая цена не может быть увеличена (путем изменения базовой цены или планирования изменения цены), хотя она может быть снижена.

## <a name="base-price"></a>Базовая цена

При выборе **базовой цены** для вашего приложения эта цена будет использоваться на любом рынке, где будет продаваться ваше приложение, если только вы не переопределите базовую цену для каких-либо рынков.

Вы можете задать для **базовой цены** значение **Бесплатно** или выбрать доступную ценовую категорию, которая определяет цену во всех странах, где вы распространяете приложение. Ценовые категории начинаются с 0,99 долларов США и далее увеличиваются пошагово (1,09; 1,19 долларов США и т. д.). Как правило, величина шага также растет с увеличением цены. 

> [!NOTE]
> Эти ценовые категории также применимы к надстройкам. 

Каждая ценовая категория имеет соответствующее значение в одной из более чем 60 валют, которые поддерживает Магазин. Мы используем эти значения, чтобы помочь вам продавать приложения по всему миру по сопоставимой цене. Вы можете выбрать базовую цену в любой валюте, и мы будем использовать соответствующие цены для других рынков. Обратите внимание, что иногда мы можем корректировать соответствующее значение на определенных рынках с учетом текущего коэффициента конверсии.

В разделе **Цена** нажмите **Просмотреть таблицу конверсии**, чтобы просмотреть соответствующие цены во всех валютах. В ней также отображается идентификационный номер, связанный с каждой ценовой категорией, который понадобится вам, если вы используете [API отправки в Microsoft Store](../monetize/manage-app-submissions.md#price-tiers) для ввода цен. Вы можете нажать **Скачать**, чтобы скачать копию таблицы ценовых категорий как CSV-файл.

Помните, что выбранный набор цен может включать в себя налог с продажи или налог на добавленную стоимость, которые должны оплачиваться вашими клиентами. Дополнительные сведения о налоговых вопросах на отдельных рынках см. в разделе [Налогообложение для платных приложений](tax-details-for-paid-apps.md). Также ознакомьтесь с [факторами, которые необходимо учитывать при выборе цен для отдельных рынков](define-market-selection.md#price-considerations-for-specific-markets).

> [!NOTE]
> Если выбрать параметр " **прерывать приобретение** " в разделе **сделать продукт доступным, но не обнаруживаемым в магазине** в разделе [видимость](choose-visibility-options.md#discoverability) , вы не сможете установить цены на отправку (поскольку никто не сможет получить приложение, если для него не будет использоваться код рекламного предложения).

## <a name="schedule-price-changes"></a>Планирование изменения цен

Вы можете запланировать изменение цен, если хотите, чтобы базовая цена приложения изменилась в определенное время. 

> [!IMPORTANT]
> Изменения цены отображаются только для пользователей устройств с Windows 10 (включая Xbox). Если ранее опубликованное приложение поддерживает более ранние версии ОС, изменения цены не будут применяться к этим клиентам. Пользователям Windows 8 приложение всегда будет предлагаться по **базовой цене** (а не особой цене для рынка), даже если вы запланируете изменения цены. Для клиентов на Windows 8.1, а также на Windows Phone 8,1 и более ранних версиях приложение всегда будет предлагаться в первой ценовой категории для рынка клиента.

Нажмите **Запланировать изменение цены**, чтобы открыть параметры изменения цены. Выберите нужный ценовой уровень (или введите для определенного рынка цену в свободной форме, чтобы переопределить базовую цену), затем выберите дату, время и часовой пояс.

Вы можете снова нажать кнопку **запланировать изменение цены** , чтобы запланировать необходимое количество последующих изменений.

> [!NOTE]
> Запланированные изменения цен работают не так, как [цена распродажи](put-apps-and-add-ons-on-sale.md). Когда вы назначаете распродажу, цена в Store отображается с зачеркиванием. Клиенты смогут приобрести приложение по цене распродажи в течение выбранного периода. Как только период распродажи закончится, цена распродажи перестанет действовать и приложение будет доступно по базовой цене (или любой другой цене, указанной для этого рынка, если применимо).
>
> Запланированное изменение цены позволяет скорректировать цену. Изменение вступит в силу в указанную дату, но не будет отображаться как распродажа в Store и не будет иметь никакого специального форматирования. У приложения только будет новая базовая цена. 


## <a name="override-base-price-for-specific-markets"></a>Переопределение базовой цены на конкретных рынках

По умолчанию выбранные выше варианты будут применены для всех стран, в которых доступно приложение. Можно изменить стоимость для одного или нескольких рынков, выбрав другой ценовой уровень или введя цену в местной валюте рынка в свободной форме.

> [!IMPORTANT]
> Если ранее опубликованное приложение поддерживает Windows 8, эти клиенты всегда будут видеть приложение по своей **базовой цене**, даже если вы выбрали другую цену для своего рынка.

Чтобы изменить цену для конкретных рынков, нажмите **Выбрать рынки для переопределения базовой цены**. Появится всплывающее окно **Выбор страны** с перечислением всех выбранных вами стран, в которых доступно ваше приложение. (Страны, исключенные в разделе **Страны**, не отображаются здесь.) 

Можно переопределить базовую цену только для одного рынка или для целой группы рынков. После этого можно переопределить базовую цену для другого рынка (или группы рынков), снова нажав **Выбрать рынки для переопределения базовой цены** и повторив описанную ниже процедуру. Чтобы удалить указанную для рынка (или группы рынков) цену переопределения, нажмите **Удалить**.


### <a name="override-the-base-price-for-a-single-market"></a>Переопределение базовой цены для отдельного рынка

Чтобы изменить цену только для одного рынка, выберите его и нажмите **Создать**. После этого будут отображаться те же параметры **Базовая цена** и **Запланировать изменение цены**, что описано выше, но для этого рынка будет применяться выбранная цена. Поскольку вы переопределяете базовую цену только для одного рынка, ценовые уровни будут отображаться в местной валюте этого рынка. Можно нажать **Просмотреть таблицу конверсии**, чтобы увидеть соответствующие цены во всех валютах. 

Переопределяя базовую цену для отдельного рынка, можно ввести выбранную вами цену в свободной форме в местной валюте рынка. Можно ввести любую цену (в пределах минимального и максимального диапазона), даже если она не соответствует одному из стандартных ценовых уровней. Эта цена будет использоваться только для клиентов с Windows 10 (включая Xbox) на избранном рынке. 

> [!IMPORTANT]
> Если ввести цену в свободной форме, эта цена не будет корректироваться (даже если коэффициент конверсии изменится), пока вы не подадите заявку на обновление и не укажете новую цену. 

### <a name="override-the-base-price-for-a-market-group"></a>Переопределение базовой цены для группы рынков

Чтобы переопределить базовую цену для нескольких рынков, создайте *группу рынков*. Чтобы сделать это, выберите страны, которые вы хотите включить в группу, а затем (необязательно) введите имя группы. (Это имя предназначено только для вас и не будет видно пользователям.) Когда закончите, нажмите **Создать**. После этого будут отображаться те же параметры **Базовая цена** и **Запланировать изменение цены**, что описано выше, но для этой группы рынков будет применяться выбранная цена. Обратите внимание, что цены в свободной форме не могут использоваться с группами рынков; необходимо выбрать доступный ценовой уровень.

Чтобы изменить рынки, включенные в группу рынков, нажмите имя группы рынков и добавьте или удалите любые нужные рынки, а затем нажмите кнопку **ОК**, чтобы сохранить изменения. 

> [!NOTE]
> Рынок не может относиться к нескольким рыночным группам в разделе **Цены**.





