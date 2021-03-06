---
Description: В приведенной ниже статье описываются все свойства и элементы полезных данных XML содержимого всплывающего уведомления.
title: Схема XML содержимого всплывающего уведомления
ms.assetid: AF49EFAC-447E-44C3-93C3-CCBEDCF07D22
label: Toast content XML schema
template: detail.hbs
ms.date: 05/19/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 33244391ca9f2ffcba21adacccebf2e9b1d73419
ms.sourcegitcommit: ac7f3422f8d83618f9b6b5615a37f8e5c115b3c4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/29/2019
ms.locfileid: "66363363"
---
# <a name="toast-content-xml-schema"></a>Схема XML содержимого всплывающего уведомления

 

В приведенной ниже статье описываются все свойства и элементы полезных данных XML содержимого всплывающего уведомления.

На следующих схемах XML суффикс "?" означает, что атрибут необязателен.

## <a name="ltvisualgt-and-ltaudiogt"></a>&lt;visual&gt; и &lt;audio&gt;

```
<toast launch? duration? activationType? scenario? >
  <visual lang? baseUri? addImageQuery? >
    <binding template? lang? baseUri? addImageQuery? >
      <text lang? hint-maxLines? >content</text>
      <image src placement? alt? addImageQuery? hint-crop? />
      <group>
        <subgroup hint-weight? hint-textStacking? >
          <text />
          <image />
        </subgroup>
      </group>
    </binding>
  </visual>
  <audio src? loop? silent? />
</toast>
```

**Атрибуты в &lt;всплывающее уведомление&gt;**

launch?

-   launch? ‎‎= string
-   Этот атрибут является необязательным.
-   Строка, которая передается в приложение, когда оно активируется всплывающим уведомлением.
-   В зависимости от значения activationType данное значение может быть получено приложением на переднем плане, внутри фоновой задачи или другим приложением, которое протокол запустил из начального приложения.
-   Формат и содержимое этой строки определяются приложением для собственного использования.
-   Когда пользователь касается всплывающего уведомления или щелкает его, чтобы запустить связанное с ним приложение, строка запуска предоставляет приложению контекст, позволяющий отобразить для пользователя представление, связанное с содержимым всплывающего уведомления, а не то представление, которое появляется при стандартном запуске приложения.
-   Если активация происходит вследствие того, что пользователь щелкнул в области действия, а не в области тела всплывающего уведомления, разработчик получает не предварительно заданный в теге &lt;toast&gt; «запуск», а предопределенные в теге &lt;action&gt; «аргументы».

duration?

-   duration? = "short|long"
-   Этот атрибут является необязательным. Значение по умолчанию — «short».
-   Предназначен для специальных сценариев и appCompat. Этот атрибут больше не требуется для сценария будильника.
-   Не рекомендуется использовать это свойство.

activationType?

-   activationType? = "foreground | background | protocol | system"
-   Этот атрибут является необязательным.
-   Значение по умолчанию — «foreground».

scenario?

-   scenario? = "default | alarm | reminder | incomingCall"
-   Это необязательный атрибут со значением по умолчанию «default».
-   Он нужен только в случае, если ваш сценарий должен вызвать будильник, напоминание или входящий вызов.
-   Не используйте его только для постоянного отображения уведомления на экране.

**Атрибуты в &lt;visual&gt;**

lang?

