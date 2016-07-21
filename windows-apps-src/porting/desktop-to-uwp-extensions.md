---
author: awkoren
Description: "Помимо обычных API, доступных всем приложениям UWP, имеется ряд расширений и API, доступных только преобразованным классическим приложениям. В этой статье описываются эти расширения и способы их использования."
Search.Product: eADQiWindows 10XVcnh
title: "Расширения преобразованных классических приложений"
translationtype: Human Translation
ms.sourcegitcommit: aa64c39c452beb2356186789a0d8bc44f79d82d2
ms.openlocfilehash: 0ad7e8d0fe63ffbfa8668be8955859258887d6f0

---

# Расширения преобразованных классических приложений

Вы можете расширить возможности своего преобразованного классического приложения с помощью большого числа API-интерфейсов универсальной платформы Windows (UWP). Однако помимо обычных API, доступных всем приложениям UWP, имеется ряд расширений и API, доступных только преобразованным классическим приложениям. Эти компоненты преимущественно предназначены для использования в таких сценариях, как запуск процесса при входе пользователя в систему и интеграция с проводником. Они позволяют сделать переход от первоначального классического приложения к пакету преобразованного приложения более плавным.

В этой статье описываются эти расширения и способы их использования. Для большинства из них требуется вручную изменить файл манифеста вашего преобразованного приложения, который содержит объявления расширений, используемых приложением. Чтобы изменить манифест, щелкните правой кнопкой мыши файл **Package.appxmanifest** в Visual Studio и выберите команду *Перейти к коду*. 

## Задачи автозагрузки

Задачи автозагрузки позволяют вашему приложению автоматически запускать исполняемый файл при входе пользователя. 

Для объявления задачи автозагрузки добавьте следующий код в манифест вашего приложения. 

```XML
<desktop:Extension Category="windows.startupTask" Executable="bin\MyStartupTask.exe" EntryPoint="Windows.FullTrustApplication">
    <desktop:StartupTask TaskId="MyStartupTask" Enabled="true" DisplayName="My App Service" />
</desktop:Extension>
```
- 
            Параметр *Extension Category* всегда должен иметь значение windows.startupTask.
- 
            *Extension Executable* — это относительный путь к запускаемому EXE-файлу.
- 
            Параметр *Extension EntryPoint* всегда должен иметь значение Windows.FullTrustApplication.
- 
            *StartupTask TaskId* — это уникальный идентификатор вашей задачи. Используя этот идентификатор, ваше приложение может вызывать функции API в классе **Windows.ApplicationModel.StartupTask**, чтобы программно включать или отключать задачу автозагрузки.
- 
            Параметр *StartupTask Enabled* указывает, запускается ли задача в первый раз включенной или отключенной. Включенная задача будет запущена при следующем входе пользователя (если только пользователь не отключил ее). 
- 
            *StartupTask DisplayName* — это имя задачи, которое отображается в диспетчере задач. Эта строка может быть локализована помощью ```ms-resource```. 

Приложения могут объявить несколько задач автозагрузки. Каждая из них активируется и запускается независимо от других. Все задачи автозагрузки отображаются в диспетчере задач во вкладке **Автозагрузка** и имеют значки вашего приложения и имена, указанные в манифесте вашего приложения. Диспетчер задач автоматически анализирует влияние ваших задач на процесс запуска. С помощью диспетчера задач пользователи могут вручную отключить задачи автозагрузки вашего приложения. Если пользователь отключит задачу, включить ее снова программными средствами нельзя.

## Псевдоним выполнения приложения

Псевдоним выполнения приложения позволяет указать для приложения имя с ключевым словом. Пользователи или другие процессы могут использовать это ключевое слово для быстрого запуска вашего приложения (например, с помощью команды «Выполнить» или в командной строке) без указания полного пути к приложению, как если бы оно было указано в переменной PATH. Например, если вы объявите псевдоним Foo, пользователь в окне программы cmd.exe может ввести строку Foo Bar.txt и ваше приложение будет активировано с передачей ему пути к файлу Bar.txt в качестве одного из аргументов события активации.

Чтобы задать псевдоним выполнения приложения, добавьте в манифест вашего приложения следующий код. 

```XML 
<uap3:Extension Category="windows.appExecutionAlias" Executable="exes\launcher.exe" EntryPoint="Windows.FullTrustApplication">
    <uap3:AppExecutionAlias>
        <desktop:ExecutionAlias Alias="Foo.exe">
    </uap3:AppExecutionAlias>
</uap3:Extension>
```

