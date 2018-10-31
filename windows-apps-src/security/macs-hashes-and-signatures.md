---
title: Коды проверки подлинности сообщений, хэши и подписи
description: В этой статье обсуждается выявление несанкционированных изменений сообщений с помощью кодов проверки подлинности сообщения (MAC), хэшей и подписей в приложениях универсальной платформы Windows (UWP).
ms.assetid: E674312F-6678-44C5-91D9-B489F49C4D3C
author: msatranjr
ms.author: misatran
ms.date: 02/08/2017
ms.topic: article
keywords: Windows 10, uwp, безопасность
ms.localizationpriority: medium
ms.openlocfilehash: b472a7209ddb9e67f7bb7c00e967295892568a29
ms.sourcegitcommit: ca96031debe1e76d4501621a7680079244ef1c60
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/31/2018
ms.locfileid: "5831094"
---
# <a name="macs-hashes-and-signatures"></a>Коды проверки подлинности сообщений, хэши и подписи




В этой статье обсуждается выявление несанкционированных изменений сообщений с помощью кодов проверки подлинности сообщения (MAC), хэшей и подписей в приложениях универсальной платформы Windows (UWP).

## <a name="message-authentication-codes-macs"></a>Коды проверки подлинности сообщения (MAC)


С помощью шифрования можно помешать злоумышленнику прочитать сообщение, но нельзя защитить сообщение от изменений. Цена фальсификации сообщения может оказаться очень большой, даже если текст изменен на бессмыслицу. С помощью кода проверки подлинности сообщения (MAC) можно выявить несанкционированное изменение передаваемой информации. В качестве примера рассмотрим следующую ситуацию.

-   Владимир и Юлия владеют общим секретным ключом и договорились об использовании определенной функции, выдающей код проверки подлинности сообщения.
-   Владимир создает сообщение и вместе с секретным ключом передает его функции, которая выдает код проверки подлинности сообщения.
-   Затем Владимир отправляет Юлии по сети сообщение \[в незашифрованном виде\] вместе с кодом проверки подлинности сообщения.
-   Полученное сообщение вместе с секретным ключом Юлия передает функции, которая выдает код проверки подлинности сообщения. Этот код Юлия сравнивает с кодом, полученным от Владимира. Если они совпадают, это означает, что сообщение не подвергалось изменениям в процессе передачи.

При этом у Инны, которая перехватывает сообщение Владимира Юлии, не получится подделать это сообщение. У Инны нет доступа к секретному ключу, и она, соответственно, не сможет генерировать код проверки подлинности сообщения, при помощи которого поддельное сообщение для Юлии выглядело бы подлинным.

Использование кода проверки подлинности сообщения позволяет убедиться только в том, что исходное сообщение не было изменено, а использование общего секретного ключа— убедиться, что хэш сообщения подписан человеком, имеющим доступ к этому закрытому ключу.

