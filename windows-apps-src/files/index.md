---
author: laurenhughes
ms.assetid: 1901c4c2-5161-435d-bc7b-f40c69cdb138
title: "Файлы, папки и библиотеки"
description: "Узнайте о параметрах приложения для чтения и записи, о средствах выбора файлов и папок, а также о специальных расположениях песочниц, например о библиотеках видео и музыки."
ms.author: lahugh
ms.date: 02/08/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, uwp
translationtype: Human Translation
ms.sourcegitcommit: 3c073879ab847a3e1af454e0c1550d8af0f78b3e
ms.openlocfilehash: 0baa58915bfac91d618a6da84f37174e41f7377f
ms.lasthandoff: 01/19/2017

---
 # <a name="files-folders-and-libraries"></a>Файлы, папки и библиотеки

\[ Обновлено для приложений UWP в Windows 10. Статьи, касающиеся Windows 8.x, см. в разделе [Архив](http://go.microsoft.com/fwlink/p/?linkid=619132) \]

API в пространствах имен [Windows.Storage](https://msdn.microsoft.com/library/windows/apps/br227346), [Windows.Storage.Streams](https://msdn.microsoft.com/library/windows/apps/br241791) и [Windows.Storage.Pickers](https://msdn.microsoft.com/library/windows/apps/br207928) можно использовать для считывания текста и данных других форматов из файлов и их записи в файлы, а также для управления файлами и папками. Этот раздел также содержит сведения о параметрах приложения для чтения и записи, о средствах выбора файлов и папок, а также о специальных расположениях песочниц, например о библиотеках видео и музыки.

| Раздел | Описание  |
|-------|--------------|
| [Перечисление и запрос файлов и папок](quickstart-listing-files-and-folders.md) | Получайте доступ к файлам и папкам в таких расположениях, как папка, библиотека, устройство или расположение в сети. Для получения списка файлов и папок из расположения также можно создавать запросы файлов и папок. |
| [Создание, запись и чтение файла](quickstart-reading-and-writing-files.md) | Считайте и запишите файл с помощью объекта [StorageFile](https://msdn.microsoft.com/library/windows/apps/br227171). |
| [Получение свойств файла](quickstart-getting-file-properties.md) | Получите свойства – верхнего уровня, базовые и расширенные – для файла, представленного объектом [StorageFile](https://msdn.microsoft.com/library/windows/apps/br227171). |
| [Открытие файлов и папок с помощью средства выбора](quickstart-using-file-and-folder-pickers.md) | Получите доступ к файлам и папкам, разрешив пользователю взаимодействовать со средством выбора. Для получения доступа к папкам можно использовать класс [FolderPicker](https://msdn.microsoft.com/library/windows/apps/br207881). |
| [Сохранение файла с помощью средства выбора](quickstart-save-a-file-with-a-picker.md) | Чтобы дать пользователям возможность задать имя и расположение, в которое приложение должно сохранить файл, воспользуйтесь средством [FileSavePicker](https://msdn.microsoft.com/library/windows/apps/br207871). |
| [Доступ к содержимому домашней группы](quickstart-accessing-homegroup-content.md) | Получите доступ к содержимому в папке домашней группы пользователя, включая изображения, музыку и видео. |
| [Определение доступности файлов Microsoft OneDrive](quickstart-determining-availability-of-microsoft-onedrive-files.md) | Определите доступность файла Microsoft OneDrive с помощью свойства [StorageFile.IsAvailable](https://msdn.microsoft.com/library/windows/apps/windows.storage.storagefile.isavailable.aspx). |
| [Файлы и папки в библиотеках музыки, изображений и видео](quickstart-managing-folders-in-the-music-pictures-and-videos-libraries.md) | Добавьте существующие папки музыки, изображений или видео в соответствующие библиотеки. Можно также удалить папки из библиотек, получить список папок в библиотеке и найти сохраненные фотографии, музыку и видео. |
| [Отслеживание недавно использовавшихся файлов и папок](how-to-track-recently-used-files-and-folders.md) | Отслеживайте файлы, к которым часто обращается пользователь, добавляя их в список недавно использовавшихся файлов (MRU). Платформа управляет списком MRU, сортируя элементы по времени последнего доступа к ним и удаляя самые старые элементы списка, имеющего ограничение в 25 элементов. Каждое приложение имеет собственный список MRU. |
| [Доступ к SD-карте](access-the-sd-card.md) | Не очень важные данные можно хранить на дополнительной карте microSD, в особенности на недорогих мобильных устройствах с ограниченным объемом внутренней памяти. |
| [Разрешения на доступ к файлам](file-access-permissions.md) | Приложения могут иметь доступ к определенным расположениям в файловой системе по умолчанию. Приложения также могут получить доступ к дополнительным расположениям через средство выбора файлов или с помощью объявления возможностей. |

## <a name="related-samples"></a>Связанные примеры
[Пример перечисления папок](http://go.microsoft.com/fwlink/p/?linkid=619993)

[Пример доступа к файлу](http://go.microsoft.com/fwlink/p/?linkid=619995)

[Пример средства выбора файлов](http://go.microsoft.com/fwlink/p/?linkid=619994)
 

 

