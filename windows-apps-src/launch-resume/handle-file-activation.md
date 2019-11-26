---
title: Обработка активации файла
description: Приложение можно зарегистрировать в качестве стандартного обработчика определенного типа файлов.
ms.assetid: A0F914C5-62BC-4FF7-9236-E34C5277C363
ms.date: 07/05/2018
ms.topic: article
keywords: Windows 10, uwp
ms.localizationpriority: medium
dev_langs:
- csharp
- vb
- cppwinrt
- cpp
ms.openlocfilehash: 079746d3c1619fe940ba243410f0247b7b850ed9
ms.sourcegitcommit: b52ddecccb9e68dbb71695af3078005a2eb78af1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74259459"
---
# <a name="handle-file-activation"></a>Обработка активации файла

**Важные API**

-   [**Windows. ApplicationModel. Activation. Филеактиватедевентаргс**](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Activation.FileActivatedEventArgs)
-   [**Windows. UI. XAML. Application. Онфилеактиватед**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.application.onfileactivated)

Приложение может зарегистрироваться, чтобы стать обработчиком по умолчанию для определенного типа файлов. Это касается как классических приложений для Windows, так и приложений универсальной платформы Windows (UWP). Если пользователь выбирает ваше приложение в качестве стандартного обработчика для какого-либо типа файлов, оно будет активироваться при запуске файлов этого типа.

