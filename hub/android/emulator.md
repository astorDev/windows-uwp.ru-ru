---
title: Запуск устройства или эмулятора Android из Windows
description: Протестируйте приложение на устройстве или эмуляторе Android из Windows и включите виртуализацию с Hyper-v и платформой низкоуровневой оболочки Windows (ВХПКС).
author: mattwojo
ms.author: mattwoj
manager: jken
ms.topic: article
keywords: Android, Windows, эмулятор, виртуальное устройство, установка устройства, включение устройства, разработчика, конфигурация, виртуализация, Visual Studio, Hyper-v, Intel, haxm, AMD, платформа гипервизора Windows, ВХПКС
ms.date: 04/28/2020
ms.openlocfilehash: c651661d573695902368ffa595ce5d3014791a9a
ms.sourcegitcommit: 24b19e7ee06e5bb11a0dae334806741212490ee9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/29/2020
ms.locfileid: "82255158"
---
# <a name="test-on-an-android-device-or-emulator"></a>Тестирование на устройстве или эмуляторе Android

Существует несколько способов тестирования и отладки приложения Android с помощью реального устройства или эмулятора на компьютере Windows. В этом разделе описаны некоторые рекомендации.

## <a name="run-on-a-real-android-device"></a>Запуск на реальном устройстве Android

Чтобы запустить приложение на реальном устройстве Android, сначала необходимо включить устройство Android для разработки. Параметры разработчика на Android были скрыты по умолчанию, начиная с версии 4,2, и их включение может варьироваться в зависимости от версии Android.

### <a name="enable-your-device-for-development"></a>Подготовка устройства к разработке

Для устройства под управлением последней версии Android 9.0 +:

1. Подключите устройство к компьютеру разработки Windows с помощью USB-кабеля. Вы можете получить уведомление для установки драйвера USB.
2. Откройте экран **параметров** на устройстве Android.
3. Выберите **о телефоне**.
4. Прокрутите вниз и коснитесь пункта **номер сборки** семь раз, пока **вы не станете разработчиком!** является видимым.
5. Вернитесь на предыдущий экран, выберите **система**.
6. Выберите **Дополнительно**, прокрутите вниз и нажмите кнопку **Параметры разработчика**.
7. В окне **Параметры разработчика** прокрутите вниз, чтобы найти и включить **отладку по USB**.

