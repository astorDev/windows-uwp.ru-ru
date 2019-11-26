---
ms.assetid: 79C284CA-C53A-4C24-807E-6D4CE1A29BFA
description: В этом разделе описывается, как изменить веб-приложение PlayReady для поддержки изменений, внесенных в предыдущей версии Windows 8.1, в версию Windows 10.
title: Расширение зашифрованного мультимедиа PlayReady
ms.date: 02/08/2017
ms.topic: article
keywords: Windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: b673122d707e152d24c49d3bacf71ed52cdb0ae5
ms.sourcegitcommit: b52ddecccb9e68dbb71695af3078005a2eb78af1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74256811"
---
# <a name="playready-encrypted-media-extension"></a>Расширение зашифрованного мультимедиа PlayReady



В этом разделе описывается, как изменить веб-приложение PlayReady для поддержки изменений, внесенных в предыдущей версии Windows 8.1, в версию Windows 10.

С помощью элементов мультимедиа PlayReady в Internet Explorer разработчики могут создавать веб-приложения, способные предоставлять содержимое PlayReady пользователю, и одновременно применять правила доступа, заданные поставщиком содержимого. В данном разделе рассказывается, как добавлять элементы мультимедиа PlayReady в существующие веб-приложения, используя только HTML5 и JavaScript.

## <a name="whats-new-in-playready-encrypted-media-extension"></a>Новые возможности расширения зашифрованного мультимедиа PlayReady

В этом разделе содержится список изменений, внесенных в расширение "зашифрованного мультимедиа" PlayReady (EME), чтобы включить защиту содержимого PlayReady в Windows 10.

В следующем списке описаны новые функции и изменения, внесенные в расширение мультимедиа, зашифрованное с помощью PlayReady для Windows 10.

-   Добавлено аппаратное управление цифровыми правами.

    Поддержка аппаратной защиты содержимого обеспечивает безопасное воспроизведение содержимого высокого (HD) и сверхвысокого (UHD) разрешений на нескольких платформах устройств. Материал ключей (включая закрытые ключи, ключи содержимого и любой другой материал ключей, используемый для наследования или разблокировки указанных выше ключей), а также расшифрованные сжатые и несжатые примеры видео защищены с помощью аппаратной системы безопасности.

