---
author: JordanRh1
title: "Включение доступа в режиме пользователя в Windows 10 IoT Базовая"
description: "В этом руководстве описано, как предоставить доступ в пользовательском режиме для GPIO, I2C, SPI и UART в Windows 10 IoT Базовая."
ms.author: wdg-dev-content
ms.date: 02/08/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, uwp
ms.assetid: 2fbdfc78-3a43-4828-ae55-fd3789da7b34
translationtype: Human Translation
ms.sourcegitcommit: 5645eee3dc2ef67b5263b08800b0f96eb8a0a7da
ms.openlocfilehash: ced83940fb49f5812343fee34cb11582683bd672
ms.lasthandoff: 02/08/2017

---
# <a name="enable-usermode-access-on-windows-10-iot-core"></a>Включение доступа в режиме пользователя в Windows 10 IoT Базовая

\[ Обновлено для приложений UWP в Windows 10. Статьи о Windows 8.x см. в [архиве](http://go.microsoft.com/fwlink/p/?linkid=619132) \]


Windows 10 IoT Базовая содержит новые API для получения доступа к GPIO, I2C, SPI и UART непосредственно из пользовательского режима. Платы разработки, такие как Raspberry Pi 2, предоставляют подмножество таких подключений, которые позволяют пользователям расширить базовый вычислительный модуль, добавив в него электрические схемы для определенной сферы применения. Эти низкоуровневые шины обычно используются с другими ключевыми функциями платы, и лишь некоторые контакты и шины GPIO предоставляются в заголовках. Для сохранения стабильности системы необходимо указать, какие контакты и шины можно безопасно менять в приложениях пользовательского режима. 

В этом документе показано, как определить эту конфигурацию в ACPI и предоставляются средства для проверки правильности конфигурации. 

> [!IMPORTANT]
> Документ предназначен для разработчиков UEFI и ACPI. Подразумевается, что читатель знаком с созданием ACPI, ASL и технологиями SpbCx/GpioClx.

Доступ в пользовательском режиме к низкоуровневым шинам в Windows осуществляется с помощью существующих платформ `GpioClx` и `SpbCx`. Новый драйвер *RhProxy*, доступный только в Windows 10 IoT Базовая, предоставляет ресурсы `GpioClx` и `SpbCx` пользовательскому режиму. Чтобы включить API, узел устройств для rhproxy должен быть объявлен в таблицах ACPI с каждым ресурсом GPIO и SPB, которые необходимо предоставить пользовательскому режиму. Этот документ содержит пошаговое руководство по созданию и проверке ASL. 


## <a name="asl-by-example"></a>ASL на примерах

Давайте рассмотрим объявление узла устройств rhproxy в Raspberry Pi 2. Сначала создадим объявление устройства ACPI в \\_SB scope.  

```cpp
Device(RHPX) 
{ 
    Name(_HID, "MSFT8000") 
    Name(_CID, "MSFT8000") 
    Name(_UID, 1) 
    
```

* _HID — код оборудования. Укажите заданный поставщиком код оборудования. 
* _CID — совместимый ИД. Должен иметь вид MSFT8000.  
* _UID — уникальный ИД. Установите значение 1.  

Затем объявляем ресурсы GPIO и SPB, которые должны быть предоставлены пользовательскому режиму. Порядок объявления ресурсов важен, потому что индексированные ресурсы используются для связи свойств с ресурсами. Если предоставлено несколько шин I2C или SPI, первая объявленная шина считается шиной по умолчанию для этого типа и представляет собой экземпляр, возвращаемый методами `GetDefaultAsync()` [Windows.Devices.I2c.I2cController](https://msdn.microsoft.com/library/windows/apps/windows.devices.i2c.i2ccontroller.aspx) и [Windows.Devices.Spi.SpiController](https://msdn.microsoft.com/library/windows/apps/windows.devices.spi.spicontroller.aspx). 

### <a name="spi"></a>SPI 

Raspberry Pi имеет две предоставленные шины SPI. SPI0 имеет две строки для выбора чипов, а SPI1 — одну. Для каждой строки для выбора чипов (для каждой шины) требуется одно объявление ресурса SPISerialBus(). Следующие два объявления ресурсов SPISerialBus предназначены для двух строк для выбора чипов в SPI0. Поле DeviceSelection содержит уникальное значение, которое интерпретируется диском как идентификатор строки для выбора аппаратного чипа. Точное значение, которое вы используете в поле DeviceSelection, зависит от того, как драйвер интерпретирует это поле дескриптора подключения ACPI.  

```cpp
// Index 0 
SPISerialBus(              // SCKL - GPIO 11 - Pin 23 
                           // MOSI - GPIO 10 - Pin 19 
                           // MISO - GPIO 9  - Pin 21 
                           // CE0  - GPIO 8  - Pin 24 
    0,                     // Device selection (CE0) 
    PolarityLow,           // Device selection polarity 
    FourWireMode,          // wiremode 
    0,                     // databit len: placeholder 
    ControllerInitiated,   // slave mode 
    0,                     // connection speed: placeholder 
    ClockPolarityLow,      // clock polarity: placeholder 
    ClockPhaseFirst,       // clock phase: placeholder 
    "\\_SB.SPI0",          // ResourceSource: SPI bus controller name 
    0,                     // ResourceSourceIndex 
                           // Resource usage 
    )                      // Vendor Data 

// Index 1 
SPISerialBus(              // SCKL - GPIO 11 - Pin 23 
                           // MOSI - GPIO 10 - Pin 19 
                           // MISO - GPIO 9  - Pin 21 
                           // CE1  - GPIO 7  - Pin 26 
    1,                     // Device selection (CE1) 
    PolarityLow,           // Device selection polarity 
    FourWireMode,          // wiremode 
    0,                     // databit len: placeholder 
    ControllerInitiated,   // slave mode 
    0,                     // connection speed: placeholder 
    ClockPolarityLow,      // clock polarity: placeholder 
    ClockPhaseFirst,       // clock phase: placeholder 
    "\\_SB.SPI0",          // ResourceSource: SPI bus controller name 
    0,                     // ResourceSourceIndex 
                           // Resource usage 
    )                      // Vendor Data 

```

Например, "знает" ли программное обеспечение, что эти два ресурса нужно привязать к одной шине? Сопоставление между понятным именем шины и индексом ресурса задается в DSD:  

```cpp
Package(2) { "bus-SPI-SPI0", Package() { 0, 1 }}, 
```

В результате создается шина с именем SPI0 с двумя строками для выбора чипов: индексы ресурса 0 и 1. Еще несколько свойств необходимы для объявления возможностей шины SPI.  

```cpp
Package(2) { "SPI0-MinClockInHz", 7629 }, 
Package(2) { "SPI0-MaxClockInHz", 125000000 },
```

Свойства **MinClockInHz** и **MaxClockInHz** задают минимальную и максимальную тактовую частоту, поддерживаемую контроллером. API не позволит пользователям задать значения за пределами диапазона. Тактовая частота передается драйверу SPB в поле _SPE дескриптора подключения (раздел 6.4.3.8 .2.2 ACPI).  

```cpp
Package(2) { "SPI0-SupportedDataBitLengths", Package() { 8 }}, 
```

Свойство **SupportedDataBitLengths** перечисляет длины битов данных, поддерживаемые контроллером. Несколько значений можно указать в виде списка с разделителями-запятыми. API не позволит пользователям задать значения за пределами списка. Длина бита данных передается драйверу SPB в поле _LEN дескриптора подключения (раздел 6.4.3.8 .2.2 ACPI).  

Объявления ресурсов можно представить в качестве шаблонов. Некоторые поля фиксируются на этапе загрузки системы, в то время как другие динамически задаются во время выполнения. Следующие поля дескриптора SPISerialBus фиксированы: 

* DeviceSelection 
* DeviceSelectionPolarity 
* WireMode 
* SlaveMode 
* ResourceSource 

Следующие поля являются заполнителями для значений, заданных пользователем во время выполнения: 

* DataBitLength 
* ConnectionSpeed 
* ClockPolarity 
* ClockPhase 

Поскольку SPI1 содержит только одну строку для выбора чипа, объявляется один ресурс `SPISerialBus()`. 

```cpp
// Index 2 

SPISerialBus(              // SCKL - GPIO 21 - Pin 40 
                           // MOSI - GPIO 20 - Pin 38 
                           // MISO - GPIO 19 - Pin 35 
                           // CE1  - GPIO 17 - Pin 11 
    1,                     // Device selection (CE1) 
    PolarityLow,           // Device selection polarity 
    FourWireMode,          // wiremode 
    0,                     // databit len: placeholder 
    ControllerInitiated,   // slave mode 
    0,                     // connection speed: placeholder 
    ClockPolarityLow,      // clock polarity: placeholder 
    ClockPhaseFirst,       // clock phase: placeholder 
    "\\_SB.SPI1",          // ResourceSource: SPI bus controller name 
    0,                     // ResourceSourceIndex 
                           // Resource usage 
    )                      // Vendor Data 

```

Декларация соответствующего понятного имени (обязательная) определяется в DSD и относится к индексу этой декларации ресурса. 

```cpp
Package(2) { "bus-SPI-SPI1", Package() { 2 }}, 
```

Это создает шину SPI1 и связывает ее с индексом ресурса 2.  

#### <a name="spi-driver-requirements"></a>Требования драйвера SPI 

* Необходимо использовать `SpbCx` или быть совместимым с SpbCx 
* Передача [тестов MITT SPI](https://msdn.microsoft.com/library/windows/hardware/dn919873.aspx)
* Поддержка тактовой частоты 400 МГц 
* Поддержка 8-битной длины данных 
* Поддержка всех режимов SPI: 0, 1, 2, 3 

### <a name="i2c"></a>I2C 

Затем объявляем ресурсы I2C. Raspberry Pi предоставляет одну шину I2C в контактах 3 и 5. 

```cpp
// Index 3 
I2CSerialBus(              // Pin 3 (GPIO2, SDA1), 5 (GPIO3, SCL1) 
    0xFFFF,                // SlaveAddress: placeholder 
    ,                      // SlaveMode: default to ControllerInitiated 
    0,                     // ConnectionSpeed: placeholder 
    ,                      // Addressing Mode: placeholder 
    "\\_SB.I2C1",          // ResourceSource: I2C bus controller name 
    , 
    , 
    )                      // VendorData 

```

Декларация соответствующего понятного имени (обязательная) определяется в DSD: 

```cpp
Package(2) { "bus-I2C-I2C1", Package() { 3 }}, 
```

Здесь объявляется шина I2C с понятным именем I2C1, ссылающаяся на индекс ресурсов 3, который является индексом ресурса I2CSerialBus(), который мы объявили выше. 

Следующие поля дескриптора I2CSerialBus() фиксированы: 

* SlaveMode 
* ResourceSource 

Следующие поля являются заполнителями для значений, заданных пользователем во время выполнения. 

* SlaveAddress 
* ConnectionSpeed 
* AddressingMode 

#### <a name="i2c-driver-requirements"></a>Требования драйвера I2C 

* Необходимо использовать SpbCx или быть совместимым с SpbCx 
* Передача [тестов MITT I2C](https://msdn.microsoft.com/library/windows/hardware/dn919852.aspx) 
* Поддержка 7-битной адресации 
* Поддержка тактовой частоты 100 кГц 
* Поддержка тактовой частоты 400 кГц 

### <a name="gpio"></a>GPIO 

Затем мы объявляем все контакты GPIO, которые доступны для пользовательского режима. Мы предлагаем следующие рекомендации по выбору контактов для предоставления. 

* Объявите все контакты в качестве предоставленных заголовков. 
* Объявите контакты, подключенные к полезным функциям системы, таким как кнопки и светодиодные индикаторы. 
* Не объявляйте контакты, которые зарезервированы для системных функций и ни к чему не подключены. 

В следующем блоке ASL объявлено два контакта — GPIO4 и GPIO5. Другие контакты не отображаются здесь для краткости. В приложении C представлен пример скрипта PowerShell, который можно использовать для создания ресурса GPIO. 

```cpp
// Index 4 – GPIO 4 
GpioIO(Shared, PullUp, , , , “\\_SB.GPI0”, , , , ) { 4 } 
GpioInt(Edge, ActiveBoth, Shared, PullUp, 0, “\\_SB.GPI0”,) { 4 } 

// Index 6 – GPIO 5 
GpioIO(Shared, PullUp, , , , “\\_SB.GPI0”, , , , ) { 5 } 
GpioInt(Edge, ActiveBoth, Shared, PullUp, 0, “\\_SB.GPI0”,) { 5 } 
```

Следующие требования необходимо соблюдать при объявлении контактов GPIO: 

* Поддерживаются только контроллеры GPIO с сопоставлением памяти. Контроллеры GPIO с интерфейсами в I2C/SPI не поддерживаются. Драйвер контроллера — это контроллер с сопоставлением памяти, если он задает флаг [MemoryMappedController](https://msdn.microsoft.com/library/windows/hardware/hh439449.aspx) в структуре [CLIENT_CONTROLLER_BASIC_INFORMATION](https://msdn.microsoft.com/library/windows/hardware/hh439358.aspx) в ответ на обратный вызов [CLIENT_QueryControllerBasicInformation](https://msdn.microsoft.com/library/windows/hardware/hh439399.aspx). 
* Каждый контакт требует обоих ресурсов: GpioIO и GpioInt. Ресурс GpioInt должен немедленно следовать за ресурсом GpioIO и иметь тот же номер контакта. 
* Ресурсы GPIO нужно упорядочить по увеличению номера контакта. 
* Каждый ресурс GpioIO и GpioInt должен содержать ровно один номер контакта в списке контактов. 
* Поле ShareType обоих дескрипторов должно быть общим 
* Поле EdgeLevel дескриптора GpioInt должно иметь вид Edge 
* Поле ActiveLevel дескриптора GpioInt должно иметь вид ActiveBoth 
* Поле PinConfig 
  * Должны быть одинаковым в дескрипторах GpioIO и GpioInt 
  * Должно относиться к одному из следующих объектов: PullUp, PullDown или PullNone. Использовать PullDefault невозможно.
  * Конфигурация запроса должна соответствовать включенному состоянию системы. Помещение контакта в определенный режим опроса из состояния включения не должно менять состояние контакта. Например, если в таблице определено, что контакт функционирует в восходящем режиме, задайте для свойства PinConfig значение PullUp.  

Встроенное ПО, UEFI и код инициализации драйверов не должны изменять состояние контакта по сравнению с включенным состоянием во время загрузки. Только пользователь знает, что подключено к контакту и какие изменения состояния безопасны. Состояние включения каждого контакта необходимо зафиксировать документально, чтобы можно было создать оборудование, которое бы исправно функционировало с этим контактом. Состояние контакта не должно неожиданно меняться во время загрузки. 

Если предоставленный контакт имеет несколько альтернативных функций, встроенное ПО должно инициализировать контакт с правильной конфигурацией мультиплексирования для последующего использования ОС. Динамическое изменение функции контакта (мультиплексирование) в настоящее время не поддерживается в Windows. 

#### <a name="supported-drive-modes"></a>Поддерживаемые режимы диска 

Если контроллер GPIO поддерживает встроенные восходящие и нисходящие резисторы в дополнение к высокоимпедансному вводу и выводу CMOS, необходимо указать это с помощью дополнительного свойства SupportedDriveModes. 

```cpp
Package (2) { “GPIO-SupportedDriveModes”, 0xf }, 
```

Свойство SupportedDriveModes указывает, какие режимы диска поддерживаются контроллером GPIO. В примере выше поддерживаются все следующие режимы диска. Свойство — это битовая маска следующих значений. 

| Значение флага | Режим диска | Описание |
|------------|------------|-------------|
| 0x1        | InputHighImpedance | Система поддерживает высокоимпедансный ввод, что соответствует значению PullNone в ACPI. |
| 0x2        | InputPullUp | Система поддерживает встроенный повышающий резистор, который соответствует значению PullUp в ACPI. |
| 0x4        | InputPullDown | Система поддерживает встроенный понижающий резистор, который соответствует значению PullDown в ACPI. |
| 0x8        | OutputCmos | Система поддерживает создание строгих верхних и строгих нижних значений (в противоположность открытому стоку). |

InputHighImpedance и OutputCmos поддерживаются практически всеми контроллерами GPIO. Если свойство SupportedDriveModes не задано, это значение по умолчанию. 

Если сигнал GPIO проходит через схему сдвига уровня, прежде чем достигнет предоставленного заголовка, необходимо объявить режимы диска, поддерживаемые SOC, даже если режим диска не будет отображаться на внешнем заголовке. Например, если контакт проходит через двунаправленную схему сдвига уровня, в результате чего контакт отображается как открытый сток с резистивным повышением, высокоимпедансное состояние никогда не будет наблюдаться на предоставленном заголовке, даже если контакт настроен для высокоимпедансного ввода. Даже в этом случае нужно объявлять, что контакт поддерживает высокоимпедансный ввод. 

#### <a name="pin-numbering"></a>Нумерация контактов 

Windows поддерживает две схемы нумерации контактов: 

* Последовательная нумерация контактов: пользователи видят номера 0, 1, 2 ... до суммарного числа предоставленных контактов. 0 — это первый ресурс GpioIo, объявленный в ASL, 1 — второй ресурс GpioIo, объявленный в ASL и т. д. 
* Естественная нумерация контактов: пользователи видят номера, заданные дескрипторами GpioIo, например 4, 5, 12, 13, … .  

```cpp
Package (2) { “GPIO-UseDescriptorPinNumbers”, 1 }, 
```

Свойство **UseDescriptorPinNumbers** указывает Windows, что нужно использовать естественную, а не последовательную нумерацию контактов. Если свойство UseDescriptorPinNumbers не задано или значение равно нулю, Windows по умолчанию будет использовать последовательную нумерацию контактов. 

Если используется естественная нумерация контактов, необходимо также задать свойство **PinCount**. 

```cpp
Package (2) { “GPIO-PinCount”, 54 }, 
```

Свойство **PinCount** должно соответствовать значению, возвращаемому свойству **TotalPins** в обратном вызове [CLIENT_QueryControllerBasicInformation](https://msdn.microsoft.com/library/windows/hardware/hh439399.aspx) драйвера `GpioClx`. 

Выберите схему нумерации, которая более всего соответствует существующей опубликованной документации для вашей платы. Так, Raspberry Pi использует естественную нумерацию, потому что на многих существующих диаграммах вывода используются номера контактов BCM2835. MinnowBoardMax использует последовательную нумерацию, потому что существующих диаграмм вывода не так много, а последовательная нумерация упрощает работу разработчиков, потому что лишь 10 контактов из 200 используются для вывода. Решение об использовании последовательной или естественной нумерации контактов должно избавить разработчиков от путаницы. 

#### <a name="gpio-driver-requirements"></a>Требования драйвера GPIO 

* Необходимо использовать `GpioClx`
* Сопоставление памяти on-SOC 
* Использование эмулированной обработки прерываний ActiveBoth 

### <a name="uart"></a>UART 

UART не поддерживается в Raspberry Pi на момент написания статьи, поэтому источником следующего объявления UART является MinnowBoardMax. 

```cpp
// Index 2 
UARTSerialBus(           // Pin 17, 19 of JP1, for SIO_UART2 
    115200,                // InitialBaudRate: in bits ber second 
    ,                      // BitsPerByte: default to 8 bits 
    ,                      // StopBits: Defaults to one bit 
    0xfc,                  // LinesInUse: 8 1-bit flags to declare line enabled 
    ,                      // IsBigEndian: default to LittleEndian 
    ,                      // Parity: Defaults to no parity 
    ,                      // FlowControl: Defaults to no flow control 
    32,                    // ReceiveBufferSize 
    32,                    // TransmitBufferSize 
    "\\_SB.URT2",          // ResourceSource: UART bus controller name 
    , 
    , 
    , 
    )
```

Только поле ResourceSource является фиксированным, в то время как все остальные поля — заполнители для значений, которые во время выполнения задает пользователь. 

Соответствующее объявление понятного имени имеет вид:+ 

```cpp
Package(2) { "bus-UART-UART2", Package() { 2 }}, 
```

В результате понятное имя UART2 назначается контроллеру, который является идентификатором, используемым для доступа к шине в пользовательском режиме.  

## <a name="runtime-pin-muxing"></a>Мультиплексирование контактов в среде выполнения 

Мультиплексирование контактов — это возможность использовать один и тот же физический контакт в разных целях. Несколько других чиповых периферийных устройств, таких как контроллер I2C, контроллер SPI и контроллер GPIO, можно перенаправить на тот же физический контакт в SOC. Модуль мультиплексирования контролирует активность функций контакта в конкретный момент. Как правило, встроенное ПО отвечает за назначение функций при загрузке и назначение остается статическим на протяжении всего сеанса загрузки. Мультиплексирование контактов во время выполнения добавляет возможность перенастройки назначений функций контактов во время выполнения. Предоставление пользователям возможности выбрать функцию контакта во время выполнения ускоряет разработку, потому что пользователи могут быстро перенастроить контакты платы, и обеспечивает аппаратную поддержку более широкого диапазона сфер применения по сравнению со статической конфигурацией. 

Пользователи могут пользоваться поддержкой мультиплексирования для GPIO, I2C, SPI и UART, не создавая дополнительный код. Когда пользователь открывает GPIO или шину с использованием [OpenPin()](https://msdn.microsoft.com/library/dn960157.aspx) или [FromIdAsync()](https://msdn.microsoft.com/windows.devices.i2c.i2cdevice.fromidasync), соответствующие физические контакты автоматически мультиплексируются для запрошенной функции. Если контакты уже используются другой функцией, вызов OpenPin() или FromIdAsync() завершится ошибкой. Когда пользователь закрывает устройство, удаляя объект [GpioPin](https://msdn.microsoft.com/library/windows/apps/windows.devices.gpio.gpiopin.aspx), [I2cDevice](https://msdn.microsoft.com/library/windows/apps/windows.devices.i2c.i2cdevice.aspx), [SpiDevice](https://msdn.microsoft.com/library/windows/apps/windows.devices.spi.spidevice.aspx) или [SerialDevice](https://msdn.microsoft.com/library/windows/apps/windows.devices.serialcommunication.serialdevice.aspx), контакты снова становятся свободными, что позволяет в дальнейшем использовать их для других функций. 

В Windows реализована встроенная поддержка мультиплексирования контактов на платформах [GpioClx](https://msdn.microsoft.com/library/windows/hardware/hh439515.aspx), [SpbCx](https://msdn.microsoft.com/library/windows/hardware/hh406203.aspx) и [SerCx](https://msdn.microsoft.com/library/windows/hardware/dn265349.aspx). Эти платформы взаимодействуют, чтобы автоматически переключать контакт на нужную функцию, когда осуществляется доступ к контакту или шине GPIO. Доступ к контактам осуществляется произвольно с целью предотвращения конфликтов между несколькими клиентами. В дополнение к этой встроенной поддержке интерфейсы и протоколы для мультиплексирования контактов являются универсальными и могут быть расширены для обеспечения поддержки дополнительных устройств и сценариев. 

В этом документе сначала описываются базовые интерфейсы и протоколы, участвующие в мультиплексировании контактов, а затем описывается добавление поддержки мультиплексирования контактов в драйверах контроллера GpioClx, SpbCx и SerCx. 

### <a name="pin-muxing-architecture"></a>Архитектура мультиплексирования контактов 

В этом разделе описываются основные интерфейсы и протоколы, участвующие в мультиплексировании контактов. Знание основных протоколов не обязательно для поддержки мультиплексировании контактов с драйверами GpioClx/SpbCx/SerCx. Дополнительные сведения о поддержке мультиплексирования контактов с драйверами GpioCls/SpbCx/SerCx см. в разделах [Реализация поддержки мультиплексирования контактов в драйверах клиента GpioClx](#supporting-muxing-support-in-GpioClx-client-drivers) и [Реализация поддержки мультиплексирования в драйверах контроллера SpbCx и SerCx](#supporting-muxing-in-SpbCx-and-SerCx-controller-drivers). 

Мультиплексирование контактов осуществляется взаимодействием нескольких компонентов. 

* Серверы мультиплексирования контактов — это драйверы, контролирующие блок управления мультиплексирования контактов. Серверы мультиплексирования контактов получают запросы на мультиплексирование контактов от клиентов в виде запросов на резервирование ресурсов мультиплексирования (через *IRP_MJ_CREATE*) и запросов на изменение функции контакта (через запросы *IOCTL_GPIO_COMMIT_FUNCTION_CONFIG_PINS*). Сервер мультиплексирования контактов — это, как правило, драйвер GPIO, поскольку блок мультиплексирования иногда является частью блока GPIO. Даже если блок мультиплексирования — отдельное периферийное устройство, драйвер GPIO является наиболее логичным расположением для размещения функциональности мультиплексирования. 
* Клиенты мультиплексирования контактов — драйверы, использующие мультиплексирование контактов. Клиенты мультиплексирования контактов получают ресурсы от промежуточного ПО ACPI. Ресурсы мультиплексирования контактов — это тип ресурса подключения, управляемый центром ресурсов. Клиенты мультиплексирования контактов резервируют ресурсы, открывая дескриптор для ресурса. Для внесения аппаратных изменений необходимо зафиксировать конфигурацию, отправив запрос *IOCTL_GPIO_COMMIT_FUNCTION_CONFIG_PINS*. Клиенты освобождают ресурсы мультиплексирования контактов, закрывая дескриптор. На этом этапе конфигурация мультиплексирования возвращается в состояние по умолчанию. 
* Встроенное ПО ACPI — определяет конфигурацию мультиплексирования с ресурсами `MsftFunctionConfig()`. Ресурсы MsftFunctionConfig показывают, какие контакты и в каких конфигурациях мультиплексирования необходимы клиенту. Ресурсы MsftFunctionConfig содержат номер функции, конфигурацию запроса и список номеров контактов. Ресурсы MsftFunctionConfig предоставляются клиентам мультиплексирования контактов в качестве аппаратных; их получают драйверы в ходе обратного вызова PrepareHardware аналогично ресурсам подключения GPIO и SPB. Клиенты получают ИД центра ресурсов, который можно использовать для открытия дескриптора ресурса. 

> Необходимо передать `asl.exe` параметр командной строки `/MsftInternal` , чтобы собрать ASL-файлы, содержащие дескрипторы `MsftFunctionConfig()`, поскольку эти дескрипторы в данный момент рассматриваются рабочим комитетом ACPI. Пример. `asl.exe /MsftInternal dsdt.asl`

Ниже показана последовательность операций мультиплексирования контактов. 

![Взаимодействие клиент-сервер в процессе мультиплексирования контактов](images/usermode-access-diagram-1.png)

1.    Клиент получает ресурсы MsftFunctionConfig от встроенного ПО ACPI в ходе обратного вызова [EvtDevicePrepareHardware()](https://msdn.microsoft.com/library/windows/hardware/ff540880.aspx).
2.    Клиент использует вспомогательную функцию центра ресурсов `RESOURCE_HUB_CREATE_PATH_FROM_ID()` для создания пути из кода ресурса, который открывает дескриптор для пути (с помощью [ZwCreateFile()](https://msdn.microsoft.com/library/windows/hardware/ff566424.aspx), [IoGetDeviceObjectPointer()](https://msdn.microsoft.com/library/windows/hardware/ff549198.aspx) или [WdfIoTargetOpen()](https://msdn.microsoft.com/library/windows/hardware/ff548634.aspx)).
3.    Сервер извлекает ИД центра ресурсов из пути файла, используя вспомогательные функции центра ресурсов `RESOURCE_HUB_ID_FROM_FILE_NAME()`, а затем запрашивает дескриптор ресурса в центре ресурсов.
4.    Сервер выполняет арбитраж общего доступа для каждого контакта в дескрипторе и выполняет запрос IRP_MJ_CREATE.
5.    Клиент выпускает запрос *IOCTL_GPIO_COMMIT_FUNCTION_CONFIG_PINS* на полученном дескрипторе.
6.    В ответ на запрос *IOCTL_GPIO_COMMIT_FUNCTION_CONFIG_PINS* сервер выполняет аппаратную операцию мультиплексирования, активируя указанную функцию на всех контактах.
7.    Клиент продолжает операции, которые зависят от конфигурации запрошенного мультиплексирования контактов.
8.    Когда клиент более не требует мультиплексирования контактов, он закрывает дескриптор.
9.    В ответ на это сервер возвращает контакты в исходное состояние.

###    <a name="protocol-description-for-pin-muxing-clients"></a>Описание протокола для клиентов мультиплексирования контактов

В этом разделе описано использование функции мультиплексирования контактов клиентом Это не относится к драйвера контроллера `SerCx` и `SpbCx`, поскольку платформа реализует этот протокол от имени драйверов контроллера.

####    <a name="parsing-resources"></a>Анализ ресурсов

Драйвер WDF получает ресурсы `MsftFunctionConfig()` в рамках процедуры [EvtDevicePrepareHardware()](https://msdn.microsoft.com/library/windows/hardware/ff540880.aspx). Ресурсы MsftFunctionConfig можно идентифицировать по следующим полям.

```cpp
CM_PARTIAL_RESOURCE_DESCRIPTOR::Type = CmResourceTypeConnection
CM_PARTIAL_RESOURCE_DESCRIPTOR::u.Connection.Class = CM_RESOURCE_CONNECTION_CLASS_FUNCTION_CONFIG
CM_PARTIAL_RESOURCE_DESCRIPTOR::u.Connection.Type = CM_RESOURCE_CONNECTION_TYPE_FUNCTION_CONFIG
```

Процедура `EvtDevicePrepareHardware()` может извлекать ресурсы MsftFunctionConfig следующим образом.

```cpp
EVT_WDF_DEVICE_PREPARE_HARDWARE evtDevicePrepareHardware;

_Use_decl_annotations_
NTSTATUS
evtDevicePrepareHardware (
    WDFDEVICE WdfDevice,
    WDFCMRESLIST ResourcesTranslated
    )
{
    PAGED_CODE();

    LARGE_INTEGER connectionId;
    ULONG functionConfigCount = 0;

    const ULONG resourceCount = WdfCmResourceListGetCount(ResourcesTranslated);
    for (ULONG index = 0; index < resourceCount; ++index) {
        const CM_PARTIAL_RESOURCE_DESCRIPTOR* resDescPtr =
            WdfCmResourceListGetDescriptor(ResourcesTranslated, index);

        switch (resDescPtr->Type) {
        case CmResourceTypeConnection:
            switch (resDescPtr->u.Connection.Class) {
            case CM_RESOURCE_CONNECTION_CLASS_FUNCTION_CONFIG:
                switch (resDescPtr->u.Connection.Type) {
                case CM_RESOURCE_CONNECTION_TYPE_FUNCTION_CONFIG:                    
                    switch (functionConfigCount) {
                    case 0:
                        // save the connection ID
                        connectionId.LowPart = resDescPtr->u.Connection.IdLowPart;
                        connectionId.HighPart = resDescPtr->u.Connection.IdHighPart;
                        break;
                    } // switch (functionConfigCount)
                    ++functionConfigCount;
                    break; // CM_RESOURCE_CONNECTION_TYPE_FUNCTION_CONFIG

                } // switch (resDescPtr->u.Connection.Type)
                break; // CM_RESOURCE_CONNECTION_CLASS_FUNCTION_CONFIG
            } // switch (resDescPtr->u.Connection.Class)
            break;
        } // switch
    } // for (resource list)

    if (functionConfigCount < 1) {
        return STATUS_INVALID_DEVICE_CONFIGURATION;
    }
    // TODO: save connectionId in the device context for later use

    return STATUS_SUCCESS;
}
```

####    <a name="reserving-and-committing-resources"></a>Резервирование и фиксация ресурсов

Если клиент желает выполнить мультиплексирование контактов, он резервирует и фиксирует ресурс MsftFunctionConfig. В следующем примере показано, как клиент может зарезервировать и зафиксировать ресурсы MsftFunctionConfig.

```cpp
_IRQL_requires_max_(PASSIVE_LEVEL)
NTSTATUS AcquireFunctionConfigResource (
    WDFDEVICE WdfDevice,
    LARGE_INTEGER ConnectionId,
    _Out_ WDFIOTARGET* ResourceHandlePtr
    )
{
    PAGED_CODE();

    //
    // Form the resource path from the connection ID
    //
    DECLARE_UNICODE_STRING_SIZE(resourcePath, RESOURCE_HUB_PATH_CHARS);
    NTSTATUS status = RESOURCE_HUB_CREATE_PATH_FROM_ID(
            &resourcePath,
            ConnectionId.LowPart,
            ConnectionId.HighPart);
    if (!NT_SUCCESS(status)) {
        return status;
    }
    
    //
    // Create a WDFIOTARGET
    //
    WDFIOTARGET resourceHandle;
    status = WdfIoTargetCreate(WdfDevice, WDF_NO_ATTRIBUTES, &resourceHandle);
    if (!NT_SUCCESS(status)) {
        return status;
    }

    //
    // Reserve the resource by opening a WDFIOTARGET to the resource
    //
    WDF_IO_TARGET_OPEN_PARAMS openParams;
    WDF_IO_TARGET_OPEN_PARAMS_INIT_OPEN_BY_NAME(
        &openParams,
        &resourcePath,
        FILE_GENERIC_READ | FILE_GENERIC_WRITE);

    status = WdfIoTargetOpen(resourceHandle, &openParams);
    if (!NT_SUCCESS(status)) {
        return status;
    }
    //
    // Commit the resource
    //
    status = WdfIoTargetSendIoctlSynchronously(
            resourceHandle,
            WDF_NO_HANDLE,      // WdfRequest
            IOCTL_GPIO_COMMIT_FUNCTION_CONFIG_PINS,
            nullptr,            // InputBuffer
            nullptr,            // OutputBuffer
            nullptr,            // RequestOptions
            nullptr);           // BytesReturned
    
    if (!NT_SUCCESS(status)) {
        WdfIoTargetClose(resourceHandle);
        return status;
    }

    //
    // Pins were successfully muxed, return the handle to the caller
    //
    *ResourceHandlePtr = resourceHandle;
    return STATUS_SUCCESS;
}
```

Драйвер сохраняет WDFIOTARGET в одной из контекстных областей, чтобы впоследствии закрыть его. Когда драйвер готов освободить конфигурацию мультиплексирования, он закрывает дескриптор ресурса, вызывая метод [WdfObjectDelete()](https://msdn.microsoft.com/library/windows/hardware/ff548734.aspx) или [WdfIoTargetClose()](https://msdn.microsoft.com/library/windows/hardware/ff548586.aspx), если планируется использовать WDFIOTARGET повторно.

```cpp
    WdfObjectDelete(resourceHandle);
```

Когда клиент закрывает дескриптор ресурсы, контакты возвращаются в исходное состояние. Теперь они доступны и для других клиентов.

###    <a name="protocol-description-for-pin-muxing-servers"></a>Описание протокол для серверов мультиплексирования контактов

В этом разделе описывается, как сервер мультиплексирования контактов предоставляет свою функциональность клиентам. Это не относится к драйверам мини-портов `GpioClx`, поскольку платформа реализует этот протокол от имени драйверов клиента. Подробные сведения о поддержке мультиплексирования контактов в клиентских драйверах `GpioClx` см. в разделе [Реализация поддержки мультиплексирования в клиентских драйверах GpioClx](#supporting-muxing-support-in-GpioClx-client-drivers).

####    <a name="handling-irpmjcreate-requests"></a>Обработка запросов IRP_MJ_CREATE

Клиенты открывают дескриптор для ресурса, когда требуется зарезервировать ресурс мультиплексирования контактов. Сервер мультиплексирования контактов получает запросы *IRP_MJ_CREATE* путем выполнения повторного анализа центра ресурсов. Конечный компонент пути запроса *IRP_MJ_CREATE* содержит код центра ресурсов, который является 64-битовым целым числом в шестнадцатеричном формате. Сервер должен извлечь ИД центра ресурсов из имени файла с помощью `RESOURCE_HUB_ID_FROM_FILE_NAME()` в reshub.h и отправить *IOCTL_RH_QUERY_CONNECTION_PROPERTIES* в центр ресурсов для получения дескриптора `MsftFunctionConfig()`.

Сервер должен проверить дескриптор и извлечь из него режим общего доступа и список контактов. Затем он должен выполнить арбитраж общего доступа контактов и в случае успешного завершения — пометить контакты как зарезервированные перед выполнением запроса.

Арбитраж общего доступа завершается успешно в целом, если он завершается успешно для всех контактов в списке. Арбитраж каждого контакта выполняется следующим образом.

*    Если контакт еще не зарезервирован, арбитраж общего доступа завершается успешно.
*    Если контакт уже зарезервирован как монопольный, происходит сбой арбитража.
*    Если контакт уже зарезервирован как общий,
  *    и входящий запрос используется совместно, арбитраж выполняется успешно.
  *    а входящий запрос является монопольным, происходит сбой арбитража.

Если происходит сбой арбитража общего доступа, запрос необходимо обработать с помощью *STATUS_GPIO_INCOMPATIBLE_CONNECT_MODE*. Если арбитраж общего доступа выполняется успешно, запрос обрабатывается с помощью *STATUS_SUCCESS*.

Обратите внимание, что режим общего доступа входящих запросов должен быть получен из дескриптора MsftFunctionConfig, а не из [IrpSp->Parameters.Create.ShareAccess](https://msdn.microsoft.com/library/windows/hardware/ff548630.aspx).

####    <a name="handling-ioctlgpiocommitfunctionconfigpins-requests"></a>Обработка запросов IOCTL_GPIO_COMMIT_FUNCTION_CONFIG_PINS

После того как клиент успешно резервирует ресурс MsftFunctionConfig, открывая дескриптор, можно отправить *IOCTL_GPIO_COMMIT_FUNCTION_CONFIG_PINS*, чтобы отправить на сервер запрос о выполнении фактической операции аппаратного мультиплексирования. Когда сервер получает запрос *IOCTL_GPIO_COMMIT_FUNCTION_CONFIG_PINS*, для каждого контакта в списке необходимо выполнить следующие действия: 

*    Задайте аппаратный режим запроса, заданный в члене PinConfiguration member структуры PNP_FUNCTION_CONFIG_DESCRIPTOR.
*    Выполните мультиплексирование контакта для функции, заданной членом FunctionNumber структуры PNP_FUNCTION_CONFIG_DESCRIPTOR.

Затем сервер обязан выполнить запрос с помощью *STATUS_SUCCESS*.

Значение FunctionNumber определяется сервером; исходят из того, что дескриптор MsftFunctionConfig был создан специалистом, который знает, как сервер интерпретирует это поле.

Помните, что когда дескриптор закрывается, серверу необходимо вернуть контакты в состояние конфигурации,в котором они находились, когда был получен запрос IOCTL_GPIO_COMMIT_FUNCTION_CONFIG_PINS, поэтому серверу, возможно, потребуется сохранить их состояние до начала изменений.

####    <a name="handling-irpmjclose-requests"></a>Обработка запросов IRP_MJ_CLOSE

Если клиенту более не требуется ресурс мультиплексирования, он закрывает дескриптор. Когда сервер получает запрос *IRP_MJ_CLOSE*, он должен вернуть контакты в состояние, в котором они находились на момент получения *IOCTL_GPIO_COMMIT_FUNCTION_CONFIG_PINS*. Если клиент не отправлял запрос *IOCTL_GPIO_COMMIT_FUNCTION_CONFIG_PINS*, никакие действия не требуются. Затем сервер должен пометить контакты как доступные для арбитража общего доступа и завершить запрос с помощью *STATUS_SUCCESS*. Обязательно синхронизируйте обработку запросов *IRP_MJ_CLOSE* и *IRP_MJ_CREATE*.

###    <a name="authoring-guidelines-for-acpi-tables"></a>Инструкции по созданию таблиц ACPI

В этом разделе описывается, как предоставить ресурсы мультиплексирования клиентским драйверам. Обратите внимание, что для компиляции таблиц, содержащих ресурсы `MsftFunctionConfig()`, потребуется Microsoft ASL Compiler, сборка 14327 и выше. `MsftFunctionConfig()` ресурсы предоставляются клиентам мультиплексирования контактов в качестве аппаратных. `MsftFunctionConfig()` ресурсы необходимо предоставлять драйверам, которые требуют изменений мультиплексирования контактов; как правило, это драйверы контроллеров SPB и последовательных контроллеров; не следует предоставлять ресурсы периферийным драйверам SPB и последовательным периферийным драйверам, поскольку драйвер контроллера обрабатывает конфигурацию мультиплексирования.
Макрос ACPI `MsftFunctionConfig()` определяется следующим образом.

```cpp
  MsftFunctionConfig(Shared/Exclusive
                PinPullConfig,
                FunctionNumber,
                ResourceSource,
                ResourceSourceIndex,
                ResourceConsumer/ResourceProducer,
                VendorData) { Pin List }

```

* Общий/монопольный: если установлено значение "Монопольный", контакт может использоваться только одним клиентом в определенный момент. Если выбрано значение "Общий", использовать ресурс могут несколько клиентов. Всегда устанавливайте значение "Монопольный", поскольку в противном случае несколько нескоординированных клиентов получат доступ к изменяемому ресурсу, что может привести к конфликту данных и, следовательно, непредвиденным результатам. 
* PinPullConfig — один из 
  * PullDefault — используйте определенную SOC конфигурацию запроса по умолчанию на момент включения 
  * PullUp — включите повышающий резистор 
  * PullDown — включите понижающий резистор 
  * PullNone — отключить все резисторы 
* FunctionNumber — номер функции для программного использования в мультиплексировании. 
* ResourceSource — путь пространства имен ACPI сервера мультиплексирования контакта 
* ResourceSourceIndex — задайте значение 0. 
* ResourceConsumer/ResourceProducer — задайте значение ResourceConsumer 
* VendorData — необязательные двоичные данные. значение которых определяется сервером мультиплексирования контактов. Как правило, нужно оставить пустым.
* Список контактов — разделенный запятыми список номеров контактов, в отношении которых действует конфигурация. Если сервер мультиплексирования контактов является драйвером GpioClx, это номера контактов GPIO, они имеют тоже значение, что и номера контактов в дескрипторе GpioIo. 

В следующем примере показано, как предоставить ресурс MsftFunctionConfig() драйверу контроллера I2C. 

```cpp
Device(I2C1) 
{ 
    Name(_HID, "BCM2841") 
    Name(_CID, "BCMI2C") 
    Name(_UID, 0x1) 
    Method(_STA) 
    { 
        Return(0xf) 
    } 
    Method(_CRS, 0x0, NotSerialized) 
    { 
        Name(RBUF, ResourceTemplate() 
        { 
            Memory32Fixed(ReadWrite, 0x3F804000, 0x20) 
            Interrupt(ResourceConsumer, Level, ActiveHigh, Shared) { 0x55 } 
            MsftFunctionConfig(Exclusive, PullUp, 4, "\\_SB.GPI0", 0, ResourceConsumer, ) { 2, 3 } 
        }) 
        Return(RBUF) 
    } 
} 
```

В дополнение к ресурсам памяти и прерывания, которые обычно требуются драйверу контроллера, также необходимо задать ресурс `MsftFunctionConfig()`. Этот ресурс позволяет драйверу контроллера I2C помещать контакты 2 и 3 (управляемые узлом устройств в \\_SB.GPIO0) в функцию 4 с включенным повышающим резистором. 

### <a name="supporting-muxing-support-in-gpioclx-client-drivers"></a>Поддержка мультиплексирования в драйверах клиента GpioClx 

`GpioClx` имеет встроенную поддержку мультиплексирования контактов. Драйверы мини-портов GpioClx (драйверы клиента GpioClx), оборудование контроллера GPIO. Начиная с Windows 10, сборка 14327, драйверы мини-портов GpioClx могут добавлять поддержку мультиплексирования контактов, реализуя два новых DDI: 

* CLIENT_ConnectFunctionConfigPins — вызывается `GpioClx`, чтобы отдать драйверу мини-портов команду применить заданную конфигурацию мультиплексирования. 
* CLIENT_DisconnectFunctionConfigPins — вызывается `GpioClx`, чтобы отдать драйверу мини-портов команду отменить заданную конфигурацию мультиплексирования. 

См. описание этих процедур в разделе [Функции обратного вызова события GpioClx](https://msdn.microsoft.com/library/windows/hardware/hh439464.aspx).

Помимо двух этих новых DDI необходимо проверить существующие DDI на совместимость с мультиплексированием контактов. 

* CLIENT_ConnectIoPins/CLIENT_ConnectInterrupt — CLIENT_ConnectIoPins вызывается GpioClx, чтобы отдать команду драйверу мини-портов настроить набор контактов для ввода или вывода GPIO. GPIO является взаимно исключающим с MsftFunctionConfig, что означает, что контакт никогда не будет подключен к GPIO и MsftFunctionConfig одновременно. Поскольку функция контакта по умолчанию не обязательна должна быть GPIO, при вызове ConnectIoPins контакт не обязательно мультиплексируется для GPIO. ConnectIoPins необходим для выполнения всех операций по подготовке контакта к вводу/выводу GPIO, включая операции мультиплексирования. *CLIENT_ConnectInterrupt* должно вести себя аналогично, поскольку прерывания могут быть восприняты системой как особый случай ввода GPIO. 
* CLIENT_DisconnectIoPins/CLIENT_DisconnectInterrupt — эта процедура должна возвращать контакты в состояние, в котором они находились на момент вызова CLIENT_ConnectIoPins/CLIENT_ConnectInterrupt, если только не задан флаг PreserveConfiguration. Помимо возвращения направления контактов в состояние по умолчанию мини-порт также должен возвращать состояние мультиплексора каждого контакта в состояние, в котором тот находился на момент вызова процедуры _Connect. 

Допустим, конфигурация мультиплексора контакта по умолчанию — UART, и контакт также можно использовать в качестве GPIO. Если CLIENT_ConnectIoPins вызывается для подключения контакта для GPIO, он должен установить многоканальную связь между контактом и GPIO, а в CLIENT_DisconnectIoPins — установить многоканальную связь между контактом и UART. Как правило, процедуры _Disconnect должны отменять выполненное процедурами _Connect. 

### <a name="supporting-muxing-in-spbcx-and-sercx-controller-drivers"></a>Поддержка мультиплексирования в дисках контроллеров SpbCx и SerCx 

Начиная с Windows 10, сборка 14327, платформы `SpbCx` и `SerCx` содержат встроенную поддержку мультиплексирования контактов, что позволяет дискам контроллеров `SpbCx` и `SerCx` осуществлять мультиплексирование контактов для клиентов, не внося никаких изменений в код самих дисков контроллеров. Аналогично, любой периферийный драйвер SpbCx/SerCx, который подключается к драйверу контроллеров SpbCx/SerCx с поддержкой мультиплексирования, инициирует мультиплексирование контактов. 

На следующей схеме показаны зависимости между каждым из этих компонентов. Как видно, мультиплексирование контактов обуславливает зависимость драйверов контроллера SerCx и SpbCx от драйвера GPIO, который обычно отвечает за мультиплексирование. 

![Зависимость мультиплексирования контактов](images/usermode-access-diagram-2.png)

Во время инициализации устройства платформы `SpbCx` и `SerCx` выполняют синтаксический разбор всех ресурсов `MsftFunctionConfig()`, предоставленных устройству в качестве аппаратных. Затем SpbCx/SerCx по требованию приобретают и предоставляют ресурсы мультиплексирования контактов.

`SpbCx` применяет конфигурацию мультиплексирования контактов в обработчике *IRP_MJ_CREATE* сразу перед обратным вызовом метода [EvtSpbTargetConnect()](https://msdn.microsoft.com/library/windows/hardware/hh450818.aspx) драйвера клиента. Если применить конфигурацию мультиплексирования не удается, обратный вызов `EvtSpbTargetConnect()` драйвера контроллера выполнен не будет. Таким образом драйвер контроллера SPB может предположить, что контакты мультиплексируются в функцию к моменту вызова `EvtSpbTargetConnect()`.

`SpbCx` возвращает конфигурацию мультиплексирования контакта в обработчик *IRP_MJ_CLOSE* сразу после обратного вызова метода [EvtSpbTargetDisconnect()](https://msdn.microsoft.com/library/windows/hardware/hh450820.aspx) драйвера контроллера. В результате получается, что контакты мультиплексируются для функции SPB всякий раз, когда периферийный драйвер открывает дескриптор драйверу контроллера SPB и отменяют мультиплексирование, когда периферийный драйвер закрывает дескриптор.

`SerCx` функционируют аналогично. `SerCx` приобретает все ресурсы `MsftFunctionConfig()` в обработчике *IRP_MJ_CREATE* до обратного вызова [EvtSerCx2FileOpen()](https://msdn.microsoft.com/library/windows/hardware/dn265209.aspx) драйвера контроллера и отдает все ресурсы в обработчике IRP_MJ_CLOSE сразу после обратного вызова метода [EvtSerCx2FileClose](https://msdn.microsoft.com/library/windows/hardware/dn265208.aspx) драйвера контроллера.

Условие динамического мультиплексирования контакта для драйверов контроллера `SerCx` и `SpbCx` заключается в том, что они должны нормально обрабатывать отмену мультиплексирования контактов из функции SPB/UART. Драйверы контроллера должны исходить из того, что мультиплексирование контактов не выполняется до вызова `EvtSpbTargetConnect()` или `EvtSerCx2FileOpen()`. Контакты не обязательно мультиплексируются для функции SPB/UART во время следующих обратных вызовов. Следующий список не является исчерпывающим: в нем представлены наиболее распространенные процедуры PNP, реализуемые драйверами контроллеров.

* DriverEntry 
* EvtDriverDeviceAdd 
* EvtDevicePrepareHardware/EvtDeviceReleaseHardware 
* EvtDeviceD0Entry/EvtDeviceD0Exit 

## <a name="verification"></a>Проверка 

Завершив работу по созданию ASL, необходимо выполнить тесты [Hardware Lab Kit (HLK)](https://msdn.microsoft.com/library/windows/hardware/dn930814.aspx), чтобы проверить, что все ресурсы правильно предоставляются, а соответствующие шины соответствуют условиям функциональных контрактов API. В следующих разделах содержится описание загрузки узла устройств rhproxy для тестирования без повторной компиляции вашего встроенного ПО и пошаговое описание тестов HLK. 

### <a name="compile-and-load-asl-with-acpitabldat"></a>Выполните компиляцию и загрузку ASL с использованием файлов ACPITABL.dat 

Первый этап — выполнить компиляцию и загрузку файла ASL в тестируемую систему. Мы рекомендуем использовать во время разработки и тестирования ACPITABL.dat, поскольку он не требует полного перестроения UEFI для проверки изменений ASL. 

1. Создайте файл с именем yourboard.asl и поместите узел устройств RHPX в DefinitionBlock: 
```
DefinitionBlock ("ACPITABL.dat", "SSDT", 1, "MSFT", "RHPROXY", 1)
{
    Scope (\_SB)
    {
        Device(RHPX)
        {
        ...
        }
    }
}
```
2.    Скачайте WDK и получите asl.exe
3.    Выполните следующую команду, чтобы создать ACPITABL.dat:
```
asl.exe yourboard.asl
```
4.    Скопируйте созданный файл ACPITABL.dat в папку c: \Windows\system32 в тестируемой системе.
5.    Включите testsigning в тестируемой системе.
```
bcdedit /set testsigning on
```
6.    Перезагрузить тестируемую систему. Система добавляет таблицы ACPI, определенные в ACPITABL.dat, в таблицы встроенного ПО. 
7.    Убедитесь, что узел устройств RHPX добавлен в систему:
```
devcon status *msft8000
```
Вывод devcon должен указывать, что устройство присутствует, хотя инициализация драйвера может завершиться сбоем, если в ASL имеются требующие устранения ошибки.

### <a name="run-the-hlk-tests"></a>Выполнение тестов HLK

При выборе узла устройств rhproxy в диспетчере HLK соответствующие тесты выбираются автоматически.

В диспетчере HLK выберите "Устройство прокси-сервера центра ресурсов":

![Экран диспетчера HLK](images/usermode-hlk-1.png)

Щелкните вкладку "Тесты" и выберите тесты I2C WinRT, Gpio WinRT и Spi WinRT.

![Экран диспетчера HLK](images/usermode-hlk-2.png)

Щелкните "Запустить выбранные". Дополнительная документация по каждому тесту доступна по правому щелчку мыши (необходимо выбрать команду "Описание теста" в контекстном меню).

###    <a name="more-testing-resources"></a>Другие ресурсы для тестирования

Простые средства командной строки для Gpio, I2c, Spi и Serial доступны в репозитории примеров ms-iot github (https://github.com/ms-iot/samples). Эти средства могут быть полезны для ручной отладки.

| Инструмент | Ссылка |
|------|------|
| GpioTestTool | https://developer.microsoft.com/windows/iot/win10/samples/GPIOTestTool |
| I2cTestTool    | https://developer.microsoft.com/windows/iot/win10/samples/I2cTestTool | 
| SpiTestTool |    https://developer.microsoft.com/windows/iot/win10/samples/spitesttool |
| MinComm (послед.) |    https://github.com/ms-iot/samples/tree/develop/MinComm |

## <a name="resources"></a>Ресурсы

| Расположение | Ссылка |
|-------------|------|
| Спецификация ACPI 5.0 | http://acpi.info/spec.htm |
| Asl.exe (Microsoft ASL Compiler) | https://msdn.microsoft.com/library/windows/hardware/dn551195.aspx |
| Windows.Devices.Gpio    | https://msdn.microsoft.com/library/windows/apps/windows.devices.gpio.aspx | 
| Windows.Devices.I2c | https://msdn.microsoft.com/library/windows/apps/windows.devices.i2c.aspx |
| Windows.Devices.Spi | https://msdn.microsoft.com/library/windows/apps/windows.devices.spi.aspx |
| Windows.Devices.SerialCommunication | https://msdn.microsoft.com/library/windows/apps/windows.devices.serialcommunication.aspx |
| Платформа тестирования разработки и выполнения (TAEF) | https://msdn.microsoft.com/library/windows/hardware/hh439725.aspx |
| SpbCx | https://msdn.microsoft.com/library/windows/hardware/hh450906.aspx |
| GpioClx    | https://msdn.microsoft.com/library/windows/hardware/hh439508.aspx |
| SerCx | https://msdn.microsoft.com/library/windows/hardware/ff546939.aspx |
| Тесты MITT I2C | https://msdn.microsoft.com/library/windows/hardware/dn919852.aspx |
| GpioTestTool | https://developer.microsoft.com/windows/iot/win10/samples/GPIOTestTool |
| I2cTestTool    | https://developer.microsoft.com/windows/iot/win10/samples/I2cTestTool | 
| SpiTestTool |    https://developer.microsoft.com/windows/iot/win10/samples/spitesttool |
| MinComm (послед.) |    https://github.com/ms-iot/samples/tree/develop/MinComm |
| Комплект проектирования оборудования (HLK) | https://msdn.microsoft.com/library/windows/hardware/dn930814.aspx |

## <a name="apendix"></a>Приложение

### <a name="appendix-a---raspberry-pi-asl-listing"></a>Приложение А. Списки ASL в Raspberry Pi

Вывод заголовка: https://developer.microsoft.com/windows/iot/win10/samples/PinMappingsRPi2

```
DefinitionBlock ("ACPITABL.dat", "SSDT", 1, "MSFT", "RHPROXY", 1)
{

    Scope (\_SB)
    {
        //
        // RHProxy Device Node to enable WinRT API
        //
        Device(RHPX)
        {
            Name(_HID, "MSFT8000")
            Name(_CID, "MSFT8000")
            Name(_UID, 1)

            Name(_CRS, ResourceTemplate()
            {
                // Index 0
                SPISerialBus(              // SCKL - GPIO 11 - Pin 23
                                           // MOSI - GPIO 10 - Pin 19
                                           // MISO - GPIO 9  - Pin 21
                                           // CE0  - GPIO 8  - Pin 24
                    0,                     // Device selection (CE0)
                    PolarityLow,           // Device selection polarity
                    FourWireMode,          // wiremode
                    0,                     // databit len: placeholder
                    ControllerInitiated,   // slave mode
                    0,                     // connection speed: placeholder
                    ClockPolarityLow,      // clock polarity: placeholder
                    ClockPhaseFirst,       // clock phase: placeholder
                    "\\_SB.SPI0",          // ResourceSource: SPI bus controller name
                    0,                     // ResourceSourceIndex
                                           // Resource usage
                    )                      // Vendor Data

                // Index 1
                SPISerialBus(              // SCKL - GPIO 11 - Pin 23
                                           // MOSI - GPIO 10 - Pin 19
                                           // MISO - GPIO 9  - Pin 21
                                           // CE1  - GPIO 7  - Pin 26
                    1,                     // Device selection (CE1)
                    PolarityLow,           // Device selection polarity
                    FourWireMode,          // wiremode
                    0,                     // databit len: placeholder
                    ControllerInitiated,   // slave mode
                    0,                     // connection speed: placeholder
                    ClockPolarityLow,      // clock polarity: placeholder
                    ClockPhaseFirst,       // clock phase: placeholder
                    "\\_SB.SPI0",          // ResourceSource: SPI bus controller name
                    0,                     // ResourceSourceIndex
                                           // Resource usage
                    )                      // Vendor Data

                // Index 2
                SPISerialBus(              // SCKL - GPIO 21 - Pin 40
                                           // MOSI - GPIO 20 - Pin 38
                                           // MISO - GPIO 19 - Pin 35
                                           // CE1  - GPIO 17 - Pin 11
                    1,                     // Device selection (CE1)
                    PolarityLow,           // Device selection polarity
                    FourWireMode,          // wiremode
                    0,                     // databit len: placeholder
                    ControllerInitiated,   // slave mode
                    0,                     // connection speed: placeholder
                    ClockPolarityLow,      // clock polarity: placeholder
                    ClockPhaseFirst,       // clock phase: placeholder
                    "\\_SB.SPI1",          // ResourceSource: SPI bus controller name
                    0,                     // ResourceSourceIndex
                                           // Resource usage
                    )                      // Vendor Data
                // Index 3
                I2CSerialBus(              // Pin 3 (GPIO2, SDA1), 5 (GPIO3, SCL1)
                    0xFFFF,                // SlaveAddress: placeholder
                    ,                      // SlaveMode: default to ControllerInitiated
                    0,                     // ConnectionSpeed: placeholder
                    ,                      // Addressing Mode: placeholder
                    "\\_SB.I2C1",          // ResourceSource: I2C bus controller name
                    ,
                    ,
                    )                      // VendorData

                // Index 4 - GPIO 4 -
                GpioIO(Shared, PullUp, , , , "\\_SB.GPI0", , , , ) { 4 }
                GpioInt(Edge, ActiveBoth, Shared, PullUp, 0, "\\_SB.GPI0",) { 4 }
                // Index 6 - GPIO 5 -
                GpioIO(Shared, PullUp, , , , "\\_SB.GPI0", , , , ) { 5 }
                GpioInt(Edge, ActiveBoth, Shared, PullUp, 0, "\\_SB.GPI0",) { 5 }
                // Index 8 - GPIO 6 -
                GpioIO(Shared, PullUp, , , , "\\_SB.GPI0", , , , ) { 6 }
                GpioInt(Edge, ActiveBoth, Shared, PullUp, 0, "\\_SB.GPI0",) { 6 }
                // Index 10 - GPIO 12 -
                GpioIO(Shared, PullDown, , , , "\\_SB.GPI0", , , , ) { 12 }
                GpioInt(Edge, ActiveBoth, Shared, PullDown, 0, "\\_SB.GPI0",) { 12 }
                // Index 12 - GPIO 13 -
                GpioIO(Shared, PullDown, , , , "\\_SB.GPI0", , , , ) { 13 }
                GpioInt(Edge, ActiveBoth, Shared, PullDown, 0, "\\_SB.GPI0",) { 13 }
                // Index 14 - GPIO 16 -
                GpioIO(Shared, PullDown, , , , "\\_SB.GPI0", , , , ) { 16 }
                GpioInt(Edge, ActiveBoth, Shared, PullDown, 0, "\\_SB.GPI0",) { 16 }
                // Index 16 - GPIO 18 -
                GpioIO(Shared, PullDown, , , , "\\_SB.GPI0", , , , ) { 18 }
                GpioInt(Edge, ActiveBoth, Shared, PullDown, 0, "\\_SB.GPI0",) { 18 }
                // Index 18 - GPIO 22 -
                GpioIO(Shared, PullDown, , , , "\\_SB.GPI0", , , , ) { 22 }
                GpioInt(Edge, ActiveBoth, Shared, PullDown, 0, "\\_SB.GPI0",) { 22 }
                // Index 20 - GPIO 23 -
                GpioIO(Shared, PullDown, , , , "\\_SB.GPI0", , , , ) { 23 }
                GpioInt(Edge, ActiveBoth, Shared, PullDown, 0, "\\_SB.GPI0",) { 23 }
                // Index 22 - GPIO 24 -
                GpioIO(Shared, PullDown, , , , "\\_SB.GPI0", , , , ) { 24 }
                GpioInt(Edge, ActiveBoth, Shared, PullDown, 0, "\\_SB.GPI0",) { 24 }
                // Index 24 - GPIO 25 -
                GpioIO(Shared, PullDown, , , , "\\_SB.GPI0", , , , ) { 25 }
                GpioInt(Edge, ActiveBoth, Shared, PullDown, 0, "\\_SB.GPI0",) { 25 }
                // Index 26 - GPIO 26 -
                GpioIO(Shared, PullDown, , , , "\\_SB.GPI0", , , , ) { 26 }
                GpioInt(Edge, ActiveBoth, Shared, PullDown, 0, "\\_SB.GPI0",) { 26 }
                // Index 28 - GPIO 27 -
                GpioIO(Shared, PullDown, , , , "\\_SB.GPI0", , , , ) { 27 }
                GpioInt(Edge, ActiveBoth, Shared, PullDown, 0, "\\_SB.GPI0",) { 27 }
                // Index 30 - GPIO 35 -
                GpioIO(Shared, PullUp, , , , "\\_SB.GPI0", , , , ) { 35 }
                GpioInt(Edge, ActiveBoth, Shared, PullUp, 0, "\\_SB.GPI0",) { 35 }
                // Index 32 - GPIO 47 -
                GpioIO(Shared, PullUp, , , , "\\_SB.GPI0", , , , ) { 47 }
                GpioInt(Edge, ActiveBoth, Shared, PullUp, 0, "\\_SB.GPI0",) { 47 }
            })

            Name(_DSD, Package()
            {
                ToUUID("daffd814-6eba-4d8c-8a91-bc9bbf4aa301"),
                Package()
                {
                    // Reference http://www.raspberrypi.org/documentation/hardware/raspberrypi/spi/README.md
                    // SPI 0
                    Package(2) { "bus-SPI-SPI0", Package() { 0, 1 }},                       // Index 0 & 1
                    Package(2) { "SPI0-MinClockInHz", 7629 },                               // 7629 Hz
                    Package(2) { "SPI0-MaxClockInHz", 125000000 },                          // 125 MHz
                    Package(2) { "SPI0-SupportedDataBitLengths", Package() { 8 }},          // Data Bit Length
                    // SPI 1
                    Package(2) { "bus-SPI-SPI1", Package() { 2 }},                          // Index 2
                    Package(2) { "SPI1-MinClockInHz", 30518 },                              // 30518 Hz
                    Package(2) { "SPI1-MaxClockInHz", 125000000 },                          // 125 MHz
                    Package(2) { "SPI1-SupportedDataBitLengths", Package() { 8 }},          // Data Bit Length
                    // I2C1
                    Package(2) { "bus-I2C-I2C1", Package() { 3 }},
                    // GPIO Pin Count and supported drive modes
                    Package (2) { "GPIO-PinCount", 54 },
                    Package (2) { "GPIO-UseDescriptorPinNumbers", 1 },
                    Package (2) { "GPIO-SupportedDriveModes", 0xf },                        // InputHighImpedance, InputPullUp, InputPullDown, OutputCmos
                }
            })
        }
    }
}

```

### <a name="appendix-b---minnowboardmax-asl-listing"></a>Приложение B. Списки ASL в MinnowBoardMax

Вывод заголовка: https://developer.microsoft.com/windows/iot/win10/samples/PinMappingsMBM

```
DefinitionBlock ("ACPITABL.dat", "SSDT", 1, "MSFT", "RHPROXY", 1)
{
    Scope (\_SB)
    {
        Device(RHPX)
        {
            Name(_HID, "MSFT8000")
            Name(_CID, "MSFT8000")
            Name(_UID, 1)

            Name(_CRS, ResourceTemplate() 
            {  
                // Index 0 
                SPISerialBus(            // Pin 5, 7, 9 , 11 of JP1 for SIO_SPI
                    1,                     // Device selection
                    PolarityLow,           // Device selection polarity
                    FourWireMode,          // wiremode
                    8,                     // databit len
                    ControllerInitiated,   // slave mode
                    8000000,               // Connection speed
                    ClockPolarityLow,      // Clock polarity
                    ClockPhaseSecond,      // clock phase
                    "\\_SB.SPI1",          // ResourceSource: SPI bus controller name
                    0,                     // ResourceSourceIndex
                    ResourceConsumer,      // Resource usage
                    JSPI,                  // DescriptorName: creates name for offset of resource descriptor
                    )                      // Vendor Data  
    
                // Index 1     
                I2CSerialBus(            // Pin 13, 15 of JP1, for SIO_I2C5 (signal)
                    0xFF,                  // SlaveAddress: bus address
                    ,                      // SlaveMode: default to ControllerInitiated
                    400000,                // ConnectionSpeed: in Hz
                    ,                      // Addressing Mode: default to 7 bit
                    "\\_SB.I2C6",          // ResourceSource: I2C bus controller name (For MinnowBoard Max, hardware I2C5(0-based) is reported as ACPI I2C6(1-based))
                    ,
                    ,
                    JI2C,                  // Descriptor Name: creates name for offset of resource descriptor
                    )                      // VendorData
    
                // Index 2
                UARTSerialBus(           // Pin 17, 19 of JP1, for SIO_UART2
                    115200,                // InitialBaudRate: in bits ber second
                    ,                      // BitsPerByte: default to 8 bits
                    ,                      // StopBits: Defaults to one bit
                    0xfc,                  // LinesInUse: 8 1-bit flags to declare line enabled
                    ,                      // IsBigEndian: default to LittleEndian
                    ,                      // Parity: Defaults to no parity
                    ,                      // FlowControl: Defaults to no flow control
                    32,                    // ReceiveBufferSize
                    32,                    // TransmitBufferSize
                    "\\_SB.URT2",          // ResourceSource: UART bus controller name
                    ,
                    ,
                    UAR2,                  // DescriptorName: creates name for offset of resource descriptor
                    )                      
    
                // Index 3
                GpioIo (Shared, PullNone, 0, 0, IoRestrictionNone, "\\_SB.GPO2",) {0}  // Pin 21 of JP1 (GPIO_S5[00])
                // Index 4
                GpioInt(Edge, ActiveBoth, SharedAndWake, PullNone, 0,"\\_SB.GPO2",) {0} 
    
                // Index 5
                GpioIo (Shared, PullNone, 0, 0, IoRestrictionNone, "\\_SB.GPO2",) {1}  // Pin 23 of JP1 (GPIO_S5[01])
                // Index 6
                GpioInt(Edge, ActiveBoth, SharedAndWake, PullNone, 0,"\\_SB.GPO2",) {1}
    
                // Index 7
                GpioIo (Shared, PullNone, 0, 0, IoRestrictionNone, "\\_SB.GPO2",) {2}  // Pin 25 of JP1 (GPIO_S5[02])
                // Index 8
                GpioInt(Edge, ActiveBoth, SharedAndWake, PullNone, 0,"\\_SB.GPO2",) {2} 
    
                // Index 9
                UARTSerialBus(           // Pin 6, 8, 10, 12 of JP1, for SIO_UART1
                    115200,                // InitialBaudRate: in bits ber second
                    ,                      // BitsPerByte: default to 8 bits
                    ,                      // StopBits: Defaults to one bit
                    0xfc,                  // LinesInUse: 8 1-bit flags to declare line enabled
                    ,                      // IsBigEndian: default to LittleEndian
                    ,                      // Parity: Defaults to no parity
                    FlowControlHardware,   // FlowControl: Defaults to no flow control
                    32,                    // ReceiveBufferSize
                    32,                    // TransmitBufferSize
                    "\\_SB.URT1",          // ResourceSource: UART bus controller name
                    ,
                    ,
                    UAR1,              // DescriptorName: creates name for offset of resource descriptor
                    )  
    
                // Index 10
                GpioIo (Shared, PullNone, 0, 0, IoRestrictionNone, "\\_SB.GPO0",) {62}  // Pin 14 of JP1 (GPIO_SC[62])
                // Index 11
                GpioInt(Edge, ActiveBoth, SharedAndWake, PullNone, 0,"\\_SB.GPO0",) {62} 

                // Index 12
                GpioIo (Shared, PullNone, 0, 0, IoRestrictionNone, "\\_SB.GPO0",) {63}  // Pin 16 of JP1 (GPIO_SC[63])
                // Index 13
                GpioInt(Edge, ActiveBoth, SharedAndWake, PullNone, 0,"\\_SB.GPO0",) {63} 
    
                // Index 14
                GpioIo (Shared, PullNone, 0, 0, IoRestrictionNone, "\\_SB.GPO0",) {65}  // Pin 18 of JP1 (GPIO_SC[65])
                // Index 15
                GpioInt(Edge, ActiveBoth, SharedAndWake, PullNone, 0,"\\_SB.GPO0",) {65} 
    
                // Index 16
                GpioIo (Shared, PullNone, 0, 0, IoRestrictionNone, "\\_SB.GPO0",) {64}  // Pin 20 of JP1 (GPIO_SC[64])
                // Index 17
                GpioInt(Edge, ActiveBoth, SharedAndWake, PullNone, 0,"\\_SB.GPO0",) {64} 
    
                // Index 18
                GpioIo (Shared, PullNone, 0, 0, IoRestrictionNone, "\\_SB.GPO0",) {94}  // Pin 22 of JP1 (GPIO_SC[94])
                // Index 19
                GpioInt(Edge, ActiveBoth, SharedAndWake, PullNone, 0,"\\_SB.GPO0",) {94} 
    
                // Index 20
                GpioIo (Shared, PullNone, 0, 0, IoRestrictionNone, "\\_SB.GPO0",) {95}  // Pin 24 of JP1 (GPIO_SC[95])
                // Index 21
                GpioInt(Edge, ActiveBoth, SharedAndWake, PullNone, 0,"\\_SB.GPO0",) {95} 
    
                // Index 22
                GpioIo (Shared, PullNone, 0, 0, IoRestrictionNone, "\\_SB.GPO0",) {54}  // Pin 26 of JP1 (GPIO_SC[54])
                // Index 23
                GpioInt(Edge, ActiveBoth, SharedAndWake, PullNone, 0,"\\_SB.GPO0",) {54}
            })
    
            Name(_DSD, Package() 
            {
                ToUUID("daffd814-6eba-4d8c-8a91-bc9bbf4aa301"),
                Package() 
                {
                    // SPI Mapping
                    Package(2) { "bus-SPI-SPI0", Package() { 0 }},

                    Package(2) { "SPI0-MinClockInHz", 100000 },
                    Package(2) { "SPI0-MaxClockInHz", 15000000 },
                    // SupportedDataBitLengths takes a list of support data bit length
                    // Example : Package(2) { "SPI0-SupportedDataBitLengths", Package() { 8, 7, 16 }},
                    Package(2) { "SPI0-SupportedDataBitLengths", Package() { 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23, 24, 25, 26, 27, 28, 29, 30, 31, 32 }},
                     // I2C Mapping
                    Package(2) { "bus-I2C-I2C5", Package() { 1 }},
                    // UART Mapping
                    Package(2) { "bus-UART-UART2", Package() { 2 }},
                    Package(2) { "bus-UART-UART1", Package() { 9 }},
                }
            })
        }
    }
}
```

### <a name="appendix-c---sample-powershell-script-to-generate-gpio-resources"></a>Приложение C. Пример сценария Powershell для создания ресурсов GPIO

Следующий скрипт можно использовать для создания деклараций ресурсов GPIO для Raspberry Pi:

```
$pins = @(
    @{PinNumber=4;PullConfig='PullUp'},
    @{PinNumber=5;PullConfig='PullUp'},
    @{PinNumber=6;PullConfig='PullUp'},
    @{PinNumber=12;PullConfig='PullDown'},
    @{PinNumber=13;PullConfig='PullDown'},
    @{PinNumber=16;PullConfig='PullDown'},
    @{PinNumber=18;PullConfig='PullDown'},
    @{PinNumber=22;PullConfig='PullDown'},
    @{PinNumber=23;PullConfig='PullDown'},
    @{PinNumber=24;PullConfig='PullDown'},
    @{PinNumber=25;PullConfig='PullDown'},
    @{PinNumber=26;PullConfig='PullDown'},
    @{PinNumber=27;PullConfig='PullDown'},
    @{PinNumber=35;PullConfig='PullUp'},
    @{PinNumber=47;PullConfig='PullUp'})
    
# generate the resources
$FIRST_RESOURCE_INDEX = 4
$resourceIndex = $FIRST_RESOURCE_INDEX
$pins | % {
    $a = @"
// Index $resourceIndex - GPIO $($_.PinNumber) - $($_.Name)
GpioIO(Shared, $($_.PullConfig), , , , "\\_SB.GPI0", , , , ) { $($_.PinNumber) }
GpioInt(Edge, ActiveBoth, Shared, $($_.PullConfig), 0, "\\_SB.GPI0",) { $($_.PinNumber) }
"@    
    Write-Host $a
    $resourceIndex += 2;
}
```

