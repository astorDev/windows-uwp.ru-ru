---
ms.assetid: 1901c4c2-5161-435d-bc7b-f40c69cdb138
title: Файлы, папки и библиотеки
description: Узнайте о параметрах приложения для чтения и записи, о средствах выбора файлов и папок, а также о специальных расположениях песочниц, например о библиотеках видео и музыки.
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 15e6ebd45d4f7069e09169a437476b0f785ea959
ms.sourcegitcommit: f727b68e86a86c94eff00f67ed79a1c12666e7bc
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/29/2020
ms.locfileid: "74259601"
---
 # <a name="files-folders-and-libraries"></a>Файлы, папки и библиотеки


API в пространствах имен [Windows.Storage](https://docs.microsoft.com/uwp/api/Windows.Storage), [Windows.Storage.Streams](https://docs.microsoft.com/uwp/api/Windows.Storage.Streams) и [Windows.Storage.Pickers](https://docs.microsoft.com/uwp/api/Windows.Storage.Pickers) можно использовать для считывания текста и данных других форматов из файлов и их записи в файлы, а также для управления файлами и папками. Этот раздел также содержит сведения о параметрах приложения для чтения и записи, о средствах выбора файлов и папок, а также о специальных расположениях песочниц, например о библиотеках видео и музыки.

| Раздел | Описание  |
|-------|--------------|
| [Перечисление и запрос файлов и папок](quickstart-listing-files-and-folders.md) | Получайте доступ к файлам и папкам в таких расположениях, как папка, библиотека, устройство или расположение в сети. Для получения списка файлов и папок из расположения вы также можете создавать запросы файлов и папок. |
| [Создание, запись и чтение файла](quickstart-reading-and-writing-files.md) | Считайте и запишите файл с помощью объекта [StorageFile](https://docs.microsoft.com/uwp/api/Windows.Storage.StorageFile). |
| [Рекомендации по записи в файлы](best-practices-for-writing-to-files.md) | Ознакомьтесь с рекомендациями по использованию различных методов записи в файлы, доступных в классах [FileIO](https://docs.microsoft.com/uwp/api/windows.storage.fileio) и [PathIO](https://docs.microsoft.com/uwp/api/windows.storage.pathio). |
| [Получение свойств файла](quickstart-getting-file-properties.md) | Получите свойства – верхнего уровня, базовые и расширенные – для файла, представленного объектом [StorageFile](https://docs.microsoft.com/uwp/api/Windows.Storage.StorageFile). |
| [Открытие файлов и папок с помощью средства выбора](quickstart-using-file-and-folder-pickers.md) | Получите доступ к файлам и папкам, разрешив пользователю взаимодействовать со средством выбора. Для получения доступа к папкам можно использовать класс [FolderPicker](https://docs.microsoft.com/uwp/api/Windows.Storage.Pickers.FolderPicker). |
| [Сохранение файла с помощью средства выбора](quickstart-save-a-file-with-a-picker.md) | Чтобы дать пользователям возможность задать имя и расположение, в которое приложение должно сохранить файл, воспользуйтесь средством [FileSavePicker](https://docs.microsoft.com/uwp/api/Windows.Storage.Pickers.FileSavePicker). |
| [Доступ к содержимому домашней группы](quickstart-accessing-homegroup-content.md) | Получайте доступ к содержимому в папке домашней группы пользователя, включая изображения, музыку и видео. |
| [Определение доступности файлов Microsoft OneDrive](quickstart-determining-availability-of-microsoft-onedrive-files.md) | Определите доступность файла Microsoft OneDrive с помощью свойства [StorageFile.IsAvailable](https://docs.microsoft.com/uwp/api/windows.storage.storagefile.isavailable). |
| [Файлы и папки в библиотеках музыки, изображений и видео](quickstart-managing-folders-in-the-music-pictures-and-videos-libraries.md) | Добавьте существующие папки музыки, изображений или видео в соответствующие библиотеки. Можно также удалить папки из библиотек, получить список папок в библиотеке и найти сохраненные фотографии, музыку и видео. |
| [Отслеживание недавно использовавшихся файлов и папок](how-to-track-recently-used-files-and-folders.md) | Отслеживайте файлы, к которым часто обращается пользователь, добавляя их в список недавно использованных файлов (MRU). Платформа управляет списком MRU, сортируя элементы по времени последнего доступа к ним и удаляя самые старые элементы списка, имеющего ограничение в 25 элементов. Каждое приложение имеет собственный список MRU. |
| [Отслеживание изменений файловой системы в фоновом режиме](change-tracking-filesystem.md) | Отслеживание изменений в файловой системе, даже когда приложение не запущено.|
| [Доступ к SD-карте](access-the-sd-card.md) | Не очень важные данные можно хранить на дополнительной карте microSD и там же осуществлять доступ к ним, это особенно актуально на недорогих мобильных устройствах с ограниченным объемом внутренней памяти. |
| [Разрешения на доступ к файлам](file-access-permissions.md) | Приложения могут иметь доступ к определенным расположениям в файловой системе по умолчанию. Приложения также могут получить доступ к дополнительным расположениям через средство выбора файлов или с помощью объявления возможностей. |
| [Быстрый доступ к свойствам файлов в UWP](fast-file-properties.md) | Теперь можно получить список файлов и их свойств из библиотеки для использования в приложении UWP. |

## <a name="related-samples"></a>Связанные примеры
[Пример перечисления папок](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/FolderEnumeration)

[Пример доступа к файлу](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/FileAccess)

[Пример средства выбора файлов](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/FilePicker)
 

 
