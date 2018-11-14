---
author: TylerMSFT
title: Обработка активации URI
description: Узнайте, как зарегистрировать приложение в качестве стандартного обработчика определенного имени схемы универсального кода ресурса (URI).
ms.assetid: 92D06F3E-C8F3-42E0-A476-7E94FD14B2BE
ms.author: twhitney
ms.date: 07/05/2018
ms.topic: article
keywords: Windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 808242fd7e179c225b3119dad146e7f05d72ffd4
ms.sourcegitcommit: 71e8eae5c077a7740e5606298951bb78fc42b22c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/13/2018
ms.locfileid: "6648991"
---
# <a name="handle-uri-activation"></a>Обработка активации универсального кода ресурса (URI)

**Важные API**

-   [**Windows.ApplicationModel.Activation.ProtocolActivatedEventArgs**](https://msdn.microsoft.com/library/windows/apps/br224742)
-   [**Windows.UI.Xaml.Application.OnActivated**](https://msdn.microsoft.com/library/windows/apps/br242330)

Узнайте, как зарегистрировать приложение в качестве стандартного обработчика определенного имени схемы универсального кода ресурса (URI). Как классические приложения для Windows, так и приложения универсальной платформы Windows (UWP) можно зарегистрировать в качестве стандартного обработчика для имени схемы URI. Если пользователь выбирает ваше приложение в качестве стандартного обработчика для имени схемы URI, ваше приложение будет активироваться при каждом запуске URI этого типа.

Рекомендуется регистрировать приложение в качестве обработчика имени схемы URI только в том случае, если планируется обрабатывать все запуски URI для схемы URI этого типа. Если вы все-таки решите зарегистрировать ваше приложение в качестве обработчика по умолчанию для имени схемы URI, необходимо предоставить пользователю функциональность, ожидаемую при активации вашего приложения для этой схемы URI. Например, приложение, которое регистрируется для имени схемы URI "mailto:", должно открывать окно для создания нового сообщения электронной почты, чтобы пользователь мог написать новое сообщение. Подробнее о сопоставлениях URI: [Руководство и контрольный список по типам файлов и URI](https://msdn.microsoft.com/library/windows/apps/hh700321).

Далее показано, как зарегистрировать собственное имя схемы URI (`alsdk://`) и активировать приложение, когда пользователь запустит URI `alsdk://`.

> [!NOTE]
> В приложениях UWP определенные URI и расширения файлов зарезервированы для использования встроенных приложениях и операционной системы. Попытки регистрации приложения с зарезервированным URI или расширением файла будут проигнорированы. Алфавитный список схем URI, которые нельзя зарегистрировать для приложений UWP, поскольку они уже зарегистрированы или запрещены, см. в статье [Зарезервированные имена схем URI и типы файлов](reserved-uri-scheme-names.md).

## <a name="step-1-specify-the-extension-point-in-the-package-manifest"></a>Шаг 1. Указание точки расширения в манифесте пакета

Приложение принимает события активации только для имен схемы URI, указанных в манифесте пакета. Указать, что ваше приложение обрабатывает имя схемы URI `alsdk`, можно так:

1. В **обозревателе решений** дважды щелкните файл package.appxmanifest, чтобы открыть конструктор манифеста. Перейдите на вкладку **Объявления** и в раскрывающемся списке **Доступные объявления** выберите **Протокол** и нажмите кнопку **Добавить**.

    Далее приведено краткое описание каждого из полей, которое можно заполнить в конструкторе манифеста для протокола (подробнее см. [**AppX Package Manifest**](https://msdn.microsoft.com/library/windows/apps/dn934791)):

| Поле | Описание |
|-------|-------------|
| **Логотип** | Укажите логотип, по которому можно будет определить имя схемы URI в разделе [Выбор программ по умолчанию](https://msdn.microsoft.com/library/windows/desktop/cc144154) на **панели управления**. Если логотип не указан, используется мелкий логотип приложения. |
| **Отображаемое имя** | Задайте отображаемое имя, которое будет определять имя схемы URI в разделе [Выбор программ по умолчанию](https://msdn.microsoft.com/library/windows/desktop/cc144154) на **панели управления**. |
| **Имя** | Выберите имя для схемы URI. |
|  | **Примечание.** Все буквы имени должны быть строчными. |
|  | **Зарезервированные и запрещенные типы файлов**. Алфавитный список схем URI, которые нельзя зарегистрировать для приложений UWP, поскольку они уже зарегистрированы или запрещены, см. в статье [Зарезервированные имена схем URI и типы файлов](reserved-uri-scheme-names.md). |
| **Исполняемый файл** | Указывает исполняемый файл по умолчанию для протокола. Если он не указан, используется исполняемый файл приложения. Если указан, строка должна содержать от 1 до 256 символов, заканчиваться на ".exe" и не содержать символы &gt;, &lt;, :, ", &#124;, ?, или \*. Если указан, **точка входа** также используется. Если **точка входа** не указана, используется точка входа, заданная для приложения. |
| **Точка входа** | Определяет задачу, которая обрабатывает расширение протокола. Как правило, соответствует полному имени пространства имен типа среды выполнения Windows. Если не указана, используется точка входа для приложения. |
| **Начальная страница** | Веб-страница, обрабатывающая точку расширения. |
| **Группа ресурсов** | Тег, который можно применять для группировки активаций расширения в целях управления ресурсами. |
| **Требуемое представление** (только Windows) | В поле **Desired View** укажите объем пространства, необходимого окну приложения, которое запускается для данной схемы URI. Возможные значения **Desired View**: **Default**, **UseLess**, **UseHalf**, **UseMore** и **UseMinimum**.<br/>**Примечание.** При определении окончательного размера окна целевого приложения Windows учитывает несколько различных факторов, таких как параметры исходного приложения, количество приложений на экране, ориентация экрана и т. п. Настройка **Desired View** не гарантирует, что окно целевого приложения будет работать точно запрошенным образом.<br/>**Семейство мобильных устройств: Desired View** не поддерживается на данном семействе мобильных устройств. |

2. Введите `images\Icon.png` в поле **Логотип**.
3. Введите `SDK Sample URI Scheme` в поле **Отображаемое имя**.
4. Введите `alsdk` в поле **Имя**.
5. Нажмите клавиши CTRL+S, чтобы сохранить изменения в файле package.appxmanifest.

    В результате в манифест пакета будет добавлен элемент [**Extension**](https://msdn.microsoft.com/library/windows/apps/br211400) данного типа. Категория **windows.protocol** показывает, что приложение обрабатывает имя схемы URI `alsdk`.

```xml
    <Applications>
        <Application Id= ... >
            <Extensions>
                <uap:Extension Category="windows.protocol">
                  <uap:Protocol Name="alsdk">
                    <uap:Logo>images\icon.png</uap:Logo>
                    <uap:DisplayName>SDK Sample URI Scheme</uap:DisplayName>
                  </uap:Protocol>
                </uap:Extension>
          </Extensions>
          ...
        </Application>
   <Applications>
```

## <a name="step-2-add-the-proper-icons"></a>Шаг 2. Добавление подходящих значков

Значки приложений, которые будут использоваться по умолчанию для того или иного имени схемы URI, будут отображаться в различных частях системы, например в разделе программ по умолчанию на панели управления. Добавьте для этого в проект значок размером 44 x 44. Вместо того чтобы делать фон значка прозрачным, используйте для логотипа на плитке приложения цвет, который совпадает с цветом фона самого приложения. Растяните логотип до краев, не заполняя плитку. Проверьте, как выглядят значки на белом фоне. Дополнительные сведения о значках см. в разделе [Руководство по работе с ресурсами плиток и значков](https://docs.microsoft.com/windows/uwp/shell/tiles-and-notifications/app-assets).

## <a name="step-3-handle-the-activated-event"></a>Шаг 3. Обработка события активации

Обработчик событий [**OnActivated**](https://msdn.microsoft.com/library/windows/apps/br242330) принимает все события активации. Свойство **Kind** обозначает тип события активации. В этом примере выполняется обработка событий активации [**Protocol**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.applicationmodel.activation.activationkind.aspx#Protocol).

```csharp
public partial class App
{
   protected override void OnActivated(IActivatedEventArgs args)
  {
      if (args.Kind == ActivationKind.Protocol)
      {
         ProtocolActivatedEventArgs eventArgs = args as ProtocolActivatedEventArgs;
         // TODO: Handle URI activation
         // The received URI is eventArgs.Uri.AbsoluteUri
      }
   }
}
```

```vb
Protected Overrides Sub OnActivated(ByVal args As Windows.ApplicationModel.Activation.IActivatedEventArgs)
   If args.Kind = ActivationKind.Protocol Then
      ProtocolActivatedEventArgs eventArgs = args As ProtocolActivatedEventArgs
      
      ' TODO: Handle URI activation
      ' The received URI is eventArgs.Uri.AbsoluteUri
 End If
End Sub
```

```cppwinrt
void App::OnActivated(Windows::ApplicationModel::Activation::IActivatedEventArgs const& args)
{
    if (args.Kind() == Windows::ApplicationModel::Activation::ActivationKind::Protocol)
    {
        auto protocolActivatedEventArgs{ args.as<Windows::ApplicationModel::Activation::ProtocolActivatedEventArgs>() };
        // TODO: Handle URI activation  
        auto receivedURI{ protocolActivatedEventArgs.Uri().RawUri() };
    }
}
```

```cpp
void App::OnActivated(Windows::ApplicationModel::Activation::IActivatedEventArgs^ args)
{
   if (args->Kind == Windows::ApplicationModel::Activation::ActivationKind::Protocol)
   {
      Windows::ApplicationModel::Activation::ProtocolActivatedEventArgs^ eventArgs =
          dynamic_cast<Windows::ApplicationModel::Activation::ProtocolActivatedEventArgs^>(args);
      
      // TODO: Handle URI activation  
      // The received URI is eventArgs->Uri->RawUri
   }
}
```

> [!NOTE]
> При запуске через контракт протокола, убедитесь, что этот кнопку "Назад" должна возвращать пользователя обратно на экран, который запустил это приложение, а не к предыдущему содержимому приложения.

Следующий код запускает приложение программным способом, используя его URI:

```csharp
   // Launch the URI
   var uri = new Uri("alsdk:");
   var success = await Windows.System.Launcher.LaunchUriAsync(uri)
```

Более подробные сведения о запуске приложения с помощью URI см. в разделе [Запуск приложения по умолчанию для URI](launch-default-app.md).

Рекомендуется, чтобы приложения создавали новый элемент XAML [**Frame**](https://msdn.microsoft.com/library/windows/apps/br242682) для каждого события активации, открывающего новую страницу. Тогда стек переходов назад для нового элемента XAML **Frame** не будет включать предыдущее содержимое, которое могло отображаться в текущем окне во время приостановки приложения. Приложения, использующие один элемент XAML **Frame** для контрактов файла и запуска, должны очищать страницы в журнале навигации **Frame** перед переходом на новую страницу.

При запуске через активацию протокола приложения должны предусматривать пользовательский интерфейс, при помощи которого пользователь сможет вернуться на начальную страницу приложения.

## <a name="remarks"></a>Примечания

Ваше имя схемы URI могут использовать любые приложения и веб-сайты, в том числе вредоносные. Таким образом, получаемые через URI данные могут поступать из ненадежного источника. Рекомендуется никогда не выполнять неотменяемое действие на основе параметров, принятых через URI. Например, параметры URI могут использоваться для запуска приложения на странице учетной записи пользователя, однако их ни в коем случае не следует использовать для прямого внесения изменений в учетную запись пользователя.

> [!NOTE]
> Если вы создаете новое имя схемы URI для вашего приложения, обязательно следуйте рекомендациям в [RFC 4395](http://go.microsoft.com/fwlink/p/?LinkID=266550). Это гарантирует, что созданное вами имя будет соответствовать стандартам для схем URI.

> [!NOTE]
> При запуске через контракт протокола, убедитесь, что этот кнопку "Назад" должна возвращать пользователя обратно на экран, который запустил это приложение, а не к предыдущему содержимому приложения.

Рекомендуется, чтобы приложения создавали новый элемент XAML [**Frame**](https://msdn.microsoft.com/library/windows/apps/br242682) для каждого события активации, открывающего новый целевой объект URI. Тогда стек переходов назад для нового элемента XAML **Frame** не будет включать предыдущее содержимое, которое могло отображаться в текущем окне во время приостановки приложения.

Если необходимо, чтобы в приложении использовался один элемент XAML [**Frame**](https://msdn.microsoft.com/library/windows/apps/br242682) для контрактов запуска и протокола, очищайте страницы в журнале навигации **Frame** перед переходом на новую страницу. При запуске через контракт протокола в приложениях должен быть предусмотрен пользовательский интерфейс, при помощи которого можно вернуться на начальную страницу приложения.

## <a name="related-topics"></a>Статьи по теме

### <a name="complete-sample-app"></a>Полный пример приложения

- [Пример запуска с сопоставлением](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/AssociationLaunching)

### <a name="concepts"></a>Понятия

- [Программы по умолчанию](https://msdn.microsoft.com/library/windows/desktop/cc144154)
- [Модель сопоставления типов файлов и URI](https://msdn.microsoft.com/library/windows/desktop/hh848047)

### <a name="tasks"></a>Задачи

- [Запуск приложения по умолчанию для URI](launch-default-app.md)
- [Обработка активации файла](handle-file-activation.md)

### <a name="guidelines"></a>Руководства

- [Руководство по типам файлов и URI](https://msdn.microsoft.com/library/windows/apps/hh700321)

### <a name="reference"></a>Ссылки

- [AppX Package Manifest](https://msdn.microsoft.com/library/windows/apps/dn934791)
- [Windows.ApplicationModel.Activation.ProtocolActivatedEventArgs](https://msdn.microsoft.com/library/windows/apps/br224742)
- [Windows.UI.Xaml.Application.OnActivated](https://msdn.microsoft.com/library/windows/apps/br242330)
