---
author: eliotcowley
title: Отсутствующие API-интерфейсы .NET в Unity и UWP
description: Информация об отсутствующих API-интерфейсах .NET при сборке UWP-игр в Unity, а также возможные решения для распространенных проблем.
ms.assetid: 28A8B061-5AE8-4CDA-B4AB-2EF0151E57C1
ms.author: elcowle
ms.date: 2/21/2018
ms.topic: article
keywords: windows 10, uwp, игры, .net, unity
ms.localizationpriority: medium
ms.openlocfilehash: 4b795ed47249eee1f9dc21b195d46f450997019e
ms.sourcegitcommit: cd00bb829306871e5103db481cf224ea7fb613f0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/31/2018
ms.locfileid: "5888607"
---
# <a name="missing-net-apis-in-unity-and-uwp"></a>Отсутствующие API-интерфейсы .NET в Unity и UWP

При создании UWP-игры с использованием .NET вы, вероятно, обнаружите, что некоторые API-интерфейсы, которые вы использовали в редакторе Unity или в игре для ПК, не представлены для UWP. Это вызвано тем, что .NET для приложений UWP включает в себя набор типов, предоставленных в полной платформе .NET Framework для каждого пространства имен.

Кроме того, некоторые игровые движки используют разные версии .NET, которые не полностью совместимы с .NET для UWP, например Mono в Unity. Поэтому при написании игры все может отлично работать в редакторе, но при выполнении сборки для UWP возможно получение таких ошибок: **Тип или пространство имен Formatters не существует в пространстве имен System.Runtime.Serialization (отсутствует ссылка на сборку?)**

