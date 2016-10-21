---
author: mijacobs
Description: "Функция адаптивных и интерактивных всплывающих уведомлений позволяет создавать удобные всплывающие уведомления с большим объемом содержимого, встроенными изображениями и дополнительными возможностями взаимодействия с пользователем."
title: "Адаптивные и интерактивные всплывающие уведомления"
ms.assetid: 1FCE66AF-34B4-436A-9FC9-D0CF4BDA5A01
label: Adaptive and interactive toast notifications
template: detail.hbs
translationtype: Human Translation
ms.sourcegitcommit: eb6744968a4bf06a3766c45b73b428ad690edc06
ms.openlocfilehash: 55f5cd9e647e74d7861a7472872373d8949b79ba

---
# Адаптивные и интерактивные всплывающие уведомления

<link rel="stylesheet" href="https://az835927.vo.msecnd.net/sites/uwp/Resources/css/custom.css"> 

Функция адаптивных и интерактивных всплывающих уведомлений позволяет создавать удобные всплывающие уведомления с большим объемом содержимого, встроенными изображениями и дополнительными возможностями взаимодействия с пользователем.

Модель адаптивных и интерактивных всплывающих уведомлений дополнена следующими преимуществами в сравнении с традиционным каталогом шаблонов:

-   Возможность включить во всплывающие уведомления кнопки и поля для ввода данных.
-   Три различных типа активации для основного всплывающего уведомления и для каждого действия.
-   Возможность создавать уведомления для определенных сценариев, связанных, например, с будильниками, напоминаниями и входящими вызовами.

