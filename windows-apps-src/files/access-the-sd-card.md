---
ms.assetid: CAC6A7C7-3348-4EC4-8327-D47EB6E0C238
title: Доступ к SD-карте
description: Не очень важные данные можно хранить на дополнительной карте microSD и там же осуществлять доступ к ним, это особенно актуально на недорогих мобильных устройствах с ограниченным объемом внутренней памяти.
ms.date: 03/08/2017
ms.topic: article
keywords: windows 10, uwp, sd card, storage
ms.localizationpriority: medium
ms.openlocfilehash: 4573e0959cf9d4af9b3cef8ffbbce14847a9e521
ms.sourcegitcommit: aaa4b898da5869c064097739cf3dc74c29474691
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66369496"
---
# <a name="access-the-sd-card"></a>Доступ к SD-карте



Не очень важные данные можно хранить на дополнительной карте microSD и там же осуществлять доступ к ним, это особенно актуально на недорогих мобильных устройствах с ограниченным объемом внутренней памяти и слотом для SD-карты.

Чтобы приложение могло сохранять файлы на SD-карте и обращаться к ним, в большинстве случаев следует указать возможность **removableStorage** в файле манифеста приложения. Обычно также необходима регистрация для обработки типа файлов, которые сохраняются вашим приложением и к которым оно обращается.

