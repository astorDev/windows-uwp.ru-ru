---
Description: Windows 10 (версия 1511) и обновления средств разработчика обеспечивают доступ к средствам, компонентам и возможностям универсальной платформы Windows.
title: Новые возможности разработки в Windows 10 (1511), ноябрь 2015 г.
---

# Новые возможности для разработчиков в Windows 10 (версия 1511), ноябрь, 2015 г.

\[ Обновлено для приложений UWP в Windows 10. Статьи о Windows 8.x см. в [архиве](http://go.microsoft.com/fwlink/p/?linkid=619132) \]

В версии 1511 ОС Windows 10 и обновленных средствах разработчика мы, как и прежде, предоставляем полезные инструменты, функции и возможности на основе универсальной платформы Windows. [Установив средства и пакет SDK](https://dev.windows.com/downloads) в Windows 10 (версия 1511), вы можете сразу приступить либо к [созданию нового проекта универсального приложения для Windows](https://msdn.microsoft.com/library/windows/apps/bg124288) либо к использованию [существующего кода приложения в ОС Windows](https://msdn.microsoft.com/library/windows/apps/mt238321).

## Взаимодействие с пользователем

Новые классы <a href="https://msdn.microsoft.com/library/windows/apps/windows.ui.startscreen.aspx">Windows.UI.StartScreen.JumpList</a> и <a href="https://msdn.microsoft.com/library/windows/apps/windows.ui.startscreen.aspx">Windows.UI.StartScreen.JumpListItem</a> позволяют приложениям программными средствами выбирать тип управляемого системой списка переходов, который необходимо использовать, а также добавлять в список переходов специальные группы и точки входа задачи.

## Ввод
                                        
* <a href="https://msdn.microsoft.com/library/windows/apps/windows.ui.input.keyboarddeliveryinterceptor.aspx">Перехватчик событий клавиатуры</a>
                                        
    Позволяет приложению переопределять системную обработку входных сигналов клавиатуры, в том числе сочетаний клавиш, клавиш доступа (или горячих клавиш), клавиш вызова и клавиш меню. Специальные сочетания клавиш в этот список не входят.

    Специальные сочетания клавиш (SAS), в том числе Ctrl-Alt-Del и Windows-L, по-прежнему обрабатываются системой.
                                        
* Создание цепочки межпроцессного ввода с помощью указателя как для <a href="https://msdn.microsoft.com/library/windows/apps/windows.ui.core.corewindow.aspx">приложений UWP</a>, так и для <a href="https://msdn.microsoft.com/library/windows/desktop/hh454903(v=vs.85).aspx">классических приложений для Windows</a>.
                                        
    Новые события указателя, позволяющие формировать цепочки межпроцессного ввода.    
                                        
* <a href="https://msdn.microsoft.com/library/windows/desktop/mt622165(v=vs.85).aspx">Обработчик рукописного ввода для классических приложений</a>
                                        
    API обработчика рукописного ввода позволяет классическим приложениям Майкрософт Win32 управлять вводом, обработкой и отрисовкой рукописного ввода (стандартного и модифицированного) с помощью объекта <a href="https://msdn.microsoft.com/library/windows/desktop/windows.ui.input.inking.inkpresenter.aspx">InkPresenter</a>, добавляемого в визуальное дерево приложения <a href="https://msdn.microsoft.com/library/windows/desktop/hh437371(v=vs.85).aspx">DirectComposition</a>.    
                                    
## Сеть
                                                                        
Информация для пользователей WebSockets: <a href="https://msdn.microsoft.com/library/windows/apps/windows.storage.streams.datawriter.flushasync.aspx">MessageWebSocket.OutputStream.FlushAsync</a> и <a href="https://msdn.microsoft.com/library/windows/apps/windows.storage.streams.datawriter.flushasync.aspx">StreamWebSocket.OutputStream.FlushAsync</a> были полностью реализованы и ожидают завершения произведенных ранее вызовов WriteAsync. Обратите внимание, что при использовании существующего кода могут возникать исключения, если WebSocket находится в недопустимом состоянии при вызове <a href="https://msdn.microsoft.com/library/windows/apps/windows.storage.streams.datawriter.flushasync.aspx">FlushAsync</a>.    

Новое свойство <a href="https://msdn.microsoft.com/library/windows/apps/windows.web.http.filters.httpbaseprotocolfilter.aspx">CookieUsageBehavior</a> было добавлено к существующему классу <a href="https://msdn.microsoft.com/library/windows/apps/windows.web.http.filters.httpbaseprotocolfilter.aspx">Windows.Web.Http.Filters.HttpBaseProtocolFilter</a>. Это позволяет разработчикам контролировать обработку файлов cookie системой.    
                                    
## ORTC
                                    
В Microsoft Edge теперь реализована технология <a href="https://msdn.microsoft.com/library/mt433097(v=vs.85).aspx">ORTC (объектные коммуникации в реальном времени)</a>, которая позволяет совершать аудио- или видеовызовы в режиме реального времени напрямую между двумя браузерами, мобильными устройствами и серверами с помощью собственных API Javascript. Благодаря API-интерфейсу ORTC разработчики теперь могут создавать приложения для аудио- и видеовызовов в режиме реального времени для браузера Microsoft Edge. API поддерживает групповые видеовызовы, одновременную трансляцию, масштабируемое кодирование видео (SVC) и многое другое.    

Демонстрацию аудио- и видеовызовов между браузерами Microsoft Edge с помощью API-интерфейса ORTC можно найти на странице <a href="/microsoft-edge/testdrive/demos/ortcdemo/">Сайты и демонстрации тестового выпуска</a>. Обзор и подробный анализ образца кода можно найти в разделе <a href="https://msdn.microsoft.com/library/mt588497(v=vs.85).aspx">руководства разработчика ORTC</a>.
                                        
## Средства разработчика F12 в Microsoft Edge
                                                                        
В Microsoft Edge представлены новые улучшения средств разработчика F12, в том числе некоторые наиболее востребованные возможности <a href="https://wpdev.uservoice.com/forums/257854-microsoft-edge-developer">UserVoice</a>. Ознакомьтесь с новыми функциями средств «Проводник DOM», «Консоль», «Отладчик», «Сеть», «Производительность», «Память», «Эмуляция», а также возможностями нового инструмента «Эксперименты», который позволяет оценить работу новых компонентов до выхода их окончательной версии. Новые инструменты встроены в TypeScript. Они запущены все время, поэтому необходимость в перезагрузке отсутствует. Кроме того, документация по средствам разработчика F12 теперь представлена на <a href="http://dev.modern.ie/">веб-сайте разработчиков Microsoft Edge</a>. Ее полная версия доступна на <a href="https://github.com/MicrosoftEdge/MicrosoftEdge-Documentation">GitHub</a>. Теперь содержимое документации будет не просто обновляться в соответствии с вашими отзывами — мы приглашаем вас принять участие в ее создании. Чтобы просмотреть видеоролик, кратко освещающий средства разработчика F12, посетите канал <a href="https://channel9.msdn.com/Blogs/One-Dev-Minute/Microsoft-Edge-F12-tools">One Dev Minute</a>.    
                                    
## Windows Hello
                                    
Windows Hello позволяет приложению использовать возможности распознавания лиц или отпечатков пальцев для выполнения входа в систему Windows или выполнения входа на устройстве.

API для поставщиков позволяют независимым поставщикам оборудования и его производителям передавать информацию о глубине, а также данные инфракрасных и цветных камер и связанные метаданные в UWP для обеспечения машинного зрения и использовать камеру для распознавания лиц Windows Hello. Пространство имен <a href="http://go.microsoft.com/fwlink/?LinkId=691697">Windows.Devices.Perception</a> содержит клиентские API, которые позволяют приложению UWP получать доступ к данным о цвете, глубине и тепловом излучении, получаемым с помощью камер компьютера.
                                    
## Новый API для игр

Используйте новый класс Windows.Gaming.UI.GameBar, чтобы получать уведомления об отображении или закрытии Игровой панели.    
                            
                                    
## API-интерфейсы Bluetooth
                                    
Добавлены и обновлены несколько API, позволяющих расширить поддержку Bluetooth с низким энергопотреблением, а также поддержку перечисления устройств и некоторых других возможностей Bluetooth. См. пространство имен <a href="https://msdn.microsoft.com/library/windows/apps/windows.devices.bluetooth.aspx">Windows.Devices.Bluetooth</a>.    
                                   
## API смарт-карт ## 

В пространство имен <a href="https://msdn.microsoft.com/library/windows/apps/windows.devices.smartcards.aspx">Windows.Devices.SmartCards</a> было добавлено несколько API-интерфейсов SmartCardCryptogram, которые обеспечивают поддержку протоколов оплаты с использованием безопасного шифрования. Платежные приложения, использующие эмуляцию карты хоста для осуществления платежей одним нажатием, могут использовать эти API для обеспечения дополнительной безопасности и повышения производительности. Приложения могут создавать ключ, а также защищать коды операций ограниченного использования с помощью доверенного платформенного модуля. Приложения также могут использовать платформу Next Generation Credentials (NGC) для защиты ключей с помощью пользовательского PIN-кода. Чтобы повысить производительность, данные API передают создание криптограммы системе. Это также позволяет предотвратить доступ других приложений к ключам и криптограммам.    
                                    
## Обновленные API хранения ## 
    
<a href="https://msdn.microsoft.com/library/windows/apps/windows.storage.downloadsfolder.aspx">Класс Windows.Storage.DownloadsFolder</a><br />
Теперь ваше приложение может <a href="https://msdn.microsoft.com/library/windows/apps/windows.storage.downloadsfolder.createfileforuserasync.aspx">создавать файлы</a> или <a href="https://msdn.microsoft.com/library/windows/apps/windows.storage.downloadsfolder.createfolderforuserasync.aspx">папки</a> в каталоге «Загрузки» для конкретного <a href="https://msdn.microsoft.com/library/windows/apps/windows.system.user.aspx">пользователя</a>.
                                            
<a href="https://msdn.microsoft.com/library/windows/apps/windows.storage.storagelibrary.aspx">Класс Windows.Storage.StorageLibrary</a><br />
Теперь ваше приложение может <a href="https://msdn.microsoft.com/library/windows/apps/windows.storage.storagelibrary.getlibraryforuserasync.aspx">использовать указанную библиотеку</a> для конкретного <a href="https://msdn.microsoft.com/library/windows/apps/windows.system.user.aspx">пользователя</a>.
                                    
## Комплект сертификации приложений для Windows ## 
                                    
В комплект сертификации приложений для Windows были включены новые тесты. Полный список обновлений можно найти на странице <a href="/develop/app-certification-kit">комплекта сертификации приложений для Windows</a>.    
                                    
## Загружаемые файлы оформления ## 

Ознакомьтесь с новыми шаблонами оформления приложений UWP для Adobe Photoshop. Мы также обновили шаблоны Microsoft PowerPoint и Adobe Illustrator и опубликовали PDF-версию руководства. <a href="/design/assets">Посетите страницу скачиваемых файлов, связанных с проектированием</a>.    




<!--HONumber=Mar16_HO5-->