Мы рекомендуем регистрировать приложение в качестве обработчика по умолчанию для файлов определенного типа, только если планируется обрабатывать все запуски файлов этого типа. Если файлы данного типа будут использоваться только внутри приложения, нет необходимости регистрировать его в качестве обработчика по умолчанию. Если вы все-таки решите зарегистрировать тип файла, необходимо предоставить пользователю функциональность, ожидаемую при активации вашего приложения для файлов этого типа. Например, приложение для просмотра изображений может быть зарегистрировано для отображения JPG-файлов. Подробнее о сопоставлениях файлов: [Руководство по типам файлов и URI](https://docs.microsoft.com/windows/uwp/files/index).

Далее показано, как зарегистрировать собственный тип файлов (.alsdk) и как активировать приложение при запуске ALSDK-файла пользователем.

> **Примечание** .  в приложениях UWP определенные коды URI и расширения файлов зарезервированы для использования встроенными приложениями и операционной системой. Попытки регистрации приложения с зарезервированным URI или расширением файла будут проигнорированы. Дополнительные сведения об этом см. в статье [Зарезервированные файлы и имена схем URI](reserved-uri-scheme-names.md).

## <a name="step-1-specify-the-extension-point-in-the-package-manifest"></a>Шаг 1. Указание точки расширения в манифесте пакета

Приложение принимает события активации только для расширений файлов, указанных в манифесте пакета. Указать, что ваше приложение обрабатывает файлы с расширением `.alsdk`, можно следующим образом:

1.  В **обозревателе решений** дважды щелкните файл package.appxmanifest, чтобы открыть конструктор манифеста. Перейдите на вкладку **Объявления** и в раскрывающемся списке **Доступные объявления** выберите **Сопоставление типов файлов** и нажмите кнопку **Добавить**. Дополнительные сведения об идентификаторах, используемых в сопоставлениях файлов, см. в разделе о [программных идентификаторах](https://docs.microsoft.com/windows/desktop/shell/fa-progids).

    Далее приведено краткое описание каждого из полей, которые можно заполнить в конструкторе манифеста:

| Поле | Описание |
|------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Отображаемое имя** | Укажите отображаемое имя для группы типов файлов. Это отображаемое имя используется для определения типа файла в разделе [Выбор программ по умолчанию](https://docs.microsoft.com/windows/desktop/shell/default-programs) на **панели управления**. |
| **Совместимость** | Укажите логотип, по которому можно будет определить тип файла на рабочем столе и в разделе [Выбор программ по умолчанию](https://docs.microsoft.com/windows/desktop/shell/default-programs) на **панели управления**. Если логотип не указан, используется мелкий логотип приложения. |
| **Информационный Совет** | Укажите [подсказку](https://docs.microsoft.com/windows/desktop/shell/fa-progids) для группы типов файлов. Эта подсказка отображается, когда пользователь наводит указатель мыши на значок файла этого типа. |
| **Название** | Выберите имя для группы типов файлов, которые имеют общие отображаемое имя, логотип, подсказку и флаги редактирования. Выберите для группы такое имя, которое будет сохраняться неизменным при обновлении приложения. **Примечание.** Все буквы имени должны быть строчными. |
| **Тип содержимого** | Укажите тип содержимого MIME, например **image/jpeg**, для определенного типа файла. **Важное примечание о допустимых типах содержимого!** Далее приводится список типов содержимого MIME в алфавитном порядке, которые запрещено вводить в манифест пакета, поскольку они либо зарезервированы, либо запрещены: **application/force-download**, **application/octet-stream**, **application/unknown**, **application/x-msdownload**. |
| **Тип файла** | Укажите тип файла для регистрации с точкой в начале имени, например ".jpeg". **Зарезервированные и запрещенные типы файлов.** Алфавитный список типов файлов для встроенных приложений, которые нельзя зарегистрировать для приложений UWP, поскольку они уже зарегистрированы или запрещены, см. в статье [Зарезервированные имена схем URI и типы файлов](reserved-uri-scheme-names.md). |

2.  Введите `alsdk` в поле **Имя**.
3.  Введите `.alsdk` в поле **Тип файла**.
4.  В качестве эмблемы введите "Images\\Icon. png".
5.  Нажмите клавиши CTRL+S, чтобы сохранить изменения в файле package.appxmanifest.

Шаги выше добавляют в манифест пакета элемент [**Extension**](https://docs.microsoft.com/uwp/schemas/appxpackage/appxmanifestschema/element-1-extension) указанного типа. Категория **windows.fileTypeAssociation** указывает, что приложение обрабатывает файлы с расширением `.alsdk`.

```xml
      <Extensions>
        <uap:Extension Category="windows.fileTypeAssociation">
          <uap:FileTypeAssociation Name="alsdk">
            <uap:Logo>images\icon.png</uap:Logo>
            <uap:SupportedFileTypes>
              <uap:FileType>.alsdk</uap:FileType>
            </uap:SupportedFileTypes>
          </uap:FileTypeAssociation>
        </uap:Extension>
      </Extensions>
```

## <a name="step-2-add-the-proper-icons"></a>Шаг 2. Добавление подходящих значков

Значки приложений, используемых по умолчанию для того или иного типа файла, отображаются в различных частях системы. Например, эти значки можно увидеть:

-   в представлении просмотра элементов, в контекстных меню и на ленте проводника;
-   на панели управления в разделе программ по умолчанию;
-   в средстве выбора файлов;
-   в результатах поиска на начальном экране.

Добавьте в проект значок размером 44 x 44, чтобы ваш логотип отображался в этих расположениях. Вместо того чтобы делать фон значка прозрачным, используйте для логотипа на плитке приложения цвет, который совпадает с цветом фона самого приложения. Растяните логотип до краев, не заполняя плитку. Проверьте, как выглядят значки на белом фоне. Дополнительные сведения о значках см. в разделе [Руководство по работе с ресурсами плиток и значков](https://docs.microsoft.com/windows/uwp/design/shell/tiles-and-notifications/app-assets).

## <a name="step-3-handle-the-activated-event"></a>Шаг 3. Обработка события активации

Обработчик событий [**OnFileActivated**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.application.onfileactivated) принимает все события активации файлов.

```csharp
protected override void OnFileActivated(FileActivatedEventArgs args)
{
       // TODO: Handle file activation
       // The number of files received is args.Files.Size
       // The name of the first file is args.Files[0].Name
}
```

```vb
Protected Overrides Sub OnFileActivated(ByVal args As Windows.ApplicationModel.Activation.FileActivatedEventArgs)
      ' TODO: Handle file activation
      ' The number of files received is args.Files.Size
      ' The name of the first file is args.Files(0).Name
End Sub
```

```cppwinrt
void App::OnFileActivated(Windows::ApplicationModel::Activation::FileActivatedEventArgs const& args)
{
    // TODO: Handle file activation.
    auto numberOfFilesReceived{ args.Files().Size() };
    auto nameOfTheFirstFile{ args.Files().GetAt(0).Name() };
}
```

```cpp
void App::OnFileActivated(Windows::ApplicationModel::Activation::FileActivatedEventArgs^ args)
{
    // TODO: Handle file activation
    // The number of files received is args->Files->Size
    // The name of the first file is args->Files->GetAt(0)->Name
}
```

> [!NOTE]
> При запуске через контракт файла убедитесь, что кнопка "назад" переводит пользователя на экран, который запустил приложение, а не на предыдущее содержимое приложения.

Рекомендуется создать новый **фрейм** XAML для каждого события активации, которое открывает новую страницу. В результате стека навигации для нового кадра XAML не будет содержать никакого предыдущего содержимого, которое может быть у приложения в текущем окне при приостановке. Если вы решили использовать один **кадр** XAML для запуска и для контрактов файлов, то перед переходом на новую страницу следует очистить страницы в журнале навигации **фрейма**.

При запуске приложения с помощью активации файлов следует рассмотреть возможность включения пользовательского интерфейса, который позволяет пользователю вернуться на верхнюю страницу приложения.

## <a name="remarks"></a>Примечания

Принимаемые вами файлы могут поступать из ненадежного источника. Мы рекомендуем проверять содержимое файла перед тем, как выполнять с ним какие-либо действия.

## <a name="related-topics"></a>См. также

### <a name="complete-example"></a>Полный пример

* [Пример запуска ассоциации](https://code.msdn.microsoft.com/windowsapps/Association-Launching-535d2cec)

### <a name="concepts"></a>Понятия

* [Программы по умолчанию](https://docs.microsoft.com/windows/desktop/shell/default-programs)
* [Модель сопоставления типов файлов и протоколов](https://docs.microsoft.com/windows/desktop/w8cookbook/file-type-and-protocol-associations-model)

### <a name="tasks"></a>Задачи

* [Запуск приложения по умолчанию для файла](launch-the-default-app-for-a-file.md)
* [Активация обработки URI](handle-uri-activation.md)

### <a name="guidelines"></a>Руководство

* [Рекомендации по типам файлов и URI](https://docs.microsoft.com/windows/uwp/files/index)

### <a name="reference"></a>Справочные материалы
* [Windows. ApplicationModel. Activation. Филеактиватедевентаргс](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Activation.FileActivatedEventArgs)
* [Windows. UI. XAML. Application. Онфилеактиватед](https://docs.microsoft.com/uwp/api/windows.ui.xaml.application.onfileactivated)