-   Дополнительные сведения об этом необязательном атрибуте см. в [данной статье о схеме элементов](https://docs.microsoft.com/uwp/schemas/tiles/toastschema/element-visual).

baseUri?

-   Дополнительные сведения об этом необязательном атрибуте см. в [данной статье о схеме элементов](https://docs.microsoft.com/uwp/schemas/tiles/toastschema/element-visual).

addImageQuery?

-   Дополнительные сведения об этом необязательном атрибуте см. в [данной статье о схеме элементов](https://docs.microsoft.com/uwp/schemas/tiles/toastschema/element-visual).

**Атрибуты в &lt;привязки&gt;**

template?

-   \[Важные\] шаблона? = "ToastGeneric"
-   Если вы используете новые функции адаптивных и интерактивных уведомлений, убедитесь, что вы начали использовать шаблон «ToastGeneric» вместо традиционного шаблона.
-   Традиционные шаблоны могут сейчас работать с новыми действиями, но они для этого не предназначены и мы не гарантируем, что они будут работать в дальнейшем.

lang?

-   Дополнительные сведения об этом необязательном атрибуте см. в [данной статье о схеме элементов](https://docs.microsoft.com/uwp/schemas/tiles/toastschema/element-visual).

baseUri?

-   Дополнительные сведения об этом необязательном атрибуте см. в [данной статье о схеме элементов](https://docs.microsoft.com/uwp/schemas/tiles/toastschema/element-visual).

addImageQuery?

-   Дополнительные сведения об этом необязательном атрибуте см. в [данной статье о схеме элементов](https://docs.microsoft.com/uwp/schemas/tiles/toastschema/element-visual).

**Атрибуты в &lt;текста&gt;**

lang?

-   Дополнительные сведения об этом необязательном атрибуте см. в [данной статье о схеме элементов](https://docs.microsoft.com/uwp/schemas/tiles/toastschema/element-visual).

**Атрибуты в &lt;образа&gt;**

src

-   Дополнительные сведения об этом обязательном атрибуте см. в [данной статье о схеме элементов](https://docs.microsoft.com/uwp/schemas/tiles/toastschema/element-image).

placement?

-   placement? = "inline" | "appLogoOverride"
-   Это необязательный атрибут.
-   Он указывает, где будет отображаться данное изображение.
-   «inline» означает внутри тела всплывающего уведомления под текстом; «appLogoOverride» означает замену значка приложения (в левом верхнем углу всплывающего уведомления).
-   Можно использовать одно изображение для каждого значения размещения.

alt?

-   Дополнительные сведения об этом необязательном атрибуте см. в [данной статье о схеме элементов](https://docs.microsoft.com/uwp/schemas/tiles/toastschema/element-image).

addImageQuery?

-   Дополнительные сведения об этом необязательном атрибуте см. в [данной статье о схеме элементов](https://docs.microsoft.com/uwp/schemas/tiles/toastschema/element-image).

hint-crop?

-   hint-crop? = "none" | "circle"
-   Это необязательный атрибут.
-   «none» является значением по умолчанию и означает отсутствие обрезки.
-   «circle» обрезает изображение до круглой формы. Используется для изображений профиля контактов, изображений пользователя и т. д.

**Атрибуты в &lt;аудио&gt;**

src?

-   Дополнительные сведения об этом необязательном атрибуте см. в [данной статье о схеме элементов](https://docs.microsoft.com/uwp/schemas/tiles/toastschema/element-audio).

loop?

-   Дополнительные сведения об этом необязательном атрибуте см. в [данной статье о схеме элементов](https://docs.microsoft.com/uwp/schemas/tiles/toastschema/element-audio).

silent?

-   Дополнительные сведения об этом необязательном атрибуте см. в [данной статье о схеме элементов](https://docs.microsoft.com/uwp/schemas/tiles/toastschema/element-audio).

## <a name="schemas-ltactiongt"></a>Схемы: &lt;action&gt;


На следующих схемах XML суффикс "?" означает, что атрибут необязателен.

```
<toast>
  <visual>
  </visual>
  <audio />
  <actions>
    <input id type title? placeHolderContent? defaultInput? >
      <selection id content />
    </input>
    <action content arguments activationType? imageUri? hint-inputId />
  </actions>
</toast>
```

**Атрибуты в &lt;ввода&gt;**

id

-   id = string
-   Данный атрибут является обязательным.
-   Атрибут «id» необходим и используется разработчиками для получения вводных данных пользователя после активации приложения (на переднем плане или в фоновом режиме).

type

-   type = "text | selection"
-   Данный атрибут является обязательным.
-   Он используется для выбора между текстовым вводом и вводом с помощью предложенного списка вариантов.
-   На мобильных и настольных устройствах он предназначен для выбора между вводом в текстовом поле или вводом в поле списка.

title?

-   title? ‎‎= string
-   Атрибут «title» является необязательным и используется разработчиками, чтобы указать заголовок для входных данных, который должны обрабатывать оболочки, если существует такая возможность.
-   В случае мобильных и настольных устройств этот заголовок отображается над полем ввода.

placeHolderContent?

-   placeHolderContent? ‎‎= string
-   Атрибут PlaceHolderContent является необязательным и представляет собой неактивный текст подсказки, сообщающий о текстовом вводе. Этот атрибут игнорируется, когда тип ввода отличен от «text».

defaultInput?

-   defaultInput? ‎‎= string
-   Атрибут defaultInput является необязательным и используется для предоставления значения ввода по умолчанию.
-   Если тип ввода — «text», то он будет обрабатываться как строка.
-   Если тип ввода — «selection», то ожидается, что он будет идентификатором одного из доступных вариантов выбора внутри элементов этого ввода.

**Атрибуты в &lt;выбора&gt;**

id

-   Данный атрибут является обязательным. Он используется, чтобы определить выбор пользователя. Атрибут «id» возвращается в приложение.

content

-   Данный атрибут является обязательным. Он предоставляет строку для отображения этого элемента выбора.

**Атрибуты в &lt;действие&gt;**

content

-   content = string
-   Атрибут «content» является обязательным. Он предоставляет строку текста, отображаемую на кнопке.

arguments

-   arguments = string
-   Атрибут «arguments» является обязательным. Он описывает данные, которые определяются приложением и которые это приложение может позже восстановить после его активации вследствие выполнения данного действия пользователем.

activationType?

-   activationType? = "foreground | background | protocol | system"
-   Атрибут activationType является необязательным и по умолчанию имеет значение «foreground».
-   Он описывает тип активации, к которой приведет это действие: передний план, фоновый режим, запуск другого приложения через запуск протокола, вызов действия системы.

imageUri?

-   imageUri? ‎‎= string
-   Атрибут imageUri является необязательным и используется для предоставления значка изображения для этого действия, чтобы отобразить его внутри кнопки вместе с текстовым содержимым.

hint-inputId

-   hint-inputId = string
-   Атрибут hint-inpudId является обязательным. Он в первую очередь используется для сценариев быстрого отклика.
-   Данное значение должно быть идентификатором элемента ввода, с которым необходимо связать атрибут.
-   В мобильном или настольном устройстве это поместит кнопку прямо рядом с полем ввода.

## <a name="attributes-for-system-handled-actions"></a>Атрибуты для обрабатываемых системой действий


Система может обработать такие действия, как откладывание и отключение уведомлений, если вы не хотите, чтобы ваше приложение обрабатывало откладывание или перенос уведомлений как фоновую задачу. Обрабатываемые системой действия можно объединить (или определить по отдельности), но при этом не рекомендуется реализовывать действие откладывания без действия отключения.

Поле со списком команд системы: SnoozeAndDismiss

```
<toast>
  <visual>
  </visual>
  <actions hint-systemCommands="SnoozeAndDismiss" />
</toast>
```

Отдельные действия, обрабатываемые системой

```
<toast>
  <visual>
  </visual>
  <actions>
  <input id="snoozeTime" type="selection" defaultInput="10">
    <selection id="5" content="5 minutes" />
    <selection id="10" content="10 minutes" />
    <selection id="20" content="20 minutes" />
    <selection id="30" content="30 minutes" />
    <selection id="60" content="1 hour" />
  </input>
  <action activationType="system" arguments="snooze" hint-inputId="snoozeTime" content=""/>
  <action activationType="system" arguments="dismiss" content=""/>
  </actions>
</toast>
```

Для создания отдельных действий откладывания и отключения выполните описанные ниже действия.

-   Определите activationType = "system"
-   Определите аргументы = "snooze" | "dismiss"
-   Задайте содержимое:
    -   Если вы хотите, чтобы локализованные строки «snooze» и «dismiss» отображались на действиях, укажите, что содержимое является пустой строкой: &lt;action content = ""/&gt;
    -   Если вы хотите получить индивидуальную строку, просто задайте ее значение: &lt;action content="Напомнить позже" /&gt;
-   Определите способ ввода:
    -   Если вы не хотите, чтобы пользователь выбирал интервал откладывания, а просто хотите, чтобы уведомление было отложено только один раз на определенный системой период (одинаковый во всей операционной системе), не создавайте &lt;input&gt; совсем.
    -   Если вы хотите предоставить выбор интервала откладывания, выполните следующие действия.
        -   Определите атрибут hint-inputId в действии откладывания
        -   Сопоставьте идентификатор ввода с атрибутом hint-inputId действия откладывания: &lt;input id="snoozeTime"&gt;&lt;/input&gt;&lt;action hint-inputId="snoozeTime"/&gt;
        -   Установите для идентификатора выбора значение nonNegativeInteger, которое представляет интервал в минутах: &lt;selection id="240" /&gt; означает откладывание на 4 часа
        -   Убедитесь, что значение defaultInput в &lt;input&gt; соответствует одному из идентификаторов дочерних элементов &lt;selection&gt;
        -   Можно задать не более 5 значений атрибута &lt;selection&gt;