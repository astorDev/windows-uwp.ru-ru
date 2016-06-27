---
author: Karl-Bridge-Microsoft
Description: "Узнайте, как получить доступ к списку поддерживаемых фраз (элементы PhraseList) в файле определения голосовых команд (VCD) и обновить его с помощью результата распознавания речи во время выполнения."
title: "Динамическое изменение списков фраз в формате VCD"
ms.assetid: 98024EAC-EC0E-44AA-AEC5-A611BA7C5884
label: Modify VCD phrase lists
template: detail.hbs
ms.sourcegitcommit: a4e9a90edd2aae9d2fd5d7bead948422d43dad59
ms.openlocfilehash: 623243b94cf8ef6b276f8f2971af7bbdbdece81c

---

# Динамическое изменение списков фраз в формате VCD





**Важные API**

-   [**Windows.ApplicationModel.VoiceCommands**](https://msdn.microsoft.com/library/windows/apps/dn706594)
-   [**Элементы и атрибуты в VCD-файле, версия 1.2**](https://msdn.microsoft.com/library/windows/apps/dn706593)

Откройте и обновите список поддерживаемых фраз (элементы **PhraseList**) в файле определения голосовых команд (VCD) во время выполнения с помощью результата распознавания речи во время выполнения.

Динамическое изменения списка фраз во время выполнения может быть полезно, если голосовая команда специфична для задачи, касающейся определенного пользователем или временных данных приложения. 

Пусть, например, есть приложение для путешествий, в котором пользователи могут вводить места назначения, и вы хотите предоставить пользователям возможность запускать приложение, называя имя приложения и после него "Покажи поездку в &lt;пункт назначения&gt;". В самом элементе **ListenFor** можно указать, например, `<ListenFor> Show trip to {destination}  </ListenFor>`, где destination является значением атрибута **Label** для **PhraseList**.

Обновление списка фраз во время выполнения устраняет создание отдельного элемента **ListenFor** для каждого возможного назначения. Вместо этого можно динамически заполнять элемент **PhraseList** с назначениями, указанными пользователями при вводе своих маршрутов. 

Дополнительные сведения о **PhraseList** и других элементах VCD см. в разделе [**Элементы и атрибуты VCD вер. 1.2**](https://msdn.microsoft.com/library/windows/apps/dn706593).

**Предварительные требования:  **

В этом разделе используются сведения из раздела [Запуск приложения переднего плана с помощью голосовых команд в Кортане](launch-a-foreground-app-with-voice-commands-in-cortana.md). Мы продолжим демонстрацию этих функций на примере приложения для планирования поездок и управления ими под названием **Adventure Works**.

Если вы — начинающий разработчик приложений универсальной платформы Windows (UWP), прочитайте указанные ниже статьи, чтобы ознакомиться с описанными здесь технологиями.

-   [Создание первого приложения](https://msdn.microsoft.com/library/windows/apps/bg124288)
-   Дополнительную информацию о событиях см. в разделе [Общие сведения о событиях и перенаправленных событиях](https://msdn.microsoft.com/library/windows/apps/mt185584).

**Рекомендации по взаимодействию с пользователем.  **

Сведения об интеграции приложения с **Кортаной** см. в [рекомендациях по проектированию Кортаны](https://msdn.microsoft.com/library/windows/apps/dn974233), а полезные советы по проектированию удобного и привлекательного приложения с поддержкой распознавания речи — в [рекомендациях по проектированию голосовых функций](https://msdn.microsoft.com/library/windows/apps/dn596121).

## <span id="Identify_the_command"></span><span id="identify_the_command"></span><span id="IDENTIFY_THE_COMMAND"></span>Определение команды и обновление списка фраз

Вот пример VCD файла, который определяет **команду** "showTripToDestination" и **PhraseList**, которые определяют три параметра для назначения в нашем приложении для путешествий **Adventure Works**. По мере того как пользователь сохраняет и удаляет места назначения в приложении, приложение обновляет варианты в элементе **PhraseList**.

```XML
<?xml version="1.0" encoding="utf-8"?>
<VoiceCommands xmlns="http://schemas.microsoft.com/voicecommands/1.1">
  <CommandSet xml:lang="en-us" Name="AdventureWorksCommandSet_en-us">
    <CommandPrefix> Adventure Works, </CommandPrefix>
    <Example> Show trip to London </Example>

    <Command Name="showTripToDestination">
      <Example> show trip to London  </Example>
      <ListenFor> show trip to {destination} </ListenFor>
      <Feedback> Showing trip to {destination} </Feedback>
      <Navigate/>
    </Command>

    <PhraseList Label="destination">
      <Item> London </Item>
      <Item> Dallas </Item>
      <Item> New York </Item>
    </PhraseList>

  </CommandSet>

<!-- Other CommandSets for other languages -->

</VoiceCommands>

```

Чтобы обновить элемент **PhraseList** в VCD-файле, получите элемент **CommandSet**, который содержит список фраз. Используйте атрибут **Name** этого элемента **CommandSet** (значение **Name** должно быть уникальным в VCD-файле) в качестве ключа для доступа к свойству [**VoiceCommandManager.InstalledCommandSets**](https://msdn.microsoft.com/library/windows/apps/dn653257) и получения ссылки на [**VoiceCommandSet**](https://msdn.microsoft.com/library/windows/apps/dn653258).

После определения набора команд получите ссылку на список фраз, который хотите изменить, и вызовите метод [**SetPhraseListAsync**](https://msdn.microsoft.com/library/windows/apps/dn653261); используйте атрибут **Label** элемента **PhraseList** и массив строк в качестве нового содержимого списка фраз.

**Примечание.** При изменении списка фраз заменяется весь список. Если вы хотите вставить новые элементы в список фраз, необходимо указать как существующие, так и новые элементы при вызове [**SetPhraseListAsync**](https://msdn.microsoft.com/library/windows/apps/dn653261).

В этом примере мы обновляем элемент **PhraseList**, показанный в предыдущем примере, дополнительным местом назначения в Фениксе.

```CSharp
Windows.ApplicationModel.VoiceCommands.VoiceCommnadDefinition.VoiceCommandSet commandSetEnUs;

if (Windows.ApplicationModel.VoiceCommands.VoiceCommandDefinitionManager.
      InstalledCommandSets.TryGetValue(
        "AdventureWorksCommandSet_en-us", out commandSetEnUs))
{
  await commandSetEnUs.SetPhraseListAsync(
    "destination", new string[] {“London”, “Dallas”, “New York”, “Phoenix”});
}
```

## <span id="Remarks"></span><span id="remarks"></span><span id="REMARKS"></span>Комментарии


Использование элемента **PhraseList** для ограничения распознавания подходит для относительно небольшого набора слов. Если набор слов слишком большой (например, сотни слов) или вообще не должен ограничиваться, используйте элемент **PhraseTopic** и элемент **Subject**, чтобы уточнить релевантность результатов распознавания речи и улучшить масштабируемость.

В нашем примере используется элемент **PhraseTopic** со **сценарием** «Поиск», который затем уточняется элементом **Subject** «Город\\штат»

```XML
<?xml version="1.0" encoding="utf-8"?>
<VoiceCommands xmlns="http://schemas.microsoft.com/voicecommands/1.1">
  <CommandSet xml:lang="en-us" Name="AdventureWorksCommandSet_en-us">
    <CommandPrefix> Adventure Works, </CommandPrefix>
    <Example> Show trip to London </Example>

    <Command Name="showTripToDestination">
      <Example> show trip to London  </Example>
      <ListenFor> show trip to {destination} </ListenFor>
      <Feedback> Showing trip to {destination} </Feedback>
      <Navigate/>
    </Command>

    <PhraseList Label="destination">
      <Item> London </Item>
      <Item> Dallas </Item>
      <Item> New York </Item>
    </PhraseList>

    <PhraseTopic Label="destination" Scenario="Search">
      <Subject>City/State</Subject>
    </PhraseTopic>

  </CommandSet>
```

## <span id="related_topics"></span>Связанные разделы


**Разработчикам**
* [Взаимодействие с Кортаной](cortana-interactions.md)
* [Запуск приложения переднего плана с помощью голосовых команд в Кортане](launch-a-foreground-app-with-voice-commands-in-cortana.md)
* [Запуск фонового приложения с помощью голосовых команд в Кортане](launch-a-background-app-with-voice-commands-in-cortana.md)
* [**Элементы и атрибуты в VCD-файле, версия 1.2**](https://msdn.microsoft.com/library/windows/apps/dn706593)

**Проектировщикам**
* [Рекомендации по проектированию Кортаны](https://msdn.microsoft.com/library/windows/apps/dn974233)
* [Рекомендации по проектированию голосовых функций](https://msdn.microsoft.com/library/windows/apps/dn596121)

**Примеры**
* [Пример голосовой команды Кортаны](http://go.microsoft.com/fwlink/p/?LinkID=619899)
 

 







<!--HONumber=Jun16_HO3-->


