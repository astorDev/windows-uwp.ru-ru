---
description: API запроса на оплату предоставляет интегрированное решение для приложений UWP, чтобы обойти процесс, требующий от пользователя ввода сведений о платежах и выбора методов доставки.
title: Упрощение платежей с помощью API запроса платежей
ms.date: 09/26/2017
ms.topic: article
keywords: Windows 10, UWP, запрос на оплату
ms.openlocfilehash: 3e54767496d9c8d25ce42ea389f43ca2075d128d
ms.sourcegitcommit: 26bb75084b9d2d2b4a76d4aa131066e8da716679
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/06/2020
ms.locfileid: "75685047"
---
# <a name="simplify-payments-with-the-payment-request-api"></a>Упрощение платежей с помощью API запроса платежей
API запроса на оплату для приложений UWP основан на [спецификации API запроса на оплату W3C](https://w3c.github.io/browser-payment-api/). Она дает возможность упростить процесс извлечения в приложениях UWP. Пользователи могут ускорить извлечение, используя варианты оплаты и адреса доставки, уже сохраненные вместе с учетная запись Майкрософт. Вы можете увеличить скорость преобразования и снизить риск нарушения целостности данных, так как сведения о платеже размечены. Начиная с обновления Windows 10 для дизайнеров, пользователи могут использовать сохраненные варианты оплаты для простого оплаты в приложениях UWP.

## <a name="prerequisites"></a>Необходимые условия
Прежде чем приступить к использованию API запроса на оплату, необходимо выполнить несколько действий или иметь в виду.

### <a name="getting-a-merchant-id"></a>Получение идентификатора продавца
В рамках процесса запроса на оплату Корпорация Майкрософт запрашивает маркеры оплаты от вашего имени у поставщика услуг. Итак, перед началом использования API требуется авторизация для запроса этих токенов.  Необходимо выполнить несколько шагов, чтобы зарегистрироваться для получения учетной записи продавца и предоставить необходимую авторизацию. Для этого перейдите в [центр продавцов Майкрософт](https://partner.microsoft.com/dashboard/registration/seller?accountprogram=uwp). После этого скопируйте полученный идентификатор продавца из центра партнеров в приложение при формировании запроса на оплату. После того, как приложение отправит запрос на оплату, вы получите маркер оплаты от процессора, который потребуется для отправки платежа.

### <a name="geographic-restrictions-and-language-support"></a>Географические ограничения и языковая поддержка
API запроса на оплату может использоваться только компаниями, основанными на США, для обработки транзакций в США.

## <a name="using-the-payment-request-api-in-your-app-step-by-step"></a>Использование API запроса на оплату в приложении: пошаговое руководство
В этом разделе показано, как использовать [API запроса на оплату UWP](https://docs.microsoft.com/uwp/api/windows.applicationmodel.payments) в приложении. Мы используем API здесь в своей простейшей форме для простоты. Пример более расширенного использования этих API см. в [примере приложения для покупок UWP на сайте GitHub](https://github.com/Microsoft/Windows-appsample-shopping).

### <a name="1-create-a-set-of-all-the-payment-options-that-you-accept"></a>1. Создайте набор всех принимаемых параметров оплаты.
> [!Note]
> Замените текст " **Оптовая-ID-from-продавец — портал** " идентификатором продавца, полученным из центра продавцов.

[!code-csharp[SnippetEnumerate](./code/PaymentsApiSample/PaymentsApiSample/MainPage.xaml.cs#SnippetEnumerate)]

### <a name="2-pull-the-payment-details-together"></a>2. извлекаются сведения об оплате вместе. 

Эти сведения будут отображаться для пользователя в платежном приложении. 

[!code-csharp[SnippetDisplayItems](./code/PaymentsApiSample/PaymentsApiSample/MainPage.xaml.cs#SnippetDisplayItems)]

### <a name="3-include-the-sales-tax"></a>3. Включите налог на продажу. 

> [!Important]
> API не добавляет товары или не вычисляет налог за вас. Помните, что ставка налога зависит от юрисдикции. Для ясности мы используем гипотетическую налоговую ставку 9,5%.

[!code-csharp[SnippetTaxes](./code/PaymentsApiSample/PaymentsApiSample/MainPage.xaml.cs#SnippetTaxes)]

### <a name="4-optional--add-discounts-or-other-modifiers-to-the-total"></a>4. (необязательно) добавьте в итог скидки или другие модификаторы. 

Ниже приведен пример добавления скидки для использования конкретной кредитной карты contoso с отображаемыми элементами. (*Contoso* является фиктивным именем.)

[!code-csharp[SnippetDiscountRate](./code/PaymentsApiSample/PaymentsApiSample/MainPage.xaml.cs#SnippetDiscountRate)]

### <a name="5-assemble-all-the-payment-details"></a>5. Соберите все сведения об оплате.

[!code-csharp[SnippetAggregate](./code/PaymentsApiSample/PaymentsApiSample/MainPage.xaml.cs#SnippetAggregate)]
[!code-csharp[SnippetPaymentOptions](./code/PaymentsApiSample/PaymentsApiSample/MainPage.xaml.cs#SnippetPaymentOptions)]

### <a name="6-submit-the-payment-request"></a>6. Отправьте запрос на оплату. 

Вызовите метод **субмитпайментрекуестасинк** , чтобы отправить запрос на оплату. Откроется приложение платеж, в котором отображаются доступные варианты оплаты.

[!code-csharp[SnippetSubmit](./code/PaymentsApiSample/PaymentsApiSample/MainPage.xaml.cs#SnippetSubmit)]

Пользователю предлагается выполнить вход с помощью учетная запись Майкрософт.

После входа пользователь может выбрать варианты оплаты и адрес доставки, которые были ранее сохранены.

![Пользовательский интерфейс запроса на оплату](./images/33.png "Пользовательский интерфейс запроса на оплату")

Приложение ждет, пока пользователь нажмет кнопку " **Оплата**", а затем завершает заказ.

[!code-csharp[SnippetComplete](./code/PaymentsApiSample/PaymentsApiSample/MainPage.xaml.cs#SnippetComplete)]

После завершения оплаты пользователю отображается экран **заказа с подтверждением** .

![Порядок подтвержден](./images/44.png "Порядок подтвержден")

## <a name="see-also"></a>См. также статью
- [Справочная документация по Windows. ApplicationModel. выплат](https://docs.microsoft.com/uwp/api/windows.applicationmodel.payments)
- [Пример приложения для покупок UWP на GitHub](https://github.com/Microsoft/Windows-appsample-shopping)
- [Спецификация API запроса на оплату W3C](https://www.w3.org/TR/payment-request/)
- [API запроса на оплату](https://docs.microsoft.com/microsoft-edge/dev-guide/windows-integration/payment-request-api)

