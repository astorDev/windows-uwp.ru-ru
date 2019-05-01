---
description: API запроса оплаты предоставляет интегрированное решение для приложений UWP для обхода процесс пользователю для ввода сведений об оплате и выберите методы отгрузки.
title: Упрощение платежей с помощью API запроса платежей
ms.date: 09/26/2017
ms.topic: article
keywords: Windows 10, uwp, запрос платежа
ms.openlocfilehash: a40b8265e3445319bd7baa530df0f9e9eaae0f31
ms.sourcegitcommit: fca0132794ec187e90b2ebdad862f22d9f6c0db8
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/24/2019
ms.locfileid: "63804482"
---
# <a name="simplify-payments-with-the-payment-request-api"></a>Упрощение платежей с помощью API запроса платежей
API запроса оплаты для приложений UWP основан на [спецификации W3C оплаты запроса API](https://w3c.github.io/browser-payment-api/). Он дает возможность оптимизировать процесс подсчета стоимости покупок в приложениях UWP. Пользователям может ускорить посредством извлечения с помощью варианты оплаты и доставки адреса уже сохранен со своей учетной записью Майкрософт. Вы сможете повысить скорость преобразования и снизить риск нарушения безопасности данных, так как данные об оплате размечается. Начиная с Windows 10 Creators Update, пользователи могут использовать свои варианты сохраненного оплаты для оплаты легко разных областей в приложениях универсальной платформы Windows.

## <a name="prerequisites"></a>Предварительные требования
Перед началом использования API запроса оплаты, существует несколько моментов, которые необходимо сделать или следует учитывать.

### <a name="getting-a-merchant-id"></a>Получение ИД продавца
В рамках процедуры оплаты запроса Microsoft запрашивает оплаты маркеры от вашего имени поставщика услуг. Поэтому перед запуском с помощью API, нужна авторизация для запроса этих токенов.  Необходимо выполнить несколько шагов, чтобы зарегистрироваться для учетной записи продавца и предоставить необходимые данные для авторизации. Чтобы сделать это, перейдите к [продавца Майкрософт](https://seller.microsoft.com/en-us/dashboard/registration/seller/?accountprogram=uwp). После этого, скопируйте результирующее продавца идентификатор из центра партнеров в вашем приложении, создав запрос оплаты. Затем когда приложение отправляет запрос оплаты, вы получите токен оплаты из вашего процессора, что вам потребуется обработку платежа.

### <a name="geographic-restrictions-and-language-support"></a>Географические ограничения и поддержка языков
API запроса оплаты можно использовать только с базирующаяся в США компании, для обработки транзакций в Соединенных Штатах.

## <a name="using-the-payment-request-api-in-your-app-step-by-step"></a>С помощью API запрос оплаты в приложении: шаг за шагом
В этом разделе демонстрируется использование [API запрос оплаты UWP](https://docs.microsoft.com/en-us/uwp/api/windows.applicationmodel.payments) в вашем приложении. В простейшей форме для простоты мы используем API здесь. Пример более сложных использование этих API, см. в разделе [UWP покупок пример приложения на GitHub](https://github.com/Microsoft/Windows-appsample-shopping).

### <a name="1-create-a-set-of-all-the-payment-options-that-you-accept"></a>1. Создайте набор всех вариантов оплаты, которые вы принимаете.
> [!Note]
> Замените **продавца идентификатор из продавца портала** текст с продавца идентификатор, который был получен из центра продавца.

[!code-csharp[SnippetEnumerate](./code/PaymentsApiSample/PaymentsApiSample/MainPage.xaml.cs#SnippetEnumerate)]

### <a name="2-pull-the-payment-details-together"></a>2. Объединяют сведения об оплате. 

Эти сведения отображаются для пользователя в приложении оплаты. 

[!code-csharp[SnippetDisplayItems](./code/PaymentsApiSample/PaymentsApiSample/MainPage.xaml.cs#SnippetDisplayItems)]

### <a name="3-include-the-sales-tax"></a>3. Включить налог с продаж. 

> [!Important]
> API не добавлять элементы или вычислить налог для вас. Помните, что отличаться налоговые ставки. Для ясности мы используем гипотетической 9.5% налоговой ставки.

[!code-csharp[SnippetTaxes](./code/PaymentsApiSample/PaymentsApiSample/MainPage.xaml.cs#SnippetTaxes)]

### <a name="4-optional--add-discounts-or-other-modifiers-to-the-total"></a>4. (Необязательно)  Добавьте другие модификаторы или скидки в общей сумме. 

Ниже приведен пример добавления скидка на конкретных кредитной картой Contoso отображаемым элементам. (*Contoso* — это вымышленное имя.)

[!code-csharp[SnippetDiscountRate](./code/PaymentsApiSample/PaymentsApiSample/MainPage.xaml.cs#SnippetDiscountRate)]

### <a name="5-assemble-all-the-payment-details"></a>5. Соберите все сведения об оплате.

[!code-csharp[SnippetAggregate](./code/PaymentsApiSample/PaymentsApiSample/MainPage.xaml.cs#SnippetAggregate)]
[!code-csharp[SnippetPaymentOptions](./code/PaymentsApiSample/PaymentsApiSample/MainPage.xaml.cs#SnippetPaymentOptions)]

### <a name="6-submit-the-payment-request"></a>6. Запрос оплаты. 

Вызовите **SubmitPaymentRequestAsync** метод, чтобы отправить запрос оплаты. Откроется приложение оплаты, параметрами для доступных оплаты.

[!code-csharp[SnippetSubmit](./code/PaymentsApiSample/PaymentsApiSample/MainPage.xaml.cs#SnippetSubmit)]

Пользователю предлагается выполнить вход с использованием учетной записи Майкрософт.

После входа пользователя, пользователи могут выбрать варианты оплаты и адрес доставки, которые ранее были сохранены.

![Запрос оплаты пользовательского интерфейса](./images/33.png "выплату пользовательского интерфейса")

Приложение ожидает коснитесь **платить**, затем завершает порядок.

[!code-csharp[SnippetComplete](./code/PaymentsApiSample/PaymentsApiSample/MainPage.xaml.cs#SnippetComplete)]

После завершения платежа пользователю предоставляется **подтверждения заказа** экрана.

![Порядок подтверждения](./images/44.png "подтверждения заказа ")

## <a name="see-also"></a>См. также
- [Справочная документация по Windows.ApplicationModel.Payments](https://docs.microsoft.com/en-us/uwp/api/windows.applicationmodel.payments)
- [Пример корзины приложения универсальной платформы Windows на GitHub](https://github.com/Microsoft/Windows-appsample-shopping)
- [Спецификации W3C оплаты запроса API](https://www.w3.org/TR/payment-request/)
- [Запрос оплаты API ](https://docs.microsoft.com/en-us/microsoft-edge/dev-guide/device/payment-request-api)

