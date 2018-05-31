---
author: normesta
Description: Run your packaged app and see how it looks without having to sign it. Then, set breakpoints and step through code. When you're ready to test your app in a production environment, sign your app and then install it.
Search.Product: eADQiWindows 10XVcnh
title: Запуск, отладка и тестирование упакованного классического приложения (мост для классических приложений)
ms.author: normesta
ms.date: 08/31/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, uwp
ms.assetid: f45d8b14-02d1-42e1-98df-6c03ce397fd3
ms.localizationpriority: medium
ms.openlocfilehash: 0f8d443bc201d0e60387673edb7f9b73e2e47490
ms.sourcegitcommit: 1773bec0f46906d7b4d71451ba03f47017a87fec
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/17/2018
ms.locfileid: "1662684"
---
# <a name="run-debug-and-test-a-packaged-desktop-app-desktop-bridge"></a>Запуск, отладка и тестирование упакованного классического приложения (мост для классических приложений)

Запустите неподписанное упакованное приложение и посмотрите, как оно выглядит. Затем установите точки останова и пошагово выполните код. Когда будете готовы протестировать приложение в рабочей среде, подпишите его, а затем установите. В данной статье приведено подробное описание выполнения каждого из этих действий.

<a id="run-app" />

## <a name="run-your-app"></a>Запуск приложения

Чтобы запустить приложение для локальной проверки, не обязательно получать сертификат и подписывать его. Способ запуска приложения зависит от инструмента, использованного для создания пакета.

### <a name="you-created-the-package-by-using-visual-studio"></a>Вы создали пакет с помощью Visual Studio

Назначьте упаковывающий проект стартовым и нажмите клавиши CTRL+F5, чтобы запустить приложение.

### <a name="you-created-the-package-manually-or-by-using-the-desktop-app-converter"></a>Вы создали пакет вручную или с помощью Desktop App Converter

Откройте командную строку Windows PowerShell и в подпапке **PackageFiles** выходной папки запустите этот командлет:

```
Add-AppxPackage –Register AppxManifest.xml
```
Чтобы запустить приложение, найдите его в меню "Пуск" Windows.

![Упакованное приложение в меню "Пуск"](images/desktop-to-uwp/converted-app-installed.png)

> [!NOTE]
> Упакованное приложение всегда выполняется от лица текущего пользователя, а диск, на который производится установка приложения, должен быть отформатирован в NTFS.

## <a name="debug-your-app"></a>Отладка приложения

Способ отладки приложения зависит от инструмента, использованного для создания пакета.

