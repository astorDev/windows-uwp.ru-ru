---
author: seksenov
title: "Размещенные веб-приложения. Преобразование веб-приложения в приложение для Windows с помощью компьютера Mac"
description: "Веб-сайт можно превратить в приложение универсальной платформы Windows (UWP) для Windows 10 с помощью компьютера Mac."
kw: Hosted Web Apps with a Mac, Porting to Windows 10 with a Mac, Convert website to Windows with Mac, Packaging web application with ManfoldJS for Windows Store, Add website to Windows Store with App Studio
ms.author: wdg-dev-content
ms.date: 02/08/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: "размещенные веб-приложения на Mac, портирование в Windows 10 с помощью Mac, преобразование веб-сайта на Windows с помощью Mac, веб-сайт в Магазине Windows, Manifold JS для веб-приложений, App Studio для веб-приложений"
ms.assetid: a4cea1e8-4417-4488-b0e7-45c704dc53e9
translationtype: Human Translation
ms.sourcegitcommit: 5645eee3dc2ef67b5263b08800b0f96eb8a0a7da
ms.openlocfilehash: 464097cc3f90c2e84e03e936fb354f4220aff91d
ms.lasthandoff: 02/08/2017

---

# <a name="create-your-hosted-web-app-using-a-mac"></a>Создание размещенного веб-приложения с помощью компьютера Mac

Узнайте, как быстро создать приложение универсальной платформы Windows для Windows 10, имея только URL-адрес веб-сайта. 

> [!NOTE]
> Следующие инструкции рассчитаны на применение на платформе разработки для Mac. Пользователям Windows следует ознакомиться с разделом [Инструкции по использованию платформы разработки для Windows](./hwa-create-windows.md).

## <a name="what-you-need-to-develop-on-mac"></a>Необходимые инструменты для разработки на компьютере Mac

- Веб-браузер
- Командная строка

## <a name="option-1-manifoldjs"></a>Вариант 1. ManifoldJS

[ManifoldJS](http://manifoldjs.com/) — это приложение Node.js, которое легко устанавливается из NPM. Оно берет метаданные о вашем веб-сайте и создает собственные размещенные приложения для Android, iOS и Windows. Если ваш сайт не имеет [манифеста веб-приложения](https://www.w3.org/TR/appmanifest/), он будет автоматически создан.

1. Установите [NodeJS](https://nodejs.org/), который содержит NPM (диспетчер пакетов узла). <br>

2. Откройте командную строку и установите ManifoldJS с помощью NPM.
```
npm install -g manifoldjs
```

3. Выполните команду `manifoldjs` для URL-адреса веб-сайта.
```
manifoldjs http://codepen.io/seksenov/pen/wBbVyb/?editors=101
```

4. Следуйте инструкциям в нижеприведенном видео, чтобы выполнить упаковку и опубликовать размещенное веб-приложение в Магазине Windows.

[![Публикация веб-приложения UWP на компьютере Mac с использованием ManifoldJS](images/hwa-to-uwp/mac_manifoldjs_video.png)](https://sec.ch9.ms/ch9/0a67/9b06e5c7-d7aa-478d-b30d-f99e145a0a67/ManifoldJS_high.mp4 "Публикация веб-приложения UWP на компьютере Mac с использованием ManifoldJS")

## <a name="option-2-app-studio"></a>Вариант 2. App Studio

[App Studio](http://appstudio.windows.com/) — это бесплатное интернет-средство для сборки приложений, которое позволяет быстро создавать приложения для Windows 10.

1. Откройте [App Studio](http://appstudio.windows.com/) в браузере.

2. Нажмите кнопку **Начать сейчас**.

3. В разделе **Шаблоны веб-приложения** выберите **Размещенное веб-приложение**.

4. Следуйте указаниям на экране, чтобы создать пакет, пригодный для публикации в Магазине Windows.

## <a name="related-topics"></a>Связанные разделы

- [Улучшение веб-приложения путем обращения к функциям универсальной платформы Windows (UWP)](./hwa-access-features.md)
- [Руководство по работе с приложениями универсальной платформы Windows (UWP)](http://go.microsoft.com/fwlink/p/?LinkID=397871)
- [Загружаемые ресурсы проектирования для приложений Магазина Windows](https://msdn.microsoft.com/library/windows/apps/xaml/bg125377.aspx)

