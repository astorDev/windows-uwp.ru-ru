---
Description: Узнайте, как создавать эффективные и ориентированные на пользователей уведомления, которые делают пользователей эффективными и довольными.
title: Руководство по созданию всплывающих уведомлений
label: Toast UX Guidance
template: detail.hbs
ms.date: 05/18/2018
ms.topic: article
keywords: Windows 10, UWP, уведомление, сбор, группа, UX, руководство по UX, руководство, действие, всплывающее уведомление, центр поддержки, не прерывающие, эффективные уведомления, невредоносные уведомления, действия, управление, Организация
ms.localizationpriority: medium
ms.openlocfilehash: 9e970b5ae2019dcc0290dc6b0b72b48208eb5c12
ms.sourcegitcommit: 26bb75084b9d2d2b4a76d4aa131066e8da716679
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/06/2020
ms.locfileid: "75684601"
---
# <a name="toast-notification-ux-guidance"></a>Рекомендации для UX по всплывающим уведомлениям
Уведомления являются обязательной частью современного жизненного цикла; они помогают пользователям повысить производительность и использовать приложения и веб-сайты, а также поддерживать актуальность обновлений. Тем не менее, уведомления могут быстро попытаться переключаться и перерабатываться, если они не предназначены для ориентированного на пользователя способа. Ваши уведомления являются одним щелчком правой кнопкой мыши из-за выключения, и маловероятно, что после выключения они будут включены снова.  Поэтому убедитесь, что ваши уведомления Уважайте на экране пользователя и времени, поэтому этот канал задействования можно будет открыть.

