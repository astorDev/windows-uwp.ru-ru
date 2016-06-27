---
author: Karl-Bridge-Microsoft
Description: "Узнайте, как расширить возможности Кортаны за счет более гибких и естественных голосовых команд, которые позволяют пользователю произносить имя приложения в любом месте команды."
title: "Поддержка голосовых команд на естественном языке в Кортане"
ms.assetid: 281E068A-336A-4A8D-879A-D8715C817911
label: Support natural language voice commands
template: detail.hbs
ms.sourcegitcommit: 077fcc6ff462a771ed56f875d960e46e6f4420fc
ms.openlocfilehash: c9cd6894c61f3d6cfe770d197317a97a804b4119

---

# Поддержка голосовых команд на естественном языке в Кортане

Расширьте возможности **Кортаны** за счет более гибких и естественных голосовых команд, которые позволяют пользователю произносить имя приложения в любом месте команды.

**Важные API**

-   [**Windows.ApplicationModel.VoiceCommands**](https://msdn.microsoft.com/library/windows/apps/dn706594)
-   [**Элементы и атрибуты определения голосовых команд (VCD), версия 1.2**](https://msdn.microsoft.com/library/windows/apps/dn706593)


Для использования голосовых команд с целью расширения возможностей Кортаны из приложения пользователю необходимо указать приложение и команду или функцию, которую необходимо выполнить. Для этого обычно приходится объявлять имя приложения в начале или в конце голосовой команды. Например, "Adventure Works, добавь новую поездку в Лас-Вегас".

Однако, имя приложения, заданное таким образом, может звучать нескладно и неестественно, либо оно может не иметь смысла. Во многих случаях произносить имя приложения в любом месте команды — более удобно и естественно, таким образом, взаимодействие становится более интуитивным и привлекательным для пользователя. Наш предыдущий пример "Adventure Works, добавь новую поездку в Лас-Вегас". можно перефразировать как "Добавь новую поездку Adventure Works в Лас-Вегас". или "Добавь новую поездку в Лас-Вегас с помощью Adventure Works".

Вы можете настроить свои голосовые команды на поддержку имени приложения как:

-   префикса — перед фразой команды;
-   инфикса — внутри фразы команды;
-   суффикса — после фразы команды.

**Необходимые условия.  **

В этом разделе используются сведения из раздела [Запуск фонового приложения с помощью голосовых команд в Кортане](launch-a-background-app-with-voice-commands-in-cortana.md). Мы продолжим демонстрацию этих функций на примере приложения для планирования поездок и управления ими под названием **Adventure Works**.

Если вы — начинающий разработчик приложений универсальной платформы Windows (UWP), прочитайте указанные ниже статьи, чтобы ознакомиться с описанными здесь технологиями.

-   [Создание первого приложения](https://msdn.microsoft.com/library/windows/apps/bg124288)
-   Дополнительную информацию о событиях см. в разделе [Общие сведения о событиях и перенаправленных событиях](https://msdn.microsoft.com/library/windows/apps/mt185584).

**Рекомендации по взаимодействию с пользователем.  **

Сведения об интеграции приложения с **Кортаной** см. в [рекомендациях по проектированию для Кортаны](https://msdn.microsoft.com/library/windows/apps/dn974233), а полезные советы по проектированию удобного и привлекательного приложения с поддержкой распознавания речи — в [рекомендациях по проектированию голосовых функций](https://msdn.microsoft.com/library/windows/apps/dn596121).

## <span id="Specify_an_AppName_element_in_the_VCD"></span><span id="specify_an_appname_element_in_the_vcd"></span><span id="SPECIFY_AN_APPNAME_ELEMENT_IN_THE_VCD"></span>Укажите элемент **AppName** в VCD


Элемент **AppName** используется для определения понятного имени приложения в голосовой команде.
```XML
<AppName>Adventure Works</AppName>
```

## <span id="Specify_where_the_app_name_can_be_spoken_in_the_voice_command"></span><span id="specify_where_the_app_name_can_be_spoken_in_the_voice_command"></span><span id="SPECIFY_WHERE_THE_APP_NAME_CAN_BE_SPOKEN_IN_THE_VOICE_COMMAND"></span>Определите, в каком месте голосовой команды можно произносить имя приложения


Элемент **ListenFor** имеет атрибут **RequireAppName**, который указывает, в каком месте голосовой команды может прозвучать имя приложения. Этот атрибут поддерживает четыре значения.

1.  **BeforePhrase**

    По умолчанию.

    Показывает, что пользователи должны произносить имя приложения перед фразой команды.

    Здесь Кортана ожидает услышать "Adventure Works, когда я еду в Лас-Вегас".
```xml
<ListenFor RequireAppName="BeforePhrase"> show [my] trip to {destination} </ListenFor>
```

2.  **AfterPhrase**

    Показывает, что пользователи должны произносить имя приложения после фразы команды.

    Список локализованных фраз для предложных связок предоставляется системой. Сюда входят такие фразы, как "с помощью", "при помощи" и "в".

    Здесь Кортана ожидает услышать такие команды, как "Покажи мою следующую поездку в Лас-Вегас в AdventureWorks" и "Покажи мою следующую поездку в Лас-Вегас с помощью Adventure Works".
```xml
<ListenFor RequireAppName="AfterPhrase">show [my] next trip to {destination} </ListenFor>
```

3.  **BeforeOrAfterPhrase**

    Показывает, что пользователи должны произносить имя приложения перед фразой команды или после нее.

    В версии суффикса список локализованных фраз для предложных связок предоставляется системой. Сюда входят такие фразы, как "с помощью", "при помощи" и "в".

    Здесь Кортана ожидает услышать такие команды, как "Adventure Works, покажи мою следующую поездку в Лас-Вегас" и "Покажи мою следующую поездку в Лас-Вегас в Adventure Works".
``` xml
<ListenFor RequireAppName="BeforeOrAfterPhrase">show [my] next trip to {destination}</ListenFor>
```

4.  **ExplicitlySpecified**

    Показывает, что пользователи должны произносить имя приложения точно в том месте фразы команды, которое вы определили. От пользователя не требуется произносить имя приложения перед фразой или после нее.

    Вы должны явно ссылаться на имя своего приложения, используя тег **{builtin:AppName}**.

    Здесь Кортана ожидает услышать такие команды, как "Adventure Works, покажи мою следующую поездку в Лас-Вегас" и "Покажи мою следующую поездку Adventure Works в Лас-Вегас".
```xml
<ListenFor RequireAppName="ExplicitlySpecified">show [my] next {builtin:AppName} trip to {destination} </ListenFor>
```

## <span id="Special_cases"></span><span id="special_cases"></span><span id="SPECIAL_CASES"></span>Особые случаи

Когда вы объявляете элемент **ListenFor**, где **RequireAppName** имеет значение "AfterPhrase" или "ExplicitlySpecified", вы должны обеспечить соответствие определенным требованиям.

1.  **{builtin:AppName}** должен появляться только раз, если параметр **RequireAppName** имеет значение "ExplicitlySpecified".

    С этим значением система не может предположить, где в голосовой команде может появляться имя приложения. Вы должны явно указать место.

2.  Голосовая команда не может начинаться с элемента **PhraseTopic**, который обычно используется для распознавания речи по большому словарю. Перед ним должно быть хотя бы одно слово.

    Это помогает свести к минимуму вероятность того, что **Кортана** запустит ваше приложение, если команда содержит имя вашего приложения или его часть в любом месте изречения.

    Вот пример недопустимого объявления, которое может привести к тому, что **Кортана** запустит приложение **Adventure Works**, если пользователь скажет что-то в духе "Покажи мне рецензии на приключенческие работы Kinect".
```xml
<ListenFor RequireAppName="ExplicitlySpecified">{searchPhrase} {builtin:AppName}</ListenFor>
```

3.  В строке **ListenFor** должно быть еще хотя бы два слова помимо имени приложения и ссылок на элементы **PhraseTopic**.

    Подобно случаю 2, вы должны убедиться, что ваши команды содержат достаточно фонетического содержимого, чтобы снизить вероятность случайного запуска вашего приложения

    Это помогает настроить приложение как можно успешнее, чтобы ваше приложение не запускалось неправильно, когда пользователь говорит, например, "Найди приключенческие работы Kinect".

    Вот примеры недопустимых объявлений, которые могут привести к тому, что **Кортана** запустит приложение **Adventure Works**, если пользователь произнесет что-то в духе "Эй, приключенческие работы" или "Найди приключенческие работы Kinect".
```xml
<ListenFor RequireAppName="ExplicitlySpecified">Hey {builtin:AppName}</ListenFor>
<ListenFor RequireAppName="ExplicitlySpecified">Find {searchPhrase} {builtin:AppName}</ListenFor>
```

## <span id="Remarks"></span><span id="remarks"></span><span id="REMARKS"></span>Комментарии

Поддержка большего количества вариантов того, как пользователь может произнести голосовую команду, в **Кортане** также повышает общее удобство использования приложения.

Постарайтесь не использовать "Привет, \[имя приложения\]!" в качестве **AppName** или **CommandPrefix**. Вероятнее всего, пользователи будут говорить "Привет, Кортана!", чтобы вызвать Кортану через активацию голосом, а фраза "Привет, [имя приложения]!" звучит неестественно. Например, "Привет, Кортана! Покажи мою следующую поездку в Лас-Вегас в Adventure Works".

Попробуйте добавить варианты инфиксов/суффиксов в существующие голосовые команды. Как мы уже показали, добавление дополнительного атрибута к существующим элементам **ListenFor** и поддержка вариантов суффиксов не требуют значительных усилий. Гораздо естественней сказать "Привет, Кортана! Покажи мою следующую поездку в Лас-Вегас в Adventure Works", чем "Привет, Кортана! Adventure Works, покажи мою следующую поездку в Лас-Вегас".

Попробуйте использовать имя приложения как префикс в случаях, когда голосовая команда конфликтует с существующей функцией **Кортаны** (вызов, отправка сообщений и т. д.). Например, "Adventure Works, свяжись с \[туристический агент\] насчет поездки в Лас-Вегас".

## <span id="Complete_example"></span><span id="complete_example"></span><span id="COMPLETE_EXAMPLE"></span>Полный пример


Вот VCD-файл, в котором показаны различные способы предоставить более естественные голосовые команды.

**Примечание**. Допустимо иметь несколько элементов **ListenFor** с разным значением атрибута **RequireAppName**. 

```XML
<?xml version="1.0" encoding="utf-8"?>
<VoiceCommands xmlns="http://schemas.microsoft.com/voicecommands/1.1">
  <CommandSet xml:lang="en-us" Name="commandSet_en-us">
    <AppName>Adventure Works</AppName>
    <Example> When is my trip to Las Vegas? </Example>
    <Command Name="whenIsTripToDestination">
      <Example> When is my trip to Las Vegas?</Example>
      <ListenFor RequireAppName="BeforePhrase">
        when is my] trip to {destination} </ListenFor>

      <!-- This ListenFor command will set up Cortana to accept commands like 
           “Show my next trip to Las Vegas on Adventure Works”; “Show my next 
           trip to Las Vegas using Adventure Works” -->
      <ListenFor RequireAppName="AfterPhrase">
        show [my] next trip to {destination} </ListenFor>

      <!-- This ListenFor command will set up Cortana to accept commands when 
           the user specifies your app name either before or after the command. 
           “Adventure Works, show my next trip to Las Vegas”; 
           “Show my next trip to Last Vegas on Adventure works” -->
      <ListenFor RequireAppName="BeforeOrAfterPhrase">
        show [my] next trip to {destination} </ListenFor>

      <!-- This ListenFor command will set up Cortana to accept commands 
           when the user specifies your app name inline. 
           “Show my next Adventure Works trip to Las Vegas” -->
      <ListenFor RequireAppName="ExplicitlySpecified">
        show [my] next {builtin:AppName} trip to {destination} </ListenFor>

      <Feedback> Looking for trip to {destination} </Feedback>
      <Navigate />
    </Command>
    <PhraseList Label="destination">
      <Item> Las Vegas </Item>
      <Item> Dallas </Item>
      <Item> New York </Item>
    </PhraseList>
  </CommandSet>
  <!-- Other CommandSets for other languages -->
</VoiceCommands>
```

## <span id="related_topics"></span>Статьи по теме


**Разработчикам**
* [Взаимодействие с Кортаной](cortana-interactions.md)
* [Определение настраиваемых ограничений распознавания](define-custom-recognition-constraints.md)
* [**Элементы и атрибуты в VCD-файле, версия 1.2**](https://msdn.microsoft.com/library/windows/apps/dn706593)

**Проектировщикам**
* [Рекомендации по проектированию Кортаны](https://msdn.microsoft.com/library/windows/apps/dn974233)
* [Рекомендации по проектированию голосовых функций](https://msdn.microsoft.com/library/windows/apps/dn596121)

**Примеры**
* [Пример голосовой команды Кортаны](http://go.microsoft.com/fwlink/p/?LinkID=619899)
 

 







<!--HONumber=Jun16_HO3-->


