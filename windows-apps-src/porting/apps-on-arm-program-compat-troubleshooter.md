---
title: Средство устранения проблем с совместимостью программ в ARM
author: msatranjr
description: Руководство по регулированию настроек совместимости в случае неправильной работы приложения в ARM
ms.author: misatran
ms.date: 02/15/2018
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10 s, постоянно подключенный, средство устранения неполадок с совместимостью, windows в ARM
ms.localizationpriority: medium
ms.openlocfilehash: d517b2e455f6ad75d3158c7899ad70a774cabc46
ms.sourcegitcommit: 11edca90aaf7856c762e68903483079d30ad3877
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/19/2018
ms.locfileid: "1595187"
---
# <a name="program-compatibility-troubleshooter-on-arm"></a>Средство устранения проблем с совместимостью программ в ARM
Эмуляция для поддержки приложений x86— новая функция, созданная для Windows 10 в ARM64. Иногда эмуляция выполняет оптимизации, которые не повышают удобство работы. Можно использовать средство устранения проблем с совместимостью программ для переключения параметров эмуляции для вашего приложения x86, уменьшая число оптимизаций по умолчанию и потенциально повышая совместимость.

## <a name="start-the-program-compatibility-troubleshooter"></a>Запуск средства устранения проблем с совместимостью программ
Запустить [Средство устранения проблем с совместимостью программ](https://support.microsoft.com/en-us/help/15078/windows-make-older-programs-compatible) можно вручную одинаковым способом на любом ПК с Windows 10: щелкните правой кнопкой мыши выполняемый файл (.exe) и выберите **Устранить проблемы с совместимостью**. Отобразится следующий экран.

![Снимок экрана "Устранение проблем с совместимостью"](images/arm/Capture4.png)

Если щелкнуть **Устранение неполадок программы**, отобразятся следующие варианты.

![Снимок экрана "Устранение проблем с совместимостью"](images/arm/Capture5.png)

Все варианты включают параметры, которые актуальны на всех настольных ПК с Windows 10. Кроме того, первый, второй и четвертый варианты применяют параметры эмуляции [Отключить кэш приложения](#disable-app-cache) и [Отключить гибридный режим выполнения](#disable-hybrid-exec-mode).

## <a name="toggling-emulation-settings"></a>Переключение параметров эмуляции
> [!WARNING]
> Изменение параметров эмуляции может привести к неожиданному завершению работы приложения или невозможности запустить его.

Чтобы переключить параметры эмуляции, нажмите исполняемый файл правой кнопкой мыши и выберите **Свойства**.

В ARM раздел **Windows 10 в ARM** будет доступен на вкладке **Совместимость**. Щелкните **Изменить параметры эмуляции**, чтобы запустить второе окно, как показано здесь.

![Снимок экрана изменения параметров эмуляции](images/arm/Capture.png)

В этом окне представлены два способа изменения параметров эмуляции. Можно выбрать заранее определенную группу параметров эмуляции или щелкнуть **Использовать дополнительные параметры**, чтобы обеспечить возможность выбора отдельных параметров.

Сгруппированные параметры эмуляции позволяют уменьшить число оптимизаций производительности в пользу качества. Ниже представлены некоторые сгруппированные параметры, которые можно выбрать.

![Снимок экрана изменения параметров эмуляции 2](images/arm/Capture2.png)

Выберите **Использовать дополнительные параметры**, чтобы выбрать отдельные параметры, как описано в этой таблице.

| Параметр эмуляции | Результат |
| ----------------- | ----------- |
| <p id="disable-app-cache">Выключить кэш приложения</p> | Операционная система будет кэшировать скомпилированные блоки кода, чтобы уменьшить нагрузку эмуляции на последующие выполнения. Этот параметр требует, чтобы эмулятор перекомпилировал весь код приложения во время выполнения. |
| <p id="disable-hybrid-exec-mode">Отключить гибридный режим выполнения</p> | Скомпилированные гибридные переносимые исполняемые (CHPE) двоичные файлы— это совместимые с x86 двоичные файлы, которые содержат встроенный код ARM64 для повышения производительности. Они, однако, могут быть несовместимы с некоторыми приложениями. Этот параметр обеспечивает принудительное использование двоичных файлов с поддержкой только x86. |
| Поддержка строгого самостоятельно меняющегося кода | Включите этот параметр, чтобы гарантировать правильную поддержку самостоятельно меняющегося кода в эмуляции. Самые распространенные сценарии с самостоятельно меняющимся кодом охвачены поведением эмулятора по умолчанию Включение этот параметра значительно уменьшает производительность самостоятельно меняющегося кода во время выполнения. |
| Отключение оптимизации производительности страницы RWX | Эта оптимизация повышает производительность кода на страницах с поддержкой чтения, записи и выполнения (RWX), но может быть несовместима с некоторыми приложениями. |

Также можно выбрать многоядерные параметры, как показано здесь.

![Снимок экрана многоядерных параметров](images/arm/Capture3.png)

Эти параметры изменяют число барьеров памяти, используемых для синхронизации доступов к памяти между ядрами в приложениях во время эмуляции. **Быстро**— это режим по умолчанию, однако параметры **строго** и **очень строго** увеличивают число барьеров. Это замедляет работу приложения, но уменьшает риск ошибок приложения. **Одноядерный** вариант устраняет все барьеры, однако заставляет все потоки приложения работать на одном ядре.

Если изменение определенного параметра решит вашу проблему, отправьте сообщение электронной почты с подробностями по адресу *woafeedback@microsoft.com*, чтобы мы могли учесть вашу обратную связь.