Для сохранения файлов и доступа к файлам на дополнительной SD-карте используются следующие методы.
- Средства выбора файлов.
- Интерфейсы API [**Windows.Storage**](https://docs.microsoft.com/uwp/api/Windows.Storage).

## <a name="what-you-can-and-cant-access-on-the-sd-card"></a>Доступные и недоступные объекты на SD-карте

### <a name="what-you-can-access"></a>Доступные объекты

- Приложение может считывать и записывать файлы только тех типов, обработка которых зарегистрирована в файле манифеста приложения.
- Приложение также может создавать папки и управлять ими.

### <a name="what-you-cant-access"></a>К чему нет доступа

- Приложение не видит системные папки и содержащиеся в них файлы и не может к ним обращаться.
- Приложение не может видеть файлы, помеченные атрибутом "Скрытый". Обычно атрибут "Скрытый" снижает риск случайного удаления данных.
- Приложение не видит библиотеку документов и не может обращаться к ней с помощью свойства [**KnownFolders.DocumentsLibrary**](https://docs.microsoft.com/uwp/api/windows.storage.knownfolders.documentslibrary). Однако доступ к библиотеке документов на SD-карте можно получить, выполнив проход по файловой системе.

## <a name="security-and-privacy-considerations"></a>Вопросы безопасности и конфиденциальности

Когда приложение сохраняет файлы в глобальном расположении на SD-карте, файлы не шифруются и поэтому обычно доступны другим приложениям.

- Пока SD-карта находится в устройстве, файлы доступны другим приложениям, в которых зарегистрирована обработка файлов такого типа.
- Когда SD-карта удаляется из устройства и открывается с компьютера, файлы отображаются в проводнике и доступны другим приложениям.

Когда установленное на SD-карте приложение сохраняет файлы в свою папку [**LocalFolder**](https://docs.microsoft.com/uwp/api/windows.storage.applicationdata.localfolder), эти файлы шифруются и становятся недоступны другим приложениям.

## <a name="requirements-for-accessing-files-on-the-sd-card"></a>Требования для доступа к файлам на SD-карте

Чтобы получить доступ к файлам на SD-карте, обычно необходимо указать следующее.

1.  Необходимо указать функцию **removableStorage** в файле манифеста приложения.
2.  Также необходима регистрация для обработки расширений файлов, связанных с типом мультимедиа, к которому требуется получить доступ.

Используйте предыдущий метод для доступа к файлам мультимедиа на SD-карте без ссылки на известную папку, например **KnownFolders.MusicLibrary**, или для доступа к файлам мультимедиа, которые хранятся вне папок библиотеки мультимедиа.

Для доступа к файлам мультимедиа, хранящимся в библиотеках мультимедиа ("Музыка", "Фотографии" или "Видео"), с помощью известных папок необходимо только указать связанную функцию в файле манифеста приложения **musicLibrary**, **picturesLibrary** или **videoLibrary**. Указывать функцию **removableStorage** не обязательно. Дополнительные сведения см. в статье [Файлы и папки в библиотеках музыки, изображений и видео](quickstart-managing-folders-in-the-music-pictures-and-videos-libraries.md).

## <a name="accessing-files-on-the-sd-card"></a>Доступ к файлам на SD-карте

### <a name="getting-a-reference-to-the-sd-card"></a>Получение ссылки на SD-карту

Папка [**KnownFolders.RemovableDevices**](https://docs.microsoft.com/uwp/api/windows.storage.knownfolders.removabledevices) служит логическим корнем [**StorageFolder**](https://docs.microsoft.com/uwp/api/Windows.Storage.StorageFolder) для набора съемных устройств, которые подключены к устройству в данный момент. Если присутствует SD-карта, то первая (и единственная) подпапка **StorageFolder** в папке **KnownFolders.RemovableDevices** представляет эту SD-карту.

Чтобы определить наличие SD-карты и получить ссылку на нее в виде объекта [**StorageFolder**](https://docs.microsoft.com/uwp/api/Windows.Storage.StorageFolder), используйте код следующего вида.

```csharp
using Windows.Storage;

// Get the logical root folder for all external storage devices.
StorageFolder externalDevices = Windows.Storage.KnownFolders.RemovableDevices;

// Get the first child folder, which represents the SD card.
StorageFolder sdCard = (await externalDevices.GetFoldersAsync()).FirstOrDefault();

if (sdCard != null)
{
    // An SD card is present and the sdCard variable now contains a reference to it.
}
else
{
    // No SD card is present.
}
```

> [!NOTE]
> Встроенное устройство чтения SD-карт (например, слот на ноутбуке или ПК) может быть недоступным через KnownFolders.RemovableDevices.

### <a name="querying-the-contents-of-the-sd-card"></a>Запрос содержимого SD-карты

SD-карта может содержать множество папок и файлов, которые не распознаются системой как известные папки и которые нельзя запрашивать по расположению из [**KnownFolders**](https://docs.microsoft.com/uwp/api/Windows.Storage.KnownFolders). Для поиска файлов приложение должно перечислить содержимое карты, выполнив рекурсивный проход по файловой системе. Используйте запросы [**GetFilesAsync (CommonFileQuery.DefaultQuery)** ](https://docs.microsoft.com/uwp/api/windows.storage.storagefolder.getfilesasync) и [**GetFoldersAsync (CommonFolderQuery.DefaultQuery)** ](https://docs.microsoft.com/uwp/api/windows.storage.storagefolder.getfoldersasync) для эффективного получения содержимого SD-карты.

Рекомендуется выполнять просмотр SD-карты с помощью фонового потока. SD-карта может содержать много гигабайт данных.

Приложение также может запросить пользователя выбрать определенные папки с помощью средства выбора папок.

При получении доступа к файловой системе на SD-карте по пути, который вы получили из свойства [**KnownFolders.RemovableDevices**](https://docs.microsoft.com/uwp/api/windows.storage.knownfolders.removabledevices), следующие методы работают следующим образом.

-   Метод [**GetFilesAsync**](https://docs.microsoft.com/uwp/api/windows.storage.storagefolder.getfilesasync) возвращает объединение расширений файлов, зарегистрированных для обработки, и расширений файлов, связанных с любыми указанными возможностями библиотеки мультимедиа.
-   Если вы не выполнили регистрацию для обработки расширений файлов, к которым вы пытаетесь получить доступ, происходит сбой метода [**GetFileFromPathAsync**](https://docs.microsoft.com/uwp/api/windows.storage.storagefile.getfilefrompathasync).

## <a name="identifying-the-individual-sd-card"></a>Определение отдельной SD-карты

При первом подключении SD-карты операционная система создает для нее уникальный идентификатор. Идентификатор сохраняется в файле в папке WPSystem в корневом каталоге карты. По идентификатору приложение может определять распознаваемые карты. Если приложение распознает карту, то оно может отложить определенные операции, выполненные ранее. Однако с момента последнего обращения приложения к карте ее содержимое могло измениться.

Например, возьмем приложение, которое индексирует электронные книги. Если ранее приложение сканировало всю SD-карту, проверяя наличие электронных книг, и составило индекс этих книг, то, когда карта будет вставлена снова, приложение сможет сразу отобразить список книг при условии, что оно распознает карту. Одновременно с этим приложение может запустить фоновый поток низкого приоритета для поиска новых электронных книг. Приложение также может обрабатывать ситуацию, когда пользователь пытается открыть книгу, которая ранее существовала, но потом была удалена.

Этот идентификатор хранится в свойстве **WindowsPhone.ExternalStorageId**.

```csharp
using Windows.Storage;

// Get the logical root folder for all external storage devices.
StorageFolder externalDevices = Windows.Storage.KnownFolders.RemovableDevices;

// Get the first child folder, which represents the SD card.
StorageFolder sdCard = (await externalDevices.GetFoldersAsync()).FirstOrDefault();

if (sdCard != null)
{
    var allProperties = sdCard.Properties;
    IEnumerable<string> propertiesToRetrieve = new List<string> { "WindowsPhone.ExternalStorageId" };

    var storageIdProperties = await allProperties.RetrievePropertiesAsync(propertiesToRetrieve);

    string cardId = (string)storageIdProperties["WindowsPhone.ExternalStorageId"];

    if (...) // If cardID matches the cached ID of a recognized card.
    {
        // Card is recognized. Index contents opportunistically.
    }
    else
    {
        // Card is not recognized. Index contents immediately.
    }
}
```

 

 
