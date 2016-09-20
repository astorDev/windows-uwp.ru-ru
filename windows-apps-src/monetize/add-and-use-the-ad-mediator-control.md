---
author: mcleanbyron
ms.assetid: 3C03FDD8-FA61-4E7B-BDCA-3C29DFEA20E4
description: "После установки пакета SDK Microsoft Store Engagement and Monetization следуйте инструкциям в этом разделе, чтобы применить элемент управления Ad Mediator в своем приложении."
title: "Добавление и использование элемента управления Ad Mediator"
ms.sourcegitcommit: 8c3f1997427a7c3d4f4b4b7acc876a2a091e4553
ms.openlocfilehash: a0d73b50207d251c079714265845a816f4ac23da

---

# Добавление и использование элемента управления Ad Mediator


\[ Обновлено для приложений UWP в Windows 10. Статьи для Windows8.x можно найти в [архиве](http://go.microsoft.com/fwlink/p/?linkid=619132) \]


После установки [пакета SDK Microsoft Store Engagement and Monetization](http://aka.ms/store-em-sdk) следуйте инструкциям в этом разделе, чтобы применить элемент управления Ad Mediator в своем приложении. Список рекламных сетей и типов проектов, которые в настоящее время поддерживаются рекламным посредником, см. в разделе [Выбор рекламных сетей и управление ими](select-and-manage-your-ad-networks.md).

## Добавление элемента управления рекламным посредником в проект


Чтобы добавить экземпляр элемента управления рекламного посредника в проект, выполните следующее.

1.  Откройте проект в Visual Studio.
2.  Если необходимо добавить рекламный посредник в приложение, которое уже приносит доход с помощью любой из [рекламных сетей](select-and-manage-your-ad-networks.md), поддерживаемых рекламным посредником, удалите существующую рекламу и все ссылки на нее, прежде чем продолжить.
3.  В **Обозревателе решений** найдите страницу приложения, на которой необходимо отображать рекламу, и дважды щелкните страницу, чтобы открыть ее в конструкторе.
4.  Перетащите с **панели инструментов** в конструктор новый элемент **AdMediatorControl** (убедитесь, что вы перетащили элемент управления в конструктор, а не в код XAML). Расположите элемент управления в месте, в котором необходимо отображать рекламу. Можно добавить несколько элементов управления, если необходимо отображать рекламу в нескольких областях приложения.

    Элемент **AdMediatorControl** находится в следующих расположениях **Панели элементов**.

    -   В проекте универсальной платформы Windows (UWP) используйте **AdMediatorControl** в разделе **AdMediator Universal**.
    -   В проекте Windows8.1 или Windows Phone 8.1 с применением C# или Visual Basic с XAML используйте **AdMediatorControl** в разделе **AdMediator**.
    -   В проекте Windows Phone Silverlight используйте **AdMediatorControl** в разделе **Все элементы управления Windows Phone**.

    > 
            **Примечание**  При первом перетаскивании элемента управления **AdMediatorControl** в конструктор в проекте UWP, Windows 8.1 или Windows Phone 8.1 с применением C# или Visual Basic с XAML система Visual Studio добавляет в проект необходимую ссылку на сборку Ad Mediator, при этом сам элемент управления еще не добавляется в конструктор. Для добавления элемента управления щелкните пункт «ОК» в сообщении Visual Studio, подождите несколько секунд, пока обновится конструктор, и еще раз перетащите элемент управления в конструктор. Если все же не удается добавить элемент управления к конструктор, убедитесь в том, что ваш проект предназначен для соответствующей архитектуры обработчика для приложения (например, **x86**) и что не выбран вариант **Любой процессор**. Элемент управления невозможно добавить в конструктор, если проект предназначен для **Любого процессора** платформы построения.

5.  Visual Studio добавляет ссылку на сборку рекламного посредника в проект и вставляет на текущую страницу код XAML для элемента управления рекламным посредником, содержащий уникальный идентификатор и название элемента управления. Ссылка на блок и код XAML зависят от целевой платформы. Например, для приложения UWP имя сборки — **Microsoft.AdMediator.Universal**, а созданный код XAML похож на приведенный в следующем примере.

    ```xml
    // Code that gets added to the XAML page header
    xmlns:Universal="using:Microsoft.AdMediator.Universal"

    // Code that gets added for the ad mediator control
    <Universal:AdMediatorControl x:Name="AdMediator_3D4884"
      Grid.ColumnSpan="2" HorizontalAlignment="Left" Height="250"
      Id="AdMediator-Id-D1FDFDA7-EABB-474C-940C-ECA7FBCFF143" Margin="121,175,0,0"
      VerticalAlignment="Top" Width="300"/>
    ```

    Элемент **Name** помогает определить конкретный элемент управления в приложении после настройки рекламного посредника. Его можно изменить на свое усмотрение, но не изменяйте и не копируйте элемент **Id**. Параметр **Id** должен быть уникальным для каждого элемента управления в приложении.

6.  Отрегулируйте размер и положение элемента управления нужным образом. Дополнительные сведения см. в разделе [Изменение размера и положения](#adjust-size-and-position).

## Настройка рекламных сетей

После добавления всех элементов управления можно переходить к настройке рекламных сетей через подключенные службы.

> 
            **Важно**  Если позже будет добавлен дополнительный элемент AdMediatorControl, необходимо будет снова его настроить через подключенные службы. В противном случае новый элемент управления не сможет использовать рекламный посредник.

Настройка рекламных сетей:

1.  В обозревателе решений щелкните правой кнопкой мыши имя проекта, нажмите **Добавить**, а затем— **Подключенная служба...**, чтобы открыть окно **Добавить подключенную службу** (Visual Studio 2015) или **Диспетчер служб** (Visual Studio 2013).
2.  Если вы используете VisualStudio2015, щелкните **Рекламный посредник**, а затем— **Настроить**, чтобы открыть окно **Рекламный посредник**. Если вы используете VisualStudio2013, просто щелкните **Рекламный посредник** на левой панели **диспетчера служб**.

    Файл AdMediator.config будет добавлен в ваш проект. Это файл, в котором в рамках вашего проекта будет локально сохранена начальная конфигурация рекламной сети.

3.  В окне **Ad Mediator** (VisualStudio2015) или **Диспетчер служб** (VisualStudio2013) щелкните **Выбрать рекламные сети**, выберите необходимые рекламные сети, а затем в окне **Выбор рекламных сетей** нажмите кнопку **ОК**.

    > 
            **Совет**  Рекомендуется добавить все сети, в которых у вас есть учетная запись, даже если вы не планируете сразу использовать все сети в своем приложении. После публикации приложения в центре разработки вы сможете настраивать частоту использования каждой сети (или начать использовать сеть, которая не использовалась ранее) без внесения изменений в код и повторной отправки приложения.

    VisualStudio получит все необходимые сборки для выбранных рекламных сетей и добавит ссылки на них в ваш проект. По завершении этого процесса в диалоговом окне **Состояние получения** нажмите кнопку **ОК**.

4.  Если необходимо, в окне **Рекламный посредник** (VisualStudio2015) или **Диспетчер служб** (VisualStudio2013) можно выбрать любую сеть, нажать кнопку **Настроить** и ввести сведения о конфигурации для этой сети, которые будут использоваться при тестировании приложения. Эти сведения хранятся в файле AdMediator.config в вашем проекте. Вы сможете изменять эту информацию после настройки поведения рекламной сети на информационной панели Центра разработки для Windows. Дополнительные сведения см. в статье [Отправка приложений и настройка рекламного посредника](submit-your-app-and-configure-ad-mediation.md).
    > 
            **Примечание**  Если вы не введете сведения о конфигурации на этом этапе, то когда вы запустите приложение на компьютере разработчика (для приложений UWP и Windows 8.1, использующих XAML), в эмуляторе или на устройстве (для приложений Windows Phone), рекламный посредник будет автоматически использовать значения тестовой конфигурации.

5.  В окне **Ad Mediator** (Visual Studio 2015) или **Диспетчер служб** (Visual Studio 2013) убедитесь, что для всех выбранных рекламных сетей отображается статус **Получено**. Нажмите кнопку **ОК**, чтобы внести изменения в проект.

> 
            **Примечание**   Если впоследствии вы перейдете на более новую версию пакета SDK Microsoft Store Engagement and Monetization, вам потребуется снова запустить **Подключенные службы**, чтобы убедиться в том, что все автоматически выбираемые библиотеки DLL рекламной сети обновлены правильно.

### Объявление требуемых возможностей

Для каждой рекламной сети могут требоваться определенные возможности приложения. Они указаны для каждого поставщика в окне **Рекламный посредник** (Visual Studio 2015) или **Диспетчер служб** (Visual Studio 2013). Обязательно задекларируйте все обязательные возможности в манифесте своего приложения, чтобы реклама отображалась надлежащим образом.

На следующем снимке экрана показаны требуемые возможности для некоторых рекламных сетей в приложении на XAML Windows8.1 или Windows Phone 8.1.

![диспетчер служб, в котором показаны все полученные ссылки](images/ad-med-8.jpg)

На следующем снимке экрана показаны требуемые возможности для некоторых рекламных сетей в приложении Windows Phone 8.1 Silverlight.

![диспетчер служб, в котором показаны все полученные ссылки](images/ad-med-6.jpg)
### Получение библиотек DLL рекламных сетей вручную

В некоторых случаях отдельные библиотеки DLL могут не быть получены. В таком случае вам потребуется добавить их вручную. Ссылки для скачивания отдельных сборок см. в разделе [Выбор рекламных сетей и управление ними](select-and-manage-your-ad-networks.md).

> 
            **Примечание**  При добавлении библиотек DLL вручную может отобразиться сообщение об ошибке: "В проект невозможно добавить ссылку на более позднюю версию или несовместимую сборку". Чтобы устранить эту ошибку, щелкните правой кнопкой мыши библиотеку DLL в проводнике, а затем выберите **Свойства**. В разделе "Безопасность" щелкните **Разблокировать**.

![кнопка разблокировки для устранения сообщения об ошибке](images/ad-med-4.png)
## Изменение размера и положения

Размер и положение элемента управления рекламным посредником можно настроить в конструкторе или в коде XAML. Убедитесь, что этот размер достаточно большой, чтобы соответствовать всем объявлениям, которые будут отображаться из рекламных сетей. Некоторые рекламные сети могут не выдавать объявления, если обнаружат, что размер полотна не достаточно большой для показа рекламы в полный размер. Если какое-либо объявление будет больше, чем размер по умолчанию, можно настроить размер холста по наиболее крупному объявлению.

Если перетащить элемент управления в конструктор, размеры элемента управления по умолчанию составляют:

-   UWP и Windows 8.1XAML: 300 x 250 (ШxВ).
-   Windows Phone 8.1 XAML: 400 в ширину × 67 в высоту.
-   WindowsPhone8 и Windows Phone 8.1 Silverlight: 480 x 80 (ШxВ).

Вы можете перезаписать размеры объявлений по умолчанию с помощью дополнительных параметров **Ширина** и **Высота**, как показано ниже.

```CSharp
myAdMediatorControl.AdSdkOptionalParameters[AdSdkNames.Smaato]["Width"] = 400;
myAdMediatorControl.AdSdkOptionalParameters[AdSdkNames.Smaato]["Height"] = 80;
```

Можно также указать, как будет расположен каждый элемент управления, чтобы подобрать различные размеры и расположения в зависимости от нужд приложения. Для настройки некоторых рекламных сетей вы можете использовать дополнительные параметры. Например, выровнять рекламу из Microsoft Advertising по нижнему левому краю можно следующим образом:

```CSharp
myAdMediatorControl.AdSdkOptionalParameters[AdSdkNames.MicrosoftAdvertising]["HorizontalAlignment"] = HorizontalAlignment.Left;
myAdMediatorControl.AdSdkOptionalParameters[AdSdkNames.MicrosoftAdvertising]["VerticalAlignment"] = VerticalAlignment.Bottom;
```

### Поддерживаемые размеры рекламы для Microsoft Advertising

Платформа Microsoft Advertising поддерживает только рекламу следующих стандартных размеров, рекомендуемых Бюро интерактивной рекламы (IAB) для приложений, работающих на указанных ниже платформах.

-   Windows 10 и Windows 8.1:
    -   160 x 600
    -   300 x 250
    -   300 x 600
    -   728 x 90
-   Windows 10 Mobile, Windows Phone 8.1 и Windows Phone 8:
    -   300 x 50
    -   320 x 50
    -   480 x 80 пикселей (Этот размер поддерживается только для Windows Phone Silverlight)
    -   640 x 100

Возможно, имеет смысл указать размер элемента управления Ad Mediator, который не соответствует ни одному из размеров рекламы, поддерживаемых Microsoft Advertising (например, если такой размер лучше подходит для пользовательского интерфейса вашего приложения или если вы также используете другие рекламные сети, поддерживающие другие размеры рекламы). Для этого укажите точный размер элемента управления в конструкторе или коде XAML, а затем присвойте дополнительным параметрам **Ширина** и **Высота** для Microsoft Advertising самые близкие из поддерживаемых значений, которые помещаются внутри границ элемента управления. Элемент управления будет отображаться в точном размере, указанном в конструкторе, а Microsoft Advertising будет показывать рекламу, которая соответствует размеру, определенному с помощью дополнительных параметров **Ширина** и **Высота**.

Например, если вы разрабатываете приложение UWP и хотите, чтобы элемент управления рекламным посредником отображался в размере 300 × 300, установите размер элемента управления 300 × 300 в конструкторе или в коде XAML. Затем присвойте дополнительному параметру **Ширина** значение 300, а дополнительному параметру **Высота** — значение 250 для Microsoft Advertising, как показано в следующем коде.

```CSharp
myAdMediatorControl.AdSdkOptionalParameters[AdSdkNames.MicrosoftAdvertising]["Width"] = 300;
myAdMediatorControl.AdSdkOptionalParameters[AdSdkNames.MicrosoftAdvertising]["Height"] = 250;
```

Чтобы проверить совместимость своих параметров размера с Microsoft Advertising, [протестируйте реализацию рекламного посредника](test-your-ad-mediation-implementation.md) и убедитесь, что тестовые объявления из Microsoft Advertising отображаются.

## Приостановка, возобновление и отключение рекламного посредника

Если необходимо приостановить работу рекламного посредника в любое время выполнения приложения, воспользуйтесь методом AdMediatorControl.Pause(). Обратите внимание на то, что при этом наиболее новое объявление будет и дальше отображаться, пока вы не возобновите работу посредника, вызвав метод AdMediatorControl.Resume().

Чтобы полностью отключить рекламный посредник, воспользуйтесь методом AdMediatorControl.Disable(). При этом будут удалены все объявления, которые отображаются. Будет также минимизировано влияние посредника на память. Чтобы возобновить работу посредника, можно вызвать метод AdMediatorControl.Resume(), но учтите то, что после отключения рекламного посредника время запуска будет более медленным, чем обычно.

## Настройка времени ожидания

Можно указать количество секунд (от 2 до 60), на протяжении которых рекламный посредник должен ждать после отправки запроса на объявление из этой рекламной сети, прежде чем отключить этот запрос и отправить его в другую сеть. По умолчанию время ожидания для всех рекламных сетей составляет 15 секунд.

В приведенном ниже коде показано, как указать время ожидания для Microsoft Advertising. При необходимости время ожидания и сети можно изменить.

```CSharp
myAdMediatorControl.AdSdkTimeouts[AdSdkNames.MicrosoftAdvertising] = TimeSpan.FromSeconds(10);
```

> 
            **Примечание**  Кроме того, значение времени ожидания можно задать на странице **Получение дохода с помощью рекламы** на информационной панели Центра разработки. Если вы задаете время ожидания в коде и на информационной панели, значение, заданное в коде, имеет приоритет перед значением на информационной панели.

## Обработка события

Добавление кода в журналы событий и выявление ошибок посредника может помочь в поиске и устранении неполадок. В приведенном ниже коде для определенных событий из элемента управления добавлены обработчики событий.

```CSharp
// add this during initialization of your app

    AdMediator_Bottom.AdSdkError += AdMediator_Bottom_AdError;
    AdMediator_Bottom.AdMediatorFilled += AdMediator_Bottom_AdFilled;
    AdMediator_Bottom.AdMediatorError += AdMediator_Bottom_AdMediatorError;
    AdMediator_Bottom.AdSdkEvent += AdMediator_Bottom_AdSdkEvent;

// and then add these functions

void AdMediator_Bottom_AdSdkEvent(object sender, Microsoft.AdMediator.Core.Events.AdSdkEventArgs e)
{
    Debug.WriteLine("AdSdk event {0} by {1}", e.EventName, e.Name);}

void AdMediator_Bottom_AdMediatorError(object sender, Microsoft.AdMediator.Core.Events.AdMediatorFailedEventArgs e)
{
    Debug.WriteLine("AdMediatorError:" + e.Error + " " + e.ErrorCode );
    // if (e.ErrorCode == AdMediatorErrorCode.NoAdAvailable)
    // AdMediator will not show an ad for this mediation cycle
}

void AdMediator_Bottom_AdFilled(object sender, Microsoft.AdMediator.Core.Events.AdSdkEventArgs e)
{
    Debug.WriteLine("AdFilled:" + e.Name);
}

void AdMediator_Bottom_AdError(object sender, Microsoft.AdMediator.Core.Events.AdFailedEventArgs e)
{
    Debug.WriteLine("AdSdkError by {0} ErrorCode: {1} ErrorDescription: {2} Error: {3}", e.Name, e.ErrorCode, e.ErrorDescription, e.Error);
}
```

## Обработка необработанных исключений из рекламных сетей

> 
            **Примечание**  В ходе тестирования мы определили количество необработанных исключений из определенных рекламных сетей, которые должны быть обработаны в приложении во избежание его сбоев, связанных с этими исключениями. Настоятельно рекомендуем скопировать и вставить образец кода ниже в файл App.xaml.cs.

Код для использования в приложении UWP, Windows8.1 или Windows Phone, в котором используются C# и XAML

```CSharp
// In App.xaml.cs file, register with the UnhandledException event handler.
UnhandledException += App_UnhandledException;

void App_UnhandledException(object sender, UnhandledExceptionEventArgs e)
   {
      if (e != null)
      {
         Exception exception = e.Exception;
         if (exception is NullReferenceException && exception.ToString().ToUpper().Contains("SOMA"))
         {
            Debug.WriteLine("Handled Smaato null reference exception {0}", exception);
            e.Handled = true;
            return;
         }
      }
// APP SPECIFIC HANDLING HERE

   if (Debugger.IsAttached)
      {
         // An unhandled exception has occurred; break into the debugger
         Debugger.Break();
      }
   }
```

Код для использования в приложении WindowsPhone Silverlight

```CSharp
// In App.xaml.cs file, register with the UnhandledException event handler.
UnhandledException += Application_UnhandledException;

// Code to execute on unhandled exceptions
private void Application_UnhandledException(object sender, ApplicationUnhandledExceptionEventArgs e)
{
    if (e != null)
   {
       Exception exception = e.ExceptionObject;
       if ((exception is XmlException || exception is NullReferenceException) && exception.ToString().ToUpper().Contains("INNERACTIVE"))
       {
           Debug.WriteLine("Handled Inneractive exception {0}", exception);
           e.Handled = true;
           return;
       }
       else if (exception is NullReferenceException && exception.ToString().ToUpper().Contains("SOMA"))
       {
           Debug.WriteLine("Handled Smaato null reference exception {0}", exception);
           e.Handled = true;
           return;
       }
       else if ((exception is System.IO.IOException || exception is NullReferenceException) && exception.ToString().ToUpper().Contains("GOOGLE"))
      {
          Debug.WriteLine("Handled Google exception {0}", exception);
          e.Handled = true;
          return;
       }
       else if ((exception is NullReferenceException || exception is XamlParseException) && exception.ToString().ToUpper().Contains("MICROSOFT.ADVERTISING"))
       {
           Debug.WriteLine("Handled Microsoft.Advertising exception {0}", exception);
           e.Handled = true;
           return;
       }

   }
// APP SPECIFIC HANDLING HERE

if (Debugger.IsAttached)
   {
       // An unhandled exception has occurred; break into the debugger
       Debugger.Break();
   }
   //e.Handled = true;
}
```

## Связанные темы

* [Выбор рекламных сетей и управление ими](select-and-manage-your-ad-networks.md)
* [Проверка реализации рекламного посредника](test-your-ad-mediation-implementation.md)
* [Отправка приложения и настройка рекламного посредника](submit-your-app-and-configure-ad-mediation.md)
* [Устранение неполадок рекламного посредника](troubleshoot-ad-mediation.md)
 

 



<!--HONumber=Jun16_HO4-->