- 
            Параметр *Extension Category* всегда должен иметь значение windows.appExecutionAlias.
- 
            *Extension Executable* — это относительный путь к запускаемому исполняемому файлу при активации псевдонима.
- 
            Параметр *Extension EntryPoint* всегда должен иметь значение Windows.FullTrustApplication.
- 
            *ExecutionAlias Alias* — краткое имя приложения. Оно всегда должно завершаться расширением .exe. 

Для каждого приложения в пакете можно указать только один псевдоним выполнения приложения. Если несколько приложений зарегистрированы с одним и тем же псевдонимом, система будет активировать псевдоним, который был зарегистрирован последним, поэтому следует выбирать уникальный псевдоним, который не будет переопределен другими приложениями.

## Сопоставления протоколов 

Сопоставления протоколов позволяют использовать сценарии взаимодействия между преобразованным приложением и другими программами и системными компонентами. Когда ваше преобразованное приложение запускается с использованием протокола, вы можете определить специальные параметры, которые передаются аргументам его события активации, что позволит приложению реагировать соответствующим образом. Обратите внимание, что параметры поддерживаются только преобразованными приложениями с полным доверием. Приложения UWP не могут использовать параметры.  

Чтобы объявить сопоставление протокола, добавьте следующий код в манифест вашего приложения.

```XML
<uap3:Extension Category="windows.protocol">
    <uap3:Protocol Name="myapp-cmd" Parameters="/p &quot;%1&quot;" />
</uap3:Extension>
```

- 
            Параметр *Extension Category* всегда должен иметь значение windows.protocol. 
- 
            *Protocol Name* — это имя протокола. 
- 
            *Protocol Parameters* — это список параметров и значений, передаваемых в приложение в качестве аргументов события при его активации. Обратите внимание: если переменная содержит путь к файлу, этот путь необходимо заключить в кавычки, чтобы он корректно передавался при наличии в нем пробелов.

## Интеграция с файлами и проводником

Преобразованные приложения поддерживают несколько вариантов регистрации для обработки определенных типов файлов и интеграции с проводником. Это позволяет пользователям с легкостью обращаться к приложению в ходе обычного рабочего процесса.

Для начала добавьте следующий код в манифест вашего приложения. 

```XML
<uap3:Extension Category="windows.fileTypeAssociation">
    <uap3:FileTypeAssociation Name="MyApp">
        ... additional elements here ...
    </uap3:FileTypeAssociation>
</uap3:Extension>
```

- 
            Параметр *Extension Category* всегда должен иметь значение windows.fileTypeAssociation. 
- 
            *FileTypeAssociation Name* — это уникальный идентификатор. Этот идентификатор предназначен для внутреннего использования и применяется для генерации хэшированного программного идентификатора, ассоциированного с вашим сопоставлением типов файлов. Вы можете использовать этот идентификатор для контроля изменений в будущих версиях своего приложения. Например, если вы захотите изменить значок для расширения файлов, вы можете использовать его с новым параметром FileTypeAssociation, имеющим другое имя.  

Далее добавьте к этой записи дополнительные дочерние элементы в соответствии с тем, какие функции вам нужны. Доступные варианты описаны ниже.

### Поддерживаемые типы файлов

Ваше приложение может указать, что оно поддерживает открытие определенных типов файлов. Если пользователь щелкнет файл правой кнопкой мыши и выберет команду «Открыть с помощью», ваше приложение будет отображаться в появившемся списке вариантов.

Пример.

```XML
<uap:SupportedFileTypes>
    <uap:FileType>.txt</uap:FileType>
    <uap:FileType>.avi</uap:FileType>
</uap:SupportedFileTypes>
```

- 
            *FileType* — расширение, поддерживаемое приложением.

### Команды контекстного меню 

Обычно пользователи открывают файлы двойным щелчком. Однако когда пользователь щелкает файл правой кнопкой мыши, в контекстном меню отображается несколько вариантов действий (называемых командами), которые сообщают пользователю дополнительную информацию о том, как он может взаимодействовать с файлом (например, «Открыть», «Изменить», выполнить «Предварительный просмотр» или «Печать»). 

При указании поддерживаемого типа файлов автоматически добавляется команда «Открыть». Однако приложения могут добавлять в контекстное меню проводника собственные дополнительные команды. Они позволяют приложению запускаться определенным способом в зависимости от варианта, выбранного пользователем при открытии файла.

Пример. 

