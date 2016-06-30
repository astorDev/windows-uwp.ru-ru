---
author: drewbatgit
ms.assetid: A1A0D99A-DCBF-4A14-80B9-7106BEF045EC
description: "Для перекодирования видеофайлов из одного формата в другой можно использовать API Windows.Media.Transcoding."
title: "Перекодирование файлов мультимедиа"
translationtype: Human Translation
ms.sourcegitcommit: 6530fa257ea3735453a97eb5d916524e750e62fc
ms.openlocfilehash: 06c452291f10acd35dde9659c08a386ea38fa90a

---

# Перекодирование файлов мультимедиа

\[ Обновлено для приложений UWP в Windows 10. Статьи о Windows 8.x см. в [архиве](http://go.microsoft.com/fwlink/p/?linkid=619132) \]


Для перекодирования видеофайлов из одного формата в другой можно использовать API [**Windows.Media.Transcoding**](https://msdn.microsoft.com/library/windows/apps/br207105).

*Перекодирование* — это преобразование цифрового файла мультимедиа (например, звукового или видеофайла) из одного формата в другой. Обычно этот процесс состоит из декодирования и повторного кодирования файла. Например, файл Windows Media можно преобразовать в формат MP4, чтобы его можно было воспроизводить на портативном устройстве, поддерживающем данный формат. Кроме того, можно преобразовать видеофайл высокой четкости, уменьшив его разрешение. В таком случае перекодированный файл может использовать тот же кодек, что и исходный файл, но с другим профилем кодирования.

## Настройка проекта для перекодирования

Чтобы перекодировать файлы мультимедиа с помощью кода в данной статье, помимо пространств имен, на которые по умолчанию ссылается шаблон проекта, вам потребуется ссылаться на указанные ниже пространства имен.

[!code-cs[Использование](./code/TranscodeWin10/cs/MainPage.xaml.cs#SnippetUsing)]

## Выбор исходного файла и файла назначения

Способ, которым приложение будет определять исходный файлах и файл назначения для перекодирования, зависит от вашей реализации. В этом примере используются элементы управления [**FileOpenPicker**](https://msdn.microsoft.com/library/windows/apps/br207847) и [**FileSavePicker**](https://msdn.microsoft.com/library/windows/apps/br207871), с помощью которых пользователь может выбрать исходный файл и файл назначения.

[!code-cs[TranscodeGetFile](./code/TranscodeWin10/cs/MainPage.xaml.cs#SnippetTranscodeGetFile)]

## Создание профиля кодирования мультимедиа

Профиль кодирования содержит параметры, определяющие способ кодирования файла назначения. Именно на этом этапе перекодирования файла выбор параметров наиболее велик.

Класс [**MediaEncodingProfile**](https://msdn.microsoft.com/library/windows/apps/hh701026) содержит статические методы для создания указанных ниже предопределенных профилей кодирования.

-   WAV-файлы
-   Звуковые файлы в формате AAC (M4A)
-   Звуковые файлы в формате MP3
-   Звуковые файлы в формате WMA
-   AVI-файлы
-   Видеофайлы в формате MP4 (видео в формате H.264 и звук в формате AAC)
-   Видеофайлы в формате WMV

Первые три профиля в этом списке предназначены только для звуковых файлов. Остальные три профиля предназначены и для видео, и для звука.

Указанный ниже фрагмент кода используется для создания профиля видеофайла в формате MP4.

[!code-cs[TranscodeMediaProfile](./code/TranscodeWin10/cs/MainPage.xaml.cs#SnippetTranscodeMediaProfile)]

Статический метод [**CreateMp4**](https://msdn.microsoft.com/library/windows/apps/hh701078) создает профиль кодирования для MP4. Параметр этого метода задает конечное разрешение видеофайла. В данном случае [**VideoEncodingQuality.hd720p**](https://msdn.microsoft.com/library/windows/apps/hh701290) означает разрешение 1280 x 720 пикселей со скоростью 30 кадров в секунду ("720p" означает 720 строк прогрессивной развертки на один кадр). Все остальные методы создания предопределенных профилей работают по тому же принципу.

В качестве альтернативы с помощью метода [**MediaEncodingProfile.CreateFromFileAsync**](https://msdn.microsoft.com/library/windows/apps/hh701047) можно создать профиль, который соответствует существующему файлу мультимедиа. Если же вам известны точные параметры кодирования, то можно создать новый объект [**MediaEncodingProfile**](https://msdn.microsoft.com/library/windows/apps/hh701026) и настроить для него профиль.

## Перекодировка файла

Чтобы перекодировать файл, создайте новый объект [**MediaTranscoder**](https://msdn.microsoft.com/library/windows/apps/br207080) и вызовите метод [**MediaTranscoder.PrepareFileTranscodeAsync**](https://msdn.microsoft.com/library/windows/apps/hh700936). В качестве параметров задайте исходный файл, конечный файл и профиль кодирования. Затем вызовите метод [**TranscodeAsync**](https://msdn.microsoft.com/library/windows/apps/hh700946) для объекта [**PrepareTranscodeResult**](https://msdn.microsoft.com/library/windows/apps/hh700941), возвращенного в результате выполнения операции асинхронного перекодирования.

[!code-cs[TranscodeTranscodeFile](./code/TranscodeWin10/cs/MainPage.xaml.cs#SnippetTranscodeTranscodeFile)]

## Реакция на ход выполнения перекодирования

Вы можете регистрировать события, чтобы реагировать на изменения в ходе выполнения асинхронного [**TranscodeAsync**](https://msdn.microsoft.com/library/windows/apps/hh700946). Эти события — часть платформы асинхронного программирования для приложений универсальной платформы Windows (UWP) и не зависят от API перекодирования.

[!code-cs[TranscodeCallbacks](./code/TranscodeWin10/cs/MainPage.xaml.cs#SnippetTranscodeCallbacks)]

 

 







<!--HONumber=Jun16_HO4-->


