---
author: WilliamsJason
title: "Портал устройств - отправка папки, справочник по API"
description: "Узнайте, как получить доступ к API отправки папок программными средствами."
ms.sourcegitcommit: fdc25fa4bd7bd5bfa598b993f23cd0ae9783dd0e
ms.openlocfilehash: 942ddc13b0deba382ad7758bc30bd9a5b0cceb11

---

# Загрузка папки в каталог разработки

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




<!--HONumber=Jun16_HO4-->

