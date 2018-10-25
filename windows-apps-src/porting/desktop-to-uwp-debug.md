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
ms.openlocfilehash: b5110eebde087593f07704e89c2e4708b2fcbb8b
ms.sourcegitcommit: 2c4daa36fb9fd3e8daa83c2bd0825f3989d24be8
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "5512158"
---
# <a name="run-debug-and-test-a-packaged-desktop-application"></a>Запуск, отладка и тестирование упакованного классического приложения

Запуск упакованного приложения и посмотрите, как оно выглядит без необходимости подписать его. Затем установите точки останова и пошагово выполните код. Когда вы будете готовы протестировать приложение в рабочей среде, подпишите свое приложение, а затем установите его. В данной статье приведено подробное описание выполнения каждого из этих действий.

<a id="run-app" />

## <a name="run-your-application"></a>Запуск приложения

Можно запустить приложение для проверки, локально без необходимости получать сертификат и подписывать его. Способ запуска приложения зависит от инструмента для создания пакета.

### <a name="you-created-the-package-by-using-visual-studio"></a>Вы создали пакет с помощью Visual Studio

Назначьте упаковывающий проект стартовым и нажмите клавиши CTRL+F5, чтобы запустить приложение.

### <a name="you-created-the-package-manually-or-by-using-the-desktop-app-converter"></a>Вы создали пакет вручную или с помощью Desktop App Converter

Откройте командную строку Windows PowerShell и в подпапке **PackageFiles** выходной папки запустите этот командлет:

```
Add-AppxPackage –Register AppxManifest.xml
```
Чтобы запустить приложение, найдите его в меню "Пуск" Windows.

![Упакованные приложения в меню "Пуск"](images/desktop-to-uwp/converted-app-installed.png)

> [!NOTE]
> Упакованное приложение всегда выполняется от имени текущего пользователя, а диск, на который установки упакованного приложения на должен быть отформатирован в NTFS.

## <a name="debug-your-app"></a>Отладка приложения

Способ отладки приложения зависит от инструмента для создания пакета.

Если ваш пакет создан с помощью [нового проекта упаковки](desktop-to-uwp-packaging-dot-net.md#new-packaging-project), доступного в Visual Studio 2017, выпуск 15.4, просто задайте упаковывающий проект в качестве стартового проекта и нажмите сочетание клавиш CTRL+F5, чтобы отладить приложение.

Если вы создали пакет с помощью другого инструмента, выполните следующие действия.

1. Убедитесь, что Запуск упакованного приложения хотя бы один раз, таким образом, чтобы оно полностью установилось на локальном компьютере.

   См. раздел [Запуск приложения](#run-app) выше.

2. Запустите Visual Studio.

   Если вы хотите выполнить отладку приложения с повышенным уровнем разрешений, запустите Visual Studio с помощью параметра **Запуск от имени администратора** .

3. В Visual Studio выберите **Отладка**->**Другие цели отладки**->**Отладка установленного пакета приложения**.

4. В списке **Установленные пакеты приложения** выберите соответствующий пакет приложения, а затем нажмите кнопку **Вложить**.

#### <a name="modify-your-application-in-between-debug-sessions"></a>Изменение приложения между сеансами отладки

При внесении изменений в приложение для устранения неполадок, распакуйте его с помощью средства MakeAppx. См. раздел [Запуск средства MakeAppx](desktop-to-uwp-manual-conversion.md#make-appx).

### <a name="debug-the-entire-application-lifecycle"></a>Отладка весь жизненный цикл приложения

В некоторых случаях вам может потребоваться более точное управление процессом отладки, включая возможность отладки приложения до его запуска.

[PLMDebug](https://msdn.microsoft.com/library/windows/hardware/jj680085(v=vs.85).aspx) можно использовать, чтобы получить полный контроль над жизненным циклом приложения благодаря функциям приостановки, возобновления и завершения.

[PLMDebug](https://msdn.microsoft.com/library/windows/hardware/jj680085(v=vs.85).aspx) входит в состав Windows SDK.

## <a name="test-your-app"></a>Тестирование приложения

Чтобы протестировать приложение в реалистичной среде как подготовки к распространению, лучше всего подписать приложение и затем установить его.

### <a name="test-an-application-that-you-packaged-by-using-visual-studio"></a>Тестирование приложения, упакованного с помощью Visual Studio

Visual Studio подписывает ваше приложение, используя тестовый сертификат. Этот сертификат находится в папке вывода, создаваемой мастером **Создание пакетов приложения**. Файл сертификата имеет расширение *.cer* , и вам придется установить этот сертификат в хранилище **Доверенных корневых центров сертификации** на Компьютере, на котором вы хотите протестировать приложение на. См. раздел [Загрузка пакета неопубликованного приложения](../packaging/packaging-uwp-apps.md#sideload-your-app-package).

### <a name="test-an-application-that-you-packaged-by-using-the-desktop-app-converter-dac"></a>Тестирование приложения, упакованного с помощью Desktop App Converter (DAC)

Если приложение упаковано с помощью Desktop App Converter, вы можете использовать ``sign`` параметр для автоматического подписывания приложения, используя созданный сертификат. Вам необходимо установить этот сертификат, а затем установить приложение. См. раздел [Запуск упакованного приложения](desktop-to-uwp-run-desktop-app-converter.md#run-app).   


### <a name="manually-sign-apps-optional"></a>Подпись приложений вручную (необязательно)

Также можно подписать приложение вручную. Вот как это сделать:

1. Создайте сертификат. См. статью [Создание сертификата](../packaging/create-certificate-package-signing.md).

2. Установите этот сертификат на свой компьютер в хранилище сертификатов **Доверенные корневые** или **Доверенные лица**.

3. Подписать приложение с помощью этого сертификата, см. в разделе [входа пакета приложения с помощью SignTool](../packaging/sign-app-package-using-signtool.md).

  > [!IMPORTANT]
  > Убедитесь, что имя издателя на сертификате совпадает с именем издателя вашего приложения.

    **Связанный пример**

    [SigningCerts](https://github.com/Microsoft/DesktopBridgeToUWP-Samples/tree/master/Samples/SigningCerts)


### <a name="test-your-application-for-windows-10-s"></a>Протестируйте приложение для Windows 10 S

Прежде чем публиковать приложение, убедитесь, что оно сможет корректно работать на устройствах под управлением Windows 10 S. На самом деле Если вы планируете опубликовать приложение в Microsoft Store, это нужно сделать так как это требование магазина. Приложения, которые не могут правильно работать на устройствах под управлением Windows 10 S, не пройдут сертификацию.

См. в разделе [Тестирование приложения для Windows для Windows 10 S](https://docs.microsoft.com/windows/uwp/porting/desktop-to-uwp-test-windows-s).

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
