---
title: Подход PWA к разработке Android в Windows
description: Начните разработку приложений Android с помощью веб – приложения PWA в Windows.
author: mattwojo
ms.author: mattwoj
manager: jken
ms.topic: article
keywords: Android в Windows, PWA, Android, Cordova, ионный, PhoneGap, гибридное веб-приложение
ms.date: 04/28/2020
ms.openlocfilehash: c0ff9acf1d8e93e82f1db424d7a356c974988683
ms.sourcegitcommit: 24b19e7ee06e5bb11a0dae334806741212490ee9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/29/2020
ms.locfileid: "82255218"
---
# <a name="get-started-developing-a-pwa-or-hybrid-web-app-for-android"></a>Приступая к разработке веб-приложения PWA или гибридной среды для Android

Это руководство поможет вам приступить к созданию гибридного веб-приложения или последовательного веб-приложения (PWA) в Windows с помощью одной базы кода HTML/CSS/JavaScript, которую можно использовать в Интернете и на разных платформах устройств (Android, iOS, Windows).

С помощью правильных платформ и компонентов веб-приложения могут работать на устройстве Android таким образом, чтобы пользователи были очень похожи на собственное приложение.

## <a name="features-of-a-pwa-or-hybrid-web-app"></a>Возможности PWA или гибридного веб-приложения

Существует два основных типа веб-приложений, которые можно установить на устройствах Android. Основное различие состоит в том, что код приложения внедрен в пакет приложения (гибридный) или размещен на веб-сервере (PWA).

- **Гибридные веб-приложения**: код (HTML, JS, CSS) УПАКОВАН в apk и может распространяться с помощью Google Play Маркет. Подсистема просмотра изолирована от Интернет-браузера пользователей, нет общего доступа к сеансу или кэшированию.

- **Прогрессивные веб-приложения (пвас)**: код (HTML, JS, CSS) находится в Интернете и не нуждается в упаковке как apk. Ресурсы загружаются и обновляются по мере необходимости с помощью рабочей роли службы. Браузер Chrome выполнит визуализацию и отобразит ваше приложение, но будет искать собственный код и не включать в себя обычную адресную строку браузера и т. д. С помощью браузера можно предоставлять общий доступ к хранилищу, кэшу и сеансам. По сути, это аналогично установке ярлыка в браузере Chrome в специальном режиме. Пвас также можно найти в Google Play Маркет с помощью доверенного веб-действия.

Пвас и гибридные веб-приложения очень похожи на собственные приложения Android:

- Можно установить через App Store (Google Play Маркет и (или) Microsoft Store)
- Доступ к собственным функциям устройства, таким как камера, GPS, Bluetooth, уведомления и список контактов
- Работать вне сети (без подключения к Интернету)

Пвас также имеют несколько уникальных функций:

