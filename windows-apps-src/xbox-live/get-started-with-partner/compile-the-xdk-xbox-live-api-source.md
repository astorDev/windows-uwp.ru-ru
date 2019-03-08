---
title: Скомпилируйте исходный XDK Xbox Live API
description: Узнайте, как скомпилировать источника Xbox Live API, который поставляется с комплект разработчика Xbox (XDK).
ms.assetid: 78425e82-c132-4f6b-9db3-2536862f1ce5
ms.date: 04/04/2017
ms.topic: article
keywords: Xbox live, xbox, игры, универсальной платформы Windows, windows 10, xbox, один, xdk
ms.localizationpriority: medium
ms.openlocfilehash: 9a98af637c8c60449cd2005c4fc6f83f9b0719cf
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57660999"
---
# <a name="compile-the-xbox-developer-kit-xdk-xbox-live-api-source"></a>Скомпилируйте исходный Xbox Developer Kit (XDK) Xbox Live API

Комплект разработчика Xbox (XDK) включает в себя источник для создания Microsoft.Xbox.Services.dll (XSAPI). Разработчики могут следуйте этим инструкциям для обновления своих проектов для использования локальной сборки библиотеки DLL.

Вы можете построить XSAPI самостоятельно, если:
1. Если вы хотите выполнить отладку выпуска, чтобы понять, откуда поступают код ошибки.
1. Если мы предоставляем исправление кода источника, чтобы устранить ошибку, прежде чем мы можно распространять QFE.

## <a name="to-compile-the-xdk-c-xsapi-project-for-yourself"></a>Чтобы скомпилировать проект XDK C++ XSAPI самостоятельно

<ol>
  <li> Получение Microsoft.Xbox.Services исходного кода. Чтобы сделать это, извлеките все файлы из «%XboxOneExtensionSDKLatest%\ExtensionSDKs\Xbox API\8.0\SourceDist\Xbox.Services.zip служб» в папку для записи за пределами «C:\Program Files (x86)», или вы можете клонировать источник, из <a href ="https://github.com/Microsoft/xbox-live-api">https://github.com/Microsoft/xbox-live-api</a></li>
  <li> Если проект ссылается на библиотеку DLL перед сборкой, необходимо удалить ссылку</li>
    <ul>
      <li> Для Visual Studio 2012: Выберите «Проект "->" ссылки...» в Visual Studio. Если API служб Xbox указана как ссылка, выберите его и нажмите кнопку «Удалить ссылку». Нажмите кнопку «ОК» и сохраните файл проекта.</li>
      <li> Для Visual Studio 2015 или 2017: Выберите «проект "->" Добавить ссылки...» в Visual Studio. Если установлен API служб Xbox, снимите его. Нажмите кнопку «ОК» и сохраните файл проекта.</li>
    </ul>
  <li> Если вы создаете с XDK, выберите «Файл -> Добавить -> существующий проект...» в Visual Studio для добавления следующих двух проектов в решение приложения. Файлы vcxproj будет находиться в папку, которую вы извлекли источника.</li>
Для Visual Studio 2017: <ul>
      <li>\Build\Microsoft.Xbox.Services.141.XDK.Cpp\Microsoft.Xbox.Services.141.XDK.Cpp.vcxproj</li>   <li>\External\cpprestsdk\Release\src\build\vs15.xbox\casablanca141.Xbox.vcxproj</li>
    </ul>
Для Visual Studio 2015: <ul>
      <li>\Build\Microsoft.Xbox.Services.140.XDK.Cpp\Microsoft.Xbox.Services.140.XDK.Cpp.vcxproj</li> <li>\External\cpprestsdk\Release\src\build\vs14.xbox\casablanca140.Xbox.vcxproj</li>
    </ul>