**Примечание**. Традиционные шаблоны для Windows 8.1 и Windows Phone 8.1 см. в [предыдущем каталоге шаблонов всплывающих уведомлений](https://msdn.microsoft.com/library/windows/apps/hh761494).

 

## Структура всплывающего уведомления


Всплывающее уведомление создается с помощью XML-кода, который обычно включает в себя следующие основные элементы:

-   &lt;visual&gt; отвечает за содержимое, доступное для просмотра пользователями, включая текст и изображения
-   &lt;actions&gt; содержит кнопки и поля ввода, которые разработчику нужно добавить в уведомление
-   &lt;audio&gt; определяет звуковой сигнал, который воспроизводится при открытии уведомления

Приведем пример кода:

```XML
<toast launch="app-defined-string">
  <visual>
    <binding template="ToastGeneric">
      <text>Sample</text>
      <text>This is a simple toast notification example</text>
      <image placement="AppLogoOverride" src="oneAlarm.png" />
    </binding>
  </visual>
  <actions>
    <action content="check" arguments="check" imageUri="check.png" />
    <action content="cancel" arguments="cancel" />
  </actions>
  <audio src="ms-winsoundevent:Notification.Reminder"/>
</toast>
```

И визуальное представление структуры:

![структура всплывающего уведомления](images/adaptivetoasts-structure.jpg)

### Визуальное представление

Внутри визуального элемента должен находится только один элемент привязки, который содержит визуальное содержимое всплывающего уведомления.

Для уведомлений на плитках в приложениях универсальной платформы Windows (UWP) можно использовать различные шаблоны в зависимости от размера плитки. При этом для всплывающих уведомлений предусмотрено только одно имя шаблона: **ToastGeneric**. Наличие только одного имени шаблона означает следующее.

-   При изменении содержимого всплывающего уведомления (например, когда добавляется еще одна строка текста, вставляется рисунок или заменяется эскиз, отображающий значок приложения) можно не беспокоиться о том, что изменится весь шаблон или появятся недопустимые полезные данные в связи с несоответствием между именем шаблона и содержимым.
-   Можно использовать один код, чтобы создать такие же полезные данные для **toast notification**, когда уведомление необходимо доставить на различные типы устройств сMicrosoft Windows, включая телефоны, планшеты, компьютеры и Xbox One. Каждое из этих устройств будет принимать уведомления и отображать их для пользователя на основе политик пользовательского интерфейса, с применением соответствующих визуальных возможностей и модели взаимодействия.

Информацию обо всех атрибутах, поддерживаемых в разделе визуального представления, и их дочерних элементах см. в разделе «Схема» ниже. Дополнительные примеры см. в разделе «Примеры XML-кода» ниже.

### Действия

В приложениях UWP всплывающие уведомления могут содержать кнопки и другие средства ввода данных, что дает пользователям большую свободу действий за пределами приложения. Эти действия определены в элементе &lt;actions&gt;. Существует два типа действий, которые можно указать:

-   &lt;action&gt; — отображается как кнопка на настольных и мобильных устройствах. Вы можете указать до пяти пользовательских или системных действий внутри всплывающего уведомления.
-   &lt;input&gt; — позволяет пользователям ввести данные (например, быстро ответить на сообщение или выбрать параметр в раскрывающемся меню).

Действия &lt;action&gt; и &lt;input&gt; адаптивны в рамках семейства устройств с Windows. Например, на мобильных или настольных устройствах &lt;action&gt; — это кнопка, доступная для касания или нажатия. Элемент &lt;input&gt; для текста — это поле, в котором пользователь может ввести текст с помощью физической или экранной клавиатуры. Эти элементы также адаптируются к последующим сценариям взаимодействия (например, к голосовым командам или голосовому вводу текста).

С помощью атрибута [**ActivationType**](https://msdn.microsoft.com/library/windows/desktop/dn408447) внутри элемента &lt;action&gt; можно определить, какой из следующих сценариев должен следовать за действиями пользователя:

-   Активация приложения на переднем плане с аргументом действия, который можно использовать для перехода к указанной странице или контексту.
-   Активация фоновой задачи приложения без взаимодействия с пользователем.
-   Активация другого приложения через запуск протокола.
-   Укажите действие, которое должна выполнить система. Текущие доступные действия системы — откладывание и отключение установленного будильника или напоминания. Они будут подробно описаны в разделе ниже.

Информацию обо всех атрибутах, поддерживаемых в разделе визуального представления, и их дочерних элементах см. в разделе «Схема» ниже. Дополнительные примеры см. в разделе «Примеры XML-кода» ниже.

### Звуковой сигнал

Настраиваемые звуки в настоящее время не поддерживаются в приложениях UWP для настольной платформы. Для приложения на компьютере вы можете выбрать звуковые сигналы из списка «ms-winsoundevents». Приложения UWP на мобильных платформах поддерживают звуковые сигналы из ms-winsoundevents, а также настраиваемые сигналы в следующих форматах:

-   ms-appx:///
-   ms-appdata:///

Сведения о звуковых сигналах во всплывающих уведомлениях, включая полный список ms-winsoundevents, см. на [странице со звуковой схемой](https://msdn.microsoft.com/library/windows/apps/br230842).

## Будильники, напоминания и входящие вызовы


Всплывающие уведомления можно использовать для будильников, напоминаний и входящих вызовов. Эти специальные уведомления имеют внешний вид, соответствующий стандартным всплывающим уведомлениям, однако они обладают определенным настраиваемым пользовательским интерфейсом и шаблонами на основе сценария.

-   Всплывающее уведомление с напоминанием остается на экране, пока пользователь его не закроет или не выполнит действие. Кроме того, в Windows Mobile всплывающие уведомления с напоминаниями отображаются сразу в развернутом виде.
-   Помимо описанного выше поведения всплывающих уведомлений с напоминаниями, уведомления будильника также автоматически воспроизводят повторяющийся звуковой сигнал.
-   Уведомления о входящих вызовах на устройствах Windows Mobile открываются во весь экран. Для этого задается атрибут сценария внутри корневого элемента всплывающего уведомления — &lt;toast&gt;: &lt;toast scenario=" { default | alarm | reminder | incomingCall } " &gt;

## Примеры XML


**Примечание**. Снимки экрана всплывающего уведомления для этих примеров выполнены в приложении на рабочем столе. На мобильных устройствах всплывающее уведомление может появляться на экране свернутым, с маркером для развертывания внизу.

 

**Уведомление с расширенным визуальным содержимым**

В этом примере показано, как можно получить несколько строк текста, дополнительное небольшое изображение для переопределения логотипа приложения и дополнительный эскиз встроенного рисунка.

```XML
<toast launch="app-defined-string">
  <visual>
<binding template="ToastGeneric">
    <text>Photo Share</text>
      <text>Andrew sent you a picture</text>
      <text>See it in full size!</text>
      <image placement="appLogoOverride" src="A.png" />
    <image placement="inline" src="hiking.png" />
    </binding>
  </visual>
</toast>
```

![уведомление с расширенным визуальным содержимым](images/adaptivetoasts-xmlsample01.png)

 

**Уведомление с действиями, пример 1**

В этом примере показано...

```XML
<toast launch="app-defined-string">
  <visual>
    <binding template="ToastGeneric">
      <text>Microsoft Company Store</text>
      <text>New Halo game is back in stock!</text>
      <image placement="appLogoOverride" src="A.png" />
    </binding>
  </visual>
  <actions>
    <action activationType="foreground" content="see more details" arguments="details" imageUri="check.png"/>
    <action activationType="background" content="remind me later" arguments="later" imageUri="cancel.png"/>
  </actions>
</toast>
```

![уведомление с действиями, пример 1](images/adaptivetoasts-xmlsample02.png)

 

**Уведомление с действиями, пример 2**

В этом примере показано...

```XML
<toast launch="app-defined-string">
  <visual>
    <binding template="ToastGeneric">
      <text>Cortana</text>
      <text>We noticed that you are near Wasaki.</text>
      <text>Thomas left a 5 star rating after his last visit, do you want to try?</text>
      <image placement="appLogoOverride" src="A.png" />
    </binding>
  </visual>
  <actions>
    <action activationType="foreground" content="reviews" arguments="reviews" />
    <action activationType="protocol" content="show map" arguments="bingmaps:?q=sushi" />
  </actions>
</toast>
```

![уведомление с действиями, пример 2](images/adaptivetoasts-xmlsample03.png)

 

**Уведомление с текстовым полем для ввода и действиями, пример 1**

В этом примере показано...

```XML
<toast launch="developer-defined-string">
  <visual>
    <binding template="ToastGeneric">
      <text>Andrew B.</text>
      <text>Shall we meet up at 8?</text>
      <image placement="appLogoOverride" src="A.png" />
    </binding>
  </visual>
  <actions>
    <input id="message" type="text" placeHolderContent="reply here" />
    <action activationType="background" content="reply" arguments="reply" />
    <action activationType="foreground" content="video call" arguments="video" />
  </actions>
</toast>
```

![уведомление с текстовым полем для ввода и действиями](images/adaptivetoasts-xmlsample04.png)

 

**Уведомление с текстовым полем для ввода и действиями, пример 2**

В этом примере показано...

```XML
<toast launch="developer-defined-string">
  <visual>
    <binding template="ToastGeneric">
      <text>Andrew B.</text>
      <text>Shall we meet up at 8?</text>
      <image placement="appLogoOverride" src="A.png" />
    </binding>
  </visual>
  <actions>
    <input id="message" type="text" placeHolderContent="reply here" />
    <action activationType="background" content="reply" arguments="reply" imageUri="send.png" hint-inputId="message"/>
  </actions>
</toast>
```

![уведомление с текстом и действиями для ввода](images/adaptivetoasts-xmlsample05.png)

 

**Уведомление с вводом выбора и действиями**

В этом примере показано...

```XML
<toast launch="developer-defined-string">
  <visual>
    <binding template="ToastGeneric">
      <text>Spicy Heaven</text>
      <text>When do you plan to come in tomorrow?</text>
      <image placement="appLogoOverride" src="A.png" />
    </binding>
  </visual>
  <actions>
    <input id="time" type="selection" defaultInput="2" >
  <selection id="1" content="Breakfast" />
  <selection id="2" content="Lunch" />
  <selection id="3" content="Dinner" />
    </input>
    <action activationType="background" content="Reserve" arguments="reserve" />
    <action activationType="background" content="Call Restaurant" arguments="call" />
  </actions>
</toast>
```

![уведомление с вводом выбора и действиями](images/adaptivetoasts-xmlsample06.png)

 

**Напоминание**

В этом примере показано...

```XML
<toast scenario="reminder" launch="developer-pre-defined-string">
  <visual>
    <binding template="ToastGeneric">
      <text>Adam&#39;s Hiking Camp</text>
      <text>You have an upcoming event for this Friday!</text>
      <text>RSVP before it"s too late.</text>
      <image placement="appLogoOverride" src="A.png" />
      <image placement="inline" src="hiking.png" />
    </binding>
  </visual>
  <actions>
    <action activationType="background" content="RSVP" arguments="rsvp" />
    <action activationType="background" content="Reminder me later" arguments="later" />
  </actions>
</toast>
```

![напоминание](images/adaptivetoasts-xmlsample07.png)

 

## Примеры активации


Как упомянуто выше, тело и действия во всплывающем уведомлении способны активировать приложения различными способами. В примере ниже показано, как обрабатывать различные типы активации из тела и/или действий всплывающего уведомления.

**Передний план**

В этом сценарии, за счет активации переднего плана, в ответ на действие внутри требующего действий всплывающего уведомления запускается приложение и открывается его соответствующее содержимое.

Раньше при активации из всплывающих уведомлений вызывалась команда OnLaunched(). В Windows 10 для всплывающих уведомлений предусмотрен свой тип активации, при котором вызывается OnActivated().

```
async protected override void OnActivated(IActivatedEventArgs args)
{
        //Initialize your app if it&#39;s not yet initialized;
    //Find out if this is activated from a toast;
    If (args.Kind == ActivationKind.ToastNotification)
    {
                //Get the pre-defined arguments and user inputs from the eventargs;
        var toastArgs = args as ToastNotificationActivatedEventArgs;
        var arguments = toastArgs.Arguments;
        var input = toastArgs.UserInput["1"]; 
}
     
    //...
}
```

**Фоновый режим**

В этом сценарии приложение использует фоновую задачу для обработки действия внутри интерактивного всплывающего уведомления. Приведенный ниже код показывает, как объявить эту фоновую задачу для обработки активации всплывающих уведомлений внутри манифеста вашего приложения и как получить аргументы от действия и вводных данных пользователя при нажатии кнопок.

```
<!-- Manifest Declaration -->
<!-- A new task type toastNotification is added -->
<Extension Category = "windows.backgroundTasks" 
EntryPoint = "Tasks.BackgroundTaskClass" >
  <BackgroundTasks>
    <Task Type="systemEvent" />
  </BackgroundTasks>
</Extension>
```

```
namespace ToastNotificationTask
{
    public sealed class ToastNotificationBackgroundTask : IBackgroundTask
    {
        public void Run(IBackgroundTaskInstance taskInstance)
        {
        //Inside here developer can retrieve and consume the pre-defined 
        //arguments and user inputs;
        var details = taskInstance.TriggerDetails as ToastNotificationActionTriggerDetail;
        var arguments = details.Arguments;
        var input = details.Input.Lookup("1");

            // ...
        }        
    }
}
```

## Схемы: &lt;visual&gt; и &lt;audio&gt;


На следующих схемах суффикс «?» означает, что атрибут необязателен.

```
<toast launch? duration? activationType? scenario? >
    <visual version? lang? baseUri? addImageQuery? >
        <binding template? lang? baseUri? addImageQuery? >
            <text lang? >content</text>
            <text />
            <image src placement? alt? addImageQuery? hint-crop? />
        </binding>
    </visual>
    <audio src? loop? silent? />
    <actions>
    </actions>
</toast>
```

**Атрибуты в элементе &lt;toast&gt;**

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

**Атрибуты в элементе &lt;visual&gt;**

version?

-   version? = nonNegativeInteger
-   Этот атрибут не является необходимым, поскольку управление версиями будет удалено из элемента &lt;visual&gt;. Следите за новой моделью управления версиями, которую вы при необходимости определите из более высокой иерархии.

lang?

-   Дополнительные сведения об этом необязательном атрибуте см. в [данной статье о схеме элементов](https://msdn.microsoft.com/library/windows/apps/br230847).

baseUri?

-   Дополнительные сведения об этом необязательном атрибуте см. в [данной статье о схеме элементов](https://msdn.microsoft.com/library/windows/apps/br230847).

addImageQuery?

-   Дополнительные сведения об этом необязательном атрибуте см. в [данной статье о схеме элементов](https://msdn.microsoft.com/library/windows/apps/br230847).

**Атрибуты в элементе &lt;binding&gt;**

template?

-   \[Important\] template? = "ToastGeneric"
-   Если вы используете новые функции адаптивных и интерактивных уведомлений, убедитесь, что вы начали использовать шаблон «ToastGeneric» вместо традиционного шаблона.
-   Традиционные шаблоны могут сейчас работать с новыми действиями, но они для этого не предназначены и мы не гарантируем, что они будут работать в дальнейшем.

lang?

-   Дополнительные сведения об этом необязательном атрибуте см. в [данной статье о схеме элементов](https://msdn.microsoft.com/library/windows/apps/br230847).

baseUri?

-   Дополнительные сведения об этом необязательном атрибуте см. в [данной статье о схеме элементов](https://msdn.microsoft.com/library/windows/apps/br230847).

addImageQuery?

-   Дополнительные сведения об этом необязательном атрибуте см. в [данной статье о схеме элементов](https://msdn.microsoft.com/library/windows/apps/br230847).

**Атрибуты в элементе &lt;text&gt;**

lang?

-   Дополнительные сведения об этом необязательном атрибуте см. в [данной статье о схеме элементов](https://msdn.microsoft.com/library/windows/apps/br230847).

**Атрибуты в элементе &lt;image&gt;**

src

-   Дополнительные сведения об этом обязательном атрибуте см. в [данной статье о схеме элементов](https://msdn.microsoft.com/library/windows/apps/br230844).

placement?

-   placement? = "inline" | "appLogoOverride"
-   Это необязательный атрибут.
-   Он указывает, где будет отображаться данное изображение.
-   «inline» означает внутри тела всплывающего уведомления под текстом; «appLogoOverride» означает замену значка приложения (в левом верхнем углу всплывающего уведомления).
-   Можно использовать одно изображение для каждого значения размещения.

alt?

-   Дополнительные сведения об этом необязательном атрибуте см. в [данной статье о схеме элементов](https://msdn.microsoft.com/library/windows/apps/br230844).

addImageQuery?

-   Дополнительные сведения об этом необязательном атрибуте см. в [данной статье о схеме элементов](https://msdn.microsoft.com/library/windows/apps/br230844).

hint-crop?

-   hint-crop? = "none" | "circle"
-   Это необязательный атрибут.
-   «none» является значением по умолчанию и означает отсутствие обрезки.
-   «circle» обрезает изображение до круглой формы. Используется для изображений профиля контактов, изображений пользователя и т. д.

**Атрибуты в элементе &lt;audio&gt;**

src?

-   Дополнительные сведения об этом необязательном атрибуте см. в [данной статье о схеме элементов](https://msdn.microsoft.com/library/windows/apps/br230842).

loop?

-   Дополнительные сведения об этом необязательном атрибуте см. в [данной статье о схеме элементов](https://msdn.microsoft.com/library/windows/apps/br230842).

silent?

-   Дополнительные сведения об этом необязательном атрибуте см. в [данной статье о схеме элементов](https://msdn.microsoft.com/library/windows/apps/br230842).

## Схемы: &lt;action&gt;


На следующих схемах суффикс «?» означает, что атрибут необязателен.

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

**Атрибуты в элементе &lt;input&gt;**

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

**Атрибуты в элементе &lt;selection&gt;**

id

-   Данный атрибут является обязательным. Он используется, чтобы определить выбор пользователя. Атрибут «id» возвращается в приложение.

content

-   Данный атрибут является обязательным. Он предоставляет строку для отображения этого элемента выбора.

**Атрибуты в элементе &lt;action&gt;**

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

## Атрибуты для обрабатываемых системой действий


Система может обработать такие действия, как откладывание и отключение уведомлений, если вы не хотите, чтобы ваше приложение обрабатывало откладывание или перенос уведомлений как фоновую задачу. Обрабатываемые системой действия можно объединить (или определить по отдельности), но при этом не рекомендуется реализовывать действие откладывания без действия отключения.

Сочетание системных команд: SnoozeAndDismiss

```
<toast>
    <visual>
    </visual>
    <audio />
    <actions hint-systemCommands? = "SnoozeAndDismiss" />
</toast>
```

Отдельные действия, обрабатываемые системой

```
<toast>
    <visual>
    </visual>
    <audio />
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

 

 







<!--HONumber=Aug16_HO3-->