Если ваш пакет создан с помощью [нового проекта упаковки](desktop-to-uwp-packaging-dot-net.md#new-packaging-project), доступного в Visual Studio 2017, выпуск 15.4, просто задайте упаковывающий проект в качестве стартового проекта и нажмите сочетание клавиш CTRL+F5, чтобы отладить приложение.

Если вы создали пакет с помощью другого инструмента, выполните следующие действия.

1. Обязательно запустите свое упакованное приложение хотя бы один раз, чтобы оно полностью установилось на локальном компьютере.

   См. раздел [Запуск приложения](#run-app) выше.

2. Запустите Visual Studio.

   Если вы хотите выполнить отладку приложения с более высоким уровнем разрешений, запустите Visual Studio с помощью команды **Запуск от имени администратора**.

3. В Visual Studio выберите **Отладка**->**Другие цели отладки**->**Отладка установленного пакета приложения**.

4. В списке **Установленные пакеты приложения** выберите соответствующий пакет приложения, а затем нажмите кнопку **Вложить**.

#### <a name="modify-your-app-in-between-debug-sessions"></a>Изменение приложения между сеансами отладки

Чтобы внести в приложение изменения для устранения неполадок, распакуйте его с помощью средства MakeAppx. См. раздел [Запуск средства MakeAppx](desktop-to-uwp-manual-conversion.md#make-appx).

### <a name="debug-the-entire-app-lifecycle"></a>Отладка полного жизненного цикла приложения

В некоторых случаях вам может потребоваться более точное управление процессом отладки, включая возможность отладки приложения до его запуска.

Воспользуйтесь [PLMDebug](https://msdn.microsoft.com/library/windows/hardware/jj680085(v=vs.85).aspx), чтобы получить полный контроль над жизненным циклом приложения, включая приостановку, возобновление и завершение работы.

[PLMDebug](https://msdn.microsoft.com/library/windows/hardware/jj680085(v=vs.85).aspx) входит в состав Windows SDK.

## <a name="test-your-app"></a>Тестирование приложения

Чтобы протестировать приложение в реалистичной среде во время подготовки к распространению, лучше всего подписать приложение и затем установить его.

### <a name="test-an-app-that-you-packaged-by-using-visual-studio"></a>Тестирование приложения, упакованного с помощью Visual Studio

Visual Studio подписывает ваше приложение, используя тестовый сертификат. Этот сертификат находится в папке вывода, создаваемой мастером **Создание пакетов приложения**. Файл сертификата имеет расширение *.cer*, и необходимо установить этот сертификат в хранилище **Доверенные корневые центры сертификации** на ПК, на котором требуется протестировать ваше приложение. См. раздел [Загрузка пакета неопубликованного приложения](../packaging/packaging-uwp-apps.md#sideload-your-app-package).

### <a name="test-an-app-that-you-packaged-by-using-the-desktop-app-converter-dac"></a>Тестирование приложения, упакованного с помощью Desktop App Converter (DAC)

Если приложение упаковано с помощью Desktop App Converter, используйте параметр ``sign``, чтобы автоматически подписать приложение с помощью сгенерированного сертификата. Вам необходимо установить этот сертификат, а затем установить приложение. См. раздел [Запуск упакованного приложения](desktop-to-uwp-run-desktop-app-converter.md#run-app).   


### <a name="manually-sign-apps-optional"></a>Подпись приложений вручную (необязательно)

Кроме того, можно подписать приложение вручную. Вот как это сделать:

1. Создайте сертификат. См. статью [Создание сертификата](../packaging/create-certificate-package-signing.md).

2. Установите этот сертификат на свой компьютер в хранилище сертификатов **Доверенные корневые** или **Доверенные лица**.

3. Подпишите приложение с помощью этого сертификата; см. статью [Подписание пакета приложения с помощью SignTool](../packaging/sign-app-package-using-signtool.md).

  > [!IMPORTANT]
  > Убедитесь, что имя издателя на сертификате совпадает с именем издателя вашего приложения.

    **Связанный пример**

    [SigningCerts](https://github.com/Microsoft/DesktopBridgeToUWP-Samples/tree/master/Samples/SigningCerts)


### <a name="test-your-app-for-windows-10-s"></a>Тестирование приложения на Windows 10 S

Прежде чем публиковать приложение, убедитесь, что оно сможет корректно работать на устройствах под управлением Windows 10 S. На самом деле, если вы планируете опубликовать приложение в Microsoft Store, это надо сделать обязательно, поскольку таковы требования Store. Приложения, которые не могут правильно работать на устройствах под управлением Windows 10 S, не пройдут сертификацию.

См. статью [Тестирование приложения для Windows на Windows 10 S](https://docs.microsoft.com/windows/uwp/porting/desktop-to-uwp-test-windows-s).

### <a name="run-another-process-inside-the-full-trust-container"></a>Запуск другого процесса в контейнере с полным доверием

Вы можете вызывать пользовательские процессы внутри контейнера указанного пакета приложения. Это бывает полезно при тестировании,— например, если у вас пользовательское окружение теста и вам требуется протестировать выходные данные приложения. Сделать это можно с помощью командлета ```Invoke-CommandInDesktopPackage``` PowerShell:

```CMD
Invoke-CommandInDesktopPackage [-PackageFamilyName] <string> [-AppId] <string> [-Command] <string> [[-Args]
    <string>]  [<CommonParameters>]
```

## <a name="next-steps"></a>Дальнейшие действия

**Поиск ответов на вопросы**

Есть вопросы? Задайте их на Stack Overflow. Наша команда следит за этими [тегами](http://stackoverflow.com/questions/tagged/project-centennial+or+desktop-bridge). Вы также можете задать нам вопросы [здесь](https://social.msdn.microsoft.com/Forums/en-US/home?filter=alltypes&sort=relevancedesc&searchTerm=%5BDesktop%20Converter%5D).

**Оставьте отзыв или предложите новые возможности для реализации**

См. раздел [UserVoice](https://wpdev.uservoice.com/forums/110705-universal-windows-platform/category/161895-desktop-bridge-centennial)