Для Visual Studio 2012: <ul>
      <li>\Build\Microsoft.Xbox.Services.110.XDK.Cpp\Microsoft.Xbox.Services.110.XDK.Cpp.vcxproj</li> <li>\External\cpprestsdk\Release\src\build\vs11.xbox\casablanca110.Xbox.vcxproj</li>
    </ul>
    <li> Добавьте ссылки на "->" Проекты источника как ссылка, выбрав проект... и выберите «Добавить ссылку». В разделе «Решение "->" Проекты» Проверьте записи для обоих проектов выше, затем нажмите кнопку ОК.</li>
    <li> Добавьте файл props в проект, нажав кнопку «Вид -> другие Windows "->" Диспетчер свойств», щелкнув правой кнопкой мыши проект, выбрав «Добавить существующую страницу свойств», а затем наконец выбора файла xsapi.staticlib.props в корневом каталоге sourch SDK.  Если система сборки не поддерживает файлы props, необходимо вручную добавить определения препроцессора и библиотек, как показано на %XboxOneExtensionSDKLatest%\ExtensionSDKs\Xbox.Services.API.Cpp\8.0\DesignTime\CommonConfiguration\Neutral\ Xbox.Services.API.Cpp.props</li>
    <li> Добавить файл services.h к источнику приложения, щелкнув правой кнопкой проект добавить -> существующий элемент и выбрав {SDK root}\Include\xsapi\services.h файле источника</li>
    <li> Убедитесь, что «Выходную папку» проект приложения и проект службы Xbox совпадают. Этот параметр можно найти в проекте Visual Studio, свойства "->" Свойства конфигурации "->" Общие "->" в выходной каталог.</li>
    <li> Перестроить решение Visual Studio</li>
</ol>

## <a name="to-compile-the-xdk-winrt-xsapi-project-for-yourself"></a>Чтобы скомпилировать проект XDK WinRT XSAPI самостоятельно

<ol>
  <li> Получение Microsoft.Xbox.Services исходного кода. Чтобы сделать это, извлеките все файлы из «%XboxOneExtensionSDKLatest%\ExtensionSDKs\Xbox API\8.0\SourceDist\Xbox.Services.zip служб» в папку для записи за пределами «C:\Program Files (x86)», или вы можете клонировать источник, из <a href ="https://github.com/Microsoft/xbox-live-api">https://github.com/Microsoft/xbox-live-api</a></li>
  <li> Если проект ссылается на библиотеку DLL перед сборкой, необходимо удалить ссылку</li>
    <ul>
      <li> Для Visual Studio 2012: Выберите «Проект "->" ссылки...» в Visual Studio. Если API служб Xbox указана как ссылка, выберите его и нажмите кнопку «Удалить ссылку». Нажмите кнопку «ОК» и сохраните файл проекта.</li>
      <li> Для Visual Studio 2015 или 2017: Выберите «проект "->" Добавить ссылки...» в Visual Studio. Если установлен API служб Xbox, снимите его. Нажмите кнопку «ОК» и сохраните файл проекта.</li>
    </ul>
  <li> Если вы создаете с XDK, выберите «Файл -> Добавить -> существующий проект...» в Visual Studio для добавления следующих двух проектов в решение приложения. Файлы vcxproj будет находиться в папку, которую вы извлекли источника.  Для Visual Studio 2015 проекты автоматически обновится до формата Visual Studio 2015.</li>
    <ul>
      <li>\Build\Microsoft.Xbox.Services.110.XDK.WinRT\Microsoft.Xbox.Services.110.XDK.WinRT.vcxproj</li> <li>\External\cpprestsdk\Release\src\build\vs11.xbox\casablanca110.Xbox.vcxproj</li>
    </ul>
  <li> В Visual Studio добавьте ссылки.</li>
    <ul>
      <li> Для Visual Studio 2012: Выберите «Проект "->" ссылки...» и выберите «Add Reference» в Visual Studio. В рамках решения "->" проекты, chceck записи для обоих проектов выше и нажмите кнопку ОК.</li>
      <li> Для Visual Studio 2015 или 2017: Выберите «проект "->" Добавить ссылки...» в Visual Studio. В группе проектов Проверьте записи для обоих проектов выше и нажмите кнопку ОК.</li>
    </ul>
  <li> Убедитесь, что «Выходную папку» проект приложения и проект службы Xbox совпадают. Этот параметр можно найти в проекте Visual Studio, свойства "->" Свойства конфигурации "->" Общие "->" в выходной каталог.</li>
  <li> Перестроить решение Visual Studio</li>
</ol>
