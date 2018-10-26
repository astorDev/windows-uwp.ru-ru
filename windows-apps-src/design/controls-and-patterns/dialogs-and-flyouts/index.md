---
author: mijacobs
Description: Dialogs and flyouts display transient UI elements that appear when the user requests them or when something happens that requires notification or approval.
title: Диалоговые окна и всплывающие элементы
template: detail.hbs
ms.author: mijacobs
ms.date: 07/06/2018
ms.topic: article
keywords: windows 10, uwp
ms.assetid: ad6affd9-a3c0-481f-a237-9a1ecd561be8
pm-contact: yulikl
design-contact: kimsea
dev-contact: niallm
doc-status: Published
ms.localizationpriority: medium
ms.openlocfilehash: d4ff66e988634cf1ba48809688ea6535e6e95b03
ms.sourcegitcommit: 6cc275f2151f78db40c11ace381ee2d35f0155f9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/26/2018
ms.locfileid: "5561180"
---
# <a name="dialogs-and-flyouts"></a>Диалоговые окна и всплывающие элементы



Диалоговые окна и всплывающие элементыиспользуются для временного отображения элементов пользовательского интерфейса, если происходит нечто, требующее уведомления, подтверждения или дополнительных сведений от пользователя.

> **Важные API-интерфейсы**: [класс ContentDialog](/uwp/api/Windows.UI.Xaml.Controls.ContentDialog), [класс Flyout](/uwp/api/Windows.UI.Xaml.Controls.Flyout)


:::row:::
    :::column:::
        **Dialogs**
        
        ![Example of a dialog](../images/dialogs/dialog_RS2_delete_file.png)

        Dialogs are modal UI overlays that provide contextual app information. Dialogs block interactions with the app window until being explicitly dismissed. They often request some kind of action from the user.
    :::column-end:::
    :::column::: 
        **Flyouts**

        ![Example of a flyout](../images/flyout-example2.png)

        A flyout is a lightweight contextual popup that displays UI related to what the user is doing. It includes placement and sizing logic, and can be used to reveal a secondary control or show more detail about an item.

        Unlike a dialog, a flyout can be quickly dismissed by tapping or clicking somewhere outside the flyout, pressing the Escape key or Back button, resizing the app window, or changing the device's orientation.
    :::column-end:::
:::row-end:::


## <a name="is-this-the-right-control"></a>Выбор подходящего элемента управления

Диалоговые окна и всплывающие элементы позволяют пользователям быть в курсе важной информации, но также мешают взаимодействию с пользователем. Поскольку диалоговые окна модальны (приводят к блокировке), они прерывают работу пользователей и мешают им выполнять дальнейшие действия, пока не произойдет взаимодействие с диалоговым окном. Всплывающие элементы в меньшей степени мешают взаимодействию с пользователем, но отображение слишком большого числа всплывающих элементов может отвлекать.

Определив, собираетесь ли вы использовать диалоговое окно или всплывающий элемент, необходимо выбрать один из этих вариантов.

Учитывая то, что, в отличие от всплывающих элементов, диалоговые окна блокируют взаимодействие, последние следует использовать в тех случаях, когда пользователь должен уделить все внимание определенной информации или ответить на вопрос. С другой стороны, всплывающие элементы можно использовать для привлечения внимания к определенной информации, которую пользователь вполне может проигнорировать.

:::row:::
    :::column:::
   <p><b>Используйте диалоговое окно в следующих целях.</b> <br/>
<ul>
<li>Чтобы сообщить важную информацию, которую пользователь <b>должен</b> прочитать и осознать перед продолжением работы. Ниже перечислены примеры таких данных.
<ul>
  <li>угроза безопасности пользователя;</li>
  <li>намерение изменить ценные данные без возможности отмены действия;</li>
  <li>намерение удалить ценные данные;</li>
  <li>подтверждение покупки из приложения;</li>
</ul>

</li>
<li>Сообщения об ошибках, которые относятся к приложению в целом, например ошибка подключения.</li>
<li>Вопросы, если приложению нужно задать пользователю блокирующий вопрос, например, если приложение не может сделать выбор от имени пользователя. Блокирующий вопрос нельзя проигнорировать или отложить. У пользователя должны быть понятные варианты выбора.</li>
</ul>
</p>
    :::column-end:::
    :::column:::
   <p><b>Используйте всплывающий элемент в следующих целях.</b> <br/>
<ul>
<li>сбор дополнительных сведений для завершения действия;</li>
<li>отображение временно актуальных сведений; например, когда пользователь щелкает эскиз изображения в приложении фотоальбома, можно использовать всплывающий элемент для отображения увеличенного варианта этого изображения;</li>
<li>отображение дополнительной информации, например подробных сведений или более длинных описаний элемента на странице.</li>
</ul></p>
    :::column-end:::
:::row-end:::


## <a name="ways-to-avoid-using-dialogs-and-flyouts"></a>Способы не используйте диалоговые окна и всплывающие элементы

Учитывайте важность информации, которую требуется отобразить: важна ли она настолько, чтобы прерывать пользователя? Также учтите частоту отображения информации. Если диалоговое окно или уведомление отображается каждые несколько минут, возможно, следует выделить для этой информации место в основном пользовательском интерфейсе. Возьмем, к примеру, клиент чата. Вместо того чтобы отображать всплывающий элемент каждый раз, когда друг выполняет вход, можно отображать список друзей, находящихся на данный момент в сети, и выделять друзей, выполняющих вход.

Диалоговые окна часто используются для подтверждения действия (например, удаления файла) перед его выполнением. Если ожидается, что пользователь будет часто выполнять определенное действие, вы можете предоставить пользователю возможность отменить ошибочное действие, чем запрашивать его подтверждение каждый раз.

## <a name="how-to-create-a-dialog"></a>Создание диалогового окна

См. [в статье диалоговые окна](dialogs.md). 

## <a name="how-to-create-a-flyout"></a>Создание всплывающего элемента

См. в [статье всплывающий элемент](flyouts.md). 

## <a name="examples"></a>Примеры.

<table>
<th align="left">Галерея элементов управления XAML<th>
<tr>
<td><img src="../images/xaml-controls-gallery-sm.png" alt="XAML controls gallery"></img></td>
<td>
    <p>Если у вас установлено приложение <strong style="font-weight: semi-bold">галереи элементов управления XAML</strong>, щелкните здесь, чтобы открыть приложение и увидеть <a href="xamlcontrolsgallery:/item/ContentDialog">ContentDialog</a> или <a href="xamlcontrolsgallery:/item/Flyout">Flyout</a> в действии.</p>
    <ul>
    <li><a href="https://www.microsoft.com/store/productId/9MSVH128X2ZT">Скачать приложение галереи элементов управления XAML (Microsoft Store)</a></li>
    <li><a href="https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/XamlUIBasics">Получить исходный код (GitHub)</a></li>
    </ul>
</td>
</tr>
</table>

