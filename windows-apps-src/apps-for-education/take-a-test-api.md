---
author: TylerMSFT
Description: "API JavaScript для приложения Microsoft «Тестирование» позволяет проводить безопасное тестирование. Приложение &quot;Тестирование&quot; предоставляет защищенный браузер, не позволяющий учащимся использовать другие ресурсы компьютера или интернет-ресурсы во время теста."
title: "API JavaScript для приложения &quot;Тестирование&quot;."
translationtype: Human Translation
ms.sourcegitcommit: 7f578d73a9a625b0ac7d9c10f6dc8118c36b07d0
ms.openlocfilehash: c2e1832489d36f4ccbeae4e2f67e18caf941a68f

---

# API JavaScript для приложения "Тестирование"

[Тестирование](https://technet.microsoft.com/edu/windows/take-tests-in-windows-10) — это браузерное приложение, отображающие защищенные онлайн-тесты для проведения важных тестирований. Оно поддерживает стандарт API браузера SBAC для проведения важных тестов по основным предметам и позволяет сосредоточиться на содержимом теста, а не на поиске способов заблокировать доступ в Windows.

Приложение "Тестирование" основано на браузере Microsoft Edge и предоставляет API-интерфейс JavaScript, который веб-приложения могут использовать для ограничения взаимодействия при прохождении тестов.

Этот API-интерфейс (основанный на API [Common Core SBAC](http://www.smarterapp.org/documents/SecureBrowserRequirementsSpecifications_0-3.pdf)) предоставляет возможности преобразования текста в речь и отправки запросов о том, заблокировано ли устройство, какие пользовательские и системные процессы в настоящее время выполняются и многое другое.

Дополнительные сведения о самом приложении см. в [техническом справочнике по приложению "Тестирование"](https://technet.microsoft.com/en-us/edu/windows/take-a-test-app-technical?f=255&MSPPError=-2147217396).

> [!Important]
> Эти API не работают в удаленном сеансе.  

Справку по устранению неполадок см. в разделе [Устранение неполадок приложения "Тестирование" с помощью средства просмотра событий](troubleshooting.md).

## Справочная документация
API-интерфейс приложения "Тестирование" содержит следующие пространства имен. 

| Пространство имен | Описание |
|-----------|-------------|
|[Пространство имен security](#security-namespace)|Позволяет блокировать устройство|
|[Пространство имен tts](#tts-namespace)|Возможность преобразования текста в речь|


 ### Пространство имен security

Пространство имен security позволяет заблокировать устройство, проверить список процессов пользователя и системы, получить MAC- и IP-адреса и очистить кэшированные веб-ресурсы.

| Метод | Описание   |
|--------|---------------|
|[clearCache](#clearCache) | Очищает кэшированные веб-ресурсы |
|[close](#close) | Закрывает браузер и разблокирует устройство |
|[enableLockDown](#enableLockDown) | Блокирует устройство. Также используется, чтобы разблокировать устройство. |
|[getIPAddressList](#getIPAddressList) | Получает список IP-адресов устройства. |
|[getMACAddress](#getMACAddress)|Получает список MAC-адресов устройства.|
|[getProcessList](#getProcessList)|Получает список выполняемых процессов пользователя и системы|
|[isEnvironmentSecure](#isEnvironmentSecure)|Определяет, применяется ли по-прежнему на устройстве контекст блокировки.|  

---
<span id="clearCache"/>
### void clearCache()
Очищает кэшированные веб-ресурсы.

**Синтаксис**  
`browser.security.clearCache();`

**Параметры**  
`None`

**Возвращаемое значение**  
`None`

**Требования**  
Windows 10 версии 1607

---

<span id="close"/>
### close(boolean restart)
Закрывает браузер и разблокирует устройство.

**Синтаксис**  
`browser.security.close(false);`

**Параметры**  
`restart` — этот параметр не учитывается но должен быть задан.

**Возвращаемое значение**  
`None`

**Требования**  
Windows10 версии 1607

---

<span id="enableLockDown"/>
### enableLockdown(boolean lockdown)
Блокирует устройство. Также используется, чтобы разблокировать устройство.

**Синтаксис**  
`browser.security.enableLockDown(true|false);`

**Параметры**  
`lockdown` - `true` используется для запуска приложения «Тестирование» на экране блокировки и применения политик, описанных в данном [документе](https://technet.microsoft.com/en-us/edu/windows/take-a-test-app-technical?f=255&MSPPError=-2147217396). `False` останавливает выполняемое на экране блокировки приложение «Тестирование» и закрывает его, если приложение не заблокировано. В противном случае ничего не происходит.

**Возвращаемое значение**  
`None`

**Требования**  
Windows10 версии 1607

---

<span id="getIPAddressList"/>
### string[] getIPAddressList()
Получает список IP-адресов устройства.

**Синтаксис**  
`browser.security.getIPAddressList();`

**Параметры**  
`None`

**Возвращаемое значение**  
`An array of IP addresses.`

---

<span id="getMACAddress" />
### string[] getMACAddress()
Получает список MAC-адресов устройства.

**Синтаксис**  
`browser.security.getMACAddress();`

**Параметры**  
`None`

**Возвращаемое значение**  
`An array of MAC addresses.`

**Требования**  
Windows 10 версии 1607

---

<span id="getProcessList" />
### string[] getProcessList()
Получает список выполняемых процессов пользователя.

**Синтаксис**  
`browser.security.getProcessList();`

**Параметры**  
`None`

**Возвращаемое значение**  
`An array of running process names.`

**Примечание.** Список не включает системные процессы.

**Требования**  
Windows 10 версии 1607

---

<span id="isEnvironmentSecure" />
### boolean isEnvironmentSecure()
Определяет, применяется ли по-прежнему на устройстве контекст блокировки.

**Синтаксис**  
`browser.security.isEnvironmentSecure();`

**Параметры**  
`None`

**Возвращаемое значение**  
`True indicates that the lockdown context is applied to the device; otherwise false.`

**Требования**  
Windows10 версии 1607

---

### Пространство имен tts

Пространство имен tts обслуживает функцию преобразования текста в речь в составе приложения.

| Метод | Описание |
|--------|-------------|
|[getStatus](#getStatus) | Получает состояние воспроизведения речи.|
|[getVoices](#getVoices) | Получает список доступных голосовых пакетов.|
|[pause](#pause)|Приостанавливает синтез речи.|
|[resume](#resume)|Возобновляет приостановленный синтез речи.|
|[speak](#speak)|Синтез речи на стороне клиента при преобразовании текста в речь|
|[stop](#stop)|Останавливает синтез речи|

> [!Tip]
> [API синтеза речи Microsoft Edge](https://blogs.windows.com/msedgedev/2016/06/01/introducing-speech-synthesis-api/) является реализацией [API голосовых функций W3C](https://dvcs.w3.org/hg/speech-api/raw-file/tip/webspeechapi.html). Разработчикам рекомендуется использовать этот API-интерфейс, когда это возможно.

---

<span id="getStatus" />
### string getStatus()
Получает состояние воспроизведения речи.

**Синтаксис**  
`browser.tts.getStatus();`

**Параметры**  
`None`

**Возвращаемое значение**  
`The speech playback status. Possible values are: “available”, “idle”, “paused”, and “speaking”.`

**Требования**  
Windows 10 версии 1607

---

<span id="getVoices" />
### string[] getVoices()
Получает список доступных голосовых пакетов.

**Синтаксис**  
`browser.tts.getVoices();`

**Параметры**  
`None`

**Возвращаемое значение**  
`The available voice packs. For example: “Microsoft Zira Mobile”, “Microsoft Mark Mobile”`

**Требования**  
Windows 10 версии 1607

---

<span id="pause" />
### void pause()

Приостанавливает синтез речи.

**Синтаксис**  
`browser.tts.pause();`

**Параметры**

`None`

**Возвращаемое значение**

`None`

**Требования**  
Windows 10 версии 1607

---

<span id="resume" />
### void resume()
Возобновляет приостановленный синтез речи.

**Синтаксис**  
`browser.tts.resume();`

**Параметры**
`None`

**Возвращаемое значение**
`None`

**Требования**  
Windows 10 версии 1607

---

<span id="speak" />
### void speak(string text, object options, function callback)
Запускает синтез речи на стороне клиента при преобразовании текста в речь.

**Синтаксис**  
`void browser.tts.speak(“Hello world”, options, callback);`

**Параметры**  
`Speech options such as gender, pitch, rate, volume. For example:`  
```
var options = {
   'gender': this.currentGender,
   'language': this.currentLanguage,
   'pitch': 1,
   'rate': 1,
   'voice': this.currentVoice,
   'volume': 1
};
```

**Возвращаемое значение**  
`None`

**Примечание.** Переменные параметров задаются срочными буквами. Параметры gender (пол), language (язык) и voice (голос) принимают строки.
Volume (громкость), pitch (высота) и rate (скорость) указываются в файл языка SSML, а не в объекте параметров.
В объекте параметров необходимо соблюдать порядок следования, именование и регистр, представленные в примере выше.

**Требования**  
Windows 10 версии 1607

---

<span id="stop" />
### void stop()
Останавливает синтез речи.

**Синтаксис**  
`void browser.tts.speak(“Hello world”, options, callback);`

**Параметры**  
`None`

**Возвращаемое значение**  
`None`

**Требования**  
Windows 10 версии 1607



<!--HONumber=Nov16_HO1-->


