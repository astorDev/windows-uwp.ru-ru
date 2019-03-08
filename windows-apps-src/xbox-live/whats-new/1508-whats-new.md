---
title: Новые возможности для Xbox Live SDK — август 2015 г.
description: Новые возможности для Xbox Live SDK — август 2015 г.
ms.assetid: a034867b-7cc0-4b97-89d5-3486e95a80b4
ms.date: 04/04/2017
ms.topic: article
keywords: xbox live, xbox, игры, uwp, windows 10, xbox one
ms.localizationpriority: medium
ms.openlocfilehash: a454b7339035475416934c2f921dae65283c340c
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57639819"
---
# <a name="whats-new-for-the-xbox-live-sdk---august-2015"></a>Новые возможности для Xbox Live SDK — август 2015 г.

См. в разделе [новинки - июнь 2015 г.](1506-whats-new.md) статьи о добавленных в выпуске июня 2015 г.

Августовский выпуск пакета SDK для Xbox Live включает следующие обновления:

## <a name="os-and-tool-support"></a>Поддержка операционной системы и средства
Xbox Live SDK теперь поддерживает Windows 10 RTM [версия 10.0.10240] и Visual Studio 2015 RTM [версия 14.0.23107.0].

## <a name="multiplayer-manager-winrt-apis"></a>API-интерфейсы WinRT многопользовательскую Manager
Многопользовательскую Manager (в пространстве имен экспериментальный) теперь поддерживает API-интерфейсы WinRT (в дополнение к API C++)

## <a name="submit-batch-feedback-from-a-title"></a>Отправить отзыв о пакете в заголовок
Отправляет ряд элементов за один раз из заголовка.

## <a name="newupdated-documentation"></a>Новые и обновленные документации
Теперь пакет Xbox Live SDK имеется отдельная папка «Документы», содержит обновленные справочники по API и новых «Xbox Live руководство по программированию».

Исправления ошибок:

* Сбой при удалении подписки в режиме реального времени действия службы
* Сбой при входе в учетную запись гостя
* Нарушение прав доступа при отключении сетевого кабеля
* Сбои туннель теперь предоставляют код ошибки в API-интерфейсы C++
* Проблема ETag с TitleStorageService::DownloadBlobAsync
* Различные исправления ошибок для примеров приложений.