К счастью, Unity предоставляет некоторые из этих отсутствующих API в виде методов расширения и заменяющих типов, которые описаны в разделе [Универсальная платформа Windows: отсутствующие типы .NET на сервере сценариев .NET](https://docs.unity3d.com/Manual/windowsstore-missingtypes.html). Однако если требуемая функциональность не представлена, в [Обзоре приложений .NET для Windows 8.x](https://msdn.microsoft.com/library/windows/apps/br230302) рассматриваются способы преобразования кода для использования .NET или WinRT для API-интерфейсов UWP. (В нем рассматривается Windows 8, но это также относится к приложениям UWP для Windows 10.)

## <a name="net-standard"></a>.NET Standard

Чтобы понять, почему некоторые API не работают, необходимо разобраться в разнообразии типов .NET и том, как UWP реализует .NET. [.NET Standard](https://docs.microsoft.com/dotnet/standard/net-standard) — формальная спецификация для API-интерфейсов .NET, которая должна быть кросс-платформенной и унифицировать разновидности .NET. Каждая реализация .NET поддерживает определенную версию .NET Standard. Таблицу стандартов и реализаций можно увидеть в разделе [Поддержка реализации .NET](https://docs.microsoft.com/dotnet/standard/net-standard#net-implementation-support).

Каждая версия UWP SDK соответствует своему уровню .NET Standard. Например, 16299 SDK (Fall Creators Update) поддерживает .NET Standard 2.0.

Если вы хотите знать, поддерживаются ли определенные API-интерфейсы .NET в той версии UWP, на которую вы ориентируетесь, можно проверить [Справочник по API .NET Standard](https://docs.microsoft.com/dotnet/api/index?view=netstandard-2.0) и выбрать нужную версию .NET Standard, поддерживаемую данной версией UWP.

## <a name="scripting-backend-configuration"></a>Конфигурации сервера сценариев

Первое, что требуется сделать, если возникают проблемы при сборке для UWP — проверить **параметры проигрывателя** (**Файл > Параметры сборки**выберите **Универсальную платформу Windows**, а затем **Параметры проигрывателя**). В разделе **Другие параметры > Конфигурации** первые три раскрывающихся списка (**Версия среды выполнения сценариев**, **Сервер сценариев** и **Уровень совместимости Api**) содержат важные параметры, которые необходимо учитывать.

**Версия среды выполнения сценариев** — то, что использует сервер сценариев Unity, что и это позволяет получить (примерно) равноценную поддержку версии .NET Framework, которую вы выбрали. Тем не менее, следует помнить, что будут поддерживаться не все API-интерфейсы в этой версию .NET Framework, а только те, которые представлены в версии .NET Standard, на которую ориентируется ваше приложение UWP.

Часто с новыми выпусками .NET дополнительные API-интерфейсы добавляются в .NET Standard, что может позволить использовать один и тот же код для автономных приложений и UWP. Например, пространство имен [System.Runtime.Serialization.Json](https://docs.microsoft.com/dotnet/api/system.runtime.serialization.json) впервые появилось в .NET Standard 2.0. Если задать для **Версии среды выполнения сценариев** значение **.NET 3.5 Equivalent** (предназначено для более ранней версии .NET Standard), вы получите сообщение об ошибке при попытке использовать API-интерфейс; переведите переключатель в положение **.NET 4.6 Equivalent** (поддерживает .NET Standard 2.0), и API будет работать.

**Сервер сценариев** может быть **.NET** или **IL2CPP**. В этом разделе предполагается, что вы выбрали **.NET**, поскольку здесь возникают проблемы, рассматриваемые в этой статье. Дополнительные сведения см. в разделе [Серверы сценариев](https://docs.unity3d.com/Manual/windowsstore-scriptingbackends.html).

Наконец, следует задать **Уровень совместимости Api** согласно версии .NET, в которой необходимо запускать игру. Это значение должно совпадать с **Версией среды выполнения сценариев**.

Как правило, для **Версии среды выполнения сценариев** и **Уровня совместимости Api** следует выбирать последнюю доступную версию, чтобы обеспечить дополнительную совместимость с .NET Framework и таким образом использовать дополнительные API-интерфейсы .NET.

![Конфигурация: Версия среды выполнения сценариев; Сервер сценариев; Уровень совместимости API](images/missing-dot-net-apis-in-unity-1.png)

## <a name="platform-dependent-compilation"></a>Компиляция в зависимости от платформы

Если вы разрабатываете игру на Unity для нескольких платформ, включая UWP, рекомендуется использовать компиляцию в зависимости от платформы, чтобы обеспечить выполнение предназначенного для UWP код только тогда, когда игра собрана как UWP. Таким образом можно использовать полную платформу .NET для самостоятельного приложения для ПК и других платформ и API-интерфейсы WinRT для UWP, не получая ошибок построения.

Используйте следующие директивы только для компиляции кода при запуске в качестве приложения UWP:

```csharp
#if NETFX_CORE
    // Your UWP code here
#else
    // Your standard code here
#endif
```

> [!NOTE]
> `NETFX_CORE` предназначен только для проверки того, выполняется ли компиляция кода C# с использованием сервера сценариев .NET. Если вы используете другой сервер сценариев, такой как IL2CPP, вместо этого используйте `UNITY_WSA_10_0`.

Полный список директив компиляции в зависимости от платформы см. в разделе [Компиляция в зависимости от платформы](https://docs.unity3d.com/Manual/PlatformDependentCompilation.html).

## <a name="common-issues-and-workarounds"></a>Распространенные проблемы и обходные решения

Ниже описаны распространенные проблемы, которые могут возникнуть в случаях, где API-интерфейсы .NET отсутствуют в наборе UWP, и способы их устранения.

### <a name="data-serialization-using-binaryformatter"></a>Сериализация данных с помощью BinaryFormatter

В играх часто производится сериализация для сохранения данных, чтобы игроки не могли ими манипулировать. Тем не менее, средство [BinaryFormatter](https://docs.microsoft.com/dotnet/api/system.runtime.serialization.formatters.binary.binaryformatter), которое сериализует объект в двоичный файл, не доступно в предыдущих версиях .NET Standard (до 2.0). Рассмотрите возможность использования [XmlSerializer](https://docs.microsoft.com/dotnet/api/system.xml.serialization.xmlserializer) или [DataContractJsonSerializer](https://docs.microsoft.com/dotnet/api/system.runtime.serialization.json.datacontractjsonserializer) вместо него.

```csharp
private void Save()
{
    SaveData data = new SaveData(); // User-defined object to serialize

    DataContractJsonSerializer serializer = 
      new DataContractJsonSerializer(typeof(SaveData));

    FileStream stream = 
      new FileStream(Application.persistentDataPath, FileMode.CreateNew);

    serializer.WriteObject(stream, data);
    stream.Dispose();
}
```

### <a name="io-operations"></a>Операции ввода-вывода

Некоторые типы в пространстве имен [System.IO](https://docs.microsoft.com/dotnet/api/system.io), такие как [FileStream](https://docs.microsoft.com/dotnet/api/system.io.filestream), не доступны в предыдущих версиях .NET Standard. Однако Unity предоставляет типы [Directory](https://docs.microsoft.com/dotnet/api/system.io.directory), [File](https://docs.microsoft.com/dotnet/api/system.io.file) и **FileStream**, чтобы их можно было использовать в игре.

Кроме того, можно использовать API-интерфейсы [Windows.Storage](https://docs.microsoft.com/uwp/api/Windows.Storage), которые доступны только для приложений UWP. Тем не менее, эти API-интерфейсы позволяют приложению выполнять запись только выделенную ему область и не предоставляют неограниченный доступ к файловой системе. Дополнительные сведения см. в разделе [Файлы, папки и библиотеки](https://docs.microsoft.com/windows/uwp/files/).

Важное замечание: метод [Close](https://docs.microsoft.com/dotnet/api/system.io.stream.close) доступен только в .NET Standard 2.0 и более поздних версиях (хотя Unity предоставляет метод расширения). Вместо него предлагается использовать [Dispose](https://docs.microsoft.com/dotnet/api/system.io.stream.dispose).

### <a name="threading"></a>Потоки

Некоторые типы в пространстве имен [System.Threading](https://docs.microsoft.com/dotnet/api/system.threading), такие как [ThreadPool](https://docs.microsoft.com/dotnet/api/system.threading.threadpool), не доступны в предыдущих версиях .NET Standard. В таких случаях можно использовать вместо него пространство имен [Windows.System.Threading](https://docs.microsoft.com/uwp/api/windows.system.threading).

Вот как можно обрабатывать потоки в игре Unity, используя компиляцию в зависимости от платформы для подготовки для платформ UWP и не-UWP:

```csharp
private void UsingThreads()
{
#if NETFX_CORE
    Windows.System.Threading.ThreadPool.RunAsync(workItem => SomeMethod());
#else
    System.Threading.ThreadPool.QueueUserWorkItem(workItem => SomeMethod());
#endif
}
```

### <a name="security"></a>Безопасность

Некоторые их пространств имен **System.Security.***, например [System.Security.Cryptography.X509Certificates](https://docs.microsoft.com/dotnet/api/system.security.cryptography.x509certificates?view=netstandard-2.0), недоступны при разработке игр Unity для UWP. В таких случаях используйте API-интерфейсы **Windows.Security.***, которые охватывают почти всю ту же функциональность.

Следующий пример просто получает сертификаты из хранилища сертификатов с заданным именем:

```cs
private async void GetCertificatesAsync(string certStoreName)
    {
#if NETFX_CORE
        IReadOnlyList<Certificate> certs = await CertificateStores.FindAllAsync();
        IEnumerable<Certificate> myCerts = 
            certs.Where((certificate) => certificate.StoreName == certStoreName);
#else
        X509Store store = new X509Store(certStoreName, StoreLocation.CurrentUser);
        store.Open(OpenFlags.OpenExistingOnly);
        X509Certificate2Collection certs = store.Certificates;
#endif
    }
```

Подробнее об использовании API-интерфейсов WinRT см. в статье [Безопасность](https://docs.microsoft.com/windows/uwp/security/).

### <a name="networking"></a>Сеть

Некоторые их пространств имен **System&period;Net.***, например [System.Net.Mail](https://docs.microsoft.com/dotnet/api/system.net.mail?view=netstandard-2.0), также недоступны при разработке игр Unity для UWP. Для большинства из этих API используйте соответствующие API WinRT **Windows.Networking.*** и **Windows.Web.***, которые реализуют схожую функциональность. Подробнее: [Сеть и веб-службы](https://docs.microsoft.com/windows/uwp/networking/).

В случае **System.Net.Mail** используйте пространство имен [Windows.ApplicationModel.Email](https://docs.microsoft.com/uwp/api/windows.applicationmodel.email). Подробнее: [Отправка сообщения электронной почты](https://docs.microsoft.com/windows/uwp/contacts-and-calendar/sending-email).

## <a name="see-also"></a>См. также

* [Универсальная платформа Windows: отсутствующие типы .NET на сервере сценариев .NET](https://docs.unity3d.com/Manual/windowsstore-missingtypes.html)
* [Обзор .NET для приложений UWP](https://msdn.microsoft.com/library/windows/apps/br230302)
* [Руководства по переносу на Unity UWP](https://unity3d.com/partners/microsoft/porting-guides)