Сведения об устройстве, на котором работает более старая версия Android, см. в разделе [Настройка устройства для разработки](https://docs.microsoft.com/xamarin/android/get-started/installation/set-up-device-for-development).

### <a name="run-your-app-on-the-device"></a>Запуск приложения на устройстве

1. На панели инструментов Android Studio выберите свое приложение в раскрывающемся меню **Run Configurations (конфигурации запуска** ).

    ![Меню конфигурации запуска Android Studio](../images/android-run-config-menu.png)

2. В раскрывающемся меню **целевое устройство** выберите устройство, на котором требуется запустить приложение.

    ![Меню целевого устройства Android Studio](../images/android-target-device-menu.png)

3. Выберите запустить ▷. Это приведет к запуску приложения на подключенном устройстве.

## <a name="run-your-app-on-a-virtual-android-device-using-an-emulator"></a>Запуск приложения на виртуальном устройстве Android с помощью эмулятора

Первое, что нужно узнать о запуске эмулятора Android на компьютере Windows, это то, что независимо от интегрированной среды разработки (Android Studio, Visual Studio и т. д.) производительность эмулятора значительно улучшена за счет поддержки виртуализации.

### <a name="enable-virtualization-support"></a>Включить поддержку виртуализации

Перед созданием виртуального устройства с помощью эмулятора Android рекомендуется включить виртуализацию, включив компоненты Hyper-V и платформы низкоуровневой оболочки Windows (ВХПКС). Это позволит процессору компьютера значительно повысить скорость выполнения эмулятора.

> Для запуска платформы Hyper-V и низкоуровневой оболочки Windows компьютер должен:
>
> * Доступно 4 ГБ памяти
> * Наличие 64-разрядного процессора Intel или ЦП AMD ризен с преобразованием адресов второго уровня (SLAT)
> * Запустите Windows 10 Build 1803 + ([Проверьте номер сборки](ms-settings:about))
> * Обновлены графические драйверы (Device Manager > видеоадаптеры, > обновление драйвера)
>
> Если компьютер не соответствует этим критериям, вы можете запустить гипервизор [Intel HAXM](https://github.com/intel/haxm/wiki/Installation-Instructions-on-Windows) или [AMD](https://github.com/google/android-emulator-hypervisor-driver-for-amd-processors). Дополнительные сведения см. в статье [аппаратное ускорение для производительности эмулятора](https://docs.microsoft.com/xamarin/android/get-started/installation/android-emulator/hardware-acceleration) или в [документации по эмулятору Android Studio](https://developer.android.com/studio/run/emulator).

1. Убедитесь, что оборудование и программное обеспечение компьютера совместимы с Hyper-V, открыв командную строку и введя следующую команду:`systeminfo`

    ![Требования к Hyper-V из systeminfo в командной строке](../images/systeminfo.png)

2. В поле поиска Windows (в нижнем левом углу) введите "компоненты Windows". Выберите **Включение или отключение компонентов Windows** в результатах поиска.

3. После появления списка **компонентов Windows** прокрутите экран, чтобы найти **Hyper-V** (включая средства управления и платформу) и **платформу низкоуровневой оболочки Windows**, убедитесь, что флажок установлен, чтобы включить обе функции, а затем нажмите кнопку **ОК**.

4. При появлении соответствующего запроса перезагрузите компьютер.

### <a name="emulator-for-native-development-with-android-studio"></a>Эмулятор для разработки машинного кода с Android Studio

При создании и тестировании собственного приложения Android рекомендуется [использовать Android Studio](./native-android.md). Когда приложение будет готово к тестированию, вы можете создать и запустить приложение, выполнив следующие действия.

1. На панели инструментов Android Studio выберите свое приложение в раскрывающемся меню **Run Configurations (конфигурации запуска** ).

    ![Меню конфигурации запуска Android Studio](../images/android-run-config-menu.png)

2. В раскрывающемся меню **целевое устройство** выберите устройство, на котором требуется запустить приложение.

    ![Меню целевого устройства Android Studio](../images/android-target-device-menu.png)

3. Выберите запустить ▷. Запустится [Android Emulator](https://developer.android.com/studio/run/emulator).

> [!TIP]
> После установки приложения на устройстве эмулятора можно использовать параметр [Применить изменения](https://developer.android.com/studio/run#apply-changes) для развертывания определенных изменений кода и ресурсов без создания нового apk.

### <a name="emulator-for-cross-platform-development-with-visual-studio"></a>Эмулятор для кросс-платформенной разработки с помощью Visual Studio

Для компьютеров с Windows доступно множество [вариантов эмулятора Android](https://www.androidauthority.com/best-android-emulators-for-pc-655308/) . Мы рекомендуем использовать [эмулятор Android](https://developer.android.com/studio/run/emulator)Google, так как он предоставляет доступ к последним образам ОС Android и Google Play службам.

### <a name="install-android-emulator-with-visual-studio"></a>Установка эмулятора Android с помощью Visual Studio

1. Если вы еще не установили его, скачайте [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/). Используйте Visual Studio Installer, чтобы [Изменить рабочие нагрузки](https://docs.microsoft.com/visualstudio/install/modify-visual-studio?view=vs-2019#modify-workloads) и убедиться, что у вас есть **Рабочая нагрузка для разработки мобильных приложений на .NET**.

2. Создайте новый проект. После [настройки Android Emulator](https://docs.microsoft.com/xamarin/android/get-started/installation/android-emulator/)можно использовать [Android Device Manager](https://docs.microsoft.com/xamarin/android/get-started/installation/android-emulator/device-manager?tabs=windows&pivots=windows#requirements) для создания, дублирования, настройки и запуска различных виртуальных устройств Android. Запустите Android Device Manager из меню Сервис с помощью средства: **инструменты** > **Android** > **Android Device Manager**.

3. После открытия Android Device Manager выберите **+ создать** , чтобы создать новое устройство.

4. Необходимо присвоить имя устройству, выбрать тип базового устройства из раскрывающегося меню, выбрать процессор и версию ОС, а также несколько других переменных для виртуального устройства. Дополнительные сведения см. [Android Device Manager главном экране](https://docs.microsoft.com/xamarin/android/get-started/installation/android-emulator/device-manager?tabs=windows&pivots=windows#main-screen).

5. На панели инструментов Visual Studio выберите **Отладка** (присоединяется к процессу приложения, выполняемому в эмуляторе после запуска приложения) или режим **выпуска** (отключает отладчик). Затем выберите виртуальное устройство в раскрывающемся меню устройство и нажмите кнопку Play ▷ ( **воспроизвести** ), чтобы запустить приложение в эмуляторе.

    ![Android Emulator запуска Visual Studio](../images/vs-target-device-menu.png)

## <a name="additional-resources"></a>Дополнительные ресурсы

- [Разработка приложений с двумя экранами для Android и получение пакета SDK для устройства Surface Duo](https://docs.microsoft.com/dual-screen/android/)

- [Добавление исключений защитника Windows для повышения производительности](defender-settings.md)