-   Обеспечивает заблаговременное получение несохраняемых лицензий.
-   Обеспечивает получение нескольких лицензий в одном сообщении.

    Можно либо использовать объект PlayReady с несколькими идентификаторами ключей (Кэйидс), как в Windows 8.1, либо использовать [данные модели расшифровки содержимого (кдмдата)](https://docs.microsoft.com/previous-versions/windows/apps/dn457361(v=ieb.10)?redirectedfrom=MSDN) с несколькими кэйидс.

    > [!NOTE]
    > В Windows 10 в разделе &lt;KeyID&gt; в Кдмдата поддерживаются несколько идентификаторов ключей.

-   Добавлена поддержка окончания срока действия в режиме реального времени или лицензии с ограниченной продолжительностью.

    Предоставляет возможность настраивать окончание срока действия для лицензий в режиме реального времени.

-   Добавлена поддержка политики стандарта HDCP Type 1 (версии 2.2).
-   Miracast теперь носит характер неявных выходных данных.
-   Добавлена безопасная остановка.

    Безопасная остановка предоставляет устройству PlayReady средства надежного подтверждения службе потоковой передачи мультимедиа того, что воспроизведение мультимедиа остановлено для указанного содержимого.

-   Добавлено разделение лицензирования звука и видео.

    Использование отдельных дорожек не позволяет декодировать видео как звук, обеспечивая более надежную защиту содержимого. Новые стандарты требуют отдельных ключей для аудио- и видеодорожек.

-   Добавлена функция MaxResDecode.

    Эта функция была добавлена для ограничения воспроизведения содержимого до максимального разрешения, даже при наличия ключа с большими возможностями (но не лицензии). Она используется в тех случаях, когда потоки различных размеров кодируются с использованием одного ключа.

## <a name="encrypted-media-extension-support-in-playready"></a>Поддержка расширения зашифрованного мультимедиа в PlayReady

В данном разделе описывается версия расширения зашифрованного мультимедиа W3C, поддерживаемого PlayReady.

В настоящее время PlayReady для веб-приложений описан в [черновом варианте расширения зашифрованного мультимедиа W3C от 10 мая 2013 г](https://www.w3.org/TR/2013/WD-encrypted-media-20130510/). В будущих версиях Windows эта поддержка будет изменена до обновленной спецификации расширения зашифрованного мультимедиа.

## <a name="use-hardware-drm"></a>Использование аппаратного управления цифровыми правами DRM

В данном разделе рассказывается о том, как ваше веб-приложение может использовать аппаратное управление цифровыми правами PlayReady и как отключить аппаратное управление цифровыми правами, если защищенное содержимое не поддерживает его.

Чтобы использовать аппаратное управление цифровыми правами PlayReady, ваше веб-приложение на языке JavaScript должно использовать метод расширения зашифрованного мультимедиа **isTypeSupported** с ключевым системным идентификатором `com.microsoft.playready.hardware` для запроса поддержки аппаратного управления цифровыми правами PlayReady от браузера.

Иногда некоторое содержимое не поддерживается аппаратным управлением цифровыми правами. Содержимое Cocktail никогда не поддерживается аппаратным управлением цифровыми правами. Если необходимо воспроизводить содержимое Cocktail, следует выключить аппаратное управление цифровыми правами. Некоторые виды аппаратного управления цифровыми правами поддерживают стандарт HEVC, а другие — нет. Если необходимо воспроизвести содержимое в формате HEVC, а аппаратное управление цифровыми правами не поддерживает его, можно выключить аппаратное управление цифровыми правами.

> [!NOTE]
> Чтобы определить, поддерживается ли содержимое в формате HEVC, после создания экземпляра `com.microsoft.playready` воспользуйтесь методом [**PlayReadyStatics.CheckSupportedHardware**](https://docs.microsoft.com/uwp/api/windows.media.protection.playready.playreadystatics.checksupportedhardware).

## <a name="add-secure-stop-to-your-web-app"></a>Добавление безопасной остановки в веб-приложение

В данном разделе рассказывается, как добавить безопасную остановку в веб-приложение.

Безопасная остановка предоставляет устройству PlayReady средства надежного подтверждения службе потоковой передачи мультимедиа того, что воспроизведение мультимедиа остановлено для указанного содержимого. Благодаря этой возможности службы потоковой передачи мультимедиа могут точно применять ограничения на использование и сообщать о них на различных устройствах для заданной учетной записи.

Для отправки запроса на безопасную остановку используются два указанных ниже основных сценария.

-   Когда презентация мультимедиа останавливается по завершении содержимого или когда пользователь остановил ее где-то в середине.
-   Когда неожиданно заканчивается предыдущий сеанс (например, из-за сбоя системы или приложения). При запуске или при завершении работы приложению потребуется сделать запрос на наличие невыполненных сеансов безопасной остановки и отправить запросы, не зависящие от любых других воспроизводящихся мультимедиа.

Ниже описано, как настроить безопасную остановку для различных сценариев.

Чтобы настроить безопасную остановку для обычного завершения презентации, выполните указанные ниже действия.

1.  Перед началом воспроизведения зарегистрируйте событие **onEnded**.
2.  Обработчику событий **onEnded** необходимо вызвать `removeAttribute("src")` из объекта видео- или аудиоэлемента, чтобы присвоить источнику значение **NULL**. После этого Media Foundation разрушит топологию и дескрипторы, а затем настроит состояние остановки.
3.  Вы можете начать сеанс CDM безопасной остановки в обработчике, чтобы отправить запрос на безопасную остановку на сервер и уведомить его о том, что воспроизведение остановлено. Это можно сделать и позже.

Чтобы настроить безопасную остановку в случае, если пользователь ушел со страницы или закрыл вкладку или браузер, выполните указанные ниже действия.

-   Для записи состояния остановки от приложения не требуется никаких действия. Запись будет выполнена автоматически.

Чтобы настроить безопасную остановку для пользовательских элементов управления страницей или действий пользователя (например, пользовательских кнопок навигации или запуска новой презентации до завершения текущей), выполните указанные ниже действия.

-   При возникновении пользовательских действий приложению необходимо присвоить источнику значение **NULL**. После этого Media Foundation разрушит топологию и дескрипторы, а затем настроит состояние остановки.

В примере ниже показано, как использовать безопасную остановку в веб-приложении.

```JavaScript
// JavaScript source code

var g_prkey = null;
var g_keySession = null;
var g_fUseSpecificSecureStopSessionID = false;
var g_encodedMeteringCert = 'Base64 encoded of your metering cert (aka publisher cert)';

// Note: g_encodedLASessionId is the CDM session ID of the proactive or reactive license acquisition 
//       that we want to initiate the secure stop process.
var g_encodedLASessionId = null;

function main()
{
    ...

    g_prkey = new MSMediaKeys("com.microsoft.playready");

    ...

    // add 'onended' event handler to the video element
    // Assume 'myvideo' is the ID of the video element
    var videoElement = document.getElementById("myvideo");
    videoElement.onended = function (e) { 

        //
        // Calling removeAttribute("src") will set the source to null
        // which will trigger the MF to tear down the topology, destroy the
        // decryptor(s) and set the stop state.  This is required in order
        // to set the stop state.
        //
        videoElement.removeAttribute("src");
        videoElement.load();

        onEndOfStream();
    };
}

function onEndOfStream()
{
    ...

    createSecureStopCDMSession();

    ...    
}

function createSecureStopCDMSession()
{
    try{    
        var targetMediaCodec = "video/mp4";
        var customData = "my custom data";

        var encodedSessionId = g_encodedLASessionId;
        if( !g_fUseSpecificSecureStopSessionID )
        {
            // Use "*" (wildcard) as the session ID to include all secure stop sessions
            // TODO: base64 encode "*" and place encoded result to encodedSessionId
        }

        var int8ArrayCDMdata = formatSecureStopCDMData( encodedSessionId, customData,  g_encodedMeteringCert );
        var emptyArrayofInitData = new Uint8Array();

        g_keySession = g_prkey.createSession(targetMediaCodec, emptyArrayofInitData, int8ArrayCDMdata);

        addPlayreadyKeyEventHandler();

    } catch( e )
    {
        // TODO: Handle exception
    }
}

function addPlayreadyKeyEventHandler()
{
    // add 'keymessage' eventhandler   
    g_keySession.addEventListener('mskeymessage', function (event) {

        // TODO: Get the keyMessage from event.message.buffer which contains the secure stop challenge
        //       The keyMessage format for the secure stop is similar to LA as below:
        //
        //            <PlayReadyKeyMessage type="SecureStop" >
        //              <SecureStop version="1.0" >
        //                <Challenge encoding="base64encoded">
        //                    secure stop challenge
        //                </Challenge>
        //                <HttpHeaders>
        //                    <HttpHeader>
        //                      <name>Content-Type</name>
        //                         <value>"content type data"</value>
        //                    </HttpHeader>
        //                    <HttpHeader>
        //                         <name>SOAPAction</name>
        //                         <value>soap action</value>
        //                     </HttpHeader>
        //                    ....
        //                </HttpHeaders>
        //              </SecureStop>
        //            </PlayReadyKeyMessage>
                
        // TODO: send the secure stop challenge to a server that handles the secure stop challenge

        // TODO: Receive and response and call event.target.Update() to proecess the response
    });
    
    // add 'keyerror' eventhandler
    g_keySession.addEventListener('mskeyerror', function (event) {
        var session = event.target;
        
        ...

        session.close();
    });
    
    // add 'keyadded' eventhandler
    g_keySession.addEventListener('mskeyadded', function (event) {
        
        var session = event.target;

        ...

        session.close();             
    });
}

/**
* desc@ formatSecureStopCDMData
*   generate playready CDMData
*   CDMData is in xml format:
*   <PlayReadyCDMData type="SecureStop">
*     <SecureStop version="1.0">
*       <SessionID>B64 encoded session ID</SessionID>
*       <CustomData>B64 encoded custom data</CustomData>
*       <ServerCert>B64 encoded server cert</ServerCert>
*     </SecureCert>
* </PlayReadyCDMData>        
*/
function formatSecureStopCDMData(encodedSessionId, customData, encodedPublisherCert) 
{
    var encodedCustomData = null;

    // TODO: base64 encode the custom data and place the encoded result to encodedCustomData

    var CDMDataStr = "<PlayReadyCDMData type=\"SecureStop\">" +
                     "<SecureStop version=\"1.0\" >" +
                     "<SessionID>" + encodedSessionId + "</SessionID>" +
                     "<CustomData>" + encodedCustomData + "</CustomData>" +
                     "<ServerCert>" + encodedPublisherCert + "</ServerCert>" +
                     "</SecureStop></PlayReadyCDMData>";
    
    var int8ArrayCDMdata = null

    // TODO: Convert CDMDataStr to Uint8 byte array and palce the converted result to int8ArrayCDMdata

    return int8ArrayCDMdata;
}
```

> [!NOTE]
> `<SessionID>B64 encoded session ID</SessionID>` данных для безопасного завершения в примере выше может быть звездочкой (\*), которая является подстановочным знаком для всех записанных сеансов безопасности. Это значит, что тег **SessionID** может быть конкретным сеансом или с подстановочным знаком (\*) для выбора всех сеансов безопасного завершения.

## <a name="programming-considerations-for-encrypted-media-extension"></a>Аспекты программирования, касающиеся расширения зашифрованного мультимедиа

В этом разделе перечислены вопросы программирования, которые следует учитывать при создании веб-приложения с поддержкой PlayReady для Windows 10.

Объекты **MSMediaKeys** и **MSMediaKeySession**, созданные приложением, должны оставаться активными, пока приложение не будет закрыто. Один из способов поддержания этих объектов в активном состоянии заключается в назначении их в качестве глобальных переменных (если переменные будут объявлены локальными переменными внутри функции, они окажутся вне диапазона и могут быть удалены в процессе сборки мусора). Например, в следующем примере присваиваются переменные *g\_мсмедиакэйс* и *g\_медиакэйсессион* в виде глобальных переменных, которые затем присваиваются объектам **мсмедиакэйс** и **мсмедиакэйсессион** в функции.

``` syntax
var g_msMediaKeys;
var g_mediaKeySession;

function foo() {
  ...
  g_msMediaKeys = new MSMediaKeys("com.microsoft.playready");
  ...
  g_mediaKeySession = g_msMediaKeys.createSession("video/mp4", intiData, null);
  g_mediaKeySession.addEventListener(this.KEYMESSAGE_EVENT, function (e) 
  {
    ...
    downloadPlayReadyKey(url, keyMessage, function (data) 
    {
      g_mediaKeySession.update(data);
    });
  });
  g_mediaKeySession.addEventListener(this.KEYADDED_EVENT, function () 
  {
    ...
    g_mediaKeySession.close();
    g_mediaKeySession = null;
  });
}
```

Дополнительные сведения см. в разделе [примеры приложений](https://code.msdn.microsoft.com/windowsapps/PlayReady-samples-for-124a3738).

## <a name="see-also"></a>См. также
- [Управление цифровыми правами PlayReady](playready-client-sdk.md)




