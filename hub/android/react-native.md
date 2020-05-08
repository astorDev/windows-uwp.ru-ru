---
title: Реагирование на машинный код для разработки Android в Windows
description: Приступите к разработке приложений Android с помощью Xamarin Native в Windows.
author: mattwojo
ms.author: mattwoj
manager: jken
ms.topic: article
keywords: Android, Windows, реагирование на машинный код, эмулятор, экспозиции, пакет Metro, терминал
ms.date: 04/28/2020
ms.openlocfilehash: db49e3ed12fee8e7ced7680e305a84afb89f32a2
ms.sourcegitcommit: 24b19e7ee06e5bb11a0dae334806741212490ee9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/29/2020
ms.locfileid: "82255238"
---
# <a name="get-started-developing-for-android-using-react-native"></a>Приступая к разработке для Android с помощью реакции на собственный

Это руководство поможет вам приступить к работе с Windows для создания кросс-платформенного приложения, которое будет работать на устройствах Android.

## <a name="overview"></a>Обзор

Реакция на машинный код — это платформа мобильных приложений с [открытым исходным кодом](https://github.com/facebook/react-native) , созданная Facebook. Он используется для разработки приложений для Android, iOS, Web и UWP (Windows), предоставляющих собственные элементы управления пользовательского интерфейса и полный доступ к собственной платформе. Работа с реагированием на машинный код требует понимания основ JavaScript.

## <a name="get-started-with-react-native-by-installing-required-tools"></a>Начало работы с реагированием с помощью установки необходимых средств

1. [Установите Visual Studio Code](https://code.visualstudio.com) (или в любом редакторе кода).

2. [Установите Android Studio для Windows](https://developer.android.com/studio). По умолчанию Android Studio устанавливает последнюю пакет SDK для Android. Для реагирования на Native требуется пакет SDK для Android 6,0 (Marshmallow) или более поздней версии. Мы рекомендуем использовать последнюю версию пакета SDK.

3. Создайте пути к переменным среды для пакета SDK для Java и пакет SDK для Android:
    - В меню поиска Windows введите: "изменить системные переменные среды". Откроется окно " **Свойства системы** ".
    - Выберите **переменные среды..** . и нажмите кнопку **создать...** в разделе **User Variables**.
    - Введите имя и значение переменной (путь). Ниже приведены пути по умолчанию для пакетов SDK для Java и Android. Если вы выбрали конкретное расположение для установки пакетов SDK для Java и Android, обязательно обновите пути к переменной.
        - JAVA_HOME: C:\Program Филес\андроид\андроид Студио\жре\жре
        - ANDROID_HOME: К:\усерс\усернаме\аппдата\локал\андроид\сдк

    ![Снимок экрана добавления переменной среды PATH](../images/add-environmental-variable-path.png)

4. [Установка NodeJS для Windows](https://nodejs.org/en/) Вы можете использовать [Диспетчер версий node (НВМ) для Windows](https://github.com/coreybutler/nvm-windows#node-version-manager-nvm-for-windows) , если работаете с несколькими проектами и версией NodeJS. Мы рекомендуем установить последнюю версию LTS для новых проектов.

> [!NOTE]
> Также можно установить и использовать [терминал Windows](https://www.microsoft.com/p/windows-terminal-preview/9n0dx20hk701?activetab=pivot:overviewtab) для работы с предпочтительным интерфейсом командной строки (CLI), а также [Git для управления версиями](https://git-scm.com/downloads). [JDK Java](https://www.oracle.com/java/technologies/javase-downloads.html) поставляется с Android Studio v 2.2 +, но если вам нужно обновить JDK отдельно от Android Studio, используйте [установщик Windows x64](https://www.oracle.com/java/technologies/javase-jdk14-downloads.html).

## <a name="create-a-new-project-with-react-native"></a>Создание нового проекта с реагированием на собственные

1. Используйте NPM для установки служебной программы командной строки [экспозиции CLI](https://docs.expo.io/versions/latest/) в командной строке Windows, PowerShell, в [терминале Windows](https://www.microsoft.com/p/windows-terminal-preview/9n0dx20hk701?activetab=pivot:overviewtab)или в интегрированном терминале в VS Code (представление встроенный терминал >).

    ```powershell
    npm install -g expo-cli
    ```

2. Используйте экспозиции для создания собственного приложения, работающего в iOS, Android и Web. Затем необходимо выбрать один из шаблонов проектов, включая **пустые**, **пустые (TypeScript)**, **вкладки** (например, экраны с использованием реакции), **Минимальный**или **минимальный (typescript)**.

    ```powershell
    expo init my-new-app
    ```

    > [!NOTE]
    > Если вы используете `npx create-react-native-app`, который по-прежнему будет работать, но экспозиции-CLI init предоставляет [несколько дополнительных преимуществ](https://github.com/react-native-community/discussions-and-proposals/issues/23).

3. Откройте новый каталог My-New-App.

    ```powershell
    cd my-new-app
    ```

4. Чтобы запустить проект, введите следующую команду. Откроется окно localhost в веб-браузере по умолчанию, отображающем узел Metro. Он также будет отображать QR-код как в командной строке, так и в окне браузера «Metro пучок». * Можно также использовать команду: `npm start` или. `npm run android`

     ```powershell
    expo start
    ```

    ![Снимок экрана: комплект Metro в браузере](../images/metro-bundler.png)

5. Чтобы просмотреть проект, выполняющийся на устройстве Android, необходимо сначала [установить клиентское приложение экспозиции с Google Play Маркет](https://play.google.com/store/apps/details?id=host.exp.exponent&hl=en_US) на устройстве Android. После установки клиентского приложения экспозиции откройте его на устройстве и выберите **сканировать QR-код**. После регистрации QR-кода вы сможете увидеть сборку пакета как на устройстве, так и в окне «пакет Metro», работающем на локальном узле в браузере.

6. Чтобы просмотреть проект, выполняющийся в эмуляторе Android, сначала необходимо открыть Android Studio, а затем создать и запустить виртуальное устройство. **Tools** > **AVD Manager** > **[+ создать виртуальное устройство...](https://developer.android.com/studio/run/managing-avds#createavd)** После создания виртуального устройства нажмите кнопку запустить ▷ в столбце **действия** виртуальной Device Manager Android, чтобы начать эмуляцию устройства. После открытия виртуального устройства вернитесь в окно "пакет Metro", работающее в окне Интернет-браузера, и выберите "запустить на устройстве или эмуляторе Android" в левом столбце. Вы увидите всплывающее окно с информацией о том, что «Metro» («попытка открытия симулятора»). а затем просмотрите клиентское приложение экспозиции, открытое на устройстве с эмуляцией Android, и после того как Загрузка пакета JavaScript будет завершена, отобразится ваше собственное приложение для реагирования. (При возникновении проблем [Проверьте документацию по эмулятору Android экспозиции](https://docs.expo.io/workflow/android-studio-emulator/).)

7. Откройте собственный проект реагирования, чтобы начать работу над приложением. Изменения, внесенные в приложение, которое выполняется с помощью клиента экспозиции на устройстве или в Android Emulator, должны отобразиться в автоматическом обновлении.

8. Попробуйте изменить текст представления целевой страницы следующим образом: "Hello World!". Это можно сделать в IDE по своему усмотрению. (Рекомендуется VS Code или Android Studio.) Файл целевой страницы будет отличаться в зависимости от выбранного шаблона. Это может быть `App.js`, `App.tsx`или `HomeScreen.js`.

    ```typescript
    export default function App() {
      return (
        <View style={styles.container}>
          <Text>Hello World!</Text>
        </View>
      );
    }
    ```

9. Попробуйте добавить изображение. Во-первых, необходимо создать папку на том же уровне, что и папки "Android" и "iOS" в приложении, назовем ее "изображениями". Поместите изображение в эту папку, `your-image.png` например. Используйте приведенный ниже формат для ссылки на изображение и его стиля с высотой и шириной.

     ```typescript
    export default function App() {
      return (
        <View style={styles.container}>
          <Text>Hello World!</Text>
          <Image source={require('./images/your-image.png')} style = {{height: 200, width: 250, }} />
        </View>
      );
    }
    ```

> [!TIP]
> Если вы хотите добавить поддержку вашего собственного приложения для реагирования, чтобы он выполнялся как приложение Windows 10, см. статью Начало [работы с реагированием на системные документы Windows](https://microsoft.github.io/react-native-windows/docs/getting-started) .

## <a name="additional-resources"></a>Дополнительные ресурсы

- [Разработка приложений с двумя экранами для Android и получение пакета SDK для устройства Surface Duo](https://docs.microsoft.com/dual-screen/android/)

- [Добавление исключений защитника Windows для повышения производительности](defender-settings.md)

- [Включение поддержки виртуализации для повышения производительности эмулятора](emulator.md#enable-virtualization-support)
