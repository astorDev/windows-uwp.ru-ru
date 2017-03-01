---
author: mcleblanc
ms.assetid: A5320094-DF53-42FC-A6BA-A958F8E9210B
title: "Проверка приложений Surface Hub с использованием Visual Studio"
description: "Имитатор Visual Studio обеспечивает среду для проектирования, разработки, отладки и тестирования приложений UWP, в том числе приложений, созданных для Surface Hub."
ms.author: markl
ms.date: 02/08/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, uwp
translationtype: Human Translation
ms.sourcegitcommit: c6b64cff1bbebc8ba69bc6e03d34b69f85e798fc
ms.openlocfilehash: 426670f9b8d24a770f74aa0bdfaa5abb043c39ef
ms.lasthandoff: 02/07/2017

---

# <a name="test-surface-hub-apps-using-visual-studio"></a>Проверка приложений Surface Hub с использованием Visual Studio
Имитатор Visual Studio предоставляет среду для проектирования, разработки, отладки и тестирования приложений универсальной платформы Windows (UWP), включая приложения, созданные для Microsoft Surface Hub. Имитатор не использует тот же пользовательский интерфейс, что и Surface Hub, но полезен для тестирования внешнего вида и поведения приложения в среде с тем же размером экрана и разрешением, что и Surface Hub.

Дополнительные сведения см. в разделе [Запуск приложений Магазина Windows в симуляторе](https://msdn.microsoft.com/library/hh441475.aspx).

## <a name="add-surface-hub-resolutions-to-the-simulator"></a>Добавление разрешений Surface Hub в имитатор
Чтобы добавить разрешения Surface Hub в имитатор, выполните указанные ниже действия.

1. Создайте конфигурацию для 55-дюймового Surface Hub, сохранив следующий элемент XML в файл с названием **HardwareConfigurations-SurfaceHub55.xml**.  

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <ArrayOfHardwareConfiguration xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                                  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
        <HardwareConfiguration>
            <Name>SurfaceHub55</Name>
            <DisplayName>Surface Hub 55"</DisplayName>
            <Resolution>
                <Height>1080</Height>
                <Width>1920</Width>
            </Resolution>
            <DeviceSize>55</DeviceSize>
            <DeviceScaleFactor>100</DeviceScaleFactor>
        </HardwareConfiguration>
    </ArrayOfHardwareConfiguration>
    ```

2. Создайте конфигурацию для 84-дюймового Surface Hub, сохранив следующий элемент XML в файл с названием **HardwareConfigurations-SurfaceHub84.xml**.

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <ArrayOfHardwareConfiguration xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                                  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
        <HardwareConfiguration>
            <Name>SurfaceHub84</Name>
            <DisplayName>Surface Hub 84"</DisplayName>
            <Resolution>
                <Height>2160</Height>
                <Width>3840</Width>
            </Resolution>
            <DeviceSize>84</DeviceSize>
            <DeviceScaleFactor>150</DeviceScaleFactor>
        </HardwareConfiguration>
    </ArrayOfHardwareConfiguration>
    ```

3. Скопируйте два XML-файла в папку **C:\Program Files (x86)\Common Files\Microsoft Shared\Windows Simulator\\&lt;version number&gt;\HardwareConfigurations**.

   > **Примечание.**&nbsp;&nbsp;Для сохранения файлов в эту папку требуются права администратора.

4. Запустите приложение в имитаторе Visual Studio. Нажмите кнопку **Изменить разрешение** на палитре и выберите конфигурацию Surface Hub из списка.

    ![Разрешения имитатора Visual Studio](images/vs-simulator-resolutions.png)

   > **Совет**&nbsp;&nbsp;[Включите режим планшета](http://windows.microsoft.com/windows-10/getstarted-like-a-tablet), чтобы лучше имитировать изображение на Surface Hub.

## <a name="deploy-apps-to-a-surface-hub-from-visual-studio"></a>Развертывание приложений в Surface Hub из Visual Studio
Развертывание приложения вручную является простым процессом.

### <a name="enable-developer-mode"></a>Включение режима разработчика
По умолчанию Surface Hub устанавливает только приложения из Магазина Windows. Для установки приложений, подписанных другими источниками, необходимо включить режим разработчика.

> **Примечание**.&nbsp;&nbsp;После включения режима разработчика необходимо будет сбросить Surface Hub для его повторного выключения. Сброс устройства удаляет все локальные файлы пользователя и конфигурации, а затем переустанавливает Windows.

1. В меню **Пуск** Surface Hub откройте приложение "Параметры".

   >  **Примечание.**&nbsp;&nbsp;Для получения доступа к приложению "Параметры" требуются права администратора.

2. Последовательно выберите элементы **Обновление и безопасность > Для разработчиков**.

3. Выберите пункт **Режим разработчика** и примите подсказку.

### <a name="deploy-your-app-from-visual-studio"></a>Развертывание приложения из Visual Studio
Дополнительные сведения см. в разделе [Развертывание и отладка приложений универсальной платформы Windows (UWP)](https://msdn.microsoft.com/windows/uwp/debug-test-perf/deploying-and-debugging-uwp-apps).

   > **Примечание.**&nbsp;&nbsp;Для этой функции требуется как минимум **Visual Studio 2015 с обновлением 1**.

1. Перейдите к раскрывающемуся списку цели отладки рядом с кнопкой **Начать отладку** и выберите элемент **Удаленный компьютер**.

    <!--lcap: in your screenshot, you have local machine selected-->

   ![Раскрывающийся список цели отладки Visual Studio](images/vs-debug-target.png)

2. Введите IP-адрес Surface Hub. Убедитесь, что выбран режим **Универсальный** проверки подлинности.

   > **Совет.**&nbsp;&nbsp;После включения режима разработчика IP-адрес Surface Hub можно найти на экране приветствия.

3. Выберите элемент **Начать отладку (F5)**, чтобы развернуть приложение и выполнить его отладку на Surface Hub или нажмите сочетание клавиш Ctrl+F5, чтобы просто развернуть приложение.

   > **Совет**.&nbsp;&nbsp;Если Surface Hub находится на экране приветствия, закройте его, нажав любую кнопку.

