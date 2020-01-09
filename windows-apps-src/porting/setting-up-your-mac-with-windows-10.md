---
description: Использование компьютера Mac для разработки приложений для Windows.
title: Настройка Mac с Windows 10
ms.assetid: 6D520610-5DE0-476E-A792-AA57E002D309
ms.date: 02/08/2017
ms.topic: article
keywords: Windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: b8ff6d6b3b15992f0598bfb371e6bfb1023d6dc1
ms.sourcegitcommit: 26bb75084b9d2d2b4a76d4aa131066e8da716679
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/06/2020
ms.locfileid: "75685002"
---
# <a name="setting-up-your-mac-with-windows-10"></a>Настройка Mac с Windows 10


Использование компьютера Mac для разработки приложений для Windows.

## <a name="run-windows-on-your-mac-and-use-visual-studio"></a>Установите Windows на Mac и используйте Visual Studio

Вы готовы приступить к разработке универсальных приложений для Windows, но под рукой нет ПК? Нет проблем — можно работать на Mac! С помощью популярных решений сторонних производителей, таких как Apple Boot Camp, Oracle VirtualBox, VMware Fusion и Parallels Desktop, можно установить Windows 10 и Microsoft Visual Studio на компьютере Apple.

**Обратите внимание** ,  потребуется загрузочный образ Windows 10 на диске или флэш-накопителе USB. Подписчики MSDN могут скачать образ с веб-сайта скачиваемых файлов для подписчиков MSDN. Если вы не являетесь подписчиком, установщик можно приобрести в [Microsoft Store](https://www.microsoft.com/store/apps). Его также можно скачать [здесь](https://www.microsoft.com/software-download/windows10), что полезно, если вы уже работаете с Windows и хотите обновиться.

После запуска Windows вы сможете установить последний выпуск Visual Studio из [центра загрузки для разработчиков для Windows 10](https://developer.microsoft.com/windows/downloads) и начать писать приложения!

**Примечание** .  если планируется использовать эмуляторы устройств Visual Studio, **необходимо** 64 установить 64-разрядную (x64) версию Windows 10 Pro или более. К сожалению, 64-разрядная версия Windows не будет работать на некоторых старых компьютерах Mac. Узнать, совместимо ли ваше оборудование, можно на[этой странице поддержки Apple](https://support.apple.com/kb/HT5634).

## <a name="apple-boot-camp"></a>Apple Boot Camp

Приложение Boot Camp Assistant предварительно установлено на всех последних компьютерах Mac, и его запуск поможет вам выполнить процесс установки Windows 10. Все что вам нужно — копия Windows (из источников, перечисленных выше) и не менее 30 ГБ места на диске. После установки вы сможете выбрать загрузку в Mac OSX или Windows 10. Более подробную информацию см. в статье [Страница с инструкциями Apple Boot Camp](https://support.apple.com/HT201468).

## <a name="parallels-desktop"></a>Parallels Desktop

Используя Parallels Desktop 11, вы можете запускать приложения для Windows параллельно с существующими приложениями Mac, в том числе Visual Studio и Кортану. Доступна профессиональная версия, в которую входят дополнительные возможности для разработчиков, включая улучшенную отладку и поддержку Docker и Jenkins. Дополнительные сведения и бесплатную пробную версию можно найти на странице [Parallels Desktop](https://www.parallels.com/download/desktop/).

## <a name="vmware-fusion"></a>VMWare Fusion

VMware Fusion 8 позволяет запускать Visual Studio непосредственно на компьютере Mac. Доступна профессиональная версия, предоставляющая разработчикам несколько дополнительных возможностей, таких как поддержка vSphere. Дополнительные сведения и бесплатную пробную версию можно найти на странице [VMware Fusion](http://www.vmware.com/products/fusion/).

## <a name="oracle-virtualbox"></a>Oracle VirtualBox

VirtualBox — это бесплатное приложение для запуска на компьютере виртуальных машин, которое поддерживает использование Windows на Mac. Возможностей не слишком много, но цена очень привлекательна. Дополнительные сведения: [VirtualBox](https://www.virtualbox.org/wiki/Downloads).

