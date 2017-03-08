---
author: WilliamsJason
title: "Портал устройств - отправка папки, справочник по API"
description: "Узнайте, как получить доступ к API отправки папок программными средствами."
ms.author: wdg-dev-content
ms.date: 02/08/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, uwp
ms.assetid: e1a2c7f0-0040-4ce7-94de-17224736e20b
translationtype: Human Translation
ms.sourcegitcommit: 5645eee3dc2ef67b5263b08800b0f96eb8a0a7da
ms.openlocfilehash: 8c2251c5c78fed10959b89de0d81ff563d0fa3e3
ms.lasthandoff: 02/08/2017

---

# <a name="upload-a-folder-to-the-development-directory"></a>Загрузка папки в каталог разработки

**Запрос**

Вы можете загрузить всю папку одновременно в DevelopmentFiles (или во вложенную папку в этой папке), используя идентификатор известной папки.

Метод      | URI запроса
:------     | :------
POST | /api/app/packagemanager/upload 
<br />
**Параметры URI**

В запросе URI можно указать следующие дополнительные параметры.

Параметр URI      | Описание
:------     | :-----
destinationFolder (обязательный) | Имя конечной папки, которую нужно передать. Эта папка будет помещена в каталог d: \developmentfiles\LooseApps на консоли. Имя папки должно быть в кодировке base64, поскольку оно может содержать разделители пути, если папка вложена в каталог LooseApps.
<br />

**Заголовки запроса**

- Нет

**Тело запроса**

- Несколько компонентов, из которых состоит HTTP-текст содержимого каталога.

**Ответ**

**Код состояния**

Этот API имеет следующие предполагаемые коды состояния.

Код состояния HTTP      | Описание
:------     | :-----
200 | Успех
4XX | Коды ошибок
5XX | Коды ошибок
<br />
**Доступные семейства устройств**

* Windows Xbox


