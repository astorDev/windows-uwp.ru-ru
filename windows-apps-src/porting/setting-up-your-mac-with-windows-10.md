---
description: Использование компьютера Mac для разработки приложений для Windows.
title: Установка Windows 10 на Mac
ms.assetid: 6D520610-5DE0-476E-A792-AA57E002D309
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 5bdd090b380160952dfbb8b5f04b95b6c84b75b7
ms.sourcegitcommit: 6f32604876ed480e8238c86101366a8d106c7d4e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/21/2019
ms.locfileid: "67322346"
---
# <a name="setting-up-your-mac-with-windows-10"></a>Установка Windows 10 на Mac


Использование компьютера Mac для разработки приложений для Windows.

## <a name="run-windows-on-your-mac-and-use-visual-studio"></a>Установите Windows на Mac и используйте Visual Studio

Вы готовы приступить к разработке универсальных приложений для Windows, но под рукой нет ПК? Нет проблем — можно работать на Mac! С помощью популярных решений независимых производителей, таких как Apple Boot Camp, Oracle VirtualBox, VMware Fusion и Parallels Desktop можно установить Windows 10 и Microsoft Visual Studio на компьютере Apple.

**Примечание**  потребуется загрузочного образа Windows 10 на диск или ФЛЭШ-накопитель USB. Подписчики MSDN могут скачать образ с веб-сайта скачиваемых файлов для подписчиков MSDN. Если вы не являетесь подписчиком, установщик можно приобрести у [Microsoft Store](https://www.microsoft.com/store/apps). Его также можно скачать [здесь](https://go.microsoft.com/fwlink/?LinkId=623906), что полезно, если вы уже работаете с Windows и хотите обновиться.

После выполнения Windows, затем можно установить последний выпуск Visual Studio из [загрузки для разработчиков для Windows 10](https://developer.microsoft.com/en-us/windows/downloads) и приступить к написанию приложений!

**Примечание**  Если вы планируете использовать Visual Studio работать с эмуляторами устройств, вы **необходимо** Установка 64-разрядный (x 64) версии Windows 10 Pro или более. К сожалению, 64-разрядная версия Windows не будет работать на некоторых старых компьютерах Mac. Узнать, совместимо ли ваше оборудование, можно на[этой странице поддержки Apple](https://go.microsoft.com/fwlink/p/?LinkID=397959).

## <a name="apple-boot-camp"></a>Apple Boot Camp

Boot Camp Assistant приложение предварительно установлен на каждом компьютере Mac, последних и запустив его поможет вам выполнить процесс установки Windows 10. Все что вам нужно — копия Windows (из источников, перечисленных выше) и не менее 30 ГБ места на диске. После установки вы сможете выбрать загрузку в Mac OSX или Windows 10. Более подробную информацию см. в статье [Страница с инструкциями Apple Boot Camp](https://go.microsoft.com/fwlink/?LinkId=623912).

## <a name="parallels-desktop"></a>Parallels Desktop

Используя Parallels Desktop 11, вы можете запускать приложения для Windows параллельно с существующими приложениями Mac, в том числе Visual Studio и Кортану. Доступна профессиональная версия, в которую входят дополнительные возможности для разработчиков, включая улучшенную отладку и поддержку Docker и Jenkins. Дополнительные сведения и бесплатную пробную версию можно найти на странице [Parallels Desktop](https://go.microsoft.com/fwlink/p/?LinkId=281827).

## <a name="vmware-fusion"></a>VMWare Fusion

VMware Fusion 8 позволяет запускать Visual Studio непосредственно на компьютере Mac. Доступна профессиональная версия, предоставляющая разработчикам несколько дополнительных возможностей, таких как поддержка vSphere. Дополнительные сведения и бесплатную пробную версию можно найти на странице [VMware Fusion](https://go.microsoft.com/fwlink/p/?LinkId=281826).

## <a name="oracle-virtualbox"></a>Oracle VirtualBox

VirtualBox — это бесплатное приложение для запуска на компьютере виртуальных машин, которое поддерживает использование Windows на Mac. Возможностей не слишком много, но цена очень привлекательна. Дополнительные сведения: [VirtualBox](https://go.microsoft.com/fwlink/p/?LinkId=280599).

