---
ms.assetid: A5320094-DF53-42FC-A6BA-A958F8E9210B
title: Проверка приложений Surface Hub с использованием Visual Studio
description: Имитатор Visual Studio обеспечивает среду для проектирования, разработки, отладки и тестирования приложений UWP, в том числе приложений, созданных для Surface Hub.
ms.date: 10/26/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 37c7f9edbaee008b6e16ef2ca202ff5cbcf39ca2
ms.sourcegitcommit: 6f32604876ed480e8238c86101366a8d106c7d4e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/21/2019
ms.locfileid: "67317508"
---
# <a name="test-surface-hub-apps-using-visual-studio"></a>Проверка приложений Surface Hub с использованием Visual Studio
Имитатор Visual Studio предоставляет среду для проектирования, разработки, отладки и тестирования приложений универсальной платформы Windows (UWP), включая приложения, созданные для Microsoft Surface Hub. Симулятор не использует тот же пользовательский интерфейс в качестве Surface Hub, но он удобен для тестирования выглядит и ведет себя с помощью Surface Hub размер и разрешение экрана приложения.

Как правило, Дополнительные сведения о средстве симулятор см. в разделе [запуску приложений UWP в симуляторе](https://docs.microsoft.com/visualstudio/debugger/run-windows-store-apps-in-the-simulator).

## <a name="add-surface-hub-resolutions-to-the-simulator"></a>Добавление разрешений Surface Hub в имитатор
Чтобы добавить разрешения Surface Hub в имитатор, выполните указанные ниже действия.

1. Создать конфигурацию для 55" Surface Hub, сохранив приведенный ниже код XML в файл с именем *HardwareConfigurations SurfaceHub55.xml*.  

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

2. Создать конфигурацию для 84" Surface Hub, сохранив приведенный ниже код XML в файл с именем *HardwareConfigurations SurfaceHub84.xml*.

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

3. Скопируйте два файла XML в *\Common Files\Microsoft C:\Program Files (x86) симулятор Shared\Windows\\&lt;номер версии&gt;\HardwareConfigurations*.

   > [!NOTE]
   > Чтобы сохранить файлы в эту папку требуются права администратора.

4. Запустите приложение в имитаторе Visual Studio. Нажмите кнопку **Изменить разрешение** на палитре и выберите конфигурацию Surface Hub из списка.

    ![Разрешения имитатора Visual Studio](images/vs-simulator-resolutions.png)

   > [!TIP]
   > [Включить режим планшета](https://support.microsoft.com/help/17210/windows-10-use-your-pc-like-a-tablet) для более эффективной имитации опыт Surface Hub.

## <a name="deploy-apps-to-a-surface-hub-device-from-visual-studio"></a>Развертывание приложений на устройстве Surface Hub из Visual Studio
Ручное развертывание приложения с помощью Surface Hub — это простой процесс.

### <a name="enable-developer-mode"></a>Включение режима разработчика
По умолчанию Surface Hub устанавливает только приложения из Microsoft Store. Для установки приложений, подписанных другими источниками, необходимо включить режим разработчика.

> [!NOTE]
> После включения режима разработчика, необходимо будет сбросить Surface Hub в том случае, если вы хотите отключить его снова. Сброс устройства удаляет все локальные файлы пользователя и конфигурации, а затем переустанавливает Windows.

1. В меню **Пуск** Surface Hub откройте приложение "Параметры".

   > [!NOTE]
   > Требуются права администратора для доступа к приложению параметры в Surface Hub.

2. Перейдите к **обновление и безопасность \> для разработчиков**.

3. Выберите пункт **Режим разработчика** и примите подсказку.

### <a name="deploy-your-app-from-visual-studio"></a>Развертывание приложения из Visual Studio
Как правило, Дополнительные сведения о процессе развертывания см. в разделе [развертывание и отладка приложений UWP](https://docs.microsoft.com/windows/uwp/debug-test-perf/deploying-and-debugging-uwp-apps).

   > [!NOTE]
   > Этой функции требуется Visual Studio 2015 с обновлением 1 или более поздней версии, но мы рекомендуем использовать последнюю самую свежую версию Visual Studio. В актуальном состоянии экземпляра Visual Studio будет gibe вы все последние разработки и обновления для системы безопасности.

1. Перейдите к раскрывающемуся списку цели отладки рядом с кнопкой **Начать отладку** и выберите элемент **Удаленный компьютер**.

    <!--lcap: in your screenshot, you have local machine selected-->

   ![Раскрывающийся список цели отладки Visual Studio](images/vs-debug-target.png)

2. Введите IP-адрес Surface Hub. Убедитесь, что выбран режим **Универсальный** проверки подлинности.

   > [!TIP] 
   > После включения режима разработчика, Surface Hub IP-адрес можно найти на экране приветствия.

3. Выберите **начать отладку (F5)** для развертывания и отладки приложения на устройстве Surface Hub или нажмите клавиши Ctrl + F5, чтобы просто разверните приложение.

   > [!TIP]
   > Если Surface Hub отображает экран приветствия, закройте его, выбрав любую кнопку.