```XML
<uap2:SupportedVerbs>
    <uap3:Verb Id="Edit" Parameters="/e &quot;%1&quot">Edit</uap:Verb>
    <uap3:Verb Id="Print" Extended="true" Parameters="/p &quot;%1&quot">Print</uap:Verb>
</uap2:SupportedVerbs>
```

- 
            *Verb Id* — это уникальный идентификатор команды. Если у вас приложение UWP, этот идентификатор передается в ваше приложение в составе аргументов события активации, чтобы приложение могло обработать выбор пользователя соответствующим образом. Если у вас преобразованное приложение с полным доверием, оно вместо этого будет получать параметры (см. следующий пункт). 
- 
            *Verb Parameters* — связанный с командой список параметров и значений аргументов. Если у вас преобразованное приложение с полным доверием, эти параметры передаются приложению как аргументы события при активации приложения, чтобы вы могли настроить поведение приложения для различных команд активации. Если переменная содержит путь к файлу, этот путь необходимо заключить в кавычки, чтобы он корректно передавался при наличии в нем пробелов. Обратите внимание: если у вас приложение UWP, вы не можете передавать ему параметры — вместо них приложение получает идентификатор (см. предыдущий пункт). 
- 
            *Verb Extended* указывает, что команда должна отображаться, только если пользователь удерживает клавишу **Shift** перед нажатием правой кнопки мыши, отображающим контекстное меню. Этот атрибут необязателен и при отсутствии по умолчанию имеет значение *False* (всегда отображать команду). Это поведение задается отдельно для каждой команды (кроме команды «Открыть», для которой всегда используется значение *False*). 
- 
            *Verb* — имя, отображаемое в контекстном меню проводника. Эта строка может быть локализована помощью ```ms-resource```.

### Модель выбора нескольких элементов

Модель выбора нескольких элементов позволяет указать, как приложение будет обрабатывать одновременное открытие пользователем нескольких файлов (например, если выбрать в проводнике 10 файлов и нажать команду «Открыть»).

Для преобразованных классических приложений, как и для обычных классических приложений, имеется три варианта. 
- 
            *Player*: приложение активируется один раз и все выбранные файлы передаются ему как параметры аргумента.
- 
            *Single*: приложение активируется только для первого выбранного файла. Другие файлы игнорируются. 
- 
            *Document*: для каждого выбранного файла активируется новый отдельный экземпляр приложения.

Можно установить разные настройки для разных типов файлов и действий. Например, вы можете решить открывать *документы* в режиме *Document*, а *изображения* — в режиме *Player*.

Чтобы задать поведение приложения, добавьте атрибут *MultiSelectModel* к элементам манифеста, связанным с типами файлов и запуском файлов. 

Установка модели для поддерживаемого типа файлов: 

```XML
<uap:FileType MultiSelectModel="Document">.txt</uap:FileType>
```

Установка модели для команд:

```XML
<uap3:Verb Id="Edit" MultiSelectModel="Player">Edit</uap:Verb>
<uap3:Verb Id="Preview" MultiSelectModel="Document">Preview</uap:Verb>
```

Если в приложении не указан вариант модели для выбора нескольких элементов, по умолчанию используется модель *Player*, если пользователь открывает до 15 файлов. В противном случае, если у вас преобразованное приложение, по умолчанию используется модель *Document*. Приложения UWP всегда запускаются с использованием модели *Player*. 

### Полный пример

Ниже приведен полный пример, который включает большую часть рассмотренных выше моментов, относящихся к работе с файлами и проводником. 

```XML
<uap3:Extension Category="windows.fileTypeAssociation">
    <uap3:FileTypeAssociation Name="MyApp">
        <uap:SupportedFileTypes>
            <uap:FileType MultiSelectModel="Document">.txt</uap:FileType>
            <uap:FileType>.foo</uap:FileType>
    </uap:SupportedFileTypes>
    <uap2:SupportedVerbs>
            <uap3:Verb Id="Edit" Parameters="/e &quot;%1&quot">Edit</uap:Verb>
            <uap3:Verb Id="Print" Parameters="/p &quot;%1&quot">Print</uap:Verb>
    </uap2:SupportedVerbs>
    <uap:Logo>Assets\MyExtensionLogo.png</uap:Logo>
    </uap3:FileTypeAssociation>
</uap3:Extension>
```

## См. также

- [Манифест пакета приложения](https://msdn.microsoft.com/library/windows/apps/br211474.aspx)


<!--HONumber=Jul16_HO1-->

