---
author: QuinnRadich
Description: How to use thumbnail images to help users preview files in UWP apps.
title: Руководство по эскизам в приложениях UWP
label: Thumbnail images
template: detail.hbs
ms.author: quradic
ms.date: 01/08/2018
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: df1eec58d936ba4f03e1eadae534abf0620b1a39
ms.sourcegitcommit: 1e5590dd10d606a910da6deb67b6a98f33235959
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/31/2018
ms.locfileid: "3233752"
---
# <a name="thumbnail-images"></a>Эскизы

В этом руководстве описано, как использовать эскизы для предоставления пользователям возможности предварительного просмотра файлов во время просмотра содержимого папок в приложении UWP. 

> **Важные API-интерфейсы**: [перечисление ThumbnailMode](https://docs.microsoft.com/uwp/api/windows.storage.fileproperties.thumbnailmode)

## <a name="should-my-app-include-thumbnails"></a>Следует ли включить эскизы в приложение?

Если приложение позволяет пользователям просматривать файлы, можно отобразить эскизы, чтобы предоставить возможность быстрого предварительного просмотра этих файлов. 

Используйте эскизы: 
- Для предоставления предварительного просмотра нескольких элементов в галерее (например, файлов и папок). Например, в фотоальбоме следует использовать эскизы, чтобы пользователи могли видеть уменьшенные изображения своих фотографий при просмотре папок.

    ![коллекция видео](images/thumbnail-gallery.png)

- Для предоставления предварительного просмотра отдельного элемента в списке (например, файла). Например, пользователю перед принятием решения об открытии файла может потребоваться просмотр дополнительной информации о файле, скажем, эскиза большего размера. 

    ![предварительный просмотр видео](images/thumbnail-preview.png)

## <a name="dos-and-donts"></a>Рекомендации
- Укажите [режим эскизов](https://docs.microsoft.com/uwp/api/windows.storage.fileproperties.thumbnailmode) (PicturesView, VideosView, DocumentsView, MusicView, ListView или SingleItem) при извлечении эскизов. Это гарантирует оптимизацию эскизов для отображения нужного пользователям типа файлов. 
    - Используйте режим SingleItem для получения эскиза для отдельного элемента вне зависимости от типа файла. Другие режимы эскизов предназначены для реализации предварительного просмотра нескольких файлов. 

- Во время загрузки эскизов отображайте вместо них универсальные заполнители. Использование заполнителей ускоряет реакцию вашего приложения, так как пользователи могут взаимодействовать с элементами предварительного просмотра еще до загрузки эскизов. 

    Заполнители должны:
    * Быть характерным для типа элемента, для которого он предназначен. Например, папки, изображения и видео должны иметь свои особые заполнители. 
    * Быть того же размера и пропорций, что и эскиз, для которого он предназначен. 
    * Отображаться до тех пор, пока не загрузится эскиз. 

- Используйте заполнители с текстовыми метками для представления папок и групп файлов, чтобы отделить их от отдельных файлов.

- Если получить эскиз невозможно, отобразите заполнитель. 

- Отображайте дополнительные сведения о файле, предоставляя элементы предварительного просмотра для документов и музыкальных файлов. Пользователи могут идентифицировать ключевую информацию о файле, которая может отсутствовать в эскизе. Например, в случае музыкального файла можно отображать имя исполнителя наряду с эскизом обложки альбома. 

- Не отображайте дополнительную информацию о файлах изображений и видеофайлах. В большинстве случаев пользователям, просматривающим изображения и видеофайлы, достаточно эскиза. 

## <a name="additional-usage-guidelines"></a>Дополнительные рекомендации по использованию
Рекомендуемые [режимы эскизов](https://docs.microsoft.com/uwp/api/windows.storage.fileproperties.thumbnailmode) и их функции:

<table>
<tr>
<th> Отображение образцов для</th>
<th> Режимы эскиза </th>
<th> Свойства получаемых эскизов </th>
</tr>
<tr>
<td> Изображения<br /> Видео </td>
<td> PicturesView <br />VideosView </td>
<td> <b>Размер</b>: средний, предпочтительно не менее 190 пикселей (если размер изображения составляет 190 x 130) <br />
<b>Пропорции</b>: однородные прямоугольные пропорции, примерно равные 0,7 (190 x 130, если размер составляет 190 пикселей) <br />
Обрезано для предварительного просмотра. <br /> 
Обеспечивает выравнивание изображений в сетке благодаря одинаковым пропорциям.  </td>
</tr>
<tr>
<td> Документы<br />Музыка </td>
<td> DocumentsView <br />MusicView <br /> ListView</td>
<td> <b>Размер</b>: небольшой, желательно не менее 40x40 пикселей <br />
<b>Пропорции</b>: однородные, квадратные  <br />
Хорошо подходит для предварительного просмотра обложек альбомов благодаря квадратным пропорциям. <br /> 
Документы выглядят так же, как в окне средства выбора файлов (в нем используются те же значки). </td>
</tr>
</tr>
<tr>
<td> Любой отдельный элемент (вне зависимости от типа файла) </td>
<td> SingleItem </td>
<td> <b>Размер</b>: небольшой, желательно не менее 40x40 пикселей <br />
<b>Пропорции</b>: однородные, квадратные  <br />
Хорошо подходит для предварительного просмотра обложек альбомов благодаря квадратным пропорциям. <br /> 
Документы выглядят так же, как в окне средства выбора файлов (в нем используются те же значки). </td>
</tr>
</table>

Вот пример, демонстрирующий разницу между полученными эскизами в зависимости от типа файла и режима эскиза:
<div class="mx-responsive-img">
<table>
<tr>
<th>Тип элемента</th>
<th>Если получен с использованием: <ul><li>PicturesView <li>VideosView</ul></th>
<th>Если получен с использованием: <ul><li>DocumentsView <li>MusicView <li>ListView</ul></th>
<th>Если получен с использованием: <ul><li>SingleItem</ul></th>
<tr>
<tr>
<td>Picture</td>
<td>Для изображения эскиза используются пропорции исходного файла. <br />
<img src="images/thumbnail-pic-picvidmode.png" alt="Picture thumbnail in picture or video mode"/></td>
<td>Эскиз был обрезан до квадрата. <br />
<img src="images/thumbnail-pic-doclistmusic-modes.png" alt="Picture thumbnail in documents, music, or list modes"/></td>
<td>Для изображения эскиза используются пропорции исходного файла.<br />
<img src="images/thumbnail-pic-single-mode.png" alt="Picture thumbnail in single mode"/> </td>
</tr>
<tr>
<td>Видео</td>
<td>У эскиза есть значок, отличающий его от изображений. <br />
<img src="images/thumbnail-vid-picvid-modes.png" alt="Video thumbnail in picture or video mode"/></td>
<td>Эскиз был обрезан до квадрата. <br />
<img src="images/thumbnail-vid-doclistmusic-modes.png" alt="Video thumbnail in documents, music, or list mode"/> </td>
<td>Для изображения эскиза используются пропорции исходного файла. <br />
<img src="images/thumbnail-vid-single-mode.png" alt="Video thumbnail in single mode"/></td>
</tr>
<tr>
<td>Музыка</td>
<td>Эскиз— это значок на фоне соответствующего размера. Цвет фона определяется цветом фона плитки приложения. <br />
<img src="images/thumbnail-music-picvid-modes.png" alt="Music thumbnail in picture or video mode"/></td>
<td>Если у файла есть обложка альбома, то эскизом будет обложка альбома.  <br />
<img src="images/thumbnail-music-doclistmusic-modes.png" alt="Music thumbnail in documents, music, or list mode"/> <br />
В противном случае эскиз— это значок на фоне соответствующего размера.</td>
<td>Если у файла есть обложка альбома, то эскизом будет обложка альбома, для которого будут использоваться исходные пропорции файла.  <br />
<img src="images/thumbnail-music-single-mode.png" alt="Music thumbnail in single mode"/> <br />
В противном случае эскиз— это значок. </td>
</tr>
<tr>
<td>Документ</td>
<td>Эскиз— это значок на фоне соответствующего размера. Цвет фона определяется цветом фона плитки приложения. <br />
<img src="images/thumbnail-docs-picvid-modes.png" alt="Document thumbnail in picture or video mode"/></td>
<td>Эскиз— это значок на фоне соответствующего размера. Цвет фона определяется цветом фона плитки приложения. <br />
<img src="images/thumbnail-doc-doclistmusic-modes.png" alt="Document thumbnail in documents, music, or list mode"/></td>
<td>Эскиз документа, если таковой существует. <br />
<img src="images/thumbnail-doc1-single-mode.png" alt="Document thumbnail in single mode"/><br />
В противном случае эскиз— это значок. <br />
<img src="images/thumbnail-doc2-single-mode.png" alt="Document thumbnail icon in single mode"/></td>
</tr>
<tr>
<td>Папка</td>
<td>Если есть файл изображения в папке, используется эскиз изображения.  <br />
<img src="images/thumbnail-dir-picvid-modes.png" alt="Folder thumbnail in picture or video mode"/> <br />
В противном случае эскиз не загружается.</td>
<td>Изображение эскиза не загружено.</td>
<td>Эскиз представляет собой значок папки.<br />
<img src="images/thumbnail-dir-single-mode.png" alt="Folder icon thumbnail in single mode"/></td>
</tr>
<tr>
<td>Группа файлов</td>
<td>Если есть файл изображения в папке, используется эскиз изображения.<br />
<img src="images/thumbnail-grp-picvid-modes.png" alt="File group thumbnail in picture or video mode"/> <br /> В противном случае эскиз не загружается. </td>
<td>Если среди файлов в группе есть файл, у которого есть обложка альбома, то в качестве эскиза используется обложка альбома. <br />
<img src="images/thumbnail-grp-doclistmusic-modes.png" alt="File group thumbnail in documents, music or list mode"/> <br />В противном случае эскиз не загружается. </td>
<td>Если среди файлов в группе есть файл, у которого есть обложка альбома, то в качестве эскиза используется обложка альбома, для которого используются исходные пропорции файла. <br />
<img src="images/thumbnail-grp1-single-mode.png" alt="File group thumbnail in picture or video mode"/> <br />В противном случае эскиз— это значок, представляющий группу файлов. <br />
<img src="images/thumbnail-grp2-single-mode.png" alt="File group icon in single mode"/> 
</td>
</tr>
</table>
</div>

## <a name="related-topics"></a>Статьи по теме
- [Перечисление ThumbnailMode](https://docs.microsoft.com/uwp/api/windows.storage.fileproperties.thumbnailmode)
- [Класс StorageItemThumbnail](https://docs.microsoft.com/uwp/api/Windows.Storage.FileProperties.StorageItemThumbnail)
- [Класс StorageFile](https://docs.microsoft.com/uwp/api/windows.storage.storagefile)
- [Пример эскизов файлов и папок (GitHub)](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/FileThumbnails)
- [Представления списка и сетки](../design/controls-and-patterns/lists.md)