> **Важные API**: [пакет NuGet для уведомлений Windows Community Toolkit](https://www.nuget.org/packages/Microsoft.Toolkit.Uwp.Notifications/)

Мы проанализировали данные телеметрии Windows, а также другие примеры внедрения в первую и третьи стороны, чтобы приступить к четырем правилам, позволяющим получить превосходную историю.  Мы уверены, что эти правила являются универсальными, независимо от платформы и помогут вашим уведомлениям оказать положительное воздействие на ваших пользователей.

## <a name="1-actionable-notifications"></a>1. уведомления с действиями
Уведомления, поддерживающие действия, позволяют пользователям работать без необходимости открывать приложение.  В то время как очень удобно запускать приложения, это не единственная мера успеха, и предоставление пользователям возможности выполнять небольшие задачи без необходимости перехода в приложение может быть очень мощным средством для облегчения работы пользователей.

![Уведомление о действии с текстовым полем ввода и кнопками для установки напоминаний и ответа на уведомление](images/actionable-notification-example01.png)

Выше приведен пример уведомления, использующего действия. Чувство завершенных задач является универсально положительным чувством, и вы можете перенести это чувство в свое приложение или веб-сайт, отправив уведомления с содержимым, которое может содержать действия. Предоставляемые с помощью уведомлений уведомления также могут повысить производительность, как в корпоративных, так и потребительских сценариях, уменьшая время на действия, выполняемые пользователями для выполнения этих небольших задач. Рекомендуется включить действия, которые регулярно выполняются пользователями, или то, что вы пытаетесь обучить пользователей.  Вот несколько примеров:
* Избранным, пометка или имени содержимое
* Утверждение или отклонение отчетов о расходах, времени ожидания, разрешений и т. д.
* Встроенные ответы на сообщения, сообщения электронной почты, групповые беседы, комментарии и т. д.
* Завершение заказов с помощью [ожидающего обновления](toast-pending-update.md)
* Настройка предупреждений или напоминаний за другое время, а также потенциальное время резервирования в календаре

Практичные уведомления — это очень мощный инструмент, позволяющий пользователям эффективно работать, выполнять задачи и работать с приложением или веб-сайтом очень эффективно.  Здесь есть множество возможностей. Если вы хотите помочь обсудите идеи, вы можете обратиться к группе уведомлений Windows.

## <a name="2-timing-and-urgency"></a>2. время и срочность
В отличие от того, как мы часто думаем об уведомлениях, в реальном времени это не всегда лучше! Мы настоятельно рекомендуем разработчикам подумать о пользователе, и если отправляемое им уведомление является срочной информацией или нет. Пользователи могут легко перегружаться с чрезмерно большим объемом информации и быть разочарованы, если они прерываются при попытке сосредоточиться. Windows предлагает несколько способов учитывать степень вмешательства отправляемых уведомлений:

**Необработанные уведомления:** Использование [необработанных уведомлений](raw-notification-overview.md) может быть полезным по многим причинам, особенно если оно сводится к минимизации перерывов для пользователя.  Отправка необработанных уведомлений приведет к выходу приложения из системы в фоновом режиме, чтобы вы могли оценить, имеет ли смысл уведомление о доставке немедленно в контексте приложения. Если вы хотите, чтобы пользователь отображался сразу же, вы можете открыть [Локальное](send-local-toast.md) всплывающее уведомление.  Если пользователь не должен видеть сейчас, вы можете создать [запланированное всплывающее уведомление](https://blogs.msdn.microsoft.com/tiles_and_toasts/2016/09/30/quickstart-sending-an-alarm-in-windows-10/) , которое будет срабатывать позже.


**Всплывающее уведомление.** вы также можете запустить оповещение, которое будет пропущено в правом нижнем углу экрана, а затем отправить уведомление непосредственно в центр уведомлений. Это достигается путем присвоения [свойству супресспопуп](https://docs.microsoft.com/uwp/api/windows.ui.notifications.toastnotification.suppresspopup) значения true. Несмотря на то, что некоторые скепсис не выводят уведомления за пределы центра действий, мы видим, что в центре уведомлений мы увидим более высокий уровень участия в 2-3 раз.  Пользователи работают быстрее, когда они готовы получать уведомления и могут контролировать их прерывание, поэтому содержимое в центре поддержки может быть настолько эффективным для неагрессивного уведомления пользователей.

## <a name="3-clear-out-the-clutter"></a>3. Уберите ненужные
Уведомления могут сохраняться в центре уведомлений достаточно долго (по умолчанию три дня).  Крайне важно убедиться, что содержимое, которое здесь находится в актуальном состоянии, и соответствующее значение при каждом открытии центра действий пользователем. Вы тратите на экранное пространство пользователя и можете использовать слоты, которые могут быть использованы для получения более новых данных.  Допустим, пользователь устанавливает приложение управления электронной почтой и получает десять сообщений электронной почты и десять уведомлений вместе с этими сообщениями.  В зависимости от желаемого опыта вы можете удалить эти уведомления, если пользователь прочитал соответствующее сообщение электронной почты или открыл приложение как способ удаления старых помех из центра уведомлений.

У нас есть серия API-интерфейсов [тоастнотификатионхистори](https://docs.microsoft.com/uwp/api/windows.ui.notifications.toastnotificationhistory) , которые позволяют увидеть, какое содержимое находится в центре поддержки, а также управлять этими уведомлениями. Уважайте пространство экрана пользователя и следите за тем, чтобы для пользователей выводится только соответствующее и текущее содержимое.

## <a name="4-keeping-organized"></a>4. упорядочение
Как упоминалось ранее, содержимое в центре поддержки остается в течение трех дней.  Чтобы пользователи могли быстро выбирать сведения, которые они ищут, упорядочивайте уведомления в центре уведомлений с помощью [заголовков](https://docs.microsoft.com/windows/uwp/design/shell/tiles-and-notifications/toast-headers) или [коллекций](https://docs.microsoft.com/uwp/api/windows.ui.notifications.toastcollection). Пример заголовка можно увидеть ниже.

![Примеры всплывающих уведомлений с заголовками с меткой "Кемпинг!!"](images/toast-headers-action-center.png)

Сгруппируйте эти уведомления таким образом, чтобы соответствующее содержимое собралось вместе (т. е. Подумайте о том, что в спортивном приложении разделяются различные спортивные леагуесы или сортируются сообщения по группам). Коллекции — это более очевидный способ группировки уведомлений, в то время как заголовки являются более тонкими, но оба позволяют быстрее рассмотреть и выбрать уведомления.



Эти четыре пункта выше — это рекомендации, которые мы настроили с помощью нашего собственного анализа телеметрии, а в первую и третьи эксперименты. Однако помните, что эти рекомендации относятся только к рекомендациям.  Мы уверены, что эти правила помогут повысить эффективность и производительность ваших уведомлений, но ничего не может заменять мышления, ориентированное на пользователя, и изучать собственные данные.  

## <a name="related-topics"></a>Связанные темы

* [Содержимое всплывающего уведомления](adaptive-interactive-toasts.md)
* [Необработанные уведомления](raw-notification-overview.md)
* [Ожидающее обновление](toast-pending-update.md)
* [Библиотека уведомлений на GitHub (входит в состав набора средств сообщества Windows)](https://github.com/windows-toolkit/WindowsCommunityToolkit/tree/master/Microsoft.Toolkit.Uwp.Notifications)