Для перечисления доступных алгоритмов получения кода проверки подлинности сообщения и генерации симметричного ключа можно использовать [**MacAlgorithmProvider**](https://msdn.microsoft.com/library/windows/apps/br241530). Для выполнения шифрования и генерации кода проверки подлинности сообщения можно использовать статические методы класса [**CryptographicEngine**](https://msdn.microsoft.com/library/windows/apps/br241490).

Цифровые подписи являются эквивалентом открытого ключа для кодов проверки подлинности сообщений (MAC) с закрытым ключом. Для проверки неизменности передаваемого сообщения коды проверки подлинности сообщений используют закрытые ключи, в то время как цифровые подписи — закрытый и открытый ключи в паре.

В этом примере кода показано, как использовать класс [**MacAlgorithmProvider**](https://msdn.microsoft.com/library/windows/apps/br241530), чтобы создать хэш-код проверки подлинности сообщения (HMAC).

```cs
using Windows.Security.Cryptography;
using Windows.Security.Cryptography.Core;
using Windows.Storage.Streams;

namespace SampleMacAlgorithmProvider
{
    sealed partial class MacAlgProviderApp : Application
    {
        public MacAlgProviderApp()
        {
            // Initialize the application.
            this.InitializeComponent();

            // Initialize the hashing process.
            String strMsg = "This is a message to be authenticated";
            String strAlgName = MacAlgorithmNames.HmacSha384;
            IBuffer buffMsg;
            CryptographicKey hmacKey;
            IBuffer buffHMAC;

            // Create a hashed message authentication code (HMAC)
            this.CreateHMAC(
                strMsg,
                strAlgName,
                out buffMsg,
                out hmacKey,
                out buffHMAC);

            // Verify the HMAC.
            this.VerifyHMAC(
                buffMsg,
                hmacKey,
                buffHMAC);
        }

        void CreateHMAC(
            String strMsg,
            String strAlgName,
            out IBuffer buffMsg,
            out CryptographicKey hmacKey,
            out IBuffer buffHMAC)
        {
            // Create a MacAlgorithmProvider object for the specified algorithm.
            MacAlgorithmProvider objMacProv = MacAlgorithmProvider.OpenAlgorithm(strAlgName);

            // Demonstrate how to retrieve the name of the algorithm used.
            String strNameUsed = objMacProv.AlgorithmName;

            // Create a buffer that contains the message to be signed.
            BinaryStringEncoding encoding = BinaryStringEncoding.Utf8;
            buffMsg = CryptographicBuffer.ConvertStringToBinary(strMsg, encoding);

            // Create a key to be signed with the message.
            IBuffer buffKeyMaterial = CryptographicBuffer.GenerateRandom(objMacProv.MacLength);
            hmacKey = objMacProv.CreateKey(buffKeyMaterial);

            // Sign the key and message together.
            buffHMAC = CryptographicEngine.Sign(hmacKey, buffMsg);

            // Verify that the HMAC length is correct for the selected algorithm
            if (buffHMAC.Length != objMacProv.MacLength)
            {
                throw new Exception("Error computing digest");
            }
         }

        public void VerifyHMAC(
            IBuffer buffMsg,
            CryptographicKey hmacKey,
            IBuffer buffHMAC)
        {
            // The input key must be securely shared between the sender of the HMAC and 
            // the recipient. The recipient uses the CryptographicEngine.VerifySignature() 
            // method as follows to verify that the message has not been altered in transit.
            Boolean IsAuthenticated = CryptographicEngine.VerifySignature(hmacKey, buffMsg, buffHMAC);
            if (!IsAuthenticated)
            {
                throw new Exception("The message cannot be verified.");
            }
        }
    }
}
```

## <a name="hashes"></a>Хэши


Криптографическая хэш-функция преобразовывает блок данных произвольной длины в двоичную строку фиксированной длины. Хэш-функции обычно используются при подписывании данных. В большинстве случаев операции подписывания с помощью открытого ключа требуют много вычислительных ресурсов. Вот почему целесообразно подписывать (шифровать) не само исходное сообщение, а его хэш. Рассмотрим типичный, хотя и упрощенный сценарий такой процедуры.

-   Владимир и Юлия владеют общим секретным ключом и договорились об использовании определенной функции, выдающей код проверки подлинности сообщения.
-   Владимир создает сообщение и вместе с секретным ключом передает его функции, которая выдает код проверки подлинности сообщения.
-   Затем Владимир отправляет Юлии по сети сообщение \[в незашифрованном виде\] вместе с кодом проверки подлинности сообщения.
-   Полученное сообщение вместе с секретным ключом Юлия передает функции, которая выдает код проверки подлинности сообщения. Этот код Юлия сравнивает с кодом, полученным от Владимира. Если они совпадают, это означает, что сообщение не подвергалось изменениям в процессе передачи.

Заметьте: Юлия послала незашифрованное сообщение. Только хэш был зашифрован. Эта процедура позволяет убедиться только в том, что исходное сообщение не было изменено. А использование открытого ключа Юлии показывает, что хэш сообщения был подписан кем-то, имеющим доступ к ее закрытому ключу, предположительно самой Юлией.

Можно использовать класс [**HashAlgorithmProvider**](https://msdn.microsoft.com/library/windows/apps/br241511) для перечисления доступных алгоритмов хэширования и создания значения [**CryptographicHash**](https://msdn.microsoft.com/library/windows/apps/br241498).

Цифровые подписи являются эквивалентом открытого ключа для кодов проверки подлинности сообщений (MAC) с закрытым ключом. Разница между ними в том, что MAC-коды используют закрытые ключи, для того чтобы получатель мог проверить, не было ли сообщение изменено при передаче, а в цифровых подписях для этого используются пары из открытого и закрытого ключей.

Объект [**CryptographicHash**](https://msdn.microsoft.com/library/windows/apps/br241498) можно использовать для повторного хэширования различных данных без необходимости заново создавать объект для каждого случая применения. Метод [**Append**](https://msdn.microsoft.com/library/windows/apps/br241499) добавляет новые данные для хэширования в буфер. Метод [**GetValueAndReset**](https://msdn.microsoft.com/library/windows/apps/hh701376) хэширует данные и сбрасывает параметры объекта для следующего использования. Процедура показана в следующем примере.

```cs
public void SampleReusableHash()
{
    // Create a string that contains the name of the hashing algorithm to use.
    String strAlgName = HashAlgorithmNames.Sha512;

    // Create a HashAlgorithmProvider object.
    HashAlgorithmProvider objAlgProv = HashAlgorithmProvider.OpenAlgorithm(strAlgName);

    // Create a CryptographicHash object. This object can be reused to continually
    // hash new messages.
    CryptographicHash objHash = objAlgProv.CreateHash();

    // Hash message 1.
    String strMsg1 = "This is message 1.";
    IBuffer buffMsg1 = CryptographicBuffer.ConvertStringToBinary(strMsg1, BinaryStringEncoding.Utf16BE);
    objHash.Append(buffMsg1);
    IBuffer buffHash1 = objHash.GetValueAndReset();

    // Hash message 2.
    String strMsg2 = "This is message 2.";
    IBuffer buffMsg2 = CryptographicBuffer.ConvertStringToBinary(strMsg2, BinaryStringEncoding.Utf16BE);
    objHash.Append(buffMsg2);
    IBuffer buffHash2 = objHash.GetValueAndReset();

    // Hash message 3.
    String strMsg3 = "This is message 3.";
    IBuffer buffMsg3 = CryptographicBuffer.ConvertStringToBinary(strMsg3, BinaryStringEncoding.Utf16BE);
    objHash.Append(buffMsg3);
    IBuffer buffHash3 = objHash.GetValueAndReset();

    // Convert the hashes to string values (for display);
    String strHash1 = CryptographicBuffer.EncodeToBase64String(buffHash1);
    String strHash2 = CryptographicBuffer.EncodeToBase64String(buffHash2);
    String strHash3 = CryptographicBuffer.EncodeToBase64String(buffHash3);
}

```

## <a name="digital-signatures"></a>Цифровые подписи


Цифровые подписи являются эквивалентом открытого ключа для кодов проверки подлинности сообщений (MAC) с закрытым ключом. Разница между ними в том, что MAC-коды используют закрытые ключи для того, чтобы получатель мог проверить, не было ли сообщение изменено при передаче, а в цифровых подписях для этого используются пары из открытого и закрытого ключей.

Однако в большинстве случаев операции подписывания с помощью открытого ключа требуют много вычислительных ресурсов. Вот почему целесообразно подписывать (шифровать) не само исходное сообщение, а его хэш. Отправитель создает хэш сообщения, подписывает его и посылает подпись вместе с сообщением (в незашифрованном виде). Получатель вычисляет хэш-код сообщения, расшифровывает подпись и сравнивает полученный результат со значением хэша. Если они совпадают, получатель может быть уверен, что сообщение действительно было послано отправителем и не было изменено в процессе передачи.

Подписывание сообщений дает возможность убедиться только в том, что исходное сообщение не было изменено. А использование открытого ключа отправителя показывает, что хэш сообщения был подписан кем-то, имеющим доступ к закрытому ключу.

Для перечисления доступных алгоритмов подписи, а также для формирования или импорта пары ключей можно использовать объект [**AsymmetricKeyAlgorithmProvider**](https://msdn.microsoft.com/library/windows/apps/br241478). Для подписывания сообщения или проверки подписи можно использовать статические методы класса [**CryptographicHash**](https://msdn.microsoft.com/library/windows/apps/br241498).