- Можно установить на начальном экране Android непосредственно из Интернета (без магазина приложений).
- Дополнительно можно установить через Google Play Маркет [с помощью доверенного веб-действия](https://css-tricks.com/how-to-get-a-progressive-web-app-into-the-google-play-store/) .
- Можно обнаружить с помощью веб-поиска или через URL-ссылку.
- Полагаться на [рабочую роль службы](https://developers.google.com/web/fundamentals/primers/service-workers) , чтобы избежать необходимости упаковывать машинный код

Для создания гибридного приложения или PWA не требуется платформа, но в этом разделе рассматриваются некоторые популярные платформы, в том числе PhoneGap (с Cordova) и ионные (с помощью Cordova или конденсатора).

## <a name="apache-cordova"></a>Apache Cordova.

[Apache Cordova](https://cordova.apache.org/) — это платформа с открытым исходным кодом, которая может упростить обмен данными между кодом JavaScript и собственной платформой Android с помощью [подключаемых модулей](https://cordova.apache.org/plugins/?platforms=cordova-android) [WebView](https://developer.android.com/reference/android/webkit/WebView) . Эти подключаемые модули предоставляют конечные точки JavaScript, которые можно вызывать из кода и использовать для вызова интерфейсов API устройства Android. К примерам подключаемых модулей Cordova относятся доступ к таким службам устройств, как состояние аккумулятора, доступ к файлам, звуки вибрации и кольца и т. д. Эти функции обычно недоступны для веб-приложений и браузеров.

Существует два распространенных дистрибутива Cordova:

- [PhoneGap](https://phonegap.com/)

- [Ionic](https://ionicframework.com/)

## <a name="adobe-phonegap"></a>Adobe PhoneGap

[PhoneGap](https://phonegap.com/). платформа, поддерживаемая Adobe, которая поддерживает Cordova с дополнительными инструментами, такими как [Командная строка](http://docs.phonegap.com/getting-started/1-install-phonegap/cli/), классическое [приложение](https://phonegap.com/products#desktop-app-section)и [Сборка PhoneGap](https://build.phonegap.com/), служба, которая позволяет передать код на сервер Adobe, который будет создавать собственные приложения без необходимости устанавливать собственные пакеты SDK на локальном компьютере. Это позволяет выполнять такие действия, как создание приложения iOS с помощью компьютера Windows.

### <a name="install-phonegap"></a>Установка PhoneGap

Чтобы приступить к созданию веб-приложения PWA или гибридной среды с помощью PhoneGap, необходимо сначала установить следующие средства:

- Node. js для взаимодействия с экосистемой ионных узлов. [Скачайте NodeJS для Windows](https://nodejs.org/en/) или следуйте [руководству по установке NodeJS](https://docs.microsoft.com/windows/nodejs/setup-on-wsl2) с помощью подсистемы Windows для Linux (WSL). Если вы будете работать с несколькими проектами и версией NodeJS, вы можете использовать [Диспетчер версий node (НВМ)](https://docs.microsoft.com/windows/nodejs/setup-on-wsl2#install-nvm-nodejs-and-npm) .

Установите PhoneGap, введя в командной строке следующее:

```bash
npm install -g phonegap
```

Чтобы создать новый проект PhoneGap, выполните действия, описанные в разделе [Приступая к работе](https://phonegap.com/getstarted/). Сведения о перемещении приложения из гибридной среды в PWA см. в разделе о [функциях PWA](http://stage.docs.phonegap.com/tutorials/stockpile/911-pwa-features/) документации PhoneGap.  

## <a name="ionic"></a>Ionic

[Ионная](https://ionicframework.com/) среда — это платформа, которая корректирует пользовательский интерфейс приложения в соответствии с языком разработки каждой платформы (Android, iOS, Windows). Ионные углы позволяют использовать либо [угловой](https://ionicframework.com/docs/developer-resources/guides/first-app-v4/intro) , либо [реагирующий](https://ionicframework.com/react).

> [!NOTE]
> Существует новая версия платформы ионных вызовов, которая использует альтернативу Cordova, именуемый [конденсатором](https://capacitor.ionicframework.com/). В этом альтернативном варианте используются контейнеры, чтобы сделать приложение [более удобным для веб-](https://ionicframework.com/blog/announcing-capacitor-1-0/)приложений.

### <a name="get-started-with-ionic-by-installing-required-tools"></a>Начало работы с ионными программами с помощью установки необходимых средств

Чтобы приступить к созданию веб-приложения PWA или гибридной среды с помощью ионных приложений, необходимо сначала установить следующие средства:

- Node. js для взаимодействия с экосистемой ионных узлов. [Скачайте NodeJS для Windows](https://nodejs.org/en/) или следуйте [руководству по установке NodeJS](https://docs.microsoft.com/windows/nodejs/setup-on-wsl2) с помощью подсистемы Windows для Linux (WSL). Если вы будете работать с несколькими проектами и версией NodeJS, вы можете использовать [Диспетчер версий node (НВМ)](https://docs.microsoft.com/windows/nodejs/setup-on-wsl2#install-nvm-nodejs-and-npm) .

- VS Code для написания кода. [Скачайте VS Code для Windows](https://code.visualstudio.com/). Также можно установить [Удаленное расширение WSL](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-wsl) , если вы предпочитаете создавать приложение с помощью командной строки Linux.

- Терминал Windows для работы с предпочтительным интерфейсом командной строки (CLI). [Установите терминал Windows с Microsoft Store](https://www.microsoft.com/en-us/p/windows-terminal-preview/9n0dx20hk701?activetab=pivot:overviewtab).

- Git для управления версиями. [Скачайте Git](https://git-scm.com/downloads).

## <a name="create-a-new-project-with-ionic-cordova-and-angular"></a>Создание нового проекта с помощью ионного и радиального элемента Cordova

Установите ионные и Cordova, введя в командной строке следующее:

```bash
npm install -g @ionic/cli cordova
```

Создайте угловое приложение ионного ключа с помощью шаблона приложения "Табуляция", введя команду:

```bash
ionic start photo-gallery tabs
```

Перейдите в папку приложения:

```bash
cd photo-gallery
```

Запустите приложение в веб-браузере:

```bash
ionic serve
```

Дополнительные сведения см. в разделе " [ионные угловые документы Cordova](https://ionicframework.com/docs/developer-resources/guides/first-app-v4/intro)". Сведения о том, как переместить приложение из гибридного приложения в PWA, см. в разделе [Создание приложения в угловом приложении в виде PWA](https://ionicframework.com/docs/angular/pwa) .

## <a name="create-a-new-project-with-ionic-capacitor-and-angular"></a>Создание нового проекта с конденсатором и углом

Установите ионные и Cordova-RES, введя в командной строке следующее:

```bash
npm install -g @ionic/cli native-run cordova-res
```

Создайте угловое приложение ионного ключа с помощью шаблона приложения "Табуляция" и добавьте конденсатор, введя команду:

```bash
ionic start photo-gallery tabs --type=angular --capacitor
```

Перейдите в папку приложения:

```bash
cd photo-gallery
```

Добавьте компоненты, чтобы сделать приложение PWA:

```bash
npm install @ionic/pwa-elements
```

Чтобы @ionic/pwa-elements импортировать, добавьте в `src/main.ts` файл следующее:

```typescript
import { defineCustomElements } from '@ionic/pwa-elements/loader';

// Call the element loader after the platform has been bootstrapped
defineCustomElements(window);
```

Запустите приложение в веб-браузере:

```bash
ionic serve
```

Дополнительные сведения см. в разделе « [конденсаторы ионных углов](https://ionicframework.com/docs/angular/your-first-app)». Сведения о том, как переместить приложение из гибридного приложения в PWA, см. в разделе [Создание приложения в угловом приложении в виде PWA](https://ionicframework.com/docs/angular/pwa) .  

## <a name="create-a-new-project-with-ionic-and-react"></a>Создание нового проекта с помощью ионных и откликов

Установите ионный интерфейс командной строки, введя в командной строке следующее:

```bash
npm install -g @ionic/cli
```

Создайте новый проект с откликом, введя команду:

```bash
ionic start myApp blank --type=react
```

Перейдите в папку приложения:

```bash
cd myApp
```

Запустите приложение в веб-браузере:

```bash
ionic serve
```

Дополнительные сведения см. в статье [реагирование на ионные](https://ionicframework.com/docs/react/quickstart)данные. Сведения о том, как переместить приложение из гибридного приложения в PWA, см. в разделе Создание ответного [приложения в](https://ionicframework.com/docs/react/pwa) статье о ионных документах.

## <a name="test-your-ionic-app-on-a-device-or-emulator"></a>Тестирование приложения на устройстве или в эмуляторе

Чтобы протестировать приложение на устройстве Android, подключаемый модуль ([Убедитесь, что он впервые включен для разработки](emulator.md#enable-your-device-for-development)), в командной строке введите:

```bash
ionic cordova run android
```

Чтобы протестировать приложение-ионное устройство в эмуляторе устройства Android, необходимо выполнить следующие действия.

1. [Установите необходимые компоненты — пакет средств разработки Java (JDK), Gradle и пакет SDK для Android](https://cordova.apache.org/docs/en/latest/guide/platforms/android/#installing-the-requirements).

2. [Создайте виртуальное устройство Android (AvD)](https://developer.android.com/studio/run/managing-avds.html).

3. Введите команду для ионных ресурсов, чтобы создать и развернуть приложение в эмуляторе: `ionic cordova emulate [<platform>] [options]`. В этом случае команда должна быть следующей:

```bash
ionic cordova emulate android --list
```

Дополнительные сведения см. в [эмуляторе Cordova](https://ionicframework.com/docs/cli/commands/cordova-emulate) в документации по ионным соединению.

## <a name="additional-resources"></a>Дополнительные ресурсы

- [Разработка приложений с двумя экранами для Android и получение пакета SDK для устройства Surface Duo](https://docs.microsoft.com/dual-screen/android/)

- [Добавление исключений защитника Windows для повышения производительности](defender-settings.md)

- [Включение поддержки виртуализации для повышения производительности эмулятора](emulator.md#enable-virtualization-support)
